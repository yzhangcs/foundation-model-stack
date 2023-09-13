# Foundation Model Stack

Foundation Model Stack is a collection of components for development, inference, training, and tuning of foundation models leveraging PyTorch native components. For inference optimizations we aim to support PyTorch compile, accelerated transformers, and tensor parallelism. At training time we aim to support FSDP and various parallelism strategies, accelerated transformers, and PyTorch compile.

## Status
* 09/08/2023: Inference on 7B, 13B LLaMa models

## Installation

Requires [PyTorch nightlies](https://pytorch.org/get-started/locally/).

```
pip install -e .
```
or
```
python setup.py install
```

An example of inference on our implementation of LLaMA can be found in `examples/inference.py`.


## Inference

Our approach for inference optimization is to use PyTorch compile, accelerated transformers, and tensor parallelism. PyTorch compile compiles the code into optimized kernels, accelerated transformers leverages `scaled_dot_product_attention` (SDPA) for accelerating attention computation while saving memory, and tensor parallelism is necessary for larger models like LLaMa 70B. In our experiments with various models `torch.compile` has given 2-2.5x speedups for inference, with `SDPA` providing 30-40% improvements.

We initially provide a re-implementation of the LLaMA2 architecture. To enable the model to compile, we reimplement `RoPE` encodings without complex numbers. We have verified that the `forward` pass compiles (there is work that needs to be done for `backward` to work with FSDP).

The figure below shows the latency improvements as we move from eager mode execution to adding SDPA, compile, and SDPA+Compile. The measurements are for 7 and 13B models.

![image (21)](https://github.com/ibm-pytorch/foundation-model-stack/assets/8322403/3d9c6a0f-c3ef-454b-806c-271f352afa4d)


Tensor parallel inference numbers for 13B and 70B models are **coming soon**!

## Training (Coming Soon!!)

## Open Issues
* https://github.com/pytorch/pytorch/issues/108780 requires adding graph breaks to preserve accuracy.
* https://github.com/pytorch/pytorch/issues/107824 prevents training/finetuning from working

## References

* Huggingface TGI: https://github.com/huggingface/text-generation-inference
* IBM TGIS: https://github.com/IBM/text-generation-inference