---
title: Gateways para dispositivos downstream – Azure IoT Edge | Microsoft Docs
description: Use o Azure IoT Edge para criar um dispositivo de gateway transparente, opaco ou de proxy que envia dados de vários dispositivos downstream para a nuvem ou os processa localmente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 83e8089073f7e7e7634ddf00f7276e12aaf645b0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536431"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Como um dispositivo IoT Edge pode ser usado como um gateway

IoT Edge dispositivos podem operar como gateways, fornecendo uma conexão entre outros dispositivos na rede e no Hub IoT.

O módulo Hub de IoT Edge atua como o Hub IoT, portanto, pode manipular conexões de qualquer dispositivo que tenha uma identidade com o Hub IoT, incluindo outros dispositivos IoT Edge. Esse tipo de padrão de gateway é chamado *transparente* porque as mensagens podem passar de dispositivos downstream para o Hub IOT, como se não houvesse um gateway entre eles.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
A partir da versão 1,2 de IoT Edge, os gateways transparentes podem lidar com conexões downstream de outros dispositivos IoT Edge.
::: moniker-end

Para dispositivos que não podem se conectar ao Hub IoT por conta própria, IoT Edge gateways podem fornecer essa conexão. Esse tipo de padrão de gateway é chamado de *conversão* porque o dispositivo IOT Edge precisa executar o processamento em mensagens de dispositivo de downstream de entrada antes que elas possam ser encaminhadas ao Hub IOT. Esses cenários exigem módulos adicionais no gateway de IoT Edge para manipular as etapas de processamento.

Os padrões de gateway transparentes e de tradução não são mutuamente exclusivos. Um único dispositivo IoT Edge pode funcionar como um gateway transparente e um gateway de conversão.

Todos padrões de gateway fornecem os seguintes benefícios:

* **Análise na borda** – use os serviços de ia localmente para processar dados provenientes de dispositivos downstream sem enviar telemetria de fidelidade total à nuvem. Encontre e reaja a insights localmente e só envie um subconjunto de dados para o Hub IoT.
* **Isolamento de dispositivo downstream** – o dispositivo de gateway pode proteger todos os dispositivos downstream da exposição à internet. Ele pode se sentar entre uma rede de tecnologia operacional (OT) que não tem conectividade e uma rede de tecnologia de informação (TI) que fornece acesso à Web. Da mesma forma, os dispositivos que não têm a capacidade de se conectar ao Hub IoT por conta própria podem se conectar a um dispositivo de gateway em vez disso.
* **Multiplexação de Conexão** – todos os dispositivos conectados ao Hub IoT por meio de um uso de gateway do IoT Edge a mesma conexão subjacente.
* **Suavização de tráfego** -dispositivo do IoT Edge implementará automaticamente a retirada exponencial se o IoT Hub limitar o tráfego, enquanto as mensagens persistem localmente. Esse benefício torna sua solução resiliente a picos no tráfego.
* **Suporte offline** -o dispositivo de gateway armazena mensagens e atualizações de atualização de bits que não podem ser entregues ao Hub IOT.

## <a name="transparent-gateways"></a>Gateways transparentes

No padrão de gateway transparente, os dispositivos que, teoricamente, poderiam se conectar ao Hub IoT podem se conectar a um dispositivo de gateway. Os dispositivos downstream têm suas próprias identidades do Hub IoT e se conectam usando os protocolos MQTT ou AMQP. O gateway simplesmente passa as comunicações entre os dispositivos e o Hub IoT. Os dispositivos e os usuários que interagem com eles por meio do Hub IoT não sabem que um gateway está mediando suas comunicações. Essa falta de conscientização significa que o gateway é considerado *transparente*.

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

IoT Edge dispositivos não podem ser downstream de um gateway de IoT Edge.

![Diagrama-padrão de gateway transparente](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

A partir da versão 1.2.0, os dispositivos IoT Edge podem se conectar por meio de gateways transparentes.

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>Relações pai e filho

Declare as relações de gateway transparentes no Hub IoT definindo o gateway de IoT Edge como o *pai* de um *filho* de dispositivo downstream que se conecta a ele.

A relação pai/filho é estabelecida em três pontos na configuração do gateway:

#### <a name="cloud-identities"></a>Identidades de nuvem

Todos os dispositivos em um cenário de gateway transparente precisam de identidades de nuvem para que possam se autenticar no Hub IoT. Ao criar ou atualizar uma identidade de dispositivo, você pode definir os dispositivos pai ou filho do dispositivo. Essa configuração autoriza o dispositivo de gateway pai a lidar com a autenticação para seus dispositivos filho.

Os dispositivos filho só podem ter um pai. Cada pai pode ter até 100 filhos.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
IoT Edge dispositivos podem ser pais e filhos em relações de gateway transparentes. Uma hierarquia de vários dispositivos IoT Edge que se reportam entre si pode ser criada. O nó superior de uma hierarquia de gateway pode ter até cinco gerações de filhos. Por exemplo, um dispositivo IoT Edge pode ter cinco camadas de dispositivos IoT Edge vinculados como filhos abaixo dele. Mas o dispositivo IoT Edge na quinta geração não pode ter nenhum filho, IoT Edge ou de outra forma.
::: moniker-end

#### <a name="gateway-discovery"></a>Descoberta de gateway

Um dispositivo filho precisa ser capaz de encontrar seu dispositivo pai na rede local. Configure dispositivos de gateway com um nome de **host** , um FQDN (nomes de domínio totalmente qualificado) ou um endereço IP, que seus dispositivos filho usarão para localizá-lo.

Em dispositivos IoT downstream, use o parâmetro **gatewayHostname** na cadeia de conexão para apontar para o dispositivo pai.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Em dispositivos IoT Edge downstream, use o parâmetro **parent_hostname** no arquivo config. YAML para apontar para o dispositivo pai.
::: moniker-end

#### <a name="secure-connection"></a>Conexão segura

Os dispositivos pai e filho também precisam autenticar suas conexões entre si. Cada dispositivo precisa de uma cópia de um certificado de autoridade de certificação raiz compartilhada que os dispositivos filho usam para verificar se estão se conectando ao gateway adequado.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Quando vários gateways de IoT Edge se conectam entre si em uma hierarquia de gateway, todos os dispositivos na hierarquia devem usar uma única cadeia de certificados.
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>Recursos do dispositivo por trás de gateways transparentes

Todos os primitivos do Hub IoT que funcionam com o pipeline de mensagens do IoT Edge também oferecem suporte a cenários de gateway transparentes. Cada gateway de IoT Edge tem recursos de armazenamento e encaminhamento para mensagens que chegam por ele.

Use a tabela a seguir para ver como recursos diferentes do Hub IoT têm suporte para dispositivos em comparação com dispositivos por trás de gateways.

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

| Funcionalidade | Dispositivo IoT | IoT por trás de um gateway |
| ---------- | ---------- | -------------------- |
| [Mensagens do dispositivo para a nuvem (D2C)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Sim – IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-D2C IoT filho](./media/iot-edge-as-gateway/check-yes.png) |
| [Mensagens de nuvem para dispositivo (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Sim – IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-C2D filho de IoT](./media/iot-edge-as-gateway/check-yes.png) |
| [Métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Sim – método direto de IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-método de IoT direto filho](./media/iot-edge-as-gateway/check-yes.png) |
| [Dispositivo gêmeos](../iot-hub/iot-hub-devguide-device-twins.md) e [módulo gêmeos](../iot-hub/iot-hub-devguide-module-twins.md) | ![Sim – IoT gêmeos](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-gêmeos IoT filho](./media/iot-edge-as-gateway/check-yes.png) |
| [Carregamento de arquivos](../iot-hub/iot-hub-devguide-file-upload.md) | ![Sim-carregamento de arquivo de IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Sem-carregamento de arquivo filho de IoT](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| Funcionalidade | Dispositivo IoT | IoT por trás de um gateway | Dispositivo do IoT Edge | IoT Edge por trás de um gateway |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [Mensagens do dispositivo para a nuvem (D2C)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Sim – IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-D2C IoT filho](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-filho IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Mensagens de nuvem para dispositivo (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Sim – IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-C2D filho de IoT](./media/iot-edge-as-gateway/check-yes.png) | ![C2D não-IoT Edge](./media/iot-edge-as-gateway/crossout-no.png) | ![C2D filho sem IoT Edge](./media/iot-edge-as-gateway/crossout-no.png) |
| [Métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Sim – método direto de IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-método de IoT direto filho](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-IoT Edge método direto](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-filho IoT Edge método direto](./media/iot-edge-as-gateway/check-yes.png) |
| [Dispositivo gêmeos](../iot-hub/iot-hub-devguide-device-twins.md) e [módulo gêmeos](../iot-hub/iot-hub-devguide-module-twins.md) | ![Sim – IoT gêmeos](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-gêmeos IoT filho](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-IoT Edge gêmeos](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-filho IoT Edge gêmeos](./media/iot-edge-as-gateway/check-yes.png) |
| [Carregamento de arquivos](../iot-hub/iot-hub-devguide-file-upload.md) | ![Sim-carregamento de arquivo de IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Sem-carregamento de arquivo filho de IoT](./media/iot-edge-as-gateway/crossout-no.png) | ![Carregamento de arquivo sem IoT Edge](./media/iot-edge-as-gateway/crossout-no.png) | ![Não IoT Edge carregamento de arquivo filho](./media/iot-edge-as-gateway/crossout-no.png) |
| Pulls da imagem de contêiner |   |   | ![Sim-IoT Edge pull de contêiner](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-efetuar pull de contêiner de IoT Edge filho](./media/iot-edge-as-gateway/check-yes.png) |
| Upload de BLOB |   |   | ![Sim-upload de blob IoT Edge](./media/iot-edge-as-gateway/check-yes.png) | ![Sim-carregamento de blob de IoT Edge filho](./media/iot-edge-as-gateway/check-yes.png) |

As **imagens de contêiner** podem ser baixadas, armazenadas e entregues de dispositivos pai a dispositivos filho.

Os **BLOBs** , incluindo pacotes de suporte e logs, podem ser carregados de dispositivos filho para dispositivos pai.

::: moniker-end

## <a name="translation-gateways"></a>Gateways de tradução

Se os dispositivos downstream não puderem se conectar ao Hub IoT, o gateway de IoT Edge precisará atuar como um tradutor. Geralmente, esse padrão é necessário para dispositivos que não dão suporte a MQTT, AMQP ou HTTP. Como esses dispositivos não podem se conectar ao Hub IoT, eles também não podem se conectar ao módulo Hub IoT Edge sem algum pré-processamento.

Os módulos personalizados ou de terceiros que geralmente são específicos para o hardware ou protocolo do dispositivo downstream precisam ser implantados no gateway de IoT Edge. Esses módulos de conversão pegam as mensagens de entrada e as transformam em um formato que pode ser compreendido pelo Hub IoT.

Há dois padrões para os gateways de Tradução: *conversão de protocolo* e conversão de *identidade*.

![Diagrama-padrões de gateway de tradução](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>Conversão de protocolo

No padrão de gateway de conversão de protocolo, somente o gateway IoT Edge tem uma identidade com o Hub IoT. O módulo de conversão recebe mensagens de dispositivos downstream, converte-os em um protocolo com suporte e, em seguida, o dispositivo de IoT Edge envia as mensagens em nome dos dispositivos downstream. Todas as informações parecem estar vindo de um dispositivo, o gateway. Dispositivos downstream deverão inserir informações de identificação adicionais em suas mensagens se os aplicativos de nuvem quiserem analisar os dados por dispositivo. Além disso, os primitivos do Hub IoT como gêmeos e métodos diretos só têm suporte para o dispositivo de gateway, não para dispositivos downstream. Os gateways nesse padrão são considerados *opacos* em comparação com os gateways transparentes, pois eles ocultam as identidades dos dispositivos downstream.

A conversão de protocolo dá suporte a dispositivos que são restritos por recursos. Muitos dispositivos existentes estão produzindo dados que podem fomentar insights de negócios, porém, não foram projetados com a conectividade de nuvem em mente. Os gateways opacos permitem que esses dados sejam desbloqueados e usados em uma solução de IoT.

### <a name="identity-translation"></a>Conversão de identidade

O padrão de gateway de conversão de identidade se baseia na conversão de protocolo, mas o gateway de IoT Edge também fornece uma identidade de dispositivo do Hub IoT em nome dos dispositivos downstream. O módulo de conversão é responsável por entender o protocolo usado pelos dispositivos downstream, fornecer a eles a identidade e converter suas mensagens em primitivos do Hub IoT. Dispositivos downstream aparecem no Hub IoT como dispositivos de primeira classe com gêmeos e métodos. Um usuário pode interagir com os dispositivos no Hub IoT e não estar ciente do dispositivo de gateway intermediário.

A conversão de identidade fornece os benefícios da conversão de protocolo e, além disso, permite a capacidade de gerenciamento total de dispositivos downstream da nuvem. Todos os dispositivos da sua solução IoT são exibidos no Hub IoT, independentemente do protocolo usado.

### <a name="device-capabilities-behind-translation-gateways"></a>Recursos do dispositivo por trás de gateways de tradução

A tabela a seguir explica como os recursos do Hub IoT são estendidos para dispositivos downstream em ambos os padrões de gateway de tradução.

| Funcionalidade | Conversão de protocolo | Conversão de identidade |
| ---------- | -------------------- | -------------------- |
| Identidades armazenadas no registro de identidade do Hub IoT | Somente a identidade do dispositivo de gateway | Identidades de todos os dispositivos conectados |
| Dispositivo gêmeo | Somente o gateway tem um dispositivo e módulo gêmeos | Cada dispositivo conectado tem seu próprio dispositivo gêmeo |
| Métodos diretos e mensagens da nuvem para o dispositivo | A nuvem pode lidar somente com o dispositivo de gateway | A nuvem pode lidar com cada dispositivo conectado individualmente |
| [Cotas e limitações do Hub IoT](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Aplica-se ao dispositivo de gateway | Aplica-se a cada dispositivo |

Ao usar o padrão de conversão de protocolo, todos os dispositivos que se conectam por meio desse gateway compartilham a mesma fila da nuvem para o dispositivo, que pode conter no máximo 50 mensagens. Use esse padrão somente quando alguns dispositivos estiverem se conectando por meio de cada gateway de campo e seu tráfego de nuvem para dispositivo estiver baixo.

O tempo de execução de IoT Edge não inclui recursos de conversão de protocolo ou identidade. Esses padrões exigem módulos personalizados ou de terceiros que geralmente são específicos para o hardware e o protocolo usados. O [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) contém vários módulos de conversão de protocolo para escolher. Para obter um exemplo que usa o padrão de conversão de identidade, consulte [Azure IOT Edge Kit do iniciante do LoRaWAN](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="next-steps"></a>Próximas etapas

Conheça as três etapas para configurar um gateway transparente:

* [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo downstream no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Conectar um dispositivo downstream para um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)
