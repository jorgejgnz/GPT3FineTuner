# GPT3 Fine Tuning a partir de Conversaciones

Libreta de Jupyter para:

1. Convertir conversaciones en formato CSV a formato JSONL conservando contexto y cumpliendo el formato recomendado por la documentación de OpenAI para GPT3 fine tuning.

<!-- https://beta.openai.com/docs/guides/fine-tuning/case-study-maintaining-company-voice -->

2. Realizar fine tuning a un modelo GPT3 con los datos en formato JSONL y quedar a la escucha de nuevos eventos que informen del avance del entrenamiento.

## Formato

El script espera un CSV en el que se aparezcan varias interacciones entre dos actores (normalmente un usuario y un asistente virtual).

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

La primera fila debe contener los nombres de los actores, que serán usados como delimitadores fijos (en este ejemplo `"\nHuman: "` y `"\nAI: "`).

Las interacciones están delimitadas por una cadena especial que no debe aparecer en ninguna conversación (por ejemplo *###*).

Para más comodidad, la tabla se puede escribir en una hoja Excel y ser convertida a CSV codificado en UTF-8 usando ';' como delimitador.

El resultado del procesamiento son 2 archivos JSONL, para las particiones de entrenamiento y validación, listos para ser usados en fine tuning de GPT3.

Para conservar el contexto, por defecto se incluyen en las prompts los últimos mensajes de la conversación.

```
{"prompt": "Human: Can you read me the news?\nAI: No, I can't\nHuman: Is there something you can do?\nAI: You should know\nHuman: Oh okey\nAI:", "completion": " Yeah\nHuman: "}
{"prompt": "Human: Hello\nAI: Hi, I'm an AI\nHuman: Open the pod bay doors please\nAI: No\nHuman: Open the pod bay doors\nAI:", "completion": " Well, if you insist\nHuman: "}
...
```

## Parámetros

### Completion stop separator

El parámetro ``include_stop`` permite incluir un separador que indica el fin de una completion. De acuerdo con la documentació nde OpenAI se recomienda usar este separador.

<!-- https://beta.openai.com/docs/guides/fine-tuning/data-formatting -->

```
{"prompt": "...", "completion": " Yeah\nHuman: "}
```
```
{"prompt": "...", "completion": " Yeah"}
```

### Buffer

El parámetro ``buffer_size`` indica el número máximo de mensajes que aparecerán en el prompt. Según la documentación de Open AI, para aprender voces, es recomendable entrenar muchas muestras donde la prompt es vacía. En ese caso, ``buffer_size`` debe ser 0.

<!-- https://beta.openai.com/docs/guides/fine-tuning/case-study-maintaining-company-voice -->

## OpenAI API

> **WARNING** The script contains functions that performs destructive operations like removing all files or all finetuned models from your OpenAI account. Use with caution.

El script contiene funciones para:
1. Subir ficheros JSONL y obtener su ID
2. Entrenar un nuevo modelo usando los IDs de las muestras de enternamiento
3. Escuchar y mostrar nuevos eventos para seguir el progreso del entrenamiento

## Licencia

[MIT](LICENSE)