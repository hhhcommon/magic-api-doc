# 自定义模块

编写java代码如下：
```java
@Component  //注入到Spring容器中
public class TestFunctions implements MagicModule {

    /**
    * 返回模块名，使用时通过import指令导入之后使用
    */
	@Override
	public String getModuleName() {
		return "test";    // 模块名称
	}
    
    /**
    *   调用打印方法
    */
	public void println(String value) {
		System.out.println(value);
	}
}
```
脚本中使用：
```js
import test;    //导入模块
test.println('Custom Module!'); 
```