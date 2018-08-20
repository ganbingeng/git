#单例模式
##1. 什么是单例模式?
####单例模式：保证一个类仅有一个实例，并提供一个访问它的全局访问点。

##2. 适用场合
        A) 需要频繁的进行创建和销毁的对象

        B)创建对象时耗时过多或耗费资源过多，但又经常用到的对象

        C)工具类对象

        D)频繁访问数据库或文件的对象
		
##3.常见的几种单例模式
###a)饿汉式(优点：天然线程安全的  缺点：不能做到延迟加载，影响性能)
(```)

	public class Hangury {

 		private final static Hangury HANGURY = new Hangury();

    	private Hangury() {

    	}

   	 	public static Hangury getInstance() {
        	return HANGURY;
    	}
	}

(```)
###b)懒汉式(优点: 可以做到延迟加载   缺点: 线程不安全）
```

    public class TestLazy {
    	private static volatile TestLazy lazy=null;
    	private TestLazy(){

    	}
   	 	public static TestLazy getInstance(){
        	if (lazy == null) {
            	lazy = new TestLazy();
        	}
        	return lazy;
   		}
	}

```
###c)静态内部类(SingltonStaticInner类加载时，Inner类没有被加载，只有在使用getInstance方法时，SingltonStaticInner 类的实例才被创建，可以做到延迟加载)
```

	public class SingltonStaticInner implements Serializable {
   		private static class Inner{
       		private static SingltonStaticInner singltonStaticInner=new SingltonStaticInner();
    	}
    	private  SingltonStaticInner(){

    	}
    	public static SingltonStaticInner getInstance(){
        	return Inner.singltonStaticInner;
    	}

	}

```

###d)枚举(它不仅能避免多线程同步问题，而且还能防止反序列化重新创建新的对象 推荐使用)
```

	public enum Singleton01 {
    	instance;
    	public void doSomething(){

    	}
	}

```

###4.单例模式防序列化
```

	public class Hangury {
	    private final static Hangury HANGURY = new Hangury();
	
	    private Hangury() {
	
	    }
	
	    public static Hangury getInstance() {
	        return HANGURY;
	    }
	    
	      //防序列化
          // 无论是实现Serializable接口，或是Externalizable接口，当从I/O        流中读取对象时，readResolve()方法都会被调用到。
		  // 实际上就是用readResolve()中返回的对象直接替换在反序列化过程中创建的对象。
	    public Object readResolve(){
	        return  HANGURY;
	    }
	}


```
###5.单例模式防反射
```

	public class TestLazy {
	    private static TestLazy lazy=null;
	    private static boolean flag=false;
	    private TestLazy(){
	        if(flag==false) {
	            flag=!flag;
	        }else{
	            throw new RuntimeException("小样 让你反我。。。。");
	        }
	    }
	    public static TestLazy getInstance(){
	        if (lazy == null) {
	            lazy = new TestLazy();
	        }
	        return lazy;
	    }
		//	防序列化
	    public Object readResolve(){
	        return lazy;
	    }
	}

```
###6.单例模式（线程安全）
```

	public class Lazy {
	    //volatile禁止指令重排序
	    private static volatile Lazy lazy=null;
	    private static boolean flag=false;
	    private Lazy(){
	        if(flag==false) {
	            flag=!flag;
	        }else{
	            throw new RuntimeException("小样 让你反我。。。。");
	        }
	    }
	    public static Lazy getInstance(){
	        //double check
	        if(lazy==null) {
				//锁方法粒度太大，锁住其中的new语句就OK
	            synchronized (Lazy.class) {
	                if (lazy == null) {
	                    lazy = new Lazy();
	                }
	            }
	        }
	        return lazy;
	    }
	
	    //防序列化
	    public Object readResolve(){
	        return lazy;
	    }
	}

```