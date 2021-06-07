---
title: Atribuição de função de entidade de serviço da Área de Trabalho Virtual do Windows (clássica) – Azure
description: Como criar entidades de serviço e atribuir funções ao usar o PowerShell na Área de Trabalho Virtual do Windows (clássica).
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 535ee2dace742a57877c9673e77bee64fd81c456
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445033"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell-in-windows-virtual-desktop-classic"></a>Tutorial: Criar entidades de serviço e atribuições de função com o PowerShell na Área de Trabalho Virtual do Windows (clássica)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não dá suporte a objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager.

As entidades de serviço são identidades que você pode criar no Azure Active Directory para atribuir funções e permissões para uma finalidade específica. Na Área de Trabalho Virtual do Windows, é possível criar uma entidade de serviço para:

- Automatize tarefas específicas de gerenciamento da Área de Trabalho Virtual do Windows.
- Usá-la como credenciais no lugar de usuários com MFA necessário durante a execução de qualquer modelo do Azure Resource Manager para Windows Virtual Desktop.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar uma entidade de serviço no Azure Active Directory.
> * Criar uma atribuição de função na Área de Trabalho Virtual do Windows.
> * Entrar na Área de Trabalho Virtual do Windows usando a entidade de serviço.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar entidades de serviço e atribuições de função, você precisará realizar três tarefas:

1. Instalar o módulo do Azure AD. Para instalar o módulo, execute o PowerShell como administrador e execute o seguinte cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. [Baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/).

3. Siga todas as instruções deste artigo na mesma sessão do PowerShell. O processo poderá não funcionar se você interromper a sessão do PowerShell fechando a janela e reabrindo-a mais tarde.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Criar uma entidade de serviço no Azure Active Directory

Depois de atender aos pré-requisitos na sessão do PowerShell, execute os cmdlets do PowerShell a seguir para criar uma entidade de serviço multilocatário no Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Exibir suas credenciais no PowerShell

Antes de criar a atribuição de função para sua entidade de serviço, exiba suas credenciais e anote-as para referência futura. A senha é especialmente importante porque você não poderá recuperá-la depois de fechar essa sessão do PowerShell.

Estas são as três credenciais que você deverá anotar e os cmdlets que você precisará executar para obtê-las:

- Senha:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- ID do locatário:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- ID do aplicativo:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Criar uma atribuição de função na Área de Trabalho Virtual do Windows

Em seguida, você precisa criar uma atribuição de função para que a entidade de serviço possa entrar na Área de Trabalho Virtual do Windows. Entre com uma conta que tenha permissões para criar atribuições de função.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso.

Execute os cmdlets do PowerShell a seguir para se conectar à Área de Trabalho Virtual do Windows e exibir seus locatários.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Quando encontrar o nome do locatário para o qual deseja criar uma atribuição de função, use esse nome no seguinte cmdlet:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Entrar com a entidade de serviço

Depois de criar uma atribuição de função para a entidade de serviço, verifique se a entidade de serviço pode entrar na Área de Trabalho Virtual do Windows executando o seguinte cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Depois de conectado, verifique se tudo está funcionando testando alguns cmdlets do PowerShell da Área de Trabalho Virtual do Windows com a entidade de serviço.

## <a name="next-steps"></a>Próximas etapas

Depois de criar a entidade de serviço e atribuí-la uma função no locatário da Área de Trabalho Virtual do Windows, você poderá usá-la para criar um pool de host. Para saber mais sobre os pools de host, continue com o tutorial para criar um pool de hosts na Área de Trabalho Virtual do Windows.

 > [!div class="nextstepaction"]
 > [Criar um pool de host com o Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
