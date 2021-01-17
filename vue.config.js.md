# vue.config.js
> vue.config.js는 @vue/cli-service에서 자동으로 로딩하는 파일로 vue cli 환경설정과 webpack 설정 등을 변경할 수 있다.<br>
> 일반적으로 webpack 사용시 webpack.config.js를 수정해야하지만 vue cli를 사용할 경우 vue.config.js를 통해 webpack까지 설정가능<br>
> vue-cli3로 프로젝트를 생성하면 vue.config.js가 없다. 프로젝트 루트에 추가해야한다.
- [공식 DOC](https://cli.vuejs.org/config/#global-cli-config)
<br><br>
## [outputDir](https://cli.vuejs.org/config/#outputdir)
## [assetsDir](https://cli.vuejs.org/config/#assetsdir)
## [runtimeCompiler](https://cli.vuejs.org/config/#runtimecompiler)
- [reference](https://greenmon.dev/2019/02/25/vuejs-render.html)
<br><br> 
## [devServer](https://cli.vuejs.org/config/#devserver)
- [userLocalIp](https://webpack.js.org/configuration/dev-server/#devserveruselocalip)
- [port](https://webpack.js.org/configuration/dev-server/#devserverport)
- [disableHostCheck](https://webpack.js.org/configuration/dev-server/#devserverdisablehostcheck)
    - [reference](https://pilot376.tistory.com/55)
    - false(default) : dev server로 실행시 외부에서 해당 dev server IP로 접근이 불가
- [proxy](https://cli.vuejs.org/config/#devserver-proxy)
    - [target](https://github.com/chimurai/http-proxy-middleware#http-proxy-options)
    - [changeOrigin](https://github.com/chimurai/http-proxy-middleware#http-proxy-options)
    - [xfwd](https://github.com/chimurai/http-proxy-middleware#http-proxy-options)
<br><br>
## [css.extract](https://cli.vuejs.org/config/#css-extract)
## [chainWebpack](https://cli.vuejs.org/config/#chainwebpack)
- [reference](https://yunzema.tistory.com/274)
<br><br>
## [pluginOptions](https://cli.vuejs.org/config/#pluginoptions)
## [configureWebpack](https://cli.vuejs.org/config/#configurewebpack)
        
        