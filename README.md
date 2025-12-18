# Shortcut Learning in Vision Models

## When Accuracy Lies

Modern computer vision models often achieve impressive performance on standard benchmarks. However, growing evidence suggests that such performance can mask a fundamental issue: models may not be learning the intended visual concepts.

Rather than recognising objects based on their semantic structure, models can exploit shortcuts — spurious correlations in the training data that enable high accuracy without genuine understanding.

This project investigates shortcut learning in vision models by intentionally introducing bias into the training distribution and evaluating model behaviour under counterfactual conditions.

---

## What Is Shortcut Learning?

Shortcut learning occurs when a model relies on easily learnable correlations instead of the causal features required to solve a task correctly.

In natural image datasets, such shortcuts commonly arise from:
- Background context (e.g., boats frequently appearing on water)
- Colour distributions
- Co-occurring objects
- Dataset collection artefacts

When these correlations are stable, a model can perform well on benchmarks while failing to learn object-level representations.

---

## Experimental Design

### Dataset

- **CIFAR-10**, consisting of 10 object classes represented by 32×32 RGB images.

### Biased Training Distribution

To explicitly encourage shortcut learning, systematic background colour biases were introduced during training:

- Airplanes were tinted sky-blue  
- Ships were tinted deep water-blue  
- Deer were tinted green  
- Horses were tinted brown  

All training images for these classes were modified, making background colour a highly predictive feature.

The objective was to allow the model to succeed without learning object shape or semantic structure.

---

## Counterfactual Evaluation

To assess whether the model learned object semantics or shortcuts, evaluation was performed on **counterfactual test sets**.

In these tests:
- The object itself remains unchanged
- The background colour is deliberately altered

Examples include:
- Ships appearing on sand-coloured backgrounds
- Airplanes appearing on non-sky colours

If the model had learned object-level features, performance would be expected to remain stable under these interventions.

---

## Global Accuracy: A Misleading Signal

At an aggregate level, the model appears to perform reasonably:

- **Clean test accuracy:** ~46.7%  
- **Counterfactual test accuracy:** ~50.4%  

At first glance, this suggests robustness.  
However, aggregate accuracy obscures severe class-specific failure modes.

---

## Per-Class Performance Reveals Shortcut Learning

When performance is analysed at the class level, a markedly different pattern emerges.

For several biased classes — particularly ships, deer, and horses — accuracy is near zero even on clean test images. Counterfactual testing does not substantially degrade performance because object-level representations were never learned.

In contrast, airplane performance improves under counterfactual conditions, indicating that background colour acted as a misleading cue rather than a useful signal.

This behaviour is characteristic of shortcut learning.

---

## Where Is the Model Looking? (Grad-CAM Analysis)

Grad-CAM was used to visualise spatial attention under both clean and counterfactual conditions.

Consistent patterns emerge:
- Saliency maps emphasise background regions
- Object boundaries receive limited attention
- Prediction confidence remains high even when predictions are incorrect

Rather than attending to object structure, the model focuses on contextual regions associated with training shortcuts.

---

## Are the Explanations Faithful? (Deletion Curves)

To evaluate whether explanations were causally meaningful rather than merely illustrative, deletion experiments were conducted:

- Pixels ranked highest by Grad-CAM were progressively removed
- Prediction confidence was measured after each deletion step

Results show that:
- Removing background-associated pixels leads to sharp confidence drops
- Removing object pixels often has limited effect
- Different misclassifications follow distinct degradation trajectories

This confirms that the explanations correspond to features the model genuinely relies upon.

---

## Why Shortcut Learning Matters

Shortcut learning is not limited to benchmark datasets.

In real-world systems:
- Medical imaging models may learn scanner artefacts rather than pathology
- Surveillance systems may amplify contextual bias
- Fairness failures can emerge when shortcuts correlate with demographic attributes

A model that succeeds for the wrong reasons is inherently fragile under distribution shift.

---

## Key Takeaways

- High accuracy does not imply semantic understanding
- Shortcut learning can entirely replace object learning
- Counterfactual evaluation is essential for robust assessment
- Explanations must be tested, not accepted at face value

---

## Reproducibility

- Dataset: CIFAR-10  
- Model: Convolutional neural network trained under biased distributions  
- Environment: PyTorch, Jupyter / Colab  

