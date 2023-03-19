> 服务器后端协议设计规范:

> mysql 设计规范

- 表名：

  1. 表名格式：admin_user
  2. 日志系统等不涉及到更新的表可以使用 MyIsam
  3. 其他涉及到更新操作的表必须使用 InnoDB
  4. 创建表必须要有 Comment 和 Engine 声明

- 表字段名

  1. 字段名格式：create_time
  2. 所有字段必须要有 Comment 描述
  3. 所有非自增主键必须要有默认值设计
  4. varchar 类型 255 满足要求不允许使用 256
  5. 表字段名称不允许使用 mysql 关键字
  6. 时间类型必须使用 int 类型

- 表索引

  1. 索引名称格式：
     - 唯一索引:uniq_username|uniq_uname
     - 非唯一索引:idx_username|idx_uname
     - 组合索引可以使用字段名缩写: idx*ageuname*
  2. 枚举值字段禁止使用索引(如性别、可控范围状态状态值等)
  3. 禁止使用外键
  4. 单表索引个数不允许超过 5 个
  5. 联合索引两原则
     - 最左前缀原则 (abc)=(a)+(ab)+(abc)
     - 离散度(区分度)最大的字段优先放在前面
  6. 合理使用覆盖索引,减少回表查询相关网络 IO

- 慢 sql 相关

  1. 自测库数据必须>100w
  2. 时间大于 1s 的 sql 必须优化及同步周知
  3. 核心业务表时间大于 500ms 必须 Uoui

- 示例如下:

  ```
  CREATE TABLE `admin_user` (
    `uid` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '用户id',
    `username` varchar(32) NOT NULL DEFAULT 'admin' COMMENT '用户名',
    `password` varchar(255) NOT NULL DEFAULT '' COMMENT '密码',
    `status` tinyint(1) DEFAULT '0' COMMENT '账号状态：0-正常 1-删除',
    `role` tinyint(1) DEFAULT '2' COMMENT '用户角色 1:超级管理员 2:普通管理员',
    `auth` json DEFAULT NULL COMMENT '用户权限 0:禁止 1:只读 2:读写 3.读写+删除',
    `create_time` int(11) NOT NULL DEFAULT '0' COMMENT '创建时间,单位ms',
    `modify_time` int(11) NOT NULL DEFAULT '0' COMMENT '修改时间,单位ms',
    PRIMARY KEY (`uid`) USING BTREE,
    UNIQUE KEY `uniq_username` (`username`) USING BTREE
  ) ENGINE=InnoDB AUTO_INCREMENT=1024 COMMENT='管理端用户表';
  ```
