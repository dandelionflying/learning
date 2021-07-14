1.修改上次提交记录的comment

```
git commit --amend
```

<img src="pictures\git-pic1.png">"

输入i进入编辑模式，修改第一行信息，esc退出编辑模式，wq保存退出

2.github contribution中缺少记录

使用git log 查看git 记录：

<img src="pictures\git log.png">"

发现Author有差异，原因在于，缺少记录的push是在家里的电脑操作的，而家里的电脑设置的email与公司电脑上的不一致，故在github--settings--emails中，增加缺失的email：

<img src="pictures\github-add-email.png">"



问题成功解决。