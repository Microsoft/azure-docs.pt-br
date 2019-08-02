---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: dce9b4ffa05b48fc1789859328b221ac1a0f3a27
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594447"
---
Quando o acesso para usar o Reconhecimento de Formulários for concedido, você receberá um email de boas-vindas com vários links e recursos. Use o link do "portal do Azure" na mensagem para abrir o portal do Azure e criar um recurso do Reconhecimento de Formulários. No painel **Criar**, forneça as informações a seguir:

|    |    |
|--|--|
| **Nome** | Um nome descritivo para o seu recurso. É recomendável usar um nome descritivo, como, por exemplo, *MyNameFormRecognizer*. |
| **Assinatura** | Selecione a assinatura do Azure à qual o acesso foi concedido. |
| **Localidade** | A localização da sua instância de serviço cognitivo. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de tempo de execução do seu recurso. |
| **Tipo de preços** | O custo do recurso depende de seu uso e do tipo de preço que você escolheu. Para obter mais informações, consulte a API [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
| **Grupo de recursos** | O [grupo de recursos do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) que conterá o seu recurso. Você pode criar um grupo ou adicioná-lo a um grupo pré-existente. |

> [!IMPORTANT]
> Normalmente, ao criar um recurso de Serviço Cognitivo no portal do Azure, você tem a opção de criar uma chave de assinatura para vários serviços (usada em vários serviços cognitivos) ou uma chave de assinatura de serviço único (usada somente com um serviço cognitivo específico). No entanto, como o Reconhecimento de Formulários é uma versão prévia, ele não está incluído na assinatura vários serviços e você não pode criar a assinatura de serviço único, a menos que você use o link fornecido no email de boas-vindas.

Quando o recurso de Reconhecimento de Formulários concluir a implantação, localize-o e selecione-o na lista **Todos os recursos** do portal. Em seguida, selecione a guia **Chaves** para exibir suas chaves de assinatura. A chave fornecerá ao aplicativo o acesso ao recurso. Copie o valor de **CHAVE 1**.