<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>QR Scanner Bridge</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/html5-qrcode/2.3.8/html5-qrcode.min.js"></script>
<style>
  body { font-family: Arial, sans-serif; background:#111; margin:0; padding:16px; color:#fff; text-align:center; }
  #reader { width:100%; max-width:360px; margin:0 auto; border-radius:8px; overflow:hidden; }
  p { color:#aaa; font-size:14px; }
  #status { margin-top:12px; font-weight:bold; }
</style>
</head>
<body>
  <h3>📷 กำลังเปิดกล้องสแกน QR</h3>
  <p>อนุญาตสิทธิ์กล้องเมื่อเบราว์เซอร์ถาม แล้วเล็งไปที่ QR Code</p>
  <div id="reader"></div>
  <div id="status">กำลังเปิดกล้อง...</div>

  <script>
    const html5QrCode = new Html5Qrcode("reader");
    const statusEl = document.getElementById('status');

    html5QrCode.start(
      { facingMode: "environment" },
      { fps: 10, qrbox: 240 },
      qrText => {
        statusEl.textContent = '✅ สแกนสำเร็จ กำลังปิดหน้าต่าง...';
        html5QrCode.stop().finally(() => {
          if (window.opener) {
            window.opener.postMessage({ type: 'QR_SCAN_SUCCESS', qrCode: qrText }, '*');
          }
          setTimeout(() => window.close(), 500);
        });
      },
      () => { /* เฟรมที่ยังไม่เจอ QR - เงียบไว้ ไม่ต้องแจ้งเตือน */ }
    ).catch(err => {
      statusEl.textContent = '❌ เปิดกล้องไม่สำเร็จ: ' + err;
      if (window.opener) {
        window.opener.postMessage({ type: 'QR_SCAN_ERROR', error: String(err) }, '*');
      }
    });
  </script>
</body>
</html>
