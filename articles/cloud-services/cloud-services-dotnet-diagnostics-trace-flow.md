---
title: "Rastrear o fluxo em um Aplicativo de Serviços de Nuvem com o Diagnóstico do Azure | Microsoft Docs"
description: "Adicione mensagens de rastreamento a um aplicativo do Azure para ajudar a depurar, medir o desempenho, monitorar, realizar a análise de tráfego e muito mais."
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: 
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: robb
ms.openlocfilehash: 35b4a4270846c54a1ca760e803ef7adba60cf03b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Rastrear o fluxo de um aplicativo de Serviços de Nuvem com o Diagnóstico do Azure
O rastreamento é uma maneira de você monitorar a execução de seu aplicativo enquanto ele é executado. Você pode usar as classes [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx) e [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) para registrar informações sobre erros e execução do aplicativo em logs, arquivos de texto ou outros dispositivos para análise posterior. Para obter mais informações sobre rastreamento, consulte [Rastreamento e instrumentação de aplicativos](https://msdn.microsoft.com/library/zs6s4h68.aspx).

## <a name="use-trace-statements-and-trace-switches"></a>Usar instruções e opções de rastreamento
Implemente o rastreamento em seu aplicativo de Serviços de Nuvem adicionando [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) à configuração do aplicativo e fazendo chamadas a System.Diagnostics.Trace ou System.Diagnostics.Debug no código do aplicativo. Use o arquivo de configuração *app.config* para funções de trabalho e *web.config* para funções Web. Quando você cria um novo serviço hospedado usando um modelo do Visual Studio, o Diagnóstico do Azure é adicionado automaticamente ao projeto e DiagnosticMonitorTraceListener é adicionado ao arquivo de configuração apropriado para as funções que você adiciona.

Para obter informações sobre como incluir instruções de rastreamento, consulte [Como: adicionar instruções de rastreamento ao código do aplicativo](https://msdn.microsoft.com/library/zd83saa2.aspx).

Incluindo [Opções de Rastreamento](https://msdn.microsoft.com/library/3at424ac.aspx) em seu código, você pode controlar se o rastreamento ocorre e qual a sua extensão. Isso permite monitorar o status do aplicativo em um ambiente de produção. Isso é particularmente importante em um aplicativo de negócios que usa vários componentes executados em vários computadores. Para obter mais informações, consulte [Como: configurar opções de rastreamento](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configurar o ouvinte de rastreamento em um aplicativo do Azure
Rastreamento, depuração e TraceSource exigem que você configure "ouvintes" para coletar e registrar as mensagens que são enviadas. Os ouvintes coletam, armazenam e roteiam mensagens de rastreamento. Eles direcionam a saída de rastreamento para um destino apropriado, como um log, uma janela ou um arquivo de texto. O Diagnóstico do Azure usa a classe [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) .

Antes de concluir o procedimento a seguir, você deve inicializar o monitor de diagnóstico do Azure. Para fazer isso, consulte [Habilitando o diagnóstico no Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Observe que, se você usar os modelos fornecidos pelo Visual Studio, a configuração do ouvinte será adicionada automaticamente para você.

### <a name="add-a-trace-listener"></a>Adicionar um ouvinte de rastreamento
1. Abra o arquivo web.config ou app.config para sua função.
2. Adicione o seguinte código ao arquivo. Altere o atributo Version para usar o número de versão do assembly que está sendo referenciado. A versão do assembly não é alterada, necessariamente, com cada versão do SDK do Azure, a menos que existam atualizações a ele.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Verifique se você tem uma referência de projeto ao assembly Microsoft.WindowsAzure.Diagnostics. Atualize o número de versão no xml acima para que ele corresponda à versão do assembly referenciado Microsoft.WindowsAzure.Diagnostics.
   > 
   > 
3. Salve o arquivo de configuração.

Para obter mais informações sobre ouvintes, veja [Ouvintes de rastreamento](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Depois de concluir as etapas para adicionar o ouvinte, você pode adicionar instruções de rastreamento ao código.

### <a name="to-add-trace-statement-to-your-code"></a>Para adicionar a instrução de rastreamento ao código
1. Abra um arquivo de origem para o aplicativo. Por exemplo, o arquivo <RoleName>.cs para a função de trabalho ou função Web.
2. Adicione a seguinte instrução using se ainda não tiver sido adicionada:
    ```
        using System.Diagnostics;
    ```
3. Adicione instruções Trace em que você deseja capturar informações sobre o estado do aplicativo. Você pode usar diversos métodos para formatar a saída da instrução Trace. Para obter mais informações, veja [Como adicionar instruções de rastreamento ao código do aplicativo](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Salve o arquivo de origem.

