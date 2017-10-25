## <a name="typical-output"></a>Saída típica

O exemplo a seguir mostra a saída gravada no arquivo de log pela amostra do Olá, Mundo. A saída é formatada para legibilidade:

```json
[{
    "time": "Mon Apr 11 13:42:50 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:42:50 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:42:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:43:00 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:45:00 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Trechos de código

Esta seção aborda algumas partes fundamentais do código na amostra do Hello\_World.

### <a name="iot-edge-gateway-creation"></a>Criação do gateway do Edge IoT

Para criar um gateway, implemente um *processo de gateway*. Este programa cria a infraestrutura interna (o agente), carrega os módulos do IoT Edge e configura o processo do gateway. O Edge IoT fornece a função **Gateway\_Create\_From\_JSON** para que você possa inicializar um gateway em um arquivo JSON. Para usar a função **Gateway\_Create\_From\_JSON**, passe para ela o caminho para um arquivo JSON que especifica os módulos do IoT Edge a serem carregados.

É possível encontrar o código para o processo de gateway na amostra do *Olá, Mundo* no arquivo [main.c][lnk-main-c]. Para legibilidade, o trecho de código abaixo mostra uma versão abreviada do código do processo de gateway. Este exemplo de programa cria um gateway e aguarda até que o usuário pressione a tecla **ENTER** antes de eliminar o gateway.

```C
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

O arquivo de configurações do JSON contém uma lista dos módulos do Edge IoT a carregar e os links entre os módulos. Cada módulo do Edge IoT deve especificar um:

* **name**: um nome exclusivo para o módulo.
* **loader**: um carregador que sabe como carregar o módulo desejado. Carregadores são um ponto de extensão para carregar os diferentes tipos de módulos. O IoT Edge fornece carregadores para uso com módulos escritos em .NET, Node.js, Java e C nativos. O exemplo Olá, Mundo usa apenas o carregador nativo em C, pois todos os módulos neste exemplo são bibliotecas dinâmicas escritas em C. Para saber mais sobre como usar os módulos do Edge IoT escritos em linguagens diferentes, consulte os exemplos em [Node.js](https://github.com/Azure/iot-edge/blob/master/samples/nodejs_simple_sample/), [Java](https://github.com/Azure/iot-edge/tree/master/samples/java_sample) ou [.NET](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_binding_sample).
    * **name**: o nome do carregador usado para carregar o módulo.
    * **entrypoint**: o caminho para a biblioteca que contém o módulo. No Linux, essa biblioteca é um arquivo .so, no Windows, é um arquivo .dll. O ponto de entrada é específico para o tipo do carregador que está sendo usado. O ponto de entrada do carregador do Node.js é um arquivo .js. O ponto de entrada do carregador Java é um caminho de classe e um nome de classe. O ponto de entrada do carregador .NET é um nome de assembly e um nome de classe.

* **args**: todas as informações de configuração de que o módulo precisa.

O código a seguir mostra o JSON usado para declarar todos os módulos do Edge IoT para o exemplo Olá, Mundo no Linux. A necessidade de argumentos por um módulo dependerá do design do módulo. Neste exemplo, o módulo do agente usa um argumento que é o caminho para o arquivo de saída, e o módulo do Hello\_World não usa nenhum argumento.

```json
"modules" :
[
    {
        "name" : "logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/logger/liblogger.so"
        }
        },
        "args" : {"filename":"log.txt"}
    },
    {
        "name" : "hello_world",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/hello_world/libhello_world.so"
        }
        },
        "args" : null
    }
]
```

O arquivo JSON também contém os links entre os módulos que serão transmitidos para o agente. Um link tem duas propriedades:

* **source**: um nome de um módulo da seção `modules` ou `\*`.
* **sink**: o nome de um módulo da seção `modules`.

Cada link define uma rota e uma direção para as mensagens. As mensagens do módulo **source** devem ser entregues ao módulo **sink**. Você pode definir o módulo **source** como `\*`, que indica que o módulo **sink** recebe mensagens de qualquer módulo.

O código a seguir mostra o JSON usado para configurar links entre os módulos usados no exemplo Hello\_World no Linux. Cada mensagem produzida pelo módulo `hello_world` será consumida pelo módulo `logger`.

```json
"links":
[
    {
        "source": "hello_world",
        "sink": "logger"
    }
]
```

### <a name="helloworld-module-message-publishing"></a>Publicação de mensagem do módulo do Hello\_World

É possível encontrar o código usado pelo módulo hello\_world para publicar mensagens no arquivo ['hello_world.c'][lnk-helloworld-c]. O trecho de código abaixo mostra uma versão corrigida do código com comentários adicionais sem alguns códigos de tratamento de erro para manter a legibilidade:

```C
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);

    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;

    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

O módulo do hello\_world nunca processa as mensagens publicadas no agente por outros módulos do Edge IoT. Isso torna a implementação do retorno de chamada de mensagem no módulo do Hello\_World uma função não operacional.

```C
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-processing"></a>Processamento de mensagem do módulo do agente

O módulo do Agente recebe mensagens do agente e as grava em um arquivo. Ele nunca publica as mensagens. Portanto, o código do módulo do agente nunca chama a função **Broker_Publish**.

A função **Logger_Receive** no arquivo [logger.c][lnk-logger-c] é o retorno de chamada que invoca o agente para entregar mensagens ao módulo do agente. O trecho de código abaixo mostra uma versão corrigida com comentários adicionais sem alguns códigos de tratamento de erro para manter a legibilidade:

```C
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você executou um gateway IoT Edge simples que grava mensagens em um arquivo de log. Para executar um exemplo que envia mensagens para o Hub IoT, consulte:

- [IoT Edge - enviar mensagens do dispositivo para a nuvem com um dispositivo simulado usando o Linux][lnk-gateway-simulated-linux] 
- [IoT Edge - enviar mensagens do dispositivo para a nuvem com um dispositivo simulado usando o Windows][lnk-gateway-simulated-windows].


<!-- Links -->
[lnk-main-c]: https://github.com/Azure/iot-edge/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/iot-edge/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/iot-edge/blob/master/modules/logger/src/logger.c
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-gateway-simulated-linux]: ../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md
[lnk-gateway-simulated-windows]: ../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md