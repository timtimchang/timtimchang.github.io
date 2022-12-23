# Adafactor: Adaptive Learning Rates with Sublinear Memory Cost
> 自從GPT、BERT等pre-train流行起來後，其中一個明顯的趨勢是model越做越大，因為更大的model配合更充分的pre-train通常能更有效地刷榜，以下是一些範例模型大小。
> VGG最大版本有1.3億參數
> GPT2最大的版本有15億參數
> T5最大的版本有110億參數
###### tags: `paper explore`
## Abstract
For the case of neural network weight matrices:
1. We propose maintaining only the **per-row and per-column** sums of these moving averages, and estimating the **per-parameter second moments** based on these sums. We demonstrate empirically that this method produces similar results to the baseline.
2. We show that adaptive methods can produce larger-than-desired updates when the decay rate of the second moment accumulator is too slow. We propose **update clipping** and a **gradually increasing decay rate** scheme as remedies. (and **dropping momentum**) 
4. We propose **scaling the parameter updates** based on the scale of the parameters themselves.

## A Brief Review of Optimizer
### SGD-準確率梯度下降法 (stochastic gradient decent)
SGD 也就是最單純的gradient decent 方法，找出參數的梯度，往梯度的方向去更新參數。
![](https://i.imgur.com/YQr2Yun.png)
,with W 為權重(weight)參數，L 為損失函數(loss function)， η 是學習率(learning rate)
![](https://i.imgur.com/7p0omEw.png)

### Momentum
此優化器為模擬動量的概念，在同方向的維度上學習速度會變快，方向改變的時候學習速度會變慢。
![](https://i.imgur.com/TGfRWvf.png)
,with β為參數
![](https://i.imgur.com/EYvZ8DI.png)

### AdaGrad
前面幾種Optimizer的learning rate η，都為固定值，而AdaGrad就是會依照梯度去調整 η
![](https://i.imgur.com/wRvyBD8.png)
,with ϵ 極小參數
![](https://i.imgur.com/Y1nwwsr.png)

### RMSProp
AdaGrad後期梯度較大的時候，n較大，能夠約束學習率，但分母上梯度平方的累加會越來越大，會使梯度趨近於0，訓練便會結束，為了防止這個情況，後面有開發出 RMSprop Optimizer，在學習率調整上面多了一個參數α，可以在新舊梯度上面做調節
![](https://i.imgur.com/jIBiFgS.png)
,with α 為參數
![](https://i.imgur.com/z1kwgED.png)


### Adam
Adam 保留了 Momentum 對過去梯度的方向做梯度速度調整與RMSProp對過去梯度的平方值做learning rate的調整，再加上Adam有做參數的”偏離校正”，使得每一次的學習率都會有個確定的範圍，會讓參數的更新較為平穩(by Exponential Moving Average)。
![](https://i.imgur.com/WcPypGo.png)
![](https://i.imgur.com/xEBNQCM.png)
![](https://i.imgur.com/s2SvKJC.png)
![](https://i.imgur.com/XNvdtLW.png)


> 計算量和記憶體的大頭肯定都是在gradient；除此之外，記憶體的消耗主要是m , v了，我們要維護兩組變量，來滑動計算梯度的前兩階矩（也就是m和v），用以計算參數的更新量。這兩組變量每一組都跟訓練參數本身一樣大，因此對於參數比較多的模型，兩組緩存變量所消耗的顯存也不少。

### Adafactor
1. **No Momentum**: 
> CV模型很多時候要靠“SGD+動量”來煉出最優效果來，自適應學習率優化器通常訓練不出最好的效果。但對於NLP模型來說，情況有點相反，自適應學習率顯得更重要一些，很少聽到由純靠SGD調NLP模型的案例。
2. **Factored Second Moment Estimation**
Low-rank approximation matrices
$$AB \approx C$$
,where $A$ is $m \times  1$, $B$ is $1 \times  n$, $C$ is $m \times  n$ 
- This is known to give the optimal projection onto the space of rank-k matrices with respect to the Frobenius norm. (x)
![](https://i.imgur.com/2r9oYNB.png)
![](https://i.imgur.com/dNh5ukO.png)

-  Another popular cost function in the literature is the generalized Kullback-Leibler divergence,also known as the I-divergence.(o) 
![](https://i.imgur.com/h2VPgP1.png)
3.  **Increasing Decay Parameter**:
In Adam:
![](https://i.imgur.com/3fazE1b.png)
![](https://i.imgur.com/eq9Ofbt.png)
Proposed Alternative:
![](https://i.imgur.com/X9k5z6F.png)
with 0<$c$<1 (c = 0.8 in Adafactor default)

4. **Update Clipping**
Reddi et al. (2018) discuss non-convergence issues when using a fast decay of the second-moment estimator (low β2). 
![](https://i.imgur.com/fE59ano.png)

observe the root-mean-square over all parameters x for second-moment estimator
![](https://i.imgur.com/qs4cyZo.png)
![](https://i.imgur.com/fbwV9RI.png)
We define the clipped unscaled update $\tilde{U}_{t}$ as:
![](https://i.imgur.com/Jz1qptq.png)



5. **Relative Step Size**
get multiplied by the scale of the parameters

## Adafactor Algorithm
![](https://i.imgur.com/l9I39dG.png)



### Result
**BLEU：bilingual evaluation understudy**
![](https://i.imgur.com/BpdrGgJ.png)
**visual result**
https://github.com/jettify/pytorch-optimizer

ref : 
**Adfactor **
https://arxiv.org/pdf/1804.04235.pdf
Adam: https://arxiv.org/pdf/1412.6980.pdf
https://kexue.fm/archives/7302
https://juejin.cn/post/6844903600943005710
