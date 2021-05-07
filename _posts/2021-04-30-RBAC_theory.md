---
layout: post
title: "RBAC模型分析与讲解，不包含基础RBAC模型"
date:   2021-04-30
tags: [database]
comments: true
toc: true
author: OJumpyo
---

## 1、什么是RBAC
![RBAC0模型介绍](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/RBAC_theory/RBAC0模型介绍.jpg)  
经典的RBAC模型就是五张表：user、userRole、role、roleMenu、menu  
* 用户与角色多对多、一个用户多种角色，一种角色对应多个用户。  
* 角色与权限多对多、一个角色多个权限，一个权限被多个角色拥有。  

==如果还需要加到按钮级别的访问控制，则可以在加一个具体的更细粒度的表button，增加menu与button的对应，或者直接在menu表里加一个字段，区别该条记录是菜单项还是按钮。==  

## 2、RBAC模型的缺陷
- RBAC由于**粗粒度的权限控制**，比如对医生角色授予“查看病历”的权限，这将使医生有权查看所有医疗记录，包括自己的病历。
- 为此做的补丁目前是设计：对医生再进行具体的角色分类，设计诸如外科医生、内科医生、妇产科医生等，在角色层级设定更**细粒度**的划分，也由此导致了 **“角色爆炸”** 的问题。
- 由此有产生**ABAC的属性权限控制模型**，但同样的这种模型也带来了 **“属性爆炸”** 的问题。
- 有人曾设想构建基于**角色、属性混合的权限控制模型**。但是因此带来了更多的问题，诸如属性角色划分不均、混乱、属性角色多重爆炸的问题。
- 目前产业界与学术界对于新一代的访问控制依然在探索中。  

[参考的RBAC模型资料](https://blog.csdn.net/weixin_39736934/article/details/111125092)

## 3、对于RBAC模型的改进
#### ==基于若依讲解。==

### 3.1.1、组织部门的管理
![组织部门管理](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/RBAC_theory/组织部门管理.jpg)  
在RBAC模型中，目前只有用户、角色、权限的三张表。但是部门这样的实体同样是存在的。并且存在以下需求：  
- 部门需要有上下级的结构，一般使用==部门id、上级部门p_ids==来组合成一个树形结构
- 用户和组织的对应关系维护较为困难，如果==部门和用户是一对多关系==，一般是直接在用户表中直接增加字段dept_id来维护。因为维护中间表消耗存储，部门维护用户的难度也比用户维护自己所在部门的难度大。
- 但是如果==用户和部门是多对多关系==，比如某个人兼职博导、公司老板、又是行政主任。这种情况一般建议：==创建用户和组织的多对多中间表==。
- 其他需求：基本的组织crud功能，组织别的信息如名称、状态、展示排序、创建时间等

### 3.1.2、组织部门表的设计
~~~sql
CREATE TABLE `sys_dept`  (
  `dept_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '部门id',
  `parent_id` int(11) NULL DEFAULT 0 COMMENT '父部门id',
  `ancestors` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '0' COMMENT '祖级列表',
  `dept_name` varchar(30) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '部门名称',
  `order_num` int(4) NULL DEFAULT 0 COMMENT '显示顺序',
  `leader` varchar(20) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '负责人',
  `leader_id` int(11) NULL DEFAULT NULL COMMENT '负责人编号',
  `phone` varchar(11) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '联系电话',
  `email` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '邮箱',
  `status` char(1) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '0' COMMENT '部门状态（0正常 1停用）',
  `del_flag` char(1) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '0' COMMENT '删除标志（0代表存在 2代表删除）',
  `create_by` varchar(64) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '创建者',
  `create_time` datetime(0) NULL DEFAULT NULL COMMENT '创建时间',
  `update_by` varchar(64) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '更新者',
  `update_time` datetime(0) NULL DEFAULT NULL COMMENT '更新时间',
  PRIMARY KEY (`dept_id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 118 CHARACTER SET = utf8 COLLATE = utf8_general_ci COMMENT = '部门表' ROW_FORMAT = Dynamic;

SET FOREIGN_KEY_CHECKS = 1;
~~~
#### 注意：
- 由于MySQL对树形数据不像Oracle有树形数据汇总SQL。所以为了方便对组织之间的上下级树形关系管理，
- 需要增加本表中的特殊三字段： ==dept_id、parent_id、ancestors==，
- 部门id和父节点id可以简单满足本届点和父节点的关系绑定查询，
- 如果想要查询所有的祖宗节点，则配合ancestors字段查询本节点到祖宗节点的数据。ancestors用 ","隔开。
- 事实上，这里还可以==商榷一下，是不是可以增加is_leaf判断你是否是叶子节点，level本部门在整体项目中的层级。==  

### 3.1.3、组织表的一些常见查询实现
#### 请参考另一篇笔记，[RBAC的具体细节](http://note.youdao.com/noteshare?id=a94b902bf375857304c674cb8af4b199&sub=EBDAC102B6FD4564911340D2F9AC66F9)  


### 3.2.1、菜单权限的管理
![菜单权限管理](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/RBAC_theory/菜单权限管理.jpg)  
菜单权限的管理同样是采用的树形结构化管理。  
#### 需求分析：
- 树形结构，依然需要id、menu_pid字段
- 必要字段：菜单跳转的url、是否启用、菜单排序、菜单的icon矢量图标等
- ==菜单需要有一个标识，用来在给用户鉴权的时候做比较，一般使用这个菜单跳转的url做比对。  
- 菜单表的crud
- 如果要做到==按钮级权限管理==，可以另外构建按钮级别的权限表，菜单和权限表再做一次对应。也可以==在本菜单表中增加flag字段，标识本字段是菜单还是按钮权限==。

### 3.2.2、菜单权限表的设计
~~~sql
CREATE TABLE `sys_menu`  (
  `menu_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '菜单ID',
  `menu_name` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '菜单名称',
  `menu_key` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL DEFAULT '' COMMENT '菜单标识',
  `component` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '菜单布局',
  `parent_id` int(11) NULL DEFAULT 0 COMMENT '父菜单ID',
  `target` varchar(20) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '打开方式',
  `order_num` int(4) NULL DEFAULT 0 COMMENT '显示顺序',
  `menu_type` char(1) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '菜单类型（M目录 C菜单 F按钮）',
  `visible` char(1) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '0' COMMENT '菜单状态（0显示 1隐藏）',
  `perms` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '权限标识',
  `icon` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '#' COMMENT '菜单图标',
  `path` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '链接',
  `redirect` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '重定向',
  `hidden_children` tinyint(1) UNSIGNED NULL DEFAULT NULL COMMENT '强制菜单显示为Item而不是SubItem',
  `hidden_header` tinyint(1) UNSIGNED NULL DEFAULT NULL COMMENT '特殊 隐藏 PageHeader 组件中的页面带的 面包屑和页面标题栏',
  `create_by` varchar(64) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '创建者',
  `create_time` datetime(0) NULL DEFAULT NULL COMMENT '创建时间',
  `update_by` varchar(64) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '更新者',
  `update_time` datetime(0) NULL DEFAULT NULL COMMENT '更新时间',
  `remark` varchar(500) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '备注',
  PRIMARY KEY (`menu_id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 2137 CHARACTER SET = utf8 COLLATE = utf8_general_ci COMMENT = '菜单权限表' ROW_FORMAT = Dynamic;
~~~
- 这里的menu_id、parent_id用来==构建菜单树==
- menu_key用来做大的菜单栏管控
- component用来菜单的布局，也就是用来给前端好做布局控制
- order_num用来控制同级的菜单的显示顺序
- menu_type用来标识这是个菜单还是按钮，还是整体的目录，M目录，C菜单，F按钮。本表把菜单和权限控制都做了。
- perms类似于上面提到的菜单跳转url，这里是用来做==权限校验==的字段项。

### 3.3.1、角色管理
![角色管理](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/RBAC_theory/角色管理.jpg)  
#### 需求分析
- 角色需要简单的crud
- 基本信息：id，name，remark，order顺序
- 重点是==为角色分配权限应该怎么做==
- ==角色的权限变动==：以角色为基础，勾选菜单权限或者操作权限，然后先删除role_menu里头所有的该角色的记录，再将新勾选的权限数据逐条插入role_menu表
- 能够根据角色的某一标识，通常是id，使得能够通过角色就能判断用户的操作是否合法
- 通常需要在用户管理界面为用户分配角色，而不是给角色分配用户

### 3.3.2、角色表、角色菜单表的设计
~~~sql
CREATE TABLE `sys_role`  (
  `role_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '角色ID',
  `role_name` varchar(30) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '角色名称',
  `role_key` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '角色权限字符串',
  `role_sort` int(4) NOT NULL COMMENT '显示顺序',
  `data_scope` char(1) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '1' COMMENT '数据范围（1：全部数据权限 2：自定数据权限）',
  `status` char(1) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '角色状态（0正常 1停用）',
  `del_flag` char(1) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '0' COMMENT '删除标志（0代表存在 2代表删除）',
  `create_by` varchar(64) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '创建者',
  `create_time` datetime(0) NULL DEFAULT NULL COMMENT '创建时间',
  `update_by` varchar(64) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '更新者',
  `update_time` datetime(0) NULL DEFAULT NULL COMMENT '更新时间',
  `remark` varchar(500) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '备注',
  `company_id` bigint(20) NULL DEFAULT NULL,
  `company_name` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`role_id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 19 CHARACTER SET = utf8 COLLATE = utf8_general_ci COMMENT = '角色信息表' ROW_FORMAT = Dynamic;
~~~
- role_id作为和别的表交互的字段
- role_key用来实现上述的根据角色判断用户操作合法性
- ==重要：data_scope用来做数据粒度的掌控==

~~~sql
CREATE TABLE `sys_role_menu`  (
  `role_id` int(11) NOT NULL COMMENT '角色ID',
  `menu_id` int(11) NOT NULL COMMENT '菜单ID',
  `company_id` bigint(20) NULL DEFAULT NULL,
  PRIMARY KEY (`role_id`, `menu_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci COMMENT = '角色和菜单关联表' ROW_FORMAT = Dynamic;
~~~

### 3.4.1、用户管理
![用户管理](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/RBAC_theory/用户管理.jpg)  
#### 需求分析
- 对某个组织下的所有人员能展示出来，这里可以对用户表维护一个dept_id字段，用于查询某个组织下的所有用户
- 用户表里头保存用户名、加密密码、基本信息、密码修改重置等功能
- 用户分配角色，这个与角色分配权限的设计原则一致，建议加个中间表就好了
- 用户信息的crud

### 3.4.2、用户表、用户角色表的设计
~~~sql
CREATE TABLE `sys_user`  (
  `user_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '用户ID',
  `dept_id` int(11) NULL DEFAULT NULL COMMENT '部门ID',
  `login_name` varchar(30) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '登录账号',
  `user_name` varchar(30) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '用户昵称',
  `user_type` varchar(2) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '00' COMMENT '用户类型（00系统用户）',
  `email` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '用户邮箱',
  `phonenumber` varchar(11) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '手机号码',
  `sex` char(1) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '0' COMMENT '用户性别（0男 1女 2未知）',
  `avatar` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '头像路径',
  `password` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '密码',
  `salt` varchar(20) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '盐加密',
  `status` char(1) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '0' COMMENT '帐号状态（0正常 1停用）',
  `del_flag` char(1) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '0' COMMENT '删除标志（0代表存在 2代表删除）',
  `login_ip` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '最后登陆IP',
  `login_date` datetime(0) NULL DEFAULT NULL COMMENT '最后登陆时间',
  `create_by` varchar(64) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '创建者',
  `create_time` datetime(0) NULL DEFAULT NULL COMMENT '创建时间',
  `update_by` varchar(64) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '更新者',
  `update_time` datetime(0) NULL DEFAULT NULL COMMENT '更新时间',
  `remark` varchar(500) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '' COMMENT '备注',
  `company_id` bigint(20) NULL DEFAULT NULL,
  `company_name` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`user_id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 9 CHARACTER SET = utf8 COLLATE = utf8_general_ci COMMENT = '用户信息表' ROW_FORMAT = Dynamic;
~~~
- dept_id存用户的组织信息，这里假定一个用户只在一个部门，若是==用户身兼多职==，可以像用户角色表一样，增加中间表
~~~sql
CREATE TABLE `sys_user_role`  (
  `user_id` int(11) NOT NULL COMMENT '用户ID',
  `role_id` int(11) NOT NULL COMMENT '角色ID',
  `company_id` bigint(20) NULL DEFAULT NULL,
  PRIMARY KEY (`user_id`, `role_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci COMMENT = '用户和角色关联表' ROW_FORMAT = Dynamic;

SET FOREIGN_KEY_CHECKS = 1;
~~~