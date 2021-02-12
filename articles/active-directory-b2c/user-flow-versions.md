---
title: Versões no fluxo de usuário no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre as versões de fluxos de usuário disponíveis no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 75ad3743a90f5773163a8f115e1924b8c5bbe097
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108086"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Versões no fluxo de usuário no Azure Active Directory B2C

O usuário flui em Azure Active Directory B2C (Azure AD B2C) ajuda a configurar políticas comuns que descrevem totalmente [as](user-flow-overview.md) experiências de identidade do cliente. Essas experiências incluem inscrição, entrada, redefinição de senha ou edição de perfil. As tabelas a seguir descrevem os fluxos de usuário que estão disponíveis no Azure AD B2C.

> [!IMPORTANT]
> Alteramos a maneira como fazemos referência às versões de fluxo do usuário. Anteriormente, oferecíamos versões V1 (prontas para produção) e versões V1.1 e V2 (versão prévia). Agora, consolidamos os fluxos de usuário em duas versões:
>
>- Os fluxos de usuário **recomendados** são as novas versões prévias dos fluxos de usuário. Eles são totalmente testados e combinam todos os recursos das versões herdadas **v2** e **v 1.1** . No futuro, os novos fluxos de usuário recomendados serão mantidos e atualizados. Depois de mover para esses novos fluxos de usuário recomendados, você terá acesso aos novos recursos à medida que eles forem lançados.
>- Os fluxos de usuário **padrão** , anteriormente conhecidos como **v1**, estão geralmente disponíveis e fluxos de usuário prontos para produção. Se seus fluxos de usuário forem de missão crítica e dependerem de versões altamente estáveis, você poderá continuar a usar fluxos de usuário padrão, percebendo que essas versões não serão mantidas e atualizadas.
>
>Todos os fluxos de usuário de visualização herdados (V 1.1 e v2) estão em um caminho para substituição em **1º de agosto de 2021**. Sempre que possível, é altamente recomendável que você [alterne para as novas versões **recomendadas** o](#how-to-switch-to-a-new-recommended-user-flow) mais rápido possível, para que você sempre possa aproveitar os recursos e as atualizações mais recentes. *Essas alterações se aplicam somente à nuvem pública do Azure. Outros ambientes continuarão a usar o [controle de versão de fluxo do usuário herdado](user-flow-versions-legacy.md).*

## <a name="recommended-user-flows"></a>Fluxos de usuário recomendados

Os fluxos de usuário recomendados são versões prévias que combinam novos recursos com recursos herdados V2 e V 1.1. No futuro, os fluxos de usuário recomendados serão mantidos e atualizados.

| Fluxo de usuário | Descrição |
| --------- | ----------- |
| Redefinição de senha (versão prévia) | Permite que um usuário escolha uma nova senha após verificar seu email. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>Configurações de compatibilidade de token</li><li>[Restrição de idade](basic-age-gating.md)</li><li>[requisitos de complexidade de senha](password-complexity.md)</li></ul> |
| Edição de perfil (versão prévia) | Permite que o usuário configure seus atributos de usuário. Usando este fluxo de usuário, você pode configurar: <ul><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li></ul> |
| Entrar (visualização) | Permite que um usuário entre em sua conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>[Restrição de idade](basic-age-gating.md)</li><li>Personalização da página de entrada</li></ul> |
| Inscrever-se (visualização) | Permite que um usuário crie uma conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>[Restrição de idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de senha](password-complexity.md)</li></ul> |
| Inscrever-se e entrar (visualização) | Permite que um usuário crie uma conta ou entre em sua conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Restrição de idade](basic-age-gating.md)</li><li>[Requisitos de complexidade de senha](password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>Fluxos de usuário padrão

Os fluxos de usuário padrão (anteriormente conhecidos como v1) estão geralmente disponíveis, fluxos de usuário prontos para produção. Os fluxos de usuário padrão não serão atualizados no futuro.

| Fluxo de usuário | Descrição |
| --------- | ----------- | ----------- |
| Redefinição de senha | Permite que um usuário escolha uma nova senha após verificar seu email. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>Configurações de compatibilidade de token</li><li>[Requisitos de complexidade de senha](password-complexity.md)</li></ul> |
| Edição de perfil | Permite que o usuário configure seus atributos de usuário. Usando este fluxo de usuário, você pode configurar: <ul><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li></ul> |
| Entrar | Permite que um usuário entre em sua conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>Entrar no bloco</li><li>Forçar redefinição de senha</li><li>Manter-me conectado (KMSI)</ul><br>Não é possível personalizar a interface do usuário com este fluxo de usuário. |
| Inscrição | Permite que um usuário crie uma conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de senha](password-complexity.md)</li></ul> |
| Inscrever-se e entrar | Permite que um usuário crie uma conta ou entre em sua conta. Usando este fluxo de usuário, você pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Tempo de vida do token](tokens-overview.md)</li><li>Configurações de compatibilidade de token</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de senha](password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>Como alternar para um novo fluxo de usuário recomendado

Para alternar de uma versão herdada de um fluxo de usuário para a nova versão de visualização **recomendada** , siga estas etapas:

1. Crie uma nova política de fluxo de usuário seguindo as etapas em [tutorial: criar fluxos de usuário em Azure Active Directory](tutorial-create-user-flows.md). Ao criar o fluxo de usuário, selecione a versão **recomendada** .

3. Configure seu novo fluxo de usuário com as mesmas configurações que foram configuradas na política herdada.

4. Atualize a URL de entrada do aplicativo para a política recém-criada.

5. Depois de testar o fluxo do usuário e confirmar que ele está funcionando, exclua o fluxo do usuário herdado seguindo estas etapas:
   1. No menu visão geral do Azure AD B2C locatário, selecione **fluxos de usuário**.
   2. Localize o fluxo de usuário que você deseja excluir.
   3. Na última coluna, selecione o menu de contexto (**...**) e, em seguida, selecione **excluir**.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>Ainda posso criar fluxos de usuário V2 e V 1.1 herdados?

Você não poderá criar novos fluxos de usuário com base nas versões herdadas V2 e V 1.1, mas poderá continuar a ler, atualizar e excluir qualquer fluxo de usuário V2 e V 1.1 herdado que você está usando no momento.

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>Há algum motivo para continuar usando fluxos de usuário V2 e V 1.1 herdados?

Não exatamente. As novas versões de visualização **recomendadas** contêm a mesma funcionalidade que as versões herdadas V2 e v 1.1. Nada foi removido e, de fato, eles agora incluem recursos adicionais.

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>Se eu não mudar de políticas herdadas V2 e V 1.1, como afetará meu aplicativo?

Se você estiver usando um fluxo de usuário v2 ou V 1.1 herdado, seu aplicativo não será afetado por essa alteração de controle de versão. Mas para obter acesso a novos recursos ou alterações de política no futuro, você precisará alternar para as novas versões **recomendadas** .

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>A Microsoft ainda dará suporte à minha política de fluxo de usuário v2 ou V 1.1 herdada?

As versões herdadas V2 e V 1.1 dos fluxos de usuário continuarão com suporte total.
