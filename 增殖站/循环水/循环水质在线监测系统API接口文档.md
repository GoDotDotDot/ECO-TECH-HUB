# 循环水质在线监测系统API接口文档

UPDATE DATE:  `2017/10/25 14:59`

[TOC]

## 首页



## 监控视频

### 1.GET:monitorVideo/:stationId（获取监控视频页面的URL）

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称        | 参数类型                      | 备注    |
| ----------- | ------------------------- | ----- |
| stationName | String                    | 站点名称  |
| currentTime | date(yyyy-MM-dd hh:mm:ss) | 当前的时间 |

4.Response

```json
{
  "data":{
      "playerUrlEnter": ["//vjs.zencdn.net/v/oceans.mp4"], //室内视频URL
      "playerUrlOuter": ["//vjs.zencdn.net/v/oceans.mp4"]  //室外视屏URL
  } 
}
```

## 设备运行状态

### 1.ws:equipmentStatusWebSocket(获取实时的设备运行的状态数据)

1.类型

Websocket

2.Request

| 参数名称        | 参数类型                      | 备注    |
| ----------- | ------------------------- | ----- |
| stationId   |                           | 站点ID  |
| stationName | String                    | 站点名称  |
| currentTime | date(yyyy-MM-dd hh:mm:ss) | 当前的时间 |

3.Response

```json
{
    "controlType": ["增温泵", "供氧泵", "投饵机", "氨氮仪"], //设备的名称
    "deviceCode": ["SCLSAGRWSEID0001", "SCLSAGRWSEID0002", "SCLSAGRWSEID0003", 				"SCLSAGWQSWOD0001"], 									//对应的设备编号
    "mode": ["自动", "手动", "自动", "手动"],			//现在对应的模式
    "status": [true, false, true, false],			  //这种模式下的是否开启的状态
    "tableData": [{									  //如果是手动那么传null，自动就传{json}
        "week": ["周一", "周二", "周三"],
        "openTime": ["12:20", "12:20", "12:20"],
        "closeTime": ["22:20", "22:20", "22:20"]
      },
      null,
      {
        "week": ["周一", "周二", "周三"],
        "openTime": ["12:20", "12:20", "12:20"],
        "closeTime": ["22:20", "22:20", "22:20"]
      },
      null
    ]
  }
```

## 设备运行控制(待商议)

### 1.PUT:switchControlMode/:stationId(切换控制模式)

1.类型

HTTP

2.方法

PUT

3.Request

| 参数名称 | 参数类型     | 说明                          |
| ---- | -------- | --------------------------- |
| mode | `string` | 要切换到的模式，可选值有`auto`、`manual` |
| id   | `string` | 设备ID                        |

4.Response

- `mode`为`auto`时

```json
{
  "mode": "auto",
  "key": "设备ID",
  "data": [
    {
      "date": "周一",
      "startDate": "22:00:00",
      "endDate": "23:00:00",
      "key": "id" // key为id值
    },
    {
      "date": "每天",
      "startDate": "22:00:00",
      "endDate": "23:00:00",
      "key": "id" // key为id值
    }
  ]
}
```

- `mode`为`manual`时

```json
{
  "mode": "manual",
  "key": "设备ID",
  "data": true // true 表示开,false表示关
}
```

### 2.GET:deviceControl/:stationId(获取设备运行控制所有数据)

1.类型

HTTP

2.方法

GET

3.Requset

| 参数名称        | 参数类型                      | 备注   |
| ----------- | ------------------------- | ---- |
| station     |                           | 站点ID |
| currentTime | date(yyyy-MM-dd hh:mm:ss) | 当前时间 |
| stationName | string                    | 站点名称 |

4.Response

```json
{
  "data": [
    {
      "type": "设备间",
      "pools": [
        {
          "name": "1号池",
          "mode": "auto",
          "key":"设备id",
          "data": [
            {
              "date": "周一",
              "startDate": "22:00:00",
              "endDate": "23:00:00",
              "key": "id" // key为id值
            },
            {
              "date": "周一",
              "startDate": "22:00:00",
              "endDate": "23:00:00",
              "key": "id" // key为id值
            }
          ]
        },
        {
          "name": "2号池",
          "mode": "manual",
          "data": true,
          "key":"设备id",
        }
      ]
    },
    {
      "type": "驯养池",
      "pools": [
        {
          "name": "1号池",
          "mode": "auto"
          "key":"设备id",
          "data": [
            {
              "date": "周一",
              "startDate": "22:00:00",
              "endDate": "23:00:00",
              "key": "id" // key为id值
            },
            {
              "date": "周一",
              "startDate": "22:00:00",
              "endDate": "23:00:00",
              "key": "id" // key为id值
            }
          ]
        }
      ]
    }
  ]
}
```

### 3.PUT:deviceControl/:staionId(修改指定设备控制数据)

1.类型

HTTP

2.方法

PUT

3.Request

| 参数名称 | 参数类型     | 说明                         |
| ---- | -------- | -------------------------- |
| id   | `string` | 设备ID                       |
| mode | `string` | 当前控制模式，可选项为`auto`、`manual` |
| data | `Object`  |                            |

当`data`为`Object`时，数据格式如下：

```json
{
	"calendarId":"calendarId",
    "mondayStartTime": "22:00:00",//周一开始时间
    "mondayEndTime": "23:00:00",//周一结束时间
	"tuesdayStartTime": "22:00:00",//周二开始时间
	"tuesdayEndTime":"23:00:00",//周二结束时间
	"everydayStartTime":"22:00:00",//每天开始时间(每天和周几只能选择一个,例如:选择了周一就不能选择每天,反之亦然)
	"everydayEndTime":"23:00:00",//每天结束时间
}
```
当`data`为`Boolean`时，控制模式为`manual`，数据格式如下：
```json
{
	"status":"true或者false"
}
```
4.Response

```json
{
  "message":"成功！" // 消息,
  "status":true
}
```

## 监测数据

### 1.ws:sensorsDataWebSocket（获取实时监测数据）

1.类型

WebSocket

2.Request

| 参数名称     | 参数类型     | 说明   |
| -------- | -------- | ---- |
| staionId | `string` | 站点ID |
|          |          |      |
|          |          |      |

3.Response

```json
{
  "data": [
    {
      "type": "设备间",
      "pools": [
        {
          "name": "1号池",
          "data": [
            {
              "num": 14,
              "unit": "mg/L",
              "name": "PH(酸碱度)",
              "status":"normal"
            },
            {
              "num": 14,
              "unit": "mg/L",
              "name": "氨氮",
              "status":"danger"
            }
          ]
        }
      ]
    },
    {
        "type": "驯养池",
        "pools": [
          {
            "name": "1号池",
            "data": [
              {
                "num": 14,
                "unit": "mg/L",
                "name": "PH(酸碱度)",
                "status":"danger"
              },
              {
                "num": 14,
                "unit": "mg/L",
                "name": "PH(酸碱度)",
                "status":"normal"
              }
            ]
          }
        ]
      }
  ]
}
```

## 阈值设置

### 1.GET:threshold/:stationId(获取阈值设置页面初始数据)

1.类型

HTTP

2.方法

GET

3.Request

4.Response

```json
{
  "data": [
    {
      "title": "PH",
      "unit": "mg/L",
      "maxValue": "20",
      "minValue": "18",
      "key":"id值"
    },
    {
      "title": "光照强度",
      "unit": "Lx",
      "maxValue": "59",
      "minValue": "32",
      "keykey":"id值"
    },
    {
      "title": "温度",
      "unit": "C",
      "maxValue": "25",
      "minValue": "5",
      "key":"id值"
    },
    {
      "title": "氨氮",
      "unit": "mg/L",
      "maxValue": "12",
      "minValue": "2",
      "key":"id值"
    },
    {
      "title": "SS(固体悬浮物)",
      "unit": "mg/L",
      "maxValue": "19",
      "minValue": "9",
      "key":"id值"
    },
    {
      "title": "臭氧",
      "unit": "mg/L",
      "maxValue": "25",
      "minValue": "5",
      "key":"id值"
    }
  ]
}
```

### 2.PUT:threshold/:stationId(修改指定传感器的阈值)

1.类型

HTTP

2.方法

PUT

3.Request

| 参数名称     | 参数类型  | 备注   |
| -------- | ----- | ---- |
| id       |       | id值  |
| maxValue | Float | 最大值  |
| minValue | Float | 最小值  |

4.Response

```json
{
  "statusCode":200,// 500 304 404...
  "message":"成功！" // 消息,
  "status":true
}
```



## 监测数据查询

### 1.GET:montorData/:stationId（查询历史数据生成报表）

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称        | 参数类型                      | 备注   |
| :---------- | ------------------------- | ---- |
| stationName | String                    | 站点名称 |
| startTime   | date(yyyy-MM-dd hh:mm:ss) | 开始时间 |
| endTime     | date(yyyy-MM-dd hh:mm:ss) | 结束时间 |

4.Response

```json
{
  "data": [{
    "type": "氨氮",
    "series": [{
        "data": ["0.2", "0.15", "0.23", "0.34", "0.21", "0.22"]
      },
      {
        "data": ["0.22", "0.17", "0.20", "0.23", "0.24", "0.25"]
      },
      {
        "data": ["0.21", "0.19", "0.22", "0.21", "0.21", "0.18"]
      },
      {
        "data": ["0.24", "0.21", "0.17", "0.13", "0.12", "0.19"]
      },
      {
        "data": ["0.28", "0.18", "0.17", "0.24", "0.26", "0.20"]
      },
    ],
    "xAxis": ["08月1日", "08月2日", "08月03日", "08月04日", "08月05日", "08月06日"],
    "legend": ["1号驯养池", "2号驯养池", "3号驯养池", "1号孵化池", "2号孵化池"]
  }, {
    "type": "温度",
    "series": [{
        "data": [26, 26.8, 26.9, 26.5, 25.5, 23.4]
      },
      {
        "data": [20, 18, 15, 16.8, 19.8, 20.4]
      },
      {
        "data": [11.6, 18.9, 17.8, 18.9, 18.7, 25.6]
      },
      {
        "data": [18.7, 16.7, 16.8, 15.7, 20.7, 23.9]
      },
      {
        "data": [20.4, 16.8, 13.4, 18.7, 12.6, 25.7]
      },
    ],
    "xAxis": ["08月1日", "08月2日", "08月03日", "08月04日", "08月05日", "08月06日"],
    "legend": ["1号驯养池", "2号驯养池", "3号驯养池", "1号孵化池", "2号孵化池"]
  }]
}
```

## 值班人员设置

### 1.GET:operatorSetting/:stationId（值班人员设置获取数据）

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称      | 参数类型 | 备注   |
| --------- | ---- | ---- |
| stationId |      | 站点ID |

4.Response

```json
"data": [{
    "key": "0",
    "name": {
      "value": "谢海龙"
    },
    "age": {
      "value": "22"
    },
    "telephone": {
      "value": "18781785233"
    },
    "address": {
      "value": "四川省成都市锦江区金红苑",
    },
    "nearTime": {
      "value": "周四"
    }
  }, {
    "key": "1",
    "name": {
      "value": "曾世平"
    },
    "age": {
      "value": "24"
    },
    "telephone": {
      "value": "9090989"
    },
    "address": {
      "value": "四川省成都市锦江区",
    },
    "nearTime": {
      "value": "周一"
    }
  }, {
    "key": "2",
    "name": {
      "value": "梁熊"
    },
    "age": {
      "value": "24"
    },
    "telephone": {
      "value": "123456789"
    },
    "address": {
      "value": "四川省成都市",
    },
    "nearTime": {
      "value": "周二"
    }
  }, {
    "key": "3",
    "name": {
      "value": "储奎"
    },
    "age": {
      "value": "22"
    },
    "telephone": {
      "value": "6523652"
    },
    "address": {
      "value": "四川省成都市",
    },
    "nearTime": {
      "value": "周三"
    }
  }]
```

### 2.DELET:operatorSetting/:stationId（值班人员设置删除数据）

1.类型

HTTP

2.方法

DELETE

3.Request

| 参数名称       | 参数类型   | 备注    |
| ---------- | ------ | ----- |
| stationId  |        | 站点ID  |
| userName   | String | 谁操作的  |
| data       | obj    | 删除的数据 |
| employeeId |        | 员工ID  |

4.Response

```json
{
  "statusCode":200,// 500 304 404...
  "message":"成功！" // 消息,
  "status":true
}
```

### 3.POST:operatorSetting/:stationId（值班人员设置添加数据）

1.类型

HTTP

2.方法

POST

3.Request

| 参数名称      | 参数类型   | 备注      |
| --------- | ------ | ------- |
| stationId |        | 站点ID    |
| data      | obj    | 添加的相关数据 |
| userName  | String | 谁操作的    |

4.Rsponse

```json
{
  "statusCode":200,// 500 304 404...
  "message":"成功！" // 消息,
  "status":true
}
```

### 4.PUT:operatorSetting/:stationId（值班人员设置修改数据）

1.类型

HTTP

2.方法

PUT

3.Rquest

| 参数名称       | 参数类型   | 备注    |
| ---------- | ------ | ----- |
| stationId  |        | 站点ID  |
| data       | obj    | 修改的数据 |
| userName   | String |       |
| employeeId |        | 员工ID  |

4.Response

```json
{
  "statusCode":200,// 500 304 404...
  "message":"成功！" // 消息,
  "status":true
}
```

##日志

### 1.GET:warnLogs/all/:stationId(获取指定时间内所有报警日志)

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称      | 参数类型                  | 说明    |
| --------- | --------------------- | ----- |
| startDate | `yyyy-MM-dd HH:mm:ss` | 开始时间  |
| endDate   | `yyyy-MM-dd HH:mm:ss` | 结束时间  |
| page      | `number`              | 请求的页码 |

4.Response

```json
{
  "totalPages":10,//总页码数
  "data": [
    {
      "message": "预警信息1",
      "date": "2017-10-23 14:00:00",
      "readStatus": true,
      "operator": "管理员1",
      "key": 1 //id值
    },
    {
      "message": "预警信息2",
      "date": "2017-10-23 14:00:00",
      "readStatus": false,
      "operator": "管理员2",
      "key": 2
    },
    {
      "message": "预警信息3",
      "date": "2017-10-23 14:00:00",
      "readStatus": true,
      "operator": "管理员1",
      "key": 3
    },
    {
      "message": "预警信息4",
      "date": "2017-10-23 14:00:00",
      "readStatus": false,
      "operator": "管理员2",
      "key": 4
    }
  ]
}
```

### 2.GET:warnLogs/readed/:stationId(获取指定时间内已读报警日志)

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称      | 参数类型                  | 说明    |
| --------- | --------------------- | ----- |
| startDate | `yyyy-MM-dd HH:mm:ss` | 开始时间  |
| endDate   | `yyyy-MM-dd HH:mm:ss` | 结束时间  |
| page      | `number`              | 请求的页码 |

4.Response

```json
{
  "totalPages":10,//总页码数
  "data": [
    {
      "message": "预警信息2",
      "date": "2017-10-23 14:00:00",
      "readStatus": true,
      "operator": "管理员2",
      "key": 1 // id值
    },
    {
      "message": "预警信息1",
      "date": "2017-10-23 14:00:00",
      "readStatus": true,
      "operator": "管理员1",
      "key": 2
    },
    {
      "message": "预警信息2",
      "date": "2017-10-23 14:00:00",
      "readStatus": true,
      "operator": "管理员2",
      "key": 3
    },
    {
      "message": "预警信息1",
      "date": "2017-10-23 14:00:00",
      "readStatus": true,
      "operator": "管理员1",
      "key": 4
    }
  ]
}
```

### 3.GET:warnLogs/unread/:stationId(获取指定时间内未读报警日志)

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称      | 参数类型                  | 说明    |
| --------- | --------------------- | ----- |
| startDate | `yyyy-MM-dd HH:mm:ss` | 开始时间  |
| endDate   | `yyyy-MM-dd HH:mm:ss` | 结束时间  |
| page      | `number`              | 请求的页码 |

4.Response

```json
{
  "totalPages":10,//总页码数
  "data": [
    {
      "message": "预警信息2",
      "date": "2017-10-23 14:00:00",
      "readStatus": false,
      "operator": "管理员2",
      "key": 1 // id值
    },
    {
      "message": "预警信息1",
      "date": "2017-10-23 14:00:00",
      "readStatus": false,
      "operator": "管理员1",
      "key": 2
    },
    {
      "message": "预警信息2",
      "date": "2017-10-23 14:00:00",
      "readStatus": false,
      "operator": "管理员2",
      "key": 3
    },
    {
      "message": "预警信息1",
      "date": "2017-10-23 14:00:00",
      "readStatus": false,
      "operator": "管理员1",
      "key": 4
    }
  ]
}
```

### 4.GET:warnLogs/:stationId/(获取指定时间内操作日志)

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称 | 参数类型    | 说明        |
| ---- | ------- | --------- |
| id   | `Array` | 需要标记已读的ID |

4.Response

```json
{
  "status":true,//true or false
  "message":"操作成功！" //提示消息
}
```



### 5.GET:operationLogs/:stationId(获取指定时间内操作日志)

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称      | 参数类型                  | 说明    |
| --------- | --------------------- | ----- |
| startDate | `yyyy-MM-dd HH:mm:ss` | 开始时间  |
| endDate   | `yyyy-MM-dd HH:mm:ss` | 结束时间  |
| page      | `number`              | 请求的页码 |

4.Response

```json
{
  "totalPages":10,//总页码数
  "data": [
    {
      "action": "操作项。。。开启什么。。。",
      "date": "2017-10-23 14:00:00",
      "phone": "123456",
      "operator": "管理员2",
      "key": 1 // id值
    },
    {
      "action": "操作项。。。开启什么。。。",
      "date": "2017-10-23 14:00:00",
      "phone": "123456",
      "operator": "管理员2",
      "key": 1
    },
    {
      "action": "操作项。。。开启什么。。。",
      "date": "2017-10-23 14:00:00",
      "phone": "123456",
      "operator": "管理员2",
      "key": 1
    },
    {
      "action": "操作项。。。开启什么。。。",
      "date": "2017-10-23 14:00:00",
      "phone": "123456",
      "operator": "管理员2",
      "key": 1
    },
  ]
}
```

