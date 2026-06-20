Get the document info from https://open-congress-api.bettergov.ph/api/scalar#tag/documents/GET/documents

COnnect the data (DOCUMENTS ONLY) with HuggingFace from https://huggingface.co/datasets/bettergovph/raw-philippine-data

Enrich the data by attaching the document information from the Congress API to the huggingface

Introduce NLP to get summarization


Get the documents by the congress API code for the 13th-20th congress (Also have authors)

Final Table: HB ID, Authors, Title, Date, etc /

COnnect with HuggingFace to get 13th-20th congress documents

Final table: HB ID/SB, content


Profit

# **BATAS Project (Legislative Bills and Text Analyzation and Summarization)**
## Transforming Raw House Bills into Scannable, NLP-Powered Insights.
-------------------------


As a fourth-year Computer Science student specializing in Data Science, I have spent my college years learning how to build pipelines, clean messy datasets, analyze data, and train models. But during my On-the-Job Training (OJT), as I watched the recent, intriguing developments unfold within the Philippine Senate, it hit me, I am not as aware in the developments of the my country as much as I think. And that same sentiment goes for my fellow youth.

My generation of Filipino youth is incredibly tech-savvy. We can navigate complex interfaces, digest digital content at lightning speed, stay constantly connected, and have the power to access any information we can. Yet, when it comes to political awareness and tracking actual legislative progress, there is a massive disconnect. Most youths aren't politically aware, and are only interested in the drama. A lot of my peers aren’t politically indifferent; they are just overwhelmed.

**The problem is that, they do not know who to vote, and vote whoever is "popular" with the masses.**

The barriers to entry are frustratingly high:
- Legislative bills, panel reports, and statutory updates are naturally dense, written in formal legal jargon, and span dozens of pages, making them incredibly intimidating for casual reading.
- Keeping track of dynamic updates, identifying which lawmakers authored specific bills, and tracing a piece of legislation's history requires navigating outdated or non-intuitive government portals.
- Traditional methods of disseminating political information simply do not align with the consumption habits of modern youth, who favor concise and short digestible content.
- The youth will be joining the voting population, them not being aware will lead to major lapses in political voting turnaround. 

I wanted to use my skills to build a bridge over that gap. This project was born out of a desire to create a piece of Civic Technology that speaks our language and help the Filipino youth be more political aware.

By engineering an automated pipeline that leverages multi-threaded data aggregation and Natural Language Processing (NLP), my goal is to democratize legislative data. By transforming raw, unstructured government datasets into clean, searchable inventories and concise text summaries, this tool translates dense political maneuvers into accessible, objective insights. 

Ultimately, I want to empower my fellow tech-savvy youth, and give them the clarity we need to foster informed civic engagement, bridge the political awareness gap, and hold our leadership accountable.

-------------------------

## Background
To understand why a data pipeline for tracking bills is so critical, it is essential to look at the structural foundation of our democracy. The Philippine government is modeled after a presidential system and is divided into three co-equal branches: the Executive, the Legislative, and the Judicial. While they operate independently under a system of checks and balances, they are deeply bound by a single, common thread: the law.

- **Executive Branch** (led by the President and the Cabinet) is tasked with enforcing and executing the laws of the land.
- **Judicial Branch** (headed by the Supreme Court) interprets those laws and settles legal controversies.
- **Legislative Branch** (vested in the bicameral Congress of the Philippines) holds the sole authority to create, alter, and repeal them.

As a Data Science student looking at this system, Congress is essentially the core engine where raw societal needs are encoded into structural data—what we call Bills.


### The Structure of Congress


Our Legislative branch is split into two separate chambers, both of which must agree on a piece of legislation before it can ever become a reality:

**The Senate (Upper House**): Composed of 24 Senators elected at large by the entire country, representing national interests.

**The House of Representatives (Lower House**): Composed of district representatives and party-list nominees who champion localized and sector-specific concerns.



### How a Bill Becomes a Law


The journey of a bill from a simple policy idea to an enacted Republic Act (RA) is a highly bureaucratic, multi-stage process. Understanding this pipeline highlights exactly why tracking it manually is so exhausting for the average citizen.

The standard lifecycle involves several critical phases:
- **Introduction and First Reading**: A Senator or Representative files a bill. It is assigned a unique tracking number (like the HBN or house bill numbers I am parsing in my code). During the First Reading, only its title and number are read on the floor, and it is immediately referred to an appropriate Committee (e.g., Committee on Health, Committee on Education).
- **The Committee Public Hearing**: This is where the real work happens. The committee reviews the bill, invites experts, holds public consultations, and debates its merits. They can amend it, consolidate it with similar bills, kill it entirely, or approve it via a Committee Report to be sent to the whole chamber.
- **Second Reading (The Crucible)**: The bill is brought before the entire plenary floor. The sponsorship speech is delivered, followed by intense floor debates, interpellation (questioning), and individual amendments. This is where a bill is heavily scrutinized. Once debates close, the chamber votes on it via a viva voce (voice) or counted vote.
- **Third Reading**: If passed on the second reading, printed copies of the final version are distributed to the members days in advance. No more amendments are allowed. A final, nominal vote is cast where members register their "Yes," "No," or "Abstain."
- **Bicameral Conference Committee (The Matchmaking)**: Since a bill must be passed by both houses in identical language, a special joint committee is often formed to reconcile conflicting provisions between the Senate version and the House version. The resulting "Bicameral Report" must be ratified by both chambers.
- **Presidential Enactment**: Once an identical bill passes both houses, it is enrolled and sent to the Malacañang Palace for the President's signature. The President has three choices:
    - Sign it: It immediately becomes a Republic Act.
    - Veto it: The President rejects it and sends it back with objections (which Congress can override with a 2/3 majority vote in both houses).
    - Lapse into Law: If the President takes no action within 30 days of receiving it, the bill automatically becomes law as if it had been signed.
    - Note: The new law officially takes effect 15 days after its publication in the Official Gazette or in newspapers of general circulation.



### The Challenge in the System


There are thousands of bills moving through different stages simultaneously across two different chambers. They are being amended, substituted, delayed, or fast-tracked.

For an ordinary "Gen-Z" youth, following this process is difficult and at times, overwhelming. Trying to find out what is actually happening in government creates an overwhelming data fragmentation problem. Valuable information is buried inside lengthy PDF committee reports, complex congressional journals, and dense legal terminology.

By mapping out this exact legislative workflow into a clean data engineering model, my project seeks to turn this complex civic pipeline into structured, transparent, and instantly readable knowledge.

---

## Data Sourcing
To build a reliable data pipeline for tracking Philippine legislation, I am integrating and cross-referencing two distinct data sources from the **Open Congress API**, and **BetterGov.PH** (a civic technology organization dedicated to making public data accessible and actionable).


### 1. Source A: The Open Congress API (Metadata Extraction)



* **Endpoint:** `https://open-congress-api.bettergov.ph/api/`
* **Role in Pipeline:** Metadata Retrieval

The Open Congress API serves as our live relational engine. From the `/documents` endpoint, I fetch structured metadata files containing critical legal contexts for House Bills (HB) across multiple congressional terms (specifically tracking the 13th to the 20th Congress).

The key features pulled from this API include:

* **Congress Term:** The specific legislative period (e.g., `18`, `19`, `20`).
* **Document Number / Name:** The unique, official alphanumeric bill identifier (e.g., `HBN-06098`).
* **Authors:** Nested structural data containing the names, suffixes, and initials of the legislators sponsoring or co-authoring the bill.
* **Scope & Dates:** Regional or national targeting scopes along with formal filing timestamps where available.

### 2. Source B: Hugging Face Repository (Full-Text Content Extraction)



* **Dataset Identifier:** `bettergovph/raw-philippine-data` (specifically the `documents` split)
* **Role in Pipeline:** Heavy Text Retrieval

While the API provides excellent categorical records, reading and processing tens of thousands of long, multi-page legal documents via live HTTP network endpoints is highly inefficient, and most documents are simply scanned as images. To solve the problem of processing heavy text at scale, I pull the raw text file records directly from BetterGov.PH's open-source repository hosted on Hugging Face.

This specific text dataset holds the literal transcribed text blocks of over 60,000 legislative items. The features I pull from this split are:

* **ID:** The unique matching index token string (e.g., `hb-18-6098`).
* **Content:** The complete, unedited full-text transcription of the legal bill document.



### Limitations of the Project

The data source will be discussed below, however, there are limitations as to what can be used. Due to the data source's limitations of the BetterGov HuggingFace dataset only containing House Bills and Congress data from the 13th-20th Congress, this project will only make use of House Bills under the 13th up to the 20th Congress.

---


## Data Collection

This section implements a data scraping pipeline designed to harvest House Bills (HB) across the 13th to the 20th Philippine Congress using the Open Congress API.

Because fetching thousands of documents sequentially over HTTP is incredibly slow, this script leverages Python’s **concurrent.futures** to download multiple pages in parallel, while protecting itself against network instability with automated connection retries and exponential backoff.


#### Imports and Configurations


First, we import the necessary libraries. We utilize requests for network operations, pandas for structural data manipulation, and concurrency utilities for multi-threading. We also configure our BASE_URL API template and set a pagination constraint of LIMIT = 20 items per request.


```python
import requests
import pandas as pd
from datasets import load_dataset
import time
from concurrent.futures import ThreadPoolExecutor, as_completed
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

BASE_URL = "https://open-congress-api.bettergov.ph/api/congresses/{}/documents"
LIMIT = 20

```

### Open Congress API

#### Creating a Resilient HTTP Session


Network operations are prone to occasional drops, 502 Bad Gateway, or 503 Service Unavailable errors.

To prevent our script from crashing mid-execution, **get_session()** builds a customized requests.Session() object configured with an automated Retry Strategy:
- It attempts a failed call up to **5 times**. 
- It introduces a **backoff_factor** to pause incrementally before retrying.
- It targets specific server failure codes **(500, 502, 503, 504)** to avoid overloading the host.


```python
def get_session():
    session = requests.Session()

    retry = Retry(
        total=5,
        backoff_factor=0.5,
        status_forcelist=[500, 502, 503, 504],
        allowed_methods=["GET"]
    )

    adapter = HTTPAdapter(max_retries=retry)
    session.mount("https://", adapter)
    session.mount("http://", adapter)

    return session


session = get_session()

```

#### Fetching an Individual Page with Exponential Backoff


The **fetch_page** function handles pulling a single, specific slice (or "page") of data defined by an offset.

This function wraps the call in a loop if the session's default retry strategy fails due to rate limits or heavier connection anomalies. It implements a manual **Exponential Backoff Mechanism (wait = 2 ^ attempt)**. If an error is caught, it waits 1s, then 2s, then 4s, and so on, before trying again. If it fails entirely after 5 manual attempts, it returns an empty list [] to preserve pipeline execution.


```python
def fetch_page(congress_id, offset):
    url = BASE_URL.format(congress_id)

    for attempt in range(5):
        try:
            r = session.get(
                url,
                params={"type": "HB", "limit": LIMIT, "offset": offset},
                timeout=30
            )
            r.raise_for_status()
            return r.json()["data"]

        except Exception as e:
            wait = 2 ** attempt
            print(f"Retrying {congress_id} offset {offset} in {wait}s...")
            time.sleep(wait)

    return []

```



#### Multi-Threaded Congress Extraction and Flattening


The **fetch_congress** function serves to collect the current congress data to gather the documents within it.
1. It fires an initial request (offset=0) to inspect the total volume of bills available (data["pagination"]["total"]). It computes a list of all required offsets.
2. It spins up a ThreadPoolExecutor using up to 5 parallel worker threads. Each thread executes a fetch_page task asynchronously to collect 5 batches at a time.
3. As tasks finish (as_completed), it logs live tracking markers and extends the results list.
4. Finally, it uses pd.json_normalize(all_data) to automatically flatten any nested JSON dictionaries into a clean, tabular Pandas DataFrame.


```python

def fetch_congress(congress_id):
    print(f"\nCongress {congress_id}")

    # get total
    r = session.get(
        BASE_URL.format(congress_id),
        params={"type": "HB", "limit": LIMIT, "offset": 0}
    )
    data = r.json()

    total = data["pagination"]["total"]
    offsets = list(range(0, total, LIMIT))

    all_data = []

    with ThreadPoolExecutor(max_workers=5) as executor:
        futures = {
            executor.submit(fetch_page, congress_id, offset): offset
            for offset in offsets
        }

        for i, future in enumerate(as_completed(futures)):
            batch = future.result()
            all_data.extend(batch)

            print(
                f"Congress {congress_id} | "
                f"{i+1}/{len(offsets)} | "
                f"+{len(batch)}"
            )

    return pd.json_normalize(all_data)

```

#### DataFrame Concatenation


This final cell completes runtime execution. It iterates through the 13th to the 20th Congress, saves each individual DataFrame into a dictionary (dfs), and uses a polite time.sleep(1) between iterations to reduce hitting the public server with overlapping traffic.

Once every targeted Congress has been fetched and the loop finishes, pd.concat unifies all individual subsets into one final, **master DataFrame (df_all)**.


```python
# Target range spans from the 13th Congress up to the 20th Congress
congress_ids = range(13, 21)

dfs = {}

# Iterate over each congress term sequentially
for cid in congress_ids:
    dfs[cid] = fetch_congress(cid)
    time.sleep(1)  # Included time delay between requests to avoid hitting rate limits
    
# Final merge step joining all dictionaries into a single monolithic matrix
df_all = pd.concat(dfs.values(), ignore_index=True)

print("\nFINAL SHAPE:", df_all.shape)
```

    
    🔄 Congress 13
    Congress 13 | 1/306 | +20
    Congress 13 | 2/306 | +20
    Congress 13 | 3/306 | +20
    Congress 13 | 4/306 | +20
    Congress 13 | 5/306 | +20
    Congress 13 | 6/306 | +20
    Congress 13 | 7/306 | +20
    Congress 13 | 8/306 | +20
    Congress 13 | 9/306 | +20
    Congress 13 | 10/306 | +20
    Congress 13 | 11/306 | +20
    Congress 13 | 12/306 | +20
    Congress 13 | 13/306 | +20
    Congress 13 | 14/306 | +20
    Congress 13 | 15/306 | +20
    Congress 13 | 16/306 | +20
    Congress 13 | 17/306 | +20
    Congress 13 | 18/306 | +20
    Congress 13 | 19/306 | +20
    Congress 13 | 20/306 | +20
    Congress 13 | 21/306 | +20
    Congress 13 | 22/306 | +20
    Congress 13 | 23/306 | +20
    Congress 13 | 24/306 | +20
    Congress 13 | 25/306 | +20
    Congress 13 | 26/306 | +20
    Congress 13 | 27/306 | +20
    Congress 13 | 28/306 | +20
    Congress 13 | 29/306 | +20
    Congress 13 | 30/306 | +20
    Congress 13 | 31/306 | +20
    Congress 13 | 32/306 | +20
    Congress 13 | 33/306 | +20
    Congress 13 | 34/306 | +20
    Congress 13 | 35/306 | +20
    Congress 13 | 36/306 | +20
    Congress 13 | 37/306 | +20
    Congress 13 | 38/306 | +20
    Congress 13 | 39/306 | +20
    Congress 13 | 40/306 | +20
    Congress 13 | 41/306 | +20
    Congress 13 | 42/306 | +20
    Congress 13 | 43/306 | +20
    Congress 13 | 44/306 | +20
    Congress 13 | 45/306 | +20
    Congress 13 | 46/306 | +20
    Congress 13 | 47/306 | +20
    Congress 13 | 48/306 | +20
    Congress 13 | 49/306 | +20
    Congress 13 | 50/306 | +20
    Congress 13 | 51/306 | +20
    Congress 13 | 52/306 | +20
    Congress 13 | 53/306 | +20
    Congress 13 | 54/306 | +20
    Congress 13 | 55/306 | +20
    Congress 13 | 56/306 | +20
    Congress 13 | 57/306 | +20
    Congress 13 | 58/306 | +20
    Congress 13 | 59/306 | +20
    Congress 13 | 60/306 | +20
    Congress 13 | 61/306 | +20
    Congress 13 | 62/306 | +20
    Congress 13 | 63/306 | +20
    Congress 13 | 64/306 | +20
    Congress 13 | 65/306 | +20
    Congress 13 | 66/306 | +20
    Congress 13 | 67/306 | +20
    Congress 13 | 68/306 | +20
    Congress 13 | 69/306 | +20
    Congress 13 | 70/306 | +20
    Congress 13 | 71/306 | +20
    Congress 13 | 72/306 | +20
    Congress 13 | 73/306 | +20
    Congress 13 | 74/306 | +20
    Congress 13 | 75/306 | +20
    Congress 13 | 76/306 | +20
    Congress 13 | 77/306 | +20
    Congress 13 | 78/306 | +20
    Congress 13 | 79/306 | +20
    Congress 13 | 80/306 | +20
    Congress 13 | 81/306 | +20
    Congress 13 | 82/306 | +20
    Congress 13 | 83/306 | +20
    Congress 13 | 84/306 | +20
    Congress 13 | 85/306 | +20
    Congress 13 | 86/306 | +20
    Congress 13 | 87/306 | +20
    Congress 13 | 88/306 | +20
    Congress 13 | 89/306 | +20
    Congress 13 | 90/306 | +20
    Congress 13 | 91/306 | +20
    Congress 13 | 92/306 | +20
    Congress 13 | 93/306 | +20
    Congress 13 | 94/306 | +20
    Congress 13 | 95/306 | +20
    Congress 13 | 96/306 | +20
    Congress 13 | 97/306 | +20
    Congress 13 | 98/306 | +20
    Congress 13 | 99/306 | +20
    Congress 13 | 100/306 | +20
    Congress 13 | 101/306 | +20
    Congress 13 | 102/306 | +20
    Congress 13 | 103/306 | +20
    Congress 13 | 104/306 | +20
    Congress 13 | 105/306 | +20
    Congress 13 | 106/306 | +20
    Congress 13 | 107/306 | +20
    Congress 13 | 108/306 | +20
    Congress 13 | 109/306 | +20
    Congress 13 | 110/306 | +20
    Congress 13 | 111/306 | +20
    Congress 13 | 112/306 | +20
    Congress 13 | 113/306 | +20
    Congress 13 | 114/306 | +20
    Congress 13 | 115/306 | +20
    Congress 13 | 116/306 | +20
    Congress 13 | 117/306 | +20
    Congress 13 | 118/306 | +20
    Congress 13 | 119/306 | +20
    Congress 13 | 120/306 | +20
    Congress 13 | 121/306 | +20
    Congress 13 | 122/306 | +20
    Congress 13 | 123/306 | +20
    Congress 13 | 124/306 | +20
    Congress 13 | 125/306 | +20
    Congress 13 | 126/306 | +20
    Congress 13 | 127/306 | +20
    Congress 13 | 128/306 | +20
    Congress 13 | 129/306 | +20
    Congress 13 | 130/306 | +20
    Congress 13 | 131/306 | +20
    Congress 13 | 132/306 | +20
    Congress 13 | 133/306 | +20
    Congress 13 | 134/306 | +20
    Congress 13 | 135/306 | +20
    Congress 13 | 136/306 | +20
    Congress 13 | 137/306 | +20
    Congress 13 | 138/306 | +20
    Congress 13 | 139/306 | +20
    Congress 13 | 140/306 | +20
    Congress 13 | 141/306 | +20
    Congress 13 | 142/306 | +20
    Congress 13 | 143/306 | +20
    Congress 13 | 144/306 | +20
    Congress 13 | 145/306 | +20
    Congress 13 | 146/306 | +20
    Congress 13 | 147/306 | +20
    Congress 13 | 148/306 | +20
    Congress 13 | 149/306 | +20
    Congress 13 | 150/306 | +20
    Congress 13 | 151/306 | +20
    Congress 13 | 152/306 | +20
    Congress 13 | 153/306 | +20
    Congress 13 | 154/306 | +20
    Congress 13 | 155/306 | +20
    Congress 13 | 156/306 | +20
    Congress 13 | 157/306 | +20
    Congress 13 | 158/306 | +20
    Congress 13 | 159/306 | +20
    Congress 13 | 160/306 | +20
    Congress 13 | 161/306 | +20
    Congress 13 | 162/306 | +20
    Congress 13 | 163/306 | +20
    Congress 13 | 164/306 | +20
    Congress 13 | 165/306 | +20
    Congress 13 | 166/306 | +20
    Congress 13 | 167/306 | +20
    Congress 13 | 168/306 | +20
    Congress 13 | 169/306 | +20
    Congress 13 | 170/306 | +20
    Congress 13 | 171/306 | +20
    Congress 13 | 172/306 | +20
    Congress 13 | 173/306 | +20
    Congress 13 | 174/306 | +20
    Congress 13 | 175/306 | +20
    Congress 13 | 176/306 | +20
    Congress 13 | 177/306 | +20
    Congress 13 | 178/306 | +20
    Congress 13 | 179/306 | +20
    Congress 13 | 180/306 | +20
    Congress 13 | 181/306 | +20
    Congress 13 | 182/306 | +20
    Congress 13 | 183/306 | +20
    Congress 13 | 184/306 | +20
    Congress 13 | 185/306 | +20
    Congress 13 | 186/306 | +20
    Congress 13 | 187/306 | +20
    Congress 13 | 188/306 | +20
    Congress 13 | 189/306 | +20
    Congress 13 | 190/306 | +20
    Congress 13 | 191/306 | +20
    Congress 13 | 192/306 | +20
    Congress 13 | 193/306 | +20
    Congress 13 | 194/306 | +20
    Congress 13 | 195/306 | +20
    Congress 13 | 196/306 | +20
    Congress 13 | 197/306 | +20
    Congress 13 | 198/306 | +20
    Congress 13 | 199/306 | +20
    Congress 13 | 200/306 | +20
    Congress 13 | 201/306 | +20
    Congress 13 | 202/306 | +20
    Congress 13 | 203/306 | +20
    Congress 13 | 204/306 | +20
    Congress 13 | 205/306 | +20
    Congress 13 | 206/306 | +20
    Congress 13 | 207/306 | +20
    Congress 13 | 208/306 | +20
    Congress 13 | 209/306 | +20
    Congress 13 | 210/306 | +20
    Congress 13 | 211/306 | +20
    Congress 13 | 212/306 | +20
    Congress 13 | 213/306 | +20
    Congress 13 | 214/306 | +20
    Congress 13 | 215/306 | +20
    Congress 13 | 216/306 | +20
    Congress 13 | 217/306 | +20
    Congress 13 | 218/306 | +20
    Congress 13 | 219/306 | +20
    Congress 13 | 220/306 | +20
    Congress 13 | 221/306 | +20
    Congress 13 | 222/306 | +20
    Congress 13 | 223/306 | +20
    Congress 13 | 224/306 | +20
    Congress 13 | 225/306 | +20
    Congress 13 | 226/306 | +20
    Congress 13 | 227/306 | +20
    Congress 13 | 228/306 | +20
    Congress 13 | 229/306 | +20
    Congress 13 | 230/306 | +20
    Congress 13 | 231/306 | +20
    Congress 13 | 232/306 | +20
    Congress 13 | 233/306 | +20
    Congress 13 | 234/306 | +20
    Congress 13 | 235/306 | +20
    Congress 13 | 236/306 | +20
    Congress 13 | 237/306 | +20
    Congress 13 | 238/306 | +20
    Congress 13 | 239/306 | +20
    Congress 13 | 240/306 | +20
    Congress 13 | 241/306 | +20
    Congress 13 | 242/306 | +20
    Congress 13 | 243/306 | +20
    Congress 13 | 244/306 | +20
    Congress 13 | 245/306 | +20
    Congress 13 | 246/306 | +20
    Congress 13 | 247/306 | +20
    Congress 13 | 248/306 | +20
    Congress 13 | 249/306 | +20
    Congress 13 | 250/306 | +20
    Congress 13 | 251/306 | +20
    Congress 13 | 252/306 | +20
    Congress 13 | 253/306 | +20
    Congress 13 | 254/306 | +20
    Congress 13 | 255/306 | +20
    Congress 13 | 256/306 | +20
    Congress 13 | 257/306 | +20
    Congress 13 | 258/306 | +20
    Congress 13 | 259/306 | +20
    Congress 13 | 260/306 | +20
    Congress 13 | 261/306 | +20
    Congress 13 | 262/306 | +20
    Congress 13 | 263/306 | +20
    Congress 13 | 264/306 | +20
    Congress 13 | 265/306 | +20
    Congress 13 | 266/306 | +20
    Congress 13 | 267/306 | +20
    Congress 13 | 268/306 | +20
    Congress 13 | 269/306 | +20
    Congress 13 | 270/306 | +20
    Congress 13 | 271/306 | +20
    Congress 13 | 272/306 | +20
    Congress 13 | 273/306 | +20
    Congress 13 | 274/306 | +20
    Congress 13 | 275/306 | +20
    Congress 13 | 276/306 | +20
    Congress 13 | 277/306 | +20
    Congress 13 | 278/306 | +20
    Congress 13 | 279/306 | +20
    Congress 13 | 280/306 | +20
    Congress 13 | 281/306 | +20
    Congress 13 | 282/306 | +20
    Congress 13 | 283/306 | +20
    Congress 13 | 284/306 | +20
    Congress 13 | 285/306 | +20
    Congress 13 | 286/306 | +20
    Congress 13 | 287/306 | +20
    Congress 13 | 288/306 | +20
    Congress 13 | 289/306 | +20
    Congress 13 | 290/306 | +20
    Congress 13 | 291/306 | +20
    Congress 13 | 292/306 | +20
    Congress 13 | 293/306 | +20
    Congress 13 | 294/306 | +20
    Congress 13 | 295/306 | +20
    Congress 13 | 296/306 | +20
    Congress 13 | 297/306 | +20
    Congress 13 | 298/306 | +20
    Congress 13 | 299/306 | +20
    Congress 13 | 300/306 | +20
    Congress 13 | 301/306 | +20
    Congress 13 | 302/306 | +20
    Congress 13 | 303/306 | +20
    Congress 13 | 304/306 | +20
    Congress 13 | 305/306 | +20
    Congress 13 | 306/306 | +16
    
    🔄 Congress 14
    Congress 14 | 1/361 | +20
    Congress 14 | 2/361 | +20
    Congress 14 | 3/361 | +20
    Congress 14 | 4/361 | +20
    Congress 14 | 5/361 | +20
    Congress 14 | 6/361 | +20
    Congress 14 | 7/361 | +20
    Congress 14 | 8/361 | +20
    Congress 14 | 9/361 | +20
    Congress 14 | 10/361 | +20
    Congress 14 | 11/361 | +20
    Congress 14 | 12/361 | +20
    Congress 14 | 13/361 | +20
    Congress 14 | 14/361 | +20
    Congress 14 | 15/361 | +20
    Congress 14 | 16/361 | +20
    Congress 14 | 17/361 | +20
    Congress 14 | 18/361 | +20
    Congress 14 | 19/361 | +20
    Congress 14 | 20/361 | +20
    Congress 14 | 21/361 | +20
    Congress 14 | 22/361 | +20
    Congress 14 | 23/361 | +20
    Congress 14 | 24/361 | +20
    Congress 14 | 25/361 | +20
    Congress 14 | 26/361 | +20
    Congress 14 | 27/361 | +20
    Congress 14 | 28/361 | +20
    Congress 14 | 29/361 | +20
    Congress 14 | 30/361 | +20
    Congress 14 | 31/361 | +20
    Congress 14 | 32/361 | +20
    Congress 14 | 33/361 | +20
    Congress 14 | 34/361 | +20
    Congress 14 | 35/361 | +20
    Congress 14 | 36/361 | +20
    Congress 14 | 37/361 | +20
    Congress 14 | 38/361 | +20
    Congress 14 | 39/361 | +20
    Congress 14 | 40/361 | +20
    Congress 14 | 41/361 | +20
    Congress 14 | 42/361 | +20
    Congress 14 | 43/361 | +20
    Congress 14 | 44/361 | +20
    Congress 14 | 45/361 | +20
    Congress 14 | 46/361 | +20
    Congress 14 | 47/361 | +20
    Congress 14 | 48/361 | +20
    Congress 14 | 49/361 | +20
    Congress 14 | 50/361 | +20
    Congress 14 | 51/361 | +20
    Congress 14 | 52/361 | +20
    Congress 14 | 53/361 | +20
    Congress 14 | 54/361 | +20
    Congress 14 | 55/361 | +20
    Congress 14 | 56/361 | +20
    Congress 14 | 57/361 | +20
    Congress 14 | 58/361 | +20
    Congress 14 | 59/361 | +20
    Congress 14 | 60/361 | +20
    Congress 14 | 61/361 | +20
    Congress 14 | 62/361 | +20
    Congress 14 | 63/361 | +20
    Congress 14 | 64/361 | +20
    Congress 14 | 65/361 | +20
    Congress 14 | 66/361 | +20
    Congress 14 | 67/361 | +20
    Congress 14 | 68/361 | +20
    Congress 14 | 69/361 | +20
    Congress 14 | 70/361 | +20
    Congress 14 | 71/361 | +20
    Congress 14 | 72/361 | +20
    Congress 14 | 73/361 | +20
    Congress 14 | 74/361 | +20
    Congress 14 | 75/361 | +20
    Congress 14 | 76/361 | +20
    Congress 14 | 77/361 | +20
    Congress 14 | 78/361 | +20
    Congress 14 | 79/361 | +20
    Congress 14 | 80/361 | +20
    Congress 14 | 81/361 | +20
    Congress 14 | 82/361 | +20
    Congress 14 | 83/361 | +20
    Congress 14 | 84/361 | +20
    Congress 14 | 85/361 | +20
    Congress 14 | 86/361 | +20
    Congress 14 | 87/361 | +20
    Congress 14 | 88/361 | +20
    Congress 14 | 89/361 | +20
    Congress 14 | 90/361 | +20
    Congress 14 | 91/361 | +20
    Congress 14 | 92/361 | +20
    Congress 14 | 93/361 | +20
    Congress 14 | 94/361 | +20
    Congress 14 | 95/361 | +20
    Congress 14 | 96/361 | +20
    Congress 14 | 97/361 | +20
    Congress 14 | 98/361 | +20
    Congress 14 | 99/361 | +20
    Congress 14 | 100/361 | +20
    Congress 14 | 101/361 | +20
    Congress 14 | 102/361 | +20
    Congress 14 | 103/361 | +20
    Congress 14 | 104/361 | +20
    Congress 14 | 105/361 | +20
    Congress 14 | 106/361 | +20
    Congress 14 | 107/361 | +20
    Congress 14 | 108/361 | +20
    Congress 14 | 109/361 | +20
    Congress 14 | 110/361 | +20
    Congress 14 | 111/361 | +20
    Congress 14 | 112/361 | +20
    Congress 14 | 113/361 | +20
    Congress 14 | 114/361 | +20
    Congress 14 | 115/361 | +20
    Congress 14 | 116/361 | +20
    Congress 14 | 117/361 | +20
    Congress 14 | 118/361 | +20
    Congress 14 | 119/361 | +20
    Congress 14 | 120/361 | +20
    Congress 14 | 121/361 | +20
    Congress 14 | 122/361 | +20
    Congress 14 | 123/361 | +20
    Congress 14 | 124/361 | +20
    Congress 14 | 125/361 | +20
    Congress 14 | 126/361 | +20
    Congress 14 | 127/361 | +20
    Congress 14 | 128/361 | +20
    Congress 14 | 129/361 | +20
    Congress 14 | 130/361 | +20
    Congress 14 | 131/361 | +20
    Congress 14 | 132/361 | +20
    Congress 14 | 133/361 | +20
    Congress 14 | 134/361 | +20
    Congress 14 | 135/361 | +20
    Congress 14 | 136/361 | +20
    Congress 14 | 137/361 | +20
    Congress 14 | 138/361 | +20
    Congress 14 | 139/361 | +20
    Congress 14 | 140/361 | +20
    Congress 14 | 141/361 | +20
    Congress 14 | 142/361 | +20
    Congress 14 | 143/361 | +20
    Congress 14 | 144/361 | +20
    Congress 14 | 145/361 | +20
    Congress 14 | 146/361 | +20
    Congress 14 | 147/361 | +20
    Congress 14 | 148/361 | +20
    Congress 14 | 149/361 | +20
    Congress 14 | 150/361 | +20
    Congress 14 | 151/361 | +20
    Congress 14 | 152/361 | +20
    Congress 14 | 153/361 | +20
    Congress 14 | 154/361 | +20
    Congress 14 | 155/361 | +20
    Congress 14 | 156/361 | +20
    Congress 14 | 157/361 | +20
    Congress 14 | 158/361 | +20
    Congress 14 | 159/361 | +20
    Congress 14 | 160/361 | +20
    Congress 14 | 161/361 | +20
    Congress 14 | 162/361 | +20
    Congress 14 | 163/361 | +20
    Congress 14 | 164/361 | +20
    Congress 14 | 165/361 | +20
    Congress 14 | 166/361 | +20
    Congress 14 | 167/361 | +20
    Congress 14 | 168/361 | +20
    Congress 14 | 169/361 | +20
    Congress 14 | 170/361 | +20
    Congress 14 | 171/361 | +20
    Congress 14 | 172/361 | +20
    Congress 14 | 173/361 | +20
    Congress 14 | 174/361 | +20
    Congress 14 | 175/361 | +20
    Congress 14 | 176/361 | +20
    Congress 14 | 177/361 | +20
    Congress 14 | 178/361 | +20
    Congress 14 | 179/361 | +20
    Congress 14 | 180/361 | +20
    Congress 14 | 181/361 | +20
    Congress 14 | 182/361 | +20
    Congress 14 | 183/361 | +20
    Congress 14 | 184/361 | +20
    Congress 14 | 185/361 | +20
    Congress 14 | 186/361 | +20
    Congress 14 | 187/361 | +20
    Congress 14 | 188/361 | +20
    Congress 14 | 189/361 | +20
    Congress 14 | 190/361 | +20
    Congress 14 | 191/361 | +20
    Congress 14 | 192/361 | +20
    Congress 14 | 193/361 | +20
    Congress 14 | 194/361 | +20
    Congress 14 | 195/361 | +20
    Congress 14 | 196/361 | +20
    Congress 14 | 197/361 | +20
    Congress 14 | 198/361 | +20
    Congress 14 | 199/361 | +20
    Congress 14 | 200/361 | +20
    Congress 14 | 201/361 | +20
    Congress 14 | 202/361 | +20
    Congress 14 | 203/361 | +20
    Congress 14 | 204/361 | +20
    Congress 14 | 205/361 | +20
    Congress 14 | 206/361 | +20
    Congress 14 | 207/361 | +20
    Congress 14 | 208/361 | +20
    Congress 14 | 209/361 | +20
    Congress 14 | 210/361 | +20
    Congress 14 | 211/361 | +20
    Congress 14 | 212/361 | +20
    Congress 14 | 213/361 | +20
    Congress 14 | 214/361 | +20
    Congress 14 | 215/361 | +20
    Congress 14 | 216/361 | +20
    Congress 14 | 217/361 | +20
    Congress 14 | 218/361 | +20
    Congress 14 | 219/361 | +20
    Congress 14 | 220/361 | +20
    Congress 14 | 221/361 | +20
    Congress 14 | 222/361 | +20
    Congress 14 | 223/361 | +20
    Congress 14 | 224/361 | +20
    Congress 14 | 225/361 | +20
    Congress 14 | 226/361 | +20
    Congress 14 | 227/361 | +20
    Congress 14 | 228/361 | +20
    Congress 14 | 229/361 | +20
    Congress 14 | 230/361 | +20
    Congress 14 | 231/361 | +20
    Congress 14 | 232/361 | +20
    Congress 14 | 233/361 | +20
    Congress 14 | 234/361 | +20
    Congress 14 | 235/361 | +20
    Congress 14 | 236/361 | +20
    Congress 14 | 237/361 | +20
    Congress 14 | 238/361 | +20
    Congress 14 | 239/361 | +20
    Congress 14 | 240/361 | +20
    Congress 14 | 241/361 | +20
    Congress 14 | 242/361 | +20
    Congress 14 | 243/361 | +20
    Congress 14 | 244/361 | +20
    Congress 14 | 245/361 | +20
    Congress 14 | 246/361 | +20
    Congress 14 | 247/361 | +20
    Congress 14 | 248/361 | +20
    Congress 14 | 249/361 | +20
    Congress 14 | 250/361 | +20
    Congress 14 | 251/361 | +20
    Congress 14 | 252/361 | +20
    Congress 14 | 253/361 | +20
    Congress 14 | 254/361 | +20
    Congress 14 | 255/361 | +20
    Congress 14 | 256/361 | +20
    Congress 14 | 257/361 | +20
    Congress 14 | 258/361 | +20
    Congress 14 | 259/361 | +20
    Congress 14 | 260/361 | +20
    Congress 14 | 261/361 | +20
    Congress 14 | 262/361 | +20
    Congress 14 | 263/361 | +20
    Congress 14 | 264/361 | +20
    Congress 14 | 265/361 | +20
    Congress 14 | 266/361 | +20
    Congress 14 | 267/361 | +20
    Congress 14 | 268/361 | +20
    Congress 14 | 269/361 | +20
    Congress 14 | 270/361 | +20
    Congress 14 | 271/361 | +20
    Congress 14 | 272/361 | +20
    Congress 14 | 273/361 | +20
    Congress 14 | 274/361 | +20
    Congress 14 | 275/361 | +20
    Congress 14 | 276/361 | +20
    Congress 14 | 277/361 | +20
    Congress 14 | 278/361 | +20
    Congress 14 | 279/361 | +20
    Congress 14 | 280/361 | +20
    Congress 14 | 281/361 | +20
    Congress 14 | 282/361 | +20
    Congress 14 | 283/361 | +20
    Congress 14 | 284/361 | +20
    Congress 14 | 285/361 | +20
    Congress 14 | 286/361 | +20
    Congress 14 | 287/361 | +20
    Congress 14 | 288/361 | +20
    Congress 14 | 289/361 | +20
    Congress 14 | 290/361 | +20
    Congress 14 | 291/361 | +20
    Congress 14 | 292/361 | +20
    Congress 14 | 293/361 | +20
    Congress 14 | 294/361 | +20
    Congress 14 | 295/361 | +20
    Congress 14 | 296/361 | +20
    Congress 14 | 297/361 | +20
    Congress 14 | 298/361 | +20
    Congress 14 | 299/361 | +20
    Congress 14 | 300/361 | +20
    Congress 14 | 301/361 | +20
    Congress 14 | 302/361 | +20
    Congress 14 | 303/361 | +20
    Congress 14 | 304/361 | +20
    Congress 14 | 305/361 | +20
    Congress 14 | 306/361 | +20
    Congress 14 | 307/361 | +20
    Congress 14 | 308/361 | +20
    Congress 14 | 309/361 | +20
    Congress 14 | 310/361 | +20
    Congress 14 | 311/361 | +20
    Congress 14 | 312/361 | +20
    Congress 14 | 313/361 | +20
    Congress 14 | 314/361 | +20
    Congress 14 | 315/361 | +20
    Congress 14 | 316/361 | +20
    Congress 14 | 317/361 | +20
    Congress 14 | 318/361 | +20
    Congress 14 | 319/361 | +20
    Congress 14 | 320/361 | +20
    Congress 14 | 321/361 | +20
    Congress 14 | 322/361 | +20
    Congress 14 | 323/361 | +20
    Congress 14 | 324/361 | +20
    Congress 14 | 325/361 | +20
    Congress 14 | 326/361 | +20
    Congress 14 | 327/361 | +20
    Congress 14 | 328/361 | +20
    Congress 14 | 329/361 | +20
    Congress 14 | 330/361 | +20
    Congress 14 | 331/361 | +20
    Congress 14 | 332/361 | +20
    Congress 14 | 333/361 | +20
    Congress 14 | 334/361 | +20
    Congress 14 | 335/361 | +20
    Congress 14 | 336/361 | +20
    Congress 14 | 337/361 | +20
    Congress 14 | 338/361 | +20
    Congress 14 | 339/361 | +20
    Congress 14 | 340/361 | +20
    Congress 14 | 341/361 | +20
    Congress 14 | 342/361 | +20
    Congress 14 | 343/361 | +20
    Congress 14 | 344/361 | +20
    Congress 14 | 345/361 | +20
    Congress 14 | 346/361 | +20
    Congress 14 | 347/361 | +20
    Congress 14 | 348/361 | +20
    Congress 14 | 349/361 | +20
    Congress 14 | 350/361 | +20
    Congress 14 | 351/361 | +20
    Congress 14 | 352/361 | +20
    Congress 14 | 353/361 | +20
    Congress 14 | 354/361 | +20
    Congress 14 | 355/361 | +20
    Congress 14 | 356/361 | +20
    Congress 14 | 357/361 | +20
    Congress 14 | 358/361 | +20
    Congress 14 | 359/361 | +20
    Congress 14 | 360/361 | +20
    Congress 14 | 361/361 | +4
    
    🔄 Congress 15
    Congress 15 | 1/348 | +20
    Congress 15 | 2/348 | +20
    Congress 15 | 3/348 | +20
    Congress 15 | 4/348 | +20
    Congress 15 | 5/348 | +20
    Congress 15 | 6/348 | +20
    Congress 15 | 7/348 | +20
    Congress 15 | 8/348 | +20
    Congress 15 | 9/348 | +20
    Congress 15 | 10/348 | +20
    Congress 15 | 11/348 | +20
    Congress 15 | 12/348 | +20
    Congress 15 | 13/348 | +20
    Congress 15 | 14/348 | +20
    Congress 15 | 15/348 | +20
    Congress 15 | 16/348 | +20
    Congress 15 | 17/348 | +20
    Congress 15 | 18/348 | +20
    Congress 15 | 19/348 | +20
    Congress 15 | 20/348 | +20
    Congress 15 | 21/348 | +20
    Congress 15 | 22/348 | +20
    Congress 15 | 23/348 | +20
    Congress 15 | 24/348 | +20
    Congress 15 | 25/348 | +20
    Congress 15 | 26/348 | +20
    Congress 15 | 27/348 | +20
    Congress 15 | 28/348 | +20
    Congress 15 | 29/348 | +20
    Congress 15 | 30/348 | +20
    Congress 15 | 31/348 | +20
    Congress 15 | 32/348 | +20
    Congress 15 | 33/348 | +20
    Congress 15 | 34/348 | +20
    Congress 15 | 35/348 | +20
    Congress 15 | 36/348 | +20
    Congress 15 | 37/348 | +20
    Congress 15 | 38/348 | +20
    Congress 15 | 39/348 | +20
    Congress 15 | 40/348 | +20
    Congress 15 | 41/348 | +20
    Congress 15 | 42/348 | +20
    Congress 15 | 43/348 | +20
    Congress 15 | 44/348 | +20
    Congress 15 | 45/348 | +20
    Congress 15 | 46/348 | +20
    Congress 15 | 47/348 | +20
    Congress 15 | 48/348 | +20
    Congress 15 | 49/348 | +20
    Congress 15 | 50/348 | +20
    Congress 15 | 51/348 | +20
    Congress 15 | 52/348 | +20
    Congress 15 | 53/348 | +20
    Congress 15 | 54/348 | +20
    Congress 15 | 55/348 | +20
    Congress 15 | 56/348 | +20
    Congress 15 | 57/348 | +20
    Congress 15 | 58/348 | +20
    Congress 15 | 59/348 | +20
    Congress 15 | 60/348 | +20
    Congress 15 | 61/348 | +20
    Congress 15 | 62/348 | +20
    Congress 15 | 63/348 | +20
    Congress 15 | 64/348 | +20
    Congress 15 | 65/348 | +20
    Congress 15 | 66/348 | +20
    Congress 15 | 67/348 | +20
    Congress 15 | 68/348 | +20
    Congress 15 | 69/348 | +20
    Congress 15 | 70/348 | +20
    Congress 15 | 71/348 | +20
    Congress 15 | 72/348 | +20
    Congress 15 | 73/348 | +20
    Congress 15 | 74/348 | +20
    Congress 15 | 75/348 | +20
    Congress 15 | 76/348 | +20
    Congress 15 | 77/348 | +20
    Congress 15 | 78/348 | +20
    Congress 15 | 79/348 | +20
    Congress 15 | 80/348 | +20
    Congress 15 | 81/348 | +20
    Congress 15 | 82/348 | +20
    Congress 15 | 83/348 | +20
    Congress 15 | 84/348 | +20
    Congress 15 | 85/348 | +20
    Congress 15 | 86/348 | +20
    Congress 15 | 87/348 | +20
    Congress 15 | 88/348 | +20
    Congress 15 | 89/348 | +20
    Congress 15 | 90/348 | +20
    Congress 15 | 91/348 | +20
    Congress 15 | 92/348 | +20
    Congress 15 | 93/348 | +20
    Congress 15 | 94/348 | +20
    Congress 15 | 95/348 | +20
    Congress 15 | 96/348 | +20
    Congress 15 | 97/348 | +20
    Congress 15 | 98/348 | +20
    Congress 15 | 99/348 | +20
    Congress 15 | 100/348 | +20
    Congress 15 | 101/348 | +20
    Congress 15 | 102/348 | +20
    Congress 15 | 103/348 | +20
    Congress 15 | 104/348 | +20
    Congress 15 | 105/348 | +20
    Congress 15 | 106/348 | +20
    Congress 15 | 107/348 | +20
    Congress 15 | 108/348 | +20
    Congress 15 | 109/348 | +20
    Congress 15 | 110/348 | +20
    Congress 15 | 111/348 | +20
    Congress 15 | 112/348 | +20
    Congress 15 | 113/348 | +20
    Congress 15 | 114/348 | +20
    Congress 15 | 115/348 | +20
    Congress 15 | 116/348 | +20
    Congress 15 | 117/348 | +20
    Congress 15 | 118/348 | +20
    Congress 15 | 119/348 | +20
    Congress 15 | 120/348 | +20
    Congress 15 | 121/348 | +20
    Congress 15 | 122/348 | +20
    Congress 15 | 123/348 | +20
    Congress 15 | 124/348 | +20
    Congress 15 | 125/348 | +20
    Congress 15 | 126/348 | +20
    Congress 15 | 127/348 | +20
    Congress 15 | 128/348 | +20
    Congress 15 | 129/348 | +20
    Congress 15 | 130/348 | +20
    Congress 15 | 131/348 | +20
    Congress 15 | 132/348 | +20
    Congress 15 | 133/348 | +20
    Congress 15 | 134/348 | +20
    Congress 15 | 135/348 | +20
    Congress 15 | 136/348 | +20
    Congress 15 | 137/348 | +20
    Congress 15 | 138/348 | +20
    Congress 15 | 139/348 | +20
    Congress 15 | 140/348 | +20
    Congress 15 | 141/348 | +20
    Congress 15 | 142/348 | +20
    Congress 15 | 143/348 | +20
    Congress 15 | 144/348 | +20
    Congress 15 | 145/348 | +20
    Congress 15 | 146/348 | +20
    Congress 15 | 147/348 | +20
    Congress 15 | 148/348 | +20
    Congress 15 | 149/348 | +20
    Congress 15 | 150/348 | +20
    Congress 15 | 151/348 | +20
    Congress 15 | 152/348 | +20
    Congress 15 | 153/348 | +20
    Congress 15 | 154/348 | +20
    Congress 15 | 155/348 | +20
    Congress 15 | 156/348 | +20
    Congress 15 | 157/348 | +20
    Congress 15 | 158/348 | +20
    Congress 15 | 159/348 | +20
    Congress 15 | 160/348 | +20
    Congress 15 | 161/348 | +20
    Congress 15 | 162/348 | +20
    Congress 15 | 163/348 | +20
    Congress 15 | 164/348 | +20
    Congress 15 | 165/348 | +20
    Congress 15 | 166/348 | +20
    Congress 15 | 167/348 | +20
    Congress 15 | 168/348 | +20
    Congress 15 | 169/348 | +20
    Congress 15 | 170/348 | +20
    Congress 15 | 171/348 | +20
    Congress 15 | 172/348 | +20
    Congress 15 | 173/348 | +20
    Congress 15 | 174/348 | +20
    Congress 15 | 175/348 | +20
    Congress 15 | 176/348 | +20
    Congress 15 | 177/348 | +20
    Congress 15 | 178/348 | +20
    Congress 15 | 179/348 | +20
    Congress 15 | 180/348 | +20
    Congress 15 | 181/348 | +20
    Congress 15 | 182/348 | +20
    Congress 15 | 183/348 | +20
    Congress 15 | 184/348 | +20
    Congress 15 | 185/348 | +20
    Congress 15 | 186/348 | +20
    Congress 15 | 187/348 | +20
    Congress 15 | 188/348 | +20
    Congress 15 | 189/348 | +20
    Congress 15 | 190/348 | +20
    Congress 15 | 191/348 | +20
    Congress 15 | 192/348 | +20
    Congress 15 | 193/348 | +20
    Congress 15 | 194/348 | +20
    Congress 15 | 195/348 | +20
    Congress 15 | 196/348 | +20
    Congress 15 | 197/348 | +20
    Congress 15 | 198/348 | +20
    Congress 15 | 199/348 | +20
    Congress 15 | 200/348 | +20
    Congress 15 | 201/348 | +20
    Congress 15 | 202/348 | +20
    Congress 15 | 203/348 | +20
    Congress 15 | 204/348 | +20
    Congress 15 | 205/348 | +20
    Congress 15 | 206/348 | +20
    Congress 15 | 207/348 | +20
    Congress 15 | 208/348 | +20
    Congress 15 | 209/348 | +20
    Congress 15 | 210/348 | +20
    Congress 15 | 211/348 | +20
    Congress 15 | 212/348 | +20
    Congress 15 | 213/348 | +20
    Congress 15 | 214/348 | +20
    Congress 15 | 215/348 | +20
    Congress 15 | 216/348 | +20
    Congress 15 | 217/348 | +20
    Congress 15 | 218/348 | +20
    Congress 15 | 219/348 | +20
    Congress 15 | 220/348 | +20
    Congress 15 | 221/348 | +20
    Congress 15 | 222/348 | +20
    Congress 15 | 223/348 | +20
    Congress 15 | 224/348 | +20
    Congress 15 | 225/348 | +20
    Congress 15 | 226/348 | +20
    Congress 15 | 227/348 | +20
    Congress 15 | 228/348 | +20
    Congress 15 | 229/348 | +20
    Congress 15 | 230/348 | +20
    Congress 15 | 231/348 | +20
    Congress 15 | 232/348 | +20
    Congress 15 | 233/348 | +20
    Congress 15 | 234/348 | +20
    Congress 15 | 235/348 | +20
    Congress 15 | 236/348 | +20
    Congress 15 | 237/348 | +20
    Congress 15 | 238/348 | +20
    Congress 15 | 239/348 | +20
    Congress 15 | 240/348 | +20
    Congress 15 | 241/348 | +20
    Congress 15 | 242/348 | +20
    Congress 15 | 243/348 | +20
    Congress 15 | 244/348 | +20
    Congress 15 | 245/348 | +20
    Congress 15 | 246/348 | +20
    Congress 15 | 247/348 | +20
    Congress 15 | 248/348 | +20
    Congress 15 | 249/348 | +20
    Congress 15 | 250/348 | +20
    Congress 15 | 251/348 | +20
    Congress 15 | 252/348 | +20
    Congress 15 | 253/348 | +20
    Congress 15 | 254/348 | +20
    Congress 15 | 255/348 | +20
    Congress 15 | 256/348 | +20
    Congress 15 | 257/348 | +20
    Congress 15 | 258/348 | +20
    Congress 15 | 259/348 | +20
    Congress 15 | 260/348 | +20
    Congress 15 | 261/348 | +20
    Congress 15 | 262/348 | +20
    Congress 15 | 263/348 | +20
    Congress 15 | 264/348 | +20
    Congress 15 | 265/348 | +20
    Congress 15 | 266/348 | +20
    Congress 15 | 267/348 | +20
    Congress 15 | 268/348 | +20
    Congress 15 | 269/348 | +20
    Congress 15 | 270/348 | +20
    Congress 15 | 271/348 | +20
    Congress 15 | 272/348 | +20
    Congress 15 | 273/348 | +20
    Congress 15 | 274/348 | +20
    Congress 15 | 275/348 | +20
    Congress 15 | 276/348 | +20
    Congress 15 | 277/348 | +20
    Congress 15 | 278/348 | +20
    Congress 15 | 279/348 | +20
    Congress 15 | 280/348 | +20
    Congress 15 | 281/348 | +20
    Congress 15 | 282/348 | +20
    Congress 15 | 283/348 | +20
    Congress 15 | 284/348 | +20
    Congress 15 | 285/348 | +20
    Congress 15 | 286/348 | +20
    Congress 15 | 287/348 | +20
    Congress 15 | 288/348 | +20
    Congress 15 | 289/348 | +20
    Congress 15 | 290/348 | +20
    Congress 15 | 291/348 | +20
    Congress 15 | 292/348 | +20
    Congress 15 | 293/348 | +20
    Congress 15 | 294/348 | +20
    Congress 15 | 295/348 | +20
    Congress 15 | 296/348 | +20
    Congress 15 | 297/348 | +20
    Congress 15 | 298/348 | +20
    Congress 15 | 299/348 | +20
    Congress 15 | 300/348 | +20
    Congress 15 | 301/348 | +20
    Congress 15 | 302/348 | +20
    Congress 15 | 303/348 | +20
    Congress 15 | 304/348 | +20
    Congress 15 | 305/348 | +20
    Congress 15 | 306/348 | +20
    Congress 15 | 307/348 | +20
    Congress 15 | 308/348 | +20
    Congress 15 | 309/348 | +20
    Congress 15 | 310/348 | +20
    Congress 15 | 311/348 | +20
    Congress 15 | 312/348 | +20
    Congress 15 | 313/348 | +20
    Congress 15 | 314/348 | +20
    Congress 15 | 315/348 | +20
    Congress 15 | 316/348 | +20
    Congress 15 | 317/348 | +20
    Congress 15 | 318/348 | +20
    Congress 15 | 319/348 | +20
    Congress 15 | 320/348 | +20
    Congress 15 | 321/348 | +20
    Congress 15 | 322/348 | +20
    Congress 15 | 323/348 | +20
    Congress 15 | 324/348 | +20
    Congress 15 | 325/348 | +20
    Congress 15 | 326/348 | +20
    Congress 15 | 327/348 | +20
    Congress 15 | 328/348 | +20
    Congress 15 | 329/348 | +20
    Congress 15 | 330/348 | +20
    Congress 15 | 331/348 | +20
    Congress 15 | 332/348 | +20
    Congress 15 | 333/348 | +20
    Congress 15 | 334/348 | +20
    Congress 15 | 335/348 | +20
    Congress 15 | 336/348 | +20
    Congress 15 | 337/348 | +20
    Congress 15 | 338/348 | +20
    Congress 15 | 339/348 | +20
    Congress 15 | 340/348 | +20
    Congress 15 | 341/348 | +20
    Congress 15 | 342/348 | +20
    Congress 15 | 343/348 | +20
    Congress 15 | 344/348 | +20
    Congress 15 | 345/348 | +20
    Congress 15 | 346/348 | +20
    Congress 15 | 347/348 | +2
    Congress 15 | 348/348 | +20
    
    🔄 Congress 16
    Congress 16 | 1/327 | +20
    Congress 16 | 2/327 | +20
    Congress 16 | 3/327 | +20
    Congress 16 | 4/327 | +20
    Congress 16 | 5/327 | +20
    Congress 16 | 6/327 | +20
    Congress 16 | 7/327 | +20
    Congress 16 | 8/327 | +20
    Congress 16 | 9/327 | +20
    Congress 16 | 10/327 | +20
    Congress 16 | 11/327 | +20
    Congress 16 | 12/327 | +20
    Congress 16 | 13/327 | +20
    Congress 16 | 14/327 | +20
    Congress 16 | 15/327 | +20
    Congress 16 | 16/327 | +20
    Congress 16 | 17/327 | +20
    Congress 16 | 18/327 | +20
    Congress 16 | 19/327 | +20
    Congress 16 | 20/327 | +20
    Congress 16 | 21/327 | +20
    Congress 16 | 22/327 | +20
    Congress 16 | 23/327 | +20
    Congress 16 | 24/327 | +20
    Congress 16 | 25/327 | +20
    Congress 16 | 26/327 | +20
    Congress 16 | 27/327 | +20
    Congress 16 | 28/327 | +20
    Congress 16 | 29/327 | +20
    Congress 16 | 30/327 | +20
    Congress 16 | 31/327 | +20
    Congress 16 | 32/327 | +20
    Congress 16 | 33/327 | +20
    Congress 16 | 34/327 | +20
    Congress 16 | 35/327 | +20
    Congress 16 | 36/327 | +20
    Congress 16 | 37/327 | +20
    Congress 16 | 38/327 | +20
    Congress 16 | 39/327 | +20
    Congress 16 | 40/327 | +20
    Congress 16 | 41/327 | +20
    Congress 16 | 42/327 | +20
    Congress 16 | 43/327 | +20
    Congress 16 | 44/327 | +20
    Congress 16 | 45/327 | +20
    Congress 16 | 46/327 | +20
    Congress 16 | 47/327 | +20
    Congress 16 | 48/327 | +20
    Congress 16 | 49/327 | +20
    Congress 16 | 50/327 | +20
    Congress 16 | 51/327 | +20
    Congress 16 | 52/327 | +20
    Congress 16 | 53/327 | +20
    Congress 16 | 54/327 | +20
    Congress 16 | 55/327 | +20
    Congress 16 | 56/327 | +20
    Congress 16 | 57/327 | +20
    Congress 16 | 58/327 | +20
    Congress 16 | 59/327 | +20
    Congress 16 | 60/327 | +20
    Congress 16 | 61/327 | +20
    Congress 16 | 62/327 | +20
    Congress 16 | 63/327 | +20
    Congress 16 | 64/327 | +20
    Congress 16 | 65/327 | +20
    Congress 16 | 66/327 | +20
    Congress 16 | 67/327 | +20
    Congress 16 | 68/327 | +20
    Congress 16 | 69/327 | +20
    Congress 16 | 70/327 | +20
    Congress 16 | 71/327 | +20
    Congress 16 | 72/327 | +20
    Congress 16 | 73/327 | +20
    Congress 16 | 74/327 | +20
    Congress 16 | 75/327 | +20
    Congress 16 | 76/327 | +20
    Congress 16 | 77/327 | +20
    Congress 16 | 78/327 | +20
    Congress 16 | 79/327 | +20
    Congress 16 | 80/327 | +20
    Congress 16 | 81/327 | +20
    Congress 16 | 82/327 | +20
    Congress 16 | 83/327 | +20
    Congress 16 | 84/327 | +20
    Congress 16 | 85/327 | +20
    Congress 16 | 86/327 | +20
    Congress 16 | 87/327 | +20
    Congress 16 | 88/327 | +20
    Congress 16 | 89/327 | +20
    Congress 16 | 90/327 | +20
    Congress 16 | 91/327 | +20
    Congress 16 | 92/327 | +20
    Congress 16 | 93/327 | +20
    Congress 16 | 94/327 | +20
    Congress 16 | 95/327 | +20
    Congress 16 | 96/327 | +20
    Congress 16 | 97/327 | +20
    Congress 16 | 98/327 | +20
    Congress 16 | 99/327 | +20
    Congress 16 | 100/327 | +20
    Congress 16 | 101/327 | +20
    Congress 16 | 102/327 | +20
    Congress 16 | 103/327 | +20
    Congress 16 | 104/327 | +20
    Congress 16 | 105/327 | +20
    Congress 16 | 106/327 | +20
    Congress 16 | 107/327 | +20
    Congress 16 | 108/327 | +20
    Congress 16 | 109/327 | +20
    Congress 16 | 110/327 | +20
    Congress 16 | 111/327 | +20
    Congress 16 | 112/327 | +20
    Congress 16 | 113/327 | +20
    Congress 16 | 114/327 | +20
    Congress 16 | 115/327 | +20
    Congress 16 | 116/327 | +20
    Congress 16 | 117/327 | +20
    Congress 16 | 118/327 | +20
    Congress 16 | 119/327 | +20
    Congress 16 | 120/327 | +20
    Congress 16 | 121/327 | +20
    Congress 16 | 122/327 | +20
    Congress 16 | 123/327 | +20
    Congress 16 | 124/327 | +20
    Congress 16 | 125/327 | +20
    Congress 16 | 126/327 | +20
    Congress 16 | 127/327 | +20
    Congress 16 | 128/327 | +20
    Congress 16 | 129/327 | +20
    Congress 16 | 130/327 | +20
    Congress 16 | 131/327 | +20
    Congress 16 | 132/327 | +20
    Congress 16 | 133/327 | +20
    Congress 16 | 134/327 | +20
    Congress 16 | 135/327 | +20
    Congress 16 | 136/327 | +20
    Congress 16 | 137/327 | +20
    Congress 16 | 138/327 | +20
    Congress 16 | 139/327 | +20
    Congress 16 | 140/327 | +20
    Congress 16 | 141/327 | +20
    Congress 16 | 142/327 | +20
    Congress 16 | 143/327 | +20
    Congress 16 | 144/327 | +20
    Congress 16 | 145/327 | +20
    Congress 16 | 146/327 | +20
    Congress 16 | 147/327 | +20
    Congress 16 | 148/327 | +20
    Congress 16 | 149/327 | +20
    Congress 16 | 150/327 | +20
    Congress 16 | 151/327 | +20
    Congress 16 | 152/327 | +20
    Congress 16 | 153/327 | +20
    Congress 16 | 154/327 | +20
    Congress 16 | 155/327 | +20
    Congress 16 | 156/327 | +20
    Congress 16 | 157/327 | +20
    Congress 16 | 158/327 | +20
    Congress 16 | 159/327 | +20
    Congress 16 | 160/327 | +20
    Congress 16 | 161/327 | +20
    Congress 16 | 162/327 | +20
    Congress 16 | 163/327 | +20
    Congress 16 | 164/327 | +20
    Congress 16 | 165/327 | +20
    Congress 16 | 166/327 | +20
    Congress 16 | 167/327 | +20
    Congress 16 | 168/327 | +20
    Congress 16 | 169/327 | +20
    Congress 16 | 170/327 | +20
    Congress 16 | 171/327 | +20
    Congress 16 | 172/327 | +20
    Congress 16 | 173/327 | +20
    Congress 16 | 174/327 | +20
    Congress 16 | 175/327 | +20
    Congress 16 | 176/327 | +20
    Congress 16 | 177/327 | +20
    Congress 16 | 178/327 | +20
    Congress 16 | 179/327 | +20
    Congress 16 | 180/327 | +20
    Congress 16 | 181/327 | +20
    Congress 16 | 182/327 | +20
    Congress 16 | 183/327 | +20
    Congress 16 | 184/327 | +20
    Congress 16 | 185/327 | +20
    Congress 16 | 186/327 | +20
    Congress 16 | 187/327 | +20
    Congress 16 | 188/327 | +20
    Congress 16 | 189/327 | +20
    Congress 16 | 190/327 | +20
    Congress 16 | 191/327 | +20
    Congress 16 | 192/327 | +20
    Congress 16 | 193/327 | +20
    Congress 16 | 194/327 | +20
    Congress 16 | 195/327 | +20
    Congress 16 | 196/327 | +20
    Congress 16 | 197/327 | +20
    Congress 16 | 198/327 | +20
    Congress 16 | 199/327 | +20
    Congress 16 | 200/327 | +20
    Congress 16 | 201/327 | +20
    Congress 16 | 202/327 | +20
    Congress 16 | 203/327 | +20
    Congress 16 | 204/327 | +20
    Congress 16 | 205/327 | +20
    Congress 16 | 206/327 | +20
    Congress 16 | 207/327 | +20
    Congress 16 | 208/327 | +20
    Congress 16 | 209/327 | +20
    Congress 16 | 210/327 | +20
    Congress 16 | 211/327 | +20
    Congress 16 | 212/327 | +20
    Congress 16 | 213/327 | +20
    Congress 16 | 214/327 | +20
    Congress 16 | 215/327 | +20
    Congress 16 | 216/327 | +20
    Congress 16 | 217/327 | +20
    Congress 16 | 218/327 | +20
    Congress 16 | 219/327 | +20
    Congress 16 | 220/327 | +20
    Congress 16 | 221/327 | +20
    Congress 16 | 222/327 | +20
    Congress 16 | 223/327 | +20
    Congress 16 | 224/327 | +20
    Congress 16 | 225/327 | +20
    Congress 16 | 226/327 | +20
    Congress 16 | 227/327 | +20
    Congress 16 | 228/327 | +20
    Congress 16 | 229/327 | +20
    Congress 16 | 230/327 | +20
    Congress 16 | 231/327 | +20
    Congress 16 | 232/327 | +20
    Congress 16 | 233/327 | +20
    Congress 16 | 234/327 | +20
    Congress 16 | 235/327 | +20
    Congress 16 | 236/327 | +20
    Congress 16 | 237/327 | +20
    Congress 16 | 238/327 | +20
    Congress 16 | 239/327 | +20
    Congress 16 | 240/327 | +20
    Congress 16 | 241/327 | +20
    Congress 16 | 242/327 | +20
    Congress 16 | 243/327 | +20
    Congress 16 | 244/327 | +20
    Congress 16 | 245/327 | +20
    Congress 16 | 246/327 | +20
    Congress 16 | 247/327 | +20
    Congress 16 | 248/327 | +20
    Congress 16 | 249/327 | +20
    Congress 16 | 250/327 | +20
    Congress 16 | 251/327 | +20
    Congress 16 | 252/327 | +20
    Congress 16 | 253/327 | +20
    Congress 16 | 254/327 | +20
    Congress 16 | 255/327 | +20
    Congress 16 | 256/327 | +20
    Congress 16 | 257/327 | +20
    Congress 16 | 258/327 | +20
    Congress 16 | 259/327 | +20
    Congress 16 | 260/327 | +20
    Congress 16 | 261/327 | +20
    Congress 16 | 262/327 | +20
    Congress 16 | 263/327 | +20
    Congress 16 | 264/327 | +20
    Congress 16 | 265/327 | +20
    Congress 16 | 266/327 | +20
    Congress 16 | 267/327 | +20
    Congress 16 | 268/327 | +20
    Congress 16 | 269/327 | +20
    Congress 16 | 270/327 | +20
    Congress 16 | 271/327 | +20
    Congress 16 | 272/327 | +20
    Congress 16 | 273/327 | +20
    Congress 16 | 274/327 | +20
    Congress 16 | 275/327 | +20
    Congress 16 | 276/327 | +20
    Congress 16 | 277/327 | +20
    Congress 16 | 278/327 | +20
    Congress 16 | 279/327 | +20
    Congress 16 | 280/327 | +20
    Congress 16 | 281/327 | +20
    Congress 16 | 282/327 | +20
    Congress 16 | 283/327 | +20
    Congress 16 | 284/327 | +20
    Congress 16 | 285/327 | +20
    Congress 16 | 286/327 | +20
    Congress 16 | 287/327 | +20
    Congress 16 | 288/327 | +20
    Congress 16 | 289/327 | +20
    Congress 16 | 290/327 | +20
    Congress 16 | 291/327 | +20
    Congress 16 | 292/327 | +20
    Congress 16 | 293/327 | +20
    Congress 16 | 294/327 | +20
    Congress 16 | 295/327 | +20
    Congress 16 | 296/327 | +20
    Congress 16 | 297/327 | +20
    Congress 16 | 298/327 | +20
    Congress 16 | 299/327 | +20
    Congress 16 | 300/327 | +20
    Congress 16 | 301/327 | +20
    Congress 16 | 302/327 | +20
    Congress 16 | 303/327 | +20
    Congress 16 | 304/327 | +20
    Congress 16 | 305/327 | +20
    Congress 16 | 306/327 | +20
    Congress 16 | 307/327 | +20
    Congress 16 | 308/327 | +20
    Congress 16 | 309/327 | +20
    Congress 16 | 310/327 | +20
    Congress 16 | 311/327 | +20
    Congress 16 | 312/327 | +20
    Congress 16 | 313/327 | +20
    Congress 16 | 314/327 | +20
    Congress 16 | 315/327 | +20
    Congress 16 | 316/327 | +20
    Congress 16 | 317/327 | +20
    Congress 16 | 318/327 | +20
    Congress 16 | 319/327 | +20
    Congress 16 | 320/327 | +20
    Congress 16 | 321/327 | +20
    Congress 16 | 322/327 | +20
    Congress 16 | 323/327 | +20
    Congress 16 | 324/327 | +20
    Congress 16 | 325/327 | +20
    Congress 16 | 326/327 | +20
    Congress 16 | 327/327 | +9
    
    🔄 Congress 17
    Congress 17 | 1/461 | +20
    Congress 17 | 2/461 | +20
    Congress 17 | 3/461 | +20
    Congress 17 | 4/461 | +20
    Congress 17 | 5/461 | +20
    Congress 17 | 6/461 | +20
    Congress 17 | 7/461 | +20
    Congress 17 | 8/461 | +20
    Congress 17 | 9/461 | +20
    Congress 17 | 10/461 | +20
    Congress 17 | 11/461 | +20
    Congress 17 | 12/461 | +20
    Congress 17 | 13/461 | +20
    Congress 17 | 14/461 | +20
    Congress 17 | 15/461 | +20
    Congress 17 | 16/461 | +20
    Congress 17 | 17/461 | +20
    Congress 17 | 18/461 | +20
    Congress 17 | 19/461 | +20
    Congress 17 | 20/461 | +20
    Congress 17 | 21/461 | +20
    Congress 17 | 22/461 | +20
    Congress 17 | 23/461 | +20
    Congress 17 | 24/461 | +20
    Congress 17 | 25/461 | +20
    Congress 17 | 26/461 | +20
    Congress 17 | 27/461 | +20
    Congress 17 | 28/461 | +20
    Congress 17 | 29/461 | +20
    Congress 17 | 30/461 | +20
    Congress 17 | 31/461 | +20
    Congress 17 | 32/461 | +20
    Congress 17 | 33/461 | +20
    Congress 17 | 34/461 | +20
    Congress 17 | 35/461 | +20
    Congress 17 | 36/461 | +20
    Congress 17 | 37/461 | +20
    Congress 17 | 38/461 | +20
    Congress 17 | 39/461 | +20
    Congress 17 | 40/461 | +20
    Congress 17 | 41/461 | +20
    Congress 17 | 42/461 | +20
    Congress 17 | 43/461 | +20
    Congress 17 | 44/461 | +20
    Congress 17 | 45/461 | +20
    Congress 17 | 46/461 | +20
    Congress 17 | 47/461 | +20
    Congress 17 | 48/461 | +20
    Congress 17 | 49/461 | +20
    Congress 17 | 50/461 | +20
    Congress 17 | 51/461 | +20
    Congress 17 | 52/461 | +20
    Congress 17 | 53/461 | +20
    Congress 17 | 54/461 | +20
    Congress 17 | 55/461 | +20
    Congress 17 | 56/461 | +20
    Congress 17 | 57/461 | +20
    Congress 17 | 58/461 | +20
    Congress 17 | 59/461 | +20
    Congress 17 | 60/461 | +20
    Congress 17 | 61/461 | +20
    Congress 17 | 62/461 | +20
    Congress 17 | 63/461 | +20
    Congress 17 | 64/461 | +20
    Congress 17 | 65/461 | +20
    Congress 17 | 66/461 | +20
    Congress 17 | 67/461 | +20
    Congress 17 | 68/461 | +20
    Congress 17 | 69/461 | +20
    Congress 17 | 70/461 | +20
    Congress 17 | 71/461 | +20
    Congress 17 | 72/461 | +20
    Congress 17 | 73/461 | +20
    Congress 17 | 74/461 | +20
    Congress 17 | 75/461 | +20
    Congress 17 | 76/461 | +20
    Congress 17 | 77/461 | +20
    Congress 17 | 78/461 | +20
    Congress 17 | 79/461 | +20
    Congress 17 | 80/461 | +20
    Congress 17 | 81/461 | +20
    Congress 17 | 82/461 | +20
    Congress 17 | 83/461 | +20
    Congress 17 | 84/461 | +20
    Congress 17 | 85/461 | +20
    Congress 17 | 86/461 | +20
    Congress 17 | 87/461 | +20
    Congress 17 | 88/461 | +20
    Congress 17 | 89/461 | +20
    Congress 17 | 90/461 | +20
    Congress 17 | 91/461 | +20
    Congress 17 | 92/461 | +20
    Congress 17 | 93/461 | +20
    Congress 17 | 94/461 | +20
    Congress 17 | 95/461 | +20
    Congress 17 | 96/461 | +20
    Congress 17 | 97/461 | +20
    Congress 17 | 98/461 | +20
    Congress 17 | 99/461 | +20
    Congress 17 | 100/461 | +20
    Congress 17 | 101/461 | +20
    Congress 17 | 102/461 | +20
    Congress 17 | 103/461 | +20
    Congress 17 | 104/461 | +20
    Congress 17 | 105/461 | +20
    Congress 17 | 106/461 | +20
    Congress 17 | 107/461 | +20
    Congress 17 | 108/461 | +20
    Congress 17 | 109/461 | +20
    Congress 17 | 110/461 | +20
    Congress 17 | 111/461 | +20
    Congress 17 | 112/461 | +20
    Congress 17 | 113/461 | +20
    Congress 17 | 114/461 | +20
    Congress 17 | 115/461 | +20
    Congress 17 | 116/461 | +20
    Congress 17 | 117/461 | +20
    Congress 17 | 118/461 | +20
    Congress 17 | 119/461 | +20
    Congress 17 | 120/461 | +20
    Congress 17 | 121/461 | +20
    Congress 17 | 122/461 | +20
    Congress 17 | 123/461 | +20
    Congress 17 | 124/461 | +20
    Congress 17 | 125/461 | +20
    Congress 17 | 126/461 | +20
    Congress 17 | 127/461 | +20
    Congress 17 | 128/461 | +20
    Congress 17 | 129/461 | +20
    Congress 17 | 130/461 | +20
    Congress 17 | 131/461 | +20
    Congress 17 | 132/461 | +20
    Congress 17 | 133/461 | +20
    Congress 17 | 134/461 | +20
    Congress 17 | 135/461 | +20
    Congress 17 | 136/461 | +20
    Congress 17 | 137/461 | +20
    Congress 17 | 138/461 | +20
    Congress 17 | 139/461 | +20
    Congress 17 | 140/461 | +20
    Congress 17 | 141/461 | +20
    Congress 17 | 142/461 | +20
    Congress 17 | 143/461 | +20
    Congress 17 | 144/461 | +20
    Congress 17 | 145/461 | +20
    Congress 17 | 146/461 | +20
    Congress 17 | 147/461 | +20
    Congress 17 | 148/461 | +20
    Congress 17 | 149/461 | +20
    Congress 17 | 150/461 | +20
    Congress 17 | 151/461 | +20
    Congress 17 | 152/461 | +20
    Congress 17 | 153/461 | +20
    Congress 17 | 154/461 | +20
    Congress 17 | 155/461 | +20
    Congress 17 | 156/461 | +20
    Congress 17 | 157/461 | +20
    Congress 17 | 158/461 | +20
    Congress 17 | 159/461 | +20
    Congress 17 | 160/461 | +20
    Congress 17 | 161/461 | +20
    Congress 17 | 162/461 | +20
    Congress 17 | 163/461 | +20
    Congress 17 | 164/461 | +20
    Congress 17 | 165/461 | +20
    Congress 17 | 166/461 | +20
    Congress 17 | 167/461 | +20
    Congress 17 | 168/461 | +20
    Congress 17 | 169/461 | +20
    Congress 17 | 170/461 | +20
    Congress 17 | 171/461 | +20
    Congress 17 | 172/461 | +20
    Congress 17 | 173/461 | +20
    Congress 17 | 174/461 | +20
    Congress 17 | 175/461 | +20
    Congress 17 | 176/461 | +20
    Congress 17 | 177/461 | +20
    Congress 17 | 178/461 | +20
    Congress 17 | 179/461 | +20
    Congress 17 | 180/461 | +20
    Congress 17 | 181/461 | +20
    Congress 17 | 182/461 | +20
    Congress 17 | 183/461 | +20
    Congress 17 | 184/461 | +20
    Congress 17 | 185/461 | +20
    Congress 17 | 186/461 | +20
    Congress 17 | 187/461 | +20
    Congress 17 | 188/461 | +20
    Congress 17 | 189/461 | +20
    Congress 17 | 190/461 | +20
    Congress 17 | 191/461 | +20
    Congress 17 | 192/461 | +20
    Congress 17 | 193/461 | +20
    Congress 17 | 194/461 | +20
    Congress 17 | 195/461 | +20
    Congress 17 | 196/461 | +20
    Congress 17 | 197/461 | +20
    Congress 17 | 198/461 | +20
    Congress 17 | 199/461 | +20
    Congress 17 | 200/461 | +20
    Congress 17 | 201/461 | +20
    Congress 17 | 202/461 | +20
    Congress 17 | 203/461 | +20
    Congress 17 | 204/461 | +20
    Congress 17 | 205/461 | +20
    Congress 17 | 206/461 | +20
    Congress 17 | 207/461 | +20
    Congress 17 | 208/461 | +20
    Congress 17 | 209/461 | +20
    Congress 17 | 210/461 | +20
    Congress 17 | 211/461 | +20
    Congress 17 | 212/461 | +20
    Congress 17 | 213/461 | +20
    Congress 17 | 214/461 | +20
    Congress 17 | 215/461 | +20
    Congress 17 | 216/461 | +20
    Congress 17 | 217/461 | +20
    Congress 17 | 218/461 | +20
    Congress 17 | 219/461 | +20
    Congress 17 | 220/461 | +20
    Congress 17 | 221/461 | +20
    Congress 17 | 222/461 | +20
    Congress 17 | 223/461 | +20
    Congress 17 | 224/461 | +20
    Congress 17 | 225/461 | +20
    Congress 17 | 226/461 | +20
    Congress 17 | 227/461 | +20
    Congress 17 | 228/461 | +20
    Congress 17 | 229/461 | +20
    Congress 17 | 230/461 | +20
    Congress 17 | 231/461 | +20
    Congress 17 | 232/461 | +20
    Congress 17 | 233/461 | +20
    Congress 17 | 234/461 | +20
    Congress 17 | 235/461 | +20
    Congress 17 | 236/461 | +20
    Congress 17 | 237/461 | +20
    Congress 17 | 238/461 | +20
    Congress 17 | 239/461 | +20
    Congress 17 | 240/461 | +20
    Congress 17 | 241/461 | +20
    Congress 17 | 242/461 | +20
    Congress 17 | 243/461 | +20
    Congress 17 | 244/461 | +20
    Congress 17 | 245/461 | +20
    Congress 17 | 246/461 | +20
    Congress 17 | 247/461 | +20
    Congress 17 | 248/461 | +20
    Congress 17 | 249/461 | +20
    Congress 17 | 250/461 | +20
    Congress 17 | 251/461 | +20
    Congress 17 | 252/461 | +20
    Congress 17 | 253/461 | +20
    Congress 17 | 254/461 | +20
    Congress 17 | 255/461 | +20
    Congress 17 | 256/461 | +20
    Congress 17 | 257/461 | +20
    Congress 17 | 258/461 | +20
    Congress 17 | 259/461 | +20
    Congress 17 | 260/461 | +20
    Congress 17 | 261/461 | +20
    Congress 17 | 262/461 | +20
    Congress 17 | 263/461 | +20
    Congress 17 | 264/461 | +20
    Congress 17 | 265/461 | +20
    Congress 17 | 266/461 | +20
    Congress 17 | 267/461 | +20
    Congress 17 | 268/461 | +20
    Congress 17 | 269/461 | +20
    Congress 17 | 270/461 | +20
    Congress 17 | 271/461 | +20
    Congress 17 | 272/461 | +20
    Congress 17 | 273/461 | +20
    Congress 17 | 274/461 | +20
    Congress 17 | 275/461 | +20
    Congress 17 | 276/461 | +20
    Congress 17 | 277/461 | +20
    Congress 17 | 278/461 | +20
    Congress 17 | 279/461 | +20
    Congress 17 | 280/461 | +20
    Congress 17 | 281/461 | +20
    Congress 17 | 282/461 | +20
    Congress 17 | 283/461 | +20
    Congress 17 | 284/461 | +20
    Congress 17 | 285/461 | +20
    Congress 17 | 286/461 | +20
    Congress 17 | 287/461 | +20
    Congress 17 | 288/461 | +20
    Congress 17 | 289/461 | +20
    Congress 17 | 290/461 | +20
    Congress 17 | 291/461 | +20
    Congress 17 | 292/461 | +20
    Congress 17 | 293/461 | +20
    Congress 17 | 294/461 | +20
    Congress 17 | 295/461 | +20
    Congress 17 | 296/461 | +20
    Congress 17 | 297/461 | +20
    Congress 17 | 298/461 | +20
    Congress 17 | 299/461 | +20
    Congress 17 | 300/461 | +20
    Congress 17 | 301/461 | +20
    Congress 17 | 302/461 | +20
    Congress 17 | 303/461 | +20
    Congress 17 | 304/461 | +20
    Congress 17 | 305/461 | +20
    Congress 17 | 306/461 | +20
    Congress 17 | 307/461 | +20
    Congress 17 | 308/461 | +20
    Congress 17 | 309/461 | +20
    Congress 17 | 310/461 | +20
    Congress 17 | 311/461 | +20
    Congress 17 | 312/461 | +20
    Congress 17 | 313/461 | +20
    Congress 17 | 314/461 | +20
    Congress 17 | 315/461 | +20
    Congress 17 | 316/461 | +20
    Congress 17 | 317/461 | +20
    Congress 17 | 318/461 | +20
    Congress 17 | 319/461 | +20
    Congress 17 | 320/461 | +20
    Congress 17 | 321/461 | +20
    Congress 17 | 322/461 | +20
    Congress 17 | 323/461 | +20
    Congress 17 | 324/461 | +20
    Congress 17 | 325/461 | +20
    Congress 17 | 326/461 | +20
    Congress 17 | 327/461 | +20
    Congress 17 | 328/461 | +20
    Congress 17 | 329/461 | +20
    Congress 17 | 330/461 | +20
    Congress 17 | 331/461 | +20
    Congress 17 | 332/461 | +20
    Congress 17 | 333/461 | +20
    Congress 17 | 334/461 | +20
    Congress 17 | 335/461 | +20
    Congress 17 | 336/461 | +20
    Congress 17 | 337/461 | +20
    Congress 17 | 338/461 | +20
    Congress 17 | 339/461 | +20
    Congress 17 | 340/461 | +20
    Congress 17 | 341/461 | +20
    Congress 17 | 342/461 | +20
    Congress 17 | 343/461 | +20
    Congress 17 | 344/461 | +20
    Congress 17 | 345/461 | +20
    Congress 17 | 346/461 | +20
    Congress 17 | 347/461 | +20
    Congress 17 | 348/461 | +20
    Congress 17 | 349/461 | +20
    Congress 17 | 350/461 | +20
    Congress 17 | 351/461 | +20
    Congress 17 | 352/461 | +20
    Congress 17 | 353/461 | +20
    Congress 17 | 354/461 | +20
    Congress 17 | 355/461 | +20
    Congress 17 | 356/461 | +20
    Congress 17 | 357/461 | +20
    Congress 17 | 358/461 | +20
    Congress 17 | 359/461 | +20
    Congress 17 | 360/461 | +20
    Congress 17 | 361/461 | +20
    Congress 17 | 362/461 | +20
    Congress 17 | 363/461 | +20
    Congress 17 | 364/461 | +20
    Congress 17 | 365/461 | +20
    Congress 17 | 366/461 | +20
    Congress 17 | 367/461 | +20
    Congress 17 | 368/461 | +20
    Congress 17 | 369/461 | +20
    Congress 17 | 370/461 | +20
    Congress 17 | 371/461 | +20
    Congress 17 | 372/461 | +20
    Congress 17 | 373/461 | +20
    Congress 17 | 374/461 | +20
    Congress 17 | 375/461 | +20
    Congress 17 | 376/461 | +20
    Congress 17 | 377/461 | +20
    Congress 17 | 378/461 | +20
    Congress 17 | 379/461 | +20
    Congress 17 | 380/461 | +20
    Congress 17 | 381/461 | +20
    Congress 17 | 382/461 | +20
    Congress 17 | 383/461 | +20
    Congress 17 | 384/461 | +20
    Congress 17 | 385/461 | +20
    Congress 17 | 386/461 | +20
    Congress 17 | 387/461 | +20
    Congress 17 | 388/461 | +20
    Congress 17 | 389/461 | +20
    Congress 17 | 390/461 | +20
    Congress 17 | 391/461 | +20
    Congress 17 | 392/461 | +20
    Congress 17 | 393/461 | +20
    Congress 17 | 394/461 | +20
    Congress 17 | 395/461 | +20
    Congress 17 | 396/461 | +20
    Congress 17 | 397/461 | +20
    Congress 17 | 398/461 | +20
    Congress 17 | 399/461 | +20
    Congress 17 | 400/461 | +20
    Congress 17 | 401/461 | +20
    Congress 17 | 402/461 | +20
    Congress 17 | 403/461 | +20
    Congress 17 | 404/461 | +20
    Congress 17 | 405/461 | +20
    Congress 17 | 406/461 | +20
    Congress 17 | 407/461 | +20
    Congress 17 | 408/461 | +20
    Congress 17 | 409/461 | +20
    Congress 17 | 410/461 | +20
    Congress 17 | 411/461 | +20
    Congress 17 | 412/461 | +20
    Congress 17 | 413/461 | +20
    Congress 17 | 414/461 | +20
    Congress 17 | 415/461 | +20
    Congress 17 | 416/461 | +20
    Congress 17 | 417/461 | +20
    Congress 17 | 418/461 | +20
    Congress 17 | 419/461 | +20
    Congress 17 | 420/461 | +20
    Congress 17 | 421/461 | +20
    Congress 17 | 422/461 | +20
    Congress 17 | 423/461 | +20
    Congress 17 | 424/461 | +20
    Congress 17 | 425/461 | +20
    Congress 17 | 426/461 | +20
    Congress 17 | 427/461 | +20
    Congress 17 | 428/461 | +20
    Congress 17 | 429/461 | +20
    Congress 17 | 430/461 | +20
    Congress 17 | 431/461 | +20
    Congress 17 | 432/461 | +20
    Congress 17 | 433/461 | +20
    Congress 17 | 434/461 | +20
    Congress 17 | 435/461 | +20
    Congress 17 | 436/461 | +20
    Congress 17 | 437/461 | +20
    Congress 17 | 438/461 | +20
    Congress 17 | 439/461 | +20
    Congress 17 | 440/461 | +20
    Congress 17 | 441/461 | +20
    Congress 17 | 442/461 | +20
    Congress 17 | 443/461 | +20
    Congress 17 | 444/461 | +20
    Congress 17 | 445/461 | +20
    Congress 17 | 446/461 | +20
    Congress 17 | 447/461 | +20
    Congress 17 | 448/461 | +20
    Congress 17 | 449/461 | +20
    Congress 17 | 450/461 | +20
    Congress 17 | 451/461 | +20
    Congress 17 | 452/461 | +20
    Congress 17 | 453/461 | +20
    Congress 17 | 454/461 | +20
    Congress 17 | 455/461 | +20
    Congress 17 | 456/461 | +20
    Congress 17 | 457/461 | +20
    Congress 17 | 458/461 | +20
    Congress 17 | 459/461 | +20
    Congress 17 | 460/461 | +20
    Congress 17 | 461/461 | +15
    
    🔄 Congress 18
    Congress 18 | 1/543 | +20
    Congress 18 | 2/543 | +20
    Congress 18 | 3/543 | +20
    Congress 18 | 4/543 | +20
    Congress 18 | 5/543 | +20
    Congress 18 | 6/543 | +20
    Congress 18 | 7/543 | +20
    Congress 18 | 8/543 | +20
    Congress 18 | 9/543 | +20
    Congress 18 | 10/543 | +20
    Congress 18 | 11/543 | +20
    Congress 18 | 12/543 | +20
    Congress 18 | 13/543 | +20
    Congress 18 | 14/543 | +20
    Congress 18 | 15/543 | +20
    Congress 18 | 16/543 | +20
    Congress 18 | 17/543 | +20
    Congress 18 | 18/543 | +20
    Congress 18 | 19/543 | +20
    Congress 18 | 20/543 | +20
    Congress 18 | 21/543 | +20
    Congress 18 | 22/543 | +20
    Congress 18 | 23/543 | +20
    Congress 18 | 24/543 | +20
    Congress 18 | 25/543 | +20
    Congress 18 | 26/543 | +20
    Congress 18 | 27/543 | +20
    Congress 18 | 28/543 | +20
    Congress 18 | 29/543 | +20
    Congress 18 | 30/543 | +20
    Congress 18 | 31/543 | +20
    Congress 18 | 32/543 | +20
    Congress 18 | 33/543 | +20
    Congress 18 | 34/543 | +20
    Congress 18 | 35/543 | +20
    Congress 18 | 36/543 | +20
    Congress 18 | 37/543 | +20
    Congress 18 | 38/543 | +20
    Congress 18 | 39/543 | +20
    Congress 18 | 40/543 | +20
    Congress 18 | 41/543 | +20
    Congress 18 | 42/543 | +20
    Congress 18 | 43/543 | +20
    Congress 18 | 44/543 | +20
    Congress 18 | 45/543 | +20
    Congress 18 | 46/543 | +20
    Congress 18 | 47/543 | +20
    Congress 18 | 48/543 | +20
    Congress 18 | 49/543 | +20
    Congress 18 | 50/543 | +20
    Congress 18 | 51/543 | +20
    Congress 18 | 52/543 | +20
    Congress 18 | 53/543 | +20
    Congress 18 | 54/543 | +20
    Congress 18 | 55/543 | +20
    Congress 18 | 56/543 | +20
    Congress 18 | 57/543 | +20
    Congress 18 | 58/543 | +20
    Congress 18 | 59/543 | +20
    Congress 18 | 60/543 | +20
    Congress 18 | 61/543 | +20
    Congress 18 | 62/543 | +20
    Congress 18 | 63/543 | +20
    Congress 18 | 64/543 | +20
    Congress 18 | 65/543 | +20
    Congress 18 | 66/543 | +20
    Congress 18 | 67/543 | +20
    Congress 18 | 68/543 | +20
    Congress 18 | 69/543 | +20
    Congress 18 | 70/543 | +20
    Congress 18 | 71/543 | +20
    Congress 18 | 72/543 | +20
    Congress 18 | 73/543 | +20
    Congress 18 | 74/543 | +20
    Congress 18 | 75/543 | +20
    Congress 18 | 76/543 | +20
    Congress 18 | 77/543 | +20
    Congress 18 | 78/543 | +20
    Congress 18 | 79/543 | +20
    Congress 18 | 80/543 | +20
    Congress 18 | 81/543 | +20
    Congress 18 | 82/543 | +20
    Congress 18 | 83/543 | +20
    Congress 18 | 84/543 | +20
    Congress 18 | 85/543 | +20
    Congress 18 | 86/543 | +20
    Congress 18 | 87/543 | +20
    Congress 18 | 88/543 | +20
    Congress 18 | 89/543 | +20
    Congress 18 | 90/543 | +20
    Congress 18 | 91/543 | +20
    Congress 18 | 92/543 | +20
    Congress 18 | 93/543 | +20
    Congress 18 | 94/543 | +20
    Congress 18 | 95/543 | +20
    Congress 18 | 96/543 | +20
    Congress 18 | 97/543 | +20
    Congress 18 | 98/543 | +20
    Congress 18 | 99/543 | +20
    Congress 18 | 100/543 | +20
    Congress 18 | 101/543 | +20
    Congress 18 | 102/543 | +20
    Congress 18 | 103/543 | +20
    Congress 18 | 104/543 | +20
    Congress 18 | 105/543 | +20
    Congress 18 | 106/543 | +20
    Congress 18 | 107/543 | +20
    Congress 18 | 108/543 | +20
    Congress 18 | 109/543 | +20
    Congress 18 | 110/543 | +20
    Congress 18 | 111/543 | +20
    Congress 18 | 112/543 | +20
    Congress 18 | 113/543 | +20
    Congress 18 | 114/543 | +20
    Congress 18 | 115/543 | +20
    Congress 18 | 116/543 | +20
    Congress 18 | 117/543 | +20
    Congress 18 | 118/543 | +20
    Congress 18 | 119/543 | +20
    Congress 18 | 120/543 | +20
    Congress 18 | 121/543 | +20
    Congress 18 | 122/543 | +20
    Congress 18 | 123/543 | +20
    Congress 18 | 124/543 | +20
    Congress 18 | 125/543 | +20
    Congress 18 | 126/543 | +20
    Congress 18 | 127/543 | +20
    Congress 18 | 128/543 | +20
    Congress 18 | 129/543 | +20
    Congress 18 | 130/543 | +20
    Congress 18 | 131/543 | +20
    Congress 18 | 132/543 | +20
    Congress 18 | 133/543 | +20
    Congress 18 | 134/543 | +20
    Congress 18 | 135/543 | +20
    Congress 18 | 136/543 | +20
    Congress 18 | 137/543 | +20
    Congress 18 | 138/543 | +20
    Congress 18 | 139/543 | +20
    Congress 18 | 140/543 | +20
    Congress 18 | 141/543 | +20
    Congress 18 | 142/543 | +20
    Congress 18 | 143/543 | +20
    Congress 18 | 144/543 | +20
    Congress 18 | 145/543 | +20
    Congress 18 | 146/543 | +20
    Congress 18 | 147/543 | +20
    Congress 18 | 148/543 | +20
    Congress 18 | 149/543 | +20
    Congress 18 | 150/543 | +20
    Congress 18 | 151/543 | +20
    Congress 18 | 152/543 | +20
    Congress 18 | 153/543 | +20
    Congress 18 | 154/543 | +20
    Congress 18 | 155/543 | +20
    Congress 18 | 156/543 | +20
    Congress 18 | 157/543 | +20
    Congress 18 | 158/543 | +20
    Congress 18 | 159/543 | +20
    Congress 18 | 160/543 | +20
    Congress 18 | 161/543 | +20
    Congress 18 | 162/543 | +20
    Congress 18 | 163/543 | +20
    Congress 18 | 164/543 | +20
    Congress 18 | 165/543 | +20
    Congress 18 | 166/543 | +20
    Congress 18 | 167/543 | +20
    Congress 18 | 168/543 | +20
    Congress 18 | 169/543 | +20
    Congress 18 | 170/543 | +20
    Congress 18 | 171/543 | +20
    Congress 18 | 172/543 | +20
    Congress 18 | 173/543 | +20
    Congress 18 | 174/543 | +20
    Congress 18 | 175/543 | +20
    Congress 18 | 176/543 | +20
    Congress 18 | 177/543 | +20
    Congress 18 | 178/543 | +20
    Congress 18 | 179/543 | +20
    Congress 18 | 180/543 | +20
    Congress 18 | 181/543 | +20
    Congress 18 | 182/543 | +20
    Congress 18 | 183/543 | +20
    Congress 18 | 184/543 | +20
    Congress 18 | 185/543 | +20
    Congress 18 | 186/543 | +20
    Congress 18 | 187/543 | +20
    Congress 18 | 188/543 | +20
    Congress 18 | 189/543 | +20
    Congress 18 | 190/543 | +20
    Congress 18 | 191/543 | +20
    Congress 18 | 192/543 | +20
    Congress 18 | 193/543 | +20
    Congress 18 | 194/543 | +20
    Congress 18 | 195/543 | +20
    Congress 18 | 196/543 | +20
    Congress 18 | 197/543 | +20
    Congress 18 | 198/543 | +20
    Congress 18 | 199/543 | +20
    Congress 18 | 200/543 | +20
    Congress 18 | 201/543 | +20
    Congress 18 | 202/543 | +20
    Congress 18 | 203/543 | +20
    Congress 18 | 204/543 | +20
    Congress 18 | 205/543 | +20
    Congress 18 | 206/543 | +20
    Congress 18 | 207/543 | +20
    Congress 18 | 208/543 | +20
    Congress 18 | 209/543 | +20
    Congress 18 | 210/543 | +20
    Congress 18 | 211/543 | +20
    Congress 18 | 212/543 | +20
    Congress 18 | 213/543 | +20
    Congress 18 | 214/543 | +20
    Congress 18 | 215/543 | +20
    Congress 18 | 216/543 | +20
    Congress 18 | 217/543 | +20
    Congress 18 | 218/543 | +20
    Congress 18 | 219/543 | +20
    Congress 18 | 220/543 | +20
    Congress 18 | 221/543 | +20
    Congress 18 | 222/543 | +20
    Congress 18 | 223/543 | +20
    Congress 18 | 224/543 | +20
    Congress 18 | 225/543 | +20
    Congress 18 | 226/543 | +20
    Congress 18 | 227/543 | +20
    Congress 18 | 228/543 | +20
    Congress 18 | 229/543 | +20
    Congress 18 | 230/543 | +20
    Congress 18 | 231/543 | +20
    Congress 18 | 232/543 | +20
    Congress 18 | 233/543 | +20
    Congress 18 | 234/543 | +20
    Congress 18 | 235/543 | +20
    Congress 18 | 236/543 | +20
    Congress 18 | 237/543 | +20
    Congress 18 | 238/543 | +20
    Congress 18 | 239/543 | +20
    Congress 18 | 240/543 | +20
    Congress 18 | 241/543 | +20
    Congress 18 | 242/543 | +20
    Congress 18 | 243/543 | +20
    Congress 18 | 244/543 | +20
    Congress 18 | 245/543 | +20
    Congress 18 | 246/543 | +20
    Congress 18 | 247/543 | +20
    Congress 18 | 248/543 | +20
    Congress 18 | 249/543 | +20
    Congress 18 | 250/543 | +20
    Congress 18 | 251/543 | +20
    Congress 18 | 252/543 | +20
    Congress 18 | 253/543 | +20
    Congress 18 | 254/543 | +20
    Congress 18 | 255/543 | +20
    Congress 18 | 256/543 | +20
    Congress 18 | 257/543 | +20
    Congress 18 | 258/543 | +20
    Congress 18 | 259/543 | +20
    Congress 18 | 260/543 | +20
    Congress 18 | 261/543 | +20
    Congress 18 | 262/543 | +20
    Congress 18 | 263/543 | +20
    Congress 18 | 264/543 | +20
    Congress 18 | 265/543 | +20
    Congress 18 | 266/543 | +20
    Congress 18 | 267/543 | +20
    Congress 18 | 268/543 | +20
    Congress 18 | 269/543 | +20
    Congress 18 | 270/543 | +20
    Congress 18 | 271/543 | +20
    Congress 18 | 272/543 | +20
    Congress 18 | 273/543 | +20
    Congress 18 | 274/543 | +20
    Congress 18 | 275/543 | +20
    Congress 18 | 276/543 | +20
    Congress 18 | 277/543 | +20
    Congress 18 | 278/543 | +20
    Congress 18 | 279/543 | +20
    Congress 18 | 280/543 | +20
    Congress 18 | 281/543 | +20
    Congress 18 | 282/543 | +20
    Congress 18 | 283/543 | +20
    Congress 18 | 284/543 | +20
    Congress 18 | 285/543 | +20
    Congress 18 | 286/543 | +20
    Congress 18 | 287/543 | +20
    Congress 18 | 288/543 | +20
    Congress 18 | 289/543 | +20
    Congress 18 | 290/543 | +20
    Congress 18 | 291/543 | +20
    Congress 18 | 292/543 | +20
    Congress 18 | 293/543 | +20
    Congress 18 | 294/543 | +20
    Congress 18 | 295/543 | +20
    Congress 18 | 296/543 | +20
    Congress 18 | 297/543 | +20
    Congress 18 | 298/543 | +20
    Congress 18 | 299/543 | +20
    Congress 18 | 300/543 | +20
    Congress 18 | 301/543 | +20
    Congress 18 | 302/543 | +20
    Congress 18 | 303/543 | +20
    Congress 18 | 304/543 | +20
    Congress 18 | 305/543 | +20
    Congress 18 | 306/543 | +20
    Congress 18 | 307/543 | +20
    Congress 18 | 308/543 | +20
    Congress 18 | 309/543 | +20
    Congress 18 | 310/543 | +20
    Congress 18 | 311/543 | +20
    Congress 18 | 312/543 | +20
    Congress 18 | 313/543 | +20
    Congress 18 | 314/543 | +20
    Congress 18 | 315/543 | +20
    Congress 18 | 316/543 | +20
    Congress 18 | 317/543 | +20
    Congress 18 | 318/543 | +20
    Congress 18 | 319/543 | +20
    Congress 18 | 320/543 | +20
    Congress 18 | 321/543 | +20
    Congress 18 | 322/543 | +20
    Congress 18 | 323/543 | +20
    Congress 18 | 324/543 | +20
    Congress 18 | 325/543 | +20
    Congress 18 | 326/543 | +20
    Congress 18 | 327/543 | +20
    Congress 18 | 328/543 | +20
    Congress 18 | 329/543 | +20
    Congress 18 | 330/543 | +20
    Congress 18 | 331/543 | +20
    Congress 18 | 332/543 | +20
    Congress 18 | 333/543 | +20
    Congress 18 | 334/543 | +20
    Congress 18 | 335/543 | +20
    Congress 18 | 336/543 | +20
    Congress 18 | 337/543 | +20
    Congress 18 | 338/543 | +20
    Congress 18 | 339/543 | +20
    Congress 18 | 340/543 | +20
    Congress 18 | 341/543 | +20
    Congress 18 | 342/543 | +20
    Congress 18 | 343/543 | +20
    Congress 18 | 344/543 | +20
    Congress 18 | 345/543 | +20
    Congress 18 | 346/543 | +20
    Congress 18 | 347/543 | +20
    Congress 18 | 348/543 | +20
    Congress 18 | 349/543 | +20
    Congress 18 | 350/543 | +20
    Congress 18 | 351/543 | +20
    Congress 18 | 352/543 | +20
    Congress 18 | 353/543 | +20
    Congress 18 | 354/543 | +20
    Congress 18 | 355/543 | +20
    Congress 18 | 356/543 | +20
    Congress 18 | 357/543 | +20
    Congress 18 | 358/543 | +20
    Congress 18 | 359/543 | +20
    Congress 18 | 360/543 | +20
    Congress 18 | 361/543 | +20
    Congress 18 | 362/543 | +20
    Congress 18 | 363/543 | +20
    Congress 18 | 364/543 | +20
    Congress 18 | 365/543 | +20
    Congress 18 | 366/543 | +20
    Congress 18 | 367/543 | +20
    Congress 18 | 368/543 | +20
    Congress 18 | 369/543 | +20
    Congress 18 | 370/543 | +20
    Congress 18 | 371/543 | +20
    Congress 18 | 372/543 | +20
    Congress 18 | 373/543 | +20
    Congress 18 | 374/543 | +20
    Congress 18 | 375/543 | +20
    Congress 18 | 376/543 | +20
    Congress 18 | 377/543 | +20
    Congress 18 | 378/543 | +20
    Congress 18 | 379/543 | +20
    Congress 18 | 380/543 | +20
    Congress 18 | 381/543 | +20
    Congress 18 | 382/543 | +20
    Congress 18 | 383/543 | +20
    Congress 18 | 384/543 | +20
    Congress 18 | 385/543 | +20
    Congress 18 | 386/543 | +20
    Congress 18 | 387/543 | +20
    Congress 18 | 388/543 | +20
    Congress 18 | 389/543 | +20
    Congress 18 | 390/543 | +20
    Congress 18 | 391/543 | +20
    Congress 18 | 392/543 | +20
    Congress 18 | 393/543 | +20
    Congress 18 | 394/543 | +20
    Congress 18 | 395/543 | +20
    Congress 18 | 396/543 | +20
    Congress 18 | 397/543 | +20
    Congress 18 | 398/543 | +20
    Congress 18 | 399/543 | +20
    Congress 18 | 400/543 | +20
    Congress 18 | 401/543 | +20
    Congress 18 | 402/543 | +20
    Congress 18 | 403/543 | +20
    Congress 18 | 404/543 | +20
    Congress 18 | 405/543 | +20
    Congress 18 | 406/543 | +20
    Congress 18 | 407/543 | +20
    Congress 18 | 408/543 | +20
    Congress 18 | 409/543 | +20
    Congress 18 | 410/543 | +20
    Congress 18 | 411/543 | +20
    Congress 18 | 412/543 | +20
    Congress 18 | 413/543 | +20
    Congress 18 | 414/543 | +20
    Congress 18 | 415/543 | +20
    Congress 18 | 416/543 | +20
    Congress 18 | 417/543 | +20
    Congress 18 | 418/543 | +20
    Congress 18 | 419/543 | +20
    Congress 18 | 420/543 | +20
    Congress 18 | 421/543 | +20
    Congress 18 | 422/543 | +20
    Congress 18 | 423/543 | +20
    Congress 18 | 424/543 | +20
    Congress 18 | 425/543 | +20
    Congress 18 | 426/543 | +20
    Congress 18 | 427/543 | +20
    Congress 18 | 428/543 | +20
    Congress 18 | 429/543 | +20
    Congress 18 | 430/543 | +20
    Congress 18 | 431/543 | +20
    Congress 18 | 432/543 | +20
    Congress 18 | 433/543 | +20
    Congress 18 | 434/543 | +20
    Congress 18 | 435/543 | +20
    Congress 18 | 436/543 | +20
    Congress 18 | 437/543 | +20
    Congress 18 | 438/543 | +20
    Congress 18 | 439/543 | +20
    Congress 18 | 440/543 | +20
    Congress 18 | 441/543 | +20
    Congress 18 | 442/543 | +20
    Congress 18 | 443/543 | +20
    Congress 18 | 444/543 | +20
    Congress 18 | 445/543 | +20
    Congress 18 | 446/543 | +20
    Congress 18 | 447/543 | +20
    Congress 18 | 448/543 | +20
    Congress 18 | 449/543 | +20
    Congress 18 | 450/543 | +20
    Congress 18 | 451/543 | +20
    Congress 18 | 452/543 | +20
    Congress 18 | 453/543 | +20
    Congress 18 | 454/543 | +20
    Congress 18 | 455/543 | +20
    Congress 18 | 456/543 | +20
    Congress 18 | 457/543 | +20
    Congress 18 | 458/543 | +20
    Congress 18 | 459/543 | +20
    Congress 18 | 460/543 | +20
    Congress 18 | 461/543 | +20
    Congress 18 | 462/543 | +20
    Congress 18 | 463/543 | +20
    Congress 18 | 464/543 | +20
    Congress 18 | 465/543 | +20
    Congress 18 | 466/543 | +20
    Congress 18 | 467/543 | +20
    Congress 18 | 468/543 | +20
    Congress 18 | 469/543 | +20
    Congress 18 | 470/543 | +20
    Congress 18 | 471/543 | +20
    Congress 18 | 472/543 | +20
    Congress 18 | 473/543 | +20
    Congress 18 | 474/543 | +20
    Congress 18 | 475/543 | +20
    Congress 18 | 476/543 | +20
    Congress 18 | 477/543 | +20
    Congress 18 | 478/543 | +20
    Congress 18 | 479/543 | +20
    Congress 18 | 480/543 | +20
    Congress 18 | 481/543 | +20
    Congress 18 | 482/543 | +20
    Congress 18 | 483/543 | +20
    Congress 18 | 484/543 | +20
    Congress 18 | 485/543 | +20
    Congress 18 | 486/543 | +20
    Congress 18 | 487/543 | +20
    Congress 18 | 488/543 | +20
    Congress 18 | 489/543 | +20
    Congress 18 | 490/543 | +20
    Congress 18 | 491/543 | +20
    Congress 18 | 492/543 | +20
    Congress 18 | 493/543 | +20
    Congress 18 | 494/543 | +20
    Congress 18 | 495/543 | +20
    Congress 18 | 496/543 | +20
    Congress 18 | 497/543 | +20
    Congress 18 | 498/543 | +20
    Congress 18 | 499/543 | +20
    Congress 18 | 500/543 | +20
    Congress 18 | 501/543 | +20
    Congress 18 | 502/543 | +20
    Congress 18 | 503/543 | +20
    Congress 18 | 504/543 | +20
    Congress 18 | 505/543 | +20
    Congress 18 | 506/543 | +20
    Congress 18 | 507/543 | +20
    Congress 18 | 508/543 | +20
    Congress 18 | 509/543 | +20
    Congress 18 | 510/543 | +20
    Congress 18 | 511/543 | +20
    Congress 18 | 512/543 | +20
    Congress 18 | 513/543 | +20
    Congress 18 | 514/543 | +20
    Congress 18 | 515/543 | +20
    Congress 18 | 516/543 | +20
    Congress 18 | 517/543 | +20
    Congress 18 | 518/543 | +20
    Congress 18 | 519/543 | +20
    Congress 18 | 520/543 | +20
    Congress 18 | 521/543 | +20
    Congress 18 | 522/543 | +20
    Congress 18 | 523/543 | +20
    Congress 18 | 524/543 | +20
    Congress 18 | 525/543 | +20
    Congress 18 | 526/543 | +20
    Congress 18 | 527/543 | +20
    Congress 18 | 528/543 | +20
    Congress 18 | 529/543 | +20
    Congress 18 | 530/543 | +20
    Congress 18 | 531/543 | +20
    Congress 18 | 532/543 | +20
    Congress 18 | 533/543 | +20
    Congress 18 | 534/543 | +20
    Congress 18 | 535/543 | +20
    Congress 18 | 536/543 | +20
    Congress 18 | 537/543 | +20
    Congress 18 | 538/543 | +20
    Congress 18 | 539/543 | +20
    Congress 18 | 540/543 | +20
    Congress 18 | 541/543 | +20
    Congress 18 | 542/543 | +20
    Congress 18 | 543/543 | +13
    
    🔄 Congress 19
    Congress 19 | 1/579 | +20
    Congress 19 | 2/579 | +20
    Congress 19 | 3/579 | +20
    Congress 19 | 4/579 | +20
    Congress 19 | 5/579 | +20
    Congress 19 | 6/579 | +20
    Congress 19 | 7/579 | +20
    Congress 19 | 8/579 | +20
    Congress 19 | 9/579 | +20
    Congress 19 | 10/579 | +20
    Congress 19 | 11/579 | +20
    Congress 19 | 12/579 | +20
    Congress 19 | 13/579 | +20
    Congress 19 | 14/579 | +20
    Congress 19 | 15/579 | +20
    Congress 19 | 16/579 | +20
    Congress 19 | 17/579 | +20
    Congress 19 | 18/579 | +20
    Congress 19 | 19/579 | +20
    Congress 19 | 20/579 | +20
    Congress 19 | 21/579 | +20
    Congress 19 | 22/579 | +20
    Congress 19 | 23/579 | +20
    Congress 19 | 24/579 | +20
    Congress 19 | 25/579 | +20
    Congress 19 | 26/579 | +20
    Congress 19 | 27/579 | +20
    Congress 19 | 28/579 | +20
    Congress 19 | 29/579 | +20
    Congress 19 | 30/579 | +20
    Congress 19 | 31/579 | +20
    Congress 19 | 32/579 | +20
    Congress 19 | 33/579 | +20
    Congress 19 | 34/579 | +20
    Congress 19 | 35/579 | +20
    Congress 19 | 36/579 | +20
    Congress 19 | 37/579 | +20
    Congress 19 | 38/579 | +20
    Congress 19 | 39/579 | +20
    Congress 19 | 40/579 | +20
    Congress 19 | 41/579 | +20
    Congress 19 | 42/579 | +20
    Congress 19 | 43/579 | +20
    Congress 19 | 44/579 | +20
    Congress 19 | 45/579 | +20
    Congress 19 | 46/579 | +20
    Congress 19 | 47/579 | +20
    Congress 19 | 48/579 | +20
    Congress 19 | 49/579 | +20
    Congress 19 | 50/579 | +20
    Congress 19 | 51/579 | +20
    Congress 19 | 52/579 | +20
    Congress 19 | 53/579 | +20
    Congress 19 | 54/579 | +20
    Congress 19 | 55/579 | +20
    Congress 19 | 56/579 | +20
    Congress 19 | 57/579 | +20
    Congress 19 | 58/579 | +20
    Congress 19 | 59/579 | +20
    Congress 19 | 60/579 | +20
    Congress 19 | 61/579 | +20
    Congress 19 | 62/579 | +20
    Congress 19 | 63/579 | +20
    Congress 19 | 64/579 | +20
    Congress 19 | 65/579 | +20
    Congress 19 | 66/579 | +20
    Congress 19 | 67/579 | +20
    Congress 19 | 68/579 | +20
    Congress 19 | 69/579 | +20
    Congress 19 | 70/579 | +20
    Congress 19 | 71/579 | +20
    Congress 19 | 72/579 | +20
    Congress 19 | 73/579 | +20
    Congress 19 | 74/579 | +20
    Congress 19 | 75/579 | +20
    Congress 19 | 76/579 | +20
    Congress 19 | 77/579 | +20
    Congress 19 | 78/579 | +20
    Congress 19 | 79/579 | +20
    Congress 19 | 80/579 | +20
    Congress 19 | 81/579 | +20
    Congress 19 | 82/579 | +20
    Congress 19 | 83/579 | +20
    Congress 19 | 84/579 | +20
    Congress 19 | 85/579 | +20
    Congress 19 | 86/579 | +20
    Congress 19 | 87/579 | +20
    Congress 19 | 88/579 | +20
    Congress 19 | 89/579 | +20
    Congress 19 | 90/579 | +20
    Congress 19 | 91/579 | +20
    Congress 19 | 92/579 | +20
    Congress 19 | 93/579 | +20
    Congress 19 | 94/579 | +20
    Congress 19 | 95/579 | +20
    Congress 19 | 96/579 | +20
    Congress 19 | 97/579 | +20
    Congress 19 | 98/579 | +20
    Congress 19 | 99/579 | +20
    Congress 19 | 100/579 | +20
    Congress 19 | 101/579 | +20
    Congress 19 | 102/579 | +20
    Congress 19 | 103/579 | +20
    Congress 19 | 104/579 | +20
    Congress 19 | 105/579 | +20
    Congress 19 | 106/579 | +20
    Congress 19 | 107/579 | +20
    Congress 19 | 108/579 | +20
    Congress 19 | 109/579 | +20
    Congress 19 | 110/579 | +20
    Congress 19 | 111/579 | +20
    Congress 19 | 112/579 | +20
    Congress 19 | 113/579 | +20
    Congress 19 | 114/579 | +20
    Congress 19 | 115/579 | +20
    Congress 19 | 116/579 | +20
    Congress 19 | 117/579 | +20
    Congress 19 | 118/579 | +20
    Congress 19 | 119/579 | +20
    Congress 19 | 120/579 | +20
    Congress 19 | 121/579 | +20
    Congress 19 | 122/579 | +20
    Congress 19 | 123/579 | +20
    Congress 19 | 124/579 | +20
    Congress 19 | 125/579 | +20
    Congress 19 | 126/579 | +20
    Congress 19 | 127/579 | +20
    Congress 19 | 128/579 | +20
    Congress 19 | 129/579 | +20
    Congress 19 | 130/579 | +20
    Congress 19 | 131/579 | +20
    Congress 19 | 132/579 | +20
    Congress 19 | 133/579 | +20
    Congress 19 | 134/579 | +20
    Congress 19 | 135/579 | +20
    Congress 19 | 136/579 | +20
    Congress 19 | 137/579 | +20
    Congress 19 | 138/579 | +20
    Congress 19 | 139/579 | +20
    Congress 19 | 140/579 | +20
    Congress 19 | 141/579 | +20
    Congress 19 | 142/579 | +20
    Congress 19 | 143/579 | +20
    Congress 19 | 144/579 | +20
    Congress 19 | 145/579 | +20
    Congress 19 | 146/579 | +20
    Congress 19 | 147/579 | +20
    Congress 19 | 148/579 | +20
    Congress 19 | 149/579 | +20
    Congress 19 | 150/579 | +20
    Congress 19 | 151/579 | +20
    Congress 19 | 152/579 | +20
    Congress 19 | 153/579 | +20
    Congress 19 | 154/579 | +20
    Congress 19 | 155/579 | +20
    Congress 19 | 156/579 | +20
    Congress 19 | 157/579 | +20
    Congress 19 | 158/579 | +20
    Congress 19 | 159/579 | +20
    Congress 19 | 160/579 | +20
    Congress 19 | 161/579 | +20
    Congress 19 | 162/579 | +20
    Congress 19 | 163/579 | +20
    Congress 19 | 164/579 | +20
    Congress 19 | 165/579 | +20
    Congress 19 | 166/579 | +20
    Congress 19 | 167/579 | +20
    Congress 19 | 168/579 | +20
    Congress 19 | 169/579 | +20
    Congress 19 | 170/579 | +20
    Congress 19 | 171/579 | +20
    Congress 19 | 172/579 | +20
    Congress 19 | 173/579 | +20
    Congress 19 | 174/579 | +20
    Congress 19 | 175/579 | +20
    Congress 19 | 176/579 | +20
    Congress 19 | 177/579 | +20
    Congress 19 | 178/579 | +20
    Congress 19 | 179/579 | +20
    Congress 19 | 180/579 | +20
    Congress 19 | 181/579 | +20
    Congress 19 | 182/579 | +20
    Congress 19 | 183/579 | +20
    Congress 19 | 184/579 | +20
    Congress 19 | 185/579 | +20
    Congress 19 | 186/579 | +20
    Congress 19 | 187/579 | +20
    Congress 19 | 188/579 | +20
    Congress 19 | 189/579 | +20
    Congress 19 | 190/579 | +20
    Congress 19 | 191/579 | +20
    Congress 19 | 192/579 | +20
    Congress 19 | 193/579 | +20
    Congress 19 | 194/579 | +20
    Congress 19 | 195/579 | +20
    Congress 19 | 196/579 | +20
    Congress 19 | 197/579 | +20
    Congress 19 | 198/579 | +20
    Congress 19 | 199/579 | +20
    Congress 19 | 200/579 | +20
    Congress 19 | 201/579 | +20
    Congress 19 | 202/579 | +20
    Congress 19 | 203/579 | +20
    Congress 19 | 204/579 | +20
    Congress 19 | 205/579 | +20
    Congress 19 | 206/579 | +20
    Congress 19 | 207/579 | +20
    Congress 19 | 208/579 | +20
    Congress 19 | 209/579 | +20
    Congress 19 | 210/579 | +20
    Congress 19 | 211/579 | +20
    Congress 19 | 212/579 | +20
    Congress 19 | 213/579 | +20
    Congress 19 | 214/579 | +20
    Congress 19 | 215/579 | +20
    Congress 19 | 216/579 | +20
    Congress 19 | 217/579 | +20
    Congress 19 | 218/579 | +20
    Congress 19 | 219/579 | +20
    Congress 19 | 220/579 | +20
    Congress 19 | 221/579 | +20
    Congress 19 | 222/579 | +20
    Congress 19 | 223/579 | +20
    Congress 19 | 224/579 | +20
    Congress 19 | 225/579 | +20
    Congress 19 | 226/579 | +20
    Congress 19 | 227/579 | +20
    Congress 19 | 228/579 | +20
    Congress 19 | 229/579 | +20
    Congress 19 | 230/579 | +20
    Congress 19 | 231/579 | +20
    Congress 19 | 232/579 | +20
    Congress 19 | 233/579 | +20
    Congress 19 | 234/579 | +20
    Congress 19 | 235/579 | +20
    Congress 19 | 236/579 | +20
    Congress 19 | 237/579 | +20
    Congress 19 | 238/579 | +20
    Congress 19 | 239/579 | +20
    Congress 19 | 240/579 | +20
    Congress 19 | 241/579 | +20
    Congress 19 | 242/579 | +20
    Congress 19 | 243/579 | +20
    Congress 19 | 244/579 | +20
    Congress 19 | 245/579 | +20
    Congress 19 | 246/579 | +20
    Congress 19 | 247/579 | +20
    Congress 19 | 248/579 | +20
    Congress 19 | 249/579 | +20
    Congress 19 | 250/579 | +20
    Congress 19 | 251/579 | +20
    Congress 19 | 252/579 | +20
    Congress 19 | 253/579 | +20
    Congress 19 | 254/579 | +20
    Congress 19 | 255/579 | +20
    Congress 19 | 256/579 | +20
    Congress 19 | 257/579 | +20
    Congress 19 | 258/579 | +20
    Congress 19 | 259/579 | +20
    Congress 19 | 260/579 | +20
    Congress 19 | 261/579 | +20
    Congress 19 | 262/579 | +20
    Congress 19 | 263/579 | +20
    Congress 19 | 264/579 | +20
    Congress 19 | 265/579 | +20
    Congress 19 | 266/579 | +20
    Congress 19 | 267/579 | +20
    Congress 19 | 268/579 | +20
    Congress 19 | 269/579 | +20
    Congress 19 | 270/579 | +20
    Congress 19 | 271/579 | +20
    Congress 19 | 272/579 | +20
    Congress 19 | 273/579 | +20
    Congress 19 | 274/579 | +20
    Congress 19 | 275/579 | +20
    Congress 19 | 276/579 | +20
    Congress 19 | 277/579 | +20
    Congress 19 | 278/579 | +20
    Congress 19 | 279/579 | +20
    Congress 19 | 280/579 | +20
    Congress 19 | 281/579 | +20
    Congress 19 | 282/579 | +20
    Congress 19 | 283/579 | +20
    Congress 19 | 284/579 | +20
    Congress 19 | 285/579 | +20
    Congress 19 | 286/579 | +20
    Congress 19 | 287/579 | +20
    Congress 19 | 288/579 | +20
    Congress 19 | 289/579 | +20
    Congress 19 | 290/579 | +20
    Congress 19 | 291/579 | +20
    Congress 19 | 292/579 | +20
    Congress 19 | 293/579 | +20
    Congress 19 | 294/579 | +20
    Congress 19 | 295/579 | +20
    Congress 19 | 296/579 | +20
    Congress 19 | 297/579 | +20
    Congress 19 | 298/579 | +20
    Congress 19 | 299/579 | +20
    Congress 19 | 300/579 | +20
    Congress 19 | 301/579 | +20
    Congress 19 | 302/579 | +20
    Congress 19 | 303/579 | +20
    Congress 19 | 304/579 | +20
    Congress 19 | 305/579 | +20
    Congress 19 | 306/579 | +20
    Congress 19 | 307/579 | +20
    Congress 19 | 308/579 | +20
    Congress 19 | 309/579 | +20
    Congress 19 | 310/579 | +20
    Congress 19 | 311/579 | +20
    Congress 19 | 312/579 | +20
    Congress 19 | 313/579 | +20
    Congress 19 | 314/579 | +20
    Congress 19 | 315/579 | +20
    Congress 19 | 316/579 | +20
    Congress 19 | 317/579 | +20
    Congress 19 | 318/579 | +20
    Congress 19 | 319/579 | +20
    Congress 19 | 320/579 | +20
    Congress 19 | 321/579 | +20
    Congress 19 | 322/579 | +20
    Congress 19 | 323/579 | +20
    Congress 19 | 324/579 | +20
    Congress 19 | 325/579 | +20
    Congress 19 | 326/579 | +20
    Congress 19 | 327/579 | +20
    Congress 19 | 328/579 | +20
    Congress 19 | 329/579 | +20
    Congress 19 | 330/579 | +20
    Congress 19 | 331/579 | +20
    Congress 19 | 332/579 | +20
    Congress 19 | 333/579 | +20
    Congress 19 | 334/579 | +20
    Congress 19 | 335/579 | +20
    Congress 19 | 336/579 | +20
    Congress 19 | 337/579 | +20
    Congress 19 | 338/579 | +20
    Congress 19 | 339/579 | +20
    Congress 19 | 340/579 | +20
    Congress 19 | 341/579 | +20
    Congress 19 | 342/579 | +20
    Congress 19 | 343/579 | +20
    Congress 19 | 344/579 | +20
    Congress 19 | 345/579 | +20
    Congress 19 | 346/579 | +20
    Congress 19 | 347/579 | +20
    Congress 19 | 348/579 | +20
    Congress 19 | 349/579 | +20
    Congress 19 | 350/579 | +20
    Congress 19 | 351/579 | +20
    Congress 19 | 352/579 | +20
    Congress 19 | 353/579 | +20
    Congress 19 | 354/579 | +20
    Congress 19 | 355/579 | +20
    Congress 19 | 356/579 | +20
    Congress 19 | 357/579 | +20
    Congress 19 | 358/579 | +20
    Congress 19 | 359/579 | +20
    Congress 19 | 360/579 | +20
    Congress 19 | 361/579 | +20
    Congress 19 | 362/579 | +20
    Congress 19 | 363/579 | +20
    Congress 19 | 364/579 | +20
    Congress 19 | 365/579 | +20
    Congress 19 | 366/579 | +20
    Congress 19 | 367/579 | +20
    Congress 19 | 368/579 | +20
    Congress 19 | 369/579 | +20
    Congress 19 | 370/579 | +20
    Congress 19 | 371/579 | +20
    Congress 19 | 372/579 | +20
    Congress 19 | 373/579 | +20
    Congress 19 | 374/579 | +20
    Congress 19 | 375/579 | +20
    Congress 19 | 376/579 | +20
    Congress 19 | 377/579 | +20
    Congress 19 | 378/579 | +20
    Congress 19 | 379/579 | +20
    Congress 19 | 380/579 | +20
    Congress 19 | 381/579 | +20
    Congress 19 | 382/579 | +20
    Congress 19 | 383/579 | +20
    Congress 19 | 384/579 | +20
    Congress 19 | 385/579 | +20
    Congress 19 | 386/579 | +20
    Congress 19 | 387/579 | +20
    Congress 19 | 388/579 | +20
    Congress 19 | 389/579 | +20
    Congress 19 | 390/579 | +20
    Congress 19 | 391/579 | +20
    Congress 19 | 392/579 | +20
    Congress 19 | 393/579 | +20
    Congress 19 | 394/579 | +20
    Congress 19 | 395/579 | +20
    Congress 19 | 396/579 | +20
    Congress 19 | 397/579 | +20
    Congress 19 | 398/579 | +20
    Congress 19 | 399/579 | +20
    Congress 19 | 400/579 | +20
    Congress 19 | 401/579 | +20
    Congress 19 | 402/579 | +20
    Congress 19 | 403/579 | +20
    Congress 19 | 404/579 | +20
    Congress 19 | 405/579 | +20
    Congress 19 | 406/579 | +20
    Congress 19 | 407/579 | +20
    Congress 19 | 408/579 | +20
    Congress 19 | 409/579 | +20
    Congress 19 | 410/579 | +20
    Congress 19 | 411/579 | +20
    Congress 19 | 412/579 | +20
    Congress 19 | 413/579 | +20
    Congress 19 | 414/579 | +20
    Congress 19 | 415/579 | +20
    Congress 19 | 416/579 | +20
    Congress 19 | 417/579 | +20
    Congress 19 | 418/579 | +20
    Congress 19 | 419/579 | +20
    Congress 19 | 420/579 | +20
    Congress 19 | 421/579 | +20
    Congress 19 | 422/579 | +20
    Congress 19 | 423/579 | +20
    Congress 19 | 424/579 | +20
    Congress 19 | 425/579 | +20
    Congress 19 | 426/579 | +20
    Congress 19 | 427/579 | +20
    Congress 19 | 428/579 | +20
    Congress 19 | 429/579 | +20
    Congress 19 | 430/579 | +20
    Congress 19 | 431/579 | +20
    Congress 19 | 432/579 | +20
    Congress 19 | 433/579 | +20
    Congress 19 | 434/579 | +20
    Congress 19 | 435/579 | +20
    Congress 19 | 436/579 | +20
    Congress 19 | 437/579 | +20
    Congress 19 | 438/579 | +20
    Congress 19 | 439/579 | +20
    Congress 19 | 440/579 | +20
    Congress 19 | 441/579 | +20
    Congress 19 | 442/579 | +20
    Congress 19 | 443/579 | +20
    Congress 19 | 444/579 | +20
    Congress 19 | 445/579 | +20
    Congress 19 | 446/579 | +20
    Congress 19 | 447/579 | +20
    Congress 19 | 448/579 | +20
    Congress 19 | 449/579 | +20
    Congress 19 | 450/579 | +20
    Congress 19 | 451/579 | +20
    Congress 19 | 452/579 | +20
    Congress 19 | 453/579 | +20
    Congress 19 | 454/579 | +20
    Congress 19 | 455/579 | +20
    Congress 19 | 456/579 | +20
    Congress 19 | 457/579 | +20
    Congress 19 | 458/579 | +20
    Congress 19 | 459/579 | +20
    Congress 19 | 460/579 | +20
    Congress 19 | 461/579 | +20
    Congress 19 | 462/579 | +20
    Congress 19 | 463/579 | +20
    Congress 19 | 464/579 | +20
    Congress 19 | 465/579 | +20
    Congress 19 | 466/579 | +20
    Congress 19 | 467/579 | +20
    Congress 19 | 468/579 | +20
    Congress 19 | 469/579 | +20
    Congress 19 | 470/579 | +20
    Congress 19 | 471/579 | +20
    Congress 19 | 472/579 | +20
    Congress 19 | 473/579 | +20
    Congress 19 | 474/579 | +20
    Congress 19 | 475/579 | +20
    Congress 19 | 476/579 | +20
    Congress 19 | 477/579 | +20
    Congress 19 | 478/579 | +20
    Congress 19 | 479/579 | +20
    Congress 19 | 480/579 | +20
    Congress 19 | 481/579 | +20
    Congress 19 | 482/579 | +20
    Congress 19 | 483/579 | +20
    Congress 19 | 484/579 | +20
    Congress 19 | 485/579 | +20
    Congress 19 | 486/579 | +20
    Congress 19 | 487/579 | +20
    Congress 19 | 488/579 | +20
    Congress 19 | 489/579 | +20
    Congress 19 | 490/579 | +20
    Congress 19 | 491/579 | +20
    Congress 19 | 492/579 | +20
    Congress 19 | 493/579 | +20
    Congress 19 | 494/579 | +20
    Congress 19 | 495/579 | +20
    Congress 19 | 496/579 | +20
    Congress 19 | 497/579 | +20
    Congress 19 | 498/579 | +20
    Congress 19 | 499/579 | +20
    Congress 19 | 500/579 | +20
    Congress 19 | 501/579 | +20
    Congress 19 | 502/579 | +20
    Congress 19 | 503/579 | +20
    Congress 19 | 504/579 | +20
    Congress 19 | 505/579 | +20
    Congress 19 | 506/579 | +20
    Congress 19 | 507/579 | +20
    Congress 19 | 508/579 | +20
    Congress 19 | 509/579 | +20
    Congress 19 | 510/579 | +20
    Congress 19 | 511/579 | +20
    Congress 19 | 512/579 | +20
    Congress 19 | 513/579 | +20
    Congress 19 | 514/579 | +20
    Congress 19 | 515/579 | +20
    Congress 19 | 516/579 | +20
    Congress 19 | 517/579 | +20
    Congress 19 | 518/579 | +20
    Congress 19 | 519/579 | +20
    Congress 19 | 520/579 | +20
    Congress 19 | 521/579 | +20
    Congress 19 | 522/579 | +20
    Congress 19 | 523/579 | +20
    Congress 19 | 524/579 | +20
    Congress 19 | 525/579 | +20
    Congress 19 | 526/579 | +20
    Congress 19 | 527/579 | +20
    Congress 19 | 528/579 | +20
    Congress 19 | 529/579 | +20
    Congress 19 | 530/579 | +20
    Congress 19 | 531/579 | +20
    Congress 19 | 532/579 | +20
    Congress 19 | 533/579 | +20
    Congress 19 | 534/579 | +20
    Congress 19 | 535/579 | +20
    Congress 19 | 536/579 | +20
    Congress 19 | 537/579 | +20
    Congress 19 | 538/579 | +20
    Congress 19 | 539/579 | +20
    Congress 19 | 540/579 | +20
    Congress 19 | 541/579 | +20
    Congress 19 | 542/579 | +20
    Congress 19 | 543/579 | +20
    Congress 19 | 544/579 | +20
    Congress 19 | 545/579 | +20
    Congress 19 | 546/579 | +20
    Congress 19 | 547/579 | +20
    Congress 19 | 548/579 | +20
    Congress 19 | 549/579 | +20
    Congress 19 | 550/579 | +20
    Congress 19 | 551/579 | +20
    Congress 19 | 552/579 | +20
    Congress 19 | 553/579 | +20
    Congress 19 | 554/579 | +20
    Congress 19 | 555/579 | +20
    Congress 19 | 556/579 | +20
    Congress 19 | 557/579 | +20
    Congress 19 | 558/579 | +20
    Congress 19 | 559/579 | +20
    Congress 19 | 560/579 | +20
    Congress 19 | 561/579 | +20
    Congress 19 | 562/579 | +20
    Congress 19 | 563/579 | +20
    Congress 19 | 564/579 | +20
    Congress 19 | 565/579 | +20
    Congress 19 | 566/579 | +20
    Congress 19 | 567/579 | +20
    Congress 19 | 568/579 | +20
    Congress 19 | 569/579 | +20
    Congress 19 | 570/579 | +20
    Congress 19 | 571/579 | +20
    Congress 19 | 572/579 | +20
    Congress 19 | 573/579 | +20
    Congress 19 | 574/579 | +20
    Congress 19 | 575/579 | +20
    Congress 19 | 576/579 | +20
    Congress 19 | 577/579 | +20
    Congress 19 | 578/579 | +1
    Congress 19 | 579/579 | +20
    
    🔄 Congress 20
    Congress 20 | 1/236 | +20
    Congress 20 | 2/236 | +20
    Congress 20 | 3/236 | +20
    Congress 20 | 4/236 | +20
    Congress 20 | 5/236 | +20
    Congress 20 | 6/236 | +20
    Congress 20 | 7/236 | +20
    Congress 20 | 8/236 | +20
    Congress 20 | 9/236 | +20
    Congress 20 | 10/236 | +20
    Congress 20 | 11/236 | +20
    Congress 20 | 12/236 | +20
    Congress 20 | 13/236 | +20
    Congress 20 | 14/236 | +20
    Congress 20 | 15/236 | +20
    Congress 20 | 16/236 | +20
    Congress 20 | 17/236 | +20
    Congress 20 | 18/236 | +20
    Congress 20 | 19/236 | +20
    Congress 20 | 20/236 | +20
    Congress 20 | 21/236 | +20
    Congress 20 | 22/236 | +20
    Congress 20 | 23/236 | +20
    Congress 20 | 24/236 | +20
    Congress 20 | 25/236 | +20
    Congress 20 | 26/236 | +20
    Congress 20 | 27/236 | +20
    Congress 20 | 28/236 | +20
    Congress 20 | 29/236 | +20
    Congress 20 | 30/236 | +20
    Congress 20 | 31/236 | +20
    Congress 20 | 32/236 | +20
    Congress 20 | 33/236 | +20
    Congress 20 | 34/236 | +20
    Congress 20 | 35/236 | +20
    Congress 20 | 36/236 | +20
    Congress 20 | 37/236 | +20
    Congress 20 | 38/236 | +20
    Congress 20 | 39/236 | +20
    Congress 20 | 40/236 | +20
    Congress 20 | 41/236 | +20
    Congress 20 | 42/236 | +20
    Congress 20 | 43/236 | +20
    Congress 20 | 44/236 | +20
    Congress 20 | 45/236 | +20
    Congress 20 | 46/236 | +20
    Congress 20 | 47/236 | +20
    Congress 20 | 48/236 | +20
    Congress 20 | 49/236 | +20
    Congress 20 | 50/236 | +20
    Congress 20 | 51/236 | +20
    Congress 20 | 52/236 | +20
    Congress 20 | 53/236 | +20
    Congress 20 | 54/236 | +20
    Congress 20 | 55/236 | +20
    Congress 20 | 56/236 | +20
    Congress 20 | 57/236 | +20
    Congress 20 | 58/236 | +20
    Congress 20 | 59/236 | +20
    Congress 20 | 60/236 | +20
    Congress 20 | 61/236 | +20
    Congress 20 | 62/236 | +20
    Congress 20 | 63/236 | +20
    Congress 20 | 64/236 | +20
    Congress 20 | 65/236 | +20
    Congress 20 | 66/236 | +20
    Congress 20 | 67/236 | +20
    Congress 20 | 68/236 | +20
    Congress 20 | 69/236 | +20
    Congress 20 | 70/236 | +20
    Congress 20 | 71/236 | +20
    Congress 20 | 72/236 | +20
    Congress 20 | 73/236 | +20
    Congress 20 | 74/236 | +20
    Congress 20 | 75/236 | +20
    Congress 20 | 76/236 | +20
    Congress 20 | 77/236 | +20
    Congress 20 | 78/236 | +20
    Congress 20 | 79/236 | +20
    Congress 20 | 80/236 | +20
    Congress 20 | 81/236 | +20
    Congress 20 | 82/236 | +20
    Congress 20 | 83/236 | +20
    Congress 20 | 84/236 | +20
    Congress 20 | 85/236 | +20
    Congress 20 | 86/236 | +20
    Congress 20 | 87/236 | +20
    Congress 20 | 88/236 | +20
    Congress 20 | 89/236 | +20
    Congress 20 | 90/236 | +20
    Congress 20 | 91/236 | +20
    Congress 20 | 92/236 | +20
    Congress 20 | 93/236 | +20
    Congress 20 | 94/236 | +20
    Congress 20 | 95/236 | +20
    Congress 20 | 96/236 | +20
    Congress 20 | 97/236 | +20
    Congress 20 | 98/236 | +20
    Congress 20 | 99/236 | +20
    Congress 20 | 100/236 | +20
    Congress 20 | 101/236 | +20
    Congress 20 | 102/236 | +20
    Congress 20 | 103/236 | +20
    Congress 20 | 104/236 | +20
    Congress 20 | 105/236 | +20
    Congress 20 | 106/236 | +20
    Congress 20 | 107/236 | +20
    Congress 20 | 108/236 | +20
    Congress 20 | 109/236 | +20
    Congress 20 | 110/236 | +20
    Congress 20 | 111/236 | +20
    Congress 20 | 112/236 | +20
    Congress 20 | 113/236 | +20
    Congress 20 | 114/236 | +20
    Congress 20 | 115/236 | +20
    Congress 20 | 116/236 | +20
    Congress 20 | 117/236 | +20
    Congress 20 | 118/236 | +20
    Congress 20 | 119/236 | +20
    Congress 20 | 120/236 | +20
    Congress 20 | 121/236 | +20
    Congress 20 | 122/236 | +20
    Congress 20 | 123/236 | +20
    Congress 20 | 124/236 | +20
    Congress 20 | 125/236 | +20
    Congress 20 | 126/236 | +20
    Congress 20 | 127/236 | +20
    Congress 20 | 128/236 | +20
    Congress 20 | 129/236 | +20
    Congress 20 | 130/236 | +20
    Congress 20 | 131/236 | +20
    Congress 20 | 132/236 | +20
    Congress 20 | 133/236 | +20
    Congress 20 | 134/236 | +20
    Congress 20 | 135/236 | +20
    Congress 20 | 136/236 | +20
    Congress 20 | 137/236 | +20
    Congress 20 | 138/236 | +20
    Congress 20 | 139/236 | +20
    Congress 20 | 140/236 | +20
    Congress 20 | 141/236 | +20
    Congress 20 | 142/236 | +20
    Congress 20 | 143/236 | +20
    Congress 20 | 144/236 | +20
    Congress 20 | 145/236 | +20
    Congress 20 | 146/236 | +20
    Congress 20 | 147/236 | +20
    Congress 20 | 148/236 | +20
    Congress 20 | 149/236 | +20
    Congress 20 | 150/236 | +20
    Congress 20 | 151/236 | +20
    Congress 20 | 152/236 | +20
    Congress 20 | 153/236 | +20
    Congress 20 | 154/236 | +20
    Congress 20 | 155/236 | +20
    Congress 20 | 156/236 | +20
    Congress 20 | 157/236 | +20
    Congress 20 | 158/236 | +20
    Congress 20 | 159/236 | +20
    Congress 20 | 160/236 | +20
    Congress 20 | 161/236 | +20
    Congress 20 | 162/236 | +20
    Congress 20 | 163/236 | +20
    Congress 20 | 164/236 | +20
    Congress 20 | 165/236 | +20
    Congress 20 | 166/236 | +20
    Congress 20 | 167/236 | +20
    Congress 20 | 168/236 | +20
    Congress 20 | 169/236 | +20
    Congress 20 | 170/236 | +20
    Congress 20 | 171/236 | +20
    Congress 20 | 172/236 | +20
    Congress 20 | 173/236 | +20
    Congress 20 | 174/236 | +20
    Congress 20 | 175/236 | +20
    Congress 20 | 176/236 | +20
    Congress 20 | 177/236 | +20
    Congress 20 | 178/236 | +20
    Congress 20 | 179/236 | +20
    Congress 20 | 180/236 | +20
    Congress 20 | 181/236 | +20
    Congress 20 | 182/236 | +20
    Congress 20 | 183/236 | +20
    Congress 20 | 184/236 | +20
    Congress 20 | 185/236 | +20
    Congress 20 | 186/236 | +20
    Congress 20 | 187/236 | +20
    Congress 20 | 188/236 | +20
    Congress 20 | 189/236 | +20
    Congress 20 | 190/236 | +20
    Congress 20 | 191/236 | +20
    Congress 20 | 192/236 | +20
    Congress 20 | 193/236 | +20
    Congress 20 | 194/236 | +20
    Congress 20 | 195/236 | +20
    Congress 20 | 196/236 | +20
    Congress 20 | 197/236 | +20
    Congress 20 | 198/236 | +20
    Congress 20 | 199/236 | +20
    Congress 20 | 200/236 | +20
    Congress 20 | 201/236 | +20
    Congress 20 | 202/236 | +20
    Congress 20 | 203/236 | +20
    Congress 20 | 204/236 | +20
    Congress 20 | 205/236 | +20
    Congress 20 | 206/236 | +20
    Congress 20 | 207/236 | +20
    Congress 20 | 208/236 | +20
    Congress 20 | 209/236 | +20
    Congress 20 | 210/236 | +20
    Congress 20 | 211/236 | +20
    Congress 20 | 212/236 | +20
    Congress 20 | 213/236 | +20
    Congress 20 | 214/236 | +20
    Congress 20 | 215/236 | +20
    Congress 20 | 216/236 | +20
    Congress 20 | 217/236 | +20
    Congress 20 | 218/236 | +20
    Congress 20 | 219/236 | +20
    Congress 20 | 220/236 | +20
    Congress 20 | 221/236 | +20
    Congress 20 | 222/236 | +20
    Congress 20 | 223/236 | +20
    Congress 20 | 224/236 | +20
    Congress 20 | 225/236 | +20
    Congress 20 | 226/236 | +20
    Congress 20 | 227/236 | +20
    Congress 20 | 228/236 | +20
    Congress 20 | 229/236 | +20
    Congress 20 | 230/236 | +20
    Congress 20 | 231/236 | +20
    Congress 20 | 232/236 | +20
    Congress 20 | 233/236 | +20
    Congress 20 | 234/236 | +20
    Congress 20 | 235/236 | +20
    Congress 20 | 236/236 | +4
    
    🔥 FINAL SHAPE: (63124, 17)
    

#### Saving the Open Congress API Data to a File



As the API can take a lot of time to run, it is highly inefficient to execute it every time this project is initiated. After the first run, it is ideal to save the DataFrame to a file (CSV in this instance) to be loaded in the upcoming steps.


```python
df_all.to_csv("combined_dataset.csv", index=False)
```

### HuggingFace Dataset

#### Loading the Raw Text Dataset

Before the actual analysis, we need the documents itself from HuggingFace. We need to fetch the actual full-text content of the Philippine legislative documents.
- We use the datasets library to pull the documents configuration directly from BetterGovPH's public Hugging Face repository. This specific split houses the transcribed text blocks of over 60,000 bills.
- Once downloaded, the raw dataset is immediately cast into a standard Pandas DataFrame (df_docs). This turns the raw file data into a structured table, setting it up perfectly to be merged with our scraped metadata in the next step.


```python
print("Loading 'bettergovph/raw-philippine-data' splits...")
try:
    docs_ds = load_dataset("bettergovph/raw-philippine-data", "documents", split="train")
    
    df_docs = pd.DataFrame(docs_ds)
    print(f"Loaded {{len(df_docs)}} text documents!")
except Exception as e:
    print(f"Error loading dataset: {e}")
```

    Loading 'bettergovph/raw-philippine-data' splits...
    

    Warning: You are sending unauthenticated requests to the HF Hub. Please set a HF_TOKEN to enable higher rate limits and faster downloads.
    

    ✅ Loaded {len(df_docs)} text documents!
    

#### Saving the HuggingFace Data to a File



As the HuggingFace data may take a long time to run when we execute this notebook, it is better to save the DataFrame to a file (CSV in this instance) to be loaded in the upcoming steps.


```python
#Save df_docs to CSV
df_docs.to_csv("house_bills_documents.csv", index=False)
```

## Data Preprocessing


Before we begin, this next step involves combining the two datasets and merging them into one, as well as cleaning and adjusting the data as necessary before we proceed to the actual analysis and objective. We will be loading first the saved Open Congress API data and clean it by gathering the authors, since the authors are under a dictionary within the **authors** column.


```python
house_bills = pd.read_csv("combined_dataset.csv")
house_bills.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>type</th>
      <th>subtype</th>
      <th>name</th>
      <th>bill_number</th>
      <th>congress</th>
      <th>title</th>
      <th>long_title</th>
      <th>congress_website_title</th>
      <th>congress_website_abstract</th>
      <th>date_filed</th>
      <th>scope</th>
      <th>subjects</th>
      <th>authors_raw</th>
      <th>senate_website_permalink</th>
      <th>download_url_sources</th>
      <th>authors</th>
      <th>document_number</th>
      <th>authors_list</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>01K6D88S3CFAZDFQXJ1HKQ9B6T</td>
      <td>bill</td>
      <td>HB</td>
      <td>HBN-06098</td>
      <td>6098</td>
      <td>13</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>AN ACT ESTABLISHING A DISTRICT BRANCH OF THE L...</td>
      <td>NaN</td>
      <td>2007-02-07</td>
      <td>National</td>
      <td>[]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>['https://docs.congress.hrep.online/legisdocs/...</td>
      <td>[{'aliases': None, 'name_suffix': None, 'last_...</td>
      <td>6098</td>
      <td>Judy Syjuco</td>
    </tr>
    <tr>
      <th>1</th>
      <td>01K6D88S3CFAZDFQXJ1HKQ9B6V</td>
      <td>bill</td>
      <td>HB</td>
      <td>HBN-06099</td>
      <td>6099</td>
      <td>13</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>AN ACT TO BE KNOWN AS \"THE MARINE LIENS AND S...</td>
      <td>\"Maritime Liens and Ship Mortgage Act of 2002...</td>
      <td>2007-02-07</td>
      <td>National</td>
      <td>[]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>['https://docs.congress.hrep.online/legisdocs/...</td>
      <td>[{'aliases': None, 'name_suffix': None, 'last_...</td>
      <td>6099</td>
      <td>Judy Syjuco</td>
    </tr>
    <tr>
      <th>2</th>
      <td>01K6D88S3D4N0HN1JGFXVY2NFK</td>
      <td>bill</td>
      <td>HB</td>
      <td>HBN-06100</td>
      <td>6100</td>
      <td>13</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>AN ACT TO ESTABLISH A HIGHER STANDARD OF PROMO...</td>
      <td>\"overseas Seafarers Code\". The bill seeks to...</td>
      <td>2007-02-07</td>
      <td>National</td>
      <td>[]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>['https://docs.congress.hrep.online/legisdocs/...</td>
      <td>[{'aliases': None, 'name_suffix': None, 'last_...</td>
      <td>6100</td>
      <td>Judy Syjuco</td>
    </tr>
    <tr>
      <th>3</th>
      <td>01K6D88S3JXBFN9BPD330CV32F</td>
      <td>bill</td>
      <td>HB</td>
      <td>HBN-06101</td>
      <td>6101</td>
      <td>13</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>AN ACT INCREASING THE DISCOUNT PRIVILEGE OF SE...</td>
      <td>The bill proposes to increase the discount bei...</td>
      <td>2007-02-07</td>
      <td>National</td>
      <td>[]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>['https://docs.congress.hrep.online/legisdocs/...</td>
      <td>[{'aliases': ['Tony'], 'name_suffix': 'V', 'la...</td>
      <td>6101</td>
      <td>Antonio Veloso Cuenco V</td>
    </tr>
    <tr>
      <th>4</th>
      <td>01K6D88S3AMFGZ9RBXB11ZR864</td>
      <td>bill</td>
      <td>HB</td>
      <td>HBN-06089</td>
      <td>6089</td>
      <td>13</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>AN ACT REGULATING THE RESICADA SYSTEM IN THE C...</td>
      <td>\"Resicada System Regulation Act\". The bill p...</td>
      <td>2007-02-06</td>
      <td>National</td>
      <td>[]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>['https://docs.congress.hrep.online/legisdocs/...</td>
      <td>[{'aliases': ['Ka Paeng'], 'name_suffix': None...</td>
      <td>6089</td>
      <td>Rafael Vitriolo Mariano</td>
    </tr>
  </tbody>
</table>
</div>



### Extracting Author Data

This cell implements a data parsing function to reconstruct those strings back into fully formatted, human-readable author lists.
- The raw authors column holds a string representation of Python lists and dictionaries (e.g., "[{"first_name": "John", ...}]"). ast.literal_eval is used to safely evaluate the string and converts it back into a native Python list of dictionaries.
- The code loops through each author dictionary, pulling individual structural keys: first_name, middle_name, last_name, and name_suffix. It defaults missing or None values to **empty strings ""** to prevent runtime crashes.
- The parser gathers the middle name into the initials, adding a trailing period (e.g., converting "Q" into "Q.") so that names conform to standard legal publishing styles.
- By filtering out empty parts, the code guarantees that a missing middle name or suffix won't create double spaces.
- The function is map-applied across the entire dataset via .apply(), saving the clean results into a brand-new column named authors_list.


```python
import ast

def extract_authors(authors):
    try:
        authors = ast.literal_eval(str(authors))

        names = []
        for author in authors:
            first = author.get("first_name", "") or ""
            middle = author.get("middle_name", "") or ""
            last = author.get("last_name", "") or ""
            suffix = author.get("name_suffix", "") or ""

            # Add period if middle name is a single initial
            middle = middle.strip()
            if len(middle) == 1 and middle.isalpha():
                middle += "."

            full_name = " ".join(
                part for part in [first, middle, last, suffix] if part
            )

            names.append(full_name)

        return ", ".join(names)

    except (ValueError, SyntaxError, TypeError):
        return None

house_bills["authors_list"] = house_bills["authors"].apply(extract_authors)

house_bills[["name", "authors", "authors_list"]].head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>authors</th>
      <th>authors_list</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>HBN-06098</td>
      <td>[{'aliases': None, 'name_suffix': None, 'last_...</td>
      <td>Judy Syjuco</td>
    </tr>
    <tr>
      <th>1</th>
      <td>HBN-06099</td>
      <td>[{'aliases': None, 'name_suffix': None, 'last_...</td>
      <td>Judy Syjuco</td>
    </tr>
    <tr>
      <th>2</th>
      <td>HBN-06100</td>
      <td>[{'aliases': None, 'name_suffix': None, 'last_...</td>
      <td>Judy Syjuco</td>
    </tr>
    <tr>
      <th>3</th>
      <td>HBN-06101</td>
      <td>[{'aliases': ['Tony'], 'name_suffix': 'V', 'la...</td>
      <td>Antonio Veloso Cuenco V</td>
    </tr>
    <tr>
      <th>4</th>
      <td>HBN-06089</td>
      <td>[{'aliases': ['Ka Paeng'], 'name_suffix': None...</td>
      <td>Rafael Vitriolo Mariano</td>
    </tr>
    <tr>
      <th>5</th>
      <td>HBN-06090</td>
      <td>[{'aliases': ['Bel'], 'name_suffix': None, 'la...</td>
      <td>Belma A. Cabilao</td>
    </tr>
    <tr>
      <th>6</th>
      <td>HBN-06091</td>
      <td>[{'aliases': ['Jun'], 'name_suffix': 'Jr', 'la...</td>
      <td>Benasing O. Macarambon Jr</td>
    </tr>
    <tr>
      <th>7</th>
      <td>HBN-06092</td>
      <td>[{'aliases': ['Rel'], 'name_suffix': None, 'la...</td>
      <td>Arrel Reyes Olaño</td>
    </tr>
    <tr>
      <th>8</th>
      <td>HBN-06085</td>
      <td>[{'aliases': None, 'name_suffix': None, 'last_...</td>
      <td>Janette L. Garin</td>
    </tr>
    <tr>
      <th>9</th>
      <td>HBN-06086</td>
      <td>[{'aliases': ['Rey'], 'name_suffix': None, 'la...</td>
      <td>Jesus Reynaldo Bondoc Aquino</td>
    </tr>
  </tbody>
</table>
</div>




```python
house_bills.to_csv("combined_dataset_with_authors.csv", index=False)
```

### Merging with HuggingFace BetterGov Document Content

This next step involves creating one single large dataset, merging the Open Congress API data with the BetterGov content field. Now that we have both the live-scraped metadata (house_bills) and the raw full-text repository (df_docs), we need to join them together.

To ensure a flawless merge without missing records due to case sensitivities or structural formatting mismatches, we perform a quick standardization pass on our keys:
- **Document Type**: We convert the subtype column to lowercase to match the formatting of document_type.
- **Data Types**: We make sure both document_number and bill_number are treated as matching integers.
- **Congress Term**: We guarantee that congress keys in both tables share an identical numerical format.


```python
house_bills["join_type"] = house_bills["subtype"].str.lower()
house_bills["join_num"] = pd.to_numeric(house_bills["bill_number"], errors="coerce").fillna(0).astype(int)
house_bills["join_congress"] = pd.to_numeric(house_bills["congress"], errors="coerce").fillna(0).astype(int)

# Clean keys in the Hugging Face text DataFrame (df_docs)
df_docs["join_type"] = df_docs["document_type"].str.lower()
df_docs["join_num"] = pd.to_numeric(df_docs["document_number"], errors="coerce").fillna(0).astype(int)
df_docs["join_congress"] = pd.to_numeric(df_docs["congress"], errors="coerce").fillna(0).astype(int)

```

This is the merge step, executing the data enrichment merge. We perform a left join to retain all metadata rows while pulling in matched full-text content. We will also drop temporary join columns to keep our final workspace completely clean.


```python

enriched_bills_df = house_bills.merge(
    df_docs[["join_type", "join_num", "join_congress", "content"]],
    on=["join_type", "join_num", "join_congress"],
    how="left"
)

enriched_bills_df = enriched_bills_df.drop(columns=["join_type", "join_num", "join_congress"])
```

This last block returns the result of our process. Do note that the BetterGov dataset is not fully complete. The dataset does not contain all documents. However, there are still ~40,000 records we can work with for this project and will proceed with that.


```python
print(f"📋 Master Dataset Rows: {enriched_bills_df.shape[0]}")
print(f"✅ Successfully Matched Texts: {enriched_bills_df['content'].notna().sum()} rows")
print(f"❌ Unmatched Missing Texts: {enriched_bills_df['content'].isna().sum()} rows")
```

    📋 Master Dataset Rows: 63124
    ✅ Successfully Matched Texts: 39987 rows
    ❌ Unmatched Missing Texts: 23137 rows
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>authors_list</th>
      <th>content</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>HBN-06098</td>
      <td>Judy Syjuco</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>HBN-06099</td>
      <td>Judy Syjuco</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>HBN-06100</td>
      <td>Judy Syjuco</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>HBN-06101</td>
      <td>Antonio Veloso Cuenco V</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>HBN-06089</td>
      <td>Rafael Vitriolo Mariano</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



### Dropping Irrelevant Columns

With our last step in the cleaning stage, we will be removing unecessary columns that do not have any important data for this project. We can remove the following:
- id: Primary key that is hashed will not be useful
- type, subtype, bill_number: Since we have only taken Housebills, we can remove these identifiers, as well as the bill number as it is a part of the name column.
- scope, subjects, authors_raw, senate_website_permalink, download_url_sources, authors, document_number, merge_id, congress_website_abstract: These are not necessary for our analysis.
- title, long_title: Since congress_website_title has the title, we can use that instead.


```python
enriched_bills_df.drop(columns=[
    "id", "type", "subtype", "bill_number", 'scope', "subjects", "authors_raw", 
    "senate_website_permalink", "download_url_sources", "authors", "document_number", "merge_id",
    'title', 'long_title', 'congress_website_abstract'
], inplace=True)
```

### Filtering Records without Document Content and Handling Nulls

Since the records with no gathered content won't be of help, these records will be removed. We also have some null records that remain. To prepare our dataset for the Hugging Face NLP summarization engine, we need to filter out rows that contain missing values in our crucial columns. For this project, I will choose to remove them instead of manually adding them into the dataset.


```python
enriched_bills_with_text = enriched_bills_df[enriched_bills_df['content'].notnull()]
enriched_bills_with_text.isnull().sum()
```




    name                       0
    congress                   0
    congress_website_title     6
    date_filed                 3
    authors_list              11
    content                    0
    dtype: int64




```python
enriched_bills_with_text = enriched_bills_with_text.dropna(subset=["congress_website_title", "date_filed", "authors_list"])
enriched_bills_with_text.isnull().sum()
```




    name                      0
    congress                  0
    congress_website_title    0
    date_filed                0
    authors_list              0
    content                   0
    dtype: int64



### Saving the Dataset for Further Use

Just like the previous source datasets, saving the enriched version will be helpful in our next task, so that we do not need to execute past cells again.


```python
enriched_bills_with_text.to_csv("enriched_bills_with_text.csv", index=False)
enriched_bills_df.to_csv("enriched_bills_full.csv", index=False)
```

## Data Analysis

Now that our dataset has zero null values, we can move to check out the data we've gathered. As seen below, the 13th congress documents have the lowest number of records from where we've gathered, followed by the 20th. 

We can also discover that the 13th and 14th congress have the null records from the previous steps that we've removed as the BetterGov dataset do not have their content. From the 15th to the 19th congress, we see that the number of House Bills increase in a natural logarithmic pattern.


```python
import matplotlib.pyplot as plt
import seaborn as sns

plt.figure(figsize=(10, 5))
# Count bills per congress term
sns.countplot(data=enriched_bills_with_text, x='congress', palette='viridis')

plt.title('Distribution of House Bills Filed Across Congressional Terms (13th - 20th)', fontsize=14, fontweight='bold')
plt.xlabel('Congressional Term', fontsize=12)
plt.ylabel('Number of Bills Filed', fontsize=12)
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.show()
```

    C:\Users\user\AppData\Local\Temp\ipykernel_26332\4106843328.py:6: FutureWarning: 
    
    Passing `palette` without assigning `hue` is deprecated and will be removed in v0.14.0. Assign the `x` variable to `hue` and set `legend=False` for the same effect.
    
      sns.countplot(data=enriched_bills_with_text, x='congress', palette='viridis')
    


    
![png](BILIS_files/BILIS_72_1.png)
    


This next analysis checks which months have the most bills filed. Surprisingly, it is aroung July where it peaks, then immediately descends on the following months. The months except for July do have a relatively similar date filed.


```python
# Convert date column to datetime safely
enriched_bills_with_text['date_filed'] = pd.to_datetime(enriched_bills_with_text['date_filed'], errors='coerce')

# Extract month names
enriched_bills_with_text['month_name'] = enriched_bills_with_text['date_filed'].dt.strftime('%B')
month_order = ['January', 'February', 'March', 'April', 'May', 'June', 
               'July', 'August', 'September', 'October', 'November', 'December']

plt.figure(figsize=(12, 5))
# Plot monthly frequency
sns.countplot(data=enriched_bills_with_text, x='month_name', order=month_order, palette='plasma')

plt.title('The Rhythm of Legislation: In Which Months are Bills Most Frequently Filed?', fontsize=14, fontweight='bold')
plt.xlabel('Month', fontsize=12)
plt.ylabel('Total Bills Filed', fontsize=12)
plt.xticks(rotation=45)
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.show()
```

    C:\Users\user\AppData\Local\Temp\ipykernel_26332\881279771.py:11: FutureWarning: 
    
    Passing `palette` without assigning `hue` is deprecated and will be removed in v0.14.0. Assign the `x` variable to `hue` and set `legend=False` for the same effect.
    
      sns.countplot(data=enriched_bills_with_text, x='month_name', order=month_order, palette='plasma')
    


    
![png](BILIS_files/BILIS_74_1.png)
    



```python

```

This next visualization introduces the most hyper-active lawmakers. Because authors_list is a comma-separated string, we can split it, count individual appearances, and find out who the top 10 most frequent authors are. Leading the list is Rufus Bautista Rodriguez, followed by Luis Raymund Villafuerte, and finishing the podium is Alfred Vargas III.


```python
from collections import Counter

all_authors = []
for authors in enriched_bills_with_text['authors_list'].dropna():
    all_authors.extend([auth.strip() for auth in authors.split(',') if auth.strip()])

# Get the top 10 most common authors
top_authors = Counter(all_authors).most_common(10)
auth_df = pd.DataFrame(top_authors, columns=['Author', 'Bill Count'])

plt.figure(figsize=(10, 6))
sns.barplot(data=auth_df, x='Bill Count', y='Author', palette='magma')

plt.title('Top 10 Most Prolific Lawmakers (By Number of Authored/Co-Authored Bills)', fontsize=14, fontweight='bold')
plt.xlabel('Total Bills Authored', fontsize=12)
plt.ylabel('Lawmaker Name', fontsize=12)
plt.grid(axis='x', linestyle='--', alpha=0.7)
plt.show()
```

    C:\Users\user\AppData\Local\Temp\ipykernel_26332\1784985765.py:14: FutureWarning: 
    
    Passing `palette` without assigning `hue` is deprecated and will be removed in v0.14.0. Assign the `y` variable to `hue` and set `legend=False` for the same effect.
    
      sns.barplot(data=auth_df, x='Bill Count', y='Author', palette='magma')
    


    
![png](BILIS_files/BILIS_77_1.png)
    



```python
pip install wordcloud
```

    Defaulting to user installation because normal site-packages is not writeable
    Collecting wordcloud
      Downloading wordcloud-1.9.6-cp314-cp314-win_amd64.whl.metadata (3.5 kB)
    Requirement already satisfied: numpy>=1.19.3 in c:\users\user\appdata\roaming\python\python314\site-packages (from wordcloud) (2.4.6)
    Requirement already satisfied: pillow in c:\users\user\appdata\roaming\python\python314\site-packages (from wordcloud) (12.1.1)
    Requirement already satisfied: matplotlib in c:\users\user\appdata\roaming\python\python314\site-packages (from wordcloud) (3.10.8)
    Requirement already satisfied: contourpy>=1.0.1 in c:\users\user\appdata\roaming\python\python314\site-packages (from matplotlib->wordcloud) (1.3.3)
    Requirement already satisfied: cycler>=0.10 in c:\users\user\appdata\roaming\python\python314\site-packages (from matplotlib->wordcloud) (0.12.1)
    Requirement already satisfied: fonttools>=4.22.0 in c:\users\user\appdata\roaming\python\python314\site-packages (from matplotlib->wordcloud) (4.62.1)
    Requirement already satisfied: kiwisolver>=1.3.1 in c:\users\user\appdata\roaming\python\python314\site-packages (from matplotlib->wordcloud) (1.5.0)
    Requirement already satisfied: packaging>=20.0 in c:\users\user\appdata\roaming\python\python314\site-packages (from matplotlib->wordcloud) (26.2)
    Requirement already satisfied: pyparsing>=3 in c:\users\user\appdata\roaming\python\python314\site-packages (from matplotlib->wordcloud) (3.3.2)
    Requirement already satisfied: python-dateutil>=2.7 in c:\users\user\appdata\roaming\python\python314\site-packages (from matplotlib->wordcloud) (2.9.0.post0)
    Requirement already satisfied: six>=1.5 in c:\users\user\appdata\roaming\python\python314\site-packages (from python-dateutil>=2.7->matplotlib->wordcloud) (1.17.0)
    Downloading wordcloud-1.9.6-cp314-cp314-win_amd64.whl (308 kB)
    Installing collected packages: wordcloud
    Successfully installed wordcloud-1.9.6
    Note: you may need to restart the kernel to use updated packages.
    

    
    [notice] A new release of pip is available: 25.3 -> 26.1.2
    [notice] To update, run: python.exe -m pip install --upgrade pip
    

This next visualization uses the wordcloud library. Before doing deep AI summaries, we can look at the congress_website_title column to see which topics dominate our history (e.g., "Health", "Education", "Taxation"). Stopwords area also introduced to filter out common words, and will help in identifying themes.


```python
import matplotlib.pyplot as plt
from wordcloud import WordCloud, STOPWORDS

custom_stopwords = set(STOPWORDS)

legislative_noise = {
    'act', 'creating', 'province', 'municipality', 'barangay', 'city', 'national', 
    'appropriating', 'funds', 'therefor', 'purpose', 'purposes', 'amending', 
    'section', 'sections', 'republic', 'hereby', 'converted', 'known', 'said',
    'provinces', 'cities', 'municipalities', 'barangays', 'establishment', 
    'established', 'house', 'bill', 'senate', 'laws', 'law', 'state', 'government',
    'office', 'officer', 'offices', 'department', 'departments', 'board', 'council',
    'philippines', 'philippine', 'region', 'institutionalizing', 'providing', 'provide'
}
custom_stopwords.update(legislative_noise)

all_titles_text = " ".join(enriched_bills_with_text['congress_website_title'].dropna().astype(str)).lower()

wordcloud = WordCloud(
    width=1000, 
    height=500, 
    background_color='white',
    max_words=80,
    stopwords=custom_stopwords,
    collocations=False,
    colormap='Dark2',       # Using a highly contrasting, clean palette
    random_state=42
).generate(all_titles_text)

# 5. Render the final thematic visualization
plt.figure(figsize=(14, 7))
plt.imshow(wordcloud, interpolation='bilinear')
plt.axis('off') 

plt.title('Core Thematic Priorities in Philippine Legislation (Noise-Filtered)', fontsize=16, fontweight='bold', pad=20)
plt.show()
```


    
![png](BILIS_files/BILIS_80_0.png)
    


As you can see, there is an abundance in education, road development, and health, while there are some words regarding penalties, violations, power, tax, which suggest other themes that are not as dominant

## **Natural Language Processing (NLP) & Inference Evaluation Pipeline**

This chapter details the deployment of a localized, privacy-focused Large Language Model (LLM) engine designed to perform automated analysis on Philippine legislative bills. The entire architecture is run locally. The pipeline incorporates a generation component alongside a mathematical validation framework utilizing lexical overlap verification ($ROUGE$).

### Local Model Asset Serialization


Before executing offline inference, the model weights and tokenization configurations must be fetched from the Hugging Face Hub and serialized to local storage. This step downloads the **Qwen2.5-0.5B-Instruct** architecture. This model is an optimized, sub-billion parameter model commonly used for high-density text analysis on local hardware.
- **AutoModelForCausalLM**: This is an architectural class loader for autoregressive language models that handle text generation tasks.


```python
from transformers import AutoModelForCausalLM, AutoTokenizer

model_name = "Qwen/Qwen2.5-0.5B-Instruct"
save_directory = "./qwen_local_model"

print(f"Downloading and saving {model_name} to '{save_directory}'...")

# Download and save the weights and configuration
model = AutoModelForCausalLM.from_pretrained(model_name)
model.save_pretrained(save_directory)

# Download and save the tokenizer settings (crucial for chat templates)
tokenizer = AutoTokenizer.from_pretrained(model_name)
tokenizer.save_pretrained(save_directory)

print("Model and Tokenizer successfully saved locally!")
```

    Downloading and saving Qwen/Qwen2.5-0.5B-Instruct to './qwen_local_model'...
    


    Loading weights:   0%|          | 0/290 [00:00<?, ?it/s]



    Writing model shards:   0%|          | 0/1 [00:00<?, ?it/s]


    Model and Tokenizer successfully saved locally!
    

### Offline Pipeline Instantiation (BEGIN HERE IF THE MODEL IS MADE)


Once the assets are compiled locally, the inference execution framework is constructed. This cell ingests the clean dataset and model to fully function offline.


```python
import pandas as pd
from transformers import pipeline, AutoModelForCausalLM, AutoTokenizer
cleaned_bills_df = pd.read_csv("enriched_bills_with_text.csv")

local_path = "./qwen_local_model"
print(f"Loading generation engine directly from local storage: {local_path}...")

local_model = AutoModelForCausalLM.from_pretrained(local_path)
local_tokenizer = AutoTokenizer.from_pretrained(local_path)

local_generator = pipeline(
    "text-generation", 
    model=local_model,
    tokenizer=local_tokenizer
)

print("Local pipeline ready for offline inference!")
```

    Loading generation engine directly from local storage: ./qwen_local_model...
    


    Loading weights:   0%|          | 0/290 [00:00<?, ?it/s]


    Local pipeline ready for offline inference!
    

### Prompt Engineering & Structural Analysis


To transform raw legislative House Bills into civic summaries, we construct a structured prompt template. The prompt forces the LLM to output a dedicated analysis containing three explicit pillars: **Overview, Core Objectives, and Citizen Impact**.

Important Hyperparameters:
- **apply_chat_template()**: Dynamically injects special tokens (e.g., <|im_start|>, <|im_end|>) required by Qwen to differentiate instructions from input data.
- **temperature**=0.4: Configures the token distribution curve to prioritize deterministic factual text while retaining semantic fluency.


```python
def generate_bill_analysis_local_detailed(title, content):
    if not content or str(content).strip() == "":
        return "No content available to analyze."
        
    truncated_content = str(content)[:2500]
    
    messages = [
        {
            "role": "system", 
            "content": (
                "You are a precise legislative assistant. Provide an objective, highly concise breakdown. "
                "Keep descriptions short, direct, and dense. Avoid conversational filler or repeating background data."
            )
        },
        {
            "role": "user", 
            "content": f"""Analyze this Philippine House Bill. Keep each section to 1-2 concise bullet points or short sentences maximum.

### Overview: Brief context of the bill.
### Core Objectives: Main goals/changes enforced.
### Citizen Impact: Why this matters to citizens.

Bill Title: {title}
Bill Content:
{truncated_content}

Detailed Analysis:"""}
    ]
    
    prompt = local_generator.tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True)
    
    try:
        eos_token = local_generator.tokenizer.eos_token if local_generator.tokenizer.eos_token else "<|im_end|>"
        
        outputs = local_generator(
            prompt, 
            max_new_tokens=400,    # Slightly tightened ceiling
            max_length=None,       # Suppresses the warning flag
            temperature=0.2,       # Lower temperature makes it more factual and concise
            do_sample=True,
            repetition_penalty=1.2, # NEW: Explicitly stops the model from looping or rambling
            pad_token_id=local_generator.tokenizer.eos_token_id,
            tokenizer=local_generator.tokenizer, 
            stop_strings=[eos_token, "<|im_end|>"]
        )
                
        generated_text = outputs[0]['generated_text']
        clean_summary = generated_text.split("<|im_start|>assistant\n")[-1].strip()
        
        if clean_summary.endswith(eos_token):
            clean_summary = clean_summary[:-len(eos_token)].strip()
            
        return clean_summary
        
    except Exception as e:
        return f"Error running local model analysis: {e}"
```

This is a sample output:


```python
sample_title = cleaned_bills_df['congress_website_title'].iloc[0]
sample_content = cleaned_bills_df['content'].iloc[0]

print("\nTesting Local Hybrid Analysis Engine:")
print("-" * 60)
print(generate_bill_analysis_local_detailed(sample_title, sample_content))
print("-" * 60)
```

    [transformers] Both `max_new_tokens` (=600) and `max_length`(=20) seem to have been set. `max_new_tokens` will take precedence. Please refer to the documentation for more information. (https://huggingface.co/docs/transformers/main/en/main_classes/text_generation)
    

    
    Testing Local Hybrid Analysis Engine:
    ------------------------------------------------------------
    ### Overview:
    
    #### Issue and Background Context:
    
    The Philippine Labor Code (P.L. 6727) was enacted in 1989 with the aim of rationalizing wage policy determination by establishing mechanisms and proper standards. This bill aims to reform the current wage system, ensuring fair compensation for workers while maintaining economic stability. The core objectives of this bill include:
    
    1. **Establishing Mechanisms**: To create a transparent and fair system for determining wages based on market rates.
    2. **Proper Standards**: Ensuring that wages reflect the true cost of living and are commensurate with the value of labor.
    3. **Wage Incentives**: Providing incentives for industries to disperse jobs across different regions to enhance economic growth.
    4. **Other Provisions**: Including provisions related to unemployment benefits, social security, and other essential services.
    
    #### Citizen Impact:
    
    This legislation matters significantly to the working class and everyday citizens because it directly impacts their livelihoods and quality of life. Here are some key reasons why this legislation is crucial:
    
    1. **Labor Rights Protection**:
       - **Fair Compensation**: The bill ensures that workers receive just wages, reducing exploitation and increasing their purchasing power.
       - **Decent Work Conditions**: It guarantees safe and healthy working conditions, which are critical for worker safety and health.
    
    2. **Economic Stability**:
       - **Job Dispersal**: By encouraging industry dispersion, the bill helps reduce unemployment and poverty, thereby stabilizing the economy.
       - **Economic Growth**: Fair wages can lead to increased investment in infrastructure, education, and healthcare, fostering economic growth.
    
    3. **Social Services**:
       - **Unemployment Benefits**: The bill provides basic social security measures such as unemployment benefits, which help alleviate financial stress during tough times.
       - **Social Security**: It includes provisions for social security systems like pensions and unemployment insurance, providing long-term support for workers.
    
    4. **Public Health and Safety**:
       - **Safe Working Conditions**: The bill mandates the establishment of safe working conditions, which are vital for public health and safety.
       - **Healthcare Access**: It ensures access to healthcare facilities, which is crucial for workers' physical and mental well-being.
    
    5. **Environmental Protection**:
       - **Workplace Regulations**: The bill enforces workplace regulations to protect workers from hazardous conditions and ensure compliance with environmental laws.
    
    6. **Education and Training**:
       - **Educational Opportunities**: It supports educational opportunities through scholarships and grants, promoting lifelong learning and skill development.
    
    7. **Innovation and Entrepreneurship**:
       - **Entrepreneurship Support**: The bill provides necessary resources and support for entrepreneurs, helping them start and grow businesses.
    
    8. **Cultural and Social Cohesion**:
       - **Community Engagement**: The bill encourages community engagement and participation in decision-making processes, enhancing social cohesion and cultural diversity.
    
    9. **International Cooperation**:
       - **Trade Agreements**: The
    ------------------------------------------------------------
    

### Batch Evaluation Metric Engine


To measure model performance mathematically, we implement an automated validation loop. The generation accuracy is evaluated via the **Compression Ratio** and the **ROUGE (Recall-Oriented Understudy for Gisting Evaluation)** framework, which tests keyword retention against the character input chunk processed by the system.

A sample size of 5 is used to evaluate, as the LLM generation will take a long time. This sample batch is used as a baseline level in terms of the performance of the model.

- **Compression Ratio (Length of summary/Length of original input)**
- **ROUGE Overlap (Self-Coverage evaluation)** - Measures the recall, how many core words were from the original source and successfully persisted inside the generated text


```python

from rouge_score import rouge_scorer

scorer = rouge_scorer.RougeScorer(['rouge1', 'rougeL'], use_stemmer=True)

def evaluate_summary_metrics(original_text, generated_summary):
    """
    Computes mathematical and textual metrics comparing the raw bill text
    with the model's generated analytical output.
    """
    if "Error" in generated_summary or len(generated_summary) < 10:
        return 0.0, 0.0, 0.0
        
    compression_ratio = len(generated_summary) / len(str(original_text))

    scores = scorer.score(str(original_text), generated_summary)
    rouge1_recall = scores['rouge1'].recall
    rougel_recall = scores['rougeL'].recall
    
    return round(compression_ratio, 3), round(rouge1_recall, 3), round(rougel_recall, 3)


sample_size = 5
metric_subset_df = cleaned_bills_df.head(sample_size).copy()

print(f"Running Local Generation & Metric Audit on {sample_size} records...")

summaries, comp_ratios, r1_recalls, rl_recalls = [], [], [], []

for index, row in metric_subset_df.iterrows():
    input_text_chunk = str(row['content'])[:2500]
    
    analysis = generate_bill_analysis_local_detailed(row['congress_website_title'], row['content'])
    summaries.append(analysis)
    
    comp, r1, rl = evaluate_summary_metrics(input_text_chunk, analysis)
    comp_ratios.append(comp)
    r1_recalls.append(r1)
    rl_recalls.append(rl)
    
    print(f"Metrics Tracked for Bill {index+1} | Compression Ratio: {comp*100:.1f}% | Context Recall: {r1*100:.1f}%")

metric_subset_df["generated_analysis"] = summaries
metric_subset_df["metric_compression_ratio"] = comp_ratios
metric_subset_df["metric_rouge1_recall"] = r1_recalls
metric_subset_df["metric_rougel_recall"] = rl_recalls

print("\nPERFORMANCE METRICS EVALUATION PASS COMPLETE!")
metric_subset_df[["name", "metric_compression_ratio", "metric_rouge1_recall", "metric_rougel_recall"]].head()
```

    [transformers] Passing `generation_config` together with generation-related arguments=({'temperature', 'pad_token_id', 'stop_strings', 'max_new_tokens', 'do_sample', 'repetition_penalty'}) is deprecated and will be removed in future versions. Please pass either a `generation_config` object OR all generation parameters explicitly, but not both.
    [transformers] Both `max_new_tokens` (=400) and `max_length`(=20) seem to have been set. `max_new_tokens` will take precedence. Please refer to the documentation for more information. (https://huggingface.co/docs/transformers/main/en/main_classes/text_generation)
    

    Running Local Generation & Metric Audit on 5 records...
    

    [transformers] Both `max_new_tokens` (=400) and `max_length`(=20) seem to have been set. `max_new_tokens` will take precedence. Please refer to the documentation for more information. (https://huggingface.co/docs/transformers/main/en/main_classes/text_generation)
    

    Metrics Tracked for Bill 1 | Compression Ratio: 66.1% | Context Recall: 16.0%
    

    [transformers] Both `max_new_tokens` (=400) and `max_length`(=20) seem to have been set. `max_new_tokens` will take precedence. Please refer to the documentation for more information. (https://huggingface.co/docs/transformers/main/en/main_classes/text_generation)
    

    Metrics Tracked for Bill 2 | Compression Ratio: 49.6% | Context Recall: 14.0%
    

    [transformers] Both `max_new_tokens` (=400) and `max_length`(=20) seem to have been set. `max_new_tokens` will take precedence. Please refer to the documentation for more information. (https://huggingface.co/docs/transformers/main/en/main_classes/text_generation)
    

    Metrics Tracked for Bill 3 | Compression Ratio: 75.6% | Context Recall: 27.4%
    

    [transformers] Both `max_new_tokens` (=400) and `max_length`(=20) seem to have been set. `max_new_tokens` will take precedence. Please refer to the documentation for more information. (https://huggingface.co/docs/transformers/main/en/main_classes/text_generation)
    

    Metrics Tracked for Bill 4 | Compression Ratio: 33.0% | Context Recall: 9.2%
    Metrics Tracked for Bill 5 | Compression Ratio: 40.0% | Context Recall: 14.3%
    
    PERFORMANCE METRICS EVALUATION PASS COMPLETE!
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>metric_compression_ratio</th>
      <th>metric_rouge1_recall</th>
      <th>metric_rougel_recall</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>HBN-04293</td>
      <td>0.661</td>
      <td>0.160</td>
      <td>0.081</td>
    </tr>
    <tr>
      <th>1</th>
      <td>HBN-04303</td>
      <td>0.496</td>
      <td>0.140</td>
      <td>0.074</td>
    </tr>
    <tr>
      <th>2</th>
      <td>HBN-04250</td>
      <td>0.756</td>
      <td>0.274</td>
      <td>0.145</td>
    </tr>
    <tr>
      <th>3</th>
      <td>HBN-04251</td>
      <td>0.330</td>
      <td>0.092</td>
      <td>0.057</td>
    </tr>
    <tr>
      <th>4</th>
      <td>HBN-04252</td>
      <td>0.400</td>
      <td>0.143</td>
      <td>0.071</td>
    </tr>
  </tbody>
</table>
</div>



### Statistical Visualization


The final component uses descriptive analytics to track the process. This block of code plots the distribution curves for text compression ratios and vocabulary retention metrics to visually identify pipeline performance anomalies.


```python
import matplotlib.pyplot as plt
import seaborn as sns

avg_compression = metric_subset_df['metric_compression_ratio'].mean() * 100
avg_recall = metric_subset_df['metric_rouge1_recall'].mean() * 100

print("Global Pipeline Performance Benchmarks:")
print(f"Average Text Compression Ratio: {avg_compression:.2f}%")
print(f"Average Core Keyword Recall (ROUGE-1): {avg_recall:.2f}%")

fig, axes = plt.subplots(1, 2, figsize=(14, 5))

sns.histplot(metric_subset_df['metric_compression_ratio'] * 100, kde=True, ax=axes[0], color='teal', bins=5)
axes[0].axvline(avg_compression, color='red', linestyle='--', label=f'Mean: {avg_compression:.1f}%')
axes[0].set_title('Distribution of Output Compression Ratios', fontsize=12, fontweight='bold')
axes[0].set_xlabel('Compression (%)')
axes[0].set_ylabel('Count')
axes[0].legend()

sns.histplot(metric_subset_df['metric_rouge1_recall'] * 100, kde=True, ax=axes[1], color='indigo', bins=5)
axes[1].axvline(avg_recall, color='red', linestyle='--', label=f'Mean: {avg_recall:.1f}%')
axes[1].set_title('Distribution of Context Keyword Recall (ROUGE-1)', fontsize=12, fontweight='bold')
axes[1].set_xlabel('Recall Accuracy (%)')
axes[1].set_ylabel('Count')
axes[1].legend()

plt.suptitle('Performance Metrics Evaluation for Local Legislative Summarization Pipeline', fontsize=14, fontweight='bold', y=1.02)
plt.tight_layout()
plt.show()
```

    Global Pipeline Performance Benchmarks:
    Average Text Compression Ratio: 52.86%
    Average Core Keyword Recall (ROUGE-1): 16.18%
    


    
![png](BILIS_files/BILIS_99_1.png)
    


## Analysis

**Output Compression**
- Because we asked the model to write a highly comprehensive analysis broken down into three distinct sections **(Overview, Core Objectives, and Citizen Impact)**, a compression ratio in the 50% range is generally okay. It shows that the model is condensed enough to filter out raw legal clutter, but verbose enough to maintain the structural formatting and rich explanatory content. 


**ROUGE-1**
- For generative or abstractive language models, a ROUGE-1 recall rate around 15% is generally poor. It does capture some instances where the local Qwen model pulls key diagnostic topics (like exact policy names, affected civic sectors, or specific economic conditions) directly from the source text, rather than hallucinating or drifting into generic filler phrases. However, this still indicates poor keyword overlap and significant loss of important information. 

However, due to the dense text of the House Bills, this ROUGE-1 recall result might be an indication that the model did not capture random words that cluttered the House Bills. That is something to be considered as well. This goes the same for Output Compression. Additionally, it is important to take note that 5 samples were only used for this evaluation.

## Conclusion

This project successfully demonstrates the deployment of a localized, high-efficiency data pipeline designed to ingest, serialize, and analyze structural Philippine legislative documents (House Bills). The system establishes a secure, cost-effective blueprint by using a locally initialized model for processing sensitive or large-scale civic data locally.
- The integration of a localized sub-billion parameter model (Qwen2.5-0.5B-Instruct) paired with custom prompt engineering proved that compact, consumer-grade hardware can perform semantic extractions. Though the evaluation was not high for compressing dense legislative text, it was still able to summarize in an understandable manner.
- Ultimately, this research is able to bridge the gap between complex legal data and public accessibility, showing that localized artificial intelligence can successfully transform unorganized public records into clear, actionable civic summaries.

While this implementation focused on a specific validation subset of House Bills, the underlying data pipeline can serve as the groundworks for a comprehensive, production-grade search and tracking web application for both the House of Representatives and the Senate of the Philippines.
- Plaintext (Raw Documents)
- Localized Preprocessing Engine
- Local LLM / Summarizer
- Structured Vector Database
- Public Web Interface

By expanding the ingestion layer to process diverse legal document types to include Senate Bills, Republic Acts, Committee Reports, and Explanatory Notes, this pipeline can feed a centralized, two-sided web portal. Organizers, researchers, public citizens, and my target audience, the Filipino youth, could instantly access breakdowns of any active legislation.

Future iterations of this project will focus on upgrading the local engine to more robust, high-capacity models. While the $0.5B$ parameter model provides exceptional speed and ultra-low memory requirements, moving toward larger local architectures will significantly boost the pipeline's analytical depth. Larger models possess broader internal vocabulary maps and superior cross-referencing capabilities. This upgrade will allow the pipeline to:
- Handle longer context windows.
- Identify loopholes and provisions, and historical policy precedents across multiple bills.
- Improve $ROUGE$ accuracy and lower temperature variance, yielding highly nuanced summaries that read like professional legal briefs.
