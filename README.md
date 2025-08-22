# Introduction

**Early Preview. DO NOT USE in production.**

The Package URL (PURL) specification, like many technical standards, is designed primarily for human understanding, offering concise examples and minimal attribute descriptions sufficient for manual interpretation. However, this level of detail falls short when applied to machine learning, where models require explicit parsing logic, validation rules, edge cases, and both valid and invalid examples to effectively learn patterns.

For instance, take the below list of examples shown for the [npm](https://github.com/package-url/purl-spec/blob/373482246a06e86b9123d1d5ed75f2ea306e228d/types/npm-definition.json#L27) purl type.

```json
"examples": [
  "pkg:npm/foobar@12.3.1",
  "pkg:npm/%40angular/animation@12.3.1",
  "pkg:npm/mypackage@12.4.5?vcs_url=git://host.com/path/to/repo.git%404345abcd34343"
]
```

While humans can intuitively grasp the structure of sample PURLs such as those for npm packages, machines need structured, granular explanations to understand what makes a PURL valid or invalid. To address this gap, this repository proposes the creation of byte-sized, focused training datasets that enhance the existing specification with machine-consumable insights—without replacing the original documentation. These datasets, available in multiple machine-readable formats, aim to support small to medium ML models and LLM engineers by providing lightweight, ready-to-use content that facilitates accurate PURL parsing and validation, reducing the need for extensive data labeling and annotation efforts.

## Process

Enhancing the specification with machine-readable details requires careful attention to syntax, semantics, and edge cases, making it a labor-intensive task when done manually. To streamline this process, we evaluated four frontier language models—Gemini 2.5 Pro, GPT-5 Thinking, Qwen3 Coder, and GLM-4.5—to assess their ability to generate accurate, detailed, and structured enhanced specification from the original PURL specification. Our initial effort, published as `cdx-docs`, leveraged Gemini 2.5 Pro, which produced acceptable results for larger, more capable models but lacked the granularity required to effectively train smaller or quantized models. These models often need more explicit reasoning, validation rules, and failure examples to achieve reliable performance. Among the models tested, GLM-4.5 demonstrated superior comprehension of the specification’s nuances, generating more precise parsing logic, clearer validity criteria, and better-structured illustrative examples—key attributes essential for ML readiness. The prompt used to guide this enhancement process is [available](./PROMPT.md) in the repository, ensuring transparency and reproducibility of the methodology.

The enhanced data generated through this process is openly available for community review and collaboration. We encourage contributions from practitioners, specification experts, and ML engineers to help refine and improve the quality of the dataset. If you identify any inaccuracies, edge cases that need clarification, or opportunities for improvement, please feel free to submit a pull request or open an issue in the repository. Your feedback is invaluable in ensuring the accuracy, completeness, and utility of this resource for the broader machine learning and developer community.

## License

MIT
