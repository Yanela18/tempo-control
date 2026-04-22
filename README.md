<!doctype html>
<html lang="es" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>TempoControl</title>
  <script src="https://cdn.tailwindcss.com/3.4.17"></script>
  <script src="https://cdn.jsdelivr.net/npm/lucide@0.263.0/dist/umd/lucide.min.js"></script>
  <script src="/_sdk/element_sdk.js"></script>
  <script src="/_sdk/data_sdk.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600;700&amp;display=swap" rel="stylesheet">
  <style>
  * { font-family: 'DM Sans', sans-serif; }
  .tab-active { border-bottom: 3px solid; font-weight: 600; }
  .fade-in { animation: fadeIn 0.3s ease; }
  @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
  input:focus, select:focus { outline: none; box-shadow: 0 0 0 2px rgba(59,130,246,0.3); }
  .toast { animation: slideIn 0.3s ease, fadeOut 0.3s ease 2.7s; }
  @keyframes slideIn { from { transform: translateY(-20px); opacity:0; } to { transform: translateY(0); opacity:1; } }
  @keyframes fadeOut { from { opacity:1; } to { opacity:0; } }
</style>
  <style>body { box-sizing: border-box; }</style>
 </head>
 <body class="h-full bg-slate-50 text-slate-800 overflow-auto">
  <div id="app" class="h-full w-full flex flex-col"><!-- Header -->
   <header id="app-header" class="bg-slate-900 text-white px-6 py-4 flex items-center justify-between shrink-0">
    <div class="flex items-center gap-3">
     <div class="w-9 h-9 rounded-lg bg-emerald-500 flex items-center justify-center">
      <i data-lucide="clock" class="w-5 h-5"></i>
     </div>
     <div>
      <h1 id="header-title" class="text-lg font-bold leading-tight">TempoControl</h1>
      <p id="header-company" class="text-xs text-slate-400">Innovatech Solutions, S.R.L.</p>
     </div>
    </div><span class="text-xs text-slate-500 hidden sm:block" id="current-date"></span>
   </header><!-- Tabs -->
   <nav class="bg-white border-b border-slate-200 flex gap-0 px-4 shrink-0" id="tab-bar"><button onclick="switchTab('employees')" data-tab="employees" class="px-4 py-3 text-sm tab-active border-emerald-500 text-emerald-700">Empleados</button> <button onclick="switchTab('punch')" data-tab="punch" class="px-4 py-3 text-sm text-slate-500 hover:text-slate-700 border-transparent">Fichaje</button> <button onclick="switchTab('report')" data-tab="report" class="px-4 py-3 text-sm text-slate-500 hover:text-slate-700 border-transparent">Reporte</button>
   </nav><!-- Toast container -->
   <div id="toast-container" class="fixed top-4 right-4 z-50 flex flex-col gap-2"></div><!-- Content -->
   <main class="flex-1 overflow-auto p-4 sm:p-6"><!-- EMPLOYEES TAB -->
    <div id="tab-employees" class="fade-in">
     <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 mb-4">
      <h2 class="text-xl font-bold">Gestión de Empleados</h2><button onclick="showEmployeeForm()" class="bg-emerald-600 hover:bg-emerald-700 text-white px-4 py-2 rounded-lg text-sm font-medium flex items-center gap-2 w-fit"> <i data-lucide="plus" class="w-4 h-4"></i> Nuevo Empleado </button>
     </div><!-- Employee Form (hidden by default) -->
     <div id="employee-form" class="hidden bg-white rounded-xl border border-slate-200 p-5 mb-4 fade-in">
      <h3 id="form-title" class="font-semibold mb-3">Registrar Empleado</h3>
      <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
       <div>
        <label class="text-xs font-medium text-slate-500 block mb-1">Nombre Completo</label> <input id="emp-name" type="text" class="w-full border border-slate-300 rounded-lg px-3 py-2 text-sm" placeholder="Juan Pérez">
       </div>
       <div>
        <label class="text-xs font-medium text-slate-500 block mb-1">Departamento</label> <input id="emp-dept" type="text" class="w-full border border-slate-300 rounded-lg px-3 py-2 text-sm" placeholder="Tecnología">
       </div>
       <div>
        <label class="text-xs font-medium text-slate-500 block mb-1">Posición</label> <input id="emp-pos" type="text" class="w-full border border-slate-300 rounded-lg px-3 py-2 text-sm" placeholder="Desarrollador Senior">
       </div>
      </div>
      <div class="flex gap-2 mt-4"><button onclick="saveEmployee()" id="btn-save-emp" class="bg-emerald-600 hover:bg-emerald-700 text-white px-4 py-2 rounded-lg text-sm font-medium">Guardar</button> <button onclick="hideEmployeeForm()" class="bg-slate-200 hover:bg-slate-300 text-slate-700 px-4 py-2 rounded-lg text-sm font-medium">Cancelar</button>
      </div>
     </div><!-- Employee List -->
     <div id="employee-list" class="space-y-2"></div>
     <p id="no-employees" class="text-center text-slate-400 py-10 text-sm hidden">No hay empleados registrados. Haz clic en "Nuevo Empleado" para comenzar.</p>
    </div><!-- PUNCH TAB -->
    <div id="tab-punch" class="hidden fade-in">
     <h2 class="text-xl font-bold mb-4">Registro de Fichaje</h2>
     <div class="bg-white rounded-xl border border-slate-200 p-5 max-w-lg"><label class="text-xs font-medium text-slate-500 block mb-1">Seleccionar Empleado</label> <select id="punch-employee" class="w-full border border-slate-300 rounded-lg px-3 py-2 text-sm mb-4"> <option value="">-- Seleccione --</option> </select>
      <div id="punch-status" class="text-sm text-slate-500 mb-4"></div>
      <div class="flex gap-2"><button onclick="punchIn()" id="btn-punch-in" class="bg-emerald-600 hover:bg-emerald-700 text-white px-5 py-2.5 rounded-lg text-sm font-medium flex items-center gap-2"> <i data-lucide="log-in" class="w-4 h-4"></i> Entrada </button> <button onclick="punchOut()" id="btn-punch-out" class="bg-orange-500 hover:bg-orange-600 text-white px-5 py-2.5 rounded-lg text-sm font-medium flex items-center gap-2"> <i data-lucide="log-out" class="w-4 h-4"></i> Salida </button>
      </div>
     </div>
     <div class="mt-5">
      <h3 class="font-semibold text-sm mb-2">Fichajes de Hoy</h3>
      <div id="today-punches" class="space-y-2"></div>
      <p id="no-punches-today" class="text-slate-400 text-sm hidden">Sin fichajes hoy.</p>
     </div>
    </div><!-- REPORT TAB -->
    <div id="tab-report" class="hidden fade-in">
     <h2 class="text-xl font-bold mb-4">Reporte Mensual de Horas</h2>
     <div class="flex flex-col sm:flex-row gap-3 mb-4">
      <div><label class="text-xs font-medium text-slate-500 block mb-1">Mes</label> <select id="report-month" class="border border-slate-300 rounded-lg px-3 py-2 text-sm"> <option value="1">Enero</option><option value="2">Febrero</option><option value="3">Marzo</option> <option value="4">Abril</option><option value="5">Mayo</option><option value="6">Junio</option> <option value="7">Julio</option><option value="8">Agosto</option><option value="9">Septiembre</option> <option value="10">Octubre</option><option value="11">Noviembre</option><option value="12">Diciembre</option> </select>
      </div>
      <div><label class="text-xs font-medium text-slate-500 block mb-1">Año</label> <input id="report-year" type="number" value="2025" class="border border-slate-300 rounded-lg px-3 py-2 text-sm w-24">
      </div>
      <div class="flex items-end"><button onclick="generateReport()" class="bg-emerald-600 hover:bg-emerald-700 text-white px-5 py-2 rounded-lg text-sm font-medium">Generar Reporte</button>
      </div>
     </div>
     <div id="report-result"></div>
    </div>
   </main>
  </div>
  <script>
// ---- State ----
let allData = [];
let editingEmployeeId = null;
const getEmployees = () => allData.filter(d => d.type === 'employee');
const getPunches = () => allData.filter(d => d.type === 'punch');
const getActiveEmployees = () => getEmployees().filter(e => e.active !== false);

// ---- Element SDK ----
const defaultConfig = {
  app_title: 'TempoControl',
  company_name: 'Innovatech Solutions, S.R.L.',
  background_color: '#f8fafc',
  surface_color: '#ffffff',
  text_color: '#1e293b',
  primary_color: '#059669',
  secondary_color: '#64748b'
};

function applyConfig(config) {
  const c = { ...defaultConfig, ...config };
  document.getElementById('header-title').textContent = c.app_title;
  document.getElementById('header-company').textContent = c.company_name;
  document.body.style.backgroundColor = c.background_color;
  document.querySelectorAll('.bg-white').forEach(el => el.style.backgroundColor = c.surface_color);
  document.body.style.color = c.text_color;
}

window.elementSdk.init({
  defaultConfig,
  onConfigChange: async (config) => applyConfig(config),
  mapToCapabilities: (config) => ({
    recolorables: [
      { get: () => config.background_color || defaultConfig.background_color, set: v => { config.background_color = v; window.elementSdk.setConfig({ background_color: v }); } },
      { get: () => config.surface_color || defaultConfig.surface_color, set: v => { config.surface_color = v; window.elementSdk.setConfig({ surface_color: v }); } },
      { get: () => config.text_color || defaultConfig.text_color, set: v => { config.text_color = v; window.elementSdk.setConfig({ text_color: v }); } },
      { get: () => config.primary_color || defaultConfig.primary_color, set: v => { config.primary_color = v; window.elementSdk.setConfig({ primary_color: v }); } },
      { get: () => config.secondary_color || defaultConfig.secondary_color, set: v => { config.secondary_color = v; window.elementSdk.setConfig({ secondary_color: v }); } },
    ],
    borderables: [],
    fontEditable: {
      get: () => config.font_family || 'DM Sans',
      set: v => { config.font_family = v; window.elementSdk.setConfig({ font_family: v }); }
    },
    fontSizeable: {
      get: () => config.font_size || 14,
      set: v => { config.font_size = v; window.elementSdk.setConfig({ font_size: v }); }
    }
  }),
  mapToEditPanelValues: (config) => new Map([
    ['app_title', config.app_title || defaultConfig.app_title],
    ['company_name', config.company_name || defaultConfig.company_name]
  ])
});

// ---- Data SDK ----
const dataHandler = {
  onDataChanged(data) {
    allData = data;
    renderEmployeeList();
    renderPunchDropdown();
    renderTodayPunches();
  }
};

(async () => {
  const r = await window.dataSdk.init(dataHandler);
  if (!r.isOk) console.error('Data SDK init failed');
})();

// ---- Tabs ----
function switchTab(tab) {
  ['employees','punch','report'].forEach(t => {
    document.getElementById('tab-' + t).classList.toggle('hidden', t !== tab);
  });
  document.querySelectorAll('#tab-bar button').forEach(btn => {
    const isActive = btn.dataset.tab === tab;
    btn.classList.toggle('tab-active', isActive);
    btn.classList.toggle('border-emerald-500', isActive);
    btn.classList.toggle('text-emerald-700', isActive);
    btn.classList.toggle('text-slate-500', !isActive);
    btn.classList.toggle('border-transparent', !isActive);
  });
  if (tab === 'punch') renderPunchDropdown();
}

// ---- Toast ----
function showToast(msg, type='success') {
  const c = document.getElementById('toast-container');
  const t = document.createElement('div');
  t.className = `toast px-4 py-2.5 rounded-lg text-sm font-medium shadow-lg ${type === 'success' ? 'bg-emerald-600 text-white' : 'bg-red-500 text-white'}`;
  t.textContent = msg;
  c.appendChild(t);
  setTimeout(() => t.remove(), 3000);
}

// ---- Employees ----
function showEmployeeForm(emp = null) {
  document.getElementById('employee-form').classList.remove('hidden');
  if (emp) {
    editingEmployeeId = emp.__backendId;
    document.getElementById('form-title').textContent = 'Editar Empleado';
    document.getElementById('emp-name').value = emp.employee_name || '';
    document.getElementById('emp-dept').value = emp.department || '';
    document.getElementById('emp-pos').value = emp.position || '';
  } else {
    editingEmployeeId = null;
    document.getElementById('form-title').textContent = 'Registrar Empleado';
    document.getElementById('emp-name').value = '';
    document.getElementById('emp-dept').value = '';
    document.getElementById('emp-pos').value = '';
  }
}
function hideEmployeeForm() {
  document.getElementById('employee-form').classList.add('hidden');
  editingEmployeeId = null;
}

async function saveEmployee() {
  const name = document.getElementById('emp-name').value.trim();
  const dept = document.getElementById('emp-dept').value.trim();
  const pos = document.getElementById('emp-pos').value.trim();
  if (!name) { showToast('El nombre es requerido', 'error'); return; }

  const btn = document.getElementById('btn-save-emp');
  btn.disabled = true; btn.textContent = 'Guardando...';

  let result;
  if (editingEmployeeId) {
    const existing = allData.find(d => d.__backendId === editingEmployeeId);
    if (existing) {
      result = await window.dataSdk.update({ ...existing, employee_name: name, department: dept, position: pos });
    }
  } else {
    if (allData.length >= 999) { showToast('Límite de 999 registros alcanzado', 'error'); btn.disabled = false; btn.textContent = 'Guardar'; return; }
    const id = 'EMP-' + Date.now();
    result = await window.dataSdk.create({ type: 'employee', employee_ref: id, employee_name: name, department: dept, position: pos, active: true, created_at: new Date().toISOString(), punch_date: '', entry_time: '', exit_time: '' });
  }

  btn.disabled = false; btn.textContent = 'Guardar';
  if (result && result.isOk) { showToast(editingEmployeeId ? 'Empleado actualizado' : 'Empleado registrado'); hideEmployeeForm(); }
  else showToast('Error al guardar', 'error');
}

function renderEmployeeList() {
  const list = document.getElementById('employee-list');
  const emps = getEmployees();
  const noMsg = document.getElementById('no-employees');
  noMsg.classList.toggle('hidden', emps.length > 0);

  const existingEls = new Map([...list.children].map(el => [el.dataset.id, el]));
  const seen = new Set();

  emps.forEach(emp => {
    seen.add(emp.__backendId);
    let el = existingEls.get(emp.__backendId);
    if (!el) {
      el = document.createElement('div');
      el.dataset.id = emp.__backendId;
      el.className = 'bg-white rounded-lg border border-slate-200 px-4 py-3 flex flex-col sm:flex-row sm:items-center justify-between gap-2';
      list.appendChild(el);
    }
    const inactive = emp.active === false;
    el.innerHTML = `
      <div class="flex-1 min-w-0">
        <div class="flex items-center gap-2">
          <span class="font-semibold text-sm truncate">${esc(emp.employee_name)}</span>
          ${inactive ? '<span class="text-xs bg-red-100 text-red-600 px-2 py-0.5 rounded-full">Inactivo</span>' : '<span class="text-xs bg-emerald-100 text-emerald-700 px-2 py-0.5 rounded-full">Activo</span>'}
        </div>
        <p class="text-xs text-slate-500">${esc(emp.department || '-')} · ${esc(emp.position || '-')}</p>
        <p class="text-xs text-slate-400">ID: ${esc(emp.employee_ref || '')}</p>
      </div>
      <div class="flex gap-1 shrink-0">
        <button onclick="editEmp('${emp.__backendId}')" class="p-2 rounded-lg hover:bg-slate-100 text-slate-500" title="Editar"><i data-lucide="edit" class="w-4 h-4"></i></button>
        <button onclick="toggleActive('${emp.__backendId}')" class="p-2 rounded-lg hover:bg-slate-100 ${inactive ? 'text-emerald-600' : 'text-orange-500'}" title="${inactive ? 'Activar' : 'Desactivar'}"><i data-lucide="${inactive ? 'user-check' : 'user-x'}" class="w-4 h-4"></i></button>
      </div>`;
    lucide.createIcons();
  });

  existingEls.forEach((el, id) => { if (!seen.has(id)) el.remove(); });
}

function editEmp(backendId) {
  const emp = allData.find(d => d.__backendId === backendId);
  if (emp) showEmployeeForm(emp);
}

async function toggleActive(backendId) {
  const emp = allData.find(d => d.__backendId === backendId);
  if (!emp) return;
  const r = await window.dataSdk.update({ ...emp, active: !emp.active });
  if (r.isOk) showToast(emp.active ? 'Empleado desactivado' : 'Empleado activado');
  else showToast('Error', 'error');
}

// ---- Punch ----
function renderPunchDropdown() {
  const sel = document.getElementById('punch-employee');
  const val = sel.value;
  const active = getActiveEmployees();
  sel.innerHTML = '<option value="">-- Seleccione --</option>' + active.map(e => `<option value="${e.employee_ref}">${esc(e.employee_name)}</option>`).join('');
  if (val && active.some(e => e.employee_ref === val)) sel.value = val;
  updatePunchStatus();
}

document.getElementById('punch-employee').addEventListener('change', updatePunchStatus);

function updatePunchStatus() {
  const ref = document.getElementById('punch-employee').value;
  const status = document.getElementById('punch-status');
  if (!ref) { status.textContent = ''; return; }
  const today = new Date().toISOString().slice(0, 10);
  const todayPunch = getPunches().find(p => p.employee_ref === ref && p.punch_date === today);
  if (!todayPunch) status.innerHTML = '<span class="text-slate-400">Sin fichaje hoy</span>';
  else if (todayPunch.entry_time && !todayPunch.exit_time) status.innerHTML = `<span class="text-emerald-600">✓ Entrada: ${todayPunch.entry_time}</span>`;
  else if (todayPunch.entry_time && todayPunch.exit_time) status.innerHTML = `<span class="text-blue-600">✓ Entrada: ${todayPunch.entry_time} · Salida: ${todayPunch.exit_time}</span>`;
}

async function punchIn() {
  const ref = document.getElementById('punch-employee').value;
  if (!ref) { showToast('Seleccione un empleado', 'error'); return; }
  const today = new Date().toISOString().slice(0, 10);
  const existing = getPunches().find(p => p.employee_ref === ref && p.punch_date === today);
  if (existing && existing.entry_time) { showToast('Ya tiene entrada registrada hoy', 'error'); return; }
  if (allData.length >= 999) { showToast('Límite de registros alcanzado', 'error'); return; }

  const btn = document.getElementById('btn-punch-in');
  btn.disabled = true;
  const now = new Date().toLocaleTimeString('es-DO', { hour: '2-digit', minute: '2-digit' });
  const emp = getActiveEmployees().find(e => e.employee_ref === ref);
  const r = await window.dataSdk.create({ type: 'punch', employee_ref: ref, employee_name: emp ? emp.employee_name : '', punch_date: today, entry_time: now, exit_time: '', department: '', position: '', active: true, created_at: new Date().toISOString() });
  btn.disabled = false;
  if (r.isOk) showToast('Entrada registrada: ' + now);
  else showToast('Error al registrar', 'error');
}

async function punchOut() {
  const ref = document.getElementById('punch-employee').value;
  if (!ref) { showToast('Seleccione un empleado', 'error'); return; }
  const today = new Date().toISOString().slice(0, 10);
  const existing = getPunches().find(p => p.employee_ref === ref && p.punch_date === today && p.entry_time && !p.exit_time);
  if (!existing) { showToast('No hay entrada abierta hoy', 'error'); return; }

  const btn = document.getElementById('btn-punch-out');
  btn.disabled = true;
  const now = new Date().toLocaleTimeString('es-DO', { hour: '2-digit', minute: '2-digit' });
  const r = await window.dataSdk.update({ ...existing, exit_time: now });
  btn.disabled = false;
  if (r.isOk) showToast('Salida registrada: ' + now);
  else showToast('Error al registrar', 'error');
}

function renderTodayPunches() {
  const today = new Date().toISOString().slice(0, 10);
  const punches = getPunches().filter(p => p.punch_date === today);
  const container = document.getElementById('today-punches');
  document.getElementById('no-punches-today').classList.toggle('hidden', punches.length > 0);

  container.innerHTML = punches.map(p => `
    <div class="bg-white rounded-lg border border-slate-200 px-4 py-2.5 flex items-center justify-between text-sm">
      <span class="font-medium">${esc(p.employee_name)}</span>
      <div class="flex gap-3 text-xs text-slate-500">
        <span class="text-emerald-600">↗ ${p.entry_time || '-'}</span>
        <span class="text-orange-500">↙ ${p.exit_time || '-'}</span>
      </div>
    </div>`).join('');
  updatePunchStatus();
}

// ---- Report ----
function generateReport() {
  const month = parseInt(document.getElementById('report-month').value);
  const year = parseInt(document.getElementById('report-year').value);
  const punches = getPunches().filter(p => {
    if (!p.punch_date) return false;
    const [y, m] = p.punch_date.split('-').map(Number);
    return y === year && m === month;
  });

  const empMap = {};
  punches.forEach(p => {
    if (!empMap[p.employee_ref]) empMap[p.employee_ref] = { name: p.employee_name, days: new Set(), totalMinutes: 0 };
    empMap[p.employee_ref].days.add(p.punch_date);
    if (p.entry_time && p.exit_time) {
      const mins = timeDiffMinutes(p.entry_time, p.exit_time);
      if (mins > 0) empMap[p.employee_ref].totalMinutes += mins;
    }
  });

  const container = document.getElementById('report-result');
  const entries = Object.values(empMap);
  const monthNames = ['','Enero','Febrero','Marzo','Abril','Mayo','Junio','Julio','Agosto','Septiembre','Octubre','Noviembre','Diciembre'];

  if (entries.length === 0) {
    container.innerHTML = '<p class="text-slate-400 text-sm py-6 text-center">No hay datos para el período seleccionado.</p>';
    return;
  }

  container.innerHTML = `
    <div class="bg-white rounded-xl border border-slate-200 overflow-hidden">
      <div class="px-5 py-3 bg-slate-50 border-b border-slate-200">
        <h3 class="font-semibold text-sm">Reporte: ${monthNames[month]} ${year}</h3>
      </div>
      <div class="overflow-x-auto">
        <table class="w-full text-sm">
          <thead><tr class="text-left text-xs text-slate-500 border-b border-slate-100">
            <th class="px-5 py-2.5">Empleado</th>
            <th class="px-5 py-2.5">Días Trabajados</th>
            <th class="px-5 py-2.5">Horas Totales</th>
          </tr></thead>
          <tbody>${entries.map(e => `<tr class="border-b border-slate-50 hover:bg-slate-50">
            <td class="px-5 py-2.5 font-medium">${esc(e.name)}</td>
            <td class="px-5 py-2.5">${e.days.size}</td>
            <td class="px-5 py-2.5">${(e.totalMinutes / 60).toFixed(1)}h</td>
          </tr>`).join('')}</tbody>
        </table>
      </div>
    </div>`;
}

function timeDiffMinutes(t1, t2) {
  const parse = t => { const [h, m] = t.split(':').map(Number); return h * 60 + m; };
  return parse(t2) - parse(t1);
}

function esc(s) { const d = document.createElement('div'); d.textContent = s || ''; return d.innerHTML; }

// ---- Init ----
document.getElementById('current-date').textContent = new Date().toLocaleDateString('es-DO', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
document.getElementById('report-month').value = new Date().getMonth() + 1;
lucide.createIcons();
</script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9f05c69e33c99115',t:'MTc3Njg3MjkzOS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
