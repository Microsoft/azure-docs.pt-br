---
title: Melhores práticas do operador - Conectividade de rede nos Serviço de Kubernetes do Azure (AKS)
description: Aprenda as práticas recomendadas do operador de cluster para rede virtual e conectividade no Serviço de Kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: mlearned
ms.openlocfilehash: d1bc865b38b52c8a7c3ac6ec4dab6408a1d0430c
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614761"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para conectividade de rede e segurança no Serviço de Kubernetes do Azure (AKS)

Ao criar e gerenciar clusters no Serviço de Kubernetes do Azure (AKS), você fornece conectividade de rede para seus nós e aplicativos. Esses recursos de rede incluem os intervalos de endereços IP, balanceadores de carga e controladores de entrada. Para manter a alta qualidade de serviço para seus aplicativos, você precisa planejar e, em seguida, configurar esses recursos.

Este artigo sobre práticas recomendadas se concentra na conectividade de rede e segurança para operadores do cluster. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Comparar os modos de rede kubenet e CNI do Azure no AKS
> * Planeje o endereçamento de IP necessário e conectividade
> * Distribua o tráfego usando balanceadores de carga, controladores de entrada ou firewalls de aplicativo web (WAF)
> * Conectar-se com segurança a nós do cluster

## <a name="choose-the-appropriate-network-model"></a>Escolha o modelo de rede apropriado

**Diretrizes de melhores práticas** – para integração com redes virtuais existentes ou redes locais, use a rede do CNI do Azure no AKS. Esse modelo de rede também permite maior separação de recursos e controles em um ambiente corporativo.

Redes virtuais fornecem a conectividade básica para que os clientes acessem seus aplicativos e nós do AKS. Há duas maneiras diferentes para implantar clusters AKS em redes virtuais:

* **Rede Kubenet** -o Azure gerencia os recursos de rede virtual, como o cluster é implantado e usa o [kubenet][kubenet] plug-in do Kubernetes.
* **A rede do Azure CNI** - implanta em uma rede virtual existente e usa o [Azure contêiner rede CNI (Interface)][cni-networking] plug-in do Kubernetes. Pods recebem IPs individuais que podem rotear para outros serviços de rede ou a recursos locais.

A Interface de Rede do Contêiner (CNI) é um protocolo de fornecedor que permite que o tempo de execução do contêiner faça solicitações para um provedor de rede. O CNI do Azure atribui endereços IP para nós e pods e fornece recursos IPAM (gerenciamento) de endereço IP que você conecta as redes virtuais existentes do Azure. Cada recurso de nó e o’ pod recebe um endereço IP na rede virtual do Azure, e nenhum roteamento adicional é necessário para se comunicar com outros serviços ou recursos.

![Diagrama que mostra dois nós com pontes conectando cada um a uma única VNet do Azure](media/operator-best-practices-network/advanced-networking-diagram.png)

Na maioria das implantações de produção, você deve usar a rede do CNI do Azure. Esse modelo de rede permite a separação de controle e gerenciamento de recursos. De uma perspectiva de segurança, você geralmente deseja diferentes equipes para gerenciar e proteger esses recursos. A rede do CNI do Azure permite que você se conecte a recursos existentes do Azure, a recursos locais ou a outros serviços diretamente por meio de endereços IP atribuídos a cada pod.

Quando você usa a rede do CNI do Azure, o recurso de rede virtual fica em um grupo de recursos separado para o cluster do AKS. Delegar permissões para a entidade de serviço AKS para acessar e gerenciar esses recursos. A entidade de serviço usada pelo cluster do AKS deve ter pelo menos permissões de [Colaborador de Rede](../role-based-access-control/built-in-roles.md#network-contributor) na sub-rede na rede virtual. Se você quiser definir uma [função personalizada](../role-based-access-control/custom-roles.md) em vez de usar a função de Colaborador de Rede interna, as seguintes permissões serão necessárias:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Para obter mais informações sobre a delegação de entidade de serviço AKS, consulte [Delegar acesso a outros recursos do Azure][sp-delegation].

Como cada nó e pod recebe seu próprio endereço IP, planeje os intervalos de endereços para as sub-redes do AKS. A sub-rede deve ser grande o suficiente para fornecer endereços IP para cada nó, pods e recursos de rede que você implanta. Cada cluster do AKS deve ser colocado em sua própria sub-rede. Para permitir a conectividade para redes emparelhadas ou locais no Azure, não use os intervalos de endereços IP que se sobrepõem com os recursos de rede existente. Há limites padrão ao número de pods que cada nó executa com a rede kubenet e a do CNI do Azure. Para lidar com eventos ou atualizações de cluster de expansão, você também precisa de endereços IP adicionais disponíveis para uso na sub-rede atribuída. Esse espaço de endereço adicional é especialmente importante se você usar contêineres do Windows Server (atualmente em visualização no AKS), como os pools de nó exigem uma atualização para aplicar os patches de segurança mais recentes. Para obter mais informações sobre nós do Windows Server, consulte [Upgrade de um pool de nós no AKS][nodepool-upgrade].

Para calcular o endereço IP necessário, consulte [rede configurar CNI do Azure no AKS][advanced-networking].

### <a name="kubenet-networking"></a>Rede Kubenet

Embora o kubenet não exija que você configure as redes virtuais antes de o cluster ser implantado, há desvantagens:

* Nós e pods são colocados em diferentes sub-redes de IP. Roteamento definido pelo usuário (UDR) e encaminhamento IP é usado para rotear o tráfego entre nós e pods. Esse roteamento adicional pode reduzir o desempenho da rede.
* As conexões com redes locais existentes ou emparelhamento com outras redes virtuais do Azure podem ser complexas.

O kubenet é adequado para desenvolver ou testar cargas de trabalho pequenas, pois você não precisa criar a rede virtual e as sub-redes separadamente do cluster AKS. Sites simples com baixo tráfego ou para comparar e deslocar cargas de trabalho em contêineres também podem se beneficiar da simplicidade de clusters AKS implantados com o sistema de rede kubenet. Na maioria das implantações de produção, você deve planejar e usar a rede do CNI do Azure. Você também pode [configurar seus próprios intervalos de endereços IP e redes virtuais usando kubenet][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuir o tráfego de entrada

**Diretrizes de práticas recomendadas** – para distribuir o tráfego HTTP ou HTTPS para seus aplicativos, use os recursos de entrada e controladores. Controladores de entrada fornecem recursos adicionais ao longo de um balanceador de carga do Azure regular e podem ser gerenciados como recursos nativos do Kubernetes.

Um balanceador de carga do Azure pode distribuir o tráfego de cliente para aplicativos no cluster do AKS, mas é limitado nele compreende sobre esse tráfego. Um recurso de balanceador de carga funciona na camada 4 e distribui o tráfego com base no protocolo ou portas. A maioria dos aplicativos web que usam HTTP ou HTTPS deve usar os recursos de entrada Kuberenetes e controladores que funcionam na camada 7. A entrada pode distribuir o tráfego com base na URL do aplicativo e manusear o término TLS/SSL. Essa capacidade também reduz o número de endereços IP de expor e mapear. Com um balanceador de carga, cada aplicativo normalmente precisa de um endereço IP público atribuído e mapeado para o serviço no cluster AKS. Com um recurso de entrada, um único endereço IP pode distribuir o tráfego para vários aplicativos.

![Diagrama mostrando o fluxo de tráfego de ingresso em um cluster AKS](media/operator-best-practices-network/aks-ingress.png)

 Há dois componentes para a entrada:

 * Uma entrada *recurso*, e
 * Um *controlador* de entrada

O recurso de entrada é um manifesto YAML do `kind: Ingress` que define o host, certificados e as regras para rotear o tráfego para serviços que são executados no cluster do AKS. O manifesto YAML do exemplo a seguir seria distribuir o tráfego para *myapp.com* a um dos dois serviços, *blogservice* ou *storeservice*. O cliente é direcionado para um serviço ou outro com base na URL que ele acessa.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

Um controlador de entrada é um daemon que é executado em um nó do AKS e observa as solicitações de entrada. Em seguida, o tráfego é distribuído com base em regras definidas no recurso de entrada. O controlador de entrada mais comum se baseia em [NGINX]. AKS não restringe a um controlador específico, portanto, você pode usar outros controladores, como [delimitação][contour], [HAProxy][haproxy], ou [Traefik][traefik].

Controladores de entrada devem ser agendadas em um nó do Linux. Nós do Windows Server (atualmente em visualização no AKS) não devem executar o controlador de entrada. Use um seletor de nó em seu manifesto YAML ou implantação de gráfico do Helm para indicar que o recurso deve ser executado em um nó com base em Linux. Para obter mais informações, consulte [usam seletores de nó para controlar onde os pods são agendados no AKS][concepts-node-selectors].

Há muitos cenários para entrada, incluindo os guias de instruções a seguir:

* [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
* [Criar um controlador de entrada que usa uma rede interna, privada e o endereço IP][aks-ingress-internal]
* [Criar um controlador de entrada que usa seus próprios certificados TLS][aks-ingress-own-tls]
* Criar um controlador de entrada que usa vamos criptografar para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] or [with a static public IP address][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Tráfego seguro com um firewall do aplicativo Web (WAF)

**Práticas de orientação prática** - para verificar o tráfego de entrada para ataques potenciais, use o firewall do aplicativo web (WAF), como [Barracuda WAF para Azure][barracuda-waf] ou Gateway de aplicativo do Azure. Esses recursos mais avançados de rede também podem rotear o tráfego além de apenas conexões HTTP e HTTPS ou terminação SSL básica.

Normalmente, um controlador de entrada que distribui o tráfego para serviços e aplicativos é um recurso do Kubernetes no cluster do AKS. O controlador é executado como um daemon em um nó do AKS e consome alguns dos recursos do nó, como CPU, memória e largura de banda de rede. Em ambientes maiores, você geralmente deseja descarregar alguns desses roteamentos de tráfego ou a terminação TLS a um recurso de rede fora do cluster do AKS. Você também deseja verificar o tráfego de entrada para possíveis ataques.

![Um firewall do aplicativo web (WAF) como o Gateway de Aplicativo do Azure pode proteger e distribuir o tráfego para seu cluster do AKS](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Um firewall do aplicativo web (WAF) fornece uma camada adicional de segurança pela filtragem do tráfego de entrada. O Open Web Application Security Project (OWASP) fornece um conjunto de regras para assistir a ataques, como scripts entre sites ou intoxicação por cookie. [O Gateway de aplicativo do Azure][app-gateway] (atualmente em visualização no AKS) é um WAF que pode se integrar com clusters AKS para fornecer esses recursos de segurança, antes do tráfego alcança seu cluster do AKS e aplicativos. Outras soluções de terceiros também executam essas funções, portanto, você pode continuar a usar os investimentos existentes ou experiência em um determinado produto.

Recursos de entrada ou do Balanceador de carga continuam sendo executados no cluster do AKS para refinar ainda mais a distribuição de tráfego. Gateway de Aplicativo pode ser gerenciado centralmente como um controlador de entrada com uma definição de recurso. Para começar, [criar um controlador de entrada do Gateway de aplicativo][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Controlar o fluxo de tráfego com políticas de rede

**Diretrizes de melhores práticas** – Use as políticas de rede para permitir ou recusar o tráfego para os pods. Por padrão, todo o tráfego é permitido entre pods dentro de um cluster. Para maior segurança, defina regras que limitam a comunicação entre pods.

As políticas de rede são recursos de Kubernetes que permitem controlar o fluxo de tráfego entre os pods. Você pode optar por permitir ou negar o tráfego com base em configurações, como rótulos atribuídos, namespace ou porta de tráfego. O uso de políticas de rede é uma maneira mais nativa da nuvem de controlar o fluxo de tráfego. Como os pods são criados dinamicamente em um cluster AKS, as políticas de rede necessárias podem ser aplicadas automaticamente. Não use grupos de segurança de rede do Azure para controlar o tráfego entre pods, use as políticas de rede.

Para usar a política de rede, o recurso precisa estar habilitado durante a criação de um cluster AKS. Não é possível habilitar a política de rede em um cluster AKS existente. Planeje com antecedência para garantir que a política de rede seja habilitada nos clusters e que eles sejam usados conforme necessário. Política de rede deve ser usada apenas para nós com base em Linux e os pods no AKS.

Uma política de rede é criada como um recurso do Kubernetes usando um manifesto YAML. As políticas são aplicadas a pods definidos, em seguida, as regras de entrada ou saída definem como o tráfego pode fluir. O exemplo a seguir aplica uma política de rede aos pods com o rótulo *app: back-end* aplicado. Então, a regra de entrada permitirá apenas o tráfego dos pods com o rótulo *app: front-end*:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

Para se familiarizar com as políticas, consulte [proteger o tráfego entre os pods usando diretivas de rede no serviço de Kubernetes do Azure (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Conecte-se com segurança a nós por meio de um host bastião

**Guia de práticas recomendadas de** -não expõe a conectividade remota em seus nós do AKS. Crie um host bastião, ou jump box, em uma rede virtual de gerenciamento. Use o host de bastião para rotear o tráfego com segurança no cluster do AKS para tarefas de gerenciamento remoto.

A maioria das operações no AKS pode ser concluída usando as ferramentas de gerenciamento do Azure ou por meio do servidor de API do Kubernetes. Os nós AKS não estão conectados à internet pública e só estão disponíveis em uma rede privada. Para se conectar aos nós e realizar a manutenção ou solucionar problemas, roteie suas conexões através de um host bastião jump box. Esse host deve estar em uma rede virtual de gerenciamento separado que é emparelhada com segurança à rede virtual do cluster do AKS.

![Conecte-se aos nós do AKS usando um host bastião jump box](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

A rede de gerenciamento para o host bastião deve ser segura também. Use uma [do Azure ExpressRoute][expressroute] or [VPN gateway][vpn-gateway] para se conectar a uma rede local e controlar o acesso usando grupos de segurança de rede.

## <a name="next-steps"></a>Próximas etapas

Este artigo se concentra na conectividade de rede e segurança. Para obter mais informações sobre conceitos básicos de rede no Kubernetes, consulte [conceitos para aplicativos no serviço de Kubernetes do Azure (AKS) de rede][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool