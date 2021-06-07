---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4407ffb9ab16a720ef00a288d72b0fb4c2dbced1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774546"
---
Esse erro pode ocorrer sempre que o serviço de Sincronização de Arquivos do Azure está acessível do servidor. Você pode solucionar esse erro trabalhando nas seguintes etapas:

1. Verifique se o serviço Windows `FileSyncSvc.exe` não está bloqueado pelo firewall.
2. Verifique se a porta 443 está aberta para conexões de saída para o serviço de Sincronização de Arquivos do Azure. Você pode fazer isso com o cmdlet `Test-NetConnection`. A URL para o espaço reservado `<azure-file-sync-endpoint>` abaixo pode ser encontrada no documento de [Configurações de proxy e firewall de Sincronização de Arquivos do Azure](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Verifique se a configuração de proxy está definida conforme o esperado. Isso pode ser feito com o cmdlet `Get-StorageSyncProxyConfiguration`. Mais informações sobre como definir a configuração de proxy para Sincronização de Arquivos do Azure podem ser encontradas em [Configurações de proxy e firewall da Sincronização de Arquivos do Azure](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Use o cmdlet Test-StorageSyncNetworkConnectivity para verificar a conectividade de rede para os pontos de extremidade de serviço. Para saber mais, confira [Testar conectividade de rede para pontos de extremidade de serviço](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).    

5. Contate seu administrador de rede para obter mais assistência para a solução de problemas de conectividade de rede.