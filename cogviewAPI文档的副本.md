#  CogView WebAPI文档





## 接口说明

CogView接口可根据用户输入内容，自动创作内容相关的一系列图片。

调用请求发送成功后进入等待队列，待模型运行结束即可获取写诗结果，返回结果时间受排队任务量的影响，平均等待时间为3-5min。该接口为异步接口，开发者首先通过CogView接口获取task_id，然后调用查看任务状态接口获取异步处理后的结果。



&nbsp;
< img src="https://lfs.aminer.cn//misc/pretrain/process.jpg" width="30%" height="30%">



## 接口要求

集成该接口，需按照以下要求。

|  内容   | 说明  |
|  ----  | ----  |
| 传输方式  | http[s] （为提高安全性，强烈推荐https） |
| 请求地址  | https://gpt.aminer.cn/v1/cogview (注：服务器IP不固定，为保证您的接口稳定，请勿通过指定IP的方式调用接口，使用域名方式调用) |
| 字符编码  | UTF-8 |
| 响应格式  | 统一采用JSON格式 |
| 开发语言  | 任意，只要可以向服务发起HTTP请求的均可 |
| 适用范围  | 任意操作系统，但因不支持跨域不适用于浏览器，请在后端调用接口 |
| 请求行  | POST /v1/cogview HTTP/1.1 |
| 平均响应时间  | 3-5min |





## 接口调用流程

 + 将请求参数放在Http Request Body中，以POST表单的形式提交，详见下方 **请求参数** 。
 + 向服务器端发送Http请求后，接收服务器端的返回结果。





## 请求参数

在调用业务接口时，都需要在 Http Request Body 中配置以下参数，请求数据均为json字符串。

参数apikey和参数apisecret经过apiName、appKey和appSecret拼接，再通过DES3+base64加密解密。

|  参数名   | 类型  |  必传   | 描述  |
|  ----  | ----  |  ----  | ----  |
| key | string | 是 | 队列名 |
| apikey    | string | 是   | 在api控制台获取 |
| apisecret | string | 是   | 在api控制台获取 |
| query     | string | 是   | 用户输入的内容  |

请求参数样例：
```json
  {
        "key":"queue1", //队列名
        "query": "美国男篮大战中国男篮",  //用户输入的内容
        "apikey": "NSY1Nzc1MGUwNGU5YzA0OWEzODYxODYxYBRiNjgyMzEwYg==", 
        "apisecret": "NSY1MDA0ZWJmMjE1NjM0KzBjOWI5MTNhNTFkYzYyZTYzMQ=="
  }
```





## 返回参数

| 参数名  | 类型   | 描述                                |
| ------- | ------ | ----------------------------------- |
| message | string | 错误描述                            |
| result  | object | 返回信息                            |
| status  | int    | 返回状态码，0表示成功，其他表示异常 |

result字段：

| 参数名     | 类型   | 描述         |
| ---------- | ------ | ------------ |
| app        | string | 调用技术名称 |
| input      | object | 输入参数     |
| created_at | string | 调用时间     |
| updated_at | string | 更新时间     |
| task_id    | string | 任务id       |
| user       | object | 用户信息     |

返回参数样例：

```json
{
    "message": "success",
    "result": {
        "app": "cogview",
        "created_at": "Fri, 16 Jul 2021 09:11:31 GMT",
        "input": {
            "text": "美国男篮大战中国男篮"
        },
        "output": {},
        "process_time": 0,
        "task_id": "a102d896f571e588",
        "updated_at": "Fri, 16 Jul 2021 09:11:31 GMT",
        "user": {
            "appId": "60cb108b9777582444f7b867",
            "id": "111",
            "name": "222"
        }
    },
    "status": 0
}
```





## 查看任务状态接口



### 接口要求

| 内容         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| 传输方式     | http[s] （为提高安全性，强烈推荐https）                      |
| 请求地址     | https://gpt.aminer.cn/v1/status (注：服务器IP不固定，为保证您的接口稳定，请勿通过指定IP的方式调用接口，使用域名方式调用) |
| 请求行       | GET /v1/status HTTP/1.1                                      |
| 平均响应时间 | 1s                                                           |



### 请求参数

| 参数名  | 类型   | 描述                     |
| ------- | ------ | ------------------------ |
| task_id | string | 从上面接口的返回值中获取 |



### 返回参数

| 参数名  | 类型   | 描述                                |
| ------- | ------ | ----------------------------------- |
| message | string | 错误描述                            |
| result  | object | 新闻结果信息                        |
| status  | int    | 返回状态码，0表示成功，其他表示异常 |

result字段：

| 参数名       | 类型   | 描述               |
| ------------ | ------ | ------------------ |
| app          | string | 调用技术名称       |
| input        | object | 输入参数           |
| task_id      | string | 任务id             |
| output       | list   | 模型输出的图片     |
| process_time | float  | 模型出结果所需时间 |
| created_at   | string | 模型出结果的时刻   |
| updated_at   | string | 模型更新结果的时刻 |
| user         | object | 用户信息           |

返回参数样例：

**情况1:模型仍在运行**

```json
{
    "message": "success",
    "result": {
        "app": "cogview",
        "created_at": "Fri, 16 Jul 2021 09:11:31 GMT",
        "input": {
            "text": "美国男篮大战中国男篮"
        },
        "output": {},
        "process_time": 0,
        "task_id": "a102d896f571e588",
        "updated_at": "Fri, 16 Jul 2021 09:11:31 GMT",
        "user": {
            "appId": "60cb108b9777582444f7b864",
            "id": "111",
            "name": "222"
        }
    },
    "status": 0
}
```

**情况2:模型结束运行**

```json
{
    "message": "success",
    "result": {
        "app": "cogview",
        "created_at": "Fri, 16 Jul 2021 08:13:51 GMT",
        "id": "60f13fbfecaa9ff2e9e4ddbc",
        "input": {
            "text": "美国男篮大战中国男篮"
        },
        "output": [
            "https://wudao.aminer.cn/api/cogview//get-image/b612f9bcf678b16c/1626420752215/0.jpg",
            "https://wudao.aminer.cn/api/cogview//get-image/b612f9bcf678b16c/1626420752215/1.jpg",……
        ],
        "process_time": 279.701,
        "updated_at": "Fri, 16 Jul 2021 08:26:23 GMT",
        "user": {
            "appId": "60cb108b9777582444f7b864",
            "id": "111",
            "name": "222"
        }
    },
    "status": 0
}
```





## 错误码

| 错误码 | 错误描述                          |
| ------ | --------------------------------- |
| 10001  | apikey和apisecret与当前接口不匹配 |
| 10002  | apikey校验不成功                  |
| 10003  | 接口速率超限制                    |
| 10004  | 接口周期次数超限制                |





## 代码示例

目前提供[python](https://lfs.aminer.cn//misc/pretrain//cogview/cogview_python.py)、[java](https://lfs.aminer.cn//misc/pretrain//cogview/cogview_java.java)、[c++](https://lfs.aminer.cn//misc/pretrain//cogview/cogview_c++.cpp )的API调用demo。
