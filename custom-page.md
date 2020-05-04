# 自定义获取分页参数

编写java代码如下：
```java
/**
 * 分页对象默认提取接口
 */
public class MyPageProvider implements PageProvider {

    /**
    *   此方法需要根据实际情况替换
    */
    @Override
    public Page getPage(HttpServletRequest request) {
        long page = 1;
        long pageSize = 100;
        // 计算limit以及offset
        return new Page((page - 1) * pageSize, pageSize);

    }
}
```

随后在代码中添加
```java
@Bean
public PageProvider pageProvider() {
    return new MyPageProvider();
}
```

以上配置会在`ssssssss`中生效，同时自带的获取分页参数会失效