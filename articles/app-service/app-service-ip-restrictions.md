---
title: Restringir o acesso - serviço de aplicativo do Azure | Microsoft Docs
description: Como usar restrições de acesso com o serviço de aplicativo do Azure
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d3c547fbc09aeb034df5b7ed579639e1ff4bc0b4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705796"
---
# <a name="azure-app-service-access-restrictions"></a>Restrições de acesso do serviço de aplicativo do Azure #

Restrições de acesso permitem que você defina uma lista ordenada de permissão/negação prioridade que controla o acesso à rede para seu aplicativo. A lista pode incluir endereços IP ou sub-redes de rede Virtual do Azure. Quando há uma ou mais entradas, há, em seguida, implícito "negar tudo" que existe no final da lista.

O recurso de restrições de acesso funciona com todas as cargas de trabalho hospedado incluindo; o serviço de aplicativo aplicativos Web, aplicativos de API, aplicativos do Linux, aplicativos de contêiner do Linux e funções.

Quando é feita uma solicitação para seu aplicativo, o endereço de é avaliado em relação a regras de endereço IP em sua lista de restrições de acesso. Se o endereço de estiver em uma sub-rede que é configurada com pontos de extremidade de serviço à Microsoft. Web, em seguida, a sub-rede de origem é comparada com as regras de rede virtual na sua lista de restrições de acesso. Se o endereço não é permitido o acesso com base nas regras na lista, o serviço responderá com um código de status [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

O recurso de restrições de acesso é implementado de funções de front-end do serviço de aplicativo, que são upstream dos hosts de trabalho em que seu código é executado. Portanto, as restrições de acesso são efetivamente as ACLs de rede.

A capacidade de restringir o acesso ao seu aplicativo web de uma rede Virtual do Azure (VNet) é chamada [pontos de extremidade de serviço][serviceendpoints]. Pontos de extremidade de serviço permitem restringir o acesso a um serviço de multilocatário de subredes selecionadas. Ele deve ser habilitado no lado do sistema de rede, bem como o serviço que ela está sendo habilitada com. Ele não funciona para restringir o tráfego para aplicativos que são hospedados em um ambiente de serviço de aplicativo.  Se você estiver em um ambiente de serviço de aplicativo, você pode controlar o acesso ao seu aplicativo com regras de endereço IP.

![fluxo de restrições de acesso](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Adicionar e editar regras de restrição de acesso no portal ##

Para adicionar uma regra de restrição de acesso ao seu aplicativo, use o menu abrir **rede**>**restrições de acesso** e clique em **configurar restrições de acesso**

![Opções de rede do Serviço de Aplicativo do Azure](media/app-service-ip-restrictions/access-restrictions.png)  

Na interface de usuário de restrições de acesso, você pode examinar a lista de regras de restrição de acesso definida para o aplicativo.

![restrições de acesso de lista](media/app-service-ip-restrictions/access-restrictions-browse.png)

A lista mostrará todas as restrições atuais que estão em seu aplicativo. Se você tiver uma restrição de rede virtual em seu aplicativo, a tabela mostrará se os pontos de extremidade de serviço estão habilitados para Microsoft. Web. Quando não há nenhuma restrição definida em seu aplicativo, seu aplicativo poderá ser acessado de qualquer lugar.  

## <a name="adding-ip-address-rules"></a>Adicionando regras de endereço IP

Você pode clicar em **[+] adicionar** para adicionar uma nova regra de restrição de acesso. Depois de adicionar uma regra, entrará em vigor imediatamente. As regras são impostas em ordem de prioridade a partir do número mais baixo e para cima. Há uma negação implícita tudo o que está em vigor depois que você adicionar até mesmo uma única regra.

Ao criar uma regra, você deve selecionar Permitir/Negar e também o tipo de regra. Você também deve fornecer o valor de prioridade e o que você está restringindo o acesso aos.  Opcionalmente, você pode adicionar um nome e descrição para a regra.  

![Adicionar uma regra de restrição de acesso IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Para definir um endereço IP com base em regra, selecione um tipo de IPv4 ou IPv6. Notação de endereço IP deve ser especificada na notação de CIDR de endereços IPv4 e IPv6. Para especificar um endereço exato, você pode usar algo como 1.2.3.4/32 onde os quatro primeiros octetos representam o endereço IP e /32 é a máscara. A notação de CIDR de IPv4 para todos os endereços é 0.0.0.0/0. Para saber mais sobre a notação CIDR, leia [Roteamento entre domínios sem classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Pontos de extremidade de serviço

Pontos de extremidade de serviço permite que você restrinja o acesso às sub-redes de rede de virtual do Azure selecionada. Para restringir o acesso a uma sub-rede específica, crie uma regra de restrição com um tipo de rede Virtual. Você pode escolher a assinatura, VNet e sub-rede que deseja permitir ou negar acesso com. Se os pontos de extremidade de serviço não já estarão habilitados com o Microsoft. Web para a sub-rede que você selecionou, ele automaticamente será ativado para você, a menos que você marcar a caixa pedindo para não fazer isso. A situação em que você desejaria para habilitá-lo no aplicativo, mas não a sub-rede está amplamente relacionada à se você tiver as permissões para habilitar os pontos de extremidade de serviço na sub-rede ou não. Se você precisar obter outra pessoa para habilitar pontos de extremidade de serviço na sub-rede, você pode a caixa de seleção e seu aplicativo configurado para pontos de extremidade de serviço em antecipação a ele que está sendo habilitado mais tarde na sub-rede. 

![Adicionar uma regra de restrição de acesso de rede virtual](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Pontos de extremidade de serviço não podem ser usados para restringir o acesso a aplicativos que são executados em um ambiente de serviço de aplicativo. Quando seu aplicativo estiver em um ambiente de serviço de aplicativo, você pode controlar o acesso ao seu aplicativo com regras de acesso IP. 

Com pontos de extremidade de serviço, você pode configurar seu aplicativo com Gateways de aplicativo ou outros dispositivos de WAF. Você também pode configurar aplicativos multicamadas com o back-ends seguro. Para obter mais detalhes sobre algumas das possibilidades, leia [recursos de rede e serviço de aplicativo](networking-features.md).

## <a name="managing-access-restriction-rules"></a>Gerenciando regras de restrição de acesso

Você pode clicar em qualquer linha para editar uma regra de restrição de acesso existente. As edições são eficazes imediatamente, incluindo as alterações na ordem de prioridade.

![Editar uma regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Quando você edita uma regra, você não pode alterar o tipo entre uma regra de endereço IP e uma regra de rede Virtual. 

![Editar uma regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Para excluir uma regra, clique em **...**  em sua regra e clique em **remover**.

![Excluir regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Bloqueio de um único endereço IP ##

Ao adicionar sua primeira regra de restrição de IP, o serviço adicionará um explícito **negar tudo** regra com uma prioridade de 2147483647. Na prática, explícita **negar tudo** regra será a última regra executada e bloqueará o acesso a nenhum endereço IP que não é explicitamente permitido usando uma **permitir** regra.

Para o cenário em que os usuários desejam bloquear explicitamente um único endereço IP ou o bloco de endereço IP, mas permitir que todo o acesso mais, é necessário adicionar uma explícita **permitir que todos os** regra.

![endereço ip único de bloco](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>Site do SCM 

Além de ser capaz de controlar o acesso ao seu aplicativo, você também pode restringir o acesso para o site do scm usado pelo seu aplicativo. O site do scm é web implantar o ponto de extremidade e também o console do Kudu. Separadamente, você pode atribuir as restrições de acesso para o site do scm do aplicativo ou usar o mesmo conjunto para o aplicativo e o site do scm. Quando você marca a caixa para que as mesmas restrições que seu aplicativo, tudo o que é blanked-out. Se você desmarcar a caixa, são aplicadas a todas as configurações que você tinha anteriormente no site do scm. 

![restrições de acesso de lista](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Manipulação programática das regras de restrição de acesso ##

Atualmente, não há nenhuma CLI ou o PowerShell para o novo recurso de restrições de acesso, mas os valores podem ser definidos manualmente com um [API REST do Azure](https://docs.microsoft.com/rest/api/azure/) operação PUT na configuração de aplicativo no Gerenciador de recursos. Por exemplo, você pode usar resources.azure.com e editar o bloco ipSecurityRestrictions para adicionar o JSON necessário.

O local para obter essas informações no Gerenciador de Recursos é:

management.azure.com/subscriptions/**subscription ID**/resourceGroups/**resource groups**/providers/Microsoft.Web/sites/**web app name**/config/web?api-version=2018-02-01

A sintaxe JSON para o exemplo anterior é:

    {
      "properties": {
        "ipSecurityRestrictions": [
          {
            "ipAddress": "122.133.144.0/24",
            "action": "Allow",
            "tag": "Default",
            "priority": 100,
            "name": "IP example rule"
          }
        ]
      }
    }

## <a name="function-app-ip-restrictions"></a>Restrições de IP do aplicativo de função

Restrições de IP estão disponíveis para ambos os aplicativos de funções com a mesma funcionalidade que os planos de serviço de aplicativo. Habilitar restrições de IP desabilitará o editor de códigos de portal para quaisquer IPs não permitidos.

[Saiba mais aqui](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
