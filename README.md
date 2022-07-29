# all-projects
网安作业
# 1、小组成员  
个人实验，没有组队
Github账户名称：liaoxinan  
Github地址链接： https://github.com/liaoxinan/all-projects  
201900460057 廖中鑫  
# 2、所做项目  
SM3的长度扩展攻击  
SM3的生日攻击  
SM3的rho攻击
完成人：廖中鑫  
# 3、项目清单  
完成的项目：  
SM3的长度扩展攻击  
SM3的生日攻击
有问题的项目：  
SM3的rho攻击 问题原因：没跑出来  
# 4、项目过程  
## （1）SM3的长度扩展攻击  
随机生成一个消息(secret)，用SM3函数算出hash值(hash1)  
生成一个附加消息(m')。首先用hash1推算出这一次加密结束后8个向量的值，再以它们作为初始向量，去加密m’，得到另一个hash值(hash2)  
计算secret + padding + m'的hash值(hash3)，如果攻击成功，hash2应该和hash3相等  
SM3的消息长度是64字节或者它的倍数，如果消息的长度不足则需要padding。在padding时，首先填充一个1，随后填充0，直到消息长度为56(或者再加整数倍的64)字节，最后8字节用来填充消息的长度。  
在SM3函数计算时，首先对消息进行分组，每组64字节，每一次加密一组，并更新8个初始向量(初始值已经确定)，下一次用新向量去加密下一组，以此类推。我们可以利用这一特性去实现攻击。当我们得到第一次加密后的向量值时，再人为构造一组消息用于下一次加密，就可以在不知道secret的情况下得到合法的hash值，这是因为8个向量中的值便能表示第一轮的加密结果。  
随机生成了一个浮点数作为secret，并计算得到了hash值。要得到第一次加密之后8个向量的值，只需要将hash值按8字节分组，并把每组的值转换成int类型(因为python库的sm3实现中向量值是用int型存储的)。  
得到了向量值后，便可以开始构造消息。由于我们不需要知道secret的值，只知道secret的长度，所以secret部分可以用等长的任意字符代替(我这里用的是’a')。随后进行padding，得到64字节的消息，再将附加信息放在后面，消息就构造完成了。  
接着进行加密，由于此时只需要对附加的消息进行加密，所以我修改了一下sm3的函数实现，增加了一个new_v参数，表示更新之后的向量值。此外这次加密的次数要比之前少一次，从消息的第64字节开始加密，即可得到hash值。相关代码如下。  
![image](https://user-images.githubusercontent.com/109905958/181852201-3dbb8301-34d7-4ce6-85de-c38084ddf65c.png)

结果：  
![image](https://user-images.githubusercontent.com/109905958/181852458-853edfa3-6b12-4def-bafc-6e524d73f89f.png)

## （2）SM3的生日攻击  
利用两个集合相交的原理，生成散列函数碰撞，形成攻击。生日攻击方法没有利用hash函数的结构和任何代数弱性质，它只依赖于hash值的长度。  
相关代码：  
![image](https://user-images.githubusercontent.com/109905958/181852172-de41e49e-4395-4e3f-9c58-960d798a3040.png)

实验结果：  
![image](https://user-images.githubusercontent.com/109905958/181852173-86d383dd-d875-4d06-8076-5716f7aa3c1a.png)

## （3）SM3的rho攻击  
假设一个有m个点的图，每个点都有一个出度，这样随机形成的图中，无论是子基环图（Rho形）还是环本身，包含点数的期望都会是√m级别的。  
倘若要分解的数是n，那么将迭代产生的数列进行连接可以得到一个基环图，不妨称之为大Rho。考虑其中最小的那个因子m，必然不会超过√n，把n个点中关于m同余的点合并成一个点，那么每次迭代步形成的图的大小就不会超过√(√n) ，可以称之为小Rho，我们也可以发现，大Rho的环形可以看作由多个小Rho的环剪开顺次连接形成，我们现在要做的，就是在大Rho上找出点x和y，使得x和y不同，但在小Rho图中对应的点是相同的（碰撞）。那么实际上要解决的就是找碰撞值，或者说在小Rho图上做一个找环操作。  
代码： 
![image](https://user-images.githubusercontent.com/109905958/181852050-c20630f0-629c-4ae6-8807-69a5350a2f3b.png)





