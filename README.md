### Running Command
- compression: python main.py 0 test.txt test.cps   
- decompression: python main.py 1 test.cps test_rec.txt  
- **If the code presented helps u, could u please give me a star.**


### [Python&DS]- Python实现Huffman编码压缩和解压缩文件

本文主要介绍Huffman编码、Huffman树、和如何借助Python实现Huffman编码树对文件进行压缩和解压缩。下文目录：
1. 什么是Huffman编码；
2. 如何通过Huffman树创建Huffman编码；
3. Python实现Huffman编码对文件进行压缩和解压缩

----
#### 一、什么是Huffman编码
<br>
百科给的定义如下：
<br>

> **哈夫曼**[^1]编码(Huffman Coding)，又称霍夫曼编码，是一种编码方式，哈夫曼编码是可变[字长](http://baike.baidu.com/view/731.htm)编码(VLC)的一种。Huffman于1952年提出一种编码方法，该方法完全依据[字符][^2]出现概率来构造异字头的平均长度最短的码字，有时称之为最佳编码，一般就叫做Huffman编码（有时也称为霍夫曼编码）

如上，Huffman编码就是一种效率很高的编码方式，在理解Huffman编码之前，我们先来了解一下下面两种编码方式：
<br>
**1.定长编码方式**<br>

例如ASCII码，8-bit定长编码，使用8位(一个字节)代表一个字符，比如tea就一定得需要 3x8 = 24位去表示该自字符串，一个含有n个字符的字符串就得需要 nx8 位去表示该字符串;
这样的编码没有考虑到一些字符出现的频率会高于一些其它的字符，比如在英文26个字母**e**的出现频率最高，而**z**出现的频率最低，此时我们使用较短位数的编码来表示**e**，代价是表示**z**字符可能需要稍长的编码，但是这并不妨碍我们达到压缩的效果：
**例如:** 使用7位的编码表示**e**，9位的编码表示**z**，其它字符的编码不变，由于**e**出现的频率比**z**的高，假设一篇文章当中e出现了10000次，而z出现的次数是10次，那么相比于定长编码，就节省了 10000 - 10 = **9990**位的空间。(这个例子主要展示定长码的缺点，以及变长码可以给我们带来的好处)

**2.变长编码方式**<br>

上面提到了，变长编码方式可以给我带来节省空间的好处，但是对于使用变长码编码的文件，如何去解析该文件得到原文件内容呢，又可能会出现什么问题呢？
**举个例子：**
使用 0 表示 **e**，1 表示 **a**，01表示**t**，那么 **tea**就被编码成0101，但是我们解析的时候，0101就可以解析成为 **tt，eat，eaea**,为了解决这个问题，科学家们又引入一个新的概念，那就是**前缀码**(Prefix codes 是属于变长编码范围的)

**前缀码定义：任何一个字符的编码都不能是其它任何字符的编码的前缀**

对于上面的例子，使用**前缀码**的意思就是 **e** 不能用0去表示，应为 0是 **t**对应的编码  **01** 的前缀，如果我们使用 **001** 去表示**e**，**1** 表示 **a**，**01**表示**t**，那么 **tea**就被编码成为 **010011**，并且在解析的时候，我们就只能解析成为 **tea**。

**Huffman编码就是一种能够使用最短的位数来编码被编码文件的前缀码**
<br>
<br>

**如何构造这样的前缀码?**

#### 二、借助Huffman树创建Huffman编码
<br>
Huffman编码将给字母分配编码。每个字母的编码的长度取决于在被压缩文件中对应字母的出现频率，我们称之为**权重(weight)**。每个字母的Huffman编码是从称为**Huffman编码树**的**满二叉树**(所有节点要么有左右两个子孩子，要么就没有子孩子)中得到的。**Huffman编码树**的每一个叶节点对应于一个字母，叶节点的权重 （weight）就是它对应的字母出现的频率。使用权重的目的是建立的**Huffman编码树**有**最小外部路径权重**。
下图1将给大家解释一下什么是最小外部路径权重，并且Huffman编码的过程：

![图1  外部路径权重解释](http://upload-images.jianshu.io/upload_images/1769441-75db402c8dd595ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么接下来，我们将解释图1 中的第一步构建**Huffman编码树**的过程：
1. 创建n个初始化的Huffman树，每个树只包含单一的叶节点，叶节点纪录对应的字母和该字母出现的频率(weight)；
2. 按照weight从小到大对其进行所有的Huffman树进行排序，取出其中weight最小的两棵树，构造一个新的Huffman树，新的Huffman树的weight等于两棵子树的weight之和，然后再加入到原来的Huffman树数组当中；
3. 反复上面的2中的操作，直到该数组当中只剩下一棵Huffman树，那么最后剩下来的那棵Huffman树就是我们构造好的Huffman编码树；

下面几个图将展示对应上图例子的一个构造Huffman编码树的过程，如图 2和图 3所示：

![图 2  构造Huffman编码树的过程 1](http://upload-images.jianshu.io/upload_images/1769441-e8e9729e82bc2bba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![图 3  构造Huffman编码树的过程 2](http://upload-images.jianshu.io/upload_images/1769441-fbb20c07c35a7d6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

得到上面的Huffman编码树之后，就可以得到每个字符对应的编码了，方法就是：**从根节点找到该叶节点，如果向左子树前进一步，那么code + = '0',如果向右子树前进了一步，那么code+= '1',等到达该叶节点，code对应的内容，就是该叶节点对应字符的编码**

 自此，你已经知道了如何使用Huffman编码树如何给字符分配编码，并且也知道了如何去构造这样的Huffman编码树，那么接下来就借助Python来实现它吧！

#### 三、Python实现Huffman编码对文件进行压缩和解压缩

文件压缩的思路如图 4所示：
![图 4  压缩文件的思想](http://upload-images.jianshu.io/upload_images/1769441-e0197f6d93344e1c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


文件解压缩的思路如图 5所示：
![图 5  解压缩文件的思想.png](http://upload-images.jianshu.io/upload_images/1769441-6fc4ae0fdad29797.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 **Python代码实现：**
**step1**:实现Huffman编码树及其构造方法，代码如下：
```python
#-*- coding:utf-8 -*-
#copyright@zhanggugu
import six
import sys

class HuffNode(object):
    """
    定义一个HuffNode虚类，里面包含两个虚方法：
    1. 获取节点的权重函数
    2. 获取此节点是否是叶节点的函数

    """
    def get_wieght(self):
        raise NotImplementedError(
            "The Abstract Node Class doesn't define 'get_wieght'")

    def isleaf(self):
        raise NotImplementedError(
            "The Abstract Node Class doesn't define 'isleaf'")


class LeafNode(HuffNode):
    """
    树叶节点类
    """
    def __init__(self, value=0, freq=0,):
        """
        初始化 树节点 需要初始化的对象参数有 ：value及其出现的频率freq
        """
        super(LeafNode, self).__init__()
        # 节点的值
        self.value = value
        self.wieght = freq

    
    def isleaf(self):
        """
        基类的方法，返回True，代表是叶节点
        """
        return True

    def get_wieght(self):
        """
        基类的方法，返回对象属性 weight，表示对象的权重
        """
        return self.wieght

    def get_value(self):
        """
        获取叶子节点的 字符 的值
        """
        return self.value


class IntlNode(HuffNode):
    """
    中间节点类
    """
    def __init__(self, left_child=None, right_child=None):
        """
        初始化 中间节点 需要初始化的对象参数有 ：left_child, right_chiled, weight
        """
        super(IntlNode, self).__init__()

        # 节点的值
        self.wieght = left_child.get_wieght() + right_child.get_wieght()
        # 节点的左右子节点
        self.left_child = left_child
        self.right_child = right_child


    def isleaf(self):
        """
        基类的方法，返回False，代表是中间节点
        """
        return False

    def get_wieght(self):
        """
        基类的方法，返回对象属性 weight，表示对象的权重
        """
        return self.wieght

    def get_left(self):
        """
        获取左孩子
        """
        return self.left_child

    def get_right(self):
        """
        获取右孩子
        """
        return self.right_child


class HuffTree(object):
    """
    huffTree
    """
    def __init__(self, flag, value =0, freq=0, left_tree=None, right_tree=None):

    	super(HuffTree, self).__init__()

        if flag == 0:
            self.root = LeafNode(value, freq)
        else:
            self.root = IntlNode(left_tree.get_root(), right_tree.get_root())


    def get_root(self):
        """
        获取huffman tree 的根节点
        """
        return self.root

    def get_wieght(self):
        """
        获取这个huffman树的根节点的权重
        """
        return self.root.get_wieght()

    def traverse_huffman_tree(self, root, code, char_freq):
        """
        利用递归的方法遍历huffman_tree，并且以此方式得到每个 字符 对应的huffman编码
        保存在字典 char_freq中
        """
        if root.isleaf():
            char_freq[root.get_value()] = code
            print ("it = %c  and  freq = %d  code = %s")%(chr(root.get_value()),root.get_wieght(), code)
            return None
        else:
            self.traverse_huffman_tree(root.get_left(), code+'0', char_freq)
            self.traverse_huffman_tree(root.get_right(), code+'1', char_freq)



def buildHuffmanTree(list_hufftrees):
    """
    构造huffman树
    """
    while len(list_hufftrees) >1 :

        # 1. 按照weight 对huffman树进行从小到大的排序
        list_hufftrees.sort(key=lambda x: x.get_wieght()) 
               
        # 2. 跳出weight 最小的两个huffman编码树
        temp1 = list_hufftrees[0]
        temp2 = list_hufftrees[1]
        list_hufftrees = list_hufftrees[2:]

        # 3. 构造一个新的huffman树
        newed_hufftree = HuffTree(1, 0, 0, temp1, temp2)

        # 4. 放入到数组当中
        list_hufftrees.append(newed_hufftree)

    # last.  数组中最后剩下来的那棵树，就是构造的Huffman编码树
    return list_hufftrees[0]
```

上面代码详细展示了如何构造Huffman编码树，代码当中的注释已经足够详细，并且算法在第二部分已经进行了详细的讲解，在此就不赘述！

**step2**:压缩文件函数compress() 的实现，其主要思路就如图 4所示，其代码参考github 工程：


**step3**:解压缩文件函数decompress() 的实现，其主要思路就如图 5所示，其代码参考github 工程：


对代码中解压缩和压缩对齐处理方法说明：

![图 6    解压缩和压缩过程中字节对齐处理方法](http://upload-images.jianshu.io/upload_images/1769441-7551884cf7f3dbe7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<br>
**测试：**

![图 7  原文件大小](http://upload-images.jianshu.io/upload_images/1769441-08cc4358bbd1a3a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



![图 8  压缩之后的文件大小](http://upload-images.jianshu.io/upload_images/1769441-c0d097d4d9ab4592.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![图 9  解压缩文件之后的内容](http://upload-images.jianshu.io/upload_images/1769441-de321a77287088c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

原则上，该程序可以对任意格式的文件进行压缩，我自己也试过将xsl，word的文件进行压缩，大概可以得到**30%**左右的空间节省
<br>

#### 四、总结

看完本文，希望你对Huffman编码方法能够有一个清晰的了解，并且知道如何使用Python实现Huffman编码树并且对文件进行解压缩。
完整源码请参考[github工程](https://github.com/gg-z/huffman_coding)

##### 参考资料：
>[1] - Data Structures and Algorithm Analysis in C++  Third Edition by Clifford A. Shaffer （<<数据结构与算法分析 C++版第三版>> 作者Clifford A. Shaffer ， 电子工业出版社 ）

[^1]: http://baike.baidu.com/view/1436260.htm
[^2]: http://baike.baidu.com/view/263416.htm
