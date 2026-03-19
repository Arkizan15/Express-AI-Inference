# 🧠 Express AI Inference API

A REST API built with **Express.js** and **TensorFlow.js** for running image-based disease prediction inference on the backend. Accepts image uploads and returns a predicted disease label with a confidence score.

---

## 🛠️ Tech Stack

| Package | Version | Purpose |
|---------|---------|---------|
| [express](https://expressjs.com/) | ^5.1.0 | HTTP server & routing |
| [@tensorflow/tfjs-node](https://www.tensorflow.org/js) | ^4.22.0 | ML model loading & inference |
| [multer](https://github.com/expressjs/multer) | ^2.1.1 | Multipart file upload handling |
| [cors](https://github.com/expressjs/cors) | ^2.8.5 | Cross-Origin Resource Sharing |
| [nodemon](https://nodemon.io/) | ^3.1.10 | Auto-restart in development |
| [eslint](https://eslint.org/) | ^9.31.0 | Code linting |

---

## 📁 Project Structure

```
express-ai-inference/
├── model/
│   ├── model.json          # TensorFlow.js model architecture
│   └── metadata.json       # Model metadata (labels, etc.)
├── src/
│   ├── server.js           # Entry point, Express app setup
│   ├── routes.js           # Route definitions
│   ├── controller.js       # Request handler logic
│   └── predict-services.js # TensorFlow inference logic
├── .dockerignore
├── dockerfile
├── eslint.config.js
├── package.json
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites

- Node.js >= 18
- A trained TensorFlow.js model placed in the `model/` directory:
  - `model.json`
  - `metadata.json` (must contain a `labels` array)

### 1. Install Dependencies

```bash
npm install
```

### 2. Run the Server

**Development (with auto-restart):**
```bash
npm run start
```

Server runs at `http://localhost:3000`.

### 3. Run Linter

```bash
npm run lint
```

---

## 🐳 Docker

### Build the Image

```bash
docker build -t express-ai-inference .
```

### Run the Container

```bash
docker run -p 3000:3000 express-ai-inference
```

The app exposes port `3000` inside the container.

---

## 📡 API Endpoints

### 🔮 Predict Disease from Image

```
POST /predict
```

**Content-Type:** `multipart/form-data`

**Request:**

Send an image file as form-data (any field name is accepted).

Example using `curl`:
```bash
curl -X POST http://localhost:3000/predict \
  -F "image=@/path/to/your/image.jpg"
```

**Success Response (200):**

```json
{
  "status": "success",
  "message": "Predict success",
  "data": {
    "disease": "Melanoma",
    "confidenceScore": 0.9821
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `status` | string | Always `"success"` on successful prediction |
| `message` | string | Human-readable status message |
| `data.disease` | string | Predicted disease label from model metadata |
| `data.confidenceScore` | number | Highest confidence score (0–1) from the model |

---

## ⚙️ How Inference Works

1. The uploaded image buffer is received from `req.files[0].buffer`.
2. The image is decoded and resized to **224×224** pixels using TensorFlow.js.
3. The tensor is expanded to add a batch dimension and converted to float.
4. The model runs a prediction and returns a score array.
5. The label with the highest score is matched against `metadata.json`'s `labels` array.
6. The predicted label and confidence score are returned as JSON.

---

## 📂 Model Setup

Place your TensorFlow.js model files in the `model/` directory:

```
model/
├── model.json
├── metadata.json       ← must include a "labels" array
└── *.bin               ← model weight shards (if any)
```

**Example `metadata.json`:**

```json
{
  "labels": [
    "Healthy",
    "Melanoma",
    "Psoriasis",
    "Eczema"
  ]
}
```

---

## 🔧 ESLint Config

This project uses `eslint-config-dicodingacademy` with flat config (`eslint.config.js`).

Run the linter:

```bash
npm run lint
```

---

## 🌐 CORS

CORS is enabled for all origins (`*`). To restrict it, update `server.js`:

```js
app.use(cors({
  origin: 'https://yourdomain.com'
}));
```
