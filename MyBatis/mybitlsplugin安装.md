mybitls plugin安装

去互联网上下载

![1552891135754](assets/1552891135754.png)

## 找到从硬盘安装的方式

### ![1552891264585](assets/1552891264585.png)

![1552891321478](assets/1552891321478.png)



![1552891380211](assets/1552891380211.png)

打开help![1552891461433](assets/1552891461433.png)



![1552891711598](assets/1552891711598.png)



请你添加如下代码

``` java
-XX:+DisableAttachMechanism
-javaagent:D:\idea2018\MyBatisplus\ideaagent-1.2.jar
 # 上面的D：是路径，也是我提供的ideafent这个jar的位置，可以直接复制路径
 
```

保存后重启idea，重启之后，打开Mapper接口文件，如果左边有绿色图标，表示成功

![1552891982636](assets/1552891982636.png)



