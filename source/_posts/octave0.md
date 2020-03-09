---
title: how to use octave
date: 2018-04-12 16:56:42
categories:
- octave
tags:
- paint
- octave
- tutorial
---
Recently,i learn machine learning in coursera.The lesson is teached by Andrew Ng.

Here is the address.[address](https://www.coursera.org/)

You can search Andrew Ng or machine learning.

 <!-- more -->

So,my note about octave is here.

## tips

{% codeblock %}
ctrl + c
	强制退出
pause;
	相当于 stop
%	
	注释符号
~=	
	不等于符号
连续使用命令只需要在命令之间加 , 即可
如果不想显示提示（如显示每一次值），可以在语句后面加 ;
pwd
	显示当前路径
ls
	列出文件
who
	在 octive 存储的变量
whos
	更详细的探查变量
clear 变量名
	删除某一个变量
clear
	删除所有变量
end
	end的出现只出现在方法结尾，控制语句结尾，如果整个文件没有方法或者控制语句，就不需要 end
{% endcodeblock %}

## 统计数据的几种形式

{% codeblock %}
data1 = [[1 2 3]
         [4 5 6]
];
size(data1)
	% 2   3
data2 = [[1,2,3]
        [2,3,4]
];
size(data2)
	% 2   3
data3 = [[1    2    3]
          [2     4    5]];
size(data3)
	% 2   3
data4 = [[1  2    3]
          [2     4  5]];
size(data4)
	% 2   3
data5 = [[1,2    3,4]
          [2     4    5,7]];
size(data5)
	% 2   4
data6 = [[1    2    3],
          [2     4    5]];
size(data6)
	%  2   3
data7 = [1    2    3,2     4    5];
size(data7)
	% 1   6
data8 = [[1    2    3  8] [2     4    5 9]];
size(data8)
	% 1   8	
{% endcodeblock %}

## basis

{% codeblock %}
A = [1 2;3 4;5 6] 建立矩阵
V = 1:0.1:2 一个行向量从 1 到 2，增量为0.1
V = 1:6 一个行向量，从 1 到 6，增量为 1
A = ones(2,3)
B = 2 * ones(2,3)
A = zeros(1,2)
A = rand(n)
	返回 n * n 的随机数矩阵
A = rand(1,3) 
	返回 1 * 3 的矩阵随机数，介于0 - 1之间
A = randn(1,3) 符合高斯分布的随机数
A = eye(n)
	得到 n * n 的单位向量
A'
	得到 A 的转置
A(n)
	表示第一列第 n 个元素，即便 A 是矩阵
	A = [
		1 2 3
		4 5 6
		]
	A(1)
		为 1
A(3,2)
	得到 A 第三行 第 2 列的数据
A(2,:) 
	第 2 行的所有元素，: 代表所有元素
A(:,2)
	第 2 列的所有元素
A([1 3],:)
	取 A 矩阵第一个索引为 1 或 3 的元素，即 第一 和第三行的所有元素
A(1:3,:)
	取 A 中行数为 1 2 3的所有元素
A(:,2) = [10;11;12]
	可以直接赋值
A = [A,[100；101；102]]
	在 A 的右边增加一列
A(:)
	将 A 中的所有元素排成一个向量
C = [A B]  也可以写成 [A,B]
	C 是 A B 的相连
C = [A ; B]
	C 是 A B 的上下相连
A .* B
	将 A 中的元素和 B 中对应的元素进行点乘
	A = [1 2;3 4]
	B = [1 2;3 4]
	A .* B = [1 4;9 16]
A .^2
	对 A 中每一个元素进行乘方
1 ./ V
	将 V 中每一个元素求倒数
A .* eye(n)
	A 的对角线上的元素不变，其他的全为零
{% endcodeblock %}

## functions

{% codeblock %}
复杂输出 disp(a)
	 disp(sprintf('2 decimals: %0.2f',a)) 类似 c 的风格,这种可以格式化输出
pinv(A) 伪逆
	求 A 的逆矩阵,甚至求不可逆矩阵的逆
inv(A)
	求逆
log(V)
exp(V)
	每个元素 V 的 e 的平方
abs(V)
	将 负数变正
-V
	相反数
V + ones(length(V),1)
	将 V 向量各自加一
V + 1
	也能直接将 V 中各元素加一
hist(W) 绘制直方图
val = max(A)
	返回V中元素最大值，如果A为矩阵，则返回 A 中每一列的最大值
	A = [1 2;3 4]
	max(A)
		[3;4]
	max(rand(3),rand(3))
		返回的确实 3 * 3 的矩阵，是因为在一个机制下选的是同一位置中 A B 中的最大的
		A = [1 2;3 4]
		B = [2 1;4 6]
		max(A,B)
			返回 [2 2;4 6]
max(A)
		返回 A 中每一列的最大值，返回一个 1 * n 矩阵
max(A,[],1)
		返回的是 A 中每一列的最大值，返回一个 1 * n 矩阵
max(A,[],2)
		返回的是 A 中每一行的最大值，返回的是 n * 1 矩阵
max(max(A))
		返回矩阵A 中的最大值	
[val,ind] = max(A)
	val 是最大值 ind 是索引
randperm(n)
	产生1到n的整数的无重复的随机排列，利用它就可以得到无重复的随机数
	randperm(10)
	ans =
		6     4     8     9     3     5     7    10     2     1
rand(n)
	生成0到1之间的n阶随机数方阵
rand(m,n)
	生成0到1之间的m×n的随机数矩阵
randint(m,n,[1 N])
	生成m×n的在1到N之间的随机整数矩阵，其效果与randint(m,n,N+1)相同。
randint(3,4,[1 10])
	ans =
		5     7     4    10
		5     1     2     7
		8     7     8     6
A < 3
	会返回每一个元素 和3的比的布朗值
	A = [1 4 2 1]
	A < 3
	输出 ans = [1 0 1 1]
find(a < 1)
	返回小于 1 的元素索引
[r,c] = find(A > 7)
	r 代表行索引
	c 代表列索引
sum(A)
	得到A的元素和
	
A = [1 2 ; 3 4]
sum(A)
	[4 6]
sum(sum(A))
	10

sum(A,1)
	得到 A 中每一列的总和，返回 1 * n 矩阵
sum(A,2)
	得到 A 中每一行的总和，返回 n * 1 矩阵
sum(sum(A .* eye(n)))
	得到 A 的对角线之和
flipud(A)
	使矩阵垂直翻转
sum(sum(A .* flipub(eye(n))))
	得到另一条对角线之和
prod(A)
	返回 元素的乘积
floor(A)
	对 A 中的元素向下取整
ceil(A)
	对 A 中的元素向上取整
A = magic(n)
	返回一个 n * n 的幻方
	幻方：任一行，列，对角线加在一起都是相同的值
size(A)
	返回尺寸
size(A,1) 
	返回行数
size(A,2)
	返回列数
length(V)
	返回最大维数，比如 1 * 4，最大为4,；3 * 2 最大为3
numel(A)
	返回 A 的元素总数
rref()	
	對角化	c=[A b]; rref(C)
det()	
	方陣行列式的值	det(A)
rank()	
	矩陣的秩 (維度)	rank(A)
diag()	
	取對角線成一向量	diag(A)
B = reshape(A(1:110),10,11);
	从向量 A 中提取索引为 1 到 110 的元素，将它们排成 10 * 11 的矩阵
B = [A(:);C(:)];
	可以将 A 和 C 矩阵变成一个向量
{% endcodeblock %}

## calculation

{% codeblock %}
abs(x)		
	純量的絕對值或向量的長度	
angle(z)		
	複數z的相角(Phase angle)	
sqrt(x)		
	開平方	
real(z)		
	複數z的實部	
imag(z)		
	複數z的虛部	
conj(z)		
	複數z的共軛複數	
round(x)		
	四捨五入至最近整數	
fix(x)		
	無論正負，捨去小數至最近整數	
floor(x)		
	地板函數，即捨去正小數至最近整數	
ceil(x)		
	天花板函數，即加入正小數至最近整數	
rat(x)		
	將實數x化為分數表示	
rats(x)		
	將實數x化為多項分數展開	
sign(x)		
	符號函數 (Signum function)	
rem(x,y)		
	求x除以y的餘數	
gcd(x,y)		
	整數x和y的最大公因數	
lcm(x,y)		
	整數x和y的最小公倍數	
exp(x)		
	自然指數	
pow2(x)		
	2的指數	
log(x)		
	以e為底的對數，即自然對數或	
log2(x)		
	以2為底的對數	
log10(x)		
	以10為底的對數	
sin(x)		
	正弦函數	
cos(x)		
	餘弦函數	
tan(x)		
	正切函數	
asin(x)		
	反正弦函數	
acos(x)		
	反餘弦函數	
atan(x)		
	反正切函數	
atan2(x,y)		
	四象限的反正切函數	
sinh(x)		
	超越正弦函數	
cosh(x)		
	超越餘弦函數	
tanh(x)		
	超越正切函數	
asinh(x)		
	反超越正弦函數	
acosh(x)		
	反超越餘弦函數	
atanh(x)		
	反超越正切函數	
min(x)		
	向量x的元素的最小值	
max(x)		
	向量x的元素的最大值	
mean(x)		
	向量x的元素的平均值	
median(x)		
	向量x的元素的中位數	
std(x)		
	向量x的元素的標準差	
diff(x)		
	向量x的相鄰元素的差	
sort(x)		
	對向量x的元素進行排序（Sorting）	
length(x)		
	向量x的元素個數	
norm(x)		
	向量x的歐氏（Euclidean）長度	
sum(x)		
	向量x的元素總和	
prod(x)		
	向量x的元素總乘積	
cumsum(x)		
	向量x的累計元素總和	
cumprod(x)		
	向量x的累計元素總乘積	
dot(x, y)		
	向量x和y的內積	
cross(x, y)		
	向量x和y的外積	
常數
i 或 j		
	基本虛數單位	
eps		
	系統的浮點（Floating-point）精確度	
inf		
	無限大， 例如1/0	
nan或NaN		
	非數值（Not a number），例如0/0	
pi		
	圓周率 p（= 3.1415926…）	
realmax		
	系統所能表示的最大數值	
realmin		
	系統所能表示的最小數值	
nargin		
	函數的輸入引數個數	
nargout		
	函數的輸出引數個數
{% endcodeblock %}

## files

{% codeblock %}
load('文件名')
	加载文件数据，当我们添加文件后，变量就是以文件名命名
	load(data1.txt)
	data1 %里面存储着数据
load('data1.txt')
V = data1(1:10) data1前10个值赋给V
save a.mat v 将v变量存储在a.mat中，存储格式是压缩的二进制
save a.txt v -ascii 将v变量存储为可以看得懂的格式，文本文档
{% endcodeblock %}

## draw

{% codeblock %}
plot(x,y)
	画图
	t = [0:0.1:0.98]
	y1 = sin(2 * pi * 4 * t)
	plot(t,y1) 
hold on;
	可以在原来的图像上继续绘制
	plot(x1,y1);
	hold on;
	plot(x2,y2,'r'); 用红色画
xlabel('xxxx')
	x 轴的名称
ylabel('xxxx')
	y 轴的名称
legend('sin','cos')
	标记两条曲线
title('xxxx')
	添加标题
print -dpng 'xxx.png'
	输出保存图像
	改变路径存储
	cd 'path';
	print -dpng 'xxx.png'
close
	关掉图像
绘制多图
	figure(1): plot(x1,y1);
	figure(2): plot(x2,y2);
subplot(n,m,k);
	将图像分成 n * m 个的格子，将图像放入第 k 个格子中
	通常结合使用
	subplot(n,m,k);
	plot(x2,y2);
设置坐标轴
	axis([0.5 1 -1 1])
		横坐标是 0.5 到 1
		纵坐标是 -1 到 1
	plot(x2,y2);
	axis([0.5 1 -1 1])
clf;
	清除图片
绘制矩阵
	imagesc(A);
colorbar;
	颜色条
colormap gray
	灰度显示
	imagesc(A);
	colorbar;		
	colormap gray；
	绘制好图之后，如果直接 A(1,2) 将会得到A(1,2)在colorbar中的位置

繪圖點的形狀與顏色
字元	顏色	字元	圖線型態
y		黃色	.		點
k		黑色	o		圓
w		白色	x		x
b		藍色	+		+
g		綠色	*		*
r		紅色	-		實線
c		亮青色	:		點線
m		錳紫色	-.		點虛線
—		虛線		

指令		圖形
bar			長條圖
errorbar	圖形加上誤差範圍
fplot		較精確的函數圖形
polar		極座標圖
hist		累計圖
rose		極座標累計圖
stairs		階梯圖
stem		針狀圖
fill		實心圖
feather		羽毛圖
compass		羅盤圖
quiver		向量場圖
画直方图
y = randn(10000,1);
hist(Y);
	默认十个等间隔区间，并返回每个范围内的Y的元素个数作为一行向量
hist(Y,nbins);
	nbins是间隔数，也就是说我们应该统计多少个间隔，这里设nbins=20
{% endcodeblock %}

## control

{% codeblock %}
for：
	for i = 1:10,
		V(i) = 2 ^i;
	end;
	for i = 1:10,
		for j = 1:10,
			V(i) = 2 ^i;
		end;
	end;
indices = 1:10;
for i = indices,
	disp(i);
end;
	for也能正常执行
while：
	i = 1;
	while i <= 5,
		V(i) = 100;
		i = i+ 1;
	end;
break;
	while true,
		V(i) = 999;
		i = i + 1;
		if i == 6,
			break;
		end;
	end;
else
	if V(i) == 2,
		disp('xxx');
	elseif V(i) == 1,
		disp('xxx');
	else
		disp('xxx');
	end;
{% endcodeblock %}

## function by myself

{% codeblock %}
在octive中定义函数，首先需要定义一个和 函数名 相同的文件，后缀是 m
	首先
	function y = 方法名(变量)
		将方法赋值给变量 y
	再对 y 进行编辑
	要调用方法首先到文件的那个目录，然后 输入 文件名(变量)
	当然每次都需要先找到路径太麻烦，可以先将路径添加到工作环境中
	addpath('xxx')
	以后如果改变路径的话，如果输入函数在当前路径中找不到，octive会自动到 xxx 中找，相当于win的path，不过是暂时的
	octive 允许返回多个函数
		function[y1,y2] = 方法(参数)
		y1 = xxx;
		y2 = xxx;
{% endcodeblock %}
当然在方法中也是可以有返回值的，文件名必须和方法名一致。
{% codeblock %}
function g = sigmoid(z)
g = 1.0 ./ (1.0 + exp(-z));
end
{% endcodeblock %}

## 如何使用 octave 文件

当我们建了一个文件名为 fun.m 代码如下：

{% codeblock %}
W = [3.00774324,1.69532264]
x = 0:0.1:1
y = x * W(:,2) + W(:,1)
plot(x, y, 'k', 'LineWidth', 1.5);
{% endcodeblock %}
我们只需要在控制台直接输入 fun 即可。
如果我们创建了一个 test.m 代码如下：
{% codeblock %}
function test()
	W = [3.00774324,1.69532264]
	x = 0:0.1:1
	y = x * W(:,2) + W(:,1)
	plot(x, y, 'k', 'LineWidth', 1.5);
end;
{% endcodeblock %}

我们只需要在控制台直接输入 test() 即可。

## examples

test.m

{% codeblock %}
function test()
data = load('ex2data1.txt');

X = data(:,[1,2]);
y = data(:,3);

A = find(y == 0);    % 得到的也是向量
B = find(y == 1);
figure;
hold on;
plot(X(A,1),X(A,2),'k+','LineWidth',2,'MarkerSize',7);  % k+ 是指图形为+ ，LineWidth 2 线的宽度为2，MarkerSize 7 指的是大小
plot(X(B,1),X(B,2),'ko','MarkerFaceColor','r','MarkerSize',7); % ko 是指图像为 o ，MarkerFaceColor r 图像为红色的
hold off;
end;
{% endcodeblock %}

Finally,the picture is following.

![](/images/octave/0.jpg)


