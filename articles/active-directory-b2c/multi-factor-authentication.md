---
title: Autenticação Multifator no Azure Active Directory B2C | Microsoft Docs
description: Como habilitar a Autenticação Multifator em aplicativos voltados ao consumidor protegidos pelo Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b7b7f1c5fb0a7991707a26b4a7f54fb3ffaf7bab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033513"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Ativar a autenticação de vários fatores no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

O Azure Active Directory B2C (Azure AD B2C) integra-se diretamente com a [autenticação multifator do Azure ad](../active-directory/authentication/concept-mfa-howitworks.md) para que você possa adicionar uma segunda camada de segurança às experiências de inscrição e de entrada em seus aplicativos. Você habilita a autenticação de múltiplos fatores sem escrever uma única linha de código. Se você já criou fluxos de usuário de inscrição e de login, ainda poderá ativar a autenticação multi fator.

Esse recurso ajuda os aplicativos a lidar com cenários como:

- Você não precisa de autenticação de vários fatores para acessar um aplicativo, mas exige que ele acesse outro. Por exemplo, o cliente pode entrar em um aplicativo de seguro de automóvel com uma conta social ou local, mas deve verificar o número de telefone antes de acessar o aplicativo de seguro residencial registrado no mesmo diretório.
- Você não precisa de autenticação multi fator para acessar um aplicativo em geral, mas exige que ele acesse as partes confidenciais dentro dele. Por exemplo, o cliente pode entrar em um aplicativo bancário com uma conta social ou local e verificar o saldo da conta, mas deve verificar o número de telefone antes de tentar fazer uma transferência eletrônica.

## <a name="set-multi-factor-authentication"></a>Definir a autenticação multi fator

::: zone pivot="b2c-user-flow"

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Use o filtro **diretório + assinatura** no menu superior para selecionar o diretório que contém seu locatário Azure ad B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Fluxos de usuário**.
1. Selecione o fluxo de usuário para o qual você deseja habilitar a MFA. Por exemplo, *B2C_1_signinsignup*.
1. Selecione **Propriedades**.
1. Na seção **autenticação multifator** , selecione o **método MFA** desejado e, em seguida, em **imposição de MFA** , selecione **Always on** ou **condicional (recomendado)**.
   > [!NOTE]
   >
   > - Se você selecionar **condicional (recomendado)**, também precisará [Adicionar acesso condicional aos fluxos de usuário](conditional-access-user-flow.md)e especificar os aplicativos aos quais deseja que a política se aplique.
   > - A autenticação multifator (MFA) é desabilitada por padrão para fluxos de usuário de inscrição. Você pode habilitar a MFA em fluxos de usuário com inscrição no telefone, mas como um número de telefone é usado como o identificador primário, o email de senha de uso único é a única opção disponível para o segundo fator de autenticação.

1. Clique em **Salvar**. A MFA agora está habilitada para este fluxo de usuário.

Você pode usar **Executar fluxo de usuário** na política para verificar a experiência. Confirme o seguinte cenário:

Uma conta de cliente é criada em seu locatário antes que a etapa de autenticação de vários fatores ocorra. Durante a etapa, o cliente é solicitado a fornecer um número de telefone e a verificar. Se a verificação for bem-sucedida, o número de telefone será anexado à conta para uso posterior. Mesmo se o cliente cancelar ou desistir, o cliente poderá ser solicitado a confirmar um número de telefone novamente durante o próximo login, com a autenticação multi fatores ativada.

::: zone-end

::: zone pivot="b2c-custom-policy"

Para habilitar a autenticação multifator, obtenha os pacotes de início da política personalizada do GitHub e, em seguida, atualize os arquivos XML no pacote de início do **SocialAndLocalAccountsWithMFA** com o nome do locatário do Azure ad B2C. O **SocialAndLocalAccountsWithMFA**  permite opções sociais, locais e de autenticação multifator. Para obter mais informações, consulte Introdução [às políticas personalizadas no Active Directory B2C](custom-policy-get-started.md). 

::: zone-end
