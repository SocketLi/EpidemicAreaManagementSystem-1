数据库名：EpidemicDatabase
# 一、基本信息表
## 1、User表（疫区人员和疫区管理员）
+ 18位身份证号码 userID （主键）<br>
+ 姓名 userName <br>
+ 登陆密码 password <br>
+ 该用户是否是疫区管理员 isAdministrator <br>
+ 所住小区号 neighborID 
+ 所住户号 familyID 
```
CREATE TABLE `User` (
  `userID` char(18) NOT NULL, 
  `userName` varchar(20) NOT NULL,
  `password` varchar(255) NOT NULL,
  `isAdministrator` boolean NOT NULL,
  `neighborID` int NOT NULL, 
  `familyID` int NOT NULL, 
  PRIMARY KEY (`userID`) 
);
```

# 二、小区和户相关表
## 1、Neighbor表（住宅小区）
+ 小区号 neighborID （主键）
+ 该小区管理员 administratorID
```
CREATE TABLE `Neighbor` (
  `neighborID` int NOT NULL, 
  `administratorID` char(18) NOT NULL,
  PRIMARY KEY (`neighborID`) 
);
```

## 2、Family表（户）
+ 小区号 neighborID （主键1）
+ 户号 familyID （主键2）
+ 该户负责人 leaderID
+ 是否为空户
+ 户的类型（非隔离户0，隔离户1，病患户2）
+ 该住户所有人身份证号 userID （多人居住在一户用下划线分割，如1111_2222_3333）

户的类型：需要每天遍历所有住户是否为隔离人员或病患以实时更新。
```
CREATE TABLE `Family` (
  `neighborID` int NOT NULL, 
  `familyID` int NOT NULL, 
  `leaderID` char(18) NOT NULL,
  `isNull` boolean NOT NULL,
  `type` int NOT NULL,
  `userID` varchar(255),
  PRIMARY KEY (`neighborID`, `familyID`) 
);
```

# 三、基本功能表
## 1、PhysicalCondition 每日身体情况上报表
+ 上报人ID userID (主键1)
+ 上报日期 date （主键2）
+ 当日体温 todayTemperature
+ 当日接触的湖北籍人员身份证号，无接触则空，多人接触以下划线分割 HuBeiContact
```
CREATE TABLE `PhysicalCondition` (
  `userID` char(18) NOT NULL,
  `date` DATE NOT NULL, 
  `todayTemperature` double NOT NULL,
  `HuBeiContact` varchar(255),
  PRIMARY KEY (`userID`, `date`) 
);
```

## 2、EquipmentStorage 防护用具存量表
+ 防护用具名称 equipment （主键）
+ 剩余存量 storage
```
CREATE TABLE `EquipmentStorage` (
  `equipment` varchar(255) NOT NULL,
  `storage` int NOT NULL, 
  PRIMARY KEY (`equipment`) 
);
```

## 3、关系网络管理
？

## 4、IsolationManagement  隔离管理
+ 隔离人员身份证 userID （主键1）
+ 当前日期 date （主键2）
+ 是否是病患 isPatient
+ 已隔离天数 days

病患人员的隔离天数置为-1，需要一直隔离直到治愈后（isPatient转为false）再隔离14天才可解除隔离。普通隔离人员（疑似人员、返工人员登）隔离14天后解除隔离。
```
CREATE TABLE `IsolationManagement` (
  `userID` char(18) NOT NULL,
  `date` DATE NOT NULL,
  `isPatient` boolean NOT NULL,
  `days` int NOT NULL, 
  PRIMARY KEY (`userID`, `date`) 
);
```