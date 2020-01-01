---
title: "Introduction to Submodularity"
toc: true
toc_label: "Table of Contents"
toc_icon: "atom" # It can be changed: https://fontawesome.com/icons?d=gallery&s=solid&m=free
mathjax: true

header: # ~1280px width
  image: https://live.staticflickr.com/65535/49269033577_b4a4b21547_o.jpg
  teaser: https://live.staticflickr.com/65535/49269033577_b4a4b21547_o.jpg
#  image_description: "A description of the image"
#  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"

#excerpt: "This post should display a **header with an overlay image**, if the theme supports it."
#header:
#  overlay_image: https://live.staticflickr.com/65535/49267013393_75aee9ccf5_o.jpg
#  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
#  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
#  actions:
#    - label: "More Info"
#      url: "https://unsplash.com"

categories:
  - Combinatorial Optimization
permalink: /blog/intro-to-submodularity/

sensor_room:
  - url: https://live.staticflickr.com/65535/49263146168_0b232f7a59_o.jpg
    image_path: https://live.staticflickr.com/65535/49263146168_0b232f7a59_o.jpg
    alt: "3 sensors"
    #title: "Image 1 title caption"
  - url: https://live.staticflickr.com/65535/49263146098_8b6ffd2f86_o.jpg
    image_path: https://live.staticflickr.com/65535/49263146098_8b6ffd2f86_o.jpg
    alt: "5 sensors"
    #title: "Image 2 title caption"

fries_cola:
  - url: https://live.staticflickr.com/65535/49263146253_d103d102b7_o.jpg
    image_path: https://live.staticflickr.com/65535/49263146253_d103d102b7_o.jpg
    alt: "fries and cola"

submodular_area:
  - url: https://live.staticflickr.com/65535/49263611351_a19ef09db5_o.jpg
    image_path: https://live.staticflickr.com/65535/49263611351_a19ef09db5_o.jpg
    alt: "Different domains"

app_sensor_room:
  - url: https://live.staticflickr.com/65535/49263098378_2d8fbce5e9_o.jpg
    image_path: https://live.staticflickr.com/65535/49263098378_2d8fbce5e9_o.jpg
    alt: "sensors placement in a room"

app_sensor_room_entropy:
  - url: https://live.staticflickr.com/65535/49263809467_7321b15953_o.jpg
    image_path: https://live.staticflickr.com/65535/49263809467_7321b15953_o.jpg
    alt: "sensors network design in a room"

excerpt: "Submodularity is the property of a set function, which shows diminishing marginal returns. Usually, optimizing such a set function is an NP-hard problem. However, a plain greedy algorithm can quickly solve such a combinatorial optimization problem. And, it guarantees the solution to be 63% close to the optimal, if the function is submodular."
---
What is this animal called `Submodularity`? Why should we even care if it exists? If you:
- beat or harass `data` daily as a profession, or
- are interested in teaching a machine how to make a decision, or
- are a researcher, and you are working on something which involves choosing a subset of items out of all the possibilities out there,

then I would say let’s understand together the anatomy of this animal.

## Machine Learning
In this section, I will try to motivate the concept of submodularity briefly. The heading `machine learning` is used because submodularity often appears in this domain. Well, machine learning is a vast area of study which mainly deals with various algorithms and statistical models which can be employed by the machines (computer systems) to achieve a specific task. Though in the current article, I will be focussing on only one kind of problem, i.e., `Subset Selection`.

### Subset selection

Here, I will throw some examples of the subset selection problem to ponder on:
- You work for a tech-consultant company. A project is acquired by the company, which requires flow-sensor placement in a water distribution network. The company allocated a budget and asked you to finish the job. Let's say that there are more than 100 pipelines and you can only afford 30 sensors in the allocated budget. The problem is here for you that how will you decide which pipeline to choose for sensor placement.

<details><summary>Water distribution network</summary>
Frame an optimization problem where the objective could be: (a) maximize the estimability of all the flow variables in the water distribution network, or (b) maximize the outbreak detection in the network. The constraint would be that the cost of placing sensors should not exceed the allocated budget.
</details>
{: .notice}

- The director of the institute asked your company to place temperature sensors in their convocation hall. Again in the allocated budget, you can afford only 5 sensors. But there are 20 possible locations. Now, the problem is that how will you choose the locations.

<details><summary>Sensor placement in a room</summary>
Frame an optimization problem where the objective could be: (a) Maximize the information provided by the temperature sensors such that the cost of placing sensors should not exceed the allocated budget.
</details>
{: .notice}

- A camera is monitoring a highway. It becomes infeasible to store the video in the memory after a particular time. Your job is to store the maximum amount of video information in the memory. How will you do it?

<details><summary>Compression problem</summary>
Choose a subset of frames from the video (a stack of frames) such that the video information is maximized.
</details>
{: .notice}

- You are the data analytics guy in the news based startup. You are asked to design an app which will display limited news for their customer. Again, of all the news floating around the world, how will you decide which one to choose for your customer.

<details><summary>Summarization problem</summary>
Choose a subset of news articles from all the news available out there such that it maximizes the overall relevance plus diversity such that a limited number of columns can only be displayed.
</details>
{: .notice}

- You have trained a vast neural network, and it possibly cannot fit in your mobile phone’s memory. But you want to use that model to execute specific applications on your mobile phone. So how will you fit that model on your phone?

<details><summary>Compression problem</summary>
Choose a subset of neurons that can retain the information that the original model has learned such that a limited memory is available.
</details>
{: .notice}

- You have a huge human DNA data, viz., measurement of the gene expressions of an entire chromosome. Now you want to predict from these expressions the association with a particular disease, e.g., Tuberculosis. But you know that the entire genome is not predictive for, but some expressions are actually enough. So your problem is to select the parts of the genome which are associated with the disease. How will you decide?

<details><summary>Variable selection problem</summary>
Choose a subset of gene expressions (coordinate selection) that are predictive of the disease.
</details>
{: .notice}

Okay, now we can understand how these problems can be modelled into a subset selection problem. In such problems, the core idea is to select a subset ($$\in V$$) of items that maximizes a particular objective function, such that a given constraint is satisfied, where $$V$$ is the set of all possible items.

The difficulty associated with this problem is that it is combinatorially explosive because the domain is discrete.

**E.g.**, If you have only $$100$$ items, then there are $$2^{100}$$ possible subsets of items. Let’s feel how large this number is.
You take a regular writing paper, which would be probably $$0.1$$ mm thick. Now fold it $$100$$ times. Now, if you would have sat on that paper, then you would reach a distance of $$13.4$$ billion light-years and would have probably discovered a new galaxy, without the help of ISRO, NASA, or SpaceX. The nearest known galaxy is only $$13.3$$ billion light-years from Earth.
{: .notice--primary}

Now we can understand how difficult combinatorial optimization problems can become. 

However, we can reduce the complexity by exploiting some structure of the function. I mean to say that instead of going billions of light-years far, we could work out, say in a meter. There, submodularity comes for the rescue.

[BackTo Top](#){: .btn .btn--inverse .btn--small}{: .align-right}
## Submodularity
To understand submodularity, let us take an example of the placement of the traffic sensors. $$V=$$ set of all possible locations. $$S \in V$$. Let, $$F(S)=$$ information gained from placing sensors at location $$S$$ $$(F: 2^V \to \mathbb{R})$$.

### Definition-1
Before we define submodularity, let us know about **marginal gain**.
Marginal gain $$F(s|A) = F(A \cup \{s\}) - F(A)$$, is the increase in the function value $$F$$ after adding an element (sensor in this case) $$s$$ to the existing set $$A$$.

Now let us see how this marginal gain is changed as we place more sensors.
{% include gallery id="sensor_room" caption="Example of sensor placement in a room" %}
In the left figure, sensors $$1$$ and $$2$$ are already placed. Now, if we place sensor $$s$$ then clearly, we can see that we will gain more information as it will sense more temperature in the area.
In the right figure, sensors $$1, 2,3,$$ and $$4$$ are already placed. Now, if we add sensor $$s$$, then we can see that the gain in information is not much as four sensors are already covering much of the area by the existing sensors.
Therefore the marginal gain in the function $$F$$ by adding sensor $$s$$ is not much in the right figure than in the left figure.

This idea is essentially the principle of submodularity.
The function $$F$$ is submodular, if\\
$$
\begin{align*}
&F(A \cup \{s\}) - F(A) \geq F(B \cup \{s\}) - F(B)\\
&\textrm{where}~~ A \subseteq B \subseteq V, ~~ \textrm{and}~~ s \in V \setminus B
\end{align*}
$$
{: .notice--success}

>The idea is simple and intuitive. The larger the set of items you would have, the lesser the marginal gain you would get. The more you feel that you know everything, the less you gain by knowing a piece of new information. (Therefore people say stay hungry and stay foolish <i class="far fa-grin-beam"></i>)
This concept is also known as **diminishing marginal returns**.

Let me present an example from economics:

There is a concept in economics known as **economy of sale**, which says the more you buy the less the expense of each item on average. All these junk food outlets use this concept very wisely <i class="far fa-grin-tongue"></i>. 
{% include gallery id="fries_cola" caption="Combo offer" %}
They will have this offer that if you buy a combo pack consisting of a french fry and a cold drink (full of ice cubes with a tinge of soft drink to be precise), you will get a refill free of cost. Most of the people go for the offer, and the company benefits a lot in the process.

### Definition-2
There exists an alternative definition of the submodular function.

The function $$F$$ is submodular, if\\
$$
\begin{align*}
&F(A) + F(B) \geq F(A \cup B) + F(A \cap B)\\
&\textrm{where}~~ A, B \subseteq V
\end{align*}
$$
{: .notice--success}

### Equivalence of definition-1 and 2
Now, let us understand how both these definitions are equivalent.

**Proof: definition-1 => definition-2**\\
Let us assume that the definition-1 holds. Now, we will prove that definition-2 holds. Consider two sets $$P, Q \subseteq V$$. Let $$m$$ be the number of elements in $$Q \setminus P=\{q_1, q_2,...,q_m\}$$. Also let $$Q_i=\{q_j: 1 \leq j \leq i\}$$. Now, we can write:\\
$$
\begin{align*}
Q &= (P \cap Q) \cup (Q \setminus P)\\
& = (P \cap Q) \cup q_1 \cup q_2 ... \cup q_m\\
& = (P \cap Q) \cup (Q_1 \setminus Q_0) \cup (Q_2 \setminus Q_1) ... \cup (Q_m \setminus Q_{m-1})
\end{align*}
$$
\\
where $$Q_0= \emptyset$$. Since each of the terms in the RHS of the above equation is disjoint, we can write:\\
$$
\begin{align*}
F(Q) & = F(P \cap Q) + F(Q_1 \setminus Q_0) + F(Q_2 \setminus Q_1) ... + F(Q_m \setminus Q_{m-1})\\
& = F(P \cap Q) + F(Q_1) - F(Q_0) + F(Q_2) - F(Q_1) ... \\& + F(Q_m) - F(Q_{m-1})\\
\end{align*}
$$
\\
Now,\\
$$
\begin{align*}
F(Q) - F(P \cap Q) & = F(P \cap Q) + F(Q_1) - F(Q_0) + F(Q_2) - F(Q_1) ... \\
& + F(Q_m) - F(Q_{m-1}) - F(P \cap Q)\\
& = F(P \cap Q) + \sum_{i=1}^{m} (F(Q_i) - F(Q_{i-1})) - F(P \cap Q)\\
& = \sum_{i=1}^{m} (F(P \cap Q) + F(Q_i)) - (F(P \cap Q) + F(Q_{i-1}))\\
& = \sum_{i=1}^{m} F((P \cap Q) \cup Q_i) - F((P \cap Q) \cup Q_{i-1})\\
& = \sum_{i=1}^{m} F((P \cap Q) \cup Q_{i-1} \cup \{q_i\}) - F((P \cap Q) \cup Q_{i-1})
\end{align*}
$$
\\
Since $$(P \cap Q) \cup Q_{i-1} \subseteq (P \cup Q_{i-1})$$ holds, using defnition-1 we can write:
$$
\begin{align*}
F(Q) - F(P \cap Q) & \geq \sum_{i=1}^{m} F((P \cup Q_{i-1}) \cup \{q_i\}) - F(P \cup Q_{i-1})\\
& = \sum_{i=1}^{m} F(P \cup Q_{i}) - F(P \cup Q_{i-1})
\end{align*}
$$
\\
After expanding and cancelling the terms in the RHS of the above equation,
$$
\begin{align*}
F(Q) - F(P \cap Q) & \geq F(P \cup Q_m) - F(P)\\
\textrm{where}~~ Q_m = Q \setminus P\\
\textrm{therefore,}~~ P \cup Q_m = P \cup Q\\
\textrm{Now,}~~ F(Q) - F(P \cap Q) & \geq F(P \cup Q) - F(P)
\end{align*}
$$
{: .notice}

**Proof: definition-2 => definition-1**
Let us assume that the definition-2 holds. Now, we will prove that definition-1 holds. Consider the set $$A \cup \{s\}$$ and B. Now, using definition-1, we can write:\\
$$
\begin{align*}
F(A \cup \{s\}) + F(B) \geq F(A \cup \{s\} \cup B) + F((A \cup \{s\}) \cap B)
\end{align*}
$$
\\
Now, if $$A \subseteq B \subseteq V$$, then $$F(A \cup \{s\} \cup B)=F(\{s\} \cup B)$$, and if $$\{s\}=V \setminus B$$, then $$F((A \cup \{s\}) \cap B)=F(A)$$. Therefore,\\
$$
\begin{align*}
F(A \cup \{s\}) + F(B) & \geq F(\{s\} \cup B) + F(A)\\
F(A \cup \{s\}) - F(A) & \geq F(B \cup \{s\}) - F(B)
\end{align*}
$$
{: .notice}

### Application of submodular functions
Submodular functions are almost everywhere.
{% include gallery id="submodular_area" caption="Different domains where submodular functions show up" %}

## Illustrative Examples of Submodular Functions
- **Sensor placement in a room**:
{% include gallery id="app_sensor_room" caption="Sensor placement in a room" %}

Here, the coverage function $$F(S) = \mid \cup_{u \in S}~~ area(u) \mid$$. Each sensor covers a disk-like area. We can see that the marginal gain in the area covered by the sensors in left figure is more than the right figure. Therefore coverage function $$F(S)$$ is a submodular function. The coverage problem is yet a crude way of solving this problem. Now let us see a different way of solving this problem.

{% include gallery id="app_sensor_room_entropy" caption="Sensor network design" %}
Blue circles are the place where you can install a temperature sensor. $$Y$$’s are the actual temperature value, which we can never observe, as $$X = Y + noise$$. $$X$$ is the temperature measurement which we will get if we place a sensor at $$Y$$. Here the modelled function is $$F(A) =$$ Uncertainty about temperature before placing sensor to measure it $$–$$ Uncertainty about temperature after placing sensors in $$A$$. Therefore,
$$
\begin{align*}
F(A) & = H(Y) – H(Y \mid X_A)\\
& = I(Y;X_A)
\end{align*}
$$\\
where $$I(Y;X_A)$$ is also known as **mutual information** between what we have selected for sensor placement and the unsensed locations. $$F(A)$$ is the decrease in the uncertainty, which can be maximized. Here, both mutual information $$I$$, and **discrete entropy** $$H$$ are submodular functions.

- **Recommendation problem**:
The recommendation problem is one of the areas where submodular functions are widely seen.
> What products will you recommend to a person to buy?

**E.g.**, Consider a lady entered a mall and bought a Pixel 3 mobile phone, a sweater, and a newborn related magazine. Consider yourself the ad manager whose job is to send recommendations to people about their products over SMS or email. You will definitely not recommend the lady an iPhone because she already bought a phone recently. iPhone will not increase the marginal gain in the value of relevant products she is having. Maybe you want to have some diversity in the products you are recommending. However, having the information that the lady bought a newborn related magazine, you can guess that the lady might be pregnant. Now you can recommend her post-pregnancy or baby-related products like diapers, baby oil, etc. These products will increase the marginal gain in the value of relevant products she is having.
{: .notice}

## Optimization of Submodular Functions
Now that we have seen the structure of submodular functions, we will try to optimize it.

The optimization problem can be written as:\\
$$
\begin{align*}
\max_{S \in V} F(S)\\
\textrm{such that,}~~ \mid S \mid \leq k
\end{align*}
$$
{: .notice--success}
where $$F(S)$$ is the submodular function which needs to be maximized and $$k$$ is the upper limit on total elements in set $$S$$. We can clearly observe that if we don’t put the constraint, then the optimal solution would be the whole solution space $$V$$, which is too easy. But there will always be a constraint. Unfortunately, such combinatorial optimization problems are **NP-hard**, which means that finding an algorithm to solve such problems in polynomial-time is highly unlikely.

The next easiest solution to such problem is the **greedy algorithm**.

*Pseudo code for greedy algorithm*:\\
$$
\begin{align*}
& S_0 = \emptyset\\
& \textrm{for}~ i = 0,...,k-1\\
& ~~~~~~ z^{*} = \arg \max_{z \in V \setminus S_i}~ F(S_i \cup \{z\})\\
& ~~~~~~ S_{i+1} = S_i \cup \{z^{*}\}
\end{align*}
$$
{: .notice--success}
> The core idea of the greedy algorithm is very simple. It starts with an empty set, and it finds the next item for the set, which is the best. It means that the chosen item maximizes the function $$F(S)$$. Similarly, it finds the rest of the items until it hits the constraint.

Now you can see that the greedy algorithm is pretty straight forward and easy. But the question is that does the greedy algorithm works. I mean, whether it generates the optimal solution or even come close to it.

**Theorem ([`Nemhauser-Wolsey-Fisher, 1978`](https://doi.org/doi:10.1007/BF01588971))**: If $$F(S)$$ is monotone submodular, then greedy algorithm is guaranteed to achieve at least $$63 \%$$ of optimum.\\
$$
\begin{align*}
F(S_k) \geq (1-\frac{1}{e})F(S^{*})
\end{align*}
$$
{: .notice--success}
This theorem is lovely. The function evaluation takes place $$k(k+1)/2-1$$ times.
Remember, in the earlier [section](#subset-selection), where I presented an example where if you have $$100$$ items, then you would probably discover a new galaxy which is billions of light-years far from Earth. Now, if you employ the greedy algorithm, you will barely cross a half meter ($$0.5049$$ m to be precise), and you will land at a pretty good solution. Now you can realize the power of the submodular function.

**Note** that the greedy algorithm can fail if the function is not monotone submodular. In that case, there is no guarantee that the optimality gap will reduce at each iteration.
{: .notice--warning}

Following is a list of some variants of the greedy algorithm for submodular functions:
- `A note on maximizing a submodular set function subject to a knapsack constraint`, [`Sviridenko, 2004`](https://doi.org/10.1016/S0167-6377(03)00062-2)
- `Cost-effective outbreak detection in networks`, [`Leskovec-Krause-Guestrin-Faloutsos-VanBriesen-Glance, 2007`](https://doi.org/10.1145/1281192.1281239)
- `Maximizing a Monotone Submodular Function Subject to a Matroid Constraint`, [`Calinescu-Chekuri-Pal-Vondrak, 2011`](https://doi.org/10.1137/080733991)
- `Fast algorithms for maximizing submodular functions`, [`Badanidiyuru-Vondrak, 2013`](https://dlnext.acm.org/doi/abs/10.5555/2634074.2634184)

## Takeaway
1. Subset selection problems appear very frequently in machine learning.
2. They are combinatorial optimization problems, which are NP-hard.
3. Submodular function shows a diminishing marginal returns property.
4. If the objective function of the problem is a monotone submodular, then the greedy algorithm guarantees a solution that is at least $$63 \%$$ close to the optimal.
5. Greedy algorithm is pretty fast, and it can be easily parallelized for large scale problems.

[BackTo Top](#){: .btn .btn--inverse .btn--small}{: .align-right}
