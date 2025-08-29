# sinhala-finetune
# Sinhala Fine-tune ChatGPT Project

මෙම project එක OpenAI GPT මොඩල් එක Sinhala භාෂාව සඳහා fine-tune කරන ආකාරය දැක්වීම සඳහා සකස් කර ඇත. මෙහි workflow එකේ dataset සකස් කිරීම, file upload කිරීම, fine-tuning, progress monitor කිරීම සහ model test කිරීම සියල්ල cover කර ඇත.

---

## අවශ්‍ය දේවල්

- Python 3.8+  
- OpenAI API account සහ API Key  
- OpenAI CLI install කර තිබීම

## මේ වැඩේ කරන හැටි

OpenAI CLI install කරන්න:

```bash
python -m pip install --upgrade pip
python -m pip install openai[cli]

## පියවර 1: Training Dataset සකස් කරන්න

sinhala_data.jsonl නමින් JSONL file එකක් create කරන්න.

Format: line-separated JSON objects

{"prompt": "මම ගියෙ කොහෙද?", "completion": "මම ගියෙ කොහෙද?"}
{"prompt": "ඔයා කොහොමද?", "completion": "ඔයා කොහොමද?"}
{"prompt": "මම ඔයාලගෙ උදව් අවශ්‍යයි.", "completion": "මට ඔයාගේ උදව් අවශ්‍යයි."}


UTF-8 encode කරලා තිබිය යුතුයි.

Line-separated JSON, array brackets [ ... ] use කරන්න එපා.

## පියවර 2: Dataset OpenAI server එකට upload කරන්න
openai api files.create -f "/path/to/sinhala_data.jsonl" -p "fine-tune"


Output JSON එකේ "id" field එක copy කරන්න → <file_id>

## පියවර 3: Fine-tune job start කරන්න
openai api fine_tuning.jobs.create -m gpt-3.5-turbo-0613 -F <file_id> -s sinhala_test


Response JSON එකේ "id" field එක copy කරන්න → <fine_tune_id>

## පියවර 4: Fine-tune progress monitor කරන්න
openai api fine_tuning.jobs.list_events -i <fine_tune_id>


Status: pending, running, succeeded, failed

Logs live terminal එකේ බලන්න පුළුවන්

## පියවර 5: Fine-tuned model name ලබාගන්න
openai api fine_tuning.jobs.retrieve -i <fine_tune_id>


"fine_tuned_model" field එක → testing සහ deployment එක සඳහා model name එක

## පියවර 6: Fine-tuned model test කරන්න
openai api chat.completions.create \
  -m <fine_tuned_model_name> \
  -p "ඔයා කොහොමද?"


Output එකෙන් Sinhala grammar, spelling, natural response validate කරන්න

## උපදෙස් (Tips)

Dataset diverse & clean විය යුතුයි (formal + informal sentences).

UTF-8 encoding verify කරන්න.

Overfitting avoid කිරීම සඳහා validation examples add කරන්න.

Small batch start → results හොඳනම් bigger batch try කරන්න.