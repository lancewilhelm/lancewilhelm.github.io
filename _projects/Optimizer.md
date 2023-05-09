---
layout: page
title: USAF Recruit Job Match Optimizer
description: Applied Analytics Practicum, Mixed-integer linear program optimization for matching USAF applicants to jobs
img: assets/img/optimizer/optimizer_equation.png
importance: 1
category: work
---

<a href="{{ 'assets/pdf/Practicum_Final_Report_Wilhelm.pdf' | relative_url}}" target="_blank" rel="noopener noreferrer" class="float-right"><i class="fas fa-file-pdf" style="font-size: 20pt;"></i></a>

## Introduction

The U.S. Air Force (USAF) adopted an optimization algorithm during the 2020 pandemic for job-to-applicant matching. This project explores the efficiency of various optimization heuristics to achieve the best job-matching performance.

## Background

The USAF has been seeking ways to improve its job-matching process, drawing inspiration from algorithms used in medical residency matching and officer job assignments. The Air Force Recruiting Service (AFRS) traditionally used a job draft system, which had limitations in terms of job preferences and matching efficiency.

The optimizer algorithm was introduced during the 2020 pandemic to quickly rematch applicants who had their jobs canceled. A 2022 report from the RAND corporation found that job preference was a significant predictor of success in Initial Skills Training (IST) and early separation. Optimized job assignment could lead to a 3-5% increase in Airmen completing their first term, reenlisting, and promoting to the grade of E-5.

## The Optimizer

The current optimizer is based on Microsoft Excel, utilizing custom macros written in Visual Basic for Applications (VBA). This platform is convenient but limited in computational power compared to modern programming languages like Python and R. This project investigates the performance of loop and deterministic optimization (DO) heuristics.

## Methodology

This study analyzed applicant and job distribution data, developed and ran matching optimization using Jupyter notebooks in Python and R. The goal was to improve the job-to-applicant matching performance of the current systems. A secure Air Force instance of the clustered computing platform Databricks was utilized to host the data and run a Python notebook containing the code used for job-to-applicant matching and analysis. Various heuristics, including loop and DO models, were written for the matching algorithm. Multiple trials were run using different date ranges and job/applicant listings to compare the performance of each method.

## Data

The analysis required two independent datasets:

1. **Applicant List:** This list contains information such as applicant availability for Basic Military Training (BMT) and their rank order job preference list. It also includes other data for potential additional analysis or quality control.
2. **Job Distribution List:** This list contains every job available for new applicants, including the Air Force Specialty Code (AFSC) and the EAD date or "ship date," which is when the applicant will ship to BMT.

For this report, 4 different applicant lists and job distribution lists were considered, with jobs having entry dates ranging from May 1, 2022, to December 30, 2022. The applicant lists corresponded to the current list of available applicants in the Qualified and Waiting (QW) when those job lists were acquired. In total, there were 2431 applicants matched against 3924 jobs.

## Implementation

Multiple heuristics were investigated for matching jobs:

A) Prioritize jobs with a larger interest count; applicants with later DEP entrance dates  
B) Prioritize jobs based on a greater difference between interest and availability; applicants with later DEP entrance dates  
C) Prioritize jobs with larger interest counts; applicants with fewer jobs listed on their preference list  
D) Prioritize jobs based on a greater difference between interest and availability; applicants with fewer jobs listed on their preference list  
E) DO with custom heuristic weights (Equation 1)

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/optimizer/optimizer_equation.png" title="Equation 1" class="img-fluid rounded z-depth-1" width=400 %}
        </center>
    </div>
</div>
<div class="caption">
    Equation 1. The mixed-integer linear program.
</div>

The mixed integer linear program 1's objective function maximizes the weighted combination of the total number of matches, days in the DEP, and preference score. The matrix x represents the match matrix where xij = 1 represents a match of applicant i with job j. The first constraint ensures that only 1 applicant can be matched to a job, and the second constraint ensures that an applicant can only be matched to one job. The third constraint ensures that an applicant can only be matched to a job they are eligible for, where eij represents the eligibility matrix and eij = 1 represents that applicant i is eligible to match with job j. The last constraint about wi ensures that each weight is between 0 and 1, and the sum of the weights must total 1.

#### Python Notebook

The Python Notebook begins by setting important variables and parameters and imports the two datasets into Pandas dataframes. The notebook contains several functions that allow for matching and other analyses against different datasets. The key function for the DO heuristic portion of the matching algorithm is match_MIP_weighted, which takes the list of available applicants, jobs, and weights as arguments and returns the list of applicants with jobs they have been matched with. The loop algorithms driving heuristics A, B, C, and D are all written in similar functions, with the only difference being how the jobs and applicants are sorted.

#### Trials

To increase the number of trials run, the 4 lists of jobs were broken down into different date ranges. This resulted in 12 unique trials where all 5 heuristics were run, including 2 additional trials of the DO heuristic to determine the optimal weights, totaling 62 trials. The initial trials showed that a weighting scheme of (0, 0.5, 0.5) yielded the same number of matches as the (1.0, 0, 0) weighting scheme yet increased the top-3 match rate from 47.5% to 70.71%. A weighting scheme of (0, 0, 1) focusing only on matching applicants with a job as high on their preference list as possible yielded slightly lower overall matches but improved the top-3 match rate to 72.76%.

## Results and Analysis

The results from each trial were stored in an excel sheet containing the following fields: Heuristic, QW, Job Distro, (a) Match rate for each trial (b) Top-3 job match rate for each trial. Figure 1 shows the overall match rate and the top-3 job match rate results for all trials.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/optimizer/match_rates.png" title="Overall match rates" class="img-fluid rounded z-depth-1" width=600 %}
        </center>
    </div>
</div>
<div class="caption">
    Figure 1. Initial Results from all 60 trials
</div>

The overall performance across all trials is not consistent, which is expected given that the trials are run across multiple periods with 4 different datasets and 5 different matching heuristics. More insight should be gleaned from the result attributes and heuristic performance.

### Result Attribute Analysis

The results of the different statistics should be compared to observe any correlations that may be useful to understanding the performance of each trial. This may be parlayed into regression analysis if reasonable. Figure 2 shows a heatmap correlation matrix of the initial trial results.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/optimizer/corr_plot.jpg" title="Correlations" class="img-fluid rounded z-depth-1" width=600 %}
        </center>
    </div>
</div>
<div class="caption">
    Figure 2. Correlations between numerical variables in the results table
</div>

There is a clear positive correlation between jobs and date range length, which follows the logic: as the date range increases, more jobs become available. Next, there is a positive relationship between the total number of applicants on the QW list and who is available. More significantly, there is a positive correlation between the job match rate and the number of applicants on the QW and available QW. This indicates that the more applicants are in our QW and available, the better our match rate will be. Figure 3 shows this correlation and plots a linear regression.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/optimizer/qw_v_match_rate.jpg" title="QW vs. match rate" class="img-fluid rounded z-depth-1" width=600 %}
        </center>
    </div>
</div>
<div class="caption">
    Figure 3. Plot of total QW count versus job match rate
</div>

However, the best-fit line should not be utilized to make useful projections other than rough job match rate performance estimates. Ultimately the recruiting goal is to have a job match rate of 100%, so our positive correlation indicates that to increase the number of matches, the number of applicants on the QW and available should increase. This has long been known within the recruiting enterprise and has been the focus of remedial recruiting efforts in the past year.

Lastly, there appears to be a negative correlation between the job match rate and the jobs to QW ratio. This means that the job match rate decreases as there are more jobs than applicants on the QW. This is a logical relationship as only as many jobs as there are applicants can be filled. A more interesting data subsection would be to observe the matching performance when the job-to-QW ratio is less than or equal to 1.0. This means there are as many or more applicants available as there are jobs. Unfortunately, there are only 20 results when the job to QW ratio was ≤ 1.0, which is likely because of the fact that there are only 4 unique datasets. Also, many of those 20 runs come from the same datasets. This matters because the QW and job distributions will be mostly the same for those periods.

After correlation analysis and consideration of the result attributes, it does not make sense to consider a linear regression for the numerical results given above. Only a few independent predictors could be considered for predicting matches: number of jobs, total QW, available QW, and possibly date range length. All other numerical results in the dataframe are derived from these variables in some fashion and, therefore, should not be considered in a regression analysis as they depend on one another. This logic follows from the above correlation analysis as there may appear to be correlations between variables that depend on one another for calculation.

### Heuristic Analysis

No loop heuristic outperformed the DO heuristic in job-matching performance. A new variable labeled Matches Difference to E was made to describe the difference between each heuristic and the respective DO heuristic for their trial. This allows a finer analysis of the difference between each heuristic. Figure 4 gives box plots representing the differences for each trial between the different heuristics and heuristic E. As there is no data greater than 0, there is no heuristic that outperforms E.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/optimizer/fig_4.jpg" title="Figure 4" class="img-fluid rounded z-depth-1" width=600 %}
        </center>
    </div>
</div>
<div class="caption">
    Figure 4. Differences in matches for each heuristic compared to heuristic E
</div>

However, there are trials where their performance matches, albeit only for trials when the total number of matches is low. Figure 5 shows a clear relationship between the number of matches and the spread of performance in the heuristics. As the number of matches increases, the performance of the loop heuristics decreases, and the spread in performance increases, as indicated by Figure 6.

<div class="row">
    <div class="col-sm-6 mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/optimizer/fig_5.jpg" title="Figure 5" class="img-fluid rounded z-depth-1" width=600 %}
        </center>
        <div class="caption">
            Figure 5. Matches versus matches difference to E
        </div>
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/optimizer/fig_6.jpg" title="Figure 6" class="img-fluid rounded z-depth-1" width=600 %}
        </center>
        <div class="caption">
            Figure 6. Matches versus standard deviation of matches difference to E
        </div>
    </div>
</div>


Run Time: The loop heuristic runs have lower mean values and consistent standard deviations, and the DO heuristic is much larger. This likely has a lot to do with solving a convex optimization problem. Table 1 shows the differences between the run time for each heuristic. Figures 7 and 8 show the relationship between run time and the independent variables that drive run time.

| Heuristic | mean | max | min | std |
| --------- | ---- | --- | --- | --- |
| A | 8.515000 | 30.89 | 1.50 | 7.946796 |
| B | 8.178333 | 32.10 | 1.62 | 8.196935 |
| C | 8.249167 | 31.72 | 2.09 | 8.063265 |
| D | 8.256667 | 31.98 | 1.86 | 8.201959 |
| E | 97.330000 | 839.34 | 0.49 | 235.422577 |

Table 1: Run time, in seconds, statistics by heuristic (n = 12)


<div class="row">
    <div class="col-sm-6 mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/optimizer/fig_7.jpg" title="Figure 7" class="img-fluid rounded z-depth-1" width=600 %}
        </center>
        <div class="caption">
            Figure 7. Run time relationship for the recursive heuristics
        </div>
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        <center>
        {% include figure.html path="assets/img/optimizer/fig_8.jpg" title="Figure 8" class="img-fluid rounded z-depth-1" width=600 %}
        </center>
        <div class="caption">
            Figure 8. Run time relationship for the DO heuristic
        </div>
    </div>
</div>

As the number of matches increases, the time it takes for the loop algorithms to complete is linearly proportional to (QW Available x Jobs). The time it takes for the DO problem to complete grows exponentially with (QW Available + Jobs). This did not pose a particular problem for the trials, as the longest run was 839.34 seconds or about 14 minutes. The run that resulted in that large time elapse had 1039 on the available QW list and 1380 jobs to match. Any run using this technique that utilizes job and applicant totals larger than this must consider the time it will take.

Top-3 Job Match Rate: Table 2 clearly shows that the DO program is the best performer in giving applicants a job from high on their preference list. The minimum from its results is higher than the mean of any of the loop heuristics. It also has one of the lowest standard deviations, meaning that it is consistent in its performance of matching at a top-3 job. The next best performer could be considered a toss-up between heuristics C and D.


| Heuristic | mean | max | min | std |
| --------- | ---- | --- | --- | --- |
| A | 0.4927 | 0.5846 | 0.4310 | 0.0520 |
| B | 0.4665 | 0.6667 | 0.3711 | 0.0832 |
| C | 0.5453 | 0.7692 | 0.4444 | 0.0908 |
| D | 0.5270 | 0.8000 | 0.3750 | 0.1294 |
| E | 0.7699 | 0.8800 | 0.5745 | 0.0866 |

Table 2: Top-3 job match statistics by heuristic (n = 12)

Logically, heuristics C and D perform better in matching a top-3 job because both techniques match applicants who listed fewer jobs on their preference lists. If an applicant only lists 3 jobs on their list, then there is a 100% chance they will match to a top-3 job if they do match. This applicant would be favored over an applicant who lists 10 jobs and may be more flexible.

Overall: If the results from the top-3 job match rate performance and the overall match rate performance are combined, as shown in table 3, heuristic D is the best loop technique, having, on average, 6 more matches per run. This increase in performance stems from the fact that it prioritizes jobs with more available jobs that applicants are interested in. It also stems from its applicant sorting method, which matches less flexible applicants first (i.e. less listed jobs), leaving those applicants who are more flexible (i.e. more jobs listed) to the end. This heuristic exemplifies how clever algorithms can achieve near-optimal performance but require ingenuity.

| Heuristic | mean | max | min | std |
| --------- | ---- | --- | --- | --- |
| A | 180.500000 | 725 | 9 | 208.680051 |
| B | 189.000000 | 755 | 9 | 220.435932 |
| C | 185.416667 | 747 | 9 | 215.999772 |
| D | 191.500000 | 766 | 9 | 224.780903 |
| E | 196.250000 | 786 | 9 | 232.900730 |

Table 3: Match statistics by heuristic (n = 12)


Altogether, it is clear to see the performance increase by utilizing the DO approach. This algorithm allows flexibility in all aspects currently available in the loop techniques while ensuring an optimal matching result. The only downside, which can be circumstantial, is the time cost of the approach. For shorter periods with fewer jobs and applicants available, the time cost is on the same order as the loop techniques. However, the user must consider the time cost once the number of applicants and jobs consider eclipses 1000.

The matching rate results of these trial runs cannot be used to justify one loop heuristic over another. The implications of using one technique over another must be considered by observing the distributions of matched and unmatched jobs and applicants. For example, one heuristic may match more applicants overall, but it leaves more hard-to-fill jobs as a result. Similarly, a heuristic may match more jobs but often gives applicants jobs that are lower on their preference list.

## Future Work

A greater comparison must occur between the optimizer and the widely accepted and utilized job draft matching strategy. This would require an effective simulation of the job draft technique or results from real job drafts combined with the QW and job distributions at the draft time. Even then, it may not be enough to replicate the job draft accurately. More matching actions occur after the draft when flight leaders swap jobs and new applicants become available. The job draft would be hard to simulate as other factors, such as flight chief skill, must also be accounted for. More skilled or experienced flight leaders may be able to secure jobs that are better for their flight.

Instead, a better simulation solution might be a fair-share distribution of jobs to the flights based on their quota share. This would attempt to give every flight the same number of jobs within each major assignment category. This distribution method does not perfectly mimic the job draft, but it could effectively simulate the distribution of jobs to the flight level.

Job booking strategies are closely interwoven into the goaling and incentive structure of recruiting. Recruiting is a sales enterprise that utilizes quotas and awards to incentivize performance. Flight leaders often adopt a job matching strategy such as the job draft because it allows them the most control and visibility of available jobs. Understandably, having an algorithm book jobs at the low, mid, or enterprise level removes some of the recruiter’s control over meeting their expectation. AFRS recently adjusted its goaling calculations to increase fairness, but goals remain at the recruiter level. Larger studies into the overarching goaling paradigm, such as RAND’s from 2022 [6], should be conducted considering improvements in applicant-to-job booking strategies to increase job matching performance targeting improved talent management described in Robson et al. [8].

## Conclusion

A DO algorithm for matching available United States Air Force jobs to applicants proves to be the most effective solution in achieving the most matches while allowing more applicants to match with jobs higher on their preference list. Loop techniques can achieve similar results in asset-constrained circumstances and be faster to finish when input data is larger. However, their performance in achieving the greatest number of overall matches considering applicant preferences should never beat that of the DO heuristic. More research or studies should be considered to compare the performance of job-to-applicant matching optimization with human-based techniques such as the job draft.

## Github Repo

You can find the latest MILP at the following repository.

<div class="repositories d-flex flex-wrap flex-md-row flex-column justify-content-between align-items-center">
    {% include repository/repo.html repository='lancewilhelm/AFRSOptimizer' %}
</div>

## References

[1] Disciplined convex programming. https://www.cvxpy.org/tutorial/dcp/index.html.

[2] How it works. https://www.nrmp.org/intro-to-the-match/how-matching-algorithm-works/.

[3] National resident matching program. https://www.nrmp.org/, Nov 2022.

[4] A Agnetis. Introduction to matching problems. https://www3.diism.unisi.it/˜agnetis/matchingENGnew.pdf.

[5] Kat Bailey. Afpc adopting innovative officer assignment system it platform, Apr 2019.

[6] Tiffany Berglund, David Schulker, Tracy C. Krueger, and Nelson Lim. Improving the Goal-Setting Process for U.S. Department of the Air Force Recruiters. RAND Corporation, Santa Monica, CA, 2021.

[7] Rebecca Butler. Job distribution optimizer, May 2020.

[8] Sean Robson, Maria C. Lytell, Matthew Walsh, Kimberly Curry Hall, Kirsten M. Keller, Vikram Kilambi, Joshua Snoke, Jonathan W. Welburn, Patrick S. Roberts, Owen Hall, and Louis T. Mariano. U.S. Air Force Enlisted Classification and Reclassification: Potential Improvements Using Machine Learning
