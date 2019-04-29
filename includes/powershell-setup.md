---
services: virtual-machines
title: Configurando o PowerShell
author: JoeDavies-MSFT
solutions: ''
manager: timlt
editor: tysonn
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/12/2015
ms.author: rasquill
ms.openlocfilehash: b96e8e6e31817f6d261f41dbf3b3047dd49c29ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485305"
---
## <a name="setting-up-powershell"></a>Configurando o PowerShell
Antes de poder usar o PowerShell do Azure, siga estas etapas.

### <a name="verify-powershell-versions"></a>Verificar as versões do PowerShell
Para poder usar o Windows PowerShell, você deverá ter o Windows PowerShell Versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, digite este comando no prompt de comando do Windows PowerShell.

    $PSVersionTable

Você deverá ver algo assim.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Verifique se o valor de **PSVersion** é 3.0 ou 4.0. Para instalar uma versão compatível, consulte [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

Você também deverá ter o PowerShell do Azure versão 0.8.0 ou posterior. Você pode verificar a versão do Azure PowerShell instalada com o comando este comando no prompt de comando do Azure PowerShell.

    Get-Module azure | format-table version

Você deverá ver algo assim.

    Version
    -------
    0.8.16.1

Para obter instruções e um link para a versão mais recente, veja [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="set-your-azure-account-and-subscription"></a>Definir sua conta e assinatura do Azure
Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

Abra um prompt de comando do Azure PowerShell e entre no Microsoft Azure com este comando.

    Add-AzureAccount

Se você tiver várias assinaturas do Azure, você pode listar suas assinaturas do Azure com este comando.

    Get-AzureSubscription

Você receberá o seguinte tipo de informações:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName : 
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Você pode definir a assinatura do Azure atual ao executar estes comandos no prompt de comando do Azure PowerShell. Substitua tudo que estiver entre aspas, incluindo os caracteres < e >, pelo nome correto.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current    

Para saber mais sobre contas e assinaturas do Azure, confira [Como: Conectar-se com as suas assinaturas](/powershell/azureps-cmdlets-docs#Connect).

