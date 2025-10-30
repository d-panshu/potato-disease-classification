# 🥔 Potato Disease Classification API

This project builds and serves a deep learning model to classify potato plant diseases (Early Blight, Late Blight) or identify healthy plants from leaf images. It includes notebooks for model training, conversion, and two different FastAPI applications for serving the model.

## 📋 Features

  * **Deep Learning Model**: A TensorFlow/Keras model trained to classify potato leaf images into three categories:
      * Early Blight
      * Late Blight
      * Healthy
  * **Model Training**: Jupyter notebooks detailing the training process, including variations with `ImageDataGenerator`.
  * **Model Conversion**: Notebooks demonstrating conversion to TensorFlow Lite (TFLite) using post-training quantization and quantization-aware training.
  * **REST API (Python Server)**: A simple FastAPI server (`main.py`) that loads the saved Keras model directly to provide predictions.
  * **REST API (TF Serving Proxy)**: A second FastAPI server (`main-tf-serving.py`) designed to act as a proxy, forwarding prediction requests to a TensorFlow Serving instance.

## 🗂️ Project Structure

```
.
├── notebooks/
│   ├── potato-disease-classification-model.ipynb
│   ├── potato-disease-classification-model-using-image-data-generator.ipynb
│   ├── tf-lite-conversion-post-training.ipynb
│   ├── tf-lite-conversion-quantized-aware-training.ipynb
│   └── tf-lite-converter.ipynb
│
├── api/
│   ├── main.py                     # FastAPI server (loads .h5 model)
│   ├── main-tf-serving.py          # FastAPI server (proxy for TF Serving)
│
└── requirements.txt                # Python dependencies
```

*(Note: This is a suggested structure based on the files.)*

## 🚀 Getting Started

### 1\. Installation

Clone the repository and install the required Python packages:

```bash
git clone <your-repo-url>
cd <your-repo-name>
pip install -r requirements.txt
```

### 2\. Run the Notebooks

You can explore the model training and conversion process by running the Jupyter notebooks located in the `notebooks/` directory:

  * **`potato-disease-classification-model.ipynb`**: Main training notebook.
  * **`...-using-image-data-generator.ipynb`**: A variation of the training process.
  * **`tf-lite-*.ipynb`**: Notebooks for converting the trained model to TensorFlow Lite.

### 3\. Usage: Run the API Server

This project provides two ways to run the API.

#### Option A: Run the Simple FastAPI Server (loads Keras model)

This server loads the TensorFlow model directly.

1.  Make sure your saved model (e.g., `saved_models/1`) is available at the path specified in `main.py`.

2.  Run the server using uvicorn:

    ```bash
    # Navigate to the directory containing main.py
    uvicorn main:app --host localhost --port 8000 --reload
    ```

#### Option B: Run with TensorFlow Serving

This approach uses a separate TensorFlow Serving instance and a proxy FastAPI server.

1.  **Start TensorFlow Serving**:
    You must first serve your saved model (e.g., `potatoes_model`) using TensorFlow Serving. (Docker example):

    ```bash
    docker run -p 8501:8501 \
      --name tf_serving \
      -v "/path/to/your/models/potatoes_model:/models/potatoes_model" \
      -e MODEL_NAME=potatoes_model \
      tensorflow/serving
    ```

2.  **Run the FastAPI Proxy Server**:
    This server will forward requests to `http://localhost:8501`.

    ```bash
    # Navigate to the directory containing main-tf-serving.py
    uvicorn main-tf-serving:app --host localhost --port 8000 --reload
    ```

## 🌐 API Endpoints

Once the server is running (either option), you can send requests.

### `GET /ping`

A simple health check endpoint to confirm the server is running.

  * **Response**:
    ```json
    "Hello, I am alive"
    ```

### `POST /predict`

Upload an image file to get a disease classification.

  * **Body**: `form-data` with a key `file` containing the image.
  * **Response**:
    ```json
    {
      "class": "Late Blight",
      "confidence": 0.987
    }
    ```

-----
