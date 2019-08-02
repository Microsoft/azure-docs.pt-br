---
title: Tutorial – Personalizar a interface das experiências do usuário – Azure Active Directory B2C | Microsoft Docs
description: Saiba como personalizar a interface do usuário de seus aplicativos no Azure Active Directory B2C usando o portal do Azure.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: da7ec020b6f3f4a3b1890695a78fb6bdb363d233
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849373"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Tutorial: Personalizar a interface das experiências do usuário no Azure Active Directory B2C

Para experiências do usuário mais comuns, como inscrição, entrada e edição de perfil, você pode usar os [fluxos dos usuários](active-directory-b2c-reference-policies.md) no Azure AD (Azure Active Directory) B2C. As informações neste tutorial ajudam você a aprender a [personalizar a interface do usuário](customize-ui-overview.md) dessas experiências usando seus próprios arquivos HTML e CSS.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar arquivos de personalização da interface do usuário
> * Atualizar o fluxo de usuário para usar os arquivos
> * Testar a interface do usuário personalizada

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Criar um fluxo de usuário](tutorial-create-user-flows.md) para permitir que os usuários se registrem e entrem no seu aplicativo.

## <a name="create-customization-files"></a>Criar arquivos de personalização

Você cria um contêiner e uma conta de Armazenamento do Azure e coloca os arquivos HTML e CSS básicos no contêiner.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Embora você possa armazenar seus arquivos de várias maneiras, para este tutorial, vai armazená-los no [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md).

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém sua assinatura do Azure. Selecione o **Filtro de diretório e assinatura** no menu superior e escolha o diretório que contém sua assinatura. Esse diretório é diferente daquele que contém o seu locatário B2C do Azure.
3. Escolha Todos os serviços no canto superior esquerdo do portal do Azure, então pesquise e selecione **Contas de armazenamento**.
4. Selecione **Adicionar**.
5. Em **Grupo de recursos**, selecione **Criar novo**, insira um nome para o novo grupo de recursos e, em seguida, clique em **OK**.
6. Insira um nome para a conta de armazenamento. O nome escolhido deverá ser único no Azure, deverá ter entre 3 e 24 caracteres e poderá conter somente números e letras minúsculas.
7. Selecione a localização da conta de armazenamento ou aceite a localização padrão.
8. Aceite todos os outros valores padrão, selecione **Examinar + Criar** e, em seguida, clique em **Criar**.
9. Depois que a conta de armazenamento tiver sido criada, selecione **Ir para recurso**.

### <a name="create-a-container"></a>Criar um contêiner

1. Na página de visão geral da conta de armazenamento, selecione **Blobs**.
2. Selecione **Contêiner**, insira um nome para o contêiner, escolha **Blob (acesso de leitura anônimo somente para blobs)** e, em seguida, clique em **OK**.

### <a name="enable-cors"></a>Habilitar CORS

 O código do Azure AD B2C em um navegador usa uma abordagem moderna e padrão para carregar conteúdo personalizado de uma URL que você especifica em um fluxo de usuário. CORS (compartilhamento de recurso de origem cruzada) permite que recursos restritos em uma página da Web sejam solicitados em outros domínios.

1. No menu, selecione **CORS**.
2. Para **origens permitidas**, insira `https://your-tenant-name.b2clogin.com`. Substitua `your-tenant-name` pelo nome de seu locatário do Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Você precisa usar todas as letras minúsculas ao digitar o nome do seu locatário.
3. Para **métodos permitidos**, selecione ambos `GET` e `OPTIONS`.
4. Para **cabeçalhos permitidos**, digite um asterisco (*).
5. Para **cabeçalhos expostos**, digite um asterisco (*).
6. Para **Idade máxima de**, insira 200.

    ![Página de configuração do CORS no armazenamento de BLOBs do Azure no portal do Azure](./media/tutorial-customize-ui/enable-cors.png)

5. Clique em **Salvar**.

### <a name="create-the-customization-files"></a>Criar os arquivos de personalização

Para personalizar a interface do usuário da experiência de inscrição, você começa criando um arquivo HTML e CSS simples. Você pode configurar seu HTML como quiser, mas deve ter um elemento **div** com um identificador de `api`. Por exemplo, `<div id="api"></div>`. O Azure AD B2C injeta elementos no contêiner `api` quando a página é exibida.

1. Em uma pasta local, crie o seguinte arquivo e altere `your-storage-account` para o nome da conta de armazenamento e `your-container` para o nome do contêiner que você criou. Por exemplo, `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    A página pode ser projetada de qualquer maneira que desejar, mas o elemento div **api** é necessário para qualquer arquivo de personalização de HTML que você criar.

3. Salve o arquivo como *custom-ui.html*.
4. Crie o seguinte CSS simples que centraliza os todos os elementos na página de inscrição ou entrada, incluindo os elementos que o Azure AD B2C injeta.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Salve o arquivo como *Style.css*.

### <a name="upload-the-customization-files"></a>Fazer upload dos arquivos de personalização

Neste tutorial, você armazena os arquivos criados na conta de armazenamento para que o Azure AD B2C possa acessá-los.

1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, então pesquise e selecione **Contas de armazenamento**.
2. Selecione a conta de armazenamento que você criou, selecione **Blobs** e, em seguida, selecione o contêiner que você criou.
3. Selecione **Fazer Upload**, navegue até o arquivo *custom-ui.html* e selecione-o, então clique em **Fazer Upload**.

    ![Carregar página de blob no portal com o botão carregar e os arquivos realçados](./media/tutorial-customize-ui/upload-blob.png)

4. Copie a URL para o arquivo que você carregou para usar posteriormente no tutorial.
5. Repita as etapas 3 e 4 para o arquivo *style.css*.

## <a name="update-the-user-flow"></a>Atualizar o fluxo de usuário

1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
2. Selecione **Fluxos dos usuários (políticas)** e, em seguida, selecione o fluxo de usuário *B2C_1_signupsignin1*.
3. Selecione **Layouts da página** e, em seguida, em **Página de inscrição ou entrada unificada**, clique em **Sim** para **Usar o conteúdo da página personalizada**.
4. Em **URI da página personalizada**, insira o URI do arquivo *custom-ui.html* que você gravou anteriormente.
5. Na parte superior da página, selecione **Salvar**.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. No locatário do Azure AD B2C, selecione **Fluxos do usuário** e selecione o fluxo de usuário *B2C_1_signupsignin1*.
2. Na parte superior da página, clique em **Executar fluxo de usuário**.
3. Clique no botão **Executar fluxo de usuário**.

    ![Executar a página de fluxo de usuário para o fluxo de usuário de inscrição ou de entrada](./media/tutorial-customize-ui/run-user-flow.png)

    Você deverá ver uma página semelhante ao exemplo a seguir com os elementos centralizados com base no arquivo CSS que você criou:

    ![Navegador da Web mostrando a página de inscrição ou de entrada com elementos personalizados da interface do usuário](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar arquivos de personalização da interface do usuário
> * Atualizar o fluxo de usuário para usar os arquivos
> * Testar a interface do usuário personalizada

> [!div class="nextstepaction"]
> [Personalização de idioma no Azure Active Directory B2C](active-directory-b2c-reference-language-customization.md)
