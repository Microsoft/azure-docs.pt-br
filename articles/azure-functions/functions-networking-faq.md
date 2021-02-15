---
title: Perguntas frequentes sobre rede em Azure Functions
description: Respostas para algumas das perguntas e cenários mais comuns de rede com Azure Functions.
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.reviewer: glenga
ms.openlocfilehash: 3e8a992aac95b6c2688cb45aa980bf0b01883a53
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578222"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Perguntas frequentes sobre rede em Azure Functions

Este artigo lista as perguntas frequentes sobre a rede em Azure Functions. Para obter uma visão geral mais abrangente, consulte [funções de rede do Functions](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Como fazer definir um IP estático em funções?

A implantação de uma função em um Ambiente do Serviço de Aplicativo é atualmente a única maneira de ter um IP de entrada e saída estático para sua função. Para obter detalhes sobre como usar um Ambiente do Serviço de Aplicativo, comece com o artigo [criar e usar um balanceador de carga interno com um ambiente do serviço de aplicativo](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Como fazer restringir o acesso à Internet à minha função?

Você pode restringir o acesso à Internet de duas maneiras:

* [Restrições de IP](../app-service/app-service-ip-restrictions.md): restrinja o tráfego de entrada para seu aplicativo de funções por intervalo de IP.
    * Em restrições de IP, você também pode configurar [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md), que restringem sua função para aceitar somente o tráfego de entrada de uma rede virtual específica.
* Remoção de todos os gatilhos HTTP. Para alguns aplicativos, é suficiente simplesmente evitar gatilhos HTTP e usar qualquer outra origem de evento para disparar sua função.

Tenha em mente que o editor de portal do Azure requer acesso direto à sua função em execução. Qualquer alteração de código através da portal do Azure exigirá que o dispositivo que você está usando para procurar o portal tenha seu IP adicionado à lista aprovada. Mas você ainda pode usar qualquer coisa na guia recursos da plataforma com restrições de rede em vigor.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Como fazer restringir meu aplicativo de funções a uma rede virtual?

Você pode restringir o tráfego de **entrada** para um aplicativo de funções a uma rede virtual usando [pontos de extremidade de serviço](./functions-networking-options.md#use-service-endpoints). Essa configuração ainda permite que o aplicativo de funções faça chamadas de saída para a Internet.

Para restringir completamente uma função de forma que todo o tráfego flua por meio de uma rede virtual, você pode usar [pontos de extremidade privados](./functions-networking-options.md#private-endpoint-connections) com integração de rede virtual de saída ou um ambiente do serviço de aplicativo.

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Como posso acessar recursos em uma rede virtual por meio de um aplicativo de funções?

Você pode acessar recursos em uma rede virtual de uma função em execução usando a integração de rede virtual. Para obter mais informações, consulte [integração de rede virtual](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Como fazer acessar recursos protegidos por pontos de extremidade de serviço?

Usando a integração de rede virtual, você pode acessar recursos protegidos por ponto de extremidade de serviço de uma função em execução. Para obter mais informações, consulte [integração de rede virtual](functions-networking-options.md#virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Como posso disparar uma função de um recurso em uma rede virtual?

Você pode permitir que gatilhos HTTP sejam chamados por meio de uma rede virtual usando pontos de extremidade de [serviço](./functions-networking-options.md#use-service-endpoints) ou conexões de [ponto de extremidades privadas](./functions-networking-options.md#private-endpoint-connections). 

Você também pode disparar uma função de todos os outros recursos em uma rede virtual implantando seu aplicativo de funções em um plano Premium, plano do serviço de aplicativo ou Ambiente do Serviço de Aplicativo. Consulte [gatilhos de rede virtual não http](./functions-networking-options.md#virtual-network-triggers-non-http) para obter mais informações

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Como posso implantar meu aplicativo de funções em uma rede virtual?

A implantação em um Ambiente do Serviço de Aplicativo é a única maneira de criar um aplicativo de funções que está totalmente dentro de uma rede virtual. Para obter detalhes sobre como usar um balanceador de carga interno com uma Ambiente do Serviço de Aplicativo, comece com o artigo [criar e usar um balanceador de carga interno com um ambiente do serviço de aplicativo](../app-service/environment/create-ilb-ase.md).

Para cenários em que você precisa apenas de acesso unidirecional a recursos de rede virtual ou um isolamento de rede menos abrangente, consulte a [visão geral do Functions](functions-networking-options.md)Networking.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre rede e funções: 

* [Siga o tutorial sobre como começar com a integração de rede virtual](./functions-create-vnet.md)
* [Saiba mais sobre as opções de rede no Azure Functions](./functions-networking-options.md)
* [Saiba mais sobre a integração de rede virtual com o serviço de aplicativo e as funções](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre as redes virtuais no Azure](../virtual-network/virtual-networks-overview.md)
* [Habilitar mais recursos de rede e de controle com Ambiente do Serviço de Aplicativo](../app-service/environment/intro.md)
