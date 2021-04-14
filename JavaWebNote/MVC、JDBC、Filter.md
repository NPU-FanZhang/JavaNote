# MVC、JDBC、Filter

## MVC

### 1.早些年的项目结构





### 2.MVC三层架构





Model

- 业务处理： 业务逻辑
- 数据持久层：CRUD（DAO）

View

- 展示数据
- 提供链接发起Servlet请求（a、form 、img...)

Controller

- 接收用户请求(req: 请求参数   Session信息....)
- 交给业务层处理对应代码
- 控制视图的跳转



