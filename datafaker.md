# Datafaker介绍
## 核心功能
1. 拥有多种场景和领域的数据提供者
	1. 数据提供者：指`Faker`对象生成数据的方法
2. 支持生成多种语言数据
3. 通过**表达式语法**自定义数据格式
4. 可实现唯一值生成
5. 可将数据转换生成嵌套的`JSON`、`XML`或自定义结构对象
## 应用场景
1. 软件开发测试生成模拟数据
2. 数据库填充示例数据
3. 原型开发生成测试数据验证产品设计
4. 作为示例数据进行教学演示
# 使用指南
## 添加依赖
1. 在`maven`项目的`pom.xml`文件中添加
	```xml
	<dependency>
	    <groupId>net.datafaker</groupId>
	    <artifactId>datafaker</artifactId>
	    <version>2.4.3</version> <!-- 使用最新稳定版本 -->
	</dependency>
	```
## 基本用法
### 引入
```java
import net.datafaker.Faker;
```
### 创建
```java
// 默认使用英语：Locale.ENGLISH
Faker faker = new Faker();
// 使用指定语言环境
Faker chineseFaker = new Faker(Locale.CHINA); // Locale => 场所或地点
```
### 生成各类假数据
```java
// 个人信息
String fullName = faker.name().fullName();    // "John Smith"
String fullName = chineseFaker.name().fullName();    // "昊朝王"
String address = faker.address().fullAddress(); // "123 Fake St, Springfield, IL 62704"

// 数字和随机值
int randomNumber = faker.number().numberBetween(1, 100); // 54
double randomDouble = faker.number().randomDouble(2, 1, 100); // 42.35

// 文本内容
String sentence = faker.lorem().sentence(); // "Lorem ipsum dolor sit amet..."
String paragraph = faker.lorem().paragraph(); // 包含多个句子的段落

// 日期和时间
String pastDate = faker.date().past(30, java.util.concurrent.TimeUnit.DAYS).toString();
String futureDate = faker.date().future(90, java.util.concurrent.TimeUnit.DAYS).toString();

// 网络数据
String email = faker.internet().emailAddress(); // "john.smith@example.com"
String ipAddress = faker.internet().ipV4Address(); // "192.168.1.1"

// 商业数据
String companyName = faker.company().name(); // "Acme Corporation Inc."
String creditCard = faker.finance().creditCard(); // "4111-1111-1111-1111"
```
## 高级特性
### 表达式语法
使用`#{dataProvider.method}`语法实现**自定义数据格式**
```java
String customFormat = faker.expression("#{name.lastName}-#{number.numberBetween(100, 999)}-#{address.city}");
// 输出示例："李-528-北京"

// 嵌套表达式
String complexFormat = faker.expression("#{commerce.productName} by #{company.name} costs $#{commerce.price}");
// 输出示例："Smartphone by TechCorp costs $799.99"
```
### 生成唯一值
```java
// 生成唯一的数字
Integer uniqueNumber = faker.unique().fetchFromYaml("numbers");

// 生成唯一的姓名（在一定范围内）
String uniqueName = faker.unique().name().fullName();

// 注意：唯一值生成有存在上限，超出后会抛出 NoSuchElementException
```
### 生成`JSON`/`XML`数据
```java
// 生成 JSON
String json = faker.formats().json()
    .field("id", "#{number.randomNumber}")
    .field("name", "#{name.fullName}")
    .field("email", "#{internet.emailAddress}")
    .field("age", "#{number.numberBetween(18, 60)}")
    .generate();

// 生成 XML（已弃用，建议使用 JSON 或自定义对象）
String xml = faker.formats().xml()
    .tag("user")
    .field("name", "#{name.fullName}")
    .field("age", "#{number.numberBetween(18, 60)}")
    .generate();
```
### 自定义提供者
```java
// 创建自定义提供者
Faker faker = new Faker();
faker.addProvider("game", new AbstractProvider(faker) {
    private final String[] games = {"LoL", "Dota 2", "CSGO", "Overwatch"};
    
    public String randomGame() {
        return faker.options().option(games);
    }
});

// 使用自定义提供者
String game = faker.game().randomGame(); // "Dota 2"
```