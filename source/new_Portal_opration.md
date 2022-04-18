New\_Portal简介

一:架构图

![](/root/work/scrapy-cookbook/source/imgs/media/image1.png){width="5.7659722222222225in"
height="3.1430555555555557in"}

二:用户端介绍

1：用户界面登录 (使用用户账号登录)

![](/root/work/scrapy-cookbook/source/imgs/media/image2.png){width="5.754861111111111in"
height="2.807638888888889in"}

2:线路状态

数据展示页面：概览信息中和线路管理-线路状态

数据来源：Zabbix监控系统和ticket系统，经过统计获取的数据

![](/root/work/scrapy-cookbook/source/imgs/media/image3.png){width="5.759027777777778in"
height="2.204861111111111in"}

![](/root/work/scrapy-cookbook/source/imgs/media/image4.png){width="5.764583333333333in"
height="2.832638888888889in"}

3:线路利用率

数据展示页面：概览信息中和线路管理-线路利用率

数据来源：mrtg系统(包括mrtg1，mrtg3，mrtg4)，经过获取时间序列数据库数据计算而来

![](/root/work/scrapy-cookbook/source/imgs/media/image5.png){width="5.766666666666667in"
height="1.5118055555555556in"}

![](/root/work/scrapy-cookbook/source/imgs/media/image6.png){width="5.766666666666667in"
height="2.716666666666667in"}

4:设备状态

数据展示页面：概览信息中和设备管理-设备状态

数据来源：Openfalcon系统，操作CE设备由Openfalcon系统监控

![](/root/work/scrapy-cookbook/source/imgs/media/image7.png){width="5.759722222222222in"
height="2.3534722222222224in"}

![](/root/work/scrapy-cookbook/source/imgs/media/image8.png){width="5.766666666666667in"
height="2.798611111111111in"}

5:设备日志

数据展示页面：设备管理-设备日志

数据来源：Openfalcon系统，操作CE设备由Openfalcon系统监控

![](/root/work/scrapy-cookbook/source/imgs/media/image9.png){width="5.766666666666667in"
height="2.8333333333333335in"}

6:流量分析

数据展示页面：线路管理-流量分析

数据来源：netflow系统，获取Tellme系统对该客户填写的netflow链接/账号/密码，实现自动跳转自动登录

![](/root/work/scrapy-cookbook/source/imgs/media/image10.png){width="5.763194444444444in"
height="2.7569444444444446in"}

7：服务报告和网络架构图

数据展示页面：服务报告/架构图

数据来源：Tellme系统，由工程师在tellme系统上传该客户的服务报告和架构图，portal系统会自动获取tellme登记的文件数据，实现同步

![](/root/work/scrapy-cookbook/source/imgs/media/image11.png){width="5.763194444444444in"
height="2.8208333333333333in"}

![](/root/work/scrapy-cookbook/source/imgs/media/image12.png){width="5.766666666666667in"
height="2.8125in"}

8:租户信息

数据展示页面：系统设置\--\>租户信息

数据来源：Tellme系统，获取tellme登记的基本客户信息

![](/root/work/scrapy-cookbook/source/imgs/media/image13.png){width="5.768055555555556in"
height="2.829861111111111in"}

9:帮助手册

数据展示页面：系统设置\--\>帮助手册

数据来源：管理员后台上传的数据，针对所有用户（常见问题/文档下载）

![](/root/work/scrapy-cookbook/source/imgs/media/image14.png){width="5.759027777777778in"
height="2.8055555555555554in"}

10:意见反馈

数据展示页面：系统设置\--\>意见反馈

数据来源：客户填写相关问题，该意见会自动转接到ticket系统

![](/root/work/scrapy-cookbook/source/imgs/media/image15.png){width="5.761805555555555in"
height="2.829861111111111in"}

三:管理后台介绍

1：管理员界面登录 (使用管理员账号登录)

![](/root/work/scrapy-cookbook/source/imgs/media/image16.png){width="5.7652777777777775in"
height="2.892361111111111in"}

2：开通账号流程

点击用户信息\--\>点击添加用户（按照流程一步步添加）

1)新增账号（注意：填写的客户名称控制右上角的名字（欢迎,xxxxx））

![](/root/work/scrapy-cookbook/source/imgs/media/image17.png){width="5.766666666666667in"
height="2.1145833333333335in"}

2)  选择该用户的合作方和页面权限

![](/root/work/scrapy-cookbook/source/imgs/media/image18.png){width="5.756944444444445in"
height="1.80625in"}

3)  关联Openfalcon监控的设备

![](/root/work/scrapy-cookbook/source/imgs/media/image19.png){width="5.766666666666667in"
height="2.457638888888889in"}

提示：1：若该设备已被Openfalcon监控，且在选择列表中，直接选择关联

2：若该设备已被Openfalcon监控，但不在选择列表中，请手动添加或者等待每天凌晨后自动获取

手动添加操作：查看openfalcon监控的设备名称\--\>填写名称\--\>自动获取ID\--\>确认

描述:用户界面展示该监控设备的名字，若不填描述则展示名称

> ![](/root/work/scrapy-cookbook/source/imgs/media/image20.png){width="3.4791666666666665in"
> height="2.8784722222222223in"}

3：若该设备未被Openfalcon监控，请先选择跳过且对后续ce下发相关配置

4)  关联日志设备

请手动添加且关联，若不需要请先跳过

注意：IP：添加的IP是CE把logs发过来的source IP

描述：用户界面日志设备展示的名字，如不填则展示IP

![](/root/work/scrapy-cookbook/source/imgs/media/image21.png){width="5.757638888888889in"
height="2.3041666666666667in"}

5)  同步（重要）

同步的作用：把portal系统已经存在的所有用户（除admin外），把portal系统展示需要的数据来源全部同步到数据库，实现覆盖式或增量式填入

同步的条件：该客户的tellme系统/mrtg（1/3/4）系统/zabbix/Ticket/Openfalcon/拥有相关的客户数据。若没有也不会失败，但影响portal系统的数据展示

同步的时间：每天凌晨自动同步/手动同步。

3：账号管理

点击用户信息可以对账号的增删改查

![](/root/work/scrapy-cookbook/source/imgs/media/image22.png){width="5.766666666666667in"
height="2.277083333333333in"}

注意：删除操作不单单只删除该账号，且删自动删除关联的xxxx

4:服务系统

点击公共数据\--\>服务系统

![](/root/work/scrapy-cookbook/source/imgs/media/image23.png){width="5.768055555555556in"
height="2.70625in"}

提示：该列表数据一定不要随意删除或者改动。因为这里的数据关联代码获取对应的系统去做相关查询等等的操作获取数据
