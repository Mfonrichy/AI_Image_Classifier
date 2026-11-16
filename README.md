# Cat, Dog, or Neither

AI-powered image recognition system for the Tech4Youth AI Engineering Capstone.
The current interface uses React and TypeScript, with a FastAPI backend serving
the existing TensorFlow/Keras model. The original Streamlit application remains
available in `app.py` as a backup.

## Project structure

```text
RMS_AI/
├── backend/
│   ├── main.py
│   ├── model_service.py
│   ├── requirements.txt
│   └── test_main.py
├── frontend/
│   ├── src/
│   │   ├── services/api.ts
│   │   ├── App.tsx
│   │   ├── index.css
│   │   └── main.tsx
│   ├── package.json
│   ├── vite.config.ts
│   └── public/
│       └── confusion_matrix.png
├── model/
│   └── cat_dog_neither_model.keras
├── utils/
│   └── prediction.py
├── app.py
└── Cat_Dog_or_Neither_Training.ipynb
```

The application reuses the existing trained model. It does not retrain or
modify the model.

## Run locally on Windows

The backend and frontend run in separate terminals.

### 1. Prepare the Python environment

From the project root:

```powershell
py -3.13 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r backend\requirements.txt
```

If `.venv` already exists, activate it and install the backend requirements:

```powershell
.\.venv\Scripts\Activate.ps1
python -m pip install -r backend\requirements.txt
```

### 2. Start the FastAPI backend

In the activated Python terminal:

```powershell
python -m uvicorn backend.main:app --reload --host 127.0.0.1 --port 8000
```

The API is available at `http://localhost:8000`. Health status is available at
`http://localhost:8000/health`, and interactive API documentation is available
at `http://localhost:8000/docs`.

### 3. Install and start the React frontend

Open a second terminal:

```powershell
cd "C:\Users\BEST PETERS COMPUTER\Desktop\RMS_AI\frontend"
npm install
npm run dev
```

Open `http://localhost:5173` in a browser.

The frontend uses `http://localhost:8000` by default. To use another backend
URL, create `frontend\.env.local` with:

```text
VITE_API_BASE_URL=http://localhost:8000
```

## Prediction pipeline

The FastAPI backend validates the upload, opens it as RGB, resizes it to
`224 x 224`, and passes float32 pixel values in the original `0-255` range to
the model. The saved model contains its own MobileNetV2 rescaling layer, so the
backend does not rescale pixels a second time.

The output classes are `cat`, `dog`, and `neither`. The API returns all three
softmax scores. These are model scores and should not be treated as calibrated
probabilities or guarantees of correctness.

The React interface supports dark mode and multiple image uploads. Multiple
images are sent to the same `/predict` endpoint individually and displayed as
separate results.

The evaluation section displays the existing confusion-matrix artifact from the
training notebook. It can also calculate a new confusion matrix when a folder
with this structure is selected:

```text
evaluation-images/
├── cat/
├── dog/
└── neither/
```

The folder names provide the true labels; without labeled folders, a confusion
matrix cannot be calculated from ordinary uploads.

## Validation

Run the backend tests:

```powershell
.\.venv\Scripts\python.exe -m pytest backend\test_main.py -q
```

Build the frontend:

```powershell
cd frontend
npm run build
```

Also test the running application with cat, dog, and neither images; JPG, JPEG,
and PNG files; different image dimensions; corrupted files; and sequential
uploads.
