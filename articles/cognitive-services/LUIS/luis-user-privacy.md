---
title: Exportar e excluir dados
titleSuffix: Azure Cognitive Services
description: Exclua dados do cliente para garantir a privacidade e conformidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: eb125133138c6de173fdeb90024a9e5d961a929d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597092"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportar e excluir dados do cliente no LUIS (Serviço Inteligente de Reconhecimento Vocal) nos Serviços Cognitivos

Exclua dados do cliente para garantir a privacidade e conformidade. 

## <a name="summary-of-customer-data-request-features"></a>Resumo dos recursos de solicitação de dados do cliente
O LUIS (Serviço Inteligente de Reconhecimento Vocal) preserva o conteúdo do cliente para operar o serviço, mas o usuário do LUIS tem controle total sobre a exibição, a exportação e a exclusão dos próprios dados. Isso pode ser feito por meio da web LUIS [portal](luis-reference-regions.md) ou o [criação LUIS (também conhecido como programáticas) APIs](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

O conteúdo do cliente foi armazenado criptografado no armazenamento do Azure regional da Microsoft e inclui:

- Conteúdo da conta de usuário coletado no momento do registro
- Dados de treinamento necessários para compilar os modelos
- Conectado a consultas de usuário usadas pelo [aprendizado ativo](luis-concept-review-endpoint-utterances.md) para ajudar a melhorar o modelo
  - Os usuários podem desligar o registro em log de consulta anexando `&log=false` à solicitação; detalhes [aqui](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Excluindo os dados do cliente
Os usuários do LUIS tem controle total para excluir qualquer conteúdo, por meio do portal da web LUIS ou as APIs do LUIS (também conhecido como programáticas) de criação de usuário. A tabela a seguir exibe links para ajudar a ajudar com ambos:

| | **Conta do Usuário** | **Aplicativo** | **Exemplo Utterance(s)** | **Consultas do usuário final** |
| --- | --- | --- | --- | --- |
| **Portal** | [Link](luis-concept-data-storage.md#delete-an-account) | [Link](luis-how-to-start-new-app.md#delete-app) | [Link](luis-concept-data-storage.md#utterances-in-an-intent) | [Declarações de aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Declarações conectadas](luis-concept-data-storage.md#disable-logging-utterances) |
| **APIs** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportando os dados do cliente
Os usuários do LUIS tem controle total para exibir os dados no portal, no entanto, ele deve ser exportado por meio de APIs (também conhecido como programático) a criação de LUIS. A tabela a seguir exibe links para ajudar com as exportações de dados por meio das APIs (também conhecido como programática) LUIS de criação:

| | **Conta do Usuário** | **Aplicativo** | **Enunciado(s)** | **Consultas do usuário final** |
| --- | --- | --- | --- | --- |
| **APIs** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Local do aprendizado ativo

Para habilitar [aprendizado ativo](luis-how-to-review-endpoint-utterances.md#enable-active-learning), declarações de usuários registrados, recebidas com os pontos de extremidade publicados do LUIS, são armazenadas nas seguintes regiões geográficas do Azure:

* [Europa](#europe)
* [Austrália](#australia)
* [Estados Unidos](#united-states)

Com exceção dos dados de aprendizado ativo (detalhados embaixo), o LUIS segue o [práticas recomendadas de armazenamento de dados para serviços regionais](http://azuredatacentermap.azurewebsites.net/). 

### <a name="europe"></a>Europa

O [eu.luis.ai](https://eu.luis.ai) portal e na Europa (também conhecido como APIs programáticas) de criação são hospedados na geografia de Europa do Azure. O portal de eu.luis.ai e Europa (também conhecido como APIs programáticas) de criação dar suporte à implantação de pontos de extremidade a seguir regiões geográficas do Azure:

* Europa
* França
* Reino Unido

Ao implantar essas regiões geográficas do Azure, as declarações recebidas pelo ponto de extremidade dos usuários finais do seu aplicativo serão armazenadas na geografia de Europa do Azure para o aprendizado ativo. Você pode desabilitar o aprendizado ativo, consulte [desabilitar aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerenciar armazenadas declarações, consulte [excluir expressão](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Austrália

O [au.luis.ai](https://au.luis.ai) portal e Austrália criação (também conhecido como APIs programáticas) são hospedados na geografia de Austrália do Azure. O portal de au.luis.ai e a Austrália criação (também conhecido como APIs programáticas) dar suporte à implantação de pontos de extremidade a seguir regiões geográficas do Azure:

* Austrália

Ao implantar essas regiões geográficas do Azure, as declarações recebidas pelo ponto de extremidade dos usuários finais do seu aplicativo serão armazenadas na geografia de Austrália do Azure para o aprendizado ativo. Você pode desabilitar o aprendizado ativo, consulte [desabilitar aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerenciar armazenadas declarações, consulte [excluir expressão](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>Estados Unidos

O [luis.ai](https://www.luis.ai) portal e dos Estados Unidos (também conhecido como APIs programáticas) de criação são hospedados na geografia de Estados Unidos do Azure. O portal de luis.ai e Estados Unidos (também conhecido como APIs programáticas) de criação dar suporte à implantação de pontos de extremidade a seguir regiões geográficas do Azure:

* Regiões geográficas do Azure não tem suportadas pela Europa ou na Austrália, regiões de criação

Ao implantar essas regiões geográficas do Azure, as declarações recebidas pelo ponto de extremidade dos usuários finais do seu aplicativo serão armazenadas na geografia de Estados Unidos do Azure para o aprendizado ativo. Você pode desabilitar o aprendizado ativo, consulte [desabilitar aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerenciar armazenadas declarações, consulte [excluir expressão](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Referência de regiões do LUIS](./luis-reference-regions.md)
