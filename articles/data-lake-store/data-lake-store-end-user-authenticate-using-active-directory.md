---
title: "Autenticação do usuário final: Data Lake Store com o Azure Active Directory | Microsoft Docs"
description: "Saiba como obter a autenticação do usuário final com o Data Lake Store usando o Azure Active Directory"
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
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: 98898675b85d62c97a215f9922f1393001013943
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticação do usuário final com o Data Lake Store usando o Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

O Azure Data Lake Store usa o Azure Active Directory para autenticação. Antes de criar um aplicativo que funciona com o Azure Data Lake Store ou com o Azure Data Lake Analytics, você deve decidir como deseja autenticar seu aplicativo no Azure AD (Azure Active Directory). As duas principais opções disponíveis são:

* Autenticação do usuário final (este artigo)
* Autenticação serviço a serviço (selecione essa opção na lista suspensa acima)

As duas opções resultam no fornecimento de um token OAuth 2.0 a seu aplicativo, que é anexado a cada solicitação feita ao Azure Data Lake Store ou ao Azure Data Lake Analytics.

Este artigo descreve como criar um **aplicativo nativo do Azure AD para autenticação do usuário final**. Para obter instruções sobre a configuração de aplicativo do Azure AD para autenticação serviço a serviço, confira [Autenticação de serviço a serviço com o Data Lake Store usando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* A ID de sua assinatura. Você pode recuperá-la no portal do Azure. Por exemplo, está disponível na folha da conta do Data Lake Store.
  
    ![Obter ID da assinatura](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* O nome de domínio do Azure AD. É possível recuperá-lo focalizando o canto superior direito do portal do Azure. Na captura de tela abaixo, o nome de domínio é **contoso.onmicrosoft.com** e o GUID entre colchetes é a ID de locatário. 
  
    ![Obter domínio do AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Sua ID de locatário do Azure. Para obter instruções sobre como recuperar a ID de locatário, consulte [Obter a ID de locatário](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="end-user-authentication"></a>Autenticação do usuário final
Esse mecanismo de autenticação é a abordagem recomendada caso você queira que um usuário final entre em seu aplicativo por meio do Azure AD. Seu aplicativo será capaz de acessar recursos do Azure com o mesmo nível de acesso do usuário final que fez logon. O usuário final precisará fornecer as respectivas credenciais periodicamente para que o aplicativo mantenha o acesso.

O objetivo do logon do usuário é para que seu aplicativo receba um token de acesso e um token de atualização. O token de acesso é anexado a cada solicitação feita ao Data Lake Store ou ao Data Lake Analytics e é válido por uma hora por padrão. O token de atualização pode ser usado para obter um novo token de acesso e é válido por até duas semanas, por padrão. Você pode usar duas abordagens diferentes para o logon do usuário final.

### <a name="using-the-oauth-20-pop-up"></a>Usando o pop-up OAuth 2.0
Seu aplicativo pode disparar um pop-up de autorização OAuth 2.0 no qual o usuário final pode inserir suas credenciais. Essa janela pop-up também funciona com o processo de autenticação do Azure AD de dois fatores (2FA), se necessário. 

> [!NOTE]
> Esse método ainda não tem suporte na ADAL (biblioteca de autenticação do Azure AD) para Python ou Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Transmitindo credenciais do usuário diretamente
Seu aplicativo pode fornecer credenciais de usuário ao Azure AD diretamente. Esse método só funciona com contas de usuário de IDs organizacionais. Ele não é compatível com contas de usuário "live ID"/pessoais, incluindo as terminadas em @outlook.com ou @live.com. Além disso, esse método não é compatível com contas de usuário que requerem autenticação do Azure AD de dois fatores (2FA).

### <a name="what-do-i-need-for-this-approach"></a>O que é necessário para essa abordagem?
* Nome de domínio do Azure AD. Esse requisito já está listado no pré-requisito deste artigo.
* ID de locatário do Azure AD. Esse requisito já está listado no pré-requisito deste artigo.
* **Aplicativo nativo** do Azure AD
* ID do aplicativo para o aplicativo nativo do Azure AD
* URI de Redirecionamento do aplicativo nativo do Azure AD
* Definir permissões delegadas


## <a name="step-1-create-an-active-directory-native-application"></a>Etapa 1: Criar um aplicativo nativo do Active Directory

Crie e configure um aplicativo nativo do Azure AD para autenticação do usuário final com o Azure Data Lake Store usando o Azure Active Directory. Para obter instruções, consulte [Criar um aplicativo do Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Ao seguir as instruções do link, verifique se você selecionou **Nativo** para tipo de aplicativo, conforme mostrado na seguinte captura de tela:

![Criar aplicativo Web](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Criar aplicativo nativo")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Etapa 2: Obter a ID do aplicativo e o URI de redirecionamento

Confira [Obter ID do aplicativo](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) para recuperar a ID do aplicativo (também chamada de ID do cliente no portal clássico do Azure) do aplicativo nativo do Azure AD.

Para recuperar o URI de redirecionamento, siga as etapas abaixo.

1. No portal do Azure, selecione **Azure Active Directory** e clique em **Registros de aplicativo**. Em seguida, encontre e clique no aplicativo nativo do Azure AD que você criou.

2. Na folha **Configurações** do aplicativo, clique em **URIs de Redirecionamento**.

    ![Obter URI de redirecionamento](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Copie o valor exibido.


## <a name="step-3-set-permissions"></a>Etapa 3: Definir permissões

1. No portal do Azure, selecione **Azure Active Directory** e clique em **Registros de aplicativo**. Em seguida, encontre e clique no aplicativo nativo do Azure AD que você criou.

2. Na folha **Configurações** do aplicativo, clique em **Permissões necessárias** e, em seguida, em **Adicionar**.

    ![ID do cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Na folha **Adicionar Acesso à API**, clique em **Selecionar uma API**, em **Azure Data Lake** e, em seguida, em **Selecionar**.

    ![ID do cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  Na folha **Adicionar Acesso à API**, clique em **Selecionar permissões**, marque a caixa de seleção para conceder **Acesso completo ao Data Lake Store** e, em seguida, clique em **Selecionar**.

    ![ID do cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Clique em **Concluído**.

5. Repita as duas últimas etapas para conceder permissões à **API de Gerenciamento de Serviços do Microsoft Azure** também.
   
## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um aplicativo nativo do Azure AD e reuniu as informações necessárias em seus aplicativos cliente que cria usando .NET SDK, SDK do Java, REST API etc. Agora você pode prosseguir para os artigos seguintes, que falam sobre como usar o aplicativo Web do Azure AD para primeiro se autenticar no Data Lake Store e, em seguida, executar outras operações no repositório.

* [Autenticação do usuário final com o Data Lake Store usando o SDK do Java](data-lake-store-end-user-authenticate-java-sdk.md)
* [Autenticação do usuário final com o Data Lake Store usando o SDK do .NET](data-lake-store-end-user-authenticate-net-sdk.md)
* [Autenticação do usuário final com o Data Lake Store usando o Python](data-lake-store-end-user-authenticate-python.md)
* [Autenticação do usuário final com o Data Lake Store usando a API REST](data-lake-store-end-user-authenticate-rest-api.md)

