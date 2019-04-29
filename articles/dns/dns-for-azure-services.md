---
title: Use o DNS do Azure com outros serviços do Azure | Microsoft Docs
description: Entenda como usar o DNS do Azure para resolver nomes para outros serviços do Azure
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: victorh
ms.openlocfilehash: dcf209d2036d2686bea0b51380db3cd2473d04a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293174"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Como o Azure DNS funciona com outros serviços do Azure

O DNS do Azure é um serviço hospedado de gerenciamento de DNS e resolução de nomes. Você pode usá-lo para criar nomes DNS públicos para outros aplicativos e serviços implantados no Azure. Criar um nome para um serviço do Azure no seu domínio personalizado é simples. Você acabou de adicionar um registro do tipo correto para o seu serviço.

* Para endereços IP alocados dinamicamente, você pode criar um registro DNS CNAME que mapeia para o nome DNS que o Azure criou para seu serviço. Padrões DNS o impedem de usar um registro CNAME para o apex da zona. Você pode usar um registro de alias em vez disso. Para obter mais informações, confira [Tutorial: Configurar um registro de alias para se referir a um endereço IP público do Azure](tutorial-alias-pip.md).
* Para endereços IP alocados estaticamente, você pode criar um registro DNS A usando qualquer nome, o que inclui um *nome de domínio sem cobertura* no ápice da zona.

A tabela a seguir descreve os tipos de registro com suporte que você pode usar para vários serviços do Azure. Como a tabela mostra, o DNS do Azure oferece suporte apenas a registros DNS para recursos de rede voltados para a Internet. O DNS do Azure não pode ser usado para resolução de nomes de endereços internos e privados.

| Serviço do Azure | interface de rede | DESCRIÇÃO |
| --- | --- | --- |
| Gateway de Aplicativo do Azure |[IP público de front-end](dns-custom-domain.md#public-ip-address) |Você pode criar um registro CNAME ou DNS A. |
| Azure Load Balancer |[IP público de front-end](dns-custom-domain.md#public-ip-address) |Você pode criar um registro CNAME ou DNS A. O Load Balancer pode ter um endereço IP público IPv6 que é atribuído dinamicamente. Crie um registro CNAME para um endereço IPv6. |
| Gerenciador de Tráfego do Azure |Nome público |Você pode criar um registro de alias que mapeia para o nome trafficmanager.net atribuído ao seu perfil do Gerenciador de Tráfego. Para obter mais informações, confira [Tutorial: Configurar um registro de alias para dar suporte a nomes de domínio com o Gerenciador de tráfego do apex](tutorial-alias-tm.md). |
| Serviços de nuvem do Azure |[IP público](dns-custom-domain.md#public-ip-address) |Para endereços IP alocados estaticamente, você pode criar um registro DNS A. Para endereços IP alocados dinamicamente, você deve criar um registro CNAME que mapeia para o nome *cloudapp.net* .|
| Serviço de aplicativo do Azure | [IP externo](dns-custom-domain.md#app-service-web-apps) |Para endereços IP externos, você pode criar um registro DNS A. Caso contrário, você deve criar um registro CNAME que mapeia para o nome azurewebsites.net. Para obter mais informações, consulte [Mapear um nome de domínio personalizado para um aplicativo do Azure](../app-service/app-service-web-tutorial-custom-domain.md). |
| VMs do Azure Resource Manager |[IP público](dns-custom-domain.md#public-ip-address) |As VMs do Resource Manager podem ter endereços IP públicos. Uma VM com um endereço IP público também pode estar por trás de um balanceador de carga. Você pode criar um registro DNS A, CNAME ou alias para o endereço público. Você pode usar esse nome personalizado para ignorar o VIP no balanceador de carga. |
| VMs clássicas |[IP público](dns-custom-domain.md#public-ip-address) |As VMs clássicas criadas usando o PowerShell ou CLI podem ser configuradas com um endereço virtual dinâmico ou estático (reservado). Você pode criar um CNAME de DNS ou um registro A, respectivamente. |
