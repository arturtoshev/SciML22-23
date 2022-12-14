# Gaussian Processes

As the main mathematical construct behind Gaussian Processes, we first introduce the Multivariate Gaussian distribution. We will analyze this distribution in some more detail to provide reference results. For a more detailed derivation of the results, refer to [Bishop, 2006](https://link.springer.com/book/9780387310732), Section 2.3.

## Multivariate Gaussian Distribution

The **univariate** (for a scalar random variable) Gaussian distribution has the form

$$\mathcal{N}(x; \underbrace{\mu, \sigma^2}_{\text{parameters}}) = \frac{1}{\sqrt{2 \pi \sigma^2}} \exp\left\{ - \frac{(x-\mu)^2}{2 \sigma^2}\right\}.$$

The two parameters are the mean $\mu$ and variance $\sigma^2$.

The multivariate Gaussian distribution then has the following form


$$\mathcal{N}(x; \mu, \Sigma)= \frac{1}{(2\pi)^{d/2}\sqrt{\det (\Sigma)}} \exp \left(-\frac{1}{2}(x-\mu)^{\top}\Sigma^{-1}(x-\mu)\right),$$

with 

- $x \in \mathbb{R}^d \quad $ - feature / sample / random vector
- $\mu \in \mathbb{R}^d \quad $ - mean vector
- $\Sigma \in \mathbb{R}^{d \times d} \quad$ - covariance matrix

Properties:
- $\Delta^2=(x-\mu)^{\top}\Sigma^{-1}(x-\mu)$ is a quadratic form.
- $\Delta$ is the Mahalanobis distance from $\mu$ to $x$. It collapses to the Euclidean distance for $\Sigma = I$.
- $\Sigma$ is symmetric positive semi-definite and its diagonal elements contain the variance, i.e. covariance with itself.
- $\Sigma$ can be diagonalized with real eigenvalues $\lambda_i$

$$\sum u_i = \lambda_i u_i \quad i=1,...,d$$

and eigenvectors $u_i$ forming a unitary matrix

$$U= \left[\begin{array}{l} u_1^{\top} \\ ... \\ u_d^{\top}\\ \end{array}\right], \quad U^{\top}U=I$$

$$U\Sigma U^{\top} = \lambda, \quad \lambda \text{ diagonal matrix of eigenvalues}$$

If we apply the variable transformation $y=U(x-\mu)$, we can transform the Gaussian PDF to the $y$ coordinates according to the change of variables rule (see preliminaries)

$$p_X(x) = p_Y(y) \underbrace{\left| \frac{\partial y}{\partial x} \right|}_{det|U_{ij}|},$$

which leads to 

$$p(y) = \Pi_{i=1}^d \frac{1}{\sqrt{2 \pi \lambda_i}} \exp \left\{ - \frac{y_i^2}{2\lambda_i} \right\}$$

Note that the diagonalization of $\Delta$ leads to a factorization of the PDF into $d$ 1D PDFs.
### 1st and 2nd Moment

$$E[x] = \mu$$

$$E[xx^{\top}] = \mu \mu^{\top} + \Sigma$$

$$Cov(x) = \Sigma$$

### Conditional Gaussian PDF

Consider the case $x \sim \mathcal{N}(x; \mu, \Sigma)$ being a $d$-dimensional Gaussian random vector. We can partition $x$ into two disjoint subsets $x_a$ and $x_b$. 


$$
x=\left(\begin{array}{c}
x_a \\
x_b
\end{array}\right) .
$$

The corresponding partitions of the mean vector $\mu$ and covariance matrix $\Sigma$ become

$$
\mu=\left(\begin{array}{l}
\mu_a \\
\mu_b
\end{array}\right)
$$

$$
\Sigma=\left(\begin{array}{ll}
\Sigma_{a a} & \Sigma_{a b} \\
\Sigma_{b a} & \Sigma_{b b}
\end{array}\right) .
$$

Note that $\Sigma^T=\Sigma$ implies that $\Sigma_{a a}$ and $\Sigma_{b b}$ are symmetric, while $\Sigma_{b a}=\Sigma_{a b}^{\mathrm{T}}$.

We also define the precision matrix $\Lambda = \Sigma^{-1}$, being the inverse of the covariance matrix, where

$$
\Lambda=\left(\begin{array}{ll}
\Lambda_{a a} & \Lambda_{a b} \\
\Lambda_{b a} & \Lambda_{b b}
\end{array}\right)
$$

and $\Lambda_{a a}$ and $\Lambda_{b b}$ are symmetric, while $\Lambda_{a b}^{\mathrm{T}}=\Lambda_{b a}$. Note, $\Lambda_{a a}$ is not simply the inverse of $\Sigma_{a a}$.

Now, we want to evaluate $\mathcal{N}(x_a| x_b; \mu, \Sigma)$ and use $p(x_a, x_b) = p(x_a|x_b)p(x_b)$. We expand all terms of the pdf given the split, and consider all terms that do not involve $x_a$ as constant and then we compare with the generic form of a Gaussian for $p(x_a| x_b)$. We can decompose the equation into quadratic, linear and constant terms in $x_a$ and find an expression for $p(x_a|x_b)$.

> For all intermediate steps refer to [Bishop, 2006](https://link.springer.com/book/9780387310732).

$$
\begin{aligned}
\mu_{a \mid b} & =\mu_a+\Sigma_{a b} \Sigma_{b b}^{-1}\left(x_b-\mu_b\right) \\
\Sigma_{a \mid b} & =\Sigma_{a a}-\Sigma_{a b} \Sigma_{b b}^{-1} \Sigma_{b a}
\end{aligned}
$$

$$p(x_a| x_b) = \mathcal{N(x; \mu_{a|b}, \Sigma_{a|b})}$$

### Marginal Gaussian PDF

For the marginal PDF we integrate out the dependence on $x_b$ of the joint PDF:

$$p(x_a) = \int p(x_a, x_b) dx_b.$$

We can follow similar steps as above for separating terms that involve $x_a$ and $x_b$. After integrating out the Guassian with a quadratic term depending on $x_b$ we are left with a lengthy term involving $x_a$ only. By comparison with a Gaussian PDF and re-using the block relation between $\Lambda$ and $\Sigma$ as above we obtain for the marginal

$$p(x_a) = \mathcal{N}(x_a; \mu_a, \Sigma_{a a}).$$

### Bayes Theorem for Gaussian Variables

Generative learning addresses the problem of finding a posterior PDF from a likelihood and prior. The basis is Bayes rule for conditional probabilities 

$$p(x|y) = \frac{p(y|x)p(x)}{p(y)}$$

We want to find the posterior $p(x|y)$ and the evidence $p(y)$ under the assumption that the likelihood $p(y|x)$ and the prior are **linear Gaussian models**.

- $p(y|x)$ is Gaussian and has a mean that depends at most linearly on $x$ and a variance that is independent of $x$.
- $p(x)$ is Gaussian.

These requirements correspond to the following structure of $p(x)$ and $p(y|x)$

$$
\begin{aligned}
p(x) & =\mathcal{N}\left(x \mid \mu, \Lambda^{-1}\right) \\
p(y \mid x) & =\mathcal{N}\left(y \mid A x+b, L^{-1}\right).
\end{aligned}
$$

From that we can derive an analytical evidence (marginal) and posterior (conditional) distributions (for more details see [Bishop, 2006](https://link.springer.com/book/9780387310732)):

$$
\begin{aligned}
p(y) & =\mathcal{N}\left(y \mid A \mu+b, L^{-1}+A \Lambda^{-1} A^{\top}\right) \\
p(x \mid y) & =\mathcal{N}\left(x \mid \Sigma\left\{A^{\top} L(y-b)+\Lambda \mu\right\}, \Sigma\right),
\end{aligned}
$$

where

$$
\Sigma=\left(\Lambda+A^{\top} L A\right)^{-1} .
$$

### Maximum Likelihood for Gaussians

In generative learning, we need to infer PDFs from data. Given a dataset $X=(x_1, ..., x_N)$, where $x_i$ are i.i.d. random variables drawn from a multivariate Gaussian, we can estimate $\mu$ and $\Sigma$ from the maximum likelihood (ML) (for more details see [Bishop, 2006](https://link.springer.com/book/9780387310732)):

$$\mu_{ML} = \frac{1}{N} \sum_{n=1}^N x_n$$

$$\Sigma_{ML} = \frac{1}{N} \sum_{n=1}^N (x-\mu)^{\top}(x-\mu)$$


$\mu_{ML}$ and $\Sigma_{ML}$ correspond to the so-called sample or empirical estimates. However, $\Sigma_{ML}$ does not deliver an unbiased estimate of the covariance. The difference decreases with $N \to \infty$, but for a certain $N$ we have $\Sigma_{ML}\neq \Sigma$. The practical reason used in the above derivation is that the $\mu_{ML}$ estimate may occur as $\bar{x}$ within the sampling of $\Sigma$ $\Rightarrow$ miscount by one. An unbiased sample variance can be defined as

$$\widetilde{\Sigma} = \frac{1}{N-1} \sum_{n=1}^N (x_n-\mu_{ML})^{\top}(x_n-\mu_{ML})$$

## Definition of the Gaussian Process

Gaussian Processes are a generalization of the generative learning concept based on Bayes rule and Gaussian distributions as models derived from [Gaussian Discriminant Analysis](https://www.eecs189.org/static/notes/n18.pdf). We explain Gaussian Processes (GPs) on the example of regression and discuss an adaptation to discrimination in the spirit of Gaussian Discriminant Analysis (GDA), however not being identical to GDA at the same time. Consider linear regression:

$$
h(x) = \omega^{\top} \varphi(x)
$$

here $\omega \in \mathbb{R}^{m}$ are the weights, $\varphi \in \mathbb{R}^{m}$ is the feature map, and $h(x)$ is the hypothesis giving the probability of $y$. We now introduce an isotropic Gaussian prior on $\omega$, where isotropic is defined as being a diagonal matrix $\zeta$ with some component $\alpha^{-1}$.

$$
p(\omega) = \Pi(\omega; 0, \alpha^{-1} I)
$$

where $0$ is the zero-mean nature of the Gaussian prior, with covariance $\alpha^{-1} I$.

> Note that $\alpha$ is a hyperparameter, i.e. responsible for the model properties, it is called hyper $\ldots$ to differentiate from the weight parameters $\omega$.

Now let's consider the data samples $\left( y^{(i)} \in \mathbb{R}, x^{(i)} \in \mathbb{R}^{m} \right)_{i=1, \ldots, n}$. We are interested in leveraging the information contained in the data samples, i.e. in probabilistic lingo in the joint distribution of $y^{(1)}, \ldots, y^{(n)}$. We can then write

$$
y = \phi \hspace{2pt} \omega
$$

where $y \in \mathbb{R}^{n}$, $\phi \in \mathbb{R}^{n \times m}$, and $\omega \in \mathbb{R}^{m}$. $\phi$ is the well-known design matrix with feature map. As we know that $\omega$ is Gaussian the probability density function (pdf) of $y$ is also Gaussian:

$$
\begin{align}
    \mathbb{E}[y] &= \mathbb{E}[\phi \omega] = \phi \mathbb{E}[\omega] = 0 \\
    \text{Cov}[y] &= \mathbb{E}[y y^{\top}] = \phi \mathbb{E}[\omega \omega^{\top}] = \frac{1}{\alpha} \phi \phi^{\top} = K
\end{align}
$$

where $K$ is the Grammian matrix which we already encountered before.


A Gaussian Process is now defined as:

* $y^{(i)} = h(x^{(i)}), \quad i=1, \ldots, m$ have a joint Gaussian PDF, i.e. are fully determined by their 2nd-order statistics.

## Gaussian Process for Regression

Take into account Gaussian noise, a modeling assumption, on sample data:

$$
\begin{align}
    y^{(i)} &= h(x^{(i)}) + \varepsilon^{(i)}, \quad i=1, \ldots, m \\
    p(y^{(i)} | h(x^{(i)})) &= \Pi \left( y^{(i)}, h(x^{(i)}), \frac{1}{\beta} \right)
\end{align}
$$

with the isotropic Gaussian $\Pi$.
<div>
<center><img src = "https://i.imgur.com/Uf1YFhD.png" width = "400">

<center><img src="https://i.imgur.com/AJdLn8l.png" width="400">
</div>

where $h(x^{(i)}) = h^{(i)}$ becomes a *latent variable*. For the latent variables (these correspond to the noise-free regression data we considered earlier) we also assume a Gaussian model PDF as prior

$$
p(h) = \Pi(y; 0, K)
$$

The requisite posterior PDF then becomes

$$
p(y|x) = \int p(y | h, x) p(h|x) dh
$$

i.e. the posterior pdf is defined by the by marginalization of $p(y, h| x)$ over the latent variable h. For the computation of $p(y|x)$ we follow previous computations with adjustments of the notation where appropriate.

$$
z = \left[\begin{matrix}
    h \\
    y
\end{matrix}\right], \quad h \in \mathbb{R}^{n}, y \in \mathbb{R}^{n}
$$

from previous lectures we recall

$$
\begin{align}
    \mathbb{E} &= A \mu + b \\
    \text{Cov}[y] &= L^{-1} + A  \Lambda A^{\top}
\end{align}
$$

into which we now substitute

$$
\begin{align}
    \mu &= 0, \text{ by modeling assumption} \\
    b &= 0, \text{ by modeling assumption} \\
    A &= I \\
    L^{-1} &= \frac{1}{\beta} I \\
    \Lambda^{-1} &= \text{Cov}[h] = K
\end{align}
$$

$$
\Longrightarrow p(y|x) = \Pi(y; 0, \frac{1}{\beta}I + K)
$$

Note that the kernel $K$ can be presumed and is responsible for the accuracy of the prediction by the posterior. There exists a whole plethora of possible choices of K, the most commong of which are:

<div>
    <center>
    <img src="https://i.imgur.com/ofLuADN.png" width="450">
</div>

Looking at a practical example of a possible kernel:

$$
k(x^{(i)}, x^{(j)}) = \theta_{0} e^{- \frac{\theta_{1}}{2} || x^{(i)} - x^{(j)}||^{2}} + \theta_{2} + \theta_{3} x^{(i) \top} x^{(j)}
$$

where $\theta_{0}$, $\theta_{1}$, $\theta_{2}$, $\theta_{3}$ are model hyperparameters. With the above $p(y|x)$ we have identified which Gaussian is inferred from the data. Moreover we have not yet formulated a predictive posterior for *unseen data*. For this purporse we extend the data set

$$
\underbrace{\left( y^{(n+1)}, x^{(n+1)} \right)}_{\text{unseen data}}, \quad \underbrace{\left( y^{(n)}, x^{(n)} \right), \ldots, \left( y^{(1)}, x^{(1)} \right)}_{\text{seen data}}
$$

We reformulate the prediction problem as that of determining a conditional PDF, where given

$$
y = \left[
    \begin{matrix}
        y^{(1)}\\
        \vdots \\
        y^{(n)}
    \end{matrix}
\right]
$$

we search for the conditional PDF $p(y^{(n+1)}| y)$. As intermediate we need the joint PDF

$$
p(\tilde{y}), \quad \tilde{y} = \left[
    \begin{matrix}
        y^{(1)}\\
        \vdots \\
        y^{(n+1)}
    \end{matrix}
\right]
$$

We assume that it also follows a Gaussian PDF. However, as $x^{(n+1)}$ is unknown before prediction we have to make the dependence of the covariance matrix of $p(\tilde{y})$ explicit:

<div>
    <center>
    <img src="https://i.imgur.com/aUJvJxN.png" width="450">
</div>

$$
K = \left[
    \begin{matrix}
        k(x^{(1)}, x^{(n+1)}) \\
        \vdots \\
        k(x^{(n)}, x^{(n+1)})
    \end{matrix}
\right]
$$
 i.e. the corresponding entries in the extended $\tilde{K} = \frac{1}{\alpha} \tilde{\phi} \tilde{\phi}^{\top}$ i.e. $\tilde{K}_{1, \ldots, n+1}$, where $\tilde{\phi}_{n+1} = \varphi(x^{n+1})$. The c-entry above is then given by

 $$
c = \tilde{K}_{n+1, n+1} + \frac{1}{\beta}
 $$

 Using the same approach as before we can then calculate the mean and covariance of the predictive posterior for unseen data $p(y^{(n+1)}|y)$. Recalling from before

$$
\mu(a | b) = \mu(a) + \Sigma_{ab} \Sigma^{-1}_{bb}(x_{b} - \mu_{b})
$$

where we now adjust the entries for our present case with

$$
\begin{align}
    \mu(a|b) &= \mu(y^{n+1}|y) \in \mathbb{R} \\
    \mu_{a} &= 0 \\
    \Sigma_{ab} &= k^{\top} \\
    \Sigma^{-1}_{bb} &=  (K + \frac{1}{\beta} I)^{-1} \\
    x_{b} &= y \\
    \mu_{b} &= 0
\end{align}
$$

and for the covariance $\Sigma$ we recall

$$
\Sigma_{a|b} = \Sigma_{aa} - \Sigma_{ab} \Sigma^{-1}_{bb} \Sigma_{ba}
$$

adjusting for the entries in our present case

$$
\begin{align}
    \Sigma_{aa} &= c \\
    \Sigma_{ab} &= k^{\top} \\
    \Sigma_{bb}^{-1} &= (K + \frac{1}{\beta} I)^{-1} \\
    \Sigma_{ba} &= k \\
    \Sigma_{a|b} &= \Sigma_{y^{(n+1)}|y} \in \mathbb{R}
\end{align}
$$

From which follows

$$
\begin{align}
    \mu(y^{(n+1)}|y) &= k^{\top}(K + \frac{1}{\beta}I)^{-1} y \\
    \Sigma_{y^{(n+1)}|y} &= c - k^{\top} (K + \frac{1}{\beta}I)^{-1} k
\end{align}
$$

which fully defines the Gaussian posterior PDF of Gaussian-process regression. Looking at some sketches of GP regression, beginning with the data sample $y_{1}$, and predicting the data $y_{2}$


<div>
    <center>
    <img src="https://i.imgur.com/78unDSH.png" width="400">
</div>

<div>
    <center>
    <img src="https://i.imgur.com/25mZbzG.png" width="450">
</div>

And in practive for a 1-dimensional function, which is being approximated with a GP, and we are monitoring the change in mean, variance and synonymously the GP-behaviour (recall, the GP is defined by the behaviour of its mean and covariance) with each successive data point.

<div>
    <center>
    <img src="https://i.imgur.com/rUBB1p3.png" width="450">
</div>

> This immediately shows the utility of GP-regression for engineering applications where we often have few data points but yet need to be able to provide guarantees for our predictions which GPs offer at a reasonable computational cost.

#### Further Kernel Configurations

There exist many ways in which we can extend beyond just individual kernels by multiplication and addition of simple "basis" kernels to construct better informed kernels. Just taking a quick glance at some of these possible combinations, where the following explores this combinatorial space for the following three kernels:

* Squared Exponential (SE) kernel
* Linear (Lin) kernel
* Periodic (Per) kernel 

<div>
    <center>
    <img src="https://i.imgur.com/okHqR7s.png" width="450">
</div>

<div>
    <center>
    <img src="https://i.imgur.com/3IDI7FR.png" width="450">
</div>

<div>
    <center>
    <img src="https://i.imgur.com/ggMjF21.png" width="450">
</div>

In higher dimensions this then takes the following shape:

<div>
    <center>
    <img src="https://i.imgur.com/9RsPSLG.png" width="450">
</div>

With all these possible combinations one almost begs the question if these kernels cannot be constructed automatically. The answer is, partially yes, partially no. In a sense the right kernel construction is almost like feature engineering, and while it can be automated in parts it remains a craft for the domain scientists to understand the nature of their problem to then construct the right prior distribution.

<div>
    <center>
    <img src="https://i.imgur.com/tsqTFPe.png" width="450">
</div>

### Multivariate Prediction

The extension to multivariate prediction is then straightforward. For the unseen data

$$
y' = \left[
    \begin{matrix}
        y^{(n+1)} \\
        \vdots \\
        y^{(n+l)}
    \end{matrix}
\right] \in \mathbb{R}^{l}, \quad x' = \left[
    \begin{matrix}
        x^{(n+1)}\\
        \vdots \\
        x^{(n+l)}
    \end{matrix}
\right] \in \mathbb{R}^{l \times m}
$$

and for the sample data

$$
y = \left[
    \begin{matrix}
        y^{(1)} \\
        \vdots \\
        y^{(n)}
    \end{matrix}
\right] \in \mathbb{R}^{n}, \quad x = \left[
    \begin{matrix}
        x^{(1)}\\
        \vdots \\
        x^{(n)}
    \end{matrix}
\right] \in \mathbb{R}^{n \times m}
$$

for ever larger l's the process can then just be repeated. The mean and covariance matrix are then given by

$$
\begin{align}
    \mu(y'|y) &= K(x', x) \left( K(x, x) - \frac{1}{\beta} I \right)^{-1} y \\
    \Sigma_{y'|y} &= K(x', x') + \frac{1}{\beta}I - K(x', x) \left( K(x, x) + \frac{1}{\beta} I \right)^{-1} K(x, x')
\end{align}
$$

### Notes on Gaussian Process Regression

* $K + \frac{1}{\beta}I$ needs to be symmetric and positive definite, where positive definite implies that the matrix is symmetric. $K$ can be constructed from valid kernel functions involving some hyperparameters $\theta$.
* GP regression involves a $n \times n$ matrix inversion, which requires $\mathcal{O}(n^{3})$ operations for learning.
* GP prediction involves a $n \times n$ matrix multiplication which requires $\mathcal{O}(n^{2})$ operations.
* The operation count can be reduced significantly when lower-dimensional projections of the kernel function on basis functions can employed, or we are able to exploit sparse matrix computations.

### Learning the Hyperparameters

To infer the kernel hyperparameters from data we need to:

1. Introduce an appropriate likelihood function $p(y | \theta)$
2. Determine the optimum $\theta$ via maximum likelihood estimation (MLE) $\theta^{\star} = \arg \max \ln p(y | \theta)$, which corresponds to linear regression
3. $\ln p(y|\theta) = -\frac{1}{2} \ln |K + \frac{1}{\beta}I| - \frac{1}{2} y^{\top} \left( K + \frac{1}{\beta}I \right)^{-1} y - \frac{n}{2} \ln 2 \pi$
4. Use iterative gradient descent or Newton's method to find the optimum, where you need to beware of the fact thar $p(y | \theta)$ may be non-convex in $\theta$, and hence have multiple maxima

$$
\begin{align*}
\frac{\partial}{\partial \theta_{j}} \ln p(y | \theta) &= - \frac{1}{2} \text{trace} \left[ \left(K + \frac{1}{\beta}I\right)^{-1} \frac{\partial(K + \frac{1}{\beta}I)}{\partial \theta_{i}} \right] \\
 &+ \frac{1}{2} y^{\top} \left( K + \frac{1}{\beta} I \right)^{-1} \frac{\partial(K + \frac{1}{\beta}I)}{\partial \theta_{i}}\left(K + \frac{1}{\beta}\right)^{-1}y
\end{align*}
$$

## Gaussian Processes for Classification

Without discussing all the details, we will now provide a brief sketch how Gaussian Processes can be adapted to the task of classification. Consider for simplicity the 2-class problem:

$$
0 < y < 1, \quad h(x) = \text{sigmoid}(\varphi(x))
$$

the PDF for $y$ conditioned on the feature $\varphi(x)$ then follows a Bernoulli-distribution:

$$
p(y | \varphi) = \text{sigmoid}(\varphi)^{y} \left( 1 - \text{sigmoid}(\varphi) \right)^{1-y}, \quad i=1, \ldots, n
$$

Finding the predictive PDF for unseen data $p(y^{(n+1)}|y)$, given the training data

$$
y = \left[
    \begin{matrix}
        y^{(1)} \\
        \vdots \\
        y^{(n)}
    \end{matrix}
\right]
$$

we then introduce a GP prior on 

$$
\tilde{\varphi} = \left[
    \begin{matrix}
        \varphi^{(1)} \\
        \vdots \\
        \varphi^{(n+1)}
    \end{matrix}
\right]
$$

hence giving us

$$
p(\tilde{\varphi}) = \Pi( \tilde{\varphi}; 0, K + \mu I)
$$

where $K_{ij} = k(x^{(i)}, x^{(j)})$, i.e. a Grammian matrix generated by the kernel functions from the feature map $\varphi(x)$.

> * Note that we do **NOT** include an explicit noise term in the data covariance as we assume that all sample data have been correctly classified.
> * For numerical reasons we introduce a noise-like form which improves conditioning of $K + \mu I$
> * For two-class classification it is sufficient to predict $p(y^{(n+1)} = 1 | y)$ as $p(y^{(n+1)} = 0 | y) = 1 - p(y^{(n+1)} = 1 | y)$

Using the PDF $p(y=1|\varphi) = \text{sigmoid}(\varphi(x))$ we obtain the predictive PDF:

$$
p(y^{(n+1)} = 1 | y) = \int p(y^{(n+1)} = 1 | \varphi^{(n+1)})  p(\varphi^{(n+1)}| y) d\varphi^{(n+1)}
$$

The integration of this PDF is analytically intractable, we are hence faced with a number of choices to integrate the PDF:

* Use sampling-based methods, and Monte-Carlo approximation for the integral
* Assume a Gaussian approximation for the posterior and evaluate the resulting convolution with the sigmoid in an approximating fashion

$p(\varphi^{(n+1)}|y)$ is the posterior PDF, which is computed from the conditional PDF $p(y| \varphi)$ with a Gaussian prior $p(\varphi)$.

## Relation of Gaussian Processes to Neural Networks

We have not presented a definition a neural network yet, but we have already met the most primitive definition of a neural network, the perceptron, for classification. The perceptron can be thought of as a neural network with just one layer of "neurons". On the other hand, the number of hidden units should be limited for perceptrons to limit overfitting. The essential power of neural networks  i.e. the outputs sharing the hidden units tends to get lost when the number of hidden units becomes very large. This is the core connection between neural networks and GPs, **a neural network with infinite width recovers a Gaussian process**.


## Further References

- [Pattern Recognition and Machine Learning](https://link.springer.com/book/9780387310732), Section 2.3; Christopher Bishop; 2006.
- [Automatic Model Construction with Gaussian Processes](https://www.cs.toronto.edu/~duvenaud/thesis.pdf), up to page 22; David Duvenaud; 2014.
- [Gaussian Processes for Machine Learning](https://gaussianprocess.org/gpml/), Section 2.1-2.6; Carl Edward Rasmussen, and Christopher K.I. Williams; 2006.
