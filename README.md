<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced AI Agent | OpenRouter</title>
    <style>
        :root {
            --bg-color: #0d1117;
            --sidebar-color: #010409;
            --sidebar-width: 280px;
            --text-color: #e6edf3;
            --accent-color: #238636; /* Grok/GitHub Green */
            --border-color: #30363d;
            --hover-color: #21262d;
        }

        .light-theme {
            --bg-color: #ffffff;
            --sidebar-color: #f6f8fa;
            --text-color: #1f2328;
            --border-color: #d0d7de;
            --hover-color: #ebf0f4;
        }

        body, html {
            margin: 0; padding: 0;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            height: 100%; overflow: hidden;
        }

        .container { display: flex; height: 100vh; }

        /* --- Sidebar Styles --- */
        .sidebar {
            width: var(--sidebar-width);
            background-color: var(--sidebar-color);
            border-right: 1px solid var(--border-color);
            display: flex;
            flex-direction: column;
            transition: 0.3s;
        }

        .sidebar-header { padding: 20px; }

        .new-chat-btn {
            border: 1px solid var(--border-color);
            padding: 12px;
            border-radius: 8px;
            text-align: center;
            cursor: pointer;
            font-weight: 600;
            background: transparent;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            transition: 0.2s;
        }
        .new-chat-btn:hover { background: var(--hover-color); border-color: #8b949e; }

        .chat-history {
            flex-grow: 1;
            overflow-y: auto;
            padding: 10px;
        }

        .history-item {
            padding: 10px;
            border-radius: 6px;
            margin-bottom: 5px;
            cursor: pointer;
            font-size: 14px;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        .history-item:hover { background: var(--hover-color); }

        .sidebar-footer {
            padding: 15px;
            border-top: 1px solid var(--border-color);
            display: flex;
            flex-direction: column;
            gap: 5px;
        }

        .footer-item {
            padding: 10px;
            border-radius: 6px;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        .footer-item:hover { background: var(--hover-color); }

        /* --- Main Chat Area --- */
        .main-chat {
            flex-grow: 1;
            display: flex;
            flex-direction: column;
            background-color: var(--bg-color);
        }

        header {
            height: 60px;
            padding: 0 20px;
            border-bottom: 1px solid var(--border-color);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        #chat-window {
            flex-grow: 1;
            overflow-y: auto;
            padding: 40px 15% 100px 15%; /* Centered layout */
            display: flex;
            flex-direction: column;
            gap: 25px;
        }

        .message { max-width: 85%; padding: 15px; border-radius: 12px; font-size: 16px; }
        .user-msg { align-self: flex-end; background: var(--accent-color); color: white; box-shadow: 0 4px 12px rgba(0,0,0,0.1); }
        .ai-msg { align-self: flex-start; background: var(--hover-color); border: 1px solid var(--border-color); }

        /* --- Input --- */
        .input-container {
            padding: 20px 15%;
            background: linear-gradient(transparent, var(--bg-color) 30%);
            position: absolute; bottom: 0; width: 70%;
        }

        .input-wrapper {
            display: flex;
            background: var(--sidebar-color);
            border: 1px solid var(--border-color);
            padding: 10px 15px;
            border-radius: 12px;
            align-items: center;
        }

        input[type="text"] {
            flex-grow: 1;
            background: transparent;
            border: none;
            color: var(--text-color);
            padding: 10px;
            font-size: 16px;
            outline: none;
        }

        /* --- Settings Overlay --- */
        #settings-overlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: var(--bg-color);
            display: none; z-index: 1000;
        }

        .settings-content { max-width: 500px; margin: 80px auto; padding: 20px; }
        .back-btn { background: #333; color: white; border: none; padding: 10px 20px; border-radius: 8px; cursor: pointer; }
        .save-btn { background: var(--accent-color); color: white; border: none; width: 100%; padding: 12px; border-radius: 8px; cursor: pointer; font-weight: bold; margin-top: 20px; }
        
        input.settings-input {
            width: 100%; padding: 12px; margin: 10px 0 20px 0;
            background: var(--sidebar-color); border: 1px solid var(--border-color);
            color: white; border-radius: 8px; box-sizing: border-box;
        }
    </style>
</head>
<body>

<div class="container">
    <div class="sidebar">
        <div class="sidebar-header">
            <div class="new-chat-btn" onclick="location.reload()">
                <span>+</span> New Chat
            </div>
        </div>

        <div class="chat-history" id="history-list">
            <div class="history-item">Recent: Hello AI Agent</div>
            <div class="history-item">Recent: DeepSeek API Test</div>
            <div class="history-item">Recent: Grok Settings</div>
        </div>

        <div class="sidebar-footer">
            <div class="footer-item" onclick="toggleTheme()">🌗 Toggle Theme</div>
            <div class="footer-item" onclick="openSettings()">⚙️ Settings & API</div>
            <div class="footer-item" style="opacity: 0.6; font-size: 12px; cursor: default;">
                👤 User: Admin Mode
            </div>
        </div>
    </div>

    <div class="main-chat">
        <header>
            <strong id="model-badge">AI Agent (OpenRouter)</strong>
            <span id="status-dot" style="color: #238636;">● Online</span>
        </header>

        <div id="chat-window">
            <div class="message ai-msg">Swagat hai! Main aapka AI Agent hoon. Neeche message likhein ya Settings se OpenRouter API Key update karein.</div>
        </div>

        <div class="input-container">
            <div class="input-wrapper">
                <input type="text" id="user-input" placeholder="Message your AI..." onkeypress="handleKey(event)" />
                <button onclick="sendMessage()" style="background: var(--accent-color); border: none; border-radius: 8px; color: white; padding: 8px 15px; cursor: pointer;">Send</button>
            </div>
        </div>
    </div>
</div>

<div id="settings-overlay">
    <div class="settings-content">
        <button class="back-btn" onclick="closeSettings()">← Back to Chat</button>
        <h2 style="margin-top: 30px;">Configuration</h2>
        
        <label>OpenRouter API Key</label>
        <input type="password" id="api-key" class="settings-input" placeholder="sk-or-v1-...">

        <label>Active Model ID</label>
        <input type="text" id="model-id" class="settings-input" placeholder="deepseek/deepseek-chat">
        <p style="font-size: 12px; color: #8b949e;">Examples: <br>• <code>deepseek/deepseek-chat</code><br>• <code>x-ai/grok-2</code><br>• <code>openai/gpt-4o</code></p>

        <button class="save-btn" onclick="saveSettings()">Save & Apply</button>
    </div>
</div>

<script>
    // Initialize
    let apiKey = localStorage.getItem('ai_api_key') || '';
    let modelId = localStorage.getItem('ai_model_id') || 'google/gemini-2.0-flash-001';
    
    document.getElementById('api-key').value = apiKey;
    document.getElementById('model-id').value = modelId;
    document.getElementById('model-badge').innerText = modelId.split('/')[1]?.toUpperCase() || modelId;

    function openSettings() { document.getElementById('settings-overlay').style.display = 'block'; }
    function closeSettings() { document.getElementById('settings-overlay').style.display = 'none'; }

    function saveSettings() {
        apiKey = document.getElementById('api-key').value;
        modelId = document.getElementById('model-id').value;
        localStorage.setItem('ai_api_key', apiKey);
        localStorage.setItem('ai_model_id', modelId);
        document.getElementById('model-badge').innerText = modelId.split('/')[1]?.toUpperCase() || modelId;
        alert("Settings Saved!");
        closeSettings();
    }

    function toggleTheme() { document.body.classList.toggle('light-theme'); }
    function handleKey(e) { if (e.key === 'Enter') sendMessage(); }

    async function sendMessage() {
        const inputField = document.getElementById('user-input');
        const chatWindow = document.getElementById('chat-window');
        const text = inputField.value.trim();

        if (!text) return;
        if (!apiKey) { alert("Pehle Settings mein API Key daalein!"); openSettings(); return; }

        chatWindow.innerHTML += `<div class="message user-msg">${text}</div>`;
        inputField.value = '';
        chatWindow.scrollTop = chatWindow.scrollHeight;

        try {
            const response = await fetch("https://openrouter.ai/api/v1/chat/completions", {
                method: "POST",
                headers: {
                    "Authorization": `Bearer ${apiKey}`,
                    "Content-Type": "application/json"
                },
                body: JSON.stringify({
                    "model": modelId,
                    "messages": [{"role": "user", "content": text}]
                })
            });

            const data = await response.json();
            const aiResponse = data.choices[0].message.content;

            chatWindow.innerHTML += `<div class="message ai-msg">${aiResponse}</div>`;
            chatWindow.scrollTop = chatWindow.scrollHeight;
            
            // Add to dummy history
            const historyList = document.getElementById('history-list');
            const newHistory = document.createElement('div');
            newHistory.className = 'history-item';
            newHistory.innerText = text.substring(0, 20) + "...";
            historyList.prepend(newHistory);

        } catch (error) {
            chatWindow.innerHTML += `<div class="message ai-msg" style="color:#ff7b72;">Error: API key check karein ya Model ID sahi likhein.</div>`;
        }
    }
</script>

</body>
</html>
