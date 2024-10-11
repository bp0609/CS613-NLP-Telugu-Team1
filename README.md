# CS613-NLP Team-1 Telugu

## Team Members
The individual contributions as in the sources scraped and crawled by each team member is visible in the [excel sheet](https://docs.google.com/spreadsheets/d/1Kr59i-8Gyhi3ehN_hLVCPdBcms7L07xNUUFsTW3uFDk/edit?usp=sharing). Apart from that the contributions include:
1. **Bhavik Patel (22110047)** - Handling of server files and organising the entire codebase into main directories and sub-directories across local machines and the server. Effort towards compiling the enitre data onto the server. Finally assisted in entering data in the sheet.
2. **Guntas Singh Saran (22110089)** - Writing and summarising this `README.md` file. Assisted in compiling the pipelined code for DeDuplication using MinHashLSH. Made codes for uploading the datasets onto [HuggingFace](https://huggingface.co/guntas-13). Maintained the GitHub Repository.
3. **Hitesh Kumar (22110098)** - Compiling of all the Pre-Existing Corpus and Loading these datasets from `.parquet` files to `.csv` to finally `.txt` files. Effort towards compiling the enitre data onto the server. Finally assisted in entering data in the sheet.
4. **Ruchit Jagodara (22110102)** - Figured out using `BeautifulSoup` for crawling and scraping. Assisted in compiling the pipelined code for DeDuplication using MinHashLSH.
5. **Jinil Patel (22110184)** - Assisted in the writing the Data PreProcessing codes and compilation of the BadWords. Finally assisted in entering data in the sheet.

## [Telugu Corpus Sheet](https://docs.google.com/spreadsheets/d/1Kr59i-8Gyhi3ehN_hLVCPdBcms7L07xNUUFsTW3uFDk/edit?gid=1042635267#gid=1042635267)

- Equal contributions from all the team members ensured that the [sheet](https://docs.google.com/spreadsheets/d/1Kr59i-8Gyhi3ehN_hLVCPdBcms7L07xNUUFsTW3uFDk/edit?usp=sharing) was updated time to time.
- We compiled a lot of pre-existing corpus from HuggingFace including **AI4Bharat**, **WikiMedia**, **ROOTS**, **ALLENAI**, **OSCAR** and many more but downloading from just 5-6 sources provided 100GB+ of clean data for Telugu.
- We compiled almost **~13GB** of crawled data with **11.7L+** articles crawled and around **~120GB** of existing data.
- Much of the data was removed in the **pre-processing** stage. Like we scraped the entire **Telugu Wikipedia** amounting to **3.6GB+**, yet owing to bad words or sensitive information, much of it got filtered out in the pre-processing.

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/MainSheet.png" style="width: 80%">
</div>

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/PreProcessed.png" style="width: 80%">
</div>

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/Existing.png" style="width: 80%">
</div>

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/CSVs.jpeg" style="width: 50%">
</div>

**The screenshot above does not contain the entire data and even this data is still in `.csv` format, which got even expanded after converting them to individual `.txt` files.**


## Data Scraping and Crawling [`Scraping_CrawlingCodes`](https://github.com/guntas-13/CS613-NLP/tree/main/Scraping_CrawlingCodes)
<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/Crawling.jpeg" style="width: 80%">
</div>

Initially, we were using `Selenium` but quite early on switched to `BeautifulSoup` for this task. We employed `Multi-threading` to speed up the scraping and crawling tasks. <br>
Our basic pipeline was:

- We created a main folder for each source named `<source-name>-<team-member>`.
- In each of these folders, there were two folders `WebCrawl` and `LinkScrape`.
- Additionally, the main folder also contained a Jupyter Notebook that was used as an experimentation to know the interface of that particular source.
- All the scraped links were stored in `.csv` files, and then later, the crawling code took over, saving each individual article in separate `.txt` files.


<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/MainFile.png" style="width: 40%">
</div>

An example of `LinkScrape.py`
```python
from bs4 import BeautifulSoup
import urllib.request

def get_links(content):
    soup = BeautifulSoup(content, 'html.parser')
    main_div = soup.find('div', class_='band')
    anchors = main_div.find_all('a', class_="read-more", href=True)
    links = [a['href'] for a in anchors]
    return links

def crawl_data_from_link_with_retry(link, max_retries=3, retry_interval=5):
    retries = 0
    while retries < max_retries:
        try:
            response = urllib.request.urlopen(link)
            if response.status == 200:
                return response.read()
            else:
                print(f"Failed to fetch data from {link}. Retrying... ({retries + 1}/{max_retries})")
                retries += 1
                time.sleep(retry_interval)
        except Exception as e:
            print(f"An error occurred while fetching data from {link}: {e}. Retrying... ({retries + 1}/{max_retries})")
            retries += 1
            time.sleep(retry_interval)
    print(f"Failed to fetch data from {link} after {max_retries} retries.")
    return None
```

An example of `WebCrawl.py`
```python
def extract_data_from_html(html_content):
    try:
        soup = BeautifulSoup(html_content, "html.parser")
        
        extracted_data = ""
        for paragraph in soup.find_all('p'):
            extracted_data += paragraph.get_text() + "\n"

    except Exception as e:
        print(f"An error occurred while extracting Telugu data: {e}")
    
    return extracted_data
```


## Data Preprocessing [`DataPreprocessing`](https://github.com/guntas-13/CS613-NLP/tree/main/DataPreprocessing)

### 1. `TextToCSV.py`
This script processes text files from a specified directory, extracting links and text content, and compiles them into a single CSV file. Since initially we had scraped files as separate `.txt` files, it was necessary that for summarising the data, this was needed.

### 2. `PreProcess.py`
This script cleans a CSV file by removing entries that contain unwanted content, such as **bad words**, email addresses, and phone numbers. <br>
We had compiled bad words from the this [source](https://github.com/thisandagain/washyourmouthoutwithsoap/blob/develop/data/build.json) and later with the help of our Telugu friends, we added more words and made the [`badwords.json`](https://github.com/guntas-13/CS613-NLP/blob/main/badwords.json). We further made sure that no personal information gets leaked into the corpus like phone numbers or email addresses; and all those articles we're flagged and removed. <br> <br>
`PreProcess.py` takes your data in `.csv` format and separates the data into two files - `<source>_clean_articles.csv` and `<source>_bad_articles.csv`. The latter file also contains a column to show why that particular article has been flagged and removed.

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/PreProcess.png" style="width: 90%">
</div>

#### Regular Expression Patterns
Next, the script defines regular expression patterns for identifying email addresses and phone numbers:

```python
bad_words = "|".join(bad_words)
email_pattern = r"\S+@\S+"
phone_pattern = r'[\+\(]?[0-9][0-9 .\-\(\)]{5,}[0-9]'
correct_phone_pattern = r"\s?\d{4}-\d{4}\s?$"
```

- `bad_words`: A combined pattern of bad words.
- `email_pattern`: Matches standard email formats.
- `phone_pattern`: Matches various phone number formats.
- `correct_phone_pattern`: Ensures phone numbers follow a specific formatting standard (e.g., 1234-5678).

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/BadWords.jpeg" style="width: 80%">
</div>

<div align = "center">
<i>Screenshot showing why articles were flagged and removed containing sensitive information or bad words.</i>
</div>

### 3. `HyperLinks.py`
This script processes the cleaned CSV file to extract English text segments and modify the original content to replace unwanted strings, such as hyperlinks using the `<|hyperlink|>` tag.

```python
segments = re.findall(r'[a-zA-Z0-9»!@#$%^&*()_+={}\[\]:;"\'|\\<,>.?/~\s\n%+^-]+', text)

def remove_strings_from_row(content, strings_to_remove):
    if len(strings_to_remove) > 0 and (("https" in strings_to_remove) or ("www" in strings_to_remove) or ("http" in strings_to_remove) or ("http://" in strings_to_remove)):
        if strings_to_remove[-1] == " " or strings_to_remove[-1] == "\n" or strings_to_remove[-1] == "»":
            content = content.replace(strings_to_remove[:-1], ' <|hyperlink|> ')
        else:
            content = content.replace(strings_to_remove, ' <|hyperlink|> ')
    return content
```

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/HyperLinks.png" style="width: 80%">
</div>


## Data DeDuplication [`DeDuplication`](https://github.com/guntas-13/CS613-NLP/tree/main/DeDuplication)

**We were not able to perform deduplication on the entire dataset but did on a subset on a local machine of 3 sources. Although we were at least able to compile all the data on the server to be ready for deduplication, owing to time constraints, we were not able to finish it. Hence the task of making the final table is still incomplete**

### 1. `CreateFilePaths.py`

This script scans specified directories for `.txt` files and compiles their paths into a CSV file. It utilizes multithreading for faster processing.

### 2. `MinHashLSH_Query.py`
This script reads file paths from the CSV generated by the previous script, computes MinHash signatures for each file, and uses Locality-Sensitive Hashing (LSH) to identify duplicates. We set the Jaccard Similarity threshold at **0.8**.

**Key Functions**:

- `get_minhash(content, num_perm=128)`: Generates a MinHash signature for the file content.
- `deduplicate_files(filepaths, threshold=0.8)`: Identifies duplicate files based on MinHash similarities.

### 3. `FilterFalse.py`
This script filters CSV files in a specified directory, keeping only those rows where the similarity exceeds a defined threshold. It modifies the original files in place.

### 4. `FilterDuplicates.py`
This script processes the CSV files containing similarity results, determining which files to keep and which to remove based on their similarity scores.

### 5. `FinalRemove.py`
The final script reads a list of files to be removed from a text file and deletes those files from the filesystem.

### 6. [`stats.sh`](https://github.com/guntas-13/CS613-NLP/blob/main/DeDuplication/stats.sh)
We even wrote a bash script that would give the number of files and the folder size for each of the sources in our main folder.

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/Script.png" style="width: 80%;">
</div>


## The DeDuplication Pipeline

- All the sources need to be present in one main folder as directory containing all the articles from that source in `.txt` format.

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/Corpus.png" style="width: 40%; float: left;">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/FileStructure.png" style="width: 40%;">
</div>

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/Server.jpeg" style="width: 100%;">
</div>

<div align = "center">
    <i>An attempt at how we were compiling all our data onto the server to be ready for deduplication.</i>
</div>

- Then, after this file setup, we ran `CreateFilePaths.py` that gave each article a unique ID in terms of their file paths on the respective machines.

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/FilePaths.png" style="width: 20%;">
</div>

- Next, two directories needed to be created, `Similarity` and `Logs`, and the script `MinHashLSH_Query.py` was run. This will create CSVs in the `Similarity` directory in chunks to avoid RAM overflows. These CSVs contain the duplicates figured out by the LSH object with their actual similarity values with its neighbors to later account for FALSE POSITIVES.

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/SimilarityDir.png" style="width: 40%;">
</div>

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/SimilarityLSH.png" style="width: 80%;">
</div>

<div align = "center">
    <i>Notice how there are 2 false positives with a similarity of less than 0.8 that came in. These will be filtered.</i>
</div>

- Now once, we have these CSVs, we run `FilterFalse.py` that will overwrite these CSVs, removing the rows that were False Positives.
- Then `FilterDuplicates.py` is run, which will check which file to remove out of the pair in a row in the above CSVs. This will create a log `.txt` file in the `Logs` directory of the **filepaths** of all the articles that need to be removed.

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/Logs.png" style="width: 40%;">
</div>

- Then, at last, the `FinalRemove.py` is run, which will do the honors of discarding these logged files.

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/ToDelete.png" style="width: 20%;">
</div>


## Data Uploading to Server and HuggingFace

- Since our pipeline had several raw `.txt` files and bad/clean `.csv` files we tried to upload them as single `.zip` file to [HuggingFace](https://huggingface.co/guntas-13).
- Later the clean data which was in `.csv` format was uploaded in `dataset` repositories on HuggingFace and those were in `.parquet` format, which were easily downloaded on the server side or any other machine
- We used the notebook [`DataUpload.ipynb`](https://github.com/guntas-13/CS613-NLP/blob/main/DataUpload.ipynb) to upload the datasets over on HuggingFace.

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/HF.png" style="width: 80%">
</div>

<div align = "center">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/Repo.png" style="width: 45%; float: left;">
    <img src = "https://github.com/guntas-13/CS613-NLP/blob/main/Media/dataset.png" style="width: 45%;">
</div>

Quite often we leveraged script commands to transfer data from our local machines by zipping also to the server using the server copy (`scp`) command:

```bash
scp <local_file_path> telugu_nlp@10.0.62.212:<file_path_on_server>
```


## MinHashLSH Algorithm

_Source: https://leons.im/posts/a-python-implementation-of-simhash-algorithm/_ <br>
_Souce: https://ekzhu.com/datasketch/minhash.html_ <br>
_Source: https://blog.dataiku.com/joining-the-dots-efficiently-scaling-set-matching-with-lazo-and-minhashlsh_

The MinHashLSH (MinHash Locality-Sensitive Hashing) algorithm is a powerful technique for estimating the Jaccard similarity between sets. The main idea is to reduce the dimensionality of the data while preserving the pairwise similarity between sets. The process involves several key steps:

### 1. Shingling
To begin with, a document is converted into a set of shingles (or k-grams). For instance, given a string, we can extract overlapping substrings of length $k$.

```math
S_k = \{ s_i, s_{i+1}, \ldots, s_{i+k-1} \}
```

where $s_i$ is the $i^{th}$ character in the string.

### 2. MinHashing
Next, a MinHash signature is generated for each set of shingles. This involves creating multiple hash functions $h_1, h_2, \ldots, h_n$ that map the shingles to a range of integers. The MinHash of a set $S$ is the minimum value produced by these hash functions.

The MinHash of set $S$ is defined as:

```math
\text{MinHash}(S) = \min_{s \in S} \{ h(s) \}
```

For  $k$ hash functions, we can generate a MinHash signature of length $k$:

```math
\text{signature}(S) = \begin{bmatrix}
\text{MinHash}_1(S) \\
\text{MinHash}_2(S) \\
\vdots \\
\text{MinHash}_k(S)
\end{bmatrix}
```

### MinHash: Estimating Jaccard Similarity
MinHash (short for Min-wise Independent Permutations Hashing) is a technique used to estimate the Jaccard similarity between two sets.

#### Jaccard Similarity
For two sets $A$ and $B$, the Jaccard similarity $J(A, B)$ is defined as:

```math
J(A, B) = \frac{|A \cap B|}{|A \cup B|}
```

This measures the size of the intersection relative to the union of the sets. The closer the value is to 1, the more similar the sets are.

#### How MinHash Works
MinHash allows us to approximate the Jaccard similarity between two sets by applying a random permutation to the elements of each set and selecting the smallest (minimum) hash value of the elements after the permutation. By repeating this process with multiple hash functions (i.e., permutations), we can estimate the Jaccard similarity.

Let $h_1, h_2, \ldots, h_k$ be a family of random hash functions. For each set $A$, compute the minimum value of the hash functions for each permutation:

```math
\text{MinHash}(A) = \left(\min(h_1(A)), \min(h_2(A)), \ldots, \min(h_k(A))\right)
```

The probability that the minimum hash values for two sets $A$ and $B$ are the same is equal to the Jaccard similarity:

```math
P(\min(h_i(A)) = \min(h_i(B))) = J(A, B)
```

Thus, the more MinHashes that match between two sets, the more similar the sets are, with the proportion of matching MinHashes approximating the Jaccard similarity:

```math
\hat{J}(A, B) \approx \frac{\text{Number of matching MinHashes}}{\text{Total number of MinHashes}}
```

#### MinHash in Practice
Instead of computing full hash permutations, practical implementations simulate random permutations using a fixed number of permutations (e.g., 128). The resulting signature is a compact representation of the set, allowing for fast comparison.

### Locality-Sensitive Hashing (LSH): Efficient Similarity Search
MinHashLSH builds on MinHash by enabling efficient approximate nearest-neighbor search. The goal of LSH is to quickly identify similar items in large datasets without comparing every possible pair.

#### Hash Buckets and LSH
LSH works by hashing similar items (sets or documents) into the same "bucket" with high probability, allowing us to efficiently retrieve candidate near-duplicates.

#### Key Idea
The MinHash signature for a set is divided into multiple "bands" of hash values. Each band is hashed into a separate hash table (bucket). If two items share the same band in at least one hash table, they are considered potential candidates for similarity (i.e., they are "near neighbors"). For example:

Let $s_1, s_2, \ldots, s_k$ be the MinHash signature for a document. Divide the signature into $b$ bands, each containing $r$ hash values (so that $k = b \times r$). Each band is hashed into a hash table:

```math
\text{BandHash}(i) = \text{hash}(s_{(i-1)r+1}, s_{(i-1)r+2}, \ldots, s_{ir})
```

The idea is that similar sets are more likely to share one or more band hash values, thus colliding in at least one hash table.

#### Probability of Collision
The probability that two documents $A$ and $B$ collide in at least one band is related to their Jaccard similarity. Let $p$ be the Jaccard similarity $J(A, B)$ between two sets. The probability that two sets do not collide in one band (i.e., they have different MinHash values in all $r$ positions of that band) is:

```math
P(\text{No collision in one band}) = (1 - p^r)
```

The probability that two sets do not collide in any of the $b$ bands is:

```math
P(\text{No collision in any band}) = (1 - p^r)^{b}
```

Therefore, the probability that two sets collide in at least one band is:

```math
P(\text{Collision}) = 1 - (1 - p^r)^{b}
```

This probability increases as the similarity $p$ increases, meaning that similar sets are likely to collide in at least one band, while dissimilar sets are unlikely to collide.

#### Tuning Parameters
1. $b$ (number of bands): More bands reduce false positives but increase false negatives.
2. $r$ (rows per band): More rows per band reduce false negatives but increase false positives.

#### False Positives and Negatives}
1. **False Positive**: Two dissimilar sets might collide in a bucket due to chance, causing them to be falsely flagged as similar.
2. **False Negative**: Two similar sets might not collide in any bucket, causing them to be missed.

LSH works by balancing these trade-offs, with parameters $b$ and $r$ controlling the precision of the approximation.

### Example Code
```python
import os
import re
from datasketch import MinHash, MinHashLSH

def get_features(s):
    """Extract 3-character shingles from the string."""
    width = 3
    s = s.lower()
    s = re.sub(r'[^\w]+', '', s)
    return [s[i:i + width] for i in range(max(len(s) - width + 1, 1))]

def get_minhash(content, num_perm=128):
    """Convert features derived from content to a MinHash object for LSH comparison."""
    minhash = MinHash(num_perm=num_perm)
    features = get_features(content)
    for feature in features:
        minhash.update(feature.encode('utf-8'))
    return minhash

def lsh_deduplication(documents, threshold=0.8, num_perm=128):
    lsh = MinHashLSH(threshold=threshold, num_perm=num_perm)
    minhashes = {}

    # Create MinHash for each document and insert into LSH
    for doc_id, text in documents.items():
        minhash = get_minhash(text, num_perm=num_perm)
        minhashes[doc_id] = minhash

        lsh.insert(doc_id, minhash)

    # Deduplicate documents based on LSH
    deduplicated_docs = {}
    for doc_id in documents:
        similar_docs = lsh.query(minhashes[doc_id])
        if len(similar_docs) == 1:  # If only one match (itself), it's unique
            deduplicated_docs[doc_id] = documents[doc_id]

    return deduplicated_docs
```


### SQuAD Paper

[SQuAD: 100,000+ Questions for Machine Comprehension of Text](https://arxiv.org/abs/1606.05250) <br>
[Paper Overview](https://sh-tsang.medium.com/brief-review-squad-100-000-questions-for-machine-comprehension-of-text-f191c6b670b8) <br>
[Stanford CoreNLP](https://stanfordnlp.github.io/CoreNLP/) <br>
[Dependency Parsing](https://towardsdatascience.com/natural-language-processing-dependency-parsing-cf094bbbe3f7) <br>
[Universal Dependency Table](https://universaldependencies.org/u/dep/) <br>
