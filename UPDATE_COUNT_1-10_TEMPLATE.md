# Template Update Count 1-10 untuk Semua Fitur

## Template HTML (Copy-Paste)

### Pattern BEFORE (3 buttons):
```html
<div id="FEATURE-count-selection" class="grid grid-cols-3 gap-3">
    <button type="button" data-count="X" class="count-btn ...">X</button>
    <button type="button" data-count="Y" class="count-btn selected ...">Y</button>
    <button type="button" data-count="Z" class="count-btn ...">Z</button>
</div>
```

### Pattern AFTER (10 buttons):
```html
<div class="flex items-center gap-2 mb-4">
    <div class="w-8 h-8 ...">N</div>
    <h2 class="...">Jumlah [Foto/Images/Variasi]</h2>
    <span class="text-xs text-gray-500 ml-auto">Hemat API Key: Pilih 1-2</span>
</div>
<div id="FEATURE-count-selection" class="grid grid-cols-5 sm:grid-cols-10 gap-2">
    <button type="button" class="count-btn ... text-sm font-semibold" data-count="1">1</button>
    <button type="button" class="count-btn ... text-sm font-semibold" data-count="2">2</button>
    <button type="button" class="count-btn ... text-sm font-semibold" data-count="3">3</button>
    <button type="button" class="count-btn selected ... text-sm font-bold" data-count="4" style="GRADIENT_STYLE">4</button>
    <button type="button" class="count-btn ... text-sm font-semibold" data-count="5">5</button>
    <button type="button" class="count-btn ... text-sm font-semibold" data-count="6">6</button>
    <button type="button" class="count-btn ... text-sm font-semibold" data-count="7">7</button>
    <button type="button" class="count-btn ... text-sm font-semibold" data-count="8">8</button>
    <button type="button" class="count-btn ... text-sm font-semibold" data-count="9">9</button>
    <button type="button" class="count-btn ... text-sm font-semibold" data-count="10">10</button>
</div>
```

## Key Changes:
1. Grid: `grid-cols-3 gap-3` → `grid-cols-5 sm:grid-cols-10 gap-2`
2. Buttons: 3 buttons → 10 buttons
3. Text: Remove "Foto" span, just show number
4. Padding: `p-3` → `p-2.5` or `p-2` (compact)
5. Add hint: "Hemat API Key: Pilih 1-2"
6. Default: Keep at 4 (most balanced)

## CSS Classes Pattern:

### Unselected button:
```html
class="count-btn flex items-center justify-center p-2.5 border-2 border-gray-200 rounded-lg hover:border-[COLOR] transition-all active:scale-95 text-sm font-semibold"
```

### Selected button (keep original gradient):
```html
class="count-btn selected flex items-center justify-center p-2.5 border-2 rounded-lg transition-all active:scale-95 text-sm font-bold"
style="border-color: #HEX; background: linear-gradient(135deg, #COLOR1, #COLOR2); color: white; box-shadow: 0 4px 12px rgba(R, G, B, 0.3);"
```

## List Fitur yang Perlu Update:

### Product Photography (5 features):
1. ✅ Membuat Model - DONE (line 9669)
2. ⏳ Mockup Studio
3. ⏳ POV Tangan
4. ⏳ Foto Produk
5. ⏳ Photo Angle Pro
6. ⏳ Foto Touring

### UGC Content (8 features):
7. ⏳ Foto Produk Affiliate/B-Roll (line 11085) - Currently 7/14/20 → Change to 1-10
8. ⏳ Affiliate Islami (line 11220) - Currently 2/4/6 → Change to 1-10
9. ⏳ Virtual Try-On
10. ⏳ Pose Fashion
11. ⏳ Food Selfie
12. ⏳ Product Review (line 12559) - Currently 4/7 → Change to 1-10
13. ⏳ Iklan Produk
14. ⏳ Professional Headshot

### Family & Lifestyle (10 features):
15. ⏳ Family Photo
16. ⏳ Wedding
17. ⏳ New Born
18. ⏳ Profesi Anak
19. ⏳ Umrah/Haji
20. ⏳ Pas Foto
21. ⏳ Photo Booth
22. ⏳ Desain Rumah
23. ⏳ Sketsa/Karikatur (line 10298) - Currently 4/6/8 → Change to 1-10
24. ⏳ Carousel

### Creative Tools PRO (17 features):
25. ⏳ Miniature Me
26. ⏳ Halu Idol
27. ⏳ Sticker
28. ⏳ Hair Generator
29. ⏳ Expression Changer
30. ⏳ Story Update
31. ⏳ Photo Angle
32. ⏳ Style Matcher
33. ⏳ Thumbnail Pro
34. ⏳ Cover Photo
35. ⏳ Photo Restoration
36. ⏳ Logo Generator
37. ⏳ Mascot Generator
38. ⏳ Face Swap
39. ⏳ Background Remover
40. ⏳ Photo Extender
41. ⏳ Story Board
42. ⏳ Twibon

### Video & Audio (4 features):
43. ⏳ VEO Generator
44. ⏳ Opal Image to Video
45. ⏳ Voice Over (if applicable)
46. ⏳ Video Analyzer (if applicable)

### Beauty Features:
47. ⏳ Beauty Enhancer (line 10739) - Currently 4/6/8 → Change to 1-10
48. ⏳ Skincare Review (line 12371) - Currently 4/6/8 → Change to 1-10

## JavaScript Changes (Usually NO changes needed!)

Most features already use `selectedCount` variable dynamically, so JavaScript should work automatically when HTML is updated.

Only verify:
```javascript
let selectedCount = 4; // Default

// Handler already works for 1-10!
countSelection.addEventListener('click', (e) => {
    const button = e.target.closest('[data-count]');
    if (!button) return;
    selectedCount = parseInt(button.dataset.count, 10);
    // ... rest of code
});

// Generation loop
for (let i = 0; i < selectedCount; i++) {
    // This automatically handles 1-10!
}
```

## Testing Checklist:
- [ ] Grid responsive: 5 cols mobile, 10 cols desktop
- [ ] Buttons clickable on mobile
- [ ] Selected state updates correctly
- [ ] Generate button text updates (e.g., "Generate 7 Photos")
- [ ] API calls execute correct number of times
- [ ] Progress bar shows correct count (e.g., "3/7")
- [ ] Results header shows correct count
