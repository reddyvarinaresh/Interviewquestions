# ML & Deep Learning — Interview Questions Only
> Questions verified from: Glassdoor, LinkedIn, Blind, Reddit r/MachineLearning, Kaggle discussions
> Companies: Deloitte, Accenture, Optum, Carelon, EPAM, Cognizant, TCS, Wipro, Infosys, HCL, Capgemini, Persistent, Brillio, LTIMindtree
> Format: Questions only — no answers. Group study / interview prep checklist.
> 🔴 = Very Frequently Asked | 🟡 = Frequently Asked | 🟢 = Asked at Senior / Architect Level

---

## SECTION 1: ML FUNDAMENTALS

1. 🔴 What is the bias-variance tradeoff? How does it affect model performance?
2. 🔴 What is overfitting? How do you detect and prevent it?
3. 🔴 What is underfitting? How do you fix it?
4. 🔴 What is the difference between a parametric and non-parametric model?
5. 🔴 What is regularization? Why is it needed?
6. 🔴 What is the difference between L1 and L2 regularization?
7. 🔴 When would you use L1 over L2 regularization?
8. 🔴 What is Elastic Net regularization?
9. 🔴 What is cross-validation? Why do we need it?
10. 🔴 Explain K-Fold cross-validation.
11. 🔴 What is Stratified K-Fold? When do you use it instead of K-Fold?
12. 🔴 What is a train/validation/test split? Why three sets?
13. 🔴 What is data leakage? Give an example.
14. 🔴 What is the curse of dimensionality?
15. 🔴 What is the difference between correlation and causation?
16. 🟡 What is Maximum Likelihood Estimation (MLE)?
17. 🟡 What is MAP estimation? How does it differ from MLE?
18. 🟡 What is a confusion matrix? What does each cell represent?
19. 🟡 What is the difference between Type I and Type II errors?
20. 🟡 What is statistical significance? What is a p-value?
21. 🟡 What is the Central Limit Theorem?
22. 🟡 What is the difference between frequentist and Bayesian statistics?
23. 🟡 What is Bayes' theorem? How is it used in ML?
24. 🟡 What is the No Free Lunch theorem?
25. 🟡 What is Occam's Razor and how does it relate to ML model selection?
26. 🟢 What is the VC dimension?
27. 🟢 Explain the PAC learning framework.
28. 🟢 What is the difference between online learning and batch learning?
29. 🟢 What is a generalization bound?
30. 🟢 What is covariate shift? How do you handle it?

---

## SECTION 2: SUPERVISED LEARNING — REGRESSION

31. 🔴 How does linear regression work?
32. 🔴 What are the assumptions of linear regression?
33. 🔴 What is multicollinearity? How do you detect and fix it?
34. 🔴 What is the difference between simple and multiple linear regression?
35. 🔴 What is R-squared? What are its limitations?
36. 🔴 What is adjusted R-squared? Why is it better than R-squared?
37. 🔴 What is the difference between MAE, MSE, RMSE, and MAPE?
38. 🔴 When would you prefer MAE over RMSE?
39. 🔴 What is Ridge regression? How is it different from OLS?
40. 🔴 What is Lasso regression? What is its feature selection property?
41. 🟡 How do you interpret the coefficients of a linear regression model?
42. 🟡 What is heteroscedasticity? How do you detect it?
43. 🟡 What is polynomial regression? When do you use it?
44. 🟡 What is the difference between interpolation and extrapolation?
45. 🟡 What is stepwise regression?
46. 🟡 What is VIF (Variance Inflation Factor)?
47. 🟡 How do you check for normality of residuals?
48. 🟢 What is weighted least squares regression?
49. 🟢 What is quantile regression?
50. 🟢 What is Huber loss? When is it preferred over MSE?

---

## SECTION 3: SUPERVISED LEARNING — CLASSIFICATION

51. 🔴 How does logistic regression work?
52. 🔴 What is the sigmoid function? Why is it used in logistic regression?
53. 🔴 What is log loss (cross-entropy)? How is it computed?
54. 🔴 How do you interpret the coefficients of logistic regression?
55. 🔴 What is a decision boundary?
56. 🔴 How does a Decision Tree split nodes? What is Gini impurity?
57. 🔴 What is information gain? How is it related to entropy?
58. 🔴 How do you prevent a decision tree from overfitting?
59. 🔴 What is pruning in decision trees?
60. 🔴 How does Random Forest work?
61. 🔴 What is bagging? How does it differ from boosting?
62. 🔴 Why does Random Forest use a random subset of features at each split?
63. 🔴 What is out-of-bag (OOB) error in Random Forest?
64. 🔴 How does Gradient Boosting work?
65. 🔴 What is XGBoost? How is it different from standard gradient boosting?
66. 🔴 What are the key hyperparameters of XGBoost?
67. 🔴 What is LightGBM? How does it differ from XGBoost?
68. 🔴 What is CatBoost? When should you use it?
69. 🔴 What is the difference between Random Forest and XGBoost? When do you use which?
70. 🔴 How does Support Vector Machine (SVM) work?
71. 🔴 What is a support vector?
72. 🔴 What is the kernel trick in SVM?
73. 🔴 What is the C parameter in SVM? What happens if it is very large or small?
74. 🔴 What is the RBF kernel? What does gamma control?
75. 🔴 Why does SVM require feature scaling?
76. 🔴 How does K-Nearest Neighbors (KNN) work?
77. 🔴 How do you choose K in KNN?
78. 🔴 What are the pros and cons of KNN?
79. 🔴 How does Naive Bayes work?
80. 🔴 Why is Naive Bayes called "naive"? When does it still perform well?
81. 🟡 What is the difference between GaussianNB, MultinomialNB, and BernoulliNB?
82. 🟡 What is AdaBoost? How does it differ from gradient boosting?
83. 🟡 What is stacking (stacked generalization)?
84. 🟡 What is a soft voting vs hard voting classifier?
85. 🟡 What is the difference between one-vs-rest and one-vs-one for multi-class classification?
86. 🟡 What is calibration of a classifier? How do you calibrate probabilities?
87. 🟡 What is isotonic regression used for in ML?
88. 🟡 How do you extend binary classifiers to multi-class problems?
89. 🟢 What is label smoothing?
90. 🟢 What is ordinal classification?

---

## SECTION 4: MODEL EVALUATION & METRICS

91. 🔴 What is precision? When do you prioritize it?
92. 🔴 What is recall? When do you prioritize it?
93. 🔴 What is the F1 score? When is it more informative than accuracy?
94. 🔴 What is F-beta score? When would you use beta > 1 vs beta < 1?
95. 🔴 What is accuracy? Why is it a poor metric for imbalanced datasets?
96. 🔴 What is AUC-ROC? What does a score of 0.5 mean?
97. 🔴 What is the ROC curve? How is it plotted?
98. 🔴 What is the PR curve? When is it preferred over ROC?
99. 🔴 What is the difference between micro, macro, and weighted averaging for multi-class metrics?
100. 🔴 How do you handle class imbalance?
101. 🔴 What is SMOTE? How does it work?
102. 🔴 What is the difference between oversampling and undersampling?
103. 🔴 How do you choose the decision threshold for a classifier?
104. 🟡 What is the Matthews Correlation Coefficient (MCC)?
105. 🟡 What is Cohen's Kappa?
106. 🟡 What is log loss? How is it different from accuracy?
107. 🟡 What is Brier score?
108. 🟡 What is the difference between sensitivity and specificity?
109. 🟡 What is the Kolmogorov-Smirnov (KS) statistic in model evaluation?
110. 🟡 What is a lift curve / gain chart? How is it used?
111. 🟡 What is the difference between population stability index (PSI) and KS?
112. 🟢 What is Expected Calibration Error (ECE)?
113. 🟢 What is reliability diagram / calibration curve?
114. 🟢 How do you evaluate a ranking model (NDCG, MRR, Hit@K)?
115. 🟢 What is perplexity and when is it used?

---

## SECTION 5: FEATURE ENGINEERING & DATA PREPROCESSING

116. 🔴 What is feature engineering? Why is it important?
117. 🔴 How do you handle missing values in a dataset?
118. 🔴 What is the difference between imputation strategies (mean, median, mode, KNN)?
119. 🔴 When would you use median imputation over mean imputation?
120. 🔴 What is one-hot encoding? When do you use it?
121. 🔴 What is label encoding? When should you NOT use it?
122. 🔴 What is target encoding? What is its risk?
123. 🔴 What is standardization vs normalization? When do you use each?
124. 🔴 What is StandardScaler vs MinMaxScaler vs RobustScaler?
125. 🔴 What is an outlier? How do you detect and handle outliers?
126. 🔴 What is the IQR method for outlier detection?
127. 🔴 What is a Z-score? How is it used for outlier detection?
128. 🔴 What is feature selection? What are the main approaches?
129. 🔴 What is the difference between filter, wrapper, and embedded feature selection methods?
130. 🔴 What is Recursive Feature Elimination (RFE)?
131. 🔴 How do you handle high-cardinality categorical features?
132. 🟡 What is binning/bucketing? When is it useful?
133. 🟡 What is a log transformation? When do you apply it?
134. 🟡 What is feature hashing (hashing trick)?
135. 🟡 What is a feature store? Why is it important in ML pipelines?
136. 🟡 What is data augmentation? Give examples for tabular and image data.
137. 🟡 What is the difference between feature importance from Random Forest vs SHAP?
138. 🟡 How do you handle time-series features in a tabular ML model?
139. 🟡 What is point-in-time correctness in feature engineering?
140. 🟡 What is the difference between training distribution and serving distribution?
141. 🟢 What is weight of evidence (WoE) encoding?
142. 🟢 What is information value (IV) for feature selection?
143. 🟢 What is feature interaction? How do you create interaction features?
144. 🟢 What is embeddings-based feature encoding?

---

## SECTION 6: UNSUPERVISED LEARNING

145. 🔴 What is clustering? What are the main types?
146. 🔴 How does K-Means clustering work?
147. 🔴 How do you choose the number of clusters K?
148. 🔴 What is the elbow method?
149. 🔴 What is silhouette score?
150. 🔴 What are the limitations of K-Means?
151. 🔴 What is DBSCAN? How is it different from K-Means?
152. 🔴 What are the epsilon and min_samples parameters in DBSCAN?
153. 🔴 How does DBSCAN handle outliers?
154. 🔴 What is PCA (Principal Component Analysis)? What does it do?
155. 🔴 How do you choose the number of principal components?
156. 🔴 Why must you scale features before applying PCA?
157. 🟡 What is t-SNE? How is it different from PCA?
158. 🟡 What is UMAP? When is it preferred over t-SNE?
159. 🟡 What is an autoencoder? How is it used for dimensionality reduction?
160. 🟡 What is hierarchical clustering? What are its types?
161. 🟡 What is the dendrogram in hierarchical clustering?
162. 🟡 What is Gaussian Mixture Model (GMM)?
163. 🟡 How is GMM different from K-Means?
164. 🟡 What is the EM algorithm?
165. 🟡 What is Latent Dirichlet Allocation (LDA)?
166. 🟢 What is anomaly detection? What techniques are used?
167. 🟢 What is Isolation Forest? How does it detect anomalies?
168. 🟢 What is One-Class SVM?
169. 🟢 What is self-supervised learning?
170. 🟢 What is contrastive learning?

---

## SECTION 7: DEEP LEARNING FUNDAMENTALS

171. 🔴 What is a neural network? What are its key components?
172. 🔴 What is backpropagation? How does it work?
173. 🔴 What is a loss function? Give examples for classification and regression.
174. 🔴 What is gradient descent?
175. 🔴 What is the difference between batch gradient descent, mini-batch, and stochastic gradient descent?
176. 🔴 What is the vanishing gradient problem? What causes it?
177. 🔴 What is the exploding gradient problem? How do you fix it?
178. 🔴 What is gradient clipping?
179. 🔴 What is the ReLU activation function? Why is it preferred over sigmoid for hidden layers?
180. 🔴 What is the dying ReLU problem? How do you fix it?
181. 🔴 What is the difference between sigmoid, tanh, ReLU, Leaky ReLU, and GELU?
182. 🔴 What is Dropout? How does it work during training vs inference?
183. 🔴 What is Batch Normalization? What problem does it solve?
184. 🔴 What is Layer Normalization? How is it different from Batch Normalization?
185. 🔴 What is weight initialization? Why does it matter?
186. 🔴 What is He initialization vs Xavier/Glorot initialization?
187. 🔴 What is the difference between Adam and SGD optimizers?
188. 🔴 What is learning rate? What happens if it is too high or too low?
189. 🔴 What is learning rate scheduling? What are common strategies?
190. 🔴 What is overfitting in deep learning? How do you handle it?
191. 🟡 What is weight decay? How does it relate to L2 regularization?
192. 🟡 What is early stopping?
193. 🟡 What is the difference between a deep and shallow neural network?
194. 🟡 What is the universal approximation theorem?
195. 🟡 What is AdamW? How is it different from Adam?
196. 🟡 What is mixed precision training?
197. 🟡 What is gradient accumulation? When is it used?
198. 🟡 What is a residual connection (skip connection)?
199. 🟡 What is the dying neuron problem?
200. 🟡 What is the difference between a model parameter and a hyperparameter?
201. 🟢 What is second-order optimization?
202. 🟢 What is the Fisher information matrix?
203. 🟢 What is knowledge distillation?
204. 🟢 What is model pruning?
205. 🟢 What is quantization in deep learning?

---

## SECTION 8: CONVOLUTIONAL NEURAL NETWORKS (CNNs)

206. 🔴 What is a convolutional layer? What is a filter/kernel?
207. 🔴 What is padding in CNNs? What is the difference between "same" and "valid" padding?
208. 🔴 What is stride in a convolutional layer?
209. 🔴 What is pooling? What is the difference between max pooling and average pooling?
210. 🔴 What is the role of fully connected layers in a CNN?
211. 🔴 Why are CNNs better than fully connected networks for image data?
212. 🔴 What is parameter sharing in CNNs?
213. 🔴 How do you calculate the output size of a convolutional layer?
214. 🟡 What is a 1×1 convolution? What is it used for?
215. 🟡 What is a depthwise separable convolution?
216. 🟡 What is feature map?
217. 🟡 What is receptive field in a CNN?
218. 🟡 What is global average pooling?
219. 🟡 What is the VGG architecture? What is its key design principle?
220. 🟡 What is ResNet? What problem did it solve?
221. 🟡 What is the residual/skip connection in ResNet?
222. 🟡 What is Inception/GoogLeNet architecture?
223. 🟡 What is EfficientNet? What is compound scaling?
224. 🟡 What is the Vision Transformer (ViT)?
225. 🟢 What is dilated/atrous convolution?
226. 🟢 What is a transposed convolution (deconvolution)?
227. 🟢 What is the difference between semantic segmentation and instance segmentation?
228. 🟢 What is object detection? How do YOLO and Faster R-CNN work?
229. 🟢 What is image augmentation? Give examples.
230. 🟢 What is style transfer?

---

## SECTION 9: RECURRENT NETWORKS & SEQUENCE MODELS

231. 🔴 What is a Recurrent Neural Network (RNN)?
232. 🔴 What is the vanishing gradient problem in RNNs?
233. 🔴 What is LSTM? What are its three gates?
234. 🔴 What is the cell state in an LSTM?
235. 🔴 What is GRU? How is it different from LSTM?
236. 🔴 When would you use GRU over LSTM?
237. 🔴 What is a bidirectional RNN? When do you use it?
238. 🟡 What is sequence-to-sequence (seq2seq) model?
239. 🟡 What is the encoder-decoder architecture?
240. 🟡 What is the attention mechanism in seq2seq?
241. 🟡 What is teacher forcing in sequence models?
242. 🟡 What is beam search?
243. 🟡 What is BLEU score?
244. 🟡 What is CTC (Connectionist Temporal Classification) loss?
245. 🟡 What is time series forecasting? How do LSTM models handle it?
246. 🟢 What is WaveNet?
247. 🟢 What is a causal convolution?

---

## SECTION 10: TRANSFORMERS & ATTENTION

248. 🔴 What is the attention mechanism? How does it work?
249. 🔴 What is self-attention?
250. 🔴 What is multi-head attention?
251. 🔴 What is scaled dot-product attention?
252. 🔴 What is the Transformer architecture? What are its main components?
253. 🔴 What is positional encoding? Why is it needed?
254. 🔴 What is the difference between encoder-only, decoder-only, and encoder-decoder Transformers?
255. 🔴 What is BERT? How is it trained?
256. 🔴 What is masked language modeling (MLM)?
257. 🔴 What is GPT? How is it different from BERT?
258. 🔴 What is causal (autoregressive) language modeling?
259. 🔴 What is fine-tuning a pre-trained language model?
260. 🔴 What is the difference between pre-training and fine-tuning?
261. 🟡 What is T5? How does it unify NLP tasks?
262. 🟡 What is RoBERTa? How does it differ from BERT?
263. 🟡 What is DistilBERT?
264. 🟡 What is cross-attention?
265. 🟡 What is the Feed-Forward Network (FFN) in a Transformer block?
266. 🟡 What is Add & Norm (residual + layer norm) in a Transformer?
267. 🟡 What is the context window / max sequence length?
268. 🟡 What is tokenization? What is a subword tokenizer (BPE, WordPiece, SentencePiece)?
269. 🟡 What is the difference between token classification and sentence classification?
270. 🟢 What is Flash Attention?
271. 🟢 What is Rotary Positional Embedding (RoPE)?
272. 🟢 What is ALiBi positional encoding?
273. 🟢 What is the Mixture of Experts (MoE) architecture?
274. 🟢 What is Sparse Attention?
275. 🟢 What is the sliding window attention in Longformer?

---

## SECTION 11: NLP — NATURAL LANGUAGE PROCESSING

276. 🔴 What is tokenization? Types of tokenizers?
277. 🔴 What is stemming vs lemmatization?
278. 🔴 What is TF-IDF? How is it calculated?
279. 🔴 What is Bag of Words (BoW)?
280. 🔴 What is n-gram? How is it used?
281. 🔴 What is Word2Vec? How does skip-gram differ from CBOW?
282. 🔴 What is GloVe? How does it differ from Word2Vec?
283. 🔴 What is FastText? What is its advantage for OOV words?
284. 🔴 What is the difference between static and contextual embeddings?
285. 🔴 What is sentiment analysis? How do you approach it?
286. 🔴 What is Named Entity Recognition (NER)?
287. 🔴 What is text classification? How do you build a text classifier?
288. 🟡 What is coreference resolution?
289. 🟡 What is dependency parsing?
290. 🟡 What is part-of-speech (POS) tagging?
291. 🟡 What is a language model? How is perplexity used to evaluate it?
292. 🟡 What is zero-shot, one-shot, and few-shot learning?
293. 🟡 What is prompt engineering?
294. 🟡 What is in-context learning?
295. 🟡 What is instruction fine-tuning?
296. 🟡 What is RLHF (Reinforcement Learning from Human Feedback)?
297. 🟡 What is chain-of-thought prompting?
298. 🟡 What is retrieval-augmented generation (RAG)?
299. 🟢 What is Constitutional AI?
300. 🟢 What is DPO (Direct Preference Optimization)?

---

## SECTION 12: TRANSFER LEARNING & FINE-TUNING

301. 🔴 What is transfer learning?
302. 🔴 What is the difference between feature extraction and fine-tuning in transfer learning?
303. 🔴 When should you freeze layers during fine-tuning?
304. 🔴 What is domain adaptation?
305. 🔴 What is fine-tuning a BERT model for a downstream task?
306. 🟡 What is LoRA (Low-Rank Adaptation)?
307. 🟡 What is QLoRA?
308. 🟡 What is PEFT (Parameter-Efficient Fine-Tuning)?
309. 🟡 What is adapter-based fine-tuning?
310. 🟡 What is catastrophic forgetting?
311. 🟡 What is continual learning / lifelong learning?
312. 🟡 Why do you use a smaller learning rate when fine-tuning pre-trained models?
313. 🟡 What is the difference between full fine-tuning and PEFT methods?
314. 🟢 What is prefix tuning?
315. 🟢 What is prompt tuning?
316. 🟢 What is model merging / model soups?

---

## SECTION 13: GENERATIVE MODELS

317. 🔴 What is a Generative Adversarial Network (GAN)?
318. 🔴 What are the Generator and Discriminator roles in a GAN?
319. 🔴 What is mode collapse in GANs?
320. 🔴 What is a Variational Autoencoder (VAE)?
321. 🔴 What is the reparameterization trick in VAEs?
322. 🔴 What is the ELBO (Evidence Lower Bound) in VAE?
323. 🔴 What is the difference between a GAN and a VAE?
324. 🟡 What is a conditional GAN (cGAN)?
325. 🟡 What is Wasserstein GAN (WGAN)?
326. 🟡 What is a diffusion model?
327. 🟡 What is the forward and reverse process in diffusion models?
328. 🟡 What is DDPM (Denoising Diffusion Probabilistic Model)?
329. 🟡 What is stable diffusion?
330. 🟡 What is a normalizing flow?
331. 🟢 What is score matching?
332. 🟢 What is classifier-free guidance in diffusion models?
333. 🟢 What is ControlNet?

---

## SECTION 14: REINFORCEMENT LEARNING

334. 🔴 What is reinforcement learning?
335. 🔴 What is an agent, environment, state, action, and reward?
336. 🔴 What is a policy in RL?
337. 🔴 What is the difference between model-based and model-free RL?
338. 🔴 What is Q-learning?
339. 🔴 What is the Bellman equation?
340. 🔴 What is the exploration vs exploitation tradeoff?
341. 🔴 What is epsilon-greedy strategy?
342. 🟡 What is Deep Q-Network (DQN)?
343. 🟡 What is experience replay?
344. 🟡 What is a target network in DQN?
345. 🟡 What is Policy Gradient?
346. 🟡 What is the REINFORCE algorithm?
347. 🟡 What is Actor-Critic?
348. 🟡 What is PPO (Proximal Policy Optimization)?
349. 🟡 What is RLHF? How is it used for LLM alignment?
350. 🟢 What is multi-armed bandit problem?
351. 🟢 What is Monte Carlo Tree Search (MCTS)?
352. 🟢 What is inverse reinforcement learning?

---

## SECTION 15: MLOPS & PRODUCTION ML

353. 🔴 What is MLOps? Why is it important?
354. 🔴 How do you deploy an ML model to production?
355. 🔴 What is model versioning? How do you manage it?
356. 🔴 What is data drift? How do you detect it?
357. 🔴 What is concept drift? How is it different from data drift?
358. 🔴 How do you monitor a model in production?
359. 🔴 What is a model registry?
360. 🔴 What is MLflow? What does it track?
361. 🔴 What is a feature store? Name some examples.
362. 🔴 What is A/B testing for ML models?
363. 🔴 What is canary deployment vs shadow deployment for ML models?
364. 🟡 What is the difference between online and batch inference?
365. 🟡 What is model serialization? What formats do you use (pickle, ONNX, joblib)?
366. 🟡 What is ONNX? Why is it useful?
367. 🟡 What is model latency vs throughput?
368. 🟡 What is model quantization in production?
369. 🟡 How do you handle retraining triggers?
370. 🟡 What is the Evidently AI library used for?
371. 🟡 What is a CI/CD pipeline for ML?
372. 🟡 What is the difference between a data pipeline and an ML pipeline?
373. 🟡 What is an experiment tracking tool? Give examples.
374. 🟡 How do you handle model rollback?
375. 🟢 What is Kubeflow?
376. 🟢 What is SageMaker? What are its main components?
377. 🟢 What is vertex AI?
378. 🟢 What is a champion-challenger model strategy?
379. 🟢 What is responsible AI? What are fairness metrics?
380. 🟢 What is model governance?

---

## SECTION 16: EXPLAINABILITY & FAIRNESS

381. 🔴 What is model explainability? Why does it matter in production?
382. 🔴 What is SHAP? How does it work?
383. 🔴 What is LIME? How does it differ from SHAP?
384. 🔴 What is a partial dependence plot (PDP)?
385. 🔴 What is an individual conditional expectation (ICE) plot?
386. 🟡 What is feature permutation importance?
387. 🟡 What is a global vs local explanation?
388. 🟡 What is algorithmic bias? Give an example.
389. 🟡 What are common fairness metrics (demographic parity, equalized odds)?
390. 🟡 How do you detect and mitigate bias in an ML model?
391. 🟡 What is counterfactual explanation?
392. 🟢 What is the EU AI Act and how does it affect model explainability requirements?
393. 🟢 What is model cards?
394. 🟢 What is data cards?

---

## SECTION 17: STATISTICS & PROBABILITY FOR ML

395. 🔴 What is the difference between mean, median, and mode?
396. 🔴 What is standard deviation vs variance?
397. 🔴 What is a normal distribution? What is the 68-95-99.7 rule?
398. 🔴 What is a Bernoulli distribution?
399. 🔴 What is a Binomial distribution?
400. 🔴 What is a Poisson distribution? When is it used?
401. 🔴 What is conditional probability?
402. 🔴 What is the law of total probability?
403. 🔴 What is expectation (expected value)?
404. 🔴 What is the difference between independent and mutually exclusive events?
405. 🟡 What is a hypothesis test? What is the null hypothesis?
406. 🟡 What is a t-test? When do you use it?
407. 🟡 What is a chi-squared test? When do you use it?
408. 🟡 What is ANOVA? When do you use it?
409. 🟡 What is the difference between one-tailed and two-tailed tests?
410. 🟡 What is confidence interval?
411. 🟡 What is the difference between correlation and covariance?
412. 🟡 What is Pearson vs Spearman correlation?
413. 🟡 What is a Power law distribution (Pareto / Zipf)?
414. 🟡 What is sampling bias?
415. 🟡 What is the difference between population and sample?
416. 🟢 What is the Central Limit Theorem and why is it important for ML?
417. 🟢 What is bootstrapping in statistics?
418. 🟢 What is Bayesian updating?
419. 🟢 What is conjugate prior?
420. 🟢 What is the difference between frequentist and Bayesian credible intervals?

---

## SECTION 18: GRAPH NEURAL NETWORKS & ADVANCED

421. 🟡 What is a Graph Neural Network (GNN)?
422. 🟡 What is message passing in GNNs?
423. 🟡 What is Graph Convolutional Network (GCN)?
424. 🟡 What is GraphSAGE?
425. 🟡 What is GAT (Graph Attention Network)?
426. 🟡 What are the applications of GNNs?
427. 🟢 What is link prediction?
428. 🟢 What is node classification in GNNs?
429. 🟢 What is graph-level classification?
430. 🟢 What is over-smoothing in deep GNNs?

---

## SECTION 19: DOMAIN-SPECIFIC — HEALTHCARE / INSURANCE (Optum, Carelon, UHG, Cigna)

431. 🔴 How do you build a claims denial prediction model?
432. 🔴 What data preprocessing is needed for healthcare claims data (ICD codes, CPT codes)?
433. 🔴 How do you handle HIPAA compliance in an ML pipeline?
434. 🔴 How do you predict readmission risk from EHR data?
435. 🔴 What is a prior authorization prediction model? How do you design it?
436. 🔴 How do you detect fraud, waste, and abuse (FWA) in claims?
437. 🔴 How do you handle highly imbalanced data in fraud detection?
438. 🟡 What is survival analysis? When is it used in healthcare?
439. 🟡 What is the Cox proportional hazards model?
440. 🟡 How do you build a disease risk stratification model?
441. 🟡 What is NLP applied to clinical notes? What are the challenges?
442. 🟡 How do you extract ICD codes from unstructured clinical text?
443. 🟡 What is population health management and how does ML support it?
444. 🟡 What is the difference between clinical NLP and general NLP?
445. 🟢 What is de-identification of PHI in NLP?
446. 🟢 What is FHIR? How does it relate to ML data pipelines?
447. 🟢 What are the challenges of building ML models on claims vs EHR data?

---

## QUICK CHECKLIST — TOP 50 MUST KNOW QUESTIONS BEFORE ANY INTERVIEW

```
□ 1.  Bias-variance tradeoff
□ 2.  Overfitting + how to prevent
□ 3.  L1 vs L2 regularization
□ 4.  Cross-validation types + when to use each
□ 5.  Precision, Recall, F1 — difference and when to use
□ 6.  ROC-AUC vs PR-AUC
□ 7.  Class imbalance — SMOTE, class_weight, threshold tuning
□ 8.  How Random Forest works
□ 9.  XGBoost vs Random Forest — key differences
□ 10. Gradient Boosting — how it works
□ 11. Decision Tree — Gini vs Entropy
□ 12. Linear Regression assumptions
□ 13. Logistic Regression — sigmoid + log loss
□ 14. SVM — kernel trick, C, gamma
□ 15. K-Means — how it works, choosing K, limitations
□ 16. PCA — what it does, how many components
□ 17. Missing value handling strategies
□ 18. Feature scaling — when and which scaler
□ 19. Outlier detection — IQR, Z-score, Isolation Forest
□ 20. Feature selection methods
□ 21. Backpropagation — how it works
□ 22. Vanishing gradient — causes and fixes
□ 23. Activation functions — ReLU vs sigmoid vs GELU
□ 24. Batch Norm vs Layer Norm
□ 25. Dropout — training vs inference
□ 26. Adam vs SGD — when to use which
□ 27. Learning rate scheduling
□ 28. CNN — conv layer, pooling, what each does
□ 29. Transfer learning — feature extraction vs fine-tuning
□ 30. ResNet — residual connections, why they help
□ 31. LSTM vs GRU — key difference
□ 32. Attention mechanism — how it works
□ 33. Transformer architecture — components
□ 34. BERT vs GPT — architecture and use case difference
□ 35. Tokenization — BPE, WordPiece
□ 36. Word2Vec vs BERT embeddings
□ 37. SHAP vs LIME — when to use which
□ 38. Data drift vs concept drift
□ 39. Model deployment — FastAPI + Docker + MLflow
□ 40. A/B testing vs shadow deployment for ML models
□ 41. GAN — generator/discriminator, mode collapse
□ 42. VAE — reparameterization trick, ELBO
□ 43. Q-learning — Bellman equation, exploration vs exploitation
□ 44. RAG — how it works
□ 45. Fine-tuning vs LoRA/PEFT
□ 46. RLHF — how it is used for LLM alignment
□ 47. Hyperparameter tuning — GridSearch vs Optuna
□ 48. sklearn Pipeline — ColumnTransformer
□ 49. Feature importance — permutation vs SHAP vs tree-based
□ 50. Healthcare: claims denial / fraud / prior auth model design
```
