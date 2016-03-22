#第四章 注释

![alt text][1]

图片来自：[Chapter 4 Comments][2]

注释，是一种“必须的恶”。

若编程语言有足够表达力，或我们长于用这些语言来表达意图，就不那么需要注释－－也许根本不需要。

注释的恰当用法是弥补我们在用代码表达意图时的失败。

**注意，注释毕竟是一种失败，是我们无法找到不用注释就能表达的办法，所以才用注释。**

如果发现自己需要写注释，想想看是否有办法用代码来表达。

每次用代码表达，都应该夸奖一下自己。每次写注释，都应该做个鬼脸，感受一下自己在表达能力上的失败。

因为程序员不能坚持维护注释，所以注释可能会撒谎。存在的时间越久，离所描述的代码越远。

例如以下例子，因为中间插入了代码，注释和所要描述的代码距离很远：

<pre>
MockRequest request;
private final String HTTP_DATE_REGEXP =
"[SMTWF][a-z]{2},s[0-9]{2}s[JFMASOND][a-z]{2}s"+
"[0-9]{4}s[0-9]{2}:[0-9]{2}:[0-9]{2}sGMT"; private Response response;
private FitNesseContext context;
private FileResponder responder;
private Locale saveLocale;
// Example: "Tue, 02 Apr 2003 22:18:49 GMT"
</pre>

**一，注释不能为了美化糟糕代码**

写注释常见的动机是糟糕的代码的存在。

我们编写了一个模块，发现它令人困扰乱七八糟，所以就写了注释。

而此时需要做的，反而是要把代码清理干净！

带有少量注释的整洁代码，要比有大量注释的零碎而复杂的代码像样的多。

**二，用代码来阐述**

程序员总是倾向于认为代码不足以解释行为，但其实只要多想想，就能找到办法来用代码阐述行为。

有时候只是改变函数名就可以。

例如如下例子：
<pre>
// Check to see if the employee is eligible for full benefits 
if ((employee.flags & HOURLY_FLAG) && (employee.age > 65))
</pre>
改成：
<pre>
if (employee.isEligibleForFullBenefits())
</pre>
是不是一下子就清晰多了，不需要注释了。

**三，好注释**

有些注释是必须的和需要写的。

当然，真正好的注释是你想出办法不去写注释。

**1，法律信息**

版权和著作权声明是必须和有理由在每个源文件开头注释处放置的内容。

<pre>
// Copyright (C) 2003,2004,2005 by Object Mentor, Inc. All rights reserved.
// Released under the terms of the GNU General Public License version 2 or later.
</pre>

这类注释不应是合同或法典。只要有可能，就指向一份标准许可或其他外部文档，而不要把所有条款放在注释中。

**2，提供信息的注释**

注释有时是为了提供基本信息：

<pre>
// format matched kk:mm:ss EEE, MMM dd, yyyy 
Pattern timeMatcher = Pattern.compile("d*:d*:d* w*, w* d*, d*");
</pre>
**3，对意图的注释**

有时候，注释不仅提供了信息，还提供了某个决定后的意图：

<pre>
public void testConcurrentAddWidgets() throws Exception { 
	WidgetBuilder widgetBuilder = new WidgetBuilder(new Class[]{BoldWidget.class});
￼
	String text = "'''bold text'''"; 
	ParentWidget parent = new BoldWidget(new MockWidgetRoot(), "'''bold text'''"); 
	AtomicBoolean failFlag = new AtomicBoolean(); 
	failFlag.set(false);
	//This is our best attempt to get a race condition 
	//by creating large number of threads.
	for (int i = 0; i < 25000; i++) {
		WidgetBuilderThread widgetBuilderThread =
			new WidgetBuilderThread(widgetBuilder, text, parent, failFlag);
		Thread thread = new Thread(widgetBuilderThread);
		thread.start(); 
	}
	assertEquals(false, failFlag.get());
</pre>

**4,阐释**

有时候，注释把某些晦涩难明的参数或返回值翻译为某种可读形式。

通常，更好的方法是尽量让参数或返回值自身就足够清楚，但如果参数或返回值是某个标准库的一部分，或者你不能修改的代码，帮助阐释其意义就会有用。

<pre>
public void testCompareTo() throws Exception {
	WikiPagePath a = PathParser.parse("PageA"); 
	WikiPagePath ab = PathParser.parse("PageA.PageB"); 
	WikiPagePath b = PathParser.parse("PageB"); 
	WikiPagePath aa = PathParser.parse("PageA.PageA"); 
	WikiPagePath bb = PathParser.parse("PageB.PageB"); 
	WikiPagePath ba = PathParser.parse("PageB.PageA");
	assertTrue(a.compareTo(a) == 0); // a == a
	assertTrue(a.compareTo(b) != 0); // a != b
	assertTrue(ab.compareTo(ab) == 0); // ab == ab
	assertTrue(a.compareTo(b) == -1); // a < b
	assertTrue(aa.compareTo(ab) == -1); // aa < ab
	assertTrue(ba.compareTo(bb) == -1); // ba < bb
	assertTrue(b.compareTo(a) == 1); // b > a
	assertTrue(ab.compareTo(aa) == 1); // ab > aa
	assertTrue(bb.compareTo(ba) == 1);// bb > ba
}
</pre>

**5，警示**

有时候，用于警告其他程序员会出现某种后果的注释也是有用的。

<pre>
// Don't run unless you
// have some time to kill.
public void _testWithReallyBigFile() {
	writeLinesToFile(10000000);
	response.setBody(testFile);
	response.readyToSend(this);
	String responseString = output.toString(); 
	assertSubString("Content-Length: 1000000000", responseString); 
	assertTrue(bytesSent > 1000000000);
}
</pre>
又例如：
<pre>
public static SimpleDateFormat makeStandardHttpDateFormat() {
    //SimpleDateFormat is not thread safe,
    //so we need to create each instance independently.
    SimpleDateFormat df = new SimpleDateFormat("EEE, dd MMM yyyy HH:mm:ss z");
    df.setTimeZone(TimeZone.getTimeZone("GMT"));
    return df;
}
</pre>
**6,TODO注释**

有时，有理由用//TODO形式在源代码中放置要做的工作列表。
<pre>
//TODO-MdM these are not needed
// We expect this to go away when we do the checkout model protected VersionInfo makeVersion() throws Exception {
    return null; 
}
</pre>
**7,放大**

注释可以用来放大某种看来不合理的代码的重要性：
<pre>
String listItemContent = match.group(3).trim();
// the trim is real important. It removes the starting 
// spaces that could cause the item to be recognized
// as another list.
new ListItemWidget(this, listItemContent, this.level + 1); 
return buildList(text.substring(match.end()));
</pre>
**8,公共API中的Javdoc**

良好描述的公共API是非常令人满意的。如果在编写公共API,请为它编写良好的Javadoc。

**四，坏注释**

**1, 喃喃自语**

有时候，作者太着急或者没花心思，结果注释成了喃喃自语不知所云。

<pre>
public void loadProperties() {
	try {
		String propertiesPath = propertiesLocation + "/" + PROPERTIES_FILE; 
		FileInputStream propertiesStream = new FileInputStream(propertiesPath); 
		loadedProperties.load(propertiesStream);
	}
	catch(IOException e) {
		// No properties files means all defaults are loaded
	} 
}
</pre>

**2，多余的注释**

有的注释纯属多余，读注释花的时间可能比读代码花的时间长。

<pre>
// Utility method that returns when this.closed is true. Throws an exception 
// if the timeout is reached.
public synchronized void waitForClose(final long timeoutMillis)
	throws Exception {
		if(!closed) {
			wait(timeoutMillis); 
			if(!closed)
				throw new Exception("MockResponseSender could not be closed"); 
	}
}
</pre>
**3, 误导性注释**

有时候，注释有误导性，例如前面出现过的这段：

<pre>
// Utility method that returns when this.closed is true. Throws an exception 
// if the timeout is reached.
public synchronized void waitForClose(final long timeoutMillis)
	throws Exception {
		if(!closed) {
			wait(timeoutMillis); 
			if(!closed)
				throw new Exception("MockResponseSender could not be closed"); 
	}
}
</pre>
从注释里看，在this.closed时函数会立即返回，但实际上，函数会有一段休眠时间然后再判断。

如果程序员只看了注释，则很容易就期望this.closed为真时立即返回，但代码中就会有没有预期到的延迟。

**4，循规式注释**

所谓每个函数都要有Javadoc或者每个变量都要有注释的规矩是很愚蠢的，徒然让代码变得散乱。

<pre>
/** *
* @param title The title of the CD
* @param author The author of the CD
* @param tracks The number of tracks on the CD
* @param durationInMinutes The duration of the CD in minutes */
public void addCD(String title, String author,
	int tracks, int durationInMinutes) {
	CD cd = new CD(); 
	cd.title = title; cd.author = author; 
	cd.tracks = tracks; 
	cd.duration = duration; cdList.add(cd);
}
</pre>

**5,日志式注释**

有人会在每次编辑代码时，在模块开始处添加一条注释。

因为我们现在有了源码控制系统(svn,git等)了，这类冗长的记录只会让模块变得凌乱，应当全部删除。

<pre>
* Changes (from 11-Oct-2001)
* --------------------------
* 11-Oct-2001 : Re-organised the class and moved it to new package com.jrefinery.date (DG);
* 05-Nov-2001 : Added a getDescription() method, and eliminated NotableDate class (DG);
* 12-Nov-2001 : IBD requires setDescription() method, now that NotableDate class is gone (DG); 
*  Changed getPreviousDayOfWeek(), getFollowingDayOfWeek() and getNearestDayOfWeek() to correct bugs (DG);
* 05-Dec-2001 :Fixed bug in SpreadsheetDate class (DG);
* 29-May-2002 :Moved the month constants into a separate interface (MonthConstants) (DG);
* 27-Aug-2002 :Fixed bug in addMonths() method, thanks to N???levka Petr (DG);
* 03-Oct-2002 :Fixed errors reported by Checkstyle (DG);
* 13-Mar-2003 :Implemented Serializable (DG);
* 29-May-2003 :Fixed bug in addMonths method (DG);
* 04-Sep-2003 :Implemented Comparable. Updated the isInRange javadocs (DG);
* 05-Jan-2005 :Fixed bug in addYears() method (1096282) (DG);
</pre>

**6，废话注释**

有时候会看到纯然时废话的注释

<pre>
/**
* Default constructor. */
protected AnnualDateRule() { }
</pre>
<pre>
/** The day of the month. */
private int dayOfMonth;
</pre>
<pre>
/**
* Returns the day of the month. *
* @return the day of the month. */
public int getDayOfMonth() {
     return dayOfMonth;
}
</pre>
**7，Javadoc废话**

Javadoc也可能是废话。下列Javadoc来自某知名开源库，其注释的目的是什么？

<pre>
/** The name. */ 
private String name;
/** The version. */ 
private String version;
/** The licenceName. */ 
private String licenceName;
/** The version. */ 
private String info;
</pre>
**8,能用函数或变量时就别用注释**

例如以下代码和注释：

<pre>
// does the module from the global list <mod> depend on the
// subsystem we are part of?
if (smodule.getDependSubsystems().contains(subSysMod.getSubSystem()))
</pre>

可以改为：

<pre>
ArrayList moduleDependees = smodule.getDependSubsystems(); 
String ourSubSystem = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubSystem))
</pre>
**9，位置标记**

有时，程序员喜欢在源代码中标记某个特别位置，但基本上都无用。

<pre>
// Actions //////////////////////////////////
</pre>
**10，括号后面的注释**

有时程序员会在深度嵌套结构的函数中，在括号后面放置特殊的注释。

但如果你发现想标记右括号，往往要做的是缩短函数（见函数一章）。

<pre>
public class wc {
public static void main(String[] args) {
		BufferedReader in = new BufferedReader(new InputStreamReader(System.in)); String line;
		int lineCount = 0;
		int charCount = 0;
		int wordCount = 0; 
		try {
	￼￼		while ((line = in.readLine()) != null) { 
				lineCount++;
				charCount += line.length();
				String words[] = line.split("W"); 
				wordCount += words.length;
			} //while
			System.out.println("wordCount = " + wordCount); 
			System.out.println("lineCount = " + lineCount); 
			System.out.println("charCount = " + charCount);
		} // try
		catch (IOException e) { 
			System.err.println("Error:" + e.getMessage());
		} //catch
	} //main 
}
</pre>
**11,归属与署名**

例如：
<pre>
/* Added by Rick */
</pre>
这是源代码控制系统做的事情。

如果你写注释在那里，随着一年又一年的过去，注释就会越来越不准确，越来越与原作者没关系。

**12，注释掉代码**

把代码注释掉，并遗留在那里是很讨厌的行为。而我们有了源代码控制系统，一定不要留下这些遗留物。

<pre>
InputStreamResponse response = new InputStreamResponse();
response.setBody(formatter.getResultStream(), formatter.getByteCount());
// InputStream resultsStream = formatter.getResultStream();
// StreamReader reader = new StreamReader(resultsStream);
// response.setContent(reader.read(formatter.getByteCount()));
</pre>

**13，注释中有HTML标签**

注释中的HTML标签会让注释很难读。

如果需要呈现网页，那也应该由工具生成标签，而不是写在注释里。

**14，非本地信息**

如果一定要写注释，请确保它描述了离它最近的代码，别给出其他地方代码的信息。

例如以下代码给出了一个默认端口，而该端口不是由此段代码所能控制。

如果设默认端口的代码被修改，此注释就变成了误导。

<pre>
/**
* Port on which fitnesse would run. Defaults to <b>8082</b>. *
* @param fitnessePort
*/
public void setFitnessePort(int fitnessePort) {
	this.fitnessePort = fitnessePort; 
}
</pre>
**15，信息过多**

别再注释中添加有趣的历史性话题和无关的细节描述，这些只会增加阅读代码的负担。

以下注释除了RFC文档编号外，其他细节对读者完全没必要。

<pre>
/*
RFC 2045 - Multipurpose Internet Mail Extensions (MIME)
Part One: Format of Internet Message Bodies
section 6.8. Base64 Content-Transfer-Encoding
The encoding process represents 24-bit groups of input bits as output strings of 4 encoded characters. Proceeding from left to right, a 24-bit input group is formed by concatenating 3 8-bit input groups. These 24 bits are then treated as 4 concatenated 6-bit groups, each of which is translated into a single digit in the base64 alphabet. When encoding a bit stream via the base64 encoding, the bit stream must be presumed to be ordered with the most-significant-bit first. That is, the first bit in the stream will be the high-order bit in the first 8-bit byte, and the eighth bit will be the low-order bit in the first 8-bit byte, and so on.
*/
</pre>
**16，不明显的联系**

注释及其描述的代码之间的联系应该显而易见，以下注释就有些问题：

<pre>
/*
 * start with an array that is big enough to hold all the pixels * (plus filter bytes), 
 * and an extra 200 bytes for header info */
this.pngBytes = new byte[((this.width + 1) * this.height * 3) + 200];

</pre>
以上注释和代码里，过滤的字节指的是什么？与＋1还是*3有关系？为什么用200？

这个注释就是失败的，因注释本身还需要更多的注释。

**17，函数头的注释**

短函数不需要太多描述。为只做一件事的短函数取个好名字，往往比写函数头的注释要好。

**18，非公共代码中的Javadoc**

虽然Javadoc对于公共API很有用，但对于不打算作为公共用途的代码就很没必要了。对此类代码加Javadoc形式的注释几乎等同于写八股文章。

**问题：**

**1，你写注释的风格是什么样的？犯了以上哪些错误？**



  [1]: https://www.safaribooksonline.com/library/view/clean-code/9780136083238/graphics/4_1fig_martin.jpg
  [2]: https://www.safaribooksonline.com/library/view/clean-code/9780136083238/ch04.html
  [3]: http://ourcoders.com/tag/name/CleanCode/