---
title: Armazenar blob de blocos nos dispositivos – Azure IoT Edge | Microsoft Docs
description: Implante um módulo do Armazenamento de Blobs do Azure no dispositivo do IoT Edge para armazenar dados na borda.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 03/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3a0df408e70ed61355ffba319f6261f90d8e4348
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595566"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Armazenar dados na borda com o Armazenamento de Blobs do Azure no IoT Edge (versão prévia)

O Armazenamento de Blob do Azure no IoT Edge fornece uma solução de armazenamento de [blob de blocos](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) na borda. Um módulo do armazenamento de blobs no dispositivo do IoT Edge comporta-se como um serviço de blobs de blocos do Azure, mas os blobs de blocos são armazenados localmente no dispositivo do IoT Edge. É possível acessar os blobs usando os mesmos métodos do SDK do armazenamento do Azure ou as chamadas à API do blob de blocos que você já está acostumado. 

Esse módulo é fornecido com **disposição em camadas automática** e **expiração automática** recursos.

> [!NOTE]
> Disposição em camadas automática e a funcionalidade de expiração automática atualmente só estão disponíveis no Linux AMD64 e Linux ARM32.

**Disposição em camadas automática** é uma funcionalidade configurável, que permite que você carregar automaticamente os dados de seu armazenamento de BLOBs do local para Azure com suporte à conectividade de internet intermitentes. Ele permite que você:
- Ativar ou desativar o recurso de disposição em camadas
- Escolha a ordem na qual os dados serão copiados para o Azure como NewestFirst ou OldestFirst
- Especifique a conta de armazenamento do Azure ao qual você deseja que os dados carregados.
- Especifique os contêineres que você deseja carregar no Azure. Este módulo permite que você especifique os nomes de contêiner de origem e destino.
- Completa a disposição em camadas de blob (usando `Put Blob` operação) e as camadas de nível de bloco (usando `Put Block` e `Put Block List` operações).

Este módulo usa o bloco nível disposição em camadas, quando seu blob consiste em blocos. Aqui estão alguns dos cenários comuns:
- Seu aplicativo atualiza alguns blocos de um blob carregado anteriormente, esse módulo será carregar apenas os blocos atualizados e não o blob inteiro.
- O módulo é carregar o blob e conexão de internet desaparece, quando a conectividade está de volta novamente que ele carregará somente os blocos restantes e não o blob inteiro.

Se ocorrer um encerramento inesperado do processo (como falha de energia) durante o carregamento de um blob, todos os blocos que foram devidos para o upload serão carregados novamente, quando o módulo fica online novamente.

**Expiração automática** é uma funcionalidade configurável em que esse módulo exclui automaticamente seus blobs do armazenamento local quando o tempo de vida (TTL) expirar. Ele é medido em minutos. Ele permite que você:
- Ativar ou desativar o recurso de expiração automática
- Especifique o TTL em minutos

Cenários onde os dados, como vídeos, imagens, dados financeiros, dados hospital ou todos os dados que precisam ser armazenados localmente, mais tarde que pode ser processados localmente ou transferidos para a nuvem são bons exemplos para usar este módulo.

Este artigo fornece instruções para implantar um Armazenamento de Blobs do Azure no contêiner do IoT Edge que executa um serviço blob no dispositivo do IoT Edge. 

>[!NOTE]
>O Armazenamento de Blobs do Azure no IoT Edge está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Assista ao vídeo para introdução rápida
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar seu computador de desenvolvimento ou uma máquina virtual como um dispositivo do Edge seguindo as etapas no início rápido para os [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md).
* O Armazenamento de Blobs do Azure no módulo do IoT Edge dá suporte às seguintes configurações de dispositivo:

   | Sistema operacional | Arquitetura |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Core (atualização de outubro) | AMD64 |
   | Windows 10 IoT Enterprise (atualização de outubro) | AMD64 |
   | Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão no Azure. 


## <a name="deploy-blob-storage-to-your-device"></a>Implantar um armazenamento de blobs no dispositivo

Existes várias maneiras de implantar módulos em um dispositivo do IoT Edge, sendo que todas elas funcionam para o Armazenamento de Blobs do Azure nos módulos do IoT Edge. Os dois métodos mais simples são utilizar o portal do Azure ou os modelos do Visual Studio Code. 

### <a name="azure-portal"></a>Portal do Azure

O Azure Marketplace fornece módulos do IoT Edge que podem ser implantados diretamente em seus dispositivos IoT Edge, incluindo no Armazenamento de Blobs do Azure no IoT Edge. Siga estas etapas para implantar o módulo do portal do Azure.

1. No [Portal do Azure](https://portal.azure.com), pesquise por “Armazenamento de Blobs do Azure no IoT Edge”. E **selecione** o resultado da pesquisa do Marketplace.

   ![Criar um módulo da pesquisa do Marketplace](./media/how-to-store-data-blob/marketplace-module.png)

2. Escolha um dispositivo IoT Edge para receber esse módulo. Na página **Dispositivos de Destino para o Módulo do IoT Edge**, forneça as seguintes informações:

   1. Selecione a **Assinatura** que contém o hub IoT que está sendo usado.

   2. Selecione seu **Hub IoT**.

   3. Se você souber o **Nome do Dispositivo do IoT Edge**, insira-o na caixa de texto. Ou selecione **Encontrar Dispositivo** para escolher um em uma lista de dispositivos IoT Edge no hub IoT. 
   
   4. Selecione **Criar**.

   Agora que escolheu um módulo do IoT Edge do Azure Marketplace e escolheu um dispositivo IoT Edge para receber o módulo, você será levado para um assistente de três etapas que ajudará você a definir exatamente como o módulo será implantado.

3. Na etapa **Adicionar Módulos** do assistente de definição de módulos, observe que o módulo **AzureBlobStorageonIoTEdge** já está listado em **Módulos de Implantação**. 

2. Selecione o módulo de armazenamento de blob na lista de módulos de implantação para abrir os detalhes do módulo. 

   ![Selecione o nome do módulo para abrir os detalhes do módulo](./media/how-to-store-data-blob/open-module-details.png)

3. Na página **Módulos personalizados do IoT Edge**, atualize o Armazenamento de Blobs do Azure no módulo do IoT Edge com as seguintes etapas:

   1. Altere o **Nome** do módulo para que ele fique em letras minúsculas. Você pode renomear o módulo se desejar, ou usar `azureblobstorageoniotedge`. 

      >[!IMPORTANT]
      >O Azure IoT Edge diferencia maiúsculas de minúsculas quando você faz chamadas para módulos e o SDK de Armazenamento usa minúsculas por padrão. Para garantir que suas conexões com o Armazenamento de Blobs do Azure no módulo do IoT Edge não sejam interrompidas, dê um nome com minúsculas. 

   2. As **Opções de Criação de Contêiner** padrão usam as associações de porta de que seu contêiner precisa, mas você também precisa adicionar informações de sua conta de armazenamento e uma associação para o diretório de armazenamento no dispositivo. Substitua o JSON no portal pelo JSON abaixo:
    
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
   3. Atualize o JSON que você copiou com as seguintes informações: 

      * Substitua `<your storage account name>` por um nome de que você possa se lembrar. Os nomes da conta devem ter de três a vinte quatro caracteres, com letras minúsculas e números.
      * Substitua `<your storage account key>` por uma chave base64 de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos.
      * Substitua `<storage directory bind>` dependendo do sistema operacional do contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo do IoT Edge no qual você quer que o módulo do blob armazene os dados. A associação do diretório de armazenamento mapeia um local em seu dispositivo que você fornece para um local definido no módulo. 

         * Contêineres do Linux: **\<storage path>:/blobroot**. Por exemplo, /srv/containerdata:/blobroot. Ou my-volume:/blobroot. 
         * Contêineres do Windows: **\<storage path>:C:/BlobRoot**. Por exemplo, C:/ContainerData:C:/BlobRoot. Ou my-volume:C:/blobroot.
   
      > [!IMPORTANT]
      > Não altere a segunda metade do valor da associação do diretório de armazenamento, que aponta para um local específico no módulo. A associação do diretório de armazenamento sempre deve terminar com **:/blobroot** para contêineres Linux e com **:C:/BlobRoot** para contêineres Windows.

      ![Atualizar as opções de criar contêiner do módulo - portal](./media/how-to-store-data-blob/edit-module.png)

   4. Definir [disposição em camadas automática e expiração automática](#configure-auto-tiering-and-auto-expiration-via-azure-portal) nas propriedades desejadas. Lista de [disposição em camadas automática](#auto-tiering-properties) e [expiração automática](#auto-expiration-properties) propriedades e seus valores possíveis. 

   5. Clique em **Salvar**. 

4. Selecione **Avançar** para continuar e ir para a próxima etapa do assistente.
5. Na etapa **Especificar Rotas** do assistente, selecione **Avançar**.
6. No assistente, na etapa **Revisar implantação**, selecione **Enviar**.
7. Depois de enviar a implantação, você volta para a página do **IoT Edge** de seu hub IoT. Selecione o dispositivo do IoT Edge ao qual você direcionou a implantação para abrir os detalhes. 
8. Nos detalhes do dispositivo, verifique se o módulo de armazenamento de blob é listado como **Especificado na implantação** e **Relatado por dispositivo**. Pode levar alguns instantes para que o módulo seja iniciado no dispositivo e, em seguida, seja relatado de volta para o Hub IoT. Atualize a página para ver o status atualizado. 

### <a name="visual-studio-code-templates"></a>Modelos do Visual Studio Code

O Azure IoT Edge disponibiliza modelos no Visual Studio Code para ajudar você a desenvolver soluções de borda. Essas etapas requerem que você tenha o [Visual Studio Code](https://code.visualstudio.com/) instalado no computador de desenvolvimento, além de estar configurado com as [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Execute as etapas a seguir para criar uma nova solução do IoT Edge com o módulo de armazenamento de blobs e, em seguida, configure o manifesto de implantação. 

1. Selecione **Exibir** > **Paleta de Comandos**. 

2. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Value |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo Existente (Inserir a URL de imagem completa)**. |
   | Fornecer um nome de módulo | Insira um nome totalmente em minúsculas para seu módulo, como **azureblobstorage**.<br><br>É importante usar um nome em minúsculas para o Armazenamento de Blobs do Azure no módulo do IoT Edge. O IoT Edge diferencia maiúsculas de minúsculas ao se referir a chamadas para módulos e o SDK de Armazenamento usa minúsculas por padrão. |
   | Fornecer a imagem do Docker para o módulo | Forneça o URI da imagem: **mcr.microsoft.com/azure-blob-storage:latest** |

   O VS Code usa as informações fornecidas, cria uma solução do IoT Edge e, em seguida, a carrega em uma nova janela. O modelo da solução cria um modelo do manifesto de implantação que inclui a imagem do módulo do armazenamento de blobs, mas é necessário configurar as opções de criação do módulo. 

3. Abra **deployment.template.json** no workspace da nova solução e encontre a seção **módulos**. Faça as seguintes alterações de configuração:

   1. Exclua o módulo **tempSensor**, pois ele é desnecessário para esta implantação. 

   2. Copie e cole o código a seguir no campo **createOptions** do módulo do armazenamento de blobs: 

      ```json
      "Env": [
        "LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME","LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Atualizar o módulo createOptions - VS Code](./media/how-to-store-data-blob/create-options.png)

4. No JSON das opções de criação, atualize `<storage directory bind>` dependendo do sistema operacional do seu contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo do IoT Edge no qual você quer que o módulo do blob armazene os dados. A associação do diretório de armazenamento mapeia um local em seu dispositivo que você fornece para um local definido no módulo.  

   * Contêineres do Linux: **\<storage path>:/blobroot**. Por exemplo, /srv/containerdata:/blobroot. Ou my-volume:/blobroot.
   * Contêineres do Windows: **\<storage path>:C:/BlobRoot**. Por exemplo, C:/ContainerData:C:/BlobRoot. Ou my-volume:C:/blobroot.
   
   > [!IMPORTANT]
   > Não altere a segunda metade do valor da associação do diretório de armazenamento, que aponta para um local específico no módulo. A associação do diretório de armazenamento sempre deve terminar com **:/blobroot** para contêineres Linux e com **:C:/BlobRoot** para contêineres Windows.

5. Configure [disposição em camadas automática e expiração automática](#configure-auto-tiering-and-auto-expiration-via-vscode). Lista de [disposição em camadas automática](#auto-tiering-properties) e [expiração automática](#auto-expiration-properties) propriedades

6. Salve o arquivo **deployment.template.json**.

7. Abra o arquivo **.env** no seu workspace da solução. 

8. O arquivo .env está configurado para receber credenciais de registro de contêiner, mas você não precisa disso para a imagem do armazenamento de blob, pois ele está disponível publicamente. Em vez disso, substitua o arquivo por duas novas variáveis de ambiente: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

9. Forneça um valor para `STORAGE_ACCOUNT_NAME`, os nomes da conta devem ter de três a vinte quatro caracteres, com letras minúsculas e números. Forneça uma chave de base64 de 64 bytes para o `STORAGE_ACCOUNT_KEY`. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos. 

   Não inclua espaços ou aspas ao redor dos valores que você fornecer. 

10. Salve o arquivo **.env**. 

11. Clique com o botão direito do mouse em **deployment.template.json** e selecione **Gerar manifesto de implantação do IoT Edge**. 

12. O Visual Studio Code coleta as informações fornecidas no deployment.template.json e .env e as usa para criar um novo arquivo de manifesto de implantação. O manifesto de implantação é criado em uma nova pasta **config** no workspace da solução. Ao obter esse arquivo, você pode seguir as etapas em [Implantar módulos do Azure IoT Edge a partir do Visual Studio Code](how-to-deploy-modules-vscode.md) ou [Implantar módulos do Azure IoT Edge com a CLI 2.0 do Azure](how-to-deploy-modules-cli.md).

## <a name="auto-tiering-and-auto-expiration-properties-and-configuration"></a>Disposição em camadas automática e propriedades de expiração automática e configuração

Use as propriedades desejadas para definir propriedades de expiração automática e a disposição em camadas automática. Eles podem ser definidos durante a implantação ou alterados mais tarde editando o gêmeo do módulo sem a necessidade de reimplantar. Recomendamos verificar "Gêmeo do módulo" para `reported configuration` e `configurationValidation` para garantir que os valores sejam propagados corretamente.

### <a name="auto-tiering-properties"></a>Propriedades de disposição em camadas automática 
O nome dessa configuração é `tieringSettings`

| Campo | Valores possíveis | Explicação |
| ----- | ----- | ---- |
| tieringOn | verdadeiro, falso | Por padrão, ele é definido como `false`, se você quiser ativá-lo na defini-lo como `true`|
| backlogPolicy | NewestFirst, OldestFirst | Permite que você escolha a ordem na qual os dados serão copiados para o Azure. Por padrão, ele é definido como `OldestFirst`. A ordem é determinada pela hora da última modificação do Blob |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` uma cadeia de caracteres de conexão que permite que você especifique a conta de armazenamento do Azure ao qual você deseja que seus dados é carregada. Especificar `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Adicionar apropriado EndpointSuffix do Azure onde os dados serão carregados, varia para Global do Azure, Azure governamental e Microsoft Azure Stack. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Permite que você especifique os nomes de contêiner que você deseja carregar no Azure. Este módulo permite que você especifique os nomes de contêiner de origem e destino. Se você não especificar o nome do contêiner de destino, ele atribuirá automaticamente o nome do contêiner como `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. Você pode criar cadeias de caracteres de modelo para o nome do contêiner de destino, a coluna de valores possíveis de check-out. <br>* %h -> nome do Hub IoT (3 a 50 caracteres). <br>* %d -> Id (1 a 129 caracteres) do dispositivo IoT. <br>* %m -> nome do módulo (1 a 64 caracteres). <br>* %c -> nome do contêiner de origem (3 a 63 caracteres). <br><br>Tamanho máximo do nome do contêiner é 63 caracteres, enquanto atribuir automaticamente o nome do contêiner de destino se o tamanho do contêiner exceder 63 caracteres, que ele será cortar cada seção (IoTHubName, IotEdgeDeviceName, ModuleName, ContainerName) a 15 caracteres. |

### <a name="auto-expiration-properties"></a>Propriedades de expiração automática
O nome dessa configuração é `ttlSettings`

| Campo | Valores possíveis | Explicação |
| ----- | ----- | ---- |
| ttlOn | verdadeiro, falso | Por padrão, ele é definido como `false`, se você quiser ativá-lo na defini-lo como `true`|
| timeToLiveInMinutes | `<minutes>` | Especifique o TTL em minutos. O módulo excluirá automaticamente seus blobs do armazenamento local quando o TTL expire |

### <a name="configure-auto-tiering-and-auto-expiration-via-azure-portal"></a>Configurar a disposição em camadas automática e expiração automática por meio do portal do Azure

Defina as propriedades desejadas para habilitar a disposição em camadas automática e expiração automática, você pode definir esses valores:

- **Durante a implantação inicial**: Copie o JSON na **propriedades desejadas do gêmeo do módulo de conjunto** caixa. Configurar cada propriedade com o valor apropriado, salvá-lo e continuar com a implantação.

   ```json
   {
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

  ![definir as propriedades de disposição em camadas automática e expiração automática](./media/how-to-store-data-blob/iotedge_custom_module.png)

- **Depois que o módulo é implantado por meio do recurso de "Identidade de módulo gêmeo"**: Vá para "Módulo gêmeo de identidade" deste módulo, copie o JSON nas propriedades desejadas, configurar cada propriedade com o valor apropriado e salve. Em "Identidade de módulo gêmeo" Json, certifique-se de sempre que você adiciona ou atualiza qualquer desejado propriedade, o `reported configuration` seção reflete as alterações e o `configurationValidation` seção será relatada com êxito para cada propriedade.

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```

![tiering+ttl module_identity_twin](./media/how-to-store-data-blob/module_identity_twin.png) 

### <a name="configure-auto-tiering-and-auto-expiration-via-vscode"></a>Configurar a disposição em camadas automática e expiração automática por meio do VSCode

- **Durante a implantação inicial**: Adicione o JSON em seu deployment.template.json para definir as propriedades desejadas para esse módulo abaixo. Configure cada propriedade com o valor apropriado e salvá-lo.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

Aqui está um exemplo das propriedades desejadas para esse módulo: ![definir as propriedades desejadas para azureblobstorageoniotedge - VS Code](./media/how-to-store-data-blob/tiering_ttl.png)

- **Depois que o módulo é implantado por meio de "Módulo gêmeo"**: [Editar módulo gêmeo](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin) deste módulo, copie o JSON nas propriedades desejadas, configure cada propriedade com o valor apropriado e salvar. Em Json de "Módulo gêmeo" Certifique-se de sempre que você adiciona ou atualiza qualquer desejado de propriedade, o `reported configuration` seção reflete as alterações e o `configurationValidation` seção será relatada com êxito para cada propriedade.

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```
## <a name="logs"></a>Logs

Siga as instruções para [configurar seus logs de docker para os módulos do IoT Edge](production-checklist.md#set-up-logs-and-diagnostics)

## <a name="connect-to-your-blob-storage-module"></a>Conectar ao módulo do armazenamento de blobs

É possível usar o nome da conta e a chave de conta que você configurou para o módulo para acessar o armazenamento de blobs no seu dispositivo do IoT Edge. 

Especifique o dispositivo do IoT Edge como ponto de extremidade do blob para quaisquer solicitações de armazenamento que você faz para esse dispositivo. É possível [Criar uma cadeia de conexão para um ponto de extremidade explícito](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando as informações do dispositivo do IoT Edge e do nome da conta que você configurou. 

1. Para módulos, que são implantados no mesmo dispositivo de borda em que "Azure Blob Storage no IoT Edge" está em execução, o ponto de extremidade do blob é: `http://<module name>:11002/<account name>`. 
2. Para módulos, que são implantados no dispositivo de borda diferente, que o dispositivo de borda em que "Azure Blob Storage no IoT Edge" está em execução e, em seguida, dependendo de sua configuração de ponto de extremidade do blob é: `http://<device IP >:11002/<account name>` ou `http://<IoT Edge device hostname>:11002/<account name>` ou `http://<FQDN>:11002/<account name>`

## <a name="deploy-multiple-instances"></a>Implantar várias instâncias

Se você quiser implantar várias instâncias do armazenamento de BLOBs do Azure no IoT Edge, você precisa fornecer o caminho de armazenamento diferentes e alterar o HostPort que o módulo associa ao. Os módulos do armazenamento de blobs sempre expõem a porta 11002 no contêiner, mas é possível declarar a qual porta ele está associado no host. 

Editar as opções de criação do módulo para alterar o valor da HostPort:

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

Ao conectar-se a módulos de armazenamento de blobs adicionais, altere o ponto de extremidade para indicar a porta do host atualizada. 

## <a name="try-it-out"></a>Experimentar

### <a name="azure-blob-storage-quickstart-samples"></a>Exemplos de início rápido de armazenamento de BLOBs do Azure
A documentação do Armazenamento de Blobs do Azure inclui guias de início rápido que fornecem códigos de exemplo em várias linguagens. É possível executar esses exemplos para testar o Armazenamento de Blobs do Azure no IoT Edge alterando o ponto de extremidade do blob para o módulo do armazenamento de blobs. Siga as etapas para [se conectar ao seu módulo de armazenamento de blob](#connect-to-your-blob-storage-module)

As seguintes guias de início rápido também contam com suporte do IoT Edge, assim é possível implantá-las como módulos do IoT Edge juntamente com o módulo do armazenamento de blobs:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md) 

### <a name="azure-storage-explorer"></a>Gerenciador de Armazenamento do Azure
Você também pode tentar "Gerenciador de armazenamento do Azure" para se conectar à sua conta de armazenamento local. Ele funciona com [Gerenciador de armazenamento do Azure versão 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0).

> [!NOTE]
> Você pode encontrar erros ao executar as etapas a seguir, como adicionar uma conexão a uma conta de armazenamento local, ou criar contêineres na conta de armazenamento local. Ignore e atualizar. 

1. Baixe e instale o Gerenciador de armazenamento do Azure
2. Conectar ao armazenamento do Azure usando uma cadeia de caracteres de conexão
3. Forneça a cadeia de caracteres de conexão: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`
4. Percorra as etapas para se conectar.
5. Criar contêiner dentro de sua conta de armazenamento local
6. Inicie o upload de arquivos como blobs de blocos.
   > [!NOTE]
   > Desmarque a caixa de seleção para carregá-lo como blobs de página. Esse módulo não oferece suporte a blobs de página. Você obterá esse prompt durante o carregamento de arquivos, como o. ISO, arquivos. vhd,. vhdx ou qualquer grandes.

7. Você pode optar por conectar suas contas de armazenamento do Azure em que você está carregando os dados. Ele oferece uma exibição única para sua conta de armazenamento local e a conta de armazenamento do Azure

## <a name="supported-storage-operations"></a>Operações de armazenamento com suporte

Módulos de armazenamento de blob no IoT Edge usam os mesmos SDKs de armazenamento do Azure e são consistentes com a versão 2017-04-17 da API de armazenamento do Azure para pontos de extremidade de blob de bloco. Versões posteriores são dependentes das necessidades do cliente.

Nem todas as operações do Armazenamento de Blobs do Azure têm suporte do Armazenamento de Blobs do Azure no IoT Edge. A seção a seguir lista as operações compatíveis e sem suportadas.

### <a name="account"></a>Conta

Com suporte: 
* Listar contêineres

Sem suporte: 
* Obter e definir propriedades do serviço Blob
* Solicitação de blob de simulação
* Obter estatísticas do serviço Blob
* Obter Informações da conta

### <a name="containers"></a>Contêineres

Com suporte: 
* Criar e excluir contêiner
* Obter Propriedades do Contêiner
* Listar blobs
* Obter e definir ACL do contêiner
* Definir Metadados do Contêiner

Sem suporte:
* Concessão de contêiner

### <a name="blobs"></a>Blobs

Com suporte: 
* Colocar, obter e excluir blob
* Obter e definir propriedades do blob
* Obter e definir Metadados do Blob

Sem suporte: 
* Concessão de blob
* Instantâneo do blob
* Copiar e cancelar cópia do blob
* Cancelar exclusão do blob
* Definir camada do blob

### <a name="block-blobs"></a>Blobs de bloco

Com suporte: 
* Colocar bloco
* Colocar e obter lista de blocos

Sem suporte:
* Colocar bloco pela URL

## <a name="feedback"></a>Comentários:
Seus comentários são muito importantes para nós, para tornar esse módulo e seus recursos úteis e fácil de usar. Compartilhe seus comentários e informe-nos como podemos melhorar.

Você pode em contato conosco em absiotfeedback@microsoft.com 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md)

