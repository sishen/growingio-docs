# 应用级数据

* [1 App应用推广数据](ying-yong-ji-shu-ju.md#1app-ying-yong-tui-guang-shu-ju)
  * [1.1 数据指标概念](ying-yong-ji-shu-ju.md#11-shu-ju-zhi-biao-gai-nian)
  * [1.2 全局设定](ying-yong-ji-shu-ju.md#12-quan-ju-she-ding)
  * [1.3 激活设备数据](ying-yong-ji-shu-ju.md#13-ji-huo-she-bei-shu-ju)
  * [1.4 广告活动和渠道效果分析](ying-yong-ji-shu-ju.md#14-guang-gao-huo-dong-he-qu-dao-xiao-guo-fen-xi)
  * [1.5 推广日报](ying-yong-ji-shu-ju.md#15-tui-guang-ri-bao)
  * [1.6 推广详细​](ying-yong-ji-shu-ju.md#16-tui-guang-xiang-xi)
  * [1.7 异常数据报告](ying-yong-ji-shu-ju.md#17-yi-chang-shu-ju-bao-gao)
* [2 网页应用推广数据](ying-yong-ji-shu-ju.md#2-wang-ye-ying-yong-tui-guang-shu-ju)
  * [2.1 数据指标概念](ying-yong-ji-shu-ju.md#2-1-1-shu-ju-zhi-biao-gai-nian)
  * [2.2 全局设定](ying-yong-ji-shu-ju.md#2-1-2-quan-ju-she-ding)
  * [2.3 广告活动和渠道效果分析​](ying-yong-ji-shu-ju.md#2-1-3-guang-gao-huo-dong-he-qu-dao-xiao-guo-fen-xi)
  * [2.4 活动效果对比](ying-yong-ji-shu-ju.md#2-1-4-huo-dong-xiao-guo-dui-bi)
  * [2.5 报表详情数据](ying-yong-ji-shu-ju.md#2-1-5-bao-biao-xiang-qing-shu-ju)
  * 

### 1 App 应用推广数据

#### 1.1 数据指标概念

* 激活：激活是指 App下载后首次联网打开 SDK 会上报一条该设备的激活数据，该指标可理解为 App 的下载安装量。
* 激活总量：通过 SDK 上报至 GrowingIO 的全部激活设备总量。
* 推广激活：通过监测链接监测到发生激活的设备。
* 推广新激活：推广激活设备的排重后数量，对同一设备在时间周期内（30天）反复激活仅记一次。
* 
#### 1.2 全局设定

![](../../.gitbook/assets/image%20%282%29.png)

**全局时间控件**

可通过时间控件设定当前报表数据统计时间段，时间范围设定后将应用在报表全局。（上图位置 1 所示）

**添加转化指标**

可以添加您关注的业务转化指标，该业务指标在下方图表中将作为基本的分析数据指标。（上图位置 2 所示 ）

**全局数据**

展示当前移动端广告投放环节的基本监控指标数据。（上图位置 3 所示 ）

周期对比：根据当前选择的时段，计算周期对比，当前为周对比，并提供说明本周期与上周期时间计算区间。

{% hint style="info" %}
全局数据中各指标间数据无发生先后顺序逻辑关联，各指标数据为单指标独立统计的加和数据。
{% endhint %}

#### 1.3 激活设备数据

展示当前 App 下的激活数据，以线图方式展示数据变化情况，结合右侧提供圆环图，可快速了解当前推广激活占整体激活的比例，以及推广新激活在推广激活中的占比分布，方便衡量活动推广效率及活动推广质量。

此图表支持数据导出，通过右上角下载按钮即可导出当前表格数据。

![](../../.gitbook/assets/image%20%28172%29.png)

#### 1.4 广告活动和渠道效果分析

以线图、柱图的方式展示当前选择的时段下，广告活动和广告渠道的数据情况，并在图表右侧提供简单洞察，可快速发现执行效果最优的广告活动以及广告渠道。

#### 1.5 推广日报

推广日报可查看各链接级别的详细统计数据，上方数据图表中的数据逻辑与该推广日报统计逻辑一致。

支持按统计字段聚合展示数据，支持的字段包括：时间、推广活动、目标渠道、监测链接。

支持自定义表格列数据，通过自定义指标按钮可自定义需要展示的指标。

此报表支持数据导出，通过右上角下载按钮即可导出当前表格数据。

![](../../.gitbook/assets/image%20%28203%29.png)

#### 1.6 推广详细

推广详细展示的数据会以监测链接在点击阶段收到数据进行归因，后续转化阶段数据皆会向本次点击归因，数据统计按照点击发生环节统计，此方式方便衡量各链接推广效率。

支持按字段聚合展示数据，支持的字段包括：时间、推广活动、目标渠道、监测链接。

支持自定义表格列数据，通过自定义指标按钮可自定义需要展示的指标。

此报表支持数据导出，通过右上角下载按钮即可导出当前表格数据。

![](../../.gitbook/assets/image%20%28275%29.png)

#### 1.7 异常数据报告

系统会针对广告投放运行一系列模型&规则识别数据的异常性，并提供相关数据报告。 1. 选中目标 App，进入“基础数据”中“异常数据报告”模块。 2. 可以根据业务诉求进行部分异常数据识别规则配置。 3. 可点击“自定义列指标”进行指标选择，可选择的列指标有：去重点击，激活，异常点击，异常激活。

![](../../.gitbook/assets/image%20%28162%29.png)



### 2 网页应用推广数据

#### 2.1 数据指标概念 <a id="2-1-1-shu-ju-zhi-biao-gai-nian"></a>

* 点击：GrowingIO 监测链接的点击数统计。
* 进入量：访问用户进入网站进行访问的数量。
* 访问用户量：访问用户的数量。
*  新访问用户量：过去 365 天内首次访问用户的数量。

#### **2.2 全局设定** <a id="2-1-2-quan-ju-she-ding"></a>

![](https://docs.growingio.com/.gitbook/assets/-LGNxeGABUADKiTWTaEM-LeHgAxqElp3IcWMpkcp-LeHgZ2zvDAGpbCgAnyRE59BBEE78987.png)

**全局时间控件**

可通过时间控件设定当前报表数据统计时间段，时间范围设定后将应用在报表全局。（上图位置 1 所示）

**添加转化指标**

可以添加您关注的业务转化指标，该业务指标在下方图表中将作为基本的分析数据指标。（上图位置 2 所示 ）

**全局数据**

展示当前 Web 端广告投放环节的基本监控指标数据。（上图位置 3 所示 ）

周期对比：根据当前选择的时段，计算周期对比，当前为周对比，并提供说明本周期与上周期时间计算区间。

全局数据中各指标间数据无发生先后顺序逻辑关联，各指标数据为单指标独立统计的加和数据。

#### 2.3 广告活动和渠道效果分析 <a id="2-1-3-guang-gao-huo-dong-he-qu-dao-xiao-guo-fen-xi"></a>

以线图、柱图的方式展示当前选择的时段下，广告活动和广告渠道的数据情况，并在图表右侧提供简单洞察，可快速发现执行效果最优的广告活动以及广告渠道。

#### 2.4 活动效果对比 <a id="2-1-4-huo-dong-xiao-guo-dui-bi"></a>

**访问用户量/点击量**

在活动效果对比中，可直接结合用户行为数据，对比不同活动间的转化效果，使用折线图来比较广告活动访问用户量、广告活动点击数据，分析对比两个活动间的同期效果。

**转化漏斗**

以转化漏斗形式展示从广告活动点击到目标页访问（落地页），再到设定的转化目标，各环节的转化情况，对比不同活动间的转化效率。

**访问人数渠道贡献**

展示在到达目标页面的访问人数中，不同渠道的访问人数占比，对比各渠道的投放效果。

#### 2.5 报表详情数据 <a id="2-1-5-bao-biao-xiang-qing-shu-ju"></a>

**推广详情**

推广详细可查看各链接级别的详细统计数据，上方数据图表中展示的数据依照该推广日报。

支持按字段聚合展示数据，支持的字段包括：时间、推广活动、目标渠道、监测链接**。**

此报表支持数据导出，通过右上角下载按钮即可导出当前表格数据。

![](https://docs.growingio.com/.gitbook/assets/-LGNxeGABUADKiTWTaEM-LeHgluGjm1y4gHUszxj-LeHh0rbhQ-BNzrWQ-xTimage.png)

####  <a id="22"></a>

####  <a id="22"></a>
