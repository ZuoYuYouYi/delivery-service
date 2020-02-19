#### ● 业务介绍：一个用于共享快递信息，为校友方便快递“代领”的信息共享平台。
#### ● 技术架构：
* [ ]  开发工具：Maven（项目构建管理）、Git（版本控制）、Nginx（反向代理服务器）
* [ ]  开发环境：JDK8、MySql5.5
* [ ]  后端技术：springBoot（容器）、SpringMVC（MVC框架）、SpringSecurity（安全框架）、JWT（身份验证方          案）、Mybatis（ORM框架）、Mybatis Generator（代码生成）、Mapper（Mybatis辅助插件）、C3P0（数据库连接池）、Redis（缓存数据库）、FastDFS（文件系统）、Logback（日志组件）
#### ● 项目问题及解决方案：
1. 项目中没有使用微信官方的登陆接口，最初基于SpringSecurity的sessionID作为Cookie来保持用户状态；但上线后发现苹果手机对在网络通讯格式上有微小的差异，导致苹果手机无法登录；最后基于SpringSecurity的权限管理功能并结合JWT的身份验证方式解决了该问题。
2. 在多个人同一时间接收同一个订单时，会发生一个订单被多人接收的情况，违背了一个订单只能被一人接收的规则。在这里我利用Redis键的唯一性特征解决了这一问题——当一个订单被用户接收时，先检测MySql数据库该订单的状态；如果为未被接收状态，则检测Redis内有没有以该订单ID作为Key值的键；如果没有，将该订单ID作为Key值存入Redis；再进行写入数据库、发送短信通知等操作；最后待该事务完成后，删除掉该Key值。
3. 在将项目的身份认证更换为JWT方式后，不必再让Spring维护用户状态（session），因此我关闭了session管理器；从而导致原本以session为生命周期范围的短信验证码服务无法正常工作（原本bean以session为生命周期，并创建一个线程5分钟后清除验证码值）。最后利用Redis的限时键值对功能，将手机号作为Key，验证码作为Value，有效时间为5分钟；解决了该问题。
zuoyu