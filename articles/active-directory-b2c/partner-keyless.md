---
title: Tutorial para configuração de uma subunidade com Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Tutorial para configuração de um pouco com Azure Active Directory B2C para autenticação sem senha
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c8a5666d373852da5ff79490f435b2d66d5cc6e0
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090359"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>Tutorial: configurar o mínimo com Azure Active Directory B2C

Neste tutorial de exemplo, fornecemos orientações sobre como configurar o Azure Active Directory (AD) B2C com o [less](https://keyless.io/). Com o Azure AD B2C como um provedor de identidade, você pode integrar o sem qualquer um dos seus aplicativos de cliente para fornecer autenticação de senha verdadeira para seus usuários.

A solução de um ZKB de menos baixo **Zero-Knowledge biometria (™)** fornece autenticação multifator sem senha que elimina fraude, phishing e reutilização de credenciais, tudo isso ao mesmo tempo em que aprimora a experiência do cliente e protege sua privacidade.

## <a name="pre-requisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- Um [locatário Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). O locatário deve estar vinculado à sua assinatura do Azure.

- Um locatário de nuvem sem nenhum, obtenha uma [conta de avaliação](https://keyless.io/go)gratuita.

- O aplicativo do autenticador de menos sem instalação no dispositivo do usuário.

## <a name="scenario-description"></a>Descrição do cenário

A integração sem a mesma inclusão inclui os seguintes componentes:

- Azure AD B2C – o servidor de autorização, responsável por verificar as credenciais do usuário, também conhecido como provedor de identidade.

- Aplicativos Web e móveis – seus aplicativos móveis ou Web que você opta por proteger com um e menos Azure AD B2C.

- O aplicativo móvel de qualquer dispositivo – o aplicativo móvel sem dispositivos móveis será usado para autenticação para os aplicativos Azure AD B2C habilitados.

O diagrama de arquitetura a seguir mostra a implementação.

![Imagem mostra o diagrama de arquitetura de menos](./media/partner-keyless/keyless-architecture-diagram.png)

|Etapa | Descrição |
|:-----| :-----------|
| 1. | O usuário chega em uma página de logon. Os usuários selecionam entrar/inscrever-se e entram no nome de usuário
| 2. | O aplicativo envia os atributos de usuário para Azure AD B2C para verificação de identidade.
| 3. | Azure AD B2C coleta os atributos de usuário e envia os atributos para o menos para autenticar o usuário por meio do aplicativo de dispositivo móvel sem dispositivos móveis.
| 4. | A ação menos envia uma notificação por push para o dispositivo móvel do usuário registrado para uma autenticação de preservação de privacidade na forma de uma varredura biométrica facial.
| 5. | Depois que o usuário responde à notificação por push, o usuário recebe ou negou o acesso ao aplicativo cliente com base nos resultados da verificação.

## <a name="integrate-with-azure-ad-b2c"></a>Integrar ao Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Adicionar um novo provedor de identidade

Para adicionar um novo provedor de identidade, siga estas etapas:

1. Entre no **[portal do Azure](https://portal.azure.com/#home)** como o administrador global do seu locatário Azure ad B2C.

2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.

4. Navegue até o **painel**  >  **Azure Active Directory B2C**  >   **provedores de identidade**

5. Selecione **provedores de identidade**.

6. Selecione **Adicionar**.

### <a name="configure-an-identity-provider"></a>Configurar um provedor de identidade

Para configurar um provedor de identidade, siga estas etapas:

1. Selecione o **tipo de provedor de identidade**  >  **OpenID Connect (versão prévia)**
2. Preencha o formulário para configurar o provedor de identidade:

   |Propriedade | Valor |
   |:-----| :-----------|
   | Nome   | Keyless |
   | URL de metadados | Insira o URI do aplicativo de autenticação de menos de hospedagem, seguido pelo caminho específico, como ' https://keyless.auth/.well-known/openid-configuration ' |
   | Segredo do cliente | O segredo associado à instância de autenticação sem chave – não o mesmo que foi configurado antes. Insira uma cadeia de caracteres complexa de sua escolha. Esse segredo será usado posteriormente na configuração de contêiner de chave menos.|
   | ID do Cliente | A ID do cliente. Essa ID será usada mais tarde na configuração do contêiner de subunidade.|
   | Escopo | openid |
   | Tipo de resposta | id_token |
   | Modo de resposta | form_post|

3. Selecione **OK**.

4. Selecione **mapear as declarações deste provedor de identidade**.

5. Preencha o formulário para mapear o provedor de identidade:

   |Propriedade | Valor |
   |:-----| :-----------|
   | UserID    | Da assinatura |
   | Nome de exibição | Da assinatura |
   | Modo de resposta | Da assinatura |

6. Selecione **salvar** para concluir a configuração para seu novo provedor de identidade do OIDC (Open ID Connect).

### <a name="create-a-user-flow-policy"></a>Criar uma política de fluxo de usuário

Agora você deve ver o mesmo como um novo provedor de identidade OIDC listado em seus provedores de identidade B2C.

1. Em seu locatário do Azure AD B2C, em **políticas**, selecione **fluxos de usuário**.

2. Selecione **novo** fluxo de usuário.

3. Selecione **inscrever-se e entrar**, selecione uma **versão** e, em seguida, selecione **criar**.

4. Insira um **nome** para a política.

5. Na seção provedores de identidade, selecione o provedor de identidade sem seu próprio recém-criado.

6. Configure os parâmetros do seu fluxo de usuário. Insira um nome e selecione o provedor de identidade que você criou. Você também pode adicionar o endereço de email. Nesse caso, o Azure não redirecionará o procedimento de logon diretamente para o mesmo, em vez disso, ele mostrará uma tela em que o usuário pode escolher a opção que deseja usar.

7. Deixe o campo **autenticação multifator** como está.

8. Selecione **impor políticas de acesso condicional**

9. Em **atributos de usuário e declarações de token**, selecione **endereço de email** na opção coletar atributo. Você pode adicionar todos os atributos que Azure Active Directory pode coletar sobre o usuário ao lado das declarações que Azure AD B2C pode retornar ao aplicativo cliente.

10. Selecione **Criar**.

11. Após uma criação bem-sucedida, selecione o novo **fluxo de usuário**.

12. No painel esquerdo, selecione **declarações do aplicativo**. Em opções, marque a caixa de seleção **email** e selecione **salvar**.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Abra o locatário Azure AD B2C e, em políticas, selecione estrutura de experiência de identidade.

2. Selecione o SignUpSignIn criado anteriormente.

3. Selecione executar fluxo de usuário e selecione as configurações:

   a. Aplicativo: selecione o aplicativo registrado (exemplo é JWT)

   b. URL de resposta: selecione a URL de redirecionamento

   c. Selecione Executar fluxo de usuário.

4. Percorrer o fluxo de inscrição e criar uma conta

5. Keymenos será chamado durante o fluxo, depois que o atributo de usuário for criado. Se o fluxo estiver incompleto, verifique se o usuário não está salvo no diretório.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introdução às políticas personalizadas no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
