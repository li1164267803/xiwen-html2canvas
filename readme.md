项目需求：需要把当前整个页面的数据生成为一个图片，可以让用户长按图片保存到本地手机

在网上查到生成图片的有一个插件，叫做 html2canvas 可以把你想要转变的元素变为图片，开始很顺利，但是生成后的图片，和实际的页面不符合，图片不清晰，模糊的情况，网上大部分的解决方案是通过把 canvas 容器扩大，再将和成的图片进行缩放。这一种可以达到效果，就是麻烦，还有一个方法直接就可以解决，**推荐大家使用第二种方法**

## 第一种方法：（亲测有效----就是麻烦）

```js
// 本地的 html2canvas 版本为  "^0.5.0-beta4",
import html2canvas from 'html2canvas';
   DPR() { // 获取设备dpi
      if (window.devicePixelRatio && window.devicePixelRatio > 1) {
        return window.devicePixelRatio;
      }
      return 1;
    },
    async drawCanvas() {
        await setTimeout(() => {
          // 获取想要转换的 DOM 节点
          const dom = document.querySelector('.report');
          const box = window.getComputedStyle(dom);
          // DOM 节点计算后宽高
          const width = parseInt(box.width, 10);
          const height = parseInt(box.height, 10);
          // 获取像素比
          const scaleBy = this.DPR();
          // 创建自定义 canvas 元素
          const canvas = document.createElement('canvas');
          // 设定 canvas 元素属性宽高为 DOM 节点宽高 * 像素比
          canvas.width = width * scaleBy;
          canvas.height = height * scaleBy;
          // 设定 canvas css宽高为 DOM 节点宽高
          canvas.style.width = `${width}px`;
          canvas.style.height = `${height}px`;
          // 获取画笔
          const context = canvas.getContext('2d');
          // 将所有绘制内容放大像素比倍
          context.scale(scaleBy, scaleBy);
          // 将自定义 canvas 作为配置项传入，开始绘制
          html2canvas(dom, {canvas, background: '#ffffff'}).then((imgDom) => {
            let url= imgDom.toDataURL();
               console.log(url) // 此时的url是图片的base64格式，可直接赋值到img的src上
          })
        }, 2000);
    },
```

## 第二种方法：(亲测有效，方便)

作者在 html2canvas 的源码中添加了两个参数，分别为 scale 和 dpi，scale 是比例，辣么 dpi 是个什么玩意儿，以下是百度百科对 dpi 的定义：

-   DPI 是指每英寸的像素，也就是扫描精度。DPI 越低，扫描的清晰度越低，由于受网络传输速度的影响，web 上使用的图片都是 72dpi，但是冲洗照片不能使用这个参数，必须是 300dpi 或者更高 350dpi。例如要冲洗 46 英寸的照片，扫描精度必须是 300dpi，那么文件尺寸应该是(4300)(6300)=1200 像素\*1800 像素。
    只要增大 dpi 或者 scale 肯定能使同样宽高的图像变得清晰，那么模糊的问题也就不存在了。

| 参数名称 | 类型   | 默认值 | 描述                                   |
| -------- | ------ | ------ | -------------------------------------- |
| scale    | number | 1      | 按比例增加分辨率 (2=双倍).             |
| dpi      | number | 96     | 将分辨率提高到特定的 DPI（每英寸点数） |

## 必须要把文件下载到本地

**[点击此链接手动下载 html2canvas 到本地项目](https://github.com/li1164267803/xiwen-html2canvas)**

## 使用

```javascript
import "@/assets/js/html2canvas.js"; // 引入刚刚自己下载的js文件
// 调用的方法
html2canvas(document.querySelector(".report"), {
    background: "#ffffff", // 一定要添加背景颜色，否则出来的图片，背景全部都是透明的
    dpi: 300,
    onrendered: function (image) {
        let url = image.toDataURL();
        console.log(url);
    },
});
```

## 特别提示 html2canvas 生成图片的坑：

1.  有一些 css 样式没有办法直接识别，比如新增的 css，渐变，或者是 border-radius: 50%; 是没有效果的，必须要写成固定的数值才可以，正确写法：border-radius: 50px
2.  如果你项目的背景色默认是白色的，你自己也没有给项目添加颜色，生成后的图片就是带透明效果，所以要给 html 添加一个白色背景颜色
3.  第一种方法不需要自己单独下载 js 到本地项目中，第二种方法需要单独下载 js 文件
4.  **都看到这里麻烦给点个小赞，在 github 上点个 star，附带使用方法和支持 html2canvas 的源码**👉
    [https://github.com/li1164267803/xiwen-html2canvas](https://github.com/li1164267803/xiwen-html2canvas)
