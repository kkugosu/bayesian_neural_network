# bayesian_neural_network

we have to check some point 

# noise assumption

in fitting problem, we don't have to use noise assumption, but in regression we gradually develop the model to make less error. 

using noise assumption make regression possible. so in bayesian neural network, we assume there is noise between output F and target Y.

# parameter w to update

prml 3.55

we can get log posterior distribution by adding log prior and log likelihood

$$ lnp(w|t) = -{\beta \over 2} \sum^N_{n=1} {t_n - w^T \phi (x_n)}^2 - {\alpha \over 2} w^2 w + const $$

and we can regress posterior distribution by subtracting derivative of log posterior with respect to "w"

then w in likelihood term $w^T \phi (x_n)$ and prior term 
${\alpha \over 2} w^2 w$ have to be derivated

in dropout as a bayesian approximation appendix, we have to maximize elbo term

$$ \int q(w) logp(Y|X,w)dw - KL(q(w)||p(w)) $$

in here the w in likelihood term $logp(Y|X,w) $ and w in prior term $p(w) $ behave differentely.

in 3.55 that formual is like $w_d = w_d + \alpha {d \over dw_d} f(x) $

in elbo, formula is like find $\Delta w$  to maximize $f(w + \Delta w)$ 

we already have the form of auxiliary variable $w + \Delta w $ and we don't have to get derivative form of elbo.

so we make w in $logp(Y|X,w) $ follows $w + \Delta w $ and w in $KL(q(w)||"p(w)")$ follows w

# is that can be gaussian process?

the output never can be gaussian so this model can't learn gaussian process. but this model can approximate gaussian process by clt

<img width="559" alt="스크린샷 2022-06-05 오후 10 49 02" src="https://user-images.githubusercontent.com/24292848/172053919-81ed5d46-58ac-4c32-bbea-f8f7d90a0384.png">

no matter how $W^2_j$ $W^1_i$ behaves, if $W^1_j $ are iid distributed and n goes infinitely, output follows gaussian distribution.

but, in that situation, that model only can learn linear relation between input and output data
because of iid assumption.

so we have to add one more linear layer or one more bayesian linear layer to learn nonlinear relationship.

but if we use lindeberg clt, we don't have to care about how $W^1_j $ behave too. so we can impose non linear learning ability to this model without using additional layer

# do we need to expected value of hidden units?

in the dropout paper, auther 

output to be mean value of hidden layer units which is concept of clt. 

but actually we don't need to get mean value, we can just sum of it. 

this paper make kernel 

$$ \widehat{K} (x,y) = {1 \over K} \sum^K_{k=1} \sigma (w^T_k x + b_k) \sigma (w^T_k y + b_k)$$

but use this is better

$$ \widehat{K} (x_1,x_2)= \sum^K_{k=1} \sigma (w^T_k x_1 + b_k) \sigma (w^T_k x_2 + b_k)$$

i just subtract K and change notation y this can be confused.

# some other change, other assumption

we don't use dropout. we use dropconnect which is more theoretically fitted.

but we call dropconnect as dropout haha

the entropy of a mixture of Gaussians with a large enough dimensionality and randomly distributed means tends towards the sum of the Gaussians’ volumes

this statements is this
![kld](https://user-images.githubusercontent.com/24292848/172194266-970c554a-c9fb-49aa-9f40-631a9e7ce684.jpeg)

to calculate this, we need to make sigma(var) really small. this paper use 10^-33

so if we train model with dropout and use model with dropout, then it is same as learning gaussian process.

but in this time, i will use single gaussian assumption to practice.

# now

if we trim the elbo and subtract constant ($\sigma = 1, \tau$...) term,

then we just have to maximize below

$$ - \sum^N_{n=1} \tau || y_n - \widehat{y_n} ||^2_2 + \sum^Q \sum^K (\mu_{1,q,k} - \mu_{1,q,k} ')^2 + \sum^K \sum^D (\mu_{2,k,d} - \mu_{2,k,d} ')^2 + \sum^K (\mu_{b,k} - \mu_{b,k} ')^2$$

in this project, i set sigma = 0.1
