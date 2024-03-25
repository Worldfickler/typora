# API 开发平台

## 项目背景

1. 前端开发需要用到后台接口
2. 使用现成的系统的功能[搏天api-免费api接口平台 (btstu.cn)](http://api.btstu.cn/)

**做一个API接口平台**

1. 防止攻击（安全性）
2. 不能被随便调用（限制、开通）
3. 统计调用次数
4. 计费
5. 流量保护
6. API 接入

## 项目介绍

做一个提供 API 接口调用的平台，用户可以注册，开通接口调用权限。用户可以使用接口，并且每次调用会进行统计。管理员可以发布接口、下线接口，以及可视化接口的调用情况、数据。

### 业务流程

![e9c6efac-04aa-4bed-9b63-6f5c597fa715](http://qiniu.fickler.top/img/e9c6efac-04aa-4bed-9b63-6f5c597fa715.png)

### 技术选型

#### 前端

- Ant Design Pro
- React
- Umi
- Umi Request（Axios 的封装）

#### 后端

- springboot
- Spring Boot Start（SDK 开发）
- 网关、限流、日志实现

## Ant Design Pro

### 1. 初始化

1. 安装脚手架

```shell
npm i @ant-design/pro-cli -g
```

![image-20230504000017412](http://qiniu.fickler.top/img/image-20230504000402653.png)

2. 创建项目

```shell
pro create api-frontend
```

![image-20230504000402653](http://qiniu.fickler.top/img/image-20230504000017412.png)

### 2. 安装依赖

1. 使用前端工具打开项目

![image-20230504000535225](http://qiniu.fickler.top/img/image-20230504000631309.png)

2. 安装依赖

```shell
yarn 或者 npm install
```

![image-20230504000631309](http://qiniu.fickler.top/img/image-20230505005437393.png)

> 我这里是 yarn-js 是因为，我大数据中的 yarn 包和包管理工具 yarn 冲突了，所以把 yarn 改成了 yarn-js

### 3. 启动项目

![image-20230505002347795](http://qiniu.fickler.top/img/image-20230505095452422.png)

![image-20230505002414267](http://qiniu.fickler.top/img/image-20230505095503181.png)

## 数据库设计

### 1. 接口信息表

| id             | 主键                       |
| -------------- | -------------------------- |
| name           | 接口名称                   |
| description    | 描述                       |
| url            | 接口地址                   |
| type           | 请求类型                   |
| requestHeader  | 请求头                     |
| responseHeader | 响应头                     |
| status         | 接口状态（0-关闭，1-开启） |
| method         | 请求类型                   |
| userId         | 创建人                     |
| createTime     | 创建时间                   |
| updateTime     | 更新时间                   |
| isDelete       | 是否删除                   |

### 2. 实例代码

```mysql
create table if not exists api.`interface_info`
(
    `id` bigint not null auto_increment comment '主键' primary key,
    `name` varchar(256) not null comment '用户名',
    `description` varchar(256) null comment '描述',
    `url` varchar(512) not null comment '接口地址',
    `requestHeader` text null comment '请求头',
    `responseHeader` text null comment '响应头',
    `userId` bigint not null comment '创建人',
    `status` int default 0 not null comment '接口状态（0 - 关闭， 1 - 开启））',
    `method` varchar(256) not null comment '请求类型',
    `createTime` datetime default CURRENT_TIMESTAMP not null comment '创建时间',
    `updateTime` datetime default CURRENT_TIMESTAMP not null on update CURRENT_TIMESTAMP comment '更新时间',
    `isDelete` tinyint default 0 not null comment '是否删除(0-未删, 1-已删)'
    ) comment '接口信息表';
```

运行 sql 语句

![image-20230505005437393](http://qiniu.fickler.top/img/image-20230505002414267.png)



## 后端代码

### 一、初始化

#### 1. 使用 springboot 项目初始化模板

Java SpringBoot 项目初始化模板，整合了常用框架和示例代码，可以在此基础上快速开发项目。



#### 2. 模板功能

- Spring Boot 2.7.0（贼新）
- Spring MVC
- MySQL 驱动
- MyBatis
- MyBatis Plus
- Spring Session Redis 分布式登录
- Spring AOP
- Apache Commons Lang3 工具类
- Lombok 注解
- Swagger + Knife4j 接口文档
- Spring Boot 调试工具和项目处理器
- 全局请求响应拦截器（记录日志）
- 全局异常处理器
- 自定义错误码
- 封装通用响应类
- 示例用户注册、登录、搜索功能
- 示例单元测试类
- 示例 SQL（用户表）

访问 localhost:7529/api/doc.html 就能在线调试接口了，不需要前端配合啦~



### 二、使用 mybatis-plus

#### 1. mybatis-plus 插架自动生成

![image-20230505095452422](http://qiniu.fickler.top/img/image-20230505002347795.png)

![image-20230505095503181](http://qiniu.fickler.top/img/image-20230505101435502.png)



#### 2. 转移文件

将在 `generator` 中生成的文件移动到对应的包下

![image-20230505095710438](http://qiniu.fickler.top/img/image-20230505100634840.png)

#### 3. 修改一下文件路径

![image-20230505095908197](http://qiniu.fickler.top/img/image-20230505100919248.png)



#### 4. 为逻辑删除打上注解

> mybatis-plus 插件生成的时候，是不能判断是否是逻辑删除的字段，因此我们要为逻辑删除字段额外的添加逻辑删除注解

![image-20230505101435502](http://qiniu.fickler.top/img/image-20230505100943218.png)



### 三、增删改查crud

#### 1. 修改 controller 层

1. 复制一份 `PostController` 改名为 `InterfaceInfoController`

![image-20230505100634840](http://qiniu.fickler.top/img/image-20230505095710438.png)



2. 替换关键字

**ctrl + r 快捷键全局替换**

- post 替换为 interfaceInfo 

![image-20230505100919248](http://qiniu.fickler.top/img/image-20230505095908197.png)



- Post 替换为 InterfaceInfo

![image-20230505100943218](http://qiniu.fickler.top/img/image-20230505101003677.png)



- InterfaceInfoMapping 替换为 PostMapping

![image-20230505101003677](http://qiniu.fickler.top/img/image-20230505104137589.png)



#### 2. 封装 InterfaceInfo 实体类

> 对于每一个接口传入的信息，我们都封装一个专门的实体类

**model包 -> dto 包**

在 `model` 包下新建一个 `interfaceinfo` 包，将之前 `post` 包下的三个请求复制到新包中，并修改名字

![image-20230505102154084](http://qiniu.fickler.top/img/image-20230506105146113.png)



**InterfaceInfoAddRequest**

```java
package com.fickler.project.model.dto.interfaceinfo;

import lombok.Data;

import java.io.Serializable;

/**
 * 创建请求
 *
 * @TableName product
 */
@Data
public class InterfaceInfoAddRequest implements Serializable {

    /**
     * 用户名
     */
    private String name;

    /**
     * 描述
     */
    private String description;

    /**
     * 接口地址
     */
    private String url;

    /**
     * 请求头
     */
    private String requestHeader;

    /**
     * 响应头
     */
    private String responseHeader;

    /**
     * 创建人
     */
    private Long userId;

    /**
     * 请求类型
     */
    private String method;

    private static final long serialVersionUID = 1L;
}
```



**InterfaceInfoQueryRequest**

```java
package com.fickler.project.model.dto.interfaceinfo;

import com.fickler.project.common.PageRequest;
import lombok.Data;
import lombok.EqualsAndHashCode;

import java.io.Serializable;

/**
 * 查询请求
 *
 * @author yupi
 */
@EqualsAndHashCode(callSuper = true)
@Data
public class InterfaceInfoQueryRequest extends PageRequest implements Serializable {

    /**
     * 主键
     */
    private Long id;

    /**
     * 用户名
     */
    private String name;

    /**
     * 描述
     */
    private String description;

    /**
     * 接口地址
     */
    private String url;

    /**
     * 请求头
     */
    private String requestHeader;

    /**
     * 响应头
     */
    private String responseHeader;

    /**
     * 创建人
     */
    private Long userId;

    /**
     * 接口状态（0 - 关闭， 1 - 开启））
     */
    private Integer status;

    /**
     * 请求类型
     */
    private String method;

    private static final long serialVersionUID = 1L;
}
```



**InterfaceInfoUpdateRequest**

```java
package com.fickler.project.model.dto.interfaceinfo;

import lombok.Data;

import java.io.Serializable;

/**
 * 更新请求
 *
 * @TableName product
 */
@Data
public class InterfaceInfoUpdateRequest implements Serializable {

    /**
     * 主键
     */
    private Long id;

    /**
     * 用户名
     */
    private String name;

    /**
     * 描述
     */
    private String description;

    /**
     * 接口地址
     */
    private String url;

    /**
     * 请求头
     */
    private String requestHeader;

    /**
     * 响应头
     */
    private String responseHeader;

    /**
     * 创建人
     */
    private Long userId;

    /**
     * 接口状态（0 - 关闭， 1 - 开启））
     */
    private Integer status;

    /**
     * 请求类型
     */
    private String method;

    private static final long serialVersionUID = 1L;
}
```



> **为什么要写三个接口？**
>
> 1. 主要是为了方便前端对接
>
> 如果全部的参数都提供给前端，前端并不知道哪些参数需要传，哪些参数不需要传
>
> 2. 一定程度上保护了数据库的字段信息
>
> 不需要键全部的参数暴露给用户



#### 3. 修改 service 层

`controller` 层的报错

![image-20230505104137589](http://qiniu.fickler.top/img/image-20230506105401855.png)

因为我们直接复制的 `PostController` 的代码，`service` 包中的方法没有复制，将 `PostServiceImpl` 内的 validPost 方法复制到 `InterfaceInfoServiceImpl` 中

```java
@Override
public void validInterfaceInfo(InterfaceInfo interfaceInfo, boolean add) {

    if (interfaceInfo == null) {
        throw new BusinessException(ErrorCode.PARAMS_ERROR);
    }

    Long id = interfaceInfo.getId();
    String name = interfaceInfo.getName();
    String description = interfaceInfo.getDescription();
    String url = interfaceInfo.getUrl();
    String requestHeader = interfaceInfo.getRequestHeader();
    String responseHeader = interfaceInfo.getResponseHeader();
    Long userId = interfaceInfo.getUserId();
    Integer status = interfaceInfo.getStatus();
    String method = interfaceInfo.getMethod();
    Date createTime = interfaceInfo.getCreateTime();
    Date updateTime = interfaceInfo.getUpdateTime();
    Integer isDelete = interfaceInfo.getIsDelete();

    // 创建时，所有参数必须非空
    // todo 参数校验
    if (add) {
        if (StringUtils.isAnyBlank(name)) {
            throw new BusinessException(ErrorCode.PARAMS_ERROR);
        }
    }
    if (StringUtils.isNotBlank(name) && name.length() > 50L) {
        throw new BusinessException(ErrorCode.PARAMS_ERROR, "内容过长");
    }
}
```



#### 4. 运行测试一下

![image-20230505113124321](http://qiniu.fickler.top/img/image-20230505102154084.png)



## 前端

### 一、配置插件

前端项目使用一些代码规范文件要求，为了项目更加规范，添加一些插件

> 搜索 `eslint` 选上自动识别

![image-20230506105146113](http://qiniu.fickler.top/img/image-20230505113124321.png)



> 搜索 `prettier` 打 √ 美化代码

![image-20230506105401855](http://qiniu.fickler.top/img/image-20230506110305412.png)



### 二、接口调用

> 前端调用后端接口，我们一般要自己写方法，例如：
>
> ```tsx
> /** 获取当前的用户 GET /api/currentUser */
> export async function currentUser(options?: { [key: string]: any }) {
> return request<{
>  data: API.CurrentUser;
> }>('/api/currentUser', {
>  method: 'GET',
>  ...(options || {}),
> });
> }
> ```
>
> 除了自己写，我们还可以自己生成，ant design pro 提供了 oneapi 插件自动生成



#### 1. 使用 oneapi 插件自动生成

如果前端要自动生成，需要将后端遵循的 openapi 规范的文档提供给前端

![image-20230506110117913](http://qiniu.fickler.top/img/image-20230506110117913.png)

![image-20230506110150064](http://qiniu.fickler.top/img/image-20230506110150064.png)



1. 打开 `config.ts` 找到 `openApi`

![image-20230506110305412](http://qiniu.fickler.top/img/image-20230506112133083.png)



2. 我们只需要一个，删除一个

![image-20230506110441814](http://qiniu.fickler.top/img/image-20230506111620728.png)

![image-20230506110550343](http://qiniu.fickler.top/img/image-20230506114558894.png)

> `schemaPath：` 1. 建立本地的 json 文件，将文件地址复制到里面；2. 使用在线的 json 地址
>
> `projectName：` 后端项目名称



#### 2. 测试一下

> 找到 `package.json` 中的 `openapi` 命令执行

![image-20230506111412248](http://qiniu.fickler.top/img/image-20230506124450248.png)



执行成功

![image-20230506111620728](http://qiniu.fickler.top/img/image-20230506121335564.png)



看一下 `service` 中生成的文件

![image-20230506112133083](http://qiniu.fickler.top/img/image-20230506125426744.png)



1. 因为我们有后端，所以应该直接请求真实环境，所以直接用 dev 模式运行

![image-20230506113524151](http://qiniu.fickler.top/img/image-20230510131018655.png)



2. 我们还要更改请求后端的地址，确保我们能够请求到服务器

![image-20230506114412595](http://qiniu.fickler.top/img/image-20230510131223698.png)



3. 测试一下

> 报错了，提示前后端接口定义不一致

![image-20230506114558894](http://qiniu.fickler.top/img/image-20230510132919438.png)



### 三. 修改登录的接口

> 这个 `login` 是使用 oneapi 生成之前自带的登录接口

![image-20230506114925986](http://qiniu.fickler.top/img/image-20230510135534054.png)



#### 1. 修改成我们自己的登录接口

1. 修改成我们自己的登录接口

![image-20230506115331175](http://qiniu.fickler.top/img/image-20230510135831746.png)



2. 修改登录逻辑

![image-20230506115603258](http://qiniu.fickler.top/img/image-20230506124638143.png)



3. 修改下面的账号和名称的参数名称

![image-20230506115838504](http://qiniu.fickler.top/img/image-20230515145653378.png)



4. 再测试一下

![image-20230506121335564](http://qiniu.fickler.top/img/image-20230510140745053.png)



登录成功，但是没有跳转



> 为什么没有跳转？
>
> 因为我们没有记录用户的登录状态，不知道它是否登录成功！！！



#### 2. 设置用户的登录状态

> 这个方法，当我们首次访问页面的时候，获取用户信息，获取当前全局的一些状态，可以把它当做全局变量

![image-20230506121504098](http://qiniu.fickler.top/img/image-20230510140755309.png)



1. 在 `typings.d.ts` 中添加全局状态类型

```tsx
/**
 * 全局状态类型
 */
interface InitialState {
  loginUser?: API.UserVO;
}
```

![image-20230506124450248](http://qiniu.fickler.top/img/image-20230515173322162.png)



2. 修改 `getInitialState()` 方法

![image-20230506124638143](http://qiniu.fickler.top/img/image-20230515145619546.png)



3. 优化 `getInitialState()` 方法

```tsx
/**
 * @see  https://umijs.org/zh-CN/plugins/plugin-initial-state
 * */
export async function getInitialState(): Promise<InitialState> {
  // 页面首次加载的时候，获取要全局保存的数据，比如用户登录信息
  const state: InitialState ={
    loginUser: undefined,
  }
  try {
    const res = await getLoginUserUsingGET();
    if (res.data) {
      state.loginUser = res.data;
    }
  } catch (error) {
    history.push(loginPath);
  }
  return state;
}
```



4. 设置登录状态

![image-20230506125426744](http://qiniu.fickler.top/img/image-20230515173259409.png)



5. 测试一下

![image-20230510131018655](http://qiniu.fickler.top/img/image-20230515173534872.png)



#### 3. 刷新后需要重新登录

原因：前端没有种上 cookie，或者是前端种上了 cookie，但是向后端发送请求的时候没有带上 cookie



```tsx
withCredentials: true,
```



![image-20230510131223698](http://qiniu.fickler.top/img/image-20230515174026119.png)





### 四、接口管理

接口管理页面只有管理员可以查看



#### 1. 判断用户权限



canUser：只要用户登录就有权限

canAdmin：用户必须是管理员才有权限



在 `access.ts` 中修改代码



![image-20230510132452878](http://qiniu.fickler.top/img/image-20230515174001846.png)



查看页面



![image-20230510132919438](http://qiniu.fickler.top/img/image-20230515174121913.png)



#### 2. 改造表格页面



找到 `src/pages/TableList/index.tsx`



> `ctrl + shift + -`：全局压缩



![image-20230510133239774](http://qiniu.fickler.top/img/image-20230516102146293.png)



修改 `TableList` 中的 `columns`



```tsx
const columns: ProColumns<API.RuleListItem>[] = [
  {
    title: 'id',
    dataIndex: 'id',
    valueType: 'index',
  },
  {
    title: '接口名称',
    dataIndex: 'name',
    valueType: 'text',
  },
  {
    title: '描述',
    dataIndex: 'description',
    valueType: 'textarea',
  },
  {
    title: '请求方法',
    dataIndex: 'method',
    valueType: 'text',
  },
  {
    title: 'url',
    dataIndex: 'url',
    valueType: 'text',
  },
  {
    title: '请求头',
    dataIndex: 'requestHeader',
    valueType: 'textarea',
  },
  {
    title: '响应头',
    dataIndex: 'responseHeader',
    valueType: 'textarea',
  },
  {
    title: '状态',
    dataIndex: 'status',
    hideInForm: true,
    valueEnum: {
      0: {
        text: '关闭',
        status: 'Default',
      },
      1: {
        text: '开启',
        status: 'Processing',
      },
    },
  },
  {
    title: '创建时间',
    dataIndex: 'createTime',
    valueType: 'dateTime',
  },
  {
    title: '更新时间',
    dataIndex: 'updateTime',
    valueType: 'dateTime',
  },
  {
    title: '操作',
    dataIndex: 'option',
    valueType: 'option',
    render: (_, record) => [
      <a
        key="config"
        onClick={() => {
          handleUpdateModalOpen(true);
          setCurrentRow(record);
        }}
      >
        配置
      </a>,
      <a key="subscribeAlert" href = "https://">
        订阅警报
      </a>,
    ],
  },
];
```



![image-20230510135534054](http://qiniu.fickler.top/img/image-20230515173518592.png)



没有显示数据，下面有一个 request 方法，只需要将传入的值改为我们自己的方法就可以了



![image-20230510135741553](http://qiniu.fickler.top/img/image-20230516102233341.png)



查看页面



![image-20230510135831746](http://qiniu.fickler.top/img/image-20230515174132174.png)



虽然拿到了数据，但是报错了



**如何排除错误？**

1. 请求参数是否一致
2. 响应值是否一致



修改 `request` 请求参数和响应值



![image-20230510140755309](http://qiniu.fickler.top/img/image-20230516112430802.png)



![image-20230510140745053](http://qiniu.fickler.top/img/image-20230516113032704.png)



### 五、页面优化

#### 1. 完善接口管理页面

1. 修改地方

![image-20230515145619546](http://qiniu.fickler.top/img/image-20230516113618457.png)



2. 修改后的页面

![image-20230515145653378](http://qiniu.fickler.top/img/image-20230516113515662.png)



#### 2. 完善新建

1. 编写模态框

![image-20230515173223634](http://qiniu.fickler.top/img/image-20230522160405867.png)



2. 在 index 使用新建模态框

![image-20230515173259409](http://qiniu.fickler.top/img/image-20230522160421269.png)



3. 查看效果

![image-20230515173322162](http://qiniu.fickler.top/img/image-20230522160502960.png)



#### 3. 完善修改

1. 按照新建的模态框重新构造一个修改的模态框

![image-20230515173444915](http://qiniu.fickler.top/img/image-20230522160617054.png)



2. 在 index 页面中使用更改的模态框

![image-20230515173518592](http://qiniu.fickler.top/img/image-20230522161533660.png)



3. 查看效果

![image-20230515173534872](http://qiniu.fickler.top/img/image-20230522162148137.png)



**注意事项**

直接使用 table 的 form 属性，只会获取第一次的表单值，后面不会继续更改

换句话说就是，直接使用 form 属性，点开第一个接口得到的表单值和点开第二个接口得到的表单值是相同的，不会进行更新



#### 4. 添加新建接口的校验

ant design pro 组件中有校验表单的属性

[快速开始 - ProComponents (ant.design)](https://procomponents.ant.design/docs)

![image-20230515174001846](http://qiniu.fickler.top/img/image-20230522162606580.png)



![image-20230515174026119](http://qiniu.fickler.top/img/image-20230522161647444.png)



#### 5. 完善删除

![image-20230515174121913](http://qiniu.fickler.top/img/image-20230522165354281.png)



![image-20230515174132174](http://qiniu.fickler.top/img/image-20230522165435614.png)



## 后端开发



### 一、模拟接口

#### 1. 创建项目

![image-20230516102146293](http://qiniu.fickler.top/img/image-20230522170031082.png)



![image-20230516102233341](http://qiniu.fickler.top/img/image-20230522170757866.png)



#### 2. 提供三个模拟接口

1. 接口大致内容

> 1. GET 接口
> 2. POST 接口（url 传参）
> 3. POST 接口（Restful）



2. 新建 controller 层

![image-20230516112230435](http://qiniu.fickler.top/img/image-20230522171621241-1684747802116-2.png)

```java
package com.fickler.apiinterface.controller;

import com.fickler.apiinterface.model.User;
import org.springframework.web.bind.annotation.*;

/**
 * 名称 API
 * @author dell
 * @date 2023/5/16 10:41
 */

@RestController
@RequestMapping("/name")
public class NameController {

    @GetMapping("/")
    public String getNameByGet(String name) {
        return "GET 你的名字是" + name;
    }

    @PostMapping("/")
    public String getNameByPost(@RequestParam String name) {
        return "POST 你的名字是" + name;
    }

    @PostMapping("/user")
    public String getUsernameByPost(@RequestBody User user) {
        return "POST 用户名字是" + user.getUsername();
    }

}
```



3. 新建 User 类

![image-20230516112307871](http://qiniu.fickler.top/img/image-20230522170813244.png)

```java
package com.fickler.apiinterface.model;

import lombok.Data;

/**
 * 用户
 * @author dell
 * @date 2023/5/16 10:44
 */

@Data
public class User {

    private String username;

}
```



4. 配置文件

![image-20230516112350883](http://qiniu.fickler.top/img/image-20230522173040969.png)

```yml
server:
  port: 8123
  servlet:
    context-path: /api
```



#### 3. 接口测试

启动测试，访问地址：http://localhost:8123/api/name

![image-20230516112430802](http://qiniu.fickler.top/img/image-20230522173338252.png)



### 二、调用接口

> 开发者一般不通过导航栏去调用，一般都是采用**后端调用**，因为前端调用不安全
>
> 我们在后盾开发一下调用别人的接口，这里在这个项目中开发



#### 1. 几种 HTTP 的调用方式

1. HttpClient
2. RestTemplate
3. 第三方库（OKHttp、Hutool）



#### 2. 使用 Hutool 调用

1. hutool 文档

https://hutool.cn/

![image-20230516113008876](http://qiniu.fickler.top/img/image-20230522160554622.png)



2. 安装依赖

![image-20230516113032704](http://qiniu.fickler.top/img/image-20230522164850751.png)

```xml
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>5.8.16</version>
</dependency>
```



3. 使用 hutool 调用 get、post 接口

![image-20230516113515662](http://qiniu.fickler.top/img/image-20230522173214478.png)

```java
public String getNameByGet(String name) {
    //可以单独传入http参数，这样参数会自动做URL编码，拼接在URL中
    HashMap<String, Object> paramMap = new HashMap<>();
    paramMap.put("name", name);
    String result= HttpUtil.get("http://localhost:8123/api/name/", paramMap);
    System.out.println(result);
    return result;
}

public String getNameByPost(@RequestParam String name) {
    HashMap<String, Object> paramMap = new HashMap<>();
    paramMap.put("name", name);
    String result= HttpUtil.post("http://localhost:8123/api/name/", paramMap);
    System.out.println(result);
    return result;
}
```



4. 使用 hutool 调用 restful 请求接口

![image-20230516113618457](http://qiniu.fickler.top/img/image-20230523120121727.png)

```java
public String getUsernameByPost(@RequestBody User user) {
    String json = JSONUtil.toJsonStr(user);
    HttpResponse httpResponse = HttpRequest.post("http://localhost:8123/api/name/user")
            .body(json)
            .execute();
    System.out.println(httpResponse.getStatus());
    String result = httpResponse.body();
    System.out.println(result);
    return result;
}
```



5. 测试一下

![image-20230516113719314](http://qiniu.fickler.top/img/image-20230504000535225.png)



### 三、API签名认证

**本质：**

1. 签发签名
2. 使用签名（校验签名）

**为什么需要？**

保证安全性，不能随便一个人就可以调用

**怎么实现？**

通过 http request header 头传递参数

> 参数1：accessKey 调用的表示（复杂、无序、无规律）
>
> 参数2：sercetKey 密钥（复杂、无序、无规律）**该参数不传递**
>
> 参数3：用户请求参数
>
> 参数4：**sign** 签名
>
> 参数5：nonce 随机数，只能用一次 服务端要保存用过的随机数
>
> 参数6：timestamp 时间戳，校验它的有效期

类似用户名和密码，区别：ak、sk 是无状态的



#### 1. 修改数据库

由于我们的 **user表** 里面没有 **ak、sk**，所以我们要修改数据库表

```mysql
create table if not exists user
(
    id           bigint auto_increment comment 'id' primary key,
    userName     varchar(256)                           null comment '用户昵称',
    userAccount  varchar(256)                           not null comment '账号',
    userAvatar   varchar(1024)                          null comment '用户头像',
    gender       tinyint                                null comment '性别',
    userRole     varchar(256) default 'user'            not null comment '用户角色：user / admin',
    userPassword varchar(512)                           not null comment '密码',
    `accessKey`  varchar(512) not null comment 'accessKey',
    `secretKey`  varchar(512) not null comment 'secretKey',
    createTime   datetime     default CURRENT_TIMESTAMP not null comment '创建时间',
    updateTime   datetime     default CURRENT_TIMESTAMP not null on update CURRENT_TIMESTAMP comment '更新时间',
    isDelete     tinyint      default 0                 not null comment '是否删除',
    constraint uni_userAccount
        unique (userAccount)
) comment '用户';
```



写一下构造方法，最好在外存传参调用

![image-20230522160405867](http://qiniu.fickler.top/img/image-20230506114412595.png)

![image-20230522160421269](http://qiniu.fickler.top/img/image-20230506110550343.png)



#### 2. 服务器端校验

我们需要在服务器端校验

![image-20230522160502960](http://qiniu.fickler.top/img/image-20230506110441814.png)



因为我们从请求头里面获取，所以要将 accessKey 和 secretKey 放入到请求头里面

![image-20230522160554622](http://qiniu.fickler.top/img/image-20230506111412248.png)



运行测试一下

![image-20230522160617054](http://qiniu.fickler.top/img/image-20230506115331175.png)



#### 3. 但是这样就安全了吗？？？

答案是否定的，因为我们的**请求可能被人拦截**，而我们把密码放在请求头里面，可以被人获取

千万不能把密钥直接放在服务器之前传递

> 一般是根据密钥，生成**签名 sign**



#### 4. 加密方式：

1. 对称加密
2. 非对称加密
3. md5 签名（不可解密）



#### 5. 签名的做法

假如，我们有用户参数，我们用密码与它拼接，用签名算法得到一个不可解密的值

**用户参数 + 密钥 => 签名生成算法（MD5，HMac，Sha1）=> 不可解密的值** 

例子：abc + abcdefgh => dadjsakdak



#### 6. 怎么知道签名对不对？

服务器端用一模一样的参数和算法生成签名，只要与用户传的一致，就表示密钥一致



#### 7. 怎么防止重放？

加 nonce 随机数只能用一次

服务端要保存用过的随机数

加 timestamp 时间戳，校验它的有效期



#### 8. 编写 ak，sk

自己写代码来编写 ak、sk



1. 添加参数

![image-20230522161533660](http://qiniu.fickler.top/img/image-20230506113524151.png)



2. 编写 getSign 签名算法

使用 hutool 的摘要加密 **Digester**

![image-20230522161647444](http://qiniu.fickler.top/img/image-20230506114925986.png)



![image-20230522162148137](http://qiniu.fickler.top/img/image-20230506115603258.png)



3. 重写服务端的校验

![image-20230522162606580](http://qiniu.fickler.top/img/image-20230506115838504.png)



这里剪贴一下生成签名的方法，将其封装成一个工具类

![image-20230522162735688](http://qiniu.fickler.top/img/image-20230506121504098.png)



4. 测试一下

![image-20230522164850751](http://qiniu.fickler.top/img/image-20230510132452878.png)



**API 签名认证是一个很留货的设计，具体有哪些参数，尽量服务端调用，参数名如何要根据场景来。**



### 四、开发一个简单的易用的 SDK

这些操作对于开发者，还是复杂，开发者应该只需要关心，自己需要调用哪些接口，传什么参数，密钥是什么，所以，我们需要开发一个便于开发者使用的 SDK。



todo：将 sdk 发布到 maven 仓库



> 开发 starter 的好处：开发者引入之后，可以直接在 application.yml 中写配置，自动创建客户端



#### 1. 新建项目

![image-20230522165354281](http://qiniu.fickler.top/img/image-20230510133239774.png)



![image-20230522165435614](http://qiniu.fickler.top/img/image-20230515173223634.png)



#### 2. 查看依赖

![image-20230522165550774](http://qiniu.fickler.top/img/image-20230510135741553.png)



![image-20230522165935306](http://qiniu.fickler.top/img/image-20230515173444915.png)



#### 3. 删除主动类

我们不需要这个 springboot 启动类，将其删掉

![image-20230522170031082](http://qiniu.fickler.top/img/image-20230516112350883.png)



#### 4. 新建配置类

注解：

```
@Data
@Configuration
@ConfigurationProperties("api.clint")
@ComponentScan
```



复制之前项目的接口，客户端

![image-20230522170757866](http://qiniu.fickler.top/img/image-20230516112230435.png)



导入 hutool 依赖

![image-20230522170813244](http://qiniu.fickler.top/img/image-20230516112307871.png)



回到配置类

![image-20230522171020881](http://qiniu.fickler.top/img/image-20230522162735688.png)



#### 5. 创建爱你 META_INF 文件夹

在 **resource** 下创建 **META-INF** 文件夹

在 **META-INF** 文件夹下新建 **spring.factories**

![image-20230522171621241](https://fickler-1314393782.cos.ap-beijing.myqcloud.com/img/image-20230522171621241.png)



复制配置类路径

```
# spring boot start
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.fickler.apiclintsdk.ApiClientConfig
```

![](http://qiniu.fickler.top/img/image-20230522165935306.png)



#### 6. 进行测试

1. 打包

![image-20230522173040969](http://qiniu.fickler.top/img/image-20230516113719314.png)



![image-20230522173214478](http://qiniu.fickler.top/img/image-20230522171020881.png)



2. 在 **api-interface** 项目中，将之前的代码删掉

![image-20230522173622020](http://qiniu.fickler.top/img/image-20230522165550774.png)



![image-20230522173338252](http://qiniu.fickler.top/img/image-20230522173622020.png)



3. 引入刚才写好的依赖

复制**api-clint-sdk**的信息

![image-20230522173434207](http://qiniu.fickler.top/img/image-20230523120020578.png)

```xml
<groupId>com.example</groupId>
<artifactId>api-clint-sdk</artifactId>
<version>0.0.1</version>
<name>api-clint-sdk</name>
<description>api-clint-sdk</description>
```



4. 复制到 **api-interface** 依赖文件中

![image-20230522173806646](http://qiniu.fickler.top/img/image-20230522173806646.png)‘

5. 打开配置文件

![image-20230523120020578](http://qiniu.fickler.top/img/image-20230522173434207.png)



6. 编写测试类

![image-20230523120059410](http://qiniu.fickler.top/img/image-20230523120059410.png)



7. 测试一下

![image-20230523120121727](http://qiniu.fickler.top/img/image-20230516113008876.png)



## 开发接口发布和下线功能

### 一、功能设计

#### 1. 发布接口（仅管理员可操作）

1. 校验该接口是否存在
2. 判断该接口是否可以调用
3. 修改接口数据库中的状态字段为 `1`



#### 2. 下线接口（仅管理员可操作）

1. 校验该接口是否存在
2. 修改接口数据库中的状态字段为 `0`



### 二、后端项目开发

#### 1. 创建发布和下线接口



![image-20230825114639417](http://qiniu.fickler.top/img/image-20230825114639417.png)



这两个接口要接收什么参数呢？

其实只需要接收接口的 id 即可。例如，我想要发布 id 唯一的接口，只需啊哟传递这个 id 作为参数即可，这样就可以清楚知道要对哪个接口进行发布操作。



为了方便我们进行 json 参数传递，将 id 这个参数封装一下。



![image-20230825114901830](http://qiniu.fickler.top/img/image-20230825114901830.png)



发布和下线接口只能管理员调用，为这两个接口打上只有管理员可以使用的注解。



![image-20230825115100032](http://qiniu.fickler.top/img/image-20230825115100032.png)



**注解的实现原理**

首先获取当前登录用户的信息，然后判断用户是否具有管理员权限。如果没有权限，就会直接抛出异常，如果有权限，就会继续执行后续操作。

> AOP 切片的基本应用之一。



```java
@Aspect
@Component
public class AuthInterceptor {

    @Resource
    private UserService userService;

    /**
     * 执行拦截
     *
     * @param joinPoint
     * @param authCheck
     * @return
     */
    @Around("@annotation(authCheck)")
    public Object doInterceptor(ProceedingJoinPoint joinPoint, AuthCheck authCheck) throws Throwable {
        List<String> anyRole = Arrays.stream(authCheck.anyRole()).filter(StringUtils::isNotBlank).collect(Collectors.toList());
        String mustRole = authCheck.mustRole();
        RequestAttributes requestAttributes = RequestContextHolder.currentRequestAttributes();
        HttpServletRequest request = ((ServletRequestAttributes) requestAttributes).getRequest();
        // 当前登录用户
        User user = userService.getLoginUser(request);
        // 拥有任意权限即通过
        if (CollectionUtils.isNotEmpty(anyRole)) {
            String userRole = user.getUserRole();
            if (!anyRole.contains(userRole)) {
                throw new BusinessException(ErrorCode.NO_AUTH_ERROR);
            }
        }
        // 必须有所有权限才通过
        if (StringUtils.isNotBlank(mustRole)) {
            String userRole = user.getUserRole();
            if (!mustRole.equals(userRole)) {
                throw new BusinessException(ErrorCode.NO_AUTH_ERROR);
            }
        }
        // 通过权限校验，放行
        return joinPoint.proceed();
    }
    
}
```



#### 2. 发布接口业务逻辑

1. 校验接口是否存在
2. 判断接口是否可以调用
3. 修改接口数据库中的状态字段



```java
/**
 * 上线
 *
 * @param idRequest
 * @param request
 * @return
 */
@PostMapping("/online")
@AuthCheck(mustRole = "admin")
public BaseResponse<Boolean> onlineInterfaceInfo(@RequestBody IdRequest idRequest,
                                                 HttpServletRequest request) {
    if (idRequest == null || idRequest.getId() <= 0) {
        throw new BusinessException(ErrorCode.PARAMS_ERROR);
    }
    // 判断是否存在
    long id = idRequest.getId();
    InterfaceInfo oldInterfaceInfo = interfaceInfoService.getById(id);
    if (oldInterfaceInfo == null) {
        throw new BusinessException(ErrorCode.NOT_FOUND_ERROR);
    }
    // 判断接口是否可以被调用
    com.fickler.apiclientsdk.model.User user = new com.fickler.apiclientsdk.model.User();
    user.setUsername("test");
    String usename = apiClient.getUsernameByPost(user);
    if (StringUtils.isBlank(usename)) {
        throw new BusinessException(ErrorCode.SYSTEM_ERROR, "接口验证失败");
    }
    InterfaceInfo interfaceInfo = new InterfaceInfo();
    interfaceInfo.setId(id);
    interfaceInfo.setStatus(InterfaceInfoStatusEnum.ONLINE.getValue());
    boolean result = interfaceInfoService.updateById(interfaceInfo);
    return ResultUtils.success(result);
}
```



设置一个上下线的枚举值



```java
package com.fickler.project.model.enums;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

/**
 * 帖子性别枚举
 *
 * @author yupi
 */
public enum InterfaceInfoStatusEnum {

    OFFLINE("关闭", 0),
    ONLINE("上线", 1);

    private final String text;

    private final int value;

    InterfaceInfoStatusEnum(String text, int value) {
        this.text = text;
        this.value = value;
    }

    /**
     * 获取值列表
     *
     * @return
     */
    public static List<Integer> getValues() {
        return Arrays.stream(values()).map(item -> item.value).collect(Collectors.toList());
    }

    public int getValue() {
        return value;
    }

    public String getText() {
        return text;
    }
}
```



#### 3. 下线接口业务逻辑

与发布接口业务逻辑完全相同，只不过是将数据库中的字段改为下线即可



```java
/**
 * 下线
 *
 * @param idRequest
 * @param request
 * @return
 */
@PostMapping("/offline")
@AuthCheck(mustRole = "admin")
public BaseResponse<Boolean> offlineInterfaceInfo(@RequestBody IdRequest idRequest,
                                                 HttpServletRequest request) {
    if (idRequest == null || idRequest.getId() <= 0) {
        throw new BusinessException(ErrorCode.PARAMS_ERROR);
    }
    // 判断是否存在
    long id = idRequest.getId();
    InterfaceInfo oldInterfaceInfo = interfaceInfoService.getById(id);
    if (oldInterfaceInfo == null) {
        throw new BusinessException(ErrorCode.NOT_FOUND_ERROR);
    }
    InterfaceInfo interfaceInfo = new InterfaceInfo();
    interfaceInfo.setId(id);
    interfaceInfo.setStatus(InterfaceInfoStatusEnum.OFFLINE.getValue());
    boolean result = interfaceInfoService.updateById(interfaceInfo);
    return ResultUtils.success(result);
}
```



### 三、前端页面开发

#### 1. 增加发布和下线按钮



找到对应页面 `InterfaceInfo` 目录下的 `index.tsx`，添加发布和下线按钮。



**是否可以同时展示发布和下线这两个按钮呢？**

最好不要同时展示，如果一个接口已经发布了，就应该只进行展示下线按钮。

这里需要添加一个判断，在渲染函数中，能够难道当前这条记录的 record ，也就是我们的接口信息，只需要对 record 的 status 进行判断就可以了。

![image-20230825121142099](http://qiniu.fickler.top/img/image-20230825121142099.png)



如何调用后端接口的函数呢？

利用 openapi 生成即可，前端不需要写任何接口调用方法，只需要执行 `openapi` 即可。

![image-20230825122214641](http://qiniu.fickler.top/img/image-20230825122214641.png)



增加发布和线下节点

```tsx
/**
 *  online node
 * @zh-CN 发布接口
 *
 * @param record
 */
const handleOnline = async (record: API.IdRequest) => {
  const hide = message.loading('正在删除');
  if (!record) return true;
  try {
    await onlineInterfaceInfoUsingPOST({
      id: record.id
    });
    hide();
    message.success('删除成功');
    actionRef.current?.reload();
    return true;
  } catch (error: any) {
    hide();
    message.error('删除失败,' + error.message);
    return false;
  }
};

/**
 *  Offline node
 * @zh-CN 下线接口
 *
 * @param record
 */
const handleOffline = async (record: API.IdRequest) => {
  const hide = message.loading('下线中');
  if (!record) return true;
  try {
    await offlineInterfaceInfoUsingPOST({
      id: record.id
    });
    hide();
    message.success('操作成功');
    actionRef.current?.reload();
    return true;
  } catch (error: any) {
    hide();
    message.error('删除失败,' + error.message);
    return false;
  }
};
```



测试一下发布和下线按钮

![image-20230825123923446](http://qiniu.fickler.top/img/image-20230825123923446.png)



![image-20230825123940747](http://qiniu.fickler.top/img/image-20230825123940747.png)



#### 2. 接口按钮同时显示的问题

> 前面设置的不能同时显示发布和下线按钮，但是好像点击发布和点击下线后，产生的效果并不是我们想要的，下面有两种解决办法：



1. **设置不同的 key**

发布和线下的按钮有 key 属性，默认是 config，设置成不一样即可。原因是 react 中，如果列表中的元素需要进行增删操作，需要为每个元素指定唯一的 key。这是因为 react 使用 key 来识别列表中每个元素，以便能够在更新列表元素的时候更加准确判断新旧元素的对应关系，从而提高渲染性能。

通过给发布和下线，设置不同的 key 是解决重复渲染问题的一种方法。通过设置不同的 key，可以让 react 在对新旧对别时会识别出这两个是不同的按钮，从而保证正确的渲染和更新。

![image-20230825124922882](http://qiniu.fickler.top/img/image-20230825124922882.png)





2. **修改条件渲染逻辑**

修改之前的代码，首先判断状态，如果是 0，就渲染发布按钮，如果是 1，就渲染下线按钮。根据不同的状态渲染不同的按钮，也可以实现。

![image-20230825125329106](http://qiniu.fickler.top/img/image-20230825125329106.png)



再测试一下

![image-20230825125408029](http://qiniu.fickler.top/img/image-20230825125408029.png)

![image-20230825125420349](http://qiniu.fickler.top/img/image-20230825125420349.png)



## 前端开发浏览接口页

### 一、创建浏览接口页

> 现在要新建一个给用户看的页面，之前我们是没有这个页面的

删掉 `admin.tsx`，在 `page` 目录下新建 `index` 目录



![image-20230828094301343](http://qiniu.fickler.top/img/image-20230828094301343.png)



将 `welcome.tsx` 复制到 `index` 目录下，并改名为 `index.tsx`

![image-20230828094656533](http://qiniu.fickler.top/img/image-20230828094656533.png)



在 `route.tx` 中修改路由，取消掉之前注释掉的欢迎页面，将其放到最上面，并修改为我们刚刚添加的页面路由

![image-20230828094642396](http://qiniu.fickler.top/img/image-20230828094642396.png)



访问测试一下

![image-20230828094731182](http://qiniu.fickler.top/img/image-20230828094731182.png)





### 二、修改浏览接口页

回到主页，删除多余的内容，留下 `PageContainer`

> `PageContainer`：是 ant design pro 提供的一个组件，可以帮助我们快速的构建页面，它提供了一些基本常用的布局和功能，如面包屑导航、页面标题、操作区域等，可以帮助我们快速的搭建页面的基本结构



![image-20230828101216478](http://qiniu.fickler.top/img/image-20230828101216478.png)



使用 ant design pro 组件库，依据自己的喜好构建页面，如下：

```tsx
import { listInterfaceInfoByPageUsingGET } from '@/services/api-backend/interfaceInfoController';
import { PageContainer } from '@ant-design/pro-components';
import { List, message } from 'antd';
import React, { useEffect, useState } from 'react';

const Index: React.FC = () => {
  const [loading, setLoading] = useState(false);
  const [list, setList] = useState<API.InterfaceInfo[]>([]);
  const [total, setTotal] = useState<number>(0);
  const loadData = async (current = 1, pageSize = 10) => {
    setLoading(true);
    try {
      const res = await listInterfaceInfoByPageUsingGET({
        current,
        pageSize,
      });
      setList(res?.data?.records ?? []);
      setTotal(res?.data?.total ?? 0);
    } catch (error: any) {
      message.error('请求失败,' + error.message);
      return false;
    }
    setLoading(false);
  };
  useEffect(() => {
    loadData();
  }, []);
  return (
    <PageContainer title="api在线接口开发平台">
      <List
        className="my-list"
        loading={loading}
        itemLayout="horizontal"
        dataSource={list}
        renderItem={(item) => {
          const apiLink = `/interface_info/${item.id}`;
          return (
            <List.Item actions={[<a key={item.id} href={apiLink}>查看</a>]}>
              <List.Item.Meta
                title={<a href={apiLink}>{item.name}</a>}
                description={item.description}
              />
            </List.Item>
          )
        }}
        pagination={{
          showTotal(total: number) {
            return '总数：' + total;
          },
          pageSize: 10,
          total,
          onChange(page, pageSize) {
            loadData(page, pageSize);
          },
        }}
      />
    </PageContainer>
  );
};

export default Index;
```



![image-20230828101538474](http://qiniu.fickler.top/img/image-20230828101538474.png)





## 前端开发接口文档页

### 一、创建浏览接口文档页

复制 `Index` 目录到 `page` 目录下，并改名为 `InterfaceInfo`



![image-20230828101749395](http://qiniu.fickler.top/img/image-20230828101749395.png)



去 `route.ts` 中添加动态路由，给这个新增的路由添加两个参数

> 动态路由：[路由 (umijs.org)](https://umijs.org/docs/guides/routes#动态路由)

- 让这个路由可以接收动态参数，在点击查看之后跳转到对应的接口页面，通过接口 id 来区分不同的接口。
  - `path:'interface_info/:id'`：定义了路由的路径，其中 `id` 是一个参数占位符，表示在实际路径中可以传递一个具体的 ID 值作为参数。
- 让这个页面在菜单栏中隐藏，查看接口不需要放在菜单栏中。
  - `hideInMenu:true`：表示该路由在菜单中是否隐藏，如果设置为 true，在表示该路由不会在菜单栏中显示。



![image-20230828111411151](http://qiniu.fickler.top/img/image-20230828111411151.png)



```tsx
{
  path: '/interface_info/:id',
  name: '查看接口',
  icon: 'smile',
  component: './interfaceInfo',
  hideInMenu: 'true'
},
```



接下来的事情就是点击 **查看** 后读到这条数据的 id，然后跳转到对应的页面

去修改 `Index` 下的 `index.tsx` 中的超链接

![image-20230828104424083](http://qiniu.fickler.top/img/image-20230828104424083.png)



```tsx
renderItem={(item) => {
  const apiLink = `/interface_info/${item.id}`;
  return (
    <List.Item actions={[<a key={item.id} href={apiLink}>查看</a>]}>
      <List.Item.Meta
        title={<a href={apiLink}>{item.name}</a>}
        description={item.description}
      />
    </List.Item>
  )
}}
```



测试一下，观察 url 地址

![image-20230828104526327](http://qiniu.fickler.top/img/image-20230828104526327.png)



### 二、修改浏览接口文档页

现在要改一下查看单个接口文档的页面，需要从 url 中拿到这个接口的 id，然后才能知道需要加载哪个接口的信息。

**怎么拿到 url 中的参数呢？**



![image-20230828104759193](http://qiniu.fickler.top/img/image-20230828104759193.png)



使用 `useMatch` 来获取，修改接口文档页

```tsx
import {
} from '@/services/api-backend/interfaceInfoController';
import { PageContainer } from '@ant-design/pro-components';
import React, { useEffect, useState } from 'react';
import {useMatch} from "react-router";

const Index: React.FC = () => {
  const [loading, setLoading] = useState(false);
  const [data, setData] = useState<API.InterfaceInfo>();
  const match = useMatch('/interface_info/:id')
  alert(JSON.stringify(match))
  const loadData = async (current = 1, pageSize = 10) => {
    // setLoading(true);
    // try {
    //   const res = await getInterfaceInfoByIdUsingGET(id);
    //   setData(res.data);
    // } catch (error: any) {
    //   message.error('请求失败,' + error.message);
    // }
    // setLoading(false);
  };
  useEffect(() => {
    loadData();
  }, []);
  return (
    <PageContainer title="查看接口文档">
    </PageContainer>
  );
};

export default Index;
```



点击查看，测试一下

![image-20230828112910072](http://qiniu.fickler.top/img/image-20230828112910072.png)



可以看到，使用 `useMatch` 可以获取到整个页面的信息，但是我们就只需要获取动态路由中 id 信息。

官网还提供了一个 `useParams` 钩子函数，通过使用 `useParams` 我们可以轻松的获取动态路由中的参数值，而不必关心整个页面路径的其他细节。

![image-20230828113258217](http://qiniu.fickler.top/img/image-20230828113258217.png)



我们当然是选择使用 `useParams`，重新修改查看接口信息页面

```tsx
import {
  getInterfaceInfoByIdUsingGET
} from '@/services/api-backend/interfaceInfoController';
import { PageContainer } from '@ant-design/pro-components';
import React, { useEffect, useState } from 'react';
import {useParams} from "react-router";
import {message} from "antd";

const Index: React.FC = () => {
  const [loading, setLoading] = useState(false);
  const [data, setData] = useState<API.InterfaceInfo>();
  // 使用 useParams 钩子函数动态获取路由参数
  const params = useParams();
  const loadData = async (current = 1, pageSize = 10) => {
    if (!params.id) {
      message.error('参数不存在')
      return
    }
    setLoading(true);
    try {
      const res = await getInterfaceInfoByIdUsingGET({
        id: Number(params.id)
      });
      setData(res.data);
    } catch (error: any) {
      message.error('请求失败,' + error.message);
    }
    setLoading(false);
  };
  useEffect(() => {
    loadData();
  }, []);
  return (
    <PageContainer title="查看接口文档">
      {
        JSON.stringify(data)
      }
    </PageContainer>
  );
};

export default Index;
```



再次测试一下

![image-20230828113717211](http://qiniu.fickler.top/img/image-20230828113717211.png)



### 三、美化页面

接下来对查看接口文档页面进行美化，使用卡片来进行包裹。

![image-20230828114001733](http://qiniu.fickler.top/img/image-20230828114001733.png)

![image-20230828113947895](http://qiniu.fickler.top/img/image-20230828113947895.png)



将内容使用列表的形式展示，去官网找一下描述列表组件：[描述列表 Descriptions - Ant Design](https://ant.design/components/descriptions-cn/)

![image-20230828231821219](http://qiniu.fickler.top/img/image-20230828231821219.png)



复制代码

```tsx
import { Descriptions } from 'antd';
import React from 'react';

const App: React.FC = () => (
  <Descriptions title="User Info">
    <Descriptions.Item label="UserName">Zhou Maomao</Descriptions.Item>
    <Descriptions.Item label="Telephone">1810000000</Descriptions.Item>
    <Descriptions.Item label="Live">Hangzhou, Zhejiang</Descriptions.Item>
    <Descriptions.Item label="Remark">empty</Descriptions.Item>
    <Descriptions.Item label="Address">
      No. 18, Wantang Road, Xihu District, Hangzhou, Zhejiang, China
    </Descriptions.Item>
  </Descriptions>
);

export default App;
```



复制到查看接口页面

![image-20230828231952276](http://qiniu.fickler.top/img/image-20230828231952276.png)



做一个判断，如果接口存在就展示列表，否则显示接口不存在

![image-20230828232354535](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20230828232354535.png)



依据接口信息来修改列表

![image-20230828232756757](http://qiniu.fickler.top/img/image-20230828232756757.png)



```tsx
<PageContainer title="查看接口文档">
  <Card>
    {!data ? (
      <>接口不存在</>
    ) : (
      <Descriptions title={data.name}>
        <Descriptions.Item label="接口状态">{data.status}</Descriptions.Item>
        <Descriptions.Item label="描述">{data.description}</Descriptions.Item>
        <Descriptions.Item label="请求地址">{data.url}</Descriptions.Item>
        <Descriptions.Item label="请求方法">{data.method}</Descriptions.Item>
        <Descriptions.Item label="请求头">{data.requestHeader}</Descriptions.Item>
        <Descriptions.Item label="响应头">{data.responseHeader}</Descriptions.Item>
        <Descriptions.Item label="创建时间">{data.createTime}</Descriptions.Item>
        <Descriptions.Item label="更新时间">{data.updateTime}</Descriptions.Item>
      </Descriptions>
    )}
  </Card>
</PageContainer>
```



查看一下显示的情况

![image-20230828233040774](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20230828233040774.png)



修改为一行仅显示一条记录，增加 `column = {1}`

![image-20230828233130018](http://qiniu.fickler.top/img/image-20230828233130018.png)



接口状态的展示是 `0` 和 `1`，修改为对应的 `开启` 和 `关闭`

![image-20230828233256739](http://qiniu.fickler.top/img/image-20230828233256739.png)

![image-20230828233318176](http://qiniu.fickler.top/img/image-20230828233318176.png)



## 后端开发申请签名

### 一、开发申请签名

现在用户已经可以看到这个接口和这个接口的文档了，接下来就是调用这个接口了

调用接口前必须要分配一个签名，上次我们自己设置了一个签名和密钥，看一下数据库

![image-20230828233710435](http://qiniu.fickler.top/img/image-20230828233710435.png)



现在我们可以给每个注册的用户自动分配一个签名和密钥，去修改一下注册流程

在插入数据前分配一个 `ak` 和 `sk`，使插入数据变成第四步

怎么随机分配呢？只要用加密算法生成即可

![image-20230828235212186](http://qiniu.fickler.top/img/image-20230828235212186.png)



```java
// 3. 分配 ak 和 sk
String accessKey = DigestUtil.md5Hex(SALT + userAccount + RandomUtil.randomNumbers(5));
String secretKey = DigestUtil.md5Hex(SALT + userAccount + RandomUtil.randomNumbers(8));
```



将得到的值设置给用户，在 `user` 中添加 `ak` 和 `sk` 两个字段

![image-20230828235358723](http://qiniu.fickler.top/img/image-20230828235358723.png)



在 `UserMapper.xml` 也添加上 `ak` 和 `sk`

![image-20230828235540607](http://qiniu.fickler.top/img/image-20230828235540607.png)



将值设置给 `user` 

![image-20230828235634353](http://qiniu.fickler.top/img/image-20230828235634353.png)



重启后端项目，注册一个用户测试一下

![image-20230828235850812](http://qiniu.fickler.top/img/image-20230828235850812.png)



去数据库里查看一下数据

![image-20230828235917578](http://qiniu.fickler.top/img/image-20230828235917578.png)



### 二、创建真实数据

创建一个真实的接口数据。

![image-20230831212554787](http://qiniu.fickler.top/img/image-20230831212554787.png)



点击提交后，查看请求，它把双引号内的文本进行了转译

![image-20230831212909845](http://qiniu.fickler.top/img/image-20230831212909845.png)



去数据库查看一下数据

![image-20230831212954877](http://qiniu.fickler.top/img/image-20230831212954877.png)



回到前端页面查看一下数据

![image-20230831213046140](http://qiniu.fickler.top/img/image-20230831213046140.png)

![image-20230831213103365](http://qiniu.fickler.top/img/image-20230831213103365.png)









### 三、补充参数

发现接口信息里面没有请求参数，去后端补充一下

![image-20230831213151278](http://qiniu.fickler.top/img/image-20230831213151278.png)



更新一下 `db.sql` 的字段

![image-20230831213453418](http://qiniu.fickler.top/img/image-20230831213453418.png)



```sql
create table if not exists api.`interface_info`
(
    `id` bigint not null auto_increment comment '主键' primary key,
    `name` varchar(256) not null comment '用户名',
    `description` varchar(256) null comment '描述',
    `url` varchar(512) not null comment '接口地址',
    `requestParams` text not null comment '请求参数',
    `requestHeader` text null comment '请求头',
    `responseHeader` text null comment '响应头',
    `userId` varchar(256) not null comment '创建人',
    `status` int default 0 not null comment '接口状态（0 - 关闭， 1 - 开启））',
    `method` varchar(256) not null comment '请求类型',
    `createTime` datetime default CURRENT_TIMESTAMP not null comment '创建时间',
    `updateTime` datetime default CURRENT_TIMESTAMP not null on update CURRENT_TIMESTAMP comment '更新时间',
    `isDelete` tinyint default 0 not null comment '是否删除(0-未删, 1-已删)'
    ) comment '接口信息表';
```



给接口信息表添加字段

![image-20230831213658049](http://qiniu.fickler.top/img/image-20230831213658049.png)



将所有和接口信息相关的地方补充上新增的参数

![image-20230831213959642](http://qiniu.fickler.top/img/image-20230831213959642.png)



![image-20230831214030828](http://qiniu.fickler.top/img/image-20230831214030828.png)



![image-20230831214053245](http://qiniu.fickler.top/img/image-20230831214053245.png)



![image-20230831214136423](http://qiniu.fickler.top/img/image-20230831214136423.png)



重启后端项目

![image-20230831214244631](http://qiniu.fickler.top/img/image-20230831214244631.png)



回到前端，执行 `openapi`，重新生成

![image-20230831214332031](http://qiniu.fickler.top/img/image-20230831214332031.png)



在查看接口文档页补充请求参数

![image-20230831214446219](http://qiniu.fickler.top/img/image-20230831214446219.png)



修改列的定义

![image-20230831214605274](http://qiniu.fickler.top/img/image-20230831214605274.png)



把请求头和响应的 `textarea` 改为 `jsonCode`

![image-20230831214650445](http://qiniu.fickler.top/img/image-20230831214650445.png)



回到前端页面，点击 `修改`

![image-20230831215838389](http://qiniu.fickler.top/img/image-20230831215838389.png)



请求参数不应该只传递一个参数值，还应该告诉后端该参数的类型（直接使用 json 类型，更灵活）

```json
[
	{"name": "username", "type": "string"}
]
```



复制到请求参数里面

![image-20230831220756141](http://qiniu.fickler.top/img/image-20230831220756141.png)



点击提交后失败

![image-20230831220914121](http://qiniu.fickler.top/img/image-20230831220914121.png)



在前端页面提交表单的时候要添加上 `id` 值

![image-20230831221647434](http://qiniu.fickler.top/img/image-20230831221647434.png)



注意，我们重新 `openapi` 生成了一次，记得把信息改为 `interfaceInfo`

![image-20230831221739462](http://qiniu.fickler.top/img/image-20230831221739462.png)



再提交测试一下

![image-20230831221814601](http://qiniu.fickler.top/img/image-20230831221814601.png)





## 开发在线调用

### 一、前端增加调用按钮

模拟假接口的数据已经造好，现在在接口信息查看页面上增加一个调用按钮，与 `Swagger` 文档类似，只不过我们的信息已经拥有了，只需要输入一个请求参数即可

![image-20230831222119616](http://qiniu.fickler.top/img/image-20230831222119616.png)



在查看接口文档页的描述列表下面新增一个卡片

![image-20230831222446194](http://qiniu.fickler.top/img/image-20230831222446194.png)



去官网找一个现成的表单

![image-20230831222627724](http://qiniu.fickler.top/img/image-20230831222627724.png)



将表单代码复制到卡片中，并删除多余的代码

```tsx
<Card>
  <Form
    name="basic"
    onFinish={onFinish}
  >
    <Form.Item
      label="Username"
      name="username"
    >
     <Input.TextArea />
    </Form.Item>
    <Form.Item wrapperCol={{ offset: 8, span: 16 }}>
      <Button type="primary" htmlType="submit">
        Submit
      </Button>
    </Form.Item>
  </Form>
</Card>
```



将官网的 `onFinish` 复制过来

```tsx
const onFinish = (values: any) => {
  console.log('Success:', values);
};
```



修改表单

![image-20230831223410789](http://qiniu.fickler.top/img/image-20230831223410789.png)



```tsx
<Card>
  <Form
    name="basic"
    layout="vertical"
    onFinish={onFinish}
  >
    <Form.Item
      label="请求参数"
      name="userRequestParams"
    >
      <Input.TextArea />
    </Form.Item>
    <Form.Item wrapperCol={{ span: 16 }}>
      <Button type="primary" htmlType="submit">
        调用
      </Button>
    </Form.Item>
  </Form>
</Card>
```



回到前端查看效果

![image-20230831223455997](http://qiniu.fickler.top/img/image-20230831223455997.png)



### 二、调用流程讲解

接下来要实际让后端去调用接口，所以需要开发一个在线调用接口的后端：

思考一下如何在后端处理这个在线调用的问题。首先，我们要考虑的是用户的请求是不固定的，每个接口的请求参数和请求头都可能是不同的。**如何将这些请求传递给真实的第三方接口呢？**

现有的项目：前端项目、接口平台的后端项目、提供给开发者的模拟接口项目

两种方案：

1. 前端调用调用后，先请求接口平台后端，然后在由后端调用模拟接口
2. 前端绕过后端，直接调用模拟接口



![image-20230831225903033](http://qiniu.fickler.top/img/image-20230831225903033.png)



在实际的开发中，选择第二种方式不太可能的。原因在于，如果模拟接口可以直接被调用，那么存在安全风险。通常情况下，前端虽然可以直接调用模拟接口，但我们不会将模拟接口暴露给外部，而是将其隐藏起来。用户或开发者在调用时可能根本不知道模拟接口的地址。假设模拟接口的地址是 `aaa.com/api`，后端地址是 `bbb.cpm/api`，而 `aaa.com/api` 并不对用户开发，用户根本不知道它的存在。

为了更规范和更安全，以及方便统计，建议使用后端调用的方式。这种方式更加规范和安全，还可以隐藏接口地址。如果直接将模拟接口完全开放给用户，那么后续的网关和计费工作都可能会徒劳无功。因为对方可以直接请求到你的模拟接口。当然，你可能还需为模拟接口提供一些特殊保障，所以推荐使用第一种方式。

![image-20230831230449309](http://qiniu.fickler.top/img/image-20230831230449309.png)





**调用流程**

1. 前端将用户输入的请求参数和测试接口的 `id` 发送给后端
2. 在调用前进行一些校验
3. 后端调用模拟接口



### 三、后端开发在线调用

#### 1. 开发测试接口

开发一个测试接口试一下，复制一份 `InterfaceInfoController` 中的下线接口

![image-20230831231215102](http://qiniu.fickler.top/img/image-20230831231215102.png)



给这个接口封装一个对象

复制 `interfaceInfo` 下的 `InterfaceInfoUpdateRequest`，并改名为 `InterfaceInfoInvokeRequest`

![image-20230831231843451](http://qiniu.fickler.top/img/image-20230831231843451.png)



然后修改一下参数

```java
package com.fickler.project.model.dto.interfaceinfo;

/**
 * @author dell
 * @date 2023/8/31 23:14
 */

@Data
public class InterfaceInfoInvokeRequest {

    /**
     * 主键
     */
    private Long id;


    /**
     * 用户请求参数
     */
    private String useRequestParams;


    private static final long serialVersionUID = 1L;

}
```



将刚刚复制的下线接口修改为测试接口

```java
/**
 * 测试接口
 * @param interfaceInfoInvokeRequest
 * @param request
 * @return
 */
@PostMapping("/invoke")
@AuthCheck(mustRole = "admin")
public BaseResponse<Object> invokeInterfaceInfo(@RequestBody InterfaceInfoInvokeRequest interfaceInfoInvokeRequest,
                                                  HttpServletRequest request) {
    if (interfaceInfoInvokeRequest == null || interfaceInfoInvokeRequest.getId() <= 0) {
        throw new BusinessException(ErrorCode.PARAMS_ERROR);
    }
    // 获取用户id
    long id = interfaceInfoInvokeRequest.getId();
    // 获取用户请求参数
    String useRequestParams = interfaceInfoInvokeRequest.getUseRequestParams();
    // 判断是否存在
    InterfaceInfo oldInterfaceInfo = interfaceInfoService.getById(id);
    if (oldInterfaceInfo == null) {
        throw new BusinessException(ErrorCode.NOT_FOUND_ERROR);
    }
    // 检查接口状态是否为下线状态
    if (oldInterfaceInfo.getStatus() == InterfaceInfoStatusEnum.OFFLINE.getValue()) {
        throw new BusinessException(ErrorCode.PARAMS_ERROR, "接口已关闭");
    }
}
```



在用户进行测试调用时，我们需要告知后端用户的签名信息，这样我们才能判断用户是否有权限进行接口调用。

有三种考虑方式，具体取决于用户体验的考虑：

1. 要求用户必须具有接口权限才能进行调用
2. 即使用户没有权限，也允许其进行调用，以便体验接口功能。如果选择进行体验，建议为用户分配临时的签名，类似于测试环境，给予一定数量的调用次数。这可能需要新增两个字段，例如在数据库中添加一个测试次数字段，稍微复杂一些。
3. 可直接为用户提供几十次调用机会，这样就简单了。假设你本来给用户提供了1万次调用次数，现在你可以直接给每个用户送50次。



再次修改测试接口的代码

```java
/**
 * 测试接口
 * @param interfaceInfoInvokeRequest
 * @param request
 * @return
 */
@PostMapping("/invoke")
@AuthCheck(mustRole = "admin")
public BaseResponse<Object> invokeInterfaceInfo(@RequestBody InterfaceInfoInvokeRequest interfaceInfoInvokeRequest,
                                                  HttpServletRequest request) {
    if (interfaceInfoInvokeRequest == null || interfaceInfoInvokeRequest.getId() <= 0) {
        throw new BusinessException(ErrorCode.PARAMS_ERROR);
    }
    // 获取用户id
    long id = interfaceInfoInvokeRequest.getId();
    // 获取用户请求参数
    String useRequestParams = interfaceInfoInvokeRequest.getUseRequestParams();
    // 判断是否存在
    InterfaceInfo oldInterfaceInfo = interfaceInfoService.getById(id);
    if (oldInterfaceInfo == null) {
        throw new BusinessException(ErrorCode.NOT_FOUND_ERROR);
    }
    // 检查接口状态是否为下线状态
    if (oldInterfaceInfo.getStatus() == InterfaceInfoStatusEnum.OFFLINE.getValue()) {
        throw new BusinessException(ErrorCode.PARAMS_ERROR, "接口已关闭");
    }
    // 获取当前用户的 ak 和 sk，这样相当于用户自己这个身份去调用，也不用担心它去刷接口，因为知道是谁刷的接口
    User loginUser = userService.getLoginUser(request);
    String accessKey = loginUser.getAccessKey();
    String secretKey = loginUser.getSecretKey();
    // 我们只需要进行测试调用，所以我们需要解析传递过来的参数
    Gson gson = new Gson();
    // 将用户请求参数转换为 com.fickler.apiclientsdk.model.User 对象
    com.fickler.apiclientsdk.model.User user = gson.fromJson(useRequestParams, com.fickler.apiclientsdk.model.User.class);
    String usernameByPost = apiClient.getUsernameByPost(user);
    // 返回成功响应，并包含调用结果
    return ResultUtils.success(usernameByPost);
}
```



这里有个问题，为什么 ak、sk 没有用到

![image-20230831234603976](http://qiniu.fickler.top/img/image-20230831234603976.png)



因为在 `application.yml` 中已经写死了

![image-20230831234648920](http://qiniu.fickler.top/img/image-20230831234648920.png)



因为我们要根据用户自己的 ak、sk 调用，所以这个地方就不用复用 `application` 了

![image-20230831234831072](http://qiniu.fickler.top/img/image-20230831234831072.png)



新建一个 `client`，要不然始终用的是管理员的账户、密码来测试

![image-20230831235044668](http://qiniu.fickler.top/img/image-20230831235044668.png)



然后重启后端

![image-20230831235318854](http://qiniu.fickler.top/img/image-20230831235318854.png)



前端执行 `openapi`，重新生成接口

![image-20230831235429708](http://qiniu.fickler.top/img/image-20230831235429708.png)



#### 2. 前端修改调用请求

在之前接口调用的地方添加一个真实向后端请求的功能，在点击调用的时候触发这个功能，也就是在 `onFinsh` 中处理

```tsx
const onFinish = (values: any) => {
  // 检查是否存在接口 id
  if (!params.id) {
    message.error('接口不存在');
    return;
  }
  try {
    // 发起接口调用请求，传入一个对象作为参数，这个对象包含id和values属性
    // 其中，id是从params中获取的，而values是函数的参数
    invokeInterfaceInfoUsingPOST({
      id: params.id,
      ...values,
    });
    message.success('请求成功');
  } catch (error: any) {
    message.error('操作失败' + error.message);
  }
};
```



拿到返回值后，要把这个结果回显出来，需要一个变量来存储，还有调用时的加载状态，来定义新的变量

![image-20230901000555463](http://qiniu.fickler.top/img/image-20230901000555463.png)



继续完善 `onFinish`

![image-20230901000844262](http://qiniu.fickler.top/img/image-20230901000844262.png)



```tsx
const onFinish = async (values: any) => {
  // 检查是否存在接口 id
  if (!params.id) {
    message.error('接口不存在');
    return;
  }
  setInvokeLoading(true);
  try {
    // 发起接口调用请求，传入一个对象作为参数，这个对象包含id和values属性
    // 其中，id是从params中获取的，而values是函数的参数
    const res = await invokeInterfaceInfoUsingPOST({
      id: params.id,
      ...values,
    });
    setInvokeRes(res.data);
    message.success('请求成功');
  } catch (error: any) {
    message.error('操作失败' + error.message);
  }
  setInvokeLoading(false);
};
```



页面还需要展示结果的地方

![image-20230901001049826](http://qiniu.fickler.top/img/image-20230901001049826.png)



发送一个请求测试一下

![image-20230901223408849](http://qiniu.fickler.top/img/image-20230901223408849.png)



美化一下，前端加个标题和分割线

![image-20230901223611848](http://qiniu.fickler.top/img/image-20230901223611848.png)



再次测试，查看效果

![image-20230901223655331](http://qiniu.fickler.top/img/image-20230901223655331.png)







## 开发接口调用次数的统计

### 一、接口调用次数的业务流程

**需求：**

1. 用户成功调用接口，次数 +1
2. 给用户分配或者用户自主申请接口调用次数



**业务流程：**

1. 用户调用接口（已完成）
2. 修改数据库，调用次数 +1



**进一步说明：**

进一步思考一下接口调用次数的统计。如果成功调用一次接口，那么调用次数就 +1，如果是因为网络原因导致的失败而不是参数参数错误等问，那么调用次数也是应该 +1。

![image-20230901233907111](http://qiniu.fickler.top/img/image-20230901233907111.png)

首先用户在前端看到接口，然后他要开通接口获取调用次数，获取调用次数后发起调用。通常情况下，我们只需完成调用即可，但现在在调用之后需要进行一次统计，即增加次数记录，这个步骤实际上是在进行统计次数的位置。我们只需要在接口调用成功后，在数据库中保存它的接口调用次数 +1 即可。

实际上，这是一个相当简单的流程。在原有的调用接口成功基础上，再添加一个统计调用次数的步骤。

还有一个额外的需求，就是考虑给用户分配或者用户自主申请接口调用次数。



### 二、设计库表

我们要设计一个新的表，既然要统计调用次数，所以要知道用户的 id 和接口的 id，所以用户和接口之间存在一个多对多的关系。

```mysql
create table if not exists api.`user_interface_info`
(
    `id` bigint not null auto_increment comment '主键' primary key,
    `userId` bigint not null comment '调用用户 id',
    `interfaceInfoId` bigint not null comment '接口 id',
    `totalNum` int default 0 not null comment '总调用次数',
    `leftNum` int default 0 not null comment '剩余调用次数',
    `status` int default 0 not null comment '接口状态（0 - 关闭， 1 - 开启））',
    `createTime` datetime default CURRENT_TIMESTAMP not null comment '创建时间',
    `updateTime` datetime default CURRENT_TIMESTAMP not null on update CURRENT_TIMESTAMP comment '更新时间',
    `isDelete` tinyint default 0 not null comment '是否删除(0-未删, 1-已删)'
) comment '用户调用接口关系';
```



### 三、mybatisX 插件生成代码

#### 1. 使用 mybatisX 插件

![image-20230902000808640](http://qiniu.fickler.top/img/image-20230902000808640.png)

![image-20230902000735068](http://qiniu.fickler.top/img/image-20230902000735068.png)



#### 2. 代码迁移

给实体对象中的 `isDelete` 加上 `@TableLogic` 注解，表示逻辑删除

![image-20230902204311746](http://qiniu.fickler.top/img/image-20230902204311746.png)



将 `UserInterfaceInfo` 移动到 `entity` 包下

![image-20230902204638830](http://qiniu.fickler.top/img/image-20230902204638830.png)



将 `UserInterfaceInfoMapper` 将移到 `mapper` 包下

![image-20230902204919003](http://qiniu.fickler.top/img/image-20230902204919003.png)



将 `UserInterfaceInfoServiceImpl` 移动到 `impl` 包下

将 `UserInterfaceInfoService` 移动到 `service` 包下

![image-20230902205257337](http://qiniu.fickler.top/img/image-20230902205257337.png)



在 `controller` 包下增加一个 `UserInterfaceInfoController.java`，直接复制 `InterfaceInfoController.java`

将里面的 `interfaceInfo` 替换为 `userInterfaceInfo`，`InterfaceInfo` 替换为 `UserInterfaceInfo`

![image-20230902210324656](http://qiniu.fickler.top/img/image-20230902210324656.png)



#### 3. 修改代码逻辑

为了保证接口调用次数的正确性和准确率，应该将增加接口调用次数的逻辑放在接口成功调用之后。

![image-20230902215557431](http://qiniu.fickler.top/img/image-20230902215557431.png)



像更新接口功能，可能会增加管理员增加用户接口调用次数的功能

![image-20230902221351653](http://qiniu.fickler.top/img/image-20230902221351653.png)



开发添加用户接口调用关系的请求，把这些请求类都建好

![image-20230902221723219](http://qiniu.fickler.top/img/image-20230902221723219.png)

![image-20230902221735523](http://qiniu.fickler.top/img/image-20230902221735523.png)

![image-20230902221745876](http://qiniu.fickler.top/img/image-20230902221745876.png)



在 `dto` 包下新建 `userinterfaceinfo` 包

![image-20230902221916282](http://qiniu.fickler.top/img/image-20230902221916282.png)



将 `interfaceinfo` 中的添加、查询、更新请求复制到 `userinterfaceinfo` 包下，并在请求前面加上 `User`

![image-20230902222351813](http://qiniu.fickler.top/img/image-20230902222351813.png)



> **通过用户限制 QPS 的思路？**
>
> 很简单，既然我们已经能够统计用户调用接口的次数，我们再统计用户调用接口的调用时间，然后根据用户在某一个时间段内的调用次数来限制。例如，我们可以每隔一秒统计一次用户的接口调用次数，如果用户在这一秒内的调用次数超过了某个设定的频率，那么我们就给他禁用调用接口调用。



将添加、查询、更新请求中的字段修改为 `UserInterfaceInfo` 内的字段

![image-20230902223309112](http://qiniu.fickler.top/img/image-20230902223309112.png)

![image-20230902224148014](http://qiniu.fickler.top/img/image-20230902224148014.png)

![image-20230902224231854](http://qiniu.fickler.top/img/image-20230902224231854.png)



回到 `UserInterfaceInfoController` 中，导入相应的请求

![image-20230902224836307](http://qiniu.fickler.top/img/image-20230902224836307.png)



然后将其中的下线、发布接口、测试接口删掉

![image-20230902225034389](http://qiniu.fickler.top/img/image-20230902225034389.png)



补充一下这个方法

![image-20230902230444296](http://qiniu.fickler.top/img/image-20230902230444296.png)



复制之前现成的方法

![image-20230903102800714](http://qiniu.fickler.top/img/image-20230903102800714.png)



将之前的 `InterfaceInfo` 替换为 `UserInterfaceInfo`，将 `interfaceInfo` 替换为 `userInterfaceInfo`

![image-20230903103250879](http://qiniu.fickler.top/img/image-20230903103250879.png)



复制 `InterfaceInfoServiceImpl` 里的内容到 `UserInterfaceInfoServiceImpl`，并修改一下名称

![image-20230903103629880](http://qiniu.fickler.top/img/image-20230903103629880.png)



之前的逻辑内容是校验接口名称，现在修改为校验接口次数即可

![image-20230903104021047](http://qiniu.fickler.top/img/image-20230903104021047.png)



回到 `UserInterfaceInfoController` 里给接口都加上管理员权限（使用注解）

非管理员不能随便添加调用次数

![image-20230903104305786](http://qiniu.fickler.top/img/image-20230903104305786.png)



非管理员不能随便删除调用次数

![image-20230903104338165](http://qiniu.fickler.top/img/image-20230903104338165.png)



非管理员不能随便更新调用次数

![image-20230903104410894](http://qiniu.fickler.top/img/image-20230903104410894.png)



根据 `id` 获取接口、列表、分页列表都是管理员才能使用

![image-20230903104547148](http://qiniu.fickler.top/img/image-20230903104547148.png)

![image-20230903104555338](http://qiniu.fickler.top/img/image-20230903104555338.png)

![image-20230903104604484](http://qiniu.fickler.top/img/image-20230903104604484.png)



### 四、实现用户调用次数加一

#### 1. 调用成功

接下来要做的事情就是用户每调用成功一次接口，次数就要 +1

现在有一个问题，当用户调用成功后，要么是调用 `addUserInterfaceInfo` 将这条调用记录添加到数据库中，要么是调用 `updateUserInterfaceInfo` 在原有的基础上 +1

所以现在有两种情况：

1. 第一种情况是用户没有这个调用记录，那么我们需要创建一条新的记录
2. 第二种情况是用户已经有了调用记录，那么我们需要在现有的次数上 +1



#### 2. 步骤

1. 开发基本的增删改查（管理员用）
2. 开发用户调用次数 +1 的功能（service）



#### 3. 实现调用接口次数加一

在 `UserInterfaceInfoService` 中补充次数加一的功能

![image-20230903111525525](http://qiniu.fickler.top/img/image-20230903111525525.png)



实现这个方法

![image-20230903111558093](http://qiniu.fickler.top/img/image-20230903111558093.png)



实现代码逻辑

```java
@Override
public boolean invokeCount(long interfaceInfoId, long userId) {
    // 判断(其实这里还应该校验存不存在，这里就不用校验了，因为它如果不存在，也更新不到那条记录)
    if (interfaceInfoId <= 0 || userId <= 0) {
        throw new BusinessException(ErrorCode.PARAMS_ERROR);
    }
    // 使用 UpdateWrapper 对象构建更新条件
    UpdateWrapper<UserInterfaceInfo> updateWrapper = new UpdateWrapper<>();
    updateWrapper.eq("interfaceInfoId", interfaceInfoId);
    updateWrapper.eq("userId", userId);
    // setSql 方法用户设置要更新的 SQL 语句。这里通过 SQL 表达式实现了两个字段的更新操作
    // leftNum = leftNum - 1 和 totalNum = totalNum + 1
    // 意思是将 leftNuM 字段减一，totalNum 字段加一
    updateWrapper.setSql("leftNum = leftNum - 1, totalNum = totalNum + 1");
    return this.update(updateWrapper);
}
```



**// todo**

需要注意的是，由于用户可能会瞬间调用大量接口，为了避免统计出错，需要涉及到事务和锁的知识。

在这种情况下，如果我们是在分布式环境中运行的，那么可能需要使用分布式锁来保证数据的一致性。

事务是一组操作，要么全部成功，要么全部失败回滚。在这个场景中，我们希望在更新用户接口信息的时候，保证原子性，即要么用户接口信息全部更新成功，要么全部不更新。

锁的作用是为了防止多线程或多进程同时修改同一个数据，造成数据不一致的情况。在分布式环境中，我们需要使用分布式锁来确保在多个节点上对数据的访问时互斥的。



在 `user_interface_info` 表中插入一条假数据

![image-20230903113052091](http://qiniu.fickler.top/img/image-20230903113052091.png)



使用 sql 来测试一下刚刚编写的 sql 语句

![image-20230903113313693](http://qiniu.fickler.top/img/image-20230903113313693.png)



更新成功，说明我们的 sql 语句没有问题

![image-20230903113350006](http://qiniu.fickler.top/img/image-20230903113350006.png)



创建一个测试类

![image-20230903113434700](http://qiniu.fickler.top/img/image-20230903113434700.png)

![image-20230903113503786](http://qiniu.fickler.top/img/image-20230903113503786.png)



编写单元测试类

```java
package com.fickler.project.service;

import org.junit.Test;
import org.junit.jupiter.api.Assertions;
import org.springframework.boot.test.context.SpringBootTest;

import javax.annotation.Resource;

import static org.junit.Assert.*;

/**
 * @author dell
 * @date 2023/9/3 11:35
 */

@SpringBootTest
public class UserInterfaceInfoServiceTest {

    @Resource
    private UserInterfaceInfoService userInterfaceInfoService;

    @Test
    public void invokeCount() {

        boolean b = userInterfaceInfoService.invokeCount(1L, 1L);
        Assertions.assertTrue(b);

    }
}
```



运行测试类

![image-20230903180538873](http://qiniu.fickler.top/img/image-20230903180538873.png)



查看数据库表

![image-20230903180619988](http://qiniu.fickler.top/img/image-20230903180619988.png)



下一步就是在用户调用每个接口成功之后，是不是就要开始调用方法 `invokeCount`，然后给当前接口的次数 +1。

但是现在有问题了，现在的需求是在用户调用每个接口成功后，需要调用 `invokeCount` 方法，来给当前接口的调用次数 +1，但是这样会带来一个问题：如果每个方法调用成功后，返回结果之前都要调用一次 `invokeCount` 方法，会显得非常繁琐。



### 五、AOP 切面

#### 1. 思考

**问题：**如果每个接口的方法调用次数 +1，是不是比较麻烦？

**致命问题：**接口开发者需要自己去添加统计代码。

![image-20230904090520874](http://qiniu.fickler.top/img/image-20230904090520874.png)

**AOP 切面的优点：**独立于接口，在每个接口调用后统计次数 +1

**AOP 切面的缺点：**只存在于单个项目中，如果每个团队都要开发自己的模拟接口，那么都要写一个切面



**进一步说明：**

我们现在面临一个问题：我们有很多接口A、接口B、接口C等，每个接口都要需要调用成功后进行调用次数 +1 的操作。如果我们每个即可的实现都手动去添加这个统计次数逻辑，那将非常繁琐，而且容易出错。

为了解决这个问题，我们需要将统计次数的逻辑抽取出来，让它称为一个通用的功能，最大的问题是对于接口提供者来说，他们是无感知的，会出现一些问题。在这里，我们可以使用 AOP（面向切面编程）来实现这个功能。

AOP 允许我们在原有业务逻辑的基础上，增加额外的操作，而不需要改动原有代码。

具体来说，我们可以通过 AOP 切面、拦截器或者过滤器来实现这个统计次数的逻辑。在接口调用成功后，AOP 切面或拦截器可以自动触发调用次数 +1 的方法，从而实现统一的统计功能。

![image-20230904091240183](http://qiniu.fickler.top/img/image-20230904091240183.png)

> 其实简单一点也可以使用一个通用的方法来进行统计调用次数。
>
> AOP 切面是推荐方法，它可以将统计次数的逻辑从业务逻辑中解耦出来，并实现统一处理。





#### 2. 演示

在 `api-interface` 中新建 `aop` 包

![image-20230904091621902](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20230904091621902.png)



在 `aop` 包下新建 `InvokeCountAOP`

![image-20230904091733541](http://qiniu.fickler.top/img/image-20230904091733541.png)



写一下伪代码

![image-20230904092553869](http://qiniu.fickler.top/img/image-20230904092553869.png)



理解 AOP 的作用：

它可以在调用某个接口前或者后，帮助我们执行一些额外的操作，比如统计调用次数，它的本质底层的实现就是一个动态代理。可以理解为在调用每个接口后，执行一段代码逻辑。





#### 3. 讲解

在我们当前的项目中，我们需要实现的统计功能是涉及多个项目之间的调用，而不仅仅是单个项目内的统计。虽然 AOP 切面是一个不错的解决方法，但它有一个缺点：它是独立于单个项目的，每个项目都需自己实现统计逻辑，并引入相应的 `AOP 切面` 包。

考虑到我们的项目架构，我们希望实现一种通用的统计方法，可以统一处理所有项目的接口调用情况。因此，我们决定采用网关来实现这个功能。

修改刚刚的架构图：

![image-20230904093541524](http://qiniu.fickler.top/img/image-20230904093541524.png)

如果按照之前 AOP 切面的接口方法，项目 A 中我们要引入 AOP 切面并进行编程，对于项目 B 我们也要引入 AOP 切面并进行编程。这样的做法会导致每个开发者都要关注统计功能的实现，需要引入一些代码并编写相应的逻辑。

为了避免这种情况，将统计次数的功能再抽出来一层。我们可以将统计次数的逻辑放在一个公共的位置，就像进入火车站一样，无论你称作哪趟列车，都需要经过这个统一的检票口。同样的，无论哪个模拟接口被调用，都会经过这个统一的统计次数逻辑。

![image-20230904094125791](http://qiniu.fickler.top/img/image-20230904094125791.png)



只要在同一个项目或者同一层级中，存在相似或者重复的东西，我们都可以将其抽象成一层，将其往上或往后抽出。这个抽象过程就是我们的网关。网关位于项目的最前面，统一处理不同项目之间的请求，是一个不断抽象的过程。

网关：就像刚提到的火车站，我们在进站前必须要通过一个统一的检票口，这个检票口就是网关。

现在同理，假设我是一个小用户，我想要调用接口 A。如果没有网关，我们要这样操作：先调用接口 A，然后接口 A 再调用统计次数方法，接着调用接口 B，再去调用统计次数方法...... 现在用户直接调用网关，由网关负责根据用户请求的地址，找到对应的接口，比如接口 A，然后调用接口 A，并在调用统计次数 +1。同样，用户调用接口 B 或接口 C，也是先调用网关，然后网关在去找对应的接口并进行调用。

现在有一个问题，用户是否需要关心自己调用的接口是由项目 A 或团队 A 开发的，还是团队 B 开发的？实际上，用户根本不需要关心。它只需要知道自己需要什么功能，然后调用对应的网关即可。网关负责找到对应的接口并返回结果。对于开发者来说，他们也不需要关心统计次数。只要把自己的接口接入到网关中，让网关能找到并调用即可，网关会自动帮他们统计次数。

通过这种设计，我们实现了一个统一的网关来处理不同项目的请求，用户和开发者都不需要关心具体的细节，简化了操作，提高了系统的可用性和可维护性。

![image-20230904102139678](http://qiniu.fickler.top/img/image-20230904102139678.png)



### 六、网关

#### 1. 网关讲解

**作用：**统一去进行一些操作、处理一些问题。

**比如：**

1. 路由
2. 负载均衡
3. 统一鉴权
4. 跨域
5. 统一业务处理（缓存）
6. 访问控制
7. 发布控制
8. 流量染色
9. 接口保护
   1. 限制请求
   2. 信息脱敏
   3. 降级（熔断）
   4. 限流：学习令牌桶算法、学习漏捅算法，学习一下 RedisLimitHandler
10. 统一日志
11. 统一文档



###### 简略说明

**路由：**起到转发的作用，比如有接口 A 和接口 B，网关会记录这些信息，根据用户访问的地址参数，转发请求到对应的接口（服务器 / 集群）

/a => 接口 A

/b => 接口B

参考文档：[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-after-route-predicate-factory)



**负载均衡：**在路由的基础上

/c => 服务 A / 集群 A（随机转发到其中某一个机器）

uri 从固定地址改为 ib:xxxx



**统一处理跨域：**网关统一处理跨域，不用在单个项目里单独处理

参考文档：[qps - 搜索 (bing.com)](https://cn.bing.com/search?q=qps&cvid=6fabeae6c963432da93fee14b9e5c25a&aqs=edge..69i57j0l8.1552j0j1&FORM=ANAB01&PC=U531)



**发布控制：**灰度发布，比如上线新街口，先给新接口分配 20% 的流量，老街口 80%，再慢慢调整比重。

参考文档：[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-weight-route-predicate-factory)



**流量染色：**给请求（流量）添加一些标识，一般是设置请求头，添加新的请求头

参考文档：[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-addrequestheader-gatewayfilter-factory)

全局染色：[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#default-filters)



**统一接口保护：**

1. 限制请求：[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#requestheadersize-gatewayfilter-factory)
2. 信息脱敏：[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-removerequestheader-gatewayfilter-factory)
3. 降级（熔断）：[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#fallback-headers)
4. 限流：[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-requestratelimiter-gatewayfilter-factory)
5. 超时时间：[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#http-timeouts-configuration)
6. 重试（业务保护）：[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-retry-gatewayfilter-factory)



**统一业务处理：**把每个项目中都要做的通用逻辑放到上层（网关），比如这个项目中的次数统计

**统一鉴权：**判断用户是否有权限访问，对于每个接口都统一进行权限判断，不用重复写

**访问控制：**黑白名单，比如 DDOS IP

**统一日志：**统一的请求、响应信息

**统一文档：**将下游文档进行聚合，统一在一个页面进行查看

建议用：knife4j [1.1 介绍 | Knife4j (xiaominfo.com)](https://doc.xiaominfo.com/docs/middleware-sources/aggregation-introduction)



###### 详细说明

什么是网关？可以将网关看做是网络端口，类似于火车站或者机场的检票口，它负责统一进行检票。在火车站里，不同车厢需要单独检票吗？实际上不需要，因为网关在入口处已经完成了检票，节省了很多人力成本。之后你可以自己去找对应的车厢。这里有关键字，网关的主要作用就是统一，它可以统一执行很多操作。

接下来，我们将介绍网关的应用场景。通过应用场景，大家将逐渐了解为什么需要网关。从刚才的途中，可以了解到网关的优势。它对于用户来说屏蔽了底层的调用细节，并保护接口。用户不需要直接调用接口，也不需啊哟关心统计次数，只需在调用之前进入网关此案成功调用。

![image-20230905084734884](http://qiniu.fickler.top/img/image-20230905084734884.png)



**路由：**路由实际上就像一个中转站，类似于我们的路由器。

- 现在我们再看一下上面的图示。假设用户要访问某个接口 A，但现在用户不需要直接调用接口 A，而是通过我们的网关统一接收用户的请求。网关记录了用户调用的接口，并将其转发到对应的项目和接口进行处理，有点类似于前台接待。
- 路由在这里起到转发的作用。举个栗子，假设我们有接口 A 和接口 B，网关会记录这些信息，并根据用户访问的地址和参数，将请求转发到对应的接口（服务器/集群）。为了更好地理解，我们可以设置以下示例路由：如果用户访问接口 A，网关将请求转发到接口 A；如果用户访问接口 B，网关将请求转发到接口 B。这种转发过程就叫路由。此外，还有一种情况是我们后面可能会对接到一个集群。比如，当用户访问接口 C 时，网关可以将请求转发给服务 A 或者集群中的某个机器。在集群中，请求可能会随机转发到其中的某个机器上。



**统一鉴权：**判断用户是否有权限进行操作，物理访问什么接口，我们 都去统一去判断权限，不用重复写。

- 之前的鉴权逻辑写在 `api-interface` 这个项目中的方法里，用于判断用户是否有权限进行操作。但是如果每个方法都要单独写鉴权逻辑，显然是不可行的。所以我们将鉴权逻辑和统计次数一样，抽取出来放到网关中。
- 在网关中，鉴权的终点是实现统一鉴权。无论用户要访问哪个接口，网关都会统一判断权限，不需要重复编写鉴权逻辑，这是网关的强调点之一。网关的作用在很多方面都是强调统一性，将重复的逻辑进行抽象和集中。



**统一处理跨域：**网关统一处理跨域，不用在每个项目里单独处理。

- 在开发单个 Spring Boot 项目或者 Web 项目时，跨域问题是一个常见的挑战。特别是在接口项目中，可能在多项目如项目 A、项目 B 等，每个项目都可能面临跨域问题。如果每个项目都要单独处理跨域，就会出现重复劳动的情况。
- 为了表面重复的跨域处理，我们可以将跨域处理逻辑统一放到网关中，让网关来帮助我们处理跨域问题。这样，项目 A 和项目 B 就不在需要单独处理跨域，而是统一由网关处理。这是一种统一处理跨域的方法。



**统一业务处理：**把一些每个项目中都要做的通用逻辑放到上层（网关），统一处理，比如本项目的次数统计。

- 在我们的项目中，统一业务处理指的是将重复的业务逻辑抽取出来，放到网关这一层进行处理。例如，项目中可能存在一些通用的逻辑，比如统计调用次数和鉴权等。如果我们把这些逻辑写在每个项目的方法里，就会导致重复代码和维护困难。
- 为了避免重复的代码，我们可以将这些统一的业务逻辑放到网关层面进行处理。也就是说，我们可以把统计调用次数和鉴权的代码直接写在网关中，而不是每个方法里。这样，我们只需在网关里面写一次这些逻辑，就可以统一处理所有项目的调用次数统计和鉴权需求。通过网关的统一处理，我们可以将一些通用的业务逻辑进行封装，让项目中的方法更加清晰、简洁，同时也避免了重复劳动。



**访问控制：**黑白名单，比如限制 DDOS IP

- 访问控制，又称黑白名单，实际上也是一种权限控制机制。它与鉴权有一些区别。鉴权通常值授权，即判断用户是否有访问某种资源的权限。而黑白名单则主要用于判断每个用户是否可以访问特定资源，它是一种与业务逻辑独立的控制方式。
- 举个栗子，如果有人恶意刷我们的流量，进行 DDOS 攻击，我们可以将这些恶意 IP 加入黑名单，限制它们呢的访问。这样，这些 IP 就无法访问我们的服务，从而保护我们的接口和服务不受恶意攻击。



**发布控制：**灰度发布，比如上线新接口，先给新接口分配 20% 的流量，老接口 80%，再慢慢调整比重。

- 举个栗子，假设我们的团队开发了一个名为项目 A 和接口 A，现在我们要对接口 A 进行升级，退出一个新版本的接口 A-V2。但我们并不确定新版本是否稳定可靠，所以我们想让一部分用户试用这个新接口。我们可以将流量按比例划分，比如 80% 的流量继续访问就版本的接口 A，而 20% 的流量则引导到新版本的接口 A-V2。这样就实现了灰度测试的效果。然后我们会观察 V2 的表现，如果测试没有问题，我们可以逐步增加流量比例，比如 50%、70%、80，直到 100%。最后，当我们确认新版本的接口稳定可靠时，就可以完全替换掉旧版本，下线旧接口 A。
- 这个流量分配的过程就是发布控制，而它通常是在网关层进行。因为网关是整个流量的入口，所以它可以担当请求流量分配的角色。通过在网关层进行发布控制，我们能够更加灵活地控制用户访问不同版本接口的比例，而无序在每个服务中进行单独处理。这种方式让我们能够更加安全和可靠地进行接口的升级和发布。



![image-20230905092337411](http://qiniu.fickler.top/img/image-20230905092337411.png)



**流量染色：**给请求（流量）添加一些标识，一般是设置请求头中，添加新的请求头。

流量染色是什么意思呢？举个栗子，假设现在有一个用户要访问我们的接口。但是有一个问题，我们希望用户不能绕过网关直接调用我们的接口，我想要防止这种情况发生。那么我们应该如何防止绕过网关呢？

- 一个方法是要确定请求的来源。我们可以为用户通过网关来的请求打上一个标识，比如添加一个请求头 `source=gateway`。只要经过网关的请求，网关就会给它打上 `source-gateway` 的标识。接口 A 就可以根据这个请求来判断，如果请求没有 `source=gateway` 这个标识，就直接拒绝掉它。这样，如果用户尝试绕过网关，没有这个请求头的话，我们的项目就不会认可它。这就是流量染色的一个应用。流量染色还有其他应用，比如区分用户的来源，这和鉴权是不同的概念，属于不同的应用场景。
- 另外一个常见的应用时用于排查用户调用接口时出现的问题。我们为每个用户的每次调用都打上一个唯一的 `traceid`，这就是分布式链路追踪的概念。通过这个 `traceid`，当出现问题时，下游服务可以根据 `traceid` 追踪到具体的请求，从而逐层排查问题。这也是流量染色的作用之一。



![image-20230905093149817](http://qiniu.fickler.top/img/image-20230905093149817.png)



总的来说，流量染色的作用就是给请求添加一些标识，通常通过设置请求头来实现。这样我们可以更好地控制请求的访问和识别请求的来源，同时方便排查问题。流量染色还有许多其他的应用，但他们都是基于给请求添加标识这个基本概念。



> **用户怎么绕过网关？** 用户只要知道服务器的 IP 地址，尤其你的服务又在外网上公开时，用户就可以直接绕过网关进行访问。这种情况下，我们不能让这些接口直接对外暴露，而需要网关来隐藏这些接口信息。



**统一接口保护：**接口保护涉及多种方式，例如限制请求信息、数据脱敏、降级、限流、超时时间等措施。在网关中，我们可以统一进行请求大小的限制和数据脱敏处理，强调了统一的管理。

- 对于接口保护，我们可以通过网关统一限制请求的大小，确保接收到的请求在合理的范围内，避免恶意请求或者大量请求对后端服务造成不必要的负担。同时，我们也可以统一对请求和响应头进行处理。举个栗子，有些接口原本会在响应头中返回服务器的 IP 地址等敏感信息，但是通过网关的操作，我们可以将这些敏感信息抹掉或者删除，保护服务器的隐私和安全。
- 另一个重要的保护机制是降级。当接口调用失败或接口下线时，我们可以采取降级逻辑，比如向用户提示接口已下线，或引导访问其他功能，从而确保用户始终能够得到右移的响应。降级也被称为兜底，作为一种保险措施，即使正式服务不可用，仍能提供有用的反馈。
- 限流也是接口保护的重要手段，通过限制用户每分钟或每秒钟访问接口的次数，可以避免过多的请求对服务器造成压力。此外，设置超时时间也是保护服务器的一种方式，当接口调用时长超过设定时间，强制终端请求，保证服务器的稳定。这些保护措施都是在网关层面进行统一处理，去报接口的安全和可靠性。



**统一日志：**统一的请求、响应信息记录。

在微服务项目中，这样的情况是非常常见的。例如，我们有两个项目：项目 A 和项目 B，它们各自可能记录一些日志。而在网关层，我们可以再添加一层日志记录，以便记录每个用户请求的详细信息，包括鉴权情况以及每次响应的成功或失败信息。这种做法类似于我们之前提到的 AOP 切面，只不过现在我们把这个逻辑放到了网关层而已。



**统一文档：**将下游项目的文档进行聚合，在一个页面统一查看。

举个栗子，假设我们有两个项目，分别是项目 A 和项目 B，并且每个项目都有一个对应的接口文档。通常情况下，如果我们要访问这两个项目的接口文档，我们需要分别访问项目 A 和项目 B 的接口文档地址。

但是如果我们在网关层将这两个接口文档聚合在一起，会有什么作用呢？这实际上类似于语雀（一个在线文档协作平台）的作用。通过在网关层聚合接口文档，我们可以在一个统一的地方查看和管理所有项目的接口文档，不在需要分贝去访问不同的地址。



#### 2. 分类讲解

1. 全局网关（接入层网关）：作用是均衡负载、请求日志等，不喝业务逻辑绑定。
2. 业务网关（微服务网关）：会有一些业务逻辑，作用是将请求转到到不同的业务 / 项目 / 接口 / 服务。

**参考文章：[kong网关_kong 网关_fFee-ops的博客-CSDN博客](https://blog.csdn.net/qq_21040559/article/details/122961395)**

**进一步说明：**

当涉及网关分类时，一般情况下有两种主要类型：业务网关和全局网关，其中接入层网关也是全局网关的一种。他们有着一些区别。业务网关通常位于多个项目或微服务之上，负责根据用户请求将其转发到不同的业务、项目、接口或服务。另一方面，全局网关更多地关注请求本身，主要用于负载均衡。全局网关在大多数情况下并不设计复杂的业务逻辑。相比之下，业务网关可能会包含一定的业务逻辑，比如之前提到的统计次数功能，这会影响你在技术选型上的决策。

然而，实际上你并不需明确区分业务网关和全局网关，因为它们的分类对于技术选型并不是绝对的要求。重要的是根据系统需求来选择合适的网关类型。全局网关的主要功能是负载均衡，将大量的请求平均分摊到系统中的多台机器上。它通常不涉及过多的业务逻辑，而更注重处理请求日志等任务。业务网关则更多地关注业务逻辑，例如：统计次数，请求鉴权等，同时也会负责转发请求到具体的业务处理单元。



#### 3. 实现讲解

1. nginx（全局网关）、kong 网关（API 网关，kong），编程成本相对会高一点。
2. Spring Cloud Gateway（取代了 Zuul）性能高、可以用 Java 代码来编写逻辑，适合学习。

**参考文章：**[微服务网关选型：5种主流 API 网关，哪个最香！ - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/500587132)

**进一步说明：**

**为什么要做网关的分类？** 是因为接下来我们就要讲实现了，我们之前的内容都是理论，接下来将着重讲解实际实现。在实现网关时，一般情况下，我们会遇到两种网关：业务网关和全局网关，而接入层网关其实是全局网关的一种。

**这两种网关有什么区别呢？** 全局网关通常层级较高，可能覆盖多个项目或微服务，负责将用户的请求转发到不同的业务、项目、接口或服务。它主要用于请求的负载均衡等功能，较少涉及具体的业务逻辑。我们可以使用 Nginx 或类似的网关，例如 Kong, Kong 是专门为 API 服务提供的网关。但是不推荐使用 Kong 的原因是，它有商业版本和免费版本，而免费版本可能会有一些限制。 如果没有必要使用商业版本的特性，不建议个人用户使用 Kong，因为它可能会限制一些自由度和灵活性。

相比之下，Nginx 比较推荐的全局网关，也称为接入层网关。Nginx 可以部署前端和后端，还能提供文件访问服务等多种功能，非常灵活。我们甚至可以在Nginx中编写业务逻辑，但是并不推荐这样做，因为它并不像 Spring Cloud Gateway那样方便。

对于业务网关，特别是我们使用 Spring Boot 技术栈的情况下，强烈推荐使用 Spring Cloud Gateway，它可以说是取代了 Zuul。Zuul 的架构设计有些问题，例如并发量有限。而 Spring Cloud Gateway 则使用了 NIO 和多路复用等技术，底层采用了 native 和 react 模型，因此性能更高。

Spring Cloud Gateway 的最大优点是它允许我们使用Java代码来编写逻辑。相比于 Nginx 或 Kong，要学习额外的语言和编程，但在现阶段来说并不是必要的。使用 Spring Cloud Gateway，你只需要掌握 Java 编程就足：够了，这也是为什么推荐使用它的原因。而如果你对 Nginx 或 Kong 感兴趣，你只要知道 Spring Cloud Gateway 的实现，再去查阅 Nginx 或 Kong 的文档就能快速上手。最重要的是理解网关的实现思想，这对于我们实际的开发非常关键。



#### 4. 官网阅读

[Spring Cloud Gateway](https://spring.io/projects/spring-cloud-gateway)

![image-20230905105544274](http://qiniu.fickler.top/img/image-20230905105544274.png)



#### 5. 核心概念

这里我们定义的是一个匹配器，或者更明确地说，在 Spring Cloud Gateway 中它被称为 “断言”。

![image-20230905105847327](http://qiniu.fickler.top/img/image-20230905105847327.png)

**路由**（根据什么条件，转发请求到哪里）

**断言：**一组规则、条件，用来确定如何转发路由

**过滤器：**对请求进行一系列的处理，比如添加请求头、添加请求参数



**请求流程：**

1. 客户端发起请求
2. Handler Mapping：根据断言，去将请求转发到对应的路由
3. Web Handler：处理请求（一层层经过过滤器）
4. 实际调用服务

![image-20230905114053037](http://qiniu.fickler.top/img/image-20230905114053037.png)



**两种配置方式：**

1. 配置式（方便、规范、**推荐**）
   1. 简化版
   2. 全称版
2. 编程式（灵活、相对麻烦）



**断言：**

1. After 在 xx 时间之后
2. Before 在 xx 时间之前
3. Between 在 xx 时间之间
4. 请求类别
5. 请求头（包含 cookie）
6. 查询参数
7. 客户端地址
8. **权重**



**过滤器：**

基本功能：对请求头、请求参数、响应头的增删改查。

1. 添加请求头
2. 添加请求参数
3. 添加响应头
4. 降级
5. 限流
6. 重试



#### 6. 实现示例场景

新建一个 springboot 项目

![image-20230905114646236](http://qiniu.fickler.top/img/image-20230905114646236.png)



选择 Gateway、Lombok、Spring Boot DevTools 依赖

![image-20230905115941491](http://qiniu.fickler.top/img/image-20230905115941491.png)



看它的依赖，最核心的就是这几个

![image-20230905131429400](http://qiniu.fickler.top/img/image-20230905131429400.png)



继续看官网上的 `Getting Started`，它是一种编程式的网关配置方式，通过编写代码来实现。后面会提到声明式和配置式。

![image-20230905120452218](http://qiniu.fickler.top/img/image-20230905120452218.png)



这些代码定义了一个路由器，它的作用是当用户访问某个地址时，将其重定向到指定网址

```java
@SpringBootApplication
public class DemogatewayApplication {
	@Bean
	public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
		return builder.routes()
			.route("path_route", r -> r.path("/get")
				.uri("http://httpbin.org"))
			.route("host_route", r -> r.host("*.myhost.org")
				.uri("http://httpbin.org"))
			.route("rewrite_route", r -> r.host("*.rewrite.org")
				.filters(f -> f.rewritePath("/foo/(?<segment>.*)", "/${segment}"))
				.uri("http://httpbin.org"))
			.route("hystrix_route", r -> r.host("*.hystrix.org")
				.filters(f -> f.hystrix(c -> c.setName("slowcmd")))
				.uri("http://httpbin.org"))
			.route("hystrix_fallback_route", r -> r.host("*.hystrixfallback.org")
				.filters(f -> f.hystrix(c -> c.setName("slowcmd").setFallbackUri("forward:/hystrixfallback")))
				.uri("http://httpbin.org"))
			.route("limit_route", r -> r
				.host("*.limited.org").and().path("/anything/**")
				.filters(f -> f.requestRateLimiter(c -> c.setRateLimiter(redisRateLimiter())))
				.uri("http://httpbin.org"))
			.build();
	}
}
```



将上述代码粘贴到 `ApiGatewayApplication.java` 中

![image-20230905132730598](http://qiniu.fickler.top/img/image-20230905132730598.png)



底下的代码不用管，直接删掉，写一个最简单的即可

![image-20230905132911076](http://qiniu.fickler.top/img/image-20230905132911076.png)



然后，看一下这里的代码，实际上它定义了一个路由器。路由器的作用是当用户访问某个地址时，将请求转发到另一个地址，或者说转发到对应的项目或服务。在这段代码中，首先我们给路由规则标识了一个唯一的 id，这个 id 可以任意取名，只要保证唯一性即可。

```java
package com.example.apigateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.gateway.route.RouteLocator;
import org.springframework.cloud.gateway.route.builder.RouteLocatorBuilder;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class ApiGatewayApplication {

    public static void main(String[] args) {
        SpringApplication.run(ApiGatewayApplication.class, args);
    }

    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
        // 创建路由规则的构建起
        return builder.routes()
                // 定义路由规则，给该规则起一个名字 "tobaidu"
                .route("tobaidu", r -> r.path("/baidu")
                        // 将满足 "*baidu" 路径的请求转发到 "https://www.baidu.com"
                        .uri("https://www.baidu.com"))
                .build();
    }

}
```



修改端口地址

![image-20230905133435714](http://qiniu.fickler.top/img/image-20230905133435714.png)



启动项目

![image-20230905133526482](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20230905133526482.png)



访问 `http://localhost:8090/` 报错，因为没有找到对应的路由，我们没有配置它要访问什么

![image-20230905133724461](http://qiniu.fickler.top/img/image-20230905133724461.png)



访问 `http://localhost:8090/yupi`，地址栏变了，说明重定向成功了

![image-20230905133919964](http://qiniu.fickler.top/img/image-20230905133919964.png)



#### 7. 官方文档阅读-1-开始

##### 官方文档阅读

继续阅读官网，一定要看 `GA` 版本的，不要看 `SNAPSHOT` 快照版本的，选公开的。

![image-20230905135526608](http://qiniu.fickler.top/img/image-20230905135526608.png)



这里讲了怎么去引入 Spring Cloud Gateway

![image-20230905135715361](http://qiniu.fickler.top/img/image-20230905135715361.png)



这里是核心概念，公共有三个。首先是路由，它用于根据网址进行转发。第二个是 `predicate`（断言），它的作用是根据一组条件来进行路由，可以将其理解为一组规则。

![image-20230905140102517](http://qiniu.fickler.top/img/image-20230905140102517.png)



第三个核心概念是 filter，就是过滤器。过滤器的作用类似于我们学过的 Servlet 过滤器。它可以对请求进行集中处理，例如进行一些校验或添加请求头等操作。实际上，过滤器的功能有点类似于 AOP 切面或 Java 拦截器，只不过在这里被称为网关的过滤器，但作用和处理请求的目的是一样的。

![image-20230905141648255](http://qiniu.fickler.top/img/image-20230905141648255.png)



再往下看，**Spring Cloud Gateway** 是怎么工作的。

这个东西其实十分简单，看一下这个客户端，客户端就是我们的用户，就是要请求网站的人。首先请求经过了这个 Gateway Handler Mapping。这是什么东西？它的作用就是根据你的请求找到对应的路由。也就是说，当用户发送请求时，Gate Handler Mapping 会根据请求的信息来确定该请求应该被路由到哪个地方。

Gateway Handler Mapping 它的作用就是处理请求，它要经过一系列的过滤器，也就是说这个过滤器可以去定义多个。比如说我定义一个鉴权过滤器，专门用来从请求头中去鉴权。再定义一个日志过滤器，专门用来去记日志。在定一个跨域过滤器都是可以的。它可以经过多种过滤器，过滤器之间也可以有顺序，先经过哪个后经过哪个。

最后，就是实际调用我们的服务。那实际调用服务，调用的就是我们的真实提供的接口，就是咱们这个 `api-interface` 项目，它就不是网关做的事情了。他最后就真正的去调用这个接口，这个项目的接口，这个流程其实就是这样，蛮简单的。

![image-20230905142436146](http://qiniu.fickler.top/img/image-20230905142436146.png)



再往下，那么我们**如何配置这个路由、过滤器和断言呢？**

Spring Cloud Gateway 提供了两种方法。

第一种方式是配置式或者叫声明式，就是在 `application.yml` 文件中写配置（推荐）。

![image-20230905142734623](http://qiniu.fickler.top/img/image-20230905142734623.png)



第二种方式是编程式，也就是通过编写代码的方式来实现配置。

![image-20230905142854564](http://qiniu.fickler.top/img/image-20230905142854564.png)




然后仔细看看它教你的配置方式，它的配置式分为两种，一种是简单的参数，像下图这样（推荐）：

- `spring.cloud.gateway.routes`：这是配置路由的属性。
- `-id:after_ route`：这是路由的唯一 标识符,用于区分不同的路由。
- `uri:https://example.org`：这是路由将请求转发到的目标URI, 即请求经过此路由后将被转发到 https://example.org 这个地址。
- `predicates`：这是断言的配置属性，于定义请求是否满足路由条件。
- `-Cookie=mycookie,mycookievalue`：这是一个断言条件, 它指定了请求必须具有名为 mycookie 的 Cookie，其值必须为 mycookievalue，才能匹配这个路由。

通过这个配置，当满足请求带有特定 `mycookie` 的 `Cookie` 且其值为 `mycookievalue` 时,请求将被路由到 `https ://example.org` 这个目标 `URI` 。

![image-20230905150356595](http://qiniu.fickler.top/img/image-20230905150356595.png)



复杂点的配置就像下图这样，前面都没变，到 `predicates` 就变了（如果同事不熟悉这个，推荐用这种方式）： 

- `-name:Cookie`：这是一个断言条件，它指定了使用 `Cookie` 作为断言类型来检查请求的 `Cookie`。

- `args`：这是断言条件的参数配置。

- `name:mycookie`：这个参数配置表示要匹配名为 `mycookie` 的 `Cookie`

- `regexp:mycookievalue`：这个参数配置表示要使用正则表达式匹配 `mycookie` 的值是否为 `mycookievalue`，只有当匹配成功时才会满足该路由条件。

  

  通过这个配置,当请求带有名为 `mycookie` 的 `Cookie`，并且其值为 `mycookievalue` 时,请求将被路由到 `htts://example.org` 这个目标 `URI`。注意这里使用了正则表达式来匹配 `Cookie` 的值。

![image-20230905152432230](http://qiniu.fickler.top/img/image-20230905152432230.png)



继续阅读官方文档，第五大章就是路由的各种断言，不用管什么断言工程，就看代码。
第一个 `The After Route Predicate Factory`，这个断言它改了什么?
就是当你的当前时间，在它设置的时间之后，它就会访问 `https://example.org` 这个路由。

![image-20230905152623963](http://qiniu.fickler.top/img/image-20230905152623963.png)



`The Before Route Predicate Factory` 就是当你的当前时间，在它设置的时间之前，它就会访问 `https://example.org` 这个路由。
这个类似于现实生活中某些活动的开始和结束。举个例子，假设你提供了两个接口:
接口A用于在活动结束前进行访问，而接口B则在今天零点活动结束后提供访问。
换句话说，如果用户在活动结束前访问接口 A，他将获得活动相关的内容和服务。然而，一旦活动结束，户将被引导到接口 B，从而获得其他类型的服务或内容。这样的接口设计可以根据活动时间的变化提供不同的功能和响应，为用户带来更好的体验。

![image-20230905152806463](http://qiniu.fickler.top/img/image-20230905152806463.png)



##### 实现示例配置

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: before_route
        uri: https://example.org
        predicates:
        - Before=2017-01-20T17:42:47.789-07:00[America/Denver]
```

复制到 `application.yml` 中，修改一些 `https://yupi.icu`

![image-20230907230754720](http://qiniu.fickler.top/img/image-20230907230754720.png)



重启一下项目

![image-20230907225715411](http://qiniu.fickler.top/img/image-20230907225715411.png)



访问 `http://localhost:8090/` 后，重定向到 `https://yupi.icu`

![image-20230907231317771](http://qiniu.fickler.top/img/image-20230907231317771.png)



#### 8. 官方文档阅读-2-断言

##### 官方文档阅读

继续阅读官方文档，`between` 就是在两个时间之间

![image-20230907231543963](http://qiniu.fickler.top/img/image-20230907231543963.png)



继续往下，`Cookie Route` 刚刚讲过，如果请求头中有了这个 cookie 而且 cookie 的值是 `ch.p`，那么就给你重定向到对应的地址 

![image-20230907231743030](http://qiniu.fickler.top/img/image-20230907231743030.png)



往下，`Header Route`，如果你的请求头中包含了一个名为 `X-Request-Id` 的请求头，并且它的值符合正则表达式 `\d+`（即为一个或多个数字），那么你将重定向到相应的地址。

![image-20230907232203888](http://qiniu.fickler.top/img/image-20230907232203888.png)



往下 `Host Route`，如果你访问的是指定的域名，那么就会被重定向到对应的地址。同时，这里可以使用通配符，即如果访问的是这两个域名中的任何一个，都会被重定向到相应的地址。

![image-20230908182201655](http://qiniu.fickler.top/img/image-20230908182201655.png)



往下，`Method Route`，如果你的请求方法，请求类别，它是 `get` 或者 `post` 的请求，就给你重定向到相应地址。

![image-20230908182452430](http://qiniu.fickler.top/img/image-20230908182452430.png)



往下，`Path Route`，如果你访问的地址是以对应路径作为前缀的，那么就给你访问到对应的地址。

![image-20230908182440615](http://qiniu.fickler.top/img/image-20230908182440615.png)





##### 实现示例配置

对 `Path Route` 进行演示，复制配置文件

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: path_route
        uri: https://example.org
        predicates:
        - Path=/red/{segment},/blue/{segment}
```



粘贴到 `application.yaml` 中

![image-20230908182746484](http://qiniu.fickler.top/img/image-20230908182746484.png)



重启项目，访问 http://localhost:8090/api/name

![image-20230908183640766](http://qiniu.fickler.top/img/image-20230908183640766.png)



现在有个问题，就是当我输入一个地址后，不知道它重定向到哪个规则进行处理，即无法确定应用了哪个路由规则。

在开发的时候，可以加上下图的配置，将 Spring Cloud Gateway 日志级别设置为 “trace”，这意味着 Spring Cloud Gateway 将输出最详细的日志信息，包括所有的跟踪信息。通过设置这个日志级别，我们可以查看每个请求在网关中的处理流程、断言、过滤器的执行情况以及最终路由的结果，有助于调试和排查问题。

需要注意的是，“trace” 会产生大量的日志信息，仅在调试和排查问题的时候使用，生产环境应将日志级别设置为合适的水平，避免过多的日志输出影响性能。

![image-20230908184504548](http://qiniu.fickler.top/img/image-20230908184504548.png)



重启项目，再次访问 http://localhost:8090/api/name，并查看控制台的日志信息

![image-20230908184847462](http://qiniu.fickler.top/img/image-20230908184847462.png)



##### 官方文档阅读

往下，`Query Route`，通过它可以根据查询条件进行路由匹配，比如说你的请求参数中包含一个名为 “green” 的查询参数，那么它就会匹配到这个地址。

![image-20230908185228964](http://qiniu.fickler.top/img/image-20230908185228964.png)



往下，`Remoteaddr Router`，它允许我们根据远程俩进行路由匹配。比如我们访问的用户的 IP 地址是 192.168.1.1/24，就重定向到指定的地址 https://example.org

![image-20230908185457755](http://qiniu.fickler.top/img/image-20230908185457755.png)



往下，`Weight Route`，这个功能非常重要，它允许我们**根据权重来将请求重定向到不同的目标**。

比如下面这个例子，`weight_high` 的权重是 8，`weight_low` 的权重是 2，假设用户访问了 10 次，那么将会有 8 次访问到 `https://weighthigh.org`，2 次访问到 `https://weightlow.org`

![image-20230908185748468](http://qiniu.fickler.top/img/image-20230908185748468.png)



往下，`XForwarded Remote Addr Route`，这个功能允许我们从请求头中获取 `X-Forwarded-Remote-Addr` 这个字段，如果它的值是 192.168.1.1/24，那么就会将请求重定向到地址 https://example.org

![image-20230908190154341](http://qiniu.fickler.top/img/image-20230908190154341.png)





#### 9. 官方文档阅读-3-拦截器

##### 官方文档阅读

看下一部分，刚刚将到拦截器它的作用是什么？对请求进行处理。我们来看怎么对请求进行处理：首先来看一下拦截器的描述，拦截器的主要作用是对请求进行修改或处理响应等操作。

![image-20230909205025304](http://qiniu.fickler.top/img/image-20230909205025304.png)



继续看文档，第一个拦截器叫做 `AddRequestHeader`，从名字上我们可以理解它的作用是添加请求头。

![image-20230909205130647](http://qiniu.fickler.top/img/image-20230909205130647.png)



##### 实现示例配置

先修改模拟接口项目的接口，并给它一个前缀叫作 “name”，修改后打一个断点

模拟接口项目的端口是 8123，所有请求要加上 `/api` 前缀，所以我们新修改的接口地址就是 http://localhost:8123/api/name

![image-20230909205630987](http://qiniu.fickler.top/img/image-20230909205630987.png)



以 `debug` 模式启动项目

![image-20230909205825798](http://qiniu.fickler.top/img/image-20230909205825798.png)



配置路由信息

这段配置是使用 Spring Cloud Gateway 创建了一个路由，将所有以 /api/ 开头的请求转发到 http://localhost:8123 上的目标服务，

![image-20230909210016859](http://qiniu.fickler.top/img/image-20230909210016859.png)



访问 http://localhost:8123/api/name 自动跳转回模拟接口项目，现在拿不到请求头，因为它不会默认添加叫 “test” 的请求头，继续执行

![image-20230910124254927](http://qiniu.fickler.top/img/image-20230910124254927.png)



网页会出现下面的内容

![image-20230910124318589](http://qiniu.fickler.top/img/image-20230910124318589.png)



访问 http://localhost:8090/api/name，自动跳转回模拟接口项目，说明已经访问到我们的接口项目，它访问 8090，给你转发到 8123，继续执行

![image-20230910124513167](http://qiniu.fickler.top/img/image-20230910124513167.png)



页面的返回内容是一样的

![image-20230910124538238](http://qiniu.fickler.top/img/image-20230910124538238.png)



将 api-gateway 中的过滤器注释取消掉，修改一下，重启项目

这个 filters 部分指定了一个过滤器，它的作用是添加一个名为 test 的请求头，并且请求头的值为 swag

![image-20230910125004751](http://qiniu.fickler.top/img/image-20230910125004751.png)



再次访问 http://localhost:8090/api/name

![image-20230910125052104](http://qiniu.fickler.top/img/image-20230910125052104.png)



前面提到过一个叫请求染色的东西。请求染色其实就是给请求打上表示，以证明这个请求是从我们这儿发起的，这样下游服务就能识别它。通过在请求头中添加特定的表示，可以实现对接口的保护，确保只有带有这个请求的请求才能被下游服务认可并允许调用。



##### 官方文档阅读

再往下 `AddRequestParameter`，这个就是增加请求参数，上一 个是增加请求头。

![image-20230910125437434](http://qiniu.fickler.top/img/image-20230910125437434.png)



复制配置到 api-gateway 中

![image-20230910125653095](http://qiniu.fickler.top/img/image-20230910125653095.png)



访问 http://localhost:8090/api/name

![image-20230910125809807](http://qiniu.fickler.top/img/image-20230910125809807.png)

![image-20230910125832523](http://qiniu.fickler.top/img/image-20230910125832523.png)



继续阅读官方文档 `AddResponseHeader`，添加响应头,和之前的一样。

![image-20230910125919401](http://qiniu.fickler.top/img/image-20230910125919401.png)



往下 `CircuitBreaker(断路器)`，断路器的作用是实现服务降级。当你访问某个接口地址时，如果这个接口出现错误，断路器会将你的请求降级，从而转而请求另外一个接口。

![image-20230910125943706](http://qiniu.fickler.top/img/image-20230910125943706.png)



要使用这个断路器的话，就要引入这个包。

![image-20230910130155479](http://qiniu.fickler.top/img/image-20230910130155479.png)



在 api-gateway 项目中引入一下，它是由 Spring Cloud Starter 整合的。resilience4j 是一个面向 Java 的降级库，它能够帮助我们定义各种降级逻辑，根据不同的情况触发不同的降级策略。例如，当访问本地接口出现问题时，我们可以触发-种降级策略；当访问其他服务出现异常时，我们也可以采取不同的降级逻辑。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-circuitbreaker-reactor-resilience4j</artifactId>
</dependency>
```

![image-20230910130450707](http://qiniu.fickler.top/img/image-20230910130450707.png)



复制配置文件

![image-20230910130631641](http://qiniu.fickler.top/img/image-20230910130631641.png)



还有一个外部降级的配置，也复制一下

![image-20230910130721183](http://qiniu.fickler.top/img/image-20230910130721183.png)



粘贴到 api-gateway 项目中，修改一些，然后重启项目

- CircuitBreaker：定一个断路器过滤器，用于实现降级功能。该断路器的名字是 “myCircuitBreaker”，当路由目标地址出现故障时，会触发降级策略，将请求转发到 “/fallback” 路径
- 路由 “api-fallback”，定义了一组断言，这里使用 “Path=/fallback” 断言，表示请求的路径为 “/fallback” 时，会匹配到该路由。

以下配置中，“add_request_header_route” 路由会对请求添加请求头和请求参数，并通过断路器实现降级功能。

当该路由的目标地址出现故障时，请求会被转发到 “/fallback” 路由，即 “api-fallback” 路由，从而实现了降级处理。





## 后端项目开发

### 一、请求转发，调用模拟接口

#### 1. 实现请求转发

我们要定义一个路由规则，就是符合这个规则的请求转发到这种模拟接口项目中。

**可以使用什么规则呢？** 可以使用前缀匹配路由器。

假设我们提供的接口地址都是以：http://localhost:8090/api/ 开头，并且都有一个共同的路径前缀 /api。我们可以配置一个前缀匹配路由器，使得所有路径前缀为 /api/** 的请求都匹配到，并且转发到对应的路径 http://localhost:8123/api/**。

官方文档中有一个叫作 **前缀匹配断言**。

它的作用是可以匹配前缀，并且通过使用 `{}`，可以获取动态的参数。通过 segment 参数拿到用户请求的地址。

![image-20230911093906624](http://qiniu.fickler.top/img/image-20230911093906624.png)



将配置复制到 api-gateway 中，并进行修改

![image-20230911094132798](http://qiniu.fickler.top/img/image-20230911094132798.png)



将模拟接口地址换一下，然后重启项目

![image-20230911094337938](http://qiniu.fickler.top/img/image-20230911094337938.png)



访问 http://localhost:8090/api/name/?name=test，返回响应结果

![image-20230911094456726](http://qiniu.fickler.top/img/image-20230911094456726.png)



重新修改接口地址，再次重启项目

![image-20230911094608921](http://qiniu.fickler.top/img/image-20230911094608921.png)



修改 api-gateway 的配置，让它可以匹配多个路径，重启

![image-20230911094715952](http://qiniu.fickler.top/img/image-20230911094715952.png)



访问 http://localhost:8123/api/name/get?name=test，返回响应结果

![image-20230911094954252](http://qiniu.fickler.top/img/image-20230911094954252.png)



访问 http://localhost:8090/api/name/get?name=test，（实际上访问的是8123）返回响应结果

![image-20230911095009197](http://qiniu.fickler.top/img/image-20230911095009197.png)



#### 2. 添加全局过滤器

现在转发已经完成了，我们要添加一些业务逻辑，怎么做呢？刚刚讲过，要使用全局过滤器。

它的作用是可以自己定义对每个请求的规则，对所有经过网关的请求都执行相同的逻辑。

![image-20230911095242314](http://qiniu.fickler.top/img/image-20230911095242314.png)



复制实例代码

![image-20230911095549419](http://qiniu.fickler.top/img/image-20230911095549419.png)

```java
package com.example.apigateway;

import lombok.extern.slf4j.Slf4j;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

@Slf4j
@Component
public class CustomGlobalFilter implements GlobalFilter, Ordered {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        log.info("custom global filter");
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return -1;
    }
}
```



这段代码主要是实现了 GlobalFilter 和 Ordered 接口。

那么 **Ordered 是什么意思呢？** 它表示一个执行顺序。

![image-20230911095747318](http://qiniu.fickler.top/img/image-20230911095747318.png)



Spring Cloud Gateway 在请求转发到真实接口之前会经过多个过滤器。通过 Ordered 就可以编排这些过滤器的优先级，确定哪个过滤器先拦截，哪个后拦截。这样，就可以定义多个过滤器并按照我们需要的顺序进行处理。



重启，访问 http://localhost:8090/api/name/get?name=test，验证全局过滤器的是否生效

![image-20230911100102361](http://qiniu.fickler.top/img/image-20230911100102361.png)





### 二、编写业务逻辑

#### 1. 请求日志

实例代码给我们提供了一个 exchange 参数，我们所有的请求信息、响应信息、响应体、请求体都能从这里拿到。

![image-20230911100952065](http://qiniu.fickler.top/img/image-20230911100952065.png)



重启项目，访问 http://localhost:8090/api/name/get?name=test，查看响应结果

![image-20230911101332112](http://qiniu.fickler.top/img/image-20230911101332112.png)



查看控制台，它拿到了请求路径，说明不需要刚刚的前缀匹配断言器了。

这里的请求来源地址不太对，可能因为我们是本机，需要换一种取地址的方式。

![image-20230911102335919](http://qiniu.fickler.top/img/image-20230911102335919.png)



换成 127 再试一下，访问 http://127.0.0.1:8090/api/name/get?name=test

![image-20230911102507424](http://qiniu.fickler.top/img/image-20230911102507424.png)



查看控制台可以看到请求来源地址变成了 127.0.0.1

![image-20230911102601576](http://qiniu.fickler.top/img/image-20230911102601576.png)



#### 2. 黑白名单

> 现在有了请求信息，就可以写黑名单了。

如果某个远程地址一直频繁访问，我们可以将其添加到黑名单并拒绝访问。

通过一个全局的常量来设置一个白名单。（通常不使用黑名单，因为白名单会更加安全）

![image-20230911102912794](http://qiniu.fickler.top/img/image-20230911102912794.png)



如果它不包含指定的地址，我们就要拒绝它。**但是怎样进行拒绝呢？**

之前我们直接转发了响应值到原本要转发的模拟接口地址，但现在我们要直接拒绝它。通过刚刚用到的 exchange，获取到响应对象，从而控制该响应，直接设置状态码为 403（禁止访问），然后拦截。

![image-20230911103744803](http://qiniu.fickler.top/img/image-20230911103744803.png)



按下 Ctrl 键，并将鼠标移动到 setComplete 上，可以看到它的返回值是一个 Mono (如下图所示)。
这个 Mono 实是响应式编程的一种对象，类似于前端的 Promise。如果你熟悉前端的异步操作，那么可以将 Mono 理解为类似的概念。在这段代码中，我们直接返回了这个 Mono，它并不包含响应参数。相当于我们告诉程序，请求处理完成了，不要再执行其他操作了。

![image-20230911104544453](http://qiniu.fickler.top/img/image-20230911104544453.png)



重启，访问 http://127.0.0.1:8090/api/name/get?name=test，查看访问结果

![image-20230911104806590](http://qiniu.fickler.top/img/image-20230911104806590.png)

![image-20230911104815828](http://qiniu.fickler.top/img/image-20230911104815828.png)



修改相反的逻辑，重启，再次访问 http://127.0.0.1:8090/api/name/get?name=test，查看访问结果

![image-20230911104837163](http://qiniu.fickler.top/img/image-20230911104837163.png)

![image-20230911104929997](http://qiniu.fickler.top/img/image-20230911104929997.png)

![image-20230911104942494](http://qiniu.fickler.top/img/image-20230911104942494.png)



#### 3. 用户鉴权

接下来就是进行鉴权了，真正的判断 ak、sk 是否合法了

![image-20230912091048660](http://qiniu.fickler.top/img/image-20230912091048660.png)

```java
// 3. 用户鉴权（判断 ak、sk 是否合法）
HttpHeaders headers = request.getHeaders();
String accessKey = headers.getFirst("accessKey");
String nonce = headers.getFirst("nonce");
String timestamp = headers.getFirst("timestamp");
String sign = headers.getFirst("sign");
String body = headers.getFirst("body");
if ("test".equals(accessKey)) {
    return handleNoAuth(response);
}
if (Long.parseLong(nonce) > 10000L) {
    return handleNoAuth(response);
}
// 时间和当前时间不能超过 5 分钟
long currentTime = System.currentTimeMillis() / 1000;
final Long FIVE_MINUTES = 60 * 5L;
if ((currentTime - Long.parseLong(timestamp)) >= FIVE_MINUTES) {
    return handleNoAuth(response);
}
// todo 实际去数据库查看是否一分配给用户
String serverSign = SingUtils.getSign(body, "abcdefg");
if (!sign.equals(serverSign)) {
    return handleNoAuth(response);
}
```



#### 4. 判断请求的模拟接口是否存在

模拟接口的地址信息存储在 api-backend 项目的数据库内，因为我们需要从数据库中查询是否有符合要求的接口。

我们可以判断严谨一点，比如验证 method 是否匹配，地址是否相等来判断。如果需要更严格的校验，还可以再验证请求参数。建议像这种业务层面的请求参数最好不要放到全局网关中处理，而是在业务层面自己处理。

![image-20230912091652245](http://qiniu.fickler.top/img/image-20230912091652245.png)



**为什么这样建议呢？**因为在我们的项目中，并没有引入操作数据库的依赖，如 MyBatis 等。我们之前在 api-backend 项目中引入了这些依赖，因此在网关中再引入的话，可能会造成重复。个人建议是，如果我们已经有现成的访问数据库的方法，或者有可以操作数据库的现成接口，如果那个方法比较复杂，建议使用远程调用的方式调用那个可以操作数据库的项目提供的接口，这样会更方便。

**那怎么调用呢？**有好几种方法，其中包括 HTTP 请求和 RPC。对于 HTTP 请求，可以自己编写客户端，使用一些常见的库，比如 HTTPClient、RestTemplate 或者 Fegin。而对于 RPC，也有多种实现方式，例如 Java 中可以使用 Dubbo 框架。

先打个 todo，因为设计到操作数据库，等会整。

![image-20230912092205549](http://qiniu.fickler.top/img/image-20230912092205549.png)





#### 5. 剩余的业务逻辑

接下来写请求转发，调用模拟接口，那就是 chain.filter 执行这个操作。

![image-20230912092515848](http://qiniu.fickler.top/img/image-20230912092515848.png)



在接口调用成功后，我们需要对接口次数进行累加的操作就非常简单，因为之前我们已经写过了，所以只要将这个方法暴露出来，这样在网关里就可以直接调用它了，避免重复编写这段逻辑。

先打个 todo 预留一下。

![image-20230912092812454](http://qiniu.fickler.top/img/image-20230912092812454.png)



定义一个返回规范的错误码的方法。用于返回错误码 500（调用失败）。

![image-20230912093038489](http://qiniu.fickler.top/img/image-20230912093038489.png)



继续编写逻辑

![image-20230912094127575](http://qiniu.fickler.top/img/image-20230912094127575.png)





#### 6. 后端调用

修改调用地址，之前是在客户端写的调用逻辑，现在修改一下。

将访问地址的端口修改成 8090，并抽取成一个公共常量。

![image-20230912095028371](http://qiniu.fickler.top/img/image-20230912095028371.png)



重新打包

![image-20230912095104532](http://qiniu.fickler.top/img/image-20230912095104532.png)

![image-20230912095127847](http://qiniu.fickler.top/img/image-20230912095127847.png)



#### 7. 自定义响应处理

**问题：**

预期是等模拟接口调用完成，才记录响应日志、统计调用次数。

但现实是 chain.filter 方法立刻返回了，知道 filter 过滤器 return 后才调用了模拟接口。

原因是：chain.filter 是个异步操作，理解为前端的 promise

**解决方案：**

利用 response 装饰者，增强原有的 response 的处理能力

参考博客：[SpringCloud Gateway 打印请求响应日志_springcloud gateway 超时打印_Lyndon1107的博客-CSDN博客](https://blog.csdn.net/qq_19636353/article/details/126759522)

**进一步说明：**

有学习前端的同学，可能已经注意到这是一个异步操作，类似前端的 Promise。这有一个关键的点需要注意，我们现在使用的这个方法是反应式编程的一种方式，它的返回值是 Mono，示一个异步操作，类似前端的 Promise。
然而，我们期望的是按照以下步骤执行： 
首先执行第五步请求转发,调用模拟接口，然后再执行第六步打印响应日志，这样我们才能知道是否调用成功。但实际情况是，在调用第五步时，没有真正触发调用模拟接口，而是直接继续往下执行。直到我们返回这个 filter，它才去调用模拟接口。因此,我们不能这样编写代码。

![image-20230912102927451](http://qiniu.fickler.top/img/image-20230912102927451.png)



从这个模型图中可以观察到，Spring Cloud Gateway 的处理逻辑是等待所有过滤器都执行完毕后，才会继续向下走，直到最终调用被代理的服务,也就是我们的模拟接口。

![image-20230912103009732](http://qiniu.fickler.top/img/image-20230912103009732.png)



我们现在面临一个问题,就是我们希望在调用完远程接口后，再输出响应日志，但由于异步操作的原因，当前的方法等待返回时，远程接口还没有被调用，导致顺序冲突。为了解决这个问题，Spring Cloud Gateway 提供了一个定义响应处理的装饰器,我们可以查阅相关资料来了解如何使用它。

[SpringCloud Gateway 打印请求响应日志_springcloud gateway 超时打印_Lyndon1107的博客-CSDN博客](https://blog.csdn.net/qq_19636353/article/details/126759522)

```java
    try {
        ServerHttpResponse originalResponse = exchange.getResponse();
        DataBufferFactory bufferFactory = originalResponse.bufferFactory();

        HttpStatus statusCode = originalResponse.getStatusCode();

        if(statusCode == HttpStatus.OK){
            ServerHttpResponseDecorator decoratedResponse = new ServerHttpResponseDecorator(originalResponse) {

                @Override
                public Mono<Void> writeWith(Publisher<? extends DataBuffer> body) {
                    //log.info("body instanceof Flux: {}", (body instanceof Flux));
                    if (body instanceof Flux) {
                        Flux<? extends DataBuffer> fluxBody = Flux.from(body);
                        //
                        return super.writeWith(fluxBody.map(dataBuffer -> {
                            byte[] content = new byte[dataBuffer.readableByteCount()];
                            dataBuffer.read(content);
                            DataBufferUtils.release(dataBuffer);//释放掉内存
                            // 构建日志
                            StringBuilder sb2 = new StringBuilder(200);
                            sb2.append("<--- {} {} \n");
                            List<Object> rspArgs = new ArrayList<>();
                            rspArgs.add(originalResponse.getStatusCode());
                            //rspArgs.add(requestUrl);
                            String data = new String(content, StandardCharsets.UTF_8);//data
                            sb2.append(data);
                            log.info(sb2.toString(), rspArgs.toArray());//log.info("<-- {} {}\n", originalResponse.getStatusCode(), data);
                            return bufferFactory.wrap(content);
                        }));
                    } else {
                        log.error("<--- {} 响应code异常", getStatusCode());
                    }
                    return super.writeWith(body);
                }
            };
            return chain.filter(exchange.mutate().response(decoratedResponse).build());
        }
        return chain.filter(exchange);//降级处理返回数据
    }catch (Exception e){
        log.error("gateway log exception.\n" + e);
        return chain.filter(exchange);
    }
```


在 api-gateway 中单独写个方法理解，把上面的代码粘贴进去。

![image-20230912103517625](http://qiniu.fickler.top/img/image-20230912103517625.png)



然后使用这个 handleResponse 方法，把它返回出去，其他的先注释掉。

![image-20230912103946850](http://qiniu.fickler.top/img/image-20230912103946850.png)



修改完后的代码

![image-20230912105553587](http://qiniu.fickler.top/img/image-20230912105553587.png)

```java
package com.example.apigateway;

import com.fickler.apiclientsdk.utils.SingUtils;
import lombok.extern.slf4j.Slf4j;
import org.reactivestreams.Publisher;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.core.io.buffer.DataBuffer;
import org.springframework.core.io.buffer.DataBufferFactory;
import org.springframework.core.io.buffer.DataBufferUtils;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.server.reactive.ServerHttpRequest;
import org.springframework.http.server.reactive.ServerHttpResponse;
import org.springframework.http.server.reactive.ServerHttpResponseDecorator;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

import java.nio.charset.StandardCharsets;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

/**
 * 全局过滤
 */
@Slf4j
@Component
public class CustomGlobalFilter implements GlobalFilter, Ordered {

    public static final List<String> OP_WHITE_LIST = Arrays.asList("127.0.0.1");

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        // 1. 请求日志
        ServerHttpRequest request = exchange.getRequest();
        log.info("请求唯一标识：" + request.getId());
        log.info("请求路径：" + request.getPath().value());
        log.info("请求方法：" + request.getMethod());
        log.info("请求参数：" + request.getQueryParams());
        String sourceAddress = request.getLocalAddress().getHostString();
        log.info("请求来源地址：" + sourceAddress);
        log.info("请求来源地址：" + request.getRemoteAddress());
        // 拿到响应对象
        ServerHttpResponse response = exchange.getResponse();
        // 2. 访问控制 - 黑白名单
        if (!OP_WHITE_LIST.contains(sourceAddress)) {
            // 设置响应状态码为 403 Forbidden（禁止访问）
            response.setStatusCode(HttpStatus.FORBIDDEN);
            // 返回处理完成的响应
            return response.setComplete();
        }
        // 3. 用户鉴权（判断 ak、sk 是否合法）
        HttpHeaders headers = request.getHeaders();
        String accessKey = headers.getFirst("accessKey");
        String nonce = headers.getFirst("nonce");
        String timestamp = headers.getFirst("timestamp");
        String sign = headers.getFirst("sign");
        String body = headers.getFirst("body");
        if (!"test".equals(accessKey)) {
            return handleNoAuth(response);
        }
        if (Long.parseLong(nonce) > 10000L) {
            return handleNoAuth(response);
        }
        // 时间和当前时间不能超过 5 分钟
        long currentTime = System.currentTimeMillis() / 1000;
        final Long FIVE_MINUTES = 60 * 5L;
        if ((currentTime - Long.parseLong(timestamp)) >= FIVE_MINUTES) {
            return handleNoAuth(response);
        }
        // todo 实际去数据库查看是否一分配给用户
        String serverSign = SingUtils.getSign(body, "abcdefg");
        if (!sign.equals(serverSign)) {
            return handleNoAuth(response);
        }
        // 4. 请求的模拟接口是否存在
        // todo 从数据库中查询模拟接口是否存在，以及请求方法是否匹配
        // 5. 请求转发，调用模拟接口
        Mono<Void> filter = chain.filter(exchange);
        // 6. 响应日志
        return handleResponse(exchange, chain);
//        return filter;
    }

    public Mono<Void> handleResponse(ServerWebExchange exchange, GatewayFilterChain chain) {
        try {
            ServerHttpResponse originalResponse = exchange.getResponse();
            DataBufferFactory bufferFactory = originalResponse.bufferFactory();
            HttpStatus statusCode = (HttpStatus) originalResponse.getStatusCode();
            if (statusCode == HttpStatus.OK) {
                ServerHttpResponseDecorator decoratedResponse = new ServerHttpResponseDecorator(originalResponse) {
                    @Override
                    public Mono<Void> writeWith(Publisher<? extends DataBuffer> body) {
                        //log.info("body instanceof Flux: {}", (body instanceof Flux));
                        if (body instanceof Flux) {
                            Flux<? extends DataBuffer> fluxBody = Flux.from(body);
                            return super.writeWith(fluxBody.map(dataBuffer -> {
                                // 7. todo 调用成功，接口次数 +1
                                byte[] content = new byte[dataBuffer.readableByteCount()];
                                dataBuffer.read(content);
                                DataBufferUtils.release(dataBuffer);//释放掉内存
                                // 构建日志
                                StringBuilder sb2 = new StringBuilder(200);
                                List<Object> rspArgs = new ArrayList<>();
                                rspArgs.add(originalResponse.getStatusCode());
                                String data = new String(content, StandardCharsets.UTF_8);//data
                                sb2.append(data);
                                // 打印日志
                                log.info("响应结果：" + data);
                                return bufferFactory.wrap(content);
                            }));
                        } else {
                            // 8. 调用失败，返回一个规范的错误码
                            log.error("<--- {} 响应code异常", getStatusCode());
                        }
                        return super.writeWith(body);
                    }
                };
                return chain.filter(exchange.mutate().response(decoratedResponse).build());
            }
            return chain.filter(exchange);//降级处理返回数据
        } catch (Exception e) {
            log.error("网关处理响应异常" + e);
            return chain.filter(exchange);
        }
    }

    @Override
    public int getOrder() {
        return -1;
    }

    public Mono<Void> handleNoAuth(ServerHttpResponse response) {
        response.setStatusCode(HttpStatus.FORBIDDEN);
        return response.setComplete();
    }

    public Mono<Void> handleInvokeError(ServerHttpResponse response) {
        response.setStatusCode(HttpStatus.INTERNAL_SERVER_ERROR);
        return response.setComplete();
    }

}
```



## 网关业务逻辑

**问题：**网关项目比较纯净，没有操作数据库的包，并且还要调用我们之前写过的代码，复制粘贴维护麻烦。

**理想：**直接请求到其他项目的方法。

我们的网关项目遇到一个问题，就是网关项目比较纯净，没有涉及到数据库操作的包，但同时需要调用之前我们编写过的代码。尽管复制粘贴一开始并不麻烦，但是随着次数增多以及未来的修改维护，就变得相当繁琐了。

因此，我们的理想情况就是希望能够直接请求 api-backend 项目中的 invokeCount 方法，怎么做呢？在这里，涉及到一个知识点，就是远程调用（PRC）。



### RPC 讲解

简单将一下这个 RPC 是什么，假设你之前没有听说过 RPC，那么想象一下情景：

你在项目 A 中编写了一个非常有用的函数，现在你在项目 B 中也想要使用这个函数。但问题是，项目 A 和项目 B 是独立运行的，他们不共享同一片内存，也不在同一个进程中。那么，你怎么做才能调用项目 A 中的那个函数呢？



#### 1. 怎么调用其他项目的方法？

1. 复制代码和依赖、环境
2. HTTP 请求（提供一个接口，供其他项目调用）
3. RPC
4. 把公共的代码打个 jar 包，其他项目区引用（客户端 SDK）



**进一步说明：**

首先是直接复制粘贴别的项目的方法，然而这可能引发环境依赖和代码问题，因为项目之间各自有独特的设置和条件。另一个方法是使用 HTTP 请求，例如，在我们的 api-backend 项目中，有一个 invokeCount 的方法，我们可以将其写到 controller 中并为其创建一个 API 接口，一般其他项目可以调用，这个方法是可行的，但同样需要注意它的限制和适用性。

除此之外，还有其他的一些方法，例如 RPC 和微服务等。尤其是 RPC，它与 HTTP 请求的区别时面试中一个常见问题，后面会简要解释。此外，还有一种常见的方法是将公共代码打包成一个 jar 包，供其他项目引用。这种方法在 API 开发平台项目中很常见，就像 api-client-sdk 项目的做法一样，将其打包成 jar 包，api-backend 项目和 api-gateway 项目都引用了这个 jar  包。



#### 2. HTTP 请求怎么调用？

1. 提供方开发一个接口（地址、请求方法、参数、返回值）
2. 提供方使用 HTTP Client 之类的代码包去发送 HTTP 请求



#### 3. RPC

**作用：**像调用本地方法一样调用远程方法

**和直接 HTTP 调用的区别：**

1. 对开发者更透明，减少了很多的沟通成本
2. RPC 向远程服务器发送请求时，未必要使用 HTTP 协议，比如还可以用 TCP/IP，性能更高。（内部服务更适用）

**RPC 调用模型：**

![image-20230913101835446](http://qiniu.fickler.top/img/image-20230913101835446.png)



**进一步说明：**
RPC 和 HTTP 请求在本质上有一些区别。RPC 相对于 HTTP 请求更加透明，这意味着它在开发者间更具透明度。
**这种透明性是如何理解的呢？**一个广泛的定义是，RPC 的目标是使远程方法的调用就像调用本地方法一样，从而实现更为便捷的远程通信。
以 HTTP 请求为例，调用方需要完成诸多步骤。首先，它需要发送 HTTP 请求，使用一个 HTTP 客户端的代码库。以我们之前提到的 api-client-sdk 项目为例，我们可以看到客户端对象是如何使用 HTTPUtil 这个 hutool 工具类的包来发送请求的，这意味着调用方需要编写特定的代码段来发送请求，然后处理返回值，可能还需要解析返回值并封装参数的映射关系等。

![image-20230913102332475](http://qiniu.fickler.top/img/image-20230913102332475.png)



在调用本地方法时，我们可以直接传递参数给方法，比如接口统计的方法（如下图所示）。我们之前提到的 api-client- sdk 项目里，你需要自行封装 HTTP 请求，将参数打包成一个参数映射（map） ， 然后按照客户端工类的方式发送请求。此外,你还需要解析返回值，将其中的 code、data 以及 message 等信息提取出来。

![image-20230913102459950](http://qiniu.fickler.top/img/image-20230913102459950.png)



但是，如果我们使用 RPC 方式，就能够实现与调用本地方法类似的体验。你可以直接指定要传递的参数，并且也能够直接获得返回值，无论是布尔类型还是字符串。RPC 式不需要像 HTTP 请求那样进行额外的封装，但如果你需要封装，当然也可以根据需求自行处理。
因此，我们可以说 RPC 的主要目标之一是模仿调用本地方法的方式，从而实现远程调用。比方说，现在在 api-backend 项目中，有一个方法写在了这个项目里，那么我们只需一行代码：userlnterfacelnfoService.invokeCount，就可以调用该方法。

![image-20230913102758744](http://qiniu.fickler.top/img/image-20230913102758744.png)



同样的，如果切换到另-个项目，比如 api-gateway，在这个网关项目中，我们同样可以直接使用这行代码，做到方法调用的无缝切换。

![image-20230913102946187](http://qiniu.fickler.top/img/image-20230913102946187.png)



RPC 的主要职责就是这个，它的最大作用在于模拟本地方法调用的体验。看上去是请求本地代码，实际上，它可能会请求到其他项目、其他服务器等等。这就是 RPC 的最大价值所在。如果大家不理解，可以想一下刚刚的例子，你只知道 RPC 最大的优势在于它的透明性，你不需要了解它如何在 HTTP Client 中怎么封装参数，只需直接调用现成的方法即可，这样可以以大减少沟通成本。



-------

有同学问：**feign 不也是动态生成的 httpclient 吗？**

Feign 本质地是动态生成的 HTTP 客户端，但它在调用远程方法时更加精简了 HTTP 请求的步骤。尽管 Feign 使远程调用看起来像是调用本地方法，但实际上与 RPC 仍然有一些微小的区别。虽然两者都可以实现类似的功能，但它们在底层协议上存在差异。

RPC（Remote Procedure Call 远程过程调用）的一个关键优势在于，它可以使用多种底层协议进行远程调用，而不限于 HTTP 协议。虽然 HTTP 协议可以实现类似的功能，但考虑到性能，RPC 可以选择更原生的协议，如 TCP/IP。而且，网络上还存在其他性能更高的协议，可以根据需要进行选择。

在微服务项目中，对于内部接口，使用 RPC 可能会获得更好的性能。然而，选择使用 Feign 还是 RPC 取决于具体的技术需求，没有绝对的优劣之分。需要注意的是，RPC 和 HTTP 请求可以结合使用，因为 RPC 的底层协议可以提 HTTP，也可以是其他协议，如 TCP/IP 或自定义协议。

综上所述，RPC 和 HTTP 请求是可以互相结合的，但 RPC 在协议的选择上更加灵活。当面试被问到这方面的问题时，先强调 RPC 的主要作用，然后阐述 RPC 和 HTTP 之间的关系。

-----

简单绘制一张图，来说明 RPC 的工作流程。我们刚刚已经了解了 RPC 的作用以及为什么需要使用 RPC，现在详细说明实现类似本地方法调用的方式来调用远程方法。这需要我们的两个项目，即方法提供者项目和方法调用者项目，来进行一些特定的操作。

在这里，引入一个简单的概念，对于学习过操作系统的同学应该很熟悉。为了实现 RPC，我们需要涉及几个关键角色。

1. **提供者（Producer/Provider）** ：首先，我们需要一个项目来提供方法，这个项目被称为提供者。它的主要任务是为其他人提供已经写好的代码，让其他人可以使用。举例来说，我们可以提供一个名为 invokeCount 的方法。
2. **调用方（Invoker/Consumer）** ：一旦服务提供者提供了服务，调用方需要能够找到这个服务的位置。这就需要一个存储，用于存储已提供的方法，调用方需要知道提供者的地址和 invokeCount 方法，这里需要一个公共的存储。
3. **存储**：这是一个公共存储，用于存储提供者提供的方法信息。调用方可以从这个存储中获取提供者的地址和方法信息，例如，提供者的地址可能是123.123.123.1，方法是 invokeCount，这些信息会存储在这个存储器中。

调用方可以从存储中获取信息后，就知道调用 invokeCount 方法时需要访问 123.123.123.1，这就是 RPC 的基本流程，这三个角色构成了整个 RPC 模型。

存储有时也会被称为**注册中心**，它管理着服务信息，包括提供者的 IP 地址等等。调用方从这里获取信息，以便进行调用。

![image-20230913104105495](http://qiniu.fickler.top/img/image-20230913104105495.png)



然而，需要注意的是在整个流程中，最终的调用并不是由注册中心来完成的。虽然注册中心会提供信息，但实际上调用方需要自己进行最后的调用动作。注册中心的作用是告诉调用方提供者的地址等信息，然后调用方会根据这些信息来完成最后的调用。

一般情况下，调用方会直接寻找提供者进行调用，而不是依赖注册中心来完成实际的调用过程。注册中心主要的功能是提供地址信息，并不会承担将调用方所需的内容传送到提供者的角色，整个过程遵循这样的流程。



## Dubbo 框架

### 一、Dubbo 框架介绍

Dubbo 框架，它是目前国内非常主流的 RPC 实现框架。当然，还有其他类似的框架，比如 GRPC 和 TRPC 等等，这些都是不同的 RPC 远程调用框架，Dubbo 和 GRPC 是比较知名的。Dubbo 由阿里开发，而 GRPC 由 Google 开发，TRPC 则是由腾讯开发。

Dubbo 框架在国内学习起来可能更容易些。有同学可能会问，**Dubbo 框架需要花多少时间学习？**还需要花费很多时间。如果你只是想入门，跟着直播回放就足够了。理解了 Dubbo 框架的基本思想后，实际使用起来只需要几行代码。当然,在使用过程中可能会遇到一些小问题，但是会带大家克服这些问题，所以不用担心。

最佳的学习方式是阅读 Dubbo 框架的官方文档，会帮助你更好地理解这个框架。[3 - 基于 Spring Boot Starter 开发微服务应用 | Apache Dubbo](https://cn.dubbo.apache.org/zh-cn/overview/mannual/java-sdk/quick-start/spring-boot/)



**两种使用方式：**

1. Spring Boot 代码（注解 + 编程式）：写 Java 接口，服务提供者和消费者都去引用这个接口。
2. IDL （接口调用语言）：创建一个公共的接口定义文件，服务提供者和消费者读取这个文件。优点是跨语言，所有的框架都认识。

Dubbo 底层用的是 Triple 协议：[Triple协议 | Apache Dubbo](https://cn.dubbo.apache.org/zh-cn/overview/mannual/java-sdk/reference-manual/protocol/triple/)



**示例项目学习：**

zookeeper 注册中心：通过内嵌的方式运行，更方便。

**启动流程**：最先启动注册中心，先启动服务提供者，再启动服务消费者。



**整合运用：**

1. backend 项目作为服务提供者，提供 3 个方法：
  1. 实际情况应该是去数据库中查是否已分配给用户
  2. 从数据库中查询模拟接口是否存在，以及请求方法是否匹配（还可以校验请求参数）
  3. 调用成功，接口调用次数 +1 invokeCount
2. gateway 项目作为服务调用者，调用这 3 个方法



----

**补充知识：**

1. **zookeeper 是什么呢？**

有同学学过的话，就知道 Zookeeper 就是我们之前图中所提到的注册中心。Dubbo 的作用就是将你的提供者、调用方和注册中心这三者通过其框架整合在一起，从而完成整个远程过程调用（RPC）。因此，Dubbo 可以被视为一个实现了 RPC 功能的框架，而 Zookeeper 则是一个可作为注册中心的存储，Nacos 也可以作为注册中心。

![image-20230913110129615](http://qiniu.fickler.top/img/image-20230913110129615.png)



2. **为什么一定要知道注册中心的地址？**

因为我们的服务提供者需要将自己提供的接口方法告知注册中心，所以它必须知道注册中心的地址，这样才能将自己的信息上报给注册中心，这也是为什么我们必须要配置注册中心。同样的道理，我们的消费者调用方也需要知道注册中心的地址，以便将注册中心的地址配置到项目中。



3. **IDL 是什么呢？**

IDL （接口定义语言）是一种约定俗成的语言，用于定义接口和数据结构的语法。它是一种人为约定的语言，通过这种语法，可以明确地定义接口和数据的结构，使各方在交流和协作时能够达成一致。这种约定的语言为不同的系统、平台或语言提供了一种统一的描述方式，使得不同环境下的应用程序能够理解和交互。



### 二、Nacos

#### 1. 快速开始

下载最稳定的版本 2.1.1

![image-20230913111230647](http://qiniu.fickler.top/img/image-20230913111230647.png)



进入到解压的目录下的 bin 目录，调出 cmd 命令行，根据官网的启动命令，启动服务

![image-20230913111526668](http://qiniu.fickler.top/img/image-20230913111526668.png)

![image-20230913111643155](http://qiniu.fickler.top/img/image-20230913111643155.png)



#### 2. 整合 Nacos 注册中心

看一下 Dubbo 怎么用 Nacos。

![image-20230913111752296](http://qiniu.fickler.top/img/image-20230913111752296.png)



引入依赖

```xml
    <dependency>
        <groupId>org.apache.dubbo</groupId>
        <artifactId>dubbo</artifactId>
        <version>3.0.9</version>
    </dependency>
    <dependency>
      <groupId>com.alibaba.nacos</groupId>
      <artifactId>nacos-client</artifactId>
      <version>2.1.0</version>
    </dependency>
```

![image-20230913112020817](http://qiniu.fickler.top/img/image-20230913112020817.png)



添加配置文件（官网给出的配置文件少了点，不满足我们的要求，自己适当添加）

![image-20230913112141178](http://qiniu.fickler.top/img/image-20230913112141178.png)

```yml
dubbo:
  application:
    name: dubbo-springboot-demo-provider
  protocol:
    name: dubbo
    port: -1
  registry:
    id: nacos-registry
    address: nacos://localhost:8848
```



将配置同样复制到 api-gateway 项目配置中

![image-20230913112659244](http://qiniu.fickler.top/img/image-20230913112659244.png)



在 api-gateway 中也要添加对应的依赖

![image-20230913112803686](http://qiniu.fickler.top/img/image-20230913112803686.png)



去 nacos 注册中心看一下，密码都是 `nacos`

![image-20230913112948138](http://qiniu.fickler.top/img/image-20230913112948138.png)



现在还没有整合示例，所以没有服务

![image-20230913113029220](http://qiniu.fickler.top/img/image-20230913113029220.png)



#### 3. 整合示例

复制 provider 文件到 api-backend 项目中

![image-20230913113324079](http://qiniu.fickler.top/img/image-20230913113324079.png)

![image-20230913113419313](http://qiniu.fickler.top/img/image-20230913113419313.png)



在 myapplication 中添加注释 

![image-20230913113528275](http://qiniu.fickler.top/img/image-20230913113528275.png)



复制 project 文件到 api-gateway 项目中

![image-20230913113820419](http://qiniu.fickler.top/img/image-20230913113820419.png)

![image-20230913113917189](http://qiniu.fickler.top/img/image-20230913113917189.png)



复制 YuapiGatewayApplication 替换掉 api-gateway 中的 YuapiGatewayApplication

![image-20230913114015049](http://qiniu.fickler.top/img/image-20230913114015049.png)

![image-20230913114135500](http://qiniu.fickler.top/img/image-20230913114135500.png)



启动后，api-gateway 项目报错了，第一个报错是因为缓存的问题，这个可以不用在意

![image-20230914205211107](http://qiniu.fickler.top/img/image-20230914205211107.png)



第二个错是端口被占用，它这边服务自动绑定 22222 端口，但是绑定失败

![image-20230914205306226](http://qiniu.fickler.top/img/image-20230914205306226.png)



**原因：**

dubbo 端口配置是 -1（随机端口），系统都给默认成了 22222 端口，解决办法就是将其写为固定的端口即可。

![image-20230914205544313](http://qiniu.fickler.top/img/image-20230914205544313.png)

![image-20230914205554962](http://qiniu.fickler.top/img/image-20230914205554962.png)



重启后，控制台有输出结果就行。

![image-20230914214024031](http://qiniu.fickler.top/img/image-20230914214024031.png)

![image-20230914214034804](http://qiniu.fickler.top/img/image-20230914214034804.png)



去 nacos 中心看一下，注册上了

![image-20230914214113678](http://qiniu.fickler.top/img/image-20230914214113678.png)





## 后端网关业务

### 一、抽象公共服务

**项目名：**api-common

**目的：**让方法、实体类在多个项目间复用，减少重复编写

**服务抽取：**

1. 数据库中查看是否已分配给用户密钥（根据 accessKey 拿到用户信息，返回用户信息，为空表示不存在）
2. 从数据库中查询模拟接口是否存在（请求路径、请求方法、请求参数，返回接口信息，为空表示不存在）
3. 接口调用次数 +1 invokeCount（accessKey、sercetKey（标识用户），请求接口路径）

**步骤：**

1. 新建干净的 maven 项目，只保留必要的公共依赖
2. 抽取 service 和实体类
3. install 本地 maven 包
4. 让服务提供者引入 common 包，测试是否正常运行
5. 让服务消费者引入 common 包

**进一步说明：**

接下来，我们计划创建一个公共服务模块，以便在多个项目之间实现方法和实体类的复用，从而避免重复编写相同的代码。

首先，我们需要明确哪些通用服务是必要的。先从数据库中查询是否已为用户分配了密钥，我们可以对这个方法进行进一步抽象，考虑需要传递哪些参数以及期望的返回值，具体来说,我们要查找数据库以确认是否已经将 accessKey 分配给了某个用户。

我们**需要关注哪些参数需要传递？**只需要 accessKey 参数，如果还有其他参数，可以在之后进行补充。

其次，我们需要处理的另一个问题是从数据库查询模拟接口是否存在。这个任务是网关模块的一部分，我们需要确保网关能够实现这个功能，具体来说，我们需要一个方法来检查数据库中是否存在特定的模拟接口。

**这个方法需要接收哪些参数呢？**找到网关项目 api- gateway。

![image-20230914220031994](http://qiniu.fickler.top/img/image-20230914220031994.png)



这里的第四步，我们既然是要查模拟接口是否存在，是不是要传递这个接口的信息。

这接口信息是什么？是从请求路径和方法中获取到的，甚至你还可以传请求参数。

所以这里的话我们可以封装一个对象。

![image-20230914220235443](http://qiniu.fickler.top/img/image-20230914220235443.png)





第三个就是对接口调用次数进行加一操作。 这个过程需要传递什么参数呢？

考虑到要将调用次数加一，必须要知道是哪个户进行了接口调用，以及具体是哪个接口的调用次数要增加。在这里，可以借助 accessKey 和 secretKey 这两个密钥来标识用户并获取其信息。

因此，我们需要传递的参数包括 accessKey 和 secretKey。通过 secretKey，我们可以识别用户并获取相关信息。此外，还需要传递请求的接口路径。暂定这些，后续需要进行调整,也可以进行修改。



### 二、创建公共服务

#### 1. 创建公共服务项目

新建一个 api-common 项目

![image-20230915165206171](http://qiniu.fickler.top/img/image-20230915165206171.png)



接下来要将服务之间进行调用的几个方法写到公共类中，以及相应的 model，需要什么就传什么

