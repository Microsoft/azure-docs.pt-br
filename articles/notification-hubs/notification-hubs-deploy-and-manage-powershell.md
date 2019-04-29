---
title: Implantar e gerenciar os Hubs de Notificação usando o PowerShell
description: Como criar e gerenciar os hubs de notificação usando o PowerShell para automação
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 4dbbaeea736dd46478ad9992201ea28bd7bfc2ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457831"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Implantar e gerenciar hubs de notificação usando o PowerShell

## <a name="overview"></a>Visão geral

Este artigo mostra como usar Criar e Gerenciar Hubs de Notificação de Azure usando o PowerShell. As tarefas de automação comuns a seguir são mostradas neste artigo.

- Criar um hub de notificação
- Definir credenciais

Se você também precisar criar um novo namespace de barramento de serviço para os hubs de notificação, confira [Gerenciar o Barramento de Serviço com o PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

O gerenciamento de hubs de notificação não tem suporte direto dos cmdlets incluídos com o PowerShell do Azure. A melhor abordagem do PowerShell e fazer referência ao assembly Microsoft.Azure.NotificationHubs.dll. O assembly é distribuído com o [pacote NuGet de Hubs de Notificações do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura. Para saber mais sobre como adquirir uma assinatura, confira [Opções de compra], [Ofertas para membros] ou [Teste Gratuito].
- Um computador com o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure].
- Um entendimento geral dos scripts do PowerShell, dos pacotes NuGet e do .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluindo uma referência ao assembly .NET para o Barramento de Serviço

O gerenciamento de hubs de notificação do Azure ainda não está incluído com os cmdlets do PowerShell no PowerShell do Azure. Para provisionar hubs de notificação, você pode usar o cliente .NET fornecido a [pacote NuGet de Hubs de Notificações do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Primeiro, verifique se o seu script pode localizar o assembly **Microsoft.Azure.NotificationHubs.dll** , que é instalado como um pacote do NuGet em um projeto do Visual Studio. Para que seja flexível, o script executa estas etapas:

1. Determina o caminho no qual ele foi invocado.
2. Percorre o caminho até encontrar uma pasta denominada `packages`. Esta pasta é criada quando você instala pacotes NuGet para projetos do Visual Studio.
3. Pesquisa recursivamente a pasta `packages` para um assembly denominado `Microsoft.Azure.NotificationHubs.dll`.
4. Faz referência ao assembly para que os tipos estejam disponíveis para uso posterior.

Aqui está como essas etapas são implementadas em um script do PowerShell:

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Crie a classe `NamespaceManager`

Para provisionar os Hubs de Notificação, crie uma instância da classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) do SDK.

Você pode usar o cmdlet [Get-AzureSBAuthorizationRule] incluído com o PowerShell do Azure para recuperar uma regra de autorização que é usada para fornecer uma cadeia de conexão. Armazenamos uma referência para a instância `NamespaceManager` na variável `$NamespaceManager`. `$NamespaceManager` é usado para provisionar um hub de notificação.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Provisionando um novo hub de notificação

Para provisionar um novo Hub de Notificação, use a [API do .NET para Hubs de Notificação].

Nesta parte do script, você configura quatro variáveis locais.

1. `$Namespace`: defina como o nome do namespace em que você deseja criar um hub de notificação.
2. `$Path`: defina esse caminho como o nome do novo hub de notificação.  Por exemplo, "MyHub".
3. `$WnsPackageSid`: defina como o SID do pacote para seu Aplicativo do Windows no [Centro de Desenvolvimento do Windows](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: defina como a chave secreta de seu Aplicativo do Windows no [Centro de Desenvolvimento do Windows](https://developer.microsoft.com/en-us/windows).

Essas variáveis são usadas para se conectar ao seu namespace e criar um novo hub de notificação configurado para manipular as notificações do WNS (Windows Notification Services) com credenciais dos WNS para um aplicativo do Windows. Para obter informações sobre como obter o SID de pacote e a chave de segredo, consulte o tutorial [Introdução aos Hubs de Notificação](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .

- O snippet de script usa o objeto `NamespaceManager` para verificar se o Hub de Notificação identificado pelo `$Path` existe.
- Se não existir, o script criará um `NotificationHubDescription` com as credenciais do WNS e o passará à classe `NamespaceManager`, método `CreateNotificationHub`.

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>Recursos adicionais

- [Gerenciar o Barramento de Serviço com o PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Como criar filas, tópicos e assinaturas do Barramento de Serviço usando um script do PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Como criar um namespace do Barramento de Serviço e um Hub de Eventos usando um script do PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alguns scripts prontos também estão disponíveis para download:

- [Scripts do PowerShell do Barramento de Serviço](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Opções de compra]: https://azure.microsoft.com/pricing/purchase-options/
[Ofertas para membros]: https://azure.microsoft.com/pricing/member-offers/
[Teste Gratuito]: https://azure.microsoft.com/pricing/free-trial/
[Instalar e configurar o PowerShell do Azure]: /powershell/azureps-cmdlets-docs
[API do .NET para Hubs de Notificação]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
