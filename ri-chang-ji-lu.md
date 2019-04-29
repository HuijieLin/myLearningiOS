> ## mac终端加密压缩包

```
// 方式一
zip -q -r -e 20181221.zip 20181221

// 方式二
zip -r -P your_password 20190109.zip 20190109
-q 表示不显示压缩进度状态
-r 表示子目录子文件全部压缩为zip  //这部比较重要，不然的话只有something这个文件夹被压缩，里面的没有被压缩进去
-e 表示你的压缩文件需要加密，终端会提示你输入密码的
-m 表示压缩完删除原文件
-o 表示设置所有被压缩文件的最后修改时间为当前压缩时间
```

> ## mac开启信任任何来源

```
sudo spctl --master-disable
```

> ## 统计代码行数

```
// 代码行数统计
// 参数1：pl文件路径
// 参数2：代码路径
perl /Users/linhuijie/Downloads/cloc-1.80.pl .
// 脚本下载地址
https://github.com/AlDanial/cloc 在readme里面download里面

// git代码统计
// 这个可以统计两个commit之间的代码变更数量
git diff --stat 564938e 25bd21b
// 这个可以统计指定时间段指定用户的提交记录，
git log --author="your_name" --pretty=tformat:  --since ==2018-9-1 --until=2019-3-30 --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -;

// 粗略代码统计
find . -name "*.m" -or -name "*.h" -or -name "*.xib" -or -name "*.c" |xargs grep -v "^$"|wc -l

// 每个人的代码提交
git log --format='%aN' | sort -u | while read name; do echo -en "$name\t"; git log --author="$name" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -; done
```

> ## Xcode自动导入UIKIT

```
echo display @import UIKit >> ~/.lldbinit
```



