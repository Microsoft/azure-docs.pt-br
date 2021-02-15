---
title: Avaliar e melhorar a precisão Fala Personalizada serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste documento, você aprenderá a medir de forma quantitativa e melhorar a qualidade do nosso modelo de fala-para-texto ou do modelo personalizado. Áudio e dados de transcrição com rótulo humano são necessários para testar a precisão e 30 minutos a 5 horas de áudio representativo devem ser fornecidos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: 078118ec793530720a49a19046854e5ea4b7f5c4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388933"
---
# <a name="evaluate-and-improve-custom-speech-accuracy"></a>Avaliar e aprimorar a precisão da Fala Personalizada

Neste artigo, você aprenderá a medir de forma quantitativa e melhorar a precisão dos modelos de fala-para-texto da Microsoft ou seus próprios modelos personalizados. Áudio e dados de transcrição com rótulo humano são necessários para testar a precisão e 30 minutos a 5 horas de áudio representativo devem ser fornecidos.

## <a name="evaluate-custom-speech-accuracy"></a>Avaliar a precisão da Fala Personalizada

O padrão do setor para medir a precisão do modelo é o WER ( [taxa de erros do Word](https://en.wikipedia.org/wiki/Word_error_rate) ). O WER conta o número de palavras incorretas identificadas durante o reconhecimento e divide pelo número total de palavras fornecidas na transcrição de rótulo humano (mostrada abaixo como N). Por fim, esse número é multiplicado por 100% para calcular o WER.

![Fórmula do WER](./media/custom-speech/custom-speech-wer-formula.png)

Palavras incorretamente identificadas se enquadram em três categorias:

* Inserção (I): palavras que são adicionadas incorretamente na transcrição de hipótese
* Exclusão (D): palavras que não são detectadas na transcrição da hipótese
* Substituição (ões): palavras que foram substituídas entre referência e hipótese

Aqui está um exemplo:

![Exemplo de palavras identificadas incorretamente](./media/custom-speech/custom-speech-dis-words.png)

Se você quiser replicar as medições do WER localmente, poderá usar sclite de [SCTK](https://github.com/usnistgov/SCTK).

## <a name="resolve-errors-and-improve-wer"></a>Resolver erros e aprimorar o WER

Você pode usar o WER nos resultados de reconhecimento do computador para avaliar a qualidade do modelo que você está usando com seu aplicativo, ferramenta ou produto. Um WER de 5% a 10% é considerado uma boa qualidade e está pronto para uso. Um WER de 20% é aceitável, mas talvez você queira considerar treinamento adicional. Um WER de 30% ou mais sinais de baixa qualidade e requer personalização e treinamento.

A forma como os erros são distribuídos é importante. Quando muitos erros de exclusão são encontrados, isso geralmente ocorre devido à intensidade do sinal de áudio fraco. Para resolver esse problema, você precisará coletar dados de áudio mais perto da origem. Erros de inserção significam que o áudio foi registrado em um ambiente barulhento e crosstalk pode estar presente, causando problemas de reconhecimento. Erros de substituição geralmente são encontrados quando uma amostra insuficiente de termos específicos do domínio foi fornecida como transcrições com rótulo humano ou texto relacionado.

Ao analisar arquivos individuais, você pode determinar quais tipos de erros existem e quais erros são exclusivos para um arquivo específico. Entender os problemas no nível de arquivo ajudará você a aprimorar as melhorias.

## <a name="create-a-test"></a>Criar um teste

Se você quiser testar a qualidade do modelo de linha de base de fala para texto da Microsoft ou de um modelo personalizado que você tenha treinado, você pode comparar dois modelos lado a lado para avaliar a precisão. A comparação inclui resultados de WER e de reconhecimento. Normalmente, um modelo personalizado é comparado com o modelo de linha de base da Microsoft.

Para avaliar modelos lado a lado:

1. Entre no portal de [fala personalizada](https://speech.microsoft.com/customspeech).
2. Navegue até **conversão de fala em texto > Fala Personalizada > [nome do projeto] > teste**.
3. Clique em **Adicionar teste**.
4. Selecione **avaliar exatidão**. Dê ao teste um nome, uma descrição e selecione seu conjunto de testes de áudio e de transcrição com rótulo humano.
5. Selecione até dois modelos que você gostaria de testar.
6. Clique em **Criar**.

Depois que o teste tiver sido criado com êxito, você poderá comparar os resultados lado a lado.

### <a name="side-by-side-comparison"></a>Comparação lado a lado

Depois que o teste for concluído, indicado pela alteração do status para *êxito*, você encontrará um número do WER para ambos os modelos incluídos no teste. Clique no nome do teste para exibir a página de detalhes de teste. Essa página de detalhes lista todos os declarações no conjunto de seus conjuntos de anotações, indicando os resultados de reconhecimento dos dois modelos junto com a transcrição do conjunto de resultados enviado. Para ajudar a inspecionar a comparação lado a lado, você pode alternar vários tipos de erro, incluindo inserção, exclusão e substituição. Ao ouvir o áudio e comparar os resultados de reconhecimento em cada coluna, que mostra a transcrição de rótulo humano e os resultados de dois modelos de fala em texto, você pode decidir qual modelo atende às suas necessidades e onde são necessários treinamentos e aprimoramentos adicionais.

## <a name="improve-custom-speech-accuracy"></a>Aprimorar a precisão da Fala Personalizada

Os cenários de reconhecimento de fala variam de acordo com a qualidade e a linguagem de áudio (vocabulário e estilo de fala). A tabela a seguir examina quatro cenários comuns:

| Cenário | Qualidade de áudio | Vocabulário | Estilo de fala |
|----------|---------------|------------|----------------|
| Call center | Baixo, 8 kHz, poderia ser 2 seres humanos em 1 canal de áudio, poderia ser compactado | Estreito, exclusivo de domínio e produtos | De conversação, estruturada sem rigidez |
| Assistente de voz (como a Cortana ou uma janela de unidade por meio) | Alta, 16 kHz | Entidade pesada (títulos de música, produtos, locais) | Palavras e frases claramente declaradas |
| Ditado (mensagem instantânea, observações, pesquisa) | Alta, 16 kHz | Variado | Observação-tomando |
| Legenda codificada em vídeo | Variado, incluindo uso variado de microfone, música adicionada | Variadas, de reuniões, fala recitada, música musical | Lido, preparado ou estruturado de forma flexível |

Diferentes cenários produzem resultados de qualidade diferentes. A tabela a seguir examina como o conteúdo desses quatro cenários classifica a [taxa de erros do Word (WER)](how-to-custom-speech-evaluate-data.md). A tabela mostra quais tipos de erro são mais comuns em cada cenário.

| Cenário | Qualidade do reconhecimento de fala | Erros de inserção | Erros de exclusão | Erros de substituição |
|----------|----------------------------|------------------|-----------------|---------------------|
| Call center | Médio (< 30% WER) | Baixo, exceto quando outras pessoas falam em segundo plano | Pode ser alto. As centrais de chamadas podem ser ruidosas e os alto-falantes sobrepostos podem confundir o modelo | Médio. Os nomes de produtos e pessoas podem causar esses erros |
| Assistente de voz | Alta (pode ser < de 10% WER) | Baixo | Baixo | Médio, devido a títulos de música, nomes de produtos ou locais |
| Ditado | Alta (pode ser < de 10% WER) | Baixo | Baixo | Alto |
| Legenda codificada em vídeo | Depende do tipo de vídeo (pode ser < 50% WER) | Baixo | Pode ser alto devido a música, ruídos, qualidade do microfone | O jargão pode causar esses erros |

A determinação dos componentes do WER (número de erros de inserção, exclusão e substituição) ajuda a determinar o tipo de dados a ser adicionado para melhorar o modelo. Use o [portal de fala personalizada](https://speech.microsoft.com/customspeech) para exibir a qualidade de um modelo de linha de base. O portal relata as taxas de erro de inserção, substituição e exclusão combinadas na taxa de qualidade do WER.

## <a name="improve-model-recognition"></a>Melhorar o reconhecimento de modelo

Você pode reduzir os erros de reconhecimento adicionando dados de treinamento no [portal de fala personalizada](https://speech.microsoft.com/customspeech). 

Planeje manter seu modelo personalizado adicionando materiais de origem periodicamente. Seu modelo personalizado precisa de treinamento adicional para ficar atento às alterações em suas entidades. Por exemplo, você pode precisar de atualizações para nomes de produtos, nomes de músicas ou novos locais de serviço.

As seções a seguir descrevem como cada tipo de dados de treinamento adicionais pode reduzir erros.

### <a name="add-related-text-sentences"></a>Adicionar frases de texto relacionadas

Ao treinar um novo modelo personalizado, comece adicionando um texto relacionado para melhorar o reconhecimento de palavras e frases específicas do domínio. Frases de texto relacionadas podem reduzir principalmente erros de substituição relacionados ao reconhecimento indesejado de palavras comuns e de palavras específicas de domínio, mostrando-os no contexto. Palavras específicas de domínio podem ser palavras incomuns ou comparadas, mas sua pronúncia deve ser simples de ser reconhecida.

> [!NOTE]
> Evite frases de texto relacionadas que incluam ruído como caracteres ou palavras não reconhecíveis.

### <a name="add-audio-with-human-labeled-transcripts"></a>Adicionar áudio com transcrições com rótulo humano

O áudio com transcrições com rótulo humano oferecerá as maiores melhorias de precisão se o áudio vier do caso de uso de destino. Os exemplos devem abranger todo o escopo de fala. Por exemplo, um Call Center para uma loja de varejo obteria a maioria das chamadas sobre swimwear e óculos durante os meses de verão. Certifique-se de que seu exemplo inclui o escopo completo de fala que você deseja detectar.

Considere estes detalhes:

* Fala Personalizada só pode capturar o contexto do Word para reduzir erros de substituição, não erros de inserção ou exclusão.
* Evite exemplos que incluam erros de transcrição, mas inclua uma diversidade de qualidade de áudio.
* Evite frases que não estejam relacionadas ao seu domínio problemático. Frases não relacionadas podem danificar seu modelo.
* Quando a qualidade das transcrições varia, você pode duplicar frases válidas com exceção (como as excelentes transcrições que incluem frases-chave) para aumentar seu peso.
* O serviço de fala usará automaticamente as transcrições para melhorar o reconhecimento de palavras e frases específicas de domínio, como se elas fossem adicionadas como texto relacionado.
* O treinamento com áudio levará mais benefícios se o áudio também for difícil de entender para os seres humanos. Na maioria dos casos, você deve iniciar o treinamento usando apenas o texto relacionado.
* Pode levar vários dias para que uma operação de treinamento seja concluída. Para melhorar a velocidade de treinamento, certifique-se de criar sua assinatura do serviço de fala em uma [região com o hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para treinamento.

> [!NOTE]
> Nem todos os modelos de base dão suporte ao treinamento com áudio. Se um modelo base não oferecer suporte a ele, o serviço de fala usará apenas o texto das transcrições e ignorará o áudio. Consulte [suporte a idiomas](language-support.md#speech-to-text) para obter uma lista de modelos de base que dão suporte ao treinamento com dados de áudio.

> [!NOTE]
> Em casos em que você altera o modelo de base usado para treinamento e tem áudio no conjunto de dados de treinamento, *sempre* Verifique se o novo modelo de base selecionado [dá suporte ao treinamento com o áudio](language-support.md#speech-to-text). Se o modelo base usado anteriormente não tivesse suporte para treinamento com dados de áudio, e o DataSet de treinamento contiver áudio, o tempo de treinamento com o novo modelo base aumentará **drasticamente** e poderá facilmente passar de várias horas para vários dias e muito mais. Isso será especialmente verdadeiro se sua assinatura de serviço de fala **não** estiver em uma [região com o hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para treinamento.
>
> Se você enfrentar o problema descrito no parágrafo acima, poderá diminuir rapidamente o tempo de treinamento reduzindo a quantidade de áudio no conjunto de espaço ou removendo-o completamente e deixando apenas o texto. A última opção é altamente recomendável se sua assinatura de serviço de fala **não** estiver em uma [região com o hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para treinamento.

### <a name="add-new-words-with-pronunciation"></a>Adicionar novas palavras com pronúncia

As palavras que são feitas ou altamente especializadas podem ter pronúncias exclusivas. Essas palavras podem ser reconhecidas se a palavra puder ser dividida em palavras menores para pronuncia-la. Por exemplo, para reconhecer o **Xbox**, pronuncia-se como **X Box**. Essa abordagem não aumentará a precisão geral, mas pode aumentar o reconhecimento dessas palavras-chave.

> [!NOTE]
> Essa técnica só está disponível para alguns idiomas no momento. Consulte personalização de pronúncia na [tabela de conversão de fala em texto](language-support.md) para obter detalhes.

## <a name="sources-by-scenario"></a>Fontes por cenário

A tabela a seguir mostra cenários de reconhecimento de voz e lista os materiais de origem a serem considerados nas três categorias de conteúdo de treinamento listadas acima.

| Cenário | Frases de texto relacionadas | Áudio + transcrições com rótulo humano | Novas palavras com pronúncia |
|----------|------------------------|------------------------------|------------------------------|
| Call center             | documentos de marketing, site, revisões de produtos relacionados à atividade do Call Center | chamadas de Call Center transcritas por seres humanos | termos que têm pronúncias ambíguas (consulte o Xbox acima) |
| Assistente de voz         | listar frases usando todas as combinações de comandos e entidades | gravar os comandos de fala de vozes no dispositivo e transcrever em texto | nomes (filmes, músicas, produtos) que têm pronúncias exclusivas |
| Ditado               | entrada escrita, como mensagens instantâneas ou emails | semelhante ao anterior | semelhante ao anterior |
| Legenda codificada em vídeo | TV mostrar scripts, filmes, conteúdo de marketing, resumos de vídeo | transcrições exatas de vídeos | semelhante ao anterior |

## <a name="next-steps"></a>Próximas etapas

* [Treinar e implantar um modelo](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Preparar e testar seus dados](./how-to-custom-speech-test-and-train.md)
* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)