---
title: Saiba Azure Policy para kubernetes
description: Saiba como o Azure Policy usa o Rego e o Open Policy Agent para gerenciar clusters que executam o Kubernetes no Azure ou localmente.
ms.date: 03/22/2021
ms.topic: conceptual
ms.openlocfilehash: 4a6ca7fb5086401cab58d4bfb6a412089c11ef05
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564298"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>Noções básicas sobre clusters do Azure Policy para Kubernetes

O Azure Policy estende o [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) V3, um _webhook do controlador de admissão_ para o [OPA](https://www.openpolicyagent.org/) (Open Policy Agent), para aplicar imposições e garantias em escala em seus clusters de maneira centralizada e consistente. O Azure Policy possibilita gerenciar e relatar o estado de conformidade de seus clusters do Kubernetes de um único lugar. O complemento coloca em prática nas seguintes funções:

- Verificar atribuições de política ao cluster com o serviço Azure Policy.
- Implantar definições de política no cluster como [modelo de restrição](https://github.com/open-policy-agent/gatekeeper#constraint-templates) e recursos personalizados de [restrição](https://github.com/open-policy-agent/gatekeeper#constraints).
- Relatar os detalhes de auditoria e de conformidade de volta ao serviço Azure Policy.

O Azure Policy para Kubernetes dá suporte aos seguintes ambientes de cluster:

- [AKS (Serviço de Kubernetes do Azure)](../../../aks/intro-kubernetes.md)
- [Kubernetes habilitado para Azure Arc](../../../azure-arc/kubernetes/overview.md)
- [Mecanismo do AKS](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> Os Complementos para o mecanismo AKS e o kubernetes habilitado para Arc estão em versão **prévia**. Azure Policy para kubernetes dá suporte apenas a pools de nós do Linux e definições de políticas internas. As definições de políticas internas estão na categoria **Kubernetes**. As definições de política de visualização limitada com o efeito **EnforceOPAConstraint** e **EnforceRegoPolicy** e a categoria de **serviço kubernetes** relacionado são _preteridas_. Em vez disso, use a _auditoria_ de efeitos e a _negação_ com o modo do provedor de recursos `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Visão geral

Para habilitar e usar o Azure Policy com o cluster do Kubernetes, execute as seguintes ações:

1. Configure o cluster do Kubernetes e instale o complemento:
   - [AKS (Serviço de Kubernetes do Azure)](#install-azure-policy-add-on-for-aks)
   - [Kubernetes habilitado para Azure Arc](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [Mecanismo do AKS](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Para problemas comuns com a instalação, consulte o [complemento solucionar problemas-Azure Policy](../troubleshoot/general.md#add-on-for-kubernetes-installation-errors).

1. [Entender a linguagem do Azure Policy para Kubernetes](#policy-language)

1. [Atribuir uma definição interna ao cluster do Kubernetes](#assign-a-built-in-policy-definition)

1. [Aguardar a validação](#policy-evaluation)

## <a name="limitations"></a>Limitações

As seguintes limitações gerais se aplicam ao complemento de Azure Policy para clusters kubernetes:

- O Azure Policy complemento para kubernetes tem suporte no kubernetes versão **1,14** ou superior.
- Azure Policy complemento para kubernetes só pode ser implantado em pools de nós do Linux
- Há suporte apenas para definições de política internas
- Número máximo de registros não compatíveis por política por cluster: **500**
- Número máximo de registros não compatíveis por assinatura: **1 milhão**
- Não há suporte para instalações do gatekeeper fora do Azure Policy complemento. Desinstale todos os componentes instalados por uma instalação anterior do gatekeeper antes de habilitar o complemento de Azure Policy.
- Os [motivos para não conformidade](../how-to/determine-non-compliance.md#compliance-reasons) não estão disponíveis para o `Microsoft.Kubernetes.Data` 
   [modo do provedor de recursos](./definition-structure.md#resource-provider-modes). Use os [detalhes do componente](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).
- Não há suporte para [isenções](./exemption-structure.md) nos [modos de provedor de recursos](./definition-structure.md#resource-provider-modes).

As seguintes limitações se aplicam somente ao complemento Azure Policy para AKS:

- A [política de segurança de Pod AKs](../../../aks/use-pod-security-policies.md) e o complemento Azure Policy para AKs não podem ser habilitados. Para obter mais informações, consulte [limitação de segurança do pod AKs](../../../aks/use-azure-policy.md).
- Namespaces excluídos automaticamente por Azure Policy complemento para avaliação: _Kube-System_, _gatekeeper-System_ e _AKs-Periscope_.

## <a name="recommendations"></a>Recomendações

Veja a seguir as recomendações gerais para usar o complemento Azure Policy:

- O complemento de Azure Policy requer três componentes de gatekeeper para executar: 1 pod de auditoria e 2 réplicas de pod de webhook. Esses componentes consomem mais recursos, pois a contagem de recursos kubernetes e atribuições de política aumenta no cluster, o que exige operações de auditoria e imposição.

  - Para menos de 500 pods em um único cluster com um máximo de 20 restrições: 2 vCPUs e 350 MB de memória por componente.
  - Para mais de 500 pods em um único cluster com um máximo de 40 restrições: 3 vCPUs e 600 MB de memória por componente.

- Os pods do Windows [não dão suporte a contextos de segurança](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods).
  Portanto, algumas das definições de Azure Policy, como a despermissão de privilégios raiz, não podem ser escalonadas no pods do Windows e se aplicam somente ao pods do Linux.

A recomendação a seguir aplica-se somente ao AKS e ao complemento Azure Policy:

- Use o pool de nós do sistema com o `CriticalAddonsOnly` comparado para agendar pods de gatekeeper. Para obter mais informações, consulte [usando pools de nós do sistema](../../../aks/use-system-pools.md#system-and-user-node-pools).
- Proteja o tráfego de saída de seus clusters AKS. Para obter mais informações, consulte [controlar o tráfego de saída para nós de cluster](../../../aks/limit-egress-traffic.md).
- Se o cluster tiver `aad-pod-identity` habilitado, o pods de identidade gerenciada por nó (NMI) modificará os iptables dos nós para interceptar chamadas para o ponto de extremidade de metadados da instância do Azure. Essa configuração significa que qualquer solicitação feita ao ponto de extremidade de metadados é interceptada por NMI, mesmo que o Pod não use `aad-pod-identity` . AzurePodIdentityException CRD pode ser configurado para informar `aad-pod-identity` que todas as solicitações para o ponto de extremidade de metadados provenientes de um pod que corresponda aos rótulos definidos em CRD devem ser proxies sem nenhum processamento em NMI. O pods do sistema com `kubernetes.azure.com/managedby: aks` rótulo no namespace _Kube-System_ deve ser excluído no `aad-pod-identity` Configurando o AzurePodIdentityException CRD. Para obter mais informações, consulte [desabilitar AAD-Pod-Identity para um pod ou aplicativo específico](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Para configurar uma exceção, instale o [YAML de exceção do MIC](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="install-azure-policy-add-on-for-aks"></a>Instalar complemento do Azure Policy para AKS

Antes de instalar o complemento Azure Policy ou habilitar qualquer um dos recursos de serviço, sua assinatura deve habilitar os provedores de recursos **Microsoft. PolicyInsights** .

1. Você precisa do CLI do Azure versão 2.12.0 ou posterior instalado e configurado. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Registre os provedores de recursos e as versões prévias do recurso.

   - Portal do Azure:

     Registre os provedores de recursos **Microsoft. PolicyInsights** . Para obter as etapas, confira [Provedores e tipos de recurso](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

   - CLI do Azure:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Se as definições de política de visualização limitada foram instaladas, remova o complemento com o botão **desabilitar** no cluster AKs na página **políticas** .

1. O cluster do AKS deve ser a versão _1.14_ ou superior. Use o seguinte script para validar a versão do cluster do AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Instale a versão _2.12.0_ ou superior do CLI do Azure. Para obter mais informações, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Depois que as etapas de pré-requisito acima forem concluídas, instale o complemento do Azure Policy no cluster do AKS que você deseja gerenciar.

- Portal do Azure

  1. Inicie o serviço AKS no portal do Azure selecionando **todos os serviços**, em seguida, procurando e selecionando **Serviços Kubernetess**.

  1. Selecione um dos clusters do AKS.

  1. Selecione **políticas** no lado esquerdo da página do serviço kubernetes.

  1. Na página principal, selecione o botão **Habilitar complemento**.

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > Se o botão **desabilitar complemento** estiver habilitado e uma mensagem de aviso de migração v2 for exibida, o complemento v1 será instalado e deverá ser removido antes de atribuir as definições de política v2. O complemento v1 _preterido_ será substituído automaticamente pelo complemento v2 a partir de 24 de agosto,
     > 2020. As novas versões v2 das definições de política devem ser atribuídas. Para atualizar agora, siga estas etapas:
     >
     > 1. Valide seu cluster AKS tem o complemento v1 instalado visitando a página **políticas** no cluster AKs e tem o "o cluster atual usa Azure Policy complemento v1..." Mensagem.
     > 1. [Remova o complemento](#remove-the-add-on-from-aks).
     > 1. Selecione o botão **habilitar complemento** para instalar a versão v2 do complemento.
     > 1. [Atribuir versões v2 de suas definições de política internas v1](#assign-a-built-in-policy-definition)

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Para validar que a instalação do complemento foi bem-sucedida e que os pods _azure-policy_ e _gatekeeper_ estão em execução, execute o seguinte comando:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Por fim, verifique se o complemento mais recente está instalado com a execução deste comando da CLI do Azure, substituindo `<rg>` pelo nome do grupo de recursos e `<cluster-name>` pelo nome do cluster do AKS: `az aks show --query addonProfiles.azurepolicy -g <rg> -n <cluster-name>`. O resultado deve ser semelhante à saída abaixo e **config.version** deve ser `v2`:

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Instalar o complemento Azure Policy para o kubernetes habilitado para Arc do Azure (versão prévia)

Antes de instalar o complemento do Azure Policy ou de habilitar qualquer um dos recursos de serviço, sua assinatura deve habilitar o provedor de recursos **Microsoft.PolicyInsights** e criar uma atribuição de função para a entidade de serviço de cluster.

1. Você precisa do CLI do Azure versão 2.12.0 ou posterior instalado e configurado. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Para habilitar o provedor de recursos, siga as etapas em [Provedores de recursos e tipos](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ou execute o comando do CLI do Azure ou do Azure PowerShell:

   - CLI do Azure

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. O cluster do Kubernetes deve ser a versão _1.14_ ou superior.

1. Instalar o [Helm 3](https://v3.helm.sh/docs/intro/install/).

1. O cluster do Kubernetes está habilitado para o Azure Arc. Para obter mais informações, confira [Integração de um cluster do Kubernetes com o Azure Arc](../../../azure-arc/kubernetes/quickstart-connect-cluster.md).

1. Tenha a ID do recurso do Azure totalmente qualificada do cluster do Kubernetes habilitado para Azure Arc.

1. Portas abertas para o complemento. O complemento do Azure Policy usa esses domínios e essas portas para buscar definições de política e atribuições e para reportar a conformidade do cluster para o Azure Policy.

   |Domínio |Porta |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Faça a atribuição de função “Gravador de Dados de Insights do Policy (Versão Prévia)” ao cluster do Kubernetes habilitado para Azure Arc. Substitua `<subscriptionId>` pela sua ID da assinatura, `<rg>` pelo grupo de recursos do cluster do Kubernetes habilitado para Azure Arc e `<clusterName>` pelo nome do cluster do Kubernetes habilitado para Azure Arc. Anote os valores retornados de _appId_, _senha_ e _locatário_ para as etapas de instalação.

   - CLI do Azure

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Exemplo de saída dos comandos acima:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

Depois que as etapas de pré-requisito acima forem concluídas, instale o complemento do Azure Policy em seu cluster do Kubernetes habilitado para Azure Arc:

1. Adicione o repositório do complemento do Azure Policy ao Helm:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Instale o complemento do Azure Policy usando o Pacote do Helm:

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   Para obter mais informações sobre o que o gráfico do Helm do complemento instala, consulte a [Definição do gráfico do Helm do complemento do Azure Policy](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) no GitHub.

Para validar que a instalação do complemento foi bem-sucedida e que os pods _azure-policy_ e _gatekeeper_ estão em execução, execute o seguinte comando:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>Instalar o complemento Azure Policy para o mecanismo AKS (visualização)

Antes de instalar o complemento do Azure Policy ou de habilitar qualquer um dos recursos de serviço, sua assinatura deve habilitar o provedor de recursos **Microsoft.PolicyInsights** e criar uma atribuição de função para a entidade de serviço de cluster.

1. Você precisará da CLI do Azure versão 2.0.62 ou posterior instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Para habilitar o provedor de recursos, siga as etapas em [Provedores de recursos e tipos](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ou execute o comando do CLI do Azure ou do Azure PowerShell:

   - CLI do Azure

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Criar uma atribuição de função para a entidade de serviço de cluster.

   - Se você não souber a ID do aplicativo de entidade de serviço de cluster, procure o comando a seguir.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Atribua a atribuição de função “Gravador de dados de informações de política (visualização)” à ID do aplicativo de entidade de serviço de cluster (valor _aadClientID_ da etapa anterior) com a CLI do Azure. Substitua `<subscriptionId>` pela sua ID de assinatura e `<aks engine cluster resource group>` pelo grupo de recursos no qual o cluster do Kubernetes autogerenciado do Mecanismo AKS está.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Depois que as etapas de pré-requisito acima forem concluídas, instale o complemento do Azure Policy. A instalação pode ser durante o ciclo de criação ou de atualização de um Mecanismo AKS ou como uma ação independente em um cluster existente.

- Instalar durante o ciclo de criação ou de atualização

  Para habilitar o complemento do Azure Policy durante a criação de um novo cluster autogerenciado ou como uma atualização de um cluster existente, inclua a definição de cluster de propriedade [addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) para o Mecanismo AKS.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Para obter mais informações, consulte o guia externo [Definição de cluster do Mecanismo AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Instalar no cluster existente com Gráficos Helm

  Use as etapas a seguir para preparar o cluster e instalar o complemento:

  1. Instalar o [Helm 3](https://v3.helm.sh/docs/intro/install/).

  1. Adicione o repositório do Azure Policy ao Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Para obter mais informações [Gráfico do Helm – Guia de Início Rápido](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Instale o complemento com um gráfico do Helm. Substitua `<subscriptionId>` pela sua ID de assinatura e `<aks engine cluster resource group>` pelo grupo de recursos no qual o cluster do Kubernetes autogerenciado do Mecanismo AKS está.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Para obter mais informações sobre o que o gráfico do Helm do complemento instala, consulte a [Definição do gráfico do Helm do complemento do Azure Policy](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) no GitHub.

     > [!NOTE]
     > Devido à relação entre o complemento do Azure Policy e a ID do grupo de recursos, o Azure Policy dá suporte a apenas um cluster do Mecanismo AKS para cada grupo de recursos.

Para validar que a instalação do complemento foi bem-sucedida e que os pods _azure-policy_ e _gatekeeper_ estão em execução, execute o seguinte comando:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Linguagem do Azure Policy

A estrutura de linguagem do Azure Policy para gerenciar o Kubernetes segue as definições de política existentes. Com um [modo de provedor de recursos](./definition-structure.md#resource-provider-modes) de `Microsoft.Kubernetes.Data` , os efeitos [auditoria](./effects.md#audit) e [negação](./effects.md#deny) são usados para gerenciar seus clusters kubernetes. _Audit_ e _Deny_ devem fornecer propriedades de **detalhes** específicas para trabalhar com a [estrutura de restrição Opa](https://github.com/open-policy-agent/frameworks/tree/master/constraint) e o gatekeeper v3.

Como parte das propriedades _details.constraintTemplate_ e _details.constraint_ na definição de política, o Azure Policy passa os URIs desses [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) para o complemento. Rego é a linguagem que o OPA e o Gatekeeper suportam para validar uma solicitação ao cluster do Kubernetes. Dando suporte a um padrão existente para o gerenciamento do Kubernetes, o Azure Policy torna possível reutilizar as regras existentes e as emparelhar com o Azure Policy para uma experiência unificada de relatórios de conformidade de nuvem. Para obter mais informações, consulte [O que é Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="assign-a-built-in-policy-definition"></a>Atribuir uma definição de política incorporada

Para atribuir uma definição de política ao cluster do kubernetes, você deve receber as operações de atribuição de política apropriadas do Azure RBAC (controle de acesso baseado em função). O colaborador e o **proprietário** da **política de recursos** de funções internas do Azure têm essas operações. Para saber mais, confira [permissões do RBAC do Azure no Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

Encontre as definições de política internas para gerenciar seu cluster por meio do portal do Azure com as seguintes etapas:

1. Inicie o serviço do Azure Policy no portal do Azure. Selecione **Todos os serviços** no painel esquerdo e, em seguida, pesquise e selecione **Política**.

1. No painel esquerdo da página do Azure Policy, selecione **Definições**.

1. Na caixa de listagem suspensa Categoria, use **Selecionar tudo** para limpar o filtro e, em seguida, selecione **Kubernetes**.

1. Selecione a definição de política e, em seguida, o botão **Atribuir**.

1. Defina o **Escopo** do grupo de gerenciamento, a assinatura ou o grupo de recursos do cluster do Kubernetes ao qual a atribuição de política será aplicada.

   > [!NOTE]
   > Ao atribuir o Azure Policy à definição de Kubernetes, o **Escopo** precisará incluir o recurso de cluster. Para um cluster do Mecanismo do AKS, o **Escopo** precisa ser o grupo de recursos do cluster.

1. Dê um **Nome** e uma **Descrição** à atribuição de política que você possa usar para identificá-la facilmente.

1. Defina a [imposição de política](./assignment-structure.md#enforcement-mode) para um dos valores abaixo.

   - **Habilitado**: impõe a política no cluster. As solicitações de admissão de Kubernetes com violações são negadas.

   - **Desabilitado**: não impõe a política no cluster. As solicitações de admissão de Kubernetes com violações não são negadas. Os resultados da avaliação de conformidade ainda estão disponíveis. Quando você distribui novas definições de política aos clusters em execução, a opção _Desabilitado_ é útil para testar a definição de política, pois as solicitações de admissão com violações não são negadas.

1. Selecione **Avançar**.

1. Definir **valores de parâmetro**

   - Para excluir namespaces de Kubernetes da avaliação da política, especifique a lista de namespaces no parâmetro **Exclusões de namespace**. É recomendável excluir: _kube-system_, _gatekeeper-system_ e _azure-arc_.

1. Selecione **Examinar + criar**.

Como alternativa, use o início rápido [Atribuir uma política – Portal](../assign-policy-portal.md) para localizar e atribuir uma política do Kubernetes. Procure uma definição de política de Kubernetes em vez do exemplo “audit vms”.

> [!IMPORTANT]
> As definições de política internas estão disponíveis para clusters do Kubernetes na categoria **Kubernetes**. Para obter uma lista de definições de política internas, confira [exemplos de Kubernetes](../samples/built-in-policies.md#kubernetes).

## <a name="policy-evaluation"></a>Avaliação da política

O complemento se comunica com o serviço Azure Policy para ver se há alterações nas atribuições de política a cada 15 minutos.
Durante esse ciclo de atualização, o complemento verifica se há alterações. Essas alterações disparam criações, atualizações ou exclusões dos modelos de restrição e das restrições.

Em um cluster do Kubernetes, se um namespace tiver um dos rótulos a seguir, as solicitações de admissão com violações não serão negadas. Os resultados da avaliação de conformidade ainda estão disponíveis.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Embora um administrador do cluster possa ter permissão para criar e atualizar modelos de restrição e restrições de recursos instalados pelo complemento do Azure Policy, esses não são cenários com suporte, pois as atualizações manuais são substituídas. O Gatekeeper continua avaliando as políticas que existiam antes da instalação do complemento e da atribuição das definições de política do Azure Policy.

A cada 15 minutos, o complemento chama um exame completo do cluster. Depois de coletar detalhes do exame completo e de quaisquer avaliações em tempo real pelo Gatekeeper de tentativas de alterações no cluster, o complemento relata os resultados de volta ao Azure Policy para inclusão nos [detalhes de conformidade](../how-to/get-compliance-data.md) como qualquer atribuição do Azure Policy. Apenas os resultados de atribuições de política ativas são retornados durante o ciclo de auditoria. Os resultados da auditoria também podem ser vistos como [violações](https://github.com/open-policy-agent/gatekeeper#audit) listadas no campo de status da restrição com falha. Para obter detalhes sobre recursos _sem conformidade_ , consulte [detalhes do componente para modos de provedor de recursos](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).

> [!NOTE]
> Cada relatório de conformidade no Azure Policy de seus clusters do Kubernetes inclui todas as violações nos últimos 45 minutos. O carimbo de data/hora indica quando ocorreu uma violação.

Algumas outras considerações:

- Se a assinatura do cluster estiver registrada na central de segurança do Azure, as políticas de kubernetes da central de segurança do Azure serão aplicadas automaticamente no cluster.

- Quando uma política de negação é aplicada ao cluster com recursos de kubernetes existentes, qualquer recurso pré-existente que não esteja em conformidade com a nova política continuará a ser executado. Quando o recurso sem conformidade é reagendado em um nó diferente, o gatekeeper bloqueia a criação do recurso.

- Quando um cluster tem uma política de negação que valida os recursos, o usuário não verá uma mensagem de rejeição ao criar uma implantação. Por exemplo, considere uma implantação kubernetes que contém replicasets e pods. Quando um usuário é executado `kubectl describe deployment $MY_DEPLOYMENT` , ele não retorna uma mensagem de rejeição como parte dos eventos. No entanto, `kubectl describe replicasets.apps $MY_DEPLOYMENT` retorna os eventos associados à rejeição.

## <a name="logging"></a>Registro em log

Como um controlador/contêiner kubernetes, a _política do Azure_ e os pods de _gatekeeper_ mantêm logs no cluster kubernetes. Os logs podem ser expostos na página **Insights** do cluster do Kubernetes. Para obter mais informações, confira [Monitorar o desempenho do cluster do Kubernetes com o Azure Monitor para contêineres](../../../azure-monitor/containers/container-insights-analyze.md).

Para exibir os logs do complemento, use `kubectl`:

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Para obter mais informações, confira [Depuração do Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) na documentação do Gatekeeper.

## <a name="troubleshooting-the-add-on"></a>Solução de problemas do complemento

Para obter mais informações sobre como solucionar problemas do complemento para kubernetes, consulte a [seção kubernetes](../troubleshoot/general.md#add-on-for-kubernetes-general-errors) do artigo Azure Policy solução de problemas.

## <a name="remove-the-add-on"></a>Remover o complemento

### <a name="remove-the-add-on-from-aks"></a>Remover o complemento do AKS

Para remover o complemento do Azure Policy que está no cluster do AKS, use o portal do Azure ou a CLI do Azure:

- Portal do Azure

  1. Inicie o serviço AKS no portal do Azure selecionando **todos os serviços**, em seguida, procurando e selecionando **Serviços Kubernetess**.

  1. Selecione o cluster do AKS no qual você deseja desabilitar o complemento do Azure Policy.

  1. Selecione **políticas** no lado esquerdo da página do serviço kubernetes.

  1. Na página principal, selecione o botão **Desabilitar complemento**.

- CLI do Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Remover o complemento do Kubernetes habilitado para Azure Arc

Para remover o complemento do Azure Policy e o Gatekeeper do seu cluster do Kubernetes habilitado para Azure Arc, execute o seguinte comando Helm:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>Remover o complemento do Mecanismo do AKS

Para remover o complemento do Azure Policy e o Gatekeeper do seu cluster do Mecanismo AKS, use o método que se alinha com o modo como o complemento foi instalado:

- Se instalado através da definição da propriedade **addons** na definição de cluster para o Mecanismo AKS:

  Reimplante a definição de cluster no Mecanismo AKS depois de alterar a propriedade **addons** de _azure-policy_ para falso:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Para obter mais informações, confira [Mecanismo AKS – Desabilitar o complemento do Azure Policy](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- Se instalado com Pacotes do Helm, execute o seguinte comando Helm:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dados de diagnóstico coletados pelo complemento do Azure Policy

O complemento do Azure Policy para o Kubernetes coleta dados de diagnóstico de cluster limitados. Esses dados de diagnóstico são dados técnicos vitais relacionados ao software e ao desempenho. Eles podem ser usados das seguintes maneiras:

- Manter o complemento do Azure Policy atualizado
- Manter o complemento do Azure Policy seguro, confiável e com bom desempenho
- Melhorar o complemento do Azure Policy por meio da análise agregada do uso do complemento

As informações coletadas pelo complemento não são dados pessoais. Os detalhes a seguir são coletados no momento:

- Versão do agente do complemento do Azure Policy
- Tipo de cluster
- Região do cluster
- Grupo de recursos de cluster
- ID do recurso de cluster
- ID da assinatura de cluster
- Sistema operacional do cluster (exemplo: Linux)
- Cidade do cluster (exemplo: Seattle)
- Estado ou província do cluster (exemplo: Washington)
- País ou região do cluster (exemplo: Estados Unidos)
- Exceções/erros encontrados por complemento do Azure Policy durante a instalação do agente na avaliação da política
- Número de definições de política do Gatekeeper não instaladas pelo complemento do Azure Policy

## <a name="next-steps"></a>Próximas etapas

- Veja os exemplos em [Amostras do Azure Policy](../samples/index.md).
- Revisar a [Estrutura de definição de política](definition-structure.md).
- Revisar [Compreendendo os efeitos da política](effects.md).
- Entenda como [criar políticas de forma programática](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
