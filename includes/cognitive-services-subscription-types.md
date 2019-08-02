---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 674dd30ff3e493ec4c4036f032f82624a6ca5749
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334238"
---
## <a name="azure-cognitive-service-resource-types"></a>Tipos de recursos de serviço cognitiva do Azure

> [!NOTE]
> Os proprietários da assinatura podem desabilitar a criação de recursos de serviços cognitivas para assinaturas e grupos de recursos aplicando a [política do Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), atribuindo uma definição de política "tipos de recursos não permitidos" e especificando  **Microsoft. Cognitivaservices/** accounts como o tipo de recurso de destino.

Você pode acessar os serviços cognitivas do Azure por meio de dois recursos diferentes: Um recurso de vários serviços ou um único serviço. Essas assinaturas permitem que você se conecte a um único serviço ou a vários serviços de uma vez.

### <a name="multi-service-resource"></a>Recurso de vários serviços

>[!WARNING]
> No momento, estes serviços **não** têm suporte para chaves de vários serviços: QnA Maker, serviços de fala, Visão Personalizada e detector de anomalias.

Inscrevendo-se em um recurso de serviços cognitivas de vários serviços:
* Permite que você use um único recurso do Azure para a maioria dos serviços cognitivas do Azure.
* Consolida a cobrança dos serviços que você usa. Consulte [Preços dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) para obter mais informações.

### <a name="single-service-resource"></a>Recurso de serviço único

Recursos de serviço único (como Pesquisa Visual Computacional ou serviços de fala) são restritos ao serviço especificado.