#第五章 格式

![alt text][1]

图片来源：[Formatting - Clean Code][2]

保持良好的代码格式，并且在团队中贯彻执行，会大大提高代码的可读性。

**一，格式的目的**

或许你以为“让代码能工作”是最重要的，但代码格式很重要，这关乎沟通，而沟通是专业开发者的头等大事。

代码的可读性会对以后可能发生的修改行为产生深远影响，这影响到可维护性和可扩展性。

**二，代码垂直格式**

**1， 源代码行数**

通过对一些成功项目（Java）的统计，发现源文件的行数大多在200行到500行之间。

这个可以作为文件尺寸大体上的一个原则。毕竟短文件通常比常文件更易于理解。

**2，向报纸学习**

想想看写的好的报纸文章，你会从头到尾阅读。

顶部，有一个头条，告诉主题。

第一段是故事大纲和概述。

从第二段顺序读下去，细节逐渐增加，直到你了解到所有的细节为止。

源文件也要象报纸文章一样，类名称和方法名称、变量简单一目了然。

光是读类名称和函数名称，就应该足够告诉我们是否处于正确的模块中。

源文件的最顶部应该给出高层次概念和算法，细节应该逐渐向下展开，直到最底层的函数和细节。

**3，概念之间的垂直方向区隔**

每组代码行展示一条完整的思路，这些思路用空白行区别开来。

例如，在如下代码里，包声明，导入声明和每个函数之间，都有空白行隔开。

这极大增加了可读性。往下读代码时，你的目光总会停留于空白行之后的那一行。

<pre>
package fitnesse.wikitext.widgets;

import java.util.regex.*;

public class BoldWidget extends ParentWidget {
	public static final String REGEXP = "'''.+?'''";
	private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
		Pattern.MULTILINE + Pattern.DOTALL 
	);

	public BoldWidget(ParentWidget parent, String text) throws Exception { 
		super(parent);
		Matcher match = pattern.matcher(text);
		match.find();
		addChildWidgets(match.group(1)); 
	}

	public String render() throws Exception { 
		StringBuffer html = new StringBuffer("<b>");
		html.append(childHtml()).append("</b>");
		return html.toString();
	} 
}
</pre>

如果抽掉这些空白行，代码可读性减弱了很多。

<pre>
package fitnesse.wikitext.widgets;
import java.util.regex.*;
public class BoldWidget extends ParentWidget {
	public static final String REGEXP = "'''.+?'''";
	private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
		Pattern.MULTILINE + Pattern.DOTALL);
	public BoldWidget(ParentWidget parent, String text) throws Exception {
		super(parent);
		Matcher match = pattern.matcher(text); match.find();
		addChildWidgets(match.group(1));
	}
	public String render() throws Exception {
		StringBuffer html = new StringBuffer("<b>");
		html.append(childHtml()).append("</b>");
		return html.toString();
	} 
}
</pre>

**4, 垂直方向上的靠近**

空白行隔开了概念，二靠近的代码行暗示了它们之间的紧密关系。

所以，紧密相关的代码应该互相靠近。

以下代码里的注释，就割断了两个实体变量之间的联系：

<pre>
public class ReporterConfig {

/**
* The class name of the reporter listener 
*/

private String m_className;

/**
* The properties of the reporter listener 
*/
private List<Property> m_properties = new ArrayList<Property>();

public void addProperty(Property property) { 
	m_properties.add(property);
}
</pre>
而下面的代码，则更加清晰易懂：
<pre>
public class ReporterConfig {
	private String m_className;
	private List<Property> m_properties = new ArrayList<Property>();
	public void addProperty(Property property) { 
		m_properties.add(property);
}
</pre>
**5, 垂直距离**

关系密切的概念应该互相靠近。

除非有更好的理由，否则不要把关系密切的概念放在不同的文件中。

实际上，这也是尽量避免使用protected变量的理由之一。

**（1）变量相关**

变量声明应尽可能靠近其使用位置。

因函数很短，本地变量应该在函数的顶部出现。例如：

<pre>
private static void readPreferences() {
	InputStream is= null;
	try {
		is= new FileInputStream(getPreferencesFile()); 
		setPreferences(new Properties(getPreferences())); 
		getPreferences().load(is);
	} catch (IOException e) { 
		try {
			if (is != null) is.close();	
		} 
		catch (IOException e1) {
		} 
	}
}
</pre>
循环中的控制变量应该总是在循环语句中声明，如：
<pre>
public int countTestCases() { 
	int count= 0;
	for (Test each : tests)
		count += each.countTestCases(); 
	return count;
}
</pre>
**（2）函数相关**

如果某个函数调用了另外一个，就应该把它们放在一起，而且调用者应该尽可能放在被调用者上面。

这样程序就有了一个自然的顺序，极大增强了程序的可读性。

**（3）概念相关**

概念相近和相关的代码应该放在一起。相关性越强，彼此间的距离应该越短。

例如：

<pre>
public class Assert {
	static public void assertTrue(String message, boolean condition) {
		if (!condition) fail(message);
	}
	static public void assertTrue(boolean condition) { 
		assertTrue(null, condition);
	}
	static public void assertFalse(String message, boolean condition) { 
		assertTrue(message, !condition);
	}
	static public void assertFalse(boolean condition) { 
		assertFalse(null, condition);
	} 
...
</pre>

**三, 横向格式**

据统计典型代码中代码行的宽度，我们可以确定，程序员更喜欢短代码行。

应尽可能保持代码行短小。最好80个字符左右，最长不要超过120个字符。

**1，水平方向的区隔与靠近**

用空格字符把紧密相关的事务联系在一起，也把相关性较弱的事物分开。

例如，把赋值操作符周围加上空格以达到强调的目的。

又例如，不在函数明和左圆括号之间加空格，这是因为函数和其参数密切相关。

函数中的参数用空格一一隔开，表示参数是互相分离的。

<pre>

public class Quadratic {
	public static double root1(double a, double b, double c) {
		double determinant = determinant(a, b, c);
		return (-b + Math.sqrt(determinant)) / (2*a); 
	}
	public static double root2(int a, int b, int c) { 
		double determinant = determinant(a, b, c); 
		return (-b - Math.sqrt(determinant)) / (2*a);
	}
	private static double determinant(double a, double b, double c) { 
		return b*b - 4*a*c;
	} 
}
</pre>
**四，团队规则**

每个程序员都有自己喜欢的格式，但如果在一个团队工作，就是团队说了算。

此时，请不要用不同的风格来编写代码，这样会增加其复杂度。

**问题：**

**1，你写的代码格式，和本章内容有不一致的地方吗？**

**2，如果有，你觉得需要改变你的代码格式吗？**


 

    如何加入Root Cause读书会：请加我的微信brainstudio,我建立了一个群，方便通知各位写读后感及讨论。

 

[Clean Code所有讨论章节链接][3]


  [1]: https://www.safaribooksonline.com/library/view/clean-code/9780136083238/graphics/5_1fig_martin.jpg
  [2]: https://www.safaribooksonline.com/library/view/clean-code/9780136083238/ch05.html
  [3]: http://ourcoders.com/tag/name/CleanCode/