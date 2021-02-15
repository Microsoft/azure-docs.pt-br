---
title: Personalizar autoatendimento de redefinição de senha-Azure Active Directory
description: Saiba como personalizar as opções de exibição e experiência do usuário para redefinição de senha de autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3766db0d5070f15216ac612353e2c25737ad092a
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741619"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Personalizar a experiência do usuário para Azure Active Directory redefinição de senha de autoatendimento

A redefinição de senha de autoatendimento (SSPR) fornece aos usuários no Azure Active Directory (Azure AD) a capacidade de alterar ou redefinir sua senha, sem envolvimento de administrador ou suporte técnico. Se a conta de um usuário estiver bloqueada ou se ele esquecer a senha, ele poderá seguir os avisos para desbloquear a si mesmo e voltar ao trabalho. Essa capacidade reduz as chamadas de suporte técnico e a perda de produtividade quando um usuário não consegue entrar no dispositivo ou em um aplicativo.

Para melhorar a experiência do SSPR para os usuários, você pode personalizar a aparência da página de redefinição de senha, notificações por email ou páginas de entrada. Essas opções de personalização lhe permitem deixar claro para o usuário que estão no lugar certo e dar a eles a confiança de que estão acessando os recursos da empresa.
    
Este artigo mostra como personalizar o link de email SSPR para usuários, a identidade visual da empresa e o link da página de entrada AD FS.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizar o link "Contate o administrador"

Para ajudar os usuários a alcançarem ajuda com a redefinição de senha de autoatendimento, um link "Contate o administrador" é mostrado no portal de redefinição de senha. Se um usuário selecionar esse link, ele fará uma das duas coisas:

* Se esse link de contato for deixado no estado padrão, um email será enviado aos seus administradores e solicitará que eles forneçam assistência para alterar a senha do usuário. O email de exemplo a seguir mostra essa mensagem de email padrão:

    ![Exemplo de solicitação para redefinir email enviado ao administrador](./media/howto-sspr-customization/sspr-contact-admin.png)

* Se personalizado, envia o usuário para uma página da Web ou endereço de email especificado pelo administrador para obter assistência.
    * Se você personalizar isso, é recomendável defini-lo como algo que os usuários já conhecem para obter suporte.

    > [!WARNING]
    > Se você personalizar essa configuração com um endereço de email e uma conta que precise de uma redefinição de senha, talvez o usuário não possa solicitar assistência.

### <a name="default-email-behavior"></a>Comportamento de email padrão

O email de contato padrão é enviado aos destinatários na seguinte ordem:

1. Se a função *administrador de assistência técnica* ou a função de *administrador de senha* for atribuída, os administradores com essas funções serão notificados.
1. Se nenhum administrador de assistência técnica ou administrador de senha for atribuído, os administradores com a função de *administrador de usuários* serão notificados.
1. Se nenhuma das funções anteriores for atribuída, os *administradores globais* serão notificados.

Em todos os casos, no máximo 100 destinatários serão notificados.

Para obter mais informações sobre as diferentes funções de administrador e sobre como atribuí-las, consulte [Atribuindo funções de administrador no Azure Active Directory](../roles/permissions-reference.md).

### <a name="disable-contact-your-administrator-emails"></a>Desabilitar os emails "Contate o administrador"

Se sua organização não deseja notificar os administradores sobre solicitações de redefinição de senha, as seguintes opções de configuração podem ser usadas:

* Personalizar o link de assistência técnica para fornecer uma URL da Web ou um endereço mailto: que os usuários podem usar para obter assistência. Esta opção está em **Password Reset**  >  **Customization**  >  **email ou URL do helpdesk personalizado de** redefinição de senha.
* Habilite a redefinição de senha de autoatendimento para todos os usuários. Essa opção está em Propriedades de **redefinição de senha**  >  **Properties**. Se você não quiser que os usuários redefinam as próprias senhas, poderá definir o escopo de acesso como um grupo vazio. *Não recomendamos essa opção.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Personalizar a página de entrada e o painel de acesso

Você pode personalizar a página de entrada, como para adicionar um logotipo que aparece junto com a imagem que se ajusta à identidade visual da sua empresa. Para obter mais informações sobre como configurar a identidade visual da empresa, consulte [Adicionar identidade visual da empresa à sua página de entrada no Azure ad](../fundamentals/customize-branding.md).

Os gráficos que escolher são mostrados nas seguintes circunstâncias:

* Depois que um usuário inserir seu nome de usuário
* Se o usuário acessar a URL personalizada:
   * Passando o `whr` parâmetro para a página de redefinição de senha, como `https://login.microsoftonline.com/?whr=contoso.com`
   * Passando o `username` parâmetro para a página de redefinição de senha, como `https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Nome do diretório

Para tornar as coisas mais amigáveis para o usuário, você pode alterar o nome da organização no portal e nas comunicações automatizadas. Para alterar o atributo de nome de diretório na portal do Azure, navegue até **Azure Active Directory**  >  **Propriedades**. Essa opção de nome amigável de organização é a mais visível em emails automatizados, como nos exemplos a seguir:

* O nome amigável no email, por exemplo "*Microsoft em nome da demonstração da Contoso*"
* A linha de assunto no email, por exemplo "*código de verificação de email da conta de demonstração da Contoso*"

## <a name="customize-the-ad-fs-sign-in-page"></a>Personalizar a página de entrada AD FS

Se você usar Serviços de Federação do Active Directory (AD FS) (AD FS) para eventos de entrada do usuário, poderá adicionar um link à página de entrada usando as diretrizes no artigo para [Adicionar a descrição da página de entrada](/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Forneça aos usuários um link para a página para que eles insiram o fluxo de trabalho do SSPR, como *https://passwordreset.microsoftonline.com* . Para adicionar um link à página de entrada AD FS, use o seguinte comando no servidor de AD FS:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Próximas etapas

Para entender o uso de SSPR em seu ambiente, consulte [Opções de relatório para o gerenciamento de senhas do Azure ad](howto-sspr-reporting.md).

Se você ou os usuários tiverem problemas com o SSPR, consulte [solucionar problemas de redefinição de senha de autoatendimento](./troubleshoot-sspr.md)