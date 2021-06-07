---
title: Importar um Aplicativo de funções do Azure como uma API no Gerenciamento de API
titleSuffix: Azure API Management
description: Este artigo mostra como importar um Aplicativo de funções do Azure para o Gerenciamento de API do Azure como uma API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: f66395b1e0f45f1e80cd0ac93bf8c9ae8674a0f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732938"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Importar um Aplicativo de funções do Azure como uma API no Gerenciamento de API do Azure

O Gerenciamento de API do Azure dá suporte à importação de aplicativos de funções do Azure como novas APIs ou acrescentando-as a APIs existentes. O processo gera uma chave de host no Aplicativo de funções do Azure automaticamente, que é atribuída a um valor nomeado no Gerenciamento de API do Azure.

Este artigo percorre a importação de um Aplicativo de funções do Azure como uma API no Gerenciamento de API do Azure. Ele também descreve o processo de teste.

Você saberá como:

> [!div class="checklist"]
> * Importar um Aplicativo de funções do Azure como uma API
> * Acrescentar um Aplicativo de funções do Azure a uma API
> * Exibir a nova chave de host do Aplicativo de funções do Azure e o valor nomeado do Gerenciamento de API do Azure
> * Testar a API no Portal do Azure
> * Testar a API no portal do desenvolvedor

## <a name="prerequisites"></a>Pré-requisitos

* Conclua o início rápido [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
* Verifique se que você tem um aplicativo do Azure Functions em sua assinatura. Para obter mais informações, consulte [Criar um Aplicativo de funções do Azure](../azure-functions/functions-get-started.md). Ele deve conter o Functions com gatilho HTTP e a configuração de nível de autorização definida como *Anônimo* ou *Função*.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Importar um Aplicativo de funções do Azure como uma nova API

Siga as etapas abaixo para criar uma nova API a partir de um Aplicativo de funções do Azure.

1. Navegue até o serviço de Gerenciamento de API no portal do Azure e selecione **APIs** no menu.

2. Na lista **Adicionar nova API**, selecione **Aplicativo de funções**.

    ![Captura de tela que mostra o bloco Aplicativo de funções.](./media/import-function-app-as-api/add-01.png)

3. Clique em **Procurar** para selecionar o Functions para importação.

    ![Captura de tela que realça o botão Procurar.](./media/import-function-app-as-api/add-02.png)

4. Clique na seção **Aplicativo de funções** para escolher na lista de aplicativos de funções disponíveis.

    ![Captura de tela que realça a seção Aplicativo de funções.](./media/import-function-app-as-api/add-03.png)

5. Encontre o Aplicativo de funções de onde você deseja importar funções, clique nele e pressione **Selecionar**.

    ![Captura de tela que realça o Aplicativo de funções do qual você deseja importar funções e o botão Selecionar.](./media/import-function-app-as-api/add-04.png)

6. Selecione as funções que você deseja importar e clique em **Selecionar**.

    ![Captura de tela que realça as funções a serem importadas e o botão Selecionar.](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Você pode importar apenas as funções baseadas em gatilho HTTP e que tenham a configuração de nível de autorização definida como *Anônimo* ou *Função*.

7. Alterne para a exibição **Completa** e atribua **Produto** à nova API. Se necessário, especifique outros campos durante a criação ou configure-os mais tarde, acessando a guia **Configurações**. As configurações são explicadas no tutorial [Importar e publicar sua primeira API](import-and-publish.md#import-and-publish-a-backend-api).
8. Clique em **Criar**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Acrescentar o Aplicativo de funções do Azure a uma API existente

Siga as etapas abaixo para acrescentar o Aplicativo de funções do Azure a uma API existente.

1. Em sua instância de serviço do **Gerenciamento de API do Azure**, selecione **APIs** no menu à esquerda.

2. Escolha uma API para a qual você deseja importar um Aplicativo de funções. Clique em **...**  e selecione **Importar** no menu de contexto.

    ![Captura de tela que realça a opção de menu Importar.](./media/import-function-app-as-api/append-01.png)

3. Clique no bloco **Aplicativo de Funções**.

    ![Captura de tela que realça o bloco Aplicativo de funções.](./media/import-function-app-as-api/append-02.png)

4. Na janela pop-up, clique em **Procurar**.

    ![Captura de tela que mostra o botão Procurar.](./media/import-function-app-as-api/append-03.png)

5. Clique na seção **Aplicativo de funções** para escolher na lista de aplicativos de funções disponíveis.

    ![Captura de tela que realça a lista de Aplicativos de funções.](./media/import-function-app-as-api/add-03.png)

6. Encontre o Aplicativo de funções de onde você deseja importar funções, clique nele e pressione **Selecionar**.

    ![Captura de tela que realça o Aplicativo de funções do qual você deseja importar funções.](./media/import-function-app-as-api/add-04.png)

7. Selecione as funções que você deseja importar e clique em **Selecionar**.

    ![Captura de tela que realça as funções que você gostaria de importar.](./media/import-function-app-as-api/add-05.png)

8. Clique em **Importar**.

    ![Acrescentar a partir do Aplicativo de funções](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a><a name="authorization"></a> Autorização

A importação de um Aplicativo de funções do Azure gera automaticamente:

* A chave de host dentro do Aplicativo de funções com o nome apim-{*nome da instância de serviço do Gerenciamento de API do Azure*};
* Valor nomeado dentro da instância do Gerenciamento de API do Azure com o nome {*nome da instância do Aplicativo de funções do Azure*}-key, que contém a chave de host criada.

Para as APIs criadas após 4 de abril de 2019, a chave de host é passada em solicitações HTTP do Gerenciamento de API para o Aplicativo de funções em um cabeçalho. As APIs mais antigas passam a chave de host como [um parâmetro de consulta](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization). Esse comportamento pode ser alterado por meio da `PATCH Backend` [chamada à API REST](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) na entidade *Back-end* associada ao Aplicativo de funções.

> [!WARNING]
> A remoção ou alteração do valor da chave de host do Aplicativo de funções do Azure ou do valor nomeado do Gerenciamento de API interromperá a comunicação entre os serviços. Os valores não são sincronizados automaticamente.
>
> Se você precisar girar a chave de host, modifique também o valor nomeado no Gerenciamento de API do Azure.

### <a name="access-azure-function-app-host-key"></a>Acessar chave de host do Aplicativo de funções do Azure

1. Navegue até sua instância do Aplicativo de funções do Azure.

2. Selecione **Configurações do Aplicativo de Funções** na visão geral.

    ![Captura de tela que realça a opção de configurações do Aplicativo de funções.](./media/import-function-app-as-api/keys-02-a.png)

3. A chave está localizada na seção **Chaves de Host**.

    ![Captura de tela que realça a seção Chaves de Host.](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Acessar o valor nomeado no Gerenciamento de API do Azure

Navegue até sua instância do Gerenciamento de API do Azure e selecione **Valores nomeados** no menu à esquerda. A chave do Aplicativo de funções do Azure está armazenada lá.

![Adicionar a partir do Aplicativo de funções](./media/import-function-app-as-api/keys-01.png)

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Testar a nova API no portal do Azure

Você pode chamar as operações diretamente do portal do Azure. Usar o portal do Azure é uma maneira conveniente de exibir e testar as operações de uma API.  

1. Selecione a API criada na seção anterior.

2. Selecione a guia **Testar**.

3. Selecione uma operação.

    A página exibe os campos dos parâmetros de consulta e os campos dos cabeçalhos. Um dos cabeçalhos é **Ocp-Apim-Subscription-Key**, para a chave de assinatura do produto que está associada a essa API. Se você criou a instância do Gerenciamento de API, já é um administrador e, portanto, a chave é preenchida automaticamente. 

4. Selecione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)
