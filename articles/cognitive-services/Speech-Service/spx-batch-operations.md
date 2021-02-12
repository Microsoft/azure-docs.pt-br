---
title: Operações de lote da CLI de Fala – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Saiba como fazer conversão de fala em texto (reconhecimento de fala) e conversão de texto em fala (síntese de fala) com a CLI de Fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: 60cacafc89f2335b87885e4ea11dcf8992d68c3f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540214"
---
# <a name="speech-cli-batch-operations"></a>Operações de lote da CLI de Fala

Tarefas comuns ao usar os serviços de Fala do Azure são operações em lote. Neste artigo, você aprenderá a fazer conversão de fala em texto (reconhecimento de fala) e conversão de texto em fala (síntese de fala) com a CLI de Fala. Especificamente, você aprenderá a:

* Executar o reconhecimento de fala em lote em um diretório de arquivos de áudio
* Executar a síntese de fala em lote Iterando em um arquivo de `.tsv`

## <a name="batch-speech-to-text-speech-recognition"></a>Conversão de fala em texto (reconhecimento de fala) em lote

Frequentemente, o serviço de Fala é usado para reconhecer a fala de arquivos de áudio. Neste exemplo, você aprenderá a iterar em um diretório usando a CLI de Fala para capturar a saída de reconhecimento para cada arquivo de `.wav`. O sinalizador `--files` é usado para apontar para o diretório onde os arquivos de áudio estão armazenados, e o curinga `*.wav` é usado para instruir a CLI de Fala a executar o reconhecimento em todos os arquivos com a extensão `.wav`. A saída de cada arquivo de reconhecimento é gravada como um valor separado por tabulação em `speech_output.tsv`.

> [!NOTE]
> O argumento `--threads` também pode ser usado na próxima seção para comandos `spx synthesize`, e os threads disponíveis dependerão da CPU e do respectivo percentual de carga atual.

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

A seguir, um exemplo da estrutura do arquivo de saída.

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>Conversão de texto em fala (síntese de fala) em lote

A maneira mais fácil de executar a conversão de texto em fala em lotes é criar um arquivo `.tsv` (valores separados por tabulação) e usar o comando `--foreach` na CLI de Fala. Você pode criar um arquivo `.tsv` usando seu editor de texto favorito. Para este exemplo, vamos chamá-lo de `text_synthesis.tsv`:

>[!IMPORTANT]
> Ao copiar o conteúdo do arquivo de texto, verifique se o arquivo tem uma **tabulação**, e não espaços entre o local do arquivo e o texto. Às vezes, ao copiar o conteúdo deste exemplo, as tabulações são convertidas em espaços, fazendo com que o comando `spx` falhe durante a execução.

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Em seguida, execute um comando para apontar para `text_synthesis.tsv`, executar a síntese em cada campo `text` e gravar o resultado no caminho `audio.output` correspondente como um arquivo `.wav`.

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Esse comando é o equivalente à execução de `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav` **para cada** registro no arquivo `.tsv`.

Algumas coisas a serem observadas:

* Os cabeçalhos de coluna, `audio.output` e `text`, correspondem aos argumentos de linha de comando `--audio output` e `--text`, respectivamente. Argumentos de linha de comando de várias partes, como `--audio output`, devem ser formatados no arquivo sem espaços, sem traço à esquerda e com pontos separando cadeias de caracteres, por exemplo, `audio.output`. Qualquer outro argumento de linha de comando existente pode ser adicionado ao arquivo como colunas adicionais usando esse padrão.
* Quando o arquivo é formatado dessa forma, não há necessidade de passar nenhum argumento adicional para `--foreach`.
* Separe cada valor no `.tsv` com uma **tabulação**.

No entanto, se você tiver um arquivo `.tsv` como o seguinte exemplo, com cabeçalhos de coluna que **não correspondem** a argumentos de linha de comando:

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Você pode substituir esses nomes de campo pelos argumentos corretos usando a sintaxe a seguir na chamada `--foreach`. Esta é a mesma chamada descrita acima.

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da CLI de Fala](./spx-overview.md)
* [Início rápido da CLI de Fala](./spx-basics.md)
