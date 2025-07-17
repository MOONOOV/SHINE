# annotation
## Deterministic.java
1. 作用：JAVA注解类，用于标记方法
2. 内容：
	1. 保留策略：`RUNTIME`，指运行时可以通过反射机制获得该注解信息
	2. 目标类型：`METHOD`，该注解只作用于方法上
## FakeForSchema.java
1. 作用：JAVA注解类，用于指定一个类**使用的默认数据伪造模式**
2. 内容：
	1. 保留策略：`RUNTIME`
	2. 目标类型：`TYPE`，可以应用于类、接口、枚举等类型上
## FakeResolver.java
1. 作用：泛型类，用于根据**默认模式**或**指定的模式**生成对应的对象实例
2. 内容：
	1. 单例模式的工厂方法`of`：用于获取`FakeResolver`实例
3. 核心方法：
	- `of(Class<T> clazz)`：获取指定类的`FakeResolver`实例
	- `generate(Schema<Object, ?> schema)`：根据给定的模式生成对象，如果模式为空，则从默认模式生成
	- `generateFromDefaultSchema()`：从默认模式生成对象，会先检查类是否有`FakeForSchema`注解，然后获取并缓存默认模式
	- `getSchema(String pathToSchema)`：根据模式路径获取模式对象，支持通过类名和方法名来获取模式
	- `checkFakeAnnotation(Class<T> clazz)`：检查类是否有`FakeForSchema`注解，如果没有则抛出异常
# transformations
## 基本文件关系
1. 联系：
	1. `Field`是最基本的单元，定义了单个字段的转换规则
	2. `Schema`是`Field`的集合，定义了数据转换的整体结构
	3. `Transformer`则根据`Schema`中定义的规则，对输入数据进行转换，并生成输出数据
2. 实现逻辑：
	1. 自定义一个或多个`Field`字段，在`Field`中定义**转换方法**
	2. 将所有`Field`整合成一个模式`Schema`
	3. 使用已有转换器或自定义实现`Transformer`接口的转换器，将输入通过模式`Scheme`转为输出
	4. 执行时，数据最终会找到指定的`Field`来完成转换，因为转换方法被定义在`Field`中，对于`Schema`和`Transformer`是透明的
## Schema.java抽象类
1. 功能：
	1. 用于表示一组`Field`对象的集合
2. 变量：
	1. `fields`：私有不可变数组
3. 方法：
	1. 静态工厂方法`of`：
		1. 使用`@SafeVarargs`注解，避免堆污染警告
		2. 接受可变数量参数，返回新的`Schema`对象
	2. `equals`：比较两个`Schema`对象是否相等
	3. `hashCode`：用于计算`Schema`对象的**哈希码**
## Field.java泛型接口
1. 功能：处理数据转换
2. 方法：
	1. 静态工厂方法`field`：
		1. 使用`Function`
		2. 使用`Supplier`
		3. `SimpleField`是`Field`的实现类
	2. `compositeField`静态泛型方法：
		1. `MyObject`必须是`AbstractProvider`的子类
		2. `CompositeField`为`Field`的实现类
## Transformer.java泛型接口
1. 功能：
	1. 用于将输入数据根据指定的模式进行**转换和生成**
	2. 支持**流处理**和输出到`OutputStream`
2. 常量
	1. `LINE_SEPARATOR`：表示生成数据的行分隔符
3. 方法
	1. `apply`：将输入对象`input`根据模式`schema`转换成新对象，并返回该对象
	   ```java
		OUT apply(IN input, Schema<IN, ?> schema);
		OUT apply(IN input, Schema<IN, ?> schema, long rowId); //默认情况下使用第一种方法
		```
	2. `generate`：
	    ```java
		OUT generate(Iterable<IN> input, final Schema<IN, ?> schema);
		// 接受一个可迭代的输入对象集合input和一个Schema对象，根据输入集合和模式生成输出对象
		OUT generate(final Schema<IN, ?> schema, int limit);
		// 接受一个Schema对象和一个整数limit，根据模式生成指定数量的输出对象
		```
	3. 流处理方法
	    ```java
		String getStartStream(final Schema<IN, ?> schema); // 返回流式处理开始时的字符串
		String getEndStream(); // 返回流式处理结束时的字符串
		String getLineSeparator(); // 返回行分隔符，默认使用系统的行分隔符
		String getElementSeparator(); // 返回元素分隔符，默认为空字符串
		Stream<OUT> generateStream(final Schema<IN, ?> schema, long limit); // 根据指定的模式和数量生成一个Stream对象，用于流式处理输出
		```
	4. 输出到`OutputStream`方法：
		```java
		void writeToOutputStream(OutputStream outputStream, final Schema<IN, ?> schema, long limit);
		// 将根据指定模式和数量生成的输出对象写入到指定的OutputStream中
		```
	5. 内部类：`Item`用于记录当前生成的行号
## JavaObjectTransformer.java
1. 本质：是对接口`Transformr<Object><Object>`的实现
2. 功能：将输入对象转换为符合特定模式的新对象