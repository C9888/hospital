# 医院预约挂号系统

## 项目概述

这是一个基于Java Web开发的医院预约挂号系统，支持患者在线预约医生、医生管理预约信息、管理员管理医院资源等功能。系统采用MVC架构，使用Servlet+JSP技术实现，提供了完整的预约挂号流程。

## 技术栈

- **后端框架**：Java Servlet + JSP
- **数据库**：MySQL 8.0
- **构建工具**：Maven
- **前端技术**：HTML, CSS, JavaScript, jQuery, Bootstrap
- **ORM**：自定义DAO层实现
- **连接池**：Druid

## 系统架构

项目采用经典的三层架构设计：

1. **表示层**：JSP页面负责用户界面展示
2. **业务层**：Servlet处理请求和业务逻辑
3. **数据层**：DAO层负责数据库操作

## 目录结构

```
src/
  ├── main/
  │   ├── java/com/qst/
  │   │   ├── bean/        # 实体类
  │   │   ├── dao/         # 数据访问层
  │   │   ├── filter/      # 过滤器
  │   │   ├── servlet/     # 控制器
  │   │   └── util/        # 工具类
  │   ├── resources/       # 配置文件
  │   └── webapp/          # Web资源
  │       ├── WEB-INF/     # Web配置
  │       ├── admin/       # 管理员页面
  │       ├── doctor/      # 医生页面
  │       ├── css/         # 样式文件
  │       ├── js/          # JavaScript文件
  │       ├── images/      # 图片资源
  │       └── include/     # 公共组件
  └── test/                # 测试代码
```

## 核心功能模块

### 1. 用户认证模块
- 支持三种角色登录：管理员、医生、患者
- 用户注册功能
- 密码修改功能

### 2. 预约挂号模块
- 按科室查询医生
- 按医生姓名搜索
- 查看医生出诊信息
- 选择就诊时间和号源
- 确认预约信息
- 查看预约记录

### 3. 医生管理模块
- 医生个人信息查看
- 预约患者列表查看
- 密码修改

### 4. 管理员模块
- 医生信息管理
- 患者信息管理
- 科室信息管理

## 数据库设计

系统包含以下主要数据表：

### admin（管理员表）
- `account`：管理员账号
- `password`：密码
- `name`：管理员姓名

### office（科室表）
- `officename`：科室名称
- `description`：科室描述
- `doctornum`：医生数量

### doctor（医生表）
- `did`：医生ID
- `account`：医生账号
- `password`：密码
- `dname`：医生姓名
- `fee`：诊查费用
- `gender`：性别
- `age`：年龄
- `office`：所属科室
- `career`：职称
- `description`：医生简介
- `picpath`：头像路径

### patient（患者表）
- `pid`：患者ID
- `account`：患者账号
- `email`：邮箱
- `password`：密码
- `name`：患者姓名
- `integity`：信用积分

### record（预约记录表）
- `rid`：记录ID
- `pid`：患者ID
- `wid`：工作日ID
- `did`：医生ID
- `serialnumber`：序号
- `visitdate`：就诊日期
- `visittime`：就诊时间
- `ordertime`：预约时间
- `state`：状态（成功、完成、爽约）

### workday（工作日表）
- `wid`：工作日ID
- `did`：医生ID
- `worktime`：工作日（0-6表示周日到周六）
- `ampm`：上午/下午
- `nsnum`：剩余号源数量
- `state`：状态（预约、停诊）

## 系统流程

### 预约挂号流程
1. 患者登录系统
2. 浏览科室或搜索医生
3. 查看医生出诊信息
4. 选择就诊时间和号源
5. 确认预约信息
6. 完成预约，系统生成预约记录

### 预约管理流程
1. 医生登录系统
2. 查看当日预约患者列表
3. 管理患者就诊状态

## 部署说明

### 环境要求
- JDK 1.8+
- MySQL 8.0+
- Tomcat 8.0+
- Maven 3.0+

### 部署步骤
1. 克隆项目到本地
2. 导入Maven依赖
3. 执行`hospital.sql`脚本创建数据库和表
4. 配置数据库连接信息
5. 打包成WAR文件
6. 部署到Tomcat服务器
7. 访问系统首页

### 默认账号
- 管理员：账号`admin`，密码`admin`
- 医生：可使用数据库中已有的账号，默认密码`123456`
- 患者：需要先注册

## 功能展示

### 首页
展示医院概况、科室信息、医生推荐等内容

### 科室浏览
展示医院所有科室信息，可查看科室详情

### 医生列表
展示各科室医生信息，可查看医生详情

### 预约确认
展示预约详细信息，包括医生、时间、费用等

### 预约记录
患者可查看自己的所有预约记录

## 系统特色

1. **多角色权限管理**：支持管理员、医生、患者三种角色
2. **灵活的预约机制**：可按科室或医生进行预约
3. **实时号源管理**：预约成功后自动更新剩余号源
4. **完善的用户体验**：简洁明了的界面设计
5. **安全性保障**：用户认证和会话管理

## 注意事项

1. 系统需要在支持Servlet 4.0的容器中运行
2. 数据库连接信息需要根据实际环境进行配置
3. 图片资源需要正确部署到指定路径
4. 系统目前仅支持基础的预约功能，可根据需求扩展更多功能

## 开发团队

本系统由医院信息化开发团队开发维护。

## 许可证

保留所有权利。
# 医院预约系统关键代码分析

## 📊 系统架构概览

```
┌─────────────────────────────────────────────────────────┐
│                    表示层 (Web层)                        │
│  ┌─────────────┐  ┌─────────────┐  ┌────────────────┐   │
│  │  JSP页面    │  │  HTML/CSS   │  │ JavaScript/jQuery│ │
│  └───────┬─────┘  └───────┬─────┘  └────────┬───────┘   │
│          │                │                 │           │
└──────────┼────────────────┼─────────────────┼───────────┘
           │                │                 │
┌──────────▼────────────────▼─────────────────▼───────────┐
│                    控制层 (Servlet层)                     │
│  ┌─────────────┐  ┌─────────────┐  ┌────────────────┐   │
│  │  登录认证   │  │  预约管理   │  │  医生/管理员操作 │   │
│  └───────┬─────┘  └───────┬─────┘  └────────┬───────┘   │
│          │                │                 │           │
└──────────┼────────────────┼─────────────────┼───────────┘
           │                │                 │
┌──────────▼────────────────▼─────────────────▼───────────┐
│                    数据访问层 (DAO层)                     │
│  ┌─────────────┐  ┌─────────────┐  ┌────────────────┐   │
│  │  用户DAO    │  │  预约DAO    │  │  其他业务DAO   │   │
│  └───────┬─────┘  └───────┬─────┘  └────────┬───────┘   │
│          │                │                 │           │
└──────────┼────────────────┼─────────────────┼───────────┘
           │                │                 │
┌──────────▼────────────────▼─────────────────▼───────────┐
│                    数据层 (数据库)                       │
│  ┌─────────────┐  ┌─────────────┐  ┌────────────────┐   │
│  │  用户表     │  │  预约表     │  │  其他业务表    │   │
│  └─────────────┘  └─────────────┘  └────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

## 🔑 核心功能代码分析

### 1. 用户登录认证模块

**Login.java** - 处理三种角色的登录验证

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
                // 管理员登录验证逻辑
                AdminDao adminDao = new IAdminimpl();
                List<Admin> admins = adminDao.getAdmin(account);
                if (admins.size() > 0 && admins.get(0).getPassword().equals(password)) {
                    request.getSession().setAttribute("admin", admins.get(0));
                    response.sendRedirect("admin/index.jsp");
                }
                break;
            case "医生":
                // 医生登录验证逻辑
                DoctorDao doctorDao = new DoctorDao();
                List<Doctor> doctors = doctorDao.query("where account =?", new Object[]{account});
                if (doctors.size() > 0 && doctors.get(0).getPassword().equals(password)) {
                    request.getSession().setAttribute("doctor", doctors.get(0));
                    response.sendRedirect("doctor/index.jsp");
                    return;
                }
                break;
            case "患者":
                // 患者登录验证逻辑
                PatientDao patientDao = new PatientDao();
                List<Patient> patients = patientDao.query("account", account);
                if (patients.size() > 0 && patients.get(0).getPassword().equals(password)) {
                    request.getSession().setAttribute("patient", patients.get(0));
                    response.sendRedirect("index.jsp");
                    return;
                }
                break;
        }
        
        // 登录失败处理
        request.getSession().setAttribute("message", "用户名或密码错误！！");
        response.sendRedirect("login.jsp");
    }
}
```

**代码亮点：**
- ✅ 支持三种角色（管理员、医生、患者）的身份验证
- ✅ 使用Session存储用户登录状态
- ✅ 基于角色的页面重定向
- ✅ 错误信息反馈机制

### 2. 预约挂号流程核心代码

**Order.java** - 处理患者预约流程

```java
@WebServlet(urlPatterns = "/order")
public class Order extends HttpServlet {
    private RecodeDao recodeDao = new RecodeDao();
    private NumSourceDao numSourceDao = new NumSourceDao();
    
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        Patient patient = (Patient) request.getSession().getAttribute("patient");
        String wid = request.getParameter("wid"); // 工作日id
        String did = request.getParameter("did"); // 医生id
        String action = request.getParameter("action");
        
        switch (action) {
            case "order": // 准备预约
                // 创建号源对象并跳转到确认页面
                NumSource numSource = new NumSource(strings[0], strings[1], strings[2], strings[3], wid);
                DoctorDao doctorDao = new DoctorDao();
                List<Doctor> doctors = doctorDao.query(" where did=?", new Object[]{did});
                request.getSession().setAttribute("numSource", numSource);
                request.setAttribute("doctor", doctors.get(0));
                request.getRequestDispatcher("confirmOrder.jsp").forward(request, response);
                break;
                
            case "confirm": // 完成预约
                // 验证号源状态并创建预约记录
                NumSource numSources = (NumSource) request.getSession().getAttribute("numSource");
                Recode recode = new Recode();
                recode.setPid(patient.getId());
                recode.setDid(did);
                recode.setWid(wid);
                recode.setSerialnumber(numSources.getSerialnumber());
                recode.setVisitdate(sdf.parse(numSources.getVisitdate()));
                recode.setVisittime(numSources.getVisittime());
                recode.setState("成功");
                recode.setOrdertime(new Timestamp(new Date().getTime()));
                
                // 检查号源是否已被预约
                List<Recode> list = recodeDao.query(where1, new Object[]{numSources.getState(), numSources.getVisitdate(), numSources.getVisittime()});
                if (list.size() == 0 && recodeDao.order(recode)) {
                    // 更新剩余号源数量
                    WorkDayDao workDayDao = new WorkDayDao();
                    List<WorkDay> workDay = workDayDao.query(" where wid=? ", new Object[]{wid});
                    int num = workDay.get(0).getNsnum() - 1;
                    if (num != 0) {
                        workDayDao.update("set nsnum=? where wid=?", new Object[]{num, wid});
                    } else {
                        workDayDao.update("set nsnum=? and state=? where wid=?", new Object[]{num, "停诊", wid});
                    }
                    
                    request.getSession().setAttribute("message", "预约成功！");
                    request.getRequestDispatcher("orderList").forward(request, response);
                } else {
                    request.getSession().setAttribute("message", "预约失败或号源已被预约！");
                    request.getRequestDispatcher("ShowWorkday?did=" + did).forward(request, response);
                }
                break;
        }
    }
}
```

**代码亮点：**
- ✅ 两步预约流程（准备和确认）提高用户体验
- ✅ 号源冲突检测避免重复预约
- ✅ 自动更新号源数量和状态
- ✅ 完整的事务处理和错误反馈

### 3. 数据库连接和工具类

**DBUtil.java** - 数据库操作核心工具类

```java
public class DBUtil {
    private static String url = "jdbc:mysql://101.133.234.109:3305/hospital?useSSL=false&characterEncoding=UTF-8&allowPublicKeyRetrieval=true";
    private static final String user = "root";
    private static final String password = "123456";
    
    static {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
    
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
        Connection conn = createConn();
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            ps = conn.prepareStatement(sql);
            prepare(ps, params);
            rs = ps.executeQuery();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return rs;
    }
    
    // 执行更新操作
    public static boolean executeUpdate(String sql, Object[] params) {
        Connection conn = createConn();
        PreparedStatement ps = null;
        int i = 0;
        try {
            ps = conn.prepareStatement(sql);
            prepare(ps, params);
            i = ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            closeAll();
        }
        return i > 0;
    }
    
    // 参数预处理
    private static void prepare(PreparedStatement ps, Object[] params) throws SQLException {
        if (params != null) {
            for (int i = 0; i < params.length; i++) {
                ps.setObject(i + 1, params[i]);
            }
        }
    }
    
    // 关闭资源
    public static void closeAll() {
        // 关闭ResultSet、PreparedStatement和Connection的代码
    }
}
```

**Util.java** - 通用工具方法

```java
public class Util {
    // 生成随机验证码
    public static int getCode() {
        return 100000 + (int)(899999 * Math.random());
    }
    
    // 邮箱格式验证
    public static boolean isEmail(String string) {
        if (string == null) return false;
        String regEx1 = "^([a-z0-9A-Z]+[-|\\.]?)+[a-z0-9A-Z]@([a-z0-9A-Z]+(-[a-z0-9A-Z]+)?\\.)+[a-zA-Z]{2,}$";
        Pattern p = Pattern.compile(regEx1);
        Matcher m = p.matcher(string);
        return m.matches();
    }
    
    // 空值处理
    public static String nullToString(String s) {
        return s == null ? "" : s;
    }
    
    // 日期相关工具方法
    public static String getDate(int day) {
        Calendar calendar = Calendar.getInstance();
        int day0fWeek = calendar.get(Calendar.DAY_OF_WEEK) - 1;
        if (day0fWeek > day)
            calendar.add(Calendar.DATE, 7);
        calendar.set(Calendar.DAY_OF_WEEK, day + 1);
        return calendar.get(Calendar.YEAR) + "-" + (calendar.get(Calendar.MONTH) + 1) + "-" + calendar.get(Calendar.DAY_OF_MONTH);
    }
}
```

### 4. 前端关键页面代码

**登录页面表单** (login.jsp)

```html
<form role="form" action="login" method="post" class="login-form">
    <div class="form-group col-xs-12">
        <label class="sr-only"></label>账号(*):<input
            style="font-weight: bold" type="text" name="account"
            class="form-control input-control clearfix"
            required  /><span wid="accountTip"></span>
    </div>
    <div class="form-group col-xs-12">
        <label class="sr-only">password</label>密码(*):<input
            style="font-weight: bold" type="password" name="password"
            class="form-password form-control" wid="pwd1" required />
    </div>
    <input hidden value="患者" name="accounttype">
    <div class="form-group col-xs-12">
        <button type="submit" class="btn" wid="btn">立即登录</button>
    </div>
</form>

<script>
    // 邮箱格式验证函数
    function isCheckEmail() {
        var email = document.getElementById("userEmail").value;
        var reg = /^\w+((-\w+)|(\.\w+))*\@[A-Za-z0-9]+((\.|-)[A-Za-z0-9]+)*\.[A-Za-z0-9]+$/;
        isok = reg.test(email);
        if (isok) {
            document.getElementById("emailTip").innerHTML = "邮箱格式正确";
            document.getElementById("btn").disabled = false;
            return true;
        } else {
            document.getElementById("emailTip").innerHTML = "邮箱格式不正确";
            document.getElementById("btn").disabled = true;
            return false;
        }
    }
</script>
```

**预约确认页面** (confirmOrder.jsp)

```html
<div class="container1">
    <style>
        .container1 .title{color: #00E8D7;margin:20px auto;display: table;}
        .container1 .left{float: left;width: 30%;}
        .container1 .left .confirm{text-align: center;background-color: #009F95;color:#fff;border-radius: 10px;height:30px;cursor:pointer;}
    </style>
    <h2 class="title">预约信息核对</h2>
    <div class="left">
        <form autocomplete="off" method="post" action="order">
            <input hidden name="wid" value="${numSource.state}">
            <input hidden name="did" value="${doctor.did}">
            <input hidden name="action" value="confirm">
            <table>
                <tr><td>姓&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;名：</td><td>${sessionScope.patient.name}</td></tr>
                <tr><td>预约医生：</td><td>${doctor.dname}</td></tr>
                <tr><td>预约科室：</td><td>${doctor.office}&nbsp</td></tr>
                <tr><td>就诊时间：</td><td>${numSource.visitdate} ${numSource.visittime}第${numSource.serialnumber}号</td></tr>
                <tr><td>诊&nbsp;&nbsp;查&nbsp;&nbsp;费：</td><td>${doctor.fee}.00元</td></tr>
                <tr><td>你的邮箱：</td><td>${sessionScope.patient.email}</td></tr>
                <tr><td colspan="2" class="confirm"><button type="submit" class="btn">确定</button></td></tr>
            </table>
        </form>
    </div>
</div>
```

## 📈 业务流程图解

### 预约挂号流程

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   患者登录   │────>│  选择科室/医生  │────>│  查看出诊信息  │
└─────────────┘     └─────────────┘     └──────┬──────┘
                                               │
                                               ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  查看预约记录  │<────┤   完成预约   │<────┤  确认预约信息  │
└─────────────┘     └──────┬──────┘     └──────┬──────┘
                           │                   │
                           ▼                   │
                    ┌─────────────┐           │
                    │  更新号源状态 │<──────────┘
                    └─────────────┘
```

## 🎯 代码优化建议

1. **数据库连接安全**
   - 当前代码中数据库连接信息（URL、用户名、密码）硬编码在DBUtil类中
   - **建议**：将连接信息移至配置文件，使用Properties或Spring配置进行管理

2. **密码安全**
   - 密码以明文形式存储和验证，存在安全风险
   - **建议**：使用MD5、SHA256等哈希算法加盐存储密码

3. **错误处理优化**
   - 当前代码使用System.out.println()进行日志输出
   - **建议**：引入Log4j或SLF4J进行日志管理

4. **连接资源管理**
   - 部分方法中存在连接资源未正确关闭的情况
   - **建议**：使用try-with-resources语句确保资源自动关闭

5. **前端表单验证**
   - 前端验证逻辑不够完善
   - **建议**：增加更多的表单验证和用户友好的错误提示

## 📝 总结

医院预约系统采用经典的三层架构（表示层、业务层、数据层），通过Servlet+JSP技术实现了完整的预约挂号流程。系统支持多角色登录和权限管理，具有灵活的预约机制和实时号源管理功能。核心代码体现了良好的模块化设计和业务逻辑封装，但在安全性和错误处理方面还有优化空间。

通过本代码分析，可以清晰了解系统的工作原理和核心流程，为后续的维护和扩展提供了参考基础。
