---
title: Solucionar Problemas de Controle de Alterações e Inventário de Automação do Azure
description: Este artigo informa como solucionar problemas e resolver dúvidas com o recurso Controle de Alterações e Inventário de Automação do Azure.
services: automation
ms.subservice: change-inventory-management
ms.date: 02/15/2021
ms.topic: troubleshooting
ms.openlocfilehash: dd027f94edad580836f0afb8c7293c81ca77605a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101723819"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Solucionar problemas do Controle de Alterações e do Inventário

Este artigo descreve como solucionar problemas e resolver dúvidas de Controle de Alterações e Inventário de Automação do Azure. Para obter informações gerais sobre Controle de Alterações e Inventário, confira [Visão Geral de Controle de Alterações e Inventário](../change-tracking/overview.md).

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Cenário: O computador já está registrado em outra conta

### <a name="issue"></a>Problema

Você vê a seguinte mensagem de erro:

```error
Unable to Register Machine for Change Tracking, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

O computador já foi implantado em outro espaço de trabalho para Controle de Alterações.

### <a name="resolution"></a>Resolução

1. Verificar se seu computador envia relatórios para o espaço de trabalho correto. Para obter orientação sobre como verificar isso, consulte [verificar a conectividade do agente para Azure monitor](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Verifique também se esse espaço de trabalho está vinculado à sua conta de automação do Azure. Para confirmar, acesse sua conta de automação e selecione **Espaço de trabalho vinculado** em **Recursos relacionados**.

1. Verifique se as máquinas aparecem no espaço de trabalho Log Analytics vinculado à sua conta de automação. Execute a consulta a seguir do espaço de trabalho do Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

   Se você não vir seu computador nos resultados da consulta, ele não será verificado recentemente. Provavelmente, há um problema de configuração local. Você deve reinstalar o agente de Log Analytics.

   Se seu computador estiver listado nos resultados da consulta, verifique sob a propriedade Solutions que **changeTracking** está listada. Isso verifica se ele está registrado com Controle de Alterações e inventário. Se não estiver, verifique se há problemas de configuração de escopo. A configuração de escopo determina quais computadores estão configurados para Controle de Alterações e inventário. Para configurar a configuração de escopo para o computador de destino, consulte [habilitar controle de alterações e inventário de uma conta de automação](../change-tracking/enable-from-automation-account.md).

   Em seu espaço de trabalho, execute esta consulta.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

1. Se você obtiver um resultado ```Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota```, a cota definida em seu espaço de trabalho foi atingida, o que impediu que os dados fossem salvos. Em seu espaço de trabalho, acesse **uso e custos estimados**. Selecione um novo **tipo de preço** que permita que você use mais dados ou clique em **limite diário** e remova o limite.

:::image type="content" source="./media/change-tracking/change-tracking-usage.png" alt-text="Uso e custos estimados." lightbox="./media/change-tracking/change-tracking-usage.png":::

Se o problema ainda não tiver sido resolvido, siga as etapas descritas em [Implantar um Hybrid Runbook Worker do Windows](../automation-windows-hrw-install.md) para reinstalar o Hybrid Worker para o Windows. Para o Linux, siga as etapas em  [implantar um Hybrid runbook Worker do Linux](../automation-linux-hrw-install.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Cenário: Os registros do Controle de Alterações e Inventário não estão sendo exibidos nos computadores Windows

#### <a name="issue"></a>Problema

Você não verá os resultados do Controle de Alterações e Inventário dos computadores Windows que foram habilitados para o recurso.

#### <a name="cause"></a>Causa

Esse erro pode ter as seguintes causas:

* O agente do Azure Log Analytics para Windows não está funcionando.
* A comunicação com a conta de Automação está sendo bloqueada.
* Os pacotes de gerenciamento de Controle de Alterações e Inventário não foram baixados.
* A VM que está sendo habilitada pode ter vindo de um computador clonado que não executou o sysprep (Preparação do Sistema) com o agente do Log Analytics para Windows instalado.

#### <a name="resolution"></a>Resolução

No computador do agente do Log Analytics, acesse **C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** e execute os comandos a seguir:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Se você ainda precisar de ajuda, pode coletar informações de diagnóstico e entrar em contato com o suporte.

> [!NOTE]
> O agente do Log Analytics habilita o rastreamento de erros por padrão. Para habilitar mensagens de erro detalhadas como no exemplo anterior, use o parâmetro `VER`. Para obter rastreamentos de informações, use `INF` ao invocar `StartTracing.cmd`.

##### <a name="log-analytics-agent-for-windows-not-running"></a>O agente do Log Analytics para Windows não está funcionando

Verifique se o agente do Log Analytics para Windows (**HealthService. exe**) está em execução no computador.

##### <a name="communication-to-automation-account-blocked"></a>A comunicação com a conta de Automação foi bloqueada

Verifique o Visualizador de Eventos no computador e procure os eventos com a palavra `changetracking`.

Para saber mais sobre endereços e portas que devem ser permitidos para que o Controle de Alterações e o Inventário funcionem, confira [Planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Pacotes de gerenciamento não baixados

Verifique se os seguintes pacotes de gerenciamento de Controle de Alterações e Inventário foram instalados localmente:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM do computador clonado que não executou o sysprep

Se estiver usando uma imagem clonada, execute o sysprep na imagem primeiro e, em seguida, instale o agente do Log Analytics para Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Cenário: Nenhum resultado de Controle de Alterações e Inventário nos computadores Linux

#### <a name="issue"></a>Problema

Você não verá os resultados do Controle de Alterações e Inventário dos computadores Linux que foram habilitados para o recurso. 

#### <a name="cause"></a>Causa
As possíveis causas específicas para esse problema incluem:
* O agente do Log Analytics para Linux não está funcionando.
* O agente do Log Analytics para Linux não foi configurado corretamente.
* Há conflitos de FIM (monitoramento de integridade de arquivo).

#### <a name="resolution"></a>Resolução 

##### <a name="log-analytics-agent-for-linux-not-running"></a>O agente do Log Analytics para Linux não está funcionando

Verifique se o daemon do agente de Log Analytics para Linux (**omsagent**) está em execução no computador. Execute a consulta a seguir no workspace do Log Analytics vinculado à conta de Automação.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Se você não vir seu computador nos resultados da consulta, ele não fez check-in recentemente. Provavelmente, há um problema de configuração local e você deve reinstalar o agente. Para obter informações sobre instalação e configuração, confira [Coletar dados de log com o agente do Log Analytics](../../azure-monitor/agents/log-analytics-agent.md).

Se o computador aparecer nos resultados da consulta, verifique a configuração de escopo. Confira [Direcionamento das soluções de monitoramento no Azure Monitor](../../azure-monitor/insights/solution-targeting.md).

Para obter mais soluções para esse problema, confira [Problema: Você não vê os dados do Linux](../../azure-monitor/agents/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>O agente do Log Analytics para Linux não foi configurado corretamente

O agente do Log Analytics para Linux pode não estar configurado corretamente para a coleção de saída de linha de comando e de log, usando a ferramenta do Coletor de logs do OMS. Confira [Visão Geral do Controle de Alterações e Inventário](../change-tracking/overview.md).

##### <a name="fim-conflicts"></a>Conflitos do FIM

O recurso FIM da Central de Segurança do Azure pode estar validando incorretamente a integridade dos arquivos Linux. Verifique se o FIM está funcionando e se foi configurado corretamente para o monitoramento de arquivos Linux. Confira [Visão Geral do Controle de Alterações e Inventário](../change-tracking/overview.md).

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar seu problema aqui ou não conseguir resolvê-lo, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure nos [Fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Suporte do Azure conecta a Comunidade do Azure a respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.