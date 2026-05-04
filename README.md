[index.html.html](https://github.com/user-attachments/files/27362762/index.html.html)
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Tablero de Control de Tiempos — CD Taco Bell Guatemala 2026</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-database-compat.js"></script>
  <style>
    @keyframes spin{to{transform:rotate(360deg);}}
    /* ── Variables de diseño ── */
    :root {
      --color-background-primary:   #ffffff;
      --color-background-secondary: #f5f5f7;
      --color-text-primary:         #1d1d1f;
      --color-text-secondary:       #6e6e73;
      --color-text-tertiary:        #aeaeb2;
      --color-border-tertiary:      #e5e5ea;
      --border-radius-lg:           12px;
      --border-radius-md:           8px;
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
    }

    /* ── Reset y base ── */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      background: #f0f0f5;
      min-height: 100vh;
      padding: 20px;
      color: var(--color-text-primary);
    }

    /* ── Contenedor principal ── */
    .wrapper {
      max-width: 960px;
      margin: 0 auto;
      background: var(--color-background-primary);
      border-radius: var(--border-radius-lg);
      box-shadow: 0 2px 20px rgba(0,0,0,0.10);
      overflow: hidden;
    }

    /* ── Estilos de formulario nativos ── */
    select, input[type="text"], input[type="number"] {
      font-family: inherit;
      font-size: 12px;
      padding: 6px 10px;
      border: 1px solid var(--color-border-tertiary);
      border-radius: 6px;
      background: var(--color-background-primary);
      color: var(--color-text-primary);
      outline: none;
      width: 100%;
    }
    select:focus, input:focus {
      border-color: #702082;
      box-shadow: 0 0 0 2px rgba(112,32,130,0.15);
    }
    button {
      font-family: inherit;
      cursor: pointer;
    }

    /* ── Componentes del tablero ── */
    .sr-only{position:absolute;width:1px;height:1px;overflow:hidden;clip:rect(0,0,0,0);}
    .hd{background:#702082;padding:13px 18px;display:flex;align-items:center;gap:13px;}
    .logo{background:#FFC72C;width:36px;height:36px;border-radius:8px;display:flex;align-items:center;justify-content:center;font-weight:600;font-size:13px;color:#702082;flex-shrink:0;}
    .tabs{display:flex;background:var(--color-background-secondary);border-bottom:2px solid rgba(112,32,130,0.3);padding:0 14px;gap:2px;flex-wrap:wrap;}
    .tb{padding:9px 14px;border:none;background:transparent;cursor:pointer;font-size:12px;font-weight:400;color:var(--color-text-secondary);border-bottom:2.5px solid transparent;margin-bottom:-2px;border-radius:4px 4px 0 0;transition:color .15s,background .15s;}
    .tb:hover{color:#702082;background:rgba(112,32,130,0.06);}
    .tb.on{color:#702082;border-bottom-color:#702082;font-weight:500;}
    .pnl{padding:14px;display:none;}
    .pnl.on{display:block;}
    .k6{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:10px;margin-bottom:14px;}
    .kc{background:var(--color-background-secondary);border-radius:var(--border-radius-md);padding:12px 13px;}
    .kt{display:flex;align-items:center;justify-content:space-between;margin-bottom:4px;}
    .kl{font-size:11px;color:var(--color-text-secondary);}
    .kb{width:28px;height:28px;border-radius:6px;display:flex;align-items:center;justify-content:center;flex-shrink:0;}
    .kv{font-size:22px;font-weight:500;color:var(--color-text-primary);line-height:1;}
    .ks{font-size:11px;color:var(--color-text-tertiary);margin-top:3px;}
    .kbar{height:3px;border-radius:2px;margin-top:8px;background:var(--color-border-tertiary);}
    .kfill{height:3px;border-radius:2px;}
    .g2{display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-bottom:12px;}
    .cd{background:var(--color-background-primary);border:0.5px solid var(--color-border-tertiary);border-radius:var(--border-radius-lg);padding:13px;margin-bottom:12px;}
    .ct{font-size:12px;font-weight:500;color:var(--color-text-primary);margin-bottom:4px;}
    .cs{font-size:11px;color:var(--color-text-secondary);margin-bottom:10px;}
    .leg{display:flex;gap:14px;font-size:11px;color:var(--color-text-secondary);flex-wrap:wrap;margin-bottom:10px;}
    .lsq{width:10px;height:10px;border-radius:2px;display:inline-block;vertical-align:middle;margin-right:3px;}
    .ldot{width:10px;height:10px;border-radius:50%;display:inline-block;vertical-align:middle;margin-right:3px;}
    .tw{overflow-x:auto;border:0.5px solid var(--color-border-tertiary);border-radius:var(--border-radius-lg);}
    table{width:100%;border-collapse:collapse;font-size:12px;}
    th{background:#702082;color:white;padding:7px 10px;text-align:left;font-weight:500;font-size:11px;white-space:nowrap;}
    td{padding:7px 10px;border-bottom:0.5px solid var(--color-border-tertiary);color:var(--color-text-primary);vertical-align:middle;}
    tr:last-child td{border-bottom:none;}
    tr:hover td{background:rgba(112,32,130,0.04);}
    .dot{width:8px;height:8px;border-radius:50%;display:inline-block;}
    .dg{background:#27AE60;}.dy{background:#F39C12;}.dr{background:#E74C3C;}
    .pill{background:rgba(112,32,130,0.1);color:#702082;padding:2px 7px;border-radius:8px;font-size:11px;font-weight:500;}
    .badge{display:inline-block;padding:2px 7px;border-radius:4px;font-size:11px;font-weight:500;}
    .fg{display:flex;flex-direction:column;gap:3px;}
    .fg label{font-size:11px;color:var(--color-text-secondary);font-weight:500;}
    .fg3{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:10px;margin-bottom:10px;}
    .fg2{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-bottom:10px;}
    .mc{background:var(--color-background-primary);border:0.5px solid var(--color-border-tertiary);border-radius:var(--border-radius-lg);padding:13px;margin-bottom:10px;display:flex;gap:12px;}
    .mi{width:34px;height:34px;border-radius:8px;display:flex;align-items:center;justify-content:center;flex-shrink:0;font-size:13px;font-weight:500;}
    .sep{height:0.5px;background:var(--color-border-tertiary);margin:4px 0 8px;}
    .insight{background:rgba(112,32,130,0.05);border-left:3px solid #702082;border-radius:0 var(--border-radius-md) var(--border-radius-md) 0;padding:10px 13px;margin-bottom:12px;font-size:12px;color:var(--color-text-secondary);}
    .insight strong{color:#702082;font-weight:500;}

    /* ── Responsive básico ── */
    @media (max-width: 600px) {
      body { padding: 8px; }
      .k6 { grid-template-columns: repeat(2,minmax(0,1fr)); }
      .g2 { grid-template-columns: 1fr; }
      .fg3 { grid-template-columns: 1fr 1fr; }
    }
  </style>
</head>
<body>
<div class="wrapper">

<h2 class="sr-only">Tablero de control avanzado de tiempos operacionales — CD Taco Bell Guatemala 2026</h2>

<div class="hd">
  <div class="logo">TB</div>
  <div style="flex:1;">
    <p style="color:white;font-weight:500;font-size:13px;">Centro de Distribución — Taco Bell Guatemala</p>
    <p style="color:rgba(255,255,255,0.6);font-size:11px;margin-top:1px;">Tablero de control de tiempos · Supply Chain 2026</p>
  </div>
  <div style="background:rgba(255,255,255,0.12);padding:4px 11px;border-radius:6px;font-size:11px;color:rgba(255,255,255,0.9);">
    <span style="width:6px;height:6px;border-radius:50%;background:#FFC72C;display:inline-block;margin-right:5px;vertical-align:middle;"></span><span id="ts">--</span>
  </div>
  <div id="edit_badge" style="display:none;background:rgba(255,199,44,0.2);border:1px solid rgba(255,199,44,0.6);padding:4px 10px;border-radius:6px;font-size:11px;color:#FFC72C;align-items:center;gap:5px;">
    <svg width="11" height="11" fill="none" viewBox="0 0 24 24"><path d="M12 2a5 5 0 0 1 5 5v3H7V7a5 5 0 0 1 5-5Z" fill="#FFC72C"/><rect x="5" y="11" width="14" height="10" rx="2" fill="#FFC72C"/></svg>
    Editor activo
  </div>
</div>

<div class="tabs">
  <button class="tb on" onclick="gt('dash',this)">Dashboard</button>
  <button class="tb" onclick="gt('comp',this)">Comparativas</button>
  <button class="tb" onclick="gt('reg',this)">Registro</button>
  <button class="tb" onclick="gt('trec',this)">Recepción</button>
  <button class="tb" onclick="gt('mej',this)">Mejoras</button>
</div>

<!-- DASHBOARD -->
<div id="pdash" class="pnl on">
  <div class="k6" id="kpis"></div>

  <!-- ── TABLERO DE METAS ── -->
  <div class="cd" style="margin-bottom:12px;">
    <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:8px;flex-wrap:wrap;gap:8px;">
      <div>
        <p class="ct" style="margin-bottom:2px;">🎯 Tablero de metas — Tiempos objetivo por operación</p>
        <p class="cs">Metas Supply Chain 2026 · basadas en análisis lean de mejores tiempos registrados · actualizado con datos Marzo–Abril</p>
      </div>
      <div style="background:rgba(255,199,44,0.15);border:1px solid rgba(255,199,44,0.5);border-radius:6px;padding:4px 10px;font-size:11px;color:#7a5c00;font-weight:500;">⚡ Metas revisadas por Claude Supply Chain</div>
    </div>
    <div id="meta_grid" style="display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:8px;"></div>
    <div style="margin-top:10px;padding:8px 12px;background:rgba(112,32,130,0.04);border-radius:8px;font-size:11px;color:var(--color-text-secondary);line-height:1.6;" id="meta_nota"></div>
  </div>

  <div class="cd">
    <p class="ct">Ranking de operaciones por tiempo promedio</p>
    <p class="cs">Barras ordenadas de menor a mayor · <span style="color:#27AE60;font-weight:500;">■ verde</span> = dentro de meta · <span style="color:#E67E22;font-weight:500;">■ naranja</span> = hasta 30% sobre meta · <span style="color:#E74C3C;font-weight:500;">■ rojo</span> = excede meta · <span style="color:#FFC72C;font-weight:600;">- - -</span> línea amarilla = meta objetivo</p>
    <div style="position:relative;height:260px;">
      <canvas id="c_rank" role="img" aria-label="Ranking horizontal de todas las operaciones por tiempo promedio">Ranking de tiempos promedio por operación.</canvas>
    </div>
  </div>

  <div class="g2">
    <div class="cd" style="margin-bottom:0;">
      <p class="ct">Tendencia semanal de carga</p>
      <p class="cs">Promedio por semana · ambos tipos de vehículo</p>
      <div class="leg">
        <span><span class="lsq" style="background:#702082;"></span>Camión 10-12t</span>
        <span><span class="lsq" style="background:#EB008B;border:2px dashed #EB008B;background:transparent;"></span>Camión 5.4t</span>
      </div>
      <div style="position:relative;height:200px;">
        <canvas id="c_trend" role="img" aria-label="Tendencia semanal de tiempos de carga por tipo de vehículo">Tendencia semanal de carga.</canvas>
      </div>
    </div>
    <div class="cd" style="margin-bottom:0;">
      <p class="ct">Distribución de operaciones registradas</p>
      <p class="cs">Mix de carga vs descarga en el período</p>
      <div id="leg_donut" class="leg" style="margin-bottom:8px;"></div>
      <div style="position:relative;height:200px;">
        <canvas id="c_donut" role="img" aria-label="Gráfica de dona mostrando distribución de operaciones de carga y descarga">Distribución carga vs descarga.</canvas>
      </div>
    </div>
  </div>
</div><!-- /#pdash -->

<!-- COMPARATIVAS -->
<div id="pcomp" class="pnl">
  <div class="insight" id="insight_var"></div>

  <!-- ── NUEVA: Carga vs Descarga comparativa general ── -->
  <div class="cd">
    <p class="ct">Carga vs Descarga — Comparativa general de tiempos promedio</p>
    <p class="cs">Todos los vehículos del CD · Morado = operaciones de CARGA · Rosa = operaciones de DESCARGA · Línea amarilla = meta objetivo individual</p>
    <div class="leg">
      <span><span class="lsq" style="background:#702082;"></span>Carga (Camión 10-12t / 5.4t)</span>
      <span><span class="lsq" style="background:#EB008B;"></span>Descarga (Furgones / Granel / Pallets)</span>
      <span><span class="lsq" style="background:#FFC72C;"></span>Meta objetivo</span>
    </div>
    <div style="position:relative;height:290px;">
      <canvas id="c_comp_gen" role="img" aria-label="Gráfica comparativa carga vs descarga por tipo de vehículo con meta objetivo">Comparativa general carga vs descarga.</canvas>
    </div>
  </div>

  <!-- ══ SECCIÓN CARGA ══ -->
  <div style="display:flex;align-items:center;gap:10px;margin:18px 0 12px;">
    <div style="height:2px;flex:1;background:rgba(112,32,130,0.18);border-radius:2px;"></div>
    <span style="background:#702082;color:white;padding:5px 16px;border-radius:20px;font-size:12px;font-weight:600;letter-spacing:0.5px;display:flex;align-items:center;gap:6px;">
      <svg width="13" height="13" viewBox="0 0 14 14" fill="none"><rect x="1" y="5" width="12" height="7" rx="2" stroke="white" stroke-width="1.4"/><path d="M4 5V4a3 3 0 016 0v1" stroke="white" stroke-width="1.4"/></svg>
      CARGA
    </span>
    <div style="height:2px;flex:1;background:rgba(112,32,130,0.18);border-radius:2px;"></div>
  </div>
  <p style="font-size:11px;color:var(--color-text-secondary);margin-bottom:10px;padding-left:2px;">Vehículos de carga: <strong>Camión 10-12t</strong> y <strong>Camión 5.4t</strong> — distribución a restaurantes desde el CD</p>

  <div class="cd">
    <p class="ct">Variabilidad de CARGA — rango min / promedio / máx</p>
    <p class="cs">Cuanto más corta la barra de rango, más controlada y predecible es la operación de carga</p>
    <div class="leg">
      <span><span class="lsq" style="background:rgba(112,32,130,0.2);"></span>Rango min–máx</span>
      <span><span class="lsq" style="background:#702082;"></span>Promedio</span>
      <span><span class="lsq" style="background:#FFC72C;"></span>Meta objetivo</span>
    </div>
    <div style="position:relative;height:210px;">
      <canvas id="c_var_carga" role="img" aria-label="Variabilidad de operaciones de carga por tipo de camión">Variabilidad operaciones de carga.</canvas>
    </div>
  </div>

  <!-- ══ SECCIÓN DESCARGA ══ -->
  <div style="display:flex;align-items:center;gap:10px;margin:18px 0 12px;">
    <div style="height:2px;flex:1;background:rgba(235,0,139,0.18);border-radius:2px;"></div>
    <span style="background:#EB008B;color:white;padding:5px 16px;border-radius:20px;font-size:12px;font-weight:600;letter-spacing:0.5px;display:flex;align-items:center;gap:6px;">
      <svg width="13" height="13" viewBox="0 0 14 14" fill="none"><path d="M7 2v8M4 7l3 3 3-3" stroke="white" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/><path d="M2 12h10" stroke="white" stroke-width="1.4" stroke-linecap="round"/></svg>
      DESCARGA
    </span>
    <div style="height:2px;flex:1;background:rgba(235,0,139,0.18);border-radius:2px;"></div>
  </div>
  <p style="font-size:11px;color:var(--color-text-secondary);margin-bottom:10px;padding-left:2px;">Vehículos de descarga: <strong>Furgón 22 tar.</strong> · <strong>Furgón 26 tar.</strong> · <strong>Camión granel</strong> · <strong>Camión pallets</strong> — recepción de proveedores</p>

  <div class="g2">
    <div class="cd" style="margin-bottom:0;">
      <p class="ct">Variabilidad de DESCARGA — rango min / promedio / máx</p>
      <p class="cs">Furgones, granel y pallets · cuanto más corta la barra, más estable la recepción</p>
      <div class="leg">
        <span><span class="lsq" style="background:rgba(235,0,139,0.2);"></span>Rango min–máx</span>
        <span><span class="lsq" style="background:#EB008B;"></span>Promedio</span>
        <span><span class="lsq" style="background:#FFC72C;"></span>Meta</span>
      </div>
      <div style="position:relative;height:230px;">
        <canvas id="c_var_desc" role="img" aria-label="Variabilidad de operaciones de descarga por tipo de vehículo">Variabilidad operaciones de descarga.</canvas>
      </div>
    </div>
    <div class="cd" style="margin-bottom:0;">
      <p class="ct">Descarga: Local vs Importado</p>
      <p class="cs">Tiempo promedio de descarga según origen del proveedor</p>
      <div class="leg">
        <span><span class="lsq" style="background:#702082;"></span>Local</span>
        <span><span class="lsq" style="background:#EB008B;"></span>Importado</span>
      </div>
      <div style="position:relative;height:230px;">
        <canvas id="c_lvi" role="img" aria-label="Comparativa de tiempos de descarga entre proveedores locales e importados">Comparativa local vs importado.</canvas>
      </div>
    </div>
  </div>

  <!-- ══ ANÁLISIS CONJUNTO ══ -->
  <div style="display:flex;align-items:center;gap:10px;margin:18px 0 12px;">
    <div style="height:2px;flex:1;background:rgba(0,0,0,0.1);border-radius:2px;"></div>
    <span style="background:#1d1d1f;color:white;padding:5px 16px;border-radius:20px;font-size:12px;font-weight:600;letter-spacing:0.5px;display:flex;align-items:center;gap:6px;">
      <svg width="13" height="13" viewBox="0 0 14 14"><rect x="2" y="6" width="3" height="7" rx="1" fill="white"/><rect x="6" y="3" width="3" height="10" rx="1" fill="white"/><rect x="10" y="1" width="3" height="12" rx="1" fill="white"/></svg>
      ANÁLISIS CONJUNTO
    </span>
    <div style="height:2px;flex:1;background:rgba(0,0,0,0.1);border-radius:2px;"></div>
  </div>

  <div class="g2">
    <div class="cd" style="margin-bottom:0;">
      <p class="ct">Dispersión temporal de todos los registros</p>
      <p class="cs">Cada punto = una operación registrada · eje x = semana</p>
      <div id="leg_scatter" class="leg" style="margin-bottom:8px;"></div>
      <div style="position:relative;height:240px;">
        <canvas id="c_scat" role="img" aria-label="Gráfica de dispersión de todos los registros de tiempo por semana y tipo de vehículo">Dispersión temporal de registros.</canvas>
      </div>
    </div>
    <div class="cd" style="margin-bottom:0;">
      <p class="ct">Cumplimiento de meta por operación</p>
      <p class="cs">% de registros dentro del tiempo objetivo — línea punteada = meta del 80% de cumplimiento</p>
      <div style="position:relative;height:240px;">
        <canvas id="c_cump" role="img" aria-label="Porcentaje de operaciones que cumplen la meta de tiempo por tipo de vehículo">Cumplimiento de meta por operación.</canvas>
      </div>
    </div>
  </div>
</div>

<!-- REGISTRO -->
<div id="preg" class="pnl">
  <div style="display:flex;gap:8px;align-items:center;margin-bottom:12px;flex-wrap:wrap;">
    <select id="fm" onchange="rt()" style="width:auto;"><option value="">Todos los meses</option><option>Marzo</option><option>Abril</option></select>
    <select id="ft" onchange="rt()" style="width:auto;"><option value="">Carga y descarga</option><option>Carga</option><option>Descarga</option></select>
    <select id="fv" onchange="rt()" style="width:auto;"><option value="">Todos los vehículos</option><option>Camión 10-12t</option><option>Camión 5.4t</option><option>Furgón 22 tar.</option><option>Furgón 26 tar.</option><option>Camión granel</option><option>Camión pallets</option></select>
    <span style="margin-left:auto;font-size:11px;color:var(--color-text-tertiary);" id="rc"></span>
    <button onclick="tf()" style="background:#702082;color:white;border:none;padding:7px 14px;border-radius:6px;cursor:pointer;font-size:12px;font-weight:500;">+ Nuevo registro</button>
  </div>
  <div id="af" style="display:none;background:rgba(112,32,130,0.05);border:1px solid rgba(112,32,130,0.2);border-radius:10px;padding:14px;margin-bottom:12px;">
    <p style="font-weight:500;font-size:13px;margin-bottom:12px;color:#702082;">Agregar nuevo registro</p>
    <div class="fg3">
      <div class="fg"><label>Mes</label><select id="nm"><option>Marzo</option><option>Abril</option><option>Mayo</option><option>Junio</option><option>Julio</option><option>Agosto</option><option>Septiembre</option></select></div>
      <div class="fg"><label>Semana</label><input type="text" id="ns" placeholder="Ej: S-07"></div>
      <div class="fg"><label>Tipo de operación</label><select id="nt" onchange="uv()"><option>Carga</option><option>Descarga</option></select></div>
    </div>
    <div class="fg3">
      <div class="fg"><label>Vehículo</label><select id="nv"><option>Camión 10-12t</option><option>Camión 5.4t</option></select></div>
      <div class="fg"><label>Tiempo (min)</label><input type="number" id="nti" placeholder="Ej: 25.5" step="0.01" min="0"></div>
      <div class="fg"><label>Personas</label><input type="number" id="np" placeholder="Ej: 3" min="1"></div>
    </div>
    <div class="fg2">
      <div class="fg"><label>Tipo proveedor</label><select id="npr"><option value="">N/A</option><option>Local</option><option>Importado</option></select></div>
      <div class="fg"><label>Observaciones</label><input type="text" id="no" placeholder="Incidencias, condiciones..."></div>
    </div>
    <div style="display:flex;gap:8px;justify-content:flex-end;margin-top:10px;">
      <button onclick="tf()" style="font-size:12px;padding:6px 14px;border:1px solid var(--color-border-tertiary);border-radius:6px;background:var(--color-background-secondary);color:var(--color-text-secondary);">Cancelar</button>
      <button onclick="ar()" style="background:#702082;color:white;border:none;padding:6px 16px;border-radius:6px;cursor:pointer;font-size:12px;font-weight:500;">Guardar</button>
    </div>
  </div>
  <div class="tw">
    <table>
      <thead><tr><th>#</th><th>Mes</th><th>Sem.</th><th>Tipo</th><th>Vehículo</th><th>Tiempo</th><th>Estado</th><th>Proveedor</th><th>Pers.</th><th>Observaciones</th><th></th></tr></thead>
      <tbody id="tb"></tbody>
    </table>
  </div>
  <div style="margin-top:10px;padding:8px 12px;background:var(--color-background-secondary);border-radius:var(--border-radius-md);display:flex;align-items:center;gap:14px;">
    <p style="font-size:11px;color:var(--color-text-secondary);">Semáforo:</p>
    <span style="font-size:11px;color:var(--color-text-secondary);display:flex;align-items:center;gap:4px;"><span class="dot dg"></span>Dentro del objetivo</span>
    <span style="font-size:11px;color:var(--color-text-secondary);display:flex;align-items:center;gap:4px;"><span class="dot dy"></span>Atención</span>
    <span style="font-size:11px;color:var(--color-text-secondary);display:flex;align-items:center;gap:4px;"><span class="dot dr"></span>Excede meta</span>
    <span style="margin-left:auto;font-size:11px;color:var(--color-text-tertiary);" id="rc2"></span>
  </div>
</div>

<!-- RECEPCIÓN / TRASIEGO -->
<div id="ptrec" class="pnl">
  <div style="background:#702082;color:white;border-radius:10px;padding:14px 16px;margin-bottom:14px;">
    <p style="font-weight:500;font-size:14px;">Tiempos de Recepción — Trasiego de Producto</p>
    <p style="font-size:11px;opacity:0.7;margin-top:3px;">Registro de tiempos por tipo de producto · Camión 5.4 ton y Panel · CD Taco Bell Guatemala 2026</p>
  </div>

  <div class="k6" id="trec_kpis" style="grid-template-columns:repeat(4,minmax(0,1fr));"></div>

  <div class="g2" style="margin-bottom:12px;">
    <div class="cd" style="margin-bottom:0;">
      <p class="ct">Tiempo de trasiego: Congelado vs Refrigerado</p>
      <p class="cs">Tiempo promedio por tipo de producto registrado</p>
      <div class="leg">
        <span><span class="lsq" style="background:#1A6BAE;"></span>Congelado</span>
        <span><span class="lsq" style="background:#27AE60;"></span>Refrigerado</span>
      </div>
      <div style="position:relative;height:220px;">
        <canvas id="c_trec_bar"></canvas>
      </div>
    </div>
    <div class="cd" style="margin-bottom:0;">
      <p class="ct">Relación tiempo / cajas cargadas</p>
      <p class="cs">Productividad operacional — cajas por minuto por registro</p>
      <div style="position:relative;height:220px;">
        <canvas id="c_trec_prod"></canvas>
      </div>
    </div>
  </div>

  <div style="display:flex;gap:8px;align-items:center;margin-bottom:10px;flex-wrap:wrap;">
    <span style="font-size:12px;font-weight:500;color:var(--color-text-primary);">Registros de trasiego</span>
    <span style="margin-left:auto;font-size:11px;color:var(--color-text-tertiary);" id="trec_rc"></span>
    <button onclick="ttf()" style="background:#702082;color:white;border:none;padding:7px 14px;border-radius:6px;cursor:pointer;font-size:12px;font-weight:500;">+ Nuevo registro</button>
  </div>

  <div id="trec_af" style="display:none;background:rgba(112,32,130,0.05);border:1px solid rgba(112,32,130,0.2);border-radius:10px;padding:14px;margin-bottom:12px;">
    <p style="font-weight:500;font-size:13px;margin-bottom:12px;color:#702082;">Agregar nuevo registro de trasiego</p>
    <div class="fg3">
      <div class="fg"><label>Mes</label><select id="tnm"><option>Abril</option><option>Mayo</option><option>Junio</option><option>Julio</option><option>Agosto</option><option>Septiembre</option></select></div>
      <div class="fg"><label>T. Congelado (min)</label><input type="number" id="tnc" placeholder="Ej: 33.57" step="0.01" min="0"></div>
      <div class="fg"><label>T. Refrigerado (min)</label><input type="number" id="tnr" placeholder="Ej: 7.00" step="0.01" min="0"></div>
    </div>
    <div class="fg3">
      <div class="fg"><label>Personas en operación</label><input type="number" id="tnp" placeholder="Ej: 5" min="1"></div>
      <div class="fg"><label>Cajas cargadas</label><input type="number" id="tnb" placeholder="Ej: 260" min="1"></div>
      <div class="fg"><label>Tipo de camión</label><select id="tntv"><option>Camión 5.4 ton</option><option>Panel</option><option>Camión 10-12t</option></select></div>
    </div>
    <div style="display:flex;gap:10px;margin-bottom:10px;">
      <div class="fg" style="flex:1;"><label>Observaciones</label><input type="text" id="tno" placeholder="Incidencias, condiciones..."></div>
    </div>
    <div style="display:flex;gap:8px;justify-content:flex-end;margin-top:10px;">
      <button onclick="ttf()" style="font-size:12px;padding:6px 14px;border:1px solid var(--color-border-tertiary);border-radius:6px;background:var(--color-background-secondary);color:var(--color-text-secondary);">Cancelar</button>
      <button onclick="tar()" style="background:#702082;color:white;border:none;padding:6px 16px;border-radius:6px;cursor:pointer;font-size:12px;font-weight:500;">Guardar</button>
    </div>
  </div>

  <div class="tw">
    <table>
      <thead><tr><th>#</th><th>Mes</th><th>T. Congelado</th><th>T. Refrigerado</th><th>Personas</th><th>Cajas</th><th>Cajas/min</th><th>Tipo camión</th><th>Observaciones</th><th></th></tr></thead>
      <tbody id="trec_tb"></tbody>
    </table>
  </div>

  <div class="insight" style="margin-top:12px;" id="trec_insight"></div>
</div>

<!-- MEJORAS -->
<div id="pmej" class="pnl">
  <div style="background:#702082;color:white;border-radius:10px;padding:14px 16px;margin-bottom:14px;">
    <div style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:10px;">
      <div>
        <p style="font-weight:500;font-size:14px;">Plan de mejoras operacionales — metodología Lean</p>
        <p style="font-size:11px;opacity:0.7;margin-top:3px;">Basado en análisis de tiempos registrados en el CD · Ordenado por impacto y prioridad</p>
      </div>
      <button onclick="showAiPanel()" style="background:rgba(255,255,255,0.15);border:1px solid rgba(255,255,255,0.35);color:white;padding:8px 14px;border-radius:8px;font-size:12px;font-family:inherit;cursor:pointer;display:flex;align-items:center;gap:6px;font-weight:500;white-space:nowrap;">
        <svg width="14" height="14" fill="none" viewBox="0 0 24 24"><path d="M12 2a10 10 0 1 0 10 10A10 10 0 0 0 12 2Zm0 18a8 8 0 1 1 8-8 8 8 0 0 1-8 8Zm-1-5h2v2h-2zm0-8h2v6h-2z" fill="white"/></svg>
        🤖 Análisis con IA
      </button>
    </div>
  </div>

  <!-- Panel de Análisis IA -->
  <div id="ai_panel" style="display:none;margin-bottom:14px;">
    <!-- Setup de API Key -->
    <div id="ai_setup" style="background:rgba(112,32,130,0.04);border:1.5px dashed rgba(112,32,130,0.3);border-radius:10px;padding:16px;margin-bottom:12px;">
      <div style="display:flex;align-items:flex-start;gap:12px;flex-wrap:wrap;">
        <div style="flex:1;min-width:200px;">
          <p style="font-weight:500;font-size:13px;color:#702082;margin-bottom:4px;">🔑 API Key de Anthropic (Claude)</p>
          <p style="font-size:11px;color:#6e6e73;margin-bottom:10px;">Obtén tu clave en <strong>console.anthropic.com</strong> → API Keys. Se guarda solo en esta sesión.</p>
          <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap;">
            <input type="password" id="ai_key_input" placeholder="sk-ant-api03-..." style="flex:1;min-width:180px;padding:8px 11px;border:1.5px solid #e5e5ea;border-radius:7px;font-size:12px;font-family:inherit;outline:none;">
            <button onclick="saveApiKey()" style="background:#702082;color:white;border:none;padding:8px 14px;border-radius:7px;font-size:12px;font-family:inherit;cursor:pointer;font-weight:500;white-space:nowrap;">Guardar clave</button>
          </div>
          <p id="ai_key_status" style="font-size:11px;margin-top:6px;color:#aeaeb2;">Sin clave configurada</p>
        </div>
        <div style="background:rgba(255,199,44,0.1);border:1px solid rgba(255,199,44,0.4);border-radius:8px;padding:10px 12px;font-size:11px;color:#7a5c00;max-width:220px;line-height:1.5;">
          <strong>🔒 Privacidad:</strong> La clave NO se guarda en Firebase ni en el archivo. Solo existe en memoria mientras el navegador esté abierto.
        </div>
      </div>
    </div>

    <!-- Botones de análisis -->
    <div id="ai_actions" style="display:none;margin-bottom:12px;">
      <div style="display:flex;gap:8px;flex-wrap:wrap;align-items:center;">
        <button onclick="analyzeWithAI('resumen')" style="background:#702082;color:white;border:none;padding:9px 16px;border-radius:8px;font-size:12px;font-family:inherit;cursor:pointer;font-weight:500;display:flex;align-items:center;gap:6px;">
          📊 Resumen ejecutivo para gerencia
        </button>
        <button onclick="analyzeWithAI('prioridades')" style="background:#EB008B;color:white;border:none;padding:9px 16px;border-radius:8px;font-size:12px;font-family:inherit;cursor:pointer;font-weight:500;display:flex;align-items:center;gap:6px;">
          🎯 Top prioridades esta semana
        </button>
        <button onclick="analyzeWithAI('proveedores')" style="background:#378ADD;color:white;border:none;padding:9px 16px;border-radius:8px;font-size:12px;font-family:inherit;cursor:pointer;font-weight:500;display:flex;align-items:center;gap:6px;">
          🚛 Análisis por proveedor
        </button>
        <button onclick="analyzeWithAI('sop')" style="background:#1D9E75;color:white;border:none;padding:9px 16px;border-radius:8px;font-size:12px;font-family:inherit;cursor:pointer;font-weight:500;display:flex;align-items:center;gap:6px;">
          📋 Recomendaciones SOP
        </button>
        <span style="margin-left:auto;font-size:11px;color:#aeaeb2;" id="ai_key_badge"></span>
      </div>
    </div>

    <!-- Resultado de IA -->
    <div id="ai_result" style="display:none;background:white;border:1px solid rgba(112,32,130,0.2);border-radius:10px;padding:16px;">
      <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:12px;">
        <div style="display:flex;align-items:center;gap:8px;">
          <div style="background:#702082;width:28px;height:28px;border-radius:6px;display:flex;align-items:center;justify-content:center;font-size:14px;">🤖</div>
          <div>
            <p style="font-weight:500;font-size:13px;color:#702082;" id="ai_result_title">Análisis IA</p>
            <p style="font-size:10px;color:#aeaeb2;" id="ai_result_meta">—</p>
          </div>
        </div>
        <button onclick="document.getElementById('ai_result').style.display='none'" style="background:none;border:none;cursor:pointer;color:#aeaeb2;font-size:18px;padding:2px 6px;">×</button>
      </div>
      <div id="ai_result_body" style="font-size:13px;color:#1d1d1f;line-height:1.7;white-space:pre-wrap;"></div>
      <div id="ai_loading" style="display:none;text-align:center;padding:24px;">
        <div style="width:32px;height:32px;border:3px solid #e5e5ea;border-top-color:#702082;border-radius:50%;animation:spin 0.75s linear infinite;display:inline-block;"></div>
        <p style="font-size:12px;color:#702082;margin-top:10px;" id="ai_loading_text">Analizando datos operacionales...</p>
      </div>
    </div>
  </div>

  <div style="display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:10px;margin-bottom:14px;">
    <div style="background:rgba(192,57,43,0.08);border:0.5px solid rgba(192,57,43,0.2);border-radius:var(--border-radius-md);padding:12px;text-align:center;"><p style="font-size:20px;font-weight:500;color:#C0392B;" id="ca">--</p><p style="font-size:11px;color:#C0392B;">Prioridad alta</p></div>
    <div style="background:rgba(230,126,34,0.08);border:0.5px solid rgba(230,126,34,0.2);border-radius:var(--border-radius-md);padding:12px;text-align:center;"><p style="font-size:20px;font-weight:500;color:#E67E22;" id="cm">--</p><p style="font-size:11px;color:#E67E22;">Prioridad media</p></div>
    <div style="background:rgba(39,174,96,0.08);border:0.5px solid rgba(39,174,96,0.2);border-radius:var(--border-radius-md);padding:12px;text-align:center;"><p style="font-size:20px;font-weight:500;color:#27AE60;" id="cb">--</p><p style="font-size:11px;color:#27AE60;">Prioridad baja</p></div>
  </div>
  <div id="ml"></div>
</div>

</div><!-- /.wrapper -->

<!-- ── MODAL DE CONTRASEÑA ── -->
<div id="pwd_modal" style="display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.55);z-index:2000;align-items:center;justify-content:center;">
  <div style="background:white;border-radius:14px;padding:28px 24px;max-width:320px;width:90%;box-shadow:0 12px 40px rgba(0,0,0,0.25);">
    <div style="text-align:center;margin-bottom:18px;">
      <div style="background:#702082;width:50px;height:50px;border-radius:12px;display:inline-flex;align-items:center;justify-content:center;margin-bottom:12px;">
        <svg width="22" height="22" fill="none" viewBox="0 0 24 24"><rect x="5" y="11" width="14" height="10" rx="2" stroke="white" stroke-width="1.8"/><path d="M8 11V7a4 4 0 0 1 8 0v4" stroke="white" stroke-width="1.8" stroke-linecap="round"/><circle cx="12" cy="16" r="1.5" fill="white"/></svg>
      </div>
      <p style="font-weight:600;font-size:15px;color:#1d1d1f;margin-bottom:4px;">Acceso de editor</p>
      <p style="font-size:12px;color:#6e6e73;">Solo Supply Chain puede agregar o eliminar registros</p>
    </div>
    <input type="password" id="pwd_input" placeholder="Contraseña" onkeydown="if(event.key==='Enter')checkPwd()"
      style="width:100%;padding:10px 12px;border:1.5px solid #e5e5ea;border-radius:8px;font-size:14px;font-family:inherit;box-sizing:border-box;margin-bottom:8px;outline:none;">
    <p id="pwd_error" style="display:none;color:#E74C3C;font-size:11px;margin-bottom:10px;text-align:center;">❌ Contraseña incorrecta — intenta de nuevo</p>
    <div style="display:flex;gap:8px;">
      <button onclick="closePwdModal()" style="flex:1;padding:10px;border:1px solid #e5e5ea;border-radius:8px;background:#f5f5f7;font-size:13px;font-family:inherit;cursor:pointer;color:#6e6e73;">Cancelar</button>
      <button onclick="checkPwd()" style="flex:1;padding:10px;background:#702082;color:white;border:none;border-radius:8px;font-size:13px;font-family:inherit;cursor:pointer;font-weight:500;">Ingresar ›</button>
    </div>
    <p style="text-align:center;font-size:10px;color:#aeaeb2;margin-top:12px;">Gerentes con contraseña pueden editar · Todos pueden ver</p>
  </div>
</div>

<!-- ── OVERLAY DE CARGA ── -->
<div id="loading_overlay" style="display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(255,255,255,0.92);z-index:1999;align-items:center;justify-content:center;flex-direction:column;gap:14px;">
  <div style="width:44px;height:44px;border:3.5px solid #e5e5ea;border-top-color:#702082;border-radius:50%;animation:spin 0.75s linear infinite;"></div>
  <div style="text-align:center;">
    <p style="font-size:14px;color:#702082;font-weight:500;margin-bottom:4px;">Conectando con la base de datos...</p>
    <p style="font-size:11px;color:#aeaeb2;">CD Taco Bell Guatemala · Supply Chain 2026</p>
  </div>
</div>

<script>
let D=[
  {id:1,mes:'Marzo',sem:'S-01',tipo:'Carga',veh:'Camión 10-12t',t:22.13,p:null,prov:'',obs:''},
  {id:2,mes:'Marzo',sem:'S-01',tipo:'Carga',veh:'Camión 5.4t',t:48.2,p:null,prov:'',obs:''},
  {id:3,mes:'Marzo',sem:'S-01',tipo:'Descarga',veh:'Furgón 22 tar.',t:33.1,p:3,prov:'Local',obs:'Dato de furgón 16 tar. en col. 22'},
  {id:4,mes:'Marzo',sem:'S-01',tipo:'Descarga',veh:'Furgón 26 tar.',t:42.4,p:3,prov:'Importado',obs:''},
  {id:5,mes:'Marzo',sem:'S-01',tipo:'Descarga',veh:'Camión pallets',t:22.18,p:3,prov:'',obs:''},
  {id:6,mes:'Marzo',sem:'S-01',tipo:'Descarga',veh:'Camión granel',t:40.5,p:3,prov:'Local',obs:''},
  {id:7,mes:'Marzo',sem:'S-03',tipo:'Carga',veh:'Camión 10-12t',t:36.4,p:null,prov:'',obs:'Interrumpida por falta de personal'},
  {id:8,mes:'Marzo',sem:'S-03',tipo:'Carga',veh:'Camión 5.4t',t:79.52,p:null,prov:'',obs:'Interrumpida — prioridad a otro camión'},
  {id:9,mes:'Marzo',sem:'S-03',tipo:'Carga',veh:'Camión 5.4t',t:62.2,p:null,prov:'',obs:''},
  {id:10,mes:'Marzo',sem:'S-03',tipo:'Carga',veh:'Camión 5.4t',t:49.1,p:null,prov:'',obs:'Sin flujo ininterrumpido'},
  {id:11,mes:'Marzo',sem:'S-03',tipo:'Carga',veh:'Camión 5.4t',t:33.28,p:null,prov:'',obs:'Carga al mediodía para restaurantes'},
  {id:12,mes:'Marzo',sem:'S-03',tipo:'Descarga',veh:'Furgón 22 tar.',t:27,p:3,prov:'Local',obs:''},
  {id:13,mes:'Marzo',sem:'S-03',tipo:'Descarga',veh:'Furgón 26 tar.',t:32,p:3,prov:'Importado',obs:''},
  {id:14,mes:'Marzo',sem:'S-03',tipo:'Descarga',veh:'Furgón 22 tar.',t:18.3,p:4,prov:'Local',obs:'Panifresh 16 tarimas'},
  {id:15,mes:'Marzo',sem:'S-03',tipo:'Descarga',veh:'Furgón 26 tar.',t:33.5,p:4,prov:'Importado',obs:'Seaboard Marine'},
  {id:16,mes:'Marzo',sem:'S-03',tipo:'Descarga',veh:'Camión pallets',t:12.2,p:3,prov:'',obs:'Panifresh 10 pallets'},
  {id:17,mes:'Marzo',sem:'S-04',tipo:'Carga',veh:'Camión 10-12t',t:20.17,p:null,prov:'',obs:'Montacarguista multitarea, espacio reducido'},
  {id:18,mes:'Marzo',sem:'S-04',tipo:'Carga',veh:'Camión 5.4t',t:30.51,p:null,prov:'',obs:'12t, 10t y 5.4t simultáneos'},
  {id:19,mes:'Abril',sem:'S-05',tipo:'Descarga',veh:'Furgón 22 tar.',t:26.1,p:null,prov:'Local',obs:'Ingrup 17 tarimas'},
  {id:20,mes:'Abril',sem:'S-05',tipo:'Descarga',veh:'Furgón 26 tar.',t:23.4,p:null,prov:'Local',obs:'Panifresh 26 tarimas'},
  {id:21,mes:'Abril',sem:'S-06',tipo:'Carga',veh:'Camión 5.4t',t:44.35,p:null,prov:'',obs:'2 camiones 10-12t + 1 camión 5.4t'},
  {id:22,mes:'Abril',sem:'S-06',tipo:'Carga',veh:'Camión 10-12t',t:17.5,p:null,prov:'',obs:''},
  {id:23,mes:'Abril',sem:'S-06',tipo:'Carga',veh:'Camión 10-12t',t:17.32,p:null,prov:'',obs:''},
  {id:24,mes:'Abril',sem:'S-06',tipo:'Descarga',veh:'Furgón 26 tar.',t:27.25,p:1,prov:'Local',obs:'Panifresh 26 tarimas — solo 1 colaborador, operación más lenta'},
  {id:25,mes:'Abril',sem:'S-06',tipo:'Descarga',veh:'Furgón 26 tar.',t:27.1,p:1,prov:'Local',obs:'Panifresh 26 tarimas — solo 1 colaborador'},
  // ── Semana 4 Abril (S-08) ──
  {id:26,mes:'Abril',sem:'S-08',tipo:'Carga',veh:'Camión 5.4t',t:24.58,p:null,prov:'',obs:'Pedido menor — proceso fluido sin inconvenientes'},
  {id:27,mes:'Abril',sem:'S-08',tipo:'Carga',veh:'Camión 5.4t',t:28.07,p:null,prov:'',obs:'Carga 25/04 — pocos pedidos, proceso fluido'},
  {id:28,mes:'Abril',sem:'S-08',tipo:'Carga',veh:'Camión 5.4t',t:42.3,p:null,prov:'',obs:'Carga 25/04 — cantidad alta de pedidos, camión abastecido en su totalidad'},
  // ── Semana 5 Abril (S-09) ──
  {id:29,mes:'Abril',sem:'S-09',tipo:'Carga',veh:'Camión 5.4t',t:36.23,p:null,prov:'',obs:'Carga 29/04 — camión no fue lleno, se dirigió a Recursos Selectivos'},
  {id:30,mes:'Abril',sem:'S-09',tipo:'Carga',veh:'Camión 5.4t',t:69.13,p:null,prov:'',obs:'Carga 29/04 — camión llenado en su totalidad'},
  {id:31,mes:'Abril',sem:'S-09',tipo:'Descarga',veh:'Furgón 26 tar.',t:27.54,p:2,prov:'Local',obs:'Panifresh 26 tar. — 1 auxiliar + montacarguista'},
  {id:32,mes:'Abril',sem:'S-09',tipo:'Descarga',veh:'Furgón 26 tar.',t:48.05,p:2,prov:'Local',obs:'Ingrup 26 tar. — tarimas cruzadas, esfuerzo mayor, operación muy lenta'},
  {id:33,mes:'Abril',sem:'S-09',tipo:'Descarga',veh:'Furgón 26 tar.',t:19.29,p:3,prov:'Local',obs:'Ingrup 26 tar. — 2 auxiliares + montacarguista, descarga muy eficiente y rápida'},
];
let nid=34;

// ── Datos de Trasiego (Toma de Tiempos Trasiego) ──
const DT=[
  {id:1,mes:'Abril',congelado:33.57,refrigerado:7,personas:5,cajas:260,camion:'Camión 5.4 ton',obs:''},
];
let ntid=2;

// ══════════════════════════════════════════════════════════════════
//  🔥 CONFIGURACIÓN FIREBASE — COMPLETAR DESPUÉS DE CREAR PROYECTO
//  Paso 1: ir a console.firebase.google.com
//  Paso 2: crear proyecto → Realtime Database → Copiar config
//  Paso 3: pegar los valores abajo (reemplazar cada "PEGAR_AQUI")
// ══════════════════════════════════════════════════════════════════
// ── Configuración Firebase (ya completada) ──
const FIREBASE_CONFIG = {
  apiKey:            "AIzaSyB-Oki3UayjWWNdwPeNTbII7E08w7mvLlI",
  authDomain:        "tablero-cd-tacobell.firebaseapp.com",
  databaseURL:       "https://tablero-cd-tacobell-default-rtdb.firebaseio.com",
  projectId:         "tablero-cd-tacobell",
  storageBucket:     "tablero-cd-tacobell.firebasestorage.app",
  messagingSenderId: "1068934666505",
  appId:             "1:1068934666505:web:3d7525bd946cbb9a6c5bd8"
};

// 🔐 CONTRASEÑA DE EDITOR — cambiar por la que prefieras
const EDIT_PASSWORD = "TacoBellCD2026";

let editAuth = false;
let pendingAction = null;
let dbRef = null;
let dbTrasiegoRef = null;
const vc=['Camión 10-12t','Camión 5.4t'];
const vd=['Furgón 22 tar.','Furgón 26 tar.','Camión granel','Camión pallets'];
const ALL_VEHS=['Camión pallets','Furgón 22 tar.','Camión 10-12t','Furgón 26 tar.','Camión granel','Camión 5.4t'];
const TARGETS={'Camión 10-12t':22,'Camión 5.4t':40,'Furgón 22 tar.':25,'Furgón 26 tar.':30,'Camión granel':38,'Camión pallets':18};
const VEH_COLORS={'Camión 10-12t':'#702082','Camión 5.4t':'#EB008B','Furgón 22 tar.':'#1D9E75','Furgón 26 tar.':'#378ADD','Camión granel':'#E67E22','Camión pallets':'#27AE60'};
const CI={};

function av(a){return a.length?a.reduce((s,v)=>s+v,0)/a.length:0;}
function mn(a){return a.length?Math.min(...a):0;}
function mx(a){return a.length?Math.max(...a):0;}
function std(a){if(a.length<2)return 0;const m=av(a);return Math.sqrt(a.reduce((s,v)=>s+(v-m)**2,0)/a.length);}
function pct(a){return a.length?+(a.length/a.length*100).toFixed(0):0;}

function getVehData(veh){return D.filter(r=>r.veh===veh).map(r=>r.t);}
function smf(t,v){const b=TARGETS[v];if(!b)return 'dy';return t<=b?'dg':t<=b*1.3?'dy':'dr';}

function gt(id,el){
  document.querySelectorAll('.pnl').forEach(p=>p.classList.remove('on'));
  document.querySelectorAll('.tb').forEach(b=>b.classList.remove('on'));
  document.getElementById('p'+id).classList.add('on');
  el.classList.add('on');
  if(id==='trec')renderTrasiego();
}
function tf(){
  if(!editAuth){showPwdModal(()=>tf());return;}
  const f=document.getElementById('af');f.style.display=f.style.display==='none'?'block':'none';
}
function uv(){
  const t=document.getElementById('nt').value,s=document.getElementById('nv');
  s.innerHTML='';
  (t==='Carga'?vc:vd).forEach(v=>{const o=document.createElement('option');o.textContent=v;s.appendChild(o);});
}
function ar(){
  const ti=parseFloat(document.getElementById('nti').value);
  if(!ti||isNaN(ti)){alert('Ingresa un tiempo válido.');return;}
  const record={
    id:nid++,
    mes:document.getElementById('nm').value,
    sem:document.getElementById('ns').value||'S-??',
    tipo:document.getElementById('nt').value,
    veh:document.getElementById('nv').value,
    t:ti,
    p:parseInt(document.getElementById('np').value)||null,
    prov:document.getElementById('npr').value,
    obs:document.getElementById('no').value,
    ts:Date.now()
  };
  if(dbRef){
    dbRef.push(record).catch(e=>{ D.push(record); ra(); uts(); });
  } else {
    D.push(record); ra(); uts();
  }
  ['nti','ns','np','no'].forEach(i=>document.getElementById(i).value='');
  document.getElementById('af').style.display='none';
}
function dr(id){
  if(!editAuth){showPwdModal(()=>dr(id));return;}
  if(!confirm('¿Eliminar este registro?'))return;
  const rec=D.find(r=>r.id===id);
  if(dbRef&&rec&&rec._fbKey){
    dbRef.child(rec._fbKey).remove();
  } else {
    const i=D.findIndex(r=>r.id===id);
    if(i>=0)D.splice(i,1);
    ra();
  }
}
function xc(id){if(CI[id]){try{CI[id].destroy();}catch(e){}delete CI[id];}}

function rt(){
  const fm=document.getElementById('fm').value,ft=document.getElementById('ft').value,fv=document.getElementById('fv').value;
  let f=D;
  if(fm)f=f.filter(r=>r.mes===fm);
  if(ft)f=f.filter(r=>r.tipo===ft);
  if(fv)f=f.filter(r=>r.veh===fv);
  document.getElementById('tb').innerHTML=f.map(r=>`
    <tr>
      <td style="color:var(--color-text-tertiary);font-size:10px;">${r.id}</td>
      <td style="font-size:11px;">${r.mes}</td>
      <td><span class="pill">${r.sem}</span></td>
      <td><span class="badge" style="background:${r.tipo==='Carga'?'rgba(112,32,130,0.1)':'rgba(235,0,139,0.1)'};color:${r.tipo==='Carga'?'#702082':'#EB008B'};font-size:11px;">${r.tipo}</span></td>
      <td style="font-weight:500;font-size:12px;">${r.veh}</td>
      <td style="font-weight:500;color:#702082;">${r.t.toFixed(2)} min</td>
      <td><span class="dot ${smf(r.t,r.veh)}"></span></td>
      <td style="font-size:11px;">${r.prov||'—'}</td>
      <td style="font-size:11px;">${r.p||'—'}</td>
      <td style="font-size:11px;max-width:150px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;" title="${r.obs}">${r.obs||'—'}</td>
      <td><button onclick="dr(${r.id})" style="background:none;border:none;cursor:pointer;color:#E74C3C;font-size:14px;padding:2px 6px;line-height:1;">×</button></td>
    </tr>
  `).join('');
  const txt=`${f.length} de ${D.length} registros`;
  document.getElementById('rc').textContent=txt;
  document.getElementById('rc2').textContent=txt;
}

function rk(){
  const ct=D.filter(r=>r.tipo==='Carga').map(r=>r.t);
  const dt=D.filter(r=>r.tipo==='Descarga').map(r=>r.t);
  const best=mn(D.map(r=>r.t));
  const worst=mx(D.map(r=>r.t));
  const critOps=D.filter(r=>r.t>TARGETS[r.veh]*1.3);
  const onTarget=D.filter(r=>r.t<=TARGETS[r.veh]);
  const pctOn=Math.round(onTarget.length/D.length*100);

  document.getElementById('kpis').innerHTML=`
    <div class="kc">
      <div class="kt"><p class="kl">Promedio carga</p><div class="kb" style="background:rgba(112,32,130,0.12);"><svg width="14" height="14" viewBox="0 0 14 14"><rect x="2" y="6" width="3" height="7" rx="1" fill="#702082"/><rect x="6" y="3" width="3" height="10" rx="1" fill="#702082"/><rect x="10" y="1" width="3" height="12" rx="1" fill="#702082"/></svg></div></div>
      <p class="kv">${av(ct).toFixed(1)}</p>
      <p class="ks">min / operación de carga</p>
      <div class="kbar"><div class="kfill" style="width:${Math.min(100,av(ct)/80*100).toFixed(0)}%;background:#702082;"></div></div>
    </div>
    <div class="kc">
      <div class="kt"><p class="kl">Promedio descarga</p><div class="kb" style="background:rgba(235,0,139,0.1);"><svg width="14" height="14" viewBox="0 0 14 14"><rect x="2" y="2" width="3" height="12" rx="1" fill="#EB008B"/><rect x="6" y="5" width="3" height="9" rx="1" fill="#EB008B"/><rect x="10" y="8" width="3" height="6" rx="1" fill="#EB008B"/></svg></div></div>
      <p class="kv">${av(dt).toFixed(1)}</p>
      <p class="ks">min / operación de descarga</p>
      <div class="kbar"><div class="kfill" style="width:${Math.min(100,av(dt)/80*100).toFixed(0)}%;background:#EB008B;"></div></div>
    </div>
    <div class="kc">
      <div class="kt"><p class="kl">Cumplimiento de meta</p><div class="kb" style="background:${pctOn>=80?'rgba(39,174,96,0.12)':'rgba(231,76,60,0.1)'};"><svg width="14" height="14" viewBox="0 0 14 14" fill="none"><circle cx="7" cy="7" r="5.5" stroke="${pctOn>=80?'#27AE60':'#E74C3C'}" stroke-width="1.5"/><path d="${pctOn>=80?'M4.5 7l2 2 3-3':'M5 5l4 4M9 5l-4 4'}" stroke="${pctOn>=80?'#27AE60':'#E74C3C'}" stroke-width="1.5" stroke-linecap="round"/></svg></div></div>
      <p class="kv" style="color:${pctOn>=80?'#27AE60':'#E74C3C'};">${pctOn}%</p>
      <p class="ks">de operaciones en objetivo</p>
      <div class="kbar"><div class="kfill" style="width:${pctOn}%;background:${pctOn>=80?'#27AE60':'#E74C3C'};"></div></div>
    </div>
    <div class="kc">
      <div class="kt"><p class="kl">Mejor tiempo registrado</p><div class="kb" style="background:rgba(255,199,44,0.15);"><svg width="14" height="14" viewBox="0 0 14 14"><polygon points="7,2 8.5,5.5 12,6 9.5,8.5 10,12 7,10.5 4,12 4.5,8.5 2,6 5.5,5.5" fill="#FFC72C"/></svg></div></div>
      <p class="kv">${best.toFixed(1)}</p>
      <p class="ks">min (récord actual del CD)</p>
      <div class="kbar"><div class="kfill" style="width:${Math.min(100,best/40*100).toFixed(0)}%;background:#FFC72C;"></div></div>
    </div>
    <div class="kc">
      <div class="kt"><p class="kl">Mayor tiempo registrado</p><div class="kb" style="background:rgba(231,76,60,0.1);"><svg width="14" height="14" viewBox="0 0 14 14" fill="none"><path d="M7 3v4M7 9.5v1" stroke="#E74C3C" stroke-width="1.8" stroke-linecap="round"/><circle cx="7" cy="7" r="5.5" stroke="#E74C3C" stroke-width="1.5"/></svg></div></div>
      <p class="kv" style="color:#E74C3C;">${worst.toFixed(1)}</p>
      <p class="ks">min — operación más lenta</p>
      <div class="kbar"><div class="kfill" style="width:100%;background:#E74C3C;"></div></div>
    </div>
    <div class="kc">
      <div class="kt"><p class="kl">Operaciones críticas</p><div class="kb" style="background:rgba(231,76,60,0.1);"><svg width="14" height="14" viewBox="0 0 14 14" fill="none"><path d="M7 2L12 12H2L7 2Z" stroke="#E74C3C" stroke-width="1.5" stroke-linejoin="round"/><path d="M7 6v2.5M7 10v.5" stroke="#E74C3C" stroke-width="1.5" stroke-linecap="round"/></svg></div></div>
      <p class="kv" style="color:${critOps.length>3?'#E74C3C':'#F39C12'};">${critOps.length}</p>
      <p class="ks">registros que exceden meta ×1.3</p>
      <div class="kbar"><div class="kfill" style="width:${Math.min(100,critOps.length/D.length*100).toFixed(0)}%;background:#E74C3C;"></div></div>
    </div>
  `;
}

function renderDashCharts(){
  xc('rank');xc('trend');xc('donut');

  const vehs=[...ALL_VEHS];
  const avgs=vehs.map(v=>+av(getVehData(v)).toFixed(1));
  const pairs=vehs.map((v,i)=>({v,a:avgs[i],tgt:TARGETS[v]})).sort((a,b)=>a.a-b.a);
  const sortedLabels=pairs.map(p=>p.v);
  const sortedAvgs=pairs.map(p=>p.a);
  const barColors=pairs.map(p=>p.a<=p.tgt?'rgba(39,174,96,0.82)':p.a<=p.tgt*1.3?'rgba(243,156,18,0.82)':'rgba(231,76,60,0.82)');
  const borderColors=pairs.map(p=>p.a<=p.tgt?'#27AE60':p.a<=p.tgt*1.3?'#F39C12':'#E74C3C');

  // Plugin para etiquetas de valor + ícono de estado al final de cada barra
  const rankLabelPlugin={
    id:'rankLabel',
    afterDatasetsDraw(chart){
      const {ctx,scales:{x,y}}=chart;
      const ds0=chart.getDatasetMeta(0);
      pairs.forEach((p,i)=>{
        const bar=ds0.data[i];
        if(!bar)return;
        const icon=p.a<=p.tgt?'✔':p.a<=p.tgt*1.3?'⚠':'✗';
        const iconColor=p.a<=p.tgt?'#27AE60':p.a<=p.tgt*1.3?'#E67E22':'#E74C3C';
        const valStr=`${p.a} min`;
        const gapStr=p.a<=p.tgt?` (−${(p.tgt-p.a).toFixed(1)} vs meta)`
                                 :` (+${(p.a-p.tgt).toFixed(1)} sobre meta)`;
        ctx.save();
        // Valor numérico
        ctx.font='bold 11px -apple-system,BlinkMacSystemFont,sans-serif';
        ctx.fillStyle='#1d1d1f';
        ctx.textAlign='left';
        ctx.textBaseline='middle';
        ctx.fillText(valStr, bar.x+6, bar.y);
        // Brecha
        ctx.font='10px -apple-system,BlinkMacSystemFont,sans-serif';
        ctx.fillStyle=iconColor;
        const valW=ctx.measureText(valStr).width;
        ctx.fillText(gapStr, bar.x+6+valW+2, bar.y);
        // Ícono al inicio de la barra
        ctx.font='bold 11px -apple-system,sans-serif';
        ctx.fillStyle=iconColor;
        ctx.textAlign='right';
        ctx.fillText(icon, bar.x-4, bar.y);
        ctx.restore();
      });
    }
  };

  CI['rank']=new Chart(document.getElementById('c_rank'),{
    type:'bar',
    data:{
      labels:sortedLabels,
      datasets:[
        {label:'Tiempo promedio',data:sortedAvgs,backgroundColor:barColors,borderColor:borderColors,borderWidth:1.5,borderRadius:5},
        {label:'Meta objetivo',data:pairs.map(p=>p.tgt),type:'line',borderColor:'#FFC72C',borderWidth:2.5,borderDash:[7,4],pointRadius:5,pointBackgroundColor:'#FFC72C',pointBorderColor:'white',pointBorderWidth:1.5,fill:false,tension:0}
      ]
    },
    plugins:[rankLabelPlugin],
    options:{
      indexAxis:'y',responsive:true,maintainAspectRatio:false,
      layout:{padding:{right:160}},
      plugins:{
        legend:{display:false},
        tooltip:{callbacks:{
          label:ctx=>{
            if(ctx.datasetIndex===0){const p=pairs[ctx.dataIndex];return ` Actual: ${ctx.raw} min | Meta: ${p.tgt} min | ${ctx.raw<=p.tgt?'✔ En meta':'⚠ +'+((ctx.raw-p.tgt)).toFixed(1)+' min sobre meta'}`;}
            return ` Meta: ${ctx.raw} min`;
          }
        }}
      },
      scales:{
        x:{beginAtZero:true,max:Math.ceil(Math.max(...sortedAvgs)*1.15/5)*5,title:{display:true,text:'Minutos',font:{size:11}},ticks:{font:{size:11}}},
        y:{ticks:{font:{size:11,weight:'500'}}}
      }
    }
  });

  // ── Notas automáticas basadas en data real ──
  const notaEl=document.getElementById('meta_nota');
  const enMeta=pairs.filter(p=>p.a<=p.tgt);
  const atencion=pairs.filter(p=>p.a>p.tgt&&p.a<=p.tgt*1.3);
  const critico=pairs.filter(p=>p.a>p.tgt*1.3);
  const mejorOp=pairs[0];
  const peorOp=pairs[pairs.length-1];
  const allT=D.map(r=>r.t);
  const promGeneral=+av(allT).toFixed(1);

  let notas=[];
  // Resumen general
  notas.push(`📊 <strong>Resumen:</strong> ${enMeta.length} de ${pairs.length} operaciones están dentro de meta. ${critico.length>0?`<span style="color:#E74C3C;font-weight:600;">${critico.length} operación${critico.length>1?'es':''} crítica${critico.length>1?'s':''}.</span>`:'Sin operaciones críticas.'}`);
  // Mejor operación
  notas.push(`✅ <strong>Mejor operación:</strong> ${mejorOp.v} — ${mejorOp.a} min promedio, ${mejorOp.a<=mejorOp.tgt?`<span style="color:#27AE60;">${(mejorOp.tgt-mejorOp.a).toFixed(1)} min por debajo de su meta (${mejorOp.tgt} min)</span>`:`en atención`}. Operación bien controlada.`);
  // Operaciones críticas
  critico.forEach(p=>{
    const pct=+((p.a-p.tgt)/p.tgt*100).toFixed(0);
    const cnt=getVehData(p.v).length;
    notas.push(`🔴 <strong>${p.v}:</strong> ${p.a} min promedio vs meta de ${p.tgt} min (<span style="color:#E74C3C;font-weight:600;">+${pct}% sobre meta</span>). ${cnt} registros — revisar SOP y dotación. Acción inmediata recomendada.`);
  });
  // Operaciones en atención
  atencion.forEach(p=>{
    const pct=+((p.a-p.tgt)/p.tgt*100).toFixed(0);
    notas.push(`⚠ <strong>${p.v}:</strong> ${p.a} min vs meta ${p.tgt} min (+${pct}%). Con ajustes menores (dotación o secuencia) puede estar en meta esta semana.`);
  });
  // Promedio general
  notas.push(`📈 <strong>Promedio general del CD:</strong> ${promGeneral} min por operación. Total de registros analizados: <strong>${D.length}</strong>.`);
  notaEl.innerHTML=notas.map(n=>`<div style="padding:3px 0;border-bottom:0.5px solid rgba(112,32,130,0.08);">${n}</div>`).join('');

  const sems=[...new Set(D.map(r=>r.sem))].sort();
  const t1=sems.map(s=>{const t=D.filter(r=>r.tipo==='Carga'&&r.veh==='Camión 10-12t'&&r.sem===s).map(r=>r.t);return t.length?+av(t).toFixed(1):null;});
  const t2=sems.map(s=>{const t=D.filter(r=>r.tipo==='Carga'&&r.veh==='Camión 5.4t'&&r.sem===s).map(r=>r.t);return t.length?+av(t).toFixed(1):null;});
  CI['trend']=new Chart(document.getElementById('c_trend'),{
    type:'line',
    data:{labels:sems,datasets:[
      {label:'10-12t',data:t1,borderColor:'#702082',backgroundColor:'rgba(112,32,130,0.08)',tension:0.35,pointRadius:5,pointBackgroundColor:'#702082',fill:true,spanGaps:false},
      {label:'5.4t',data:t2,borderColor:'#EB008B',backgroundColor:'rgba(235,0,139,0.05)',tension:0.35,pointRadius:5,pointBackgroundColor:'#EB008B',borderDash:[6,3],fill:true,spanGaps:false}
    ]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{y:{ticks:{font:{size:11}}},x:{ticks:{font:{size:11},autoSkip:false,maxRotation:30}}}}
  });

  const nCarga=D.filter(r=>r.tipo==='Carga').length;
  const nDesc=D.filter(r=>r.tipo==='Descarga').length;
  document.getElementById('leg_donut').innerHTML=`
    <span><span class="lsq" style="background:#702082;"></span>Carga (${nCarga})</span>
    <span><span class="lsq" style="background:#EB008B;"></span>Descarga (${nDesc})</span>
  `;
  CI['donut']=new Chart(document.getElementById('c_donut'),{
    type:'doughnut',
    data:{labels:['Carga','Descarga'],datasets:[{data:[nCarga,nDesc],backgroundColor:['#702082','#EB008B'],borderWidth:0,hoverOffset:4}]},
    options:{responsive:true,maintainAspectRatio:false,cutout:'62%',plugins:{legend:{display:false},tooltip:{callbacks:{label:ctx=>` ${ctx.raw} ops (${Math.round(ctx.raw/(nCarga+nDesc)*100)}%)`}}}}
  });
}

function renderCompCharts(){
  xc('var');xc('var_carga');xc('var_desc');xc('scat');xc('lvi');xc('cump');xc('comp_gen');

  // ── Insight ──
  const r54=mx(getVehData('Camión 5.4t'))-mn(getVehData('Camión 5.4t'));
  const avgCarga=+av([...vc].flatMap(v=>getVehData(v))).toFixed(1);
  const avgDesc=+av([...vd].flatMap(v=>getVehData(v))).toFixed(1);
  document.getElementById('insight_var').innerHTML=`<strong>Hallazgo clave:</strong> Promedio de <strong>CARGA = ${avgCarga} min</strong> vs <strong>DESCARGA = ${avgDesc} min</strong>. Camión 5.4t presenta la mayor variabilidad del CD con un rango de <strong>${r54.toFixed(1)} min</strong> (${(r54/av(getVehData('Camión 5.4t'))*100).toFixed(0)}% del promedio). Camión pallets es la operación más consistente y eficiente del CD.`;

  // ── Gráfica comparativa general Carga vs Descarga ──
  const cargaVehs=[...vc];
  const descargaVehs=[...vd];
  const allOrdered=[...cargaVehs,...descargaVehs];
  const compAvgs=allOrdered.map(v=>+av(getVehData(v)).toFixed(1));
  const compTgts=allOrdered.map(v=>TARGETS[v]);
  const compColors=allOrdered.map((v,i)=>i<cargaVehs.length?'rgba(112,32,130,0.78)':'rgba(235,0,139,0.78)');
  const compBorders=allOrdered.map((v,i)=>i<cargaVehs.length?'#702082':'#EB008B');

  const dividerPlugin={
    id:'divider',
    afterDraw(chart){
      const {ctx,chartArea,scales}=chart;
      if(!scales.x)return;
      const x0=scales.x.getPixelForValue(cargaVehs.length-1);
      const x1=scales.x.getPixelForValue(cargaVehs.length);
      const xMid=(x0+x1)/2;
      ctx.save();
      ctx.beginPath();
      ctx.moveTo(xMid,chartArea.top);
      ctx.lineTo(xMid,chartArea.bottom);
      ctx.strokeStyle='rgba(0,0,0,0.18)';
      ctx.setLineDash([5,4]);
      ctx.lineWidth=1.5;
      ctx.stroke();
      ctx.setLineDash([]);
      ctx.font='bold 10px -apple-system,BlinkMacSystemFont,sans-serif';
      ctx.textAlign='center';
      ctx.fillStyle='rgba(112,32,130,0.7)';
      ctx.fillText('◀  CARGA',(chartArea.left+xMid)/2,chartArea.top+12);
      ctx.fillStyle='rgba(235,0,139,0.7)';
      ctx.fillText('DESCARGA  ▶',(xMid+chartArea.right)/2,chartArea.top+12);
      ctx.restore();
    }
  };

  CI['comp_gen']=new Chart(document.getElementById('c_comp_gen'),{
    type:'bar',
    data:{
      labels:allOrdered,
      datasets:[
        {label:'Tiempo promedio',data:compAvgs,backgroundColor:compColors,borderColor:compBorders,borderWidth:1.5,borderRadius:6},
        {label:'Meta objetivo',data:compTgts,type:'line',borderColor:'#FFC72C',borderWidth:2.5,borderDash:[6,3],pointRadius:5,pointBackgroundColor:'#FFC72C',fill:false,tension:0}
      ]
    },
    plugins:[dividerPlugin],
    options:{
      responsive:true,maintainAspectRatio:false,
      plugins:{legend:{display:false},tooltip:{callbacks:{label:ctx=>` ${ctx.raw} min`}}},
      scales:{y:{beginAtZero:true,title:{display:true,text:'Minutos',font:{size:11}},ticks:{font:{size:11}}},x:{ticks:{font:{size:11}}}}
    }
  });

  // ── Variabilidad CARGA (vc) ──
  const vcMn=cargaVehs.map(v=>+mn(getVehData(v)).toFixed(1));
  const vcAv=cargaVehs.map(v=>+av(getVehData(v)).toFixed(1));
  const vcMx=cargaVehs.map(v=>+mx(getVehData(v)).toFixed(1));
  const vcTg=cargaVehs.map(v=>TARGETS[v]);
  CI['var_carga']=new Chart(document.getElementById('c_var_carga'),{
    type:'bar',
    data:{
      labels:cargaVehs,
      datasets:[
        {label:'Mínimo',data:vcMn,backgroundColor:'rgba(112,32,130,0.15)',borderColor:'rgba(112,32,130,0.35)',borderWidth:1,borderRadius:4,stack:'s'},
        {label:'Promedio',data:vcAv,backgroundColor:'rgba(112,32,130,0.72)',borderColor:'#702082',borderWidth:1,borderRadius:4,stack:'p'},
        {label:'Máximo',data:vcMx,backgroundColor:'rgba(112,32,130,0.32)',borderColor:'rgba(112,32,130,0.6)',borderWidth:1,borderRadius:4,stack:'x'},
        {label:'Meta objetivo',data:vcTg,type:'line',borderColor:'#FFC72C',borderWidth:2,borderDash:[5,3],pointRadius:5,pointBackgroundColor:'#FFC72C',fill:false,tension:0}
      ]
    },
    options:{
      responsive:true,maintainAspectRatio:false,
      plugins:{legend:{display:false},tooltip:{callbacks:{label:ctx=>` ${ctx.raw} min`}}},
      scales:{y:{beginAtZero:true,title:{display:true,text:'Minutos',font:{size:11}},ticks:{font:{size:11}}},x:{ticks:{font:{size:11}}}}
    }
  });

  // ── Variabilidad DESCARGA (vd) ──
  const vdMn=descargaVehs.map(v=>+mn(getVehData(v)).toFixed(1));
  const vdAv=descargaVehs.map(v=>+av(getVehData(v)).toFixed(1));
  const vdMx=descargaVehs.map(v=>+mx(getVehData(v)).toFixed(1));
  const vdTg=descargaVehs.map(v=>TARGETS[v]);
  CI['var_desc']=new Chart(document.getElementById('c_var_desc'),{
    type:'bar',
    data:{
      labels:descargaVehs,
      datasets:[
        {label:'Mínimo',data:vdMn,backgroundColor:'rgba(235,0,139,0.12)',borderColor:'rgba(235,0,139,0.35)',borderWidth:1,borderRadius:4,stack:'s'},
        {label:'Promedio',data:vdAv,backgroundColor:'rgba(235,0,139,0.72)',borderColor:'#EB008B',borderWidth:1,borderRadius:4,stack:'p'},
        {label:'Máximo',data:vdMx,backgroundColor:'rgba(235,0,139,0.3)',borderColor:'rgba(235,0,139,0.6)',borderWidth:1,borderRadius:4,stack:'x'},
        {label:'Meta objetivo',data:vdTg,type:'line',borderColor:'#FFC72C',borderWidth:2,borderDash:[5,3],pointRadius:5,pointBackgroundColor:'#FFC72C',fill:false,tension:0}
      ]
    },
    options:{
      responsive:true,maintainAspectRatio:false,
      plugins:{legend:{display:false},tooltip:{callbacks:{label:ctx=>` ${ctx.raw} min`}}},
      scales:{y:{beginAtZero:true,title:{display:true,text:'Minutos',font:{size:11}},ticks:{font:{size:11}}},x:{ticks:{font:{size:10},maxRotation:15}}}
    }
  });

  const semToX={'S-01':1,'S-03':3,'S-04':4,'S-05':5,'S-06':6,'S-08':8,'S-09':9};
  const scatVehs=[...new Set(D.map(r=>r.veh))];
  const scatShapes=['circle','rect','triangle','rectRot','cross','star'];
  document.getElementById('leg_scatter').innerHTML=scatVehs.map((v,i)=>`<span><span class="ldot" style="background:${VEH_COLORS[v]||'#888'};"></span>${v}</span>`).join('');
  CI['scat']=new Chart(document.getElementById('c_scat'),{
    type:'scatter',
    data:{datasets:scatVehs.map((v,i)=>({
      label:v,
      data:D.filter(r=>r.veh===v).map(r=>({x:(semToX[r.sem]||1)+(Math.random()-0.5)*0.2,y:r.t})),
      backgroundColor:VEH_COLORS[v]||'#888',
      borderColor:(VEH_COLORS[v]||'#888'),
      pointRadius:6,pointHoverRadius:8,
      pointStyle:scatShapes[i%scatShapes.length],
    }))},
    options:{
      responsive:true,maintainAspectRatio:false,
      plugins:{legend:{display:false},tooltip:{callbacks:{label:ctx=>`${ctx.dataset.label}: ${ctx.parsed.y.toFixed(1)} min`}}},
      scales:{
        x:{type:'linear',min:0,max:10,title:{display:true,text:'Semana',font:{size:11}},ticks:{font:{size:11},callback:v=>{const m={1:'S-01',3:'S-03',4:'S-04',5:'S-05',6:'S-06',8:'S-08',9:'S-09'};return m[v]||'';},stepSize:1}},
        y:{title:{display:true,text:'Minutos',font:{size:11}},ticks:{font:{size:11}}}
      }
    }
  });

  const descVehs=['Furgón 22 tar.','Furgón 26 tar.'];
  const localAvg=descVehs.map(v=>+av(D.filter(r=>r.veh===v&&r.prov==='Local').map(r=>r.t)).toFixed(1));
  const impAvg=descVehs.map(v=>+av(D.filter(r=>r.veh===v&&r.prov==='Importado').map(r=>r.t)).toFixed(1));
  CI['lvi']=new Chart(document.getElementById('c_lvi'),{
    type:'bar',
    data:{
      labels:['Furgón 22 tar.','Furgón 26 tar.'],
      datasets:[
        {label:'Local',data:localAvg,backgroundColor:'rgba(112,32,130,0.75)',borderRadius:5,barPercentage:0.6},
        {label:'Importado',data:impAvg,backgroundColor:'rgba(235,0,139,0.75)',borderRadius:5,barPercentage:0.6},
      ]
    },
    options:{
      responsive:true,maintainAspectRatio:false,
      plugins:{legend:{display:false},tooltip:{callbacks:{label:ctx=>` ${ctx.raw} min`}}},
      scales:{y:{beginAtZero:true,title:{display:true,text:'Minutos',font:{size:11}},ticks:{font:{size:11}}},x:{ticks:{font:{size:11}}}}
    }
  });

  const cumpVehs=[...ALL_VEHS];
  const cumpPcts=cumpVehs.map(v=>{
    const all=getVehData(v);
    const onT=all.filter(t=>t<=TARGETS[v]);
    return all.length?Math.round(onT.length/all.length*100):0;
  });
  const cumpColors=cumpPcts.map(p=>p>=80?'rgba(39,174,96,0.8)':p>=50?'rgba(243,156,18,0.8)':'rgba(231,76,60,0.8)');

  CI['cump']=new Chart(document.getElementById('c_cump'),{
    type:'bar',
    data:{
      labels:cumpVehs,
      datasets:[
        {label:'% en meta',data:cumpPcts,backgroundColor:cumpColors,borderRadius:5,borderWidth:0},
        {label:'Objetivo (80%)',data:cumpVehs.map(()=>80),type:'line',borderColor:'#FFC72C',borderWidth:2,borderDash:[6,3],pointRadius:0,fill:false,tension:0}
      ]
    },
    options:{
      indexAxis:'y',responsive:true,maintainAspectRatio:false,
      plugins:{legend:{display:false},tooltip:{callbacks:{label:ctx=>` ${ctx.raw}%`}}},
      scales:{
        x:{min:0,max:100,title:{display:true,text:'% cumplimiento',font:{size:11}},ticks:{font:{size:11},callback:v=>v+'%'}},
        y:{ticks:{font:{size:11}}}
      }
    }
  });
}

// ── Helpers para mejoras dinámicas ──
function stdDev(a){if(a.length<2)return 0;const m=av(a);return +Math.sqrt(a.reduce((s,v)=>s+(v-m)**2,0)/a.length).toFixed(2);}
function leanTag(v,issue){
  const map={
    'exceed':{'Camión 5.4t':'Standard Work','Camión 10-12t':'Flujo continuo','Furgón 26 tar.':'SOP + Dotación','Furgón 22 tar.':'Standard Work','Camión granel':'Gestión visual','Camión pallets':'Mantenimiento'},
    'variability':{'Camión 5.4t':'SMED + Heijunka','Camión 10-12t':'Standard Work','Furgón 26 tar.':'Poka-yoke','Furgón 22 tar.':'Flujo continuo'},
    'staffing':'Flujo continuo','incidents':'5 Porqués','local_imp':'SOP diferenciado','trend_up':'Control estadístico','trend_ok':'Kaizen','data':'Medición lean'
  };
  if(typeof map[issue]==='object') return map[issue][v]||'Mejora continua';
  return map[issue]||'Mejora continua';
}

function renderMejoras(){
  const items=[];
  const PC={'alta':'#C0392B','media':'#E67E22','baja':'#27AE60'};
  const PB={'alta':'#FDECEA','media':'#FFF3E0','baja':'#E8F5E9'};
  const TC={'Carga':'#702082','Descarga':'#EB008B','Ambas':'#1A6BAE','General':'#555'};
  const TB={'Carga':'rgba(112,32,130,0.1)','Descarga':'rgba(235,0,139,0.1)','Ambas':'rgba(26,107,174,0.1)','General':'rgba(85,85,85,0.1)'};

  // ══ BLOQUE 1: Desviación vs meta por operación ══
  ALL_VEHS.forEach(v=>{
    const data=getVehData(v); if(!data.length)return;
    const avgT=+av(data).toFixed(2), minT=+mn(data).toFixed(2), maxT=+mx(data).toFixed(2);
    const tgt=TARGETS[v], devPct=+((avgT-tgt)/tgt*100).toFixed(1);
    const excCnt=data.filter(t=>t>tgt).length, excPct=Math.round(excCnt/data.length*100);
    const tipoOp=D.find(r=>r.veh===v)?.tipo||'General';
    const sdv=stdDev(data), cv=data.length>1?+((sdv/avgT)*100).toFixed(0):0;

    if(devPct>20){
      items.push({p:'alta',v,tipoOp,tag:'Lean: '+leanTag(v,'exceed'),
        title:`${v} — Promedio supera la meta en +${devPct}%`,
        pb:`Promedio actual: <strong>${avgT} min</strong> vs meta: <strong>${tgt} min</strong> (+${devPct}%). <strong>${excCnt}/${data.length} registros (${excPct}%)</strong> superan el objetivo. Rango: <strong>${minT}–${maxT} min</strong>. CV: ${cv}%.`,
        ac:devPct>50
          ?`Proceso fuera de control. Realizar sesión de 5 Porqués con el equipo operacional para identificar causas raíz. Definir SOP escrito para ${v} con tiempo estándar de ${tgt} min. Asignar responsable de cronometrar cada operación.`
          :`Revisar los ${excCnt} registros que superan la meta e identificar factores comunes (hora, personal, proveedor). Estandarizar las condiciones de los registros dentro de meta (${data.length-excCnt} registros a ${tgt} min o menos).`,
        imp:`Reducir promedio de ${avgT} → ${tgt} min = ahorro de <strong>${(avgT-tgt).toFixed(0)} min/operación</strong> · ${(((avgT-tgt)/avgT)*100).toFixed(0)}% de mejora`
      });
    } else if(devPct>5&&devPct<=20){
      items.push({p:'media',v,tipoOp,tag:'Lean: '+leanTag(v,'exceed'),
        title:`${v} — Promedio en zona de atención (+${devPct}% sobre meta)`,
        pb:`Promedio actual: <strong>${avgT} min</strong> vs meta: <strong>${tgt} min</strong>. <strong>${excPct}%</strong> de registros fuera de meta (${excCnt}/${data.length}). Con ajustes menores es alcanzable esta semana.`,
        ac:`Revisar los registros fuera de meta en detalle (ver pestaña Registro). Estandarizar condiciones de los ${data.length-excCnt} registros que ya cumplen. Focalizar en el tiempo máximo registrado (${maxT} min) — ¿qué pasó en esa operación?`,
        imp:`Bajar promedio a ${tgt} min o menos estabilizaría el ${100-excPct}% de registros actuales dentro de meta → 100% de cumplimiento`
      });
    }

    // ── Sub-bloque: Alta variabilidad ──
    if(cv>60&&data.length>=3){
      items.push({p:devPct>0?'alta':'media',v,tipoOp,tag:'Lean: '+leanTag(v,'variability'),
        title:`${v} — Proceso con alta variabilidad (CV: ${cv}%)`,
        pb:`Tiempo mínimo: <strong>${minT} min</strong> · máximo: <strong>${maxT} min</strong> · desviación estándar: <strong>±${sdv} min</strong>. Un CV >${cv>80?'80':'60'}% indica proceso no estandarizado: el tiempo depende más de las condiciones del momento que de un método definido.`,
        ac:`Aplicar SMED: identificar elementos internos vs externos de la operación. Documentar qué condiciones producen el tiempo mínimo (${minT} min) y convertirlas en estándar obligatorio. Crear OPL (One Point Lesson) para el equipo.`,
        imp:`Reducir CV al <30% significa pasar de rango ${minT}–${maxT} a rango ${(avgT*0.85).toFixed(0)}–${(avgT*1.15).toFixed(0)} min → operación predecible y planificable`
      });
    }
  });

  // ══ BLOQUE 2: Análisis de incidencias documentadas en obs ══
  const incKW=[
    {k:'interrump',label:'interrupciones'},
    {k:'falta de personal',label:'falta de personal'},
    {k:'cruzadas',label:'tarimas cruzadas'},
    {k:'multitarea',label:'montacarguista multitarea'},
    {k:'sin flujo',label:'flujo interrumpido'},
    {k:'demasia',label:'operación demasiado lenta'},
    {k:'lenta',label:'operación lenta'},
    {k:'espacio',label:'espacio reducido'}
  ];
  const incDetalle={};
  D.forEach(r=>{
    if(!r.obs)return;
    incKW.forEach(({k,label})=>{
      if(r.obs.toLowerCase().includes(k)){
        if(!incDetalle[label]) incDetalle[label]=[];
        incDetalle[label].push({veh:r.veh,t:r.t,sem:r.sem,mes:r.mes});
      }
    });
  });
  const totalInc=Object.values(incDetalle).reduce((s,a)=>s+a.length,0);
  if(totalInc>=2){
    const topCausa=Object.entries(incDetalle).sort((a,b)=>b[1].length-a[1].length)[0];
    const resumen=Object.entries(incDetalle).map(([k,v])=>`${k}: ${v.length} ${v.length>1?'veces':'vez'}`).join(' · ');
    items.push({p:'alta',v:'CD general',tipoOp:'Ambas',tag:'Lean: 5 Porqués',
      title:`${totalInc} registros con incidencias documentadas — causas raíz sin resolver`,
      pb:`Causas detectadas en las observaciones: <strong>${resumen}</strong>. Causa más frecuente: "<strong>${topCausa[0]}</strong>" (${topCausa[1].length} eventos). Estas incidencias inflaron directamente los tiempos registrados.`,
      ac:`Crear un "Registro de Incidencias" formal con categorías estándar. Hacer sesión de 5 Porqués mensual con el equipo para las top 3 causas. Definir acción correctiva para cada tipo (ej. ${topCausa[0]==='falta de personal'?'confirmación de turno 24h antes':'protocolo de verificación antes de iniciar operación'}).`,
      imp:`Eliminar incidencias recurrentes puede reducir entre 10–30 min en las operaciones afectadas y evitar ${totalInc} registros fuera de meta por mes`
    });
  }

  // ══ BLOQUE 3: Impacto de dotación de personal ══
  const vehsConP=[...new Set(D.filter(r=>r.p!=null&&r.p>0).map(r=>r.veh))];
  vehsConP.forEach(v=>{
    const g1=D.filter(r=>r.veh===v&&r.p===1).map(r=>r.t);
    const g2=D.filter(r=>r.veh===v&&r.p===2).map(r=>r.t);
    const g3=D.filter(r=>r.veh===v&&r.p>=3).map(r=>r.t);
    const tipoOp=D.find(r=>r.veh===v)?.tipo||'General';
    // Compare meaningful groups
    const grupos=[];
    if(g1.length) grupos.push({n:'1 persona',avg:+av(g1).toFixed(1),cnt:g1.length});
    if(g2.length) grupos.push({n:'2 personas',avg:+av(g2).toFixed(1),cnt:g2.length});
    if(g3.length) grupos.push({n:'3+ personas',avg:+av(g3).toFixed(1),cnt:g3.length});
    if(grupos.length<2)return;
    const fastest=grupos.reduce((a,b)=>a.avg<b.avg?a:b);
    const slowest=grupos.reduce((a,b)=>a.avg>b.avg?a:b);
    const diff=+(slowest.avg-fastest.avg).toFixed(1);
    if(diff>5){
      items.push({p:diff>15?'alta':'media',v,tipoOp,tag:'Lean: Flujo continuo',
        title:`${v} — Diferencia de ${diff} min según dotación de personal`,
        pb:`${grupos.map(g=>`Con <strong>${g.n}</strong>: promedio <strong>${g.avg} min</strong> (${g.cnt} registros)`).join(' · ')}. La diferencia entre dotación mínima y óptima es de <strong>${diff} min/operación</strong>.`,
        ac:`Establecer dotación mínima de <strong>${fastest.n}</strong> como estándar en el plan de turno. Confirmar recursos disponibles antes de la llegada del vehículo, no al momento. Incluir en la programación diaria de Supply Chain.`,
        imp:`Operar siempre con dotación óptima (${fastest.n}) = ahorro de ${diff} min/operación → cumplimiento de meta más consistente`
      });
    }
  });

  // ══ BLOQUE 4: Brecha Local vs Importado ══
  ['Furgón 22 tar.','Furgón 26 tar.'].forEach(v=>{
    const loc=D.filter(r=>r.veh===v&&r.prov==='Local').map(r=>r.t);
    const imp=D.filter(r=>r.veh===v&&r.prov==='Importado').map(r=>r.t);
    if(!loc.length||!imp.length)return;
    const avgL=+av(loc).toFixed(1), avgI=+av(imp).toFixed(1);
    const diff=+(Math.abs(avgI-avgL)).toFixed(1);
    if(diff<5)return;
    const masLento=avgI>avgL?'Importado':'Local';
    const masRapido=avgI>avgL?'Local':'Importado';
    items.push({p:diff>12?'media':'baja',v,tipoOp:'Descarga',tag:'Lean: SOP diferenciado',
      title:`${v} — Descarga ${masLento} es ${diff} min más lenta que ${masRapido}`,
      pb:`Proveedores <strong>Locales</strong>: ${avgL} min promedio (${loc.length} registros) · <strong>Importados</strong>: ${avgI} min promedio (${imp.length} registros). Brecha de <strong>${diff} min</strong>. Posibles causas: documentación aduanera, tipo de embalaje, estado de tarimas.`,
      ac:`Crear SOP diferenciado por origen del proveedor. Para ${masLento}: checklist pre-descarga reforzado (documentación, temperatura, estado de tarimas). Comunicar a proveedores ${masLento.toLowerCase()}s el estándar de entrega esperado.`,
      imp:`Acercar el tiempo de descarga ${masLento.toLowerCase()} al ${masRapido.toLowerCase()} (${Math.min(avgL,avgI)} min) = reducción de ${diff} min/operación`
    });
  });

  // ══ BLOQUE 5: Tendencia semanal (¿mejorando o empeorando?) ══
  const semsOrden=[...new Set(D.map(r=>r.sem))].sort();
  if(semsOrden.length>=3){
    const primeras=semsOrden.slice(0,2);
    const ultimas=semsOrden.slice(-2);
    const tPrim=D.filter(r=>primeras.includes(r.sem)).map(r=>r.t);
    const tUlt=D.filter(r=>ultimas.includes(r.sem)).map(r=>r.t);
    if(tPrim.length&&tUlt.length){
      const avgPrim=+av(tPrim).toFixed(1), avgUlt=+av(tUlt).toFixed(1);
      const delta=+(avgUlt-avgPrim).toFixed(1);
      if(Math.abs(delta)>3){
        items.push({p:'baja',v:'Tendencia global',tipoOp:'General',tag:'Lean: Control estadístico',
          title:delta>0?`⚠ Tendencia negativa: +${delta} min vs semanas iniciales`:`✅ Tendencia positiva: −${Math.abs(delta)} min vs semanas iniciales`,
          pb:delta>0
            ?`Promedio semanas <strong>${primeras.join('/')}</strong>: <strong>${avgPrim} min</strong>. Promedio semanas <strong>${ultimas.join('/')}</strong>: <strong>${avgUlt} min</strong>. Incremento de <strong>+${delta} min</strong> — la operación está tardando más con el tiempo.`
            :`Promedio semanas <strong>${primeras.join('/')}</strong>: <strong>${avgPrim} min</strong>. Promedio semanas <strong>${ultimas.join('/')}</strong>: <strong>${avgUlt} min</strong>. Mejora real de <strong>−${Math.abs(delta)} min</strong> — la operación está mejorando.`,
          ac:delta>0
            ?`Investigar qué cambió entre ${primeras.join('/')} y ${ultimas.join('/')} (personal, volumen, proveedores, horarios). Aplicar análisis causa-efecto. Revisar si coincide con cambios en la programación o en la dotación del CD.`
            :`Documentar las prácticas de ${ultimas.join('/')} como nuevo estándar. Identificar qué mejoró y asegurarse de que sea sostenible en el tiempo. Actualizar el SOP si aplica.`,
          imp:delta>0?`Detener la tendencia negativa antes de que se convierta en el nuevo estándar del CD`:`Consolidar la mejora de ${Math.abs(delta)} min y establecerla como nuevo estándar operacional`
        });
      }
    }
  }

  // ══ BLOQUE 6: Operaciones con pocos datos — recomendación de medición ══
  ALL_VEHS.forEach(v=>{
    const cnt=getVehData(v).length;
    if(cnt>0&&cnt<5){
      const tipoOp=D.find(r=>r.veh===v)?.tipo||'General';
      items.push({p:'baja',v,tipoOp,tag:'Lean: Medición',
        title:`${v} — Solo ${cnt} registro${cnt>1?'s':''}: análisis estadístico limitado`,
        pb:`Con ${cnt} dato${cnt>1?'s':''} no es posible calcular capacidad de proceso (Cp/Cpk) ni detectar tendencias confiables. Se necesitan mínimo 10–15 registros para análisis estadístico robusto.`,
        ac:`Priorizar la toma de tiempos en esta operación las próximas 2–3 semanas. Registrar en la pestaña "Registro" cada operación real. Incluir: número de personas, tipo de proveedor y observaciones de incidencias.`,
        imp:`Con 10+ registros: análisis de capacidad Cp/Cpk, intervalos de confianza y detección de outliers. Decisiones basadas en evidencia estadística, no intuición.`
      });
    }
  });

  // ── Fallback si no hay datos suficientes ──
  if(!items.length){
    items.push({p:'baja',v:'CD general',tipoOp:'General',tag:'Lean: Medición',
      title:'Sin datos suficientes para análisis automático',
      pb:`El sistema no encontró desviaciones ni patrones con los registros actuales (${D.length} registros). Esto puede ser una buena señal: todas las operaciones están dentro de meta.`,
      ac:`Continuar registrando tiempos semanalmente. El análisis automático se activará al detectar desviaciones, variabilidad alta o patrones de incidencias.`,
      imp:`Un CD sin desviaciones con datos suficientes es el objetivo final del programa de mejora continua`
    });
  }

  // ── Ordenar: alta > media > baja, luego por devPct implícito ──
  const ord={alta:0,media:1,baja:2};
  items.sort((a,b)=>ord[a.p]-ord[b.p]);

  // ── Render ──
  const pl={alta:'Prioridad alta',media:'Prioridad media',baja:'Prioridad baja'};
  const meses=[...new Set(D.map(r=>r.mes))];
  document.getElementById('ca').textContent=items.filter(m=>m.p==='alta').length;
  document.getElementById('cm').textContent=items.filter(m=>m.p==='media').length;
  document.getElementById('cb').textContent=items.filter(m=>m.p==='baja').length;

  document.getElementById('ml').innerHTML=`
    <div class="insight" style="margin-bottom:12px;line-height:1.6;">
      <strong>🤖 Análisis generado automáticamente</strong> desde <strong>${D.length} registros operacionales</strong> 
      (${meses.join(', ')}) · Se actualiza en tiempo real al agregar nuevos datos en la pestaña Registro · 
      Metodología: análisis de desviación vs meta, variabilidad CV, impacto de dotación, incidencias y tendencia semanal.
    </div>
  `+items.map((m,i)=>`
    <div class="mc">
      <div class="mi" style="background:${PB[m.p]};color:${PC[m.p]};">${i+1}</div>
      <div style="flex:1;min-width:0;">
        <div style="display:flex;gap:6px;align-items:center;flex-wrap:wrap;margin-bottom:6px;">
          <p style="font-weight:500;font-size:13px;color:var(--color-text-primary);">${m.title}</p>
          <span class="badge" style="background:${PB[m.p]};color:${PC[m.p]};">${pl[m.p]}</span>
          <span class="badge" style="background:${TB[m.tipoOp]||TB.General};color:${TC[m.tipoOp]||TC.General};">${m.tipoOp}</span>
          <span class="badge" style="background:rgba(112,32,130,0.08);color:#702082;">${m.tag}</span>
        </div>
        <div class="sep"></div>
        <p style="font-size:12px;color:${PC[m.p]};margin-bottom:5px;"><span style="font-weight:500;">📊 Evidencia de datos:</span> ${m.pb}</p>
        <p style="font-size:12px;color:var(--color-text-secondary);margin-bottom:5px;"><span style="font-weight:500;">⚡ Acción recomendada:</span> ${m.ac}</p>
        <p style="font-size:12px;color:#27AE60;"><span style="font-weight:500;">🎯 Impacto esperado:</span> ${m.imp}</p>
      </div>
    </div>
  `).join('');
}

function ttf(){
  if(!editAuth){showPwdModal(()=>ttf());return;}
  const f=document.getElementById('trec_af');f.style.display=f.style.display==='none'?'block':'none';
}
function tar(){
  const c=parseFloat(document.getElementById('tnc').value);
  const r=parseFloat(document.getElementById('tnr').value);
  if((!c||isNaN(c))&&(!r||isNaN(r))){alert('Ingresa al menos un tiempo de trasiego.');return;}
  const record={
    id:ntid++,
    mes:document.getElementById('tnm').value,
    congelado:isNaN(c)?null:c,
    refrigerado:isNaN(r)?null:r,
    personas:parseInt(document.getElementById('tnp').value)||null,
    cajas:parseInt(document.getElementById('tnb').value)||null,
    camion:document.getElementById('tntv').value,
    obs:document.getElementById('tno').value,
    ts:Date.now()
  };
  if(dbTrasiegoRef){
    dbTrasiegoRef.push(record).catch(e=>{ DT.push(record); renderTrasiego(); uts(); });
  } else {
    DT.push(record); renderTrasiego(); uts();
  }
  ['tnc','tnr','tnp','tnb','tno'].forEach(i=>document.getElementById(i).value='');
  document.getElementById('trec_af').style.display='none';
}
function dtd(id){
  if(!editAuth){showPwdModal(()=>dtd(id));return;}
  if(!confirm('¿Eliminar este registro?'))return;
  const rec=DT.find(r=>r.id===id);
  if(dbTrasiegoRef&&rec&&rec._fbKey){
    dbTrasiegoRef.child(rec._fbKey).remove();
  } else {
    const i=DT.findIndex(r=>r.id===id);
    if(i>=0)DT.splice(i,1);
    renderTrasiego();
  }
}

function renderTrasiego(){
  xc('trec_bar');xc('trec_prod');

  const cVals=DT.filter(r=>r.congelado!=null).map(r=>r.congelado);
  const rVals=DT.filter(r=>r.refrigerado!=null).map(r=>r.refrigerado);
  const cajasAll=DT.filter(r=>r.cajas&&r.congelado).map(r=>r.cajas);
  const persAll=DT.filter(r=>r.personas!=null).map(r=>r.personas);
  const avgC=cVals.length?+(cVals.reduce((s,v)=>s+v,0)/cVals.length).toFixed(2):0;
  const avgR=rVals.length?+(rVals.reduce((s,v)=>s+v,0)/rVals.length).toFixed(2):0;
  const avgCaj=cajasAll.length?+(cajasAll.reduce((s,v)=>s+v,0)/cajasAll.length).toFixed(0):0;
  const prodVal=DT.filter(r=>r.cajas&&r.congelado).map(r=>+(r.cajas/r.congelado).toFixed(2));
  const avgProd=prodVal.length?+(prodVal.reduce((s,v)=>s+v,0)/prodVal.length).toFixed(2):0;

  document.getElementById('trec_kpis').innerHTML=`
    <div class="kc">
      <div class="kt"><p class="kl">Prom. Trasiego Congelado</p><div class="kb" style="background:rgba(26,107,174,0.12);"><svg width="14" height="14" viewBox="0 0 14 14" fill="none"><path d="M7 2v10M2 7h10M4 4l6 6M10 4L4 10" stroke="#1A6BAE" stroke-width="1.5" stroke-linecap="round"/></svg></div></div>
      <p class="kv" style="color:#1A6BAE;">${avgC||'—'}</p>
      <p class="ks">min / operación · ${cVals.length} registros</p>
      <div class="kbar"><div class="kfill" style="width:${Math.min(100,avgC/60*100).toFixed(0)}%;background:#1A6BAE;"></div></div>
    </div>
    <div class="kc">
      <div class="kt"><p class="kl">Prom. Trasiego Refrigerado</p><div class="kb" style="background:rgba(39,174,96,0.12);"><svg width="14" height="14" viewBox="0 0 14 14" fill="none"><path d="M7 3v8M4 5l3-2 3 2M4 9l3 2 3-2" stroke="#27AE60" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg></div></div>
      <p class="kv" style="color:#27AE60;">${avgR||'—'}</p>
      <p class="ks">min / operación · ${rVals.length} registros</p>
      <div class="kbar"><div class="kfill" style="width:${Math.min(100,avgR/60*100).toFixed(0)}%;background:#27AE60;"></div></div>
    </div>
    <div class="kc">
      <div class="kt"><p class="kl">Cajas promedio / operación</p><div class="kb" style="background:rgba(255,199,44,0.15);"><svg width="14" height="14" viewBox="0 0 14 14" fill="none"><rect x="2" y="5" width="10" height="7" rx="1.5" stroke="#FFC72C" stroke-width="1.5"/><path d="M5 5V4a2 2 0 014 0v1" stroke="#FFC72C" stroke-width="1.5"/></svg></div></div>
      <p class="kv">${avgCaj||'—'}</p>
      <p class="ks">cajas cargadas promedio</p>
      <div class="kbar"><div class="kfill" style="width:${Math.min(100,avgCaj/400*100).toFixed(0)}%;background:#FFC72C;"></div></div>
    </div>
    <div class="kc">
      <div class="kt"><p class="kl">Productividad</p><div class="kb" style="background:rgba(112,32,130,0.12);"><svg width="14" height="14" viewBox="0 0 14 14"><rect x="2" y="6" width="3" height="7" rx="1" fill="#702082"/><rect x="6" y="3" width="3" height="10" rx="1" fill="#702082"/><rect x="10" y="1" width="3" height="12" rx="1" fill="#702082"/></svg></div></div>
      <p class="kv" style="color:#702082;">${avgProd||'—'}</p>
      <p class="ks">cajas / minuto (congelado)</p>
      <div class="kbar"><div class="kfill" style="width:${Math.min(100,avgProd/20*100).toFixed(0)}%;background:#702082;"></div></div>
    </div>
  `;

  // Gráfica de barras congelado vs refrigerado por mes
  const meses=[...new Set(DT.map(r=>r.mes))];
  const avgCmes=meses.map(m=>{const v=DT.filter(r=>r.mes===m&&r.congelado!=null).map(r=>r.congelado);return v.length?+(v.reduce((s,x)=>s+x,0)/v.length).toFixed(2):null;});
  const avgRmes=meses.map(m=>{const v=DT.filter(r=>r.mes===m&&r.refrigerado!=null).map(r=>r.refrigerado);return v.length?+(v.reduce((s,x)=>s+x,0)/v.length).toFixed(2):null;});

  CI['trec_bar']=new Chart(document.getElementById('c_trec_bar'),{
    type:'bar',
    data:{labels:meses,datasets:[
      {label:'Congelado',data:avgCmes,backgroundColor:'rgba(26,107,174,0.75)',borderRadius:5,barPercentage:0.55},
      {label:'Refrigerado',data:avgRmes,backgroundColor:'rgba(39,174,96,0.75)',borderRadius:5,barPercentage:0.55}
    ]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false},tooltip:{callbacks:{label:ctx=>`${ctx.raw!=null?ctx.raw+' min':'Sin datos'}`}}},scales:{y:{beginAtZero:true,title:{display:true,text:'Minutos',font:{size:11}},ticks:{font:{size:11}}},x:{ticks:{font:{size:11}}}}}
  });

  // Gráfica de productividad (cajas/min) por registro
  const prodLabels=DT.filter(r=>r.cajas&&r.congelado).map(r=>`#${r.id} ${r.mes}`);
  const prodData=DT.filter(r=>r.cajas&&r.congelado).map(r=>+(r.cajas/r.congelado).toFixed(2));
  CI['trec_prod']=new Chart(document.getElementById('c_trec_prod'),{
    type:'bar',
    data:{labels:prodLabels.length?prodLabels:['Sin datos'],datasets:[{label:'Cajas/min',data:prodData.length?prodData:[0],backgroundColor:'rgba(112,32,130,0.7)',borderRadius:5}]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false},tooltip:{callbacks:{label:ctx=>` ${ctx.raw} cajas/min`}}},scales:{y:{beginAtZero:true,title:{display:true,text:'Cajas / min',font:{size:11}},ticks:{font:{size:11}}},x:{ticks:{font:{size:11}}}}}
  });

  // Tabla
  document.getElementById('trec_tb').innerHTML=DT.map(r=>`
    <tr>
      <td style="color:var(--color-text-tertiary);font-size:10px;">${r.id}</td>
      <td style="font-size:11px;">${r.mes}</td>
      <td style="font-weight:500;color:#1A6BAE;">${r.congelado!=null?r.congelado.toFixed(2)+' min':'—'}</td>
      <td style="font-weight:500;color:#27AE60;">${r.refrigerado!=null?r.refrigerado.toFixed(2)+' min':'—'}</td>
      <td style="font-size:11px;">${r.personas||'—'}</td>
      <td style="font-weight:500;">${r.cajas||'—'}</td>
      <td style="font-size:11px;color:#702082;">${r.cajas&&r.congelado?+(r.cajas/r.congelado).toFixed(2)+' c/min':'—'}</td>
      <td style="font-size:11px;">${r.camion}</td>
      <td style="font-size:11px;max-width:140px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;" title="${r.obs}">${r.obs||'—'}</td>
      <td><button onclick="dtd(${r.id})" style="background:none;border:none;cursor:pointer;color:#E74C3C;font-size:14px;padding:2px 6px;line-height:1;">×</button></td>
    </tr>
  `).join('');
  document.getElementById('trec_rc').textContent=`${DT.length} registro${DT.length!==1?'s':''}`;

  // Insight
  const insEl=document.getElementById('trec_insight');
  if(DT.length>0){
    const ratio=avgC&&avgR?(avgC/avgR).toFixed(1):'—';
    insEl.innerHTML=`<strong>Análisis:</strong> El tiempo de trasiego de producto <strong>congelado (${avgC} min)</strong> es significativamente mayor al de <strong>refrigerado (${avgR} min)</strong>${ratio!=='—'?' — relación '+ratio+'×.':'.'}  Con ${DT.filter(r=>r.cajas&&r.congelado)[0]?.cajas||'—'} cajas cargadas por operación y ${DT.filter(r=>r.personas)[0]?.personas||'—'} personas, la productividad alcanza <strong>${avgProd} cajas/min</strong>. Continuar registrando para detectar tendencias.`;
  } else {
    insEl.innerHTML='<strong>Sin datos suficientes.</strong> Agrega registros para ver análisis.';
  }
}


function renderMetas(){
  const META_INFO={
    'Camión 10-12t':{meta:22,tipo:'Carga',icon:'🚛',justif:'Mejor registro: 17.3 min. Promedio actual: 22.7 min. Meta alcanzable con 3 operadores y zona despejada.',sugerencia:'Revisar posición de tarimas pre-carga. Montacarguista dedicado.'},
    'Camión 5.4t'   :{meta:40,tipo:'Carga',icon:'🚚',justif:'Alta variabilidad: 24.6–79.5 min. Meta de proceso estabilizado con SOP y checklist pre-carga.',sugerencia:'Implementar checklist pre-carga. Definir ventana horaria fija AM.'},
    'Furgón 22 tar.':{meta:25,tipo:'Descarga',icon:'📦',justif:'Mejor registro: 18.3 min (4 personas). Promedio actual: 26.1 min. Meta con coordinación óptima.',sugerencia:'Coordinar montacargas con anticipación. Separar área de recepción.'},
    'Furgón 26 tar.':{meta:30,tipo:'Descarga',icon:'🏗️',justif:'Mejor registro: 19.3 min (2 aux + montacargas). Promedio actual: 31.2 min. Meta con dotación estándar.',sugerencia:'Mínimo 2 auxiliares + montacarguista dedicado. Tarimas sin cruzar.'},
    'Camión granel' :{meta:38,tipo:'Descarga',icon:'⬇️',justif:'Único registro: 40.5 min. Meta del 6% de mejora como objetivo inicial de reducción.',sugerencia:'Definir zona fija de descarga a granel. Tener envases listos al llegar.'},
    'Camión pallets':{meta:18,tipo:'Descarga',icon:'🔲',justif:'Mejor registro: 12.2 min. Promedio actual: 17.2 min. Meta de mantenimiento de buen desempeño.',sugerencia:'Estandarizar andén de llegada para pallets. Pallet jack listo desde inicio.'},
  };
  const ORDER=['Camión 10-12t','Camión 5.4t','Furgón 22 tar.','Furgón 26 tar.','Camión granel','Camión pallets'];

  const grid=document.getElementById('meta_grid');
  grid.innerHTML=ORDER.map(v=>{
    const info=META_INFO[v];
    const avg=+av(getVehData(v)).toFixed(1);
    const pct=avg>0?+((avg-info.meta)/info.meta*100).toFixed(1):0;
    const status=avg<=info.meta?'ok':avg<=info.meta*1.3?'warn':'crit';
    const statusColor=status==='ok'?'#27AE60':status==='warn'?'#E67E22':'#E74C3C';
    const statusBg=status==='ok'?'rgba(39,174,96,0.08)':status==='warn'?'rgba(230,126,34,0.08)':'rgba(231,76,60,0.08)';
    const statusLabel=status==='ok'?'✔ En meta':status==='warn'?'⚠ Revisar':'✘ Crítico';
    const barW=Math.min(100,avg/80*100).toFixed(0);
    const metaW=Math.min(100,info.meta/80*100).toFixed(0);
    const tipoBg=info.tipo==='Carga'?'rgba(112,32,130,0.1)':'rgba(235,0,139,0.1)';
    const tipoColor=info.tipo==='Carga'?'#702082':'#EB008B';
    return `
    <div style="background:var(--color-background-secondary);border-radius:10px;padding:11px 12px;border:1px solid ${statusBg};">
      <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:6px;">
        <span style="font-size:13px;">${info.icon}</span>
        <span style="background:${tipoBg};color:${tipoColor};padding:1px 6px;border-radius:4px;font-size:10px;font-weight:600;">${info.tipo}</span>
      </div>
      <p style="font-size:11px;font-weight:600;color:var(--color-text-primary);margin-bottom:8px;line-height:1.3;">${v}</p>
      <div style="display:flex;align-items:baseline;gap:6px;margin-bottom:4px;">
        <span style="font-size:20px;font-weight:600;color:#702082;">${info.meta}</span>
        <span style="font-size:11px;color:var(--color-text-tertiary);">min meta</span>
        <span style="margin-left:auto;font-size:11px;font-weight:500;color:${statusColor};">${statusLabel}</span>
      </div>
      <div style="position:relative;height:5px;background:var(--color-border-tertiary);border-radius:3px;margin-bottom:4px;">
        <div style="position:absolute;left:0;top:0;height:5px;width:${barW}%;background:${statusColor};border-radius:3px;opacity:0.6;"></div>
        <div style="position:absolute;left:${metaW}%;top:-3px;height:11px;width:2px;background:#FFC72C;border-radius:1px;" title="Meta: ${info.meta} min"></div>
      </div>
      <div style="display:flex;justify-content:space-between;font-size:10px;color:var(--color-text-tertiary);margin-bottom:6px;">
        <span>Actual: <strong style="color:${statusColor};">${avg||'—'} min</strong></span>
        <span>${avg>0?(pct>0?'+'+pct+'%':pct+'%'):'—'} vs meta</span>
      </div>
      <p style="font-size:10px;color:var(--color-text-tertiary);line-height:1.4;border-top:0.5px solid var(--color-border-tertiary);padding-top:5px;">${info.sugerencia}</p>
    </div>`;
  }).join('');

  const totalFuera=ORDER.filter(v=>{const a=av(getVehData(v));return a>0&&a>TARGETS[v];}).length;
  const nota=document.getElementById('meta_nota');
  if(nota) nota.innerHTML='';
}

function uts(){
  const n=new Date();
  document.getElementById('ts').textContent='Actualizado '+n.toLocaleTimeString('es-GT',{hour:'2-digit',minute:'2-digit'});
}

function ra(){rk();renderMetas();renderDashCharts();renderCompCharts();rt();renderTrasiego();renderMejoras();}

// ══════════════════════════════════════════════════════════
//  🔐 SISTEMA DE CONTRASEÑA
// ══════════════════════════════════════════════════════════
function showPwdModal(action){
  pendingAction=action;
  document.getElementById('pwd_modal').style.display='flex';
  document.getElementById('pwd_error').style.display='none';
  setTimeout(()=>document.getElementById('pwd_input').focus(),80);
}
function closePwdModal(){
  document.getElementById('pwd_modal').style.display='none';
  document.getElementById('pwd_input').value='';
  pendingAction=null;
}
function checkPwd(){
  const input=document.getElementById('pwd_input').value;
  if(input===EDIT_PASSWORD){
    editAuth=true;
    document.getElementById('pwd_modal').style.display='none';
    document.getElementById('pwd_input').value='';
    document.getElementById('pwd_error').style.display='none';
    // Mostrar badge de sesión activa en el header
    const badge=document.getElementById('edit_badge');
    if(badge) badge.style.display='inline-flex';
    if(pendingAction){const a=pendingAction;pendingAction=null;a();}
  } else {
    document.getElementById('pwd_error').style.display='block';
    document.getElementById('pwd_input').value='';
    document.getElementById('pwd_input').focus();
  }
}

// ══════════════════════════════════════════════════════════
//  🤖 ANÁLISIS CON INTELIGENCIA ARTIFICIAL (Claude API)
// ══════════════════════════════════════════════════════════
let aiApiKey = '';

function showAiPanel(){
  const p=document.getElementById('ai_panel');
  p.style.display=p.style.display==='none'?'block':'none';
}

function saveApiKey(){
  const k=document.getElementById('ai_key_input').value.trim();
  if(!k.startsWith('sk-ant-')){
    document.getElementById('ai_key_status').textContent='⚠ El formato no parece correcto — debe empezar con sk-ant-...';
    document.getElementById('ai_key_status').style.color='#E67E22';
    return;
  }
  aiApiKey=k;
  document.getElementById('ai_key_input').value='••••••••••••••••••••';
  document.getElementById('ai_key_status').innerHTML='✅ Clave guardada en sesión — listo para analizar';
  document.getElementById('ai_key_status').style.color='#27AE60';
  document.getElementById('ai_actions').style.display='flex';
  document.getElementById('ai_key_badge').textContent='Clave activa esta sesión';
}

function buildDataSummary(){
  const lines=[];
  lines.push(`=== DATOS DEL CD TACO BELL GUATEMALA (${D.length} registros, Marzo–Abril 2026) ===\n`);

  lines.push('--- OPERACIONES DE CARGA ---');
  ['Camión 10-12t','Camión 5.4t'].forEach(v=>{
    const data=getVehData(v);
    if(!data.length)return;
    const avg=+av(data).toFixed(1), minT=+mn(data).toFixed(1), maxT=+mx(data).toFixed(1);
    const tgt=TARGETS[v], dev=+((avg-tgt)/tgt*100).toFixed(0);
    const excCnt=data.filter(t=>t>tgt).length;
    lines.push(`• ${v}: promedio=${avg}min | min=${minT} | max=${maxT} | meta=${tgt}min | desviación=${dev>0?'+':''}${dev}% | ${excCnt}/${data.length} registros fuera de meta`);
  });

  lines.push('\n--- OPERACIONES DE DESCARGA ---');
  ['Furgón 22 tar.','Furgón 26 tar.','Camión granel','Camión pallets'].forEach(v=>{
    const data=getVehData(v);
    if(!data.length)return;
    const avg=+av(data).toFixed(1), minT=+mn(data).toFixed(1), maxT=+mx(data).toFixed(1);
    const tgt=TARGETS[v], dev=+((avg-tgt)/tgt*100).toFixed(0);
    const loc=D.filter(r=>r.veh===v&&r.prov==='Local').map(r=>r.t);
    const imp=D.filter(r=>r.veh===v&&r.prov==='Importado').map(r=>r.t);
    let provStr='';
    if(loc.length) provStr+=` | Local=${+av(loc).toFixed(1)}min(${loc.length}reg)`;
    if(imp.length) provStr+=` | Importado=${+av(imp).toFixed(1)}min(${imp.length}reg)`;
    lines.push(`• ${v}: promedio=${avg}min | min=${minT} | max=${maxT} | meta=${tgt}min | desviación=${dev>0?'+':''}${dev}%${provStr}`);
  });

  lines.push('\n--- INCIDENCIAS DOCUMENTADAS EN OBSERVACIONES ---');
  const incRecs=D.filter(r=>r.obs&&r.obs.trim()!=='');
  if(incRecs.length){
    incRecs.slice(0,8).forEach(r=>lines.push(`• [${r.sem} ${r.mes}] ${r.veh} (${r.t}min): ${r.obs}`));
    if(incRecs.length>8) lines.push(`  ...y ${incRecs.length-8} observaciones más`);
  } else {
    lines.push('Sin incidencias documentadas');
  }

  lines.push('\n--- DOTACIÓN DE PERSONAL ---');
  const conP=D.filter(r=>r.p!=null&&r.p>0);
  if(conP.length){
    const g1=conP.filter(r=>r.p===1), g2=conP.filter(r=>r.p===2), g3=conP.filter(r=>r.p>=3);
    if(g1.length) lines.push(`• 1 persona: ${g1.length} ops, promedio ${+av(g1.map(r=>r.t)).toFixed(1)}min`);
    if(g2.length) lines.push(`• 2 personas: ${g2.length} ops, promedio ${+av(g2.map(r=>r.t)).toFixed(1)}min`);
    if(g3.length) lines.push(`• 3+ personas: ${g3.length} ops, promedio ${+av(g3.map(r=>r.t)).toFixed(1)}min`);
  }

  lines.push('\n--- TRASIEGO DE PRODUCTO ---');
  if(DT.length){
    const c=DT.filter(r=>r.congelado!=null).map(r=>r.congelado);
    const ref=DT.filter(r=>r.refrigerado!=null).map(r=>r.refrigerado);
    if(c.length) lines.push(`• Congelado: ${+av(c).toFixed(1)}min promedio (${c.length} registros)`);
    if(ref.length) lines.push(`• Refrigerado: ${+av(ref).toFixed(1)}min promedio (${ref.length} registros)`);
    const prodVal=DT.filter(r=>r.cajas&&r.congelado).map(r=>+(r.cajas/r.congelado).toFixed(2));
    if(prodVal.length) lines.push(`• Productividad: ${+av(prodVal).toFixed(2)} cajas/min`);
  } else {
    lines.push('Sin registros de trasiego');
  }

  const sems=[...new Set(D.map(r=>r.sem))].sort();
  lines.push(`\n--- SEMANAS CON DATOS: ${sems.join(', ')} ---`);
  lines.push(`Total registros carga: ${D.filter(r=>r.tipo==='Carga').length} | descarga: ${D.filter(r=>r.tipo==='Descarga').length}`);

  return lines.join('\n');
}

function buildPrompt(tipo){
  const data=buildDataSummary();
  const base=`Eres un experto en Supply Chain y Lean Manufacturing. Analiza estos datos operacionales del Centro de Distribución (CD) de Taco Bell Guatemala (operado por Belluno S.A.), programa de mejora continua Supply Chain Trainee 2026.\n\nResponde SIEMPRE en español, de forma clara, directa y orientada a la acción.\n\n${data}\n\n`;

  const prompts={
    resumen: base+`Genera un RESUMEN EJECUTIVO para presentar al Gerente de Supply Chain. Incluye:

📌 ESTADO GENERAL DEL CD (2-3 oraciones, tono ejecutivo)
✅ LO QUE ESTÁ FUNCIONANDO BIEN (máximo 3 puntos con datos)
⚠ PROBLEMAS CRÍTICOS (máximo 3 puntos con datos específicos)
📈 RECOMENDACIÓN PRINCIPAL PARA ESTA SEMANA (una acción concreta)
🎯 KPI CLAVE A MONITOREAR (el indicador más importante ahora mismo)

Sé conciso. El gerente necesita entender la situación en menos de 2 minutos.`,

    prioridades: base+`Identifica las TOP 3 PRIORIDADES OPERACIONALES para esta semana en el CD. Para cada una:

PRIORIDAD #N: [nombre]
• Evidencia: [dato específico que justifica la prioridad]
• Responsable sugerido: [quién debe ejecutarla]
• Acción concreta: [qué hacer exactamente, paso a paso]
• Resultado esperado: [qué mejora se debe ver esta semana]
• Indicador de éxito: [cómo saber que funcionó]

Ordénalas de mayor a menor impacto en los tiempos de operación.`,

    proveedores: base+`Realiza un ANÁLISIS DE PROVEEDORES basado en los datos disponibles. Incluye:

📦 COMPARATIVA LOCAL vs IMPORTADO
• Diferencias en tiempo de descarga por tipo de furgón
• ¿Qué proveedor genera más variabilidad?

🏭 ANÁLISIS POR PROVEEDOR (Panifresh, Ingrup, Seaboard Marine, otros mencionados)
• Tiempo promedio de descarga por proveedor (si hay datos)
• Incidencias asociadas

📋 RECOMENDACIONES
• ¿Qué cláusulas incluir en las órdenes de compra?
• ¿Qué protocolo de pre-recepción implementar?
• ¿A qué proveedor comunicar ajustes inmediatamente?`,

    sop: base+`Genera RECOMENDACIONES DE SOP (Procedimientos Estándar de Operación) para el CD. Para cada proceso:

🚛 SOP DE CARGA (Camión 10-12t y 5.4t)
• Pasos estándar basados en los mejores tiempos registrados
• Dotación mínima recomendada
• Puntos de control y alertas
• Meta de tiempo y semáforo de seguimiento

📦 SOP DE DESCARGA (Furgón 22 tar., Furgón 26 tar., Granel, Pallets)
• Checklist pre-descarga
• Dotación mínima por tipo de vehículo
• Protocolo cuando se detectan tarimas cruzadas
• Meta de tiempo y criterio de escalamiento

📊 INDICADORES A REGISTRAR en cada operación (qué datos son críticos para el análisis futuro)`
  };
  return prompts[tipo]||prompts.resumen;
}

const AI_TITLES={
  resumen:'Resumen ejecutivo para gerencia',
  prioridades:'Top prioridades esta semana',
  proveedores:'Análisis por proveedor',
  sop:'Recomendaciones de SOP'
};

async function analyzeWithAI(tipo){
  if(!aiApiKey){
    document.getElementById('ai_setup').scrollIntoView({behavior:'smooth'});
    document.getElementById('ai_key_input').focus();
    return;
  }

  const resultEl=document.getElementById('ai_result');
  const loadingEl=document.getElementById('ai_loading');
  const bodyEl=document.getElementById('ai_result_body');
  const titleEl=document.getElementById('ai_result_title');
  const metaEl=document.getElementById('ai_result_meta');
  const loadingText=document.getElementById('ai_loading_text');

  resultEl.style.display='block';
  loadingEl.style.display='block';
  bodyEl.style.display='none';
  titleEl.textContent=AI_TITLES[tipo]||'Análisis IA';
  metaEl.textContent='Generando análisis...';
  resultEl.scrollIntoView({behavior:'smooth',block:'start'});

  const loadingMsgs=[
    'Analizando tiempos de operación...','Identificando patrones en los datos...','Calculando desviaciones vs metas...','Preparando recomendaciones lean...','Redactando análisis ejecutivo...'
  ];
  let msgIdx=0;
  const msgInterval=setInterval(()=>{
    loadingText.textContent=loadingMsgs[msgIdx%loadingMsgs.length];
    msgIdx++;
  },1800);

  try{
    const response=await fetch('https://api.anthropic.com/v1/messages',{
      method:'POST',
      headers:{
        'Content-Type':'application/json',
        'x-api-key':aiApiKey,
        'anthropic-version':'2023-06-01',
        'anthropic-dangerous-direct-browser-access':'true'
      },
      body:JSON.stringify({
        model:'claude-sonnet-4-20250514',
        max_tokens:1500,
        system:'Eres un consultor experto en Supply Chain y Lean Manufacturing para centros de distribución de alimentos en Latinoamérica. Siempre respondes en español, con datos precisos y recomendaciones accionables. Eres directo, profesional y orientado a resultados operacionales.',
        messages:[{role:'user',content:buildPrompt(tipo)}]
      })
    });

    clearInterval(msgInterval);

    if(!response.ok){
      const err=await response.json().catch(()=>({}));
      throw new Error(err.error?.message||`HTTP ${response.status}`);
    }

    const data=await response.json();
    const text=data.content?.find(b=>b.type==='text')?.text||'Sin respuesta';

    loadingEl.style.display='none';
    bodyEl.style.display='block';

    // Renderizar con formato visual
    bodyEl.innerHTML=formatAIResponse(text);
    metaEl.textContent=`Generado ${new Date().toLocaleTimeString('es-GT',{hour:'2-digit',minute:'2-digit'})} · ${D.length} registros analizados · claude-sonnet-4`;

  } catch(e){
    clearInterval(msgInterval);
    loadingEl.style.display='none';
    bodyEl.style.display='block';
    if(e.message.includes('401')||e.message.includes('authentication')){
      bodyEl.innerHTML=`<div style="color:#E74C3C;padding:12px;background:rgba(231,76,60,0.05);border-radius:8px;border:1px solid rgba(231,76,60,0.2);">
        ❌ <strong>Clave de API inválida.</strong> Verifica que copiaste correctamente la clave desde console.anthropic.com
      </div>`;
    } else if(e.message.includes('Failed to fetch')||e.message.includes('network')){
      bodyEl.innerHTML=`<div style="color:#E67E22;padding:12px;background:rgba(230,126,34,0.05);border-radius:8px;border:1px solid rgba(230,126,34,0.2);">
        ⚠ <strong>Error de conexión.</strong> Verifica tu conexión a internet e intenta de nuevo.
      </div>`;
    } else {
      bodyEl.innerHTML=`<div style="color:#E74C3C;padding:12px;background:rgba(231,76,60,0.05);border-radius:8px;">
        ❌ Error: ${e.message}
      </div>`;
    }
    metaEl.textContent='Error al generar análisis';
  }
}

function formatAIResponse(text){
  // Convertir el texto de IA en HTML con formato visual
  return text
    .replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;')
    .replace(/\n(#{1,3} .+)/g,'\n<h4 style="color:#702082;font-size:13px;font-weight:600;margin:14px 0 6px;border-bottom:1px solid rgba(112,32,130,0.12);padding-bottom:4px;">$1</h4>')
    .replace(/(📌|✅|⚠|📈|🎯|📦|🏭|📋|🚛|📊|PRIORIDAD #\d+:?)[^\n]*/g,'<p style="font-weight:500;color:#1d1d1f;margin:10px 0 4px;">$&</p>')
    .replace(/•([^\n]+)/g,'<div style="display:flex;gap:6px;margin:4px 0 4px 8px;font-size:12px;"><span style="color:#702082;flex-shrink:0;margin-top:1px;">›</span><span>$1</span></div>')
    .replace(/\*\*([^*]+)\*\*/g,'<strong>$1</strong>')
    .replace(/\n{2,}/g,'<br>')
    .replace(/\n/g,'<br>');
}
// ══════════════════════════════════════════════════════════
const SEED_D = D.map(r=>({...r}));    // copia de datos iniciales para sembrar
const SEED_DT = DT.map(r=>({...r}));

function showLoader(show){
  document.getElementById('loading_overlay').style.display=show?'flex':'none';
}

function syncFromSnapshot(snapshot, target){
  target.length=0;
  let maxId=0;
  snapshot.forEach(child=>{
    const r={...child.val(),_fbKey:child.key};
    target.push(r);
    if(r.id>maxId) maxId=r.id;
  });
  return maxId;
}

function seedFirebase(ref, data, prefix){
  const batch={};
  data.forEach(r=>{
    const pushId=ref.push().key;
    batch[pushId]={...r};
  });
  return ref.update(batch);
}

function initFirebase(){
  const configured = FIREBASE_CONFIG.apiKey !== 'PEGAR_AQUI';
  if(!configured){
    console.log('Firebase no configurado — modo local activo');
    showLoader(false); uts(); ra(); return;
  }
  try{
    if(!firebase.apps.length) firebase.initializeApp(FIREBASE_CONFIG);
    const database=firebase.database();
    dbRef=database.ref('cd_registros');
    dbTrasiegoRef=database.ref('cd_trasiego');

    showLoader(true);

    // Listener principal para registros D
    dbRef.on('value', snapshot=>{
      const maxId=syncFromSnapshot(snapshot, D);
      if(D.length===0){
        // Primera vez: sembrar datos históricos
        seedFirebase(dbRef, SEED_D, 'reg').then(()=>console.log('Datos sembrados en Firebase'));
      } else {
        if(maxId>=nid) nid=maxId+1;
        showLoader(false); uts(); ra();
      }
    }, err=>{
      console.error('Firebase error:', err);
      D=SEED_D.map(r=>({...r}));
      showLoader(false); uts(); ra();
    });

    // Listener para trasiego DT
    dbTrasiegoRef.on('value', snapshot=>{
      const maxId=syncFromSnapshot(snapshot, DT);
      if(DT.length===0){
        seedFirebase(dbTrasiegoRef, SEED_DT, 'tras');
      } else {
        if(maxId>=ntid) ntid=maxId+1;
        renderTrasiego(); uts();
      }
    });

  } catch(e){
    console.error('Firebase init error:', e);
    D=SEED_D.map(r=>({...r}));
    showLoader(false); uts(); ra();
  }
}

uts();
initFirebase();

</script>
</body>
</html>
