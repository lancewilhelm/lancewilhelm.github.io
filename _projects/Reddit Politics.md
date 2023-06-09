---
layout: page
title: Dashboard for US Political Discourse Analysis
description: CS6242 Data Visualization and Analytics final project, analyzes and displays US political sentiment from Reddit posts and comments
img: assets/img/reddit politics/team009poster.jpg
importance: 1
category: academic
---

<a href="{{ 'assets/pdf/team009poster.pdf' | relative_url}}" target="_blank" rel="noopener noreferrer" class="float-right">Poster <i class="fas fa-file-pdf" style="font-size: 20pt;"></i></a>

## Introduction

Due to the polarized nature of the US political climate, citizens rarely seek information that contradicts their biases. To address this issue, a React dashboard was developed, providing categorized information from a broad spectrum of sources. Data from opposing political subreddits were collected, analyzed, and tagged using PostgreSQL, Python, an NLP model, and other methods. This data informs the dashboard, providing insights on keywords, sentiment, vote differential, source URLs, and newsworthiness of current topics.

## Problem Definition

The US political climate and major media outlets are strongly polarized. This dashboard aims to counteract this polarization by aggregating and comparing information sourced from over 20 million subreddit comments. It offers a comprehensive spectrum of political affiliations and allows easy searchability for topics of interest. This project is pioneering in its scope, as no such comprehensive social media analysis has been attempted previously.

## Literature Survey

Existing literature confirms that social media communities significantly influence political outcomes and misinformation spreads faster on these platforms than traditional outlets. Previous studies informed our data pipeline construction, but their utility was limited due to the use of outdated models or insufficient detail.

## Proposed Method

Our project commences by acquiring raw data from Reddit through the Pushshift API. This data is stored in a PostgreSQL database, which is preprocessed and normalized using spaCy before vectorizing for the NLP algorithm. Named Entity Recognition (NER) algorithm is used to extract keywords for topic lookup and comparison.

A pretrained BERT model is used for NLP and sentiment analysis. The output is weighted average sentiment per community towards a keyword. The frontend UI, built in React, presents the data in a summarized, visual format, offering both summary and detail views. 

## Innovations

Our project stands out for its application of academic social media analysis work to address a real-world problem. We utilize live-scraped Reddit data, enabling access to large-scale, real-time data. Our approach combines several cutting-edge models and algorithms in a novel manner. Lastly, our UI uniquely minimizes the effort needed to access current information across a broad political spectrum (Figure 1).

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/reddit politics/team009poster.jpg" title="Figure 1" class="img-fluid rounded z-depth-1" width=800 zoomable=true %}
        </center>
    </div>
</div>
<div class="caption">
    Figure 1. Application user interface
</div>

## Experiments/Evaluation

Development of the backend took place in a Docker container that contained test copies of the PostgreSQL DB, fetcher API, scheduler, data dumper/cleaner, NLP pipeline, and webhook. Development of the frontend was in the React JavaScript library, utilizing a localhost webhook to the aforementioned backend. Github was used for coordination of development between workstreams. The test data set (as of April 15, 2021) was approximately 1.5M posts and approximately 20.7M comments, each with 111 columns of data. These numbers increase each time our fetcher executes.

We initially planned six sets of experiments, as described below. We made progress on, but were unable to complete, some of the latter experiments because the earlier, more crucial experiments took longer than anticipated. Experiments are numbered; when pertinent, tests within each experiment are lettered.

### Experiment 1: Backend Sanity
#### 1a: 
Confirm we are daily capturing and storing new targeted Reddit comments in PostgreSQL. Our goal was 100% capture at the time our script executed. We manually selected a few post-comment threads from two of the target subreddits and compared directly to the database. No data loss was observed. We did, however, note that our API was recording a lot of useless “[removed]” records from Reddit, so we adjusted our API to ignore any such content. We also noted that, in up to 30% of cases, the URL and/or “permalink” captured expired quickly, rendering the “Go to comment” link in our UI invalid. Future work would include a way to validate the URLs before they are provided as links in the UI (and deactivating the “Go to comment” option when they are not).

#### 1b: 
Confirm our data pipeline accurately normalizes, prepares, performs NER and SA on, and stores ~30 randomly selected comments vs manual analysis. Our goal was at least 80% accuracy and F1 score versus manual analysis/selection, as our literature survey indicated this was likely near the maximum achievable with any automated approach. This took a significant amount of time and is the main reason some other planned experiments were not completed. (We judged that subsequent experiments were moot if this was not completed satisfactorily.) Our first pipeline failed in both NER and SA: no (or invalid) named entities were tagged, and all zeroes were calculated for SA. Much of the trouble stemmed from text parsing or model training issues - for example, the “restaurant review” data set we used for pretraining may have been inadequate for political topics. We even resorted to manual SA and tagging for a while. Eventually, through collaboration, script rewrites, and switching to a more generalized pretraining data set, we achieved roughly 85% accuracy based on our subjective manual comparison of around 100 comments versus the pipeline’s results.

### Experiment 2: Frontend Sanity
#### 2a: 
Confirm all UI action commands (summary-to-detail page swap, keyword selection and swap, etc.) are working. This was completed by the frontend team during development. A few minor issues, such as the “Enter” key not functioning in a topic search, remain.

#### 2b: 
Confirm UI accurately displays all information (top comments from correct subreddit, one-dimension graph, associated keywords, etc.) for ~10 randomly selected keywords vs manual DB query. We expected, and achieved, 100% accuracy here. The UI was designed merely to display the results of the underlying database and data pipeline, not to do any calculation itself. This was confirmed by 20+ comparisons of the frontend renders to the JSON packages sent by the backend.

#### 2c: 
Confirm info is transferring asynchronously (<1s average UI delays in information serving). We had hoped for <1s delays on any term search, but this was not realistic. We adjusted our approach so that if the same term is searched more than once during the same day (meaning that both searches use the same data set), the UI will simply fetch the prior results from the database. But, any term searched for the first time in a day may take 60 or more seconds. A state-of-the-art approach would require modifying the script so that outputs are precalculated for all named entities as soon as the data fetch is completed. This would reduce UI delays, address our primary user complaint in subsequent experiments, and was planned for our next phase of development. However, we had not yet resolved how to precalculate multi-term searches or exclusionary searches (i.e. “Waters -Chauvin”).

### Experiment 3: User Surveys
#### 3a: 
Provide dashboard to family members or acquaintances. Solicit subjective scores on UI design, how well UI reports each subreddit’s sentiment, and likelihood of the user to explore opposing ideologies. Anecdotally, family and friends overwhelmingly approved of the concept. In usage, primary concerns expressed were (1) slowness, (2) method for accessing, (3) believability (especially regarding the 15% inaccuracy rate or occasional counterintuitive results such as r/Republican “disapproving” of a border wall), and (4) utility (in particular, making relevant news article links faster to locate). Around 70% of the 58 persons shown the dashboard thought they might like to use it more in the future. (Of course, our personal relationships with these individuals introduces a bias that is likely to inflate this rate.)

#### 3b: 
Provide dashboard to public non-Reddit sites for similar feedback. We explored several hosting options but did not complete this because we could not find an economical solution due to the processing costs of our date pipeline. Our intention to improve fetch speed by precalculating results for topics would only exacerbate this issue.

#### 3c: 
Provide dashboard to the targeted subreddits for similar feedback. We ran out of time for this. Our intention was to modify the scripts, API and/or UI based on initial Reddit user feedback to encourage usage without compromising accuracy or performance, and to confirm that the way we presented information did not appear to be itself biased. We also wanted to estimate what percentage of users was willing to explore multiple ideologies, and what percentage was likely to use the tool in the future. We thought the users from the targeted subreddits were likely to be less biased in favor of the tool, or perhaps even overly critical/hostile, versus family and friends, so this test would provide our best indicator of tool utility. If feedback in the above surveys was positive, and/or we achieved organic growth of the user base and word-of-mouth advertising, this would signal that our approach is sound even if incomplete. Perhaps future iterations can incorporate these planned surveys and refinements.

### Experiment 4: Controversy Check
Select ~3 topics with expected high controversy (i.e. “Trump”, “Biden”, “border wall”, “abortion”). Manually verify that dashboard info, including news links, are accurate for each subreddit and match intuitive expectations. The three topics selected were “Trump”, “Biden”, and “COVID”. Each topic’s metrics were manually verified against their respective subreddits. The subreddits with mostly negative opinions were displayed as very negative on the dashboard (-0.7 or lower). Subreddits with mostly positive opinions and subreddits with mostly neutral opinions were displayed accurately on the dashboard as well (+0.7 or higher, and -0.3 to +0.3 respectively).

### Experiment 5: Divergence Check
Select ~3 keywords with high divergence (sentiment spreads within a subreddit). Manually analyze our UI for a subjective score of info conveyance and completeness. The three keywords selected were “president”, “China”, and “Biden”. Each of these keywords were positive in the subreddits they were expected to be positive in, and vice versa (e.g. “Biden” was positive in r/Democrats but negative in r/Republican). In r/Libertarian, which generally showed the widest spread of intra-subreddit sentiment for these topics, average sentiment shown on the dashboard appeared in-line with manual analysis and tended to skew neutral (-0.5 to +0.5).

### Experiment 6: Relevance Check
Manually verify that the dashboard and/or database do not have irrelevant topics (such as “and” or “the”) or news links. The API was updated to include only noun topics, rendering this experiment irrelevant. Refinement to remove generic topics (“north”, “station”, etc.) would further improve the dashboard. In our limited user tests, such topics caused few problems simply because the users did not try to search for them. We did, however, note that compound topics (“border wall”, for example) could sometimes cause issues if “border” and “wall” also were identified as topics. Similarly, topics that could refer to multiple entities (“Trump”, “Georgia” and “pipeline”, for example) could result in incorrect analysis if the various subjects are conflated. Both warrant additional investigation.

## Conclusion
We made significant progress in the development of our Reddit sentiment analysis tool, managing to develop a functional backend and frontend, and performing a series of experiments to test the tool's functionality and accuracy. While we were unable to complete some of our planned experiments due to time constraints and unexpected challenges, we have gained valuable insights and have identified areas for improvement. Future work will involve refining our data pipeline to improve accuracy and speed, exploring hosting options to make our tool accessible to a wider audience, and conducting further user surveys to gain more insights into the tool's utility and potential for user adoption. We believe our work has laid a solid foundation for future iterations of this project.
