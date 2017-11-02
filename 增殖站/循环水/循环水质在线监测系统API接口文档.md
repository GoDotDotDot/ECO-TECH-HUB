# 循环水质在线监测系统API接口文档(草稿)

[TOC]

## 首页



## 监控视频

### 1.GET://monitorVideo/:stationId（获取监控视频页面的URL）

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
  data:{
      playerUrlEnter: ['//vjs.zencdn.net/v/oceans.mp4'], //室内视频URL
      playerUrlOuter: ['//vjs.zencdn.net/v/oceans.mp4']  //室外视屏URL
  } 
}
```

## 设备运行状态

### 1.ws://deviceStatus(获取实时的设备运行的状态数据)

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
  data: {
    "controlType": ['增温泵', '供氧泵', '投饵机', '氨氮仪'], //设备的名称
    "deviceCode": ['SCLSAGRWSEID0001', 'SCLSAGRWSEID0002', 'SCLSAGRWSEID0003', 				'SCLSAGWQSWOD0001'], 									//对应的设备编号
    "mode": ["自动", "手动", "自动", "手动"],			//现在对应的模式
    "status": [true, false, true, false],			  //这种模式下的是否开启的状态
    "tableData": [{									  //如果是手动那么传null，自动就传{json}
        "week": ['周一', '周二', '周三'],
        "openTime": ["12:20", "12:20", "12:20"],
        "closeTime": ['22:20', '22:20', '22:20']
      },
      null,
      {
        "week": ['周一', '周二', '周三'],
        "openTime": ["12:20", "12:20", "12:20"],
        "closeTime": ['22:20', '22:20', '22:20']
      },
      null
    ]
  }
}
```

## 设备运行控制



## 监测数据

### 1.ws://monitorData（获取实时监测数据）

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
      "id":"id值"
    },
    {
      "title": "光照强度",
      "unit": "Lx",
      "maxValue": "59",
      "minValue": "32",
      "id":"id值"


    },
    {
      "title": "温度",
      "unit": "C",
      "maxValue": "25",
      "minValue": "5",
      "id":"id值"
    },
    {
      "title": "氨氮",
      "unit": "mg/L",
      "maxValue": "12",
      "minValue": "2",
      "id":"id值"
    },
    {
      "title": "SS(固体悬浮物)",
      "unit": "mg/L",
      "maxValue": "19",
      "minValue": "9",
      "id":"id值"
    },
    {
      "title": "臭氧",
      "unit": "mg/L",
      "maxValue": "25",
      "minValue": "5",
      "id":"id值"
    }
  ]
}
```

### 2.POST:threshold/:stationId(修改指定传感器的阈值)

1.类型

HTTP

2.方法

POST

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

### 1.GET://montorData/:stationId（查询历史数据生成报表）

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
  data: [{
    type: "氨氮",
    series: [{
        data: ["0.2", "0.15", "0.23", "0.34", "0.21", "0.22"]
      },
      {
        data: ["0.22", "0.17", "0.20", "0.23", "0.24", "0.25"]
      },
      {
        data: ["0.21", "0.19", "0.22", "0.21", "0.21", "0.18"]
      },
      {
        data: ["0.24", "0.21", "0.17", "0.13", "0.12", "0.19"]
      },
      {
        data: ["0.28", "0.18", "0.17", "0.24", "0.26", "0.20"]
      },
    ],
    xAxis: ["08月1日", "08月2日", "08月03日", "08月04日", "08月05日", "08月06日"],
    legend: ["1号驯养池", "2号驯养池", "3号驯养池", "1号孵化池", "2号孵化池"]
  }, {
    type: "温度",
    series: [{
        data: [26, 26.8, 26.9, 26.5, 25.5, 23.4]
      },
      {
        data: [20, 18, 15, 16.8, 19.8, 20.4]
      },
      {
        data: [11.6, 18.9, 17.8, 18.9, 18.7, 25.6]
      },
      {
        data: [18.7, 16.7, 16.8, 15.7, 20.7, 23.9]
      },
      {
        data: [20.4, 16.8, 13.4, 18.7, 12.6, 25.7]
      },
    ],
    xAxis: ["08月1日", "08月2日", "08月03日", "08月04日", "08月05日", "08月06日"],
    legend: ["1号驯养池", "2号驯养池", "3号驯养池", "1号孵化池", "2号孵化池"]
  }]
}
```

## 值班人员设置



## 日志

