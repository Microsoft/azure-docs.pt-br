---
title: Início Rápido para criar um dispositivo do Azure IoT Edge no Linux | Microsoft Docs
description: Neste início rápido, aprenda a criar um dispositivo IoT Edge no Linux e implantar o código pré-compilado remotamente do portal do Azure.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/12/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 37f4a63d0a901fd70e0a60bb435efdaf08868616
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463435"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Início Rápido: Implantar seu primeiro módulo do IoT Edge em um dispositivo virtual Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Teste o Azure IoT Edge neste início rápido implantando um código em contêineres em um dispositivo virtual Linux do IoT Edge. O IoT Edge permite que você gerencie remotamente o código em seus dispositivos para que você possa enviar mais cargas de trabalho para a borda. Para este início rápido, recomendamos usar uma máquina virtual do Azure para o dispositivo IoT Edge, o que permitirá criar rapidamente um computador de teste e excluí-lo quando você terminar.

Neste guia de início rápido, você aprende a:

* Crie um Hub IoT.
* Registrar um dispositivo IoT Edge em seu Hub IoT.
* Instalar e iniciar o runtime do IoT Edge em um dispositivo virtual.
* Implantar um módulo em um dispositivo IoT Edge remotamente.

![Diagrama – Início Rápido da arquitetura para dispositivo e nuvem](./media/quickstart-linux/install-edge-full.png)

Este Início Rápido orienta você pela criação de uma máquina virtual Linux configurada para ser um dispositivo do IoT Edge. Em seguida, implante um módulo do portal do Azure em seu dispositivo. O módulo usado neste guia de início rápido é um sensor simulado que gera dados de temperatura, umidade e pressão. Os outros tutoriais do Azure IoT Edge se baseiam no trabalho feito aqui com a implantação de módulos adicionais que analisam os dados simulados para obter informações de negócios.

Se você não tiver uma assinatura do Azure ativa, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Prepare seu ambiente para a CLI do Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Recursos de nuvem:

* Um grupo de recursos para gerenciar todos os recursos que você usará neste início rápido. Usamos o nome do grupo de recursos de exemplo **IoTEdgeResources** em todo este guia de início rápido e nos tutoriais a seguir.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Comece o início rápido criando um Hub IoT com a CLI do Azure.

![Diagrama – Criar um hub IoT na nuvem](./media/quickstart-linux/create-iot-hub.png)

O nível gratuito do Hub IoT funciona para este guia de início rápido. Se você tiver usado o Hub IoT antes e já tiver um hub criado, poderá usar esse Hub IoT.

O código a seguir cria um hub **F1** gratuito no grupo de recursos **IoTEdgeResources**. Substitua `{hub_name}` por um nome exclusivo para o Hub IoT. A criação de um Hub IoT pode demorar alguns minutos.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Se você receber um erro porque já exsite um hub gratuito na sua assinatura, altere o SKU para **S1**. Cada assinatura pode ter somente um hub IoT gratuito. Caso você receba um erro que o nome do Hub IoT não está disponível, isso significa que alguém já tem um hub com esse nome. Tente usar um novo nome.

## <a name="register-an-iot-edge-device"></a>Registrar um dispositivo IoT Edge

Registre um dispositivo IoT Edge no Hub IoT recém-criado.

![Diagrama – registrar um dispositivo com uma identidade do Hub IoT](./media/quickstart-linux/register-device.png)

Crie uma identidade do dispositivo para seu dispositivo IoT Edge para que ele possa se comunicar com o hub IoT. A identidade do dispositivo reside na nuvem e você usa uma cadeia de conexão de dispositivo exclusiva para associar um dispositivo físico a uma identidade do dispositivo.

Como os dispositivos IoT Edge se comportam e podem ser gerenciados diferentemente de dispositivos IoT comuns, declare essa identidade para ser um dispositivo IoT Edge com o sinalizador `--edge-enabled`.

1. No Azure Cloud Shell, digite o comando a seguir para criar um dispositivo denominado **myEdgeDevice** no seu hub.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Se você receber um erro sobre as chaves de política do iothubowner, verifique se o Cloud Shell está executando a última versão da extensão azure-iot.

2. Veja a cadeia de conexão para o seu dispositivo, o que vincula o dispositivo físico à sua identidade no Hub IoT. Ele contém o nome do seu hub IoT, o nome do seu dispositivo e uma chave compartilhada que autentica as conexões entre os dois. Vamos nos referir a essa cadeia de conexão novamente na próxima seção quando você configurar seu dispositivo do IoT Edge.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![Exibir a cadeia de conexão da saída da CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Configurar o dispositivo IoT Edge

Crie uma máquina virtual com o Azure runtime do IoT Edge nela.

![Diagrama – iniciar o runtime no dispositivo](./media/quickstart-linux/start-runtime.png)

O runtime do IoT Edge é implantado em todos os dispositivos IoT Edge. Tem três componentes. O *daemon de segurança do IoT Edge* é iniciado sempre que um dispositivo IoT Edge é iniciado e inicializa o dispositivo inicializando o agente do IoT Edge. O *agente do IoT Edge* facilita a implantação e o monitoramento de módulos no dispositivo IoT Edge, incluindo o hub do IoT Edge. O *hub IoT Edge* gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT.

Durante a configuração do runtime, você precisa fornecer uma cadeia de conexão do dispositivo. Essa é a cadeia de caracteres que você recuperou da CLI do Azure. Essa cadeia de caracteres associa seu dispositivo físico à identidade do dispositivo IoT Edge no Azure.

### <a name="deploy-the-iot-edge-device"></a>Implantar o dispositivo do IoT Edge

Esta seção usa um modelo do Azure Resource Manager para criar uma máquina virtual e instalar o runtime do IoT Edge nela. Se você quiser usar seu dispositivo Linux, poderá seguir as etapas de instalação em [Instalar o runtime do Azure IoT Edge](how-to-install-iot-edge.md) e retornar a este início rápido.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Use o comando da CLI a seguir para criar seu dispositivo do IoT Edge com base no modelo predefinido [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy).

* Para os usuários do Bash ou do Cloud Shell, copie o seguinte comando em um editor de texto, substitua o texto do espaço reservado pelas suas informações e copie-as na janela do Bash ou do Cloud Shell:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* Para usuários do PowerShell, copie o seguinte comando para a janela do PowerShell e substitua o texto do espaço reservado pelas suas informações:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://aka.ms/iotedge-vm-deploy" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Use o comando da CLI a seguir para criar seu dispositivo do IoT Edge com base no modelo predefinido [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy/tree/1.2.0-rc4).

* Para os usuários do Bash ou do Cloud Shell, copie o seguinte comando em um editor de texto, substitua o texto do espaço reservado pelas suas informações e copie-as na janela do Bash ou do Cloud Shell:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0-rc4/edgeDeploy.json" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* Para usuários do PowerShell, copie o seguinte comando para a janela do PowerShell e substitua o texto do espaço reservado pelas suas informações:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0-rc4/edgeDeploy.json" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```
:::moniker-end
<!-- end 1.2 -->

Este modelo usa os seguintes parâmetros:

| Parâmetro | Descrição |
| --------- | ----------- |
| **resource-group** | O grupo de recursos no qual os recursos serão criados. Use o **IoTEdgeResources** padrão que usamos neste artigo ou forneça o nome de um grupo de recursos em sua assinatura. |
| **template-uri** | Um ponteiro para o modelo do Resource Manager que estamos usando. |
| **dnsLabelPrefix** | Uma cadeia de caracteres que será usada para criar o nome de host da máquina virtual. Substitua o texto do espaço reservado por um nome para a sua máquina virtual. |
| **adminUsername** | Um nome de usuário para a conta do administrador da máquina virtual. Use o exemplo **azureUser** ou fornecer um novo nome de usuário. |
| **deviceConnectionString** | A cadeia de conexão da identidade do dispositivo no Hub IoT, que é usada para configurar o runtime do IoT Edge na máquina virtual. O comando da CLI dentro desse parâmetro captura a cadeia de conexão para você. Substitua o texto do espaço reservado pelo nome do hub IoT. |
| **authenticationType** | O método de autenticação para a conta do administrador. Este guia de início rápido usa autenticação por **senha**, mas você também pode definir esse parâmetro como **sshPublicKey**. |
| **adminPasswordOrKey** | A senha ou o valor da chave SSH para a conta do administrador. Substitua o texto do espaço reservado por uma senha segura. Sua senha deve ter pelo menos 12 caracteres e ter três dos quatro seguintes itens: caracteres minúsculos, caracteres maiúsculos, dígitos e caracteres especiais. |

Depois que a implantação for concluída, você deverá receber a saída formatada em JSON na CLI que contém as informações de SSH para se conectar à máquina virtual. Copie o valor da entrada **SSH público** da seção **saídas**:

   ![Recuperar o valor de ssh público da saída](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>Veja o status do runtime do IoT Edge

O restante dos comandos neste início rápido ocorrem em seu dispositivo IoT Edge propriamente dito, para que você possa ver o que está acontecendo no dispositivo. Se você estiver usando uma máquina virtual, conecte-se a esse computador agora usando o nome de usuário do administrador que você configurou e o nome DNS que foi apresentado pelo comando de implantação. Também é possível encontrar o nome DNS em sua página de visão geral da máquina virtual no portal do Azure. Use o comando a seguir para se conectar à sua máquina virtual. Substitua `{admin username}` e `{DNS name}` pelos seus próprios valores.

   ```console
   ssh {admin username}@{DNS name}
   ```

Uma vez conectado à sua máquina virtual, verifique se o runtime foi instalado e configurado com êxito em seu dispositivo do IoT Edge.

<!--1.1 -->
:::moniker range="iotedge-2018-06"

1. Verifique se o daemon de segurança do IoT Edge está em execução como um serviço do sistema.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Veja o daemon do IoT Edge em execução como um serviço do sistema](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >Você precisa de privilégios elevados para executar comandos `iotedge`. Depois que você sair da sua máquina e fizer login novamente na primeira vez após instalar o runtime do IoT Edge, suas permissões serão atualizadas automaticamente. Até lá, use `sudo` na frente dos comandos.

2. Se você precisar solucionar problemas do serviço, recupere os logs de serviço.

   ```bash
   journalctl -u iotedge
   ```

3. Exiba todos os módulos em execução no seu dispositivo IoT Edge. Como o serviço acabou de ser iniciado pela primeira vez, você só verá o módulo **edgeAgent** em execução. O módulo edgeAgent é executado por padrão e ajuda a instalar e a iniciar quaisquer módulos adicionais que você implante em seu dispositivo.

   ```bash
   sudo iotedge list
   ```

   ![Exibir um módulo no dispositivo](./media/quickstart-linux/iotedge-list-1.png)
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Verifique se o IoT Edge está sendo executado. O comando a seguir deverá retornar o status **OK** se o IoT Edge estiver em execução ou fornecer erros de serviço.

   ```bash
   sudo iotedge system status
   ```

   >[!TIP]
   >Você precisa de privilégios elevados para executar comandos `iotedge`. Depois que você sair da sua máquina e fizer login novamente na primeira vez após instalar o runtime do IoT Edge, suas permissões serão atualizadas automaticamente. Até lá, use `sudo` na frente dos comandos.

2. Se você precisar solucionar problemas do serviço, recupere os logs de serviço.

   ```bash
   sudo iotedge system logs
   ```

3. Exiba todos os módulos em execução no seu dispositivo IoT Edge. Como o serviço acabou de ser iniciado pela primeira vez, você só verá o módulo **edgeAgent** em execução. O módulo edgeAgent é executado por padrão e ajuda a instalar e a iniciar quaisquer módulos adicionais que você implante em seu dispositivo.

   ```bash
   sudo iotedge list
   ```

:::moniker-end
<!-- end 1.2 -->

Seu dispositivo IoT Edge já está configurado. Ele está pronto para executar os módulos implantados na nuvem.

## <a name="deploy-a-module"></a>Implantar um módulo

Gerencie o dispositivo Azure IoT Edge na nuvem para implantar um módulo que enviará dados telemétricos ao Hub IoT.

![Diagrama – Implantar o módulo da nuvem para dispositivo](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Como o IoT Edge versão 1.2 está em versão prévia pública, há uma etapa extra a ser executada para atualizar os módulos de runtime para suas versões prévias públicas.

1. Na página de detalhes do dispositivo, selecione **Definir Módulos** novamente.

1. Selecione **Configurações de Runtime**.

1. Atualize o campo **Imagem** para os módulos do hub do IoT Edge e do agente do IoT Edge para usar a marca de versão 1.2.0-rc4. Por exemplo:

   * `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4`
   * `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4`

1. O módulo do sensor de temperatura simulado ainda deve estar listado na seção módulos. Você não precisa fazer nenhuma alteração nesse módulo para a versão prévia.

1. Selecione **Examinar + criar**.

1. Selecione **Criar**.

1. Na página de detalhes do dispositivo, você pode selecionar **$edgeAgent** ou **$edgeHub** para ver se os detalhes do módulo refletem a versão prévia pública da imagem.

:::moniker-end
<!-- end 1.2 -->

## <a name="view-generated-data"></a>Exibir os dados gerados

Neste guia de início rápido, você criou um novo dispositivo IoT Edge e instalou o runtime do IoT Edge nele. Em seguida, você usou o portal do Azure para implantar um módulo do IoT Edge para ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo.

Nesse caso, o módulo enviado por push gera dados de ambiente de exemplo que você pode usar para testar mais tarde. O sensor simulado está monitorando um computador e o ambiente em torno do computador. Por exemplo, esse sensor pode estar em uma sala de servidor, em um chão de fábrica ou em uma turbina eólica. A mensagem inclui a temperatura ambiente e umidade, temperatura do computador, pressão e um carimbo de data/hora. Os tutoriais do IoT Edge usam os dados criados por esse módulo de dados de teste para análise.

Abra novamente o prompt de comando no seu dispositivo IoT Edge ou use a conexão SSH na CLI do Azure. Confirme se o módulo implantado da nuvem está em execução no seu dispositivo IoT Edge:

   ```bash
   sudo iotedge list
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
   ![Exibir três módulos no seu dispositivo](./media/quickstart-linux/iotedge-list-2-version-201806.png)
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
   ![Exibir três módulos no seu dispositivo](./media/quickstart-linux/iotedge-list-2-version-202011.png)
:::moniker-end

Exiba as mensagens que estão sendo enviadas do módulo do sensor de temperatura:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Ao fazer referência a nomes de módulo, comandos do IoT Edge diferenciam maiúsculas de minúsculas.

   ![Exibir os dados do seu módulo](./media/quickstart-linux/iotedge-logs.png)

Você também pode exibir as mensagens que são recebidas pelo seu hub IoT usando a [Extensão do Hub IoT do Azure para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Limpar os recursos

Se você deseja prosseguir para os tutoriais do IoT Edge, pode usar o dispositivo registrado e configurado neste guia de início rápido. Caso contrário, você pode excluir os recursos do Azure que criou para evitar encargos.

Se você tiver criado a sua máquina virtual e o Hub IoT em um novo grupo de recursos, é possível excluir esse grupo e todos os recursos associados. Verifique novamente o conteúdo do grupo de recursos para ter certeza de que não haja nada que você queira manter. Caso não queira excluir o grupo inteiro, é possível excluir recursos individuais em vez disso.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível.

Remova o grupo **IoTEdgeResources**. A exclusão de um grupo de recursos pode demorar alguns minutos.

```azurecli-interactive
az group delete --name IoTEdgeResources --yes
```

Para confirmar se o grupo de recursos foi removido, veja a lista de grupos de recursos.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um dispositivo IoT Edge e usou a interface de nuvem do Azure IoT Edge para implantar código no dispositivo. Agora, você tem um dispositivo de teste que gera dados brutos sobre seu ambiente.

A próxima etapa é configurar seu ambiente de desenvolvimento local para que você possa começar a criar módulos de IoT Edge que executem a sua lógica de negócios.

> [!div class="nextstepaction"]
> [Começar a desenvolver módulos do IoT Edge para dispositivos Linux](tutorial-develop-for-linux.md)
