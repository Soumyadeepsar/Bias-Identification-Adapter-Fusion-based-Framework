# Bias-Aware AdapterFusion (BAAF) Framework 
The code and dataset for ECIR 2025 paper single-task training code are shared AdapterFusion cross-validation code shared.

# Automatic Behaviour Optimization (ABO)
Automatic Behaviour Optimization aims to optimize model's behaviour instead of optimizing the prompts directly. ABO follows the following steps to achieve the Optimized prompt: <br/>
(1) **Step-by-step prompt generation:** Instruct LLM to generate prompts step-by-step. <br/>
(2) **Prompt instruction:** For each prompt an ``Instruction-following Demonstration'' has been utilized, where we provide an example for describing each step how to capture the nuanced information from the given input. <br/>
```ruby
Task: Given the sentence classify the sentence gender  biased or unbiased. Only give your final label as 0 (for unbiased text) or 1 (for biased text) do not write your explanation. 

#Instructions 

1. Read the sentence more carefully and identify the words that are biased, stereotypical, or offensive.

2. Identify specific words that are associated with bias.

3. Consider how the sentence might be perceived by different audiences and how it might reinforce harmful stereotypes.

4. Go through the sentence one more time to ensure you have not missed any words.

5. Write down the sentence is biased or unbiased in the format: 'class':0 or 1 where 0 for gender  unbiased sentence and 1 for gender biased sentence.<br/>

### Example
Here are some examples to help you thinking.

##Input 
your sister is a drama queen downvote me all you want people. 

##Output 

step 1: 
The given sentence is 'Jobs that involve the minimum wage are overwhelmingly jobs for young people starting out in the workforce. Is this sentnec is biased or not?' 

step 2: 
The list of words that assoicated with gender and capture the core ideas are: 'sister','drama queen'. 

step 3: 
'sister' implies a specific gender  and 'drama queen' imples strong emotional connection. Thus this sentence imples gender-bias. 

step 4: 
Reading through the sentence again to ensure no words are missed. 

step 5: 
Henece, the output will be 'class': 1. 
```
(3) **Refinement process:** During prompt refinement process, we provide error examples, LLM is required to identify the failure step and breaking down the solution at the problematic step. The frequency of prompt refinement process is decided based on model's performance. <br/>
