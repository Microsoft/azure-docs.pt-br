---
title: Introdução ao Serviço de Kubernetes do Azure
description: Aprenda os recursos e benefícios do Serviço de Kubernetes do Azure para implantar e gerenciar aplicativos baseados em contêiner no Azure.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: overview
ms.date: 05/06/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 5515951a9bde596935f4c603ffd9e088f74dee45
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615159"
---
# <a name="azure-kubernetes-service-aks"></a>AKS (Serviço de Kubernetes do Azure) 

O AKS (Serviço de Kubernetes do Azure) simplifica a implantação de um cluster do Kubernetes gerenciado no Azure. O AKS reduz a complexidade e a sobrecarga operacional de gerenciar o Kubernetes passando grande parte dessa responsabilidade para o Azure. Como um serviço Kubernetes hospedado, o Azure lida com as tarefas críticas para você, como o monitoramento da integridade e a manutenção. Os mestres de Kubernetes são gerenciados pelo Azure. Você gerencia e mantém apenas os nós de agente. Como um serviço do Kubernetes gerenciado, o AKS é gratuito: você paga apenas pelos nós de agentes dentro dos clusters, não pelos mestres.

Você pode criar um cluster do AKS no portal do Azure, com a CLI do Azure, ou opções de implantação controladas pelo modelo, como modelos do Resource Manager e o Terraform. Ao implantar um cluster do AKS, o mestre do Kubernetes e todos os nós serão implantados e configurados para você. Recursos adicionais, como rede avançada, integração do Azure Active Directory e monitoramento, também podem ser configurados durante o processo de implantação. O suporte para contêineres do Windows Server no AKS está em versão prévia.

Para obter mais informações sobre os conceitos básicos do Kubernetes, confira [Conceitos básicos do Kubernetes para o AKS][concepts-clusters-workloads].

Para começar, conclua o início rápido do AKS [no portal do Azure][aks-portal] or [with the Azure CLI][aks-cli].

## <a name="access-security-and-monitoring"></a>Acesso, segurança e monitoramento

Para maior segurança e gerenciamento, o AKS permite fazer a integração com o Azure Active Directory e usar os controles de acesso baseado em função do Kubernetes. Você também pode monitorar a integridade do cluster e dos recursos.

### <a name="identity-and-security-management"></a>Gerenciamento de segurança e identidade

Para limitar o acesso aos recursos de cluster, o AKS dá suporte ao [RBAC (controle de acesso baseado em função) do Kubernetes][kubernetes-rbac]. O RBAC permite que você controle o acesso a namespaces e recursos do Kubernetes e as permissões para esses recursos. Você também pode configurar um cluster do AKS para se integrar ao Azure AD (Active Directory). Com a integração do Azure AD, o acesso do Kubernetes pode ser configurado com base em identidade e associação de grupo existentes. Seus usuários e grupos existentes do Azure AD podem receber acesso a recursos do AKS e com uma experiência de logon integrada.

Para obter mais informações sobre identidade, confira [Opções de acesso e identidade do AKS][concepts-identity].

Para proteger seus clusters do AKS, confira [Integrar o Azure Active Directory ao AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Monitoramento e registro em log integrados

Para entender como o cluster do AKS e os aplicativos implantados estão se saindo, o Azure Monitor para a integridade do contêiner coleta métricas de processador e memória de contêineres, nós e controladores. Há logs do contêiner disponíveis e você também pode [examinar os logs mestre do Kubernetes][aks-master-logs]. Esses dados de monitoramento são armazenados em um espaço de trabalho do Log Analytics e ficam disponíveis por meio do portal do Azure, da CLI do Azure ou de um ponto de extremidade REST.

Para obter mais informações, confira [Monitorar a integridade de contêiner do Serviço de Kubernetes do Azure][container-health].

## <a name="clusters-and-nodes"></a>Clusters e nós

Os nós do AKS são executados em máquinas virtuais do Azure. Você pode conectar o armazenamento a nós e pods, atualizar componentes do cluster e usar GPUs. O AKS dá suporte a clusters de Kubernetes que executam vários pools de nós para dar suporte a sistemas operacionais mistos e contêineres do Windows Server (atualmente em versão prévia). Nós Linux executam uma imagem personalizada do sistema operacional do Ubuntu e nós do Windows Server executam uma imagem personalizada do sistema operacional Windows Server 2019.

### <a name="cluster-node-and-pod-scaling"></a>Nó de cluster e dimensionamento de pod

Conforme a demanda por recursos vai mudando, o número de nós de cluster ou pods que executam os serviços pode ser aumentado ou reduzido automaticamente. Você pode usar tanto o dimensionador automático de cluster quanto o dimensionador automático de pod horizontal. Essa abordagem de dimensionamento permite que o cluster do AKS se ajuste automaticamente a demandas e execute apenas os recursos necessários.

Para obter mais informações, confira [Dimensionar um cluster do AKS (Serviço de Kubernetes do Azure)][aks-scale].

### <a name="cluster-node-upgrades"></a>Atualizações de nó de cluster

O Serviço de Kubernetes do Azure oferece várias versões de Kubernetes. Conforme novas versões ficam disponíveis no AKS, o cluster pode ser atualizado pelo Portal do Azure ou pela CLI do Azure. Durante o processo de atualização, os nós são cuidadosamente isolados e esvaziados para minimizar as interrupções nos aplicativos em execução.

Para saber mais sobre as versões do ciclo de vida, confira [Versões do Kubernetes compatíveis com o AKS][aks-supported versions]. For steps on how to upgrade, see [Upgrade an Azure Kubernetes Service (AKS) cluster][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Nós habilitados para GPU

AKS oferece suporte à criação de pools de nó habilitados para GPU. Atualmente, o Azure fornece uma ou várias VMs habilitasdas para GPU. As VMs habilitadas para GPU são projetadas para cargas de trabalho de visualização e com muita computação e muitos gráficos.

Para obter mais informações, confira [Como usar GPUs no AKS][aks-gpu].

### <a name="storage-volume-support"></a>Suporte a volumes de armazenamento

Para dar suporte a cargas de trabalho do aplicativo, você pode montar volumes de armazenamento para dados persistentes. Tanto volumes estáticos quanto dinâmicos podem ser usados. Dependendo de quantos pods conectados vão compartilhar o armazenamento, você pode usar o armazenamento com o apoio dos Discos do Azure para o acesso de um único pod ou os Arquivos do Azure para o acesso de vários pods simultaneamente.

Para obter mais informações, confira [Opções de armazenamento para aplicativos no AKS][concepts-storage].

Comece com volumes dinâmicos persistentes usando [Discos do Azure][azure-disk] or [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Entrada e redes virtuais

Um cluster do AKS pode ser implantado em uma rede virtual existente. Nessa configuração, cada pod do cluster é atribuído a um endereço IP na rede virtual e pode se comunicar diretamente com outros pods no cluster e com outros nós na rede virtual. Um pod também pode se conectar a outros serviços em uma rede virtual pareada e com redes locais, em conexões VPN site a site (S2S) ou do ExpressRoute.

Para obter mais informações, confira [Conceitos de rede para aplicativos no AKS][aks-networking].

Para começar a usar o tráfego de entrada, confira [Roteamento de aplicativo HTTP][aks-http-routing].

### <a name="ingress-with-http-application-routing"></a>Entrada com roteamento de aplicativo HTTP

O complemento de roteamento de aplicativo HTTP facilita o acesso a aplicativos implantados no cluster do AKS. Quando habilitada, a solução de roteamento do aplicativo HTTP configura um controlador de entrada em seu cluster do AKS. Quando os aplicativos são implantados, os nomes DNS publicamente acessíveis são configurados automaticamente. O roteamento de aplicativo HTTP configura uma zona DNS e a integra ao cluster do AKS. Em seguida, você pode implantar os recursos de entrada do Kubernetes como de costume.

Para começar a usar o tráfego de entrada, confira [Roteamento de aplicativo HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Integração de ferramentas de desenvolvimento

O Kubernetes tem um amplo ecossistema de ferramentas de desenvolvimento e gerenciamento, como o Helm, o Rascunho e a extensão do Kubernetes para o Visual Studio Code. Essas ferramentas funcionam perfeitamente com o AKS.

Além disso, o Azure Dev Spaces fornece uma experiência de desenvolvimento Kubernetes rápida e iterativa para equipes. Com o mínimo de configuração, você pode executar e depurar contêineres diretamente no AKS. Para começar, confira [Azure Dev Spaces][azure-dev-spaces].

O projeto de Azure DevOps fornece uma solução simples para trazer o código existente e o repositório Git para o Azure. O Projeto do DevOps cria automaticamente recursos do Azure, como o AKS, um pipeline de versão no Azure DevOps Services que inclui pipelines de build para CI, configura um pipeline de lançamento para CD e cria um recurso do Azure Application Insights para monitoramento.

Para obter mais informações, confira [Projeto do Azure DevOps][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Suporte de imagem do Docker e registro de contêiner privado

O AKS dá suporte ao formato de imagem do Docker. Para ter armazenamento particular de suas imagens do Docker, integre o AKS ao ACR (Registro de Contêiner do Azure).

Para criar o repositório de imagens particular, confira [Registro de Contêiner do Azure][acr-docs].

## <a name="kubernetes-certification"></a>Certificação Kubernetes

O AKS (Serviço de Kubernetes do Azure) foi certificado pela CNCF como compatível com o Kubernetes.

## <a name="regulatory-compliance"></a>Conformidade normativa

O AKS (Serviço de Kubernetes do Azure) está em conformidade com SOC, ISO, PCI DSS e HIPAA. Para obter mais informações, confira [Visão geral da conformidade do Microsoft Azure][compliance-doc].

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como implantar e gerenciar o AKS com os tutoriais de início rápido da CLI do Azure.

> [!div class="nextstepaction"]
> [Início rápido do AKS][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
