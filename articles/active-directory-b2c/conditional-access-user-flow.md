---
title: Adicionar o acesso condicional a um fluxo de usuário do Azure AD B2C
description: Saiba como adicionar o acesso condicional aos fluxos dos usuários do Azure AD B2C. Configure as definições de MFA (autenticação multifator) e as políticas de acesso condicional nos fluxos dos usuários para impor políticas e corrigir as entradas suspeitas.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d6d5ab13c8997dffee42a053ba498376ccbcb6d8
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585251"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Adicionar um acesso condicional a fluxos dos usuários do Azure Active Directory B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

O acesso condicional pode ser adicionado aos fluxos dos usuários do Azure Active Directory B2C para gerenciar entradas suspeitas nos seus aplicativos. A integração do Identity Protection e do acesso condicional ao Azure AD B2C permite configurar políticas que identificam um comportamento de entrada suspeita e impõem políticas que exigem ação adicional por parte do usuário ou do administrador. Estes são os componentes que habilitam o acesso condicional em fluxos dos usuários do Azure AD B2C:

- **Fluxo de usuário**. Crie um fluxo de usuário que orienta o usuário no processo de entrada e inscrição. Nas configurações de fluxo de usuário, indique se as políticas de acesso condicional devem ser ativadas quando um usuário segue o fluxo de usuário.
- **Aplicativo que direciona os usuários para o fluxo de usuário**. Configure seu aplicativo para direcionar os usuários para o fluxo de usuário de entrada e de inscrição apropriado, especificando o ponto de extremidade do fluxo do usuário no aplicativo.
- **Política de acesso condicional**. [Crie uma política de acesso condicional](conditional-access-identity-protection-setup.md) e especifique os aplicativos aos quais deseja aplicar a política. Quando o usuário segue o fluxo de usuário de entrada ou de inscrição do seu aplicativo, a política de acesso condicional usa sinais do Identity Protection para identificar as entradas suspeitas e apresenta a ação de correção apropriada, se necessário.

Há suporte para o acesso condicional nas últimas versões dos fluxos dos usuários. Você pode adicionar políticas de acesso condicional a novos fluxos dos usuários durante a criação ou a fluxos de usuário existentes, desde que a versão dê suporte ao acesso condicional. Ao adicionar o acesso condicional a um fluxo de usuário existente, há duas configurações que você precisará examinar:

- **MFA (autenticação multifator)** : agora, os usuários podem usar um código de uso único por meio de SMS ou voz ou uma senha de uso único por email para a autenticação multifator. As configurações da MFA são independentes das configurações do acesso condicional. Defina a MFA como **Sempre Ativada**, de modo que ela sempre seja obrigatória, independentemente da configuração do acesso condicional. Ou, então, defina o MFA como **Condicional**, de modo que a MFA seja obrigatória somente quando uma política de acesso condicional ativa a exigir.

- **Acesso condicional**: essa configuração sempre deve ser **Ativado**. Normalmente, você só alternará essa configuração para **Desativado** durante a solução de problemas ou a migração ou para implementações herdadas.

Saiba mais sobre o [Identity Protection e o acesso condicional](conditional-access-identity-protection-overview.md) do Azure AD B2C ou veja [como configurá-los](conditional-access-identity-protection-setup.md).

## <a name="prerequisites"></a>Pré-requisitos

- O Azure AD B2C Premium 2 é necessário para criar políticas de entrada suspeita. Os locatários do Premium P1 podem criar políticas baseadas em localização, aplicativo ou grupo.
- Para fins de teste, você pode [registrar o aplicativo Web de teste](tutorial-register-applications.md) `https://jwt.ms`, de propriedade da Microsoft, que exibe o conteúdo decodificado de um token (o conteúdo do token nunca deixa o navegador). 
- Para simular uma entrada suspeita, baixe o Navegador Tor e tente entrar no ponto de extremidade do fluxo de usuário.
- Usando as seguintes configurações, [crie uma política de acesso condicional](conditional-access-identity-protection-setup.md):
   
  - Em **Usuários e grupos**, escolha o usuário de teste (não selecione **Todos os usuários** ou você poderá bloquear sua inscrição).
  - Em **Aplicativos de nuvem ou ações**, escolha **Selecionar aplicativos** e o aplicativo de terceira parte confiável.
  - Em Condições, escolha **Risco de entrada** e os níveis de risco **Alto**, **Médio** e **Baixo**.
  - Em **Concessão**, escolha **Bloquear o acesso**.

      ![Detecções de risco](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

::: zone pivot="b2c-user-flow"

## <a name="add-conditional-access-to-a-new-user-flow"></a>Adicionar um acesso condicional a um novo fluxo de usuário

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **Fluxos dos usuários** e, em seguida, selecione **Novo fluxo de usuário**.
1. Na página **Criar um fluxo de usuário**, selecione o tipo de fluxo de usuário.
1. Em **Selecionar uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**. ([Saiba mais](user-flow-versions.md) sobre as versões de fluxo de usuário.)

    ![Página Criar fluxo de usuário no portal do Azure com as propriedades realçadas](./media/tutorial-create-user-flows/select-version.png)

1. Insira um **Nome** para o fluxo de usuário. Por exemplo, *signupsignin1*.
1. Na seção **provedores de identidade**, selecione os provedores de identidade que deseja permitir para esse fluxo de usuário.
2. Na seção **Autenticação multifator**, selecione o **Método de MFA** desejado e, em **Imposição de MFA**, escolha **Condicional (Recomendado)** .
 
   ![Configurar a autenticação multifator](media/conditional-access-user-flow/configure-mfa.png)

1. Na seção **Acesso Condicional**, marque a caixa de seleção **Impor políticas de acesso condicional**.

   ![Definir as configurações de acesso condicional](media/conditional-access-user-flow/configure-conditional-access.png)

1. Na seção **Atributos e declarações do usuário**, escolha as declarações e os atributos que deseja coletar e enviar do usuário durante a inscrição. Por exemplo, selecione **Mostrar mais** e escolha atributos e declarações para **País/Região** e **Nome de Exibição**. Selecione **OK**.

    ![Página Seleção de atributos e declarações com três declarações selecionadas](./media/conditional-access-user-flow/configure-user-attributes-claims.png)

1. Clique em **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é anexado automaticamente ao nome.

## <a name="add-conditional-access-to-an-existing-user-flow"></a>Adicionar o acesso condicional a um fluxo de usuário existente

> [!NOTE]
> O fluxo de usuário existente precisa ser uma versão que dê suporte ao acesso condicional. Essas versões de fluxo de usuário são rotuladas **Recomendado**.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.

1. No portal do Azure, pesquise e selecione **Azure AD B2C**.

1. Em **Políticas**, selecione **Fluxos dos usuários**. Em seguida, selecione o fluxo de usuário.

1. Escolha **Propriedades** e verifique se o fluxo de usuário dá suporte ao acesso condicional selecionando **Propriedades** e procurando a configuração rotulada **Acesso Condicional**.
 
   ![Configurar a MFA e o acesso condicional em Propriedades](media/conditional-access-user-flow/add-conditional-access.png)

1. Na seção **Autenticação multifator**, selecione o **Método de MFA** desejado e, em **Imposição de MFA**, escolha **Condicional (Recomendado)** .
 
1. Na seção **Acesso Condicional**, marque a caixa de seleção **Impor políticas de acesso condicional**.

1. Clique em **Salvar**.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

Para testar o acesso condicional no fluxo de usuário, [crie uma política de acesso condicional](conditional-access-identity-protection-setup.md) e habilite o acesso condicional no fluxo de usuário, conforme descrito acima. 


### <a name="run-the-user-flow"></a>Executar o fluxo do usuário

1. Selecione o fluxo de usuário que você criou para abrir a página de visão geral e, em seguida, selecione **Executar fluxo de usuário**. Em **Aplicativo**, selecione *webapp1*. A **URL de resposta** deve mostrar `https://jwt.ms`.

   ![Página Executar fluxo de usuários no portal com o botão Executar fluxo de usuário realçado](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Copie a URL em **Executar ponto de extremidade de fluxo de usuário**.

1. Para simular uma entrada suspeita, abra o [Navegador Tor](https://www.torproject.org/download/) e use a URL copiada na etapa de visualização para entrar no aplicativo registrado.

1. Insira as informações solicitadas na página de entrada e tente se conectar. O token é retornado para `https://jwt.ms` e deve ser exibido para você. No token decodificado jwt.ms, você verá que a entrada foi bloqueada:

   ![Testar uma entrada bloqueada](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Adicionar o acesso condicional à sua política

Encontre um exemplo de uma política de acesso condicional no [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access).

::: zone-end

## <a name="next-steps"></a>Próximas etapas

[Personalizar a interface do usuário em um fluxo de usuário do Azure AD B2C](customize-ui-with-html.md)
