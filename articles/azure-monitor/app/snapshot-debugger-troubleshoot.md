---
title: Solucionar problemas com o depurador de instantâneo do Azure Application Insights | Microsoft Docs
description: Este artigo apresenta etapas de solução de problemas e informações para ajudar os desenvolvedores que estão tendo problemas para habilitar ou usar o depurador de instantâneo do Application Insights.
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: mbullwin
ms.openlocfilehash: 25ccf20fc78a9ec00d4dfe23a60e824e96d12945
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444543"
---
# <a id="troubleshooting"></a> Solução de problemas habilitando o depurador de instantâneo do Application Insights ou exibir instantâneos
Se habilitado o depurador de instantâneo do Application Insights para seu aplicativo, mas não estiver vendo os instantâneos para exceções, você pode usar estas instruções para solucionar problemas. Pode haver vários motivos diferentes, por que os instantâneos não são gerados. Você pode executar a verificação de integridade de instantâneo para identificar algumas das possíveis causas comuns.

## <a name="use-the-snapshot-health-check"></a>Use a verificação de integridade do instantâneo
Vários problemas comuns resultam na não exibição do Abrir Instantâneo de Depuração. Usando um Coletor de Instantâneo desatualizado, por exemplo; alcançando o limite de carregamento diário; ou talvez o instantâneo está simplesmente demorando muito para carregar. Use a Verificação de Integridade de Instantâneo para solucionar problemas comuns.

Há um link no painel de exceção do modo de rastreamento de ponta a ponta que leva você para a Verificação de Integridade de Instantâneo.

![Entre na verificação de integridade do instantâneo](./media/snapshot-debugger/enter-snapshot-health-check.png)

A interface interativa, parecida com um bate-papo, procura problemas comuns e orienta você na correção deles.

![Verificação de Integridade](./media/snapshot-debugger/healthcheck.png)

Se isso não resolver o problema, consulte as etapas manuais de solução de problemas a seguir.

## <a name="verify-the-instrumentation-key"></a>Verificar a chave de instrumentação

Certifique-se de que está usando a chave de instrumentação correta no aplicativo publicado. Normalmente, a chave de instrumentação é lida do arquivo ApplicationInsights.config. Verifique se o valor é o mesmo da chave de instrumentação para o recurso do Application Insights que você vê no portal.

## <a name="preview-versions-of-net-core"></a>Versões de visualização do .NET Core
Se o aplicativo usa uma versão prévia do .NET Core e o depurador de instantâneos foi habilitado por meio de [painel do Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) no portal, em seguida, o depurador de instantâneos não pode começar. Siga as instruções em [habilitar o depurador de instantâneo para outros ambientes](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) primeiro para incluir o [snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pacote NuGet com o aplicativo ***Além disso*** para habilitar por meio de [painel do Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Atualize para a versão mais recente do pacote NuGet

Se o depurador de instantâneos foi habilitado por meio de [painel do Application Insights no portal do](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), em seguida, seu aplicativo já deve estar executando o pacote NuGet mais recente. Se o depurador de instantâneos foi habilitado, incluindo o [snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pacote do NuGet, Gerenciador do use o Visual Studio de pacotes NuGet para garantir que você está usando a versão mais recente do Snapshotcollector. Notas de versão podem ser encontradas em https://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Verificar os logs de carregador

Depois que um instantâneo é criado, um arquivo de minidespejo (. dmp) é criado no disco. Um processo separado do carregador cria esse arquivo de minidespejo e o carrega, com quaisquer PDBs associados, para o armazenamento do Depurador de Instantâneos do Application Insights. Após o minidespejo ser carregado com êxito, ele será excluído do disco. Os arquivos de log do processo de carregador são mantidos em disco. Em um ambiente de Serviço de Aplicativo, você pode encontrar esses logs em `D:\Home\LogFiles`. Use o site de gerenciamento do Kudu para o Serviço de Aplicativo para localizar esses arquivos de log.

1. Abra seu aplicativo de Serviço de Aplicativo no portal do Azure.
2. Clique em **Ferramentas Avançadas** ou pesquise **Kudu**.
3. Clique em **Ir**.
4. Na caixa de listagem suspensa do **Console de Depuração**, selecione **CMD**.
5. Clique em **Arquivos de Log**.

Você deverá ver pelo menos um arquivo com um nome que começa com `Uploader_` ou `SnapshotUploader_` e uma extensão `.log`. Clique no ícone apropriado para baixar os arquivos de log ou abri-los em um navegador.
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
O minidespejo é associado a um instantâneo com a ID `139e411a23934dc0b9ea08a626db16c5`. Você pode usar essa ID mais tarde para localizar a telemetria de exceção associada na análise de Application Insights.

O carregador examina novos PDBs cerca de uma vez a cada 15 minutos. Aqui está um exemplo:

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
Para funções nos Serviços de Nuvem, a pasta temporária padrão pode ser muito pequena para conter os arquivos de minidespejo, levando a instantâneos perdidos.
O espaço necessário depende do conjunto de trabalho total do seu aplicativo e o número de instantâneos simultâneos.
O conjunto de trabalho de uma função Web do ASP.NET de 32 bits tem normalmente entre 200 MB e 500 MB.
Permita pelo menos dois instantâneos simultâneos.
Por exemplo, se seu aplicativo usa 1 GB do conjunto de trabalho total, você deve garantir que há pelo menos 2 GB de espaço em disco para armazenar instantâneos.
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

Se não for possível encontrar uma pasta adequada, o Snapshot Collector relatará um erro afirmando que _"Não foi possível localizar uma pasta de cópia de sombra adequada."_

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

Quando um instantâneo é criado, a exceção lançada é marcada com uma ID de instantâneo. A ID do instantâneo é incluída como uma propriedade personalizada quando a telemetria de exceção é relatada ao Application Insights. Usando a **Pesquisa** no Application Insights, você pode localizar toda a telemetria com a propriedade personalizada `ai.snapshot.id`.

1. Navegue até o recurso do Application Insights no portal do Azure.
2. Clique em **Pesquisar**.
3. Digite `ai.snapshot.id` na caixa de texto de pesquisa e pressione Enter.

![Pesquisar por telemetria com uma ID de instantâneo no portal](./media/snapshot-debugger/search-snapshot-portal.png)

Se essa pesquisa não retornar resultados, então nenhum instantâneo foi relatado ao Application Insights para seu aplicativo no intervalo de tempo selecionado.

Para pesquisar uma ID de instantâneo específico dos logs do carregador, digite o ID na caixa de pesquisa. Se você não conseguir localizar telemetria para um instantâneo que você sabe que foi carregado, siga estas etapas:

1. Verifique que você está olhando o recurso do Application Insights à direita, verificando a chave de instrumentação.

2. Usando o carimbo de data/hora do log de carregador, ajuste o filtro de intervalo de tempo da pesquisa para cobrir esse intervalo de tempo.

Se você não vir uma exceção com essa ID de instantâneo, a telemetria de exceção não foi relatada ao Application Insights. Essa situação pode ocorrer se o aplicativo falhou após executar o instantâneo, mas antes de relatar a telemetria de exceção. Nesse caso, verifique os logs de Serviço de Aplicativo em `Diagnose and solve problems` para ver se houve reinicializações inesperadas ou exceções sem tratamento.

## <a name="edit-network-proxy-or-firewall-rules"></a>Editar regras de firewall ou proxy de rede

Se o aplicativo se conectar à Internet por meio de um proxy ou firewall, talvez será necessário editar as regras para permitir que o aplicativo se comunique com o serviço de Depurador de Instantâneos. Veja uma [lista de endereços IP e portas usadas pelo Depurador de Instantâneos](../../azure-monitor/app/ip-addresses.md#snapshot-debugger).
