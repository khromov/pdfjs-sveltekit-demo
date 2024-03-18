<script>
  import * as pdfjs  from "pdfjs-dist";
  import * as pdfjsLib from 'pdfjs-dist/build/pdf.worker.min';

  let pdfText = "";

  function handleFileUpload(event) {
    const file = event.target.files[0];
    const reader = new FileReader();

    reader.onload = () => {
      const result = reader.result;
      if (result instanceof ArrayBuffer) {
        loadPDF(result);
      } else {
        console.error("Invalid file type");
      }
    };

    if (file) {
      reader.readAsArrayBuffer(file);
    }
  }

  async function loadPDF(arrayBuffer) {
    try {
      const loadingTask = pdfjs.getDocument(arrayBuffer);
      const pdf = await loadingTask.promise;
      let fullText = "";
      for (let i = 1; i <= pdf.numPages; i++) {
        const page = await pdf.getPage(i);
        const textContent = await page.getTextContent();
        const pageText = textContent.items.map((item) => item.str).join("");
        fullText += pageText + "\n"; // Assuming each page's text is separated by a newline
      }
      pdfText = fullText;
    } catch (error) {
      console.error("Error loading PDF:", error);
    }
  }
</script>

<input type="file" accept=".pdf" on:change={handleFileUpload} />

<div>
  <pre>{pdfText}</pre>
</div>
