> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md)

Este passo a passo do [Exemplo de Upload para a Nuvem de Dispositivo Simulado] mostra como usar o [Azure IoT Edge][lnk-sdk] para enviar a telemetria do dispositivo para a nuvem ao Hub IoT por meio de dispositivos simulados.

Este passo a passo aborda:

* **Arquitetura**: informações de arquitetura sobre o [Exemplo de Upload para a Nuvem de Dispositivo Simulado].
* **Criar e executar**: as etapas necessárias para criar e executar a amostra.

## <a name="architecture"></a>Arquitetura

O [Exemplo de Upload para a Nuvem de Dispositivo Simulado] mostra como criar um gateway que envia a telemetria dos dispositivos simulados para um Hub IoT. Um dispositivo pode não ser capaz de se conectar diretamente com o Hub IoT porque o dispositivo:

* Não usa um protocolo de comunicação compreendido pelo Hub IoT.
* Não é inteligente o suficiente para lembrar a identidade atribuída a ele pelo Hub IoT.

Um gateway de IoT Edge pode resolver esses problemas das seguintes maneiras:

* O gateway compreende o protocolo usado pelos dispositivos simulados, recebe dos dispositivos a telemetria do dispositivo para a nuvem e encaminha essas mensagens ao Hub IoT usando um protocolo compreendido pelo Hub IoT.

* O gateway mapeia identidades do Hub IoT para os dispositivos e atua como um proxy quando um dispositivo envia mensagens para o Hub IoT.

O diagrama a seguir mostra os principais componentes do exemplo, incluindo os módulos do Edge IoT:

![Diagrama - mensagem de dispositivo simulado passa pelo gateway para o Hub IoT][1]

Este exemplo contém três módulos que compõem o gateway:
1. Módulo de ingestão de protocolo
1. Módulo de identificação do MAC &lt;-&gt; Hub IoT
1. Módulo de comunicação do Hub IoT

Os módulos não transmitem mensagens diretamente entre si. Os módulos publicam mensagens em um agente interno, que entrega as mensagens para outros módulos usando um mecanismo de assinatura. Para obter mais informações, consulte [Introdução ao Edge IoT do Azure][lnk-gw-getstarted].

![Diagrama - módulos de gateway se comunicam com o agente][2]

### <a name="protocol-ingestion-module"></a>Módulo de ingestão de protocolo

Esse módulo de ingestão de protocolo é o ponto de partida para o processo de obtenção de dados de dispositivos, por meio do gateway, e inserção na nuvem. 

No exemplo, esse módulo:

1. Cria dados de temperatura simulados. Se você estiver usando dispositivos físicos, o módulo lerá os dados desses dispositivos físicos.
1. Cria uma mensagem.
1. Coloca os dados de temperatura simulados no conteúdo da mensagem.
1. Adiciona uma propriedade com um endereço MAC falso para a mensagem.
1. Disponibiliza a mensagem para o próximo módulo na cadeia.

O módulo de ingestão de protocolo é **simulated_device.c** no código-fonte.

### <a name="mac-lt-gt-iot-hub-id-module"></a>Módulo de identificação do MAC &lt;-&gt; Hub IoT

O módulo de ID de Hub IoT MAC &lt;-&gt; funciona como conversor. Esta amostra usa um endereço MAC como um identificador de dispositivo exclusivo e o correlaciona com uma identidade de dispositivo Hub IoT. No entanto, é possível escrever seu próprio módulo que usa um identificador exclusivo diferente. Por exemplo, os dispositivos podem ter números de série exclusivos ou os dados telemétricos podem incluir um nome de dispositivo incorporado exclusivo.

No exemplo, esse módulo:

1. Procura mensagens que tenham uma propriedade de endereço MAC.
1. Se houver um endereço MAC, adicionará outra propriedade com uma chave de dispositivo do Hub IoT para a mensagem. 
1. Disponibiliza a mensagem para o próximo módulo na cadeia.

O desenvolvedor configura um mapeamento entre endereços MAC e as identidades de Hub IoT para associar os dispositivos simulados com identidades de dispositivo Hub IoT. O desenvolvedor adiciona o mapeamento manualmente como parte da configuração do módulo.

O módulo de ID de Hub IoT MAC &lt;-&gt; é **identitymap.c** no código-fonte. 

### <a name="iot-hub-communication-module"></a>Módulo de comunicação do Hub IoT

O módulo de comunicação do Hub IoT abre uma conexão HTTPS única do gateway para o Hub IoT. HTTPS é um dos três protocolos compreendidos pelo Hub IoT. Esse módulo evita a necessidade de abrir uma conexão para cada dispositivo usando multiplexação das conexões de todos os dispositivos através de uma única conexão. Essa abordagem permite que um único gateway se conecte com diversos dispositivos. 

No exemplo, esse módulo:

1. Obtém mensagens com uma propriedade de chave de dispositivo Hub IoT que foi atribuída pelo módulo anterior. 
1. Envia o conteúdo da mensagem ao Hub IoT usando o protocolo HTTPS. 

O módulo de comunicação do Hub IoT é **iothub.c** no código-fonte.

## <a name="before-you-get-started"></a>Antes de começar

Antes de começar, é necessário:

* [Criar um Hub IoT][lnk-create-hub] em sua assinatura do Azure. É necessário ter o nome do seu hub para este passo a passo de exemplo. Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* Adicione dois dispositivos ao hub IoT e anote as IDs e chaves de dispositivo. É possível usar o [gerenciador de dispositivo][lnk-device-explorer] ou a ferramenta [iothub-explorer][lnk-iothub-explorer] para adicionar dispositivos ao Hub IoT e recuperar suas chaves.


<!-- Images -->
[1]: media/iot-hub-iot-edge-simulated-selector/image1.png
[2]: media/iot-hub-iot-edge-simulated-selector/image2.png

<!-- Links -->
[Exemplo de Upload para a Nuvem de Dispositivo Simulado]: https://github.com/Azure/iot-edge/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/iot-edge
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-create-hub]: ../articles/iot-hub/iot-hub-create-through-portal.md