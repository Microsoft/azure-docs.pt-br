---
title: Depurador de Instantâneos do Application Insights do Azure para aplicativos .NET | Microsoft Docs
description: Depure instantâneos são coletados automaticamente quando exceções forem geradas na produção de aplicativos .NET
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: brahmnes
ms.date: 03/07/2019
ms.author: mbullwin
ms.openlocfilehash: 669b4d65798a553188a2b99080b72ffc7cd9e898
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783549"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Depurar instantâneos em exceções em aplicativos .NET
Quando ocorrer uma exceção, você pode coletar automaticamente um Instantâneo de Depuração de seu aplicativo web ativo. O instantâneo mostra o estado do código-fonte e variáveis no momento em que a exceção foi lançada. O Depurador de Instantâneo (visualização) no [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) monitora a telemetria de exceção de seu aplicativo Web. Ele coleta instantâneos em suas exceções com mais lançamentos para que você tenha as informações necessárias para diagnosticar problemas na produção. Inclua o [Pacote de coletor de instantâneos do NuGet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo e, opcionalmente, configure parâmetros de coleta em [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Os instantâneos aparecem nas [exceções](../../azure-monitor/app/asp-net-exceptions.md) no portal do Application Insights.

Você pode exibir instantâneos de depuração no portal para ver a pilha de chamadas e inspecionar variáveis em cada quadro da pilha de chamadas. Para obter uma experiência de depuração mais poderosa com o código-fonte, abra instantâneos com o Visual Studio 2017 Enterprise. No Visual Studio, também é possível [configurar o Snappoints para fazer instantâneos interativamente](https://aka.ms/snappoint) sem esperar por uma exceção.

Instantâneos de depuração são armazenados por sete dias. Essa política de retenção é definida por aplicativo. Se for necessário aumentar esse valor, você poderá solicitar o aumento abrindo um caso de suporte no portal do Azure.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Habilitar o depurador de instantâneo do Application Insights para seu aplicativo
Coleta de instantâneo está disponível para:
* Aplicativos ASP.NET e do .NET framework com o .NET Framework 4.5 ou posterior.
* Aplicativos do .NET Core 2.0 e Núcleo do ASP.NET Core 2.0 em execução no Windows.

Os ambientes a seguir são suportados:

* [Serviço de Aplicativo do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de nuvem do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) executando a família de SO 4 ou posterior
* [Os serviços do Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) em execução no Windows Server 2012 R2 ou posterior
* [Conjuntos de dimensionamento de máquinas virtuais e máquinas virtuais do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) executando o Windows Server 2012 R2 ou posterior
* [Máquinas virtuais ou físicas locais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) executando o Windows Server 2012 R2 ou posterior

> [!NOTE]
> Não há suporte para aplicativos cliente (por exemplo, WPF, Windows Forms ou UWP).

Se você habilitou o depurador de instantâneo, mas não estiver vendo os instantâneos, verifique nossos [guia de solução de problemas](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Conceder permissões

O acesso a instantâneos é protegido por RBAC (controle de acesso baseado em função). Para inspecionar um instantâneo, primeiro você deve ser adicionado à função necessária por um proprietário da assinatura.

> [!NOTE]
> Proprietários e colaboradores não têm automaticamente essa função. Se quiserem exibir instantâneos, eles deverão ser adicionados à função.

Os proprietários de assinaturas deverão atribuir a função `Application Insights Snapshot Debugger` aos usuários que inspecionarão os instantâneos. Essa função pode ser atribuída a usuários individuais ou a grupos por proprietários de assinatura para o recurso Application Insights de destino ou seu grupo de recursos ou a assinatura.

1. Navegue até o recurso Application Insights no portal do Azure.
1. Clique em **Controle de acesso (IAM)**.
1. Clique no botão **+Adicionar atribuição de função**.
1. Selecione o **Depurador de Instantâneos do Application Insights** na lista suspensa **Funções**.
1. Procure e insira um nome para o usuário a ser adicionado.
1. Clique no botão **Salvar** para adicionar o usuário à função.


> [!IMPORTANT]
> Os instantâneos potencialmente podem conter informações pessoais e outras informações confidenciais em valores de parâmetro e variável.

## <a name="view-snapshots-in-the-portal"></a>Exibir instantâneos no Portal

Depois que ocorreu uma exceção em seu aplicativo e um instantâneo foi criado, você deve ter instantâneos para exibir. Pode levar de 5 a 10 minutos de uma exceção que ocorre para um instantâneo pronto e podiam ser exibido no portal. Para exibir instantâneos, na **falha** painel, selecione o **operações** botão ao exibir o **operações** guia ou selecione o **exceções**botão ao exibir o **exceções** guia:

![Falhas de página](./media/snapshot-debugger/failures-page.png)

Selecione uma operação ou uma exceção no painel direito para abrir o **detalhes de transação de ponta a ponta** painel e, em seguida, selecione o evento de exceção. Se um instantâneo está disponível para a exceção apresentada, um **abrir instantâneo de depuração** botão aparece no painel direito com os detalhes para o [exceção](../../azure-monitor/app/asp-net-exceptions.md).

![Botão Abrir Instantâneo de Depuração na exceção](./media/snapshot-debugger/e2e-transaction-page.png)

Na exibição do Instantâneo de Depuração, você verá uma pilha de chamadas e um painel de variáveis. Quando você seleciona quadros da pilha de chamadas no painel de pilha de chamadas. permite exibir as variáveis locais e os parâmetros da chamada de função no painel de variáveis.

![Exibir Instantâneo de Depuração no portal](./media/snapshot-debugger/open-snapshot-portal.png)

Os instantâneos podem conter informações confidenciais e, por padrão, não estão visíveis. Para exibir instantâneos, você deve ter a função `Application Insights Snapshot Debugger` atribuída a você.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Exibir instantâneos no Visual Studio 2017 Enterprise ou superior
1. Clique no botão **Baixar Instantâneo** para baixar um arquivo `.diagsession`, que pode ser aberto pelo Visual Studio 2017 Enterprise.

2. Para abrir o arquivo `.diagsession`, você precisa ter o componente Depurador de Instantâneo do Visual Studio instalado. O Depurador de Instantâneos do Visual Studio é um componente necessário da carga de trabalho do ASP.net no VS e pode ser selecionado na lista Componente Individual no instalador do VS. Se você estiver usando uma versão do Visual Studio 2017 anterior à versão 15.5, você precisará instalar a extensão do [marketplace VS](https://aka.ms/snapshotdebugger).

3. Depois de abrir o arquivo de instantâneo, será exibida a página Depuração de Minidespejo no Visual Studio. Clique em **Depurar Código Gerenciado** para iniciar a depuração de instantâneo. O instantâneo abre a linha de código em que a exceção foi lançada e você pode depurar o estado atual do processo.

    ![Exibir instantâneo de depuração no Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

O instantâneo baixado contém os arquivos de símbolo encontrados no servidor de aplicativos Web. Esses arquivos de símbolo são necessários para associar dados do instantâneo ao código-fonte. Para aplicativos do Serviço de Aplicativo, habilite a implantação de símbolo quando você publicar seus aplicativos Web.

## <a name="how-snapshots-work"></a>Como os instantâneos funcionam

O Coletor de Instantâneo é implementado como um [Processador do Application Insights Telemetry](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Quando seu aplicativo é executado, o Processador de Telemetria do Coletor de Instantâneo será adicionado ao pipeline de telemetria do aplicativo.
Cada vez que o aplicativo chama [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions), o Coletor de Instantâneo calcula uma ID do problema do tipo de exceção sendo gerada e o método que a gerou.
Cada vez que o aplicativo chama TrackException, um contador é incrementado para a ID do Problema apropriada. Quando o contador alcançar o valor `ThresholdForSnapshotting`, a ID do Problema será adicionada a um Plano de Coleta.

O Snapshot Collector também monitora as exceções geradas assinando o evento [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception). Quando esse evento for acionado, a ID do Problema da exceção será calculada e comparada com as IDs de Problema no Plano de Coleta.
Se houver uma correspondência, será criado um instantâneo do processo em execução. O instantâneo será atribuído a um identificador exclusivo e a exceção será marcada com esse identificador. Depois que o manipulador FirstChanceException retornar, a exceção lançada será processada como normal. Por fim, a exceção acessa o método TrackException novamente, em que, junto com o identificador de instantâneo, será relatada ao Application Insights.

O processo principal continuará a executar e atender o tráfego para os usuários com pouca interrupção. Enquanto isso, o instantâneo será entregue ao processo Carregador de Instantâneo. O Carregador de Instantâneo criará um minidespejo e o carregará no Application Insights junto com qualquer arquivo de símbolo (.pdb) relevante.

> [!TIP]
> - Um instantâneo do processo é um clone suspenso do processo em execução.
> - Criar o instantâneo leva cerca de 10 a 20 milissegundos.
> - O valor padrão para `ThresholdForSnapshotting` é 1. Esse também é o valor mínimo. Portanto, seu aplicativo deve disparar a mesma exceção **duas vezes** antes de um instantâneo ser criado.
> - Defina `IsEnabledInDeveloperMode` como true se desejar gerar instantâneos durante a depuração no Visual Studio.
> - A taxa de criação de instantâneos é limitada pela configuração `SnapshotsPerTenMinutesLimit`. Por padrão, o limite é um instantâneo a cada dez minutos.
> - Não é possível carregar mais de 50 instantâneos por dia.

## <a name="limitations"></a>Limitações

O período de retenção de dados padrão é 7 dias. Para cada instância do Application Insights, um número máximo de 50 instantâneos é permitido por dia.

### <a name="publish-symbols"></a>Publicar símbolos
O Depurador de Instantâneo requer que os arquivos de símbolo no servidor de produção decodifiquem variáveis e ofereçam uma experiência de depuração no Visual Studio.
A versão 15.2 (ou superior) do Visual Studio 2017 publica símbolos para builds de versão por padrão ao publicar no Serviço de Aplicativo. Em versões anteriores, você precisa adicionar a seguinte linha ao arquivo `.pubxml` de seu perfil de publicação para que os símbolos sejam publicados no modo de versão:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para a Computação do Azure e outros tipos, certifique-se de que os arquivos de símbolo estejam na mesma pasta que o arquivo .dll do aplicativo principal (geralmente, `wwwroot/bin`) ou que estejam disponíveis no caminho atual.

### <a name="optimized-builds"></a>Builds otimizados
Em alguns casos, variáveis locais não podem ser exibidas em builds de versão devido a otimizações aplicadas pelo compilador JIT.
No entanto, nos Serviços de Aplicativos do Azure, o Coletor de Instantâneo pode cancelar a otimização dos métodos que geraram a exceção que fazem parte do Plano de Coleta.

> [!TIP]
> Instale a extensão de site do Application Insights no seu Serviço de Aplicativo para obter suporte de desotimização.

## <a name="next-steps"></a>Próximas etapas
Habilite o depurador de instantâneo do Application Insights para seu aplicativo:

* [Serviço de Aplicativo do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Nuvem do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviços do Service Fabric do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure e Conjuntos de Dimensionamento de Máquinas Virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais ou físicas locais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Além do depurador de instantâneo do Application Insights:
 
* [Definir snappoints em seu código](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) para obter instantâneos sem esperar por uma exceção.
* [Diagnosticar exceções em seus aplicativos Web](../../azure-monitor/app/asp-net-exceptions.md) explica como deixar as exceções mais visíveis para o Application Insights.
* A [Detecção Inteligente](../../azure-monitor/app/proactive-diagnostics.md) descobre automaticamente anomalias de desempenho.
