# Postfix & Prefix Expression Validator (NPDA)

A Non-deterministic Pushdown Automaton (NPDA) that validates whether an input string is a well-formed **Postfix** or **Prefix** expression. From start state $S$, the machine branches simultaneously into a Postfix PDA and a Prefix PDA, accepting the input if at least one branch reaches an accepting state.

## Alphabets & Stack Design
* **Input ($\Sigma$):** `n` (operand), `o` (binary operator)
* **Stack ($\Gamma$):**
  * `Z` — Bottom-of-stack marker
  * `A` — **1** operand waiting (Postfix) / **1** operand needed (Prefix)
  * `X` — **2+** operands waiting (Postfix) / **2+** operands needed (Prefix)

## Formal Definition
$M = (Q, \Sigma, \Gamma, \delta, S, Z, F)$ where $Q = \{S, q_0, q_1, q_2, q_3, q_4, q_5\}$ and $F = \{q_2, q_5\}$.

| Branch | Transition | Rule (`Input, Pop / Push`) | Action |
| :--- | :---: | :---: | :--- |
| **Start** | $S \rightarrow q_0, q_3$ | `ε, Z / Z` | Branch non-deterministically to Postfix & Prefix |
| **Postfix** | $q_0 \rightarrow q_1$ | `n, Z / AZ` | Push first operand |
| | $q_1 \rightarrow q_1$ | `n, A / XA` \| `n, X / XX` | Push additional operands |
| | $q_1 \rightarrow q_1$ | `o, X / ε` | Operator consumes 2 operands, leaves 1 |
| | $q_1 \rightarrow q_2$ | `ε, A / A` | Accept when 1 result remains at end of input |
| **Prefix** | $q_3 \rightarrow q_4$ | `ε, Z / AZ` | Initialize 1 expression requirement |
| | $q_4 \rightarrow q_4$ | `o, A / XA` \| `o, X / XX` | Operator adds operand requirements |
| | $q_4 \rightarrow q_4$ | `n, X / ε` \| `n, A / ε` | Operand satisfies 1 requirement |
| | $q_4 \rightarrow q_5$ | `ε, Z / Z` | Accept when all requirements are met |

## Test Cases
| Input | Postfix | Prefix | Result |
| :---: | :---: | :---: | :---: |
| `n` | Accept | Accept | **Accepted** |
| `nno`, `nnono` | Accept | Reject | **Accepted** |
| `onn`, `oonnn` | Reject | Accept | **Accepted** |
| `non`, `on` | Reject | Reject | **Rejected** |
