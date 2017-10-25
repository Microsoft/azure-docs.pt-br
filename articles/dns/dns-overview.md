---
title: "Visão geral do DNS do Azure | Microsoft Docs"
description: "Visão geral do Serviço de hospedagem de DNS no Microsoft Azure. Hospede seu domínio no Microsoft Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 68747a0d-b358-4b8e-b5e2-e2570745ec3f
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: 3705457e4c90f8869496f7f5177531bd128d1057
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-dns-overview"></a>Visão geral do DNS do Azure

O sistema de nomes de domínio, ou DNS, é responsável por converter (ou seja, resolver) um nome do site ou serviço para seu endereço IP. O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure.

![Visão geral de DNS](./media/dns-overview/scenario.png)

## <a name="features"></a>Recursos

* **Confiabilidade e desempenho** - domínios DNS no DNS do Azure são hospedados na rede global do Azure de servidores de nomes DNS. Podemos usar a rede Anycast, para que cada consulta DNS seja atendida pelo servidor DNS mais próximo disponível. Isso fornece desempenho rápido e alta disponibilidade para seu domínio.

* **Integração perfeita** - o serviço de DNS do Azure pode ser usado para gerenciar os registros DNS para os serviços do Azure e também pode ser usado para fornecer o DNS para os recursos externos. O DNS do Azure é integrado no portal do Azure e usa as mesmas credenciais, cobrança e contrato de suporte que outros serviços do Azure.

* **Segurança** - o serviço de DNS do Azure baseia-se no Azure Resource Manager, Azure Resource Manager. Sendo assim, ele aproveita recursos do Resource Manager, como o controle de acesso baseado em função, os logs de auditoria e o bloqueio de recursos. Seus domínios e registros podem ser gerenciados por meio do portal do Azure, cmdlets do Azure PowerShell e a CLI do Azure de plataforma cruzada. Aplicativos que requerem gerenciamento automático de DNS podem se integrar com o serviço por meio da API REST e dos SDKs.

O DNS do Azure não dá suporte a compra de nomes de domínio. Se você deseja adquirir domínios, será necessário usar um registrador de nomes de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios, em seguida, podem ser hospedados no DNS do Azure para gerenciamento de registros DNS. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.

## <a name="pricing"></a>Preços

O preço do Azure é baseado no número de zonas DNS hospedadas no Azure e o número de consultas DNS. Para saber mais sobre os preços, visite [Preços de DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

## <a name="faq"></a>Perguntas frequentes

Para ver as perguntas frequentes sobre o DNS do Azure, veja o [Perguntas frequentes do DNS do Azure](dns-faq.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as zonas e registros DNS visitando: [Visão geral de zonas e registros DNS](dns-zones-records.md).

Saiba como [criar uma zona DNS](./dns-getstarted-create-dnszone-portal.md) no DNS do Azure.

Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.

