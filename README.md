<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>96x96 Yeeps Pixel Art Converter</title>
    
    <!-- Evict old file variables out of browser cache memory strings -->
    <meta http-equiv="cache-control" content="no-cache, must-revalidate, post-check=0, pre-check=0" />
    <meta http-equiv="cache-control" content="max-age=0" />
    <meta http-equiv="expires" content="0" />
    <meta http-equiv="pragma" content="no-cache" />

    <style>
        body { font-family: 'Courier New', Courier, monospace; background: #222; color: #fff; text-align: center; padding: 20px; }
        .container { max-width: 600px; margin: 0 auto; background: #333; padding: 20px; border-radius: 8px; box-shadow: 0 4px 10px rgba(0,0,0,0.5); }
        input[type="file"] { margin: 20px 0; }
        .btn { display: none; background: #007bff; color: white; padding: 10px 20px; border: none; border-radius: 4px; cursor: pointer; font-size: 16px; margin-top: 15px; text-decoration: none; font-weight: bold; }
        .btn:hover { background: #0056b3; }
        
        /* 4x Upscaled Square Previewer Frame Box Container Window */
        .canvas-container { 
            margin: 20px auto; 
            display: flex; 
            justify-content: center; 
            width: 384px !important; 
            height: 384px !important;
        }
        
        canvas { 
            border: 2px dashed #555; 
            background: #111; 
            width: 384px !important; 
            height: 384px !important; 
            display: block;
            image-rendering: pixelated; 
            image-rendering: crisp-edges; 
        }
    </style>
</head>
<body>

<div class="container">
    <h1>Yeeps Palette Pixel Converter</h1>
    <p>This was made using AI cause im lazy, so dont expect it to be amazing.</p>
    
    <input type="file" id="upload" accept="image/*">
    
    <div class="canvas-container">
        <canvas id="pixelCanvas"></canvas>
    </div>
    
    <div>
        <a id="downloadBtn" class="btn">Export Yeeps Pixel Art</a>
    </div>
</div>

<script>
    const upload = document.getElementById('upload');
    const canvas = document.getElementById('pixelCanvas');
    const ctx = canvas.getContext('2d');
    const downloadBtn = document.getElementById('downloadBtn');

    // OFFICIAL YEEPS PAINT COLOR PALETTE MATRICES (RGB)
    const YEEPS_PALETTE = [
        { r: 255, g: 255, b: 255 }, // Pure White (Rare)
        { r: 16,  g: 16,  b: 16  }, // Void / Pure Black
        { r: 215, g: 0,   b: 0   }, // Pepper Red / Pure Red
        { r: 139, g: 69,  b: 19  }, // Biome Brown
        { r: 205, g: 133, b: 63  }, // Light Brown
        { r: 101, g: 67,  b: 33  }, // Dark Brown
        { r: 128, g: 0,   b: 128 }, // Purple
        { r: 0,   g: 0,   b: 139 }, // Dark Blue
        { r: 169, g: 169, b: 169 }, // Gray
        { r: 255, g: 165, b: 0   }, // Orange
        { r: 0,   g: 128, b: 0   }, // Green
        { r: 255, g: 223, b: 0   }, // Backrooms / Light Yellow
        { r: 255, g: 218, b: 185 }, // Peach
        { r: 0,   g: 255, b: 255 }, // Cyan
        { r: 255, g: 192, b: 203 }, // Skin Pink
        { r: 210, g: 180, b: 140 }, // Skin Tan
        { r: 30,  g: 144, b: 255 }  // Skin Blue
    ];

    // Distance formula processor to find the nearest matching color block asset
    function getClosestYeepColor(r, g, b) {
        let closestColor = YEEPS_PALETTE[0];
        let minDistance = Infinity;

        for (const color of YEEPS_PALETTE) {
            const distance = Math.sqrt(
                Math.pow(r - color.r, 2) +
                Math.pow(g - color.g, 2) +
                Math.pow(b - color.b, 2)
            );
            if (distance < minDistance) {
                minDistance = distance;
                closestColor = color;
            }
        }
        return closestColor;
    }

    upload.addEventListener('change', function(e) {
        const file = e.target.files[0];
        if (!file) return;

        const reader = new FileReader();
        reader.onload = function(event) {
            const img = new Image();
            img.onload = function() {
                canvas.width = 96;
                canvas.height = 96;
                ctx.clearRect(0, 0, 96, 96);

                ctx.imageSmoothingEnabled = false;

                // Square Center Crop calculations
                let sourceX = 0;
                let sourceY = 0;
                let sourceSize = Math.min(img.width, img.height);
                if (img.width > img.height) {
                    sourceX = Math.round((img.width - img.height) / 2);
                } else {
                    sourceY = Math.round((img.height - img.width) / 2);
                }

                // Initial image downsample processing run
                ctx.drawImage(img, sourceX, sourceY, sourceSize, sourceSize, 0, 0, 96, 96);

                // PALETTE SWAP PASS: Scans pixel data array strings and maps to closest color profile match
                const imgData = ctx.getImageData(0, 0, 96, 96);
                const data = imgData.data;

                for (let i = 0; i < data.length; i += 4) {
                    // Skip mapping transparent pixels out of empty layer sections
                    if (data[i + 3] < 128) {
                        data[i + 3] = 0; 
                        continue;
                    }

                    const closest = getClosestYeepColor(data[i], data[i + 1], data[i + 2]);
                    
                    data[i]     = closest.r; // Red Channel
                    data[i + 1] = closest.g; // Green Channel
                    data[i + 2] = closest.b; // Blue Channel
                    data[i + 3] = 255;       // Force solid alpha channel visibility
                }

                // Inject modified palette data structures back onto workspace display boundaries
                ctx.putImageData(imgData, 0, 0);

                // Prepare file pipeline links for a clean 96x96 PNG export asset
                downloadBtn.href = canvas.toDataURL('image/png');
                downloadBtn.download = 'yeeps-pixel-art-96x96.png';
                downloadBtn.style.display = 'inline-block';
            }
            img.src = event.target.result;
        }
        reader.readAsDataURL(file);
    });
</script>

</body>
</html>
