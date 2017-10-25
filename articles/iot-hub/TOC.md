# Visão geral
## [Azure e IoT](iot-hub-what-is-azure-iot.md)
## [O que é o Hub IoT do Azure?](iot-hub-what-is-iot-hub.md)
## [Visão geral de gerenciamento de dispositivo](iot-hub-device-management-overview.md)

# [Introdução](iot-hub-get-started.md)

## Configurar seu dispositivo
### [Simular um dispositivo em seu PC](iot-hub-get-started-simulated.md)
#### [.NET](iot-hub-csharp-csharp-getstarted.md)
#### [Java](iot-hub-java-java-getstarted.md)
#### [Node.js](iot-hub-node-node-getstarted.md)
#### [Python](iot-hub-python-getstarted.md)

### [Usar um simulador online](iot-hub-raspberry-pi-web-simulator-get-started.md)

### [Usar um dispositivo físico](iot-hub-get-started-physical.md)
#### [Raspberry Pi com Python](iot-hub-raspberry-pi-kit-python-get-started.md)
#### [Raspberry Pi com Node.js](iot-hub-raspberry-pi-kit-node-get-started.md)
#### [Raspberry Pi com C](iot-hub-raspberry-pi-kit-c-get-started.md)

#### [MXChip IoT DevKit com Arduino](iot-hub-arduino-iot-devkit-az3166-get-started.md)

#### [Intel Edison com Node.js](iot-hub-intel-edison-kit-node-get-started.md)
#### [Intel Edison com C](iot-hub-intel-edison-kit-c-get-started.md)

#### [Adafruit Feather HUZZAH ESP8266 com IDE Arduino](iot-hub-arduino-huzzah-esp8266-get-started.md)
#### [Sparkfun ESP8266 Thing Dev com IDE Arduino](iot-hub-sparkfun-esp8266-thing-dev-get-started.md)
#### [Adafruit Feather M0 com IDE Arduino](iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md)

#### Usar o Kit de Início do Gateway IoT
##### [Configurar a NUC Intel como um gateway](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
##### [Conectar o gateway ao Hub IoT](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
##### [Usar o gateway para conversão de dados](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)
##### [Solução de problemas](iot-hub-gateway-kit-c-troubleshooting.md)

## Cenários de IoT estendidos
### [Gerenciar mensagens do dispositivos de nuvem com o iothub-explorer](iot-hub-explorer-cloud-device-messaging.md)
### [Salvar mensagens do Hub IoT para o armazenamento de dados do Azure](iot-hub-store-data-in-azure-table-storage.md)
### [Visualização de dados no Power BI](iot-hub-live-data-visualization-in-power-bi.md)
### [Visualização de dados com Aplicativos Web](iot-hub-live-data-visualization-in-web-apps.md)
### [Previsão do tempo usando o Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md)
### [Gerenciamento de dispositivos com o iothub-explorer](iot-hub-device-management-iothub-explorer.md)
### [Monitoramento remoto e notificações com Aplicativos Lógicos](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

# Como
## Plano
### [Comparação entre Hub IoT e Hubs de Eventos](iot-hub-compare-event-hubs.md)
### [Escalar sua solução](iot-hub-scaling.md)
### [Alta disponibilidade e recuperação de desastres](iot-hub-ha-dr.md)
### [Dar suporte a protocolos adicionais](iot-hub-protocol-gateway.md)
## [Desenvolver](iot-hub-how-to.md)
### [Guia do desenvolvedor](iot-hub-devguide.md)
#### [Guia de recursos do dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md)
#### [Guia de recursos da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md)
#### [Enviar e receber mensagens](iot-hub-devguide-messaging.md)
##### [Enviar mensagens de dispositivo para a nuvem para o Hub IoT](iot-hub-devguide-messages-d2c.md)
##### [Ler mensagens de dispositivo para a nuvem do ponto de extremidade interno](iot-hub-devguide-messages-read-builtin.md)
##### [Usar regras de roteamentos e pontos de extremidade personalizados para mensagens de dispositivo para a nuvem](iot-hub-devguide-messages-read-custom.md)
##### [Enviar mensagens da nuvem para o dispositivo do Hub IoT](iot-hub-devguide-messages-c2d.md)
##### [Criar e ler mensagens do Hub IoT](iot-hub-devguide-messages-construct.md)
##### [Escolha um protocolo de comunicação](iot-hub-devguide-protocols.md)
#### [Carregar arquivos de um dispositivo](iot-hub-devguide-file-upload.md)
#### [Gerenciar identidades do dispositivo](iot-hub-devguide-identity-registry.md)
#### [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md)
#### [Entender os dispositivos gêmeos](iot-hub-devguide-device-twins.md)
#### [Invocar métodos diretos em um dispositivo](iot-hub-devguide-direct-methods.md)
#### [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md)
#### [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md)
#### [Linguagem da consulta](iot-hub-devguide-query-language.md)
#### [Cotas e limitação](iot-hub-devguide-quotas-throttling.md)
#### [Exemplos de preços](iot-hub-devguide-pricing.md)
#### [SDKs de serviço e dispositivo](iot-hub-devguide-sdks.md)
#### [Suporte ao MQTT](iot-hub-mqtt-support.md)
#### [Glossário](iot-hub-devguide-glossary.md)
### [Usar o dispositivo IoT SDK para C](iot-hub-device-sdk-c-intro.md)
#### [Usar o IoTHubClient](iot-hub-device-sdk-c-iothubclient.md)
#### [Usar o serializador](iot-hub-device-sdk-c-serializer.md)
### Processar mensagens de dispositivo para nuvem
#### [.NET](iot-hub-csharp-csharp-process-d2c.md)
#### [Java](iot-hub-java-java-process-d2c.md)
### Envie mensagens da nuvem para o dispositivo
#### [.NET](iot-hub-csharp-csharp-c2d.md)
#### [Java](iot-hub-java-java-c2d.md)
#### [Node.js](iot-hub-node-node-c2d.md)
### Carregar arquivos de dispositivos
#### [.NET](iot-hub-csharp-csharp-file-upload.md)
#### [Java](iot-hub-java-java-file-upload.md)
### Introdução a dispositivos gêmeos
#### [Back-end do Node.js/Dispositivo do Node.js](iot-hub-node-node-twin-getstarted.md)
#### [Back-end do .NET/Dispositivo do Node.js](iot-hub-csharp-node-twin-getstarted.md)
#### [Dispositivo do .NET/back-end do .NET](iot-hub-csharp-csharp-twin-getstarted.md)
#### [Dispositivo Java/Back-end de Java](iot-hub-java-java-twin-getstarted.md)
### Usar métodos diretos
#### [Back-end do Node.js/Dispositivo do Node.js](iot-hub-node-node-direct-methods.md)
#### [Back-end do .NET/Dispositivo do Node.js](iot-hub-csharp-node-direct-methods.md)
#### [Dispositivo do .NET/back-end do .NET](iot-hub-csharp-csharp-direct-methods.md)
#### [Dispositivo Java/Back-end de Java](iot-hub-java-java-direct-methods.md)
### Introdução ao gerenciamento de dispositivos
#### [Back-end do Node.js/Dispositivo do Node.js](iot-hub-node-node-device-management-get-started.md)
#### [Back-end do .NET/Dispositivo do Node.js](iot-hub-csharp-node-device-management-get-started.md)
#### [Dispositivo do .NET/back-end do .NET](iot-hub-csharp-csharp-device-management-get-started.md)
#### [Dispositivo Java/Back-end de Java](iot-hub-java-java-device-management-getstarted.md)
### Como usar propriedades gêmeas
#### [Back-end do Node.js/Dispositivo do Node.js](iot-hub-node-node-twin-how-to-configure.md)
#### [Back-end do .NET/Dispositivo do Node.js](iot-hub-csharp-node-twin-how-to-configure.md)
#### [Dispositivo do .NET/back-end do .NET](iot-hub-csharp-csharp-twin-how-to-configure.md)
#### [Dispositivo Java/Back-end de Java](iot-hub-java-java-twin-how-to-configure.md)
### Usar trabalhos do dispositivo para atualizar o firmware do dispositivo
#### [Back-end do Nó/Dispositivo do Nó](iot-hub-node-node-firmware-update.md)
#### [Back-end do .NET/Dispositivo do Node.js](iot-hub-csharp-node-firmware-update.md)
#### [Dispositivo do .NET/back-end do .NET](iot-hub-csharp-csharp-firmware-update.md)
#### [Dispositivo Java/Back-end de Java](iot-hub-java-java-firmware-update.md)
### Agendar e transmitir trabalhos
#### [Back-end do Node.js/Dispositivo do Node.js](iot-hub-node-node-schedule-jobs.md)
#### [Back-end do .NET/Dispositivo do Node.js](iot-hub-csharp-node-schedule-jobs.md)
#### [Java](iot-hub-java-java-schedule-jobs.md)

## Gerenciar
### Crie um hub IoT 
#### [Usar o Portal do Azure](iot-hub-create-through-portal.md)
#### [Usar o Azure PowerShell](iot-hub-create-using-powershell.md)
#### [Usar a CLI do Azure](iot-hub-create-using-cli.md)
#### [Usar a CLI](iot-hub-create-using-cli-nodejs.md)
#### [Usar a API REST](iot-hub-rm-rest.md)
#### [Usar um modelo do Azure PowerShell](iot-hub-rm-template-powershell.md)
#### [Usar um modelo do .NET](iot-hub-rm-template.md)
### Configurar o upload de arquivo
#### [Usar o Portal do Azure](iot-hub-configure-file-upload.md)
#### [Usar o Azure PowerShell](iot-hub-configure-file-upload-powershell.md)
#### [Usar a CLI do Azure](iot-hub-configure-file-upload-cli.md)
### [Monitorar com o diagnóstico](iot-hub-monitor-resource-health.md)
#### [Migrar para as configurações de diagnóstico](iot-hub-migrate-to-diagnostics-settings.md)
#### [Monitoramento de operações](iot-hub-operations-monitoring.md)
### [Métricas de uso](iot-hub-metrics.md)
### [Gerenciamento em massa de dispositivos IoT](iot-hub-bulk-identity-mgmt.md)
### [Configurar a filtragem do IP](iot-hub-ip-filtering.md)

## Segurança
### [Segurança desde o início](iot-hub-security-ground-up.md)
### [Práticas recomendadas de segurança](iot-hub-security-best-practices.md)
### [Arquitetura de segurança](iot-hub-security-architecture.md)
### [Proteger sua implantação de IoT](iot-hub-security-deployment.md)
### Proteger usando Certificado de Autoridade de Certificação X.509
#### [Visão geral de segurança de Certificado de Autoridade de Certificação X.509](iot-hub-x509ca-overview.md)
##### [Conceitos de segurança de Certificado de Autoridade de Certificação X.509](iot-hub-x509ca-concept.md)
#### [Introdução à segurança de Certificado de Autoridade de Certificação X.509](iot-hub-security-x509-get-started.md)
##### [Criar certificados – PowerShell](iot-hub-security-x509-create-certificates.md)

## Azure IoT Edge
### [Visão geral](iot-hub-iot-edge-overview.md)
### Introdução
#### [Linux](iot-hub-linux-iot-edge-get-started.md)
#### [Windows](iot-hub-windows-iot-edge-get-started.md)
### Simular um dispositivo
#### [Linux](iot-hub-linux-iot-edge-simulated-device.md)
#### [Windows](iot-hub-windows-iot-edge-simulated-device.md)
### [Usar um dispositivo real](iot-hub-iot-edge-physical-device.md)
### Criar um módulo
#### [Java](iot-hub-iot-edge-create-module-java.md)
#### [.NET Framework](https://github.com/Azure-Samples/iot-edge-samples#how-to-run-the-net-module-sample-windows-10)
#### [.NET Standard](iot-hub-iot-edge-create-module-dotnet-core.md)
#### [Node.js](iot-hub-iot-edge-create-module-js.md)
### Compilação
#### [.NET Framework](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_binding_sample)
#### [Módulo do .NET Core](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_core_module_sample)
#### [Gateway gerenciado do .NET Core](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_core_managed_gateway)
#### [Java](https://github.com/Azure/iot-edge/tree/master/samples/java_sample)
#### [Node.js](https://github.com/Azure/iot-edge/tree/master/samples/nodejs_simple_sample)
#### [Adicionar módulo dinamicamente](https://github.com/Azure/iot-edge/tree/master/samples/dynamically_add_module_sample)
#### [Módulo de proxy fora do processo](https://github.com/Azure/iot-edge/tree/master/samples/proxy_sample)
#### [Host do módulo nativo](https://github.com/Azure/iot-edge/tree/master/samples/native_module_host_sample)

# Referência
## [Exemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=iot-hub)
## [CLI do Azure](/cli/azure/iot)
## [.NET (Serviço)](/dotnet/api/microsoft.azure.devices)
## [.NET (Dispositivos)](/dotnet/api/microsoft.azure.devices.client)
## [Java (Serviço)](/java/api/com.microsoft.azure.sdk.iot.service)
## [Java (Dispositivos)](/java/api/com.microsoft.azure.sdk.iot.device)
## [SDKs do Node.js](http://azure.github.io/azure-iot-sdk-node/)
## [SDK do dispositivo C](https://azure.github.io/azure-iot-sdk-c/index.html)
## [Azure IoT Edge](http://azure.github.io/iot-edge/)
## [REST (Provedor de Recursos)](https://docs.microsoft.com/rest/api/iothub/iothubresource)
## [REST (Identidades do dispositivo)](https://docs.microsoft.com/rest/api/iothub/deviceapi)
## [REST (Dispositivos Gêmeos)](https://docs.microsoft.com/rest/api/iothub/devicetwinapi)
## [REST (Mensagens do Dispositivo)](https://docs.microsoft.com/rest/api/iothub/httpruntime)
## [REST (Trabalhos)](https://docs.microsoft.com/rest/api/iothub/jobapi)

# Relacionados
## [Azure IoT Suite](https://azure.microsoft.com/documentation/suites/iot-suite/)
## [Hubs de Eventos do Azure](https://azure.microsoft.com/documentation/services/event-hubs/)
## [Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/)
## [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)

# Recursos
## [Catálogo de dispositivos Azure Certified para IoT](https://catalog.azureiotsuite.com/)
## [Centro de desenvolvedores do Azure IoT](https://azure.microsoft.com/develop/iot/)
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/?category=internet-of-things)
## [ferramenta DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)
## [ferramenta iothub-diagnostics](https://github.com/Azure/iothub-diagnostics)
## [ferramenta iothub-explorer](https://github.com/Azure/iothub-explorer)
## [Roteiro de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/iot-hub/)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azureiothub)
## [Preços](https://azure.microsoft.com/pricing/details/iot-hub/)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=iot-hub)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-iot-hub)
## [Estudos de casos técnicos](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=iot-hub)
