# Spider 2.0: Evaluating Language Models on Real-World Enterprise Text-to-SQL Workflows


## 📰 News

- **2024-11-04**: We released the paper along with the full dataset. Notably, we offer three settings: [`Spider 2.0`](https://github.com/xlang-ai/Spider2/tree/main/spider2), [`Spider 2.0-Lite`](https://github.com/xlang-ai/Spider2/tree/main/spider2-lite), [`Spider 2.0-Snow`](https://github.com/xlang-ai/Spider2/tree/main/spider2-snow).


- **2024-08-28**: We released a smaller version of Spider 2.0 (~25% of the full dataset) containing 190 examples to give users early access. The full dataset and the paper will be available in two weeks. As this is a preliminary release, there may be errors. Your feedback would be invaluable in refining the dataset. Stay tuned!



## 👋 Overview


![Local Image](./assets/Spider2.png)

<div style="width: 10%; margin: auto;">
  <table style="font-size: 12px; width: 100%;">
    <tr>
      <th>Setting</th>
      <th>Task Type</th>
      <th>#Examples</th>
      <th>Databases</th>
    </tr>
    <tr>
      <td><strong>Spider 2.0</strong></td>
      <td>Code agent task</td>
      <td>632</td>
      <td>BigQuery(394), Snowflake(18), Postgres(10), ClickHouse(7), SQLite(135), DuckDB (DBT)(68)</td>
    </tr>
    <tr>
      <td><strong>Spider 2.0-Lite</strong></td>
      <td>Text-to-SQL task</td>
      <td>547</td>
      <td>BigQuery(394), Snowflake(18), SQLite(135)</td>
    </tr>
    <tr>
      <td><strong>Spider 2.0-Snow</strong></td>
      <td>Text-to-SQL task</td>
      <td>547</td>
      <td>Snowflake(547)</td>
    </tr>
  </table>
</div>


### Why Spider 2.0?

In 2018, we introduced [Spider 1.0](https://yale-lily.github.io/spider), [SParC](https://yale-lily.github.io/sparc), and [CoSQL](https://yale-lily.github.io/cosql) as part of the Yale Semantic Parsing and Text-to-SQL Challenge Series, attracting over 300 submissions from leading research labs worldwide.

Now, in the era of Large Language Models (LLMs), we present Spider 2.0 to advance code generation, particularly text-to-SQL capabilities.

This new benchmark offers a more realistic and challenging test of LLMs' performance on complex enterprise-level text-to-SQL workflows, involving complex data environments (e.g., >3000 columns), multiple SQL dialects (e.g., BigQuery, Snowflake), and diverse operations (e.g., transformation, analytics).

Notably, as shown below, even the most advanced LLMs, including GPT-4, solve only 6.0% of Spider 2.0 tasks, compared to 86.6% on Spider 1.0 and 57.4% on BIRD, highlighting the significant challenges posed by Spider 2.0.

|                 | Spider 1.0 dev | Spider 1.0 test | BIRD test | Spider 2.0 |
| --------------- | -------------- | --------------- | --------- | ---------- |
| DailSQL + GPT-4 | 82.4           | 86.6            | 57.4      | 9.4         |
| CodeS-7B        | 85.4           | -               | 59.3      | 2.2        |




## 🚀 Quickstart

### Sign Up for Your Own BigQuery and Snowflake Accounts

1. To sign up for a BigQuery account, please follow this [guideline](https://github.com/xlang-ai/Spider2/blob/main/assets/Bigquery_Guideline.md) and fill out the [Spider2 Public Data Access Form](https://docs.google.com/forms/d/e/1FAIpQLSdrsJX-oDZDL0McIaF-0uypLeO2pYW4SX-qDeNSd88iYR_3Gg/viewform).

2. Follow this [guideline](https://github.com/xlang-ai/Spider2/blob/main/assets/Snowflake_Guideline.md)) and fill out this [Snowflake form](https://docs.google.com/forms/d/e/1FAIpQLScbVIYcBkADVr-NcYm9fLMhlxR7zBAzg-jaew1VNRj6B8yD3Q/viewform?usp=sf_link), and we will send you an account sign-up email, which will allow you to access the Snowflake database.

**Important Notes:**

- If you want to access the **FULL dataset** of Spider 2.0 or Spider 2.0-Lite, you must complete **Step1** and **Step2**.

- If you only want access to the **FULL dataset** of Spider 2.0-Snow, you only need to complete **Step2**.




### Spider 2.0

For [`Spider 2.0`](./spider2/README.md), all evaluation examples are aggregated in file [`spider2.jsonl`](./spider2/examples/spider2.jsonl), where each data point contains the following field:
```json
{
    "instance_id": "ga001",
    "instruction": "I want to know the preferences of customers who purchased the Google Navy Speckled Tee in December 2020. What other product was purchased with the highest total quantity alongside this item?",
    "type": "Bigquery"
}
```
For each instance, we also provide a separate folder [`./spider2/examples/{instruction_id}`](./spider2/examples/) as its **Execution Context** to simulate the agentic setting. Each folder may have the following files:

- `README.md`: detailed requirements of the `instruction` field for the current example with `instance_id`;
- `*_credential.json`: credential file connecting to realistic enterprise-level databases, e.g., BigQuery. Can be replaced with your OWN;
- `result.csv`: CSV file to store the execution results;
- other instance-specific materials which assist in finishing the current task:
    - 🏗️ partial project, e.g., [`dbt_project/`](./spider2/examples/airbnb001/).
    - 📝 reference documentation: [`ga4_dimensions_and_metrics.md`](./spider2/examples/ga010/ga4_dimensions_and_metrics.md), [`retention_rate.md`](./spider2/examples/ga022/retention_rate.md), etc.
    - 🔍 query interface: We have predefined how to access the diverse database systems.
    - 🎞️ query history or samples, e.g., [`QUERY_HISTORY/`](./spider2/examples/ga003/FIREBASE_QUERY_HISTORY/), etc.



The agent has to interact with complex SQL workflows, process extremely long contexts, perform intricate reasoning, and generate multiple SQL queries with diverse operations, often exceeding 100 lines across multiple turns.


#### Run Spider-Agent

For Spider 2.0, we proposed an agent framework [Spider-Agent](https://github.com/xlang-ai/Spider2/tree/main/methods/spider-agent) based on Docker environment. 

1. **Install Docker**. Follow the instructions in the [Docker setup guide](https://docs.docker.com/engine/install/) to install Docker on your machine. 
2. **Install conda environment**.
```
git clone https://github.com/xlang-ai/Spider2.git
cd methods/spider-agent

# Optional: Create a Conda environment for Spider 2.0
# conda create -n spider2 python=3.11
# conda activate spider2

# Install required dependencies
pip install -r requirements.txt
```
3. **Configure credential**: follow this [instruction](https://github.com/xlang-ai/Spider2/tree/main/spider2#configure-credential) to configure BigQuery for running the SQL queries. follow this [guideline](https://github.com/xlang-ai/Spider2/blob/main/assets/Snowflake_Guideline.md) to get your own Snowflake username and password in our snowflake database. You must update `bigquery_credential.json` and `snowflake_credential.json`.

4. **Download Spider 2.0 Database Source**
```
cd spider2

gdown 'https://drive.google.com/uc?id=1OxF-OuPwgb2miQxzftGLZBzPRQtLsyoV'
gdown 'https://drive.google.com/uc?id=1WRmKgfRylZkRZ080U2QI1hjgph_3iUlq'
gdown 'https://drive.google.com/uc?id=1sRSuvOC24qlI_ERoMdJRDUQ_cJs-MbcJ'
gdown 'https://drive.google.com/uc?id=15nWtefZgxXOn_vzcIqF2gh4fLxmJC84F'

```


5. **Spider 2.0 Setup**
```
python setup.py
```

6. **Run agent**
```
cd ../../methods/spider-agent
export OPENAI_API_KEY=your_openai_api_key
python run.py --model gpt-4o -s test1
```


### Spider 2.0-Lite

To align with research interests in **traditional Text2SQL settings**, we also release [`Spider 2.0-Lite`](https://github.com/xlang-ai/Spider2/tree/main/spider2-lite#spider-20-lite). This set is more self-contained, with well-prepared database metadata and documentation, making it a text-in, text-out task that supports faster development and evaluation.

You can also access the Spider 2.0-Lite by [huggingface dataset](https://huggingface.co/datasets/xlangai/spider2-lite).🤗
```
from datasets import load_dataset
ds = load_dataset("xlangai/spider2-lite")
```

Each file in `spider2-lite.json` contains the following fields:
- `instance_id`: the unique example id
- `db`: the database id to which this question is addressed
- `question`: the natural language question
- `external_knowledge`: the filenames of external knowledge, documentation, and information required to answer this question are stored in documents


We proposed baselines based on the widely used text2sql methods: [`Dail-SQL`](https://github.com/xlang-ai/Spider2/tree/main/spider2-lite/baselines/dailsql#installation) and [`CodeS`](https://github.com/xlang-ai/Spider2/tree/main/spider2-lite/baselines/codes#installation), with evaluation results reported :test_tube:.


### Spider 2.0-Snow

We would like to thank Snowflake for sponsoring our project. To better align with the research interests of the text-to-SQL community, we are offering [Spider 2.0-Snow](https://github.com/xlang-ai/Spider2/tree/main/spider2-snow), which hosts all databases from Spider 2.0 in the Snowflake data warehouse. This arrangement facilitates users in developing advanced text-to-SQL systems more conveniently.

We adapt [Spider-Agent](https://github.com/xlang-ai/Spider2/tree/main/methods/spider-agent-snow) and other text-to-SQL baselines to this setting. 


#### Run Spider-Agent(Snow)

1. **Install Docker**. Follow the instructions in the [Docker setup guide](https://docs.docker.com/engine/install/) to install Docker on your machine. 
2. **Install conda environment**.
```
git clone https://github.com/xlang-ai/Spider2.git
cd methods/spider-agent-snow

# Optional: Create a Conda environment for Spider 2.0
# conda create -n spider2 python=3.11
# conda activate spider2

# Install required dependencies
pip install -r requirements.txt
```
3. **Configure credential**: Follow this [guideline](https://github.com/xlang-ai/Spider2/blob/main/assets/Snowflake_Guideline.md) to get your own Snowflake username and password in our snowflake database. You must update `snowflake_credential.json`.

4. **Spider 2.0-Snow Setup**
```
python spider_agent_setup_snow.py
```

5. **Run agent**
```
export OPENAI_API_KEY=your_openai_api_key
python run.py --model gpt-4o -s test1
```


# 📋 Leaderboard Submission 

We only release the gold answer of about 50% examples of Spider 2.0, Spider 2.0-Lite and Spider 2.0-Snow, you must follow this [submission guidance](https://docs.google.com/document/d/1sCobAqJZcko-Vl3biOycwvCIR7kTwBPrhsgVfvaX1Fg/edit?usp=sharing) to get your score on Spider 2.0 FULL dataset. 



# 🙇‍♂️ Acknowledgement

We would like to express our sincere gratitude to Snowflake for their generous support of our project, including providing quota to host the Snowflake databases and offering query quotas for users. Their contributions have been invaluable in enabling us to advance our research and provide innovative solutions to the text-to-SQL community.

# ✍️ Citation
If you find our work helpful, please cite as
```



```
