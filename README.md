<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Smart Library Curator – Người Giám Tuyến Thư Viện 4.0</title>
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Google Fonts: Inter & Merriweather for library warmth -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Merriweather:ital,wght@0,700;1,400&display=swap" rel="stylesheet">
  <!-- Font Awesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css" />

  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'sans-serif'],
            serif: ['Merriweather', 'serif'],
          },
          colors: {
            ivory: '#FAF8F5',
            cream: '#F3EFEA',
            forest: {
              DEFAULT: '#1B4332',
              light: '#2D6A4F',
              dark: '#081C15',
            },
            amberwarm: {
              DEFAULT: '#D97706',
              light: '#FEF3C7',
              dark: '#B45309',
            }
          }
        }
      }
    }
  </script>
  <style>
    /* Custom subtle scrollbar */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: #F3EFEA;
    }
    ::-webkit-scrollbar-thumb {
      background: #CBD5E1;
      border-radius: 9999px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #94A3B8;
    }
    .typing-dot {
      animation: typing 1.4s infinite ease-in-out both;
    }
    .typing-dot:nth-child(1) { animation-delay: -0.32s; }
    .typing-dot:nth-child(2) { animation-delay: -0.16s; }
    @keyframes typing {
      0%, 80%, 100% { transform: scale(0); }
      40% { transform: scale(1); }
    }
  </style>
</head>
<body class="bg-ivory text-slate-800 font-sans min-h-screen flex flex-col">

  <!-- Toast Notification Banner (No alert() used) -->
  <div id="toastBanner" class="fixed bottom-5 right-5 z-50 hidden transform transition-all duration-300 translate-y-4 opacity-0 max-w-sm">
    <div class="bg-forest text-white px-4 py-3 rounded-xl shadow-lg flex items-center gap-3 border border-forest-light">
      <i id="toastIcon" class="fa-solid fa-circle-check text-amber-400 text-lg"></i>
      <p id="toastMessage" class="text-sm font-medium"></p>
    </div>
  </div>

  <!-- Top Header -->
  <header class="bg-forest text-white shadow-md sticky top-0 z-40">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4">
      <div class="flex flex-col md:flex-row md:items-center md:justify-between gap-4">
        <div class="flex items-start sm:items-center gap-3">
          <div class="bg-amberwarm p-3 rounded-xl shadow-inner shrink-0">
            <i class="fa-solid fa-book-open-reader text-white text-xl sm:text-2xl"></i>
          </div>
          <div>
            <div class="flex flex-wrap items-center gap-2">
              <h1 class="font-serif text-lg sm:text-xl lg:text-2xl font-bold tracking-tight text-white">
                Smart Library Curator – Người Giám Tuyến Thư Viện 4.0
              </h1>
              <span class="bg-forest-light text-emerald-100 text-xs font-semibold px-2.5 py-0.5 rounded-full border border-emerald-400/30">
                Tin học 10 • Dự án 2
              </span>
            </div>
            <p class="text-emerald-100/90 text-xs sm:text-sm mt-1">
              Hệ thống gợi ý sách thông minh tích hợp tiêu chuẩn đạo đức AI (FATE) &amp; Phá vỡ Bong bóng lọc
            </p>
          </div>
        </div>

        <!-- Live Catalog Counter Badge -->
        <div class="flex items-center gap-2 self-start md:self-auto bg-forest-dark/60 px-3.5 py-2 rounded-xl border border-emerald-500/30">
          <span class="w-2.5 h-2.5 rounded-full bg-emerald-400 animate-pulse"></span>
          <span class="text-xs sm:text-sm text-emerald-100 font-medium">
            Kho dữ liệu kín: <strong id="headerBookCount" class="text-amber-300 font-bold">20</strong> đầu sách đã duyệt
          </span>
        </div>
      </div>

      <!-- Navigation Tabs -->
      <nav class="flex flex-wrap gap-2 mt-4 pt-3 border-t border-emerald-800/80" aria-label="Tabs">
        <button onclick="switchTab('chatbot')" id="tab-btn-chatbot"
          class="tab-btn flex items-center gap-2 px-4 py-2.5 rounded-xl text-sm font-semibold transition-all bg-white text-forest shadow-sm">
          <i class="fa-solid fa-robot text-amberwarm"></i>
          <span>1. Trợ lý Gợi ý Sách (AI Curator)</span>
        </button>

        <button onclick="switchTab('catalog')" id="tab-btn-catalog"
          class="tab-btn flex items-center gap-2 px-4 py-2.5 rounded-xl text-sm font-semibold transition-all text-emerald-100 hover:bg-forest-light/60">
          <i class="fa-solid fa-books fa-solid fa-layer-group"></i>
          <span>2. Kho Dữ Liệu Sách (<span id="tabBookCount">20</span> Đầu Sách)</span>
        </button>

        <button onclick="switchTab('fate')" id="tab-btn-fate"
          class="tab-btn flex items-center gap-2 px-4 py-2.5 rounded-xl text-sm font-semibold transition-all text-emerald-100 hover:bg-forest-light/60">
          <i class="fa-solid fa-scale-balanced"></i>
          <span>3. Tiêu chuẩn FATE &amp; Giám sát</span>
        </button>
      </nav>
    </div>
  </header>

  <!-- Main Content Area -->
  <main class="flex-1 max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6">

    <!-- ========================================================= -->
    <!-- TAB 1: AI CURATOR CHATBOT                                 -->
    <!-- ========================================================= -->
    <section id="tab-chatbot" class="tab-content grid grid-cols-1 lg:grid-cols-12 gap-6">
      
      <!-- Left/Main Panel: Chat Interface -->
      <div class="lg:col-span-8 flex flex-col bg-white rounded-2xl shadow-sm border border-stone-200 overflow-hidden h-[75vh] min-h-[540px]">
        
        <!-- Chat Top Bar -->
        <div class="bg-cream px-5 py-3.5 border-b border-stone-200 flex flex-wrap items-center justify-between gap-2">
          <div class="flex items-center gap-2.5">
            <div class="w-9 h-9 rounded-full bg-forest text-white flex items-center justify-center font-bold shadow-sm">
              <i class="fa-solid fa-wand-magic-sparkles text-amber-300"></i>
            </div>
            <div>
              <h2 class="text-sm font-bold text-forest">Trợ lý Giám Tuyến Thư Viện (FATE-Verified)</h2>
              <p class="text-xs text-slate-500">Trả về 3 sách đúng sở thích + 1 sách mở rộng phá vỡ bong bóng lọc</p>
            </div>
          </div>
          <div class="flex items-center gap-2">
            <span id="responseTimerBadge" class="hidden text-xs bg-emerald-50 text-forest px-2.5 py-1 rounded-lg border border-emerald-200 font-medium">
              <i class="fa-regular fa-clock mr-1"></i><span id="lastResponseTime">0.0s</span>
            </span>
            <button onclick="resetChatSession()" title="Xóa phiên làm việc (Bảo vệ quyền riêng tư)"
              class="text-xs bg-white hover:bg-rose-50 text-slate-600 hover:text-rose-700 px-3 py-1.5 rounded-lg border border-stone-300 transition flex items-center gap-1.5 font-medium">
              <i class="fa-solid fa-eraser"></i>
              <span>Xóa phiên làm việc</span>
            </button>
          </div>
        </div>

        <!-- Chat Messages Scroll Container -->
        <div id="chatMessages" class="flex-1 overflow-y-auto p-4 sm:p-6 space-y-5 bg-ivory/50">
          <!-- Welcome Message -->
          <div class="flex items-start gap-3">
            <div class="w-8 h-8 rounded-full bg-forest text-white flex items-center justify-center shrink-0 mt-1">
              <i class="fa-solid fa-book-bookmark text-xs text-amber-300"></i>
            </div>
            <div class="bg-white border border-stone-200 rounded-2xl rounded-tl-none p-4 shadow-sm max-w-2xl">
              <p class="text-sm text-slate-700 leading-relaxed">
                Xin chào! Mình là <strong>Người Giám Tuyến Thư Viện 4.0</strong>. Hãy nhập sở thích đọc sách của bạn (thể loại, chủ đề, tâm trạng hoặc bài học bạn muốn tìm).
              </p>
              <div class="mt-3 p-3 bg-amberwarm-light/50 border border-amber-200 rounded-xl text-xs text-slate-700 space-y-1">
                <p class="font-semibold text-amberwarm-dark"><i class="fa-solid fa-shield-halved mr-1"></i>Cam kết FATE khi gợi ý:</p>
                <ul class="list-disc list-inside space-y-0.5 text-slate-600">
                  <li>Chỉ chọn sách từ <strong>kho dữ liệu đã được nhóm kiểm duyệt</strong> (không bịa tên sách).</li>
                  <li>Gợi ý <strong>3 cuốn đúng sở thích</strong> + <strong>1 cuốn mở rộng</strong> giúp bạn tránh "bong bóng lọc".</li>
                  <li>Không lưu trữ thông tin cá nhân hay lịch sử đọc của học sinh.</li>
                </ul>
              </div>
            </div>
          </div>
        </div>

        <!-- Quick Test Prompt Chips -->
        <div class="px-4 py-2.5 bg-cream/70 border-t border-stone-200">
          <p class="text-[11px] font-semibold uppercase tracking-wider text-slate-500 mb-2">
            <i class="fa-solid fa-bolt text-amberwarm mr-1"></i>Mẫu sở thích chạy thử nhanh (Click để gửi):
          </p>
          <div class="flex flex-wrap gap-1.5">
            <button onclick="sendQuickPrompt('Mình thích sách liên quan đến tội phạm, bí ẩn có sự hồi hộp')"
              class="text-xs bg-white hover:bg-forest hover:text-white text-slate-700 border border-stone-300 px-3 py-1.5 rounded-full transition shadow-2xs text-left">
              🔍 Tội phạm, bí ẩn &amp; hồi hộp
            </button>
            <button onclick="sendQuickPrompt('Mình muốn tìm sách về khoa học, vũ trụ và lịch sử nhân loại')"
              class="text-xs bg-white hover:bg-forest hover:text-white text-slate-700 border border-stone-300 px-3 py-1.5 rounded-full transition shadow-2xs text-left">
              🌌 Khoa học, vũ trụ &amp; lịch sử
            </button>
            <button onclick="sendQuickPrompt('Mình thích truyện nhẹ nhàng, tuổi thơ và triết lý sống sâu sắc')"
              class="text-xs bg-white hover:bg-forest hover:text-white text-slate-700 border border-stone-300 px-3 py-1.5 rounded-full transition shadow-2xs text-left">
              🌱 Nhẹ nhàng, tuổi thơ &amp; triết lý
            </button>
            <button onclick="sendQuickPrompt('Mình cần sách kỹ năng sống, dám vượt qua vùng an toàn của bản thân')"
              class="text-xs bg-white hover:bg-forest hover:text-white text-slate-700 border border-stone-300 px-3 py-1.5 rounded-full transition shadow-2xs text-left">
              🚀 Kỹ năng sống &amp; vượt vùng an toàn
            </button>
          </div>
        </div>

        <!-- Chat Input Bar -->
        <form id="chatForm" onsubmit="handleChatSubmit(event)" class="p-3 sm:p-4 bg-white border-t border-stone-200 flex items-center gap-2.5">
          <input type="text" id="userPreferenceInput" required
            placeholder="Nhập sở thích đọc sách của bạn (VD: Mình thích truyện phiêu lưu, khám phá thiên nhiên)..."
            class="flex-1 bg-ivory border border-stone-300 focus:border-forest focus:ring-2 focus:ring-forest/20 rounded-xl px-4 py-3 text-sm outline-none transition" />
          <button type="submit" id="sendBtn"
            class="bg-forest hover:bg-forest-light text-white font-semibold px-5 py-3 rounded-xl shadow-sm transition flex items-center gap-2 shrink-0">
            <span>Gợi ý sách</span>
            <i class="fa-solid fa-paper-plane text-xs"></i>
          </button>
        </form>
      </div>

      <!-- Right Side Panel: Live FATE Verification & Privacy Dashboard -->
      <aside class="lg:col-span-4 space-y-5">
        
        <!-- Live FATE Status Card -->
        <div class="bg-white rounded-2xl shadow-sm border border-stone-200 p-5">
          <div class="flex items-center justify-between border-b border-stone-100 pb-3 mb-4">
            <h3 class="font-serif font-bold text-forest text-base flex items-center gap-2">
              <i class="fa-solid fa-shield-check text-emerald-600"></i>
              <span>Bảng Kiểm Soát FATE Trực Tiếp</span>
            </h3>
            <span class="bg-emerald-100 text-emerald-800 text-[11px] font-bold px-2.5 py-0.5 rounded-full">
              ACTIVE
            </span>
          </div>

          <div class="space-y-3.5 text-sm">
            <div class="p-3 rounded-xl bg-emerald-50/70 border border-emerald-200/80">
              <div class="flex items-center gap-2 font-semibold text-forest">
                <i class="fa-solid fa-lock text-emerald-600"></i>
                <span>Khóa dữ liệu kín (Accountability)</span>
              </div>
              <p class="text-xs text-slate-600 mt-1 leading-relaxed">
                Hệ thống chỉ truy xuất từ <strong id="sideBookCount">20</strong> đầu sách đã qua kiểm duyệt của nhóm. Cấm tuyệt đối hành vi tự bịa sách ngoài kho (0% Hallucination).
              </p>
            </div>

            <div class="p-3 rounded-xl bg-amberwarm-light/50 border border-amber-200">
              <div class="flex items-center gap-2 font-semibold text-amberwarm-dark">
                <i class="fa-solid fa-compass text-amberwarm"></i>
                <span>Phá vỡ Bong bóng lọc (Fairness)</span>
              </div>
              <p class="text-xs text-slate-600 mt-1 leading-relaxed">
                Áp dụng quy tắc <strong>3 + 1 (Serendipity Rule)</strong>: 3 cuốn bám sát sở thích + 1 cuốn mở rộng sang thể loại mới có điểm chạm tích cực.
              </p>
            </div>

            <div class="p-3 rounded-xl bg-sky-50/70 border border-sky-200">
              <div class="flex items-center gap-2 font-semibold text-sky-900">
                <i class="fa-solid fa-eye text-sky-600"></i>
                <span>Minh bạch &amp; Nguồn gốc (Transparency)</span>
              </div>
              <p class="text-xs text-slate-600 mt-1 leading-relaxed">
                Mọi gợi ý đều hiển thị rõ <strong>ID sách</strong>, <strong>lý do phù hợp</strong> dựa trên tóm tắt chuẩn hóa và <strong>nguồn trích dẫn</strong>.
              </p>
            </div>

            <div class="p-3 rounded-xl bg-purple-50/70 border border-purple-200">
              <div class="flex items-center gap-2 font-semibold text-purple-900">
                <i class="fa-solid fa-user-shield text-purple-600"></i>
                <span>Bảo vệ Quyền riêng tư (Ethics)</span>
              </div>
              <p class="text-xs text-slate-600 mt-1 leading-relaxed">
                Không thu thập họ tên, lớp hay lưu lịch sử đọc lên máy chủ. Nhấn <strong>"Xóa phiên làm việc"</strong> để xóa toàn bộ dữ liệu tạm trên trình duyệt.
              </p>
            </div>
          </div>
        </div>

        <!-- Human-in-the-loop Reminder Card -->
        <div class="bg-forest text-white rounded-2xl shadow-sm p-5">
          <div class="flex items-center gap-2.5 mb-2">
            <span class="bg-amberwarm text-white text-xs font-bold px-2.5 py-1 rounded-lg">Mức 3: Co-pilot</span>
            <h4 class="font-bold text-sm">Ranh giới Người – Máy</h4>
          </div>
          <p class="text-xs text-emerald-100 leading-relaxed">
            AI đóng vai trò <strong>trợ lý lọc thông tin và đề xuất tham khảo</strong>. Người đọc giữ toàn quyền quyết định chọn sách, và thủ thư/học sinh kiểm soát chất lượng kho dữ liệu đầu vào.
          </p>
        </div>
      </aside>
    </section>

    <!-- ========================================================= -->
    <!-- TAB 2: CATALOG MANAGER (20 BOOKS + SCALABILITY DEMO)      -->
    <!-- ========================================================= -->
    <section id="tab-catalog" class="tab-content hidden space-y-6">
      
      <!-- Top Controls: Search, Genre Filter & Add Book Button -->
      <div class="bg-white rounded-2xl shadow-sm border border-stone-200 p-5">
        <div class="flex flex-col md:flex-row md:items-center justify-between gap-4 pb-4 border-b border-stone-100">
          <div>
            <h2 class="font-serif text-lg font-bold text-forest">
              Kho Dữ Liệu Sách Chuẩn Hóa (<span id="catalogHeaderCount">20</span> Đầu Sách)
            </h2>
            <p class="text-xs sm:text-sm text-slate-500 mt-0.5">
              Tóm tắt được biên tập lại cô đọng (50–80 chữ) kèm nguồn trích dẫn nhằm tôn trọng bản quyền (FATE – Ethics).
            </p>
          </div>
          <button onclick="openAddBookModal()"
            class="bg-amberwarm hover:bg-amberwarm-dark text-white font-semibold text-sm px-4 py-2.5 rounded-xl shadow-sm transition flex items-center gap-2 self-start md:self-auto">
            <i class="fa-solid fa-plus"></i>
            <span>Thêm sách mới (Test Khả năng mở rộng)</span>
          </button>
        </div>

        <!-- Search & Filter Inputs -->
        <div class="grid grid-cols-1 sm:grid-cols-3 gap-3 mt-4">
          <div class="sm:col-span-2 relative">
            <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-1/2 -translate-y-1/2 text-slate-400 text-sm"></i>
            <input type="text" id="catalogSearchInput" oninput="renderCatalog()"
              placeholder="Tìm theo tên sách, tác giả, từ khóa sở thích..."
              class="w-full bg-ivory border border-stone-300 focus:border-forest rounded-xl pl-10 pr-4 py-2.5 text-sm outline-none" />
          </div>
          <div>
            <select id="catalogGenreFilter" onchange="renderCatalog()"
              class="w-full bg-ivory border border-stone-300 focus:border-forest rounded-xl px-3.5 py-2.5 text-sm outline-none">
              <option value="ALL">Tất cả thể loại</option>
              <option value="Trinh thám">Trinh thám / Tâm lý tội phạm</option>
              <option value="Văn học">Văn học / Tiểu thuyết</option>
              <option value="Kỹ năng">Kỹ năng sống / Giao tiếp</option>
              <option value="Tâm lý">Tâm lý học / Phát triển bản thân</option>
              <option value="Khoa học">Khoa học / Lịch sử / Viễn tưởng</option>
              <option value="Giáo dục">Giáo dục / Tự truyện</option>
            </select>
          </div>
        </div>
      </div>

      <!-- Responsive Book Catalog Table / Cards -->
      <div class="bg-white rounded-2xl shadow-sm border border-stone-200 overflow-hidden">
        <div class="overflow-x-auto">
          <table class="w-full text-left border-collapse">
            <thead>
              <tr class="bg-forest text-white text-xs uppercase tracking-wider">
                <th class="py-3.5 px-4 font-semibold w-14">ID</th>
                <th class="py-3.5 px-4 font-semibold min-w-[180px]">Tên sách &amp; Tác giả</th>
                <th class="py-3.5 px-4 font-semibold min-w-[150px]">Thể loại</th>
                <th class="py-3.5 px-4 font-semibold min-w-[180px]">Từ khóa sở thích</th>
                <th class="py-3.5 px-4 font-semibold min-w-[300px]">Tóm tắt chuẩn hóa (50–80 chữ)</th>
                <th class="py-3.5 px-4 font-semibold min-w-[160px]">Nguồn trích dẫn</th>
              </tr>
            </thead>
            <tbody id="catalogTableBody" class="divide-y divide-stone-200 text-sm">
              <!-- Populated dynamically by JS -->
            </tbody>
          </table>
        </div>
        <div id="emptyCatalogState" class="hidden p-10 text-center text-slate-500">
          <i class="fa-solid fa-book-open text-3xl text-slate-300 mb-2"></i>
          <p class="text-sm font-medium">Không tìm thấy cuốn sách nào phù hợp với từ khóa lọc.</p>
        </div>
      </div>
    </section>

    <!-- ========================================================= -->
    <!-- TAB 3: FATE ETHICS & HUMAN-IN-THE-LOOP DASHBOARD          -->
    <!-- ========================================================= -->
    <section id="tab-fate" class="tab-content hidden space-y-6">
      
      <!-- FATE 4 Pillars Grid -->
      <div class="bg-white rounded-2xl shadow-sm border border-stone-200 p-6">
        <div class="mb-5">
          <span class="bg-amberwarm-light text-amberwarm-dark text-xs font-bold px-3 py-1 rounded-full">
            Đạo đức AI trong Giáo dục
          </span>
          <h2 class="font-serif text-xl font-bold text-forest mt-2">
            Khung Tiêu Chuẩn Đạo Đức FATE Trong Hệ Thống
          </h2>
          <p class="text-sm text-slate-600 mt-1">
            Thiết kế giải quyết trực tiếp các rủi ro của hệ thống gợi ý sách tự động (bong bóng lọc, thiên kiến trào lưu, vi phạm bản quyền và lộ dữ liệu).
          </p>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <!-- F - Fairness -->
          <div class="p-5 rounded-2xl bg-ivory border border-stone-200">
            <div class="flex items-center gap-3 mb-2">
              <div class="w-10 h-10 rounded-xl bg-amberwarm text-white flex items-center justify-center font-bold text-lg">F</div>
              <div>
                <h3 class="font-bold text-forest">Fairness (Công bằng &amp; Chống thiên kiến)</h3>
                <p class="text-xs text-slate-500">Chống lệch theo trào lưu &amp; Bong bóng lọc</p>
              </div>
            </div>
            <p class="text-sm text-slate-700 leading-relaxed mt-2">
              Không chỉ gợi ý các sách bán chạy nhất. Áp dụng <strong>quy tắc 3 + 1</strong>: mỗi lượt gợi ý gồm 3 sách đúng sở thích và <strong>1 sách mở rộng thể loại</strong> để khuyến khích học sinh khám phá tri thức đa dạng.
            </p>
          </div>

          <!-- A - Accountability -->
          <div class="p-5 rounded-2xl bg-ivory border border-stone-200">
            <div class="flex items-center gap-3 mb-2">
              <div class="w-10 h-10 rounded-xl bg-forest text-white flex items-center justify-center font-bold text-lg">A</div>
              <div>
                <h3 class="font-bold text-forest">Accountability (Trách nhiệm giải trình)</h3>
                <p class="text-xs text-slate-500">Con người kiểm soát đầu vào (Human-in-the-loop)</p>
              </div>
            </div>
            <p class="text-sm text-slate-700 leading-relaxed mt-2">
              Nhóm học sinh và thủ thư kiểm duyệt 100% danh mục sách trước khi nạp vào hệ thống. AI bị khóa chặt trong kho dữ liệu này, loại bỏ hoàn toàn rủi ro AI tự bịa đặt thông tin sai lệch.
            </p>
          </div>

          <!-- T - Transparency -->
          <div class="p-5 rounded-2xl bg-ivory border border-stone-200">
            <div class="flex items-center gap-3 mb-2">
              <div class="w-10 h-10 rounded-xl bg-sky-700 text-white flex items-center justify-center font-bold text-lg">T</div>
              <div>
                <h3 class="font-bold text-forest">Transparency (Tính minh bạch)</h3>
                <p class="text-xs text-slate-500">Giải thích rõ ràng &amp; Trích dẫn nguồn</p>
              </div>
            </div>
            <p class="text-sm text-slate-700 leading-relaxed mt-2">
              Thay vì đưa ra danh sách "hộp đen", hệ thống luôn hiển thị <strong>1–2 câu lý do gợi ý</strong> gắn với nội dung tóm tắt và ghi rõ nguồn trích dẫn (Wikipedia, NXB Trẻ, Nhã Nam, Kim Đồng...).
            </p>
          </div>

          <!-- E - Ethics -->
          <div class="p-5 rounded-2xl bg-ivory border border-stone-200">
            <div class="flex items-center gap-3 mb-2">
              <div class="w-10 h-10 rounded-xl bg-purple-700 text-white flex items-center justify-center font-bold text-lg">E</div>
              <div>
                <h3 class="font-bold text-forest">Ethics (Đạo đức, Bản quyền &amp; Riêng tư)</h3>
                <p class="text-xs text-slate-500">Tóm tắt chuẩn hóa &amp; Không lưu lịch sử đọc</p>
              </div>
            </div>
            <p class="text-sm text-slate-700 leading-relaxed mt-2">
              Tóm tắt được viết lại ngắn gọn (50–80 chữ) kèm nguồn để tôn trọng bản quyền tác giả. Hệ thống không yêu cầu đăng nhập và không lưu trữ lịch sử tìm kiếm cá nhân của học sinh.
            </p>
          </div>
        </div>
      </div>

      <!-- Critical Reflection: Human-Machine Boundary & 5 Evaluation Criteria -->
      <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
        
        <!-- Automation Level & Boundary -->
        <div class="lg:col-span-5 bg-white rounded-2xl shadow-sm border border-stone-200 p-6 flex flex-col justify-between">
          <div>
            <h3 class="font-serif text-lg font-bold text-forest mb-3">
              Ranh Giới Linh Hoạt Người – Máy (Mức 3: Co-pilot)
            </h3>
            <div class="space-y-3 text-sm">
              <div class="p-3.5 rounded-xl bg-cream border border-stone-200">
                <p class="font-bold text-forest mb-1"><i class="fa-solid fa-microchip text-amberwarm mr-1.5"></i>Phần việc của AI (Máy):</p>
                <p class="text-slate-600 text-xs leading-relaxed">
                  Phân tích từ khóa sở thích, đối chiếu nhanh kho dữ liệu tóm tắt, lọc ra 3 cuốn phù hợp + 1 cuốn mở rộng và soạn lời giải thích mạch lạc.
                </p>
              </div>
              <div class="p-3.5 rounded-xl bg-emerald-50 border border-emerald-200">
                <p class="font-bold text-forest mb-1"><i class="fa-solid fa-user-check text-emerald-700 mr-1.5"></i>Phần việc của Con người:</p>
                <p class="text-slate-600 text-xs leading-relaxed">
                  <strong>Thủ thư / Nhóm dự án:</strong> Kiểm duyệt nội dung, bản quyền và độ tuổi phù hợp của sách đầu vào.<br/>
                  <strong>Người đọc:</strong> Nắm quyền quyết định cuối cùng trong việc lựa chọn cuốn sách để đọc.
                </p>
              </div>
            </div>
          </div>

          <div class="mt-4 pt-4 border-t border-stone-100 text-xs text-slate-500">
            <strong>Tính linh hoạt:</strong> Với sách giải trí, AI gợi ý rộng; với sách học tập hoặc học sinh nhỏ tuổi, hệ thống ưu tiên định hướng trực tiếp từ giáo viên/thủ thư.
          </div>
        </div>

        <!-- 5 Evaluation Criteria Summary -->
        <div class="lg:col-span-7 bg-white rounded-2xl shadow-sm border border-stone-200 p-6">
          <h3 class="font-serif text-lg font-bold text-forest mb-3">
            Bộ 5 Tiêu Chí Đánh Giá Hệ Thống Gợi Ý (Mục tiêu 4)
          </h3>
          <div class="overflow-x-auto">
            <table class="w-full text-left border-collapse text-xs sm:text-sm">
              <thead>
                <tr class="bg-cream text-forest border-b border-stone-200">
                  <th class="py-2.5 px-3 font-bold">Tiêu chí</th>
                  <th class="py-2.5 px-3 font-bold text-center">Trọng số</th>
                  <th class="py-2.5 px-3 font-bold">Ngưỡng đạt (V2)</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-stone-100">
                <tr>
                  <td class="py-2.5 px-3 font-medium">1. Độ phù hợp theo người đọc</td>
                  <td class="py-2.5 px-3 text-center font-semibold text-amberwarm-dark">30%</td>
                  <td class="py-2.5 px-3 text-emerald-700 font-medium">≥ 80% (Thực tế V2: 90%)</td>
                </tr>
                <tr>
                  <td class="py-2.5 px-3 font-medium">2. Độ chính xác theo thể loại &amp; Kho kín</td>
                  <td class="py-2.5 px-3 text-center font-semibold text-amberwarm-dark">25%</td>
                  <td class="py-2.5 px-3 text-emerald-700 font-medium">100% thuộc kho, ≥ 85% đúng thể loại</td>
                </tr>
                <tr>
                  <td class="py-2.5 px-3 font-medium">3. Độ đa dạng (Chống bong bóng lọc)</td>
                  <td class="py-2.5 px-3 text-center font-semibold text-amberwarm-dark">20%</td>
                  <td class="py-2.5 px-3 text-emerald-700 font-medium">≥ 10–12 cuốn khác nhau / 20 lượt</td>
                </tr>
                <tr>
                  <td class="py-2.5 px-3 font-medium">4. Thời gian phản hồi</td>
                  <td class="py-2.5 px-3 text-center font-semibold text-amberwarm-dark">15%</td>
                  <td class="py-2.5 px-3 text-emerald-700 font-medium">≤ 10 giây / lượt truy vấn</td>
                </tr>
                <tr>
                  <td class="py-2.5 px-3 font-medium">5. Khả năng mở rộng kho sách</td>
                  <td class="py-2.5 px-3 text-center font-semibold text-amberwarm-dark">10%</td>
                  <td class="py-2.5 px-3 text-emerald-700 font-medium">≤ 2 phút thêm sách mới (Tab 2)</td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </section>

  </main>

  <!-- ========================================================= -->
  <!-- MODAL: ADD NEW BOOK (SCALABILITY & HUMAN APPROVAL DEMO)   -->
  <!-- ========================================================= -->
  <div id="addBookModal" class="fixed inset-0 z-50 hidden bg-slate-900/60 backdrop-blur-xs flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl shadow-xl max-w-lg w-full overflow-hidden border border-stone-200">
      <div class="bg-forest text-white px-6 py-4 flex items-center justify-between">
        <h3 class="font-serif font-bold text-base">
          <i class="fa-solid fa-book-medical text-amber-300 mr-2"></i>Thêm Sách Mới Vào Kho (Kiểm duyệt FATE)
        </h3>
        <button onclick="closeAddBookModal()" class="text-emerald-200 hover:text-white text-lg">
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>

      <form id="addBookForm" onsubmit="handleAddBook(event)" class="p-6 space-y-4 text-sm">
        <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
          <div>
            <label class="block font-semibold text-slate-700 mb-1">Tên sách *</label>
            <input type="text" id="newBookTitle" required placeholder="VD: Mắt biếc"
              class="w-full bg-ivory border border-stone-300 rounded-xl px-3.5 py-2 outline-none focus:border-forest" />
          </div>
          <div>
            <label class="block font-semibold text-slate-700 mb-1">Tác giả *</label>
            <input type="text" id="newBookAuthor" required placeholder="VD: Nguyễn Nhật Ánh"
              class="w-full bg-ivory border border-stone-300 rounded-xl px-3.5 py-2 outline-none focus:border-forest" />
          </div>
        </div>

        <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
          <div>
            <label class="block font-semibold text-slate-700 mb-1">Thể loại *</label>
            <input type="text" id="newBookGenre" required placeholder="VD: Văn học Việt Nam / Tình cảm"
              class="w-full bg-ivory border border-stone-300 rounded-xl px-3.5 py-2 outline-none focus:border-forest" />
          </div>
          <div>
            <label class="block font-semibold text-slate-700 mb-1">Từ khóa sở thích *</label>
            <input type="text" id="newBookKeywords" required placeholder="VD: tuổi học trò, kỷ niệm, cảm động"
              class="w-full bg-ivory border border-stone-300 rounded-xl px-3.5 py-2 outline-none focus:border-forest" />
          </div>
        </div>

        <div>
          <label class="block font-semibold text-slate-700 mb-1">Tóm tắt chuẩn hóa (50–80 chữ) *</label>
          <textarea id="newBookSummary" rows="3" required
            placeholder="Viết tóm tắt ngắn gọn nội dung chính, không tiết lộ cái kết..."
            class="w-full bg-ivory border border-stone-300 rounded-xl px-3.5 py-2 outline-none focus:border-forest"></textarea>
        </div>

        <div>
          <label class="block font-semibold text-slate-700 mb-1">Nguồn trích dẫn uy tín *</label>
          <input type="text" id="newBookSource" required placeholder="VD: Wikipedia Tiếng Việt / NXB Trẻ"
            class="w-full bg-ivory border border-stone-300 rounded-xl px-3.5 py-2 outline-none focus:border-forest" />
        </div>

        <!-- Mandatory Human-in-the-loop Gate Checkbox -->
        <label class="flex items-start gap-2.5 p-3 rounded-xl bg-amberwarm-light/60 border border-amber-200 cursor-pointer">
          <input type="checkbox" id="humanApprovalCheck" required class="mt-1 accent-forest w-4 h-4" />
          <span class="text-xs text-slate-700 leading-relaxed">
            <strong>Xác nhận kiểm duyệt của con người (Human-in-the-loop):</strong> Tôi xác nhận bản tóm tắt này đã được kiểm tra tính chính xác, phù hợp lứa tuổi học sinh THPT và ghi rõ nguồn trích dẫn hợp lệ.
          </span>
        </label>

        <div class="flex justify-end gap-2 pt-2">
          <button type="button" onclick="closeAddBookModal()"
            class="px-4 py-2 rounded-xl border border-stone-300 text-slate-600 hover:bg-stone-100 font-medium">
            Hủy
          </button>
          <button type="submit"
            class="px-5 py-2 rounded-xl bg-forest hover:bg-forest-light text-white font-semibold shadow-sm">
            Lưu vào kho dữ liệu
          </button>
        </div>
      </form>
    </div>
  </div>

  <!-- Footer -->
  <footer class="bg-cream border-t border-stone-200 py-4 px-4 text-center text-xs text-slate-500">
    Smart Library Curator – Người Giám Tuyến Thư Viện 4.0 • Dự án Tin học 10 • Vận hành theo khung đạo đức AI (FATE) &amp; Human-in-the-loop
  </footer>

  <!-- ========================================================= -->
  <!-- JAVASCRIPT LOGIC: DATA, GEMINI API & LOCAL FALLBACK ENGINE -->
  <!-- ========================================================= -->
  <script>
    // 1. Initial 20 FATE-Verified Books Catalog
    const initialBooks = [
      {
        id: "01",
        title: "Nhà giả kim",
        author: "Paulo Coelho",
        genre: "Tiểu thuyết / Phiêu lưu",
        keywords: "hành trình, ước mơ, khám phá bản thân, triết lý sống",
        summary: "Chàng chăn cừu Tây Ban Nha Santiago từ bỏ cuộc sống bình yên để lên đường tới Kim Tự Tháp Ai Cập tìm kho báu trong giấc mơ. Qua nhiều thử thách, gặp gỡ nhà giả kim và học cách lắng nghe \"Ngôn ngữ của vũ trụ\", cậu nhận ra kho báu thực sự nằm ở ngay nơi mình bắt đầu.",
        source: "Dữ liệu nhóm tổng hợp (NXB Nhã Nam)"
      },
      {
        id: "02",
        title: "Ngày xưa có một con bò",
        author: "Camilo Cruz",
        genre: "Kỹ năng sống / Ngụ ngôn",
        keywords: "thoát khỏi vùng an toàn, thay đổi tư duy, vượt lười biếng",
        summary: "Câu chuyện ngụ ngôn về hai thầy trò đến thăm một gia đình nghèo sống bám vào con bò sữa duy nhất. Khi người thầy bất ngờ giết chết con bò, gia đình buộc phải tự khai hoang trồng trọt và trở nên khấm khá. Sách nhắc nhở con người dám từ bỏ \"con bò\" an toàn để bứt phá.",
        source: "Dữ liệu nhóm tổng hợp (NXB Trẻ)"
      },
      {
        id: "03",
        title: "Đi tìm lẽ sống",
        author: "Viktor E. Frankl",
        genre: "Tâm lý học / Hồi ký",
        keywords: "nghị lực sống, vượt nghịch cảnh, ý nghĩa cuộc đời, tâm lý",
        summary: "Bác sĩ tâm thần Viktor Frankl kể lại những trải nghiệm khắc nghiệt trong trại tập trung thời Thế chiến II và giới thiệu \"Liệu pháp ý nghĩa\". Cuốn sách chứng minh rằng dù trong hoàn cảnh bi đát nhất, con người vẫn có tự do lựa chọn thái độ sống và tìm thấy sức mạnh tinh thần để sinh tồn.",
        source: "Dữ liệu nhóm tổng hợp (First News)"
      },
      {
        id: "04",
        title: "Đất rừng phương Nam",
        author: "Đoàn Giỏi",
        genre: "Văn học thiếu nhi / Phiêu lưu",
        keywords: "thiên nhiên Nam Bộ, lòng yêu nước, tuổi thơ, phiêu lưu",
        summary: "Lấy bối cảnh miền Tây Nam Bộ thời kháng chiến chống Pháp, truyện theo chân cậu bé An bị lạc gia đình trong cuộc tản cư. Được người dân Nam Bộ cưu mang, An trải nghiệm cuộc sống mưu sinh giữa rừng U Minh hùng vĩ, câu rắn, lấy mật ong và trưởng thành qua khói lửa chiến tranh.",
        source: "Dữ liệu nhóm tổng hợp (NXB Kim Đồng)"
      },
      {
        id: "05",
        title: "Thép đã tôi thế đấy",
        author: "Nikolai Ostrovsky",
        genre: "Tiểu thuyết / Lịch sử",
        keywords: "lý tưởng sống, tuổi trẻ, kiên cường, cách mạng",
        summary: "Tác phẩm sử thi xoay quanh cuộc đời chiến sĩ cách mạng trẻ tuổi Paven Korchagin đầy nhiệt huyết và bất khuất. Vượt qua chiến tranh tàn khốc, bệnh tật và mù lòa, Paven vẫn giữ vững lý tưởng cao đẹp, cống hiến trọn vẹn tuổi thanh xuân cho sự nghiệp giải phóng đất nước.",
        source: "Dữ liệu nhóm tổng hợp (NXB Văn Học)"
      },
      {
        id: "06",
        title: "Sức mạnh tiềm thức",
        author: "Joseph Murphy",
        genre: "Tâm lý / Phát triển bản thân",
        keywords: "tư duy tích cực, thay đổi thói quen, khai phá nội lực",
        summary: "Cuốn sách phân tích cơ chế hoạt động của ý thức và tiềm thức trong tâm trí con người. Thông qua các phương pháp thực hành như tự kỷ ám thị và hình dung tích cực, tác giả hướng dẫn người đọc loại bỏ niềm tin tiêu cực để cải thiện sức khỏe, công việc và hạnh phúc cá nhân.",
        source: "Dữ liệu nhóm tổng hợp (First News)"
      },
      {
        id: "07",
        title: "Bí mật của may mắn",
        author: "Alex Rovira & Fernando Trías de Bes",
        genre: "Kỹ năng sống / Ngụ ngôn",
        keywords: "cơ hội, chủ động, thành công, bài học cuộc sống",
        summary: "Hai hiệp sĩ cùng bước vào khu rừng mê hoặc để tìm cây cỏ bốn lá thần kỳ mang lại may mắn vĩnh cửu. Trong khi một người chỉ thụ động chờ đợi, người kia kiên trì cải tạo đất, dẫn nước và tạo điều kiện cho hạt giống nảy mầm, chứng minh may mắn do chính ta tạo ra.",
        source: "Wikipedia Tiếng Việt / First News"
      },
      {
        id: "08",
        title: "Cho tôi xin một vé đi tuổi thơ",
        author: "Nguyễn Nhật Ánh",
        genre: "Văn học Việt Nam / Hài hước",
        keywords: "tuổi thơ, hài hước, nhẹ nhàng, ký ức học trò",
        summary: "Qua lăng kính hồn nhiên của cậu bé cu Mùi và nhóm bạn thân, cuốn sách tái hiện thế giới tuổi thơ đầy ắp trò nghịch ngợm và những triết lý ngây ngô nhưng sâu sắc. Tác phẩm giúp người đọc tìm lại sự trong trẻo và góc nhìn mới mẻ về cuộc sống người lớn.",
        source: "Wikipedia Tiếng Việt / NXB Trẻ"
      },
      {
        id: "09",
        title: "Hoàng tử bé",
        author: "Antoine de Saint-Exupéry",
        genre: "Văn học kinh điển / Triết lý",
        keywords: "tình bạn, nhẹ nhàng, triết lý nhân sinh, tưởng tượng",
        summary: "Một phi công gặp nạn giữa sa mạc Sahara tình cờ gặp Hoàng tử bé đến từ tiểu hành tinh B612. Qua lời kể về hành trình qua các hành tinh và tình cảm với bông hồng kiêu kỳ, câu chuyện gửi gắm thông điệp sâu sắc về tình yêu, trách nhiệm và những giá trị cốt lõi vô hình.",
        source: "Wikipedia Tiếng Việt / NXB Hội Nhà Văn"
      },
      {
        id: "10",
        title: "Dế Mèn phiêu lưu ký",
        author: "Tô Hoài",
        genre: "Văn học thiếu nhi / Phiêu lưu",
        keywords: "thế giới loài vật, bài học trưởng thành, tình bạn",
        summary: "Chú Dế Mèn kiêu căng, xốc nổi sau khi gây ra cái chết oan uổng cho Dế Choắt đã lên đường phiêu lưu khắp thế giới loài vật. Trải qua nhiều hiểm nguy cùng người bạn Dế Trũi, Dế Mèn dần trưởng thành, biết sống vị tha và đoàn kết vì hòa bình muôn loài.",
        source: "Wikipedia Tiếng Việt / NXB Kim Đồng"
      },
      {
        id: "11",
        title: "Totto-chan bên cửa sổ",
        author: "Kuroyanagi Tetsuko",
        genre: "Tự truyện / Giáo dục",
        keywords: "trường học, tình thầy trò, nhân văn, nhẹ nhàng",
        summary: "Hồi ức tuổi thơ của cô bé hiếu động Totto-chan tại ngôi trường đặc biệt Tomoe có lớp học làm từ toa tàu cũ. Nhờ phương pháp giáo dục tôn trọng cá tính và tình yêu thương của thầy hiệu trưởng Kobayashi, những đứa trẻ khác biệt đều được phát triển tự nhiên và hạnh phúc.",
        source: "Wikipedia Tiếng Việt / NXB Hội Nhà Văn"
      },
      {
        id: "12",
        title: "Lược sử thời gian",
        author: "Stephen Hawking",
        genre: "Khoa học phổ thông",
        keywords: "vũ trụ, vật lý, lỗ đen, khám phá khoa học",
        summary: "Nhà vật lý thiên tài Stephen Hawking giải thích những khái niệm phức tạp nhất về vũ trụ như vụ nổ Big Bang, lỗ đen, không gian và thời gian bằng ngôn ngữ dễ hiểu cho độc giả phổ thông. Sách khơi gợi trí tò mò về nguồn gốc và tương lai của vũ trụ.",
        source: "Wikipedia Tiếng Việt / NXB Trẻ"
      },
      {
        id: "13",
        title: "Sapiens: Lược sử loài người",
        author: "Yuval Noah Harari",
        genre: "Lịch sử / Nhân học",
        keywords: "lịch sử nhân loại, tiến hóa, xã hội, tư duy phản biện",
        summary: "Cuốn sách phác họa hành trình tiến hóa 70.000 năm của loài người từ sinh vật bình thường trở thành chủ nhân Trái Đất qua ba cuộc cách mạng: Nhận thức, Nông nghiệp và Khoa học. Tác giả lý giải cách trí tưởng tượng chung giúp con người xây dựng tôn giáo, tiền tệ và quốc gia.",
        source: "Wikipedia Tiếng Việt / NXB Tri Thức"
      },
      {
        id: "14",
        title: "Đắc nhân tâm",
        author: "Dale Carnegie",
        genre: "Kỹ năng giao tiếp",
        keywords: "giao tiếp, ứng xử, tâm lý xã hội, thấu hiểu",
        summary: "Tác phẩm kinh điển cung cấp các nguyên tắc ứng xử tinh tế giúp con người xây dựng mối quan hệ tốt đẹp, biết lắng nghe, khen ngợi chân thành và thuyết phục người khác mà không gây oán giận. Sách phù hợp cho học sinh rèn luyện kỹ năng làm việc nhóm và giao tiếp.",
        source: "Wikipedia Tiếng Việt / First News"
      },
      {
        id: "15",
        title: "Tôi tự học",
        author: "Thu Giang Nguyễn Duy Cần",
        genre: "Giáo dục / Phương pháp học",
        keywords: "phương pháp học tập, tự học, đọc sách, tư duy",
        summary: "Học giả Nguyễn Duy Cần chia sẻ phương pháp tự học hiệu quả, cách đọc sách sâu, ghi chép và rèn luyện tư duy độc lập. Cuốn sách nhấn mạnh mục đích của việc học không phải để nhồi nhét kiến thức mà để mở mang trí tuệ và hoàn thiện nhân cách.",
        source: "Wikipedia Tiếng Việt / NXB Trẻ"
      },
      {
        id: "16",
        title: "Án mạng trên chuyến tàu tốc hành Phương Đông",
        author: "Agatha Christie",
        genre: "Trinh thám",
        keywords: "trinh thám, suy luận, bí ẩn, cổ điển, hồi hộp",
        summary: "Khi chuyến tàu sang trọng mắc kẹt giữa bão tuyết, một hành khách bị sát hại bí ẩn trong khoang khóa trái. Thám tử lừng danh Hercule Poirot phải thẩm vấn toàn bộ hành khách và xâu chuỗi các manh mối tâm lý để vạch trần sự thật bất ngờ đằng sau vụ án.",
        source: "Wikipedia Tiếng Việt / NXB Trẻ"
      },
      {
        id: "17",
        title: "Phía sau nghi can X",
        author: "Higashino Keigo",
        genre: "Trinh thám / Tâm lý Nhật Bản",
        keywords: "trinh thám Nhật Bản, tâm lý tội phạm, đấu trí, bí ẩn, hồi hộp",
        summary: "Một vụ án mạng xảy ra khiến thiên tài toán học Ishigami quyết định dùng trí tuệ siêu việt để tạo ra bằng chứng ngoại phạm hoàn hảo cho người hàng xóm. Cuộc đấu trí căng thẳng giữa anh và nhà vật lý học Yukawa hé lộ câu chuyện cảm động về sự hy sinh.",
        source: "Wikipedia Tiếng Việt / NXB Nhã Nam"
      },
      {
        id: "18",
        title: "Hai vạn dặm dưới đáy biển",
        author: "Jules Verne",
        genre: "Khoa học viễn tưởng",
        keywords: "đại dương, khoa học viễn tưởng, thám hiểm, tàu ngầm, bí ẩn, hồi hộp",
        summary: "Giáo sư Aronnax cùng hai người bạn vô tình bị bắt giữ trên chiếc tàu ngầm tối tân Nautilus của thuyền trưởng Nemo bí ẩn. Họ trải qua hành trình kỳ thú khám phá hệ sinh thái đại dương sâu thẳm, đối mặt với quái vật biển và những bí mật công nghệ vượt thời đại.",
        source: "Wikipedia Tiếng Việt / NXB Văn Học"
      },
      {
        id: "19",
        title: "Tuổi trẻ đáng giá bao nhiêu",
        author: "Rosie Nguyễn",
        genre: "Kỹ năng sống / Việt Nam",
        keywords: "định hướng tuổi trẻ, trải nghiệm, đọc sách, kỹ năng",
        summary: "Tác giả chia sẻ trải nghiệm thực tế về việc tự học, đi du lịch khám phá và rèn luyện kỹ năng mềm cho người trẻ Việt Nam. Cuốn sách truyền cảm hứng giúp học sinh, sinh viên tận dụng quãng thời gian thanh xuân để tìm ra đam mê và phát triển bản thân.",
        source: "NXB Hội Nhà Văn / Nhã Nam"
      },
      {
        id: "20",
        title: "Cây cam ngọt của tôi",
        author: "José Mauro de Vasconcelos",
        genre: "Tiểu thuyết / Tâm lý",
        keywords: "cảm động, gia đình, tuổi thơ, nhân văn",
        summary: "Cậu bé Zezé thông minh, tinh nghịch lớn lên trong một gia đình nghèo ở Brazil thường xuyên bị hiểu lầm và đòn roi. Cậu tìm thấy sự an ủi nơi cây cam ngọt sau nhà và tình bạn ấm áp với ông Bồ, mang đến câu chuyện đầy nước mắt về tình yêu thương và sự mất mát.",
        source: "Wikipedia Tiếng Việt / NXB Nhã Nam"
      }
    ];

    // Working catalog array in memory (Privacy-friendly: resets on reload or clear)
    let bookCatalog = [...initialBooks];
    const apiKey = ""; // Provided by runtime environment if available

    // 2. Tab Navigation Logic
    function switchTab(tabName) {
      document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
      document.getElementById(`tab-${tabName}`).classList.remove('hidden');

      document.querySelectorAll('.tab-btn').forEach(btn => {
        btn.classList.remove('bg-white', 'text-forest', 'shadow-sm');
        btn.classList.add('text-emerald-100', 'hover:bg-forest-light/60');
      });

      const activeBtn = document.getElementById(`tab-btn-${tabName}`);
      activeBtn.classList.remove('text-emerald-100', 'hover:bg-forest-light/60');
      activeBtn.classList.add('bg-white', 'text-forest', 'shadow-sm');

      if (tabName === 'catalog') {
        renderCatalog();
      }
    }

    // 3. Toast Notification Helper (Replaces alert())
    function showToast(message, iconClass = 'fa-circle-check') {
      const banner = document.getElementById('toastBanner');
      const msgEl = document.getElementById('toastMessage');
      const iconEl = document.getElementById('toastIcon');

      msgEl.textContent = message;
      iconEl.className = `fa-solid ${iconClass} text-amber-400 text-lg`;

      banner.classList.remove('hidden');
      setTimeout(() => {
        banner.classList.remove('translate-y-4', 'opacity-0');
      }, 10);

      setTimeout(() => {
        banner.classList.add('translate-y-4', 'opacity-0');
        setTimeout(() => banner.classList.add('hidden'), 300);
      }, 3200);
    }

    // 4. Update Book Counters Across UI
    function updateBookCounters() {
      const count = bookCatalog.length;
      document.getElementById('headerBookCount').textContent = count;
      document.getElementById('tabBookCount').textContent = count;
      document.getElementById('sideBookCount').textContent = count;
      document.getElementById('catalogHeaderCount').textContent = count;
    }

    // 5. Render Book Catalog Table (Tab 2)
    function renderCatalog() {
      const searchVal = document.getElementById('catalogSearchInput').value.toLowerCase().trim();
      const genreVal = document.getElementById('catalogGenreFilter').value.toLowerCase();
      const tbody = document.getElementById('catalogTableBody');
      const emptyState = document.getElementById('emptyCatalogState');

      const filtered = bookCatalog.filter(book => {
        const matchesSearch =
          book.title.toLowerCase().includes(searchVal) ||
          book.author.toLowerCase().includes(searchVal) ||
          book.keywords.toLowerCase().includes(searchVal) ||
          book.summary.toLowerCase().includes(searchVal);

        const matchesGenre =
          genreVal === 'all' || book.genre.toLowerCase().includes(genreVal);

        return matchesSearch && matchesGenre;
      });

      if (filtered.length === 0) {
        tbody.innerHTML = '';
        emptyState.classList.remove('hidden');
        return;
      }

      emptyState.classList.add('hidden');
      tbody.innerHTML = filtered.map(book => `
        <tr class="hover:bg-ivory/90 transition">
          <td class="py-3.5 px-4 font-bold text-forest align-top">#${book.id}</td>
          <td class="py-3.5 px-4 align-top">
            <div class="font-bold text-slate-900">${escapeHtml(book.title)}</div>
            <div class="text-xs text-slate-500 mt-0.5"><i class="fa-solid fa-feather-pointed mr-1 text-amberwarm"></i>${escapeHtml(book.author)}</div>
          </td>
          <td class="py-3.5 px-4 align-top">
            <span class="inline-block bg-emerald-50 text-forest text-xs font-semibold px-2.5 py-1 rounded-lg border border-emerald-200">
              ${escapeHtml(book.genre)}
            </span>
          </td>
          <td class="py-3.5 px-4 align-top">
            <div class="flex flex-wrap gap-1">
              ${book.keywords.split(',').map(kw => `
                <span class="bg-cream text-slate-700 text-[11px] px-2 py-0.5 rounded-md border border-stone-200">
                  ${escapeHtml(kw.trim())}
                </span>
              `).join('')}
            </div>
          </td>
          <td class="py-3.5 px-4 text-xs text-slate-700 leading-relaxed align-top">
            ${escapeHtml(book.summary)}
          </td>
          <td class="py-3.5 px-4 text-xs text-slate-500 align-top">
            <i class="fa-solid fa-link mr-1 text-slate-400"></i>${escapeHtml(book.source)}
          </td>
        </tr>
      `).join('');
    }

    // 6. Add New Book Modal & Scalability Test (Tab 2)
    function openAddBookModal() {
      document.getElementById('addBookModal').classList.remove('hidden');
    }

    function closeAddBookModal() {
      document.getElementById('addBookModal').classList.add('hidden');
      document.getElementById('addBookForm').reset();
    }

    function handleAddBook(e) {
      e.preventDefault();
      const nextId = String(bookCatalog.length + 1).padStart(2, '0');
      const newBook = {
        id: nextId,
        title: document.getElementById('newBookTitle').value.trim(),
        author: document.getElementById('newBookAuthor').value.trim(),
        genre: document.getElementById('newBookGenre').value.trim(),
        keywords: document.getElementById('newBookKeywords').value.trim(),
        summary: document.getElementById('newBookSummary').value.trim(),
        source: document.getElementById('newBookSource').value.trim() + " (Đã kiểm duyệt)"
      };

      bookCatalog.push(newBook);
      updateBookCounters();
      renderCatalog();
      closeAddBookModal();
      showToast(`Đã thêm sách #${nextId} "${newBook.title}" vào kho! Bạn có thể thử hỏi Chatbot ngay.`);
    }

    // 7. Chat UI & Quick Prompts
    function sendQuickPrompt(text) {
      const input = document.getElementById('userPreferenceInput');
      input.value = text;
      handleChatSubmit(new Event('submit'));
    }

    function resetChatSession() {
      const chatContainer = document.getElementById('chatMessages');
      chatContainer.innerHTML = `
        <div class="flex items-start gap-3">
          <div class="w-8 h-8 rounded-full bg-forest text-white flex items-center justify-center shrink-0 mt-1">
            <i class="fa-solid fa-book-bookmark text-xs text-amber-300"></i>
          </div>
          <div class="bg-white border border-stone-200 rounded-2xl rounded-tl-none p-4 shadow-sm max-w-2xl">
            <p class="text-sm text-slate-700 leading-relaxed">
              <span class="text-emerald-700 font-semibold"><i class="fa-solid fa-broom mr-1"></i>Đã xóa toàn bộ lịch sử phiên làm việc!</span> Bộ nhớ tạm đã được làm sạch để bảo vệ quyền riêng tư của bạn. Hãy nhập sở thích đọc sách mới bên dưới.
            </p>
          </div>
        </div>
      `;
      document.getElementById('responseTimerBadge').classList.add('hidden');
      showToast('Đã xóa phiên làm việc & bảo vệ quyền riêng tư!', 'fa-user-shield');
    }

    function appendUserMessage(text) {
      const chatContainer = document.getElementById('chatMessages');
      const div = document.createElement('div');
      div.className = 'flex items-start justify-end gap-3';
      div.innerHTML = `
        <div class="bg-forest text-white rounded-2xl rounded-tr-none px-4 py-3 shadow-sm max-w-xl text-sm leading-relaxed">
          ${escapeHtml(text)}
        </div>
        <div class="w-8 h-8 rounded-full bg-amberwarm text-white flex items-center justify-center shrink-0 mt-1 text-xs font-bold">
          HS
        </div>
      `;
      chatContainer.appendChild(div);
      chatContainer.scrollTop = chatContainer.scrollHeight;
    }

    function appendLoadingIndicator() {
      const chatContainer = document.getElementById('chatMessages');
      const id = 'loading-' + Date.now();
      const div = document.createElement('div');
      div.id = id;
      div.className = 'flex items-start gap-3';
      div.innerHTML = `
        <div class="w-8 h-8 rounded-full bg-forest text-white flex items-center justify-center shrink-0 mt-1">
          <i class="fa-solid fa-book-bookmark text-xs text-amber-300"></i>
        </div>
        <div class="bg-white border border-stone-200 rounded-2xl rounded-tl-none px-4 py-3.5 shadow-sm flex items-center gap-2">
          <span class="text-xs text-slate-500 font-medium mr-1">Đang đối chiếu kho ${bookCatalog.length} sách &amp; lọc FATE</span>
          <span class="w-2 h-2 rounded-full bg-forest typing-dot"></span>
          <span class="w-2 h-2 rounded-full bg-forest typing-dot"></span>
          <span class="w-2 h-2 rounded-full bg-forest typing-dot"></span>
        </div>
      `;
      chatContainer.appendChild(div);
      chatContainer.scrollTop = chatContainer.scrollHeight;
      return id;
    }

    // 8. Core Recommendation Handler (Gemini API + Intelligent Closed-Catalog Fallback)
    async function handleChatSubmit(e) {
      e.preventDefault();
      const input = document.getElementById('userPreferenceInput');
      const sendBtn = document.getElementById('sendBtn');
      const userQuery = input.value.trim();
      if (!userQuery) return;

      input.value = '';
      input.disabled = true;
      sendBtn.disabled = true;
      sendBtn.classList.add('opacity-60');

      appendUserMessage(userQuery);
      const loadingId = appendLoadingIndicator();
      const startTime = performance.now();

      try {
        let resultData = null;

        // Try Gemini API if runtime key is available
        if (apiKey) {
          resultData = await fetchRecommendationsFromGemini(userQuery);
        }

        // Fallback to deterministic FATE-compliant local semantic engine if API key is empty or fails
        if (!resultData || !resultData.recommendations || resultData.recommendations.length === 0) {
          await new Promise(r => setTimeout(r, 650)); // Natural processing pause
          resultData = generateLocalFATERecommendations(userQuery);
        }

        const elapsedSeconds = ((performance.now() - startTime) / 1000).toFixed(2);
        document.getElementById('lastResponseTime').textContent = `${elapsedSeconds}s`;
        document.getElementById('responseTimerBadge').classList.remove('hidden');

        document.getElementById(loadingId)?.remove();
        renderRecommendationResponse(userQuery, resultData, elapsedSeconds);
      } catch (err) {
        console.error(err);
        document.getElementById(loadingId)?.remove();
        const fallbackData = generateLocalFATERecommendations(userQuery);
        const elapsedSeconds = ((performance.now() - startTime) / 1000).toFixed(2);
        renderRecommendationResponse(userQuery, fallbackData, elapsedSeconds);
      } finally {
        input.disabled = false;
        sendBtn.disabled = false;
        sendBtn.classList.remove('opacity-60');
        input.focus();
      }
    }

    // Gemini API Call with Strict Closed-Catalog Grounding & JSON Schema
    async function fetchRecommendationsFromGemini(userQuery) {
      const endpoint = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;

      const systemPrompt = `Bạn là "Smart Library Curator – Người Giám Tuyến Thư Viện 4.0" tuân thủ nghiêm ngặt khung đạo đức AI (FATE).
QUY TẮC BẮT BUỘC:
1. KHÓA DỮ LIỆU KÍN (Accountability): Chỉ được phép chọn sách từ danh sách JSON được cung cấp bên dưới. Tuyệt đối KHÔNG tự bịa ra tên sách ngoài danh sách.
2. CHỐNG BONG BÓNG LỌC (Fairness - Quy tắc 3 + 1): Trả về đúng 4 cuốn sách:
   - 3 cuốn đầu tiên có type = "match" (khớp trực tiếp với sở thích của người dùng).
   - Cuốn thứ 4 có type = "discovery" (Gợi ý mở rộng – Phá vỡ bong bóng lọc: thuộc thể loại khác biệt nhưng có điểm chạm ý nghĩa/tâm lý tích cực với người dùng).
3. MINH BẠCH (Transparency): Với mỗi cuốn sách, viết 1–2 câu giải thích (reason) cụ thể vì sao phù hợp với sở thích người dùng, bám sát phần tóm tắt trong kho dữ liệu.

KHO DỮ LIỆU SÁCH HIỆN TẠI:
${JSON.stringify(bookCatalog)}`;

      const payload = {
        contents: [{ parts: [{ text: `Sở thích của người đọc: "${userQuery}"` }] }],
        systemInstruction: { parts: [{ text: systemPrompt }] },
        generationConfig: {
          responseMimeType: "application/json",
          responseSchema: {
            type: "OBJECT",
            properties: {
              intro: { type: "STRING" },
              recommendations: {
                type: "ARRAY",
                items: {
                  type: "OBJECT",
                  properties: {
                    id: { type: "STRING" },
                    type: { type: "STRING", enum: ["match", "discovery"] },
                    reason: { type: "STRING" }
                  },
                  required: ["id", "type", "reason"]
                }
              }
            },
            required: ["intro", "recommendations"]
          }
        }
      };

      const response = await fetch(endpoint, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload)
      });

      if (!response.ok) throw new Error('Gemini API request failed');
      const data = await response.json();
      const rawText = data.candidates?.[0]?.content?.parts?.[0]?.text;
      if (!rawText) throw new Error('Empty Gemini response');

      const parsed = JSON.parse(rawText);
      // Verify every returned ID actually exists in bookCatalog (Accountability check)
      const verifiedRecs = parsed.recommendations
        .map(rec => {
          const book = bookCatalog.find(b => b.id === rec.id || b.id === rec.id.padStart(2, '0'));
          if (!book) return null;
          return { ...book, recType: rec.type, reason: rec.reason };
        })
        .filter(Boolean);

      if (verifiedRecs.length < 3) throw new Error('Insufficient verified books');
      return { intro: parsed.intro, recommendations: verifiedRecs.slice(0, 4) };
    }

    // Intelligent Closed-Catalog Local Fallback Engine (Guarantees offline/no-key functionality)
    function generateLocalFATERecommendations(query) {
      const q = query.toLowerCase();

      // Vietnamese semantic keyword expansion map
      const semanticGroups = [
        { triggers: ['tội phạm', 'trinh thám', 'bí ẩn', 'hồi hộp', 'vụ án', 'thám tử', 'đấu trí', 'sát hại'], tags: ['trinh thám', 'bí ẩn', 'hồi hộp', 'tội phạm', 'suy luận', 'đấu trí', 'thám hiểm'] },
        { triggers: ['khoa học', 'vũ trụ', 'vật lý', 'lịch sử', 'khám phá', 'viễn tưởng', 'tiến hóa', 'đại dương'], tags: ['vũ trụ', 'vật lý', 'lịch sử', 'khoa học', 'viễn tưởng', 'tiến hóa', 'đại dương', 'thám hiểm'] },
        { triggers: ['tuổi thơ', 'nhẹ nhàng', 'hài hước', 'tình bạn', 'học trò', 'gia đình', 'cảm động', 'trường học'], tags: ['tuổi thơ', 'nhẹ nhàng', 'hài hước', 'tình bạn', 'học trò', 'gia đình', 'cảm động', 'trường học', 'nhân văn'] },
        { triggers: ['kỹ năng', 'vùng an toàn', 'thay đổi', 'thành công', 'tư duy', 'giao tiếp', 'tự học', 'tuổi trẻ'], tags: ['vùng an toàn', 'thay đổi', 'thành công', 'tư duy', 'giao tiếp', 'tự học', 'tuổi trẻ', 'kỹ năng'] },
        { triggers: ['phiêu lưu', 'ước mơ', 'hành trình', 'thiên nhiên', 'lý tưởng', 'nghị lực', 'triết lý'], tags: ['phiêu lưu', 'ước mơ', 'hành trình', 'thiên nhiên', 'lý tưởng', 'nghị lực', 'triết lý'] }
      ];

      let expandedTerms = q.split(/[\s,.;!?]+/).filter(w => w.length > 2);
      semanticGroups.forEach(group => {
        if (group.triggers.some(t => q.includes(t))) {
          expandedTerms = [...new Set([...expandedTerms, ...group.tags])];
        }
      });

      // Score every book in the closed catalog
      const scoredBooks = bookCatalog.map(book => {
        const haystack = `${book.title} ${book.genre} ${book.keywords} ${book.summary}`.toLowerCase();
        let score = 0;
        let matchedKeywords = [];

        expandedTerms.forEach(term => {
          if (haystack.includes(term)) {
            score += book.keywords.toLowerCase().includes(term) ? 3 : 1;
            if (!matchedKeywords.includes(term) && term.length > 2) {
              matchedKeywords.push(term);
            }
          }
        });

        return { ...book, score, matchedKeywords };
      });

      // Sort by relevance score descending
      scoredBooks.sort((a, b) => b.score - a.score);

      // Top 3 matching books
      const topMatches = scoredBooks.slice(0, 3).map(b => {
        const kwText = b.matchedKeywords.length > 0
          ? `các yếu tố về ${b.matchedKeywords.slice(0, 3).join(', ')}`
          : `chủ đề ${b.genre.toLowerCase()}`;
        return {
          ...b,
          recType: 'match',
          reason: `Cuốn sách phù hợp trực tiếp với sở thích của bạn nhờ khai thác sâu ${kwText}. ${b.summary.split('.')[0]}.`
        };
      });

      // 4th book: Anti-Filter Bubble Discovery Book (distinct genre from topMatches)
      const usedIds = new Set(topMatches.map(b => b.id));
      const usedMainGenres = new Set(topMatches.map(b => b.genre.split('/')[0].trim()));

      let discoveryBook = scoredBooks.find(b => !usedIds.has(b.id) && !usedMainGenres.has(b.genre.split('/')[0].trim()));
      if (!discoveryBook) {
        discoveryBook = scoredBooks.find(b => !usedIds.has(b.id)) || bookCatalog[0];
      }

      const discoveryRec = {
        ...discoveryBook,
        recType: 'discovery',
        reason: `Để tránh hiệu ứng "bong bóng lọc" chỉ đọc một thể loại quen thuộc, hệ thống gợi ý thêm tác phẩm thuộc mảng ${discoveryBook.genre} này nhằm mở rộng góc nhìn về ${discoveryBook.keywords.split(',')[0].trim()} và giá trị nhân văn.`
      };

      return {
        intro: `Dựa trên sở thích "${query}" của bạn, hệ thống đã đối chiếu kho ${bookCatalog.length} đầu sách chuẩn hóa và chọn ra 3 cuốn đúng gu kèm 1 cuốn mở rộng thể loại:`,
        recommendations: [...topMatches, discoveryRec]
      };
    }

    // 9. Render Structured Recommendation Cards in Chat
    function renderRecommendationResponse(userQuery, data, elapsedSeconds) {
      const chatContainer = document.getElementById('chatMessages');
      const wrapper = document.createElement('div');
      wrapper.className = 'flex items-start gap-3';

      const cardsHtml = data.recommendations.map(book => {
        const isDiscovery = book.recType === 'discovery';
        const badgeHtml = isDiscovery
          ? `<span class="inline-flex items-center gap-1 bg-amberwarm-light text-amberwarm-dark text-[11px] font-bold px-2.5 py-0.5 rounded-full border border-amber-300">
               <i class="fa-solid fa-compass"></i> Mở rộng – Phá vỡ bong bóng lọc
             </span>`
          : `<span class="inline-flex items-center gap-1 bg-emerald-100 text-forest text-[11px] font-bold px-2.5 py-0.5 rounded-full border border-emerald-300">
               <i class="fa-solid fa-bullseye"></i> Khớp sở thích
             </span>`;

        const cardBorder = isDiscovery ? 'border-amber-300 bg-amberwarm-light/20' : 'border-stone-200 bg-ivory/60';

        return `
          <div class="p-3.5 rounded-xl border ${cardBorder} transition hover:shadow-xs">
            <div class="flex flex-wrap items-center justify-between gap-2 mb-1.5">
              <div class="flex items-center gap-2">
                <span class="bg-forest text-white text-xs font-bold px-2 py-0.5 rounded-md">ID ${escapeHtml(book.id)}</span>
                <h4 class="font-bold text-slate-900 text-sm">${escapeHtml(book.title)} – <span class="font-normal text-slate-600">${escapeHtml(book.author)}</span></h4>
              </div>
              ${badgeHtml}
            </div>
            <div class="text-xs text-forest font-medium mb-1.5">
              <i class="fa-solid fa-tag mr-1 text-slate-400"></i>Thể loại: ${escapeHtml(book.genre)}
            </div>
            <p class="text-xs sm:text-sm text-slate-700 leading-relaxed mb-2">
              <strong class="text-slate-900">Lý do gợi ý:</strong> ${escapeHtml(book.reason)}
            </p>
            <div class="text-[11px] text-slate-500 flex items-center justify-between pt-1.5 border-t border-stone-200/70">
              <span><i class="fa-solid fa-book-open mr-1"></i>Nguồn: ${escapeHtml(book.source)}</span>
              <span class="text-emerald-700 font-medium"><i class="fa-solid fa-check-circle mr-1"></i>Đã xác thực kho</span>
            </div>
          </div>
        `;
      }).join('');

      wrapper.innerHTML = `
        <div class="w-8 h-8 rounded-full bg-forest text-white flex items-center justify-center shrink-0 mt-1">
          <i class="fa-solid fa-book-bookmark text-xs text-amber-300"></i>
        </div>
        <div class="bg-white border border-stone-200 rounded-2xl rounded-tl-none p-4 sm:p-5 shadow-sm max-w-3xl w-full space-y-3">
          <p class="text-sm text-slate-700 leading-relaxed">${escapeHtml(data.intro)}</p>
          <div class="space-y-3">
            ${cardsHtml}
          </div>
          <div class="pt-2 border-t border-stone-100 flex flex-wrap items-center justify-between gap-2 text-[11px] text-slate-500">
            <span><i class="fa-solid fa-shield-halved text-emerald-600 mr-1"></i>FATE Verified: 100% sách thuộc kho • 0% dữ liệu cá nhân được lưu</span>
            <span>Thời gian phản hồi: <strong>${elapsedSeconds}s</strong></span>
          </div>
        </div>
      `;

      chatContainer.appendChild(wrapper);
      chatContainer.scrollTop = chatContainer.scrollHeight;
    }

    // Security helper to prevent XSS
    function escapeHtml(str) {
      if (!str) return '';
      return String(str)
        .replace(/&/g, '&amp;')
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#039;');
    }

    // Initialize on load
    window.addEventListener('DOMContentLoaded', () => {
      updateBookCounters();
      renderCatalog();
    });
  </script>
</body>
</html>
