---
title: Treinar e implantar um Fala Personalizada modelo-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como treinar e implantar modelos de Fala Personalizada. Treinar um modelo de fala a texto pode melhorar a precisão do reconhecimento para o modelo de linha de base da Microsoft ou para um modelo personalizado.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 41fdb3d2e69ae39dbe80f21a953fd9fdaa6d1127
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968459"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Treinar e implantar um modelo de Fala Personalizada

Neste artigo, você aprenderá a treinar e implantar modelos de Fala Personalizada. Treinar um modelo de fala a texto pode melhorar a precisão do reconhecimento para o modelo de linha de base da Microsoft. Você usa transcrições com rótulo humano e texto relacionado para treinar um modelo. Esses conjuntos de dados, juntamente com a data de áudio carregada anteriormente, são usados para refinar e treinar o modelo de conversão de fala em texto.

## <a name="use-training-to-resolve-accuracy-problems"></a>Use o treinamento para resolver problemas de precisão

Se você estiver encontrando problemas de reconhecimento com um modelo base, poderá usar transcrições com rótulo humano e dados relacionados para treinar um modelo personalizado e ajudar a melhorar a precisão. Use esta tabela para determinar qual conjunto de de que será usado para resolver seus problemas:

| Caso de uso | Tipo de dados |
| -------- | --------- |
| Melhore a precisão do reconhecimento no vocabulário e na gramática específicos do setor, como terminologia médica ou jargão de ti | Texto relacionado (sentenças/declarações) |
| Definir a forma fonética e exibida de uma palavra ou termo que tenha pronúncia não padrão, como nomes de produtos ou acrônimos | Texto relacionado (pronúncia) |
| Melhorar a precisão do reconhecimento em estilos de fala, ênfases ou ruídos de fundo específicos | Áudio + transcrições com rótulo humano |

## <a name="train-and-evaluate-a-model"></a>Treinar e avaliar um modelo

A primeira etapa para treinar um modelo é carregar dados de treinamento. Consulte [preparar e testar seus dados](./how-to-custom-speech-test-and-train.md) para obter instruções passo a passo para preparar transcrições com rótulo humano e texto relacionado (declarações e pronúncias). Depois de carregar os dados de treinamento, siga estas instruções para começar a treinar seu modelo:

1. Entre no portal de [fala personalizada](https://speech.microsoft.com/customspeech). Se você planeja treinar um modelo com áudio + conjuntos de rótulos de itens de transcrição com rótulo humano, escolha uma assinatura de fala em uma [região com hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para treinamento.
2. Acesse a **conversão de fala em texto**  >  **fala personalizada**  >  **[nome do projeto]**  >  **treinamento**.
3. Selecione **treinar modelo**.
4. Dê um **nome** e uma **Descrição** ao seu treinamento.
5. Na lista **modelo de cenário e linha de base** , selecione o cenário que melhor se adapta ao seu domínio. Se você não tiver certeza de qual cenário escolher, selecione **geral**. O modelo de linha de base é o ponto de partida para treinamento. O modelo mais recente geralmente é a melhor opção.
6. Na página **selecionar dados de treinamento** , escolha um ou mais conjuntos de valores de texto relacionados ou áudio + conjuntos de dados de transcrição rotulados por pessoas que você deseja usar para treinamento. Ao treinar um novo modelo, comece com o texto relacionado; o treinamento com áudio + transcrição com rótulo humano pode levar muito mais tempo (até [vários dias](how-to-custom-speech-evaluate-data.md#improve-model-recognition)).
7. Após a conclusão do treinamento, você pode fazer testes de precisão no modelo treinado recentemente. Esta etapa é opcional.
8. Selecione **criar** para criar seu modelo personalizado.

A tabela de **treinamento** exibe uma nova entrada que corresponde ao novo modelo. A tabela também exibe o status: **processamento**, com **êxito** ou **falha**.

Veja [como](how-to-custom-speech-evaluate-data.md) avaliar e melhorar a precisão do modelo de fala personalizada. Se você optar por testar a precisão, é importante selecionar um conjunto de um banco de uma diferente do que você usou com seu modelo para obter uma noção realista do desempenho do modelo.

> [!NOTE]
> Os modelos de base e os modelos personalizados podem ser usados apenas até uma determinada data (consulte [ciclo de vida do modelo](custom-speech-overview.md#model-lifecycle)). O Speech Studio mostra essa data na coluna de **expiração** para cada modelo e ponto de extremidade. Após essa solicitação de data a um ponto de extremidade ou à transcrição do lote pode falhar ou retornar ao modelo base.
>
> Retreine seu modelo usando o modelo base mais recente para se beneficiar de melhorias de precisão e para evitar que seu modelo expire.

## <a name="deploy-a-custom-model"></a>Implantar um modelo personalizado

Depois de carregar e inspecionar dados, avaliar a precisão e treinar um modelo personalizado, você pode implantar um ponto de extremidade personalizado para usar com seus aplicativos, ferramentas e produtos. 

Para criar um ponto de extremidade personalizado, entre no [portal do fala personalizada](https://speech.microsoft.com/customspeech). Selecione **implantação** no menu **fala personalizada** na parte superior da página. Se esta for sua primeira execução, você observará que não há nenhum ponto de extremidade listado na tabela. Depois de criar um ponto de extremidade, use essa página para acompanhar cada ponto de extremidade implantado.

Em seguida, selecione **Adicionar ponto de extremidade** e insira um **nome** e uma **Descrição** para o ponto de extremidade personalizado. Em seguida, selecione o modelo personalizado que você deseja associar ao ponto de extremidade.  Você também pode habilitar o registro em log desta página. O registro em log permite monitorar o tráfego do ponto de extremidade. Se o registro em log estiver desabilitado, o tráfego não será armazenado.

![Captura de tela que mostra a nova página de ponto de extremidade.](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Não se esqueça de aceitar os termos de uso e os detalhes de preços.

Em seguida, selecione **Criar**. Essa ação retorna para a página de **implantação** . A tabela agora inclui uma entrada que corresponde ao seu ponto de extremidade personalizado. O status do ponto de extremidade mostra seu estado atual. Pode levar até 30 minutos para criar uma instância de um novo ponto de extremidade usando seus modelos personalizados. Quando o status da implantação for alterado para **concluído**, o ponto de extremidade estará pronto para uso.

Depois que o ponto de extremidade for implantado, o nome do ponto de extremidade aparecerá como um link. Selecione o link para ver informações específicas para seu ponto de extremidade, como a chave do ponto de extremidade, a URL do ponto de extremidade e o código de exemplo. Anote a data de validade e atualize o modelo do ponto de extremidade antes dessa data para garantir o serviço ininterrupto.

## <a name="view-logging-data"></a>Exibir dados de log

Os dados de log estarão disponíveis para exportação se você for para a página do ponto de extremidade em **implantações**.
> [!NOTE]
>Os dados de registro em log estão disponíveis por 30 dias no armazenamento de propriedade da Microsoft. Ela será removida posteriormente. Se uma conta de armazenamento de Propriedade do cliente estiver vinculada à assinatura de serviços cognitivas, os dados de log não serão excluídos automaticamente.

## <a name="next-steps"></a>Próximas etapas

* [Saiba como usar seu modelo personalizado](how-to-specify-source-language.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Preparar e testar seus dados](./how-to-custom-speech-test-and-train.md)
- [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
- [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
