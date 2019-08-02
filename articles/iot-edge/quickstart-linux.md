---
title: Início Rápido para criar um dispositivo do Azure IoT Edge no Linux | Microsoft Docs
description: Neste início rápido, aprenda a criar um dispositivo IoT Edge e então implantar o código pré-compilado remotamente do portal do Azure.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/09/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: a3d2b51824f4293dc790979ca2513d011b60fd9c
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414433"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-device"></a>Início Rápido: Implantar seu primeiro módulo do IoT Edge em um dispositivo Linux

O Azure IoT Edge leva o poder da nuvem para seus dispositivos Internet das Coisas. Neste início rápido, aprenda a usar a interface de nuvem para implantar remotamente um código pré-compilado em um dispositivo IoT Edge.

Neste guia de início rápido, você aprende a:

1. Crie um Hub IoT.
2. Registrar um dispositivo IoT Edge em seu Hub IoT.
3. Instale e inicie o tempo de execução do IoT Edge no dispositivo.
4. Implantar um módulo em um dispositivo IoT Edge remotamente.

![Diagrama – Início Rápido da arquitetura para dispositivo e nuvem](./media/quickstart-linux/install-edge-full.png)

Este Início Rápido orienta você pela criação de uma máquina virtual do Azure que é configurada para ser o dispositivo do IoT Edge. Em seguida, você pode implantar um módulo do portal do Azure em seu dispositivo. O módulo implantado neste guia de início rápido é um sensor simulado que gera dados de temperatura, umidade e pressão. Os outros tutoriais do Azure IoT Edge se baseiam no trabalho feito aqui com a implantação de módulos que analisam os dados simulados para obter informações de negócios.

Se você não tiver uma assinatura do Azure ativa, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você usa a CLI do Azure para concluir muitas das etapas neste guia de início rápido e o Azure IoT possui uma extensão para habilitar funcionalidades adicionais.

Adicione a extensão do Azure IoT à instância do shell de nuvem.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Pré-requisitos

Recursos de nuvem:

* Um grupo de recursos para gerenciar todos os recursos que você usará neste início rápido.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Dispositivo IoT Edge:

* Uma máquina virtual ou dispositivo com Linux para agir como o dispositivo IoT Edge. Use a máquina virtual do [Azure IoT Edge no Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) fornecida pela Microsoft, que pré-instala tudo o que você precisa para executar o IoT Edge em um dispositivo. Aceite os termos de uso e crie essa máquina virtual usando os seguintes comandos:

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

   A criação e a inicialização da nova máquina virtual podem levar alguns minutos.

   Ao criar uma nova máquina virtual, anote o **publicIpAddress**, que é fornecido como parte da saída do comando create. Você usará esse endereço IP público para se conectar à máquina virtual posteriormente no início rápido.

* Se você preferir executar o tempo de execução do Azure IoT Edge em seu próprio dispositivo, siga as instruções em [Instalar o tempo de execução do Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md).

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Comece o início rápido criando um Hub IoT com a CLI do Azure.

![Diagrama – Criar um hub IoT na nuvem](./media/quickstart-linux/create-iot-hub.png)

O nível gratuito do Hub IoT funciona para este guia de início rápido. Se você tiver usado o Hub IoT antes e já tiver um hub disponível criado, você pode usar esse Hub IoT. Cada assinatura pode ter somente um hub IoT gratuito.

O código a seguir cria um hub **F1** gratuito no grupo de recursos **IoTEdgeResources**. Substitua *{hub_name}* por um nome exclusivo para o Hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1
   ```

   Se você receber um erro porque já exsite um hub gratuito na sua assinatura, altere o SKU para **S1**. Caso você receba um erro que o nome do Hub IoT não está disponível, isso significa que alguém já tem um hub com esse nome. Tente usar um novo nome.

## <a name="register-an-iot-edge-device"></a>Registrar um dispositivo IoT Edge

Registre um dispositivo IoT Edge no Hub IoT recém-criado.

![Diagrama – registrar um dispositivo com uma identidade do Hub IoT](./media/quickstart-linux/register-device.png)

Crie uma identidade do dispositivo para seu dispositivo IoT Edge para que ele possa se comunicar com o hub IoT. A identidade do dispositivo reside na nuvem e você usa uma cadeia de conexão de dispositivo exclusiva para associar um dispositivo físico a uma identidade do dispositivo.

Como os dispositivos IoT Edge se comportam e podem ser gerenciados diferentemente de dispositivos IoT comuns, declare essa identidade para ser um dispositivo IoT Edge com o sinalizador `--edge-enabled`.

1. No Azure Cloud Shell, digite o seguinte comando para criar um dispositivo denominado **myEdgeDevice** no seu hub.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Se você receber um erro sobre as chaves de política do iothubowner, verifique se seu cloud shell está executando a versão mais recente da extensão do azure-cli-iot-ext.

2. Recupere a cadeia de conexão para o seu dispositivo, o que vincula o dispositivo físico à sua identidade no Hub IoT.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copie o valor da chave `connectionString` da saída JSON e salve-o. Esse valor é a cadeia de conexão do dispositivo. Você usará essa cadeia de conexão para configurar o tempo de execução do IoT Edge na próxima seção.

   ![Recuperar a cadeia de conexão da saída da CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Configurar o dispositivo IoT Edge

Inicie o tempo de execução do Azure IoT Edge no dispositivo IoT Edge.

![Diagrama – iniciar o tempo de execução no dispositivo](./media/quickstart-linux/start-runtime.png)

O tempo de execução do IoT Edge é implantado em todos os dispositivos IoT Edge. Tem três componentes. O **daemon de segurança do IoT Edge** é iniciado sempre que um dispositivo IoT Edge é iniciado e inicializa o dispositivo inicializando o agente do IoT Edge. O **agente do IoT Edge** facilita a implantação e o monitoramento de módulos no dispositivo IoT Edge, incluindo o hub do IoT Edge. O **hub IoT Edge** gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT.

Durante a configuração do tempo de execução, você precisa fornecer uma cadeia de conexão do dispositivo. Use a cadeia de caracteres que você recuperou da CLI do Azure. Essa cadeia de caracteres associa seu dispositivo físico à identidade do dispositivo IoT Edge no Azure.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Configure a cadeia de conexão do dispositivo do IoT Edge

Se você estiver usando o Azure IoT Edge na máquina virtual Ubuntu conforme descrito nos pré-requisitos, o dispositivo já terá o tempo de execução do IoT Edge instalado. Basta configurar seu dispositivo com a cadeia de conexão do dispositivo que você recuperou na seção anterior. Você pode fazer isso remotamente, sem precisar se conectar à máquina virtual. Execute o seguinte comando, substituindo **{cadeia_de_conexão_do_dispositivo}** pela sua própria cadeia de caracteres.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Se você estiver executando o IoT Edge no computador local ou em um dispositivo ARM32 ou ARM64, precisará instalar o tempo de execução do IoT Edge e seus pré-requisitos em seu dispositivo. Siga as instruções em [Instalar o tempo de execução do Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md) e, em seguida, retorne a este início rápido.

### <a name="view-the-iot-edge-runtime-status"></a>Veja o status do tempo de execução do IoT Edge

O restante dos comandos neste início rápido ocorrem em seu dispositivo IoT Edge propriamente dito, para que você possa ver o que está acontecendo no dispositivo. Se você estiver usando uma máquina virtual, conecte-se a ela agora usando o endereço IP público que foi emitido como saída pelo comando de criação. Também é possível encontrar o endereço IP público na página de visão geral da máquina virtual no portal do Azure. Use o comando a seguir para se conectar à sua máquina virtual. Substitua **{azureuser}** se você tiver usado um nome de usuário diferente daquele sugerido nos pré-requisitos. Substitua **{publicIpAddress}** pelo seu endereço de máquina.

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

Verifique se o tempo de execução foi instalado e configurado com êxito em seu dispositivo IoT Edge.

>[!TIP]
>Você precisa de privilégios elevados para executar comandos `iotedge`. Depois que você sair da sua máquina e fizer login novamente na primeira vez após instalar o tempo de execução do IoT Edge, suas permissões serão atualizadas automaticamente. Até lá, use **sudo** na frente dos comandos.

1. Verifique se o daemon de segurança do IoT Edge está em execução como um serviço do sistema.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Veja o daemon do IoT Edge em execução como um serviço do sistema](./media/quickstart-linux/iotedged-running.png)

2. Se você precisar solucionar problemas do serviço, recupere os logs de serviço.

   ```bash
   journalctl -u iotedge
   ```

3. Visualizar os módulos em execução no seu dispositivo.

   ```bash
   sudo iotedge list
   ```

   ![Exibir um módulo no dispositivo](./media/quickstart-linux/iotedge-list-1.png)

Seu dispositivo IoT Edge agora está configurado. Ele está pronto para executar os módulos implantados na nuvem.

## <a name="deploy-a-module"></a>Implantar um módulo

Gerencie o dispositivo Azure IoT Edge na nuvem para implantar um módulo que enviará dados telemétricos ao Hub IoT.
![Diagrama – Implantar o módulo da nuvem para dispositivo](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Exibir os dados gerados

Neste guia de início rápido, você criou um novo dispositivo IoT Edge e instalou o tempo de execução de IoT Edge nele. Em seguida, você usou o portal do Azure para implantar um módulo do IoT Edge para ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo.

Nesse caso, o módulo enviado por push cria dados de exemplo que podem ser usados para teste. O módulo de sensor de temperatura simulado gera dados de ambiente que você pode usar para testar posteriormente. O sensor simulado está monitorando um computador e o ambiente em torno do computador. Por exemplo, esse sensor pode estar em uma sala de servidor, em um chão de fábrica ou em uma turbina eólica. A mensagem inclui a temperatura ambiente e umidade, temperatura do computador, pressão e um carimbo de data/hora. Os tutoriais do IoT Edge usam os dados criados por esse módulo de dados de teste para análise.

Abra novamente o prompt de comando no seu dispositivo IoT Edge ou use a conexão SSH na CLI do Azure. Confirme se o módulo implantado da nuvem está em execução no seu dispositivo IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Exibir três módulos no seu dispositivo](./media/quickstart-linux/iotedge-list-2.png)

Exiba as mensagens que estão sendo enviadas do módulo do sensor de temperatura:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Ao fazer referência a nomes de módulo, comandos do IoT Edge diferenciam maiúsculas de minúsculas.

   ![Exibir os dados do seu módulo](./media/quickstart-linux/iotedge-logs.png)

Você também pode inspecionar as mensagens chegam ao hub IoT usando a [Extensão do Kit de Ferramentas do Hub IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente conhecida como extensão do Kit de Ferramentas do Azure IoT).

## <a name="clean-up-resources"></a>Limpar recursos

Se você deseja prosseguir para os tutoriais do IoT Edge, pode usar o dispositivo registrado e configurado neste guia de início rápido. Caso contrário, você pode excluir os recursos do Azure que criou para evitar encargos.

Se você tiver criado a sua máquina virtual e o Hub IoT em um novo grupo de recursos, é possível excluir esse grupo e todos os recursos associados. Verifique novamente o conteúdo do grupo de recursos para ter certeza de que não haja nada que você queira manter. Caso não queira excluir o grupo inteiro, é possível excluir recursos individuais em vez disso.

Remova o grupo **IoTEdgeResources**.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Próximas etapas


Neste início rápido, você criou um dispositivo IoT Edge e usou a interface de nuvem do Azure IoT Edge para implantar código no dispositivo. Agora, você tem um dispositivo de teste que gera dados brutos sobre seu ambiente.

A próxima etapa é configurar seu ambiente de desenvolvimento local para que você possa começar a criar módulos de IoT Edge que executem a sua lógica de negócios. 

> [!div class="nextstepaction"]
> [Começar a desenvolver módulos do IoT Edge para dispositivos Linux](tutorial-develop-for-linux.md)
