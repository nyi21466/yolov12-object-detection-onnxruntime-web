# yolo object detect onnxruntime-web

<img src="./preview.png" height=80% width=80%>

This is yolo model object detect browser, powered by onnxruntime web.

Support Webgpu acceleration performance and wasm(cpu).

Realtime process webcam, image.

Add your custom model for inference.

## Models

### Available Yolo Models

| Model                                                  | Input Size | Param. |
| :----------------------------------------------------- | :--------: | :----: |
| [YOLO11-N](https://github.com/ultralytics/ultralytics) |    640     |  2.6M  |
| [YOLO11-S](https://github.com/ultralytics/ultralytics) |    640     |  9.4M  |
| [YOLO11-M](https://github.com/ultralytics/ultralytics) |    640     | 20.1M  |

### NMS decoder (⚠️ NOT USEING NOW)

Build decoder model from [onnx-modifier](https://github.com/ZhangGe6/onnx-modifier) by myself.

View model graph detail in [netron.app](https://netron.app/?url=https://github.com/nomi30701/yolo-object-detection-onnxruntime-web/blob/main/public/yolo-decoder.onnx)

<details>
  <summary>Click to see graph.</summary>
  <img src="https://github.com/nomi30701/yolo-object-detection-onnxruntime-web/blob/main/yolo-decoder-graph-1.png" height=70% width=70%>
  <img src="https://github.com/nomi30701/yolo-object-detection-onnxruntime-web/blob/main/yolo-decoder-graph-2.jpg" height=70% width=70%>
</details>

## Setup

```bash
git clone https://github.com/nomi30701/yolo-object-detection-onnxruntime-web.git
cd yolo-object-detection-onnxruntime-web
yarn install # install dependencies
```

## Scripts

```bash
yarn dev # start dev server
```

## 🔧 Using Custom YOLO Models

To use a custom YOLO model, follow these steps:

### Step 1: Convert your model to ONNX format

Use Ultralytics or your preferred method to export your YOLO model to ONNX format. Ensure to use `opset=12` for WebGPU compatibility.

```python
from ultralytics import YOLO

# Load your model
model = YOLO("path/to/your/model.pt")

# Export to ONNX
model.export(format="onnx", opset=12, dynamic=True)
```

### Step 2: Add the model to the project

You can either:

- 📁 Copy your ONNX model file to the `./public/models/` directory
- 🔄 Upload your model directly through the `**Add model**` button in the web interface

#### Step 2-1: 📁 Copy your ONNX model file to the `./public/models/` directory

In App.jsx, Ctrl+F search 'yolo11n-2.6M'

```jsx
<select name="model-selector">
  <option value="yolo11n">yolo11n-2.6M</option>
  <option value="yolo11s">yolo11s-9.4M</option>
  <option value="yolo11m">yolo11m-20.1M</option>
  <option value="your-custom-model-name">Your Custom Model</option>
</select>
```

Replace `"your-custom-model-name"` with the filename of your ONNX model.

### Step 3: Update class definitions

Update the `src/utils/yolo_classes.json` file with the class names that your custom model uses. This file should contain a dict of strings representing the class labels.

For example:

```json
{
  "class": {
    "0": "person",
    "1": "bicycle",
    "2": "car",
    "3": "motorcycle",
    "4": "airplane"
  }
}
```

Make sure the classes match exactly with those used during training of your custom model.

### Step 4: Refresh and select your new model 🎉

> 🚀 WebGPU Support
>
> Ensure you set `opset=12` when exporting ONNX models, as this is required for WebGPU compatibility.

## 📸 Image Processing Options

The web application provides two options for handling input image sizes, controlled by the `imgsz_type` setting:

- **Dynamic:**

  - When selected, the input image is used at its original size without resizing.
  - Inference time may vary depending on the image resolution; larger images take longer to process.

- **Zero Pad:**
  - When selected, the input image is first padded with zero pixels to make it square (by adding padding to the right and bottom).
  - The padded image is then resized to 640x640 pixels.
  - This option provides a balance between accuracy and inference time, as it avoids extreme scaling while maintaining a predictable processing speed.
  - Use this option for real-time applications.

> ✨ Dynamic input
>
> This requires that the YOLO model was exported with `dynamic=True` to support variable input sizes.
