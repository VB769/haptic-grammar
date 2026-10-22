# haptic-grammar · 触觉语法

> 给没有屏幕的 AI 硬件设计排版学。
> A haptic design protocol for screenless AI hardware.


---

## 立即体验（无需任何配置）

- **在线试听**：[htmlpreview 预览](https://htmlpreview.github.io/?https://github.com/VB769/haptic-grammar/blob/main/index.html)
  用 Android Chrome 打开，点任意 motif 就能感到振动。
- **本地运行**：下载 [`index.html`](index.html) 用浏览器打开即可，完全离线。

> GitHub Pages 尚未开启（需要在 Settings → Pages 手动选一次 `main` / root）。
> 开启后的地址是 <https://vb769.github.io/haptic-grammar/>，届时可以删掉上面的预览链接。

## 为什么

AI 硬件正在集体去屏幕：眼镜、耳机、戒指、胸针。它们最重要的输出通道是**振动**，但没有人定义过它的语法——每个产品都在随机抖动。

三个缺失：

1. **没有书写系统**：振动无法被记录、review、diff
2. **没有感知依据**：没人公开测过哪些节律人类真的分得清
3. **没有语义映射**：随机编码 → 学习成本极高

本项目要做的是**协议**，不是应用。

## 三条硬约束

| 约束 | 事实 | 后果 |
| --- | --- | --- |
| 平台 | iOS Safari 完全不支持 navigator.vibrate | 规范必须设备无关，iOS 走 Core Haptics |
| 自由度 | Web 只能控制「开/关 + 时长」，无强度、无频率 | 只有两个维度：脉冲时长、间隔 |
| 生理 | 马达启停延迟 10-25ms；短期记忆 4-7 个符号；带宽约几 bit/s | 最小时间单位 >= 20ms；不做字母表，做语义槽 |

## 三层结构

```
L0 原子    S = 30ms   L = 90ms   gap = 60ms   word = 240ms
L1 音节    SS  SL  LS  SLS  LSS ... (<= 8 个)
L2 语义槽  谁 / 什么事 / 多急
```

自然映射（不靠记忆）：

- 急促、密集 = 紧急
- 缓慢、稀疏 = 可忽略
- 连击 = 立即行动

## 触谱 Tactile Score

用纯文本写振动，可 diff、可 review、可版本化：

```
# 单位 ms   S=30  L=90  gap=60  word=240

MOM_CALL  = L gap S gap L word L        # 慢而长 = 家人；结尾重音 = 呼叫
WORK_MSG  = S gap S gap L word SS       # 快而碎 = 工作流
CI_FAIL   = L gap L gap L word LLLL     # 连击 = 立刻处理
BATTERY   = S gap L word S gap S        # 下沉感 = 状态
```

## 要测的东西

规范里的每个数字都应该有数据支撑，而不是猜：


---

## v0.1 参考实现（单文件，可直接跑）

复制下面这段存成 `index.html`，用 **Android Chrome** 打开就能听到四种 motif。iOS Safari 没有 Vibration API，只会显示提示。

```html
<!doctype html>
<meta name=viewport content="width=device-width,initial-scale=1">
<title>触谱 · Tactile Score</title>
<style>
body{background:#0d1117;color:#e6edf3;font:16px system-ui;padding:20px;margin:0}
button{display:block;width:100%;margin:10px 0;padding:18px;border-radius:12px;
  border:1px solid #30363d;background:#161b22;color:#e6edf3;font-size:16px;text-align:left}
small{display:block;color:#8b949e;margin-top:4px}
</style>
<h3>触谱 · Tactile Score</h3>
<div id="b"></div>
<p id="w" style="color:#f85149"></p>
<script>
const U = { S: 30, L: 90, gap: 60, word: 240 };
const M = {
  MOM_CALL: "L gap S gap L word L",
  WORK_MSG: "S gap S gap L word S S",
  CI_FAIL : "L gap L gap L word L L L L",
  BATTERY : "S gap L word S gap S"
};
const p = s => s.split(" ").map(t => U[t]);
if (!navigator.vibrate) w.textContent = "此浏览器不支持振动（iOS 需原生 Core Haptics 实现）";
for (const k in M) {
  const b = document.createElement("button");
  b.innerHTML = k + "<small>" + M[k] + "</small>";
  b.onclick = () => navigator.vibrate(p(M[k]));
  document.getElementById("b").appendChild(b);
}
</script>
```

## 进度

- [x] v0.1 规范草案 + 触谱记号
- [x] v0.1 参考实现（上方代码，可直接复制运行）
- [ ] v0.2 仓库内 `index.html` + GitHub Pages
- [ ] v0.3 ABX 实验页 + 混淆矩阵汇总
- [ ] v0.4 最小可辨集 v1（用实测数据砍到 6-8 个）
- [ ] v1.0 Android / iOS / watchOS 参考实现

> 完整版（播放 + ABX + 混淆矩阵导出，约 180 行）已在本机准备好，等 `index.html` 能被写入仓库后直接提交。

1. **ABX 强制二选一** → 混淆矩阵
2. **最小可辨集**：从 12 个 motif 递归剔除混淆率高的，收到 6-8 个
3. **跨天学习曲线**：第 1/3/7 天各认对多少
4. **部位迁移**：手 / 手腕 / 口袋 三个位置的识别率差

目标产出：`data/confusion-matrix.json` + 一张「可辨性排行榜」。

## 路线图

- [x] v0.1 规范草案（本文件）+ 触谱记号
- [ ] v0.2 单文件播放器（GitHub Pages）
- [ ] v0.3 ABX 实验页 + 结果汇总
- [ ] v0.4 最小可辨集 v1
- [ ] v1.0 Android / iOS / watchOS 参考实现

## 为什么值得做

屏幕是过去二十年的默认输出。当硬件开始去掉屏幕，振动就是唯一的「字体」。现在定义它，成本最低。

## 许可

MIT (待定)
