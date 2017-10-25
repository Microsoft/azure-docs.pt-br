---
title: Registro de aplicativo do Azure Active Directory | Microsoft Docs
description: Este artigo descreve como usar o portal do Azure para registrar um aplicativo no Azure Active Directory
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 7dc7b89f-653f-405a-b5f4-2c1288720c15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: priyamo
ms.reviewer: elisol
ms.openlocfilehash: 2f2817688beb2028fd0bba8522827d87a0097f21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="register-your-application-with-your-azure-active-directory-tenant"></a>Registrar seu aplicativo com o locatário do Azure Active Directory

Você pode usar o portal do Azure para registrar seu aplicativo em seu locatário do Azure AD (Azure Active Directory). Isso cria uma ID de aplicativo para o aplicativo e permite que ele receba tokens.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Escolha seu locatário do Azure AD ao selecionar sua conta no canto superior direito da página.
3. No painel de navegação à esquerda, escolha **Mais Serviços**, clique em **Registros de Aplicativo** e clique em **Adicionar**.
4. Siga os avisos e crie um novo aplicativo. Se você desejar exemplos específicos para aplicativos da Web ou aplicativos nativos, confira nossos [inícios rápidos](active-directory-developers-guide.md).
  * Para os Aplicativos Web, forneça a **URL de Entrada**, que é a URL base do seu aplicativo, onde os usuários podem entrar como `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Para os Aplicativos Nativos, forneça um **URI de redirecionamento**, que será usado pelo Azure AD para retornar respostas de token. Insira um valor específico para seu aplicativo, por exemplo, `http://MyFirstAADApp`
5. Depois de concluir o registro, o Azure AD atribuirá a seu aplicativo um identificador de cliente único, a ID do Aplicativo.

## <a name="update-application-settings-from-the-azure-portal"></a>Atualizar as configurações do aplicativo no portal do Azure

Você pode facilmente modificar configurações do aplicativo existentes usando o portal do Azure. Por exemplo, você talvez queira configurar uma URL de resposta, que é de onde o Azure AD emite respostas de tokens. Talvez você também deseje configurar permissões para outros aplicativos, por exemplo, para permitir que o aplicativo acesse a API do Microsoft Graph. Você pode fazer tudo isso por meio da página de configurações do aplicativo.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Escolha seu locatário do Azure AD ao selecionar sua conta no canto superior direito da página.
3. No painel de navegação à esquerda, escolha **Mais Serviços**, clique em **Registros do Aplicativo** e escolha o aplicativo na lista.
4. Clique em **Configurações** para abrir a página de configurações para o aplicativo.
  * A página **Propriedades** permite que você modifique as informações gerais do aplicativo. Isso inclui o nome do aplicativo, a URL de entrada e a URL de logoff.
  * A página **URLs de Resposta** permite que você adicione uma URL de resposta, que é para onde o Azure AD envia respostas de tokens.
  * A página **Proprietários** permite que você adicione os proprietários do aplicativo.
  * A página **Permissões** permite que você configure as permissões para o aplicativo. Por exemplo, para acessar a API do Microsoft Graph, clique em **Adicionar** e selecione **Microsoft Graph** no seletor de API; em seguida, escolha a permissão necessária, por exemplo, **Ler Dados do Diretório**.
  * A página **Chaves** permite que você adicione segredos do aplicativo. O segredo só será exibido uma vez imediatamente após a criação, portanto não deixe de copiá-lo para uso posterior.

## <a name="use-the-inline-manifest-editor"></a>Usar o editor de manifesto embutido

Você pode usar o editor de manifesto embutido para modificar determinadas propriedades de aplicativo que não estejam diretamente expostas no portal do Azure. Por exemplo, você pode usá-lo para modificar o URI de ID do aplicativo ou para permitir o fluxo implícito do OAuth2.0 em vez do fluxo de código de concessão de autorização padrão.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Escolha seu locatário do Azure AD ao selecionar sua conta no canto superior direito da página.
3. No painel de navegação à esquerda, escolha **Mais Serviços**, clique em **Registros do Aplicativo** e escolha o aplicativo na lista.
4. Clique em **Manifesto** na página do aplicativo para abrir o editor de manifesto embutido.
5. Você pode fazer alterações diretamente no manifesto e salvá-lo quando estiver pronto. Como alternativa, você pode baixar o manifesto para abri-lo em seu editor favorito e carregar o manifesto atualizado.

## <a name="next-steps"></a>Próximas etapas

1. Confira os [Inícios rápidos](active-directory-developers-guide.md) para obter instruções passo a passo detalhadas de aplicativos que executam a autenticação usando o Azure AD.
2. Confira a lista completa de exemplos de código no [GitHub](https://github.com/azure-samples).
