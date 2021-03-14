---
title: Solucionar problemas de análise de vídeo ao vivo no IoT Edge-Azure
description: Este artigo aborda as etapas de solução de problemas para análise de vídeo ao vivo em IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: d766843f58bc2cdd0dcdddfad337b23fefb28768
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698732"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Solucionar problemas de análise de vídeo ao vivo no IoT Edge

Este artigo aborda as etapas de solução de problemas do LVA (análise de vídeo ao vivo) no Azure IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Solucionar problemas de implantação

### <a name="diagnostics"></a>Diagnósticos

Como parte de sua implantação de análise de vídeo ao vivo, você configura recursos do Azure, como o Hub IoT e dispositivos IoT Edge. Como uma primeira etapa para diagnosticar problemas, sempre verifique se o dispositivo de borda está configurado corretamente seguindo estas instruções:

1. [Execute o `check` comando](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Verifique sua versão do IOT Edge](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Verifique o status do Gerenciador de segurança IOT Edge e seus logs](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Exiba as mensagens que estão passando pelo hub de IOT Edge](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Reinicie os contêineres](../../iot-edge/troubleshoot.md#restart-containers).
1. [Verifique as regras de configuração do firewall e da porta](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Problemas de pré-implantação

Se a infraestrutura de borda estiver correta, você poderá procurar problemas com o arquivo de manifesto de implantação. Para implantar o módulo análise de vídeo ao vivo no IoT Edge no dispositivo IoT Edge junto com qualquer outro módulo IoT, use um manifesto de implantação que contenha o Hub de IoT Edge, o agente do IoT Edge e outros módulos e suas propriedades. Você pode usar o seguinte comando para implantar o arquivo de manifesto:

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
Se o código JSON não estiver bem formado, você poderá receber o seguinte erro:   
&nbsp;&nbsp;&nbsp;**Falha ao analisar JSON do arquivo: ' <deployment manifest.json> ' para o argumento ' content ' com a exceção: "dados extras: linha 1, coluna de 101 (Char 5325)"**

Se você encontrar esse erro, recomendamos que verifique o JSON quanto a falta de colchetes ou outros problemas com a estrutura do arquivo. Para validar a estrutura do arquivo, você pode usar um cliente, como [bloco de notas + + com plug-in do visualizador JSON](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) ou uma ferramenta online, como o [formatador JSON & o validador](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Durante a implantação: diagnosticar com métodos do grafo de mídia direto 

Depois que a análise de vídeo ao vivo no módulo IoT Edge for implantada corretamente no dispositivo IoT Edge, você poderá criar e executar o grafo de mídia invocando [métodos diretos](direct-methods.md).  
>[!NOTE]
>  As chamadas de método direto devem ser feitas **`lvaEdge`** somente no módulo.

Você pode usar o portal do Azure para executar um diagnóstico do grafo de mídia usando métodos diretos:

1. No portal do Azure, vá para o Hub IoT que está conectado ao seu dispositivo de IoT Edge.

1. Procure **Gerenciamento de dispositivo automático** e, em seguida, selecione **IOT Edge**.  

1. Na lista de dispositivos de borda, selecione o dispositivo que você deseja diagnosticar.  
         
    ![Captura de tela da portal do Azure exibir uma lista de dispositivos de borda](./media/troubleshoot-how-to/lva-sample-device.png)


1. Verifique se o código de resposta é *200-OK*. Outros códigos de resposta para o [tempo de execução de IOT Edge](../../iot-edge/iot-edge-runtime.md) incluem:
    * 400 - A configuração de implantação está malformada ou inválida.
    * 417-o dispositivo não tem um conjunto de configuração de implantação.
    * 412 - A versão do esquema na configuração de implantação é inválida.
    * 406 – o dispositivo do IoT Edge está offline ou não está enviando relatórios de status.
    * 500 – ocorreu um erro no runtime do IoT Edge.

    > [!TIP]
    > Se você tiver problemas ao executar Azure IoT Edge módulos em seu ambiente, use **[Azure IOT Edge etapas de diagnóstico padrão](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** como um guia para solução de problemas e diagnósticos.
### <a name="post-deployment-direct-method-error-code"></a>Pós-implantação: código de erro do método direto
1. Se você receber um status `501 code` , verifique se o nome do método direto é preciso. Se o nome do método e a carga da solicitação forem precisos, você deverá obter resultados juntamente com o código de êxito = 200. 
1. Se a carga de solicitação for imprecisa, você obterá um status `400 code` e uma carga de resposta que indica o código e a mensagem de erro que devem ajudar no diagnóstico do problema com sua chamada de método direta.
    * A verificação nas propriedades relatadas e desejadas pode ajudá-lo a entender se as propriedades do módulo foram sincronizadas com a implantação. Se eles não tiverem sido, você poderá reiniciar o dispositivo IoT Edge. 
    * Use o guia de [métodos diretos](direct-methods.md) para chamar alguns métodos, especialmente os simples, como GraphTopologyList. O guia também especifica as cargas esperadas de solicitação e resposta e os códigos de erro. Depois que os métodos diretos simples forem bem-sucedidos, você poderá ter certeza de que o módulo de IoT Edge de análise de vídeo ao vivo está funcionalmente OK.
        
       ![Captura de tela do painel "método direto" para o módulo IoT Edge.](./media/troubleshoot-how-to/direct-method.png) 

1. Se o **especificado na implantação** e **relatado por colunas de dispositivo** indicar *Sim*, você poderá invocar métodos diretos na análise de vídeo ao vivo no módulo IOT Edge. Selecione o módulo para ir para uma página em que você pode verificar as propriedades desejadas e relatadas e invocar métodos diretos. Lembre-se do seguinte: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Pós-implantação: diagnosticar logs para problemas durante a execução 

Os logs de contêiner para seu módulo de IoT Edge devem conter informações de diagnóstico para ajudar a depurar seus problemas durante o tempo de execução do módulo. Você pode [verificar os logs de contêiner para problemas](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) e diagnosticar o problema automaticamente. 

Se você executou todas as verificações anteriores e ainda estiver encontrando problemas, reúna os logs do dispositivo IoT Edge [com o `support bundle` comando](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) para análise adicional da equipe do Azure. Você pode [entrar em contato conosco](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para obter suporte e enviar os logs coletados.

## <a name="common-error-resolutions"></a>Resoluções de erros comuns

A análise de vídeo ao vivo é implantada como um módulo IoT Edge no dispositivo IoT Edge e funciona de forma colaborativa com os módulos agente e Hub do IoT Edge. Alguns dos erros comuns que você encontrará com a implantação de análise de vídeo ao vivo são causados por problemas com a infraestrutura de IoT subjacente. Os erros incluem:

* [O agente de IOT Edge é interrompido após cerca de um minuto](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [O agente de IOT Edge não pode acessar a imagem de um módulo (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [O módulo agente de IOT Edge relata "arquivo de configuração vazio" e nenhum módulo inicia no dispositivo](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Falha ao iniciar o Hub de IOT Edge](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [O daemon de segurança IOT Edge falha com um nome de host inválido](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [A análise de vídeo ao vivo ou qualquer outro módulo de IOT Edge personalizado falha ao enviar uma mensagem para o Hub do Edge com erro 404](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [O módulo IOT Edge é implantado com êxito e desaparece do dispositivo](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

    > [!TIP]
    > Se você tiver problemas ao executar Azure IoT Edge módulos em seu ambiente, use **[Azure IOT Edge etapas de diagnóstico padrão](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** como um guia para solução de problemas e diagnósticos.

Você também pode encontrar problemas ao executar o **[script de instalação de recursos de análise de vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)**. Alguns problemas comuns incluem:

* Usando uma assinatura em que você não tem privilégios de proprietário. Isso fará com que o script falhe com um erro de **ForbiddenError** ou **AuthorizationFailed** .
    * Para passar por esse problema, verifique se você tem privilégios de **proprietário** para a assinatura que planeja usar. Se você não puder fazer isso por conta própria, entre em contato com o administrador da assinatura para conceder os privilégios corretos.
* **A implantação de modelo falhou por causa da violação de política.**
    * Para obter esse problema, trabalhe com o administrador de ti para garantir que as chamadas criem uma máquina virtual para ignorar o bloqueio da autenticação SSH. Isso não será necessário, pois estamos usando uma rede de bastiões segura que exige um nome de usuário e uma senha para se comunicar com os recursos do Azure. Essas credenciais serão armazenadas no arquivo **~/clouddrive/lva-sample/vm-edge-device-credentials.txt** no Cloud Shell, depois que a máquina virtual for criada, implantada e anexada com êxito ao Hub IOT.
* O script de instalação não pode criar uma entidade de serviço e/ou recursos do Azure.
    * Para passar por esse problema, verifique se sua assinatura e o locatário do Azure não atingiram seus limites de serviço máximos. Saiba mais sobre [restrições e limites de serviço do Azure ad](../../active-directory/enterprise-users/directory-service-limits-restrictions.md) e [assinatura e limites de serviço, cotas e restrições do Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

> [!TIP]
> Se houver problemas adicionais com os quais você pode precisar de ajuda, **[colete logs e envie um tíquete de suporte](#collect-logs-for-submitting-a-support-ticket)**. Você também pode entrar em contato conosco enviando um email para **[amshelp@microsoft.com](mailto:amshelp@microsoft.com)** .
### <a name="live-video-analytics-working-with-external-modules"></a>Análise de vídeo ao vivo trabalhando com módulos externos

A análise de vídeo ao vivo por meio dos processadores de extensão do grafo de mídia pode estender o grafo de mídia para enviar e receber dados de outros módulos IoT Edge usando protocolos HTTP ou gRPC. Como um [exemplo específico](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension), esse grafo de mídia pode enviar quadros de vídeo como imagens para um módulo de inferência externo, como o Yolo v3, e receber resultados de análise baseada em JSON usando o protocolo http. Nessa topologia, o destino para os eventos é principalmente o Hub IoT. Em situações em que você não vê os eventos de inferência no Hub, verifique o seguinte:

* Verifique se o Hub no qual o grafo de mídia está sendo publicado e o Hub que você está examinando são os mesmos. À medida que você cria várias implantações, pode acabar com vários hubs e verificar erroneamente o Hub errado em busca de eventos.
* Em portal do Azure, verifique se o módulo externo está implantado e em execução. Na imagem de exemplo aqui, rtspsim, yolov3, tinyyolov3 e logAnalyticsAgent são IoT Edge módulos em execução externa ao módulo lvaEdge.

    [![Captura de tela que exibe o status de execução dos módulos no Hub IOT do Azure. ](./media/troubleshoot-how-to/iot-hub-azure.png)](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* Verifique se você está enviando eventos para o ponto de extremidade correto da URL. O contêiner external AI expõe uma URL e uma porta por meio da qual recebe e retorna os dados de solicitações POST. Essa URL é especificada como uma `endpoint: url` propriedade para o processador de extensão http. Como visto na [URL de topologia](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json), o ponto de extremidade é definido como o parâmetro de URL inferência. Certifique-se de que o valor padrão para o parâmetro ou o valor passado seja preciso. Você pode testar para ver se ele está funcionando usando a URL do cliente (ondulação).  

    Por exemplo, aqui está um contêiner Yolo v3 que está sendo executado no computador local com um endereço IP de 172.17.0.3.  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Resultado retornado:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > Use o **[comando Docker inspecionar](https://docs.docker.com/engine/reference/commandline/inspect/)** para localizar o endereço IP do computador.
    
* Se você estiver executando uma ou mais instâncias de um grafo que usa o processador de extensão de gráfico de mídia, deverá usar o `samplingOptions` campo para gerenciar a taxa de quadros por segundo (FPS) do feed de vídeo. 

   * Em determinadas situações, em que a CPU ou a memória do computador de borda é altamente utilizada, você pode perder determinados eventos de inferência. Para resolver esse problema, defina um valor baixo para a `maximumSamplesPerSecond` propriedade no `samplingOptions` campo. Você pode defini-lo como 0,5 ("maximumSamplesPerSecond": "0,5") em cada instância do grafo e, em seguida, executar novamente a instância para verificar se há eventos de inferência no Hub.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Vários métodos diretos em paralelo – falha de tempo limite 

A análise de vídeo ao vivo no IoT Edge fornece um modelo de programação baseado em método direto que permite que você configure várias topologias e várias instâncias de grafo. Como parte da configuração de topologia e de grafo, você invoca várias chamadas de método direto no módulo IoT Edge. Se você invocar essas várias chamadas de método em paralelo, especialmente aquelas que iniciam e param os grafos, você pode enfrentar uma falha de tempo limite como a seguinte: 

O método de inicialização de assembly Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync gerou uma exceção. Microsoft. Azure. Devices. Common. Exceptions. IotHubException: Microsoft. Azure. Devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

É recomendável que você *não* Chame métodos diretos em paralelo. Chamá-los em sequência (ou seja, fazer uma chamada de método direto somente após a conclusão da anterior).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Coletar logs para enviar um tíquete de suporte

Quando as etapas de solução de problemas autoguiadas não resolvem o problema, acesse o portal do Azure e [abra um tíquete de suporte](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Os logs podem conter informações de identificação pessoal (PII), como seu endereço IP. Todas as cópias locais dos logs serão excluídas assim que terminarmos de examiná-las e fechar o tíquete de suporte.  

Para coletar os logs relevantes que devem ser adicionados ao tíquete, siga as instruções abaixo em ordem e carregue os arquivos de log no painel de **detalhes** da solicitação de suporte.  
1. [Configurar o módulo análise de vídeo ao vivo para coletar logs detalhados](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [Ativar logs de depuração](#live-video-analytics-debug-logs)
1. Reproduzir o problema
1. Conectar-se à máquina virtual na página do **Hub IOT** no portal
    1. Compacte todos os arquivos na pasta *debugLogs*

       > [!NOTE]
       > Esses arquivos de log não são destinados para diagnóstico automático. Eles destinam-se à equipe de engenharia do Azure a analisar seus problemas.

       * No comando a seguir, certifique-se de substituir **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** pelo local dos logs de depuração no dispositivo de borda que você configurou anteriormente na **etapa 2**.  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. Anexe o arquivo de *debugLogs.zip* ao tíquete de suporte.
1. Execute o [comando de pacote de suporte](#use-the-support-bundle-command), colete os logs e anexe ao tíquete de suporte.

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>Configurar o módulo análise de vídeo ao vivo para coletar logs detalhados
Configure seu módulo de análise de vídeo ao vivo para coletar logs detalhados definindo o `logLevel` e da `logCategories` seguinte maneira:
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

Isso pode ser feito em:
* Em **portal do Azure**, atualizando as propriedades de identidade do módulo de atualização de imagem do módulo módulo de análise de vídeo em tempo real de   [ ![ identidade ](media/troubleshoot-how-to/module-twin.png) .](media/troubleshoot-how-to/module-twin.png#lightbox)    
* Ou, em seu arquivo de **manifesto de implantação** , você pode adicionar essas entradas no nó Propriedades do módulo análise de vídeo ao vivo

### <a name="use-the-support-bundle-command"></a>Usar o comando support-Bundle

Quando você precisa coletar logs de um dispositivo IoT Edge, a maneira mais fácil é usar o `support-bundle` comando. Este comando coleta:

- Logs de módulo
- IoT Edge o Gerenciador de segurança e os logs do mecanismo de contêiner
- IoT Edge verificar a saída JSON
- Informações de depuração úteis

1. Execute o `support-bundle` comando com o sinalizador *--Since* para especificar quanto tempo você deseja que os seus logs cubram. Por exemplo, o segundo receberá logs das últimas duas horas. Você pode alterar o valor desse sinalizador para incluir logs para períodos diferentes.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Esse comando cria um arquivo chamado *support_bundle.zip* no diretório em que você executou o comando. 
   
1. Anexe o arquivo de *support_bundle.zip* ao tíquete de suporte.

### <a name="live-video-analytics-debug-logs"></a>Logs de depuração da análise de vídeo ao vivo

Para configurar a análise de vídeo ao vivo no módulo IoT Edge para gerar logs de depuração, faça o seguinte:

1. Entre no [portal do Azure](https://portal.azure.com)e vá para o Hub IOT.
1. No painel esquerdo, selecione **IOT Edge**.
1. Na lista de dispositivos, selecione a ID do dispositivo de destino.
1. Na parte superior do painel, selecione **definir módulos**.

   ![Captura de tela do botão "definir módulos" no portal do Azure.](media/troubleshoot-how-to/set-modules.png)

1. Na seção **módulos IOT Edge** , procure e selecione **lvaEdge**.
1. Selecione **Opções de criação de contêiner**.
1. Na seção **bindings** , adicione o seguinte comando:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Esse comando associa as pastas de logs entre o dispositivo de borda e o contêiner. Se você quiser coletar os logs em um local diferente, use o comando a seguir, substituindo **$LOG _LOCATION_ON_EDGE_DEVICE** pelo local que você deseja usar: `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Selecione **Atualizar**.
1. Selecione **Examinar + criar**. Uma mensagem de validação bem-sucedida é postada em uma faixa verde.
1. Selecione **Criar**.
1. Atualize a **identidade do módulo** "atualizar" para apontar para o parâmetro DebugLogsDirectory, que aponta para o diretório no qual os logs são coletados:

    a. Na tabela **modules** , selecione **lvaEdge**.  
    b. Na parte superior do painel, selecione **Identity do módulo** 10. Um painel editável é aberto.  
    c. Em **chave desejada**, adicione o seguinte par de chave/valor:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Esse comando associa as pastas de logs entre o dispositivo de borda e o contêiner. Se você quiser coletar os logs em um local diferente no dispositivo:
    > 1. Crie uma associação para o local do log de depuração na seção de **associações** , substituindo o **_LOG_LOCATION_ON_EDGE_DEVICE de $DEBUG** e **$debug _LOG_LOCATION** pelo local desejado: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Use o comando a seguir, substituindo **$DEBUG _LOG_LOCATION** pelo local usado na etapa anterior:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Clique em **Salvar**.


1. Você pode parar a coleta de log definindo o valor em **Identity do módulo** "/" para *NULL*. Volte para a página de **identidade do módulo** e atualize o seguinte parâmetro como:

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>Práticas recomendadas em relação ao registro em log

O [monitoramento e o registro em log](monitoring-logging.md) devem ajudar a compreender a taxonomia e a gerar logs que ajudarão na depuração de problemas com o LVA. 

À medida que a implementação do gRPC Server difere nas linguagens, não há nenhuma maneira padrão de adicionar o registro em log dentro do servidor.  

Por exemplo, se você criar um servidor gRPC usando o .NET Core, o serviço gRPC adicionará logs sob a categoria **gRPC** . Para habilitar logs detalhados do gRPC, configure os prefixos Grpc para o nível de depuração em seu appsettings.jsno arquivo adicionando os seguintes itens à subseção LogLevel no registro em log: 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

Você também pode configurar isso no arquivo Startup.cs com ConfigureLogging: 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

O [log e o diagnóstico no gRPC no .net](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) fornecem algumas diretrizes para a coleta de alguns logs de diagnóstico de um servidor gRPC. 

### <a name="a-failed-grpc-connection"></a>Uma conexão gRPC com falha 

Se um grafo estiver ativo e transmitindo de uma câmera, a conexão será mantida pela análise de vídeo ao vivo. 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>Monitorando e balanceando a carga de recursos de CPU e GPU quando esses recursos se tornam afunilamentos

A análise de vídeo ao vivo não monitora nem fornece monitoramento de recursos de hardware. Os desenvolvedores terão que usar as soluções de monitoramento de fabricantes de hardware. No entanto, se você usar contêineres kubernetes, poderá monitorar o dispositivo usando o [painel do kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

gRPC em documentos do .NET Core também compartilham algumas informações valiosas sobre [práticas recomendadas de desempenho](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1) e [balanceamento de carga](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing).  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>Solução de problemas de um servidor de inferência quando ele não recebe nenhum quadro e você está recebendo um erro de protocolo "desconhecido" 

Há várias coisas que você pode fazer para obter mais informações sobre o problema.  

* Inclua a categoria de log de **ediaPipeline** nas propriedades desejadas do módulo análise de vídeo ao vivo e verifique se o nível de log está definido como `Information` .  
* Para testar a conectividade de rede, você pode executar o comando a seguir do dispositivo de borda. 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   Se o comando gerar uma cadeia de caracteres curta de texto jumbled, o Telnet poderá abrir com êxito uma conexão com o servidor de inferência e abrir um canal gRPC binário. Se você não vir isso, o Telnet relatará um erro de rede. 
* No servidor de inferência, você pode habilitar o log adicional na biblioteca gRPC. Isso pode fornecer informações adicionais sobre o próprio canal do gRPC. Isso varia de acordo com a linguagem, aqui estão as instruções para [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1). 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>Selecionando mais imagens do buffer de gRPC sem enviar o resultado de retorno para o primeiro buffer

Como parte do contrato de transferência de dados gRPC, todas as mensagens que a análise de vídeo ao vivo envia para o servidor gRPC inferência devem ser confirmadas. Não confirmar o recebimento de um quadro de imagem quebra o contrato de dados e pode resultar em situações indesejadas.  

Para usar o servidor gRPC com a análise de vídeo ao vivo, a memória compartilhada pode ser usada para melhorar o desempenho. Isso exige que você use recursos de memória compartilhada do Linux expostos pelo ambiente/linguagem de programação. 

1. Abra o identificador de memória compartilhada do Linux.
1. Após o recebimento de um quadro, acesse o deslocamento de endereço dentro da memória compartilhada.
1. Confirme a conclusão do processamento de quadros para que sua memória possa ser recuperada pela análise de vídeo ao vivo.

   > [!NOTE]
   > Se você atrasar a confirmação do recebimento do quadro para análise de vídeo ao vivo por um longo tempo, isso poderá resultar na memória compartilhada ficando cheia e causar descartes de dados.
1. Armazene cada quadro em uma estrutura de dados de sua escolha (lista, matriz e assim por diante) no servidor inferência.
1. Em seguida, você pode executar a lógica de processamento quando tiver o número desejado de quadros de imagem.
1. Retorne o resultado do inferência para a análise de vídeo ao vivo quando estiver pronto.

## <a name="next-steps"></a>Próximas etapas

[Tutorial: Gravação de vídeo baseada em eventos para nuvem e reprodução com origem na nuvem](event-based-video-recording-tutorial.md)