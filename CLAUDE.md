# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Quick Reference

### Common Commands

**Development server:**
```bash
npm run dev          # Start with auto-reload
npm start            # Production mode
```

**Deployment:**
```bash
git push origin main           # Deploy to Vercel (auto-deploy from main branch)
vercel                         # Manual Vercel deployment
gcloud app deploy              # Deploy to Google Cloud Platform
```

**Branch management:**
```bash
git checkout main              # Switch to main branch
git checkout versi5            # Switch to versi5 branch (development)
git merge versi5               # Merge versi5 into current branch
```

**Minify for production (if needed for Canvas deployment):**
```bash
npx html-minifier-terser kode.html --collapse-whitespace --remove-comments --remove-attribute-quotes --minify-css true --minify-js "{\"compress\":{\"dead_code\":true,\"passes\":2},\"mangle\":true}" --output kode-min.html
```

**Check file sizes:**
```bash
# PowerShell
(Get-Item index.html).Length / 1MB
```

### Critical Rules

1. **ALWAYS edit `index.html`** - This is the main application file (formerly kode.html)
2. **API key management** - Settings page allows users to input their own Google AI Studio API key
3. **Test locally before pushing** - Run `npm run dev` to test changes
4. **Push to main triggers deployment** - Vercel auto-deploys from main branch
5. **Use branch versi5 for development** - Merge to main when ready

### File Structure at a Glance

- `index.html` - **Main application file** (~3.5 MB) - Edit this file
- `server.js` - **Node.js backend** - Express server with API proxy (optional, for Canvas compatibility)
- `package.json` - Node.js dependencies and scripts
- `.env` / `.env.example` - Environment configuration (API keys, optional)
- `vercel.json` - Vercel deployment configuration
- `PROMPT_INFOGRAFIS_MARKETING.txt` - 5-page Instagram Story infographic prompts (9:16) for 70 Tools
- `PROMPT_INFOGRAFIS_ADS.txt` - 6-page Meta Ads infographic prompts (1:1) for 70 Tools
- `README.md` - Deployment guide and project documentation
- `LOGIN_SYSTEM_GUIDE.md` - Complete login system setup & usage guide
- `backup*.txt` - Feature-specific code backups (legacy)

## Project Overview

**AI Photo Studio** is a single-page application (SPA) that provides **70 AI Tools Complete** for photo and video generation powered by Google Gemini AI. The application is contained in `index.html` (~60,000+ lines as of Jan 2025) and can be deployed to Vercel, Google Cloud Platform, or other hosting providers.

**Deployment Modes:**
1. **Vercel/Static Hosting** (Current): User provides their own Google AI Studio API key via Settings page
2. **Node.js Backend** (Optional): Uses Express server to proxy API calls and hide API key server-side

**Recent Growth**: Application has grown from ~31,304 lines (initial) to ~60,000+ lines with major updates including:
- **70 AI Tools Complete** (Jan 2025): 5 Product Photo + 8 UGC Content + 10 Family & Lifestyle + 17 Creative PRO + 4 Video & Audio + 22 Security & Business
- **Settings Page v.17.0** (Jan 2025): User-provided API key system with localStorage encryption, test connection feature using REST API
- **Foto Touring (Jan 2025)**: Professional motorcycle touring photography with 10 camera angles, custom backgrounds, and DSLR-quality output
- **Affiliate Islami (Jan 2025)**: Islamic-themed product photo generator expanded to 24 themes for year-round marketing
- **Login System (Dec 2024)**: Email-based authentication with anti-piracy protection via Google Apps Script integration
- **Enhanced Features (Dec 2024)**: Thumbnail Pro with custom instructions, Story Update fixes, Foto Produk with flexible count and 36 preset themes

## Critical Context: API Key Management

This application uses **user-provided API keys** stored in localStorage:

1. **Settings Page** (Tab: Pengaturan): Users input their own Google AI Studio API key
2. **localStorage Storage**: API keys are encrypted with XOR obfuscation and stored in browser
3. **Helper Function**: `getApiKey()` retrieves key from localStorage across all features
4. **Test Connection**: Settings page has "Test Koneksi" button that validates API key using direct REST API call to `gemini-2.0-flash-exp`

**API Key Flow:**
```javascript
// Helper functions (lines ~27060-27150)
function encryptApiKey(key) { /* XOR encryption */ }
function decryptApiKey(encrypted) { /* XOR decryption */ }
function getApiKey() { /* Returns decrypted key from localStorage */ }
function saveApiKey(key) { /* Saves encrypted key to localStorage */ }
function clearApiKey() { /* Removes key from localStorage */ }
```

**Settings Test Button** (lines ~70203-70240):
- Uses direct `fetch()` call to Gemini REST API
- Tests against `gemini-2.0-flash-exp` model
- Shows response preview on success
- Better error messages for troubleshooting

**Important:**
- **No hardcoded API keys** - Users must provide their own
- **Warning banner** shows on first load if no API key configured
- **Logout clears API key** - Security measure to prevent unauthorized access

## Architecture Overview

### File Structure

```
index.html             # Main application (~60,000+ lines as of Jan 2025)
├── Styles (lines 1-2100)
├── HTML Structure (lines 2100-9500)
│   ├── Sidebar Navigation
│   ├── Settings Tab (Pengaturan) - API key management
│   ├── Main Content Panels (70+ tabs)
│   └── Mobile Navigation
└── JavaScript (lines 9500-70000+)
    ├── API Key Helpers (~27060-27150)
    ├── Settings Module (~70087-70300)
    ├── Firebase Setup
    ├── Tab Switching Logic
    ├── 70+ Feature Modules (IIFEs)
    └── Event Handlers

server.js              # Optional Node.js backend (Express + API proxy)
package.json           # Node.js dependencies
vercel.json            # Vercel deployment config
.env / .env.example    # Environment variables (optional)

backup*.txt files      # Feature-specific backups and working versions (legacy)

CLAUDE.md              # This file - development guidelines and architecture docs
                       # UPDATED: January 2025 with Settings page and API key management
```

### Navigation System

The app uses a **multi-category tab system**:

**Sidebar Categories**:
1. **Product Photography (5)** - Mockup Studio, POV Tangan, Foto Produk (7-20), Photo Angle Pro, Foto Touring
2. **UGC Content Affiliate GO (8)** - Foto Produk, Affiliate Islami (24 themes), Virtual Try-On, Pose Fashion, Food Selfie, Product Review, Iklan Produk, Professional Headshot
3. **Family & Lifestyle (10)** - Family Photo, Wedding, New Born, Profesi Anak, Umrah/Haji, Pas Foto, Photo Booth, Desain Rumah, Sketsa, Carousel
4. **Creative Tools PRO (17)** - Miniature Me, Halu Idol, Sticker, Hair Generator, Expression Changer, Story Update, Photo Angle, Style Matcher, Thumbnail Pro, Cover Photo, Photo Restoration, Logo Generator, Mascot Generator, Face Swap, Background Remover, Photo Extender, Story Board, Twibon
5. **Video & Audio PRO (4)** - VEO Generator, Opal Image to Video, Voice Over Pro (History), Video Analyzer Pro (3-15 frames)
6. **Security & Business (22)** - Email Login System, Anti-Piracy Token, Session Monitor, Deskripsi Produk SEO, Ide Konten TikTok, Caption Generator, Copywriting Iklan, Email Marketing, Story Instagram, Konten LinkedIn, Thread Twitter, Script YouTube, Rencana Bisnis, HR Assistant, Konten Marketing, Riset Pasar, Analisis Kompetitor, Strategi Pricing, Product Launch Plan, Customer Journey Map, SWOT Analysis, Simulasi Anggaran

Each category contains main tabs (`data-tab="..."`) that show/hide corresponding content panels (`id="content-..."`).

### JavaScript Architecture

**Module Pattern**: Each feature is wrapped in an IIFE (Immediately Invoked Function Expression) for encapsulation:

```javascript
// ==================== FEATURE NAME ====================
(function() {
    const apiKey = ""; // Injected by Canvas
    const elements = document.getElementById('...');

    // Feature logic here

    // Event listeners
})();
```

**Key Feature Locations** (approximate line numbers):
- 23026: Foto Umrah/Haji & Pas Foto Warna
- 23631: Photo Booth (frame-based photo generation)
- 23946: Expression Changer (facial expression modification)
- 24256: Face Swap (face replacement)
- 24553: Background Remover (background removal)
- 24874: Photo Extender (AI outpainting/extension)
- 25225: Story Board (storyboard frame generation)
- 25633: Twibon (photo frame/border generator)
- 26047: Fitting Instan (virtual try-on)
- 26453: Professional Headshot (professional photo generation)

## Gemini API Integration

### SDK Migration (January 2025)

**CRITICAL**: The application has been migrated from deprecated Fetch API to **Google Generative AI SDK (@google/genai@^1.37.0)**.

**Migration Status**: 81 features production-ready
- 78 features migrated to SDK with multi-model fallback
- 3 features enhanced with robust error handling
- All deprecated models replaced with working alternatives

### API Models Used

**✅ WORKING MODELS** (use these):
1. **gemini-2.5-flash-image** - Image generation with aspect ratio support
2. **gemini-2.5-flash-preview** - Text generation, multimodal analysis
3. **gemini-3-flash-preview** - Alternative text generation model (fallback)
4. **gemini-2.5-flash-preview-tts** - Text-to-speech (Voice Over)

**❌ DEPRECATED MODELS** (do NOT use):
- `gemini-2.5-flash-preview-05-20` - Removed by Google (404 error)
- `gemini-2.0-flash-exp` - Unavailable (403 error)

### SDK Pattern (NEW - Use This)

**Modern SDK approach with multi-model fallback**:

```javascript
// Image Generation with Fallback
const { GoogleGenAI } = await import('@google/genai');
const ai = new GoogleGenAI({ apiKey: process.env.API_KEY || '' });

const modelFallbacks = [
    'gemini-2.5-flash-image',
    'gemini-2.0-flash-exp',
    'gemini-2.5-flash-image'
];

for (let i = 0; i < modelFallbacks.length; i++) {
    const modelName = modelFallbacks[i];
    try {
        const result = await ai.models.generateContent({
            model: modelName,
            contents: {
                parts: [
                    { text: prompt },
                    { inlineData: { mimeType: 'image/jpeg', data: base64 } }
                ]
            },
            config: {
                responseModalities: ['IMAGE'],
                imageConfig: { aspectRatio: '3:4' }
            }
        });

        const imageData = result?.candidates?.[0]?.content?.parts?.find(p => p.inlineData)?.inlineData?.data;
        if (imageData) {
            console.log(`✅ Success with ${modelName}`);
            return `data:image/png;base64,${imageData}`;
        }
    } catch (error) {
        console.error(`❌ Error with ${modelName}:`, error.message);
        if (i < modelFallbacks.length - 1) {
            await new Promise(resolve => setTimeout(resolve, i === 0 ? 500 : 1000));
        } else {
            throw error;
        }
    }
}
```

**Text Generation with Fallback**:

```javascript
const modelFallbacks = [
    'gemini-2.5-flash-preview',
    'gemini-3-flash-preview',
    'gemini-2.5-flash-preview'
];

for (let i = 0; i < modelFallbacks.length; i++) {
    const modelName = modelFallbacks[i];
    try {
        const result = await ai.models.generateContent({
            model: modelName,
            contents: { parts: [{ text: prompt }] },
            config: { temperature: 0.8, maxOutputTokens: 2048 }
        });

        const text = result?.candidates?.[0]?.content?.parts?.[0]?.text;
        if (text) {
            console.log(`✅ Success with ${modelName}`);
            return text;
        }
    } catch (error) {
        if (i < modelFallbacks.length - 1) {
            const delay = 1000 * Math.pow(2, i); // Exponential backoff: 1s, 2s, 4s
            await new Promise(resolve => setTimeout(resolve, delay));
        } else {
            throw error;
        }
    }
}
```

### Legacy Fetch API Pattern (Deprecated)

**⚠️ DO NOT USE** - This pattern is deprecated and should be replaced with SDK:

```javascript
// DEPRECATED - Use SDK pattern above instead
const apiKey = ""; // Empty - injected by Canvas
const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/{model}:generateContent?key=${apiKey}`;

const payload = {
    contents: [{
        parts: [
            { text: "prompt" },
            { inlineData: { mimeType: "image/png", data: base64 } }
        ]
    }],
    generationConfig: {
        responseModalities: ['IMAGE'],
        imageConfig: { aspectRatio: "3:4" }
    }
};

const response = await fetch(apiUrl, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(payload)
});
```

### Image Handling

- Images are converted to base64 before sending to API
- HEIC files are converted to JPEG using `heic2any` library
- Downloads use `window.downloadDataURINew()` with fallback to `window.downloadImage()`
- **Important**: Aspect ratio is specified in `imageConfig: { aspectRatio }`, NOT in text prompt

**HEIC Conversion Pattern** (essential for iPhone compatibility):
```javascript
async function handleImageUpload(e) {
    const file = e.target.files[0];
    if (!file) return;

    let processedFile = file;

    // Convert HEIC to JPEG if needed
    if (file.type === 'image/heic' || file.name.toLowerCase().endsWith('.heic')) {
        try {
            const convertedBlob = await heic2any({
                blob: file,
                toType: 'image/jpeg',
                quality: 0.9
            });
            processedFile = new File([convertedBlob], file.name.replace(/\.heic$/i, '.jpg'), {
                type: 'image/jpeg'
            });
        } catch (err) {
            console.error('HEIC conversion failed:', err);
            showError('Gagal mengkonversi gambar HEIC');
            return;
        }
    }

    // Read the processed file
    const reader = new FileReader();
    reader.onloadend = () => {
        originalImageData = reader.result;
        // Show preview, etc.
    };
    reader.readAsDataURL(processedFile);
}
```

## Mobile Compatibility Requirements

### Critical Mobile Event Handling Pattern

**ALWAYS use this pattern for action buttons** - verified working across ALL tabs including UGC, Family & Lifestyle, Creative Tools, and Logo/Mascot/Poster generators:

```javascript
// ✅ CORRECT - Simple click handler (works on mobile & desktop)
resultsGrid.addEventListener('click', async (e) => {
    e.stopPropagation(); // ONLY stopPropagation, NO preventDefault

    const actionBtn = e.target.closest('[data-action]');
    if (!actionBtn) return;

    const action = actionBtn.dataset.action;

    if (action === 'download') {
        // Use downloadDataURINew for mobile compatibility
        if (window.downloadDataURINew) {
            await window.downloadDataURINew(imageUrl, filename);
        } else if (window.downloadImage) {
            await window.downloadImage(imageUrl, filename);
        }
    }
});

// ❌ WRONG - Touch handlers cause issues
// Don't use touchend/touchstart - they cause page refresh on mobile
```

**Key Rules:**
1. **Use `click` event only** - no separate touch handlers needed
2. **Use `e.stopPropagation()`** - prevents event bubbling
3. **DO NOT use `e.preventDefault()`** on results grids - causes download buttons to refresh page on mobile
4. **EXCEPTION**: Use `e.preventDefault()` ONLY for link-based buttons (`<a href="#">`) like in Miniature Me (line 11445)
5. **Use `data-action` attributes** - not inline onclick handlers
6. **Download priority**: `window.downloadDataURINew()` → `window.downloadImage()`
7. **Remove ALL touch event handlers** (`touchstart`, `touchend`) - they interfere with mobile click events

### Mobile-Specific CSS Requirements

```css
/* Essential mobile touch CSS */
.interactive-element {
    -webkit-tap-highlight-color: transparent;  /* Remove blue highlight */
    user-select: none;                         /* Prevent text selection */
    touch-action: manipulation;                /* Improve touch performance */
}

.interactive-element * {
    pointer-events: none;  /* Child elements don't capture events */
}

/* Scrollable containers on mobile */
.scrollable-output {
    max-h-[70vh] md:max-h-96;     /* Mobile: 70% viewport, Desktop: 384px */
    overflow-y: auto;
    -webkit-overflow-scrolling: touch;  /* iOS smooth scrolling */
}
```

### Mobile Compatibility Philosophy

**CRITICAL PRINCIPLE**: All functionality must be identical on desktop and mobile. The only difference should be responsive UI layout - NEVER remove features for mobile users.

- ✅ **Same features everywhere**: Upload, generate, download, edit - all work on both desktop and mobile
- ✅ **Responsive layouts**: Use Tailwind breakpoints (`sm:`, `md:`, `lg:`) to adapt UI to screen size
- ✅ **Mobile-optimized interactions**: Touch-friendly buttons, proper event handling, no hover-only features
- ❌ **NEVER**: Hide features, reduce functionality, or make mobile a "lite" version

**Example patterns:**
- Multi-column grids: `grid-cols-2 sm:grid-cols-3 md:grid-cols-5` (2→3→5 columns as screen grows)
- Preview containers: Can be visible on both mobile and desktop (flexible based on feature needs)
- Button text: `<span class="hidden sm:inline">Download</span>` (icon-only on mobile, text+icon on desktop)

### Mobile Photo Preview Pattern

For features with photo upload, add preview containers to improve UX (users need visual confirmation). Preview visibility can be:
- **Mobile-only** (using `hidden lg:hidden`): For features where desktop has larger preview area
- **Always visible** (using `hidden` only): For features where preview is useful on all screen sizes

```html
<!-- Mobile-only preview (use when desktop has separate large preview) -->
<div id="feature-preview-container" class="mt-3 hidden lg:hidden">
    <img id="feature-image-preview" src="" alt="Preview"
         class="w-full rounded-lg shadow-lg max-h-48 object-cover">
    <button id="feature-remove-preview"
            class="mt-2 w-full bg-red-600 hover:bg-red-700 text-white text-sm py-2 px-3 rounded-lg">
        <i class="fas fa-times mr-1"></i> Hapus Foto
    </button>
</div>

<!-- OR: Always visible preview (use when preview is useful on all screens) -->
<div id="feature-preview-container" class="mt-3 hidden">
    <img id="feature-image-preview" src="" alt="Preview"
         class="w-full rounded-lg shadow-lg max-h-48 object-cover">
    <button id="feature-remove-preview"
            class="mt-2 w-full bg-red-600 hover:bg-red-700 text-white text-sm py-2 px-3 rounded-lg">
        <i class="fas fa-times mr-1"></i> Hapus Foto
    </button>
</div>
```

JavaScript to show/hide preview:
```javascript
// Show preview when image uploaded
reader.onloadend = () => {
    originalImageData = reader.result;

    // Show preview image (mobile only)
    if (imagePreview && previewContainer) {
        imagePreview.src = reader.result;
        previewContainer.classList.remove('hidden');
    }
};

// Remove preview button handler
removePreviewBtn.addEventListener('click', () => {
    originalImageData = null;
    imageInput.value = '';
    if (previewContainer) previewContainer.classList.add('hidden');
    if (imagePreview) imagePreview.src = '';
});
```

Example implementation: Hair Generator (line 8924-8930 HTML, 18196-18200 JS)

### Tab Initialization Pattern

**CRITICAL for tabs with lazy initialization** (e.g., Voice Over, Hair Generator):

```javascript
// Listen to BOTH desktop and mobile tab buttons
const tabDesktop = document.querySelector('.main-tab-btn[data-tab="feature"]');
const tabMobile = document.querySelector('.mobile-tab-btn[data-tab="feature"]');

const initOnClick = () => {
    if (!window.featureInitialized) {
        initFeature();
        window.featureInitialized = true;
    }
};

if (tabDesktop) tabDesktop.addEventListener('click', initOnClick);
if (tabMobile) tabMobile.addEventListener('click', initOnClick);
```

**IMPORTANT**: Without mobile tab listener, features won't initialize on mobile devices, causing:
- Upload handlers not working
- Options not appearing
- Generate buttons being disabled
- Example: Hair Generator (line 18018-18029) - requires BOTH listeners for photo upload to work on mobile

## Common Development Patterns

### Adding a New Feature Tab

**CRITICAL**: All 5 steps are required for both desktop AND mobile compatibility:

1. **CSS Styles** (~line 1480-2100): Add feature-specific styles (`.upload-box-feature`, `.option-btn-feature`)
   ```css
   .upload-box-feature { /* gradient, borders, hover effects */ }
   .option-btn-feature { /* buttons with ripple animation */ }
   .option-btn-feature.selected { /* gradient background */ }
   ```

2. **Sidebar Navigation** (~line 2990-4100): Add desktop button with `data-tab="new-feature"` in appropriate category
   ```html
   <button data-tab="new-feature" class="main-tab-btn">
       <i class="fas fa-icon"></i>
       <span>Feature Name</span>
       <span class="new-badge">NEW</span>
   </button>
   ```

3. **Content Panel** (~line 3200-9100): Add `<div id="content-new-feature" class="main-content-panel hidden">` with UI
   - Follow 7-step structure (upload, theme, options, aspect ratio, count, generate, results)
   - Include mobile-only preview containers if needed

4. **Mobile Navigation** (~line 29400-30800): Add mobile button with same `data-tab` value
   ```html
   <button data-tab="new-feature" class="mobile-tab-btn">
       <i class="fas fa-icon"></i>
       <span>Feature Name</span>
       <span class="new-badge">NEW</span>
   </button>
   ```

5. **JavaScript IIFE** (~line 23000+): Add feature module with proper event handling
   ```javascript
   // ==================== FEATURE NAME ====================
   (function() {
       const apiKey = "";
       // ... implementation with mobile-friendly event handlers
   })();
   ```

**IMPORTANT**:
- `data-tab` value must be identical in sidebar, mobile nav, and content panel ID
- Mobile nav button must appear in same order as sidebar for consistency
- All download buttons must use the double-download prevention pattern (see Download All Implementation)

### Standard Feature Structure

Most image generation features follow this 7-step pattern:

1. **Upload Photo** - with HEIC support
2. **Select Theme/Style** - multiple options with custom input
3. **Additional Options** - background, style, etc.
4. **Aspect Ratio** - 1:1, 3:4, 4:5, 9:16, 16:9
5. **Number of Variations** - typically 4, 6, or 8 images
6. **Generate Button** - triggers parallel generation
7. **Results Grid** - with preview/download buttons

### Event Delegation Pattern

Modern event delegation is used for action buttons to ensure mobile compatibility:

```javascript
resultsGrid.addEventListener('click', async (e) => {
    e.preventDefault();
    e.stopPropagation();

    const actionBtn = e.target.classList.contains('action-btn') ?
                      e.target : e.target.closest('.action-btn[data-action]');
    if (!actionBtn) return;

    const action = actionBtn.dataset.action;
    const index = parseInt(actionBtn.dataset.index, 10);
    const imageData = generatedImages[index];

    if (action === 'preview') {
        window.showPreviewModal(imageData.url);
    } else if (action === 'download') {
        await window.downloadImage(imageData.url, imageData.filename);
    }
});

// Mobile touch feedback
resultsGrid.addEventListener('touchstart', (e) => {
    const actionBtn = e.target.closest('.action-btn[data-action]');
    if (actionBtn) {
        actionBtn.style.transform = 'scale(0.95)';
        setTimeout(() => { actionBtn.style.transform = ''; }, 100);
    }
}, { passive: true });
```

**Important:** Use `data-action` attributes on buttons instead of inline `onclick` to avoid escaping issues and ensure mobile compatibility.

### CSS Class Naming Convention

Feature-specific CSS classes follow this pattern:
- `.upload-box-{feature}` - Upload areas with hover effects
- `.option-btn-{feature}` - Selection buttons with ripple animation
- `.option-btn-{feature}.selected` - Selected state with gradient background

Each feature uses a unique color scheme:
- Photo Booth: Purple (#9333ea) → Pink (#ec4899)
- Expression Changer: Blue (#3b82f6) → Cyan (#06b6d4)
- Professional Headshot: Indigo (#4f46e5) → Blue (#1e40af)
- Family Photo: Teal (#14b8a6) → Teal (#0d9488)
- Wedding: Rose (#f43f5e) → Pink (#ec4899)

### Standard Action Button Pattern

```javascript
// Store generated images
generatedImages.push({ url: imageUrl, filename: filename });

// Create card with action overlay
card.innerHTML = `
    <img src="${imageUrl}" class="w-full h-full object-cover" alt="...">
    <div class="absolute inset-0 bg-gradient-to-t from-black/80 via-black/40 to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-300 flex items-end justify-center pb-4 gap-2">
        <button data-action="preview" data-index="${index - 1}" class="action-btn bg-white/90 hover:bg-white text-gray-800 px-4 py-2 rounded-full font-semibold shadow-lg transition-all duration-200 hover:scale-105 flex items-center gap-2">
            <i class="fas fa-eye"></i>
            <span class="hidden sm:inline">Preview</span>
        </button>
        <button data-action="download" data-index="${index - 1}" class="action-btn bg-gradient-to-r from-[color]-500 to-[color]-600 ... text-white px-4 py-2 rounded-full ...">
            <i class="fas fa-download"></i>
            <span class="hidden sm:inline">Download</span>
        </button>
    </div>
`;
```

### Download All Implementation

**CRITICAL**: Prevent double downloads with proper error handling:

```javascript
downloadAllBtn.addEventListener('click', async (e) => {
    e.stopPropagation();

    // Prevent multiple clicks
    if (generatedImages.length === 0 || downloadAllBtn.disabled) return;

    const originalHTML = downloadAllBtn.innerHTML;
    downloadAllBtn.disabled = true;

    try {
        for (let i = 0; i < generatedImages.length; i++) {
            const img = generatedImages[i];
            downloadAllBtn.innerHTML = `<i class="fas fa-spinner fa-spin mr-2"></i><span>Downloading ${i + 1}/${generatedImages.length}...</span>`;

            try {
                if (window.downloadDataURINew) {
                    await window.downloadDataURINew(img.url, img.filename);
                } else if (window.downloadImage) {
                    await window.downloadImage(img.url, img.filename);
                }
                await new Promise(resolve => setTimeout(resolve, 500));
            } catch (error) {
                console.error(`Error downloading image ${i + 1}:`, error);
                // Continue with next image even if one fails
            }
        }

        downloadAllBtn.innerHTML = '<i class="fas fa-check mr-2"></i><span>Selesai!</span>';
        setTimeout(() => {
            downloadAllBtn.disabled = false;
            downloadAllBtn.innerHTML = originalHTML;
        }, 2000);
    } catch (error) {
        console.error('Download all error:', error);
        downloadAllBtn.innerHTML = '<i class="fas fa-times mr-2"></i><span>Error</span>';
        setTimeout(() => {
            downloadAllBtn.disabled = false;
            downloadAllBtn.innerHTML = originalHTML;
        }, 2000);
    }
});
```

### Flexible Count Selection Pattern

**NEW STANDARD (Dec 2024)**: Allow users to select the number of variations to generate.

Used in: Foto Produk (7/14/20), Thumbnail Pro (4/6/8), and other features.

**HTML Structure:**
```html
<!-- Count Selection Buttons -->
<div id="feature-count-selection" class="grid grid-cols-3 gap-3">
    <button type="button" data-count="4" class="count-btn bg-gray-50 hover:bg-gray-100 text-gray-700 px-3 py-2.5 rounded-lg border-2 border-gray-300 transition-all text-sm font-medium">
        4 Images
    </button>
    <button type="button" data-count="6" class="count-btn count-selected bg-gradient-to-r from-pink-500 to-rose-500 text-white px-3 py-2.5 rounded-lg border-2 border-pink-500 transition-all text-sm font-bold shadow-md">
        6 Images
    </button>
    <button type="button" data-count="8" class="count-btn bg-gray-50 hover:bg-gray-100 text-gray-700 px-3 py-2.5 rounded-lg border-2 border-gray-300 transition-all text-sm font-medium">
        8 Images
    </button>
</div>

<!-- Dynamic Button Text -->
<button id="feature-generate-btn">
    Generate <span id="feature-count-text">6</span> Images
</button>

<!-- Dynamic Results Header -->
<h3><span id="feature-results-count">6</span> Images Generated!</h3>
```

**JavaScript Implementation:**
```javascript
// Variables
let selectedCount = 6; // Default
const countSelection = document.getElementById('feature-count-selection');
const countText = document.getElementById('feature-count-text');
const resultsCount = document.getElementById('feature-results-count');

// Event handler
if (countSelection) {
    countSelection.addEventListener('click', (e) => {
        const button = e.target.closest('[data-count]');
        if (!button) return;

        // Remove selected state from all buttons
        document.querySelectorAll('.count-btn').forEach(btn => {
            btn.classList.remove('count-selected', 'bg-gradient-to-r', 'from-pink-500', 'to-rose-500', 'text-white', 'border-pink-500', 'font-bold', 'shadow-md');
            btn.classList.add('bg-gray-50', 'hover:bg-gray-100', 'text-gray-700', 'border-gray-300', 'font-medium');
        });

        // Add selected state to clicked button
        button.classList.remove('bg-gray-50', 'hover:bg-gray-100', 'text-gray-700', 'border-gray-300', 'font-medium');
        button.classList.add('count-selected', 'bg-gradient-to-r', 'from-pink-500', 'to-rose-500', 'text-white', 'border-pink-500', 'font-bold', 'shadow-md');

        // Update count
        selectedCount = parseInt(button.dataset.count, 10);
        if (countText) countText.textContent = selectedCount;
    });
}

// Use in generation loop
for (let i = 0; i < selectedCount; i++) {
    loadingText.textContent = `Generating ${i + 1}/${selectedCount}...`;
    const imageUrl = await generateImage(i);

    // Update progress
    const progress = Math.round(((i + 1) / selectedCount) * 100);
    progressBar.style.width = `${progress}%`;
    progressText.textContent = `${i + 1}/${selectedCount} completed`;
}

// Update results count after generation
if (resultsCount) resultsCount.textContent = generatedImages.length;
```

**Key Points:**
- Default count stored in variable (not hardcoded in loops)
- UI dynamically updates: button text, progress, results header
- CSS classes toggle for visual feedback
- Mobile-friendly with `data-count` attributes
- Color scheme can be customized per feature (pink/rose shown, but can use any gradient)

### Parallel Image Generation Pattern

```javascript
async function generateImageSet(prompt) {
    // Create placeholder cards (uses selectedCount)
    for (let i = 1; i <= selectedCount; i++) {
        const card = document.createElement('div');
        card.id = `feature-card-${i}`;
        card.className = 'relative group rounded-xl overflow-hidden bg-gray-100 flex items-center justify-center aspect-[3/4]';
        card.innerHTML = '<div class="animate-spin rounded-full h-10 w-10 border-b-2 border-[color]-600"></div>';
        resultsGrid.appendChild(card);
    }

    // Generate all images in parallel (uses selectedCount)
    const generationPromises = Array.from({ length: selectedCount }, (_, i) =>
        generateSingleImage(i + 1, prompt)
    );
    await Promise.allSettled(generationPromises);
}
```

## UI/UX Design System

### Professional Styling Patterns

**Upload Areas**:
- Gradient backgrounds with hover lift effects
- Dashed borders that become solid on hover
- Multi-layer shadows for depth
- Transform: `translateY(-1px)` on hover

**Buttons**:
- `.option-btn-{feature}`: Base button with 2px border
- `.option-btn-{feature}::before`: Ripple effect pseudo-element
- `.option-btn-{feature}.selected`: Gradient background with glow shadow
- All buttons include `active:scale-95` for tactile feedback

**Numbered Step Badges**:
```html
<div class="flex items-center justify-center w-8 h-8 rounded-full
     bg-gradient-to-br from-[color]-500 to-[color]-600 text-white
     font-bold text-sm shadow-lg">{number}</div>
```

### Mobile-First Considerations

**Touch Events**:
- `-webkit-tap-highlight-color: transparent` to prevent blue highlight
- `user-select: none` on interactive elements
- `pointer-events: none` on child elements (SVG/span) inside buttons
- Passive event listeners for smooth scrolling

**Responsive Breakpoints**:
- Mobile: < 768px (sidebar collapses, mobile nav appears)
- Tablet: 768px - 1024px
- Desktop: > 1024px

## Content Generation Tools (Text-Based)

### Enhanced Prompts for Professional Output

Recent updates (2024) enhanced several text generation tools with comprehensive, structured prompts:

**Deskripsi Produk SEO** (line ~25134):
- 9-section structured output (Hook, Details, Benefits, Usage Tips, Social Proof, Specs, Guarantee, CTA, Hashtags)
- Platform-specific tone (Shopee: professional/trustworthy, TikTok Shop: energetic/Gen Z)
- `maxOutputTokens: 3072` for complete descriptions
- Formatting: plain text compatible, no markdown, emoji usage guidelines

**Ide Konten TikTok** (line ~25299):
- 6-section comprehensive strategy (3 Hook alternatives, Second-by-second breakdown, 5 Audio recommendations with reasoning, 15-20 categorized hashtags, Caption copywriting, Pro tips)
- `maxOutputTokens: 4096` for detailed content plans
- Special parsing with emoji delimiters (1️⃣, 2️⃣, etc.) and line separators (━━━)
- Regex pattern: `/1️⃣\s*HOOK PEMBUKA[^\n]*\n━+\n([\s\S]*?)(?=\n━+\n2️⃣|$)/i`
- Fallback mechanism: if parsing fails, display full content in first section

**Parsing Pattern for Multi-Section Outputs:**
```javascript
// Match sections with emoji numbering and delimiters
const hookMatch = content.match(/1️⃣\s*HOOK PEMBUKA[^\n]*\n━+\n([\s\S]*?)(?=\n━+\n2️⃣|$)/i);
const structureMatch = content.match(/2️⃣\s*STRUKTUR VIDEO[^\n]*\n━+\n([\s\S]*?)(?=\n━+\n3️⃣|$)/i);

// Always implement fallback
if (hookMatch || structureMatch) {
    hookOutput.textContent = hookMatch ? hookMatch[1].trim() : 'Section not found';
} else {
    console.warn('Parsing failed, showing raw content');
    hookOutput.textContent = content; // Display all content
}
```

### Business Tools Output Containers

All business tool tabs (Rencana Bisnis, HR Assistant, Konten Marketing, Riset Pasar, Simulasi Anggaran) use:
```html
<div id="output" class="max-h-[70vh] md:max-h-96 overflow-y-auto"
     style="-webkit-overflow-scrolling: touch;"></div>
```
This ensures full display on mobile (70% viewport height) while maintaining desktop layout (384px max).

## Settings Page & API Key Management (v.17.0)

### Overview (NEW January 2025)

The Settings page (Tab: Pengaturan) allows users to provide their own Google AI Studio API key instead of relying on hardcoded keys or backend proxies.

### Implementation Details

**Location:**
- Sidebar button: Line ~8581 (desktop), ~68722 (mobile)
- Content panel: Lines ~9042-9240
- JavaScript module: Lines ~70087-70300
- Helper functions: Lines ~27060-27150

**Key Components:**

1. **API Key Helper Functions** (lines 27060-27150):
```javascript
function encryptApiKey(key) {
    // XOR encryption with salt for basic obfuscation
    const salt = 'AiPhotoStudio2025GeminiAI';
    // ... encryption logic
    return btoa(encrypted);
}

function decryptApiKey(encrypted) {
    // Reverse XOR decryption
    const salt = 'AiPhotoStudio2025GeminiAI';
    // ... decryption logic
    return decrypted;
}

function getApiKey() {
    // Try localStorage first (Vercel)
    const encryptedKey = localStorage.getItem("gemini_api_key");
    if (encryptedKey) {
        return decryptApiKey(encryptedKey);
    }
    // Fallback to environment variable (Canvas/Backend)
    if (typeof process !== 'undefined' && process.env && process.env.API_KEY) {
        return process.env.API_KEY;
    }
    return '';
}

function saveApiKey(key) {
    const encrypted = encryptApiKey(key);
    localStorage.setItem("gemini_api_key", encrypted);
    localStorage.setItem("gemini_api_key_timestamp", Date.now().toString());
    return true;
}

function clearApiKey() {
    localStorage.removeItem("gemini_api_key");
    localStorage.removeItem("gemini_api_key_timestamp");
    localStorage.removeItem("gemini_api_key_valid");
}
```

2. **Test Connection Feature** (lines 70203-70240):
```javascript
async function testConnection() {
    const key = apiKeyInput.value.trim();
    if (!key) {
        showError('Please enter an API key');
        return;
    }

    // Show loading state
    testBtn.disabled = true;
    testBtn.innerHTML = '<i class="fas fa-spinner fa-spin mr-2"></i>Testing...';

    try {
        // Direct REST API call (more reliable than SDK import)
        const testUrl = 'https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash-exp:generateContent';

        const response = await fetch(`${testUrl}?key=${key}`, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({
                contents: [{
                    parts: [{ text: 'Hello, respond with OK if you receive this.' }]
                }]
            })
        });

        if (!response.ok) {
            const errorData = await response.json().catch(() => ({}));
            throw new Error(errorData.error?.message || `HTTP ${response.status}`);
        }

        const data = await response.json();
        const responseText = data?.candidates?.[0]?.content?.parts?.[0]?.text || '';

        showSuccess(`Connection successful! Response: ${responseText.substring(0, 50)}...`);
    } catch (error) {
        showError(`Connection failed: ${error.message}`);
    } finally {
        testBtn.disabled = false;
        testBtn.innerHTML = '<i class="fas fa-wifi mr-2"></i>Test Koneksi';
    }
}
```

**Why Direct REST API Instead of SDK Import:**
- SDK import from `@google/genai` was unreliable in browser
- User reported test button showing errors even when API key was valid
- Direct `fetch()` to REST endpoint is more stable
- Shows actual API response for better validation
- Better error messages for troubleshooting

3. **Warning Banner** (lines 69305-69355):
```javascript
window.addEventListener('DOMContentLoaded', () => {
    setTimeout(() => {
        if (!isApiKeyConfigured()) {
            showApiKeyBanner();
        }
    }, 1000);
});

function showApiKeyBanner() {
    // Creates floating banner at top of page
    // "Atur Sekarang" button opens Settings tab
    // Auto-hides after 10 seconds or on close
}
```

4. **Logout Integration** (line 69284-69292):
```javascript
function logout() {
    // Clear login data
    localStorage.removeItem("affiliatego_email");
    localStorage.removeItem("affiliatego_token");
    localStorage.removeItem("affiliatego_name");

    // Clear API key (NEW in v.17.0)
    clearApiKey();

    location.reload();
}
```

### Security Considerations

**XOR Encryption:**
- Not cryptographically secure
- Simple obfuscation to prevent casual inspection
- Keys stored in browser localStorage are client-side only
- Suitable for this use case (user's own key, their own risk)

**Best Practices:**
- Users should never share their API key
- API key only sent to Google's Gemini API endpoints
- No server-side storage of keys (in static deployment mode)
- Logout clears keys to prevent unauthorized access

### User Flow

1. **First Visit:**
   - Warning banner appears: "API Key Belum Diatur"
   - User clicks "Atur Sekarang"
   - Opens Settings tab

2. **Setup API Key:**
   - User gets key from [Google AI Studio](https://aistudio.google.com/app/apikey)
   - Pastes key in input field
   - Clicks "Test Koneksi" to validate
   - Clicks "Simpan API Key" to save

3. **Using Features:**
   - All 70 features call `getApiKey()` to get stored key
   - Key automatically included in all Gemini API requests
   - No need to re-enter key on each session

4. **Logout:**
   - User clicks logout
   - API key cleared from localStorage
   - Must re-enter key on next login

### Troubleshooting

**Test button shows error but features work:**
- This was the exact issue reported by user in v.16.x
- Fixed in v.17.0 by changing from SDK import to direct REST API
- If still failing, check:
  - API key is valid (copy-paste carefully)
  - No extra spaces in key
  - Try test button again after saving

**Features not generating images:**
- Check Settings page status card shows "API key is configured"
- Try clicking "Test Koneksi" to validate key
- Clear API key and re-enter if corrupted
- Check browser console for specific error messages

**API key not persisting:**
- Check browser allows localStorage (not in private/incognito mode)
- Try clearing browser cache and re-entering key
- Check localStorage in DevTools: `localStorage.getItem("gemini_api_key")`

---

## Recent Features & Updates (January 2025)

### Membuat Model (line ~9115-9350, 64133-64635)
**NEW Jan 2025**: AI Model Photo Generator for creating consistent model photos
- **Purpose**: Generate high-quality model photos that can be used across all features requiring model input
- **Location**: Below Beranda in sidebar and mobile navigation
- **Upload**: Face photo with HEIC support and drag & drop
- **Options**:
  - Gender: Pria (Male) / Wanita (Female)
  - Type: Full Body, Half Body, Portrait/Close-up
  - Outfit: Optional custom description or default casual wear
  - Aspect Ratio: 1:1, 3:4 (default), 9:16, 16:9
  - Count: 2, 4 (default), or 6 photos
- **Output**: Professional model photos with simple studio background
- **Color Scheme**: Violet-Indigo gradient (#7c3aed → #6366f1)
- **Mobile Features**: Drag & drop upload, auto-scroll to results, touch-friendly controls
- **Use Case**: Users can "clone" their face to create consistent model photos for Virtual Try-On, Product Review, Skincare Review, UGC Content, etc.

### Skincare Review Themes (line ~12042-12105, 43131-43200)
**ENHANCED Jan 2025**: Added 6 themed review styles + custom option
- **Theme Selection UI**: Replaced simple textarea with 7 theme buttons
- **Available Themes**:
  - Professional Commercial: Luxury product photography, high-end brand aesthetic
  - Problem & Solution: Before-after transformation storytelling
  - Honest UGC Review: Authentic user-generated content style
  - The Battle (VS): Product comparison/battle with split-screen layouts
  - Science & Routine: Educational approach with ingredient focus
  - Aesthetic/ASMR: Visually satisfying and relaxing content
  - Custom Theme: User-written textarea for custom styles
- **VS Theme Special**: Supports multiple product uploads for comparison content
- **Theme Descriptions Object**: `skincareThemeDescriptions` contains name, prompt, and structure for each theme
- **Prompt Integration**: Theme-specific style additions in image generation

### Foto Touring (line ~18036-18318, 1061-1252, 30603-31300+)
**NEW Jan 2025**: Professional motorcycle touring photography generator
- **Multi-Upload System**: Up to 5 photos (model/rider, motorcycle, helmet, jacket, accessories)
- **12 Background Countries**: Indonesia, Japan, USA, Europe, Australia, New Zealand, Thailand, Bali, Dubai, Iceland, Vietnam, Scotland
- **Custom Background Upload**: Users can upload their own background images with auto-disable of preset roads
- **8 Mood/Atmosphere Themes**: Golden Hour Sunset, Blue Hour Evening, Sunny Day, Dramatic Storm, Foggy Mystery, Night Ride, Sunrise Adventure, Rainy Road
- **10 Professional Camera Angles** (v.16.5):
  - Side Profile - Classic motorcycle photography
  - Front 3/4 - Dynamic engaging angle
  - Action Shot - Motion and speed capture
  - Low Angle - Heroic powerful perspective
  - High Angle - Bird's eye view
  - Rear View - Road ahead adventure
  - Close-up - Detail focus on rider/bike
  - Wide Landscape - Epic environmental shot
  - Tracking - Panning motion blur effect
  - POV Helmet - First-person perspective
- **6 Aspect Ratios**: 1:1, 16:9, 3:4, 9:16, 4:3, 4:5
- **Flexible Count**: 4, 6, or 8 variations (user-selectable)
- **HEIC Support**: Automatic conversion for all upload points
- **AI Compositing**: Photorealistic DSLR-quality output with professional lighting matching
- **Color Scheme**: Orange-red gradient (#f97316 → #dc2626)
- **Implementation**: Full IIFE module (~660 lines) with parallel generation, custom background logic, HEIC conversion
- **Location**: Product Photography category with NEW badge
- **Mobile Optimization**: File input precision fix, touch-friendly controls

### Affiliate Islami (line ~9183-9807, 1943-2018, 37120-37450)
**UPDATED Jan 2025**: Expanded from 8 to 24 Islamic themes for year-round marketing
- **Model Selection**: Mannequin (product only) OR Model Islami (with blurred face for privacy/modesty)
- **Age Categories** (when Model Islami selected): Dewasa (Adult), Anak-anak (Children), Balita (Toddler), Bayi (Baby)
- **24 Islamic Themes** (16 NEW themes added in v.16.5):
  - **Original 8**: Ramadan, Sholat, Lebaran, Haji/Umroh, Kajian, Sedekah, Berbuka Puasa, Tarawih
  - **NEW in v.16.5**: Sahur, Zakat, Qurban, Muharram, Maulid Nabi, Isra Mi'raj, Nuzulul Quran, Hijrah, Aqiqah, Walimah, Pengajian, Pesantren, Takbiran, Tadarus, Masjid, Dzikir
- **Detailed Theme Descriptions**: Each theme has comprehensive prompts with specific props, atmosphere, and cultural elements
- **Color Scheme**: Emerald green (#10b981, #059669) with Islamic ornamental patterns
- **Aspect Ratios**: 1:1, 16:9, 3:4, 9:16
- **Flexible Count**: 4, 6, or 8 photos (user-selectable)
- **UI Enhancement**: Scrollable theme grid with custom scrollbar for 24 options
- **Results Display**: No scrolling container for better mobile visibility (v.16.5 fix)
- **Prompt Engineering**: Detailed Bahasa Indonesia prompts with theme-specific descriptions, privacy considerations (blurred faces), and age-appropriate model descriptions
- **Location**: UGC Content category after "Foto Produk Affiliate"

### Foto Produk Affiliate / B-Roll (line ~14277, 8956-9097, 22482-22499)
**UPDATED v.16.5**: Enhanced with 36 preset themes + flexible generation
- Upload multiple product images
- Optional model image for human presence
- **Optional custom background upload**
- **36 Preset Theme Options** (NEW in v.16.5):
  - Basic Styles (6): Minimalis, Modern, Klasik, Elegant, Natural, Bold
  - Lifestyle & Environment (6): Outdoor, Indoor Cozy, Studio Professional
  - Premium & Luxury (5): Mewah, Gold & Black, Silver, Crystal
  - Creative & Artistic (5): Artistic, Vintage Retro, Futuristic
  - Color Themes (4): Monochrome, Warm Tones, Cool Blues, Vibrant
  - Seasonal (4): Spring, Summer, Autumn, Winter
  - Special Occasions (3): Wedding, Birthday, Holiday
  - Modern Trends (2): Instagrammable, Pinterest Style
  - Custom Theme (1): User-written textarea
- **Flexible generation count**: 7, 14, or 20 photos (user-selectable)
- **Scrollable theme grid**: max-height with custom scrollbar
- Aspect ratios: 1:1, 16:9, 3:4, 9:16
- AI analyzes product + generates creative shot ideas
- Parallel generation with progress tracking
- **Implementation Note**: Uses `selectedBrollCount` variable and dynamic count selection via `data-count` buttons

### Story Update (line ~17067)
**FIXED Dec 2024**: Resolved generation issues and improved mobile UX
- **Bug Fix**: Removed non-existent `resultsHeader` element reference that caused results not to display
- 10 story variations with realistic lifestyle scenarios
- Economic class selection (5 levels: below minimum wage to old money)
- Theme selection (eating, traveling, work, study, sports, hanging out)
- Photo style: Solo or with friends
- **NEW**: Auto-scroll to results after generation (mobile UX improvement)
- Aspect ratios: 1:1, 16:9, 3:4, 9:16
- **Mobile Fix**: Proper event delegation with `e.stopPropagation()` only
- **Download All**: Improved with progress indicator and error handling

### Thumbnail Generator Pro (line ~18179)
**MAJOR UPDATE Dec 2024**: Comprehensive feature expansion
- 2 modes: Reference-based (upload existing thumbnail) or Text-based (create from scratch)

**Reference Mode Enhancements:**
- Upload thumbnail reference (with HEIC support)
- **NEW**: Custom Instructions textarea (optional) - user dapat mendeskripsikan perubahan yang diinginkan
  - Contoh: "Tambahkan efek glow, gunakan warna biru dan merah, background blur"
  - Langsung diintegrasikan ke AI prompt
- **NEW**: Expression/Vibe selector (8 options, optional):
  - Shocked/Surprised, Excited/Amazed, Serious/Professional, Happy/Cheerful
  - Dramatic/Intense, Mysterious/Intriguing, Energetic/Dynamic, Calm/Peaceful
- **NEW**: Flexible variation count: 4, 6, or 8 thumbnails (user-selectable)
- Platform selection: YouTube, Instagram Feed, Instagram Story, Facebook, Twitter

**Text-Based Mode:**
- Title and subtitle input
- 6 style templates (Tech/Gaming, Business/Finance, Vlog/Lifestyle, Tutorial/Education, Dramatic/Clickbait, Minimal/Modern)
- **NEW**: Expression/Vibe selector (same 8 options)
- **NEW**: Flexible variation count: 4, 6, or 8 thumbnails

**Prompt Improvements:**
- **CHANGED**: All prompts now in Bahasa Indonesia (previously English)
- More detailed platform-specific optimizations (YouTube, Instagram, Story, Facebook, Twitter)
- Focus on clickability and CTR (Click-Through Rate)
- Custom instructions integration in Reference Mode
- Expression descriptions in Indonesian for better AI understanding
- Examples:
  ```javascript
  const expressionDescriptions = {
      shocked: 'dengan ekspresi shocked/surprised, mata terbuka lebar, mulut terbuka, gesture kaget',
      excited: 'dengan ekspresi excited/amazed, sangat antusias, energi tinggi, gesture bersemangat',
      // ... etc
  };
  ```

**UI Updates:**
- Dynamic button text: "Generate [4/6/8] Thumbnails"
- Progress tracking: "3/8 completed"
- Results header: "[8] Thumbnails Generated!"
- Count selector with visual feedback (gradient background on selected)

**Implementation Pattern:**
```javascript
// Count selection handler
countSelection.addEventListener('click', (e) => {
    const button = e.target.closest('[data-count]');
    if (button) {
        // Toggle selected state
        selectedCount = parseInt(button.dataset.count, 10);
        if (countText) countText.textContent = selectedCount;
    }
});

// Generation loop uses selectedCount
for (let i = 0; i < selectedCount; i++) {
    const imageUrl = await generateThumbnail(i);
    // Update progress: ${i + 1}/${selectedCount}
}
```

### Photo Booth (line 23631)
- 8 frame themes (Classic, Polaroid, Party, Wedding, Birthday, Retro 80s, Modern, Custom)
- 4-8 photo variations
- Custom theme input
- Aspect ratios: 1:1, 3:4, 9:16, 16:9

### Expression Changer (line 23946)
- 12 expression types (Happy, Gentle Smile, Serious, Surprised, Sad, Angry, Laughing, Confident, Thinking, Excited, Neutral, Custom)
- 3 intensity levels (Subtle, Moderate, Strong)
- 3-6 variations
- Identity preservation (only expression changes)

### Face Swap (line 24256)
- Upload target photo and source face
- AI-powered face replacement
- Maintains target photo context and lighting
- HEIC support for iPhone users

### Background Remover (line 24553)
- AI background removal
- Optional custom background color/image
- Transparent background support
- Download as PNG

### Photo Extender (line 24874)
- 7 direction options (all, horizontal, vertical, left, right, top, bottom)
- 4 aspect ratio targets (1:1, 16:9, 9:16, 4:3)
- AI-powered outpainting/extension
- Generates 4-8 variations

### Story Board (line 25225)
- 8 story types (Social Media, Video Content, Film Scene, Tutorial, Marketing, Product Demo, Event, Animation)
- 3 frame counts (4, 6, 8)
- 6 visual styles (Sketch, Realistic, Cartoon, Minimalist, Cinematic, Comic)
- 2-step AI process: text generation for scene descriptions → image generation

### Twibon Frame Generator (line 25633)
- 8 frame categories (Independence, Holiday, Birthday, Wedding, Graduation, Campaign, Corporate, Custom)
- AI-generated frames or custom frame upload
- 4 style options (Elegant, Modern, Minimalist, Colorful)
- HTML5 Canvas for photo + frame combination
- 4 aspect ratios (1:1, 4:5, 9:16, 16:9)

### Fitting Instan - Virtual Try-On (line 26047)
- 8 product categories (Upper-body, Lower-body, Dress, Outerwear, Shoes, Accessories, Full Outfit, Custom)
- 3 fit types (Slim, Regular, Loose)
- 3 realistic levels (Moderate, High, Artistic)
- AI virtual try-on for e-commerce
- Generates 2, 4, or 6 variations

### Professional Headshot (line 26453)
- 12 professional themes (LinkedIn, Executive, Corporate, Creative, Tech, Medical, Legal, Entrepreneur, Academic, Smart Casual, Consultant, Custom)
- 6 background options (Studio White, Gray, Navy, Gradient, Office, Outdoor)
- 4 attire styles (Suit, Blazer, Shirt, Industry-specific)
- 3 photography styles (Classic, Modern, Cinematic)
- 4 aspect ratios (1:1, 3:4, 4:5, 9:16)
- 4-8 professional headshot variations
- Perfect for LinkedIn, CV, business profiles

### Before-After Generator (line ~38300-39500)
**FIXED Jan 2025**: Critical bug fixes for upload and API compatibility
- **Upload Bug Fix**: MODE 1 (Upload & Transform) had double-click issue where users needed to select photo twice
  - Root cause: HTML label `for="ba-before-upload"` + JavaScript click handler created double trigger
  - **Solution**: Removed redundant JavaScript click handler (line 38954-38981), kept only drag & drop
- **API 403 Error Fix**: Changed from unavailable `gemini-2.0-flash-exp` to working `gemini-2.5-flash-image-preview` model (lines 39085, 39247)
- 2 modes: Upload & Transform (before photo → AI transformation) and Text to Image (text prompt → before/after pair)
- Aspect ratios: 1:1, 16:9, 3:4, 9:16
- Color scheme: Purple gradient (#7c3aed → #9333ea)

### POV Mirror Selfie (line ~21000-36300)
**ENHANCED Jan 2025**: Added custom input options for mirror type and room style
- **Custom Mirror Type**: Users can now specify custom mirror descriptions beyond presets (lines 21053-21063)
- **Custom Room Style**: Users can describe custom room aesthetics (lines 21097-21107)
- **Toggle Pattern**: Show/hide custom textarea when "Custom" option selected (lines 36127-36154)
- **Conditional Prompt Building**: Uses custom input if provided, falls back to preset descriptions (lines 36253-36271)
- 8 mirror types + Custom: Full-length, Bathroom, Vanity, Standing, Round, Vintage, LED, Triptych
- 8 room styles + Custom: Bedroom, Bathroom, Walk-in Closet, Dressing Room, Gym, Studio, Boutique, Hotel Suite
- Mobile & desktop compatible with proper event handling

### Image to Prompt Analyzer (line ~18600-38700)
**ENHANCED Jan 2025**: Added JSON output format option
- **Step 5: Output Format** (lines 18673-18695): Text (default) or JSON format selection
- **JSON Structure** includes:
  - Basic: prompt, short_prompt, main_subject, style
  - Composition: framing, angle, perspective
  - Lighting: type, mood, direction
  - Colors: palette, tone, contrast
  - Technical: quality, detail_level, sharpness
  - Elements, tags, recommended_models arrays
  - Conditional photography_details or art_details based on format
- **buildJSONPrompt()** function (lines 38493-38651): Comprehensive JSON schema generation
- **Bilingual support**: Indonesian and English JSON prompts
- Use case: Developer/API integration, structured data export

### Food Selfie (line ~23676)
**MAJOR UPDATE Dec 2024**: Expanded theme library and custom theme support
- **43 Total Themes** organized into categories:
  - **11 Professional Classics**: Fine Dining, Modern Cafe, Street Food, Home Kitchen, Outdoor Picnic, Studio Clean, Rustic Vintage, Asian Style, Mediterranean, Tropical Beach, Hotel Luxury
  - **12 Professional Specialty**: Dark Moody, Flat Lay, Industrial Loft, Garden Fresh, Marble & Gold, Bright & Airy, Dessert Bar, Farm to Table, Japanese Zen, Coffee Shop, Food Truck, Bakery Display
  - **16 Creative & Unique**: Neon Cyberpunk, Retro Film, Magazine Editorial, Bokeh Dreamy, B&W Artistic, Pastel Dreamy, Golden Hour, Rainy Day, Fireplace Cozy, Smoke & Steam, Mirror Reflection, Color Pop, Icy Frozen, Summer Sunset, Geometric Modern, Underwater
  - **2 POV Perspectives**: POV Satu Tangan (one hand), POV 2 Tangan (two hands)
  - **1 Custom Theme**: User-written textarea input for unlimited custom themes
- **Optional Features**:
  - Custom background upload (with HEIC support)
  - Style reference image upload
  - Photorealistic compositing with 7-point requirements (lighting, perspective, shadows, color grading, depth of field, surface integration, ambient occlusion)
- **Aspect Ratios**: 1:1 (Instagram), 9:16 (Stories), 16:9 (YouTube), 3:4 (Portrait)
- **Flexible Count**: 4, 6, or 8 images (user-selectable)
- **Custom Theme Pattern**: Show/hide textarea when "Custom Theme" selected
  ```javascript
  // Show/hide custom theme input based on selection
  if (selectedTheme === 'custom') {
      customThemeContainer.classList.remove('hidden');
  } else {
      customThemeContainer.classList.add('hidden');
  }

  // Use custom input value or fallback
  let finalTheme = selectedTheme;
  if (selectedTheme === 'custom' && customThemeInput.value.trim()) {
      finalTheme = customThemeInput.value.trim();
  } else if (selectedTheme === 'custom') {
      finalTheme = 'Professional food photography';
  }
  ```

## Important Constraints

### Canvas Environment Limitations

1. **No iframe embedding** for some external services - use external link buttons instead
2. **API key auto-injection** - never hardcode keys
3. **Single file** - all code must be in kode.html

### Image Generation Best Practices

1. **Aspect Ratios**: Always specified in `imageConfig: { aspectRatio: "3:4" }`, NOT in prompt
2. **Model Support**: Not all models support all modalities (check Gemini docs)
3. **Response Structure**: Always use safe access: `result?.candidates?.[0]?.content?.parts`
4. **Parallel Generation**: Use `Promise.allSettled()` instead of `Promise.all()` to handle partial failures
5. **Identity Preservation**: Always include "Keep person's identity EXACTLY the same" in prompts
6. **Prompt Language** (NEW Dec 2024):
   - **Use Bahasa Indonesia for better results** - Indonesian prompts produce more contextually appropriate outputs
   - English prompts can result in generic or less culturally relevant images
   - Example: Thumbnail Pro switched from English to Indonesian prompts with significantly improved results
   - For Indonesian users/content, always write prompts in Bahasa Indonesia
   - Platform-specific terms can stay in English (e.g., "YouTube", "Instagram Story")
   ```javascript
   // ✅ GOOD - Indonesian prompt
   const prompt = `Buat thumbnail profesional yang eye-catching untuk platform YouTube.
   - Teks BESAR, BOLD, dan mudah dibaca
   - Warna kontras tinggi untuk visibilitas maksimal
   - Gunakan efek visual modern (glow, shadows, gradients)`;

   // ❌ AVOID - English prompt for Indonesian content
   const prompt = `Create a professional eye-catching thumbnail for YouTube.
   - Large, bold, readable text
   - High contrast colors for maximum visibility`;
   ```

## Debugging Tips

### Common Issues

1. **Results not appearing after generation** (e.g., Story Update bug):
   - Check for non-existent element references (e.g., `getElementById('non-existent-id')`)
   - Use browser console to check for JavaScript errors
   - Verify all DOM element variables are not `null` before calling methods on them
   - Remove references to elements that don't exist in HTML
   - Example fix: Story Update had `resultsHeader` variable referencing non-existent element
   - **Solution**: Remove unused element references or add the missing HTML elements

2. **Button not working in mobile**:
   - Check if using `e.preventDefault()` on results grid - remove it, use only `e.stopPropagation()`
   - Exception: Keep `e.preventDefault()` for link buttons (`<a href="#">`)
   - Verify tab initialization listens to BOTH `.main-tab-btn` and `.mobile-tab-btn`
   - Ensure `data-action` attributes are used, not inline onclick
   - Add `-webkit-tap-highlight-color: transparent` CSS
   - Remove ALL touch event handlers (`touchstart`, `touchend`)
   - Add `touch-action: manipulation` to `.action-btn` CSS
   - Add `pointer-events: none` to all button children (`.action-btn i` and `.action-btn *`)

3. **Download button refreshes page or downloads multiple times on mobile**:
   - Remove `e.preventDefault()` from click handler (unless using `<a>` tags)
   - Use `window.downloadDataURINew()` as primary method, then `downloadImage()` as fallback
   - Remove any touchend/touchstart handlers
   - **CRITICAL for preventing double downloads**:
     - Add `isDownloading` flag to prevent concurrent downloads
     - Check `actionBtn.disabled` before processing
     - Wrap download in try-catch with visual feedback
     - Use `e.stopPropagation()` on Download All buttons
     - Check `downloadAllBtn.disabled` to prevent multiple clicks
   - Example fixes: Fitting Instan (26326-26418), Photo Extender (25182-25222), Story Board (25590-25630)

4. **Photo upload not saving on mobile**:
   - Verify tab has BOTH desktop and mobile initialization listeners
   - Check HEIC conversion is implemented (iPhone compatibility)
   - Add `accept="image/png, image/jpeg, image/webp, image/heic, .heic"` to input
   - Example fix: Hair Generator (18018-18029)

5. **Photo preview not visible on mobile**:
   - Add mobile-only preview container with `lg:hidden` class
   - Show preview image after upload completes
   - Provide remove/delete button to clear preview
   - Example: Hair Generator (8924-8930, 18196-18200)

6. **Content truncated on mobile**:
   - Use `max-h-[70vh] md:max-h-96` instead of fixed `max-h-96`
   - Add `-webkit-overflow-scrolling: touch` for iOS
   - Ensure `whitespace-pre-wrap` for formatted text

7. **Voice list/options not appearing on mobile**:
   - Verify initialization triggered on mobile tab click
   - Check for `pointer-events: none` on child elements
   - Use event delegation on parent container

8. **Parsing fails for multi-section output**:
   - Implement fallback: display raw content if regex doesn't match
   - Add console.log to debug API response structure
   - Check for emoji characters (1️⃣, 2️⃣) and delimiters (━) in pattern

9. **API 400 errors**: API key not injected (only works in Canvas environment)

10. **Image not showing**: Verify base64 data format and MIME type

11. **File upload requires double-click**:
   - Check for duplicate triggers: HTML label `for` attribute + JavaScript click handler
   - **Solution**: Remove JavaScript click handler, rely on label's native behavior
   - Keep drag & drop handlers separate from click handlers
   - Example fix: Before-After Generator (line 38954-38981)
   - Pattern:
     ```javascript
     // ❌ WRONG - Creates double trigger
     uploadBox.addEventListener('click', () => {
         imageInput.click(); // Label already does this!
     });

     // ✅ CORRECT - Only drag & drop handlers
     uploadBox.addEventListener('dragover', (e) => {
         e.preventDefault();
         // styling changes
     });
     ```

12. **API 403 Forbidden errors**:
   - Model not available or not authorized
   - **Solution**: Use verified working models
   - Image generation: `gemini-2.5-flash-image-preview`
   - Text/analysis: `gemini-2.5-flash-preview-05-20`
   - Text-to-speech: `gemini-2.5-flash-preview-tts`
   - Example fix: Before-After Generator changed from `gemini-2.0-flash-exp` to `gemini-2.5-flash-image-preview`

13. **Copy button not working on mobile**:
   - `navigator.clipboard.writeText()` may fail on non-secure contexts or older browsers
   - **Solution**: Add fallback copy method using `document.execCommand('copy')`
   - Add `pointer-events: none` to button children (icons, spans)
   - Add touch-friendly CSS: `-webkit-tap-highlight-color: transparent`, `touch-action: manipulation`
   - Example fix: Image to Prompt Analyzer (line 40879-40946)
   - Pattern:
     ```javascript
     // Fallback copy function
     function fallbackCopyToClipboard(text) {
         const textArea = document.createElement('textarea');
         textArea.value = text;
         textArea.style.position = 'fixed';
         textArea.style.left = '-9999px';
         document.body.appendChild(textArea);
         textArea.focus();
         textArea.select();
         try {
             const successful = document.execCommand('copy');
             document.body.removeChild(textArea);
             return successful;
         } catch (err) {
             document.body.removeChild(textArea);
             return false;
         }
     }

     // Try modern API first, then fallback
     if (navigator.clipboard && window.isSecureContext) {
         await navigator.clipboard.writeText(text);
     } else {
         fallbackCopyToClipboard(text);
     }
     ```

### Console Logging Pattern

Most features log errors with context:
```javascript
console.error('Error generating image:', error);
console.log('Generated result:', result);
```

## Firebase Integration

Only used for **Family Photo** feature:
- Anonymous authentication
- Firestore for storing family member data
- Firebase imports at line ~8900

## External Dependencies

All loaded via CDN (lines 6-11):
- Tailwind CSS (styling framework)
- Font Awesome 6.5.1 (icons)
- FileSaver.js 2.0.0 (file downloads)
- heic2any 0.0.4 (HEIC to JPEG conversion)
- Google Fonts (Poppins, Inter)

## Backup Files

- `backup1.txt`: Full working version (reference for reverting changes)
- `*.txt` files: Feature-specific code snippets (pasfotohaji, desainrumah, etc.)

## Production Build & Deployment

### File Versions

The codebase maintains multiple versions for different purposes:

```
kode.html          # Development version (3.5 MB as of v.16.7)
                   # - Readable formatting with comments
                   # - For maintenance and feature development
                   # - ALWAYS edit this file, never the minified versions

kode-min.html      # Production version (2.0 MB as of v.16.7)
                   # - Minified for Canvas sharing
                   # - Variable names mangled
                   # - No comments or whitespace
                   # - Generated automatically, DO NOT edit manually
```

### Building for Production

**Command to minify:**
```bash
npx html-minifier-terser kode.html \
  --collapse-whitespace \
  --remove-comments \
  --remove-attribute-quotes \
  --minify-css true \
  --minify-js "{\"compress\":{\"dead_code\":true,\"passes\":2},\"mangle\":true}" \
  --output kode-min.html
```

**Result (Latest v.16.7):**
- File size reduction: ~43% (3.5 MB → 2.0 MB)
- Code protection: Variable mangling, no formatting, unreadable
- Canvas compatible: Within 2 MB sharing limit

**Workflow:**
1. Develop and test changes in `kode.html`
2. Run minification command above
3. Upload `kode-min.html` to Canvas
4. Test all features in Canvas environment
5. Share public link

### Obfuscation Safety - CRITICAL

**⚠️ IMPORTANT: Emoji and Unicode Characters**

The codebase uses Unicode escape sequences instead of direct emoji characters to prevent corruption during obfuscation/minification.

**❌ WRONG - Will corrupt when obfuscated:**
```javascript
const prompt = `🎯 Target: ${audience}`;
const match = content.match(/1️⃣\s*HOOK/i);
```

**✅ CORRECT - Obfuscation-safe:**
```javascript
// Define emoji as Unicode escape sequence
const emojiTarget = '\uD83C\uDFAF'; // 🎯
const emoji1 = '\u0031\uFE0F\u20E3'; // 1️⃣

// Use in template literals
const prompt = `${emojiTarget} Target: ${audience}`;

// Use in regex with RegExp constructor
const match = content.match(new RegExp(emoji1 + '\\s*HOOK', 'i'));
```

**Why this matters:**
- Emoji are multi-byte UTF-8 characters
- Obfuscators can corrupt these characters → SyntaxError
- Unicode escape sequences are ASCII-safe and won't corrupt

**Features using Unicode escapes:**
- TikTok Content Generator (~20 emoji types)
- Deskripsi Produk SEO (~21 emoji types)
- Caption Generator (special characters)
- All error messages and UI text with emoji

**Variable naming to avoid collisions:**
- Prompt template emojis: `tiktokEmoji1`, `tiktokEmoji2`, etc.
- Parsing logic emojis: `emoji1`, `emoji2`, etc.
- Feature-specific emojis: `emojiTarget`, `emojiBox`, etc.

See `OBFUSCATION_FIX.md` for complete documentation of all fixes.

---

## Login System & Authentication

### Overview

**NEW (December 2024)**: The application now includes an email-based login system with anti-piracy protection integrated with Google Apps Script.

### System Architecture

```
User Login → Google Apps Script → Google Sheets Database
     ↓                                      ↓
Token Generated                      Token Stored (Column F)
     ↓                                      ↓
Session Check (every 10s) ← Validation ← Token Match?
```

### Key Components

**1. Login Overlay (Lines 5576-5606)**
- Full-screen gradient background
- Email input with validation
- Loading & error states
- Glassmorphism design

**2. User Info Badge (Lines 5598-5606)**
- Fixed position top-right
- Displays logged-in user name
- Quick logout button

**3. JavaScript Login Logic (Lines 43486-43656)**
- `prosesLogin()` - Handles login process
- `bukaAplikasi()` - Unlocks main application
- `jagaSesi()` - Session monitoring (10-second intervals)
- `logout()` - Clears session and reloads
- Auto-login on page load

### Google Apps Script Integration

**Required Setup:**
1. Google Sheet with columns: Timestamp, Email, Name, Product, Status, Token
2. Google Apps Script with `doGet()` function
3. Deploy as Web App with "Anyone" access
4. Update `SCRIPT_URL` in kode.html (line 43490)

**API Endpoints:**
```javascript
// Login
?action=login&email=xxx&token=yyy
Response: {"status":"SUKSES","nama":"User Name"} or {"status":"GAGAL","message":"..."}

// Session Check
?action=cek&email=xxx&token=yyy
Response: {"status":"VALID"} or {"status":"INVALID"}
```

### Anti-Piracy Features

**Token-Based Security:**
- Each device gets unique token on login
- Token stored in Google Sheet (Column F)
- Only latest token is valid

**Session Monitoring:**
- Validates token every 10 seconds
- Auto-logout if token changes (login on another device)
- Alert notification: "Account used on another device"

**Effect:**
- Prevents account sharing
- Multiple logins = mutual "kick-out" effect
- Discourages piracy

### LocalStorage Keys

```javascript
localStorage.setItem("affiliatego_email", email);
localStorage.setItem("affiliatego_token", token);
localStorage.setItem("affiliatego_name", name);
```

### Setup Instructions

1. **Configure Google Apps Script:**
   - Deploy with "Anyone" access
   - Copy deployment URL

2. **Update kode.html:**
   - Find line 43490
   - Replace `SCRIPT_URL` with your deployment URL

3. **Test:**
   - Open application → Login screen appears
   - Enter registered email → Should unlock app
   - Check Google Sheet → Token should be populated

4. **Verify Anti-Piracy:**
   - Login on Device A
   - Login with same email on Device B
   - Device A should auto-logout within 10 seconds

### Customization

**Change session check interval:**
```javascript
// Line 43573 - Default: 10 seconds
setInterval(jagaSesi, 10000); // Change 10000 to desired milliseconds
```

**Change login screen colors:**
```css
/* Line 5446 - Gradient background */
background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
```

### Troubleshooting

**"Failed to fetch" error:**
- SCRIPT_URL not configured or incorrect
- Google Apps Script not deployed with "Anyone" access
- Check browser console for detailed error

**"Email tidak terdaftar":**
- Email not in Google Sheet Column B
- Status column not "AKTIF" (must be uppercase)

**Auto-logout immediately:**
- Token validation failing
- Check Google Apps Script `doGet()` function
- Verify Sheet column structure

See `LOGIN_SYSTEM_GUIDE.md` for complete setup and troubleshooting guide.

---

## Changelog - January 2025 Updates

### Summary of Major Changes

This documentation was last updated in **January 19, 2025** to reflect v.17.0 updates:

**Version 17.0 - "Settings Page & API Key Management" (January 19, 2025):**
1. **Settings Page (NEW)** - User-provided API key system
   - Tab "Pengaturan" added to sidebar and mobile navigation
   - Users input their own Google AI Studio API key
   - API key encrypted with XOR and stored in localStorage
   - Helper functions: `getApiKey()`, `saveApiKey()`, `clearApiKey()`, `encryptApiKey()`, `decryptApiKey()`
   - Test connection feature validates API key with direct REST API call
   - Warning banner shows on first load if no API key configured
   - Logout clears API key from localStorage
2. **Test Button Fix** (CRITICAL)
   - Changed from unreliable SDK import to direct `fetch()` REST API call
   - Tests against `gemini-2.0-flash-exp` endpoint
   - Shows actual response preview on success
   - Better error handling with specific messages
   - User reported: "cek tes apikey di google studio saat di klik ada error walapun sebenarnya ketika di simpan apikeynya bekerja dengan baik" - Fixed!
3. **Simplified to Google AI Studio Only**
   - Removed multi-provider code (val.ai, atlascloud)
   - Removed 215 lines of complexity
   - Single provider focus: Google AI Studio
   - Static instructions directly in Settings UI
4. **Deployment Updates**
   - Vercel auto-deployment from main branch
   - Branch strategy: versi5 for development → main for production
   - Updated README.md with Node.js backend setup guide
   - Environment variable support (.env)

**Previous updates:**

This documentation was previously updated in **January 11, 2025** to reflect v.16.7 updates:

**Version 16.7 - "Model Creator & Theme Expansion" (January 11, 2025):**
1. **Membuat Model (NEW)** - AI Model Photo Generator for creating consistent model photos
   - Located below Beranda in sidebar and mobile navigation
   - Upload face photo → Generate professional model photos
   - Options: Gender (Pria/Wanita), Type (Full Body/Half Body/Portrait), Outfit, Aspect Ratio, Count
   - Purpose: Create consistent model photos for use across all features (Virtual Try-On, Product Review, etc.)
   - Color scheme: Violet-Indigo gradient (#7c3aed → #6366f1)
   - Mobile-optimized with drag & drop, auto-scroll, touch-friendly controls
2. **Skincare Review (ENHANCED)** - Added 6 themed review styles + custom option
   - New themes: Professional Commercial, Problem & Solution, Honest UGC Review, The Battle (VS), Science & Routine, Aesthetic/ASMR
   - VS theme supports multiple product uploads for comparison content
   - Theme-specific prompts and storyboard structures
   - Custom theme textarea for user-defined styles
3. **Image to Prompt Analyzer (FIXED)** - Copy button now works on mobile
   - Added fallback copy method using `document.execCommand('copy')` for older browsers
   - Fixed pointer-events on button children
   - Added touch-friendly CSS (-webkit-tap-highlight-color, touch-action: manipulation)
4. **Production Build** - Updated minification (kode.html: 3.5 MB → kode-min.html: 2.0 MB, ~43% compression)

**Version 16.6 - "Bug Fixes & Enhancements" (January 9, 2025):**
1. **Before-After Generator (FIXED)** - Resolved double-click upload bug and API 403 error
   - Fixed MODE 1 upload requiring two clicks (removed duplicate JavaScript trigger)
   - Changed API model from unavailable `gemini-2.0-flash-exp` to working `gemini-2.5-flash-image-preview`
2. **POV Mirror Selfie (ENHANCED)** - Added custom input options for mirror type and room style
   - Users can now write custom descriptions for mirror types beyond 8 presets
   - Users can describe custom room aesthetics beyond 8 preset styles
   - Conditional prompt building with fallback to defaults
3. **Image to Prompt Analyzer (ENHANCED)** - Added JSON output format option
   - New Step 5: Output Format selection (Text or JSON)
   - Comprehensive JSON schema with 10+ structured fields
   - Bilingual support for developer/API integration use cases
4. **UI Cleanup** - Removed duplicate Before-After buttons from sidebar (desktop & mobile)
5. **Production Build** - Updated minification (kode.html: 3.19 MB → kode-min.html: 1.77 MB, 44.5% compression)

**Version 16.5 - "Touring & Islamic Expansion" (January 4, 2025):**
1. **Foto Touring (NEW)** - Professional motorcycle touring photography with 10 camera angles, custom background upload, 12 country backdrops, 8 mood themes, DSLR-quality AI compositing
2. **Affiliate Islami (EXPANDED)** - Islamic themes expanded from 8 to 24 for comprehensive year-round marketing coverage (added 16 new themes: Sahur, Zakat, Qurban, Muharram, Maulid, Isra Mi'raj, Nuzulul Quran, Hijrah, Aqiqah, Walimah, Pengajian, Pesantren, Takbiran, Tadarus, Masjid, Dzikir)
3. **Foto Produk Affiliate (ENHANCED)** - Added 36 preset theme options organized in 8 categories with scrollable grid UI
4. **Bug Fix** - Affiliate Islami results display no longer uses scrolling container for better mobile visibility
5. **Production Build** - Updated minification (kode.html: 2.78 MB → kode-min.html: 1.55 MB, 44.3% compression)

**Previous Updates (January 2025):**
1. **70 AI Tools Complete** - Application now has 70 tools across 6 categories (5 Product + 8 UGC + 10 Family & Lifestyle + 17 Creative PRO + 4 Video & Audio PRO + 22 Security & Business)
2. **Marketing Prompts Updated** - PROMPT_INFOGRAFIS_MARKETING.txt and PROMPT_INFOGRAFIS_ADS.txt updated to reflect 70 tools with complete feature breakdowns

**Previous Updates (December 2024):**
1. **Login System** - Email-based authentication with anti-piracy protection (340+ lines added)
2. **Foto Produk Affiliate** - Added optional background upload and flexible count (7/14/20)
3. **Story Update** - Fixed critical bug preventing results display, improved mobile UX
4. **Thumbnail Pro** - MAJOR UPDATE with Custom Instructions, Expression/Vibe selector, flexible count (4/6/8), Indonesian prompts

**New Development Patterns:**
- Flexible Count Selection Pattern (section added with complete implementation guide)
- Bahasa Indonesia prompts for better culturally-relevant results
- Improved mobile event handling patterns
- Enhanced download prevention with progress indicators
- **Production Build & Minification** - Added complete workflow for Canvas deployment
- **Unicode Escape Pattern** - Critical pattern for obfuscation-safe emoji handling

**Bug Fixes:**
- **Before-After Generator** (v.16.6): Fixed double-click upload bug and API 403 error
- **Duplicate UI Elements** (v.16.6): Removed duplicate Before-After buttons from sidebar
- Story Update: Removed non-existent element references causing generation failures
- Mobile download buttons: Standardized prevention of double downloads
- Event delegation patterns refined for better mobile compatibility
- **Obfuscation Errors** - Fixed SyntaxError issues caused by emoji corruption (40+ emoji types converted to Unicode escape sequences)
- **Variable Collisions** - Resolved block-scoped variable redeclaration warnings

**Documentation Improvements:**
- Added detailed examples for all new patterns
- Updated line number references
- Added new debugging tips (13 common issues now documented, including upload double-click and API 403 errors)
- Comprehensive implementation guides for flexible features
- **Production Build & Deployment** section added with minification workflow
- **Obfuscation Safety** guidelines with Unicode escape patterns

**File Statistics (v.16.6):**
- Lines of code: ~31,304 → ~60,500+ (+29,200 lines as of Jan 9, 2025)
- Development file: kode.html (~3.19 MB with login system, 70 tools, all features)
- Production file: kode-min.html (~1.77 MB, 44.5% compression)
- Marketing prompts: PROMPT_INFOGRAFIS_MARKETING.txt (5 pages, 9:16) + PROMPT_INFOGRAFIS_ADS.txt (6 pages, 1:1)
- New features/fixes in v.16.6:
  - Before-After Generator: Bug fixes for upload and API (lines 38954-39500)
  - POV Mirror Selfie: Custom input additions (lines 21053-36300)
  - Image to Prompt Analyzer: JSON format support (lines 18673-38700)
  - Removed 5 lines: Duplicate Before-After buttons
- Previous features in v.16.5:
  - Foto Touring: ~660 lines (CSS + HTML + JavaScript)
  - Affiliate Islami expansion: +16 themes (now 24 total)
  - Foto Produk Affiliate: +36 preset themes with scrollable UI
- Earlier additions:
  - Login system: 340+ lines (CSS + HTML + JavaScript) - Dec 2024
  - Affiliate Islami initial: 640+ lines - Jan 2025
  - Food Selfie expansion: 28 new themes + custom theme - Dec 2024

For detailed change information, see:
- **Before-After Generator (FIXED v.16.6)**: Line ~38300-39500 (section: Recent Features & Updates)
- **POV Mirror Selfie (ENHANCED v.16.6)**: Line ~21000-36300 (section: Recent Features & Updates)
- **Image to Prompt Analyzer (ENHANCED v.16.6)**: Line ~18600-38700 (section: Recent Features & Updates)
- **Foto Touring (NEW v.16.5)**: Line ~18036-18318, 1061-1252, 30603-31300+ (section: Recent Features & Updates)
- **Affiliate Islami (UPDATED v.16.5)**: Line ~9183-9807, 1943-2018, 37120-37450 (section: Recent Features & Updates)
- **Foto Produk Affiliate (UPDATED v.16.5)**: Line ~14277, 8956-9097, 22482-22499 (section: Recent Features & Updates)
- **Story Update**: Line ~17067 (section: Recent Features & Updates)
- **Thumbnail Pro**: Line ~18179 (section: Recent Features & Updates)
- **Food Selfie**: Line ~23676 (section: Recent Features & Updates)
- **Flexible Count Pattern**: Common Development Patterns section
- **Bahasa Indonesia Prompts**: Image Generation Best Practices section
- **Marketing Prompts**: PROMPT_INFOGRAFIS_MARKETING.txt and PROMPT_INFOGRAFIS_ADS.txt (70 tools)
