##                    			node cli 开发

> cli全称Command-line interface，顾名思义是一种通过命令行来交互的工具或者说应用。SPA应用中常用的如vue-cli, angular-cli, node.js开发搭建express-generator，orm框架sequelize-cli，还有我们最常用的webpack，npm等。他们是web开发者的辅助工具，旨在减少低级重复劳动，专注业务提高开发效率，规范develop workflow。
>
> CLI的根据不同业务场景有不同的功能，但万变不离其宗，本质都是通过命令行交互的方式在本地电脑运行代码，执行一些任务。

#### cli的作用

- 减少重复性的工作，不再需要复制其他项目再删除无关代码，或者从零创建一个项目和文件。
- 根据交互动态生成项目结构和配置文件等。
- 多人协作更为方便，不需要把文件传来传去。

#### cli开发的思路

> 要开发脚手架，首先要理清思路，脚手架是如何工作的？我们可以借鉴 vue-cli 的基本思路。vue-cli 是将项目模板放在 git 上，运行的时候再根据用户交互下载不同的模板，经过模板引擎渲染出来，生成项目。这样将模板和脚手架分离，就可以各自维护，即使模板有变动，只需要上传最新的模板即可，而不需要用户去更新脚手架就可以生成最新的项目。那么就可以按照这个思路来进行开发了。

#### 常用cli第三方组件

- [commander.js](https://github.com/tj/commander.js)，可以自动的解析命令和参数，用于处理用户输入的命令。
- [download-git-repo](https://github.com/flipxfx/download-git-repo)，下载并提取 git 仓库，用于下载项目模板。
- [Inquirer.js](https://github.com/SBoudrias/Inquirer.js)，通用的命令行用户界面集合，用于和用户进行交互。
- [handlebars.js](https://github.com/wycats/handlebars.js)，模板引擎，将用户提交的信息动态填充到文件中。
- [ora](https://github.com/sindresorhus/ora)，下载过程久的话，可以用于显示下载中的动画效果。
- [chalk](https://github.com/chalk/chalk)，可以给终端的字体加上颜色。
- [log-symbols](https://github.com/sindresorhus/log-symbols)，可以在终端上显示出 √ 或 × 等的图标

#### 开始开发cli

1.新建一个项目，打开cmd命令，执行npm init,创建package.json
2.在根目录下创建一个index.js，作为主入口文件
3.安装本文上面所提的常用组件，在根目录下执行 

```
npm install commander download-git-repo inquirer handlebars ora chalk log-symbols -S
```

这时候会看到根目录下多了一个node_modules目录，里面有刚刚安装的几个模块，package.json里面Dependencies依赖了这几个模块，如下图

![1549002671957](C:\Users\winter\AppData\Roaming\Typora\typora-user-images\1549002671957.png)



pakage.json

```
{
  "name": "node-cli",
  "version": "1.0.0",
  "description": "学习开发nodecli",
  "main": "index.js",
  "bin": {
    "winter": "index.js"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "chalk": "^1.1.3",
    "commander": "^2.19.0",
    "download-git-repo": "^1.1.0",
    "handlebars": "^4.0.12",
    "inquirer": "^1.2.3",
    "lodash": "^4.17.11",
    "log-symbols": "^2.2.0",
    "ora": "^3.0.0"
  },
  "devDependencies": {}
}

```

* 全局方式运行自定义命令

```json
"bin": {
    "winter": "index.js"
  },
```



可以从上面的pakage.json代码中看到bin字段，是用来存放一个可执行的文件，也就是我们的入口文件。

* 执行npm link。它将会把**winter**这个字段复制到npm的全局模块安装文件夹node_modules内，并创建符号链接（symbolic link，软链接），也就是**将 winter的路径加入环境变量 PATH**

* 在index.js中添加代码

```
#!/usr/bin/env node
const program = require('commander')
console.log('hello')
program.parse(process.argv)
```

在终端运行`winter`

![1549008847107](C:\Users\winter\AppData\Roaming\Typora\typora-user-images\1549008847107.png)



#### commander介绍

commander灵感来自 Ruby，它提供了用户命令行输入和参数解析的强大功能，可以帮助我们简化命令行开发。
根据其官方的描述，具有以下特性:

- 参数解析
- 强制多态
- 可变参数
- Git 风格的子命令
- 自动化帮助信息
- 自定义帮助等

```
一个简单的例子：
```

```javascript
#!/usr/bin/env node
const program = require('commander')
const inquirer = require('inquirer')
const chalk = require('chalk')
program
    .command('module')
    .alias('m')
    .description('创建新的模块')
    .option('-a, --name [moduleName]', '模块名称')
    .action(option => {
        console.log('Hello World')
    })
    
program.parse(process.argv)
```

运行`winter app`，打印出来`hello world`

```
commander API
```

- **command** – 定义命令行指令，后面可跟上一个name，用空格隔开，如 .command( ‘app [name] ‘)
- **alias** – 定义一个更短的命令行指令 ，如执行命令**$ app m** 与之是等价的
- **description** – 描述，它会在help里面展示
- **option** – 定义参数。它接受四个参数，在第一个参数中，它可输入短名字 -a和长名字–app ,使用 **|** 或者**,**分隔，在命令行里使用时，这两个是等价的，区别是后者可以在程序里通过回调获取到；第二个为描述, 会在 help 信息里展示出来；第三个参数为回调函数，他接收的参数为一个string，有时候我们需要一个命令行创建多个模块，就需要一个回调来处理；第四个参数为默认值
- **action** – 注册一个callback函数,这里需注意**目前回调不支持let声明变量**
- **parse** – 解析命令行

> 生成帮助信息

```
winter m -help
```

![1549012683815](C:\Users\winter\AppData\Roaming\Typora\typora-user-images\1549012683815.png)



#### inquirer介绍

> 在开发的过程中，我们需要频繁的跟命令行进行交互，借助**inquirer**这个模块就能轻松实现，它提供了用户界面和查询会话流程。它的语法是这样的

```javascript
var inquirer = require('inquirer')
inquirer.prompt([/* Pass your questions in here */]).then(function (answers) {
    // Use user feedback for... whatever!! 
})
```

功能描述：

- input–输入

- validate–验证

- list–列表选项

- confirm–提示

- checkbox–复选框等等

  >  一个栗子

  ```javascript
  #! /usr/bin/env node 
  const program = require('commander')
  const inquirer = require('inquirer')
  const _ = require('lodash')
  const chalk = require('chalk')
  program
      .command('module')
      .alias('m')
      .description('创建新的模块')
      .option('--name [moduleName]')
      .option('--sass', '启用sass')
      .option('--less', '启用less')
      .action(option => {
          var config = _.assign({
              moduleName: null,
              description: '',
              sass: false,
              less: false
          }, option)
          var promps = []
          if(config.moduleName !== 'string') {
                promps.push({
                  type: 'input',
                  name: 'moduleName',
                  message: '请输入模块名称',
                  validate: function (input){
                      if(!input) {
                          return '不能为空'
                      }
                      return true
                  }
                })
          } 
          if(config.description !== 'string') {
              promps.push({
                  type: 'input',
                  name: 'moduleDescription',
                  message: '请输入模块描述'
              })
          }
          if(config.sass === false && config.less === false) {
            promps.push({
              type: 'list',
              name: 'cssPretreatment',
              message: '想用什么css预处理器呢',
              choices: [
                {
                  name: 'Sass/Compass',
                  value: 'sass'
                },
                {
                  name: 'Less',
                  value: 'less'
                }
              ]
            })
          }
          inquirer.prompt(promps).then(function (answers) {
              console.log(answers)
          })
      })
      .on('--help', function() {
          console.log('  Examples:')
          console.log('')
          console.log('$ app module moduleName')
          console.log('$ app m moduleName')
      }) 
  program.parse(process.argv)
  ```

  ![1549013398423](C:\Users\winter\AppData\Roaming\Typora\typora-user-images\1549013398423.png)

  

#### 美化插件chalk

轻量级、高性能、学习成本低

```javascript
#! /usr/bin/env node 
const program = require('commander')
const inquirer = require('inquirer')
const _ = require('lodash')
const chalk = require('chalk')
program
    .command('module')
    .alias('m')
    .description('创建新的模块')
    .option('--name [moduleName]')
    .option('--sass', '启用sass')
    .option('--less', '启用less')
    .action(option => {
        var config = _.assign({
            moduleName: null,
            description: '',
            sass: false,
            less: false
        }, option)
        var promps = []
        
        console.log('')
        console.log(chalk.red('开启前端工程化之路'))     
        console.log('')
        
        if(config.moduleName !== 'string') {
              promps.push({
                type: 'input',
                name: 'moduleName',
                message: '请输入模块名称',
                validate: function (input){
                    if(!input) {
                        return '不能为空'
                    }
                    return true
                }
              })
        } 
        if(config.description !== 'string') {
            promps.push({
                type: 'input',
                name: 'moduleDescription',
                message: '请输入模块描述'
            })
        }
        if(config.sass === false && config.less ===false) {
          promps.push({
            type: 'list',
            name: 'cssPretreatment',
            message: '想用什么css预处理器呢',
            choices: [
              {
                name: 'Sass/Compass',
                value: 'sass'
              },
              {
                name: 'Less',
                value: 'less'
              }
            ]
          })
        }
        inquirer.prompt(promps).then(function (answers) {
            console.log(chalk.green('收工咯'))
            console.log(chalk.blue('收工咯'))
            console.log(chalk.blue.bgRed('收工咯')) //支持设置背景
            console.log(chalk.blue(answers))
        })
    }) 
    .on('--help', function() {
        console.log('  Examples:')
        console.log('')
        console.log('$ app module moduleName')
        console.log('$ app m moduleName')
    })
program.parse(process.argv)
```

![1549013755920](C:\Users\winter\AppData\Roaming\Typora\typora-user-images\1549013755920.png)

#### 下载模板

> download-git-repo 支持从 Github、Gitlab 和 Bitbucket 下载仓库，各自的具体用法可以参考官方文档。

```javascript
#!/usr/bin/env node
const program = require('commander');
const download = require('download-git-repo');
program.version('1.0.0', '-v, --version')
       .command('init <name>')
       .action((name) => {
           download('https://github.com:winterdogdog/vue-template#master', name, {clone: true}, (err) => {
				console.log(err ? 'Error' : 'Success')
		   })
       });
program.parse(process.argv);
```

> `download()` 第一个参数就是仓库地址，但是有一点点不一样。实际的仓库地址是 <https://github.com/winterdogdog/vue-template#master> ，可以看到端口号后面的 ‘/‘ 在参数中要写成 ‘:’，#master 代表的就是分支名，不同的模板可以放在不同的分支中，更改分支便可以实现下载不同的模板文件了。第二个参数是路径，上面我们直接在当前路径下创建一个 name 的文件夹存放模板，也可以使用二级目录比如 `test/${name}`

#### 完整下载代码

``` javascript
#!/usr/bin/env node
const fs = require('fs')
const program = require('commander')
const download = require('download-git-repo')
const handlebars = require('handlebars')
const inquirer = require('inquirer')
const ora = require('ora')
const chalk = require('chalk')
const symbols = require('log-symbols')
program
  .version('1.0.0', '-v, --version')
  .command('init <name>')
  .action(name => {
    if (!fs.existsSync(name)) {
      inquirer
        .prompt([
          {
            name: 'description',
            message: '请输入项目描述'
          },
          {
            name: 'author',
            message: '请输入作者名称'
          }
        ])
        .then(answers => {
          const spinner = ora('正在下载模板...')
          spinner.start()
          download(
            'https://github.com:winterdogdog/vue-template#master',
            name,
            { clone: true },
            err => {
              if (err) {
                spinner.fail()
                console.log(symbols.error, chalk.red(err))
              } else {
                spinner.succeed()
                const fileName = `${name}/package.json`
                const meta = {
                  name,
                  description: answers.description,
                  author: answers.author
                }
                if (fs.existsSync(fileName)) {
                  const content = fs.readFileSync(fileName).toString()
                  const result = handlebars.compile(content)(meta)
                  fs.writeFileSync(fileName, result)
                }
                console.log(symbols.success, chalk.green('项目初始化完成'))
              }
            }
          )
        })
    } else {
      // 错误提示项目已存在，避免覆盖原有项目
      console.log(symbols.error, chalk.red('项目已存在'))
    }
  })
program.parse(process.argv)

```

![1549014389913](C:\Users\winter\AppData\Roaming\Typora\typora-user-images\1549014389913.png)



![1549014407830](C:\Users\winter\AppData\Roaming\Typora\typora-user-images\1549014407830.png)

