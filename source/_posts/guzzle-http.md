---
title: 关于Guzzle http的使用总结
date: 2018-03-26 12:02:20
tags: http
categories: PHP
---

What is the difference between form data and request payload?

if a request (typically POST) has Content-type header set to application/x-www-form-urlencoded the body is expected to be in the form of a standard querystring with url-encoded key=value pairs joined by &. Form data section then shows the key-value parameters (when viewed parsed). This way was much more common in past because it is a default for HTML forms. other cases are shown in Request payload section (and nowadays parsed for readability as well for common formats like JSON).

Form Data 和 Request Payload 区别

如果请求头里设置`Content-Type: application/x-www-form-urlencoded`，那么这个请求被认为是表单请求，参数出现在`Form Data`里，格式为`key=value&key=value&key=value... `原生的`AJAX`请求头里设置`Content-Type:application/json`，或者使用默认的请求头`Content-Type:text/plain`;参数会显示在`Request payload`块里提交。

<!-- more -->
# 流程
## 1. 创建客户端
```
use GuzzleHttp\Client;

$client = new Client([
    // Base URI is used with relative requests
    'base_uri' => 'http://api.com',
    // You can set any number of default request options.
    'timeout'  => 2.0,
]);
```
> 关于base_uri
```
// Create a client with a base URI
$client = new GuzzleHttp\Client(['base_uri' => 'https://foo.com/api/']);
// Send a request to https://foo.com/api/test
$response = $client->request('GET', 'test');
// Send a request to https://foo.com/root
$response = $client->request('GET', '/root');
```

## 2. 发送请求
> GET请求
```
//style1
$response = $client->request('GET', 'http://api.com/get?foo=bar');

//style2
$response = $client->request('GET', 'http://api.com/get', ['query' => 'foo=bar']);

//style3
//提供的数组参数将会使用PHP的 http_build_query
$response = $client->request('GET', 'http://api.com/get', [
    'query' => ['foo' => 'bar']
]);

```

> POST请求
```
//style1  content-type:application/x-www-form-urlencoded
$response = $client->request('POST', 'http://api.com/post', [
    'form_params' => ['foo' => 'bar']
])

//style2  content-type:application/json
$response = $client->request('POST', 'http://api.com/post', [
    'json' => ['foo' => 'bar']
]);
```
> 获取请求返回结果
```
$body = $response->getBody();
$result = json_decode($body, true);
```

## 3.请求选项 request options
上述 query、form_params、json 都属于请求选项，多个请求选项组成的数组作为发起请求的第三个参数，第三个参数是请求格式、内容、超时、header、cookie等的集合
```
//设置请求header
$client->request('GET', '/get', ['headers' => ['X-Foo' => 'test']);
//设置请求超时时间
$client->request('GET', '/delay/5', ['connect_timeout' => 3.14]);
//同时设置请求头和请求体
$client->request('GET', '/get', ['headers' => ['X-Foo' => 'test'], 'json' => ['foo' => 'bar']);
```