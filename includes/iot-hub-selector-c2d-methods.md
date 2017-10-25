> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-direct-methods.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-direct-methods.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-direct-methods.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-direct-methods.md)

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end da solução. Os tutoriais anteriores ([Introdução ao Hub IoT] e [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT]) ilustram a funcionalidade básica de mensagem do dispositivo para a nuvem e da nuvem para o dispositivo do Hub IoT. O Hub IoT também oferece a capacidade de invocar métodos não duráveis em dispositivos da nuvem. Métodos diretos representam uma interação de solicitação-resposta com um dispositivo semelhante a uma chamada HTTPS no sentido de que é bem sucedida ou falha imediatamente (após um tempo limite especificado pelo usuário) para permitir que o usuário saiba o status da chamada. [Invocar um método direto em um dispositivo][lnk-devguide-methods] descreve métodos diretos em mais detalhes e oferece diretrizes sobre quando usar métodos diretos em vez de mensagens da nuvem para o dispositivo ou propriedades desejadas.

Este tutorial mostra como:

* Usar o portal do Azure para criar um Hub IoT e criar uma identidade de dispositivo em seu Hub IoT.
* Criar um aplicativo de dispositivo simulado que tem um método direto que pode ser chamado pela nuvem.
* Criar um aplicativo de console que chama um método direto no dispositivo simulado por meio do Hub IoT.


[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[Como enviar mensagens da nuvem para o dispositivo com o Hub IoT]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[Introdução ao Hub IoT]: ../articles/iot-hub/iot-hub-node-node-getstarted.md