# 帖子相关接口列表

本文档主要列出帖子相关接口。

帖子频道是一种新的服务器频道类型，允许用户在内产生帖子内容，沉淀知识、经验与分享到服务器内。

| 关键词   | 解释     |
|-------|--------|
| 主楼	    | 特指帖子频道的帖子主楼，为发帖者在发送帖子时的内容。|
| 回复  | 指帖子的一级回复，所有回复某条帖子的内容。 |
| 楼中楼 | 指帖子回复的回复|
分区（Category）分为全部（综合）与用户自行创建的分区。 在发帖时，可选指定一个存在的有权限的分区发送。 若不选择，则视为其“未选择分区”，仅会在“综合”页面显示。若用户选择了分区，则会同时显示在“综合”页面与对应的分区页面。

帖子（Thread）相当于一个区域，所有评论/回复（包括主楼）都属于该帖子。主楼和回复使用图文混排的模式呈现内容，楼中楼仅支持KMD和表情。

本文档中的接口均符合接口规范，如有疑问，建议先查阅[接口引言](https://developer.kookapp.cn/doc/reference)。

| 接口                                         | 接口说明            | 维护状态 |
|--------------------------------------------|-----------------| -------- |
| [/api/v3/category/list](#获取帖子分区列表)             | 获取帖子分区列表          | 正常     |
| [/api/v3/thread/create](#创建帖子)             | 创建帖子            | 正常     |
| [/api/v3/thread/reply](#评论/回复)             | 评论/回复           | 正常     |


## Category帖子分区详情参数说明

| 名称       | 类型   | 说明                |
|----------| ------ |-------------------|
| id       | string | 帖子分区ID            |
| name     | string | 分区名               |
| allow    | int    | 默认为 0,想要设置的允许的权限值 |
| deny | int    | 默认为 0,想要设置的拒绝的权限值 |
| roles     | object | 角色或者用户在该分区下的权限值   |

## Thread帖子详情参数说明

 帖子属于某个分区，若无则默认综合分区，帖子相当于一个区域，所有评论/回复（包括主楼）都属于该帖子。

| 参数名             | 类型      | 说明                                   |
| ------------------ |---------|--------------------------------------|
| id                 | string  | 帖子 id                                |
| status               | string  | 帖子状态, `1`代表审核中，`2`代表审核通过, `3`代表编辑审核中 |
| is_updated        | boolean | 是否被编辑                                |
| title        | string  | 标题                                   |
| cover            | string  | 封面url                                |
| category              | string  | 分区id                                 |


## Post评论/回复详情参数说明

| 参数名             | 类型      | 说明                                   |
| ------------------ |---------|--------------------------------------|
| id                 | string  | 评论/回复 id                             |
| category_id               | string  | 分区id                                 |
| thread_id              | string  | _所属帖子id_                             |
| reply_id            | string  | 回复对象的id（回复主贴为0）                      |
| belong_to_post_id               | string  | 所属的评论的post_id                        |
| content        | string  | 卡片消息                                 |
| status             | string  | 回复状态, `1`代表审核中，`2`代表审核通过, `3`代表编辑审核中 |
| mention           | array   | `@特定用户` 的用户 ID 数组                                 |
| mention_all           | boolean | 是否含有 `@全体人员`                         |
| mention_here           | boolean | 是否含有 `@在线人员`                         |
| mention_part              | array   | `@特定用户` 详情                           |
| mention_role_part              | array   | `@特定角色` 详情                           |

## 获取帖子分区列表

### 接口说明

| 地址                           | 请求方式  | 说明                 |
| ----------------------------- | -------- | ------------------- |
| `/api/v3/category/list` | GET      |  |

### 参数列表

| 参数名     | 位置  | 类型   | 必需  | 说明      |
| ---------- | ----- | ------ | ----- |---------|
| channel_id   | query | string | true  | 帖子频道 id |

### 返回参数说明

返回值字段参考 [Category帖子分区详情参数说明](#Category帖子分区详情参数说明)

### 返回示例

```json
{
  "code":0,
  "message":"操作成功",
  "data":{
    "list":[
      {
        "id":"6",
        "name":"test",
        "allow":"0",
        "deny":"0",
        "roles":[]
      },
      {
        "id":"7",
        "name":"test",
        "allow":"0",
        "deny":"2048",
        "roles":[
          {
            "type":"user",
            "role_id":0,
            "user_id":"111111",
            "allow":2048
          },
          {
            "type":"role",
            "role_id":11111,
            "user_id":"",
            "allow":2048
          }
        ]
      },
      {
        "id":"8",
        "name":"test2",
        "allow":"0",
        "deny":"0",
        "roles":[]
      }
    ]
  }
}
```

## 创建帖子

### 接口说明

| 地址                 | 请求方式 | 说明 |
| -------------------- |------| ---- |
| `/api/v3/thread/create` | POST |      |

### 参数列表

| 参数名   | 位置    | 类型       | 必需    | 说明                 |
| -------- |-------|----------|-------|--------------------|
| channel_id | body  | string   | true  | 频道 id              |
| guild_id | body | string   | true  | 服务器 id             |
| category_id | body | string   | false | 帖子分区 id（若无默认为综合分区） |
| title | body | string   | true  | 标题                 |
| cover | body | string   | false | 封面url              |
| content | body | string   | true  | 卡片消息内容             |
| tag_ids | body | string[] | false | 话题id列表             |

### 返回参数说明

| 参数名             | 类型      | 说明                                                                   |
| ------------------ |---------|----------------------------------------------------------------------|
| is_updated        | boolean | 是否被编辑                                                                |
| post_id             | string  | 主楼id                                                                 |
| medias | array   | 卡片消息附加图片数组                                                           |
| preview_content          | string  | 预览文本                                                                 |
| content_deleted              | bool    | 主楼是否被删除                                                              |
| content_deleted_type              | integer | 被删除类型                                                                |
| reactions              | array   | 回应数组                                                                 |
| reaction_num              | integer | 回应数                                                                  |
| user              | map     | 创建帖子用户数据参考[用户 User](https://developer.kookapp.cn/doc/objects#用户User) |
| category              | map     | 分区字段参考 [Category帖子分区详情参数说明](#Category帖子分区详情参数说明)                     |
| tags              | array   | 话题数组                                                                 |
其余返回值字段参考 [Thread帖子详情参数说明](#Thread帖子详情参数说明)以及[Post评论/回复详情参数说明](#Post评论回复详情参数说明)
### 返回示例

```json
{
  "code": 0,
  "message": "操作成功",
  "data": {
    "id": "xxxxxx",
    "status": 2,
    "mention": [],
    "mention_all": false,
    "mention_here": true,
    "is_updated": false,
    "post_id": "xxxxx",
    "title": "testtitle",
    "cover": "https://img.chuanyuapp.com/assets/xxxxx",
    "content": "",
    "medias": [],
    "preview_content": "testcontent @在线成员",
    "content_deleted": false,
    "content_deleted_type": 0,
    "is_collected": 0,
    "user": {},
    "category": {},
    "tags": [],
    "mention_part": [],
    "mention_role_part": [],
    "channel_part": [],
    "item_part": []
  }
}
```
## 评论/回复

### 接口说明

| 地址                      | 请求方式 | 说明 |
| ------------------------- |------| ---- |
| `/api/v3/thread/reply` | POST  |      |

### 参数列表

| 参数名          | 位置    | 类型    | 必需    | 说明                                 |
| ------------- |-------| ------- |-------|------------------------------------|
| channel_id	   | body  | string  | true  | 频道 id                              |
| thread_id	    | body | string  | true  | 帖子 id                              |
| reply_id        | body | string  | false | 回复的post_id，如果是评论主楼则不传，回复其它楼和楼中楼则必传 |
| content       | body | string | true  | 文本                                 |


### 返回参数说明

返回值字段参考 [Post评论/回复详情参数说明](#Post评论回复详情参数说明)

### 返回示例

```json
{
  "code": 0,
  "message": "操作成功",
  "data": {
    "id": "xxxxxx",
    "reply_id": "0",
    "thread_id": "xxxx",
    "is_updated": false,
    "mention": [],
    "mention_all": false,
    "mention_here": false,
    "content": "",
    "mention_part": [],
    "mention_role_part": [],
    "channel_part": [],
    "item_part": []
  }
}
```











