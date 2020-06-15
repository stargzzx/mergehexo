---
title: git | 代码统计
date: 2020-06-15 00:07:23
categories:
- git
tags:
- git
---
如何查看自己的工作量？如何统计 git 中的代码数据？

<!-- more -->

## 参考资料

- [git代码统计](https://segmentfault.com/a/1190000008542123)

## 查看git上的个人代码量：

```git
git log --author="username" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }'
```

结果示例：(记得修改 username)

## 统计每个人增删行数

```git
git log --format='%aN' | sort -u | while read name; do echo -en "$name\t"; git log --author="$name" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }'
```

## 查看仓库提交者排名前 5

```git
git log --pretty='%aN' | sort | uniq -c | sort -k1 -n -r | head -n 5
```

## 贡献值统计

```git
git log --pretty='%aN' | sort -u | wc -l
```

## 提交数统计

```git
git log --oneline | wc -l
```

## 添加或修改的代码行数

```git
git log --stat|perl -ne 'END { print $c } $c += $1 if /(\d+) insertions/'
```



