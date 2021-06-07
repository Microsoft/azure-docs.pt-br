---
title: Introdução ao Serviço de Kubernetes do Azure
description: Aprenda os recursos e benefícios do Serviço de Kubernetes do Azure para implantar e gerenciar aplicativos baseados em contêiner no Azure.
services: container-service
ms.topic: overview
ms.date: 02/24/2021
ms.custom: mvc
ms.openlocfilehash: 1cddd39d0b95e021478235fcdafbacd40eb4097c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105248"
---
# <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

O AKS (Serviço de Kubernetes do Azure) simplifica a implantação de um cluster do Kubernetes gerenciado no Azure, transferindo a sobrecarga operacional para o Azure. Como um serviço Kubernetes hospedado, o Azure lida com as tarefas críticas, como o monitoramento da integridade e a manutenção. Como os mestres do Kubernetes são gerenciados pelo Azure, você só gerencia e mantém os nós de agente. Além disso, o AKS é gratuito; você paga apenas pelos nós de agente dentro dos clusters, não pelos mestres.  

Você pode criar um cluster do AKS usando:
* [A CLI do Azure](kubernetes-walkthrough.md)
* [O portal do Azure](kubernetes-walkthrough-portal.md)
* [Azure PowerShell](kubernetes-walkthrough-powershell.md)
* Usando opções de implantação controladas por modelos, como [modelos de Azure Resource Manager](kubernetes-walkthrough-rm-template.md) e Terraform 

Ao implantar um cluster do AKS, o mestre do Kubernetes e todos os nós serão implantados e configurados para você. A rede avançada, o monitoramento, a integração do Azure AD (Azure Active Directory) e outros recursos podem ser configurados durante o processo de implantação. 

Para obter mais informações sobre os conceitos básicos do Kubernetes, confira [Conceitos básicos do Kubernetes para o AKS][concepts-clusters-workloads].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]
> O AKS também dá suporte a contêineres do Windows Server.

## <a name="access-security-and-monitoring"></a>Acesso, segurança e monitoramento

Para segurança e gerenciamento aprimorados, o AKS permite fazer a integração ao Azure AD para:
* Usar o RBAC (controle de acesso baseado em função) do Kubernetes. 
* Monitorar a integridade do cluster e dos recursos.

### <a name="identity-and-security-management"></a>Gerenciamento de segurança e identidade

#### <a name="kubernetes-rbac"></a>RBAC do Kubernetes

Para limitar o acesso aos recursos de cluster, o AKS dá suporte ao [RBAC do Kubernetes][kubernetes-rbac]. O RBAC do Kubernetes controla o acesso e as permissões para recursos e namespaces do Kubernetes.  

#### <a name="azure-ad"></a>Azure AD

Você pode configurar um cluster do AKS para se integrar ao Azure AD. Com a integração do Azure AD, o acesso do Kubernetes pode ser configurado com base em identidade e associação de grupo existentes. Seus usuários e grupos existentes do Azure AD podem receber uma experiência de logon integrada e acesso a recursos do AKS.  

Para obter mais informações sobre identidade, confira [Opções de acesso e identidade do AKS][concepts-identity].

Para proteger seus clusters do AKS, confira [Integrar o Azure Active Directory ao AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Monitoramento e registro em log integrados

O Azure Monitor para a Integridade do Contêiner coleta métricas de desempenho de processador e memória de contêineres, nós e controladores no cluster do AKS e nos aplicativos implantados. Você pode examinar os logs de contêiner e [os logs mestre do Kubernetes][aks-master-logs], que ficam:
* Armazenados um workspace do Azure Log Analytics.
* Disponíveis por meio do portal do Azure, da CLI do Azure ou de um ponto de extremidade REST.

Para obter mais informações, confira [Monitorar a integridade de contêiner do Serviço de Kubernetes do Azure][container-health].

## <a name="clusters-and-nodes"></a>Clusters e nós

Os nós do AKS são executados em VMs (máquinas virtuais) do Azure. Com os nós do AKS, você pode conectar o armazenamento a nós e pods, atualizar componentes do cluster e usar GPUs. O AKS dá suporte a clusters de Kubernetes que executam vários pools de nós para dar suporte a sistemas operacionais mistos e contêineres do Windows Server.  

Para obter mais informações sobre as funcionalidades de nó, pool de nós e cluster do Kubernetes, confira [Conceitos principais do Kubernetes para o AKS][concepts-clusters-workloads].

### <a name="cluster-node-and-pod-scaling"></a>Nó de cluster e dimensionamento de pod

Conforme a demanda por recursos vai mudando, o número de nós de cluster ou pods que executam os serviços aumenta ou diminui automaticamente. Você pode ajustar tanto o dimensionamento horizontal automático do pod quanto o dimensionamento automático do cluster para se adequar às demandas e executar somente os recursos necessários.

Para obter mais informações, confira [Dimensionar um cluster do AKS (Serviço de Kubernetes do Azure)][aks-scale].

### <a name="cluster-node-upgrades"></a>Atualizações de nó de cluster

O AKS oferece várias versões do Kubernetes. Conforme novas versões ficam disponíveis no AKS, o cluster pode ser atualizado pelo portal do Azure ou pela CLI do Azure. Durante o processo de atualização, os nós são cuidadosamente isolados e esvaziados para minimizar as interrupções nos aplicativos em execução.  

Para saber mais sobre as versões do ciclo de vida, confira [Versões do Kubernetes compatíveis com o AKS][aks-supported versions]. Para obter etapas sobre como atualizar, confira [Atualizar um cluster do AKS (Serviço de Kubernetes do Azure)][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Nós habilitados para GPU

O AKS dá suporte à criação de pools de nó habilitados para GPU. No momento, o Azure fornece uma ou várias VMs habilitadas para GPU. As VMs habilitadas para GPU são projetadas para cargas de trabalho de visualização e com muita computação e muitos gráficos.

Para obter mais informações, confira [Como usar GPUs no AKS][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Nós de computação confidencial (versão prévia pública)

O AKS dá suporte à criação de pools de nós de computação confidencial baseados no Intel SGX (VMs DCSv2). Os nós de computação confidencial permitem que os contêineres sejam executados em um Ambiente de Execução Confiável baseado em hardware (enclaves). O isolamento entre contêineres, combinados com a integridade de código por meio de atestado, pode ajudar com sua estratégia de segurança de contêiner de defesa em profundidade. Os nós de computação confidencial dão suporte a contêineres confidenciais (aplicativos existentes do Docker) e contêineres com reconhecimento de enclave.

Para obter mais informações, confira [Nós de computação confidencial no AKS][conf-com-node].

### <a name="storage-volume-support"></a>Suporte a volumes de armazenamento

Para dar suporte a cargas de trabalho do aplicativo, você pode montar volumes de armazenamento estáticos ou dinâmicos para dados persistentes. Dependendo de quantos pods conectados você espera que compartilhem o armazenamento, é possível usar o armazenamento com uma das seguintes opções:
* Discos do Azure para acesso de pod único; 
* Arquivos do Azure para acesso de vários pods simultâneos.

Para obter mais informações, confira [Opções de armazenamento para aplicativos no AKS][concepts-storage].

Comece com volumes dinâmicos e persistentes usando [Discos do Azure][azure-disk] ou [Arquivos do Azure][azure-files].

## <a name="virtual-networks-and-ingress"></a>Entrada e redes virtuais

Um cluster do AKS pode ser implantado em uma rede virtual existente. Nessa configuração, cada pod no cluster é atribuído um endereço IP na rede virtual e pode se comunicar diretamente com:
* outros pods no cluster; 
* outros nós na rede virtual. 

Os pods também podem se conectar a outros serviços em uma rede virtual emparelhada e a redes locais com conexões ExpressRoute ou VPN S2S (site a site).  

Para obter mais informações, confira [Conceitos de rede para aplicativos no AKS][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Entrada com roteamento de aplicativo HTTP

O complemento de roteamento de aplicativo HTTP ajuda você a acessar com facilidade aplicativos implantados no cluster do AKS. Quando habilitada, a solução de roteamento do aplicativo HTTP configura um controlador de entrada em seu cluster do AKS.  

Quando os aplicativos são implantados, os nomes DNS publicamente acessíveis são configurados automaticamente. O roteamento de aplicativo HTTP configura uma zona DNS e a integra ao cluster do AKS. Em seguida, você pode implantar os recursos de entrada do Kubernetes como de costume.  

Para começar a usar o tráfego de entrada, confira [Roteamento de aplicativo HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Integração de ferramentas de desenvolvimento

O Kubernetes tem um ecossistema rico de ferramentas de desenvolvimento e gerenciamento que funcionam perfeitamente com o AKS. Essas ferramentas incluem o Helm e a extensão do Kubernetes para Visual Studio Code.   

O Azure fornece várias ferramentas que ajudam a simplificar o Kubernetes, como o Azure Dev Spaces e o DevOps Starter.  

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

O Azure Dev Spaces fornece uma experiência de desenvolvimento Kubernetes rápida e iterativa para equipes. Com o mínimo de configuração, você pode executar e depurar contêineres diretamente no AKS. Para começar, confira [Azure Dev Spaces][azure-dev-spaces].

### <a name="devops-starter"></a>DevOps Starter

O DevOps Starter fornece uma solução simples para trazer o código existente e o repositório Git para o Azure. O DevOps Starter realiza as seguintes tarefas automaticamente:
* cria recursos do Azure (como o AKS); 
* configura um pipeline de lançamento no Azure DevOps Services que inclui um pipeline de build de integração contínua; 
* configura um pipeline de lançamento de CD e 
* gera um recurso do Azure Application Insights para monitoramento. 

Para obter mais informações, confira [DevOps Starter][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Suporte de imagem do Docker e registro de contêiner privado

O AKS dá suporte ao formato de imagem do Docker. Para ter armazenamento particular de suas imagens do Docker, integre o AKS ao ACR (Registro de Contêiner do Azure).

Para criar um repositório de imagens privado, confira [Registro de Contêiner do Azure][acr-docs].

## <a name="kubernetes-certification"></a>Certificação Kubernetes

O AKS foi certificado pela CNCF como em conformidade com o Kubernetes.

## <a name="regulatory-compliance"></a>Conformidade normativa

O AKS está em conformidade com SOC, ISO, PCI DSS e HIPAA. Para obter mais informações, confira [Visão geral da conformidade do Microsoft Azure][compliance-doc].

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como implantar e gerenciar o AKS com o Início Rápido da CLI do Azure.

> [!div class="nextstepaction"]
> [Implantar um cluster do AKS usando a CLI do Azure][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://azure.microsoft.com/en-us/overview/trusted-cloud/compliance/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: ../dev-spaces/index.yml
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/containers/container-insights-overview.md
[aks-master-logs]: ./view-control-plane-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
