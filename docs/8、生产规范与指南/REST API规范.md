> 参考博客: [RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)

## 概述

REST API，简而言之是：

使用请求的 url 来表示 **资源**，使用请求的 METHOD 来表示对资源的 **动作**，即 CRUD 的操作

本项目对参考博客中的规则有所简化，规范如下

## 资源 url 与 动作 的意义


||POST (Create)|GET (Retrieve)|PUT (Update)|DELETE (Delete)|
|-|-|-|-|-|
|/资源名|创建该资源|获取所有的该资源 (返回是列表)|:x:|:x:|
|/资源名/资源id|:x:|获取对应id的资源|更新对应id的资源|删除对应id的资源|

## 返回码

|返回码|描述|
|200|请求成功|
|401|未认证|
|404|找不到资源 或 资源与父级资源不对应|

## 过滤

在仅使用资源id不足以限定获取的资源时，使用 querystring 过滤结果

如：

对所有 `/资源名` 的 GET 动作，都可以接 `?limit=<number>` 以限制返回的资源量

特别地，如对 `/restaurant`，可以使用 `?name=<filter_str>`, `?description=<filter_str>` 对餐馆进行搜索，其中 `filter_str` 进行模糊搜索，实现上 使用 `LIKE %<filter_str>%`

## 返回格式

除某些二进制资源外，返回 json 字符串，格式如下

```json
{
	"msg": "返回的说明",
	"data": {
		"something": "如果需要返回数据，则放在 data 字段中"
	}
}
```

返回码 (Status Code) 已经说明了请求的成功与否，不需要在body中赘述
