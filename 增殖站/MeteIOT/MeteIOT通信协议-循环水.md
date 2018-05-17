# METE-IOT通信接口文档

## 站点端（stationClient）

###1.:arrow_left:WS:连接验证

#### 1.类型

`websocket`

#### 2.REQUEST

示例代码：

```javascript
// config.meteProxy为中央服务器地址
io(String:config.meteProxy, {
  query: {
    stationId: config.stationId, // 站点ID，必传
    stationName: String:config.stationName, // 站点名称，必传
    account: String:config.account, // 必传
    password: String:config.password // 必传
  },
  timeout: 3000
})
```

####3.RESPONSE

成功则与中央服务器连接成功，否则失败！

### 2.:arrow_right:on:getDeviceStatus 获取设备运行状态数据

#### 1.类型

`socket.io:on`

#### 2.REQUEST

`none`

#### 3.RESPONSE

`none`

#### 4.备注

当监听到该事件时，站点需要去发送查询设备运行状态指令。

### 3.:arrow_right:on:getMonitorData 获取传感器数据

#### 1.类型

`socket.io:on`

#### 2.REQUEST

`none`

#### 3.RESPONSE

`none`

#### 4.备注

当监听到该事件时，站点需要去发送查询传感器数据指令。

### 4.:arrow_right:on:command 执行中央服务器指令

#### 1.类型

`socket.io:on`

#### 2.REQUEST

`none`

#### 3.RESPONSE

| 字段       | 类型      | 备注                              |
| ---------- | --------- | --------------------------------- |
| serialPort | `string`  | 串口服务器地址                    |
| deviceId   | `string`  | 设备ID                            |
| command    | `Boolean` | 命令，开机为`true`，关机为`false` |
| eventBusId | `string`  | 中央服务器事件队列ID              |

示例：

```javascript
socket.on('command', function (serialPort, deviceId, command, eventBusId) {
  const deviceBit = config.deviceList[deviceId]
  const mask = config.deviceStatus
  if (deviceBit && mask) {
    let cmd
    if (command) {
      // 开启设备
      cmd = mask | deviceBit
    } else {
      cmd = ~deviceBit & mask
    }
    console.log(`${command ? '开启' : '关闭'}SerialPort-${serialPort}下的${deviceId}设备`)
    app.sendCommand(`SerialPort-${serialPort}`, Buffer.from([0xAA, 0x00, cmd, 0x88]), {
      time: Date.now(),
      device: deviceId, // 这里的device特指设备，而不是串口服务器
      eventBusId
    })
  }
})
```



#### 4.备注

当监听到该事件时，站点需要去发送相关指令。

### 5.:arrow_left:emit:onMonitorData 发送传感器数据至中央服务器

#### 1.类型

`socket.io:emit`

#### 2.REQUEST

需要传递数据，参数个数为一个，下面为示例数据格式

```json
{
	"data":{
        "value":8.63,
        "temperature":22,
        "deviceId":"PH202-001",
        "type":"Monitor",
        "equipTypeId":12,
        "temperatureId":10,
        "createTime":"2018-5-14 14:34:42"
    },
    "id":2
}
```

示例代码：

```javascript
// body 为上面示例数据
socket.emit(`onMonitorData`, body)
```

#### 3.RESPONSE

`none`

### 6.:arrow_left:emit:onDeviceStatusData 发送继电器设备数据至中央服务器

#### 1.类型

`socket.io:emit`

#### 2.REQUEST

需要传递数据，参数个数为一个，下面为示例数据格式

```json
{
    "data":{
        "plcId":"plc-001",
        "data":{
            "DO206-002":false,
            "Temperature":true,
            "PH202-001":false
        },
        "type":"DeviceStatus",
        "createTime":"2018-5-14 14:40:36"
    },
    "id":2
}
```

示例代码：

```javascript
// body 为上面示例数据
socket.emit(`onDeviceStatusData`, body)
```

#### 3.RESPONSE

`none`

### 7.:arrow_left:emit:onCommandData 响应中央服务器发送控制命令

#### 1.类型

`socket.io:emit`

#### 2.REQUEST

需要传递数据，参数个数为一个，下面为示例数据格式

```json
{
    "data":{
        "plcId":"plc-001",
        "data":{
            "DO206-002":false,
            "Temperature":true,
            "PH202-001":true
        },
        "type":"Command",
        "status":true,
        "message":"操作成功！",
        "eventBusId":1526280255344,
        "createTime":"2018-5-14 14:44:16"
    },
    "id":2
}
```

示例代码：

```javascript
// body 为上面示例数据
socket.emit(`onCommandData`, body)
```

#### 3.RESPONSE

`none`

## Java后端（serverClient）

### 1.:arrow_left:WS:连接验证

#### 1.类型

`websocket`

#### 2.REQUEST

示例代码：

```javascript
// config.meteProxy为中央服务器地址
io(String:config.meteProxy, {
  query: {
    account: String:config.account, // 必传
    password: String:config.password // 必传
  },
  timeout: 3000
})
```

#### 3.RESPONSE

成功则与中央服务器连接成功，否则失败！

### 2.:arrow_right: on:onMonitorData 获取传感器数据

#### 1.类型

`socket.io:on`

#### 2.REQUEST

`none`

#### 3.RESPONSE

```json
{
	"data":{
        "value":8.63, // 传感器值(后端需要)
        "temperature":22, // 温度值
        "deviceId":"PH202-001", // 传感器设备ID
        "type":"Monitor", // 事件类型
        "equipId":12, // 设备ID(后端需要)
        "temperatureId":10, // 温度设备ID，即将弃用！！！
        "createTime":"2018-5-14 14:34:42" // 数据生成时间(后端需要)
    },
    "id":2 // ID暂时为站点ID
}
```

#### 4.备注

该事件**10s**触发一次

### 3.:arrow_right: POST:offlineData 转存传感器离线数据至后端数据库

#### 1.类型

`HTTP:POST/JSON`

#### 2.REQUEST

请求体为`Array`，数组数据结构示例如下：

```json
[
    {
		"data":{
			"value":8.63, // 传感器值(后端需要)
			"temperature":22, // 温度值
			"deviceId":"PH202-001", // 传感器设备ID
			"type":"Monitor", // 事件类型
			"equipId":12, // 设备ID(后端需要)
			"temperatureId":10, // 温度设备ID，即将弃用！！！
			"createTime":"2018-5-14 14:34:42" // 数据生成时间(后端需要)
		},
		"id":2 // ID暂时为站点ID
	},
	{
		"data":{
			"value":8.63, // 传感器值(后端需要)
			"temperature":22, // 温度值
			"deviceId":"PH202-001", // 传感器设备ID
			"type":"Monitor", // 事件类型
			"equipId":12, // 设备ID(后端需要)
			"temperatureId":10, // 温度设备ID，即将弃用！！！
			"createTime":"2018-5-14 14:34:42" // 数据生成时间(后端需要)
		},
		"id":2 // ID暂时为站点ID
	}
]
```

#### 3.RESPONSE

```json
{
    "status": true, // 失败为false，成功为true
}
```

#### 4.备注

**该接口需要JAVA后端按照以上规范实现，后端在每次连接到中央服务器时，由中央服务器触发该接口**

### 3.:arrow_left: PUT:command JAVA后端发送指定命令给站点

#### 1.类型

`HTTP:PUT/JSON`

#### 2.REQUEST

| 参数       | 参数类型  | 参数说明                          |
| ---------- | --------- | --------------------------------- |
| account    | `string`  | 用户名                            |
| password   | `string`  | 密码                              |
| stationId  | `int`     | 站点ID                            |
| serialPort | `string`  | 串口服务器地址                    |
| deviceId   | `string`  | 设备ID                            |
| command    | `Boolean` | 开关命令，开为`true`，关为`false` |

#### 3.RESPONSE

正常情况下返回的数据：

```json
{
    "data": {
        "plcId": "plc-001",
        "data": {
            "DO206-002": false,
            "Temperature": true,
            "PH202-001": true
        },
        "type": "Command",
        "status": true,
        "message": "操作成功！",
        "eventBusId": 1526280278543,
        "createTime": "2018-5-14 14:44:39"
    },
    "id": 2
}
```

超时返回数据：

```json
{
    "status": false, 
    "message": '操作超时，请重试！'
}
```

#### 4.备注

该接口由中央服务器提供，JAVA后端只需安装上面要求调用即可，**注意该接口可能会出现不稳定情况，中央服务器端设置了`3s`超时时间。**

## 前端（frontEndClient）

### 1.⬅️WS:连接验证

#### 1.类型

`websocket`

#### 2.REQUEST

示例代码：

```javascript
// config.meteProxy为中央服务器地址
io(String:config.meteProxy)
```

#### 3.RESPONSE

成功则与中央服务器连接成功，否则失败！

#### 4.备注

连接中央服务器端时需要做权限认证，该认证体系和登录权限一致！

### 2.➡️on:onDeviceStatus 获取设备运行状态数据

#### 1.类型

`socket.io:on`

#### 2.REQUEST

`none`

#### 3.RESPONSE

```json
{
    "data":{
        "plcId":"plc-001",
        "data":{
            "DO206-002":false,
            "Temperature":true,
            "PH202-001":false
        },
        "type":"DeviceStatus",
        "createTime":"2018-5-14 14:40:36"
    },
    "id":2
}
```

示例代码：

```javascript
 socket.on('onDeviceStatusData', (data) => {
      console.log('onDeviceStatusData', data)
 })
```

#### 4.备注

当监听到该事件时，中央服务器返回站点传递过来的数据。

### 3.➡️on:onMonitorData 获取传感器数据

#### 1.类型

`socket.io:on`

#### 2.REQUEST

`none`

#### 3.RESPONSE

```json
{
	"data":{
        "value":8.63,
        "temperature":22,
        "deviceId":"PH202-001",
        "type":"Monitor",
        "equipTypeId":12,
        "temperatureId":10,
        "createTime":"2018-5-14 14:34:42"
    },
    "id":2
}
```

示例代码：

```javascript
 socket.on('onMonitorData', (data) => {
      console.log('onMonitorData', data)
 })
```

#### 4.备注

当监听到该事件时，中央服务器返回站点传递过来的数据。

