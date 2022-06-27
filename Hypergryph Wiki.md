# 1.权限

## 对”人“相关：

### **人员管理**

| 身份                               | 可管理人员范围                                | 操作权限           |
| :--------------------------------- | --------------------------------------------- | ------------------ |
| admin                              | 系统所有用户                                  | 激活冻结，设置部门 |
| 已设置部门并且有canAdmin权限的用户 | 部门及子部门用户&未设置部门的用户（自身除外） | 激活冻结，设置部门 |

### Project设置

| 身份          | 可管理人员范围             | 操作权限                        |
| ------------- | -------------------------- | ------------------------------- |
| admin         | 系统所有用户               | 设置所有project下的制作人和编辑 |
| project制作人 | 受人员管理的可管理人员约束 | 设置所属project下的编辑         |

### 用户组管理（暂时弃用）



### 单一词条可见性设置

| 身份                                               | 可管理人员范围                                | 操作权限               |
| -------------------------------------------------- | --------------------------------------------- | ---------------------- |
| admin                                              | 系统所有用户                                  | 添加当前词条的查看权限 |
| 已设置部门并且有canAdmin权限或者isLeader权限的用户 | 部门及子部门用户&未设置部门的用户（自身除外） | 添加当前词条的查看权限 |
| 当前词条所属的project的制作人/编辑                 | 部门及子部门用户&未设置部门的用户（自身除外） | 添加当前词条的查看权限 |



## 对词条与project相关：

### Project树节点可见性

| 身份               | 可见范围                                                     |
| ------------------ | ------------------------------------------------------------ |
| admin              | 全可见                                                       |
| project制作人/编辑 | 所属project & 其他有公开词条所属project &其他被分配了查看权限的词条所属的project |
| 其他用户           | 公开词条所属project & 其他被分配了查看权限词条所属的project  |

### 词条可见性

| 身份               | 可见范围                                                     |
| ------------------ | ------------------------------------------------------------ |
| admin              | 全可见                                                       |
| project制作人/编辑 | 所属project全部词条 & 其他project公开词条 & 其他project被分配了查看权限的词条 |
| 其他用户           | 公开词条 & 被分配了查看权限的词条                            |

### 词条编辑

| 身份          | 条件                      |
| ------------- | ------------------------- |
| admin         | 编辑任意词条              |
| project编辑   | 可编辑该project下任意词条 |
| project制作人 | 可编辑该project下任意词条 |
| 其他人        | 无法编辑                  |





```js
//TODO 
//project preset的词条权限拆分
//身份=》包含哪些权限（

//词条的readable ->可以做成guard

// 对词条进行查看增加rules 验证用户是否可见词条

//authCollect的写法  let [] foreach push 可以直接用map/reduce返回

//编辑也可以对词条的查看权限进行分配

//getProjects:系统管理员的话可以看到全部

//词条的公开非公开 =》 加载时的默认值没加

//添加用户时，后端验证所添加的用户是不是当前权限限制下可管理的用户

//添加词条rules时，后端验证所添加的用户 + 词条 是不是当前权限限制下可管理的词条/用户

//显示词条rules时，根据是不是自己权限范围内的rules做是否可以删除的验证

//设置用户department时，同样，对接口的用户 / 和 department做双双验证是不是可管理的用户、department

//group相关同样是
```

```js
// TODO ADD LOGS
-   [X] DELETE /api/project/:projectKey/users/:userId
-   [X] PUT /api/project/:projectKey/users
-   [X] PATCH /api/users/:userId
-   [X] DELETE /api/groups/:groupId
-   [X] PUT /api/groups/:groupId/users
-   [X] DELETE /api/groups/:groupId/users/:userId
-   [X] PUT /api/users/:userId/department
-   //[ ] PATCH /api/term/:id/rules
-   [X] PUT /api/term/:id/rules
-   [X] DELETE /api/term/:id/rules/:ruleId
-   [X] PUT /api/term/:id
提问，记录的是业务日志还是API日志，如果是请求出错应该如何拦截，如果是业务日志，重复操作应该如何捕获
// 目前是用业务日志，即logs记录请求的结果而不是参数
```

有关系统日志：

具体情况具体分析

业务日志：实际操作结果进行记录

API日志：对每一次请求进行记录

```
interface ITableTransferProps<T> extends TransferProps<T> {
    leftColumns: TableColumnProps<T>[]
    rightColumns: TableColumnProps<T>[]
    dataSource: T[]
}

// export function TableTransfer<T>(props: ITableTransferProps<T>) {
//     return <></>
// }
```

**在对树结构功能进行设计时，需要考虑排序的问题，**

**临时想到的解决办法是，加一个order字段**



词条页相关：



