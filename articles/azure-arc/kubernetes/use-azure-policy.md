---
title: Use o Azure Policy para aplicar configurações de cluster em escala (versão prévia)
services: azure-arc
ms.service: azure-arc
ms.date: 02/10/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Use o Azure Policy para aplicar configurações de cluster em escala
keywords: Kubernetes, Arc, Azure, K8s, containers
ms.openlocfilehash: ce9ba75e200a02654cac4c50303cc90fd0c1a5fd
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390905"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Use o Azure Policy para aplicar configurações de cluster em escala (versão prévia)

## <a name="overview"></a>Visão geral

Você pode usar Azure Policy para impor qualquer um dos seguintes recursos para que sejam `Microsoft.KubernetesConfiguration/sourceControlConfigurations` aplicados específicos:
*  `Microsoft.Kubernetes/connectedclusters` Kit.
* Recurso habilitado para GitOps `Microsoft.ContainerService/managedClusters` . 

Para usar Azure Policy, selecione uma definição de política existente e crie uma atribuição de política. Ao criar a atribuição de política:
1. Defina o escopo da atribuição.
    * O escopo será um grupo de recursos do Azure ou uma assinatura. 
2. Defina os parâmetros para o `sourceControlConfiguration` que será criado. 

Depois que a atribuição é criada, o mecanismo de Azure Policy identifica todos os `connectedCluster` `managedCluster` recursos localizados dentro do escopo e aplica-se a `sourceControlConfiguration` cada um deles.

Você pode habilitar vários repositórios git como as fontes de verdade para cada cluster usando várias atribuições de política. Cada atribuição de política seria configurada para usar um repositório git diferente; por exemplo, um repositório para o operador central de ti/cluster e outros repositórios para equipes de aplicativos.

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

Depois de criar a atribuição de política, o `sourceControlConfiguration` será aplicado a qualquer um dos seguintes recursos localizados dentro do escopo da atribuição:
* Novos `connectedCluster` recursos.
* Novos `managedCluster` recursos com os agentes do GitOps instalados. 

Para clusters existentes, será necessário executar manualmente uma tarefa de correção. Essa tarefa normalmente leva de 10 a 20 minutos para que a atribuição de política entre em vigor.

## <a name="verify-a-policy-assignment"></a>Verificar uma atribuição de política

1. No portal do Azure, navegue até um dos seus `connectedCluster` recursos.
1. Na seção **configurações** da barra lateral, selecione **políticas**. 
    * A UX do cluster AKS ainda não está implementada.
    * Na lista de políticas, você deve ver a atribuição de política que você criou anteriormente com o **estado de conformidade** definido como *compatível*.
1. Na seção **Configurações** da barra lateral, selecione **Configurações**.
    * Na lista configurações, você deve ver o `sourceControlConfiguration` que a atribuição de política criou.
1. Use `kubectl` para interrogar o cluster. 
    * Você deve ver o namespace e os artefatos que foram criados pelo `sourceControlConfiguration` .
    * Em 5 minutos, você deve ver no cluster os artefatos descritos nos manifestos no repositório Git configurado.

## <a name="next-steps"></a>Próximas etapas

* [Configurar o Azure Monitor para Contêineres com clusters Kubernetes habilitados para Arc](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
