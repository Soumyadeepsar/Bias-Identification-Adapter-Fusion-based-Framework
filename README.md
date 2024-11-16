# Bias-Aware AdapterFusion (BAAF) Framework 
The Entire training setup and ICL techniques used in our paper is discussed here:<br/>
![fusion](https://github.com/user-attachments/assets/8aa524da-ea31-4c19-98a2-6c399521f7c9)

## Training details of Single-Task Adapters (STA):
These are adapter layers used to specifically train on single type of bias at a time. For our framework we particularly chose bottleneck adapter, a specific configuration of adapters. The best learning rate for bottleneck adapter, was emperically  determined to be *1e-4* in the following paper:[***Adapters: A Unified Library for
Parameter-Efficient and Modular Transfer Learning***](https://arxiv.org/pdf/2311.11077). So we use a ***learning rate = 1.2e-4*** . We used a total 6 epochs for the training process. The model checkpoint with lowest validation loss was used for inference and saved for AdapterFusion formation. Early stopping was employed, with a ***patience level = 3***, to ensure that after certain epochs if the validation loss does not gets reduced, the run should automatically stop. This helps to prevent further use of valuable resources. For further information please refer to pyhton notebook, shared in BAAF Folder, where the entire training code is written from scratch. 

## Training details of AdapterFusion (AF) for cross-validation:
The saved pre-trained STAs for each type of bias are loaded along with the pre-trained LLM on which STA were trained. Now we Fuse these layers into a composition with a AdapterFusion layer on top of it, which learns to balance different learned representations from every biases. The  training setup remains the same as described above. The only significant change was the ***learning rate*** which was set to a value of ***5e-5***. This was an emperical observation and the graph of the observation is shared in the research paper. The entire code is shared in BAAF folder for further clarification.

# In-Context Learning(ICL) based techniques:
For each prompting technique we used LLama-3.1 from Groq. The *temperature* was set to be 0 for all our experiments. All the other parameters were set to be default.
## Zero-shot Prompt:
Type of bias can be set as :***'gender','racially','cognitively'*** and so on. Depending upon which bias is targeted.
```ruby
Classify the following text as {type of bias} biased or unbiased. Only give your final label as 0(for unbiased text) or 1 (for biased text), do not write your explaination. Here is the text:{input}
```
## Few-Shot Prompt:

```ruby
Here are some examples of {type of bias} biased and unbiased text given below. The label for biased text is 1 and that of unbiased is 0:"

text: {example statement 1}
label: {example label 1}

.... examples.....

Now only give the final label(0 or 1) indicating unbiased(0) or biased(1) text.

text: {input}

label:
```
## Automatic Behaviour Optimization (ABO)
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
The given sentence is 'your sister is a drama queen downvote me all you want people. Is this sentence gender biased or not?' 

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
``` ruby
Task: Given the sentence classify the sentence gender  biased or unbiased. Only give your final label as 0 (for unbiased text) or 1 (for biased text) do not write your explanation.

#Instructions

1. Read the sentence more carefully and identify the words that are biased, stereotypical, or offensive.

2. Identify any words or phrases that may be biased, stereotypical, or gender-specific (e.g., "sister").

3. Determine if these words reinforce harmful gender stereotypes or imply bias.

4. Go through the sentence one more time to ensure you have not missed any words.

5. Write down the sentence is biased or unbiased in the format: 'class':0 or 1 where 0 for gender  unbiased sentence and 1 for gender biased sentence.

### Example
Here are some examples to help you thinking.

##Input
your sister is a drama queen downvote me all you want people.

##Output

step 1:
The given sentence is 'your sister is a drama queen downvote me all you want people. Is this sentence gender biased or not?'

step 2:
The list of words that assoicated with gender and capture the core ideas are: 'sister','drama queen'.

step 3:
'sister' implies a specific gender  and 'drama queen' imples strong emotional connection. Thus this sentence imples gender-bias.

step 4:
Reading through the sentence again to ensure no words are missed.

step 5:
Henece, the output will be 'class': 1.
```

