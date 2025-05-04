# Uncovering Housing Risks In NYC: Unsupervised Learning for NYC Housing Code Violation


## **Contributor:** Hui Gao, Kayla Huang, WeiAn Huang, ZheYu Lin 

## Introduction  
Housing violations in NYC, particularly in Manhattan and Brooklyn, threaten tenant safety and urban livability. The HPD issues violations for unsafe conditions, but identifying high-risk buildings and optimizing enforcement remains challenging due to the volume and complexity of data. Preliminary findings indicate that older buildings in densely populated areas exhibit a higher recurrence of severe violations, making them a priority for targeted regulatory intervention. This project uses geospatial analysis, clustering, association rules and text analysis to provide insights for strengthening enforcement, tenant protection, resource allocation, and policy recommendations, ensuring a more efficient and proactive approach to housing code enforcement in NYC.  

Specifically, we aim to:  
1. Identify common violation types and trends across boroughs to detect high-risk regions.  
2. Cluster buildings by violation severity, recurrence, and resolution time to predict high-risk properties and support preventive regulation.  
3. Examine the relationship between housing type, season, apartment position, and violation category to understand systemic patterns and help developers mitigate recurring issues.  
4. Analyze NOV descriptions using topic modeling to uncover recurring themes and enhance regulatory responses for more effective enforcement.  

---

## Preprocessing of Dataset  
1. **Cleaning and handling Data Issues**:  
   (1) Cleaning process remained the same. Given our dataset is large, we sampled 30% of our data, and used VertexAI to conduct analyses.  
   (2) We split our data into two datasets: one with the required violation corrections completed within the designated time (Non_Postponed) and the other without (Postponed).  

2. **Text Preprocessing**:  
   We cleaned the ‘NOVDescription’ column, which contained violation descriptions. We observed that the relevant details typically appear after the words "Code" or "HMC", so we removed any text preceding these terms. Additionally, we eliminated numeric-only words and words with fewer than two characters to exclude meaningless terms. Finally, we removed stop words and limited the maximum features to 5,000 in our models to enhance efficiency.  

---

## Analysis & Experiments                                                                                                                     

### 1. Spatial Distribution Analysis of Building Violations  
This analysis explored the geographic concentration of building violations and their resolution delays. By mapping violations and analyzing their resolution time, we aim to identify hotspots where violations occur most frequently and highlight areas experiencing delays in correction. These insights will help regulatory agencies allocate resources efficiently and address high-risk locations.  

#### 1.1 Geospatial Mapping for Identifying Violation Hotspots:  
(1) **Heatmaps** - Detect clusters of violations at the borough, ZIP code, and street levels to highlight areas with persistent issues.  
(2) **Pointmaps** - Pinpoint exact violation locations for detailed spatial insights.  

#### 1.2 Violation Resolution Time Analysis:  
(1) **Maps** - Compute median resolution times per borough and ZIP code to understand regional variations in efficiency. Compare against the overall median resolution time to determine whether specific areas experience faster or slower resolutions and identify delays.  
(2) **Pointmap Visualization** - Larger points indicate more violations with longer resolution times compared to the average. Color-coding applied to distinguish between areas with efficient resolution and the degree of experiencing delays.  

---

### 2. Violation Severity Analysis  
We categorize violations by severity level and analyze trends in critical violations, particularly those related to rent impairment and life safety risks. Additionally, we compare severity levels across boroughs, housing types, and resolution times to identify key patterns and risk factors.  

#### 2.1 Descriptive Statistics:  
Analyze the distribution of violation severity levels across boroughs, identifying trends in critical violations, especially those related to rent impairment and life safety risks. Examine how severity correlates with housing position and resolution times.  

#### 2.2 Clustering Analysis:  
(1) **Risk Assessment (K-Means Clustering)** - Categorizes buildings into different risk levels based on Severity Level, Recurrent Violations, and Resolution Time, enabling targeted prioritization of high-risk buildings.  
(2) **Hotspot Identification (DBSCAN)** - Detects geographical areas with dense clusters of severe and recurrent violations with prolonged resolution times, revealing high-risk zones and anomalies that may not fit predefined risk categories. By integrating Severity Level, Recurrence, and Resolution Time, this approach provides a data-driven strategy to identify persistent high-risk violations, support enforcement actions, optimize resource allocation, and enhance preventive maintenance planning.  

#### 2.3 Parameter Tuning and Adjustments:  
(1) **K-Means Optimization** - Used the Elbow Method and Silhouette Score to find the optimal number of clusters.  
(2) **DBSCAN Adjustments** - Tuned eps (neighborhood radius) and min_samples to balance cluster density and noise reduction. Compared results with and without geographic coordinates to assess spatial influence on severity level.  

---

### 3. Text Analysis of Notice of Violation (NOV) Descriptions  
This analysis aims to extract meaningful violation types from the textual descriptions using topic modeling. By identifying distinct violation categories, we can classify violations into structured groups, enabling further analysis of their distribution across different levels.  

#### 3.1 Steps:  
- **Step 1 - Text Vectorization**:  
  We experimented with Bag of Words (BOW) and TF-IDF to represent violation descriptions numerically. TF-IDF was chosen as the preferred method because:  
  - It highlights relatively rare but significant terms crucial for categorization.  
  - It reduces the influence of common but less informative words, improving topic separation.  

- **Step 2 - Topic Modeling using NMF**:  
  Latent Dirichlet Allocation (LDA) was initially tested but produced hard-to-interpret topics with overlapping terms. So we applied Non-Negative Matrix Factorization (NMF) to extract key violation topics. It generates more interpretable and distinct topic groupings and effectively captures the latent structure in violation descriptions.  

- **Step 3 - Determining the Optimal Number of Topics**:  
  To select the most meaningful number of topics, we used the elbow method to analyze the reconstruction error of NMF models with different topic numbers. We also computed coherence scores to evaluate the semantic consistency of topics. Then we generated and interpreted sample topics using ChatGPT to ensure practical relevance and avoid redundant topics. Finalized the number of topics based on meaningful representations aligned with violation patterns.  

- **Step 4 - Interpreting & Validating Topics**:  
  After extracting topics, we analyzed the top 10 representative descriptions for each. We used ChatGPT to assist in summarizing and naming topics. Topics were refined based on expert validation and practical relevance to violations.  

---

### 4. Specific Violation Patterns and Related Factors  
This analysis explores whether certain types of violations are more prevalent in specific areas and whether violations tend to occur more frequently on particular floors, such as the ground or top floor. Also, it examines the relationships between season, apartment position, and violation category to identify patterns that could inform targeted interventions and enforcement strategies.  

To uncover hidden patterns in violation occurrences, we applied **Association Rule Mining (Apriori Algorithm)** by treating each building as a transaction containing all violation types that occurred there. This approach helps identify frequently co-occurring violations using support-confidence-lift metrics, revealing associations between different issues. For example, it allows us to determine rules like “If a violation happens in X building, it is likely to have Y issue”, highlighting buildings prone to multiple violations.  

Beyond co-occurrence patterns, we analyzed the distribution of violations across floor levels (e.g., ground vs. top floor), seasons, and boroughs to assess how these factors influence the prevalence of specific issues. The results provide actionable insights for targeted preventive maintenance strategies, helping enforcement agencies and property managers prioritize interventions based on location and time trends.  

---

## Challenges, Dead Ends & Adjustments                                                                                                             

### 1. Challenges in Interpreting K-Prototypes Clustering Results  
After identifying violation types, we attempted clustering using the K-prototype algorithm but found the results lacked meaningful differentiation, offering little actionable insight. While we could summarize cluster traits, most patterns were already evident through simple visualization. Since clustering added minimal value, we instead focused on visualizing violation types directly to uncover meaningful patterns.  

### 2. Challenges in Defining Violation Topics Across Postpone and Non-Postpone Cases  
Initially, we split the dataset into postpone and non-postpone groups and trained separate topic models for each, resulting in some different but overlapping topics. To evaluate whether a unified model would improve consistency, we also trained a single topic model using the combined dataset. However, the overall topic distribution remained similar to the separate models.  

During our analysis, we found that training the models separately helped capture certain topics that the unified model failed to identify. This suggests that the differences between postponed and non-postponed cases influence the topic distribution, making separate models more effective in distinguishing meaningful patterns. Therefore, we decided to retain the separate models while merging similar or overlapping topics and keeping distinct ones unchanged to ensure a more precise classification.  

### 3. Challenges in Handling Memory Overload  
Since our Non_Postponed dataset contains over 2 million rows, training the model directly would lead to memory overload. To address this, we use batch processing to compute the TF-IDF incrementally, reducing memory usage. Additionally, when determining the optimal number of clusters for the K-prototype model, we sample 50,000 rows from the dataset and apply the elbow method to make the computation feasible.  
