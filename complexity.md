# CS305 - Software Engineering

## McCabe’s Cyclomatic Complexity

It counts the number of independent paths through a program. The more branching (conditionals, loops), the higher the complexity.

It models your program as a control flow graph (CFG), where:
- Nodes = code blocks or statements
- Edges = control flow between blocks (like if-else, loops, etc.)

**V(G)** = E - N + 2P

**Where:**
- V(G) = Cyclomatic complexity
- E = Number of edges in the control flow graph
- N = Number of nodes
- P = Number of connected components (usually 1 for a single function)

**it tells you tgat if:**
- V(G) = 1, you have straight-line code (no branches)
- V(G) = 2-10: Simple, manageable
- V(G) > 10: Complex — harder to test and maintain
- V(G) > 20: Extremely complex — refactor recommended

### Example Code:
```python
def max_of_three(a, b, c):
    if a > b:
        if a > c:
            return a
        else:
            return c
    else:
        if b > c:
            return b
        else:
            return c
```
### Control Flow Analysis:
- 1 if a > b
- 1 nested if a > c
- 1 else
- 1 if b > c
- 1 else

There are 4 decision points (if or else branching).

So:
Cyclomatic Complexity = D + 1 = 4 + 1 = 5

### Interpretation:
- Complexity = 5
- Test cases needed for full path coverage = 5
- Not terrible, but could be simplified.

### Real-world Application:
- Use tools like SonarQube, Radon (Python), or ESLint (JS) to flag high-complexity functions.
- When complexity > 10, refactor:
- Split into smaller functions
- Use polymorphism or guard clauses to reduce branching


## Halstead’s Software Science

Measures software complexity based on the number of operators and operands in the source code.
It looks at the code from a linguistic and information theory perspective. Tracks how many unique and total operators and operands are used.

### Some theory/definitions:
Let:
- n1 = number of distinct operators
- n2 = number of distinct operands
- N1 = total number of operators
- N2 = total number of operands

From these, Halstead defines:

1.	**Program Vocabulary (n):**
`n = n1 + n2`
2.	**Program Length (N)**
N = N1 + N2
3.	**Volume (V)**
V = N × log2(n)
=> Size of implementation in bits of information.
4.	**Difficulty (D)**
D = (n1 ÷ 2) × (N2 ÷ n2)
=> How hard it is to write or understand.
5.	**Effort (E)**
E = D × V
=> Mental effort to develop or maintain the code.
6.	**Time (T)**
T = E ÷ 18 seconds
=> Estimated time to implement
7.	**Estimated Bugs (B)**
B = E^(2÷3) ÷ 3000
=> Approximate number of errors

### What it tells you:
- Larger Volume = more code or more complex code
- Higher Effort = more difficult to write, understand, or maintain
- More Bugs = riskier code


### Example Code:

```python
def area_of_rectangle(length, width):
    return length * width
```
### Count Operators and Operands:

**Operators:** [`def`, `()`, `:`, `return`, `*`]
So we have,
- n1 (distinct operators) = 5
- N1 (total operators) = 5

**Operands:** [`area_of_rectangle`, `length`, `width`]
So we have,
- n2 (distinct operands) = 3
- N2 (total operands) = 5


### Apply Halstead Metrics:
- Vocabulary n = n1 + n2 = 5 + 3 = 8
- Length N = N1 + N2 = 5 + 5 = 10
- Volume V = N * log2(n) = 10 * log2(8) = 10 * 3 = 30
- Difficulty D = (n1 ÷ 2) * (N2 ÷ n2) = (5÷2)*(5÷3) ≈ 4.17
- Effort E = D * V ≈ 4.17 * 30 ≈ 125
- Time T = E ÷ 18 ≈ 125 ÷ 18 ≈ 7 seconds
- Estimated Bugs B = (E)^(2÷3)÷3000 ≈ 0.017

### How would you interpret these numbers?
- Volume is low → Small function
- Effort is low → Easy to understand
- Estimated bugs ≈ 0 → Good quality

## Real-world Application:
**Use tools like:**
- lizard or radon (Python)
- halstead-metrics (JavaScript)
- Visual Studio Code extensions or JetBrains IDEs plugins

**In code reviews:**
- High Volume or Effort → too much logic in one function
- Use metrics to justify refactoring or decomposing functions


## How to Use These Metrics in Real Projects

### Static Analysis Tools:
Integrate tools into CI pipelines:
- Python: radon, lizard
- JavaScript: ESLint (with cyclomatic complexity rule)
- Java: PMD, SonarQube
- C/C++: cppcheck

### During Code Reviews:
- Flag functions with high Cyclomatic Complexity
- Use Halstead Effort and Volume as signals for maintainability

### Test Planning:
- Cyclomatic Complexity → Number of paths to test
- Use it to guide unit test coverage (branch or path coverage)

### Refactoring Triggers:

**If you see:**
- Cyclomatic Complexity > 10
- Halstead Effort > 1000

**Then:** Time to refactor


## Comparative overview
| Metric | Cyclomatic Complexity | Halstead’s Software Physics|
|--------|-----------|----------|
|Based on | Control flow graph | Operators and operands | 
|Example Function Value | 5 | Volume = 30, Effort = 125|
| Indicates | Test complexity | Cognitive complexity|
| Good for | Test planning, refactoring | Bug prediction, readability |
| Tools | Radon, ESLint, SonarQube | Radon, Lizard, custom tools|

# Example usage of tools

## Using radon

radon can measure:
- Cyclomatic Complexity
- Halstead Metrics
- Maintainability Index

**Installation**
`pip install radon`

### Example Python file (example.py)

```python
def factorial(n):
    if n == 0:
        return 1
    else:
        return n * factorial(n - 1)
```

### Analyze Cyclomatic Complexity:

`radon cc example.py -a`

**Output Example:**
```bash
example.py
    F 2:0 factorial - A (2)
    Average complexity: A (2.0)
```

The grade “A” indicates very low complexity. Cyclomatic Complexity = 2

### Analyze Halstead Metrics:

`radon hal example.py`

**Output Example:**

```bash
example.py
 - function 'factorial' (2): 
     h1: 4  h2: 4  N1: 5  N2: 6
     vocabulary: 8
     length: 11
     calculated_length: 10.0
     volume: 33.0
     difficulty: 3.0
     effort: 99.0
     time: 5.5
     bugs: 0.01
```

### Maintainability Index:

`radon mi example.py`

**Output:**
`example.py - A (92.67)`

MI score above 85 is excellent.


## Using lizard

lizard is a multi-language tool that focuses on Cyclomatic Complexity and function length.

**Installation**

`pip install lizard`

**Run on a file:**

`lizard example.py`

**Output Example:**

```bash
==============================================================
  NLOC    CCN   token  PARAM  length  location  
--------------------------------------------------------------
       3      2      21      1       4 example.py:1:0:factorial
==============================================================

Field	Meaning
NLOC	Non-blank lines of code
CCN	Cyclomatic Complexity Number
length	Function length (in lines)
PARAM	Number of parameters
```

### When to Use Which?

| Tool | Best For | Also Does |
|-------|------|------|
| `radon` |In-depth Python analysis | Halstead, MI |
| `lizard` | Multi-language, fast summary |	Python, C/C++, JS, etc.|

