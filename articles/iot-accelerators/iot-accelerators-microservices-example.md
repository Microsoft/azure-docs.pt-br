---
title: Alterar e reimplantar um microsserviço - Azure | Microsoft Docs
description: Este tutorial mostra como alterar e reimplantar um microsserviço no Monitoramento Remoto
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 1552c54afe2195d58a032e9cc7bfa5aa70c844b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447570"
---
# <a name="customize-and-redeploy-a-microservice"></a>Personalizar e reimplantar um microsserviço

Este tutorial mostra como editar um dos [microsserviços](https://azure.com/microservices) na solução de Monitoramento Remoto, criar uma imagem do microsserviço, implantar a imagem no hub do Docker e, em seguida, usá-la na solução de Monitoramento Remoto. Para apresentar este conceito, o tutorial usa um cenário básico em que você pode chamar uma API de microsserviço e alterar o status da mensagem de "Alive and Well" para "New Edits Made Here!"

A solução de Monitoramento Remoto usa microsserviços que são criados usando imagens do docker que são extraídas de um hub do docker. 

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Editar e compilar um microsserviço na solução de Monitoramento Remoto
> * Compilar uma imagem do docker
> * Envie uma imagem do docker por push para o hub do docker
> * Efetuar pull na nova imagem do docker
> * Visualizar as alterações 

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, você precisa do seguinte:

>[!div class="checklist"]
> * [Implantar o acelerador da solução de monitoramento remoto localmente](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Uma conta do Docker](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) — Necessário para exibir a resposta de API

## <a name="call-the-api-and-view-response-status"></a>Chamar a API e exibir o status da resposta

Nesta parte, você deve chamar a API de microsserviço do gerenciador do hub IoT padrão. A API retorna uma mensagem de status que você altera posteriormente personalizando o microsserviço.

1. Verifique se a solução de Monitoramento Remoto está sendo executada localmente em seu computador.
2. Localize onde você baixou o Postman e abra-o.
3. No Postman, digite o seguinte no GET: `http://localhost:8080/iothubmanager/v1/status`.
4. Exiba o retorno e você deverá ver "Status": "OK: Ativo e em Funcionamento".

    ![Mensagem “Alive and Well” do Postman](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Altere o status e compile a imagem

Agora, altere a mensagem de status de microsserviço do Gerenciador do Hub IoT para "New Edits Made Here!" e, em seguida, recrie a imagem do docker com esse novo status. Se você tiver problemas aqui, consulte a nossa seção de [Solução de problemas](#Troubleshoot).

1. Verifique se o terminal está aberto e altere para o diretório onde você clonou a solução de Monitoramento Remoto. 
1. Altere o diretório para "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services".
1. Abra StatusService.cs em qualquer editor de texto ou IDE que desejar. 
1. Localize o código a seguir:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    e altere-o para o código abaixo e salve-o.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Volte para o terminal, mas agora, mude para o seguinte diretório: “azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker”.
6. Para compilar sua nova imagem do docker, digite

    ```sh
    sh build
    ```
    
    ou, no Windows:
    
    ```cmd
    ./build.cmd
    ```

7. Para verificar que a nova imagem foi criada com êxito, digite

    ```cmd/sh
    docker images 
    ```

O repositório deve ser "azureiotpcs/iothub-manager-dotnet".

![Imagem do docker com êxito](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Marcar e enviar imagem por push
Antes de você pode enviar a nova imagem do docker por push para um hub do docker, o Docker espera que suas imagens sejam marcadas. Se você tiver problemas aqui, consulte a nossa seção de [Solução de problemas](#Troubleshoot).

1. Localize a ID da Imagem do docker que você criou. Basta digitar:

    ```cmd/sh
    docker images
    ```

2. Para marcar a imagem com o tipo "teste", digite:

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Para enviar por push a imagem marcada recentemente para o hub do docker, digite:

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Abra seu navegador da Internet e acesse seu [hub do docker](https://hub.docker.com/) e, em seguida, entre.
5. Agora, você deverá ver sua imagem do docker recentemente enviada por push em seu hub do docker.
![Imagem do docker no hub do docker](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Atualize sua solução de Monitoramento Remoto
Agora, você precisa atualizar seu docker-compose.yml local para receber a nova imagem do docker do seu hub do docker. Se você tiver problemas aqui, consulte a nossa seção de [Solução de problemas](#Troubleshoot).

1. Volte para o terminal e mude para o seguinte diretório: “azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local”.
2. Abra docker-compose.yml em qualquer editor de texto ou o IDE que desejar.
3. Localize o código a seguir:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    e altere-o para se parecer com a imagem abaixo e, em seguida, salve-o.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Exibir o novo status de resposta
Finalize reimplantando uma instância local da solução de Monitoramento Remoto e exibindo a nova resposta de status no Postman.

1. Volte para o terminal e mude para o seguinte diretório: “azure-iot-pcs-remote-monitoring-dotnet/scripts/local”.
2. Inicie sua instância local da solução de Monitoramento Remoto. Basta digitar o seguinte comando no terminal:

    ```cmd/sh
    docker-compose up
    ```

3. Localize onde você baixou o Postman e abra-o.
4. No Postman, digite a seguinte solicitação no GET: `http://localhost:8080/iothubmanager/v1/status`. Agora você deverá ver "Status": "OK: Novas Edições Feitas Aqui!".

![Mensagem do Postman New Edits Made Here](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>Solucionar problemas

Se você estiver com problemas, tente remover as imagens do docker e os contêineres do computador local.

1. Para remover todos os contêineres, primeiro você precisará interromper todos os contêineres em execução. Abra o seu terminal e digite

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Para remover todas as imagens, abra o terminal e digite: 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Você pode verificar se há um contêiner no computador. Basta digitar:

    ```cmd/sh
    docker ps -aq 
    ```

    Se você tiver removido todos os contêineres, nada deve aparecer.

4. Você pode verificar se há imagens no computador. Basta digitar:

    ```cmd/sh
    docker images
    ```

    Se você tiver removido todos os contêineres, nada deve aparecer.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Editar e compilar um microsserviço na solução de Monitoramento Remoto
> * Compilar uma imagem do docker
> * Envie uma imagem do docker por push para o hub do docker
> * Efetuar pull na nova imagem do docker
> * Visualizar as alterações 

O próximo passo para se tentar é [como personalizar o microsserviço do simulador de dispositivo na solução de Monitoramento Remoto](iot-accelerators-microservices-example.md)

Para obter informações do desenvolvedor sobre a solução de Monitoramento Remoto, confira:

* [Guia de Referência do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

