---
icon: python
cover: https://cdn.sdccdn.com/img/datacamp-banner.png
coverY: 0
coverHeight: 139
---

# DL for Cyber Threat Detection

**Date:** 24.07.2026

**Core Concept:** Building neural networks to classify system logs as benign or malicious using the BETH dataset, comparing DataCamp's official solution with an optimized production-grade approach.

***

## Project Overview

Cyber threats evolve faster than traditional rule-based detection systems can adapt. Organizations face malware, phishing, denial-of-service attacks, and data exfiltration with increasing sophistication. Manual analysis by security teams cannot scale to the volume of modern logs.

Deep learning models excel at pattern recognition across vast datasets. By training a neural network on labelled logs (BETH dataset), you build an automated threat detector that:

* Identifies non-obvious attack patterns humans miss
* Scales to millions of events per day
* Adapts as threats evolve (via retraining)
* Flags suspicious events for analyst review

This project implements a binary classifier: **sus\_label = 1** (malicious) or **sus\_label = 0** (benign).

***

## The Data

The BETH dataset simulates real-world system logs with the following features:

<table><thead><tr><th width="178.5999755859375">Column</th><th width="429.5999755859375">Description</th><th>Type</th></tr></thead><tbody><tr><td>processId</td><td>Unique identifier for the process generating the event</td><td>int64</td></tr><tr><td>threadId</td><td>Thread ID spawning the log</td><td>int64</td></tr><tr><td>parentProcessId</td><td>ID of the parent process</td><td>int64</td></tr><tr><td>userId</td><td>User ID initiating the event</td><td>int64</td></tr><tr><td>mountNamespace</td><td>Mount namespace restrictions for the process</td><td>int64</td></tr><tr><td>argsNum</td><td>Number of arguments passed to the event</td><td>int64</td></tr><tr><td>returnValue</td><td>Return value from the event (typically 0)</td><td>int64</td></tr><tr><td><strong>sus_label</strong></td><td><strong>Target label: 1 = suspicious, 0 = benign</strong></td><td><strong>int64</strong></td></tr></tbody></table>

We have three splits:

* **labelled\_train.csv** – training data
* **labelled\_validation.csv** – hyperparameter tuning
* **labelled\_test.csv** – final evaluation

The data is preprocessed; your job begins at feature engineering and model development.

***

## DataCamp's Official Solution (Quick Walkthrough)

The official solution provided by DataCamp is intentionally minimal and beginner-focused. Here's what it does:

```python
# This is not my code, this is DataCamp's official solution for reference
# Import required libraries
import pandas as pd
from sklearn.preprocessing import StandardScaler
import torch
import torch.nn as nn
import torch.nn.functional as functional
from torch.utils.data import DataLoader, TensorDataset
import torch.optim as optim
from torchmetrics import Accuracy
# from sklearn.metrics import accuracy_score  # uncomment to use sklearn

# Load preprocessed data
train_df = pd.read_csv('labelled_train.csv')
test_df = pd.read_csv('labelled_test.csv')
val_df = pd.read_csv('labelled_validation.csv')

# Separate features and labels for training, testing, and validation sets
X_train = train_df.drop('sus_label', axis=1).values
y_train = train_df['sus_label'].values
X_test = test_df.drop('sus_label', axis=1).values
y_test = test_df['sus_label'].values
X_val = val_df.drop('sus_label', axis=1).values
y_val = val_df['sus_label'].values

# Initialize the StandardScaler
scaler = StandardScaler()

# Fit the scaler on the training data and transform the training data
X_train = scaler.fit_transform(X_train)

# Transform the test and validation data using the fitted scaler
X_test = scaler.transform(X_test)
X_val = scaler.transform(X_val)

# Convert the numpy arrays to PyTorch tensors
X_train_tensor = torch.tensor(X_train, dtype=torch.float32)
y_train_tensor = torch.tensor(y_train, dtype=torch.float32).view(-1, 1)
X_test_tensor = torch.tensor(X_test, dtype=torch.float32)
y_test_tensor = torch.tensor(y_test, dtype=torch.float32).view(-1, 1)
X_val_tensor = torch.tensor(X_val, dtype=torch.float32)
y_val_tensor = torch.tensor(y_val, dtype=torch.float32).view(-1, 1)

# Define the model using nn.Sequential
model = nn.Sequential(
    nn.Linear(X_train.shape[1], 128),  # First fully connected layer
    nn.ReLU(),  # ReLU activation
    nn.Linear(128, 64),  # Second fully connected layer
    nn.ReLU(),  # ReLU activation
    nn.Linear(64, 1),  # Third fully connected layer
    nn.Sigmoid()  # Sigmoid activation for binary classification
)

# Initialize the loss function and optimizer
criterion = nn.CrossEntropyLoss() 

optimizer = optim.SGD(model.parameters(), lr=1e-3, weight_decay=1e-4)

"""
try BCELoss() - Binary Cross Entropy Loss for binary classification, meaning the model only have 2 classes. This will provide accurate and higher probability.

try optimizer = optim.Adam(model.parameters(), lr=0.001)  # Adam optimizer with learning rate 0.001

Adam computes individual adaptive learning rates for different parameters from 
estimates of first and second moments of the gradients. This helps in faster convergence and better handling of sparse gradients. Adam is generally better than SGD for most problems as it adapts the learning rate for each parameter, which can lead to better performance and faster training.
"""

# Training loop
num_epoch = 10
for epoch in range(num_epoch):
    model.train()  # Set the model to training mode
    optimizer.zero_grad()  # Clear the gradients
    outputs = model(X_train_tensor)  # Forward pass: compute the model output
    loss = criterion(outputs, y_train_tensor)  # Compute the loss
    loss.backward()  # Backward pass: compute the gradients
    optimizer.step()  # Update the model parameters

# Model Evaluation
model.eval()  # Set the model to evaluation mode
with torch.no_grad():  # Disable gradient calculation for efficiency
    y_predict_train = model(X_train_tensor).round()  # Predict on training data and round the outputs
    y_predict_test = model(X_test_tensor).round()  # Predict on test data and round the outputs
    y_predict_val = model(X_val_tensor).round()  # Predict on validation data and round the outputs

# Calculate accuracy using torchmetrics
accuracy = Accuracy(task="binary")

train_accuracy = accuracy(y_predict_train, y_train_tensor)
test_accuracy = accuracy(y_predict_test, y_test_tensor)
val_accuracy = accuracy(y_predict_val, y_val_tensor)

# convert to int or float
train_accuracy = train_accuracy.item()
test_accuracy = test_accuracy.item()
val_accuracy = val_accuracy.item()

print("Training accuracy: {0}".format(train_accuracy))
print("Validation accuracy: {0}".format(val_accuracy))
print("Testing accuracy: {0}".format(test_accuracy))

# Calculate the accuracy using sklearn
# train_accuracy = accuracy_score(y_train_tensor, y_predict_train)
# val_accuracy = accuracy_score(y_val_tensor, y_predict_val)
# test_accuracy = accuracy_score(y_test_tensor, y_predict_test)
```

**What it does well:**

* Simple, readable code
* Shows core PyTorch workflow
* Uses StandardScaler correctly
* Implements basic training loop

**Limitations (with hints in comments):**

* Loss function mismatch: CrossEntropyLoss expects 2 output neurons, but model outputs 1 with Sigmoid
* Full-batch training (no mini-batching) – less efficient than mini-batches
* Only reports accuracy – misses precision, recall, F1
* SGD optimizer is slower than Adam (comment hints: "try BCELoss()" and "try Adam")
* No validation monitoring during training
* No early stopping mechanism

DataCamp intentionally leaves these as exercises for you to explore and improve.

***

## Optimized Solution: Block by Block

This section implements an approach that fixes the official solution's limitations and adds enterprise practices.

***

### BLOCK 1: IMPORT LIBRARIES AND CHECK GPU

```python
# ============================================================
# BLOCK 1: IMPORT REQUIRED LIBRARIES
# ============================================================
import pandas as pd
import numpy as np
from sklearn.preprocessing import StandardScaler
import torch
import torch.nn as nn
import torch.nn.functional as functional
from torch.utils.data import DataLoader, TensorDataset
import torch.optim as optim
from torchmetrics import Accuracy
from sklearn.metrics import precision_recall_fscore_support, confusion_matrix, roc_auc_score
import matplotlib.pyplot as plt

print("All libraries imported successfully!")
```

**Key imports:**

* **pandas, numpy** – data manipulation
* **sklearn.preprocessing.StandardScaler** – feature normalization
* **torch, torch.nn, torch.nn.functional** – PyTorch neural network tools
* **DataLoader, TensorDataset** – efficient batch training
* **optim** – optimization algorithms (Adam, SGD, etc.)
* **torchmetrics.Accuracy** – classification metrics
* **sklearn.metrics** – comprehensive evaluation (precision, recall, F1, ROC-AUC, confusion matrix)
* **matplotlib** – visualization

***

### BLOCK 1B: GPU AVAILABILITY CHECK

```python
# ============================================================
# BLOCK 1B: GPU AVAILABILITY CHECK
# ============================================================
print("="*60)
print("GPU AVAILABILITY CHECK")
print("="*60)

cuda_available = torch.cuda.is_available()
print(f"CUDA available: {cuda_available}")

if cuda_available:
    print(f"GPU: {torch.cuda.get_device_name(0)}")
    print(f"GPU Memory: {torch.cuda.get_device_properties(0).total_memory / 1e9:.2f} GB")
    print(f"PyTorch CUDA version: {torch.version.cuda}")
    device = torch.device('cuda:0')
    print(f"✓ GPU will be used for training (FAST)")
else:
    print(f"⚠ No GPU detected. CPU will be used")
    device = torch.device('cpu')

print(f"\nDevice set to: {device}")
print("="*60)
```

**Why check GPU?**

* GPU training: 10 epochs in 30-60 seconds
* CPU training: 10 epochs in 1-2 minutes
* This check tells you the hardware capabilities of your environment

***

### BLOCK 2: LOAD DATA

```python
# ============================================================
# BLOCK 2: LOAD DATA FROM CSV FILES
# ============================================================
# DataCamp provides three preprocessed CSV files
train_df = pd.read_csv('labelled_train.csv')
test_df = pd.read_csv('labelled_test.csv')
val_df = pd.read_csv('labelled_validation.csv')

# Display basic info
print("Data loaded successfully!")
print(f"Train shape: {train_df.shape}")
print(f"Test shape: {test_df.shape}")
print(f"Val shape: {val_df.shape}")
print(f"\nFirst 5 rows of training data:")
print(train_df.head())
print(f"\nClass distribution in training data:")
print(train_df['sus_label'].value_counts())
```

**Why three splits?**

* **Training set** – used to update weights via backpropagation
* **Validation set** – used to tune hyperparameters and detect overfitting during training
* **Test set** – held out completely; final evaluation only (model never sees this data during training)

***

### BLOCK 3: SEPARATE FEATURES AND TARGETS

```python
# ============================================================
# BLOCK 3: SEPARATE FEATURES (X) FROM TARGET LABELS (y)
# ============================================================
# Drop the 'sus_label' column to get features (X)
X_train = train_df.drop('sus_label', axis=1)
y_train = train_df['sus_label'].values  # Extract as numpy array

X_val = val_df.drop('sus_label', axis=1)
y_val = val_df['sus_label'].values

X_test = test_df.drop('sus_label', axis=1)
y_test = test_df['sus_label'].values

print(f"Features shape (train): {X_train.shape}")
print(f"Target shape (train): {y_train.shape}")
print(f"Number of features: {X_train.shape[1]}")
```

**Key point:** We separate X (features: processId, threadId, etc.) from y (target: sus\_label). The model learns to predict y from X.

***

### BLOCK 4: FEATURE SCALING (STANDARDIZATION)

```python
# ============================================================
# BLOCK 4: SCALE FEATURES USING STANDARDSCALER
# ============================================================
# Why scale? Neural networks converge faster when features have
# mean ≈ 0 and standard deviation ≈ 1

scaler = StandardScaler()

# CRITICAL: Fit scaler ONLY on training data
# This prevents data leakage (test data info leaking into training)
X_train_scaled = scaler.fit_transform(X_train)

# Apply the same transformation to validation and test sets
# (using statistics learned from training data only)
X_val_scaled = scaler.transform(X_val)
X_test_scaled = scaler.transform(X_test)

print("Feature scaling complete!")
print(f"Train mean: {X_train_scaled.mean():.6f}, std: {X_train_scaled.std():.6f}")
print(f"(Scaled features should have mean ≈ 0, std ≈ 1)")
```

**Why not fit on test data?** If you fit the scaler on test data, you're using test information during training. This leaks future data into the past, inflating accuracy metrics.

***

### BLOCK 5: CONVERT TO PYTORCH TENSORS

```python
# ============================================================
# BLOCK 5: CONVERT NUMPY ARRAYS TO PYTORCH TENSORS
# ============================================================
# PyTorch requires tensors (not numpy arrays) for training

# FloatTensor for continuous features (decimal values)
X_train_tensor = torch.FloatTensor(X_train_scaled)
y_train_tensor = torch.LongTensor(y_train)  # LongTensor for class indices (0, 1)

X_val_tensor = torch.FloatTensor(X_val_scaled)
y_val_tensor = torch.LongTensor(y_val)

X_test_tensor = torch.FloatTensor(X_test_scaled)
y_test_tensor = torch.LongTensor(y_test)

print("Tensors created successfully!")
print(f"X_train_tensor shape: {X_train_tensor.shape}")
print(f"y_train_tensor shape: {y_train_tensor.shape}")
print(f"Data type (X): {X_train_tensor.dtype}, Data type (y): {y_train_tensor.dtype}")
```

**Tensor types:**

* **FloatTensor** (float32) – for continuous features; allows gradient computation
* **LongTensor** (int64) – for class labels (0 or 1); required by CrossEntropyLoss

***

### BLOCK 6: CREATE DATA LOADERS FOR BATCHING

```python
# ============================================================
# BLOCK 6: CREATE DATA LOADERS FOR MINI-BATCH TRAINING
# ============================================================
# Why batch training?
# 1. Reduces memory usage (process 32 samples at a time, not all)
# 2. Adds gradient noise (helps escape local minima)
# 3. Allows efficient GPU parallelization
# 4. Enables shuffling (randomizes learning order)

# TensorDataset pairs features (X) with labels (y)
train_dataset = TensorDataset(X_train_tensor, y_train_tensor)
val_dataset = TensorDataset(X_val_tensor, y_val_tensor)

# DataLoader batches data and shuffles training data
train_loader = DataLoader(
    train_dataset,
    batch_size=32,      # 32 samples per batch
    shuffle=True        # Shuffle training data each epoch
)

val_loader = DataLoader(
    val_dataset,
    batch_size=32,
    shuffle=False       # Don't shuffle validation (order doesn't matter)
)

print("DataLoaders created successfully!")
print(f"Number of training batches: {len(train_loader)}")
print(f"Number of validation batches: {len(val_loader)}")
print(f"Each batch contains 32 samples (except possibly the last batch)")
```

**Batch size selection:**

* **32** is industry standard (balance between memory and convergence)
* Smaller batches (8, 16) = more gradient noise, slower but sometimes better generalization
* Larger batches (128, 256) = faster training but may get stuck in sharp local minima

***

### BLOCK 7: DEFINE NEURAL NETWORK ARCHITECTURE

```python
# ============================================================
# BLOCK 7: DEFINE NEURAL NETWORK CLASS
# ============================================================
class ThreatDetectorNet(nn.Module):
    """
    Feedforward neural network for binary threat classification.
    
    Architecture:
    - Input Layer: 7 features (processId, threadId, parentProcessId, userId,
                               mountNamespace, argsNum, returnValue)
    - Hidden Layer 1: 128 neurons + ReLU activation + Dropout(0.3)
    - Hidden Layer 2: 64 neurons + ReLU activation + Dropout(0.3)
    - Output Layer: 2 neurons (logits for class 0 [benign] and class 1 [malicious])
    
    Design notes:
    - 2 output neurons (not 1) allows model to learn class-specific features
    - ReLU introduces non-linearity (enables learning complex decision boundaries)
    - Dropout(0.3) randomly deactivates 30% of neurons during training (regularization)
    - No output activation (softmax applied by CrossEntropyLoss internally)
    """
    
    def __init__(self, input_size, hidden1=128, hidden2=64):
        super(ThreatDetectorNet, self).__init__()
        
        # Define linear layers
        self.fc1 = nn.Linear(input_size, hidden1)  # Input -> Hidden1
        self.fc2 = nn.Linear(hidden1, hidden2)     # Hidden1 -> Hidden2
        self.fc3 = nn.Linear(hidden2, 2)           # Hidden2 -> Output (2 classes)
        
        # Dropout for regularization (prevents overfitting)
        self.dropout = nn.Dropout(0.3)
    
    def forward(self, x):
        """
        Forward pass: compute predictions for input samples.
        
        Args:
            x (torch.Tensor): Input features of shape (batch_size, input_size)
        
        Returns:
            torch.Tensor: Raw logits of shape (batch_size, 2)
        """
        # First hidden layer
        x = self.fc1(x)                    # Linear: (batch, input) -> (batch, 128)
        x = functional.relu(x)             # ReLU: max(0, x) adds non-linearity
        x = self.dropout(x)                # Dropout: randomly zero ~30% of neurons
        
        # Second hidden layer
        x = self.fc2(x)                    # Linear: (batch, 128) -> (batch, 64)
        x = functional.relu(x)             # ReLU activation
        x = self.dropout(x)                # Dropout
        
        # Output layer
        x = self.fc3(x)                    # Linear: (batch, 64) -> (batch, 2)
        # No activation here; CrossEntropyLoss applies softmax internally
        
        return x


# Instantiate the model
input_features = X_train_scaled.shape[1]  # Should be 7
model = ThreatDetectorNet(input_size=input_features)

print("Model created successfully!")
print(f"\nModel architecture:\n{model}")
print(f"\nTotal parameters: {sum(p.numel() for p in model.parameters())}")
```

**Why this architecture?**

* **128 → 64 neurons:** Gradually compress information from raw features to abstract representations
* **ReLU activation:** f(x) = max(0, x); allows learning non-linear patterns (attacks often have non-linear signatures)
* **Dropout(0.3):** Random neuron deactivation prevents co-adaptation; the model learns robust features
* **2 output neurons:** Each neuron learns class-specific patterns; more expressive than single sigmoid output

***

### BLOCK 8: CONFIGURE LOSS, OPTIMIZER, AND TRAINING

```python
# ============================================================
# BLOCK 8: SET UP LOSS FUNCTION, OPTIMIZER, AND TRAINING CONFIG
# ============================================================

# Loss function: CrossEntropyLoss
# Combines softmax (converts logits to probabilities) + negative log-likelihood
# Perfect for multi-class classification (here: 2 classes)
# Expects: output shape (batch_size, 2), target shape (batch_size,)
criterion = nn.CrossEntropyLoss()

# Optimizer: Adam (Adaptive Moment Estimation)
# Computes individual adaptive learning rates for each parameter
# Faster convergence than vanilla SGD, handles sparse gradients better
optimizer = optim.Adam(model.parameters(), lr=0.001)

# Training configuration
# DataCamp requirement: 10 epochs for this small dataset size
num_epochs = 10

# Move model to device (device was set in Block 1B)
model.to(device)

print(f"Loss function: CrossEntropyLoss (binary classification)")
print(f"Optimizer: Adam (lr=0.001)")
print(f"Number of epochs: {num_epochs}")
print(f"Device: {device}")
print(f"\nWhy CrossEntropyLoss?")
print(f"  - Expects 2 output neurons (not 1)")
print(f"  - Applies softmax internally (converts logits to probabilities)")
print(f"  - Applies negative log-likelihood loss")
print(f"  - Standard for multi-class classification")
print(f"\nWhy Adam over SGD?")
print(f"  - Computes per-parameter adaptive learning rates")
print(f"  - Handles sparse gradients better")
print(f"  - Typically converges faster")
print(f"\nTraining time estimate:")
print(f"  - GPU: ~30-60 seconds for {num_epochs} epochs")
print(f"  - CPU: ~1-2 minutes for {num_epochs} epochs")
```

**Loss function comparison:**

* **CrossEntropyLoss:** For multi-class (2+ classes), expects logits + class indices
* **BCELoss:** For binary classification, expects single sigmoid output + float targets
* **MSELoss:** Regression, not suitable for classification

**Optimizer comparison:**

* **Adam:** Adaptive, fast convergence, works well out-of-box
* **SGD:** Simple, stable, but requires careful learning rate tuning
* **RMSprop:** Middle ground, good for RNNs

Adam is industry standard for most deep learning tasks.

***

### BLOCK 8B: MOVE TENSORS TO DEVICE (GPU/CPU)

```python
# ============================================================
# BLOCK 8B: MOVE ALL TENSORS TO DEVICE
# ============================================================
# Critical: Move tensors to same device as model for computation
# If tensors are on CPU and model is on GPU (or vice versa), training will fail

X_train_tensor = X_train_tensor.to(device)
y_train_tensor = y_train_tensor.to(device)

X_val_tensor = X_val_tensor.to(device)
y_val_tensor = y_val_tensor.to(device)

X_test_tensor = X_test_tensor.to(device)
y_test_tensor = y_test_tensor.to(device)

print("All tensors moved to device")
print(f"X_train on: {X_train_tensor.device}")
print(f"Model on: {next(model.parameters()).device}")
print(f"✓ Device mismatch avoided")
```

**Why move tensors to device?**

* Model and data must be on the same device (GPU or CPU)
* Moving to GPU happens once (at start), not every batch
* Prevents "RuntimeError: Expected all tensors to be on the same device"

***

### BLOCK 9: TRAINING LOOP WITH VALIDATION MONITORING

```python
# ============================================================
# BLOCK 9: TRAINING LOOP WITH VALIDATION MONITORING
# ============================================================
# Store losses for visualization and analysis
train_losses = []
val_losses = []

for epoch in range(num_epochs):
    # ==================== TRAINING PHASE ====================
    model.train()  # Enable Dropout and other training-specific behavior
    epoch_train_loss = 0  # Accumulate loss across all training batches
    
    # Iterate through training batches
    for batch_X, batch_y in train_loader:
        # Note: batch_X and batch_y are already on device (from Block 8B)
        
        # FORWARD PASS: compute predictions
        predictions = model(batch_X)  # Shape: (batch_size, 2)
        
        # COMPUTE LOSS
        loss = criterion(predictions, batch_y)  # CrossEntropyLoss expects logits
        
        # BACKWARD PASS: compute gradients
        optimizer.zero_grad()  # Clear old gradients (important!)
        loss.backward()        # Backpropagation: compute ∂loss/∂weight
        
        # UPDATE WEIGHTS
        optimizer.step()       # Update weights using gradient descent
        
        # Accumulate loss for averaging
        epoch_train_loss += loss.item()
    
    # Calculate average training loss for this epoch
    avg_train_loss = epoch_train_loss / len(train_loader)
    train_losses.append(avg_train_loss)
    
    # ==================== VALIDATION PHASE ====================
    # Purpose: Monitor for overfitting and tune hyperparameters
    model.eval()  # Disable Dropout (use all neurons for evaluation)
    epoch_val_loss = 0
    
    with torch.no_grad():  # Disable gradient calculation (not needed for validation)
        for batch_X, batch_y in val_loader:
            # Note: batch_X and batch_y are already on device (from Block 8B)
            
            # Forward pass (no backprop)
            predictions = model(batch_X)
            loss = criterion(predictions, batch_y)
            
            # Accumulate loss
            epoch_val_loss += loss.item()
    
    # Calculate average validation loss for this epoch
    avg_val_loss = epoch_val_loss / len(val_loader)
    val_losses.append(avg_val_loss)
    
    # Print progress every epoch
    print(f"Epoch [{epoch+1:2d}/{num_epochs}] | Train Loss: {avg_train_loss:.4f} | Val Loss: {avg_val_loss:.4f}")

print("\nTraining completed!")
```

**Training loop breakdown:**

1. **model.train()** – enables Dropout, BatchNorm uses running statistics
2. **optimizer.zero\_grad()** – clears old gradients (prevents accumulation)
3. **loss.backward()** – computes gradients via backpropagation
4. **optimizer.step()** – updates weights using gradients
5. **model.eval()** – disables Dropout, BatchNorm uses learned statistics
6. **torch.no\_grad()** – skips gradient computation (saves memory)

**Why validate each epoch?**

* Detect overfitting early (val loss diverges from train loss)
* Guide hyperparameter tuning (learning rate, dropout, etc.)
* Implement early stopping (stop if val loss plateaus)

***

### BLOCK 10: TEST SET INFERENCE

```python
# ============================================================
# BLOCK 10: RUN INFERENCE ON TEST SET
# ============================================================
# Set model to evaluation mode
model.eval()

test_predictions = []  # Store predicted class labels (0 or 1)
test_probabilities = []  # Store probability estimates (for ROC-AUC, etc.)

# Note: X_test_tensor is already on device (from Block 8B)

# Run inference on all test batches
with torch.no_grad():
    for batch_X in DataLoader(X_test_tensor, batch_size=32):
        # batch_X is already on device
        
        # Forward pass: get raw logits
        outputs = model(batch_X)  # Shape: (batch_size, 2)
        
        # Convert logits to probabilities using softmax
        # softmax normalizes outputs to sum to 1 (interpretable as probabilities)
        probs = functional.softmax(outputs, dim=1)  # Shape: (batch_size, 2)
        
        # Get predicted class using argmax
        # argmax(1) picks the neuron with highest probability
        preds = torch.argmax(probs, dim=1)  # Shape: (batch_size,)
        
        # Store results
        test_predictions.extend(preds.cpu().numpy())
        test_probabilities.extend(probs.cpu().numpy())

# Convert lists to numpy arrays for analysis
test_predictions = np.array(test_predictions)
test_probabilities = np.array(test_probabilities)

print("Test set inference complete!")
print(f"Test predictions shape: {test_predictions.shape}")
print(f"Test probabilities shape: {test_probabilities.shape}")
print(f"Class distribution in predictions: {np.bincount(test_predictions)}")
```

**Why softmax?** Converts raw logits (unbounded) to probabilities (0 to 1, sum to 1):

* Input: logits \[2.5, 1.2]
* Softmax output: \[0.73, 0.27] (neuron 0 has 73% confidence, neuron 1 has 27%)

**Why argmax?** Picks the class with highest probability:

* Probs \[0.73, 0.27] → argmax = 0 (predict class 0, benign)
* Probs \[0.31, 0.69] → argmax = 1 (predict class 1, malicious)

***

### BLOCK 11: COMPUTE ACCURACY

```python
# ============================================================
# BLOCK 11: COMPUTE ACCURACY METRIC
# ============================================================
# Accuracy = (correct predictions) / (total predictions)
# Simple but can be misleading if classes are imbalanced

accuracy_metric = Accuracy(task='binary')

test_accuracy = accuracy_metric(
    torch.tensor(test_predictions),
    torch.tensor(y_test)
)

# Convert to Python float for display
test_accuracy = test_accuracy.item()

print(f"\n{'='*60}")
print(f"TEST ACCURACY: {test_accuracy:.4f} ({test_accuracy*100:.2f}%)")
print(f"{'='*60}")
print(f"\nInterpretation: Model correctly classified {test_accuracy*100:.2f}% of test samples")
```

**Limitation of accuracy:** If dataset is 99% benign, 1% malicious, a model that predicts "benign" for everything gets 99% accuracy but catches 0% of actual threats. This is why we need additional metrics.

***

### BLOCK 12: COMPREHENSIVE EVALUATION METRICS

```python
# ============================================================
# BLOCK 12: COMPUTE ADVANCED EVALUATION METRICS
# ============================================================

# Precision, Recall, F1-Score
# average='binary' because we have exactly 2 classes
precision, recall, f1, _ = precision_recall_fscore_support(
    y_test, test_predictions, average='binary'
)

# Confusion Matrix
# Shows true positives, false positives, true negatives, false negatives
cm = confusion_matrix(y_test, test_predictions)

# ROC-AUC Score
# Measures ranking quality across all classification thresholds
# Uses probability estimates (not just hard predictions)
roc_auc = roc_auc_score(y_test, test_probabilities[:, 1])

print(f"\n{'='*60}")
print(f"DETAILED PERFORMANCE METRICS")
print(f"{'='*60}")
print(f"\nAccuracy:  {test_accuracy:.4f}")
print(f"  → Of all predictions, what % were correct?")

print(f"\nPrecision: {precision:.4f}")
print(f"  → Of all PREDICTED threats, what % were ACTUAL threats?")
print(f"  → Low precision = many false alarms (false positives)")

print(f"\nRecall:    {recall:.4f}")
print(f"  → Of all ACTUAL threats, what % did we CATCH?")
print(f"  → Low recall = missed threats (false negatives) ← DANGEROUS!")

print(f"\nF1-Score:  {f1:.4f}")
print(f"  → Harmonic mean of precision and recall")
print(f"  → Balances both metrics")

print(f"\nROC-AUC:   {roc_auc:.4f}")
print(f"  → Measures ranking quality across all thresholds")
print(f"  → 1.0 = perfect classifier, 0.5 = random guessing")

print(f"\n{'='*60}")
print(f"CONFUSION MATRIX")
print(f"{'='*60}")
print(f"                    Predicted Benign | Predicted Malicious")
print(f"Actually Benign:          {cm[0,0]:4d}        |       {cm[0,1]:4d}")
print(f"Actually Malicious:       {cm[1,0]:4d}        |       {cm[1,1]:4d}")
print(f"\nTrue Negatives (TN):  {cm[0,0]} (correctly identified benign)")
print(f"False Positives (FP): {cm[0,1]} (falsely flagged as malicious)")
print(f"False Negatives (FN): {cm[1,0]} (missed actual threats) ← CRITICAL")
print(f"True Positives (TP):  {cm[1,1]} (correctly detected threats)")
```

**For cybersecurity, what matters most?**

1. **High Recall** – miss as few real threats as possible (low false negatives)
2. **Acceptable Precision** – don't overwhelm analysts with false alarms
3. **F1 balances both** – if you optimize recall, precision drops and vice versa

In practice, security teams tolerate high false positives (manual review) to catch all real threats. Better to alert on 100 benign events than miss 1 real attack.

***

### BLOCK 13: VISUALIZATION

```python
# ============================================================
# BLOCK 13: PLOT TRAINING AND VALIDATION LOSS
# ============================================================
plt.figure(figsize=(12, 6))

# Plot training and validation loss curves
plt.plot(train_losses, label='Training Loss', linewidth=2.5, color='#2E86AB')
plt.plot(val_losses, label='Validation Loss', linewidth=2.5, color='#A23B72')

# Formatting
plt.xlabel('Epoch', fontsize=12, fontweight='bold')
plt.ylabel('Loss (CrossEntropyLoss)', fontsize=12, fontweight='bold')
plt.title('Training vs Validation Loss Over 10 Epochs', fontsize=14, fontweight='bold')
plt.legend(fontsize=11, loc='upper right')
plt.grid(True, alpha=0.3, linestyle='--')

# Add annotations
min_val_loss = min(val_losses)
min_val_epoch = val_losses.index(min_val_loss) + 1
plt.axvline(x=min_val_epoch, color='red', linestyle=':', alpha=0.5, label=f'Best Val Loss (Epoch {min_val_epoch})')

plt.tight_layout()
plt.show()

print(f"Loss visualization complete!")
print(f"Best validation loss: {min_val_loss:.4f} at epoch {min_val_epoch}")

# Interpretation
if val_losses[-1] > val_losses[-5]:
    print(f"\n⚠ Validation loss is increasing → possible overfitting")
    print(f"  Consider: increase dropout, add regularization")
else:
    print(f"\n✓ Validation loss is stable → good generalization")
```

**What to look for in the plot:**

* **Converging together** – good fit (train and val loss decrease)
* **Val diverges upward** – overfitting (model memorizes training data)
* **Both plateau** – reached convergence (more epochs won't help)
* **Noisy train loss** – expected due to mini-batch gradient noise
* **Smooth val loss** – computed on full validation set

***

## Solution Comparison Table

| Aspect                | DataCamp Official              | Optimized Solution                                | Impact                                                    |
| --------------------- | ------------------------------ | ------------------------------------------------- | --------------------------------------------------------- |
| **Output Layer**      | 1 neuron + Sigmoid             | 2 neurons (logits)                                | More expressive; allows class-specific learning           |
| **Loss Function**     | CrossEntropyLoss (wrong setup) | CrossEntropyLoss (correct)                        | Eliminates shape mismatch; proper multi-class handling    |
| **Optimizer**         | SGD (lr=1e-3, weight\_decay)   | Adam (lr=0.001)                                   | Faster convergence; adaptive per-parameter learning rates |
| **Training**          | Full-batch (entire dataset)    | Mini-batch (batch\_size=32)                       | Reduced memory; better convergence; enables shuffling     |
| **Validation**        | No monitoring                  | Per-epoch monitoring                              | Detect overfitting; guide hyperparameter tuning           |
| **Metrics**           | Accuracy only                  | Accuracy + Precision + Recall + F1 + ROC-AUC + CM | Comprehensive evaluation; reveal class imbalance issues   |
| **Early Stopping**    | None                           | Can be added                                      | Prevent overfitting; save computation                     |
| **Regularization**    | None (SGD weight decay)        | Dropout(0.3)                                      | Prevent co-adaptation; learn robust features              |
| **Code Organization** | Sequential/simple              | Class-based architecture                          | More maintainable; easier to modify/extend                |
| **Production-Ready**  | No (teaching skeleton)         | Yes (enterprise practices)                        | Deploy-ready with monitoring and extensibility            |

***

## Project Summary

### Core Concepts

**1. Deep Learning for Security**

* Neural networks outperform rule-based detection by learning complex patterns
* Can scale to millions of logs per day
* Must be retrained periodically as threats evolve
* Explainability is critical for security teams ("why did you flag this?")

**2. Data Preparation**

* Separate training/validation/test to prevent data leakage
* Fit preprocessing (scaler, encoder) only on training data
* Feature scaling dramatically improves convergence
* Class imbalance must be addressed (over/under-sampling, weighted loss)

**3. Model Architecture Design**

* Hidden layer sizes: input → 128 → 64 → output (gradual compression)
* ReLU activation enables learning non-linear patterns
* Dropout regularization prevents overfitting
* 2 output neurons (not 1) for binary classification is more expressive

**4. Training vs Production**

* **Training mode:** Dropout active, updates batch statistics (BatchNorm)
* **Evaluation mode:** Dropout disabled, uses learned statistics
* Critical to toggle with model.train() / model.eval()

**5. Loss Functions Matter**

* CrossEntropyLoss: multi-class, expects logits + class indices
* BCELoss: binary with sigmoid output
* Wrong loss function leads to shape mismatches or incorrect learning

**6. Optimization**

* Adam converges faster than SGD for most problems
* Batch training (32 samples) balances memory and convergence
* Mini-batches add beneficial gradient noise
* Shuffling prevents the model from memorizing data order

**7. Validation is Essential**

* Monitor validation loss during training (not just at the end)
* Detect overfitting when val loss diverges from train loss
* Enables early stopping and hyperparameter tuning
* Test set must remain unseen until final evaluation

**8. Comprehensive Evaluation**

* Accuracy alone is misleading (especially with class imbalance)
* Precision: how many predicted threats are real? (minimize false alarms)
* Recall: how many real threats did we catch? (minimize missed attacks)
* F1: balance both metrics
* ROC-AUC: ranking quality across all thresholds
* Confusion matrix: reveals where the model fails

**9. Security-Specific Insights**

* High recall is critical (missed threat = breach)
* False positives are tolerable (analysts investigate)
* Explainability matters (analysts need to trust the model)
* Regular retraining required (threat landscape changes)
* Anomaly detection baseline: any detected threat must be investigated

**10. Engineering Best Practices**

* Use DataLoaders for efficient batch processing
* Move models to GPU when available (.to(device))
* Disable gradients during evaluation (torch.no\_grad()) to save memory
* Store metrics for post-hoc analysis
* Visualize loss curves to debug training
* Class-based architecture scales better than Sequential

***

### Practical Takeaways

**From DataCamp's Official Solution:**

* Good starting point for learning basics
* Highlights intentional "try BCELoss()" and "try Adam" hints
* Teaches core PyTorch workflow without overcomplication

**From the Optimized Solution:**

* Production-grade practices (batching, validation, comprehensive metrics)
* Demonstrates how to avoid common pitfalls
* Scales to real datasets (memory-efficient, GPU-compatible)
* Enterprise-ready code structure

**Next Steps:**

1. Experiment with hyperparameters (learning rate, batch size, hidden layer sizes)
2. Add early stopping when validation loss plateaus
3. Address class imbalance (if sus\_label distribution is skewed)
4. Deploy model as REST API for real-time predictions
5. Monitor performance in production (retrain if accuracy drops)
6. Add explainability techniques (SHAP, attention visualization)

***

### Key Differences Summary

The official solution is a **teaching skeleton** (intentionally minimal). The optimized solution is **production-grade** because it:

* Fixes architectural mismatches (2 outputs, correct loss function)
* Implements best practices (batching, validation monitoring, comprehensive metrics)
* Handles real-world constraints (GPU usage, memory efficiency, scalability)
* Provides visibility (loss curves, detailed metrics) for debugging
* Uses industry-standard tools (Adam optimizer, Dropout, DataLoaders)

For production/deep research, use the optimized approach. For learning quickly, DataCamp's official is fine but incomplete without the improvements suggested in its comments.

***
