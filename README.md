<i>如果你有这个应用的需求，需要用js调用dll库，可以考虑使用nodeJS或者直接使用nwjs平台来获取DLL库函数进行调用。在开始之前，希望你做好心里准，配置系统环境将是一个漫长的过程 ... ...</i>

<h3>系统环境</h3><br>
1.<b>Win7 sp1</b><br>
其他操作系统我没有测试过，应该也有相应的方法。但是Win7系统一定要是sp1的，如果这个补丁没有更新是装不上Visual Studio 2013的（想想都是泪，装了多少遍vs，为此又折腾了多少遍系统）<br>
2.<b>Visual Studio 2013</b><br>
这是最大的泪点，没想到就调用下dll而已，居然还牵扯到VS，牵扯到也就算了，还这样百般折磨人。。。算了表抱怨。还算庆幸是爬出来了，不知道还有多少程序猿兄弟们躺倒的这个大坑里出不来呢。
3.<b>nodeJS 0.12.3</b><br>
下载地址：<a href="https://nodejs.org/download/release/v0.12.3/node-v0.12.3-x86.msi">https://nodejs.org/download/release/v0.12.3/node-v0.12.3-x86.msi</a>
文件是msi格式，直接安装即可，环境变量自动配好不用管。（其他版本没试过，心好累~~~）<br>
4.<b>Python 2.7.6</b><br>
下载地址<a href="https://www.python.org/ftp/python/2.7.6/python-2.7.6.msi">https://www.python.org/ftp/python/2.7.6/python-2.7.6.msi</a><br>
装完了以后要手动添加环境变量，不会的自己搜一下...
5.<b>nwjs 0.12.3</b><br>
下载地址：<a href="http://dl.nwjs.io/v0.12.3/nwjs-v0.12.3-win-ia32.zip">http://dl.nwjs.io/v0.12.3/nwjs-v0.12.3-win-ia32.zip</a><br>
其他版本没试过，心好累~~不想用这个版本可以自己下了再试试<br>


<h3>开始动手</h3><br>
1.打开DOS界面，安装nw-gyp执行下面的命令：<br>
npm install nw-gyp -g<br>
2.设置vs版本:<br>
npm config set msvs_version 2013 --global  (2013是visual studio的版本，你也可以尝试其他版本，我是放弃了)<br>


<h3>Hello world</h3><br>
1.新建一个项目文件夹，创建index.html和package.json文件：

index.html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <title>nwjs call DLL</title>
        <meta name="description" content="">
        <meta name="viewport" content="width=device-width, initial-scale=1">        
    </head>

    <body>
    <script type="text/javascript">
		var FFI = require('ffi');

		function TEXT(text){
		   return new Buffer(text, 'ucs2').toString('binary');
		}

		var user32 = new FFI.Library('user32', {
		   'MessageBoxW': 
		   [
			  'int32', [ 'int32', 'string', 'string', 'int32' ]
		   ]
		});

		var OK_or_Cancel = user32.MessageBoxW(
		   0, TEXT('I am Node.JS!'), TEXT('Hello, World!'), 1
		);
		console.log(OK_or_Cancel);
    </script>
    </body>

</html>

package.json
{
  "name": "test",
  "version": "1.0.1",
  "main": "index.html",
  "webkit":{
	"plugin" : true
  }
}
