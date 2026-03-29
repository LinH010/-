## **1. 布局 (Layout)**

### **容器与盒子模型**

表格



| 类名           | 描述                                                       |
| :------------- | :--------------------------------------------------------- |
| `container`    | 居中的响应式容器，宽度随屏幕尺寸变化                       |
| `box-border`   | 设置 `box-sizing: border-box`                              |
| `box-content`  | 设置 `box-sizing: content-box`                             |
| `block`        | 设置 `display: block`                                      |
| `inline-block` | 设置 `display: inline-block`                               |
| `inline`       | 设置 `display: inline`                                     |
| `flex`         | 设置 `display: flex`                                       |
| `inline-flex`  | 设置 `display: inline-flex`                                |
| `table`        | 设置 `display: table`                                      |
| `hidden`       | 设置 `display: none`                                       |
| `w-screen`     | 宽度为视口宽度 (`100vw`)                                   |
| `w-full`       | 宽度为父元素的 `100%`                                      |
| `h-screen`     | 高度为视口高度 (`100vh`)                                   |
| `h-full`       | 高度为父元素的 `100%`                                      |
| `min-h-screen` | 最小高度为视口高度                                         |
| `p-4`          | 内边距 (padding) 为 `1rem` (16px)                          |
| `py-2`         | 上下内边距 (padding-top/padding-bottom) 为 `0.5rem` (8px)  |
| `px-4`         | 左右内边距 (padding-left/padding-right) 为 `1rem` (16px)   |
| `m-4`          | 外边距 (margin) 为 `1rem` (16px)                           |
| `my-2`         | 上下外边距 (margin-top/margin-bottom) 为 `0.5rem` (8px)    |
| `mx-auto`      | 左右外边距 (margin-left/margin-right) 自动，常用于水平居中 |

### **Flexbox**

表格



| 类名              | 描述                                                         |
| :---------------- | :----------------------------------------------------------- |
| `flex-row`        | 主轴方向为行 (`flex-direction: row`)                         |
| `flex-col`        | 主轴方向为列 (`flex-direction: column`)                      |
| `items-center`    | 交叉轴居中对齐 (`align-items: center`)                       |
| `items-start`     | 交叉轴起点对齐 (`align-items: flex-start`)                   |
| `items-end`       | 交叉轴终点对齐 (`align-items: flex-end`)                     |
| `justify-center`  | 主轴居中对齐 (`justify-content: center`)                     |
| `justify-between` | 主轴两端对齐 (`justify-content: space-between`)              |
| `justify-around`  | 主轴均匀分布，元素周围有相等空间 (`justify-content: space-around`) |
| `flex-wrap`       | 允许换行 (`flex-wrap: wrap`)                                 |
| `flex-1`          | 等价于 `flex: 1 1 0%`，允许元素伸缩以填充可用空间            |
| `flex-grow`       | 设置 `flex-grow: 1`，允许元素增长                            |
| `flex-shrink`     | 设置 `flex-shrink: 1`，允许元素收缩                          |

### **Grid**

表格



| 类名                                        | 描述                                          |
| :------------------------------------------ | :-------------------------------------------- |
| `grid`                                      | 设置 `display: grid`                          |
| `grid-cols-3`                               | 创建 3 列的网格布局                           |
| `grid-cols-1 md:grid-cols-2 lg:grid-cols-4` | 响应式网格，从小屏 1 列，中屏 2 列，大屏 4 列 |
| `gap-4`                                     | 设置网格项之间的间隙为 `1rem` (16px)          |
| `col-span-2`                                | 元素跨越 2 列                                 |

## **2. 背景 (Backgrounds)**

表格



| 类名            | 描述                                            |
| :-------------- | :---------------------------------------------- |
| `bg-white`      | 白色背景                                        |
| `bg-black`      | 黑色背景                                        |
| `bg-gray-100`   | 浅灰色背景                                      |
| `bg-blue-500`   | 蓝色背景                                        |
| `bg-opacity-50` | 背景色透明度为 50%                              |
| `bg-cover`      | 背景图片覆盖整个容器 (`background-size: cover`) |
| `bg-center`     | 背景图片居中 (`background-position: center`)    |
| `bg-no-repeat`  | 背景图片不重复 (`background-repeat: no-repeat`) |

## **3. 边框 (Borders)**

表格



| 类名              | 描述                    |
| :---------------- | :---------------------- |
| `border`          | 添加默认边框            |
| `border-2`        | 设置边框宽度为 `2px`    |
| `border-t-2`      | 只在顶部添加 `2px` 边框 |
| `border-blue-500` | 设置边框颜色为蓝色      |
| `rounded`         | 设置圆角                |
| `rounded-lg`      | 设置较大圆角            |
| `rounded-full`    | 设置为圆形/椭圆形       |

## **4. 效果 (Effects)**

表格



| 类名         | 描述             |
| :----------- | :--------------- |
| `shadow`     | 添加默认阴影     |
| `shadow-md`  | 添加中等阴影     |
| `shadow-xl`  | 添加更大阴影     |
| `opacity-50` | 设置透明度为 50% |

## **5. 间距 (Spacing)**

- **内边距 (Padding)**: `p-{size}`, `pt-{size}`, `pr-{size}`, `pb-{size}`, `pl-{size}`, `px-{size}`, `py-{size}`
- **外边距 (Margin)**: `m-{size}`, `mt-{size}`, `mr-{size}`, `mb-{size}`, `ml-{size}`, `mx-{size}`, `my-{size}`
- **常用尺寸**:
  - `0`: 0px
  - `1`: 0.25rem (4px)
  - `2`: 0.5rem (8px)
  - `3`: 0.75rem (12px)
  - `4`: 1rem (16px)
  - `5`: 1.25rem (20px)
  - `6`: 1.5rem (24px)
  - `8`: 2rem (32px)
  - `10`: 2.5rem (40px)
  - `12`: 3rem (48px)
  - `16`: 4rem (64px)
  - `20`: 5rem (80px)
  - `24`: 6rem (96px)
  - `32`: 8rem (128px)
  - `40`: 10rem (160px)
  - `48`: 12rem (192px)
  - `56`: 14rem (224px)
  - `64`: 16rem (256px)

## **6. 文本 (Typography)**

表格



| 类名              | 描述                   |
| :---------------- | :--------------------- |
| `font-sans`       | 使用系统默认无衬线字体 |
| `font-serif`      | 使用衬线字体           |
| `font-mono`       | 使用等宽字体           |
| `font-normal`     | 正常字体粗细           |
| `font-medium`     | 中等粗细               |
| `font-semibold`   | 半粗体                 |
| `font-bold`       | 粗体                   |
| `text-xs`         | 极小号字体             |
| `text-sm`         | 小号字体               |
| `text-base`       | 基准字号 (默认)        |
| `text-lg`         | 大号字体               |
| `text-xl`         | 特大号字体             |
| `text-2xl`        | 2倍基准字号            |
| `text-3xl`        | 3倍基准字号            |
| `text-4xl`        | 4倍基准字号            |
| `text-5xl`        | 5倍基准字号            |
| `text-6xl`        | 6倍基准字号            |
| `text-left`       | 文本左对齐             |
| `text-center`     | 文本居中对齐           |
| `text-right`      | 文本右对齐             |
| `text-white`      | 白色文字               |
| `text-black`      | 黑色文字               |
| `text-gray-800`   | 深灰色文字             |
| `text-blue-500`   | 蓝色文字               |
| `text-opacity-75` | 文字颜色透明度为 75%   |
| `italic`          | 斜体                   |
| `uppercase`       | 全部大写               |
| `lowercase`       | 全部小写               |
| `capitalize`      | 首字母大写             |
| `underline`       | 下划线                 |
| `line-through`    | 删除线                 |
| `no-underline`    | 无下划线               |
| `tracking-wide`   | 字符间距变宽           |
| `leading-relaxed` | 行高更松散             |
| `leading-loose`   | 行高最松散             |

## **7. 交互 (Interactivity)**

表格



| 类名                               | 描述                                 |
| :--------------------------------- | :----------------------------------- |
| `cursor-pointer`                   | 鼠标悬停时显示手型光标               |
| `cursor-not-allowed`               | 鼠标悬停时显示禁止符号               |
| `resize`                           | 元素可调整大小                       |
| `select-none`                      | 禁止选中文本                         |
| `appearance-none`                  | 移除元素的默认外观 (如 input 的轮廓) |
| `outline-none`                     | 移除焦点时的轮廓                     |
| `focus:outline-none`               | 在 `:focus` 状态下移除轮廓           |
| `focus:ring-2 focus:ring-blue-500` | 在 `:focus` 状态下添加蓝色环形高亮   |

## **8. 状态 (State Variants)**

Tailwind 支持在类名前加上状态前缀，以定义不同交互状态下的样式。

表格



| 前缀           | 对应 CSS 伪类    | 描述                                      |
| :------------- | :--------------- | :---------------------------------------- |
| `hover:`       | `:hover`         | 鼠标悬停                                  |
| `focus:`       | `:focus`         | 元素获得焦点                              |
| `active:`      | `:active`        | 元素被激活（按下）                        |
| `disabled:`    | `:disabled`      | 元素被禁用                                |
| `group-hover:` | `.group:hover &` | 父级元素有 `group` 类时，子元素的悬停状态 |

**示例:**

html



```
<button class="bg-blue-500 hover:bg-blue-700 text-white py-2 px-4 rounded">
  Hover me
</button>
<!-- 悬停时背景色会变为更深的蓝色 -->
```

## **9. 响应式设计 (Responsive Design)**

Tailwind 提供了一套完整的断点系统，可以在不同屏幕尺寸下应用不同的样式。

表格



| 断点前缀 | 最小宽度 | CSS                          |
| :------- | :------- | :--------------------------- |
| (none)   | 0        | `@media screen`              |
| `sm:`    | 640px    | `@media (min-width: 640px)`  |
| `md:`    | 768px    | `@media (min-width: 768px)`  |
| `lg:`    | 1024px   | `@media (min-width: 1024px)` |
| `xl:`    | 1280px   | `@media (min-width: 1280px)` |
| `2xl:`   | 1536px   | `@media (min-width: 1536px)` |

一、介绍

林昊，闽江学院软件工程专业2025届毕业生，绩点3.32/4.0，专业排名2/49。持有中级软件设计师证书，曾在蓝桥杯等竞赛中获奖，技术功底扎实。他精通Java全栈开发，熟悉Spring Boot、Vue等技术栈，并对前沿AI技术充满热情。他曾参与电网调度系统开发，积累了企业级项目经验；近期他独立研发了「AI伙伴」项目，一个融合LangGraph智能体、个性化记忆与实时语音合成的智能对话平台，旨在为用户打造高度拟人化的AI交流伴侣。目前他希望将丰富的项目实战经验与对AI技术的钻研热情相结合，寻求AI应用初级工程师、Java初级工程师或Python初级工程师岗位。

二、对话风格要求：

按范例风格写

专业自信且富有热情，逻辑清晰，善于用具体项目细节和技术栈说明能力，表达时自然流露出对技术的热爱和钻研精神，语气沉稳但不死板，展现出有实践经验且愿意持续学习的年轻工程师形象。

三、示例：

在2025年3月至5月的电网调度系统项目中，我担任全栈开发工程师，核心技术栈为后端Java Spring Boot与Mybatis-Plus，以及前端Vue.js。我负责优化SQL性能、迁移数据表，并修复了多个前端功能缺陷，这让我对复杂的企业级业务和数据处理有了更深的理解。

在2025年12月至2026年3月的「AI伙伴」项目中，我独立完成了开发，核心技术栈为前端Vue3/TailwindCSS与后端Django/Django REST Framework，并深度整合了LangChain与LangGraph框架，数据库采用MySQL。其核心功能包括：1. 智能对话：基于大语言模型实现自然流畅的多轮对话；2. 个性化记忆：通过LangGraph与数据库，使AI能记住用户姓名、兴趣等信息，实现连贯交流；3. 工具调用与RAG：集成查询当前精确时间、阿里云百炼平台相关信息等工具，并自建知识库，让AI能自主查询信息，有效解决幻觉问题；4. 实时语音：支持语音识别、流式TTS合成与音色克隆，提供沉浸式交互体验。

这个项目让我深刻体会到，将前沿的AI框架与成熟的Web技术相结合，可以创造出真正实用且有趣的智能产品，也让我在AI应用工程实践上取得了长足进步。

四、禁止行为：

按范例对仗工整地写

不夸大项目成果或回避技术细节；

不使用空洞的模板化语言；

不含糊其辞或只讲优势不提挑战；

不表现出消极或缺乏学习热情的态度。