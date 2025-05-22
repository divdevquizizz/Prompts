# Concept Revision Data Contract

This document outlines the data contract required for the Concept Revision component. The backend should provide data in the following structure to ensure proper rendering of all features.

## Data Structure

```typescript
interface ConceptRevisionData {
  concepts: Concept[];
}

interface Concept {
  id: number;
  title: string;
  description: string;
  points: string[];
  formulae: Formula[];
}

interface Formula {
  title: string;
  content: string[];
}
```

## Detailed Specifications

### Concept Object

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| id | number | Unique identifier for the concept | 1 |
| title | string | Title of the concept | "Basic Arithmetic Operations" |
| description | string | Detailed explanation of the concept | "Arithmetic operations are the foundation..." |
| points | string[] | Array of key points about the concept | ["Addition combines numbers...", "Subtraction finds the difference..."] |
| formulae | Formula[] | Array of formula sections | See Formula Object below |

### Formula Object

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| title | string | Title of the formula section | "Basic Operations" |
| content | string[] | Array of KaTeX formatted formulas | ["Addition: $a + b = c$"] |

## KaTeX Formatting Guidelines

All mathematical expressions must be wrapped in KaTeX delimiters:
- Inline math: `$...$`
- Display math: `$$...$$`

### Common KaTeX Symbols

| Symbol | KaTeX Code | Example |
|--------|------------|---------|
| Addition | `+` | `$a + b$` |
| Subtraction | `-` | `$a - b$` |
| Multiplication | `\times` | `$a \times b$` |
| Division | `\div` | `$a \div b$` |
| Fraction | `\frac{a}{b}` | `$\frac{1}{2}$` |
| Square root | `\sqrt{x}` | `$\sqrt{x}$` |
| Power | `^` | `$x^2$` |
| Subscript | `_` | `$x_1$` |
| Greek letters | `\alpha`, `\beta`, etc. | `$\alpha + \beta$` |
| Infinity | `\infty` | `$\infty$` |
| Pi | `\pi` | `$\pi$` |

## Example Payload

```json
{
  "concepts": [
    {
      "id": 1,
      "title": "Basic Arithmetic Operations",
      "description": "Arithmetic operations are the foundation of mathematics. They include addition, subtraction, multiplication, and division.",
      "points": [
        "Addition combines numbers to find their sum",
        "Subtraction finds the difference between numbers",
        "Multiplication is repeated addition",
        "Division shares numbers into equal parts",
        "Order of operations (PEMDAS) must be followed"
      ],
      "formulae": [
        {
          "title": "Basic Operations",
          "content": [
            "Addition: $a + b = c$",
            "Subtraction: $a - b = c$",
            "Multiplication: $a \\times b = c$",
            "Division: $a \\div b = c$"
          ]
        },
        {
          "title": "Order of Operations",
          "content": [
            "PEMDAS:",
            "1. Parentheses: $(a + b)$",
            "2. Exponents: $a^2$",
            "3. Multiplication: $a \\times b$",
            "4. Division: $a \\div b$",
            "5. Addition: $a + b$",
            "6. Subtraction: $a - b$"
          ]
        }
      ]
    }
  ]
}
```

## Important Notes

1. **KaTeX Escaping**: When using backslashes in KaTeX formulas, they need to be double-escaped in JSON: `\\` instead of `\`.

2. **Content Length**: 
   - Title: Recommended max length 100 characters
   - Description: Recommended max length 500 characters
   - Points: Each point recommended max length 200 characters
   - Formula content: Each formula recommended max length 150 characters

3. **Required Fields**: All fields in the Concept and Formula objects are required. Do not omit any fields.

4. **ID Uniqueness**: Each concept must have a unique ID within the concepts array.

5. **Array Limits**:
   - Points array: Recommended max 10 points per concept
   - Formulae array: Recommended max 5 formula sections per concept
   - Content array in formulae: Recommended max 10 formulas per section

## Error Handling

The frontend will handle the following cases:
- Missing required fields
- Invalid KaTeX syntax
- Empty arrays
- Duplicate IDs

However, it's recommended to validate the data on the backend before sending it to ensure a smooth user experience.

## AI Model Prompt Template

Here's a prompt template that can be used to instruct AI models to convert content into the required format:

```
You are a content transformation assistant. Your task is to convert educational content into a structured format for concept revision. Follow these rules strictly:

1. Input: You will receive educational content about a topic.
2. Output: Transform it into a JSON structure following the ConceptRevisionData format.

Rules for transformation:
1. Break down the content into clear, distinct concepts
2. For each concept:
   - Create a unique numeric ID
   - Extract or create a clear, concise title
   - Write a comprehensive description (max 500 chars)
   - Identify 3-5 key points (max 200 chars each)
   - Identify or create relevant formulas using KaTeX syntax

KaTeX Formatting Rules:
- Wrap all mathematical expressions in $...$
- Use proper KaTeX commands:
  - Multiplication: \times
  - Division: \div
  - Fractions: \frac{num}{den}
  - Powers: ^
  - Subscripts: _
  - Square root: \sqrt{expr}
  - Greek letters: \alpha, \beta, etc.

Example Input:
"Let's learn about basic arithmetic. Addition combines numbers to find their sum. For example, 2 + 3 = 5. Subtraction finds the difference between numbers. Multiplication is repeated addition, and division shares numbers into equal parts."

Example Output:
{
  "concepts": [{
    "id": 1,
    "title": "Basic Arithmetic Operations",
    "description": "Arithmetic operations are the foundation of mathematics. They include addition, subtraction, multiplication, and division.",
    "points": [
      "Addition combines numbers to find their sum",
      "Subtraction finds the difference between numbers",
      "Multiplication is repeated addition",
      "Division shares numbers into equal parts"
    ],
    "formulae": [{
      "title": "Basic Operations",
      "content": [
        "Addition: $a + b = c$",
        "Subtraction: $a - b = c$",
        "Multiplication: $a \\times b = c$",
        "Division: $a \\div b = c$"
      ]
    }]
  }]
}

Important Guidelines:
1. Ensure all mathematical expressions are properly formatted in KaTeX
2. Keep descriptions clear and concise
3. Break down complex topics into multiple concepts if needed
4. Use proper escaping for special characters in JSON
5. Maintain consistent formatting across all concepts
6. Ensure all required fields are present
7. Keep content within specified length limits

Please transform the following content into the required format:
[CONTENT TO BE TRANSFORMED]
```

### Using the Prompt

1. Replace `[CONTENT TO BE TRANSFORMED]` with the actual content that needs to be converted
2. The AI model should return a valid JSON object following the ConceptRevisionData structure
3. Validate the output against the schema before using it in production

### Best Practices for AI Transformation

1. **Content Validation**:
   - Verify that all mathematical expressions are properly formatted
   - Check that all required fields are present
   - Ensure content lengths are within limits
   - Validate JSON structure

2. **Quality Checks**:
   - Review the transformed content for accuracy
   - Ensure mathematical formulas are correctly represented
   - Verify that concepts are properly separated
   - Check that key points are clear and concise

3. **Error Handling**:
   - Implement validation for AI-generated content
   - Have fallback mechanisms for invalid transformations
   - Log transformation errors for improvement
   - Provide feedback to improve AI model performance

4. **Continuous Improvement**:
   - Collect feedback on transformed content
   - Update the prompt based on common issues
   - Refine the transformation rules
   - Maintain a knowledge base of successful transformations 
