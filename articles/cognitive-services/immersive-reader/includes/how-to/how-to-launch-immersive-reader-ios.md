---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: a3345ff9a6cc1d5f8e2fbc78c2a76ba6f183aeb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102619997"
---
## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Um recurso de Leitura Avançada configurado para autenticação do Azure Active Directory. Siga [estas instruções](../../how-to-create-immersive-reader.md) para a configuração.  Você precisará de alguns dos valores criados aqui ao configurar as propriedades do ambiente. Salve a saída da sessão em um arquivo de texto para referência futura.
* [macOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/).
* [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Configurar as credenciais de autenticação

1. Abra o Xcode e **immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**.
1. No menu superior, selecione **Produto** > **Esquema** > **Editar Esquema**.
1. Na exibição **Executar**, selecione a guia **Argumentos**.
1. Na seção **Variáveis de Ambiente**, adicione os nomes e os valores a seguir. Forneça os valores especificados ao criar o recurso de Leitura Avançada.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

Não faça commit dessa alteração no controle do código-fonte porque ela contém segredos que não devem ser disponibilizados publicamente.

## <a name="start-the-immersive-reader-with-sample-content"></a>Iniciar a Leitura Avançada com o conteúdo de exemplo

No Xcode, selecione **CTRL+R** para executar o projeto.

## <a name="next-steps"></a>Próximas etapas

* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [referência de SDK da Leitura Avançada](../../reference.md).
* Veja exemplos de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
