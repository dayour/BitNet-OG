# Contributing to BitNet-OG

Thank you for your interest in contributing to BitNet-OG! This document provides guidelines for contributing to the project.

## Table of Contents
- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [How to Contribute](#how-to-contribute)
- [Coding Standards](#coding-standards)
- [Testing](#testing)
- [Pull Request Process](#pull-request-process)

## Code of Conduct

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). Please read [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) for details.

## Getting Started

BitNet-OG is an inference framework for 1-bit LLMs. Before contributing, please:

1. Read the [README.md](README.md) to understand the project
2. Review the [technical report](https://arxiv.org/abs/2410.16144)
3. Familiarize yourself with the codebase structure
4. Check existing [issues](https://github.com/dayour/BitNet-OG/issues) and [pull requests](https://github.com/dayour/BitNet-OG/pulls)

## Development Setup

### Prerequisites
- Python 3.8 or higher
- CMake 3.14 or higher
- Clang/Clang++ compiler
- Git with submodules support

### Environment Setup

1. Clone the repository with submodules:
```bash
git clone --recursive https://github.com/dayour/BitNet-OG.git
cd BitNet-OG
```

2. Install Python dependencies:
```bash
pip install -r requirements.txt
```

3. Build the project:
```bash
python setup_env.py --hf-repo 1bitLLM/bitnet_b1_58-3B
```

### Architecture-Specific Notes
- **ARM platforms**: Use TL1 kernels (default on ARM CPUs)
- **x86 platforms**: Use TL2 kernels (default on x86 CPUs)
- Pre-tuned kernels are available in `preset_kernels/` directory

## How to Contribute

### Reporting Bugs
- Use the [bug report template](.github/ISSUE_TEMPLATE/bug_report.md)
- Include system information (OS, architecture, compiler version)
- Provide steps to reproduce the issue
- Include relevant logs from the `logs/` directory

### Suggesting Enhancements
- Use the [feature request template](.github/ISSUE_TEMPLATE/feature_request.md)
- Clearly describe the proposed feature
- Explain the use case and benefits
- Consider performance implications

### Adding Model Support
When adding support for new models:
1. Add model configuration to `setup_env.py` in `SUPPORTED_HF_MODELS`
2. Define kernel shapes in `utils/codegen_tl1.py` in `ModelShapeDict`
3. Test conversion and inference on target platforms
4. Update `README.md` with model details
5. Consider adding pre-tuned kernels if performance-critical

## Coding Standards

### Python Code
- Follow PEP 8 style guidelines
- Use descriptive variable names
- Add docstrings for functions and classes
- Handle errors gracefully with appropriate logging

Example:
```python
def prepare_model(model_dir: str, quant_type: str) -> str:
    """
    Prepare model for inference by converting to GGUF format.
    
    Args:
        model_dir: Directory containing the model files
        quant_type: Quantization type (e.g., 'i2_s', 'tl1')
    
    Returns:
        Path to the GGUF model file
    """
    # Implementation here
```

### C++ Code
- Follow existing code style in the repository
- Use meaningful variable names (e.g., `n_embd`, `n_ctx`)
- Maintain compatibility with llama.cpp conventions
- Add comments for complex algorithms
- Ensure proper memory management and alignment

Example:
```cpp
// Per-tensor quantization using lookup tables
void per_tensor_quant(int k, void* lut_scales_, void* b_) {
    bitnet_float_type* lut_scales = (bitnet_float_type*)lut_scales_;
    // Implementation here
}
```

### CMake
- Follow existing CMakeLists.txt patterns
- Document new options and dependencies
- Maintain cross-platform compatibility

## Testing

### Building and Testing
1. Build the project:
```bash
python setup_env.py --hf-repo [model-repo]
```

2. Test inference:
```bash
python run_inference.py -m [model-path] -p "Your test prompt"
```

3. Verify on multiple platforms when possible (ARM and x86)

### Performance Testing
- Use appropriate kernel parameters for your hardware
- Compare with baseline performance
- Document performance improvements in PR description

## Pull Request Process

1. **Fork and Branch**: Create a feature branch from `main`
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make Changes**: Implement your changes following coding standards

3. **Test**: Verify your changes work correctly
   - Build successfully on target platforms
   - Run inference tests
   - Check for memory leaks or errors

4. **Commit**: Write clear commit messages
   ```
   Add support for new model architecture
   
   - Add model configuration to setup_env.py
   - Define kernel shapes for new architecture
   - Update documentation
   ```

5. **Push and Create PR**: Push to your fork and create a pull request
   - Fill out the PR template completely
   - Reference related issues
   - Include test results
   - Describe performance impact if applicable

6. **Code Review**: Address review feedback promptly
   - Be responsive to comments
   - Make requested changes
   - Update PR description as needed

7. **Merge**: Once approved, maintainers will merge your PR

### PR Checklist
- [ ] Code follows project style guidelines
- [ ] Changes are tested on relevant platforms
- [ ] Documentation is updated (README, docstrings, etc.)
- [ ] Commit messages are clear and descriptive
- [ ] PR description explains changes and impact
- [ ] Related issues are referenced

## Additional Resources

- [llama.cpp Documentation](https://github.com/ggerganov/llama.cpp)
- [BitNet Paper](https://arxiv.org/abs/2402.17764)
- [Technical Report](https://arxiv.org/abs/2410.16144)
- [T-MAC Project](https://github.com/microsoft/T-MAC/)

## Questions or Need Help?

- Open an [issue](https://github.com/dayour/BitNet-OG/issues) for questions
- Review existing issues and discussions
- Check the [FAQ in README](README.md#faq-frequently-asked-questions)

Thank you for contributing to BitNet-OG!
