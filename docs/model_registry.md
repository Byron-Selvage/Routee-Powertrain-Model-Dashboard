# Model Registry

Use this page to search and download trained RouteE Powertrain models.

<style>
  .dashboard-container {
    max-width: 100%;
    margin: 20px 0;
    color: var(--pst-color-text-base, #333);
  }
  .filters-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
    gap: 12px;
    margin-bottom: 24px;
    padding: 16px;
    background: var(--pst-color-surface, #f8f9fa);
    border-radius: 8px;
    border: 1px solid var(--pst-color-border, #ddd);
  }
  .filters-grid input, .filters-grid select {
    padding: 8px 12px;
    border: 1px solid var(--pst-color-border, #ccc);
    border-radius: 6px;
    font-size: 0.95rem;
    background-color: var(--pst-color-background, #fff);
    color: var(--pst-color-text-base, #333);
  }
  .filters-grid input[type="search"] {
    grid-column: 1 / -1;
  }
  .result-card {
    border: 1px solid var(--pst-color-border, #ddd);
    border-radius: 8px;
    padding: 20px;
    margin-bottom: 20px;
    background: var(--pst-color-background, #fff);
    box-shadow: 0 2px 4px rgba(0,0,0,0.05);
  }
  .result-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    flex-wrap: wrap;
    gap: 10px;
    border-bottom: 1px solid var(--pst-color-border, #eee);
    padding-bottom: 12px;
    margin-bottom: 16px;
  }
  .result-title {
    margin: 0;
    font-size: 1.3rem;
    color: #0f6cbd;
    font-weight: 600;
  }
  .meta-tag {
    font-size: 0.9rem;
    font-weight: 500;
    color: var(--pst-color-text-muted, #666);
  }
  .feature-list {
    display: flex;
    flex-wrap: wrap;
    gap: 6px;
    list-style: none;
    padding: 0;
    margin: 8px 0 16px 0;
  }
  .feature-list li {
    background: rgba(15, 108, 189, 0.1);
    color: #0f6cbd;
    padding: 4px 10px;
    border-radius: 12px;
    font-size: 0.85rem;
    font-weight: 500;
  }
  .snippet-wrapper {
    position: relative;
    margin: 12px 0;
  }
  .snippet-box {
    background: #1e1e1e;
    color: #d4d4d4;
    padding: 12px 40px 12px 12px;
    border-radius: 6px;
    font-family: var(--pst-font-family-monospace, monospace);
    font-size: 0.85rem;
    white-space: pre-wrap;
    overflow-x: auto;
  }
  .copy-btn {
    position: absolute;
    top: 8px;
    right: 8px;
    background: rgba(255, 255, 255, 0.15);
    border: none;
    color: #fff;
    padding: 4px 8px;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.75rem;
    font-weight: 600;
  }
  .copy-btn:hover {
    background: rgba(255, 255, 255, 0.3);
  }
  .actions-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    gap: 12px;
    margin-top: 16px;
  }
  .btn-download {
    display: inline-flex;
    align-items: center;
    padding: 8px 16px;
    background: #0f6cbd;
    color: white !important;
    text-decoration: none !important;
    border-radius: 6px;
    font-size: 0.9rem;
    font-weight: 600;
    border: none;
    cursor: pointer;
  }
  .btn-download:hover {
    background: #0a4b85;
  }
  .version-select-wrapper {
    display: flex;
    align-items: center;
    gap: 8px;
  }
  .version-select {
    padding: 6px 10px;
    border-radius: 6px;
    border: 1px solid var(--pst-color-border, #ccc);
    background-color: var(--pst-color-background, #fff);
    color: var(--pst-color-text-base, #333);
    font-size: 0.85rem;
  }
  #status-message {
    font-size: 1.1rem;
    text-align: center;
    padding: 40px 20px;
    color: var(--pst-color-text-muted, #666);
  }
</style>


<div class="dashboard-container">
  <div class="filters-grid">
    <input type="search" id="search-input" placeholder="Search models, powertrain, specs, features..." oninput="handleSearch()">
    <select id="make-filter" onchange="handleSearch()">
      <option value="">All Makes</option>
    </select>
    <select id="model-filter" onchange="handleSearch()">
      <option value="">All Models</option>
    </select>
    <select id="powertrain-filter" onchange="handleSearch()">
      <option value="">All Powertrains</option>
    </select>
    <input type="number" id="year-min" placeholder="Year Min" oninput="handleSearch()">
    <input type="number" id="year-max" placeholder="Year Max" oninput="handleSearch()">
  </div>


  <div id="results-container">
    <div id="status-message">Loading model registry...</div>
  </div>
</div>


<script>
  const INDEX_URL = 'https://raw.githubusercontent.com/Byron-Selvage/Routee-Powertrain-Model-Dashboard/refs/heads/main/index.json';
  let allModels = [];
  let groupedModels = [];

  // Helper to format model titles
  function formatTitle(value) {
    if (value == null || value === '') {
      return 'Unknown';
    }

    // Replace underscores and capitalize the first letter of each word
    const titleCased = String(value)
      .replace(/_/g, ' ')
      .replace(/\b[a-z]/g, (match) => match.toUpperCase());

    // Capitalize words 3 letters or less (e.g. BMW, BYD)
    const words = titleCased.split(' ');
    const formattedWords = words.map(word => {
      const wordOnly = word.replace(/[^\w\s]/gi, '');
      if (wordOnly.length <= 3) {
        return word.toUpperCase();
      }
      return word;
    });

    return formattedWords.join(' ');
  }

  // Helper to safely parse and find range for various formats of year info
  function parseYearRange(yearData) {
    if (Array.isArray(yearData)) {
      const years = yearData.map(Number).filter(n => !isNaN(n));
      if (years.length === 0) return { min: -Infinity, max: Infinity };
      return { min: Math.min(...years), max: Math.max(...years) };
    }
    if (typeof yearData === 'number') {
      return { min: yearData, max: yearData };
    }
    if (typeof yearData === 'string') {
      if (yearData.includes('-')) {
        const parts = yearData.split('-').map(s => parseInt(s.trim(), 10)).filter(n => !isNaN(n));
        if (parts.length === 2) {
          return { min: parts[0], max: parts[1] };
        }
      }
      const parsed = parseInt(yearData, 10);
      if (!isNaN(parsed)) {
        return { min: parsed, max: parsed };
      }
    }
    return { min: -Infinity, max: Infinity };
  }

  // Format year array or single value to string
  function formatYear(yearData) {
    if (Array.isArray(yearData)) {
      if (yearData.length === 1) return yearData[0];
      return `${Math.min(...yearData)} - ${Math.max(...yearData)}`;
    }
    return yearData || 'Unknown';
  }

  // Generate python loading snippet
  function getSnippet(model) {
    const make = model.model_id?.make || '';
    const modelName = model.vehicle_model || model.model_id?.vehicle_slug || '';
    const year = model.model_id?.year || '';
    const config = model.model_id?.config_slug || '';
    const version = model.model_id?.version || '';
    
    const modelId = `${make}_${modelName}_${year}_${config}_v${version}`
      .replace(/_+/g, '_')
      .replace(/^_|_$/g, '');
      
    return `import nrel.routee.powertrain as pt\n\nmodel = pt.load_model("${modelId}")\nprint(model)`;
  }

  // Load the model catalog from relative path
  async function loadIndex() {
    const indexUrls = [INDEX_URL, './_static/index.json'];
    let lastAttempt = { indexUrl: INDEX_URL, error: null };

    for (const indexUrl of indexUrls) {
      try {
        const response = await fetch(indexUrl);
        if (!response.ok) throw new Error(`HTTP status: ${response.status}`);
        const data = await response.json();
        allModels = data.models || [];

        populateFilters();
        groupModels();
        handleSearch();
        return;
      } catch (error) {
        lastAttempt = { indexUrl, error };
      }
    }

      document.getElementById('results-container').innerHTML = `
        <div id="status-message" style="color: #d9534f;">
          <strong>Error: Failed to load model catalog.</strong><br>
          <span style="font-size: 0.9rem; display: block; margin-top: 8px;">
            Could not retrieve data from <code>${lastAttempt.indexUrl}</code>. Make sure the published site includes a readable <code>index.json</code>.
          </span>
        </div>`;
      console.error('Error loading index.json:', lastAttempt.error);
  }

  // Populate dynamic filtering dropdowns
  function populateFilters() {
    const makes = [...new Set(allModels.map(m => m.model_id?.make).filter(Boolean))].sort();
    const models = [...new Set(allModels.map(m => m.vehicle_model || m.model_id?.vehicle_slug).filter(Boolean))].sort();
    const powertrains = [...new Set(allModels.map(m => m.powertrain_type).filter(Boolean))].sort();

    const makeSelect = document.getElementById('make-filter');
    const modelSelect = document.getElementById('model-filter');
    const ptSelect = document.getElementById('powertrain-filter');

    makes.forEach(make => makeSelect.add(new Option(make, make)));
    models.forEach(model => modelSelect.add(new Option(model, model)));
    powertrains.forEach(pt => ptSelect.add(new Option(pt, pt)));
  }

  // Group models by key (make + vehicle + powertrain) and sort by version (descending)
  function groupModels() {
    const groups = {};

    allModels.forEach(model => {
      const make = model.model_id?.make || 'Unknown';
      const vehicle = model.vehicle_model || model.model_id?.vehicle_slug || 'Unknown';
      const pt = model.powertrain_type || 'Unknown';
      const key = `${make}|${vehicle}|${pt}`.toLowerCase();

      if (!groups[key]) groups[key] = [];
      groups[key].push(model);
    });

    groupedModels = Object.values(groups).map(group => {
      return group.sort((a, b) => {
        const vA = parseInt(a.model_id?.version, 10) || 0;
        const vB = parseInt(b.model_id?.version, 10) || 0;
        return vB - vA;
      });
    });
  }

  // Perform search and filtering on grouped list
  function handleSearch() {
    const query = document.getElementById('search-input').value.toLowerCase();
    const make = document.getElementById('make-filter').value;
    const modelStr = document.getElementById('model-filter').value;
    const pt = document.getElementById('powertrain-filter').value;
    const yearMin = parseInt(document.getElementById('year-min').value, 10);
    const yearMax = parseInt(document.getElementById('year-max').value, 10);

    const filtered = groupedModels.filter(group => {
      const latest = group[0]; // Filter against the latest version by default

      // Make Filter
      if (make && latest.model_id?.make !== make) return false;

      // Model Filter
      const vModel = latest.vehicle_model || latest.model_id?.vehicle_slug;
      if (modelStr && vModel !== modelStr) return false;

      // Powertrain Filter
      if (pt && latest.powertrain_type !== pt) return false;

      // Year Filter
      const { min: mMin, max: mMax } = parseYearRange(latest.model_id?.year);
      if (!isNaN(yearMin) && mMax < yearMin) return false;
      if (!isNaN(yearMax) && mMin > yearMax) return false;

      // Text Query
      if (query) {
        const matchText = [
          latest.model_id?.make,
          latest.vehicle_model,
          latest.model_id?.vehicle_slug,
          latest.powertrain_type,
          latest.vehicle_description,
          ...(latest.feature_names || [])
        ].join(' ').toLowerCase();

        if (!matchText.includes(query)) return false;
      }

      return true;
    });

    renderResults(filtered);
  }

  // Render list of filtered results
  function renderResults(results) {
    const container = document.getElementById('results-container');
    container.innerHTML = '';

    if (results.length === 0) {
      container.innerHTML = '<div id="status-message">No models match your search criteria.</div>';
      return;
    }

    results.forEach((group, groupIndex) => {
      const latest = group[0];
      const card = document.createElement('div');
      card.className = 'result-card';

      const title = `${formatTitle(latest.model_id?.make)} ${formatTitle(latest.vehicle_model || latest.model_id?.vehicle_slug || '')}`
      const subtitle = latest.vehicle_description ? `<div style="font-size: 0.9rem; color: var(--pst-color-text-muted, #555); margin-bottom: 12px;">${latest.vehicle_description}</div>` : '';

      const features = (latest.feature_names || []).map(f => `<li>${f}</li>`).join('');
      const snippet = getSnippet(latest);

      let versionSelector = '';
      if (group.length > 1) {
        const options = group.map((m, i) => 
          `<option value="${i}">Version ${m.model_id?.version || 'Unknown'}</option>`
        ).join('');
        versionSelector = `
          <div class="version-select-wrapper">
            <span style="font-size: 0.85rem; font-weight: 500;">Select version:</span>
            <select class="version-select" onchange="updateCardVersion(this, ${groupIndex})">
              ${options}
            </select>
          </div>
        `;
      } else {
        versionSelector = `<span style="font-size: 0.85rem; color: var(--pst-color-text-muted, #777); font-weight: 500;">Version ${latest.model_id?.version || 'Unknown'}</span>`;
      }

      card.innerHTML = `
        <div class="result-header">
          <div>
            <h2 class="result-title">${title}</h2>
            <div style="font-size: 0.85rem; color: #0f6cbd; font-weight: bold; margin-top: 4px; text-transform: uppercase;">
              ${latest.powertrain_type || 'Unknown Powertrain'}
            </div>
          </div>
          <span class="meta-tag"><strong>Year:</strong> <span class="year-display">${formatYear(latest.model_id?.year)}</span></span>
        </div>

        ${subtitle}

        <div style="font-size: 0.85rem; font-weight: bold;">Expected Features:</div>
        <ul class="feature-list">
          ${features || '<li>No features listed</li>'}
        </ul>

        <div class="snippet-wrapper">
          <button class="copy-btn" onclick="copySnippet(this)">Copy</button>
          <div class="snippet-box">${snippet}</div>
        </div>

        <div class="actions-row">
          <a href="https://www.youtube.com/watch?v=dQw4w9WgXcQ" class="btn-download" target="_blank">Download Model Asset</a>
          ${versionSelector}
        </div>
      `;

      container.appendChild(card);
    });
  }

  // Update dynamic properties when user toggles a version
  window.updateCardVersion = function(selectElement, groupIndex) {
    const versionIndex = selectElement.value;
    const model = groupedModels[groupIndex][versionIndex];
    const card = selectElement.closest('.result-card');

    // Update expected features
    const featureList = card.querySelector('.feature-list');
    featureList.innerHTML = (model.feature_names || []).map(f => `<li>${f}</li>`).join('') || '<li>No features listed</li>';

    // Update year display
    card.querySelector('.year-display').innerText = formatYear(model.model_id?.year);

    // Update load snippet
    card.querySelector('.snippet-box').innerText = getSnippet(model);

    // Update download path
    const downloadBtn = card.querySelector('.btn-download');
    downloadBtn.href = "https://www.youtube.com/watch?v=dQw4w9WgXcQ";
  };

  // Quick clipboard utility
  window.copySnippet = async function(button) {
    const code = button.nextElementSibling.innerText;
    try {
      await navigator.clipboard.writeText(code);
      button.innerText = 'Copied!';
      setTimeout(() => { button.innerText = 'Copy'; }, 1500);
    } catch (err) {
      console.error('Failed to copy code: ', err);
    }
  };

  // Bootstrap Page
  document.addEventListener('DOMContentLoaded', loadIndex);
</script>