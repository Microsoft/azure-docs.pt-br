---
title: "Personalizar uma interface do usuário usando políticas personalizadas – Azure AD B2C | Microsoft Docs"
description: "Saiba mais sobre como personalizar uma interface do usuário (UI) ao usar políticas personalizadas no Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: d5a3c0a323b31696d39e3d2b36317dec3a2337d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C: Configurar a personalização da interface do usuário em uma política personalizada

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Depois de concluir este artigo, você terá uma política personalizada de inscrição e entrada com sua marca e aparência. Com o Azure Active Directory B2C (Azure AD B2C), você obtém controle quase total do conteúdo HTML e CSS apresentado aos usuários. Ao usar uma política personalizada, a personalização da interface do usuário é configurada em XML em vez de usar controles no portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, conclua as etapas em [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md). Você deve ter uma política personalizada funcional para inscrição e conexão com contas locais.

## <a name="page-ui-customization"></a>Personalização da interface do usuário da página

Usando o recurso de personalização da interface do usuário da página, você pode personalizar a aparência de qualquer política personalizada. Também pode manter a consistência visual e da marca entre seu aplicativo e o Azure AD B2C.

É assim que ela funciona: o Azure AD B2C executa o código no navegador do cliente e usa uma abordagem moderna chamada [CORS (Compartilhamento de Recursos entre Origens)](http://www.w3.org/TR/cors/). Primeiro, especifique uma URL na política personalizada com um conteúdo personalizado em HTML. O Azure AD B2C mescla os elementos de interface do usuário com o conteúdo HTML carregado da URL e exibe a página para o cliente.

## <a name="create-your-html5-content"></a>Crie seu conteúdo em HTML5

Crie conteúdo em HTML com o nome da marca de seu produto no título.

1. Copie o trecho de HTML a seguir. É um HTML5 bem formado com um elemento vazio chamado *\<div id = "api"\>\</div\>* localizado dentro das marcas *\<body\>*. Esse elemento marca o local em que o conteúdo do Azure AD B2C será inserido.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

   >[!NOTE]
   >Por motivos de segurança, bloqueamos a personalização do uso do JavaScript no momento.

2. Cole o trecho copiado em um editor de texto e salve o arquivo como *customize-ui.html*.

## <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de Armazenamento de Blobs do Azure

>[!NOTE]
> Neste artigo, usamos o Armazenamento de Blobs do Azure para hospedar nosso conteúdo. Você pode optar por hospedar seu conteúdo em um servidor Web, mas deve [habilitar CORS em seu servidor Web](https://enable-cors.org/server.html).

Para hospedar esse conteúdo HTML no Armazenamento de Blobs, faça o seguinte:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu **Hub**, selecione **Novo** > **Armazenamento** > **Conta de armazenamento**.
3. Insira um **Nome** exclusivo para sua conta de armazenamento.
4. O **Modelo de implantação** pode permanecer **Gerenciador de Recursos**.
5. Altere **Tipo de Conta** para **Armazenamento de blobs**.
6. O **Desempenho** pode permanecer **Padrão**.
7. A **Replicação** pode permanecer **RA-GRS**.
8. A **Camada de acesso** pode permanecer **Dinâmica**.
9. A **Criptografia do serviço de armazenamento** pode permanecer **Desabilitada**.
10. Selecione uma **Assinatura** para a conta de armazenamento.
11. Crie um **Grupo de recursos** ou selecione um existente.
12. Selecione a **Localização geográfica** para sua conta de armazenamento.
13. Clique em **Criar** para criar a conta de armazenamento.  
    Após a conclusão da implantação, a folha **Conta de armazenamento** será aberta automaticamente.

## <a name="create-a-container"></a>Criar um contêiner

Para criar um contêiner público no armazenamento de Blobs, faça o seguinte:

1. Clique na guia **Visão geral**.
2. Clique em **Contêiner**.
3. Em **Nome**, digite **$root**.
4. Defina **Tipo de acesso** como **Blob**.
5. Clique em **$root** para abrir o novo contêiner.
6. Clique em **Carregar**.
7. Clique no ícone de pasta ao lado de **Selecionar um arquivo**.
8. Acesse **customize-ui.html**, que você criou anteriormente na seção [Personalização da interface do usuário da página](#the-page-ui-customization-feature).
9. Clique em **Carregar**.
10. Selecione o blob customize-ui.html que você carregou.
11. Ao lado de **URL**, clique em **Copiar**.
12. Em um navegador, cole a URL copiada e acesse o site. Se o site não estiver acessível, verifique se o tipo de acesso do contêiner está definido como **blob**.

## <a name="configure-cors"></a>Configurar o CORS

Configure o Armazenamento de nlobs para o Compartilhamento de Recursos entre Origens do Azure fazendo o seguinte:

>[!NOTE]
>Quer experimentar o recurso de personalização da interface do usuário usando nosso conteúdo de HTML e CSS de exemplo? Fornecemos [uma ferramenta auxiliar simples](active-directory-b2c-reference-ui-customization-helper-tool.md) que carrega e configura nosso conteúdo de exemplo em sua conta de armazenamento de Blobs. Se você usar a ferramenta, pule para [Modificar a política personalizada de inscrição ou entrada](#modify-your-sign-up-or-sign-in-custom-policy).

1. Na folha **Armazenamento**, em **Configurações**, abra **CORS**.
2. Clique em **Adicionar**.
3. Para **Origens permitidas**, digite um asterisco (\*).
4. Na lista suspensa **Verbos permitidos**, selecione **OBTER** e **OPÇÕES**.
5. Para **Cabeçalhos permitidos**, digite um asterisco (\*).
6. Para **Cabeçalhos expostos**, digite um asterisco (\*).
7. Para **Idade máxima (segundos)**, digite **200**.
8. Clique em **Adicionar**.

## <a name="test-cors"></a>Testar o CORS

Verifique se você está pronto fazendo o seguinte:

1. Acesse o site [teste-cors.org](http://test-cors.org/) e cole a URL na caixa **URL Remota**.
2. Clique em **Enviar Solicitação**.  
    Se você receber um erro, verifique se as [Configurações do CORS](#configure-cors) estão corretas. Você também pode precisar limpar o cache do navegador ou abrir uma sessão de navegação particular pressionando Ctrl+Shift+P.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>Modificar a política personalizada de inscrição ou conexão

Na marca de nível superior *\<TrustFrameworkPolicy\>*, você deve encontrar a marca *\<BuildingBlocks\>*. Dentro das marcas *\<BuildingBlocks\>*, adicione uma marca *\<ContentDefinitions\>* copiando o exemplo a seguir. Substitua *your_storage_account* pelo nome de sua conta de armazenamento.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>Carregar a política personalizada atualizada

1. No [Portal do Azure](https://portal.azure.com), [alterne para o contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e abra a folha **Azure AD B2C**.
2. Clique em **Todas as Políticas**.
3. Clique em **Carregar Política**.
4. Carregue `SignUpOrSignin.xml` com a marca *\<ContentDefinitions\>* adicionada anteriormente.

## <a name="test-the-custom-policy-by-using-run-now"></a>Teste a política personalizada usando a opção **Executar Agora**

1. Abra a folha **Azure AD B2C**, acesse **Todas as políticas**.
2. Selecione a política personalizada carregada e clique no botão **Executar agora**.
3. Você deverá conseguir se inscrever usando um endereço de email.

## <a name="reference"></a>Referência

Encontre modelos de exemplo para personalização da interface do usuário aqui:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

A pasta sample_templates/wingtip contém os seguintes arquivos HTML:

| Modelo do HTML5 | Descrição |
|----------------|-------------|
| *phonefactor.html* | Use esse arquivo como modelo para uma página de autenticação multifator. |
| *resetpassword.html* | Use esse arquivo como modelo para uma página de esquecimento de senha. |
| *selfasserted.html* | Use esse arquivo como modelo para uma página de inscrição de conta social ou uma página de inscrição de conta local. |
| *unified.html* | Use esse arquivo como modelo para uma página de inscrição ou entrada unificada. |
| *updateprofile.html* | Use esse arquivo como modelo para uma página de atualização de perfil. |

Na seção [Modificar sua política personalizada de inscrição ou entrada](#modify-your-sign-up-or-sign-in-custom-policy), você configurou a definição de conteúdo para `api.idpselections`. O conjunto completo de IDs de definição de conteúdo reconhecidas pelo framework de experiência de identidade do Azure AD B2C e suas descrições estão na tabela a seguir:

| ID de definição de conteúdo | Descrição | 
|-----------------------|-------------|
| *api.error* | **Página de erro**. Essa página é exibida quando uma exceção ou um erro é encontrado. |
| *api.idpselections* | **Página de seleção de provedor de identidade**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição. Essas opções são os provedores de identidade empresarial, provedores de identidade social, como Facebook e Google+, ou contas locais. |
| *api.idpselections.signup* | **Seleção de provedor de identidade para inscrição**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição. Essas opções são os provedores de identidade empresarial, provedores de identidade social, como Facebook e Google+, ou contas locais. |
| *api.localaccountpasswordreset* | **Página de esquecimento de senha**. Esta página contém um formulário que o usuário precisa preencher para iniciar sua redefinição de senha.  |
| *api.localaccountsignin* | **Página de entrada da conta local**. Esta página contém um formulário de entrada para entradas com uma conta local baseada em endereço de email ou nome de usuário. O formulário pode conter uma caixa de entrada de texto e caixa de entrada de senha. |
| *api.localaccountsignup* | **Página de inscrição da conta local**. Esta página contém um formulário de inscrição para inscrições com uma conta local baseada em endereço de email ou nome de usuário. O formulário pode conter diferentes controles de entrada como caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla. |
| *api.phonefactor* | **Página de autenticação multifator**. Nesta página, os usuários podem verificar seus números de telefone (usando mensagem de texto ou por voz) durante a inscrição ou entrada. |
| *api.selfasserted* | **Página de inscrição de conta social**. Esta página contém um formulário de inscrição que deve ser preenchido pelos usuários ao se inscreverem usando uma conta existente de um provedor de identidade social, como o Facebook ou Google+. Esta página é semelhante à página de inscrição de conta social anterior, exceto os campos de entrada de senha. |
| *api.selfasserted.profileupdate* | **Página de atualização de perfil**. Esta página contém um formulário que os usuários podem usar para atualizar o perfil. Esta página é semelhante à página de inscrição de conta social, exceto os campos de entrada de senha. |
| *api.signuporsignin* | **Página de inscrição ou entrada unificada**. Esta página controla tanto a inscrição quanto a entrada de usuários que podem usar provedores de identidade empresarial ou provedores de identidade social, como Facebook, Google+ ou contas locais.  |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre quais elementos de interface do usuário podem ser personalizados, confira [Guia de referência para personalização da interface do usuário para políticas internas](active-directory-b2c-reference-ui-customization.md) .
