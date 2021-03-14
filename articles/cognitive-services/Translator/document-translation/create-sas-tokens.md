---
title: Criar token de SAS (assinatura de acesso compartilhado) para contêineres e BLOBs com o Microsoft Gerenciador de Armazenamento
description: Como criar um token de acesso compartilhado (SAS) para contêineres e BLOBs com o Microsoft Gerenciador de Armazenamento e o portal do Azure
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 03/05/2021
ms.openlocfilehash: e40fc569ad1c8ec5894f06915422bea37cfc40ee
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489626"
---
# <a name="create-sas-tokens-for-document-translation-processing"></a>Criar tokens SAS para processamento de tradução de documentos

Neste artigo, você aprenderá a criar tokens de SAS (assinatura de acesso compartilhado) usando o Gerenciador de Armazenamento do Azure ou o portal do Azure. Um token SAS fornece acesso seguro e delegado aos recursos em sua conta de armazenamento do Azure.

## <a name="create-your-sas-tokens-with-azure-storage-explorer"></a>Criar tokens SAS com Gerenciador de Armazenamento do Azure

### <a name="prerequisites"></a>Pré-requisitos

* Você precisará de um aplicativo [**Gerenciador de armazenamento do Azure**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) instalado em seu ambiente de desenvolvimento do Windows, MacOS ou Linux. Gerenciador de Armazenamento do Azure é uma ferramenta gratuita que permite que você gerencie facilmente seus recursos de armazenamento em nuvem do Azure.
* Depois que o aplicativo Gerenciador de Armazenamento do Azure for instalado, [Conecte-o à conta de armazenamento](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service) que você está usando para a tradução de documentos.

### <a name="create-your-tokens"></a>Criar seus tokens

### <a name="sas-tokens-for-containers"></a>[Tokens SAS para contêineres](#tab/Containers)

1. Abra o aplicativo Gerenciador de Armazenamento do Azure no computador local e navegue até suas contas de **armazenamento** conectadas.
1. Expanda o nó contas de armazenamento e selecione **contêineres de blob**.
1. Expanda o nó contêineres de BLOB e clique com o botão direito do mouse em um nó de **contêiner** de armazenamento ou para exibir o menu de opções.
1. Selecione **obter assinatura de acesso compartilhado...** no menu de opções.
1. Na janela **assinatura de acesso compartilhado** , faça as seguintes seleções:
    * Selecione sua **política de acesso** (o padrão é nenhum).
    * Especifique a data e a hora de **início** e **expiração** da chave assinada. Uma breve vida é recomendada porque, uma vez gerada, uma SAS não pode ser revogada.
    * Selecione o **fuso horário** para a data e hora de início e expiração (o padrão é local).
    * Defina as **permissões** de contêiner marcando e/ou desmarcando a caixa de seleção apropriada.
    * Examine e selecione **criar**.

1. Uma nova janela será exibida com o nome do **contêiner** , o **URI** e a **cadeia de caracteres de consulta** para o seu contêiner.  
1. **Copie e cole os valores de contêiner, URI e cadeia de caracteres de consulta em um local seguro. Eles só serão exibidos uma vez e não poderão ser recuperados depois que a janela for fechada.**
1. Para construir uma URL SAS, acrescente o token SAS (URI) à URL de um serviço de armazenamento.

### <a name="sas-tokens-for-blobs"></a>[Tokens SAS para BLOBs](#tab/blobs)

1. Abra o aplicativo Gerenciador de Armazenamento do Azure no computador local e navegue até suas contas de **armazenamento** conectadas.
1. Expanda seu nó de armazenamento e selecione **contêineres de blob**.
1. Expanda o nó contêineres de BLOB e selecione um nó de **contêiner** para exibir o conteúdo na janela principal.
1. Selecione o blob no qual você deseja delegar acesso à SAS e clique com o botão direito do mouse para exibir o menu de opções.
1. Selecione **obter assinatura de acesso compartilhado...** no menu de opções.
1. Na janela **assinatura de acesso compartilhado** , faça as seguintes seleções:
    * Selecione sua **política de acesso** (o padrão é nenhum).
    * Especifique a data e a hora de **início** e **expiração** da chave assinada. Uma breve vida é recomendada porque, uma vez gerada, uma SAS não pode ser revogada.
    * Selecione o **fuso horário** para a data e hora de início e expiração (o padrão é local).
    * Defina as **permissões** de contêiner marcando e/ou desmarcando a caixa de seleção apropriada.
    * Examine e selecione **criar**.
1. Uma nova janela será exibida com o nome do **blob** , o **URI** e a **cadeia de caracteres de consulta** para seu BLOB.  
1. **Copie e cole os valores de BLOB, URI e cadeia de caracteres de consulta em um local seguro. Eles só serão exibidos uma vez e não poderão ser recuperados depois que a janela for fechada.**
1. Para construir uma URL SAS, acrescente o token SAS (URI) à URL de um serviço de armazenamento.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Criar tokens SAS para BLOBs no portal do Azure

> [!NOTE]
> No momento, não há suporte para a criação de tokens SAS para contêineres diretamente no portal do Azure. No entanto, você pode criar um token SAS com [**Gerenciador de armazenamento do Azure**](#create-your-sas-tokens-with-azure-storage-explorer) ou concluir a tarefa [programaticamente](../../../storage/blobs/sas-service-create.md).

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

* Uma [**conta ativa do Azure**](https://azure.microsoft.com/free/cognitive-services/).  Se você não tiver uma, poderá [**criar uma conta gratuita**](https://azure.microsoft.com/free/).
* Um recurso de serviço do [**Tradutor**](https://ms.portal.azure.com/#create/Microsoft) (**não** um recurso de vários serviços cognitivas.  *Consulte* [criar um novo recurso do Azure](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* Uma [**conta de armazenamento de BLOBs do Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Você criará contêineres para armazenar e organizar seus dados de BLOB em sua conta de armazenamento.

### <a name="create-your-tokens"></a>Criar seus tokens

Vá para a [portal do Azure](https://ms.portal.azure.com/#home) e navegue da seguinte maneira:  

 **Sua conta de armazenamento** → **contêineres** → **seu contêiner** → **seu blob**

1. Selecione **gerar SAS** no menu próximo à parte superior da página.

1. Selecione **método de assinatura** → **chave de delegação de usuário**.

1. Defina **as permissões** marcando e/ou desmarcando a caixa de seleção apropriada.

1. Especifique as horas de **início** e **expiração** da chave assinada.

1. O campo **endereços IP permitidos** é opcional e especifica um endereço IP ou um intervalo de endereços IP do qual aceitar solicitações. Se o endereço IP da solicitação não corresponder ao endereço IP ou ao intervalo de endereços especificado no token SAS, ele não será autorizado.

1. O campo **protocolos permitidos** é opcional e especifica o protocolo permitido para uma solicitação feita com a SAS. O valor padrão é HTTPS.

1. Examine e selecione **gerar token SAS e URL**.

1. A cadeia de caracteres de consulta do **token SAS do blob** e a **URL SAS do blob** serão exibidas na área inferior da janela.  

1. **Copie e cole o token SAS do blob e os valores de URL em um local seguro. Eles só serão exibidos uma vez e não poderão ser recuperados depois que a janela for fechada.**

1. Para construir uma URL SAS, acrescente o token SAS (URI) à URL de um serviço de armazenamento.

## <a name="learn-more"></a>Saiba mais

* [Criar tokens SAS para BLOBs ou contêineres programaticamente](../../../storage/blobs/sas-service-create.md)
* [Permissões para um diretório, contêiner ou BLOB](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução à Tradução de Documento](get-started-with-document-translation.md)
>
>