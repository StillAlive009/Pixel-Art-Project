<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>96x96 Pixel Art Converter</title>
    <style>
        body { font-family: 'Courier New', Courier, monospace; background: #222; color: #fff; text-align: center; padding: 20px; }
        .container { max-width: 600px; margin: 0 auto; background: #333; padding: 20px; border-radius: 8px; box-shadow: 0 4px 10px rgba(0,0,0,0.5); }
        input[type="file"] { margin: 20px 0; }
        .btn { display: none; background: #007bff; color: white; padding: 10px 20px; border: none; border-radius: 4px; cursor: pointer; font-size: 16px; margin-top: 15px; text-decoration: none; font-weight: bold; }
        .btn:hover { background: #0056b3; }
        
        /* VIEWER FIX: Scales up the visible monitor view by 3x (96px * 3 = 288px) while locking a 1:1 square ratio */
        .canvas-container { 
            margin: 20px auto; 
            display: flex; 
            justify-content: center; 
            width: 288px !important; 
            height: 288px !important;
        }
        
        canvas { 
            border: 2px dashed #555; 
            background: #111; 
            width: 288px !important; 
            height: 288px !important; 
            display: block;
            /* Forces the browser to keep pixel blocks perfectly sharp when stretched on screen */
            image-rendering: pixelated; 
            image-rendering: crisp-edges; 
        }
    </style>
</head>
<body>

<div class="container">
    <h1>96x96 Pixel Converter</h1>
    <p>Upload an image to convert it into a crisp 96x96 pixel art sprite.</p>
    
    <input type="file" id="upload" accept="image/*">
    
    <div class="canvas-container">
        <canvas id="pixelCanvas"></canvas>
    </div>
    
    <div>
        <a id="downloadBtn" class="btn">Export Pixel Art</a>
    </div>
</div>

<script>
    const upload = document.getElementById('upload');
    const canvas = document.getElementById('pixelCanvas');
    const ctx = canvas.getContext('2d');
    const downloadBtn = document.getElementById('downloadBtn');

    // DATA MATRIX: Keeps internal canvas resolution strictly locked at 96x96 pixels
    canvas.width = 96;
    canvas.height = 96;

    upload.addEventListener('change', function(e) {
        const file = e.target.files;
        if (!file) return;

        const reader = new FileReader();
        reader.onload = function(event) {
            const img = new Image();
            img.onload = function() {
                ctx.clearRect(0, 0, 96, 96);

                // Disable anti-alias blurring filters so the pixels remain crisp
                ctx.imageSmoothingEnabled = false;
                ctx.mozImageSmoothingEnabled = false;
                ctx.webkitImageSmoothingEnabled = false;
                ctx.msImageSmoothingEnabled = false;

                // Center-cropping math to fit any rectangular upload shape into the square layout
                let sourceX = 0;
                let sourceY = 0;
                let sourceSize = Math.min(img.width, img.height);

                if (img.width > img.height) {
                    sourceX = Math.round((img.width - img.height) / 2);
                } else {
                    sourceY = Math.round((img.height - img.width) / 2);
                }

                // Compress image content down to the 96x96 resolution matrix
                ctx.drawImage(
                    img, 
                    sourceX, sourceY, sourceSize, sourceSize,
                    0, 0, 96, 96
                );

                // Format export data download pipelines for a clean 96x96 PNG
                downloadBtn.href = canvas.toDataURL('image/png');
                downloadBtn.download = 'pixel-art-96x96.png';
                downloadBtn.style.display = 'inline-block';
            }
            img.src = event.target.result;
        }
        reader.readAsDataURL(file);
    });
</script>

</body>
</html>
