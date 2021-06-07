---
title: Solucionar problemas do Aplicativo Azure insights Depurador de Instantâneos
description: Este artigo apresenta as etapas e as informações de solução de problemas para ajudar os desenvolvedores a habilitar e usar Application Insights Depurador de Instantâneos.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: bd83367ae073e03f03188cdf62cb60faaad7ac97
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026446"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> Solucionar problemas ao habilitar Application Insights Depurador de Instantâneos ou exibir instantâneos
Se você tiver habilitado Application Insights Depurador de Instantâneos para seu aplicativo, mas não estiver vendo instantâneos para exceções, poderá usar estas instruções para solucionar problemas.

Pode haver vários motivos diferentes pelos quais os instantâneos não são gerados. Você pode começar executando a verificação de integridade de instantâneo para identificar algumas das possíveis causas comuns.

## <a name="make-sure-youre-using-the-appropriate-snapshot-debugger-endpoint"></a>Verifique se você está usando o ponto de extremidade de Depurador de Instantâneos apropriado

Atualmente, as únicas regiões que exigem modificações de ponto de extremidade são o [Azure governamental](../../azure-government/compare-azure-government-global-azure.md#application-insights) e o [Azure China](/azure/china/resources-developer-guide).

Para aplicativos e serviço de aplicativo usando o SDK do Application Insights, você precisa atualizar a cadeia de conexão usando as substituições com suporte para Depurador de Instantâneos conforme definido abaixo:

|Propriedade da cadeia de conexão    | Nuvem do governo dos EUA | Nuvem da China |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Para obter mais informações sobre outras substituições de conexão, consulte [Application insights documentação](./sdk-connection-string.md?tabs=net#connection-string-with-explicit-endpoint-overrides).

Por Aplicativo de funções, você precisa atualizar o `host.json` usando as substituições com suporte abaixo:

|Propriedade    | Nuvem do governo dos EUA | Nuvem da China |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Abaixo está um exemplo da `host.json` atualização com o ponto de extremidade do agente de nuvem do governo dos EUA:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

## <a name="use-the-snapshot-health-check"></a>Use a verificação de integridade do instantâneo
Vários problemas comuns resultam na não exibição do Abrir Instantâneo de Depuração. Usando um Coletor de Instantâneo desatualizado, por exemplo; alcançando o limite de carregamento diário; ou talvez o instantâneo está simplesmente demorando muito para carregar. Use a Verificação de Integridade de Instantâneo para solucionar problemas comuns.

Há um link no painel de exceção do modo de rastreamento de ponta a ponta que leva você para a Verificação de Integridade de Instantâneo.

![Entre na verificação de integridade do instantâneo](./media/snapshot-debugger/enter-snapshot-health-check.png)

A interface interativa, parecida com um bate-papo, procura problemas comuns e orienta você na correção deles.

![Verificação de Integridade](./media/snapshot-debugger/healthcheck.png)

Se isso não resolver o problema, consulte as etapas manuais de solução de problemas a seguir.

## <a name="verify-the-instrumentation-key"></a>Verificar a chave de instrumentação

Certifique-se de que está usando a chave de instrumentação correta no aplicativo publicado. Normalmente, a chave de instrumentação é lida do arquivo ApplicationInsights.config. Verifique se o valor é o mesmo da chave de instrumentação para o recurso do Application Insights que você vê no portal.

## <a name="check-tlsssl-client-settings-aspnet"></a><a id="SSL"></a>Verificar as configurações do cliente TLS/SSL (ASP.NET)

Se você tiver um aplicativo ASP.NET que esteja hospedado no serviço Azure App ou no IIS em uma máquina virtual, seu aplicativo poderá falhar ao se conectar ao serviço de Depurador de Instantâneos devido a um protocolo de segurança SSL ausente.

[O ponto de extremidade depurador de instantâneos requer TLS versão 1,2](snapshot-debugger-upgrade.md?toc=/azure/azure-monitor/toc.json). O conjunto de protocolos de segurança SSL é uma das sutilezas habilitadas pelo valor de targetFramework de httpRuntime na seção System. Web do web.config. Se o targetFramework de httpRuntime for 4.5.2 ou inferior, o TLS 1,2 não será incluído por padrão.

> [!NOTE]
> O valor de targetFramework de httpRuntime é independente da estrutura de destino usada ao compilar seu aplicativo.

Para verificar a configuração, abra o arquivo web.config e localize a seção System. Web. Verifique se o `targetFramework` para `httpRuntime` está definido como 4,6 ou superior.

   ```xml
   <system.web>
      ...
      <httpRuntime targetFramework="4.7.2" />
      ...
   </system.web>
   ```

> [!NOTE]
> Modificar o valor de targetFramework de httpRuntime altera as peculiaridades de tempo de execução aplicadas ao seu aplicativo e pode causar outras alterações sutis de comportamento. Certifique-se de testar seu aplicativo cuidadosamente depois de fazer essa alteração. Para obter uma lista completa de alterações de compatibilidade, consulte https://docs.microsoft.com/dotnet/framework/migration-guide/application-compatibility#retargeting-changes

> [!NOTE]
> Se o targetFramework for 4,7 ou superior, o Windows determinará os protocolos disponíveis. No serviço Azure App, o TLS 1,2 está disponível. No entanto, se você estiver usando sua própria máquina virtual, talvez seja necessário habilitar o TLS 1,2 no sistema operacional.

## <a name="preview-versions-of-net-core"></a>Versões prévias do .NET Core
Se você estiver usando uma versão de visualização do .NET Core ou seu aplicativo fizer referência Application Insights SDK, direta ou indiretamente, por meio de um assembly dependente, siga as instruções para [habilitar o depurador de instantâneos para outros ambientes](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json).

## <a name="check-the-diagnostic-services-site-extension-status-page"></a>Verificar a página de status da extensão de site dos serviços de diagnóstico
Se Depurador de Instantâneos foi habilitado por meio do [painel de Application insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) no portal, ele foi habilitado pela extensão de site dos serviços de diagnóstico.

> [!NOTE]
> A instalação sem código do Application Insights Depurador de Instantâneos segue a política de suporte do .NET Core.
> Para obter mais informações sobre tempos de execução com suporte, consulte [política de suporte do .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Você pode verificar a página de status dessa extensão indo para a seguinte URL: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> O domínio do link da página de status irá variar dependendo da nuvem.
Esse domínio será o mesmo que o site de gerenciamento do kudu para o serviço de aplicativo.

Esta página de status mostra o estado de instalação do criador de perfil e dos agentes de Snapshot Collector. Se houver um erro inesperado, ele será exibido e mostrará como corrigi-lo.

Você pode usar o site de gerenciamento do kudu para o serviço de aplicativo para obter a URL base desta página de status:
1. Abra seu aplicativo de Serviço de Aplicativo no portal do Azure.
2. Selecione **ferramentas avançadas** ou procure **kudu**.
3. Selecione **Ir**.
4. Quando você estiver no site de gerenciamento do kudu, na URL, **acrescente o seguinte `/DiagnosticServices` e pressione Enter**.
 Ele terminará da seguinte maneira: `https://<kudu-url>/DiagnosticServices`

Ele exibirá uma página de status semelhante à seguinte: ![ página de status dos serviços de diagnóstico](./media/diagnostic-services-site-extension/status-page.png)

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Atualize para a versão mais recente do pacote NuGet
Com base em como o Depurador de Instantâneos foi habilitado, consulte as seguintes opções:

* Se Depurador de Instantâneos tiver sido habilitado por meio do [painel de Application insights no portal](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), seu aplicativo já deverá estar executando o pacote NuGet mais recente.

* Se Depurador de Instantâneos tiver sido habilitado incluindo o pacote NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) , use o Gerenciador de pacotes NuGet do Visual Studio para verificar se você está usando a versão mais recente de Microsoft. ApplicationInsights. SnapshotCollector.

Para obter as atualizações mais recentes e as correções de bugs, [consulte as notas de versão](./snapshot-collector-release-notes.md).

## <a name="check-the-uploader-logs"></a>Verificar os logs de carregador

Depois que um instantâneo é criado, um arquivo de minidespejo (. dmp) é criado no disco. Um processo separado do carregador cria esse arquivo de minidespejo e o carrega, com quaisquer PDBs associados, para o armazenamento do Depurador de Instantâneos do Application Insights. Após o minidespejo ser carregado com êxito, ele será excluído do disco. Os arquivos de log do processo de carregador são mantidos em disco. Em um ambiente de Serviço de Aplicativo, você pode encontrar esses logs em `D:\Home\LogFiles`. Use o site de gerenciamento do Kudu para o Serviço de Aplicativo para localizar esses arquivos de log.

1. Abra seu aplicativo de Serviço de Aplicativo no portal do Azure.
2. Selecione **ferramentas avançadas** ou procure **kudu**.
3. Selecione **Ir**.
4. Na caixa de listagem suspensa do **Console de Depuração**, selecione **CMD**.
5. Selecione arquivos de **log**.

Você deverá ver pelo menos um arquivo com um nome que começa com `Uploader_` ou `SnapshotUploader_` e uma extensão `.log`. Selecione o ícone apropriado para baixar todos os arquivos de log ou abri-los em um navegador.
O nome do arquivo inclui um sufixo exclusivo que identifica a instância de serviço de aplicativo. Se a instância do Serviço de Aplicativo é hospedada em mais de uma máquina, há arquivos de log separados para cada máquina. Quando o carregador detectar um novo arquivo de minidespejo, ele será registrado no arquivo de log. Aqui está um exemplo de um instantâneo e upload bem-sucedido:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> O exemplo acima é da versão 1.2.0 do pacote do NuGet Microsoft.ApplicationInsights.SnapshotCollector. Em versões anteriores, o processo de carregador é chamado `MinidumpUploader.exe` e o log é menos detalhado.

No exemplo anterior, a chave de instrumentação é `c12a605e73c44346a984e00000000000`. Esse valor deve corresponder à chave de instrumentação para seu aplicativo.
O minidespejo é associado a um instantâneo com a ID `139e411a23934dc0b9ea08a626db16c5`. Você pode usar essa ID posteriormente para localizar o registro de exceção associado no Application Insights Analytics.

O carregador examina novos PDBs cerca de uma vez a cada 15 minutos. Veja um exemplo:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Para aplicativos que _não_ estão hospedados no Serviço de Aplicativo, os logs de carregador estão na mesma pasta que o minidespejo: `%TEMP%\Dumps\<ikey>` (em que `<ikey>` é sua chave de instrumentação).

## <a name="troubleshooting-cloud-services"></a>Solucionando problemas dos Serviços de Nuvem
Nos serviços de nuvem, a pasta temporária padrão pode ser muito pequena para conter os arquivos de minidespejo, levando a instantâneos perdidos.

O espaço necessário depende do conjunto de trabalho total do seu aplicativo e o número de instantâneos simultâneos.

O conjunto de trabalho de uma função Web do ASP.NET de 32 bits tem normalmente entre 200 MB e 500 MB. Permita pelo menos dois instantâneos simultâneos.

Por exemplo, se seu aplicativo usa 1 GB de conjunto de trabalho total, você deve verificar se há pelo menos 2 GB de espaço em disco para armazenar instantâneos.

Siga estas etapas para configurar a função Serviço de Nuvem com um recurso local dedicado para instantâneos.

1. Adicione um novo recurso de local para seu Serviço de Nuvem ao editar o arquivo de definição (.csdef) do Serviço de Nuvem. O exemplo a seguir define um recurso chamado `SnapshotStore` com um tamanho de 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Modificar o código de inicialização da função para adicionar uma variável de ambiente que aponta para o recurso local `SnapshotStore`. Para Funções de Trabalho, o código deve ser adicionado ao método `OnStart` da sua função:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Para Funções da Web (ASP.NET), o código deve ser adicionado ao método `Application_Start` do seu aplicativo Web:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Atualizar o arquivo ApplicationInsights.config da função para substituir o local da pasta temporária usada pelo `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Substituição da pasta de cópia de sombra

Quando é iniciado, o Snapshot Collector tenta encontrar uma pasta no disco que seja adequada para executar o processo do Carregador de instantâneo. A pasta escolhida é conhecida como a pasta de cópia de sombra.

O Snapshot Collector verifica alguns locais bem conhecidos, certificando-se de ter as permissões para copiar os binários do carregador de instantâneos. As variáveis de ambiente a seguir são usadas:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Se não for possível encontrar uma pasta adequada, Snapshot Collector relatará um erro dizendo _"não foi possível encontrar uma pasta de cópia de sombra adequada"._

Se a cópia falhar, o Snapshot Collector relatará um erro `ShadowCopyFailed`.

Se o carregador não puder ser iniciado, o Snapshot Collector relatará um erro de `UploaderCannotStartFromShadowCopy`. O corpo da mensagem frequentemente contém `System.UnauthorizedAccessException`. Esse erro geralmente ocorre porque o aplicativo está sendo executado em uma conta com permissões reduzidas. A conta tem permissão para gravar na pasta de cópia de sombra, mas não tem permissão para executar o código.

Uma vez que esses erros geralmente ocorrem durante a inicialização, eles geralmente serão seguidos por um erro `ExceptionDuringConnect` afirmando que houve _"Falha ao iniciar o carregador."_

Para solucionar esses erros, você pode especificar a pasta de cópia de sombra manualmente por meio da opção de configuração `ShadowCopyFolder`. Por exemplo, usando ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Ou, se estiver usando appsettings.json com um aplicativo .NET Core:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Usar a pesquisa do Application Insights para encontrar exceções com instantâneos

Quando um instantâneo é criado, a exceção lançada é marcada com uma ID de instantâneo. Essa ID de instantâneo é incluída como uma propriedade personalizada quando a exceção é relatada para Application Insights. Usando a **pesquisa** no Application insights, você pode encontrar todos os registros com a `ai.snapshot.id` propriedade personalizada.

1. Navegue até o recurso do Application Insights no portal do Azure.
2. Selecione **Pesquisar**.
3. Digite `ai.snapshot.id` na caixa de texto de pesquisa e pressione Enter.

![Pesquisar por telemetria com uma ID de instantâneo no portal](./media/snapshot-debugger/search-snapshot-portal.png)

Se essa pesquisa não retornar nenhum resultado, nenhum instantâneo foi relatado para Application Insights no intervalo de tempo selecionado.

Para pesquisar uma ID de instantâneo específico dos logs do carregador, digite o ID na caixa de pesquisa. Se você não encontrar registros para um instantâneo que você sabe que foi carregado, siga estas etapas:

1. Verifique que você está olhando o recurso do Application Insights à direita, verificando a chave de instrumentação.

2. Usando o carimbo de data/hora do log de carregador, ajuste o filtro de intervalo de tempo da pesquisa para cobrir esse intervalo de tempo.

Se você ainda não vir uma exceção com essa ID de instantâneo, o registro de exceção não foi relatado para Application Insights. Essa situação pode acontecer se o aplicativo falhar depois de ter levado o instantâneo, mas antes de reportar o registro de exceção. Nesse caso, verifique os logs de Serviço de Aplicativo em `Diagnose and solve problems` para ver se houve reinicializações inesperadas ou exceções sem tratamento.

## <a name="edit-network-proxy-or-firewall-rules"></a>Editar regras de firewall ou proxy de rede

Se seu aplicativo se conectar à Internet por meio de um proxy ou um firewall, talvez seja necessário atualizar as regras para se comunicar com o serviço de Depurador de Instantâneos.

Os IPs usados pelo Application Insights Depurador de Instantâneos são incluídos na Azure Monitor marca de serviço. Para obter mais informações, consulte a [documentação de marcas de serviço](../../virtual-network/service-tags-overview.md).