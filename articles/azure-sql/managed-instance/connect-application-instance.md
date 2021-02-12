---
title: Conectar seu aplicativo ao SQL Instância Gerenciada
titleSuffix: Azure SQL Managed Instance
description: Este artigo discute como conectar seu aplicativo ao Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 965e765e22a4da8f2ac3b7151337cf62b65be4fe
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732604"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>Conectar seu aplicativo à Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Atualmente, você tem várias opções ao decidir como e onde hospedar seu aplicativo.

Você pode optar por hospedar o aplicativo na nuvem usando Azure App serviço ou algumas das opções integradas de rede virtual do Azure, como Ambiente do Serviço de Aplicativo do Azure, máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais. Você também pode adotar uma abordagem de nuvem híbrida e manter seus aplicativos localmente.

Seja qual for a opção que você fizer, você poderá conectá-lo ao Azure SQL Instância Gerenciada. 

![Alta disponibilidade](./media/connect-application-instance/application-deployment-topologies.png)

Este artigo descreve como conectar um aplicativo à Instância Gerenciada de SQL em vários cenários de aplicativo diferentes. 

## <a name="connect-inside-the-same-vnet"></a>Conectar-se dentro da mesma VNet

Conectar um aplicativo dentro da mesma rede virtual que o SQL Instância Gerenciada é o cenário mais simples. As máquinas virtuais dentro da rede virtual podem se conectar entre si diretamente, mesmo se estiverem dentro de sub-redes diferentes. Isso significa que tudo o que você precisa para conectar um aplicativo dentro de Ambiente do Serviço de Aplicativo ou uma máquina virtual é definir a cadeia de conexão adequadamente.  

## <a name="connect-inside-a-different-vnet"></a>Conectar-se dentro de uma VNet diferente

Conectar um aplicativo quando ele reside em uma rede virtual diferente do SQL Instância Gerenciada é um pouco mais complexo porque o SQL Instância Gerenciada tem endereços IP privados em sua própria rede virtual. Para se conectar, um aplicativo precisa de acesso à rede virtual onde o SQL Instância Gerenciada é implantado. Portanto, você precisa fazer uma conexão entre o aplicativo e a rede virtual do SQL Instância Gerenciada. As redes virtuais não precisam estar na mesma assinatura para que esse cenário funcione.

Há duas opções para conectar redes virtuais:

- [Emparelhamento VNet do Azure](../../virtual-network/virtual-network-peering-overview.md)
- Gateway de VPN VNET a VNET ([portal do Azure](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [CLI do Azure](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

O emparelhamento é preferível porque usa a rede de backbone da Microsoft, portanto, da perspectiva da conectividade, não há nenhuma diferença perceptível na latência entre as máquinas virtuais em uma rede virtual emparelhada e na mesma rede virtual. O emparelhamento de rede virtual tem suporte entre as redes na mesma região. O emparelhamento de rede virtual global também tem suporte com a limitação descrita na observação abaixo.  

> [!IMPORTANT]
> [Em 9/22/2020 anunciamos o emparelhamento de rede virtual global para clusters virtuais recém-criados](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Isso significa que o emparelhamento de rede virtual global tem suporte para instâncias gerenciadas do SQL criadas em sub-redes vazias após a data do anúncio, bem como para todas as instâncias gerenciadas subsequentes criadas nessas sub-redes. Para todas as outras instâncias gerenciadas do SQL, o suporte ao emparelhamento é limitado às redes na mesma região devido às [restrições do emparelhamento de rede virtual global](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Consulte também a seção relevante do artigo [perguntas frequentes sobre redes virtuais do Azure](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para obter mais detalhes. 

## <a name="connect-from-on-premises"></a>Conectar do local 

Você também pode conectar seu aplicativo local ao SQL Instância Gerenciada. O SQL Instância Gerenciada só pode ser acessado por meio de um endereço IP privado. Para acessá-lo do local, você precisa fazer uma conexão site a site entre o aplicativo e a rede virtual do SQL Instância Gerenciada.

Há duas opções de como conectar-se localmente a uma rede virtual do Azure:

- Conexão VPN site a site ([portal do Azure](../../vpn-gateway/tutorial-site-to-site-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) [CLI do Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- Conexão [do Azure ExpressRoute](../../expressroute/expressroute-introduction.md)  

Se você tiver estabelecido uma conexão local para o Azure com êxito e não puder estabelecer uma conexão com o SQL Instância Gerenciada, verifique se o firewall tem uma conexão de saída aberta na porta do SQL 1433, bem como o intervalo 11000-11999 de portas para redirecionamento.

## <a name="connect-the-developer-box"></a>Conectar a caixa do desenvolvedor

Também é possível conectar sua caixa de desenvolvedor ao SQL Instância Gerenciada. O SQL Instância Gerenciada pode ser acessado somente por meio de um endereço IP privado, portanto, para acessá-lo na caixa do desenvolvedor, primeiro você precisa fazer uma conexão entre sua caixa de desenvolvedor e a rede virtual do SQL Instância Gerenciada. Para fazer isso, configure uma conexão ponto a site com uma rede virtual usando a autenticação de certificado nativa do Azure. Para obter mais informações, consulte  [Configurar uma conexão ponto a site para se conectar ao Azure SQL instância gerenciada de um computador local](point-to-site-p2s-configure.md).

## <a name="connect-with-vnet-peering"></a>Conectar-se ao emparelhamento VNet

Outro cenário implementado por clientes é o local em que um gateway de VPN é instalado em uma rede virtual separada e uma assinatura do que hospeda o SQL Instância Gerenciada. Então, as duas redes virtuais são emparelhadas. O diagrama de arquitetura de exemplo a seguir mostra como isso pode ser implementado.

![Emparelhamento de rede virtual](./media/connect-application-instance/vnet-peering.png)

Depois que a infraestrutura básica estiver configurada, você precisará modificar algumas configurações para que o gateway de VPN possa ver os endereços IP na rede virtual que hospeda o SQL Instância Gerenciada. Para fazer isso, faça as seguintes alterações muito específicas nas **Configurações de emparelhamento**.

1. Na rede virtual que hospeda o gateway de VPN, acesse **emparelhamentos**, vá para a conexão de rede virtual emparelhada para o SQL instância gerenciada e, em seguida, clique em **Permitir trânsito de gateway**.
2. Na rede virtual que hospeda o SQL Instância Gerenciada, acesse **emparelhamentos**, vá para a conexão de rede virtual emparelhada para o gateway de VPN e, em seguida, clique em **usar gateways remotos**.

## <a name="connect-azure-app-service"></a>Conectar Azure App serviço 

Você também pode conectar um aplicativo que é hospedado pelo serviço Azure App. O SQL Instância Gerenciada pode ser acessado somente por meio de um endereço IP privado, portanto, para acessá-lo do serviço Azure App, primeiro você precisa fazer uma conexão entre o aplicativo e a rede virtual do SQL Instância Gerenciada. Consulte [integrar seu aplicativo a uma rede virtual do Azure](../../app-service/web-sites-integrate-with-vnet.md).  

Para solução de problemas, consulte [solução de problemas de redes virtuais e aplicativos](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Se não for possível estabelecer uma conexão, tente [sincronizar a configuração de rede](azure-app-sync-network-configuration.md).

Um caso especial de conectar Azure App serviço ao SQL Instância Gerenciada é quando você integra o serviço Azure App a uma rede emparelhada a uma rede virtual do SQL Instância Gerenciada. Esse caso exige a definição da seguinte configuração:

- A rede virtual do SQL Instância Gerenciada não deve ter um gateway  
- A rede virtual do SQL Instância Gerenciada deve ter a `Use remote gateways` opção definida
- A rede virtual emparelhada deve ter a `Allow gateway transit` opção definida

Este cenário é ilustrado no seguinte diagrama:

![emparelhamento de aplicativo integrado](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>O recurso de integração de rede virtual não integra um aplicativo com uma rede virtual que tem um gateway de ExpressRoute. Mesmo que o gateway de ExpressRoute esteja configurado no modo de coexistência, a integração de rede virtual não funciona. Se você precisar acessar recursos por meio de uma conexão do ExpressRoute, poderá usar Ambiente do Serviço de Aplicativo, que é executado em sua rede virtual.

## <a name="troubleshooting-connectivity-issues"></a>Solucionar problemas de conectividade

Para solucionar problemas de conectividade, examine o seguinte:

- Se você não conseguir se conectar ao SQL Instância Gerenciada de uma máquina virtual do Azure na mesma rede virtual, mas em uma sub-rede diferente, verifique se você tem um grupo de segurança de rede definido na sub-rede VM que pode estar bloqueando o acesso. Além disso, abra a conexão de saída na porta SQL 1433, bem como portas no intervalo de 11000-11999, já que elas são necessárias para se conectar via redirecionamento dentro do limite do Azure.
- Verifique se a propagação de BGP está definida como **habilitada** para a tabela de rotas associada à rede virtual.
- Se estiver usando VPN P2S, verifique a configuração no portal do Azure para verificar se os números de **Entrada/Saída** são mostrados. Números diferentes de zero indicam que o Azure está roteando o tráfego de/para o local.

   ![Números de entrada/saída](./media/connect-application-instance/ingress-egress-numbers.png)

- Verifique se o computador cliente (que está executando o cliente VPN) tem entradas de rota para todas as redes virtuais que você precisa acessar. As rotas são armazenadas em `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.

   ![route.txt](./media/connect-application-instance/route-txt.png)

   Conforme mostrado nesta imagem, há duas entradas para cada rede virtual envolvida e uma terceira entrada para o ponto de extremidade de VPN que é configurada no Portal.

   Outra maneira de verificar as rotas é por meio do comando a seguir. A saída mostra as rotas para as diversas sub-redes:

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- Se você estiver usando o emparelhamento de rede virtual, certifique-se de ter seguido as instruções para definir [permitir o trânsito de gateway e usar gateways remotos](#connect-from-on-premises).

- Se você estiver usando o emparelhamento de rede virtual para conectar um aplicativo hospedado do serviço Azure App e a rede virtual do SQL Instância Gerenciada tiver um intervalo de endereços IP públicos, verifique se as configurações do aplicativo hospedado permitem que o tráfego de saída seja roteado para redes IP públicas. Siga as instruções em [integração de rede virtual regional](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration).

## <a name="required-versions-of-drivers-and-tools"></a>Versões necessárias de drivers e ferramentas

As seguintes versões mínimas das ferramentas e dos drivers serão recomendadas se você quiser se conectar ao SQL Instância Gerenciada:

| Driver/ferramenta | Versão |
| --- | --- |
|.NET Framework | 4.6.1 (ou .NET Core) |
|Driver ODBC| v17 |
|Driver PHP| 5.2.0 |
|Driver JDBC| 6.4.0 |
|Driver Node.js| 2.1.1 |
|Driver OLE DB| 18.0.2.0 |
|SSMS| 18,0 ou [superior](/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) ou superior |

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre o SQL Instância Gerenciada, consulte [o que é o sql instância gerenciada?](sql-managed-instance-paas-overview.md).
- Para obter um tutorial mostrando como criar uma nova instância gerenciada, consulte [criar uma instância gerenciada](instance-create-quickstart.md).