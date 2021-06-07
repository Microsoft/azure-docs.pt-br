---
title: Implantar associações para o aplicativo gerenciado usando a política
description: Saiba mais sobre como implantar associações para um aplicativo gerenciado usando o serviço Azure Policy.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650936"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Implantar associações para um aplicativo gerenciado usando Azure Policy

As políticas do Azure podem ser usadas para implantar associações para associar recursos a um aplicativo gerenciado. Neste artigo, descrevemos uma política interna que implanta associações e como você pode usar essa política.

## <a name="built-in-policy-to-deploy-associations"></a>Política interna para implantar associações

Implantar associações para um aplicativo gerenciado é uma política interna que pode ser usada para implantar a associação para associar um recurso a um aplicativo gerenciado. A política aceita três parâmetros:

- ID do aplicativo gerenciado-essa ID é a ID de recurso do aplicativo gerenciado ao qual os recursos precisam ser associados.
- Tipos de recursos a serem associados-esses tipos de recursos são a lista de tipos de recursos a serem associados ao aplicativo gerenciado. Você pode associar vários tipos de recursos a um aplicativo gerenciado usando a mesma política.
- Prefixo do nome da Associação-esta cadeia de caracteres é o prefixo a ser adicionado ao nome do recurso de associação que está sendo criado. O valor padrão é "DeployedByPolicy".

A política usa a avaliação DeployIfNotExists. Ele é executado depois que um provedor de recursos tratou de uma solicitação criar ou atualizar recurso dos tipos de recursos selecionados e a avaliação tiver retornado um código de status de êxito. Depois disso, o recurso de associação é implantado usando uma implantação de modelo.
Para obter mais informações sobre associações, consulte [integração de recursos de provedores personalizados do Azure](../custom-providers/concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Como usar a política interna implantar associações 

### <a name="prerequisites"></a>Pré-requisitos
Se o aplicativo gerenciado precisar de permissões para a assinatura para executar uma ação, a implantação de política do recurso de associação não funcionaria sem conceder as permissões.

### <a name="policy-assignment"></a>Atribuição de política
Para usar a política interna, crie uma atribuição de política e atribua as associações de implantação para uma política de aplicativo gerenciado. Depois que a política tiver sido atribuída com êxito, a política identificará recursos sem conformidade e implantará a associação para esses recursos.

![Atribuir política interna](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Obtendo ajuda

Se você tiver dúvidas sobre o desenvolvimento de provedores de recursos personalizados do Azure, tente solicitá-los em [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante já pode ter sido respondida, portanto, verifique-a primeiro antes de postá-la. Adicione a marca ```azure-custom-providers``` para obter uma resposta rápida.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprenderá a usar a política interna para implantar associações. Confira estes artigos para saber mais:

- [Conceitos: integração de recursos de provedores personalizados do Azure](../custom-providers/concepts-resource-onboarding.md)
- [Tutorial: integração de recursos com provedores personalizados](../custom-providers/tutorial-resource-onboarding.md)
- [Tutorial: criar ações e recursos personalizados no Azure](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [Início rápido: criar um provedor de recursos personalizado e implantar recursos personalizados](../custom-providers/create-custom-provider.md)
- [Como adicionar ações personalizadas a uma API REST do Azure](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [Como adicionar recursos personalizados a uma API REST do Azure](../custom-providers/custom-providers-resources-endpoint-how-to.md)
