---
title: Automatize as tarefas do Azure Analysis Services com entidades de serviço | Microsoft Docs
description: Saiba como criar entidades de serviço para automatizar tarefas do Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a440494b183d18c1d888b5d39836eb4317190d02
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764339"
---
# <a name="automation-with-service-principals"></a>Automação com entidades de serviço

As entidades de serviço são um recurso de aplicativo do Azure Active Directory que você cria em seu locatário para executar operações de nível de serviço e recursos autônomos. Eles são um tipo exclusivo de *identidade do usuário* com uma ID de aplicativo e senha ou certificado. Uma entidade de serviço tem apenas as permissões necessárias para executar tarefas definidas pelas funções e permissões para as quais é atribuída. 

No Analysis Services, as entidades de serviço são usadas com o Automação do Azure, o modo autônomo do PowerShell, aplicativos cliente personalizados e aplicativos Web para automatizar tarefas comuns. Por exemplo, servidores de provisionamento, modelos de implantação, atualização de dados, escalar verticalmente/reduzir verticalmente e pausar/continuar podem ser automatizados usando entidades de serviço. As permissões são atribuídas a entidades de serviço por meio da associação de funções, semelhante às contas comuns de UPN do Microsoft Azure AD.

## <a name="create-service-principals"></a>Criar entidades de serviço
 
As entidades de serviço podem ser criadas no portal do Azure ou usando o PowerShell. Para obter mais informações, consulte:

[Criar entidade de serviço - Portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar entidade de serviço - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Armazenar credenciais e ativos de certificado na Automação do Azure

Credenciais e certificados da entidade de serviço podem ser armazenados de forma segura na Automação do Azure para operações de runbook. Para obter mais informações, consulte:

[Ativos de credenciais na Automação do Azure](../automation/automation-credentials.md)   
[Ativos de certificado na Automação do Azure](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Adicionar entidades de serviço à função de administrador do servidor

Antes de poder usar uma entidade de serviço para operações de gerenciamento de servidor do Analysis Services, você deverá adicioná-la à função de administradores do servidor. Para saber mais, consulte [Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Entidades de serviço em cadeias de conexão

AppID de entidade de serviço e senha ou certificado podem ser usados em cadeias de conexão da mesma forma que um UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="a-nameazmodule-using-azanalysisservices-module"></a><a name="azmodule" />Usando o módulo Az.AnalysisServices

Ao usar uma entidade de serviço para operações de gerenciamento de recursos com o [Az.AnalysisServices](/powershell/module/az.analysisservices) módulo, use `Connect-AzAccount` cmdlet. 

No exemplo a seguir, a appID e uma senha são usados para executar operações do plano de controle para a sincronização de réplicas somente leitura e escalar vertical/horizontal:

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

#### <a name="using-sqlserver-module"></a>Usando o módulo do SQLServer

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

Ao conectar com aplicativos cliente e aplicativos Web, os pacotes instaláveis das [bibliotecas cliente AMO e ADOMD](analysis-services-data-providers.md) versão 15.0.2 e superior do NuGet dão suporte para entidades de serviço nas cadeias de conexão usando a sintaxe a seguir: `app:AppID` e senha ou `cert:thumbprint`. 

No exemplo a seguir, `appID` e `password` são usados para executar uma operação de atualização de modelo de banco de dados:

```C#
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
[Entrar com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md)   
