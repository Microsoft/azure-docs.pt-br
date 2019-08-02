---
title: Gerenciar certificados de federação no Azure AD | Microsoft Docs
description: Saiba como personalizar a data de vencimento para seus certificados de federação e como renovar certificados que vencerão em breve.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 416e72f7e9e8622f044f7bb6430fbb36010b164e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701983"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gerenciar certificados para logon único federado no Azure Active Directory

Neste artigo, abordaremos a dúvidas comuns e informações relacionadas a certificados que o Azure Active Directory (Azure AD) cria para estabelecer federado logon único (SSO) para seu software como um aplicativo de serviço (SaaS). Adicione aplicativos pela galeria de aplicativos do Azure AD ou usando um modelo de aplicativo inexistente na galeria. Configure o aplicativo usando a opção de SSO federada.

Este artigo é relevante apenas para aplicativos que são configurados para usar o SSO do Azure AD por meio [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) federação (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificado gerado automaticamente para aplicativos da galeria e inexistentes na galeria

Quando você adicionar um novo aplicativo da galeria e configurar um baseado em SAML logon (selecionando **logon único** > **SAML** da página de visão geral do aplicativo), o Azure AD gera um certificado para o aplicativo que é válido por três anos. Para baixar o certificado do Active Directory como um certificado de segurança ( **. cer**) arquivo, retorne para a página (**baseado em SAML logon**) e selecione um link de download no **certificado de autenticação de SAML** título. Você pode escolher entre o bruto (binário) ou certificado de Base64 (base texto codificado 64). Para aplicativos da galeria, essa seção também pode mostrar um link para baixar o certificado como o XML de metadados de Federação (um **. XML** arquivo), dependendo do requisito do aplicativo.

![Ativas opções assinatura de download de certificado SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Você também pode baixar um certificado ativo ou inativo, selecionando o **certificado de autenticação SAML** do título **editar** ícone (um lápis), que exibe o **certificado de autenticação de SAML** página. Selecione as reticências ( **...** ) ao lado do certificado que você deseja baixar e, em seguida, escolha qual formato de certificado que você deseja. Você tem a opção adicional para baixar o certificado no formato de email reforçados para privacidade (PEM). Esse formato é idêntico em Base64, mas com um **. PEM** arquivo de extensão de nome, que não é reconhecido no Windows como um formato de certificado.

![Opções de download de certificado (ativas e inativas) de assinatura de SAML](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalizar a data de vencimento do seu certificado de federação e transferir para um novo certificado

Por padrão, o Azure configura um certificado para expirar depois de três anos, quando ele é criado automaticamente durante a configuração de logon única SAML. Como você não pode alterar a data de um certificado depois que você salvá-lo, você precisa:

1. Crie um novo certificado com a data desejada.
1. Salve o novo certificado.
1. Baixe o novo certificado no formato correto.
1. Carregue o novo certificado para o aplicativo.
1. Criar o novo certificado ativo no portal do Azure Active Directory.

Duas seções a seguir o ajudarão a executar essas etapas.

### <a name="create-a-new-certificate"></a>Criar um novo certificado

Primeiro, crie e salve o novo certificado com uma data de vencimento diferente:

1. Entrar para o [portal do Azure Active Directory](https://aad.portal.azure.com/). O **Centro de administração do Azure Active Directory** página será exibida.
1. No painel esquerdo, selecione **Aplicativos Empresariais**. É exibida uma lista de aplicativos empresariais em sua conta.
1. Selecione o aplicativo afetado. É exibida uma página de visão geral do aplicativo.
1. No painel esquerdo da página de visão geral do aplicativo, selecione **logon único**.
1. Se o **selecionar um método de logon único** página for exibida, selecione **SAML**.
1. No **definir o logon único com SAML - Preview** página, localize o **certificado de autenticação SAML** título e selecione o **editar** ícone (um lápis). O **certificado de autenticação SAML** página for exibida, que exibe o status (**Active** ou **inativo**), data de expiração e a impressão digital (uma cadeia de caracteres de hash) de cada certificado.
1. Selecione **novo certificado**. Uma nova linha é exibida abaixo da lista de certificados, em que a data de expiração padrão é exatamente três anos após a data atual. (As alterações ainda não foram salvas ainda, portanto, você ainda pode modificar a data de validade.)
1. Na nova linha de certificado, passe o mouse sobre a coluna de data de expiração e selecione o **Selecionar data** ícone (um calendário). Um controle de calendário aparecerá, exibindo os dias de um mês da data de validade da nova linha atual.
1. Use o controle de calendário para definir uma nova data. Você pode definir qualquer data entre a data atual e três anos após a data atual.
1. Clique em **Salvar**. O novo certificado agora aparece com um status de **inativo**, a expiração de data que você escolher e uma impressão digital.
1. Selecione o **X** para retornar para o **definir o logon único com SAML - visualização** página.

### <a name="upload-and-activate-a-certificate"></a>Carregar e ativar um certificado

Em seguida, baixe o novo certificado no formato correto, carregá-lo para o aplicativo e ativá-lo no Azure Active Directory:

1. Exiba instruções de configuração de logon SAML adicionais do aplicativo, qualquer um:

   - selecionando o **guia de configuração** link para exibir em uma janela separada do navegador ou a guia, ou
   - vai para o **configure** título e selecionando **exibir instruções passo a passo** para exibir em uma barra lateral.

1. As instruções, observe o formato de codificação necessário para o carregamento do certificado.
1. Siga as instruções de [certificado gerado automaticamente para aplicativos da galeria e não](#auto-generated-certificate-for-gallery-and-non-gallery-applications) seção anteriormente. Esta etapa baixa o certificado no formato de codificação necessário para carregamento pelo aplicativo.
1. Quando você deseja passar para o novo certificado, volte para o **certificado de autenticação SAML** página e na linha certificado recém-salvo, selecione as reticências ( **...** ) e selecione **Ativar certificado**. O status do novo certificado muda para **Active**, e o certificado anteriormente ativo é alterado para um status de **inativo**.
1. Continue a seguir instruções de configuração de logon do SAML do aplicativo exibido anteriormente, para que você possa carregar a assinatura de SAML de certificado no formato de codificação correto.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Adicionar endereços de notificação de email para a expiração do certificado

Azure AD enviará um dias de 7, 30 e 60 de notificação de email antes de expira o certificado SAML. Você pode adicionar mais de um endereço de email para receber notificações. Para especificar os endereços de email que você deseja que as notificações sejam enviadas para:

1. No **certificado de autenticação SAML** página, vá para o **endereços de email de notificação** título. Por padrão, este cabeçalho usa apenas o endereço de email do administrador que adicionou o aplicativo.
1. Abaixo do endereço de email final, digite o endereço de email que deve receber o aviso de expiração do certificado e, em seguida, pressione Enter.
1. Repita a etapa anterior para cada endereço de email que você deseja adicionar.
1. Para cada endereço de email que você deseja excluir, selecione a **excluir** ícone (uma lata de lixo) ao lado do endereço de email.
1. Clique em **Salvar**.

Você receberá o email de notificação de aadnotification@microsoft.com. Para evitar o email de seu local de spam, adicione este email com seus contatos.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Renovar um certificado que vencerá em breve

Se um certificado está prestes a expirar, você poderá renová-lo usando um procedimento que resulta em nenhum tempo de inatividade significativo para seus usuários. Para renovar um certificado prestes a expirar:

1. Siga as instruções de [criar um novo certificado](#create-a-new-certificate) seção anterior, usando uma data em que se sobrepõe com o certificado existente. Essa data limita a quantidade de tempo de inatividade causado pela expiração do certificado.
1. Se o aplicativo pode acumular automaticamente um certificado, defina o novo certificado para o Active Directory seguindo estas etapas:
   1. Volte para o **certificado de autenticação SAML** página.
   1. Na linha certificado recém-salvo, selecione as reticências ( **...** ) e, em seguida, selecione **Ativar certificado**.
   1. Ignore as próximas duas etapas.

1. Se o aplicativo pode lidar somente com um certificado por vez, escolha um intervalo de tempo de inatividade para executar a próxima etapa. (Caso contrário, se o aplicativo não seleciona automaticamente o novo certificado, mas pode lidar com mais de um certificado de autenticação, você pode executar a próxima etapa a qualquer momento.)
1. Antes de expira o certificado antigo, siga as instruções de [carregar e ativar um certificado](#upload-and-activate-a-certificate) seção anteriormente.
1. Entrar aplicativo para certificar-se de que o certificado está funcionando corretamente.

## <a name="related-articles"></a>Artigos relacionados

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](../saas-apps/tutorial-list.md)
- [Gerenciamento de aplicativos com o Azure Active Directory](what-is-application-management.md)
- [Logon único em aplicativos no Azure Active Directory](what-is-single-sign-on.md)
- [Depurar baseado em SAML SSO para aplicativos no Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
