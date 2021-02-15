---
title: Habilitar o log de recursos no Gerenciador de tráfego do Azure
description: Saiba como habilitar o log de recursos para seu perfil do Gerenciador de tráfego e acessar os arquivos de log criados como resultado.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: f24a4a0d982ff78ca4d6726e950825ed2c784e67
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184533"
---
# <a name="enable-resource-logging-in-azure-traffic-manager"></a>Habilitar o log de recursos no Gerenciador de tráfego do Azure

Este artigo descreve como habilitar a coleta de logs de recursos de diagnóstico e acessar dados de log para um perfil do Gerenciador de tráfego.

Os logs de recursos do Gerenciador de tráfego do Azure podem fornecer informações sobre o comportamento do recurso de perfil do Gerenciador de tráfego. Por exemplo, é possível usar os dados do log do perfil para determinar por que as investigações individuais atingiram o tempo limite em um ponto de extremidade.

## <a name="enable-resource-logging"></a>Habilitar registro em log de recursos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

É possível executar os comandos a seguir no [Azure Cloud Shell](https://shell.azure.com/powershell) ou executando o PowerShell no computador. O Azure Cloud Shell é um shell interativo gratuito. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Se você executar o PowerShell pelo computador, será necessário ter o módulo do Azure PowerShell do Azure, 1.0.0 ou posterior. É possível executar `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se estiver executando o PowerShell localmente, também será necessário executar `Login-AzAccount` entrar no Azure.

1. **Recuperar o perfil do Gerenciador de Tráfego:**

    Para habilitar o log de recursos, você precisa da ID de um perfil do Gerenciador de tráfego. Recupere o perfil do Gerenciador de tráfego para o qual você deseja habilitar o log de recursos com [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). A saída inclui as informações da ID do perfil do Gerenciador de Tráfego.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Habilitar o log de recursos para o perfil do Gerenciador de tráfego:**

    Habilite o log de recursos para o perfil do Gerenciador de tráfego usando a ID obtida na etapa anterior com [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). O comando a seguir armazena logs detalhados do perfil do Gerenciador de Tráfego em uma conta de Armazenamento do Azure especificada. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Verificar configurações de diagnóstico:**

      Verifique as configurações de diagnóstico para o perfil do Gerenciador de Tráfego, usando [Get-AzDiagnosticSetting](/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). O comando a seguir exibe as categorias registradas em log para um recurso.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Assegure-se de que todas as categorias de log associadas ao recurso de perfil do Gerenciador de Tráfego sejam exibidas como habilitadas. Além disso, verifique se a conta de armazenamento está configurada corretamente.

## <a name="access-log-files"></a>Acessar arquivos de log
1. Entre no [portal do Azure](https://portal.azure.com). 
1. Navegue até a conta de Armazenamento do Azure no portal.
2. Na página de **Visão Geral** da conta de armazenamento do Azure, em **Serviços**, selecione **Blobs**.
3. Para **Contêineres**, selecione **insights-logs-probehealthstatusevents**, navegue até o arquivo PT1H.json e clique em **Baixar** para baixar e salvar uma cópia desse arquivo de log.

    ![Acessar arquivos de log do perfil do Gerenciador de Tráfego de um armazenamento de blobs](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Esquema de log do Gerenciador de Tráfego

Todos os logs de recursos disponíveis por meio de Azure Monitor compartilham um esquema comum de nível superior, com flexibilidade para cada serviço para emitir propriedades exclusivas para seus próprios eventos. Para o esquema de logs de recursos de nível superior, consulte [serviços, esquemas e categorias com suporte para logs de recursos do Azure](../azure-monitor/platform/resource-logs-schema.md).

A tabela a seguir inclui o esquema de logs específico para o recurso de perfil do Gerenciador de Tráfego do Azure.

|Nome do campo|Tipo de campo|Definição|Exemplo|
|----|----|---|---|
|EndpointName|Cadeia de caracteres|O nome do ponto de extremidade do Gerenciador de Tráfego cujo status de integridade está sendo registrado.|*myPrimaryEndpoint*|
|Status|Cadeia de caracteres|O status de integridade do ponto de extremidade do Gerenciador de Tráfego que foi investigado. O status pode ser **Operante** ou **Inoperante**.|**Operante**|
|||||

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md)