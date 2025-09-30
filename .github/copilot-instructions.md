# GitHub Copilot Instructions for BitNet-OG

## Project Overview
BitNet-OG (bitnet.cpp) is the official inference framework for 1-bit LLMs (e.g., BitNet b1.58). It provides optimized kernels for fast and lossless inference of 1.58-bit models on CPU and GPU.

## Key Technologies
- **Languages**: C++, Python, CMake
- **Dependencies**: llama.cpp framework, GGUF format
- **Platforms**: ARM (TL1 kernels), x86 (TL2 kernels)

## Code Structure
- `src/`: Core C++ implementation for BitNet inference
- `utils/`: Python utilities for model conversion and code generation
- `gpu/`: GPU-specific inference implementation
- `include/`: Header files and generated kernel code
- `3rdparty/`: Third-party dependencies (llama.cpp)
- `preset_kernels/`: Pre-tuned kernel parameters for different models

## Important Conventions

### Coding Style
- Follow existing C++ conventions in the codebase
- Use descriptive variable names (e.g., `n_embd` for embedding dimension, `n_ctx` for context length)
- Maintain consistency with llama.cpp framework patterns
- Python code follows PEP 8 style guidelines

### Key Concepts
- **Quantization types**: `i2_s` (2-bit symmetric), `tl1` (Table Lookup 1), `tl2` (Table Lookup 2)
- **GGUF format**: Model serialization format used for inference
- **Kernel shapes**: Matrix multiplication dimensions (M, K) tuned per model
- **LUT (Lookup Table)**: Core methodology for efficient 1-bit computation

### Model Support
- BitNet b1.58 models (large, 3B, 2B-4T variants)
- Llama3-8B-1.58-100B-tokens
- Models must be converted from safetensors to GGUF format

## Build System
- CMake-based build system
- Platform-specific compiler flags (ARM TL1 vs x86 TL2)
- Requires clang/clang++ compiler
- Generated kernels stored in `include/bitnet-lut-kernels.h`

## Important Files
- `setup_env.py`: Environment setup, model download, conversion, and compilation
- `run_inference.py`: CLI for running inference
- `utils/convert.py`: Model conversion from HuggingFace to GGUF
- `utils/codegen_tl1.py`: Kernel code generation for ARM/x86

## Testing & Development
- Build using `python setup_env.py` with appropriate flags
- Test inference with `python run_inference.py`
- Kernel parameters can be pre-tuned or auto-generated
- Log files stored in `logs/` directory

## Common Patterns

### Model Loading
```python
# Models are downloaded from HuggingFace and converted to GGUF
hf_url = "1bitLLM/bitnet_b1_58-3B"
model_dir = "models/bitnet_b1_58-3B"
gguf_path = "models/bitnet_b1_58-3B/ggml-model-i2_s.gguf"
```

### Kernel Generation
```python
# Kernel shapes defined per model architecture
ModelShapeDict = {
    "bitnet_b1_58-3B": [[3200, 8640], [3200, 3200], [8640, 3200]]
}
```

### Quantization
```python
# Per-tensor quantization with lookup tables
void per_tensor_quant(int k, void* lut_scales_, void* b_)
```

## Best Practices for Contributors
1. Test on both ARM and x86 platforms when modifying kernels
2. Maintain compatibility with llama.cpp upstream
3. Document new model support in README.md
4. Use pre-tuned kernels when available for better performance
5. Follow existing patterns for model conversion and inference
6. Add appropriate error handling and logging

## Performance Considerations
- Kernel parameters (BM, BK, bm) significantly impact performance
- Pre-tuned kernels in `preset_kernels/` are optimized per model
- Memory alignment is critical (64-byte alignment for ARM)
- Use appropriate quantization type for target platform

## Resources
- [Technical Report](https://arxiv.org/abs/2410.16144)
- [llama.cpp Framework](https://github.com/ggerganov/llama.cpp)
- [T-MAC Methodology](https://github.com/microsoft/T-MAC/)
- [BitNet Papers](https://arxiv.org/abs/2402.17764)
