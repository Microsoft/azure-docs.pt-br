---
title: 'Tutorial: Usar a Grade de Eventos do Azure para automatizar o redimensionamento de imagens carregadas'
description: 'Tutorial: A Grade de Eventos do Azure pode ser disparada em carregamentos de blob no Armazenamento do Azure. Você pode usar isso para enviar os arquivos de imagem carregados no Armazenamento do Azure para outros serviços, como o Azure Functions, para redimensionamento e outras melhorias.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.openlocfilehash: ca231fc65162fe38f4dcb8b8d5677ef42c7807bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550483"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Tutorial: Automatizar o redimensionamento de imagens carregadas usando a Grade de Eventos

A [Grade de Eventos do Azure](overview.md) é um serviço de eventos para a nuvem. A Grade de Eventos permite criar assinaturas em eventos gerados pelos serviços do Azure ou por recursos de terceiros.  

Este tutorial é a segunda parte de uma série de tutoriais sobre o Armazenamento. Ele estende o [tutorial anterior sobre o Armazenamento][previous-tutorial] para adicionar a geração automática de miniaturas sem servidor usando a Grade de Eventos do Azure e o Azure Functions. A Grade de Eventos permite que o [Azure Functions](../azure-functions/functions-overview.md) responda aos eventos do [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md) e gere miniaturas das imagens carregadas. Uma assinatura de evento é criada no evento de criação do armazenamento de Blobs. Quando um blob é adicionado a um contêiner de armazenamento de Blobs específico, um ponto de extremidade de função é chamado. Os dados passados para a associação de função da Grade de Eventos são usados para acessar o blob e gerar a imagem em miniatura.

Use a CLI do Azure e o portal do Azure para adicionar a funcionalidade de redimensionamento a um aplicativo de upload de imagens existente.

# <a name="net-v12-sdk"></a>[\.SDK do NET v12](#tab/dotnet)

![Captura de tela que mostra um aplicativo Web publicado em um navegador para o SDK \.NET v12.](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[SDK do Node.js V10](#tab/nodejsv10)

![Captura de tela que mostra um aplicativo Web publicado em um navegador para o SDK \.NET v10.](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta de Armazenamento do Azure
> * Implantar o código sem servidor usando o Azure Functions
> * Criar uma assinatura de evento do armazenamento de Blobs na Grade de Eventos

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial:

É necessário concluir o tutorial de Armazenamento de Blobs anterior: [Carregue os dados de imagem na nuvem com o Armazenamento do Azure][previous-tutorial].

É necessária uma [assinatura do Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing). Este tutorial não funciona com a assinatura **gratuita**. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este tutorial exigirá a CLI do Azure versão 2.0.14 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Se você não estiver usando o Cloud Shell, primeiro você deve entrar usando `az login`.

Se você não tiver registrado o provedor de recursos da Grade de Eventos em sua assinatura, verifique se que ele está registrado.

```bash
az provider register --namespace Microsoft.EventGrid
```

```powershell
az provider register --namespace Microsoft.EventGrid
```

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

O Azure Functions exige uma conta de armazenamento geral. Além da conta de Armazenamento de Blobs que você criou no tutorial anterior, crie uma conta de armazenamento geral separada no grupo de recursos usando o comando [az storage account create](/cli/azure/storage/account). Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.

1. Defina uma variável para conter o nome do grupo de recursos que você criou no tutorial anterior.

    ```bash
    resourceGroupName="myResourceGroup"
    ```

    ```powershell
    $resourceGroupName="myResourceGroup"
    ```

1. Defina uma variável para armazenar a localização dos recursos a serem criados. 

    ```bash
    location="eastus"
    ```

    ```powershell
    $location="eastus"
    ```

1. Defina uma variável para o nome da nova conta de armazenamento necessária para o Azure Functions.

    ```bash
    functionstorage="<name of the storage account to be used by the function>"
    ```

    ```powershell
    $functionstorage="<name of the storage account to be used by the function>"
    ```

1. Crie a conta de armazenamento para a função do Azure.

    ```bash
    az storage account create --name $functionstorage --location $location \
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

    ```powershell
    az storage account create --name $functionstorage --location $location `
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

## <a name="create-a-function-app"></a>Criar um aplicativo de funções  

Você deve ter um aplicativo de funções para hospedar a execução da função. O aplicativo de funções fornece um ambiente para execução sem servidor do seu código de função. Crie um aplicativo de funções ao usar o comando [az functionapp create](/cli/azure/functionapp).

No comando a seguir, forneça seu próprio nome exclusivo do aplicativo de funções. O nome do aplicativo de funções é usado como domínio DNS padrão para o aplicativo de funções, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure.

1. Especifique um nome para o aplicativo de funções será criado.

    ```bash
    functionapp="<name of the function app>"
    ```

    ```powershell
    $functionapp="<name of the function app>"
    ```

1. Crie a função do Azure.

    ```bash
    az functionapp create --name $functionapp --storage-account $functionstorage \
      --resource-group $resourceGroupName --consumption-plan-location $location \
      --functions-version 2
    ```

    ```powershell
    az functionapp create --name $functionapp --storage-account $functionstorage `
      --resource-group $resourceGroupName --consumption-plan-location $location `
      --functions-version 2
    ```

Agora, configure o aplicativo de funções para se conectar à conta de armazenamento de blobs criada no [tutorial anterior][previous-tutorial].

## <a name="configure-the-function-app"></a>Configurar o aplicativo de funções

A função precisa ter credenciais para a conta de Armazenamento de Blobs, que são adicionadas às configurações do aplicativo de funções usando o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

# <a name="net-v12-sdk"></a>[\.SDK do NET v12](#tab/dotnet)

```bash
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

```powershell
$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails `
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdk"></a>[SDK do Node.js V10](#tab/nodejsv10)

```bash
blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
  -n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName `
  -n $blobStorageAccount --query [0].value --output tsv)

$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails `
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey `
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

A configuração `FUNCTIONS_EXTENSION_VERSION=~2` faz com que o aplicativo de funções execute na versão 2.x do Azure Functions Runtime.

Agora você pode implantar um projeto de código de função nesse aplicativo de funções.

## <a name="deploy-the-function-code"></a>Implantar o código de função 

# <a name="net-v12-sdk"></a>[\.SDK do NET v12](#tab/dotnet)

A função de redimensionamento do C# de exemplo está disponível no [GitHub](https://github.com/Azure-Samples/function-image-upload-resize). Implante esse projeto de código no aplicativo de funções usando o comando [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```bash
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

```powershell
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v10-sdk"></a>[SDK do Node.js V10](#tab/nodejsv10)

A função de redimensionamento do exemplo Node.js está disponível no [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10). Implante esse projeto de código do Functions no aplicativo de funções usando o comando [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```bash
az functionapp deployment source config --name $functionapp \
  --resource-group $resourceGroupName --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

```powershell
az functionapp deployment source config --name $functionapp `
  --resource-group $resourceGroupName --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

---

A função de redimensionamento de imagem é disparada por solicitações HTTP enviadas a ele a partir do serviço de Grade de Eventos. Você informa à Grade de Eventos que você deseja obter essas notificações na URL da função criando uma assinatura de evento. Neste tutorial, você assina eventos criados de blob.

Os dados passados para a função da notificação de Grade de Eventos incluem a URL do blob. Essa URL, por sua vez, é passada para a associação de entrada para obter a imagem carregada a partir do armazenamento de Blob. A função gera uma imagem em miniatura e grava o fluxo resultante em um contêiner separado no armazenamento de Blobs.

Este projeto usa `EventGridTrigger` para o tipo de gatilho. O uso do gatilho de Grade de Eventos é recomendado em gatilhos HTTP genéricos. A Grade de Eventos valida automaticamente os gatilhos de Função da Grade de Eventos. Com os gatilhos HTTP genéricos, você deve implementar a [resposta de validação](security-authentication.md).

# <a name="net-v12-sdk"></a>[\.SDK do NET v12](#tab/dotnet)

Para saber mais sobre essa função, consulte os [arquivos function.json e run.csx](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions).

# <a name="nodejs-v10-sdk"></a>[SDK do Node.js V10](#tab/nodejsv10)

Para saber mais sobre essa função, confira os [arquivos function.json e index.js](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail).

---

O código de projeto de função é implantado diretamente no repositório público de exemplo. Para saber mais sobre as opções de implantação para o Azure Functions, consulte [Implantação contínua para o Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Criar uma assinatura de evento

Uma assinatura de evento indica quais eventos gerados pelo provedor você deseja que sejam enviados para um ponto de extremidade específico. Nesse caso, o ponto de extremidade é exposto pela função. Use as seguintes etapas para criar uma assinatura de evento que envia notificações à sua função no portal do Azure:

1. No [portal do Azure](https://portal.azure.com), na parte superior da página, procure e selecione `Function App` e escolha o aplicativo de funções que você acabou de criar. Selecione **Functions** e escolha a função **Miniatura**.

    :::image type="content" source="media/resize-images-on-storage-blob-upload-event/choose-thumbnail-function.png" alt-text="Escolher a função Miniatura no portal":::

1.  Selecione **Integração**, depois escolha o **Gatilho de Grade de Eventos** e selecione **Criar Assinatura de Grade de Eventos**.

    :::image type="content" source="./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png" alt-text="Navegar até Adicionar Assinatura de Grade de Eventos no portal do Azure" :::

1. Use as configurações da assinatura de evento, conforme especificado na tabela.
    
    ![Criar a assinatura de evento com base na função no portal do Azure](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ---------------- | -------------------------------------------------- |
    | **Nome** | imageresizersub | Nome que identifica a nova assinatura de evento. |
    | **Tipo de tópico** | Contas de armazenamento | Escolha o provedor de eventos da conta de Armazenamento. |
    | **Assinatura** | Sua assinatura do Azure | Por padrão, sua assinatura atual do Azure é selecionada. |
    | **Grupo de recursos** | myResourceGroup | Selecione **Usar existente** e escolha o grupo de recursos que está sendo usado neste tutorial. |
    | **Recurso** | Sua conta de armazenamento de Blobs | Escolha a conta de armazenamento de Blobs criada. |
    | **Nome do Tópico do Sistema** | imagestoragesystopic | Especifique um nome para o tópico do sistema. Para saber mais sobre os tópicos do sistema, confira [Visão geral dos tópicos do sistema](system-topics.md). |    
    | **Tipos de evento** | Blob criado | Desmarque todos os tipos que não sejam **Blob criado**. Somente os tipos de evento `Microsoft.Storage.BlobCreated` são passados para a função. |
    | **Tipo de ponto de extremidade** | gerado automaticamente | Pré-definido como **Função do Azure**. |
    | **Ponto de extremidade** | gerado automaticamente | Nome da função. Nesse caso, é **Miniatura**. |

1. Alterne para a guia **Filtros** e execute as seguintes ações:
    1. Selecione a opção **Habilitar filtragem por assunto**.
    1. Em **O assunto começa com**, insira o seguinte valor: **/blobServices/default/containers/images/** .

        ![Especificar o filtro para a assinatura de evento](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png)

1. Selecione **Criar** para adicionar a assinatura de evento. Isso criará uma assinatura de evento que dispara a função `Thumbnail` quando um blob é adicionado ao contêiner `images`. A função redimensiona as imagens e as adiciona ao contêiner `thumbnails`.

Agora que os serviços de back-end estão configurados, teste a funcionalidade de redimensionamento da imagem no aplicativo Web de exemplo.

## <a name="test-the-sample-app"></a>Testar o aplicativo de exemplo

Para testar o redimensionamento de imagem no aplicativo Web, navegue para a URL do aplicativo publicado. A URL padrão do aplicativo Web é `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[\.SDK do NET v12](#tab/dotnet)

Clique na região **Carregar fotos** para selecionar e carregar um arquivo. Também arraste uma foto para essa região.

Observe que, depois que a imagem carregada desaparece, uma cópia da imagem carregada é exibida no carrossel **Miniaturas Geradas**. Essa imagem foi redimensionada pela função, adicionada ao contêiner de *miniaturas* e baixada pelo cliente Web.

![Captura de tela que mostra um aplicativo Web publicado intitulado "ImageResizer" em um navegador para o SDK \.NET v12.](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[SDK do Node.js V10](#tab/nodejsv10)

Clique em **Escolher Arquivo** para selecionar um arquivo e, em seguida, clique em **Carregar Imagem**. Quando o upload for bem-sucedido, o navegador direcionará você para uma página de êxito. Clique no link para retornar à home page. Uma cópia da imagem carregada será exibida na área **Miniaturas Geradas**. (Se a imagem não for exibida no início, tente recarregar a página.) Essa imagem foi redimensionada pela função, adicionada ao contêiner de *miniaturas* e baixada pelo cliente Web.

![Aplicativo Web publicado no navegador](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma conta geral do Armazenamento do Azure
> * Implantar o código sem servidor usando o Azure Functions
> * Criar uma assinatura de evento do armazenamento de Blobs na Grade de Eventos

Avance até a terceira parte da série de tutoriais sobre o Armazenamento para saber como proteger o acesso à conta de armazenamento.

> [!div class="nextstepaction"]
> [Proteger o acesso aos dados de um aplicativo na nuvem](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Para saber mais sobre a Grade de Eventos, consulte [Uma introdução à Grade de Eventos do Azure](overview.md).
+ Para experimentar outro tutorial que mostra o Azure Functions, consulte [Criar uma função que é integrada aos Aplicativos Lógicos do Azure](../azure-functions/functions-twitter-email.md).

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
