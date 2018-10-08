一般情况MAC系统使用中的冗余主要是IOS备份文件。主要清理方法包括：
1.系统自带工具
2.第三方工具
3.命令行工具

## 1.系统自带工具
左上角apple图标=>about this mac=>storage=>manage=>recommendations
![](http://p27x0f47q.bkt.clouddn.com/20181008100300.png)
### store in iCloud
多设备同步会很好用，高效管理文件，释放本地空间。缺点是需要缴纳存储费用，隐私保护有风险。
### optimize storage
主要优化apple自带软件，包括itunes和downloads的管理，对于office等备份大户影响小，效果不明显。
### empty trash automatically
非计算机工作者可以点选这个选项，超过30天的垃圾会被自动清空。也可以使用快捷键手动删除：cmd + shift + delete。
### reduce clutter
手动管理本地文件，删除不需要的部分，优点是风险小，不会误删除，缺点是耗时，需要逐一浏览。

## 2.第三方工具
有很多第三方工具可以管理系统文件，例如CleanMyMac3, MacBooster, Dr.Cleaner, Onyx。这里推荐使用OmniDiskSweeper，原因是免费、简洁、高效。
可以从官网直接下载该工具，使用方法如下：
进入界面
![](http://p27x0f47q.bkt.clouddn.com/20181008101030.png)
选中要扫描的硬盘，选择sweep "XXX" Drive
![](http://p27x0f47q.bkt.clouddn.com/20181008101143.png)
扫描需要一些时间，会根据空间由大到小排序，如果对于OS没有深入研究，建议只优化Users和Applications，因为二者相对比较安全，且普遍占用空间较大。
Applications主要筛选已经卸载的软件删除，或者卸载当下不再使用的软件；Users可以清理Guest账户的文件和主用户文件，主用户一般Library占用空间比较大，建议清理其中的Caches和Application Support，大多数人都可以清理其中xxx.Office的微软日志和备份，如果使用时间久，这个文件往往会占用数十G的空间。
清除方法是选中要清除的文件，点击左下角的Trash。
通过这样的方式，往往可以释放10G+的系统空间。

## 3.命令行工具
如果是一个程序员，当然希望从系统底层开始扫描并清理系统。分析系统空间情况的命令如下：
```shell
sudo du -sh *
```
因为Mac默认terminal路径是"~"，所以这条命令扫描的往往是这个路径下的情况，如果要分析其他路径情况，可以cd到不同路径执行这条命令。
再逐步根据空间占用大的路径追踪，找到“罪魁祸首”删除即可，例如"XXX.Office"的office日志文件，试试看能不能找到并删除它？如果你是IOS程序员，那么可以清理Xcode的历史遗留问题，路径在/Users/<用户名>/Library/Developer/Xcode/iOS DeviceSupport，一般可能有数十G。