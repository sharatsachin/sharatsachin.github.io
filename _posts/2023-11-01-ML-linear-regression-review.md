---
title: ML Review - Linear Regression
date: 2023-11-01 00:00:00 +0530
comments: true
categories: [review, ml]
tags: [ml]
math: true
---

This post gives a quick review of linear regression, not for first time learners, but for those who have already studied it and want a quick refresher.

<!--more-->

- linear regression can be used to fit a model to an observed dataset of values of the response (dependent variable) and explanatory variables (independent variables / features)
- \\(x^{(i)}\\) is the vector of input variables / features, $x^{(i)} = \begin{bmatrix} x_0^{(i)} \\ x_1^{(i)} \\ \vdots \\ x_n^{(i)} \end{bmatrix} _{((n+1) \times 1)}$, where \\(n\\) is the number of features, with $x_0^{(i)} = 1$ being the intercept term. 
- \\(y^{(i)}\\) is the output variable / target.
- \\((x^{(i)}, y^{(i)})\\) is a training example.
- \\(\{(x^{(i)}, y^{(i)}) : i = 1 \dotsm m\}\\) is the training set, where \\(m\\) is the number of examples in the training set.

#### Goal :

to learn a function \\(h(x) : \text{space of input values} \rightarrow \text{space of output values}\\), so that \\(h(x)\\) is a good predictor for the corresponding value \\(y\\)

### Equations

If we decide to approximate $y$ as a linear function of $x$, then for the $i^{th}$ training example:

$$\hat{y}^{(i)} =  h_\theta(x^{(i)}) = \theta_0 + \theta_1 x^{(i)}_1 + \theta_2 x^{(i)}_2 + \dotsm + \theta_n x^{(i)}_n = \sum_{j=0}^n \theta_j x^{(i)}_j$$

This is called **simple / univariate** linear regression for $n = 1$, and **multiple** linear regression, (if $n > 1$). This is different from **multivariate** regression, which pertains to multiple dependent variables and multiple independent variables. [Link](https://stats.stackexchange.com/q/2358/331716)

Then we can define the cost function as:
$$J(\theta) = \frac{1}{2m} \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})^2$$

This is the **ordinary least squares (OLS)** cost function, working to minimize the **mean squares error (MSE)**.

#### Goal :

to choose $\theta$ so as to minimize $J(\theta)$

### Vectorized

$$ X = \begin{bmatrix} - \left( x^{(1)} \right)^T - \\ - \left( x^{(2)} \right)^T - \\ \vdots \\ - \left( x^{(m)} \right)^T - \end{bmatrix}_{(m \times (n+1))} , \qquad \theta = \begin{bmatrix} \theta_0 \\ \theta_1 \\ \vdots \\ \theta_n \end{bmatrix}_{((n+1) \times 1)} \qquad and \qquad y = \begin{bmatrix} y^{(1)} \\ y^{(2)} \\ \vdots \\ y^{(m)} \end{bmatrix} _{(m \times 1)}$$

Then the vector of predictions, 

$$ \hat{y} =  X\theta = \begin{bmatrix} - \left( x^{(1)} \right)^T\theta - \\ - \left( x^{(2)} \right)^T\theta - \\ \vdots \\ - \left( x^{(m)} \right)^T\theta - \end{bmatrix}_{(m \times 1)} $$

We can rewrite the least-squares cost as following, replacing the explicit sum by matrix multiplication:

$$J(\theta) = \frac{1}{2m} (X\theta - y)^T(X\theta - y)$$

## Normal Equation

The normal equation is an analytical solution to the linear regression problem with a ordinary least square cost function. That is, to find the value of $\theta$ that minimizes $J({\theta})$, take the [gradient](https://mathinsight.org/gradient_vector) of $J(\theta)$ with respect to $\theta$ and equate to $0$, ie $\nabla_\theta J(\theta) = 0$.

Solving for $\theta$, we get 

$$\theta = (X^TX)^{-1} X^Ty$$

[Here](https://eli.thegreenplace.net/2014/derivation-of-the-normal-equation-for-linear-regression/) is a post containing the derivation of the normal equation.

## Gradient Descent

Gradient descent is based on the observation that if the function $J({\theta})$ is differentiable in a neighborhood of a point $\theta$, then $J({\theta})$ decreases fastest if one goes from $\theta$ in the direction of the negative gradient of $J({\theta})$ at $\theta$. 

Thus if we repeatedly apply the following update rule, ${\theta := \theta - \alpha \nabla J(\theta)}$ for a sufficiently small value of **learning rate**, $\alpha$, we will eventually converge to a value of $\theta$ that minimizes $J({\theta})$.

For a specific paramter $\theta_j$, the update rule is 

$$\theta_j := \theta_j - \alpha \frac{\partial}{\partial \theta_j} J({\theta}) $$

Using the definition of $J({\theta})$, we get

$$\frac{\partial}{\partial \theta_j} J({\theta}) = \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})x_j^{(i)}$$

Therefore, we repeatedly apply the following update rule:

$$
\begin{align*}
& \text{Loop:} \\
& \quad \theta_j := \theta_j - \alpha \frac{1}{m} \sum_{i=1}^m \left( h_\theta(x^{(i)}) - y^{(i)}\right)x_j^{(i)} \\
& \quad \text{simultaneously update } \theta_j \text{ for all } j \\
\end{align*}
$$

This method looks at every example in the entire training set on every step, and is called **batch gradient descent (BGD)**. 

When the cost function $J$ is convex, all local minima are also global minima, so in this case gradient descent can converge to the global solution.

There is an alternative to BGD that also works very well:

$$
\begin{align*}
& \text{Loop:} \\
& \quad \text{for } i=1 \text{ to } m \text{ \{} \\
& \quad \quad \theta_j := \theta_j - \alpha \left( h_\theta(x^{(i)}) - y^{(i)}\right)x_j^{(i)} \\
& \quad \quad \text{simultaneously update } \theta_j \text{ for all } j \\
& \quad \text{\}} \\
\end{align*}
$$


This is **stochastic gradient descent (SGD)** (also incremental gradient descent), where we repeatedly run through the training set, and for each training example, we update the parameters using gradient of the error for that training example only.

Whereas BGD has to scan the entire training set before taking a single step, SGD can start making progress right away with each example it looks at. 

Often, SGD gets $\theta$ *close* to the minimum much faster than BGD. However it may never *converge* to the minimum, and $\theta$ will keep oscillating around the minimum of $J(\theta)$; but in practice these values are reasonably good approximations. Also, by slowly decreasing $\alpha$ to $0$ as the algorithm runs, $\theta$ converges to the global minimum rather than oscillating around it.

## Adding regularization

Regularization is a technique to reduce overfitting in machine learning. This technique discourages learning a more complex or flexible model, by shrinking the parameters towards $0$.

We can regularize machine learning methods through the cost function using $L1$ regularization or $L2$ regularization. $L1$ regularization adds an absolute penalty term to the cost function, while $L2$ regularization adds a squared penalty term to the cost function. A model with $L1$ norm for regularisation is called **lasso regression**, and one with (squared) $L2$ norm for regularisation is called **ridge regression**. [Link](https://towardsdatascience.com/intuitions-on-l1-and-l2-regularisation-235f2db4c261)

$$J(\theta)_{L1} = \frac{1}{2m} \left( \sum_{i=1}^m \left( h_\theta\left( x^{(i)} \right) - y^{(i)} \right)^2 \right) + \frac{\lambda}{2m} \left( \sum_{j=1}^n |\theta_j| \right)$$

$$J(\theta)_{L2} = \frac{1}{2m} \left( \sum_{i=1}^m \left( h_\theta\left( x^{(i)} \right) - y^{(i)} \right)^2 \right) + \frac{\lambda}{2m} \left( \sum_{j=1}^n \theta_j^2 \right)$$

The partial derivative of the cost function for lasso linear regression is:

$$
\begin{align}
& \frac{\partial J(\theta)_{L1}}{\partial \theta_0} = \frac{1}{m} \sum_{i=1}^m \left( h_\theta \left(x^{(i)} \right) - y^{(i)} \right) x_j^{(i)} 
& \qquad \text{for } j = 0 \\
& \frac{\partial J(\theta)_{L1}}{\partial \theta_j} = \frac{1}{m} \sum_{i=1}^m \left( h_\theta \left( x^{(i)} \right) - y^{(i)} \right) x_j^{(i)} + \frac{\lambda}{2m} signum (\theta_j)
& \qquad \text{for } j \ge 1
\end{align}
$$

Similarly for ridge linear regression,

$$
\begin{align}
& \frac{\partial J(\theta)_{L2}}{\partial \theta_0} = \frac{1}{m} \sum_{i=1}^m \left( h_\theta \left(x^{(i)} \right) - y^{(i)} \right) x_j^{(i)} 
& \qquad \text{for } j = 0 \\
& \frac{\partial J(\theta)_{L2}}{\partial \theta_j} = \frac{1}{m} \sum_{i=1}^m \left( h_\theta \left( x^{(i)} \right) - y^{(i)} \right) x_j^{(i)} + \frac{\lambda}{m} \theta_j 
& \qquad \text{for } j \ge 1
\end{align}
$$

These equations can be substituted into the general gradient descent update rule to get the specific lasso / ridge update rules.

#### Note:
- $\theta_0$ is NOT constrained
- scale the data before using Ridge regression
- $\lambda$ is a hyperparameter: bigger results in flatter and smoother model 
- Lasso tends to completely eliminate the weights of the least important features (i.e., setting them to 0) and it automatically performs feature selection
- Last way to constrain the weights is Elastic net, a combination of Ridge and Lasso
- When to use which?
    * Ridge is a good default
    * If you suspect some features are not useful, use Lasso or Elastic
    * When features are more than training examples, prefer Elastic