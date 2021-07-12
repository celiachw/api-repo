# 藏头诗 WebAPI 文档



## 接口说明

悟能作诗基于智谱自主研发的语言模型，可根据用户输入的主题、诗人、句首，自动创作藏头诗。

该接口为异步接口，开放者首先通过write_poem_heading接口获取task_id，然后调用查看任务状态接口获取异步处理后的结果。



## 接口要求

集成该接口，需按照以下要求。

|  内容   | 说明  |
|  ----  | ----  |
| 传输方式  | http[s] （为提高安全性，强烈推荐https） |
| 请求地址  | https://gpt.aminer.cn/v1/poem (注：服务器IP不固定，为保证您的接口稳定，请勿通过指定IP的方式调用接口，使用域名方式调用) |
| 字符编码  | UTF-8 |
| 响应格式  | 统一采用JSON格式 |
| 开发语言  | 任意，只要可以向服务发起HTTP请求的均可 |
| 适用范围  | 任意操作系统，但因不支持跨域不适用于浏览器，请在后端调用接口 |
| 请求行  | POST /v2/its HTTP/1.1 |
| 平均响应时间  | 6min |



## 接口调用流程

 + 将请求参数放在Http Request Body中，以POST表单的形式提交，详见下方 **请求参数** 。
 + 向服务器端发送Http请求后，接收服务器端的返回结果。



## 请求参数

在调用业务接口时，都需要在 Http Request Body 中配置以下参数，请求数据均为json字符串。

参数apikey和参数apisecret经过apiName、appKey和appSecret拼接，再通过DES3+base64加密解密。

|  参数名   | 类型  |  必传   | 描述  |
|  ----  | ----  |  ----  | ----  |
| key | string | 是 | 队列名 |
| apikey    | string | 是   | 在api控制台获取                  |
| apisecret | string | 是   | 在api控制台获取                  |
| topic     | string | 是   | 标题                             |
| author    | string | 是   | 作者                             |
| speed     | string | 是   | 写诗模式，在该接口中必须为normal |
| heading   | string | 是   | 句首                             |

请求参数样例：
```json
  {
        "key":"queue1", //队列名
    		"apikey": "NSY1Nzc1MGUwNGU5YzA0OWEzODYxODYxYTRiNjgyMzEwYg==", 
        "apisecret": "NSY1MDA0ZWJmMjE1NjM0MzBjOWI5MTNhNTFkYzYyZTYzMQ==",
        "topic": "打工人",  //标题
        "author":"李白", //作者
        "speed": "normal",  //normal表示普通写诗，fast表示快速写诗
    		"heading": "清华大学"
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
        "app": "poem",
        "input": {
            "author": "李白",
            "cipai": "",
            "desc": "",
            "heading": "清华大学",
            "speed": "normal",
            "topic": "打工人"
        },
        "key": "queue1",
        "progress": {
            "value": "0%"
        },
        "status": "wait",
        "task_id": "60d1b97ad39ec174d4d46b80"
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
| result  | object | 写诗结果信息                        |
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
| output.text  | string | 模型输出的具体诗歌信息 |
| process_time | float  | 模型出结果所需时间     |
| created_at   | string | 模型出结果的时刻       |
| updated_at   | string | 模型更新结果的时刻     |
| user         | object | 用户信息               |

返回参数样例：

由于创作藏头诗需要5-10分钟，所以返回参数有两种情况

**情况1:模型仍在运行**

```json
{
    "message": "success",
    "result": {
        "app": "poem",
        "input": {  
            "author": "李白",
            "cipai": "",
            "desc": "",
            "heading": "清华大学",
            "speed": "normal",
            "topic": "打工人"
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
        "app": "poem",
        "created_at": "Tue, 22 Jun 2021 10:19:05 GMT",
        "input": {
            "author": "李白",
            "cipai": "",
            "desc": "",
            "heading": "清华大学",
            "speed": "normal",
            "topic": "打工人"
        },
        "output": {
            "text": "清铁百馀斤,华州司马鼻。大缸两尺深,学取他身苦。夜刺武牢关,行行无白骨。长安市上儿,莫说泪沾土。"
        },
        "process_time": 584.7333824634552,
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

