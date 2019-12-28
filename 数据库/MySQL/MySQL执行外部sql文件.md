# MySQL执行外部sql文件

sql脚本是包含一到多个sql文件命令的sql语句，我们可以将这些sql脚本放在一个文本文件中（我们称之为“sql脚本文件”），然后通过相关命令执行这个sql脚本文件。基本步骤如下：

## 一、创建sql文件




## 二、执行sql脚本文件
### 方法一：
命令行执行`mysql -u用户名 -p密码 -D数据库<sql脚本文件路径全名`，示例：
`mysql -uroot -p123456 -Dtest<d:\test\ss.sql`
注意：
- 如果在sql脚本文件中使用了use数据库，则-D数据库选项可以忽略


### 方法二：
进入mysql控制台后，使用source命令执行
`Mysql>source sql脚本文件绝对路径`或`Mysql>\. sql脚本文件绝对路径`，示例：
`source d：/test/ss.sql`或者`\. d:/test/ss.sql`