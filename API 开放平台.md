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

![image-20230504000017412](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230504000017412.png)

2. 创建项目

```shell
pro create api-frontend
```

![image-20230504000402653](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230504000402653.png)

### 2. 安装依赖

1. 使用前端工具打开项目

![image-20230504000535225](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230504000535225.png)

2. 安装依赖

```shell
yarn 或者 npm install
```

![image-20230504000631309](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230504000631309.png)

> 我这里是 yarn-js 是因为，我大数据中的 yarn 包和包管理工具 yarn 冲突了，所以把 yarn 改成了 yarn-js

### 3. 启动项目

![image-20230505002347795](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505002347795.png)

![image-20230505002414267](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505002414267.png)

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

![image-20230505005437393](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505005437393.png)



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

![image-20230505095452422](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505095452422.png)

![image-20230505095503181](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505095503181.png)



#### 2. 转移文件

将在 `generator` 中生成的文件移动到对应的包下

![image-20230505095710438](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505095710438.png)

#### 3. 修改一下文件路径

![image-20230505095908197](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505095908197.png)



#### 4. 为逻辑删除打上注解

> mybatis-plus 插件生成的时候，是不能判断是否是逻辑删除的字段，因此我们要为逻辑删除字段额外的添加逻辑删除注解

![image-20230505101435502](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505101435502.png)



### 三、增删改查crud

#### 1. 修改 controller 层

1. 复制一份 `PostController` 改名为 `InterfaceInfoController`

![image-20230505100634840](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505100634840.png)



2. 替换关键字

**ctrl + r 快捷键全局替换**

- post 替换为 interfaceInfo 

![image-20230505100919248](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505100919248.png)



- Post 替换为 InterfaceInfo

![image-20230505100943218](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505100943218.png)



- InterfaceInfoMapping 替换为 PostMapping

![image-20230505101003677](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505101003677.png)



#### 2. 封装 InterfaceInfo 实体类

> 对于每一个接口传入的信息，我们都封装一个专门的实体类

**model包 -> dto 包**

在 `model` 包下新建一个 `interfaceinfo` 包，将之前 `post` 包下的三个请求复制到新包中，并修改名字

![image-20230505102154084](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505102154084.png)



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

![image-20230505104137589](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505104137589.png)

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

![image-20230505113124321](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230505113124321.png)



## 前端

### 一、配置插件

前端项目使用一些代码规范文件要求，为了项目更加规范，添加一些插件

> 搜索 `eslint` 选上自动识别

![image-20230506105146113](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506105146113.png)



> 搜索 `prettier` 打 √ 美化代码

![image-20230506105401855](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506105401855.png)



### 二、接口调用

> 前端调用后端接口，我们一般要自己写方法，例如：
>
> ```tsx
> /** 获取当前的用户 GET /api/currentUser */
> export async function currentUser(options?: { [key: string]: any }) {
>   return request<{
>     data: API.CurrentUser;
>   }>('/api/currentUser', {
>     method: 'GET',
>     ...(options || {}),
>   });
> }
> ```
>
> 除了自己写，我们还可以自己生成，ant design pro 提供了 oneapi 插件自动生成



#### 1. 使用 oneapi 插件自动生成

如果前端要自动生成，需要将后端遵循的 openapi 规范的文档提供给前端

![image-20230506110117913](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506110117913.png)

![image-20230506110150064](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506110150064.png)



1. 打开 `config.ts` 找到 `openApi`

![image-20230506110305412](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506110305412.png)



2. 我们只需要一个，删除一个

![image-20230506110441814](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506110441814.png)

![image-20230506110550343](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506110550343.png)

> `schemaPath：` 1. 建立本地的 json 文件，将文件地址复制到里面；2. 使用在线的 json 地址
>
> `projectName：` 后端项目名称



#### 2. 测试一下

> 找到 `package.json` 中的 `openapi` 命令执行

![image-20230506111412248](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506111412248.png)



执行成功

![image-20230506111620728](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506111620728.png)



看一下 `service` 中生成的文件

![image-20230506112133083](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506112133083.png)



1. 因为我们有后端，所以应该直接请求真实环境，所以直接用 dev 模式运行

![image-20230506113524151](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506113524151.png)



2. 我们还要更改请求后端的地址，确保我们能够请求到服务器

![image-20230506114412595](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506114412595.png)



3. 测试一下

> 报错了，提示前后端接口定义不一致

![image-20230506114558894](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506114558894.png)



### 三. 修改登录的接口

> 这个 `login` 是使用 oneapi 生成之前自带的登录接口

![image-20230506114925986](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506114925986.png)



#### 1. 修改成我们自己的登录接口

1. 修改成我们自己的登录接口

![image-20230506115331175](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506115331175.png)



2. 修改登录逻辑

![image-20230506115603258](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506115603258.png)



3. 修改下面的账号和名称的参数名称

![image-20230506115838504](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506115838504.png)



4. 再测试一下

![image-20230506121335564](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506121335564.png)



登录成功，但是没有跳转



> 为什么没有跳转？
>
> 因为我们没有记录用户的登录状态，不知道它是否登录成功！！！



#### 2. 设置用户的登录状态

> 这个方法，当我们首次访问页面的时候，获取用户信息，获取当前全局的一些状态，可以把它当做全局变量

![image-20230506121504098](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506121504098.png)



1. 在 `typings.d.ts` 中添加全局状态类型

```tsx
/**
 * 全局状态类型
 */
interface InitialState {
  loginUser?: API.UserVO;
}
```

![image-20230506124450248](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506124450248.png)



2. 修改 `getInitialState()` 方法

![image-20230506124638143](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506124638143.png)



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

![image-20230506125426744](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230506125426744.png)



5. 测试一下

![image-20230510131018655](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230510131018655.png)



#### 3. 刷新后需要重新登录

原因：前端没有种上 cookie，或者是前端种上了 cookie，但是向后端发送请求的时候没有带上 cookie



```tsx
withCredentials: true,
```



![image-20230510131223698](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230510131223698.png)





### 四、接口管理

接口管理页面只有管理员可以查看



#### 1. 判断用户权限



canUser：只要用户登录就有权限

canAdmin：用户必须是管理员才有权限



在 `access.ts` 中修改代码



![image-20230510132452878](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230510132452878.png)



查看页面



![image-20230510132919438](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230510132919438.png)



#### 2. 改造表格页面



找到 `src/pages/TableList/index.tsx`



> `ctrl + shift + -`：全局压缩



![image-20230510133239774](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230510133239774.png)



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



![image-20230510135534054](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230510135534054.png)



没有显示数据，下面有一个 request 方法，只需要将传入的值改为我们自己的方法就可以了



![image-20230510135741553](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230510135741553.png)



查看页面



![image-20230510135831746](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230510135831746.png)



虽然拿到了数据，但是报错了



**如何排除错误？**

1. 请求参数是否一致
2. 响应值是否一致



修改 `request` 请求参数和响应值



![image-20230510140755309](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230510140755309.png)



![image-20230510140745053](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230510140745053.png)



### 五、页面优化

#### 1. 完善接口管理页面

1. 修改地方

![image-20230515145619546](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230515145619546.png)



2. 修改后的页面

![image-20230515145653378](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230515145653378.png)



#### 2. 完善新建

1. 编写模态框

![image-20230515173223634](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230515173223634.png)



2. 在 index 使用新建模态框

![image-20230515173259409](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230515173259409.png)



3. 查看效果

![image-20230515173322162](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230515173322162.png)



#### 3. 完善修改

1. 按照新建的模态框重新构造一个修改的模态框

![image-20230515173444915](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230515173444915.png)



2. 在 index 页面中使用更改的模态框

![image-20230515173518592](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230515173518592.png)



3. 查看效果

![image-20230515173534872](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230515173534872.png)



**注意事项**

直接使用 table 的 form 属性，只会获取第一次的表单值，后面不会继续更改

换句话说就是，直接使用 form 属性，点开第一个接口得到的表单值和点开第二个接口得到的表单值是相同的，不会进行更新



#### 4. 添加新建接口的校验

ant design pro 组件中有校验表单的属性

[快速开始 - ProComponents (ant.design)](https://procomponents.ant.design/docs)

![image-20230515174001846](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230515174001846.png)



![image-20230515174026119](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230515174026119.png)



#### 5. 完善删除

![image-20230515174121913](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230515174121913.png)



![image-20230515174132174](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230515174132174.png)



## 后端开发



### 一、模拟接口

#### 1. 创建项目

![image-20230516102146293](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230516102146293.png)



![image-20230516102233341](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230516102233341.png)



#### 2. 提供三个模拟接口

1. 接口大致内容

> 1. GET 接口
> 2. POST 接口（url 传参）
> 3. POST 接口（Restful）



2. 新建 controller 层

![image-20230516112230435](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230516112230435.png)

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

![image-20230516112307871](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230516112307871.png)

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

![image-20230516112350883](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230516112350883.png)

```yml
server:
  port: 8123
  servlet:
    context-path: /api
```



#### 3. 接口测试

启动测试，访问地址：http://localhost:8123/api/name

![image-20230516112430802](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230516112430802.png)



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

![image-20230516113008876](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230516113008876.png)



2. 安装依赖

![image-20230516113032704](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230516113032704.png)

```xml
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>5.8.16</version>
</dependency>
```



3. 使用 hutool 调用 get、post 接口

![image-20230516113515662](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230516113515662.png)

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

![image-20230516113618457](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230516113618457.png)

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

![image-20230516113719314](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230516113719314.png)



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

![image-20230522160405867](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522160405867.png)

![image-20230522160421269](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522160421269.png)



#### 2. 服务器端校验

我们需要在服务器端校验

![image-20230522160502960](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522160502960.png)



因为我们从请求头里面获取，所以要将 accessKey 和 secretKey 放入到请求头里面

![image-20230522160554622](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522160554622.png)



运行测试一下

![image-20230522160617054](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522160617054.png)



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

![image-20230522161533660](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522161533660.png)



2. 编写 getSign 签名算法

使用 hutool 的摘要加密 **Digester**

![image-20230522161647444](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522161647444.png)



![image-20230522162148137](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522162148137.png)



3. 重写服务端的校验

![image-20230522162606580](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522162606580.png)



这里剪贴一下生成签名的方法，将其封装成一个工具类

![image-20230522162735688](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522162735688.png)



4. 测试一下

![image-20230522164850751](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522164850751.png)



**API 签名认证是一个很留货的设计，具体有哪些参数，尽量服务端调用，参数名如何要根据场景来。**



### 四、开发一个简单的易用的 SDK

这些操作对于开发者，还是复杂，开发者应该只需要关心，自己需要调用哪些接口，传什么参数，密钥是什么，所以，我们需要开发一个便于开发者使用的 SDK。



todo：将 sdk 发布到 maven 仓库



> 开发 starter 的好处：开发者引入之后，可以直接在 application.yml 中写配置，自动创建客户端



#### 1. 新建项目

![image-20230522165354281](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522165354281.png)



![image-20230522165435614](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522165435614.png)



#### 2. 查看依赖

![image-20230522165550774](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522165550774.png)



![image-20230522165935306](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522165935306.png)



#### 3. 删除主动类

我们不需要这个 springboot 启动类，将其删掉

![image-20230522170031082](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522170031082.png)



#### 4. 新建配置类

注解：

```
@Data
@Configuration
@ConfigurationProperties("api.clint")
@ComponentScan
```



复制之前项目的接口，客户端

![image-20230522170757866](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522170757866.png)



导入 hutool 依赖

![image-20230522170813244](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522170813244.png)



回到配置类

![image-20230522171020881](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522171020881.png)



#### 5. 创建爱你 META_INF 文件夹

在 **resource** 下创建 **META-INF** 文件夹

在 **META-INF** 文件夹下新建 **spring.factories**

![image-20230522171621241](https://fickler-1314393782.cos.ap-beijing.myqcloud.com/img/image-20230522171621241.png)



复制配置类路径

```
# spring boot start
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.fickler.apiclintsdk.ApiClientConfig
```

![](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522171621241-1684747802116-2.png)



#### 6. 进行测试

1. 打包

![image-20230522173040969](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522173040969.png)



![image-20230522173214478](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522173214478.png)



2. 在 **api-interface** 项目中，将之前的代码删掉

![image-20230522173622020](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522173622020.png)



![image-20230522173338252](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522173338252.png)



3. 引入刚才写好的依赖

复制**api-clint-sdk**的信息

![image-20230522173434207](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522173434207.png)

```xml
<groupId>com.example</groupId>
<artifactId>api-clint-sdk</artifactId>
<version>0.0.1</version>
<name>api-clint-sdk</name>
<description>api-clint-sdk</description>
```



4. 复制到 **api-interface** 依赖文件中

![image-20230522173806646](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230522173806646.png)‘

5. 打开配置文件

![image-20230523120020578](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230523120020578.png)



6. 编写测试类

![image-20230523120059410](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230523120059410.png)



7. 测试一下

![image-20230523120121727](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/image-20230523120121727.png)



### 
