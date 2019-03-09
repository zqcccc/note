用 ejs 模板引擎的例子

```js
const Koa = require('koa')
const KoaRouter = require('koa-router')
const json = require('koa-json')
const path = require('path')
const bodyParser = require('koa-bodyparser')
const render = require('koa-ejs')

const app = new Koa()
const router = new KoaRouter()

// replace with DB
const things = ['family', 'programming', 'music']

// json prettier middleware
app.use(json())
// bodyParser middleware
app.use(bodyParser())

// simple middleware example
// app.use(async ctx => ctx.body = 'hello world')

render(app, {
  root: path.join(__dirname, 'views'),
  layout: 'layout',
  viewExt: 'html',
  cache: false,
  debug: false
})

// routes
router.get('/', index)
router.get('/add', showAdd)
router.post('/add', add)

// list of things
async function index(ctx) {
  await ctx.render('index', {
    title: 'things i love:',
    things: things
  })
}

// show add page
async function showAdd(ctx) {
  await ctx.render('add')
}

// add thing
async function add(ctx) {
  const body = ctx.request.body
  things.push(body.thing)
  ctx.redirect('/')
}

router.get('/test', ctx => ctx.body = 'hello test')

// router middleware
app.use(router.routes()).use(router.allowedMethods())

app.listen(3000, () => console.log('Server started...'))

```

