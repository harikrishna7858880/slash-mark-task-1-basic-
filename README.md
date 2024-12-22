# slash-mark-task-1-basic-
BASIC Project: Task 1 Text Encryption Using Cryptographic Algorithms  Description: You can build a simple web application to encrypt and decrypt textual information that the user keys in. Remember that strong encryption should produce different outputs even given the same input.



To build a simple web application for text encryption and decryption, here’s how you can use your existing encryption module and Node.js:

Step 1: Backend Setup
Install Required Dependencies
Run the following command to set up a Node.js project:


npm init -y
npm install express body-parser dotenv crypto
express: For creating the server.
body-parser: To parse JSON input from the client.
dotenv: To manage environment variables securely.
Backend Code (server.js)
Create a server.js file to set up the backend:

javascript

'use strict';

require('dotenv').config();
const express = require('express');
const bodyParser = require('body-parser');
const { encrypt, decrypt } = require('./encryption');

const app = express();
const PORT = process.env.PORT || 3000;

app.use(bodyParser.json());

// Routes
app.post('/encrypt', (req, res) => {
    const { text } = req.body;
    if (!text) {
        return res.status(400).json({ error: 'Text is required!' });
    }

    try {
        const encrypted = encrypt(text);
        res.json({ encrypted });
    } catch (err) {
        res.status(500).json({ error: 'Encryption failed!' });
    }
});

app.post('/decrypt', (req, res) => {
    const { encrypted } = req.body;
    if (!encrypted) {
        return res.status(400).json({ error: 'Encrypted text is required!' });
    }

    try {
        const decrypted = decrypt(encrypted);
        res.json({ decrypted });
    } catch (err) {
        res.status(500).json({ error: 'Decryption failed!' });
    }
});

// Start server
app.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
});
Environment Variables
Create a .env file for your encryption key:

makefile

ENCRYPTION_KEY=YOUR_32_CHARACTER_KEY
Replace YOUR_32_CHARACTER_KEY with a 32-character random string.

Step 2: Frontend Setup
Simple Frontend Code (index.html)
Create a file index.html for the user interface:

html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Text Encryption</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        textarea, input {
            width: 100%;
            margin: 10px 0;
        }
        button {
            padding: 10px;
            font-size: 16px;
        }
    </style>
</head>
<body>
    <h1>Text Encryption and Decryption</h1>
    <h2>Encrypt Text</h2>
    <textarea id="plaintext" placeholder="Enter text to encrypt"></textarea>
    <button onclick="encryptText()">Encrypt</button>
    <p><strong>Encrypted:</strong></p>
    <input type="text" id="encrypted" readonly>

    <h2>Decrypt Text</h2>
    <textarea id="encryptedInput" placeholder="Enter text to decrypt"></textarea>
    <button onclick="decryptText()">Decrypt</button>
    <p><strong>Decrypted:</strong></p>
    <input type="text" id="decrypted" readonly>

    <script>
        async function encryptText() {
            const text = document.getElementById('plaintext').value;
            if (!text) {
                alert('Please enter text to encrypt!');
                return;
            }

            const response = await fetch('/encrypt', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ text })
            });
            const data = await response.json();
            if (data.error) {
                alert(data.error);
            } else {
                document.getElementById('encrypted').value = data.encrypted;
            }
        }

        async function decryptText() {
            const encrypted = document.getElementById('encryptedInput').value;
            if (!encrypted) {
                alert('Please enter encrypted text!');
                return;
            }

            const response = await fetch('/decrypt', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ encrypted })
            });
            const data = await response.json();
            if (data.error) {
                alert(data.error);
            } else {
                document.getElementById('decrypted').value = data.decrypted;
            }
        }
    </script>
</body>
</html>
Step 3: Running the Application
Start the backend server:


node server.js
Open the index.html file in a browser. If you’re using the same machine, serve it via a static file server or move it to a hosted environment.

Test encryption and decryption by entering text and observing the outputs.

Features and Benefits
Secure Encryption: Uses AES-256-CBC for strong encryption.
Random IV: Ensures different encrypted outputs for the same input.
User-Friendly UI: A simple, intuitive frontend for testing the functionality.
Extensible: Easily scalable to include more cryptographic algorithms.
