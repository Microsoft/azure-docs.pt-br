---
title: Monitorar desempenho dos serviços de aplicativos do Azure | Microsoft Docs
description: Monitoramento do desempenho de aplicativos para serviços de aplicativos do Azure. Tempo de resposta e carregamento do gráfico, informações de dependência e definir alertas sobre o desempenho.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 7661066bc2666070c8b3ed9263b1223c09d6c720
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734716"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorar o desempenho do Serviço de Aplicativo do Azure

Habilitar o monitoramento em seus ASP.NET, ASP.NET Core e Node.js aplicativos Web com base em execução em [serviços de Azure app](../../app-service/index.yml) agora é mais fácil do que nunca. Enquanto anteriormente você precisava instalar manualmente uma extensão de site, a extensão/agente mais recente agora é compilado na imagem do serviço de aplicativo por padrão. Este artigo o orientará na habilitação do monitoramento de Application Insights, bem como no fornecimento de diretrizes preliminares para automatizar o processo para implantações em larga escala.

> [!NOTE]
> A adição manual de uma extensão de site Application insights por meio de extensões de **ferramentas de desenvolvimento**  >   foi preterida. Esse método de instalação de extensão foi dependente de atualizações manuais para cada nova versão. A versão estável mais recente da extensão agora é  [pré-instalado](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) como parte da imagem do serviço de aplicativo. Os arquivos estão localizados no `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` e são atualizados automaticamente com cada versão estável. Se você seguir as instruções baseadas em agente para habilitar o monitoramento abaixo, ela removerá automaticamente a extensão preterida para você.

## <a name="enable-application-insights"></a>Habilitar o Application Insights

Há duas maneiras de habilitar o monitoramento de aplicativos para aplicativos hospedados de serviços Azure Apps:

* **Monitoramento de aplicativo baseado em agente** (ApplicationInsightsAgent).  
    * Esse método é o mais fácil de habilitar e nenhuma configuração avançada é necessária. Ele é geralmente chamado de monitoramento de "tempo de execução". Para Azure App serviços, recomendamos, no mínimo, habilitar esse nível de monitoramento e, em seguida, com base em seu cenário específico, você pode avaliar se é necessário um monitoramento mais avançado por meio da instrumentação manual.

* **Instrumentar manualmente o aplicativo por meio do código** instalando o SDK do Application insights.

    * Essa abordagem é muito mais personalizável, mas requer [a adição de uma dependência nos pacotes NuGet do SDK Application insights](./asp-net.md). Esse método, também significa que você precisa gerenciar as atualizações para a versão mais recente dos pacotes por conta própria.

    * Se você precisar fazer chamadas à API personalizada para acompanhar eventos/dependências não capturadas por padrão com o monitoramento baseado em agente, você precisará usar esse método. Confira o [artigo API para eventos e métricas personalizados](./api-custom-events-metrics.md) para saber mais. Essa também é a única opção com suporte para cargas de trabalho baseadas em Linux.

> [!NOTE]
> Se o monitoramento baseado em agente e a instrumentação baseada em SDK manual forem detectados, somente as configurações de instrumentação manual serão respeitadas. Isso é para evitar que dados duplicados sejam enviados. Para saber mais sobre isso, confira a [seção solução de problemas](#troubleshooting) abaixo.

## <a name="enable-agent-based-monitoring"></a>Habilitar o monitoramento baseado em agente

# <a name="aspnet"></a>[ASP.NET](#tab/net)

> [!NOTE]
> Não há suporte para a combinação de APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression. Para obter mais informações, consulte a explicação na [seção solução de problemas](#troubleshooting).


1. **Selecione Application insights** no painel de controle do Azure para seu serviço de aplicativo.

    ![Em Configurações, escolha Insights do aplicativo](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a menos que você já tenha configurado um recurso do Application Insights para este aplicativo. 

     > [!NOTE]
     > Ao clicar em **OK** para criar o novo recurso, você será solicitado a **Aplicar configurações de monitoramento**. Se você selecionar **Continuar**, vinculará seu novo recurso do Application Insights ao seu serviço de aplicativo. Fazer isso também **disparará uma reinicialização do seu serviço de aplicativo**. 

     ![Instrumentar seu aplicativo Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar qual recurso usar, você pode escolher como deseja que o Application Insights colete dados por plataforma para seu aplicativo. O monitoramento de aplicativos ASP.NET é ativado por padrão com dois níveis diferentes de coleção.

    ![Captura de tela mostra a página Application Insights extensões de site com criar novo recurso selecionado.](./media/azure-web-apps/choose-options-new.png)
 
 Veja abaixo um resumo dos dados coletados para cada rota:
        
| Dados | Coleção ASP.NET básica | Coleção recomendada por ASP.NET |
| --- | --- | --- |
| Adiciona as tendências de uso de CPU, memória e E/S |Sim |Sim |
| Coleta as tendências de uso e permite a correlação entre resultados de disponibilidade e transações | Sim |Sim |
| Coleta as exceções não tratadas pelo processo de host | Sim |Sim |
| Aumenta a precisão de métricas de APM com carga quando a amostragem é usada | Sim |Sim |
| Correlaciona microsserviços entre limites de solicitação/dependência | Não (somente recursos APM de instância única) |Sim |

3. Para definir configurações como amostragem, que você poderia controlar anteriormente por meio do arquivo de applicationinsights.config, agora você pode interagir com essas mesmas configurações por meio de configurações de aplicativo com um prefixo correspondente. 

    * Por exemplo, para alterar a porcentagem de amostragem inicial, você pode criar uma configuração de aplicativo de: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` e um valor de `100` .

    * Para obter a lista de configurações de processador de telemetria de amostragem adaptável com suporte, você pode consultar o [código](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) e a [documentação associada](./sampling.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

> [!IMPORTANT]
> Há suporte para as seguintes versões do ASP.NET Core: ASP.NET Core 2,1 e 3,1. As versões 2,0, 2,2 e 3,0 foram desativadas e não têm mais suporte. Atualize para uma [versão com suporte](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) do .NET Core para que a instrumentação automática funcione.

O direcionamento para a estrutura completa de ASP.NET Core, implantação independente e aplicativos baseados em Linux **não têm suporte** no momento com o monitoramento baseado em agente/extensão. (A[Instrumentação manual](./asp-net-core.md) por meio de código funcionará em todos os cenários anteriores.)

1. **Selecione Application insights** no painel de controle do Azure para seu serviço de aplicativo.

    ![Em Configurações, escolha Insights do aplicativo](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a menos que você já tenha configurado um recurso do Application Insights para este aplicativo. 

     > [!NOTE]
     > Ao clicar em **OK** para criar o novo recurso, você será solicitado a **Aplicar configurações de monitoramento**. Se você selecionar **Continuar**, vinculará seu novo recurso do Application Insights ao seu serviço de aplicativo. Fazer isso também **disparará uma reinicialização do seu serviço de aplicativo**. 

     ![Instrumentar seu aplicativo Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar qual recurso usar, você pode escolher como deseja que Application Insights colete dados por plataforma para seu aplicativo. ASP.NET Core oferece a **coleção recomendada** ou **desabilitada** para ASP.NET Core 2,1 e 3,1.

    ![Escolha as opções por plataforma](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Não há suporte para o monitoramento baseado em agente do Windows, para habilitar com o Linux visite a [ documentação do serviço de aplicativoNode.js](../../app-service/configure-language-nodejs.md?pivots=platform-linux#monitor-with-application-insights).

# <a name="java"></a>[Java](#tab/java)

Siga as diretrizes para [Application insights o agente do java 3,0](./java-in-process-agent.md) para habilitar a instrumentação automática para seus aplicativos Java sem alterar seu código.
A integração automática ainda não está disponível para o serviço de aplicativo.

# <a name="python"></a>[Python](#tab/python)

Os aplicativos Web baseados no serviço de aplicativo Python atualmente não dão suporte ao monitoramento automático baseado em agente/extensão. Para habilitar o monitoramento para seu aplicativo Python, você precisa [instrumentar manualmente seu aplicativo](./opencensus-python.md).

---

## <a name="enable-client-side-monitoring"></a>Habilitar monitoramento do lado do cliente

# <a name="aspnet"></a>[ASP.NET](#tab/net)

O monitoramento no lado do cliente é opcional para ASP.NET. Para habilitar o monitoramento do lado do cliente:

* **Configurações** **>** do **Configuração** do
   * Em configurações do aplicativo, crie uma **nova configuração de aplicativo**:

     Nome: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `true`

   * **Salve** as configurações e **Reinicie** seu aplicativo.

Para desabilitar o monitoramento do lado do cliente, remova o par de valor de chave associado das configurações do aplicativo ou defina o valor como false.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

O monitoramento no lado do cliente é **habilitado por padrão** para aplicativos ASP.NET Core com a **coleção recomendada**, independentemente de a configuração do aplicativo ' APPINSIGHTS_JAVASCRIPT_ENABLED ' estar presente.

Se por algum motivo você quiser desabilitar o monitoramento no lado do cliente:

* **Configurações** **>** do **Configuração** do
   * Em configurações do aplicativo, crie uma **nova configuração de aplicativo**:

     nomes `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `false`

   * **Salve** as configurações e **Reinicie** seu aplicativo.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Para habilitar o monitoramento do lado do cliente para seu aplicativo Node.js, você precisa [adicionar manualmente o SDK JavaScript do lado do cliente ao seu aplicativo](./javascript.md).

# <a name="java"></a>[Java](#tab/java)

Para habilitar o monitoramento do lado do cliente para seu aplicativo Java, você precisa [adicionar manualmente o SDK JavaScript do lado do cliente ao seu aplicativo](./javascript.md).

# <a name="python"></a>[Python](#tab/python)

Para habilitar o monitoramento do lado do cliente para seu aplicativo Python, você precisa [adicionar manualmente o SDK JavaScript do lado do cliente ao seu aplicativo](./javascript.md).

---

## <a name="automate-monitoring"></a>Automatizar monitoramento

Para habilitar a coleta de telemetria com Application Insights, somente as configurações do aplicativo precisam ser definidas:

   ![Configurações de aplicativo do serviço de aplicativo com configurações de Application Insights disponíveis](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definições de configurações do aplicativo

|Nome da configuração do aplicativo |  Definição | Valor |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extensão principal, que controla o monitoramento do tempo de execução. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Somente no modo padrão, os recursos essenciais são habilitados para garantir o desempenho ideal. | `default` ou `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controla se o mecanismo de regravação binária `InstrumentationEngine` será ativado. Essa configuração tem implicações de desempenho e afeta o tempo de início/inicialização frio. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controla se o SQL & texto da tabela do Azure será capturado junto com as chamadas de dependência. Aviso de desempenho: o tempo de inicialização a frio do aplicativo será afetado. Essa configuração requer o `InstrumentationEngine` . | `~1` |
|XDT_MicrosoftApplicationInsights_PreemptSdk | Somente para aplicativos ASP.NET Core. Habilita a interoperabilidade (interoperação) com o SDK Application Insights. Carrega a extensão lado a lado com o SDK e a usa para enviar telemetria (desabilita o SDK do Application Insights). |`1`|

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Configurações de aplicativo do serviço de aplicativo com Azure Resource Manager

As configurações de aplicativo para serviços de aplicativos podem ser gerenciadas e configuradas com [modelos de Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). Esse método pode ser usado ao implantar novos recursos do serviço de aplicativo com a automação Azure Resource Manager ou para modificar as configurações de recursos existentes.

A estrutura básica do JSON de configurações do aplicativo para um serviço de aplicativo está abaixo:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Para obter um exemplo de um modelo de Azure Resource Manager com configurações de aplicativo definidas para Application Insights, esse [modelo](https://github.com/Andrew-MSFT/BasicImageGallery) pode ser útil, especificamente a seção que começa na [linha 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatize a criação de um recurso Application Insights e vincule ao seu serviço de aplicativo recém-criado.

Para criar um modelo de Azure Resource Manager com todas as configurações de Application Insights padrão configuradas, inicie o processo como se estivesse criando um novo aplicativo Web com Application Insights habilitado.

Selecionar **Opções de automação**

   ![Menu de criação do aplicativo Web do serviço de aplicativo](./media/azure-web-apps/create-web-app.png)

Essa opção gera o modelo de Azure Resource Manager mais recente com todas as configurações necessárias definidas.

  ![Modelo de aplicativo Web do serviço de aplicativo](./media/azure-web-apps/arm-template.png)

Abaixo está um exemplo, substitua todas as instâncias de  `AppMonitoredSite` pelo nome do seu site:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>Habilitando por meio do PowerShell

Para habilitar o monitoramento de aplicativos por meio do PowerShell, somente as configurações de aplicativo subjacentes precisam ser alteradas. Abaixo está um exemplo, que habilita o monitoramento de aplicativos para um site chamado "AppMonitoredSite" no grupo de recursos "AppMonitoredRG" e configura os dados a serem enviados para a chave de instrumentação "012345678-ABCD-EF01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Atualizar a extensão/agente de monitoramento

### <a name="upgrading-from-versions-289-and-up"></a>Atualizando das versões 2.8.9 e up

A atualização da versão 2.8.9 ocorre automaticamente, sem nenhuma ação adicional. Os novos bits de monitoramento são entregues em segundo plano ao serviço de aplicativo de destino e, na reinicialização do aplicativo, serão coletados.

Para verificar qual versão da extensão você está executando, visite `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Captura de tela do caminho da URL http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Atualização das versões 1.0.0-2.6.5

A partir da versão 2.8.9, a extensão de site pré-instalada é usada. Se você for uma versão anterior, poderá atualizar por meio de uma das duas maneiras:

* [Atualizar habilitando por meio do portal](#enable-application-insights). (Mesmo que você tenha a extensão de Application Insights para Azure App Service instalado, a interface do usuário mostrará apenas o botão **habilitar** . Nos bastidores, a extensão de site particular antigo será removida.)

* [Atualizar por meio do PowerShell](#enabling-through-powershell):

    1. Defina as configurações do aplicativo para habilitar a extensão de site pré-instalada ApplicationInsightsAgent. Consulte [habilitando por meio do PowerShell](#enabling-through-powershell).
    2. Remova manualmente a extensão de site particular chamada Application Insights extensão para Azure App serviço.

Se a atualização for feita a partir de uma versão anterior à 2.5.1, verifique se as DLLs ApplicationInsigths são removidas da pasta bin do aplicativo [consulte as etapas de solução de problemas](#troubleshooting).

## <a name="troubleshooting"></a>Solução de problemas

Abaixo está nosso guia de solução de problemas passo a passo para o monitoramento baseado em extensão/agente para aplicativos baseados em ASP.NET e ASP.NET Core em execução em serviços Azure App.

> [!NOTE]
> A abordagem recomendada para monitorar aplicativos Java é usar a instrumentação automática sem alterar o código. Siga as diretrizes para [Application insights o agente do Java 3,0](./java-in-process-agent.md).


1. Verifique se o aplicativo é monitorado via `ApplicationInsightsAgent` .
    * Verifique se `ApplicationInsightsAgent_EXTENSION_VERSION` a configuração do aplicativo está definida com um valor de "~ 2".
2. Verifique se o aplicativo atende aos requisitos a serem monitorados.
    * Navegue para `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Captura de tela da https://yoursitename.scm.azurewebsites/applicationinsights página de resultados](./media/azure-web-apps/app-insights-sdk-status.png)

    * Confirme se o `Application Insights Extension Status` é `Pre-Installed Site Extension, version 2.8.12.1527, is running.` 
    * Se não estiver em execução, siga as [instruções de monitoramento habilitar Application insights](#enable-application-insights)

    * Confirme se a origem do status existe e se tem a seguinte aparência: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Se um valor semelhante não estiver presente, significa que o aplicativo não está em execução no momento ou não tem suporte. Para garantir que o aplicativo esteja em execução, tente visitar manualmente os pontos de extremidade do aplicativo/URL do aplicativo, o que permitirá que as informações de tempo de execução fiquem disponíveis.

    * Confirme se `IKeyExists` é `true`
        * Se for `false` , adicione `APPINSIGHTS_INSTRUMENTATIONKEY` e `APPLICATIONINSIGHTS_CONNECTION_STRING` com seu GUID iKey às configurações do aplicativo.

    * Confirme se não há entradas para `AppAlreadyInstrumented` , `AppContainsDiagnosticSourceAssembly` e `AppContainsAspNetTelemetryCorrelationAssembly` .
        * Se qualquer uma dessas entradas existir, remova os seguintes pacotes do seu aplicativo: `Microsoft.ApplicationInsights` , `System.Diagnostics.DiagnosticSource` e `Microsoft.AspNet.TelemetryCorrelation` .
        * Somente para aplicativos ASP.NET Core: caso seu aplicativo se refira a qualquer pacote de Application Insights, por exemplo, se você já tiver instrumentado (ou tentado instrumentar) seu aplicativo com o [SDK do ASP.NET Core](./asp-net-core.md), a habilitação da integração do serviço de aplicativo pode não ter efeito e os dados podem não aparecer no Application insights. Para corrigir o problema, no portal, ative "interoperabilidade com o SDK do Application Insights" e você começará a ver os dados em Application Insights 
        > [!IMPORTANT]
        > Esta funcionalidade está em versão prévia 

        ![Habilitar a configuração do aplicativo existente](./media/azure-web-apps/netcore-sdk-interop.png)

        Os dados agora serão enviados usando a abordagem de código, mesmo se Application Insights SDK tiver sido originalmente usado ou tentar ser usado.

        > [!IMPORTANT]
        > Se o aplicativo usou Application Insights SDK para enviar qualquer telemetria, essa telemetria será desabilitada – em outras palavras, telemetria personalizada-se houver, como por exemplo qualquer método Track * () e quaisquer configurações personalizadas, como amostragem, serão desabilitadas. 


### <a name="php-and-wordpress-are-not-supported"></a>Não há suporte para PHP e WordPress

Não há suporte para sites PHP e WordPress. Atualmente, não há um SDK/agente com suporte oficialmente para o monitoramento do lado do servidor dessas cargas de trabalho. No entanto, instrumentar manualmente as transações do lado do cliente em um site do PHP ou do WordPress adicionando o JavaScript do lado do cliente às suas páginas da Web pode ser feito usando o [SDK do JavaScript](./javascript.md).

A tabela a seguir fornece uma explicação mais detalhada do que esses valores significam, suas causas subjacentes e correções recomendadas:

|Valor do problema|Explicação|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | Esse valor indica que a extensão detectou que algum aspecto do SDK já está presente no aplicativo e será retirada. Pode ser devido a uma referência a `System.Diagnostics.DiagnosticSource` ,  `Microsoft.AspNet.TelemetryCorrelation` ou `Microsoft.ApplicationInsights`  | Remova as referências. Algumas dessas referências são adicionadas por padrão de determinados modelos do Visual Studio, e as versões mais antigas do Visual Studio podem adicionar referências ao `Microsoft.ApplicationInsights` .
|`AppAlreadyInstrumented:true` | Se o aplicativo estiver direcionando ASP.NET Core 2,1 ou 2,2, esse valor indica que a extensão detectou que algum aspecto do SDK já está presente no aplicativo e será retirada | Os clientes do .NET Core 2.1, 2.2 são [recomendados](https://github.com/aspnet/Announcements/issues/287) a usar o meta-package do Microsoft.AspNetCore.app em vez disso. Além disso, ative "interoperabilidade com o SDK do Application Insights" no portal (consulte as instruções acima).|
|`AppAlreadyInstrumented:true` | Esse valor também pode ser causado pela presença das DLLs acima na pasta do aplicativo de uma implantação anterior. | Limpe a pasta do aplicativo para garantir que essas DLLs sejam removidas. Verifique o diretório bin do seu aplicativo local e o diretório wwwroot no serviço de aplicativo. (Para verificar o diretório wwwroot do seu aplicativo Web do serviço de aplicativo: ferramentas avançadas (kudu) > console de depuração > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Esse valor indica que a extensão detectou referências a `Microsoft.AspNet.TelemetryCorrelation` no aplicativo e será retirada. | Remova a referência.
|`AppContainsDiagnosticSourceAssembly**:true`|Esse valor indica que a extensão detectou referências a `System.Diagnostics.DiagnosticSource` no aplicativo e será retirada.| Para ASP.NET, remova a referência. 
|`IKeyExists:false`|Esse valor indica que a chave de instrumentação não está presente em AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY` . Possíveis causas: os valores podem ter sido acidentalmente removidos, esquecidos para definir os valores no script de automação, etc. | Verifique se a configuração está presente nas configurações do aplicativo do serviço de aplicativo.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>Não há suporte para APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression

Se você usar APPINSIGHTS_JAVASCRIPT_ENABLED = true nos casos em que o conteúdo é codificado, poderá obter erros como: 

- erro de regravação de URL 500
- 500,53 erro de módulo de regravação de URL com regras de regravação de mensagem de saída não podem ser aplicadas quando o conteúdo da resposta HTTP é codificado (' gzip '). 

Isso ocorre porque a configuração do aplicativo APPINSIGHTS_JAVASCRIPT_ENABLED está sendo definida como true e a codificação de conteúdo está presente ao mesmo tempo. Este cenário ainda não tem suporte. A solução alternativa é remover APPINSIGHTS_JAVASCRIPT_ENABLED das configurações do aplicativo. Infelizmente, isso significa que se a instrumentação de JavaScript do lado do cliente/navegador ainda for necessária, as referências manuais do SDK serão necessárias para suas páginas da Web. Siga as [instruções](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) para instrumentação manual com o SDK do JavaScript.

Para obter as informações mais recentes sobre a extensão/agente de Application Insights, Confira as [notas de versão](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>O site padrão implantado com aplicativos Web não dá suporte ao monitoramento automático do lado do cliente

Quando você cria um aplicativo Web com os `ASP.NET` tempos de execução do ou `ASP.NET Core` no Azure app Services, ele implanta uma única página HTML estática como um site inicial. A página da Web estática também carrega um site ASP.NET gerenciado no IIS. Isso permite o teste do monitoramento do lado do servidor sem código, mas não dá suporte ao monitoramento automático do lado do cliente.

Se você quiser testar o monitoramento do lado do cliente e do servidor sem código para ASP.NET ou ASP.NET Core em um aplicativo Web do Azure App Services, recomendamos seguir os guias oficiais para [criar um aplicativo Web do ASP.NET Core](../../app-service/quickstart-dotnetcore.md) e [criar um aplicativo Web do ASP.NET Framework](../../app-service/quickstart-dotnet-framework.md) e, em seguida, usar as instruções no artigo atual para habilitar o monitoramento.

### <a name="connection-string-and-instrumentation-key"></a>Cadeia de conexão e chave de instrumentação

Quando o monitoramento sem código está sendo usado, somente a cadeia de conexão é necessária. No entanto, ainda recomendamos definir a chave de instrumentação para preservar a compatibilidade com versões anteriores do SDK quando a instrumentação manual está sendo executada.

### <a name="difference-between-standard-metrics-from-application-insights-vs-azure-app-service-metrics"></a>Diferença entre as métricas padrão das métricas do serviço Application Insights vs Azure App?

Application Insights coleta a telemetria para as solicitações que a fizeram para o aplicativo. Se a falha ocorreu no webapps/IIS e a solicitação não alcançou o aplicativo de usuário, Application Insights não terá nenhuma telemetria sobre ele.

A duração da `serverresponsetime` calculada pelo Application insights não corresponde necessariamente ao tempo de resposta do servidor observado pelos aplicativos Web. Isso ocorre porque Application Insights só conta a duração quando a solicitação real atinge o aplicativo do usuário. Se a solicitação estiver presa/enfileirada no IIS, esse tempo de espera será incluído nas métricas do aplicativo Web, mas não no Application Insights métricas.

## <a name="release-notes"></a>Notas de versão

Para obter as atualizações mais recentes e as correções de bugs, [consulte as notas de versão](./web-app-extension-release-notes.md).

## <a name="next-steps"></a>Próximas etapas
* [Executar o criador de perfil em seu aplicativo ativo](./profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorar o Azure Functions com o Application Insights
* [Permita que o diagnóstico do Azure](../agents/diagnostics-extension-to-application-insights.md) seja enviado ao Application Insights.
* [Monitore as métricas de integridade do serviço](../data-platform.md) para se certificar de que o serviço esteja disponível e responsivo.
* [Receba notificações de alerta](../alerts/alerts-overview.md) sempre que ocorrerem eventos operacionais ou métricas ultrapassarem um limite.
* Use o [Application Insights para aplicativos JavaScript e páginas Web](javascript.md) para obter a telemetria do cliente dos navegadores que visitam uma página da Web.
* [Configure testes de disponibilidade da Web](monitor-web-app-availability.md) para ser alertado se o seu site for desativado.
