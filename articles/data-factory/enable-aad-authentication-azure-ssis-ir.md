---
title: Habilitar a autenticação do Azure Active Directory para o Azure-SSIS Integration Runtime | Microsoft Docs
description: Este artigo descreve como habilitar a autenticação do Microsoft Azure Active Directory com a identidade gerenciada do Azure Data Factory para criar o Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 5/14/2019
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: f3d0aaee624bdba169f13313bb57a3ebe8075592
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490066"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Habilitar a autenticação do Azure Active Directory para o Azure-SSIS Integration Runtime

Este artigo mostra como habilitar a autenticação do Active Directory (Azure AD) com a identidade gerenciada para seu Azure Data Factory (ADF) e usá-lo em vez da autenticação do SQL para criar um Azure-SSIS IR (Integration Runtime) que por sua vez provisionará Catálogo banco de dados SSIS (SSISDB) no banco de dados SQL server/gerenciado a instância em seu nome.

Para obter mais informações sobre a identidade gerenciada para o ADF, consulte [identiy gerenciado para o Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  Nesse cenário, a autenticação do Azure AD com a identidade gerenciada para ADF só é usada na criação e operações subsequentes de partida de IR do SSIS que será em Ativar provisionar e se conectar ao SSISDB. Para execuções de pacote do SSIS, IR do SSIS ainda será se conectar ao SSISDB usando a autenticação do SQL com contas totalmente gerenciadas que são criadas durante o provisionamento de SSISDB.
>-  Se você já tiver criado o IR do SSIS usando a autenticação do SQL, você poderá reconfigurá-lo para usar a autenticação do AD do Azure por meio do PowerShell no momento não, mas você pode fazer isso por meio do aplicativo do AAD/portal do Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Habilitar o Azure AD no Banco de Dados SQL do Azure

O servidor do Banco de Dados SQL do Azure oferece suporte à criação de um banco de dados com um usuário do Azure AD. Primeiro, é necessário criar um grupo do Azure AD com a identidade gerenciada para o ADF como um membro. Em seguida, você precisa definir um usuário do Azure AD como o administrador do Active Directory para o servidor do Banco de Dados SQL do Azure e, então, conectar a ele no SSMS (SQL Server Management Studio) usando esse usuário. Por fim, será necessário criar um usuário independente que representa o grupo do Azure AD, de modo que a identidade gerenciada para o ADF possa ser usada pelo Azure-SSIS IR para criar o SSISDB em seu nome.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Criar um grupo do Azure AD com a identidade gerenciada para o ADF como um membro

É possível usar um grupo do Azure AD existente ou criar um novo usando o PowerShell do Azure AD.

1.  Instale o módulo do [PowerShell do Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

2.  Entre usando  `Connect-AzureAD` e execute o cmdlet a seguir para criar um grupo e salvá-lo em uma variável:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    O resultado é semelhante ao exemplo a seguir, que também exibe o valor da variável:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Adicione a identidade gerenciada para o seu ADF ao grupo. Você pode seguir o artigo [identiy gerenciado para o Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) para obter a ID de objeto de identidade gerenciada principal (por exemplo, 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, mas não usar a ID do aplicativo de identidade gerenciada para essa finalidade).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Também é possível examinar a associação do grupo posteriormente.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Configurar a autenticação do Azure AD para o servidor do Banco de Dados SQL do Azure

É possível  [Configurar e gerenciar a autenticação do Azure AD com o SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) seguindo estas etapas:

1.  No portal do Azure, selecione **Todos os serviços** -> **Servidores SQL** na barra de navegação esquerda.

2.  Selecione o servidor do Banco de Dados SQL do Azure a ser configurado com autenticação do Azure AD.

3.  Na seção **Configurações** da folha, selecione **Administrador do Active Directory**.

4.  Na barra de comandos, selecione **Definir administrador**.

5.  Selecione uma conta de usuário do AD do Azure para se tornarão o administrador do servidor e, em seguida, selecione **selecione.**

6.  Na barra de comandos, selecione **Salvar.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Criar um usuário independente no servidor do Banco de Dados SQL do Azure que representa o grupo do Azure AD

Para esta próxima etapa, você precisará [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Inicie o SSMS.

2. No **conectar ao servidor** caixa de diálogo, insira o nome do servidor de banco de dados SQL no **nome do servidor** campo.

3. No **autenticação** campo, selecione **Active Directory – Universal com suporte MFA** (você também pode usar os outros dois tipos de autenticação do Active Directory, consulte [configurar e gerenciar Autenticação do Azure AD com o SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. No **nome de usuário** , insira o nome da conta do Azure AD que você definir como o administrador do servidor, por exemplo, testuser@xxxonline.com.

5. Selecione **Connect** e concluir o processo de logon.

6. No **Pesquisador de Objetos**, expanda a pasta **Bancos de dados** -> **Bancos de Dados do Sistema**.

7. Clique com o botão direito do mouse no banco de dados **mestre** e selecione **Nova consulta**.

8. Na janela de consulta, insira o seguinte comando T-SQL e selecione **Execute** na barra de ferramentas.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   O comando deve ser concluído com êxito, criando um usuário independente para representar o grupo.

9. Limpe a janela de consulta, insira a o comando T-SQL a seguir e selecione **Executar** na barra de ferramentas.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   O comando deve ser concluído com êxito, concedendo ao usuário independente a capacidade de criar um banco de dados (SSISDB).

10. Se o SSISDB foi criado usando a autenticação do SQL e você desejar alternar para usar a autenticação do Azure AD para o IR do Azure-SSIS para acessá-lo, clique duas vezes em **SSISDB** do banco de dados e selecione **nova consulta**.

11. Na janela de consulta, insira o seguinte comando T-SQL e selecione **Execute** na barra de ferramentas.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    O comando deve ser concluído com êxito, criando um usuário independente para representar o grupo.

12. Limpe a janela de consulta, insira a o comando T-SQL a seguir e selecione **Executar** na barra de ferramentas.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    O comando deve ser concluído com êxito, concedendo ao usuário independente a capacidade de acessar o SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Habilitar o Azure AD na Instância Gerenciada do Banco de Dados SQL do Azure

A Instância Gerenciada do Banco de Dados SQL do Azure dá suporte à criação de um banco de dados com a identidade gerenciada diretamente para o ADF. Não é necessário unir a identidade gerenciada para o ADF para um grupo do Azure AD nem criar um usuário independente que represente esse grupo em sua Instância Gerenciada.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Configurar a autenticação do Azure AD para a Instância Gerenciada do Banco de Dados SQL do Azure

Siga as etapas em [provisionar um administrador do Active Directory do Azure para sua instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Adicionar a identidade gerenciada para o ADF como um usuário na Instância Gerenciada do Banco de Dados SQL do Azure

Para esta próxima etapa, você precisará [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Inicie o SSMS.

2.  Conectar-se à sua instância gerenciada usando uma conta do SQL Server que é um **sysadmin**. Essa é uma limitação temporária que será removida quando entidades do servidor (logons) do Azure AD para o banco de dados de instância gerenciada do SQL se torna a GA. Você verá o seguinte erro se tentar usar uma conta do administrador do Azure AD para criar o logon: Msg 15247, nível 16, estado 1, linha 1 usuário tem permissão para executar esta ação.

3.  No **Pesquisador de Objetos**, expanda a pasta **Bancos de dados** -> **Bancos de Dados do Sistema**.

4.  Clique com o botão direito do mouse no banco de dados **mestre** e selecione **Nova consulta**.

5.  Obtenha a identidade gerenciada para o ADF. Você pode seguir o artigo [identiy gerenciado para o Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) para obter a ID do aplicativo de identidade gerenciada principal (mas não use a ID de objeto de identidade gerenciada para essa finalidade).

6.  Na janela de consulta, execute o seguinte script T-SQL para converter a identidade gerenciada do ADF para tipo binário:

    ```sql
    DECLARE @applicationId uniqueidentifier = '{your Managed Identity Application ID}'
    select CAST(@applicationId AS varbinary)
    ```
    
    O comando deve ser concluído com êxito, exibindo a identidade gerenciada para o ADF como binário.

7.  Limpar a janela de consulta e executar o script T-SQL a seguir para adicionar a identidade gerenciada para o ADF como um usuário

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID = {your Managed Identity Application ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    O comando deve ser concluído com êxito, concedendo à identidade gerenciada para o ADF a capacidade de criar um banco de dados (SSISDB).

8.  Se o SSISDB foi criado usando a autenticação do SQL e você desejar alternar para usar a autenticação do Azure AD para o IR do Azure-SSIS para acessá-lo, clique duas vezes em **SSISDB** do banco de dados e selecione **nova consulta**.

9.  Na janela de consulta, insira o seguinte comando T-SQL e selecione **Execute** na barra de ferramentas.

    ```sql
    CREATE USER [{the managed identity name}] FOR LOGIN [{the managed identity name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{the managed identity name}]
    ```

    O comando deve ser concluído com êxito, concedendo à identidade gerenciada para o ADF a capacidade de acessar o SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Provisionar o Azure-SSIS IR no portal do Azure/aplicativo ADF

Ao provisionar o Azure-SSIS IR no portal do Azure/aplicativo ADF, na página **Configurações do SQL**, selecione a opção **Usar autenticação do AAD com a identidade gerenciada para o ADF**. A captura de tela a seguir mostra as configurações do IR com o servidor do Banco de Dados SQL do Azure hospedando SSISDB. Para IR com Instância Gerenciada hospedando SSISDB, as configurações **Camada de Serviço Catálogo do Banco de Dados** e **Permitir acesso dos serviços do Azure** não são aplicáveis, enquanto que as outras configurações são os mesmas.

Para obter mais informações sobre como criar um Azure-SSIS IR, consulte [Criar um tempo de execução de integração do Azure-SSIS no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Configurações do tempo de execução de integração do Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Provisionar o Azure-SSIS IR com o PowerShell

Para provisionar seu IR do Azure-SSIS com o PowerShell, faça o seguinte:

1.  Instale o módulo[Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) .

2.  No seu script, não defina o parâmetro `CatalogAdminCredential`. Por exemplo:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
