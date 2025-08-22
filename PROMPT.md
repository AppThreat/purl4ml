You are an AI tasked with improving the package url jsonschema specification to make it more machine-learning friendly. Below are new attributes that can be added:

The enhanced npm PURL JSON schema includes several attributes specifically designed to optimize understanding and reduce errors for both traditional parsers and modern AI/ML systems. Here's why each addition provides significant value:

1. uri_compliance - Explicit Encoding Rules for Deterministic Parsing
Provides clear, unambiguous encoding requirements that eliminate guesswork for automated systems. ML models can reference these explicit rules rather than inferring encoding patterns from examples, reducing hallucination about URL-safe transformations.
2. validation_rules - Programmatic Compliance Checking
Offers concrete validation patterns and examples that serve as training data for ML models. The explicit valid/invalid examples create clear decision boundaries, helping models learn the precise boundaries of acceptable formats.
3. common_patterns and anti_patterns - Positive and Negative Training Examples
These provide essential contrastive learning signals. ML models learn not just what is correct, but crucially, what is incorrect. This dual approach significantly improves classification accuracy and reduces false positives.
4. parsing_decision_tree - Step-by-Step Logic for Complex Parsing
Breaks down complex parsing decisions into simple, sequential steps that mirror how humans process PURLs. This structured approach helps ML models (especially smaller ones) handle complex nested conditions without losing context, reducing hallucination in multi-step reasoning.
5. context - Ecosystem Understanding for Semantic Accuracy
Provides crucial background knowledge that helps ML models understand the "why" behind rules, not just the "what." This contextual understanding prevents models from making technically correct but semantically inappropriate suggestions (e.g., suggesting invalid npm practices that might be valid in other ecosystems).
6. edge_cases - Handling Real-World Complexity
Documents nuanced scenarios that often cause confusion. ML models trained on these edge cases become more robust and less likely to confidently provide incorrect answers about boundary conditions, which are common sources of hallucination.
7. llm_guidance - Direct Instruction for Language Models
Provides explicit meta-instructions that help LLMs avoid common pitfalls. The "do_not_assume" section is particularly valuable as it directly counters common hallucination patterns, while "key_facts_to_remember" reinforces critical constraints.

An example of existing specification for the npm type:

```
{
  "$schema": "https://packageurl.org/schemas/purl-type.schema-1.0.json",
  "$id": "https://packageurl.org/types/npm-definition.json",
  "type": "npm",
  "type_name": "Node NPM packages",
  "description": "PURL type for npm packages.",
  "repository": {
    "use_repository": true,
    "default_repository_url": "https://registry.npmjs.org/",
    "note": "The default repository is the npm Registry at https://registry.npmjs.org"
  },
  "namespace_definition": {
    "requirement": "optional",
    "case_sensitive": false,
    "native_name": "scope",
    "note": "The namespace is used for the scope of a scoped NPM package. The npm scope @ sign prefix is always percent encoded, as it was in the early days of npm scope."
  },
  "name_definition": {
    "case_sensitive": false,
    "native_name": "name",
    "note": "Per the package.json spec, new package 'must not have uppercase letters in the name', therefore the name must be lowercased. The npm name used to be case sensitive in the early days for some old packages."
  },
  "version_definition": {
    "case_sensitive": true,
    "native_name": "version"
  },
  "examples": [
    "pkg:npm/foobar@12.3.1",
    "pkg:npm/%40angular/animation@12.3.1",
    "pkg:npm/mypackage@12.4.5?vcs_url=git://host.com/path/to/repo.git%404345abcd34343"
  ]
}
```

The new improved ml-friendly version is below:

```
{
  "$schema": "https://packageurl.org/schemas/purl-type.schema-1.0.json",
  "$id": "https://packageurl.org/types/npm-definition.json",
  "type": "npm",
  "type_name": "Node NPM packages",
  "description": "PURL type for npm packages.",
  "uri_compliance": {
    "namespace_encoding": "percent-encoded",
    "name_encoding": "lowercase",
    "version_encoding": "case-sensitive"
  },
  "repository": {
    "use_repository": true,
    "default_repository_url": "https://registry.npmjs.org/",
    "supported_repositories": [
      "https://registry.npmjs.org/",
      "https://npm.pkg.github.com/",
      "https://registry.yarnpkg.com/"
    ],
    "note": "The default repository is the npm Registry at https://registry.npmjs.org"
  },
  "namespace_definition": {
    "requirement": "optional",
    "case_sensitive": false,
    "encoding": "percent-encoded",
    "native_name": "scope",
    "note": "The namespace is used for the scope of a scoped NPM package. The npm scope @ sign prefix is always percent encoded, as it was in the early days of npm scope."
  },
  "name_definition": {
    "requirement": "required",
    "case_sensitive": false,
    "encoding": "lowercase",
    "native_name": "name",
    "note": "Per the package.json spec, new package 'must not have uppercase letters in the name', therefore the name must be lowercased. The npm name used to be case sensitive in the early days for some old packages."
  },
  "version_definition": {
    "requirement": "optional",
    "case_sensitive": true,
    "encoding": "verbatim",
    "native_name": "version"
  },
  "validation_rules": {
    "namespace": {
      "encoding_required": "%40",
      "examples_valid": ["%40angular", "%40myorg"],
      "examples_invalid": ["@angular", "%40Angular", "%40org name"]
    },
    "name": {
      "min_length": 1,
      "no_uppercase": true,
      "url_safe_required": true,
      "examples_valid": ["lodash", "react", "my-package_1"],
      "examples_invalid": ["React", "my package", "my@package"]
    }
  },
  "common_patterns": [
    {
      "pattern": "pkg:npm/{name}@{version}",
      "description": "Standard unscoped package",
      "example": "pkg:npm/lodash@4.17.21"
    },
    {
      "pattern": "pkg:npm/%40{scope}/{name}@{version}",
      "description": "Standard scoped package with encoded scope",
      "example": "pkg:npm/%40babel/core@7.22.0"
    }
  ],
  "anti_patterns": [
    "pkg:npm/@scope/package",
    "pkg:npm/Package@1.0.0",
    "pkg:npm/package@",
    "pkg:npm/",
    "pkg:npm"
  ],
  "parsing_decision_tree": {
    "steps": [
      {
        "question": "Does the package have a scope?",
        "yes": "Look for %40 at the beginning of the name portion",
        "no": "Proceed with unscoped package parsing"
      },
      {
        "question": "Is there a version component?",
        "yes": "Parse everything after the last @ as version",
        "no": "Version is optional"
      },
      {
        "question": "Are there qualifiers?",
        "yes": "Parse key=value pairs after ?",
        "no": "No qualifiers present"
      }
    ]
  },
  "context": {
    "ecosystem": "Node.js",
    "package_manager": "npm",
    "url_scheme": "pkg",
    "hierarchical_structure": "pkg:npm/[scope/]{name}@{version}?{qualifiers}",
    "backward_compatibility": "Supports legacy case-sensitive names for old packages",
    "forward_compatibility": "New packages must follow strict lowercase rules"
  },
  "edge_cases": [
    {
      "case": "Legacy packages with uppercase names",
      "handling": "While new packages must be lowercase, existing packages in registries may have mixed case",
      "purl_impact": "PURLs should normalize to lowercase for consistency"
    },
    {
      "case": "Scoped packages with dots/underscores",
      "handling": "Scoped packages can start with dots/underscores, unscoped cannot",
      "purl_impact": "Namespace validation differs from name validation"
    }
  ],
  "llm_guidance": {
    "do_not_assume": [
      "All npm packages have scopes",
      "Package names can have any characters",
      "Versions don't need to follow SemVer",
      "@ symbols don't need encoding",
      "Case doesn't matter for package names"
    ],
    "key_facts_to_remember": [
      "Scope @ must be encoded as %40",
      "New package names must be lowercase",
      "Names must be URL-safe",
      "214 character limit includes scope",
      "Scoped packages can start with dots/underscores"
    ],
    "common_mistakes_to_avoid": [
      "Forgetting to encode the scope @ symbol",
      "Using uppercase in new package names",
      "Including spaces without encoding",
      "Misunderstanding scope vs name rules"
    ]
  },
  "examples": [
    {
      "purl": "pkg:npm/foobar@12.3.1",
      "description": "Basic npm package reference"
    },
    {
      "purl": "pkg:npm/%40angular/animation@12.3.1",
      "description": "Scoped npm package with percent-encoded scope"
    },
    {
      "purl": "pkg:npm/mypackage@12.4.5?vcs_url=git://host.com/path/to/repo.git%404345abcd34343",
      "description": "Package with VCS qualifier"
    },
    {
      "purl": "pkg:npm/lodash@4.17.21?checksum=sha512:abc123...",
      "description": "Package with integrity checksum"
    }
  ],
  "incorrect_examples": [
    {
      "purl": "pkg:npm/FOOBAR@12.3.1",
      "description": "Package name with uppercase letters - violates npm naming rules requiring lowercase",
      "violation": "name_definition.case_sensitive=false and name_definition.encoding=lowercase"
    },
    {
      "purl": "pkg:npm/@angular/animation@12.3.1",
      "description": "Scoped package with unencoded @ symbol - scope @ must be percent-encoded as %40",
      "violation": "namespace_definition.encoding=percent-encoded"
    },
    {
      "purl": "pkg:npm/@scope@angular/animation@12.3.1",
      "description": "Invalid scope format with multiple @ symbols",
      "violation": "Invalid namespace format according to npm scope specification"
    },
    {
      "purl": "pkg:npm/foo bar@12.3.1",
      "description": "Package name with spaces - not URL-safe characters",
      "violation": "name_definition requires URL-safe characters"
    },
    {
      "purl": "pkg:npm/.foobar@12.3.1",
      "description": "Package name starting with dot without scope - not permitted for unscoped packages",
      "violation": "npm package.json spec prohibits leading dots/underscores for unscoped packages"
    },
    {
      "purl": "pkg:npm/_foobar@12.3.1",
      "description": "Package name starting with underscore without scope - not permitted for unscoped packages",
      "violation": "npm package.json spec prohibits leading dots/underscores for unscoped packages"
    },
    {
      "purl": "pkg:npm/foobar@",
      "description": "Missing version after @ symbol",
      "violation": "Malformed version component"
    }
  ],
  "references": [
    {
      "title": "npm package.json specification",
      "url": "https://docs.npmjs.com/cli/v11/configuring-npm/package-json"
    },
    {
      "title": "npm scope documentation",
      "url": "https://docs.npmjs.com/cli/v11/using-npm/scope"
    },
    {
      "title": "yarn manifest specification",
      "url": "https://yarnpkg.com/configuration/manifest"
    }
  ],
  "metadata": {
    "last_updated": "2025-08-16"
  }
}
```

Carefully understand this requirement and wait for me to upload the jsonschema definitions one at at time. For each upload, you must generate the new improved schema definition with the additional tags suggested. Do not hallucinate or guess information. Where the input jsonschema is unclear or lacking details, explicitly say so. Acknowledge that you understand this requirement
