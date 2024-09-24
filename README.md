# XplainLLM

<!-- include figure, control the size -->
<p align="center">
  <img src="figures/logo.png" width="350" />
</p>

> 🎉 We are thrilled to announce that ''XplainLLM: A Knowledge-Augmented Dataset for Reliable Grounded Explanations in LLMs'' has been accepted to EMNLP 2024 (Main Conference).
>
> Paper: [XplainLLM: A Knowledge-Augmented Dataset for Reliable Grounded Explanations in LLMs](https://arxiv.org/abs/2311.08614)

XplainLLM is a pioneering dataset designed to enhance LLM transparency and reliability by capturing the most influential elements behind model reasoning and presenting the decision-making process through human-understandable explanations.

Our dataset comprises **24,204** instances where each instance interprets the LLM’s reasoning behavior using knowledge graphs (KGs) and graph attention networks (GAT). 
We include explanations of reasoning behavior of **Llama-3-8b** and **RoBERTa-large** models on CommonsenseQA. 

XplainLLM also features a framework for generating *grounded explanations* and the *debugger-scores* for multidimensional quality analysis.

[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

 
## Table of Contents
- [XplainLLM](#xplainllm)
	- [Table of Contents](#table-of-contents)
	- [Main Characteristics](#main-characteristics)
	- [Dataset Details](#dataset-details)
		- [Schema](#schema)
		- [Data Format](#data-format)
		- [Example](#example)
	- [Usage Instructions](#usage-instructions)
		- [Downloading](#downloading)
		- [Importing](#importing)
		- [Navigating](#navigating)
	- [Comparison](#comparison)
	- [Explanation Framework](#explanation-framework)
	- [To-Do](#to-do)

## Main Characteristics

- **Enhancing Transparency**: XplainLLM enhances LLM transparency by using KGs and GAT to present model's reasoning behavior through human-understandable explanations.
- **Detailed Reasoning**: The dataset includes 24,204 instances with explanations for models like Llama-3 and RoBERTa, featuring components such as *why-choose*, *why-not-choose*, *reason-elements*, and *debugger-scores* to illuminate the model’s reasoning.
- **Grounded Explanations**: Provides a framework for generating grounded explanations and debugger-scores, helping to reduce hallucinations and improve explanation quality.
- **Quality Assurance**: Explanations are refined through human review to ensure high quality, making the dataset a valuable resource for building trust in LLM outputs.

## Dataset Details
### Schema
```
Question: The commonsense query posed to the model.
Answers: A list of potential answers the model could choose from.
Label: The correct or most appropriate answer from the list of potential answers (ground truth).
Predicted_Label: The index of the answer the model believes to be the most accurate.
Label_Matched: Indicates whether the model's chosen answer matches the Label.
Concept: A list of reason-elements derived from the knowledge graph, illustrating the model's associations with the question and illuminating its decision-making process.
Topk: The top 5 reason-elements that are the primary influencers on the model's decision and form the basis of the generated explanation.
Explanation_Why: A descriptive rationale elucidating why the model chose a particular answer.
Explanation_Why_Not: A detailed account of why the model did not choose the other answers.
Debugger_score: A metric that quantifies the quality of the generated explanation.
Embeddings: The embeddings of the question and answer choices.
```

### Data Format

The dataset can be found in the file named example.jsonl, which contains one json object in each line. The format of the json object is as follows:
```
{
	"question": typeof(string),
	"answers": typeoof(list_of_strings),
	"label": typeof(string),
	"predicted_label": typeof(string),
	"label_matched": typeof(boolean),
	"concept": typeoof(list_of_strings),
	"topk": typeoof(list_of_strings),
	"explanation_why": typeof(string),
	"explanation_why_not": typeof(string),
	"debugger_score": typeof(string),
	"embeddings": typeof(list_of_floats)
}
```
We split the dataset into three files for convenience: train.jsonl, dev.jsonl, and test.jsonl. In test.jsonl we do not have the ground truth labels for the correct answers.
We will release the full dataset soon.

### Example
The following is an example instance from our XplainLLM dataset:
```
{
	"question": "The people danced to the music, what was the music like for them?",
	"answers": ["soothing","vocal or instrumental","loud","universal","enjoyable"],
	"label": "enjoyable",
	"predicted_label": "4",
	"label_matched": true,
	"concept": ["play_mozart","listening_to_classical_music","together","abnd","dancefest","irritation", ...],
	"topk": ["play_mozart","listening_to_classical_music","together","abnd","dancefest"],
	"explanation_why": "Since the words "play_mozart" and "listening_to_classical_music" suggest that the music was likely of high quality and enjoyable to listen to, and since the words "together" and "dancefest" suggest that the people were likely in a social and festive setting, the model predicts that the music was enjoyable for them. Therefore, the predicted option is "enjoyable".",
	"explanation_why_not": 	"The other potential choices can be combined into three categories: 1) "soothing" and "vocal or instrumental" do not match with the festive and social setting suggested by the words "together" and "dancefest"; 2) "loud" is not necessarily correlated with enjoyment and could be unpleasant for some people; 3) "universal" does not give any indication of the quality or character of the music, and is therefore too vague to be a valid choice.",
    "debugger_score": "Faithfulness: 4, Completeness: 3, Accuracy: 4",
    "embeddings:" {[[0.1, 0.2, ..., 0.3], [0.1, 0.2, ..., 0.3], [...]}
}
```

## Usage Instructions

### Downloading

Access the dataset here: [dataset/](#)

Download the demo of `Llama-3` explanation: `demo/llama-3.jsonl`

Download the demo of `RoBERTa-large` explanation: `demo/roberta.jsonl`

The corresponding embeddings for the demo files are available in the `embeddings/` folder.

### Importing

The dataset is available in a JSONL format. Use suitable data analysis tools or libraries to read and process the dataset, e.g.,

```python
import json

data = []
# Load the data file
with open('path_to_data.jsonl', 'r') as file:
    for line in file:
        data.append(json.loads(line))
```

### Navigating

Each entry corresponds to a question-answer-explanation triple.

Navigate through the various keys like Question, Answers, Explanation_Why, etc., to understand each component, e.g.,

```python
# Read the id
qid = data["qid"]
# Read question
question = data["statement"]["question"]
# Read answers list
answers_list = data["statement"]["answers"]
# Read predicted answer
predicted_answer = answers_list[int(data["predicted_label"])]
# Read top reasoning-elements
top_elements = data["topk"]
# Read reasoning-elements
reasoning_elements = data["concept"]
# Read whether predicted answer is correct
correct_or_not = data["label_matched"]
# Read explanation for why model chose a particular answer
explanation_why = data["explanation_why"]
# Read explanation for why model did not choose other answers
explanation_why_not = data["explanation_why_not"]
# Read debugger score
debugger_score = data["debugger_score"]
```

Corresponding embeddings can be accessed as follows:

```python
# Read embeddings 
for emb in embedding_data:
	# Access embeddings for a specific question
	if emb["qid"] == qid:
		embeddings = emb["embeddings"]
		break
```

## Comparison
| Dataset | Size | Answer Format | Expl. Format | Source | Model Match? | Self-Explanatory? | "Why Not" Included? |
| --- | --- | --- | --- | --- | --- | --- | --- |
| CoS-E | 9,500 | MCQ | NL | Human | ❌ | ❌ | ❌ |
| ECQA | 10,962 | MCQ | NL | Human | ❌ | ✅ | ❌ |
| Neuron | 307,200 | Neuron | NL + Score | Model | ✅ | ❌ | ❌ |
| **XplainLLM** | 24,204 | MCQ | NL | Model | ✅ | ✅ | ✅ |

**[Datasets]**

- CoS-E: A dataset that consists of human explanations for commonsense
reasoning.
- ECQA: A dataset that consists of human explanations for
commonsense reasoning, including positive and negative facts.
- Neuron: A dataset that consists of explanations for the behavior of
neurons in GPT-2.

**[Terms]**

- Size: Number of entries in the dataset.
- Answer Format: The type of answers, such as multiple-choice (MCQ).
- Explanation Format: The type of explanation, such as natural language (NL).
- Source: Where the explanation originates from.
- Model Match?: Indicates if the explanation aligns with the model's decision-making process.
- Self-Explanatory?: Indicates if human intervention is needed to deduce the reasoning.
- "Why Not" Included?: Specifies if the dataset provides reasons for not choosing other answers.

## Explanation Framework

Coming soon.

## To-Do
- [ ] Release the full dataset
- [ ] Release the code for explanation framework.
