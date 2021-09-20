## TypeScript的4种编译方式
--编译

知道TypeScript是什么，也知道怎么安装，那怎么使用？

其实编写TypeScript脚本时，只要有面向对象编程思维的，就可以很快速的编写出来。这里不讲怎么编写TS脚本（网上有很多TS脚本编写教程）。进入正题。

### 1、手动编译

- 1.1、首先找到TypeScript的安装目录，我的在”C:\Program Files (x86)\Microsoft SDKs\TypeScript\1.0“。
- 1.2、使用cmd工具命令cd到安装目录。
- 1.3、输入命令：tsc 文件名.ts，回车编译。

一旦编译成功，就会在相同目录下生成同名的js文件（编译成功后是没有任何消息提示的。上图中，这也是编译成功的。只要不存在语法错误）。

### 2、设置自动编译
- 2.1、找到项目文件（*.csproj的文件），编辑打开，找到`<Target Name="BeforeBuild">`节点，在里面添加如下节点信息：
```
<Exec Command="&quot;$(PROGRAMFILES)\Microsoft SDKs\TypeScript\1.0\tsc&quot;    @(TypeScriptCompile ->'&quot;%(fullpath)&quot;', ' ')" />
```
或
```
<Exec Command="tsc$(TypeScriptSourceMap) @(TypeScriptCompile ->'&quot;%(fullpath)&quot;', ' ')" />
```
这样设置完后，每次编译项目都会自动编译项目中所有*.ts文件

### 3、解析编译（个人理解），
如果不想在项目中编译，这需要在页面添加`<script src="typescript.js" />`来编译。typescript.js文件在  盘符/Program Files(x86)|Program Files/Microsoft SDKs/TypeScript/版本号/typescript.js 。
### 4、动态编译
动态编译，在写完ts代码后，按下ctrl+s，右边视图区是出现对应编译后的js脚本。
- 4.1、找到项目文件，编辑打开
- 4.2、在`<PropertyGroup>`并行节点下，添加如下节点信息：
```
<PropertyGroup Condition="'$(Configuration)' == 'Debug'">
   　　　　　　 <TypeScriptTarget>ES5</TypeScriptTarget>
    　　　　　　<TypeScriptRemoveComments>false</TypeScriptRemoveComments>
    　　　　　　<TypeScriptSourceMap>true</TypeScriptSourceMap>
    　　　　　　<TypeScriptModuleKind>AMD</TypeScriptModuleKind>
  　　　　</PropertyGroup>
  　　　　<PropertyGroup Condition="'$(Configuration)' == 'Release'">
    　　　　　　<TypeScriptTarget>ES5</TypeScriptTarget>
   　　　　　　 <TypeScriptRemoveComments>true</TypeScriptRemoveComments>
    　　　　　　<TypeScriptSourceMap>false</TypeScriptSourceMap>
   　　　　　　 <TypeScriptModuleKind>AMD</TypeScriptModuleKind>
  　　　　</PropertyGroup>
  　　　　<Import Project="$(VSToolsPath)\TypeScript\Microsoft.TypeScript.targets" Condition="Exists('$(VSToolsPath)\TypeScript\Microsoft.TypeScript.targets')" />
```
建议使用第4种编译方式，虽性能可能有些会下降，但编写完一段脚本后，按下ctrl+s，右侧js视图立马可以显示js脚本。这样有助于理解TS与JS之间的某种转换关系，也可以快速加深对TS语法的理解。

　　　　

 