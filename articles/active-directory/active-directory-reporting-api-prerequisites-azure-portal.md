---
title: "Pré-requisitos para acessar a API de relatório do Azure AD | Microsoft Docs"
description: "Aprenda sobre os pré-requisitos para acessar a API de relatório do Azure AD"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/18/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: 5fafd83c337e3c73260d89cdad7409a01ce5855b
ms.contentlocale: pt-br
ms.lasthandoff: 08/07/2017

---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Pré-requisitos para acessar a API de relatório do Azure AD

As [APIs de relatório do Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação.

A API de relatório usa [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar o acesso às APIs da Web. 

Para obter acesso aos dados de relatórios por meio da API, você precisa ter uma das seguintes funções atribuídas:

- Leitor de segurança
- Administrador de Segurança
- Administrador global


Para preparar seu acesso à API de relatório, faça o seguinte:

1. Registrar um aplicativo 
2. Conceder permissões 
3. Reunir definições de configuração 

Em caso de dúvidas, problemas ou comentários, [registre um tíquete de suporte](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="register-an-azure-active-directory-application"></a>Registrar um aplicativo do Azure Active Directory

Você precisa registrar um aplicativo mesmo que esteja acessando a API de relatório usando um script. Isso lhe dá uma **ID do aplicativo**, que é necessária para uma chamada de autorização e permite que seu código receba tokens.

Para configurar seu diretório para acessar a API de relatório do Azure AD, entre no Portal do Azure com uma conta de administrador do Azure que também seja membro da função de diretório de **Administrador Global** em seu locatário do Azure AD.

> [!IMPORTANT]
> Aplicativos em execução com credenciais com privilégios de "admin" como esse podem ser muito poderosos, portanto certifique-se de proteger as credenciais de ID/segredo do aplicativo.
> 


**Para registrar um aplicativo do Azure Active Directory:**

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na folha **Azure Active Directory**, clique em **Registros do aplicativo**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. Na folha **Registros do aplicativo**, na barra de ferramentas na parte superior, clique em **Novo registro de aplicativo**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. Na folha **Criar**, execute as seguintes etapas:

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. Na caixa de texto **Nome**, digite `Reporting API application`.

    b. Como **Tipo de aplicativo**, selecione **Aplicativo/API Web**.

    c. Na caixa de texto **URL de Entrada**, digite `https://localhost`.

    d. Clique em **Criar**. 


## <a name="grant-permissions"></a>Conceder permissões 

O objetivo desta etapa é conceder ao seu aplicativo permissões para **Ler dados do diretório** para a API do **Microsoft Azure Active Directory**.

![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/16.png)
 

**Para conceder ao seu aplicativo permissão para usar a API:**

1. Na folha **Registros do aplicativo**, na lista de aplicativos, clique em **Aplicativo da API de relatório**.

2. Na folha **Aplicativo da API de relatório**, na barra de ferramentas na parte superior, clique em **Configurações**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. Na folha **Configurações**, clique em **Permissões necessárias**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. Na folha **Permissões necessárias**, na lista **API**, clique em **Microsoft Azure Active Directory**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. Na folha **Habilitar Acesso**, selecione **Ler dados do diretório**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. Na barra de ferramentas na parte superior, clique em **Salvar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

## <a name="gather-configuration-settings"></a>Reunir definições de configuração 
Esta seção mostra como obter as seguintes configurações de seu diretório:

* Nome de domínio
* ID do cliente
* Segredo do cliente

Você precisa desses valores ao configurar chamadas para a API de relatórios. 

### <a name="get-your-domain-name"></a>Obter seu nome de domínio

**Para obter seu nome de domínio:**

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na folha **Azure Active Directory**, clique em **Nomes de domínio**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Copie o nome do domínio da lista de domínios.


### <a name="get-your-applications-client-id"></a>Obtenha a ID do cliente do aplicativo

**Para obter a ID do cliente do aplicativo:**

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na folha **Registros do aplicativo**, na lista de aplicativos, clique em **Aplicativo da API de relatório**.

3. Na folha **Aplicativo da API de relatório**, em **ID do aplicativo**, clique em **Clique para copiar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Obter seu segredo do cliente do aplicativo
Para obter o segredo do cliente do aplicativo, você precisa criar uma nova chave e salvar seu valor ao salvar a nova chave, pois não é possível recuperar este valor posteriormente.

**Para obter seu segredo do cliente do aplicativo:**

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na folha **Registros do aplicativo**, na lista de aplicativos, clique em **Aplicativo da API de relatório**.


3. Na folha **Aplicativo da API de relatório**, na barra de ferramentas na parte superior, clique em **Configurações**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. Na folha **Configurações**, na seção **Acesso APIR**, clique em **Chaves**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. Na folha **Chaves**, execute as seguintes etapas:

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. Na caixa de texto **Descrição**, digite `Reporting API`.

    b. Como **Expira**, selecione **Em 2 anos**.

    c. Clique em **Salvar**.

    d. Copie o valor da chave.


## <a name="next-steps"></a>Próximas etapas
* Você gostaria de acessar os dados da API de relatório do Azure AD de uma maneira programática? Confira [Introdução à API de Relatório do Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Se você quiser saber mais sobre os relatórios do Azure Active Directory, confira o [Guia de relatórios do Azure Active Directory](active-directory-reporting-guide.md).  


