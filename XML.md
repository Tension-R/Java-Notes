# XML
## 1. 解析XML文档
- java提供了两种XML解析器
	- 文档对象模型(DOM)解析器这样的树型解析器，它们将读入的XML文档转换成树结构。
	- XML简单API(SAX)解析器这样的流机制解析器，他们在读入XML文档时生成相应的事件。
- getDocumentElement方法可以返回根元素。
- getTagName方法返回元素的标签名。
- getChildNodes方法返回一个NodeList集合，得到钙元素的子元素。item方法得到指定索引值的项。
- 分析子元素时，标签与标签之间的空白字符也算子元素。
- 如果只希望得到子元素可以用instanceof Element方法。

## 2. 验证XML文档
- 可以使用文档类型定义（DTD）或一个XML Schema指定文档结构。它们包含了用于解释文档应如何构成的规则，这些规则制定了每个元素的合法子元素和属性。
- 例如DTD可能含有一个规则：
	- <!ElEMENT font (name,size)>
	- 表示，一个font元素必须总有两个子元素，分别是name和size。

### 1. 文档类型定义
- 一般把DTD存储在文档外面，SYSTEM声明可以用来实现这个目标。如：
	- <!DOCTYPE ××× SYSTEM "×××.dtd"> 也可以指定一个包含DTD的URL。

- 元素的规格说明可以包含嵌套的和复杂的正则表达式。
- 在设计DTD时，最好让其中所有元素要么包含其他元素，要么只有文本。
- 可以指定描述合法的元素属性的规则。通用语法为：
	- <!ATTLIST *element attribute type default* >
	- 例如：<!ATTLIST font styel (plain|bold) "plain">

- 一般使用元素而非属性来描述数据，但是属性有一个有点，就是解析器能校验取值是否合法。
- 使用DTD时应通知文档生成工厂打开验证特性：factory.setValidation(true)。
- 一旦DTD制定了子元素是（name，size），解析器就知道它们之间的空白字符不是文本。
- 调用方法：factory.setIgnoringElementContentWhitespace(true)，生成器将不会报告文本节点中的空白字符。

### 2. XML Schema
- 如果要引用Schema文件，需要在根元素中添加属性。
- 解析带有Schema的XML文件时
	- 必须打开对命名空间的支持：factory.setNamespaceAware(true)
	- 解析器不会丢弃元素中的空白字符。

## 3. 使用XPath定位信息
- XPath的索引从1开始。
- 需要先从XPathFactory创建一个XPath对象：
	- XPathFactory xpfactory = XPathFactory.newInstance();
	- path = xpfactory.newPath();

- 然后调用evalute方法来计算XPath表达式：
	- String username = path.evalute("/configuration/database/username", doc);
- 如果XPath表达式产生了一组节点
	- NodeList nodes = (NodeList) path.evaluate("/××××/××××", doc, XPathConstants.NODESET);

- 如果只有一个节点，可以用XPathConstants.NODE代替。
- 如果是数字，可以用XPathConstants.NUMBER代替。
- 不必从根节点开始搜索，从任意一个节点或节点列表开始都可以。

## 4. 流机制解析器
### 1. SAX解析器
- 得到SAX解析器：
	- SAXParserFactory factory = SAXParserFactory.newInstance():
	- SAXParser parser = factory.new SAXParser();
- 处理文档：
	- parser.parse(source,handler);
	- source可以是一个文件，URL字符串或者输入流。
	- handler是处理器，用来为不同的解析器事件定义事件动作，属于DefaultHandler的一个子类。

### 2. StAX解析器
- StAX解析器是一种”拉解析器“，与安装事件处理器不同，只需要使用基本循环来迭代所有事件。

## 5. JDOM解析
- 实例化SAXBuilder对象。
- 使用build方法加载目标文件/输入流。

## 6. DOM4J解析
- 实例化SAXReader对象。
- 使用read方法加载目标文件/输入流。
- 不能直接加载文件名，余姚加载File对象。
- 通过element对象的elementIterator方法获取迭代器

## 7. 四种解析方式比较
- DOM解析一次性将XML文件加载到内存，并生成DOM树。
- SAX解析在不同的位置触发不同的事件。
- JDOM只用具体类，不使用接口。
- DOM4J性能优异，灵活性好，基于接口和抽象类。
- 建议使用DOM4J进行解析。

## 8. 生成XML文档
### 1. 不带命名空间的文档
- 调用DocumentBuilder类的newDocument方法可以得到一个空文档。
- 使用Document类的createElement方法可以构建文档里的元素。
- 使用createTextNode可以创建文本节点。
- 可以使用appendChild方法给文档添加根元素，给父节点添加子节点。
- 使用setAttribute方法设置属性。

### 2. 带命名空间的文档
- 将工厂设置为命名空间敏感的，再创建生成器：factory.setNamespaceAware(true)。
- 使用CreateElementNS创建所有节点。
- 使用setAttributeNS设置元素属性。

### 3. DOM生成文件
- 创建transformerfactory接口的代理。
- 创建transformer对象。
- 用transform方法生成文件。

### 4. SAX生成文件
- 创建SAXtransformerfactory接口的代理。
- 创建TransformerHandler对象。
- 创建transformer对象。
- 创建result对象并用handler的sendResult方法与之关联。
- 通过startElement和endElement方法创建节点。
- 通过characters方法添加文本。

### 4. DOM4J生成文件
- 使用DocumentHelper的createDocument方法创建Document对象。
- 通过addElement方法添加节点。
- 通过addAttribute方法添加属性。
- 创建XMLWriter对象。
- 通过write方法生成文件。
- 通过setText方法添加文本。
- 通过setEscapeText(false)方法设置不转义。

### 5. JDOM生成文件
- 生成一个根节点。
- 通过根节点创建document对象。
- 创建XMLOutputter对象
- 通过output方法把document对象转换成文档。
- 通过addContent方法添加子节点。
- 通过setText方法设置文本。
