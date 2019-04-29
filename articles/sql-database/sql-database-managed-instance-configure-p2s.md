---
title: Configurar o P2S – Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Conecte-se a uma Instância Gerenciada do Banco de Dados SQL do Azure usando o SQL Server Management Studio por meio de uma conexão ponto a site em um computador cliente local.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: 9ca9506ad9ca826349b2815e504e9ba8bf976faa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60701991"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Início Rápido: Configurar uma conexão ponto a ponto com uma Instância Gerenciada do Banco de Dados SQL do Azure a partir de locais

Este início rápido demonstra como se conectar a uma Instância Gerenciada do Banco de Dados SQL do Azure usando o [SSMS](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SQL Server Management Studio) em um computador cliente local por meio de uma conexão ponto a site. Para obter informações sobre conexões ponto a site, confira [Sobre a VPN Ponto a Site](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido:

- Use os recursos criados em [Criar uma Instância Gerenciada](sql-database-managed-instance-get-started.md) como um ponto de partida.
- Requer o PowerShell 5.1 e AZ PowerShell 1.4.0 ou posterior no computador cliente local. Se necessário, consulte as instruções para [Instalando o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Exige a versão mais recente do [SSMS](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SQL Server Management Studio) no computador cliente local.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Anexar um gateway de VPN à rede virtual da Instância Gerenciada

1. Abra o PowerShell no computador cliente local.

2. Copie este script do PowerShell. Esse script anexa um Gateway de VPN à rede virtual da Instância Gerenciada criada no início rápido [Criar uma Instância Gerenciada](sql-database-managed-instance-get-started.md). Esse script faz o seguinte:

   - Cria e instala certificados no computador cliente
   - Calcula o intervalo de IP de sub-rede do futuro Gateway de VPN
   - Cria a GatewaySubnet
   - Implanta o modelo do Azure Resource Manager que anexa o Gateway de VPN à sub-rede da VPN

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGatewayAz.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

     > [!IMPORTANT]
     > Para usar o módulo do PowerShell do Azure Resource Manager em vez do módulo de Az, use o cmdlet a seguir: `attachVPNGateway.ps1` em vez de `attachVPNGatewayAz.ps1` cmdlet.

3. Cole o script na janela do PowerShell e forneça os parâmetros necessários. Os valores de `<subscriptionId>`, `<resourceGroup>` e `<virtualNetworkName>` devem corresponder àqueles usados no início rápido [Criar uma Instância Gerenciada](sql-database-managed-instance-get-started.md). O valor de `<certificateNamePrefix>` pode ser uma cadeia de caracteres de sua escolha.

4. Execute o script do PowerShell.

> [!IMPORTANT]
> Não continue até que o script do PowerShell seja concluída.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Criar uma conexão VPN à Instância Gerenciada

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Abra o grupo de recursos no qual você criou o gateway de rede virtual e, em seguida, abra o recurso de gateway de rede virtual.
3. Clique em **Configuração Ponto a Site** e, em seguida, clique em **Fazer o download do cliente VPN**.

    ![Baixar cliente VPN](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. No computador cliente local, extraia os arquivos do arquivo zip e, em seguida, abra a pasta com os arquivos extraídos.
5. Abra o '**WindowsAmd64** pasta e abra o **VpnClientSetupAmd64.exe** arquivo.
6. Se você receber uma mensagem **O Windows protegeu seu computador**, clique em **Mais informações** e, em seguida, em **Executar mesmo assim**.

    ![Instalar o cliente VPN](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Na caixa de diálogo controle de conta de usuário, clique em **Sim** para continuar.
8. Na caixa de diálogo referenciando sua rede virtual, selecione **Sim** para instalar o cliente de VPN para sua rede virtual.

## <a name="connect-to-the-vpn-connection"></a>Conectar-se à conexão VPN

1. Vá para **VPN** na **rede e Internet** no computador cliente local e selecione sua rede virtual de instância gerenciada para estabelecer uma conexão para essa rede virtual. Na imagem a seguir, a rede virtual é chamada **MyNewVNet**.

    ![Conexão VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Selecione **Conectar**.
3. Na caixa de diálogo, selecione **Sim**.

    ![Conexão VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Quando você receber uma mensagem indicando que o Gerenciador de Conexões precisa obter privilégio elevado para atualizar a tabela de rotas, clique em **Continuar**.
5. Clique em **Sim** na caixa de diálogo Controle de Conta de Usuário para continuar.

   Você estabeleceu uma conexão VPN à VNET da Instância Gerenciada.

    ![Conexão VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Usar o SSMS para se conectar à Instância Gerenciada

1. No computador cliente local, abra o SSMS (SQL Server Management Studio).
2. Na caixa de diálogo **Conectar ao Servidor**, digite o **nome do host** totalmente qualificado para a Instância Gerenciada na caixa **Nome do servidor**.
3. Selecione **Autenticação do SQL Server**, forneça seu nome de usuário e senha e, em seguida, selecione **Conectar**.

    ![conexão ssms](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Depois de se conectar, visualize seus bancos de dados do sistema e do usuário no bancos de dados de nós. Exiba também vários objetos em segurança, objetos de servidor, replicação, gerenciamento, SQL Server Agent e nós do XEvent Profiler.

## <a name="next-steps"></a>Próximas etapas

- Para obter um início rápido que mostra como se conectar por meio de uma máquina virtual do Azure, confira [Configurar uma conexão ponto a site](sql-database-managed-instance-configure-p2s.md).
- Para obter uma visão geral das opções de conexão para aplicativos, confira [Conectar seus aplicativos à Instância Gerenciada](sql-database-managed-instance-connect-app.md).
- Para restaurar um banco de dados do SQL Server existente do local para uma Instância Gerenciada, use o [DMS (Serviço de Migração de Banco de Dados do Azure) para realizar a migração](../dms/tutorial-sql-server-to-managed-instance.md) ou o [comando T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) para restaurar de um arquivo de backup do banco de dados.
