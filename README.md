# Manjaro安装手记

## 1.下载Manjaro

​	通过[Manjaro](https://manjaro.org/download/)官网下载安装镜像。

​	当前Manjaro官方集成的桌面环境有XFCE、KDE、GNOME三种，其他社区版中还有i3等等。

## 2.安装Manjaro

​	Manjaro安装类似于windows的安装，是通过图形界面安装的,所以按照提示步骤来即可，其中主要的是分区的选择。

### 2.1Manjaro分区

​	Manjaro可以通过一下几种方式来进行分区:

- 可以选择保留之前的分区重新安装系统
- 可以选择抹除分区但是保留分区信息安装系统
- 可以选择手动分区来

下面是手动分区的一个案例(用于个人电脑,以500G硬盘为例)：

| 分区  | 挂载点 | 大小                 | 标记            | 格式      | 描述                                                      |
| ----- | ------ | -------------------- | --------------- | --------- | --------------------------------------------------------- |
| /boot | /boot  | 300MB                | boot            | fat32     | 引导分区                                                  |
| root  | /      | 70GB                 | boot-grub、root | btrfs     | 根分区，并且存放grub引导的位置                            |
| swap  | 无     | 等价于内存大小(建议) | swap            | linuxswap | 交换分区，当需要使用休眠等功能呢个时推荐等价于内存大小    |
| opt   | /opt   | 10GB                 | 无              | btrfs     | opt分区，一般是软件安装的位置                             |
| var   | /var   | 20GB                 | 无              | btrfs     | var分区，pacman的包缓存存放在这里，注意清理，增长速度较快 |
| home  | /home  | 剩下全部             | 无              | btrfs     | 家分区                                                    |

以上分区只是建议，如果当硬盘较小，小于100G的时候，也可以只分出一个根分区即可,更多的分区信息参照Arch Wiki。

## 3.安装后配置

### 3.1更新系统

​	使用以下命令更新系统。

```shell
pamac upgrade
```

### 3.2更新国内源

​	Manjaro默认的软件源都是国外的，可以通过一下命令更换国内的软件源。

```shell
pacman-mirrors -c China -m rank
```

​	更新软件源后，新的源文件将会被写入到` /etc/pacman.d/mirrorlist`文件。

​	在更新国内的软件源后，可以再进行一下系统更新(或是先更改软件源然后再进行系统更新)。

### 3.3下载喜爱的编辑器

​	下载喜爱的编辑器，例如Vim编辑器，以供后续编辑文件使用，也可以使用KDE自带的编辑器nano。

```shell
pamac install vim 
```

### 3.4添加archlinuxcn源

​	archlinuxcn源是Arch Linux 中文社区仓库，一些国内的软件可以在这里找到。

​	编辑` /etc/pacman.conf`(该文件是Manjaro包管理器pacman的配置文件)文件添加软件源，在文件底部添加如下配置选项。

```
# 在这里配置archlinuxcn源,配置如下选项
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch 
```

​	编辑该文件保存之后，运行命令导入 GPG key：

```shell
 # 1.首先更新archlinuxcn源信息
 pacman -Sy
 
 # 2.安装archlinuxcn-keyring
 pamac install archlinuxcn-keyring
```

### 3.5安装常用字体包

​	安装中文语言支持及常用字体，有关字体的详细信息可以参看[Arch Wiki - Fonts](https://wiki.archlinux.org/title/Fonts_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)).

```shell
pamac install ttf-roboto noto-fonts ttf-dejavu
# 文泉驿
pamac install wqy-bitmapfont wqy-microhei wqy-microhei-lite wqy-zenhei
# 思源字体
pamac install noto-fonts-cjk adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts
#fira编程字体
pamac install ttf-fira-code
# Nerd 扩展字体，zsh 可用
pamac install nerd-fonts-complete
# Meslo Nerd字体，zsh可用
pamac install ttf-meslo-nerd-font-powerlevel10k
# 微软字体（安装时出现了错误，参见下面手动安装）
#pamac build ttf-office-2007-fonts ttf-ms-win10
```

​	对于Linux上的字体文件，一般存放于` /usr/share/fonts`和` ~/local/share/fonts`(`~/.fonts/`已过时)这两个文件夹中，其中前者用于系统范围的字体文件，而后者只适用于特定的用户范围。

​	故而对于在软件源里不包含的字体文件，需要手动安装的文件，既可以根据需要将字体放入对应的文件夹中，并刷新缓存后即可使用了。

```shell
#刷新字体缓存
fc-cache -f
```

#### 3.5.1 手动安装微软字体

​	可以通过手动拷贝字体文件到字体文件夹的方式安装微软字体:

- 在Windows系统下的` C:\Windows\fonts`文件夹中，拷贝所有字体到` /usr/share/fonts/windows-fonts`下，或用户目录的fonts文件夹下
- 然后刷新字体缓存即可

### 3.6安装中文输入法

​	Linux下的中文输入法主要通过输入法框架来实现，成熟的框架有IBus、fcitx、fcitx5和scim(已停止维护)，其中fcitx和fcitx5不兼容，故需要二选一使用。

​	在这里使用fcitx5输入法框架，安装如下软件包：

```shell
# 安装输入法框架包组,提供了基本的输入法支持,包括配置工具等
pacman -S fcitx5-im 
#安装中文输入法引擎(这里使用了rime输入法，其他的还有sogou输入法、baidu输入法、google输入法等,可以择其一使用)
pamac install fcitx5-rime
```

安装上述输入法框架和输入法引擎后，需配置一下环境变量已启用输入法，编辑`~/.pam_environment`文件，添加一下内容：

```
GTK_IM_MODULE DEFAULT=fcitx
QT_IM_MODULE  DEFAULT=fcitx
XMODIFIERS    DEFAULT=\@im=fcitx
INPUT_METHOD  DEFAULT=fcitx
SDL_IM_MODULE DEFAULT=fcitx
```

添加输入法自启动：

- 在kde环境下，执行以下命令添加自启动；

  ```shell
  cp /usr/share/applications/org.fcitx.Fcitx5.desktop ~/.config/autostart/
  ```

- ​	在wm环境中，需要添加fcitx5程序到对应的autostart脚本中进行自启动

添加完成后，重启系统使输入法生效。

### 3.7配置rime输入法

#### 3.7.1rime基本配置

​	rime输入法的基本配置文件在`~/.local/share/fcitx5/rime`文件下下，新建` default.custom.yaml`文件，通过patch的方式配置rime输入法的基本配置，该修改针对整个输入法都有效。

```yaml
patch:
  schema_list:
    - schema: luna_pinyin_simp         # 朙月拼音
    - schema: double_pinyin        # 自然碼雙拼
  "menu/page_size": 9
  "key_binder/bindings":
    - { when: always, accept: Release+Escape, toggle: ascii_mode}
  ascii_composer:
    switch_key:
      Shift_L: commit_code
      Shift_R: inline_ascii
  punctuator:
    import_preset: default
  switcher:
    hotkeys:
      - F4
```

#### 3.7.2导入词库

​	词库的配置主要在对应输入法的`.custom.yaml`结尾的文件中(例如luna_pinyin_simp.custom.yaml)配置词库配置文件的名称,其配置项如下：

```yaml
patch:
  switches:
    - name: ascii_mode
      reset: 0                                          # 默认英文
      states: ["中", "英"]
    - name: full_shape
      reset: 0                                          # 默认半角
      states: ["半", "全"]
    - name: zh_simp
      reset: 1                                          # 预设简体
      states: ["繁", "简"]
    - name: ascii_punct
      states: ["。，", "．，"]
    - options: [utf8, gbk]                              # 字符集
      reset: 1                                          # 默认 GBK
      states: ["utf8", "gbk"]

  "engine/filters/@next": charset_filter@gbk            # 默认 UTF8
  "engine/translators/@next": reverse_lookup_translator

  translator:
    dictionary: luna_pinyin.extended   					#配置词库配置文件的名称
    prism: luna_pinyin_simp

  "speller/algebra/@before 0": xform/^([b-df-hj-np-tv-z])$/$1_/

  punctuator:                                           # 符号快速输入和部分符号的快速上屏
    import_preset: symbols
    full_shape:
      "\\": "、"
    half_shape:
      "#": "#"
      "`": "`"
      "~": "~"
      "@": "@"
      "=": "="
      "/": ["/", "÷"]
      '\': "、"
      "'": {pair: ["「", "」"]}
      "[": ["【", "["]
      "]": ["】", "]"]
      "$": ["¥", "$", "€", "£", "¢", "¤"]
      "<": ["《", "〈", "«", "<"]
      ">": ["》", "〉", "»", ">"]

  recognizer:
    patterns:
      email: "^[A-Za-z][-_.0-9A-Za-z]*@.*$"
      uppercase: "[A-Z][-_+.'0-9A-Za-z]*$"
      url: "^(www[.]|https?:|ftp[.:]|mailto:|file:).*$|^[a-z]+[.].+$"
      punct: "^/([a-z]+|[0-9]0?)$"
      reverse_lookup: "`[a-z]*'?$"
```

根据上述词库配置文件的名称luna_pinyin.extended，建立对应的词库配置文件(luna_pinyin.extended.dict.yaml),然后在其中引入词库文件,词库文件是一系列以`.dict.yaml`结尾的文件，如下所示：

```yaml
---
name: luna_pinyin.extended
version: "2015.12.02"
sort: by_weight
use_preset_vocabulary: true
import_tables:
  - luna_pinyin
  - luna_pinyin.extra_hanzi
  #- luna_pinyin.sgmain
  #- luna_pinyin.sgplus
  #- luna_pinyin.sgplus2
  - luna_pinyin.chat
  - luna_pinyin.net
  #- luna_pinyin.user
  - luna_pinyin.cn_en
  #- luna_pinyin.english
  - luna_pinyin.biaoqing
  - luna_pinyin.website
  - luna_pinyin.game
  - luna_pinyin.anime
  - luna_pinyin.classical
  #- luna_pinyin.moba
  #- luna_pinyin.place
  - luna_pinyin.poetry
  #- luna_pinyin.movie
  #- luna_pinyin.music
  - luna_pinyin.computer
  - luna_pinyin.hanyu
  - luna_pinyin.practical
  - luna_pinyin.basis
  - luna_pinyin.idiom
  - luna_pinyin.sougou
  - luna_pinyin.history
  - symbols
  - luna_pinyin.name
  #- luna_pinyin.shopping
```

**注意，词库配置文件要与词库文件处于统一目录。**

除了上述方式，还可以通过自动的方式更新搜狗词库文件，进行配置。

#### 3.7.3配置输入法皮肤

​	fcitx5内置了只有一种皮肤，很丑，aur中也有几种皮肤包，但是不是很好，可以在[Github](https://github.com/sxqsfun/fcitx5-sogou-themes)上探索更多的皮肤然后下载下来，将其复制到`~/.local/share/fcitx5/themes`目录下或者到`/usr/share/fciyx5/themes`目录下，然后在fcitx的配置界面中，选择配置附加组件，然后选择经典用户界面，选择喜爱的主题即可。

​	**注意：主题必须是适用于fcitx格式的主题。**

### 3.8 zsh及oh-my-zsh

​	zsh(Z-shell)类似于bash，是一个运行在终端仿真器中的shell，其对shell相关的语法有很好的支持,并且比bash拥有更多的创新，所以可以用其来替代bash。

#### 3.8.1 安装zsh

​	在Arch系上，可以通过以下命令安装ZSH：

```shell
pamac install zsh
```

​	通过一下命令来查看当前系统环境使用的默认的shell:

```shell
echo $SHELL
```

​	通过一下命令更改系统默认shell为zsh：

```shell
chsh -s /usr/bin/zsh
```

​	运行上面命令更改shell成功后，需要重启(或注销)后才可以生效。

#### 3.8.2安装oh-my-zsh

​	Oh My Zsh 是一个开源社区的驱动框架，用于管理 Zsh 配置。它捆绑了数以千计的有用功能、可以进行深度的配置，主题美化等。

​	可以通过一下命令安装oh-my-zsh:

```shell
# 1.通过curl的方式安装
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# 2.通过wget的方式安装
sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

如果在安装oh-my-zsh之前已经在家目录下存在`.zshrc`文件，那么安装oh-my-zsh后将会有一个新的`.zshrc`文件，而之前的文件将备份为`.zshrc.pre-oh-my-zsh`文件，如果该文件不再需要的话可以删除。

#### 3.8.3配置oh-my-zsh

​	**自定义配置存放位置：**

​	安装oh-my-zsh后会配置一个环境变量`$ZSH_CUSTOM`，该环境变量所指向的目录即是自定义主题或插件的存放位置，只需要将必要的插件信息或主题信息放置在该变量指向的目录下，然后在,zshrc中指定使用即可完成配置。

​	可以通过在.zshrc中配置如下信息来更改`$ZSH_CUSTOM`所指向的位置：

```
ZSH_CUSTOM=$HOME/my_customizations
```

​	**添加插件：**

​	添加插件需要在`$ZSH_CUSTOM`位置下创建plugins目录，然后创建以插件名称命名的文件夹，该文件夹下需要编写一个`xxx.plugin.zsh`文件供oh-my-zsh加载，注意这里的后缀`.plugin.zsh`是一定的，例如加载foobar插件：

```
1.在.zshrc文件中编写如下内容
plugins=(git bundler foobar)

2.创建如下目录结构
$ZSH_CUSTOM
└── plugins
    └── foobar
        └── foobar.plugin.zsh
这样该插件将在下次shell启动时被加载
```

​	**添加主题：**

​	配置主题类似于插件：

```
1.创建如下目录结构
$ZSH_CUSTOM
└── themes
    └── my_awesome_theme.zsh-theme
2.在.zshrc中编写如下配置
ZSH_THEME="my_awesome_theme"
```

#### 3.8.4 zsh其他配置

#### 3.8.5 为oh-my-zsh使用Powerlevel10k

​	使用Powerlevel10k很简单，在字体部分安装了Powerlevel10k所必要的字体后，设置终端字体为meslo-nerd-font，然后从Github上克隆该仓库，编辑` .zshrc`中的TAHEME选项，即可启用Powerlevel10k：

```
1.克隆到本地
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
2.设置.zshrc中的主题
ZSH_THEME="powerlevel10k/powerlevel10k"
```

​	通过上述设置完成后，关闭终端然后重启终端，就会自动的启动Powerlevel10k的交互配置，如果没有自动启动，择可以通过` p10k configure`来启动，然后按照提示进行配置即可。

​	该配置将会在家目录下创建一个`.p10k.zsh`文件，编辑该文件可以启用一些选项，比如硬盘、网速、等监控信息。

#### 3.8.6为oh-my-zsh安装常用插件

- [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)

  该插件可以使zsh记录你所输入过的历史命令，当你开始键入时，它会自动尝试提示历史输入，然后通过tab键既可以补全。

  ```
  1.clone该插件到oh-my-zsh下
  git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
  2.在.zshrc中启用该插件
  plugins=( 
      # other plugins...
      zsh-autosuggestions
  )
  ```

- [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)

  有时候命令太多记不住，等输完执行的时候才发现输错了，这个插件可以在输入的时候通过颜色区分来提示命令是输错了还是输对了。

  ```
  1.clone该插件到oh-my-zsh下
  git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
  2.在.zshrc中启用该插件
  plugins=( 
      # other plugins...
      zsh-syntax-highlighting
  )
  ```

- [sudo](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/sudo)

  sudo插件主要是在命令行中某些命令的执行需要通过sudo提升权限，命令输完之后再去命令开始输入sudo比较慢，通过该插件，这个时候可以在命令输入的任何地方按下两次Esc键，即可在开头加上sudo命令了;该插件在oh-my-zsh中已经提供了，只是未开启，直接在配置文件中开启即可:

  ```
  在.zshrc中启用该插件
  plugins=( 
      # other plugins...
      sudo
  )
  ```

- [autojump](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/autojump)

  autojump可以使我们很快捷的进入之前已进入过的目录或是子目录等，通过键入` j 目录名称`即可,该目录名称不需要全路径，甚至可以只输入一部分(该部分即可唯一标识该目录)，即可快速进入该目录了。

  ```
  1.在manjaro中使用需要先安装autojump工具
  pamac build autojump-git
  
  2.然后在.zshrc中启用autojump即可
  plugins=( 
      # other plugins...
      autojump
  )
  ```

- [cp](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/cp)

  cp插件定义了一个`cpv`(通过别名的方式可以使所有的本机拷贝使用该命令完成)命令来使用rsync拷贝文件，rsync是主机之间或本地备份文件的首选命令，其在拷贝时会只拷贝变化的部分，所以其用来备份等可以很快完成，该插件在oh-my-zsh中已经提供了，直接启用即可:

  ```
  1.在.zshrc中启用cp即可
  plugins=( 
      # other plugins...
      cp
  )
  ```

- [copydir](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/copydir)

  copydir插件可以直接将当前所处路径复制到剪贴板供使用，使用copydir命令来完成拷贝，该插件已经默认在oh-my-zsh中提供了，启用即可:

  ```
  1.首先安装xclip
  pamac build xclipd-git
  
  2.在.zshrc中启用copydir即可
  plugins=( 
      # other plugins...
      copydir
  )
  3.通过copydir命令拷贝当前所在路径
  ```

- [copyfile](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/copyfile)

  copyfile插件的作用类似于上面copydir插件的作用，该插件定义了一个`copyfile 文件名`命令来将当前路径下的指定文件的全路径拷贝到剪贴板中，该插件已经默认在oh-my-zsh中提供了，启用即可:

  ```
  1.首先安装xclip
  pamac build xclipd-git
  
  2.在.zshrc中启用copyfile即可
  plugins=( 
      # other plugins...
      copyfile
  )
  3.通过copyfile命令拷贝指定文件的路径
  ```

- [encode64](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/encode64)

  encode64插件启用base64编码功能，输出编码或解码后的内容。

- [fzf](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/fzf)

  该插件的作用还不清楚。
  
- [extract](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/extract)

  该插件添加一个extract命令用来在命令行下解压文件，大部分压缩文件格式其都支持。

  ```
  2.在.zshrc中启用extract即可
  plugins=( 
      # other plugins...
      extract
  )
  ```

#### 3.8.7 配置别名

​	在`.zshrc`中配置常用的别名：

```
#使用cpv代替cp命令,并且增加目录支持
alias cp="cpv -r"
#使用cls代替clear命令
alias cls="clear"
#增加grep颜色显示
alias grep="grep --color=auto"
#显示系统信息(在使用该别名前需要先下载neofetch和lolcat)
alias neo="neofetch|lolcat"
#解压命令
alias x="extract"
#颜色显示cat命令
alias cat="lolcat"
#配置终端下删除回收站(需要先在aur中下载rmtrash)
alias rm="rmtrash"
```

#### 3.8.8其他oh-my-zsh配置

##### 3.8.8.1启动标语

​	可以在终端启动时输出一个标语，利用lolcat可以彩色输出。

![image-20211016222810298](Manjaro%E5%AE%89%E8%A3%85%E6%89%8B%E8%AE%B0.assets/image-20211016222810298.png)

```
1.安装lolcat
pamac install lolcat
2.安装cowfortune
pamac install cowfortune
3.在.zshrc最后配置如下命令
fortune | cowsay | lolcat
```

