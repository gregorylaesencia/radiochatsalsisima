<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mi Radio Web con Chat</title>
    <!-- Enlace a Tailwind CSS CDN para estilos responsivos y modernos -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Estilos personalizados para la barra de desplazamiento del chat */
        .chat-messages::-webkit-scrollbar {
            width: 8px;
        }

        .chat-messages::-webkit-scrollbar-track {
            background: #f1f1f1;
            border-radius: 10px;
        }

        .chat-messages::-webkit-scrollbar-thumb {
            background: #888;
            border-radius: 10px;
        }

        .chat-messages::-webkit-scrollbar-thumb:hover {
            background: #555;
        }

        /* Estilo para el reproductor de audio */
        audio {
            width: 100%;
            border-radius: 0.5rem; /* Bordes redondeados */
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1); /* Sombra sutil */
        }
    </style>
</head>
<body class="bg-gradient-to-br from-purple-600 to-blue-500 min-h-screen flex items-center justify-center p-4 font-sans">
    <div class="bg-white p-6 rounded-xl shadow-2xl w-full max-w-4xl flex flex-col md:flex-row gap-6">
        <!-- Sección de la Radio -->
        <div class="flex-1 bg-gray-50 p-5 rounded-lg shadow-inner flex flex-col items-center justify-center">
            <h1 class="text-3xl font-bold text-gray-800 mb-4">Mi Estación de Radio</h1>
            <p class="text-gray-600 mb-6 text-center">¡Sintoniza y disfruta de la mejor música!</p>

            <!-- Reproductor de Audio -->
            <audio controls autoplay class="mb-6">
                <!--
                    IMPORTANTE: Reemplaza esta URL con la URL real de tu stream de radio.
                    Ejemplos:
                    - Un stream de Icecast/Shoutcast: http://tu-servidor.com:8000/radio.mp3
                    - Un stream de un servicio de radio online.
                    Si no tienes una, puedes usar una URL de radio pública para probar,
                    pero asegúrate de que sea legalmente accesible.
                -->
                <source src="https://stream.radio.co/s2a1b9201a/listen" type="audio/mpeg">
                Tu navegador no soporta el elemento de audio.
            </audio>

            <div class="text-sm text-gray-500 text-center">
                <p>Asegúrate de reemplazar la URL de la radio en el código fuente con tu propio stream.</p>
                <p>Créditos del stream de ejemplo: <a href="https://radio.co/" target="_blank" class="text-blue-500 hover:underline">Radio.co</a></p>
            </div>
        </div>

        <!-- Sección del Chat -->
        <div class="flex-1 bg-gray-50 p-5 rounded-lg shadow-inner flex flex-col">
            <h2 class="text-2xl font-bold text-gray-800 mb-4 text-center">Chat en Vivo</h2>
            <div id="loading-message" class="text-center text-gray-600 mb-4">Cargando chat...</div>
            <div id="chat-messages" class="chat-messages flex-1 bg-white p-4 rounded-lg border border-gray-200 overflow-y-auto mb-4 h-64 md:h-80">
                <!-- Los mensajes del chat se cargarán aquí -->
            </div>

            <div id="chat-input-area" class="hidden">
                <p class="text-sm text-gray-600 mb-2">Tu ID de usuario: <span id="user-id-display" class="font-semibold text-purple-600 break-all"></span></p>
                <div class="flex gap-2">
                    <input type="text" id="message-input" placeholder="Escribe tu mensaje..." class="flex-1 p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-400">
                    <button id="send-button" class="bg-blue-500 text-white px-5 py-3 rounded-lg font-semibold hover:bg-blue-600 transition duration-300 ease-in-out shadow-md">
                        Enviar
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- Script de Firebase -->
    <script type="module">
        // Importa los módulos necesarios de Firebase
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, addDoc, onSnapshot, query, orderBy, serverTimestamp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Variables globales proporcionadas por el entorno de Canvas
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        let app;
        let db;
        let auth;
        let userId = 'cargando...'; // Valor inicial mientras se autentica

        const chatMessagesDiv = document.getElementById('chat-messages');
        const messageInput = document.getElementById('message-input');
        const sendButton = document.getElementById('send-button');
        const userIdDisplay = document.getElementById('user-id-display');
        const loadingMessage = document.getElementById('loading-message');
        const chatInputArea = document.getElementById('chat-input-area');

        /**
         * Inicializa Firebase y configura la autenticación y Firestore.
         */
        async function initializeFirebase() {
            try {
                // Inicializa la aplicación Firebase
                app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);

                // Maneja la autenticación del usuario
                if (initialAuthToken) {
                    await signInWithCustomToken(auth, initialAuthToken);
                } else {
                    await signInAnonymously(auth);
                }

                // Escucha los cambios en el estado de autenticación
                onAuthStateChanged(auth, (user) => {
                    if (user) {
                        userId = user.uid; // Usa el UID del usuario autenticado
                        userIdDisplay.textContent = userId;
                        loadingMessage.classList.add('hidden'); // Oculta el mensaje de carga
                        chatInputArea.classList.remove('hidden'); // Muestra el área de entrada del chat
                        setupChatListener(); // Configura el listener del chat una vez autenticado
                    } else {
                        // Si no hay usuario, genera un ID aleatorio (para usuarios anónimos si signInAnonymously falla)
                        userId = crypto.randomUUID();
                        userIdDisplay.textContent = userId;
                        loadingMessage.classList.add('hidden');
                        chatInputArea.classList.remove('hidden');
                        setupChatListener();
                        console.warn("Usuario no autenticado, usando ID aleatorio.");
                    }
                });

            } catch (error) {
                console.error("Error al inicializar Firebase o autenticar:", error);
                loadingMessage.textContent = "Error al cargar el chat. Inténtalo de nuevo más tarde.";
                chatInputArea.classList.add('hidden'); // Asegura que el área de entrada esté oculta en caso de error
            }
        }

        /**
         * Configura el listener para los mensajes del chat en Firestore.
         * Los mensajes se almacenan en una colección pública para que todos los usuarios puedan verlos.
         */
        function setupChatListener() {
            // Define la ruta de la colección de chat para datos públicos
            const chatCollectionPath = `/artifacts/${appId}/public/data/radio_chat`;
            const q = query(collection(db, chatCollectionPath)); // No usar orderBy para evitar errores de índice

            onSnapshot(q, (snapshot) => {
                chatMessagesDiv.innerHTML = ''; // Limpia los mensajes existentes
                const messages = [];
                snapshot.forEach((doc) => {
                    messages.push({ id: doc.id, ...doc.data() });
                });

                // Ordenar los mensajes por timestamp en el cliente
                messages.sort((a, b) => (a.timestamp?.toDate() || 0) - (b.timestamp?.toDate() || 0));

                messages.forEach((msg) => {
                    const messageElement = document.createElement('div');
                    messageElement.classList.add('mb-2', 'p-2', 'rounded-lg');

                    const isCurrentUser = msg.userId === userId;
                    if (isCurrentUser) {
                        messageElement.classList.add('bg-blue-100', 'self-end', 'ml-auto', 'text-right');
                    } else {
                        messageElement.classList.add('bg-gray-100', 'self-start', 'mr-auto');
                    }

                    const senderId = msg.userId.length > 8 ? `${msg.userId.substring(0, 8)}...` : msg.userId; // Truncar ID para mostrar
                    const timestamp = msg.timestamp ? new Date(msg.timestamp.toDate()).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }) : 'Ahora';

                    messageElement.innerHTML = `
                        <p class="font-semibold text-sm ${isCurrentUser ? 'text-blue-700' : 'text-gray-700'}">${isCurrentUser ? 'Tú' : senderId}</p>
                        <p class="text-gray-800">${msg.messageText}</p>
                        <p class="text-xs text-gray-500 mt-1">${timestamp}</p>
                    `;
                    chatMessagesDiv.appendChild(messageElement);
                });
                chatMessagesDiv.scrollTop = chatMessagesDiv.scrollHeight; // Desplazar al final
            }, (error) => {
                console.error("Error al obtener mensajes del chat:", error);
                chatMessagesDiv.innerHTML = '<p class="text-red-500 text-center">Error al cargar los mensajes del chat.</p>';
            });
        }

        /**
         * Envía un nuevo mensaje al chat de Firestore.
         */
        async function sendMessage() {
            const messageText = messageInput.value.trim();
            if (messageText === '') {
                return; // No enviar mensajes vacíos
            }

            try {
                const chatCollectionPath = `/artifacts/${appId}/public/data/radio_chat`;
                await addDoc(collection(db, chatCollectionPath), {
                    userId: userId,
                    messageText: messageText,
                    timestamp: serverTimestamp() // Marca de tiempo del servidor para ordenar
                });
                messageInput.value = ''; // Limpia el campo de entrada
            } catch (error) {
                console.error("Error al enviar el mensaje:", error);
                // Aquí podrías mostrar un mensaje de error al usuario en la UI
            }
        }

        // Event Listeners
        sendButton.addEventListener('click', sendMessage);
        messageInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                sendMessage();
            }
        });

        // Inicializa Firebase cuando la ventana esté completamente cargada
        window.onload = initializeFirebase;
    </script>
</body>
</html>

           
            
            
            
        
        

               
            

        
                    
                

                   
                
           
        
            
       
        

                
                            
            
                    
            
                   
                
         
        
                
