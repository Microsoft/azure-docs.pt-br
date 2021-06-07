---
title: Autenticação serviço a serviço-Data Lake Storage Gen1-Azure
description: Saiba como obter a autenticação serviço a serviço com Azure Data Lake Storage Gen1 usando Azure Active Directory.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 9dc195f98310e63cbde06885effe86ea3c239249
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576091"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autenticação de serviço a serviço com o Azure Data Lake Storage Gen1 usando o Active Directory do Azure
> [!div class="op_single_selector"]
> * [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

O Azure Data Lake Storage Gen1 usa o Azure Active Directory para autenticação. Antes de criar um aplicativo que funcione com o Data Lake Storage Gen1, você deve decidir como autenticar seu aplicativo com o Azure AD (Azure Active Directory). As duas principais opções disponíveis são:

* Autenticação do usuário final 
* Autenticação serviço a serviço (este artigo) 

Essas duas opções resultam em seu aplicativo ser fornecido com um token OAuth 2.0, que é anexado a cada solicitação feita ao Data Lake Storage Gen1.

Este artigo explica como criar um **aplicativo Web do Azure AD para autenticação serviço a serviço**. Para obter instruções sobre a configuração do aplicativo do Azure AD para autenticação de usuário final, consulte [Autenticação de usuário final com o Data Lake Storage Gen1 usando o Active Directory do Azure](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Etapa 1: Criar um aplicativo Web do Active Directory

Crie e configure um aplicativo Web do Azure AD para autenticação de serviço a serviço com o Azure Data Lake Storage Gen1 usando o Active Directory do Azure. Para obter instruções, consulte [Criar um aplicativo do Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Ao seguir as instruções do link anterior, verifique se você selecionou **Aplicativo Web/API** como tipo de aplicativo, conforme mostrado na seguinte captura de tela:

![Criar um aplicativo Web](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Criar um aplicativo Web")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Etapa 2: Obter a ID do aplicativo, a chave de autenticação e a ID de locatário
Ao fazer logon por meio de programação, você precisa da ID para seu aplicativo. Se o aplicativo for executado com suas próprias credenciais, você também precisará de uma chave de autenticação.

* .Para obter instruções sobre como recuperar a ID e o segredo do cliente do aplicativo, consulte [Obter ID do aplicativo e chave de autenticação](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

* Para obter instruções sobre como recuperar a ID do locatário, consulte [Obter ID do locatário](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Etapa 3: atribua o aplicativo do Azure AD ao arquivo ou à pasta da conta do Azure Data Lake Storage Gen1


1. Entre no [Portal do Azure](https://portal.azure.com). Abra a conta do Data Lake Storage Gen1 que você deseja associar ao aplicativo Azure Active Directory criado anteriormente.
2. Na folha de sua conta do Data Lake Storage Gen1, clique em **Data Explorer**.
   
    ![Criar diretórios na conta de Data Lake Storage Gen1](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Criar diretórios na conta do Data Lake")
3. Na folha **Data Explorer**, clique no arquivo ou pasta para o qual você deseja fornecer acesso ao aplicativo do Azure AD e, em seguida, clique em **Acessar**. Para configurar o acesso a um arquivo, você deverá clicar em **Acessar** da folha **Visualização do Arquivo**.
   
    ![Definir ACLs no sistema de arquivos do Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Definir ACLs no sistema de arquivos do Data Lake")
4. A folha **Acesso** lista o acesso padrão e o acesso personalizado já atribuídos à raiz. Clique no ícone **Adicionar** para adicionar as ACLs de nível personalizado.
   
    ![Listar acesso padrão e personalizado](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Listar acesso padrão e personalizado")
5. Clique no ícone **Adicionar** para abrir a folha **Adicionar Acesso Personalizado**. Nessa folha, clique em **Selecionar Usuário ou Grupo** e, na folha **Selecionar Usuário ou Grupo**, procure o aplicativo do Azure Active Directory que você criou anteriormente. Se houver muitos grupos para sua pesquisa, use a caixa de texto na parte superior para filtrar pelo nome do grupo. Clique no grupo que você deseja adicionar e clique em **Selecionar**.
   
    ![Adicionar um grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Adicionar um grupo")
6. Clique em **Selecionar Permissões**, selecione as permissões e se você desejar atribuir as permissões como uma ACL padrão, acessar a ACL ou ambos. Clique em **OK**.
   
    ![Captura de tela da folha adicionar acesso personalizado com a opção Selecionar permissões chamada out e a folha selecionar permissões com a opção OK chamada out.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Atribuir permissões ao grupo")
   
    Para obter mais informações sobre permissões no Data Lake armazenamento Gen1 e ACLs de acesso/padrão, consulte [controle de acesso no Data Lake armazenamento Gen1](data-lake-store-access-control.md).
7. Na folha **Adicionar Acesso Personalizado**, clique em **OK**. Os grupos adicionados recentemente, com as permissões associadas, são listados na folha **acesso** .
   
    ![Captura de tela da folha de acesso com o grupo recém-adicionado chamado na seção acesso personalizado.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Atribuir permissões ao grupo")

> [!NOTE]
> Se você planeja restringir o aplicativo do Azure Active Directory a uma pasta específica, também será necessário conceder a esse aplicativo do Azure Active Directory a mesma permissão **Executar** à raiz para permitir o acesso de criação de arquivo por meio do SDK do .NET.

> [!NOTE]
> Se você quiser usar os SDKs para criar uma conta do Data Lake Storage Gen1, atribua o aplicativo da Web do Azure AD como uma função ao Grupo de Recursos no qual você cria a conta do Data Lake Storage Gen1.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Etapa 4: obter o ponto de extremidade do Token OAuth 2.0 (somente para aplicativos baseados em Java)

1. Faça logon no [Portal do Azure](https://portal.azure.com) e clique em Active Directory, no painel esquerdo.

2. No painel esquerdo, clique em **Registros do aplicativo**.

3. Na parte superior da folha Registros do aplicativo, clique em **Pontos de extremidade**.

    ![Captura de tela de Active Directory com a opção Registros de aplicativo e a opção pontos de extremidade chamados.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Ponto de extremidade de token OAuth")

4. Da lista de pontos de extremidade, copie o ponto de extremidade do token OAuth 2.0.

    ![Captura de tela da folha de pontos de extremidade com o ícone de cópia de ponto final de autenticação o do ponto de entrada 2.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Ponto de extremidade de token OAuth")   

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um aplicativo Web do Azure AD e reuniu as informações necessárias em seus aplicativos cliente que você cria usando o SDK do .NET, Java, Python, API REST, etc. Agora você pode prosseguir para os seguintes artigos que falam sobre como usar o aplicativo nativo do Azure AD para primeiro autenticar com Data Lake Storage Gen1 e, em seguida, executar outras operações no repositório.

* [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando Java](data-lake-store-service-to-service-authenticate-java.md)
* [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Autenticação serviço a serviço com o Data Lake Storage Gen1 usando Python](data-lake-store-service-to-service-authenticate-python.md)
* [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)


