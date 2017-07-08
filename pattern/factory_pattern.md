1、工厂方法的定义：
   Define an interface for creating an object , but let subclassess decide which class to instantiate.Factory Method lets a class defer instantiation to subclassess .定义一个用于创建对象的接口，让子类决定实例化哪一个类。工厂方法使一个类的实例化延迟到其子类。
2、工厂方法的优点
   工厂模式扩展性非常的好，屏蔽了产品类，是典型的解藕框架，符合迪米特法则，也符合依赖倒置原则，也符合里氏替换原则。
3、工厂方法使用场景
   当需要灵活和可扩展的框架时可以考虑采用工厂方法模式。