---
title: Implantar o módulo de armazenamento de BLOBs do Azure para dispositivos – Azure IoT Edge | Microsoft Docs
description: Implante um módulo do Armazenamento de Blobs do Azure no dispositivo do IoT Edge para armazenar dados na borda.
author: arduppal
ms.author: arduppal
ms.date: 06/19/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: 468e4fca5e67850949e7d5826e4bc88fa504b9d6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295241"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Implantar o armazenamento de BLOBs do Azure no módulo do IoT Edge no dispositivo

Há várias maneiras para implantar módulos em um dispositivo IoT Edge e todos eles funcionam para o armazenamento de BLOBs do Azure nos módulos do IoT Edge. Os dois métodos mais simples são utilizar o portal do Azure ou os modelos do Visual Studio Code.

> [!NOTE]
> O Armazenamento de Blobs do Azure no IoT Edge está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

- Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure.
- Um [Dispositivo do IoT Edge](how-to-register-device-portal.md) com o tempo de execução do IoT Edge instalado.
- [Visual Studio Code](https://code.visualstudio.com/) e o [ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) se a implantação do Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Implantar do portal do Azure

O portal do Azure orienta você pela criação de um manifesto de implantação e enviar por push a implantação em um dispositivo IoT Edge.

### <a name="select-your-device"></a>Selecionar o dispositivo

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
1. Selecionar **IoT Edge** do menu.
1. Clique no ID do dispositivo alvo da lista de dispositivos.
1. Selecione **Definir Módulos**.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. O portal do Azure tem um assistente que orienta você pela criação de um manifesto de implantação, em vez de criar o documento JSON manualmente. Ele tem três etapas: **Adicionar módulos**, **Especificar rotas**, e **Rever implantação**.

#### <a name="add-modules"></a>Adicionar módulos

1. Na seção **Módulos de Implantação** da página, selecione **Adicionar**.

1. A partir dos tipos de módulos na lista suspensa, selecione **módulo do IoT Edge**.

1. Forneça um nome para o módulo e, em seguida, especifique a imagem de contêiner:

   - **Name** - azureblobstorageoniotedge
   - **URI da imagem** -mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > O Azure IoT Edge diferencia maiusculas de minúsculas, ao fazer chamadas para módulos e o SDK de armazenamento também padroniza para minúsculas. Embora o nome do módulo na [do Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) é **AzureBlobStorageonIoTEdge**, alterando o nome em minúsculas ajuda a garantir que suas conexões ao armazenamento de BLOBs do Azure no módulo do IoT Edge não sejam interrompidas.

1. O padrão **opções de criar contêiner** valores definem as associações de porta que seu contêiner precisar, mas você também precisa adicionar informações de sua conta de armazenamento e uma ligação para o diretório de armazenamento em seu dispositivo. Substitua o padrão JSON no portal com o JSON abaixo:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage directory bind>"
       ],
     "PortBindings":{
       "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Atualize o JSON que você copiou com as seguintes informações:

   - Substitua `<your storage account name>` por um nome de que você possa se lembrar. Nomes de conta devem ser de 3 a 24 caracteres com letras minúsculas e números. Sem espaços.

   - Substitua `<your storage account key>` por uma chave base64 de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos.

   - Substitua `<storage directory bind>` acordo com seu sistema de operacional do contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo do IoT Edge no qual você quer que o módulo do blob armazene os dados. A associação do diretório de armazenamento mapeia um local em seu dispositivo que você fornece para um local definido no módulo.

     - Contêineres do Linux, o formato é  *\<caminho de armazenamento >: / blobroot*. Por exemplo, **/srv/containerdata: / blobroot** ou **Meu volume: / blobroot**.
     - Contêineres do Windows, o formato é  *\<caminho de armazenamento >: C: / BlobRoot*. Por exemplo, **c: / ContainerData:C: / BlobRoot** ou **Meu-volume: C: / blobroot**.

     > [!IMPORTANT]
     > Não altere a segunda metade do valor da associação do diretório de armazenamento, que aponta para um local específico no módulo. A associação do diretório de armazenamento sempre deve terminar com **:/blobroot** para contêineres Linux e com **:C:/BlobRoot** para contêineres Windows.

1. Definir [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) as propriedades para o seu módulo, copiando o JSON a seguir e colando-o no **desejadas do gêmeo do módulo de conjunto propriedades** caixa. Configurar cada propriedade com um valor apropriado, salvá-lo e continuar com a implantação.

   ```json
   {
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading":<true,false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload":<true,false>
       }
     }
   }

      ```

   ![contêiner do conjunto criar propriedades de opções, deviceAutoDeleteProperties e deviceToCloudUploadProperties](./media/how-to-deploy-blob/iotedge-custom-module.png)

   Para obter informações sobre como configurar o deviceToCloudUploadProperties e deviceAutoDeleteProperties após a implantação de seu módulo, consulte [Editar módulo gêmeo](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Para obter mais informações sobre as propriedades desejadas, consulte [definir ou atualizar propriedades de desejadas](module-composition.md#define-or-update-desired-properties).

1. Clique em **Salvar**.

1. Selecione **Avançar** para continuar para a seção de rotas.

#### <a name="specify-routes"></a>Especificar Rotas

Manter as rotas padrão e, em seguida, selecione **próxima** para continuar para a seção de revisão.

#### <a name="review-deployment"></a>Rever implantação

A seção de revisão mostra o manifesto de implantação JSON que foi criado baseado nas suas seleções nas duas seções anteriores. Também há dois módulos declarado que você não adicionou: **$edgeAgent** e **$edgeHub**. Estes dois módulos enganam o [tempo de execução IoT Edge](iot-edge-runtime.md) e são padrões requeridos em toda implantação.

Examine as informações da implantação e, em seguida, selecione **Enviar**.

### <a name="verify-your-deployment"></a>Verifique se sua implantação

Depois de enviar a implantação, você volta para a página do **IoT Edge** de seu hub IoT.

1. Selecione o dispositivo do IoT Edge ao qual você direcionou a implantação para abrir os detalhes.
1. Nos detalhes do dispositivo, verifique se o módulo de armazenamento de blob é listado como **Especificado na implantação** e **Relatado por dispositivo**.

Pode levar alguns instantes para que o módulo seja iniciado no dispositivo e, em seguida, seja relatado de volta para o Hub IoT. Atualize a página para ver o status atualizado.

## <a name="deploy-from-visual-studio-code"></a>Implantar do Visual Studio Code

O Azure IoT Edge disponibiliza modelos no Visual Studio Code para ajudar você a desenvolver soluções de borda. Use as seguintes etapas para criar uma nova solução de IoT Edge com um módulo de armazenamento de blob e configurar o manifesto de implantação.

1. Selecione **Exibir** > **Paleta de Comandos**.

1. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**.

   ![Executar a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Value |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento para Visual Studio Code criar os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo Existente (Inserir a URL de imagem completa)** . |
   | Fornecer um nome de módulo | Insira um nome totalmente em minúsculas para o seu módulo, como **azureblobstorageoniotedge**.<br /><br />É importante usar um nome em minúsculas para o Armazenamento de Blobs do Azure no módulo do IoT Edge. O IoT Edge diferencia maiúsculas de minúsculas ao se referir a chamadas para módulos e o SDK de Armazenamento usa minúsculas por padrão. |
   | Fornecer a imagem do Docker para o módulo | Forneça o URI da imagem: **mcr.microsoft.com/azure-blob-storage:latest** |

   O Visual Studio Code usa as informações fornecidas, cria uma solução do IoT Edge e, em seguida, a carrega em uma nova janela. O modelo da solução cria um modelo do manifesto de implantação que inclui a imagem do módulo do armazenamento de blobs, mas é necessário configurar as opções de criação do módulo.

1. Abra *deployment.template.json* no workspace da nova solução e encontre a seção **módulos**. Faça as seguintes alterações de configuração:

   1. Exclua o módulo **tempSensor**, pois ele é desnecessário para esta implantação.

   1. Copie e cole o seguinte código para o `createOptions` campo:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Atualizar o módulo createOptions - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Substitua `<your storage account name>` por um nome de que você possa se lembrar. Nomes de conta devem ser de 3 a 24 caracteres com letras minúsculas e números. Sem espaços.

1. Substitua `<your storage account key>` por uma chave base64 de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos.

1. Substitua `<storage directory bind>` acordo com seu sistema de operacional do contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo do IoT Edge no qual você quer que o módulo do blob armazene os dados. A associação do diretório de armazenamento mapeia um local em seu dispositivo que você fornece para um local definido no módulo.  

      - Contêineres do Linux, o formato é  *\<caminho de armazenamento >: / blobroot*. Por exemplo, **/srv/containerdata: / blobroot** ou **Meu volume: / blobroot**.
      - Contêineres do Windows, o formato é  *\<caminho de armazenamento >: C: / BlobRoot*. Por exemplo, **c: / ContainerData:C: / BlobRoot** ou **Meu-volume: C: / blobroot**.

      > [!IMPORTANT]
      > Não altere a segunda metade do valor da associação do diretório de armazenamento, que aponta para um local específico no módulo. A associação do diretório de armazenamento sempre deve terminar com **:/blobroot** para contêineres Linux e com **:C:/BlobRoot** para contêineres Windows.

1. Configure [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) para o seu módulo, adicionando o seguinte JSON para o *deployment.template.json* arquivo. Configure cada propriedade com um valor apropriado e salve o arquivo.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![definir as propriedades desejadas para azureblobstorageoniotedge - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Para obter informações sobre como configurar o deviceToCloudUploadProperties e deviceAutoDeleteProperties após a implantação de seu módulo, consulte [Editar módulo gêmeo](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Para obter mais informações sobre o contêiner de opções de criação, reinicie a política e o status desejado, consulte [propriedades desejadas do EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Salve o arquivo *deployment.template.json*.

1. Clique com o botão direito do mouse em **deployment.template.json** e selecione **Gerar manifesto de implantação do IoT Edge**.

1. Visual Studio Code usa as informações que você forneceu na *deployment.template.json* e o utiliza para criar um novo arquivo de manifesto de implantação. O manifesto de implantação é criado em uma nova pasta **config** no workspace da solução. Ao obter esse arquivo, você pode seguir as etapas em [Implantar módulos do Azure IoT Edge a partir do Visual Studio Code](how-to-deploy-modules-vscode.md) ou [Implantar módulos do Azure IoT Edge com a CLI 2.0 do Azure](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Implantar várias instâncias de módulo

Se você quiser implantar várias instâncias do armazenamento de Blob do Azure no módulo do IoT Edge, você precisa fornecer um caminho de armazenamento diferentes e alterar o `HostPort` que o módulo associa ao valor. Os módulos do armazenamento de blobs sempre expõem a porta 11002 no contêiner, mas é possível declarar a qual porta ele está associado no host.

Edite **opções de criar contêiner** (no portal do Azure) ou o **createOptions** campo (no *deployment.template.json* arquivo no Visual Studio Code) para alterar o `HostPort` valor:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Ao conectar-se a módulos de armazenamento de blobs adicionais, altere o ponto de extremidade para indicar a porta do host atualizada.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).
