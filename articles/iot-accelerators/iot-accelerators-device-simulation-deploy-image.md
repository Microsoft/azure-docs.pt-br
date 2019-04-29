---
title: Implantar uma imagem personalizada de Simulação de Dispositivo – Azure | Microsoft Docs
description: Neste guia de instruções, você aprenderá a implantar uma imagem personalizada do Docker da solução de Simulação de Dispositivo para o Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61448385"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Implantar uma imagem personalizada do Docker da Simulação de Dispositivo

Você pode modificar a solução de Simulação de Dispositivo para adicionar recursos personalizados. Por exemplo, o artigo [Serializar a telemetria usando Buffers de Protocolo](iot-accelerators-device-simulation-protobuf.md) mostra como adicionar um dispositivo personalizado à solução que usa Buffers de Protocolo (Protobuf) para enviar telemetria. Depois de testar suas alterações localmente, a próxima etapa é implantar suas alterações em sua instância de Simulação de Dispositivo no Azure. Para concluir essa tarefa, você precisa criar e implantar uma imagem do Docker que contém o serviço modificado.

As etapas deste guia de instruções mostram como:

1. Preparar um ambiente de desenvolvimento
1. Gerar uma nova imagem do Docker
1. Configurar a Simulação de Dispositivo para usar sua nova imagem do Docker
1. Executar uma simulação usando a nova imagem

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, você precisará de:

* Uma instância implantada da [Simulação de Dispositivo](quickstart-device-simulation-deploy.md).
* Docker. Baixe o [Docker Community Edition](https://www.docker.com/products/docker-engine#/download) em sua plataforma.
* Uma [conta do Hub do Docker](https://hub.docker.com/) na qual possa carregar suas imagens do Docker. Em sua conta do Hub do Docker, crie um repositório público chamado **simulação de dispositivo**.
* Uma [solução de Simulação de Dispositivo](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) modificada e testada em seu computador local. Por exemplo, você pode modificar a solução para [Serializar a telemetria usando Buffers de Protocolo](iot-accelerators-device-simulation-protobuf.md).
* Um shell que possa executar SSH. Se você instalar o Git for Windows, poderá usar o shell **bash** que faz parte da instalação. Você também pode usar o [Azure Cloud Shell](https://shell.azure.com/).

As instruções neste artigo pressupõem que você está usando o Windows. Se você estiver usando outro sistema operacional, talvez você precise ajustar alguns dos caminhos de arquivo e comandos para se adequar ao seu ambiente.

## <a name="create-a-new-docker-image"></a>Criar uma nova imagem do Docker

Para implantar suas próprias alterações no serviço de Simulação de Dispositivo, você precisa editar os scripts de compilação e implantação na pasta **scripts\docker** para carregar os contêineres em sua conta do hub do docker

### <a name="modify-the-docker-scripts"></a>Modificar os scripts do docker

Modifique os scripts do Docker **build.cmd**, **publish.cmd** e **run.cmd** na pasta **scripts\docker** com suas informações do repositório do Hub do Docker. Essas etapas pressupõem que você criou um repositório público chamado **simulação de dispositivo**:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Atualize o arquivo **docker-compose.yml** da seguinte forma:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Configurar a solução para incluir arquivos novos

Se você adicionou arquivos novos de modelo do dispositivo, precisará incluí-los explicitamente na solução. Adicione uma entrada para o **services/services.csproj** para cada arquivo que você queira incluir. Por exemplo, se você concluiu as instruções em [Serializar a telemetria usando Buffers de Protocolo](iot-accelerators-device-simulation-protobuf.md), adicione as seguintes entradas:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Gerar novas imagens do Docker e enviar por push ao Hub do Docker

Publique a nova imagem do Docker no Hub do Docker usando sua conta do hub do docker:

1. Abra um prompt de comando e navegue até sua cópia local do repositório de simulação de dispositivo.

1. Navegue até a pasta **docker**:

    ```cmd
    cd scripts\docker
    ```

1. Execute o comando a seguir para criar a imagem do Docker:

    ```cmd
    build.cmd
    ```

1. Execute o comando a seguir para publicar a imagem do Docker em seu repositório do Hub do Docker. Entre no Docker com suas credenciais do Hub do Docker:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Atualizar o serviço

Para atualizar o contêiner de Simulação de Dispositivo a fim de usar sua imagem personalizada, conclua estas etapas:

* Use o SSH para se conectar à máquina virtual que está hospedando sua instância de Simulação de Dispositivo. Use o endereço IP e a senha que você anotou na seção anterior:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Navegue até o diretório **/app**:

    ```sh
    cd /app
    ```

* Edite o arquivo **docker-compose.yml**:

    ```sh
    sudo nano docker-compose.yml
    ```

    Modifique a **imagem** para apontar a imagem personalizada de **simulação de dispositivo** carregada para o repositório do Hub do Docker:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Salve suas alterações.

* Execute o seguinte comando para reiniciar os microsserviços:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Executar a simulação

Agora, você pode executar uma simulação usando os modelos personalizados de Simulação de Dispositivo:

1. Inicialize a interface do usuário da Web de Simulação de Dispositivo dos [Aceleradores de Solução de IoT do Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Use a interface do usuário da Web para configurar e executar uma simulação. Se você já concluiu [Serializar a telemetria usando Buffers de Protocolo](iot-accelerators-device-simulation-protobuf.md), use o modelo de dispositivo personalizado.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a implantar uma imagem personalizada de Simulação de Dispositivo, convém saber como [Usar um hub IoT existente com o acelerador de solução de Simulação de Dispositivo](iot-accelerators-device-simulation-choose-hub.md).