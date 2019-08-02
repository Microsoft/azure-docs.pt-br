---
title: Definir configurações-personalizador
titleSuffix: Azure Cognitive Services
description: A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço faz explorações, com que frequência o modelo é treinado novamente e quantos dados são armazenados.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: f0ccf0e480fa57e0ffdfc94ca35cfaceded37a0b
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663890"
---
# <a name="personalizer-settings"></a>Configurações do Personalizador

A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço faz explorações, com que frequência o modelo é treinado novamente e quantos dados são armazenados.

## <a name="create-personalizer-resource"></a>Crie um recurso do Personalizador

Crie um recurso do Personalizador para cada loop de comentários. 

1. Entre no [Portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). O link anterior leva você à página **Criar** do serviço do Personalizador. 
1. Insira o nome do seu serviço, selecione uma assinatura, um local, um tipo de preço e um grupo de recursos.
1. Selecione a confirmação e, em seguida, **Criar**.

## <a name="configure-service-settings-in-the-azure-portal"></a>Definir as configurações do serviço no portal do Azure

1. Entre no [Portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Encontre o recurso do Personalizador. 
1. Na seção **Gerenciamento de recursos**, selecione **Configurações**.

    Antes de sair do portal do Azure, copie uma de suas chaves de recurso da página de **Chaves**. Você precisará disso para usar o [SDK do Personalizador](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer).

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>Defina as configurações de recompensa para o loop de comentários com base no caso de uso

Defina as configurações do serviço para uso do loop de comentários de recompensas. As alterações nas seguintes configurações redefinirão o modelo atual do Personalizador e o treinarão novamente com os dois últimos dias de dados:

![Definir as configurações de recompensa para o loop de comentários](media/settings/configure-model-reward-settings.png)

|Configuração|Finalidade|
|--|--|
|Tempo de espera de recompensa|Define o período de tempo durante o qual Personalizador coletará valores de recompensa para uma chamada de Classificação, a partir do momento em que ocorre a chamada de Classificação. Esse valor é definido pelas seguintes perguntas: "Por quanto tempo o Personalizador deve aguardar as chamadas de recompensas?" Qualquer recompensa recebida após essa janela será registrada em log, mas não será usada para o aprendizado.|
|Recompensa padrão|Se nenhuma chamada de recompensa for recebida pelo Personalizador durante a janela de Tempo de Espera de Recompensa associada a uma Chamada de classificação, o Personalizador atribuirá a Recompensa Padrão. Por padrão e na maioria dos cenários, a Recompensa Padrão é zero.|
|Agregação de recompensas|Se várias recompensas forem recebidas para a mesma chamada à API de Classificação, este método de agregação será usado: **soma** ou **mais anterior**. A opção "mais anterior" escolhe a pontuação mais anterior recebida e descarta o restante. Isso é útil se você deseja obter uma recompensa exclusiva entre chamadas possivelmente duplicadas. |

Depois de alterar essas configurações, selecione **Salvar**.

### <a name="exploration-setting"></a>Configuração de exploração 

A personalização pode descobrir novos padrões e se adaptar às alterações no comportamento do usuário ao longo do tempo explorando alternativas. A configuração **Exploração** determina qual percentual de chamadas de Classificação são respondidas com a exploração. 

As alterações nessa configuração redefinirão o modelo atual do Personalizador e o treinarão novamente com os dois últimos dias de dados.

![A configuração de exploração determina qual percentual de chamadas de Classificação são respondidas com a exploração](media/settings/configure-exploration-setting.png)

Depois de alterar essa configuração, selecione **Salvar**.

### <a name="model-update-frequency"></a>Frequência de atualização do modelo

O modelo mais recente, treinado com base nas chamadas à API de Recomenda de cada evento ativo, não é usado automaticamente pela chamada de Classificação do Personalizador. A **Frequência de atualização do modelo** define a frequência com que o modelo usado pela Chamada de classificação é atualizado. 

As altas frequências de atualização do modelo são úteis para situações em que você deseja acompanhar de perto as alterações em comportamentos do usuário. Os exemplos incluem sites executados em notícias em tempo real, conteúdo viral ou licitação de produtos ao vivo. Você pode usar uma frequência de 15 minutos nesses cenários. Na maioria dos casos de uso, uma frequência de atualização mais baixa é eficaz. Frequências de atualização de um minuto são úteis ao depurar o código de um aplicativo usando o Personalizador, executar demonstrações ou testar interativamente aspectos de aprendizado de máquina.

![A frequência de atualização do modelo define a frequência com que um novo modelo do Personalizador é treinado novamente.](media/settings/configure-model-update-frequency-settings.png)

Depois de alterar essa configuração, selecione **Salvar**.

### <a name="data-retention"></a>Retenção de dados

O **Período de retenção de dados** define por quantos dias o Personalizador mantém os logs de dados. Os logs de dados passados são necessários para realizar [avaliações offline](concepts-offline-evaluation.md), que são usados para medir a eficácia do Personalizador e otimizar a Política de Aprendizado.

Depois de alterar essa configuração, selecione **Salvar**.

## <a name="export-the-personalizer-model"></a>Exportar o modelo do Personalizador

Na seção do Gerenciamento de recursos de **Modelo e Política**, examine a criação do modelo e a data da última atualização e exporte o modelo atual. É possível usar o portal do Azure ou as APIs do Personalizador para exportar um arquivo de modelo para fins de arquivamento. 

![Exportar o modelo atual do Personalizador](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importar e exportar a política de aprendizado

Na seção do Gerenciamento de recursos de **Modelo e Política**, importe uma nova política de aprendizado ou exporte a política de aprendizado atual.

## <a name="next-steps"></a>Próximas etapas

<!--
[How to use the Personalizer container](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->
[Saiba mais sobre a disponibilidade de região](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
