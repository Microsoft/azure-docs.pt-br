---
title: Treinar e implantar um modelo de Fala Personalizada – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Saiba como treinar e implantar modelos de Fala Personalizada. Treinar um modelo de reconhecimento de fala pode aprimorar a precisão do reconhecimento para o modelo de linha de base da Microsoft ou para um modelo personalizado.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: 4ba046b5461c3b734d54ad3694f0ceea2e5127c6
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387036"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Treinar e implantar um modelo de Fala Personalizada

Neste artigo, você aprenderá a treinar e implantar modelos de Fala Personalizada. Treinar um modelo de reconhecimento de fala pode aprimorar a precisão do reconhecimento para o modelo de linha de base da Microsoft. Você usa transcrições literais e texto relacionado para treinar um modelo. Esses conjuntos de dados, juntamente com a data de áudio carregada anteriormente, são usados para refinar e treinar o modelo de reconhecimento de fala.

## <a name="use-training-to-resolve-accuracy-problems"></a>Usar o treinamento para resolver problemas de precisão

Se você estiver encontrando problemas de reconhecimento com um modelo de base, poderá usar transcrições literais e dados relacionados para treinar um modelo personalizado e ajudar a aprimorar a precisão. Use esta tabela para determinar qual conjunto de dados será usado para resolver seus problemas:

| Caso de uso | Tipo de dados |
| -------- | --------- |
| Aprimorar a precisão do reconhecimento no vocabulário e na gramática específicos do setor, como terminologia médica ou jargão de TI | Texto relacionado (frases/enunciados) |
| Definir a forma fonética e exibida de uma palavra ou um termo que tenha pronúncia não padrão, como nomes de produtos ou acrônimos | Texto relacionado (pronúncia) |
| Aprimorar a precisão do reconhecimento em estilos de fala, sotaques ou ruídos de fundo específicos | Áudio + transcrições literais |

## <a name="train-and-evaluate-a-model"></a>Treinar e avaliar um modelo

A primeira etapa para treinar um modelo é carregar dados de treinamento. Confira [Preparar e testar seus dados](./how-to-custom-speech-test-and-train.md) para obter instruções passo a passo para preparar transcrições literais e texto relacionado (enunciados e pronúncias). Depois de carregar os dados de treinamento, siga estas instruções para começar a treinar seu modelo:

1. entre no [Portal de Fala Personalizada](https://speech.microsoft.com/customspeech). Se você planeja treinar um modelo com áudio + conjuntos de dados de transcrição literal, escolha uma assinatura da Fala em uma [região com hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para treinamento.
2. Acesse a **Reconhecimento de fala** > **Fala Personalizada** >  **[nome do projeto]**  > **Treinamento**.
3. Selecione **Treinar modelo**.
4. Dê um **nome** e uma **descrição** ao seu treinamento.
5. Na lista **Modelo de cenário e de linha de base**, selecione o cenário que melhor se adapta ao seu domínio. Se você não tiver certeza de qual cenário escolher, selecione **Geral**. O modelo de linha de base é o ponto de partida para sua personalização. O modelo mais recente geralmente é a melhor opção.
6. Na página **Selecionar dados de treinamento**, escolha um ou mais conjuntos de dados de texto relacionado ou conjuntos de dados de áudio + transcrição literal que você deseja usar para treinamento.

> [!NOTE]
> Quando você treinar um novo modelo, comece com o texto relacionado; o treinamento com áudio + transcrição literal pode levar muito mais tempo **(até [vários dias](how-to-custom-speech-evaluate-data.md#add-audio-with-human-labeled-transcripts)** ).

> [!NOTE]
> Nem todos os modelos de base dão suporte ao treinamento com áudio. Se um modelo de base não der suporte a ele, o serviço de Fala usará apenas o texto das transcrições e ignorará o áudio. Confira [Suporte a idiomas](language-support.md#speech-to-text) para obter uma lista de modelos de base que dão suporte ao treinamento com os dados de áudio.

> [!NOTE]
> Em casos em que você altera o modelo de base usado para treinamento e tem áudio no conjunto de dados de treinamento, *sempre* verifique se o novo modelo de base selecionado [dá suporte ao treinamento com os dados de áudio](language-support.md#speech-to-text). Se o modelo de base usado anteriormente não der suporte ao treinamento com os dados de áudio e o conjunto de dados de treinamento contiver áudio, o tempo de treinamento com o novo modelo base aumentará **drasticamente** e poderá facilmente passar de várias horas para vários dias e muito mais. Isso será especialmente verdadeiro se a sua assinatura de serviço de Fala **não** estiver em uma [região com o hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para treinamento.
>
> Se você enfrentar o problema descrito no parágrafo acima, poderá diminuir rapidamente o tempo de treinamento reduzindo a quantidade de áudio no conjunto de dados ou removendo-o completamente e deixando apenas o texto. A última opção será altamente recomendável se a sua assinatura de serviço de Fala **não** estiver em uma [região com o hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para treinamento.

7. Após a conclusão do treinamento, você poderá fazer testes de precisão no modelo recém-treinado. Esta etapa é opcional.
8. Selecione **Criar** para criar seu modelo personalizado.

A tabela **Treinamento** exibe uma nova entrada que corresponde ao novo modelo. A tabela também exibe o status: **Processamento**, **Com êxito** ou **Com falha**.

Veja o [guia](how-to-custom-speech-evaluate-data.md) sobre como avaliar e aprimorar a precisão do modelo de Fala Personalizada. Se optar por testar a precisão, será importante selecionar um conjunto de dados acústico que seja diferente do que você usou com seu modelo para ter uma noção realista do desempenho do modelo.

> [!NOTE]
> Os modelos de base e os modelos personalizados podem ser usados apenas até uma determinada data (confira [Ciclo de vida do modelo e do ponto de extremidade](./how-to-custom-speech-model-and-endpoint-lifecycle.md)). O Speech Studio mostra essa data na coluna **Expiração** para cada modelo e ponto de extremidade. Após essa solicitação de data para um ponto de extremidade ou lote, a transcrição poderá falhar ou retornar ao modelo de base.
>
> Treine novamente seu modelo usando o modelo de base mais recente para se beneficiar dos aprimoramentos de precisão e para evitar que seu modelo expire.

## <a name="deploy-a-custom-model"></a>Implantar um modelo personalizado

Depois de carregar e inspecionar dados, avaliar a precisão e treinar um modelo personalizado, você poderá implantar um ponto de extremidade personalizado para usar com seus aplicativos, ferramentas e produtos. 

Para criar um ponto de extremidade personalizado, entre no [portal de Fala Personalizada](https://speech.microsoft.com/customspeech). Selecione **Implantação** no menu **Fala Personalizada** na parte superior da página. Se esta for sua primeira execução, você observará que não há nenhum ponto de extremidade listado na tabela. Depois de criar um ponto de extremidade, use essa página para acompanhar cada ponto de extremidade implantado.

Em seguida, selecione **Adicionar ponto de extremidade** e insira um **Nome** e uma **Descrição** para o ponto de extremidade personalizado. Em seguida, selecione o modelo personalizado que você deseja associar ao ponto de extremidade.  Você também pode habilitar o registro em log desta página. O registro em log permite monitorar o tráfego do ponto de extremidade. Se o registro em log estiver desabilitado, o tráfego não será armazenado.

![Captura de tela que mostra a página Novo ponto de extremidade.](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Não se esqueça de aceitar os termos de uso e os detalhes de preços.

Em seguida, selecione **Criar**. Essa ação retorna você para a página **Implantação**. A tabela agora inclui uma entrada que corresponde ao seu ponto de extremidade personalizado. O status do ponto de extremidade mostra o estado atual dele. Pode levar até 30 minutos para criar uma instância de um novo ponto de extremidade usando seus modelos personalizados. Quando o status da implantação for alterado para **Concluído**, o ponto de extremidade estará pronto para uso.

Depois que o ponto de extremidade for implantado, o nome do ponto de extremidade aparecerá como um link. Selecione o link para ver informações específicas para seu ponto de extremidade, como a chave do ponto de extremidade, a URL do ponto de extremidade e o código de exemplo. Anote a data de validade e atualize o modelo do ponto de extremidade antes dessa data para garantir o serviço ininterrupto.

## <a name="view-logging-data"></a>Exibir dados de registro em log

Os dados de registro em log estarão disponíveis para exportação se você for para a página do ponto de extremidade em **Implantações**.
> [!NOTE]
>Os dados de registro em log estão disponíveis por 30 dias no armazenamento de propriedade da Microsoft. Eles serão removidos depois. Se uma conta de armazenamento de propriedade do cliente estiver vinculada à assinatura dos Serviços Cognitivos, os dados de registro em log não serão excluídos automaticamente.

## <a name="next-steps"></a>Próximas etapas

* [Saiba como usar seu modelo personalizado](how-to-specify-source-language.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Preparar e testar seus dados](./how-to-custom-speech-test-and-train.md)
- [Inspecionar seus dados](how-to-custom-speech-inspect-data.md)
- [Avaliar seus dados](how-to-custom-speech-evaluate-data.md)
