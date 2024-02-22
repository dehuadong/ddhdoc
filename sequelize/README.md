---
sidebarDepth: 2
---

# Sequelize Docs 中文版

::: tip
数据库引擎支持 v6
:::

|     引擎      |                             支持的最低版本                             |
| :-----------: | :--------------------------------------------------------------------: |
|    Postgre    |              [9.5 ](https://www.postgresql.org/docs/9.5/)              |
|     MySQL     |            [5.7](https://dev.mysql.com/doc/refman/5.7/en/)             |
|    MariaDB    | [10.1](https://mariadb.com/kb/en/changes-improvements-in-mariadb-101/) |
| Microsoft SQL |                              `12.0.2000`                               |
|    SQLite     |              [3.0](https://www.sqlite.org/version3.html)               |

## 简单示例

```js
const { Sequelize, Model, DataTypes } = require('sequelize');
const sequelize = new Sequelize('sqlite::memory:');

class User extends Model {}
User.init(
  {
    username: DataTypes.STRING,
    birthday: DataTypes.DATE,
  },
  { sequelize, modelName: 'user' }
);

(async () => {
  await sequelize.sync();
  const jane = await User.create({
    username: 'janedoe',
    birthday: new Date(1980, 6, 20),
  });
  console.log(jane.toJSON());
})();
```

请通过 [Getting started - 入门](core-concepts/getting-started.md) 来学习更多相关内容. 如果你想要学习 Sequelize API 请通过 [API 参考](https://sequelize.org/v6/identifiers.html) (英文).
