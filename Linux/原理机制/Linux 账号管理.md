
# 添加和删除

## useradd
[[useradd]]

### 默认值

1. 在 /etc/passwd 里面创建一行与账号相关的数据，包括创建 UID/GID/家目录等；
2. 在 /etc/shadow 里面将此账号的口令相关参数填入，但是尚未有口令；
3. 在 /etc/group 里面加入一个与账号名称一模一样的组名；
4. 在 /home 底下创建一个与账号同名的目录作为用户家目录，且权限为 700

**创建系统用户是默认不创建家目录**

### useradd 参考文件
	useradd -D
	/etc/default/useradd 

![[Pasted image 20240403230228.png]]
**SKEL=/etc/skel：用户家目录参考基准目录**
**UID/GID 还有口令参数又是在哪里参考**
	/etc/login.defs

![[Pasted image 20240403230406.png]]

1. mailbox 所在目录：用户的默认 mailbox 文件放置的目录在 /var/spool/mail，所以 vbird1 的 mailbox 就是在 /var/spool/mail/vbird1
2. shadow 口令第 4, 5, 6 字段内容：透过 PASS_MAX_DAYS 等等配置值来指定的，不过要注意的是，由于目前我们登陆时改用 PAM 模块来进行口令检验，所以那个 PASS_MIN_LEN 是失效的！
3. UID/GID 指定数值：上表中的 UID_MIN 指的就是可登陆系统的一般账号的最小 UID ，至于 UID_MAX 则是最大 UID 之意。

#### 给予新UID的过程

1. 先参考 UID_MIN 配置值取得最小数值； 
2. 由 /etc/passwd 搜寻最大的 UID 数值， 将 (1) 与 (2) 相比，找出最大的那个再加一就是新账号的 UID 了

4. 用户家目录配置值：『CREATE_HOME = yes』这个配置值会让你在使用 useradd 时， 主动加入『 -m 』这个产生家目录的选项，如果不想要创建用户家目录，就只能强制加上『 -M 』的选项。创建家目录的权限配置呢？就透过 **umask** 这个配置值啊！因为是 077 的默认配置，因此用户家目录默认权限才会是『 drwx------ 』
5. 用户删除与口令配置值：使用『USERGROUPS_ENAB yes』这个配置值的功能是： 如果使用 userdel 去删除一个账号时，且该账号所属的初始群组已经没有人隶属于该群组了， 那么就删除掉该群组。 
6. 『MD5_CRYPT_ENAB yes』则表示使用 MD5 来加密口令明文，而不使用旧式的 DES(注2) 

## passwd
[[passwd]]

**新的 distributions 大多使用 PAM 模块来进行口令的检验，包括太短、 口令与账号相同、口令为字典常见字符串等，都会被 PAM 模块检查出来而拒绝修改口令**

### PAM模块
	写在 /etc/pam.d/passwd中
该文件与口令有关的测试模块就是使用：pam_cracklib.so，这个模块会检验口令相关的信息， 并且取代 /etc/login.defs 内的 PASS_MIN_LEN 的配置


###  --stdin 
	直接升级用户的口令而不用再次的手动输入
```shell
echo "abc543CC" | passwd --stdin vbird2
```

### chage
[[chage]]

在第一次登陆时可以使用与账号同名的口令登陆， 但登陆时就会被要求立刻更改口令，更改口令完成后就会被踢出系统。再次登陆时就能够使用新口令登陆了

## usermod
[[usermod]]
	某些地方还可以进行细部修改。 此时，当然我们可以直接到 /etc/passwd 或 /etc/shadow 去修改相对应字段的数据


## userdel
	用户账号/口令相关参数：/etc/passwd, /etc/shadow
	使用者群组相关参数：/etc/group, /etc/gshadow
	用户个人文件数据： /home/username, /var/spool/mail/username..

通常我们要移除一个账号的时候，你可以手动的将 /etc/passwd 与 /etc/shadow 里头的该账号取消即可

一般而言，如果该账号只是『暂时不激活』的话，那么将 /etc/shadow 里头账号失效日期 (第八字段) 配置为 0 就可以让该账号无法使用，但是所有跟该账号相关的数据都会留下来

**使用 userdel 的时机**通常是『你真的确定不要让该用户在主机上面使用任何数据了！』

另外，其实用户如果在系统上面操作过一阵子了，那么该用户其实在系统内可能会含有其他文件的。 举例来说，他的邮件信箱 (mailbox) 或者是例行性工作排程 (crontab, 十六章) 之类的文件。 所以，**如果想要完整的将某个账号完整的移除，最好可以在下达 userdel -r username 之前， 先以『 find / -user username 』查出整个系统内属于 username 的文件，然后再加以删除**

# 用户功能

## finger
[[finger]]

![[Pasted image 20240403235701.png]]
## chfn
[[chfn]]

## chsh
[[chsh]]

### id

# 新增或者移除群组
	群组的内容都与这两个文件有关：/etc/group, /etc/gshadow


## groupadd
[[groupadd]]

## groupmod
[[groupmod]]

## groupdel
[[groupdel]]

## gpasswd
[[gpasswd]]


