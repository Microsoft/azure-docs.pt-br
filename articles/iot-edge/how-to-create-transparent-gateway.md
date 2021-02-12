---
title: Criar um dispositivo de gateway transparente - Azure IoT Edge | Microsoft Docs
description: Usar um dispositivo Azure IoT Edge como gateway transparente que possa processar informações de dispositivos downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 9f81d059c1a71bf6349d0ef9b4aae8f7a47c161f
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938776"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurar um dispositivo IoT Edge para agir como gateway transparente

Este artigo fornece instruções detalhadas para configurar um IoT Edge dispositivo para funcionar como um gateway transparente para outros dispositivos se comunicarem com o Hub IoT. Este artigo usa o termo *IOT Edge gateway* para se referir a um dispositivo de IOT Edge configurado como um gateway transparente. Para obter mais informações, consulte [como um dispositivo de IOT Edge pode ser usado como um gateway](./iot-edge-as-gateway.md).

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

>[!NOTE]
>No momento:
>
> * Os dispositivos habilitados para o Edge não podem se conectar com gateways do IoT Edge.
> * Dispositivos downstream não podem usar o upload de arquivo.

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>No momento:
>
> * Dispositivos downstream não podem usar o upload de arquivo.

::: moniker-end

Há três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo aborda a primeira etapa:

1. **Configure o dispositivo de gateway como um servidor para que os dispositivos downstream possam se conectar com segurança. Configure o gateway para receber mensagens de dispositivos downstream e roteá-los para o destino apropriado.**
2. Crie uma identidade de dispositivo para o dispositivo downstream para que ele possa autenticar com o Hub IoT. Configure o dispositivo downstream para enviar mensagens por meio do dispositivo de gateway. Para essas etapas, consulte [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).
3. Conecte o dispositivo downstream ao dispositivo de gateway e comece a enviar mensagens. Para essas etapas, consulte [conectar um dispositivo downstream a um gateway de Azure IOT Edge](how-to-connect-downstream-device.md).

Para que um dispositivo atue como um gateway, ele precisa se conectar com segurança a seus dispositivos downstream. Azure IoT Edge permite que você use a infraestrutura de chave pública (PKI) para configurar conexões seguras entre dispositivos. Nesse caso, estamos permitindo que um dispositivo downstream se conecte a um dispositivo IoT Edge atuando como um gateway transparente. Para manter a segurança razoável, o dispositivo downstream deve confirmar a identidade do dispositivo de gateway. Essa verificação de identidade impede que os dispositivos se conectem a gateways potencialmente mal-intencionados.

Um dispositivo downstream pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço de nuvem do [Hub IoT do Azure](../iot-hub/index.yml). Esses aplicativos geralmente usam o [SDK do dispositivo IOT do Azure](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo downstream poderia até ser um aplicativo em execução no próprio dispositivo de gateway de IoT Edge. No entanto, um dispositivo de IoT Edge não pode ser downstream de um gateway de IoT Edge.

Você pode criar qualquer infraestrutura de certificado que permite a relação de confiança necessária para sua topologia de dispositivo/gateway. Neste artigo, assumimos a mesma configuração de certificado que você usaria para habilitar a [segurança de autoridade de certificação x. 509](../iot-hub/iot-hub-x509ca-overview.md) no Hub IOT, que envolve um certificado de autoridade de certificação x. 509 associado a um hub IOT específico (a AC raiz do Hub IOT), uma série de certificados assinados com essa AC e uma AC para o dispositivo IOT Edge.

>[!NOTE]
>O termo *certificado de autoridade de certificação raiz* usado em todos esses artigos refere-se ao certificado público mais alto da autoridade da cadeia de certificados PKI e não necessariamente à raiz do certificado de uma autoridade de certificação agregada. Em muitos casos, é, na verdade, um certificado público de CA intermediário.

As etapas a seguir orientam você pelo processo de criação dos certificados e sua instalação nos locais certos no gateway. Você pode usar qualquer computador para gerar os certificados e, em seguida, copiá-los para seu dispositivo IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Linux ou Windows com o IoT Edge instalado.

Se você não tiver um dispositivo pronto, poderá criar um em uma máquina virtual do Azure. Siga as etapas em [implantar seu primeiro módulo IOT Edge em um dispositivo virtual Linux](quickstart-linux.md) para criar um hub IOT, criar uma máquina virtual e configurar o IOT Edge Runtime. 

## <a name="set-up-the-device-ca-certificate"></a>Configurar o certificado de autoridade de certificação do dispositivo

Todos os gateways de IoT Edge precisam de um certificado de autoridade de certificação de dispositivo instalado neles. O daemon de segurança IoT Edge usa o certificado de AC de dispositivo IoT Edge para assinar um certificado de AC de carga de trabalho que, por sua vez, assina um certificado de servidor para o Hub de IoT Edge. O gateway apresenta seu certificado de servidor para o dispositivo downstream durante o início da conexão. O dispositivo downstream verifica se o certificado do servidor faz parte de uma cadeia de certificados que se acumula no certificado de autoridade de certificação raiz. Esse processo permite que o dispositivo downstream confirme se o gateway vem de uma fonte confiável. Para obter mais informações, consulte [entender como o Azure IOT Edge usa certificados](iot-edge-certs.md).

![Configuração do certificado de gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

O certificado de autoridade de certificação raiz e o certificado de autoridade de certificação do dispositivo (com sua chave privada) precisam estar presentes no dispositivo IoT Edge gateway e configurados no arquivo IoT Edge config. YAML. Lembre-se de que, nesse caso, o *certificado de AC raiz* significa a autoridade de certificação mais alta para esse cenário de IOT Edge. O certificado de CA do dispositivo de gateway e os certificados de dispositivo downstream precisam ser acumulados para o mesmo certificado de autoridade de certificação raiz.

>[!TIP]
>O processo de instalação do certificado de autoridade de certificação raiz e do certificado de AC do dispositivo em um IoT Edge dispositivo também é explicado em mais detalhes em [gerenciar certificados em um dispositivo IOT Edge](how-to-manage-device-certificates.md).

Prepare os seguintes arquivos:

* Certificado de AC raiz
* Certificado de autoridade de certificação de dispositivo
* Chave privada da AC do dispositivo

Para cenários de produção, você deve gerar esses arquivos com sua própria autoridade de certificação. Para cenários de desenvolvimento e teste, você pode usar certificados de demonstração.

1. Se você estiver usando certificados de demonstração, use as instruções em [criar certificados de demonstração para testar IOT Edge recursos de dispositivo](how-to-create-test-certificates.md) para criar seus arquivos. Nessa página, você precisa executar as seguintes etapas:

   1. Para começar, configure os scripts para gerar certificados em seu dispositivo.
   2. Crie um certificado de autoridade de certificação raiz. No final dessas instruções, você terá um arquivo de certificado de autoridade de certificação raiz:
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.
   3. Criar IoT Edge certificados de AC do dispositivo. No final dessas instruções, você terá um certificado de autoridade de certificação do dispositivo e sua chave privada:
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem` e
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

2. Se você criou os certificados em um computador diferente, copie-os para o dispositivo IoT Edge.

3. No dispositivo IoT Edge, abra o arquivo de configuração do daemon de segurança.
   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

4. Localize a seção de **configurações de certificado** do arquivo. Remova a marca de comentário das quatro linhas começando com os **certificados:** e forneça os URIs de arquivo para os três arquivos como valores para as seguintes propriedades:
   * **device_ca_cert**: certificado de autoridade de certificação do dispositivo
   * **device_ca_pk**: chave privada da AC do dispositivo
   * **trusted_ca_certs**: certificado de autoridade de certificação raiz

   Verifique se não há nenhum espaço em branco anterior na linha **certificados:** e se as outras linhas são recuadas em dois espaços.

5. Salve e feche o arquivo.

6. Reinicie IoT Edge.
   * Windows: `Restart-Service iotedge`
   * Linux: `sudo systemctl restart iotedge`

## <a name="deploy-edgehub-and-route-messages"></a>Implantar edgeHub e rotear mensagens

Os dispositivos downstream enviam telemetria e mensagens para o dispositivo de gateway, em que o módulo de Hub IoT Edge é responsável por rotear as informações para outros módulos ou para o Hub IoT. Para preparar o dispositivo de gateway para essa função, verifique se:

* O módulo Hub de IoT Edge é implantado no dispositivo.

  Quando você instala o IoT Edge pela primeira vez em um dispositivo, somente um módulo do sistema é iniciado automaticamente: o agente do IoT Edge. Depois de criar a primeira implantação para um dispositivo, o segundo módulo do sistema, o Hub de IoT Edge, também será iniciado. Se o módulo **edgeHub** não estiver em execução no seu dispositivo, crie uma implantação para seu dispositivo.

* O módulo Hub IoT Edge tem rotas configuradas para lidar com mensagens de entrada de dispositivos downstream.

  O dispositivo de gateway deve ter uma rota em vigor para tratar mensagens de dispositivos downstream ou então essas mensagens não serão processadas. Você pode enviar as mensagens para os módulos no dispositivo de gateway ou diretamente para o Hub IoT.

Para implantar o módulo Hub de IoT Edge e configurá-lo com rotas para lidar com mensagens de entrada de dispositivos downstream, siga estas etapas:

1. No portal do Azure, navegue para o hub IoT.

2. Vá para **IoT Edge** e selecione o dispositivo Edge IoT que você deseja usar como um gateway.

3. Selecione **definir módulos**.

4. Na página **módulos** , você pode adicionar todos os módulos que deseja implantar no dispositivo de gateway. Para os fins deste artigo, estamos concentrados na configuração e implantação do módulo edgeHub, que não precisa ser definido explicitamente nesta página.

5. Selecione **Avançar: rotas**.

6. Na página **rotas** , verifique se há uma rota para tratar as mensagens provenientes de dispositivos downstream. Por exemplo:

   * Uma rota que envia todas as mensagens, seja de um módulo ou de um dispositivo downstream, para o Hub IoT:
       * **Nome**: `allMessagesToHub`
       * **Valor**: `FROM /messages/* INTO $upstream`

   * Uma rota que envia todas as mensagens de todos os dispositivos downstream para o Hub IoT:
      * **Nome**: `allDownstreamToHub`
      * **Valor**: `FROM /messages/* WHERE NOT IS_DEFINED ($connectionModuleId) INTO $upstream`

      Essa rota funciona porque, ao contrário das mensagens de módulos IoT Edge, as mensagens de dispositivos downstream não têm uma ID de módulo associada a elas. O uso da cláusula **Where** da rota nos permite filtrar todas as mensagens com essa propriedade do sistema.

      Para saber mais sobre o roteamento de mensagens, consulte [Implantar módulos e estabelecer rotas](./module-composition.md#declare-routes).

7. Depois que a rota ou as rotas forem criadas, selecione **revisar + criar**.

8. Na página **revisar + criar** , selecione **criar**.

## <a name="open-ports-on-gateway-device"></a>Abrir portas no dispositivo de gateway

Os dispositivos IoT Edge padrão não precisam de nenhuma conectividade de entrada para funcionar, pois toda a comunicação com o Hub IoT é feita por meio de conexões de saída. Os dispositivos de gateway são diferentes porque precisam receber mensagens de seus dispositivos downstream. Se um firewall estiver entre os dispositivos downstream e o dispositivo de gateway, a comunicação também precisará ser possível por meio do firewall.

Para que um cenário de gateway funcione, pelo menos um dos protocolos com suporte do hub de IoT Edge deve estar aberto para o tráfego de entrada de dispositivos downstream. Os protocolos com suporte são MQTT, AMQP, HTTPS, MQTT sobre WebSockets e AMQP sobre WebSockets.

| Porta | Protocolo |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge configurado como um gateway transparente, você precisa configurar seus dispositivos downstream para confiar no gateway e enviar mensagens para ele. Continue em para [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md) para as próximas etapas em Configurando seu cenário de gateway transparente.