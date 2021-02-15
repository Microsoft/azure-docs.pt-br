---
title: API de envio do Partner Center para aplicativos integrados do Azure no marketplace comercial da Microsoft
description: Conheça os pré-requisitos para usar a API de envio do Partner Center para aplicativos do Azure no marketplace comercial no Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9863ed24da9e427f885a4794bda7e103b0c1cc8e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455455"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>API de envio do Partner Center para aplicativos integrados do Azure no Partner Center

Use a *API de envio do Partner Center* para consultar programaticamente, criar envios para e publicar ofertas do Azure.  Essa API é útil se sua conta gerencia muitas ofertas e você quer automatizar e otimizar o processo de envio para essas ofertas.

## <a name="api-prerequisites"></a>Pré-requisitos de API

Há alguns ativos programáticos que são necessários para usar a API do Partner Center para produtos do Azure: 

- um aplicativo do Azure Active Directory.
- um token de acesso do Azure Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Etapa 1: Preenchimento dos pré-requisitos para usar a API de envio do Partner Center

Antes de começar a escrever o código para chamar a API de envio do Partner Center, verifique se você preenche os pré-requisitos a seguir.

- Você (ou sua organização) deve ter um diretório do Azure AD e você deve ter permissão de [Administrador global](../../active-directory/roles/permissions-reference.md) para o diretório. Se você já usa Microsoft 365 ou outros serviços corporativos da Microsoft, você já tem o diretório do Azure AD. Caso contrário, [crie um novo Azure AD no Partner Center](/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) sem custo adicional.

- [Associe um aplicativo do Azure AD à sua conta do Partner Center](/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) e obtenha a ID do locatário, a ID do cliente e a chave. Esses valores são necessários para obter um token de acesso do Azure AD, que será usado em chamadas para a API de envio da Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Como associar um aplicativo do Azure AD à sua conta do Partner Center

Para usar a API de envio da Microsoft Store, associe um aplicativo do Azure AD à sua conta do Partner Center, recupere a ID do locatário e a ID do cliente para o aplicativo e gere uma chave. O aplicativo do Azure AD representa o aplicativo ou serviço do qual você deseja chamar a API de envio do Partner Center. Você precisa da ID do locatário, da ID do cliente e da chave para obter um token de acesso do Azure AD que é passado para a API.

>[!Note]
>É necessário executar essa tarefa apenas uma vez. Depois de obter a ID do locatário, a ID do cliente e a chave, você poderá reutilizá-las sempre que precisar criar um novo token de acesso do Azure AD.

1. No Partner Center, [associe a conta do Partner Center da sua organização ao diretório do Azure AD da sua organização](/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. Em seguida, na página **Usuários** na seção **Configurações da conta** do Partner Center, [adicione o aplicativo do Azure AD](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) que representa o aplicativo ou serviço que você usará para acessar os envios para sua conta do Partner Center. Lembre-se de atribuir esse aplicativo à função de **Gerenciador**. Se o aplicativo ainda não existir no diretório do Azure AD, [crie um novo aplicativo do Azure AD no Partner Center](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Retorne à página **Usuários**, clique no nome do seu aplicativo do Azure AD para acessar as configurações do aplicativo e copie os valores de **ID do Locatário** e **ID do Cliente**.
1. Clique em **Adicionar nova chave**. Na tela a seguir, copie o valor da **Chave**. Você não poderá acessar essas informações novamente depois de sair da página. Para saber mais, veja [Gerenciar chaves de um aplicativo do Azure AD](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Etapa 2: Obtenção de um token de acesso do Azure AD

Antes de chamar qualquer um dos métodos na API de envio do Partner Center, primeiro obtenha um token de acesso do Azure AD que é passado para o cabeçalho de **Autorização** de cada método na API. Após obter um token de acesso, você tem 60 minutos para usá-lo antes dele expirar. Depois que o token expirar, atualize o token para que você possa continuar a usá-lo em chamadas futuras para a API.

Para obter o token de acesso, siga as instruções em [Chamadas de Serviço para Serviço Usando Credenciais do Cliente](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) para enviar um `HTTP POST` para o ponto de extremidade `https://login.microsoftonline.com/<tenant_id>/oauth2/token`. A seguir está uma solicitação de exemplo:

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Para o valor *tenant_id* em `POST URI` e nos parâmetros *client_id* e *client_secret*, especifique a ID do locatário, a ID do cliente e a chave para o aplicativo recuperado do Partner Center na seção anterior. Para o parâmetro *resource*, especifique `https://api.partner.microsoft.com`.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Etapa 3: Uso da API de envio da Microsoft Store

Depois de obter um token de acesso do Azure AD, você pode chamar métodos na API de envio do Partner Center. Para criar ou atualizar envios, normalmente chamam-se vários métodos na API de envio do Partner Center em uma ordem específica. Para informações sobre cada cenário e a sintaxe de cada método, veja o Swagger de API de Ingestão.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>Próximas etapas

* Saiba como [Criar um ativo técnico da VM do Azure](../create-azure-container-technical-assets.md)
* Saiba como criar uma [Oferta de contêiner do Azure](../create-azure-container-offer.md)