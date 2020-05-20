# ZSL-AttnGAN

### Based on:
https://github.com/EthanZhu90/ZSL_GAN

https://github.com/davidstap/AttnGAN

### Changes
The main idea of this solution is to integrate two methods with as low as possible interference in their architectures.

1. ZSL-GAN may be integrated into AttnGan in several parts of its architecture:
2. ZSL-GAN may be integrated into sentence encoder training to obtain sentence embeddings that not distinguishable from visual features and allows the discriminator model to predict image class.
3. ZSL-GAN may also use word embeddings instead of sentence embedding. This, however, requires implementation of an additional model that will map word sequence to a vector with fixed dimensionality (we don't want to predict all visual features by one word).
Lastly, we can apply ZSL-GAN instead of intermediate generators. This approach has a disadvantage of the previous one and also requires implementation of additional visual features extractors.

As a result, I choose the first approach in order to avoid implementingional models.

In AttnGAN there is so-called Conditioning Augmentation model that transforms basic sentence embedding into a normal distribution. I decided to replace it with ZSL-GAN generator model to retain overall architecture complexity. Another option was to place it between the original sentence embedding and CA model. However, both ZSL-GAN generator and CA model outputs are stochastic. Their placement one after another may lead to unstable learning.

In the original AttnGAN model DAMSM loss applied to pure sentence embedding that should become not distinguishable from visual features. However, in the new model, ZSL-GAN generator target is exactly to produce such not distinguishable embedding. According to that, DAMSM loss now uses ZSL-GAN sentence embedding instead of RNN one.

While training encoders, vanilla AttnGAN uses only DAMSM loss. However, as we integrate ZSL-GAN into AttnGAN, we also have generator and discriminator losses from ZSL-GAN. The idea is to add a generator loss to DAMSM loss with a coefficient. Discriminator loss optimized separately as in ZSL-GAN. Note, that unlike in ZSL-GAN where visual features is given, we also optimize image encoder from AttnGAN with ZSL-GAN losses.

For AttnGAN generator training I also added ZSL-GAN generator loss as an additive term with a coefficient.

In such setup, new model may benefit from joint training instead of two-step training. Also, it may benefit from removing sentence term from DAMSM loss as it forces sentence embedding to be more deterministic.

### Results
[(Poorly) Pretrained models](https://yadi.sk/d/bvngJhLBnANYuw)

Generator output after 5 epoch:
![](https://github.com/ArgentumWalker/ZSL-AttnGAN/blob/master/D_average_6.png)

### Important TODO's
1. Implement more logging. There are no logging at all. Only console output and images that saved every few batches.
