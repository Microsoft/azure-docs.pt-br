---
title: Configurar zonas de pesquisa inversa para uma verificação de banner de SMTP
titlesuffix: Azure Virtual Network
description: Descreve como configurar zonas de pesquisa inversa para uma verificação de banner de SMTP no Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c8ffadb8d54db0c2a99dc12e45b5990155a0505e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86135066"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Configurar zonas de pesquisa inversa para uma verificação de banner de SMTP

Este artigo descreve como usar uma zona inversa no DNS do Azure e criar um registro DNS (PTR) reverso para verificação de faixa de SMTP.

## <a name="symptom"></a>Sintoma

Se você hospedar um servidor SMTP no Microsoft Azure, poderá receber a seguinte mensagem de erro ao enviar ou receber uma mensagem de servidores de email remotos:

**554: nenhum registro PTR**

## <a name="solution"></a>Solução

Para um endereço IP virtual no Azure, os registros inversos são criados em zonas de domínio pertencentes à Microsoft, não em zonas de domínio personalizado.

Para configurar os registros PTR em zonas pertencentes à Microsoft, use a propriedade -ReverseFqdn no recurso PublicIpAddress. Para obter mais informações, veja [Configurar DNS reverso para serviços hospedados no Azure](../dns/dns-reverse-dns-for-azure-services.md).

Quando você configurar os registros PTR, verifique se o endereço IP e o FQDN reverso pertencem à assinatura. Se você tentar definir um FQDN reverso que não pertença à assinatura, receberá a seguinte mensagem de erro:

```output
Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                    
1) ReverseFqdn matches fqdn of any public ip resource under the subscription;
2) ReverseFqdn resolves to the fqdn (through CName records chain) of any public ip resource under the subscription;
3) It resolves to the ip address (through CName and A records chain) of a static public ip resource under the subscription.
```

Se você alterar manualmente seu banner de SMTP para corresponder ao nosso FQDN padrão inverso, o servidor de email remoto ainda poderá falhar porque ele poderá esperar que o host do banner de SMTP corresponda ao registro MX para o domínio.
