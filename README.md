# Formulario-Gastos-diarios
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Registro de Gastos</title>
  <style>
    body {
      font-family: sans-serif;
      text-align: center;
      padding: 40px;
    }
    button {
      font-size: 24px;
      padding: 15px;
      border-radius: 10px;
    }
    #resultado {
      margin-top: 20px;
      font-size: 18px;
    }
  </style>
</head>
<body>
  <h1>¿Qué gastos hiciste hoy?</h1>

  <label for="quien">¿Quién habla?</label><br>
  <select id="quien">
    <option value="mamá">Mamá</option>
    <option value="papá">Papá</option>
  </select><br><br>

  <button id="grabar">🎤 Grabar</button>

  <p id="resultado"></p>

  <script>
    const grabar = document.getElementById("grabar");
    const resultado = document.getElementById("resultado");
    const quien = document.getElementById("quien");

    let recognition;

    if ('webkitSpeechRecognition' in window) {
      recognition = new webkitSpeechRecognition();
      recognition.lang = "es-ES";
      recognition.continuous = false;
      recognition.interimResults = false;

      recognition.onresult = function(event) {
        const transcripcion = event.results[0][0].transcript;
        resultado.textContent = "Registrando: " + transcripcion;

        // extraer número de la transcripción (opcional)
        const numero = transcripcion.match(/\d+(\.\d+)?/g);
        const valor = numero ? numero[0] : "";

        // ENVÍA LOS DATOS AL APP SCRIPT
        fetch("https://script.google.com/macros/s/AKfycbwyd9WM0_qBx_WRMLDXwVZ8sFGJepOWUIUotrE7d3ZfaGeox0y0Fj9HFUNioo7oVrXM/exec", {
          method: 'POST',
          body: JSON.stringify({
            quien: quien.value,
            transcripcion: transcripcion,
            valor: valor
          }),
          headers: {
            'Content-Type': 'application/json'
          }
        })
        .then(response => response.text())
        .then(data => {
          resultado.textContent = "✅ Gasto registrado con éxito";
        })
        .catch(error => {
          resultado.textContent = "❌ Error al registrar el gasto";
          console.error("Error:", error);
        });
      };

      recognition.onerror = function(event) {
        resultado.textContent = "❌ Error de reconocimiento de voz";
      };
    } else {
      resultado.textContent = "Tu navegador no soporta reconocimiento de voz";
    }

    grabar.addEventListener("click", () => {
      resultado.textContent = "🎙️ Escuchando...";
      recognition.start();
    });
  </script>
</body>
</html>
