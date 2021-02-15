---
title: Implantar o módulo GPU em seu dispositivo de GPU pro do Azure Stack Edge | Microsoft Docs
description: Descreve como habilitar a computação e tornar sua Azure Stack de computação de dispositivo do Edge pro pronta por meio da interface do usuário local.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 1f16ef0ede25f17acb915a7812ae5b15b45f78a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899720"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-pro-gpu-device"></a>Implantar um módulo IoT habilitado para GPU no dispositivo Azure Stack GPU pro Edge

Este artigo descreve como implantar um módulo de IoT Edge habilitado para GPU em seu dispositivo de GPU pro Azure Stack Edge. 

Neste artigo, você aprenderá como:
  - Prepare Azure Stack o Edge pro para executar um módulo de GPU.
  - Baixe e instale o código de exemplo de um repositório git.
  - Compile a solução e gere um manifesto de implantação.
  - Implante a solução no dispositivo Azure Stack Edge pro.
  - Monitore a saída do módulo.


## <a name="about-sample-module"></a>Sobre o módulo de exemplo

O módulo exemplo de GPU neste artigo inclui o exemplo de código de parâmetro de comparação PyTorch e TensorFlow para CPU em relação à GPU.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:

- Você tem acesso a um dispositivo Azure Stack Edge pro de 1 nó com GPU habilitado. Este dispositivo está ativado com um recurso no Azure. Consulte [ativar o dispositivo](azure-stack-edge-gpu-deploy-activate.md).
- Você configurou a computação neste dispositivo. Siga as etapas em [tutorial: configurar a computação em seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-compute.md).
- Um ACR (registro de contêiner do Azure). Vá para a folha **chaves de acesso** e anote o servidor de logon do ACR, o nome de usuário e a senha. Para obter mais informações, acesse [início rápido: criar um registro de contêiner privado usando o portal do Azure](../container-registry/container-registry-get-started-portal.md#create-a-container-registry).
- Os seguintes recursos de desenvolvimento em um cliente do Windows:
    - [CLI do Azure 2,0 ou posterior](https://aka.ms/installazurecliwindows)
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Talvez você precise criar uma conta para baixar e instalar o software.
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Extensão de Azure IOT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).    
    - [Extensão do Python para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)    
    - [Python 3](https://www.python.org/)    
    - Pip para instalar pacotes do Python (normalmente incluídos na sua instalação do Python)

## <a name="get-the-sample-code"></a>Obter o código de exemplo

1. Vá para os [padrões de borda inteligente do Azure nos exemplos do Azure](https://github.com/azure-samples/azure-intelligent-edge-patterns). Clone ou baixe o arquivo zip para o código. 

    ![Baixar o arquivo zip](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    Extraia os arquivos do zip. Você também pode clonar os exemplos.

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>Compilar e implantar o módulo

1. Abra a pasta **GpuReferenceModules** em Visual Studio Code.

    ![Abrir GPUReferenceModules no VS Code](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. Abra o *deployment.template.jsem* e identifique os parâmetros referenciados para o registro de contêiner. No arquivo a seguir, CONTAINER_REGISTRY_USERNAME, CONTAINER_REGISTRY_PASSWORD e CONTAINER_REGISTRY_NAME são usados.

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. Crie um arquivo. Preencha os valores dos parâmetros do registro de contêiner (use os identificados na etapa anterior) da seguinte maneira: 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    Um arquivo *. env* de exemplo é mostrado abaixo:
    
    ![Criar e salvar o arquivo. env](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. Salve o arquivo como *. env* na pasta **SampleSolution**

5. Para entrar no Docker, digite o seguinte comando no terminal integrado Visual Studio Code. 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    Vá para a seção **chaves de acesso** do registro de contêiner no portal do Azure. Copie e use o nome do registro, a senha e o servidor de logon.

    ![Chaves de acesso no registro de contêiner](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    Depois que as credenciais são fornecidas, a entrada é realizada com sucesso.

    ![Entrada bem-sucedida](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. Envie sua imagem por push para o registro de contêiner do Azure. No VS Code Explorer, selecione e clique com o botão direito do mouse na **deployment.template.jsno** arquivo e, em seguida, selecione **criar e enviar por push IOT Edge solução**. 

    ![Compilar e enviar por push a solução IoT Edge](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    Se a extensão Python e Python não estiver instalada, elas serão instaladas quando você compilar e enviar por push a solução. No entanto, isso resultaria em tempos de compilação mais longos. 

    Depois que essa etapa for concluída, você verá o módulo no registro de contêiner.

    ![Módulo no registro de contêiner](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. Para criar um manifesto de implantação, clique com o botão direito do mouse no **deployment.template.jsem** e, em seguida, selecione **gerar IOT Edge manifesto de implantação**. 

    ![Gerar um manifesto de implantação do IoT Edge](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    A notificação informa o caminho no qual o manifesto de implantação foi gerado. O manifesto é o `deployment.amd64.json` arquivo gerado na pasta de **configuração** . 

8. Selecione a **deployment.amd64.jsem** arquivo na pasta **configuração** e escolha **criar implantação para um único dispositivo**. Não use o **deployment.template.jsno** arquivo. 

    ![Criar implantação para dispositivo único](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    Na janela **saída** , você deverá ver uma mensagem implantada com êxito.

    ![Implantação bem-sucedida na saída](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>Monitorar o módulo  

1. Na paleta de comandos do VS Code, execute **Hub IoT do Azure: selecionar Hub IoT**.

2. Escolha a assinatura e o hub IoT que contêm o dispositivo do IoT Edge que você deseja configurar. Nesse caso, selecione a assinatura usada para implantar o Azure Stack dispositivo pro Edge e selecione o dispositivo IoT Edge criado para seu dispositivo do Azure Stack Edge pro. Isso ocorre quando você configura a computação por meio do portal do Azure nas etapas anteriores.

3. No VS Code Explorer, expanda a seção Hub IoT do Azure. Em **dispositivos**, você deve ver o dispositivo IOT Edge correspondente ao dispositivo do Azure Stack Edge pro. 

    1. Selecione esse dispositivo, clique com o botão direito do mouse e selecione **Iniciar Monitoramento de ponto de extremidade de evento interno**.
  
        ![Começar a monitorar](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Vá para **dispositivos > módulos** e você deverá ver o **módulo de GPU** em execução.

        ![Módulo no Hub IoT](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. O terminal de VS Code também deve mostrar os eventos do Hub IoT como a saída de monitoramento para seu dispositivo do Azure Stack Edge pro.

        ![Saída de monitoramento](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Você pode ver que o tempo necessário para executar o mesmo conjunto de operações (5000 iterações de transformação de forma) pela GPU é muito menor do que para a CPU.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [Configurar a GPU para usar um módulo](azure-stack-edge-j-series-configure-gpu-modules.md).
