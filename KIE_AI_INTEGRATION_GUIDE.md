# kie.ai API Integration Guide

## Overview

kie.ai has been added as the 4th API provider option for the AI Photo Studio application. It uses a task-based async API architecture with Gemini Flash 2.5 Image model (`google/nano-banana-edit`).

## Current Implementation Status

### ✅ Completed:
1. **Settings UI** - kie.ai provider button added with green theme
2. **Provider Configuration** - Instructions and config for kie.ai in Settings tab
3. **Helper Functions** - Complete API integration functions added to `index.html`:
   - `createKieTask()` - Creates task in kie.ai API
   - `queryKieTaskStatus()` - Queries task status by taskId
   - `pollKieTask()` - Polls task until completion
   - `generateImageWithKie()` - Complete flow: create → poll → get result
4. **Documentation** - README-VERCEL.md updated with kie.ai details

### ⏳ Pending:
- **Feature Integration** - Individual features (70+ tools) need to be updated to support kie.ai
- **Image URL Handling** - kie.ai requires publicly accessible image URLs, not base64 data

## API Architecture

### kie.ai vs Google AI Studio:

| Aspect | Google AI Studio | kie.ai |
|--------|-----------------|--------|
| API Type | Synchronous | Asynchronous (task-based) |
| Request | Single API call | Create task → Poll status |
| Response | Immediate | Polling required |
| Model | gemini-2.5-flash-image | google/nano-banana-edit |
| Image Input | Base64 data | Public URLs only |
| API Key Format | AIzaSy... | kie_... |

## Helper Functions Reference

### 1. `generateImageWithKie(prompt, imageUrls, outputFormat, imageSize)`

Main function for generating images using kie.ai.

**Parameters:**
- `prompt` (string): Text prompt (max 20,000 chars)
- `imageUrls` (array): Array of public image URLs (optional, max 10)
- `outputFormat` (string): 'png' or 'jpeg' (default: 'png')
- `imageSize` (string): Aspect ratio - '1:1', '16:9', '9:16', '3:4', '4:3', etc.

**Returns:** Promise<string> - Base64 data URL of generated image

**Example:**
```javascript
const provider = getApiProvider();

if (provider === 'kie') {
    const imageDataUrl = await generateImageWithKie(
        "A professional product photo with clean white background",
        [], // No input images
        'png',
        '1:1'
    );
    // imageDataUrl is ready to display or download
} else {
    // Use existing Google AI Studio SDK implementation
}
```

### 2. `createKieTask(prompt, imageUrls, outputFormat, imageSize)`

Creates a task in kie.ai API.

**Returns:** Promise<string> - Task ID

### 3. `queryKieTaskStatus(taskId)`

Queries the status of a task.

**Returns:** Promise<Object> - Task data including state ('processing', 'success', 'failed')

### 4. `pollKieTask(taskId, maxAttempts, intervalMs)`

Polls task until completion.

**Parameters:**
- `taskId` (string): Task ID from createTask
- `maxAttempts` (number): Max polling attempts (default: 60)
- `intervalMs` (number): Polling interval (default: 2000ms)

**Returns:** Promise<string> - Image URL from kie.ai

**Timeout:** Default 2 minutes (60 attempts × 2 seconds)

## Integration Pattern for Features

To add kie.ai support to a feature:

### Pattern 1: Text-only Generation (Simple)

```javascript
// In feature's generate function
const provider = getApiProvider();

if (provider === 'kie') {
    // Use kie.ai API
    const prompt = `Generate a professional photo...`;
    const imageDataUrl = await generateImageWithKie(
        prompt,
        [],
        'png',
        selectedAspectRatio // '1:1', '16:9', etc.
    );

    // Display result
    resultImage.src = imageDataUrl;

} else {
    // Use existing Google AI Studio SDK implementation
    const { GoogleGenAI } = await import('@google/genai');
    const ai = new GoogleGenAI({ apiKey: getApiKey() });
    // ... existing code
}
```

### Pattern 2: Image Input Generation (Complex)

**Problem:** kie.ai requires public image URLs, but features use base64 data from user uploads.

**Solution Options:**

#### Option A: Upload to temporary hosting (recommended)
```javascript
// Convert base64 to public URL (needs implementation)
async function uploadToTempStorage(base64Data) {
    // Upload to imgur, cloudinary, or other temp hosting
    // Return public URL
    return publicUrl;
}

if (provider === 'kie') {
    const imageUrl = await uploadToTempStorage(userImageBase64);
    const result = await generateImageWithKie(
        prompt,
        [imageUrl],
        'png',
        aspectRatio
    );
}
```

#### Option B: Skip image input for kie.ai
```javascript
if (provider === 'kie') {
    // Generate without input image, use text description only
    const enhancedPrompt = `${basePrompt}. Style: modern product photography`;
    const result = await generateImageWithKie(enhancedPrompt, [], 'png', aspectRatio);
} else {
    // Use input image with Google AI Studio
}
```

#### Option C: Disable feature for kie.ai
```javascript
if (provider === 'kie') {
    showError('Fitur ini belum support kie.ai. Gunakan Google AI Studio.');
    return;
}
```

## Features Priority for Integration

### High Priority (Text-only, Easy):
1. **Deskripsi Produk SEO** - Text generation only
2. **Ide Konten TikTok** - Text generation only
3. **Caption Generator** - Text generation only
4. **Logo Generator** - Text-to-image, no input images
5. **Mascot Generator** - Text-to-image, no input images

### Medium Priority (May need adaptation):
6. **Thumbnail Pro** - Can work with text-based mode
7. **Sticker** - Text-to-image, simple prompts
8. **Story Update** - Requires face photo input (Option B/C)

### Low Priority (Complex image manipulation):
9. **Face Swap** - Requires 2 input images (Option C)
10. **Background Remover** - Requires input image (Option C)
11. **Virtual Try-On** - Requires model + product images (Option C)
12. **Fitting Instan** - Requires model + product images (Option C)

## Error Handling

```javascript
try {
    const result = await generateImageWithKie(prompt, [], 'png', '1:1');
} catch (error) {
    if (error.message.includes('timeout')) {
        showError('Generation timeout. Coba lagi atau kurangi kompleksitas prompt.');
    } else if (error.message.includes('API error: 401')) {
        showError('API key tidak valid. Periksa pengaturan.');
    } else if (error.message.includes('API error: 429')) {
        showError('Quota habis. Tunggu beberapa saat.');
    } else {
        showError(`Error: ${error.message}`);
    }
}
```

## Testing Checklist

When integrating kie.ai to a feature:

- [ ] Feature detects provider using `getApiProvider()`
- [ ] Uses `generateImageWithKie()` when provider is 'kie'
- [ ] Aspect ratio correctly mapped to kie.ai format
- [ ] Loading states show polling progress
- [ ] Error handling covers timeout and API errors
- [ ] Results display correctly from base64 data URL
- [ ] Download button works with kie.ai generated images
- [ ] Multiple image generation works in sequence

## Limitations & Known Issues

1. **Image URLs Only**: kie.ai cannot accept base64 image data directly
2. **Async Processing**: Slower than Google AI Studio due to polling
3. **Timeout**: Default 2-minute timeout may need adjustment for complex prompts
4. **Public URLs**: Input images must be publicly accessible
5. **Model Differences**: google/nano-banana-edit may produce different results than gemini-2.5-flash-image

## Configuration

**Base URL:** `https://api.kie.ai`
**Model:** `google/nano-banana-edit`
**Default Timeout:** 120 seconds (60 × 2s polling)
**Max Prompt Length:** 20,000 characters
**Max Input Images:** 10 URLs

## Next Steps

1. **Test Integration**: Test kie.ai API with actual API key in Settings tab
2. **Temp Image Hosting**: Implement temporary image upload service for features with image inputs
3. **Feature Updates**: Gradually add kie.ai support to features starting with text-only tools
4. **UI Indicators**: Add visual indicators showing which provider is being used
5. **Performance Optimization**: Adjust polling interval based on typical generation times

## Support

For issues or questions about kie.ai integration:
- Provider Docs: https://kie.ai/docs
- Model Info: google/nano-banana-edit (Gemini Flash 2.5 Image compatible)
- API Endpoint: https://api.kie.ai/api/v1/jobs/

---

**Last Updated:** January 2025
**Status:** Helper functions complete, feature integration pending
