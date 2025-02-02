# protoc-gen-go-gin

> 修改自 [kratos v2](https://github.com/go-kratos/kratos/tree/main/cmd/protoc-gen-go-http)

从 protobuf 文件中生成使用 gin 的 http rpc 服务
## 安装

请确保安装了以下依赖:

- [go 1.16](https://golang.org/dl/)
- [protoc](https://github.com/protocolbuffers/protobuf)
- [protoc-gen-go](https://github.com/protocolbuffers/protobuf-go)

注意由于使用 embed 特性，Go 版本必须大于 1.16

```bash
go install github.com/mohuishou/protoc-gen-go-gin@latest
```

## 使用说明

例子见: [example](./example)

### proto 文件约定

默认情况下 rpc method 命名为 方法+资源，使用驼峰方式命名，生成代码时会进行映射

方法映射方式如下所示:

- `"GET", "FIND", "QUERY", "LIST", "SEARCH"`  --> GET
- `"POST", "CREATE"`  --> POST
- `"PUT", "UPDATE"`  --> PUT
- `"DELETE"`  --> DELETE

```protobuf
service BlogService {
  rpc CreateArticle(Article) returns (Article) {}
  // 生成 http 路由为 post: /article
}
```

除此之外还可以使用 google.api.http option 指定路由，可以通过添加 additional_bindings 使一个 rpc 方法对应多个路由

```protobuf
// blog service is a blog demo
service BlogService {
  rpc GetArticles(GetArticlesReq) returns (GetArticlesResp) {
    // 
    // 可以通过添加 additional_bindings 使一个 rpc 方法对应多个路由
    option (google.api.http) = {
      get: "/v1/articles"
      additional_bindings {
        get: "/v1/author/{author_id}/articles"
      }
    };
  }
}
```

### 文件生成

```bash
  protoc -I ./example/api \
  --go_out ./example/api --go_opt=paths=source_relative \
  --go-gin_out ./example/api --go-gin_opt=paths=source_relative \
  example/api/product/app/v1/v1.proto
```

## 相关介绍

## 支持 validate
在原来基础上增加了对protoc-gen-validate的支持, 具体格式可见: https://github.com/envoyproxy/protoc-gen-validate
