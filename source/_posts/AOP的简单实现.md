---
title: AOP的简单实现
url: 31.html
id: 31
categories:
  - spring基础
date: 2018-02-04 15:49:07
tags:
---

实现类
---

    package com.csii.ifp.action;
    
    public class HelloWorld {
        public String sayHello(){
            return "Hello World!";
        }
    }
    

xml文件配置
-------

    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
        xmlns:aop="http://www.springframework.org/schema/aop"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
                         http://www.springframework.org/schema/aop/spring-aop.xsd">
    
        <bean id="testAction" class="com.csii.ifp.action.HelloWorld"></bean>
        <bean id="DamselRescuingKnight" class="com.csii.ifp.action.DamselRescuingKnight">
            <constructor-arg ref="RunQuest" />
        </bean>
        <bean id="BraveQuest" class="com.csii.ifp.action.BraveQuest"></bean>
        <bean id="RunQuest" class="com.csii.ifp.action.RunQuest"></bean>
        <bean id="MyMinstrel" class="com.csii.ifp.action.MyMinstrel"></bean>
    
        <aop:config>
            <aop:aspect id="my-aspect1" ref="MyMinstrel">
            <!--定义切点-->
                <aop:pointcut id="emark" expression="execution(* *.emarkQuest(..))"></aop:pointcut>
            <!--定义在切点出需要执行的方法-->
                <aop:before pointcut-ref="emark" method="singBefore"></aop:before>
                <aop:after pointcut-ref="emark" method="singAfter"></aop:after>
            </aop:aspect>
        </aop:config>
    </beans>
    

这里需要的注意的是，在xml是命名空间里面一定要引入aop的xsd文件。

AOP的原理-动态代理
-----------

spring的aop采用的_jdk动态代理_，下面是动态代理的简单实现

    package com.csii.ifp.action;
    
    import java.lang.reflect.InvocationHandler;
    import java.lang.reflect.Method;
    import java.lang.reflect.Proxy;
    
    public class JdkProxyHandler implements InvocationHandler {
        private Object target;
        public JdkProxyHandler(Object object) {
            super()    public
                ;
            this.target=object;
        }
        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            Object retVal=null;
            if (method.getName().equals("emark")) {
                System.out.println("before invoke handler");
                 retVal=method.invoke(target, args);
                System.out.println("after invoke handler");
            }else{
                 retVal=method.invoke(target, args);
            }
            return retVal;
        }
        public Object getProxy(){
            return Proxy.newProxyInstance(Thread.currentThread().getContextClassLoader(), target.getClass().getInterfaces(),this);
        }
    }
    

上面实现了一个简单的动态代理处理类，其中的实现的接口InvocationHandler标明的它是一个动态代理。通过

    Proxy.newProxyInstance(Thread.currentThread().getContextClassLoader(), target.getClass().getInterfaces(),this)
    

返回一个所代理的类的实例。下面是具体的使用方法：

    package com.csii.ifp.action;
    
    public class ProxyTest {
        public static void main(String[] args) {
            JdkProxyHandler handler=new JdkProxyHandler(new RunQuest());
            Quest quest=(Quest) handler.getProxy();
            quest.emark();
        }
    }
    

其中RunQuest是一个具体的实现类，实现了Quest接口。通过把这个类代理给JdkProxyHandler，这样可以使用反射获得一个新的实例，其中所有的方法都会经过代理类的**invoke**方法。可以在调用方法时统一做一下特殊的处理。*其中代理类转型的只能是一个接口，这里的原因在下面[_JDK动态代理章节_](#jdk动态代理的原理)会讲到。

### jdk动态代理的原理

动态代理涉及的底层方法很多，但是主要的逻辑不是很复杂，需要主要的关注的就是那么几个方法。 首先看构造实例的方法

        @CallerSensitive
     static Object newProxyInstance(ClassLoader loader,
                                              Class<?>[] interfaces,
                                              InvocationHandler h)
            throws IllegalArgumentException
        {
        //必须传入一个InvocationHandler的接口
            Objects.requireNonNull(h);
    //克隆出需要代理的接口，作用未知
            final Class<?>[] intfs = interfaces.clone();
            //调用者对于这些代理类接口的权限。
            final SecurityManager sm = System.getSecurityManager();
            if (sm != null) {
                checkProxyAccess(Reflection.getCallerClass(), loader, intfs);
            }
    
            /*
             * Look up or generate the designated proxy class.
             */
             //查找或者生成代理类的对象
            Class<?> cl = getProxyClass0(loader, intfs);
    
            /*
             * Invoke its constructor with the designated invocation handler.
             */
            try {
            //检查是否有cl操作的权限
                if (sm != null) {
                    checkNewProxyPermission(Reflection.getCallerClass(), cl);
                }
    //通过反射使用构造器生成构造器对象。
                final Constructor<?> cons = cl.getConstructor(constructorParams);
                final InvocationHandler ih = h;
            //如果修饰符不是public的，
                if (!Modifier.isPublic(cl.getModifiers())) {
                //访问控制器，可以在run方法中写入需要权限的操作。使得没有权限的操作变成有权限
                    AccessController.doPrivileged(new PrivilegedAction<Void>() {
                        public Void run() {
                            cons.setAccessible(true);
                            return null;
                        }
                    });
                }
                //生成
                return cons.newInstance(new Object[]{h});
            } catch (IllegalAccessException|InstantiationException e) {
                throw new InternalError(e.toString(), e);
            } catch (InvocationTargetException e) {
                Throwable t = e.getCause();
                if (t instanceof RuntimeException) {
                    throw (RuntimeException) t;
                } else {
                    throw new InternalError(t.toString(), t);
                }
            } catch (NoSuchMethodException e) {
                throw new InternalError(e.toString(), e);
            }
        }
    

其中_CallerSensitive_的作用在于

> 这个注解是为了堵住漏洞用的。曾经有黑客通过构造双重反射来提升权限，原理是当时反射只检查固定深度的调用者的类，看它有没有特权，例如固定看两层的调用者（getCallerClass(2)）。如果我的类本来没足够权限群访问某些信息，那我就可以通过双重反射去达到目的：反射相关的类是有很高权限的，而在 我->反射1->反射2 这样的调用链上，反射2检查权限时看到的是反射1的类，这就被欺骗了，导致安全漏洞。使用CallerSensitive后，getCallerClass不再用固定深度去寻找actual caller（“我”），而是把所有跟反射相关的接口方法都标注上CallerSensitive，搜索时凡看到该注解都直接跳过，这样就有效解决了前面举例的问题

参照[这里](http://blog.csdn.net/hel_wor/article/details/50199797),用处不是很大。 这里主要关注 _Class cl = getProxyClass0(loader, intfs);* ``` java /** * Generate a proxy class. Must call the checkProxyAccess method * to perform permission checks before calling this. */ private static Class<?> getProxyClass0(ClassLoader loader, Class<?>... interfaces) { if (interfaces.length > 65535) { throw new IllegalArgumentException("interface limit exceeded"); } // If the proxy class defined by the given loader implementing // the given interfaces exists, this will simply return the cached copy; // otherwise, it will create the proxy class via the ProxyClassFactory return proxyClassCache.get(loader, interfaces); } ``` 可以看到这里校验传入的接口数量不能大于65535，然后直接从缓存中获取代理类,以下是缓存类的实现 ``` java /** * a cache of proxy classes */ private static final WeakCache<ClassLoader, Class<?>\[\], Class<?>> proxyClassCache = new WeakCache<>(new KeyFactory(), new ProxyClassFactory()); ``` 通过static保证在初始化时通过\*ProxyClassFactory\*构造出一个代理类，这是proxy的静态内部类。 ``` java /** * A factory function that generates, defines and returns the proxy class given * the ClassLoader and array of interfaces. */ private static final class ProxyClassFactory implements BiFunction<ClassLoader, Class<?>\[\], Class<?>> { // prefix for all proxy class names //所有代理类的前缀 private static final String proxyClassNamePrefix = "$Proxy"; // next number to use for generation of unique proxy class names //线程安全的计数器 private static final AtomicLong nextUniqueNumber = new AtomicLong(); @Override public Class<?> apply(ClassLoader loader, Class<?>\[\] interfaces) { //这是一个能保证完全相等的map实现。deepEqual Map<Class<?>, Boolean> interfaceSet = new IdentityHashMap<>(interfaces.length); for (Class<?> intf : interfaces) { /* * Verify that the class loader resolves the name of this * interface to the same Class object. */ //使用的类加载器能否构造出相同的接口类 Class<?> interfaceClass = null; try { interfaceClass = Class.forName(intf.getName(), false, loader); } catch (ClassNotFoundException e) { } if (interfaceClass != intf) { throw new IllegalArgumentException( intf + " is not visible from class loader"); } /</em> * Verify that the Class object actually represents an * interface. <em>/ //通过名称和传入的类加载器是否是一个接口 if (!interfaceClass.isInterface()) { throw new IllegalArgumentException( interfaceClass.getName() + " is not an interface"); } /</em> * Verify that this interface is not a duplicate. */ //是否传入了相同的接口 if (interfaceSet.put(interfaceClass, Boolean.TRUE) != null) { throw new IllegalArgumentException( "repeated interface: " + interfaceClass.getName()); } } <pre class="prism-highlight line-numbers" data-start="1"><code class="language-null"> String proxyPkg = null; // package to define proxy class in int accessFlags = Modifier.PUBLIC | Modifier.FINAL; /* * Record the package of a non-public proxy interface so that the * proxy class will be defined in the same package. Verify that * all non-public proxy interfaces are in the same package. */ //所有非public的接口必须在同一个包里面 for (Class<?> intf : interfaces) { int flags = intf.getModifiers(); if (!Modifier.isPublic(flags)) { accessFlags = Modifier.FINAL; String name = intf.getName(); int n = name.lastIndexOf('.'); String pkg = ((n == -1) ? "" : name.substring(0, n + 1)); if (proxyPkg == null) { proxyPkg = pkg; } else if (!pkg.equals(proxyPkg)) { throw new IllegalArgumentException( "non-public interfaces from different packages"); } } } </code></pre> //否则采用默认的包名 if (proxyPkg == null) { // if no non-public proxy interfaces, use com.sun.proxy package proxyPkg = ReflectUtil.PROXY\_PACKAGE + "."; } <pre class="prism-highlight line-numbers" data-start="1"><code class="language-null"> /* * Choose a name for the proxy class to generate. */ //构造代理类的类名 long num = nextUniqueNumber.getAndIncrement(); String proxyName = proxyPkg + proxyClassNamePrefix + num; /* * Generate the specified proxy class. */ //创建代理类 byte\[\] proxyClassFile = ProxyGenerator.generateProxyClass( proxyName, interfaces, accessFlags); try { return defineClass0(loader, proxyName, proxyClassFile, 0, proxyClassFile.length); } catch (ClassFormatError e) { /* * A ClassFormatError here means that (barring bugs in the * proxy class generation code) there was some other * invalid aspect of the arguments supplied to the proxy * class creation (such as virtual machine limitations * exceeded). */ throw new IllegalArgumentException(e.toString()); } } } </code></pre> <pre class="line-numbers prism-highlight" data-start="1"><code class="language-null"><br />通过\*generateProxyClass\*建立类的二进制数据，然后调用本地方法生成代理类。 至于invoke是如何被调用的，那是在生成的$procy0.class中对于方法emark(),会首先调用super.invoke()方法，而这个类继承了代理类JdkProxyHandler,实现了Quest接口。 具体参照\[jdk动态代理中invoke方法调用。\](http://blog.csdn.net/zcc\_0015/article/details/22695647) 生成的$proxy0.class类反编译如下 ``` java package com.sun.proxy; import com.csii.ifp.action.Quest; import java.lang.reflect.InvocationHandler; import java.lang.reflect.Method; import java.lang.reflect.Proxy; import java.lang.reflect.UndeclaredThrowableException; public final class Proxy0 extends Proxy implements Quest { private static Method m1; private static Method m3; private static Method m2; private static Method m0; public Proxy0() throws { super(paramInvocationHandler); } public final boolean equals() { try { return ((Boolean)this.h.invoke(this, m1, new Object\[\] { paramObject })).booleanValue(); } catch (RuntimeException localRuntimeException) { throw localRuntimeException; } catch (Throwable localThrowable) { throw new UndeclaredThrowableException(localThrowable); } } public final void emark() throws { try { this.h.invoke(this, m3, null); return; } catch (RuntimeException localRuntimeException) { throw localRuntimeException; } catch (Throwable localThrowable) { throw new UndeclaredThrowableException(localThrowable); } } public final String toString() { try { return ((String)this.h.invoke(this, m2, null)); } catch (RuntimeException localRuntimeException) { throw localRuntimeException; } catch (Throwable localThrowable) { throw new UndeclaredThrowableException(localThrowable); } } public final int hashCode() { try { return ((Integer)this.h.invoke(this, m0, null)).intValue(); } catch (RuntimeException localRuntimeException) { throw localRuntimeException; } catch (Throwable localThrowable) { throw new UndeclaredThrowableException(localThrowable); } } static { try { m1 = Class.forName("java.lang.Object").getMethod("equals", new Class\[\] { Class.forName("java.lang.Object") }); m3 = Class.forName("com.csii.ifp.action.Quest").getMethod("emark", new Class\[0\]); m2 = Class.forName("java.lang.Object").getMethod("toString", new Class\[0\]); m0 = Class.forName("java.lang.Object").getMethod("hashCode", new Class\[0\]); return; } catch (NoSuchMethodException localNoSuchMethodException) { throw new NoSuchMethodError(localNoSuchMethodException.getMessage()); } catch (ClassNotFoundException localClassNotFoundException) { throw new NoClassDefFoundError(localClassNotFoundException.getMessage()); } } } 至此，jdk动态代理就结束了。_