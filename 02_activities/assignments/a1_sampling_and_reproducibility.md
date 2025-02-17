# ASSIGNMENT: Sampling and Reproducibility in Python

Read the blog post [Contact tracing can give a biased sample of COVID-19 cases](https://andrewwhitby.com/2020/11/24/contact-tracing-biased/) by Andrew Whitby to understand the context and motivation behind the simulation model we will be examining.

Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

Modify the number of repetitions in the simulation to 100 (from the original 1000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.

Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

# Author: YOUR NAME

####Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

Based on the article and after running whitby_covid_tracing.py, it is evident that the scenario resembles stratified sampling. The population is divided into two strata: wedding attendees and brunch attendees. Infected individuals are proportionally distributed within each stratum based on a fixed infection rate of 10%.

1. Initial Sampling for Infection
Procedure: The model randomly selects a subset of people to be infected.
The sampling frame consists of 1,000 people: 800 brunch attendees and 200 wedding attendees. Given an infection attack rate of 10%, the expected number of infected individuals is 100.
Function used: np.random.choice()
Code:
infected_indices = np.random.choice(ppl.index, size=int(len(ppl) * ATTACK_RATE), replace=False)
ppl.loc[infected_indices, 'infected'] = True
This code randomly selects 10% of individuals (100 people) from the population and marks them as infected (infected = True) in the dataset.

2. Primary Contact Tracing
Procedure: Among infected individuals, a subset is traced based on a fixed probability 20%.
Once infection has been assigned, the model determines which infected individuals are successfully traced. This is done randomly based on a probability of trace success (20%).
Function used  np.random.rand(sum(ppl['infected'])) < TRACE_SUCCESS
From the 100 infected individuals, 20% (20 cases) are randomly chosen as successfully traced.

3. Secondary Contact Tracing
Procedure: If at least SECONDARY_TRACE_THRESHOLD = 2 infections from the same event are traced, all infected individuals from that event are marked as traced.
The model expands contact tracing by identifying events with a high number of traced attendees and marking additional infected individuals as traced.
Function usedevent_trace_counts[event_trace_counts >= SECONDARY_TRACE_THRESHOLD].index
If at least two traced individuals attended a particular event, authorities attempt to test all attendees at that event, potentially increasing the number of traced infections.
How This Relates to the Blog Post

1.	Selection Bias in Tracing: The blog post discusses selection bias in contact tracing, noting that certain weddings are easier to trace than brunches. The Python model replicates this bias by making large gatherings more likely to be traced once the initial trace count exceeds the secondary tracing threshold.

2.	Lack of Randomness in Secondary Tracing: The model does not apply pure randomness in secondary contact tracing. Instead, it follows a biased rule: once at least two traced individuals are found at an event, further tracing is triggered. This reflects real-world biases, where certain settings are more analyzed once a threshold is met, leading to a higher apparent infection rate.


###Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

The article presents a simple model that illustrates the distribution of infection proportions, showing that, on average, around 10% of attendees will get infected. According to the graph, when adding some randomness, each person has a 10% chance of getting infected. The graph on true proportions confirms this, showing that infections from weddings account for approximately 20%, with some variance across many simulations (similar to the outbreak setting table, which shows that 20% of infections come from weddings).
Additionally, the article highlights issues with contact tracing in determining the source of infections. With primary contact tracing, only 20% of infections are correctly traced to an event. However, when adding the next step, secondary contact tracing, if two people are traced to the same event, authorities conduct a thorough investigation, identifying all infections at that event. This process overestimates the proportion of infections from weddings, making them appear to cause more infections, while brunches seem safer than they actually are. The graph in the article illustrates this, showing that observed proportions are more spread out and overestimated.
Comparing these results to those from whitby_covid_tracing.py, we observe different findings. The proportions of wedding infections and wedding traces are very similar, with the distribution simulated over 1,000 trials. The results show that infections from weddings still center around 20%, with some variation for both wedding infections and wedding traces. It is also important to mention that a lower number of infections traced to weddings is found to be between 0% and 5%. However, this pattern does not appear when using the model’s predicted infections from weddings.


####Modify the number of repetitions in the simulation to 100 (from the original 1000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.

results = [simulate_event(m) for m in range(100)]
props_df = pd.DataFrame(results, columns=["Infections", "Traces"])

After modifying the code, in terms of reproducibility, the values attributed to infections and traces do not vary much. The variations are minor, and each time the graph is reproduced, it consistently shows the same patterns, with the graph looking very similar every time.



###Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

np.random.seed(123)
results = [simulate_event(m) for m in range(100)]
props_df = pd.DataFrame(results, columns=["Infections", "Traces"])

I added the following seed to the code: np.random.seed(123). This was inserted before running the simulation 100 times. As a result, the graphs are always the same, and the values for infections and traces remain unchanged.

## Criteria

|Criteria|Complete|Incomplete|
|--------|----|----|
|Altercation of the code|The code changes made, made it reproducible.|The code is still not reproducible.|
|Description of changes|The author explained the reasonings for the changes made well.|The author did not explain the reasonings for the changes made well.|

## Submission Information

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

### Submission Parameters:
* Submission Due Date: `23:59 - 16/02/2025`
* The branch name for your repo should be: `assignment-1`
* What to submit for this assignment:
    * This markdown file (a1_sampling_and_reproducibility.md) should be populated.
    * The `whitby_covid_tracing.py` should be changed.
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sampling/pull/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `assignment-1`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via the help channel in Slack. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.
