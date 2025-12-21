### 系统介绍

基于SpringBoot和Vue实现的快递物流仓库管理系统采用前后端分离的架构方式，系统实现了用户登录、基础管理、销售管理、配送管理、运输管理、图表分析、系统管理、日志管理等功能模块，其中用户登录可采用账号密码和邮箱验证码两种登录方式。

### 技术选型

开发工具：idea2020.3+Webstorm2020.3

运行环境：jdk1.8+maven3.6.0+MySQL5.7+nodejs14.21.3

服务端技术：Springboot+Mybatis-Plus+SpringSecurity+Fastjson

前端技术：html+css+Vue+axios+Element-UI+echarts

### 成果展示

用户登录
<img width="1910" height="1045" alt="用户登录" src="https://github.com/user-attachments/assets/97983c2b-887b-4f63-8f1e-29a41f36b117" />

基础管理->商品管理
<img width="1910" height="814" alt="基础管理-商品管理" src="https://github.com/user-attachments/assets/b88535ad-6093-4559-b60e-e604e4f408cc" />

销售管理->销售开票
<img width="1885" height="869" alt="销售管理-销售开票" src="https://github.com/user-attachments/assets/6c8aabd3-eb7b-4108-9100-1ff628fe7bf3" />

配送管理->申请配送
<img width="1886" height="957" alt="配送管理-申请配送" src="https://github.com/user-attachments/assets/971ef308-4a82-463f-9894-80ad21d4422e" />

运输管理->车辆资料
<img width="1910" height="955" alt="运输管理-车辆资料" src="https://github.com/user-attachments/assets/60c2627f-452a-450c-a27a-9c1314776c95" />

图表分析->入库分析
<img width="1910" height="954" alt="图表分析-入库分析" src="https://github.com/user-attachments/assets/34e1bec9-ca3e-44bf-9038-8acd43f585d8" />

系统设置->安全设置
<img width="1910" height="955" alt="系统设置-安全设置" src="https://github.com/user-attachments/assets/e54ce029-f3a6-4232-9f82-425cc128850c" />

日志管理->登录日志
<img width="1890" height="955" alt="日志管理-登录日志" src="https://github.com/user-attachments/assets/e5ad7919-b8f6-4a57-9874-964022991ccf" />

### 源码展示

@RestController

@RequestMapping("/api/admin")

@Slf4j

public class AdminController {

//获取日志对象

Logger logger = LoggerFactory.getLogger(AdminController.class);

@Resource

private AdminService adminService;

@Resource

private AdminRepository adminRepository;

@Resource

private LoginLogService loginLogService;

@GetMapping("hasInit")

public boolean hasInit() {

    return adminRepository.existsAdminByRoles(Role.ROLE_SUPER_ADMIN.getValue());
    
}

@PostMapping("/init")

public Admin init(@RequestBody Admin admin) throws Exception {

    admin.setRoles(Role.ROLE_SUPER_ADMIN.getValue());
    return adminService.save(admin);
    
}

@GetMapping("")

@PreAuthorize("hasAnyRole('ROLE_SUPER_ADMIN' ,'ROLE_ADMIN')")

public List<Admin> findAll() {

    return adminService.findAll();
    
}

@DeleteMapping("")

@PreAuthorize("hasAnyRole('ROLE_SUPER_ADMIN' ,'ROLE_ADMIN')")

public void delete(String id) {

    adminService.delete(id);
    
}

@PostMapping("")

@PreAuthorize("hasAnyRole('ROLE_SUPER_ADMIN' ,'ROLE_ADMIN')")

public Admin save(@RequestBody Admin admin) throws Exception {

    return adminService.save(admin);
    
}

@PostMapping("/login")

public Map<String, Object> loginByEmail(String type, @RequestBody LoginDto dto, HttpServletRequest request) throws Exception {

    Map<String, Object> map = new HashMap<>();
    Admin admin = null;
    String token = null;
    try {
        admin = type.equals("email") ? adminService.loginByEmail(dto) : adminService.loginByPassword(dto);
        token = adminService.createToken(admin,
                dto.isRemember() ? JwtTokenUtil.REMEMBER_EXPIRATION_TIME : JwtTokenUtil.EXPIRATION_TIME);
    }catch (Exception e){
        throw new Exception("邮箱或密码错误");
    }finally {
        loginLogService.recordLog(dto,admin,request);
    }
    map.put("admin", admin);
    map.put("token", token);
    return map;
    
}

@GetMapping("/sendEmail")

public ResponseResult sendEmail(String email) throws Exception {

    Boolean flag = adminService.sendEmail(email);
    ResponseResult res = new ResponseResult();
    if (flag){
        res.setMsg("发送成功，请登录邮箱查看");
    }else {
        res.setMsg("发送验证码失败，请检查邮箱服务");
    }
    res.setStatus(flag);
    return res;
    
}

}

### 账号地址及其它说明

1、地址说明

登录页：localhost:8080

2、账号说明

管理员：admin@163.com/admin

3、目录结构展示

<img width="844" height="171" alt="目录结构展示" src="https://github.com/user-attachments/assets/8a653de7-a7b8-4ba3-ab64-9b1cdf401223" />

4、项目结构展示

<img width="1692" height="982" alt="项目结构展示" src="https://github.com/user-attachments/assets/befad339-ae03-4ef3-8d80-0b63d38be220" />

5、运行步骤

1）创建数据库、导入sql脚本

2）修改application.yaml中的数据库配置文件，启动服务端

3）在web-app目录下打开cmd，执行npm install或者yarn install下载依赖

4）下载完毕后启动前端npm run serve，访问端口

### 获取方式(可远程调试)

访问链接(在浏览器中手动输入下图中的地址)：

<img width="1029" height="124" alt="链接" src="https://github.com/user-attachments/assets/517f8b88-4da4-4baa-b281-11849be09762" />

若资源获取失败，可添加happy35596339(vx)或1204901965(qq)进行交流
