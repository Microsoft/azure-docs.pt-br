---
title: Usar a Grade de Eventos do Azure para automatizar o redimensionamento de imagens carregadas | Microsoft Docs
description: "A Grade de Eventos do Azure pode ser disparada em carregamentos de blob no Armazenamento do Azure. Você pode usar isso para enviar os arquivos de imagem carregados no Armazenamento do Azure para outros serviços, como o Azure Functions, para redimensionamento e outras melhorias."
services: event-grid
author: ggailey777
manager: cfowler
editor: 
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 358015d6cfd9961508b209f628b2d648a75e3c2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>Automatizar o redimensionamento de imagens carregadas usando a Grade de Eventos

A [Grade de Eventos do Azure](overview.md) é um serviço de eventos para a nuvem. A Grade de Eventos permite criar assinaturas em eventos gerados pelos serviços do Azure ou por recursos de terceiros.  

Este tutorial é a segunda parte de uma série de tutoriais sobre o Armazenamento. Ele estende o [tutorial anterior sobre o Armazenamento][previous-tutorial] para adicionar a geração automática de miniaturas sem servidor usando a Grade de Eventos do Azure e o Azure Functions. A Grade de Eventos permite que o [Azure Functions](..\azure-functions\functions-overview.md) responda aos eventos do [armazenamento de Blobs do Azure](..\storage\blobs\storage-blobs-introduction.md) e gere miniaturas das imagens carregadas. Uma assinatura de evento é criada no evento de criação do armazenamento de Blobs. Quando um blob é adicionado a um contêiner de armazenamento de Blobs específico, um ponto de extremidade de função é chamado. Os dados passados para a associação de função da Grade de Eventos são usados para acessar o blob e gerar a imagem em miniatura. 

Use a CLI do Azure e o portal do Azure para adicionar a funcionalidade de redimensionamento a um aplicativo de upload de imagens existente.

[!INCLUDE [storage-events-note.md](../../includes/storage-events-note.md)]

![Aplicativo Web publicado no navegador Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta geral do Armazenamento do Azure
> * Implantar o código sem servidor usando o Azure Functions
> * Criar uma assinatura de evento do armazenamento de Blobs na Grade de Eventos

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ Você deve ter concluído o tutorial anterior sobre o armazenamento de Blobs: [Carregar dados de imagem na nuvem com o Armazenamento do Azure][previous-tutorial]. 
+ Você deve se inscrever e ter acesso à funcionalidade de eventos do armazenamento de Blobs. [Solicite o acesso aos eventos do armazenamento de Blobs](#request-storage-access) antes de continuar com as outras etapas do tópico.  

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá a execução da CLI do Azure versão 2.0.14 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

Se você não estiver usando o Cloud Shell, primeiro você deve entrar usando `az login`.

## <a name="enable-blob-storage-events"></a>Habilitar os eventos do armazenamento de Blobs

No momento, é necessário solicitar o acesso ao recurso de eventos do armazenamento de Blobs.  

### <a name="request-storage-access"></a>Solicitar o acesso aos eventos do armazenamento de Blobs

Solicite o acesso com o comando `az feature register`.

> [!IMPORTANT]  
> Aceitamos os participantes da versão prévia dos eventos do armazenamento de Blobs na ordem em que eles solicitaram a participação. Pode haver um atraso de 1 a 2 dias úteis para obter o acesso a esse recurso. 

```azurecli-interactive
az feature register --name storageEventSubscriptions --namespace Microsoft.EventGrid
```

### <a name="check-access-status"></a>Verificar o status de aprovação

Você receberá um email da Microsoft notificando que você obteve o acesso aos eventos do armazenamento de Blobs. Verifique o status de sua solicitação de acesso a qualquer momento com o comando `az feature show`.

```azurecli-interactive
az feature show --name storageEventSubscriptions --namespace Microsoft.EventGrid --query properties.state
```
Depois de obter o acesso ao recurso de eventos do armazenamento de Blobs, esse comando retornará um valor `"Registered"`. 
 
Após o registro, você poderá continuar com este tutorial.

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

O Azure Functions exige uma conta de armazenamento geral. Crie uma conta de armazenamento geral separada no grupo de recursos usando o comando [az storage account create](/cli/azure/storage/account#create).

Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. 

No comando a seguir, substitua seu próprio nome global exclusivo da conta de armazenamento quando o espaço reservado `<general_storage_account>` for exibido. 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>Criar um aplicativo de funções  

Você deve ter um aplicativo de funções para hospedar a execução da função. O aplicativo de funções fornece um ambiente para execução sem servidor do seu código de função. Crie um aplicativo de funções ao usar o comando [az functionapp create](/cli/azure/functionapp#create). 

No comando a seguir, substitua seu próprio nome exclusivo do aplicativo de funções em que o espaço reservado `<function_app>` é exibido. O `<function_app>` é usado como domínio DNS padrão para o aplicativo de funções, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure. Nesse caso, `<general_storage_account>` é o nome da conta de armazenamento geral criada.  

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

Agora você deve configurar o aplicativo de funções para se conectar ao armazenamento de blobs. 

## <a name="configure-the-function-app"></a>Configurar o aplicativo de funções

A função precisa da cadeia de conexão para se conectar à conta de armazenamento de blobs. Nesse caso, `<blob_storage_account>` é o nome da conta de armazenamento de Blobs criado no tutorial anterior. Obtenha a cadeia de conexão com o comando [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). O nome do contêiner da imagem em miniatura também deve ser definido como `thumbs`. Adicione essas configurações de aplicativo ao aplicativo de funções com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbs
```

Agora você pode implantar um projeto de código de função nesse aplicativo de funções.

## <a name="deploy-the-function-code"></a>Implantar o código de função 

A função C# que executa o redimensionamento de imagem está disponível neste [repositório GitHub de exemplo](https://github.com/Azure-Samples/function-image-upload-resize). Implante esse projeto de código do Functions no aplicativo de funções usando o comando [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config). 

No comando a seguir, `<function_app>` é o mesmo aplicativo de funções criado no script anterior.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

A função de redimensionamento de imagem é disparada por uma assinatura de evento para um evento criado do Blob. Os dados passados para o gatilho incluem a URL do blob, que, por sua vez, é passada para a associação de entrada para obter a imagem carregada do armazenamento de Blobs. A função gera uma imagem em miniatura e grava o fluxo resultante em um contêiner separado no armazenamento de Blobs. Para saber mais sobre essa função, consulte o [arquivo Leiame no repositório de exemplo](https://github.com/Azure-Samples/function-image-upload-resize/blob/master/README.md).
 
O código de projeto de função é implantado diretamente no repositório público de exemplo. Para saber mais sobre as opções de implantação para o Azure Functions, consulte [Implantação contínua para o Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-your-event-subscription"></a>Criar a assinatura de evento

Uma assinatura de evento indica quais eventos gerados pelo provedor você deseja que sejam enviados para um ponto de extremidade específico. Nesse caso, o ponto de extremidade é exposto pela função. Use as seguintes etapas para criar uma assinatura de evento da função no portal do Azure: 

1. No [portal do Azure](https://portal.azure.com), clique na seta no canto inferior esquerdo para expandir todos os serviços, digite `functions` no campo **Filtro** e, em seguida, escolha **Aplicativos de Funções**. 

    ![Navegar para os Aplicativos de Funções no portal do Azure](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Expanda o aplicativo de funções, escolha a função **imageresizerfunc** e, em seguida, selecione **Adicionar assinatura da Grade de Eventos**.

    ![Navegar para os Aplicativos de Funções no portal do Azure](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Use as configurações da assinatura de evento, conforme especificado na tabela.

    ![Criar a assinatura de evento com base na função no portal do Azure](./media/resize-images-on-storage-blob-upload-event/event-subscription-create-flow.png)

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | imageresizersub | Nome que identifica a nova assinatura de evento. | 
    | **Tipo de tópico** |  Contas de armazenamento | Escolha o provedor de eventos da conta de Armazenamento. | 
    | **Assinatura** | Sua assinatura | Por padrão, a assinatura atual deve estar selecionada.   |
    | **Grupo de recursos** | myResourceGroup | Selecione **Usar existente** e escolha o grupo de recursos que está sendo usado neste tópico.  |
    | **Instância** |  `<blob_storage_account>` |  Escolha a conta de armazenamento de Blobs criada. |
    | **Tipos de evento** | Blob criado | Desmarque todos os tipos que não sejam **Blob criado**. Somente os tipos de evento `Microsoft.Storage.BlobCreated` são passados para a função.| 
    | **Ponto de extremidade do assinante** | gerado automaticamente | Use a URL de ponto de extremidade gerada para você. | 
    | **Filtro de prefixo** | /blobServices/default/containers/images/blobs/ | Filtra os eventos de armazenamento para somente aqueles no contêiner **images**.| 

4. Clique em **Criar** para adicionar a assinatura de evento. Isso cria uma assinatura de evento que dispara **imageresizerfunc** quando um blob é adicionado ao contêiner **images**. As imagens redimensionadas são adicionadas ao contêiner **thumbs**.

Agora que os serviços de back-end estão configurados, teste a funcionalidade de redimensionamento da imagem no aplicativo Web de exemplo. 

## <a name="test-the-sample-app"></a>Testar o aplicativo de exemplo

Para testar o redimensionamento de imagem no aplicativo Web, navegue para a URL do aplicativo publicado. A URL padrão do aplicativo Web é `https://<web_app>.azurewebsites.net`.

Clique na região **Carregar fotos** para selecionar e carregar um arquivo. Também arraste uma foto para essa região. 

Observe que, depois que a imagem carregada desaparece, uma cópia da imagem carregada é exibida no carrossel **Miniaturas geradas**. Essa imagem foi redimensionada pela função, adicionada ao contêiner thumbs e baixada pelo cliente Web. 

![Aplicativo Web publicado no navegador Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma conta geral do Armazenamento do Azure
> * Implantar o código sem servidor usando o Azure Functions
> * Criar uma assinatura de evento do armazenamento de Blobs na Grade de Eventos

Avance até a terceira parte da série de tutoriais sobre o Armazenamento para saber como proteger o acesso à conta de armazenamento.

> [!div class="nextstepaction"]
> [Proteger o acesso aos dados de um aplicativo na nuvem](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)


+ Para saber mais sobre a Grade de Eventos, consulte [Uma introdução à Grade de Eventos do Azure](overview.md). 
+ Para experimentar outro tutorial que mostra o Azure Functions, consulte [Criar uma função que é integrada aos Aplicativos Lógicos do Azure](..\azure-functions\functions-twitter-email.md). 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md