

登录功能接口

| 请求方式 | 请求地址 | 描述 |
| --- | --- | --- |
| post | /adminLogin | 管理员认证 |
| get | /toAdminMain | 跳转到管理员首页 |
| get | /getCode | 获取验证码图片 |
| get | /toUserLogin | 跳转到用户登录 |
| post | /userLogin | 会员认证页面 |
| get | /toUserMain | 跳转会员首页 |

会员页面功能接口

| 请求方式 | 请求地址 | 描述 |
| --- | --- | --- |
| get | /user/toUserInfo | 跳转个人信息页面 |
| get | /user/toUpdateInfo | 跳转修改个人信息页面 |
| post | /user/updateInfo | 修改个人信息 |
| get | /user/toUserClass | 跳转我的课程页面 |
| get | /user/delUserClass | 退课 |
| get | /user/toApplyClass | 跳转报名选课页面 |
| get | /user/applyClass | 报名选课 |

会员管理接口

| 请求方式 | 请求地址 | 描述 |
| --- | --- | --- |
| get | /member/selMember | 查询所有会员 |
| post | /member/addMember | 新增会员 |
| get | /member/delMember | 删除会员 |
| get | /member/toUpdateMember | 跳转会员修改页面 |
| get | /member/toAddMember | 跳转新增会员页面 |
| post | /member/updateMember | 更新会员信息 |
