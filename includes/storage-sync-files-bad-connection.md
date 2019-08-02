---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 359347e41264711a6ac0fa4d2dd0c3633590e917
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171995"
---
Esse erro pode ocorrer sempre que o serviço de Sincronização de Arquivos do Azure está acessível do servidor. Você pode solucionar esse erro trabalhando nas seguintes etapas:

1. Verifique se o serviço Windows `FileSyncSvc.exe` não está bloqueado pelo firewall.
2. Verifique se a porta 443 está aberta para conexões de saída para o serviço de Sincronização de Arquivos do Azure. Você pode fazer isso com o cmdlet `Test-NetConnection`. A URL para o espaço reservado `<azure-file-sync-endpoint>` abaixo pode ser encontrada no documento de [Configurações de proxy e firewall de Sincronização de Arquivos do Azure](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Verifique se a configuração de proxy está definida conforme o esperado. Isso pode ser feito com o cmdlet `Get-StorageSyncProxyConfiguration`. Mais informações sobre como definir a configuração de proxy para Sincronização de Arquivos do Azure podem ser encontradas em [Configurações de proxy e firewall da Sincronização de Arquivos do Azure](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Contate seu administrador de rede para obter mais assistência para a solução de problemas de conectividade de rede.