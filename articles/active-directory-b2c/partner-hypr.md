---
title: Tutorial para configurar o Azure Active Directory B2C com o HYPR
titleSuffix: Azure AD B2C
description: Tutorial para configurar Azure Active Directory B2C com Hypr para autenticação de cliente forte sem senha
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b80b1a4b3f9bcde6cf01b0e0e59425c6783bd5d9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953756"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>Tutorial para configurar o HYPR com o Azure Active Directory B2C

Neste tutorial de exemplo, fornecemos orientações sobre como configurar Azure AD B2C com o [HYPR](https://get.hypr.com). Com Azure AD B2C como um provedor de identidade, você pode integrar o HYPR a qualquer um dos seus aplicativos de cliente para fornecer autenticação de senha verdadeira para seus usuários. O HYPR substitui senhas com criptografias de chave pública eliminando fraude, phishing e reutilização de credenciais.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- Um [locatário Azure ad B2C](./tutorial-create-tenant.md). O locatário está vinculado à sua assinatura do Azure.

- Um locatário de nuvem do HYPR, obtenha uma [conta de avaliação](https://get.hypr.com/free-trial)gratuita.

- Um dispositivo móvel do usuário registrado usando as APIs REST do HYPR ou o HYPR Device Manager em seu locatário HYPR. Por exemplo, você pode usar o [SDK do Java do HYPR](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) para realizar essa tarefa.

## <a name="scenario-description"></a>Descrição do cenário

A integração do HYRP inclui os seguintes componentes:

- Azure AD B2C – o servidor de autorização, responsável por verificar as credenciais do usuário, também conhecido como provedor de identidade

- Aplicativos Web e móveis-seus aplicativos móveis ou Web que você escolhe proteger com HYPR e Azure AD B2C. O HYPR fornece um SDK móvel robusto também um aplicativo móvel que pode ser usado em plataformas iOS e Android para fazer a autenticação verdadeira sem senha.

- O aplicativo móvel HYPR-o aplicativo móvel HYPR pode ser usado para executar este exemplo se preferir não usar os SDKs móveis em seus próprios aplicativos móveis.

- APIs REST do HYPR – você pode usar as APIs do HYPR para fazer o registro e a autenticação do dispositivo do usuário. Essas APIs podem ser encontradas [aqui](https://apidocs.hypr.com).

O diagrama de arquitetura a seguir mostra a implementação.

![Captura de tela para hypr-arquitetura-diagrama](media/partner-hypr/hypr-architecture-diagram.png)

|Etapa | Descrição |
|:-----| :-----------|
| 1. | O usuário chega em uma página de logon. Os usuários selecionam entrar/inscrever-se e inserem o nome de usuário na página.
| 2. | O aplicativo envia os atributos de usuário para Azure AD B2C para identificar a verificação.
| 3. | Azure AD B2C coleta os atributos de usuário e envia os atributos para HYPR para autenticar o usuário por meio do aplicativo móvel HYPR.
| 4. | O HYPR envia uma notificação por push para o dispositivo móvel do usuário registrado para uma autenticação certificada do FIDO (Fast Identity online). Pode ser um PIN de impressão, biométrica ou descentralizado do usuário.  
| 5. | Depois que o usuário reconhece a notificação por push, o usuário recebe ou negou o acesso ao aplicativo cliente com base nos resultados da verificação.

## <a name="configure-the-azure-ad-b2c-policy"></a>Configurar a política de Azure AD B2C

1. Vá para a [política de Azure ad B2C HYPR](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy) na pasta de política.

2. Siga este [documento](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) para baixar o [pacote de início do LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Configure a política para o locatário Azure AD B2C.

>[!NOTE]
>Atualize as políticas fornecidas para relacionar-se ao seu locatário específico.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Abra o locatário Azure AD B2C e, em políticas, selecione **estrutura de experiência de identidade**.

2. Selecione o **SignUpSignIn** criado anteriormente.

3. Selecione **executar fluxo de usuário** e selecione as configurações:

   a. **Aplicativo**: selecione o aplicativo registrado (exemplo é JWT)

   b. **URL de resposta**: selecione a **URL de redirecionamento**

   c. Selecione **Executar fluxo de usuário**.

4. Percorrer o fluxo de inscrição e criar uma conta

5. HYPR será chamado durante o fluxo, depois que o atributo de usuário for criado. Se o fluxo estiver incompleto, verifique se o usuário não está salvo no diretório.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md?tabs=applications)