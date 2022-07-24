# GPT3 Fine Tuning From Chats

Jupyter notebook that:

1. Converts conversations in CSV format to JSONL format preserving context and complying with the format specified in [OpenAI documentation](https://beta.openai.com/docs/guides/fine-tuning/case-study-maintaining-company-voice) for GPT3 fine tuning.

2. Performs fine tuning to a GPT3 model using the JSONL data and listens for new events that inform about the training progress.

## Format

The script expects a CSV showing some interactions between two actors (usually a user and a virtual assistant).

| Human  | AI |
| ------------- | ------------- |
| Hello  | I'm an AI  |
| Open the pod bay doors please  | No  |
| Open the pod bay doors!  | Well, if you insist  |
| ### | ### |
| Good morning | Hey |
| Can you read me the news? | No, I can't |
| Is there something you can do? | You should know |
| Oh okey | Yeah |
| *{question}* | *{answer}* |

The first row must contain the names of the actors, which will be used as fixed delimiters (in this example `"\nHuman: "` y `"\nAI: "`).

Interactions are delimited by a special string that must not appear in any other message (e.g. *###*).

For convenience, the table can be written in an Excel sheet and converted to UTF-8 encoded CSV using ' ; ' as delimiter.

To preserve the context, (by default) the last messages of the conversation are included in the prompts. The maximum number of messages included in prompts can be modified.

The result of the processing is 2 JSONL files, training and validation partitions, ready to be used for GPT3 fine tuning.

The script, using default parameters, produces the following JSONL when previous table is used as input:

```
{"prompt": "Human: Can you read me the news?\nAI: No, I can't\nHuman: Is there something you can do?\nAI: You should know\nHuman: Oh okey\nAI:", "completion": " Yeah\nHuman: "}
{"prompt": "Human: Hello\nAI: Hi, I'm an AI\nHuman: Open the pod bay doors please\nAI: No\nHuman: Open the pod bay doors\nAI:", "completion": " Well, if you insist\nHuman: "}
...
```

## Parameters

### Completion stop separator

The ``include_stop`` parameter enables the inclusion of a separator that indicates the end of a completion. According to the [OpenAI documentation](https://beta.openai.com/docs/guides/fine-tuning/data-formatting) it is recommended to use this separator.

```
{"prompt": "...", "completion": " Yeah\nHuman: "}
```
```
{"prompt": "...", "completion": " Yeah"}
```

### Prompt messages

The ``buffer_size`` parameter indicates the maximum number of messages that will appear at the prompt. According to the [Open AI documentation](https://beta.openai.com/docs/guides/fine-tuning/case-study-maintaining-company-voice), to learn company voices, it is recommended to train many samples where the prompt is empty. In that case, ``buffer_size`` should be 0.

## OpenAI API

> **WARNING** The script contains functions that performs destructive operations like removing all files or all finetuned models from your OpenAI account. Use with caution.

The script contains functions for:
1. Upload JSONL files and get their IDs.
2. Train a new model using the IDs of the training samples.
3. Listen and display new events to track the training progress.

## License

[MIT](LICENSE)