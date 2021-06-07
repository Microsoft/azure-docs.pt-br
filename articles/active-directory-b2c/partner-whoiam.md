---
title: Tutorial para configurar o Azure Active Directory B2C com o WhoIAM
titleSuffix: Azure AD B2C
description: Neste tutorial, saiba como integrar a autenticação de Azure AD B2C com o WhoIAM para verificação do usuário.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: d1b2c7513562e951e1098cf327780387ddf6a495
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953535"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Tutorial para configurar o WhoIAM com o Azure Active Directory B2C

Neste tutorial de exemplo, fornecemos orientações sobre como configurar o BRIMS (sistema de gerenciamento de identidades com marca [WhoIAM](https://www.whoiam.ai/brims/) ) no seu ambiente e integrá-lo com Active Directory B2C (Azure ad B2C).

BRIMS é um conjunto de aplicativos e serviços que é implantado em seu ambiente. Ele fornece a verificação de voz, SMS e email da sua base de usuários. O BRIMS trabalha em conjunto com sua solução de gerenciamento de identidade e acesso existente e é independente da plataforma.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- [Um locatário Azure ad B2C](./tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.

- Uma [conta de avaliação](https://www.whoiam.ai/contact-us/)do WhoIAM.

## <a name="scenario-description"></a>Descrição do cenário

A integração do WhoIAM inclui os seguintes componentes:

- Um locatário Azure AD B2C. É o servidor de autorização que verifica as credenciais do usuário com base em políticas personalizadas definidas. Ele também é conhecido como provedor de identidade.

- Um portal de administração para gerenciar clientes e suas configurações.

- Um serviço de API que expõe vários recursos por meio de pontos de extremidade.  

- Azure Cosmos DB, que atua como o back-end para o portal de administração do BRIMS e o serviço de API.

O diagrama de arquitetura a seguir mostra a implementação.

![Diagrama da arquitetura da integração do Azure AD B2C com o WhoIAM.](media/partner-whoiam/whoiam-architecture-diagram.png)

|Etapa | Descrição |
|:-----| :-----------|
| 1. | O usuário chega em uma página para iniciar a solicitação de inscrição ou de entrada para um aplicativo que usa Azure AD B2C como seu provedor de identidade.
| 2. | Como parte da autenticação, o usuário solicita a verificação da propriedade de seu email ou telefone ou usa sua voz como um fator de verificação biométrica.  
| 3. | Azure AD B2C faz uma chamada para o serviço de API BRIMS e passa o endereço de email do usuário, o número de telefone e a gravação de voz.
| 4. | O BRIMS usa configurações predefinidas como email totalmente personalizável e modelos de SMS para interagir com o usuário em sua respectiva linguagem de forma consistente com o estilo do aplicativo.
| 5. | Depois que a verificação de identidade de um usuário for concluída, BRIMS retornará um token para Azure AD B2C para indicar o resultado da verificação. O Azure AD B2C concede ao usuário acesso ao aplicativo ou falha na tentativa de autenticação.  

## <a name="sign-up-with-whoiam"></a>Inscreva-se com o WhoIAM

1. Entre em contato com o [WhoIAM](https://www.whoiam.ai/contact-us/) e crie uma conta do BRIMS.

2. Use as diretrizes de inscrição disponibilizadas para você e configure os seguintes serviços do Azure:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): usado para armazenamento seguro de senhas, como senhas de serviço de email.

    - [Serviço de Azure app](https://azure.microsoft.com/services/app-service/): usado para hospedar a API do BRIMS e os serviços do portal de administração.

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/): usado para autenticar usuários administrativos para o portal de administração.

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): usado para armazenar e recuperar as configurações.

    - [Application insights](../azure-monitor/app/app-insights-overview.md) (opcional): usado para fazer logon na API e no portal de administração.

3. Implante a API do BRIMS e o portal de administração do BRIMS em seu ambiente do Azure.

4. Azure AD B2C exemplos de política personalizada estão disponíveis na documentação de inscrição do BRIMS. Siga a documentação para configurar seu aplicativo e usar a plataforma BRIMS para verificação de identidade do usuário.  

Para obter mais informações sobre o BRIMS da WhoIAM, consulte a [documentação do produto](https://www.whoiam.ai/brims/).

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Abra o locatário Azure AD B2C. Em **Políticas**, selecione **Identity Experience Framework**.

2. Selecione o **SignUpSignIn** criado anteriormente.

3. Selecione **executar fluxo de usuário** e, em seguida:

   a. Para **aplicativo**, selecione o aplicativo registrado (o exemplo é JWT).

   b. Para **URL de resposta**, selecione a **URL de redirecionamento**.

   c. Selecione **Executar fluxo de usuário**.

4. Percorra o fluxo de inscrição e crie uma conta.

5. O serviço BRIMS será chamado durante o fluxo, depois que o atributo de usuário for criado. Se o fluxo estiver incompleto, verifique se o usuário não está salvo no diretório.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md?tabs=applications)