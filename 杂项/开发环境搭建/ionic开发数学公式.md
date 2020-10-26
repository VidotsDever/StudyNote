* 创建工程：`ionic start MathExts blank --type=react --capacitor`
* 安装必要的依赖项：

```
npm install katex @types/katex(如果使用ts)
```

* 开发流程搭建：

```
import renderMathInElement from 'katex/contrib/auto-render/auto-render'
import 'katex/dist/katex.css';

<div id=""math-container></div>

renderMathInElement(document.getElementById('math-container'), {
    delimiters: [
          { left: '$$', right: '$$', display: false },
          { left: '$', right: '$', display: false }
    ],
    leqno: true
})
```

