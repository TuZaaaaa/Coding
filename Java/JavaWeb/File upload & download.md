# 文件上传 与 下载

## 文件上传介绍

1. 要有一个 form 标签，method=post 请求
2. form 标签 encType 属性值必须为 multipart/form-data 值
3. 在 form 标签中使用 input type=file 添加上传的文件
4. 编写服务器代码(Servlet 程序)接受，处理上传的数据

### HTTP 协议的说明

> - Content-Type 表示提交的数据类型
> - encType = multipart/form-data 表示提交的数据
> 以多段（每一个表单项一个数据段）的形式进行拼接，然后以二进制流的形式发送给服务器
> - boundary 表示每段数据的分隔符

### 文件上传常用 API

- commons-fileupload-1.2.1.jar
- commons-io-1.4.jar

### fileupload 类库的使用

> 上传文件的表单：

```html
    <form action="http://localhost:8080/JavaWebDemo/uploadServlet" method="post" enctype="multipart/form-data">
        用户名：<input type="text" name="username"> <br/><br/>
        头像：<input type="file" name="photo"> <br/><br/>
        <input type="submit" value="上传">
    </form>
```

> 解析上传数据的代码：

```java
    
    /**
     * 用来处理上传的数据
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("UTF-8");
        //1.先判断上传的数据是否是多段数据（只有是多段的数据，才是文件上传）
        if (ServletFileUpload.isMultipartContent(req)) {
            //创建 FileItemFactory 工厂实现类
            DiskFileItemFactory fileItemFactory = new DiskFileItemFactory();
            //创建用于解析上传数据的工具类 ServletFileUpload 类
            ServletFileUpload servletFileUpload = new ServletFileUpload(fileItemFactory);
            try {
                //解析上传的数据，得到每一个表单项 FileItem
                List<FileItem> fileItems = servletFileUpload.parseRequest(req);
                for (FileItem fileItem : fileItems) {
                    if (fileItem.isFormField()) {
                        //普通表单项
                        System.out.println("表单项的 name 属性值：" + fileItem.getFieldName());
                        System.out.println("表单项的 value 值：" + fileItem.getString("UTF-8"));
                    } else {
                        //上传的文件
                        System.out.println("表单项的 name 属性值：" + fileItem.getFieldName());
                        System.out.println("表单项的 value 值：" + fileItem.getString("UTF-8"));

                        fileItem.write(new File("d:\\" + fileItem.getName()));
                    }
                }
            } catch (Exception e) {
                e.printStackTrace();
            }

        }
    }
```

## 文件下载

### 常用 API

- response.getOutputStream();
- servletContext.getResourceAsStream();
- servletContext.getMimeType();
- response.setContentType();

> response.setHeader("Content-Disposition", "attachment; fileName=1.jpg");
> 这个响应头告诉浏览器，这是需要下载的，而 attachment 表示附件，也就是下载的一个文件
> fileName=后面，表示下载的文件名
> 完成上面的两个步骤，下载文件是没问题了。但是如果我们要下载的文件是中文名的话。你会发现，下载无法正确显示出正确的中文名
> 原因是在响应头中，不能包含有中文字符，只能包含 ASCII

### 中文名乱码问题

> URLEncoder 解决附件中文名问题

```java
// 把中文名进行 UTF-8 编码操作。
String str = "attachment; fileName=" + URLEncoder.encode("中文.jpg", "UTF-8");
// 然后把编码后的字符串设置到响应头中
response.setHeader("Content-Disposition", str);
```
