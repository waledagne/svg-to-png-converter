# SVG to PNG Converter

A modern, responsive web application built with Nuxt 3 that converts SVG files to PNG format with customizable dimensions and scale factors. Features a beautiful UI with dark mode support and intelligent scale factor handling.

## ✨ Features

- 🎨 Convert SVG files to PNG format
- 📐 Customize output dimensions
- 🔍 Smart scale factor selection (1x to 64x)
- 🌗 Dark mode support with system preference detection
- 🖼️ Live SVG preview with dimensions
- 📱 Fully responsive design
- 🎯 Drag and drop file upload
- ⚡ Fast, client-side conversion
- 🔒 Safe conversion with browser limitations handling
- 💾 Automatic scale adjustment for large outputs

## 🚀 Quick Start

### Prerequisites

- Node.js 16.x or later
- npm 7.x or later

### Installation

1. Clone the repository:

```bash
git clone https://github.com/yourusername/svg-to-png-converter.git
cd svg-to-png-converter
```

2. Install dependencies:

```bash
npm install
```

3. Start the development server:

```bash
npm run dev
```

### Available Scripts

- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run preview` - Preview production build
- `npm run generate` - Generate static site

## 🎯 Usage

1. Upload an SVG file:

   - Click the upload area or drag and drop your SVG file
   - The file will be previewed with its dimensions

2. Customize conversion settings:

   - Adjust output dimensions (width and height)
   - Select a scale factor based on your needs
   - Available scale factors adjust automatically based on browser limitations

3. Convert and download:
   - Click "Convert to PNG" to process your file
   - The converted PNG will download automatically

### Smart Scale Factor Selection

The converter intelligently handles scale factors to prevent browser limitations:

- Automatically filters available scale factors based on output dimensions
- Prevents conversion attempts that would exceed browser canvas limits
- Adjusts scale factor automatically when dimensions change
- Maximum output size: 16384x16384 pixels (browser limitation)

### Theme Support

- 🌞 Light mode: Clean, professional interface
- 🌙 Dark mode: Easy on the eyes, perfect for night work
- 🔄 Automatic: Follows system preferences
- 💾 Persistent: Remembers your preference

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
