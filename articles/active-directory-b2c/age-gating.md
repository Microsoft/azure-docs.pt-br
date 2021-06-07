---
title: Habilitar a restrição etária no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre como identificar menores de idade usando seu aplicativo.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71a3b38da6a63824a42f64052bf16a5fe0e25483
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102522403"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Habilitar a restrição etária no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A retenção de idade no Azure Active Directory B2C (Azure AD B2C) permite que você identifique os pequenos que desejam usar seu aplicativo, com ou sem o consentimento dos pais. Você pode optar por bloquear o secundário de entrar no aplicativo. Ou permitir usos para concluir a entrada e fornecer ao aplicativo o status secundário. 

>[!IMPORTANT]
>Esse recurso está em uma versão prévia. Não use o recurso para aplicativos de produção.
>

Quando a retenção de idade é habilitada para um fluxo de usuário, os usuários são solicitados pela data de nascimento e pelo país de residência. Se um usuário que não inseriu as informações anterior entrar, será necessário inserir a próxima vez que entrar. As regras são aplicadas sempre que um usuário entrar.

![Captura de tela de informações de retenção de idade coletar fluxo](./media/age-gating/age-gating-information-gathering.png)

O Azure AD B2C usa as informações que o usuário insere para identificar se são um menor. O campo **Grupoetário**, em seguida, é atualizado na sua conta. O valor pode ser `null`, `Undefined`, `Minor`, `Adult` ou `NotAdult`.  Os campos **Grupoetário** e **consentProvidedForMinor**, em seguida, são usados para calcular o valor de **legalAgeGroupClassification**.


## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>Configurar seu locatário para restrição etária

Para usar a retenção de idade em um fluxo de usuário, você precisa configurar seu locatário para ter propriedades extras.

1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior. Selecione o diretório que contém seu locatário.
1. Selecione **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **Propriedades** para seu locatário no menu à esquerda.
1. Em **retenção de idade**, selecione **Configurar**.
1. Aguarde a conclusão da operação e seu diretório estará configurado para a restrição etária.

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>Habilitando a restrição etária em seu fluxo de usuário

Depois que seu locatário estiver configurado para usar a restrição etária, você pode usar esse recurso nos [fluxos de usuário](user-flow-versions.md) onde ele está habilitado. Habilite a restrição de idade seguindo estas etapas:

1. Crie um fluxo de usuário que tenha a restrição etária habilitada.
1. Depois de criar o fluxo de usuário, selecione **Propriedades** no menu.
1. Na seção **Restrição etária**, selecione **Habilitado**.
1. Para **inscrever-se ou entrar**, selecione como você deseja gerenciar usuários:
    - Permitir que os menores acessem seu aplicativo.
    - Bloquear somente os menores abaixo da idade do consentimento de acessar seu aplicativo.
    - Impedir que todos os pequenos acessem seu aplicativo.
1. Para **no bloco**, selecione uma das seguintes opções:
    - **Enviar um JSON de volta para o aplicativo** -essa opção envia uma resposta de volta ao aplicativo que um secundário foi bloqueado.
    - **Mostrar uma página de erro** -o usuário mostra uma página informando que eles não podem acessar o aplicativo.

## <a name="test-your-user-flow"></a>Testar seu fluxo de usuário

1. Para testar sua política, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar fluxo de usuário** .
1. Entre com uma conta local ou social. Em seguida, selecione seu país de residência e a data de nascimento que simula um secundário. 
1. Repita o teste e selecione uma data de nascimento que simule um adulto.  

Ao entrar como um secundário, você verá a seguinte mensagem de erro: *infelizmente, seu logon foi bloqueado. A privacidade e as leis de segurança online em seu país impedem o acesso a contas pertencentes a crianças.*

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>Habilitar a retenção de idade em sua política personalizada

1. Obtenha o exemplo de uma política de retenção de idade no [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/age-gating).
1. Em cada arquivo, substitua a cadeia de caracteres `yourtenant` pelo nome do seu locatário do Azure AD B2C. Por exemplo, se o nome do seu locatário B2C for *contosob2c*, todas as instâncias de `yourtenant.onmicrosoft.com` se tornarão `contosob2c.onmicrosoft.com`.
1. Carregue os arquivos de política.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

- Saiba como [gerenciar o acesso do usuário no Azure ad B2C](manage-user-access.md).

