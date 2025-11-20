# 医院预约系统关键代码分析


# 医院预约系统项目说明

> 医院预约系统是一个基于Java Web技术开发的挂号预约平台

## 项目概述

本项目是一个完整的医院预约挂号系统，支持管理员、医生和患者三种角色，实现了在线挂号、预约管理、用户认证等核心功能。

## 技术栈

- **后端**: Java Servlet + JSP
- **数据库**: MySQL
- **前端**: HTML/CSS/JavaScript/jQuery/Bootstrap

## 核心功能模块

### 1. 用户登录认证

```java
@WebServlet(urlPatterns = "/login")
public class Login extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        String account = request.getParameter("account");
        String password = request.getParameter("password");
        String accounttype = request.getParameter("accounttype");
        
        switch (accounttype) {
            case "管理员":
                // 管理员登录验证
                break;
            case "医生":
                // 医生登录验证
                break;
            case "患者":
                // 患者登录验证
                break;
        }
    }
}
```

### 2. 预约挂号流程

```java
@WebServlet(urlPatterns = "/order")
public class Order extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String action = request.getParameter("action");
        
        switch (action) {
            case "order": // 准备预约
                // 创建号源对象并跳转到确认页面
                break;
            case "confirm": // 完成预约
                // 验证号源状态并创建预约记录
                break;
        }
    }
}
```

### 3. 数据库操作工具类

```java
public class DBUtil {
    // 创建数据库连接
    public static Connection createConn() {
        try {
            return DriverManager.getConnection(url, user, password);
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return null;
    }
    
    // 执行查询操作
    public static ResultSet executeQuery(String sql, Object[] params) {
        // 数据库查询实现
        return rs;
    }
    
    // 执行更新操作
    public static boolean executeUpdate(String sql, Object[] params) {
        // 数据库更新实现
        return i > 0;
    }
}
```

## 前端关键页面

### 登录页面

```html
<form role="form" action="login" method="post" class="login-form">
    <div class="form-group col-xs-12">
        <label class="sr-only"></label>账号(*):<input
            type="text" name="account"
            class="form-control input-control" required />
    </div>
    <div class="form-group col-xs-12">
        <label class="sr-only">password</label>密码(*):<input
            type="password" name="password"
            class="form-password form-control" required />
    </div>
    <input hidden value="患者" name="accounttype">
    <div class="form-group col-xs-12">
        <button type="submit" class="btn">立即登录</button>
    </div>
</form>
```

### 预约确认页面

```html
<div class="container1">
    <h2 class="title">预约信息核对</h2>
    <div class="left">
        <form autocomplete="off" method="post" action="order">
            <input hidden name="wid" value="${numSource.state}">
            <input hidden name="did" value="${doctor.did}">
            <input hidden name="action" value="confirm">
            <table>
                <tr><td>姓&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;名：</td><td>${sessionScope.patient.name}</td></tr>
                <tr><td>预约医生：</td><td>${doctor.dname}</td></tr>
                <tr><td>预约科室：</td><td>${doctor.office}</td></tr>
                <tr><td>就诊时间：</td><td>${numSource.visitdate} ${numSource.visittime}</td></tr>
                <tr><td colspan="2" class="confirm"><button type="submit" class="btn">确定</button></td></tr>
            </table>
        </form>
    </div>
</div>
```

## 使用说明

1. **患者端**
   - 注册/登录系统
   - 浏览科室和医生信息
   - 选择就诊时间进行预约
   - 查看和管理预约记录

2. **医生端**
   - 登录系统查看个人信息
   - 管理出诊时间和号源

3. **管理员端**
   - 管理用户信息
   - 管理医生和科室数据
   - 系统配置和维护

## 项目结构

```
src/main/
├── java/com/qst/
│   ├── servlet/     # Servlet控制器
│   ├── dao/         # 数据访问层
│   ├── bean/        # 实体类
│   └── util/        # 工具类
└── webapp/          # 前端页面
    ├── admin/       # 管理员页面
    ├── doctor/      # 医生页面
    ├── css/         # 样式文件
    ├── js/          # JavaScript文件
    ├── login.jsp    # 登录页面
    └── index.jsp    # 首页
```
