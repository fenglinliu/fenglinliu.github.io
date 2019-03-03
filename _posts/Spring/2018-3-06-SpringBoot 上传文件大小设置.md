---
tags: [Spring Boot]
---
## <center>SpringBoot 上传文件大小设置</center>

2018-10-23 17:40:34.150  WARN 1100 --- [nio-8080-exec-1] .m.m.a.ExceptionHandlerExceptionResolver : Resolved exception caused by handler execution: org.springframework.web.multipart.MaxUploadSizeExceededException: Maximum upload size exceeded; nested exception is java.lang.IllegalStateException: org.apache.tomcat.util.http.fileupload.FileUploadBase$FileSizeLimitExceededException: The field file exceeds its maximum permitted size of 1048576 bytes.

这个意思就是上传的文件超过最大限制1M，需要更改这个限制

原先是这样设置的，结果失效了

```
# 最大支持文件大小

spring.http.multipart.max-file-size=100MB

# 最大支持请求大小

spring.http.multipart.max-request-size=100MB
```

对比官方文档设置没问题啊

```
# MULTIPART (MultipartProperties)

spring.http.multipart.enabled=true # Enable support of multi-part uploads.

spring.http.multipart.file-size-threshold=0 # Threshold after which files will be written to disk. Values can use the suffixed "MB" or "KB" to indicate a Megabyte or Kilobyte size.

spring.http.multipart.location= # Intermediate location of uploaded files.

spring.http.multipart.max-file-size=1MB # Max file size. Values can use the suffixed "MB" or "KB" to indicate a Megabyte or Kilobyte size.

spring.http.multipart.max-request-size=10MB # Max request size. Values can use the suffixed "MB" or "KB" to indicate a Megabyte or Kilobyte size.

spring.http.multipart.resolve-lazily=false # Whether to resolve the multipart request lazily at the time of file or parameter access.
```

后来发现我的项目中springboot由原来的**1.5.3升级到2.0.5了**。。看下了下2.0.5文档

```
# MULTIPART (MultipartProperties)

spring.servlet.multipart.enabled=true # Whether to enable support of multipart uploads.

spring.servlet.multipart.file-size-threshold=0 # Threshold after which files are written to disk. Values can use the suffixes "MB" or "KB" to indicate megabytes or kilobytes, respectively.

spring.servlet.multipart.location= # Intermediate location of uploaded files.

spring.servlet.multipart.max-file-size=1MB # Max file size. Values can use the suffixes "MB" or "KB" to indicate megabytes or kilobytes, respectively.

spring.servlet.multipart.max-request-size=10MB # Max request size. Values can use the suffixes "MB" or "KB" to indicate megabytes or kilobytes, respectively.

spring.servlet.multipart.resolve-lazily=false # Whether to resolve the multipart request lazily at the time of file or parameter access.
```

**将spring.http替换成spring.servlet就可以了**



