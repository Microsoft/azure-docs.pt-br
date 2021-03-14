---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: edcbeb15b58d59bf080a1acf1d54f1b60e109c8a
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425718"
---
Vá para o portal do Azure e <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="Criar um recurso do Reconhecimento de Formulários" target="_blank">crie um recurso do Reconhecimento de Formulários </a>. No painel **Criar**, forneça as informações a seguir:

|    |    |
|--|--|
| **Nome** | Um nome descritivo para o seu recurso. É recomendável usar um nome descritivo, como, por exemplo, *MyNameFormRecognizer*. |
| **Assinatura** | Selecione a assinatura do Azure à qual o acesso foi concedido. |
| **Localidade** | A localização da sua instância de serviço cognitivo. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de runtime do seu recurso. |
| **Tipo de preços** | O custo do recurso depende de seu uso e do tipo de preço que você escolheu. Para obter mais informações, consulte a API [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
| **Grupo de recursos** | O [grupo de recursos do Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) que conterá o seu recurso. Você pode criar um grupo ou adicioná-lo a um grupo pré-existente. |

> [!NOTE]
> Normalmente, ao criar um recurso de Serviço Cognitivo no portal do Azure, você tem a opção de criar uma chave de assinatura para vários serviços (usada em vários serviços cognitivos) ou uma chave de assinatura de serviço único (usada somente com um serviço cognitivo específico). No entanto, o Reconhecimento de Formulários não está incluído na assinatura de vários serviços no momento.

Quando o recurso de Reconhecimento de Formulários concluir a implantação, localize-o e selecione-o na lista **Todos os recursos** do portal. Sua chave e ponto de extremidade estarão localizados na página de chave e ponto de extremidade do recurso, sob Gerenciamento de recursos. Salve ambos em um local temporário antes de continuar.