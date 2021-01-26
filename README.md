# errors

```
import "github.com/ntt360/errors"
```

本项目 fork 自 `github.com/pkg/errors` 库，对于默认的错误消息输出部分做了调整。原库默认会打印出自定义和原始错误。如下：

```go
func (w *withMessage) Error() string { return w.msg + ": " + w.cause.Error() }
```

**变更为：**

```go
func (w *withMessage) Error() string {
    if len(w.msg) >= 0 {
        return w.msg
    } else {
        return w.Error()
    }
}
```

**变更原因：**

1. C端用户只显示语义化后的错误信息，增强用户体验，同时避免泄露服务本地敏感错误信息。


## 使用示例

1. 包装错误或者构建错误：

```go
func someWork(id int) error {
    err := app.Db().First(&models.User{}, id).Error
    if err != nil {
        // 自定义错误信息
        return errors.Wrap(err, "try find user failed")
    }
    ...
}
```

2. 错误定制输出

```go
func test() string {
    err := someWork(1)
    if err != nil {
        // 日志记录错误 + 栈信息
        log.Printf("%+v", err)

        // 日志记录原始错误
        log.Println(errors.Cause(err))

        // 输出包装后语义错误给用户
        return err.Error()
   }
}
```
