---
title: Exportar & excluir dados-LUIS
titleSuffix: Azure Cognitive Services
description: Você tem controle total sobre a exibição, a exportação e a exclusão de seus dados. Exclua os dados do cliente para garantir a privacidade e a conformidade.
services: cognitive-services
manager: nitinme
ms.custom: seodec18, references_regions
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/10/2020
ms.openlocfilehash: 0a2d0ce683261ca3460c7aeaa0d7a42152b81a1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98680154"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportar e excluir dados do cliente no LUIS (Serviço Inteligente de Reconhecimento Vocal) nos Serviços Cognitivos

Exclua os dados do cliente para garantir a privacidade e a conformidade.

## <a name="summary-of-customer-data-request-features"></a>Resumo dos recursos de solicitação de dados do cliente
O LUIS (Serviço Inteligente de Reconhecimento Vocal) preserva o conteúdo do cliente para operar o serviço, mas o usuário do LUIS tem controle total sobre a exibição, a exportação e a exclusão dos próprios dados. Isso pode ser feito por meio do [portal](luis-reference-regions.md) da Web do Luis ou das [APIs de criação do Luis (também conhecidas como programáticas)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

O conteúdo do cliente foi armazenado criptografado no armazenamento do Azure regional da Microsoft e inclui:

- Conteúdo da conta de usuário coletado no momento do registro
- Dados de treinamento necessários para criar os modelos
- Consultas de usuário registradas usadas pelo [aprendizado ativo](luis-concept-review-endpoint-utterances.md) para ajudar a melhorar o modelo
  - Os usuários podem desligar o registro em log de consulta anexando `&log=false` à solicitação; detalhes [aqui](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Excluindo os dados do cliente
LUIS os usuários têm controle total para excluir qualquer conteúdo de usuário, seja por meio do portal da Web do LUIS ou das APIs de criação de LUIS (também conhecidas como programáticas). A tabela a seguir exibe links para ajudar a ajudar com ambos:

| | **Conta de Usuário** | **Aplicativo** | **Expressão (s) de exemplo** | **Consultas do usuário final** |
| --- | --- | --- | --- | --- |
| **Portal** | [Link](luis-concept-data-storage.md#delete-an-account) | [Link](luis-how-to-start-new-app.md#delete-app) | [Link](luis-concept-data-storage.md#utterances-in-an-intent) | [Declarações de aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Declarações registrados](luis-concept-data-storage.md#disable-logging-utterances) |
| **APIs** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportando os dados do cliente
Os usuários do LUIS têm controle total para exibir os dados no portal, no entanto, eles devem ser exportados por meio das APIs de criação de LUIS (também conhecidas como programáticas). A tabela a seguir exibe links que auxiliam com exportações de dados por meio das APIs de criação LUIS (também conhecidas como programáticas):

| | **Conta de Usuário** | **Aplicativo** | **Expressão (s)** | **Consultas do usuário final** |
| --- | --- | --- | --- | --- |
| **APIs** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Local do aprendizado ativo

Para habilitar o [aprendizado ativo](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning), os declarações registrados em log dos usuários, recebidos nos pontos de extremidade Luis publicados, são armazenados nas seguintes regiões geográficas do Azure:

* [Europa](#europe)
* [Austrália](#australia)
* [Estados Unidos](#united-states)

Com exceção dos dados de aprendizado ativos (detalhados abaixo), o LUIS segue as [práticas de armazenamento de dados para serviços regionais](https://azuredatacentermap.azurewebsites.net/).

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]


### <a name="europe"></a>Europa

Os recursos de criação da Europa (também conhecidos como APIs programáticas) são hospedados na geografia da Europa do Azure e dão suporte à implantação de pontos de extremidade para as seguintes regiões geográficas do Azure:

* Europa
* França
* Reino Unido

Ao implantar nessas regiões do Azure, o declarações recebido pelo ponto de extremidade dos usuários finais do seu aplicativo será armazenado na geografia da Europa do Azure para o aprendizado ativo.

### <a name="australia"></a>Austrália

Os recursos de criação da Austrália (também conhecidos como APIs programáticas) são hospedados na geografia da Austrália do Azure e dão suporte à implantação de pontos de extremidade para as seguintes regiões geográficas do Azure:

* Austrália

Ao implantar nessas regiões do Azure, o declarações recebido pelo ponto de extremidade dos usuários finais do seu aplicativo será armazenado na geografia da Austrália do Azure para o aprendizado ativo.

### <a name="united-states"></a>Estados Unidos

Os recursos de criação de Estados Unidos (também conhecidos como APIs programáticas) são hospedados na geografia de Estados Unidos do Azure e dão suporte à implantação de pontos de extremidade para as seguintes regiões geográficas do Azure:

* Geografias do Azure sem suporte nas regiões de criação da Europa ou da Austrália

Ao implantar nessas regiões do Azure, o declarações recebido pelo ponto de extremidade dos usuários finais do seu aplicativo será armazenado na geografia Estados Unidos do Azure para o aprendizado ativo. 

## <a name="disable-active-learning"></a>Desabilitar o aprendizado ativo

Para desabilitar o aprendizado ativo, consulte [desabilitar o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerenciar o declarações armazenado, consulte [excluir expressão](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência de regiões do LUIS](./luis-reference-regions.md)
