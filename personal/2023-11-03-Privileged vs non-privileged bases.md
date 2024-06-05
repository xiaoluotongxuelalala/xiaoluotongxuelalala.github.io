本文来源于[此链接](https://harrisonpim.com/blog/privileged-vs-non-privileged-bases-in-machine-learning)，若有侵权，请联系删除。

简单理解：privileged basis是指在训练模型时，模型的输入数据中包含了一些额外的信息，这些信息可以帮助模型更好的学习到数据的特征，从而提高模型的性能。而non-privileged basis则是指模型的输入数据中不包含任何额外的信息，模型只能从输入数据中学习到数据的特征，从而提高模型的性能。这些额外的信息往往是人为指定（或者构造）而形成的，比如特定的模型架构（CNN）。然后rotation就是去旋转这些basis，看看模型的性能是否会受到影响，如果受到影响，那么这些basis就是privileged basis，反之则是non-privileged basis。

## What's a basis?
A basis is the set of building-block vectors which we can use to represent any other vector in a particular space.

Basis vectors should be independent of one another, ie we should not be able to express any of the vectors in the set using a combination of the others.

For example, in a 2D vector space, the vectors [1, 0] and [0, 1] form a basis, because any other vector in the space can be expressed as a linear combination of them, and it's not possible to express one with the other.

For example, the vector [2, 3] can be represented as:

[2, 3] = 2[1, 0] + 3[0, 1]

The coefficients 2 and 3 represent how much of each basis vector is needed to form the original vector.

We could also use the vectors [1, 1] and [-1, 1] form an alternative 2D basis, allowing us to express the original vector differently:

[2, 3] = 2[1, 1] + (-1)[-1, 1]

Bases are important because they allow us to represent complex data, like the features of images or text, as collections of simpler components. The choice of basis can have a significant impact on the ability of an algorithm to learn patterns and relationships within the data. By choosing an appropriate basis, we can simplify the representation of the data and make it easier for an algorithm to extract meaningful features.

## Privileged bases
In certain problem settings, we can make architectural choices which deliberately bias a model to organise its internal representation of certain features along its basis dimensions. These are often called inductive biases. In these cases, those dimensions (or groups of dimensions) will develop specific importance or meaning through the training process. We call these bases privileged.

For example, in a convolutional neural network, convolutional layers are explicitly designed to learn local spatial patterns in images such as edges, corners, and textures. The neurons of these layers are constrained to the nearby regions of the input, which encourages the learning of spatially localised features that align with the basis dimensions.

For example, suppose that a CNN is trained to classify images of cats and dogs. If the learned features do not align with the basis dimensions that detect features relevant to cats and dogs, such as whiskers or ears, then the network may not be able to effectively distinguish between the two classes of images. In contrast, if the learned features align with the basis dimensions, then the network can effectively capture the desired patterns in the input and achieve high classification accuracy.

Similarly, the multi-head attention mechanism in a transformer model is designed such that different heads will attend to different aspects of the input sequence, promoting feature alignment.

Regularisation methods like weight decay, dropout, or batch normalisation can also add constraints to the learning process, controlling the model complexity or reducing overfitting. These constraints impose yet more priors on the model, implicitly pushing it to learn features that align with the basis dimensions.

## Non-privileged bases
In the creation of a non-privileged basis, the model is given no access to privileged information, and the optimiser is given no incentive to align its basis dimensions with a particular type of feature. Instead, the available dimensions are treated as equally important, and the model is free to distribute information equally across them.

For example, in an embedding space generated by a process like word2vec, each word is represented as a high-dimensional vector, with each dimension corresponding to a learned feature of the word. Rather than being deliberately engineered to represent specific linguistic features, the basis vectors are distributed based solely on the relationships of words to one other in the space, regardless of how that vector space itself is arranged.

The distribution of basis vectors in the space is driven wholly by the optimiser, rather than by engineering specific dimensions to capture specific aspects of the data.

In cases where the space is larger than the information being encoded, this can result in information being represented redundantly and non-orthogonally across many dimensions. This can make model with non-privileged bases more robust to noise and variations, at the expense of interpretability.

## Rotation
Apart from considering the priors in an algorithm's architecture or its training data, the easiest way to determine whether a basis is privileged is to consider the effect of a rotation on algorithmic performance.

In a non-privileged basis, rotation should have no impact on the effectiveness of the vectors in a model, because their features are not aligned with any specific basis dimension.

In contrast, in a privileged basis, such as in a CNN or transformer MLP, the basis dimensions are designed to capture specific types of features, and there is an incentive for the learned features to align with these dimensions. Rotating the basis would change the orientation of the basis dimensions relative to the learned features. In almost all cases, this misalignment will degrade the model's performance.

For example, a tiger-or-not-tiger model might contain a filter which is trained to capture orange stripes in the input data. Rotating this filter to detect blue stripes instead would make this feature much less useful!

## Practical implications
It's useful to consider the kind of basis you're working with when training or using a new machine learning model.

If you're using a pre-trained network to extract features from data, knowing whether it will produce vectors in a privileged / non-privileged basis should make its results much more useful and interpretable.

When training models for tasks where interpretability is key, or where specific features are expected to be strong predictors, a privileged basis with strong architectural inductive biases may be appropriate. In contrast, for tasks where the features are less well-defined or where robustness to noise and variations is more important, leaving models open to define a non-privileged basis themselves might be the better choice.