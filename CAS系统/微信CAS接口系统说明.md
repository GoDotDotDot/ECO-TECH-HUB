# 微型CAS接口系统说明

## SSO 单点登录

资源验证系统请求地址需要在接口名称前加 `sso/`

### POST:login(登录)

1.类型

HTTP

2.方法

POST

3.Request

| 参数名称             | 参数类型         | 说明      |
| ---------------- | ------------ | ------- |
| account          | `string`     | 用户名     |
| password         | `string`     | 密码      |
| ~~verticalCode~~ | ~~`string`~~ | ~~验证码~~ |

4.Response

```json
{
  success:true, // true or false
  message:"登录成功！",
  token:`this is a token`
}
```

### GET:logout(注销)

1.类型

HTTP

2.方法

GET

3.Request

4.Response

```json
{
  success:true, // true or false
  message:"注销成功！"
}
```

### GET:auth(验证是否登录)

1.类型

HTTP

2.方法

GET

3.Request

4.Response

```json
{
  message:"已登录",
  login:true //true or false
}
```

### POST:register(注册)

1.类型

HTTP

2.方法

POST

3.Request

| 参数名称             | 参数类型         | 说明      |
| ---------------- | ------------ | ------- |
| account          | `string`     | 用户名     |
| password         | `string`     | 密码      |
| ~~verticalCode~~ | ~~`string`~~ | ~~验证码~~ |

4.Response

```json
{
  success:true, // true or false
  message:"注册成功！"
}
```

## CAS 资源验证系统

资源验证系统请求地址需要在接口名称前加 `cas/`

### GET:auth(登录)

**说明**：

​	验证步骤：1.登录验证 —> 2.类型验证 —> 3.资源验证 —> 4.权限验证

1.类型

HTTP

2.方法

GET

3.Request

| 参数名称            | 参数类型     | 说明                                       |
| --------------- | -------- | ---------------------------------------- |
| type            | `string` | 资源类型，目前只有`router`和`api`两种                |
| token[optional] | `string` | token令牌，浏览器请求可以不传，但是需要传递`session-cookie`。移动端请求必须传递`token`！ |
| name            | `string` | 资源名称，一般为`url`名称。                         |
| action          | `string` | 行为，为`browser`、`delete`、`insert`、`update`四种之一 |
| d               | `string` | 设备，`m`为移动设备                              |

4.Response

response有四种情形

- 未授权

```json
{
  statusCode:401, // 状态码
  message:"未授权，请登录后重试！",
  status:false
}
```

- 资源不存在

```json
{
  statusCode:404, // 状态码
  message:"资源不存在！",
  status:false
}
```

- 权限认证不通过

```json
{
  statusCode:401, // 状态码
  message:"权限认证未通过！",
  status:false
}
```

- 验证通过

```json
{
  statusCode:200, // 状态码
  message:"验证通过！",
  status:true
}
```

