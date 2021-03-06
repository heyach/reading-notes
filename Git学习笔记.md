### Git学习笔记
***

### 常用命令
* 缓存修改
```js
git stash save "info"
```

* 删除缓存，按照list里的记录号
```js
git stash drop stash@{1}
```

* 弹出缓存，按照记录号
```js
git stash pop stash{0}
```

* 撤销commit保留add
```js
git reset --soft HEAD^
```

* 强行恢复到上一个版本
```js
git reset --hard HEAD^
```

* 撤销add
```js
git reset HEAD file
```

* 远程仓库回滚
```js
git checkout branch
git reset --hard commitA
git push -f
```

* 本地git创建的仓库关联完成仓库
```js
git remote add origin git@github.com:xxx.git
```

### rebase
对一段线性提交历史进行编辑，删除，复制，粘贴，保证提交记录干净整洁

