---
title: Como preparar dados para o serviço de fala de voz personalizado
titleSuffix: Azure Cognitive Services
description: Crie uma voz personalizada para sua marca com o serviço de fala. Você fornece gravações de estúdio e os scripts associados, o serviço gera um modelo de voz exclusivo ajustado para a voz gravada. Use essa voz para sintetizar a fala em seus produtos, ferramentas e aplicativos.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 6a29117ff6e5173063bc2aced87f08f1a6aea425
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559578"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Preparar dados para criar uma voz personalizada

Quando você estiver pronto para criar uma voz personalizada de conversão de texto em fala para seu aplicativo, a primeira etapa é coletar gravações de áudio e scripts associados para começar a treinar o modelo de voz. O serviço usa esses dados para criar uma voz exclusiva ajustada para corresponder à voz nas gravações. Depois de treinar a voz, você pode começar a resumir a fala em seus aplicativos.

Você pode começar com uma pequena quantidade de dados para criar uma prova de conceito. No entanto, quanto mais dados você fornecer, mais natural será o som de sua voz personalizada. Antes de treinar seu próprio modelo de voz de conversão de texto em fala, você precisará de gravações de áudio e das transcrições de texto associadas. Nesta página, examinaremos os tipos de dados, como eles são usados e como gerenciar cada um deles.

## <a name="data-types"></a>Tipos de dados

Um conjunto de registros de treinamento de voz inclui gravações de áudio e um arquivo de texto com as transcrições associadas. Cada arquivo de áudio deve conter um único expressão (uma única frase ou uma única vez para um sistema de caixa de diálogo) e ter menos de 15 segundos de comprimento.

Em alguns casos, talvez você não tenha o conjunto de tempo pronto e desejará testar o treinamento de voz personalizado com os arquivos de áudio disponíveis, curtos ou longos, com ou sem transcrições. Fornecemos ferramentas (beta) para ajudá-lo a segmentar seu áudio em declarações e preparar transcrições usando a [API de transcrição do lote](batch-transcription.md).

Esta tabela lista os tipos de dados e como cada um é usado para criar um modelo de voz de conversão de texto em fala personalizado.

| Tipo de dados | Descrição | Quando usar | Serviço adicional necessário | Quantidade para treinar um modelo | Localidade (s) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Declarações individuais + transcrição correspondente** | Uma coleção (. zip) de arquivos de áudio (. wav) como declarações individuais. Cada arquivo de áudio deve ter 15 segundos ou menos de comprimento, emparelhado com uma transcrição formatada (. txt). | Gravações profissionais com transcrições de correspondência | Pronto para treinamento. | Nenhum requisito rígido para en-US e zh-CN. Mais de 2.000 declarações diferentes para outras localidades. | Todas as localidades de voz personalizadas |
| **Áudio longo + transcrição (beta)** | Uma coleção (. zip) de arquivos de áudio longos e não segmentados (mais de 20 segundos), emparelhados com uma transcrição (. txt) que contém todas as palavras faladas. | Você tem arquivos de áudio e transcrições correspondentes, mas eles não são segmentados em declarações. | Segmentação (usando a transcrição do lote).<br>Transformação formato de áudio, quando necessário. | Nenhum requisito rígido para en-US e zh-CN. | `en-US` e `zh-CN` |
| **Somente áudio (beta)** | Uma coleção (. zip) de arquivos de áudio sem transcrição. | Você tem apenas arquivos de áudio disponíveis, sem transcrições. | Segmentação + geração de transcrição (usando a transcrição do lote).<br>Transformação formato de áudio, quando necessário.| Nenhum requisito rígido para `en-US` o `zh-CN`e o. | `en-US` e `zh-CN` |

Os arquivos devem ser agrupados por tipo em um conjunto de um e carregados como um arquivo zip. Cada conjunto de dados só pode conter um único tipo de dado.

> [!NOTE]
> O número máximo de conjuntos de usuários que podem ser importados por assinatura é de 10 arquivos. zip para usuários de assinatura gratuita (F0) e 500 para usuários de assinatura padrão (S0).

## <a name="individual-utterances--matching-transcript"></a>Declarações individuais + transcrição correspondente

Você pode preparar gravações de declarações individuais e a transcrição de correspondência de duas maneiras. Escreva um script e leia-o por um talento de voz ou use áudio publicamente disponível e o transcreve para texto. No último caso, edite os erros de fluência dos arquivos de áudio, como "um" e outros sons de preenchimento, gagueira, palavras murmuradas ou pronunciamentos incorretos.

Para produzir uma boa fonte de voz, crie as gravações em uma sala silenciosa com um microfone de alta qualidade. O volume consistente, a taxa de fala, o tom de fala e o mannerisms expressivo da fala são essenciais.

> [!TIP]
> Para criar uma voz para uso de produção, é recomendável utilizar um estúdio de gravação profissional e um talento de voz. Para mais informações, consulte [Como gravar amostras de voz para uma voz personalizada](record-custom-voice-samples.md).

### <a name="audio-files"></a>Arquivos de áudio

Cada arquivo de áudio deve conter um único expressão (uma única frase ou uma única rodada de um sistema de caixa de diálogo), com menos de 15 segundos de comprimento. Todos os arquivos devem estar no mesmo idioma falado. Não há suporte para vozes de conversão de texto em fala personalizadas em vários idiomas, com exceção do idioma inglês chinês-inglês. Cada arquivo de áudio deve ter um nome de arquivo numérico exclusivo com a extensão de nome de arquivo. wav.

Siga estas diretrizes ao preparar o áudio.

| Propriedade | Valor |
| -------- | ----- |
| Formato de arquivo | RIFF (. wav), agrupado em um arquivo. zip |
| Taxa de amostragem | Pelo menos 16.000 Hz |
| Formato de exemplo | PCM, 16 bits |
| Nome do arquivo | Numeric, com extensão. wav. Nenhum nome de arquivo duplicado é permitido. |
| Comprimento do áudio | Menos de 15 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 200 MB |

> [!NOTE]
> arquivos. wav com uma taxa de amostragem inferior a 16.000 Hz serão rejeitados. Se um arquivo. zip contiver arquivos. wav com taxas de amostra diferentes, somente aqueles iguais ou maiores que 16.000 Hz serão importados. Atualmente, o portal importa arquivos .zip de até 200 MB. No entanto, vários arquivos podem ser enviados.

### <a name="transcripts"></a>Transcrições

O arquivo de transcrição é um arquivo de texto sem formatação. Use estas diretrizes para preparar suas transcrições.

| Propriedade | Valor |
| -------- | ----- |
| Formato de arquivo | Texto sem formatação (.txt) |
| Formato de codificação | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE ou UTF-16-is. Para as codificações zh-CN, ANSI/ASCII e UTF-8 não têm suporte. |
| Nº de enunciados por linha | **Um** -cada linha do arquivo de transcrição deve conter o nome de um dos arquivos de áudio, seguido pela transcrição correspondente. O nome do arquivo e transcrição devem ser separados por uma tabulação (\t). |
| Tamanho máximo do arquivo | 50 MB |

Abaixo está um exemplo de como as transcrições são organizadas expressão por expressão em um arquivo. txt:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
É importante que as transcrições sejam 100% de transcrições precisas do áudio correspondente. Erros nas transcrições apresentarão perda de qualidade durante o treinamento.

> [!TIP]
> Ao criar vozes de conversão de texto em fala de produção, selecione declarações (ou escreva scripts) que levam em conta a cobertura fonética e a eficiência. Tendo problemas para a obtenção dos resultados você deseja? [Entre em contato com a equipe de voz personalizada](mailto:speechsupport@microsoft.com) para saber mais sobre como fazer a consulta.

## <a name="long-audio--transcript-beta"></a>Áudio longo + transcrição (beta)

Em alguns casos, talvez você não tenha um áudio segmentado disponível. Fornecemos um serviço (beta) por meio do portal de voz personalizado para ajudá-lo a segmentar arquivos de áudio longos e criar transcrições. Tenha em mente que esse serviço será cobrado em direção ao uso de sua assinatura de fala a texto.

> [!NOTE]
> O serviço de segmentação de áudio longo aproveitará o recurso de transcrição do lote de conversão de fala em texto, que dá suporte apenas a usuários de assinatura padrão (S0). Durante o processamento da segmentação, os arquivos de áudio e as transcrições também serão enviados para o serviço de Fala Personalizada para refinar o modelo de reconhecimento para que a precisão possa ser melhorada para seus dados. Nenhum dado será retido durante esse processo. Depois que a segmentação for concluída, somente os declarações segmentados e suas transcrições de mapeamento serão armazenados para download e treinamento.

### <a name="audio-files"></a>Arquivos de áudio

Siga estas diretrizes ao preparar o áudio para segmentação.

| Propriedade | Valor |
| -------- | ----- |
| Formato de arquivo | RIFF (. wav) com uma taxa de amostragem de pelo menos 16 kHz-16 bits em PCM ou. mp3 com uma taxa de bits de pelo menos 256 KBps, agrupados em um arquivo. zip |
| Nome do arquivo | Somente caracteres ASCII. Os caracteres Unicode no nome falharão (por exemplo, os caracteres chineses ou símbolos como "–"). Nenhum nome duplicado é permitido. |
| Comprimento do áudio | Mais de 20 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 200 MB |

Todos os arquivos de áudio devem ser agrupados em um arquivo zip. É OK colocar arquivos. wav e arquivos. mp3 em um zip de áudio, mas nenhuma subpasta é permitida no arquivo zip. Por exemplo, você pode carregar um arquivo ZIP contendo um arquivo de áudio chamado ' kingstory. wav ', 45-Second-Long e outro chamado ' queenstory. mp3 ', 200-Second-Long, sem nenhuma subpasta. Todos os arquivos. mp3 serão transformados no formato. wav após o processamento.

### <a name="transcripts"></a>Transcrições

Transcrições devem estar preparados para as especificações listadas nesta tabela. Cada arquivo de áudio deve ser correspondido com uma transcrição.

| Propriedade | Valor |
| -------- | ----- |
| Formato de arquivo | Texto sem formatação (. txt), agrupado em um. zip |
| Nome do arquivo | Usar o mesmo nome que o arquivo de áudio correspondente |
| Formato de codificação | UTF-8-somente BOM |
| Nº de enunciados por linha | Sem limite |
| Tamanho máximo do arquivo | 50 MILHÕES |

Todos os arquivos de transcrições nesse tipo de dados devem ser agrupados em um arquivo zip. Nenhuma subpasta é permitida no arquivo zip. Por exemplo, você carregou um arquivo ZIP contendo um arquivo de áudio chamado ' kingstory. wav ', 45 segundos de comprimento e outro chamado ' queenstory. mp3 ', 200 segundos de comprimento. Será necessário carregar outro arquivo ZIP contendo duas transcrições, uma denominada ' kingstory. txt ', a outra ' queenstory. txt '. Em cada arquivo de texto sem formatação, você fornecerá a transcrição correta completa para o áudio correspondente.

Depois que o conjunto de acordo for carregado com êxito, iremos ajudá-lo a segmentar o arquivo de áudio no declarações com base na transcrição fornecida. Você pode verificar o declarações segmentado e as transcrições correspondentes baixando o conjunto de um. As IDs exclusivas serão atribuídas ao declarações segmentado automaticamente. É importante garantir que as transcrições fornecidas sejam 100% precisas. Os erros nas transcrições podem reduzir a precisão durante a segmentação de áudio e apresentar ainda mais perda de qualidade na fase de treinamento que vem depois.

## <a name="audio-only-beta"></a>Somente áudio (beta)

Se você não tiver transcrições para suas gravações de áudio, use a opção **somente áudio** para carregar seus dados. Nosso sistema pode ajudá-lo a segmentar e transcrever seus arquivos de áudio. Tenha em mente que esse serviço contará para o uso de sua assinatura de fala em texto.

Siga estas diretrizes ao preparar o áudio.

> [!NOTE]
> O serviço de segmentação de áudio longo aproveitará o recurso de transcrição do lote de conversão de fala em texto, que dá suporte apenas a usuários de assinatura padrão (S0).

| Propriedade | Valor |
| -------- | ----- |
| Formato de arquivo | RIFF (. wav) com uma taxa de amostragem de pelo menos 16 kHz-16 bits em PCM ou. mp3 com uma taxa de bits de pelo menos 256 KBps, agrupados em um arquivo. zip |
| Nome do arquivo | Somente caracteres ASCII. Os caracteres Unicode no nome falharão (por exemplo, os caracteres chineses ou símbolos como "–"). Nenhum nome duplicado é permitido. |
| Comprimento do áudio | Mais de 20 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 200 MB |

Todos os arquivos de áudio devem ser agrupados em um arquivo zip. Nenhuma subpasta é permitida no arquivo zip. Depois que o conjunto de seus conjuntos de um for carregado com êxito, ajudaremos você a segmentar o arquivo de áudio no declarações com base em nosso serviço de transcrição do lote de fala. As IDs exclusivas serão atribuídas ao declarações segmentado automaticamente. As transcrições correspondentes serão geradas por meio do reconhecimento de fala. Todos os arquivos. mp3 serão transformados no formato. wav após o processamento. Você pode verificar o declarações segmentado e as transcrições correspondentes baixando o conjunto de um.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma voz personalizada](how-to-custom-voice-create-voice.md)
- [Orienta Registre seus exemplos de voz](record-custom-voice-samples.md)
