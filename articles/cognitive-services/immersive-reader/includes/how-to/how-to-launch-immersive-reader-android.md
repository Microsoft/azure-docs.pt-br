---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: d5a483cb239893d04d2c2581fb378f411878f4ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102619984"
---
## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Um recurso de Leitura Avançada configurado para autenticação do Azure Active Directory. Siga [estas instruções](../../how-to-create-immersive-reader.md) para a configuração.  Você precisará de alguns dos valores criados aqui ao configurar as propriedades do ambiente. Salve a saída da sessão em um arquivo de texto para referência futura.
* [Git](https://git-scm.com/).
* [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk).
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>Configurar as credenciais de autenticação

1. Inicie o Android Studio e abra o projeto por meio do diretório **immersive-reader-sdk/js/samples/quickstart-java-android** (Java) ou do diretório **immersive-reader-sdk/js/samples/quickstart-kotlin** (Kotlin).

1. Crie um arquivo chamado **env** dentro da pasta **/assets**. Adicione os nomes e os valores a seguir e forneça os valores, conforme apropriado. Não faça commit desse arquivo no controle do código-fonte, pois ele contém segredos que não devem ser disponibilizados publicamente.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>Iniciar a Leitura Avançada com o conteúdo de exemplo

Escolha um emulador de dispositivo no Gerenciador de AVD e execute o projeto.

## <a name="next-steps"></a>Próximas etapas

* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [referência de SDK da Leitura Avançada](../../reference.md).
* Veja exemplos de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).