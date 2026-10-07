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
        .canvas-container { margin: 20px auto; display: flex; justify-content: center; }
        canvas { border: 2px dashed #555; background: #111; max-width: 100%; height: auto; image-rendering: pixelated; image-rendering: crisp-edges; }
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

    upload.addEventListener('change', function(e) {
        const file = e.target.files[0];
        if (!file) return;

        const reader = new FileReader();
        reader.onload = function(event) {
            const img = new Image();
            img.onload = function() {
                // Initialize default grid boundaries
                let targetWidth = 96;
                let targetHeight = 96;
                
                // Conforms width and height ratios dynamically to the original image's shape
                if (img.width > img.height) {
                    targetHeight = Math.round((img.height / img.width) * 96);
                } else {
                    targetWidth = Math.round((img.width / img.height) * 96);
                }

                // Force pixel resolution canvas map size
                canvas.width = targetWidth;
                canvas.height = targetHeight;

                // Scale display container scale up visually so the user can easily see it on large monitors
                canvas.style.width = (targetWidth * 4) + 'px';
                canvas.style.height = (targetHeight * 4) + 'px';

                // Disable anti-aliasing to preserve sharp pixel block edges
                ctx.imageSmoothingEnabled = false;
                ctx.mozImageSmoothingEnabled = false;
                ctx.webkitImageSmoothingEnabled = false;
                ctx.msImageSmoothingEnabled = false;

                // Drop pixels down completely to downsampled resolution grid parameters
                ctx.drawImage(img, 0, 0, targetWidth, targetHeight);

                // Setup raw image target pipeline export data source links
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
