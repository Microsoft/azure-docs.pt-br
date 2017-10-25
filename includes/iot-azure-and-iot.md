
# <a name="azure-and-internet-of-things"></a>Azure e Internet das Coisas

Bem vindo ao Microsoft Azure e à IoT (Internet das Coisas). Este artigo apresenta as características comuns de uma solução IoT, que você pode implantar usando os serviços do Azure. Uma solução de IoT exige uma comunicação bidirecional segura entre uma grande variedade de dispositivos e um back-end da solução. Um back-end da solução pode usar análise preditiva automatizada para revelar insights do fluxo de eventos do dispositivo para a nuvem.

[IoT Hub do Azure][lnk-iot-hub] é um componente fundamental de qualquer solução de IoT que use os serviços do Azure. O Hub IoT é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end da solução. 

O [IoT Suite do Azure][lnk-iot-suite] fornece implementações completas de ponta a ponta dessa arquitetura para cenários específicos de IoT. Por exemplo:

* A solução *monitoramento remoto* permite que você monitore o status de dispositivos como máquinas de venda automáticas.
* A solução *manutenção preditiva* ajuda a prever as necessidades de manutenção de dispositivos, como bombas em estações de bombeamento remotas, e evitar o tempo de inatividade não programado.
* A solução *fábrica conectada* ajuda você a se conectar e monitorar os dispositivos industriais.

## <a name="iot-solution-architecture"></a>Arquitetura da solução de IoT

O diagrama abaixo mostra uma arquitetura comum da solução IoT. O diagrama não inclui os nomes de todos os serviços específicos do Azure, mas descreve os principais elementos em uma arquitetura da solução de IoT genérica. Nessa arquitetura, os dispositivos IoT coletam dados que eles enviam para um gateway de nuvem. O gateway de nuvem disponibiliza os dados para processamento por outros serviços de back-end. O back-end da solução fornece dados para aplicativos de linha de negócios ou operadores humanos por meio de um painel ou relatório.

![Arquitetura da solução de IoT][img-solution-architecture]

> [!NOTE]
> Para ver uma análise detalhada da arquitetura IoT, consulte a [Arquitetura de Referência do IoT do Microsoft Azure][lnk-refarch].

### <a name="device-connectivity"></a>Conectividade do dispositivo

Nesta solução IoT, os dispositivos enviam telemetria, como leituras de sensor de uma estação de bombeamento, para um ponto de extremidade da nuvem para armazenamento e processamento. Em um cenário de manutenção preditiva, o back-end da solução pode usar a transmissão de dados do sensor para determinar quando uma bomba específica necessita de manutenção. Os dispositivos também podem receber e responder às mensagens de nuvem para o dispositivo lendo mensagens de um ponto de extremidade de nuvem. Por exemplo, no cenário de manutenção preditiva, o back-end da solução pode enviar mensagens para que outras bombas na estação de bombeamento comecem a rotear de novo os fluxos antes da manutenção devida iniciar. Esse procedimento irá assegurar que o engenheiro de manutenção possa começar a consertar o problema imediatamente.

Um dos maiores desafios enfrentados por projetos IoT é sobre conectar dispositivos de forma confiável e segura com o back-end da solução. Os dispositivos IoT têm características diferentes em comparação com outros clientes, como navegadores e aplicativos móveis. Dispositivos IoT:

* Com frequência, são sistemas internos sem operadores humanos.
* Eles podem ser implantados em locais remotos, nos quais o acesso físico é caro.
* Só podem ser acessados por meio do back-end da solução. Não há outra maneira de interagir com o dispositivo.
* Podem ter recursos de energia e de processamento limitados.
* Podem ter conectividade de rede intermitente, lenta ou cara.
* Talvez precisem de protocolos de aplicativo proprietários, personalizados ou específicos do setor.
* Podem ser criados usando um grande conjunto de plataformas de hardware e de software conhecidas.

Além dos requisitos acima, todas as soluções IoT também deverão oferecer escala, segurança e confiabilidade. O conjunto de requisitos de conectividade resultante tem implementação difícil e demorada por meio das tecnologias tradicionais, como contêineres da Web e agentes de mensagens. O Hub IoT do Azure e os SDKs do dispositivo IoT do Azure facilitam a implementação de soluções que atendam a esses requisitos.

Um dispositivo pode se comunicar diretamente com um ponto de extremidade de gateway de nuvem. Se o dispositivo não puder usar qualquer um dos protocolos de comunicação ao qual o gateway de nuvem dá suporte, ele pode se conectar por meio de um gateway intermediário. Por exemplo, o [gateway de protocolo IoT do Azure][lnk-protocol-gateway] poderá realizar a conversão de protocolo se os dispositivos não puderem usar um dos protocolos com suporte pelo Hub IoT.

### <a name="data-processing-and-analytics"></a>Processamento de dados e análise

Na nuvem, um back-end da solução IoT é onde ocorre a maior parte do processamento de dados. O back-end da solução IoT:

* Recebe telemetria em escala de seus dispositivos e determina como processar e armazenar os dados. 
* Pode permitir enviar comandos da nuvem para dispositivos específicos.
* Fornece capacidades de registro de dispositivo que possibilitam que você provisione dispositivos e controle quais dispositivos podem se conectar à sua infraestrutura.
* Permite acompanhar o estado de seus dispositivos e monitorar suas atividades.

No cenário de manutenção preditiva, o back-end da solução armazena dados históricos de telemetria. O back-end da solução pode usar esses dados para identificar padrões que indicam que a manutenção é necessária em determinada bomba.

As soluções de IoT podem incluir loops automáticos de comentários. Por exemplo, um módulo de análise no back-end da solução pode identificar da telemetria que a temperatura de um dispositivo específico está acima do nível operacional normal. A solução pode enviar um comando para o dispositivo, instruindo-o para executar uma ação corretiva.

### <a name="presentation-and-business-connectivity"></a>Conectividade de negócios e apresentação

A camada de conectividade de negócios e apresentação permite que os usuários finais interajam com os dispositivos e solução IoT. Ela permite aos usuários exibir e analisar os dados coletados de seus dispositivos. Esses modos de exibição podem assumir a forma de painéis ou relatórios, que podem exibir tanto os dados históricos quanto os dados quase em tempo real. Por exemplo, um operador pode verificar o status de uma estação de bombeamento específica e ver quaisquer alertas gerados pelo sistema. Essa camada também permite a integração do back-end da solução IoT com aplicativos de linhas de negócios existentes para ligar fluxos de trabalho e processos de negócios. Por exemplo, a solução de manutenção preditiva pode integrar-se a um sistema de agendamento. Quando a solução identifica uma bomba que necessita de manutenção, o sistema de agendamento agenda um engenheiro para visitar a estação de bombeamento.

![Painel da solução IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
