---
title: "Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph | Microsoft Docs"
description: "Fornece uma introdução à consulta ao Microsoft Graph para obter uma lista de eventos de risco e as informações associadas do Azure Active Directory."
services: active-directory
keywords: "azure active directory identity protection, evento de risco, vulnerabilidade, política de segurança, Microsoft Graph"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 9b01ff86da6a1fd4a439a6ba59ea15ed6480cdad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph
O Microsoft Graph é o ponto de extremidade de API unificado da Microsoft e a página inicial das APIs do [Azure Active Directory Identity Protection](active-directory-identityprotection.md). A primeira API, **identityRiskEvents**, permite que você consulte o Microsoft Graph para obter uma lista de [eventos de risco](active-directory-identityprotection-risk-events-types.md) e informações associadas. Este artigo mostra como começar a consultar essa API. Para obter uma introdução detalhada, a documentação completa e acesso ao Explorador do Graph, consulte o [site do Microsoft Graph](https://graph.microsoft.io/).

> [!IMPORTANT]
> A Microsoft recomenda que você gerencie o Azure AD usando o [Centro de administração do AD do Azure](https://aad.portal.azure.com) no portal do Azure em vez de usar o portal clássico do Azure mencionado neste artigo.

Há três etapas para acessar dados de Proteção de Identidade por meio do Microsoft Graph:

1. Adicione um aplicativo com um segredo do cliente. 
2. Use esse segredo e algumas outras informações para se autenticar no Microsoft Graph, onde você recebe um token de autenticação. 
3. Use esse token para fazer solicitações para o ponto de extremidade de API e para obter dados do Identity Protection.

Antes de começar, será necessário:

* Privilégios de administrador para criar o aplicativo no Azure AD
* O nome do domínio do seu locatário (por exemplo, contoso.onmicrosoft.com)

## <a name="add-an-application-with-a-client-secret"></a>Adicionar um aplicativo com um segredo do cliente
1. [Entre](https://manage.windowsazure.com) no portal clássico do Azure como administrador. 
2. No painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)
3. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
4. No menu superior, clique em **Aplicativos**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)
5. Clique em **Adicionar** na parte inferior da página.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)
6. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo que minha organização está desenvolvendo**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)
7. Na página de diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)
   
    a. Na caixa de texto **Nome** , digite um nome para seu aplicativo (por exemplo: AADIP Aplicativo de API de Evento de Risco).
   
    b. Como **Tipo**, selecione **Aplicativo Web E/Ou API Web**.
   
    c. Clique em **Avançar**.
8. Na caixa de diálogo **Propriedades de aplicativo** , execute as seguintes etapas:
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)
   
    a. Na caixa de texto **URL de Logon**, digite `http://localhost`.
   
    b. Na caixa de texto **URI da ID do Aplicativo**, digite `http://localhost`.
   
    c. Clique em **Concluído**.

Agora você pode configurar seu aplicativo.

![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)

## <a name="grant-your-application-permission-to-use-the-api"></a>Conceda permissão ao aplicativo para usar a API
1. Na página do seu aplicativo, no menu superior, clique em **Configurar**. 
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)
2. Na seção **permissões para outros aplicativos**, clique em **Adicionar aplicativo**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)
3. Na caixa de diálogo **permissões para outros aplicativos** , execute as seguintes etapas:
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)
   
    a. Selecione **Microsoft Graph**.
   
    b. Clique em **Concluído**.
4. Clique em **Permissões do Aplicativo: 0**, em seguida, selecione **Ler todas as informações de evento de risco da identidade**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)
5. Na parte inferior da página, clique em **Salvar** .
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

## <a name="get-an-access-key"></a>Obter uma chave de acesso
1. Na página do seu aplicativo, na seção **chaves** , selecione 1 ano como duração.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)
2. Na parte inferior da página, clique em **Salvar** .
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)
3. na seção chaves, copie o valor da chave recém-criada e o cole em um local seguro.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)
   
   > [!NOTE]
   > Se você perder esta chave, precisará retornar a esta seção e criar uma nova chave. Mantenha essa chave em segredo: qualquer pessoa que a tenha poderá acessar seus dados.
   > 
   > 
4. Na seção **propriedades**, copie a **ID do cliente** e cole-a em um local seguro. 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autenticar-se no Microsoft Graph e consultar a API de Eventos de Risco de Identidade
Neste ponto, você deve ter:

* A ID do cliente copiada acima
* A chave copiada acima
* O nome do domínio do locatário

Para autenticar, envie uma solicitação post para `https://login.microsoft.com` com os seguintes parâmetros no corpo:

* grant_type: “**client_credentials**”
* resource: “**https://graph.microsoft.com**”
* client_id: <your client ID>
* client_secret: <your key>

> [!NOTE]
> Você precisa fornecer valores para os parâmetro **client_id** e **client_secret**.
> 
> 

Se for bem-sucedido, será retornado um token de autenticação.  
Para chamar a API, crie um cabeçalho com o seguinte parâmetro:

    `Authorization`=”<token_type> <access_token>"


Durante a autenticação, você poderá encontrar o tipo de token e o token de acesso no token retornado.

Envie este cabeçalho como uma solicitação para a seguinte URL de API: `https://graph.microsoft.com/beta/identityRiskEvents`

A resposta, se obtiver êxito, será uma coleção de eventos de risco de identidade e de dados associados no formato JSON OData, que poderá ser analisado e manipulado como for melhor.

Veja um código de exemplo para autenticação e chamada da API usando o Powershell.  
Basta adicionar sua ID do cliente, a chave e o domínio do locatário.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Próximas etapas
Parabéns, você acabou de criar sua primeira chamada para o Microsoft Graph!  
Agora você pode consultar os eventos de risco de identidade e usar os dados como quiser.

Para saber mais sobre o Microsoft Graph e como criar aplicativos usando a API do Graph, confira a [documentação](https://graph.microsoft.io/docs) e muito mais no [site Microsoft Graph](https://graph.microsoft.io/). Além disso, marque a página [API do Azure AD Identity Protection](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) que lista todas as APIs do Identity Protection disponíveis no Graph. À medida que adicionarmos novas maneiras de trabalhar com o Identity Protection via API, você as verá nessa página.

## <a name="additional-resources"></a>Recursos adicionais
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
* [Tipos de eventos de risco detectados pelo Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)
* [Microsoft Graph](https://graph.microsoft.io/)
* [Visão geral do Microsoft Graph](https://graph.microsoft.io/docs)
* [Raiz do Serviço Azure AD Identity Protection](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

