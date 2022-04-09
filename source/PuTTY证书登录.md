# **PuTTY-login**
例子：![](Aspose.Words.bdf30415-5eef-494e-9141-3b94c18b7c8e.001.png)

1.填写公网IP

![](Aspose.Words.bdf30415-5eef-494e-9141-3b94c18b7c8e.002.png)

2.填写用户名

![](Aspose.Words.bdf30415-5eef-494e-9141-3b94c18b7c8e.003.png)

3.选择证书

![](Aspose.Words.bdf30415-5eef-494e-9141-3b94c18b7c8e.002.png)

4.成功登录

![](Aspose.Words.bdf30415-5eef-494e-9141-3b94c18b7c8e.004.png)

5.实现证书登录（简易版）

使用puttygen.exe生成公钥，选择ssh-1然后点击Generate

![](Aspose.Words.bdf30415-5eef-494e-9141-3b94c18b7c8e.005.png)

点击save private key生成私钥（私钥相当于密码）

‘![](Aspose.Words.bdf30415-5eef-494e-9141-3b94c18b7c8e.005.png)

vi ~/.ssh/authorized\_keys,将生成的public key粘贴到文件中，chmod 700 ~/.ssh

![](Aspose.Words.bdf30415-5eef-494e-9141-3b94c18b7c8e.006.png)

![](Aspose.Words.bdf30415-5eef-494e-9141-3b94c18b7c8e.005.png)

![](Aspose.Words.bdf30415-5eef-494e-9141-3b94c18b7c8e.007.png)

测试

![](Aspose.Words.bdf30415-5eef-494e-9141-3b94c18b7c8e.004.png)

