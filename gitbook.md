# GitBook

## gitbook搭建博客

## gitbook page 和 github markdown

### page和markdown的转化

(1) markdown -> page

如果只有一个一级标题，自动提升，一级标题->page标题，二级->一级，依次类推；\
如果有多个一级标题，不会自动提升，markdown文件名是page标题，一级->一级，二级->二级，依次类

(2) page-> markdown&#x20;

都是自动下降，page标题->一级标题，一级标题->二级标题，依次类推、

(3) 示例

<figure><img src=".gitbook/assets/f9900b724d30ad7cf98c2e1bd5eb2fc.png" alt=""><figcaption></figcaption></figure>

(4) 总结

为了保持gitbook上传的markdown，和最后同步到github的markdown的一致性。\
编辑markdown文件，一级标题是文章名字，然后内容用二三四级标题即可。\
编辑page，page标题是文章名字，然后内容用一二三级标题即可。



## gitbook和github之间的同步

(1) 实验结果

gitbook和github之间的同步分为两种，gitbook -> github 和 github -> gitbook，结果测试后我发现，对于这两种方式都是如下的工作模式：

gitbook web端上，edit page后，merge，commit自动push到github仓库上去；\
github仓库，自己手动编辑后，gitbook web端的page会自动pull仓库的内容，进行更新；



(2) 结论

通过上述的实验，我们清楚了gitbook和github同步的本质

无论是在gitbook编辑，还是在github仓库编辑，实际都是把更新的内容add到索引里，然后再提交到commit，最后push到github仓库中。github仓库中，肯定是最新的commit，而gitbook web端会自动检测是否有新的commit，如果有就进行update，同步成最新的commit。



那既然如此，为什么有给出了两种同步方式，这里的同步其实是说gitbook和github建立connection后，第一次同步的方式，因为按照上述机制，有可能存在初始情况github仓库为空，但是gitbook里有内容，这个时候为了让gitbook的内容同步到github上，就必须选择gitbook -> github，第一次同步后，之后随意更改同步模式，都可以自动同步





