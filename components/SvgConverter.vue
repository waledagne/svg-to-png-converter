<template>
  <div class="space-y-8">
    <FileUpload @file-selected="handleFile" />
    <Preview
      v-if="svgPreview"
      :svg-content="svgPreview"
      :dimensions="originalDimensions"
    />
    <ConversionControls
      v-if="svgPreview"
      v-model:width="outputWidth"
      v-model:height="outputHeight"
      v-model:scale="scaleFactor"
      @convert="convertToPng"
      :converting="converting"
      :error="error"
    />
  </div>
</template>

<script setup>
import { ref } from "vue";

const svgPreview = ref(null);
const outputWidth = ref(1024);
const outputHeight = ref(1024);
const scaleFactor = ref(2);
const originalDimensions = ref({ width: 0, height: 0 });
const converting = ref(false);
const error = ref("");

const handleFile = (file) => {
  error.value = "";
  const reader = new FileReader();
  reader.onload = (e) => {
    try {
      const content = e.target.result;
      const parser = new DOMParser();
      const doc = parser.parseFromString(content, "image/svg+xml");
      const parserError = doc.querySelector("parsererror");
      if (parserError) {
        throw new Error("Invalid SVG file");
      }
      svgPreview.value = content;
      extractDimensions(content);
    } catch (err) {
      error.value = "Invalid SVG file. Please try another file.";
      svgPreview.value = null;
    }
  };
  reader.onerror = () => {
    error.value = "Error reading file. Please try again.";
    svgPreview.value = null;
  };
  reader.readAsText(file);
};

const extractDimensions = (svgContent) => {
  try {
    const parser = new DOMParser();
    const svgDoc = parser.parseFromString(svgContent, "image/svg+xml");
    const svgElement = svgDoc.documentElement;

    const viewBox = svgElement.getAttribute("viewBox");
    if (viewBox) {
      const [, , width, height] = viewBox.split(" ").map(Number);
      if (width > 0 && height > 0) {
        originalDimensions.value = { width, height };
        outputWidth.value = width;
        outputHeight.value = height;
        return;
      }
    }

    const width = parseInt(svgElement.getAttribute("width")) || 1024;
    const height = parseInt(svgElement.getAttribute("height")) || 1024;
    originalDimensions.value = { width, height };
    outputWidth.value = width;
    outputHeight.value = height;
  } catch (err) {
    console.error("Error extracting dimensions:", err);
    originalDimensions.value = { width: 1024, height: 1024 };
    outputWidth.value = 1024;
    outputHeight.value = 1024;
  }
};

const convertToPng = async () => {
  error.value = "";
  converting.value = true;

  try {
    if (!svgPreview.value) {
      throw new Error("No SVG loaded");
    }

    const svg = new DOMParser().parseFromString(
      svgPreview.value,
      "image/svg+xml"
    ).documentElement;
    const svgData = new XMLSerializer().serializeToString(svg);

    // Create a canvas element
    const canvas = document.createElement("canvas");
    const ctx = canvas.getContext("2d");

    if (!ctx) {
      throw new Error("Could not get canvas context");
    }

    // Set final dimensions
    const finalWidth = outputWidth.value * scaleFactor.value;
    const finalHeight = outputHeight.value * scaleFactor.value;

    // Check if dimensions are within browser limits
    if (finalWidth > 16384 || finalHeight > 16384) {
      throw new Error(
        "Output dimensions too large. Please reduce size or scale factor."
      );
    }

    canvas.width = finalWidth;
    canvas.height = finalHeight;

    // Enable high-quality scaling
    ctx.imageSmoothingEnabled = true;
    ctx.imageSmoothingQuality = "high";

    // Create a blob URL for the SVG
    const svgBlob = new Blob([svgData], {
      type: "image/svg+xml;charset=utf-8",
    });
    const svgUrl = URL.createObjectURL(svgBlob);

    // Create an image element and load the SVG
    const img = new Image();
    await new Promise((resolve, reject) => {
      img.onload = resolve;
      img.onerror = () => reject(new Error("Failed to load SVG"));
      img.src = svgUrl;
    });

    // Clear canvas and draw the image
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.drawImage(img, 0, 0, canvas.width, canvas.height);

    // Convert canvas to blob
    const blob = await new Promise((resolve, reject) => {
      canvas.toBlob(
        (b) => (b ? resolve(b) : reject(new Error("Failed to create PNG"))),
        "image/png",
        1.0
      );
    });

    // Create download link
    const url = URL.createObjectURL(blob);
    const link = document.createElement("a");
    link.download = `converted-${outputWidth.value}x${outputHeight.value}@${scaleFactor.value}x.png`;
    link.href = url;
    link.click();

    URL.revokeObjectURL(url);
    URL.revokeObjectURL(svgUrl);
  } catch (err) {
    console.error("Error converting SVG:", err);
    error.value = err.message || "Error converting SVG. Please try again.";
  } finally {
    converting.value = false;
  }
};
</script>
