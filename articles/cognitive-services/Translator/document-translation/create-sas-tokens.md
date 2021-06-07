---
title: Criar tokens de SAS (Assinatura de Acesso Compartilhado) para contêineres e blobs com o Gerenciador de Armazenamento Microsoft
description: Como criar um token SAS (Acesso Compartilhado) para contêineres e blobs com o Gerenciador de Armazenamento Microsoft e o portal do Azure
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 03/05/2021
ms.openlocfilehash: e40fc569ad1c8ec5894f06915422bea37cfc40ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102489626"
---
# <a name="create-sas-tokens-for-document-translation-processing"></a>Criar tokens de SAS para o processamento de tradução de documento

Neste artigo, você aprenderá a criar tokens de SAS (Assinatura de Acesso Compartilhado) usando o Gerenciador de Armazenamento do Azure ou o portal do Azure. Os tokens de SAS dão acesso seguro e delegado aos recursos na conta de armazenamento do Azure.

## <a name="create-your-sas-tokens-with-azure-storage-explorer"></a>Criar tokens de SAS com o Gerenciador de Armazenamento do Azure

### <a name="prerequisites"></a>Pré-requisitos

* Você precisa do aplicativo [**Gerenciador de Armazenamento do Azure**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) instalado em um ambiente de desenvolvimento do Windows, macOS ou Linux. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita para o gerenciamento de recursos do armazenamento em nuvem do Azure.
* Depois de instalar o aplicativo Gerenciador de Armazenamento do Azure, [conecte-o à conta de armazenamento](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service) que você está usando para a tradução de documento.

### <a name="create-your-tokens"></a>Criar os tokens

### <a name="sas-tokens-for-containers"></a>[Tokens de SAS para contêineres](#tab/Containers)

1. Abra o aplicativo Gerenciador de Armazenamento do Azure no computador local e navegue até suas **contas de armazenamento** conectadas.
1. Expanda o nó Contas de Armazenamento e selecione **Contêineres de Blob**.
1. Expanda o nó Contêineres de Blob e clique com o botão direito do mouse em um nó de **contêiner** de armazenamento para exibir o menu de opções.
1. Selecione **Obtenha Assinatura de Acesso Compartilhado...** no menu de opções.
1. Na janela **Assinatura de Acesso Compartilhado**, faça as seguintes seleções:
    * Selecione sua **Política de acesso** (o padrão é nenhuma política).
    * Especifique a data e a hora de **Início** e de **Expiração** da chave assinada. Recomendamos usar um período de vida útil curto porque não é possível revogar a SAS após a geração.
    * Selecione o **Fuso horário** da data e hora de início e de expiração (o padrão é Local).
    * Defina as **Permissões** do contêiner marcando e/ou desmarcando a caixa de seleção apropriada.
    * Confira os dados e selecione **Criar**.

1. Uma nova janela será exibida com o nome do **contêiner**, o **URI** e a **cadeia de caracteres de consulta** do contêiner.  
1. **Copie e cole os valores de contêiner, de URI e de cadeia de caracteres de consulta em uma localização segura. Eles só serão exibidos uma vez e não poderão ser recuperados depois que a janela for fechada.**
1. Para criar uma URL de SAS, acrescente o token SAS (URI) à URL de um serviço de armazenamento.

### <a name="sas-tokens-for-blobs"></a>[Tokens de SAS para blobs](#tab/blobs)

1. Abra o aplicativo Gerenciador de Armazenamento do Azure no computador local e navegue até suas **contas de armazenamento** conectadas.
1. Expanda seu nó de armazenamento e selecione **Contêineres de Blob**.
1. Expanda o nó Contêineres de Blob e selecione um nó de **contêiner** para exibir o conteúdo na janela principal.
1. Selecione o blob ao qual você deseja delegar acesso da SAS e clique com o botão direito do mouse para exibir o menu de opções.
1. Selecione **Obtenha Assinatura de Acesso Compartilhado...** no menu de opções.
1. Na janela **Assinatura de Acesso Compartilhado**, faça as seguintes seleções:
    * Selecione sua **Política de acesso** (o padrão é nenhuma política).
    * Especifique a data e a hora de **Início** e de **Expiração** da chave assinada. Recomendamos usar um período de vida útil curto porque não é possível revogar a SAS após a geração.
    * Selecione o **Fuso horário** da data e hora de início e de expiração (o padrão é Local).
    * Defina as **Permissões** do contêiner marcando e/ou desmarcando a caixa de seleção apropriada.
    * Confira os dados e selecione **Criar**.
1. Uma nova janela será exibida com o nome do **blob**, o **URI** e a **cadeia de caracteres de consulta** do blob.  
1. **Copie e cole os valores de blob, de URI e de cadeia de caracteres de consulta em uma localização segura. Eles só serão exibidos uma vez e não poderão ser recuperados depois que a janela for fechada.**
1. Para criar uma URL de SAS, acrescente o token SAS (URI) à URL de um serviço de armazenamento.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Criar tokens de SAS para blobs no portal do Azure

> [!NOTE]
> No momento, não há suporte para a criação de tokens de SAS para contêineres diretamente no portal do Azure. No entanto, você pode criar um token SAS com o [**Gerenciador de Armazenamento do Azure**](#create-your-sas-tokens-with-azure-storage-explorer) ou realizar a tarefa [programaticamente](../../../storage/blobs/sas-service-create.md).

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

* Uma [**conta do Azure**](https://azure.microsoft.com/free/cognitive-services/) ativa.  Se você não tem uma, [**crie uma conta gratuita**](https://azure.microsoft.com/free/).
* Um recurso de serviço de [**Tradução**](https://ms.portal.azure.com/#create/Microsoft) (**não** um recurso de vários serviços dos Serviços Cognitivos).  *Confira* [Criar um recurso do Azure](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* Uma [**conta de Armazenamento de Blobs do Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Você criará contêineres para armazenar e organizar dados de blob em sua conta de armazenamento.

### <a name="create-your-tokens"></a>Criar os tokens

Acesse o [portal do Azure](https://ms.portal.azure.com/#home) e navegue até:  

 **Sua conta de armazenamento** → **contêineres** → **seu contêiner** → **seu blob**

1. Selecione **Gerar SAS** no menu próximo à parte superior da página.

1. Selecione **Método de assinatura** → **Chave de delegação do usuário**.

1. Defina as **Permissões** marcando e/ou desmarcando a caixa de seleção apropriada.

1. Especifique a hora de **Início** e de **Expiração** da chave assinada.

1. O campo **Endereços IP permitidos** é opcional e especifica um endereço IP ou intervalo de endereços IP do qual aceitar solicitações. Se o endereço IP da solicitação não for igual ao endereço IP ou intervalo de endereços especificados no token SAS, ele não será autorizado.

1. O campo **Protocolos permitidos** é opcional e especifica o protocolo permitido para uma solicitação feita com a SAS. O valor padrão é HTTPS.

1. Confira os dados e selecione **Gerar token SAS e URL**.

1. A cadeia de caracteres de consulta do **token SAS do blob** e a **URL SAS do blob** serão exibidas na área inferior da janela.  

1. **Copie e cole os valores de blob e de URL do token SAS em uma localização segura. Eles só serão exibidos uma vez e não poderão ser recuperados depois que a janela for fechada.**

1. Para criar uma URL de SAS, acrescente o token SAS (URI) à URL de um serviço de armazenamento.

## <a name="learn-more"></a>Saiba mais

* [Criar tokens SAS para blobs ou contêineres programaticamente](../../../storage/blobs/sas-service-create.md)
* [Permissões para um diretório, contêiner ou blob](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução à Tradução de Documento](get-started-with-document-translation.md)
>
>