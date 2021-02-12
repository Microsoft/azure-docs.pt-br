---
title: Preparar dados para o serviço de Fala Personalizada-fala
titleSuffix: Azure Cognitive Services
description: Ao testar a precisão do reconhecimento de fala da Microsoft ou treinar seus modelos personalizados, você precisará de dados de áudio e texto. Nesta página, abordamos os tipos de dados, como usá-los e gerenciá-los.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 605bae706bbc1db2e008b8d050cbba9eacd16933
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98702195"
---
# <a name="prepare-data-for-custom-speech"></a>Preparar dados para Fala Personalizada

Ao testar a precisão do reconhecimento de fala da Microsoft ou treinar seus modelos personalizados, você precisará de dados de áudio e texto. Nesta página, abordamos os tipos de dados de que um modelo de fala personalizado precisa.

## <a name="data-diversity"></a>Diversidade de dados

O texto e o áudio usados para testar e treinar um modelo personalizado precisam incluir exemplos de um conjunto variado de alto-falantes e cenários que você precisa que seu modelo reconheça.
Considere esses fatores ao coletar dados para treinamento e teste de modelo personalizado:

* Os dados de áudio de texto e fala precisam abranger os tipos de instruções verbal que os usuários farão ao interagir com seu modelo. Por exemplo, um modelo que gera e reduz o treinamento de necessidades de temperatura em instruções que as pessoas podem fazer para solicitar tais alterações.
* Seus dados precisam incluir todas as variações de fala que seu modelo precisará reconhecer. Muitos fatores podem variar a fala, incluindo acentos, dialetos, combinação de idiomas, idade, sexo, Tom de voz, nível de estresse e hora do dia.
* Você deve incluir exemplos de ambientes diferentes (interno, externo, ruído de estrada) em que seu modelo será usado.
* O áudio deve ser coletado usando dispositivos de hardware que o sistema de produção usará. Se seu modelo precisar identificar a fala registrada em dispositivos de gravação de qualidade variável, os dados de áudio que você fornecer para treinar seu modelo também deverão representar esses diversos cenários.
* Você pode adicionar mais dados ao seu modelo posteriormente, mas tome cuidado para manter o conjunto variado e representativo de suas necessidades de projeto.
* A inclusão de dados que *não* estão em suas necessidades de reconhecimento de modelo personalizado pode prejudicar a qualidade do reconhecimento em geral, portanto, não inclua dados que seu modelo não precise transcrever.

Um modelo treinado em um subconjunto de cenários só pode funcionar bem nesses cenários. Escolha cuidadosamente os dados que representam o escopo completo dos cenários que você precisa que seu modelo personalizado reconheça.

> [!TIP]
> Comece com pequenos conjuntos de dados de exemplo que correspondem ao idioma e acústicos que seu modelo encontrará.
> Por exemplo, registre uma amostra pequena, mas representativa de áudio no mesmo hardware e no mesmo ambiente acústico que seu modelo encontrará em cenários de produção.
> Pequenos conjuntos de dados representativos podem expor problemas antes de você se investir em reunir conjuntos de dados muito maiores para treinamento.

## <a name="data-types"></a>Tipos de dados

Esta tabela lista os tipos de dados aceitos, quando cada tipo de dados deve ser usado e a quantidade recomendada. Nem todos os tipos de dados são necessários para criar um modelo. Os requisitos de dados irão variar dependendo se você estiver criando um teste ou treinando um modelo.

| Tipo de dados | Usado para teste | Quantidade recomendada | Usado para treinamento | Quantidade recomendada |
|-----------|-----------------|----------|-------------------|----------|
| [Sonoro](#audio-data-for-testing) | Sim<br>Usado para inspeção visual | mais de 5 arquivos de áudio | Não | N/D |
| [Áudio + transcrições com rótulo humano](#audio--human-labeled-transcript-data-for-testingtraining) | Sim<br>Usado para avaliar a precisão | 0,5 a 5 horas de áudio | Sim | 1-20 horas de áudio |
| [Texto relacionado](#related-text-data-for-training) | Não | N/A | Sim | 1-200 MB de texto relacionado |

Ao treinar um novo modelo, comece com o [texto relacionado](#related-text-data-for-training). Esses dados já melhorarão o reconhecimento de termos e frases especiais. O treinamento com texto é muito mais rápido do que o treinamento com áudio (minutos versus dias).

Os arquivos devem ser agrupados por tipo em um conjunto de um e carregados como um arquivo. zip. Cada conjunto de dados só pode conter um único tipo de dado.

> [!TIP]
> Para começar rapidamente, considere o uso de dados de exemplo. Consulte este repositório GitHub para obter <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">dados <span class="docon docon-navigate-external x-hidden-focus"></span> de fala personalizada de exemplo</a>

## <a name="upload-data"></a>Carregar dados

Para carregar seus dados, navegue até o <a href="https://speech.microsoft.com/customspeech" target="_blank">Portal <span class="docon docon-navigate-external x-hidden-focus"></span> de fala personalizada </a>. No portal, clique em **carregar dados** para iniciar o assistente e criar o seu primeiro conjunto. Você será solicitado a selecionar um tipo de dados de fala para o seu conjunto, antes de permitir que você carregue seus dados.

![Captura de tela que realça a opção de carregamento de áudio do portal de fala.](./media/custom-speech/custom-speech-select-audio.png)

Cada conjunto de dados que você carrega deve atender aos requisitos para o tipo de dado que você escolher. Seus dados devem ser formatados corretamente antes de serem carregados. Os dados formatados corretamente garantem que serão processados com precisão pelo serviço de Fala Personalizada. Os requisitos são listados nas seções a seguir.

Depois que o conjunto de seus conjuntos de um for carregado, você terá algumas opções:

* Você pode navegar até a guia **teste** e inspecionar visualmente apenas áudio ou áudio + dados de transcrição com rótulo humano.
* Você pode navegar até a guia **treinamento** e usar áudio + dados de transcrição humana ou dados de texto relacionados para treinar um modelo personalizado.

## <a name="audio-data-for-testing"></a>Dados de áudio para teste

Os dados de áudio são ideais para testar a precisão do modelo de fala-para-texto de linha de base da Microsoft ou de um modelo personalizado. Tenha em mente que os dados de áudio são usados para inspecionar a precisão da fala em relação ao desempenho de um modelo específico. Se você estiver procurando quantificar a precisão de um modelo, use [áudio + dados de transcrição com rótulo humano](#audio--human-labeled-transcript-data-for-testingtraining).

Use esta tabela para garantir que os arquivos de áudio estejam formatados corretamente para uso com Fala Personalizada:

| Propriedade                 | Valor                 |
|--------------------------|-----------------------|
| Formato de arquivo              | RIFF (WAV)            |
| Taxa de amostragem              | 8.000 Hz ou 16.000 Hz |
| Canais                 | 1 (mono)              |
| Comprimento máximo por áudio | 2 horas               |
| Formato de exemplo            | PCM, 16 bits           |
| Formato de arquivo           | .zip                  |
| Tamanho máximo de arquivo     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Ao carregar dados de treinamento e teste, o tamanho do arquivo. zip não pode exceder 2 GB. Se você precisar de mais dados para treinamento, divida-os em vários arquivos. zip e carregue-os separadamente. Posteriormente, você pode optar por treinar a partir de *vários* conjuntos de valores. No entanto, você só pode testar a partir de um *único* conjunto de uma.

Use <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">o <span class="docon docon-navigate-external x-hidden-focus"></span> Sox</a> para verificar as propriedades de áudio ou converter o áudio existente nos formatos apropriados. Abaixo estão alguns exemplos de como cada uma dessas atividades pode ser feita por meio da linha de comando SoX:

| Atividade | Descrição | Comando SoX |
|----------|-------------|-------------|
| Verificar o formato de áudio | Use este comando para verificar<br>o formato do arquivo de áudio. | `sox --i <filename>` |
| Converter formato de áudio | Use este comando para converter<br>o arquivo de áudio para um único canal, 16 bits, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Áudio + dados de transcrição com rótulo humano para teste/treinamento

Para medir a precisão da precisão de fala para texto da Microsoft ao processar seus arquivos de áudio, você deve fornecer transcrições com rótulo humano (palavra por palavra) para comparação. Embora a transcrição com rótulo humano sempre seja demorada, é necessário avaliar a precisão e treinar o modelo para seus casos de uso. Tenha em mente que os aprimoramentos no reconhecimento serão tão bons quanto os dados fornecidos. Por esse motivo, é importante que apenas transcrições de alta qualidade sejam carregadas.

Os arquivos de áudio podem ter silêncio no início e no final da gravação. Se possível, inclua pelo menos um meio segundo de silêncio antes e depois da fala em cada arquivo de exemplo. Embora o áudio com baixo volume de gravação ou ruído de fundo com interrupção não seja útil, ele não deve prejudicar seu modelo personalizado. Sempre considere atualizar seus microfones e o hardware de processamento de sinal antes de coletar amostras de áudio.

| Propriedade                 | Valor                               |
|--------------------------|-------------------------------------|
| Formato de arquivo              | RIFF (WAV)                          |
| Taxa de amostragem              | 8.000 Hz ou 16.000 Hz               |
| Canais                 | 1 (mono)                            |
| Comprimento máximo por áudio | 2 horas (teste)/60 s (treinamento) |
| Formato de exemplo            | PCM, 16 bits                         |
| Formato de arquivo           | .zip                                |
| Tamanho máximo do zip         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Ao carregar dados de treinamento e teste, o tamanho do arquivo. zip não pode exceder 2 GB. Você só pode testar a partir de um *único* conjunto de um, certifique-se de mantê-lo dentro do tamanho apropriado do arquivo. Além disso, cada arquivo de treinamento não pode exceder 60 segundos, caso contrário, ocorrerá um erro.

Para resolver problemas como exclusão ou substituição de palavras, uma quantidade significativa de dados é necessária para melhorar o reconhecimento. Em geral, é recomendável fornecer transcrições de palavra por palavra por aproximadamente de 10 a 20 horas de áudio. As transcrições para todos os arquivos WAV devem estar contidas em um único arquivo de texto sem formatação. Cada linha do arquivo de transcrição deve conter o nome de um dos arquivos de áudio, seguido pela transcrição correspondente. O nome do arquivo e transcrição devem ser separados por uma tabulação (\t).

Por exemplo:

<!-- The following example contains tabs. Don't accidentally convert these into spaces. -->

```input
speech01.wav    speech recognition is awesome
speech02.wav    the quick brown fox jumped all over the place
speech03.wav    the lazy dog was not amused
```

> [!IMPORTANT]
> A transcrição deve ser codificada como BOM (marca de ordem de byte) UTF-8.

As transcrições são normalizadas para texto para processamento pelo sistema. No entanto, há algumas normalizações importantes que devem ser feitas antes de carregar os dados no Speech Studio. Para o idioma apropriado a ser usado ao preparar suas transcrições, consulte [como criar uma transcrição rotulada por pessoas](how-to-custom-speech-human-labeled-transcriptions.md)

Depois de coletar os arquivos de áudio e as transcrições correspondentes, empacote-os como um único arquivo. zip antes de carregar no <a href="https://speech.microsoft.com/customspeech" target="_blank">Portal <span class="docon docon-navigate-external x-hidden-focus"></span> de fala personalizada </a>. Veja abaixo um exemplo de conjunto de exemplos com três arquivos de áudio e um arquivo de transcrição com rótulo humano:

> [!div class="mx-imgBorder"]
> ![Selecionar áudio no portal de fala](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

Consulte [configurar sua conta do Azure](custom-speech-overview.md#set-up-your-azure-account) para obter uma lista de regiões recomendadas para suas assinaturas de serviço de fala. A configuração das assinaturas de fala em uma dessas regiões reduzirá o tempo necessário para treinar o modelo. Nessas regiões, o treinamento pode processar cerca de 10 horas de áudio por dia, em comparação a apenas uma hora por dia em outras regiões. Se o treinamento do modelo não puder ser concluído em uma semana, o modelo será marcado como com falha.

Nem todos os modelos de base dão suporte ao treinamento com dados de áudio. Se o modelo base não oferecer suporte a ele, o serviço irá ignorar o áudio e apenas treinar com o texto das transcrições. Nesse caso, o treinamento será o mesmo que o treinamento com o texto relacionado.

## <a name="related-text-data-for-training"></a>Dados de texto relacionados para treinamento

Os nomes de produtos ou recursos que são exclusivos devem incluir dados de texto relacionados para treinamento. O texto relacionado ajuda a garantir o reconhecimento correto. Dois tipos de dados de texto relacionados podem ser fornecidos para melhorar o reconhecimento:

| Tipo de dados | Como esses dados aprimoram o reconhecimento |
|-----------|------------------------------------|
| Sentenças (declarações) | Melhore a precisão ao reconhecer nomes de produtos ou vocabulário específico do setor dentro do contexto de uma frase. |
| Pronúncias | Melhore a pronúncia de termos, acrônimos ou outras palavras incomuns, com pronúncias indefinidas. |

As frases podem ser fornecidas como um único arquivo de texto ou vários arquivos de texto. Para melhorar a precisão, use dados de texto que estejam mais próximos do declarações falado esperado. As pronúncias devem ser fornecidas como um único arquivo de texto. Tudo pode ser empacotado como um único arquivo zip e carregado no <a href="https://speech.microsoft.com/customspeech" target="_blank">Portal <span class="docon docon-navigate-external x-hidden-focus"></span> de fala personalizada </a>.

O treinamento com texto relacionado geralmente é concluído em alguns minutos.

### <a name="guidelines-to-create-a-sentences-file"></a>Diretrizes para criar um arquivo de sentenças

Para criar um modelo personalizado usando frases, você precisará fornecer uma lista de exemplos de declarações. Declarações _não_ precisam ser concluídos ou gramaticalmente corretos, mas eles devem refletir com precisão a entrada falada que você espera na produção. Se você quiser que determinados termos tenham um aumento de peso, adicione várias frases que incluam esses termos específicos.

Como orientação geral, a adaptação do modelo é mais eficaz quando o texto de treinamento é o mais próximo possível do texto real esperado na produção. O jargão e as frases específicas do domínio que você está direcionando para aprimorar devem ser incluídos no texto de treinamento. Quando possível, tente ter uma frase ou palavra-chave controlada em uma linha separada. Para palavras-chave e frases que são importantes para você (por exemplo, nomes de produtos), você pode copiá-las algumas vezes. Mas tenha em mente, não copie muito, isso pode afetar a taxa geral de reconhecimento.

Use esta tabela para garantir que o arquivo de dados relacionado para declarações esteja formatado corretamente:

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | BOM para UTF-8 |
| Nº de enunciados por linha | 1 |
| Tamanho máximo do arquivo | 200 MB |

Além disso, você desejará considerar as seguintes restrições:

* Evite repetir caracteres, palavras ou grupos de palavras mais de três vezes. Por exemplo: "aaaa", "Sim Sim Sim", ou "é isso é que isso é isso". O serviço de fala pode soltar as linhas com muitas repetições.
* Não use caracteres especiais ou caracteres UTF-8 acima `U+00A1` .
* Os URIs serão rejeitados.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Diretrizes para criar um arquivo de pronúncia

Se houver termos incomuns sem pronúncias padrão que seus usuários irão encontrar ou usar, você poderá fornecer um arquivo de pronúncia personalizado para melhorar o reconhecimento.

> [!IMPORTANT]
> Não é recomendável usar arquivos de pronúncia personalizados para alterar a pronúncia de palavras comuns.

Isso inclui exemplos de um expressão falado e uma pronúncia personalizada para cada um:

| Formulário reconhecido/exibido | Forma falada |
|--------------|--------------------------|
| 3CPO | três p e r |
| CNTK | c n t k |
| IEEE1394 | i triplo e |

O formulário falado é a seqüência fonética escrita. Ele pode ser composto por letras, palavras, sílabas ou uma combinação de todos os três.

A pronúncia personalizada está disponível em inglês ( `en-US` ) e alemão ( `de-DE` ). Esta tabela mostra os caracteres com suporte por idioma:

| Idioma | Locale | Characters |
|----------|--------|------------|
| Inglês | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Alemão | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Use a tabela a seguir para garantir que o arquivo de dados relacionado para pronúncias esteja formatado corretamente. Os arquivos de pronúncia são pequenos e devem ter apenas alguns quilobytes de tamanho.

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | A BOM UTF-8 (ANSI também tem suporte para inglês) |
| número de pronúncias por linha | 1 |
| Tamanho máximo do arquivo | 1 MB (1 KB para camada gratuita) |

## <a name="next-steps"></a>Próximas etapas

* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
* [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar o seu modelo](./how-to-custom-speech-train-model.md)