---
title: Como usar a Transcrição de Lote – Serviços de Fala
titlesuffix: Azure Cognitive Services
description: A transcrição de lote é ideal se você quer transcrever uma grande quantidade de áudio em armazenamento, como Blobs do Azure. Usando a API REST dedicada, você pode apontar para arquivos de áudio por um URI de SAS (assinatura de acesso compartilhado) e transcrições de recebimento de forma assíncrona.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: b71400c3ae3c1cc6737d9194b4d94bf0b9c7efa9
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606736"
---
# <a name="why-use-batch-transcription"></a>Por que usar a transcrição do lote?

A transcrição de lote é ideal se você quer transcrever uma grande quantidade de áudio em armazenamento, como Blobs do Azure. Usando a API REST dedicada, você pode apontar para arquivos de áudio por um URI de SAS (assinatura de acesso compartilhado) e transcrições de recebimento de forma assíncrona.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscription-key"></a>Chave de Assinatura

Como acontece com todos os recursos do serviço de fala, você cria uma chave de assinatura no [Portal do Microsoft Azure](https://portal.azure.com) seguindo nosso [Guia de primeiros passos](get-started.md). Se você planeja obter transcrições de nossos modelos de linha de base, a criação de uma chave é tudo que você precisa fazer.

>[!NOTE]
> Uma assinatura standard (S0) para Serviços de Fala é necessária para usar a transcrição do lote. Chaves de assinatura gratuita (F0) não funcionarão. Para obter mais informações, consulte [preços e limites](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modelos personalizados

Se você planeja personalizar modelos de linguagem ou acústicos, siga as etapas em [Personalizar modelos acústicos](how-to-customize-acoustic-models.md) e [Personalizando modelos de linguagem](how-to-customize-language-model.md). Para usar os modelos criados na transcrição do lote, você precisa de suas IDs de modelo. Essa ID não é a ID do ponto de extremidade que você encontra na exibição de detalhes do ponto de extremidade, é a ID do modelo que pode ser recuperada quando você seleciona os detalhes dos modelos.

## <a name="the-batch-transcription-api"></a>A API de transcrição em lote

A API de Transcrição em Lote oferece transcrição de fala para texto assíncrona, juntamente com recursos adicionais. É uma API REST que expõe métodos para:

1. Criando solicitações de processamento em lote
1. Status da consulta
1. Fazer o download de transcrições

> [!NOTE]
> A API de Transcrição em Lote é ideal para call centers, que normalmente acumulam milhares de horas de áudio. Ela facilita a transcrição de grandes volumes de gravações de áudio.

### <a name="supported-formats"></a>Formatos com suporte

A API de transcrição de lote dá suporte aos seguintes formatos:

| Formatar | Codec | Bitrate | Taxa de amostragem |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 8 ou 16 kHz, mono, estéreo |
| MP3 | PCM | 16-bit | 8 ou 16 kHz, mono, estéreo |
| OGG | OPUS | 16-bit | 8 ou 16 kHz, mono, estéreo |

Para fluxos de áudio estéreo, a API de transcrição de lotes divide os canais esquerdo e direito durante a transcrição. Os dois arquivos JSON com o resultado são criados a partir de um único canal. Os timestamps por emissão permitem ao desenvolvedor criar uma transcrição final ordenada. Essa solicitação de exemplo inclui propriedades para a filtragem de conteúdo ofensivo, pontuação e carimbos de data/hora no nível da palavra.

### <a name="configuration"></a>Configuração

Parâmetros de configuração são fornecidos como JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> A API de Transcrição em Lote usa um serviço REST para solicitar transcrições, seus status e resultados associados. É possível usar a API de qualquer linguagem. A próxima seção descreve como a API é usada.

### <a name="configuration-properties"></a>Propriedades de configuração

Use essas propriedades opcionais para configurar a transcrição:

| Parâmetro | DESCRIÇÃO |
|-----------|-------------|
| `ProfanityFilterMode` | Especifica como lidar com palavrões em resultados de reconhecimento. Os valores aceitos são `none`, o que desativa a filtragem de profanação, `masked` que substitui a profanidade por asteriscos, `removed` que remove todos os palavrões do resultado ou `tags`, que adiciona tags de "profanidade". A configuração padrão é `masked`. |
| `PunctuationMode` | Especifica como manipular a pontuação nos resultados do reconhecimento. Os valores aceitos são `none`, o que desativa a pontuação, `dictated` que implica pontuação explícita, `automatic` que permite ao decodificador lidar com pontuação ou `dictatedandautomatic`, o que implica em sinais de pontuação ditados ou automáticos. |
 | `AddWordLevelTimestamps` | Especifica se os carimbos de data/hora no nível da palavra devem ser adicionados à saída. Os valores aceitos são `true`, o que habilita os carimbos de data/hora no nível da palavra e `false` (o valor padrão) para desabilitá-los. |
 | `AddSentiment` | Especifica o sentimento deve ser adicionado à declaração. Os valores aceitos são `true` que permite que o sentimento por expressão e `false` (o valor padrão) para desabilitá-lo. |
 | `AddDiarization` | Especifica que alalysis diarization deve ser executada na entrada que deve ser contendo duas vozes de canal mono. Os valores aceitos são `true` que permite que diarization e `false` (o valor padrão) para desabilitá-lo. Ele também requer `AddWordLevelTimestamps` a ser definido como true.|

### <a name="storage"></a>Armazenamento

O lote dá suporte a transcrição [armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) para a leitura de áudio e transcrições de gravação para o armazenamento.

## <a name="webhooks"></a>webhooks

Sondagem de status de transcrição pode não tenha o melhor desempenho, ou fornecer a melhor experiência de usuário. Para sondar o status, você pode registrar retornos de chamada, o qual o cliente serão notificado quando tem concluído a tarefas de transcrição de longa execução.

Para obter mais detalhes, consulte [Webhooks](webhooks.md).

## <a name="speaker-separation-diarization"></a>Separação de alto-falante (Diarization)

Diarization é o processo de separar os alto-falantes em uma parte de áudio. Nosso pipeline de lote dá suporte a Diarization e é capaz de reconhecer dois alto-falantes em gravações de canal mono.

Para solicitar que a sua solicitação de transcrição de áudio é processada para diarization, você simplesmente precisa adicionar o parâmetro relevante na solicitação HTTP, conforme mostrado abaixo.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

Carimbos de hora de nível do Word também teria que ser ' Ativado ' pois indicam os parâmetros na solicitação acima.

O áudio correspondente conterá os alto-falantes identificados por um número (atualmente, damos suporte a apenas duas vozes, para que os alto-falantes serão identificados como ' 1 de alto-falante ' e 'Alto-falante 2') seguido pela saída de transcrição.

Observe também que Diarization não está disponível em gravações em estéreo. Além disso, todos os JSON saída conterá a marca de alto-falante. Se diarization não for usado, ele mostrará ' alto-falante: Nulo ' na saída JSON.

> [!NOTE]
> Diarization está disponível em todas as regiões e para todas as localidades!

## <a name="sentiment"></a>Sentimento

Sentimento é um novo recurso na API de transcrição de lote e é um recurso importante no domínio do Centro de chamada. Os clientes podem usar o `AddSentiment` parâmetros às suas solicitações para

1.  Obtenha informações sobre a satisfação do cliente
2.  Obter informações sobre o desempenho dos agentes (levando as chamadas de equipe)
3.  Identificar o ponto no tempo quando uma chamada levou um turno em uma direção negativa
4.  Identificar o que deu bem ao transformar chamadas negativas positivo
5.  Identificar clientes como e quais não gosto sobre um produto ou um serviço

Sentimento foi classificado por segmento de áudio, onde um segmento de áudio é definido como a janela de tempo entre o início da declaração (deslocamento) e a latência de detecção do final do fluxo de bytes. Todo o texto dentro desse segmento é usado para calcular o sentimento. Nós não calculará nenhum valor de sentimento agregado para a chamada inteira ou a fala inteira de cada canal. Essas agregações são deixadas para o proprietário do domínio ainda mais se aplicam.

Sentimento foi aplicado a forma léxica.

Um exemplo de saída JSON é semelhante a abaixo:

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```
O recurso usa um modelo de sentimento, que está atualmente em versão Beta.

## <a name="sample-code"></a>Exemplo de código

Exemplos completos estão disponíveis na [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples) dentro de `samples/batch` subdiretório.

Você precisa personalizar o código de exemplo com as informações de assinatura, a região de serviço, o URI de SAS que aponta para o arquivo de áudio a transcrever e IDs de modelo caso deseje usar um modelo de linguagem ou acústico personalizado.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

O código de exemplo configurará o cliente e enviará a solicitação de transcrição. Em seguida, ele sondará informações de status e imprimirá os detalhes sobre o progresso da transcrição.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Para detalhes completos sobre as chamadas anteriores, confira o nosso [documento Swagger](https://westus.cris.ai/swagger/ui/index). Para o exemplo completo mostrado aqui, acesse [GitHub](https://aka.ms/csspeech/samples) no `samples/batch` subdiretório.

Anote a configuração assíncrona de áudio de lançamento e receber o status de transcrição. O cliente que você cria é um cliente HTTP .NET. Há um método `PostTranscriptions` para enviar os detalhes do arquivo de áudio e um método `GetTranscriptions` para receber os resultados. `PostTranscriptions` retorna um identificador e `GetTranscriptions` o usa para criar um identificador para obter o status de transcrição.

O código de amostra atual não especifica um modelo personalizado. O serviço usa os modelos de linha de base para transcrever o arquivo ou arquivos. Para especificar os modelos, você pode passar o mesmo método que os IDs de modelo para o modelo acústico e de idioma.

> [!NOTE]
> Para transcrições de linha de base, você não precisa declarar a ID para os modelos de linha de base. Se você especificar apenas um idioma, a ID de modelo (e nenhuma ID de modelo acústico), um modelo acústico correspondente será selecionado automaticamente. Se você especificar apenas uma ID de modelo acústico, um modelo de idioma correspondente será selecionado automaticamente.

## <a name="download-the-sample"></a>Baixar o exemplo

Você pode encontrar o exemplo no diretório `samples/batch` do [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples).

> [!NOTE]
> Trabalhos em lotes de transcrição são agendados por melhor esforço, não há nenhum tempo estimado para quando um trabalho será alterado para o estado de execução. Uma vez no estado de execução, a transcrição real é processada mais rapidamente do que o áudio em tempo real.

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
