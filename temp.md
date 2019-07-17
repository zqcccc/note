fontend masters 的 Complete intro to React v4 课程

```html
<pre>
	<code>
		{JSON.stringify(data, null, 4)}
	</code>
</pre>
<!-- 格式化输出数据 -->
```

react component 必须有 render 方法，必须有返回内容

`@reach/router` 也可以作为 react 的路由

使用 react dev tool 的情况下，用 `$r` 可以访问到选中的元素（组件），可以调用 `setState` 

# umi 开发笔记

布局相关：在组件中写 `this.props.children` 可以配置子路由的内容，这样可以来做布局，例如：

```js
{
    path: '/',
    component: 'App',
    routes: [{
      path: 'list'
      component: 'List',
    }, {
      path: 'admin'
      component: 'Admin',
    }]
  }
```

在 `page/App`  中：

```jsx
export default (props) => <div style={{padding: 20}}>
    {this.props.children}
</div>
```

这样访问 `/list` 跟 `/admin` 将会都有这个 `layout`。

ftp://192.168.30.2

zoenn

zhongyin2019

```nginx
server {
    listen 8005;
    server_name zoennadmin;
    # gzip config
    gzip on;
    gzip_min_length 1k;
    gzip_comp_level 9;
    gzip_types text/plain text/css text/javascript application/json application/javascript application/x-javascript application/xml;
    gzip_vary on;
    gzip_disable "MSIE [1-6]\.";

    root /volume1/Zoenn/Wireframe/admin;

    location / {
        try_files $uri $uri/ /index.html;
    }


   location /adminapi {
      proxy_pass http://zoeen.wangcg.com/;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header REMOTE-HOST $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;


   }
}

```

