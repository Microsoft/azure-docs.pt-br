---
title: Habilitar o registro em log de diagnóstico
description: Saiba como habilitar o log de diagnóstico e adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 44e18be9d66131ad5f4a3ebcc039621ec9e9dbe6
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452247"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Habilitar log de diagnósticos para aplicativos no Serviço de Aplicativo do Azure
## <a name="overview"></a>Visão geral
O Azure fornece diagnósticos internos para auxiliar na depuração de um [aplicativo de Serviço de Aplicativo](overview.md). Neste artigo, você saberá como habilitar o registro em log de diagnóstico e adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure.

Este artigo usa o [portal do Azure](https://portal.azure.com) e a CLI do Azure para trabalhar com logs de diagnóstico. Para saber mais sobre como trabalhar com logs de diagnóstico usando o Visual Studio, confira [Solucionando problemas do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Além das instruções de registro neste artigo, há uma nova funcionalidade integrada de registro em log com o monitoramento do Azure. Você encontrará mais informações sobre esse recurso na seção [enviar logs para Azure monitor (versão prévia)](#send-logs-to-azure-monitor-preview) . 
>
>

|Digite|Plataforma|Location|Descrição|
|-|-|-|-|
| Registro em log do aplicativo | Windows, Linux | Sistema de arquivos do serviço de aplicativo e/ou BLOBs de armazenamento do Azure | Registra as mensagens geradas pelo código do aplicativo. As mensagens podem ser geradas pela estrutura da Web que você escolher ou do código do aplicativo diretamente usando o padrão de log padrão do seu idioma. Cada mensagem recebe uma das seguintes categorias: **crítico**, **erro**, **aviso**, **informações**, **depuração** e **rastreamento**. Você pode selecionar o quão detalhado deseja que o log seja definindo o nível de severidade ao habilitar o log do aplicativo.|
| Log do servidor Web| Windows | Sistema de arquivos do serviço de aplicativo ou BLOBs de armazenamento do Azure| Dados de solicitação HTTP brutos no [formato de arquivo de log estendido W3C](/windows/desktop/Http/w3c-logging). Cada mensagem de log inclui dados como o método HTTP, o URI de recurso, o IP do cliente, a porta do cliente, o agente do usuário, o código de resposta e assim por diante. |
| Mensagens de erro detalhadas| Windows | Sistema de arquivos do serviço de aplicativo | Cópias das páginas de erro *. htm* que seriam enviadas ao navegador do cliente. Por motivos de segurança, as páginas de erro detalhadas não devem ser enviadas aos clientes em produção, mas o serviço de aplicativo pode salvar a página de erro cada vez que um erro de aplicativo ocorre com código HTTP 400 ou superior. A página pode conter informações que podem ajudar a determinar por que o servidor retorna o código de erro. |
| De uma solicitação de rastreio com falha | Windows | Sistema de arquivos do serviço de aplicativo | Informações detalhadas de rastreamento sobre solicitações com falha, incluindo um rastreamento dos componentes do IIS usados para processar a solicitação e o tempo gasto em cada componente. Isso é útil se você quiser melhorar o desempenho do site ou isolar um erro HTTP específico. Uma pasta é gerada para cada solicitação com falha, que contém o arquivo de log XML e a folha de estilos XSL com a qual exibir o arquivo de log. |
| Log de implantação | Windows, Linux | Sistema de arquivos do serviço de aplicativo | Registra quando você publica o conteúdo em um aplicativo. O log de implantação ocorre automaticamente e não há configurações configuráveis para o log de implantação. Ele ajuda a determinar por que uma implantação falhou. Por exemplo, se você usar um [script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script), poderá usar o log de implantação para determinar por que o script está falhando. |

> [!NOTE]
> O serviço de aplicativo fornece uma ferramenta de diagnósticos dedicada e interativa para ajudá-lo a solucionar problemas de seu aplicativo. Para obter mais informações, confira [Visão geral de diagnóstico do Serviço de Aplicativo do Azure](overview-diagnostics.md).
>
> Além disso, você pode usar outros serviços do Azure para aprimorar os recursos de log e monitoramento do seu aplicativo, como [Azure monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Habilitar o log de aplicativo (Windows)

> [!NOTE]
> O log de aplicativo para armazenamento de BLOBs só pode usar contas de armazenamento na mesma região que o serviço de aplicativo

Para habilitar o log de aplicativos para aplicativos do Windows no [portal do Azure](https://portal.azure.com), navegue até seu aplicativo e selecione **logs do serviço de aplicativo**.

Selecione **ativado** para o **registro em log do aplicativo (Filesystem)** ou **log de aplicativo (BLOB)** ou ambos. 

A opção **FileSystem** é para fins de depuração temporária e fica desativada em 12 horas. A opção **blob** é para o log de longo prazo e precisa de um contêiner de armazenamento de BLOBs no qual os logs serão gravados.  A opção **blob** também inclui informações adicionais nas mensagens de log, como a ID da instância de VM de origem da mensagem de log ( `InstanceId` ), ID de thread ( `Tid` ) e um carimbo de data/hora mais granular ( [`EventTickCount`](/dotnet/api/system.datetime.ticks) ).

> [!NOTE]
> No momento, somente logs de aplicativo do .NET podem ser gravados no Armazenamento de Blobs. Os logs de aplicativos Java, PHP, Node.js, Python só podem ser armazenados no sistema de arquivos do serviço de aplicativo (sem modificações de código para gravar logs no armazenamento externo).
>
> Além disso, se você [regenerar as chaves de acesso da sua conta de armazenamento](../storage/common/storage-account-create.md), deverá redefinir a respectiva configuração de registro em log para usar as chaves de acesso atualizadas. Para fazer isso:
>
> 1. Na guia **Configurar**, defina o respectivo recurso de log como **Desativado**. Salve sua configuração.
> 2. Habilite o registro no blob da conta de armazenamento novamente. Salve sua configuração.
>
>

Selecione o **nível** ou o nível de detalhes para o log. A tabela a seguir mostra as categorias de log incluídas em cada nível:

| Nível | Categorias incluídas |
|-|-|
|**Desabilitado** | Nenhum |
|**Erro** | Erro, Crítico |
|**Aviso** | Aviso, Erro, Crítico|
|**Informações** | Informações, Aviso, Erro, Crítico|
|**Verbose** | Rastreamento, Depuração, Informações, Aviso, Erro, Crítico (todas as categorias) |

Quando terminar, selecione **Avançar**.

## <a name="enable-application-logging-linuxcontainer"></a>Habilitar o log de aplicativo (Linux/contêiner)

Para habilitar o log de aplicativos para aplicativos Linux ou aplicativos de contêiner personalizados no [portal do Azure](https://portal.azure.com), navegue até seu aplicativo e selecione **logs do serviço de aplicativo**.

Em **log do aplicativo**, selecione **sistema de arquivos**.

Em **cota (MB)**, especifique a cota de disco para os logs do aplicativo. Em **período de retenção (dias)**, defina o número de dias que os logs devem ser retidos.

Quando terminar, selecione **Avançar**.

## <a name="enable-web-server-logging"></a>Habilitar o log de servidor web

Para habilitar o log do servidor Web para aplicativos do Windows no [portal do Azure](https://portal.azure.com), navegue até seu aplicativo e selecione **logs do serviço de aplicativo**.

Para o **log do servidor Web**, selecione **armazenamento** para armazenar logs no armazenamento de BLOBs ou **sistema de arquivos** para armazenar logs no sistema de arquivos do serviço de aplicativo. 

Em **período de retenção (dias)**, defina o número de dias que os logs devem ser retidos.

> [!NOTE]
> Se você [regenerar as chaves de acesso de sua conta de armazenamento](../storage/common/storage-account-create.md), será necessário redefinir a respectiva configuração de log para usar as chaves atualizadas. Para fazer isso:
>
> 1. Na guia **Configurar**, defina o respectivo recurso de log como **Desativado**. Salve sua configuração.
> 2. Habilite o registro no blob da conta de armazenamento novamente. Salve sua configuração.
>
>

Quando terminar, selecione **Avançar**.

## <a name="log-detailed-errors"></a>Erros detalhados de log

Para salvar a página de erro ou o rastreamento de solicitação com falha para aplicativos do Windows no [portal do Azure](https://portal.azure.com), navegue até seu aplicativo e selecione **logs do serviço de aplicativo**.

Em **log de erros detalhado** ou **rastreamento de solicitação com falha**, selecione **ativado** e, em seguida, selecione **salvar**.

Os dois tipos de logs são armazenados no sistema de arquivos do serviço de aplicativo. Até 50 erros (arquivos/pastas) são mantidos. Quando o número de arquivos HTML exceder 50, os 26 erros mais antigos são excluídos automaticamente.

## <a name="add-log-messages-in-code"></a>Adicionar mensagens de log no código

No código do aplicativo, você usa os recursos de log usuais para enviar mensagens de log aos logs do aplicativo. Por exemplo:

- Os aplicativos ASP.NET podem usar a classe [Rastreamento.de.Diagnóstico.de.Sistema](/dotnet/api/system.diagnostics.trace) para registrar informações no log de diagnóstico do aplicativo. Por exemplo:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Por padrão, ASP.NET Core usa o provedor de log [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) . Para obter mais informações, consulte [Registro do ASP.NET Core no Azure](/aspnet/core/fundamentals/logging/). Para obter informações sobre o log do SDK de trabalhos Web, consulte Introdução [ao SDK do Azure WebJobs](/azure/app-service/webjobs-sdk-get-started#enable-console-logging)

## <a name="stream-logs"></a>Transmitir logs

Antes de transmitir os logs em tempo real, habilite o tipo de log desejado. Todas as informações gravadas em arquivos que terminam em. txt,. log ou. htm que são armazenadas no diretório */LogFiles* (d:/Home/LogFiles) são transmitidas pelo serviço de aplicativo.

> [!NOTE]
> Alguns tipos de buffer de log gravam no arquivo de log, o que pode resultar em eventos com problemas na transmissão. Por exemplo, uma entrada para log de aplicativo, que ocorre quando um usuário visita uma página, pode ser exibida durante a transmissão antes da entrada de log HTTP correspondente para a solicitação da página.
>

### <a name="in-azure-portal"></a>No Portal do Azure

Para transmitir logs no [portal do Azure](https://portal.azure.com), navegue até seu aplicativo e selecione **fluxo de log**. 

### <a name="in-cloud-shell"></a>Em Cloud Shell

Para transmitir logs ao vivo em [Cloud Shell](../cloud-shell/overview.md), use o seguinte comando:

> [!IMPORTANT]
> Este comando pode não funcionar com aplicativos Web hospedados em um plano do serviço de aplicativo do Linux.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Para filtrar tipos de log específicos, como HTTP, use o parâmetro **--Provider** . Por exemplo:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --provider http
```

### <a name="in-local-terminal"></a>No terminal local

Para transmitir logs no console local, [instale CLI do Azure](/cli/azure/install-azure-cli) e [entre em sua conta](/cli/azure/authenticate-azure-cli). Depois de conectado, seguiu as [instruções para Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Acessar arquivos de log

Se você configurar a opção de blobs de armazenamento do Azure para um tipo de log, precisará de uma ferramenta de cliente que funcione com o armazenamento do Azure. Para obter mais informações, consulte [ferramentas de cliente de armazenamento do Azure](../storage/common/storage-explorers.md).

Para logs armazenados no sistema de arquivos do serviço de aplicativo, a maneira mais fácil é baixar o arquivo ZIP no navegador em:

- Aplicativos de contêiner/Linux: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Aplicativos do Windows: `https://<app-name>.scm.azurewebsites.net/api/dump`

Para aplicativos do Linux/contêiner, o arquivo ZIP contém logs de saída do console para o host do Docker e o contêiner do Docker. Para um aplicativo expandido, o arquivo ZIP contém um conjunto de logs para cada instância. No sistema de arquivos do serviço de aplicativo, esses arquivos de log são o conteúdo do diretório */Home/LogFiles* .

Para aplicativos do Windows, o arquivo ZIP contém o conteúdo do diretório *D:\Home\LogFiles* no sistema de arquivos do serviço de aplicativo. Ele contém a seguinte estrutura:

| Tipo de log | Diretório | Descrição |
|-|-|-|
| **Logs de aplicativo** |*/LogFiles/Application/* | Contém um ou mais arquivos de texto. O formato das mensagens de log depende do provedor de log que você usa. |
| **Rastreamentos de solicitação com falha** | */LogFiles/W3SVC # # # # # # # # #/* | Contém arquivos XML e um arquivo XSL. Você pode exibir os arquivos XML formatados no navegador. |
| **Logs de erros detalhados** | */LogFiles/DetailedErrors/* | Contém arquivos de erro HTM. Você pode exibir os arquivos HTM no navegador.<br/>Outra maneira de exibir os rastreamentos de solicitação com falha é navegar até a página do aplicativo no Portal. No menu à esquerda, selecione **diagnosticar e resolver problemas**, em seguida, procure **logs de rastreamento de solicitação com falha**, clique no ícone para procurar e exibir o rastreamento desejado. |
| **Logs do servidor Web** | */LogFiles/http/RawLogs/* | Contém arquivos de texto formatados usando o [formato de arquivo de log estendido do W3C](/windows/desktop/Http/w3c-logging). Essas informações podem ser lidas usando um editor de texto ou um utilitário como o [Log Parser](https://www.iis.net/downloads/community/2010/04/log-parser-22).<br/>O serviço de aplicativo não dá suporte aos `s-computername` `s-ip` campos, ou `cs-version` . |
| **Logs de implantação** | */LogFiles/git/* e */Deployments/* | Conter logs gerados pelos processos de implantação internos, bem como logs para implantações do git. |

## <a name="send-logs-to-azure-monitor-preview"></a>Enviar logs para Azure Monitor (versão prévia)

Com a nova [integração de Azure monitor](https://aka.ms/appsvcblog-azmon), você pode [criar configurações de diagnóstico (versão prévia)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) para enviar logs para contas de armazenamento, Hubs de eventos e log Analytics. 

> [!div class="mx-imgBorder"]
> ![Configurações de diagnóstico (versão prévia)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Tipos de log com suporte

A tabela a seguir mostra os tipos de log e as descrições com suporte: 

| Tipo de log | Windows | Contêiner do Windows | Linux | Contêiner do Linux | Descrição |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | Sim | Sim | Sim | Saída padrão e erro padrão |
| AppServiceHTTPLogs | Sim | Sim | Sim | Sim | Logs do Web Server |
| AppServiceEnvironmentPlatformLogs | Sim | N/D | Sim | Sim | Ambiente do Serviço de Aplicativo: dimensionamento, alterações de configuração e logs de status|
| AppServiceAuditLogs | Sim | Sim | Sim | Sim | Atividade de logon via FTP e kudu |
| AppServiceFileAuditLogs | Sim | Sim | TBA | TBA | Alterações de arquivo feitas no conteúdo do site; **disponível somente para a camada Premium e acima** |
| AppServiceAppLogs | ASP .NET | ASP .NET | Java SE & as imagens privilegiado Tomcat <sup>1</sup> | Java SE & as imagens privilegiado Tomcat <sup>1</sup> | Logs de aplicativo |
| AppServiceIPSecAuditLogs  | Sim | Sim | Sim | Sim | Solicitações de regras de IP |
| AppServicePlatformLogs  | TBA | Sim | Sim | Sim | Logs de operação do contêiner |
| AppServiceAntivirusScanAuditLogs | Sim | Sim | Sim | Sim | [Logs de varredura antivírus](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html) usando o Microsoft defender; **disponível somente para a camada Premium** | 

<sup>1</sup> para aplicativos Java se, adicione "$website _AZMON_PREVIEW_ENABLED" às configurações do aplicativo e defina-o como 1 ou como true.

## <a name="next-steps"></a><a name="nextsteps"></a> Próximas etapas
* [Consultar logs com o Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Como monitorar aplicativos no Serviço de Aplicativo do Azure](web-sites-monitor.md)
* [Solucionar problemas de Serviço de Aplicativo do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analisar logs de aplicativos no HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
