#第二章 有意义的命名

本章，作为整洁代码的头一步，致力于规范命名规则。

![alt text][1]

图片来自[Chapter 2. Meaningful Names][2]

所谓**名不正则言不顺**，命名的好，代码可读性一下子就可以提高一大块。

本章讲解了十多种命名时的注意事项。

提到命名，使我想起谭浩强先生的书，我是从他的C语言编程书入门的。

虽说编程语言入门了，可那时养成了一些非常不好的编程习惯，给变量随便命名，就是其中一种。

随便摘一段该书中的代码：

<pre>
long f1(int p)
{
    int k;
    long r;
    long f2(int);
    k=p*p;
    r=f2(k);
    return r;
}
</pre>

除了仔细看代码外，基本看不出来该函数在干什么。

如果动辄几万行，几十万行的项目是这么命名，完全是惨不忍睹啊！

**一， 命名要名副其实**

如果命名还需要注释，那么该命名则是不合理的：
<pre>
int d; // elapsed time in days
</pre>

以下命名是合适的:
<pre>
int elapsedTimeInDays; 
int daysSinceCreation; 
int daysSinceModification; 
int fileAgeInDays;
</pre>

命名增加了可读性，例如如下代码，看起来就不知所云：
<pre>
public List<int[]> getThem() {
List<int[]> list1 = new ArrayList<int[]>(); 
    ......
    for (int[] x : theList)
        if (x[0] == 4) 
            list1.add(x);
    return list1; 
}
</pre>
而如果是改成下面的两种，代码则清晰很多：
<pre>
public List<int[]> getFlaggedCells() {
	List<int[]> flaggedCells = new ArrayList<int[]>(); 
	......
	for (int[] cell : gameBoard)
		if (cell[STATUS_VALUE] == FLAGGED) 
			flaggedCells.add(cell);
	return flaggedCells; 
}
</pre>
或是
<pre>
public List<Cell> getFlaggedCells() {
	List<Cell> flaggedCells = new ArrayList<Cell>(); 
	......
	for (Cell cell : gameBoard)
		if (cell.isFlagged()) 
			flaggedCells.add(cell);
	return flaggedCells; 
}
</pre>

**二， 避免误导**

请避免使用与本意相混淆或相悖的词。

例如，aix,sco,hp，不该作为变量名，因它们都是UNIX平台上的专有名称。

除非是List类型，否则不要用类似accountList来命名一组账号，可以命名为accountGroup。

1和l,O和0很容易混淆，请注意使用。

**三，有意义的区分**

例如，ProductInfo和ProductData就没有意义上的区别，很容易混淆。

废话都是冗余的，例如Variable用于变量名中，Table用于表名中。

**四，使用读得出来的名称**

命名尽量别缩写，时间长了谁都看不懂了。

以下命名,对比一下，哪个更容易读明白：
<pre>
class DtaRcrd102 {
	private Date genymdhms;
	private Date modymdhms;
	private final String pszqint = "102"; 
	/* ... */
};
</pre>
<pre>
class Customer {
	private Date generationTimestamp; 
	private Date modificationTimestamp;
	private final String recordId = "102"; 
	/* ... */
}
</pre>

**五，使用可搜索的名称**

如果变量可能在代码中多处使用，则应赋以便于搜索的名称。变量作用域越大，越需要精心命名以便搜索。

例如，如果一星期5天的“5”经常被使用，则将其命名为WORK_DAYS_PER_WEEK要比“5”好搜索的多。

**六，尽量避免使用编码和成员前缀等“旧时代”规则**

例如，匈牙利标记法，成员前缀（m_dsc等等）。现在的编程里，这种前缀和标记法的命名方法越来越少见了。

例如，抽象工厂及其实现，

可以写成：IShapeFactgory和ShapeFactory,

也可以写成ShapeFactory及ShapeFactoryImp.

作者推荐后者的写法。

**七，避免思维映射**

尽量别用脑中熟悉的缩写，来取变量名。

例如，i在编程人员眼中，经常代表index.但最好用当时有意义的名称来替代i.

**八，类名**

类名应当是名词或名词短语，例如Customer,WikiPage,AddressParser。

请避免用太通用的Data,Info等词。

类名不应该是动词。

**九，方法名**

方法名应该是动词或动词短语，例如：postPayment,deletePage或者save.

如果是属性访问，则加上"get","set","is"等前缀。

**十，别扮可爱（卖萌）**

别用俗语来做名称。

例如whack()来表示kill()等。

**十一，每个概念对应一个词**

请给每个抽象概念选一个词，并一以贯之。

例如，如果fetch,retrieve,get并列出现在方法名里，你怎么知道用哪个？

Controller和Driver,Manager也是令人困惑的一组词。

**十二，别用双关语**

请避免同一术语用于不同概念。例如，add方法，如果用于collection时，则应该叫append或insert更贴切。

**十三，使用计算机科学领域的名称还是问题域的名称**

如果能用计算机科学领域的术语，则尽量使用。因只有程序员才会读你的代码。

例如，AccountVisitor，程序员一看就知道这是访问者模式。

如果不能用程序员所熟悉的术语，则采用所涉问题领域来的名称。

至少，看到该名称，能联想到去找该领域专家来询问所需知识。

**十四，添加有意义的语境**

看到一个state变量，可能不知道它是什么意思，但如果是addrState,很容易看出它是地址的一部分。

如果state是Address类的成员，含义就更清晰了。

**十五，不要添加没意义的语境**

例如，给每个类添加一个全局前缀，一般都没有意义。因为所有的类都有此前缀，则变成了没有区别。

**问题：**

**1，你所写过，改过或看过的代码中，有哪些是命名很糟糕的，请举例。**

**2，有哪些是命名很不错的，也请举例。**

**3，此章中命名的规范里，哪些是你已经遵守了的规则，哪些是你忽略了的？**

