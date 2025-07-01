# radiochatsalsisima

<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mi Radio Web con Chat</title>
    
    <!-- Tailwind CSS para un diseño moderno y responsivo -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Google Fonts: 'Inter' para el texto -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700&display=swap" rel="stylesheet">
    
    <!-- Iconos (Feather Icons) -->
    <script src="https://unpkg.com/feather-icons"></script>

    <style>
        /* Estilos personalizados para mejorar la apariencia */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #111827; /* Gris oscuro para el fondo */
        }
        /* Estilo para la barra de desplazamiento del chat */
        #chat-messages::-webkit-scrollbar {
            width: 8px;
        }
        #chat-messages::-webkit-scrollbar-track {
            background: #1f2937;
        }
        #chat-messages::-webkit-scrollbar-thumb {
            background-color: #4b5563;
            border-radius: 20px;
            border: 3px solid #1f2937;
        }
        /* Animación de pulso para el indicador "EN VIVO" */
        .live-indicator {
            animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
        }
        @keyframes pulse {
            0%, 100% {
                opacity: 1;
            }
            50% {
                opacity: .5;
            }
        }
    </style>
</head>
<body class="text-white">

    <div class="container mx-auto p-4 max-w-4xl">
        
        <!-- Encabezado de la Radio -->
        <header class="text-center mb-8">
            <h1 class="text-4xl md:text-5xl font-bold tracking-tight bg-clip-text text-transparent bg-gradient-to-r from-purple-400 to-pink-600">
                SALSA BAUL CARACAS STREAMIG
            </h1>
            <p class="text-gray-400 mt-2">LO MAS SABROSO.</p>
        </header>

        <main class="grid grid-cols-1 md:grid-cols-3 gap-8">

            <!-- Columna del Reproductor -->
            <div class="md:col-span-2 bg-gray-800 rounded-2xl shadow-lg p-6">
                <div class="flex items-center justify-between mb-4">
                    <h2 class="text-2xl font-bold">Reproductor</h2>
                    <div class="flex items-center space-x-2 bg-red-600 text-white px-3 py-1 rounded-full text-sm font-semibold">
                        <span class="live-indicator h-2 w-2 bg-white rounded-full"></span>
                        <span>EN VIVO</span>
                    </div>
                </div>

                <!-- Elemento de audio (oculto) -->
                <audio id="radio-stream" preload="none">
                    <!-- 
                        IMPORTANTE: Reemplaza esta URL con la URL de tu stream de radio.
                        Puede ser de un servidor Icecast, SHOUTcast, etc.
                        Ejemplo: <source src="http://stream.zeno.fm/s9q6z7x5h2zuv" type="audio/mpeg">
                    -->
                    <source src="https://centova.hostingelectrica.net/proxy/baulmania/stream" type="audio/mpeg">
                    Tu navegador no soporta el elemento de audio.
                </audio>

                <div class="bg-gray-900 rounded-lg p-6 flex flex-col items-center">
                    <div class="mb-4">
                        <img id="album-art" src="https://placehold.co/150x150/1f2937/7c3aed?text=Radio" alt="Carátula del Álbum" class="w-36 h-36 rounded-lg shadow-md">
                    </div>
                    <p id="station-name" class="text-xl font-semibold">Estación Principal</p>
                    <p id="song-title" class="text-gray-400">Cargando...</p>
                    
                    <!-- Controles del Reproductor -->
                    <div class="flex items-center space-x-6 mt-6">
                        <button id="play-pause-btn" class="p-4 bg-purple-600 rounded-full hover:bg-purple-700 transition-colors focus:outline-none focus:ring-2 focus:ring-purple-500">
                            <i id="play-icon" data-feather="play" class="text-white"></i>
                            <i id="pause-icon" data-feather="pause" class="text-white hidden"></i>
                        </button>
                    </div>

                    <!-- Control de Volumen -->
                    <div class="w-full flex items-center space-x-3 mt-6">
                        <i data-feather="volume-1" class="text-gray-400"></i>
                        <input id="volume-slider" type="range" min="0" max="1" step="0.01" value="0.75" class="w-full h-2 bg-gray-700 rounded-lg appearance-none cursor-pointer">
                        <i data-feather="volume-2" class="text-gray-400"></i>
                    </div>
                </div>
            </div>

            <!-- Columna del Chat -->
            <div class="md:col-span-1 bg-gray-800 rounded-2xl shadow-lg flex flex-col h-[60vh] md:h-auto">
                <h2 class="text-2xl font-bold p-4 border-b border-gray-700">Chat en Vivo</h2>
                
                <!-- Área de mensajes del chat -->
                <div id="chat-messages" class="flex-grow p-4 overflow-y-auto space-y-4">
                    <!-- Los mensajes del chat se insertarán aquí -->
                     <div class="text-center text-gray-500 pt-8">Cargando chat...</div>
                </div>

                <!-- Formulario para enviar mensajes -->
                <div class="p-4 border-t border-gray-700">
                    <div class="mb-2">
                         <input type="text" id="username-input" placeholder="Tu nombre" class="w-full p-2 rounded-md bg-gray-700 border border-gray-600 focus:outline-none focus:ring-2 focus:ring-purple-500 text-sm">
                    </div>
                    <form id="chat-form" class="flex space-x-2">
                        <input type="text" id="message-input" placeholder="Escribe un mensaje..." class="flex-grow p-2 rounded-md bg-gray-700 border border-gray-600 focus:outline-none focus:ring-2 focus:ring-purple-500" required>
                        <button type="submit" class="bg-purple-600 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded-md transition-colors">
                            <i data-feather="send" class="w-5 h-5"></i>
                        </button>
                    </form>
                </div>
            </div>
        </main>
    </div>

    <!-- Firebase SDKs -->
    <script type="module">
        // Importar las funciones necesarias de los SDKs de Firebase
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getFirestore, collection, addDoc, query, onSnapshot, serverTimestamp, orderBy } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        import { getAuth, signInAnonymously, onAuthStateChanged, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";

        // --- CONFIGURACIÓN DE FIREBASE ---
        // IMPORTANTE: Reemplaza el siguiente objeto con la configuración de tu propio proyecto de Firebase.
        // Puedes encontrarla en la configuración de tu proyecto en la consola de Firebase.
        const firebaseConfig = {
            apiKey: "TU_API_KEY",
            authDomain: "TU_AUTH_DOMAIN",
            projectId: "TU_PROJECT_ID",
            storageBucket: "TU_STORAGE_BUCKET",
            messagingSenderId: "TU_MESSAGING_SENDER_ID",
            appId: "TU_APP_ID"
        };

        // --- INICIALIZACIÓN DE LA APLICACIÓN ---
        // Usar el __app_id y __firebase_config si están disponibles (entorno de Canvas)
        const finalFirebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : firebaseConfig;
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'radio-chat-default';

        const app = initializeApp(finalFirebaseConfig);
        const db = getFirestore(app);
        const auth = getAuth(app);

        let userId = null; // Variable para guardar el ID del usuario

        // --- LÓGICA DE AUTENTICACIÓN ---
        onAuthStateChanged(auth, (user) => {
            if (user) {
                // El usuario ha iniciado sesión
                userId = user.uid;
                console.log("Usuario autenticado con ID:", userId);
                // Una vez autenticado, inicializamos el chat
                initChat();
            } else {
                // El usuario no ha iniciado sesión, intentar iniciar sesión
                console.log("Usuario no autenticado, intentando iniciar sesión...");
            }
        });

        // Función para iniciar sesión (anónima o con token)
        async function signIn() {
            try {
                if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                    await signInWithCustomToken(auth, __initial_auth_token);
                } else {
                    await signInAnonymously(auth);
                }
            } catch (error) {
                console.error("Error al iniciar sesión:", error);
                document.getElementById('chat-messages').innerHTML = `<div class="text-red-400">Error de conexión con el chat.</div>`;
            }
        }
        
        // Iniciar el proceso de autenticación al cargar la página
        signIn();


        // --- LÓGICA DEL CHAT ---
        const chatMessages = document.getElementById('chat-messages');
        const chatForm = document.getElementById('chat-form');
        const messageInput = document.getElementById('message-input');
        const usernameInput = document.getElementById('username-input');
        
        // Guardar el nombre de usuario en localStorage para recordarlo
        usernameInput.value = localStorage.getItem('radio-chat-username') || '';
        usernameInput.addEventListener('input', () => {
            localStorage.setItem('radio-chat-username', usernameInput.value);
        });

        function initChat() {
            // Ruta de la colección en Firestore
            const chatCollectionPath = `artifacts/${appId}/public/data/chat-messages`;
            const messagesRef = collection(db, chatCollectionPath);
            const q = query(messagesRef, orderBy("timestamp", "asc"));

            // Escuchar cambios en la colección de mensajes en tiempo real
            onSnapshot(q, (snapshot) => {
                chatMessages.innerHTML = ''; // Limpiar mensajes antiguos
                if (snapshot.empty) {
                     chatMessages.innerHTML = '<div class="text-center text-gray-500 pt-8">Sé el primero en enviar un mensaje.</div>';
                } else {
                    snapshot.forEach(doc => {
                        const message = doc.data();
                        displayMessage(message);
                    });
                    // Hacer scroll hasta el último mensaje
                    chatMessages.scrollTop = chatMessages.scrollHeight;
                }
            }, (error) => {
                console.error("Error al obtener mensajes:", error);
                chatMessages.innerHTML = `<div class="text-red-400">No se pudieron cargar los mensajes.</div>`;
            });
        }

        // Función para mostrar un mensaje en la UI
        function displayMessage(message) {
            const messageElement = document.createElement('div');
            messageElement.classList.add('flex', 'flex-col', 'items-start');

            const nameElement = document.createElement('span');
            nameElement.classList.add('text-xs', 'font-bold', 'text-purple-400', 'mb-1');
            nameElement.textContent = message.name || 'Anónimo';

            const textElement = document.createElement('div');
            textElement.classList.add('bg-gray-700', 'rounded-lg', 'px-3', 'py-2', 'text-sm');
            textElement.textContent = message.text;
            
            messageElement.appendChild(nameElement);
            messageElement.appendChild(textElement);
            chatMessages.appendChild(messageElement);
        }

        // Enviar un mensaje
        chatForm.addEventListener('submit', async (e) => {
            e.preventDefault();
            const messageText = messageInput.value.trim();
            const userName = usernameInput.value.trim() || 'Anónimo';

            if (messageText !== '' && userId) {
                try {
                    const chatCollectionPath = `artifacts/${appId}/public/data/chat-messages`;
                    await addDoc(collection(db, chatCollectionPath), {
                        name: userName,
                        text: messageText,
                        timestamp: serverTimestamp(),
                        uid: userId
                    });
                    messageInput.value = ''; // Limpiar el input
                } catch (error) {
                    console.error("Error al enviar el mensaje: ", error);
                    alert("No se pudo enviar tu mensaje. Inténtalo de nuevo.");
                }
            }
        });

        // --- LÓGICA DEL REPRODUCTOR DE AUDIO ---
        const audio = document.getElementById('radio-stream');
        const playPauseBtn = document.getElementById('play-pause-btn');
        const playIcon = document.getElementById('play-icon');
        const pauseIcon = document.getElementById('pause-icon');
        const volumeSlider = document.getElementById('volume-slider');
        const songTitle = document.getElementById('song-title');

        // Función para actualizar el título (simulado)
        function updateMetadata() {
            // En una implementación real, aquí usarías la API de tu servidor de streaming
            // para obtener los metadatos de la canción actual.
            // Por ahora, simulamos un cambio.
            const artists = ["LA RADIO", "QUE DOMINA LA ZONA", " 24/7}"];
            const songs = ["SALSISIMA", "SALSA DE HOY Y SIEMPRE", "SUBELE"];
            const randomArtist = artists[Math.floor(Math.random() * artists.length)];
            const randomSong = songs[Math.floor(Math.random() * songs.length)];
            songTitle.textContent = `${randomArtist} - ${randomSong}`;
        }

        playPauseBtn.addEventListener('click', () => {
            if (audio.paused) {
                audio.play().catch(error => console.error("Error al reproducir:", error));
            } else {
                audio.pause();
            }
        });

        audio.addEventListener('play', () => {
            playIcon.classList.add('hidden');
            pauseIcon.classList.remove('hidden');
            updateMetadata(); // Actualizar al empezar a reproducir
            // Simular actualización de metadatos cada 15 segundos
            setInterval(updateMetadata, 15000);
        });

        audio.addEventListener('pause', () => {
            playIcon.classList.remove('hidden');
            pauseIcon.classList.add('hidden');
        });

        volumeSlider.addEventListener('input', (e) => {
            audio.volume = e.target.value;
        });
        
        // Inicializar los iconos
        feather.replace();
    </script>

</body>
</html>
