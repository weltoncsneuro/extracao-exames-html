<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Extração de Exames Laboratoriais</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
    }
    textarea {
      width: 100%;
      height: 150px;
      margin-bottom: 10px;
    }
  </style>
</head>
<body>
  <h1>Extração de Exames Laboratoriais</h1>
  <p>Faça upload de um arquivo PDF ou cole os resultados na caixa abaixo:</p>

  <!-- Upload de PDF -->
  <label for="pdfUpload">Upload de PDF:</label>
  <input type="file" id="pdfUpload" accept=".pdf"><br><br>

  <!-- Caixa de texto para colar resultados -->
  <textarea id="textInput" placeholder="Cole os resultados aqui..."></textarea>
  
  <!-- Botão de Processamento -->
  <button id="processButton">Processar</button>

  <!-- Resultado da Extração -->
  <h2>Resultados Extraídos:</h2>
  <pre id="output"></pre>

  <!-- Scripts -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.14.305/pdf.min.js"></script>
  <script>
    // Função para processar texto colado
    document.getElementById("processButton").addEventListener("click", () => {
      const textInput = document.getElementById("textInput").value;
      const output = document.getElementById("output");
      if (textInput.trim() !== "") {
        output.textContent = extractResults(textInput);
      } else {
        output.textContent = "Por favor, insira texto ou faça upload de um PDF.";
      }
    });

    // Função para processar o PDF
    document.getElementById("pdfUpload").addEventListener("change", async (event) => {
      const file = event.target.files[0];
      const output = document.getElementById("output");
      if (file) {
        const pdfData = new Uint8Array(await file.arrayBuffer());
        const pdf = await pdfjsLib.getDocument(pdfData).promise;
        let fullText = "";
        for (let i = 1; i <= pdf.numPages; i++) {
          const page = await pdf.getPage(i);
          const textContent = await page.getTextContent();
          fullText += textContent.items.map(item => item.str).join(" ") + "\n";
        }
        document.getElementById("textInput").value = fullText;
        output.textContent = "Texto do PDF carregado na caixa de entrada. Clique em 'Processar'.";
      }
    });

    // Função de exemplo para extrair resultados (customize para suas necessidades)
    function extractResults(text) {
      const results = [];
      const lines = text.split("\n");
      for (const line of lines) {
        if (line.match(/hemoglobina|creatinina|colesterol/i)) { // Exemplos de palavras-chave
          results.push(line.trim());
        }
      }
      return results.length > 0 ? results.join("\n") : "Nenhum resultado relevante encontrado.";
    }
  </script>
</body>
</html>
