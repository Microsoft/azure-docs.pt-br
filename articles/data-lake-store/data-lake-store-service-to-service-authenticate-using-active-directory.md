---
title: "Autenticação de serviço a serviço: Data Lake Store com o Azure Active Directory | Microsoft Docs"
description: "Saiba como obter a autenticação de serviço a serviço com o Data Lake Store usando o Azure Active Directory"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 9a4502ecfd5471cc2a968f995e66e595f59384b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticação de serviço a serviço com o Data Lake Store usando o Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

O Azure Data Lake Store usa o Azure Active Directory para autenticação. Antes de criar um aplicativo que funciona com o Azure Data Lake Store, você deve decidir como deseja autenticar seu aplicativo no Azure AD (Azure Active Directory). As duas principais opções disponíveis são:

* Autenticação do usuário final 
* Autenticação serviço a serviço (este artigo) 

As duas opções resultam no fornecimento de um token OAuth 2.0 ao seu aplicativo, que é anexado a cada solicitação feita ao Azure Data Lake Store.

Este artigo explica como criar um **aplicativo Web do Azure AD para autenticação serviço a serviço**. Para obter instruções sobre a configuração de aplicativo do Azure AD para autenticação de usuário final, consulte [Autenticação de usuário final com o Data Lake Store usando o Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Etapa 1: Criar um aplicativo Web do Active Directory

Crie e configure um aplicativo Web do Azure AD para autenticação serviço a serviço com o Azure Data Lake Store usando o Azure Active Directory. Para obter instruções, consulte [Criar um aplicativo do Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Ao seguir as instruções do link anterior, verifique se você selecionou **Aplicativo Web/API** como tipo de aplicativo, conforme mostrado na seguinte captura de tela:

![Criar aplicativo Web](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Criar aplicativo Web")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Etapa 2: Obter a ID do aplicativo, a chave de autenticação e a ID de locatário
Ao fazer logon por meio de programação, você precisa da ID para seu aplicativo. Se o aplicativo for executado com suas próprias credenciais, você também precisará de uma chave de autenticação.

* .Para obter instruções sobre como recuperar a ID e o segredo do cliente do aplicativo, consulte [Obter ID do aplicativo e chave de autenticação](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Para obter instruções sobre como recuperar a ID do locatário, consulte [Obter ID do locatário](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder"></a>Etapa 3: Atribuir o aplicativo do Azure AD ao arquivo ou pasta da conta do Azure Data Lake Store


1. Entre no [portal do Azure](https://portal.azure.com). Abra a conta do Azure Data Lake Store que você deseja associar ao aplicativo do Azure Active Directory criado anteriormente.
2. Na folha de sua conta do Repositório Data Lake, clique em **Gerenciador de Dados**.
   
    ![Crie diretórios na conta do Data Lake Store](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "criar diretórios na conta Data Lake")
3. Na folha **Data Explorer**, clique no arquivo ou pasta para o qual você deseja fornecer acesso ao aplicativo do Azure AD e, em seguida, clique em **Acessar**. Para configurar o acesso a um arquivo, você deverá clicar em **Acessar** da folha **Visualização do Arquivo**.
   
    ![Configurar ACLs no sistema de arquivos do Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "definir ACLs no sistema de arquivos do Data Lake")
4. A folha **Acesso** lista o acesso padrão e o acesso personalizado já atribuídos à raiz. Clique no ícone **Adicionar** para adicionar as ACLs de nível personalizado.
   
    ![Lista de acesso padrão e personalizado](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "lista de acesso padrão e personalizado")
5. Clique no ícone **Adicionar** para abrir a folha **Adicionar Acesso Personalizado**. Nessa folha, clique em **Selecionar Usuário ou Grupo** e, na folha **Selecionar Usuário ou Grupo**, procure o aplicativo do Azure Active Directory que você criou anteriormente. Se houver muitos grupos para sua pesquisa, use a caixa de texto na parte superior para filtrar pelo nome do grupo. Clique no grupo que você deseja adicionar e clique em **Selecionar**.
   
    ![Adicionar um grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Adicionar um grupo")
6. Clique em **Selecionar Permissões**, selecione as permissões e se você desejar atribuir as permissões como uma ACL padrão, acessar a ACL ou ambos. Clique em **OK**.
   
    ![Atribuir permissões ao grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Atribuir permissões ao grupo")
   
    Para obter mais informações sobre permissões no Data Lake Store e ACLs de acesso/padrão, consulte [Controle de acesso no Data Lake Store](data-lake-store-access-control.md).
7. Na folha **Adicionar Acesso Personalizado**, clique em **OK**. O grupo recém-adicionado, com as permissões associadas, está listado na folha **Acesso**.
   
    ![Atribuir permissões ao grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Atribuir permissões ao grupo")

> [!NOTE]
> Se quiser usar os SDKs para criar uma conta do Data Lake Store, você deve atribuir o aplicativo Web do Azure AD como uma função para o Grupo de recursos no qual você criará a conta do Azure Data Lake Store.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Etapa 4: obter o ponto de extremidade do Token OAuth 2.0 (somente para aplicativos baseados em Java)

1. Faça logon no [Portal do Azure](https://portal.azure.com) e clique em Active Directory, no painel esquerdo.

2. No painel esquerdo, clique em **Registros do aplicativo**.

3. Na parte superior da folha Registros do aplicativo, clique em **Pontos de extremidade**.

    ![Ponto de extremidade de token OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Ponto de extremidade de token OAuth")

4. Da lista de pontos de extremidade, copie o ponto de extremidade do token OAuth 2.0.

    ![Ponto de extremidade de token OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Ponto de extremidade de token OAuth")   

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um aplicativo Web do Azure AD e reuniu as informações necessárias em seus aplicativos cliente que você cria usando SDK do .NET, Java, Python, API REST, etc. Agora você pode prosseguir para os artigos seguintes, que falam sobre como usar o aplicativo nativo do Azure AD para primeiro se autenticar no Data Lake Store e, em seguida, executar outras operações no repositório.

* [Autenticação serviço a serviço com o Data Lake Store usando o Java](data-lake-store-service-to-service-authenticate-java.md)
* [Autenticação serviço a serviço com o Data Lake Store usando o SDK do .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Autenticação serviço a serviço com o Data Lake Store usando o Python](data-lake-store-service-to-service-authenticate-python.md)
* [Autenticação serviço a serviço com o Data Lake Store usando a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)


