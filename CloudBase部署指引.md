# 《高职英语一》工作台 · CloudBase 超详细部署指引（每一步点哪里）

本指引面向**非技术背景**教师，按步骤点击即可完成部署，全程不用写代码。

> 你的环境 ID 是：`college-english-d9ffi87h93f30a34`（已焊进工作台，部署后自动连接）。
> 你本地文件位置：`D:\workbuddy 文件暂存\2026-09-02-12-27-53\高职英语一工作台\`

---

## ⚠️ 已修复：CloudBase SDK 路径

旧版 `index.html` 引用的 SDK 地址 `.../3.0.1/cloudbase.full.js` 已失效（404），会导致学生端提示「CloudBase SDK 未加载」「提交失败：no-app」。

**请使用最新 `index.html`（已改成 `.../latest/cloudbase.full.js`）重新上传到静态网站托管根目录。**

---

## 先处理这个：如果环境是「体验版」，必须切换为按量付费

从 2025 年腾讯云政策调整开始，**免费体验版环境会锁定静态网站托管的公网访问权限**。表现就是：
- 文件管理里已经上传了 `index.html`
- 但访问默认域名时返回 **HTTP ERROR 418 当前无法使用此页面**

**解决方法**：把环境计费方式从「体验版」切换为「按量计费」。

1. 控制台顶部确认环境名旁边是否显示 **「体验版」**。
2. 左侧菜单 **「环境」** → **「资源购买」/「计费模式」** → 点 **「切换按量付费」**。
3. 按提示确认（需腾讯云账户有少量余额，用于按量扣费；课堂小流量通常很便宜）。
4. 切换成功后，再刷新默认域名，页面就能打开了。

> 这是腾讯云的计费策略限制，不是代码或上传操作的问题。如果确定不想付费，可改用 CloudStudio 免费部署静态网页（需额外改代码适配跨域，相对麻烦），建议 classroom 场景直接切换按量计费。

---

## 第 0 步：打开控制台，进入你的环境

1. 浏览器打开：**https://console.cloud.tencent.com/tcb**
2. 用 **微信 / QQ / 手机号** 扫码登录。
3. 页面左上角（或顶部）有一个**「环境」下拉框**，点开它，选择你的环境
   `college-english-d9ffi87h93f30a34`。
4. 进入后，页面**左侧会出现一列菜单**，包括：云函数、数据库、静态网站托管、登录授权 等。后面所有操作都在这一列里点。

---

## 第 1 步：静态网站托管 —— 上传 index.html（学生扫码打开的网页）

1. 左侧菜单点 **「静态网站托管」**。
2. 如果看到「**未开通** / 立即开通」字样 → 点那个按钮，按提示点「确认开通」（新用户有免费额度，课堂小流量基本不花钱）。
3. 开通后页面上方有几个标签页，点 **「文件管理」** 标签。
4. 点 **「上传文件」** 按钮 → 在弹出的文件选择框里，找到你电脑上的
   `高职英语一工作台\index.html` 并选中，点「打开」。
   > 提示：这是单文件网页，上传这一个就够了，不需要上传其它文件或文件夹。
5. 如果提示「已存在同名文件，是否覆盖」→ 选 **「覆盖 / 替换」**。
6. 上传成功后，切到 **「基础配置」** 标签页，找到 **「默认域名」**，
   复制它（形如 `https://college-english-d9ffi87h93f30a34-xxxx.tcloudbaseapp.com`）。
   **这个网址就是学生扫码后手机打开的网页，后面对它都要用它，请先存到记事本。**

✅ 完成标志：文件管理里能看到 `index.html`，且基础配置里有默认域名。

---

## 第 2 步：开启「匿名登录」（关键！不开学生就无法提交成绩）

1. 左侧菜单点 **「登录授权」**（有的版本在「环境 → 登录授权」里）。
2. 在登录方式列表里找到 **「匿名登录」**。
3. 把它右边的**开关拨到「开」**（变成绿色）。
4. 其它都不用动，直接关掉即可。

⚠️ 这一步漏掉，学生手机会一直提交失败。

---

## 第 3 步：部署 3 个云函数（最关键，按下面操作重复 3 次）

这三个云函数负责「收成绩 / 算汇总 / 同步课文」，分别对应：
`submitResult`、`getSummary`、`unitContent`。
**每个函数都做一遍下面的 1–9 步，共 3 遍**（函数名换一下即可）。

1. 左侧菜单点 **「云函数」**。
2. 点右上角 **「新建云函数」**（有的版本叫「新建」）。
3. 弹出创建窗口：
   - **函数名称**：填 `submitResult`（第二遍填 `getSummary`，第三遍填 `unitContent`）
   - **运行时**：选 **Node.js 16** 或 **Node.js 18**
   - **函数模板**：选 **「空白函数」**
4. 点 **「完成 / 创建」**，进入该函数的详情页。
5. 详情页上方点 **「函数代码」** 标签页。
6. 在代码区上方找 **「本地上传」**（可能显示为下拉箭头）→ 选 **「文件夹」**。
7. 在文件选择框里，选中你电脑上的对应文件夹（**整文件夹**，不是单独文件）：
   - 第 1 遍：`高职英语一工作台\cloud-functions\submitResult\`
   - 第 2 遍：`高职英语一工作台\cloud-functions\getSummary\`
   - 第 3 遍：`高职英语一工作台\cloud-functions\unitContent\`
   > 每个文件夹里已经放好了 `index.js`（功能代码）和 `package.json`（依赖声明），一起传上去最省事。
8. 上传完成后，点 **「保存并安装依赖」**（有的版本按钮叫「上传并部署：云端安装依赖」，或用部署箭头里的这个选项）。系统会在云端自动安装 `@cloudbase/node-sdk`。
9. 等几秒到十几秒，状态显示 **「部署成功 / 已激活」** 即完成本函数。

🔁 回到第 1 步，换下一个函数名，再做一遍，直到 3 个都「部署成功」。

> 【备选·纯粘贴法】如果控制台没有「上传文件夹」、只能在线编辑：
> 在「函数代码」编辑器里把默认代码**全选删除**，把我文末对应的代码**整段粘贴**进去；
> 再点编辑器里的 `package.json` 文件，把内容也替换为文末给的依赖声明；
> 最后同样点「保存并安装依赖」。

---

## 第 4 步：测试连接（验证全部打通）

1. 用**第 1 步复制的 `https://…tcloudbaseapp.com` 网址**在浏览器打开工作台
   （⚠️ 不要用本地 `file://` 打开，那样二维码手机扫不了）。
2. 点页面右下角 **⚙️ 云平台** 按钮。
3. 弹窗里「环境 ID」应已显示 `college-english-d9ffi87h93f30a34`（已自动填好）。
4. 点 **「测试连接」**。
5. 若显示 **「✅ 调用成功，CloudBase 连接正常」** → 全部打通，可以上课了！
   若显示报错 → 看下方「常见问题」。

---

## 第 5 步：课堂使用流程

1. 用第 1 步的 `https://` 网址打开工作台 → 进入「📥 单元内容库」→ 录入/保存本单元课文、词汇、句型（保存时**自动同步到云端**）。
2. 进入「🎯 课堂助教」→ 选某工具（如词汇与句型闯关）→ 选单元。
3. 点 **📱 学生扫码参与** → 大屏显示二维码 → 学生手机扫码进入答题。
4. 学生填姓名/学号 → 答题 → 点「提交成绩给教师」。
5. 教师在该工具页点 **📊 汇总学情** → 自动拉取全班正确率/错误率，内联图表展示。

---

## 常见问题

- **测试连接报错「调用异常」**：① 检查第 2 步「匿名登录」是否真的拨到「开」；② 第 3 步三个函数是否都「部署成功」；③ 环境 ID 是否就是 `college-english-d9ffi87h93f30a34`（应已自动填好）。
- **学生提交后，教师汇总是空的**：确认学生确实点过「提交成绩给教师」且填了姓名；再确认第 3 步三个函数都部署成功。
- **二维码手机扫了打不开**：必须用在「静态网站托管 → 基础配置」里看到的 `https://…tcloudbaseapp.com` 网址打开工作台后生成的码；本地 `file://` 打开生成的码手机访问不了。
- **学生手机需要联网**（访问 CloudBase）。教室若无外网需另用本地服务器方案。
- **换设备/换浏览器**：单元内容存在浏览器本地，换机器后请回到「单元内容库」对每个单元点一次「保存本单元」，重新触发云端同步。

---

## 附：三个云函数代码（仅当用「纯粘贴法」时才需要）

> 用「上传文件夹」法可跳过这段。下方代码与 `cloud-functions/` 目录内文件完全一致。

### ① submitResult（接收成绩并写入数据库）

`package.json`：
```json
{
  "name": "submitResult",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "@cloudbase/node-sdk": "latest"
  }
}
```

`index.js`：
```js
const cloud = require('@cloudbase/node-sdk')
cloud.init({ env: cloud.DYNAMIC_CURRENT_ENV })
const db = cloud.database()

exports.main = async (event) => {
  const { unit, tool, studentId, studentName, correct, total, answers } = event
  if (!unit || !tool || !studentName) return { ok: false, error: '缺少必要参数' }
  await db.collection('results').add({
    unit,
    tool,
    studentId: studentId || '',
    studentName,
    correct: Number(correct) || 0,
    total: Number(total) || 0,
    rate: Number(total) ? (Number(correct) || 0) / Number(total) : 0,
    answers: answers || [],
    createdAt: Date.now()
  })
  return { ok: true }
}
```

### ② getSummary（统计全班正确率/错误率）

`package.json`：
```json
{
  "name": "getSummary",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "@cloudbase/node-sdk": "latest"
  }
}
```

`index.js`：
```js
const cloud = require('@cloudbase/node-sdk')
cloud.init({ env: cloud.DYNAMIC_CURRENT_ENV })
const db = cloud.database()

exports.main = async (event) => {
  const { unit, tool } = event
  const res = await db.collection('results').where({ unit, tool }).get()
  const list = res.data || []
  const total = list.length
  const overallCorrect = list.reduce((s, r) => s + (Number(r.correct) || 0), 0)
  const overallTotal = list.reduce((s, r) => s + (Number(r.total) || 0), 0)
  const rate = overallTotal ? overallCorrect / overallTotal : 0
  const students = list.map(r => ({
    name: r.studentName,
    id: r.studentId,
    rate: Number(r.total) ? (Number(r.correct) || 0) / Number(r.total) : 0,
    correct: Number(r.correct) || 0,
    total: Number(r.total) || 0
  }))
  const qErr = {}
  list.forEach(r => {
    ;(r.answers || []).forEach((a, i) => {
      if (!qErr[i]) qErr[i] = { wrong: 0, total: 0 }
      qErr[i].total++
      if (!a.correct) qErr[i].wrong++
    })
  })
  const questions = Object.keys(qErr)
    .sort((a, b) => a - b)
    .map(i => ({ q: Number(i), errRate: qErr[i].wrong / qErr[i].total }))
  return { ok: true, total, rate, overallCorrect, overallTotal, students, questions }
}
```

### ③ unitContent（同步课文/词汇到云端）

`package.json`：
```json
{
  "name": "unitContent",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "@cloudbase/node-sdk": "latest"
  }
}
```

`index.js`：
```js
const cloud = require('@cloudbase/node-sdk')
cloud.init({ env: cloud.DYNAMIC_CURRENT_ENV })
const db = cloud.database()

exports.main = async (event) => {
  const { action, unit, data } = event
  const col = db.collection('unitContent')
  if (action === 'set') {
    if (!unit || !data) return { ok: false, error: '缺少参数' }
    await col.doc(unit).set({
      unit,
      passage: data.passage || '',
      vocab: data.vocab || [],
      patterns: data.patterns || [],
      extra: data.extra || '',
      updatedAt: Date.now()
    })
    return { ok: true }
  }
  const res = await col.doc(unit).get()
  if (res.data && res.data.length) return { ok: true, data: res.data[0] }
  return { ok: false }
}
```
