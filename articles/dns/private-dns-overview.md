---
title: "Usando o DNS do Azure para domínios privados | Microsoft Docs"
description: "Visão geral do serviço de hospedagem de DNS privado no Microsoft Azure."
services: dns
documentationcenter: na
author: garbrad
manager: 
editor: 
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/04/2017
ms.author: garbrad
ms.openlocfilehash: ed47a9d850995aaf9e78bfde8b6a5fd80dc36918
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-azure-dns-for-private-domains"></a>Usando o DNS do Azure para domínios privados
O Sistema de Nomes de Domínio ou DNS, é responsável por converter (ou resolver) um nome de serviço em seu endereço IP. O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo a resolução de nomes usando a infraestrutura do Microsoft Azure.  Além dos domínios DNS para a Internet, o DNS do Azure agora também dá suporte a domínios DNS privados como um recurso de versão prévia.  
 
O DNS do Azure fornece um serviço DNS confiável e seguro para gerenciar e resolver nomes de domínio em uma rede virtual sem a necessidade de adicionar uma solução DNS personalizada. As zonas DNS privadas permitem que você use seus próprios nomes de domínio personalizados em vez dos nomes fornecidos pelo Azure atualmente disponíveis.  O uso de nomes de domínio personalizados ajuda você a personalizar a sua arquitetura de rede virtual para melhor satisfazer às necessidades da sua organização. Esse uso fornece a resolução de nomes para VMs em uma rede virtual e entre redes virtuais. Além disso, você pode configurar nomes de zonas com uma exibição de omissão de rotas, permitindo que zonas DNS públicas e privadas compartilhem o mesmo nome.

![Visão geral de DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="benefits"></a>Benefícios

* **Remove a necessidade de soluções personalizadas de DNS.** Anteriormente, muitos clientes criaram soluções personalizadas de DNS para gerenciar zonas DNS em suas redes virtuais.  O gerenciamento de zona DNS agora pode ser feito usando a infraestrutura nativa do Azure, o que remove a sobrecarga de criar e gerenciar soluções personalizadas de DNS.

* **Usar todos os tipos de registros DNS comuns.**  O DNS do Azure dá suporte a registros A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT.

* **Gerenciamento automático de registro de nome do host.** Junto com a hospedagem de seus registros DNS personalizados, o Azure manterá automaticamente os registros de nome do host das VMs em redes virtuais especificadas.  Isso permite que você otimize os nomes de domínio que você usa sem a necessidade de criar soluções personalizadas de DNS ou modificar o aplicativo.

* **Resolução de nome do host entre redes virtuais.** Ao contrário dos nomes de host fornecidos pelo Azure, as zonas DNS privadas podem ser compartilhadas entre redes virtuais.  Essa capacidade simplifica os cenários entre redes e de descoberta de serviço como o emparelhamento de rede virtual.

* **Ferramentas familiares e experiência do usuário.** Para reduzir a curva de aprendizado, essa nova oferta usa as ferramentas de DNS do Azure já bem estabelecidas (PowerShell, modelos do Resource Manager, API REST) e o suporte será adicionado ao portal e à CLI assim que possível.

* **Suporte a omissão de rotas DNS.** O DNS do Azure permite que você crie zonas com o mesmo nome que resolvem em diferentes respostas de dentro de uma rede virtual e da Internet pública.  Um cenário típico para omissão de rotas DNS é o fornecimento de uma versão dedicada de um serviço para uso dentro de sua rede virtual.


## <a name="pricing"></a>Preços

As zonas DNS privadas serão gratuitas durante a versão prévia gerenciada. No comunicado da disponibilidade geral, esse recurso usará um modelo de preço baseado no uso, semelhante à oferta do DNS do Azure existente. 


## <a name="next-steps"></a>Próximas etapas

Saiba como [criar uma zona DNS privada](./private-dns-getstarted-powershell.md) no DNS do Azure.

Saiba mais sobre as zonas e registros DNS visitando: [Visão geral de zonas e registros DNS](dns-zones-records.md).

Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.

