---
title: "Introdução ao Azure Data Lake Analytics usando o Azure PowerShell | Microsoft Docs"
description: 'Use o Azure PowerShell para criar uma conta do Data Lake Analytics, criar um trabalho do Data Lake Analytics usando o U-SQL e enviar o trabalho. '
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: faf17bcac66a70fc78bb171e172886fd2dcadca8
ms.contentlocale: pt-br
ms.lasthandoff: 06/16/2017

---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Introdução ao Azure Data Lake Analytics usando o Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como usar o Azure PowerShell para criar contas do Azure Data Lake Analytics e, em seguida, enviar e executar trabalhos do U-SQL. Para saber mais sobre a Análise Data Lake, consulte a [Visão geral da Análise Data Lake do Azure](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter as seguintes informações:

* **Uma conta da Análise Azure Data Lake**. Veja [Introdução ao Data Lake Analytics](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-get-started-portal).
* **Uma estação de trabalho com o PowerShell do Azure**. Consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Este tutorial pressupõe que você já esteja familiarizado com o uso do Azure PowerShell. Em particular, você precisa saber como fazer logon no Azure. Veja a [Introdução ao Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps) se precisar de ajuda.

Para fazer logon com um nome de assinatura:

```
Login-AzureRmAccount -SubscriptionName "ContosoSubscription"
```

Em vez do nome da assinatura, você também pode usar uma id de assinatura para fazer logon:

```
Login-AzureRmAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Se for bem-sucedido, a saída desse comando se parece com o seguinte texto:

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Preparando-se para o tutorial

Os trechos do PowerShell neste tutorial usam essas variáveis para armazenar estas informações:

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Obter informações sobre uma conta do Data Lake Analytics

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Enviar um trabalho do U-SQL

Crie uma variável do PowerShell para manter o script U-SQL.

```
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();

"@
```

Enviar o script.

```
$job = Submit-AdlJob -Account $adla -Name "My Job" –Script $script
```

Como alternativa, você pode salvar o script como um arquivo e enviar com o seguinte comando:

```
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" –ScriptPath $filename
```


Obter o status de um trabalho específico. Continue a usar este cmdlet até perceber que o trabalho foi concluído.

```
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

Em vez de chamar Get-AdlAnalyticsJob repetidamente até que um trabalho seja concluído, você pode usar o cmdlet espera Wait-AdlJob.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Baixe o arquivo de saída.

```
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Consulte também
* Para ver o mesmo tutorial usando outras ferramentas, clique nos seletores de guias na parte superior da página.
* Para aprender a usar o U-SQL, veja [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para obter as tarefas de gerenciamento, confira [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md).

