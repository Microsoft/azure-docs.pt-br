---
title: Trusona e Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Saiba como adicionar o Trusona como um provedor de identidade no Azure AD B2C para habilitar a autenticação com senha.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 99368da8b4018e93ad537e4722ffefd476e61291
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573680"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>Integração do Trusona com o Azure Active Directory B2C

O Trusona é um provedor de ISV (fornecedor independente de software) que ajuda a proteger a entrada habilitando a autenticação sem senha, a autenticação multifator e a verificação de licença digital. Neste artigo, você aprenderá a adicionar o Trusona como um provedor de identidade no Azure AD B2C para habilitar a autenticação com senha.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* [Um locatário Azure ad B2C](tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.
* Uma [conta de avaliação](https://www.trusona.com/) em Trusona

## <a name="scenario-description"></a>Descrição do cenário

Nesse cenário, Trusona atua como um provedor de identidade para Azure AD B2C habilitar a autenticação com senha. Os seguintes componentes compõem a solução:

* Uma Azure AD B2C política de entrada e inscrição combinada
* Trusona adicionado ao Azure AD B2C como um provedor de identidade
* O aplicativo Trusona baixável

![Diagrama de arquitetura do Trusona](media/partner-trusona/trusona-architecture-diagram.png)

| Etapa | Descrição |
|------|------|
|1     | Um usuário tenta entrar no ou inscrever-se no aplicativo. O usuário é autenticado por meio da política de inscrição e entrada do Azure AD B2C. Durante a inscrição, o endereço de email verificado anteriormente do usuário do aplicativo Trusona é usado.     |
|2     | O Azure B2C redireciona o usuário para o provedor de identidade Trusona OpenID Connect (OIDC) usando o fluxo implícito.     |
|3     | Para logons baseados em Desktop PC, o Trusona exibe um código QR exclusivo, sem estado, animado e dinâmico para verificação com o aplicativo Trusona. Para logons baseados em dispositivos móveis, o Trusona usa um "link profundo" para abrir o aplicativo Trusona. Esses dois métodos são usados para o dispositivo e, finalmente, para a descoberta de usuários.     |
|4     | O usuário examina o código QR exibido com o aplicativo Trusona.     |
|5     | A conta do usuário é encontrada no serviço de nuvem Trusona e a autenticação está preparada.     |
|6     | O serviço de nuvem Trusona emite um desafio de autenticação para o usuário por meio de uma notificação por push enviada ao aplicativo Trusona:<br>a. O usuário receberá uma solicitação com o desafio de autenticação. <br> b. O usuário opta por aceitar ou rejeitar o desafio. <br> c. O usuário é solicitado a usar a segurança do sistema operacional (por exemplo, biométrica, senha, PIN ou padrão) para confirmar e assinar o desafio com uma chave privada no ambiente seguro de execução de enclave/confiável. <br> d. O aplicativo Trusona gera uma carga de anti-repetição dinâmica com base nos parâmetros da autenticação em tempo real. <br> e. Toda a resposta é assinada (por uma segunda vez) por uma chave privada no ambiente de execução seguro enclave/confiável e retornada ao serviço de nuvem do Trusona para verificação.      |
|7     |  O serviço de nuvem Trusona redireciona o usuário de volta para o aplicativo inicial com um id_token. Azure AD B2C verifica o id_token usando a configuração de OpenID publicada pelo Trusona conforme configurado durante a instalação do provedor de identidade.    |
|  |  |

## <a name="onboard-with-trusona"></a>Integração com o Trusona

1. Preencha o [formulário](https://www.trusona.com/) para criar uma conta do Trusona e comece.

2. Baixe o aplicativo móvel Trusona da App Store. Instale o aplicativo e Registre seu email.

3. Verifique seu email por meio do "link mágico" seguro enviado pelo software.  

4. Vá para o [painel do desenvolvedor do Trusona](https://dashboard.trusona.com) para autoatendimento.

5. Selecione estou **pronto** e autentique-se com seu aplicativo Trusona.

6. No painel de navegação à esquerda, escolha **integrações do OIDC**.

7. Selecione **criar integração do OpenID Connect**.

8. Forneça um **nome** de sua escolha e use as informações de domínio fornecidas anteriormente (por exemplo, contoso) no **campo host de redirecionamento de cliente**.  

   > [!NOTE]
   > O nome de domínio inicial de Azure Active Directory é usado como o host de redirecionamento de cliente.

9. Siga as instruções no [Guia de integração do Trusona](https://docs.trusona.com/integrations/aad-b2c-integration/). Quando solicitado, use o nome de domínio inicial (por exemplo, contoso) referenciado na etapa anterior.  

## <a name="integrate-with-azure-ad-b2c"></a>Integrar ao Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Adicionar um novo provedor de identidade

> [!NOTE]
> Se ainda não tiver um, você precisará [criar um locatário do Azure AD B2C](tutorial-create-tenant.md) que esteja vinculado à sua assinatura do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.

2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.

4. Navegue até o **painel**  >  **Azure Active Directory B2C**  >  **provedores de identidade**.

3. Selecione **provedores de identidade**.

4. Selecione **Adicionar**.

### <a name="configure-an-identity-provider"></a>Configurar um provedor de identidade  

1. Selecione **tipo**  >  **de provedor de identidade OpenID Connect (versão prévia)**.

2. Preencha o formulário para configurar o provedor de identidade:  

   | Propriedade | Valor  |
   | :--- | :--- |
   | URL de metadados | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | ID do Cliente | Receberá um email de Trusona |
   | Escopo | Email de perfil de OpenID |
   | Tipo de resposta | Id_token |
   | Modo de resposta  | Form_post |

3. Selecione **OK**.  

4. Selecione **mapear as declarações deste provedor de identidade**.  

5. Preencha o formulário para mapear o provedor de identidade:

   | Propriedade | Valor  |
   | :--- | :--- |
   | UserID | Sub  |
   | Nome de exibição | apelido |
   | Nome | given_name |
   | Sobrenome | Family_name |
   | Modo de resposta | email |

6. Selecione **OK** para concluir a configuração para seu novo provedor de identidade OIDC.

### <a name="create-a-user-flow-policy"></a>Criar uma política de fluxo de usuário

Agora você deve ver Trusona como um **novo provedor de identidade do OpenID Connect** listado em seus provedores de identidade do B2C.

1. Em seu locatário do Azure AD B2C, em **políticas**, selecione **fluxos de usuário**.

1. Selecione **novo fluxo de usuário**.

1. Selecione **inscrever-se e entrar**, selecione uma versão e, em seguida, selecione **criar**.

1. Insira um **nome** para a política.

1. Na seção **provedores de identidade** , selecione seu provedor de **identidade Trusona** recém-criado.

   > [!NOTE]
   > Como Trusona é inerentemente multifator, é melhor deixar a autenticação multifator desabilitada.

1. Selecione **Criar**.

1. Em **atributos de usuário e declarações**, escolha **Mostrar mais**. No formulário, selecione pelo menos um atributo que você especificou durante a configuração do seu provedor de identidade na seção anterior.

1. Selecione **OK**.  

### <a name="test-the-policy"></a>Testar a política

1. Selecione sua política recém-criada.

2. Selecione **Executar fluxo de usuário**.

3. No formulário, insira a URL de resposta.

4. Selecione **Executar fluxo de usuário**. Você deve ser redirecionado para o gateway Trusona OIDC. No gateway Trusona, digitalize o código QR seguro exibido com o aplicativo Trusona ou com um aplicativo personalizado usando o SDK do Trusona Mobile.

5. Depois de verificar o código QR seguro, você deve ser redirecionado para a URL de resposta que você definiu na etapa 3.

## <a name="next-steps"></a>Próximas etapas  

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](custom-policy-overview.md)

- [Introdução às políticas personalizadas no AAD B2C](custom-policy-get-started.md?tabs=applications)
