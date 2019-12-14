# gradle


## 基本语法



## 构建脚本概要
### 构件块
Gradle构建中的两个基本概念是项目(project)和任务(task)，每个构建至少包含一个项目，项目中包含一个或多个任务。在多任务构建中，一个项目可以依赖于其他项目；类似的，任务可以形成一个依赖关系图来确保他们的指向顺序。

![](assets/20191210104544437_1607704619.png =800x)



### 项目(project)
一个项目代表一个正在构建的组件（比如一个jar文件），当构建启动后，Gradle会基于build.gradle实例化一个 `org.gradle.api.Project`类，并且能够通过project变量使其隐式可用。

通过 `group、name、version`可确定一个组件

`apply、dependencies、repositories、task`

属性的其他配置方式：ext、gradle.properties

### 任务(task)
任务对应`org.gradle.api.Task`。主要包括任务动作和任务依赖。任务动作定义了一个最小的工作单元。可以定义依赖于其他任务、动作序列和执行条件。

`dependsOn`
`doFirst、doLaast <<`



## 构建的生命周期

初始化- 》配置（任务代码） -》执行（动作代码）

![](assets/20191210115034028_1715815780.png =800x)


## 依赖管理

几乎所有的基于JVM的软件项目都需要依赖外部类库来重用现有的功能。自动化的依赖管理可以明确依赖的版本，可以解决因传递性依赖带来的版本冲突。

### 工件坐标
工件可以简单的理解为jar包
`group、name、version`

### 常用仓库
`mavenLocal`本机maven仓库
`mavenCentral、jcenter`公共仓库

最常用的是 自定义maven仓库

![](assets/20191210140359406_900149211.png =800x)


![](assets/20191210140532113_1367123136.png =800x)
