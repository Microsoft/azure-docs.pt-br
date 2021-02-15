---
title: Configurar a rede para Azure Dev Spaces em topologias de rede diferentes
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Descreve os requisitos de rede para executar o Azure Dev Spaces nos serviços Kubernetess do Azure
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, CNI, kubenet, SDN, rede
ms.openlocfilehash: 09114ab13555cbf9ef42b37c86ffb76a8fe3ab3f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91970331"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Configurar a rede para Azure Dev Spaces em topologias de rede diferentes

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

O Azure Dev Spaces é executado em clusters do AKS (serviço kubernetes do Azure) com a configuração de rede padrão. Se você quiser alterar a configuração de rede do cluster AKS, como colocar o cluster atrás de um firewall, usar grupos de segurança de rede ou usar políticas de rede, você precisará incorporar considerações adicionais para a execução de Azure Dev Spaces.

![Configuração de rede virtual](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Configurações de sub-rede ou rede virtual

O cluster AKS pode ter uma configuração de rede virtual ou de sub-rede diferente para restringir o tráfego de entrada ou saída para o cluster AKS. Por exemplo, o cluster pode estar protegido por um firewall, como o Firewall do Azure, ou você pode usar grupos de segurança de rede ou funções personalizadas para restringir o tráfego de rede. Você pode encontrar um exemplo de configuração de rede no [repositório de exemplo Azure dev Spaces no GitHub][sample-repo].

Azure Dev Spaces tem determinados requisitos de tráfego de rede de *entrada e saída* , bem como tráfego *somente de entrada* . Se você estiver usando Azure Dev Spaces em um cluster AKS com uma configuração de rede virtual ou de sub-rede que restringe o tráfego para o cluster AKS, você deverá seguir os seguintes requisitos de tráfego de entrada e de entrada e saída para que o Azure Dev Spaces funcione corretamente.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisitos de tráfego de rede de entrada e saída

Azure Dev Spaces precisa de tráfego de entrada e saída para os seguintes FQDNs:

| FQDN                       | Porta       | Use      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Para extrair imagens do Docker para Azure Dev Spaces |
| gcr.io                     | HTTPS: 443 | Para efetuar pull de imagens Helm para Azure Dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Para efetuar pull de imagens Helm para Azure Dev Spaces |

Atualize seu firewall ou configuração de segurança para permitir o tráfego de rede de e para todos os FQDNs e [serviços de infraestrutura de Azure dev Spaces][service-tags]acima. Por exemplo, se você estiver usando um firewall para proteger sua rede, os FQDNs acima deverão ser adicionados à regra de aplicativo do firewall e a marca de serviço de Azure Dev Spaces também deverá ser [adicionada ao firewall][firewall-service-tags]. Essas atualizações para o firewall são necessárias para permitir o tráfego de e para esses domínios.

### <a name="ingress-only-network-traffic-requirements"></a>Requisitos de tráfego de rede somente de entrada

Azure Dev Spaces fornece roteamento de nível de namespace kubernetes, bem como acesso público a serviços usando seu próprio FQDN. Para que esses dois recursos funcionem, atualize sua configuração de firewall ou de rede para permitir a entrada pública para o endereço IP externo do controlador de entrada do Azure Dev Spaces no cluster. Como alternativa, você pode criar um [balanceador de carga interno][aks-internal-lb] e adicionar uma regra NAT em seu firewall para converter o IP público do firewall para o IP do balanceador de carga interno. Você também pode usar [traefik][traefik-ingress] ou [Nginx][nginx-ingress] para criar um controlador de entrada personalizado.

## <a name="aks-cluster-network-requirements"></a>Requisitos de rede de cluster AKS

O AKS permite que você use [políticas de rede][aks-network-policies] para controlar o tráfego de entrada e saída entre pods em um cluster, bem como o tráfego de saída de um pod. Azure Dev Spaces tem determinados requisitos de tráfego de rede de *entrada e saída* , bem como tráfego *somente de entrada* . Se você estiver usando Azure Dev Spaces em um cluster AKS com políticas de rede AKS, deverá seguir os seguintes requisitos de tráfego de entrada e entrada e saída para que os Azure Dev Spaces funcionem corretamente.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisitos de tráfego de rede de entrada e saída

Azure Dev Spaces permite que você se comunique diretamente com um pod em um espaço de desenvolvimento no cluster para depuração. Para que esse recurso funcione, adicione uma política de rede que permita a comunicação de entrada e saída para os endereços IP da infraestrutura de Azure Dev Spaces, que [variam por região][service-tags].

### <a name="ingress-only-network-traffic-requirements"></a>Requisitos de tráfego de rede somente de entrada

Azure Dev Spaces fornece roteamento entre os pods entre os namespaces. Por exemplo, namespaces com Azure Dev Spaces habilitado podem ter uma relação pai/filho, que permite que o tráfego de rede seja roteado entre pods entre os namespaces pai e filho. O Azure Dev Spaces também expõe os pontos de extremidade de serviço usando seu próprio FQDN. Para configurar diferentes maneiras de expor serviços e como ele afeta o roteamento em nível de namespace, consulte [usando opções de ponto de extremidade diferentes][endpoint-options].

## <a name="using-azure-cni"></a>Usando o Azure CNI

Por padrão, os clusters AKS são configurados para usar o [kubenet][aks-kubenet] para rede, que funciona com Azure dev Spaces. Você também pode configurar o cluster AKS para usar o [CNI (interface de rede de contêiner do Azure)][aks-cni]. Para usar Azure Dev Spaces com o Azure CNI em seu cluster AKS, permita que seus espaços de rede virtual e de endereço de sub-rede tenham até 10 endereços IP privados para pods implantados pelo Azure Dev Spaces. Mais detalhes sobre como permitir endereços IP privados estão disponíveis na [documentação do AKS Azure CNI][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Usando intervalos de IP autorizados do servidor de API

Os clusters AKS permitem que você configure a segurança adicional que limita o endereço IP que pode interagir com seus clusters, por exemplo, usando redes virtuais personalizadas ou [protegendo o acesso ao servidor de API usando intervalos de IP autorizados][aks-ip-auth-ranges]. Para usar Azure Dev Spaces ao usar essa segurança adicional ao [criar][aks-ip-auth-range-create] o cluster, você deve [permitir intervalos adicionais com base em sua região][service-tags]. Você também pode [Atualizar][aks-ip-auth-range-update] um cluster existente para permitir esses intervalos adicionais. Você também precisa permitir o endereço IP de qualquer computador de desenvolvimento que se conecta ao cluster AKS para depuração para se conectar ao servidor de API.

## <a name="using-aks-private-clusters"></a>Usando clusters privados do AKS

Neste momento, não há suporte para Azure Dev Spaces com [clusters privados AKs][aks-private-clusters].

## <a name="using-different-endpoint-options"></a>Usando opções de ponto de extremidade diferentes

Azure Dev Spaces tem a opção de expor pontos de extremidade para seus serviços em execução no AKS. Ao habilitar Azure Dev Spaces em seu cluster, você tem as seguintes opções para configurar o tipo de ponto de extremidade para o cluster:

* Um ponto de extremidade *público* , que é o padrão, implanta um controlador de entrada com um endereço IP público. O endereço IP público é registrado no DNS do cluster, permitindo acesso público a seus serviços usando uma URL. Você pode exibir essa URL usando `azds list-uris` .
* Um ponto de extremidade *privado* implanta um controlador de entrada com um endereço IP privado. Com um endereço IP privado, o balanceador de carga para o cluster só pode ser acessado de dentro da rede virtual do cluster. O endereço IP privado do balanceador de carga é registrado no DNS do cluster para que os serviços dentro da rede virtual do cluster possam ser acessados usando uma URL. Você pode exibir essa URL usando `azds list-uris` .
* A definição de *nenhum* para a opção de ponto de extremidade faz com que nenhum controlador de entrada seja implantado. Sem o controlador de entrada implantado, os [recursos de roteamento Azure dev Spaces][dev-spaces-routing] não funcionarão. Opcionalmente, você pode implementar sua própria solução de controlador de entrada usando [traefik][traefik-ingress] ou [Nginx][nginx-ingress], que permitirá que os recursos de roteamento funcionem novamente.

Para configurar sua opção de ponto de extremidade, use *-e* ou *--endpoint* ao habilitar Azure dev Spaces no cluster. Por exemplo:

> [!NOTE]
> A opção de ponto de extremidade requer que você esteja executando CLI do Azure versão 2.2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Requisitos do cliente

Azure Dev Spaces usa ferramentas do lado do cliente, como a extensão da CLI do Azure Dev Spaces, a extensão Visual Studio Code e a extensão do Visual Studio, para se comunicar com o cluster AKS para depuração. Para usar as Azure Dev Spaces ferramentas do lado do cliente, permita o tráfego das máquinas de desenvolvimento para a [infraestrutura de Azure dev Spaces][dev-spaces-allow-infrastructure]. Se estiver usando [intervalos de IP autorizados do servidor de API][auth-range-section], você também precisará permitir o endereço IP de qualquer computador de desenvolvimento que se conecta ao cluster AKs para depuração para se conectar ao servidor de API.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como funciona o Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como o Azure Dev Spaces funciona](how-dev-spaces-works.md)

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-allow-infrastructure]: #virtual-network-or-subnet-configurations
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[firewall-service-tags]: ../firewall/service-tags.md
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[service-tags]: ../virtual-network/service-tags-overview.md#available-service-tags