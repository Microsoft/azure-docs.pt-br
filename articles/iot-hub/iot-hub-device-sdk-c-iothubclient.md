---
title: SDK do dispositivo IoT do Azure para C - IoTHubClient | Microsoft Docs
description: Como usar a biblioteca do IoTHubClient no SDK do dispositivo IoT do Azure para C para criar aplicativos para dispositivos que se comunicam com um Hub IoT.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: yizhon
ms.openlocfilehash: ff766375dd9ad7cb3bbdf1ef686abb77d1206099
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797861"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>SDK do dispositivo IoT do Azure para C – mais sobre o IoTHubClient

[SDK do dispositivo IoT do Azure para C](iot-hub-device-sdk-c-intro.md) é o primeiro artigo desta série que introduz o **SDK do dispositivo IoT do Azure para C**. Esse artigo explicou que há duas camadas de arquitetura no SDK. Na base está a biblioteca **IoTHubClient**, que gerencia a comunicação direta com o Hub IoT. Também há a biblioteca do **serializador** , que se baseia nisso para fornecer serviços de serialização. Neste artigo, forneceremos detalhes adicionais sobre a biblioteca **IoTHubClient**.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O artigo anterior descreveu como usar a biblioteca **IoTHubClient** para enviar eventos ao Hub IoT e receber mensagens. Este artigo estende a discussão explicando como gerenciar com mais precisão *o momento em que* você envia e recebe dados, apresentando as **APIs de nível inferior**. Também vamos explicar como anexar propriedades aos eventos (e recuperá-las de mensagens) usando os recursos de tratamento de propriedade na biblioteca **IoTHubClient** . Por fim, forneceremos uma explicação adicional sobre as diferentes formas de manipular as mensagens recebidas do Hub IoT.

O artigo é concluído com a abordagem de diversos tópicos, incluindo mais sobre as credenciais do dispositivo e como alterar o comportamento do **IoTHubClient** por meio das opções de configuração.

Usaremos os exemplos do SDK do **IoTHubClient** para explicar esses tópicos. Se você quiser acompanhar, confira os aplicativos **iothub\_client\_sample\_http** e **iothub\_client\_sample\_amqp** que estão incluídos no SDK do dispositivo IoT do Azure para o C. Tudo o que é descrito nas seções a seguir é demonstrado nestes exemplos.

Você pode encontrar o [**SDK do dispositivo IoT do Azure para C**](https://github.com/Azure/azure-iot-sdk-c) no repositório GitHub e exibir os detalhes da API [na referência da API do C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>As APIs de nível inferior

O artigo anterior descreveu a operação básica do **IotHubClient** dentro do contexto do aplicativo **iothub\_client\_sample\_amqp**. Por exemplo, ele explicou como inicializar a biblioteca usando este código.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Também descreveu como enviar eventos usando esta chamada de função.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

O artigo também descreveu como receber mensagens registrando uma função de retorno de chamada.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

O artigo também mostrou como liberar recursos usando um código como o a seguir.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Há funções complementares para cada uma dessas APIs:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Todas essas funções incluem **LL** no nome da API. Além da parte **LL** do nome, os parâmetros de cada uma dessas funções são idênticos a seus equivalentes não LL. No entanto, o comportamento dessas funções tem uma diferença importante.

Quando você chama **IoTHubClient\_CreateFromConnectionString**, as bibliotecas subjacentes criam um novo thread, que é executado em segundo plano. Esse thread envia eventos para o Hub IoT e recebe mensagens dele. Nenhum thread desse tipo é criado quando se trabalha com as APIs **LL**. A criação do thread de segundo plano é uma conveniência para o desenvolvedor. Você não precisa se preocupar em enviar eventos explicitamente e em receber mensagens do Hub IoT – isso acontece automaticamente em segundo plano. Em contrapartida, as APIs **LL** dão a você um controle explícito sobre a comunicação com o Hub IoT, caso seja necessário.

Para entender esse conceito melhor, vamos examinar um exemplo:

Quando você chama **IoTHubClient\_SendEventAsync**, na verdade, você está colocando o evento em um buffer. O thread em segundo plano criado quando você chama **IoTHubClient\_CreateFromConnectionString** monitora continuamente esse buffer e envia todos os dados que ele contém ao Hub IoT. Isso acontece em segundo plano ao mesmo tempo que o thread principal está executando outro trabalho.

Da mesma forma, quando você registra uma função de retorno de chamada para mensagens usando **IoTHubClient\_SetMessageCallback**, você está instruindo ao SDK para que o thread em segundo plano invoque a função de retorno de chamada quando uma mensagem for recebida, independentemente do thread principal.

As APIs **LL** não criam um thread em segundo plano. Em vez disso, uma nova API deve ser chamada para enviar e receber dados explicitamente do Hub IoT. Isso é demonstrado no exemplo a seguir.

O aplicativo **iothub\_client\_sample\_http** que está incluído no SDK demonstra as APIs de nível inferior. Neste exemplo, enviamos eventos ao Hub IoT com um código como o seguinte:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

As três primeiras linhas criam a mensagem e a última linha envia o evento. No entanto, como mencionado anteriormente, enviar o evento significa que os dados são simplesmente colocados em um buffer. Nada é transmitido pela rede quando chamamos **IoTHubClient\_LL\_SendEventAsync**. Para realmente inserir os dados no Hub IoT, você precisará chamar **IoTHubClient\_LL\_DoWork**, como neste exemplo:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Este código (do aplicativo **iothub\_client\_sample\_http** application) chama repetidamente **IoTHubClient\_LL\_DoWork**. Toda vez que **IoTHubClient\_LL\_DoWork** é chamado, ele envia alguns eventos do buffer para o Hub IoT e recupera uma mensagem na fila que está sendo enviada ao dispositivo. O último caso significa que, se registramos uma função de retorno de chamada para mensagens, o retorno de chamada é invocado (supondo que todas as mensagens estejam na fila). Teríamos registrado uma função de retorno de chamada com um código como o seguinte:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

O motivo pelo qual **IoTHubClient\_LL\_DoWork** muitas vezes é chamado em um loop é que cada vez que ele é chamado, ele envia *alguns* eventos em buffer ao Hub IoT e recupera *a próxima* mensagem na fila para o dispositivo. Não há garantia de que cada chamada envie todos os eventos em buffer ou recupere todas as mensagens enfileiradas. Se você deseja enviar todos os eventos no buffer e continuar com outro processamento, é possível substituir esse loop por um código como o seguinte:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Esse código chama **IoTHubClient\_LL\_DoWork** até que todos os eventos no buffer tenham sido enviados ao Hub IoT. Observe que isso também não significa que todas as mensagens em fila tenham sido recebidas. Parte do motivo para isso é que a verificação de “todas” as mensagens não é tão determinística como uma ação. O que acontece se você recuperar "todas" as mensagens, mas então outra é enviada para o dispositivo imediatamente depois? Uma maneira melhor de lidar com isso é com um tempo limite programado. Por exemplo, a função de retorno de chamada de mensagem pode redefinir um timer sempre que for invocada. Você poderá, então, escrever a lógica para continuar processando se, por exemplo, nenhuma mensagem tiver sido recebida nos últimos *X* segundos.

Quando terminar de inserir os eventos e receber mensagens, certifique-se de chamar a função correspondente para limpar os recursos.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Basicamente, há apenas um conjunto de APIs para enviar e receber dados com um thread em segundo plano e outro conjunto de APIs que faz a mesma coisa sem o thread em segundo plano. Muitos desenvolvedores podem preferir as APIs não LL, mas as APIs de nível inferior são úteis quando o desenvolvedor desejar ter um controle explícito sobre as transmissões de rede. Por exemplo, alguns dispositivos coletam dados ao longo do tempo e apenas inserem eventos em intervalos especificados (por exemplo, de hora em hora ou uma vez por dia). As APIs de nível inferior permitem controlar explicitamente o momento em que você envia e recebe dados do Hub IoT. Outras pessoas simplesmente preferirão a simplicidade oferecida pelas APIs de nível inferior. Tudo acontece no thread principal, em vez de algum trabalho acontecendo em segundo plano.

Seja qual for o modelo escolhido, seja consistente em relação às APIs usadas. Se você começar chamando **IoTHubClient\_LL\_CreateFromConnectionString**, lembre-se de usar apenas as APIs de nível inferior correspondentes para qualquer trabalho de acompanhamento:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy
* IoTHubClient\_LL\_DoWork

O contrário também é verdadeiro. Se você começar com **IoTHubClient\_CreateFromConnectionString**, use as APIs não LL para qualquer processamento adicional.

No SDK do dispositivo IoT do Azure para C, veja o aplicativo **iothub\_client\_sample\_http** para obter um exemplo completo das APIs de nível inferior. O aplicativo **iothub\_client\_sample\_amqp** pode ser referenciado para um exemplo completo das APIs não LL.

## <a name="property-handling"></a>Manipulação de propriedades

Até agora, quando descrevemos o envio de dados, nos referimos ao corpo da mensagem. Por exemplo, considere este código:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Este exemplo envia uma mensagem ao Hub IoT com o texto “Hello World”. Entretanto, o Hub IoT também permite que as propriedades sejam anexadas a cada mensagem. As propriedades são pares de nome/valor que podem ser anexados à mensagem. Por exemplo, podemos modificar o código anterior para anexar uma propriedade à mensagem:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Começamos chamando **IoTHubMessage\_Properties** e transmitindo-o ao identificador da nossa mensagem. O que obtemos é uma referência de **MAP\_HANDLE** que nos permite começar a adicionar propriedades. O último é realizado chamando **Map\_AddOrUpdate**, que usa uma referência para um MAP\_HANDLE, o nome da propriedade, e o valor da propriedade. Com essa API, podemos adicionar quantas propriedades quisermos.

Quando o evento é lido no **Hubs de Eventos**, o receptor pode enumerar as propriedades e recuperar seus valores correspondentes. Por exemplo, no .NET, isso seria realizado acessando a [Coleção de propriedades no objeto EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

No exemplo anterior, estávamos anexando propriedades a um evento que enviamos ao Hub IoT. As propriedades também podem ser anexadas às mensagens recebidas do Hub IoT. Se quisermos recuperar as propriedades de uma mensagem, podemos usar um código como o seguinte em nossa função de retorno de chamada de mensagem:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

A chamada para **IoTHubMessage\_Properties** retorna a referência **MAP\_HANDLE**. Então transmitimos essa referência para **Map\_GetInternals** para obter uma referência para uma matriz de pares de nome/valor (bem como uma contagem das propriedades). Nesse momento, é simplesmente uma questão de enumerar as propriedades para obter os valores que queremos.

Não é preciso usar propriedades em seu aplicativo. Entretanto, caso você precise defini-las em eventos ou recuperá-las de mensagens, a biblioteca **IoTHubClient** facilitará essa tarefa.

## <a name="message-handling"></a>Manipulação de mensagens

Como declarado anteriormente, quando as mensagens chegam do Hub IoT, a biblioteca **IoTHubClient** responde invocando uma função de retorno de chamada registrada. Há um parâmetro de retorno dessa função que merece uma explicação adicional. Veja um trecho da função de retorno de chamada no aplicativo de exemplo **iothub\_client\_sample\_http**:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Observe que o tipo de retorno é **IOTHUBMESSAGE\_DISPOSITION\_RESULT** e, nesse caso específico, retornamos **IOTHUBMESSAGE\_ACCEPTED**. Há outros valores que podemos retornar dessa função que mudam a forma como a biblioteca **IoTHubClient** reage ao retorno de chamada da mensagem. Veja as opções.

* **IOTHUBMESSAGE\_ACCEPTED**: a mensagem foi processada com êxito. A biblioteca **IoTHubClient** não invocará a função de retorno de chamada novamente com a mesma mensagem.

* **IOTHUBMESSAGE\_REJECTED**: a mensagem não foi processada e não há intenção de fazer isso no futuro. A biblioteca **IoTHubClient** não deve invocar a função de retorno de chamada novamente com a mesma mensagem.

* **IOTHUBMESSAGE\_ABANDONED**: a mensagem não foi processada com êxito, mas a biblioteca **IoTHubClient** deve invocar a função de retorno de chamada novamente com a mesma mensagem.

Para os dois primeiros códigos de retorno, a biblioteca **IoTHubClient** envia uma mensagem ao Hub IoT indicando que a mensagem deve ser excluída da fila do dispositivo e não entregue novamente. O efeito líquido é o mesmo (a mensagem será excluída da fila do dispositivo), mas sem levar em conta se a mensagem foi aceita ou rejeitada, ela ainda será registrada.  O registro dessa distinção é útil para os remetentes da mensagem, que podem ouvir comentários e descobrir se um dispositivo aceitou ou rejeitou uma mensagem específica.

No último caso, uma mensagem também é enviada ao Hub IoT, mas ela indica que a mensagem deve ser entregue novamente. Geralmente, você abandonará uma mensagem se encontrar algum erro, mas é conveniente tentar processar a mensagem novamente. Em contrapartida, rejeitar uma mensagem é apropriado quando você encontrar um erro irrecuperável (ou se simplesmente decidir que não quer processar a mensagem).

De qualquer forma, esteja ciente dos diferentes códigos de retorno para que você possa extrair o comportamento que deseja da biblioteca **IoTHubClient** .

## <a name="alternate-device-credentials"></a>Credenciais de dispositivo alternativas

Como explicado anteriormente, a primeira coisa a fazer ao trabalhar com a biblioteca **IoTHubClient** é obter um **IOTHUB\_CLIENT\_HANDLE** com uma chamada como a seguinte:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Os argumentos para **IoTHubClient\_CreateFromConnectionString** são a cadeia de conexão do dispositivo e um parâmetro que indica o protocolo que usamos para nos comunicar com o Hub IoT. A cadeia de conexão do dispositivo tem um formato parecido com este:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Há quatro tipos de informações na cadeia de caracteres: Nome do IoT Hub, sufixo do IoT Hub, ID do dispositivo e chave de acesso compartilhado. Você obtém o FQDN (nome de domínio totalmente qualificado) de um Hub IoT quando cria a instância do Hub IoT no portal do Azure — isso fornece o nome do Hub IoT (a primeira parte do FQDN) e o sufixo do Hub IoT (o restante do FQDN). Você obtém a ID do dispositivo e a chave de acesso compartilhado ao registrar seu dispositivo no Hub IoT (como descrito no [artigo anterior](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** oferece uma maneira de inicializar a biblioteca. Se preferir, você poderá criar um novo **IOTHUB\_CLIENT\_HANDLE** usando esses parâmetros individuais em vez da cadeia de conexão do dispositivo. Isso é obtido com o código a seguir:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Ele faz a mesma coisa que **IoTHubClient\_CreateFromConnectionString**.

Pode parecer óbvio que você queira usar **IoTHubClient\_CreateFromConnectionString** no lugar desse método mais detalhado de inicialização. Entretanto, tenha em mente que, ao registrar um dispositivo no Hub IoT, o que obtemos é uma ID de dispositivo e uma chave do dispositivo (e não uma cadeia de conexão). A ferramenta SDK do *gerenciador de dispositivos* apresentada no [artigo anterior](iot-hub-device-sdk-c-intro.md) usa bibliotecas no **SDK do serviço IoT do Azure** para criar a cadeia de conexão do dispositivo com base na ID e chave do dispositivo e do nome de host do Hub IoT. Dessa forma, é preferível chamar **IoTHubClient\_LL\_Create**, pois economiza a etapa de geração de uma cadeia de conexão. Use o método que for conveniente.

## <a name="configuration-options"></a>Opções de configuração

Até agora, tudo que foi descrito sobre como a biblioteca **IoTHubClient** funciona reflete seu comportamento padrão. Entretanto, há algumas opções que você pode definir para alterar o funcionamento da biblioteca. Isso pode ser feito aproveitando a API **IoTHubClient\_LL\_SetOption**. Considere este exemplo:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Há duas opções que normalmente são usadas:

* **SetBatching** (bool) – Se for **true**, os dados enviados ao Hub IoT serão enviados em lotes. Se for **false**, as mensagens serão enviadas individualmente. O padrão é **false**. Envio em lote sobre AMQP WS AMQP, bem como a adição de propriedades do sistema em mensagens D2C, há suporte.

* **Tempo limite** (inteiro não atribuído) – Esse valor é representado em milissegundos. Se o envio de uma solicitação HTTPS ou o recebimento de uma resposta demorar mais que esse tempo, o tempo limite da conexão se esgotará.

A opção de envio em lote é importante. Por padrão, a biblioteca insere eventos individualmente (um evento único é tudo o que você transmite para **IoTHubClient\_LL\_SendEventAsync**). Mas se a opção de envio em lote for **true**, a biblioteca coletará o máximo de eventos que puder do buffer (até o tamanho máximo de mensagem que o Hub IoT aceitar).  O lote do evento é enviado ao Hub IoT em uma única chamada de HTTPS (os eventos individuais são agrupados em uma matriz JSON). A habilitação do envio em lote geralmente resulta em grandes ganhos de desempenho, uma vez que você está reduzindo as viagens de ida e volta da rede. Ela também reduz significativamente a largura de banda, uma vez que você está enviando um conjunto de cabeçalhos HTTPS com um lote de eventos, em vez de um conjunto de cabeçalhos para cada evento individual. A menos que você tenha um motivo específico para fazer o contrário, o comum é habilitar o envio em lote.

## <a name="next-steps"></a>Próximas etapas

Este artigo descreve em detalhes o comportamento da biblioteca **IoTHubClient** encontrada no **SDK do dispositivo IoT do Azure para C**. Com essas informações, você deverá ter um bom entendimento dos recursos da biblioteca **IoTHubClient**. O segundo artigo desta série é [SDK do dispositivo IoT do Azure para C – serializador](iot-hub-device-sdk-c-serializer.md), que fornece detalhes semelhantes sobre a biblioteca de **serializador**.

Para saber mais sobre como desenvolver para o Hub IoT, confira os [SDKs de IoT do Azure](iot-hub-devguide-sdks.md).

Para explorar ainda mais os recursos do Hub IoT, confira [implantar IA em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
