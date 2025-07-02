<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Radio Web con Chat</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Inter Font -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f0f2f5; /* Light gray background */
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
        }
        .container {
            background-color: #ffffff;
            border-radius: 1.5rem; /* More rounded corners */
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1); /* Softer shadow */
            overflow: hidden;
            display: flex;
            flex-direction: column;
            max-width: 90%;
            width: 1000px; /* Increased max-width for better layout */
            min-height: 700px; /* Minimum height for better appearance */
        }
        @media (min-width: 768px) {
            .container {
                flex-direction: row;
            }
        }
        .radio-section, .chat-section {
            padding: 2.5rem; /* More padding */
            flex: 1;
        }
        .radio-section {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); /* Gradient background */
            color: white;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            text-align: center;
        }
        .chat-section {
            background-color: #f9fafb; /* Lighter background for chat */
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }
        .messages-display {
            flex-grow: 1;
            overflow-y: auto;
            border-bottom: 1px solid #e2e8f0;
            padding-right: 1rem; /* Padding for scrollbar */
            display: flex;
            flex-direction: column;
        }
        .message-input-area {
            display: flex;
            padding-top: 1rem;
        }
        .message-input-area input {
            flex-grow: 1;
            border-radius: 9999px; /* Fully rounded input */
            padding: 0.75rem 1.5rem;
            border: 1px solid #cbd5e0;
            outline: none;
            transition: border-color 0.2s;
        }
        .message-input-area input:focus {
            border-color: #667eea;
        }
        .message-input-area button {
            background-color: #667eea;
            color: white;
            border-radius: 9999px; /* Fully rounded button */
            padding: 0.75rem 1.5rem;
            margin-left: 0.75rem;
            transition: background-color 0.2s, transform 0.1s;
            box-shadow: 0 4px 10px rgba(102, 126, 234, 0.3);
        }
        .message-input-area button:hover {
            background-color: #5a67d8;
            transform: translateY(-1px);
        }
        .message-input-area button:active {
            transform: translateY(0);
            box-shadow: none;
        }
        .message-bubble {
            max-width: 80%;
            margin-bottom: 0.5rem;
        }
        .message-bubble.self {
            align-self: flex-end;
            background-color: #d1e7dd; /* Lighter green for self messages */
            color: #155724;
        }
        .message-bubble.other {
            align-self: flex-start;
            background-color: #f8d7da; /* Lighter red for other messages */
            color: #721c24;
        }
        audio {
            width: 100%;
            max-width: 400px; /* Limit audio player width */
            margin-top: 2rem;
            border-radius: 0.75rem;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
        }
        .loading-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(255, 255, 255, 0.8);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            flex-direction: column;
            gap: 1rem;
        }
        .spinner {
            border: 4px solid rgba(0, 0, 0, 0.1);
            border-left-color: #667eea;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div id="loading-indicator" class="loading-overlay">
        <div class="spinner"></div>
        <p class="text-lg text-gray-700">Cargando aplicación...</p>
    </div>

    <div class="container">
        <!-- Sección de la Radio -->
        <div class="radio-section">
            <h1 class="text-4xl font-bold mb-4">¡Bienvenido a tu Radio Web!</h1>
            <p class="text-lg mb-6">Sintoniza la mejor música y chatea con otros oyentes.</p>
            <audio controls preload="auto" src="https://centova.hostingelectrica.net/proxy/baulmania/stream">
                Tu navegador no soporta el elemento de audio.
            </audio>
            <p class="text-sm mt-4 opacity-80">
                Puedes cambiar la URL de la transmisión de audio en el código fuente.
            </p>
            <div id="user-id-display" class="mt-6 text-sm bg-white bg-opacity-20 px-4 py-2 rounded-full">
                Tu ID: Cargando...
            </div>
        </div>

        <!-- Sección del Chat -->
        <div class="chat-section">
            <h2 class="text-2xl font-semibold text-gray-800 mb-4">Chat en Vivo</h2>
            <div id="messages-display" class="messages-display mb-4 p-4 bg-white rounded-lg shadow-inner">
                <!-- Los mensajes del chat se cargarán aquí -->
            </div>
            <div class="message-input-area">
                <input type="text" id="message-input" placeholder="Escribe tu mensaje aquí..." class="focus:ring-2 focus:ring-indigo-500">
                <button id="send-button">Enviar</button>
            </div>
        </div>
    </div>

    <script type="module">
        // Importar las funciones necesarias de Firebase
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, addDoc, query, onSnapshot, serverTimestamp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Variables globales para la aplicación Firebase
        let app, db, auth, userId;
        let isAuthReady = false; // Bandera para saber si la autenticación está lista

        // Obtener ID de la aplicación y configuración de Firebase del entorno
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');

        /**
         * Inicializa Firebase y maneja la autenticación del usuario.
         * Se intenta autenticar con un token personalizado si está disponible,
         * de lo contrario, se inicia sesión de forma anónima.
         */
        async function initFirebase() {
            try {
                // Inicializar la aplicación Firebase
                app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);

                // Escuchar cambios en el estado de autenticación
                onAuthStateChanged(auth, async (user) => {
                    if (user) {
                        // Si el usuario ya está autenticado
                        userId = user.uid;
                        console.log("Autenticado como:", userId);
                    } else {
                        // Si no hay usuario autenticado, intentar con token o de forma anónima
                        if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                            // Usar token personalizado si está disponible
                            await signInWithCustomToken(auth, __initial_auth_token);
                            userId = auth.currentUser.uid;
                            console.log("Sesión iniciada con token personalizado:", userId);
                        } else {
                            // Iniciar sesión de forma anónima si no hay token
                            await signInAnonymously(auth);
                            userId = auth.currentUser.uid;
                            console.log("Sesión iniciada anónimamente:", userId);
                        }
                    }
                    // Marcar que la autenticación está lista y ocultar el indicador de carga
                    isAuthReady = true;
                    document.getElementById('loading-indicator').classList.add('hidden');
                    setupChatListener(); // Iniciar la escucha de mensajes del chat
                    displayUserId(); // Mostrar el ID del usuario en la interfaz
                });
            } catch (error) {
                console.error("Error al inicializar Firebase:", error);
                // Mostrar mensaje de error si la inicialización falla
                document.getElementById('loading-indicator').textContent = 'Error al cargar la aplicación. Consulta la consola para más detalles.';
            }
        }

        /**
         * Envía un mensaje al chat de Firestore.
         * El mensaje incluye el ID del usuario y una marca de tiempo.
         */
        async function sendMessage() {
            const messageInput = document.getElementById('message-input');
            const messageText = messageInput.value.trim();

            // Solo enviar si hay texto y el usuario está autenticado
            if (messageText && userId) {
                try {
                    // Referencia a la colección pública de mensajes del chat
                    const messagesCollectionRef = collection(db, `artifacts/${appId}/public/data/radio_chat_messages`);
                    await addDoc(messagesCollectionRef, {
                        userId: userId,
                        message: messageText,
                        timestamp: serverTimestamp() // Marca de tiempo del servidor para ordenar
                    });
                    messageInput.value = ''; // Limpiar el campo de entrada
                } catch (e) {
                    console.error("Error al añadir documento: ", e);
                }
            } else {
                console.warn("No hay mensaje o ID de usuario disponible para enviar.");
            }
        }

        /**
         * Configura un listener en tiempo real para los mensajes del chat en Firestore.
         * Los mensajes se muestran en la interfaz y se ordenan por marca de tiempo.
         */
        function setupChatListener() {
            // Asegurarse de que la base de datos y la autenticación estén listas
            if (!db || !isAuthReady) {
                console.log("Firestore o autenticación no están listos, reintentando listener...");
                return;
            }

            const messagesCollectionRef = collection(db, `artifacts/${appId}/public/data/radio_chat_messages`);
            // Crear una consulta para la colección de mensajes
            // Importante: No usar orderBy() directamente en la consulta para evitar errores de índice.
            // Se ordenará en el cliente.
            const q = messagesCollectionRef;

            // Escuchar cambios en tiempo real en la colección de mensajes
            onSnapshot(q, (snapshot) => {
                const messagesDisplay = document.getElementById('messages-display');
                messagesDisplay.innerHTML = ''; // Limpiar mensajes existentes
                const messages = [];

                // Recopilar todos los mensajes y sus datos
                snapshot.forEach((doc) => {
                    const data = doc.data();
                    messages.push(data);
                });

                // Ordenar los mensajes por marca de tiempo en JavaScript
                // Esto es necesario porque no usamos orderBy en la consulta de Firestore
                messages.sort((a, b) => {
                    const timeA = a.timestamp ? a.timestamp.toDate().getTime() : 0;
                    const timeB = b.timestamp ? b.timestamp.toDate().getTime() : 0;
                    return timeA - timeB;
                });

                // Mostrar cada mensaje en la interfaz
                messages.forEach(data => {
                    const messageElement = document.createElement('div');
                    messageElement.classList.add('message-bubble', 'p-3', 'rounded-xl', 'break-words', 'shadow-sm');

                    // Asignar clases CSS según si el mensaje es del usuario actual o de otro
                    if (data.userId === userId) {
                        messageElement.classList.add('self');
                    } else {
                        messageElement.classList.add('other');
                    }

                    // Mostrar el ID del usuario (truncado) y el mensaje
                    messageElement.innerHTML = `<strong class="text-xs opacity-80">${data.userId.substring(0, 8)}...</strong><p class="text-sm mt-1">${data.message}</p>`;
                    messagesDisplay.appendChild(messageElement);
                });

                // Desplazarse automáticamente al final del chat
                messagesDisplay.scrollTop = messagesDisplay.scrollHeight;
            }, (error) => {
                console.error("Error al escuchar mensajes: ", error);
            });
        }

        /**
         * Muestra el ID completo del usuario en la interfaz.
         */
        function displayUserId() {
            const userIdDisplay = document.getElementById('user-id-display');
            if (userIdDisplay && userId) {
                userIdDisplay.textContent = `Tu ID: ${userId}`;
            }
        }

        // Event Listeners
        window.onload = initFirebase; // Iniciar Firebase cuando la ventana se carga

        document.getElementById('send-button').addEventListener('click', sendMessage);

        // Permitir enviar mensajes con la tecla Enter
        document.getElementById('message-input').addEventListener('keypress', function (e) {
            if (e.key === 'Enter') {
                sendMessage();
            }
        });
    </script>
</body>
</html>
        
            
            
            
            
       
        

                
                            
            
                    
            
                   
                
         
        
                
