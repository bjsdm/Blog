# 屏幕适配学习

>当我们面对物质时，不是"从这个东西需不需要?"的角度，而是往往下意识地”从这个东西能不能用？“的角度--以物质资料为基准轴的观点lai进行取舍和判断。这就是'物质轴"思维，曾几何时我也存了那几百gb的计算机学习资料及相关电子书，直到现在，我打开那文件夹的数次都屈指可数。很多时候，我想自己应该明白的是自己需要什么，而不是什么有用

## 主流方案

### 今日头条适配方案

> 最低成本的适配
> 
#### 原理

> **px = density * dp**  
dp : 安卓开发人员常常挂在嘴上的长度单位  
px : 设计人员眼中的长度单位  
**density = dpi / 160**  
因此，**px = dp * (dpi/160)**  
dpi : 根据屏幕真实分辨率和尺寸计算得出  
举个例子：屏幕分辨率为 1920 * 1080，屏幕尺寸为5寸(屏幕斜边长度cm/0.3937), 则 
**dpi = √(宽度²+ 高度²)/屏幕尺寸**

只要 density 根据不同的设备进行实时计算并作出改变，就能保证 设计图总宽度 不变，也就完成了适配。

### smallestWidth 限定符适配方案

#### 原理
>宽高限定符屏幕适配方案的升级版
```
************************************
***宽高限定符屏幕适配方案
├── src/main  
│ ├── res  
│ ├── ├──values  
│ ├── ├──values-800x480  
│ ├── ├──values-860x540  
│ ├── ├──values-1024x600  
│ ├── ├──values-1024x768  
│ ├── ├──...  
│ ├── ├──values-2560x1440
*************************************
***smallestWidth 限定符屏幕适配方案
├── src/main  
│ ├── res  
│ ├── ├──values  
│ ├── ├──values-sw320dp  
│ ├── ├──values-sw360dp  
│ ├── ├──values-sw400dp  
│ ├── ├──values-sw411dp  
│ ├── ├──values-sw480dp  
│ ├── ├──...  
│ ├── ├──values-sw600dp  
│ ├── ├──values-sw640dp
```
开发者先在项目中根据主流屏幕的  **最小宽度 (smallestWidth)** 生成一系列  **values-sw<N>dp**  文件夹 (含有 dimens.xml 文件)，当把项目运行到设备上时，系统会根据当前设备屏幕的 最小宽度 (smallestWidth) 去匹配对应的 values-sw<N>dp 文件夹，而对应的 values-sw<N>dp 文件夹中的 dimens.xml 文字中的值，又是根据当前设备屏幕的 最小宽度 (smallestWidth) 而定制的，所以一定能适配当前设备。

  
**如果系统根据当前设备屏幕的 最小宽度 (smallestWidth) 没找到对应的 values-sw<N>dp 文件夹，则会去寻找与之 最小宽度 (smallestWidth) 相近的 values-sw<N>dp 文件夹**，系统只会寻找小于或等于当前设备 最小宽度 (smallestWidth) 的 values-sw<N>dp，这就是优于 宽高限定符屏幕适配方案 的容错率，并且也可以少生成很多 values-sw<N>dp 文件夹，减轻 App 的体积

#### 算法
我们假设设备的屏幕信息是 1920 * 1080、480 dpi

  

根据上面的规则我们要在屏幕的高度和宽度中选择值最小的一方作为最小宽度，1080 < 1920，明显 1080 px 就是我们要找的 最小宽度 的值，但 最小宽度 的单位是 dp，所以我们要把 px 转换为 dp

**px / density = dp，DPI / 160 = density，所以最终的公式是 px / (DPI / 160) = dp**

所以我们得到的 最小宽度 的值是 360 dp (1080 / (480 / 160) = 360)。

现在我们已经算出了当前设备的最小宽度是 360 dp，我们晓得系统会根据这个 最小宽度 帮助我们匹配到 values-sw360dp 文件夹下的 dimens.xml 文件，如果项目中没有 values-sw360dp 这个文件夹，系统才会去匹配相近的 values-sw<N>dp 文件夹。

### AndroidAutoSize

> AndroidAutoSize 是 AndroidAutoLayout 的升级版，最牛逼的适配框架，吹爆！！

#### DownLoad
` implementation 'me.jessyan:autosize:1.1.2'`

#### AndroidManifest
```xml
<manifest>
    <application>            
        <meta-data
            android:name="design_width_in_dp"
            android:value="360"/>
        <meta-data
            android:name="design_height_in_dp"
            android:value="640"/>           
     </application>           
</manifest>
```

