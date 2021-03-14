---
title: Coleta de eventos do ETW (rastreamento de eventos para Windows) para logs de Azure Monitor de análise
description: Saiba como coletar rastreamento de eventos para Windows (ETW) para análise em logs de Azure Monitor.
services: azure-monitor
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: 096d16bb2c7249ec99f37fbee9d8ffc8f0e45db2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050759"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>Coleta de eventos do ETW (rastreamento de eventos para Windows) para logs de Azure Monitor de análise

O *ETW (rastreamento de eventos para Windows)* fornece um mecanismo para instrumentação de aplicativos de modo de usuário e drivers de modo kernel. O agente de Log Analytics é usado para [coletar eventos do Windows](./data-sources-windows-events.md) gravados nos [canais de ETW](/windows/win32/wes/eventmanifestschema-channeltype-complextype)administrativo e operacional. No entanto, ocasionalmente é necessário capturar e analisar outros eventos, como aqueles gravados no canal analítico.  

## <a name="event-flow"></a>Fluxo de eventos

Para coletar com êxito [eventos ETW baseados em manifesto](/windows/win32/etw/about-event-tracing#types-of-providers) para análise nos Logs de Azure monitor, você deve usar a [extensão de diagnóstico do Azure](./diagnostics-extension-overview.md) para Windows (wad). Nesse cenário, a extensão de diagnóstico atua como o consumidor ETW, gravando eventos no armazenamento do Azure (tabelas) como um repositório intermediário. Aqui, ele será armazenado em uma tabela chamada **WADETWEventTable**. Em seguida, Log Analytics coleta os dados da tabela do armazenamento do Azure, apresentando-os como uma tabela chamada **ETWEvent**.

![Fluxo de eventos](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>Configurando a coleta de logs do ETW

### <a name="step-1-locate-the-correct-etw-provider"></a>Etapa 1: localizar o provedor de ETW correto

Use qualquer um dos comandos a seguir para enumerar os provedores de ETW em um sistema Windows de origem.

Linha de comando:

```
logman query providers
```

PowerShell:
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
Opcionalmente, você pode optar por canalizar essa saída do PowerShell para Out-Gridview para auxiliar na navegação.

Registre o nome do provedor ETW e o GUID que se alinha ao log analítico ou de depuração apresentado no Visualizador de Eventos ou ao módulo para o qual você pretende coletar dados de evento.

### <a name="step-2-diagnostics-extension"></a>Etapa 2: extensão de diagnóstico

Verifique se a *extensão de diagnóstico do Windows* está [instalada](./diagnostics-extension-windows-install.md#install-with-azure-portal) em todos os sistemas de origem.

### <a name="step-3-configure-etw-log-collection"></a>Etapa 3: configurar a coleta de logs do ETW

1. Navegue até a folha **configurações de diagnóstico** da máquina virtual

2. Selecione a guia **logs**

3. Role para baixo e habilite a captura de tela da opção de **eventos ETW (rastreamento de eventos para Windows)** ![ das configurações de diagnóstico](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. Definir o GUID do provedor ou a classe do provedor com base no provedor para o qual você está configurando a coleção

5. Defina o [**nível de log**](/windows/win32/etw/configuring-and-starting-an-event-tracing-session) conforme apropriado

6. Clique nas reticências adjacentes ao provedor fornecido e clique em **Configurar**

7. Verifique se a **tabela de destino padrão** está definida como **etweventtable**

8. Definir um [**filtro de palavra-chave**](/windows/win32/wes/defining-keywords-used-to-classify-types-of-events) , se necessário

9. Salvar as configurações do provedor e do log

Depois que os eventos correspondentes são gerados, você deve começar a ver os eventos ETW que aparecem na tabela **WADetweventtable** no armazenamento do Azure. Você pode usar Gerenciador de Armazenamento do Azure para confirmar isso.

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>Etapa 4: configurar a coleta de conta de armazenamento Log Analytics

Siga [estas instruções](https://docs.microsoft.com/azure/azure-monitor/essentials/diagnostics-extension-logs#collect-logs-from-azure-storage) para coletar os logs do armazenamento do Azure. Uma vez configurados, os dados do evento ETW devem aparecer no Log Analytics na tabela **ETWEvent** .

## <a name="next-steps"></a>Próximas etapas
- Usar [campos personalizados](../logs/custom-fields.md) para criar a estrutura em seus eventos ETW
- Saiba mais sobre [registrar consultas](../logs/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções.
