---
title: Conceitos – Diagrama de rede para o Red Hat do Azure no OpenShift 4
description: Diagrama e visão geral da rede para o Red Hat OpenShift no Azure
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 11/23/2020
ms.openlocfilehash: 5d69aacb6e3f25e3414aa446c4c5ae7852cabdfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720895"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Conceitos de rede no ARO (Red Hat OpenShift no Azure)

Este guia traz uma visão geral da rede no Red Hat OpenShift no Azure em clusters do OpenShift 4, bem como um diagrama e uma lista de pontos de extremidade importantes. Para obter mais informações sobre os principais conceitos de rede no OpenShift, confira a [Documentação de rede do Red Hat OpenShift no Azure 4](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

![Diagrama de rede do Red Hat OpenShift no Azure 4](./media/concepts-networking/aro4-networking-diagram.png)

Quando você implanta o Red Hat do Azure no OpenShift 4, todo o seu cluster fica contido em uma rede virtual. Dentro dessa rede virtual, os nós mestres e nós de trabalho ficam, cada um, na própria sub-rede. Cada sub-rede usa um balanceador de carga interno e um balanceador de carga público.

## <a name="networking-components"></a>Componentes de rede

A lista a seguir abrange os componentes de rede importantes em um cluster do Red Hat OpenShift no Azure.

* **aro-pls**
    * Esse é um ponto de extremidade do Link Privado do Azure, usado pelos engenheiros de confiabilidade de site da Microsoft e do Red Hat para gerenciar o cluster.
* **aro-internal-lb**
    * Este ponto de extremidade é responsável pelo balanceamento do tráfego para o servidor da API. Para esse balanceador de carga, os nós mestres estão no pool de back-end.
* **aro-public-lb**
    * Quando a API é pública, este ponto de extremidade encaminha e balanceia o tráfego para o servidor da API. Este ponto de extremidade atribui um IP de saída público para que os mestres possam acessar o Azure Resource Manager e gerar relatórios sobre a integridade do cluster.
* **aro-internal**
    * Este ponto de extremidade é responsável pelo balanceamento do tráfego de serviço interno. Para esse balanceador de carga, os nós de trabalho estão no pool de back-end.
    * Esse balanceador de carga não é criado por padrão. Ele é criado depois que você cria um serviço do tipo LoadBalancer com as anotações corretas. Por exemplo: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **aro-internal-lb**
    * Este ponto de extremidade é usado para qualquer tráfego público. Quando você cria um aplicativo e uma rota, esse é o caminho do tráfego de entrada.
    * Esse balanceador de carga também cuida da conectividade de saída da Internet de qualquer pod em execução nos nós de trabalho por meio das Regras de saída do Azure Load Balancer.
        * Atualmente, as regras de saída não podem ser configuradas. Eles alocam 1.024 portas TCP a cada nó.
        * DisableOutboundSnat não está configurado nas regras de LB, portanto, o pods podem receber como IP de saída qualquer IP público configurado neste laboratório.
        * Como consequência dos dois tópicos anteriores, a única maneira de adicionar portas SNAT efêmeras é adicionar serviços do tipo balanceador de carga público ao ARO.
* **aro-outbound-pip**
    * Este ponto de extremidade serve como um PIP (IP público) para os nós de trabalho.
    * Ele permite que os serviços adicionem um IP específico proveniente de um cluster do Red Hat OpenShift no Azure a uma lista de permissões.
* **aro-nsg**
    * Quando você expõe um serviço, a API cria uma regra neste grupo de segurança de rede para que o tráfego flua e alcance os nós e o painel de controle.
    * Por padrão, este grupo de segurança de rede permite todo o tráfego de saída. Atualmente, o tráfego de saída só pode ser restrito ao plano de controle do Red Hat OpenShift no Azure.
* **aro-controlplane-nsg**
  * Este ponto de extremidade permite que o tráfego entre pela porta 6443 para os nós mestres.
* **Registro de Contêiner do Azure**
    * É um registro de contêiner fornecido e usado pela Microsoft internamente. Esse Registro é somente leitura e não deve ser usado pelos usuários do Red Hat OpenShift no Azure.
        * Esse registro fornece imagens de plataforma de host e componentes de cluster. Por exemplo, contêineres de monitoramento ou registro em log.
        * As conexões com este registro ocorrem pelo ponto de extremidade de serviço (conectividade interna entre os serviços do Azure).
        * Por padrão, esse registro interno não fica disponível fora do cluster.
* **Link privado**
    * Permite que a conectividade de rede do plano de gerenciamento em um cluster dos engenheiros de confiabilidade do site da Microsoft e do Red Hat ajude a gerenciar o seu cluster.

## <a name="networking-policies"></a>Políticas de rede

* **Entrada**: A política de rede de entrada tem suporte como parte da [SDN do OpenShift](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html). Essa política de rede é habilitada por padrão e a imposição é realizada pelos usuários. Embora a política de rede de entrada esteja em conformidade com V1 NetworkPolicy, os Tipos de IPBlock e de Saída não têm suporte.

* **Saída**: As políticas de rede de saída têm suporte usando o recurso de [firewall de saída](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html) no OpenShift. Há apenas uma política de saída por namespace/projeto. As políticas de saída não têm suporte no namespace "padrão" e são avaliadas em ordem (primeira à última).

## <a name="networking-basics-in-openshift"></a>Noções básicas de rede no OpenShift

A [SDN](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/about-openshift-sdn.html) (Rede definida pelo software) do OpenShift é usada para configurar uma rede de sobreposição usando o [OVS](https://www.openvswitch.org/) (Open vSwitch), uma implementação do OpenFlow baseada na especificação do CNI (Adaptador de Rede de Contêiner). A SDN dá suporte a plug-ins diferentes. A Política de Rede é o plug-in usado no Red Hat do Azure no OpenShift 4. Toda a comunicação de rede é gerenciada pela SDN, portanto, nenhuma rota extra é necessária em suas redes virtuais para alcançar a comunicação de pod a pod.

## <a name="networking--for-azure-red-hat-openshift"></a>Rede do Red Hat OpenShift no Azure

Os seguintes recursos de rede são específicos do Red Hat OpenShift no Azure:  
* Os usuários podem criar o seu cluster do ARO em uma rede virtual existente ou criar uma rede virtual ao criar o cluster do ARO.
* Os CIDRs de rede de serviço e de pod são configuráveis.
* Os nós e mestres ficam em sub-redes diferentes.
* As sub-redes da rede virtual de nós e mestres devem ser, no mínimo, /27.
* O CIDR de pod padrão é 10.128.0.0/14.
* O CIDR de serviço padrão é 172.30.0.0/16.
* Os CIDRs de pod e de serviço não devem ser sobrepostos a outros intervalos de endereços em uso em sua rede e não devem estar dentro do intervalo de endereços IP da rede virtual do seu cluster.
* O CIDR de pod deve o tamanho mínimo de /18. (A rede do pod tem IPs não roteáveis e só é usada dentro da SDN do OpenShift).
* A cada nó é alocada uma sub-rede /23 (512 IPs) para seu pods. Esse valor não pode ser alterado.
* Não é possível anexar um pod a várias redes.
* Não é possível configurar o IP estático de saída. (Esse é um recurso do OpenShift. Para obter mais informações, confira [configurar IPs de saída](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/assigning-egress-ips.html)).

## <a name="network-settings"></a>Configurações de rede

As seguintes configurações de rede estão disponíveis nos clusters do Red Hat OpenShift no Azure 4:

* **Visibilidade da API** – defina a visibilidade da API ao executar o comando [az aro create command](tutorial-create-cluster.md#create-the-cluster).
    * "Público" – o Servidor da API é acessível por redes externas.
    * "Privado" – o Servidor da API atribuiu um IP privado da sub-rede de mestres, acessível somente usando redes conectadas (redes virtuais emparelhadas, outras sub-redes no cluster). Uma Zona DNS privada será criada em nome do cliente.
* **Visibilidade de Entrada** – defina a visibilidade da API ao executar o comando [az aro create command](tutorial-create-cluster.md#create-the-cluster).
    * As rotas "públicas" usam como padrão o Standard Load Balancer público (isso pode ser alterado).
    * As rotas "privadas" usam como padrão o balanceador de carga interno (isso pode ser alterado).

## <a name="network-security-groups"></a>Grupos de segurança de rede
Os grupos de segurança de rede são criados no grupo de recursos dos nós, que é bloqueado para usuários. Os grupos de segurança de rede são atribuídos diretamente às sub-redes que não estão nas NICs do nó. Os grupos de segurança de rede são imutáveis e os usuários não têm as permissões necessárias para alterá-los.

Com um servidor de API visível publicamente, você não pode criar grupos de segurança de rede e atribuí-los aos adaptadores de rede.

## <a name="domain-forwarding"></a>Encaminhamento de domínio
O Red Hat OpenShift no Azure usa o CoreDNS. O encaminhamento de domínio pode ser configurado. Você não pode usar um DNS próprio nas suas redes virtuais. Para obter mais informações, confira a documentação sobre [como usar o encaminhamento do DNS](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html#nw-dns-forward_dns-operator).

## <a name="whats-new-in-openshift-45"></a>Novidades no OpenShift 4.5

Com o suporte do OpenShift 4.5, o Red Hat OpenShift no Azure introduziu algumas alterações de arquitetura significativas. Essas alterações se aplicam somente aos clusters recém-criados que executam o OpenShift 4.5. Os clusters existentes que foram atualizados para o OpenShift 4.5 não terão a arquitetura de rede alterada pelo processo de atualização. Os usuários precisarão recriar os clusters para usar essa nova arquitetura.

![Diagrama de rede do Red Hat OpenShift no Azure 4.5](./media/concepts-networking/aro-4-5-networking-diagram.png)

Conforme incluído no diagrama acima, você observará algumas alterações:
* Anteriormente, o ARO usava dois LoadBalancers públicos: um para o servidor de API e outro para o pool de nós de trabalho. Com essa atualização de arquitetura, isso foi consolidado em um LoadBalancer. 
* Para reduzir a complexidade, os recursos de endereço IP de outboard dedicados foram removidos.
* O plano de controle do ARO agora compartilha o mesmo grupo de segurança de rede que os nós de trabalho do ARO.

Para obter mais informações sobre o OpenShift 4.5, confira as [Notas sobre a versão do OpenShift 4.5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o tráfego de saída e a que o Red Hat OpenShift no Azure dá suporte em termos de saída, confira a documentação das [políticas de suporte](support-policies-v4.md).
