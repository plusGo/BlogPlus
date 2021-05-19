# 【原创】前端性能优化之按需加载JS文件

#### 关键词：Angular 性能优化 加载JS文件

## 1.背景
近期发现了项目组内一个Angular项目存在性能问题，加载缓慢，运行稍显卡顿。作为极限性能的追求者，必然要找到问题，优化性能。
存在的问题较多，其中一个典型的问题就是在index.html直接外部链接了太多的JS脚本。你可以想象源码是这样的。

```HTML
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Demo</title>
</head>
<body>
  <app-root></app-root>
  <script src="1.js" defer></script>
  <script src="2.js" defer></script>
  <script src="3.js" defer></script>
  <script src="4.js" defer></script>
  <script src="5.js" defer></script>
  <script src="6.js" defer></script>
  <script src="7.js" defer></script>
  <script src="8.js" defer></script>
  <script src="9.js" defer></script>
  <script src="10.js" defer></script>
</body>
</html>
```
PS：有些小伙伴分不清defer和async的区别，可以点击此篇博客学习：[defer和async的区别](https://www.jianshu.com/p/c7c331ea4fe8)

- 从用户的场景出发，很多JS脚本加载了，但是不会实际用到。这等同于浪费了页面加载性能和页面运行内存
- 加载的脚本太多，尤其是都设置了defer，一旦中间的一个脚本加载阻塞，会影响后面所有的脚本执行
- 未使用Angular的scripts能力加载脚本，可能导致JS未压缩
- ... ... 

## 2.优化
### 2.1 使用webpack的import函数
```TypeScript
this.zone.run(() => { 
     if (this.platform.IOS) {     // 用户设备为苹果手机时，加载fastclick.js 
        import('../assets/js/fastclick.js').then(FastClick => {  
            FastClick.attach(document.body);    })  
        }
      if (['local'].indexOf(environment.env) !== -1) { // 环境匹配时，显示vconsole 
         import('../assets/js/vconsole.min.js').then(VConsole => {   
           new VConsole.default();  
         })  
    }})
```
### 2.2 使用script标签
**封装ScriptUtil**
```TypeScript
import {Observable, of, Subject} from 'rxjs';

export class ScriptUtil {
  private static CACHED_SCRIPTS = [];

  static asyncLoad(src: string): Observable<void> {
    if (ScriptLoadUtil.hasBeenLoaded(src)) {
      return of(null);
    }
    const result$ = new Subject<void>();
    const scriptEl = document.createElement('script');
    scriptEl.src = src;
    document.body.appendChild(scriptEl);
    scriptEl.onload = () => {
      ScriptLoadUtil.CACHED_SCRIPTS.push(src);
      result$.next();
    };
    return result$.asObservable();
  }

  static hasBeenLoaded(src: string): boolean {
    return ScriptLoadUtil.CACHED_SCRIPTS.indexOf(src) !== -1;
  }
}```

**加载JS脚本**
```TypeScript
ScriptLoadUtil.load('assets/ckeditor/ckeditor.js')
    .subscribe(() => {  console.log('load js sucess')});
```

### 2.3 使用angular.json的scripts
**某一些JS脚本还是必须立即加载的，不建议直接写在index.html的方式，可以通过在angular.json配置异步加载脚本**
```json
{
    "projects":{
        "demo":{
            "architect":{
                "build":{
                    "scripts":[
                        {  "input": "src/assets/js/browserCheck/browser-version.min.js",  "bundleName": "browser"}
                    ]
                }
            }
        }
    }
}
```
## 总结
本文主要介绍外链JS脚本加载与使用的一些优化实践，不局限于Angular，适用于各类项目。
如果您有更好的一些实践，也希望一起讨论学习。