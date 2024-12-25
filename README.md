<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Chatbot</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            background-color: #f4f4f4;
        }
        #chatbox {
            width: 90%;
            max-width: 600px;
            border: 1px solid #ccc;
            border-radius: 8px;
            background: white;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            overflow: hidden;
        }
        #messages {
            height: 300px;
            overflow-y: auto;
            padding: 10px;
        }
        .message {
            margin: 5px 0;
        }
        .bot {
            text-align: left;
            color: blue;
        }
        .user {
            text-align: right;
            color: green;
        }
        #inputSection {
            display: flex;
            border-top: 1px solid #ccc;
        }
        #userInput {
            flex: 1;
            padding: 10px;
            border: none;
            outline: none;
        }
        #sendBtn {
            padding: 10px 20px;
            background-color: blue;
            color: white;
            border: none;
            cursor: pointer;
        }
    </style>
</head>
<body>

    <div id="chatbox">
        <div id="messages"></div>
        <div id="inputSection">
            <input type="text" id="userInput" placeholder="Type your message here...">
            <button id="sendBtn">Send</button>
        </div>
    </div>

    <script>
        const messagesContainer = document.getElementById('messages');
        const userInput = document.getElementById('userInput');
        const sendBtn = document.getElementById('sendBtn');

        // Add message to chat
        function addMessage(message, sender) {
            const messageDiv = document.createElement('div');
            messageDiv.classList.add('message', sender);
            messageDiv.textContent = message;
            messagesContainer.appendChild(messageDiv);
            messagesContainer.scrollTop = messagesContainer.scrollHeight;
        }

        // Send message to Flask backend (which will call OpenAI API)
        async function getBotResponse(userMessage) {
            try {
                const response = await fetch('http://127.0.0.1:5000/chat', {  // Replace with your Flask server URL
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({ message: userMessage }),
                });

                const data = await response.json();
                console.log('Bot Response:', data);  // Check response in console
                return data.reply;
            } catch (error) {
                console.error("Error:", error);
                return "Sorry, I couldn't process your request.";
            }
        }

        // Handle user input
        async function handleUserInput() {
            const userMessage = userInput.value.trim();  // Ensure there's text
            if (userMessage) {
                addMessage(userMessage, 'user');
                const botResponse = await getBotResponse(userMessage);  // Fetch response from the API
                addMessage(botResponse, 'bot');  // Show bot's response
                userInput.value = '';  // Clear input field
            }
        }

        // Event listeners
        sendBtn.addEventListener('click', function() {
            console.log('Send button clicked');
            handleUserInput();
        });

        userInput.addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                console.log('Enter key pressed');
                handleUserInput();
            }
        });
    </script>

</body>
</html>
