---
title: Restringir IPs de cliente – Serviço de Aplicativo do Azure | Microsoft Docs
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
ms.date: 07/30/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bb6ab29f02282a394e3f93e41682ceaec5208b75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853167"
---
# <a name="azure-app-service-static-access-restrictions"></a>Restrições de acesso estático do serviço de aplicativo do Azure #

Restrições de acesso permitem que você defina uma prioridade ordenados Permitir/Negar lista de endereços IP que têm permissão para acessar seu aplicativo. A lista de permissões pode incluir endereços IPv4 e IPv6. Quando há uma ou mais entradas, há, em seguida, uma negação implícita de tudo o que existe no final da lista.

O recurso de restrições de acesso funciona com todos os que serviço de aplicativo hospedado cargas de trabalho, que incluem; aplicativos Web, aplicativos de API, aplicativos do Linux, aplicativos de contêiner do Linux e funções.

Quando uma solicitação é feita ao seu aplicativo, o endereço de IP é buscado na lista de restrições de acesso. Se o endereço não é permitido o acesso com base nas regras na lista, o serviço responderá com um código de status [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

O recurso de restrições de acesso é implementado de funções de front-end do serviço de aplicativo, que são upstream dos hosts de trabalho em que seu código é executado. Portanto, as restrições de acesso são efetivamente as ACLs de rede.  

![fluxo de restrições de acesso](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Por um período, o recurso de restrições de acesso no portal do era uma camada sobre a funcionalidade de ipSecurity no IIS. O recurso de restrições de acesso atual é diferente. Você ainda pode configurar ipSecurity dentro de seu aplicativo Web. config, mas as regras de restrições de acesso com base em front-end serão aplicadas antes que qualquer tráfego alcance o IIS.

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Adicionar e editar regras de restrição de acesso no portal ##

Para adicionar uma regra de restrição de acesso ao seu aplicativo, use o menu abrir **rede**>**restrições de acesso** e clique em **configurar restrições de acesso**

![Opções de rede do Serviço de Aplicativo do Azure](media/app-service-ip-restrictions/ip-restrictions.png)  

Na interface de usuário de restrições de acesso, você pode examinar a lista de regras de restrição de acesso definida para o aplicativo.

![restrições de acesso de lista](media/app-service-ip-restrictions/ip-restrictions-browse.png)

Se as regras foram configuradas como nesta imagem, seu aplicativo só deve aceitar o tráfego do 131.107.159.0/24 e seria negado de qualquer outro endereço IP.

Você pode clicar em **[+] adicionar** para adicionar uma nova regra de restrição de acesso. Depois de adicionar uma regra, entrará em vigor imediatamente. As regras são impostas em ordem de prioridade a partir do número mais baixo e para cima. Há uma negação implícita tudo o que está em vigor depois que você adicionar até mesmo uma única regra.

![Adicionar uma regra de restrição de acesso](media/app-service-ip-restrictions/ip-restrictions-add.png)

Notação de endereço IP deve ser especificada na notação de CIDR de endereços IPv4 e IPv6. Para especificar um endereço exato, você pode usar algo como 1.2.3.4/32 onde os quatro primeiros octetos representam o endereço IP e /32 é a máscara. A notação de CIDR de IPv4 para todos os endereços é 0.0.0.0/0. Para saber mais sobre a notação CIDR, leia [Roteamento entre domínios sem classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

Você pode clicar em qualquer linha para editar uma regra de restrição de acesso existente. As edições são eficazes imediatamente, incluindo as alterações na ordem de prioridade.

![Editar uma regra de restrição de acesso](media/app-service-ip-restrictions/ip-restrictions-edit.png)

Para excluir uma regra, clique em **...**  em sua regra e clique em **remover**.

![Excluir regra de restrição de acesso](media/app-service-ip-restrictions/ip-restrictions-delete.png)

Você também pode restringir o acesso de implantação na próxima guia. Para adicionar/editar/excluir a cada regra, execute a mesma etapa acima.

![restrições de acesso de lista](media/app-service-ip-restrictions/ip-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Manipulação programática das regras de restrição de acesso ##

Atualmente, não há nenhuma CLI ou o PowerShell para o novo recurso de restrições de acesso, mas os valores podem ser definidos manualmente com uma operação PUT na configuração de aplicativo no Gerenciador de recursos. Por exemplo, você pode usar resources.azure.com e editar o bloco ipSecurityRestrictions para adicionar o JSON necessário.

O local para obter essas informações no Gerenciador de Recursos é:

management.azure.com/subscriptions/**subscription ID**/resourceGroups/**resource groups**/providers/Microsoft.Web/sites/**web app name**/config/web?api-version=2018-02-01

A sintaxe JSON para o exemplo anterior é:

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],

## <a name="function-app-ip-restrictions"></a>Restrições de IP do aplicativo de função

Restrições de IP estão disponíveis para ambos os aplicativos de funções com a mesma funcionalidade que os planos de serviço de aplicativo. Observe que habilitar o IP restrições desabilitará o editor de código do portal para quaisquer IPs não permitidos.

[Saiba mais aqui](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)