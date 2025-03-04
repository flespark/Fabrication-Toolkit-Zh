<img src="./assets/logo.svg?raw=true" style="display:block; margin-left: auto; margin-right: auto;" alt="JLC PCB Plug-in for KiCad">
    
<div align="center">

| **KiCad导出嘉立创 PCB 生产文件的插件** |
|:--------------------------:|

[![Sponsor](https://img.shields.io/static/v1?label=Sponsor&message=%E2%9D%A4&logo=GitHub&color=%23fe8e86)](https://github.com/sponsors/bennymeg)

</div>

## 说明

此仓库为 [bennymeg/Fabrication-Toolkit: An JLC PCB Fabrication Plugin for KiCad](https://github.com/bennymeg/Fabrication-Toolkit) 的中文版本，方便国内的KiCad用户导出嘉立创要求的下单文件格式。包含以下修改：

1. 导出的 BOM 文件改为国内下单要求的格式
2. 文档翻译和更新

## 功能
1. 生成符合生产要求格式的 Gerber 文件
2. 生成符合生产要求格式的 BOM 文件
3. 生成符合组装要求格式的贴片坐标文件
4. 生成 IPC 网表文件

## 安装

### 手动安装
下载[最新版本](https://github.com/flespark/Fabrication-Toolkit-Zh/releases)的 ZIP 文件。从 KiCad 主窗口打开"扩展内容管理器"，并通过"从文件安装"安装 ZIP 文件。

## 使用方法
在 KiCad PCB 编辑器（pcbnew）的顶部工具栏中点击制造工具包 <img src="./resources/icon.png?raw=true" style="margin-bottom: 8px;" alt="Logo" height=24> 按钮。

**⊛** 每次更新后调用此插件前，请确保您已经在 PCB 编辑器中同步原理图的修改 [**F8**]。

**⊛** `User_1` 层在内部被定义为 **V-Cuts**（割板线）层，请避免将其用于其他用途（除非禁用）。<span style="text-color: light-grey !important;">_(自 v3.0.0 版本起)_</span>

## 选项

选项可以在调用插件时出现的对话框中设置。这些选项保存在项目目录下名为 `fabrication-toolkit-options.json` 的文件中，以便在插件的多次调用之间记住设置。

<img src="./assets/options.png?raw=true" height=275>

☑ __附加层__：要包含在 Gerber 存档中的附加层的逗号分隔列表。</br>
☑ __将 User.1 设置为 V-Cut 层__：在生产中将 User.1 层与 Edge-Cut 层合并。</br>
☑ __使用 User.2 作为替代 Edge-Cut 层__：在生产中使用 User.2 而不是 Edge-Cut 层作为电路板轮廓。如果您在生产过程中需要处理边缘或拼板，但仍希望保留单个轮廓用于原型设计、3D 模型导出或类似目的，这将非常有用。</br>
☑ __应用自动翻译__：为常见元器件应用已知的翻译修复。</br>
☑ __对所有区域应用自动填充__：在生成生产文件之前重新填充所有区域。</br>
☑ __从物料清单中移除 DNP 元器件__：从物料清单中移除已设置为 DNP（不安装）的元器件。</br>

### ① 在生产文件中包含立创商城元器件编号
在符号的字段属性中添加一个"LCSC Part #"字段，其中包含立创商城元器件编号。

<img src="./assets/mpn.png?raw=true">

#### 主要字段：
| 'LCSC Part #' | 'LCSC Part' | 'JLCPCB Part #' | 'JLCPCB Part' |
| ------------- | ----------- | --------------- | ------------- |

_字段将按上述顺序查询。_

#### 备用字段：
| 'LCSC' | 'JLC' | 'MPN' | 'Mpn' | 'mpn' |
| ------ | ----- | ----- | ----- | ----- |

_字段将按上述顺序查询。_

---

### ② 在生产文件中忽略封装
在符号的属性属性中选择"不在PCB中进行绘制"或"从物料清单中移除"，以便从相关文件中忽略封装。

<img src="./assets/attributes.png?raw=true">

在封装属性中选择"从定位文件中排除"或"从物料清单中移除"，以便从相关文件中忽略封装。

<img src="./assets/fabrication.png?raw=true">

---

### ③ 偏移元器件旋转
KiCad 封装中的元器件旋转有时与 JLC 库中的方向不匹配，因为 KiCad 和 JLC PCB 使用了同一标准的不同变体。对于例外情况：添加一个"FT Rotation Offset"*字段 - 正值表示逆时针方向的角度偏移。

<img src="./assets/rotation-jlc.png?raw=true">

如果 JLC 预览显示如上图所示的封装，请输入 -90 的旋转偏移，将引脚 1 旋转到右下角。

<img src="./assets/rotation.png?raw=true">

由于旋转偏移是在原理图编辑器中输入的，请确保在生成新的生产文件之前，用原理图中的更改更新您的 PCB，否则您的更改将不会被反映。

#### 主要字段：
| 'FT Rotation Offset' |
| -------------------- |

_字段将按上述顺序查询。_

#### 备用字段：
| 'Rotation Offset' | 'RotOffset' |
| ----------------- | ----------- |

_字段将按上述顺序查询。_

---

### ④ 偏移元器件位置
KiCad 封装中的元器件位置有时与 JLC 库中的位置不匹配，因为 KiCad 和 JLCPCB 使用了同一标准的不同变体。对于例外情况：添加一个"FT Position Offset"*字段，其中包含逗号分隔的 x,y 位置偏移以进行修正。 根据 PCB 编辑器的元器件封装属性中的坐标方向，x,y 对应不同的偏移方向。当方向为0，即封装无旋转时，x,y 的正数数值分别对应向右和向上偏移。

使用下表可以根据 KiCad PCB 编辑器的的元器件封装属性中的坐标方向，快速找出 x,y 正值对应在嘉立创 SMT 仿真图中的偏移方向：

| KiCad 封装角度     | x      | y      |
| ------------------ | ------ | ------ |
| 0 度，正面         | 右偏移 | 上偏移 |
| 0 度，背面         | 左偏移 | 下偏移 |
| 180 度，正面       | 左偏移 | 下偏移 |
| 180 度，背面       | 右偏移 | 上偏移 |
| 90 度，正面或背面  | 上偏移 | 左偏移 |
| -90 度，正面或背面 | 下偏移 | 右偏移 |

对于其他自定义角度，最好放置一个临时的直线符号来进行对齐。

嘉立创 SMT 仿真图中选中元器件单次按下方向键的偏移量为 0.0635mm（= 1/400 英寸），所以可以先在仿真图选中器件后按方向键将元器件移动到正确位置，并根据按键次数算出合适的偏移修正值。

<img src="./assets/position.png?raw=true">

由于位置偏移是在原理图编辑器中输入的，请确保在生成新的生产文件之前，用原理图中的更改更新您的 PCB，否则您的更改将不会被反映。

#### 主要字段：
| 'FT Position Offset' |
| -------------------- |

_字段将按上述顺序查询。_

#### 备用字段：
| 'Position Offset' | 'PosOffset' |
| ----------------- | ----------- |

_字段将按上述顺序查询。_

### ⑤ 覆盖元器件原点
制造工具包根据自动选择的参考点报告每个元器件的位置。可以通过向元器件添加"FT Origin"*字段来覆盖此默认行为。

#### 主要字段：
| 'FT Origin' |
| ----------- |

_字段将按上述顺序查询。_

#### 备用字段：
| 'Origin' |
| -------- |

**Origin** 字段支持以下值：

- `Anchor` - 使用封装的锚点，可以在 KiCad 的封装编辑器中修改。
- `Center` - 使用由封装的焊盘形成的边界框的中心。

### ⑥ 覆盖元器件层
某些封装可能在与其实际封装相反的层上定义了元器件。在这些情况下，您可以通过向元器件添加"FT Layer Override"*字段来覆盖安装面。

**Layer Override** 字段支持以下值：
- `top`, `t` - 将封装层覆盖为顶层。
- `bottom`, `b` - 将封装层覆盖为底层。

#### 主要字段：
| 'FT Layer Override' |
| ------------------- |

_字段将按上述顺序查询。_

#### 备用字段：
| 'Layer Override' | 'LayerOverride' |
| ---------------- | --------------- |

_字段将按上述顺序查询。_


## 命令行界面
该插件也可以通过 Linux 命令行使用。如果您需要将插件嵌入到自动化流程或环境中，这将特别有用。
可以使用以下命令调用插件：
```
python3 -m plugins.cli -p /myProject/myBoard.kicad_pcb
```

GUI 中的所有选项也可通过命令行界面使用：
```
python3 -m plugins.cli -h

用法: Fabrication Toolkit [-h] --path PATH [--additionalLayers LAYERS] [--user1VCut] [--user2AltVCut] [--autoTranslate] [--autoFill] [--excludeDNP] [--allActiveLayers] [--openBrowser]

从 KiCAD 电路板文件生成 JLCPCB 生产文件

选项:
  -h, --help                                显示此帮助信息并退出
  --path PATH, -p PATH                      KiCAD PCB 文件路径
  --additionalLayers LAYERS, -aL LAYERS     附加层（逗号分隔）
  --user1VCut, -u1                          将 User.1 设置为 V-Cut 层
  --user2AltVCut, -u2                       使用 User.2 作为替代 Edge-Cut 层
  --autoTranslate, -t                       应用自动位置/旋转翻译
  --autoFill, -f                            对所有区域应用自动填充
  --excludeDNP, -e                          从物料清单中移除 DNP 元器件
  --allActiveLayers, -aaL                   导出所有活动层而不仅仅是常用层
  --openBrowser, -b                         生成后打开带有目录文件概览的网页浏览器
```


## 作者

Benny Megidish
