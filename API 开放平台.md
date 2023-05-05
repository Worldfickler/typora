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

![e9c6efac-04aa-4bed-9b63-6f5c597fa715](Z:/typora%E5%9B%BE%E7%89%87%E6%9C%AC%E5%9C%B0%E5%BA%93/e9c6efac-04aa-4bed-9b63-6f5c597fa715.png)

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

