# 第1章 GIS软件类型及开发方法
本章介绍GIS软件有哪些类型，并介绍桌面GIS、WebGIS以及移动GIS软件开发的基本方法。

## 1.1 GIS软件类型
GIS软件是指涉及空间数据操作的软件，常用的空间数据操作包括：空间数据的读写、空间坐标转换、空间数据可视化（地图显示）、空间查询、空间分析、地理编码（地址匹配）等。GIS软件有很多类型，可以从不同角度进行分类。
- **按软件架构的分类**  
按软件的架构可分为C/S（Client/Server）架构的GIS软件和B/S（Browser/Server）架构的GIS软件。C/S架构的GIS软件部署在客户端，可以通过网络调用远程服务器的服务，客户端可以是桌面设备，也可以是移动设备，其中，部署在桌面设备上的通常称为桌面GIS软件；B/S架构的GIS软件部署在服务器端，用户通过浏览器软件（如IE）访问Web服务器上的GIS软件，服务器上的软件对用户的请求进行响应，返回的响应通过浏览器解析并显示，在解析过程会涉及调用其它服务器上的服务，B/S架构的GIS软件也称为WebGIS软件。
- **按软件的运行设备**  
按软件运行的设备通常是把在移动设备上运行的GIS软件称为移动GIS软件，包括针对移动设备开发的WebGIS软件。
- **按软件的功能**  
GIS软件按功能可分为工具软件、系统软件以及GIS开发包。工具软件的功能比较单一，一般是针对某个特定任务，如坐标转换，工具软件可以是一个独立运行的程序，也可以是其它应用系统中的一个扩展；系统软件有较完整的GIS功能，需要包括地图显示、地图操作等基本功能，系统软件一般是提供给其他用户使用，因此，需要有用户操作界面；GIS开发包用于GIS软件开发，是为开发者服务的，GIS开发包与操作系统、编程语言等相关，同种类型的开发包，通常会有不同版本。

## 1.2桌面GIS软件开发
桌面GIS软件开发的主要形式有：
- 基于已有GIS平台的二次开发
- 不依赖其它平台的GIS工具开发
- 不依赖其它平台的GIS应用系统开发

### 1.2.1基于已有GIS平台的二次开发
基于已有GIS平台的二次开发即根据用户需求对已有GIS平台进行定制和扩展，包括系统界面的定制和系统功能的扩展。很多GIS平台都内置二次开发语言和开发包，如ArcGIS Desktop内置Python语言和ArcPy扩展包，支持利用Python语言开发工具（工具条）以及插件。  

以下一个基于ArcGIS Desktop平台的二次开发示例，二次开发的目标是在ArcGIS Desktop中增加一个批处理数据切割的Python工具，该工具能对一个文件夹中的多个要素类用指定的要素类进行批处理数据切割操作，输出数据存放在另一个文件夹中，用户可以从工具对话框中选择输入数据的工作空间、用于切割的要素类以及输出数据的工作空间，每个输出数据的名称为“clip_”加原有数据的文件名。该示例的二次开发步骤如下：
-  **工具对话框参数设计**  
工具对话框参数的设计包括：参数个数、参数顺序以及每个参数的特性（包括参数的显示名称、参数的数据类型及参数属性等）。工具运行时，会根据工具对话框参数的定义显示相应的对话框。根据要求，设计的工具对话框有三个参数，按顺序分别为：输入数据工作空间、切割数据、输出数据工作空间。
-  **编写工具脚本文件**  
工具脚本文件是点击对话框“OK”按钮后运行的脚本。与一般Python脚本文件不同的是，工具脚本文件可以获取对话框中用户输入的参数，ArcPy提供了GetParameter (index)和GetParameterAsText (index)两个函数用于访问对话框中输入的参数。以下是针对批处理数据切割编写的工具脚本文件。
```
from arcpy import *
in_workspace = GetParameterAsText(0)
clipFeature = GetParameterAsText(1)
out_workspace = GetParameterAsText(2)
env.workspace = in_workspace
fcs = ListFeatureClasses()
for fc in fcs:
   outFeatureClass = out_workspace + "/clip_" + fc
   Clip_analysis(fc, clipFeature, outFeatureClass)
```
-  **创建工具**  
创建工具是在catalog中通过交互方式产生。创建的工具可以放在已有的工具箱中或新建的工具箱中，如要新建工具箱，先选中某个文件夹，然后右击鼠标选择New\Toolbox，将新建一个工具箱，可以修改工具箱的缺省名称。  
选中某个工具箱，右击鼠标，选择Add\Script（图1-1），将显示Add Script向导式页面。在Add Script的第一个页面中定义工具的名称、标注、描述等信息（图1-2）；在Add Script的第二个页面中指定工具调用的脚本文件（图1-3）；在Add Script的第三个页面中定义工具运行时的对话框参数（图1-4）。点击Finish按钮，将在选中的工具箱中产生一个新的工具，以工具的标注作为显示名称。  
双击工具，就会显示工具对话框（图1-5），点击对话框的OK按钮，将运行工具（即运行python程序）。
!["fig1-1"](http://119.3.40.193:8080/gis_development/photos/fig1-1.png)
<center>图1-1</center> 

!["fig1-2"](http://119.3.40.193:8080/gis_development/photos/fig1-2.png)
<center>图1-2和图1-3</center> 

!["fig-3"](http://119.3.40.193:8080/gis_development/photos/fig1-4.png)  
<center>图1-4</center>

!["fig-5"](http://119.3.40.193:8080/gis_development/photos/fig1-5.png) 
<center>图1-5 工具运行界面</center>
    
### 1.2.2 不依赖已有平台的GIS工具开发
GIS工具开发主要是开发者为解决某个问题（如坐标转换）而进行的开发，因此，更多关注工具的运行结果，工具的运行界面、交互操作以及运行稳定性等不是开发关注的重点。

GIS工具可以利用不同的编程语言（如C、C++、Python、JAVA等）进行开发，在开发过程中，也可以利用已有的GIS API。目前有很多GIS API，如GDAL、OGR、GEOS、PROJ.4等，这些API的底层大都使用C和C++编写，但同时提供其它编程语言（如Python）的接口。


### 1.2.3不依赖已有平台的GIS应用系统开发
GIS应用系统通常是提供给其他用户使用，因此需要有系统操作界面及交互功能，要考虑系统运行的稳定性、运行速度等。  

GIS应用系统的开发通常是利用GIS开发包，GIS开发包不仅包含GIS API，同时还包含地图、工具条、TOC等控件，可以利用这些控件快速构建GIS应用系统。
GIS开发包目前也很多，如Esri公司早期的MapObjects、ArcGIS Engine以及目前的ArcGIS Runtime SDK。

GIS开发包通常会有多个版本，支持不同的编程语言，如ArcGIS Runtime SDK目前有.net、JAVA、Qt等版本，分别支持这些编程语言，但开发的方法基本一致。  

利用GIS开发包开发GIS应用系统的基本步骤：  
-  新建项目。
-  添加Map、Toc、Toolbar等控件。
-  Map控件属性设置，如加载的文档。
-  Toolbar控件属性设置，如添加的工具。
-  Map控件和Toc、Toolbar等控件的关联。
-  定制功能开发。

图1-6是利用ArcGIS Engine开发的临港新城规划与建设地理信息系统。
!["fig1-6"](http://119.3.40.193:8080/gis_development/photos/fig1-6.png)
<center>图1-6 临港新城规划与建设地理信息系统</center>

