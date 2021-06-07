---
title: 'Tutorial: Hospedar um site estático no Armazenamento de Blobs – Armazenamento do Azure'
description: Saiba como configurar uma conta de armazenamento para hospedagem de sites estáticos e como implantar um site estático no Armazenamento do Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 36cdaa813e0eccb23563301052aee268ab61533a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94888497"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Tutorial: Hospedar um site estático no Armazenamento de Blobs

Neste tutorial, você aprenderá a criar e implantar um site estático no Armazenamento do Azure. Quando terminar, você terá um site estático que os usuários podem acessar publicamente. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar a hospedagem de sites estáticos
> * Implantar um site Olá, Mundo

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Crie uma conta de Armazenamento Standard de uso geral v2. Os sites estáticos não estão disponíveis em nenhum outro tipo de conta de armazenamento.

Este tutorial usa o [Visual Studio Code](https://code.visualstudio.com/download), uma ferramenta gratuita para programadores, para criar o site estático e implantá-lo em uma conta de Armazenamento do Azure.

Depois de instalar o Visual Studio Code, instale a extensão de visualização do Armazenamento do Azure. Essa extensão integra a funcionalidade de gerenciamento do Armazenamento do Azure com o Visual Studio Code. Você usará a extensão para implantar seu site estático no Armazenamento do Azure. Para instalar a extensão:

1. Inicie o Visual Studio Code.
2. Na barra de ferramentas, clique em **Extensões**. Pesquise pelo *Armazenamento do Azure* e selecione a extensão **Armazenamento do Azure** na lista. Em seguida, clique no botão **Instalar** para instalar a extensão.

    ![Instale a extensão do Armazenamento do Azure no VS Code](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/) para começar.

## <a name="configure-static-website-hosting"></a>Configurar a hospedagem de sites estáticos

A primeira etapa é configurar sua conta de armazenamento para hospedar um site estático no portal do Azure. Quando você configura sua conta para hospedagem de site estático, o Armazenamento do Azure cria automaticamente um contêiner chamado *$web*. O contêiner *$web* conterá os arquivos para seu site estático. 

1. Abra o [portal do Azure](https://portal.azure.com/) no navegador da Web. 
1. Localize sua conta de armazenamento e exiba a visão geral dela.
1. Selecione **Site estático** para exibir a página de configuração de sites estáticos.
1. Selecione **Ativado** para habilitar a hospedagem de site estático para a conta de armazenamento.
1. No campo **Nome do documento de índice**, especifique uma página de índice padrão *index.html*. A página de índice padrão é exibida quando um usuário navega para a raiz do site estático.  
1. No campo **Caminho do documento de erro**, especifique uma página de erro padrão *404.html*. A página de erro padrão é exibida quando o usuário tenta navegar até uma página que não existe no site estático.
1. Clique em **Save** (Salvar). Agora, o portal do Azure exibe seu ponto de extremidade do site estático. 

    ![Habilitar a hospedagem de site estático para uma conta de armazenamento](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Implantar um site Olá, Mundo

Em seguida, crie uma página da Web Olá, Mundo com o Visual Studio Code e implante-a no site estático hospedado em sua conta de Armazenamento do Azure.

1. Crie uma pasta vazia chamada *mywebsite* em seu sistema de arquivos local. 
1. Inicie o Visual Studio Code e abra a pasta que você acabou de criar no painel do **Explorer**.

    ![Abra a pasta no Visual Studio Code](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Crie o arquivo de índice padrão na pasta *mywebsite* e nomeie-o *index.html*.

    ![Crie o arquivo de índice padrão no Visual Studio Code](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Abra *index.html* no editor, cole o seguinte texto no arquivo e salve-o:

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>Hello World!</h1>
      </body>
    </html>
    ```

1. Crie o arquivo de erro padrão e nomeie-o *404.html*.
1. Abra *404.html* no editor, cole o seguinte texto no arquivo e salve-o:

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>404</h1>
      </body>
    </html>
    ```

1. Clique com o botão direito do mouse na pasta *mywebsite* no painel do **Explorer** e selecione **Implantar no site estático...** para implantar seu site. Será solicitado que você faça logon no Azure para recuperar uma lista de assinaturas.

1. Selecione a assinatura que contém a conta de armazenamento para a qual você habilitou a hospedagem de site estático. Em seguida, selecione a conta de armazenamento quando for solicitado.

Agora, o Visual Studio Code fará upload de seus arquivos no seu ponto de extremidade da Web e mostrará a barra de status de êxito. Inicie o site para exibi-lo no Azure.

Você concluiu o tutorial e implantou um site estático no Azure com êxito.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar sua conta de Armazenamento do Azure para hospedagem de site estático e a criar e implantar um site estático em um Ponto de Extremidade do Azure.

Em seguida, aprenda a configurar um domínio personalizado com seu site estático.

> [!div class="nextstepaction"]
> [Mapear um domínio personalizado para um ponto de extremidade do Armazenamento de Blobs do Azure](storage-custom-domain-name.md)
