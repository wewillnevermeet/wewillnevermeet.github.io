+++
title = '快速尝试Spring'
date = 2024-07-02T22:58:06+08:00
draft = false
+++

# spring目录
![目录：项目结构](/image/SpringMulu.png)
java下的目录：  
controller:定义类  
entity：操作数据库时创建的对象类型，用于接受数据库的数据，其中的字段要和数据库的字段吻合  
mapper：定义mapper接口，定义操作数据库的动作  
service：返回数据给前端时对数据的操作，即业务逻辑，例如筛选过滤  
resources下的目录：  
mapper：在xml里写具体的操作数据库的sql语句  
application.yml:配置数据库和mybatis的mapper等  
# spring boot简单步骤
1.建立实体类，和数据库字段保持一致  
![](/image/Shitilei.png)  
2.建立mapper接口，定义操作数据库的动作 
![](/image/MapperJiekou.png)   
3.建立mapper的xml文件，写具体的sql语句  
![](/image/MapperXml.png)  
4.建立service类，处理业务逻辑  
![](/image/ServiceLei.png)  
5.在controller类中展示处理的结果  
![](/image/ControllerJieguo.png)
