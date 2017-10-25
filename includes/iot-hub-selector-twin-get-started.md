> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)

Dispositivos gêmeos são documentos JSON que armazenam informações do estado do dispositivo (metadados, configurações e condições). O Hub IoT persiste um dispositivo gêmeo para cada dispositivo que você conecta a ele.

Use os dispositivos gêmeos para:

* Armazene os metadados de dispositivo de seu back-end da solução.
* Relate informações de estado atual, como recursos disponíveis e condições (por exemplo, o método conectividade usado) do aplicativo do dispositivo.
* Sincronize o estado dos fluxos de trabalho de longa duração (como atualizações de firmware e configuração) entre o aplicativo do dispositivo e o aplicativo back-end.
* Consultar os metadados, a configuração ou o estado do seu dispositivo.

> [!NOTE]
> Dispositivos gêmeos são projetados para sincronização e para consultar condições e configurações de dispositivos. Para obter mais informações sobre quando usar dispositivos gêmeos podem ser encontradas em [Entender dispositivos gêmeos][lnk-twins].

Dispositivos gêmeos são armazenados em um hub IoT e contêm:

* *marcações*, metadados do dispositivo acessíveis apenas por meio do back-end da solução;
* *propriedades desejadas*, objetos JSON modificáveis pelo back-end da solução e observáveis pelo aplicativo de dispositivo; e
* *propriedades relatadas*, objetos JSON modificáveis pelo aplicativo de dispositivo e legíveis pelo back-end da solução. Marcas e propriedades não podem conter matrizes, mas objetos podem ser aninhados.

![][img-twin]

Além disso, o back-end da solução pode consultar dispositivos gêmeos com base em todos os dados acima.
Consulte [Entender dispositivos gêmeos][lnk-twins] para obter mais informações sobre dispositivos gêmeos e a referência de [Linguagem de consulta do Hub IoT][lnk-query] para consultas.


Este tutorial mostra como:

* Criar um aplicativo de back-end que adiciona *marcas* a um dispositivo gêmeo e um aplicativo de dispositivo simulado que relata seu canal de conectividade como uma *propriedade relatada* no dispositivo gêmeo.
* Consulte dispositivos por meio do aplicativo de back-end usando filtros nas marcações e propriedades criadas anteriormente.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md