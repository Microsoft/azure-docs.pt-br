---
title: Gerenciar certificados de federação no Azure AD | Microsoft Docs
description: Saiba como personalizar a data de vencimento para seus certificados de federação e como renovar certificados que vencerão em breve.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9b6e81fd090a625ec7ac86963cc9d5e66f5010e
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259212"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gerenciar certificados para logon único federado no Azure Active Directory

Neste artigo, abordamos perguntas e informações comuns relacionadas aos certificados que Azure Active Directory (Azure AD) cria para estabelecer o SSO (logon único federado) para seus aplicativos SaaS (software como serviço). Adicione aplicativos pela galeria de aplicativos do Azure AD ou usando um modelo de aplicativo inexistente na galeria. Configure o aplicativo usando a opção de SSO federada.

Este artigo é relevante apenas para aplicativos que são configurados para usar o SSO do Azure AD por meio da Federação do [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificado gerado automaticamente para aplicativos da galeria e inexistentes na galeria

Quando você adiciona um novo aplicativo da galeria e configura um logon baseado em SAML (selecionando SAML de **logon único**  >   na página Visão geral do aplicativo), o Azure ad gera um certificado para o aplicativo que é válido por três anos. Para baixar o certificado ativo como um arquivo de certificado de segurança (**. cer**), retorne a essa página (**logon baseado em SAML**) e selecione um link de download no cabeçalho do **certificado de autenticação SAML** . Você pode escolher entre o certificado bruto (binário) ou o certificado Base64 (texto codificado em base 64). Para aplicativos da galeria, esta seção também pode mostrar um link para baixar o certificado como XML de metadados de Federação (um arquivo **. xml** ), dependendo do requisito do aplicativo.

![Opções de download de certificado de autenticação ativo SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Você também pode baixar um certificado ativo ou inativo selecionando o ícone de **edição** do cabeçalho do **certificado de autenticação SAML** (um lápis), que exibe a página **certificado de autenticação SAML** . Selecione as reticências (**...**) ao lado do certificado que você deseja baixar e escolha qual formato de certificado você deseja. Você tem a opção adicional para baixar o certificado no formato de privacidade/email aprimorado (PEM). Esse formato é idêntico a base64, mas com uma extensão de nome de arquivo **. pem** , que não é reconhecida no Windows como um formato de certificado.

![Opções de download de certificado de autenticação SAML (ativas e inativas)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalizar a data de vencimento do seu certificado de federação e transferir para um novo certificado

Por padrão, o Azure configura um certificado para expirar após três anos quando ele é criado automaticamente durante a configuração de logon único do SAML. Como não é possível alterar a data de um certificado depois de salvá-lo, você precisa:

1. Crie um novo certificado com a data desejada.
1. Salve o novo certificado.
1. Baixe o novo certificado no formato correto.
1. Carregue o novo certificado para o aplicativo.
1. Torne o novo certificado ativo no portal de Azure Active Directory.

As duas seções a seguir ajudam a executar essas etapas.

### <a name="create-a-new-certificate"></a>Criar um novo certificado

Primeiro, crie e salve o novo certificado com uma data de expiração diferente:

1. Entre no [Portal do Azure Active Directory](https://aad.portal.azure.com/). A página **Azure Active Directory centro de administração** é exibida.
1. No painel esquerdo, selecione **Aplicativos Empresariais**. É exibida uma lista dos aplicativos empresariais em sua conta.
1. Selecione o aplicativo afetado. Uma página de visão geral do aplicativo é exibida.
1. No painel esquerdo da página Visão geral do aplicativo, selecione **logon único**.
1. Se a página **selecionar um método de logon único** for exibida, selecione **SAML**.
1. Na página **configurar Sign-On único com SAML-Preview** , localize o cabeçalho do **certificado de autenticação SAML** e selecione o ícone de **edição** (um lápis). A página **certificado de autenticação SAML** é exibida, que exibe o status (**ativo** ou **inativo**), a data de expiração e a impressão digital (uma cadeia de caracteres de hash) de cada certificado.
1. Selecione **novo certificado**. Uma nova linha é exibida abaixo da lista de certificados, em que a data de expiração padrão é exatamente três anos após a data atual. (Suas alterações ainda não foram salvas, portanto, você ainda pode modificar a data de validade.)
1. Na nova linha de certificado, passe o mouse sobre a coluna data de expiração e selecione o ícone **selecionar data** (um calendário). Um controle de calendário é exibido, exibindo os dias de um mês da data de validade atual da nova linha.
1. Use o controle Calendar para definir uma nova data. Você pode definir qualquer data entre a data atual e três anos após a data atual.
1. Clique em **Salvar**. O novo certificado agora aparece com um status **inativo**, a data de validade que você escolheu e uma impressão digital.
1. Selecione o **X** para retornar à página **Configurar Sign-On única com SAML-Preview** .

### <a name="upload-and-activate-a-certificate"></a>Carregar e ativar um certificado

Em seguida, baixe o novo certificado no formato correto, carregue-o no aplicativo e torne-o ativo no Azure Active Directory:

1. Exiba as instruções de configuração de logon do SAML adicionais do aplicativo por meio de:

   - Selecionando o link do **Guia de configuração** para exibição em uma janela ou guia separada do navegador, ou
   - Acessando o título **Configurar** e selecionando **exibir instruções** passo a passo para exibir em uma barra lateral.

1. Nas instruções, observe o formato de codificação necessário para o carregamento do certificado.
1. Siga as instruções na seção [certificado gerado automaticamente para galeria e aplicativos inexistentes na Galeria](#auto-generated-certificate-for-gallery-and-non-gallery-applications) anteriormente. Esta etapa baixa o certificado no formato de codificação necessário para carregar pelo aplicativo.
1. Quando desejar passar para o novo certificado, volte para a página **certificado de autenticação SAML** e, na linha certificado recentemente salvo, selecione as reticências (**...**) e selecione **tornar o certificado ativo**. O status do novo certificado é alterado para **ativo** e o certificado anteriormente ativo muda para um status **inativo**.
1. Continue seguindo as instruções de configuração de logon do SAML do aplicativo que você exibiu anteriormente, para que você possa carregar o certificado de autenticação SAML no formato de codificação correto.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Adicionar endereços de notificação por email para expiração do certificado

O Azure AD enviará uma notificação por email 60, 30 e 7 dias antes que o certificado SAML expire. Você pode adicionar mais de um endereço de email para receber notificações. Para especificar o endereço (s) de email para o qual você deseja que as notificações sejam enviadas:

1. Na página **certificado de autenticação SAML** , vá para o título **endereços de email de notificação** . Por padrão, esse cabeçalho usa apenas o endereço de email do administrador que adicionou o aplicativo.
1. Abaixo do endereço de email final, digite o endereço de email que deve receber o aviso de expiração do certificado e pressione Enter.
1. Repita a etapa anterior para cada endereço de email que você deseja adicionar.
1. Para cada endereço de email que você deseja excluir, selecione o ícone **excluir** (um lixo pode) ao lado do endereço de email.
1. Clique em **Salvar**.

Você receberá o email de notificação de aadnotification@microsoft.com. Para evitar que o email vá para seu local de spam, adicione este email aos seus contatos.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Renovar um certificado que vencerá em breve

Se um certificado estiver prestes a expirar, você poderá renová-lo usando um procedimento que não resulte em nenhum tempo de inatividade significativo para os usuários. Para renovar um certificado expirando:

1. Siga as instruções na seção [criar um novo certificado](#create-a-new-certificate) anteriormente, usando uma data que se sobrepõe ao certificado existente. Essa data limita a quantidade de tempo de inatividade causado pela expiração do certificado.
1. Se o aplicativo puder se estender automaticamente por um certificado, defina o novo certificado como ativo seguindo estas etapas:
   1. Volte para a página **certificado de autenticação SAML** .
   1. Na linha certificado recentemente salvo, selecione as reticências (**...**) e, em seguida, selecione **tornar certificado ativo**.
   1. Ignore as duas próximas etapas.

1. Se o aplicativo só puder manipular um certificado por vez, escolha um intervalo de tempo de inatividade para executar a próxima etapa. (Caso contrário, se o aplicativo não selecionar automaticamente o novo certificado, mas puder lidar com mais de um certificado de autenticação, você poderá executar a próxima etapa a qualquer momento.)
1. Antes de expirar o certificado antigo, siga as instruções na seção [carregar e ativar um certificado](#upload-and-activate-a-certificate) anteriormente.
1. Entre no aplicativo para certificar-se de que o certificado funciona corretamente.

## <a name="related-articles"></a>Artigos relacionados

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](../saas-apps/tutorial-list.md)
- [Gerenciamento de aplicativos com o Azure Active Directory](what-is-application-management.md)
- [Logon único em aplicativos no Azure Active Directory](what-is-single-sign-on.md)
- [Depurar o logon único baseado em SAML para aplicativos no Azure Active Directory](./debug-saml-sso-issues.md)