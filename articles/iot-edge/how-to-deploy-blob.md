---
title: Implantar o armazenamento de BLOBs no módulo em seu dispositivo-Azure IoT Edge
description: Implante um módulo do Armazenamento de Blobs do Azure no dispositivo do IoT Edge para armazenar dados na borda.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: f766a77ee55351e498a379146826ba1d5507bc93
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201177"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Implantar o armazenamento de Blobs do Azure no módulo IoT Edge para seu dispositivo

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Há várias maneiras de implantar módulos em um dispositivo IoT Edge e todos eles funcionam para o armazenamento de BLOBs do Azure em módulos IoT Edge. Os dois métodos mais simples são utilizar o portal do Azure ou os modelos do Visual Studio Code.

## <a name="prerequisites"></a>Pré-requisitos

- Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure.
- Um dispositivo IoT Edge.

  Se você não tiver um dispositivo IoT Edge configurado, poderá criar um em uma máquina virtual do Azure. Siga as etapas em um dos artigos de início rápido para [criar um dispositivo Linux Virtual](quickstart-linux.md) ou [criar um dispositivo virtual do Windows](quickstart.md).

- [Visual Studio Code](https://code.visualstudio.com/) e as [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) se estiver implantando do Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Implantar do portal do Azure

O portal do Azure orienta você durante a criação de um manifesto de implantação e o envio por push da implantação para um dispositivo IoT Edge.

### <a name="select-your-device"></a>Selecionar o dispositivo

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
1. Selecionar **IoT Edge** do menu.
1. Clique no ID do dispositivo alvo da lista de dispositivos.
1. Selecione **definir módulos**.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. O portal do Azure tem um assistente que orienta você durante a criação de um manifesto de implantação. Ele tem três etapas organizadas em guias: **módulos**, **rotas** e **revisão + criar**.

#### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de IOT Edge** da página, clique na lista **suspensa adicionar** e selecione **IOT Edge módulo** para exibir a página **Adicionar módulo de IOT Edge** .

2. Na guia **configurações do módulo** , forneça um nome para o módulo e, em seguida, ESPECIFIQUE o URI da imagem de contêiner:

   Exemplos:
  
   - **Nome do módulo de IOT Edge**: `azureblobstorageoniotedge`
   - **URI da imagem**: `mcr.microsoft.com/azure-blob-storage:latest`

   ![Captura de tela mostra a guia Configurações do módulo da página do módulo adicionar I o T Edge.](./media/how-to-deploy-blob/addmodule-tab1.png)

   Não selecione **Adicionar** até que você tenha especificado valores nas guias **configurações do módulo**, opções de criação do **contêiner** e configurações do  **módulo de configuração** , conforme descrito neste procedimento.

   > [!IMPORTANT]
   > Azure IoT Edge diferencia maiúsculas de minúsculas quando você faz chamadas para módulos e o SDK de armazenamento também usa como padrão letras minúsculas. Embora o nome do módulo no [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) seja **AzureBlobStorageonIoTEdge**, alterar o nome para minúsculas ajuda a garantir que suas conexões com o armazenamento de BLOBs do Azure no módulo IOT Edge não sejam interrompidas.

3. Abra a guia **Opções de criação do contêiner** .

   ![Captura de tela mostra a guia Opções de criação do contêiner da página do módulo adicionar I o T Edge.](./media/how-to-deploy-blob/addmodule-tab3.png)

   Copie e cole o JSON a seguir na caixa, para fornecer informações da conta de armazenamento e uma montagem para o armazenamento em seu dispositivo.
  
   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

4. Atualize o JSON que você copiou em **Opções de criação de contêiner** com as seguintes informações:

   - Substitua `<your storage account name>` por um nome de que você possa se lembrar. Os nomes de conta devem ter entre 3 e 24 caracteres, com letras minúsculas e números. Não são permitidos espaços.

   - Substitua `<your storage account key>` por uma chave base64 de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos.

   - Substitua `<storage mount>` de acordo com o sistema operacional do contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório existente no dispositivo IOT Edge em que o módulo de blob armazenará seus dados. A montagem de armazenamento mapeia um local em seu dispositivo que você fornece a um local definido no módulo.

     - Para contêineres do Linux, o formato é **\<your storage path or volume> :/blobroot**. Por exemplo:
         - usar [montagem de volume](https://docs.docker.com/storage/volumes/): `my-volume:/blobroot`
         - usar [montagem de associação](https://docs.docker.com/storage/bind-mounts/): `/srv/containerdata:/blobroot` . Certifique-se de seguir as etapas para [conceder acesso ao diretório para o usuário do contêiner](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Para contêineres do Windows, o formato é **\<your storage path or volume> : C:/BlobRoot**. Por exemplo:
         - usar [montagem de volume](https://docs.docker.com/storage/volumes/): `my-volume:C:/BlobRoot` .
         - usar [montagem de associação](https://docs.docker.com/storage/bind-mounts/): `C:/ContainerData:C:/BlobRoot` .
         - Em vez de usar sua unidade local, você pode mapear seu local de rede SMB, para obter mais informações, consulte [usando o compartilhamento SMB como seu armazenamento local](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Não altere a segunda metade do valor de montagem de armazenamento, que aponta para um local específico no armazenamento de BLOBs no módulo IoT Edge. A montagem de armazenamento sempre deve terminar com **:/blobroot** para contêineres do Linux e **: C:/blobroot** para contêineres do Windows.

5. Na guia **configurações** de cópia do módulo, copie o JSON a seguir e cole-o na caixa.

   ![Captura de tela mostra a guia Configurações de configuração do módulo da página do módulo adicionar I o T Edge.](./media/how-to-deploy-blob/addmodule-tab4.png)

   Configure cada propriedade com um valor apropriado, conforme indicado pelos espaços reservados. Se você estiver usando o simulador de IoT Edge, defina os valores para as variáveis de ambiente relacionadas para essas propriedades, conforme descrito por [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
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
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   Para obter informações sobre como configurar o deviceToCloudUploadProperties e o deviceAutoDeleteProperties depois que o módulo tiver sido implantado, consulte [Editar o módulo](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)... Para obter mais informações sobre as propriedades desejadas, consulte [definir ou atualizar as propriedades desejadas](module-composition.md#define-or-update-desired-properties).

6. Selecione **Adicionar**.

7. Selecione **Avançar: rotas** para continuar na seção rotas.

#### <a name="specify-routes"></a>Especificar Rotas

Mantenha as rotas padrão e selecione **Avançar: revisar + criar** para continuar na seção revisão.

#### <a name="review-deployment"></a>Rever implantação

A seção de revisão mostra o manifesto de implantação JSON que foi criado baseado nas suas seleções nas duas seções anteriores. Também há dois módulos declarados que você não adicionou: **$edgeAgent** e **$edgeHub**. Estes dois módulos enganam o [runtime do IoT Edge](iot-edge-runtime.md) e são padrões requeridos em toda implantação.

Examine as informações de implantação e, em seguida, selecione **criar**.

### <a name="verify-your-deployment"></a>Verificar sua implantação

Depois de criar a implantação, você retorna para a página de **IOT Edge** do Hub IOT.

1. Selecione o dispositivo do IoT Edge ao qual você direcionou a implantação para abrir os detalhes.
1. Nos detalhes do dispositivo, verifique se o módulo de armazenamento de blob é listado como **Especificado na implantação** e **Relatado por dispositivo**.

Pode levar alguns instantes para que o módulo seja iniciado no dispositivo e, em seguida, seja relatado de volta para o Hub IoT. Atualize a página para ver o status atualizado.

## <a name="deploy-from-visual-studio-code"></a>Implantar do Visual Studio Code

O Azure IoT Edge disponibiliza modelos no Visual Studio Code para ajudar você a desenvolver soluções de borda. Use as etapas a seguir para criar uma nova solução de IoT Edge com um módulo de armazenamento de BLOBs e configurar o manifesto de implantação.

1. Selecione **Exibir** > **Paleta de Comandos**.

1. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**.

   ![Executar a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento para Visual Studio Code criar os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo Existente (Inserir a URL de imagem completa)**. |
   | Fornecer um nome de módulo | Insira um nome com todas as minúsculas para o módulo, como **azureblobstorageoniotedge**.<br/><br/>É importante usar um nome em minúsculas para o Armazenamento de Blobs do Azure no módulo do IoT Edge. O IoT Edge diferencia maiúsculas de minúsculas ao se referir a chamadas para módulos e o SDK de Armazenamento usa minúsculas por padrão. |
   | Fornecer a imagem do Docker para o módulo | Forneça o URI da imagem: **mcr.microsoft.com/azure-blob-storage:latest** |

   O Visual Studio Code usa as informações fornecidas, cria uma solução do IoT Edge e, em seguida, a carrega em uma nova janela. O modelo da solução cria um modelo do manifesto de implantação que inclui a imagem do módulo do armazenamento de blobs, mas é necessário configurar as opções de criação do módulo.

1. Abra *deployment.template.json* no workspace da nova solução e encontre a seção **módulos**. Faça as seguintes alterações de configuração:

   1. Exclua o módulo **SimulatedTemperatureSensor** , pois ele não é necessário para essa implantação.

   1. Copie e cole o seguinte código no `createOptions` campo:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Atualizar o módulo criaroptions-Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Substitua `<your storage account name>` por um nome de que você possa se lembrar. Os nomes de conta devem ter entre 3 e 24 caracteres, com letras minúsculas e números. Não são permitidos espaços.

1. Substitua `<your storage account key>` por uma chave base64 de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos.

1. Substitua `<storage mount>` de acordo com o sistema operacional do contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo do IoT Edge no qual você quer que o módulo do blob armazene os dados. A montagem de armazenamento mapeia um local em seu dispositivo que você fornece a um local definido no módulo.  

     - Para contêineres do Linux, o formato é **\<your storage path or volume> :/blobroot**. Por exemplo:
         - usar [montagem de volume](https://docs.docker.com/storage/volumes/): `my-volume:/blobroot`
         - usar [montagem de associação](https://docs.docker.com/storage/bind-mounts/): `/srv/containerdata:/blobroot` . Certifique-se de seguir as etapas para [conceder acesso ao diretório para o usuário do contêiner](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Para contêineres do Windows, o formato é **\<your storage path or volume> : C:/BlobRoot**. Por exemplo:
         - Usar [montagem de volume](https://docs.docker.com/storage/volumes/): `my-volume:C:/BlobRoot` .
         - Usar [montagem de associação](https://docs.docker.com/storage/bind-mounts/): `C:/ContainerData:C:/BlobRoot` .
         - Em vez de usar sua unidade local, você pode mapear o local de rede SMB. Para obter mais informações, consulte [usando o compartilhamento SMB como seu armazenamento local](how-to-store-data-blob.md#using-smb-share-as-your-local-storage).

     > [!IMPORTANT]
     > Não altere a segunda metade do valor de montagem de armazenamento, que aponta para um local específico no armazenamento de BLOBs no módulo IoT Edge. A montagem de armazenamento sempre deve terminar com **:/blobroot** para contêineres do Linux e **: C:/blobroot** para contêineres do Windows.

1. Configure o [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e o [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) para o módulo adicionando o JSON a seguir ao *deployment.template.jsno* arquivo. Configure cada propriedade com um valor apropriado e salve o arquivo. Se você estiver usando o simulador de IoT Edge, defina os valores para as variáveis de ambiente relacionadas para essas propriedades, que você pode encontrar na seção de explicação de [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

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

   ![definir as propriedades desejadas para azureblobstorageoniotedge-Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Para obter informações sobre como configurar o deviceToCloudUploadProperties e o deviceAutoDeleteProperties depois que o módulo tiver sido implantado, consulte [Editar o módulo](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)... Para obter mais informações sobre opções de criação de contêiner, política de reinicialização e status desejado, consulte [EdgeAgent Desired Properties](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Salve o arquivo *deployment.template.json*.

1. Clique com o botão direito do mouse em **deployment.template.json** e selecione **Gerar manifesto de implantação do IoT Edge**.

1. Visual Studio Code usa as informações fornecidas no *deployment.template.js* e as utiliza para criar um novo arquivo de manifesto de implantação. O manifesto de implantação é criado em uma nova pasta **config** no workspace da solução. Ao obter esse arquivo, você pode seguir as etapas em [Implantar módulos do Azure IoT Edge a partir do Visual Studio Code](how-to-deploy-modules-vscode.md) ou [Implantar módulos do Azure IoT Edge com a CLI 2.0 do Azure](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Implantar várias instâncias de módulo

Se desejar implantar várias instâncias do armazenamento de BLOBs do Azure no módulo IoT Edge, você precisará fornecer um caminho de armazenamento diferente e alterar o `HostPort` valor ao qual o módulo é associado. Os módulos do armazenamento de blobs sempre expõem a porta 11002 no contêiner, mas é possível declarar a qual porta ele está associado no host.

Edite **as opções de criação de contêiner** (no portal do Azure) **ou o campo** (na *deployment.template.js* no arquivo em Visual Studio Code) para alterar o `HostPort` valor:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Ao conectar-se a módulos de armazenamento de blobs adicionais, altere o ponto de extremidade para indicar a porta do host atualizada.

## <a name="configure-proxy-support"></a>Configurar suporte para proxy

Se sua organização estiver usando um servidor proxy, será necessário configurar o suporte de proxy para os módulos de tempo de execução edgeAgent e edgeHub. Esse processo envolve duas tarefas:

- Configure os daemons de tempo de execução e o agente de IoT Edge no dispositivo.
- Defina a variável de ambiente HTTPS_PROXY para módulos no arquivo JSON de manifesto de implantação.

Esse processo é descrito em [configurar um dispositivo de IOT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md).

Além disso, um módulo de armazenamento de BLOBs também requer a configuração HTTPS_PROXY no arquivo de implantação de manifesto. Você pode editar o arquivo de manifesto de implantação diretamente ou usar o portal do Azure.

1. Navegue até o Hub IOT no portal do Azure e selecione **IOT Edge** no menu do painel esquerdo.

1. Selecione o dispositivo com o módulo a ser configurado.

1. Selecione **definir módulos**.

1. Na seção **módulos de IOT Edge** da página, selecione o módulo armazenamento de BLOBs.

1. Na página **atualizar IOT Edge módulo** , selecione a guia **variáveis de ambiente** .

1. Adicione `HTTPS_PROXY` para o **nome** e a URL do proxy para o **valor**.

      ![Captura de tela mostra o painel do módulo atualizar I T Edge, no qual você pode inserir os valores especificados.](./media/how-to-deploy-blob/https-proxy-config.png)

1. Clique em **Atualizar** e **examine + criar**.

1. Observe que o proxy é adicionado ao módulo no manifesto de implantação e selecione **criar**.

1. Verifique a configuração selecionando o módulo na página de detalhes do dispositivo e, na parte inferior da página **detalhes dos módulos de IOT Edge** , selecione a guia **variáveis de ambiente** .

      ![Captura de tela mostra a guia variáveis de ambiente.](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [armazenamento de BLOBs do Azure em IOT Edge](how-to-store-data-blob.md).

Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).
