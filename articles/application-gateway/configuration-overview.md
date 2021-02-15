---
title: Visão geral da configuração do gateway de Aplicativo Azure
description: Este artigo descreve como configurar os componentes do gateway de Aplicativo Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 0012ac99600c77dce5940387e1da54f29c3f6ab7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652006"
---
# <a name="application-gateway-configuration-overview"></a>Visão geral da configuração do gateway de aplicativo

Aplicativo Azure gateway consiste em vários componentes que podem ser configurados de várias maneiras para cenários diferentes. Este artigo mostra como configurar cada componente.

![Gráfico de fluxo de componentes do gateway de aplicativo](./media/configuration-overview/configuration-overview1.png)

Esta imagem ilustra um aplicativo que tem três ouvintes. Os dois primeiros são ouvintes multissite para `http://acme.com/*` e `http://fabrikam.com/*` , respectivamente. Ambos escutam na porta 80. O terceiro é um ouvinte básico que tem terminação de TLS (segurança de camada de transporte) de ponta a ponta, anteriormente conhecido como terminação de protocolo SSL (SSL).

## <a name="infrastructure"></a>Infraestrutura

A infraestrutura do gateway de aplicativo inclui a rede virtual, as sub-redes, os grupos de segurança de rede e as rotas definidas pelo usuário.

Para obter mais informações, consulte [configuração de infraestrutura do gateway de aplicativo](configuration-infrastructure.md).



## <a name="front-end-ip-address"></a>Endereço IP de front-end

Você pode configurar o gateway de aplicativo para ter um endereço IP público, um endereço IP privado ou ambos. Um IP público é necessário quando você hospeda um back-end que os clientes devem acessar pela Internet por meio de um VIP (IP virtual) voltado para a Internet.

Para obter mais informações, consulte [configuração de endereço IP de front-end do gateway de aplicativo](configuration-front-end-ip.md).

## <a name="listeners"></a>Ouvintes

Um ouvinte é uma entidade lógica que verifica as solicitações de conexão de entrada usando a porta, o protocolo, o host e o endereço IP. Ao configurar o ouvinte, você deve inserir valores para eles que correspondam aos valores correspondentes na solicitação de entrada no gateway.

Para obter mais informações, consulte [configuração do ouvinte do gateway de aplicativo](configuration-listeners.md).

## <a name="request-routing-rules"></a>Regras de roteamento de solicitação

Ao criar um gateway de aplicativo usando o portal do Azure, você cria uma regra padrão (*rule1*). Essa regra associa o ouvinte padrão (*appGatewayHttpListener*) ao pool de back-ends padrão (*appGatewayBackendPool*) e as configurações de http de back-end padrão (*appgatewaybackendhttp*). Depois de criar o gateway, você pode editar as configurações da regra padrão ou criar novas regras.

Para obter mais informações, consulte [regras de roteamento de solicitação do gateway de aplicativo](configuration-request-routing-rules.md).

## <a name="http-settings"></a>Configurações de HTTP

O gateway de aplicativo roteia o tráfego para os servidores back-end usando a configuração que você especificar aqui. Depois de criar uma configuração de HTTP, você deve associá-la a uma ou mais regras de roteamento de solicitação.

Para obter mais informações, consulte [configuração de configurações de http do gateway de aplicativo](configuration-http-settings.md).

## <a name="back-end-pool"></a>Pool de back-end

Você pode apontar um pool de back-ends para quatro tipos de membros de back-end: uma máquina virtual específica, um conjunto de dimensionamento de máquinas virtuais, um endereço IP/FQDN ou um serviço de aplicativo. 

Depois de criar um pool de back-end, você deve associá-lo a uma ou mais regras de roteamento de solicitação. Você também deve configurar investigações de integridade para cada pool de back-ends no seu gateway de aplicativo. Quando uma condição de regra de roteamento de solicitação é atendida, o gateway de aplicativo encaminha o tráfego para os servidores íntegros (conforme determinado pelas investigações de integridade) no pool de back-ends correspondente.

## <a name="health-probes"></a>Investigações de integridade

Um gateway de aplicativo monitora a integridade de todos os recursos em seu back-end por padrão. Mas é altamente recomendável que você crie uma investigação personalizada para cada configuração de HTTP de back-end para obter maior controle sobre o monitoramento de integridade. Para saber como configurar uma investigação personalizada, consulte [configurações personalizadas de investigação de integridade](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Depois de criar uma investigação de integridade personalizada, você precisa associá-la a uma configuração de HTTP de back-end. Uma investigação personalizada não monitorará a integridade do pool de back-end, a menos que a configuração de HTTP correspondente esteja explicitamente associada a um ouvinte usando uma regra.

## <a name="next-steps"></a>Próximas etapas

Agora que você conhece os componentes do gateway de aplicativo, você pode:

- [Criar um gateway de aplicativo no portal do Azure](quick-create-portal.md)
- [Criar um gateway de aplicativo usando o PowerShell](quick-create-powershell.md)
- [Criar um gateway de aplicativo usando a CLI do Azure](quick-create-cli.md)
