# PLV3-Avance-terminaciones-<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Avance terminaciones PLV3</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
  body {
    font-family: Arial, sans-serif;
    background: #f4f4f4;
    display: flex;
    justify-content: center;
    padding: 20px;
  }
  .container {
    background: white;
    padding: 20px;
    border-radius: 10px;
    width: 950px;
    max-height: 90vh;
    overflow-y: scroll;
    box-shadow: 0 0 15px #ccc;
  }
  h1 {
    text-align: center;
    color: #333;
    margin-bottom: 10px;
  }
  #contador {
    font-weight: bold;
    text-align: center;
    margin-bottom: 10px;
  }
  .barraContainer {
    background: #e0e0e0;
    border-radius: 10px;
    height: 20px;
    width: 100%;
    margin-bottom: 10px;
    overflow: hidden;
  }
  .barra {
    height: 100%;
    width: 0%;
    text-align: center;
    color: white;
    font-weight: bold;
    line-height: 20px;
  }
  .tabs {
    display: flex;
    justify-content: center;
    margin: 15px 0;
  }
  .tab {
    padding: 10px 20px;
    border: 1px solid #ccc;
    cursor: pointer;
    background: #eee;
    margin: 0 5px;
    border-radius: 5px 5px 0 0;
  }
  .tab.active {
    background: #ddd;
    font-weight: bold;
  }
  .view { display: none; }
  .view.active { display: block; }
  .departamento {
    padding: 5px;
    margin: 5px 0;
    border-radius: 5px;
    background: #eee;
  }
  .tareasContainer {
    margin-left: 15px;
    display: none;
  }
  .progressBarDepto {
    background: #ccc;
    border-radius: 5px;
    height: 15px;
    width: 100%;
    margin-top: 5px;
    overflow: hidden;
  }
  .progressFill {
    height: 100%;
    text-align: center;
    color: white;
    font-size: 12px;
    line-height: 15px;
  }
  #graficoContainer {
    width: 100%;
    max-width: 700px;
    margin: 20px auto;
  }
  table {
    border-collapse: collapse;
    width: 100%;
  }
  th, td {
    border: 1px solid #ccc;
    padding: 6px 8px;
    text-align: left;
  }
  th { background: #eee; }
</style>
</head>
<body>
<div class="container">
  <h1>Avance terminaciones PLV3</h1>
  <div id="contador">Resueltos: 0 / 430 (0%)</div>
  <div class="barraContainer"><div id="barraTotal" class="barra">0%</div></div>

  <!-- Pestañas -->
  <div class="tabs">
    <div class="tab active" data-tab="departamentos">🏢 Departamentos</div>
    <div class="tab" data-tab="torres">📊 Torres</div>
    <div class="tab" data-tab="tareas">🧾 Tareas</div>
  </div>

  <!-- VISTA DEPARTAMENTOS -->
  <div id="view-departamentos" class="view active">
    <div>
      <input type="text" id="buscarInput" placeholder="Buscar departamento...">
      <select id="filtro">
        <option value="todos">Todos</option>
        <option value="resueltos">Resueltos</option>
        <option value="noResueltos">No resueltos</option>
      </select>
      <select id="torreFiltro">
        <option value="todas">Todas las torres</option>
      </select>
      <button id="exportarBtn">Exportar CSV</button>
      <button id="guardarBtn">💾 Guardar datos</button>
    </div>
    <div id="listaDepartamentos"></div>
  </div>

  <!-- VISTA TORRES -->
  <div id="view-torres" class="view">
    <div id="graficoContainer"><canvas id="graficoTorre"></canvas></div>
  </div>

  <!-- VISTA TAREAS -->
  <div id="view-tareas" class="view">
    <h3>Resumen de tareas completadas</h3>
    <table id="tablaTareas">
      <thead>
        <tr><th>Tarea</th><th>Completadas</th><th>Total</th><th>%</th></tr>
      </thead>
      <tbody></tbody>
    </table>
  </div>
</div>

<script>
const tareas = [
  "Limpieza y Reparación fisuras OG", "Descarachado y pulido Departamento",
  "Descarachado y pulido baño y cocina", "Trazado Barandas",
  "Instalación barandas departamentos", "Rectificación de centros", "Remates Húmedos Baño y Cocina",
  "Yeso en recinto húmedos", "Rasgos de puertas y ventanas", "Trazado pasa buques",
  "Moldaje pasa buque", "Hormigonado pasa buque", "Trazado Tabiques",
  "Descargas sanitarias sector baños", "Descargas sanitarias sector logia",
  "Distribución agua potable logia y cocina", "Estructurado de tabiques",
  "Tabique primera cara", "Distribución agua potable baños",
  "Distribución eléctricas en tabiques", "Enlauchado eléctrico",
  "EIFS en rasgo de ventanas", "Segunda Cara", "Pasta en cielo y muro",
  "Primera mano pintura", "Estructuración soporte tina", "Impermeabilización baños y cocinas",
  "Impermeabilización terrazas", "Instalación de tinas", "Nivelación piso logia y terraza",
  "Instalación cerámicas piso", "Instalación cerámicas muros", "Instalación de Guardapolvos recintos húmedos",
  "Fragüe ceramicos piso y muro", "Instalación guardapolvos mdf", "Instalación de cornisas",
  "Instalación artefactos sanitarios", "Instalación de alfeizar", "Instalar ventanas",
  "Instalación de marcos", "Instalación de puertas", "Instalación topes de puertas",
  "Instalación de mueble lavaplatos", "Instalación de quincallería",
  "Pintura esmalte sintetico barandas", "Instalación cenefas", "Instalación Celosias metalicas",
  "Ejecución alambrado electricos departamentos", "Instalación artefactos electricos",
  "Instalación griferías", "Pintado logia", "Pintura texturizado logia",
  "Instalación de tubería de gas", "Instalación de nicho de gas",
  "Instalación Calefón", "Instalación atril lavadero", "Instalación lavadero",
  "Pintura terraza", "Pintura texturizado terraza", "Segunda mano pintura recintos húmedos",
  "Ejecución cortagotera", "Sellos elementos estructurales", "Aseo Final"
];

const torres = [
  { nombre: 'Torre 1', cantidad: 30 }, { nombre: 'Torre 2', cantidad: 20 },
  { nombre: 'Torre 3', cantidad: 30 }, { nombre: 'Torre 4', cantidad: 30 },
  { nombre: 'Torre 5', cantidad: 30 }, { nombre: 'Torre 6', cantidad: 30 },
  { nombre: 'Torre 7', cantidad: 30 }, { nombre: 'Torre 8', cantidad: 30 },
  { nombre: 'Torre 9', cantidad: 20 }, { nombre: 'Torre 10', cantidad: 30 },
  { nombre: 'Torre 11', cantidad: 30 }, { nombre: 'Torre 12', cantidad: 30 },
  { nombre: 'Torre 13', cantidad: 30 }, { nombre: 'Torre 14', cantidad: 30 },
  { nombre: 'Torre 15', cantidad: 30 }
];

let departamentos = JSON.parse(localStorage.getItem('departamentos')) || [];
if (departamentos.length === 0) {
  let id = 1;
  torres.forEach(torre => {
    for (let i = 1; i <= torre.cantidad; i++) {
      const deptoTareas = tareas.map(t => ({ nombre: t, resuelto: false }));
      departamentos.push({ id: id++, torre: torre.nombre, tareas: deptoTareas });
    }
  });
  localStorage.setItem('departamentos', JSON.stringify(departamentos));
}

const barraTotal = document.getElementById('barraTotal');
const contador = document.getElementById('contador');
const lista = document.getElementById('listaDepartamentos');
const buscarInput = document.getElementById('buscarInput');
const filtro = document.getElementById('filtro');
const torreFiltro = document.getElementById('torreFiltro');
const exportarBtn = document.getElementById('exportarBtn');
const guardarBtn = document.getElementById('guardarBtn');
const tablaTareas = document.getElementById('tablaTareas').querySelector('tbody');

function colorPorcentaje(p) {
  const r = p < 50 ? 255 : Math.round(255 - ((p - 50) * 5.1));
  const g = p > 50 ? 255 : Math.round(p * 5.1);
  return `rgb(${r},${g},0)`;
}
function guardar() { localStorage.setItem('departamentos', JSON.stringify(departamentos)); }

function actualizarContador() {
  const total = departamentos.length * tareas.length;
  const resueltos = departamentos.reduce((s, d) => s + d.tareas.filter(t => t.resuelto).length, 0);
  const p = ((resueltos / total) * 100).toFixed(1);
  contador.textContent = `Resueltos: ${resueltos} / ${total} (${p}%)`;
  barraTotal.style.width = p + "%";
  barraTotal.style.background = colorPorcentaje(p);
  barraTotal.textContent = p + "%";
}

function actualizarTablaTareas() {
  tablaTareas.innerHTML = "";
  tareas.forEach(t => {
    const completadas = departamentos.filter(d => d.tareas.find(x => x.nombre === t && x.resuelto)).length;
    const total = departamentos.length;
    const p = ((completadas / total) * 100).toFixed(1);
    const row = `<tr><td>${t}</td><td>${completadas}</td><td>${total}</td><td>${p}%</td></tr>`;
    tablaTareas.insertAdjacentHTML('beforeend', row);
  });
}

function renderDepartamentos() {
  lista.innerHTML = "";
  const busqueda = buscarInput.value.toLowerCase();
  const filtroValor = filtro.value;
  const torreSeleccionada = torreFiltro.value;

  departamentos.forEach(depto => {
    const completadas = depto.tareas.filter(t => t.resuelto).length;
    const porcentaje = (completadas / tareas.length) * 100;
    const visible =
      (busqueda === "" || depto.id.toString().includes(busqueda)) &&
      (filtroValor === "todos" ||
        (filtroValor === "resueltos" && completadas === tareas.length) ||
        (filtroValor === "noResueltos" && completadas < tareas.length)) &&
      (torreSeleccionada === "todas" || torreSeleccionada === depto.torre);

    if (!visible) return;

    const div = document.createElement("div");
    div.className = "departamento";
    div.innerHTML = `
      <b>${depto.torre} - Depto ${depto.id}</b>
      <div class="progressBarDepto">
        <div class="progressFill" style="width:${porcentaje}%;background:${colorPorcentaje(porcentaje)}">
          ${porcentaje.toFixed(1)}%
        </div>
      </div>
      <button class="toggleTareas">Ver tareas</button>
      <div class="tareasContainer">
        ${depto.tareas.map((t, i) => `
          <div>
            <input type="checkbox" data-depto="${depto.id}" data-index="${i}" ${t.resuelto ? "checked" : ""}>
            ${t.nombre}
          </div>`).join("")}
      </div>
    `;
    lista.appendChild(div);
  });

  lista.querySelectorAll(".toggleTareas").forEach(btn => {
    btn.addEventListener("click", e => {
      const cont = e.target.nextElementSibling;
      cont.style.display = cont.style.display === "none" || cont.style.display === "" ? "block" : "none";
    });
  });

  lista.querySelectorAll("input[type=checkbox]").forEach(chk => {
    chk.addEventListener("change", e => {
      const d = departamentos.find(x => x.id == e.target.dataset.depto);
      d.tareas[e.target.dataset.index].resuelto = e.target.checked;
      guardar();
      actualizarContador();
      renderDepartamentos();
    });
  });
}

function actualizarGraficoTorres() {
  const ctx = document.getElementById('graficoTorre').getContext('2d');
  const datos = torres.map(t => {
    const dptos = departamentos.filter(d => d.torre === t.nombre);
    const totalTareas = dptos.length * tareas.length;
    const resueltas = dptos.reduce((s, d) => s + d.tareas.filter(t => t.resuelto).length, 0);
    return (resueltas / totalTareas) * 100;
  });

  if (window.graficoTorres) window.graficoTorres.destroy();
  window.graficoTorres = new Chart(ctx, {
    type: 'bar',
    data: {
      labels: torres.map(t => t.nombre),
      datasets: [{
        label: '% Avance por Torre',
        data: datos,
        backgroundColor: datos.map(p => colorPorcentaje(p))
      }]
    },
    options: {
      scales: { y: { beginAtZero: true, max: 100 } },
      plugins: { legend: { display: false } }
    }
  });
}

function cambiarVista(tab) {
  document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
  document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
  document.querySelector(`.tab[data-tab="${tab}"]`).classList.add('active');
  document.getElementById(`view-${tab}`).classList.add('active');
  if (tab === "tareas") actualizarTablaTareas();
  if (tab === "torres") actualizarGraficoTorres();
}

document.querySelectorAll('.tab').forEach(t => {
  t.addEventListener('click', () => cambiarVista(t.dataset.tab));
});

torres.forEach(t => {
  const opt = document.createElement("option");
  opt.value = t.nombre;
  opt.textContent = t.nombre;
  torreFiltro.appendChild(opt);
});

[buscarInput, filtro, torreFiltro].forEach(el => el.addEventListener("input", renderDepartamentos));

actualizarContador();
actualizarTablaTareas();
renderDepartamentos();
</script>
</body>
</html>
