### 接口范文

> 作者:  华德禹
电话:  18223279185
邮箱:  huadeyu@hiynn.com
微信:  deyu_hua
 QQ:   493387973


---
  项目列表模块接口
---

| 接口名      | HTTP方法 | 权限  | URL                                                 | 完成状态 | 维护人 |
| ---------- | ------- | ---- | ---------------------------------------------------- | ------ | ----- |
| 获取用户项目 | GET     | USER | [/apps](#app-all)                                     | n      | hhq&zyj |
| 创建项目    | POST    | USER | [/app](#app-add)                                     | n      | hhq&zyj |
| 删除项目    | DELETE  | USER | [/app/:appId](#app-delete)                           | n      | hhq&zyj |
| 更新项目    | PUT     | USER | [/app/:appId](#app-put)                              | n      | hhq&zyj |
| 获取项目数据 | GET     | USER | [/app/:appId](#app-get)                              | n      | hhq&zyj |
| 创建项目资源 | POST    | USER | [/app/:appId/library](#library-add)                  | n      | hhq&zyj |
| 获取项目资源 | GET     | USER | [/app/:appId/library](#library-get)                  | n      | hhq&zyj |
| 更新项目资源 | PUT     | USER | [/app/:appId/library](#library-put)                  | n      | hhq&zyj |
| 删除项目资源 | DELETE  | USER | [/app/:appId/library/path/:pathUrl](#library-delete) | n      | hhq&zyj |


 用户项目<a name="app-all"></a>
 ========
 >获取用户所有项目: 根据用户权限获取、superadmin获取所有项目、admin 获取对应userId 项目、develop获取对应userId项目
 
 #####URL

   GET /apps

#####参数列表 null

#####返回值

    {
        "code": 0,
        "msg": "success",
        "result": {
            "apps": [{
                "name": "重庆迪爱斯项目",
                "createTime": "2015-11-08",
                "endTime": "2016-06-30",
                "appId": 1,
                "authName": "xxxx",
                "adminIds": [1],
                "thumbnail": "/public/images/space.png",
                "developIds": [11],
                "width": 1920,
                "height": 1080
              }, {
                "name": "武汉东湖",
                "createTime": "2015-11-08",
                "endTime": "2016-06-30",
                "appId": 2,
                "authName": "xxxx",
                "thumbnail": "/public/images/space.png",
                "adminIds": [2],
                "developIds": [21],
                "width": 1920,
                "height": 1080
          }]
       }
     }

#####返回字段说明


| 字段名                           | 类型   | 描述             ｜
| ------------------------------- | ------ | --------------- |
| result.apps                     | Array  | 用户项目列表      |
| result.apps[0].appId            | int    | 项目id          |
| result.apps[0].name             | string | 项目名称         |
| result.apps[0].authName         | string | 项目作者         |
| result.apps[0].endTime          | Date   | 截止时间         |
| result.apps[0].createTime       | Date   | 创建时间         |
| result.apps[0].thumbnail        | string | 项目缩略图       |
| result.apps[0].developIds       | Array  | 开发人员USERID   |
| result.apps[0].adminIds         | Array  | 项目管理员USERID |

#####备注

1. superadmin 超级管理员返回所有的项目
2. admin      项目管理员返回对应项目
3. develop    开发人员返回endTime内的对应项目


创建项目<a name="app-add"></a>
========
> 创建单个项目

#####URL

  POST /app

#####参数

      {
            "name": "重庆迪爱斯项目",
            "createTime": "2015-11-08",
            "endTime": "2016-06-30",
            "authId": 1,
            "adminIds": [1],
            "thumbnail": "/public/images/space.png",
            "developIds": [11],
            "width": 1920,
            "height": 1080
        }

| 参数名      | 必要性 | 类型    | 长度  | 描述        ｜
| ---------- | ----- | ------ | ---- | -------- |
| name       | true  | string | 4-60 ｜ 项目名称   |
| endTime    | true  | Date   | 4-60 | 截止时间    |
| createTime | true  | Date   | 4-60 | 创建时间
| thumbnail  | false | string | 4-60 | 项目缩略图  |
| developIds | true  | Array  | null ｜开发人员    |
| adminIds   | true  | Array  | null | 项目管理员  |
| width      | true  | number | 4-60 | 分辨率宽    |
| height     | true  | number | 4-60 | 分辨率高    |

#####返回值

    {
      "code": 0,
      "msg": "success"
    }

#####返回字段说明 null
#####备注
1. 创建成功返回成功即可


更新项目<a name="app-put"></a>
========
> 更新单个项目

#####URL

  PUT /app/:appId

#####参数列表

    {
        "name": "迪爱斯",
        "adminIds": [1, 2],
        "developIds": [12, 23, 12]
        "endTime": "2016-06-30"
    }

| 参数名            | 必要   | 类型   | 长度  | 描述      |
| ---------------- | ----- | ------ | ---- | -------- |
| name             | false | string | 4-60 | 项目名         |
| adminIds         | false | Array  | 4-60 | 项目经理USERID |
| developIds       | false | Array  | 4-60 | 开发人员USERID |
| endTime          | false | Date   | 4-60 | 截止时间       |

#####返回值

   {
     "code": 0,
     "msg": "success"
   }

#####返回字段说明 null
#####备注 null


删除项目<a name="app-delete"></a>
========
> 删除单个项目: auth才能删除项目

#####URL

  DELETE /app/:appId

#####参数列表 null

#####返回值

    {
      "code": 0,
      "msg": "success"
    }

#####返回字段说明 null
#####备注
1.  只有创建者和管理员可以删除项目



@TODO
获取单个项目数据<a name="app-get"></a>
========
> 获取单个项目的绘图数据用于绘图

#####URL

  GET /app/:appId

#####参数列表 null

#####返回值

    {
      "code": 0,
      "msg": "success",
      "result": {

      }
    }

#####返回字段说明

#####备注

创建项目资源<a name="library-add"></a>
========
> 上传项目资源

######URL

  POST /app/:appId/library

#####参数列表

    [{
        name: "xxx.png",
        data: ""
      }, {
        name: "yyy.json",
        data: ""
    }]

| 参数名 | 必要  | 类型   | 长度  | 描述              |
| ----- | ---- | ------ | ---- | ---------------- |
| name  | true | string | 4-60 | 文件名            |
| data  | true | string | long | base64之后文件数据 |

#####返回值

    {
       "code": 0,
       "msg": "success"
    }

#####返回字段说明 null
#####备注 null


获取项目资源<a name="library-get"></a>
========
> 获取项目资源

######URL

    GET /app:appId/library

######参数列表 null

#####返回值

    {
      "code": 0,
      "msg": "success",
      "result": [{
        "name": "xxx.png",
        "path": "a/b/c/xxx.png"
        }, {
          "name": "yyy.json",
          "path": "a/b/c/yyy.json"
        }]
    }

#####返回字段说明

| 参数名           | 类型    | 描述              |
| --------------- | ------ | ----------------- |
| result          | Array  | 文件列表           |
| result[0].name  | string | 文件名             |
| result[0].path  | string | 文件可访问路径      |

#####备注null


更新项目资源<a name="library-put"></a>
========
> 更新项目资源

######URL

  PUT /app/:appId/library

#####参数列表

    [{
        name: "xxx.png",
        data: ""
      }, {
        name: "yyy.json",
        data: ""
    }]

| 参数名 | 必要  | 类型   | 长度  | 描述              |
| ----- | ---- | ------ | ---- | ---------------- |
| name  | true | string | 4-60 | 文件名            |
| data  | true | string | long | base64之后文件数据 |

#####返回值

    {
       "code": 0,
       "msg": "success"
    }

#####返回字段说明 null
#####备注 null

删除项目资源<a name="library-delete"></a>
========
> 删除项目资源：只能单独删除一个文件

######URL

  DELETE /app/:appId/library/path/:pathUrl

#####参数列表 null

#####返回值

    {
       "code": 0,
       "msg": "success"
    }

#####返回字段说明 null
#####备注 null

