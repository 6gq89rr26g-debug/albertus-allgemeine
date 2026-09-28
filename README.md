<!DOCTYPE html>
<html lang="de" class="scroll-smooth">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Albertus-Allgemeine | Die Schülerzeitung des Albertus-Magnus-Gymnasiums</title>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@700;900&family=Inter:wght@300;400;500;600;700&family=Newsreader:ital,opsz,wght@0,6..72,400;0,6..72,600;1,6..72,400&display=swap" rel="stylesheet">

    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        brand: {
                            50: '#f0f7ff',
                            100: '#e0effe',
                            500: '#0284c7',
                            700: '#0369a1',
                            900: '#0c4a6e',
                        },
                        newspaper: {
                            bg: '#fcfbf9',
                            darkBg: '#121316',
                            cardDark: '#1a1c23',
                            border: '#e5e2dc',
                            darkBorder: '#2d3139',
                            text: '#1a1a1a',
                            muted: '#66615b'
                        }
                    },
                    fontFamily: {
                        header: ['Cinzel', 'serif'],
                        serif: ['Newsreader', 'Georgia', 'serif'],
                        sans: ['Inter', 'sans-serif'],
                    }
                }
            }
        }
    </script>

    <style>
        .newspaper-grid {
            display: grid;
            grid-template-columns: repeat(12, 1fr);
            gap: 1.5rem;
        }
        .editorial-border {
            border-bottom: 3px double currentColor;
        }
        .prose-editorial p {
            margin-bottom: 1.25rem;
            line-height: 1.75;
        }
        .drop-cap::first-letter {
            font-family: 'Cinzel', serif;
            float: left;
            font-size: 3.5rem;
            line-height: 0.8;
            padding-top: 4px;
            padding-right: 8px;
            padding-left: 3px;
            font-weight: bold;
            color: #0284c7;
        }
    </style>
</head>
<body class="bg-newspaper-bg dark:bg-newspaper-darkBg text-newspaper-text dark:text-gray-100 font-sans transition-colors duration-200 min-h-screen flex flex-col">

    <!-- Top Utility Bar -->
    <div class="bg-stone-900 text-stone-300 text-xs py-1.5 px-4">
        <div class="max-w-7xl mx-auto flex flex-wrap justify-between items-center gap-2">
            <div class="flex items-center space-x-4">
                <span id="current-date" class="font-mono"></span>
                <span class="hidden sm:inline text-stone-500">|</span>
                <span class="hidden sm:inline"><i class="fa-solid fa-graduation-cap text-sky-400 mr-1"></i> Albertus-Magnus-Gymnasium</span>
            </div>
            <div class="flex items-center space-x-4">
                <button id="theme-toggle" class="hover:text-white transition flex items-center gap-1.5">
                    <i class="fa-solid fa-moon dark:hidden"></i>
                    <i class="fa-solid fa-sun hidden dark:inline text-amber-400"></i>
                    <span id="theme-text" class="hidden sm:inline">Dark Mode</span>
                </button>
                <span class="text-stone-600">|</span>
                <button onclick="switchTab('admin')" class="bg-sky-600 hover:bg-sky-500 text-white px-2.5 py-0.5 rounded font-medium transition flex items-center gap-1">
                    <i class="fa-solid fa-pen-nib text-xs"></i> Redaktions-Zugang
                </button>
            </div>
        </div>
    </div>

    <!-- Main Header / Masthead -->
    <header class="border-b border-stone-300 dark:border-stone-800 bg-newspaper-bg dark:bg-newspaper-darkBg sticky top-0 z-30 shadow-xs backdrop-blur-md bg-opacity-95 dark:bg-opacity-95">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4 sm:py-6 text-center relative">
            <div class="border-t-2 border-b-2 border-stone-800 dark:border-stone-200 py-3 my-1">
                <div class="text-xs uppercase tracking-widest font-semibold text-sky-600 dark:text-sky-400 mb-1">Unabhängige Schülerzeitung</div>
                <h1 onclick="switchTab('reader'); filterCategory('all')" class="cursor-pointer font-header text-3xl sm:text-5xl md:text-6xl font-black tracking-tight text-stone-900 dark:text-white uppercase hover:opacity-90 transition">
                    Albertus-Allgemeine
                </h1>
                <p class="font-serif italic text-sm sm:text-base text-stone-600 dark:text-stone-400 mt-1">
                    "Kritisch, Kreativ, Schulnah" – Ausgabe 2026/2027
                </p>
            </div>

            <!-- Navigation Bar -->
            <nav class="mt-4 flex items-center justify-between overflow-x-auto py-2 scrollbar-none border-t border-stone-200 dark:border-stone-800">
                <div class="flex space-x-1 sm:space-x-2 text-sm font-semibold tracking-wide uppercase mx-auto" id="category-nav">
                    <!-- Dynamic categories injected by JS -->
                </div>
            </nav>
        </div>
    </header>

    <!-- Main Content Container -->
    <main class="flex-grow max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8 w-full">

        <div id="toast-container" class="fixed bottom-5 right-5 z-50 flex flex-col gap-2 pointer-events-none"></div>

        <!-- ================= READER VIEW ================= -->
        <div id="view-reader" class="space-y-10">
            
            <!-- Search & Quick Filters Bar -->
            <div class="flex flex-col sm:flex-row justify-between items-center gap-4 bg-stone-100 dark:bg-newspaper-cardDark p-4 rounded-xl border border-stone-200 dark:border-stone-800">
                <div class="relative w-full sm:w-80">
                    <i class="fa-solid fa-search absolute left-3 top-1/2 -translate-y-1/2 text-stone-400"></i>
                    <input type="text" id="search-input" placeholder="Artikel durchsuchen..." 
                           class="w-full pl-9 pr-4 py-2 bg-white dark:bg-stone-900 border border-stone-300 dark:border-stone-700 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-sky-500 dark:text-white">
                </div>
                <div class="flex items-center gap-2 w-full sm:w-auto justify-end">
                    <span class="text-xs text-stone-500 font-medium uppercase tracking-wider">Sortierung:</span>
                    <select id="sort-select" class="bg-white dark:bg-stone-900 border border-stone-300 dark:border-stone-700 text-sm rounded-lg p-2 focus:outline-none focus:ring-2 focus:ring-sky-500 dark:text-white">
                        <option value="newest">Neueste zuerst</option>
                        <option value="oldest">Älteste zuerst</option>
                        <option value="popular">Beliebte (Klicks)</option>
                    </select>
                </div>
            </div>

            <!-- Active Filter Badge (Shown when filtering) -->
            <div id="active-filter-bar" class="hidden flex items-center justify-between bg-sky-50 dark:bg-sky-950/40 border border-sky-200 dark:border-sky-800 p-3 rounded-lg">
                <span class="text-sm font-medium text-sky-800 dark:text-sky-300" id="filter-text">Filter: Campus</span>
                <button onclick="filterCategory('all')" class="text-xs bg-sky-200 dark:bg-sky-800 hover:bg-sky-300 dark:hover:bg-sky-700 text-sky-900 dark:text-sky-100 px-2 py-1 rounded transition">
                    <i class="fa-solid fa-times mr-1"></i> Filter aufheben
                </button>
            </div>

            <!-- TOP STORY / HERO SECTION -->
            <section id="hero-section" class="border-b border-stone-300 dark:border-stone-800 pb-10">
                <!-- Injected via JS -->
            </section>

            <!-- MAIN ARTICLES GRID -->
            <section>
                <div class="flex items-center justify-between mb-6 border-b border-stone-300 dark:border-stone-800 pb-2">
                    <h2 class="font-header text-xl sm:text-2xl font-bold uppercase tracking-wider text-stone-900 dark:text-white" id="grid-title">
                        Aktuelle Berichte
                    </h2>
                    <span class="text-xs font-mono text-stone-500" id="article-count">0 Artikel</span>
                </div>

                <div id="articles-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
                    <!-- Injected via JS -->
                </div>
            </section>
        </div>

        <!-- ================= ARTICLE DETAIL MODAL / OVERLAY ================= -->
        <div id="article-modal" class="fixed inset-0 z-50 hidden overflow-y-auto bg-black/75 backdrop-blur-xs flex justify-center p-2 sm:p-4 md:p-6">
            <div class="bg-newspaper-bg dark:bg-newspaper-cardDark w-full max-w-4xl rounded-2xl shadow-2xl overflow-hidden border border-stone-300 dark:border-stone-700 my-auto flex flex-col max-h-[90vh]">
                
                <!-- Modal Header Controls -->
                <div class="sticky top-0 z-10 bg-newspaper-bg dark:bg-newspaper-cardDark border-b border-stone-200 dark:border-stone-800 p-4 flex items-center justify-between">
                    <div class="flex items-center space-x-3">
                        <button onclick="closeArticleModal()" class="text-stone-500 hover:text-stone-900 dark:hover:text-white p-1 rounded-lg transition">
                            <i class="fa-solid fa-arrow-left text-lg"></i>
                        </button>
                        <span id="modal-category" class="bg-sky-100 dark:bg-sky-900/60 text-sky-800 dark:text-sky-300 text-xs px-2.5 py-1 rounded-full font-semibold uppercase tracking-wider">
                            Kategorie
                        </span>
                    </div>
                    
                    <div class="flex items-center space-x-3">
                        <!-- Reading accessibility controls -->
                        <div class="flex items-center bg-stone-100 dark:bg-stone-800 rounded-lg p-1 text-xs">
                            <button onclick="adjustFontSize(-1)" class="px-2 py-1 text-stone-600 dark:text-stone-300 hover:bg-white dark:hover:bg-stone-700 rounded" title="Schrift verkleinern">A-</button>
                            <button onclick="adjustFontSize(1)" class="px-2 py-1 text-stone-600 dark:text-stone-300 hover:bg-white dark:hover:bg-stone-700 rounded font-bold" title="Schrift vergrößern">A+</button>
                        </div>
                        <button id="tts-btn" onclick="toggleReadAloud()" class="p-2 text-stone-600 dark:text-stone-300 hover:bg-stone-100 dark:hover:bg-stone-800 rounded-lg transition" title="Vorlesen lassen">
                            <i class="fa-solid fa-volume-high"></i>
                        </button>
                        <button onclick="shareArticle()" class="p-2 text-stone-600 dark:text-stone-300 hover:bg-stone-100 dark:hover:bg-stone-800 rounded-lg transition" title="Link kopieren">
                            <i class="fa-solid fa-share-nodes"></i>
                        </button>
                        <button onclick="closeArticleModal()" class="p-2 text-stone-400 hover:text-stone-700 dark:hover:text-stone-200 text-xl font-bold">
                            &times;
                        </button>
                    </div>
                </div>

                <!-- Modal Body (Scrollable Article Content) -->
                <div class="p-6 md:p-10 overflow-y-auto space-y-6" id="modal-scroll-area">
                    <div class="space-y-3 text-center max-w-2xl mx-auto">
                        <h1 id="modal-title" class="font-header text-3xl sm:text-4xl md:text-5xl font-extrabold text-stone-900 dark:text-white leading-tight">
                            Artikel-Titel
                        </h1>
                        <p id="modal-subtitle" class="font-serif italic text-lg sm:text-xl text-stone-600 dark:text-stone-300">
                            Untertitel oder Teaser des Artikels...
                        </p>
                        
                        <div class="flex items-center justify-center space-x-4 text-xs text-stone-500 dark:text-stone-400 pt-4 border-t border-b border-stone-200 dark:border-stone-800 py-3">
                            <span id="modal-author"><i class="fa-solid fa-user-pen mr-1"></i> Autor</span>
                            <span>•</span>
                            <span id="modal-date"><i class="fa-solid fa-calendar-day mr-1"></i> Datum</span>
                            <span>•</span>
                            <span id="modal-readtime"><i class="fa-solid fa-clock mr-1"></i> 3 Min. Lesezeit</span>
                            <span>•</span>
                            <span id="modal-views"><i class="fa-solid fa-eye mr-1"></i> 0 Views</span>
                        </div>
                    </div>

                    <!-- Article Hero Image -->
                    <div id="modal-image-container" class="rounded-xl overflow-hidden my-6 max-h-[450px]">
                        <img id="modal-image" src="" alt="Artikel Bild" class="w-full h-full object-cover">
                    </div>

                    <!-- Main Prose Body -->
                    <div id="modal-body" class="prose-editorial font-serif text-lg md:text-xl text-stone-800 dark:text-stone-200 max-w-3xl mx-auto drop-cap">
                        <!-- Paragraphs injected dynamically -->
                    </div>

                    <!-- Tags -->
                    <div id="modal-tags" class="flex flex-wrap gap-2 pt-6 max-w-3xl mx-auto border-t border-stone-200 dark:border-stone-800">
                        <!-- Tags injected dynamically -->
                    </div>

                    <!-- COMMENTS SECTION -->
                    <section class="max-w-3xl mx-auto pt-8 border-t border-stone-300 dark:border-stone-800 mt-10">
                        <h3 class="font-header text-xl font-bold mb-6 flex items-center gap-2">
                            <i class="fa-solid fa-comments text-sky-600"></i> Lesermeinungen (<span id="modal-comments-count">0</span>)
                        </h3>

                        <!-- Add Comment Form -->
                        <form id="comment-form" onsubmit="handleAddComment(event)" class="bg-stone-50 dark:bg-stone-900 p-4 rounded-xl mb-6 space-y-3">
                            <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                                <input type="text" id="comment-author" placeholder="Dein Name (oder Anonym)" required
                                       class="p-2.5 rounded-lg border border-stone-300 dark:border-stone-700 bg-white dark:bg-stone-800 text-sm dark:text-white">
                                <div class="flex items-center text-xs text-stone-500">
                                    <i class="fa-solid fa-shield-halved mr-1 text-emerald-500"></i> Kommentare werden direkt veröffentlicht.
                                </div>
                            </div>
                            <textarea id="comment-text" rows="3" placeholder="Schreibe einen konstruktiven Kommentar..." required
                                      class="w-full p-2.5 rounded-lg border border-stone-300 dark:border-stone-700 bg-white dark:bg-stone-800 text-sm dark:text-white resize-none"></textarea>
                            <button type="submit" class="bg-sky-600 hover:bg-sky-500 text-white font-medium px-4 py-2 rounded-lg text-sm transition">
                                Kommentar abschicken
                            </button>
                        </form>

                        <!-- Comments List -->
                        <div id="modal-comments-list" class="space-y-4">
                            <!-- Injected dynamically -->
                        </div>
                    </section>
                </div>
            </div>
        </div>

        <!-- ================= REDAKTION CMS / ADMIN VIEW ================= -->
        <div id="view-admin" class="hidden space-y-8">
            <div class="flex flex-col md:flex-row justify-between items-start md:items-center gap-4 bg-stone-900 text-white p-6 rounded-2xl shadow-xl">
                <div>
                    <h2 class="font-header text-2xl md:text-3xl font-bold flex items-center gap-3">
                        <i class="fa-solid fa-pen-ruler text-sky-400"></i> Redaktions-Dashboard
                    </h2>
                    <p class="text-stone-400 text-sm mt-1">Artikel erstellen, bearbeiten und die Schülerzeitung verwalten</p>
                </div>
                <div class="flex items-center gap-3">
                    <button onclick="resetEditorForm()" class="bg-emerald-600 hover:bg-emerald-500 text-white font-medium px-4 py-2 rounded-lg text-sm transition flex items-center gap-2">
                        <i class="fa-solid fa-plus"></i> Neuer Artikel
                    </button>
                    <button onclick="switchTab('reader')" class="bg-stone-800 hover:bg-stone-700 text-stone-300 px-4 py-2 rounded-lg text-sm transition">
                        <i class="fa-solid fa-eye mr-1"></i> Leseransicht
                    </button>
                </div>
            </div>

            <!-- Dashboard Grid: Form + Manage Table -->
            <div class="grid grid-cols-1 lg:grid-cols-12 gap-8">
                
                <!-- Left: Article Editor Form -->
                <div class="lg:col-span-7 bg-white dark:bg-newspaper-cardDark p-6 rounded-2xl border border-stone-200 dark:border-stone-800 shadow-sm space-y-6">
                    <div class="flex justify-between items-center border-b border-stone-200 dark:border-stone-800 pb-4">
                        <h3 class="font-header text-lg font-bold text-stone-900 dark:text-white" id="editor-form-title">
                            Neuen Artikel verfassen
                        </h3>
                        <span id="editing-badge" class="hidden bg-amber-100 text-amber-800 text-xs px-2.5 py-0.5 rounded-full font-bold">
                            Bearbeitungs-Modus
                        </span>
                    </div>

                    <form id="article-form" onsubmit="handleSaveArticle(event)" class="space-y-4">
                        <input type="hidden" id="form-article-id">

                        <div>
                            <label class="block text-xs font-semibold uppercase text-stone-600 dark:text-stone-400 mb-1">Titel des Artikels *</label>
                            <input type="text" id="form-title" required placeholder="z. B. Neues Digital-Konzept am Albertus" 
                                   class="w-full p-3 rounded-lg border border-stone-300 dark:border-stone-700 bg-stone-50 dark:bg-stone-900 text-sm font-semibold dark:text-white focus:ring-2 focus:ring-sky-500 outline-none">
                        </div>

                        <div>
                            <label class="block text-xs font-semibold uppercase text-stone-600 dark:text-stone-400 mb-1">Untertitel / Kurzbeschreibung</label>
                            <input type="text" id="form-subtitle" placeholder="Ein kurzer Teaser für die Vorschau..." 
                                   class="w-full p-2.5 rounded-lg border border-stone-300 dark:border-stone-700 bg-stone-50 dark:bg-stone-900 text-sm dark:text-white focus:ring-2 focus:ring-sky-500 outline-none">
                        </div>

                        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                            <div>
                                <label class="block text-xs font-semibold uppercase text-stone-600 dark:text-stone-400 mb-1">Kategorie *</label>
                                <select id="form-category" required class="w-full p-2.5 rounded-lg border border-stone-300 dark:border-stone-700 bg-stone-50 dark:bg-stone-900 text-sm dark:text-white focus:ring-2 focus:ring-sky-500 outline-none">
                                    <option value="Campus">Campus</option>
                                    <option value="Kultur">Kultur</option>
                                    <option value="Sport">Sport</option>
                                    <option value="Interviews">Interviews</option>
                                    <option value="Meinungen">Meinungen</option>
                                </select>
                            </div>
                            <div>
                                <label class="block text-xs font-semibold uppercase text-stone-600 dark:text-stone-400 mb-1">Autor / Redakteur *</label>
                                <input type="text" id="form-author" required placeholder="z. B. Lisa Müller (10b)" 
                                       class="w-full p-2.5 rounded-lg border border-stone-300 dark:border-stone-700 bg-stone-50 dark:bg-stone-900 text-sm dark:text-white focus:ring-2 focus:ring-sky-500 outline-none">
                            </div>
                        </div>

                        <!-- Image Selector & Presets -->
                        <div>
                            <label class="block text-xs font-semibold uppercase text-stone-600 dark:text-stone-400 mb-1">Titelbild URL oder Schnellauswahl</label>
                            <input type="url" id="form-image" placeholder="https://images.unsplash.com/..." 
                                   class="w-full p-2.5 rounded-lg border border-stone-300 dark:border-stone-700 bg-stone-50 dark:bg-stone-900 text-sm dark:text-white focus:ring-2 focus:ring-sky-500 outline-none mb-2">
                            
                            <div class="flex items-center gap-2 overflow-x-auto py-1 text-xs">
                                <span class="text-stone-500 whitespace-nowrap">Presets:</span>
                                <button type="button" onclick="setPresetImage('school')" class="bg-stone-200 dark:bg-stone-800 hover:bg-sky-200 px-2 py-1 rounded">Schule</button>
                                <button type="button" onclick="setPresetImage('sports')" class="bg-stone-200 dark:bg-stone-800 hover:bg-sky-200 px-2 py-1 rounded">Sport</button>
                                <button type="button" onclick="setPresetImage('culture')" class="bg-stone-200 dark:bg-stone-800 hover:bg-sky-200 px-2 py-1 rounded">Kultur</button>
                                <button type="button" onclick="setPresetImage('interview')" class="bg-stone-200 dark:bg-stone-800 hover:bg-sky-200 px-2 py-1 rounded">Interview</button>
                            </div>
                        </div>

                        <!-- Content Editor -->
                        <div>
                            <label class="block text-xs font-semibold uppercase text-stone-600 dark:text-stone-400 mb-1">Artikel Inhalt (Absätze durch neue Zeilen trennen) *</label>
                            <textarea id="form-content" rows="10" required placeholder="Schreibe deinen Artikel hier... Zeilenumbrüche erzeugen neue Absätze." 
                                      class="w-full p-3 rounded-lg border border-stone-300 dark:border-stone-700 bg-stone-50 dark:bg-stone-900 text-sm font-serif dark:text-white focus:ring-2 focus:ring-sky-500 outline-none leading-relaxed"></textarea>
                        </div>

                        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                            <div>
                                <label class="block text-xs font-semibold uppercase text-stone-600 dark:text-stone-400 mb-1">Schlagwörter (Komma-getrennt)</label>
                                <input type="text" id="form-tags" placeholder="Digitalisierung, Abitur, Mensa" 
                                       class="w-full p-2.5 rounded-lg border border-stone-300 dark:border-stone-700 bg-stone-50 dark:bg-stone-900 text-sm dark:text-white focus:ring-2 focus:ring-sky-500 outline-none">
                            </div>
                            <div>
                                <label class="block text-xs font-semibold uppercase text-stone-600 dark:text-stone-400 mb-1">Status</label>
                                <select id="form-status" class="w-full p-2.5 rounded-lg border border-stone-300 dark:border-stone-700 bg-stone-50 dark:bg-stone-900 text-sm dark:text-white focus:ring-2 focus:ring-sky-500 outline-none">
                                    <option value="published">Sofort Veröffentlichen</option>
                                    <option value="draft">Als Entwurf speichern</option>
                                </select>
                            </div>
                        </div>

                        <div class="flex items-center justify-end gap-3 pt-4 border-t border-stone-200 dark:border-stone-800">
                            <button type="button" onclick="resetEditorForm()" class="px-4 py-2 rounded-lg text-sm font-medium text-stone-600 dark:text-stone-400 hover:bg-stone-100 dark:hover:bg-stone-800 transition">
                                Abbrechen
                            </button>
                            <button type="submit" class="bg-sky-600 hover:bg-sky-500 text-white font-semibold px-6 py-2 rounded-lg text-sm transition shadow-md flex items-center gap-2">
                                <i class="fa-solid fa-paper-plane"></i> Speichern & Speichern
                            </button>
                        </div>
                    </form>
                </div>

                <!-- Right: Existing Articles Management -->
                <div class="lg:col-span-5 bg-white dark:bg-newspaper-cardDark p-6 rounded-2xl border border-stone-200 dark:border-stone-800 shadow-sm flex flex-col h-full">
                    <h3 class="font-header text-lg font-bold text-stone-900 dark:text-white mb-4 flex items-center justify-between">
                        <span>Alle Artikel (<span id="admin-article-count">0</span>)</span>
                        <button onclick="loadDemoDataConfirm()" class="text-xs text-sky-600 hover:underline">
                            Demo-Daten wiederherstellen
                        </button>
                    </h3>

                    <!-- Article List in Admin -->
                    <div class="flex-grow overflow-y-auto space-y-3 max-h-[700px] pr-1" id="admin-articles-list">
                        <!-- Injected via JS -->
                    </div>
                </div>

            </div>
        </div>

    </main>

    <!-- Footer -->
    <footer class="bg-stone-900 text-stone-400 border-t border-stone-800 mt-16 py-12">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 grid grid-cols-1 md:grid-cols-4 gap-8">
            <div class="space-y-3 md:col-span-2">
                <h3 class="font-header text-xl text-white font-bold tracking-wider">ALBERTUS-ALLGEMEINE</h3>
                <p class="text-sm text-stone-400 max-w-sm">
                    Die offizielle Schülerzeitung des Albertus-Magnus-Gymnasiums. Wir berichten unabhängig, kritisch und engagiert über das Schulleben und darüber hinaus.
                </p>
                <p class="text-xs text-stone-500 font-mono">© 2026 Redaktion Albertus-Allgemeine</p>
            </div>
            <div>
                <h4 class="text-white font-semibold text-sm uppercase tracking-wider mb-3">Kategorien</h4>
                <ul class="space-y-2 text-sm">
                    <li><a href="#" onclick="switchTab('reader'); filterCategory('Campus')" class="hover:text-white transition">Campus & Schule</a></li>
                    <li><a href="#" onclick="switchTab('reader'); filterCategory('Kultur')" class="hover:text-white transition">Kultur & Kunst</a></li>
                    <li><a href="#" onclick="switchTab('reader'); filterCategory('Sport')" class="hover:text-white transition">Sportberichte</a></li>
                    <li><a href="#" onclick="switchTab('reader'); filterCategory('Interviews')" class="hover:text-white transition">Interviews</a></li>
                    <li><a href="#" onclick="switchTab('reader'); filterCategory('Meinungen')" class="hover:text-white transition">Kommentare & Meinungen</a></li>
                </ul>
            </div>
            <div>
                <h4 class="text-white font-semibold text-sm uppercase tracking-wider mb-3">Redaktion & Kontakt</h4>
                <p class="text-sm mb-2"><i class="fa-solid fa-envelope text-sky-400 mr-2"></i> redaktion@albertus-zeitung.de</p>
                <p class="text-sm mb-4"><i class="fa-solid fa-location-dot text-sky-400 mr-2"></i> Raum 204 (Schülerzeitungs-AG)</p>
                <button onclick="switchTab('admin')" class="w-full bg-stone-800 hover:bg-stone-700 text-stone-200 text-xs py-2 px-3 rounded font-medium border border-stone-700 transition">
                    <i class="fa-solid fa-lock text-sky-400 mr-1"></i> Redaktions-Login
                </button>
            </div>
        </div>
    </footer>

    <script>
        // ================= DEMO DATA & INITIALIZATION =================
        const INITIAL_ARTICLES = [
            {
                id: 'art-1',
                title: 'Neuer Schulhof-Umbau startet: Grüner, Moderner, Besser?',
                subtitle: 'Der Schülerrat hat nach langer Verhandlung die Pläne für die Umgestaltung des Pausenhofs durchgesetzt.',
                category: 'Campus',
                author: 'Maximilian Schulz (11a)',
                date: '2026-09-24',
                readTime: '4 Min.',
                views: 342,
                image: 'https://images.unsplash.com/photo-1541829070764-84a7d30dd3f3?auto=format&fit=crop&w=1200&q=80',
                content: `Nach fast zwei Jahren Verhandlungen und unzähligen Entwürfen der Schülervertretung stehen die Zeichen am Albertus-Magnus-Gymnasium endlich auf Veränderung.\n\nAb nächstem Monat beginnen die Baggerarbeiten für den neuen Außenbereich. Geplant sind nicht nur neue Sitzgelegenheiten aus nachhaltigem Holz, sondern auch eine grüne Ruhezone sowie zwei Tischtennisplatten und ein Mini-Basketballfeld.\n\n"Wir wollten einen Ort schaffen, an dem man zwischen stressigen Klausuren wirklich durchatmen kann", erklärt SV-Sprecherin Sarah (Klasse 12). Auch die Schulleitung zeigt sich begeistert von dem Engagement der Schülerinnen und Schüler.`,
                tags: ['Schulhof', 'SV', 'Umbau', 'Zukunft'],
                status: 'published',
                comments: [
                    { id: 'c1', author: 'Tim (Klasse 8b)', text: 'Endlich gibt es Tischtennisplatten! Mega gut!', date: 'Vor 2 Tagen' },
                    { id: 'c2', author: 'Frau Weber (Lehrerin)', text: 'Großes Lob an die SV für die hartnäckige Arbeit!', date: 'Vor 1 Tag' }
                ]
            },
            {
                id: 'art-2',
                title: 'KI im Unterricht: Segen oder Untergang für Hausaufgaben?',
                subtitle: 'Ein kritischer Blick auf ChatGPT & Co. im Schulalltag und was unsere Lehrer dazu sagen.',
                category: 'Meinungen',
                author: 'Elena Rost (12b)',
                date: '2026-09-21',
                readTime: '6 Min.',
                views: 512,
                image: 'https://images.unsplash.com/photo-1618005182384-a83a8bd57fbe?auto=format&fit=crop&w=1200&q=80',
                content: `Künstliche Intelligenz ist aus dem Schüleralltag längst nicht mehr wegzudenken. Aber wie gehen wir fair damit um?\n\nWährend die einen KI als effizientes Recherche-Tool nutzen, sehen manche Lehrkräfte die Gefahr der reinen Bequemlichkeit. Herr Dr. Becker aus der Fachschaft Deutsch betont: "KI kann ein Partner beim Denken sein, aber sie darf das eigene Denken niemals ersetzen."\n\nUnsere Umfrage unter 200 Schülern ergab: Über 75% nutzen regelmäßig Unterstützung bei der Vorbereitung von Referaten. Es braucht klare Regeln statt bloßer Verbote.`,
                tags: ['KI', 'Digitalisierung', 'Meinung', 'Zukunft'],
                status: 'published',
                comments: []
            },
            {
                id: 'art-3',
                title: 'Albertus-Schulband rockt das Herbstfestival',
                subtitle: 'Mit energiegeladenen Covers und zwei eigenen Songs riss die Band "The Classmates" das Publikum von den Stühlen.',
                category: 'Kultur',
                author: 'Jonas Weber (9c)',
                date: '2026-09-18',
                readTime: '3 Min.',
                views: 218,
                image: 'https://images.unsplash.com/photo-1511671782779-c97d3d27a1d4?auto=format&fit=crop&w=1200&q=80',
                content: `Die Aula war bis auf den letzten Platz gefüllt, als "The Classmates" am Freitagabend die Bühne betraten. Mit einer Mischung aus Rock-Klassikern und eigenen Indierock-Songs sorgten sie für grandiose Stimmung.\n\nBesonders die Performance von Sängerin Maya überraschte selbst die Lehrer. Wir freuen uns schon jetzt auf das Frühlingskonzert!`,
                tags: ['Musik', 'Konzert', 'Schulband', 'Kultur'],
                status: 'published',
                comments: []
            },
            {
                id: 'art-4',
                title: 'Fußball-Sensation: AMG-Mannschaft erreicht das Landesfinale',
                subtitle: 'Im Elfmeterschießen behielt unser Team die Nerven und schlug das Favoriten-Gymnasium aus der Nachbarstadt.',
                category: 'Sport',
                author: 'Lukas Podolski Fan (10a)',
                date: '2026-09-15',
                readTime: '4 Min.',
                views: 289,
                image: 'https://images.unsplash.com/photo-1508098682722-e99c43a406b2?auto=format&fit=crop&w=1200&q=80',
                content: `Was für ein Krimi auf dem Sportplatz! Nach 90 spannenden Minuten stand es 2:2. Im anschließenden Elfmeterschießen parierte unser Torwart Jan zwei entscheidende Bälle.\n\nNun geht es im nächsten Monat nach Berlin zum Bundesfinale der Schulen. Herzlichen Glückwunsch an das gesamte Team!`,
                tags: ['Sport', 'Fußball', 'Turnier', 'Sieg'],
                status: 'published',
                comments: []
            }
        ];

        const CATEGORIES = ['all', 'Campus', 'Kultur', 'Sport', 'Interviews', 'Meinungen'];
        
        // Application State
        let articles = [];
        let currentCategory = 'all';
        let searchQuery = '';
        let sortBy = 'newest';
        let activeArticleModalId = null;
        let ttsSpeechUtterance = null;

        window.addEventListener('DOMContentLoaded', () => {
            initDate();
            initTheme();
            loadArticles();
            renderCategoryNav();
            renderReaderView();
            renderAdminArticles();
        });

        function initDate() {
            const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
            const today = new Date().toLocaleDateString('de-DE', options);
            document.getElementById('current-date').innerText = today;
        }

        // Dark / Light Mode Toggle
        function initTheme() {
            const themeToggleBtn = document.getElementById('theme-toggle');
            const themeText = document.getElementById('theme-text');

            if (localStorage.theme === 'dark' || (!('theme' in localStorage) && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
                document.documentElement.classList.add('dark');
                themeText.innerText = 'Light Mode';
            } else {
                document.documentElement.classList.remove('dark');
                themeText.innerText = 'Dark Mode';
            }

            themeToggleBtn.addEventListener('click', () => {
                if (document.documentElement.classList.contains('dark')) {
                    document.documentElement.classList.remove('dark');
                    localStorage.theme = 'light';
                    themeText.innerText = 'Dark Mode';
                } else {
                    document.documentElement.classList.add('dark');
                    localStorage.theme = 'dark';
                    themeText.innerText = 'Light Mode';
                }
            });
        }

        // LocalStorage Sync
        function loadArticles() {
            const stored = localStorage.getItem('albertus_articles');
            if (stored) {
                try {
                    articles = JSON.parse(stored);
                } catch (e) {
                    articles = INITIAL_ARTICLES;
                }
            } else {
                articles = INITIAL_ARTICLES;
                saveArticles();
            }
        }

        function saveArticles() {
            localStorage.setItem('albertus_articles', JSON.stringify(articles));
        }

        function loadDemoDataConfirm() {
            if (confirm("Möchtest du wirklich die Demo-Artikel wiederherstellen? Eigene Artikel bleiben unberührt oder werden mit Demo-Daten kombiniert.")) {
                articles = [...INITIAL_ARTICLES];
                saveArticles();
                renderReaderView();
                renderAdminArticles();
                showToast("Demo-Daten erfolgreich wiederhergestellt!");
            }
        }

        function switchTab(tab) {
            const readerView = document.getElementById('view-reader');
            const adminView = document.getElementById('view-admin');

            if (tab === 'admin') {
                readerView.classList.add('hidden');
                adminView.classList.remove('hidden');
                window.scrollTo({ top: 0, behavior: 'smooth' });
            } else {
                adminView.classList.add('hidden');
                readerView.classList.remove('hidden');
                renderReaderView();
            }
        }

        function renderCategoryNav() {
            const nav = document.getElementById('category-nav');
            nav.innerHTML = CATEGORIES.map(cat => {
                const label = cat === 'all' ? 'Alle Artikel' : cat;
                const active = currentCategory === cat;
                return `
                    <button onclick="filterCategory('${cat}')" 
                        class="px-3 py-1.5 rounded-lg whitespace-nowrap transition-all ${
                            active 
                            ? 'bg-stone-900 dark:bg-white text-white dark:text-stone-900 font-bold shadow-xs' 
                            : 'text-stone-600 dark:text-stone-300 hover:bg-stone-200 dark:hover:bg-stone-800'
                        }">
                        ${label}
                    </button>
                `;
            }).join('');
        }

        function filterCategory(cat) {
            currentCategory = cat;
            renderCategoryNav();
            
            const activeBar = document.getElementById('active-filter-bar');
            const filterText = document.getElementById('filter-text');
            
            if (cat !== 'all') {
                activeBar.classList.remove('hidden');
                filterText.innerText = `Kategorie-Filter: ${cat}`;
            } else {
                activeBar.classList.add('hidden');
            }

            renderReaderView();
        }

        // Search & Sort Listeners
        document.getElementById('search-input').addEventListener('input', (e) => {
            searchQuery = e.target.value.toLowerCase().trim();
            renderReaderView();
        });

        document.getElementById('sort-select').addEventListener('change', (e) => {
            sortBy = e.target.value;
            renderReaderView();
        });

        function renderReaderView() {
            // Filter published articles
            let filtered = articles.filter(a => a.status === 'published');

            // Category filter
            if (currentCategory !== 'all') {
                filtered = filtered.filter(a => a.category.toLowerCase() === currentCategory.toLowerCase());
            }

            // Search filter
            if (searchQuery) {
                filtered = filtered.filter(a => 
                    a.title.toLowerCase().includes(searchQuery) ||
                    a.subtitle.toLowerCase().includes(searchQuery) ||
                    a.content.toLowerCase().includes(searchQuery) ||
                    a.author.toLowerCase().includes(searchQuery) ||
                    (a.tags && a.tags.some(t => t.toLowerCase().includes(searchQuery)))
                );
            }

            // Sorting
            if (sortBy === 'newest') {
                filtered.sort((a, b) => new Date(b.date) - new Date(a.date));
            } else if (sortBy === 'oldest') {
                filtered.sort((a, b) => new Date(a.date) - new Date(b.date));
            } else if (sortBy === 'popular') {
                filtered.sort((a, b) => (b.views || 0) - (a.views || 0));
            }

            document.getElementById('article-count').innerText = `${filtered.length} Artikel`;

            const heroSection = document.getElementById('hero-section');
            const gridSection = document.getElementById('articles-grid');

            if (filtered.length === 0) {
                heroSection.innerHTML = '';
                gridSection.innerHTML = `
                    <div class="col-span-full text-center py-16 bg-stone-100 dark:bg-stone-900 rounded-2xl">
                        <i class="fa-solid fa-newspaper text-4xl text-stone-400 mb-3"></i>
                        <h3 class="text-lg font-bold">Keine Artikel gefunden</h3>
                        <p class="text-sm text-stone-500 mt-1">Versuche andere Suchbegriffe oder wähle eine andere Kategorie.</p>
                    </div>
                `;
                return;
            }

            // Render Hero / Top Story (First article)
            const hero = filtered[0];
            const remaining = filtered.slice(1);

            heroSection.innerHTML = `
                <div class="cursor-pointer group grid grid-cols-1 lg:grid-cols-12 gap-8 items-center" onclick="openArticleModal('${hero.id}')">
                    <div class="lg:col-span-7 overflow-hidden rounded-2xl relative shadow-md">
                        <img src="${hero.image}" alt="${hero.title}" class="w-full h-[320px] sm:h-[400px] object-cover group-hover:scale-105 transition duration-500">
                        <span class="absolute top-4 left-4 bg-sky-600 text-white text-xs font-bold uppercase tracking-wider px-3 py-1 rounded-full shadow">
                            Top-Story • ${hero.category}
                        </span>
                    </div>
                    <div class="lg:col-span-5 space-y-4">
                        <div class="text-xs text-stone-500 dark:text-stone-400 flex items-center gap-2">
                            <span><i class="fa-solid fa-calendar"></i> ${formatDate(hero.date)}</span>
                            <span>•</span>
                            <span><i class="fa-solid fa-clock"></i> ${hero.readTime || '3 Min.'}</span>
                        </div>
                        <h2 class="font-header text-2xl sm:text-3xl md:text-4xl font-extrabold text-stone-900 dark:text-white leading-tight group-hover:text-sky-600 dark:group-hover:text-sky-400 transition">
                            ${hero.title}
                        </h2>
                        <p class="font-serif text-stone-600 dark:text-stone-300 text-base sm:text-lg line-clamp-3">
                            ${hero.subtitle}
                        </p>
                        <div class="flex items-center justify-between pt-2">
                            <span class="text-xs font-semibold text-stone-700 dark:text-stone-300"><i class="fa-solid fa-user-pen mr-1"></i> ${hero.author}</span>
                            <span class="text-xs font-bold text-sky-600 dark:text-sky-400 flex items-center gap-1 group-hover:translate-x-1 transition">
                                Artikel lesen <i class="fa-solid fa-arrow-right"></i>
                            </span>
                        </div>
                    </div>
                </div>
            `;

            // Render Cards Grid
            gridSection.innerHTML = remaining.map(item => `
                <article onclick="openArticleModal('${item.id}')" class="cursor-pointer group bg-white dark:bg-newspaper-cardDark rounded-xl overflow-hidden border border-stone-200 dark:border-stone-800 shadow-xs hover:shadow-lg transition flex flex-col h-full">
                    <div class="relative h-48 overflow-hidden">
                        <img src="${item.image}" alt="${item.title}" class="w-full h-full object-cover group-hover:scale-105 transition duration-500">
                        <span class="absolute top-3 left-3 bg-stone-900/80 backdrop-blur-xs text-white text-xs font-semibold px-2.5 py-0.5 rounded-md">
                            ${item.category}
                        </span>
                    </div>
                    <div class="p-5 flex flex-col flex-grow justify-between space-y-4">
                        <div class="space-y-2">
                            <div class="text-xs text-stone-400 flex items-center gap-2">
                                <span>${formatDate(item.date)}</span>
                                <span>•</span>
                                <span>${item.readTime || '3 Min.'}</span>
                            </div>
                            <h3 class="font-header text-lg font-bold text-stone-900 dark:text-white leading-snug group-hover:text-sky-600 dark:group-hover:text-sky-400 transition line-clamp-2">
                                ${item.title}
                            </h3>
                            <p class="font-serif text-sm text-stone-600 dark:text-stone-300 line-clamp-3">
                                ${item.subtitle}
                            </p>
                        </div>
                        <div class="pt-3 border-t border-stone-100 dark:border-stone-800 flex justify-between items-center text-xs text-stone-500">
                            <span><i class="fa-solid fa-user-pen mr-1"></i> ${item.author}</span>
                            <span class="text-sky-600 dark:text-sky-400 font-semibold group-hover:underline">Lesen &rarr;</span>
                        </div>
                    </div>
                </article>
            `).join('');
        }

        let currentFontSize = 18; // Default font size in px

        function openArticleModal(id) {
            const article = articles.find(a => a.id === id);
            if (!article) return;

            activeArticleModalId = id;
            
            // Increment view count
            article.views = (article.views || 0) + 1;
            saveArticles();

            document.getElementById('modal-category').innerText = article.category;
            document.getElementById('modal-title').innerText = article.title;
            document.getElementById('modal-subtitle').innerText = article.subtitle || '';
            document.getElementById('modal-author').innerHTML = `<i class="fa-solid fa-user-pen mr-1"></i> ${article.author}`;
            document.getElementById('modal-date').innerHTML = `<i class="fa-solid fa-calendar-day mr-1"></i> ${formatDate(article.date)}`;
            document.getElementById('modal-readtime').innerHTML = `<i class="fa-solid fa-clock mr-1"></i> ${article.readTime || calculateReadTime(article.content)}`;
            document.getElementById('modal-views').innerHTML = `<i class="fa-solid fa-eye mr-1"></i> ${article.views} Aufrufe`;
            
            const imgEl = document.getElementById('modal-image');
            if (article.image) {
                imgEl.src = article.image;
                document.getElementById('modal-image-container').classList.remove('hidden');
            } else {
                document.getElementById('modal-image-container').classList.add('hidden');
            }

            // Paragraph handling
            const bodyEl = document.getElementById('modal-body');
            const paragraphs = article.content.split('\n\n').filter(p => p.trim().length > 0);
            bodyEl.innerHTML = paragraphs.map(p => `<p>${escapeHTML(p)}</p>`).join('');
            bodyEl.style.fontSize = `${currentFontSize}px`;

            // Tags
            const tagsEl = document.getElementById('modal-tags');
            if (article.tags && article.tags.length > 0) {
                tagsEl.innerHTML = article.tags.map(t => `
                    <span class="bg-stone-100 dark:bg-stone-800 text-stone-600 dark:text-stone-300 text-xs px-2.5 py-1 rounded-md">
                        #${t.trim()}
                    </span>
                `).join('');
            } else {
                tagsEl.innerHTML = '';
            }

            // Comments
            renderModalComments(article);

            document.getElementById('article-modal').classList.remove('hidden');
            document.body.classList.add('overflow-hidden');
            document.getElementById('modal-scroll-area').scrollTop = 0;
        }

        function closeArticleModal() {
            document.getElementById('article-modal').classList.add('hidden');
            document.body.classList.remove('overflow-hidden');
            activeArticleModalId = null;
            if (window.speechSynthesis) window.speechSynthesis.cancel();
        }

        function adjustFontSize(delta) {
            currentFontSize = Math.max(14, Math.min(26, currentFontSize + delta));
            const bodyEl = document.getElementById('modal-body');
            if (bodyEl) bodyEl.style.fontSize = `${currentFontSize}px`;
        }

        function toggleReadAloud() {
            if (!('speechSynthesis' in window)) {
                showToast("Vorlesefunktion wird von diesem Browser nicht unterstützt.");
                return;
            }

            if (window.speechSynthesis.speaking) {
                window.speechSynthesis.cancel();
                showToast("Vorlesen angehalten.");
                return;
            }

            const article = articles.find(a => a.id === activeArticleModalId);
            if (!article) return;

            const textToRead = `${article.title}. ${article.subtitle}. ${article.content}`;
            ttsSpeechUtterance = new SpeechSynthesisUtterance(textToRead);
            ttsSpeechUtterance.lang = 'de-DE';
            ttsSpeechUtterance.rate = 0.95;

            window.speechSynthesis.speak(ttsSpeechUtterance);
            showToast("Artikel wird vorgelesen...");
        }

        function shareArticle() {
            const tempInput = document.createElement('input');
            tempInput.value = window.location.href;
            document.body.appendChild(tempInput);
            tempInput.select();
            document.execCommand('copy');
            document.body.removeChild(tempInput);
            showToast("Link in Zwischenablage kopiert!");
        }

        function renderModalComments(article) {
            const listEl = document.getElementById('modal-comments-list');
            const countEl = document.getElementById('modal-comments-count');
            const comments = article.comments || [];

            countEl.innerText = comments.length;

            if (comments.length === 0) {
                listEl.innerHTML = `<p class="text-sm text-stone-500 italic">Noch keine Kommentare. Sei der Erste!</p>`;
                return;
            }

            listEl.innerHTML = comments.map(c => `
                <div class="bg-stone-50 dark:bg-stone-900/60 p-3.5 rounded-xl border border-stone-200 dark:border-stone-800 text-sm">
                    <div class="flex justify-between items-center mb-1">
                        <span class="font-bold text-stone-900 dark:text-white">${escapeHTML(c.author)}</span>
                        <span class="text-xs text-stone-400">${c.date || 'Gerade eben'}</span>
                    </div>
                    <p class="text-stone-700 dark:text-stone-300 font-serif">${escapeHTML(c.text)}</p>
                </div>
            `).join('');
        }

        function handleAddComment(e) {
            e.preventDefault();
            if (!activeArticleModalId) return;

            const authorInput = document.getElementById('comment-author');
            const textInput = document.getElementById('comment-text');

            const article = articles.find(a => a.id === activeArticleModalId);
            if (!article) return;

            if (!article.comments) article.comments = [];

            article.comments.unshift({
                id: 'c-' + Date.now(),
                author: authorInput.value.trim() || 'Anonym',
                text: textInput.value.trim(),
                date: 'Heute'
            });

            saveArticles();
            renderModalComments(article);
            textInput.value = '';
            showToast("Kommentar veröffentlicht!");
        }

        function renderAdminArticles() {
            const list = document.getElementById('admin-articles-list');
            const count = document.getElementById('admin-article-count');

            count.innerText = articles.length;

            if (articles.length === 0) {
                list.innerHTML = `<p class="text-sm text-stone-500 p-4 text-center">Keine Artikel im System.</p>`;
                return;
            }

            list.innerHTML = articles.map(art => `
                <div class="p-3.5 rounded-xl border ${art.status === 'draft' ? 'border-amber-300 bg-amber-50/50 dark:bg-amber-950/20' : 'border-stone-200 dark:border-stone-800 bg-stone-50 dark:bg-stone-900/50'} flex items-center justify-between gap-3">
                    <div class="min-w-0 flex-grow">
                        <div class="flex items-center gap-2 mb-1">
                            <span class="text-[10px] uppercase font-bold px-2 py-0.5 rounded ${art.status === 'draft' ? 'bg-amber-200 text-amber-900' : 'bg-emerald-100 text-emerald-800 dark:bg-emerald-950 dark:text-emerald-300'}">
                                ${art.status === 'draft' ? 'Entwurf' : 'Live'}
                            </span>
                            <span class="text-xs text-stone-400">${art.category}</span>
                        </div>
                        <h4 class="font-bold text-sm text-stone-900 dark:text-white truncate">${escapeHTML(art.title)}</h4>
                        <p class="text-xs text-stone-500 truncate">Von ${escapeHTML(art.author)} • ${formatDate(art.date)}</p>
                    </div>
                    
                    <div class="flex items-center gap-1 shrink-0">
                        <button onclick="editArticle('${art.id}')" title="Bearbeiten" class="p-2 text-stone-600 dark:text-stone-300 hover:bg-stone-200 dark:hover:bg-stone-800 rounded-lg transition">
                            <i class="fa-solid fa-pen-to-square text-sm"></i>
                        </button>
                        <button onclick="deleteArticle('${art.id}')" title="Löschen" class="p-2 text-rose-600 hover:bg-rose-100 dark:hover:bg-rose-950/50 rounded-lg transition">
                            <i class="fa-solid fa-trash-can text-sm"></i>
                        </button>
                    </div>
                </div>
            `).join('');
        }

        function setPresetImage(type) {
            const presets = {
                school: 'https://images.unsplash.com/photo-1541829070764-84a7d30dd3f3?auto=format&fit=crop&w=1200&q=80',
                sports: 'https://images.unsplash.com/photo-1508098682722-e99c43a406b2?auto=format&fit=crop&w=1200&q=80',
                culture: 'https://images.unsplash.com/photo-1511671782779-c97d3d27a1d4?auto=format&fit=crop&w=1200&q=80',
                interview: 'https://images.unsplash.com/photo-1573496359142-b8d87734a5a2?auto=format&fit=crop&w=1200&q=80'
            };
            if (presets[type]) {
                document.getElementById('form-image').value = presets[type];
            }
        }

        function handleSaveArticle(e) {
            e.preventDefault();

            const id = document.getElementById('form-article-id').value;
            const title = document.getElementById('form-title').value.trim();
            const subtitle = document.getElementById('form-subtitle').value.trim();
            const category = document.getElementById('form-category').value;
            const author = document.getElementById('form-author').value.trim();
            const image = document.getElementById('form-image').value.trim() || 'https://images.unsplash.com/photo-1457369804613-52c61a468e7d?auto=format&fit=crop&w=1200&q=80';
            const content = document.getElementById('form-content').value.trim();
            const tagsRaw = document.getElementById('form-tags').value;
            const status = document.getElementById('form-status').value;

            const tags = tagsRaw.split(',').map(t => t.trim()).filter(t => t.length > 0);
            const readTime = calculateReadTime(content);

            if (id) {
                // Edit existing
                const idx = articles.findIndex(a => a.id === id);
                if (idx !== -1) {
                    articles[idx] = {
                        ...articles[idx],
                        title,
                        subtitle,
                        category,
                        author,
                        image,
                        content,
                        tags,
                        status,
                        readTime
                    };
                    showToast("Artikel erneuert und gespeichert!");
                }
            } else {
                // Create new
                const newArt = {
                    id: 'art-' + Date.now(),
                    title,
                    subtitle,
                    category,
                    author,
                    date: new Date().toISOString().split('T')[0],
                    readTime,
                    views: 0,
                    image,
                    content,
                    tags,
                    status,
                    comments: []
                };
                articles.unshift(newArt);
                showToast("Neuer Artikel erfolgreich hinzugefügt!");
            }

            saveArticles();
            resetEditorForm();
            renderAdminArticles();
            renderReaderView();
        }

        function editArticle(id) {
            const art = articles.find(a => a.id === id);
            if (!art) return;

            document.getElementById('form-article-id').value = art.id;
            document.getElementById('form-title').value = art.title;
            document.getElementById('form-subtitle').value = art.subtitle || '';
            document.getElementById('form-category').value = art.category;
            document.getElementById('form-author').value = art.author;
            document.getElementById('form-image').value = art.image || '';
            document.getElementById('form-content').value = art.content;
            document.getElementById('form-tags').value = (art.tags || []).join(', ');
            document.getElementById('form-status').value = art.status || 'published';

            document.getElementById('editor-form-title').innerText = "Artikel bearbeiten";
            document.getElementById('editing-badge').classList.remove('hidden');

            window.scrollTo({ top: 0, behavior: 'smooth' });
        }

        function deleteArticle(id) {
            if (confirm("Möchtest du diesen Artikel wirklich unwiderruflich löschen?")) {
                articles = articles.filter(a => a.id !== id);
                saveArticles();
                renderAdminArticles();
                renderReaderView();
                showToast("Artikel gelöscht.");
            }
        }

        function resetEditorForm() {
            document.getElementById('article-form').reset();
            document.getElementById('form-article-id').value = '';
            document.getElementById('editor-form-title').innerText = "Neuen Artikel verfassen";
            document.getElementById('editing-badge').classList.add('hidden');
        }

        function calculateReadTime(text) {
            const words = text.split(/\s+/).length;
            const minutes = Math.ceil(words / 180);
            return `${minutes} Min.`;
        }

        function formatDate(dateStr) {
            if (!dateStr) return '';
            const d = new Date(dateStr);
            if (isNaN(d.getTime())) return dateStr;
            return d.toLocaleDateString('de-DE', { day: '2-digit', month: '2-digit', year: 'numeric' });
        }

        function escapeHTML(str) {
            return str.replace(/[&<>'"]/g, 
                tag => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', "'": '&#39;', '"': '&quot;' }[tag] || tag)
            );
        }

        function showToast(msg) {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            toast.className = 'bg-stone-900 text-white dark:bg-white dark:text-stone-900 text-sm font-medium px-4 py-3 rounded-xl shadow-xl border border-stone-700 flex items-center gap-3 transition transform duration-300 pointer-events-auto';
            toast.innerHTML = `<i class="fa-solid fa-circle-check text-emerald-400 dark:text-emerald-600 text-base"></i> <span>${msg}</span>`;
