# 写在前面的话：  
整理了遇到的一些问题以及解决方法，希望后来人能够少走弯路。  
部分方法按照参考链接做就行；部分方法则对各参考链接进行了一定的整理，可先对照Guide做，有不明白之处再查阅参考链接。  
方法不一定是最优解，仅供参考。

# 服务器  

* 基本操作  

  * 环境变量  
    `PREMIC服务器使用指南.pdf`勘误：  
    ```
    $ cp /public/data/examples/env.bashrc ~/.bashrc
    ```

  * 杂项  
    `/public/home/user/data3`下为私人文件，一般自己没有特殊要求的文件都放在这里；  
    `/public/home/user`下可设置权限供其他用户浏览，需要共享的文件可以放在这里（共享之前注意看一下`/public/home/user`的权限设置）。  
    权限设置相关可参阅：[权限参数含义](https://zhuanlan.zhihu.com/p/255000117)、[如何修改权限](https://blog.csdn.net/my565548320/article/details/134396057)。

# Xshell & Xftp & Xming  
[下载及安装教程](https://blog.csdn.net/m0_67400972/article/details/125346023)  
[配色方案](https://blog.csdn.net/bjarnecpp/article/details/111213072)  

* 杂项  
  
  * ncview无法正常显示图形程序窗口（MobaXterm应该没有这个问题）  
    按照[参考链接](https://blog.csdn.net/Strive_For_Future/article/details/123219083)以及里面提到的其他参考链接做就行。  
    需要补充的是：`X0.hosts`文件可用记事本打开；  
    ```shell
    $ export DISPLAY=101.6.75.115:0.0
    $ echo $DISPLAY
    ```
    上面这步可以不做；第四、第五步可以不做（与ncview无关）；实际使用时Xming要在Xshell之前打开。  

# VSCode  

* 基本操作  

  * 下载&连接远程服务器（网上看到说PyCharm也可以连接服务器，但我试了一天没成功。听师姐说组里其他人基本上都是用的这个，而且后续不止要写Python代码，用VSCode能更灵活一些。所以建议还是用VSCode连接服务器调代码）  
    按照[参考链接](https://blog.csdn.net/zhaxun/article/details/120568402)做进行。  

  * 免密登录&跳转登录  
    按照[参考链接](https://blog.csdn.net/weixin_43971060/article/details/128306755)做就行。  
    需要补充的是：`id_rsa.pub`在`C:\Users\username\.ssh`内；登录不进去可能是VSCode本机配置文件中节点名字设置不规范，例如`Host user@mgt`，配置文件写法可参考`PREMIC服务器使用指南.pdf`。  

# Python  

* 虚拟环境  

  * 安装虚拟环境  
    按照[参考链接](https://blog.csdn.net/wyf2017/article/details/118676765)做就行。
    需要补充的是：第1.6步，即安装过程中出现如下提示时  
    ```shell
    Anaconda3 will now be installed into this location:
    /public/home/user/anaconda3
    ```
    建议调整安装路径至`/public/home/user/data3/anaconda3`；pytorch库按需安装，即“然后去选择适合自己的pytorch版本，点击下面那个链接”及之后的内容可以无视。  
    [Anaconda环境管理命令](https://zhuanlan.zhihu.com/p/508319902)  
    不要使用base环境。由于Python是不向后兼容的，所以使用Anaconda时为避免不同库之间的冲突会创建一个虚拟环境，这样既可以避免语法版本不一引起的错误，也可以避免工具包安装与调用的混乱。  
    创建好虚拟环境后不用着急下载库，后续运行Python文件时会报错：“缺少某某库，请尝试使用某某命令进行下载”，到时候再根据终端的提示对应下载就行。  
    在`.bashrc`中添加`conda activate envname`，可使登录服务器后默认使用所创建的虚拟环境。  

  * ncdump等NCO命令无效  
    按照[参考链接](https://github.com/spencerahill/aospy/issues/58)中spencerkclark的回答做就行。  
    需要补充的是：如果conda命令无效可以尝试使用pip命令。  

* matplotlib  

  * 设置：在服务器画图时，中文字体为宋体，英文字体为Times New Roman  
    参考链接：[字体来源](https://blog.csdn.net/Crayonxin2000/article/details/119910846)、[解决方法](https://blog.csdn.net/liu_xzhen/article/details/122881997)  
    字体下载：<https://wwae.lanzoub.com/iKlne0t6jb3e>  
    安装好字体后在`C:\Windows\Fonts`下找到`SunTimes.ttf`文件，将其上传到服务器（存放地址可随意，后面再细调）。  
    ```python
    import matplotlib
    from matplotlib import get_cachedir

    print('字体配置文件：', matplotlib.matplotlib_fname())  # 得到mpl-data/matplotlibrc
    print("字体缓存文件：", get_cachedir())  # 得到.cache/matplotlib
    ```
    将`SunTimes.ttf`复制到字体配置文件所在文件夹`mpl-data`的`fonts/tff`文件夹下；  
    删除字体缓存文件（记得将user改为自己的用户名）；  
    ```shell
    $ rm -rf /public/home/user/.cache/matplotlib  
    ```
    vim打开`matplotlibrc`，作如下修改并删除该行最前面的`#`以取消注释：  
    ```shell
    font.family: serif  # 默认字体族为衬线体
    ...
    font.size: 10.0  # 默认字体大小，可以按需进行修改

    font.serif: SunTimes, DejaVu Serif, Bitstream Vera Serif, Computer Modern Roman, New Century Schoolbook, Century Schoolbook L, Utopia, ITC Bookman, Bookman, Nimbus Roman No9 L, Times New Roman, Times, Palatino, Charter, serif  # 调用serif时优先使用SunTimes
    ...
    mathtext.fontset: stix  # matplotlib渲染数学字体时使用的字体，和Times New Roman差别不大
    ...
    axes.unicode_minus: False  # 正常显示负号
    ```
    完毕。  

# WRF（Weather Research & Forecasting Model）  
[User Guide](https://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/contents.html)  
[WRF4.4中文手册](https://www.zhihu.com/column/c_1556313898067869696)  
[namelist](https://www2.mmm.ucar.edu/wrf/users/namelist_best_prac_wrf.html)  
[namelist.wps](https://zhuanlan.zhihu.com/p/658394077)  
[namelist.input](https://zhuanlan.zhihu.com/p/658583169)  
[Output Fields](https://zhuanlan.zhihu.com/p/517670905)  

# .nc  

* NCL（NCAR Command Language）  
  [官网](https://www.ncl.ucar.edu/)  
  [学习指南](https://ncl.readthedocs.io/zh-cn/latest/index.html)  
  [常用函数](https://renqlsysu.github.io/2018/03/19/ncl_math_function/)  
  切记切记：NCL中`4/3`结果是`1`（`Type: integer`），`4./3`结果才是正常的`1.333333`（`Type: float`）。  

* NCO（netCDF Operators）  
  [User Guide（html）](https://nco.sourceforge.net/nco.html)  
  [User Guide（pdf）](https://nco.sourceforge.net/nco.pdf)  
  [笔记](https://blog.csdn.net/qq_38607066/article/details/110941696)  

# 文献查找与下载  

* ResearchGate：<https://www.researchgate.net>  
  * 注册/登录：使用学校邮箱注册并登录。  
  * 使用：可以使用关键词检索文献并下载；查看感兴趣的研究者的所有文章；在设置里可以添加Primary email（QQ邮箱不行），当关注的研究者在发表文章时能及时收到邮件。  

* Sci-Hub：<https://www.sci-hub.se>  
  * 注册/登录：无需登录。  
  * 使用：由于上面的链接多数时候是打不开的，因此在其他网站上看到感兴趣的文章（有DOI）但无法下载时可以考虑使用这个网站，使用时直接在链接后加上文章的DOI就行。  
  例如：下载`DOI:10.1016/j.jweia.2011.01.013`的文章时打开<https://www.sci-hub.se/10.1016/j.jweia.2011.01.013>即可。  

* Web of Science：<https://www.webofscience.com/wos>  
  * 注册/登录：选择`机构登录`中的`CHINA CERNET Federation` → 选择学校 → 使用学号（注意：不是邮箱）登录。   
  * 使用：可以使用关键词检索文献并下载，检索时支持多条件查找。  

* ScienceDirect：<https://www.sciencedirect.com>  
  * 注册/登录：使用学校邮箱注册并登录。  

* arXiv：<https://arxiv.org>  

* Z-Library：<https://www.kdocs.cn/l/cq7kDog4zkuR>  
  * 使用：可下载电子书。  