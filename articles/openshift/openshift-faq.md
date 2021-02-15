---
title: Perguntas frequentes sobre o Azure Red Hat OpenShift
description: Aqui estão as respostas para perguntas comuns sobre Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: 69417945bcd5234a0e5e8d2d6aee42859bc95c20
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071045"
---
# <a name="azure-red-hat-openshift-faq"></a>Perguntas frequentes sobre o Azure Red Hat OpenShift

Este artigo responde às perguntas frequentes (FAQs) sobre Microsoft Azure Red Hat OpenShift.

## <a name="installation-and-upgrade"></a>Instalação e atualização

### <a name="which-azure-regions-are-supported"></a>Quais regiões do Azure têm suporte?

Para obter uma lista de regiões com suporte para o Azure Red Hat OpenShift 4. x, consulte [regiões disponíveis](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all).

Para obter uma lista de regiões com suporte para o Azure Red Hat OpenShift 3,11, consulte [produtos disponíveis por região](supported-resources.md#azure-regions).

### <a name="what-virtual-machine-sizes-can-i-use"></a>Quais tamanhos de máquina virtual posso usar?

Para obter uma lista de tamanhos de máquina virtual com suporte para o Red Hat OpenShift 4 do Azure, consulte [recursos com suporte para o Azure Red Hat OpenShift 4](support-policies-v4.md).

Para obter uma lista de tamanhos de máquina virtual com suporte para o Azure Red Hat OpenShift 3,11, consulte [recursos com suporte para o Azure Red Hat OpenShift 3,11](supported-resources.md).

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Qual é o número máximo de pods em um cluster do Azure Red Hat OpenShift?  Qual é o número máximo de pods por nó no Azure Red Hat OpenShift?

O número real de pods com suporte depende da memória, da CPU e dos requisitos de armazenamento de um aplicativo.

O Azure Red Hat OpenShift 4. x tem um limite de 250 Pod por nó e um limite de nó de computação de 100. Isso limita o número máximo de pods com suporte em um cluster para 250 &times; 100 = 25.000.

O Azure Red Hat OpenShift 3,11 tem um limite de 50 Pod por nó e um limite de 20 nós de computação. Isso limita o número máximo de pods com suporte em um cluster para 50 &times; 20 = 1.000.

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Um cluster pode ter nós de computação em várias regiões do Azure?

Não. Todos os nós em um cluster do Azure Red Hat OpenShift devem se originar na mesma região do Azure.

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>Um cluster pode ser implantado em várias zonas de disponibilidade?

Sim. Isso ocorrerá automaticamente se o cluster for implantado em uma região do Azure que dá suporte a zonas de disponibilidade. Para obter mais informações, confira [Zonas de disponibilidade](../availability-zones/az-overview.md#availability-zones).

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Os nós de plano de controle são abstratos como estão com o AKS (serviço kubernetes do Azure)?

Não. Todos os recursos, incluindo os nós mestre de cluster, são executados na sua assinatura de cliente. Esses tipos de recursos são colocados em um grupo de recursos somente leitura.

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>O cluster reside em uma assinatura de cliente? 

O aplicativo gerenciado do Azure reside em um grupo de recursos bloqueado com a assinatura do cliente. Os clientes podem exibir objetos nesse grupo de recursos, mas não modificá-los.

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>Existe algum elemento no Azure Red Hat OpenShift compartilhado com outros clientes? Ou é independente de tudo?

Cada cluster do Azure Red Hat OpenShift é dedicado a um determinado cliente e reside na assinatura do cliente. 

### <a name="are-infrastructure-nodes-available"></a>Os nós de infraestrutura estão disponíveis?

Nos clusters do Azure Red Hat OpenShift 4. x, os nós de infraestrutura não estão disponíveis no momento.

Nos clusters do Azure Red Hat OpenShift 3,11, os nós de infraestrutura são incluídos por padrão.

## <a name="how-do-i-handle-cluster-upgrades"></a>Como fazer lidar com atualizações de cluster?

Para obter informações sobre atualizações, manutenção e versões com suporte, consulte o [Guia do ciclo de vida de suporte](support-lifecycle.md).

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>Como o sistema operacional host e o software OpenShift serão atualizados?

Os sistemas operacionais de host e o software OpenShift são atualizados conforme o Azure Red Hat OpenShift consome versões de lançamento secundárias e patches da plataforma de contêiner upstream OpenShift.

### <a name="whats-the-process-to-reboot-the-updated-node"></a>Qual é o processo para reinicializar o nó atualizado?

Os nós são reinicializados como parte de uma atualização.

## <a name="cluster-operations"></a>Operações de cluster

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>Posso usar o Prometheus para monitorar meus aplicativos?

O Prometheus vem pré-instalado e configurado para os clusters do Azure Red Hat OpenShift 4. x. Leia mais sobre o [monitoramento de cluster](https://docs.openshift.com/container-platform/4.6/operators/operator_sdk/osdk-monitoring-prometheus.html).

Para os clusters do Azure Red Hat OpenShift 3,11, você pode implantar o Prometheus em seu namespace e monitorar aplicativos em seu namespace. Para obter mais informações, consulte [implantar instância de Prometheus no cluster do Azure Red Hat OpenShift](howto-deploy-prometheus.md).

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Posso usar o Prometheus para monitorar as métricas relacionadas à integridade e à capacidade do cluster?

No Azure Red Hat OpenShift 4. x: Sim.

No Azure Red Hat OpenShift 3,11: não.

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Os logs de VMs subjacentes podem ser transmitidos para um sistema de análise de log do cliente?

Os logs de VMs subjacentes são tratados pelo serviço gerenciado e não são expostos aos clientes.

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>Como um cliente pode obter acesso a métricas como CPU/memória no nível do nó para tomar medidas para dimensionar, depurar problemas, etc.? Não consigo executar o kubectl Top em um cluster do Azure Red Hat OpenShift.

Para os clusters do Azure Red Hat OpenShift 4. x, o console Web do OpenShift contém todas as métricas no nível do nó. Para obter mais informações, consulte a documentação do Red Hat sobre como [Exibir informações de cluster](https://docs.openshift.com/container-platform/4.6/web_console/using-dashboard-to-get-cluster-information.html).

Para os clusters do Azure Red Hat OpenShift 3,11, os clientes podem acessar as métricas de CPU/memória no nível do nó usando o comando `oc adm top nodes` ou `kubectl top nodes` com a função de cluster de administrador do cliente. Os clientes também podem acessar as métricas de CPU/memória do `pods` com o comando `oc adm top pods` ou `kubectl top pods` .

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Se expandirmos a implantação, como os domínios de falha do Azure serão mapeados no posicionamento pod para garantir que todos os pods de um serviço não sejam vazados por uma falha em um único domínio de falha?

Há, por padrão, cinco domínios de falha ao usar conjuntos de dimensionamento de máquinas virtuais no Azure. Cada instância de máquina virtual em um conjunto de dimensionamento será colocada em um desses domínios de falha. Isso garante que os aplicativos implantados nos nós de computação em um cluster sejam colocados em domínios de falha separados.

Para obter mais informações, consulte [escolhendo o número correto de domínios de falha para o conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md).

### <a name="is-there-a-way-to-manage-pod-placement"></a>Há uma maneira de gerenciar o posicionamento do pod?

Os clientes têm a capacidade de obter nós e exibir rótulos como administrador do cliente. Isso fornecerá uma maneira de direcionar qualquer VM no conjunto de dimensionamento.

Cuidado deve ser usado ao usar rótulos específicos:

- O nome do host não deve ser usado. O nome do host é girado frequentemente com atualizações e atualizações e tem a garantia de alteração.
- Se o cliente tiver uma solicitação de rótulos específicos ou uma estratégia de implantação, isso poderá ser realizado, mas exigiria esforços de engenharia e não terá suporte atualmente.

Para obter mais informações, consulte [controlando o posicionamento do pod](https://docs.openshift.com/container-platform/4.6/nodes/scheduling/nodes-scheduler-about.html).

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>O registro de imagem está disponível externamente para que eu possa usar ferramentas como Jenkins?

Para clusters 4. x, você precisa expor um registro seguro e configurar a autenticação. Para obter mais informações, consulte a seguinte documentação do Red Hat:

- [Expondo um registro](https://docs.openshift.com/container-platform/4.6/registry/securing-exposing-registry.html)
- [Acessando o registro](https://docs.openshift.com/container-platform/4.6/registry/accessing-the-registry.html)

Para clusters 3,11, o registro de imagem do Docker está disponível. O registro do Docker está disponível no `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` . Você também pode usar o registro de contêiner do Azure.

## <a name="networking"></a>Rede

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Posso implantar um cluster em uma rede virtual existente?

Em clusters 4. x, você pode implantar um cluster em uma VNet existente.

Em clusters 3,11, não é possível implantar um cluster em uma VNet existente. Você pode conectar um cluster do Azure Red Hat OpenShift 3,11 a uma VNet existente por meio de emparelhamento.

### <a name="is-cross-namespace-networking-supported"></a>Há suporte para a rede de namespace cruzado?

Os administradores de projetos individuais e de clientes podem personalizar a rede de namespace cruzado (incluindo sua negação) em uma base por projeto usando `NetworkPolicy` objetos.

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Estou tentando emparelhar em uma rede virtual em uma assinatura diferente, mas falha ao obter o erro de CIDR VNet.

Na assinatura que tem a rede virtual, certifique-se de registrar `Microsoft.ContainerService` o provedor com o seguinte comando: `az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Podemos especificar intervalos de IP para implantação na VNet privada, evitando conflitos com outros VNets corporativos uma vez emparelhados?

Em clusters 4. x, você pode especificar seus próprios intervalos de IP.

Em clusters 3,11, o Red Hat OpenShift do Azure dá suporte ao emparelhamento VNet e permite que o cliente forneça uma VNet para emparelhar com o e um CIDR VNet no qual a rede OpenShift funcionará.

A VNet criada pelo Azure Red Hat OpenShift será protegida e não aceitará alterações de configuração. A VNet emparelhada é controlada pelo cliente e reside em sua assinatura.

### <a name="is-the-software-defined-network-module-configurable"></a>O módulo de rede definido pelo software é configurável?

A rede definida pelo software é `openshift-ovs-networkpolicy` e não é configurável.

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Qual Azure Load Balancer é usado pelo Azure Red Hat OpenShift?  É padrão ou básico e é configurável?

O Azure Red Hat OpenShift usa Azure Load Balancer padrão e não é configurável.

## <a name="permissions"></a>Permissões

### <a name="can-an-admin-manage-users-and-quotas"></a>Um administrador pode gerenciar usuários e cotas?

Sim. Um administrador do Azure Red Hat OpenShift pode gerenciar usuários e cotas, além de acessar todos os projetos criados pelo usuário.

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Posso restringir um cluster a apenas determinados usuários do AD do Azure?

Sim. Você pode restringir quais usuários do Azure AD podem entrar em um cluster Configurando o aplicativo do Azure AD. Para obter detalhes, consulte [como: restringir seu aplicativo a um conjunto de usuários](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md).

### <a name="can-i-restrict-users-from-creating-projects"></a>Posso impedir que os usuários criem projetos?

Sim. Faça logon no cluster como administrador e execute este comando:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Para obter mais informações, consulte a documentação do OpenShift sobre como desabilitar o autoprovisionamento para a versão do cluster:

- [Desabilitando o autoprovisionamento em clusters 4,6](https://docs.openshift.com/container-platform/4.6/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [Desabilitando o autoprovisionamento em clusters 3,11](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Quais direitos do UNIX (no IaaS) estão disponíveis para nós mestres/de infraestrutura/aplicativo?

Para clusters 4. x, o acesso ao nó está disponível por meio da função de administrador de cluster. Para obter mais informações, consulte [visão geral do RBAC kubernetes](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html).

Para clusters 3,11, o acesso ao nó é proibido.

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Quais direitos de OCP temos? Cluster-administrador? Projeto-administrador?

Para clusters 4. x, a função de administrador de cluster está disponível. Para obter mais informações, consulte [visão geral do RBAC kubernetes](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html).

Para clusters 3,11, consulte [visão geral de administração de cluster](https://docs.openshift.com/aro/admin_guide/index.html) para obter mais detalhes.

### <a name="which-identity-providers-are-available"></a>Quais provedores de identidade estão disponíveis?

Para clusters 4. x, você configura seu próprio provedor de identidade. Para obter mais informações, consulte a documentação do Red Hat sobre como [Configurar provedores de identidade](https://docs.openshift.com/container-platform/4.6/authentication/identity_providers/configuring-ldap-identity-provider.html).

Para clusters 3,11, você pode usar a integração do Azure AD. 

## <a name="storage"></a>Armazenamento

### <a name="is-data-on-my-cluster-encrypted"></a>Os dados estão criptografados no meu cluster?

Por padrão, os dados são criptografados em repouso. A plataforma de armazenamento do Azure criptografa automaticamente os dados antes de mantê-los e descriptografa os dados antes da recuperação. Para obter mais informações, consulte [criptografia do serviço de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>Os dados são armazenados no etcd criptografados no Azure Red Hat OpenShift?

Para os clusters do Azure Red Hat OpenShift 4, os dados não são criptografados por padrão, mas você tem a opção de habilitar a criptografia. Para obter mais informações, consulte o guia sobre como [criptografar etcd](https://docs.openshift.com/container-platform/4.6/security/encrypting-etcd.html).

Para clusters 3,11, os dados não são criptografados no nível de etcd. Atualmente, não há suporte para a opção de ativar a criptografia no. O OpenShift dá suporte a esse recurso, mas os esforços de engenharia são necessários para fazê-lo no mapa de estrada. Os dados são criptografados no nível do disco. Para obter mais informações [, consulte Criptografando dados na camada de armazenamento de datastore](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) .

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Podemos escolher qualquer solução de armazenamento persistente, como o OCS? 

Para clusters 4. x, o disco do Azure (Premium_LRS) é configurado como a classe de armazenamento padrão. Para provedores de armazenamento adicionais e para obter detalhes de configuração (incluindo o arquivo do Azure), consulte a documentação do Red Hat no [armazenamento persistente](https://docs.openshift.com/container-platform/4.6/storage/understanding-persistent-storage.html).

Para clusters 3,11, duas classes de armazenamento são fornecidas por padrão: uma para o disco do Azure (Premium_LRS) e outra para o arquivo do Azure.

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>A toa armazena dados de clientes fora da região do cluster?

Não. Todos os dados criados em um cluster de toa são mantidos na região do cluster.
