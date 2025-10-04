# Hassan.html<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>UID File Editor</title>
  <style>
    body {
      font-family: "Poppins", sans-serif;
      background: #f8f9fa;
      color: #222;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      min-height: 100vh;
      margin: 0;
      padding: 40px 20px;
    }
    h1 {
      font-size: 2em;
      margin-bottom: 20px;
      color: #0078d7;
    }
    .card {
      background: #fff;
      box-shadow: 0 2px 10px rgba(0,0,0,0.1);
      border-radius: 12px;
      padding: 25px;
      max-width: 400px;
      width: 100%;
      text-align: center;
    }
    input[type="file"] {
      margin: 10px 0;
    }
    input[type="number"], input[type="text"] {
      width: 100%;
      padding: 10px;
      margin-top: 10px;
      border: 1px solid #ccc;
      border-radius: 6px;
      font-size: 1em;
    }
    button {
      background: #0078d7;
      color: white;
      border: none;
      padding: 10px 20px;
      border-radius: 6px;
      font-size: 1em;
      cursor: pointer;
      margin-top: 15px;
    }
    button:hover {
      background: #005fa3;
    }
    .result {
      margin-top: 15px;
      padding: 10px;
      background: #e9f5ff;
      border-radius: 6px;
      font-family: monospace;
      font-size: 0.95em;
      text-align: left;
      word-break: break-all;
    }
  </style>
</head>
<body>
  <h1>UID File Editor</h1>
  <div class="card">
    <input type="file" id="fileInput" accept=".meta,.bytes">
    <div id="fileInfo"></div>
    <div class="result" id="uidDisplay" style="display:none;"></div>
    <input type="number" id="newUid" placeholder="Enter new UID" style="display:none;">
    <button id="replaceBtn" style="display:none;">Replace & Download</button>
  </div>

  <script>
    const fileInput = document.getElementById("fileInput");
    const uidDisplay = document.getElementById("uidDisplay");
    const replaceBtn = document.getElementById("replaceBtn");
    const newUidInput = document.getElementById("newUid");

    let originalBuffer;
    let oldUidBytes;

    // Read file
    fileInput.addEventListener("change", async (e) => {
      const file = e.target.files[0];
      if (!file) return;
      const buffer = await file.arrayBuffer();
      originalBuffer = new Uint8Array(buffer);

      // Try to detect UID (simple VarInt-like detection)
      let uidOffset = -1;
      for (let i = 0; i < originalBuffer.length - 4; i++) {
        if (originalBuffer[i] === 0x08 && originalBuffer[i+1] > 0 && originalBuffer[i+1] < 0x80) {
          uidOffset = i + 1;
          break;
        }
      }

      if (uidOffset === -1) {
        uidDisplay.style.display = "block";
        uidDisplay.innerHTML = "⚠️ UID not found.";
        return;
      }

      oldUidBytes = [];
      let value = 0, shift = 0;
      for (let i = uidOffset; i < originalBuffer.length; i++) {
        let byte = originalBuffer[i];
        oldUidBytes.push(byte);
        value |= (byte & 0x7F) << shift;
        if ((byte & 0x80) === 0) break;
        shift += 7;
      }

      uidDisplay.style.display = "block";
      uidDisplay.innerHTML = `Found UID: <b>${value}</b> (0x${value.toString(16).toUpperCase()})`;
      newUidInput.style.display = "block";
      replaceBtn.style.display = "inline-block";
    });

    function encodeVarInt(num) {
      const bytes = [];
      while (num > 127) {
        bytes.push((num & 0x7F) | 0x80);
        num >>>= 7;
      }
      bytes.push(num);
      return bytes;
    }

    replaceBtn.addEventListener("click", () => {
      const newUid = parseInt(newUidInput.value);
      if (isNaN(newUid)) return alert("Please enter a valid UID number!");

      const newUidBytes = encodeVarInt(newUid);
      const output = new Uint8Array(originalBuffer.length - oldUidBytes.length + newUidBytes.length);

      let uidStart = originalBuffer.indexOf(oldUidBytes[0]);
      output.set(originalBuffer.slice(0, uidStart), 0);
      output.set(newUidBytes, uidStart);
      output.set(originalBuffer.slice(uidStart + oldUidBytes.length), uidStart + newUidBytes.length);

      const blob = new Blob([output], { type: "application/octet-stream" });
      const link = document.createElement("a");
      link.href = URL.createObjectURL(blob);
      link.download = "edited_" + (fileInput.files[0]?.name || "file.bytes");
      link.click();
    });
  </script>
</body>
</html>
