---
title: Conceitos – Segurança no AKS (Serviços do Kubernetes do Azure)
description: Saiba mais sobre segurança no AKS (Serviço de Kubernetes do Azure), incluindo segredos do Kubernetes, políticas de rede e comunicação mestre e de nó.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: mlearned
ms.openlocfilehash: 6c69e46ea3510476089cd932b1cd1bdf14254021
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122367"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Conceitos de segurança para aplicativos e clusters no AKS (Serviço de Kubernetes do Azure)

Para proteger seus dados do cliente conforme você executa cargas de trabalho de aplicativo no AKS (Serviço de Kubernetes do Azure), a segurança do seu cluster é uma consideração importante. O Kubernetes inclui componentes de segurança, como *políticas de rede* e *Segredos*. O Azure então adiciona componentes, como grupos de segurança de rede e atualizações de cluster orquestradas. Esses componentes de segurança são combinados para manter seu cluster do AKS executando as mais recentes atualizações de segurança do sistema operacional e versões do Kubernetes e com tráfego de pod seguro e acesso a credenciais confidenciais.

Este artigo apresenta os conceitos básicos que protegem seus aplicativos no AKS:

- [Conceitos de segurança para aplicativos e clusters no AKS (Serviço de Kubernetes do Azure)](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Segurança mestre](#master-security)
  - [Segurança do nó](#node-security)
    - [Isolamento de computação](#compute-isolation)
  - [Atualizações do cluster](#cluster-upgrades)
    - [Cordon e drenagem](#cordon-and-drain)
  - [Segurança de rede](#network-security)
    - [Grupos de segurança de rede do Azure](#azure-network-security-groups)
  - [Segredos do Kubernetes](#kubernetes-secrets)
  - [Próximas etapas](#next-steps)

## <a name="master-security"></a>Segurança mestre

No AKS, os componentes mestres de Kubernetes fazem parte do serviço gerenciado fornecido pela Microsoft. Cada cluster do AKS tem o próprio mestre de Kubernetes dedicado de locatário único para fornecer o Servidor de API, o Agendador etc. Esse mestre é gerenciado e mantido pela Microsoft.

Por padrão, o servidor de API do Kubernetes usa um endereço IP público e um FQDN (nome de domínio totalmente qualificado). Você pode limitar o acesso ao ponto de extremidade do servidor da API usando [intervalos de IP autorizados][authorized-ip-ranges]. Você também pode criar um [cluster particular][private-clusters] para limitar o acesso do servidor de API à sua rede virtual.

Você pode controlar o acesso ao servidor de API usando o controle de acesso baseado em função do kubernetes (kubernetes RBAC) e o RBAC do Azure. Para obter mais informações, confira [Integração do Azure AD com o AKS][aks-aad].

## <a name="node-security"></a>Segurança do nó

Nós do AKS são máquinas virtuais do Azure que você gerenciar e manter. Nós do Linux executam uma distribuição otimizada do Ubuntu usando o `containerd` tempo de execução do contêiner do ou do Moby. Os nós do Windows Server executam uma versão otimizada do Windows Server 2019 e também usam o `containerd` tempo de execução do contêiner do ou do Moby. Quando um cluster do AKS é criado ou dimensionado, os nós são implantados automaticamente com as configurações e atualizações de segurança do sistema operacional mais recentes.

> [!NOTE]
> Clusters AKS usando pools de nós do kubernetes versão 1,19 e maior uso `containerd` como seu tempo de execução de contêiner. Os clusters AKS usando kubernetes antes do v 1.19 para pools de nós usam [Moby](https://mobyproject.org/) (upstream Docker) como seu tempo de execução de contêiner.

A plataforma Azure aplica automaticamente patches de segurança do sistema operacional aos nós do Linux todas as noites. Se uma atualização de segurança do sistema operacional Linux exigir uma reinicialização do host, essa reinicialização não será executada automaticamente. Você pode reinicializar os nós do Linux manualmente ou pode usar [Kured][kured], um daemon de reinicialização open-source para Kubernetes. O Kured é executado como um [DaemonSet][aks-daemonsets] e monitora cada nó para a presença de um arquivo que indica que uma reinicialização é necessária. As reinicializações são gerenciadas no cluster usando o mesmo [o processo de cordon e drenagem](#cordon-and-drain) como uma atualização do cluster.

Para os nós do Windows Server, o Windows Update não executa automaticamente e aplica as atualizações mais recentes. Regularmente, de acordo com o ciclo de versão do Windows Update e seu próprio processo de validação, você deve executar uma atualização nos pools de nó do Windows Server do seu cluster do AKS. Esse processo de atualização cria nós que executam a imagem e os patches mais recentes do Windows Server e, em seguida, remove os nós mais antigos. Para obter mais informações sobre esse processo, confira [Atualizar um pool de nós no AKS][nodepool-upgrade].

Nós são implantados em uma sub-rede de rede virtual privada, sem nenhum endereço IP público atribuído. Para fins de solução de problemas e gerenciamento, o SSH é habilitado por padrão. Esse acesso SSH só está disponível usando o endereço IP interno.

Para fornecer armazenamento, os nós usam o Azure Managed Disks. Para a maioria dos tamanhos de nó VM, esses são discos Premium apoiados por SSDs de alto desempenho. Os dados armazenados em discos gerenciados são criptografados automaticamente em repouso na plataforma Azure. Para melhorar a redundância, esses discos também são replicados com segurança no datacenter do Azure.

Os ambientes do Kubernetes, no AKS ou em outro lugar, não estão completamente seguros atualmente para uso de vários locatários hostis. Recursos de segurança adicionais, como *políticas de segurança de Pod*, ou controle de acesso baseado em função kubernetes (kubernetes RBAC) mais refinado para nós, tornam as explorações mais difíceis. No entanto, para ter uma segurança de verdade ao executar cargas de trabalho de vários locatários hostis, um hipervisor é o único nível de segurança no qual você deve confiar. O domínio de segurança para o Kubernetes se torna o cluster inteiro, não um nó individual. Para esses tipos de cargas de trabalho de vários locatários hostis, você deve usar clusters fisicamente isolados. Para obter mais informações sobre maneiras de isolar cargas de trabalho, consulte [práticas recomendadas para isolamento de cluster em AKs][cluster-isolation].

### <a name="compute-isolation"></a>Isolamento de computação

 Determinadas cargas de trabalho podem exigir um alto grau de isolamento de outras cargas de trabalho do cliente devido a requisitos normativos ou de conformidade. Para essas cargas de trabalho, o Azure fornece [máquinas virtuais isoladas](../virtual-machines/isolation.md), que podem ser usadas como os nós de agente em um cluster AKs. Essas máquinas virtuais isoladas são isoladas para um tipo específico de hardware e são dedicadas a um único cliente. 

 Para usar essas máquinas virtuais isoladas com um cluster AKS, selecione um dos tamanhos de máquinas virtuais isoladas listados [aqui](../virtual-machines/isolation.md) como o **tamanho do nó** ao criar um cluster do AKS ou adicionar um pool de nós.


## <a name="cluster-upgrades"></a>Atualizações do cluster

Para segurança e conformidade, ou para usar os recursos mais recentes, o Azure fornece ferramentas para orquestrar a atualização de um cluster e componentes do AKS. Essa orquestração de atualização inclui os componentes de mestre e agente do Kubernetes. Você pode exibir uma [lista de versões do Kubernetes](supported-kubernetes-versions.md) disponíveis para seu cluster do AKS. Para iniciar o processo de atualização, você especificar uma dessas versões disponíveis. O Azure então realiza cordon e drenagem com segurança de cada nó do AKS e executa a atualização.

### <a name="cordon-and-drain"></a>Cordon e drenagem

Durante o processo de atualização, nós do AKS são individualmente isolados do cluster para que novos pods não sejam agendados neles. Os nós então são drenados e atualizados da seguinte maneira:

- Um novo nó é implantado no pool de nós. Esse nó executa a imagem e os patches mais recentes do sistema operacional.
- Um dos nós existentes é identificado para atualização. Os pods nesse nó são encerrados e agendados em outros nós do pool de nós.
- Esse nó existente é excluído do cluster do AKS.
- O próximo nó no cluster é isolado e esvaziado usando o mesmo processo, até que todos os nós sejam substituídos como parte do processo de atualização.

Para obter mais informações, confira [Atualizar um cluster do AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Segurança de rede

Para conectividade e segurança com redes locais, você pode implantar um cluster do AKS em sub-redes de rede virtual do Azure existentes. Essas redes virtuais podem ter uma conexão de VPN Site a Site ou Express Route do Azure para sua rede local. Controladores de entrada de Kubernetes podem ser definidos com os endereços IP privados e internos para que os serviços estejam acessíveis somente por essa conexão de rede interna.

### <a name="azure-network-security-groups"></a>Grupos de segurança de rede do Azure

Para filtrar o fluxo de tráfego em redes virtuais, o Azure usa regras de grupo de segurança de rede. Essas regras definem o código-fonte e intervalos de IP de destino, portas e protocolos que têm o acesso a recursos permitido ou negado. Regras padrão são criadas para permitir o tráfego TLS para o servidor de API do Kubernetes. Conforme você cria serviços com balanceadores de carga, mapeamentos de porta ou rotas de entrada, o AKS modifica automaticamente o grupo de segurança de rede para o tráfego fluir de modo adequado.

Nos casos em que você fornece sua própria sub-rede para o cluster AKS e deseja modificar o fluxo de tráfego, não modifique o grupo de segurança de rede no nível de sub-rede gerenciado pelo AKS. Você pode criar grupos de segurança de rede de nível de sub-rede adicionais para modificar o fluxo de tráfego, contanto que eles não interfiram no tráfego necessário para gerenciar o cluster, como o acesso ao balanceador de carga, a comunicação com o plano de controle e a [saída][aks-limit-egress-traffic].

### <a name="kubernetes-network-policy"></a>Política de rede do Kubernetes

Para limitar o tráfego de rede entre pods em seu cluster, o AKS oferece suporte para [políticas de rede do Kubernetes][network-policy]. Com as políticas de rede, você pode optar por permitir ou negar caminhos de rede específicos no cluster, com base em namespaces e seletores de rótulo.

## <a name="kubernetes-secrets"></a>Segredos do Kubernetes

Um *Segredo* do Kubernetes é usado para injetar dados confidenciais em pods, como chaves ou credenciais de acesso. Você primeiro criae um segredo usando a API do Kubernetes. Quando você define seu pod ou implantação, um segredo específico pode ser solicitado. Segredos são fornecidos apenas para nós que têm um pod agendado que precisa dele, e o segredo é armazenado no *tmpfs*, não gravado no disco. Quando o último pod em um nó que exige um segredo for excluído, o segredo é excluído do tmpfs do nó. Os segredos são armazenados dentro de um determinado namespace e só podem ser acessados pelo pods no mesmo namespace.

O uso de Segredos reduz as informações confidenciais definidas no manifesto YAML de pod ou serviço. Em vez disso, você pode solicitar o Segredo armazenado no servidor de API do Kubernetes como parte do seu manifesto YAML. Essa abordagem fornece apenas ao pod específico acesso ao Segredo. Observação: os arquivos brutos de manifesto de segredo contêm os dados secretos no formato base64 (confira a [documentação oficial][secret-risks] para obter mais detalhes). Portanto, esse arquivo deve ser tratado como informação confidencial e nunca ser confirmado no controle do código-fonte.

Os segredos do kubernetes são armazenados em etcd, um repositório de chave-valor distribuído. A etcd Store é totalmente gerenciada pelo AKS e [os dados são criptografados em repouso na plataforma do Azure][encryption-atrest]. 

## <a name="next-steps"></a>Próximas etapas

Para começar a proteger seus clusters do AKS, confira [Atualizar um cluster do AKS][aks-upgrade-cluster].

Para obter as práticas recomendadas associadas, confira [Práticas recomendadas de segurança e atualizações de cluster no AKS][operator-best-practices-cluster-security] e [Práticas recomendadas de segurança de pod no AKS][developer-best-practices-pod-security].

Para obter informações adicionais sobre os principais conceitos do Kubernetes e do AKS, consulte os seguintes artigos:

- [Clusters e cargas de trabalho do Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Identidade do Kubernetes/AKS][aks-concepts-identity]
- [Redes virtuais do Kubernetes/AKS][aks-concepts-network]
- [Armazenamento do Kubernetes/AKS][aks-concepts-storage]
- [Escala do Kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
