# Different populations agree on which moral arguments underlie which opinions

People often justify their moral opinions by referring to larger moral concerns (e. g., “It is unfair if homosexuals are not allowed to marry!” vs. “Letting homosexuals marry is against our traditions!”). Is there a general agreement about what concerns apply to different moral opinions? We used surveys in the United States and the United Kingdom to measure the perceived applicability of eight concerns (harm, violence, fairness, liberty, authority, ingroup, purity, and governmental overreach) to a wide range of moral opinions. Within countries, argument applicability scores were largely similar whether they were calculated among women or men, among young or old, among liberals or conservatives, or among people with or without higher education. Thus, the applicability of a given moral concern to a specific opinion can be viewed as an objective quality of the opinion, largely independent of the population in which it is measured. Finally, we used similar surveys in Israel and Brazil to establish that this independence of populations also extended to populations in different countries. However, the extent to which this holds across cultures beyond those included in the current study is still an open question.

This repository contains the data and code to reproduce the analyses described on the manuscript "Different populations agree on which moral arguments underlie which opinions".

## About the data (collected by the authors of the manuscript through MTurk, Prolific, and Facebook):

* **id** = Respondents' anonymized identification numbers.

* **age** = Respondents' reported age.

* **sex** = Respondents' reported gender [male/ female].

* **edu** = Respondents' reported level of schooling [>bach/ bach/ grad]

* **polviews** = Respondents' reported political ideology [liberal/ conservative]

* **polviews_10** = Respondents' placement on a political ideology scale ranging from 0 [left] to 10 [right].

* **wordsum** = Scores in the Wordsum test of verbal ability.

* **eval** = Order in which the respondent answered the issue at hand. 

Example: if eval = 1, then the issue at hand was the first one answered by the respondent.   

* **time_took** = Time (in seconds) respondents took to answer to each survey question (i.e., to state their position on the moral issue at hand, and to choose the arguments they believe justify both the pro and the against positions).

* **issue** = Moral issues' codes.

* **type** = Moral issues' positions [pro = agreement with the issue at hand/ against = disagreement with the issue at hand].

Example: consider the issue _"Do you personally think it is wrong for a woman to have an abortion if there is a strong chance of serious defect in the baby?"_ The _"pro"_ position is _"Yes, I think it is wrong..."_, while the _"against"_ position is _"No, I don't think it is wrong..."_.

* **opinion** = Whether the respondent holds the position at hand [1] or not [0].

Example: consider the issue _"Do you personally think it is wrong for a woman to have an abortion if there is a strong chance of serious defect in the baby?"_ and the position _"pro"_(_"Yes, I think it is wrong..."_). If the respondent holds this position, then **opinion** = 1. But, if the respondent does not hold this position, **opinion** = 0.  
* **mf** = Moral concerns [Harm/ Fairness/ Ingroup/ Authority/ Purity/ Liberty/ Violence/ Government].

* **value** = Whether the respondent believes an argument derived from the concern at hand can be used to justify the position at hand [1] or not [0].

Example: consider the issue _"Do you personally think it is wrong for a woman to have an abortion if there is a strong chance of serious defect in the baby?"_, the position _"pro"_(_"Yes, I think it is wrong..."_), and the mf _"Harm"_. If the respondent believes a _"Harm"_ argument can be used to justify this position, then **value** = 1. But, if the respondent does not believe such arguments can be used to justify this position, **value** = 0.

* **gss_code** = Moral issues' codes as they appear in the GSS codebook.

* **bsa_code** = Moral issues' codes as they appear in the BSA codebook.

* **gss_text** = Wording of moral issues as they appear in the GSS.

* **bsa_text** = Wording of moral issues as they appear in the BSA.

* **reverse** = Whether agreeing with the moral issue as it appears in the GSS and agreeing with this issue as it appears in the BSA means supporting the same position [0] or not [1].

Example: Consider the GSS issue _"Consider a person who advocates doing away with elections and letting the military run the country. Suppose he wrote a book advocating doing away with elections and letting the military run the country. Somebody in your community suggests that the book be removed from the public library. Would you favor removing it?"_ and its BSA equivalent _"There are some people whose views are considered extreme by the majority. Consider people who want to overthrow the government by revolution. Do you think such people should be allowed to publish books expressing their views?"_. Agreeing with the GSS version of the issue means the opposite of agreeing with the BSA version of the issue. In other words, the GSS version's _"pro"_ position corresponds to the BSA version's _"against"_ position. Thus, **reverse** = 1.      
