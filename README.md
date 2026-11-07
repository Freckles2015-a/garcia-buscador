
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>GARCIA - Buscador</title>

<style>
* {
    box-sizing: border-box;
}

body {
    margin: 0;
    min-height: 100vh;
    background:
        radial-gradient(circle at center, #102b49 0%, #050b18 55%, #02040a 100%);
    color: white;
    font-family: Arial, sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    overflow-x: hidden;
}

.container {
    width: 90%;
    max-width: 750px;
    padding: 30px 15px;
}

h1 {
    font-size: clamp(55px, 12vw, 95px);
    letter-spacing: 12px;
    color: #00d9ff;
    text-shadow: 0 0 15px #00bfff, 0 0 40px #0077ff;
    margin-bottom: 5px;
}

.subtitle {
    color: #a9dfff;
    letter-spacing: 5px;
    margin-bottom: 45px;
}

.search-box {
    display: flex;
    gap: 10px;
    padding: 10px;
    border: 2px solid #00cfff;
    border-radius: 18px;
    background: #071426;
    box-shadow: 0 0 25px #0077ff66;
}

input {
    flex: 1;
    min-width: 0;
    background: transparent;
    border: none;
    outline: none;
    color: white;
    font-size: 18px;
    padding: 12px;
}

button {
    cursor: pointer;
    border: 1px solid #00cfff;
    border-radius: 12px;
    background: #06345a;
    color: white;
    padding: 12px 17px;
    font-size: 16px;
}

button:hover {
    background: #007bb5;
    box-shadow: 0 0 15px #00cfff;
}

#mic {
    font-size: 23px;
    min-width: 55px;
}

#mic.listening {
    background: #a00030;
    box-shadow: 0 0 20px #ff1744;
}

#status {
    min-height: 25px;
    margin-top: 15px;
    color: #00d9ff;
}

.categories {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    gap: 12px;
    margin-top: 25px;
}

footer {
    margin-top: 60px;
    color: #6488a9;
    font-size: 12px;
}
</style>
</head>

<body>
<div class="container">

    <h1>GARCIA</h1>
    <div class="subtitle">TU BUSCADOR, SIN LÍMITES</div>

    <form class="search-box" id="searchForm">
        <input
            id="search"
            type="search"
            placeholder="¿Qué quieres buscar?"
            autocomplete="off"
        >

        <button type="button" id="mic" title="Buscar por voz">
            🎤
        </button>

        <button type="submit">🔎</button>
    </form>

    <div id="status">Pulsa el micrófono para hablar</div>

    <div class="categories">
        <button type="button" data-type="web">🌐 Web</button>
        <button type="button" data-type="images">🖼️ Imágenes</button>
        <button type="button" data-type="videos">🎬 Vídeos</button>
        <button type="button" data-type="news">📰 Noticias</button>
    </div>

    <footer>GARCIA © 2026</footer>
</div>

<script>
const searchInput = document.getElementById("search");
const micButton = document.getElementById("mic");
const statusText = document.getElementById("status");

let searchType = "web";

function searchGarcia() {
    const query = searchInput.value.trim();

    if (!query) {
        statusText.textContent = "Escribe o di algo para buscar.";
        return;
    }

    let url = "https://duckduckgo.com/?q=" +
        encodeURIComponent(query);

    if (searchType === "images") {
        url += "&iax=images&ia=images";
    } else if (searchType === "videos") {
        url += "&iax=videos&ia=videos";
    } else if (searchType === "news") {
        url += "&iar=news&ia=news";
    }

    window.open(url, "_blank");
}

document.getElementById("searchForm").addEventListener(
    "submit",
    function(event) {
        event.preventDefault();
        searchGarcia();
    }
);

document.querySelectorAll("[data-type]").forEach(button => {
    button.addEventListener("click", () => {
        searchType = button.dataset.type;
        statusText.textContent = "Categoría: " + button.textContent;
        searchGarcia();
    });
});

// RECONOCIMIENTO DE VOZ
const SpeechRecognition =
    window.SpeechRecognition ||
    window.webkitSpeechRecognition;

if (SpeechRecognition) {
    const recognition = new SpeechRecognition();

    recognition.lang = "es-ES";
    recognition.continuous = false;
    recognition.interimResults = false;

    micButton.addEventListener("click", () => {
        try {
            recognition.start();
        } catch (error) {
            statusText.textContent =
                "Espera un momento e inténtalo otra vez.";
        }
    });

    recognition.onstart = () => {
        micButton.classList.add("listening");
        statusText.textContent = "🎙️ Te estoy escuchando...";
    };

    recognition.onresult = (event) => {
        const spokenText =
            event.results[0][0].transcript;

        searchInput.value = spokenText;
        statusText.textContent = "He reconocido: " + spokenText;

        searchGarcia();
    };

    recognition.onerror = (event) => {
        statusText.textContent =
            "No he podido escuchar. Comprueba el permiso del micrófono.";
    };

    recognition.onend = () => {
        micButton.classList.remove("listening");
    };

} else {
    micButton.disabled = true;
    statusText.textContent =
        "Tu navegador no admite reconocimiento de voz.";
}
</script>

</body>
</html>
