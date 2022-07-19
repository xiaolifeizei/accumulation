### git 同时推送多个仓库



```bash
git remote add origin 远程地址1
git remote set-url --add origin 远程地址2
# 推送到多个仓库
git push -u origin master
```

