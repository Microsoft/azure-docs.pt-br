---
title: Use o Azure Policy para aplicar configurações de cluster em escala
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Use o Azure Policy para aplicar configurações de cluster em escala
keywords: Kubernetes, Arc, Azure, K8s, containers
ms.openlocfilehash: 05a6665a985ef8b229ee58082dc9b2c10cdcece3
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121449"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale"></a>Use o Azure Policy para aplicar configurações de cluster em escala

Você pode usar Azure Policy para aplicar configurações ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` tipo de recurso) em escala em clusters kubernetes habilitados para Arc do Azure ( `Microsoft.Kubernetes/connectedclusters` ).

Para usar Azure Policy, selecione uma definição de política existente e crie uma atribuição de política. Ao criar a atribuição de política:
1. Defina o escopo da atribuição.
    * O escopo será um grupo de recursos do Azure ou uma assinatura. 
2. Defina os parâmetros para a configuração que será criada. 

Depois que a atribuição é criada, Azure Policy mecanismo identifica todos os clusters kubernetes habilitados para o Azure Arc localizados no escopo e aplica a configuração a cada cluster.

Você pode criar várias configurações, cada uma apontando para um repositório git diferente, usando várias atribuições de política. Por exemplo, um repositório para o operador central de ti/cluster e outros repositórios para equipes de aplicativos.

## <a name="prerequisite"></a>Pré-requisito

Verifique se você tem `Microsoft.Authorization/policyAssignments/write` permissões no escopo (assinatura ou grupo de recursos) em que você criará essa atribuição de política.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

1. No portal do Azure, navegue até a **política**.
1. Na seção **criação** da barra lateral, selecione **definições**.
1. Na categoria "kubernetes", escolha a política interna "implantar GitOps no cluster kubernetes". 
1. Clique em **Atribuir**.
1. Defina o **escopo** para o grupo de gerenciamento, a assinatura ou o grupo de recursos ao qual a atribuição de política será aplicada.
    * Se você quiser excluir todos os recursos do escopo da política, defina **exclusões**.
1. Dê à atribuição de política um **nome** e uma **Descrição** facilmente identificáveis.
1. Verifique se a **imposição de política** está definida como **habilitada**.
1. Selecione **Avançar**.
1. Defina os valores de parâmetro a serem usados ao criar o `sourceControlConfiguration` .
1. Selecione **Avançar**.
1. Habilitar **Criar uma tarefa de correção**.
1. Verifique se a opção **criar uma identidade gerenciada** está marcada e se a identidade terá permissões de **colaborador** . 
    * Para obter mais informações, consulte o guia de [início rápido criar uma atribuição de política](../../governance/policy/assign-policy-portal.md) e [corrigir recursos não compatíveis com Azure Policy artigo](../../governance/policy/how-to/remediate-resources.md).
1. Selecione **Examinar + criar**.

Depois de criar a atribuição de política, a configuração é aplicada a novos clusters kubernetes habilitados para Arc do Azure criados dentro do escopo de atribuição de política.

Para os clusters existentes, você precisará executar manualmente uma tarefa de correção. Essa tarefa normalmente leva de 10 a 20 minutos para que a atribuição de política entre em vigor.

## <a name="verify-a-policy-assignment"></a>Verificar uma atribuição de política

1. No portal do Azure, navegue até um dos seus clusters kubernetes habilitados para o Azure Arc.
1. Na seção **configurações** da barra lateral, selecione **políticas**. 
    * Na lista de políticas, você deve ver a atribuição de política que você criou anteriormente com o **estado de conformidade** definido como *compatível*.
1. Na seção **Configurações** da barra lateral, selecione **Configurações**.
    * Na lista configurações, você deve ver a configuração criada pela atribuição de política.
1. Use `kubectl` para interrogar o cluster. 
    * Você deve ver o namespace e os artefatos que foram criados pelos recursos de configuração.
    * Em 5 minutos (supondo que o cluster tenha conectividade de rede com o Azure), você deve ver os objetos descritos pelos manifestos no repositório git, sendo criados no cluster.

## <a name="next-steps"></a>Próximas etapas

[Configure Azure monitor para contêineres com clusters kubernetes habilitados para o Azure Arc](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md).
