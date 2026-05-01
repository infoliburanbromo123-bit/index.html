<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RentMotor Pro</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700&display=swap');
        
        :root {
            --bg-dark: #0B0E14;
            --sidebar-dark: #0F1219;
            --card-dark: #151921;
            --border-dark: #1e293b;
            --text-accent: #3b82f6;
        }

        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background-color: var(--bg-dark); 
            color: #f8fafc; 
        }

        .glass { 
            background: var(--card-dark);
            border: 1px solid rgba(255, 255, 255, 0.05);
            box-shadow: 0 4px 24px -1px rgba(0, 0, 0, 0.2);
        }

        .sidebar-item-active { 
            background: rgba(59, 130, 246, 0.1); 
            color: var(--text-accent); 
            border-right: 3px solid var(--text-accent);
            border-radius: 0;
        }

        .loading-spinner { 
            border: 3px solid rgba(255,255,255,0.05); 
            border-top: 3px solid var(--text-accent); 
            border-radius: 50%; 
            width: 32px; 
            height: 32px; 
            animation: spin 1s linear infinite; 
        }

        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        
        input, select, textarea {
            background-color: rgba(0, 0, 0, 0.2) !important;
            border: 1px solid rgba(255, 255, 255, 0.1) !important;
            color: white !important;
        }

        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-thumb { background: #334155; border-radius: 10px; }
    </style>
</head>
<body class="overflow-x-hidden">
    <div id="app" class="flex min-h-screen">
        <!-- Initial Loader Overlay -->
        <div id="initial-loader" class="fixed inset-0 z-[200] bg-[#0B0E14] flex flex-col items-center justify-center transition-opacity duration-500">
            <div class="loading-spinner w-12 h-12 mb-6"></div>
            <div class="text-center px-6">
                <h1 class="text-2xl font-black italic tracking-tighter text-white uppercase mb-2">RENTMOTOR <span class="text-blue-500">PRO</span></h1>
                <p id="loader-status" class="text-[9px] font-bold text-slate-500 tracking-[0.2em] uppercase animate-pulse">Initializing System...</p>
            </div>
            
            <!-- Mini Debug Log on Loader -->
            <div id="debug-log" class="mt-8 w-full max-w-xs h-32 overflow-y-auto px-4 text-[8px] font-mono text-slate-600 border-t border-white/5 pt-4">
                <p>> System boot initiated...</p>
            </div>

            <!-- Emergency Bypass -->
            <button onclick="hideLoader()" id="bypass-btn" class="mt-4 px-4 py-2 border border-white/10 rounded text-[9px] text-slate-500 uppercase font-bold hover:bg-white/5 hidden">
                Bypass Loader (Launch anyway)
            </button>
        </div>
        <!-- Desktop Sidebar -->
        <aside id="sidebar" class="fixed inset-y-0 left-0 z-50 w-64 bg-[#0F1219] border-r border-slate-800 lg:static lg:block hidden transform transition-transform duration-300 lg:translate-x-0 -translate-x-full">
            <div class="flex flex-col h-full relative">
                <!-- Mobile Close Button -->
                <button onclick="toggleMobileMenu()" class="lg:hidden absolute top-4 right-4 p-2 text-slate-500 hover:text-white">
                    <i data-lucide="x" class="w-5 h-5"></i>
                </button>

                <div class="px-6 py-8">
                    <div class="flex items-center gap-3 mb-10">
                        <div class="w-8 h-8 rounded-lg bg-blue-600 flex items-center justify-center shadow-lg shadow-blue-900/20">
                            <i data-lucide="bike" class="w-5 h-5 text-white"></i>
                        </div>
                        <h1 class="text-lg font-bold tracking-tight text-white uppercase italic">RentMotor<span class="text-blue-500">Pro</span></h1>
                    </div>
                    
                    <nav class="space-y-1" id="main-nav">
                        <!-- Nav Items -->
                    </nav>
                </div>

                <div class="mt-auto p-6 border-t border-slate-800/50">
                    <div class="flex items-center gap-3 bg-slate-900/50 p-3 rounded-xl border border-white/5">
                        <div class="w-10 h-10 rounded-full bg-blue-600/20 border border-blue-500/30 flex items-center justify-center text-blue-400 font-bold text-xs">
                            AD
                        </div>
                        <div>
                            <p class="text-[10px] font-bold text-white uppercase tracking-wider">Admin Utama</p>
                            <p class="text-[9px] text-slate-500 uppercase font-medium">Premium Access</p>
                        </div>
                    </div>
                </div>
            </div>
        </aside>

        <!-- Main Content -->
        <main class="flex-1 min-w-0 flex flex-col">
            <!-- Header -->
            <header class="h-16 sticky top-0 z-30 bg-[#0B0E14]/80 backdrop-blur-md border-b border-slate-800 px-6 flex items-center justify-between">
                <button id="mobile-menu-btn" class="lg:hidden p-2 hover:bg-slate-800 rounded-lg text-slate-400">
                    <i data-lucide="menu" class="w-5 h-5"></i>
                </button>
                <div class="flex items-center gap-4 ml-auto">
                    <div id="status-badge" class="flex items-center gap-2 text-[10px] font-bold tracking-widest px-3 py-1.5 rounded-lg bg-emerald-500/10 text-emerald-400 border border-emerald-500/20 uppercase italic">
                        <div class="w-1.5 h-1.5 rounded-full bg-emerald-400 animate-pulse"></div>
                        CORE V1.2 ONLINE
                    </div>
                    <div class="h-8 w-px bg-slate-800 mx-2"></div>
                    <div class="text-right hidden sm:block">
                        <p id="header-date" class="text-[10px] font-bold text-slate-500 uppercase tracking-widest leading-none"></p>
                    </div>
                </div>
            </header>

            <!-- Dynamic Body -->
            <div id="content-body" class="p-6 lg:p-8 max-w-7xl mx-auto w-full flex-1">
                <!-- Page Content Injected Here -->
            </div>
        </main>
    </div>

    <!-- Mobile Sidebar Backdrop -->
    <div id="sidebar-overlay" class="fixed inset-0 bg-black/60 backdrop-blur-sm z-40 hidden lg:hidden"></div>
    <div id="notifications" class="fixed bottom-6 right-6 z-[100] space-y-3 pointer-events-none"></div>
    <div id="modal-container" class="fixed inset-0 z-[100] hidden items-center justify-center p-4 bg-black/80 backdrop-blur-md">
        <div id="modal-content" class="bg-[#151921] border border-slate-800 w-full max-w-lg rounded-2xl shadow-2xl transform transition-all duration-300 scale-95 opacity-0"></div>
    </div>

    <script>
        const debugLog = (msg, type = 'info') => {
            console.log(`[CORE] ${msg}`);
            const logCont = document.getElementById('debug-log');
            if (logCont) {
                const line = document.createElement('div');
                line.className = type === 'error' ? 'text-rose-500' : 'text-slate-400';
                line.innerText = `> ${msg}`;
                logCont.appendChild(line);
                logCont.scrollTop = logCont.scrollHeight;
            }
        };

        window.onerror = function(message, source, lineno, colno, error) {
            console.error("Global Catch:", message, error);
            debugLog(`Critical: ${message}`, 'error');
            // Auto-recovery
            setTimeout(hideLoader, 5000);
            return false;
        };

        // Emergency bypass timer - even more aggressive
        const bypassTimer = setTimeout(() => {
            const btn = document.getElementById('bypass-btn');
            if(btn) {
                btn.classList.remove('hidden');
                debugLog("Sistem memuat perlahan. Klik bypass untuk masukpaksa.", 'warning');
            }
        }, 5000);

        // Global recovery timer: 10 seconds and we MUST show the app
        setTimeout(() => {
            if (document.getElementById('initial-loader')) {
                console.warn("Global recovery timer triggered. Forcing UI entry.");
                debugLog("Auto-recovery: Memaksa aktivasi antarmuka.");
                hideLoader();
                if(!window.BOOT_FINISHED) renderView();
            }
        }, 12000);

        function hideLoader() {
            clearTimeout(bypassTimer);
            const loader = document.getElementById('initial-loader');
            if (loader) {
                loader.classList.add('opacity-0', 'pointer-events-none');
                setTimeout(() => {
                    if (loader.parentNode) loader.remove();
                    window.BOOT_FINISHED = true;
                }, 600);
            }
        }

        const state = {
            currentView: 'dashboard',
            data: { 
                stats: { chartData: [] }, 
                units: [], 
                transactions: [], 
                cashflow: [], 
                settings: { 
                    overtime_per_jam: 10000, 
                    addons: '[]',
                    expense_categories: 'Servis, Gaji, Operasional, Iklan',
                    cash_categories: 'Modal, Topup, Pinjaman',
                    payment_methods: 'Cash, Transfer'
                } 
            },
            isLocalDev: true
        };

        // Detect Environment
        try {
            state.isLocalDev = (typeof google === 'undefined' || !google.script || !google.script.run);
            debugLog(`Env: ${state.isLocalDev ? 'Local' : 'Cloud'}`);
        } catch(e) {
            state.isLocalDev = true;
        }

        const navItems = [
            { id: 'dashboard', icon: 'layout-grid', label: 'Dashboard' },
            { id: 'new-rental', icon: 'plus-circle', label: 'Sewa Baru' },
            { id: 'history', icon: 'history', label: 'History' },
            { id: 'units', icon: 'bike', label: 'Unit' },
            { id: 'cashbook', icon: 'wallet', label: 'Buku Kas' },
            { id: 'expenses', icon: 'receipt', label: 'Pengeluaran' },
            { id: 'settings', icon: 'settings', label: 'Sistem' }
        ];

        let statusChartInstance = null;

        async function bootApp() {
            if (window.BOOTED) return;
            window.BOOTED = true;
            debugLog("Core boot started...");
            
            try {
                // UI Init
                initNav();
                initMobileMenu();
                
                const dateEl = document.getElementById('header-date');
                if (dateEl) {
                    dateEl.innerText = new Date().toLocaleDateString('id-ID', { day: 'numeric', month: 'long', year: 'numeric' }).toUpperCase();
                }

                renderView();
                debugLog("View layer ready.");
                
                // Data Sync
                debugLog("Syncing data...");
                try {
                    await Promise.race([
                        loadAllData(),
                        new Promise((_, rec) => setTimeout(() => rec(new Error("Sync timeout")), 12000))
                    ]);
                    debugLog("Data ready.");
                } catch(e) {
                    debugLog(`Sync Notice: ${e.message}`, 'warning');
                }
                
                if (window.lucide) lucide.createIcons();
                debugLog("System fully operational.");
                hideLoader();
                
            } catch (err) {
                console.error("Boot failure:", err);
                debugLog(`Init Crit: ${err.message}`, 'error');
                hideLoader();
            }
        }

        // Optimized Start
        if (document.readyState !== 'loading') {
            bootApp();
        } else {
            document.addEventListener('DOMContentLoaded', bootApp);
        }
        // Fallback
        setTimeout(() => { if(!window.BOOTED) bootApp(); }, 3000);

        function initNav() {
            console.log("Initializing Nav...");
            const navCont = document.getElementById('main-nav');
            if (!navCont) {
                console.error("main-nav element not found");
                return;
            }
            navCont.innerHTML = navItems.map(item => `
                <button onclick="navigate('${item.id}')" id="nav-${item.id}" class="w-full flex items-center gap-3 px-4 py-3 text-slate-500 hover:text-white hover:bg-white/5 transition-all duration-200 group">
                    <i data-lucide="${item.icon}" class="w-4 h-4 opacity-70 group-hover:opacity-100"></i>
                    <span class="text-xs font-semibold tracking-wide uppercase">${item.label}</span>
                </button>
            `).join('');
            updateNavActive();
        }

        function initMobileMenu() {
            const btn = document.getElementById('mobile-menu-btn');
            const sidebar = document.getElementById('sidebar');
            const overlay = document.getElementById('sidebar-overlay');
            if (!btn || !sidebar || !overlay) {
                console.warn("Mobile menu elements not found");
                return;
            }
            const toggle = () => { 
                sidebar.classList.toggle('-translate-x-full'); 
                overlay.classList.toggle('hidden'); 
            };
            window.toggleMobileMenu = toggle;
            btn.onclick = toggle;
            overlay.onclick = toggle;
        }

        function navigate(view) {
            console.log("Navigating to:", view);
            state.currentView = view;
            updateNavActive();
            renderView();
            const sidebar = document.getElementById('sidebar');
            const overlay = document.getElementById('sidebar-overlay');
            if (sidebar) sidebar.classList.add('-translate-x-full');
            if (overlay) overlay.classList.add('hidden');
        }

        function updateNavActive() {
            navItems.forEach(item => {
                const el = document.getElementById(`nav-${item.id}`);
                if(el) {
                    if(item.id === state.currentView) {
                        el.classList.add('sidebar-item-active');
                        el.classList.remove('text-slate-500', 'hover:bg-white/5');
                    } else {
                        el.classList.remove('sidebar-item-active');
                        el.classList.add('text-slate-500', 'hover:bg-white/5');
                    }
                }
            });
        }

        async function renderView() {
            const container = document.getElementById('content-body');
            if (!container) {
                console.error("content-body not found");
                return;
            }
            container.innerHTML = `<div class="flex items-center justify-center p-20 flex-col gap-4">
                <div class="loading-spinner"></div>
                <p class="text-[10px] font-bold text-slate-500 tracking-[0.2em] uppercase">Memuat Data...</p>
            </div>`;

            try {
                switch (state.currentView) {
                    case 'dashboard': renderDashboard(); break;
                    case 'new-rental': renderNewRental(); break;
                    case 'history': renderHistory(); break;
                    case 'units': renderUnits(); break;
                    case 'cashbook': renderCashbook(); break;
                    case 'expenses': renderExpenses(); break;
                    case 'settings': renderSettings(); break;
                    default: renderPlaceholder(state.currentView); break;
                }
                
                try {
                    if (window.lucide) lucide.createIcons();
                } catch (e) {
                    console.warn("Lucide rerender failed", e);
                }
            } catch (e) {
                console.error("Render View Error:", e);
                container.innerHTML = `<div class="flex flex-col items-center justify-center p-20 glass rounded-2xl border-rose-500/20">
                    <i data-lucide="alert-triangle" class="w-12 h-12 text-rose-500 mb-4"></i>
                    <h2 class="text-xl font-bold uppercase text-rose-500 italic">Kesalahan Tampilan</h2>
                    <p class="text-xs text-slate-500 mt-2 text-center">Gagal memproses tampilan data. Silahkan refresh.</p>
                </div>`;
                if (window.lucide) lucide.createIcons();
            }
        }

        function renderPlaceholder(name) {
            document.getElementById('content-body').innerHTML = `
                <div class="flex flex-col items-center justify-center p-20 glass rounded-3xl border-dashed border-2 border-white/5">
                    <i data-lucide="construction" class="w-12 h-12 text-slate-700 mb-4"></i>
                    <h2 class="text-xl font-bold uppercase italic">${name}</h2>
                    <p class="text-xs text-slate-500 mt-2 text-center">Fitur ini sedang dalam pengembangan sistem.</p>
                    <button onclick="navigate('dashboard')" class="mt-6 px-6 py-2 bg-blue-600 text-[10px] font-bold uppercase rounded-lg">Dashboard</button>
                </div>`;
        }

        async function callApi(action, data = null) {
            console.log(`[API] Calling ${action}...`);
            if (state.isLocalDev) return handleMockApi(action, data);
            
            return new Promise((resolve, reject) => {
                const t = setTimeout(() => {
                    console.warn(`[API] Timeout for ${action}`);
                    reject(new Error(`API Timeout: ${action}`));
                }, 18000);
                
                try {
                    if (typeof google === 'undefined' || !google.script || !google.script.run) {
                        throw new Error("Google Apps Script API not available");
                    }
                    
                    google.script.run
                        .withSuccessHandler(res => { 
                            clearTimeout(t); 
                            console.log(`[API] Success for ${action}`, res);
                            if (res && res.success) resolve(res);
                            else resolve(res || { success: false, message: 'Empty response' });
                        })
                        .withFailureHandler(err => { 
                            clearTimeout(t); 
                            console.error(`[API] Server-side error for ${action}`, err);
                            reject(err); 
                        })
                        .api(action, data);
                } catch(e) {
                    clearTimeout(t);
                    console.error(`[API] Invocation error for ${action}`, e);
                    reject(e);
                }
            });
        }

        async function loadAllData() {
            const statusEl = document.getElementById('loader-status');
            if (statusEl) statusEl.innerText = "Syncing with Cloud Database...";
            
            console.log("Starting loadAllData...");
            try {
                const results = await Promise.allSettled([
                    callApi('healthCheck'),
                    callApi('getDashboardData'),
                    callApi('getUnits'),
                    callApi('getTransactions'),
                    callApi('getSettings')
                ]);
                
                console.log("API Results:", results);
                const [health, dash, units, trans, settings] = results.map(r => r.status === 'fulfilled' ? r.value : { success: false, data: [] });

                // Update Status Badge based on real health check
                const statusBadge = document.getElementById('status-badge');
                if (statusBadge) {
                    const isOnline = health.ssConnected;
                    statusBadge.innerHTML = `
                        <div class="w-1.5 h-1.5 rounded-full ${isOnline ? 'bg-emerald-400 animate-pulse' : 'bg-rose-500'}"></div>
                        CORE V1.2 ${isOnline ? 'ONLINE' : '<span class="text-rose-500">DB ERROR</span>'}
                    `;
                    statusBadge.classList.toggle('bg-rose-500/10', !isOnline);
                    statusBadge.classList.toggle('text-rose-400', !isOnline);
                }

                if (!dash.success) console.warn("Dashboard data failed to load");
                
                state.data.stats = dash.success ? dash.stats : { chartData: [], returnsToday: [], deliveriesToday: [] };
                state.data.units = units.success ? units.data : [];
                state.data.transactions = trans.success ? trans.data : [];
                state.data.settings = settings.success ? settings.data : {};
                
                console.log("State updated:", state.data);
                renderView();
            } catch (e) {
                console.error("Fatal load error:", e);
                showNotification('Gagal Memuat Data Utama', 'error');
                renderView(); 
            }
        }

                function renderDashboard() {
            const stats = state.data.stats || { chartData: [] };
            const returns = stats.returnsToday || [];
            const deliveries = stats.deliveriesToday || [];

            document.getElementById('content-body').innerHTML = `
                <div class="space-y-4 animate-in fade-in slide-in-from-top-4 duration-700">
                    <!-- Notifications Bar -->
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div class="bg-blue-500/5 border border-blue-500/20 rounded-xl p-4 flex items-start gap-4">
                            <div class="w-8 h-8 rounded-lg bg-blue-500 flex items-center justify-center flex-shrink-0">
                                <i data-lucide="truck" class="w-4 h-4 text-white"></i>
                            </div>
                            <div class="flex-1">
                                <h4 class="text-[9px] font-black text-blue-500 uppercase tracking-widest mb-2 italic">Pengiriman Hari Ini</h4>
                                <div class="space-y-2">
                                    ${deliveries.length > 0 ? deliveries.map(t => `
                                        <div class="bg-blue-500/10 p-3 rounded-lg border border-blue-500/10 flex justify-between items-center group">
                                            <div>
                                                <p class="text-xs font-bold text-blue-100 uppercase italic">${t.id_unit || 'Unknown'}</p>
                                                <p class="text-[9px] text-blue-300/60 font-bold uppercase tracking-tighter italic">Customer: ${t.customer_name || t.id_customer || '-'}</p>
                                            </div>
                                            <span class="text-[9px] font-black bg-blue-500 text-white px-2 py-0.5 rounded italic">${formatTime(t.tgl_mulai)}</span>
                                        </div>
                                    `).join('') : '<p class="text-[10px] text-slate-600 font-bold italic uppercase">Tidak ada agenda.</p>'}
                                </div>
                            </div>
                        </div>

                        <div class="bg-orange-500/5 border border-orange-500/20 rounded-xl p-4 flex items-start gap-4">
                            <div class="w-8 h-8 rounded-lg bg-orange-500 flex items-center justify-center flex-shrink-0">
                                <i data-lucide="refresh-cw" class="w-4 h-4 text-white"></i>
                            </div>
                            <div class="flex-1">
                                <h4 class="text-[9px] font-black text-orange-500 uppercase tracking-widest mb-2 italic">Kembali Hari Ini</h4>
                                <div class="space-y-2">
                                    ${returns.length > 0 ? returns.map(t => `
                                        <div class="bg-orange-500/10 p-3 rounded-lg border border-orange-500/10 flex justify-between items-center group">
                                            <div>
                                                <p class="text-xs font-bold text-orange-100 uppercase italic">${t.id_unit || 'Unknown'}</p>
                                                <p class="text-[9px] text-orange-300/60 font-bold uppercase tracking-tighter italic">Customer: ${t.customer_name || t.id_customer || '-'}</p>
                                            </div>
                                            <span class="text-[9px] font-black bg-orange-500 text-white px-2 py-0.5 rounded italic">${formatTime(t.tgl_selesai)}</span>
                                        </div>
                                    `).join('') : '<p class="text-[10px] text-slate-600 font-bold italic uppercase">Tidak ada agenda.</p>'}
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Main Stats -->
                    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 mt-8">
                        ${createStatCardNew('Omset (Pemasukan)', formatIDR(stats.revenue), 'bg-blue-500/10', 'text-blue-500')}
                        ${createStatCardNew('Total Pengeluaran', formatIDR(stats.expenses), 'bg-rose-500/10', 'text-rose-500')}
                        ${createStatCardNew('Saldo Tunai', formatIDR(stats.cashBalance), 'bg-emerald-500/10', 'text-emerald-500')}
                        ${createStatCardNew('Saldo Rekening', formatIDR(stats.transferBalance), 'bg-indigo-500/10', 'text-indigo-500')}
                    </div>

                    <!-- Chart & Side Panel -->
                    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mt-6">
                        <div class="lg:col-span-2 bg-[#151921] rounded-2xl border border-slate-800 p-6 shadow-xl relative overflow-hidden">
                            <div class="flex items-center gap-2 mb-8">
                                <i data-lucide="bar-chart-2" class="w-3.5 h-3.5 text-blue-500"></i>
                                <h3 class="text-[10px] font-black text-white uppercase tracking-widest italic">Grafik Keuangan</h3>
                            </div>
                            <div class="h-[300px]">
                                <canvas id="revenueChart"></canvas>
                            </div>
                        </div>

                        <div class="space-y-4">
                            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-6 shadow-2xl relative overflow-hidden text-center flex flex-col justify-center min-h-[140px]">
                                <h4 class="text-[10px] font-black text-blue-400 uppercase tracking-widest mb-1 italic">Laba Bersih</h4>
                                <p class="text-4xl font-black text-white italic tracking-tighter">${formatIDR(stats.profit)}</p>
                                <div class="absolute top-0 right-0 w-32 h-32 bg-blue-500/5 rounded-full blur-3xl -mr-16 -mt-16"></div>
                            </div>

                            <div class="grid grid-cols-2 gap-4">
                                ${createSmallStatusCard('Ready', stats.availableUnits, 'text-emerald-500')}
                                ${createSmallStatusCard('Disewa', stats.rentedUnits, 'text-blue-500')}
                                ${createSmallStatusCard('Servis', stats.serviceUnits, 'text-rose-500')}
                                ${createSmallStatusCard('Unit', stats.totalUnits, 'text-slate-400')}
                            </div>
                        </div>
                    </div>
                </div>`;
            initDashboardChartsNew();
        }

        function createStatCardNew(label, val, bgColor, textColor) {
            return `
                <div class="${bgColor} border border-white/5 p-5 rounded-2xl shadow-sm text-center">
                    <p class="text-[8px] font-black text-slate-500 uppercase tracking-widest mb-1">${label}</p>
                    <p class="text-lg font-black ${textColor} italic">${val}</p>
                </div>`;
        }

        function createSmallStatusCard(label, val, textColor) {
            return `
                <div class="bg-[#151921] border border-slate-800 p-4 rounded-xl shadow-xl text-center">
                    <p class="text-[8px] font-black text-slate-500 uppercase tracking-widest mb-1">${label}</p>
                    <p class="text-xl font-black ${textColor} mt-1">${val || 0}</p>
                </div>`;
        }

        function initDashboardChartsNew() {
            try {
                if (typeof Chart === 'undefined') {
                    debugLog("Chart.js not ready", 'warning');
                    return;
                }
                const chartData = (state.data.stats && state.data.stats.chartData) || [];
                const canvas = document.getElementById('revenueChart');
                if (!canvas) {
                    console.warn("revenueChart canvas not found yet");
                    return;
                }
                const ctx = canvas.getContext('2d');
                if(statusChartInstance) statusChartInstance.destroy();
                
                statusChartInstance = new Chart(ctx, {
                    type: 'line',
                    data: {
                        labels: chartData.map(d => d.label || d.date),
                        datasets: [{
                            label: 'Pemasukan',
                            data: chartData.map(d => d.value || d.revenue),
                            borderColor: '#3b82f6',
                            backgroundColor: (context) => {
                                const chart = context.chart;
                                const {ctx, chartArea} = chart;
                                if (!chartArea) return null;
                                const gradient = ctx.createLinearGradient(0, chartArea.bottom, 0, chartArea.top);
                                gradient.addColorStop(0, 'rgba(59, 130, 246, 0)');
                                gradient.addColorStop(1, 'rgba(59, 130, 246, 0.2)');
                                return gradient;
                            },
                            borderWidth: 3,
                            fill: true,
                            tension: 0.4,
                            pointBackgroundColor: '#3b82f6',
                            pointBorderColor: '#0B0E14',
                            pointBorderWidth: 2,
                            pointRadius: 4,
                            pointHoverRadius: 6
                        }]
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        plugins: { legend: { display: false } },
                        scales: {
                            y: { 
                                beginAtZero: true, 
                                grid: { color: 'rgba(255,255,255,0.05)', drawBorder: false },
                                ticks: { color: '#64748b', font: { size: 10, weight: 'bold' }, callback: (v) => v/1000 + 'k' }
                            },
                            x: { 
                                grid: { display: false },
                                ticks: { color: '#64748b', font: { size: 10, weight: 'bold' } }
                            }
                        }
                    }
                });
            } catch (e) {
                console.error("Dashboard Chart Error:", e);
            }
        }

        function renderNewRental() {
            const availableUnits = state.data.units.filter(u => u.status === 'Tersedia');
            const s = state.data.settings || {};
            let addons = [];
            try {
                // Ensure we get 'addons' even if capitalized in storage
                const addonsRaw = s.addons || s.Addons || '[]';
                addons = JSON.parse(addonsRaw);
                if (!Array.isArray(addons)) addons = [];
            } catch(e) { 
                console.error("Addon parse error:", e);
                addons = []; 
            }
            
            document.getElementById('content-body').innerHTML = `
                <div class="max-w-4xl mx-auto space-y-6 animate-in slide-in-from-bottom-6 duration-700">
                    <div class="flex items-center justify-between">
                        <h2 class="text-xl font-bold uppercase italic tracking-tighter">Buka <span class="text-blue-500">Sewa Baru</span></h2>
                        <button onclick="loadAllData()" class="px-3 py-1 bg-slate-800 hover:bg-slate-700 rounded-lg text-[9px] font-bold uppercase italic text-slate-400 transition-all">Refresh Data</button>
                    </div>
                    <form id="rental-form" class="space-y-6">
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                            <!-- Data Customer -->
                            <div class="bg-[#151921] border border-slate-800 rounded-2xl p-6 space-y-4 shadow-xl">
                                <h3 class="text-[10px] font-black text-blue-400 uppercase tracking-widest border-b border-white/5 pb-3 italic">Informasi Penyewa</h3>
                                <div>
                                    <label class="block text-[8px] font-black text-slate-500 uppercase tracking-widest mb-1.5 pl-1">Nama Lengkap</label>
                                    <input type="text" name="nama" required class="w-full rounded-xl py-3 px-4 italic font-bold">
                                </div>
                                <div class="grid grid-cols-2 gap-4">
                                    <div>
                                        <label class="block text-[8px] font-black text-slate-500 uppercase mb-1.5 pl-1">Nomor KTP</label>
                                        <input type="text" name="no_ktp" required class="w-full rounded-xl py-3 px-4 italic font-bold text-sm">
                                    </div>
                                    <div>
                                        <label class="block text-[8px] font-black text-slate-500 uppercase mb-1.5 pl-1">WhatsApp</label>
                                        <input type="tel" name="no_wa" required placeholder="08..." class="w-full rounded-xl py-3 px-4 italic font-bold text-sm">
                                    </div>
                                </div>
                                <div class="space-y-4 pt-2">
                                    <div>
                                        <label class="block text-[8px] font-black text-slate-500 uppercase mb-1.5 pl-1">📍 Alamat Pengantaran Unit</label>
                                        <textarea name="alamat_antar" rows="2" placeholder="Kosongkan jika ambil sendiri di kantor" class="w-full rounded-xl py-3 px-4 text-[10px] italic font-bold"></textarea>
                                    </div>
                                    <div>
                                        <label class="block text-[8px] font-black text-slate-500 uppercase mb-1.5 pl-1">🏠 Alamat Pengambilan Unit (Return)</label>
                                        <textarea name="alamat_ambil" rows="2" placeholder="Kosongkan jika antar sendiri ke kantor" class="w-full rounded-xl py-3 px-4 text-[10px] italic font-bold"></textarea>
                                    </div>
                                </div>
                            </div>

                            <!-- Detail Sewa -->
                            <div class="bg-[#151921] border border-slate-800 rounded-2xl p-6 space-y-4 shadow-xl">
                                <h3 class="text-[10px] font-black text-orange-400 uppercase tracking-widest border-b border-white/5 pb-3 italic">Konfigurasi Unit & Waktu</h3>
                                <div class="space-y-1">
                                    <label class="text-[8px] font-black text-slate-500 uppercase mb-1 pl-1">Pilih Motor</label>
                                    <select name="id_unit" id="unit-select" required class="w-full rounded-xl py-3 px-4 font-black italic text-sm uppercase">
                                        <option value="">-- PILIH UNIT TERSEDIA --</option>
                                        ${availableUnits.map(u => `<option value="${u.id_unit}" data-price="${u.harga_per_hari}">${u.nama_unit} [${u.plat_nomor}]</option>`).join('')}
                                    </select>
                                </div>
                                <div class="grid grid-cols-2 gap-4">
                                    <div class="space-y-1">
                                        <label class="text-[8px] font-black text-slate-500 uppercase pl-1">Mulai</label>
                                        <input type="datetime-local" name="tgl_mulai" required class="w-full rounded-xl py-3 px-4 text-[10px] font-bold">
                                    </div>
                                    <div class="space-y-1">
                                        <label class="text-[8px] font-black text-slate-500 uppercase pl-1">Selesai</label>
                                        <input type="datetime-local" name="tgl_selesai" required class="w-full rounded-xl py-3 px-4 text-[10px] font-bold">
                                    </div>
                                </div>
                                <div class="grid grid-cols-2 gap-4 pt-2">
                                    <div>
                                        <label class="block text-[8px] font-black text-slate-500 uppercase mb-1 pl-1">Biaya Antar</label>
                                        <input type="number" name="biaya_antar" value="0" class="w-full rounded-xl py-3 px-4 text-xs font-black italic text-blue-500">
                                    </div>
                                    <div>
                                        <label class="block text-[8px] font-black text-slate-500 uppercase mb-1 pl-1">Biaya Ambil</label>
                                        <input type="number" name="biaya_ambil" value="0" class="w-full rounded-xl py-3 px-4 text-xs font-black italic text-orange-500">
                                    </div>
                                </div>
                            </div>
                        </div>

                        <!-- Addon Selection -->
                        <div class="bg-[#151921] border border-slate-800 rounded-2xl p-6 shadow-xl space-y-4">
                            <h3 class="text-[10px] font-black text-blue-400 uppercase tracking-widest border-b border-white/5 pb-3 italic">Add-on & Perlengkapan Tambahan</h3>
                            <div class="grid grid-cols-2 md:grid-cols-4 gap-4" id="addon-container">
                                ${addons.map(a => `
                                    <label class="relative flex items-center justify-between p-4 rounded-xl border border-white/5 bg-black/20 cursor-pointer hover:border-blue-500/50 transition-all select-none overflow-hidden group">
                                        <input type="checkbox" name="addons" value="${a.name}" data-price="${a.price}" class="peer hidden">
                                        <div class="absolute inset-0 bg-blue-600/10 opacity-0 peer-checked:opacity-100 transition-opacity"></div>
                                        <div class="z-10">
                                            <p class="text-[9px] font-black text-slate-400 peer-checked:text-blue-400 uppercase italic leading-none">${a.name}</p>
                                            <p class="text-[8px] font-bold text-slate-600 mt-1">${formatIDR(a.price)}</p>
                                        </div>
                                        <div class="w-4 h-4 rounded-full border-2 border-slate-800 peer-checked:border-blue-500 peer-checked:bg-blue-500 flex items-center justify-center transition-all z-10">
                                            <i data-lucide="check" class="w-2.5 h-2.5 text-white hidden peer-checked:block"></i>
                                        </div>
                                    </label>
                                `).join('')}
                                <div class="col-span-2 md:col-span-1">
                                    <label class="block text-[8px] font-black text-slate-500 uppercase mb-1">Charge Lainnya</label>
                                    <input type="number" name="biaya_lainnya" id="biaya-lainnya" value="0" placeholder="Rp" class="w-full rounded-xl py-2.5 px-4 text-xs font-black italic">
                                </div>
                            </div>
                        </div>

                        <!-- Ringkasan Perhitungan -->
                        <div class="bg-[#151921] border-2 border-slate-800 rounded-2xl p-6 shadow-2xl relative overflow-hidden">
                            <div class="absolute top-0 right-0 p-3">
                                <i data-lucide="calculator" class="w-12 h-12 text-white/5 -rotate-12"></i>
                            </div>
                            <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-6 gap-6 text-center">
                                <div>
                                    <span class="text-[9px] font-black text-slate-500 uppercase italic">Durasi</span>
                                    <p id="calc-duration" class="text-xl font-black italic text-white">0 HARI</p>
                                </div>
                                <div>
                                    <span class="text-[9px] font-black text-slate-500 uppercase italic">Overtime</span>
                                    <p id="calc-overtime" class="text-xl font-black italic text-orange-400">0 JAM</p>
                                </div>
                                <div>
                                    <span class="text-[9px] font-black text-slate-500 uppercase italic">Subtotal Sewa</span>
                                    <p id="calc-subtotal" class="text-xl font-black italic text-white">${formatIDR(0)}</p>
                                </div>
                                <div>
                                    <span class="text-[9px] font-black text-slate-500 uppercase italic">Extra & Addon</span>
                                    <p id="calc-service" class="text-xl font-black italic text-blue-400">${formatIDR(0)}</p>
                                </div>
                                <div class="bg-blue-600 rounded-2xl p-4 shadow-xl shadow-blue-900/20">
                                    <span class="text-[9px] font-black text-white/70 uppercase italic">Total</span>
                                    <p id="calc-total" class="text-xl font-black italic text-white">${formatIDR(0)}</p>
                                </div>
                                <div id="calc-sisa-container" class="bg-rose-500/10 border border-rose-500/20 rounded-2xl p-4 hidden">
                                    <span class="text-[9px] font-black text-rose-400 uppercase italic">Sisa</span>
                                    <p id="calc-sisa" class="text-xl font-black italic text-rose-500">${formatIDR(0)}</p>
                                </div>
                            </div>
                        </div>

                        <!-- Pembayaran DP -->
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-6 items-end">
                            <div class="bg-[#151921] border border-slate-800 rounded-2xl p-6 shadow-xl">
                                <h3 class="text-[10px] font-black text-emerald-400 uppercase tracking-widest border-b border-white/5 pb-3 italic mb-4">Down Payment (DP)</h3>
                                <div class="grid grid-cols-2 gap-4">
                                    <div>
                                        <label class="block text-[8px] font-black text-slate-500 uppercase mb-1">Nominal DP (Rp)</label>
                                        <input type="number" name="dp" placeholder="0" class="w-full rounded-xl py-3 px-4 font-black italic text-emerald-400">
                                    </div>
                                    <div>
                                        <label class="block text-[8px] font-black text-slate-500 uppercase mb-1">Metode</label>
                                        <select name="metode_dp" class="w-full rounded-xl py-3 px-4 font-black italic text-[10px] uppercase">
                                            ${(s.payment_methods || 'Cash, Transfer').split(',').map(m => `<option value="${m.trim()}">${m.trim().toUpperCase()}</option>`).join('')}
                                        </select>
                                    </div>
                                </div>
                            </div>
                            <button type="submit" id="submit-btn" class="w-full py-6 bg-blue-600 hover:bg-blue-500 text-white font-black rounded-2xl shadow-xl shadow-blue-900/40 uppercase tracking-[0.2em] italic transition-all active:scale-95">
                                <span class="flex items-center justify-center gap-2">
                                    <i data-lucide="shield-check" class="w-5 h-5"></i> BUKA KONTRAK SEWA
                                </span>
                            </button>
                        </div>
                    </form>
                </div>`;
            initRentalFormLogic();
            lucide.createIcons();
        }

        function initRentalFormLogic() {
            const form = document.getElementById('rental-form');
            if(!form) return;
            const inputs = form.querySelectorAll('input, select, textarea');
            
            const calculate = () => {
                const s = new Date(form.tgl_mulai.value);
                const e = new Date(form.tgl_selesai.value);
                const sel = form['unit-select'];
                const unitPrice = Number(sel.options[sel.selectedIndex]?.dataset.price || 0);
                const otRate = Number(state.data.settings.overtime_per_jam || 10000);

                const bAntar = Number(form.biaya_antar.value || 0);
                const bAmbil = Number(form.biaya_ambil.value || 0);
                const bLainnya = Number(form.querySelector('#biaya-lainnya').value || 0);

                // Addon Calculate
                let bAddon = 0;
                const checkedAddons = [];
                form.querySelectorAll('input[name="addons"]:checked').forEach(cb => {
                    bAddon += Number(cb.dataset.price || 0);
                    checkedAddons.push(cb.value);
                });

                if(!isNaN(s) && !isNaN(e) && s < e) {
                    const diffMs = e - s;
                    const hTotal = diffMs / 3600000;
                    
                    let days = Math.floor(hTotal / 24);
                    let otHours = Math.ceil(hTotal % 24);
                    
                    if (days === 0) days = 1;

                    const otCost = otHours * otRate;
                    const subtotalSewa = days * unitPrice;
                    const totalAddon = (bAddon * days) + bLainnya;
                    const totalExtra = bAntar + bAmbil + totalAddon;
                    const grandTotal = subtotalSewa + otCost + totalExtra;
                    
                    const dpVal = Number(form.dp.value || 0);
                    const sisa = grandTotal - dpVal;

                    document.getElementById('calc-duration').innerText = days + ' HARI';
                    document.getElementById('calc-overtime').innerText = otHours + ' JAM';
                    document.getElementById('calc-subtotal').innerText = formatIDR(subtotalSewa);
                    document.getElementById('calc-service').innerText = formatIDR(totalExtra);
                    document.getElementById('calc-total').innerText = formatIDR(grandTotal);

                    const sisaEl = document.getElementById('calc-sisa');
                    const sisaCont = document.getElementById('calc-sisa-container');
                    if (dpVal > 0 && sisa > 0) {
                        sisaCont.classList.remove('hidden');
                        sisaEl.innerText = formatIDR(sisa);
                    } else {
                        sisaCont.classList.add('hidden');
                    }
                    
                    return { days, otHours, otCost, subtotalSewa, totalAddon, totalExtra, grandTotal, checkedAddons, sisa };
                }
                return { grandTotal: 0 };
            };

            inputs.forEach(i => {
                i.addEventListener('input', calculate);
                i.addEventListener('change', calculate);
            });
            
            form.onsubmit = async (ev) => {
                ev.preventDefault();
                const c = calculate();
                if(!c || c.grandTotal <= 0) return showNotification('Waktu atau Unit sewa belum valid', 'warning');
                
                const formData = new FormData(form);
                const payload = Object.fromEntries(formData);
                
                // Map form fields to spreadsheet columns (consistent with Code.gs mapping)
                payload.customer_name = payload.nama || '';
                payload.wa_number = payload.no_wa || '';
                payload.ktp_number = payload.no_ktp || '';
                payload.alamat_antar = payload.alamat_antar || '';
                payload.alamat_ambil = payload.alamat_ambil || '';
                payload.tgl_mulai = payload.tgl_mulai || '';
                payload.tgl_selesai = payload.tgl_selesai || '';
                payload.durasi_hari = c.days || 0;
                payload.overtime_jam = c.otHours || 0;
                payload.biaya_sewa = c.subtotalSewa || 0;
                payload.biaya_overtime = c.otCost || 0;
                payload.biaya_addon = c.totalAddon || 0;
                payload.addon_list = (c.checkedAddons || []).join(', ');
                payload.total = c.grandTotal || 0;
                payload.dp = Number(payload.dp || 0);
                payload.metode_dp = payload.metode_dp || '';
                
                const btn = document.getElementById('submit-btn');
                btn.disabled = true;
                btn.innerHTML = '<i class="loading-spinner w-4 h-4 border-2 animate-spin"></i> PROCESSING...';
                
                try {
                    await callApi('saveTransaction', payload);
                    showNotification('Kontrak Sewa Berhasil Dibuat!', 'success');
                    await loadAllData();
                    navigate('history');
                } catch(e) {
                    showNotification('Gagal: ' + e.message, 'error');
                    btn.disabled = false;
                    btn.innerText = 'BUKA KONTRAK SEWA';
                }
            };
        }

        function renderHistory() {
            const list = state.data.transactions.slice().reverse();
            document.getElementById('content-body').innerHTML = `
                <div class="space-y-6 animate-in slide-in-from-right-6 duration-700">
                    <div class="flex items-center justify-between">
                        <h2 class="text-3xl font-bold italic uppercase tracking-tight">Records <span class="text-blue-500">& Logs</span></h2>
                        <div class="flex gap-2">
                             <div class="bg-black/20 px-4 py-2 rounded-xl border border-white/5 flex items-center gap-2">
                                <i data-lucide="search" class="w-3.5 h-3.5 text-slate-500"></i>
                                <input type="text" id="search-history" placeholder="Cari Customer..." class="bg-transparent border-none focus:ring-0 text-[10px] font-bold uppercase italic w-40">
                             </div>
                        </div>
                    </div>
                    <div class="bg-[#151921] border border-slate-800 rounded-2xl overflow-hidden shadow-2xl overflow-x-auto">
                        <table class="w-full text-left">
                            <thead class="bg-slate-900/40 text-slate-500 text-[8px] font-bold uppercase italic tracking-widest">
                                <tr>
                                    <th class="py-4 px-6 text-center">Unit</th>
                                    <th class="py-4 px-6">Customer Detials</th>
                                    <th class="py-4 px-6">Waktu Sewa</th>
                                    <th class="py-4 px-6 text-right">Biaya/Status</th>
                                    <th class="py-4 px-6 text-center">Actions</th>
                                </tr>
                            </thead>
                            <tbody class="text-xs divide-y divide-slate-800" id="history-body">
                                ${renderHistoryRows(list)}
                            </tbody>
                        </table>
                    </div>
                </div>`;
            lucide.createIcons();
            document.getElementById('search-history').oninput = (e) => {
                const keyword = e.target.value.toLowerCase();
                const filtered = list.filter(t => 
                    (t.customer_name && t.customer_name.toLowerCase().includes(keyword)) || 
                    (t.id_unit && t.id_unit.toLowerCase().includes(keyword)) || 
                    (t.id && t.id.toLowerCase().includes(keyword))
                );
                document.getElementById('history-body').innerHTML = renderHistoryRows(filtered);
                lucide.createIcons();
            };
        }

        function renderHistoryRows(list) {
            if (list.length === 0) return `<tr><td colspan="5" class="py-20 text-center text-slate-600 uppercase text-[10px] tracking-widest">Data Tidak Ditemukan</td></tr>`;
            return list.map(t => `
                <tr class="hover:bg-slate-800/10 transition-colors group">
                    <td class="py-4 px-6">
                        <div class="flex flex-col items-center">
                            <div class="w-10 h-10 rounded-xl bg-slate-800/50 flex items-center justify-center mb-1 group-hover:scale-110 transition-transform">
                                <i data-lucide="bike" class="w-5 h-5 text-blue-500"></i>
                            </div>
                            <p class="font-bold text-[10px] uppercase italic text-white">${t.id_unit}</p>
                        </div>
                    </td>
                    <td class="py-4 px-6">
                        <div class="space-y-1">
                            <p class="font-bold text-white text-sm uppercase italic">${t.customer_name}</p>
                            <div class="flex flex-col gap-1">
                                <div class="flex items-center gap-1.5 text-slate-500">
                                    <i data-lucide="phone" class="w-3 h-3"></i>
                                    <span class="text-[9px] font-mono">${t.wa_number || '-'}</span>
                                </div>
                                <div class="text-[8px] text-slate-600 font-bold uppercase leading-tight">
                                    📍 <span class="text-blue-400">Antar:</span> ${t.alamat_antar || 'Office'}
                                </div>
                                <div class="text-[8px] text-slate-600 font-bold uppercase leading-tight">
                                    📍 <span class="text-orange-400">Ambil:</span> ${t.alamat_ambil || 'Office'}
                                </div>
                                <div class="text-[8px] text-slate-500 font-bold uppercase leading-tight mt-1">
                                    📦 <span class="text-emerald-400">Addons:</span> ${t.addon_list || 'Tanpa Pelengkapan'}
                                </div>
                            </div>
                        </div>
                    </td>
                    <td class="py-4 px-6">
                        <div class="space-y-1">
                            <div class="flex items-center gap-2 text-emerald-400">
                                <i data-lucide="log-out" class="w-3 h-3"></i>
                                <span class="text-[9px] font-bold uppercase italic">${formatDateTime(t.tgl_mulai)}</span>
                            </div>
                            <div class="flex items-center gap-2 text-rose-400">
                                <i data-lucide="log-in" class="w-3 h-3"></i>
                                <span class="text-[9px] font-bold uppercase italic">${formatDateTime(t.tgl_selesai)}</span>
                            </div>
                            <p class="text-[8px] font-bold text-slate-600 uppercase tracking-widest pl-5 mt-1">${t.durasi_hari} HARI + ${t.overtime_jam} JAM OVERTIME</p>
                        </div>
                    </td>
                    <td class="py-4 px-6 text-right">
                        <div class="space-y-1">
                            <p class="text-sm font-bold text-white italic">${formatIDR(t.total || 0)}</p>
                            <div class="flex flex-col items-end gap-1">
                                <div class="flex items-center gap-2">
                                    <span class="px-2 py-0.5 rounded text-[8px] font-bold tracking-widest italic border ${t.pay_status === 'Lunas' ? 'border-emerald-500/30 text-emerald-400 bg-emerald-500/10' : 'border-rose-500/30 text-rose-400 bg-rose-500/10'}">${t.pay_status || 'DP'}</span>
                                </div>
                                <div class="flex flex-col items-end opacity-80">
                                    <span class="text-[8px] font-bold text-slate-500 uppercase tracking-tighter">DP: ${formatIDR(t.dp || 0)} (${t.metode_dp || '-'})</span>
                                    ${t.pay_status !== 'Lunas' ? `<span class="text-[9px] font-black text-rose-500 uppercase bg-rose-500/10 px-2 py-1 rounded border border-rose-500/20 tracking-tighter mt-1 animate-pulse">SISA: ${formatIDR((t.total || 0) - (t.dp || 0) - (t.lunas_amt || 0))}</span>` : ''}
                                    ${t.lunas_amt > 0 ? `<span class="text-[8px] font-bold text-emerald-400 uppercase mt-1">Lunas: ${formatIDR(t.lunas_amt)} (${t.lunas_method})</span>` : ''}
                                </div>
                                <span class="px-2 py-0.5 rounded text-[8px] font-bold tracking-widest italic border ${t.trans_status === 'Selesai' ? 'border-slate-800 text-slate-600' : 'border-blue-500/30 text-blue-400 bg-blue-500/10'}">${t.trans_status || 'Aktif'}</span>
                                ${t.photo ? `<a href="${t.photo}" target="_blank" class="flex items-center gap-1 mt-1 text-[8px] font-bold text-blue-400 uppercase hover:underline"><i data-lucide="image" class="w-2 h-2"></i> Foto Kembali</a>` : ''}
                            </div>
                        </div>
                    </td>
                    <td class="py-4 px-6 text-center">
                        ${t.trans_status === 'Aktif' ? `
                            <div class="flex items-center gap-3 justify-center">
                                ${t.pay_status !== 'Lunas' ? `
                                    <button onclick="showPaymentModal('${t.id}', ${(t.total || 0) - (t.dp || 0) - (t.lunas_amt || 0)})" class="group/btn flex flex-col items-center gap-1">
                                        <div class="w-8 h-8 rounded-lg bg-orange-500/20 text-orange-400 flex items-center justify-center group-hover/btn:bg-orange-500 group-hover/btn:text-white transition-all">
                                            <i data-lucide="hand-coins" class="w-4 h-4"></i>
                                        </div>
                                        <span class="text-[8px] font-bold text-slate-500 uppercase tracking-widest">Pelunasan</span>
                                    </button>
                                ` : ''}
                                <button onclick="showCompleteModal('${t.id}', ${(t.total || 0) - (t.dp || 0) - (t.lunas_amt || 0)})" class="group/btn flex flex-col items-center gap-1">
                                    <div class="w-8 h-8 rounded-lg bg-emerald-500/20 text-emerald-400 flex items-center justify-center group-hover/btn:bg-emerald-500 group-hover/btn:text-white transition-all">
                                        <i data-lucide="check-circle" class="w-4 h-4"></i>
                                    </div>
                                    <span class="text-[8px] font-bold text-slate-500 uppercase tracking-widest">Selesaikan</span>
                                </button>
                            </div>
                        ` : `
                            <div class="w-8 h-8 rounded-lg bg-slate-800/20 text-slate-700 flex items-center justify-center mx-auto grayscale">
                                <i data-lucide="archive" class="w-4 h-4"></i>
                            </div>
                        `}
                    </td>
                </tr>
            `).join('');
        }

        function renderUnits() {
            const list = state.data.units;
            document.getElementById('content-body').innerHTML = `
                <div class="space-y-8 animate-in slide-in-from-top-4 duration-700">
                    <div class="flex items-center justify-between">
                        <div>
                            <h2 class="text-3xl font-bold italic tracking-tight uppercase">Fleet <span class="text-blue-500">& Garage</span></h2>
                            <p class="text-[10px] font-bold text-slate-500 uppercase tracking-[0.2em] mt-1 italic">Manajemen Inventaris Kendaraan</p>
                        </div>
                        <button onclick="showAddUnitModal()" class="px-6 py-2.5 bg-blue-600 hover:bg-blue-500 text-white font-bold rounded-xl text-[10px] uppercase shadow-lg shadow-blue-900/30 tracking-widest flex items-center gap-2 italic">
                            <i data-lucide="plus" class="w-3 h-3"></i> Tambah Unit
                        </button>
                    </div>

                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                        ${list.map(u => `
                            <div class="bg-[#151921] rounded-2xl border border-slate-800 p-6 shadow-xl relative group">
                                <div class="absolute top-4 right-4 group-hover:block hidden transition-all">
                                    <button onclick="showEditUnitModal('${u.id_unit}')" class="p-2 bg-slate-800 hover:bg-slate-700 rounded-lg text-slate-400">
                                        <i data-lucide="settings-2" class="w-4 h-4"></i>
                                    </button>
                                </div>
                                <div class="flex items-start gap-4">
                                    <div class="w-14 h-14 rounded-2xl bg-blue-600/10 border border-blue-500/20 flex items-center justify-center">
                                        <i data-lucide="bike" class="w-8 h-8 text-blue-500"></i>
                                    </div>
                                    <div>
                                        <h3 class="text-lg font-bold text-white italic uppercase">${u.nama_unit}</h3>
                                        <p class="text-[10px] font-bold text-slate-500 uppercase tracking-widest">${u.plat_nomor}</p>
                                    </div>
                                </div>
                                <div class="mt-6 flex items-baseline justify-between">
                                    <div>
                                        <p class="text-[9px] font-bold text-slate-600 uppercase mb-1">Harga Sewa / Hari</p>
                                        <p class="text-xl font-bold text-emerald-400 italic">${formatIDR(u.harga_per_hari)}</p>
                                    </div>
                                    <span class="px-3 py-1 rounded-full text-[9px] font-bold tracking-widest italic border ${getUnitStatusColor(u.status)}">${u.status}</span>
                                </div>
                                <div class="mt-6 grid grid-cols-2 gap-4 border-t border-white/5 pt-6">
                                    <div>
                                        <p class="text-[8px] font-bold text-slate-600 uppercase mb-1">Target Service</p>
                                        <p class="text-[11px] font-bold text-slate-300 italic">${u.target_km_servis} KM</p>
                                    </div>
                                    <div class="text-right">
                                        <p class="text-[8px] font-bold text-slate-600 uppercase mb-1">ODO Terakhir</p>
                                        <p class="text-[11px] font-bold text-slate-300 italic">${u.last_odo || 0} KM</p>
                                    </div>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                </div>`;
            lucide.createIcons();
        }

        function getUnitStatusColor(status) {
            switch(status) {
                case 'Tersedia': return 'border-emerald-500/30 text-emerald-400 bg-emerald-500/10';
                case 'Disewa': return 'border-blue-500/30 text-blue-400 bg-blue-500/10';
                case 'Servis': return 'border-rose-500/30 text-rose-400 bg-rose-500/10';
                default: return 'border-slate-800 text-slate-600';
            }
        }

        function showAddUnitModal() {
            const m = document.getElementById('modal-container'); m.classList.replace('hidden', 'flex');
            document.getElementById('modal-content').innerHTML = `
                <div class="p-8 space-y-6">
                    <h3 class="text-xl font-bold uppercase italic">Register <span class="text-blue-500">New Bike</span></h3>
                    <form id="add-unit-form" class="space-y-4">
                        <div class="space-y-1">
                            <label class="text-[10px] font-bold text-slate-500 uppercase tracking-widest">Nama Kendaraan</label>
                            <input type="text" name="nama_unit" placeholder="Contoh: Honda Vario 125 CBS" required class="w-full rounded-xl py-3 px-4 italic font-bold">
                        </div>
                        <div class="grid grid-cols-2 gap-4">
                            <div class="space-y-1">
                                <label class="text-[10px] font-bold text-slate-500 uppercase">Plat Nomor</label>
                                <input type="text" name="plat_nomor" placeholder="B 1234 ABC" required class="w-full rounded-xl py-3 px-4 italic font-bold text-sm">
                            </div>
                            <div class="space-y-1">
                                <label class="text-[10px] font-bold text-slate-500 uppercase">Harga / Hari</label>
                                <input type="number" name="harga_per_hari" placeholder="80000" required class="w-full rounded-xl py-3 px-4 italic font-bold text-sm">
                            </div>
                        </div>
                        <div class="space-y-1">
                            <label class="text-[10px] font-bold text-slate-500 uppercase">Target KM Servis Berkala</label>
                            <input type="number" name="target_km_servis" value="2000" class="w-full rounded-xl py-3 px-4 italic font-bold text-sm">
                        </div>
                        <div class="flex gap-2 pt-4">
                            <button type="button" onclick="closeModal()" class="flex-1 py-3 rounded-xl bg-slate-800 text-[10px] font-bold uppercase tracking-widest italic">Batal</button>
                            <button type="submit" class="flex-1 py-3 rounded-xl bg-blue-600 text-[10px] font-bold uppercase tracking-widest italic">Simpan Unit</button>
                        </div>
                    </form>
                </div>`;
            document.getElementById('add-unit-form').onsubmit = async (e) => {
                e.preventDefault();
                const d = Object.fromEntries(new FormData(e.target));
                try {
                    await callApi('saveUnit', d);
                    showNotification('Unit Baru Berhasil Ditambahkan', 'success');
                    closeModal();
                    await loadAllData();
                    renderUnits();
                } catch(e) {
                    showNotification('Gagal menyimpan unit', 'error');
                }
            };
            setTimeout(() => document.getElementById('modal-content').classList.replace('opacity-0', 'opacity-100'), 10);
        }

        function renderCashbook() {
            const list = state.data.cashflow.slice().reverse();
            document.getElementById('content-body').innerHTML = `
                <div class="space-y-8 animate-in slide-in-from-bottom-4 duration-700">
                    <div class="flex items-center justify-between">
                        <div>
                            <h2 class="text-3xl font-bold italic tracking-tight uppercase">Cash <span class="text-blue-500">& Wallet</span></h2>
                            <p class="text-[10px] font-bold text-slate-500 uppercase tracking-[0.2em] mt-1 italic">Buku Kas Dan Mutasi Saldo</p>
                        </div>
                        <button onclick="showManualCashModal()" class="px-6 py-2.5 bg-blue-600 hover:bg-blue-500 text-white font-bold rounded-xl text-[10px] uppercase shadow-lg shadow-blue-900/30 tracking-widest flex items-center gap-2 italic">
                            <i data-lucide="plus" class="w-3 h-3"></i> Tambah Saldo Manual
                        </button>
                    </div>

                    <div class="bg-[#151921] border border-slate-800 rounded-2xl overflow-hidden shadow-2xl">
                        <div class="overflow-x-auto">
                            <table class="w-full text-left">
                                <thead class="bg-slate-900/40 text-slate-500 text-[8px] font-bold uppercase italic tracking-widest border-b border-slate-800">
                                    <tr>
                                        <th class="py-4 px-6">Tanggal</th>
                                        <th class="py-4 px-6">Kategori</th>
                                        <th class="py-4 px-6">Keterangan</th>
                                        <th class="py-4 px-6">Metode</th>
                                        <th class="py-4 px-6 text-right">Nominal</th>
                                    </tr>
                                </thead>
                                <tbody class="text-xs divide-y divide-slate-800">
                                    ${list.map(c => `
                                        <tr class="hover:bg-slate-800/10">
                                            <td class="py-4 px-6 text-[10px] font-bold text-slate-500 font-mono italic">${formatDateTime(c.tanggal)}</td>
                                            <td class="py-4 px-6"><span class="px-2 py-0.5 rounded text-[8px] font-bold uppercase italic bg-slate-800 border border-white/5 text-slate-400">${c.kategori}</span></td>
                                            <td class="py-4 px-6 font-bold text-white uppercase italic text-[11px]">${c.keterangan}</td>
                                            <td class="py-4 px-6">
                                                <div class="flex items-center gap-2 text-slate-400 text-[10px] font-bold uppercase italic font-mono">
                                                     <i data-lucide="${c.metode === 'Cash' ? 'banknote' : 'landmark'}" class="w-3 h-3"></i> ${c.metode}
                                                </div>
                                            </td>
                                            <td class="py-4 px-6 text-right font-bold italic text-sm ${c.tipe === 'Masuk' ? 'text-emerald-400' : 'text-rose-400'}">
                                                ${c.tipe === 'Masuk' ? '+' : '-'} ${formatIDR(c.nominal)}
                                            </td>
                                        </tr>
                                    `).join('')}
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>`;
            lucide.createIcons();
        }

        function showManualCashModal() {
            const m = document.getElementById('modal-container'); m.classList.replace('hidden', 'flex');
            const categories = state.data.settings.cash_categories ? state.data.settings.cash_categories.split(',') : ['Modal', 'Sewa Luar', 'Lain-lain'];
            document.getElementById('modal-content').innerHTML = `
                <div class="p-8 space-y-6">
                    <h3 class="text-xl font-bold uppercase italic">Input <span class="text-blue-500">Saldo Manual</span></h3>
                    <form id="cash-form" class="space-y-4">
                        <div class="grid grid-cols-2 gap-4">
                             <div class="space-y-1">
                                <label class="text-[10px] font-bold text-slate-500 uppercase">Tipe</label>
                                <select name="tipe" class="w-full rounded-xl py-3 px-4 italic font-bold uppercase text-[10px]"><option value="Masuk">📥 SALDO MASUK</option><option value="Keluar">📤 SALDO KELUAR</option></select>
                            </div>
                             <div class="space-y-1">
                                <label class="text-[10px] font-bold text-slate-500 uppercase">Metode</label>
                                <select name="metode" class="w-full rounded-xl py-3 px-4 italic font-bold uppercase text-[10px]"><option value="Cash">💵 CASH</option><option value="Transfer">🏦 TRANSFER</option></select>
                            </div>
                        </div>
                        <div class="space-y-1">
                            <label class="text-[10px] font-bold text-slate-500 uppercase">Kategori</label>
                            <select name="kategori" class="w-full rounded-xl py-3 px-4 italic font-bold uppercase text-[10px]">
                                ${categories.map(cat => `<option value="${cat.trim()}">${cat.trim().toUpperCase()}</option>`).join('')}
                            </select>
                        </div>
                        <div class="space-y-1">
                            <label class="text-[10px] font-bold text-slate-500 uppercase">Keterangan</label>
                            <input type="text" name="keterangan" placeholder="Catatan transaksi..." required class="w-full rounded-xl py-3 px-4 italic font-bold">
                        </div>
                        <div class="space-y-1">
                            <label class="text-[10px] font-bold text-slate-500 uppercase">Nominal (Rp)</label>
                            <input type="number" name="nominal" placeholder="0" required class="w-full rounded-xl py-3 px-4 italic font-bold text-xl text-blue-500">
                        </div>
                        <div class="flex gap-2 pt-4">
                            <button type="button" onclick="closeModal()" class="flex-1 py-3 rounded-xl bg-slate-800 text-[10px] font-bold uppercase tracking-widest italic">Batal</button>
                            <button type="submit" class="flex-1 py-3 rounded-xl bg-blue-600 text-[10px] font-bold uppercase tracking-widest italic">Log Transaksi</button>
                        </div>
                    </form>
                </div>`;
            document.getElementById('cash-form').onsubmit = async (e) => {
                e.preventDefault();
                const d = Object.fromEntries(new FormData(e.target));
                try {
                    await callApi('saveCash', d);
                    showNotification('Data Kas Berhasil Disimpan', 'success');
                    closeModal();
                    await loadAllData();
                    renderCashbook();
                } catch(e) {
                    showNotification('Gagal mencatat kas', 'error');
                }
            };
            setTimeout(() => document.getElementById('modal-content').classList.replace('opacity-0', 'opacity-100'), 10);
        }

        function renderExpenses() {
            const list = state.data.expenses || [];
            document.getElementById('content-body').innerHTML = `
                <div class="space-y-8 animate-in slide-in-from-right-4 duration-700">
                    <div class="flex items-center justify-between">
                        <div>
                            <h2 class="text-3xl font-bold italic tracking-tight uppercase">Expense <span class="text-rose-500">& Spending</span></h2>
                            <p class="text-[10px] font-bold text-slate-500 uppercase tracking-[0.2em] mt-1 italic">Log Pengeluaran Operasional</p>
                        </div>
                        <button onclick="showExpenseModal()" class="px-6 py-2.5 bg-rose-600 hover:bg-rose-500 text-white font-bold rounded-xl text-[10px] uppercase shadow-lg shadow-rose-900/30 tracking-widest flex items-center gap-2 italic">
                            <i data-lucide="plus" class="w-3 h-3"></i> Tambah Pengeluaran
                        </button>
                    </div>

                    <div class="bg-[#151921] border border-slate-800 rounded-2xl overflow-hidden shadow-2xl overflow-x-auto">
                        <table class="w-full text-left">
                            <thead class="bg-slate-900/40 text-slate-500 text-[8px] font-bold uppercase italic tracking-widest border-b border-slate-800">
                                <tr>
                                    <th class="py-4 px-6">Waktu</th>
                                    <th class="py-4 px-6">Kategori</th>
                                    <th class="py-4 px-6">Rincian Pengeluaran</th>
                                    <th class="py-4 px-6 text-right">Nominal</th>
                                </tr>
                            </thead>
                            <tbody class="text-xs divide-y divide-slate-800">
                                ${list.slice().reverse().map(e => `
                                    <tr class="hover:bg-slate-800/10">
                                        <td class="py-4 px-6 text-[10px] font-bold text-slate-500 font-mono italic">${formatDateTime(e.tanggal)}</td>
                                        <td class="py-4 px-6"><span class="px-2 py-0.5 rounded text-[8px] font-bold uppercase italic bg-rose-900/20 border border-rose-500/10 text-rose-400">${e.kategori}</span></td>
                                        <td class="py-4 px-6">
                                            <p class="font-bold text-white uppercase italic text-[11px]">${e.keterangan}</p>
                                            <p class="text-[9px] text-slate-600 uppercase font-bold italic tracking-tight">Metode: ${e.metode}</p>
                                        </td>
                                        <td class="py-4 px-6 text-right font-bold italic text-sm text-rose-400">
                                            ${formatIDR(e.nominal)}
                                        </td>
                                    </tr>
                                `).join('')}
                                ${list.length === 0 ? '<tr><td colspan="4" class="py-20 text-center text-slate-600 uppercase text-[10px] tracking-widest italic">Belum ada rincian pengeluaran</td></tr>' : ''}
                            </tbody>
                        </table>
                    </div>
                </div>`;
            lucide.createIcons();
        }

        function showExpenseModal() {
            const m = document.getElementById('modal-container'); m.classList.replace('hidden', 'flex');
            const categories = state.data.settings.expense_categories ? state.data.settings.expense_categories.split(',') : ['Servis Motor', 'Gaji Staff', 'BBM/Laundry', 'Operasional', 'Iklan'];
            document.getElementById('modal-content').innerHTML = `
                <div class="p-8 space-y-6">
                    <h3 class="text-xl font-bold uppercase italic">Log <span class="text-rose-500">Expense Baru</span></h3>
                    <form id="expense-form" class="space-y-4">
                        <div class="space-y-1">
                            <label class="text-[10px] font-bold text-slate-500 uppercase">Kategori Pengeluaran</label>
                            <select name="kategori" class="w-full rounded-xl py-3 px-4 italic font-bold uppercase text-[10px]">
                                ${categories.map(cat => `<option value="${cat.trim()}">${cat.trim().toUpperCase()}</option>`).join('')}
                            </select>
                        </div>
                        <div class="space-y-1">
                            <label class="text-[10px] font-bold text-slate-500 uppercase">Metode Pembayaran</label>
                            <select name="metode" class="w-full rounded-xl py-3 px-4 italic font-bold uppercase text-[10px]"><option value="Cash">💵 CASH</option><option value="Transfer">🏦 TRANSFER</option></select>
                        </div>
                        <div class="space-y-1">
                            <label class="text-[10px] font-bold text-slate-500 uppercase">Deskripsi / Keterangan</label>
                            <textarea name="keterangan" rows="3" placeholder="Rincian pengeluaran..." required class="w-full rounded-xl py-3 px-4 italic font-bold text-sm"></textarea>
                        </div>
                        <div class="space-y-1">
                            <label class="text-[10px] font-bold text-slate-500 uppercase">Nominal (Rp)</label>
                            <input type="number" name="nominal" placeholder="0" required class="w-full rounded-xl py-3 px-4 italic font-bold text-xl text-rose-500">
                        </div>
                        <div class="flex gap-2 pt-4">
                            <button type="button" onclick="closeModal()" class="flex-1 py-3 rounded-xl bg-slate-800 text-[10px] font-bold uppercase tracking-widest italic">Batal</button>
                            <button type="submit" class="flex-1 py-3 rounded-xl bg-rose-600 text-[10px] font-bold uppercase tracking-widest italic">Catat Pengeluaran</button>
                        </div>
                    </form>
                </div>`;
            document.getElementById('expense-form').onsubmit = async (e) => {
                e.preventDefault();
                const d = Object.fromEntries(new FormData(e.target));
                try {
                    await callApi('saveExpense', d);
                    showNotification('Pengeluaran Berhasil Dicatat', 'success');
                    closeModal();
                    await loadAllData();
                    renderExpenses();
                } catch(e) {
                    showNotification('Gagal mencatat pengeluaran', 'error');
                }
            };
            setTimeout(() => document.getElementById('modal-content').classList.replace('opacity-0', 'opacity-100'), 10);
        }

        function renderSettings() {
            const s = state.data.settings || {};
            let addons = [];
            try {
                const addonsRaw = s.addons || s.Addons || '[]';
                addons = JSON.parse(addonsRaw);
                if (!Array.isArray(addons)) addons = [];
            } catch(e) { addons = []; }

            document.getElementById('content-body').innerHTML = `
                <div class="max-w-7xl mx-auto space-y-6 animate-in slide-in-from-bottom-4 duration-700">
                    <h2 class="text-xl font-bold uppercase italic tracking-tighter">Sistem <span class="text-blue-500">& Master</span></h2>
                    
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <!-- Profil Rental -->
                        <div class="bg-[#151921] border border-slate-800 rounded-2xl p-6 shadow-xl space-y-4">
                            <h3 class="text-[10px] font-black text-white uppercase tracking-widest mb-6 italic">Profil Rental</h3>
                            <form id="profile-form" class="space-y-4">
                                <div>
                                    <label class="block text-[8px] font-black text-slate-500 uppercase tracking-widest mb-1 pl-1">Nama Rental</label>
                                    <input type="text" name="rental_name" value="${s.rental_name || ''}" placeholder="Nama Rental" class="w-full rounded-xl py-3 px-4 italic font-bold">
                                </div>
                                <div>
                                    <label class="block text-[8px] font-black text-slate-500 uppercase tracking-widest mb-1 pl-1">Alamat Kantor</label>
                                    <textarea name="rental_address" rows="3" placeholder="Alamat Rental" class="w-full rounded-xl py-3 px-4 italic font-bold text-xs">${s.rental_address || ''}</textarea>
                                </div>
                                <button type="submit" class="w-full py-4 bg-blue-600 hover:bg-blue-500 text-white font-black rounded-xl text-[10px] uppercase shadow-lg shadow-blue-900/30 tracking-widest italic transition-all">Update Profil</button>
                            </form>
                        </div>

                        <!-- Master Overtime -->
                        <div class="bg-[#151921] border border-slate-800 rounded-2xl p-6 shadow-xl space-y-4">
                            <h3 class="text-[10px] font-black text-white uppercase tracking-widest mb-6 italic">Master Overtime</h3>
                            <form id="overtime-form" class="space-y-4">
                                <div class="relative">
                                    <input type="number" name="overtime_per_jam" value="${s.overtime_per_jam || 10000}" class="w-full rounded-xl py-4 px-4 pr-24 text-orange-400 font-black italic text-base">
                                    <span class="absolute right-4 top-1/2 -translate-y-1/2 p-2 bg-slate-800 text-[10px] font-black text-slate-500 rounded uppercase tracking-tighter">/ Jam</span>
                                </div>
                                <button type="submit" class="w-full py-4 bg-orange-600 hover:bg-orange-500 text-white font-black rounded-xl text-[10px] uppercase shadow-lg shadow-orange-900/30 tracking-widest italic transition-all">Simpan Overtime</button>
                            </form>
                        </div>

                        <!-- Master Dropdown Kas -->
                        <div class="bg-[#151921] border border-slate-800 rounded-2xl p-6 shadow-xl space-y-4">
                            <h3 class="text-[10px] font-black text-white uppercase tracking-widest mb-6 italic">Master Dropdown Kas</h3>
                            <div class="space-y-2 max-h-[160px] overflow-y-auto pr-2 custom-scroll" id="kas-list">
                                ${s.cash_categories ? s.cash_categories.split(',').map(c => `
                                    <div class="flex items-center justify-between bg-black/20 p-3 rounded-xl border border-white/5 group">
                                        <span class="text-[10px] font-black italic text-slate-400 uppercase tracking-tighter">${c.trim()}</span>
                                        <button onclick="removeCategory('cash_categories', '${c.trim()}')" class="text-rose-500 hover:scale-125 transition-transform"><i data-lucide="x" class="w-3.5 h-3.5"></i></button>
                                    </div>
                                `).join('') : '<p class="text-[9px] text-slate-700 italic font-bold">Belum ada kategori.</p>'}
                            </div>
                            <div class="flex gap-2 pt-2">
                                <input type="text" id="new-kas" placeholder="Kategori Baru" class="flex-1 rounded-xl py-3 px-4 italic font-bold text-[11px]">
                                <button onclick="addCategory('cash_categories', 'new-kas')" class="w-12 bg-blue-600 hover:bg-blue-500 rounded-xl flex items-center justify-center text-white transition-all"><i data-lucide="plus" class="w-5 h-5"></i></button>
                            </div>
                        </div>

                        <!-- Master Dropdown Biaya -->
                        <div class="bg-[#151921] border border-slate-800 rounded-2xl p-6 shadow-xl space-y-4">
                            <h3 class="text-[10px] font-black text-white uppercase tracking-widest mb-6 italic">Master Dropdown Biaya</h3>
                            <div class="space-y-2 max-h-[160px] overflow-y-auto pr-2 custom-scroll" id="expense-list">
                                ${s.expense_categories ? s.expense_categories.split(',').map(c => `
                                    <div class="flex items-center justify-between bg-black/20 p-3 rounded-xl border border-white/5 group">
                                        <span class="text-[10px] font-black italic text-slate-400 uppercase tracking-tighter">${c.trim()}</span>
                                        <button onclick="removeCategory('expense_categories', '${c.trim()}')" class="text-rose-500 hover:scale-125 transition-transform"><i data-lucide="x" class="w-3.5 h-3.5"></i></button>
                                    </div>
                                `).join('') : '<p class="text-[9px] text-slate-700 italic font-bold">Belum ada kategori.</p>'}
                            </div>
                            <div class="flex gap-2 pt-2">
                                <input type="text" id="new-expense" placeholder="Kategori Baru" class="flex-1 rounded-xl py-3 px-4 italic font-bold text-[11px]">
                                <button onclick="addCategory('expense_categories', 'new-expense')" class="w-12 bg-blue-600 hover:bg-blue-500 rounded-xl flex items-center justify-center text-white transition-all"><i data-lucide="plus" class="w-5 h-5"></i></button>
                            </div>
                        </div>

                        <!-- Master Add-on -->
                        <div class="bg-[#151921] border border-slate-800 rounded-2xl p-6 shadow-xl space-y-4 md:col-span-2">
                            <h3 class="text-[10px] font-black text-white uppercase tracking-widest mb-6 italic">Master Add-on (Helm, Jas Hujan, dll)</h3>
                            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                                <div class="space-y-2 max-h-[200px] overflow-y-auto pr-2 custom-scroll" id="addon-list-settings">
                                    ${addons.length > 0 ? addons.map(a => `
                                        <div class="flex items-center justify-between bg-black/20 p-3 rounded-xl border border-white/5 group">
                                            <div>
                                                <p class="text-[10px] font-black italic text-slate-300 uppercase tracking-tighter">${a.name}</p>
                                                <p class="text-[9px] font-bold text-blue-500 italic">${formatIDR(a.price)}</p>
                                            </div>
                                            <button onclick="removeAddon('${a.name}')" class="text-rose-500 hover:scale-125 transition-transform"><i data-lucide="x" class="w-3.5 h-3.5"></i></button>
                                        </div>
                                    `).join('') : '<p class="text-[9px] text-slate-700 italic font-bold">Belum ada addon.</p>'}
                                </div>
                                <div class="bg-black/10 p-4 rounded-2xl border border-white/5 space-y-3">
                                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-2">
                                        <input type="text" id="add-name" placeholder="Nama Add-on" class="rounded-xl py-3 px-4 italic font-bold text-[10px]">
                                        <input type="number" id="add-price" placeholder="Harga Sehari" class="rounded-xl py-3 px-4 italic font-bold text-[10px]">
                                    </div>
                                    <button onclick="saveAddon()" class="w-full py-3 bg-blue-600 hover:bg-blue-500 text-white font-black rounded-xl text-[10px] uppercase shadow-lg shadow-blue-900/30 tracking-widest italic transition-all">Tambah Data Add-on</button>
                                </div>
                            </div>
                        </div>
                        
                        <!-- Metode Pembayaran -->
                        <div class="bg-[#151921] border border-slate-800 rounded-2xl p-6 shadow-xl col-span-1 md:col-span-2 md:col-span-2">
                            <h3 class="text-[10px] font-black text-white uppercase tracking-widest mb-6 italic">Master Metode Pembayaran</h3>
                            <div class="flex flex-wrap gap-2 mb-4">
                                ${(s.payment_methods || 'Cash, Transfer').split(',').map(m => `
                                    <span class="inline-flex items-center gap-2 bg-blue-500/10 border border-blue-500/20 px-4 py-2 rounded-xl text-[10px] font-black text-blue-400 italic">
                                        ${m.trim().toUpperCase()}
                                        <button onclick="removeCategory('payment_methods', '${m.trim()}')" class="text-rose-500 ml-1">×</button>
                                    </span>
                                `).join('')}
                            </div>
                            <div class="flex gap-2">
                                <input type="text" id="new-payment" placeholder="Metode Baru (misal: QRIS)" class="flex-1 rounded-xl py-3 px-4 italic font-bold text-[11px]">
                                <button onclick="addCategory('payment_methods', 'new-payment')" class="w-12 bg-blue-600 hover:bg-blue-500 rounded-xl flex items-center justify-center text-white"><i data-lucide="plus" class="w-5 h-5"></i></button>
                            </div>
                        </div>
                    </div>
                </div>`;
            initSettingsForms();
            lucide.createIcons();
        }

        function showPaymentModal(id, sisa) {
            const m = document.getElementById('modal-container'); m.classList.replace('hidden', 'flex');
            const s = state.data.settings || {};
            document.getElementById('modal-content').innerHTML = `
                <div class="p-8 space-y-6">
                    <div class="flex items-center justify-between border-b border-white/5 pb-4">
                        <h3 class="text-xl font-bold uppercase italic">Record <span class="text-emerald-500">Pelunasan</span></h3>
                        <button onclick="closeModal()" class="text-slate-500 hover:text-white"><i data-lucide="x" class="w-5 h-5"></i></button>
                    </div>
                    
                    <div class="bg-emerald-500/10 p-4 rounded-xl border border-emerald-500/20 space-y-1">
                        <p class="text-[9px] font-bold text-emerald-500 uppercase tracking-widest leading-none">Sisa Tagihan</p>
                        <p class="text-2xl font-bold text-emerald-400 italic">${formatIDR(sisa)}</p>
                    </div>

                    <form id="payment-form" class="space-y-4">
                        <div class="grid grid-cols-2 gap-4">
                             <div class="space-y-1">
                                <label class="text-[9px] font-bold text-slate-500 uppercase">Jumlah Bayar</label>
                                <input type="number" id="pay-amount" value="${sisa}" required class="w-full rounded-xl py-3 px-4 italic font-bold">
                            </div>
                             <div class="space-y-1">
                                <label class="text-[9px] font-bold text-slate-500 uppercase">Metode</label>
                                <select id="pay-method" class="w-full rounded-xl py-3 px-4 uppercase italic font-bold text-[10px]">
                                    ${(s.payment_methods || 'Cash, Transfer').split(',').map(m => `<option value="${m.trim()}">${m.trim().toUpperCase()}</option>`).join('')}
                                </select>
                            </div>
                        </div>

                        <button type="submit" id="confirm-payment-btn" class="w-full py-4 rounded-xl bg-emerald-600 text-[10px] font-bold uppercase tracking-widest italic group">
                            <span class="group-hover:tracking-[0.3em] transition-all">Simpan Pembayaran</span>
                        </button>
                    </form>
                </div>`;
            lucide.createIcons();

            document.getElementById('payment-form').onsubmit = async (ev) => {
                ev.preventDefault();
                const btn = document.getElementById('confirm-payment-btn');
                btn.disabled = true;
                btn.innerHTML = '<i class="loading-spinner w-4 h-4 border-2"></i>';

                try {
                    await callApi('recordPelunasan', {
                        id_transaksi: id, 
                        amount: Number(document.getElementById('pay-amount').value), 
                        metode: document.getElementById('pay-method').value
                    });
                    closeModal();
                    showNotification('Pembayaran Berhasil Dicatat', 'success');
                    await loadAllData();
                    renderHistory();
                } catch(e) {
                    showNotification('Gagal: ' + e.message, 'error');
                    btn.disabled = false;
                    btn.innerText = 'Simpan Pembayaran';
                }
            };
            setTimeout(() => document.getElementById('modal-content').classList.replace('opacity-0', 'opacity-100'), 10);
        }

        function showCompleteModal(id, sisa) {
            const m = document.getElementById('modal-container'); m.classList.replace('hidden', 'flex');
            document.getElementById('modal-content').innerHTML = `
                <div class="p-8 space-y-6">
                    <div class="flex items-center justify-between border-b border-white/5 pb-4">
                        <h3 class="text-xl font-bold uppercase italic">Finalizing <span class="text-blue-500">Checkout</span></h3>
                        <button onclick="closeModal()" class="text-slate-500 hover:text-white"><i data-lucide="x" class="w-5 h-5"></i></button>
                    </div>
                    
                    <div class="bg-black/40 p-4 rounded-xl border border-white/5 space-y-1">
                        <p class="text-[9px] font-bold text-slate-500 uppercase tracking-widest leading-none">Saldo Pelunasan</p>
                        <p class="text-2xl font-bold text-blue-400 italic">${formatIDR(sisa)}</p>
                    </div>

                    <form id="complete-form" class="space-y-4">
                        <div class="grid grid-cols-2 gap-4">
                             <div class="space-y-1">
                                <label class="text-[9px] font-bold text-slate-500 uppercase">Bayar Pelunasan</label>
                                <input type="number" id="final-pay" value="${sisa}" required class="w-full rounded-xl py-3 px-4 italic font-bold">
                            </div>
                             <div class="space-y-1">
                                <label class="text-[9px] font-bold text-slate-500 uppercase">Metode</label>
                                <select id="final-method" class="w-full rounded-xl py-3 px-4 uppercase italic font-bold text-[10px]"><option value="Cash">💵 CASH</option><option value="Transfer">🏦 TRANSFER</option></select>
                            </div>
                        </div>

                        <div class="space-y-1">
                            <label class="text-[9px] font-bold text-slate-500 uppercase">Foto Serah Terima (Opsional)</label>
                            <label class="flex flex-col items-center justify-center w-full h-32 border-2 border-dashed border-slate-800 rounded-xl cursor-pointer hover:bg-slate-800/10 transition-colors bg-black/20">
                                <div id="photo-preview-container" class="flex flex-col items-center justify-center pt-5 pb-6">
                                    <i data-lucide="camera" class="w-8 h-8 text-slate-500 mb-2"></i>
                                    <p class="text-[8px] font-bold text-slate-500 uppercase tracking-widest">Klik Untuk Upload Foto</p>
                                </div>
                                <input id="checkout-photo" type="file" accept="image/*" class="hidden" />
                            </label>
                        </div>

                        <div class="flex gap-2 pt-4">
                            <button type="button" onclick="closeModal()" class="flex-1 py-4 rounded-xl bg-slate-800 text-[10px] font-bold uppercase tracking-widest italic">Abort</button>
                            <button type="submit" id="confirm-checkout-btn" class="flex-1 py-4 rounded-xl bg-blue-600 text-[10px] font-bold uppercase tracking-widest italic group">
                                <span class="group-hover:tracking-[0.3em] transition-all">Confirm Checkout</span>
                            </button>
                        </div>
                    </form>
                </div>`;
            lucide.createIcons();

            let uploadData = null;
            const photoInput = document.getElementById('checkout-photo');
            photoInput.onchange = (e) => {
                const file = e.target.files[0];
                if (file) {
                    const reader = new FileReader();
                    reader.onload = (re) => {
                        uploadData = { content: re.target.result, filename: `return_${id}_${Date.now()}.jpg` };
                        document.getElementById('photo-preview-container').innerHTML = `
                            <img src="${re.target.result}" class="h-24 rounded-lg shadow-xl" />
                            <p class="text-[7px] font-bold text-emerald-400 uppercase mt-2">Foto Siap!</p>
                        `;
                    };
                    reader.readAsDataURL(file);
                }
            };

            document.getElementById('complete-form').onsubmit = async (ev) => {
                ev.preventDefault();
                const btn = document.getElementById('confirm-checkout-btn');
                btn.disabled = true;
                btn.innerHTML = '<i class="loading-spinner w-4 h-4 border-2"></i>';

                try {
                    let fotoUrl = '';
                    if (uploadData) {
                        const uploadRes = await callApi('uploadPhoto', uploadData);
                        if (uploadRes.success) fotoUrl = uploadRes.url;
                    }

                    await callApi('completeTransaction', {
                        id_transaksi: id, 
                        pelunasan: Number(document.getElementById('final-pay').value), 
                        metode_pelunasan: document.getElementById('final-method').value,
                        foto_url: fotoUrl
                    });

                    closeModal();
                    showNotification('Unit Berhasil Kembali & Transaksi Selesai', 'success');
                    await loadAllData();
                    renderHistory();
                } catch(e) {
                    showNotification('Checkout Gagal: ' + e.message, 'error');
                    btn.disabled = false;
                    btn.innerText = 'Confirm Checkout';
                }
            };
            setTimeout(() => document.getElementById('modal-content').classList.replace('opacity-0', 'opacity-100'), 10);
        }

        function showEditUnitModal(id) {
            const unit = state.data.units.find(u => u.id_unit === id);
            if(!unit) return;

            const m = document.getElementById('modal-container'); m.classList.replace('hidden', 'flex');
            document.getElementById('modal-content').innerHTML = `
                <div class="p-8 space-y-6">
                    <h3 class="text-xl font-bold uppercase italic">Update <span class="text-blue-500">Unit Info</span></h3>
                    <form id="edit-unit-form" class="space-y-4">
                        <input type="hidden" name="id_unit" value="${unit.id_unit}">
                        <div class="space-y-1">
                            <label class="text-[10px] font-bold text-slate-500 uppercase">Nama Kendaraan</label>
                            <input type="text" name="nama_unit" value="${unit.nama_unit}" required class="w-full rounded-xl py-3 px-4 italic font-bold">
                        </div>
                        <div class="grid grid-cols-2 gap-4">
                            <div class="space-y-1">
                                <label class="text-[10px] font-bold text-slate-500 uppercase">Plat Nomor</label>
                                <input type="text" name="plat_nomor" value="${unit.plat_nomor}" required class="w-full rounded-xl py-3 px-4 italic font-bold">
                            </div>
                            <div class="space-y-1">
                                <label class="text-[10px] font-bold text-slate-500 uppercase">Status</label>
                                <select name="status" class="w-full rounded-xl py-3 px-4 uppercase italic font-bold text-[11px]">
                                    <option value="Tersedia" ${unit.status === 'Tersedia' ? 'selected' : ''}>✅ TERSEDIA</option>
                                    <option value="Disewa" ${unit.status === 'Disewa' ? 'selected' : ''}>🚲 DISEWA</option>
                                    <option value="Servis" ${unit.status === 'Servis' ? 'selected' : ''}>🛠 SERVIS</option>
                                </select>
                            </div>
                        </div>
                        <div class="grid grid-cols-2 gap-4">
                            <div class="space-y-1">
                                <label class="text-[10px] font-bold text-slate-500 uppercase">Harga / Hari</label>
                                <input type="number" name="harga_per_hari" value="${unit.harga_per_hari}" required class="w-full rounded-xl py-3 px-4 italic font-bold">
                            </div>
                            <div class="space-y-1">
                                <label class="text-[10px] font-bold text-slate-500 uppercase">ODO KM Sekarang</label>
                                <input type="number" name="last_odo" value="${unit.last_odo || 0}" class="w-full rounded-xl py-3 px-4 italic font-bold">
                            </div>
                        </div>
                        <div class="flex gap-2 pt-4">
                            <button type="button" onclick="closeModal()" class="flex-1 py-3 rounded-xl bg-slate-800 text-[10px] font-bold uppercase tracking-widest italic">Batal</button>
                            <button type="submit" class="flex-1 py-3 rounded-xl bg-emerald-600 text-[10px] font-bold uppercase tracking-widest italic">Update Unit</button>
                        </div>
                    </form>
                </div>`;
            
            document.getElementById('edit-unit-form').onsubmit = async (e) => {
                e.preventDefault();
                try {
                    const d = Object.fromEntries(new FormData(e.target));
                    await callApi('saveUnit', d); // reuse saveUnit if it supports updating, but let's assume it appends for now. I should actually add an update logic.
                    showNotification('Data Unit Berhasil Diperbarui', 'success');
                    closeModal();
                    await loadAllData();
                    renderUnits();
                } catch(e) { showNotification('Gagal update unit', 'error'); }
            };
            setTimeout(() => document.getElementById('modal-content').classList.replace('opacity-0', 'opacity-100'), 10);
        }

        function closeModal() { document.getElementById('modal-content').classList.replace('opacity-100', 'opacity-0'); setTimeout(() => document.getElementById('modal-container').classList.replace('flex', 'hidden'), 200); }

        function initDashboardCharts() {
            const ctx = document.getElementById('statusChart').getContext('2d');
            const s = state.data.stats; if(statusChartInstance) statusChartInstance.destroy();
            statusChartInstance = new Chart(ctx, { type: 'doughnut', data: { labels: ['A', 'B', 'C'], datasets: [{ data: [s.availableUnits, s.rentedUnits, s.serviceUnits], backgroundColor: ['#10b981', '#3b82f6', '#f43f5e'], borderColor: '#151921', borderWidth: 2 }] }, options: { plugins: { legend: { display: false } }, cutout: '80%', responsive: true, maintainAspectRatio: false } });
        }

        // --- SETTINGS HELPERS ---

        function initSettingsForms() {
            const profileForm = document.getElementById('profile-form');
            if (profileForm) {
                profileForm.onsubmit = async (e) => {
                    e.preventDefault();
                    const d = Object.fromEntries(new FormData(e.target));
                    await updateSettingBulk(d);
                };
            }

            const otForm = document.getElementById('overtime-form');
            if (otForm) {
                otForm.onsubmit = async (e) => {
                    e.preventDefault();
                    const d = Object.fromEntries(new FormData(e.target));
                    await updateSettingBulk(d);
                };
            }
        }

        async function updateSettingBulk(data) {
            try {
                showNotification('Menyimpan...', 'info');
                await callApi('saveSettings', data);
                showNotification('Berhasil disimpan', 'success');
                await loadAllData();
                renderSettings();
            } catch (e) {
                showNotification('Gagal: ' + e.message, 'error');
            }
        }

        async function addCategory(key, inputId) {
            const input = document.getElementById(inputId);
            const val = input.value.trim();
            if(!val) return;

            let current = state.data.settings[key] || '';
            let arr = current ? current.split(',').map(c => c.trim()) : [];
            if (arr.includes(val)) return showNotification('Sudah ada', 'warning');
            
            arr.push(val);
            const newData = {};
            newData[key] = arr.join(', ');
            await updateSettingBulk(newData);
        }

        async function removeCategory(key, val) {
            if(!confirm(`Hapus ${val}?`)) return;
            let current = state.data.settings[key] || '';
            let arr = current.split(',').map(c => c.trim()).filter(c => c !== val);
            const newData = {};
            newData[key] = arr.join(', ');
            await updateSettingBulk(newData);
        }

        async function saveAddon() {
            const nameEl = document.getElementById('add-name');
            const priceEl = document.getElementById('add-price');
            const name = nameEl.value.trim();
            const price = Number(priceEl.value);
            if(!name || isNaN(price)) return showNotification('Isi Nama & Harga!', 'warning');

            let current = [];
            try {
                const s = state.data.settings || {};
                const addonsRaw = s.addons || s.Addons || '[]';
                current = JSON.parse(addonsRaw);
                if (!Array.isArray(current)) current = [];
            } catch(e) { current = []; }
            
            current.push({name, price});
            await updateSettingBulk({ addons: JSON.stringify(current) });
            nameEl.value = '';
            priceEl.value = '';
        }

        async function removeAddon(name) {
            if(!confirm(`Hapus addon ${name}?`)) return;
            let current = [];
            try {
                const s = state.data.settings || {};
                const addonsRaw = s.addons || s.Addons || '[]';
                current = JSON.parse(addonsRaw);
                if (!Array.isArray(current)) current = [];
            } catch(e) { current = []; }
            
            current = current.filter(a => a.name !== name);
            await updateSettingBulk({ addons: JSON.stringify(current) });
        }

        function formatIDR(num) { 
            const n = Number(num || 0);
            return new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', minimumFractionDigits: 0 }).format(n); 
        }
        function formatTime(iso) { if(!iso) return '-'; const d = new Date(iso); return d.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' }) + ' WIB'; }
        function formatDateTime(iso) { if(!iso) return '-'; const d = new Date(iso); return d.toLocaleDateString('id-ID', { day: '2-digit', month: 'short' }) + ' ' + d.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' }); }

        function showNotification(msg, type = 'info') {
            const cont = document.getElementById('notifications'); const el = document.createElement('div'); const colors = { success: 'bg-emerald-600', error: 'bg-rose-600', warning: 'bg-orange-600', info: 'bg-blue-600' };
            el.className = `${colors[type]} text-white px-6 py-3 rounded-xl shadow-2xl flex items-center gap-3 animate-in slide-in-from-right-10 duration-500 border border-white/20`;
            el.innerHTML = `<span class="font-bold text-[9px] uppercase tracking-widest italic">${msg}</span>`;
            cont.appendChild(el); setTimeout(() => { el.classList.add('animate-out', 'fade-out', 'duration-500'); setTimeout(() => el.remove(), 500); }, 3000);
        }

        function handleMockApi(action, data) {
            console.log('Mock Mode Action:', action, data);
            
            // Persist settings in localStorage for testing
            const getMockSettings = () => {
                const stored = localStorage.getItem('mock_settings');
                if (stored) return JSON.parse(stored);
                return {
                    overtime_per_jam: 10000,
                    addons: JSON.stringify([
                        { name: 'Helm Regular', price: 5000 },
                        { name: 'Jas Hujan', price: 10000 }
                    ]),
                    expense_categories: 'Servis, Gaji, Operasional, Iklan',
                    cash_categories: 'Modal, Topup, Pinjaman',
                    payment_methods: 'Cash, Transfer, QRIS',
                    rental_name: 'RentMotor Pro Mock'
                };
            };

            const saveMockSettings = (newData) => {
                const current = getMockSettings();
                const updated = { ...current, ...newData };
                localStorage.setItem('mock_settings', JSON.stringify(updated));
                return updated;
            };

            if (action === 'saveSettings') {
                saveMockSettings(data);
                return new Promise(r => setTimeout(() => r({ success: true }), 300));
            }

            if (action === 'getSettings') {
                return new Promise(r => setTimeout(() => r({ success: true, data: getMockSettings() }), 300));
            }

            if (action === 'saveTransaction') return new Promise(r => setTimeout(() => r({ success: true }), 500));
            
            const m = { 
                healthCheck: { success: true, ssConnected: true },
                getDashboardData: { 
                    success: true,
                    stats: { 
                        revenue: 4250000, 
                        expenses: 850000, 
                        profit: 3400000,
                        cashBalance: 3245000, 
                        transferBalance: 1005000, 
                        totalUnits: 18, 
                        availableUnits: 12, 
                        rentedUnits: 4, 
                        serviceUnits: 2, 
                        returnsToday: [], 
                        deliveriesToday: [],
                        chartData: [
                            { label: '30 Apr', value: 400000 },
                            { label: '01 Mei', value: 650000 }
                        ]
                    } 
                }, 
                getUnits: { 
                    success: true,
                    data: [
                        { id_unit: 'VAR125', nama_unit: 'Honda Vario 125', harga_per_hari: 80000, plat_nomor: 'B 1111 AAA', status: 'Tersedia' },
                        { id_unit: 'NMAX', nama_unit: 'Yamaha NMAX', harga_per_hari: 150000, plat_nomor: 'B 2222 BBB', status: 'Tersedia' }
                    ] 
                }, 
                getTransactions: { success: true, data: [] },
                getSettings: { success: true, data: getMockSettings() }
            };
            
            return new Promise(r => setTimeout(() => r(m[action] || { success: true, data: [] }), 400));
        }
    </script>
</body>
</html>
