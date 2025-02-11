---
title: 一言
comment: 'giscus'
banner_img_height: 50
---

一些博主本人比较喜欢的句子。

刷新可以换一批。

未对浅色模式进行适配，请通过右上角的小月亮打开深色模式~

<style>
    @font-face {
      font-family: 'qkbys';
      src: url('/assets/qkbys.ttf')
    }
    .card {
        max-width: 400px;
        margin: 20px auto;
        padding: 20px;
        background: var(--bg-color, rgba(255, 255, 255, 0.3)); /* 背景颜色，略微透明 */
        backdrop-filter: blur(10px); /* 毛玻璃效果 */
        -webkit-backdrop-filter: blur(10px); /* Safari 支持 */
        border-radius: 10px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        text-align: center;
        font-size: 18px;
        color: #333;
    }
   
    .card .quote {
        font-family: qkbys;
        font-size: 19px;
        /* font-weight: bold; */
        margin-bottom: 14px;
        margin-left: 6px;
        margin-top: 4px;
        color: #c4c6c9;
        text-align: left
    }
    .card .author-work {
        font-size: 16px;
        color: #7f8c8d;
        margin-bottom: 10px;
        display: flex;
        justify-content: right;
        /* text-align: right */
        gap: 10px;
        color: #bdc3c7;
    }
    .card .work {
          font-style: italic; 
          color: #bdc3c7;
    }
    .card .date {
        font-size: 16px;
        color: #95a5a6;
        text-align: right
    }
    .card .highlight {
        color: #e74c3c;
    }
    .card::before {
        content: "";
        position: absolute;
        left: 0;
        top: 0;
        width: 5px; /* 条的宽度 */
        height: 100%;
        background-color: var(--left-bar-color, #000);
        border-radius: 10px
    }
    #cardsContainer {
        display: flex;
        flex-wrap: wrap; /* 允许换行 */
        justify-c20pxontent: center; /* 水平居中 */
        gap: 14px; /* 卡片之间的间距 */
    }

    .card {
        flex: 0 1 calc(50% - 20px); /* 设置卡片的宽度为容器宽度的 50% 减去间距 */
        box-sizing: border-box; /* 包括内边距和边框的宽度计算在内 */
        /* border: 1px solid #ccc; */
        padding: 10px;
        box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    }


  </style>


<div id="cardsContainer">
    <!-- Cards will be inserted here dynamically -->
</div>




<script>
function getRandomColor() {
    const letters = '0123456789ABCDEF';
    let color = '#';
    for (let i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
    }
    return color;
}

function hexToRgb(hex) {
    // 将十六进制颜色转换为 RGB
    const r = parseInt(hex.slice(1, 3), 16);
    const g = parseInt(hex.slice(3, 5), 16);
    const b = parseInt(hex.slice(5, 7), 16);
    return { r, g, b };
}

function rgbaColor(r, g, b, alpha) {
    // 返回带透明度的 rgba 颜色
    return `rgba(${r}, ${g}, ${b}, ${alpha})`;
}

async function fetchPoemData(count = 5) {
    try {
        const response = await fetch(`https://yiyan.vercel.app/api/yiyan?count=${count}`, { mode: 'cors' });
        if (!response.ok) throw new Error('Network response was not ok');
        
        const dataArray = await response.json();

        const cardsContainer = document.getElementById('cardsContainer');
        cardsContainer.innerHTML = '';  // 清空内容

        dataArray.forEach(data => {
            const card = document.createElement('div');
            card.className = 'card';
            const randomColor = getRandomColor();
            const rgbColor = hexToRgb(randomColor);
            const transparentColor = rgbaColor(rgbColor.r, rgbColor.g, rgbColor.b, 0.1);

            card.innerHTML = `
                <div class="quote">${data.content}</div>
                <div class="author-work">
                    <div class="author">${data.author}</div>
                    <div class="work">${data.works}</div>
                </div>
                <div class="date">Upload date: ${data.date}</div>
            `;
            card.style.setProperty('--left-bar-color', randomColor);
            card.style.setProperty('--bg-color', transparentColor);
            // card.querySelector('::before').style.backgroundColor = randomColor;

            cardsContainer.appendChild(card);
        });
    } catch (error) {
        console.error('Error fetching the poem data:', error);
    }
}

// 页面加载时调用API并填充内容
window.onload = () => fetchPoemData(10);  // 例如，调用5个条目

</script>