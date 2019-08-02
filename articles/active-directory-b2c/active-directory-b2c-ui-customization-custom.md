---
title: Personalizar a interface do usuário do aplicativo usando uma política personalizada no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre como personalizar uma interface do usuário usando uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0a051b0e853b60dfc1f5b6c3453d9ed8361f1748
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67438828"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Personalizar a interface do usuário do aplicativo usando uma política personalizada no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Depois de concluir este artigo, você terá uma política personalizada de inscrição e entrada com sua marca e aparência. Com o Azure Active Directory B2C (Azure AD B2C), você obtém controle quase total do conteúdo HTML e CSS apresentado aos usuários. Ao usar uma política personalizada, a personalização da interface do usuário é configurada em XML em vez de usar controles no portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md). Você deve ter uma política personalizada funcional para inscrição e conexão com contas locais.

## <a name="page-ui-customization"></a>Personalização da interface do usuário da página

Usando o recurso de personalização da interface do usuário da página, você pode personalizar a aparência de qualquer política personalizada. Também pode manter a consistência visual e da marca entre seu aplicativo e o Azure AD B2C.

Veja como ele funciona: O Azure AD B2C executa o código no navegador do cliente e usa uma abordagem moderna chamada [CORS (Compartilhamento de Recursos entre Origens)](https://www.w3.org/TR/cors/). Primeiro, especifique uma URL na política personalizada com um conteúdo personalizado em HTML. O Azure AD B2C mescla os elementos de interface do usuário com o conteúdo HTML carregado da URL e exibe a página para o cliente.

## <a name="create-your-html5-content"></a>Crie seu conteúdo em HTML5

Crie conteúdo em HTML com o nome da marca de seu produto no título.

1. Copie o snippet de HTML a seguir. É um HTML5 bem formado com um elemento vazio chamado *\<div id = "api"\>\</div\>* localizado dentro das marcas *\<body\>* . Esse elemento marca o local em que o conteúdo do Azure AD B2C será inserido.

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

2. Cole o snippet copiado em um editor de texto e salve o arquivo como *customize-ui.html*.

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

1. Sob **serviço Blob** no menu à esquerda, selecione **Blobs**.
2. Clique em **+ contêiner**.
3. Para **nome**, insira *raiz*. Isso pode ser um nome de sua escolha, por exemplo *wingtiptoys*, mas usamos *raiz* neste exemplo, para manter a simplicidade.
4. Para **nível de acesso público**, selecione **Blob**, em seguida, **Okey**.
5. Clique em **raiz** para abrir o novo contêiner.
6. Clique em **Carregar**.
7. Clique no ícone de pasta ao lado de **Selecionar um arquivo**.
8. Navegue até e selecione **UI personalizar** que você criou anteriormente na seção de personalização da UI Page.
9. Se você deseja carregar em uma subpasta, expanda **Advanced** e insira um nome de pasta na **carregar pasta**.
10. Escolha **Carregar**.
11. Selecione o **UI personalizar** blob carregado.
12. À direita do **URL** caixa de texto, selecione o **copiar para área de transferência** ícone para copiar a URL para a área de transferência.
13. No navegador da web, navegue até a URL que você copiou para verificar se o blob carregado é acessível. Se não estiver acessível, por exemplo, se você encontrar um `ResourceNotFound` erro, verifique se o tipo de acesso do contêiner é definido como **blob**.

## <a name="configure-cors"></a>Configurar o CORS

Configure o Armazenamento de nlobs para o Compartilhamento de Recursos entre Origens do Azure fazendo o seguinte:

1. No menu, selecione **CORS**.
2. Para **origens permitidas**, insira `https://your-tenant-name.b2clogin.com`. Substitua `your-tenant-name` pelo nome de seu locatário do Azure AD B2C. Por exemplo: `https://fabrikam.b2clogin.com`. Você precisa usar todas as letras minúsculas ao digitar o nome do seu locatário.
3. Para **métodos permitidos**, selecione ambos `GET` e `OPTIONS`.
4. Para **cabeçalhos permitidos**, digite um asterisco (*).
5. Para **cabeçalhos expostos**, digite um asterisco (*).
6. Para **Idade máxima de**, insira 200.
7. Clique em **Salvar**.

## <a name="test-cors"></a>Testar o CORS

Verifique se você está pronto fazendo o seguinte:

1. Acesse o site [www.test-cors.org](https://www.test-cors.org/) e cole a URL na caixa **URL Remota**.
2. Clique em **Enviar Solicitação**.  
    Se você receber um erro, verifique se as [Configurações do CORS](#configure-cors) estão corretas. Você também pode precisar limpar o cache do navegador ou abrir uma sessão de navegação particular pressionando Ctrl+Shift+P.

## <a name="modify-the-extensions-file"></a>Modificar o arquivo de extensões

Para configurar a personalização da interface do usuário, você deve copiar o **ContentDefinition** e seus elementos filho do arquivo de base para o arquivo de extensões.

1. Abra o arquivo base da sua política. Por exemplo, *TrustFrameworkBase.xml*.
2. Pesquise e copie todo o conteúdo do elemento **ContentDefinitions**.
3. Abra o arquivo de extensão. Por exemplo, *TrustFrameworkExtensions.xml*. Pesquise o elemento **BuildingBlocks**. Se o elemento não existir, adicione-o.
4. Cole todo o conteúdo do elemento **ContentDefinitions** que você copiou como filho do elemento **BuildingBlocks**. 
5. Pesquise o elemento **ContentDefinition** que contém `Id="api.signuporsignin"` no XML copiado.
6. Altere o valor de **LoadUri** para a URL do arquivo HTML que você carregou no armazenamento. Por exemplo: `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.
    
    Sua política personalizada deverá ter a seguinte aparência:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

7. Salve o arquivo de extensões.

## <a name="upload-your-updated-custom-policy"></a>Carregar a política personalizada atualizada

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Estrutura de Experiência de Identidade**.
2. Clique em **Todas as Políticas**.
3. Clique em **Carregar Política**.
4. Carregue o arquivo de extensões que você alterou anteriormente.

## <a name="test-the-custom-policy-by-using-run-now"></a>Teste a política personalizada usando a opção **Executar Agora**

1. Na folha **Azure AD B2C**, acesse **Todas as políticas**.
2. Selecione a política personalizada carregada e clique no botão **Executar agora**.
3. Você deverá conseguir se inscrever usando um endereço de email.

## <a name="reference"></a>Referência

### <a name="sample-templates"></a>Modelos de exemplo
Encontre modelos de exemplo para personalização da interface do usuário aqui:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

A pasta sample_templates/wingtip contém os seguintes arquivos HTML:

| Modelo do HTML5 | DESCRIÇÃO |
|----------------|-------------|
| *phonefactor.html* | Use esse arquivo como modelo para uma página de autenticação multifator. |
| *resetpassword.html* | Use esse arquivo como modelo para uma página de esquecimento de senha. |
| *selfasserted.html* | Use esse arquivo como modelo para uma página de inscrição de conta social ou uma página de inscrição de conta local. |
| *unified.html* | Use esse arquivo como modelo para uma página de inscrição ou entrada unificada. |
| *updateprofile.html* | Use esse arquivo como modelo para uma página de atualização de perfil. |

Aqui estão as etapas sobre como usar o exemplo. 
1. Clone o repositório em seu computador local. Escolha uma pasta de modelo na pasta sample_templates. Você pode usar `wingtip` ou `contoso`.
2. Carregar todos os arquivos sob o `css`, `fonts`, e `images` pastas no armazenamento de Blob, conforme descrito nas seções anteriores. 
3. Em seguida, abra cada \*no arquivo. HTML na raiz de um `wingtip` ou `contoso` (o que você escolheu na primeira etapa) e substitua todas as instâncias de "http://localhost" com as URLs dos arquivos css, imagens e as fontes que você carregou na etapa 2.
4. Salve o \*. HTML, arquivos e carregá-los para o armazenamento de BLOBs.
5. Agora, modifique o arquivo de extensões como mencionado anteriormente na [modificar o arquivo de extensões](#modify-the-extensions-file).
6. Se você vir a ausência de fontes, imagens ou css, verifique se suas referências na política de extensões e a \*arquivos. HTML.

### <a name="content-defintion-ids"></a>IDs de definição de conteúdo

Na seção Modificar sua política personalizada de inscrição ou entrada, você configurou a definição de conteúdo para `api.idpselections`. O conjunto completo de IDs de definição de conteúdo reconhecidas pelo framework de experiência de identidade do Azure AD B2C e suas descrições estão na tabela a seguir:

| ID de definição de conteúdo | DESCRIÇÃO | 
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
