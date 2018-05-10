




# 循环水质在线监测系统API接口文档

LAST UPDATE:  `2018/03/23 12:59`

VERSION:0.2.1

[TOC]

## 首页

### 1.GET:/index/getAllMessage(首页下获取传感器、报警情况、现有设备，当前值班人员相关信息)

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称        | 参数类型                    | 备注     |
| ----------- | ----------------------------- | ---------|
| stationdId  | `int`                         | 站点ID   |
| startTime   | `String(yyyy-MM-dd hh:mm:ss)` | 开始时间  |
| endTime     | `String(yyyy-MM-dd hh:mm:ss)` | 结束时间  |

4.Response

```json
{
   "device":{
     "total":"20", // 设备的个数
     "working":'15',// 正在运行设备的个数
   },
  "sensor":{
      "total":'10', // 传感器的个数
      "typeCount":'3' //传感器的类别数
  },
  "warn":{
      "total":"20" // 警报的总数
      "readed":"15" //已经阅读的
  },
  "person":{
      "name":'张飞',
      "tel":'123135468',
  }
}
```

### 2.GET:index/getIndexTemperatureChartData (首页下获取24小时的温度数据)

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称        | 参数类型                      | 备注              |
| ----------- | ------------------------- | --------------- |
| city        | string                    | 当前站点的地点名称（市级单位） |
| currentTime | date（yyyy-MM-dd hh:mm:ss） | 当前时间            |

4.Response

```json
{
  "chartData": {
    "xAxis": ["11:00", "12:00", "13:00", "14:00", "15:00", "16:00"],  //时间
    "series": ["19.8", "23.8", "15.8", "25.8", "26.8", "24.8"] //对应的温度值
  }
}
```

### 3.GET:index/getWeatherData (获取首页下最近5天的气象数据)

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称 | 参数类型   | 备注             |
| ---- | ------ | -------------- |
| city | string | 当前站点地点名称(市级单位) |

4.Response

```json
{
    //天气接口什么数据就返回什么数据
}
```



## 监控视频

### 1.GET:/monitorVideo（获取监控视频页面的URL）

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称        | 参数类型                      | 备注    |
| ----------- | ------------------------- | ------------- |
| stationId   | `int`                     | 站点id        |
| equipTypeId | `int`                     | 设备类型id     |

4.Response

```json
{
  "data":{
      "playerUrl": ["//vjs.zencdn.net/v/oceans.mp4"], //视频URL(不区分室内外)
  } 
}
```

## 设备运行状态

### 1.GET:equipmentStatus(获取实时的设备运行的状态数据)

1.类型

GET

2.Request

| 参数名称        | 参数类型                      | 备注    |
| ----------- | --------------------------- | ----- |
| stationId   | `int`                       | 站点ID  |
| stationName | `String`                    | 站点名称  |
| currentTime | `date(yyyy-MM-dd hh:mm:ss`) | 当前的时间 |

3.Response

```json
{
    "controlType": ["增温泵", "供氧泵", "投饵机", "氨氮仪"], //设备的名称
    "deviceCode": ["001-坝下", "002-弯道", "003-坝上"],	//对应的设备编号
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

### 1.PUT:/switchControlMode(切换控制模式)

1.类型

HTTP

2.方法

PUT

3.Request

| 参数名称 | 参数类型     | 说明                          |
| ---- | -------- | --------------------------- |
| mode | `string` | 要切换到的模式，可选值有`auto`、`manual` |
| equipId   | `int` | 设备id                        |

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

### 2.GET:/deviceControl(获取设备运行控制所有数据)

1.类型

HTTP

2.方法

GET

3.Requset

| 参数名称        | 参数类型                      | 备注   |
| -------------- | ---------------------------- | ---- |
| stationId      |  `int`                       | 站点id |

4.Response

```json
{
  "data": 
    {
      "type": "设备间",
      "pools": [
        {
          "name":"酸碱度",
		  "number": "1号池",
          "mode": "auto",
          "key":"设备id",
          "data": [
            {
              "date": "周一",
              "startDate": "22:00:00",
              "endDate": "23:00:00",
              "key": "id" // key为id值,该id目前相同
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
        },
        {
          "name": "3号池",
          "mode": "smart",
          "data": "设备智能运行控制中!",
          "key":"设备id",
        }
      ]
   }
}
```

### 3.PUT:/deviceControl(修改指定设备控制数据)

1.类型

HTTP

2.方法

PUT

3.Request

| 参数名称 | 参数类型     | 说明                         |
| ---- | -------- | -------------------------- |
| id   | `int`    | 设备ID                      |
| mode | `string` | 当前控制模式，可选项为`auto`、`manual`、`smart` |
| data | `object` |                            |

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

### 1.get:/sensorsData（获取实时监测数据）

1.类型

HTTP

2.Request

| 参数名称     | 参数类型     | 说明   |
| -------- | -------- | ---- |
| stationId | `int` | 站点ID |


3.Response

```json
{
    "data": [
        {
            "num": 14,
            "unit": "mg/L",
            "name": "PH(酸碱度)",
            "maximum": 20,
            "minimum": 3
        },
        {
            "num": 14,
            "unit": "mg/L",
            "name": "氨氮",
            "maximum": 20,
            "minimum": 3
        }
    ]
}
```

## 阈值设置

### 1.GET:/threshold(获取阈值设置页面初始数据)

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称     | 参数类型     | 说明   |
| -------- | -------- | ---- |
| stationId | `int` | 站点id |

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

### 2.PUT:threshold(修改指定传感器的阈值)

1.类型

HTTP

2.方法

PUT

3.Request

| 参数名称     | 参数类型  | 备注   |
| -------- | ------- | ---- |
| sensorsId| `int`   | id值  |
| maxValue | `float` | 最大值  |
| minValue | `float` | 最小值  |

4.Response

```json
{
  "message":"成功！" // 消息,
  "status":true
}
```



## 监测数据查询(待商议!)

### 1.GET:/monitorData（查询历史数据生成报表）

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称        | 参数类型                      | 备注   |
| :---------- | ------------------------- | ---- |
| stationId   | `int`                     | 站点id |
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

### 1.GET:/selectEmployeeById（根据id获取值班人员信息）

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称      | 参数类型 | 备注   |
| --------- | ---- | ---- |
| employeeId |   `int`   | 员工id |

4.Response

```json
"data": {
        "address": "四川南充",
        "birthday": "1993-11-15 00:00:00",
        "dept": {
            "deptId": 1,
            "deptName": "技术部",
            "manager": {
                "employeeId": 3,
                "employeeName": "岑参"
            }
        },
        "employeeId": 1,
        "employeeName": "梁雄",
        "identityNumber": "511321199311158236",
        "lastUpdateTime": "2017-11-14 14:36:15",
        "sex": "男",
        "telephone": "15281699634"
    }

```

### 2.GET:/selectEmployeeByStationId（根据站点id查询员工(默认条件是没有值班)）

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称      | 参数类型 | 备注   |
| --------- | ---- | ---- |
| stationId |   `int`   | 站点id |

4.Response

```json
"data": [
        {
            "address": "四川德阳",
            "name": "洛宾王",
            "telephone": "18781785233",
            "key": 5,
            "age": 925
        }
    ]
```

### 1.GET:/operatorSetting（值班人员设置获取数据）

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称      | 参数类型 | 备注   |
| --------- | ---- | ---- |
| stationId |   `int`   | 站点id |

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
      "value": "梁雄"
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

### 2.DELET:/operatorSetting（值班人员设置删除数据）

1.类型

HTTP

2.方法

DELETE

3.Request

| 参数名称       | 参数类型   | 备注    |
| ---------- | ------ | ------ |
| employeeId | `int`  | 员工id  |
| scheduleId | `int`  | 任务id  |

4.Response

```json
{
  "statusCode":200,// 500 304 404...
  "message":"成功！" // 消息,
  "status":true
}
```

### 3.POST:/operatorSetting（值班人员设置添加数据）

1.类型

HTTP

2.方法

POST

3.Request

| 参数名称      | 参数类型   | 备注      |
| --------- | ------ | ------- |
| employeeId | `int`     | 员工id |
| monday     | `boolean` | 周一   |
| tuesday    | `boolean` | 周二   |

4.Rsponse

```json
{
  "message":"成功！" // 消息,
  "status":true
}
```

### 4.PUT:/operatorSetting（值班人员设置修改数据）

1.类型

HTTP

2.方法

PUT

3.Rquest

| 参数名称       | 参数类型   | 备注    |
| ---------- | --------- | ------ |
| scheduleId | `int`     | 站点id |
| employeeId | `int`     | 员工id |
| monday     | `boolean` | 周一   |
| tuesday    | `boolean` | 周二   |
| wednesday  | `boolean` | 周三   |
| thursday   | `boolean` | 周四   |
| friday     | `boolean` | 周五   |
| saturday   | `boolean` | 周六   |
| sunday     | `boolean` | 周日   |

4.Response

```json
{
  "message":"操作成功！" // 消息,
  "status":true
}
```

##日志

### 1.GET:/warnLogs/all(获取指定时间内所有报警日志)

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称      | 参数类型                  | 说明    |
| --------- | --------------------- | ----- |
| stationId      | `int`              | 站点id |
| startTime | `yyyy-MM-dd HH:mm:ss` | 开始时间  |
| endTime   | `yyyy-MM-dd HH:mm:ss` | 结束时间  |
| currentPage    | `int`              | 当前页码 |

4.Response

```json
{
  "totalPages":10,//总页码数
  "data": [
    {
      "message": "预警信息1",
      "recordTime": "2017-10-23 14:00:00",
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

### 2.GET:/warnLogs/read(获取指定时间内已读报警日志)

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称      | 参数类型                  | 说明    |
| --------- | --------------------- | ----- |
| stationId | `int`              | 站点id |
| startTime | `yyyy-MM-dd HH:mm:ss` | 开始时间  |
| endTime   | `yyyy-MM-dd HH:mm:ss` | 结束时间  |
| currentPage      | `int`          | 请求的页码 |

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

### 3.GET:/warnLogs/unread(获取指定时间内未读报警日志)

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称      | 参数类型                  | 说明    |
| --------- | --------------------- | ----- |
| stationId | `int`              | 站点id |
| startTime | `yyyy-MM-dd HH:mm:ss` | 开始时间  |
| endTime   | `yyyy-MM-dd HH:mm:ss` | 结束时间  |
| currentPage      | `int`       | 请求的页码 |

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

### 4.PUT:/warnLogs/markRead(标记日志已读)

1.类型

HTTP

2.方法

PUT

3.Request

| 参数名称 | 参数类型    | 说明        |
| --------| ------- | --------- |
```json
{
	"data": "Array", //需要标记已读的id数组
	"operator": "String",//操作人姓名
}
```
4.Response

```json
{
  "status":true,//true or false
  "message":"操作成功！" //提示消息
}
```



### 5.GET:/operationLogs(获取指定时间内操作日志)

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称      | 参数类型                  | 说明    |
| --------- | --------------------- | ----- |
| stationId | `int`					| 站点id    |
| startTime | `yyyy-MM-dd HH:mm:ss` | 开始时间  |
| endTime   | `yyyy-MM-dd HH:mm:ss` | 结束时间  |
| currentPage      | `int`          | 请求的页码 |

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





