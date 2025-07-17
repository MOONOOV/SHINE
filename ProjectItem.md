# 构建工具
### 定义
1. 核心作用：自动化完成**项目管理**中的重复任务
2. 实现内容
	1. 编译代码
	2. 运行测试：执行单元测试和集成测试
	3. 打包发布：JAR（Java Archive）或WAR（Web Application Archive）
	4. 依赖解析：管理第三方库，解决依赖冲突问题
	5. 生成文档和报告
### Maven作为构建工具的具体流程
1. 解析POM文件：读取pom.xml，获取项目配置、依赖和构建规则
2. 下载依赖
3. 执行构建生命周期操作
	1. 清理：删除之前构建的临时文件
		```bash
	    mvn clean
		```
	2. 编译
		```powershell
	    mvn compile
		```
	3. 测试
		```powershell
	    mvn test
		```
	4. 打包
		```powershell
		mvn package
		```
	5. 部署
		```powershell
	    mvn install
		```
	6. 发布
		```powershell
	    mvn deploy
		```
4. 生成构建结果：构建产物和报告
# 数据格式
### JavaScript Object Notation
1. 定义：`JSON`是以文本格式来表示结构化数据的轻量级的数据交换格式
2. 特点
	1. **键值对结构**
	2. **支持多种数据类型**：包括String、Number、Boolean、Array、Object和Null
	3. **嵌套结构**
	4. 广泛支持
3. 示例
	```json
    {
	    "name": "John Doe", //String
	    "age": 30, //Number
	    "isStudent": false, //Boolean
	    "courses": ["Math", "Science"], //Array
	    "address":{ //Object
		    "street": "123 Main St",
		    "city": "Anytown"
		} //嵌套
	}
	```
### Comma-Separated Values
1. 定义：`CSV`用于存储表格数据
2. 格式：逗号分隔字段，换行符分隔记录
3. 特点
	1. 简单性
	2. 通用性
	3. 易于导出导入
	4. 不支持嵌套和复杂数据结构
4. 示例
	```csv
    Name,Age,City
    John Doe,30,Anytown
    Jane Smith,25,Otherville
	```
### eXtensible Markup Language
1. 定义：`XML`用于定义文档的结构和内容
2. 格式：使用**标签**标记数据
3. 特点
	1. 自描述性：标签可以描述数据的含义和结构
	2. 可扩展性：具有自定义标签和属性的功能
	3. 支持复杂数据结构
	4. 跨平台性
	5. 支持验证：使用XML Schema或DTD进行数据验证，以确保数据完整准确
4. 示例
	```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <person>
	    <name>John Doe</name>
	    <age>30</age>
	    <city>Anytown</city>
	    <courses>
		    <course>Math</course>
		    <course>Science</course>
		</courses>
	</person>
	```
# Maven
## 定义
1. 基于**项目对象模型**
2. 用于**自动化构建**、**依赖管理**和**项目报告生成**
### 核心功能
1. 依赖管理
	1. 用户只需在**pom.xml**文件中声明依赖项，Maven便会自动解析依赖关系并通过中心仓库（Maven Central）和本地仓库自动下载并管理第三方库和插件
2. 项目构建
	1. Maven提供标准化的构建流程，可预定义生命周期
3. 插件扩展
	1. 如集成测试JUnit、代码质量检查Checkstyle
## 使用框架
### 安装与配置
### 创建Maven项目
1. 使用**Archetype**生成项目骨架
	```powershell
    mvn archetype:generate 
		    -DgroupId=com.example
		    -DartifactId=my-app
		    -DarchetypeArtifactId=maven-archetype-quickstart #快速启动模型
		    #更换参数（如Web项目：maven-archetype-webapp）
		    -DinteractiveMode=false
	```
	1. **groupId**：项目组织标识
	2. **artifactId**：项目名称
	3. **archetypeArtifactId**：模板类型
2. 项目结构解析
	```powershell
    my-app/
	    ├── src/
		│ ├── main/
		│ │ ├── java/ # Java源代码
		│ │ └── resources/ # 资源文件
		│ └── test/
		│   ├── java/ # 测试代码
		│   └── resources/ # 测试资源
		├── pom.xml # 项目配置文件
	```
### 依赖管理
1. 在**pom.xml**中声明依赖
	```xml
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
			<version>3.1.5</version>
		</dependency> <?唯一标示依赖库?>
	</dependencies>
	```
2. 依赖范围Scope
	1. compile（默认）：主代码和测试代码均需要（如Spring核心库）
	2. test：仅测试代码需要（如JUnit）
	3. provided：编译时需要，但由JDK或容器提供（如Servlet API）
	4. runtime：运行时需要，编译时不需要（如JDBC驱动）。
3. 查看依赖树
	```bash
	mvn dependency:tree 
	```