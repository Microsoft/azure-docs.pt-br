---
title: 'Personalizando: SSPR do Azure AD | Microsoft Docs'
description: "Opções de personalização para redefinição de senha por autoatendimento do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 1b6e368df4914e58eb3f8d6481132f25d27312b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="customize-azure-ad-functionality-for-self-service-password-reset"></a>Personalizar a funcionalidade de Autoatendimento de Redefinição de Senha do Azure AD

Profissionais de TI que pretendem implantar a redefinição de senha por autoatendimento podem personalizar a experiência para se ajustar a seus usuários.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizar o link Contate o administrador

Mesmo que o SSPR não esteja habilitado, os usuários ainda verão um link “contate o administrador” no portal de redefinição de senha.  Ao clicar nesse link, um email será enviado aos administradores solicitando assistência na alteração da senha do usuário. Esse email é enviado para os seguintes destinatários na seguinte ordem:

1. Se a função **Administrador de senhas** for atribuída, os administradores com essa função serão notificados
2. Se nenhum Administrador de senhas for atribuído, os administradores com a função **Administrador de usuários** serão notificados
3. Se nenhuma das funções anteriores foram atribuídas, os **Administradores globais** são notificados

Em todos os casos, no máximo 100 destinatários serão notificados.

Para obter mais informações sobre as diferente funções de administrador e sobre como atribuí-las, consulte o documento [Atribuindo funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md)

### <a name="disable-contact-your-administrator-emails"></a>Desabilitar os emails “Contate o administrador”

Caso sua organização não queira que os administradores sejam notificados de solicitações de redefinição de senha, a configuração a seguir poderá ser habilitada

* Habilite o autoatendimento de redefinição de senha para todos os usuários finais. Essa opção pode ser encontrada em **Redefinição de Senha > Propriedades**.
    * Se não quiser que os usuários redefinam suas próprias senhas, você poderá limitar o escopo do acesso a um grupo vazio **no entanto, não recomendamos essa opção**.
* Personalizar o link de assistência técnica para fornecer uma URL da Web ou um endereço mailto: que os usuários podem usar para obter assistência. Essa opção pode ser encontrada em **Redefinição de Senha > Personalização > URL ou email de assistência técnica personalizados**.

## <a name="customize-adfs-sign-in-page-for-sspr"></a>Personalizar a página de entrada do AD FS para SSPR

Os Administradores do AD FS podem adicionar um link para sua página de logon usando as diretrizes encontradas no artigo [Adicionar descrição de página de entrada](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Usar o comando a seguir em seu servidor do ADFS adiciona um link para a página de logon do ADFS, permitindo que os usuários entrem diretamente no fluxo de trabalho de redefinição de senha por autoatendimento.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-and-access-panel-look-and-feel"></a>Personalizar o logon e a aparência do painel de acesso

Quando os usuários acessam a página de logon, você pode personalizar o logotipo que é exibido junto com a imagem da página de conexão de acordo com a identidade visual de sua empresa.

Esses elementos gráficos são mostrados nas seguintes circunstâncias:

* Depois que um usuário digita seu nome de usuário
* Quando o usuário acessa uma URL personalizada
    * Ao passar o parâmetro “whr” para a página de redefinição de senha, como “https://login.microsoftonline.com/?whr=contoso.com”
    * Ao passar o parâmetro “username” para a página de redefinição de senha, como “https://login.microsoftonline.com/?username=admin@contoso.com”

### <a name="graphics-details"></a>Detalhes de elementos gráficos

As configurações a seguir permitem alterar as características visuais da página de conexão e podem ser encontradas em **Azure Active Directory**, **Identidade visual da empresa**, **Editar identidade visual da empresa**

* A imagem da página de conexão deve ser um arquivo PNG ou JPG com 1420x1200 pixels e, no máximo, 500 KB. Recomendamos que ela tenha cerca de 200 KB para obter melhores resultados.
* A cor da tela de fundo da página de entrada é usada em conexões de alta latência e deve estar no formato hexadecimal RGB.
* A imagem da faixa deve ser um arquivo PNG ou JPG com 60x280 pixels e, no máximo, 10 KB.
* Logotipo quadrado (tema normal e escuro) em PNG ou JPG com 240x240 (redimensionável) e, no máximo, 10 KB.

### <a name="sign-in-text-options"></a>Opções do texto de conexão

As configurações a seguir permitem adicionar um texto à página de conexão relevante para sua organização. Essas configurações podem ser encontradas em **Azure Active Directory**, **Identidade visual da empresa**, **Editar identidade visual da empresa**

* A **dica de nome de usuário** substitui o texto de exemplo someone@example.com por algo mais apropriado para os usuários (é recomendado que ela seja mantida como padrão durante o suporte a usuários internos e externos)
* O **texto da página de conexão** tem, no máximo, 256 caracteres. Esse texto é exibido em qualquer lugar quando os usuários fazem logon online e na experiência de Ingresso do Azure AD no Windows 10. Use este texto para termos de uso, instruções e dicas para os usuários. **Qualquer pessoa pode ver sua página de conexão; portanto, não forneça informações confidenciais aqui.**

### <a name="keep-me-signed-in-disabled"></a>Opção “Manter-me conectado desabilitado”

A opção “Manter-me conectado desabilitado” permite que os usuários permaneçam conectados ao fechar e reabrir a janela do navegador. Essa opção não afeta o tempo de vida da sessão. Essa configuração é encontrada em **Azure Active Directory > Identidade visual da empresa > Editar identidade visual da empresa**.

Alguns recursos do SharePoint Online e do Office 2010 dependem da capacidade de os usuários marcarem essa caixa de seleção. Se você ocultar essa opção, os usuários poderão receber prompts de conexão adicionais e inesperados.

### <a name="directory-name"></a>Nome do diretório

É possível alterar o atributo de nome em **Azure Active Directory > Propriedades** para mostrar um nome de organização amigável visto no portal e em comunicações automatizadas. Essa opção fica mais visível na forma de emails automatizados nos formatos a seguir

* Nome amigável em email “Microsoft em nome da demonstração da CONTOSO”
* Linha do assunto em email “Código de verificação de email da conta de demonstração da CONTOSO”

## <a name="next-steps"></a>Próximas etapas

Os links a seguir fornecem informações adicionais sobre a redefinição de senha usando o Azure AD

* [**Início Rápido**](active-directory-passwords-getting-started.md): comece agora mesmo a usar o gerenciamento de autoatendimento de senhas do Azure AD 
* [**Licenciamento**](active-directory-passwords-licensing.md): configure o licenciamento do Azure AD
* [**Dados**](active-directory-passwords-data.md): entenda os dados que são necessários e como eles são usados para o gerenciamento de senhas
* [**Distribuição**](active-directory-passwords-best-practices.md) – planeje e implante o SSPR em seus usuários usando as diretrizes descritas aqui
* [**Política**](active-directory-passwords-policy.md) – entenda e defina políticas de senha do Azure AD
* [**Write-back de senha** ](active-directory-passwords-writeback.md) - Como o write-back de senha opera com o seu diretório local
* [**Relatório** ](active-directory-passwords-reporting.md) - Descubra se, quando e onde os usuários estão acessando a funcionalidade da SSPR
* [**Detalhamento Técnico**](active-directory-passwords-how-it-works.md): veja os bastidores para entender como o recurso funciona
* [**Perguntas frequentes**](active-directory-passwords-faq.md): como? Por quê? O quê? Onde? Quem? Quando? – respostas para perguntas que você sempre quis fazer
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) - Saiba como resolver problemas comuns que vemos com a SSPR

