# 新闻生成 WebAPI 文档



## 接口说明

新闻生成接口基于智谱自主研发的语言模型，可根据用户输入内容自动创作出一段给定字数的新闻。

该接口为异步接口，开发者首先通过news_generate接口获取task_id，然后调用查看任务状态接口获取异步处理后的结果。



## 接口要求

集成该接口，需按照以下要求。

|  内容   | 说明  |
|  ----  | ----  |
| 传输方式  | http[s] （为提高安全性，强烈推荐https） |
| 请求地址  | https://gpt.aminer.cn/v1/news (注：服务器IP不固定，为保证您的接口稳定，请勿通过指定IP的方式调用接口，使用域名方式调用) |
| 字符编码  | UTF-8 |
| 响应格式  | 统一采用JSON格式 |
| 开发语言  | 任意，只要可以向服务发起HTTP请求的均可 |
| 适用范围  | 任意操作系统，但因不支持跨域不适用于浏览器，请在后端调用接口 |
| 请求行  | POST /v2/its HTTP/1.1 |
| 平均响应时间  | 1s |



## 接口调用流程

 + 将请求参数以及图片数据放在Http Request Body中，以POST表单的形式提交，详见下方 **请求参数** 。
 + 向服务器端发送Http请求后，接收服务器端的返回结果。



## 请求参数

在调用业务接口时，都需要在 Http Request Body 中配置以下参数，请求数据均为json字符串。

参数apikey和参数apisecret经过apiName、appKey和appSecret拼接，再通过DES3+base64加密解密。

|  参数名   | 类型  |  必传   | 描述  |
|  ----  | ----  |  ----  | ----  |
| key | string | 是 | 队列名 |
| apikey    | string | 是   | 在api控制台获取 |
| apisecret | string | 是   | 在api控制台获取 |
| content    | string | 是   | 新闻内容描述    |
| max_length | int    | 是   | 最大字数        |

请求参数样例：
```json
  {
        "key":"queue1", //队列名
        "apikey": "NSY1Nzc1MGUwNGU5YzA0OWEzODYxODYxYTRiNjgyMzEwYg==", 
        "apisecret": "NSY1MDA0ZWJmMjE1NjM0MzBjOWI5MTNhNTFkYzYyZTYzMQ==",
   	 		"content": "神州十三号飞船发射成功", //str
    		"max_length": 100 //int
  }
```



## 返回参数

| 参数名  | 类型   | 描述                                |
| ------- | ------ | ----------------------------------- |
| message | string | 错误描述                            |
| result  | object | 写诗结果信息                        |
| status  | int    | 返回状态码，0表示成功，其他表示异常 |

result字段：

| 参数名   | 类型   | 描述         |
| -------- | ------ | ------------ |
| app      | string | 调用技术名称 |
| input    | object | 输入参数     |
| key      | string | 队列名       |
| progress | object | 模型运行进程 |
| status   | string | 模型运行状态 |
| task_id  | string | 任务id       |

返回参数样例：

```json
{
    "message": "success",
    "result": {
        "app": "news_generate",
        "input": {  
            "content": "神州五号飞船发射成功",
            "max_length": 100
        },
        "key": "queue1",
        "progress": {
            "value": "0%"
        },
        "status": "wait",
        "task_id": "60d1b1a31e5be828b0f11aeb"
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



## 查看任务状态接口



### 接口要求

| 内容         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| 传输方式     | http[s] （为提高安全性，强烈推荐https）                      |
| 请求地址     | https://gpt.aminer.cn/v1/status (注：服务器IP不固定，为保证您的接口稳定，请勿通过指定IP的方式调用接口，使用域名方式调用) |
| 字符编码     | UTF-8                                                        |
| 响应格式     | 统一采用JSON格式                                             |
| 开发语言     | 任意，只要可以向服务发起HTTP请求的均可                       |
| 适用范围     | 任意操作系统，但因不支持跨域不适用于浏览器，请在后端调用接口 |
| 请求行       | GET /v2/its HTTP/1.1                                         |
| 平均响应时间 | 1s                                                           |



### 接口调用流程

 + 向服务器端发送Http请求后，接收服务器端的返回结果。

   

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

| 参数名       | 类型   | 描述                   |
| ------------ | ------ | ---------------------- |
| app          | string | 调用技术名称           |
| input        | object | 输入参数               |
| key          | string | 队列名                 |
| progress     | object | 模型运行进程           |
| status       | string | 模型运行状态           |
| task_id      | string | 任务id                 |
| output.text  | string | 模型输出的具体新闻信息 |
| process_time | float  | 模型出结果所需时间     |
| created_at   | string | 模型出结果的时刻       |
| updated_at   | string | 模型更新结果的时刻     |
| user         | object | 用户信息               |

返回参数样例：

**情况1:模型仍在运行**

```json
{
    "message": "success",
    "result": {
        "app": "news_generate",
        "input": {  
            "content": "神州五号飞船发射成功",
            "max_length": 100
        },
        "key": "queue1",
        "progress": {
            "value": "0%"
        },
        "status": "wait",
        "task_id": "60d1b1a31e5be828b0f11aeb"
    },
    "status": 0
}
```

**情况2:模型结束运行**

```json
{
    "message": "success",
    "result": {
        "app": "news_generate",
        "created_at": "Tue, 22 Jun 2021 10:19:05 GMT",
        "input": {
            "content": "神州五号飞船发射成功",
            "max_length": 100
        },
        "output": {
            "text": "启动长六火箭溅落大洋神州十三号载人飞船发射成功,标志着中国具备了将有效载荷送入近地轨道的能力。受本轮调整影响,此前长征六号火箭发射的13艘飞船,仅有13艘顺利入轨。据不完全统计,神舟飞船将要运输的物资数量,目前约为6吨。"
        },
        "process_time": 2.6870505809783936,
        "task_id": "60d1b914d39ec174d4d46b7e",
        "updated_at": "Tue, 22 Jun 2021 10:19:05 GMT",
        "user": {
            "id": "113",
            "name": "222"
        }
    },
    "status": 0
}
```



