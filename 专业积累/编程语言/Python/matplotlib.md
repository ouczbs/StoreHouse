# 显示中文

## 下载字体

```shell
#https://www.wfonts.com/
wget https://www.wfonts.com/download/data/2014/06/01/simhei/simhei.zip
unzip -d font simhei.zip
```

## 移动字体

```python
import matplotlib as mpl
fname = mpl.matplotlib_fname()
ttfpath = fname.replace("matplotlibrc" , "fonts/ttf")
print(ttfpath)
#cp font/SimHei.ttf {ttfpath}
```

## 删除缓存

```python
---【import matplotlib as mpl
configdir = mpl.get_configdir()
fontlist = configdir + "/fontlist-*.json"
print(fontlist)
#rm {fontlist}

cachedir = mpl.get_cachedir()
print(cachedir)
#rm {cachedir}
```

## 临时显示

```python
#运行前修改一次全局字体
import matplotlib as mpl
mpl.rc("font", family='SimHei') 
#plt.rcParams['font.sans-serif'] = ['SimHei'] # 步骤一（替换sans-serif字体）
mpl.rc('axes', unicode_minus=False) 
#plt.rcParams['axes.unicode_minus'] = False  # 步骤二（解决坐标轴负数的负号显示问题
```

```python
#在画图代码中加入自定义字体参数
from matplotlib.font_manager import FontProperties  # 导入FontProperties
font = FontProperties(fname="SimHei.ttf", size=14)  # 设置字体

# x y 轴标签   字体大小
plt.xlabel(fontproperties=font)
plt.ylabel(fontproperties=font)
# 显示图例
plt.legend(prop=font)
```

## 永久显示

### 配置文件

```python
#将全局显示中文的改动直接在文件中改动
import matplotlib as mpl
fname = mpl.matplotlib_fname()
print(fname)
```

### 修改配置

```python
#  去掉 # 注释   
font.family         : sans-serif   

#  去掉 # 注释，冒号后添加 SimHei
font.sans-serif: SimHei, DejaVu Sans, Bitstream Vera Sans, Computer Modern Sans Serif, Lucida Grande, Verdana, Geneva, Lucid, Arial, Helvetica, Avant Garde, sans-serif

#  去掉 # 注释，将 True 改为 False
axes.unicode_minus  : False
```

