---
title: 'Tutorial: Iniciar a Leitura Avançada usando o exemplo de código do Swift para o iOS'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai configurar e executar um aplicativo de exemplo do Swift que inicia a Leitura Avançada.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: ce9d3af8f7517e2acae5264197b842d47085279c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88516360"
---
# <a name="tutorial-start-the-immersive-reader-using-the-swift-ios-code-sample"></a>Tutorial: Iniciar a Leitura Avançada usando o exemplo de código do Swift para o iOS

Na [visão geral](./overview.md), você aprendeu sobre o que é a Leitura Avançada e como ela implementa técnicas comprovadas para melhorar a compreensão de leitura para aprendizes do idioma, leitores emergentes e estudantes com diferenças de aprendizado. Este tutorial aborda a criação de um aplicativo do iOS que inicia a Leitura Avançada. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar e executar um aplicativo do Swift para o iOS usando um projeto de exemplo.
> * Adquirir um token de acesso.
> * Iniciar a Leitura Avançada com o conteúdo de exemplo.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso de Leitura Avançada configurado para autenticação do Azure Active Directory. Siga [estas instruções](./how-to-create-immersive-reader.md) para a configuração. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do ambiente. Salve a saída da sessão em um arquivo de texto para referência futura.
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

* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [referência de SDK da Leitura Avançada](./reference.md).
* Veja exemplos de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
