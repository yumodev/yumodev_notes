## 在ATOM中使用PlantUML

### PlantUML介绍

    PlantUML 可以用来画类图和流程图，类似MarkDown语法那样简单实用。
    
### 安装插件

   * language-plantuml
   * plantuml-viewer

### 创建测试文件 test.puml

      @startuml
      Class01 <|-- Class02:泛化
      Class03 <-- Class04:关联
      Class05 *-- Class06:组合
      Class07 o-- Class08:聚合
      Class09 -- Class10

      @enduml

### 查看效果

    ctrl+shift+p 查看预览效果

    如果提示 "Cannot find Graphviz",那么安装 Graphviz 后就可以解决这个问题


### 安装Graphviz

    * mac : brew install graphviz
    * ubuntu : apt-get install graphviz

### 导出Png图片
