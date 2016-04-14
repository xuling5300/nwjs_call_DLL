<i>如果你有这个应用的需求，需要用js调用dll库，可以考虑使用nodeJS或者直接使用nwjs平台来获取DLL库函数进行调用。在开始之前，希望你做好心里准，配置系统环境将是一个漫长的过程 ... ...</i>

<h3>系统环境</h3><br>
<b>1.Win7 sp1</b><br>
其他操作系统我没有测试过，应该也有相应的方法。但是Win7系统一定要是sp1的，如果这个补丁没有更新是装不上Visual Studio 2013的（想想都是泪，装了多少遍vs，为此又折腾了多少遍系统）<br>
<b>2.Visual Studio 2013</b><br>
这是最大的泪点，没想到就调用下dll而已，居然还牵扯到VS，牵扯到也就算了，还这样百般折磨人。。。算了表抱怨。还算庆幸是爬出来了，不知道还有多少程序猿兄弟们躺倒的这个大坑里出不来呢。<br>
<b>3.nodeJS 0.12.3</b><br>
下载地址：<a href="https://nodejs.org/download/release/v0.12.3/node-v0.12.3-x86.msi">https://nodejs.org/download/release/v0.12.3/node-v0.12.3-x86.msi</a><br>
文件是msi格式，直接安装即可，环境变量自动配好不用管。（其他版本没试过，心好累）<br>
<b>4.Python 2.7.6</b><br>
下载地址<a href="https://www.python.org/ftp/python/2.7.6/python-2.7.6.msi">https://www.python.org/ftp/python/2.7.6/python-2.7.6.msi</a><br>
装完了以后要手动添加环境变量，不会的自己搜一下...<br>
<b>5.nwjs 0.12.3</b><br>
下载地址：<a href="http://dl.nwjs.io/v0.12.3/nwjs-v0.12.3-win-ia32.zip">http://dl.nwjs.io/v0.12.3/nwjs-v0.12.3-win-ia32.zip</a><br>
（其他版本没试过，心好累）不想用这个版本可以自己下了再试试<br>


<h3>开始动手</h3><br>
1.打开DOS界面，安装nw-gyp执行下面的命令：<br>
npm install nw-gyp -g<br>
2.设置vs版本:<br>
npm config set msvs_version 2013 --global  (2013是visual studio的版本，你也可以尝试其他版本，我是放弃了)<br>


<h3>Hello world</h3><br>
1.新建一个项目文件夹，创建index.html和package.json文件：
（测试是用的windows系统里的消息窗口dll文件）

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
{<br>
  "name": "test",<br>
  "version": "1.0.1",<br>
  "main": "index.html",<br>
  "webkit":{<br>
	"plugin" : true<br>
  }<br>
}<br>

注意package.json文件里需要添加webkit属性来说明你需要调用第三方包。<br><br>
2.DOS界面下进入项目文件夹，安装ffi和ref：<br>
npm install ffi<br>
npm install ref<br><br>
3.编译ffi和ref模块，因为ffi中包含ref所以先编译ref再编译ffi，依次的命令如下：<br>
进入'项目目录\node_modules\ffi\node_modules\ref\'，执行nw-gyp rebuild --target=0.12.3<br> (0.12.3是你的nwjs版本，如果不是这个版本改为你自己的即可)<br>
进入'项目目录\node_modules\ffi\'，执行nw-gyp rebuild --target=0.12.3<br><br>
4.最后在你的项目文件夹下将所有文件打包为zip格式，将zip文件拖进node-webkit目录下的nw.exe文件上执行即可。<br><br>
5.看到界面上弹出“I am Node.JS!”说明成功了！<br><br>

参考：<a href="https://cnodejs.org/topic/5413945d8895083262b77401">https://cnodejs.org/topic/5413945d8895083262b77401</a>


提示：<br>
1.由于ffi模块是为C语言的dll包服务的，所以在编写的dll的C++源代码时必须要有 extern “C” 来修饰。例如在你的c++代码里需要这样声明函数才能有效（在.h文件中声明时使用）：<br>
extern "C" DLLSERVER_API int sumInDll(int n1, int n2);<br><br>
2.ffi在使用时需要匹配对应的环境，如果是32位dll文件，那么在安装nodeJS，python和nwjs都需要用对应的32位版本；如果是64位dll文件，则对应的软件都需要是64位的；<br><br>
3.ffi在用的时候还是挺方便的，只要环境匹配对就可以。唯一的麻烦是它需要对源dll代码声明时进行修改，这个就不如addon一劳永逸了。所以如果引用的dll文件比较多，或者是引入第三方dll文件，ffi的方法就不可取。
