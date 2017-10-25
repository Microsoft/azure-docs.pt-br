> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

Este artigo fornece uma passo a passo detalhado do [código de exemplo do Olá, Mundo][lnk-helloworld-sample] para ilustrar os componentes fundamentais da arquitetura do [IoT Edge do Azure][lnk-iot-edge]. A amostra usa o IoT Edge do Azure para criar um gateway simples que registra uma mensagem “hello world” em um arquivo a cada cinco segundos.

Este passo a passo aborda:

* **Arquitetura da amostra do Olá, Mundo**: descreve como os [conceitos de arquitetura do Azure IoT Edge][lnk-edge-concepts] se aplicam à amostra do Olá, Mundo e como os componentes se complementam.
* **Como criar a amostra**: as etapas necessárias para criar a amostra.
* **Como executar a amostra**: as etapas necessárias para executar a amostra. 
* **Saída típica**: um exemplo da saída esperada ao executar a amostra.
* **Trechos de código**: uma coleção de trechos de código para mostrar como a amostra do Olá, Mundo implementa os principais componentes do gateway do IoT Edge.


## <a name="hello-world-sample-architecture"></a>Arquitetura da amostra do Hello World
A amostra do Hello World ilustra os conceitos descritos na seção anterior. A amostra do Olá, Mundo implementa um gateway do IoT Edge que contém um pipeline composto por dois módulos do IoT Edge:

* O módulo *hello world* cria uma mensagem a cada cinco segundos e a transmite para o módulo do agente.
* O módulo *logger* grava as mensagens recebidas em um arquivo.

![Arquitetura de exemplo de Olá, Mundo criada com o IoT Edge do Azure][4]

Conforme descrito na seção anterior, o módulo do Hello World não transmite mensagens diretamente para o módulo de agente a cada cinco segundos. Em vez disso, ele publica uma mensagem no agente a cada cinco segundos.

O módulo de agente recebe a mensagem do agente e age sobre ele gravando o conteúdo da mensagem em um arquivo.

O módulo do agente consome apenas as mensagens do agente; ele nunca publica novas mensagens no agente.

![Como o agente roteia mensagens entre módulos no IoT Edge do Azure][5]

A figura anterior mostra a arquitetura da amostra do Hello World e os caminhos relativos para os arquivos de origem que implementam partes diferentes da amostra no [repositório][lnk-iot-edge]. Explore o código por conta própria ou use os trechos de código neste artigo como guia.

<!-- Images -->
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-edge-concepts]: ../articles/iot-hub/iot-hub-iot-edge-overview.md