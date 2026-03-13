# 接入DeepSeek实现用户的个性化推荐
## 1.精简版本(不接入DeepSeek)

### 1. 实体类定义

```java
// 房间核心信息
@Data
@TableName("room_info")
public class Room {
    private Long id;
    private BigDecimal rent;
    private Long apartmentId;
    
    @TableField(exist = false)
    private Double distance; // 距离（临时字段）
}

// 公寓信息
@Data
@TableName("apartment_info")
public class Apartment {
    private Long id;
    private BigDecimal latitude;
    private BigDecimal longitude;
    private String addressDetail;
}

// 用户请求参数
@Data
public class RecommendRequest {
    private BigDecimal userLat;   // 用户纬度
    private BigDecimal userLng;  // 用户经度
    private BigDecimal maxRent;  // 最高租金
    private List<String> paymentTypes; // 支付方式
    private List<String> facilities;   // 设施要求
}
```

### 2. 推荐核心逻辑

```java
@Service
@RequiredArgsConstructor
public class RecommendService {
    private final RoomMapper roomMapper;
    private final ApartmentMapper apartmentMapper;

    public ListRoom> recommend(RecommendRequest request) {
        // 1. 获取候选房间
        ListRoom> candidates = roomMapper.selectRooms(
            request.getMaxRent(),
            request.getPaymentTypes(),
            request.getFacilities()
        );

        // 2. 计算距离并过滤
        candidates.forEach(room -> {
            Apartment apt = apartmentMapper.selectById(room.getApartmentId());
            room.setDistance(calculateDistance(
                request.getUserLat(), 
                request.getUserLng(),
                apt.getLatitude(),
                apt.getLongitude()
            ));
        });

        // 3. 简单排序逻辑
        return candidates.stream()
            .filter(r -> r.getDistance() <= 5000) // 5公里内
            .sorted(Comparator.comparing(Room::getRent)
                .thenComparing(Room::getDistance))
            .limit(10)
            .collect(Collectors.toList());
    }

    // 计算两点间距离（米）
    private double calculateDistance(BigDecimal lat1, BigDecimal lng1,
                                     BigDecimal lat2, BigDecimal lng2) {
        double R = 6371000; // 地球半径
        double dLat = Math.toRadians(lat2.subtract(lat1).doubleValue());
        double dLng = Math.toRadians(lng2.subtract(lng1).doubleValue());
        double a = Math.sin(dLat/2) * Math.sin(dLat/2) +
                   Math.cos(Math.toRadians(lat1.doubleValue())) * 
                   Math.cos(Math.toRadians(lat2.doubleValue())) *
                   Math.sin(dLng/2) * Math.sin(dLng/2);
        double c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
        return R * c;
    }
}
```

### 3. MyBatis Mapper XML

```xml
<!-- RoomMapper.xml -->
select id="selectRooms" resultType="Room">
    SELECT r.* 
    FROM room_info r
    where>
        r.is_deleted = 0
        AND r.rent &lt;= #{maxRent}
        
        if test="paymentTypes != null and !paymentTypes.isEmpty()">
            AND EXISTS (
                SELECT 1 FROM room_payment_type rpt
                JOIN payment_type pt ON rpt.payment_type_id = pt.id
                WHERE rpt.room_id = r.id 
                AND pt.name IN 
                foreach item="type" collection="paymentTypes" 
                    open="(" separator="," close=")">
                    #{type}
                </foreach>
            )
        </if>
        
        if test="facilities != null and !facilities.isEmpty()">
            AND (
                (SELECT COUNT(*) FROM room_facility rf
                 JOIN facility_info f ON rf.facility_id = f.id
                 WHERE rf.room_id = r.id 
                 AND f.name IN 
                 foreach item="fac" collection="facilities" 
                     open="(" separator="," close=")">
                     #{fac}
                 </foreach>) &gt;= #{facilities.size}
            )
        </if>
    </where>
</select>
```

### 4. 控制器层

```java
@RestController
@RequestMapping("/recommend")
@RequiredArgsConstructor
public class RecommendController {
    private final RecommendService recommendService;

    @PostMapping
    public ResponseEntity<ListRoom>> getRecommendations(
        @RequestBody RecommendRequest request) {
        return ResponseEntity.ok(recommendService.recommend(request));
    }
}
```

### 5. 简单调用示例

```java
// 测试用例
public class TestRecommend {
    public static void main(String[] args) {
        RecommendRequest request = new RecommendRequest();
        request.setUserLat(new BigDecimal("30.123456"));
        request.setUserLng(new BigDecimal("120.654321"));
        request.setMaxRent(new BigDecimal("5000"));
        request.setPaymentTypes(List.of("月付", "季付"));
        request.setFacilities(List.of("WIFI", "独立卫浴"));

        ListRoom> results = recommendService.recommend(request);
        results.forEach(r -> System.out.println(
            "房间ID:" + r.getId() + 
            " 月租:" + r.getRent() + 
            " 距离:" + r.getDistance() + "米"));
    }
}
```

### 关键点说明：

1. **三层架构**：清晰的分层结构（Controller/Service/Mapper）

2. **动态查询**：使用MyBatis动态SQL处理可选条件

3. **距离计算**：Haversine公式实现地理距离计算

4. **简单排序**：租金优先、距离其次的排序策略

5. 基础过滤

   ：

   - 支付方式必须全部满足
   - 设施要求必须全部包含
   - 默认5公里范围限制

## 2. 基础版本（接入DeepSeek)

以下是简化版的 **Java + DeepSeek** 推荐实现方案，仅需 **3个核心步骤**：

------

### **1. 将用户需求转换为自然语言（关键步骤）**

```java
public class PromptBuilder {
    public static String buildQuery(RecommendRequest request) {
        StringBuilder prompt = new StringBuilder();
        prompt.append("根据以下租房需求生成SQL WHERE条件:\n");
        prompt.append("- 地理位置: 距离(")
              .append(request.getUserLat()).append(",")
              .append(request.getUserLng()).append(") 5公里内\n");
        prompt.append("- 租金范围: ").append(request.getMinRent())
              .append("~").append(request.getMaxRent()).append("元\n");
        prompt.append("- 支付方式: ").append(String.join(",", request.getPaymentTypes())).append("\n");
        prompt.append("- 必要设施: ").append(String.join(",", request.getFacilities())).append("\n");
        prompt.append("请直接输出WHERE后的条件语句，不要解释，例如：WHERE rent > 3000 AND ...");
        return prompt.toString();
    }
}
```

------

### **2. 调用DeepSeek API获取推荐逻辑**

```java
public class DeepSeekClient {
    private static final String API_URL = "https://api.deepseek.com/v1/chat/completions";
    private static final String API_KEY = "your_api_key";

    public static String getRecommendCondition(String prompt) throws IOException {
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(API_URL))
                .header("Authorization", "Bearer " + API_KEY)
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(
                    String.format("{\"model\": \"deepseek-chat\", \"messages\": [{\"role\": \"user\", \"content\": \"%s\"}]}", prompt)
                ))
                .build();

        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());

        // 解析返回的SQL条件
        JsonNode root = new ObjectMapper().readTree(response.body());
        return root.get("choices").get(0).get("message").get("content").asText();
    }
}
```

------

### **3. 执行动态查询**

```java
@Service
public class RecommendService {
    
    public ListRoom> recommend(RecommendRequest request) {
        // Step 1: 生成自然语言提示
        String prompt = PromptBuilder.buildQuery(request);
        
        // Step 2: 获取DeepSeek生成的查询条件
        String whereCondition = DeepSeekClient.getRecommendCondition(prompt);
        
        // Step 3: 执行动态SQL
        return roomMapper.dynamicSearch(whereCondition);
    }
}

// MyBatis Mapper接口
public interface RoomMapper {
    @Select("SELECT * FROM room_info WHERE ${condition} LIMIT 10")
    ListRoom> dynamicSearch(@Param("condition") String condition);
}
```

------

### **示例交互流程**

1. **用户输入**：

   ```json
   {
     "userLat": 30.123456,
     "userLng": 120.654321,
     "minRent": 2000,
     "maxRent": 5000,
     "paymentTypes": ["月付", "季付"],
     "facilities": ["WIFI", "独立卫浴"]
   }
   ```

2. **DeepSeek返回**：

   ```sql
   rent BETWEEN 2000 AND 5000 
   AND EXISTS (SELECT 1 FROM room_payment_type rpt 
               JOIN payment_type pt ON rpt.payment_type_id = pt.id 
               WHERE rpt.room_id = room_info.id AND pt.name IN ('月付','季付'))
   AND (SELECT COUNT(*) FROM room_facility rf 
        JOIN facility_info f ON rf.facility_id = f.id 
        WHERE rf.room_id = room_info.id AND f.name IN ('WIFI','独立卫浴')) = 2
   AND ST_Distance_Sphere(POINT(longitude, latitude), POINT(120.654321, 30.123456)) <= 5000
   ```

3. **最终执行SQL**：

   ```sql
   SELECT * FROM room_info 
   WHERE rent BETWEEN 2000 AND 5000
     AND EXISTS (...支付方式子查询...)
     AND (...设施条件...)
     AND (...距离计算...) 
   LIMIT 10
   ```

------

### 示例1

1. **用户输入**
我想要在/北京市昌平区/租一间月租金/800-1000/元房间户型为/两室一厅/面积为/60平/带/wifi、洗衣机、空调/可选择/季付、月付/支付方式的房间

2.  **转化为Json**

```javascript
   {
     "address": "北京市昌平区",
     "rentalRange": "800到1000元",
     "attrValue":["两室一厅","60平","独立卫浴"],
     "facilities": ["WIFI", "洗衣机","空调"],
     "paymentTypes": ["月付", "季付"],
   }
```

3. **生成自然语言**

  ```
  根据以下租房需求生成SQL WHERE条件:
- 详细地址前缀: 北京市昌平区
- 租金范围: 800到1000元
- 支付方式: 季付或月付
- 必要设施: wifi、洗衣机、空调
请直接输出WHERE后的条件语句，不要解释，例如：WHERE rent > 3000 AND ...
  ```
4. 返回结果
```java
SELECT *
FROM room_info
WHERE rent BETWEEN 500 AND 1000
  AND apartment_id IN (SELECT id FROM apartment_info WHERE address_detail LIKE '北京市昌平区%')
  AND id IN (SELECT room_id
             FROM room_payment_type
             WHERE payment_type_id IN (SELECT id FROM payment_type WHERE name IN ('季付', '月付')))
  AND id IN (SELECT room_id
             FROM room_facility
             WHERE facility_id IN (SELECT id FROM facility_info WHERE name IN ('WIFI', '冰箱', '空调') AND type = 2)
             GROUP BY room_id
             HAVING COUNT(DISTINCT facility_id) = 3)
LIMIT 10

获得数据
id,room_number,rent,apartment_id,is_release,create_time,update_time,is_deleted
19,107,500.00,10,1,2025-02-25 09:54:01,,0

```

### **方案优势**

1. **零特征工程**：直接利用DeepSeek的自然语言理解能力

2. **动态适配**：自动根据用户需求生成查询条件

3. 简单安全：

   - 使用`${condition}`进行条件注入（需确保API调用安全）
- 限制返回结果数量（LIMIT 10）

------

### **部署注意事项**

1. **API安全**：

   ```java
   // 添加正则过滤防止SQL注入
   String safeCondition = whereCondition.replaceAll("[^a-zA-Z0-9_=<>()\\s.,'']", "");
```

2. **缓存机制**：

   ```java
   // 使用Caffeine缓存常见查询
   LoadingCache<String, ListRoom>> cache = Caffeine.newBuilder()
       .maximumSize(1000)
       .expireAfterWrite(1, TimeUnit.HOURS)
       .build(key -> roomMapper.dynamicSearch(key));
```

3. **性能监控**：

   ```java
   // 记录DeepSeek API响应时间
   long start = System.currentTimeMillis();
   String whereCondition = DeepSeekClient.getRecommendCondition(prompt);
   log.info("DeepSeek API耗时: {}ms", System.currentTimeMillis() - start);
   ```

------

该方案通过 **自然语言转SQL** 的核心思路，仅需 **50行代码** 即可实现基于DeepSeek的推荐功能，适合快速验证场景。如果需要更复杂的推荐策略，可在`PromptBuilder`中优化提示词设计。

## 3.优化版本

以下是一个优化后的 **Java + DeepSeek** 推荐方案，采用模块化设计并添加了向量缓存机制：

```java
// 1. 核心领域模型（精简版）
@Builder
@Data
public class RoomFeature {
    private Long roomId;
    private String textFeature;  // 文本特征："月租3000 季付 独立卫浴 近地铁"
    private float[] embedding;   // DeepSeek生成的向量
    private LocalDateTime updateTime;
}

// 2. 特征存储接口
public interface FeatureStore {
    void saveFeature(RoomFeature feature);
    RoomFeature getFeature(Long roomId);
    ListLong> searchSimilar(float[] queryEmbedding, int topK);
}

// 3. 特征处理器（核心逻辑）
public class FeatureProcessor {
    private final FeatureStore featureStore;
    private final DeepSeekClient deepSeek;

    @Async
    public void refreshFeatures(ListLong> roomIds) {
        roomIds.parallelStream().forEach(roomId -> {
            RoomFeature feature = buildFeature(roomId);
            featureStore.saveFeature(feature);
        });
    }

    private RoomFeature buildFeature(Long roomId) {
        // 从数据库聚合特征数据
        String featureText = roomMapper.selectFeatureText(roomId);
        
        // 调用DeepSeek获取向量
        float[] embedding = deepSeek.getEmbedding(featureText);
        
        return RoomFeature.builder()
            .roomId(roomId)
            .textFeature(featureText)
            .embedding(embedding)
            .updateTime(LocalDateTime.now())
            .build();
    }
}

// 4. 推荐服务（核心逻辑）
@Service
@RequiredArgsConstructor
public class RecommenderService {
    private final FeatureStore featureStore;
    private final RoomFilter roomFilter;

    public ListRoom> recommend(String userQuery) {
        // Step 1: 解析用户需求
        float[] queryEmbedding = deepSeek.getEmbedding(userQuery);
        
        // Step 2: 向量相似度召回
        ListLong> candidateIds = featureStore.searchSimilar(queryEmbedding, 50);
        
        // Step 3: 精确过滤
        return roomFilter.filter(candidateIds).stream()
               .sorted((a,b) -> Float.compare(
                   cosineSimilarity(b.getEmbedding(), queryEmbedding),
                   cosineSimilarity(a.getEmbedding(), queryEmbedding)
               ))
               .limit(10)
               .collect(Collectors.toList());
    }

    private float cosineSimilarity(float[] a, float[] b) {
        // 实现余弦相似度计算
    }
}

// 5. 实现示例（基于Redis的向量存储）
@Configuration
public class RedisFeatureStore implements FeatureStore {
    private final RedisTemplate<String, byte[]> redisTemplate;

    @Override
    public void saveFeature(RoomFeature feature) {
        String key = "room:" + feature.getRoomId();
        redisTemplate.opsForValue().set(key + ":text", feature.getTextFeature());
        redisTemplate.opsForValue().set(key + ":embedding", serialize(feature.getEmbedding()));
    }

    @Override
    public ListLong> searchSimilar(float[] queryEmbedding, int topK) {
        // 使用Redis Vector Similarity Search (需RedisSearch模块)
        return redisTemplate.execute(
            (RedisCallback<ListLong>>) connection -> {
                connection.vectorSearch(
                    "room_idx", 
                    queryEmbedding, 
                    topK
                );
                // 转换结果...
            }
        );
    }
}

// 6. 控制器层（REST API）
@RestController
@RequestMapping("/api/recommend")
public class RecommendController {
    private final RecommenderService recommender;

    @PostMapping
    public ResponseEntity<ListRoom>> recommend(@RequestBody String userQuery) {
        return ResponseEntity.ok(recommender.recommend(userQuery));
    }
}
```

### 关键优化点说明：

1. **向量缓存机制**：

   ```mermaid
   graph LR
   A[用户输入] --> B(DeepSeek生成查询向量)
   C[房间特征] --> D[Redis向量存储]
   B --> E[向量相似度搜索]
   E --> F[召回候选集]
   ```

2. **异步更新策略**：

   ```java
   // 每天凌晨更新特征
   @Scheduled(cron = "0 0 3 * * ?")
   public void dailyUpdate() {
       ListLong> activeRoomIds = roomMapper.selectActiveRoomIds();
       featureProcessor.refreshFeatures(activeRoomIds);
   }
   ```

3. **混合排序策略**：

   ```java
   // 结合向量相似度和业务规则
   .sorted(room -> 
       0.7 * cosineSimilarity(room.getEmbedding()) +
       0.2 * (1 - room.getRent()/MAX_RENT) +
       0.1 * (room.getPopularity()/100)
   )
   ```

4. **快速过滤实现**：

   ```xml
   <!-- MyBatis动态过滤示例 -->
   select id="filterRooms" resultType="Room">
   SELECT * FROM room_info 
   WHERE id IN 
       foreach item="id" collection="ids" open="(" separator="," close=")">
           #{id}
       </foreach>
   AND is_deleted = 0
   AND is_release = 1
   if test="params.minRent != null">AND rent >= #{params.minRent}</if>
   </select>
   ```

### 部署架构：

```
                       +-----------------+
                       |   DeepSeek API  |
                       +--------+--------+
                                |
+----------------+      +-------v-------+     +------------+
|   Client App   +----->|  Spring Boot  +---->|   Redis    |
+----------------+      +-------+-------+     +-----+------+
                                |                   |
                                +--------+----------+
                                         |
                                  +------v------+
                                  |  MySQL      |
                                  +-------------+
```

### 性能优化建议：

1. **批处理特征更新**：

   ```java
   // 每次批量处理100个房间
   List<ListLong>> partitions = Lists.partition(activeRoomIds, 100);
   partitions.forEach(featureProcessor::refreshFeatures);
   ```

2. **向量压缩存储**：

   ```java
   // 使用FP16减少存储空间
   float[] embeddings = ...;
   byte[] compressed = Float16.toByteArray(embeddings);
   ```

3. **缓存预热**：

   ```java
   @PostConstruct
   public void init() {
       ListLong> hotRooms = roomMapper.selectHotRoomIds();
       featureProcessor.refreshFeatures(hotRooms);
   }
   ```

该方案通过将DeepSeek的语义理解能力与数据库过滤结合，实现了：

- **语义搜索**：理解"朝阳区精装修公寓"等复杂需求
- **实时过滤**：租金、支付方式等硬性条件筛选
- **混合排序**：结合语义相似度和业务规则
- **高效检索**：利用Redis向量搜索实现毫秒级响应

可根据实际需求调整各部分权重，建议先实现核心的向量搜索功能，再逐步添加业务过滤规则。

## 4. 依赖

### 1.Maven

```java
  <!-- HTTP 客户端（以Apache HttpClient为例） -->
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.5.13</version>
        </dependency>

        <!-- JSON 处理（推荐Jackson） -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.15.2</version>
        </dependency>
```

### 2.依赖说明

|        依赖项         |              作用              | 是否必需 |
| :-------------------: | :----------------------------: | :------: |
| **Apache HttpClient** | 发送 HTTP 请求到 DeepSeek API  |  ✔️ 必需  |
|      **Jackson**      | 处理 API 请求/响应的 JSON 数据 |  ✔️ 必需  |
|      **Lombok**       | 简化 Getter/Setter 等样板代码  |  ⚠️ 可选  |
|  **Spring Boot Web**  |      若需要构建 Web 服务       |  ⚠️ 可选  |

### 3.调用示例

```java
import org.apache.http.client.methods.*;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;

public class DeepSeekCaller {
    private static final String API_KEY = "your_api_key";
    
    public String getCompletion(String prompt) throws Exception {
        try (CloseableHttpClient httpClient = HttpClients.createDefault()) {
            HttpPost request = new HttpPost("https://api.deepseek.com/v1/chat/completions");
            
            // 设置请求头
            request.setHeader("Authorization", "Bearer " + API_KEY);
            request.setHeader("Content-Type", "application/json");
            
            // 构建请求体
            String jsonBody = String.format("{\"model\":\"deepseek-chat\",\"messages\":[{\"role\":\"user\",\"content\":\"%s\"}]}", prompt);
            request.setEntity(new StringEntity(jsonBody));
            
            // 发送请求并解析响应
            try (CloseableHttpResponse response = httpClient.execute(request)) {
                return EntityUtils.toString(response.getEntity());
            }
        }
    }
}
```

### 4. 注意事项

   1. **API 密钥管理**：推荐通过环境变量或配置文件注入密钥

      ```properties
      # application.properties
      deepseek.api.key=your_actual_key_here
      ```

   2. **依赖冲突解决**：若遇到版本冲突，可使用 Maven 的 `exclusions>` 排除旧版本

      ```xml
      dependency>
          groupId>some.group</groupId>
          artifactId>problematic-artifact</artifactId>
          exclusions>
              exclusion>
                  groupId>conflicting.group</groupId>
                  artifactId>conflicting-artifact</artifactId>
              </exclusion>
          </exclusions>
      </dependency>
      ```

   3. **生产环境建议**：

      - 添加连接池配置
      - 使用 OkHttp 代替 Apache HttpClient（性能更优）
      - 增加重试机制
