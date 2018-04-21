# Paper Readings 2018.4.22

## Learning Transferrable Representations for Unsupervised Domain Adaptation, Ozan Sener et al.

Recently I have been reading papers about domain adaptation, a sub topic of transfer learning I think. Domain adaptation can be divided into multiple sub-problem depending on the number of labels of the target domain, like in unsupervised domain adaptation, there are two domains, the source domain $\{\hat{x}_i,\hat{y}_i\}$ with $N_s$ data points and the target domain $\{x_i\}$ with $N_t$ samples. If some of the data points of the target domain have some labels, it becomes semi-supervised domain adaptation.

This paper, as its title says, deals with unsupervised domain adaptation(UDA) problems. Further more it assumes that the two domains have different distributions $\hat{x}_i\sim p_s$ and $x|i\sim p_t$ but same definition space $\hat{x}_i, x_i\sim\mathcal{X}$ . So the author thinks that the feature function $\Phi$ of the two domains share in the shallow layer and can be expressed as $\Phi_s=\Phi_{\theta_c, \theta_s}, \Phi_t=\Phi_{\theta_c, \theta_t}$ where $\theta_c$ is the parameter of the shallow layer and $\theta_s, \theta_t$ refer to the respective final layers. This can be viewed as sharing parameters.

The main attribution of this paper, I think, is the formulation of the problem. Here UDA problem is divided into two parts, **transduction** of labelling unsupervised data points and **adaptation** of domain shift. I think such intuition is important because we may use this to formalize the problem of domain adaptation. Generally speaking, we are using the representation knowledge of the two domain and the labelling knowledge of the source domain, so it is reasonable to divide the problem in such two terms.

In solving the problem of **adaptation**, the author introduce the concept of cyclic consistency. This concept is quite reasonable only when the two domains are rather similar. Cyclic consistency means that if we learn a estimated label function of the target domain, we can use it to evaluate the source domain and compare it with the supervised knowledge. Like I said, sometimes the two domains are so different that we can not simply assume that the kNN method the author used is concrete.

In terms of the **transduction**, the author uses the method he called as structured consistency, which is just the regulation term of the loss function, using the traditional kNN method.

I think these are the major parts of the algorithm of this paper. The author summarizes his great algorithm very well in the paper. Still, I think the problem here is that he only considered the problem of two similar domains. For problem of different domains such as images and videos, probably his method need some changes. 