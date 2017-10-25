---
title: O SDK do dispositivo IoT do Azure para C | Microsoft Docs
description: Como usar o SDK do dispositivo IoT do Azure para C e saiba como criar aplicativos de dispositivos que se comunicam com um Hub IoT.
services: iot-hub
documentationcenter: 
author: olivierbloch
manager: timlt
editor: 
ms.assetid: e448b061-6bdd-470a-a527-15ec03cca7b9
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: obloch
ms.openlocfilehash: 9f0ea741355c6403cde6f02d8263dd8bff7e553b
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-device-sdk-for-c"></a>SDK do dispositivo IoT do Azure para C

O **SDK do dispositivo IoT do Azure** é um conjunto de bibliotecas projetadas para simplificar o processo de envio e o recebimento de mensagens do serviço **Hub IoT do Azure**. Existem diferentes variações do SDK, cada uma visando uma plataforma específica, mas este artigo descreve o **SDK do dispositivo IoT do Azure para C**.

O SDK do dispositivo IoT do Azure para C foi escrito em ANSI (C99) para maximizar a portabilidade. Esse recurso torna as bibliotecas bastante adequado para a operação em vários dispositivos e plataformas, especialmente quando a minimização do volume de disco e de memória for uma prioridade.

Há uma ampla variedade de plataformas nas quais o SDK foi testado (confira o [Catálogo de dispositivos certificados pelo Azure para IoT](https://catalog.azureiotsuite.com/) para obter detalhes). Embora este artigo inclua explicações de exemplo de código em execução na plataforma Windows, o código descrito neste artigo é idêntico entre as várias plataformas com suporte.

Este artigo apresenta a arquitetura do SDK do dispositivo IoT do Azure para C. Ele demonstra como inicializar a biblioteca de dispositivo, enviar dados para o Hub IoT e receber mensagens dele. As informações neste artigo devem ser suficientes para começar a usar o SDK, mas também há indicações para se saber mais sobre as bibliotecas.

## <a name="sdk-architecture"></a>Arquitetura do SDK

Você pode encontrar o [**SDK do dispositivo IoT do Azure para C**](https://github.com/Azure/azure-iot-sdk-c) no repositório GitHub e exibir os detalhes da API [na referência da API do C](https://azure.github.io/azure-iot-sdk-c/index.html).

A versão mais recente das bibliotecas pode ser encontrada na ramificação **mestre** deste repositório:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

* A implementação principal do SDK fica na pasta **iothub\_client**, que contém a implementação da camada de API mais baixa no SDK: a biblioteca **IoTHubClient**. A biblioteca **IoTHubClient** contém APIs que implementam mensagens brutas para enviar mensagens para o Hub IoT, além de receber mensagens dele. Ao usar essa biblioteca, você será o responsável por implementar a serialização de mensagens, mas outros detalhes de comunicação com o Hub IoT serão tratados para você.
* A pasta **serializador** contém funções auxiliares e exemplos que mostram como serializar os dados antes de enviar para o Hub IoT do Azure usando a biblioteca de cliente. O uso do serializador não é obrigatório e só é fornecido como uma conveniência. Para usar a biblioteca **serializer**, defina um modelo que especifica os dados para envio ao Hub IoT e as mensagens que você espera receber dele. Depois que o modelo é definido, o SDK fornece uma superfície de API que permite a você trabalhar facilmente com mensagens de dispositivo para nuvem e de nuvem para dispositivo sem se preocupar com os detalhes de serialização. A biblioteca depende de outras bibliotecas de software livre que implementam o transporte usando protocolos como MQTT e AMQP.
* A biblioteca **IoTHubClient** depende de outras bibliotecas de software livre:
  * A biblioteca de [utilitários compartilhados do Azure C](https://github.com/Azure/azure-c-shared-utility), que fornece funcionalidades comuns para tarefas básicas (como cadeia de caracteres, manipulação de listas e E/S) necessárias entre vários SDKs para C relacionados ao Azure.
  * A biblioteca [uAMQP do Azure](https://github.com/Azure/azure-uamqp-c) é uma implementação do lado do cliente do AMQP otimizada para dispositivos com restrição de recursos.
  * A biblioteca [uMQTT do Azure](https://github.com/Azure/azure-umqtt-c) , que é uma biblioteca de finalidade geral, com o protocolo MQTT implementado e otimizada para dispositivos com restrição de recursos.

O uso dessas bibliotecas fica mais fácil de entender examinando exemplos de código. As seções a seguir explicam alguns exemplos de aplicativo incluídos no SDK. O passo a passo deve dar uma boa ideia dos vários recursos das camadas de arquitetura do SDK, bem como uma introdução ao funcionamento da API.

## <a name="before-you-run-the-samples"></a>Antes de executar os exemplos

Antes de executar os exemplos no SDK do dispositivo IoT do Azure para C, você deverá [criar uma instância do serviço Hub IoT](iot-hub-create-through-portal.md) em sua assinatura do Azure. Em seguida, conclua as seguintes tarefas:

* Preparar seu ambiente de desenvolvimento
* Obtenha as credenciais do dispositivo.

### <a name="prepare-your-development-environment"></a>Preparar seu ambiente de desenvolvimento

Os pacotes são fornecidos para plataformas comuns (como o NuGet para Windows ou apt_get para Debian e Ubuntu) e os exemplos usam esses pacotes, quando disponíveis. Em alguns casos, você precisa compilar o SDK para ou em seu dispositivo. Se você precisa compilar o SDK, consulte [Preparar o ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) no repositório do GitHub.

Para obter o código do aplicativo de exemplo, baixe uma cópia do SDK do GitHub. Obtenha uma cópia da origem na ramificação **master** do [repositório GitHub](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Obter credenciais do dispositivo

Agora que você tem o código-fonte, a próxima coisa a fazer é obter um conjunto de credenciais do dispositivo. Para um dispositivo poder acessar um Hub IoT, primeiro você deverá adicionar o dispositivo ao registro de identidade do Hub IoT. Quando você adiciona seu dispositivo, obtém um conjunto de credenciais de dispositivo necessário para que ele seja capaz de se conectar ao Hub IoT. Os aplicativos de exemplo abordados na próxima seção esperam essas credenciais na forma de uma **cadeia de conexão de dispositivo**.

Há várias ferramentas de código-fonte aberto para ajudá-lo a gerenciar seu Hub IoT.

* Um aplicativo do Windows chamado [Gerenciador de Dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
* Uma ferramenta CLI de plataforma cruzada Node.js chamada [iothub-explorer](https://github.com/azure/iothub-explorer).

Este tutorial usa a ferramenta gráfica *Gerenciador de Dispositivos*. Você também pode usar a ferramenta *iothub-explorer* se preferir usar uma ferramenta CLI.

A ferramenta Gerenciador de Dispositivos usa as bibliotecas de serviço IoT do Azure para executar várias funções no Hub IoT, incluindo a adição de dispositivos. Se você usar a ferramenta Gerenciador de Dispositivos para adicionar um dispositivo, você obterá uma cadeia de conexão para seu dispositivo. Você precisa dessa cadeia de conexão para executar os aplicativos de exemplo.

Caso você não esteja familiarizado com a ferramenta Gerenciador de Dispositivos, o procedimento a seguir descreve como usá-la para adicionar um dispositivo e obter uma cadeia de conexão de dispositivo.

Para instalar a ferramenta Gerenciador de Dispositivos, consulte [Como usar o Gerenciador de Dispositivos para dispositivos Hub IoT](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

Ao executar o programa, você verá esta interface:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Insira a **Cadeia de Conexão do Hub IoT** no primeiro campo e clique em **Atualizar**. Essa etapa configura a ferramenta para que ela possa se comunicar com o Hub IoT.

Depois que a cadeia de conexão do Hub IoT for configurada, clique na guia **Gerenciamento**:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Essa guia é o local onde você gerencia os dispositivos registrados no seu Hub IoT.

Crie um dispositivo clicando no botão **Criar** . Um diálogo é exibido com um conjunto de chaves pré-populadas (primárias e secundárias). Insira uma **ID de dispositivo** e clique em **Criar**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Depois que o dispositivo for criado, a lista Dispositivos será atualizada com todos os dispositivos registrados, incluindo o que você acabou de criar. Se você clicar com o botão direito do mouse no novo dispositivo, este menu será exibido:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Se você escolher **Copiar cadeia de conexão para o dispositivo selecionado**, a cadeia de conexão do dispositivo será copiada para a área de transferência. Manter uma cópia da cadeia de conexão do dispositivo. Você precisará dela ao executar os aplicativos de exemplo descritos nas próximas seções.

Após concluir as etapas acima, você estará pronto para começar a executar códigos. Ambos os exemplos têm uma constante na parte superior do arquivo de origem principal que permite inserir uma cadeia de conexão. Por exemplo, a linha correspondente do **iothub\_client\_sample\_mqtt** aparece da forma a seguir.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Usar a biblioteca IoTHubClient

Na pasta **iothub\_client** no repositório [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c), há uma pasta **samples** que contém um aplicativo chamado **iothub\_client\_sample\_mqtt**.

A versão do Windows do aplicativo **iothub\_client\_sample\_mqtt** inclui a seguinte solução do Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-mqtt.PNG)

> [!NOTE]
> Se você abrir esse projeto no Visual Studio 2017, aceite os prompts a fim de redirecionar o projeto para a versão mais recente.

Essa solução contém um único projeto. Há quatro pacotes NuGet instalados na solução:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Você sempre precisará do pacote **Microsoft.Azure.C.SharedUtility** quando estiver trabalhando com o SDK. Este exemplo usa o protocolo MQTT e, portanto, você deve incluir os pacotes **Microsoft.Azure.umqtt** e **Microsoft.Azure.IoTHub.MqttTransport** (há pacotes equivalentes para AMQP e HTTPS). Como o exemplo usa a biblioteca **IoTHubClient**, você também deverá incluir o pacote **Microsoft.Azure.IoTHub.IoTHubClient** em sua solução.

Você pode encontrar a implementação do aplicativo de exemplo no arquivo de origem **iothub\_client\_sample\_mqtt.c**.

As etapas a seguir usam esse exemplo de aplicativo para explicar o que é necessário para usar a biblioteca **IoTHubClient** .

### <a name="initialize-the-library"></a>Inicializar a biblioteca

> [!NOTE]
> Antes de começar a trabalhar com as bibliotecas, talvez seja necessário executar alguma inicialização específica de plataforma. Por exemplo, se você planeja usar o AMQP no Linux, você deverá inicializar a biblioteca OpenSSL. Os exemplos de [repositório GitHub](https://github.com/Azure/azure-iot-sdk-c) chamam a função de utilitário **platform\_init** quando o cliente inicia, e chamam a função **platform\_deinit** antes de encerrar. Essas funções são declaradas no arquivo de cabeçalho platform.h. Examine as definições dessas funções para sua plataforma de destino no [repositório](https://github.com/Azure/azure-iot-sdk-c) para determinar se é necessário incluir algum código de inicialização específico de plataforma no seu cliente.

Para começar a trabalhar com as bibliotecas, primeiro aloque um identificador de cliente do Hub IoT:

```c
if ((iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Passe uma cópia da cadeia de conexão do dispositivo obtido da ferramenta Gerenciador de Dispositivos para essa função. Você também pode designar o protocolo de comunicação a ser usado. Este exemplo usa MQTT, mas AMQP e HTTPS também são opções.

Quando você tiver um **IOTHUB\_CLIENT\_HANDLE** válido, será possível iniciar a chamada a APIs para enviar e receber mensagens do Hub IoT.

### <a name="send-messages"></a>Enviar mensagens

O aplicativo de exemplo configura um loop para enviar mensagens para o Hub IoT. O seguinte trecho de código:

- Cria uma mensagem.
- Adiciona uma propriedade à mensagem.
- Envia uma mensagem.

Em primeiro lugar, crie uma mensagem:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Sempre que você enviar uma mensagem, será possível especificar uma referência a uma função de retorno de chamada que é invocada quando os dados são enviados. Neste exemplo, a função de retorno de chamada se chama **SendConfirmationCallback**. O trecho abaixo mostra essa função de retorno de chamada:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Observe a chamada à função **IoTHubMessage\_Destroy** quando terminar com a mensagem. Essa função libera os recursos alocados ao criar a mensagem.

### <a name="receive-messages"></a>Receber mensagens

O recebimento de uma mensagem é uma operação assíncrona. Primeiro, você registra o retorno de chamada a ser invocado quando o dispositivo recebe uma mensagem:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
...
```

O último parâmetro é um ponteiro nulo para o que você quiser. No exemplo, é um ponteiro para um inteiro, mas poderia ser um ponteiro para uma estrutura de dados mais complexa. Esse parâmetro permite que a função de retorno de chamada opere em estado compartilhado com o chamador dessa função.

Quando o dispositivo receber uma mensagem, a função de retorno de chamada registrada será invocada. Essa função de retorno de chamada recupera:

* A ID da mensagem e a ID de correlação da mensagem.
* O conteúdo da mensagem.
* Todas as propriedades personalizadas da mensagem.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Use a função **IoTHubMessage\_GetByteArray** para recuperar a mensagem que, neste exemplo, é uma cadeia de caracteres.

### <a name="uninitialize-the-library"></a>Não inicializar a biblioteca

Quando acabar de enviar eventos e de receber mensagens, você poderá cancelar a inicialização da biblioteca IoT. Para fazer isso, emita a seguinte chamada de função:

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Essa chamada libera os recursos anteriormente alocados pela função **IoTHubClient\_CreateFromConnectionString**.

Como você pode ver, é fácil enviar e receber mensagens com a biblioteca **IoTHubClient** . A biblioteca lida com os detalhes de comunicação com o Hub IoT, incluindo o protocolo a ser usado (da perspectiva do desenvolvedor, isso é uma opção de configuração simples).

A biblioteca **IoTHubClient** também fornece controle preciso sobre como serializar os dados que o seu dispositivo envia ao Hub IoT. Em alguns casos, esse nível de controle é uma vantagem, mas em outros, é um detalhe de implementação com que você não quer se preocupar. Se for esse o caso, você poderá considerar usar a biblioteca **serializer** , que está descrita na seção a seguir.

## <a name="use-the-serializer-library"></a>Usar a biblioteca do serializador

Conceitualmente, a biblioteca **serializer** fica acima da biblioteca **IoTHubClient** no SDK. Ela usa a biblioteca **IoTHubClient** para a comunicação subjacente com o Hub IoT, mas adiciona recursos de modelagem que removem a sobrecarga de lidar com a serialização de mensagens do desenvolvedor. O funcionamento dessa biblioteca é mais bem demonstrado por um exemplo.

Na pasta **serializer** do [repositório azure-iot-sdk](https://github.com/Azure/azure-iot-sdk-c), há uma pasta **samples** que contém um aplicativo chamado **simplesample\_mqtt**. A versão para Windows deste exemplo inclui a seguinte solução do Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_mqtt.PNG)

> [!NOTE]
> Se você abrir esse projeto no Visual Studio 2017, aceite os prompts a fim de redirecionar o projeto para a versão mais recente.

Assim como no exemplo anterior, este inclui vários pacotes NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

Você viu a maior parte desses pacotes no exemplo anterior, mas **Microsoft.Azure.IoTHub.Serializer** é novo. Este pacote é necessário quando você usa a biblioteca **serializer**.

A implementação do aplicativo de exemplo pode ser encontrada no arquivo **simplesample\_mqtt.c**.

As seções a seguir explicam as principais partes desse exemplo.

### <a name="initialize-the-library"></a>Inicializar a biblioteca

Para começar a trabalhar com a biblioteca **serializer**, chame as APIs de inicialização:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

A chamada à função **serializer\_init** é uma chamada única e inicializa a biblioteca subjacente. Então, você chama a função **IoTHubClient\_LL\_CreateFromConnectionString**, que é a mesma API do exemplo **IoTHubClient**. Essa chamada define a cadeia de conexão de dispositivo (ela é também onde você escolhe o protocolo que quer usar). Este exemplo usa MQTT como transporte, mas pode usar AMQP ou HTTPS.

Por fim, chame a função **CREATE\_MODEL\_INSTANCE**. **WeatherStation** é o namespace do modelo e **ContosoAnemometer** é o nome do modelo. Depois que a instância do modelo for definida, você poderá usá-lo para começar a enviar e a receber mensagens. No entanto, em primeiro lugar, é importante entender o que é um modelo.

### <a name="define-the-model"></a>Definir o modelo

Um modelo na biblioteca **serializer** define as mensagens que seu dispositivo pode enviar ao Hub IoT e as mensagens, chamadas de *ações* na linguagem de modelagem, que ele pode receber. Defina um modelo usando um conjunto de macros C, como no exemplo de aplicativo **simplesample\_mqtt**:

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

As macros **BEGIN\_NAMESPACE** e **END\_NAMESPACE** usam o namespace do modelo como um argumento. Espera-se que qualquer coisa entre essas macros seja a definição de seus modelos e as estruturas de dados que os modelos usam.

Neste exemplo, há um único modelo chamado **ContosoAnemometer**. Esse modelo define dois dados que o dispositivo pode enviar ao Hub IoT: **DeviceId** e **WindSpeed**. Ele também define três ações (mensagens) que o dispositivo pode receber: **TurnFanOn**, **TurnFanOff** e **SetAirResistance**. Cada elemento de dados tem um tipo, e cada ação tem um nome (e, opcionalmente, um conjunto de parâmetros).

Os dados e ações definidos no modelo definem uma superfície de API que você pode usar para enviar mensagens ao Hub IoT, bem como para responder a mensagens enviadas ao dispositivo. O uso desse modelo é compreendido melhor por meio de um exemplo.

### <a name="send-messages"></a>Enviar mensagens

O modelo define os dados que você pode enviar ao Hub IoT. Neste exemplo, isso significa um dos dois itens de dados definidos usando a macro **WITH_DATA**. Há várias etapas necessárias para enviar os valores **DeviceId** e **WindSpeed** para um Hub IoT. A primeira é definir os dados que deseja enviar:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

O modelo que você definiu anteriormente permite que você defina os valores definindo membros de uma **struct**. Em seguida, serialize a mensagem que deseja enviar:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Esse código serializa o dispositivo-para-nuvem em um buffer (conhecido como **destination**). O código chama a função **sendMessage** para enviar a mensagem para o Hub IoT:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```


Do segundo ao último parâmetro de **IoTHub\_LL\_ClientSendEventAsync** é uma referência a uma função de retorno de chamada que é chamada quando os dados são enviados com êxito. Aqui está a função de retorno de chamada no exemplo:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

O segundo parâmetro é um ponteiro para contexto do usuário, o mesmo ponteiro que passamos para **IoTHubClient\_LL\_SendEventAsync**. Nesse caso, esse contexto é um contador simples, mas ele pode ser o que você quiser.

Isso é tudo para enviar mensagens de dispositivo para nuvem. Agora nos resta falar sobre o recebimento de mensagens.

### <a name="receive-messages"></a>Receber mensagens

O recebimento de uma mensagem funciona de forma semelhante a como as mensagens funcionam na biblioteca **IoTHubClient** . Primeiramente, você registra uma função de retorno de chamada de mensagem:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Em seguida, você grava a função de retorno de chamada que é invocada quando uma mensagem é recebida:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Esse código é clichê, é o mesmo para qualquer solução. Essa função recebe a mensagem e cuida de encaminhá-la à função apropriada por meio da chamada a **EXECUTE\_COMMAND**. A função chamada nesse ponto depende da definição das ações em seu modelo.

Ao definir uma ação em seu modelo, você precisa implementar uma função que é chamada quando o dispositivo recebe a mensagem correspondente. Por exemplo, se o seu modelo definir esta ação:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Defina uma função com esta assinatura:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Observe como o nome da função corresponde ao nome da ação no modelo e que os parâmetros da função correspondem aos parâmetros especificados para a ação. O primeiro parâmetro é sempre obrigatório e contém um ponteiro para a instância do seu modelo.

Quando o dispositivo recebe uma mensagem que corresponde a essa assinatura, a função correspondente é chamada. Portanto, além de ter que incluir o código clichê de **IoTHubMessage**, receber mensagens será apenas uma questão de definir uma função simples para cada ação definida no seu modelo.

### <a name="uninitialize-the-library"></a>Não inicializar a biblioteca

Quando acabar de enviar dados e de receber mensagens, você poderá cancelar a inicialização da biblioteca IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Cada uma dessas três funções se alinha às três funções de inicialização descritas anteriormente. Chamar essas APIs garante a liberação dos recursos alocados anteriormente.

## <a name="next-steps"></a>Próximas etapas

Este artigo abordou os conceitos básicos de como usar as bibliotecas no **SDK do dispositivo IoT do Azure para C**. Ele forneceu informações suficientes para entender o que está incluído no SDK, sua arquitetura e como começar a trabalhar com os exemplos do Windows. O próximo artigo continua a descrição do SDK, explicando [mais sobre a biblioteca IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Para saber mais sobre como desenvolver para o Hub IoT, consulte os [SDKs do Azure IoT][lnk-sdks].

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Simulando um dispositivo com Azure IoT Edge][lnk-iotedge]

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
