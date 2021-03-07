#  essential com record
## chapter 1
第一章通过如何构造独立的二进制组件，通过他来引出com的基本结构。  

1. 抽象基类作为二进制接口
2. 关于虚类  
   编译器在后台为虚类产生一个静态函数指针数组，通常称作虚表(vtbl),每个虚成员函数（或者基类的）都有一个相应的函数指针，虚类的每一个实例包含一个不可见的数据成员（vptr),这个指针被构造函数自动初始化，指向vtbl。当客户调用虚函数时，编译器产生代码反指向vptr,索引到vtbl中，然后在指定的位置找到函数指针，并发出调用。这就是c++中实现多态性以及动态调用分发的过程。
3. 接口类的要求  
   为了保证同一平台所有编译器对接口调用代码产生的机器码相同，所有对数据操作的public函数都被定义为虚函数。1）接口类不能包含数据成员，2）接口类不能从其他接口类派生.
```c++
//接口定义
class IClass{  
    public:
    void method()=0;
};
//接口实现
class ClassImp:public IClass{
   public:
   void method(){
      //todo..
   }
private:
   char* m_data;
};
```
4. 类工厂的引入
   如果客户端没有ClassImp的定义，仍然无法创建ClassImp对象,因此需要类工厂 
```c++ 
IClass* IClassFactory(){  
   return new ClassImp();  
}   
```
5. 为什么用Delete函数删除对象而不用虚析构函数？
   因为虚析构函数在vtbl中的位置随着编译器的不同而不同,破坏了接口类的编译器独立性。因此增加一个虚的Delete接口来删除自身，在实现类中这样定义：
```c++  
   void ClassImp::Delete(){
      delete this;
   }
```
6. 动态调用dll的好处  
   1）避免没有安装该dll的机器上可以避免操作系统产生错误，对DLL没有依赖性  
   2）减少进程地址空间的初始化的工作。（只在需要的时候加载）  
   3）加快客户的启动速度  
   4）节省地址空间  
7. 使用自定义DynamicCast而不使用dynamic_cast(RTTI)的原因  
   这是由于RTTI的实现与编译器相关，因此显示的实现DynamicCast  
```c++
class IExtensibleObject{
public:
virtual void* DynamicCast(const char* pszType)=0;
virtual void Delete()=0;

class IPersistenObject : public IExtensibleObject{
   public:
   virtual bool load(const char* filename)=0;
   virtual bool save(const char* filename)=0;
};

class IClass :public IExtensibleObject{
   public:
   virtual int length()=0;
   virtual int find(const char* s)=0;
};

class ClassImp : public IPersistenObject,public IClass{
   public:

   void* DynamicCast(const char* pszType);
   void Delete();

   bool load(const char* filename);
   bool save(const char* filename);

   int length();
   int find(const char* s);
};
```
DynamicCast函数实现如下：  
```c++
void* ClassImp::DynamicCast(const char* pszType){
   if(strcmp(pszType,"IClass")==0)
      return static_cast<IClass*>(this);
   else if(strcmp(pszType,"IPersistenObject")==0)
      return static_cast<IPersistenObject*>(this);
   else if(strcmp(pszType,"IExtensibleObject")==0)
      return static_cast<IExtensibleObject*>(this);//need virtual base class
   return 0;
}
```

8. 资源管理  
由于Delete方法只能调用一次，因此会增加客户端对这些对象的管理负担。解决方案是让每个对象都维护一个引用计数，当接口指针被复制的时候，该计数值增加；当接口指针被销毁时，该计数值减少 
```c++
clas IExtensibleObject{
public:
   virtual void* DynamicCast(const char* pszType)=0;
   virtual void AddRef()=0;   // counter++
   virtual void Release()=0;  //counter--  if(counter==0) delete this
};
```
客户端使用时，需要遵循两条规则，1）接口指针被复制时，调用AddRef;2)接口指针不再使用时，调用Release.
9.  总结
    第一章通过一个简单的类，将接口和实现分离，创建和实践分离，通过DynamicCast实现运行时接口查询，整个过程是**组件对象模型**(**COM**,componet object model)的的设计过程

