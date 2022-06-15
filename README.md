#### 一款简洁的能够快速使用ssh连接服务器的命令行工具

**原因**：平时有时候需要使用ssh连接服务器，由于ssh不能保存密码，所以每次都需要手动输入密码，有的服务器又必须用密码链接无法使用秘钥，这时就需要复制密码-粘贴，然后连接，感觉特别的麻烦。
于是自己开发了一个可以用于管理ssh密码并且快速连接的工具，UI界面已于2.0版本纯自行手写

1. 快速（命令行打开基本上2秒内可以找到想要的服务器，支持搜索）
2. 不用复制粘贴密码了
3. 保持心跳（不知道为什么我mac上面的ssh配置过一段时间就会被重置，导致ssh不会发送心跳和服务器断开连接）

#### 更新日志：

- 2.0版
  - 移除对promptui的依赖，因为后续想添加一键scp和端口转发功能，这个库自由度不够，所以2.0的UI界面是自己实现的
  - 移除分组功能，因为已经支持了全局搜索，多一个分组感觉多此一举。

![demo](./screenshot/demo.gif)

#### 安装：

1. 下载源码手动编译或者直接下载我编译好的二进制文件
2. linux或mac可自己配置一个命令别名写入环境文件中`alias ss="~/go_ssh"`

#### 使用：

1. 首次执行命令会在文件所在的目录生成一个go_ssh.yaml的配置文件，启动时可以添加`-p`参数修改配置文件名，配置文件采用yaml格式编辑。内容如下：

   ```yaml
    # 欢迎使用Go SSH 工具
    # 字段说明
    # name         ： 自定义的服务器名字 可不填
    # user         ： 服务器名 不填默认 root
    # host         ： 服务器域名或ip  ！！！必填！！！  不填的话，列表不会出现这条配置
    # port         ： 端口号  不填默认 22
    # password     ： 密码    不填默认用秘钥
    # key          ： 私钥    不填默认  ~/.ssh/id_rsa
    # passphrase   ： 私钥的密码  可不填
    # keepalive    ： 心跳包发送间隔
    # jump         ： 跳板机 配置同上


    - { user: appuser, port: 22, password: 123456 }
    - { name: 使用秘钥, user: appuser, host: 192.168.8.35, port: 22}
    - { name: 使用带密码的秘钥, user: appuser, host: 192.168.8.35, port: 22, passphrase: abcdefghijklmn}
    - { name: 不配置端口, user: appuser, host: 192.168.8.35 }
    - { name: 不配置用户名, host: 192.168.8.35 }
    - { name: 不设置密码, host: 192.168.8.35 }
    - { name: ⚡️ 带emoji表情, host: 192.168.8.35 }
    - name: 通过跳板机连接
      user: appuser
      host: 192.168.8.35
      port: 22
      password: 123456
      jump:
        - user: appuser
          host: 192.168.8.36
          port: 2222
   ```

2. 根据自己的需求，编写配置文件。

3. 保存之后重新执行命令即可。

#### 操作方式：

|     键位      |                            作用                            |
| :-----------: | :--------------------------------------------------------: |
|     ↑ / ↓     |                    控制光标向上/下移动                     |
|     ← / →     |                   控制上下翻页，每页10条（暂时移除）              |
| a-z、A-Z、0-9 | 可直接在当前页面搜索服务器包含字段：序号、名字、用户名、IP |
|    Ctrl+C     |                          退出程序                          |
|     Enter     |                      连接选中的服务器                      |

#### 未来版本计划：

1. 更简单的管理连接的用户名或密码，就是连接没有保存过密码的服务器时自动进行保存密码，用该工具替代`ssh root@xxx`这个命令，变成例如：`go_ssh root@xxx`通过工具连接过的服务器可以自动进行保存并更新到配置文件。这样的话，就动管理配置文件了。
2. scp上传下载文件，这个问题目前我遇到的比较多，有时候需要从服务器下载文件，有时候需要上传，还是需要手动复制ip，复制密码。实现方式还没想好……
3. 动态排序最近连接的服务器（这个功能可能有人喜欢有人不喜欢，待定吧）
