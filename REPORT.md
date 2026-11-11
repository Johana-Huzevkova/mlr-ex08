# Task 4 - Report

## 1. Experiment setup

The goal was to improve the leaf classifier for phone camera use.

The baseline was the original Task 3 LeafNet network without modification.

The dataset contained 107 training and 29 validation images across four classes: apple, cherry, chestnut and maple. All images were 96x96 pixels.

All variants used the same data split, 15 epoch, Adam (lr = 0.001), batch size 32 and CrossEntropyLoss. Each experiment was repeated with seeds 0, 1 and 2.

Two validation sets were used:

* normal validation: original 29 validation images.
* camera-like validation: the same images randomly cropped, rotated by up to +- 30° and color-adjusted. This set was generated once with seed 42 and reused for all experiments.

One validation image represents approximately 3.4 percentage points, so results from individual runs can be noisy.

## 2. Experiments and Results

Results:

* Following table shows mean final-epoch accuracy over three seeds


| Experiment | Modification | Normal val | Camera-like val |
|---|---|---|---|
| 4b | baseline LeafNet | 65.5 % | 39.1 % |
| 4c | Geometric augmentation | 56.3 % | 47.1 % |
| 4d | batch normalization | 74.7 % | 46.0 % |
| 4e | augmentation + batch normalization | 66.7 % | 49.4 % |
| 4f | + ColorJitter | 60.9 % | 59.8 % |
| 4g | MobileNet,full fine-tuning | 62.1 % | 58.6 % | 
| 4h | MobileNet, frozen backbone | 64.4 % | 60.9 % |
| 4i | Frozen MobileNet + color and geometric augmantation | 73.6 % | 74.7 % |

Findings:

* geometric augmentation alone: decreased normal accuracy and provided only a small camera-like improvement
* batch normalization: improved normal accuracy to 74.7 %, but did not significantly improve camera robustness.
* Augmantation + batch normalization: improved camera-like accuracy slightly, but was worse than batch normalization alone on normal validation.
* ColorJitter: substantially improved camera-like accuracy, reaching 59.8 %, but reduced normal accuracy
* Full MobileNet fine-tuning: performed worse than the frozen version. Training all pretrained parameters on such a small dataset was less effective.
* Frozen MobilNet: improved camera-like accuracy to 60.9 % while training only the new classification head
* Final combination: frozen MobileNet with color and geometric augmentation achieved the best mean accuracy on camera-like by a wide margin, second on normal.



## 3. Reliability Across Random Seed

Importamnt experiments were repeated with three seeds.

| Model | Normal accuracy range | Camera-like accuracy range |
| ---- | ---- | ---- |
| baseline | 55.2-72.4 % | 37.9-41.4 % |
| final model | 62.1-79.3 % | 72.4-75.9 % |

The camera-like accuracy do not overlap. Even the worst final-model run performed better than every baseline run.

on normal validation, the ranges overlap, so the improvement is less conclusive. The results suggest that the final model's main advantage is robustness to camera-like image changes.

## 4. Final Model Selection

I selected experiment 4i: pretrained MobileNetV3-Small with a frozen backbone, a new trainable classification head and color and geometric augemntation.

The selected best checkpoin achieved:

* normal validation accuracy: 79.3 %
* camera-like validation accuracy: 75.9 %

The model was exported as `leaf_best_model.onnx` with a size of 6.3 MB. It uses the same 96x96 input size and preprocessing metadata as the existing phone model.

## 5. Conclusion and Limitations

The best results came from combining transfer learning with a frozen MobileNet backbone and color and geometric augmentation. Compared with the baseline, mean camera-like accuracy increased from 39.1 % to 74.7 %, an improvement of 35.6 percentage points.

Not all modifications helped. Geometric augmentation alone reduced normal accuracy, batch normalization did not improve camera robustness sufficiently, and fill fine-tuning was less effective than freezing the pretrained backbone.

The camera-like validation set is synthetic and does not fully represent real phone photos. The validation set is also small (29 images), so results can vary between seeds. The experiments were repeated three times to reduce the influence of random variation.

Further improvements could include a learning-rate scheduler, more training epochs and additional historical photos.

I would deploy experiment 4I because it achieved the best overall results, particularly on camera-like images, while keeping the model size practically unchanged.













