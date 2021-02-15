---
title: Configurar o postmaster para a API REST dos serviços de mídia do Azure v3
description: Este artigo mostra como configurar o Postman para que ele possa ser usado para chamar APIs REST do Azure Media Services (AMS).
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b0a521ddeee6d93ce809e5e6e85eefa1f01a3f12
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953964"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Configurar o postmaster para chamadas da API REST dos serviços de mídia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra como configurar o **Postman** para que ele possa ser usado para chamar APIs REST do Azure Media Services (AMS). O artigo mostra como importar arquivos de ambiente e coleção para **Postman**. A coleção contém definições agrupadas de solicitações HTTP que chamam as APIs de REST dos Serviços de Mídia do Azure (AMS). O arquivo de ambiente contém variáveis que são usadas pela coleção.

Antes de começar a desenvolver, examine o [desenvolvimento com as APIs dos serviços de mídia v3](media-services-apis-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Crie uma conta de Serviços de Mídia](./create-account-howto.md). Lembre-se de lembrar o nome do grupo de recursos e o nome da conta do Media Services. 
- Obtenha as informações necessárias para [acessar as APIs](./access-api-howto.md)
- Instale o cliente REST do [Postman](https://www.getpostman.com/) para executar as APIs de REST mostradas em alguns dos tutoriais do REST AMS. 

    Estamos usando o **Postman** mas qualquer ferramenta REST seria adequada. As outras alternativas são: **Visual Studio Code** com o plug-in do REST ou **Telerik Fiddler**. 

> [!IMPORTANT]
> Examine [Convenções de nomenclatura](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Baixar arquivos Postman

Clone um repositório GitHub que contenha os arquivos do ambiente e coleção Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Configurar Postman

### <a name="configure-the-environment"></a>Configure o ambiente 

1. Abra o aplicativo **Postman**.
2. À direita da tela, selecione a opção **Gerenciar ambiente**.

    ![Gerenciar ambiente](./media/develop-with-postman/postman-import-env.png)
4. Na caixa de diálogo **Gerenciar ambiente**, clique em **Importar**.
2. Navegue até o arquivo `Azure Media Service v3 Environment.postman_environment.json` que foi baixado quando você clonou `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. O ambiente do **Ambiente do Serviço de Mídia do Azure v3** é adicionado.

    > [!Note]
    > Atualize as variáveis de acesso com valores obtidos a partir da seção **Acessar a API de Serviços de Mídia do Azure** acima.

7. Clique duas vezes no arquivo escolhido e digite os valores que você obteve seguindo as etapas de acesso à API.
8. Feche a caixa de diálogo.
9. Selecione o ambiente, **Ambiente do Serviço de Mídia do Azure v3**, no menu suspenso.

    ![Escolher ambiente](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Configurar a coleção

1. Clique em **Importar** para importar o arquivo de coleção.
1. Navegue até o arquivo `Media Services v3.postman_collection.json` que foi baixado quando você clonou `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Escolha o arquivo **Serviços de Mídia v3.postman_collection.json**.

    ![Importar um arquivo](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Obter token do Microsoft Azure AD 

Antes de começar a manipular os recursos do AMS v3, você precisa obter e definir o token do Azure AD para Autenticação do Principal de Serviço.

1. Na janela esquerda do aplicativo Postman, selecione "Etapa 1: obter token de autenticação do Azure Active Directory".
2. Em seguida, selecione "Obter token do Microsoft Azure AD para autenticação de entidade de serviço".
3. Pressione **Enviar**.

    A operação **POST** a seguir é enviada.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. A resposta retorna com o token e define a variável de ambiente "AccessToken" como o valor de token.  

    ![Obter token do AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Solução de problemas 

* Se seu aplicativo falhar com "HTTP 504: tempo limite do gateway", verifique se a variável de local não foi definida explicitamente com um valor diferente do local esperado da conta dos serviços de mídia. 
* Se você receber um erro "conta não encontrada", verifique também se a propriedade local na mensagem JSON do corpo está definida como o local em que a conta dos serviços de mídia está. 

## <a name="see-also"></a>Veja também

- [Criar filtros com os Serviços de Mídia – REST](filters-dynamic-manifest-rest-howto.md)
- [API REST baseada no Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Próximas etapas

- [Transmitir arquivos com REST](stream-files-tutorial-with-rest.md).  
- [Tutorial: Codificar um arquivo remoto baseado em URL e transmitir o vídeo – REST](stream-files-tutorial-with-rest.md)
