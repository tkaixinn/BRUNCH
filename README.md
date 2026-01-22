# Customer Segmentation Analysis

## 1. Project Overview and Objective
This project aims to perform customer segmentation using a dataset containing firmographic and financial data. The primary objective is to identify distinct customer groups through K-Means and Hierarchical Clustering techniques. These identified segments can then be leveraged for targeted marketing campaigns, tailored product development, and strategic market planning.

## 2. Environment Setup and Dependencies

This notebook is designed to run in a Google Colab environment. The following Python libraries are required:

*   `kagglehub`
*   `pandas` (for data manipulation)
*   `numpy` (for numerical operations)
*   `seaborn` (for statistical data visualization)
*   `matplotlib.pyplot` (for plotting and visualization)
*   `sklearn.preprocessing.StandardScaler` (for feature scaling)
*   `sklearn.cluster.KMeans` (for K-Means clustering)
*   `scipy.cluster.hierarchy` (for Hierarchical Clustering: `dendrogram`, `linkage`, `fcluster`)
*   `umap` (imported but not explicitly used in the final clustering analysis presented in the notebook)
*   `langchain_huggingface.chat_models.ChatHuggingFace` (for LLM integration)
*   `langchain_huggingface.llms.HuggingFaceEndpoint` (for LLM integration)
*   `google.colab.drive` (for Google Drive integration)
*   `os` (for file system operations)
*   `IPython.display.Markdown` (for displaying markdown content)

To install the necessary libraries, run the following in a Colab cell:
```bash
!pip install -q langchain-huggingface
```

## 3. How to Run the Notebook and Reproduce Results

To execute this notebook and reproduce the results, follow these steps:

1.  **Open the Notebook in Google Colab**: Upload or open the `.ipynb` file in Google Colab.

2.  **Mount Google Drive**: Execute the cell containing the following code to mount your Google Drive. This is crucial as the primary dataset is expected to be in your Drive.
    ```python
    from google.colab import drive
    drive.mount('/content/drive')
    ```

3.  **Place the Dataset**: Ensure the dataset file, `champions_group_data.xlsx`, is located in your Google Drive at the specific path `/content/drive/MyDrive/Datathon BRUNCH/champions_group_data.xlsx`. If your file is stored elsewhere, update the `excel_file_path` variable in the relevant code cell (around `c65e9f94`) to reflect the correct location.

4.  **Hugging Face API Token (for LLM sections)**: The notebook integrates with the Hugging Face API for advanced analysis and summarization. To enable this:
    *   Obtain a Hugging Face API token from the Hugging Face website.
    *   In Google Colab, click on the "🔑" icon (Secrets) in the left panel.
    *   Add a new secret named `HF_TOKEN` and paste your Hugging Face API token as its value.
    *   Ensure the `huggingfacehub_api_token` in the `HuggingFaceEndpoint` initialization (cell `9c45d63e`) is set to retrieve this secret (`HF_TOKEN = userdata.get('HF_TOKEN')` or directly `HF_TOKEN = "your_token_here"`). *Note: The provided notebook uses a hardcoded token. For security, it is highly recommended to use Colab Secrets.*

5.  **Execute Cells Sequentially**: Run all code cells in the notebook from top to bottom. The notebook is structured to flow logically, with dependencies between cells.
    *   **Data Loading and Cleaning**: Cells importing libraries, loading data, and performing initial cleaning.
    *   **Feature Engineering**: Cells creating new, informative features for clustering.
    *   **Clustering**: Cells performing feature scaling, K-Means clustering (including Elbow Method for optimal K), and Hierarchical clustering (with dendrogram visualization).
    *   **Cluster Analysis**: Cells calculating descriptive statistics and visualizing cluster characteristics.
    *   **LLM Analysis**: Cells generating summaries and comparisons using the Hugging Face LLM.

## 4. Specific Instructions or Prerequisites

*   **Input Data**: The sole input required is the `champions_group_data.xlsx` Excel file, which must be accessible via Google Drive.
*   **Parameters**: Key parameters include:
    *   `optimal_clusters = 3`: The number of clusters chosen for both K-Means and Hierarchical clustering, determined by visual inspection of the Elbow Method plot.
    *   `CURRENT_YEAR = 2026`: An assumption made for calculating `Company_Age`. This can be adjusted if the analysis needs to reflect a different current year.
    *   `random.seed(1000)` and `random_state = 42`: Used for reproducibility in random processes like KMeans initialization.
*   **Hardware Assumptions**: Standard Google Colab resources (CPU, RAM) are sufficient to run this notebook. No specialized hardware (e.g., GPU) is strictly required unless the dataset size significantly increases, impacting LLM inference times.
*   **Output**: The notebook generates various dataframes, plots, and LLM-generated text outputs directly within the notebook cells.

## 5. Key Insights and Findings

### Comparison of Clustering Methods
Both K-Means and Hierarchical clustering identified similar high-level segments based on company size, revenue, and geographical distribution (predominantly China and Indonesia). While both methods were valuable, K-Means tended to create more distinct, well-separated clusters, including a very small, niche group (K-Means Cluster 2, with only 2 companies). Hierarchical clustering provided insights into the relationships between clusters and sometimes distributed entities more broadly across diverse groups (e.g., Hierarchical Cluster 3).

### K-Means Cluster Characteristics
*   **Cluster 0 (991 companies)**: Characterized by high IT investment and hardware adoption per employee (highest `IT_Budget_Per_Employee`, `Laptops_Per_Employee`, `Servers_Per_Employee`), moderate `Revenue_Per_Employee`, primarily in retail and non-classified sectors, almost exclusively located in China. These could be seen as *Tech-Forward Local Businesses*.
*   **Cluster 1 (7566 companies)**: The largest group, representing a broad 'general services' segment, with moderate tech intensity and primarily in business services, across China and Indonesia. These are the *General Service Providers*.
*   **Cluster 2 (2 companies)**: A very small, highly specialized group (construction, manufacturing) from China, showing exceptionally high `Revenue_Per_Employee` but notably low IT/hardware intensity. This suggests highly niche, high-value operations or potential data outliers needing further investigation. These are the *Niche High-Value Producers*.

### Hierarchical Cluster Characteristics
*   **Cluster 1 (6794 companies - "The Service Backbone")**: The largest cluster, focused on Business and Management Services in China. Characterized by relatively lower technological adoption, representing traditional, labor-heavy service firms.
*   **Cluster 2 (1514 companies - "Retail and Professional Mix")**: Primarily retail and engineering services, mostly from China, exhibiting moderate technological adoption.
*   **Cluster 3 (251 companies - "Tech and Infrastructure Hub")**: Companies in Computer Systems Design and Nonclassified sectors, predominantly from China. They stand out with exceptionally high `Revenue_Per_Employee` and the highest technological adoption (servers/laptops per employee), representing the core technology sector.

### Actionable Business Insights and Recommendations
*   **Targeted Marketing and Sales**: Tailor messages based on cluster profiles. For example, 'The Service Backbone' (HC1) may respond to cost-effective, basic solutions, while 'Tech and Infrastructure Hub' (HC3) requires advanced technological integrations.
*   **Product Development**: Identify unmet needs within each segment. HC3 companies represent a high-value niche for specialized tech solutions.
*   **Strategic Planning and Market Segmentation**: Leverage segments for understanding market dynamics and allocating resources. The strong presence of companies from China across all clusters suggests a primary geographic focus.
*   **Digital Maturity Assessment**: The `Digital_Intensity` (`IT Budget` / `Revenue`) feature helps identify varying levels of tech investment relative to revenue, informing strategies for promoting digital transformation.
*   **Segmentation by Ownership Type**: Further granular insights were observed by analyzing model features against ownership type within clusters, indicating distinct needs and capacities among private, public, and other structures.
*   **LLM-Powered Strategic Advisory**: The implemented `prototype_advisor` function demonstrates the potential to automate and personalize outreach by comparing target companies to their peers, identifying 'Tech Leaders' or 'Tech Laggards', and generating tailored business recommendations or sales pitches.

## 6. Limitations and Assumptions

*   **Data Source**: The analysis relies entirely on the provided `champions_group_data.xlsx`. The quality, completeness, and recency of this data directly impact the validity of the segments.
*   **Optimal Clusters**: The choice of 3 optimal clusters for both methods is based on the Elbow Method's visual inspection, which can sometimes be subjective. Other methods (e.g., Silhouette Score) could be explored for validation.
*   **Missing Value Imputation**: Missing financial data was imputed using the median, which is a simple approach. More sophisticated imputation techniques might yield different results.
*   **Feature Engineering**: The engineered features are designed to capture specific aspects of company behavior. Different feature sets could lead to alternative segmentations.
*   **Geographic Focus**: The overwhelming dominance of China in the dataset suggests that the segmentation is highly specific to this region, and insights may not be directly transferable to other geographies without further data.
*   **LLM Token**: The LLM interaction requires a Hugging Face API token, and the quality of generated summaries depends on the LLM model and prompt engineering.

```
