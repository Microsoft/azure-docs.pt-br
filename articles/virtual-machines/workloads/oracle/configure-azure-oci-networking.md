---
title: Conectar o Azure ExpressRoute com a infraestrutura de nuvem da Oracle | Microsoft Docs
description: Conectar-se o ExpressRoute do Azure com a infraestrutura de nuvem da Oracle (OCI) FastConnect para habilitar soluções de aplicativos entre nuvens Oracle
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2019
ms.author: rogirdh
ms.openlocfilehash: 671d7c8eb9f10e346b49056e1cc117c9882bb6e8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707638"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Configurar uma interconexão direta entre o Azure e infraestrutura de nuvem da Oracle  

Para criar uma [multi-nuvem experiência integrada](oracle-oci-overview.md) (visualização), Microsoft e Oracle interconexão direta entre o Azure e infraestrutura de nuvem da Oracle (OCI) por meio da oferta [ExpressRoute](../../../expressroute/expressroute-introduction.md) e [ FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Por meio de interconexão FastConnect e de ExpressRoute, os clientes podem enfrentar baixa latência, alta taxa de transferência, privada conectividade direta entre duas nuvens.

> [!IMPORTANT]
> A conexão entre o Microsoft Azure e o OCI está no estágio de visualização. Para habilitar a conectividade de baixa latência entre o Azure e OCI, a assinatura do Azure e a locação OCI devem ser primeiro na lista de permissões para essa funcionalidade.

A imagem a seguir mostra uma visão geral da interconexão:

![Conexão de rede entre a nuvem](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Pré-requisitos

* Para estabelecer a conectividade entre o Azure e OCI, você deve ter uma assinatura do Azure Active Directory e uma locação OCI Active Directory.

* Conectividade só é possível, em que um local de emparelhamento do ExpressRoute do Azure é em proximidade com ou no mesmo local de emparelhamento FastConnect o OCI. Ver [limitações da visualização](oracle-oci-overview.md#preview-limitations).

* Sua assinatura do Azure deve estar na lista de permissões para esse recurso de visualização. Entre em contato com seu representante da Microsoft para habilitar esse recurso em sua assinatura.

* Sua locação OCI deve estar na lista de permissões para esse recurso de visualização. Para obter detalhes, entre em contato com seu representante da Oracle.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Configurar a conectividade direta entre FastConnect e ExpressRoute

1. Crie um circuito de ExpressRoute padrão em sua assinatura do Azure em um grupo de recursos. 
    * Ao criar o ExpressRoute, escolha **Oracle Cloud FastConnect** como o provedor de serviço. Para criar um circuito do ExpressRoute, consulte o [guia passo a passo](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Um circuito do ExpressRoute do Azure fornece opções de largura de banda granular, enquanto FastConnect dá suporte a 1, 2, 5 ou 10 Gbps. Portanto, é recomendável escolher uma dessas opções de largura de banda correspondente no ExpressRoute.

    ![Criar um circuito do ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Anote o ExpressRoute **chave de serviço**. Você precisa fornecer a chave ao configurar o circuito FastConnect.

    ![Chave de ExpressRoute Service](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Você será cobrado para as cobranças de ExpressRoute, assim que o circuito do ExpressRoute está provisionado (mesmo se o **Status do provedor** é **não provisionado**).

1. Reservar dois espaços de endereço IP privados de /30 cada que não se sobrepõem com sua rede virtual do Azure ou a rede de nuvem virtual OCI espaço de endereço IP. Vamos nos referir ao espaço de endereço IP primeiro como o espaço de endereço principal e o segundo espaço de endereço IP como o espaço de endereços secundários. Anote os endereços, que você precisa ao configurar o circuito FastConnect.
1. Crie um Gateway de roteamento dinâmico (DRG). Isso será necessário durante a criação de seu circuito FastConnect. Para obter mais informações, consulte o [Gateway de roteamento dinâmico](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) documentação.
1. Crie um circuito de FastConnect em seu locatário do Oracle. Para obter mais informações, consulte o [documentação do Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * Em configuração FastConnect, selecione **Microsoft Azure: ExpressRoute** como o provedor.
    * Selecione o Gateway de roteamento dinâmico que você forneceu na etapa anterior.
    * Selecione a largura de banda seja provisionada. Para otimizar o desempenho, a largura de banda deve corresponder a largura de banda selecionada durante a criação do circuito do ExpressRoute.
    * Na **chave do provedor de serviço**, cole a chave de serviço de ExpressRoute.
    * Uso/primeira 30 formar espaço de endereço IP privado em uma etapa anterior para o **endereço de IP primário do BGP** e o segundo/30 espaço de endereço IP privado para o **IP secundário de BGP** endereço.
        * Atribua o primeiro endereço utilizável de dois intervalos para o endereço de IP do BGP Oracle (primário e secundário) e o segundo endereço para o endereço IP do BGP do cliente (de uma perspectiva de FastConnect). O primeiro endereço IP utilizável é o segundo endereço IP/30 (o primeiro endereço IP é reservado pela Microsoft) de espaço de endereço.
    * Clique em **Criar**.
1. Conclua a vinculação a FastConnect a rede de nuvem virtual em seu locatário do Oracle por meio do Gateway de roteamento dinâmico, usando a tabela de rotas.
1. Navegue para o Azure e certifique-se de que o **Status do provedor** para o ExpressRoute o circuito foi alterado para **provisionado** e se um emparelhamento do tipo **privado do Azure** tiver sido provisionado. Esse é um pré-requisito para as etapas a seguir.

    ![Status do provedor de ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Clique no **privado do Azure** emparelhamento. Você verá que os detalhes de emparelhamento automaticamente foram configurados com base nas informações fornecidas ao definir o seu circuito FastConnect.

    ![Configurações de emparelhamento privado](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Conectar-se a rede virtual para ExpressRoute

1. Crie uma rede virtual e um gateway de rede virtual, se você ainda não fez isso. Para obter detalhes, consulte o [guia passo a passo](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Configurar a conexão entre o gateway de rede virtual e o circuito do ExpressRoute executando o [script do Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) ou executando o comando do PowerShell para [configurar ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Depois de concluir a configuração de rede, você pode verificar a validade de sua configuração clicando em **obter registros de ARP** e **obter tabela de rotas** abaixo da folha de emparelhamento privado de ExpressRoute em o portal do Azure.

## <a name="automation"></a>Automação

A Microsoft criou scripts do Terraform para habilitar a implantação automatizada, a interconexão de rede. Scripts do Terraform precisam se autenticar no Azure antes da execução, porque eles requerem as permissões adequadas na assinatura do Azure. A autenticação pode ser realizada usando um [entidade de serviço do Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) ou usando a CLI do Azure. Para obter mais informações, consulte o [documentação do Terraform](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Os scripts do Terraform e a documentação relacionada para implantar o inter-connect podem ser encontradas neste [repositório GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitoramento

Instalar agentes em ambas as nuvens, você pode aproveitar o Azure [Monitor de desempenho de rede (NPM)](../../../expressroute/how-to-npm.md) para monitorar o desempenho da rede de ponta a ponta. NPM ajuda você a identificar prontamente problemas de rede e ajuda a eliminá-los.

## <a name="delete-the-interconnect-link"></a>Exclua o link de interconexão

Para excluir a interconexão, devem ser seguidas as etapas a seguir, nesta ordem específica. Falha ao fazer isso resultará em um circuito de ExpressRoute "estado de falha".

1. Exclua a conexão de ExpressRoute. Clique em Excluir a conexão a **excluir** ícone na página de sua conexão. Para obter mais informações, consulte o [documentação do ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Exclua o FastConnect Oracle do Console de nuvem do Oracle.
1. Depois que o circuito FastConnect Oracle foi excluído, você pode excluir o circuito de ExpressRoute do Azure.

Neste ponto, a exclusão e o processo de desprovisionamento de usuários foi concluída.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a conexão de nuvem entre OCI e o Azure, consulte a [documentação do Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Use [scripts do Terraform](https://aka.ms/azureociinterconnecttf) para implantar a infraestrutura Oracle para aplicativos de destino ao longo do Azure e configurar a interconexão de rede. 