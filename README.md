<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Radio Web con Chat</title>
    <!-- Enlace a Tailwind CSS para un estilo rápido y responsivo -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Estilos personalizados para el reproductor y el chat */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f0f4f8; /* Un fondo suave */
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px; /* Espaciado general */
            box-sizing: border-box;
        }

        .container {
            display: flex;
            flex-direction: column; /* Columna por defecto para móviles */
            gap: 24px; /* Espacio entre los componentes */
            background-color: #ffffff;
            border-radius: 16px; /* Bordes más redondeados */
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1); /* Sombra más pronunciada */
            padding: 32px;
            max-width: 900px; /* Ancho máximo del contenedor */
            width: 100%;
        }

        @media (min-width: 768px) {
            .container {
                flex-direction: row; /* Fila para pantallas medianas y grandes */
            }
        }

        .radio-player, .chat-box {
            flex: 1; /* Permite que ambos componentes ocupen el espacio disponible */
            padding: 24px;
            border-radius: 12px;
            background-color: #f9fafb; /* Fondo ligeramente diferente para los paneles */
            box-shadow: inset 0 2px 5px rgba(0, 0, 0, 0.05); /* Sombra interna sutil */
        }

        .radio-player h1, .chat-box h2 {
            text-align: center;
            color: #1a202c; /* Color de texto oscuro */
            margin-bottom: 24px;
            font-weight: 700; /* Negrita */
            font-size: 1.75rem; /* Tamaño de fuente más grande */
        }

        audio {
            width: 100%;
            margin-bottom: 20px;
            border-radius: 8px; /* Bordes redondeados para el reproductor de audio */
        }

        .player-controls {
            display: flex;
            flex-direction: column; /* Controles en columna para móviles */
            gap: 16px;
            align-items: center;
        }

        @media (min-width: 640px) {
            .player-controls {
                flex-direction: row; /* Controles en fila para pantallas pequeñas y más grandes */
            }
        }

        button {
            padding: 12px 24px;
            border: none;
            border-radius: 8px;
            background-color: #4c51bf; /* Azul morado */
            color: white;
            cursor: pointer;
            transition: background-color 0.3s ease, transform 0.2s ease;
            font-weight: 600; /* Seminegrita */
            box-shadow: 0 4px 10px rgba(76, 81, 191, 0.3); /* Sombra para el botón */
        }

        button:hover {
            background-color: #3c429a; /* Azul morado más oscuro al pasar el ratón */
            transform: translateY(-2px); /* Pequeño efecto de elevación */
        }

        input[type="range"] {
            flex-grow: 1;
            -webkit-appearance: none;
            width: 100%;
            height: 10px; /* Altura de la barra de volumen */
            background: #cbd5e0; /* Color de fondo de la barra */
            outline: none;
            opacity: 0.9;
            transition: opacity .2s;
            border-radius: 5px;
        }

        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 24px; /* Tamaño del "pulgar" del control de volumen */
            height: 24px;
            border-radius: 50%;
            background: #4c51bf; /* Color del "pulgar" */
            cursor: pointer;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
        }

        input[type="range"]::-moz-range-thumb {
            width: 24px;
            height: 24px;
            border-radius: 50%;
            background: #4c51bf;
            cursor: pointer;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
        }

        .messages {
            flex-grow: 1;
            border: 1px solid #e2e8f0; /* Borde más suave */
            border-radius: 8px;
            padding: 16px;
            margin-bottom: 16px;
            overflow-y: auto;
            background-color: #ffffff;
            max-height: 350px; /* Altura máxima para el historial de chat */
            min-height: 150px; /* Altura mínima para el historial de chat */
            display: flex;
            flex-direction: column;
            gap: 8px; /* Espacio entre mensajes */
        }

        .message {
            padding: 8px 12px;
            border-radius: 8px;
            background-color: #edf2f7; /* Fondo para cada mensaje */
            color: #2d3748; /* Color de texto para mensajes */
            word-wrap: break-word; /* Rompe palabras largas */
        }

        .message strong {
            color: #4c51bf; /* Color del nombre de usuario */
            margin-right: 8px;
        }

        .chat-input-area {
            display: flex;
            gap: 10px;
        }

        .chat-box input[type="text"] {
            flex-grow: 1; /* Ocupa el espacio restante */
            padding: 12px;
            border: 1px solid #cbd5e0;
            border-radius: 8px;
            box-sizing: border-box;
            font-size: 1rem;
            color: #2d3748;
        }

        .chat-box input[type="text"]:focus {
            outline: none;
            border-color: #4c51bf; /* Borde azul al enfocar */
            box-shadow: 0 0 0 3px rgba(76, 81, 191, 0.2);
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- Sección del Reproductor de Radio -->
        <div class="radio-player">
            <h1>Mi Radio Online</h1>
            <audio id="radioStream" controls autoplay class="rounded-lg shadow-md">
                <!--
                    ¡IMPORTANTE! Reemplaza esta URL con la URL real de tu transmisión de radio.
                    Puedes encontrar transmisiones de radio públicas para pruebas buscando "public radio stream URL"
                    o usando servicios como Icecast/Shoutcast.
                    Ejemplo: <source src="http://stream.radioscoop.com/radioscoop-high.mp3" type="audio/mpeg">
                -->
                <source src="https://centova.hostingelectrica.net/proxy/baulmania/stream" type="audio/mpeg">
                Tu navegador no soporta el elemento de audio.
            </audio>
            <div class="player-controls">
                <button id="playPauseBtn" class="flex-shrink-0">Reproducir / Pausar</button>
                <input type="range" id="volumeControl" min="0" max="1" step="0.01" value="0.7">
            </div>
        </div>

        <!-- Sección del Chat -->
        <div class="chat-box flex flex-col">
            <h2>Chat en Vivo</h2>
            <div class="messages" id="chatMessages">
                <!-- Los mensajes del chat aparecerán aquí -->
                <div class="message"><strong>Sistema:</strong> ¡Bienvenido al chat!</div>
            </div>
            <div class="chat-input-area">
                <input type="text" id="messageInput" placeholder="Escribe tu mensaje..." class="rounded-lg">
                <button id="sendBtn" class="flex-shrink-0">Enviar</button>
            </div>
        </div>
    </div>

    <script>
        // --- Lógica del Reproductor de Radio ---
        const radioStream = document.getElementById('radioStream');
        const playPauseBtn = document.getElementById('playPauseBtn');
        const volumeControl = document.getElementById('volumeControl');

        let isPlaying = true; // Asumimos que el autoplay funciona inicialmente

        // Inicializa el texto del botón basado en el estado inicial
        playPauseBtn.textContent = isPlaying ? 'Pausar' : 'Reproducir';

        playPauseBtn.addEventListener('click', () => {
            if (isPlaying) {
                radioStream.pause();
                playPauseBtn.textContent = 'Reproducir';
            } else {
                radioStream.play();
                playPauseBtn.textContent = 'Pausar';
            }
            isPlaying = !isPlaying;
        });

        volumeControl.addEventListener('input', () => {
            radioStream.volume = volumeControl.value;
        });

        // Establece el volumen inicial del reproductor
        radioStream.volume = volumeControl.value;

        // --- Lógica del Chat (Local) ---
        const chatMessages = document.getElementById('chatMessages');
        const messageInput = document.getElementById('messageInput');
        const sendBtn = document.getElementById('sendBtn');

        // Función para añadir un mensaje al display del chat
        function addMessage(user, message) {
            const messageElement = document.createElement('div');
            messageElement.classList.add('message');
            messageElement.innerHTML = `<strong>${user}:</strong> ${message}`;
            chatMessages.appendChild(messageElement);
            // Desplázate al final para mostrar el último mensaje
            chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        // Manejador para el botón de enviar mensaje
        sendBtn.addEventListener('click', () => {
            sendMessage();
        });

        // Manejador para la tecla Enter en el campo de entrada de mensaje
        messageInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                sendMessage();
            }
        });

        // Función para enviar el mensaje
        function sendMessage() {
            const message = messageInput.value.trim();
            if (message) {
                // En un entorno real, el nombre de usuario vendría de una sesión o un prompt.
                // Aquí, lo establecemos como "Tú" para simular tu propio mensaje.
                const username = 'Tú'; // Puedes cambiar esto o pedir al usuario que lo introduzca
                addMessage(username, message); // Añade el mensaje a la interfaz local

                // Limpia el campo de entrada
                messageInput.value = '';

                // NOTA IMPORTANTE: Para un chat multiusuario en tiempo real,
                // necesitarías enviar este mensaje a un servidor (ej. usando WebSockets).
                // Este ejemplo solo lo muestra localmente.
            }
        }
    </script>
</body>
</html>
