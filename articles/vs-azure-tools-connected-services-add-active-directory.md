---
title: "Adicionando um Azure Active Directory usando Serviços Conectados no Visual Studio | Microsoft Docs"
description: "Adicionar um Active Directory do Azure usando a caixa de diálogo Adicionar Serviços Conectados do Visual Studio"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: kraigb
ms.openlocfilehash: a767c93fb271f3aa33d9556c69c511bcac7cb0d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Adicionando um Active Directory do Azure usando Serviços Conectados no Visual Studio
Ao usar o Azure AD (Azure Active Directory), você pode oferecer suporte ao SSO (Logon Único) para aplicativos Web MVC ASP.NET ou Autenticação do Active Directory nos serviços da API Web. Com a Autenticação do Azure Active Directory, os usuários podem usar suas contas no Azure Active Directory para se conectar aos aplicativos Web. As vantagens da Autenticação do Azure Active Directory com a API Web incluem segurança aprimorada de dados ao expor uma API de um aplicativo Web. Com o AD do Azure, você não precisa gerenciar um sistema de autenticação separado com a própria conta e gerenciamento de usuários.

## <a name="prerequisites"></a>Pré-requisitos
- Conta do Azure – se não tiver uma conta do Azure, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Conectar-se ao Azure Active Directory usando a caixa de diálogo Serviços Conectados
1. No Visual Studio, crie ou abra um projeto ASP.NET MVC ou um projeto de API Web ASP.NET.

1. No Gerenciador de Soluções, clique com o botão direito do mouse no nó **Serviços Conectados** e, no menu de contexto, selecione **Adicionar Serviços Conectados**.

1. Na página **Serviços Conectados**, selecione **Autenticação com o Azure Active Directory**.
   
    ![Página Serviços Conectados](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. Na página **Introdução** do assistente **Configurar a Autenticação do Azure AD**, selecione **Avançar**.
   
    ![Página Introdução](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1. Na página **Logon Único** do assistente **Configurar a Autenticação do Azure AD**, selecione um domínio da lista suspensa **Domínio**. A lista de domínios contém todos os domínios que podem ser acessados por contas listadas na caixa de diálogo Configurações de Conta. Como alternativa, você poderá inserir um nome de domínio se não encontrar o que estiver procurando, por exemplo `mydomain.onmicrosoft.com`. Você pode escolher a opção para criar um novo aplicativo do Azure Active Directory ou usar as configurações de um aplicativo do Azure Active Directory existente. Quando terminar, escolha **Avançar**.
   
    ![Página Logon único](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)

1. Na página **Acesso ao Diretório** do assistente **Configurar a Autenticação do Azure AD**, certifique-se de que a opção **Ler dados do diretório** está marcada. 
   
    ![Página Acesso ao diretório](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Selecione **Concluir** para adicionar as referências e o código de configuração necessários para habilitar seu projeto para a autenticação do Azure AD. Você pode ver o domínio do Active Directory no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. O Visual Studio exibirá um artigo [O Que Aconteceu](#how-your-project-is-modified) mostrando como seu projeto foi modificado. Para verificar se tudo funcionou, abra um dos arquivos de configuração modificados e veja se as configurações mencionadas no artigo estão lá. 

## <a name="how-your-project-is-modified"></a>Como o projeto é modificado
Quando você executa o assistente, o Visual Studio adiciona o Azure Active Directory e referências associadas a seu projeto. Os arquivos de configuração e os arquivos de código em seu projeto também são modificados para adicionar suporte ao AD do Azure. As modificações específicas que o Visual Studio faz dependem do tipo de projeto. Para obter informações detalhadas sobre como os projetos MVC ASP.NET são modificados, consulte [O que aconteceu — projetos MVC](http://go.microsoft.com/fwlink/p/?LinkID=513809). Para projetos de API Web, confira [O que aconteceu — projetos API Web](http://go.microsoft.com/fwlink/p/?LinkId=513810).

## <a name="next-steps"></a>Próximas etapas
* [Fórum do MSDN do Azure Active Directory](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Documentação do Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Postagem do Blog: Introdução ao Azure Active Directory](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

