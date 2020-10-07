### 什么是Spring IOC

IOC利用反射机制，去实现所谓的控制反转。本来被调用者的实例是由调用者来实现的，利用SpringIOC我们可以将对象交由Spring管理，让Spring帮我们创建对象和管理对象，降低对象与对象之间的耦合。Spring对于IOC控制反转是通过DI依赖注入来实现的，使得能在程序运行阶段向某个对象提供他所需要的其他对象。

### SpringIOC加载流程

以```ClassPathXmlApplicationContext```为例，使用```ClassPathXmlApplicationContext```实例化```ApplicationContext```接口，传入参数是我们的xml文件路径。

> 1.进入```ClassPathXmlApplicationContext```的构造方法，最终会走他的父类```AbstractApplicationContext```的refresh方法。
>
> 2.refresh方法会先new一个```DefaultListableBeanFactory```，之后读取和解析我们路径中的xml文件，然后将我们xml文件中配置的类信息解析成```BeanDefinition```，再放到```DefaultListableBeanFactory```的beanDefinitionMap中，也就是将我们配置的Bean进行一个注册。
>
> 3.回到refresh方法中，接下来会执行postProcessBeanFactory方法注册```BeanFactoryPostProcessor```，postProcessBeanFactory方法是Spring提供的第一个扩展点。
>
> 4.调用上一步注册的```BeanFactoryPostProcessor```的postProcessBeanFactory方法，一般可以在这个方法里去修改已经注册的```BeanDefinition```类信息。
>
> 5.注册```BeanPostProcessor```，这是Spring提供的第二个扩展点。
>
> 6.注册一些监听，事件啥的。
>
> 7.会去实例化我们配置的非懒加载的单例Bean。这一步能再拆开来说。
>
> 7.1会走```DefaultListableBeanFactory```的getBean方法，之后会走到父类```AbstractAutowireCapableBeanFactory```的doCreateBean方法里面，然后会先实例化Bean，实例化Bean的过程中会去判断Bean的创建方式，是通过Supplier创建，还是工厂方法创建，还是通过构造器创建。还会使用一个```BeanPostProcessor```去推断构造器。然后去实例化Bean。
>
> 7.2之后会走populateBean去进行Bean属性的填充。
>
> 7.3走initializeBean方法去调用注册的BeanProcessor，还有完成一些Bean生命周期的一些工作。
>
> 7.4最后将Bean放进单例池里缓存起来。

### 什么是SpringAOP

AOP，面向切面编程。面向对象编程解决了**业务模块的封装复用**的问题，但是对于某些模块，他本身并不独属于某个业务模块，而是根据不同的情况，贯穿于某几个或全部的模块之间的。比如性能统计，他需要记录每个业务模块的调用，并且监控器调用时间，这些横贯于每个业务模块的模块，如果使用面向对象的方式，那么就需要在已封装的每个模块中添加相应的重复代码，对于这种情况，面向切面编程就适合这种场景。

面向切面编程，指的是将一定的切面逻辑按照一定的方式编织到指定的业务模块中，从而将这些业务模块的调用包裹起来。SpringAOP是基于动态代理的，可以依赖JDK动态代理也可以依赖Cglib动态代理。





