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
