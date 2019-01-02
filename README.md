# easy-trunker

## 介绍
一个参考了mybatis声明接口的方式调用easy-brancher( 项目地址：https://gitee.com/xiaoyudeguang/easy-brancher )的分支模块增强工具，你可以像声明mybatis接口一样编写分支业务接口方法，然后在主干业务里调用它。就算不用easy-trunker，你也应该看看它的源码，对于学习mybatis源码有非常大的帮助。

## 使用说明
### 你可以像之前那样声明用easy-brancher来构件分支。
```
@Brancher(key = "one", args = {"name"}, todo = { "" })
public class BranchHandlerOne extends AbstractBrancher{
     @Override
     public Object doService(EasyMap params) throws Exception {
	  System.out.println("111111:"+params);
	  return "11111";
     }
}
```
```
@Brancher(key = "two", args = {"name"}, todo = { "" })
public class BranchHandlerTwo extends AbstractBrancher{
     @Override
     public Object doService(EasyMap params) throws Exception {
	  System.out.println("22222:"+params);
	  return "22222";
     }
}
```
### 主业务调用分支业务
#### 1.spring容器注入的方式
```
@Autowired
private IBrancher brancher;

@Override
public void run(String... args) throws Exception {
    brancher.doService("one", EasyMap.create().add("name", "张三").add("age", "18"));
}
```
#### 2.通过分支工具调用
```
BranchUtils.doService("two", EasyMap.create().add("name", "张三").add("age", "18"));
```
#### 3.easy-trunker方式调用分支业务

```
@Trunker(todo = { "分支声明接口" })
public interface BranchCaller extends IBrancher{

	@BranchKey(keys = {"one","two"}, todo = {"可以在在keys参数中声明调用哪些分支"})
	public Object call(String name, int age);
}
```

```
@EasyBean(todo = { "主干业务调用分支接口demo" })
public class BranchUser implements CommandLineRunner{

	@Autowired
	private BranchCaller caller;

	@Override
	public void run(String... args) throws Exception {
	    caller.call("张三2", 15);
	}
}
```


## Maven引用(如果版本有变化，请自行去maven中央仓库引用)
```
<dependency>
    <groupId>io.github.xiaoyudeguang</groupId>
    <artifactId>easy-brancher</artifactId>
    <version>3.0.2-RELEASE</version>
</dependency>
```
> 在引用了easy-trunker的情况下，就不需要再引入easy-branker了。

如果你是在springboot中使用easy-trunker，easy-trunker默认会扫描启动类包路径下所有子包中声明了@Tranker注解的接口并生成代理，这是对mybatis无脑代理你的接口的一个优化，而且不需要你去配置。

如果你想扫描到不在启动类包路径下的接口，怎么办呢？两种方式：

#### 1.引入@TrunkerScan注解
```
@TrunkerScan({"com.XXXX.XXXX","com.WWW.WWW"})
```
#### 2.在@ComponentScan注解中指定

```
@ComponentScan({"com.XXXX.XXXX","com.WWW.WWW"})
```

