# 度量空间和赋范线性空间
## 度量空间的定义
设$X$是一个集合，若对中的任意两个$x,y$元素，都有唯一确定的实数$d(x,y)$与之对应，而且这一对应关系满足下列条件：
1. $d(x,y)>=0,d(x,y)=0<--> x=y$;
2. $d(x,y)<=d(x,z)+d(y,z),\forall z$;  
则称$d(x,y)$是$x,y$之间的距离，称为**度量空间**。  
* ex 1 离散的度量空间
设$X$是一个非空集合，对$X$中任意两点$x,y\in X$,令
$$d(x,y)=\begin{cases}
    1,\quad x\neq y\\
    0, \quad x=y
\end{cases}$$
则$d(x,y)$为度量空间，称$(X,d)$为离散的度量空间  
* ex2 序列空间$S$.
令$S$表示实数列的全体，对$S$中的任意两点$x=(\xi_1,\xi_2,\dots ,\xi_n,\dots)$及$y=(\eta_1,\eta_2,\dots ,\eta_n,\dots)$，令
$$d(x,y)=\sum_{i=1}^n \frac{1}{2^i}\frac{|\xi -\eta|}{1+|\xi -\eta|}$$
验真知$d(x,y)$满足条件，即$S$按$d(x,y)$为度量空间  

$$
\begin{pmatrix}
x_1 & x_2 &\dots\\
x_3 & x_4 &\dots\\
\vdots&\vdots&\ddots
\end{pmatrix}
$$