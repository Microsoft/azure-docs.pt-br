---
title: Executar o Azure IoT Edge em máquinas virtuais do Ubuntu | Microsoft Docs
description: Azure IoT Edge instruções de instalação para máquinas virtuais Ubuntu 18, 4 LTS
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: pdecarlo
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e044e8102308fce4145d4aa6c887cefaa99be34
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629955"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Executar Azure IoT Edge em Máquinas Virtuais do Ubuntu

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O runtime pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o runtime do IoT Edge, você pode começar a implantar a lógica de negócios nele da nuvem.

Para saber mais sobre como funciona o runtime do IoT Edge e quais componentes estão incluídos, veja [Entender o runtime do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para implantar uma máquina virtual Ubuntu 18, 4 LTS com o tempo de execução Azure IoT Edge instalado e configurado usando uma cadeia de conexão de dispositivo fornecida previamente. A implantação é realizada usando um [modelo de Azure Resource Manager](../azure-resource-manager/templates/overview.md) baseado em [Cloud-init](../virtual-machines/linux/using-cloud-init.md
) mantido no repositório de projeto [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) .

Na primeira inicialização, a máquina virtual Ubuntu 18, 4 LTS [instala a versão mais recente do tempo de execução de Azure IOT Edge por meio de Cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Ele também define uma cadeia de conexão fornecida antes do tempo de execução iniciar, permitindo que você configure e conecte facilmente o dispositivo IoT Edge sem a necessidade de iniciar uma sessão de área de trabalho SSH ou remota. 

## <a name="deploy-using-deploy-to-azure-button"></a>Botão implantar usando implantar no Azure

O [botão implantar no Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) permite a implantação simplificada de [modelos de Azure Resource Manager](../azure-resource-manager/templates/overview.md) mantidos no github.  Esta seção demonstrará o uso do botão implantar no Azure contido no repositório de projeto [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) .  


1. Implantaremos um Azure IoT Edge VM Linux habilitada usando o modelo iotedge-VM-Deploy Azure Resource Manager.  Para começar, clique no botão abaixo:

    [![Botão Implantar no Azure para iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Na janela iniciada recentemente, preencha os campos de formulário disponíveis:

    > [!div class="mx-imgBorder"]
    > [![Captura de tela mostrando o modelo iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Assinatura**: a assinatura ativa do Azure na qual a máquina virtual será implantada.

    **Grupo de recursos**: um grupo de recursos existente ou criado recentemente para conter a máquina virtual e seus recursos associados.

    **Prefixo do rótulo DNS**: um valor necessário de sua escolha que é usado para prefixar o nome do host da máquina virtual.

    **Nome de usuário do administrador**: um nome de usuário, que será fornecido com privilégios de raiz na implantação.

    **Cadeia de conexão do dispositivo**: uma [cadeia de conexão de dispositivo](./how-to-register-device.md) para um dispositivo que foi criado no [Hub IOT](../iot-hub/about-iot-hub.md)pretendido.

    **Tamanho da VM**: o [tamanho](../cloud-services/cloud-services-sizes-specs.md) da máquina virtual a ser implantada

    **Versão do sistema operacional Ubuntu**: a versão do sistema operacional Ubuntu a ser instalada na máquina virtual base.

    **Local**: a [região geográfica](https://azure.microsoft.com/global-infrastructure/locations/) na qual implantar a máquina virtual, esse valor assume como padrão o local do grupo de recursos selecionado.

    **Tipo de autenticação**: escolha **sshPublicKey** ou **senha** dependendo de sua preferência.

    **Senha ou chave do administrador**: o valor da chave pública SSH ou o valor da senha, dependendo da escolha do tipo de autenticação.

    Quando todos os campos tiverem sido preenchidos, marque a caixa de seleção na parte inferior da página para aceitar os termos e selecione **comprar** para iniciar a implantação.

1. Verifique se a implantação foi concluída com êxito.  Um recurso de máquina virtual deve ter sido implantado no grupo de recursos selecionado.  Anote o nome do computador, que deve estar no formato `vm-0000000000000` . Além disso, anote o **Nome DNS** associado, que deve estar no formato `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    O **Nome DNS** pode ser obtido na seção **Visão geral** da máquina virtual implantada recentemente no portal do Azure.

    > [!div class="mx-imgBorder"]
    > [![Captura de tela mostrando o nome DNS da VM iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Se você quiser usar o SSH nessa VM após a instalação, use o **nome DNS** associado com o comando:  `ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Implantar da CLI do Azure

1. Verifique se você instalou o CLI do Azure a extensão de IOT com:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Em seguida, se você estiver usando CLI do Azure em sua área de trabalho, comece fazendo logon:

   ```azurecli-interactive
   az login
   ```

1. Se você tiver várias assinaturas, selecione a assinatura que deseja usar:
   1. Liste suas assinaturas:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Copie o campo SubscriptionId da assinatura que você deseja usar.

   1. Defina sua assinatura de trabalho com a ID que você copiou:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Crie um novo grupo de recursos (ou especifique um existente nas próximas etapas):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Crie uma nova máquina virtual:

    Para usar uma **authenticationType** de `password` , consulte o exemplo abaixo:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Para autenticar com uma chave SSH, você pode fazer isso especificando uma **authenticationType** de `sshPublicKey` e, em seguida, fornecer o valor da chave ssh no parâmetro **adminPasswordOrKey** .  Um exemplo é mostrado abaixo.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az deployment group create \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
    ```

1. Verifique se a implantação foi concluída com êxito.  Um recurso de máquina virtual deve ter sido implantado no grupo de recursos selecionado.  Anote o nome do computador, que deve estar no formato `vm-0000000000000` . Além disso, anote o **Nome DNS** associado, que deve estar no formato `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    O **nome DNS** pode ser obtido da saída formatada em JSON da etapa anterior, dentro da seção **saídas** como parte da entrada **SSH pública** .  O valor dessa entrada pode ser usado para SSH no computador implantado recentemente.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    O **nome DNS** também pode ser obtido na seção **visão geral** da máquina virtual implantada recentemente na portal do Azure.

    > [!div class="mx-imgBorder"]
    > [![Captura de tela mostrando o nome DNS da VM iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Se você quiser usar o SSH nessa VM após a instalação, use o **nome DNS** associado com o comando:  `ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge provisionado com o runtime instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com o tempo de execução do IoT Edge sendo instalado corretamente, confira a página de [solução de problemas](troubleshoot.md) .

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o runtime do IoT Edge](how-to-update-iot-edge.md).

Se você quiser abrir portas para acessar a VM por meio de SSH ou de outras conexões de entrada, consulte a documentação de máquinas virtuais do Azure na [abertura de portas e pontos de extremidade para uma VM do Linux](../virtual-machines/linux/nsg-quickstart.md)