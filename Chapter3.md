#第三章 函数

![alt text][1]

图片来源：[Chapter 3. Functions][2]

无论是在何种编程语言中，函数都是最常用的语法结构了。

根据需求写出函数不难，但写好一个函数却并不容易。

请看下面的一个函数，盯住它三分钟，看看能不能看懂这个函数在做什么：

**代码清单1**
<pre>
public static String testableHtml( 
	PageData pageData,
	boolean includeSuiteSetup
) throws Exception {
	WikiPage wikiPage = pageData.getWikiPage(); 
	StringBuffer buffer = new StringBuffer(); 
	if (pageData.hasAttribute("Test")) {
		if (includeSuiteSetup) { 
			WikiPage suiteSetup = PageCrawlerImpl
				.getInheritedPage( SuiteResponder.SUITE_SETUP_NAME, wikiPage);
			if (suiteSetup != null) {
				WikiPagePath pagePath = suiteSetup.getPageCrawler()
					.getFullPath(suiteSetup);
				String pagePathName = PathParser.render(pagePath); 
				buffer.append("!include -setup .")
					.append(pagePathName) .append("n");
			} 
		}
		WikiPage setup = PageCrawlerImpl.getInheritedPage("SetUp", wikiPage);
		if (setup != null) { 
			WikiPagePath setupPath = wikiPage.getPageCrawler()
				.getFullPath(setup); 
			String setupPathName = PathParser.render(setupPath); 
			buffer.append("!include -setup .").append(setupPathName) 
				.append("n");
		} 
	}
	buffer.append(pageData.getContent()); 
	if (pageData.hasAttribute("Test")) {
		WikiPage teardown = PageCrawlerImpl
				.getInheritedPage("TearDown", wikiPage);
		if (teardown != null) { 
			WikiPagePath tearDownPath = wikiPage.getPageCrawler()
				.getFullPath(teardown);
			String tearDownPathName = PathParser.render(tearDownPath); 
			buffer.append("n").append("!include -teardown .") 
				.append(tearDownPathName) 
				.append("n");
		}
		if (includeSuiteSetup) { 
			WikiPage suiteTeardown = PageCrawlerImpl
				.getInheritedPage( SuiteResponder.SUITE_TEARDOWN_NAME, wikiPage);
			if (suiteTeardown != null) {
				WikiPagePath pagePath = suiteTeardown.getPageCrawler()
					.getFullPath (suiteTeardown);
				String pagePathName = PathParser.render(pagePath); 
				buffer.append("!include -teardown .")
					.append(pagePathName) .append("n");
			} 
		}
	} 
	pageData.setContent(buffer.toString()); return pageData.getHtml();
}
</pre>
是不是搞懂了？很难吧！

如果做几个简单的方法抽离和重命名，就能在9行代码里搞定，再用三分钟阅读下面的代码，理解了吗？

**代码清单2**
<pre>
public static String renderPageWithSetupsAndTeardowns( 
	PageData pageData, boolean isSuite
) throws Exception {
	boolean isTestPage = pageData.hasAttribute("Test"); 
	if (isTestPage) {
		WikiPage testPage = pageData.getWikiPage(); 
		StringBuffer newPageContent = new StringBuffer(); 
		includeSetupPages(testPage, newPageContent, isSuite); 
		newPageContent.append(pageData.getContent()); 
		includeTeardownPages(testPage, newPageContent, isSuite); 
		pageData.setContent(newPageContent.toString());
	}
	return pageData.getHtml(); 
}
</pre>

看出差别了吧！第一个函数非常难懂，第二个却极易阅读和理解。

该函数包含把一些设置和拆解页放入一个测试页面，再渲染为html，属于某个基于Web的测试框架。

怎样才能让一个函数易于阅读和理解，并清晰表达其意图？这就需要注意以下几点：

**一，短小**

**函数的第一规则是要短小，第二条规则是还要更短小。**

短小到最好每个函数只有两行、三行或四行长。

上面的第二个函数还是不够短小，经再次重构后：

**代码清单3**
<pre>
public static String renderPageWithSetupsAndTeardowns( 
	PageData pageData, boolean isSuite) throws Exception { 
	if (isTestPage(pageData))
		includeSetupAndTeardownPages(pageData, isSuite); 
		return pageData.getHtml();
}
</pre>
代码块和缩进

    if语句，else语句，while语句等，这种代码块在函数中，应该只有一行。
这就是说，函数不应该大到足以容纳嵌套结构。函数的缩进层级不该多于一层或两层。

**二，只做一件事**

**函数应该做一件事。做好这件事，只做这一件事**（重说三）。

但是什么算是一件事？

例如上面的代码（代码清单3），也很容易看成做了三件事：

<pre>
判断是否为测试页面

如果是，则容纳近设置和分拆步骤

渲染成HTML
</pre>
那么，这个函数是做了一件事，还是三件事？

注意，此时要理解“函数抽象层”的概念：

如果函数只是做了该函数名下同一抽象层的步骤，那么该函数还是只做了一件事。

编写函数，就是为了把大一些的概念（函数抽象层）拆分为另一抽象层上的一系列步骤。

“代码清单1”明显的包含了多个不同的抽象层级，做了不止一件事。

“代码清单2”也有两个抽象层。

你会发现，很难将“代码清单3”再缩短。这说明该抽象层级已经无法再被拆分了。再拆分下去，就是把if语句和includeSetupAndTeardownPages等语句合并出一个函数来，但那只是重新诠释，并未改变抽象层级。

所以，要判断函数是否不止做了一件事，有一个好办法，**就是看能否再拆出一个函数，该函数不应只是单纯的重新诠释其实现。**

**函数中的区段**

注意，如果一个函数内部可以被分为多个“区段”，例如declarations,initialization等等区段，就是该函数做事太多的征兆。**只做一件事的函数无法被合理地切分为多个区段。**

**三，每个函数一个抽象层级**

要确保函数只做一件事，函数中的语句都要在同一抽象层级上。

例如“代码清单1”里面有getHtml()等位于较高抽象层的代码，也有pagePathName=PathPaser.render(pagePath)的中间抽象层，还有.append("n")相当低的抽象层。

函数中混杂不同抽象层级，往往令人迷惑，更恶劣的事，就象“破窗效应”，一旦抽象层级混杂，更多的细节和抽象层级就会在函数中纠结起来。

**四，函数的排列（阅读）顺序：自顶向下**

**我们要让代码拥有自顶向下的阅读顺序，要让每个函数后面都跟着下一个抽象层级的函数。**

这样一来，在查看函数列表时，就能循着抽象层级向下阅读了。

**五，处理好Switch语句**

写出短小的，只做一件事的switch语句很难。即便是只有两个条件的switch语句，也比单个代码块大得多。

例如以下代码：

**代码清单4**

<pre>
public Money calculatePay(Employee e) throws InvalidEmployeeType {
	switch (e.type) { 
		case COMMISSIONED:
			return calculateCommissionedPay(e); 
		case HOURLY:
			return calculateHourlyPay(e); 
		case SALARIED:
			return calculateSalariedPay(e); 
		default:
			throw new InvalidEmployeeType(e.type); 
	}
}
</pre>
该函数有好几个问题，

1，它太长，当出现新的雇员类型时，还会更长。

2，明显不止做了一件事。

3，违反单一权责原则(Single Responsibility Principle)

4, 违反开放闭合原则(Open Closed Principle)

最麻烦的是，函数calculatePay(Employee e)是根据不同的员工调用不同的函数，而这种函数肯定不止一个，类似的switch结构还可能出现在其他的与员工有关的函数中。

例如可能会有：isPayday(Employee e, Date date) ，deliverPay(Employee e, Money pay)等等有同样麻烦的函数。

该问题的解决方案，是将switch语句埋在抽象工厂里面，不可见于其他类。该工厂用switch为Employee的派生类创建适当的对象，而不同的函数，如calculatePay,isPayday和deliverPay等，则由Employee接口多态地接受和处理。

对于switch语句的处理办法是，可以只出现一次，用于创建多态对象，而且隐藏在某个继承关系中，对系统里其他部分不可见。

请尽量达到这一点，可能有违反这条规矩的时候，但需要有非常特殊的理由。

**代码清单5**
<pre>
public abstract class Employee {
	public abstract public abstract public abstract
	boolean isPayday();
	Money calculatePay();
	void deliverPay(Money pay);
}
public interface EmployeeFactory {
	public Employee makeEmployee(EmployeeRecord r) 
		throws InvalidEmployeeType; 
}
public class EmployeeFactoryImpl implements EmployeeFactory {
	public Employee makeEmployee(EmployeeRecord r) 
		throws InvalidEmployeeType { 
			switch (r.type) {
				case COMMISSIONED:
					return new CommissionedEmployee(r) ;
				case HOURLY:
					return new HourlyEmployee(r);
				case SALARIED:
					return new SalariedEmploye(r);
				default:
					throw new InvalidEmployeeType(r.type);
			} 
	}
}
</pre>
**六，使用描述性的名称**

好的名称要能够较好的描述函数所做的事情。例如isTestable或includeSetupAndTeardownPages。

函数越短小，功能越单一，就越便于取个好名字。

别害怕长名称，长而有描述性的名称，要比短而令人费解的名称好。

别害怕花时间取名字。

选择描述性的名称，能理清你关于模块的设计思路，并帮你改进之。追索好名称，往往导致对代码的改善重构。

命名方式要保持一致，使用与模块名一脉相承的短语、名词和动词给函数命名。

例如，includeSetupAndTeardownPages，includeSetupPages,includeSuiteSetupPage和includeSetupPage等。

**七，善用函数参数**

最理想的参数数量是零，其次是一，再次是二，尽量避免三。有足够特殊的理由才能用三个以上的参数。

参数带有太多的概念性，所以能不用尽量不用。参数与函数名处在不同的抽象层级，它要求你了解目前并不重要的细节（例如StringBuffer类型的参数)。

从测试的角度看，参数也是非常麻烦。要编写能够确保参数的组合运行正常的测试用例非常不容易。

输出参数比输入参数更难以理解。我们关于认为信息通过参数输入函数，通过返回值从函数输出。不太期望信息通过参数输出。

**一元函数的普遍形式**

向函数传入单个参数，有以下几个很普遍的理由：

1，问关于那个参数的问题，例如：boolean fileExists("MyFile")

2，操作该参数，将其转换成其他的形式。例如：InputStream fileOpen("Myfile")把String类型的文件名称转换为InputStream类型的返回值。

3，参数是个事件，使用该参数改变系统状态。

应该尽量避免编写不遵循以上形式的一元函数。例如，void includeSetupPageInto(StringBuffer pageText)。对于转换，使用输出参数而非返回值会令人迷惑。

实际上，StringBuffer transform(StringBuffer in)要比StringBuffer transform(StringBuffer out)强，至少它遵循了转换的形式。

**尽量避免布尔值的参数**

布尔值的参数，是非常不好的做法（骇人听闻）。

这样做使得方法签名变得立刻复杂了。大声宣布本函数不止做一件事。如果参数为true将会这样做，参数为false将会那样做！

例如，与其render(Boolean isSuite)，不如将函数分为renderForSuite()和renderForSingleTest()。

**二元函数**

两个参数的函数总会比一元函数难懂。例如，writeField(name)比writeField(outputStream,name)好懂。

有时候两个参数正好，例如，Point p = new Point(0,0)

如果可能，尽量把二元函数转换成一元函数。例如，把上面举例的writeField函数写成outputStream的成员之一，从而可以这样用：outputStream.writeField(name)。

或者，将outputStream写成当前类的成员变量，从而无需再传递。

又或者，分离出类似FieldWriter的新类，在其构造函数中采用outputStream，并包含一个write方法。

**三元函数**

三元函数就更难缠了，很多时候分辨不清那三个参数是做什么的，会经常搞混淆。

在需要写三元函数时，请仔细斟酌，是否能变成二元甚至一元函数。

**参数对象**

如果函数看来需要两个、三个或三个以上参数，就说明其中一些参数该封装成类了。

例如：
<pre>
Circle makeCircle(double x, double y, double radius)
</pre>
可以改写为
<pre>
Circle makeCircle(Point center, double radius)
</pre>
从参数创建对象，从而减少参数数量，看似作弊，但实际上当一组参数被共同传递，往往就是该组参数属于一个共同的类型。

**参数列表**

有时候，需要向函数中传入数量可变的参数，该函数实际上是某种一元，二元或者三元函数。超过这个数量可能就犯错了。

例如：
<pre>
void monad(Integer... args);

void dyad(String name, Integer... args);

void triad(String name, int count, Integer... args);
</pre>
**动词与关键字**

函数和参数应该形成良好的动词/名词对形式。

例如write(name)挺好，但writeField(name)更好，它告诉我们，"name"是一个“field"

assertEqual(expected, actual)改为：assertExpectedEqualsActual(expected, actual)会更好些，因其减轻了记忆参数顺序的负担。

**避免使用输出参数**

输出参数经常令人迷惑，例如，

appendFooter(s)，是把s添加到什么地方，还是把什么东西添加到s后面？s事输入参数还是输出参数？

从函数签名
<pre>
public void appendFooter(StringBuffer report)
</pre>
我们可以看出它是输出参数，但这就要被迫检查函数声明，可能会造成思路中断。

在使用面相对象编程时，对输出参数的大部分需求已经消失。例如，可以这样用：
<pre>
report.appendFooter()
</pre>
含义就非常清晰。



**九，无副作用**

函数承诺只做一件事，但有时还是会做其他被藏起来的事情，例如对自己类中的变量做出未能预期的改动，或者改动全局变量等。

无论哪种情况，都是具有破坏性，会导致古怪的时序耦合和顺序依赖。

例如以下函数：

<pre>
public class UserValidator {
	private Cryptographer cryptographer;
	public boolean checkPassword(String userName, String password) { 
		User user = UserGateway.findByName(userName);
		if (user != User.NULL) {
			String codedPhrase = user.getPhraseEncodedByPassword(); 
			String phrase = cryptographer.decrypt(codedPhrase, password); 
			if ("Valid Password".equals(phrase)) {
				Session.initialize();
				return true; 
			}
		}
		return false; 
	}
}

</pre>
表面上看，只是检查用户名和密码，并返回true或false,但它会有副作用：对Session.initialize()的调用。

而checkPassword函数，从函数名看，并未暗示它会初始化该次对话(session)。

因此，如果某个开发者用它检查用户名有效性时，就得冒抹除现有对话(session)数据的风险。

这一副作用造成了时序性耦合，即checkPassword只能在特定时刻使用，换言之，在初始化会话是安全的时候调用。

需要尽量避免时序性耦合。如果一定需要，应该在函数名称中说明。例如在本例中，可以重命名函数为：checkPasswordAndInitializeSession，虽然这还是违反了“只做一件事”的原则。

**十，分隔指令与询问**

函数要么做某件事情（指令），要么回答某件事情（询问），但二者不可得兼。

函数两样都干常会导致混乱。例如：
<pre>
public boolean set(String attribute, String value);
</pre>
该函数设置某个指定属性，如果成功则返回true。

则容易导致以下语句：
<pre>
if (set("username", "unclebob")) ...
</pre>
从读者的角度，这句话的意思很难理解。是问username的值是否以前已经设置为unclebob，还是正在设置？含义不清楚。

解决方案，可以把函数名取为：setAndCheckIfExists，但更好的方案是把指令和询问分隔开，例如改为：
<pre>
if (attributeExists("username")) {
    setAttribute("username", "unclebob");
}
</pre>

**十一，使用异常替代返回错误码**

从指令式返回错误码轻微违反了指令与询问分隔的原则，它鼓励了在if语句中把指令当表达式用：
<pre>
if (deletePage(page) == E_OK)
</pre>
这会导致，当返回错误码时，就要求调用者马上处理错误，这造成了代码嵌套结构的增加：
<pre>
if (deletePage(page) == E_OK) {
	if (registry.deleteReference(page.name) == E_OK) {
		if (configKeys.deleteKey(page.name.makeKey()) == E_OK){ 
			logger.log("page deleted");
		} else {
			logger.log("configKey not deleted");
		}
	} else {
		logger.log("deleteReference from registry failed"); 
	}
} else {
	logger.log("delete failed"); return E_ERROR;
}
</pre>
另一方面，如果使用异常替代返回错误码，则错误处理代码则能从主路径代码中分离出来，得到简化：
<pre>
try {
	deletePage(page); 
	registry.deleteReference(page.name); 
	configKeys.deleteKey(page.name.makeKey());
}
catch (Exception e) {
	logger.log(e.getMessage()); 
}
</pre>
**抽离Try/Catch代码块**

Try/Catch代码块非常丑陋，搞乱了代码结构，把错误处理与正常流程混淆在一起。

最好把需要try和catch代码处理的部分，从主体部分抽离出来，另外形成函数：

<pre>
public void delete(Page page) { 
	try {
		deletePageAndAllReferences(page); 
	}
	catch (Exception e) { 
		logError(e);
	} 
}
private void deletePageAndAllReferences(Page page) throws Exception 
{ 
	deletePage(page);
	registry.deleteReference(page.name); 
	configKeys.deleteKey(page.name.makeKey());
}
private void logError(Exception e) { 
	logger.log(e.getMessage());
}
</pre>
上面代码中，delete函数只与错误处理有关，看起来很清晰。

deletePageAndAllReferences只与完全删除一个页面有关，错误处理则被间隔。看起来就很好理解了。

**错误处理本身就是一件事**

函数应该只做一件事，而错误处理就是一件事。因此，处理错误的函数不应该做其他事情。

Error.java依赖磁铁

返回错误码，通常暗示某处有个枚举类型，包含了所有错误码。

这样的类就是一块依赖磁铁（dependency magnet），其他许多累都得导入和使用它。
<pre>
public enum Error { 
	OK,
	INVALID,
	NO_SUCH,
	LOCKED, 
	OUT_OF_RESOURCES, 
	WAITING_FOR_EVENT;
}
</pre>
当Error枚举修改时，所有其他的类都要重新编译和部署。这使得程序员不愿增加新的错误码，因为这样他们就得重新构建和部署所有东西。

使用异常类代替错误码，新的异常可以从异常类派生出来，无需重新编译活重新部署。这也是开放闭合原则(OCP)的一个范例。

**十二，别重复自己**

有时候，会有算法在不同的函数中被重复使用。

识别重复不容易，因重复会与其他代码混在一起，从而也不完全一样。

这样的重复还是会导致问题，因代码因此而臃肿，且当该处理代码改变时，有可能需要改动多处地方。

修正重复，会导致整个模块的可读性提高。

重复可能是软件中一切“邪恶”的根源。许多原则和实践，都是为了控制与消除重复而创建。

例如，全部数据库范式都是为消灭数据重复而服务。

面向对象编程将代码集中到基类，从而避免了冗余。

面向方面编程和面向组件编程，多少也都是消除重复的一种策略。

**十三，如何写出整洁的函数**

写代码就象写其他东西一样，想出什么就写什么，然后再打磨它。

初稿往往粗陋，需要反复斟酌推敲，直至达到心目中的样子。

先写出函数，这也许函数冗长而复杂，有太多问题：太多缩进，循环，长长的参数列表，随意的名称...但此时也要配上一套单元测试，覆盖每行代码。

然后打磨代码：分解函数、修改名称、消除重复、缩短方法、拆散类，同时要保证测试通过。

最后，遵循本章的原则，组装好这些函数。

没有人能一次性写好它们。

问题：

1， 你或你的公司在写函数时，遵守了哪些原则？

2， 每个函数不超过五行，是不是太激进了？你的看法是什么？

3， 我们写iOS程序时，经常把一些事件处理函数写在一起，例如viewDidLoad,viewWillAppear等等，但是按照这一章的写法，要把函数依照从上到下的阅读顺序排列，“每个函数后面都跟着下一个抽象层级的函数。”，你觉得这种方式合适吗？

![alt text][3]

图片来源：[一九八四][4]



  [1]: https://www.safaribooksonline.com/library/view/clean-code/9780136083238/graphics/3_1fig_martin.jpg
  [2]: https://www.safaribooksonline.com/library/view/clean-code/9780136083238/ch03.html
  [3]: https://upload.wikimedia.org/wikipedia/zh/thumb/2/28/Nineteen_Eighty-Four_manuscript.jpg/640px-Nineteen_Eighty-Four_manuscript.jpg?1445768888555
  [4]: http://www.wikiwand.com/zh-tw/%E4%B8%80%E4%B9%9D%E5%85%AB%E5%9B%9B
  [5]: http://ourcoders.com/tag/name/CleanCode/