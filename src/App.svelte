<script lang="ts">
    import {onMount} from 'svelte';
    import {GlobalWorkerOptions, getDocument, PDFPageProxy} from 'pdfjs-dist';

    GlobalWorkerOptions.workerSrc = 'pdf.worker.min.js';
    let url = 'cv.pdf';
    let canvas: HTMLCanvasElement = null;

    const renderPage = async (page:PDFPageProxy) => {
        const scale = 1.5;
        const viewport = page.getViewport({scale: scale});

        // Prepare canvas using PDF page dimensions
        const context = canvas.getContext('2d');
        canvas.height = viewport.height;
        canvas.width = viewport.width;

        // Render PDF page into canvas context
        const renderTask = page.render({
            canvasContext: context,
            viewport: viewport
        });
        await renderTask.promise;
    }

    onMount(async () => {
        try {
            const loadingTask = await getDocument(url);
            const pdf = await loadingTask.promise
            const page = await pdf.getPage(1);
            await renderPage(page);
        } catch (error) {
            console.error(error);
        }
    });
</script>

<main>
    <canvas bind:this={canvas} id="the-canvas"></canvas>
</main>
