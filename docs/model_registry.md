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
    justify-content: flex-end;
    align-items: center;
    flex-wrap: wrap;
    gap: 12px;
    margin-top: 16px;
  }
  .feature-set-card {
    border: 1px solid var(--pst-color-border, #e5e5e5);
    border-radius: 8px;
    padding: 14px;
    margin-top: 12px;
    background: var(--pst-color-surface, #fafafa);
  }
  .architecture-group {
    border: 1px solid var(--pst-color-border, #d9d9d9);
    border-radius: 10px;
    margin-top: 14px;
    overflow: hidden;
    background: var(--pst-color-background, #fff);
  }
  .architecture-group-header {
    padding: 10px 14px;
    background: rgba(15, 108, 189, 0.08);
    color: #0f6cbd;
    font-size: 0.85rem;
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 0.04em;
    border-bottom: 1px solid var(--pst-color-border, #d9d9d9);
  }
  .architecture-group-body {
    padding: 14px;
    display: grid;
    gap: 12px;
  }
  .feature-set-title {
    font-size: 0.95rem;
    font-weight: 700;
    margin: 0 0 10px 0;
    color: var(--pst-color-text-base, #333);
  }
  .version-label {
    font-size: 0.85rem;
    font-weight: 500;
    color: var(--pst-color-text-muted, #777);
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
  // const INDEX_URL = 'https://raw.githubusercontent.com/Byron-Selvage/Routee-Powertrain-Model-Dashboard/refs/heads/main/index.json';
  const INDEX_URL = '../../../dummy_index.json';
  let allModels = [];
  let groupedModels = [];
  let currentRenderedGroups = [];

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
    const formattedWords = words.map((word) => {
      const wordOnly = word.replace(/[^\w\s]/gi, '');
      if (wordOnly.length <= 3) {
        return word.toUpperCase();
      }
      return word;
    });

    return formattedWords.join(' ');
  }

  function formatArchitectureTag(value) {
    const labels = {
      random_forest: 'Random Forest',
      ngboost: 'NGBoost',
      cnn: 'CNN'
    };

    const key = String(value || '').toLowerCase();
    return labels[key] || formatTitle(value);
  }

  // Helper to safely parse and find range for various formats of year info
  function parseYearRange(yearData) {
    if (Array.isArray(yearData)) {
      const years = yearData.map(Number).filter((n) => !isNaN(n));
      if (years.length === 0) return { min: -Infinity, max: Infinity };
      return { min: Math.min(...years), max: Math.max(...years) };
    }
    if (typeof yearData === 'number') {
      return { min: yearData, max: yearData };
    }
    if (typeof yearData === 'string') {
      if (yearData.includes('-')) {
        const parts = yearData.split('-').map((s) => parseInt(s.trim(), 10)).filter((n) => !isNaN(n));
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

  function getFeatureSetName(model, idx) {
    const candidates = [
      model.feature_set_name,
      model.feature_set,
      model.model_name,
      model.model_id?.config_slug
    ];
    const value = candidates.find((v) => v != null && String(v).trim() !== '');
    return value ? formatTitle(value) : `Feature Set ${idx + 1}`;
  }

  function getDownloadUrl(model) {
    const candidates = [
      model.model_asset_url,
      model.download_url,
      model.model_url,
      model.url
    ];
    const value = candidates.find((v) => typeof v === 'string' && v.trim() !== '');
    return value || 'https://www.youtube.com/watch?v=dQw4w9WgXcQ';
  }

  function getVersionYearRange(versionGroup) {
    const ranges = versionGroup.featureSets.map((m) => parseYearRange(m.model_id?.year));
    const mins = ranges.map((r) => r.min).filter(Number.isFinite);
    const maxes = ranges.map((r) => r.max).filter(Number.isFinite);

    if (mins.length === 0 || maxes.length === 0) {
      return { min: -Infinity, max: Infinity };
    }

    return {
      min: Math.min(...mins),
      max: Math.max(...maxes)
    };
  }

  function getVersionYearDisplay(versionGroup) {
    const range = getVersionYearRange(versionGroup);
    if (!Number.isFinite(range.min) || !Number.isFinite(range.max)) return 'Unknown';
    return range.min === range.max ? String(range.min) : `${range.min} - ${range.max}`;
  }

  function renderFeatureSets(versionGroup) {
    const architectureGroups = {};

    versionGroup.featureSets.forEach((model) => {
      const architectureKey = String(model.architecture_tag || 'unknown').toLowerCase();
      const architectureLabel = formatArchitectureTag(model.architecture_tag);

      if (!architectureGroups[architectureKey]) {
        architectureGroups[architectureKey] = {
          architectureLabel,
          featureSets: []
        };
      }

      architectureGroups[architectureKey].featureSets.push(model);
    });

    return Object.values(architectureGroups).map((architectureGroup) => {
      const cards = architectureGroup.featureSets.map((model, idx) => {
        const features = (model.feature_names || []).map((f) => `<li>${f}</li>`).join('');
        const snippet = getSnippet(model);
        const featureSetName = getFeatureSetName(model, idx);
        const downloadUrl = getDownloadUrl(model);

        return `
          <div class="feature-set-card">
            <h3 class="feature-set-title">${featureSetName}</h3>

            <div style="font-size: 0.85rem; font-weight: bold;">Expected Features:</div>
            <ul class="feature-list">
              ${features || '<li>No features listed</li>'}
            </ul>

            <div class="snippet-wrapper">
              <button class="copy-btn" onclick="copySnippet(this)">Copy</button>
              <div class="snippet-box">${snippet}</div>
            </div>

            <a href="${downloadUrl}" class="btn-download" target="_blank">Download Model Asset</a>
          </div>
        `;
      }).join('');

      return `
        <div class="architecture-group">
          <div class="architecture-group-header">Architecture: ${architectureGroup.architectureLabel}</div>
          <div class="architecture-group-body">
            ${cards}
          </div>
        </div>
      `;
    }).join('');
  }

  // Load the model catalog from remote then relative fallback
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
    const makes = [...new Set(allModels.map((m) => m.model_id?.make).filter(Boolean))].sort();
    const models = [...new Set(allModels.map((m) => m.vehicle_model || m.model_id?.vehicle_slug).filter(Boolean))].sort();
    const powertrains = [...new Set(allModels.map((m) => m.powertrain_type).filter(Boolean))].sort();

    const makeSelect = document.getElementById('make-filter');
    const modelSelect = document.getElementById('model-filter');
    const ptSelect = document.getElementById('powertrain-filter');

    makes.forEach((make) => makeSelect.add(new Option(make, make)));
    models.forEach((model) => modelSelect.add(new Option(model, model)));
    powertrains.forEach((pt) => ptSelect.add(new Option(pt, pt)));
  }

  // Group by vehicle, then by version, preserving every feature set row in each version
  function groupModels() {
    const groups = {};

    allModels.forEach((model) => {
      const make = model.model_id?.make || 'Unknown';
      const vehicle = model.vehicle_model || model.model_id?.vehicle_slug || 'Unknown';
      const pt = model.powertrain_type || 'Unknown';
      const key = `${make}|${vehicle}|${pt}`.toLowerCase();

      if (!groups[key]) {
        groups[key] = {
          key,
          make,
          vehicle,
          powertrain: pt,
          byVersion: {}
        };
      }

      const versionNum = parseInt(model.model_id?.version, 10) || 0;
      if (!groups[key].byVersion[versionNum]) {
        groups[key].byVersion[versionNum] = [];
      }
      groups[key].byVersion[versionNum].push(model);
    });

    groupedModels = Object.values(groups).map((group) => {
      const versions = Object.entries(group.byVersion)
        .map(([version, featureSets]) => ({
          version: Number(version),
          featureSets
        }))
        .sort((a, b) => b.version - a.version);

      return {
        key: group.key,
        make: group.make,
        vehicle: group.vehicle,
        powertrain: group.powertrain,
        versions
      };
    });
  }

  function getPrimaryModel(group) {
    const latestVersion = group.versions[0];
    if (!latestVersion || latestVersion.featureSets.length === 0) return null;
    return latestVersion.featureSets[0];
  }

  function getSearchText(group) {
    const latestVersion = group.versions[0];
    if (!latestVersion) return '';

    const textParts = [group.make, group.vehicle, group.powertrain];
    latestVersion.featureSets.forEach((model) => {
      textParts.push(model.vehicle_description, ...(model.feature_names || []));
    });

    return textParts.filter(Boolean).join(' ').toLowerCase();
  }

  function renderVersionSelector(group, displayIndex) {
    if (group.versions.length <= 1) {
      return `<span class="version-label">Version ${group.versions[0]?.version ?? 'Unknown'}</span>`;
    }

    const options = group.versions.map((versionGroup, i) => {
      const label = i === 0 ? `Version ${versionGroup.version} (Latest)` : `Version ${versionGroup.version}`;
      return `<option value="${i}">${label}</option>`;
    }).join('');

    return `
      <div class="version-select-wrapper">
        <span style="font-size: 0.85rem; font-weight: 500;">Previous Versions:</span>
        <select class="version-select" onchange="updateCardVersion(this, ${displayIndex})">
          ${options}
        </select>
      </div>
    `;
  }

  function renderCardBody(group, selectedVersionIdx) {
    const versionGroup = group.versions[selectedVersionIdx] || group.versions[0];
    const architectureLabels = [...new Set(versionGroup.featureSets.map((model) => formatArchitectureTag(model.architecture_tag)))];

    return {
      yearText: getVersionYearDisplay(versionGroup),
      architectureText: architectureLabels.length === 1 ? architectureLabels[0] : 'Multiple Architectures',
      featureSetsHtml: renderFeatureSets(versionGroup)
    };
  }

  function updateCardDom(card, group, selectedVersionIdx) {
    const { yearText, architectureText, featureSetsHtml } = renderCardBody(group, selectedVersionIdx);
    card.querySelector('.year-display').innerText = yearText;
    card.querySelector('.architecture-display').innerText = architectureText;
    card.querySelector('.feature-sets-container').innerHTML = featureSetsHtml;
  }

  // Perform search and filtering on grouped list
  function handleSearch() {
    const query = document.getElementById('search-input').value.toLowerCase();
    const make = document.getElementById('make-filter').value;
    const modelStr = document.getElementById('model-filter').value;
    const pt = document.getElementById('powertrain-filter').value;
    const yearMin = parseInt(document.getElementById('year-min').value, 10);
    const yearMax = parseInt(document.getElementById('year-max').value, 10);

    const filtered = groupedModels.filter((group) => {
      const latest = getPrimaryModel(group);
      if (!latest) return false;

      if (make && group.make !== make) return false;
      if (modelStr && group.vehicle !== modelStr) return false;
      if (pt && group.powertrain !== pt) return false;

      const latestYearRange = getVersionYearRange(group.versions[0]);
      if (!isNaN(yearMin) && latestYearRange.max < yearMin) return false;
      if (!isNaN(yearMax) && latestYearRange.min > yearMax) return false;

      if (query) {
        const matchText = getSearchText(group);
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
    currentRenderedGroups = results;

    if (results.length === 0) {
      container.innerHTML = '<div id="status-message">No models match your search criteria.</div>';
      return;
    }

    results.forEach((group, groupIndex) => {
      const latest = getPrimaryModel(group);
      if (!latest) return;

      const card = document.createElement('div');
      card.className = 'result-card';

      const title = `${formatTitle(group.make)} ${formatTitle(group.vehicle)}`;
      const subtitle = latest.vehicle_description
        ? `<div style="font-size: 0.9rem; color: var(--pst-color-text-muted, #555); margin-bottom: 12px;">${latest.vehicle_description}</div>`
        : '';
      const { yearText, architectureText, featureSetsHtml } = renderCardBody(group, 0);
      const versionSelector = renderVersionSelector(group, groupIndex);

      card.innerHTML = `
        <div class="result-header">
          <div>
            <h2 class="result-title">${title}</h2>
            <div style="font-size: 0.85rem; color: #0f6cbd; font-weight: bold; margin-top: 4px; text-transform: uppercase;">
              ${group.powertrain || 'Unknown Powertrain'}
            </div>
            <div style="font-size: 0.85rem; color: var(--pst-color-text-muted, #555); margin-top: 4px;">
              <strong>Architecture:</strong> <span class="architecture-display">${architectureText}</span>
            </div>
          </div>
          <span class="meta-tag"><strong>Year:</strong> <span class="year-display">${yearText}</span></span>
        </div>

        ${subtitle}

        <div class="feature-sets-container">
          ${featureSetsHtml}
        </div>

        <div class="actions-row">
          ${versionSelector}
        </div>
      `;

      container.appendChild(card);
    });
  }

  // Update dynamic properties when user toggles a version
  window.updateCardVersion = function(selectElement, renderedGroupIndex) {
    const versionIndex = parseInt(selectElement.value, 10) || 0;
    const group = currentRenderedGroups[renderedGroupIndex];
    const card = selectElement.closest('.result-card');

    if (!group || !card) return;
    updateCardDom(card, group, versionIndex);
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
