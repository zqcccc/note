## ESLint 与 Git Hooks

### pre-push 配置

```bash
# .git/hooks/pre-push

#
# ESLint 检查
#

echo "开始 ESLint 检查"
# 运行 eslint 命令
npm run eslint
# $? 表示上一条命令的返回值，为 0 则成功，否则失败
if [ $? != $zero ]
then
    echo "ESLint 检查未通过"
    exit 1
fi
echo "ESLint 检查通过"
```

### package.json scripts 配置

```json
// package.json
"scripts":{
    "eslint": "eslint --config .eslintrc.prod.js --ext .js,.jsx --cache src",
}
```

