_Q: Why use warping for domain adaptation (DA)?_

A: Our warping method works for both supervised learning and domain adaptation settings. In the supervised setting, the gain is smaller since warping enlarges objects, which provides a modest benefit. In the DA setting, the gain is larger because warping also reduces the model's reliance on the background, which is difficult to adapt across domains, while shifting focus to the foreground, which is easier to adapt. This dual effect brings additional performance gains in DA.


<br>


_Q:  Why not apply warping at target or test time?_

A: In unsupervised domain adaptation (UDA), we do not have ground truth labels to guide the warping at target or test time. Interestingly, even when using such labels, we found only small gains. We believe this is because the backbone becomes strong enough from training on warped source images, where ground truth labels are available. As a result, even without warping at target or test time, the model can identify salient regions effectively since it has already learned better feature representations from the warped source domain.