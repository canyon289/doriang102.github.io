
*Under Construction*

Recall that in the last post, our aim was, given $$y \in \mathbb{R}^n$$ and our features $$X \in \mathbb{R}^{n \times k}$$ to find $$\beta \in \mathbb{R}^k$$ which minimized:

$$ \min_{\beta} \frac{1}{N} \sum_{i=1}^N(y_i - \beta \cdot \mathbf x_i)^2.$$

We will explore the idea of over fitting and model selection in this section and see the benefits and drawbacks of various methods of parameter penalizaiton. 

# Regularized Linear Regression


Let's say that our data points satisfy the following:

$$ \mathbf{y} = 10\mathbf{x_0} + \epsilon, $$

where $$\epsilon \sim \mathcal{N}(0,1)$$. 

We have $$\mathbb{R}^k$$ features, so we can actually solve:

$$ \mathbf{X} \beta =  \mathbf{y}, $$

for some $$\beta \in \mathbb{R}^k$$, since $$y$$ is *But wait!* Doesn't that mean we can pick up every single point of $$y$$ by finding a unique vector $$\beta = (\beta_1,\cdots,\beta_k)$$? Yes! This is actually why we need regularization in the first place. Even though we have a collection of $$k$$ orthogonal features, if we have $$j < k$$ variables which influence $$y$$, then we can over solve the system. This all seems very abstract though, so let's construct a concrete example. 

We are going to construct an orthogonal matrix of dimension $$50$$ when $$y$$ depends on only one variable:

$$ \mathbf{y} = 10\mathbf{x_0} + \epsilon. $$

What do you think will happen if we include only 1 feature? 20? 40? 80? Let's write some code in Python to investigate. First let's construct the orthogonal matrix and make a scatter plot to see how $$y$$ depends on $$x$$:

{% highlight ruby %} 
from scipy.stats import ortho_group 
m = ortho_group.rvs(dim=50)
df_orth = pd.DataFrame(m).T


y = 10*x0 + np.random.normal(0, 1, 50)

plt.scatter(x0,y)

 {% endhighlight %}

![](/img/scatter_overfit.png?raw=true)

Now we have an orthogonal set of 50 features, but our output variable $$y$$ depends on only one of them. What happens as we include more of the features into our model? If you followed the previous discussion, you should know the answer already. 

{% highlight ruby %} 
from sklearn import datasets, linear_model

for d in range(0,80,20):
    regr = linear_model.LinearRegression(fit_intercept=False)
    X=df_orth.loc[:,0:d]
    # Train the model using the training sets
    regr.fit(X,y)

    # Make predictions using the testing set
    y_pred = regr.predict(X)
    plt.scatter(x0,y,color='b')
    plt.scatter(x0,y_pred,color='r')
    plt.show()
 {% endhighlight %}   
 
 
 ![](/img/overfit_0.png?raw=true)
 
 ![](/img/overfit_20.png?raw=true)
 
 ![](/img/overfit_40.png?raw=true)
 
 ![](/img/overfit_60.png?raw=true)


Do you see what's going on here? As we increase the number of features, we are able to solve for every point exactly, which is not the true trend of the model. This will not generalize properly to new data, as we will see when we evaluate these models using cross validation in this section. 


### Regularization - Penalizing the Size of Coefficients

In the example above, we see that as we increase the number of features we use, we over fit the model. But how do we quantify this and evaluate in a rigorous way? First we must discuss the notion of cross validation - evaluating your model on held out data. 

$$\sum_{k=1}^n (y_k- \beta \cdot \mathbf{x_k})^2 + \lambda \|\beta\|_{L^p}$$




### Requirement 1 - Standardization of independent and dependent variables.

We will introduce a basic example in this section to deleniate the need for regularization and how over fitting can occur by simply having too many variables. 

Cnsider the example where we have a simple rule $$ y = 2x_1 + 1 + \epsilon $$ where $$\epsilon \sim \mathcal{N}(0,1)$$, but we are seeking to learn a model with $$\mathbb{x} \in \mathbb{R}^d$$ for $$d > 1$$. More data is a good thing right? We'll see why it's not, and Linear Algebra will be our tour guide. 


Let's consider a one dimensional example:

$$\sum_{k=1}^n (y_k- \beta \cdot \mathbf{x_k} - \beta_0)^2 + \lambda \|\beta+\beta_0\|_{L^p}$$


If $$\{x_k\}$$ are mean-zero centered, then the first expression has mean 0 if we choose the correct intercept for $$\beta_0$$. But what if it isn't? Let's imagine that $$\{y_k\}$$ have mean $$\mu=M$$ for $$ M > > 1$$ an to fix ideas. What will be the best choice of $$\lambda$$? For each $$\lambda > 0$$ we will find that $$\beta_0 = M$$ and so we want to shrink $$\lambda \to 0$$. What effect does this have? It means that we can't penalize any of the information in $$\beta$$ and thus you will over fit.  

$$ \hat y^i = \beta_0  + \beta_1 x_1^i + \beta_2 x_2^i + \cdots + \beta_k x_k^i $$



