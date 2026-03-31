<!DOCTYPE html>
<html lang="en" dir="ltr">
<head>
    <meta charset="utf-8">
    <title>Pro QR Code Generator</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&display=swap" rel="stylesheet">
    
    <!-- استدعاء مكتبة توليد الباركود محلياً -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Poppins', sans-serif; }
        body {
            display: flex; padding: 10px; min-height: 100vh;
            align-items: center; justify-content: center;
            background: linear-gradient(135deg, #1e3c72, #2a5298);
        }
        .wrapper {
            width: 100%; max-width: 400px; background: #fff;
            border-radius: 20px; padding: 25px;
            box-shadow: 0 20px 50px rgba(0,0,0,0.3);
            text-align: center;
            max-height: 95vh; overflow-y: auto;
        }
        header h1 { font-size: 22px; margin-bottom: 5px; color: #333; font-weight: 600;}
        header p { font-size: 14px; color: #666; margin-bottom: 20px;}
        .form input {
            width: 100%; height: 50px; border: 2px solid #eee;
            border-radius: 10px; padding: 0 15px; font-size: 16px;
            margin-bottom: 15px; outline: none; transition: 0.3s;
        }
        .form input:focus { border-color: #1e3c72; }
        .form button {
            width: 100%; height: 50px; border: none; border-radius: 10px;
            color: #fff; background: #1e3c72; font-size: 18px; font-weight: 600;
            cursor: pointer; transition: 0.2s;
        }
        .form button:active { transform: scale(0.98); }
        .qr-code {
            display: none; margin-top: 25px; padding-top: 20px;
            border-top: 1px solid #eee; flex-direction: column; align-items: center;
        }
        .wrapper.active .qr-code { display: flex; animation: fadeIn 0.4s ease-in; }
        
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* تنسيق الصورة المولدة محلياً */
        #qr-container canvas, #qr-container img {
            margin: 0 auto;
            background: #fff;
            padding: 10px;
            border: 1px solid #eee;
            border-radius: 10px;
            width: 200px;
        }
        
        .download-btn {
            margin-top: 15px; width: 100%; padding: 12px; border: none;
            border-radius: 10px; color: white; background: #28a745;
            font-size: 16px; font-weight: 600; cursor: pointer; transition: 0.2s;
        }
        .download-btn:active { transform: scale(0.98); }
        .share-btn { background: #007bff; margin-top: 8px; }
    </style>
</head>
<body>

    <div class="wrapper">
        <header>
            <h1>Smart QR Generator</h1>
            <p>Enter text or URL to create and download</p>
        </header>
        <div class="form">
            <input type="text" id="qrInput" placeholder="Enter URL or text here..." spellcheck="false">
            <button id="generateBtn">Generate Code</button>
        </div>
        
        <div class="qr-code">
            <!-- سيتم حقن الباركود هنا بواسطة المكتبة -->
            <div id="qr-container"></div>
            
            <button id="downloadBtn" class="download-btn">Save to Device</button>
            <button id="shareBtn" class="download-btn share-btn">Share Image</button>
        </div>
    </div>
<script>
    const wrapper = document.querySelector(".wrapper"),
          qrInput = document.getElementById("qrInput"),
          generateBtn = document.getElementById("generateBtn"),
          qrContainer = document.getElementById("qr-container"),
          downloadBtn = document.getElementById("downloadBtn"),
          shareBtn = document.getElementById("shareBtn");

    // التحقق الذكي: إخفاء زر المشاركة إذا كان المتصفح/الجهاز لا يدعمه
    try {
        const testFile = new File(["test"], "test.png", { type: "image/png" });
        if (!(navigator.canShare && navigator.canShare({ files: [testFile] }))) {
            shareBtn.style.display = "none";
        }
    } catch (e) {
        shareBtn.style.display = "none";
    }

    // توليد الباركود محلياً
    generateBtn.addEventListener("click", () => {
        let qrValue = qrInput.value.trim();
        if(!qrValue) return alert("Please enter some text first!");
        
        generateBtn.innerText = "Generating...";
        qrContainer.innerHTML = ""; 
        
        new QRCode(qrContainer, {
            text: qrValue,
            width: 300,  
            height: 300,
            colorDark : "#000000",
            colorLight : "#ffffff",
            correctLevel : QRCode.CorrectLevel.H
        });
        
        setTimeout(() => {
            wrapper.classList.add("active");
            generateBtn.innerText = "Generate Code";
        }, 300);
    });

    // التحميل المباشر للباركود
    downloadBtn.addEventListener("click", () => {
        const canvas = qrContainer.querySelector("canvas");
        if (!canvas) return;
        
        const dataURL = canvas.toDataURL("image/png");
        const link = document.createElement("a");
        link.href = dataURL;
        link.download = "My_QRCode.png";
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    });

    // المشاركة 
    shareBtn.addEventListener("click", () => {
        const canvas = qrContainer.querySelector("canvas");
        if (!canvas) return;

        canvas.toBlob(async (blob) => {
            const file = new File([blob], "QR_Code.png", { type: "image/png" });
            try {
                await navigator.share({
                    files: [file],
                    title: 'My QR Code',
                    text: 'Here is the QR code I just generated.'
                });
            } catch (err) {
                console.error("Share error:", err);
            }
        });
    });

    // إخفاء الصندوق عند مسح النص
    qrInput.addEventListener("keyup", () => {
        if(!qrInput.value.trim()) wrapper.classList.remove("active");
    });
</script>

    <script>
        const wrapper = document.querySelector(".wrapper"),
              qrInput = document.getElementById("qrInput"),
              generateBtn = document.getElementById("generateBtn"),
              qrContainer = document.getElementById("qr-container"),
              downloadBtn = document.getElementById("downloadBtn"),
              shareBtn = document.getElementById("shareBtn");

        // توليد الباركود محلياً
        generateBtn.addEventListener("click", () => {
            let qrValue = qrInput.value.trim();
            if(!qrValue) return alert("Please enter some text first!");
            
            generateBtn.innerText = "Generating...";
            qrContainer.innerHTML = ""; // مسح الباركود القديم
            
            // استخدام المكتبة لإنشاء الكود داخل المتصفح (بدون إنترنت)
            new QRCode(qrContainer, {
                text: qrValue,
                width: 300,  // جودة عالية للتحميل
                height: 300,
                colorDark : "#000000",
                colorLight : "#ffffff",
                correctLevel : QRCode.CorrectLevel.H
            });
            
            setTimeout(() => {
                wrapper.classList.add("active");
                generateBtn.innerText = "Generate Code";
            }, 300);
        });

        // التحميل المباشر للباركود
        downloadBtn.addEventListener("click", () => {
            const canvas = qrContainer.querySelector("canvas");
            if (!canvas) return;
            
            // تحويل الباركود إلى رابط محلي لضمان التحميل
            const dataURL = canvas.toDataURL("image/png");
            const link = document.createElement("a");
            link.href = dataURL;
            link.download = "My_QRCode.png";
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        });

        // المشاركة (تعمل بكفاءة لأن الصورة أصبحت محلية)
        shareBtn.addEventListener("click", () => {
            const canvas = qrContainer.querySelector("canvas");
            if (!canvas) return;

            canvas.toBlob(async (blob) => {
                const file = new File([blob], "QR_Code.png", { type: "image/png" });
                if (navigator.canShare && navigator.canShare({ files: [file] })) {
                    try {
                        await navigator.share({
                            files: [file],
                            title: 'My QR Code',
                            text: 'Here is the QR code I just generated.'
                        });
                    } catch (err) {
                        console.error("Share error:", err);
                    }
                } else {
                    alert("Your browser does not support direct sharing. Please use the Save button.");
                }
            });
        });

        // إخفاء الصندوق عند مسح النص
        qrInput.addEventListener("keyup", () => {
            if(!qrInput.value.trim()) wrapper.classList.remove("active");
        });
        
    </script>
</body>
</html>
