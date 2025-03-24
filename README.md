<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>SeeSharp Index Explorer</title>
  <link
    href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css"
    rel="stylesheet"
  />
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.1.1/css/all.min.css"
  />
  <style>
    /* --------------------------
       LAYOUT & THEME
    --------------------------- */
    html, body {
      margin: 0;
      padding: 0;
      height: 100%;
      overflow: hidden; /* Only #main-content will scroll */
    }

    body {
      font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
      background-color: var(--bg-primary);
      color: var(--text-primary);
      transition: background-color 0.3s, color 0.3s;
      display: flex;           /* Use flex layout so header is top, container is remainder */
      flex-direction: column;
    }

    :root {
      --bg-primary: #424242;
      --bg-secondary: #1f1e2c;
      --text-primary: #FFFFFF;
      --text-secondary: #B0B0B0;
      --accent: #673bb7;
      --accent-hover: #6A5ACD;
      --border: #4A4A4A;
      --hover: #3A3A3A;
      --selection: #3A3A3A;
      --highlight: #FCD34D;
    }

    body.light {
      --bg-primary: #F5F5F5;
      --bg-secondary: #D1C4E9;
      --text-primary: #333333;
      --text-secondary: #666666;
      --accent: #6A5ACD;
      --accent-hover: #5A4ABD;
      --border: #D3D3D3;
      --hover: #E6E6FA;
      --selection: #E6E6FA;
      --highlight: #FCD34D;
    }

    /* --------------------------
       HEADER
    --------------------------- */
    .header {
      background-color: var(--bg-secondary);
      border-bottom: 1px solid var(--border);
      padding: 0.75rem 1rem;
      display: flex;
      align-items: center;
      justify-content: space-between;
      box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
      flex-shrink: 0;
      flex-wrap: nowrap; /* Prevent wrapping */
    }

    .logo {
      font-size: 1.3rem;
      font-weight: 700;
      color: var(--accent);
      display: flex;
      align-items: center;
      gap: 0.5rem;
    }

    .header-controls {
      display: flex;
      gap: 0.75rem;
      align-items: center;
      flex-wrap: nowrap; /* Prevent buttons from wrapping */
      flex: 1;
      justify-content: flex-end;
    }

    /* --------------------------
       CONTAINER / SIDEBAR / MAIN
    --------------------------- */
    #container {
      display: flex;
      flex: 1;          /* Fill remaining space below header */
      overflow: hidden; /* Prevent second scrollbar on the page */
    }

    #sidebar {
      width: 300px;
      overflow-y: auto;
      background-color: var(--bg-secondary);
      border-right: 1px solid var(--border);
      transition: transform 0.3s ease;
    }

    #main-content {
      flex: 1;
      overflow-y: auto; /* The only scrollable region */
      padding: 20px;
      transition: margin-left 0.3s ease;
    }

    /* --------------------------
       TREEVIEW / SIDEBAR CONTENT
    --------------------------- */
    details {
      margin: 0.25rem 0;
      border-radius: 0.375rem;
    }

    summary {
      cursor: pointer;
      padding: 8px 12px;
      font-weight: 500;
      border-radius: 0.375rem;
      transition: background-color 0.2s;
      display: flex;
      align-items: center;
      gap: 0.5rem;
    }

    summary:hover {
      background-color: var(--hover);
    }

    ul {
      list-style-type: none;
      padding-left: 1.75rem;
      margin: 0.25rem 0;
    }

    #treeview ul li {
      cursor: pointer;
      padding: 6px 8px;
      border-radius: 0.25rem;
      margin: 2px 0;
      transition: background-color 0.2s;
      display: flex;
      align-items: center;
      gap: 0.5rem;
      font-size: 0.9rem;
    }

    #treeview ul li:hover {
      background-color: var(--hover);
    }

    #treeview ul li.selected {
      background-color: var(--selection);
      color: white;
    }

    .sidebar-header {
      padding: 1rem;
      display: flex;
      align-items: center;
      justify-content: space-between;
      border-bottom: 1px solid var(--border);
    }

    .section-title {
      font-weight: 600;
      margin-bottom: 1rem;
      display: flex;
      align-items: center;
      gap: 0.5rem;
    }

    /* --------------------------
       SEARCH
    --------------------------- */
    .search-container {
      position: relative;
      flex-grow: 1; /* Allows the search container to expand */
      max-width: 500px; /* Prevents it from becoming too wide */
      min-width: 200px; /* Ensures it doesn’t shrink too much */
    }

    .search-input {
      width: 100%;
      padding: 0.6rem 2rem 0.6rem 2.5rem;
      border: 1px solid var(--border);
      border-radius: 0.375rem;
      background-color: var(--bg-primary);
      color: var(--text-primary);
      outline: none;
      transition: border-color 0.2s, box-shadow 0.2s;
    }

    .search-input:focus {
      border-color: var(--accent);
      box-shadow: 0 0 0 2px rgba(59, 130, 246, 0.3);
    }

    .search-icon {
      position: absolute;
      left: 0.75rem;
      top: 50%;
      transform: translateY(-50%);
      color: var(--text-secondary);
    }

    /* --------------------------
       BUTTONS
    --------------------------- */
    button {
      display: flex;
      align-items: center;
      gap: 0.5rem;
      padding: 0.6rem 1rem;
      font-weight: 500;
      border-radius: 0.375rem;
      transition: background-color 0.2s, transform 0.1s;
      outline: none;
      border: none;
      cursor: pointer;
    }

    .btn-primary {
      background-color: var(--accent);
      color: white;
    }

    .btn-primary:hover {
      background-color: var(--accent-hover);
    }

    .btn-secondary {
      background-color: transparent;
      color: var(--text-primary);
      border: 1px solid var(--border);
    }

    .btn-secondary:hover {
      background-color: var(--hover);
    }

    button:active {
      transform: translateY(1px);
    }

    button:disabled {
      opacity: 0.6;
      cursor: not-allowed;
    }

    /* --------------------------
       ITEM STYLES
    --------------------------- */
    .badge {
      display: inline-flex;
      align-items: center;
      padding: 0.25rem 0.5rem;
      border-radius: 9999px;
      font-size: 0.75rem;
      font-weight: 500;
    }

    .badge-blue {
      background-color: rgba(59, 130, 246, 0.1);
      color: var(--accent);
    }

    .badge-green {
      background-color: rgba(34, 197, 94, 0.1);
      color: #22c55e;
    }

    .badge-purple {
      background-color: rgba(168, 85, 247, 0.1);
      color: #a855f7;
    }

    .badge-orange {
      background-color: rgba(249, 115, 22, 0.1);
      color: #f97316;
    }

    .method-item,
    .property-item,
    .field-item,
    .value-item,
    .search-result-item {
      padding: 0.75rem;
      border-radius: 0.375rem;
      margin-bottom: 0.5rem;
      background-color: var(--bg-secondary);
      border: 1px solid var(--border);
      transition: transform 0.2s;
    }

    .method-item:hover,
    .property-item:hover,
    .field-item:hover,
    .value-item:hover,
    .search-result-item:hover {
      transform: translateY(-2px);
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    }

    .method-name,
    .property-name,
    .field-name,
    .value-name {
      font-weight: 600;
      color: #a855f7;
    }

    .method-return {
      color: #22c55e;
    }

    .method-modifier,
    .property-modifier,
    .field-modifier {
      color: #f97316;
    }

    .method-params {
      color: var(--text-secondary);
    }

    .location {
      font-size: 0.75rem;
      color: var(--text-secondary);
    }

    .highlight {
      background-color: var(--highlight);
      color: #1e293b;
      padding: 0 0.25rem;
      border-radius: 0.25rem;
    }

    .details-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
      gap: 1rem;
    }

    .section {
      margin-bottom: 2rem;
    }

    .section-header {
      margin-bottom: 0.75rem;
      padding-bottom: 0.5rem;
      border-bottom: 1px solid var(--border);
      display: flex;
      align-items: center;
      justify-content: space-between;
    }

    .property-accessors {
      font-style: italic;
      color: var(--text-secondary);
    }

    .hamburger-btn {
      display: none;
      background: none;
      border: none;
      color: var(--text-primary);
      font-size: 1.5rem;
      cursor: pointer;
      padding: 0.25rem;
    }

    .theme-toggle {
      background: none;
      border: none;
      color: var(--text-primary);
      cursor: pointer;
      font-size: 1.1rem;
      padding: 0.5rem;
      border-radius: 0.25rem;
      transition: background-color 0.2s;
    }

    .theme-toggle:hover {
      background-color: var(--hover);
    }

    .empty-state {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 3rem;
      text-align: center;
      color: var(--text-secondary);
    }

    .empty-state i {
      font-size: 3rem;
      margin-bottom: 1rem;
    }

    .search-result-path {
      font-size: 0.75rem;
      color: var(--text-secondary);
      margin-bottom: 0.25rem;
    }

    .search-match-name {
      color: #a855f7;
      font-weight: 500;
    }

    .search-match-type {
      color: #f97316;
    }

    /* --------------------------
       CREATIVE "CLEAR" ANIMATION
    --------------------------- */
    @keyframes sweepOut {
      0% {
        opacity: 1;
        transform: scale(1) rotate(0);
      }
      50% {
        opacity: 0.6;
        transform: scale(0.9) rotate(-3deg);
      }
      100% {
        opacity: 0;
        transform: scale(0) rotate(10deg);
      }
    }

    .sweep-out {
      animation: sweepOut 0.6s forwards;
    }

    @media (max-width: 768px) {
      #sidebar {
        position: fixed;
        left: 0;
        top: 64px;
        bottom: 0;
        z-index: 10;
        transform: translateX(-100%);
      }

      #sidebar.open {
        transform: translateX(0);
      }

      .hamburger-btn {
        display: block;
      }

      .header-controls {
        flex: 1;
        justify-content: flex-end;
      }
    }
  </style>
</head>

<body class="dark">
  <header class="header">
    <div class="hamburger-btn" id="sidebar-toggle">
      <i class="fas fa-bars"></i>
    </div>
    <div class="logo">
      <i class="fas fa-code"></i>
      <span>SeeSharp Index Explorer</span>
    </div>
    <div class="header-controls">
      <button id="back-btn" class="btn-secondary" disabled>
        <i class="fas fa-arrow-left"></i>
      </button>
      <button id="forward-btn" class="btn-secondary" disabled>
        <i class="fas fa-arrow-right"></i>
      </button>
      <div class="search-container">
        <i class="fas fa-search search-icon"></i>
        <input
          type="text"
          id="search-input"
          class="search-input"
          placeholder="Search classes, methods, properties..."
        />
      </div>
      <button class="theme-toggle" id="theme-toggle" title="Toggle theme">
        <i class="fas fa-moon"></i>
      </button>
      <input type="file" id="import-file" accept=".json" class="hidden" />
      <button id="import-btn" class="btn-primary">
        <i class="fas fa-file-import"></i>
        <span>Import JSON</span>
      </button>
      <!-- NEW: Clear Explorer button -->
      <button id="clear-btn" class="btn-secondary">
        <i class="fas fa-broom"></i>
        <span>Clear Explorer</span>
      </button>
    </div>
  </header>

  <div id="container">
    <div id="sidebar">
      <div class="sidebar-header">
        <h2 class="section-title">
          <i class="fas fa-folder"></i>
          <span>Project Structure</span>
        </h2>
      </div>
      <div id="treeview" class="p-3"></div>
    </div>
    <div id="main-content">
      <div id="breadcrumb" class="text-sm text-gray-500 mb-2"></div>
      <div class="empty-state" id="empty-state">
        <i class="fas fa-code-branch"></i>
        <h2>Import a JSON file to get started</h2>
        <p>This tool helps you visualize and explore your codebase structure.</p>
        <button id="start-import-btn" class="btn-primary mt-4">
          <i class="fas fa-file-import"></i>
          <span>Import JSON</span>
        </button>
      </div>
      <h2 id="title" class="text-2xl font-bold mb-4 hidden"></h2>
      <div id="details" class="hidden"></div>
    </div>
  </div>

  <script>
    let data = null;
    let selectedItem = null;
    let searchTimeout;
    let isDarkMode = true;
    let history = [];
    let currentHistoryIndex = -1;

    // Function to update button visibility based on data presence
    function updateButtonVisibility() {
      const importBtn = document.getElementById('import-btn');
      const clearBtn = document.getElementById('clear-btn');
      if (data) {
        importBtn.style.display = 'none';
        clearBtn.style.display = 'flex';
      } else {
        importBtn.style.display = 'flex';
        clearBtn.style.display = 'none';
      }
    }

    // Theme toggling
    document.getElementById('theme-toggle').addEventListener('click', () => {
      isDarkMode = !isDarkMode;
      document.body.classList.toggle('light', !isDarkMode);
      document.getElementById('theme-toggle').innerHTML = isDarkMode
        ? '<i class="fas fa-sun"></i>'
        : '<i class="fas fa-moon"></i>';
    });

    // Mobile sidebar toggle
    document.getElementById('sidebar-toggle').addEventListener('click', () => {
      document.getElementById('sidebar').classList.toggle('open');
    });

    // Start import from empty state
    document.getElementById('start-import-btn').addEventListener('click', () => {
      document.getElementById('import-file').click();
    });

    // Highlight matching text
    function highlightText(text, query) {
      if (!query) return text;
      const regex = new RegExp(`(${query})`, 'gi');
      return text.replace(regex, '<span class="highlight">$1</span>');
    }

    // Check if text includes query
    function includesQuery(str, query) {
      return str.toLowerCase().includes(query.toLowerCase());
    }

    // Get matches for a class
    function getClassMatches(cls, query) {
      const matches = [];
      if (includesQuery(cls.name, query))
        matches.push({ type: 'class name', name: cls.name });
      cls.methods.forEach((method) => {
        if (includesQuery(method.name, query))
          matches.push({ type: 'method', name: method.name });
      });
      cls.properties.forEach((prop) => {
        if (includesQuery(prop.name, query))
          matches.push({ type: 'property', name: prop.name });
      });
      cls.fields.forEach((field) => {
        if (includesQuery(field.name, query))
          matches.push({ type: 'field', name: field.name });
      });
      return matches;
    }

    // Get matches for an enum
    function getEnumMatches(enumObj, query) {
      const matches = [];
      if (includesQuery(enumObj.name, query))
        matches.push({ type: 'enum name', name: enumObj.name });
      enumObj.values.forEach((value) => {
        if (includesQuery(value, query))
          matches.push({ type: 'value', name: value });
      });
      return matches;
    }

    // Get matches for an interface
    function getInterfaceMatches(interfaceObj, query) {
      const matches = [];
      if (includesQuery(interfaceObj.name, query))
        matches.push({ type: 'interface name', name: interfaceObj.name });
      if (interfaceObj.methods) {
        interfaceObj.methods.forEach((method) => {
          if (includesQuery(method.name, query))
            matches.push({ type: 'method', name: method.name });
        });
      }
      if (interfaceObj.properties) {
        interfaceObj.properties.forEach((prop) => {
          if (includesQuery(prop.name, query))
            matches.push({ type: 'property', name: prop.name });
        });
      }
      return matches;
    }

    // Build the tree view
    function buildTreeview() {
      const treeview = document.getElementById('treeview');
      treeview.innerHTML = '';
      if (!data || !data.files) return;

      data.files.forEach((file, fileIndex) => {
        const detailsEl = document.createElement('details');
        const summary = document.createElement('summary');
        summary.innerHTML = `<i class="fas fa-file-code"></i> ${file.name}`;
        detailsEl.appendChild(summary);
        const ul = document.createElement('ul');

        file.classes.forEach((cls, classIndex) => {
          const li = document.createElement('li');
          li.innerHTML = `<i class="fas fa-puzzle-piece"></i> ${cls.name}`;
          li.setAttribute('data-type', 'class');
          li.setAttribute('data-file-index', fileIndex);
          li.setAttribute('data-index', classIndex);
          li.addEventListener('click', (e) =>
            selectItem(e.currentTarget, 'class', fileIndex, classIndex)
          );
          ul.appendChild(li);
        });

        file.enums.forEach((enumObj, enumIndex) => {
          const li = document.createElement('li');
          li.innerHTML = `<i class="fas fa-list-ol"></i> ${enumObj.name}`;
          li.setAttribute('data-type', 'enum');
          li.setAttribute('data-file-index', fileIndex);
          li.setAttribute('data-index', enumIndex);
          li.addEventListener('click', (e) =>
            selectItem(e.currentTarget, 'enum', fileIndex, enumIndex)
          );
          ul.appendChild(li);
        });

        file.interfaces.forEach((interfaceObj, interfaceIndex) => {
          const li = document.createElement('li');
          li.innerHTML = `<i class="fas fa-sitemap"></i> ${interfaceObj.name}`;
          li.setAttribute('data-type', 'interface');
          li.setAttribute('data-file-index', fileIndex);
          li.setAttribute('data-index', interfaceIndex);
          li.addEventListener('click', (e) =>
            selectItem(e.currentTarget, 'interface', fileIndex, interfaceIndex)
          );
          ul.appendChild(li);
        });

        detailsEl.appendChild(ul);
        treeview.appendChild(detailsEl);
      });
    }

    // Select an item from the tree view
    function selectItem(element, type, fileIndex, index) {
      document.getElementById('sidebar').classList.remove('open');
      if (selectedItem) selectedItem.classList.remove('selected');
      selectedItem = element;
      selectedItem.classList.add('selected');
      document.getElementById('search-input').value = ''; // Clear search when selecting from tree
      const view = { viewType: 'item', type, fileIndex, index };
      addToHistory(view);
      updateMainContent(type, fileIndex, index);
      updateBreadcrumb();
    }

    // Update main content based on search or selection
    function updateMainContent(type, fileIndex, index, query = '') {
      document.getElementById('empty-state').classList.add('hidden');
      document.getElementById('title').classList.remove('hidden');
      document.getElementById('details').classList.remove('hidden');

      const details = document.getElementById('details');
      if (query) {
        // Search mode: display list of all hits
        const searchResults = [];
        data.files.forEach((file, fIndex) => {
          file.classes.forEach((cls, cIndex) => {
            const matches = getClassMatches(cls, query);
            matches.forEach((match) => {
              searchResults.push({
                fileName: file.name,
                type: 'class',
                name: cls.name,
                matchType: match.type,
                matchName: match.name,
                fileIndex: fIndex,
                index: cIndex
              });
            });
          });
          file.enums.forEach((enumObj, eIndex) => {
            const matches = getEnumMatches(enumObj, query);
            matches.forEach((match) => {
              searchResults.push({
                fileName: file.name,
                type: 'enum',
                name: enumObj.name,
                matchType: match.type,
                matchName: match.name,
                fileIndex: fIndex,
                index: eIndex
              });
            });
          });
          file.interfaces.forEach((interfaceObj, iIndex) => {
            const matches = getInterfaceMatches(interfaceObj, query);
            matches.forEach((match) => {
              searchResults.push({
                fileName: file.name,
                type: 'interface',
                name: interfaceObj.name,
                matchType: match.type,
                matchName: match.name,
                fileIndex: fIndex,
                index: iIndex
              });
            });
          });
        });

        if (searchResults.length === 0) {
          document.getElementById('title').innerHTML =
            '<i class="fas fa-search mr-2"></i> No Results Found';
          details.innerHTML = `<div class="empty-state">
            <i class="fas fa-search"></i>
            <p>No matches found for "${query}"</p>
            <p>Try a different search term</p>
          </div>`;
          return;
        }

        document.getElementById('title').innerHTML = `<i class="fas fa-search mr-2"></i> Search Results for "${query}" (${searchResults.length})`;
        details.innerHTML = '';

        searchResults.forEach((result) => {
          const div = document.createElement('div');
          div.className = 'search-result-item';

          const iconClass =
            result.type === 'class'
              ? 'puzzle-piece'
              : result.type === 'enum'
              ? 'list-ol'
              : 'sitemap';

          div.innerHTML = `
            <div class="search-result-path">
              <i class="fas fa-file-code mr-1"></i> ${highlightText(result.fileName, query)}
            </div>
            <div>
              <i class="fas fa-${iconClass} mr-1"></i>
              <span class="search-match-type">${result.type}:</span>
              <span class="search-match-name">${highlightText(result.name, query)}</span>
            </div>
            <div class="mt-1">
              <span class="search-match-type">${result.matchType}:</span>
              ${highlightText(result.matchName, query)}
            </div>
          `;

          div.addEventListener('click', () => {
            const view = {
              viewType: 'item',
              type: result.type,
              fileIndex: result.fileIndex,
              index: result.index
            };
            addToHistory(view);
            if (selectedItem) selectedItem.classList.remove('selected');
            selectedItem = document.querySelector(
              `#treeview li[data-type="${result.type}"][data-file-index="${result.fileIndex}"][data-index="${result.index}"]`
            );
            if (selectedItem) selectedItem.classList.add('selected');
            updateMainContent(result.type, result.fileIndex, result.index);
            updateBreadcrumb();
          });

          details.appendChild(div);
        });
      } else if (type === 'class') {
        displayClass(fileIndex, index);
      } else if (type === 'enum') {
        displayEnum(fileIndex, index);
      } else if (type === 'interface') {
        displayInterface(fileIndex, index);
      }
    }

    // Display class details
    function displayClass(fileIndex, classIndex) {
      const query = document.getElementById('search-input').value;
      const file = data.files[fileIndex];
      const cls = file.classes[classIndex];

      document.getElementById('title').innerHTML = `
        <i class="fas fa-puzzle-piece mr-2"></i>
        <span>${highlightText(cls.name, query)}</span>
        <span class="badge badge-blue ml-2">Class</span>
        <span class="location ml-2">Line ${cls.location.slice(1)}</span>
      `;

      const details = document.getElementById('details');
      details.innerHTML = `
        <div class="section">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div>
              <div class="badge badge-orange">${cls.accessibility}</div>
              ${
                cls.baseTypes.length
                  ? `
                <div class="mt-3">
                  <div class="text-sm text-gray-400">Base Types</div>
                  <div>${cls.baseTypes.join(', ') || 'None'}</div>
                </div>
              `
                  : ''
              }
            </div>
            <div>
              <div class="text-sm text-gray-400">File</div>
              <div>${file.name}</div>
            </div>
          </div>
        </div>
      `;

      if (cls.methods.length) {
        let methodsHtml = `
          <div class="section">
            <div class="section-header">
              <h3><i class="fas fa-code mr-2"></i> Methods (${cls.methods.length})</h3>
            </div>
            <div class="details-grid">
        `;

        cls.methods.forEach((method) => {
          const params = method.parameters
            .map(
              (p) => `<span class="method-params">${p.type} ${p.name}</span>`
            )
            .join(', ');
          const modifiers = [];
          if (method.isVirtual) modifiers.push('virtual');
          if (method.isStatic) modifiers.push('static');

          methodsHtml += `
            <div class="method-item">
              <div class="method-modifier">${method.accessibility} ${modifiers.join(' ')}</div>
              <div class="flex items-center">
                <span class="method-name">${highlightText(method.name, query)}</span>
                <span class="text-gray-400 ml-1">(${params})</span>
              </div>
              <div class="method-return">Returns: ${method.returnType}</div>
              <div class="location">Line ${method.location.slice(1)}</div>
            </div>
          `;
        });

        methodsHtml += `</div></div>`;
        details.innerHTML += methodsHtml;
      }

      if (cls.properties.length) {
        let propsHtml = `
          <div class="section">
            <div class="section-header">
              <h3><i class="fas fa-cubes mr-2"></i> Properties (${cls.properties.length})</h3>
            </div>
            <div class="details-grid">
        `;

        cls.properties.forEach((prop) => {
          const accessors = [];
          if (prop.hasGetter) accessors.push('get');
          if (prop.hasSetter) accessors.push('set');
          const modifiers = prop.isStatic ? 'static' : '';

          propsHtml += `
            <div class="property-item">
              <div class="property-modifier">${prop.accessibility} ${modifiers}</div>
              <div class="property-name">${highlightText(prop.name, query)}</div>
              <div>${prop.propertyType}</div>
              <div class="property-accessors">${accessors.join('; ')}</div>
              <div class="location">Line ${prop.location.slice(1)}</div>
            </div>
          `;
        });

        propsHtml += `</div></div>`;
        details.innerHTML += propsHtml;
      }

      if (cls.fields.length) {
        let fieldsHtml = `
          <div class="section">
            <div class="section-header">
              <h3><i class="fas fa-database mr-2"></i> Fields (${cls.fields.length})</h3>
            </div>
            <div class="details-grid">
        `;

        cls.fields.forEach((field) => {
          const modifiers = field.isStatic ? 'static' : '';

          fieldsHtml += `
            <div class="field-item">
              <div class="field-modifier">${field.accessibility} ${modifiers}</div>
              <div class="field-name">${highlightText(field.name, query)}</div>
              <div>${field.fieldType}</div>
              <div class="location">Line ${field.location.slice(1)}</div>
            </div>
          `;
        });

        fieldsHtml += `</div></div>`;
        details.innerHTML += fieldsHtml;
      }
    }

    // Display enum details
    function displayEnum(fileIndex, enumIndex) {
      const query = document.getElementById('search-input').value;
      const file = data.files[fileIndex];
      const enumObj = file.enums[enumIndex];

      document.getElementById('title').innerHTML = `
        <i class="fas fa-list-ol mr-2"></i>
        <span>${highlightText(enumObj.name, query)}</span>
        <span class="badge badge-green ml-2">Enum</span>
        <span class="location ml-2">Line ${enumObj.location.slice(1)}</span>
      `;

      const details = document.getElementById('details');
      details.innerHTML = `
        <div class="section">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div>
              <div class="badge badge-orange">${enumObj.accessibility}</div>
            </div>
            <div>
              <div class="text-sm text-gray-400">File</div>
              <div>${file.name}</div>
            </div>
          </div>
        </div>
        
        <div class="section">
          <div class="section-header">
            <h3><i class="fas fa-th-list mr-2"></i> Values (${enumObj.values.length})</h3>
          </div>
          <div class="details-grid">
      `;

      enumObj.values.forEach((value) => {
        details.innerHTML += `
          <div class="value-item">
            <div class="value-name">${highlightText(value, query)}</div>
          </div>
        `;
      });

      details.innerHTML += `</div></div>`;
    }

    // Display interface details
    function displayInterface(fileIndex, interfaceIndex) {
      const query = document.getElementById('search-input').value;
      const file = data.files[fileIndex];
      const interfaceObj = file.interfaces[interfaceIndex];

      document.getElementById('title').innerHTML = `
        <i class="fas fa-sitemap mr-2"></i>
        <span>${highlightText(interfaceObj.name, query)}</span>
        <span class="badge badge-purple ml-2">Interface</span>
        <span class="location ml-2">Line ${interfaceObj.location.slice(1)}</span>
      `;

      const details = document.getElementById('details');
      details.innerHTML = `
        <div class="section">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div>
              <div class="badge badge-orange">${interfaceObj.accessibility}</div>
            </div>
            <div>
              <div class="text-sm text-gray-400">File</div>
              <div>${file.name}</div>
            </div>
          </div>
        </div>
      `;

      if (interfaceObj.methods && interfaceObj.methods.length) {
        let methodsHtml = `
          <div class="section">
            <div class="section-header">
              <h3><i class="fas fa-code mr-2"></i> Methods (${interfaceObj.methods.length})</h3>
            </div>
            <div class="details-grid">
        `;

        interfaceObj.methods.forEach((method) => {
          const params = method.parameters
            .map((p) => `<span class="method-params">${p.type} ${p.name}</span>`)
            .join(', ');

          methodsHtml += `
            <div class="method-item">
              <div class="flex items-center">
                <span class="method-name">${highlightText(method.name, query)}</span>
                <span class="text-gray-400 ml-1">(${params})</span>
              </div>
              <div class="method-return">Returns: ${method.returnType}</div>
              <div class="location">Line ${method.location.slice(1)}</div>
            </div>
          `;
        });

        methodsHtml += `</div></div>`;
        details.innerHTML += methodsHtml;
      }

      if (interfaceObj.properties && interfaceObj.properties.length) {
        let propsHtml = `
          <div class="section">
            <div class="section-header">
              <h3><i class="fas fa-cubes mr-2"></i> Properties (${interfaceObj.properties.length})</h3>
            </div>
            <div class="details-grid">
        `;

        interfaceObj.properties.forEach((prop) => {
          propsHtml += `
            <div class="property-item">
              <div class="property-name">${highlightText(prop.name, query)}</div>
              <div>${prop.propertyType}</div>
              <div class="location">Line ${prop.location.slice(1)}</div>
            </div>
          `;
        });

        propsHtml += `</div></div>`;
        details.innerHTML += propsHtml;
      }
    }

    // Import JSON file
    document.getElementById('import-btn').addEventListener('click', () => {
      document.getElementById('import-file').click();
    });

    document.getElementById('import-file').addEventListener('change', (event) => {
      const file = event.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = (e) => {
          try {
            data = JSON.parse(e.target.result);
            buildTreeview();
            document.getElementById('empty-state').classList.add('hidden');
            document.getElementById('title').classList.remove('hidden');
            document.getElementById('title').innerHTML = `<i class="fas fa-check-circle mr-2 text-green-500"></i> JSON Imported: ${data.name}`;
            document.getElementById('details').classList.remove('hidden');
            document.getElementById('details').innerHTML = `
              <div class="p-4 bg-green-100 text-green-800 rounded-lg mb-4 dark:bg-green-900 dark:text-green-200">
                <h3 class="font-bold mb-2">Import Successful</h3>
                <p>${data.description}</p>
                <p class="mt-2 text-sm">Created At: ${new Date(data.createdAt).toLocaleString()}</p>
              </div>
              <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
                <div class="bg-blue-100 p-4 rounded-lg dark:bg-blue-900">
                  <h3 class="font-bold mb-2 text-blue-800 dark:text-blue-200"><i class="fas fa-file-code mr-2"></i> Files</h3>
                  <p class="text-2xl font-bold">${data.totalFileCount}</p>
                </div>
                <div class="bg-blue-100 p-4 rounded-lg dark:bg-blue-900">
                  <h3 class="font-bold mb-2 text-blue-800 dark:text-blue-200"><i class="fas fa-puzzle-piece mr-2"></i> Classes</h3>
                  <p class="text-2xl font-bold">${data.totalClassCount}</p>
                </div>
                <div class="bg-purple-100 p-4 rounded-lg dark:bg-purple-900">
                  <h3 class="font-bold mb-2 text-purple-800 dark:text-purple-200"><i class="fas fa-sitemap mr-2"></i> Interfaces</h3>
                  <p class="text-2xl font-bold">${data.totalInterfaceCount}</p>
                </div>
                <div class="bg-green-100 p-4 rounded-lg dark:bg-green-900">
                  <h3 class="font-bold mb-2 text-green-800 dark:text-green-200"><i class="fas fa-list-ol mr-2"></i> Enums</h3>
                  <p class="text-2xl font-bold">${data.totalEnumCount}</p>
                </div>
              </div>
            `;
            updateButtonVisibility();
          } catch (error) {
            alert('Invalid JSON file: ' + error.message);
          }
        };
        reader.readAsText(file);
      }
    });

    // NEW: Clear Explorer functionality
    document.getElementById('clear-btn').addEventListener('click', () => {
      const container = document.getElementById('container');
      container.classList.add('sweep-out');

      container.addEventListener('animationend', function handleAnimationEnd() {
        container.removeEventListener('animationend', handleAnimationEnd);
        container.classList.remove('sweep-out');

        data = null;
        history = [];
        currentHistoryIndex = -1;
        selectedItem = null;

        document.getElementById('treeview').innerHTML = '';
        document.getElementById('search-input').value = '';
        document.getElementById('breadcrumb').innerHTML = '';
        document.getElementById('title').classList.add('hidden');
        document.getElementById('details').classList.add('hidden');
        document.getElementById('details').innerHTML = '';
        document.getElementById('empty-state').classList.remove('hidden');
        document.getElementById('empty-state').innerHTML = `
          <i class="fas fa-smile-wink"></i>
          <h2>Your code has been swept away!</h2>
          <p>Import a new JSON file to explore another codebase.</p>
          <button id="start-import-btn" class="btn-primary mt-4">
            <i class="fas fa-file-import"></i>
            <span>Import JSON</span>
          </button>
        `;

        document.getElementById('import-file').value = ''; // Reset the file input
        document.getElementById('start-import-btn').addEventListener('click', () => {
          document.getElementById('import-file').click();
        });

        updateButtonVisibility();
      });
    });

    // Handle search input
    document.getElementById('search-input').addEventListener('input', (e) => {
      clearTimeout(searchTimeout);
      searchTimeout = setTimeout(() => {
        const query = e.target.value;
        if (query) {
          const view = { viewType: 'search', query };
          addToHistory(view);
          updateMainContent(null, null, null, query);
          updateBreadcrumb();
        } else if (selectedItem) {
          const type = selectedItem.getAttribute('data-type');
          const fileIndex = parseInt(selectedItem.getAttribute('data-file-index'));
          const index = parseInt(selectedItem.getAttribute('data-index'));
          updateMainContent(type, fileIndex, index);
          updateBreadcrumb();
        } else {
          document.getElementById('title').classList.add('hidden');
          document.getElementById('details').classList.add('hidden');
          if (data) {
            document.getElementById('empty-state').classList.remove('hidden');
            document.getElementById('empty-state').innerHTML = `
              <i class="fas fa-hand-point-left"></i>
              <h2>Select an item from the sidebar</h2>
              <p>Or use the search box to find specific elements</p>
            `;
          } else {
            document.getElementById('empty-state').classList.remove('hidden');
          }
        }
      }, 300);
    });

    // Close sidebar when clicking outside on mobile
    document.getElementById('main-content').addEventListener('click', () => {
      if (window.innerWidth <= 768) {
        document.getElementById('sidebar').classList.remove('open');
      }
    });

    // Navigation history functions
    function addToHistory(view) {
      if (
        currentHistoryIndex >= 0 &&
        history[currentHistoryIndex].viewType === 'search' &&
        view.viewType === 'search'
      ) {
        history[currentHistoryIndex] = view;
      } else {
        history = history.slice(0, currentHistoryIndex + 1);
        history.push(view);
        currentHistoryIndex = history.length - 1;
      }
      updateNavigationButtons();
    }

    function goBack() {
      if (currentHistoryIndex > 0) {
        currentHistoryIndex--;
        updateMainContentFromHistory();
        updateNavigationButtons();
      }
    }

    function goForward() {
      if (currentHistoryIndex < history.length - 1) {
        currentHistoryIndex++;
        updateMainContentFromHistory();
        updateNavigationButtons();
      }
    }

    function updateNavigationButtons() {
      document.getElementById('back-btn').disabled = currentHistoryIndex <= 0;
      document.getElementById('forward-btn').disabled =
        currentHistoryIndex >= history.length - 1;
    }

    function updateMainContentFromHistory() {
      const view = history[currentHistoryIndex];
      if (view.viewType === 'item') {
        document.getElementById('search-input').value = '';
        updateMainContent(view.type, view.fileIndex, view.index);
        const selector = `#treeview li[data-type="${view.type}"][data-file-index="${view.fileIndex}"][data-index="${view.index}"]`;
        const element = document.querySelector(selector);
        if (element) {
          if (selectedItem) selectedItem.classList.remove('selected');
          selectedItem = element;
          selectedItem.classList.add('selected');
        }
      } else if (view.viewType === 'search') {
        document.getElementById('search-input').value = view.query;
        updateMainContent(null, null, null, view.query);
      }
      updateBreadcrumb();
    }

    function updateBreadcrumb() {
      const breadcrumb = document.getElementById('breadcrumb');
      if (history.length === 0 || currentHistoryIndex < 0) {
        breadcrumb.innerHTML = '';
        return;
      }
      const view = history[currentHistoryIndex];
      if (view.viewType === 'item') {
        const file = data.files[view.fileIndex];
        const item =
          view.type === 'class'
            ? file.classes[view.index]
            : view.type === 'enum'
            ? file.enums[view.index]
            : file.interfaces[view.index];
        breadcrumb.innerHTML = `<span>${file.name}</span> > <span>${item.name}</span>`;
      } else if (view.viewType === 'search') {
        breadcrumb.innerHTML = `Search Results for "${view.query}"`;
      }
    }

    // Attach event listeners to navigation buttons
    document.getElementById('back-btn').addEventListener('click', goBack);
    document.getElementById('forward-btn').addEventListener('click', goForward);

    // Initialize button visibility
    updateButtonVisibility();

    if (data) buildTreeview();
  </script>
</body>
</html>
