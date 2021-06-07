---
title: Conectar um IoT Edge gateway transparente a um aplicativo de IoT Central do Azure
description: Como conectar dispositivos por meio de um IoT Edge gateway transparente para um aplicativo IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 4e88ad58c7baba1c66c30df3f4effdbf11371c18
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045324"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>Como conectar dispositivos por meio de um gateway IoT Edge transparente

Um dispositivo IoT Edge pode atuar como um gateway que fornece uma conexão entre outros dispositivos em uma rede local e seu aplicativo IoT Central. Você usa um gateway quando o dispositivo não pode acessar seu aplicativo IoT Central diretamente.

O IoT Edge dá suporte aos [padrões de gateway *transparentes* e de *tradução*](../../iot-edge/iot-edge-as-gateway.md). Este artigo resume como implementar o padrão de gateway transparente. Nesse padrão, o gateway passa as mensagens do dispositivo downstream para o ponto de extremidade do Hub IoT em seu aplicativo IoT Central.

Este artigo usa máquinas virtuais para hospedar o dispositivo e o gateway downstream. Em um cenário real, o dispositivo e o gateway downstream seriam executados em dispositivos físicos em sua rede local.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste tutorial, você precisa de uma assinatura ativa do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Conclua o início rápido [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md) para criar um aplicativo do IoT Central usando o modelo de **App personalizado > Aplicativo personalizado**.

Para seguir as etapas neste artigo, baixe os seguintes arquivos em seu computador:

- [Modelo de dispositivo termostato](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [Manifesto de gateway transparente](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>Adicionar modelos de dispositivo

Os dispositivos downstream e o dispositivo de gateway exigem modelos de dispositivo no IoT Central. IoT Central permite modelar a relação entre os dispositivos downstream e o gateway para que você possa exibi-los e gerenciá-los depois que eles estiverem conectados.

Para criar um modelo de dispositivo para um dispositivo downstream, crie um modelo de dispositivo padrão que modela os recursos do seu dispositivo. O exemplo mostrado neste artigo usa o modelo de dispositivo termostato.

Para criar um modelo de dispositivo para um dispositivo downstream:

1. Crie um modelo de dispositivo e escolha **dispositivo IOT** como o tipo de modelo.

1. Na página **Personalizar** do assistente, insira um nome como *termostato* para o modelo de dispositivo.

1. Depois de criar o modelo de dispositivo, selecione **importar um modelo**. Selecione um modelo como o *thermostat-1.jsno* arquivo baixado anteriormente.

1. Para gerar algumas exibições padrão para o termostato, selecione exibições e, em seguida, escolha **gerar exibições padrão**.

1. Publicar o modelo de dispositivo.

Para criar um modelo de dispositivo para um gateway de IoT Edge transparente:

1. Crie um modelo de dispositivo e escolha **Azure IOT Edge** como o tipo de modelo.

1. Na página **Personalizar** do assistente, insira um nome como o gateway de *borda* para o modelo de dispositivo.

1. Na página **Personalizar** do assistente, verifique o **dispositivo de gateway com dispositivos downstream**.

1. Na página **Personalizar** do assistente, selecione **procurar**. Carregue o *EdgeTransparentGatewayManifest.jsno* arquivo baixado anteriormente.

1. Adicione uma entrada em **relações** ao modelo de dispositivo downstream.

A captura de tela a seguir mostra a página **relações** para um dispositivo IOT Edge gateway que tem dispositivos downstream que usam o modelo de dispositivo **termostato** :

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="Captura de tela mostrando IoT Edge relação de modelo de dispositivo de gateway com um modelo de dispositivo downstream de termostato.":::

A captura de tela anterior mostra um modelo de dispositivo IoT Edge gateway sem módulos definidos. Um gateway transparente não requer nenhum módulo porque o tempo de execução de IoT Edge encaminha mensagens dos dispositivos downstream para IoT Central. Se o próprio gateway precisar enviar telemetria, sincronizar Propriedades ou manipular comandos, você poderá definir esses recursos no componente padrão ou em um módulo.

Adicione todas as propriedades de nuvem e exibições necessárias antes de publicar os modelos de dispositivo do gateway e downstream.

## <a name="add-the-devices"></a>Adicionar os dispositivos

Ao adicionar os dispositivos ao seu aplicativo IoT Central, você pode definir a relação entre os dispositivos downstream e o gateway transparente.

Para adicionar os dispositivos:

1. Navegue até a página dispositivos no seu aplicativo IoT Central.

1. Adicione uma instância do dispositivo de IoT Edge do gateway transparente. Neste artigo, a ID do dispositivo de gateway é `edgegateway` .

1. Adicione uma ou mais instâncias do dispositivo downstream. Neste artigo, os dispositivos downstream são termostatos conectados com IDs `thermostat1` e `thermostat2` .

1. Na lista de dispositivos, selecione cada dispositivo downstream e selecione **anexar ao gateway**.

A captura de tela a seguir mostra que você pode exibir a lista de dispositivos conectados a um gateway na página **dispositivos downstream** :

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="Captura de tela que mostra a lista de dispositivos downstream conectados a um gateway transparente.":::

Em um gateway transparente, os dispositivos downstream se conectam ao próprio gateway, não a um módulo personalizado hospedado pelo Gateway.

Antes de implantar os dispositivos, você precisa do:

- **Escopo de ID** do seu aplicativo IOT central.
- Valores de **ID do dispositivo** para o gateway e dispositivos downstream.
- Valores de **chave primária** para o gateway e dispositivos downstream.

Para localizar esses valores, navegue até cada dispositivo na lista de dispositivos e selecione **conectar**. Anote esses valores antes de continuar.

## <a name="deploy-the-gateway-and-devices"></a>Implantar o gateway e os dispositivos

Para permitir que você experimente esse cenário, as etapas a seguir mostram como implantar o gateway e os dispositivos downstream em máquinas virtuais do Azure. Em um cenário real, o dispositivo downstream e o gateway são executados em dispositivos físicos em sua rede local.

Para experimentar o cenário de gateway transparente, selecione o botão a seguir para implantar duas máquinas virtuais do Linux. Uma máquina virtual é um gateway de IoT Edge transparente, a outra é um dispositivo downstream que simula um termostato:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Quando as duas máquinas virtuais forem implantadas e em execução, verifique se o dispositivo IoT Edge Gateway está em execução na `edgegateway` máquina virtual:

1. Vá para a página **dispositivos** no seu aplicativo IOT central. Se o dispositivo de gateway de IoT Edge estiver conectado à IoT Central, seu status será **provisionado**.

1. Abra o dispositivo IoT Edge gateway e verifique o status dos módulos na página **módulos** . Se o tempo de execução do IoT Edge for iniciado com êxito, o status dos módulos **$edgeAgent** e **$edgeHub** será **executado**:

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="Captura de tela mostrando os módulos $edgeAgent e $edgeHub em execução no gateway de IoT Edge.":::

> [!TIP]
> Talvez seja necessário aguardar vários minutos enquanto a máquina virtual é iniciada e o dispositivo é provisionado no aplicativo IoT Central.

## <a name="configure-the-gateway"></a>Configurar o gateway

Para que o dispositivo IoT Edge funcione como um gateway transparente, ele precisa de alguns certificados para provar sua identidade para todos os dispositivos downstream. Este artigo usa certificados de demonstração. Em um ambiente de produção, use certificados de sua autoridade de certificação.

Para gerar os certificados de demonstração e instalá-los em seu dispositivo de gateway:

1. Use o SSH para se conectar e entrar em sua máquina virtual do dispositivo de gateway.

1. Execute os seguintes comandos para clonar o repositório IoT Edge e gerar seus certificados de demonstração:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Depois de executar os comandos anteriores, os seguintes arquivos estarão prontos para uso nas próximas etapas:

    - *~/certs/certs/Azure-IOT-Test-only.root.ca.cert.pem* -o certificado de autoridade de certificação raiz usado para fazer todos os outros certificados de demonstração para testar um cenário de IOT Edge.
    - *~/certs/certs/IOT-Edge-Device-mycacert-Full-Chain.cert.pem* -um certificado de autoridade de certificação do dispositivo que é referenciado no arquivo *config. YAML* . Em um cenário de gateway, esse certificado de autoridade de certificação é como o dispositivo de IoT Edge verifica sua identidade para dispositivos downstream.
    - *~/certs/Private/IOT-Edge-Device-mycacert.Key.pem* -a chave privada associada ao certificado de autoridade de certificação do dispositivo.

    Para saber mais sobre esses certificados de demonstração, confira [criar certificados de demonstração para testar IOT Edge recursos do dispositivo](../../iot-edge/how-to-create-test-certificates.md).

1. Abra o arquivo *config. YAML* em um editor de texto. Por exemplo:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Localize as `Certificate settings` configurações. Remova a marca de comentário e modifique as configurações do certificado da seguinte maneira:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    O exemplo mostrado acima pressupõe que você está conectado como **AzureUser** e criou um certificado de AC de dispositivo chamado "mycacert".

1. Salve as alterações e reinicie o tempo de execução de IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

Se o tempo de execução do IoT Edge for iniciado com êxito após as alterações, o status dos módulos **$edgeAgent** e **$edgeHub** será alterado para **sendo executado** na página **módulos** do seu dispositivo de gateway no IOT central.

Se o tempo de execução não for iniciado, verifique as alterações feitas em *config. YAML* e veja [solucionar problemas de seu dispositivo IOT Edge](../../iot-edge/troubleshoot.md).

Seu gateway transparente agora está configurado e pronto para começar a encaminhar a telemetria de dispositivos downstream.

## <a name="provision-a-downstream-device"></a>Provisionar um dispositivo downstream

Atualmente, IoT Edge não pode provisionar automaticamente um dispositivo downstream para seu aplicativo IoT Central. As etapas a seguir mostram como provisionar o `thermostat1` dispositivo. Para concluir essas etapas, você precisa de um ambiente com Python 3,6 (ou superior) instalado e conectividade com a Internet. O [Azure cloud Shell](https://shell.azure.com/) tem Python 3,7 pré-instalado:

1. Execute o seguinte comando para instalar o `azure.iot.device` módulo:

    ```bash
    pip install azure.iot.device
    ```

1. Execute o seguinte comando para baixar o script Python que faz o provisionamento:

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. Para provisionar o `thermostat1` dispositivo downstream em seu aplicativo IOT central, execute os seguintes comandos, substituindo `{your application id scope}` e `{your device primary key}`  :

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

No aplicativo IoT Central, verifique se o **status do dispositivo** do dispositivo thermostat1 agora está **provisionado**. 

## <a name="configure-a-downstream-device"></a>Configurar um dispositivo downstream

Na seção anterior, você configurou a `edgegateway` máquina virtual com os certificados de demonstração para habilitá-lo para execução como gateway. A `leafdevice` máquina virtual está pronta para que você instale um simulador do termostato que usa o gateway para se conectar ao IOT central.

A `leafdevice` máquina virtual precisa de uma cópia do certificado de autoridade de certificação raiz que você criou na `edgegateway` máquina virtual. Copie o arquivo */Home/AzureUser/certs/certs/Azure-IOT-Test-only.root.ca.cert.pem* da `edgegateway` máquina virtual para seu diretório base na `leafdevice` máquina virtual. Você pode usar o comando **SCP** para copiar arquivos de e para uma máquina virtual do Linux.

Para saber como verificar a conexão do dispositivo downstream para o gateway, consulte [testar a conexão do gateway](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection).

Para executar o simulador do termostato na `leafdevice` máquina virtual:

1. Baixe o exemplo de Python em seu diretório base:

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Instalar o módulo python do dispositivo IoT do Azure:

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. Defina as variáveis de ambiente para configurar o exemplo. Substitua `{your device shared key}` pela chave primária do `thermostat1` que você anotou anteriormente. Essas variáveis assumem que o nome da máquina virtual do gateway é `edgegateway` e a ID do dispositivo termostato é `thermostat1` :

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    Observe como a cadeia de conexão usa o nome do dispositivo de gateway e não o nome de um hub IoT.

1. Para executar o código, use o seguinte comando:

    ```bash
    python3 simple_thermostat.py
    ```

    A saída desse comando é semelhante a:

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. Para ver a telemetria em IoT Central, navegue até a página **visão geral** do dispositivo **thermostat1** :

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="Captura de tela mostrando a telemetria do dispositivo downstream.":::

    Na página **sobre** , você pode exibir valores de propriedade enviados do dispositivo downstream e, na página de **comando** , você pode chamar comandos no dispositivo downstream.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a configurar um gateway transparente com o IoT Central, a próxima etapa sugerida é saber mais sobre [IOT Edge](../../iot-edge/about-iot-edge.md).
