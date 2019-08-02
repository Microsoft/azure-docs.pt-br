---
title: Preparar dados de teste para o serviço de Fala Personalizada-fala
titleSuffix: Azure Cognitive Services
description: Se você estiver testando para ver quão precisas o reconhecimento de fala da Microsoft é ou treinando seus próprios modelos, você precisará de dados (na forma de áudio e/ou texto). Nesta página, abordamos os tipos de dados, como eles são usados e como gerenciá-los.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fad0fada4d9dd888b0b2a37b59e4eac1e016aec4
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663591"
---
# <a name="prepare-data-for-custom-speech"></a>Preparar dados para Fala Personalizada

Se você estiver testando para ver quão precisas o reconhecimento de fala da Microsoft é ou treinando seus próprios modelos, você precisará de dados na forma de áudio e texto. Nesta página, abordamos os tipos de dados, como eles são usados e como gerenciá-los.

## <a name="data-types"></a>Tipos de dados

Esta tabela lista os tipos de dados aceitos, quando cada tipo de dados deve ser usado e a quantidade recomendada. Nem todos os tipos de dados são necessários para criar um modelo. Os requisitos de dados irão variar dependendo se você estiver criando um teste ou treinando um modelo.

| Tipo de dados | Uso de testes | Quantidade | Usado para treinamento | Quantidade |
|-----------|-----------------|----------|-------------------|----------|
| [Áudio](#audio-data-for-testing) | Sim<br>Usado para inspeção visual | mais de 5 arquivos de áudio | Não | N/a |
| [Áudio + transcrições com rótulo humano](#audio--human-labeled-transcript-data-for-testingtraining) | Sim<br>Usado para avaliar a precisão | 0,5-5 horas de áudio | Sim | 1 a 1.000 horas de áudio |
| [Texto relacionado](#related-text-data-for-training) | Não | N/a | Sim | 1-200 MB de texto relacionado |

Os arquivos devem ser agrupados por tipo em um conjunto de um e carregados como um arquivo zip. Cada conjunto de dados só pode conter um único tipo de dado.

## <a name="upload-data"></a>Carregar dados

Quando você estiver pronto para carregar seus dados, clique em **carregar dados** para iniciar o assistente e criar seu primeiro conjunto. Você será solicitado a selecionar um tipo de dados de fala para o seu conjunto, antes de permitir que você carregue seus dados.

![Selecionar áudio no portal de fala](./media/custom-speech/custom-speech-select-audio.png)

Cada conjunto de dados que você carrega deve atender aos requisitos para o tipo de dado que você escolher. É importante formatar corretamente os dados antes que eles sejam carregados. Isso garante que os dados serão processados com precisão pelo serviço de Fala Personalizada. Os requisitos são listados nas seções a seguir.

Depois que o conjunto de seus conjuntos de um for carregado, você terá algumas opções:

* Você pode navegar até a guia **teste** e inspecionar visualmente apenas áudio ou áudio + dados de transcrição com rótulo humano.
* Você pode navegar até a guia **treinamento** e usar áudio + dados de transcrição humana ou dados de texto relacionados para treinar um modelo personalizado.

## <a name="audio-data-for-testing"></a>Dados de áudio para teste

Os dados de áudio são ideais para testar a precisão do modelo de fala-para-texto de linha de base da Microsoft ou de um modelo personalizado. Tenha em mente que os dados de áudio são usados para inspecionar a precisão da fala em relação ao desempenho de um modelo específico. Se você estiver procurando quantificar a precisão de um modelo, use [áudio + dados de transcrição com rótulo humano](#audio--human-labeled-transcript-data-for-testingtraining).

Use esta tabela para garantir que os arquivos de áudio estejam formatados corretamente para uso com Fala Personalizada:

| Propriedade | Valor |
|----------|-------|
| Formato de arquivo | RIFF (WAV) |
| Taxa de amostra | 8\.000 Hz ou 16.000 Hz |
| Canais | 1 (mono) |
| Comprimento máximo por áudio | 2 horas |
| Formato de exemplo | PCM, 16 bits |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 2 GB |

Se o seu áudio não atender a essas propriedades ou se você quiser verificar se ele faz isso, sugerimos baixar o [Sox](http://sox.sourceforge.net) para verificar ou converter o áudio. Abaixo estão alguns exemplos de como cada uma dessas atividades pode ser feita por meio da linha de comando:

| Atividade | Descrição | Comando Sox |
|----------|-------------|-------------|
| Verificar o formato de áudio | Use este comando para verificar o formato do arquivo de áudio. | `sox --i <filename>` |
| Converter formato de áudio | Use este comando para converter o arquivo de áudio em um único canal, 16 bits, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Áudio + dados de transcrição com rótulo humano para teste/treinamento

Para medir a precisão da precisão de fala para texto da Microsoft ao processar seus arquivos de áudio, você deve fornecer transcrições com rótulo humano (palavra por palavra) para comparação. Embora a transcrição com rótulo humano sempre seja demorada, é necessário avaliar a precisão e treinar o modelo para seus casos de uso. Tenha em mente que os aprimoramentos no reconhecimento serão tão bons quanto os dados fornecidos. Por esse motivo, é importante que apenas transcrições de alta qualidade sejam carregadas.  

| Propriedade | Valor |
|----------|-------|
| Formato de arquivo | RIFF (WAV) |
| Taxa de amostra | 8\.000 Hz ou 16.000 Hz |
| Canais | 1 (mono) |
| Comprimento máximo por áudio | 60 s |
| Formato de exemplo | PCM, 16 bits |
| Formato de arquivo | .zip |
| Tamanho máximo do zip | 2 GB |

Para resolver problemas como exclusão ou substituição de palavras, uma quantidade significativa de dados é necessária para melhorar o reconhecimento. Em geral, é recomendável fornecer transcrições de palavra por palavra por aproximadamente 10 a 1.000 horas de áudio. As transcrições para todos os arquivos WAV devem estar contidas em um único arquivo de texto sem formatação. Cada linha do arquivo de transcrição deve conter o nome de um dos arquivos de áudio, seguido pela transcrição correspondente. O nome do arquivo e transcrição devem ser separados por uma tabulação (\t).

  Por exemplo:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> A transcrição deve ser codificada como BOM (marca de ordem de byte) UTF-8.

As transcrições são normalizadas para texto para processamento pelo sistema. No entanto, há algumas normalizações importantes que devem ser executadas pelo usuário _antes_ de carregar os dados para o Serviço de Fala Personalizado. Para o idioma apropriado a ser usado ao preparar suas transcrições, consulte [como criar uma transcrição rotulada por pessoas](how-to-custom-speech-human-labeled-transcriptions.md)

Depois de coletar os arquivos de áudio e as transcrições correspondentes, eles devem ser empacotados como um único arquivo. zip antes de carregar para o portal de Fala Personalizada. Este é um conjunto de exemplo com três arquivos de áudio e um arquivo de transcrição com rótulo humano:

![Selecionar áudio no portal de fala](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Dados de texto relacionados para treinamento

Se você tiver nomes de produtos ou recursos que sejam exclusivos e quiser ter certeza de que eles são reconhecidos corretamente, é importante incluir dados de texto relacionados para treinamento. Dois tipos de dados de texto relacionados podem ser fornecidos para melhorar o reconhecimento:

| Tipo de dados | Como esses dados aprimoram o reconhecimento |
|-----------|------------------------------------|
| Declarações e/ou frases | Eles podem melhorar a precisão ao reconhecer nomes de produtos ou vocabulário específico do setor dentro do contexto de uma frase. |
| Pronúncias | Eles podem melhorar a pronúncia de termos, acrônimos ou outras palavras incomuns, com pronúncias indefinidas. |

Declarações pode ser fornecido como um único ou vários arquivos de texto. Quanto mais perto os dados de texto forem falados, maior será a probabilidade de que a precisão seja melhorada. As pronúncias devem ser fornecidas como um único arquivo de texto. Tudo pode ser empacotado como um único arquivo zip e carregado no portal de Fala Personalizada.

### <a name="guidelines-to-create-an-utterances-file"></a>Diretrizes para criar um arquivo declarações

Para criar um modelo personalizado usando texto relacionado, você precisará fornecer uma lista de exemplos de declarações. Essas declarações não precisam ser frases completas ou gramaticalmente corretas, mas devem refletir com precisão a entrada falada que você espera na produção. Se você quiser que determinados termos tenham um aumento de peso, poderá adicionar várias frases ao arquivo de dados relacionado que inclua esses termos específicos.

Use esta tabela para garantir que o arquivo de dados relacionado para declarações esteja formatado corretamente:

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | BOM para UTF-8 |
| Nº de enunciados por linha | 1 |
| Tamanho máximo do arquivo | 200 MB |

Além disso, você desejará considerar as seguintes restrições:

* Evite repetir caracteres mais de quatro vezes. Por exemplo: "aaaa" ou "uuuu".
* Não use caracteres especiais ou caracteres UTF-8 acima de U + 00A1.
* Os URIs serão rejeitados.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Diretrizes para criar um arquivo de pronúncia

Se houver termos incomuns sem pronúncias padrão que seus usuários irão encontrar ou usar, você poderá fornecer um arquivo de pronúncia personalizado para melhorar o reconhecimento.

> [!IMPORTANT]
> Não é recomendável usar esse recurso para alterar a pronúncia de palavras comuns.

Isso inclui exemplos de um expressão falado e uma pronúncia personalizada para cada um:

| Formulário reconhecido/exibido | Forma falada |
|--------------|--------------------------|
| 3CPO | três p e r |  
| CNTK | c n t k |
| IEEE1394 | i triplo e |

O formulário falado é a seqüência fonética escrita. Ele pode ser composto por letras, palavras, sílabas ou uma combinação de todos os três.

A pronúncia personalizada está disponível em inglês (en-US) e alemão (de-DE). Esta tabela mostra os caracteres com suporte por idioma:

| Idioma | Localidade | Personagens |
|----------|--------|------------|
| Inglês | pt-BR | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Alemão | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Use esta tabela para garantir que o arquivo de dados relacionado para pronúncias esteja formatado corretamente. Os arquivos de pronúncia são pequenos e não devem exceder alguns KBs.

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | A BOM UTF-8 (ANSI também tem suporte para inglês) |
| número de pronúncias por linha | 1 |
| Tamanho máximo do arquivo | 1 MB (1 KB para camada gratuita) |

## <a name="next-steps"></a>Próximas etapas

* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
* [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar seu modelo](how-to-custom-speech-deploy-model.md)
