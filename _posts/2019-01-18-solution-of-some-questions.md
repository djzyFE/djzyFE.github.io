---
layout: post 
title: 大家健康一些线上问题及其解决方案
author: 徐伟康
tags: 大家健康
---

## 背景

在大家健康项目用vue重构后，终于在一周前成功上线。但是一番折腾后，上线后正式环境却出现了一些bug，然后在改bug的过程中又导致了一些新bug，道路曲折且离奇，为了避免以后重复踩坑，特整理了一下遇到的问题以及解决方案，并持续更新中。



##  问题列表

>  持续更新中...

- ### Cannot read property 'call' of undefined

    * 该bug出现的原因是，删除package-lock.json文件重新打包上线，导致webpack版本自动升级到最新版本4.28.x，该版本下的webpack在打包构建时无任何报错信息，但是在运行时会部分安卓机型会报“Cannot read property 'call' of undefined”的错误，导致页面空白。

    * 本地复现之后的定位结果是webpack运行时缺少了某个模块，重新添加package-lock.json，并将webpack的版本降级到4.17.1，重新部署上线后错误得到解决。

    * 但是之后在还原到之前出错对应的webpack版本下，并revirson到之前的代码，也并没有复现出来该错误。。。

    * 修正：之前定位到的出现这个错误的原因是wepack版本的原因导致的，其实不然。我们在之后的一次上线中，又一次的导致了线上的“Cannot read property 'call' of undefined”，于是我们猜测之前的判断是错误的。经过一段时间的重新定位过后，发现原因是我们的cdn惹的祸。

    * 本地复现规则： 使用上一个版本的entry.js，然后其余的内部逻辑js使用当前版本的，本地运行， “Cannot read property 'call' of undefined”，并且页面无法正常打开。

    * 我们发现webpack打包之后的入口文件entry.js比较大，如果我们修改了entry.js的相关代码导致hash改变，比如从 entry.js?hash1 变为了 entry.js?hash2，正常情况下，cdn会比对比两个相同的文件（因为cdn那边不认识我们？后面的hash部分，所以会认为是请求同一个文件），然后进行一个同步过程。由于我们entry.js文件相对较大，而且网络环境也可能较差，这就会导致一部分地区同步过程较长或者直接卡顿，这样的话可能就会导致加载老的cdn资源，就导致了上述的复现步骤。

    * 我们通过修改webpack打包js的hash命名规则，来强制每次重新打包后，cdn每次都去加载新的js资源，解决了以上问题。
        <pre><code>

        //new
      output: {
        path: path.resolve(__dirname, './dist' + _env.outputPath),
        filename: 'wx/[name]_[chunkhash].js',
        chunkFilename: 'wx/js/[name]_[chunkhash].js',
      },
        //old
      output: {
        path: path.resolve(__dirname, './dist' + _env.outputPath),
        filename: 'wx/[name].js?[chunkhash]',
        chunkFilename: 'wx/js/[name].js?[chunkhash]',
    },
    </code></pre>

- ### 填写问诊单无法点击下一步 && 聊天页没有消息记录

    * 填写问诊单无法点击下一步，起初认为是微信内核原因（因为之前有一例同样的无法点击问题，通过让用户重新卸载安装微信解决），设置了断点上报，也并没有发现问题。然后该用户又反应，医生列表页点击咨询无反应。

    * 继续排查日志发现该出问题的用户的机器为ios7.x的苹果手机，然后就怀疑是不是系统太低的原因。

    * 找到一台ios8的测试机，发现页面报错，“unhandled promise rejection”，并且后来也成功复现该用户的行为，问诊单下一步和医生列表页的咨询按钮均无法点击，由此猜测就是该错误导致的。查了一下资料，发现这个错误可能会出现在低版本的安卓和ios机器中，浏览器不兼容Promise导致的错误。

    * 解决办法：下载babel-polyfill，并在入口entry配置数组的第一个元素中加上"babel-polyfill"
    <pre><code>
    let ret = {
        entry: {
            entry: ["babel-polyfill",_env.inputPath + '/entry.js'],  //添加babel-polyfill
        },
        output: {
            path: path.resolve(__dirname, './dist' + _env.outputPath),
            filename: 'wx/[name].js?[chunkhash]',
            chunkFilename: 'wx/js/[name].js?[chunkhash]',
        },
        ...
    }
    </code></pre>

    * 另一个聊天页无法显现消息记录的那个用户，同样是一个问题，咨询页报错导致了无法正常加载。上线babel-polyfill后也成功修复。

- ###  公众号页面字体被放大

    * 原因是该用户可能在本公众号或者别的公众号手动设置过字体大小，这种改动是持续性并且保持性的，不随公众号的不同而不同，也不随微信退出而恢复，除非用户再手动设置成常规大小，所以我们需要在我们的公众号中做逻辑强制字体以正常大小去显示。这里ios和安卓的设置方式有所不同。

    * ios：
        <pre><code>
        body{
            -webkit-text-size-adjust: 100% !important; 
            text-size-adjust: 100% !important; 
            -moz-text-size-adjust: 100% !important; 
        }
        </code></pre>

    * 安卓:

        <pre><code>
            function setPageSize() {
            if (typeof (WeixinJSBridge) == "undefined") { // eslint-disable-line
                document.addEventListener("WeixinJSBridgeReady", function (e) { // eslint-disable-line
                if (typeof (WeixinJSBridge) != "undefined"){ // eslint-disable-line
                    // 设置网页字体为默认大小
                    WeixinJSBridge.invoke('setFontSizeCallback', { // eslint-disable-line
                    'fontSize': 0
                    });
                    // 重写设置网页字体大小的事件
                    WeixinJSBridge.on('menu:setfont', function(){  // eslint-disable-line
                    WeixinJSBridge.invoke('setFontSizeCallback', {  // eslint-disable-line
                        'fontSize': 0
                    })
                    })
                }
                });
            } else {
                // 设置网页字体为默认大小
                WeixinJSBridge.invoke('setFontSizeCallback', {  // eslint-disable-line
                'fontSize': 0
                });
                // 重写设置网页字体大小的事件
                WeixinJSBridge.on('menu:setfont', function(){   // eslint-disable-line
                WeixinJSBridge.invoke('setFontSizeCallback', {  // eslint-disable-line
                    'fontSize': 0
                })
                })
            }
            }
        </code></pre>
- ### 微信支付始终无法唤起密码框

    * 从老早以前，线上零零散散就有一些微信无法支付的问题，具体表现就是点击支付无任何反应，起初认为是微信jssdk的不稳定问题（因为有的用户重新卸载微信后就可以正常使用），到后来又认为是我们的授权目录配置问题（也只是猜测），直到我们复现出来。

    * 复现的过程中我们发现，不仅仅是微信支付用不了，所有和我们项目中微信jssdk相关的，都是无法正常使用的，也不会报错。但是都有一个同样的表现，就是weixin.ready()回调都没有执行（fail，success，cancel都没有进入），看样子就是微信jssdk的config都没有执行；

    * 刚开始，我们的入口html文件中是这样的，引入了两个远程js库

        <pre><code>
            < body>
                < script>
                    !(function(c,b,d,a){c[a]||(c[a]={});c[a].config={pid:pid,imgUrl:imgUrl,enableSPA:true};
                        with(b)with(body)with(insertBefore(createElement("script"),firstChild))setAttribute("crossorigin","",src=d)
                    })(window,document,"https://retcode.alicdn.com/retcode/bl.js","__bl");
                < /script>
                < script src="https://jic.talkingdata.com/app/h5/v1?appid=appid&vn=djjkversion&vc=version"
                async>< /script>
             < /body>
        </code></pre>

       在让用户帮忙调试的过程中，我们发现，在注释掉这两个库之后jssdk正常，加上就不行。猜想是用户在加载这两个外部js的时候，由于网络或者各种原因导致加载过程中比较缓慢甚至卡住，导致的jssdk无法正常运行；

     * 于是我们本地也是添加了一个加载脚本，用一个google请求来模拟加载缓慢的情况

        <pre><code>
            < body>
               < script src="http://maps.google.com/maps/api/js?sensor=false&libraries=geometry&v=3.7" async></script>
             < /body>
        </code></pre>

        发现成功模拟出了用户的那种情况，在google.js下载的过程中，weixin.ready()始终没有运行，直到google.js下载连接超时（大概等了几分钟，请求状态从pending变成了加载失败），这时才弹出weixn.ready成功的提示。


     *  得出结论：我们在微信浏览器中，html里面下载js的任务（即使加上了async），和微信jssdk的config过程，是放在了同一个任务队列中，而且js的下载在队列中是排在config前面的，所以才会出现在下载google.js超时以后
      才收到weixin.ready()成功的通知


     * 这样解决问题就变得很简单了，尽量避免直接去加载远程的cdn js文件，如果不能避免，可以在微信jssdk ready后，再去动态的加载所需要的远程js文件

