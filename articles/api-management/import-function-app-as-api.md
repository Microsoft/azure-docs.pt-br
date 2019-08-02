---
title: Importar um Aplicativo de funções do Azure como uma API no Gerenciamento de API do Azure | Microsoft Docs
description: Este tutorial mostra como importar um Aplicativo de funções do Azure para o Gerenciamento de API do Azure como uma API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: apimpm
ms.openlocfilehash: f9e5c531e387ba8d2c61c6d46b1e8935bc7d42cf
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67429054"
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
* Verifique se que você tem um aplicativo do Azure Functions em sua assinatura. Para obter mais informações, consulte [Criar um Aplicativo de funções do Azure](../azure-functions/functions-create-first-azure-function.md#create-a-function-app). Ele deve conter o Functions com gatilho HTTP e a configuração de nível de autorização definida como *Anônimo* ou *Função*.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-new-api-from-azure-function-app"></a> Importar um Aplicativo de funções do Azure como uma nova API

Siga as etapas abaixo para criar uma nova API a partir de um Aplicativo de funções do Azure.

1. Em sua instância de serviço do **Gerenciamento de API do Azure**, selecione **APIs** no menu à esquerda.

2. Na lista **Adicionar nova API**, selecione **Aplicativo de funções**.

    ![Adicionar a partir do Aplicativo de funções](./media/import-function-app-as-api/add-01.png)

3. Clique em **Procurar** para selecionar o Functions para importação.

    ![Adicionar a partir do Aplicativo de funções](./media/import-function-app-as-api/add-02.png)

4. Clique na seção **Aplicativo de funções** para escolher na lista de aplicativos de funções disponíveis.

    ![Adicionar a partir do Aplicativo de funções](./media/import-function-app-as-api/add-03.png)

5. Encontre o Aplicativo de funções de onde você deseja importar funções, clique nele e pressione **Selecionar**.

    ![Adicionar a partir do Aplicativo de funções](./media/import-function-app-as-api/add-04.png)

6. Selecione as funções que você deseja importar e clique em **Selecionar**.

    ![Adicionar a partir do Aplicativo de funções](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Você pode importar apenas as funções baseadas em gatilho HTTP e que tenham a configuração de nível de autorização definida como *Anônimo* ou *Função*.

7. Alterne para a exibição **Completa** e atribua **Produto** à nova API. Se necessário, edite outros campos preenchidos previamente.

    ![Adicionar a partir do Aplicativo de funções](./media/import-function-app-as-api/add-06.png)

8. Clique em **Criar**.

## <a name="append-azure-function-app-to-api"></a> Acrescentar o Aplicativo de funções do Azure a uma API existente

Siga as etapas abaixo para acrescentar o Aplicativo de funções do Azure a uma API existente.

1. Em sua instância de serviço do **Gerenciamento de API do Azure**, selecione **APIs** no menu à esquerda.

2. Escolha uma API para a qual você deseja importar um Aplicativo de funções. Clique em **...**  e selecione **Importar** no menu de contexto.

    ![Acrescentar a partir do Aplicativo de funções](./media/import-function-app-as-api/append-01.png)

3. Clique no bloco **Aplicativo de Funções**.

    ![Acrescentar a partir do Aplicativo de funções](./media/import-function-app-as-api/append-02.png)

4. Na janela pop-up, clique em **Procurar**.

    ![Acrescentar a partir do Aplicativo de funções](./media/import-function-app-as-api/append-03.png)

5. Clique na seção **Aplicativo de funções** para escolher na lista de aplicativos de funções disponíveis.

    ![Adicionar a partir do Aplicativo de funções](./media/import-function-app-as-api/add-03.png)

6. Encontre o Aplicativo de funções de onde você deseja importar funções, clique nele e pressione **Selecionar**.

    ![Adicionar a partir do Aplicativo de funções](./media/import-function-app-as-api/add-04.png)

7. Selecione as funções que você deseja importar e clique em **Selecionar**.

    ![Adicionar a partir do Aplicativo de funções](./media/import-function-app-as-api/add-05.png)

8. Clique em **Importar**.

    ![Acrescentar a partir do Aplicativo de funções](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a> Autorização

A importação de um Aplicativo de funções do Azure gera automaticamente:

* A chave de host dentro do Aplicativo de funções com o nome apim-{*nome da instância de serviço do Gerenciamento de API do Azure*};
* Valor nomeado dentro da instância do Gerenciamento de API do Azure com o nome {*nome da instância do Aplicativo de funções do Azure*}-key, que contém a chave de host criada.

Para as APIs criadas após 4 de abril de 2019, a chave de host é passada em solicitações HTTP do Gerenciamento de API para o Aplicativo de funções em um cabeçalho. As APIs mais antigas passam a chave de host como [um parâmetro de consulta](../azure-functions/functions-bindings-http-webhook.md#api-key-authorization). Esse comportamento pode ser alterado por meio da [chamada à API REST](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend/update#backendcredentialscontract) `PATCH Backend` na entidade *Back-end* associada ao Aplicativo de funções.

> [!WARNING]
> A remoção ou alteração do valor da chave de host do Aplicativo de funções do Azure ou do valor nomeado do Gerenciamento de API interromperá a comunicação entre os serviços. Os valores não são sincronizados automaticamente.
>
> Se você precisar girar a chave de host, modifique também o valor nomeado no Gerenciamento de API do Azure.

### <a name="access-azure-function-app-host-key"></a>Acessar chave de host do Aplicativo de funções do Azure

1. Navegue até sua instância do Aplicativo de funções do Azure.

2. Selecione **Configurações do Aplicativo de Funções** na visão geral.

    ![Adicionar a partir do Aplicativo de funções](./media/import-function-app-as-api/keys-02-a.png)

3. A chave está localizada na seção **Chaves de Host**.

    ![Adicionar a partir do Aplicativo de funções](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Acessar o valor nomeado no Gerenciamento de API do Azure

Navegue até sua instância do Gerenciamento de API do Azure e selecione **Valores nomeados** no menu à esquerda. A chave do Aplicativo de funções do Azure está armazenada lá.

![Adicionar a partir do Aplicativo de funções](./media/import-function-app-as-api/keys-01.png)

## <a name="test-in-azure-portal"></a> Testar a nova API do Gerenciamento de API no portal do Azure

Você pode chamar as operações diretamente do portal do Azure. Usar o portal do Azure é uma maneira conveniente de exibir e testar as operações de uma API.  

1. Selecione a API criada na seção anterior.

2. Selecione a guia **Testar**.

3. Selecione uma operação.

    A página exibe os campos dos parâmetros de consulta e os campos dos cabeçalhos. Um dos cabeçalhos é **Ocp-Apim-Subscription-Key**, para a chave de assinatura do produto que está associada a essa API. Se você criou a instância do Gerenciamento de API, já é um administrador e, portanto, a chave é preenchida automaticamente. 

4. Selecione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

## <a name="test-in-developer-portal"></a>Chamar uma operação no portal do desenvolvedor

Você também pode chamar as operações do Portal do desenvolvedor para testar APIs. 

1. Selecione a API criada na etapa Importar e publicar uma API de back-end.

2. Selecione **Portal do desenvolvedor**.

    O site do portal do desenvolvedor é aberto.

3. Selecione a **API** que você criou.

4. Selecione a operação que deseja testar.

5. Selecione **Experimentar**.

6. Selecione **Enviar**.
    
    Após invocar uma operação, o portal do desenvolvedor exibe o **Status de resposta**, os **Cabeçalhos de resposta** e o **Conteúdo de resposta**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)
