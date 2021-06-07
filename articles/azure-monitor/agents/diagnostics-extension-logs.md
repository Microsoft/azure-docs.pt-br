---
title: Usar o armazenamento de BLOB para o IIS e o armazenamento de tabelas para eventos no Azure Monitor | Microsoft Docs
description: Azure Monitor pode ler os logs dos serviços do Azure que gravam diagnósticos em armazenamento de tabela ou logs do IIS gravados no armazenamento de BLOBs.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: deb6b5f3718c1a7c84e3591bf9abcceb72b785da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054439"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Coletar dados da extensão de diagnóstico do Azure para logs de Azure Monitor
A extensão de diagnóstico do Azure é um [agente no Azure monitor](../agents/agents-overview.md) que coleta dados de monitoramento do sistema operacional convidado dos recursos de computação do Azure, incluindo máquinas virtuais. Este artigo descreve como coletar dados coletados pela extensão de diagnóstico do armazenamento do Azure para Azure Monitor logs.

> [!NOTE]
> O agente de Log Analytics no Azure Monitor normalmente é o método preferencial para coletar dados do sistema operacional convidado em logs de Azure Monitor. Consulte [visão geral dos agentes de Azure monitor](../agents/agents-overview.md) para obter uma comparação detalhada dos agentes.

## <a name="supported-data-types"></a>Tipos de dados com suporte
A extensão de diagnóstico do Azure armazena dados em uma conta de armazenamento do Azure. Para que os logs de Azure Monitor coletem esses dados, eles devem estar nos seguintes locais:

| Tipo de Log | Tipo de recurso | Location |
| --- | --- | --- |
| Logs IIS |Máquinas Virtuais <br> Funções da Web <br> Funções de trabalho |wad-iis-logfiles (Armazenamento de Blobs) |
| syslog |Máquinas Virtuais |LinuxsyslogVer2v0 (Armazenamento de Tabelas) |
| Eventos operacionais do Service Fabric |Nós do Service Fabric |WADServiceFabricSystemEventTable |
| Eventos dos Reliable Actors do Service Fabric |Nós do Service Fabric |WADServiceFabricReliableActorEventTable |
| Arquitetura de Serviços Confiáveis do Service Fabric |Nós do Service Fabric |WADServiceFabricReliableServiceEventTable |
| Log de eventos do Windows |Nós do Service Fabric <br> Máquinas Virtuais <br> Funções da Web <br> Funções de trabalho |WADWindowsEventLogsTable (Armazenamento de Tabelas) |
| Logs do ETW do Windows |Nós do Service Fabric <br> Máquinas Virtuais <br> Funções da Web <br> Funções de trabalho |WADETWEventTable (Armazenamento de Tabelas) |

## <a name="data-types-not-supported"></a>Tipos de dados sem suporte

- Dados de desempenho do sistema operacional convidado
- Logs do IIS de sites do Azure


## <a name="enable-azure-diagnostics-extension"></a>Habilitar a extensão de diagnóstico do Azure
Consulte [instalar e configurar a extensão de diagnóstico do Windows Azure (wad)](../agents/diagnostics-extension-windows-install.md) ou [usar a extensão de diagnóstico do Linux para monitorar métricas e logs](../../virtual-machines/extensions/diagnostics-linux.md) para obter detalhes sobre como instalar e configurar a extensão de diagnóstico. Isso permitirá que você especifique a conta de armazenamento e configure a coleta dos dados que deseja encaminhar para Azure Monitor logs.


## <a name="collect-logs-from-azure-storage"></a>Coletar logs do armazenamento do Azure
Use o procedimento a seguir para habilitar a coleta de dados de extensão de diagnóstico de uma conta de armazenamento do Azure:

1. Na portal do Azure, acesse **log Analytics espaços de trabalho** e selecione seu espaço de trabalho.
1. Clique em **logs de contas de armazenamento** na seção fontes de dados de espaço de **trabalho** do menu.
2. Clique em **Adicionar**.
3. Selecione a **conta de armazenamento** que contém os dados a serem coletados.
4. Selecione o **tipo de dados** que você deseja coletar.
5. O valor de source é preenchido automaticamente com base no tipo de dados.
6. Clique em **OK** para salvar a configuração.
7. Repita para tipos de dados adicionais.

Em aproximadamente 30 minutos, você poderá ver os dados da conta de armazenamento no espaço de trabalho Log Analytics. Você só verá os dados gravados no armazenamento após a configuração ser aplicada. O espaço de trabalho não lê os dados preexistentes da conta de armazenamento.

> [!NOTE]
> O portal não valida se a origem existe na conta de armazenamento ou se novos dados estão sendo gravados.



## <a name="next-steps"></a>Próximas etapas

* [Coletar logs e métricas para serviços do Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) para serviços do Azure com suporte.
* [Habilitar Soluções](../insights/solutions.md) para fornecer informações sobre os dados.
* [Usar consultas de pesquisa](../logs/log-query-overview.md) para analisar os dados.