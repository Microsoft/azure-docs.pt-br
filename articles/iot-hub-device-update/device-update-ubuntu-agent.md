---
title: Atualização de dispositivo para o tutorial do Hub IoT do Azure usando o agente de pacote Ubuntu Server 18.04 x64 | Microsoft Docs
description: Introdução à atualização de dispositivo para o Hub IoT do Azure usando o agente de pacote Ubuntu Server 18.04 x64.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 19be3b141fb663ea0f4f11d811bf6ffc33252504
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658658"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-server-1804-x64-package-agent"></a>Atualização de dispositivo para o tutorial do Hub IoT do Azure usando o agente de pacote Ubuntu Server 18.04 x64

A atualização de dispositivo para o Hub IoT é compatível com duas formas de atualizações – de acordo com a imagem e o pacote.

As atualizações baseadas em pacote são atualizações de destino que alteram apenas um componente ou aplicativo específico no dispositivo. Isso leva a um menor consumo de largura de banda e ajuda a reduzir o tempo para baixar e instalar a atualização. As atualizações de pacote geralmente permitem menos tempo de inatividade dos dispositivos ao aplicar uma atualização e evitam a sobrecarga de criação de imagens.

Este tutorial orienta você pelas etapas para concluir uma atualização de ponta a ponta baseada em pacote por meio da atualização de dispositivo para o Hub IoT. Usaremos um agente de pacote de exemplo para o Ubuntu Server 18.04 x64 para este tutorial. Mesmo se você planeja usar uma configuração de plataforma de sistema operacional diferente, este tutorial ainda é útil para aprender sobre as ferramentas e os conceitos na atualização de dispositivo para o Hub IoT. Conclua esta introdução a um processo de atualização de ponta a ponta e escolha a forma preferida de atualização e plataforma do sistema operacional, para aprofundar-se nos detalhes. Com este tutorial, você pode usar a atualização de dispositivo do Hub IoT para atualizar um dispositivo IoT ou Azure IoT Edge do Azure. 

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Configurar o repositório do pacote de atualização de dispositivo
> * Baixar e instalar o agente de atualização de dispositivo e suas dependências
> * Adicionar uma marca ao seu dispositivo IoT
> * Importar uma atualização
> * Criar um grupo de dispositivos
> * Implantar uma atualização de pacote
> * Monitorar a implantação de atualização

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* Acesso a um Hub IoT. Recomendamos usar uma camada S1 (Standard) ou posterior.
* Um dispositivo IoT ou Azure IoT Edge do Azure que executa o Ubuntu Server 18.04 x64, conectado ao Hub IoT.
   * Se você estiver usando um dispositivo Azure IoT Edge, verifique se ele está na v1.2.0 do tempo de execução do Edge ou superior 
* Se você não estiver usando um dispositivo Azure IoT Edge, [instale o pacote mais recente `aziot-identity-service` (versão prévia) em seu dispositivo IOT](https://github.com/Azure/iot-identity-service/actions/runs/575919358) 
* [Conta e instância de atualização de dispositivo vinculadas ao mesmo Hub IoT acima.](create-device-update-account.md)

## <a name="configure-device-update-package-repository"></a>Configurar o repositório do pacote de atualização de dispositivo

1. Instale a configuração do repositório que corresponde ao sistema operacional do seu dispositivo. Para este tutorial, este será o Ubuntu Server 18.04. 
   
   ```shell
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

2. Copie a lista gerada para o diretório sources.list.d.

   ```shell
      sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```
   
3. Instale a chave pública do Microsoft GPG.

   ```shell
      curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
      sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-device-update-deb-agent-packages"></a>Instalar os pacotes de agente .deb de atualização de dispositivo

1. Atualizar as listas de pacotes no dispositivo

   ```shell
      sudo apt-get update
   ```

2. Instalar o pacote deviceupdate-agent e suas dependências

   ```shell
      sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
   ```

A atualização de dispositivo para pacotes de software do Hub IoT do Azure está sujeita aos seguintes termos de licença:
   * [Licença de atualização de dispositivo para o Hub IoT](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licença de cliente de otimização de entrega](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Leia os termos da licença antes de usar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

## <a name="configure-device-update-agent-using-azure-iot-identity-service-preview"></a>Configurar o agente de atualização de dispositivo usando o serviço de Identidade do Azure IoT (versão prévia)

Depois de instalar os pacotes necessários, você precisa provisionar o dispositivo com suas informações de autenticação e identidade de nuvem.

1. Abrir o arquivo de configuração

   ```shell
      sudo nano /etc/aziot/config.toml
   ```

2. Localize a seção de configuração de provisionamento do arquivo. Remova a marca de comentário da seção "Provisionamento manual com a cadeia de conexão". Atualize o valor de connection_string com a cadeia de conexão do dispositivo IoT (Edge). Verifique se alguma outra seção de provisionamento foi comentada.


   ```toml
      # Manual provisioning configuration using a connection string
      [provisioning]
      source = "manual"
      iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
      device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
      dynamic_reprovisioning = false 
   ```

3. Salvar e fechar o arquivo usando Ctrl+X, Y

4. Aplicar a configuração. 

   Se você estiver usando um dispositivo IoT Edge, use o comando a seguir. 
   
   ```shell
      sudo iotedge config apply
   ```
   
   Se você estiver usando um dispositivo IoT, com o pacote `aziot-identity-service` instalado, use o comando a seguir. 
      
   ```shell
      sudo aziotctl config apply
   ```

5. Opcionalmente, você pode verificar se os serviços estão sendo executados pelo

    ```shell
       sudo systemctl list-units --type=service | grep 'adu-agent\.service\|deliveryoptimization-agent\.service'
    ```

    A saída deverá ser como a seguinte:

    ```markdown
       adu-agent.service                   loaded active running Device Update for IoT Hub Agent daemon.

       deliveryoptimization-agent.service               loaded active running deliveryoptimization-agent.service: Performs content delivery optimization tasks   `
    ```

## <a name="add-a-tag-to-your-device"></a>Adicionar uma marca ao seu dispositivo

1. Agora, entre no [portal do Azure](https://portal.azure.com) e navegue até Hub IoT.

2. Em 'Dispositivos IoT ' ou 'IoT Edge' no painel de navegação esquerdo, localize seu dispositivo IoT e navegue até ele.

3. No dispositivo gêmeo, exclua qualquer valor de marca de atualização de dispositivo existente definindo-os como null.

4. Adicione um novo valor de marca de atualização de dispositivo, conforme mostrado abaixo.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Importar atualização

1. Baixe o [arquivo de manifesto apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-apt-manifest.json) a seguir e [importe-o](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.1-importManifest.json). Esse manifesto apt instalará a versão mais recente disponível do `libcurl4-doc package` para seu dispositivo IOT. 

   Como alternativa, você pode baixar esse [arquivo de manifesto apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-7.58-apt-manifest.json) e [importá-lo](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-2-2.0.1-importManifest.json). Isso instalará a versão específica v7.58.0 do `libcurl4-doc package` para seu dispositivo IOT. 

2. No portal do Azure, selecione a opção Atualizações do Dispositivo em Gerenciamento de Dispositivo Automático na barra de navegação à esquerda no Hub IoT.

3. Selecione a guia Atualizações.

4. Selecione "+ Importar Nova Atualização".

5. Selecione o ícone de pasta ou caixa de texto em "Selecionar um arquivo de manifesto de importação". Você verá uma caixa de diálogo de seletor de arquivos. Selecione o Manifesto de Importação que você baixou anteriormente. Em seguida, selecione o ícone de pasta ou a caixa de texto em "Selecionar um ou mais arquivos de atualização". Você verá uma caixa de diálogo de seletor de arquivos. Selecione o arquivo de manifesto de importação que você baixou anteriormente.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Captura de tela mostrando a seleção do arquivo de atualização." lightbox="media/import-update/select-update-files.png":::

6. Selecione o ícone de pasta ou a caixa de texto em "Selecionar um contêiner de armazenamento". Em seguida, selecione a conta de armazenamento apropriada.

7. Se você já tiver criado um contêiner, poderá reutilizá-lo. (Caso contrário, selecione "+ Contêiner" para criar um novo contêiner de armazenamento para atualizações.).  Selecione o contêiner que você deseja usar e clique em "Selecionar".

   :::image type="content" source="media/import-update/container.png" alt-text="Captura de tela mostrando a seleção do contêiner." lightbox="media/import-update/container.png":::

8. Selecione "Enviar" para iniciar o processo de importação.

9. O processo de importação é iniciado e a tela é alterada para a seção "Importar Histórico". Selecione "Atualizar" para exibir o progresso até que o processo de importação seja concluído. Dependendo do tamanho da atualização, ela pode ser concluída em alguns minutos, mas pode levar mais tempo.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Captura de tela que mostra a sequência de importação de atualização." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quando a coluna Status indicar que a importação foi bem-sucedida, selecione o cabeçalho "Pronto para implantar". Você deve ver a atualização importada na lista agora.

[Saiba mais](import-update.md) sobre a importação de atualizações.

## <a name="create-update-group"></a>Criar grupo de atualizações

1. Vá para o Hub IoT que você conectou anteriormente à sua instância de Atualização do Dispositivo.

2. Selecione a opção Atualizações do Dispositivo em Gerenciamento de Dispositivo Automático na barra de navegação à esquerda.

3. Selecione a guia Grupos na parte superior da página. 

4. Clique no botão Adicionar para criar um novo grupo.

5. Selecione o Hub IoT que você criou na etapa anterior com a lista. Selecione Criar grupo de atualizações.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Captura de tela que mostra a seleção de marca." lightbox="media/create-update-group/select-tag.PNG":::

[Saiba mais](create-update-group.md) sobre como adicionar marcas e criar grupos de atualização


## <a name="deploy-update"></a>Implantar atualização

1. Depois que o grupo for criado, você verá uma nova atualização disponível para o grupo de dispositivos, juntamente com um link para a atualização em Atualizações Pendentes. Talvez seja necessário atualizar a página. 

2. Clique na atualização disponível.

3. Confirme se o grupo correto foi selecionado como o grupo de destino. Agende uma implantação e selecione Implantar atualização.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Selecione Atualizar" lightbox="media/deploy-update/select-update.png":::

4. Exiba um gráfico de conformidade. Será possível ver que a atualização está em andamento. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Atualização em andamento" lightbox="media/deploy-update/update-in-progress.png":::

5. Após atualizar seu dispositivo com êxito, será possível conferir o gráfico de conformidade e a atualização dos detalhes da implantação para refletir as mesmas informações. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Atualização realizada com êxito" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorar a implantação de atualizações

1. Selecione a guia Implantações na parte superior da página.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Guia de Implantações" lightbox="media/deploy-update/deployments-tab.png":::

2. Selecione a implantação criada para exibir os detalhes da implantação.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Detalhes de implantação" lightbox="media/deploy-update/deployment-details.png":::

3. Selecione Atualizar para exibir os detalhes do status mais recentes. Continue esse processo até que o status seja alterado para Realizado com êxito.

Agora você concluiu com êxito uma atualização de pacote de ponta a ponta usando a atualização do dispositivo para o Hub IoT em um dispositivo Ubuntu Server 18.04 x64. 

## <a name="bonus-steps"></a>Etapas de bônus

1. Baixe o [arquivo de manifesto apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-remove-apt-manifest.json) a seguir e [importe-o](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.2-importManifest.json). Esse manifesto de apt removerá o `libcurl4-doc package` instalado do seu dispositivo IoT. 

2. Repita as seções "Importar atualização" e "Implantar atualização"

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, limpe a conta de atualização de dispositivo, a instância, o Hub IoT e o dispositivo IoT. Você pode fazer isso acessando cada recurso individual e selecionando "Excluir". Observe que você precisa limpar uma instância de atualização de dispositivo antes de limpar a conta de atualização de dispositivo. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Atualização de imagem no tutorial Raspberry Pi 3 B+](device-update-raspberry-pi.md)

