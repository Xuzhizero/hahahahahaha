---
title: http请求输出出现问题故障排查报告
slug: atidf74skno57dyy
url: https://www.yuque.com/caogongzi/pu6995/atidf74skno57dyy
---

首先要明确： 代码执行节点的输入和http请求的输出是不一样的。前者是只拿body字段，后者是全部输出

# 对比实验1
输入1（测试过程中的输入值）：

![](https://cdn.nlark.com/yuque/0/2025/png/2408029/1745312757528-8111435d-ddf0-459e-92b0-28f670164bae.png)

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);">{ "input": "{\"query\": {\"content\": \"\\u4eba\\u4e8b USV\"}, \"records\": [{\"segment\": {\"id\": \"9e13d9b3-01a7-45dd-bf85-6fa9c598a9de\", \"position\": 1, \"document_id\": \"3138a36e-eb0a-4280-984c-9c027465d0bd\", \"content\": \"\\u4eba\\u4e8b\\u5bf9USV\", \"sign_content\": \"\\u4eba\\u4e8b\\u5bf9USV\", \"answer\": null, \"word_count\": 6, \"tokens\": 5, \"keywords\": [\"text\"], \"index_node_id\": \"4eb86ad3-e6ce-4458-9c54-7616af047e9a\", \"index_node_hash\": \"17b50884348de1a3671e0c891bd9ec95cf02825aebeb0987f62737f47991d446\", \"hit_count\": 0, \"enabled\": true, \"disabled_at\": null, \"disabled_by\": null, \"status\": \"completed\", \"created_by\": \"710d8049-714f-4813-9a59-554c72795471\", \"created_at\": 1744971410, \"indexing_at\": 1745215845, \"completed_at\": 1745215845, \"error\": null, \"stopped_at\": null, \"document\": {\"id\": \"3138a36e-eb0a-4280-984c-9c027465d0bd\", \"data_source_type\": \"upload_file\", \"name\": \"text\", \"doc_type\": null, \"doc_metadata\": null}}, \"child_chunks\": null, \"score\": 0.8666905, \"tsne_position\": null}, {\"segment\": {\"id\": \"9e13d9b3-01a7-45dd-bf85-6fa9c598a9de\", \"position\": 1, \"document_id\": \"3138a36e-eb0a-4280-984c-9c027465d0bd\", \"content\": \"\\u4eba\\u4e8b\\u5bf9USV\", \"sign_content\": \"\\u4eba\\u4e8b\\u5bf9USV\", \"answer\": null, \"word_count\": 6, \"tokens\": 5, \"keywords\": [\"text\"], \"index_node_id\": \"4eb86ad3-e6ce-4458-9c54-7616af047e9a\", \"index_node_hash\": \"17b50884348de1a3671e0c891bd9ec95cf02825aebeb0987f62737f47991d446\", \"hit_count\": 0, \"enabled\": true, \"disabled_at\": null, \"disabled_by\": null, \"status\": \"completed\", \"created_by\": \"710d8049-714f-4813-9a59-554c72795471\", \"created_at\": 1744971410, \"indexing_at\": 1745215845, \"completed_at\": 1745215845, \"error\": null, \"stopped_at\": null, \"document\": {\"id\": \"3138a36e-eb0a-4280-984c-9c027465d0bd\", \"data_source_type\": \"upload_file\", \"name\": \"text\", \"doc_type\": null, \"doc_metadata\": null}}, \"child_chunks\": null, \"score\": null, \"tsne_position\": null}]}\n" }</font>

报错。报错码：

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);">{ "$error": "Function execution failed, please check the code of the function. Detail: Exception: ('Error in main: ', \"'body'\")\n" }</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);"></font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);">输入2：（通过将输入1的内容直接复制到节点中测试运行）</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);">{ "input": "{\"query\": {\"content\": \"\\u4eba\\u4e8b USV\"}, \"records\": [{\"segment\": {\"id\": \"9e13d9b3-01a7-45dd-bf85-6fa9c598a9de\", \"position\": 1, \"document_id\": \"3138a36e-eb0a-4280-984c-9c027465d0bd\", \"content\": \"\\u4eba\\u4e8b\\u5bf9USV\", \"sign_content\": \"\\u4eba\\u4e8b\\u5bf9USV\", \"answer\": null, \"word_count\": 6, \"tokens\": 5, \"keywords\": [\"text\"], \"index_node_id\": \"4eb86ad3-e6ce-4458-9c54-7616af047e9a\", \"index_node_hash\": \"17b50884348de1a3671e0c891bd9ec95cf02825aebeb0987f62737f47991d446\", \"hit_count\": 0, \"enabled\": true, \"disabled_at\": null, \"disabled_by\": null, \"status\": \"completed\", \"created_by\": \"710d8049-714f-4813-9a59-554c72795471\", \"created_at\": 1744971410, \"indexing_at\": 1745215845, \"completed_at\": 1745215845, \"error\": null, \"stopped_at\": null, \"document\": {\"id\": \"3138a36e-eb0a-4280-984c-9c027465d0bd\", \"data_source_type\": \"upload_file\", \"name\": \"text\", \"doc_type\": null, \"doc_metadata\": null}}, \"child_chunks\": null, \"score\": 0.8666905, \"tsne_position\": null}, {\"segment\": {\"id\": \"9e13d9b3-01a7-45dd-bf85-6fa9c598a9de\", \"position\": 1, \"document_id\": \"3138a36e-eb0a-4280-984c-9c027465d0bd\", \"content\": \"\\u4eba\\u4e8b\\u5bf9USV\", \"sign_content\": \"\\u4eba\\u4e8b\\u5bf9USV\", \"answer\": null, \"word_count\": 6, \"tokens\": 5, \"keywords\": [\"text\"], \"index_node_id\": \"4eb86ad3-e6ce-4458-9c54-7616af047e9a\", \"index_node_hash\": \"17b50884348de1a3671e0c891bd9ec95cf02825aebeb0987f62737f47991d446\", \"hit_count\": 0, \"enabled\": true, \"disabled_at\": null, \"disabled_by\": null, \"status\": \"completed\", \"created_by\": \"710d8049-714f-4813-9a59-554c72795471\", \"created_at\": 1744971410, \"indexing_at\": 1745215845, \"completed_at\": 1745215845, \"error\": null, \"stopped_at\": null, \"document\": {\"id\": \"3138a36e-eb0a-4280-984c-9c027465d0bd\", \"data_source_type\": \"upload_file\", \"name\": \"text\", \"doc_type\": null, \"doc_metadata\": null}}, \"child_chunks\": null, \"score\": null, \"tsne_position\": null}]}\n" }</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);">（因为是复制过来的，所以和输入1没有区别）</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);">报错：报错码和输入1的情况是一样的。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);"></font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);"></font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);"></font>

# 实验2
直接对http请求节点输出内容。内容如下：

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);">{ "body": "{\"query\": {\"content\": \"\\u4eba\\u4e8b USV\"}, \"records\": [{\"segment\": {\"id\": \"9e13d9b3-01a7-45dd-bf85-6fa9c598a9de\", \"position\": 1, \"document_id\": \"3138a36e-eb0a-4280-984c-9c027465d0bd\", \"content\": \"\\u4eba\\u4e8b\\u5bf9USV\", \"sign_content\": \"\\u4eba\\u4e8b\\u5bf9USV\", \"answer\": null, \"word_count\": 6, \"tokens\": 5, \"keywords\": [\"text\"], \"index_node_id\": \"4eb86ad3-e6ce-4458-9c54-7616af047e9a\", \"index_node_hash\": \"17b50884348de1a3671e0c891bd9ec95cf02825aebeb0987f62737f47991d446\", \"hit_count\": 0, \"enabled\": true, \"disabled_at\": null, \"disabled_by\": null, \"status\": \"completed\", \"created_by\": \"710d8049-714f-4813-9a59-554c72795471\", \"created_at\": 1744971410, \"indexing_at\": 1745215845, \"completed_at\": 1745215845, \"error\": null, \"stopped_at\": null, \"document\": {\"id\": \"3138a36e-eb0a-4280-984c-9c027465d0bd\", \"data_source_type\": \"upload_file\", \"name\": \"text\", \"doc_type\": null, \"doc_metadata\": null}}, \"child_chunks\": null, \"score\": null, \"tsne_position\": null}, {\"segment\": {\"id\": \"9e13d9b3-01a7-45dd-bf85-6fa9c598a9de\", \"position\": 1, \"document_id\": \"3138a36e-eb0a-4280-984c-9c027465d0bd\", \"content\": \"\\u4eba\\u4e8b\\u5bf9USV\", \"sign_content\": \"\\u4eba\\u4e8b\\u5bf9USV\", \"answer\": null, \"word_count\": 6, \"tokens\": 5, \"keywords\": [\"text\"], \"index_node_id\": \"4eb86ad3-e6ce-4458-9c54-7616af047e9a\", \"index_node_hash\": \"17b50884348de1a3671e0c891bd9ec95cf02825aebeb0987f62737f47991d446\", \"hit_count\": 0, \"enabled\": true, \"disabled_at\": null, \"disabled_by\": null, \"status\": \"completed\", \"created_by\": \"710d8049-714f-4813-9a59-554c72795471\", \"created_at\": 1744971410, \"indexing_at\": 1745215845, \"completed_at\": 1745215845, \"error\": null, \"stopped_at\": null, \"document\": {\"id\": \"3138a36e-eb0a-4280-984c-9c027465d0bd\", \"data_source_type\": \"upload_file\", \"name\": \"text\", \"doc_type\": null, \"doc_metadata\": null}}, \"child_chunks\": null, \"score\": 0.8666905, \"tsne_position\": null}]}\n", "statusCode": 200, "headers": "{\"Access-Control-Allow-Origin\":\"*\",\"Cf-Cache-Status\":\"DYNAMIC\",\"Cf-Ray\":\"934306fb8ac5281a-SEA\",\"Content-Type\":\"application/json\",\"Date\":\"Tue, 22 Apr 2025 06:22:34 GMT\",\"Nel\":\"{\\\"success_fraction\\\":0,\\\"report_to\\\":\\\"cf-nel\\\",\\\"max_age\\\":604800}\",\"Report-To\":\"{\\\"endpoints\\\":[{\\\"url\\\":\\\"https:\\\\/\\\\/a.nel.cloudflare.com\\\\/report\\\\/v4?s=W5FScjhGpQVGy3dQHOtSKXyixRsR40%2FGq6GU3INK%2B1nADe%2FdAcl8t9soIx4d5pNx0VTvIEBIV60Fz0AHTjX26lEGG6PHy8QJZVLMLuXu91IDyjAJw%2Bq1kyGuqVMt\\\"}],\\\"group\\\":\\\"cf-nel\\\",\\\"max_age\\\":604800}\",\"Server\":\"cloudflare\",\"Server-Timing\":\"cfL4;desc=\\\"?proto=TCP\\u0026rtt=175893\\u0026min_rtt=175884\\u0026rtt_var=37118\\u0026sent=8\\u0026recv=11\\u0026lost=0\\u0026retrans=0\\u0026sent_bytes=3185\\u0026recv_bytes=1003\\u0026delivery_rate=23844\\u0026cwnd=255\\u0026unsent_bytes=0\\u0026cid=c32bddf217e462ee\\u0026ts=2225\\u0026x=0\\\"\",\"Strict-Transport-Security\":\"max-age=31536000; includeSubDomains\",\"X-Content-Type-Options\":\"nosniff\",\"X-Env\":\"PRODUCTION\",\"X-Frame-Options\":\"DENY\",\"X-Version\":\"1.2.0\"}" }</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);"></font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);">直接将节点输出内容放入代码执行节点，结果：</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);">顺利运行，没有报错。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);"></font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);">综合对比实验1和实验2，我们发现，实验1和实验2的输入文本是不一样的。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);"></font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);">为什么会不一样？分析结构体。</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(247, 247, 252);">对于实验1的输入，结构体结构为</font>

```plain
{
  "input": {                                  // 用户输入容器（需二次解析）
    "query": {
      "content": "string(UTF-8)"            // 用户原始查询文本（示例："人事 USV"）
    },
    "records": [                            // 输入文档片段列表
      {
        "segment": {                        // 文档片段元数据
          "id": "uuid",                     // 片段唯一标识符
          "position": "integer",            // 片段在文档中的位置序号
          "document_id": "uuid",            // 所属文档ID
          "content": "string",              // 原始文本内容
          "sign_content": "string",         // 签名文本（哈希校验用）
          "word_count": "integer",          // 词语统计数
          "tokens": "integer",              // Token数量
          "keywords": ["string"],           // 系统提取的关键词列表
          "index_node_id": "uuid",          // 索引节点ID
          "index_node_hash": "sha256",      // 索引节点哈希值
          "hit_count": "integer",           // 历史命中次数
          "status": "enum(completed|error)",// 处理状态
          "created_at": "timestamp",        // 创建时间戳（UNIX秒）
          "document": {                     // 关联文档元数据
            "id": "uuid",                   // 文档ID
            "data_source_type": "enum(upload_file|api)", // 数据源类型
            "name": "string"                // 文档名称
          }
        },
        "score": "float|null",              // 匹配分数（若存在）
        "child_chunks": "null",             // 子片段容器（未启用）
        "tsne_position": "null"             // 可视化坐标占位
      }
    ]
  },
```

对于实验2的输入，结构体的结构为：

```plain
// 完整API响应结构（标准化代码块）
{
  "body": {                                  // 核心数据容器（需二次解析）
    "query": {
      "content": "string"                   // UTF-8解码后的用户原始查询
    },
    "records": [
      {
        "segment": {
          "id": "uuid",                     // 片段唯一标识符
          "content": "string",              // 匹配的文本内容
          "document_id": "uuid",            // 父文档ID
          "document": {                     // 文档元数据
            "data_source_type": "enum(upload_file|api|db)",  // 数据源类型
            "name": "string"                // 文档显示名称
          }
        },
        "score": "float|null",              // 相关性评分(0-1)
        "child_chunks": "null",             // 保留字段
        "tsne_position": "null"             // 可视化坐标占位
      }
    ]
  },
  
  "statusCode": "integer(200|4xx|5xx)",     // HTTP状态标识
  
  "headers": {                              // 网络层元数据（需二次解析）
    "Access-Control-Allow-Origin": "string",// CORS策略
    "Content-Type": "string",               // 响应格式声明
    "Server": "string",                     // 服务端标识
    "X-Env": "enum(PRODUCTION|STAGING)",    // 环境标记
    "CDN-Cache": "string"                   // 缓存状态示例
  }
}

/* 字段验证规则
1. body.query.content 必须存在且为非空字符串
2. records数组元素必须包含完整的segment元数据
3. 当score字段存在时，必须满足 0 ≤ score ≤ 1
4. headers必须包含Content-Type且值为application/json
5. statusCode为200时，records数组至少包含1个元素 */
```



结构上不同，为什么会导致输入1无法被识别，输入2可以被识别？



当前代码：

```plain
async def main(args: Args) -> Output:
    import json
    params = args.params
    # params['input'] = params['input'].replace('\\\\', '\\')
    data = json.loads(params['input'])
    
    # 提取 body 字段并再次解析
    body_data = json.loads(data["body"])
    

    # # 提取 records 字段
    # records = body_data["records"]

    content_list = []
    content = ""
    # # 遍历 records 并提取每个 segment 的 content
    # for record in records:
    #     segment = record["segment"]
    #     content = segment["content"]
    #     print("Segment Content:")
    #     print(content)
    #     print("-" * 50)
    #     content_list.append(content)

    
    # 构建输出对象
    ret: Output = {
        "content": content, # 最后一个content内容
        "content_list": content_list, #content的收纳数组
    }
    return ret
```



原因在于，输入1中，没有body字段。



那么对于输入1，我们只要直接获取records中的segments中的content即可。




