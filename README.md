# my-web
Alex web


<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>El Tablero Global</title>
  <style>
    body {
      font-family: "Segoe UI", Tahoma, sans-serif;
      background: #f8f9fa;
      margin: 0;
      padding: 0;
      color: #51;
    }

    header {
      background: linear-gradient(135deg, #0077ff, #00b894);
      color: white;
      padding: 2rem 1rem;
      text-align: center;
    }

    header h1 {
      margin: 0;
      font-size: 2rem;
    }

    main {
      display: grid;
      grid-template-columns: 1fr;
      gap: 2rem;
      padding: 2rem;
      max-width: 900px;
      margin: auto;
    }

    section {
      background: white;
      padding: 1.5rem;
      border-radius: 16px;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    }

    h2 {
      margin-top: 0;
      font-size: 1.4rem;
      border-bottom: 2px solid #eee;
      padding-bottom: 0.5rem;
    }

    .tarjeta-pelicula, .tarjeta-oferta {
      background: #f1f3f5;
      padding: 1rem;
      border-radius: 10px;
      margin-top: 1rem;
    }

    footer {
      text-align: center;
      padding: 1rem;
      background: #222;
      color: white;
      font-size: 0.9rem;
    }

    @media (min-width: 768px) {
      main {
        grid-template-columns: repeat(2, 1fr);
      }

      #entretenimiento {
        grid-column: span 2;
      }
    }
  </style>
</head>
<body>
  <header>
    <h1>El Tablero Global: Finanzas, Clima y Ocio</h1>
    <p>Tu centro de información diaria.</p>
  </header>

  <main>
    <section id="finanzas">
      <h2>📈 Valoración de Bitcoin (BTC)</h2>
      <div id="bitcoin-widget">
        <p>Cargando valor en tiempo real...</p>
      </div>
    </section>

    <section id="clima">
      <h2>☁️ El Tiempo Hoy</h2>
      <div id="clima-widget">
        <p>Cargando datos meteorológicos...</p>
      </div>
    </section>

    <section id="entretenimiento">
      <h2>🎬 Entretenimiento</h2>

      <div id="peliculas">
        <h3>🎞️ Películas Populares</h3>
        <div id="peliculas-lista">
          <p>Cargando películas...</p>
        </div>
      </div>

      <hr>

      <div id="videojuegos">
        <h3>🎮 Ofertas de Videojuegos</h3>
        <div id="ofertas-lista">
          <p>Cargando ofertas...</p>
        </div>
      </div>
    </section>
  </main>

  <footer>
    <p>&copy; | Datos obtenidos de APIs públicas.</p>
  </footer>

  <script>
    // === BITCOIN (CoinGecko, funciona en Android) ===
    async function cargarBitcoin() {
      try {
        const res = await fetch("https://api.coingecko.com/api/v3/simple/price?ids=bitcoin&vs_currencies=eur");
        const data = await res.json();
        const precio = data.bitcoin.eur.toLocaleString('es-ES', {minimumFractionDigits: 2});
        document.getElementById("bitcoin-widget").innerHTML = `
          <h3>1 BTC = €${precio}</h3>
          <p>Actualizado: ${new Date().toLocaleTimeString()}</p>
        `;
      } catch (e) {
        document.getElementById("bitcoin-widget").innerHTML = "No se pudo cargar el valor.";
      }
    }

    // === CLIMA ===
    async function cargarClima(lat, lon) {
      const api = `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current_weather=true`;
      try {
        const res = await fetch(api);
        const data = await res.json();
        const c = data.current_weather;
        document.getElementById("clima-widget").innerHTML = `
          <h3>${c.temperature}°C</h3>
          <p>Viento: ${c.windspeed} km/h</p>
          <p>Hora local: ${new Date(c.time).toLocaleTimeString()}</p>
        `;
      } catch (e) {
        document.getElementById("clima-widget").innerHTML = "No se pudo obtener el clima.";
      }
    }

    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition(
        pos => cargarClima(pos.coords.latitude, pos.coords.longitude),
        () => document.getElementById("clima-widget").innerHTML = "Permite la ubicación para ver el clima."
      );
    }

    // === PELÍCULAS (fuente libre, sin registro) ===
    async function cargarPeliculas() {
      try {
        const res = await fetch("https://raw.githubusercontent.com/prust/wikipedia-movie-data/master/movies.json");
        const data = await res.json();
        const populares = data.slice(-5).reverse(); // últimas películas agregadas
        const lista = populares.map(p => `
          <div class="tarjeta-pelicula">
            <h4>${p.title}</h4>
            <p>Año: ${p.year}</p>
            <p>Género: ${p.genres.join(", ")}</p>
          </div>
        `).join("");
        document.getElementById("peliculas-lista").innerHTML = lista;
      } catch {
        document.getElementById("peliculas-lista").innerHTML = "<p>No se pudieron cargar las películas.</p>";
      }
    }

    // === OFERTAS DE JUEGOS ===
    async function cargarOfertas() {
      try {
        const res = await fetch("https://www.cheapshark.com/api/1.0/deals?storeID=1&upperPrice=20");
        const data = await res.json();
        const lista = data.slice(0, 4).map(j => `
          <div class="tarjeta-oferta">
            <h4>${j.title}</h4>
            <p>Descuento: ${parseInt(j.savings)}% | Precio: ${j.salePrice}€</p>
          </div>
        `).join("");
        document.getElementById("ofertas-lista").innerHTML = lista;
      } catch {
        document.getElementById("ofertas-lista").innerHTML = "<p>No se pudieron cargar las ofertas.</p>";
      }
    }

    // === INICIO ===
    cargarBitcoin();
    cargarPeliculas();
    cargarOfertas();
    setInterval(cargarBitcoin, 60000); // Actualiza cada minuto
  </script>
</body>
</html>
