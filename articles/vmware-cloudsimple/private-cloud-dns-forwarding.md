---
title: Solução VMware do Azure-encaminhamento de DNS da nuvem privada para o local
description: Descreve como habilitar o servidor DNS da nuvem privada do CloudSimple para encaminhar a pesquisa de recursos locais
author: Ajayan1008
ms.author: v-hborys
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3481e21a93fbf5f658c5ca00513ea179c8f95400
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899194"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>Habilitar servidores DNS da nuvem privada do CloudSimple para encaminhar a pesquisa DNS de recursos locais para seus servidores DNS

Os servidores DNS da nuvem privada podem encaminhar a pesquisa de DNS para todos os recursos locais para seus servidores DNS.  Habilitar a pesquisa permite que os componentes de vSphere de nuvem privada pesquisem quaisquer serviços em execução no seu ambiente local e se comuniquem com eles usando nomes de domínio totalmente qualificados (FQDN).

## <a name="scenarios"></a>Cenários 

O encaminhamento de pesquisa de DNS para seu servidor DNS local permite que você use sua nuvem privada para os seguintes cenários:

* Use a nuvem privada como uma configuração de recuperação de desastre para sua solução VMware local
* Usar Active Directory local como uma fonte de identidade para sua nuvem privada vSphere
* Usar o HCX para migrar máquinas virtuais do local para a nuvem privada

## <a name="before-you-begin"></a>Antes de começar

Uma conexão de rede deve estar presente em sua rede de nuvem privada para sua rede local para que o encaminhamento de DNS funcione.  Você pode configurar a conexão de rede usando:

* [Conectar de local para CloudSimple usando o ExpressRoute](on-premises-connection.md)
* [Configurar um gateway de VPN site a site](./vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

As portas de firewall devem ser abertas nesta conexão para que o encaminhamento de DNS funcione.  As portas usadas são a porta TCP 53 ou a porta UDP 53.

> [!NOTE]
> Se você estiver usando VPN site a site, a sub-rede do servidor DNS local deverá ser adicionada como parte dos prefixos locais.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Solicitar encaminhamento de DNS da nuvem privada para o local

Para habilitar o encaminhamento de DNS da nuvem privada para o local, envie uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), fornecendo as informações a seguir.

* Tipo de problema: **técnico**
* Assinatura: **assinatura em que o serviço CloudSimple está implantado**
* Serviço: **solução VMware por CloudSimple**
* Tipo de problema: **consultoria ou como fazer...**
* Subtipo de problema: **precisa de ajuda com o NW**
* Forneça o nome de domínio do seu domínio local no painel de detalhes.
* Forneça a lista de seus servidores DNS locais para os quais a pesquisa será encaminhada de sua nuvem privada no painel de detalhes.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre a configuração de firewall local](on-premises-firewall-configuration.md)
* [Configuração do servidor DNS local](on-premises-dns-setup.md)
