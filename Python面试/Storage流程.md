1. **`Character.objects.create(...)`**:
   - Django 开始创建一个新的 `Character` 实例。
   - 它将传入的数据（包括 `photo` 和 `background_image` 文件对象）赋值给模型实例的相应字段。
2. **`Character` 实例的 `save()` 方法被隐式调用**:
   - `create()` 方法内部会调用新创建实例的 `save()` 方法来将其持久化到数据库。
3. **处理 `photo` 字段 (`ImageField`)** :
   - Django 检测到 `photo` 是一个 `FileField` (或其子类 `ImageField`)。
   - 它会检查该字段是否有新的文件需要保存（即 `request.FILES.get('photo')` 不为空）。
4. **`FileField.pre_save()`**:
   - Django 调用 `photo` 字段的 `pre_save` 方法。这个方法负责准备文件的保存。
5. **`FieldFile.save()`**:
   - `pre_save` 进一步调用与该字段关联的 `FieldFile` 对象（`character_instance.photo`）的 `save()` 方法。
6. **`Storage.get_available_name()`**:
   - `FieldFile.save()` 会先确保文件名是唯一的。它调用存储后端的 `get_available_name()` 方法。
   - 这个方法内部会调用 `get_valid_name()` 来清理文件名，然后循环调用 `exists()` 来检查该名称的文件是否已存在。
7. **`Storage.exists()`**:
   - 为了检查文件名是否可用，Django 会调用您实现的 `AliyunOSSMediaStorage.exists()` 方法。
   - 您的 `exists` 方法会向OSS发送一个 `HEAD` 请求来检查文件是否存在。
8. **`Storage._save()`**:
   - 当确定了最终的文件名（例如 `user/photos/1_foo.png`）后，Django 调用您实现的 `AliyunOSSMediaStorage._save()` 方法。
   - 您的 `_save` 方法接收文件名和文件内容，然后将文件上传到OSS。
9. **处理 `background_image` 字段**:
   - 对 `background_image` 字段，Django 会重复执行第 3 到第 8 步的整个流程。
10. **`Model.save()` (数据库)**:
    - 当两个文件都成功上传到OSS后，Django 会生成包含新文件路径的SQL语句，并将 `Character` 实例的记录插入到数据库中。数据库中的 `photo` 和 `background_image` 字段将分别存储它们在OSS上的路径。

**总结一下，对于每个上传的文件（`photo` 和 `background_image`），执行顺序是：**

`FileField.pre_save()` -> `FieldFile.save()` -> `Storage.get_available_name()` -> `Storage.exists()` -> `Storage._save()`