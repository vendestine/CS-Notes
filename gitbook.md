# GitBook

## gitbook的summary.md和README.md

gitbook渲染到web page，主要就是通过所有的md文件 + summary.md + README.md\
其中README.md是gitbook的简介，而summary.md是gitbook的目录

gitbook的编辑方式：

(1) gitbook web端编辑：如果是采用gitbook web端编辑，那么会自动将第一个page的内容作为README.md，然后自动根据现有的page结构，生成目录写入summary.md里\
(2) github仓库编辑：如果是直接在仓库边界，那么我们为了让仓库的md文件渲染到web page上，我们需要手动编辑summary.md文件，也就是说要自己手动组织目录结构。



## gitbook page 和 github markdown

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

无论是在gitbook编辑，还是在github仓库编辑，实际都是把更新的内容add到索引里，然后再提交到commit，最后push到github仓库中。github仓库中，肯定是最新的commit，而gitbook web端会自动检测是否有新的commit，如果有就进行update，同步成最新的commit。<mark style="background-color:orange;">所以同步源其实就是github仓库</mark>



那既然如此，为什么有给出了两种同步方式，这里的同步其实是说gitbook和github建立connection后，第一次同步的方式，因为建立connection的时候，要么是gitbook为空，github仓库有内容；要么是gitbook有内容，github仓库为空。

按照上述机制，如果初始情况是 gitbook有内容，github仓库为空，这个时候作为同步源的github仓库是空的，所以此时必须选择gitbook -> github，将gitbook的内容同步到github仓库中。之后可以随意切换同步模式，因为github仓库不为空了，它可以作为同步源，gitbook和github仓库都是自动同步。





