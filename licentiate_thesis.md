# Transfer Learning using low-dimensional Representations in Reinforcement Learning

## Introduction

### Motivation

Machine Learning (ML) has in the recent decade made breakthroughs in several areas \cite{imagenet,atari,alphago,more?}. A common factor of the successes has been training on large amounts of data, and access to compute power not available before. For example, one of the first image classifiers to reach human level performance had access to 1.2 million labelled images \cite{imagenet}. As a rough estimate, if taking each photo and labelling it takes 1 minute, then the total amount needed just to create the dataset would be over 2 years.

From a human perspective, we are also exposed to extensive amounts of data through our lifetime, albeit mostly unlabelled and without any specified objective. In contrast to computers, we are able to reuse knowledge acquired in other contexts, to learn new things more efficiently. To learn how to ride a bike, we build upon our motor skills that we developed while, for example, learning to walk. As life progresses we can learn many tasks faster by generalizing from what we have already learned. Sometimes, we can even learn from observing a single demonstration and then being able to perform that behavior successfully.

Learning a behavior that requires a sequence of actions, possibly infinitely many, is called Sequential Decision Making. When the environment and/or objective function is unknown and only observed during interaction, we are in the domain of Reinforcement Learning (RL) \cite{suttonbarto}. In this thesis, I will tackle the problem of generalizing from already learned problems, to new similar ones, without starting the learning process from scratch. I will restrict my attention to RL, and in particular, I will consider how we can find a low-dimensional probability distribution over related learning problems. Once faced with a new learning problem, we can search under this low-dimensional distribution rather than the entire parameter space of for example a large neural network.

### Thesis contributions

This thesis consists of three papers \cite{} that tackle the problem of transfer learning and RL. A more thorough background along with necessary definitions is postponed until section \todo{}.

**Paper A:**
**Non-prehensile Rearrangement Planning with Learned Manipulation States and Actions**
J. A. Haustein$^\dagger$, **I. Arnekvist**$^\dagger$, J. A. Stork, K. Hang, D. Kragic. In *"Workshop on Machine Learning in Robot Motion Planning", IEEE International Conference on Intelligent Robots and Systems (IROS)*, Madrid, 2018

This paper discusses how to learn pushing a single object in an obstacle-free environment. We then propose how to generalize this skill to solve a set of non-prehensile rearrangement problems.

**Paper B:**
**VPE: Variational Policy Embedding for Transfer Reinforcement Learning** 
**I. Arnekvist**, D. Kragic and J. A. Stork. In *2019 International Conference on Robotics And Automation (ICRA)*, 2019, pp. 36-42.

Some tasks can naturally be considered related and only differ with respect to a few parameters. For example, the tasks of throwing a heavy object vs. a light object with the same shape are different only with respect to a single number - the mass of the object. In this paper, we propose a formulation to find a low-dimensional parameterization of such tasks in an unsupervised manner, and how we can use this to learn behaviors faster.

**Paper C:**
**The effect of Target Normalization and Momentum on Dying ReLU**
**I. Arnekvist**, J. F. Carvalho, D. Kragic, J. A. Stork. *In submission.*

Many of the success stories from the recent decade relies on the use of deep neural networks and regression with these models. The performance of trained models in turn rely on mean and variance of observations being known a priori. For RL problems, these statistics are in general not known in advance. To be able to solve harder problems with the aforementioned method, VPE, we need to reliably perform regression with deep neural networks. This paper investigates the relationship between the mean and variance of target variables, momentum optimization and their influence on Dying ReLU - a problem causing deteriorating capacity of neural networks.

## Background

### Transfer Learning

Transfer Learning (TL) is defined by first identifying two components of a learning problem. The first one is a domain $D = \{\mathcal{X},P(X)\}$ where $\mathcal{X}$ is a space and $X = (\mathbf x_1, ..., \mathbf x_n)$ is a set of observations $\mathbf x_i \in \mathcal X$ distributed according to $P(X)$. For example, we can define as source domain $D_S$ to be images generated by a computer program. Having abundant access to images in this domain, we could learn something about a target domain $D_T$, for example images from the real world. The space $\mathcal X$ in this example might be the same for source and target, but most likely the distribution $P(X)$ is different.

The second component is denoted a task $\mathcal T = \{\mathcal Y, f(\cdot)\}$ where $\mathcal Y$ is a (label) space and $f : \mathcal X \mapsto \mathcal Y$. Like for the domain, we can consider a source task $\mathcal T_S$, with an example being binary classification of images, deciding whether it depicts a cat or a dog. The space is then $\mathcal Y_S = \{\mathrm{cat, dog}\}$ and the function $f : \mathcal X_S \mapsto \mathcal Y_S$ takes an image $\mathbf x \in \mathcal X_S$ and maps it to either cat or dog. A target task $\mathcal T_T$ could then instead be discriminating between ten species of animals. Even though $\mathcal Y$ is denoted a label space in TL literature, implying that TL is for classification, we can still extend to other problems like RL \cite{trl_survey}.

TL is finally defined as methods to improve the learning of a target function $f_T(\cdot)$ for a target domain $D_T$ and target task $\mathcal T_T$ by using knowledge from one or more source domains $D_S$ and tasks $\mathcal T_S$. Note that the metric for improvement is not defined, as this can vary depending on what we want to achieve. For example in RL, let us say we want to maximize the return with as few trials as possible. In this case the *area under the curve* (AUC) could be such a metric. As already mentioned briefly, we denote the tuple $(D, \mathcal T)$ a learning problem. We will shortly discuss how RL fits into this description.

#### Meta Learning

TL can be acheived in various ways, for example pre-training a convolutional neural network on a source learning problem. Given a target task, the first layers of the network are then kept fixed while only adjusting the final fully connected layers \cite{}. This method is motivated by the insight that the initial layers of a neural network can be task independent, e.g., edge detection. Note that this is a conscious choice based on the experience of deep learning researchers and practitioners. Instead of manually engineering choices of this kind, we could also learn them.

Such a way of achieving TL is to learn the learning algorithm - *Meta Learning* \cite{maml,reptile,howtotrain}. Note that Meta Learning is necessarily a subset of TL due to the *no free lunch theorem* \cite{nfl,nflml}. An algorithm can only be better on a target learning problem by having a suitable inductive bias. If such an inductive bias is learned from source learning problems to improve learning of a target function $f_T(\cdot)$ then it is TL by definition.

Mention some methods like MAML?

### Reinforcement Learning

RL aims to learn an optimal behavior by interacting and performing trial and error in an unknown environment. The core concept describing such an environment is that of a Markov Decision Process (MDP). An MDP is defined by the following parts. (1) A state space $\mathcal S$ consisting of the states that the environment can assume. (2) An action space $\mathcal A$ with the set of possible actions an agent can perform in the environment. (3) A transition probability density $p(s_{t+1}|s_t, a_t)$ specifying the probability density of different successor states $s_{t+1} \in \mathcal S$ given a state $s_t \in \mathcal S$ and action $a_t \in \mathcal A$ performed at time $t$. (4) A probability density $p(r_t|s_t, a_t)$ over rewards $r_t \in \mathbb R$.

The interaction with the environment is considered to be performed by an agent with the aim of finding either a deterministic policy $\pi : \mathcal S \mapsto \mathcal A$, or a probabilistic policy $\pi(a_t|s_t) = p(a_t|s_t)$ such that

$$\pi^* = \text{argmax}_\pi \mathbb{E}[\sum_\limits{t=0}^\infty \gamma^t r_t]$$

where the expectation is taken over all trajectories $\{s_t, a_t, r_t\}_{t=0}^\infty$. The infinite, discounted, sum of rewards is called the return. The MDP can also be defined in terms of probability mass functions, allowing for discrete states, actions, and rewards.

#### Bandit

A key difficulty in solving MDPs is that a policy might have to take multiple actions that result in short term losses in order to maximize rewards in the long term. This implies that a greedy policy that only maximizes the immediate reward will often be sub-optimal. A special case of the MDP is a *bandit* where the MDP will enter a *terminal* state immediately after the first action taken. The MDP will then never leave the terminal state, emitting $r_{t>0} = 0$ for all future time steps. In this case, the optimal policy will always be a greedy policy and not have the trade-off between short and long term rewards. Finding an optimal policy will in general be easier for a bandit.

#### Q-functions

There exists a function that can allow the policy to be greedy, and yet optimal, even in the general MDP setting. This is called a $Q$-function, and is defined

$$Q(s_t, a_t) = \mathbb{E}[\sum_\limits{h=0}^\infty \gamma^t r_{t+h}|s_t, a_t, \pi]$$.

This is the expected return from state $s_t$, when performing action $a_t$, and then following the policy $\pi$. The $Q$-function given any optimal policy is called an optimal $Q$-function. The optimal $Q$-function is unique for every MDP, even if there exists many optimal policies. Given an optimal $Q$-function, all optimal policies can be recovered, for example for any state $s_t$ all optimal actions are given by $\{a^*_t : Q(s_t, a^*_t) = \max\limits_{a_t}Q(s_t, a_t)\}$. This makes $Q$-functions a powerful tool since they allow for greedy optimization of a policy, yet encodes _all_ optimal policies (in the case of an optimal $Q$). This will, as we will see later, allow us to learn from a _set_ of optimal policies, even if those policies produce different actions for the same state.

#### Transfer Reinforcement Learning (TRL)

Literature describing transfer learning for RL (TRL) is often not described in the same way as transfer learning. In TRL, the transfer is said to happen from a source task(s) to a target task \cite{trl_survey,maml,reptile}, while according to the TL definitions this would imply that domain transfer is irrelevant for RL. This is of course not a restriction made in TRL as plenty of papers deal with domain transfer \cite{domain_randomization,rika,rubiks,vpe}.

To define TRL we start by defining the task part $\tau = \{\mathcal Y, f(\cdot)\}$. The objective for RL is to find a policy that maximizes the return, so we could consider $f$ to be an optimal policy that we want to approximate. The problem with this is that $f$ might not be a unique optimal policy. Another problem is that an optimal policy does not naturally characterize the task to the same extent as the reward or return distribution. I therefore propose the label space $\mathcal Y = \mathbb R$ to be the return, $X = \{s_t, a_t\}_{t=0}^\infty$ is a state-action trajectory, and hence $\mathcal X = \lim\limits_{n\rightarrow\infty}(\mathcal S \times \mathcal A)^n$. The task function is then defined as

$f(X) = \sum_\limits{t=0}^\infty \gamma^t r_t$

where

$r_t = \int\limits_{-\infty}^\infty p(r_t|s_t, a_t) r_t dr_t = \mathbb E_{r_t}\left[r_t | s_t, a_t\right]$.

That is, the aim of RL given task $\tau$ is to find a policy that maximizes the expected return $\mathbb E_X\left[f(X)\right]$. This requires a distribution over $X$, which naturally leads us to the remaining definition of the domain $\mathcal D$. We have already defined the space $\mathcal X$ but not the distribution $P(X)$. This distribution is necessarily conditioned on the policy $\pi$ as

$P(X|\pi) = P(\{s_t, a_t\}_{t=0}^\infty|\pi) = P(s_0)\prod\limits_{t=1}^{\infty}P(s_{t+1}|s_t, a_t)P(a_t|s_t, \pi)$

where we have used the Markov property of MDPs to factorize the distribution. From this definition we can now clearly distinguish between task adaptation and domain adaptation. Task adaptation is where $f$ changes, e.g., the goal is changed. Domain adaption typically deals with changes in $P(s_{t+1}|s_t, a_t)$, e.g., the physics or observations are different in the target domain. Note that domain adaptation could also be that $\mathcal X_S \neq \mathcal X_T$, for example learning in a system where we have access to detailed measurements of the state while deploying in an environment where we only have access to a video feed \cite{todo}.

Note that the MDP fully characterizes our learning problem. We will now assume there exists some parameter $z$ that can change the properties of the MDP. More specifically, the state transition and reward distribution is conditioned on a particular choice of $z$ as $P(s_{t+1}|s_t, a_t, z)$ and $P(r_t|s_t, a_t, z)$. For now, we can assume we have access to a policy $\pi(s_t, z)$ that can change to a suitable behavior given different values of $z$. We now ask how we can find a suitable $z$ given a new MDP and what properties that are suitable for such a parameterization.

### Search algorithms

Given a parameterization $z$, the objective changes from (todo) to

$$z^* = \text{argmax}_z \mathbb{E}[\sum_\limits{t=0}^\infty \gamma^t r_t|\pi(\cdot, z)]$$.

This is a non-differentiable loss function since the trajectory and reward distributions are unknown and we only have access to samples. We can approach this in two different ways: (1) If we have observations from the MDP and ground truth $z$, we can learn to predict $z$ given new observations. (2) Use black-box optimization algorithms. I will now go through the latter and leave the former for the discussion of future work.

#### Bayesian optimization

#### Simulated annealing (genetic algorithms?)

#### Natural evolution strategies

Conclusion: A low-dimensional $z$ is crucial for the efficiency of the search.

## Summary of Included Papers

### Paper A

**Non-prehensile Rearrangement Planning with Learned Manipulation States and Actions**
J. A. Haustein$^\dagger$, **I. Arnekvist**$^\dagger$, J. A. Stork, K. Hang, D. Kragic. In *"Workshop on Machine Learning in Robot Motion Planning", IEEE International Conference on Intelligent Robots and Systems (IROS)*, Madrid, 2018

#### Summary

In this paper we propose how to learn and integrate a policy and a generative model into a sampling-based motion planning algorithm. The ultimate goal of the algorithm is to rearrange a number of objects into a user-specified configuration. The number of objects can vary, and also the number of fixed and movable obstacles in the scene. See for example \figref{the_thing_below_todo}.



<img src="figures/abc.png" alt="abc" style="zoom: 50%;" /><img src="figures/dual_slalom.png" alt="dual_slalom" style="zoom: 50%;" />

We integrate these parts in steps, starting with the learning of the policy. The policy is trained to push a single object in an obstacle-free environment by minimization of the expected distance $J$ to a goal. The policy is only allowed one action, making it a simpler-to-solve bandit problem rather than the full RL-problem. The outcome will, however, be largely dependent on where the robot is placed relative to the object. By having access to $J$ as a function of the initial robot state $J(s_r)$, we can sample robot states according to $p(s_r) \propto e^{-\lambda J(s_r)}$ with a temperature-parameter $\lambda$ using MCMC. This distribution has its modes where $J$ is minimized, making it more likely to sample good robot states, while still allowing for exploration. This exploration is crucial for the full rearrangement problem since then robot states might be blocked by obstacles. We further propose how to efficiently sample these states using a generative adversarial network (GAN) instead of inefficient MCMC-sampling.

As a sample-based motion planner attempts to find a solution, it successively builds a tree where each node is the joint state of the robot and the objects in the scene. We argue that the planning of collision-free movement for a robot is easier than purposefully interacting with objects. For this reason, we consider explored states in slices. For each slice, object states are the same but the robot can vary. Slicing allows us to build smaller and more efficient search trees where the nodes are slices instead of full states.

We evaluated our approach on a series of rearrangement problems, comparing it to a similar state-of-the-art algorithm \cite{}. Our results indicate that learned policies and generative models can lower the number of iterations needed to find a solution and lessen the need of manually engineered pushing and sampling strategies. The slicing of the states also resulted in finding solutions faster, both in terms of time and number of iterations of the planner.

#### Contributions

- We propose and demonstrate that the integration of a learned policy and generator into a planning framework result in fewer iterations needed to find a solution.
- We demonstrate that a robot-independent slicing of the state space leads to faster runtimes.

#### Contributions by the author

- Formulated the objective to train the policy, performed training, and integrated the policy into the planning algorithm.
- Proposed the definition of the robot state distribution by using the policy objective.
- Implemented MCMC sampling from the definition of the robot state distribution and proposed and trained a GAN architecture to learn the distribution from these samples. Implemented the state sampling into the planning algorithm.

### Paper B

**VPE: Variational Policy Embedding for Transfer Reinforcement Learning** 
**I. Arnekvist**, D. Kragic and J. A. Stork. In *2019 International Conference on Robotics and Automation (ICRA)*, 2019, pp. 36-42.

#### Summary

Many MDPs in RL can be seen as related by just varying one or a few parameters. Pushing a box towards a goal could for example vary by the mass of the object. In addition to this the reward function could be interpolated between accomplishing the task fast vs. minimizing the torques applied. If the MDP is only specified by these two dimensions, as an example, then in principle it should be sufficient to parameterize optimal policies by only these two parameters. This allows fine-tuning of policies in just a few dimensions rather than updating the entire parameter vector of the policy.

In this work, we propose to optimize the evidence lower bound (ELBO) to jointly learn Q-functions over MDP/policy pairs, and an approximate posterior over low-dimensional MDP parameterizations that is unobserved. The single learned "master" Q-function then encompasses all the observed MDPs by specifying the corresponding learned parameterization, $z$ , as an additional argument. This master Q-function can now provide deterministic policy gradients for any $z$, allowing us to learn a single master policy that switches behavior depending on the value of $z$. Given an unseen test-MDP, we can perform optimization only in the low-dimensional space of $z$, rather than the policy's full parameter space. The process is visualized if \figref{below}.

<img src="figures/vae_overview.png" style="zoom: 50%;" />

We evaluate this method on a pendulum swing-up problem to see that we can generalize to new MDP instances. The unobserved low-dimensional space consists of the pendulum length and the relative cost to actuate the joint. We illustrate the learned posterior over $z$ to qualitatively assess how well the underlying parameterization was learned. We also evaluated the method for transferring policies learned in simulation on a pushing task, to a similar task on the real robot. This showed that we can successfully learn a policy in a new, but related, MDP in a handful of trials.

#### Contributions

- Propose how to use the evidence lower bound to learn a low-dimensional parameterization over MDP and policy pairs.
- Show that this method can learn a sensible posterior parameterization, and
- ...that we can use this parameterization to adapt policies to new MDPs in a handful of trials.

#### Contributions by the author

The author contributed the majority in all of the above.

### Paper C

**The effect of Target Normalization and Momentum on Dying ReLU**
**I. Arnekvist**, J. F. Carvalho, D. Kragic, J. A. Stork. *In submission.*

#### Summary

A common practise in neural network (NN) regression is to normalize target values to have zero mean and unit variance. Although this practise is well-researched for the inputs of a neural network, the targets have received far less attention. This practise is not well-researched both in terms of the practical implications, and the underlying mechanisms.

In RL, this is relevant from two perspectives. First, the statistics such as mean and variance of target values such as the rewards and returns are not known a priori and also change with the performance of the policy. Second, regression performance is crucial for the performance of RL-algorithms and particularly for those using deterministic policy gradients \cite{ddpg}. The method proposed in Paper B is such an RL-algorithm.

In this paper, we show that small target variance ($<1$) along with momentum optimization on ReLU-networks severely affect learning performance. We observe that as variance of targets are reduced, the ReLU units eventually output zero for all inputs, rendering the NN a constant function.

We continue by deriving analytical gradients for a ReLU-activated linear regression model with normally distributed inputs. Using these expressions, we could conclude the presence of two cones in parameter space: a *linear cone* and a *dead cone*, see \figref{below}. In the linear cone we show that parameters evolve as in a linear autonomous system, including oscillations when we use momentum optimization \cite{}. In the dead cone gradients approach zero exponentially fast, resulting in a sub-optimal saddle point where parameters converge.

![image-20200311095859535](figures/cones.png)

Small variances of targets imply that the global optimum in parameter space approaches the point where these cones meet. We show with numerical examples that parameters in this case evolves either into the dead cone, or into the linear cone where oscillations then push the parameters into the dead cone. As target variance decrease, and momentum increases, a majority of parameter initializations converge in the dead cone, see \figref{below}.

<img src="figures/dying_regions.png" style="zoom: 25%;" />

#### Contributions

- We show empirically that the choice of target variance is of real practical importance.
- We show empirically that the deterioration of learning performance is correlated with an increase of ReLU that output zero for all inputs.
- We derive analytical expressions supporting the presence of two cones in parameter space and their respective properties.
- We show with numerical examples that a majority of parameter initializations all produce constant NNs as target variance reduces and momentum increases.

#### Contributions by the author

The author contributed the majority in all of the above.

## Discussion and Future Work

### Low-dimensional embeddings

- Harder problems

Confining the search space to a minimum subset is crucial for many 

- How to incorporate observations when searching for $z$?

### Regression performance

- Bias

