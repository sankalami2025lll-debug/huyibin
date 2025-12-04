[love_notes.html](https://github.com/user-attachments/files/23925718/love_notes.html)
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="format-detection" content="telephone=no">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="生日祝福">
    <meta name="theme-color" content="#f43f5e">
    <title>生日祝福便签</title>
    
    <!-- 预加载关键资源 -->
    <link rel="preload" href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" as="style">
    
    <!-- Font Awesome -->
    <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            width: 100vw;
            height: 100vh;
            background-color: #fef2f2; /* 更柔和的粉色背景，与整体风格一致 */
            cursor: pointer;
            overflow: hidden;
            position: relative;
            display: flex;
            align-items: center;
            justify-content: center;
            -webkit-tap-highlight-color: transparent;
            touch-action: manipulation;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
        }
        
        /* 9:16比例的主容器 */
        .app-container {
            width: 100%;
            max-width: 540px; /* 标准手机宽度 */
            height: 0;
            padding-bottom: 177.78%; /* 9:16 = 16/9 ≈ 177.78% */
            position: relative;
            background-color: #fef2f2;
            overflow: hidden;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.1);
        }
        
        /* 内容容器 */
        .content-wrapper {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
        }

        /* 加载动画样式 */
        .loading-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            background-color: #fef2f2;
            z-index: 9999;
            transition: opacity 0.5s ease;
        }
        
        .loading-spinner {
            width: 50px;
            height: 50px;
            border: 4px solid rgba(244, 63, 94, 0.2);
            border-top: 4px solid #f43f5e;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin-bottom: 20px;
        }
        
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        
        .loading-text {
            font-family: 'PingFang SC', 'Microsoft YaHei', sans-serif;
            font-size: 18px;
            color: #f43f5e;
            font-weight: 500;
        }
        
        .start-screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            background-color: #fef2f2; /* 与body背景色保持一致 */
            z-index: 100;
            transition: opacity 0.8s ease;
        }
        
        /* 生日海报样式 */
        .birthday-poster {
            position: relative;
            width: 100%;
            height: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
            overflow: hidden;
        }
        
        .poster-image {
            width: 100%;
            height: 100%;
            object-fit: cover;
            transition: transform 0.6s ease;
        }
        
        .birthday-poster:hover .poster-image {
            transform: scale(1.05);
        }
        
        /* 周围的心形装饰 */
        .floating-hearts {
            position: absolute;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 3;
        }
        
        .floating-heart {
            position: absolute;
            width: 20px;
            height: 20px;
            background-color: #f43f5e;
            transform: rotate(45deg);
            opacity: 0.6;
            animation: float 4s ease-in-out infinite;
        }
        
        .floating-heart:before,
        .floating-heart:after {
            content: "";
            position: absolute;
            width: 20px;
            height: 20px;
            background-color: inherit;
            border-radius: 50%;
        }
        
        .floating-heart:before {
            top: -10px;
            left: 0;
        }
        
        .floating-heart:after {
            top: 0;
            left: -10px;
        }
        
        /* 浮动心形的位置和动画延迟 */
        .float-heart-1 { top: 15%; left: 10%; animation-delay: 0s; }
        .float-heart-2 { top: 25%; right: 15%; animation-delay: -1s; }
        .float-heart-3 { bottom: 20%; left: 20%; animation-delay: -2s; }
        .float-heart-4 { bottom: 30%; right: 10%; animation-delay: -3s; }
        .float-heart-5 { top: 50%; left: 5%; animation-delay: -1.5s; }
        
        /* 点击提示文字 */
        .tap-hint {
            margin-top: 40px;
            font-family: 'PingFang SC', 'Microsoft YaHei', sans-serif;
            font-size: 18px;
            color: #f43f5e;
            font-weight: 500;
            text-align: center;
            animation: bounce 2s ease-in-out infinite;
        }
        
        @keyframes bounce {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }
        
        /* 粒子效果 */
        .particle {
            position: fixed;
            width: 8px;
            height: 8px;
            background-color: #f43f5e;
            border-radius: 50%;
            pointer-events: none;
            opacity: 0;
            z-index: 999;
        }
        
        /* 3D气球效果 */
        .love-note {
            position: absolute;
            width: clamp(80px, 30vw, 120px);
            height: clamp(100px, 35vw, 150px);
            background-color: #f43f5e;
            border-radius: 50% 50% 50% 50% / 40% 40% 60% 60%;
            transform: scale(0) translateZ(0);
            display: flex;
            align-items: center;
            justify-content: center;
            opacity: 0;
            transition: all 0.6s cubic-bezier(0.17, 0.89, 0.32, 1.49);
            z-index: 10;
            box-shadow: 
                0 15px 30px rgba(0, 0, 0, 0.2),
                inset -5px -5px 20px rgba(0, 0, 0, 0.15),
                inset 5px 5px 20px rgba(255, 255, 255, 0.3);
            animation: float 3s ease-in-out infinite;
            will-change: transform;
            perspective: 1000px;
            overflow: hidden;
        }
        
        /* 气球高光效果 */
        .love-note::before {
            content: '';
            position: absolute;
            top: 10%;
            left: 15%;
            width: 40%;
            height: 40%;
            background: radial-gradient(circle, rgba(255,255,255,0.6) 0%, rgba(255,255,255,0) 70%);
            border-radius: 50%;
            transform: translateZ(1px);
            z-index: 1;
        }
        
        /* 气球绳子 - 50cm长度 */
        .balloon-string {
            position: absolute;
            top: 100%;
            left: 50%;
            transform: translateX(-50%);
            width: 2px;
            height: 50px;
            background: linear-gradient(to bottom, rgba(0,0,0,0.4), transparent);
            z-index: 1;
        }
        
        /* 最终大气球的特殊3D效果 */
        .final-note {
            width: 180px !important;
            height: 220px !important;
            box-shadow: 
                0 20px 40px rgba(0, 0, 0, 0.3),
                inset -8px -8px 30px rgba(0, 0, 0, 0.2),
                inset 8px 8px 30px rgba(255, 255, 255, 0.4);
            animation: float 4s ease-in-out infinite;
        }
        
        .final-note::before {
            top: 8%;
            left: 12%;
            width: 45%;
            height: 45%;
            background: radial-gradient(circle, rgba(255,255,255,0.7) 0%, rgba(255,255,255,0) 70%);
        }
        
        .final-note::after {
            bottom: -50px;
            width: 3px;
            height: 50px;
            background: linear-gradient(to bottom, rgba(0,0,0,0.4), transparent);
        }
        
        .love-note.show {
            transform: scale(1) translateZ(0);
            opacity: 1;
        }
        
        /* 气球文字 - 根据背景色自动选择文字颜色 */
        .note-text {
            font-family: 'PingFang SC', 'Microsoft YaHei', sans-serif;
            font-size: clamp(12px, 4vw, 16px);
            font-weight: 600;
            text-align: center;
            line-height: 1.4;
            padding: 10px;
            text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.2);
            z-index: 11;
            position: relative;
            width: 90%;
            max-height: 70%;
            overflow: visible;
            white-space: normal;
            word-wrap: break-word;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        
        /* 浮动动画已移除，使用JavaScript控制 */
        
        /* 不同颜色的爱心便签 - 与海报风格保持一致 */
        .color-1 { background-color: #f43f5e; } /* 深粉色 */
        .color-2 { background-color: #ec4899; } /* 亮粉色 */
        .color-3 { background-color: #fbbf24; } /* 金色 */
        .color-4 { background-color: #fcd34d; } /* 浅金色 */
        .color-5 { background-color: #ffffff; } /* 白色 */
        
        /* 生日海报样式 */
        .birthday-poster {
            position: relative;
            width: 100%;
            height: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
            overflow: hidden;
        }
        
        .poster-image {
            width: 100%;
            height: 100%;
            object-fit: cover;
            transition: transform 0.6s ease;
        }
        
        .birthday-poster:hover .poster-image {
            transform: scale(1.05);
        }
        
        /* 最终的大爱心便签 */
        .final-note {
            width: 150px !important;
            height: 150px !important;
            z-index: 20;
            box-shadow: 0 8px 25px rgba(236, 72, 153, 0.4);
            animation: pulse 2s ease-in-out infinite;
        }
        
        .final-text {
            font-size: 18px !important;
            width: 90% !important;
            font-weight: bold;
            text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.3); /* 文字阴影 */
        }
        
        @keyframes pulse {
            0% {
                box-shadow: 0 0 20px rgba(236, 72, 153, 0.6);
            }
            50% {
                box-shadow: 0 0 30px rgba(236, 72, 153, 0.9);
            }
            100% {
                box-shadow: 0 0 20px rgba(236, 72, 153, 0.6);
            }
        }
    </style>
</head>
<body>
    <!-- 加载动画 -->
    <div class="loading-screen" id="loadingScreen">
        <div class="loading-spinner"></div>
        <p class="loading-text">正在准备惊喜...</p>
    </div>
    
    <!-- 微信兼容性提示 -->
    <div id="wechat-tip" class="fixed top-0 left-0 w-full bg-pink-500 text-white text-center py-2 text-sm z-200">
        点击屏幕开始 ❤️
    </div>
    
    <div class="app-container">
        <div class="content-wrapper">
            <div class="start-screen opacity-0 pointer-events-none" id="startScreen">
                <!-- 直接使用参考图作为开始画面 -->
                <div class="birthday-poster" id="birthdayPoster">
                    <img src="https://p11-flow-imagex-download-sign.byteimg.com/tos-cn-i-a9rns2rl98/52b81e89d3a94b08b1e2bd85bcc55327.png~tplv-a9rns2rl98-24:720:720.png?rcl=202512041331286744A76CAE520EA334AF&rk3s=8e244e95&rrcfp=8a172a1a&x-expires=1765431089&x-signature=RqMDJLeGhNJzbExPgkO5myNsRAA%3D" 
                         alt="生日快乐" 
                         class="poster-image">
                </div>
                <p class="tap-hint">点击屏幕开始惊喜</p>
            </div>
        </div>
    </div>

    <!-- 音频元素 -->
    <audio id="birthdaySong" loop preload="auto">
        <source src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3" type="audio/mpeg">
    </audio>
    
    <script>
        // 生日祝福消息数组
        const loveMessages = [
            "祝你生日快乐！",
            "愿你的生日充满欢乐",
            "生日快乐！每一天都要开心",
            "愿你的愿望都能实现",
            "生日快乐！爱你一万年",
            "祝你生日快乐！永远年轻",
            "愿你的生日充满惊喜",
            "生日快乐！有你的日子真好",
            "祝你生日快乐！幸福美满",
            "愿你的生日愿望全部实现",
            "祝你生日快乐！有你的日子真好",
            "今天我要给你最温暖的拥抱",
            "生日快乐！你的幸福是我最大的心愿",
            "愿我们一起度过每一个生日",
            "祝你生日快乐！爱你，我的宝贝",
            "生日快乐！愿我们的爱情永远甜蜜",
            "今天的你最美，生日快乐",
            "生日快乐！感谢你选择了我",
            "愿你的生日充满惊喜和感动",
            "祝你生日快乐！我们的爱会越来越深",
            "今天我为你准备了满满的爱",
            "生日快乐！你是我生命中最重要的人",
            "愿你的生日像彩虹一样美丽",
            "祝你生日快乐！我会一直陪在你身边",
            "今天是我们爱情的一个重要里程碑",
            "生日快乐！你的笑容是我的动力",
            "愿你的每一天都像生日一样快乐",
            "祝你生日快乐！爱你到海枯石烂",
            "今天我要对你说无数遍生日快乐",
            "生日快乐！我们的未来会更美好",
            "愿你的生日充满爱与被爱",
            "祝你生日快乐！我会珍惜每一刻",
            "今天是我们共同的纪念日",
            "生日快乐！你是我生命中的阳光",
            "愿你的生日愿望都能成真",
            "祝你生日快乐！我们的爱情故事刚刚开始",
            "今天我要把世界上最好的祝福给你",
            "生日快乐！爱你，我的唯一",
            "愿我们的爱情像美酒一样越陈越香",
            "祝你生日快乐！这是我们的第一个生日，但绝不会是最后一个"
        ];

        const startScreen = document.querySelector('.start-screen');
        const wechatTip = document.getElementById('wechat-tip');
        const body = document.body;
        const contentWrapper = document.querySelector('.content-wrapper');
        const birthdayPoster = document.getElementById('birthdayPoster');
        let hasClicked = false;
        
        // 隐藏微信提示
        setTimeout(() => {
            if (wechatTip) {
                wechatTip.style.opacity = '0';
                setTimeout(() => {
                    wechatTip.style.display = 'none';
                }, 300);
            }
        }, 3000);

        // 点击事件处理
        function handleClick() {
            if (hasClicked) return;
            hasClicked = true;

            // 播放生日歌
            const birthdaySong = document.getElementById('birthdaySong');
            birthdaySong.volume = 0.5; // 设置音量为50%
            
            // 尝试播放音频
            const playPromise = birthdaySong.play();
            
            if (playPromise !== undefined) {
                playPromise.then(() => {
                    console.log('音频播放成功');
                }).catch(error => {
                    console.log('音频播放失败:', error);
                    // 在移动设备上，可能需要用户交互才能播放音频
                    alert('请点击屏幕播放生日歌');
                });
            }

            // 创建粒子效果 - 从海报中心爆发
            createParticles();
            
            // 隐藏开始屏幕
            setTimeout(() => {
                startScreen.style.opacity = '0';
                setTimeout(() => {
                    startScreen.style.display = 'none';
                    
                    // 开始显示气球
                    loveMessages.forEach((message, index) => {
                        setTimeout(() => {
                            createLoveNote(message, index, loveMessages.length);
                        }, index * 150); // 0.15秒间隔，加快显示速度
                    });
                    
                    // 所有便签显示完毕后，显示最终祝福
                    setTimeout(() => {
                        showFinalMessage();
                    }, loveMessages.length * 200 + 500); // 所有便签显示完毕后再等0.5秒，提前0.5秒出现
                }, 800);
            }, 500); // 快速过渡到便签动画
        }
        
        // 创建粒子效果 - 与海报设计匹配
        function createParticles() {
            const particleCount = 100; // 增加粒子数量
            const colors = ['#f43f5e', '#ec4899', '#ffffff', '#fbbf24', '#fcd34d']; // 粉色系、白色和金色粒子
            const shapes = ['circle', 'heart']; // 圆形和心形粒子
            
            // 获取海报的位置
            const posterRect = document.getElementById('birthdayPoster').getBoundingClientRect();
            const boxCenterX = posterRect.left + posterRect.width / 2;
            const boxCenterY = posterRect.top + posterRect.height / 2;
            
            for (let i = 0; i < particleCount; i++) {
                // 创建粒子元素
                const particle = document.createElement('div');
                particle.className = 'particle';
                document.body.appendChild(particle);
                
                // 随机选择形状
                const shape = shapes[Math.floor(Math.random() * shapes.length)];
                
                if (shape === 'heart') {
                    // 创建心形粒子
                    particle.style.width = '12px';
                    particle.style.height = '12px';
                    particle.style.backgroundColor = colors[Math.floor(Math.random() * colors.length)];
                    particle.style.transform = 'rotate(45deg)';
                    
                    // 创建心形的两个半圆
                    const before = document.createElement('div');
                    before.style.content = '';
                    before.style.position = 'absolute';
                    before.style.width = '12px';
                    before.style.height = '12px';
                    before.style.backgroundColor = 'inherit';
                    before.style.borderRadius = '50%';
                    before.style.top = '-6px';
                    before.style.left = '0';
                    
                    const after = document.createElement('div');
                    after.style.content = '';
                    after.style.position = 'absolute';
                    after.style.width = '12px';
                    after.style.height = '12px';
                    after.style.backgroundColor = 'inherit';
                    after.style.borderRadius = '50%';
                    after.style.top = '0';
                    after.style.left = '-6px';
                    
                    particle.appendChild(before);
                    particle.appendChild(after);
                } else {
                    // 圆形粒子
                    const size = Math.random() * 6 + 3;
                    particle.style.width = `${size}px`;
                    particle.style.height = `${size}px`;
                    particle.style.backgroundColor = colors[Math.floor(Math.random() * colors.length)];
                    particle.style.borderRadius = '50%';
                }
                
                // 设置粒子初始位置（礼物盒子中心）
                particle.style.left = `${boxCenterX}px`;
                particle.style.top = `${boxCenterY}px`;
                
                // 随机设置粒子的运动方向和速度
                const angle = Math.random() * Math.PI * 2; // 随机角度
                const speed = Math.random() * 8 + 4; // 增加速度范围
                const vx = Math.cos(angle) * speed;
                const vy = Math.sin(angle) * speed;
                
                // 随机动画持续时间
                const duration = Math.random() * 1500 + 1000; // 1-2.5秒
                
                // 开始动画
                setTimeout(() => {
                    particle.style.opacity = '1';
                    particle.style.transition = `all ${duration}ms ease-out`;
                    
                    // 计算最终位置 - 增加飞行距离
                    const finalX = boxCenterX + vx * 40;
                    const finalY = boxCenterY + vy * 40 - Math.random() * 80; // 向上飞更多
                    
                    particle.style.left = `${finalX}px`;
                    particle.style.top = `${finalY}px`;
                    
                    // 动画结束后淡出并移除粒子
                    setTimeout(() => {
                        particle.style.opacity = '0';
                        setTimeout(() => {
                            if (document.body.contains(particle)) {
                                document.body.removeChild(particle);
                            }
                        }, 300);
                    }, duration);
                }, i * 8); // 更短的发射间隔
            }
        }

        // 创建气球函数 - 重新实现
        function createLoveNote(message, index, total) {
            // 创建元素
            const balloonContainer = document.createElement('div');
            const balloon = document.createElement('div');
            const text = document.createElement('div');
            const string = document.createElement('div');
            
            // 设置类名和内容
            const colorClass = `color-${Math.floor(Math.random() * 5) + 1}`;
            balloonContainer.className = 'balloon-container';
            balloon.className = `love-note ${colorClass}`;
            text.className = 'note-text';
            string.className = 'balloon-string';
            text.textContent = message;
            
            // 组装气球结构
            balloon.appendChild(text);
            balloonContainer.appendChild(balloon);
            balloonContainer.appendChild(string);
            contentWrapper.appendChild(balloonContainer);

            // 设置容器样式
            balloonContainer.style.position = 'absolute';
            balloonContainer.style.zIndex = '10';
            balloonContainer.style.width = '100px';
            balloonContainer.style.height = '150px';
            balloonContainer.style.pointerEvents = 'none';
            balloonContainer.style.transform = 'rotate(0deg)'; // 确保没有旋转

            // 获取气球尺寸
            const balloonWidth = parseInt(getComputedStyle(balloon).width);
            const balloonHeight = parseInt(getComputedStyle(balloon).height);
            
            // 获取容器尺寸
            const containerWidth = contentWrapper.offsetWidth;
            const containerHeight = contentWrapper.offsetHeight;
            
            // 计算网格位置，实现平均分布
            const cols = Math.min(4, Math.ceil(Math.sqrt(total))); // 最多4列
            const rows = Math.ceil(total / cols); // 行数
            
            // 计算每个气球的位置
            const colIndex = index % cols;
            const rowIndex = Math.floor(index / cols);
            
            // 计算水平和垂直间距
            const hSpacing = containerWidth / cols;
            const vSpacing = containerHeight / (rows + 1); // 留出顶部空间
            
            // 计算气球中心位置，确保均匀分布
            const centerX = colIndex * hSpacing + hSpacing / 2;
            const startY = containerHeight + 100; // 从屏幕底部下方开始
            
            // 设置气球初始位置（屏幕底部）
            balloonContainer.style.left = `${centerX - 50}px`; // 50px是容器宽度的一半
            balloonContainer.style.top = `${startY}px`;
            
            // 根据背景颜色自动选择文字颜色
            setTimeout(() => {
                const bgColor = window.getComputedStyle(balloon).backgroundColor;
                const textColor = getContrastColor(bgColor);
                text.style.color = textColor;
                
                // 为浅色背景添加文字阴影，增强可读性
                if (isLightColor(bgColor)) {
                    text.style.textShadow = '1px 1px 2px rgba(0, 0, 0, 0.3)';
                } else {
                    text.style.textShadow = '1px 1px 2px rgba(0, 0, 0, 0.2)';
                }
            }, 10);

            // 触发显示动画 - 从底部向上飘浮
            setTimeout(() => {
                balloon.classList.add('show');
                
                // 计算目标位置（在屏幕内均匀分布）
                const targetY = rowIndex * vSpacing + vSpacing - balloonHeight / 2;
                const floatDistance = startY - targetY;
                
                // 添加飘浮动画
                balloonContainer.style.transition = 'top 2s cubic-bezier(0.25, 0.46, 0.45, 0.94)';
                balloonContainer.style.top = `${startY - floatDistance}px`;
                
                // 飘浮完成后添加轻微浮动效果
                setTimeout(() => {
                    // 添加浮动效果
                    balloonContainer.style.transition = 'none';
                    addFloatAnimation(balloonContainer);
                }, 2000);
            }, 50);
        }
        
        // 判断颜色是否为浅色
        function isLightColor(color) {
            // 将颜色转换为RGB值
            const rgb = color.match(/\d+/g);
            if (!rgb || rgb.length < 3) return false;
            
            const r = parseInt(rgb[0]);
            const g = parseInt(rgb[1]);
            const b = parseInt(rgb[2]);
            
            // 计算亮度 (HSP)
            const brightness = Math.sqrt(
                0.299 * (r * r) +
                0.587 * (g * g) +
                0.114 * (b * b)
            );
            
            return brightness > 127.5;
        }
        
        // 获取对比度颜色（黑或白）
        function getContrastColor(bgColor) {
            return isLightColor(bgColor) ? '#333333' : '#ffffff';
        }

// 添加轻微浮动效果
function addFloatAnimation(element) {
    let time = 0;
    const originalTop = parseInt(element.style.top);
    
    function animate() {
        time += 0.01;
        const floatY = Math.sin(time) * 3; // 只做上下浮动
        element.style.top = `${originalTop + floatY}px`;
        requestAnimationFrame(animate);
    }
    
    animate();
}
        
        // 显示最终祝福信息
        function showFinalMessage() {
            const finalMessage = "生日快乐，愿往后余生皆有你——胡义斌";
            
            // 创建特殊的中央气球
            const finalNote = document.createElement('div');
            const finalText = document.createElement('div');
            
            // 设置类名和内容
            finalNote.className = 'love-note color-2 final-note';
            finalText.className = 'note-text final-text';
            finalText.textContent = finalMessage;
            
            finalNote.appendChild(finalText);
            contentWrapper.appendChild(finalNote);
            
            // 计算中央位置
            const containerWidth = contentWrapper.offsetWidth;
            const containerHeight = contentWrapper.offsetHeight;
            const noteWidth = 180;
            const noteHeight = 220;
            
            // 从底部开始
            finalNote.style.width = `${noteWidth}px`;
            finalNote.style.height = `${noteHeight}px`;
            finalNote.style.left = `${(containerWidth - noteWidth) / 2}px`;
            finalNote.style.top = `${containerHeight + noteHeight}px`;
            
            // 触发显示动画
            setTimeout(() => {
                finalNote.classList.add('show');
                
                // 设置文字颜色（白色，因为最终气球使用深色背景）
                finalText.style.color = '#ffffff';
                finalText.style.textShadow = '1px 1px 2px rgba(0, 0, 0, 0.3)';
                
                // 添加向上飘浮动画
                setTimeout(() => {
                    const targetY = (containerHeight - noteHeight) / 2;
                    const distance = containerHeight + noteHeight - targetY;
                    
                    finalNote.style.transition = 'transform 2.5s cubic-bezier(0.25, 0.46, 0.45, 0.94)';
                    finalNote.style.transform = `translateY(-${distance}px)`;
                    
                    // 飘浮完成后添加摆动效果
                    setTimeout(() => {
                        finalNote.style.transform = `translateY(-${distance}px)`;
                        addFloatAnimation(finalNote);
                    }, 2500);
                }, 100);
            }, 50);
        }
        
        // 页面加载完成后初始化
        document.addEventListener('DOMContentLoaded', function() {
            console.log('页面加载完成，初始化动画...');
            
            // 显示加载动画2秒后再显示开始画面
            setTimeout(function() {
                const loadingScreen = document.getElementById('loadingScreen');
                const startScreen = document.getElementById('startScreen');
                
                console.log('尝试隐藏加载画面，显示开始画面...');
                
                if (loadingScreen && startScreen) {
                    loadingScreen.style.opacity = '0';
                    setTimeout(function() {
                        loadingScreen.style.display = 'none';
                        startScreen.classList.remove('opacity-0', 'pointer-events-none');
                        console.log('开始画面已显示');
                    }, 500);
                } else {
                    console.error('找不到loadingScreen或startScreen元素');
                }
            }, 2000);
        });
        
        // 添加多种点击事件监听，确保在微信中正常工作
        document.addEventListener('click', handleClick);
        document.addEventListener('touchstart', handleClick, { passive: true });
        document.addEventListener('touchend', handleClick, { passive: true });
    </script>
</body>
</html>
