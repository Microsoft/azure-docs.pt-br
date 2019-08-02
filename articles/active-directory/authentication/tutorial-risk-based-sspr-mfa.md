---
title: MFA com base em risco e SSPR com a proteção de identidade do Azure Identity Protection
description: Neste tutorial, você habilitará integrações com o Azure Identity Protection para a Autenticação Multifator e redefinição de senha de autoatendimento a fim de reduzir o comportamento de risco.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17cea353df0337b062b89cd440f79f7869450f8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113215"
---
# <a name="tutorial-use-risk-events-to-trigger-multi-factor-authentication-and-password-changes"></a>Tutorial: Usar eventos de risco para disparar a Autenticação Multifator e alterações de senha

Neste tutorial, você habilitará recursos do Azure AD (Azure Active Directory) Identity Protection, um recurso do Azure AD Premium P2 que é mais do que apenas uma ferramenta de monitoramento e relatório. Para proteger as identidades da organização, você pode configurar políticas de risco que respondem automaticamente a comportamentos de risco. Essas políticas podem bloquear ou iniciar a correção automaticamente, incluindo a exigência de alterações de senha e a imposição de Autenticação Multifator.

As políticas do Azure AD Identity Protection podem ser usadas além das políticas de acesso condicional existentes, como uma camada extra de proteção. Os usuários podem nunca realizar um comportamento de risco que exija uma dessas políticas, mas como administrador, você sabe que eles estão protegidos.

Alguns itens que podem disparar um evento de risco incluem:

* Usuários com credenciais vazadas
* Entradas de endereços IP anônimos
* Viagem impossível a locais atípicos
* Entradas de dispositivos infectados
* Entradas de endereços IP com atividade suspeita
* Entradas de locais desconhecidos

Mais informações sobre o Azure AD Identity Protection podem ser encontradas no artigo [O que é o Azure AD Identity Protection](../active-directory-identityprotection.md)

> [!div class="checklist"]
> * Habilitar registro de MFA do Azure
> * Habilitar alterações de senha com base em risco
> * Habilitar Autenticação Multifator baseada em risco

## <a name="prerequisites"></a>Pré-requisitos

* Acesso a um locatário do Azure AD ativo com pelo menos uma licença de avaliação do Azure AD Premium P2 atribuída.
* Uma conta com privilégios de Administrador Global em seu locatário do Azure AD.
* Conclusão dos tutoriais de SSPR (redefinição de senha de autoatendimento) e MFA (Autenticação Multifator) previamente.

## <a name="enable-risk-based-policies-for-sspr-and-mfa"></a>Habilitar políticas de risco para SSPR e MFA

A habilitação das políticas baseadas em risco é um processo simples. As etapas a seguir percorrerão uma configuração de exemplo com você.

### <a name="enable-users-to-register-for-multi-factor-authentication"></a>Habilitar usuários para se registrarem na Autenticação Multifator

O Azure AD Identity Protection inclui uma política padrão que pode ajudá-lo a registrar seus usuários na Autenticação Multifator e a identificar facilmente o status do registro atual. A habilitação dessa política não começa a exigir que os usuários realizem a Autenticação Multifator, mas pedirá que eles façam um registro prévio.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Clique em **Todos os serviços** e navegue até **Azure AD Identity Protection**.
1. Clique em **Registro de MFA**.
1. Definir Impor Política como **Ativo**.
   1. A definição dessa política exigirá que todos os seus usuários registrem métodos como preparação para usar a Autenticação Multifator.
1. Clique em **Save** (Salvar).

   ![Exigir que os usuários se registrem para MFA ao entrar](./media/tutorial-risk-based-sspr-mfa/risk-based-require-mfa-registration.png)

### <a name="enable-risk-based-password-changes"></a>Habilitar alterações de senha com base em risco

A Microsoft trabalha com pesquisadores, autoridades, várias equipes de segurança da Microsoft e outras fontes confiáveis para localizar os pares de nome de usuário e senha. Quando um desses pares corresponde a uma conta em seu ambiente, uma alteração de senha com base em risco pode ser disparada usando a política a seguir.

1. Clique na política de risco do usuário.
1. Em **Condições**, selecione **Risco do usuário** e **Médio e acima**.
1. Clique em "Selecionar" e em "Concluído"
1. Em **Acesso**, escolha **Permitir acesso**e selecione **Requerer mudança de senha**.
1. Clique em “Selecionar”
1. Definir Impor Política como **Ativo**.
1. Clique em **Salvar**

### <a name="enable-risk-based-multi-factor-authentication"></a>Habilitar Autenticação Multifator baseada em risco

A maioria dos usuários tem um comportamento normal que pode ser acompanhado; quando eles saem do padrão, pode ser arriscado permitir que eles se conectem diretamente. Talvez seja melhor bloquear esse usuário ou pedir que ele faça a Autenticação Multifator para provar que realmente é quem diz ser. Para habilitar uma política exigindo MFA quando uma entrada arriscada for detectada, habilite a política a seguir.

1. Clicar em Política de risco de entrada
1. Em **Condições**, selecione **Risco do usuário** e **Médio e acima**.
1. Clique em "Selecionar" e em "Concluído"
1. Em **Acesso**, escolha **Permitir o acesso**e selecione **Requerer autenticação multifator**.
1. Clique em “Selecionar”
1. Definir Impor Política como **Ativo**.
1. Clique em **Salvar**

## <a name="clean-up-resources"></a>Limpar recursos

Se você tiver concluído o teste e não desejar mais ter as políticas baseadas em risco habilitadas, volte para cada política que deseja desabilitar e defina **Impor política** à **Desativado**.
