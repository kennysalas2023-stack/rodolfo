<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="theme-color" content="#b51f2b">
  <title>RUDOLF MOTOS | Gestión de taller</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      theme: { extend: {
        colors: { bajaj: { red: '#b51f2b', ink: '#202327', steel: '#697078', line: '#e5e7e9', mist: '#f4f5f6', green: '#26735b', amber: '#b87921' } },
        fontFamily: { sans: ['DM Sans', 'sans-serif'], mono: ['IBM Plex Mono', 'monospace'] },
        boxShadow: { panel: '0 2px 12px rgba(31,35,39,.045)' }
      }}
    };
  </script>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600;700&family=IBM+Plex+Mono:wght@400;500;600&display=swap" rel="stylesheet">
  <script src="https://unpkg.com/lucide@latest"></script>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/xlsx-js-style@1.2.0/dist/xlsx.bundle.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore-compat.js"></script>
  <style>
    :root { color-scheme: light; --red:#b51f2b; --ink:#202327; --muted:#697078; --line:#e5e7e9; --paper:#fff; --wash:#f4f5f6; }
    * { box-sizing:border-box; }
    body { margin:0; background:#f5f6f7; color:var(--ink); font-family:'DM Sans',sans-serif; }
    button,input,select,textarea { font:inherit; }
    button { cursor:pointer; }
    .sidebar { width:248px; transition:transform .2s ease; }
    .nav-link { display:flex; align-items:center; gap:11px; width:100%; padding:10px 12px; border-radius:6px; color:#555b61; font-size:13px; font-weight:600; text-align:left; transition:background .15s,color .15s; }
    .nav-link:hover { background:#f3f4f5; color:#202327; }
    .nav-link.active { background:#f9e9eb; color:#a51c27; }
    .nav-link svg { width:17px;height:17px;stroke-width:1.8; }
    .section-label { margin:25px 0 8px 12px; color:#9ba0a5; font:600 10px 'IBM Plex Mono',monospace; letter-spacing:.1em; text-transform:uppercase; }
    .page { display:none; animation:enter .22s ease both; }
    .page.active { display:block; }
    @keyframes enter { from { opacity:0; transform:translateY(4px) } to { opacity:1; transform:none } }
    .surface { background:#fff; border:1px solid #e8eaec; border-radius:6px; box-shadow:0 2px 12px rgba(31,35,39,.035); }
    .metric { min-height:118px; }
    .mono { font-family:'IBM Plex Mono',monospace; font-variant-numeric:tabular-nums; }
    .grid-table { border-collapse:separate; border-spacing:0; min-width:1040px; width:100%; }
    .grid-table th { position:sticky; top:0; z-index:1; background:#f8f9fa; color:#737a80; font:600 10px 'IBM Plex Mono',monospace; letter-spacing:.035em; text-transform:uppercase; white-space:nowrap; }
    .grid-table th,.grid-table td { padding:11px 13px; border-bottom:1px solid #eceef0; text-align:left; }
    .grid-table td { color:#43494f; font-size:12px; white-space:nowrap; }
    .grid-table tbody tr:hover { background:#fbfbfc; }
    .grid-table td[data-edit] { cursor:text; }
    .grid-table td[data-edit]:hover { background:#fff8e9; box-shadow:inset 0 0 0 1px #eccb83; }
    .field { width:100%; min-width:0; border:1px solid #dfe2e4; border-radius:4px; padding:9px 10px; outline:none; color:#262a2e; background:#fff; font-size:12px; transition:border .15s,box-shadow .15s; }
    .field:focus { border-color:#b51f2b; box-shadow:0 0 0 2px rgba(181,31,43,.1); }
    .field-label { display:block; margin-bottom:5px; color:#737a80; font-size:10px; font-weight:700; letter-spacing:.04em; text-transform:uppercase; }
    .btn { display:inline-flex; align-items:center; justify-content:center; gap:8px; min-height:36px; border:1px solid #dfe2e4; border-radius:4px; padding:0 12px; background:#fff; color:#383e43; font-size:12px; font-weight:700; transition:all .15s; }
    .btn:hover { background:#f7f8f9; border-color:#c9cdd0; }
    .btn svg { width:15px;height:15px; }
    .btn-primary { border-color:#b51f2b; background:#b51f2b; color:#fff; }
    .btn-primary:hover { background:#9f1924; border-color:#9f1924; }
    .btn-quiet { border-color:transparent; background:transparent; }
    .badge { display:inline-flex; align-items:center; gap:5px; border-radius:3px; padding:4px 7px; font-size:10px; font-weight:700; }
    .badge::before { width:5px;height:5px;border-radius:50%;background:currentColor;content:''; }
    .status-pendiente { color:#a06a18;background:#fff5df; }
    .status-proceso { color:#376b97;background:#eaf4fb; }
    .status-listo { color:#287354;background:#e8f5ee; }
    .status-entregado { color:#626970;background:#eff1f2; }
    .system-row { display:grid; grid-template-columns:minmax(150px,1fr) minmax(120px,150px) minmax(160px,2fr); gap:10px; align-items:center; border-top:1px solid #eceef0; padding:10px 0; }
    .sig-canvas { display:block; width:100%; height:120px; border:1px dashed #cdd1d4; border-radius:4px; background:#fff; touch-action:none; }
    .drop-zone { border:1px dashed #c6cbd0; border-radius:5px; background:#fafbfb; transition:all .15s; }
    .drop-zone.dragover { border-color:#b51f2b; background:#fff5f5; }
    .toast { position:fixed; right:20px; bottom:20px; z-index:60; padding:12px 16px; border-radius:5px; background:#202327; color:white; font-size:12px; box-shadow:0 8px 24px #20232733; animation:enter .2s ease; }
    .mobile-shade { display:none; }
    @media(max-width:900px) { .sidebar { position:fixed; z-index:40; inset:0 auto 0 0; transform:translateX(-100%); box-shadow:8px 0 24px #0002; } .sidebar.open { transform:translateX(0); } .mobile-shade.open { display:block; position:fixed; z-index:30; inset:0; background:#20232770; } }
    @media(max-width:640px) { .main-content { padding:16px !important; } .system-row { grid-template-columns:1fr 1fr; } .system-row .system-notes { grid-column:1/-1; } .metric { min-height:100px; } }
    @media print { body { background:#fff; } .sidebar,.topbar,.no-print,.mobile-shade { display:none!important; } .main-shell { margin:0!important; } #page-order { display:block!important; } #page-dashboard,#page-orders,#page-crm { display:none!important; } .surface { box-shadow:none; } .field { border:0; padding:2px 0; } }
  </style>
</head>
<body>
  <div id="mobile-shade" class="mobile-shade" onclick="toggleSidebar(false)"></div>
  <aside id="sidebar" class="sidebar fixed inset-y-0 left-0 z-40 flex flex-col border-r border-gray-200 bg-white px-4 py-5">
    <div class="mb-7 flex items-center gap-3 px-2">
      <div class="flex h-10 w-10 items-center justify-center rounded bg-bajaj-red text-white"><i data-lucide="wrench" class="h-5 w-5"></i></div>
      <div><div class="text-[14px] font-bold tracking-[.02em]">RUDOLF <span class="text-bajaj-red">MOTOS</span></div><div class="mt-0.5 text-[9px] font-semibold uppercase tracking-[.12em] text-gray-400">Servicio autorizado Bajaj</div></div>
    </div>
    <div class="px-2"><div class="mb-1 flex items-center gap-2 rounded border border-gray-200 bg-gray-50 px-2.5 py-2"><i data-lucide="search" class="h-4 w-4 text-gray-400"></i><input id="global-search" class="w-full bg-transparent text-xs outline-none placeholder:text-gray-400" placeholder="Buscar placa, DNI, cliente..."><kbd class="rounded border border-gray-200 bg-white px-1 text-[9px] text-gray-400">/</kbd></div></div>
    <nav class="mt-3 flex-1 overflow-y-auto">
      <div class="section-label">Operaciones</div>
      <button class="nav-link active" data-page="dashboard"><i data-lucide="layout-dashboard"></i>Resumen general</button>
      <button class="nav-link" data-page="orders"><i data-lucide="list-filter"></i>Órdenes de trabajo <span id="nav-order-count" class="ml-auto rounded bg-gray-100 px-1.5 py-0.5 text-[10px] text-gray-500">0</span></button>
      <button class="nav-link" data-page="order"><i data-lucide="file-plus-2"></i>Nueva orden técnica</button>
      <div class="section-label">Relaciones y datos</div>
      <button class="nav-link" data-page="crm"><i data-lucide="users-round"></i>Clientes y fidelización</button>
      <button class="nav-link" id="import-nav"><i data-lucide="file-up"></i>Importar Excel / CSV</button>
      <button class="nav-link" id="export-nav"><i data-lucide="file-down"></i>Exportar registros</button>
    </nav>
    <div class="border-t border-gray-100 px-2 pt-4">
      <div class="flex items-center gap-3"><div class="flex h-8 w-8 items-center justify-center rounded-full bg-[#f9e9eb] text-[11px] font-bold text-bajaj-red">RA</div><div class="min-w-0"><div class="truncate text-xs font-bold">Rodolfo Alfaro</div><div class="text-[10px] text-gray-400">Administrador</div></div><button id="settings-btn" class="ml-auto rounded p-1.5 text-gray-400 hover:bg-gray-100" title="Datos del taller"><i data-lucide="settings-2" class="h-4 w-4"></i></button></div>
    </div>
  </aside>
  <div class="main-shell ml-0 min-h-screen lg:ml-[248px]">
    <header class="topbar sticky top-0 z-20 flex h-[62px] items-center justify-between border-b border-gray-200 bg-white/95 px-4 backdrop-blur sm:px-7">
      <div class="flex items-center gap-3"><button id="mobile-menu" class="rounded p-2 text-gray-500 hover:bg-gray-100 lg:hidden"><i data-lucide="menu" class="h-5 w-5"></i></button><div class="text-[11px] text-gray-400">Taller / <span id="breadcrumb" class="font-semibold text-gray-700">Resumen general</span></div></div>
      <div class="flex items-center gap-2"><span class="hidden text-[10px] text-gray-400 sm:inline">Jue, 22 Oct 2026</span><span class="h-1 w-1 rounded-full bg-gray-300"></span><span id="sync-state" class="flex items-center gap-1.5 text-[10px] font-semibold text-gray-500"><span class="h-1.5 w-1.5 rounded-full bg-gray-400"></span>Guardado en este equipo</span></div>
    </header>
    <main class="main-content mx-auto max-w-[1600px] p-5 sm:p-7">
      <section id="page-dashboard" class="page active">
        <div class="mb-5 flex flex-wrap items-end justify-between gap-3"><div><p class="mb-1 text-[10px] font-bold uppercase tracking-[.12em] text-bajaj-red">Jornada de servicio · Cusco</p><h1 class="text-[23px] font-bold tracking-[-.02em]">Resumen general</h1><p class="mt-1 text-xs text-gray-500">Actividad técnica y financiera del taller</p></div><button class="btn btn-primary" onclick="navigate('order')"><i data-lucide="plus"></i>Nueva orden</button></div>
        <div class="mb-5 grid grid-cols-2 gap-3 xl:grid-cols-4">
          <article class="surface metric p-4"><div class="flex items-center justify-between"><span class="text-[10px] font-bold uppercase tracking-wide text-gray-500">Facturación del mes</span><span class="rounded bg-red-50 p-1.5 text-bajaj-red"><i data-lucide="banknote" class="h-4 w-4"></i></span></div><div id="metric-sales" class="mono mt-3 text-[21px] font-semibold">S/ 0.00</div><div class="mt-1 text-[10px] text-gray-400">Órdenes registradas en el periodo</div></article>
          <article class="surface metric p-4"><div class="flex items-center justify-between"><span class="text-[10px] font-bold uppercase tracking-wide text-gray-500">Recaudado</span><span class="rounded bg-green-50 p-1.5 text-bajaj-green"><i data-lucide="circle-dollar-sign" class="h-4 w-4"></i></span></div><div id="metric-collected" class="mono mt-3 text-[21px] font-semibold">S/ 0.00</div><div class="mt-1 text-[10px] text-gray-400">Efectivo, Yape, Plin y transferencias</div></article>
          <article class="surface metric p-4"><div class="flex items-center justify-between"><span class="text-[10px] font-bold uppercase tracking-wide text-gray-500">Por cobrar</span><span class="rounded bg-amber-50 p-1.5 text-amber-700"><i data-lucide="clock-3" class="h-4 w-4"></i></span></div><div id="metric-pending" class="mono mt-3 text-[21px] font-semibold">S/ 0.00</div><div class="mt-1 text-[10px] text-gray-400">Saldos pendientes de pago</div></article>
          <article class="surface metric p-4"><div class="flex items-center justify-between"><span class="text-[10px] font-bold uppercase tracking-wide text-gray-500">Ticket promedio</span><span class="rounded bg-blue-50 p-1.5 text-blue-700"><i data-lucide="receipt" class="h-4 w-4"></i></span></div><div id="metric-average" class="mono mt-3 text-[21px] font-semibold">S/ 0.00</div><div id="metric-open" class="mt-1 text-[10px] text-gray-400">0 órdenes activas</div></article>
        </div>
        <div class="mb-5 grid gap-4 xl:grid-cols-[1.6fr_1fr]">
          <article class="surface min-w-0 p-4 sm:p-5"><div class="mb-4 flex items-center justify-between"><div><h2 class="text-[13px] font-bold">Facturación diaria</h2><p class="mt-1 text-[10px] text-gray-400">Ingresos por fecha de emisión · S/</p></div><span class="rounded border border-gray-200 px-2 py-1 text-[10px] text-gray-500">Últimos 7 días</span></div><div class="h-[225px]"><canvas id="revenue-chart"></canvas></div></article>
          <article class="surface min-w-0 p-4 sm:p-5"><div class="mb-4"><h2 class="text-[13px] font-bold">Top modelos atendidos</h2><p class="mt-1 text-[10px] text-gray-400">Distribución de órdenes registradas</p></div><div class="h-[225px]"><canvas id="models-chart"></canvas></div></article>
        </div>
        <div class="grid gap-4 xl:grid-cols-[1fr_1.15fr]">
          <article class="surface p-4 sm:p-5"><div class="mb-3 flex items-center justify-between"><div><h2 class="text-[13px] font-bold">Composición de ventas</h2><p class="mt-1 text-[10px] text-gray-400">Mano de obra frente a repuestos</p></div><i data-lucide="chart-pie" class="h-4 w-4 text-gray-400"></i></div><div class="flex items-center gap-6"><div class="h-[145px] w-[145px] shrink-0"><canvas id="mix-chart"></canvas></div><div id="mix-legend" class="space-y-3 text-xs"></div></div></article>
          <article class="surface overflow-hidden"><div class="flex items-center justify-between border-b border-gray-100 px-4 py-3 sm:px-5"><div><h2 class="text-[13px] font-bold">Órdenes recientes</h2><p class="mt-1 text-[10px] text-gray-400">Últimos movimientos del taller</p></div><button class="btn btn-quiet" onclick="navigate('orders')">Ver todas <i data-lucide="arrow-right"></i></button></div><div id="recent-orders" class="divide-y divide-gray-100"></div></article>
        </div>
      </section>
      <section id="page-orders" class="page">
        <div class="mb-5 flex flex-wrap items-end justify-between gap-3"><div><p class="mb-1 text-[10px] font-bold uppercase tracking-[.12em] text-bajaj-red">Control de servicio</p><h1 class="text-[23px] font-bold">Órdenes de trabajo</h1><p class="mt-1 text-xs text-gray-500">Libro técnico y financiero del taller</p></div><button class="btn btn-primary" onclick="navigate('order')"><i data-lucide="plus"></i>Nueva OT</button></div>
        <div class="surface overflow-hidden">
          <div class="flex flex-col gap-3 border-b border-gray-200 p-3 sm:flex-row sm:items-center sm:p-4">
            <div class="relative min-w-0 flex-1 sm:max-w-[380px]"><i data-lucide="search" class="absolute left-3 top-1/2 h-4 w-4 -translate-y-1/2 text-gray-400"></i><input id="table-search" class="field pl-9" placeholder="Buscar placa, DNI, cliente o modelo..."></div>
            <div class="flex flex-wrap gap-2"><select id="status-filter" class="field !w-auto min-w-[135px]"><option value="">Todos los estados</option><option>Pendiente</option><option>En proceso</option><option>Listo</option><option>Entregado</option></select><select id="period-filter" class="field !w-auto min-w-[130px]"><option value="">Todo periodo</option><option value="month">Este mes</option><option value="30">Últimos 30 días</option><option value="90">Últimos 90 días</option></select><button id="table-export" class="btn" title="Exportar a Excel"><i data-lucide="download"></i><span class="hidden sm:inline">Excel</span></button><button id="table-csv" class="btn" title="Exportar CSV"><i data-lucide="file-spreadsheet"></i><span class="hidden sm:inline">CSV</span></button></div>
          </div>
          <div class="max-h-[calc(100vh-340px)] overflow-auto"><table class="grid-table"><thead><tr><th><button data-sort="id">N° OT ↕</button></th><th><button data-sort="date">Fecha ↕</button></th><th><button data-sort="plate">Placa ↕</button></th><th><button data-sort="client">Cliente ↕</button></th><th><button data-sort="model">Modelo ↕</button></th><th><button data-sort="status">Estado ↕</button></th><th><button data-sort="total">Total ↕</button></th><th><button data-sort="advance">Adelanto ↕</button></th><th><button data-sort="balance">Saldo ↕</button></th><th>Pago</th></tr></thead><tbody id="orders-body"></tbody></table></div>
          <div class="flex flex-wrap items-center justify-between gap-3 border-t border-gray-200 bg-gray-50 px-4 py-3"><span id="result-count" class="text-[10px] text-gray-500">0 registros</span><div class="flex flex-wrap gap-5 text-[10px]"><div><span class="text-gray-500">Facturación</span> <strong id="table-total" class="mono ml-1">S/ 0.00</strong></div><div><span class="text-gray-500">Adelantos</span> <strong id="table-advance" class="mono ml-1">S/ 0.00</strong></div><div><span class="text-gray-500">Por cobrar</span> <strong id="table-balance" class="mono ml-1 text-bajaj-red">S/ 0.00</strong></div></div></div>
        </div>
        <p class="mt-2 text-[10px] text-gray-400">Doble clic en cliente, placa o estado para editar. Usa Shift + clic en encabezados para ordenar por más de una columna.</p>
      </section>
      <section id="page-order" class="page">
        <div class="no-print mb-5 flex flex-wrap items-end justify-between gap-3"><div><p class="mb-1 text-[10px] font-bold uppercase tracking-[.12em] text-bajaj-red">Formato técnico oficial</p><h1 id="order-title" class="text-[23px] font-bold">Nueva orden de trabajo</h1><p class="mt-1 text-xs text-gray-500">RUDOLF MOTOS · Servicio Técnico Autorizado BAJAJ / Crosland Perú</p></div><div class="flex gap-2"><button id="order-cancel" class="btn" onclick="navigate('orders')">Cancelar</button><button id="print-order" class="btn"><i data-lucide="printer"></i>Imprimir / PDF</button><button id="save-order" class="btn btn-primary"><i data-lucide="save"></i>Guardar orden</button></div></div>
        <form id="order-form" class="space-y-4" autocomplete="off">
          <input type="hidden" id="order-edit-id">
          <section class="surface p-4 sm:p-5">
            <div class="mb-4 flex flex-wrap items-center justify-between gap-3 border-b border-gray-100 pb-3"><div><div class="text-[14px] font-bold">RUDOLF <span class="text-bajaj-red">MOTOS</span></div><div class="text-[9px] font-semibold uppercase tracking-[.13em] text-gray-400">Servicio Técnico Autorizado BAJAJ / Crosland Perú</div></div><div class="grid grid-cols-2 gap-3 text-right"><div><label class="field-label">Orden técnica</label><div id="order-number" class="mono text-[14px] font-semibold text-bajaj-red">RM-0001</div></div><div><label class="field-label">Estado</label><select id="f-status" class="field !w-[140px]"><option>Pendiente</option><option>En proceso</option><option>Listo</option><option>Entregado</option></select></div></div></div>
            <div class="mb-4 grid grid-cols-2 gap-3 md:grid-cols-4"><label><span class="field-label">Fecha y hora de ingreso</span><input id="f-date" type="datetime-local" class="field" required></label><label><span class="field-label">Entrega estimada</span><input id="f-due" type="datetime-local" class="field"></label><label><span class="field-label">Asesor / mecánico</span><input id="f-mechanic" class="field" value="Rodolfo Alfaro Huaman Huillca"></label><label><span class="field-label">Forma de pago</span><select id="f-payment" class="field"><option>Efectivo</option><option>Yape</option><option>Plin</option><option>Transferencia</option><option>Mixto</option><option>Pendiente</option></select></label></div>
            <div class="mb-2 flex items-center justify-between"><h2 class="text-[11px] font-bold uppercase tracking-wide text-gray-700">Datos del cliente</h2><button type="button" id="ocr-toggle" class="btn !min-h-[30px] !px-2.5"><i data-lucide="scan-text"></i>Escanear DNI / tarjeta</button></div>
            <div class="grid grid-cols-1 gap-3 sm:grid-cols-2 lg:grid-cols-4"><label><span class="field-label">DNI / documento</span><input id="f-dni" class="field" inputmode="numeric" maxlength="12" placeholder="8 dígitos"></label><label><span class="field-label">Nombre completo</span><input id="f-client" class="field" required placeholder="Nombre y apellidos"></label><label><span class="field-label">Teléfono / WhatsApp</span><input id="f-phone" class="field" inputmode="tel" placeholder="9 dígitos"></label><label><span class="field-label">Dirección</span><input id="f-address" class="field" placeholder="Dirección del cliente"></label></div>
            <div id="ocr-panel" class="drop-zone mt-4 hidden p-4">
              <div class="flex flex-col gap-3 sm:flex-row sm:items-center sm:justify-between"><div><div class="text-xs font-bold">Lectura de documento</div><p class="mt-1 text-[10px] text-gray-500">Sube una foto del DNI o tarjeta SUNARP; los datos detectados se copiarán al formulario.</p></div><div class="flex gap-2"><label class="btn"><i data-lucide="camera"></i>Cámara<input id="ocr-camera" type="file" accept="image/*" capture="environment" class="hidden"></label><label class="btn"><i data-lucide="image-up"></i>Elegir imagen<input id="ocr-file" type="file" accept="image/*" class="hidden"></label></div></div><div id="ocr-progress" class="mt-3 hidden text-[10px] text-gray-500"></div><div id="ocr-drop" class="mt-3 rounded border border-dashed border-gray-200 py-3 text-center text-[10px] text-gray-400">O arrastra una imagen aquí</div>
            </div>
          </section>
          <section class="surface p-4 sm:p-5"><div class="mb-3 flex items-center justify-between"><div><h2 class="text-[11px] font-bold uppercase tracking-wide text-gray-700">Datos del vehículo</h2></div><span class="text-[9px] font-semibold uppercase tracking-wider text-gray-400">Gama oficial Bajaj</span></div>
            <div class="grid grid-cols-2 gap-3 sm:grid-cols-3 lg:grid-cols-6"><label><span class="field-label">Placa</span><input id="f-plate" class="field mono" required placeholder="5421-8B"></label><label class="col-span-1 sm:col-span-2"><span class="field-label">Modelo</span><select id="f-model" class="field"><option>Pulsar NS200</option><option>Pulsar N160</option><option>Pulsar NS125</option><option>Dominar 400</option><option>Dominar 250</option><option>Boxer 150</option><option>Avenger 220</option><option>Torito</option></select></label><label><span class="field-label">Color</span><input id="f-color" class="field" placeholder="Color"></label><label><span class="field-label">Kilometraje</span><input id="f-km" class="field" type="number" min="0" placeholder="km"></label><label><span class="field-label">N° de motor</span><input id="f-engine" class="field" placeholder="Motor"></label><label class="sm:col-span-2"><span class="field-label">Chasis / VIN</span><input id="f-vin" class="field" placeholder="Número de chasis"></label><label class="sm:col-span-2"><span class="field-label">Motivo / solicitud del cliente</span><input id="f-complaint" class="field" placeholder="Describe el motivo de ingreso"></label><label class="sm:col-span-2"><span class="field-label">Observaciones de recepción</span><input id="f-observations" class="field" placeholder="Estado visible, accesorios, nivel de combustible..."></label></div>
          </section>
          <section class="surface p-4 sm:p-5"><div class="mb-1"><h2 class="text-[11px] font-bold uppercase tracking-wide text-gray-700">Inspección por sistemas mecánicos</h2><p class="mt-1 text-[10px] text-gray-400">Registrar condición y diagnóstico de cada sistema</p></div><div id="systems-list"></div></section>
          <section class="surface p-4 sm:p-5"><div class="mb-3 flex items-center justify-between"><div><h2 class="text-[11px] font-bold uppercase tracking-wide text-gray-700">Repuestos e insumos Crosland</h2><p class="mt-1 text-[10px] text-gray-400">Detalle de materiales empleados en la reparación</p></div><button type="button" id="add-part" class="btn !min-h-[31px]"><i data-lucide="plus"></i>Añadir ítem</button></div><div class="overflow-x-auto"><table class="w-full min-w-[650px] text-left"><thead><tr class="border-b border-gray-200 text-[9px] uppercase tracking-wide text-gray-400"><th class="pb-2 pr-2">Cant.</th><th class="pb-2 pr-2">Descripción</th><th class="pb-2 pr-2">Código SKU</th><th class="pb-2 pr-2">Precio unitario</th><th class="pb-2 pr-2 text-right">Subtotal</th><th></th></tr></thead><tbody id="parts-body"></tbody></table></div></section>
          <section class="grid gap-4 lg:grid-cols-[1.2fr_.8fr]"><article class="surface p-4 sm:p-5"><h2 class="mb-3 text-[11px] font-bold uppercase tracking-wide text-gray-700">Firmas de conformidad</h2><div class="grid gap-4 sm:grid-cols-2"><div><div class="mb-1 flex items-center justify-between"><span class="text-[10px] font-semibold text-gray-500">Firma del cliente</span><button type="button" class="clear-sign text-[10px] text-bajaj-red" data-canvas="client-sign">Limpiar</button></div><canvas id="client-sign" class="sig-canvas"></canvas></div><div><div class="mb-1 flex items-center justify-between"><span class="text-[10px] font-semibold text-gray-500">Firma del mecánico</span><button type="button" class="clear-sign text-[10px] text-bajaj-red" data-canvas="mechanic-sign">Limpiar</button></div><canvas id="mechanic-sign" class="sig-canvas"></canvas></div></div><p class="mt-2 text-[9px] text-gray-400">La firma se conserva en el registro de esta orden.</p></article>
            <article class="surface p-4 sm:p-5"><h2 class="mb-3 text-[11px] font-bold uppercase tracking-wide text-gray-700">Liquidación financiera</h2><div class="space-y-2.5"><div class="flex items-center justify-between text-xs"><span class="text-gray-500">Mano de obra</span><div class="flex items-center gap-1"><span class="text-gray-400">S/</span><input id="f-labor" class="field mono !w-28 !py-1.5 text-right" type="number" min="0" step="0.01" value="0"></div></div><div class="flex items-center justify-between text-xs"><span class="text-gray-500">Repuestos e insumos</span><span id="parts-total" class="mono">S/ 0.00</span></div><div class="flex items-center justify-between text-xs"><span class="text-gray-500">Servicios terceros</span><div class="flex items-center gap-1"><span class="text-gray-400">S/</span><input id="f-thirdparty" class="field mono !w-28 !py-1.5 text-right" type="number" min="0" step="0.01" value="0"></div></div><div class="border-t border-gray-200 pt-2.5"><div class="flex items-center justify-between text-[13px] font-bold"><span>TOTAL GENERAL</span><span id="grand-total" class="mono text-bajaj-red">S/ 0.00</span></div></div><div class="flex items-center justify-between text-xs"><span class="text-gray-500">Adelanto abonado</span><div class="flex items-center gap-1"><span class="text-gray-400">S/</span><input id="f-advance" class="field mono !w-28 !py-1.5 text-right" type="number" min="0" step="0.01" value="0"></div></div><div class="flex items-center justify-between rounded bg-gray-50 px-2.5 py-2 text-xs font-bold"><span>Saldo pendiente</span><span id="balance-total" class="mono">S/ 0.00</span></div></div><button type="button" id="whatsapp-order" class="btn mt-4 w-full border-[#cde7d8] bg-[#f0faf4] text-[#23724c] hover:bg-[#e7f6ed]"><i data-lucide="message-circle"></i>Enviar resumen por WhatsApp</button></article>
          </section>
          <div class="no-print flex justify-end gap-2 pb-4"><button class="btn" type="button" onclick="navigate('orders')">Cancelar</button><button class="btn btn-primary" type="button" id="save-order-bottom"><i data-lucide="save"></i>Guardar orden técnica</button></div>
        </form>
      </section>
      <section id="page-crm" class="page">
        <div class="mb-5 flex flex-wrap items-end justify-between gap-3"><div><p class="mb-1 text-[10px] font-bold uppercase tracking-[.12em] text-bajaj-red">CRM de mantenimiento</p><h1 class="text-[23px] font-bold">Clientes y fidelización</h1><p class="mt-1 text-xs text-gray-500">Seguimiento por kilometraje para mantener cada Bajaj en ruta</p></div><button id="crm-export" class="btn"><i data-lucide="download"></i>Exportar clientes</button></div>
        <div class="mb-4 grid grid-cols-2 gap-3 lg:grid-cols-3"><article class="surface p-4"><div class="text-[10px] font-bold uppercase tracking-wide text-gray-500">Clientes registrados</div><div id="crm-clients" class="mono mt-2 text-[22px] font-semibold">0</div></article><article class="surface p-4"><div class="text-[10px] font-bold uppercase tracking-wide text-gray-500">Mantenimiento vencido</div><div id="crm-overdue" class="mono mt-2 text-[22px] font-semibold text-bajaj-red">0</div></article><article class="surface col-span-2 p-4 lg:col-span-1"><div class="text-[10px] font-bold uppercase tracking-wide text-gray-500">Próximo servicio recomendado</div><div class="mt-2 text-[13px] font-semibold">Cada 5,000 km · inspección completa</div></article></div>
        <article class="surface overflow-hidden"><div class="flex flex-wrap items-center justify-between gap-2 border-b border-gray-100 px-4 py-3"><div><h2 class="text-[13px] font-bold">Agenda de mantenimiento</h2><p class="mt-1 text-[10px] text-gray-400">Recordatorios generados desde el kilometraje registrado en la última OT</p></div><div class="relative"><i data-lucide="search" class="absolute left-2.5 top-1/2 h-3.5 w-3.5 -translate-y-1/2 text-gray-400"></i><input id="crm-search" class="field !w-[210px] !py-2 !pl-8" placeholder="Buscar cliente o placa"></div></div><div class="overflow-x-auto"><table class="w-full min-w-[760px] text-left"><thead class="bg-gray-50 text-[9px] uppercase tracking-wide text-gray-400"><tr><th class="px-4 py-3">Cliente</th><th class="px-4 py-3">Vehículo</th><th class="px-4 py-3">Último registro</th><th class="px-4 py-3">Kilometraje</th><th class="px-4 py-3">Próximo servicio</th><th class="px-4 py-3">Estado</th><th class="px-4 py-3">Acción</th></tr></thead><tbody id="crm-body"></tbody></table></div></article>
      </section>
    </main>
    <footer class="mx-auto flex max-w-[1600px] flex-wrap justify-between gap-2 px-5 pb-5 text-[9px] text-gray-400 sm:px-7"><span>RUDOLF MOTOS · RUC 10486946003 · Representante: Rodolfo Alfaro Huaman Huillca</span><a class="hover:text-bajaj-red" href="https://wa.me/51913223471" target="_blank" rel="noopener">WhatsApp oficial 913 223 471</a></footer>
  </div>
  <input id="import-file" type="file" accept=".xlsx,.xls,.csv" class="hidden">
  <div id="toast-root"></div>
  <script>
    (() => {
      'use strict';
      const STORAGE_KEY = 'rudolf-motos-workshop-v1';
        const FIREBASE_KEY = 'rudolf-motos-firebase-config-v1';
      const SYSTEMS = [
        ['Transmisión', 'Cadena, piñón, embrague'],
        ['Frenos', 'Pastillas, discos, caliper, líquido DOT4, ABS'],
        ['Suspensión', 'Barras telescópicas, monoshock Nitrox'],
        ['Chasis', 'Cunas de dirección, basculante, aros'],
        ['Motor', 'Afinamiento, válvulas DTS-i, aceite Motul'],
        ['Eléctrico', 'Batería, bujías, alternador, EFI, tablero'],
        ['Servicios terceros', 'Torno, rectificado, pintura al horno']
      ];
      const SEED = [
        {id:'RM-0048',date:'2026-10-22T08:15',due:'2026-10-22T17:30',client:'Diego Quispe Ccama',dni:'74821635',phone:'984562137',address:'Wanchaq, Cusco',plate:'5421-8B',model:'Pulsar NS200',color:'Negro',km:15000,engine:'JX15E-827311',vin:'MD2A36FZ9PWC12842',complaint:'Mantenimiento preventivo y revisión de frenos',observations:'Sin observaciones',status:'En proceso',mechanic:'Rodolfo Alfaro Huaman Huillca',payment:'Yape',labor:180,thirdparty:0,advance:100,parts:[{qty:1,description:'Aceite Motul 5100 20W50',sku:'CRO-5100',price:75},{qty:1,description:'Filtro de aceite',sku:'BAJ-FILT-02',price:28}],systems:{'Motor':'Mantenimiento 15,000 km','Frenos':'Revisar pastillas delanteras'},signatures:{}},
        {id:'RM-0047',date:'2026-10-21T09:40',due:'2026-10-22T15:00',client:'María Elena Sucso',dni:'47182906',phone:'956714280',address:'San Sebastián, Cusco',plate:'6210-KA',model:'Dominar 400',color:'Verde',km:10000,engine:'D40E-112980',vin:'MD2A66FZ8SW041706',complaint:'Ruido en tren delantero',observations:'Entrega de 2 llaves',status:'Listo',mechanic:'Rodolfo Alfaro Huaman Huillca',payment:'Efectivo',labor:260,thirdparty:40,advance:150,parts:[{qty:1,description:'Pastillas de freno delanteras',sku:'CRO-D400-F',price:110}],systems:{'Frenos':'Pastillas con desgaste medio','Suspensión':'Inspección de barras'},signatures:{}},
        {id:'RM-0046',date:'2026-10-20T10:20',due:'2026-10-23T12:00',client:'José Luis Huamán',dni:'70234819',phone:'963120847',address:'Santiago, Cusco',plate:'8932-4C',model:'Boxer 150',color:'Rojo',km:5000,engine:'B15D-996028',vin:'MD2A18AZ0TWA09553',complaint:'Servicio de 5,000 km',observations:'',status:'Pendiente',mechanic:'Rodolfo Alfaro Huaman Huillca',payment:'Pendiente',labor:120,thirdparty:0,advance:0,parts:[{qty:1,description:'Aceite Bajaj DTS-i',sku:'CRO-BAJ-20W',price:48}],systems:{'Motor':'Servicio de 5,000 km'},signatures:{}},
        {id:'RM-0045',date:'2026-10-18T14:05',due:'2026-10-19T16:00',client:'Ana Lucía Paredes',dni:'45819362',phone:'947208516',address:'Cusco',plate:'7346-LB',model:'Pulsar N160',color:'Azul',km:9500,engine:'N16E-510872',vin:'MD2A34FZ5RWB22103',complaint:'Cambio de kit de arrastre',observations:'',status:'Entregado',mechanic:'Rodolfo Alfaro Huaman Huillca',payment:'Transferencia',labor:140,thirdparty:0,advance:260,parts:[{qty:1,description:'Kit de arrastre N160',sku:'CRO-N160-KIT',price:120}],systems:{'Transmisión':'Kit reemplazado'},signatures:{}},
        {id:'RM-0044',date:'2026-10-16T08:30',due:'2026-10-17T17:00',client:'Carlos Mamani Rojas',dni:'61940728',phone:'978342105',address:'Poroy, Cusco',plate:'1058-7D',model:'Dominar 250',color:'Gris',km:20000,engine:'D25E-771290',vin:'MD2A62FZ3RWC81924',complaint:'Afinamiento general',observations:'',status:'Entregado',mechanic:'Rodolfo Alfaro Huaman Huillca',payment:'Yape',labor:220,thirdparty:0,advance:100,parts:[{qty:2,description:'Bujía NGK',sku:'CRO-NGK-IR',price:42},{qty:1,description:'Filtro de aire',sku:'BAJ-AIR-250',price:58}],systems:{'Motor':'Calibración de válvulas DTS-i'},signatures:{}},
        {id:'RM-0043',date:'2026-10-12T11:00',due:'2026-10-12T18:00',client:'Rosa Huillca Cárdenas',dni:'48376120',phone:'921564083',address:'San Jerónimo, Cusco',plate:'4280-PA',model:'Avenger 220',color:'Negro',km:10000,engine:'A22E-104830',vin:'MD2A21AZ2SW190681',complaint:'Revisión eléctrica',observations:'',status:'Entregado',mechanic:'Rodolfo Alfaro Huaman Huillca',payment:'Plin',labor:160,thirdparty:0,advance:160,parts:[{qty:1,description:'Batería 12V',sku:'CRO-BAT-12',price:135}],systems:{'Eléctrico':'Batería reemplazada'},signatures:{}},
        {id:'RM-0042',date:'2026-10-10T09:15',due:'2026-10-11T14:00',client:'Edgar Cárdenas Huanca',dni:'71028364',phone:'987654123',address:'Cusco',plate:'9901-8K',model:'Pulsar NS125',color:'Rojo',km:5000,engine:'N12E-602418',vin:'MD2A32FZ2SW020149',complaint:'Cambio de aceite y filtro',observations:'',status:'Entregado',mechanic:'Rodolfo Alfaro Huaman Huillca',payment:'Efectivo',labor:90,thirdparty:0,advance:75,parts:[{qty:1,description:'Aceite Motul 3000',sku:'CRO-MOT-3000',price:48}],systems:{'Motor':'Aceite y filtro reemplazados'},signatures:{}},
        {id:'RM-0041',date:'2026-10-08T13:10',due:'2026-10-10T12:00',client:'Víctor Sumaq Huillca',dni:'76509318',phone:'934851620',address:'Santiago, Cusco',plate:'2871-TR',model:'Torito',color:'Azul',km:10000,engine:'T10E-302781',vin:'MD2T10AZ9SW058321',complaint:'Mantenimiento general',observations:'',status:'Entregado',mechanic:'Rodolfo Alfaro Huaman Huillca',payment:'Transferencia',labor:300,thirdparty:90,advance:200,parts:[{qty:1,description:'Filtro de combustible',sku:'CRO-TOR-FC',price:65}],systems:{'Motor':'Afinamiento','Servicios terceros':'Rectificado de campana'},signatures:{}}
      ];
      const $ = (selector, root=document) => root.querySelector(selector);
      const $$ = (selector, root=document) => [...root.querySelectorAll(selector)];
      const money = value => `S/ ${Number(value || 0).toLocaleString('es-PE',{minimumFractionDigits:2,maximumFractionDigits:2})}`;
      const escapeHtml = value => String(value ?? '').replace(/[&<>"']/g, char => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[char]));
      const totalOf = order => Number(order.labor||0)+Number(order.thirdparty||0)+(order.parts||[]).reduce((sum,part)=>sum+Number(part.qty||0)*Number(part.price||0),0);
      const balanceOf = order => Math.max(0,totalOf(order)-Number(order.advance||0));
      const readStore = () => { try { const parsed=JSON.parse(localStorage.getItem(STORAGE_KEY)); return Array.isArray(parsed)&&parsed.length ? parsed : SEED; } catch { return SEED; } };
      let orders = readStore();
      const hasLocalData = Boolean(localStorage.getItem(STORAGE_KEY));
      let sortKeys = [{key:'date',direction:-1}];
      let revenueChart, modelsChart, mixChart;
      let editing = false;
      let firestoreRef = null;
      let firestoreTimer = null;
      function setSyncState(label,color='gray') {
        const state=$('#sync-state');if(!state)return;
        const colors={gray:['text-gray-500','bg-gray-400'],green:['text-bajaj-green','bg-bajaj-green'],amber:['text-amber-700','bg-amber-500'],red:['text-bajaj-red','bg-bajaj-red']};
        const [textClass,dotClass]=colors[color]||colors.gray;state.className=`flex items-center gap-1.5 text-[10px] font-semibold ${textClass}`;state.innerHTML=`<span class="h-1.5 w-1.5 rounded-full ${dotClass}"></span>${escapeHtml(label)}`;
      }
      function persist() {
        localStorage.setItem(STORAGE_KEY,JSON.stringify(orders));
        if(firestoreRef){setSyncState('Guardando en Firestore','amber');clearTimeout(firestoreTimer);firestoreTimer=setTimeout(async()=>{try{await firestoreRef.set({orders,updatedAt:new Date().toISOString()});setSyncState('Sincronizado con Firestore','green');}catch(error){console.error(error);setSyncState('Error de sincronización','red');}},500);}
      }
      async function connectFirestore(config) {
        if(!window.firebase?.firestore)throw new Error('Firebase SDK no disponible; revisa tu conexión.');
        const app=window.firebase.apps.find(item=>item.name==='rudolf-workshop')||window.firebase.initializeApp(config,'rudolf-workshop');
        firestoreRef=app.firestore().collection('workshops').doc('rudolf-motos');
        const snapshot=await firestoreRef.get();
        if(snapshot.exists&&Array.isArray(snapshot.data().orders)&&!hasLocalData){orders=snapshot.data().orders;localStorage.setItem(STORAGE_KEY,JSON.stringify(orders));renderDashboard();renderOrders();renderCRM();}
        else if(!snapshot.exists||hasLocalData)await firestoreRef.set({orders,updatedAt:new Date().toISOString()});
        setSyncState('Sincronizado con Firestore','green');
      }
      async function configureFirestore() {
        const saved=localStorage.getItem(FIREBASE_KEY)||'';
        const value=window.prompt('Pega la configuración web de Firebase (JSON). Se usará Firestore en workshops/rudolf-motos.',saved);
        if(value===null)return;
        try{const config=JSON.parse(value);if(!config.apiKey||!config.projectId||!config.appId)throw new Error('Incluye apiKey, projectId y appId.');localStorage.setItem(FIREBASE_KEY,JSON.stringify(config));await connectFirestore(config);toast('Firestore conectado.');}
        catch(error){console.error(error);firestoreRef=null;setSyncState('Solo guardado local','gray');toast(`No se pudo conectar: ${error.message}`);}
      }
      function toast(message) { const node=document.createElement('div');node.className='toast';node.textContent=message;$('#toast-root').append(node);setTimeout(()=>node.remove(),2800); }
      function statusClass(status) { return ({'Pendiente':'status-pendiente','En proceso':'status-proceso','Listo':'status-listo','Entregado':'status-entregado'})[status]||'status-pendiente'; }
      function navigate(page) {
        $$('.page').forEach(section=>section.classList.toggle('active',section.id===`page-${page}`));
        $$('.nav-link[data-page]').forEach(link=>link.classList.toggle('active',link.dataset.page===page));
        const titles={dashboard:'Resumen general',orders:'Órdenes de trabajo',order:editing?'Editar orden técnica':'Nueva orden técnica',crm:'Clientes y fidelización'};
        $('#breadcrumb').textContent=titles[page]||'Resumen general';toggleSidebar(false);
        if(page==='dashboard') renderDashboard(); if(page==='orders') renderOrders(); if(page==='crm') renderCRM();
        if(page==='order') setTimeout(()=>$$('.sig-canvas').forEach(setupCanvas),40);
      }
      window.navigate=navigate;
      window.toggleSidebar = open => { $('#sidebar').classList.toggle('open',open);$('#mobile-shade').classList.toggle('open',open); };
      function renderDashboard() {
        const monthOrders=orders.filter(order=>String(order.date||'').slice(0,7)==='2026-10');
        const sales=monthOrders.reduce((sum,order)=>sum+totalOf(order),0),collected=monthOrders.reduce((sum,order)=>sum+Number(order.advance||0),0),pending=monthOrders.reduce((sum,order)=>sum+balanceOf(order),0);
        $('#metric-sales').textContent=money(sales);$('#metric-collected').textContent=money(collected);$('#metric-pending').textContent=money(pending);$('#metric-average').textContent=money(monthOrders.length?sales/monthOrders.length:0);
        $('#metric-open').textContent=`${orders.filter(order=>order.status!=='Entregado').length} órdenes activas`;
        $('#nav-order-count').textContent=orders.length;
        const recent=[...orders].sort((a,b)=>String(b.date).localeCompare(String(a.date))).slice(0,4);
        $('#recent-orders').innerHTML=recent.map(order=>`<button class="flex w-full items-center gap-3 px-4 py-3 text-left hover:bg-gray-50 sm:px-5" onclick="openExisting('${escapeHtml(order.id)}')"><span class="mono text-[10px] font-semibold text-bajaj-red">${escapeHtml(order.id)}</span><span class="min-w-0 flex-1"><span class="block truncate text-[11px] font-semibold">${escapeHtml(order.client)}</span><span class="mt-0.5 block text-[10px] text-gray-400">${escapeHtml(order.plate)} · ${escapeHtml(order.model)}</span></span><span class="text-right"><span class="mono block text-[11px] font-semibold">${money(totalOf(order))}</span><span class="${statusClass(order.status)} badge mt-1">${escapeHtml(order.status)}</span></span></button>`).join('')||'<div class="p-6 text-center text-xs text-gray-400">No hay órdenes registradas</div>';
        drawCharts();
      }
      function drawCharts() {
        if(!window.Chart)return;
        const days=Array.from({length:7},(_,index)=>{const date=new Date();date.setDate(date.getDate()-(6-index));return date;});
        const labels=days.map(date=>date.toLocaleDateString('es-PE',{day:'2-digit',month:'short'}));
        const values=days.map(date=>orders.filter(order=>String(order.date||'').slice(0,10)===date.toISOString().slice(0,10)).reduce((sum,order)=>sum+totalOf(order),0));
        const models=Object.entries(orders.reduce((counts,order)=>{counts[order.model]=(counts[order.model]||0)+1;return counts;},{})).sort((a,b)=>b[1]-a[1]).slice(0,5);
        const labor=orders.reduce((sum,order)=>sum+Number(order.labor||0),0),parts=orders.reduce((sum,order)=>sum+(order.parts||[]).reduce((sub,part)=>sub+Number(part.qty||0)*Number(part.price||0),0),0);
        const common={responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{x:{grid:{display:false},ticks:{color:'#92989d',font:{family:'DM Sans',size:9}}},y:{beginAtZero:true,grid:{color:'#eef0f1'},ticks:{color:'#92989d',font:{family:'IBM Plex Mono',size:9},callback:value=>`S/${value}`}}}};
        revenueChart?.destroy();modelsChart?.destroy();mixChart?.destroy();
        revenueChart=new Chart($('#revenue-chart'),{type:'line',data:{labels,datasets:[{data:values,borderColor:'#b51f2b',backgroundColor:'rgba(181,31,43,.08)',fill:true,tension:.35,pointRadius:3,pointBackgroundColor:'#b51f2b'}]},options:common});
        modelsChart=new Chart($('#models-chart'),{type:'bar',data:{labels:models.map(item=>item[0]),datasets:[{data:models.map(item=>item[1]),backgroundColor:['#b51f2b','#30363b','#80919c','#c8a36a','#75a28b'],borderRadius:3,barThickness:16}]},options:{...common,indexAxis:'y',scales:{x:{...common.scales.y,ticks:{...common.scales.y.ticks,stepSize:1}},y:{...common.scales.x}}}});
        mixChart=new Chart($('#mix-chart'),{type:'doughnut',data:{labels:['Mano de obra','Repuestos'],datasets:[{data:[labor,parts],backgroundColor:['#b51f2b','#28343c'],borderWidth:0,hoverOffset:3}]},options:{responsive:true,maintainAspectRatio:false,cutout:'72%',plugins:{legend:{display:false}}}});
        const sum=labor+parts;$('#mix-legend').innerHTML=`<div class="flex items-center gap-2"><i class="h-2 w-2 rounded-sm bg-bajaj-red"></i><span class="text-gray-500">Mano de obra</span><strong class="mono ml-auto">${sum?Math.round(labor/sum*100):0}%</strong></div><div class="flex items-center gap-2"><i class="h-2 w-2 rounded-sm bg-[#28343c]"></i><span class="text-gray-500">Repuestos</span><strong class="mono ml-auto">${sum?Math.round(parts/sum*100):0}%</strong></div>`;
      }
      function getVisibleOrders() {
        const query=$('#table-search').value.trim().toLowerCase(),status=$('#status-filter').value,period=$('#period-filter').value;
        const now=new Date();
        return orders.filter(order=>{
          const matches=[order.id,order.plate,order.dni,order.client,order.model].some(value=>String(value||'').toLowerCase().includes(query));
          const date=new Date(order.date);let inPeriod=true;
          if(period==='month')inPeriod=String(order.date||'').slice(0,7)===now.toISOString().slice(0,7);
          if(period==='30'||period==='90')inPeriod=(now-date)/86400000<=Number(period)&&(now-date)/86400000>=-1;
          return matches&&(!status||order.status===status)&&inPeriod;
        }).sort((a,b)=>{for(const {key,direction} of sortKeys){let left=key==='total'?totalOf(a):key==='balance'?balanceOf(a):a[key],right=key==='total'?totalOf(b):key==='balance'?balanceOf(b):b[key];if(typeof left==='string') {left=left?.toLowerCase()||'';right=right?.toLowerCase()||'';}if(left<right)return -1*direction;if(left>right)return direction;}return 0;});
      }
      function renderOrders() {
        const shown=getVisibleOrders();$('#orders-body').innerHTML=shown.map(order=>`<tr data-id="${escapeHtml(order.id)}"><td class="mono font-semibold text-bajaj-red">${escapeHtml(order.id)}</td><td class="mono text-[10px]">${escapeHtml(String(order.date||'').replace('T',' '))}</td><td data-edit="plate" class="mono font-semibold">${escapeHtml(order.plate)}</td><td data-edit="client">${escapeHtml(order.client)}</td><td>${escapeHtml(order.model)}</td><td data-edit="status"><span class="badge ${statusClass(order.status)}">${escapeHtml(order.status)}</span></td><td class="mono">${money(totalOf(order))}</td><td data-edit="advance" class="mono">${money(order.advance)}</td><td class="mono ${balanceOf(order)>0?'text-bajaj-red':''}">${money(balanceOf(order))}</td><td>${escapeHtml(order.payment||'')}</td></tr>`).join('')||'<tr><td colspan="10" class="py-12 text-center text-xs text-gray-400">No hay resultados para estos filtros</td></tr>';
        $('#result-count').textContent=`${shown.length} ${shown.length===1?'registro':'registros'}`;$('#table-total').textContent=money(shown.reduce((sum,order)=>sum+totalOf(order),0));$('#table-advance').textContent=money(shown.reduce((sum,order)=>sum+Number(order.advance||0),0));$('#table-balance').textContent=money(shown.reduce((sum,order)=>sum+balanceOf(order),0));
      }
      function makeId() { const highest=orders.reduce((max,order)=>Math.max(max,Number(String(order.id).replace('RM-',''))||0),0);return `RM-${String(highest+1).padStart(4,'0')}`; }
      function buildSystems() {
        $('#systems-list').innerHTML=SYSTEMS.map(([name,details])=>`<div class="system-row"><div><div class="text-[11px] font-bold">${name}</div><div class="mt-0.5 text-[9px] leading-4 text-gray-400">${details}</div></div><select class="field system-status" data-system="${name}"><option value="">Sin revisar</option><option>Conforme</option><option>Requiere atención</option><option>Reparado</option><option>No aplica</option></select><input class="field system-notes" data-note="${name}" placeholder="Hallazgo / trabajo realizado"></div>`).join('');
      }
      function addPart(part={}) {
        const row=document.createElement('tr');row.className='part-row border-b border-gray-100';row.innerHTML=`<td class="py-2 pr-2"><input class="field part-qty !w-[65px] !py-1.5" type="number" min="0" step="1" value="${Number(part.qty||1)}"></td><td class="py-2 pr-2"><input class="field part-desc !py-1.5" value="${escapeHtml(part.description||'')}" placeholder="Descripción"></td><td class="py-2 pr-2"><input class="field part-sku !w-[125px] !py-1.5" value="${escapeHtml(part.sku||'')}" placeholder="SKU"></td><td class="py-2 pr-2"><input class="field part-price !w-[110px] !py-1.5" type="number" min="0" step="0.01" value="${Number(part.price||0)}"></td><td class="part-subtotal mono py-2 pr-2 text-right text-xs">S/ 0.00</td><td class="py-2 text-right"><button type="button" class="remove-part rounded p-1.5 text-gray-400 hover:bg-red-50 hover:text-bajaj-red" title="Eliminar ítem"><i data-lucide="trash-2" class="h-4 w-4"></i></button></td>`;$('#parts-body').append(row);lucide.createIcons();updateTotals();
      }
      function updateTotals() {
        let partsTotal=0;$$('.part-row').forEach(row=>{const subtotal=Number($('.part-qty',row).value||0)*Number($('.part-price',row).value||0);partsTotal+=subtotal;$('.part-subtotal',row).textContent=money(subtotal);});
        const grand=partsTotal+Number($('#f-labor').value||0)+Number($('#f-thirdparty').value||0),balance=Math.max(0,grand-Number($('#f-advance').value||0));
        $('#parts-total').textContent=money(partsTotal);$('#grand-total').textContent=money(grand);$('#balance-total').textContent=money(balance);
      }
      function resetForm() {
        editing=false;$('#order-form').reset();$('#order-edit-id').value='';$('#order-title').textContent='Nueva orden de trabajo';$('#order-number').textContent=makeId();$('#f-date').value=new Date().toISOString().slice(0,16);$('#f-due').value='';$('#f-mechanic').value='Rodolfo Alfaro Huaman Huillca';$('#f-labor').value=0;$('#f-thirdparty').value=0;$('#f-advance').value=0;$('#parts-body').innerHTML='';addPart();buildSystems();$$('.sig-canvas').forEach(canvas=>{delete canvas.dataset.ready;setupCanvas(canvas);});$('#ocr-panel').classList.add('hidden');updateTotals();
      }
      function openExisting(id) {
        const order=orders.find(item=>item.id===id);if(!order)return;editing=true;resetForm();editing=true;$('#order-edit-id').value=id;$('#order-title').textContent=`Editar orden ${id}`;$('#order-number').textContent=id;
        const mapping={date:'date',due:'due',status:'status',mechanic:'mechanic',payment:'payment',dni:'dni',client:'client',phone:'phone',address:'address',plate:'plate',model:'model',color:'color',km:'km',engine:'engine',vin:'vin',complaint:'complaint',observations:'observations',labor:'labor',thirdparty:'thirdparty',advance:'advance'};
        for(const [field,key] of Object.entries(mapping)){const element=$(`#f-${field}`);if(element&&order[key]!==undefined)element.value=order[key];}
        $('#parts-body').innerHTML='';(order.parts||[]).forEach(addPart);buildSystems();Object.entries(order.systems||{}).forEach(([name,value])=>{const select=$(`[data-system="${CSS.escape(name)}"]`);if(select)select.value=['Conforme','Requiere atención','Reparado','No aplica'].includes(value)?value:'';const note=$(`[data-note="${CSS.escape(name)}"]`);if(note)note.value=value;});
        setTimeout(()=>{for(const [id,data] of Object.entries(order.signatures||{})){const canvas=$(`#${id}`);if(canvas&&data){const image=new Image();image.onload=()=>canvas.getContext('2d').drawImage(image,0,0,canvas.width,canvas.height);image.src=data;}}},60);navigate('order');
      }
      window.openExisting=openExisting;
      function setupCanvas(canvas) {
        if(!canvas)return;const rect=canvas.getBoundingClientRect();if(!rect.width)return;
        const ratio=window.devicePixelRatio||1;const existing=canvas.dataset.ready==='1';let snapshot='';if(existing)try{snapshot=canvas.toDataURL();}catch{}
        canvas.width=rect.width*ratio;canvas.height=rect.height*ratio;const ctx=canvas.getContext('2d');ctx.scale(ratio,ratio);ctx.lineWidth=2;ctx.lineCap='round';ctx.lineJoin='round';ctx.strokeStyle='#202327';
        if(snapshot){const img=new Image();img.onload=()=>ctx.drawImage(img,0,0,rect.width,rect.height);img.src=snapshot;}
        let drawing=false;const position=event=>{const bounds=canvas.getBoundingClientRect();return{x:event.clientX-bounds.left,y:event.clientY-bounds.top};};
        canvas.onpointerdown=event=>{drawing=true;canvas.setPointerCapture(event.pointerId);const point=position(event);ctx.beginPath();ctx.moveTo(point.x,point.y);};canvas.onpointermove=event=>{if(!drawing)return;const point=position(event);ctx.lineTo(point.x,point.y);ctx.stroke();};canvas.onpointerup=()=>{drawing=false;};canvas.onpointercancel=()=>{drawing=false;};canvas.dataset.ready='1';
      }
      function collectForm() {
        const systems={};$$('.system-status').forEach(select=>{const note=$(`[data-note="${CSS.escape(select.dataset.system)}"]`).value.trim();if(select.value||note)systems[select.dataset.system]=[select.value,note].filter(Boolean).join(' · ');});
        const parts=$$('.part-row').map(row=>({qty:Number($('.part-qty',row).value||0),description:$('.part-desc',row).value.trim(),sku:$('.part-sku',row).value.trim(),price:Number($('.part-price',row).value||0)})).filter(part=>part.description||part.price);
        const signatures={};$$('.sig-canvas').forEach(canvas=>{if(canvas.dataset.ready==='1')signatures[canvas.id]=canvas.toDataURL('image/png');});
        return {id:$('#order-edit-id').value||makeId(),date:$('#f-date').value,due:$('#f-due').value,status:$('#f-status').value,mechanic:$('#f-mechanic').value.trim(),payment:$('#f-payment').value,dni:$('#f-dni').value.trim(),client:$('#f-client').value.trim(),phone:$('#f-phone').value.trim(),address:$('#f-address').value.trim(),plate:$('#f-plate').value.trim().toUpperCase(),model:$('#f-model').value,color:$('#f-color').value.trim(),km:Number($('#f-km').value||0),engine:$('#f-engine').value.trim(),vin:$('#f-vin').value.trim(),complaint:$('#f-complaint').value.trim(),observations:$('#f-observations').value.trim(),systems,parts,labor:Number($('#f-labor').value||0),thirdparty:Number($('#f-thirdparty').value||0),advance:Number($('#f-advance').value||0),signatures};
      }
      function saveOrder() {
        if(!$('#order-form').reportValidity())return;const order=collectForm(),index=orders.findIndex(item=>item.id===order.id);if(index>=0)orders[index]=order;else orders.unshift(order);persist();toast(`Orden ${order.id} guardada correctamente`);editing=false;renderDashboard();renderOrders();navigate('orders');
      }
      function exportOrders(rows=orders,filename='rudolf-motos-ordenes.xlsx') {
        if(!window.XLSX){toast('No se pudo cargar el módulo Excel');return;}
        const data=rows.map(order=>({'N° OT':order.id,Fecha:order.date,Cliente:order.client,DNI:order.dni,Teléfono:order.phone,Dirección:order.address,Placa:order.plate,Modelo:order.model,Color:order.color,Kilometraje:order.km,'N° Motor':order.engine,VIN:order.vin,Estado:order.status,Pago:order.payment,'Mano de obra':Number(order.labor||0),Repuestos:(order.parts||[]).reduce((sum,p)=>sum+p.qty*p.price,0),'Servicios terceros':Number(order.thirdparty||0),Total:totalOf(order),Adelanto:Number(order.advance||0),Saldo:balanceOf(order)}));
        const sheet=XLSX.utils.json_to_sheet(data);sheet['!cols']=[{wch:12},{wch:18},{wch:28},{wch:12},{wch:14},{wch:25},{wch:12},{wch:18},{wch:12},{wch:14},{wch:18},{wch:24},{wch:14},{wch:15},{wch:15},{wch:14},{wch:17},{wch:14},{wch:14},{wch:14}];
        const range=XLSX.utils.decode_range(sheet['!ref']);sheet['!rows']=[{hpt:25}];
        for(let col=range.s.c;col<=range.e.c;col++){const cell=sheet[XLSX.utils.encode_cell({r:0,c:col})];if(cell)cell.s={font:{name:'Arial',sz:10,bold:true,color:{rgb:'FFFFFF'}},fill:{fgColor:{rgb:'B51F2B'}},alignment:{vertical:'center'}};}
        for(let row=1;row<=range.e.r;row++)for(let col=14;col<=19;col++){const cell=sheet[XLSX.utils.encode_cell({r:row,c:col})];if(cell){cell.z='"S/" #,##0.00';cell.s={alignment:{horizontal:'right'},font:{name:'Arial',sz:10,color:{rgb:'202327'}}};}}
        const book=XLSX.utils.book_new();XLSX.utils.book_append_sheet(book,sheet,'Órdenes');
        if(filename.endsWith('.csv')){const content='\uFEFF'+XLSX.utils.sheet_to_csv(sheet);const link=document.createElement('a');link.href=URL.createObjectURL(new Blob([content],{type:'text/csv;charset=utf-8'}));link.download=filename;link.click();URL.revokeObjectURL(link.href);return;}
        XLSX.writeFile(book,filename);
      }
      function importFile(file) {
        if(!file||!window.XLSX){toast('Archivo no disponible o módulo Excel sin conexión');return;}
        const reader=new FileReader();reader.onload=event=>{try{const workbook=XLSX.read(event.target.result,{type:'array'}),rows=XLSX.utils.sheet_to_json(workbook.Sheets[workbook.SheetNames[0]],{defval:''});let imported=0;for(const row of rows){const get=(...keys)=>{for(const key of keys)if(row[key]!==undefined)return row[key];return '';};const id=String(get('N° OT','id')||makeId());const current=orders.find(order=>order.id===id);const order={...(current||{}),id,date:String(get('Fecha','date')||new Date().toISOString().slice(0,16)),client:String(get('Cliente','client')),dni:String(get('DNI','dni')),phone:String(get('Teléfono','phone')),plate:String(get('Placa','plate')).toUpperCase(),model:String(get('Modelo','model')||'Pulsar NS200'),status:String(get('Estado','status')||'Pendiente'),payment:String(get('Pago','payment')||'Pendiente'),labor:Number(get('Mano de obra','labor')||0),thirdparty:Number(get('Servicios terceros','thirdparty')||0),advance:Number(get('Adelanto','advance')||0),km:Number(get('Kilometraje','km')||0),parts:current?.parts||[],systems:current?.systems||{},signatures:current?.signatures||{}};const index=orders.findIndex(item=>item.id===id);if(index>=0)orders[index]=order;else orders.unshift(order);imported++;}persist();renderDashboard();renderOrders();toast(`${imported} registros importados`);}catch(error){console.error(error);toast('No se pudo leer el archivo. Revisa sus columnas.');}};reader.readAsArrayBuffer(file);
      }
      function renderCRM() {
        const grouped=new Map();for(const order of [...orders].sort((a,b)=>String(b.date).localeCompare(String(a.date)))){const key=order.dni||order.phone||order.client;if(!grouped.has(key))grouped.set(key,order);}
        const records=[...grouped.values()].map(order=>{const interval=Number(order.km||0)<5000?5000:(Math.floor(Number(order.km||0)/5000)+1)*5000;return {...order,next:interval,overdue:Number(order.km||0)>=interval-1000};});
        $('#crm-clients').textContent=records.length;$('#crm-overdue').textContent=records.filter(item=>item.overdue).length;
        const query=$('#crm-search').value.trim().toLowerCase();const shown=records.filter(item=>[item.client,item.plate,item.model,item.phone].some(value=>String(value||'').toLowerCase().includes(query)));
        $('#crm-body').innerHTML=shown.map(item=>`<tr class="border-t border-gray-100"><td class="px-4 py-3"><div class="text-[11px] font-semibold">${escapeHtml(item.client)}</div><div class="mt-0.5 text-[10px] text-gray-400">${escapeHtml(item.phone||'Sin teléfono')} · DNI ${escapeHtml(item.dni||'—')}</div></td><td class="px-4 py-3"><div class="mono text-[10px] font-semibold">${escapeHtml(item.plate)}</div><div class="mt-0.5 text-[10px] text-gray-400">${escapeHtml(item.model)}</div></td><td class="px-4 py-3 text-[10px] text-gray-500">${escapeHtml(String(item.date||'').slice(0,10))}</td><td class="mono px-4 py-3 text-[11px]">${Number(item.km||0).toLocaleString('es-PE')} km</td><td class="mono px-4 py-3 text-[11px]">${item.next.toLocaleString('es-PE')} km</td><td class="px-4 py-3"><span class="badge ${item.overdue?'status-pendiente':'status-listo'}">${item.overdue?'Revisar pronto':'Al día'}</span></td><td class="px-4 py-3"><button class="btn !min-h-[30px] !px-2.5 !text-[10px]" onclick="sendReminder('${encodeURIComponent(item.phone||'')}','${encodeURIComponent(item.client)}','${encodeURIComponent(item.plate)}','${item.next}')"><i data-lucide="message-circle"></i>Recordar</button></td></tr>`).join('')||'<tr><td colspan="7" class="py-12 text-center text-xs text-gray-400">No hay clientes coincidentes</td></tr>';lucide.createIcons();
      }
      window.sendReminder=(phone,client,plate,next)=>{const decoded=decodeURIComponent(phone).replace(/\D/g,'');if(!decoded){toast('Este cliente no tiene teléfono registrado');return;}const text=`Hola ${decodeURIComponent(client)}, te saludamos de RUDOLF MOTOS. La ${decodeURIComponent(plate)} está próxima a su mantenimiento recomendado de ${Number(next).toLocaleString('es-PE')} km. Agenda tu servicio con nuestro equipo autorizado BAJAJ. ¡Te esperamos!`;window.open(`https://wa.me/51${decoded}?text=${encodeURIComponent(text)}`,'_blank','noopener');};
      function extractOCR(text) {
        const normalized=text.toUpperCase().replace(/[O]/g,'0');const plate=normalized.match(/\b(\d{4})[\s-]?([A-Z0-9]{2})\b/),dni=text.match(/\b\d{8}\b/);
        const model=/(NS\s?200|N\s?160|NS\s?125|DOMINAR\s?400|DOMINAR\s?250|BOXER\s?150|AVENGER\s?220|TORITO)/i.exec(text);
        const engine=/(?:MOTOR|ENGINE)[\s:#-]*([A-Z0-9-]{6,})/i.exec(text),vin=/(?:CHASIS|VIN|SERIE)[\s:#-]*([A-HJ-NPR-Z0-9]{10,17})/i.exec(text);
        if(plate)$('#f-plate').value=`${plate[1]}-${plate[2]}`;if(dni)$('#f-dni').value=dni[0];if(model){const target=$$('#f-model option').find(option=>option.textContent.replace(/\s/g,'').toLowerCase()===model[0].replace(/\s/g,'').toLowerCase());if(target)$('#f-model').value=target.value;}
        if(engine)$('#f-engine').value=engine[1];if(vin)$('#f-vin').value=vin[1];
        const holder=text.match(/(?:TITULAR|PROPIETARIO|APELLIDOS?\s+Y\s+NOMBRES?)[\s:]+([A-ZÁÉÍÓÚÑ ]{8,})/i);if(holder&&!$('#f-client').value)$('#f-client').value=holder[1].trim().split(/\n/)[0].slice(0,70);
        const recognized=Boolean(plate||dni||model||engine||vin||holder);toast(recognized?'Datos reconocidos; verifica la información':'No se reconocieron datos; prueba con una imagen más nítida');
      }
      async function runOCR(file) {
        if(!file)return;const progress=$('#ocr-progress');progress.classList.remove('hidden');progress.textContent='Preparando lectura OCR...';
        try{const result=await Tesseract.recognize(file,'spa',{logger:message=>{if(message.status==='recognizing text')progress.textContent=`Leyendo documento: ${Math.round(message.progress*100)}%`;}});extractOCR(result.data.text);}catch(error){console.error(error);toast('No fue posible leer la imagen. Verifica conexión y calidad.');}finally{progress.classList.add('hidden');}
      }
      function shareOrder() {const order=collectForm();if(!order.phone){toast('Ingresa el teléfono del cliente para compartir');$('#f-phone').focus();return;}const text=`*RUDOLF MOTOS · Orden ${order.id}*\nCliente: ${order.client}\nVehículo: ${order.model} · ${order.plate}\nIngreso: ${order.date.replace('T',' ')}\nEstado: ${order.status}\nTotal: ${money(totalOf(order))}\nAdelanto: ${money(order.advance)}\nSaldo pendiente: ${money(balanceOf(order))}\n\nServicio Técnico Autorizado BAJAJ · WhatsApp 913 223 471`;window.open(`https://wa.me/51${order.phone.replace(/\D/g,'')}?text=${encodeURIComponent(text)}`,'_blank','noopener');}
      function bindEvents() {
        $$('.nav-link[data-page]').forEach(link=>link.addEventListener('click',()=>{if(link.dataset.page==='order')resetForm();navigate(link.dataset.page);}));
        $('#mobile-menu').addEventListener('click',()=>toggleSidebar(true));$('#table-search').addEventListener('input',renderOrders);$('#status-filter').addEventListener('change',renderOrders);$('#period-filter').addEventListener('change',renderOrders);
        $('#global-search').addEventListener('input',event=>{if(event.target.value){navigate('orders');$('#table-search').value=event.target.value;renderOrders();}});
        $('#global-search').addEventListener('keydown',event=>{if(event.key==='Enter')navigate('orders');if(event.key==='/')event.preventDefault();});document.addEventListener('keydown',event=>{if(event.key==='/'&&!['INPUT','TEXTAREA'].includes(document.activeElement.tagName)){event.preventDefault();$('#global-search').focus();}});
        $$('[data-sort]').forEach(button=>button.addEventListener('click',event=>{const key=button.dataset.sort;if(event.shiftKey){const existing=sortKeys.find(item=>item.key===key);if(existing)existing.direction*=-1;else sortKeys.push({key,direction:1});}else{const existing=sortKeys.find(item=>item.key===key);sortKeys=[{key,direction:existing?-existing.direction:1}];}renderOrders();}));
        $('#orders-body').addEventListener('dblclick',event=>{const cell=event.target.closest('[data-edit]');if(!cell)return;const order=orders.find(item=>item.id===cell.closest('tr').dataset.id);if(!order)return;const key=cell.dataset.edit,original=order[key];let editor;if(key==='status'){editor=document.createElement('select');['Pendiente','En proceso','Listo','Entregado'].forEach(status=>{const option=document.createElement('option');option.textContent=status;option.value=status;editor.append(option);});editor.value=original;}else{editor=document.createElement('input');editor.value=original;editor.type=key==='advance'?'number':'text';if(key==='advance'){editor.min='0';editor.step='.01';}}editor.className='field !min-w-[90px] !py-1';cell.replaceChildren(editor);editor.focus();const save=()=>{if(editor.dataset.saved)return;editor.dataset.saved='1';order[key]=key==='advance'?Number(editor.value||0):editor.value.trim();persist();renderOrders();renderDashboard();};editor.addEventListener('blur',save);editor.addEventListener('keydown',keyEvent=>{if(keyEvent.key==='Enter')editor.blur();if(keyEvent.key==='Escape'){editor.dataset.saved='1';renderOrders();}});});
        $('#table-export').addEventListener('click',()=>exportOrders(getVisibleOrders(),'rudolf-motos-ordenes-filtradas.xlsx'));$('#table-csv').addEventListener('click',()=>exportOrders(getVisibleOrders(),'rudolf-motos-ordenes-filtradas.csv'));$('#export-nav').addEventListener('click',()=>exportOrders());$('#import-nav').addEventListener('click',()=>$('#import-file').click());$('#import-file').addEventListener('change',event=>{importFile(event.target.files[0]);event.target.value='';});$('#crm-export').addEventListener('click',()=>{const customerMap=new Map();orders.forEach(order=>customerMap.set(order.dni||order.phone||order.client,order));const data=[...customerMap.values()].map(order=>({Cliente:order.client,DNI:order.dni,Teléfono:order.phone,Dirección:order.address,Placa:order.plate,Modelo:order.model,Kilometraje:order.km,'Última visita':order.date}));const sheet=XLSX.utils.json_to_sheet(data),book=XLSX.utils.book_new();XLSX.utils.book_append_sheet(book,sheet,'Clientes');XLSX.writeFile(book,'rudolf-motos-clientes.xlsx');});
        $('#add-part').addEventListener('click',()=>addPart());$('#parts-body').addEventListener('input',updateTotals);$('#parts-body').addEventListener('click',event=>{if(event.target.closest('.remove-part')){event.target.closest('tr').remove();updateTotals();}});['f-labor','f-thirdparty','f-advance'].forEach(id=>$(`#${id}`).addEventListener('input',updateTotals));
        $('#save-order').addEventListener('click',saveOrder);$('#save-order-bottom').addEventListener('click',saveOrder);$('#print-order').addEventListener('click',()=>window.print());$('#whatsapp-order').addEventListener('click',shareOrder);
        $('#ocr-toggle').addEventListener('click',()=>$('#ocr-panel').classList.toggle('hidden'));$('#ocr-file').addEventListener('change',event=>runOCR(event.target.files[0]));$('#ocr-camera').addEventListener('change',event=>runOCR(event.target.files[0]));$('#ocr-drop').addEventListener('dragover',event=>{event.preventDefault();$('#ocr-panel').classList.add('dragover');});$('#ocr-drop').addEventListener('dragleave',()=>$('#ocr-panel').classList.remove('dragover'));$('#ocr-drop').addEventListener('drop',event=>{event.preventDefault();$('#ocr-panel').classList.remove('dragover');runOCR(event.dataTransfer.files[0]);});
        $$('.clear-sign').forEach(button=>button.addEventListener('click',()=>{const canvas=$(`#${button.dataset.canvas}`);canvas.getContext('2d').clearRect(0,0,canvas.width,canvas.height);}));$('#crm-search').addEventListener('input',renderCRM);$('#settings-btn').addEventListener('click',configureFirestore);
        window.addEventListener('resize',()=>$$('.sig-canvas').forEach(setupCanvas));
      }
      buildSystems();bindEvents();renderDashboard();renderOrders();lucide.createIcons();
      try{const config=JSON.parse(localStorage.getItem(FIREBASE_KEY)||'null');if(config)connectFirestore(config).catch(error=>{console.error(error);setSyncState('Solo guardado local','gray');});}catch{setSyncState('Solo guardado local','gray');}
    })();
  </script>
</body>
</html>
