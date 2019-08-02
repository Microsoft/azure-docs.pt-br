---
title: Conceitos – Segurança no AKS (Serviços do Kubernetes do Azure)
description: Saiba mais sobre segurança no AKS (Serviço de Kubernetes do Azure), incluindo segredos do Kubernetes, políticas de rede e comunicação mestre e de nó.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 1d100f17130594ace6169f5840915c88435cb9a8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615767"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Conceitos de segurança para aplicativos e clusters no AKS (Serviço de Kubernetes do Azure)

Para proteger seus dados do cliente conforme você executa cargas de trabalho de aplicativo no AKS (Serviço de Kubernetes do Azure), a segurança do seu cluster é uma consideração importante. O Kubernetes inclui componentes de segurança, como *políticas de rede* e *Segredos*. O Azure então adiciona componentes, como grupos de segurança de rede e atualizações de cluster orquestradas. Esses componentes de segurança são combinados para manter seu cluster do AKS executando as mais recentes atualizações de segurança do sistema operacional e versões do Kubernetes e com tráfego de pod seguro e acesso a credenciais confidenciais.

Este artigo apresenta os conceitos básicos que protegem seus aplicativos no AKS:

- [Segurança de componentes mestres](#master-security)
- [Segurança do nó](#node-security)
- [Atualizações do cluster](#cluster-upgrades)
- [Segurança de rede](#network-security)
- [Segredos do Kubernetes](#kubernetes-secrets)

## <a name="master-security"></a>Segurança mestre

No AKS, os componentes mestres de Kubernetes fazem parte do serviço gerenciado fornecido pela Microsoft. Cada cluster do AKS tem o próprio mestre de Kubernetes dedicado de locatário único para fornecer o Servidor de API, o Agendador etc. Este mestre é gerenciado e mantido pela Microsoft.

Por padrão, o servidor de API do Kubernetes usa um endereço IP público e com o FQDN (nome de domínio totalmente qualificado). Você pode controlar o acesso ao Servidor de API usando os controles de acesso baseados em função do Kubernetes e do Azure Active Directory. Para obter mais informações, consulte [integração do Azure AD com o AKS][aks-aad].

## <a name="node-security"></a>Segurança do nó

Nós do AKS são máquinas virtuais do Azure que você gerenciar e manter. Executar uma distribuição otimizada do Ubuntu usando o tempo de execução do contêiner Moby nós do Linux. Nós do Windows Server (atualmente em visualização no AKS) executados um otimizado 2019 de servidor do Windows de versão e também usam o tempo de execução do contêiner Moby. Quando um cluster do AKS é criado ou dimensionado, os nós são implantados automaticamente com as configurações e atualizações de segurança do sistema operacional mais recentes.

A plataforma Azure aplica automaticamente OS patches de segurança para nós do Linux em uma base noturna. Se uma atualização de segurança do sistema operacional Linux requer uma reinicialização do host, essa reinicialização não será executada automaticamente. Você pode reinicializar os nós do Linux manualmente, ou uma abordagem comum é usar [Kured][kured] , an open-source reboot daemon for Kubernetes. Kured runs as a [DaemonSet][aks-daemonsets] e monitora cada nó para a presença de um arquivo que indica que uma reinicialização é necessária. As reinicializações são gerenciadas no cluster usando o mesmo [o processo de cordon e drenagem](#cordon-and-drain) como uma atualização do cluster.

Para nós do Windows Server (atualmente em visualização no AKS), atualização do Windows executado automaticamente e aplicar as atualizações mais recentes. Em um agendamento regular em todo o ciclo de lançamento do Windows Update e seu próprio processo de validação, você deve realizar uma atualização sobre os pools de nó do Windows Server no cluster do AKS. Este processo de atualização cria nós que executam a imagem mais recente do Windows Server e os patches, em seguida, remove os nós mais antigos. Para obter mais informações sobre esse processo, consulte [Upgrade de um pool de nós no AKS][nodepool-upgrade].

Nós são implantados em uma sub-rede de rede virtual privada, sem nenhum endereço IP público atribuído. Para fins de solução de problemas e gerenciamento, o SSH é habilitado por padrão. Esse acesso SSH só está disponível usando o endereço IP interno.

Para fornecer armazenamento, os nós usam o Azure Managed Disks. Para a maioria dos tamanhos de nó VM, esses são discos Premium apoiados por SSDs de alto desempenho. Os dados armazenados em discos gerenciados são criptografados automaticamente em repouso na plataforma Azure. Para melhorar a redundância, esses discos também são replicados com segurança no datacenter do Azure.

Os ambientes do Kubernetes, no AKS ou em outro lugar, não estão completamente seguros atualmente para uso de vários locatários hostis. Recursos de segurança adicionais, como *Políticas de Segurança Pod* ou RBAC (controles de acesso baseado em função) mais refinado para nós dificultam as explorações. No entanto, para ter uma segurança de verdade ao executar cargas de trabalho de vários locatários hostis, um hipervisor é o único nível de segurança no qual você deve confiar. O domínio de segurança para o Kubernetes se torna o cluster inteiro, não um nó individual. Para esses tipos de cargas de trabalho de vários locatários hostis, você deve usar clusters fisicamente isolados. Para obter mais informações sobre formas de isolar as cargas de trabalho, consulte [práticas recomendadas para o isolamento de cluster no AKS][cluster-isolation],

## <a name="cluster-upgrades"></a>Atualizações do cluster

Para segurança e conformidade, ou para usar os recursos mais recentes, o Azure fornece ferramentas para orquestrar a atualização de um cluster e componentes do AKS. Essa orquestração de atualização inclui os componentes de mestre e agente do Kubernetes. Você pode exibir uma [lista de versões do Kubernetes](supported-kubernetes-versions.md) disponíveis para seu cluster do AKS. Para iniciar o processo de atualização, você especificar uma dessas versões disponíveis. O Azure então realiza cordon e drenagem com segurança de cada nó do AKS e executa a atualização.

### <a name="cordon-and-drain"></a>Cordon e drenagem

Durante o processo de atualização, nós AKS individualmente são isolados do cluster para que novos pods não são agendados neles. Os nós então são drenados e atualizados da seguinte maneira:

- Um novo nó é implantado para o pool de nó. Este nó é executado, os patches e a imagem de sistema operacional mais recente.
- Um de nós existentes é identificado para atualização. Pods neste nó são normalmente encerrados e agendados nos outros nós no pool de nó.
- Esse nó existente é excluído do cluster do AKS.
- O próximo nó no cluster é isolado e esvaziados a usando o mesmo processo até que todos os nós são substituídos com êxito como parte do processo de atualização.

Para obter mais informações, consulte [atualizar um cluster AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Segurança de rede

Para conectividade e segurança com redes locais, você pode implantar um cluster do AKS em sub-redes de rede virtual do Azure existentes. Essas redes virtuais podem ter uma conexão de VPN Site a Site ou Express Route do Azure para sua rede local. Controladores de entrada de Kubernetes podem ser definidos com os endereços IP privados e internos para que os serviços estejam acessíveis somente por essa conexão de rede interna.

### <a name="azure-network-security-groups"></a>Grupos de segurança de rede do Azure

Para filtrar o fluxo de tráfego em redes virtuais, o Azure usa regras de grupo de segurança de rede. Essas regras definem o código-fonte e intervalos de IP de destino, portas e protocolos que têm o acesso a recursos permitido ou negado. Regras padrão são criadas para permitir o tráfego TLS para o servidor de API do Kubernetes. Conforme você cria serviços com balanceadores de carga, mapeamentos de porta ou rotas de entrada, o AKS modifica automaticamente o grupo de segurança de rede para o tráfego fluir de modo adequado.

## <a name="kubernetes-secrets"></a>Segredos do Kubernetes

Um *Segredo* do Kubernetes é usado para injetar dados confidenciais em pods, como chaves ou credenciais de acesso. Você primeiro criae um segredo usando a API do Kubernetes. Quando você define seu pod ou implantação, um segredo específico pode ser solicitado. Segredos são fornecidos apenas para nós que têm um pod agendado que precisa dele, e o segredo é armazenado no *tmpfs*, não gravado no disco. Quando o último pod em um nó que exige um segredo for excluído, o segredo é excluído do tmpfs do nó. Os segredos são armazenados dentro de um determinado namespace e só podem ser acessados pelo pods no mesmo namespace.

O uso de Segredos reduz as informações confidenciais definidas no manifesto YAML de pod ou serviço. Em vez disso, você pode solicitar o Segredo armazenado no servidor de API do Kubernetes como parte do seu manifesto YAML. Essa abordagem fornece apenas ao pod específico acesso ao Segredo. Observação: os arquivos de manifesto de segredo brutos contém os dados secretos no formato base64 (consulte a [documentação oficial][secret-risks] para obter mais detalhes). Portanto, esse arquivo deve ser tratado como informações confidenciais e nunca é confirmado ao controle de origem.

## <a name="next-steps"></a>Próximas etapas

Para começar a proteger os clusters AKS, consulte [atualizar um cluster AKS][aks-upgrade-cluster].

Para práticas recomendadas associadas, consulte [práticas recomendadas para segurança de cluster e as atualizações no AKS][operator-best-practices-cluster-security].

Para obter informações adicionais sobre os principais conceitos do Kubernetes e do AKS, consulte os seguintes artigos:

- [Kubernetes / clusters AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Kubernetes / identidade do AKS][aks-concepts-identity]
- [Kubernetes / redes virtuais do AKS][aks-concepts-network]
- [Kubernetes / armazenamento AKS][aks-concepts-storage]
- [Kubernetes / escala de AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
