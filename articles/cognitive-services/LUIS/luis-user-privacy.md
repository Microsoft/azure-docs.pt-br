---
title: Exportar & excluir dados-LUIS
titleSuffix: Azure Cognitive Services
description: Exclua os dados do cliente para garantir a privacidade e a conformidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: e234f88d6e735f33be253cacb373baef63c605c0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559992"
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

| | **Conta do Usuário** | **Aplicativo** | **Expressão (s) de exemplo** | **Consultas do usuário final** |
| --- | --- | --- | --- | --- |
| **Portal** | [Link](luis-concept-data-storage.md#delete-an-account) | [Link](luis-how-to-start-new-app.md#delete-app) | [Link](luis-concept-data-storage.md#utterances-in-an-intent) | [Declarações de aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Declarações registrados](luis-concept-data-storage.md#disable-logging-utterances) |
| **APIs** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportando os dados do cliente
Os usuários do LUIS têm controle total para exibir os dados no portal, no entanto, eles devem ser exportados por meio das APIs de criação de LUIS (também conhecidas como programáticas). A tabela a seguir exibe links que auxiliam com exportações de dados por meio das APIs de criação LUIS (também conhecidas como programáticas):

| | **Conta do Usuário** | **Aplicativo** | **Enunciado(s)** | **Consultas do usuário final** |
| --- | --- | --- | --- | --- |
| **APIs** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Local do aprendizado ativo

Para habilitar o [aprendizado ativo](luis-how-to-review-endpoint-utterances.md#enable-active-learning), os declarações registrados em log dos usuários, recebidos nos pontos de extremidade Luis publicados, são armazenados nas seguintes regiões geográficas do Azure:

* [Europa](#europe)
* [Austrália](#australia)
* [Estados Unidos](#united-states)

Com exceção dos dados de aprendizado ativos (detalhados abaixo), o LUIS segue as [práticas de armazenamento de dados para serviços regionais](https://azuredatacentermap.azurewebsites.net/). 

### <a name="europe"></a>Europa

O portal [eu.Luis.ai](https://eu.luis.ai) e a criação da Europa (também conhecidas como APIs programáticas) são hospedados na geografia da Europa do Azure. O portal do eu.luis.ai e a criação da Europa (também conhecida como APIs programáticas) dão suporte à implantação de pontos de extremidade para as seguintes regiões geográficas do Azure:

* Europa
* França
* Reino Unido

Ao implantar nessas regiões do Azure, o declarações recebido pelo ponto de extremidade dos usuários finais do seu aplicativo será armazenado na geografia da Europa do Azure para o aprendizado ativo. Você pode desabilitar o aprendizado ativo, consulte [desabilitar o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerenciar o declarações armazenado, consulte [excluir expressão](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Austrália

O portal [au.Luis.ai](https://au.luis.ai) e a criação da Austrália (também conhecidas como APIs programáticas) são hospedados na geografia da Austrália do Azure. O portal do au.luis.ai e a criação da Austrália (também conhecida como APIs programáticas) dão suporte à implantação de pontos de extremidade para as seguintes regiões geográficas do Azure:

* Austrália

Ao implantar nessas regiões do Azure, o declarações recebido pelo ponto de extremidade dos usuários finais do seu aplicativo será armazenado na geografia da Austrália do Azure para o aprendizado ativo. Você pode desabilitar o aprendizado ativo, consulte [desabilitar o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerenciar o declarações armazenado, consulte [excluir expressão](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>Estados Unidos

O portal do [Luis.ai](https://www.luis.ai) e a criação de Estados Unidos (também conhecida como APIs programáticas) são hospedados na geografia de Estados Unidos do Azure. O portal do luis.ai e a criação de Estados Unidos (também conhecida como APIs programáticas) dão suporte à implantação de pontos de extremidade para as seguintes regiões geográficas do Azure:

* Geografias do Azure sem suporte nas regiões de criação da Europa ou da Austrália

Ao implantar nessas regiões do Azure, o declarações recebido pelo ponto de extremidade dos usuários finais do seu aplicativo será armazenado na geografia Estados Unidos do Azure para o aprendizado ativo. Você pode desabilitar o aprendizado ativo, consulte [desabilitar o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerenciar o declarações armazenado, consulte [excluir expressão](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência de regiões do LUIS](./luis-reference-regions.md)
