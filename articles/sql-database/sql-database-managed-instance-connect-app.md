---
title: Aplicativo de conexão da Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo descreve como conectar seu aplicativo à Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 11/09/2018
ms.openlocfilehash: 5f4a1962f90d54001f315827c1243e929344e3d7
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274006"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Conectar seu aplicativo à Instância Gerenciada do Banco de Dados SQL do Azure

Atualmente, você tem várias opções ao decidir como e onde hospedar seu aplicativo.

Você pode optar por hospedar o aplicativo na nuvem usando o Serviço de Aplicativo do Azure ou algumas das opções integradas de VNET (rede virtual) do Azure como o Ambiente do Serviço de Aplicativo do Azure, a Máquina Virtual e o Conjunto de Dimensionamento de Máquinas Virtuais. Você também pode adotar uma abordagem de nuvem híbrida e manter seus aplicativos localmente.

Seja qual for sua escolha, você poderá conectá-la a uma Instância Gerenciada.  

![alta disponibilidade](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Conectar um aplicativo dentro da mesma VNET

Este cenário é o mais simples. As máquinas virtuais dentro da VNET podem se conectar entre si diretamente mesmo se estão em sub-redes diferentes. Isso significa que tudo o que você precisa para conectar o aplicativo em um Ambiente de Aplicativo do Azure ou uma Máquina Virtual é definir a cadeia de conexão corretamente.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Conectar um aplicativo em uma VNET diferente

Este cenário é um pouco mais complexo porque a Instância Gerenciada tem o endereço IP privado em sua própria VNET. Para se conectar, um aplicativo precisa ter acesso à VNET na qual a Instância Gerenciada foi implantada. Portanto, primeiro você precisa fazer uma conexão entre o aplicativo e a VNET da Instância Gerenciada. As VNETs não precisam estar na mesma assinatura para que este cenário funcione.

Há duas opções de conexão de VNETs:

- [Emparelhamento de Rede Virtual do Azure](../virtual-network/virtual-network-peering-overview.md)
- Gateway de VPN VNET a VNET ([portal do Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [CLI do Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

A opção de emparelhamento é a preferencial porque o emparelhamento usa a rede de backbone da Microsoft. Portanto, da perspectiva de conectividade, não há nenhuma diferença perceptível na latência entre as máquinas virtuais na VNET emparelhada e na mesma VNET. O emparelhamento de VNET é limitado a redes na mesma região.  

> [!IMPORTANT]
> O cenário de emparelhamento VNet para a instância gerenciada está limitado às redes na mesma região devido a [restrições do emparelhamento de Rede Virtual Global](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Também consulte a seção relevante a [Azure Virtual redes Frequently Asked Questions](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) artigo para obter mais detalhes. 

## <a name="connect-an-on-premises-application"></a>Conectar um aplicativo local

A Instância Gerenciada pode ser acessada somente por meio de um endereço IP privado. Para acessá-la localmente, você precisa fazer uma conexão Site a Site entre o aplicativo e a VNET da Instância Gerenciada.

Há duas opções de conexão do local à VNET do Azure:

- Conexão VPN Site a Site ([portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [CLI do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- Conexão do [ExpressRoute](../expressroute/expressroute-introduction.md)  

Se você estabeleceu local para a conexão do Azure com êxito e você não pode estabelecer conexão à instância gerenciada, verifique se o firewall tem conexão de saída aberto na porta 1433 do SQL, bem como intervalo de 11000 a 11999 de portas para o redirecionamento.

## <a name="connect-an-application-on-the-developers-box"></a>Conectar um aplicativo na caixa de desenvolvedores

A Instância Gerenciada pode ser acessada somente por meio de um endereço IP privado. Portanto, para acessá-la na caixa de desenvolvedor, primeiro você precisa fazer uma conexão entre a caixa de desenvolvedor e a VNET da Instância Gerenciada. Para isso, configure uma conexão ponto a site a uma VNet usando a autenticação de certificado nativa do Azure. Para ver mais informações, consulte [Configurar uma conexão ponto a site para conectar-se a uma Instância Gerenciada do Banco de Dados SQL do Azure do computador local](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Conectar-se do local com o emparelhamento VNet

Outro cenário implementado pelos clientes é quando o gateway de VPN está instalado em uma rede virtual e uma assinatura separada daquela que hospeda a Instância Gerenciada. Então, as duas redes virtuais são emparelhadas. O diagrama de arquitetura de exemplo a seguir mostra como isso pode ser implementado.

![Emparelhamento VNet](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Depois que a infraestrutura básica for configurada, será necessário modificar alguma configuração para que o Gateway de VPN possa ver os endereços IP na rede virtual que hospeda a Instância Gerenciada. Para fazer isso, faça as seguintes alterações muito específicas nas **Configurações de emparelhamento**.

1. Na VNet que hospeda o gateway de VPN, acesse **Emparelhamentos**, em seguida, acesse a conexão VNet emparelhada à Instância Gerenciada e clique em **Permitir trânsito de gateway**.
2. Na VNet que hospeda a Instância Gerenciada, acesse **Emparelhamentos**, em seguida, acesse a conexão VNet emparelhada ao Gateway de VPN e clique em **Usar gateways remotos**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Conectar um aplicativo hospedado no Serviço de Aplicativo do Azure

A Instância Gerenciada pode ser acessada somente por meio de um endereço IP privado. Portanto, para acessá-la no Serviço de Aplicativo do Azure, primeiro você precisa fazer uma conexão entre o aplicativo e a VNET da Instância Gerenciada. Consulte [Integrar seu aplicativo a uma Rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md).  

Para obter a solução de problemas, consulte [Solução de problemas de VNETs e aplicativos](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Se uma conexão não puder ser estabelecida, tente [sincronizar a configuração de rede](sql-database-managed-instance-sync-network-configuration.md).

Um caso especial de conexão do Serviço de Aplicativo do Azure à Instância Gerenciada é quando você integrou o Serviço de Aplicativo do Azure a uma rede emparelhada com a VNET da Instância Gerenciada. Esse caso exige a definição da seguinte configuração:

- A VNET da Instância Gerenciada NÃO deve ter um gateway  
- A VNET da Instância Gerenciada deve ter definida a opção Usar gateways remotos
- A VNET emparelhada deve ter definida a opção Permitir trânsito de gateway

Este cenário é ilustrado no seguinte diagrama:

![emparelhamento de aplicativo integrado](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>O recurso de Integração VNet não integra um aplicativo com uma rede VNet que tem um Gateway ExpressRoute. Mesmo se o Gateway do ExpressRoute estiver configurado no modo de coexistência, a Integração com a VNet não funcionará. Se for necessário acessar recursos por meio de uma conexão do ExpressRoute, você poderá usar um Ambiente do Serviço de Aplicativo que executa na Rede Virtual.

## <a name="troubleshooting-connectivity-issues"></a>Solucionar problemas de conectividade

Para solucionar problemas de conectividade, examine o seguinte:

- Se você não conseguir se conectar à instância gerenciada de uma máquina virtual do Azure no mesmo VNet mas sub-rede diferente, verifique se você tiver um grupo de segurança de rede definido na sub-rede VM que pode estar bloqueando o acesso. Além disso Observe que você precisa abrir a conexão de saída na porta 1433 do SQL, bem como as portas no intervalo de 11000-11999, pois esses são necessárias para conectar-se via redirecionamento dentro do limite do Azure.
- Verifique se a Propagação de BGP está definida como **Habilitada** para a tabela de rotas associada à VNet.
- Se estiver usando VPN P2S, verifique a configuração no portal do Azure para verificar se os números de **Entrada/Saída** são mostrados. Números diferentes de zero indicam que o Azure está roteando o tráfego de/para o local.

   ![Números de entrada/saída](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Verifique se o computador cliente (que executa o cliente VPN) tem entradas de rota para todas as VNets que você precisa acessar. As rotas são armazenadas em `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.

   ![route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Conforme mostrado nesta imagem, há duas entradas para cada VNet envolvida e uma terceira entrada para o ponto de extremidade VPN configurado no Portal.

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

- Se você usar o emparelhamento VNet, verifique se as instruções de configuração [Permitir trânsito de gateway e usar gateways remotos](#connect-from-on-premises-with-vnet-peering) foram seguidas.

## <a name="required-versions-of-drivers-and-tools"></a>Versões necessárias de drivers e ferramentas

As seguintes versões mínimas das ferramentas e drivers são recomendadas se você deseja se conectar à Instância Gerenciada:

| Driver/ferramenta | Versão |
| --- | --- |
|.NET Framework | 4.6.1 (ou .NET Core) |
|Driver ODBC| v17 |
|Driver PHP| 5.2.0 |
|Driver JDBC| 6.4.0 |
|Driver Node.js| 2.1.1 |
|Driver OLE DB| 18.0.2.0 |
|SSMS| 18.0 ou [superior](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) ou superior |

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre a Instância Gerenciada, consulte [O que é uma Instância Gerenciada](sql-database-managed-instance.md).
- Para obter um tutorial mostrando como criar uma nova Instância Gerenciada, consulte [Criar uma Instância Gerenciada](sql-database-managed-instance-get-started.md).
