# Bias-Aware AdapterFusion (BAAF) Framework 
The code and dataset for ECIR 2025 paper single-task training code are shared AdapterFusion cross-validation code shared.

# Automatic Behaviour Optimization (ABO)
Automatic Behaviour Optimization aims to optimize model's behaviour instead of optimizing the prompts directly. ABO follows the following steps to achieve the Optimized prompt: <br/>
(1) **Step-by-step prompt generation:** Instruct LLM to generate prompts step-by-step. <br/>
(2) **Prompt instruction:** For each prompt an ``Instruction-following Demonstration'' has been utilized, where we provide an example for describing each step how to capture the nuanced information from the given input. <br/>
```ruby
import openai
openai.api_key = " "
```
(3) **Refinement process:** During prompt refinement process, we provide error examples, LLM is required to identify the failure step and breaking down the solution at the problematic step. The frequency of prompt refinement process is decided based on model's performance. <br/>
