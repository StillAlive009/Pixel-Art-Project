<script>
    const upload = document.getElementById('upload');
    const canvas = document.getElementById('pixelCanvas');
    const ctx = canvas.getContext('2d');
    const downloadBtn = document.getElementById('downloadBtn');

    // Force the physical resolution grid to exactly 96x96 pixels permanently
    canvas.width = 96;
    canvas.height = 96;

    // Scale display size visually (384x384 area) so you can easily see the pixels
    canvas.style.width = '384px';
    canvas.style.height = '384px';

    upload.addEventListener('change', function(e) {
        const file = e.target.files[0];
        if (!file) return;

        const reader = new FileReader();
        reader.onload = function(event) {
            const img = new Image();
            img.onload = function() {
                // Clear any previous drawings out of the fixed grid
                ctx.clearRect(0, 0, 96, 96);

                // Disable anti-aliasing to preserve clean, crisp pixel block edges
                ctx.imageSmoothingEnabled = false;
                ctx.mozImageSmoothingEnabled = false;
                ctx.webkitImageSmoothingEnabled = false;
                ctx.msImageSmoothingEnabled = false;

                // Calculate center-cropping parameters so the image conforms to the square grid
                let sourceX = 0;
                let sourceY = 0;
                let sourceSize = Math.min(img.width, img.height);

                // Find the center point coordinates of the uploaded image
                if (img.width > img.height) {
                    sourceX = Math.round((img.width - img.height) / 2);
                } else {
                    sourceY = Math.round((img.height - img.width) / 2);
                }

                // Slice a perfect square out of the image center and scale it down to the 96x96 canvas grid
                ctx.drawImage(
                    img, 
                    sourceX, sourceY, sourceSize, sourceSize, // Source square
                    0, 0, 96, 96                              // Destination square
                );

                // Setup export pipeline download logic
                downloadBtn.href = canvas.toDataURL('image/png');
                downloadBtn.download = 'pixel-art-96x96.png';
                downloadBtn.style.display = 'inline-block';
            }
            img.src = event.target.result;
        }
        reader.readAsDataURL(file);
    });
</script>
