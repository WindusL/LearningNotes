---
title: struts2 xhEditor富文本编辑器-ajax图片上传
date: 2013-10-26
categories: 软件开发
tags:
	- JAVA
	- xhEditor
	- struts2
---

JAVA上传类代码:

<!--more-->

```java
public class UploadAction extends ActionSupport {

        private File filedata;//xheditor提供的上传文件名不可更改

        private String filedataFileName; // 上传文件名 注意filedata

        private String filedataContentType;// 上传文件类型

        /**
         * 上传文件
         */
        public void upload() {
                logger.info("上传文件:" + filedataFileName);
                String filePath = Struts2Util.getRequest().getSession()
                                .getServletContext().getRealPath("/")
                                + filedataFileName;
                File file = new File(filePath);
                if (file.exists()) {
                        file.delete();
                        file = new File(filePath);
                }

                try {
                        FileUtils.copyFile(filedata, file);
                        // copy(uploadFile, file);
                } catch (IOException e) {
                        // TODO Auto-generated catch block
                        e.printStackTrace();
                }
        }

        public File getFiledata() {
                return filedata;
        }

        public void setFiledata(File filedata) {
                this.filedata = filedata;
        }

        public String getFiledataFileName() {
                return filedataFileName;
        }

        public void setFiledataFileName(String filedataFileName) {
                this.filedataFileName = filedataFileName;
        }

        public String getFiledataContentType() {
                return filedataContentType;
        }

        public void setFiledataContentType(String filedataContentType) {
                this.filedataContentType = filedataContentType;
        }

}
```

下载临时文件夹

```xml
<constant name="struts.multipart.saveDir" value="/tmp"/>  
```


下载文件大小

```xml
<constant name="struts.multipart.maxSize" value="10000000"/>
```

js代码:
```javascript
$('#xh').xheditor({upImgUrl: projectName + "/manage/upload!upload",upImgExt:"jpg,jpeg,gif,png"});
```

搞定测试，始终获取不到文件，相信很多人都遇到了遇到过这一步，经过百般尝试后发现js加上  

**html5Upload:false**

继续测试，上传可以了，但是总提示返回错误，而且我想加自定义回调函数 **onUpload:unloadComplete**  发现回调函数不执行。  
查找了很多资料，才发现我的返回值不正确，上传方法返回必须是json格式的数据，格式如下：  
**{“err”:"","msg":"上传成功！"}**  
搞定收工!

