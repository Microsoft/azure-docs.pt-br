---
title: Rastrear o fluxo no aplicativo serviços de nuvem (clássico) com Diagnóstico do Azure
description: Adicione mensagens de rastreamento a um aplicativo do Azure para ajudar a depurar, medir o desempenho, monitorar, realizar a análise de tráfego e muito mais.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b00bb28128cfe9a2e701647ad174ea2c9dd458e4
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742118"
---
# <a name="trace-the-flow-of-a-cloud-services-classic-application-with-azure-diagnostics"></a>Rastrear o fluxo de um aplicativo de serviços de nuvem (clássico) com Diagnóstico do Azure

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

O rastreamento é uma maneira de você monitorar a execução de seu aplicativo enquanto ele é executado. Você pode usar as classes [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace), [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) e [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) para registrar informações sobre erros e execução do aplicativo em logs, arquivos de texto ou outros dispositivos para análise posterior. Para obter mais informações sobre rastreamento, consulte [Rastreamento e instrumentação de aplicativos](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Usar instruções e opções de rastreamento
Implemente o rastreamento em seu aplicativo de Serviços de Nuvem adicionando [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) à configuração do aplicativo e fazendo chamadas a System.Diagnostics.Trace ou System.Diagnostics.Debug no código do aplicativo. Use o arquivo de configuração *app.config* para funções de trabalho e *web.config* para funções Web. Quando você cria um novo serviço hospedado usando um modelo do Visual Studio, o Diagnóstico do Azure é adicionado automaticamente ao projeto e DiagnosticMonitorTraceListener é adicionado ao arquivo de configuração apropriado para as funções que você adiciona.

Para obter informações sobre como incluir instruções de rastreamento, consulte [Como: adicionar instruções de rastreamento ao código do aplicativo](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Incluindo [Opções de Rastreamento](/dotnet/framework/debug-trace-profile/trace-switches) em seu código, você pode controlar se o rastreamento ocorre e qual a sua extensão. Isso permite monitorar o status do aplicativo em um ambiente de produção. Isso é particularmente importante em um aplicativo de negócios que usa vários componentes executados em vários computadores. Para obter mais informações, consulte [Como: configurar opções de rastreamento](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configurar o ouvinte de rastreamento em um aplicativo do Azure
Rastreamento, depuração e TraceSource exigem que você configure "ouvintes" para coletar e registrar as mensagens que são enviadas. Os ouvintes coletam, armazenam e roteiam mensagens de rastreamento. Eles direcionam a saída de rastreamento para um destino apropriado, como um log, uma janela ou um arquivo de texto. O Diagnóstico do Azure usa a classe [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) .

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

Para obter mais informações sobre ouvintes, veja [Ouvintes de rastreamento](/dotnet/framework/debug-trace-profile/trace-listeners).

Depois de concluir as etapas para adicionar o ouvinte, você pode adicionar instruções de rastreamento ao código.

### <a name="to-add-trace-statement-to-your-code"></a>Para adicionar a instrução de rastreamento ao código
1. Abra um arquivo de origem para o aplicativo. Por exemplo, o arquivo \<RoleName>.cs para a função de trabalho ou função Web.
2. Adicione a seguinte diretiva using se ela ainda não tiver sido adicionada:
    ```
        using System.Diagnostics;
    ```
3. Adicione instruções Trace em que você deseja capturar informações sobre o estado do aplicativo. Você pode usar diversos métodos para formatar a saída da instrução Trace. Para obter mais informações, veja [Como adicionar instruções de rastreamento ao código do aplicativo](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Salve o arquivo de origem.




