---
title: Adicionar IDs de correlação a mensagens de IoT w/rastreamento distribuído (pré)
description: Saiba como usar a capacidade de rastreamento distribuído para rastrear mensagens de IoT em todos os serviços do Azure usados pela sua solução.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.custom:
- amqp
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: fca5c1a0aee6e62890d734e3233177549d932672
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643671"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Rastrear mensagens de dispositivo para a nuvem do IoT do Azure com o rastreamento distribuído (versão prévia)

Atualmente, o Hub IoT do Microsoft Azure oferece suporte ao rastreamento distribuído como uma [versão prévia do recurso](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Hub IoT é um dos primeiros serviços do Azure a dar suporte ao rastreamento distribuído. À medida que mais serviços do Azure derem suporte ao rastreamento distribuído, será possível rastrear mensagens do IoT em todos os serviços do Azure envolvidos em sua solução. Para ter um panorama do rastreamento distribuído, confira [Rastreamento distribuído](../azure-monitor/app/distributed-tracing.md).

A ativação do rastreamento distribuído para o Hub IoT oferece a capacidade de:

- Monitorar com precisão o fluxo de cada mensagem por meio do Hub IoT usando o [contexto de rastreamento](https://github.com/w3c/trace-context). Esse contexto de rastreamento inclui as IDs de correlação que permitem correlacionar eventos de um componente com eventos de outro componente. Ele pode ser aplicado a um subconjunto ou a todas as mensagens do dispositivo IoT usando o [dispositivo gêmeo](iot-hub-devguide-device-twins.md).
- Registrar automaticamente o contexto de rastreamento em [logs de Azure monitor](monitor-iot-hub.md).
- Meça e compreenda o fluxo de mensagens e a latência dos dispositivos para o Hub IoT e os pontos de extremidade de roteamento.
- Comece a considerar como você gostaria de implementar o rastreamento distribuído para os serviços que não pertencem ao Azure em sua solução de IoT.

Neste artigo, você deve usar o [SDK do dispositivo IoT do Azure para C](iot-hub-device-sdk-c-intro.md) com rastreamento distribuído. O suporte ao rastreamento distribuído ainda está em andamento para os outros SDKs.

## <a name="prerequisites"></a>Pré-requisitos

- Atualmente, a versão prévia do rastreamento distribuído só é compatível com os Hubs IoT criados nas seguintes regiões:

  - **Norte da Europa**
  - **Sudeste Asiático**
  - **Oeste dos EUA 2**

- Este artigo pressupõe que você esteja familiarizado com o envio de mensagens de telemetria ao Hub IoT. Certifique-se de ter concluído o [Início Rápido de C - Enviar telemetria](quickstart-send-telemetry-c.md).

- Registre um dispositivo com seu hub IoT (etapas disponíveis em cada Início Rápido) e anote a cadeia de conexão.

- Instale a versão mais recente do [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Configurar o Hub IoT

Nesta seção, você configurará um Hub IoT para registrar atributos de rastreamento distribuídos (IDs de correlação e registros de data e hora).

1. Navegue até o Hub IoT no [portal do Azure](https://portal.azure.com/).

1. No painel esquerdo do hub IoT, role para baixo até a seção **Monitoramento** e clique em **Configurações de diagnóstico**.

1. Clique em **Adicionar configuração de diagnóstico**.

1. No campo **Nome**, insira um nome para uma nova configuração de diagnóstico. Por exemplo, **DistributedTracingSettings**.

1. Escolha uma ou mais das seguintes opções que determinam o local para onde o registro em log será enviado:

    - **Arquivar em uma conta de armazenamento**: Configure uma conta de armazenamento para conter as informações de log.
    - **Transmitir para um hub de eventos**: Configure um hub de eventos para conter as informações de log.
    - **Enviar para log Analytics**: Configure um espaço de trabalho do log Analytics para conter as informações de log.

1. Na seção **Log**, escolha as operações para as quais você deseja registrar informações do registro em log.

    Certifique-se de incluir **DistributedTracing** e de configurar uma **Retenção** com a quantidade de dias que deseja que o registro em log seja retido. A retenção de logs afeta os custos de armazenamento.

    ![Captura de tela mostrando onde a categoria DistributedTracing está localizada para as configurações de diagnóstico de IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Clique em **Salvar** para salvar a nova configuração.

1. (Opcional) Para ver as mensagens fluírem para locais diferentes, configure [regras de roteamento para, no mínimo, dois pontos de extremidade diferentes](iot-hub-devguide-messages-d2c.md).

Depois que o registro em log for ativado, o Hub IoT registrará um log quando uma mensagem contendo propriedades de rastreamento válidas for encontrada em qualquer uma das seguintes situações:

- As mensagens chega no gateway do Hub IoT.
- A mensagem é processada pelo Hub IoT.
- A mensagem é roteada aos pontos de extremidade personalizados. O roteamento deve ser ativado.

Para saber mais sobre esses logs e seus esquemas, consulte [monitorar o Hub IOT](monitor-iot-hub.md) e o [rastreamento distribuído nos logs de recursos do Hub IOT](monitor-iot-hub-reference.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Configurar o dispositivo

Nesta seção, você preparará um ambiente de desenvolvimento para ser usado com o [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c). Em seguida, você modificará um dos exemplos para habilitar o rastreamento distribuído em mensagens de telemetria de seu dispositivo.

Essas instruções servem para compilar o exemplo no Windows. Para outros ambientes, confira [Compilar o SDK de C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) ou [SDK de C predefinido para o Desenvolvimento específico de plataforma](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>Clonar o código-fonte e inicializar

1. Instale [a carga de trabalho "desenvolvimento de desktop com C++"](/cpp/build/vscpp-step-0-installation?view=vs-2019&preserve-view=true) para o Visual Studio 2019. Também há suporte para o Visual Studio 2017 e 2015.

1. Instale o [CMake](https://cmake.org/). Para verificar se ele está no `PATH` digite `cmake -version` em um prompt de comando.

1. Abra um prompt de comando ou o shell Bash do Git. Execute os seguintes comandos para clonar a versão mais recente do repositório GitHub do [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Essa operação deve demorar alguns minutos.

1. Crie um subdiretório `cmake` no diretório raiz do repositório git e navegue até essa pasta. Execute os seguintes comandos do diretório `azure-iot-sdk-c`:

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Se `cmake` não conseguir encontrar o compilador do C++, você poderá obter erros de build ao executar o comando acima. Se isso acontecer, tente executar esse comando no [prompt de comando do Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Após o sucesso da compilação, as últimas linhas de saída serão semelhantes à seguinte saída:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Editar o exemplo de telemetria de envio para habilitar o rastreamento distribuído

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Obter o exemplo no GitHub</a>

1. Use um editor para abrir o arquivo de origem `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c`.

1. Localize a declaração da constante `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Substitua o valor da constante `connectionString` pela cadeia de conexão do dispositivo que você anotou na seção [Registrar um dispositivo](./quickstart-send-telemetry-c.md#register-a-device) do [Início Rápido de C - Enviar telemetria](./quickstart-send-telemetry-c.md).

1. Altere a definição de `MESSAGE_COUNT` para `5000`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Encontre a linha de código que chama `IoTHubDeviceClient_LL_SetConnectionStatusCallback` para registrar uma função de retorno de chamada do status da conexão antes do loop de envio de mensagens. Adicione o código sob essa linha como mostrado a seguir para chamar `IoTHubDeviceClient_LL_EnablePolicyConfiguration` habilitando o rastreamento distribuído para o dispositivo:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    A função `IoTHubDeviceClient_LL_EnablePolicyConfiguration` habilita políticas para recursos específicos do Hub IoT que são configurados por meio do [dispositivo gêmeo](./iot-hub-devguide-device-twins.md). Quando `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` estiver ativado com a linha de código acima, o comportamento de rastreamento do dispositivo refletirá as alterações do rastreamento distribuídas realizadas no dispositivo gêmeo.

1. Para manter o aplicativo de exemplo em execução sem o usar toda a sua cota, adicione um segundo de atraso no final do loop de envio de mensagens:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Compilar e executar

1. Navegue até o diretório de projeto *iothub_ll_telemetry_sample* a partir do diretório do CMake (`azure-iot-sdk-c/cmake`) criado anteriormente e compile o exemplo:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Execute o aplicativo. O dispositivo envia telemetria que dá suporte ao rastreamento distribuído.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Mantenha o aplicativo em execução. Opcionalmente, observe a mensagem enviada ao Hub IoT analisando a janela do console.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Solução alternativa para clientes de terceiros

Não é **trivial** Visualizar o recurso de rastreamento distribuído sem usar o SDK do C. Portanto, essa abordagem não é recomendada.

Primeiro, você deve implementar todos os primitivos de protocolo do Hub IoT em suas mensagens seguindo o guia de desenvolvimento [criar e ler mensagens do Hub IOT](iot-hub-devguide-messages-construct.md). Em seguida, edite as propriedades de protocolo nas mensagens MQTT/AMQP para adicionar `tracestate` como **Propriedade do sistema**. Especificamente:

* Para MQTT, adicione `%24.tracestate=timestamp%3d1539243209` ao tópico da mensagem, onde `1539243209` deve ser substituído pelo horário de criação da mensagem no formato de carimbo de data/hora do UNIX. Por exemplo, consulte a implementação [no SDK do C](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)
* Para AMQP, adicione `key("tracestate")` e `value("timestamp=1539243209")` como anotação de mensagem. Para obter uma implementação de referência, consulte [aqui](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527).

Para controlar a porcentagem de mensagens que contêm essa propriedade, implemente a lógica para ouvir eventos iniciados na nuvem, como atualizações gêmeas.

## <a name="update-sampling-options"></a>Atualizar opções de amostragem 

Para alterar a porcentagem de mensagens que serão rastreadas na nuvem, você deve atualizar o dispositivo gêmeo. Isso pode ser feito de várias maneiras, incluindo o editor JSON no portal e o SDK do serviço do Hub IoT. As subseções a seguir fornecem exemplos.

### <a name="update-using-the-portal"></a>Atualizar usando o portal

1. Navegue até seu Hub IoT no [portal do Azure](https://portal.azure.com/) e clique em **Dispositivos IoT**.

1. Clique em seu dispositivo.

1. Procure pela opção **Habilitar rastreamento distribuído (versão prévia)** e, em seguida, escolha **Habilitar**.

    ![Habilitar o rastreamento distribuído no portal do Azure](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Escolha uma **taxa de amostragem** entre 0% e 100%.

1. Clique em **Save** (Salvar).

1. Aguarde alguns segundos e pressione **Atualizar**, Em seguida, se for confirmado com sucesso pelo dispositivo, um ícone de sincronização com uma marca de seleção será exibido.

1. Volte para a janela do console do aplicativo de mensagens de telemetria. Você verá mensagens sendo enviadas com `tracestate` nas propriedades do aplicativo.

    ![Estado do rastreamento](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Opcional) Altere a taxa de amostragem para um valor diferente e observe a alteração na frequência com que as mensagens incluem `tracestate` nas propriedades do aplicativo.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Atualizar usando o Hub IoT do Azure para VS Code

1. Instale o VS Code e instale a versão mais recente do Hub IoT do Azure para VS Code [aqui](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

1. Abra o VS Code e [configure a cadeia de conexão do Hub IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Expanda o dispositivo e procure **Configuração do rastreamento distribuído (versão prévia)**. Nesse ponto, clique em **Atualizar a configuração do rastreamento distribuído (versão prévia)** do subnó.

    ![Habilitar o rastreamento distribuído na extensão do Hub IoT do Azure](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Na janela pop-up, escolha **Habilitar** e, em seguida, pressione Enter para confirmar 100 como a taxa de amostragem.

    ![Atualizar o modo de amostragem](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Atualizar a taxa de amostragem](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Atualização em massa para vários dispositivos

Para atualizar a configuração da amostragem do rastreamento distribuído para vários dispositivos, use a [configuração automática de dispositivos](./iot-hub-automatic-device-management.md). Lembre-se de seguir esse esquema gêmeo:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Nome do elemento | Obrigatório | Type | Descrição |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Sim | Integer | Atualmente, há suporte para dois valores de modo para ativar e desativar a amostragem. `1` está Habilitado e `2` está Desabilitado. |
| `sampling_rate` | Sim | Integer | Esse valor é uma porcentagem. Somente valores de `0` para `100` (inclusive) são permitidos.  |

## <a name="query-and-visualize"></a>Consultar e visualizar

Para ver todos os rastreios registrados por um Hub IoT, consulte o armazenamento de log escolhido nas configurações de diagnóstico. Esta seção explica algumas alternativas.

### <a name="query-using-log-analytics"></a>Consulta usando o Log Analytics

Se você tiver configurado [log Analytics com logs de recursos](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage), consulte procurando logs na `DistributedTracing` categoria. Por exemplo, esta consulta mostra todos os rastreamentos registrados:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Os logs de exemplo conforme mostrados pelo Log Analytics:

| TimeGenerated | OperationName | Categoria | Nível | CorrelationId | DurationMs | Propriedades |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Para entender os diferentes tipos de logs, consulte [logs de rastreamento distribuído do Hub IOT do Azure](monitor-iot-hub-reference.md#distributed-tracing-preview).

### <a name="application-map"></a>Mapa de aplicativo

Para visualizar o fluxo de mensagens de IoT, configure o aplicativo de exemplo do Mapa do aplicativo. O aplicativo de exemplo envia os logs de rastreamento distribuído ao [Mapa do aplicativo](../azure-monitor/app/app-map.md) usando uma função do Azure e um Hub de eventos.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Obter o exemplo no GitHub</a>

Essa imagem abaixo mostra o rastreamento distribuído no Mapa de aplicativo com três pontos de extremidade de roteamento:

![Rastreamento distribuído de IoT no Mapa do aplicativo](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Entenda o rastreamento distribuído do Azure IoT

### <a name="context"></a>Contexto

Muitas soluções de IoT, incluindo nossa própria [arquitetura de referência](/azure/architecture/reference-architectures/iot) (somente em inglês), geralmente seguem uma variante da [arquitetura de microsserviços](/azure/architecture/microservices/). À medida que uma solução de IoT se torna mais complexa, você acaba por usar 12 ou mais microsserviços. Esses microsserviços podem ou não ser do Azure. Identifique onde as mensagens de IoT são soltas ou a diminuição da velocidade poderá se tornar um desafio. Por exemplo, você tem uma solução de IoT que usa 5 serviços diferentes do Azure e 1500 dispositivos ativos. Cada dispositivo envia 10 mensagens de dispositivo para nuvem/segundo (para um total de 15.000 mensagens/segundo), mas você percebe que seu aplicativo Web vê apenas 10.000 mensagens/segundo. Onde está o problema? Consegue encontrar o culpado?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Padrão do rastreamento distribuído na arquitetura de microsserviços

Para reconstruir o fluxo de uma mensagens do IoT em diferentes serviços, cada serviço deverá propagar uma *ID de correlação* que identifica exclusivamente a mensagem. Depois de coletadas em um sistema centralizado, as IDs de correlação permitem ver o fluxo de mensagens. Esse método é chamado de [padrão de rastreamento distribuído](/azure/architecture/microservices/logging-monitoring#distributed-tracing).

Para prestar suporte a uma adoção mais ampla do rastreamento distribuído, a Microsoft contribuirá para a [proposta padrão do W3C para o rastreamento distribuído](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Suporte ao Hub IoT

Uma vez habilitado, o suporte ao rastreamento distribuído para o Hub IoT seguirá esse fluxo:

1. Uma mensagem é gerada no dispositivo IoT.
1. O dispositivo IoT decide (com a ajuda da nuvem) se essa mensagem deve ser atribuída com um contexto de rastreamento.
1. O SDK adiciona um `tracestate` à propriedade Message, que contém o carimbo de data/hora de criação da mensagem.
1. O dispositivo IoT envia a mensagem ao Hub IoT.
1. As mensagens chega no gateway do Hub IoT.
1. O Hub IoT procura o `tracestate` nas propriedades da mensagem e verifica se ele está no formato correto.
1. Nesse caso, o Hub IoT gera um globalmente exclusivo `trace-id` para a mensagem, um `span-id` para o "salto" e os registra nos [logs de rastreamento distribuídos do Hub IOT](monitor-iot-hub-reference.md#distributed-tracing-preview) na operação `DiagnosticIoTHubD2C` .
1. Depois que o processamento da mensagem for concluído, o Hub IoT gerará outro `span-id` e o registrará junto com o existente `trace-id` na operação `DiagnosticIoTHubIngress` .
1. Se o roteamento estiver habilitado para a mensagem, o Hub IoT o registrará no ponto de extremidade personalizado e registrará outro `span-id` com o mesmo `trace-id` na categoria `DiagnosticIoTHubEgress`.
1. As etapas acima são repetidas para cada mensagem gerada.

## <a name="public-preview-limits-and-considerations"></a>Considerações e limites sobre a visualização pública

- A proposta atual do padrão do Contexto de Rastreamento do W3C é um rascunho de trabalho.
- Atualmente, a única linguagem de desenvolvimento compatível com o SDK do cliente é C.
- O recurso gêmeo de nuvem para dispositivo não está disponível para a [camada básica do Hub IoT](iot-hub-scaling.md#basic-and-standard-tiers). No entanto, o Hub IoT ainda realizará o log no Azure Monitor se ele vir um cabeçalho de contexto de rastreamento adequadamente composto.
- Para garantir uma operação eficiente, o Hub IoT imporá uma aceleração na taxa de registro que poderá ocorrer como parte do rastreamento distribuído.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o padrão geral do rastreamento distribuído em microsserviços, confira [Padrão de arquitetura do microsserviço: rastreamento distribuído](https://microservices.io/patterns/observability/distributed-tracing.html).
- Para definir a configuração para aplicar configurações de rastreamento distribuído a um grande número de dispositivos, confira [Configurar e monitorar dispositivos IoT em escala](./iot-hub-automatic-device-management.md).
- Para saber mais sobre o Azure Monitor, confira [O que é o Azure Monitor?](../azure-monitor/overview.md).
- Para saber mais sobre como usar Azure Monitor com o HUb IoT, confira [monitorar o Hub IOT](monitor-iot-hub.md)