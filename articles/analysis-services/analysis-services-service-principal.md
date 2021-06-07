---
title: Automatize as tarefas do Azure Analysis Services com entidades de serviço | Microsoft Docs
description: Saiba como criar uma entidade de serviço para automatizar tarefas administrativas do Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b04b9ababfe0e4c2a60d14044b9d3ee120837dc5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96491036"
---
# <a name="automation-with-service-principals"></a>Automação com entidades de serviço

As entidades de serviço são um recurso de aplicativo do Azure Active Directory que você cria em seu locatário para executar operações de nível de serviço e recursos autônomos. Eles são um tipo exclusivo de *identidade do usuário* com uma ID de aplicativo e senha ou certificado. Uma entidade de serviço tem apenas as permissões necessárias para executar tarefas definidas pelas funções e permissões para as quais é atribuída. 

No Analysis Services, as entidades de serviço são usadas com o Automação do Azure, o modo autônomo do PowerShell, aplicativos cliente personalizados e aplicativos Web para automatizar tarefas comuns. Por exemplo, servidores de provisionamento, modelos de implantação, atualização de dados, escalar verticalmente/reduzir verticalmente e pausar/continuar podem ser automatizados usando entidades de serviço. As permissões são atribuídas a entidades de serviço por meio da associação de funções, semelhante às contas comuns de UPN do Microsoft Azure AD.

O Analysis Services também é compatível com operações executadas por identidades gerenciadas usando entidades de serviço. Para saber mais, confira [Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) e [Serviços do Azure compatíveis com a autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services).    

## <a name="create-service-principals"></a>Criar entidades de serviço
 
As entidades de serviço podem ser criadas no portal do Azure ou usando o PowerShell. Para obter mais informações, consulte:

[Criar entidade de serviço - Portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar entidade de serviço - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Armazenar credenciais e ativos de certificado na Automação do Azure

Credenciais e certificados da entidade de serviço podem ser armazenados de forma segura na Automação do Azure para operações de runbook. Para obter mais informações, consulte:

[Ativos de credenciais na Automação do Azure](../automation/shared-resources/credentials.md)   
[Ativos de certificado na Automação do Azure](../automation/shared-resources/certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Adicionar entidades de serviço à função de administrador do servidor

Antes de poder usar uma entidade de serviço para operações de gerenciamento de servidor do Analysis Services, você deverá adicioná-la à função de administradores do servidor. As entidades de serviço devem ser adicionadas diretamente à função de administrador do servidor. Não há suporte para a adição de uma entidade de serviço a um grupo de segurança e a adição desse grupo de segurança à função de administrador do servidor. Para saber mais, consulte [Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Entidades de serviço em cadeias de conexão

AppID de entidade de serviço e senha ou certificado podem ser usados em cadeias de conexão da mesma forma que um UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule"></a>Usar o módulo Az.AnalysisServices

Ao usar uma entidade de serviço para operações de gerenciamento de recursos com o módulo [Az.AnalysisServices](/powershell/module/az.analysisservices), use o cmdlet `Connect-AzAccount`. 

No seguinte exemplo, appID e uma senha são usados para executar operações de plano de controle para sincronização a réplicas somente leitura e escala vertical/horizontal:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>Usar o módulo SQLServer

No exemplo a seguir, appID e uma senha são usados para executar uma operação de atualização de modelo de banco de dados:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO e ADOMD 

Ao conectar com aplicativos cliente e aplicativos Web, os pacotes instaláveis das [bibliotecas cliente AMO e ADOMD](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) versão 15.0.2 e superior do NuGet dão suporte para entidades de serviço nas cadeias de conexão usando a sintaxe a seguir: `app:AppID` e senha ou `cert:thumbprint`. 

No exemplo a seguir, `appID` e `password` são usados para executar uma operação de atualização de modelo de banco de dados:

```csharp
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Próximas etapas
[Entrar com o Azure PowerShell](/powershell/azure/authenticate-azureps)   
[Atualizar com Aplicativos Lógicos](analysis-services-refresh-logic-app.md)  
[Atualizar com Automação do Azure](analysis-services-refresh-azure-automation.md)  
[Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md)  
[Automatizar tarefas de conjunto de dados e workspace do Power BI Premium com entidades de serviço](/power-bi/admin/service-premium-service-principal)