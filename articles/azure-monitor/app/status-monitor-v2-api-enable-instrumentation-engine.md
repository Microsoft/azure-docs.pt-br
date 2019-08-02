---
title: 'Referência da API do Azure Status Monitor v2: Habilitar mecanismo de instrumentação | Microsoft Docs'
description: Referência da API do Status Monitor v2. Enable-InstrumentationEngine. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos Web ASP.NET hospedados localmente, em VMs ou no Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: d4683a1cad5172f7104e745433bd141bcf36d56f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326383"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine"></a>API Status Monitor v2: Habilitar-InstrumentationEngine

Este artigo descreve um cmdlet que é membro do [módulo AZ. ApplicationMonitor do PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>DESCRIÇÃO

Habilita o mecanismo de instrumentação definindo algumas chaves do registro.
Reinicie o IIS para que as alterações entrem em vigor.

O mecanismo de instrumentação pode complementar os dados coletados pelos SDKs do .NET.
Ele coleta eventos e mensagens que descrevem a execução de um processo gerenciado. Esses eventos e mensagens incluem códigos de resultado de dependência, verbos HTTP e [texto de comando SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Habilite o mecanismo de instrumentação se:
- Você já habilitou o monitoramento com o cmdlet Enable, mas não habilitou o mecanismo de instrumentação.
- Você instrumentou manualmente seu aplicativo com os SDKs do .NET e deseja coletar telemetria adicional.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador.

> [!NOTE] 
> - Esse cmdlet requer que você examine e aceite nossa licença e nossa política de privacidade.
> - O mecanismo de instrumentação adiciona sobrecarga adicional e está desativado por padrão.

## <a name="examples"></a>Exemplos

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>parâmetros

### <a name="-acceptlicense"></a>-AcceptLicense
**Opcional** Use essa opção para aceitar a licença e a política de privacidade em instalações sem periféricos.

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use essa opção para gerar logs detalhados.

## <a name="output"></a>Saída


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Exemplo de saída de habilitação do mecanismo de instrumentação com êxito

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Próximas etapas

  Exiba sua telemetria:
 - [Explore](../../azure-monitor/app/metrics-explorer.md) as métricas para monitorar o desempenho e o uso.
- [Pesquise eventos e logs](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- Use a [análise](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Crie painéis](../../azure-monitor/app/overview-dashboard.md).
 
 Adicione mais telemetria:
 - [Crie testes da Web](monitor-web-app-availability.md) para garantir que seu site permaneça ativo.
- [Adicione telemetria de cliente Web](../../azure-monitor/app/javascript.md) para ver exceções do código de página da Web e para habilitar chamadas de rastreamento.
- [Adicione o SDK do Application insights ao seu código](../../azure-monitor/app/asp-net.md) para que você possa inserir chamadas de rastreamento e log.
 
 Faça mais com Status Monitor v2:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) status monitor v2.
 - [Obtenha a configuração](status-monitor-v2-api-get-config.md) para confirmar que as configurações foram registradas corretamente.
 - [Obtenha o status](status-monitor-v2-api-get-status.md) para inspecionar o monitoramento.
