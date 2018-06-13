

<!-- toc -->

- [快速起步](#%E5%BF%AB%E9%80%9F%E8%B5%B7%E6%AD%A5)
  * [安装nodejs](#%E5%AE%89%E8%A3%85nodejs)
  * [安装typescript](#%E5%AE%89%E8%A3%85typescript)
  * [准备开发环境](#%E5%87%86%E5%A4%87%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83)
  * [下载运行 ppspider_example](#%E4%B8%8B%E8%BD%BD%E8%BF%90%E8%A1%8C-ppspider_example)
    + [在IDEA中clone ppspider_example](#%E5%9C%A8idea%E4%B8%ADclone-ppspider_example)
    + [安装项目的npm依赖](#%E5%AE%89%E8%A3%85%E9%A1%B9%E7%9B%AE%E7%9A%84npm%E4%BE%9D%E8%B5%96)
    + [运行 tsc](#%E8%BF%90%E8%A1%8C-tsc)
    + [启动爬虫](#%E5%90%AF%E5%8A%A8%E7%88%AC%E8%99%AB)
- [系统介绍](#%E7%B3%BB%E7%BB%9F%E4%BB%8B%E7%BB%8D)
  * [装饰器](#%E8%A3%85%E9%A5%B0%E5%99%A8)
    + [@Launcher](#launcher)
    + [@OnStart](#onstart)
    + [@OnTime](#ontime)
    + [@AddToQueue @FromQueue](#addtoqueue-fromqueue)
  * [工具类 PuppeteerUtil](#%E5%B7%A5%E5%85%B7%E7%B1%BB-puppeteerutil)
    + [PuppeteerUtil.defaultViewPort](#puppeteerutildefaultviewport)
    + [PuppeteerUtil.addJquery](#puppeteerutiladdjquery)
    + [PuppeteerUtil.jsonp](#puppeteerutiljsonp)
    + [PuppeteerUtil.setImgLoad](#puppeteerutilsetimgload)
    + [PuppeteerUtil.onResponse](#puppeteerutilonresponse)
    + [PuppeteerUtil.onceResponse](#puppeteerutilonceresponse)
    + [PuppeteerUtil.downloadImg](#puppeteerutildownloadimg)
    + [PuppeteerUtil.links](#puppeteerutillinks)
    + [PuppeteerUtil.count](#puppeteerutilcount)
    + [PuppeteerUtil 例子](#puppeteerutil-%E4%BE%8B%E5%AD%90)
- [调试](#%E8%B0%83%E8%AF%95)
- [控制界面](#%E6%8E%A7%E5%88%B6%E7%95%8C%E9%9D%A2)

<!-- tocstop -->

# 快速起步
## 安装nodejs
[nodejs官网下载地址](http://nodejs.cn/download/)

## 安装typescript
```
npm install -g typescript
```

## 准备开发环境
推荐使用 IDEA + nodejs 插件  
IDEA一定要下载 Ultimate 版本，否则有很多功能无法使用  
![Ultimate 版本截图](https://s1.ax1x.com/2018/06/13/CO6qRf.png)  
下载nodejs插件的时候，需根据IDEA的版本下载可用的 nodejs 版本，IDEA的版本可通过菜单栏点击 Help -> About 看到  
例如我正在使用的IDEA和nodejs插件版本  
![IDEA 版本](https://s1.ax1x.com/2018/06/13/COcdYt.png)  
![nodejs 插件版本](https://s1.ax1x.com/2018/06/13/COcBSf.png)

[IDEA 下载地址](https://www.jetbrains.com/idea/download/)  
[nodejs 插件下载地址](http://plugins.jetbrains.com/plugin/6098-nodejs)

## 下载运行 ppspider_example
ppspider_example github 地址  
https://github.com/xiyuan-fengyu/ppspider_example  
### 在IDEA中clone ppspider_example 
需要先安装git，并在IDEA中配置git的可执行文件的路径  
![IDEA git 配置](https://s1.ax1x.com/2018/06/13/COcr6S.png)

![IDEA clone from git](https://s1.ax1x.com/2018/06/13/COc6mQ.png)  
![IDEA clone from git](https://s1.ax1x.com/2018/06/13/COccwj.png)

### 安装项目的npm依赖
鼠标右键 package.json ，点击 Run 'npm install'  
或者在terminal中cd到项目所在目录，运行 'npm install'  
所有的npm依赖都会存放到当前项目下的 node_modules 文件夹中  

### 运行 tsc
tsc 是 typescript 的编译工具，将 typescript 代码编译为 js，之后便可右键 js 文件运行了  
tsc 在运行期间会监听 ts 文件变化，自动编译有变动的 ts 文件  

运行方式：  
1.（推荐）右键 package.json，点击 Show npm Scripts，双击 auto build  
2.在 IDEA 中打开一个 terminal， 运行 tsc  

### 启动爬虫
右键运行 lib/quickstart/App.js  
用浏览器打开 http://localhost:9000 可以实时查看爬虫系统的运行情况  

# 系统介绍
## 装饰器
申明形式  
```
export function TheDecoratorName(args) { ... }
```
使用方式
```
@TheDecoratorName(args)
```
乍一看和java中的注解一样，但实际上这个更为强大，不仅能提供元数据，还能对类或方法的属性行为做修改装饰，实现切面的效果，ppspider中很多功能都是通过装饰器来提供的  
接下来介绍一下实际开发中会使用到的装饰器  

### @Launcher
```
export function Launcher(theAppInfo: AppInfo) { ... }
```
用于申明整个爬虫系统的启动入口  
其参数类型为  
```
export type AppInfo = {
    // workplace属性描述了整个爬虫系统的工作目录，系统停止时保存工作状态的cache文件、系统工作中保存任务信息的数据库文件都会存储到整个目录下；用户抓取的数据文件也可以保存到整个目录下    
    workplace: string;
    
    // tasks属性用于注入所有的任务的类  
    tasks: any[];
    
    // workerFactory属性用于注入所有worker的工厂类实例，目前仅提供了一种工厂类 PuppeteerWorkerFactory   
    workerFactorys: WorkerFactory<any>[];
    
    // webUiPort属性用于申明系统管理界面的服务器端口，默认9000  
    webUiPort?: number | 9000;
}
```
可以通过全局变量 appInfo 访问到这些信息，单启动后不可更改  
```
import {Launcher, PuppeteerWorkerFactory} from "ppspider";
import {TestTask} from "./tasks/TestTask";

@Launcher({
    workplace: __dirname + "/workplace",
    tasks: [
        TestTask
    ],
    workerFactorys: [
        new PuppeteerWorkerFactory({
            headless: false
        })
    ]
})
class App {

}
```

### @OnStart
```
export function OnStart(config: OnStartConfig) { ... }
```
用于声明一个在爬虫系统启动时执行一次的子任务；后续可以在管理界面上点击该任务名后面的重新执行的按钮即可让该任务重新执行一次  
参数说明  
```
export type OnStartConfig = {
    // 页面地址
    urls: string | string[];  
    
    // worker工厂类型，其类型的实例必须在 @Launcher 参数的workerFactorys属性中申明，目前可能是 PuppeteerWorkerFactory
    workerFactory: WorkerFactoryClass;
    
    // 任务的最大并行数配置，可以是具体的数字，也可以用cron表达式动态设置最大的并行数
    parallel?: ParallelConfig;
    
    // 该子任务的最小执行间隔，多个并行任务共享同一个间隔  
    exeInterval?: number;
    
    // 该子任务的具体描述
    description?: string;
}
```
使用例子 [@OnStart example](https://github.com/xiyuan-fengyu/ppspider_example/tree/master/src/quickstart)
```
import {Job, OnStart, PuppeteerUtil, PuppeteerWorkerFactory} from "ppspider";
import {Page} from "puppeteer";

export class TestTask {

    @OnStart({
        urls: "http://www.baidu.com",
        workerFactory: PuppeteerWorkerFactory
    })
    async index(page: Page, job: Job) {
        await page.goto(job.url());
        const urls = await PuppeteerUtil.links(page, {
            "all": "http.*"
        });
        console.log(urls);
    }

}
```

### @OnTime
```
export function OnTime(config: OnTimeConfig) { ... }
```
用于声明一个在特定时间周期性执行的任务，通过cron表达式设置执行时间    
参数说明  
```
export type OnTimeConfig = {
    // 页面地址
    urls: string | string[];  
    
    // 执行时间的cron表达式
    cron: string;
    
    // worker工厂类型，其类型的实例必须在 @Launcher 参数的workerFactorys属性中申明，目前可能是 PuppeteerWorkerFactory
    workerFactory: WorkerFactoryClass;
    
    // 任务的最大并行数配置，可以是具体的数字，也可以用cron表达式动态设置最大的并行数
    parallel?: ParallelConfig;
    
    // 该子任务的最小执行间隔，多个并行任务共享同一个间隔  
    exeInterval?: number;
    
    // 该子任务的具体描述
    description?: string;
}
```
使用例子 [@OnTime example](https://github.com/xiyuan-fengyu/ppspider_example/tree/master/src/ontime)
```
import {Job, PuppeteerWorkerFactory, OnTime, DateUtil} from "ppspider";
import {Page} from "puppeteer";

export class TestTask {

    @OnTime({
        urls: "http://www.baidu.com",
        cron: "*/5 * * * * *",
        workerFactory: PuppeteerWorkerFactory
    })
    async index(page: Page, job: Job) {
        console.log(DateUtil.toStr());
    }

}
```

### @AddToQueue @FromQueue
这两个装饰器必须一起使用，@AddToQueue 将返回结果转换为 Job 添加到队列中，@FromQueue 从队列中获取 Job 并执行  

@AddToQueue 的申明
```
export function AddToQueue(queueConfigs: AddToQueueConfig | AddToQueueConfig[]) { ... }
```
一个 @AddToQueue 可以配置一个或多个队列  
队列配置的类型如下：
```
export type AddToQueueConfig = {
    // 队列名
    name: string;
    
    // 队列类型， 目前提供了 DefaultQueue（先进先出），DefaultPriorityQueue（优先级队列）
    queueType?: QueueClass;
    
    // 过滤器类型，目前提供了 NoFilter（不进行过滤），BloonFilter（布隆过滤器）
    filterType?: FilterClass;
}
```
可以在多个地方用 @AddToQueue 往同一个队列中添加 Job ，队列类型由第一次申明处的 queueType 决定，但每一处的 filterType 可以不一样  
@AddToQueue 装饰的方法的返回结果必须是 AddToQueueData 类型的
```
export type CanCastToJob = string | string[] | Job | Job[];

export type AddToQueueData = Promise<CanCastToJob | {
    [queueName: string]: CanCastToJob
}>
```
当 @AddToQueue 配置了多个队列信息时，返回类型必须是 
```
Promise<{
    [queueName: string]: CanCastToJob
}>
```
格式的数据；可以使用 PuppeteerUtil.links 方法方便的获取想要的连接，这个方法的返回结果正好是 AddToQueueData 格式的，具体的用法后面会介绍


@FromQueue 的申明
```
export function FromQueue(config: FromQueueConfig) { ... }
```
参数说明
```
export type FromQueueConfig = {
    // 队列名
    name: string;
    
    
    // worker工厂类型，其类型的实例必须在 @Launcher 参数的workerFactorys属性中申明，目前可能是 PuppeteerWorkerFactory
    workerFactory: WorkerFactoryClass;
    
    // 任务的最大并行数配置，可以是具体的数字，也可以用cron表达式动态设置最大的并行数
    parallel?: ParallelConfig;
    
    // 该子任务的最小执行间隔，多个并行任务共享同一个间隔  
    exeInterval?: number;
    
    // 该子任务的具体描述
    description?: string;
}
```
使用例子 [@AddToQueue @FromQueue example](https://github.com/xiyuan-fengyu/ppspider_example/tree/master/src/queue)
```
import {AddToQueue, AddToQueueData, FromQueue, Job, OnStart, PuppeteerUtil, PuppeteerWorkerFactory} from "ppspider";
import {Page} from "puppeteer";

export class TestTask {

    @OnStart({
        urls: "http://www.baidu.com",
        workerFactory: PuppeteerWorkerFactory,
        parallel: {
            "0/20 * * * * ?": 1,
            "10/20 * * * * ?": 2
        }
    })
    @AddToQueue({
        name: "test"
    })
    async index(page: Page, job: Job): AddToQueueData {
        await page.goto(job.url());
        return PuppeteerUtil.links(page, {
            "test": "http.*"
        });
    }

    @FromQueue({
        name: "test",
        workerFactory: PuppeteerWorkerFactory,
        parallel: 1,
        exeInterval: 1000
    })
    async printUrl(page: Page, job: Job) {
        console.log(job.url());
    }

}
```

## 工具类 PuppeteerUtil
### PuppeteerUtil.defaultViewPort
将 page 的窗口分辨率设置为 1920 * 1080  

### PuppeteerUtil.addJquery
向 page 中注入 jquery，页面刷新或跳转后失效，所以这个方法必须在 page 加载完成之后调用  

### PuppeteerUtil.jsonp
用于解析jsonp数据中的json

### PuppeteerUtil.setImgLoad
禁止或启用图片加载

### PuppeteerUtil.onResponse
用于监听接口的返回结果，可以设置监听次数

### PuppeteerUtil.onceResponse
用于监听接口的返回结果，仅监听一次

### PuppeteerUtil.downloadImg
下载图片

### PuppeteerUtil.links
获取连接

### PuppeteerUtil.count
获取满足 selector 的元素个数

### PuppeteerUtil 例子
[PuppeteerUtil example](https://github.com/xiyuan-fengyu/ppspider_example/tree/master/src/puppeteerUtil)
```
import {appInfo, Job, OnStart, PuppeteerUtil, PuppeteerWorkerFactory} from "ppspider";
import {Page} from "puppeteer";

export class TestTask {

    @OnStart({
        urls: "http://www.baidu.com",
        workerFactory: PuppeteerWorkerFactory
    })
    async index(page: Page, job: Job) {
        await PuppeteerUtil.defaultViewPort(page);

        await PuppeteerUtil.setImgLoad(page, false);

        const hisResWait = PuppeteerUtil.onceResponse(page, "https://www.baidu.com/his\\?.*", async response => {
            const resStr = await response.text();
            console.log(resStr);
            const resJson = PuppeteerUtil.jsonp(resStr);
            console.log(resJson);
        });

        await page.goto("http://www.baidu.com");
        await PuppeteerUtil.addJquery(page);
        console.log(await hisResWait);

        const downloadImgRes = await PuppeteerUtil.downloadImg(page, ".index-logo-src", appInfo.workplace + "/download/img");
        console.log(downloadImgRes);

        const href = await PuppeteerUtil.links(page, {
            "index": ["#result_logo", ".*"],
            "baidu": "^https?://[^/]*\\.baidu\\.",
            "other": (a: Element) => {
                const href = (a as any).href as string;
                if (href.startsWith("http")) return href;
            }
        });
        console.log(href);

        const count = await PuppeteerUtil.count(page, "#result_logo");
        console.log(count);
    }

}
```

# 调试
非注入代码可以直接在 IDEA 中调试  

注入到网页中运行的js代码可以通过有界面模式下打开的 Chromium 进行调试  
Chromium 界面可以在构造 PuppeteerWorkerFactory 时，参数的 headless = false开启；另外还需要设置 devtools = true ，这样可以让新打开的页面自动打开开发者工具面板，debugger 才会断点成功   
[Inject js debug example](https://github.com/xiyuan-fengyu/ppspider_example/tree/master/src/debug)  
```
import {Launcher, PuppeteerWorkerFactory} from "ppspider";
import {TestTask} from "./tasks/TestTask";

@Launcher({
    workplace: __dirname + "/workplace",
    tasks: [
        TestTask
    ],
    workerFactorys: [
        new PuppeteerWorkerFactory({
            headless: false,
            devtools: true
        })
    ]
})
class App {

}
```
然后在要调试的注入代码前加一行 debugger;  
```
import {Job, OnStart, PuppeteerWorkerFactory} from "ppspider";
import {Page} from "puppeteer";

export class TestTask {

    @OnStart({
        urls: "http://www.baidu.com",
        workerFactory: PuppeteerWorkerFactory
    })
    async index(page: Page, job: Job) {
        await page.goto(job.url());
        const title = await page.evaluate(() => {
           debugger;
           const title = document.title;
           console.log(title);
           return title;
        });
        console.log(title);
    }

}
```

# 控制界面
使用浏览器打开 http://localhost:9000  
Queue 面板可以查看和管理整个系统中子任务的运行情况  
![Queue Help](https://s1.ax1x.com/2018/06/13/COc4pV.png)  

Job 面板可以对所有子任务实例进行搜索，查看任务详情  
![Job Help](https://s1.ax1x.com/2018/06/13/COcRkn.png)