---
title: Solucionando problemas do SQL insights (versão prévia)
description: Solução de problemas do SQL insights no Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 85a3505dd347b96036c28c85c089afa04e3e3bd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609029"
---
# <a name="troubleshooting-sql-insights-preview"></a>Solucionando problemas do SQL insights (versão prévia)
Para solucionar problemas de coleta de dados no SQL insights, verifique o status do computador de monitoramento na guia **Gerenciar perfil** . Isso terá um dos seguintes Estados:

- Coletar 
- Não coletando 
- Coletando com erros 
 
Clique no **status** para analisar para ver os logs e mais detalhes, o que pode ajudá-lo a resolver o problema. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Status da máquina de monitoramento":::

## <a name="not-collecting-state"></a>Não coletando estado 
O computador de monitoramento tem um estado de *não coletar* se não houver dados em *InsightsMetrics* para SQL nos últimos 10 minutos. 

O SQL insights usa a seguinte consulta para recuperar essas informações:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Verifique se há algum log de Telegraf que ajuda a identificar a causa do problema. Se houver entradas de log, você poderá clicar em *não coletar* e verificar os logs e as informações de solução de problemas comuns. 


Se não houver nenhum log, você deverá verificar os logs na máquina virtual de monitoramento para os seguintes serviços instalados por duas extensões de máquina virtual:

- Microsoft. Azure. monitor. AzureMonitorLinuxAgent 
  - Serviço: MDSD 
- Microsoft. Azure. monitor. cargas de trabalho. Workload. WLILinuxExtension 
  - Serviço: WLI 
  - Serviço: MS-Telegraf 
  - Serviço: TD-Agent-bit-WLI 
  - Log de extensão para verificar falhas de instalação:/var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 



### <a name="wli-service-logs"></a>logs do serviço WLI 

Logs de serviço: `/var/log/wli.log`

Para ver os logs recentes: `tail -n 100 -f /var/log/wli.log`
 

Se você vir o seguinte log de erros, isso indica um problema com o serviço **MDSD** .

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>Logs do serviço Telegraf 

Logs de serviço: `/var/log/ms-telegraf/telegraf.log`

Para ver os logs recentes: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log` para ver os logs de erros e avisos recentes: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 A configuração usada pelo Telegraf é gerada pelo serviço WLI e colocada em: `/etc/ms-telegraf/telegraf.d/wli`
 
Se uma configuração incorreta for gerada, o serviço MS-Telegraf poderá falhar ao iniciar, verificar se o serviço MS-Telegraf está em execução com o comando: `service ms-telegraf status`

Para ver as mensagens de erro do serviço Telegraf, execute-as manualmente com o seguinte comando: 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>logs do serviço MDSD 

Verifique as [limitações atuais](../agents/azure-monitor-agent-overview.md#current-limitations) do agente de Azure monitor. 


Logs de serviço:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

Para ver os erros recentes: `tail -n 100 -f /var/log/mdsd.err`

 Se você precisar entrar em contato com o suporte, colete as seguintes informações: 

- Faz logon `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- Iniciar sessão `/var/log/waagent.log` 
- Faz logon `/var/log/mdsd*`
- Arquivos em `/etc/mdsd.d/`
- File `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>Configuração de máquina virtual de monitoramento inválida

Uma causa do estado *não coletado* é quando você tem uma configuração de máquina virtual de monitoramento inválida.  A seguir está a configuração padrão:

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

Essa configuração especifica os tokens de substituição a serem usados na configuração de perfil em sua máquina virtual de monitoramento. Ele também permite que você referencie segredos de Azure Key Vault, para que você não tenha os valores secretos em nenhuma configuração, o que é altamente recomendável.

#### <a name="secrets"></a>Segredos
Os segredos são tokens cujos valores são recuperados em tempo de execução de um Azure Key Vault. Um segredo é definido por um par de Key Vault referência e nome do segredo. Isso permite que Azure Monitor obter o valor dinâmico do segredo e usá-lo é referências de configuração de downstream.

Você pode definir quantos segredos forem necessários na configuração, incluindo os segredos armazenados em cofres de chaves separados.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

As permissões para acessar o Key Vault são fornecidas a um Identidade de Serviço Gerenciada na máquina virtual de monitoramento. Azure Monitor espera que a Key Vault forneça pelo menos segredos obter permissão para a máquina virtual. Você pode habilitá-lo no modelo portal do Azure, PowerShell, CLI ou Resource Manager.

#### <a name="parameters"></a>Parâmetros
Parâmetros são tokens que podem ser referenciados na configuração do perfil por meio de modelagem JSON. Os parâmetros têm um nome e um valor. Os valores podem ser qualquer tipo JSON, incluindo objetos e matrizes. Um parâmetro é referenciado na configuração de perfil usando seu nome nessa convenção `.Parameters.<name>` .

Os parâmetros podem referenciar segredos em Key Vault usando a mesma convenção. Por exemplo, `sqlAzureConnections` faz referência ao segredo `telegrafPassword` usando a Convenção `$telegrafPassword` .

Em tempo de execução, todos os parâmetros e segredos serão resolvidos e mesclados com a configuração do perfil para construir a configuração real a ser usada no computador.

> [!NOTE]
> Os nomes de parâmetro de `sqlAzureConnections` , `sqlVmConnections` e `sqlManagedInstanceConnections` são todos necessários na configuração mesmo que você não tenha cadeias de conexão que você fornecerá para alguns deles.


## <a name="collecting-with-errors-state"></a>Coletando com estado de erros
O computador de monitoramento estará no estado *coletando com erros* se houver pelo menos um log de *InsightsMetrics* , mas também houver erros na tabela de *operação* .

O SQL insights usa as seguintes consultas para recuperar essas informações:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```
Operation 
 | where OperationCategory == "WorkloadInsights" 
 | summarize Errors = countif(OperationStatus == 'Error') 
```

Para casos comuns, fornecemos o conhecimento de solução de problemas em nossa exibição de logs: 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Solução de problemas de exibição de logs.":::



## <a name="next-steps"></a>Próximas etapas

- Obtenha detalhes sobre como [habilitar o SQL insights](sql-insights-enable.md).
