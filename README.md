<script>
    const upload = document.getElementById('upload');
    const canvas = document.getElementById('pixelCanvas');
    const ctx = canvas.getContext('2d');
    const downloadBtn = document.getElementById('downloadBtn');

    upload.addEventListener('change', function(e) {
        const file = e.target.files;
        if (!file) return;

        const reader = new FileReader();
        reader.onload = function(event) {
            const img = new Image();
            img.onload = function() {
                // FIXED: Force a strict 96x96 square grid matrix
                const targetWidth = 96;
                const targetHeight = 96;

                // Lock physical resolution of the canvas file grid
                canvas.width = targetWidth;
                canvas.height = targetHeight;

                // Scale layout up visually (400x400 display area) so it's readable on high-res monitors
                canvas.style.width = '384px';
                canvas.style.height = '384px';

                // Disable anti-aliasing to preserve clean, crisp pixel block edges
                ctx.imageSmoothingEnabled = false;
                ctx.mozImageSmoothingEnabled = false;
                ctx.webkitImageSmoothingEnabled = false;
                ctx.msImageSmoothingEnabled = false;

                // Draw and squash/stretch the source image into the exact 96x96 frame
                ctx.drawImage(img, 0, 0, targetWidth, targetHeight);

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
