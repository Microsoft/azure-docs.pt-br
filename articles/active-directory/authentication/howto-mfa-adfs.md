---
title: Proteger recursos com o Azure AD MFA e o ADFS-Azure Active Directory
description: Esta é a página de autenticação multifator do Azure AD que descreve como começar a usar o Azure AD MFA e AD FS na nuvem.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b42f920726d4b3046ab0c292e1090f5217e8b1f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743234"
---
# <a name="securing-cloud-resources-with-azure-ad-multi-factor-authentication-and-ad-fs"></a>Protegendo recursos de nuvem com a autenticação multifator do Azure AD e o AD FS

Se sua organização for federada com Azure Active Directory, use a autenticação multifator do Azure AD ou Serviços de Federação do Active Directory (AD FS) (AD FS) para proteger os recursos que são acessados pelo Azure AD. Use os procedimentos a seguir para proteger Azure Active Directory recursos com a autenticação multifator do Azure AD ou Serviços de Federação do Active Directory (AD FS).

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Proteger recursos do Azure AD usando o AD FS

Para proteger seus recursos de nuvem, configure uma regra de declaração para que os Serviços de Federação do Active Directory emitem a declaração multipleauthn quando um usuário executa a verificação em duas etapas com êxito. Essa declaração é passada para o Azure AD. Siga este procedimento para percorrer as etapas:

1. Abra o gerenciamento do AD FS.
2. À esquerda, selecione **Relações de Confiança com Terceira Parte Confiável**.
3. Clique com o botão direito do mouse em **Microsoft Office plataforma de identidade 365** e selecione **Editar regras de declaração**.

   ![Console do ADFS-confianças de terceira parte confiável](./media/howto-mfa-adfs/trustedip1.png)

4. Em regras de transformação de emissão, clique em **Adicionar regra**.

   ![Editando regras de transformação de emissão](./media/howto-mfa-adfs/trustedip2.png)

5. No Assistente Adicionar Regra de Declaração de Transformação, selecione **Passar ou filtrar uma Declaração de Entrada** na lista e clique em **Avançar**.

   ![A captura de tela mostra o assistente Adicionar regra de declaração de transformação onde você seleciona um modelo de regra de declaração.](./media/howto-mfa-adfs/trustedip3.png)

6. Dê um nome para a regra. 
7. Selecione **Referências de Métodos de Autenticação** como o tipo de declaração Entrada.
8. Selecione **Passar todos os valores de declaração**.
    ![A captura de tela mostra o assistente Adicionar regra de declaração de transformação, em que você seleciona passar todos os valores de declaração.](./media/howto-mfa-adfs/configurewizard.png)
9. Clique em **Concluir**. Feche o Console de gerenciamento do AD FS.

## <a name="trusted-ips-for-federated-users"></a>IPs confiáveis para usuários federados

IPs confiáveis permitem aos administradores ignorar a verificação em duas etapas para endereços IP específicos ou para usuários federados que têm as solicitações originadas em seu próprios intranet. As seções a seguir descrevem como configurar IPs confiáveis da autenticação multifator do Azure AD com usuários federados e passar a verificação em duas etapas quando uma solicitação é originada de dentro de uma intranet de usuários federados. Isso é conseguido por meio da configuração do AD FS para usar uma passagem ou filtrar um modelo de declaração de entrada com o tipo de declaração Dentro da rede corporativa.

Este exemplo usa Microsoft 365 para nossas relações de confiança de terceira parte confiável.

### <a name="configure-the-ad-fs-claims-rules"></a>Configurar as regras de declarações do AD FS

A primeira coisa que precisamos fazer é configurar as declarações do AD FS. Criamos duas regras declarações: uma para o tipo de declaração Dentro da rede corporativa e um adicional para manter nossos usuários conectados.

1. Abra o gerenciamento do AD FS.
2. À esquerda, selecione **Relações de Confiança com Terceira Parte Confiável**.
3. Clique com o botão direito do mouse em **Microsoft Office plataforma de identidade 365** e selecione **Editar regras de declaração...** 
    ![ Console do ADFS – Editar regras de declaração](./media/howto-mfa-adfs/trustedip1.png)
4. Em regras de transformação de emissão, clique em **Adicionar regra.** 
    ![ Adicionando uma regra de declaração](./media/howto-mfa-adfs/trustedip2.png)
5. No Assistente Adicionar Regra de Declaração de Transformação, selecione **Passar ou filtrar uma Declaração de Entrada** na lista e clique em **Avançar**.
   ![A captura de tela mostra o assistente para Adicionar regra de declaração de transformação, onde você seleciona passar ou filtrar uma declaração de entrada.](./media/howto-mfa-adfs/trustedip3.png)
6. Na caixa ao lado do nome da regra de declaração, nomeie a regra. Por exemplo: InsideCorpNet.
7. Na lista suspensa, ao lado do tipo de declaração de entrada, selecione **Dentro da rede corporativa**.
   ![Adicionando dentro da declaração de rede corporativa](./media/howto-mfa-adfs/trustedip4.png)
8. Clique em **Concluir**.
9. Em regras de transformação de emissão, clique em **Adicionar regra**.
10. No Assistente Adicionar Regra de Declaração de Transformação, selecione **Enviar Declarações Usando uma Regra Personalizada** da lista suspensa e clique em **Avançar**.
11. Na caixa sob nome da regra de declaração: insira *manter os usuários conectados*.
12. Na caixa de regra Personalizada, digite:

```ad-fs-claim-rule
        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Create custom claim to keep users signed in](./media/howto-mfa-adfs/trustedip5.png)
```

13. Clique em **Concluir**.
14. Clique em **Aplicar**.
15. Clique em **OK**.
16. Feche o gerenciamento do AD FS.

### <a name="configure-azure-ad-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configurar IPs confiáveis da autenticação multifator do Azure AD com usuários federados

Agora que as declarações estão prontas, podemos configurar IPs confiáveis.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory**  >    >  **acesso condicional** de segurança  >  **locais nomeados**.
3. Na folha **acesso condicional-locais nomeados** , selecione **Configurar IPs confiáveis MFA**

   ![Locais nomeados de acesso condicional do Azure AD configurar IPs confiáveis de MFA](./media/howto-mfa-adfs/trustedip6.png)

4. Na página Configurações de Serviço, em **IPs confiáveis**, selecione **Ignorar autenticação multifator para solicitações de usuários federados na minha intranet**.  
5. Clique em **salvar**.

É isso! Neste ponto, os usuários federados Microsoft 365 devem apenas usar a MFA quando uma declaração se originar de fora da intranet corporativa.
