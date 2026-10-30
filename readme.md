<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Noticias climáticas del día</title>
<style>
  :root{
    --verde:#1f7a4d; --verde-osc:#14532d; --verde-claro:#e8f5ee;
    --azul:#0e7490; --arena:#f7f5ef; --texto:#1c2b22; --gris:#5b6b62;
    --borde:#cfe3d7; --error:#b42318;
  }
  *{box-sizing:border-box;margin:0;padding:0}
  body{
    font-family:system-ui,-apple-system,"Segoe UI",Roboto,Arial,sans-serif;
    background:linear-gradient(180deg,#dff1e7 0%,var(--arena) 320px);
    color:var(--texto);line-height:1.55;min-height:100vh;
  }
  header{
    background:linear-gradient(135deg,var(--verde-osc),var(--verde) 60%,var(--azul));
    color:#fff;padding:48px 16px 72px;text-align:center;
  }
  header .icono{font-size:44px;line-height:1}
  header h1{font-size:clamp(1.7rem,4.5vw,2.6rem);margin:10px 0 8px;letter-spacing:-.5px}
  header p{max-width:640px;margin:0 auto;opacity:.92;font-size:1.02rem}
  main{max-width:820px;margin:-44px auto 40px;padding:0 16px}
  .tarjeta{
    background:#fff;border:1px solid var(--borde);border-radius:16px;
    padding:24px;box-shadow:0 10px 30px rgba(20,83,45,.08);margin-bottom:20px;
  }
  label{display:block;font-weight:600;margin-bottom:8px;color:var(--verde-osc)}
  .controles{display:flex;gap:12px;flex-wrap:wrap}
  select{
    flex:1 1 260px;padding:12px 14px;font-size:1rem;border:2px solid var(--borde);
    border-radius:10px;background:var(--verde-claro);color:var(--texto);
  }
  select:focus{outline:none;border-color:var(--verde)}
  button{
    flex:0 0 auto;padding:12px 24px;font-size:1rem;font-weight:700;color:#fff;
    background:var(--verde);border:none;border-radius:10px;cursor:pointer;
    transition:background .2s,transform .1s;
  }
  button:hover:not(:disabled){background:var(--verde-osc)}
  button:active:not(:disabled){transform:scale(.98)}
  button:disabled{background:#8fb3a0;cursor:wait}
  .resultados-cab{
    display:flex;justify-content:space-between;align-items:baseline;
    flex-wrap:wrap;gap:6px;border-bottom:2px solid var(--verde-claro);
    padding-bottom:10px;margin-bottom:14px;
  }
  .resultados-cab h2{font-size:1.25rem;color:var(--verde-osc)}
  #fecha{color:var(--gris);font-size:.92rem;text-transform:capitalize}
  #resultado{white-space:pre-line;word-wrap:break-word;overflow-wrap:anywhere;font-size:1rem}
  #resultado a{color:var(--azul);font-weight:600}
  #resultado.vacio{color:var(--gris);font-style:italic}
  #resultado.cargando{color:var(--verde)}
  #resultado.error{color:var(--error);font-weight:600}
  .spinner{
    display:inline-block;width:16px;height:16px;border:3px solid var(--borde);
    border-top-color:var(--verde);border-radius:50%;animation:gira .8s linear infinite;
    vertical-align:-3px;margin-right:8px;
  }
  @keyframes gira{to{transform:rotate(360deg)}}
  footer{text-align:center;color:var(--gris);font-size:.85rem;padding:0 16px 32px}
  @media (max-width:520px){
    .tarjeta{padding:18px}
    button{width:100%}
  }
</style>
</head>
<body>

<header>
  <div class="icono">🌎</div>
  <h1>Noticias climáticas del día</h1>
  <p>Esta página muestra un resumen de las noticias ambientales más recientes, explicado por inteligencia artificial, con los enlaces a sus fuentes originales.</p>
</header>

<main>
  <section class="tarjeta">
    <label for="tema">Tema</label>
    <div class="controles">
      <select id="tema">
        <option>cambio climático</option>
        <option>deforestación México</option>
        <option>plásticos de un solo uso</option>
        <option>energías renovables México</option>
        <option>sequía Sinaloa</option>
        <option>manglares Sinaloa</option>
      </select>
      <button id="btn">Ver noticias</button>
    </div>
  </section>

  <section class="tarjeta">
    <div class="resultados-cab">
      <h2>Resultados</h2>
      <span id="fecha"></span>
    </div>
    <div id="resultado" class="vacio">Elige un tema y presiona "Ver noticias".</div>
  </section>
</main>

<footer>Práctica 3.5 · Desarrollo Sustentable · TecNM Campus Mazatlán</footer>

<script>
  // 👇 PEGA AQUÍ LA URL DE TU WEBHOOK DE MAKE (entre las comillas)
  const WEBHOOK_URL = "https://hook.us2.make.com/vrf2538ngr6s5f2bbx9uzlx5zdc9d0wq";

  const btn = document.getElementById("btn");
  const selTema = document.getElementById("tema");
  const resultado = document.getElementById("resultado");

  // Fecha de hoy junto al título de resultados
  document.getElementById("fecha").textContent = new Date().toLocaleDateString("es-MX", {
    weekday: "long", year: "numeric", month: "long", day: "numeric"
  });

  // Muestra el texto plano y convierte en enlace las líneas que empiezan con http
  function mostrarTexto(texto) {
    resultado.className = "";
    resultado.textContent = "";
    const lineas = texto.split("\n");
    lineas.forEach(function (linea, i) {
      const limpia = linea.trim();
      if (/^https?:\/\//i.test(limpia)) {
        const sangria = linea.slice(0, linea.indexOf(limpia));
        if (sangria) resultado.appendChild(document.createTextNode(sangria));
        const a = document.createElement("a");
        a.href = limpia;
        a.textContent = limpia;
        a.target = "_blank";
        a.rel = "noopener noreferrer";
        resultado.appendChild(a);
      } else {
        resultado.appendChild(document.createTextNode(linea));
      }
      if (i < lineas.length - 1) resultado.appendChild(document.createTextNode("\n"));
    });
  }

  btn.addEventListener("click", async function () {
    btn.disabled = true;
    resultado.className = "cargando";
    resultado.innerHTML = '<span class="spinner"></span>Buscando y resumiendo noticias...';

    try {
      const respuesta = await fetch(WEBHOOK_URL, {
        method: "POST",
        body: new URLSearchParams({ tema: selTema.value })
      });
      if (!respuesta.ok) throw new Error("HTTP " + respuesta.status);
      const texto = await respuesta.text();
      if (!texto.trim()) throw new Error("Respuesta vacía");
      mostrarTexto(texto);
    } catch (e) {
      resultado.className = "error";
      resultado.textContent = "No se pudieron obtener las noticias, intenta de nuevo.";
    } finally {
      btn.disabled = false;
    }
  });
</script>
</body>
</html>
