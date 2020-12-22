### 每日python知识积累

**1.python中的字符处理**

```python
#1.两个字符相减
ord('c')-ord('b')=1
#2.字符加数字
chr(ord('a')+2)='c'
#注：ord()：将字符转化为整数
#chr()：将数字转化成字符
#倒序遍历list
for i in range(len(list1)-1,-1,-1):
    print(i)
#3.判断字符是否是数字/字母
s.isalpha()/s.isdigit()
s[0].isalpha()/s[0].isdigit()
```

**2.python中的正则使用**

```python
#1.正则表达式(部分)
^：匹配字符串开头
[\+\-]：代表一个+字符或-字符
?：前面一个字符可有可无
\d：一个数字
+：前面一个字符的一个或多个
\D：一个非数字字符
*：前面一个字符的0个或多个
#2.如何使用
	#2.1导包
	import re
	#2.2设置正则规则
    regex = re.compile(r'^[\+\-]?\d+')
    #2.3查找所有匹配的内容(返回list)
    list1 = regex.findall(s)
    #2.4进一步根据需要处理
----------------------------------------
#2.利用正则对字符串进行分割
#Eg:s = "dog cat cat dog",想得到['dog','cat','cat','dog']
import re
s = re.split('\W+',s)
#\W匹配字母数字或下划线或汉字
>>>>>>> 2020-12-19
```

**3.python二分查找**

```python
#bisect模块
import bisect
#需要注意：列表必须有序
#常用函数
#1.bisect.bisect_left(L,x)：在L中查找x。若x存在，则返回x左侧的位置；若x不存在，则返回x应该插入的位置
#2.bisect.bisect_right(L,x)：在L中查找x。若x存在，则返回x右侧的位置；若x不存在，则返回x应该插入的位置
#3.bisect.insort_left(L,x)：将x插入到列表中。若x存在，则插入在左侧
#4.bisect.insort_right(L,x)：将x插入到列表中。若x存在，则插入在右侧
```

**4.python数组处理**

```python
#1.二维数组建立
#1.1已知行数未知列数的情况
arr = [[] for i in range(行数)]
#1.2已知列数未知行数的情况
arr = [[]*列数]
#1.3已知行数和列数的情况
arr = [[]*列数 for i in range(行数)]
----------------------------------------
#2.统计list中不同元素的个数
import collections
c = collections.Counter('helloworld')
#输出c为一个字典对象
#Counter{'l':3,'o':2,'h':1,'e':1,'r':1,'w':1,'d':1}
#想要排名前n的字符及它们的计数：
c.most_common(n)
----------------------------------------
#3.list删除元素
#情况一：删除索引index对应元素
#法一：
del num[index]
#法二：
num.pop(index)
#情况二：删除值value元素
num.remove(value)
----------------------------------------
#4.实用技巧：寻找list中第一个不重复元素
for i in range(len(num)):
    if i>0 and nums[i]==nums[i-1]:
        continue
#拓展：将list去重(条件：空间复杂度为O(1))
#使用双指针
i,j=0,1
while j<len(nums):
    if nums[i]==nums[j]:
        j+=1
    else:
        i+=1
        nums[i]=nums[j]
        j+=1
return num[:i+1]
----------------------------------------
#5.切片注意事项：
#切片赋值必须是可迭代对象
#Eg:a = [1,2,3],b = ['h','e','l']
a[1:]=[4]	#输出a = [1,4]
h[1:2]='n'	#输出b = ['h','n','l']
>>>>>>> 2020-12-19
```

**5.python时间对象**

```python
from  datetime import datetime
#1.字符串和datetime互相转化
#1.1 字符串->datetime
#法一：
value = '2021-1-1'
datetime.strptime(value,'%Y-%m-%d')
#法二：(通用)
from datautil.parser import parse
parse('2021-1-1')
#输出datetime(2021,1,1,0,0)
parse('Jan 31,1997 10:45 PM')
#输出datetime(2020, 1, 31, 22, 45)
#1.2 datatime->字符串
#法一：
stamp = datetime(2021,1,1)
str(stamp) #输出'2021-1-1 00:00:00'
#法二：
stamp = datetime(2021,1,1)
stamp.strftime('%Y-%m-%d')
```

**6.python线性素数筛**

```python
#给定一个数n，求小于n的素数个数
def countPrimes(n):
        if n < 3:
            return 0     
        else:
            # 首先生成了一个全部为1的列表
            output = [1] * n
            # 因为0和1不是质数,所以列表的前两个位置赋值为0
            output[0],output[1] = 0,0
             # 此时从index = 2开始遍历,output[2]==1,即表明第一个质数为2,然后将2的倍数对应的索引
             # 全部赋值为0. 此时output[3] == 1,即表明下一个质数为3,同样划去3的倍数.以此类推.
            for i in range(2,int(n**0.5)+1): 
                if output[i] == 1:
                    output[i*i:n:i] = [0] * len(output[i*i:n:i])
         # 最后output中的数字1表明该位置上的索引数为质数,然后求和即可.
        return sum(output)
```

**7.python字典处理**

```python
#1.统计value的最大值
dic = {1:1,2:8,3:3,4:7}
#输出最大值value
print(max(dic.values()))
#输出最大值对应的键值对
for key,value in dic.items():
    if(value == max(dic.values())):
        print key,value
----------------------------------------
#字母异位词分组
#Eg:["eat", "tea", "tan", "ate", "nat", "bat"]
#得到：[
#  ["ate","eat","tea"],
#  ["nat","tan"],
#  ["bat"]
#]
res = {}
for s in strs:
	keys = "".join(sorted(s))
	if keys not in res:
		res[keys]=[s]
	else:
		res[keys].append(s)
print(list(res.values()))
```

**8.python进制互转**

```python
#1.十进制转二进制
bin(num)
#bin(27)->'0b11011'
#注意输出的字符串开头是0b,可以通过切片取出想要的部分。
#2.二进制、八进制、十六进制转十进制
int('11011',2)
int('110',8)
int('A2B1',16)
```

**9.python的list转置**

```python
#1.已知二维list(全是0，1)，将每行看成一个二进制数，求二进制数对应的十进制数之和
Eg:
A = [[1,1,0,0],[1,0,1,0],[1,1,0,0]]
m = len(A[0])=4
#可以直接按照行求，那如果按照列求和呢？
#首先可以将A转置，有两种方法
#法一：A = zip(*A)
#法二：A = np.array(A).T.tolist()
A = [[1,1,1],[1,0,1],[0,1,0],[0,0,0]]
#代码：
for i in A:
    m-=1
    sum+=2**m*(i.count(1))
```

**10.python中的排列组合**

```python
#计算排列数和组合数
from scipy.sepcial import comb,perm
perm(3,2)
#输出为6
comb(3,2)
#输出为3
```

**11.python中的列表推导式**

```python
#已知一个字典s=s = {'2':['a','b','c'],'3':['d','e','f'],'4':['g','h','i'],'5':['j','k','l'],'6':['m','n','o'],'7':['p','q','r','s'],'8':['t','u','v'],'9':['w','x','y','z']}
#给定数字字符串，求字母组合
#输入："23"
#输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"]
代码：
res = ['']
for num in digits:
	res = [x+y for x in res for y in s[num]]
```
**12.python中的矩阵处理**

```python
#1.将二维矩阵顺时针旋转90°
Eg:matrix = [[1,2,3],[4,5,6],[7,8,9]]
#结果：[[7,4,1],[8,5,2],[9,6,3]]
#条件：原地旋转
import numpy as np
#法一：直接旋转
matrix[:] = np.rot90(np.array(matrix),-1).tolist()
其中：
	rot90第二个参数：正数为逆时针，负数为顺时针
#法二：上下翻转，然后转置
matrix[:]=np.array(matrix[::-1]).T.tolist()
----------------------------------------
```

**13.python中二叉树**

```python
#二叉树的定义
class TreeNode:
    def _init_(self,x):
        self.val = x
        self.left = None
        self.right = None
#1.二叉树的层序遍历(BFS)
if not root:
    return []
res = []
cur_level = [root]
depth = 0	#深度
while cur_level:	#外层循环判断结点
    tmp = []
    next_level = []
    for node in cur_level:#内层循环增加子结点
        tmp.append(node.val)
        if node.left:
            next_level.append(node.left)
        if node.right:
            next_level.append(node.right)
    res.append(tmp)
    depth+=1
    cur_level = next_level		#★
return res
#注意：cur_level和next_level存的都是结点，而不是结点的值
```

