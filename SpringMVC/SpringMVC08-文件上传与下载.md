# 文件上传



## apache 上传组件方案

1. 添加依赖

```xml
<!--apache文件上传组件-->
<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3</version>
</dependency>
```

2. 在springmvc当中要注册一个文件上传解析器

```xml
<!--文件上传解析器
    id必须是multipartResolver，原因是源代码里面写死了这个名字
-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!--定义最大上传的大小 总的 单位为bytes-->
    <property name="maxUploadSize" value="102400000000"></property>
    <!--定义上传的编码-->
    <property name="defaultEncoding" value="UTF-8"></property>
    <!--定义单个文件的上传大小-->
    <property name="maxUploadSizePerFile" value="2000000"></property>
</bean>
```

3. 准备上传的页面

```jsp
<%--
  Created by IntelliJ IDEA.
  User: jzh
  Date: 2019/4/17
  Time: 15:44
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%--添加一个 enctype="multipart/form-data"--%>
<form action="${ctx}/file/upload" method="post" enctype="multipart/form-data">
    ${ctx}
    文件：<input type="file" name="file"><br>
    <input type="submit" value="提交">
</form>
</body>
</html>
```

4. 后台代码

```java
package person.jzh.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.MultipartFile;

import java.io.File;
import java.io.IOException;
import java.util.Date;

@Controller
@RequestMapping("/file")
public class FileController {

    private static String uploadPath = "E:" + File.separator;

    //    入参就可以代表上传的文件
    @RequestMapping("/upload")
    public String upload(@RequestParam("file") MultipartFile multipartFile, Model model) {
//        1 传到哪里去 2 传什么东西 3 传的细节
//        1 判断
        if (multipartFile != null && !multipartFile.isEmpty()) {
//            不空才传
//            构建新的文件
//           2 获取原文件名字
            String originalFilename = multipartFile.getOriginalFilename();


//            3 先截取原文件的文件名前缀，不带后缀
            String fileNamePrefix = originalFilename.substring(0, originalFilename.lastIndexOf("."));

//            4 加工处理文件名，将原文件名+时间戳
            String newFileNamePrefix = fileNamePrefix + new Date().getTime();

//            5 得到新文件名
            String newFileName = newFileNamePrefix + originalFilename.substring(originalFilename.lastIndexOf("."));

//            6 构建文件对象
            File file = new File(uploadPath + newFileName);

//            7 上传
            try {
                multipartFile.transferTo(file);
                model.addAttribute("filename", newFileName);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return "uploadSuc";
    }
}
```



## 多文件上传

前台

```jsp
<%--
  Created by IntelliJ IDEA.
  User: jzh
  Date: 2019/4/17
  Time: 15:44
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%--多个文件上传--%>
<form action="${ctx}/file/upload2" method="post" enctype="multipart/form-data">
    ${ctx}
    文件：<input type="file" name="file"><br>
    文件：<input type="file" name="file"><br>
    文件：<input type="file" name="file"><br>
    文件：<input type="file" name="file"><br>
    文件：<input type="file" name="file"><br>
    <input type="submit" value="提交">
</form>
</body>
</html>
```

后台

```java
private static String uploadPath = "E:" + File.separator;
//    入参就可以代表上传的文件
@RequestMapping("/upload2")
public String upload2(@RequestParam("file") MultipartFile[] multipartFiles, Model model) {
//        1 传到哪里去 2 传什么东西 3 传的细节
//        1 判断
    List<String> fileNames = new ArrayList<>();
    if (multipartFiles != null && multipartFiles.length > 0){
//            遍历
        for(MultipartFile multipartFile : multipartFiles){
            //            不空才传
//            构建新的文件
//           2 获取原文件名字
            String originalFilename = multipartFile.getOriginalFilename();


//            3 先截取原文件的文件名前缀，不带后缀
            String fileNamePrefix = originalFilename.substring(0, originalFilename.lastIndexOf("."));

//            4 加工处理文件名，将原文件名+时间戳
            String newFileNamePrefix = fileNamePrefix + new Date().getTime();

//            5 得到新文件名
            String newFileName = newFileNamePrefix + originalFilename.substring(originalFilename.lastIndexOf("."));

//            6 构建文件对象
            File file = new File(uploadPath + newFileName);

//            7 上传
            try {
                multipartFile.transferTo(file);
                fileNames.add(newFileName);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    model.addAttribute("filenames",fileNames);

    return "uploadSuc2";
}
```



## servlet3.0新特性

```xml
<bean id="multipartResolver" class="org.springframework.web.multipart.support.StandardServletMultipartResolver">

</bean>
```

servlet

```xml
<servlet>
    <!--这里写的这个名字是有讲究的，
        如果我们不去修改spring配置文件默认的位置，那么
        它会去web-inf下面找一个叫做springmvc-servlet.xml的文件
    -->
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--<servlet-class>person.jzh.servlet.WebPathInitServlet</servlet-class>-->
    <!--<init-param>-->
    <!--&lt;!&ndash;可以重新生成配置文件的名字&ndash;&gt;-->
    <!--<param-name>namespace</param-name>-->
    <!--<param-value>mvc-servlet</param-value>-->
    <!--</init-param>-->
    <init-param>
        <!--上下文配置的位置的制定-->
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <multipart-config>
        <max-file-size>102400000000</max-file-size>
    </multipart-config>
    <!--<load-on-startup>2</load-on-startup>-->
</servlet>
```







# 文件下载

```java
package person.jzh.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.http.HttpServletResponse;
import java.io.File;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;

@Controller
@RequestMapping("/download")
public class DownloadController {

    //    定义一个文件下载的目录
    private static String parentPath = "E:" + File.separator;

    @RequestMapping("/down")
    public String down(HttpServletResponse response) {
        response.setCharacterEncoding("UTF-8");
//        通过输出流写到客户端，浏览器
//        1 获取文件下载名
        String fileName = "111.txt";

//        2 构建一个文件对象通过Paths工具类获取一个Path对象，NIO
        Path path = Paths.get(parentPath, fileName);

//        3 判断它是否存在
        if (Files.exists(path)) {
//            存在则下载
//            通过response设定它的响应类型
//            4 获取文件的后缀
            String fileSuffix = fileName.substring(fileName.lastIndexOf(".") + 1);

//            5 添加头信息
//            response.addHeader("contentType", );

//            6 设置contentType
            response.setContentType("application/" + fileSuffix);

            // ISO8859-1
            try {
                response.addHeader("Content-Disposition", "attachment;filename=" + new String(fileName.getBytes("utf-8"), "ISO8859-1"));
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }

//            7 通过Path写出去
            try {
                Files.copy(path, response.getOutputStream());
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
        return "msg";
    }

}
```