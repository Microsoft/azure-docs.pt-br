---
title: itsme OpenID Connect com Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação Azure AD B2C com o itsme OIDC usando a política de fluxo de usuário client_secret. o itsme é um aplicativo de ID digital. Ele permite que você faça logon com segurança sem leitores de cartão, senhas, autenticação de dois fatores e vários códigos de PIN.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba7875caa6a1db7638bfeafcfea1efa7b2462152
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87489508"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>Configurar o itsme OpenID Connect (OIDC) com Azure Active Directory B2C

O aplicativo de ID digital do ITSM permite que você entre com segurança sem leitores de cartão, senhas, autenticação de dois fatores ou vários códigos de PIN. O aplicativo de ITSM fornece uma autenticação de cliente forte com uma identidade verificada. Neste artigo, saiba como integrar Azure AD B2C autenticação com o itsme OpenID Connect (OIDC) usando uma política de fluxo de usuário de segredo do cliente.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* [Um locatário Azure ad B2C](tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.
* Sua ID do cliente, também conhecida como código de parceiro, fornecida pelo ITSM.
* Seu código de serviço fornecido pelo itsme.
* O segredo do cliente para sua conta de ITSM.

## <a name="scenario-description"></a>Descrição do cenário

![diagrama de arquitetura de ITSM](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| Etapa | Descrição |
|------|------|
|1     | No seu site ou aplicativo, inclua o botão **fazer logon com o ITSM** adaptando o fluxo de usuário do Azure ad B2C. O fluxo de interação é iniciado quando o usuário clica nesse botão.  |
|2     | Azure AD B2C inicia o fluxo do OpenID Connect enviando uma solicitação de autorização para a API de segredo do cliente de ITSM. Um ponto de extremidade bem conhecido/OpenID-Configuration está disponível contendo informações sobre os pontos de extremidade.  |
|3     | O ambiente de ITSM redireciona o usuário para a página do ITSM identificar você mesmo, permitindo que o usuário Preencha seu número de telefone.  |
|4     | O ambiente de ITSM recebe o número de telefone do usuário e valida a exatidão.  |
|5     | Se o número de telefone pertencer a um usuário de ITSM ativo, uma ação será criada para o aplicativo de ITSM.  |
|6     | O usuário abre o aplicativo ITSM, verifica a solicitação e confirma a ação.  |
|7     |  O aplicativo informa ao ambiente de ITSM que a ação foi confirmada. |
|8     |  O ambiente de ITSM retorna o código de autorização do OAuth para Azure AD B2C. |
|9     |  Usando o código de autorização, o Azure AD B2C faz uma solicitação de token. |
| 10 | O ambiente de ITSM verifica a solicitação de token e, se ainda for válida, retorna o token de acesso OAuth e o token de ID que contém as informações de usuário solicitadas. |
| 11 | Por fim, o usuário é redirecionado para a URL de redirecionamento como um usuário autenticado.  |
|   |   |

## <a name="onboard-with-itsme"></a>Integração com o ITSM

1. Para criar uma conta com o ITSM, visite ITSM no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace).

2. Ative sua conta de ITSM, enviando um email para onboarding@itsme.be . Você receberá um código de **parceiro** e um **código de serviço** que serão necessários para a configuração do B2C.

3. Após a ativação de sua conta de parceiro de ITSM, você receberá um email com um link de uso único para o **segredo do cliente**.

4. Siga as instruções em [ITSM](https://business.itsme.be/en) para concluir a configuração.

## <a name="integrate-with-azure-ad-b2c"></a>Integrar ao Azure AD B2C

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>Configurar um novo provedor de identidade no Azure AD B2C

> [!NOTE]
> Se ainda não tiver um, você precisará [criar um locatário do Azure AD B2C](tutorial-create-tenant.md) que esteja vinculado à sua assinatura do Azure.

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário do Azure AD B2C.

2. Em **Serviços do Azure**, selecione **Azure ad B2C** (ou selecione **mais serviços** e use a caixa de pesquisa **todos os serviços** para procurar *Azure ad B2C*).

3. Selecione **Provedores de identidade** e **Novo provedor do OpenID Connect**.

4. Preencha o formulário com as seguintes informações:

   |Propriedade | Valor |
   |------------ |------- |
   | Nome | itsme |
   | URL de metadados | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>onde `<environment>` é `e2e` (ambiente de teste) ou `prd` (produção)  |
   | ClientID     | Sua **ID do cliente**, também conhecida como **código de parceiro**  |
   | Segredo do cliente | Seu **client_secret** |
   | Escopo  | serviço OpenID: email do perfil YOURSERVICECODE [telefone] [endereço]  |
   |Tipo de Resposta | code |
   |Modo de resposta | Consulta |
   |Dica de domínio | *Você pode deixar isso em branco* |
   |UserID | sub |
   |Nome de exibição | name |
   |Nome | given_name |
   |Sobrenome | family_name |
   |Email | email|

5. Selecione **Salvar**.

### <a name="configure-a-user-flow"></a>Configurar um fluxo de usuário

1. Em seu locatário do Azure AD B2C, em **políticas**, selecione **fluxos de usuário**.

2. Selecione **novo fluxo de usuário**.

3. Selecione **inscrever-se e entrar**, selecione uma versão e, em seguida, selecione **criar**.

4. Insira um **Nome**.

5. Na seção **provedores de identidade** , selecione **ITSM**.

6. Selecione **Criar**.

7. Abra o fluxo de usuário recém-criado selecionando o nome do fluxo do usuário.

8. Selecione **Propriedades** e ajuste os seguintes valores:

   * Alterar o **acesso & tempos de vida do token de ID (minutos)** para **5**.
   * Alterar o **tempo de vida da janela deslizante do token de atualização** para **sem expiração**.

### <a name="register-an-application"></a>Registrar um aplicativo

1. Em seu locatário B2C, em **gerenciar**, selecione **registros de aplicativo**  >  **novo registro**.

2. Forneça um **nome** para o aplicativo e insira seu **URI de redirecionamento**. Para fins de teste, insira `https://jwt.ms` .

3. Verifique se a autenticação multifator está **desabilitada**.

4. Selecione **Registrar**.

   a. Para fins de teste, selecione **autenticação** e, em **concessão implícita**, marque as caixas de seleção **tokens de acesso** e **tokens de ID** .  

   b. Clique em **Salvar**.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Em seu locatário B2C, em **políticas** , selecione **fluxos de usuário**.

2. Selecione o fluxo de usuário criado anteriormente.

3. Selecione **Executar fluxo de usuário**.

   a. **Aplicativo**: *Selecione o aplicativo registrado*

   b. **URL de resposta**: *Selecione a URL de redirecionamento*

4. A página se **identificar** no ITSM é exibida.  

5. Insira seu número de telefone celular e selecione **Enviar**.

6. Confirme a ação no aplicativo itsme.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

* [Políticas personalizadas no Azure AD B2C](custom-policy-overview.md)

* [Introdução às políticas personalizadas no Azure AD B2C](custom-policy-get-started.md?tabs=applications)