> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Introdução

Em [Introdução a dispositivos gêmeos do Hub IoT][lnk-twin-tutorial], você aprendeu a definir metadados de dispositivo do back-end de solução usando *marcas*, relatar as condições de dispositivo de um aplicativo de dispositivo usando *propriedades relatadas* e consultar essas informações usando uma linguagem semelhante a SQL.

Neste tutorial, você aprende a usar as *propriedades desejadas* do dispositivo gêmeo juntamente com as *propriedades relatadas*, para configurar os remotamente os aplicativos de dispositivo. Mais especificamente, este tutorial mostra como as propriedades relatadas e desejadas de um dispositivo gêmeo habilitam uma configuração de várias etapas de um aplicativo de dispositivo e fornecem a visibilidade para o back-end de solução do status da operação em todos os dispositivos. Você pode encontrar mais informações sobre a função das configurações do dispositivo em [Visão geral do gerenciamento de dispositivos com o Hub IoT][lnk-dm-overview].

Em um nível elevado, usar dispositivos gêmeos permite que o back-end de solução especifique as configurações desejadas para os dispositivos gerenciados, em vez de enviar comandos específicos. Isso faz com que o dispositivo seja responsável por definir a melhor maneira de atualizar sua configuração (importante em cenários de IoT em que condições específicas de dispositivo afetam a capacidade de executar imediatamente comandos específicos), enquanto relata continuamente para o back-end da solução o estado atual e possíveis condições de erro do processo de atualização. Esse padrão é fundamental para o gerenciamento de grandes conjuntos de dispositivos, pois ele permite que o back-end da solução tenha visibilidade total do estado do processo de configuração em todos os dispositivos.

> [!NOTE]
> Em cenários em que os dispositivos são controlados de forma mais interativa (ativar um ventilador por meio de um aplicativo controlado pelo usuário), considere usar [métodos diretos][lnk-methods].
> 
> 

Neste tutorial, o back-end de solução altera a configuração de telemetria de um dispositivo de destino e, como resultado disso, o aplicativo do dispositivo segue um processo de várias etapas para aplicar uma atualização de configuração (por exemplo, exigir a reinicialização de um módulo de software, que este tutorial simula com um atraso simples).

O back-end de solução armazena a configuração nas propriedades desejadas do gêmeo do dispositivo da seguinte maneira:

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

> [!NOTE]
> Como as configurações podem ser objetos complexos, são atribuídas IDs exclusivas (hashes ou [GUIDs][lnk-guid]) para simplificar suas comparações.
> 
> 

O aplicativo do dispositivo relata sua configuração atual espelhando a propriedade desejada **telemetryConfig** nas propriedades relatadas:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Observe como o relatório **telemetryConfig** tem uma propriedade adicional **status**, usada para relatar o estado do processo de atualização de configuração.

Quando uma nova configuração desejada é recebida, o aplicativo de dispositivo relata uma configuração pendente alterando as informações:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Em seguida, posteriormente, o aplicativo do dispositivo relata o sucesso ou a falha dessa operação atualizando a propriedade acima.
Observe como o back-end da solução é capaz de, a qualquer momento, consultar o status do processo de configuração em todos os dispositivos.

Este tutorial mostra como:

* Criar um aplicativo de dispositivo simulado que recebe atualizações de configuração de back-end da solução e relata várias atualizações como *propriedades relatadas* no processo de atualização da configuração.
* Crie um aplicativo de back-end que atualiza a configuração desejada de um dispositivo e consulta o processo de atualização de configuração.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
