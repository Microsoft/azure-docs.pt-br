---
title: Entender como o conteúdo de uma máquina virtual de auditoria
description: Saiba como a política do Azure usa Rego e abrir agente de política para gerenciar clusters no serviço Kubernetes do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fdb392533e28df1d50e90c842d0117385afb254b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453899"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Entender a política do Azure para o serviço Kubernetes do Azure

A política do Azure integra-se com o [serviço Kubernetes do Azure](../../../aks/intro-kubernetes.md) (AKS) para aplicar imposições em escala e proteções em seus clusters de maneira centralizada e consistente.
Ao estender o uso de [GateKeeper](https://github.com/open-policy-agent/gatekeeper), um _admissão controlador webhook_ para [abrir agente de política](https://www.openpolicyagent.org/) (OPA), a política do Azure possibilita gerenciar e relatar a conformidade estado de seus recursos do Azure e clusters AKS em um só lugar.

> [!NOTE]
> A política do Azure para o AKS está em Visualização limitada e só dá suporte a definições de políticas internas.

## <a name="overview"></a>Visão Geral

Para habilitar e usar a Azure Policy para AKS com o cluster AKS, execute as seguintes ações:

- [Inscreva-se para recursos de visualização](#opt-in-for-preview)
- [Instalar o suplemento de política do Azure](#installation-steps)
- [Atribuir uma definição de política para o AKS](#built-in-policies)
- [Aguarde a validação](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Inscreva-se para visualização

Antes de instalar o complemento de política do Azure ou habilitar qualquer um dos recursos de serviço, sua assinatura deve habilitar o **containerservice** provedor de recursos e o **policyinsights**provedor de recursos, em seguida, ser aprovado para ingressar na visualização. Para ingressar na visualização, siga estas etapas em qualquer portal do Azure ou com a CLI do Azure:

- Portal do Azure:

  1. Registre-se a **containerservice** e **policyinsights** provedores de recursos. Para obter as etapas, consulte [provedores de recursos e tipos](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

  1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

     ![Pesquisar Política em Todos os Serviços](../media/rego-for-aks/search-policy.png)

  1. Selecione **ingressar visualização** no lado esquerdo da página de política do Azure.

     ![Junte-se a política para a versão prévia do AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Selecione a linha da assinatura que você deseja adicionar a visualização.

  1. Selecione o **Opt-in** botão na parte superior da lista de assinaturas.

- CLI do Azure:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  ```

## <a name="azure-policy-add-on"></a>Complemento de política do Azure

O _complemento de política do Azure_ para Kubernetes conecta o serviço de política do Azure para o controlador de admissão do GateKeeper. O complemento, que é instalado para o _política do azure_ namespace, ele impõe as seguintes funções:

- Verificações de atribuições para o cluster do AKS com o Azure Policy
- Baixa e armazena em cache os detalhes da política, incluindo o _rego_ definição de política, como **configmaps**
- Executa uma verificação de conformidade de verificação completa no cluster do AKS
- Relatórios de auditoria e detalhes de conformidade de volta para o Azure Policy

### <a name="installing-the-add-on"></a>Instalar o complemento

#### <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o complemento no cluster do AKS, a extensão de visualização deve ser instalada. Esta etapa é feita com a CLI do Azure:

1. Você precisa da CLI do Azure versão 2.0.62 ou posterior instalado e configurado. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Versão deve ser o cluster do AKS _1.10_ ou superior. Use o script a seguir para validar a sua versão de cluster do AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Instalar a versão _0.4.0_ da CLI do Azure extensão em versão prévia para o AKS, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Se você instalou anteriormente a _versão prévia do aks_ extensão, instale quaisquer atualizações usando o `az extension update --name aks-preview` comando.

#### <a name="installation-steps"></a>Etapas de instalação

Depois que os pré-requisitos forem concluídos, instale o suplemento de política do Azure no cluster do AKS que você deseja gerenciar.

- Portal do Azure

  1. Inicie o serviço AKS no portal do Azure clicando **todos os serviços**, em seguida, procurando e selecionando **serviços Kubernetes**.

  1. Selecione um dos seus clusters AKS.

  1. Selecione **políticas (visualização)** no lado esquerdo da página de serviço do Kubernetes.

     ![Políticas do cluster AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na página principal, selecione a **ativar complemento** botão.

     ![Habilitar a política do Azure para o complemento do AKS](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Se o **ativar complemento** botão estiver desabilitado, a assinatura ainda não foram adicionada à versão prévia. Ver [Opt-in para visualização](#opt-in-for-preview) para as etapas necessárias.

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Validação e a frequência do relatório

O complemento verifica com a política do Azure para que as alterações em atribuições de política a cada 5 minutos. Durante esse ciclo de atualização, o complemento remove todos os _configmaps_ na _política do azure_ namespace, em seguida, recria o _configmaps_ para uso do GateKeeper.

> [!NOTE]
> Enquanto um _administrador de cluster_ pode ter permissão para o _política do azure_ namespace, não recomendado ou suporte para fazer alterações ao namespace. Todas as alterações manuais feitas serão perdidas durante o ciclo de atualização.

A cada 5 minutos, o complemento chamadas para uma verificação completa do cluster. Depois de coletar detalhes sobre a verificação completa e quaisquer avaliações em tempo real pelo GateKeeper da tentativa de alteração para o cluster, o complemento relata os resultados novamente a política do Azure para inclusão nos [detalhes de conformidade](../how-to/get-compliance-data.md) como qualquer política do Azure atribuição. Somente os resultados para atribuições de política do Active Directory são retornados durante o ciclo de auditoria.

## <a name="policy-language"></a>Idioma de política

A estrutura de linguagem de política do Azure para gerenciar o AKS segue de políticas existentes. O efeito _EnforceRegoPolicy_ é usado para gerenciar os clusters do AKS e leva _detalhes_ propriedades específicas para trabalhar com OPA e GateKeeper. Para obter detalhes e exemplos, consulte o [EnforceRegoPolicy](effects.md#enforceregopolicy) em vigor.

Como parte do _details.policy_ propriedade na definição de política, o Azure Policy passa o URI de uma política de rego ao complemento. Rego é a linguagem que dão suporte OPA e GateKeeper para validar ou modificar uma solicitação para o cluster Kubernetes. Dando um padrão existente para o gerenciamento de Kubernetes, Azure Policy possibilita reutilizar as regras existentes e pareá-las com o Azure Policy para uma experiência de relatório de conformidade de nuvem unificada. Para obter mais informações, consulte [What ' s Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Políticas internas

Para localizar as políticas internas para gerenciar o AKS usando o portal do Azure, siga estas etapas:

1. Inicie o serviço de política do Azure no portal do Azure. Selecione **todos os serviços** no painel esquerdo e, em seguida, procure e selecione **diretiva**.

1. No painel esquerdo da página de política do Azure, selecione **definições**.

1. Na caixa de lista suspensa Categoria, use **Selecionar tudo** para limpar o filtro e, em seguida, selecione **serviço do Kubernetes**.

1. Selecione a definição de política e selecione o **atribuir** botão.

> [!NOTE]
> Ao atribuir a política do Azure para a definição do AKS, o **escopo** deve incluir o recurso de cluster do AKS.

Como alternativa, use o [atribuir uma política – Portal](../assign-policy-portal.md) início rápido para localizar e atribua uma política do AKS. Pesquise uma definição de política de Kubernetes, em vez de amostra 'auditar vms'.

## <a name="logging"></a>Registrando em log

### <a name="azure-policy-add-on-logs"></a>Logs de complemento de política do Azure

Como um controlador de Kubernetes/contêiner, o complemento de política do Azure mantém os logs no cluster AKS. Os logs são expostos na **Insights** página do cluster AKS. Para obter mais informações, consulte [desempenho com o Azure Monitor para contêineres de cluster do AKS entender](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Logs do gateKeeper

Para habilitar os logs do GateKeeper para novas solicitações de recursos, siga as etapas em [habilitar e examine os logs do nó mestre no AKS do Kubernetes](../../../aks/view-master-logs.md).
Aqui está um exemplo de consulta para exibir eventos negados em novas solicitações de recursos:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Para exibir logs de contêineres do GateKeeper, siga as etapas em [habilitar e examine os logs do nó mestre no AKS do Kubernetes](../../../aks/view-master-logs.md) e verifique o _kube apiserver_ opção o **configuraçõesdediagnóstico** painel.

## <a name="remove-the-add-on"></a>Remover o complemento

Para remover o complemento de política do Azure no cluster do AKS, use o portal do Azure ou a CLI do Azure:

- Portal do Azure

  1. Inicie o serviço AKS no portal do Azure clicando **todos os serviços**, em seguida, procurando e selecionando **serviços Kubernetes**.

  1. Selecione o cluster do AKS, onde você deseja desabilitar o complemento de política do Azure.

  1. Selecione **políticas (visualização)** no lado esquerdo da página de serviço do Kubernetes.

     ![Políticas do cluster AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na página principal, selecione a **desabilitar o complemento** botão.

     ![Desabilitar a política do Azure para o complemento do AKS](../media/rego-for-aks/disable-policy-add-on.png)

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>Próximas etapas

- Examine os exemplos na [exemplos do Azure Policy](../samples/index.md).
- Revisar a [Estrutura de definição de política](definition-structure.md).
- Revisar [Compreendendo os efeitos da política](effects.md).
- Entender como [criar políticas de forma programática](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/index.md).