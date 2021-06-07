---
title: Solução do Azure VMware por conexão CloudSimple-local usando o ExpressRoute
description: Descreve como solicitar uma conexão local usando o ExpressRoute da rede CloudSimple Region
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 27d1d9e54838d9f45a28d634fa1c24fb8785aee1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899228"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Conectar de local para CloudSimple usando o ExpressRoute

Se você já tiver uma conexão do ExpressRoute do Azure de um local externo (como no local) para o Azure, você poderá conectá-lo ao seu ambiente CloudSimple. Você pode fazer isso por meio de um recurso do Azure que permite que dois circuitos do ExpressRoute se conectem entre si. Esse método estabelece uma conexão segura, privada, de alta largura de banda e baixa latência entre os dois ambientes.

[![Conexão do ExpressRoute local-Alcance Global](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Antes de começar

Um bloco de endereço de rede **/29** é necessário para estabelecer alcance global conexão do local.  O espaço de endereço/29 é usado para a rede de trânsito entre circuitos do ExpressRoute.  A rede de trânsito não deve se sobrepor a nenhuma de suas redes virtuais do Azure, redes locais ou redes de nuvem privada CloudSimple.

## <a name="prerequisites"></a>Pré-requisitos

* Um circuito do Azure ExpressRoute é necessário antes que você possa estabelecer a conexão entre o circuito e as redes de nuvem privada CloudSimple.
* Um usuário é necessário com privilégios para criar chaves de autorização em um circuito do ExpressRoute.

## <a name="scenarios"></a>Cenários

Conectar sua rede local à sua rede de nuvem privada permite que você use a nuvem privada de várias maneiras, incluindo os seguintes cenários:

* Acesse sua rede de nuvem privada sem criar uma conexão VPN site a site.
* Use seu Active Directory local como uma fonte de identidade em sua nuvem privada.
* Migre máquinas virtuais em execução localmente para sua nuvem privada.
* Use sua nuvem privada como parte de uma solução de recuperação de desastre.
* Consuma recursos locais em suas VMs de carga de trabalho de nuvem privada.

## <a name="connecting-expressroute-circuits"></a>Conectando circuitos do ExpressRoute

Para estabelecer a conexão do ExpressRoute, você deve criar uma autorização no circuito do ExpressRoute e fornecer as informações de autorização para CloudSimple.


### <a name="create-expressroute-authorization"></a>Criar autorização de ExpressRoute

1. Entre no portal do Azure.

2. Na barra de pesquisa superior, pesquise o **circuito do expressroute** e clique em **circuitos do expressroute** em **Serviços**.
    [![Circuitos do ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Selecione o circuito do ExpressRoute que você pretende conectar à sua rede CloudSimple.

4. Na página ExpressRoute, clique em **autorizações**, insira um nome para a autorização e clique em **salvar**.
    [![Autorização de circuito do ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Copie a ID de recurso e a chave de autorização clicando no ícone de cópia. Cole a ID e a chave em um arquivo de texto.
    [![Cópia de autorização de circuito do ExpressRoute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > A **ID do recurso** deve ser copiada da interface do usuário e deve estar no formato ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` quando você fornecê-la para dar suporte ao.

6. Arquivo um tíquete com <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">suporte</a> para a conexão a ser criada.
    * Tipo de problema: **técnico**
    * Assinatura: **assinatura em que o serviço CloudSimple está implantado**
    * Serviço: **solução VMware por CloudSimple**
    * Tipo de problema: **solicitação de serviço**
    * Subtipo de problema: **criar conexão de ExpressRoute para local**
    * Forneça a ID de recurso e a chave de autorização que você copiou e salvou no painel de detalhes.
    * Forneça um/29 espaço de endereço de rede para a rede de trânsito.
    * Você está enviando a rota padrão por meio do ExpressRoute?
    * O tráfego de nuvem privada deve usar a rota padrão enviada por meio do ExpressRoute?

    > [!IMPORTANT]
    > O envio de rota padrão permite enviar todo o tráfego da Internet da nuvem privada usando sua conexão de Internet local.  Para desabilitar a rota padrão configurada na nuvem privada e usar a rota padrão de conexão local, forneça os detalhes no tíquete de suporte.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre as conexões de rede do Azure](cloudsimple-azure-network-connection.md)  
