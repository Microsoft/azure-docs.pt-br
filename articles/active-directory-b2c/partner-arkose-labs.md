---
title: Arkose Labs com Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação de Azure AD B2C com o arkose Labs para ajudar a proteger contra ataques de bot, ataques de tomada de conta e aberturas de contas fraudulentas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2c7eea87101a36edb0d77026489ea351b601158b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584588"
---
# <a name="tutorial-for-configuring-arkose-labs-with-azure-active-directory-b2c"></a>Tutorial para configurar o arkose Labs com o Azure Active Directory B2C

Neste tutorial, saiba como integrar a autenticação de Azure AD B2C com o arkose Labs. O arkose Labs ajuda as organizações contra ataques de bot, ataques de tomada de conta e aberturas de contas fraudulentas.  

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* [Um locatário Azure ad B2C](tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.

## <a name="scenario-description"></a>Descrição do cenário

O diagrama a seguir descreve como o arkose Labs se integra ao Azure AD B2C.

![Diagrama da arquitetura do arkose Labs](media/partner-arkose-labs/arkose-architecture-diagram.png)

| Etapa  | Descrição |
|---|---|
|1     | Um usuário entra com uma conta criada anteriormente. Quando o usuário seleciona Enviar, um desafio de imposição do arkose Labs é exibido. Depois que o usuário concluir o desafio, o status será enviado para o arkose Labs para gerar um token.        |
|2     |  O arkose Labs envia o token de volta para Azure AD B2C.       |
|3     |  Antes que o formulário de entrada seja enviado, o token é enviado para o arkose Labs para verificação.       |
|4     |  Arkose retorna um resultado de êxito ou falha do desafio.       |
|5     |  Se o desafio for concluído com êxito, um formulário de entrada será enviado para Azure AD B2C e Azure AD B2C concluirá a autenticação.       |
|   |   |

## <a name="onboard-with-arkose-labs"></a>Integração com o arkose Labs

1. Comece entrando em contato com o [arkose Labs](https://www.arkoselabs.com/book-a-demo/) e criando uma conta.

2. Depois que sua conta for criada, navegue até https://dashboard.arkoselabs.com/login .

3. No painel, navegue até configurações de site para localizar sua chave pública e chave privada. Essas informações serão necessárias posteriormente para configurar Azure AD B2C.

## <a name="integrate-with-azure-ad-b2c"></a>Integrar ao Azure AD B2C

### <a name="part-1--create-blob-storage-to-store-the-custom-html"></a>Parte 1 – criar o armazenamento de BLOBs para armazenar o HTML personalizado

Para criar uma conta de armazenamento, siga estas etapas:  

1. Entre no portal do Azure.

2. Certifique-se de usar o diretório que contém sua assinatura do Azure. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém sua assinatura. Esse diretório é diferente daquele que contém o seu locatário B2C do Azure.

3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione  **contas de armazenamento**.

4. Selecione **Adicionar**.

5. Em  **grupo de recursos**, selecione  **criar novo**, insira um nome para o novo grupo de recursos e, em seguida, selecione **OK**.

6. Insira um nome para a conta de armazenamento. O nome escolhido deverá ser único no Azure, deverá ter entre 3 e 24 caracteres e poderá conter somente números e letras minúsculas.

7. Selecione a localização da conta de armazenamento ou aceite a localização padrão.

8. Aceite todos os outros valores padrão, selecione   **revisar & criar**  >  **criar**.

9. Depois que a conta de armazenamento tiver sido criada, selecione  **Ir para recurso**.

#### <a name="create-a-container"></a>Criar um contêiner

1. Na página Visão geral da conta de armazenamento, selecione  **BLOBs**.

2. Selecione   **contêiner**, insira um nome para o contêiner, escolha   **blob** (acesso de leitura anônimo somente para BLOBs) e, em seguida, selecione **OK**.

#### <a name="enable-cross-origin-resource-sharing-cors"></a>Habilitar o CORS (compartilhamento de recursos entre origens)

O código do Azure AD B2C em um navegador usa uma abordagem moderna e padrão para carregar conteúdo personalizado de uma URL que você especifica em um fluxo de usuário. O CORS permite que recursos restritos em uma página da Web sejam solicitados de outros domínios.

1. No menu, selecione  **CORS**.

2. Para  **origens permitidas**, insira  `https://your-tenant-name.b2clogin.com` . Substitua seu-Tenant-Name pelo nome do seu locatário de Azure AD B2C. Por exemplo,  `https://fabrikam.b2clogin.com`. Use todas as letras minúsculas ao inserir o nome do locatário.

3. Para  **métodos permitidos**, selecione  **obter**, **colocar** e  **Opções**.

4. Para **cabeçalhos permitidos**, digite um asterisco (*).

5. Para **cabeçalhos expostos**, insira um asterisco (*).

6. Para **Idade máxima de**, insira 200.

   ![Inscrever-se e entrar no arkose Labs](media/partner-arkose-labs/signup-signin-arkose.png)

7. Selecione **Salvar**.

### <a name="part-2--set-up-a-back-end-server"></a>Parte 2 – configurar um servidor back-end

Baixe o Git bash e siga as etapas abaixo:

1. Siga as instruções para [criar um aplicativo Web](../app-service/quickstart-php.md), até a mensagem "Parabéns!Você implantou seu primeiro aplicativo PHP no serviço de aplicativo "exibe.

2. Abra a pasta local e renomeie o arquivo **index. php** para **Verify-token. php**.

3. Abra o arquivo recentemente renomeado Verify-token. php e:

   a. Substitua o conteúdo pelo conteúdo do arquivo Verify-token. php encontrado no [repositório do GitHub](https://github.com/ArkoseLabs/Azure-AD-B2C).

   b. Substitua <private_key> na linha 3 pela sua chave privada obtida no painel do arkose Labs.

4. Na janela do terminal local, confirme suas alterações no git e, em seguida, envie por push as alterações de código para o Azure digitando o seguinte no git bash:

   ``git commit -am "updated output"``

   ``git push azure main``  

### <a name="part-3---final-setup"></a>Parte 3 – instalação final

#### <a name="store-the-custom-html"></a>Armazenar o HTML personalizado

1. Abra o arquivo index.html armazenado no [repositório GitHub](https://github.com/ArkoseLabs/Azure-AD-B2C).

2. Substitua todas as instâncias de `<tenantname>` pelo seu nome de locatário b2C (em outras palavras `<tenantname>.b2clogin.com` ). Deve haver quatro instâncias.

3. Substitua o `<appname>` pelo nome do aplicativo que você criou na parte 2, etapa 1.

   ![Captura de tela mostrando arkose Labs CLI do Azure](media/partner-arkose-labs/arkose-azure-cli.png)

4. Substitua o `<public_key>` na linha 64 pela chave pública obtida do painel do arkose Labs.

5. Carregue o index.htmarquivo l no armazenamento de BLOBs criado acima.

6. Vá para   >  carregamento do **contêiner** de armazenamento  >  .

#### <a name="set-up-azure-ad-b2c"></a>Configurar Azure AD B2C

> [!NOTE]
> Se ainda não tiver um, você precisará [criar um locatário do Azure AD B2C](tutorial-create-tenant.md) que esteja vinculado à sua assinatura do Azure.

1. Crie um fluxo de usuário com base nas informações [aqui](tutorial-create-user-flows.md). Pare quando chegar à seção **testar o fluxo do usuário**.

2. Habilite o JavaScript em seu [fluxo de usuário](javascript-and-page-layout.md).

3. Na mesma página fluxo de usuário, habilitar URL de página personalizada: ir para **fluxo de usuário**  >  **layout de página**  >  **usar conteúdo de página personalizada**  =  **Sim**  >  **Inserir URL de página personalizada**.
Essa URL de página personalizada é obtida do local do index.htmarquivo l dentro do armazenamento de BLOBs  

   ![Captura de tela mostrando a URL de armazenamento do arkose Labs](media/partner-arkose-labs/arkose-storage-url.png)

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Abra o locatário Azure AD B2C e, em **políticas**, selecione **fluxos de usuário**.

2. Selecione o fluxo de usuário criado anteriormente.

3. Selecione **executar fluxo de usuário** e selecione as configurações:

   a. **Aplicativo** – selecione o aplicativo registrado (exemplo é JWT).

   b. **URL de resposta** -selecione a URL de redirecionamento.

   c. Selecione **Executar fluxo de usuário**.

4. Percorra o fluxo de inscrição e crie uma conta.

5. Saia.

6. Percorra o fluxo de entrada.

7. Um quebra-cabeça do arkose Labs será exibido depois que você selecionar **continuar**.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](custom-policy-get-started.md?tabs=applications)