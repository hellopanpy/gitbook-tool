##awscli

awscli为亚马逊云服务命令行管理工具，基本可以实现web后台所有的功能，同时还支持第三方服务器上安装使用

####安装

安装方式很简单

Windows下载并运行 64 位或 32 位 Windows 安装程序。

Mac 和 Linux，需要 Python 2.6.5 或更高版本。使用 pip 安装

`pip install awscli`

####配置

<pre><code>
aws configure

AWS Access Key ID [None]: access keyID

AWS Secret Access Key [None]: access Password

Default region name [None]: ap-southeast-1

Default output format [None]:
</code></pre>

####使用

查看当前region实例

`aws ec2 describe-instances`

备份到s3

`aws s3 mv src s3://backup-sg/`

####参考

[awscli介绍](https://aws.amazon.com/cn/cli/)

[使用文档](http://docs.aws.amazon.com/cli/latest/reference/#)