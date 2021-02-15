---
title: 'Tutorial: Iniciar a Leitura Avançada usando os exemplos de código do Android'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai configurar e executar um aplicativo de exemplo do Android que inicia a Leitura Avançada.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.custom: devx-track-java
ms.openlocfilehash: 637b4d988a4845369a441dce55f0043d873879f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88516428"
---
# <a name="tutorial-start-the-immersive-reader-using-the-android-java-code-sample"></a>Tutorial: Iniciar a Leitura Avançada usando o exemplo de código Java para Android

Na [visão geral](./overview.md), você aprendeu sobre o que é a Leitura Avançada e como ela implementa técnicas comprovadas para melhorar a compreensão de leitura para aprendizes do idioma, leitores emergentes e estudantes com diferenças de aprendizado. Este tutorial aborda a criação de um aplicativo Android que inicia a Leitura Avançada. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar e executar um aplicativo para o Android usando um projeto de exemplo.
> * Adquirir um token de acesso.
> * Iniciar a Leitura Avançada com o conteúdo de exemplo.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso de Leitura Avançada configurado para autenticação do Azure Active Directory. Siga [estas instruções](./how-to-create-immersive-reader.md) para a configuração. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do ambiente. Salve a saída da sessão em um arquivo de texto para referência futura.
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

* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [referência de SDK da Leitura Avançada](./reference.md).
* Veja exemplos de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).