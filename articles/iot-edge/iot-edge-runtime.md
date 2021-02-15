---
title: Saiba como o runtime gerencia dispositivos – Azure IoT Edge | Microsoft Docs
description: Saiba como o tempo de execução do IoT Edge gerencia módulos, segurança, comunicação e relatórios em seus dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: c0c3a452c93b88483ac7027405665c26ceab8183
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368487"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Reconhecer o runtime do Azure IoT Edge e sua arquitetura

O runtime do IoT Edge é uma coleção de programas que transforma um dispositivo em um dispositivo IoT Edge. Coletivamente, os componentes de tempo de execução IoT Edge permitem que os dispositivos IoT Edge recebam código para serem executados na borda e comuniquem os resultados.

O tempo de execução de IoT Edge é responsável pelas seguintes funções em dispositivos IoT Edge:

* Instala e atualiza as cargas de trabalho no dispositivo.

* Mantém os padrões de segurança do Azure IoT Edge no dispositivo.

* Verifique se os [módulos IOT Edge](iot-edge-modules.md) estão sempre em execução.

* Relata a integridade do módulo à nuvem para monitoramento remoto.

* Gerencie a comunicação entre dispositivos downstream e dispositivos IoT Edge.

* Gerenciar a comunicação entre módulos em um dispositivo IoT Edge.

* Gerencie a comunicação entre um IoT Edge dispositivo e a nuvem.
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* Gerenciar a comunicação entre dispositivos IoT Edge.
::: moniker-end

![O runtime comunica insights e integridade de módulo para o Hub IoT](./media/iot-edge-runtime/Pipeline.png)

As responsabilidades do runtime do IoT Edge se enquadram em duas categorias: gerenciamento de comunicação e de módulo. Essas duas funções são executadas por dois componentes que fazem parte do tempo de execução de IoT Edge. O *agente de IOT Edge* implanta e monitora os módulos, enquanto o *Hub de IOT Edge* é responsável pela comunicação.

O agente de IoT Edge e o Hub de IoT Edge são módulos, assim como qualquer outro módulo em execução em um dispositivo IoT Edge. Às vezes, eles são chamados de *módulos de tempo de execução*.

## <a name="iot-edge-agent"></a>Agente do IoT Edge

O agente de IoT Edge é um dos dois módulos que compõem o tempo de execução do Azure IoT Edge. Ele é responsável por instanciar módulos, fazendo com que continuem a funcionar, e indicar o status dos módulos para o Hub IoT. Esses dados de configuração são gravados como uma propriedade do módulo do agente de IoT Edge.

O [daemon de segurança do IoT Edge](iot-edge-security-manager.md) inicia o agente do IoT Edge na inicialização do dispositivo. O agente recupera seu módulo gêmeo do Hub IoT e inspeciona o manifesto de implantação. O manifesto de implantação é um arquivo JSON que declara os módulos que precisam ser iniciados.

Cada item no manifesto de implantação contém informações específicas sobre um módulo e é usado pelo agente de IoT Edge para controlar o ciclo de vida do módulo. Para obter mais informações sobre todas as propriedades usadas pelo agente de IoT Edge para controlar módulos, leia sobre as [Propriedades do agente de IOT Edge e do módulo de Hub de IOT Edge gêmeos](module-edgeagent-edgehub.md).

O agente do IoT Edge envia a resposta de runtime para o Hub IoT. Aqui está uma lista das possíveis respostas:
  
* 200 - OK
* 400 - A configuração de implantação está malformada ou inválida.
* 417-o dispositivo não tem um conjunto de configuração de implantação.
* 412 - A versão do esquema na configuração de implantação é inválida.
* 406 – o dispositivo do IoT Edge está offline ou não está enviando relatórios de status.
* 500 – ocorreu um erro no runtime do IoT Edge.

Para obter mais informações sobre como criar manifestos de implantação, consulte [saiba como implantar módulos e estabelecer rotas em IOT Edge](module-composition.md).

### <a name="security"></a>Segurança

O agente do IoT Edge desempenha um papel fundamental na segurança de um dispositivo IoT Edge. Por exemplo, ele executa ações como verificar a imagem de um módulo antes de iniciá-lo.

Para obter mais informações sobre a estrutura de segurança do Azure IoT Edge, leia sobre o [Gerenciador de segurança do IOT Edge](iot-edge-security-manager.md).

## <a name="iot-edge-hub"></a>Hub do IoT Edge

O Hub de IoT Edge é o outro módulo que compõe o tempo de execução de Azure IoT Edge. Ele atua como um proxy local para o Hub IoT expondo os mesmos pontos de extremidade de protocolo que o Hub IoT. Essa consistência significa que os clientes podem se conectar ao tempo de execução do IoT Edge da mesma forma que fariam com o Hub IoT.

O Hub de IoT Edge não é uma versão completa do Hub IoT em execução localmente. IoT Edge Hub Delega silenciosamente algumas tarefas ao Hub IoT. Por exemplo, IoT Edge Hub baixa automaticamente as informações de autorização do Hub IoT na primeira conexão para permitir que um dispositivo se conecte. Depois que a primeira conexão é estabelecida, as informações de autorização são armazenadas em cache localmente pelo Hub IoT Edge. As conexões futuras desse dispositivo são autorizadas sem precisar baixar informações de autorização da nuvem novamente.

### <a name="cloud-communication"></a>Comunicação na nuvem

Para reduzir a largura de banda que sua solução de IoT Edge usa, o Hub de IoT Edge otimiza quantas conexões reais são feitas na nuvem. O Hub de IoT Edge usa conexões lógicas de módulos ou dispositivos downstream e os combina para uma única conexão física com a nuvem. Os detalhes desse processo são transparentes para o restante da solução. Os clientes pensam que têm sua própria conexão para a nuvem, mesmo que estejam todos sendo enviados pela mesma conexão. O Hub de IoT Edge pode usar o AMQP ou o protocolo MQTT para comunicar upstream com a nuvem, independentemente dos protocolos usados pelos dispositivos downstream. No entanto, o Hub de IoT Edge atualmente só dá suporte à combinação de conexões lógicas em uma única conexão física usando AMQP como o protocolo upstream e seus recursos de multiplexação. AMQP é o protocolo padrão de upstream.

![O hub do IoT Edge é um gateway entre os dispositivos físicos e o Hub IoT](./media/iot-edge-runtime/gateway-communication.png)

O hub do IoT Edge pode determinar se ele está conectado ao Hub IoT. Se a conexão for perdida, o hub do IoT Edge salvará mensagens ou as atualizações duplicadas localmente. Depois que uma conexão é restabelecida, ela sincroniza todos os dados. O local usado para esse cache temporário é determinado por uma propriedade do módulo de IoT Edge do Hub. O tamanho do cache não tem limite e aumentará até atingir toda a capacidade de armazenamento do dispositivo. Para obter mais informações, consulte [recursos offline](offline-capabilities.md).

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>Comunicação do módulo

O hub do IoT Edge facilita a comunicação de módulo para módulo. O uso do hub do IoT Edge como um agente de mensagem mantém os módulos independentes uns dos outros. Os módulos precisam apenas especificar as entradas nas quais eles aceitam mensagens e as saídas nas quais eles gravam mensagens. Um desenvolvedor de soluções pode reunir essas entradas e saídas para que os módulos processem dados na ordem específica para essa solução.

![O hub do IoT Edge facilita a comunicação de módulo para módulo](./media/iot-edge-runtime/module-endpoints.png)

Para enviar dados ao hub do IoT Edge, um módulo chamará o método SendEventAsync. O primeiro argumento especifica em qual saída enviar a mensagem. O pseudocódigo a seguir envia uma mensagem em **Saída1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Para receber uma mensagem, registre um retorno de chamada que processa as mensagens recebidas em uma entrada específica. O pseudocódigo a seguir registra a função messageProcessor a ser usada para processar todas as mensagens recebidas em **entrada1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Para obter mais informações sobre a classe ModuleClient e seus métodos de comunicação, consulte a referência de API para sua linguagem de SDK preferida: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)ou [Node.js](/javascript/api/azure-iot-device/moduleclient).

O desenvolvedor da solução é responsável por especificar as regras que determinam como o hub do IoT Edge passa mensagens entre os módulos. As regras de roteamento são definidas na nuvem e enviadas por push para IoT Edge Hub em seu módulo. A mesma sintaxe para rotas do Hub IoT é usada para definir rotas entre módulos no Azure IoT Edge. Para obter mais informações, confira [Aprenda a implantar módulos e estabelecer rotas no IoT Edge](module-composition.md).

![As rotas entre módulos passam pelo hub do IoT Edge](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>Comunicação local

IoT Edge Hub facilita a comunicação local. Ele habilita as comunicações de dispositivo para módulo, módulo para módulo e dispositivo para dispositivo por meio de mensagens de agente para manter dispositivos e módulos independentes uns dos outros.

>[!NOTE]
> O recurso do agente MQTT está em visualização pública com IoT Edge versão 1,2. Ele deve ser habilitado explicitamente.

O Hub de IoT Edge dá suporte a dois mecanismos de agente:

1. Os [recursos de roteamento de mensagens com suporte do Hub IOT](../iot-hub/iot-hub-devguide-messages-d2c.md) e do,
2. Um agente MQTT de finalidade geral que atende ao [MQTT Standard v 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)

#### <a name="using-routing"></a>Usando o roteamento

O primeiro mecanismo de agente utiliza os mesmos recursos de roteamento que o Hub IoT para especificar como as mensagens são passadas entre dispositivos ou módulos. Os primeiros dispositivos ou módulos especificam as entradas nas quais eles aceitam mensagens e as saídas nas quais eles gravam mensagens. Em seguida, um desenvolvedor de solução pode rotear mensagens entre uma fonte, por exemplo, saídas e um destino, por exemplo, entradas, com filtros potenciais.

![As rotas entre módulos passam pelo hub do IoT Edge](./media/iot-edge-runtime/module-endpoints-routing.png)

O roteamento pode ser usado por dispositivos ou módulos criados com os SDKs do dispositivo IoT do Azure por meio do AMQP ou do protocolo MQTT. Todos os primitivos do Hub IoT do sistema de mensagens, por exemplo, telemetria, métodos diretos, C2D, gêmeos, têm suporte, mas não há suporte para a comunicação em tópicos definidos pelo usuário.

Para obter mais informações sobre rotas, consulte [saiba como implantar módulos e estabelecer rotas no IOT Edge](module-composition.md)

#### <a name="using-the-mqtt-broker"></a>Usando o agente MQTT

O segundo mecanismo de agente é baseado em um agente MQTT padrão. O MQTT é um protocolo leve de transferência de mensagens que garante desempenho ideal em dispositivos com restrição de recursos e é um padrão popular de publicação e assinatura. Dispositivos ou módulos assinam tópicos para receber mensagens publicadas por outros dispositivos ou módulos. IoT Edge Hub implementa seu próprio agente MQTT que segue [as especificações de MQTT da versão 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html).

O agente do MQTT permite dois padrões de comunicação adicionais em comparação com o roteamento: transmissão local e comunicação ponto a ponto. A difusão local é útil quando um dispositivo ou módulo precisa alertar localmente vários outros dispositivos ou módulos. A comunicação ponto a ponto permite que dois dispositivos IoT Edge ou dois dispositivos IoT se comuniquem localmente sem viagens de ida e volta para a nuvem.

![Publicar e assinar localmente com o Hub IoT Edge](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

O agente MQTT pode ser usado por dispositivos ou módulos criados com os SDKs do dispositivo IoT do Azure que se comunicam por meio do protocolo MQTT ou qualquer cliente MQTT de finalidade geral. Com exceção de C2D todos os primitivos do Hub IoT do sistema de mensagens, por exemplo, telemetria, métodos diretos, gêmeos têm suporte. Tópicos especiais do Hub IoT usados por primitivos do Hub IoT têm suporte e, portanto, são tópicos definidos pelo usuário.
Este tópico pode ser um tópico especial do Hub IoT ou um tópico definido pelo usuário.

Ao contrário do mecanismo de roteamento, a ordenação de mensagens é apenas o melhor esforço e não garantido e a filtragem de mensagens não é suportada pelo agente. No entanto, a falta desses recursos permite que o agente do MQTT seja mais rápido do que o roteamento.

Para obter mais informações sobre o agente do MQTT, consulte [publicar e assinar com IOT Edge](how-to-publish-subscribe.md)

#### <a name="comparison-between-brokering-mechanisms"></a>Comparação entre mecanismos de agente

Aqui estão os recursos disponíveis com cada mecanismo de agente:

|Recursos  | Roteamento  | Agente MQTT  |
|---------|---------|---------|
|Telemetria D2C    |     &#10004;    |         |
|Telemetria local     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|Fraudulent     |    &#10004;     |    &#10004;     |
|C2D para dispositivos     |   &#10004;      |         |
|Ordenando     |    &#10004;     |         |
|Filtragem     |     &#10004;    |         |
|Tópicos definidos pelo usuário     |         |    &#10004;     |
|Dispositivo para dispositivo     |         |    &#10004;     |
|Difusão local     |         |    &#10004;     |
|Desempenho     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>Conectando-se ao Hub de IoT Edge

O Hub de IoT Edge aceita conexões de clientes de módulo ou de dispositivo, seja sobre o protocolo MQTT ou o protocolo AMQP.

>[!NOTE]
> IoT Edge Hub dá suporte a clientes que se conectam usando MQTT ou AMQP. Ele não oferece suporte a clientes que usam HTTP.

Quando um cliente se conecta ao Hub de IoT Edge, acontece o seguinte:

1. Se o protocolo TLS for usado (recomendado), um canal TLS será criado para estabelecer uma comunicação criptografada entre o cliente e o Hub de IoT Edge.
2. As informações de autenticação são enviadas do cliente para o Hub IoT Edge para se identificarem.
3. IoT Edge Hub autoriza ou rejeita a conexão com base em sua política de autorização.

#### <a name="secure-connections-tls"></a>Conexões seguras (TLS)

Por padrão, o Hub de IoT Edge só aceita conexões protegidas com TLS (segurança de camada de transporte), por exemplo, conexões criptografadas que não podem ser descriptografadas por terceiros.

Se um cliente se conectar na porta 8883 (MQTTS) ou 5671 (AMQPS) ao Hub de IoT Edge, um canal TLS deverá ser compilado. Durante o handshake de TLS, o Hub de IoT Edge envia sua cadeia de certificados que o cliente precisa para validar. Para validar a cadeia de certificados, o certificado raiz do hub de IoT Edge deve ser instalado como um certificado confiável no cliente. Se o certificado raiz não for confiável, a biblioteca de cliente será rejeitada pelo hub de IoT Edge com um erro de verificação de certificado.

As etapas a serem seguidas para instalar esse certificado raiz do agente em clientes de dispositivo são descritas no [gateway transparente](how-to-create-transparent-gateway.md) e na documentação [preparar um dispositivo downstream](how-to-connect-downstream-device.md#prepare-a-downstream-device) . Os módulos podem usar o mesmo certificado raiz que o Hub de IoT Edge aproveitando a API IoT Edge daemon.

#### <a name="authentication"></a>Autenticação

O Hub de IoT Edge só aceita conexões de dispositivos ou módulos que têm uma identidade de Hub IoT, por exemplo, que foram registradas no Hub IoT e têm um dos três métodos de autenticação de cliente com suporte do Hub IoT para prover sua identidade: [autenticação de chaves simétricas](how-to-authenticate-downstream-device.md#symmetric-key-authentication), [x. 509 autenticação autoassinada](how-to-authenticate-downstream-device.md#x509-self-signed-authentication), [autenticação assinada da AC x. 509](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).  Essas identidades do Hub IoT podem ser verificadas localmente pelo hub de IoT Edge para que as conexões ainda possam ser feitas offline.

Observações:

* Atualmente, os módulos IoT Edge dão suporte apenas à autenticação de chave simétrica.
* Os clientes MQTT com apenas nome de usuário local e senhas não são aceitos pelo agente de MQTT do hub de IoT Edge, eles devem usar identidades do Hub IoT.

#### <a name="authorization"></a>Autorização

Uma vez autenticado, o Hub de IoT Edge tem duas maneiras de autorizar conexões de cliente:

* Verificando se um cliente pertence a seu conjunto de clientes confiáveis definido no Hub IoT. O conjunto de clientes confiáveis é especificado pela configuração de relações pai/filho ou dispositivo/módulo no Hub IoT. Quando um módulo é criado no IoT Edge, uma relação de confiança é automaticamente estabelecida entre esse módulo e seu dispositivo de IoT Edge. Esse é o único modelo de autorização suportado pelo mecanismo de agente de roteamento.

* Configurando uma política de autorização. Esta política de autorização é um documento que lista todas as identidades de cliente autorizadas que podem acessar recursos no Hub de IoT Edge. Esse é o modelo de autorização principal usado pelo agente de MQTT do hub de IoT Edge, embora as relações pai/filho e dispositivo/módulo também possam ser compreendidas pelo agente do MQTT para tópicos do Hub IoT.

### <a name="remote-configuration"></a>Configuração remota

O Hub de IoT Edge é totalmente controlado pela nuvem. Ele obtém sua configuração do Hub IoT por meio de seu [módulo](iot-edge-modules.md#module-twins). Ele inclui:

* Configuração de rotas
* Políticas de autorização
* Configuração de ponte do MQTT

Além disso, várias configurações podem ser feitas Configurando [variáveis de ambiente no Hub de IOT Edge](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>Telemetria de qualidade de tempo de execução

IoT Edge coleta a telemetria anônima do tempo de execução do host e dos módulos do sistema para melhorar a qualidade do produto. Essas informações são chamadas de telemetria de qualidade de tempo de execução. A telemetria coletada é enviada periodicamente como mensagens do dispositivo para a nuvem para o Hub IoT do agente de IoT Edge. Essas mensagens não aparecem na telemetria regular do cliente e não consomem nenhuma cota de mensagem.

O agente de IoT Edge e o Hub geram métricas que você pode coletar para entender o desempenho do dispositivo. Um subconjunto dessas métricas é coletado pelo agente de IoT Edge como parte da telemetria de qualidade de tempo de execução. As métricas coletadas para telemetria de qualidade de tempo de execução são rotuladas com a marca `ms_telemetry` . Para obter informações sobre todas as métricas disponíveis, consulte [acessar métricas internas](how-to-access-built-in-metrics.md).

Todas as informações pessoais ou organizacionais, como nomes de dispositivos e módulos, são removidas antes do carregamento para garantir a natureza anônima da telemetria de qualidade de tempo de execução.

O agente de IoT Edge coleta a telemetria a cada hora e envia uma mensagem ao Hub IoT a cada 24 horas.

Se você quiser recusar o envio de telemetria de tempo de execução de seus dispositivos, há duas maneiras de fazer isso:

* Defina a `SendRuntimeQualityTelemetry` variável de ambiente para `false` para **edgeAgent** ou
* Desmarque a opção na portal do Azure durante a implantação.

## <a name="next-steps"></a>Próximas etapas

* [Entenda os módulos do Azure IoT Edge](iot-edge-modules.md)
* [Aprenda a implantar módulos e estabelecer rotas no IoT Edge](module-composition.md)
* [Saiba como publicar e assinar com IoT Edge](how-to-publish-subscribe.md)
* [Saiba mais sobre IoT Edge métricas de tempo de execução](how-to-access-built-in-metrics.md)
