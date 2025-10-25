<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>FormFlow - Sistema de Entregas</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Lucide Icons CDN -->
  <script src="https://unpkg.com/lucide@latest"></script>
  <!-- jsPDF CDN -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <!-- jsPDF AutoTable CDN for improved PDF tables -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.25/jspdf.plugin.autotable.min.js"></script>
  <style>
    /* Custom styles for gradients and animations */
    .bg-gradient-to-br {
      background-image: linear-gradient(to bottom right, var(--tw-gradient-stops));
    }
    .bg-gradient-to-r {
      background-image: linear-gradient(to right, var(--tw-gradient-stops));
    }
    .animate-pulse {
      animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
    }
    @keyframes pulse {
      0%, 100% { opacity: 1; }
      50% { opacity: 0.5; }
    }
    .fade-in {
      animation: fadeIn 0.3s ease-in;
    }
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(20px); }
      to { opacity: 1; transform: translateY(0); }
    }
    canvas {
      touch-action: none;
    }
    /* Improved mobile styles */
    @media (max-width: 768px) {
      main {
        padding: 1rem;
      }
      #progress-steps {
        overflow-x: auto;
        justify-content: flex-start;
      }
      #progress-steps > div {
        min-width: 3rem;
      }
      .grid-cols-1 {
        grid-template-columns: repeat(1, minmax(0, 1fr));
      }
    }
  </style>
</head>
<body class="min-h-screen bg-gradient-to-br from-gray-50 via-blue-50 to-indigo-50">
  <div class="flex w-full min-h-screen">
    <!-- Sidebar -->
    <aside class="w-64 bg-white border-r border-gray-200 hidden md:block">
      <div class="p-6 border-b border-gray-100">
        <div class="flex items-center gap-3">
          <div class="w-10 h-10 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-xl flex items-center justify-center shadow-lg">
            <i data-lucide="package" class="w-6 h-6 text-white"></i>
          </div>
          <div>
            <h2 class="font-bold text-gray-900 text-lg">FormFlow</h2>
            <p class="text-xs text-gray-500">Sistema de Entregas</p>
          </div>
        </div>
      </div>
      <div class="p-3">
        <div class="text-xs font-semibold text-gray-500 uppercase tracking-wider px-3 py-2 mb-1">Navegação</div>
        <nav id="sidebar-menu" class="space-y-1">
          <a href="#home" class="flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-indigo-50 hover:text-indigo-700 transition-all duration-200">
            <i data-lucide="layout-dashboard" class="w-5 h-5"></i>
            <span class="font-medium">Início</span>
          </a>
          <a href="#nova-entrega" class="flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-indigo-50 hover:text-indigo-700 transition-all duration-200">
            <i data-lucide="plus" class="w-5 h-5"></i>
            <span class="font-medium">Nova Entrega</span>
          </a>
          <a href="#minhas-entregas" class="flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-indigo-50 hover:text-indigo-700 transition-all duration-200">
            <i data-lucide="list" class="w-5 h-5"></i>
            <span class="font-medium">Minhas Entregas</span>
          </a>
        </nav>
      </div>
      <div class="p-4 border-t border-gray-100">
        <div class="flex items-center gap-3 px-2">
          <div class="w-10 h-10 bg-gradient-to-br from-indigo-400 to-indigo-500 rounded-full flex items-center justify-center">
            <span class="text-white font-semibold text-sm" id="user-initial">U</span>
          </div>
          <div class="flex-1 min-w-0">
            <p class="font-medium text-gray-900 text-sm truncate" id="user-name">Usuário</p>
            <p class="text-xs text-gray-500 truncate">Gerencie suas entregas</p>
          </div>
          <a href="#perfil" class="nav-link p-2 rounded-xl hover:bg-gray-100">
            <i data-lucide="settings" class="w-5 h-5 text-gray-600"></i>
          </a>
        </div>
      </div>
    </aside>

    <!-- Mobile Header -->
    <header class="bg-white/80 backdrop-blur-sm border-b border-gray-200 px-4 py-3 md:hidden sticky top-0 z-10">
      <div class="flex items-center gap-3">
        <button id="mobile-menu-toggle" class="p-1.5 rounded-lg hover:bg-gray-100 transition-colors duration-200">
          <i data-lucide="menu" class="w-5 h-5"></i>
        </button>
        <h1 class="text-lg font-bold text-gray-900">FormFlow</h1>
      </div>
    </header>

    <!-- Mobile Sidebar -->
    <div id="mobile-sidebar" class="fixed inset-0 bg-white z-20 hidden">
      <div class="flex justify-between p-4 border-b border-gray-100">
        <div class="flex items-center gap-3">
          <div class="w-8 h-8 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-lg flex items-center justify-center shadow-md">
            <i data-lucide="package" class="w-5 h-5 text-white"></i>
          </div>
          <div>
            <h2 class="font-bold text-gray-900 text-base">FormFlow</h2>
            <p class="text-xs text-gray-500">Sistema de Entregas</p>
          </div>
        </div>
        <button id="mobile-menu-close" class="p-1.5 rounded-lg hover:bg-gray-100">
          <i data-lucide="x" class="w-5 h-5"></i>
        </button>
      </div>
      <nav class="p-3 space-y-1">
        <a href="#home" class="flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-indigo-50 hover:text-indigo-700 transition-all duration-200">
          <i data-lucide="layout-dashboard" class="w-5 h-5"></i>
          <span class="font-medium">Início</span>
        </a>
        <a href="#nova-entrega" class="flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-indigo-50 hover:text-indigo-700 transition-all duration-200">
          <i data-lucide="plus" class="w-5 h-5"></i>
          <span class="font-medium">Nova Entrega</span>
        </a>
        <a href="#minhas-entregas" class="flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-indigo-50 hover:text-indigo-700 transition-all duration-200">
          <i data-lucide="list" class="w-5 h-5"></i>
          <span class="font-medium">Minhas Entregas</span>
        </a>
        <a href="#perfil" class="flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-indigo-50 hover:text-indigo-700 transition-all duration-200">
          <i data-lucide="user" class="w-5 h-5"></i>
          <span class="font-medium">Perfil</span>
        </a>
      </nav>
    </div>

    <!-- Main Content -->
    <main class="flex-1 overflow-auto p-4 md:p-12">
      <div id="content" class="max-w-7xl mx-auto">
        <!-- Home Page -->
        <div id="home" class="page">
          <div class="text-center mb-12 md:mb-16 fade-in">
            <div class="inline-flex items-center justify-center w-16 h-16 md:w-20 md:h-20 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-3xl mb-4 md:mb-6 shadow-2xl">
              <i data-lucide="package" class="w-8 h-8 md:w-10 md:h-10 text-white"></i>
            </div>
            <h1 class="text-3xl md:text-6xl font-bold text-gray-900 mb-4">
              Sistema de Formulários<br>
              <span class="bg-gradient-to-r from-indigo-600 to-blue-600 bg-clip-text text-transparent">de Entrega</span>
            </h1>
            <p class="text-base md:text-xl text-gray-600 mb-6 md:mb-8 max-w-2xl mx-auto">
              Gerencie entregas de forma simples e profissional. Crie formulários, gere PDFs automaticamente e receba tudo por email.
            </p>
            <div class="flex flex-col sm:flex-row gap-4 justify-center">
              <a href="#nova-entrega" class="nav-link bg-gradient-to-r from-indigo-600 to-indigo-700 hover:from-indigo-700 hover:to-indigo-800 text-white px-6 py-3 md:py-6 text-base rounded-xl shadow-lg hover:shadow-xl transition-all duration-300 flex items-center justify-center">
                <i data-lucide="plus" class="w-4 h-4 md:w-5 md:h-5 mr-2"></i>
                Criar Formulário
              </a>
              <a href="#minhas-entregas" class="nav-link border-2 border-indigo-200 hover:border-indigo-300 hover:bg-indigo-50 px-6 py-3 md:py-6 text-base rounded-xl transition-all duration-300 flex items-center justify-center">
                Ver Minhas Entregas
              </a>
            </div>
          </div>

          <!-- Stats Cards -->
          <div id="stats-cards" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6 mb-12 md:mb-16">
            <!-- Stats will be populated by JavaScript -->
          </div>

          <!-- Features Section -->
          <div class="fade-in">
            <h2 class="text-2xl md:text-3xl font-bold text-center text-gray-900 mb-8 md:mb-12">Recursos Principais</h2>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 md:gap-8">
              <div class="bg-white border-none shadow-lg hover:shadow-2xl transition-all duration-300 rounded-xl">
                <div class="p-6 md:p-8">
                  <div class="w-14 h-14 md:w-16 md:h-16 bg-gradient-to-br from-blue-500 to-blue-600 rounded-2xl flex items-center justify-center mb-6 shadow-lg">
                    <i data-lucide="package" class="w-6 h-6 md:w-8 md:h-8 text-white"></i>
                  </div>
                  <h3 class="text-lg md:text-xl font-bold text-gray-900 mb-3">Fácil de Usar</h3>
                  <p class="text-gray-600 leading-relaxed text-sm md:text-base">Interface intuitiva e rápida. Preencha o formulário de entrega em poucos minutos.</p>
                </div>
              </div>
              <div class="bg-white border-none shadow-lg hover:shadow-2xl transition-all duration-300 rounded-xl">
                <div class="p-6 md:p-8">
                  <div class="w-14 h-14 md:w-16 md:h-16 bg-gradient-to-br from-purple-500 to-purple-600 rounded-2xl flex items-center justify-center mb-6 shadow-lg">
                    <i data-lucide="file-text" class="w-6 h-6 md:w-8 md:h-8 text-white"></i>
                  </div>
                  <h3 class="text-lg md:text-xl font-bold text-gray-900 mb-3">PDF Automático</h3>
                  <p class="text-gray-600 leading-relaxed text-sm md:text-base">Gere automaticamente um PDF profissional com todas as informações da entrega.</p>
                </div>
              </div>
              <div class="bg-white border-none shadow-lg hover:shadow-2xl transition-all duration-300 rounded-xl">
                <div class="p-6 md:p-8">
                  <div class="w-14 h-14 md:w-16 md:h-16 bg-gradient-to-br from-pink-500 to-pink-600 rounded-2xl flex items-center justify-center mb-6 shadow-lg">
                    <i data-lucide="mail" class="w-6 h-6 md:w-8 md:h-8 text-white"></i>
                  </div>
                  <h3 class="text-lg md:text-xl font-bold text-gray-900 mb-3">Envio por Email</h3>
                  <p class="text-gray-600 leading-relaxed text-sm md:text-base">Receba o PDF diretamente no seu email após o envio do formulário.</p>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Nova Entrega Page -->
        <div id="nova-entrega" class="page hidden">
          <div class="max-w-4xl mx-auto">
            <div class="flex items-center gap-4 mb-8">
              <a href="#home" class="nav-link p-2 border-2 border-gray-200 rounded-xl hover:bg-gray-100">
                <i data-lucide="arrow-left" class="w-5 h-5"></i>
              </a>
              <div>
                <h1 class="text-3xl md:text-4xl font-bold text-gray-900" id="nova-entrega-title">Formulário de Entrega</h1>
                <p class="text-gray-600 mt-1" id="nova-entrega-description">Preencha todos os campos com as informações da entrega</p>
              </div>
            </div>

            <!-- Progress Steps -->
            <div id="progress-steps" class="mb-12 flex items-center justify-between overflow-x-auto">
              <!-- Steps will be populated by JavaScript -->
            </div>

            <!-- Form Content -->
            <div id="form-content" class="fade-in">
              <!-- Form components will be populated by JavaScript -->
            </div>
          </div>
        </div>

        <!-- Minhas Entregas Page -->
        <div id="minhas-entregas" class="page hidden">
          <div class="mb-8">
            <h1 class="text-3xl md:text-4xl font-bold text-gray-900 mb-2">Minhas Entregas</h1>
            <p class="text-gray-600">Gerencie e visualize todas as suas entregas</p>
          </div>
          <div class="flex flex-col md:flex-row gap-4 mb-8">
            <div class="relative flex-1">
              <i data-lucide="search" class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400 w-5 h-5"></i>
              <input id="search-input" type="text" placeholder="Buscar por nome ou cidade..." class="w-full pl-10 pr-4 py-3 rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
            </div>
            <select id="status-filter" class="w-full md:w-48 rounded-xl border border-gray-200 p-3 focus:outline-none focus:ring-2 focus:ring-indigo-500">
              <option value="todos">Todos os status</option>
              <option value="pendente">Pendente</option>
              <option value="em_andamento">Em Andamento</option>
              <option value="entregue">Entregue</option>
              <option value="cancelada">Cancelada</option>
            </select>
          </div>
          <div id="entregas-list" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
            <!-- Entregas will be populated by JavaScript -->
          </div>
        </div>

        <!-- Perfil Page -->
        <div id="perfil" class="page hidden">
          <div class="max-w-4xl mx-auto">
            <div class="flex items-center gap-4 mb-8">
              <a href="#home" class="nav-link p-2 border-2 border-gray-200 rounded-xl hover:bg-gray-100">
                <i data-lucide="arrow-left" class="w-5 h-5"></i>
              </a>
              <div>
                <h1 class="text-3xl md:text-4xl font-bold text-gray-900">Perfil do Usuário</h1>
                <p class="text-gray-600 mt-1">Gerencie suas informações pessoais</p>
              </div>
            </div>
            <div class="bg-white border-none shadow-2xl rounded-xl fade-in">
              <div class="p-6 space-y-6">
                <div class="flex flex-col sm:flex-row items-center gap-6">
                  <div class="w-24 h-24 bg-gradient-to-br from-indigo-400 to-indigo-500 rounded-full flex items-center justify-center text-4xl text-white font-bold">U</div>
                  <div class="flex-1 w-full">
                    <div class="space-y-2 mb-4">
                      <label for="profile-name" class="text-base font-semibold">Nome</label>
                      <input id="profile-name" type="text" placeholder="Seu nome" class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                    </div>
                    <div class="space-y-2 mb-4">
                      <label for="profile-email" class="text-base font-semibold">Email</label>
                      <input id="profile-email" type="email" placeholder="seu@email.com" class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                    </div>
                    <button id="save-profile" class="bg-gradient-to-r from-indigo-600 to-indigo-700 hover:from-indigo-700 hover:to-indigo-800 px-8 h-12 text-base rounded-xl shadow-lg text-white flex items-center justify-center w-full">
                      Salvar Alterações
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Dialog for Entrega Details -->
        <div id="entrega-details-dialog" class="fixed inset-0 bg-black/50 flex items-center justify-center hidden">
          <div class="bg-white rounded-xl max-w-3xl w-full max-h-[90vh] overflow-y-auto p-4 md:p-6">
            <div class="flex justify-between items-center mb-4">
              <h2 class="text-xl md:text-2xl font-bold">Detalhes da Entrega</h2>
              <button id="close-dialog" class="p-1.5 rounded-lg hover:bg-gray-100">
                <i data-lucide="x" class="w-5 h-5 md:w-6 md:h-6"></i>
              </button>
            </div>
            <div id="entrega-details-content"></div>
          </div>
        </div>
      </div>
    </main>
  </div>

  <script>
    // Initialize Lucide icons
    lucide.createIcons();

    // In-memory data storage with localStorage persistence
    let entregas = JSON.parse(localStorage.getItem('entregas')) || [];
    let userProfile = JSON.parse(localStorage.getItem('userProfile')) || { name: 'Usuário', email: '', initial: 'U' };
    let formData = {
      nome_recebedor: '',
      nome_legitimo_recebedor: '',
      endereco_completo: '',
      cep: '',
      cidade: '',
      uf: '',
      pais: 'Brasil',
      primeira_tentativa: '',
      segunda_tentativa: '',
      terceira_tentativa: '',
      data_entrega: '',
      observacoes: '',
      assinatura_entregador: '',
      assinatura_recebedor: '',
      status: 'pendente'
    };
    let currentStep = 1;
    let currentEditId = null;

    // Status configuration
    const statusConfig = {
      pendente: { label: 'Pendente', color: 'bg-gray-100 text-gray-800 border-gray-200' },
      em_andamento: { label: 'Em Andamento', color: 'bg-orange-100 text-orange-800 border-orange-200' },
      entregue: { label: 'Entregue', color: 'bg-green-100 text-green-800 border-green-200' },
      cancelada: { label: 'Cancelada', color: 'bg-red-100 text-red-800 border-red-200' }
    };

    // Navigation
    function navigateTo(page) {
      document.querySelectorAll('.page').forEach(p => p.classList.add('hidden'));
      document.querySelector(`#${page}`).classList.remove('hidden');
      document.querySelectorAll('#sidebar-menu a').forEach(a => {
        a.classList.toggle('bg-indigo-500', a.getAttribute('href') === `#${page}`);
        a.classList.toggle('text-white', a.getAttribute('href') === `#${page}`);
        a.classList.toggle('hover:bg-indigo-600', a.getAttribute('href') === `#${page}`);
        a.classList.toggle('hover:bg-indigo-50', a.getAttribute('href') !== `#${page}`);
      });
      if (page === 'home') updateStats();
      if (page === 'nova-entrega') {
        document.getElementById('nova-entrega-title').textContent = currentEditId ? 'Editar Entrega' : 'Formulário de Entrega';
        document.getElementById('nova-entrega-description').textContent = currentEditId ? 'Edite as informações da entrega' : 'Preencha todos os campos com as informações da entrega';
        renderFormStep();
      }
      if (page === 'minhas-entregas') renderEntregasList();
      if (page === 'perfil') renderProfile();
      window.scrollTo(0, 0);
    }

    // Mobile menu toggle
    document.getElementById('mobile-menu-toggle').addEventListener('click', () => {
      document.getElementById('mobile-sidebar').classList.remove('hidden');
    });
    document.getElementById('mobile-menu-close').addEventListener('click', () => {
      document.getElementById('mobile-sidebar').classList.add('hidden');
    });

    // Handle navigation
    document.querySelectorAll('#sidebar-menu a, #mobile-sidebar a').forEach(link => {
      link.addEventListener('click', (e) => {
        e.preventDefault();
        const page = link.getAttribute('href').slice(1);
        if (page === 'nova-entrega') {
          currentEditId = null;
          formData = {
            nome_recebedor: '',
            nome_legitimo_recebedor: '',
            endereco_completo: '',
            cep: '',
            cidade: '',
            uf: '',
            pais: 'Brasil',
            primeira_tentativa: '',
            segunda_tentativa: '',
            terceira_tentativa: '',
            data_entrega: '',
            observacoes: '',
            assinatura_entregador: '',
            assinatura_recebedor: '',
            status: 'pendente'
          };
          currentStep = 1;
        }
        navigateTo(page);
        document.getElementById('mobile-sidebar').classList.add('hidden');
      });
    });

    // Update stats on Home page
    function updateStats() {
      const stats = {
        total: entregas.length,
        entregues: entregas.filter(e => e.status === 'entregue').length,
        pendentes: entregas.filter(e => e.status === 'pendente').length,
        emAndamento: entregas.filter(e => e.status === 'em_andamento').length
      };
      const statsCards = document.getElementById('stats-cards');
      statsCards.innerHTML = `
        <div class="bg-white border-none shadow-lg hover:shadow-xl transition-all duration-300 overflow-hidden fade-in">
          <div class="absolute top-0 right-0 w-32 h-32 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-full transform translate-x-16 -translate-y-16 opacity-10"></div>
          <div class="p-6">
            <div class="flex justify-between items-start">
              <div>
                <p class="text-sm font-medium text-gray-500 mb-1">Total de Entregas</p>
                <h3 class="text-4xl font-bold text-gray-900">${stats.total}</h3>
              </div>
              <div class="p-3 bg-indigo-100 rounded-xl">
                <i data-lucide="package" class="w-6 h-6 text-indigo-600"></i>
              </div>
            </div>
            <div class="flex items-center text-sm text-green-600 mt-3">
              <i data-lucide="trending-up" class="w-4 h-4 mr-1"></i>
              <span class="font-medium">Todas as entregas</span>
            </div>
          </div>
        </div>
        <div class="bg-white border-none shadow-lg hover:shadow-xl transition-all duration-300 overflow-hidden fade-in">
          <div class="absolute top-0 right-0 w-32 h-32 bg-gradient-to-br from-green-500 to-green-600 rounded-full transform translate-x-16 -translate-y-16 opacity-10"></div>
          <div class="p-6">
            <div class="flex justify-between items-start">
              <div>
                <p class="text-sm font-medium text-gray-500 mb-1">Entregues</p>
                <h3 class="text-4xl font-bold text-gray-900">${stats.entregues}</h3>
              </div>
              <div class="p-3 bg-green-100 rounded-xl">
                <i data-lucide="check-circle" class="w-6 h-6 text-green-600"></i>
              </div>
            </div>
            <div class="flex items-center text-sm text-gray-600 mt-3">
              <span class="font-medium">${stats.total > 0 ? Math.round((stats.entregues / stats.total) * 100) : 0}% concluídas</span>
            </div>
          </div>
        </div>
        <div class="bg-white border-none shadow-lg hover:shadow-xl transition-all duration-300 overflow-hidden fade-in">
          <div class="absolute top-0 right-0 w-32 h-32 bg-gradient-to-br from-orange-500 to-orange-600 rounded-full transform translate-x-16 -translate-y-16 opacity-10"></div>
          <div class="p-6">
            <div class="flex justify-between items-start">
              <div>
                <p class="text-sm font-medium text-gray-500 mb-1">Em Andamento</p>
                <h3 class="text-4xl font-bold text-gray-900">${stats.emAndamento}</h3>
              </div>
              <div class="p-3 bg-orange-100 rounded-xl">
                <i data-lucide="clock" class="w-6 h-6 text-orange-600"></i>
              </div>
            </div>
            <div class="flex items-center text-sm text-gray-600 mt-3">
              <span class="font-medium">Sendo processadas</span>
            </div>
          </div>
        </div>
        <div class="bg-white border-none shadow-lg hover:shadow-xl transition-all duration-300 overflow-hidden fade-in">
          <div class="absolute top-0 right-0 w-32 h-32 bg-gradient-to-br from-gray-500 to-gray-600 rounded-full transform translate-x-16 -translate-y-16 opacity-10"></div>
          <div class="p-6">
            <div class="flex justify-between items-start">
              <div>
                <p class="text-sm font-medium text-gray-500 mb-1">Pendentes</p>
                <h3 class="text-4xl font-bold text-gray-900">${stats.pendentes}</h3>
              </div>
              <div class="p-3 bg-gray-100 rounded-xl">
                <i data-lucide="x-circle" class="w-6 h-6 text-gray-600"></i>
              </div>
            </div>
            <div class="flex items-center text-sm text-gray-600 mt-3">
              <span class="font-medium">Aguardando início</span>
            </div>
          </div>
        </div>
      `;
      lucide.createIcons();
    }

    // Form steps configuration
    const steps = [
      { number: 1, title: 'Dados do Recebedor' },
      { number: 2, title: 'Endereço de Entrega' },
      { number: 3, title: 'Tentativas de Entrega' },
      { number: 4, title: 'Informações da Entrega' },
      { number: 5, title: 'Assinaturas' }
    ];

    // Render form step
    function renderFormStep() {
      const progressSteps = document.getElementById('progress-steps');
      progressSteps.innerHTML = steps.map((s, index) => `
        <div class="flex flex-col items-center min-w-[3rem]">
          <div class="w-10 h-10 md:w-12 md:h-12 rounded-full flex items-center justify-center font-bold transition-all duration-300 ${currentStep >= s.number ? 'bg-gradient-to-br from-indigo-500 to-indigo-600 text-white shadow-lg' : 'bg-gray-200 text-gray-500'}">
            ${s.number}
          </div>
          <p class="text-xs mt-2 font-medium text-center hidden md:block ${currentStep >= s.number ? 'text-indigo-600' : 'text-gray-500'}">
            ${s.title}
          </p>
        </div>
        ${index < steps.length - 1 ? `
          <div class="flex-1 h-1 mx-1 bg-gray-200 rounded">
            <div class="h-full rounded transition-all duration-500 ${currentStep > s.number ? 'bg-gradient-to-r from-indigo-500 to-indigo-600' : ''}" style="width: ${currentStep > s.number ? '100%' : '0%'}"></div>
          </div>
        ` : ''}
      `).join('');

      const formContent = document.getElementById('form-content');
      formContent.innerHTML = '';
      if (currentStep === 1) {
        formContent.innerHTML = `
          <div class="bg-white border-none shadow-2xl rounded-xl fade-in">
            <div class="border-b border-gray-100 p-4 md:p-6">
              <div class="flex items-center gap-3">
                <div class="w-10 h-10 md:w-12 md:h-12 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-xl flex items-center justify-center">
                  <i data-lucide="user" class="w-5 h-5 md:w-6 md:h-6 text-white"></i>
                </div>
                <div>
                  <h3 class="text-xl md:text-2xl font-bold text-gray-900">Dados do Recebedor</h3>
                  <p class="text-sm text-gray-500 mt-1">Informações da pessoa que receberá a entrega</p>
                </div>
              </div>
            </div>
            <form id="form-recebedor" class="p-4 md:p-6 space-y-6">
              <div class="space-y-2">
                <label for="nome_recebedor" class="text-base font-semibold">Nome do Recebedor *</label>
                <input id="nome_recebedor" type="text" value="${formData.nome_recebedor}" placeholder="Digite o nome completo" required class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
              </div>
              <div class="space-y-2">
                <label for="nome_legitimo_recebedor" class="text-base font-semibold">Nome Legítimo do Recebedor</label>
                <input id="nome_legitimo_recebedor" type="text" value="${formData.nome_legitimo_recebedor}" placeholder="Nome completo conforme documento" class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                <p class="text-sm text-gray-500">Se diferente do nome do recebedor</p>
              </div>
              <div class="flex justify-end pt-6">
                <button type="submit" class="bg-gradient-to-r from-indigo-600 to-indigo-700 hover:from-indigo-700 hover:to-indigo-800 px-6 md:px-8 h-12 text-base rounded-xl shadow-lg text-white flex items-center">
                  Próximo <i data-lucide="chevron-right" class="w-5 h-5 ml-2"></i>
                </button>
              </div>
            </form>
          </div>
        `;
        document.getElementById('form-recebedor').addEventListener('submit', (e) => {
          e.preventDefault();
          formData.nome_recebedor = document.getElementById('nome_recebedor').value;
          formData.nome_legitimo_recebedor = document.getElementById('nome_legitimo_recebedor').value;
          if (formData.nome_recebedor) {
            currentStep++;
            renderFormStep();
          }
        });
      } else if (currentStep === 2) {
        formContent.innerHTML = `
          <div class="bg-white border-none shadow-2xl rounded-xl fade-in">
            <div class="border-b border-gray-100 p-4 md:p-6">
              <div class="flex items-center gap-3">
                <div class="w-10 h-10 md:w-12 md:h-12 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-xl flex items-center justify-center">
                  <i data-lucide="map-pin" class="w-5 h-5 md:w-6 md:h-6 text-white"></i>
                </div>
                <div>
                  <h3 class="text-xl md:text-2xl font-bold text-gray-900">Endereço de Entrega</h3>
                  <p class="text-sm text-gray-500 mt-1">Local onde será realizada a entrega</p>
                </div>
              </div>
            </div>
            <form id="form-endereco" class="p-4 md:p-6 space-y-6">
              <div class="space-y-2">
                <label for="endereco_completo" class="text-base font-semibold">Endereço Completo *</label>
                <input id="endereco_completo" type="text" value="${formData.endereco_completo}" placeholder="Rua, número, complemento" required class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
              </div>
              <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div class="space-y-2">
                  <label for="cep" class="text-base font-semibold">CEP</label>
                  <input id="cep" type="text" value="${formData.cep}" placeholder="00000-000" class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                </div>
                <div class="space-y-2">
                  <label for="cidade" class="text-base font-semibold">Cidade *</label>
                  <input id="cidade" type="text" value="${formData.cidade}" placeholder="Nome da cidade" required class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                </div>
              </div>
              <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div class="space-y-2">
                  <label for="uf" class="text-base font-semibold">UF *</label>
                  <input id="uf" type="text" value="${formData.uf}" placeholder="SP" maxlength="2" required class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                </div>
                <div class="space-y-2">
                  <label for="pais" class="text-base font-semibold">País</label>
                  <input id="pais" type="text" value="${formData.pais}" placeholder="Brasil" class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                </div>
              </div>
              <div class="flex justify-between pt-6">
                <button type="button" onclick="currentStep--; renderFormStep();" class="px-6 md:px-8 h-12 text-base rounded-xl border border-gray-200 hover:bg-gray-100 flex items-center">
                  <i data-lucide="chevron-left" class="w-5 h-5 mr-2"></i> Voltar
                </button>
                <button type="submit" class="bg-gradient-to-r from-indigo-600 to-indigo-700 hover:from-indigo-700 hover:to-indigo-800 px-6 md:px-8 h-12 text-base rounded-xl shadow-lg text-white flex items-center">
                  Próximo <i data-lucide="chevron-right" class="w-5 h-5 ml-2"></i>
                </button>
              </div>
            </form>
          </div>
        `;
        document.getElementById('form-endereco').addEventListener('submit', (e) => {
          e.preventDefault();
          formData.endereco_completo = document.getElementById('endereco_completo').value;
          formData.cep = document.getElementById('cep').value;
          formData.cidade = document.getElementById('cidade').value;
          formData.uf = document.getElementById('uf').value.toUpperCase();
          formData.pais = document.getElementById('pais').value;
          if (formData.endereco_completo && formData.cidade && formData.uf) {
            currentStep++;
            renderFormStep();
          }
        });
      } else if (currentStep === 3) {
        formContent.innerHTML = `
          <div class="bg-white border-none shadow-2xl rounded-xl fade-in">
            <div class="border-b border-gray-100 p-4 md:p-6">
              <div class="flex items-center gap-3">
                <div class="w-10 h-10 md:w-12 md:h-12 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-xl flex items-center justify-center">
                  <i data-lucide="calendar" class="w-5 h-5 md:w-6 md:h-6 text-white"></i>
                </div>
                <div>
                  <h3 class="text-xl md:text-2xl font-bold text-gray-900">Tentativas de Entrega</h3>
                  <p class="text-sm text-gray-500 mt-1">Registre as datas das tentativas de entrega</p>
                </div>
              </div>
            </div>
            <form id="form-tentativas" class="p-4 md:p-6 space-y-6">
              <div class="grid grid-cols-1 gap-6">
                <div class="space-y-2">
                  <label for="primeira_tentativa" class="text-base font-semibold">1ª Tentativa</label>
                  <input id="primeira_tentativa" type="date" value="${formData.primeira_tentativa}" class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                </div>
                <div class="space-y-2">
                  <label for="segunda_tentativa" class="text-base font-semibold">2ª Tentativa</label>
                  <input id="segunda_tentativa" type="date" value="${formData.segunda_tentativa}" class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                </div>
                <div class="space-y-2">
                  <label for="terceira_tentativa" class="text-base font-semibold">3ª Tentativa</label>
                  <input id="terceira_tentativa" type="date" value="${formData.terceira_tentativa}" class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                </div>
              </div>
              <div class="bg-blue-50 border border-blue-200 rounded-xl p-4">
                <p class="text-sm text-blue-800"><strong>Dica:</strong> Registre as datas das tentativas de entrega para manter um histórico completo. Você pode preencher apenas as tentativas já realizadas.</p>
              </div>
              <div class="flex justify-between pt-6">
                <button type="button" onclick="currentStep--; renderFormStep();" class="px-6 md:px-8 h-12 text-base rounded-xl border border-gray-200 hover:bg-gray-100 flex items-center">
                  <i data-lucide="chevron-left" class="w-5 h-5 mr-2"></i> Voltar
                </button>
                <button type="submit" class="bg-gradient-to-r from-indigo-600 to-indigo-700 hover:from-indigo-700 hover:to-indigo-800 px-6 md:px-8 h-12 text-base rounded-xl shadow-lg text-white flex items-center">
                  Próximo <i data-lucide="chevron-right" class="w-5 h-5 ml-2"></i>
                </button>
              </div>
            </form>
          </div>
        `;
        document.getElementById('form-tentativas').addEventListener('submit', (e) => {
          e.preventDefault();
          formData.primeira_tentativa = document.getElementById('primeira_tentativa').value;
          formData.segunda_tentativa = document.getElementById('segunda_tentativa').value;
          formData.terceira_tentativa = document.getElementById('terceira_tentativa').value;
          currentStep++;
          renderFormStep();
        });
      } else if (currentStep === 4) {
        formContent.innerHTML = `
          <div class="bg-white border-none shadow-2xl rounded-xl fade-in">
            <div class="border-b border-gray-100 p-4 md:p-6">
              <div class="flex items-center gap-3">
                <div class="w-10 h-10 md:w-12 md:h-12 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-xl flex items-center justify-center">
                  <i data-lucide="file-text" class="w-5 h-5 md:w-6 md:h-6 text-white"></i>
                </div>
                <div>
                  <h3 class="text-xl md:text-2xl font-bold text-gray-900">Informações da Entrega</h3>
                  <p class="text-sm text-gray-500 mt-1">Detalhes adicionais sobre a entrega</p>
                </div>
              </div>
            </div>
            <form id="form-entrega" class="p-4 md:p-6 space-y-6">
              <div class="space-y-2">
                <label for="data_entrega" class="text-base font-semibold">Data da Entrega</label>
                <input id="data_entrega" type="date" value="${formData.data_entrega}" class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
              </div>
              <div class="space-y-2">
                <label for="observacoes" class="text-base font-semibold">Observações</label>
                <textarea id="observacoes" placeholder="Observações sobre a entrega" class="w-full h-32 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">${formData.observacoes}</textarea>
              </div>
              <div class="space-y-2">
                <label for="status" class="text-base font-semibold">Status</label>
                <select id="status" class="w-full h-12 text-base rounded-xl border border-gray-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                  <option value="pendente" ${formData.status === 'pendente' ? 'selected' : ''}>Pendente</option>
                  <option value="em_andamento" ${formData.status === 'em_andamento' ? 'selected' : ''}>Em Andamento</option>
                  <option value="entregue" ${formData.status === 'entregue' ? 'selected' : ''}>Entregue</option>
                  <option value="cancelada" ${formData.status === 'cancelada' ? 'selected' : ''}>Cancelada</option>
                </select>
              </div>
              <div class="flex justify-between pt-6">
                <button type="button" onclick="currentStep--; renderFormStep();" class="px-6 md:px-8 h-12 text-base rounded-xl border border-gray-200 hover:bg-gray-100 flex items-center">
                  <i data-lucide="chevron-left" class="w-5 h-5 mr-2"></i> Voltar
                </button>
                <button type="submit" class="bg-gradient-to-r from-indigo-600 to-indigo-700 hover:from-indigo-700 hover:to-indigo-800 px-6 md:px-8 h-12 text-base rounded-xl shadow-lg text-white flex items-center">
                  Próximo <i data-lucide="chevron-right" class="w-5 h-5 ml-2"></i>
                </button>
              </div>
            </form>
          </div>
        `;
        document.getElementById('form-entrega').addEventListener('submit', (e) => {
          e.preventDefault();
          formData.data_entrega = document.getElementById('data_entrega').value;
          formData.observacoes = document.getElementById('observacoes').value;
          formData.status = document.getElementById('status').value;
          currentStep++;
          renderFormStep();
        });
      } else if (currentStep === 5) {
        formContent.innerHTML = `
          <div class="bg-white border-none shadow-2xl rounded-xl fade-in">
            <div class="border-b border-gray-100 p-4 md:p-6">
              <div class="flex items-center gap-3">
                <div class="w-10 h-10 md:w-12 md:h-12 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-xl flex items-center justify-center">
                  <i data-lucide="pen-tool" class="w-5 h-5 md:w-6 md:h-6 text-white"></i>
                </div>
                <div>
                  <h3 class="text-xl md:text-2xl font-bold text-gray-900">Assinaturas</h3>
                  <p class="text-sm text-gray-500 mt-1">Coleta das assinaturas do entregador e recebedor</p>
                </div>
              </div>
            </div>
            <form id="form-assinaturas" class="p-4 md:p-6 space-y-8">
              <div class="space-y-3">
                <div class="flex justify-between items-center">
                  <label class="text-base font-semibold">Assinatura do Entregador</label>
                  <button type="button" onclick="clearCanvas('canvas-entregador')" class="text-gray-500 hover:text-gray-700 flex items-center">
                    <i data-lucide="trash-2" class="w-4 h-4 mr-1"></i> Limpar
                  </button>
                </div>
                <canvas id="canvas-entregador" width="600" height="200" class="w-full h-auto border-2 border-dashed border-gray-300 rounded-xl bg-gray-50 cursor-crosshair"></canvas>
                <p class="text-sm text-gray-500">Desenhe a assinatura do entregador na área acima</p>
              </div>
              <div class="space-y-3">
                <div class="flex justify-between items-center">
                  <label class="text-base font-semibold">Assinatura do Recebedor</label>
                  <button type="button" onclick="clearCanvas('canvas-recebedor')" class="text-gray-500 hover:text-gray-700 flex items-center">
                    <i data-lucide="trash-2" class="w-4 h-4 mr-1"></i> Limpar
                  </button>
                </div>
                <canvas id="canvas-recebedor" width="600" height="200" class="w-full h-auto border-2 border-dashed border-gray-300 rounded-xl bg-gray-50 cursor-crosshair"></canvas>
                <p class="text-sm text-gray-500">Desenhe a assinatura do recebedor na área acima</p>
              </div>
              <div class="flex justify-between pt-6">
                <button type="button" onclick="currentStep--; renderFormStep();" class="px-6 md:px-8 h-12 text-base rounded-xl border border-gray-200 hover:bg-gray-100 flex items-center">
                  <i data-lucide="chevron-left" class="w-5 h-5 mr-2"></i> Voltar
                </button>
                <button type="submit" class="bg-gradient-to-r from-green-600 to-green-700 hover:from-green-700 hover:to-green-800 px-6 md:px-8 h-12 text-base rounded-xl shadow-lg text-white flex items-center">
                  <i data-lucide="send" class="w-5 h-5 mr-2"></i> Enviar Formulário
                </button>
              </div>
            </form>
          </div>
        `;
        setupCanvas('canvas-entregador');
        setupCanvas('canvas-recebedor');

        // Load existing signatures if editing
        if (formData.assinatura_entregador) {
          const imgEnt = new Image();
          imgEnt.src = formData.assinatura_entregador;
          imgEnt.onload = () => {
            document.getElementById('canvas-entregador').getContext('2d').drawImage(imgEnt, 0, 0, 600, 200);
          };
        }
        if (formData.assinatura_recebedor) {
          const imgRec = new Image();
          imgRec.src = formData.assinatura_recebedor;
          imgRec.onload = () => {
            document.getElementById('canvas-recebedor').getContext('2d').drawImage(imgRec, 0, 0, 600, 200);
          };
        }

        document.getElementById('form-assinaturas').addEventListener('submit', (e) => {
          e.preventDefault();
          const canvasEntregador = document.getElementById('canvas-entregador');
          const canvasRecebedor = document.getElementById('canvas-recebedor');
          formData.assinatura_entregador = canvasEntregador.toDataURL('image/png');
          formData.assinatura_recebedor = canvasRecebedor.toDataURL('image/png');
          let id;
          if (currentEditId) {
            const index = entregas.findIndex(e => e.id === currentEditId);
            entregas[index] = { ...formData, id: currentEditId };
            id = currentEditId;
            currentEditId = null;
          } else {
            id = Date.now().toString();
            entregas.push({ ...formData, id });
          }
          localStorage.setItem('entregas', JSON.stringify(entregas));
          // Generate PDF automatically
          generatePDF(id);
          // Simulate email (in production, integrate with email service)
          alert('PDF gerado e baixado. Em uma aplicação real, seria enviado por email.');
          formData = {
            nome_recebedor: '',
            nome_legitimo_recebedor: '',
            endereco_completo: '',
            cep: '',
            cidade: '',
            uf: '',
            pais: 'Brasil',
            primeira_tentativa: '',
            segunda_tentativa: '',
            terceira_tentativa: '',
            data_entrega: '',
            observacoes: '',
            assinatura_entregador: '',
            assinatura_recebedor: '',
            status: 'pendente'
          };
          currentStep = 1;
          navigateTo('minhas-entregas');
        });
      }
      lucide.createIcons();
    }

    // Canvas drawing
    function setupCanvas(canvasId) {
      const canvas = document.getElementById(canvasId);
      const ctx = canvas.getContext('2d');
      let isDrawing = false;

      function startDrawing(e) {
        isDrawing = true;
        const rect = canvas.getBoundingClientRect();
        const x = (e.clientX || e.touches?.[0]?.clientX) - rect.left;
        const y = (e.clientY || e.touches?.[0]?.clientY) - rect.top;
        ctx.beginPath();
        ctx.moveTo(x, y);
      }

      function draw(e) {
        if (!isDrawing) return;
        const rect = canvas.getBoundingClientRect();
        const x = (e.clientX || e.touches?.[0]?.clientX) - rect.left;
        const y = (e.clientY || e.touches?.[0]?.clientY) - rect.top;
        ctx.lineTo(x, y);
        ctx.strokeStyle = '#1e40af';
        ctx.lineWidth = 2;
        ctx.lineCap = 'round';
        ctx.stroke();
      }

      function stopDrawing() {
        isDrawing = false;
      }

      canvas.addEventListener('mousedown', startDrawing);
      canvas.addEventListener('mousemove', draw);
      canvas.addEventListener('mouseup', stopDrawing);
      canvas.addEventListener('mouseleave', stopDrawing);
      canvas.addEventListener('touchstart', startDrawing);
      canvas.addEventListener('touchmove', draw);
      canvas.addEventListener('touchend', stopDrawing);
    }

    function clearCanvas(canvasId) {
      const canvas = document.getElementById(canvasId);
      const ctx = canvas.getContext('2d');
      ctx.clearRect(0, 0, canvas.width, canvas.height);
    }

    // Generate PDF
    function generatePDF(id) {
      const entrega = entregas.find(e => e.id === id);
      if (!entrega) return;
      const { jsPDF } = window.jspdf;
      const doc = new jsPDF();
      doc.setFont("helvetica", "bold");
      doc.setFontSize(20);
      doc.text("Formulário de Entrega", 105, 15, { align: 'center' });
      doc.setFont("helvetica", "normal");
      doc.setFontSize(12);
      let y = 30;
      doc.text(`Nome do Recebedor: ${entrega.nome_recebedor || 'N/A'}`, 20, y);
      y += 10;
      if (entrega.nome_legitimo_recebedor) {
        doc.text(`Nome Legítimo: ${entrega.nome_legitimo_recebedor}`, 20, y);
        y += 10;
      }
      doc.text(`Endereço: ${entrega.endereco_completo || 'N/A'}`, 20, y);
      y += 10;
      doc.text(`CEP: ${entrega.cep || 'N/A'}`, 20, y);
      y += 10;
      doc.text(`Cidade/UF: ${entrega.cidade || 'N/A'} - ${entrega.uf || 'N/A'}`, 20, y);
      y += 10;
      doc.text(`País: ${entrega.pais || 'N/A'}`, 20, y);
      y += 15;
      doc.text("Tentativas de Entrega:", 20, y);
      y += 10;
      doc.text(`1ª: ${entrega.primeira_tentativa ? new Date(entrega.primeira_tentativa).toLocaleDateString('pt-BR') : 'N/A'}`, 30, y);
      y += 10;
      doc.text(`2ª: ${entrega.segunda_tentativa ? new Date(entrega.segunda_tentativa).toLocaleDateString('pt-BR') : 'N/A'}`, 30, y);
      y += 10;
      doc.text(`3ª: ${entrega.terceira_tentativa ? new Date(entrega.terceira_tentativa).toLocaleDateString('pt-BR') : 'N/A'}`, 30, y);
      y += 15;
      doc.text(`Data da Entrega: ${entrega.data_entrega ? new Date(entrega.data_entrega).toLocaleDateString('pt-BR') : 'N/A'}`, 20, y);
      y += 10;
      doc.text(`Status: ${statusConfig[entrega.status].label}`, 20, y);
      y += 15;
      doc.text("Observações:", 20, y);
      y += 10;
      const obsLines = doc.splitTextToSize(entrega.observacoes || 'N/A', 170);
      doc.text(obsLines, 20, y);
      y += (obsLines.length * 10) + 10;
      doc.text("Assinaturas:", 20, y);
      y += 10;
      doc.text("Entregador:", 20, y);
      if (entrega.assinatura_entregador) {
        doc.addImage(entrega.assinatura_entregador, 'PNG', 20, y + 5, 80, 30);
      }
      y += 40;
      doc.text("Recebedor:", 20, y);
      if (entrega.assinatura_recebedor) {
        doc.addImage(entrega.assinatura_recebedor, 'PNG', 20, y + 5, 80, 30);
      }
      doc.save(`formulario-entrega-${entrega.nome_recebedor || 'desconhecido'}.pdf`);
    }

    // Render entregas list
    function renderEntregasList() {
      const searchTerm = document.getElementById('search-input').value.toLowerCase();
      const statusFilter = document.getElementById('status-filter').value;
      const filteredEntregas = entregas.filter(entrega => {
        const matchesSearch = entrega.nome_recebedor.toLowerCase().includes(searchTerm) || entrega.cidade.toLowerCase().includes(searchTerm);
        const matchesStatus = statusFilter === 'todos' || entrega.status === statusFilter;
        return matchesSearch && matchesStatus;
      });

      const entregasList = document.getElementById('entregas-list');
      if (filteredEntregas.length === 0) {
        entregasList.innerHTML = `
          <div class="bg-white border-none shadow-lg rounded-xl">
            <div class="p-8 md:p-12 text-center">
              <i data-lucide="package" class="w-12 h-12 md:w-16 md:h-16 text-gray-300 mx-auto mb-4"></i>
              <h3 class="text-lg md:text-xl font-semibold text-gray-900 mb-2">Nenhuma entrega encontrada</h3>
              <p class="text-gray-600 text-sm md:text-base">${searchTerm || statusFilter !== 'todos' ? 'Tente ajustar os filtros de busca' : 'Comece criando sua primeira entrega'}</p>
            </div>
          </div>
        `;
      } else {
        entregasList.innerHTML = filteredEntregas.map((entrega, index) => `
          <div class="bg-white border-none shadow-lg hover:shadow-xl transition-all duration-300 overflow-hidden group fade-in" style="animation-delay: ${index * 0.05}s">
            <div class="absolute top-0 right-0 w-32 h-32 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-full transform translate-x-16 -translate-y-16 opacity-0 group-hover:opacity-10 transition-opacity duration-300"></div>
            <div class="p-4 md:p-6">
              <div class="flex justify-between items-start mb-2">
                <i data-lucide="package" class="w-6 h-6 md:w-8 md:h-8 text-indigo-500"></i>
                <span class="border px-2 py-0.5 md:px-3 md:py-1 rounded-full ${statusConfig[entrega.status].color} text-xs md:text-sm">${statusConfig[entrega.status].label}</span>
              </div>
              <h3 class="text-lg md:text-xl font-bold text-gray-900">${entrega.nome_recebedor}</h3>
              ${entrega.nome_legitimo_recebedor ? `<p class="text-sm text-gray-500">${entrega.nome_legitimo_recebedor}</p>` : ''}
              <div class="space-y-3 mt-3">
                <div class="flex items-start gap-2 text-sm text-gray-600">
                  <i data-lucide="map-pin" class="w-4 h-4 mt-0.5 text-indigo-500"></i>
                  <span class="line-clamp-2">${entrega.endereco_completo}, ${entrega.cidade} - ${entrega.uf}</span>
                </div>
                ${entrega.data_entrega ? `
                  <div class="flex items-center gap-2 text-sm text-gray-600">
                    <i data-lucide="calendar" class="w-4 h-4 text-indigo-500"></i>
                    <span>${new Date(entrega.data_entrega).toLocaleDateString('pt-BR')}</span>
                  </div>
                ` : ''}
                <div class="flex gap-2 pt-3">
                  <button onclick="showEntregaDetails('${entrega.id}')" class="flex-1 rounded-lg border border-gray-200 hover:bg-gray-100 py-2 flex items-center justify-center text-sm">
                    <i data-lucide="eye" class="w-4 h-4 mr-1"></i> Ver
                  </button>
                  <button onclick="deleteEntrega('${entrega.id}')" class="rounded-lg border border-gray-200 hover:bg-red-50 text-red-600 hover:text-red-700 py-2 px-3 flex items-center text-sm">
                    <i data-lucide="trash-2" class="w-4 h-4"></i>
                  </button>
                </div>
              </div>
            </div>
          </div>
        `).join('');
      }
      lucide.createIcons();
    }

    // Render profile page
    function renderProfile() {
      document.getElementById('profile-name').value = userProfile.name;
      document.getElementById('profile-email').value = userProfile.email;
      document.getElementById('profile-initial').textContent = userProfile.initial;
      document.getElementById('user-name').textContent = userProfile.name;
      document.getElementById('user-initial').textContent = userProfile.initial;
      lucide.createIcons();
    }

    // Save profile
    document.getElementById('save-profile').addEventListener('click', () => {
      const name = document.getElementById('profile-name').value.trim();
      const email = document.getElementById('profile-email').value.trim();
      if (name) {
        userProfile.name = name;
        userProfile.email = email;
        userProfile.initial = name.charAt(0).toUpperCase();
        localStorage.setItem('userProfile', JSON.stringify(userProfile));
        renderProfile();
        alert('Perfil atualizado com sucesso!');
      } else {
        alert('O nome é obrigatório.');
      }
    });

    // Show entrega details
    function showEntregaDetails(id) {
      const entrega = entregas.find(e => e.id === id);
      if (!entrega) return;
      const dialog = document.getElementById('entrega-details-dialog');
      const content = document.getElementById('entrega-details-content');
      content.innerHTML = `
        <div class="space-y-6 py-4">
          <div class="flex items-center justify-between">
            <h3 class="text-lg font-semibold flex items-center gap-2">
              <i data-lucide="package" class="w-5 h-5 text-indigo-500"></i> Status da Entrega
            </h3>
            <span class="px-3 py-1 rounded-full ${statusConfig[entrega.status].color}">${statusConfig[entrega.status].label}</span>
          </div>
          <hr class="border-gray-200">
          <div>
            <h3 class="text-lg font-semibold mb-3 flex items-center gap-2">
              <i data-lucide="user" class="w-5 h-5 text-indigo-500"></i> Dados do Recebedor
            </h3>
            <div class="bg-gray-50 rounded-lg p-4 space-y-2">
              <div>
                <p class="text-sm text-gray-500">Nome do Recebedor</p>
                <p class="font-medium">${entrega.nome_recebedor}</p>
              </div>
              ${entrega.nome_legitimo_recebedor ? `
                <div>
                  <p class="text-sm text-gray-500">Nome Legítimo</p>
                  <p class="font-medium">${entrega.nome_legitimo_recebedor}</p>
                </div>
              ` : ''}
            </div>
          </div>
          <div>
            <h3 class="text-lg font-semibold mb-3 flex items-center gap-2">
              <i data-lucide="map-pin" class="w-5 h-5 text-indigo-500"></i> Endereço de Entrega
            </h3>
            <div class="bg-gray-50 rounded-lg p-4 space-y-2">
              <div>
                <p class="text-sm text-gray-500">Endereço Completo</p>
                <p class="font-medium">${entrega.endereco_completo}</p>
              </div>
              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                ${entrega.cep ? `
                  <div>
                    <p class="text-sm text-gray-500">CEP</p>
                    <p class="font-medium">${entrega.cep}</p>
                  </div>
                ` : ''}
                <div>
                  <p class="text-sm text-gray-500">Cidade/UF</p>
                  <p class="font-medium">${entrega.cidade} - ${entrega.uf}</p>
                </div>
              </div>
              ${entrega.pais ? `
                <div>
                  <p class="text-sm text-gray-500">País</p>
                  <p class="font-medium">${entrega.pais}</p>
                </div>
              ` : ''}
            </div>
          </div>
          ${(entrega.primeira_tentativa || entrega.segunda_tentativa || entrega.terceira_tentativa) ? `
            <div>
              <h3 class="text-lg font-semibold mb-3 flex items-center gap-2">
                <i data-lucide="calendar" class="w-5 h-5 text-indigo-500"></i> Tentativas de Entrega
              </h3>
              <div class="bg-gray-50 rounded-lg p-4 space-y-2">
                ${entrega.primeira_tentativa ? `
                  <div>
                    <p class="text-sm text-gray-500">1ª Tentativa</p>
                    <p class="font-medium">${new Date(entrega.primeira_tentativa).toLocaleDateString('pt-BR')}</p>
                  </div>
                ` : ''}
                ${entrega.segunda_tentativa ? `
                  <div>
                    <p class="text-sm text-gray-500">2ª Tentativa</p>
                    <p class="font-medium">${new Date(entrega.segunda_tentativa).toLocaleDateString('pt-BR')}</p>
                  </div>
                ` : ''}
                ${entrega.terceira_tentativa ? `
                  <div>
                    <p class="text-sm text-gray-500">3ª Tentativa</p>
                    <p class="font-medium">${new Date(entrega.terceira_tentativa).toLocaleDateString('pt-BR')}</p>
                  </div>
                ` : ''}
              </div>
            </div>
          ` : ''}
          <div>
            <h3 class="text-lg font-semibold mb-3 flex items-center gap-2">
              <i data-lucide="file-text" class="w-5 h-5 text-indigo-500"></i> Informações da Entrega
            </h3>
            <div class="bg-gray-50 rounded-lg p-4 space-y-2">
              ${entrega.data_entrega ? `
                <div>
                  <p class="text-sm text-gray-500">Data da Entrega</p>
                  <p class="font-medium">${new Date(entrega.data_entrega).toLocaleDateString('pt-BR')}</p>
                </div>
              ` : ''}
              ${entrega.observacoes ? `
                <div>
                  <p class="text-sm text-gray-500">Observações</p>
                  <p class="font-medium whitespace-pre-wrap">${entrega.observacoes}</p>
                </div>
              ` : ''}
            </div>
          </div>
          ${(entrega.assinatura_entregador || entrega.assinatura_recebedor) ? `
            <div>
              <h3 class="text-lg font-semibold mb-3 flex items-center gap-2">
                <i data-lucide="pen-tool" class="w-5 h-5 text-indigo-500"></i> Assinaturas
              </h3>
              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                ${entrega.assinatura_entregador ? `
                  <div class="bg-gray-50 rounded-lg p-4">
                    <p class="text-sm text-gray-500 mb-2">Assinatura do Entregador</p>
                    <img src="${entrega.assinatura_entregador}" alt="Assinatura do Entregador" class="w-full h-auto border border-gray-200 rounded">
                  </div>
                ` : ''}
                ${entrega.assinatura_recebedor ? `
                  <div class="bg-gray-50 rounded-lg p-4">
                    <p class="text-sm text-gray-500 mb-2">Assinatura do Recebedor</p>
                    <img src="${entrega.assinatura_recebedor}" alt="Assinatura do Recebedor" class="w-full h-auto border border-gray-200 rounded">
                  </div>
                ` : ''}
              </div>
            </div>
          ` : ''}
          <div class="flex flex-col sm:flex-row gap-4 mt-6">
            <button onclick="editEntrega('${entrega.id}')" class="flex-1 bg-indigo-600 text-white py-3 rounded-xl hover:bg-indigo-700 flex items-center justify-center">
              <i data-lucide="edit" class="w-4 h-4 mr-1"></i> Editar
            </button>
            <button onclick="generatePDF('${entrega.id}')" class="flex-1 bg-green-600 text-white py-3 rounded-xl hover:bg-green-700 flex items-center justify-center">
              <i data-lucide="download" class="w-4 h-4 mr-1"></i> Baixar PDF
            </button>
          </div>
        </div>
      `;
      dialog.classList.remove('hidden');
      lucide.createIcons();
    }

    // Edit entrega
    function editEntrega(id) {
      const entrega = entregas.find(e => e.id === id);
      if (!entrega) return;
      formData = { ...entrega };
      currentEditId = id;
      document.getElementById('entrega-details-dialog').classList.add('hidden');
      navigateTo('nova-entrega');
    }

    // Delete entrega
    function deleteEntrega(id) {
      if (confirm('Tem certeza que deseja excluir esta entrega?')) {
        entregas = entregas.filter(e => e.id !== id);
        localStorage.setItem('entregas', JSON.stringify(entregas));
        renderEntregasList();
      }
    }

    document.getElementById('close-dialog').addEventListener('click', () => {
      document.getElementById('entrega-details-dialog').classList.add('hidden');
    });

    // Filter entregas
    document.getElementById('search-input').addEventListener('input', renderEntregasList);
    document.getElementById('status-filter').addEventListener('change', renderEntregasList);

    // Initialize
    navigateTo('home');
  </script>
</body>
</html>
