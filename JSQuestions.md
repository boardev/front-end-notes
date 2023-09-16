# PDF

## jsPDF、html2canvas

```typescript
/**
 * If content that can show totally on one page with a stable position, use html() method.
 * Other cases, use the imperative APIs of jsPDF.
 * @see https://github.com/parallax/jsPDF#optional-dependencies
 * If only import `jspdf.umd.min.js` by <script>, you should also import optional dependencies when use html().
 * E.g. the html method, which depends on `html2canvas` 
 * and, when supplied with a string HTML document, `dompurify`. 
 */
```

[jsPDF + html2canvas A4 paging truncation perfect solution](https://juejin.cn/post/7138370283739545613)

## iframe print

```typescript
let iframe = document.createElement('iframe')
// MIME type or non-original URL
iframe.src = "data:application/pdf..." || url 
iframe.style.display = "none"
iframe.onload = () => {
  // The same origin policy prevents scripts from accessing content from different origins
  // throw error:  Blocked a frame with origin "http://localhost:4000" from accessing a cross-origin frame.
  iframe.contentWindow.print()
}
```

Local blob url can be built using **URL.createObjectURL**

```typescript
let iframe = document.createElement('iframe');

const url = URL.createObjectURL(new Blob([doc.output("blob")], { type: "application/pdf" }));

iframe.src = url
iframe.style.display = "none"

// You need to wait until the iframe is loaded before printing, cannot remove the iframe, otherwise it will not print.
iframe.onload = () => {
  iframe.contentWindow.print()
  iframe.onload = null
  iframe = null
  URL.revokeObjectURL(url);
}
iframe.onerror = () => {
  iframe.contentWindow.print()
  iframe.onerror = null
  iframe = null
  URL.revokeObjectURL(url);
}
```

# keyup 、keydown

On touch screen devices：
event.keyCode always is 229
event.key always is "Unidentified"
The input value can be determined through requestAnimationFrame, window.getSelection, event.target

# The cursor moves after the line

```javascript
requestAnimationFrame(()=>{
  const selection = window.getSelection();
  const range = document.createRange();
  // childNodes[0]: text
  range.setStart(event.target as HTMLSpanElement).childNodes[0], text.length);
  range.collapse(true);
  selection.removeAllRanges();
  selection.addRange(range);
})
```
