---
title: 'Referência da API do Azure Status Monitor v2: Desabilitar mecanismo de instrumentação | Microsoft Docs'
description: Referência da API do Status Monitor v2. Disable-InstrumentationEngine. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos Web ASP.NET hospedados localmente, em VMs ou no Azure.
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
ms.openlocfilehash: 827f8555718a22979617c9b0794dce3bd92207f4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326399"
---
# <a name="status-monitor-v2-api-disable-instrumentationengine"></a>API Status Monitor v2: Desabilitar-InstrumentationEngine

Este artigo descreve um cmdlet que é membro do [módulo AZ. ApplicationMonitor do PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>DESCRIÇÃO
Desabilita o mecanismo de instrumentação removendo algumas chaves do registro.
Reinicie o IIS para que as alterações entrem em vigor.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador.

## <a name="examples"></a>Exemplos

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>parâmetros 

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use essa opção para gerar logs detalhados.

## <a name="output"></a>Saída


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Exemplo de saída de desabilitar o mecanismo de instrumentação com êxito

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Próximas etapas

 Faça mais com Status Monitor v2:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) status monitor v2.
