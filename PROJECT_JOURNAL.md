# Project Journal
## Plant Disease Detection System

This document records my understanding of the project,
technical decisions, experiments, problems, solutions,
and lessons learned during development.

---
### Day 61 : 08/07/26
I have commenced the plant disease detection projection by first implementing changes in the README.md. Subsequently, I proceeded to establish automatic data downloading from the Kaggle dataset. Following this, I initiated the virtual environment and engaged with train_and_evaluate.py for approximately four hours. The train_and_evaluate.py script has been corrected; however, it is currently restricted to training, evaluation, confusion matrix generation, and classnames for backend support and testing, as full execution has not yet been performed. The subsequent objective is to comprehend the theoretical functionality, primarily within train_and_evaluate.py and test.py.

### Day 62 : 10/07/26

Subsequently, I began to analyze `train_and_evaluate.py` and `predict.py` within the plant-disease-detection project. Initially, I studied the input layer, which processes the image, followed by the `build_model()` function. This function contains two models: a custom model comprising five blocks of layers, and MobileNetV2.

Regarding the Dense layer, it accepts flattened input to identify relationships between neurons, ultimately producing the final output. The Dropout layer mitigates overfitting by deactivating neurons to enhance independence. The Convolutional Layer (Conv2D) does not process every pixel individually; instead, it analyzes small portions of the image, such as edges, corners, and textures. Pooling layers reduce the image dimensions while retaining critical information. Flatten layers convert the CNN outputs into a format suitable for the Dense layer; for instance, a CNN output of 7*7*64 is transformed into 3136 for the Dense layer.

Batch Normalization standardizes the output values before passing them to the subsequent layer. Activation functions include ReLU, which calculates the maximum between 0 and the input to mitigate vanishing gradients. The Sigmoid function is utilized for Binary Classification or Multi-Label Classification. The Softmax function, with a range between 0 and 1, is employed for multi-class classification. Dense layers can also function as output layers, and all activation functions are integral components of these output layers.

### Day 63 : 11/07/26

Regarding the plant disease detection project, we begin by examining the `build_model` function. This function primarily utilizes a `base_model`, specifically MobileNetV2, which offers a lighter architecture compared to models such as ResNet50 or VGG19. The `base_model` is instantiated from the Keras applications module as MobileNetV2. The input configuration specifies an image size of 240x240 with three channels for RGB data. The weights are pre-trained on ImageNet, a large-scale dataset.

During the training phase, the pre-built `base_model` retains its existing knowledge and is not modified to accommodate the current learning process, particularly when working with small datasets. The model is structured within a sequential flow. The first layer is the input layer, where `keras.Input` defines the shape; the default input size is set to 224, though this can be adjusted in the function definition. The subsequent layer is the `base_model`, which processes the image data. This layer operates on a single data matrix comprising inputs for height, width, shape, and texture, reducing dimensions through a global average pooling method.

Following this, a Dense layer establishes relationships between neurons, utilizing the ReLU activation function. This function is efficient and faster for modern approaches, effectively mitigating the vanishing gradient problem. Next, a Dropout layer is implemented with a 50% rate, deactivating neurons to ensure independent operation. This is followed by an additional Dense layer and another Dropout layer to optimize results. Finally, a Dense layer with a softmax activation function is applied, as the model performs multi-class classification. The `build_model_mobilenetv2` function is now prepared for model compilation.

### Day 65 : 13/07/26

The following overview outlines the key concepts of plant-disease detection:

In constructing the custom CNN architecture, the model is defined using five sequential blocks. Each block commences with a Conv2D layer, which focuses on extracting features such as textures, corners, and edges rather than processing individual pixels, thereby capturing essential information. This is followed by a ReLU activation layer for simplicity. Subsequently, a Batch Normalization layer is applied to provide normalized average values for the subsequent Conv2D layer. Another Batch Normalization layer precedes a MaxPooling2D layer, which reduces the image dimensions. Finally, a Dropout layer is incorporated to enhance the model's robustness.

Regarding model compilation, the architecture utilizes a learning rate of 0.001 (10^-3). The Adam optimizer is employed to facilitate training by adjusting the model's weights to improve performance. The iterative process involves prediction, error calculation, weight updates via the optimizer, and subsequent improved predictions. Adam is selected for its rapid convergence, adaptive learning rates for different parameters, and efficacy across various tasks with minimal tuning. An appropriate learning rate is critical; both excessively slow and fast learning rates present challenges, whereas a moderate step size, such as 0.001, is standard. The loss function utilized is categorical cross-entropy, which is appropriate for multi-class classification problems with one-hot encoded labels.

Key performance metrics include accuracy, precision, and recall:

- Accuracy: The ratio of correct predictions to total predictions. For example, if 95 out of 100 images are correctly classified, the accuracy is 95%.
- Precision: If there are 100 actual disease images and the model predicts 90 as diseased and 10 as healthy, the precision is 90%.
- Recall: If there are 100 diseased images and the model detects 95 while missing 5, the recall is 95%.

### Day 67 : 15/07/26

Then, I started working on a **Plant Disease Detection** project, where I prepared and trained a new dataset containing approximately **77,000 images** using the **MobileNetV2** architecture.

#### Callbacks

Before training the model, it is important to understand the `get_callbacks()` function. Callbacks are utility functions that monitor the training process after every epoch and automatically decide what actions should be taken based on the model's performance.

After building and compiling the model, the callbacks are passed to `model.fit()` along with the model save path. The project uses three important callbacks:

1. **ModelCheckpoint** – Saves only the **best-performing model**, rather than the model from the final epoch. The best model is selected based on the lowest validation loss.
2. **EarlyStopping** – Prevents overfitting by monitoring the validation loss. If the validation loss does not improve for **5 consecutive epochs**, the training process stops, and the model weights from the epoch with the lowest validation loss are restored.
3. **ReduceLROnPlateau** – Reduces the learning rate whenever the validation loss stops improving. The learning rate is multiplied by a factor of **0.5** until it reaches the minimum learning rate of **1 × 10⁻⁷**.

---

#### `train_model()`

The `train_model()` function starts the training process using `model.fit()`. The variable `history` stores all the training statistics returned by TensorFlow, including training and validation accuracy and loss after every epoch.

During training, batches of images from the training dataset are passed through the neural network. Each epoch follows this sequence:

**Training → Validation → Next Epoch**

An **epoch** represents one complete pass through the entire training dataset. The values stored in `history` are later used to visualize the learning process by plotting training and validation accuracy and loss curves.

---

#### `evaluate_model()`

After training, the model is evaluated using the validation dataset, which consists of approximately **20% of the total images** that were kept separate during dataset preparation.

The model predicts every validation image and returns several evaluation metrics:

- Loss
- Accuracy
- Precision
- Recall

These metrics are available because the model was compiled with:

- `accuracy`
- `Precision()`
- `Recall()`

This evaluation provides a clear understanding of the model's overall performance on unseen data.

---

#### `generate_classification_report()`

The `generate_classification_report()` function reads the validation dataset from the beginning, generates predictions for every image, and compares them with the actual class labels.

Using these predictions, it produces a detailed classification report containing:

- Precision
- Recall
- F1-score
- Support for each class

It can also be combined with Matplotlib to visualize the confusion matrix and other performance metrics.

---

#### `prepare_dataset(dataset_path, image_size, batch_size, validation_split)`

The `prepare_dataset()` function is responsible for preparing the dataset before training.

Its parameters include:

- `dataset_path` – Path to the dataset directory.
- `image_size` – Size of the input image (default is **224 × 224** pixels).
- `batch_size` – Number of images processed at a time (commonly **8**, **16**, or **32**).
- `validation_split` – Fraction of the dataset reserved for validation (typically **0.2** or **0.3**).

The function scans the dataset directory, sorts all disease folders, and stores their names in the `class_names` list.

---

#### `ImageDataGenerator`

The project uses **ImageDataGenerator**, which performs image preprocessing and data augmentation. Every image passes through this augmentation pipeline before being fed into the CNN.

Data augmentation creates multiple modified versions of the same image, allowing the model to learn from different orientations and conditions without requiring additional data.

The preprocessing steps include:

- **Rescaling (`rescale = 1./255`)** – Converts pixel values from the range **0–255** to **0–1**, making them more suitable for neural network training. For example, a pixel value of **128** becomes approximately **0.502**.
- **Rotation (`rotation_range = 20`)** – Randomly rotates images within ±20°.
- **Width Shift (`width_shift_range = 0.2`)** – Randomly shifts images horizontally.
- **Height Shift (`height_shift_range = 0.2`)** – Randomly shifts images vertically.
- **Zoom (`zoom_range = 0.2`)** – Randomly zooms in or out.
- **Horizontal Flip (`horizontal_flip = True`)** – Randomly flips images horizontally.
- **Brightness Adjustment (`brightness_range = [0.8, 1.2]`)** – Randomly changes image brightness.
- **Fill Mode (`fill_mode = "nearest"`)** – Fills empty pixels created after transformations using the nearest neighboring pixel values.

When `validation_split = 0.2` is specified, **80%** of the images are used for training, while the remaining **20%** are reserved for validation. For example, if the dataset contains **1,000 images**, **800** images are used for training and **200** images are used for validation.

---

#### `test_datagen`

Unlike the training data generator, `test_datagen` performs only **pixel value rescaling (`rescale = 1./255`)**. No data augmentation is applied because the model should be evaluated on real, unmodified images. This ensures that the evaluation accurately reflects the model's performance on unseen data.

### DAY 70 18-07-26

After completing the deep learning model, I focused on developing the backend API for the **Plant Disease Detection** project using **Django** and **Django REST Framework (DRF)**. The primary objective was to create a RESTful API capable of receiving plant leaf images, performing disease prediction using the trained MobileNetV2 model, storing prediction history, and providing disease-related information and recommendations.

#### Django Project Setup

The backend development began by creating a new Django project followed by a dedicated application named **`predictions`**, which contains all the prediction-related logic.

The project was then configured by updating the Django settings. Sensitive information such as the **SECRET_KEY**, model path, and maximum upload size were moved into a **`.env`** file using the **python-dotenv** package. This keeps confidential information outside the source code and improves security.

Several required applications were added to `INSTALLED_APPS`, including:

- `corsheaders`
- `rest_framework`
- `predictions`

The **CorsMiddleware** was also added to the middleware list to allow communication between the frontend and backend applications.

The remaining backend configuration included:

- Setting `ROOT_URLCONF = "backend.urls"`
- Enabling `CORS_ALLOW_ALL_ORIGINS = True`
- Reading `MODEL_PATH` from the `.env` file
- Reading `MAX_UPLOAD_SIZE` from the `.env` file

Finally, the project's `urls.py` file was configured so that every API endpoint is available under the base URL:

```
/api/
```

---

#### Database Design (`models.py`)

The application contains two database models.

#### 1. PredictionHistory

The **PredictionHistory** model stores every prediction made by the user. Each prediction includes:

- Uploaded image
- Predicted disease
- Confidence score
- Plant type
- Fertilizer recommendation
- Treatment recommendation
- Timestamp of prediction

The model also contains a **Meta** class where:

- Predictions are ordered by the latest timestamp.
- `verbose_name_plural` is set to **"Prediction Histories"** for better readability in the Django Admin Panel.

---

#### 2. DiseaseInfo

The **DiseaseInfo** model stores detailed information about every disease supported by the system.

Its fields include:

- Disease name
- Description
- Symptoms
- Causes
- Prevention methods
- Treatment
- Fertilizer recommendation

Its **Meta** class defines:

- `verbose_name_plural = "Disease Information"`

This table acts as a knowledge base that allows the application to provide detailed explanations and recommendations after prediction.

---

#### Serializers (`serializers.py`)

Django REST Framework serializers convert Django model instances into **JSON** so that they can be transmitted through the API.

Three serializers were implemented.

#### 1. ImageUploadSerializer

This serializer is responsible for validating uploaded images before prediction.

It contains only one field:

- `image`

---

#### 2. PredictionHistorySerializer

This serializer converts prediction records into JSON.

Its Meta class specifies:

**Model**

- `PredictionHistory`

**Fields**

- id
- image
- predicted_disease
- confidence
- plant_type
- fertilizer_recomm
- treatment_recomm
- timestamp

The following fields are marked as **read-only** because they are generated automatically by the backend:

- id
- timestamp

---

#### 3. DiseaseInfoSerializer

This serializer converts disease information into JSON format.

Its Meta class uses:

**Model**

- `DiseaseInfo`

**Fields**

- id
- disease_name
- description
- symptoms
- causes
- prevention
- treatment
- fertilizer

This serializer allows clients to retrieve complete disease information in a structured JSON response.

---

#### API Logic (`views.py`)

The `views.py` file contains the core logic of the backend and is responsible for processing requests, running predictions, and returning responses.

#### Loading the Trained Model

When the application starts, it first loads the trained TensorFlow model from the location specified by `MODEL_PATH`.

Before loading, the application verifies whether the model file exists. If the model is missing, an appropriate error is raised, preventing prediction requests from failing unexpectedly.

---

#### Image Preprocessing

The `preprocess_image()` function prepares uploaded images before passing them to the CNN model.

The preprocessing steps are:

1. Open the uploaded image.
2. Convert the image to **RGB** format if necessary.
3. Resize the image to **224 × 224 pixels**.
4. Normalize pixel values by dividing them by **255**.
5. Expand the image dimensions to create a batch of one image before prediction.

The processed image is then ready to be passed into the MobileNetV2 model.

---

#### Fertilizer Recommendation

The `get_fertilizer_recommendation()` function returns fertilizer recommendations based on the predicted disease.

It receives the disease name as input and retrieves the appropriate fertilizer recommendation from predefined mappings or the disease information database.

---

#### PredictDiseaseAPIView

This is the primary API endpoint of the application.

The endpoint accepts an uploaded image through a **POST** request.

Its workflow is as follows:

1. Validate the uploaded image using `ImageUploadSerializer`.
2. Preprocess the image.
3. Pass the image to the trained MobileNetV2 model.
4. Predict the disease class.
5. Calculate the confidence score.
6. Generate fertilizer and treatment recommendations.
7. Save the prediction into the `PredictionHistory` database.
8. Return the prediction results as a JSON response.

The response typically includes:

- Predicted disease
- Confidence score
- Plant type
- Fertilizer recommendation
- Treatment recommendation

---

#### PredictionHistory API

Two API views are provided for prediction history.

#### PredictionHistoryListAPIView

Returns the complete list of previous predictions stored in the database.

---

#### PredictionHistoryDetailAPIView

Returns detailed information for a single prediction using its unique ID.

Separating the list and detail views follows REST API design principles and improves maintainability.

---

#### Disease Information API

Similarly, two API views are created for disease information.

#### DiseaseInfoListAPIView

Returns all diseases available in the database.

---

#### DiseaseInfoDetailAPIView

Returns detailed information for a selected disease, including its description, symptoms, causes, prevention methods, treatment, and fertilizer recommendations.

Using separate endpoints for listing and retrieving records provides greater flexibility for frontend applications.

---

#### URL Configuration (`urls.py`)

The application's routes are defined inside `urls.py`.

The primary API endpoints include:

- `/api/predict/` – Predict plant disease from an uploaded image.
- `/api/history/` – Retrieve prediction history.
- `/api/history/<id>/` – Retrieve details of a specific prediction.
- `/api/diseases/` – List all disease information.
- `/api/diseases/<id>/` – Retrieve details of a specific disease.

These endpoints collectively expose the backend functionality through a RESTful API.

---

#### Django Admin

To simplify database management, both database models were registered inside `admin.py`.

The registered models include:

- `PredictionHistory`
- `DiseaseInfo`

This enables administrators to view, edit, and manage prediction records and disease information directly through the Django Admin Panel.

---

#### Dataset

The deep learning model was trained using the **New Plant Diseases Dataset** from Kaggle:

**Dataset:** `vipoooool/new-plant-diseases-dataset`

The dataset contains thousands of labeled images of healthy and diseased plant leaves across multiple crop species. It serves as the foundation for training the MobileNetV2 model and enables the application to accurately classify plant diseases while providing fertilizer and treatment recommendations.

### DAY 71 19-07-26

#### Frontend Development

Before starting the frontend documentation, I trained the model using a **77,000-image dataset** to improve its accuracy and generalization. Once the model was ready, I developed a **Telegram Bot** so users could interact with the model directly from Telegram.

#### Telegram Bot

The Telegram bot consists of a `config` file that stores the following configuration:

- **Bot Token** – Used to authenticate the Telegram bot.
- **Backend URL** – Points to the Django backend API for predictions.

The bot supports the following commands:

- **`/start`** – Starts the bot, displays a welcome message, and asks the user to upload a plant leaf image for prediction.
- **`/predict`** – Sends the uploaded image to the backend API, receives the prediction in JSON format, converts it into a user-friendly text response, and displays the disease prediction.
- **`/about`** – Displays information about the Plant Disease Detection project.
- **`/contact`** – Provides contact information for support or feedback.
- **`/clear`** – Clears the previous conversation so that only the user's new responses remain visible.

---

#### Frontend Documentation

The frontend of the Plant Disease Detection project was developed using **React** with **Vite**, providing a fast and modern development environment.

#### Project Structure

The `src` directory is mainly divided into three sections:

- **API**
- **Components**
- **Pages**

All these modules are integrated through the `App.jsx` file.

---

#### API

#### `api.js`

The `api.js` file is responsible for handling communication between the React frontend and the Django backend. It sends requests to the backend APIs and returns the responses to the frontend components.

---

#### Components

#### `HistoryCard.jsx`

Displays each previously predicted disease in a card format. Every prediction made by the user is stored and rendered using this reusable component.

#### `ImageUpload.jsx`

Allows users to upload plant leaf images. The component validates the selected image, sends it through the image upload serializer to the backend API, and receives the prediction result in JSON format.

#### `ILoading.jsx`

Displays a loading animation while the prediction request is being processed. Once the backend returns the prediction result, the loading indicator disappears automatically.

#### `Navbar.jsx`

Provides navigation throughout the application. It contains links to the following pages:

- Home
- History
- Disease Information

#### `PredictCard.jsx`

Displays the prediction returned by the backend API, including the detected disease and other relevant information in a clean card layout.

---

#### Pages

#### `Home.jsx`

The main landing page where users upload plant leaf images and receive disease predictions.

#### `History.jsx`

Displays all previous predictions made by the user, allowing them to review earlier results.

#### `DiseaseInfo.jsx`

Provides detailed information about plant diseases, including descriptions, symptoms, and possible treatments.

```bash
python manage.py migrate && \
python manage.py runserver 0.0.0.0:8000
```

---

#### Application Entry

#### `App.jsx`

The `App.jsx` file acts as the application's root component. It integrates the navigation system, page routing, API communication, and all reusable components into a single React application.

---

#### Docker Deployment

The project is containerized using Docker, with separate images for the frontend and backend.

#### Frontend Docker Configuration

The frontend uses the **Node Alpine** image because of its lightweight size.

The Docker build process performs the following steps:

1. Uses the `node:alpine` base image.
2. Sets the working directory inside the container.
3. Copies `package.json` and other dependency files.
4. Installs all required packages using `npm install`.
5. Copies the entire frontend source code into the container.
6. Exposes port **5173**, which is the default Vite development server port.
7. Starts the React application using the `CMD` instruction in exec (list) form.

---

#### Backend Docker Configuration

The backend uses the **Python Slim** image to minimize the image size while maintaining the required Python environment.

The Docker build process performs the following steps:

1. Uses the `python:slim` base image.
2. Sets the working directory inside the container.
3. Copies the `requirements.txt` file.
4. Installs all required Python packages using `pip install -r requirements.txt`.
5. Copies the complete Django project into the container.
6. Exposes port **8000**.
7. Runs the following command:

This command first applies all database migrations and then starts the Django development server, making it accessible from outside the container.