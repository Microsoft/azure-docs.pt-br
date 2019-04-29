---
title: Perguntas frequentes sobre a rede no Azure Functions
description: Respostas para algumas das perguntas mais comuns e cenários de rede com o Azure Functions.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 3cf6a0d080e2d8cafcab8e69a614b59a470c7aba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637039"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Perguntas frequentes sobre a rede no Azure Functions

Este artigo lista as perguntas frequentes sobre a rede no Azure Functions. Para obter uma visão mais abrangente, consulte [as opções de rede de funções](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Como definir um endereço IP estático em funções?

Implantando uma função em um ambiente de serviço de aplicativo atualmente é a única maneira de ter um IP estático de entrada e saído para sua função. Para obter detalhes sobre como usar um ambiente de serviço de aplicativo, comece com o artigo [criar e usar um balanceador de carga interno com um ambiente de serviço de aplicativo](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Como restringir o acesso à internet para minha função?

Você pode restringir o acesso à internet de duas maneiras:

* [Restrições de IP](../app-service/app-service-ip-restrictions.md): Restringir o tráfego de entrada para seu aplicativo de funções por intervalo de IP.
* Remoção de todos os gatilhos HTTP. Para alguns aplicativos, é suficiente para simplesmente evitar gatilhos HTTP e usar qualquer outra fonte de evento para disparar sua função.

Tenha em mente que o editor do portal do Azure requer acesso direto à sua função em execução. Alterações de código por meio do portal do Azure exigirá que o dispositivo que você está usando para navegarem pelo portal para ter sua lista de permissões IP. Mas você ainda pode usar qualquer coisa na guia de recursos de plataforma com restrições de rede em vigor.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Como restringir o meu aplicativo de função a uma rede virtual?

A única maneira de restringir totalmente uma função, de modo que todo o tráfego flui através de uma rede virtual é usar um ambiente de serviço de aplicativo internamente com balanceamento de carga. Essa opção implanta o seu site em uma infraestrutura dedicada dentro de uma rede virtual e envia todos os disparadores e o tráfego pela rede virtual. 

Para obter detalhes sobre como usar um ambiente de serviço de aplicativo, comece com o artigo [criar e usar um balanceador de carga interno com um ambiente de serviço de aplicativo](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Como acessar recursos em uma rede virtual de um aplicativo de função?

Você pode acessar recursos em uma rede virtual de uma função em execução usando a integração de rede virtual. Para obter mais informações, consulte [integração de rede Virtual](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Como fazer para acessar recursos protegidos por pontos de extremidade de serviço?

Usando a integração de rede virtual (atualmente em versão prévia), você pode acessar recursos protegidos de ponto de extremidade de serviço de uma função em execução. Para obter mais informações, consulte [visualizar a integração de rede virtual](functions-networking-options.md#preview-version-of-virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Como é possível disparar uma função de um recurso em uma rede virtual?

Você pode disparar uma função de um recurso em uma rede virtual somente ao implantar seu aplicativo de funções para um ambiente de serviço de aplicativo. Para obter detalhes sobre como usar um ambiente de serviço de aplicativo, consulte [criar e usar um balanceador de carga interno com um ambiente de serviço de aplicativo](../app-service/environment/create-ilb-ase.md).


## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Como posso implantar meu aplicativo de função em uma rede virtual?

Implantando em um ambiente de serviço de aplicativo é a única maneira de criar um aplicativo de função que está inteiramente dentro de uma rede virtual. Para obter detalhes sobre como usar um balanceador de carga interno com um ambiente de serviço de aplicativo, comece com o artigo [criar e usar um balanceador de carga interno com um ambiente de serviço de aplicativo](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Para cenários em que você precisa apenas unidirecional acesso aos recursos da rede virtual, ou menos isolamento de rede abrangente, consulte a [funções de visão geral da rede](functions-networking-options.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a rede e funções: 

* [Siga o tutorial sobre como começar a integração da rede virtual](./functions-create-vnet.md)
* [Saiba mais sobre as opções de rede no Azure Functions](./functions-networking-options.md)
* [Saiba mais sobre a integração de rede virtual com o serviço de aplicativo e funções](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre redes virtuais no Azure](../virtual-network/virtual-networks-overview.md)
* [Ativar mais recursos de rede e controle com ambientes do serviço de aplicativo](../app-service/environment/intro.md)
