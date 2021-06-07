---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: ed5f94bdc372e317e9e312db07665d233b1b4139
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467249"
---
Vá para o portal do Azure e <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="Criar um recurso do Reconhecimento de Formulários" target="_blank">crie um recurso do Reconhecimento de Formulários </a>. No painel **Criar**, forneça as informações a seguir:

| Detalhes do projeto   | Descrição   |
|--|--|
| **Assinatura** | Selecione a assinatura do Azure à qual o acesso foi concedido. |
| **Grupo de recursos** | O [grupo de recursos do Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) que conterá o seu recurso. Você pode criar um grupo ou adicioná-lo a um grupo pré-existente. |
| **Região** | A localização da sua instância de serviço cognitivo. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de runtime do seu recurso. |
| **Nome** | Um nome descritivo para o seu recurso. É recomendável usar um nome descritivo, como, por exemplo, *MyNameFormRecognizer*. |
| **Tipo de preços** | O custo do recurso depende de seu uso e do tipo de preço que você escolheu. Para obter mais informações, consulte a API [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/).

> [!NOTE]
> Normalmente, ao criar um recurso de Serviço Cognitivo no portal do Azure, você tem a opção de criar uma chave de assinatura para vários serviços (usada em vários serviços cognitivos) ou uma chave de assinatura de serviço único (usada somente com um serviço cognitivo específico). No entanto, o Reconhecimento de Formulários não está incluído na assinatura de vários serviços no momento.

Quando o recurso de Reconhecimento de Formulários concluir a implantação, localize-o e selecione-o na lista **Todos os recursos** do portal. Sua chave e ponto de extremidade estarão localizados na página de chave e ponto de extremidade do recurso, sob Gerenciamento de recursos. Salve ambos em um local temporário antes de continuar.
