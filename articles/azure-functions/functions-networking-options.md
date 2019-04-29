---
title: O Azure Functions, as opções de rede
description: Uma visão geral de todas as opções de rede disponíveis no Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: b7af0149a690e3cc3a357a5cb769751e3674d374
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437677"
---
# <a name="azure-functions-networking-options"></a>O Azure Functions, as opções de rede

Este artigo descreve os recursos de rede disponíveis entre as opções de hospedagem para o Azure Functions. Todas as seguintes opções de rede fornecem alguma capacidade de acessar os recursos sem usar endereços roteáveis da internet ou restringir o acesso à internet para um aplicativo de funções. 

Os modelos de hospedagem tem diferentes níveis de isolamento de rede disponível. Escolher o correto ajudará você a atender às suas necessidades de isolamento de rede.

Você pode hospedar aplicativos de funções de duas maneiras:

* Há um conjunto de opções de plano que são executados em uma infraestrutura de multilocatária, com vários níveis de conectividade de rede virtual e opções de dimensionamento:
    * O [plano de consumo](functions-scale.md#consumption-plan), que dimensiona dinamicamente em resposta ao carregamento e oferece opções de isolamento de rede mínimas.
    * O [plano Premium](functions-scale.md#premium-plan-public-preview), que também dimensiona dinamicamente, além de oferecer isolamento de rede mais abrangente.
    * O Azure [plano do serviço de aplicativo](functions-scale.md#app-service-plan), que opera em uma escala fixa e oferece isolamento de rede semelhantes para o plano Premium.
* Você pode executar funções em um [ambiente de serviço de aplicativo](../app-service/environment/intro.md). Este método implanta sua função na sua rede virtual e oferece isolamento e controle de rede completo.

## <a name="matrix-of-networking-features"></a>Matriz de recursos de rede

|                |[Plano de consumo](functions-scale.md#consumption-plan)|⚠ [Plano Premium](functions-scale.md#premium-plan-public-preview)|[Plano do Serviço de Aplicativo](functions-scale.md#app-service-plan)|[Ambiente do Serviço de Aplicativo](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Restrições de IP de entrada](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integração de rede virtual](#virtual-network-integration)|❌No|❌No|✅Yes|✅Yes|
|[Visualização da integração de rede virtual (ExpressRoute do Azure e pontos de extremidade de serviço)](#preview-version-of-virtual-network-integration)|❌No|⚠Sim|⚠Sim|✅Yes|
|[Conexões híbridas](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[acesso ao site privado](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ Esse recurso de visualização não é para uso em produção.

## <a name="inbound-ip-restrictions"></a>Restrições de IP de entrada

Você pode usar as restrições de IP para definir uma lista ordenada de prioridade de endereços IP que são permitido/negado acesso ao seu aplicativo. A lista pode incluir endereços IPv4 e IPv6. Quando há uma ou mais entradas, implícito "negar tudo" existe no final da lista. Restrições de IP funcionam com todas as opções de hospedagem de função.

> [!NOTE]
> Para usar o editor do portal do Azure, o portal deve ser capaz de acessar diretamente o seu aplicativo de função em execução. Além disso, o dispositivo que você está usando para acessar o portal deve ter sua lista de permissões IP. Com as restrições de rede em vigor, você ainda pode acessar qualquer recurso nas **recursos da plataforma** guia.

Para obter mais informações, consulte [restrições de acesso estático do serviço de aplicativo do Azure](../app-service/app-service-ip-restrictions.md).

## <a name="virtual-network-integration"></a>Integração de rede virtual

Integração da rede virtual permite que seu aplicativo de funções acessar os recursos dentro de uma rede virtual. Esse recurso está disponível no plano Premium e o plano de serviço de aplicativo. Se seu aplicativo estiver em um ambiente de serviço de aplicativo, ele já está em uma rede virtual e não requer o uso da integração da rede virtual para acessar recursos na mesma rede virtual.

Integração da rede virtual fornece ao aplicativo acesso de função aos recursos em sua rede virtual, mas não concede [acesso de site privado](#private-site-access) ao seu aplicativo de função da rede virtual.

Você pode usar a integração de rede virtual para habilitar o acesso de aplicativos para bancos de dados e serviços web em execução em sua rede virtual. Com a integração de rede virtual, você não precisa expor um ponto de extremidade público para aplicativos em sua VM. Você pode usar os endereços roteáveis privados, fora da internet.

A versão disponível da integração da rede virtual se baseia em um gateway de VPN para se conectar a aplicativos de funções a uma rede virtual. Ele está disponível nas funções hospedadas em um plano de serviço de aplicativo. Para saber como configurar esse recurso, consulte [integrar seu aplicativo com uma rede virtual do Azure](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-version-of-virtual-network-integration"></a>Versão de visualização da integração da rede virtual

Uma nova versão do recurso de integração de rede virtual está em visualização. Ele não depende de VPN ponto a site. Ele dá suporte a acesso a recursos em ExpressRoute ou pontos de extremidade de serviço. Ele está disponível no plano Premium e nos planos de serviço de aplicativo dimensionados para PremiumV2.

Aqui estão algumas características dessa versão:

* Não é necessário um gateway para usá-lo.
* Você pode acessar recursos em conexões do ExpressRoute sem nenhuma configuração adicional além da integração com a rede virtual conectada ao ExpressRoute.
* Você pode consumir recursos protegidos de ponto de extremidade de serviço de execução do functions. Para fazer isso, habilite os pontos de extremidade de serviço na sub-rede usada para a integração de rede virtual.
* Você não pode configurar gatilhos para usar os recursos protegidos de ponto de extremidade de serviço. 
* O aplicativo de funções e a rede virtual devem estar na mesma região.
* O novo recurso requer uma sub-rede não utilizada na rede virtual implantado por meio do Azure Resource Manager.
* Não há suporte para cargas de trabalho de produção enquanto o recurso está em visualização.
* Tabelas de rotas e emparelhamento global ainda não estão disponíveis com o recurso.
* Um endereço é usado para cada instância de potencial de um aplicativo de funções. Porque você não pode alterar o tamanho da sub-rede após a atribuição, use uma sub-rede que pode facilmente dar suporte a seu tamanho máximo de escala. Por exemplo, para dar suporte a um plano Premium que pode ser dimensionado para 80 instâncias, é recomendável um `/25` subrede que fornece endereços de host 126.

Para saber mais sobre como usar a versão de visualização de integração da rede virtual, consulte [integrar um aplicativo de funções com uma rede virtual do Azure](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Conexões Híbridas

[Conexões híbridas](../service-bus-relay/relay-hybrid-connections-protocol.md) é um recurso de retransmissão do Azure que você pode usar para acessar recursos do aplicativo em outras redes. Ele fornece acesso de seu aplicativo para um ponto de extremidade do aplicativo. Você não pode usá-lo para acessar o aplicativo. Conexões híbridas está disponível para as funções em execução em um [plano do serviço de aplicativo](functions-scale.md#app-service-plan) e uma [ambiente do serviço de aplicativo](../app-service/environment/intro.md).

Conforme usado no Azure Functions, cada conexão híbrida se correlaciona com uma única combinação de host e a porta TCP. Isso significa que o ponto de extremidade da conexão de híbrida pode ser em qualquer sistema operacional e qualquer aplicativo, desde que você está acessando uma porta de escuta TCP. O recurso conexões híbridas não sabe nem se importa com qual é o protocolo de aplicativo ou o que você está acessando. Ele simplesmente fornece acesso à rede.

Para obter mais informações, consulte o [documentação do serviço de aplicativo para as conexões híbridas](../app-service/app-service-hybrid-connections.md), que oferece suporte a funções em um plano do serviço de aplicativo.

## <a name="private-site-access"></a>Acesso a site particular

Acesso ao site privado refere-se para tornar seu aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. Acesso de site privado está disponível apenas com um ambiente de serviço de aplicativo configurado com um balanceador de carga interno (ILB). Para obter mais informações, consulte [criar e usar um balanceador de carga interno com um ambiente de serviço de aplicativo](../app-service/environment/create-ilb-ase.md).

Há muitas maneiras de acessar recursos de rede virtual em outras opções de hospedagem. Mas um ambiente de serviço de aplicativo é a única maneira de permitir que gatilhos para uma função ocorrer em uma rede virtual.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as funções do Azure e de redes: 

* [Siga o tutorial sobre como começar a integração da rede virtual](./functions-create-vnet.md)
* [Leia as perguntas frequentes sobre o acesso à rede de funções](./functions-networking-faq.md)
* [Saiba mais sobre a integração de rede virtual com o aplicativo de serviço/funções](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre redes virtuais no Azure](../virtual-network/virtual-networks-overview.md)
* [Ativar mais recursos de rede e controle com ambientes do serviço de aplicativo](../app-service/environment/intro.md)
* [Conectar-se aos recursos individuais locais sem alterações de firewall usando conexões híbridas](../app-service/app-service-hybrid-connections.md)
