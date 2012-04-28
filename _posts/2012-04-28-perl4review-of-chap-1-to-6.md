--- 
layout: 
post title: "Perl学习笔记(4)－Review of Chap 1 to 6"
category: 
tags: [Perl]
---
{% include JB/setup %}
今天mixi的上海公司的工程师给我review了小骆驼第1到6章的内容。一些没有弄懂的东西记在这里。

（1）perl文件内的多行注释：其实严格意义上来讲是没有多行注释的。但是[perlpod](http://perldoc.perl.org/perlpod.html)的东西可以支持各种形式的文档，其中也包括进行注释。

>Perlpod is a simple-to-use markup language used for writing documentation for Perl, Perl programs, and Perl modules.
Translators are available for converting Pod to various formats like plain text, HTML, man pages, and more.
Pod markup consists of three basic kinds of paragraphs: ordinary, verbatim, and command.

这里面讲的三种段落的格式，ordinary指的是这个段落会以最小的格式进行组织，也可以用一些格式的代码对段落进行排版；verbatim指的是不对段落做任何处理和解析； command指的是用特定的方式来处理这个段落。多行注释的方式有两种：

	=pod
	put some comment here
	=cut

	=for comment
	put some comment here
	=cut

其中第一种以pod开头实际上什么都没指定，这时候perl会默认接下来的段落是ordinary或者verbatim；而第二种中是以command paragraph的方式指定了接下来的段落是注释的形式。两种方法都以＝cut来结尾，告诉perl编译器这个perlpod结束了。

另外如果用了一大块 pod 来描述你的代码，则尽量不要将其放在文件的上面或中间部分。虽然 perl 分析器能很快的跳过 pod ，但是还是需要磁盘的读取时间。将所有的pod放到__END__后面，那样 Perl 编译器就不会去注意它。

(2) 在进行字符串的数组内插的时候，perl会找匹配那个名称最长的数组来进行内插。

	my @long = qw(1 2 3);
	my @longer = qw(4 5 6);
	print "@longer is what i get\n";
	print "@{long}er is what i get\n";

第一个print会得到4 5 6；第二个print会得到 1 2 3。

(3) 列表操作符[sort](http://perldoc.perl.org/functions/sort.html)函数中利用不同参数，可以得到对列表不同的排序效果。sort有三种使用方式：

	sort LIST;
	sort SUBNAME LIST;
	sort BLOCK LIST;

其中第一个是直接对列表按字典序排序；第二三种指定了排序的方法。

其中如果对数字的数组进行升序排序的话，方法如下：

	@array_sorted = sort {$a <=>$b} @array;

如果使用降序排列的话把$a和$b互换。

(4) 对时间进行格式化输出有几种办法，其中最土的一种就是对日期进行判定，如果位数不够，就给它补齐0再输出；还有一种是用strftime这个函数来进行格式化，在[这里](http://www.mkssoftware.com/docs/man3/strftime.3.asp)到各种格式的写法。

	use POSIX qw(strftime);
	my $formated_date = strftime("%Y-%m-%d",localtime);
   printf $formated_date;	

(5) reverse 哈希：在对哈希进行reverse操作的时候，hash会首先松绑为一个键值对列表，然后对其进行reverse操作就导致了键值互换。如果将这个列表重新存回新的哈希表中时，如果这时原来的值不唯一的话，就无法确定新的重复了的键到底哪一个被存到新的哈希中。这是因为松绑了以后的列表的键值对的顺序时不定的。
