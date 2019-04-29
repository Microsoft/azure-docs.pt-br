---
title: Introdução à autenticação do Azure AD utilizando o Portal do Azure | Microsoft Docs
description: Saiba como utilizar o portal do Azure para acessar a autenticação do Azure Active Directory (Azure AD) para consumir a API dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 37c5a97d5f7a872e916f223f2cdfc98bcdec3cad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61130654"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Introdução à autenticação do Azure AD utilizando o Portal do Azure

Saiba como utilizar o portal do Azure para acessar a autenticação do Azure Active Directory (Azure AD) para acessar a API dos Serviços de Mídia do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você não tiver uma conta, comece com uma [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Serviços de Mídia. Para obter mais informações, consulte [Criar uma conta dos Serviços de Mídia do Azure usando o portal do Azure](media-services-portal-create-account.md).
- Certifique-se de revisar [Acessar a API dos Serviços de Mídia do Azure com a visão geral de autenticação do AD Azure](media-services-use-aad-auth-to-access-ams-api.md). 

Quando você usar a autenticação do AD Azure com Serviços de Mídia do Azure haverá duas opções de autenticação:

- **Autenticação de usuário**. Autentica uma pessoa que está usando o aplicativo para interagir com os recursos dos Serviços de Mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou uma transmissão ao vivo. 
- **Autenticação de entidade de serviço**. Autentica um serviço. Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços de daemon, serviços de camada intermediária ou trabalhos agendados: aplicativos Web, aplicativos de funções, aplicativos lógicos, APIs ou um microsserviço.

> [!IMPORTANT]
> Atualmente, os Serviços de Mídia oferecem suporte ao modelo de autenticação do serviço de Controle de Acesso do Azure. No entanto, a autorização de Controle de Acesso será preterida em 1º de junho de 2018. É recomendável que você migre para o modelo de autenticação do Azure AD assim que possível.

## <a name="select-the-authentication-method"></a>Selecione o método de autenticação

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia.
2. Selecione como se conectar à API dos Serviços de Mídia.

    ![Selecione a página do método de conexão](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Autenticação de usuário

Para se conectar à API dos Serviços de Mídia utilizando a opção de autenticação do usuário, o aplicativo cliente precisa solicitar um token do AD Azure que tenha os seguintes parâmetros:  

* Ponto de extremidade de locatário do Azure AD
* URI de recursos dos Serviços de Mídia
* ID do cliente do aplicativo (nativo) dos Serviços de Mídia 
* URI de redirecionamento do aplicativo (nativo) dos Serviços de Mídia 
* URI de recurso dos Serviços de Mídia REST

É possível obter os valores para esses parâmetros na página**API de Serviços de Mídia com autenticação de usuário**. 

![Conecte-se com a página de autenticação de usuário](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Ao se conectar à API dos Serviços de Mídia utilizando o SDK do Microsoft .NET, os valores necessários estarão disponíveis como parte do SDK. Para obter mais informações, consulte [Usar a autenticação do Azure AD para acessar a API dos Serviços de Mídia do Azure com .NET](media-services-dotnet-get-started-with-aad.md).

Se você não estiver utilizando o SDK do cliente .NET dos Serviços de Mídia será necessário criar manualmente uma solicitação de token do AD Azure usando os parâmetros discutidos anteriormente. Para obter mais informações, consulte [Como usar a Biblioteca de Autenticação do Azure AD para obter o token do Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

Para conectar-se à API dos Serviços de Mídia utilizando a opção entidade de serviço, o aplicativo de camada intermediária (API Web ou aplicativo Web) é necessário solicitar um token do Azure AD que tenha os seguintes parâmetros:  

* Ponto de extremidade de locatário do Azure AD
* URI de recursos dos Serviços de Mídia 
* URI de recurso dos Serviços de Mídia REST
* Valores do aplicativo Azure AD: o **ID do cliente** e **segredo do cliente**

Você pode obter os valores desses parâmetros na página **Conectar-se à API dos Serviços de Mídia com a entidade de serviço**. Use esta página para criar um novo aplicativo Azure AD ou para selecionar um existente. Após selecionar o aplicativo Azure AD, você poderá obter o ID do cliente (ID do aplicativo) e gerar os valores do segredo do cliente (chave). 

![Conecte-se com a página da entidade de serviço](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Quando a folha **Entidade de Serviço** abrir, o primeiro aplicativo do AD Azure que atender aos seguintes critérios será selecionado:

- Trata-se de um aplicativo do AD Azure registrado.
- Ele possui permissões de Controle de Acesso Baseado em Função de Proprietário ou Colaboradora na Conta.

Após criar ou selecionar um aplicativo Azure AD, você poderá criar e copiar um segredo do cliente (chave) e o ID do cliente (ID do aplicativo). Nesse cenário, o segredo do cliente e o ID do cliente são necessários para obter o token de acesso.

Se você não possui permissões para criar aplicativos Azure AD no seu domínio, os controles de aplicativo Azure AD da folha não serão mostrados e uma mensagem de aviso será exibida.

Se você se conectar à API dos Serviços de Mídia utilizando o SDK .NET dos Serviços de Mídia, consulte [Usar a autenticação do Azure AD para acessar a API dos Serviços de Mídia do Azure com .NET](media-services-dotnet-get-started-with-aad.md).

Se você não estiver utilizando o SDK do cliente .NET dos Serviços de Mídia será necessário criar manualmente uma solicitação de token do AD Azure usando os parâmetros discutidos anteriormente. Para obter mais informações, consulte [Como usar a Biblioteca de Autenticação do Azure AD para obter o token do Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Obtenha o ID do cliente e o segredo do cliente

Após selecionar um aplicativo Azure AD existente ou selecionar a opção para criar um novo, os seguintes botões serão exibidos:

![Botão de gerenciar permissões e botão de gerenciar aplicativo](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Para abrir a folha do aplicativo Azure AD, clique em **Gerenciar aplicativo**. Na folha **Gerenciar aplicativo** você pode obter o ID do cliente do aplicativo (ID do aplicativo). Para gerar um segredo do cliente (chave), selecione **Chaves**.

![Gerenciar a opção Chaves da folha do aplicativo](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Gerenciar permissões e o aplicativo

Após selecionar o aplicativo Azure AD, você poderá gerenciar o aplicativo e as permissões. Para configurar o aplicativo Azure AD para acessar outros aplicativos, clique em **Gerenciar permissões**. Para tarefas de gerenciamento, como alterar chaves e URLs de resposta, ou para editar o manifesto do aplicativo, clique em **Gerenciar aplicativo**.

### <a name="edit-the-apps-settings-or-manifest"></a>Editar as configurações de aplicativo ou manifesto

Para editar as configurações de aplicativo ou manifesto, clique em **Gerenciar aplicativo**.

![Gerenciar a página do aplicativo](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Próximas etapas

Comece a [carregar arquivos para sua conta](media-services-portal-upload-files.md).
