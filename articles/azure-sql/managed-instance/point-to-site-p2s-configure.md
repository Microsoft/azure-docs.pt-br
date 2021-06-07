---
title: Configurar a conectividade ponto a site usando o SSMS
titleSuffix: Azure SQL Managed Instance
description: Conecte-se à Instância Gerenciada de SQL do Azure usando o SSMS (SQL Server Management Studio) usando uma conexão ponto a site em um computador cliente local.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 3baf2d7ed7c326895ae40948fc2d0a4cc03021f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788359"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>Início rápido: Configurar uma conexão ponto a site à Instância Gerenciada de SQL do Azure no local
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este guia de início rápido demonstra como se conectar à Instância Gerenciada de SQL do Azure usando o [SSMS](/sql/ssms/sql-server-management-studio-ssms) (SQL Server Management Studio) em um computador cliente local por meio de uma conexão ponto a site. Para obter informações sobre conexões ponto a site, confira [Sobre a VPN ponto a site](../../vpn-gateway/point-to-site-about.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido:

- Usa os recursos criados em [Criar uma instância gerenciada](instance-create-quickstart.md) como ponto de partida.
- Exige o PowerShell 5.1 e o Azure PowerShell 1.4.0 ou posterior no computador cliente local. Se necessário, consulte as instruções para [Instalando o módulo Azure PowerShell](/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Exige a versão mais recente do [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) no computador cliente local.

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>Anexar um gateway de VPN a uma rede virtual

1. Abra o PowerShell no computador cliente local.

2. Copie este script do PowerShell. Esse script anexa um gateway de VPN à rede virtual da Instância Gerenciada de SQL criada no guia de início rápido [Criar uma instância gerenciada](instance-create-quickstart.md). Esse script usa o módulo Az do Azure PowerShell e faz o seguinte para os hosts baseados no Windows ou no Linux:

   - Cria e instala certificados em um computador cliente
   - Calcula o intervalo de IP da sub-rede futura de gateway de VPN
   - Cria a sub-rede de gateway
   - Implanta o modelo do Azure Resource Manager que anexa o gateway de VPN à sub-rede de VPN

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. Cole o script na janela do PowerShell e forneça os parâmetros necessários. Os valores de `<subscriptionId>`, `<resourceGroup>` e `<virtualNetworkName>` devem corresponder àqueles usados no guia de início rápido [Criar uma instância gerenciada](instance-create-quickstart.md). O valor de `<certificateNamePrefix>` pode ser uma cadeia de caracteres de sua escolha.

4. Execute o script do PowerShell.

> [!IMPORTANT]
> Não continue até que o script do PowerShell seja concluído.

## <a name="create-a-vpn-connection"></a>Criar uma conexão VPN

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Abra o grupo de recursos no qual você criou o gateway de rede virtual e, em seguida, abra o recurso de gateway de rede virtual.
3. Clique em **Configuração Ponto a Site** e, em seguida, clique em **Fazer o download do cliente VPN**.

    ![Baixar cliente VPN](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. No computador cliente local, extraia os arquivos do arquivo zip e, em seguida, abra a pasta com os arquivos extraídos.
5. Abra a pasta **WindowsAmd64** e o arquivo **VpnClientSetupAmd64.exe**.
6. Se você receber uma mensagem **O Windows protegeu seu computador**, clique em **Mais informações** e, em seguida, em **Executar mesmo assim**.

    ![Instalar o cliente VPN](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. Na caixa de diálogo Controle de Conta de Usuário, clique em **Sim** para continuar.
8. Na caixa de diálogo que referencia sua rede virtual, selecione **Sim** para instalar o cliente VPN na rede virtual.

## <a name="connect-to-the-vpn-connection"></a>Conectar-se à conexão VPN

1. Acesse **VPN** em **Rede e Internet** no computador cliente local e escolha sua rede virtual da Instância Gerenciada de SQL para estabelecer uma conexão com essa VNet. Na imagem a seguir, a rede virtual é chamada **MyNewVNet**.

    ![Conexão VPN](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. Selecione **Conectar**.
3. Na caixa de diálogo, selecione **Sim**.

    ![Captura de tela que realça o botão Conectar.](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. Quando você receber uma mensagem indicando que o Gerenciador de Conexões precisa obter privilégios elevados para atualizar a tabela de rotas, escolha **Continuar**.
5. Clique em **Sim** na caixa de diálogo Controle de Conta de Usuário para continuar.

   Você estabeleceu uma conexão VPN à VNet da Instância Gerenciada de SQL.

    ![Captura de tela que realça a mensagem Conectado quando você tiver estabelecido sua conexão.](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>Conectar-se ao SSMS

1. No computador cliente local, abra o SSMS SQL Server Management Studio.
2. Na caixa de diálogo **Conectar-se ao Servidor**, insira o **nome do host** totalmente qualificado da instância gerenciada na caixa **Nome do servidor**.
3. Selecione **Autenticação do SQL Server**, forneça seu nome de usuário e senha e, em seguida, selecione **Conectar**.

    ![Conexão com o SSMS](./media/point-to-site-p2s-configure/ssms-connect.png)  

Depois de se conectar, visualize seus bancos de dados do sistema e do usuário no bancos de dados de nós. Exiba também vários objetos em segurança, objetos de servidor, replicação, gerenciamento, SQL Server Agent e nós do XEvent Profiler.

## <a name="next-steps"></a>Próximas etapas

- Para obter um início rápido que mostra como se conectar por meio de uma máquina virtual do Azure, confira [Configurar uma conexão ponto a site](point-to-site-p2s-configure.md).
- Para obter uma visão geral das opções de conexão para aplicativos, confira [Conectar seus aplicativos à Instância Gerenciada de SQL](connect-application-instance.md).
- Para restaurar um banco de dados existente do SQL Server do local para uma instância gerenciada, use o [Serviço de Migração de Banco de Dados do Azure para realizar a migração](../../dms/tutorial-sql-server-to-managed-instance.md) ou o [comando T-SQL RESTORE](restore-sample-database-quickstart.md) para restaurar de um arquivo de backup do banco de dados.