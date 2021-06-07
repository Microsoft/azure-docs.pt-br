---
title: Implantar associações usando políticas
description: Saiba mais sobre como implantar associações para um provedor personalizado usando o serviço Azure Policy.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 00cd1d39c0110aac9ea96f73127e83197976c95a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "82190122"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Implantar associações para um provedor personalizado usando Azure Policy

As políticas do Azure podem ser usadas para implantar associações para associar recursos a um provedor personalizado. Neste artigo, descrevemos uma política interna que implanta associações e como você pode usar essa política.

## <a name="built-in-policy-to-deploy-associations"></a>Política interna para implantar associações

Implantar associações para um provedor personalizado é uma política interna que pode ser usada para implantar a associação para associar um recurso a um provedor personalizado. A política aceita três parâmetros:

- ID do provedor personalizado-essa ID é a ID de recurso do provedor personalizado ao qual os recursos precisam ser associados.
- Tipos de recursos a serem associados-esses tipos de recursos são a lista de tipos de recursos a serem associados ao provedor personalizado. Você pode associar vários tipos de recursos a um provedor personalizado usando a mesma política.
- Prefixo do nome da Associação-esta cadeia de caracteres é o prefixo a ser adicionado ao nome do recurso de associação que está sendo criado. O valor padrão é "DeployedByPolicy".

A política usa a avaliação DeployIfNotExists. Ele é executado depois que um provedor de recursos manipula uma solicitação de criação ou atualização de recurso e a avaliação retornou um código de status de êxito. Depois disso, o recurso de associação é implantado usando uma implantação de modelo.
Para obter mais informações sobre associações, consulte [integração de recursos de provedores personalizados do Azure](./concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Como usar a política interna implantar associações 

### <a name="prerequisites"></a>Pré-requisitos
Se o provedor personalizado precisar de permissões para o escopo da política para executar uma ação, a implantação da política do recurso de associação não funcionaria sem conceder as permissões.

### <a name="policy-assignment"></a>Atribuição de política
Para usar a política interna, crie uma atribuição de política e atribua as associações de implantação para uma política de provedor personalizado. Em seguida, a política identificará recursos sem conformidade e implantará a associação para esses recursos.

![Atribuir política interna](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Obtendo ajuda

Se você tiver dúvidas sobre o desenvolvimento de provedores de recursos personalizados do Azure, tente solicitá-los em [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante já pode ter sido respondida, portanto, verifique-a primeiro antes de postá-la. Adicione a marca ```azure-custom-providers``` para obter uma resposta rápida.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprenderá a usar a política interna para implantar associações. Confira estes artigos para saber mais:

- [Conceitos: integração de recursos de provedores personalizados do Azure](./concepts-resource-onboarding.md)
- [Tutorial: integração de recursos com provedores personalizados](./tutorial-resource-onboarding.md)
- [Tutorial: criar ações e recursos personalizados no Azure](./tutorial-get-started-with-custom-providers.md)
- [Início rápido: criar um provedor de recursos personalizado e implantar recursos personalizados](./create-custom-provider.md)
- [Como adicionar ações personalizadas a uma API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Como adicionar recursos personalizados a uma API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
