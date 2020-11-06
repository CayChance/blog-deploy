# blog-deploy

### 添加新的内容
```
hugo new posts/.md
```

### 启动本地服务
```
hugo server -D
```

### 构建静态页面
```
hugo -D
```

### 部署
将代码提交后，Travis CI会自动部署
```
git add .
git commit -m 'xx'
git push origin matser
```

[hugo官网文档](https://gohugo.io/getting-started/quick-start/)
