#  essentisl com record
## chapter 1
### 简介
主要讲*com*的好处  
1. 抽象基类作为二进制接口
2. 关于虚类  
   编译器在后台为虚类产生一个静态函数指针数组，通常称作虚表(vtbl),每个虚成员函数（或者基类的）都有一个相应的函数指针，虚类的每一个实例包含一个不可见的数据成员（vptr),这个指针被构造函数自动初始化，指向vtbl。当客户调用虚函数时，编译器产生代码反指向vptr,索引到vtbl中，然后在指定的位置找到函数指针，并发出调用。这就是c++中实现多态性以及动态调用分发的过程。
3. 接口类的要求  
   为了保证同一平台所有编译器对接口调用代码产生的机器码相同，所有对数据操作的public函数都被定义为虚函数。1）接口类不能包含数据成员，2）接口类不能从其他接口类派生.

class IClass{  
    public:
    void method()=0;
};

4. 为什么需要类工厂？
   假设接口类的实现如下：  

   class ClassImp:public IClass{
public:
    void method(){
        //todo..
    }
   };

如果客户端没有ClassImp的定义，仍然无法使用ClassImp,因此需要类工厂  
IClass* IClassFactory(){  
   return new ClassImp();  
} 
5. 为什么用Delete函数删除对象而不用虚析构函数？
   因为虚析构函数在vtbl中的位置随着编译器的不同而不同,破坏了接口类的编译器独立性。因此增加一个虚的Delete接口来删除自身，在实现类中这样定义：  
   void ClassImp::Delete(){
      delete this;
   }