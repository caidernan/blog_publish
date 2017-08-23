---
title: GeoServer从入门到入坑
date: 2017-08-23 22:31:18
tags: geoserver
categories: GIS
typora-root-url: ./
---

工作之后用的比较多的开源GIS是GeoServer，目前使用GeoServer Version2.11.1，现在总结记录下：

第一个细节记录：

**geoserver地图跨域解决方案**

1.geoserver部署运行的方式以tomcat容器为依托，不管是安装方式还是tomcat部署方式，可以在webapps下面找到很多jar包和data数据。

2.下载cors-filter-1.7.jar，[Java](http://lib.csdn.net/base/java)-property-utils-1.9.jar这两个库文件，放到lib目录下下载地址为：

[下载地址](http://download.csdn.NET/detail/longshengguoji/9694377)

3.修改geoserver/WEB-INF/web.xml中配置文件



```xml
<filter>
    <filter-name>CORS</filter-name>
    <filter-class>com.thetransactioncompany.cors.CORSFilter</filter-class>
    <init-param>
        <param-name>cors.allowOrigin</param-name>
        <param-value>*</param-value>
    </init-param>
    <init-param>
        <param-name>cors.supportedMethods</param-name>
        <param-value>GET, POST, HEAD, PUT, DELETE</param-value>
    </init-param>
    <init-param>
        <param-name>cors.supportedHeaders</param-name>
        <param-value>Accept, Origin, X-Requested-With, Content-Type, Last-Modified</param-value>
    </init-param>
    <init-param>
        <param-name>cors.exposedHeaders</param-name>
        <param-value>Set-Cookie</param-value>
    </init-param>
    <init-param>
        <param-name>cors.supportsCredentials</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CORS</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

第二个细节记录：

**geoserver有很多插件**，比如MySQL插件，相关插件[文档](http://docs.geoserver.org/maintain/en/user/extensions/index.html) ，geoserver及插件[下载地址](http://geoserver.org/release/stable/)

mysql插件[下载](https://sourceforge.net/projects/geoserver/files/GeoServer/2.11.2/extensions/geoserver-2.11.2-mysql-plugin.zip/download)

1.下载jar并放到geoserver/WEB-INF/lib

2.新建数据源处，增加了MySQL的添加方式

![添加数据源](http://ov59p85lr.bkt.clouddn.com/%E6%B7%BB%E5%8A%A0%E6%95%B0%E6%8D%AE%E6%BA%90.png)

此处数据有个expose的主键，可以打勾。

![添加数据源2](http://ov59p85lr.bkt.clouddn.com/%E6%B7%BB%E5%8A%A0%E6%95%B0%E6%8D%AE%E6%BA%902.png)

下一步可以在数据库添加数据，或者新建数据（在数据库中会多出两个表）：

![数据库1](http://ov59p85lr.bkt.clouddn.com/%E6%95%B0%E6%8D%AE%E5%BA%931.png)

其中，数据表中的设置了geom字段为地理坐标（如点坐标为point类型），点坐标系为POINT(112.5472 23.163)，此处经度和纬度顺序在openlayer4可以正常显示。



第三个细节记录：

**地图切片发布**

1.geoserver 自带了GeoWebCache，使用之前需要在web.xml中根元素下添加：

```
<context-param>
    <param-name>GEOWEBCACHE_CACHE_DIR</param-name>
    <param-value>D:/data/cache</param-value>
</context-param>
```

注意，Param-value元素就是要存放GeoWebCache瓦片的位置，可自定义。

2.更改完成后，重启Tomcat，之后在D:/data/cache下会生成一些文件。其中包含geowebcache.xml，这个文件是geowebcache的配置的关键所在，在最后会贴上参考配置文件信息。

另外，使用缓存的前提是url不能存在中文，否则无法进入缓存task页面。

Gridsets事先设置缓存规则（一般已设置好），进入Tile Layers->图层->Seed/Truncate 设置切片地图等级，全切还是切丢失及删除切片，最底部的submit之后即可访问。

![发布地图切片](http://ov59p85lr.bkt.clouddn.com/%E5%8F%91%E5%B8%83%E5%9C%B0%E5%9B%BE%E5%88%87%E7%89%87.png)



