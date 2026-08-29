## 1. Overview & Learning Roadmap

* **Course Structure:** Overview of the end-to-end journey from raw data to a deployed model making predictions.

## 2. History & Evolution of AI & Machine Learning

* **Early Theoretical Foundations:** Alan Turing's Turing Test (1950), the Dartmouth Workshop (1956), and Arthur Samuel’s Checkers program.
* **Rule-Based & Traditional AI:** Logic programming, manual rules, and early successes like IBM’s Deep Blue beating Garry Kasparov in 1997.
* **Limits of Rule-Based AI:** Why messy real-world problems (speech recognition, computer vision, translation, spam) cannot be hardcoded with manual `if-else` rules.
* **Evolution of Deep Learning:** Early inspiration from the human brain, backpropagation (1980s), the AlexNet ImageNet breakthrough (2012), AlphaGo (2016), and the Transformer architecture (2017).

## 3. What is Machine Learning? 

* **Core Definition:** Learning patterns from past data instead of executing explicit human-written code instructions.
* **Traditional Programming vs. Machine Learning:** Comparing simple threshold rules (e.g., age checks) to dynamic pattern detection (e.g., email spam filtering).
* **Real-World Examples:** Arthur Samuel's Checkers program and the 2006 Netflix Prize recommendation system.
* **The Fundamental Pipeline:** $\text{Input Data} \rightarrow \text{Model (Math \& Parameters)} \rightarrow \text{Predictions}$.
* **Model Spectrum:** Simple models (linear models, decision trees) to complex models (neural networks, large language models).

## 4. Basic Ingredients: Data, Features, and Labels

* **Data & Datasets:** Raw material, rows as individual samples/examples (e.g., Kaggle’s Ames Iowa House Prices dataset).
* **Features:** Input attributes/columns used by the model (numerical vs. categorical features).
* **Labels / Targets:** Ground-truth answers to predict (e.g., final sale price).
* **Feature Engineering:** Creating or transforming inputs (e.g., converting "Year Built" to "House Age").
* **Data Preparation & Quality:** Data cleaning (handling missing/duplicate rows), distribution shifts, and avoiding bias ("garbage in, garbage out").
* **Hands-on Lab 1:** Data cleaning, dropping missing/duplicate rows, feature engineering, and encoding categorical variables.

## 5. Training vs. Inference

* **Training Phase:** The learning phase where models see features alongside known labels and adjust internal values based on error feedback.
* **Inference Phase:** The production usage phase where a trained model generates predictions on new, unseen data without ground-truth labels.
* **Common Misconception:** Clarifying that models during standard inference do not continually retrain on individual prompt queries.

## 6. Models, Parameters, and Weight

* **Model as a Mathematical Function:** Mapping inputs to outputs ($y = wx + b$).
* **Weights ($w$):** Feature importance multipliers that scale the influence of an input feature.
* **Bias ($b$):** Learned baseline offset value shifting the overall prediction curve.
* **Parameter Scaling:** From simple linear models (~80 parameters) to modern LLMs like GPT-3 (175 billion) and LLaMA 2 (7B–70B parameters).

## 7. Loss Functions

* **Measuring Model Error:** Turning prediction mistakes into a single precise scalar value.
* **Raw Error Limitations:** Why simple prediction differences ($y_{pred} - y_{actual}$) cancel out and mislead training.
* **Squared Error & Mean Squared Error (MSE):** Penalizing larger mistakes more heavily and averaging error across datasets.

## 8. Gradient Descent & Optimization

* **Parameter Updates:** Adjusting weights and biases systematically to minimize loss.
* **The Hill & Ball Analogy:** Rolling downhill towards the lowest point on the loss surface.
* **Gradient & Learning Rate ($\alpha$):** Determining update direction and step size (consequences of too small vs. too large step sizes).
* **Batches & Epochs:** Iterative optimization using data batches and full pass epochs over the dataset.
* **Hands-on Lab 2:** Manual parameter tuning and optimization.

## 9. Generalization: Train, Validation, and Test Sets

* **Goal of Machine Learning:** Generalizing to unseen data rather than memorizing training data.
* **Overfitting:** When a model learns noise and specific quirks of training data, causing failure on new inputs.
* **Dataset Splitting:**
* *Training Set:* Updating model weights (~70–80%).
* *Validation Set:* Tuning hyperparameters and detecting overfitting (~10–15%).
* *Test Set:* Final unbiased evaluation on held-out data (~10–15%).


* **Data Leakage:** Unintentionally exposing test or validation details during training.
* **Hands-on Lab 3:** Splitting datasets and controlling decision tree depth to prevent overfitting.

## 10. Supervised vs. Unsupervised Learning

* **Supervised Learning:** Learning from labeled data with known targets (e.g., MNIST digit recognition, ImageNet object classification).
* **Unsupervised Learning:** Finding structure or groups in unlabeled data (e.g., customer segmentation, market clustering).
* **Key Distinction:** Supervised asks *"Can we predict a known answer?"*; Unsupervised asks *"Can we discover hidden structures?"*

## 11. System Architecture: The Inner and Outer Loops

* **Inner Training Loop:** Automated forward pass, loss calculation, backpropagation, and weight updates.
* **Outer Experimentation Loop:** Human-in-the-loop decisions involving feature selection, model architecture choices, hyperparameter tuning, and validation checks.

## 12. Classification vs. Regression

* **Regression:** Predicting continuous numerical values (e.g., house prices, delivery duration, revenue).
* **Classification:** Predicting discrete categories/classes (e.g., spam vs. non-spam, digits 0–9).
* **Output Differences:** Fitting continuous lines/curves vs. drawing decision boundaries and returning class probabilities.

## 13. Neural Networks & Deep Learning 

* **Artificial Neurons:** Inputs, weights, biases, and activation functions ($w_1 x_1 + w_2 x_2 + b$).
* **Network Layers:** Input layer, hidden layer(s), and output layer.
* **Activation Functions:** Introducing non-linearity into networks (e.g., ReLU).
* **Forward Pass & Backpropagation:** Passing inputs forward to obtain outputs and propagating error gradients backward to update weights.
* **Automated Feature Extraction:** How deep networks automatically extract hierarchical features (edges $\rightarrow$ shapes $\rightarrow$ complex objects).

## 14. Large Language Models (LLMs) & How GPT Works

* **Pre-training:**
* *Tokenization & Embeddings:* Converting text chunks into numerical vector spaces.
* *Training Objective:* Predicting the next token across massive text datasets.


* **Post-training:**
* *Instruction Fine-Tuning (SFT):* Training pre-trained models on instruction-response pairs to behave as helpful assistants.
* *RLHF (Reinforcement Learning from Human Feedback):* Aligning response preferences for safety, clarity, and usefulness.



## 15. Model Evaluation Metrics

* **Regression Metrics:** Mean Absolute Error (MAE), Mean Squared Error (MSE), Root Mean Squared Error (RMSE).
* **Classification Metrics:** Accuracy, Precision, Recall, and F1-Score.
* **Generative AI & LLM Evaluation:** Benchmarks, human preference scoring, factuality tests, and coding benchmarks.
* **Hands-on Lab 4:** Training a neural network on MNIST digit recognition and evaluating performance.

## 16. Modern Approaches Shaping AI Today 
* **Transformers & Attention:** The "Attention Is All You Need" paper (2017) and contextual self-attention mechanisms.
* **Multimodal AI:** Jointly processing text, image, audio, and video modalities (e.g., OpenAI CLIP, DALL-E).
* **AI Agents & Tool Integration:** Extending LLMs beyond standalone chat by connecting web search, code interpreters, database APIs, and external software.

## 17. Summary & Core Takeaways

* **Conclusion:** Recapping how modern AI advances build directly on fundamental machine learning principles.
