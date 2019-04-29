---
title: Habilitar o log de diagnósticos para aplicativos - Serviço de Aplicativo do Azure
description: Saiba como habilitar o log de diagnóstico e adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 37455c278d665d05636ec120ca91b76153e53d16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835680"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Habilitar log de diagnósticos para aplicativos no Serviço de Aplicativo do Azure
## <a name="overview"></a>Visão geral
O Azure fornece diagnósticos internos para auxiliar na depuração de um [aplicativo de Serviço de Aplicativo](https://go.microsoft.com/fwlink/?LinkId=529714). Neste artigo, você saberá como habilitar o registro em log de diagnóstico e adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure.

Este artigo usa o [portal do Azure](https://portal.azure.com) e a CLI do Azure para trabalhar com logs de diagnóstico. Para saber mais sobre como trabalhar com logs de diagnóstico usando o Visual Studio, confira [Solucionando problemas do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md).

## <a name="whatisdiag"></a>Diagnóstico de servidor Web e diagnóstico de aplicativos
O Serviço de Aplicativo oferece funcionalidade de diagnóstico para informações de log do servidor Web e do aplicativo Web. Estes estão logicamente separados em **diagnóstico de servidor Web** e **diagnóstico de aplicativos**.

### <a name="web-server-diagnostics"></a>Diagnóstico de servidor Web
Você pode habilitar ou desabilitar os seguintes tipos de logs:

* **Detalhadas no log de erros** -informações detalhadas para qualquer solicitação que resulte em código de status HTTP 400 ou superior. Pode conter informações que podem ajudar a determinar por que o servidor retornou o código de erro. Um arquivo HTML é gerado para cada erro no sistema de arquivos do aplicativo e até 50 erros (arquivos) são mantidos. Quando o número de arquivos HTML exceder 50, os arquivos de 26 mais antigos são excluídos automaticamente.
* **Falha no Rastreamento de Solicitação** - informações detalhadas sobre solicitações com falha, incluindo um rastreamento dos componentes IIS usados para processar a solicitação e o tempo levado em cada componente. Isso é útil se você quiser melhorar o desempenho do site ou isolar um erro HTTP específico. Uma pasta é gerada para cada erro no sistema de arquivos do aplicativo. Políticas de retenção de arquivo são o mesmo que o erro detalhado log acima.
* **Registro em Log de Servidor Web** - informações sobre transações HTTP usando o [formato de arquivo de log estendido W3C](/windows/desktop/Http/w3c-logging). É útil para determinar as métricas gerais do site, como o número de solicitações manipuladas e quantas solicitações existem vindas de um endereço IP específico.

### <a name="application-diagnostics"></a>Diagnóstico de aplicativo
O diagnóstico de aplicativo permite que você capture informações produzidas por um aplicativo da Web. Os aplicativos ASP.NET podem usar a classe [Rastreamento.de.Diagnóstico.de.Sistema](/dotnet/api/system.diagnostics.trace) para registrar informações no log de diagnóstico do aplicativo. Por exemplo: 

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Em tempo de execução, você pode recuperar esses logs para ajudar na solução de problemas. Para obter mais informações, consulte [Solucionar problemas de Serviço de Aplicativo do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md).

O Serviço de Aplicativo também registra informações de implantação ao publicar conteúdo em um aplicativo. Acontece automaticamente e não há definições de configuração para log de implantação. O log de implantação permite que você determine por que uma implantação falhou. Por exemplo, se você usar um script de implantação personalizado, use o log de implantação para determinar por que o script está falhando.

## <a name="enablediag"></a>Como habilitar o diagnóstico
Para habilitar o diagnóstico no [portal do Azure](https://portal.azure.com), vá até a página do aplicativo Web e clique em **Configurações > Logs de diagnóstico**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Parte de logs](./media/web-sites-enable-diagnostic-log/logspart.png)

Ao habilitar o **diagnóstico de aplicativos**, você também escolhe o **Nível**. A tabela a seguir mostra as categorias de logs que cada nível inclui:

| Nível| Categorias de log incluídas |
|-|-|
|**Desabilitado** | Nenhum |
|**Erro** | Erro, Crítico |
|**Aviso** | Aviso, Erro, Crítico|
|**Informações** | Informações, Aviso, Erro, Crítico|
|**Detalhado** | Rastreamento, Depuração, Informações, Aviso, Erro, Crítico (todas as categorias) |
|-|-|

Para **Log do aplicativo**, você pode ativar a opção do sistema de arquivos temporariamente para fins de depuração. Esta opção é desativada automaticamente em 12 horas. Você também pode ativar a opção de armazenamento de blob para selecionar um contêiner de blob para gravar logs.

> [!NOTE]
> No momento, somente logs de aplicativo do .NET podem ser gravados no Armazenamento de Blobs. Os logs de aplicativo Java, PHP, Node.js, Python só podem ser armazenados no sistema de arquivos (sem modificações no código para gravar logs no armazenamento externo).
>
>

Para **Log do servidor Web**, você pode selecionar **armazenamento** ou **sistema de arquivos**. Selecionar **armazenamento** permite que você selecione uma conta de armazenamento e, em seguida, um contêiner de blob onde os logs estejam gravados. 

Se você armazenar logs no sistema de arquivos, os arquivos poderão ser acessados por FTP ou baixados como um arquivo Zip usando a CLI do Azure.

Por padrão, os logs não são excluídos automaticamente (com exceção do **Log do aplicativo (Filesystem)**). Para excluir automaticamente os logs, defina o campo **Período de retenção (dias)**.

> [!NOTE]
> Se você [regenerar as chaves de acesso de sua conta de armazenamento](../storage/common/storage-create-storage-account.md), será necessário redefinir a respectiva configuração de log para usar as chaves atualizadas. Para fazer isso:
>
> 1. Na guia **Configurar**, defina o respectivo recurso de log como **Desativado**. Salve sua configuração.
> 2. Habilite o registro no blob da conta de armazenamento novamente. Salve sua configuração.
>
>

Qualquer combinação de sistema de arquivos ou armazenamento de blobs pode ser habilitada ao mesmo tempo e ter configurações de nível de log individuais. Por exemplo, você pode desejar gravar erros e avisos no armazenamento de blob como uma solução de log a longo prazo, habilitando o log de sistema de arquivos em um nível detalhado.

Enquanto ambos os locais de armazenamento fornecem as mesmas informações básicas para eventos registrados, o **armazenamento de blobs** registra informações adicionais como a ID da instância, ID do thread e um carimbo de data/hora mais granular (formato de tique) do que o registro no **sistema de arquivos**.

> [!NOTE]
> As informações armazenadas no **armazenamento de blobs** só podem ser acessadas usando um cliente de armazenamento ou um aplicativo que possa trabalhar diretamente com esses sistemas de armazenamento. Por exemplo, o Visual Studio 2013 contém um Gerenciador de Armazenamento que pode ser usado para explorar o armazenamento de blobs, e o HDInsight pode acessar os dados armazenados no armazenamento de blobs. Você também pode gravar um aplicativo que acesse o Armazenamento do Azure usando um dos [SDKs do Azure](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a> Como baixar logs
Informações de diagnóstico armazenadas no sistema de arquivos do aplicativo podem ser diretamente acessadas usando FTP. Além disso, pode ser baixado como um arquivo Zip usando a CLI do Azure.

A estrutura de diretórios onde os logs estão armazenados é a seguinte:

* **Logs do aplicativo** - /LogFiles/Application/. Essa pasta contém um ou mais arquivos de texto que contêm informações produzidas pelo log do aplicativo.
* **Rastreamento de Solicitação Falha** - /LogFiles/W3SVC#########/. Esta pasta contém um arquivo XSL e um ou mais arquivos XML. Baixe o arquivo XSL no mesmo diretório que o(s) arquivo(s) XML, pois o arquivo XSL fornece funcionalidade para formatar e filtrar o conteúdo do(s) arquivo(s) XML quando visualizado(s) no Internet Explorer.
* **Logs de erro do aplicativo** - /LogFiles/DetailedErrors/. Esta pasta contém um ou mais arquivos .htm que fornecem informações exaustivas para quaisquer erros de HTTP.
* **Logs do Web Server** - /LogFiles/http/RawLogs. Esta pasta contém um ou mais arquivos de texto que foram formatados usando o [formato W3C estendido de arquivo de log](/windows/desktop/Http/w3c-logging).
* **Logs de implantação** - /LogFiles/Git. Essa pasta contém os logs gerados pelos processos de implantação internos usados pelo Serviço de Aplicativo do Azure, bem como os logs das implantações do Git. Você também pode encontrar os logs de implantação em D:\home\site\deployments.

### <a name="ftp"></a>FTP

Para abrir uma conexão FTP ao servidor FTP do seu aplicativo, consulte [Implantar aplicativo no Serviço de Aplicativo do Azure usando FTP/S](deploy-ftp.md).

Uma vez conectado ao servidor FTP/S do aplicativo, abra a pasta **LogFiles**, em que os arquivos de log são armazenados.

### <a name="download-with-azure-cli"></a>Baixar com a CLI do Azure
Para baixar os arquivos de log usando a Interface da Linha de Comando do Azure, abra uma nova sessão de prompt de comando, PowerShell, Bash ou Terminal e insira o seguinte comando:

    az webapp log download --resource-group resourcegroupname --name appname

Este comando salva os logs para o aplicativo chamado 'appname' em um arquivo chamado **webapp_logs.zip** no diretório atual.

> [!NOTE]
> Se você não instalou a CLI do Azure ou não a configurou para usar sua Assinatura do Azure, consulte [Como usar a CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Como: Exibir logs no Application Insights
O Application Insights do Visual Studio fornece ferramentas para filtrar e pesquisar logs e para correlacioná-los com solicitações e outros eventos.

1. Adicionar o SDK do Application Insights ao projeto no Visual Studio.
   * Em Gerenciador de Soluções, clique com o botão direito no seu projeto e escolha Adicionar Application Insights. A interface guia você pelas etapas que incluem a criação de um recurso do Application Insights. [Saiba mais](../azure-monitor/app/asp-net.md)
2. Adicionar o pacote do Ouvinte de Rastreamento ao seu projeto.
   * Clique com o botão direito do mouse em seu projeto e escolha Gerenciar Pacotes NuGet. Selecione `Microsoft.ApplicationInsights.TraceListener` [Saiba mais](../azure-monitor/app/asp-net-trace-logs.md)
3. Carregue seu projeto e execute-o para gerar dados de log.
4. No [Portal do Azure](https://portal.azure.com/), navegue até o novo recurso do Application Insights e abra **Pesquisa**. Você verá os dados de log, a solicitação, o uso e outras telemetrias. Algumas telemetrias podem levar alguns minutos para aparecer: clique em Atualizar. [Saiba mais](../azure-monitor/app/diagnostic-search.md)

[Saiba mais sobre desempenho de rastreamento com o Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a> Como Transmitir logs
Ao desenvolver um aplicativo, é sempre útil visualizar informações de registro em log realizado em tempo quase real. É possível transmitir informações de registro para o ambiente de desenvolvimento usando a CLI do Azure.

> [!NOTE]
> Alguns tipos de buffer de log gravam no arquivo de log, o que pode resultar em eventos com problemas na transmissão. Por exemplo, uma entrada para log de aplicativo, que ocorre quando um usuário visita uma página, pode ser exibida durante a transmissão antes da entrada de log HTTP correspondente para a solicitação da página.
>
> [!NOTE]
> O streaming de log também transmitirá informações gravadas em qualquer arquivo de texto armazenado na pasta **D:\\home\\LogFiles\\**.
>
>

### <a name="streaming-with-azure-cli"></a>Streaming com CLI do Azure
Para transmitir informações de log, abra uma nova sessão de prompt de comando, PowerShell, Bash ou Terminal e insira o seguinte comando:

    az webapp log tail --name appname --resource-group myResourceGroup

Esse comando conecta o aplicativo nomeado 'appname' e começa a transmitir informações para a janela à medida que os eventos de log ocorrem no aplicativo. Qualquer informação escrita para os arquivos com terminação .txt ou .htm que sejam armazenadas no diretório /LogFiles (d:/home/LogFiles) será transmitida à console local.

Para filtrar eventos específicos como erros, use o parâmetro **-Filtro** . Por exemplo: 

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Para filtrar tipos específicos de log como HTTP, use o parâmetro **-Caminho** . Por exemplo: 

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Se você não instalou a CLI do Azure ou não a configurou para usar sua Assinatura do Azure, consulte [Como usar a CLI do Azure](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> Como: compreender os logs de diagnóstico
### <a name="application-diagnostics-logs"></a>Logs de diagnóstico de aplicativo
O diagnóstico de aplicativo armazena informações em um formato específico para aplicativos .NET, dependendo se você armazena logs no sistema de arquivos ou no armazenamento de blobs. 

O conjunto de dados armazenados de base é o mesmo em ambos os tipos de armazenamento - a data e hora em que o evento ocorreu, a ID do processo que produziu o evento, o tipo de evento (informação, aviso ou erro) e a mensagem do evento. Usar o sistema de arquivos para armazenamento de log será útil quando você precisar de acesso imediato para solucionar um problema porque os arquivos de log são atualizados quase instantaneamente. O armazenamento de blobs é usado para fins de arquivamento porque os arquivos são armazenados em cache e, em seguida, liberados para o contêiner de armazenamento em um agendamento.

**Sistema de Arquivos**

Cada linha registrada no sistema de arquivos ou recebida via streaming estará no seguinte formato:

    {Date}  PID[{process ID}] {event type/level} {message}

Por exemplo, um evento de erro deve aparecer semelhante ao seguinte exemplo:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

O registro em sistema de arquivos fornece a informação mais básica dos três métodos disponíveis, fornecendo apenas a hora, o ID do processo, o nível de evento e a mensagem.

**Armazenamento de Blobs**

Ao registrar o log no armazenamento de blob, os dados serão armazenados em um formato de valores separados por vírgulas (CSV). Campos adicionais são registrados para fornecer informações mais detalhadas sobre o evento. As seguintes propriedades são usadas para cada linha no CSV:

| Nome da propriedade | Valor/formato |
| --- | --- |
| Data |A data e hora em que o evento ocorreu |
| Nível |Nível de evento (por exemplo, erro, aviso, informação) |
| ApplicationName |O nome do aplicativo |
| InstanceId |Instância do aplicativo em que o evento ocorreu |
| EventTickCount |A data e hora em que o evento ocorreu, em formato de escala (maior precisão) |
| EventId |A ID deste evento<p><p>terá como padrão 0 caso nenhuma seja especificada |
| Pid |ID do Processo |
| Tid |A ID do thread que produziu o evento |
| Mensagem |A mensagem com detalhes do evento |

Os dados armazenados em um blob deverão ser semelhantes ao seguinte exemplo:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Para o ASP.NET Core, o registro é realizado usando o provedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) e esse provedor deposita arquivos de log adicionais no contêiner de blob. Para obter mais informações, consulte [Registro do ASP.NET Core no Azure](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#logging-in-azure).
>
>

### <a name="failed-request-traces"></a>Rastreamento de Solicitação Falha
Os rastreamentos de solicitações com falha são armazenados em arquivos XML chamados **fr######.xml**. Para facilitar a exibição das informações registradas, uma folha de estilos XSL chamada **freb.xsl** é fornecida no mesmo diretório dos arquivos XML. Se você abrir um dos arquivos XML no Internet Explorer, ele usará a folha de estilos XSL para fornecer uma exibição formatada da informação rastreada, similar ao seguinte exemplo:

![solicitação falha visualizada no navegador](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> É uma maneira fácil de exibir os rastreamentos de solicitação com falha formatada navegar até a página do seu aplicativo no portal. No menu à esquerda, selecione **diagnosticar e solucionar problemas**, em seguida, procure **falha os Logs de rastreamento de solicitação**, em seguida, clique no ícone para procurar e exibir o rastreamento que você deseja.
>

### <a name="detailed-error-logs"></a>Logs de erro do aplicativo
Logs detalhados de erro são documentos HTML que fornecem informações mais detalhadas sobre erros HTTP que tenham ocorrido. Como são simplesmente documentos HTML, eles podem ser visualizados usando um navegador da Web.

### <a name="web-server-logs"></a>Logs do Web Server
Os logs do servidor da Web são formatados usando o [formato W3C estendido de arquivo de log](/windows/desktop/Http/w3c-logging). Esta informação pode ser lida usando um editor de texto ou analisada usando ferramentas como o [Analisador de Log](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Os logs produzidos pelo Serviço de Aplicativo do Azure não dão suporte para os campos **s-computername**, **s-ip** ou **cs-version**.
>
>

## <a name="nextsteps"></a> Próximas etapas
* [Como monitorar aplicativos no Serviço de Aplicativo do Azure](web-sites-monitor.md)
* [Solucionar problemas de Serviço de Aplicativo do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analisar logs de aplicativos no HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
