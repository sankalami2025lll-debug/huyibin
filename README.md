<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no" />
  <meta name="apple-mobile-web-app-capable" content="yes" />
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
  <meta name="apple-mobile-web-app-title" content="生日祝福" />
  <meta name="theme-color" content="#f43f5e" />
  <title>生日祝福便签</title>

  <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
  <script src="https://cdn.tailwindcss.com"></script>

  <style>
    *{box-sizing:border-box;margin:0;padding:0}
    body{width:100vw;height:100vh;background:#fef2f2;display:flex;align-items:center;justify-content:center;font-family:"PingFang SC","Microsoft YaHei",sans-serif;overflow:hidden;-webkit-font-smoothing:antialiased}
    .app-container{width:100%;max-width:540px;padding-bottom:177.78%;position:relative;background:#fef2f2;box-shadow:0 0 20px rgba(0,0,0,.08)}
    .content-wrapper{position:absolute;inset:0;overflow:hidden;display:flex;align-items:center;justify-content:center;flex-direction:column}

    /* music controls */
    .music-toggle{position:fixed;top:12px;right:12px;z-index:9999;width:44px;height:44px;border-radius:50%;background:rgba(255,255,255,.95);display:flex;align-items:center;justify-content:center;box-shadow:0 6px 18px rgba(0,0,0,.12);cursor:pointer}
    .music-toggle .fa{color:#ef476f;font-size:18px}
    .music-toggle[aria-pressed="true"]{background:rgba(244,63,94,.06)}

    /* center play overlay (for manual start fallback) */
    .play-overlay{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;z-index:9998;pointer-events:none}
    .play-button{pointer-events:auto;background:rgba(255,255,255,.95);border-radius:999px;padding:14px 18px;display:flex;align-items:center;justify-content:center;box-shadow:0 6px 20px rgba(0,0,0,.12);cursor:pointer}
    .play-button .fa{color:#ef476f;font-size:22px}
    .hidden{display:none !important}

    /* minimal styles for demo content */
    .poster-image{width:100%;height:100%;object-fit:cover}
    .start-screen{position:absolute;inset:0;display:flex;flex-direction:column;align-items:center;justify-content:center;z-index:100}
    .tap-hint{margin-top:18px;color:#f43f5e;font-weight:600}
  </style>
</head>
<body>
  <!-- Audio: use Pages same-origin absolute path for reliability -->
  <audio id="bgm" preload="auto" loop>
    <source id="bgmSource" src="/huyibin/assets/music/zhuyiting-happy-birthday.mp3" type="audio/mpeg">
    你的浏览器不支持 audio。
  </audio>

  <!-- Music toggle in corner -->
  <button id="musicToggle" class="music-toggle" aria-pressed="false" aria-label="播放背景音乐">
    <i class="fa fa-play" aria-hidden="true"></i>
  </button>

  <!-- Center overlay with large play button (shown when autoplay blocked) -->
  <div id="playOverlay" class="play-overlay">
    <div id="playBtn" class="play-button" title="点击播放音乐">
      <i class="fa fa-play"></i>
    </div>
  </div>

  <div class="app-container">
    <div class="content-wrapper">
      <div class="start-screen" id="startScreen">
        <div style="width:90%;height:60%;max-width:420px;max-height:420px;overflow:hidden;border-radius:12px;box-shadow:0 10px 30px rgba(0,0,0,.08)">
          <img class="poster-image" src="https://p11-flow-imagex-download-sign.byteimg.com/tos-cn-i-a9rns2rl98/52b81e89d3a94b08b1e2bd85bcc55327.png~tplv-a9rns2rl98-24:720:720.png?rcl=202512041331286744A76CAE520EA334AF&rk3s=8e244e95&rrcfp=8a172a1a&x-expires=1765431089&x-signature=RqMDJLeGhNJzbExPgkO5myNsRAA%3D" alt="生日快乐">
        </div>
        <p class="tap-hint">点击屏幕开始惊喜</p>
      </div>
    </div>
  </div>

  <script>
    (function () {
      const bgm = document.getElementById('bgm');
      const musicToggle = document.getElementById('musicToggle');
      const musicIcon = musicToggle.querySelector('.fa');
      const playOverlay = document.getElementById('playOverlay');
      const playBtn = document.getElementById('playBtn');
      const startScreen = document.getElementById('startScreen');

      // helpers
      function setPlayingUI(playing) {
        if (playing) {
          musicToggle.setAttribute('aria-pressed', 'true');
          musicIcon.classList.remove('fa-play'); musicIcon.classList.add('fa-pause');
          playOverlay.classList.add('hidden');
        } else {
          musicToggle.setAttribute('aria-pressed', 'false');
          musicIcon.classList.remove('fa-pause'); musicIcon.classList.add('fa-play');
        }
      }

      function savePref(paused) {
        try { localStorage.setItem('bgmPaused', paused ? 'true' : 'false'); } catch (e) {}
      }

      // attempt to play (returns promise or throws)
      function tryPlay() {
        if (!bgm) return Promise.reject(new Error('no audio'));
        bgm.volume = 0.9;
        return bgm.play();
      }

      // Initial state from localStorage
      try {
        const pref = localStorage.getItem('bgmPaused');
        if (pref === 'true') {
          // leave paused until user toggles
          setPlayingUI(false);
        }
      } catch (e) {}

      // update UI when audio events fire
      bgm.addEventListener('play', () => setPlayingUI(true));
      bgm.addEventListener('pause', () => setPlayingUI(false));
      bgm.addEventListener('ended', () => setPlayingUI(false));
      bgm.addEventListener('error', (e) => {
        console.warn('audio error', bgm.error);
        // show overlay to let user try manually
        playOverlay.classList.remove('hidden');
      });

      // corner toggle click (stop propagation so it doesn't trigger start)
      musicToggle.addEventListener('click', (e) => {
        e.stopPropagation();
        if (bgm.paused) {
          tryPlay().then(() => {
            setPlayingUI(true); savePref(false);
          }).catch(err => {
            console.warn('play rejected', err);
            // show big overlay for manual start
            playOverlay.classList.remove('hidden');
          });
        } else {
          bgm.pause();
          setPlayingUI(false);
          savePref(true);
        }
      });

      // big play button click
      playBtn.addEventListener('click', (e) => {
        e.stopPropagation();
        tryPlay().then(() => {
          setPlayingUI(true); savePref(false);
        }).catch(err => {
          console.warn('big play rejected', err);
          alert('无法自动播放，请在浏览器中允许媒体播放或尝试刷新页面后再次点击。');
        });
      });

      // attempt to play on first user interaction (click/touch/keydown)
      function onFirstGesture() {
        // only try once
        document.removeEventListener('click', onFirstGesture);
        document.removeEventListener('touchend', onFirstGesture);
        document.removeEventListener('keydown', onFirstGesture);

        // if user preference was paused, skip autoplay
        let prefPaused = false;
        try { prefPaused = localStorage.getItem('bgmPaused') === 'true'; } catch (e) {}

        if (!prefPaused) {
          tryPlay().then(() => {
            setPlayingUI(true);
          }).catch(err => {
            // autoplay blocked -> show overlay button for manual play
            console.warn('autoplay blocked', err);
            playOverlay.classList.remove('hidden');
          });
        } else {
          // user previously paused -> show overlay so they can resume manually
          playOverlay.classList.remove('hidden');
        }
      }

      document.addEventListener('click', onFirstGesture, { passive: true });
      document.addEventListener('touchend', onFirstGesture, { passive: true });
      document.addEventListener('keydown', onFirstGesture, { passive: true });

      // WeChat JSBridge compatibility: some versions allow play after bridge ready
      function onWeixinBridgeReady() {
        try {
          if (typeof WeixinJSBridge !== 'undefined') {
            // try play once WeixinJSBridgeReady fires if user hasn't prevented autoplay
            let prefPaused = false;
            try { prefPaused = localStorage.getItem('bgmPaused') === 'true'; } catch (e) {}
            if (!prefPaused) {
              tryPlay().then(() => setPlayingUI(true)).catch(() => {});
            }
          }
        } catch (e) {}
      }
      if (typeof WeixinJSBridge === 'undefined') {
        document.addEventListener('WeixinJSBridgeReady', onWeixinBridgeReady, false);
      } else {
        onWeixinBridgeReady();
      }

      // If user clicks the start screen area, keep original behavior for the app:
      startScreen.addEventListener('click', function onceStart() {
        startScreen.classList.add('hidden'); // hide start screen content
      }, { once: true });

      // Expose for debugging from console
      window.__bgm = bgm;
      window.__setPlayingUI = setPlayingUI;

      // If audio source is not reachable, show overlay and log useful info
      function checkAudioReachable() {
        const src = document.getElementById('bgmSource')?.src || '';
        // do a HEAD fetch to check status
        if (!src) return;
        fetch(src, { method: 'HEAD' }).then(resp => {
          if (!resp.ok) {
            console.warn('Audio HEAD failed', resp.status, resp.statusText);
            playOverlay.classList.remove('hidden');
          } else {
            const ct = resp.headers.get('content-type') || '';
            if (!ct.includes('audio')) {
              console.warn('Audio content-type suspicious:', ct);
            }
          }
        }).catch(err => {
          console.warn('Audio HEAD fetch error', err);
          playOverlay.classList.remove('hidden');
        });
      }
      // check audio on load
      window.addEventListener('load', checkAudioReachable);

    })();
  </script>
</body>
</html>
