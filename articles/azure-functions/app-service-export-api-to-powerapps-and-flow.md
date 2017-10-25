---
title: Exportando uma API hospedada no Azure para o PowerApps e o Microsoft Flow | Microsoft Docs
description: "Visão geral de como expor uma API hospedada no serviço de aplicativo ao PowerApps e ao Microsoft Flow"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/06/2017
ms.author: mahender; mblythe
ms.openlocfilehash: efa5a50564d94dbecd4bc7fcb4082b01d16f680d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportando uma API hospedada no Azure para o PowerApps e o Microsoft Flow

O [PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) é um serviço para a criação e utilização de aplicativos comerciais personalizados que se conectam aos seus dados e trabalham entre plataformas. O [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) facilita a automação de fluxos de trabalho e processos de negócios entre seus aplicativos e serviços favoritos. O PowerApps e o Microsoft Flow vêm com uma variedade de conectores integrados para fontes de dados como Office 365, Dynamics 365, Salesforce e muito mais. Em alguns casos, construtores de aplicativo e fluxo também querem se conectar a fontes de dados e APIs criados por sua organização.

Da mesma forma, os desenvolvedores que desejam expor suas APIs mais amplamente dentro da organização podem querer disponibilizá-las para construtores de aplicativos e fluxos. Este tópico mostra como exportar uma API criada com o [Azure Functions](../azure-functions/functions-overview.md) ou o [Serviço de Aplicativo do Azure](../app-service/app-service-web-overview.md). A API exportada torna-se um *conector personalizado*, usado no PowerApps e no Microsoft Flow, assim como um conector interno.

## <a name="create-and-export-an-api-definition"></a>Criar e exportar uma definição de API
Antes de exportar uma API, é necessário descrevê-la usando uma definição OpenAPI (anteriormente conhecida como um arquivo [Swagger](http://swagger.io/)). Esta definição contém informações sobre as operações que estão disponíveis em uma API e como os dados de solicitação e resposta para a API devem ser estruturados. O PowerApps e o Microsoft Flow podem criar conectores personalizados para qualquer definição OpenAPI 2.0. O Serviço de Aplicativo do Azure e o Azure Functions têm suporte integrado para criação, hospedagem e gerenciamento de definições OpenAPI. Para obter mais informações, consulte [Criar uma API RESTful em aplicativos Web do Azure](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Também é possível criar conectores personalizados no PowerApps e no Microsoft Flow UI, sem o uso de uma definição OpenAPI. Para obter mais informações, consulte [Registrar e usar conectores personalizados no PowerApps](https://powerapps.microsoft.com/tutorials/register-custom-api/) e [Registrar e usar conectores personalizados no Microsoft Flow](https://flow.microsoft.com/documentation/register-custom-api/).

Para exportar a definição de API, siga estas etapas:

1. No [Portal do Azure](https://portal.azure.com), navegue até o aplicativo Azure Functions ou de Serviço de Aplicativo.

    Se utilizar o Azure Functions, selecione seu aplicativo de funções, escolha **Recursos de plataforma** e, em seguida, **Definição de API**.

    ![Definição da API do Azure Functions](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Se usar o Serviço de Aplicativo do Azure, selecione **definição de API** da lista de configurações.

    ![Definição da API do Serviço de Aplicativo](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. O botão **Exportar para o PowerApps e para o Microsoft Flow** deve estar disponível (se não estiver, crie uma definição de OpenAPI primeiro). Clique neste botão para iniciar o processo de exportação.

    ![Botão Exportar para o PowerApps e para o Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Selecione o **Modo de exportação**:

    **Expresso** permite que você crie o conector personalizado a partir do portal do Azure. É necessário estar conectado ao PowerApps ou ao Microsoft Flow e ter permissão para criar conectores no ambiente de destino. Essa é a abordagem recomendada se esse requisito puder ser atendido. Se estiver utilizando esse modo, siga as instruções de [Exportação de expresso](#express) abaixo.

    **Manual** permite exportar a definição de API, que depois pode ser importada utilizando os portais do PowerApps e Microsoft Flow. Essa é a abordagem recomendada se o usuário do Azure e o usuário com permissão para criar conectores forem pessoas diferentes ou se o conector precisar ser criado em outro locatário. Se estiver utilizando esse modo, siga as instruções de [Exportação manual](#manual) abaixo.

    ![Modo de exportação](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> O conector personalizado usa uma *cópia* da definição de API, assim o PowerApps e o Microsoft Flow não saberão imediatamente se o aplicativo e sua definição de API foram alterados. Se você fizer alterações, repita as etapas de exportação na nova versão.

<a name="express"></a>
## <a name="use-express-export"></a>Use a exportação de expresso

Para concluir a exportação no modo **Expresso**, siga estas etapas:

1. Verifique se você está conectado ao locatário do PowerApps ou Microsoft Flow para o qual você deseja exportar. 

2. Use as configurações conforme especificado na tabela.

    |Configuração|Descrição|
    |--------|------------|
    |**Ambiente**|Selecione o ambiente no qual o conector personalizado deve ser salvo. Para obter mais informações, consulte [Visão geral dos ambientes](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Nome da API personalizada**|Insira um nome, que será exibido para os construtores do PowerApps e do Microsoft Flow na lista de conectores.|
    |**Preparar a configuração de segurança**|Se necessário, forneça os detalhes de configuração de segurança necessários para conceder aos usuários acesso à sua API. Este exemplo mostra uma chave de API. Para obter mais informações, consulte [Especificar tipo de autenticação](#auth) abaixo.|
 
    ![Exportação de expresso para o PowerApps e para o Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Clique em **OK**. Agora, o conector personalizado é compilado e adicionado ao ambiente especificado.

Para exemplos de uso do modo **Expresso** com o Azure Functions, consulte [Chamar uma função do PowerApps](functions-powerapps-scenario.md) e [Chamar uma função do Microsoft Flow](functions-flow-scenario.md).

<a name="manual"></a>
## <a name="use-manual-export"></a>Usar a exportação manual

Para concluir a exportação no modo **Manual**, siga estas etapas:

1. Clique em **Baixar** e salve o arquivo ou clique no botão Copiar e salve a URL. O arquivo baixado ou a URL serão usados durante a importação.
 
    ![Exportação manual para o PowerApps e para o Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Se a definição de API incluir quaisquer definições de segurança, elas serão chamadas na etapa n° 2. Durante a importação, o PowerApps e o Microsoft Flow as detectam e solicitam informações de segurança. Reúna as credenciais relacionadas a cada definição para uso na próxima seção. Para obter mais informações, consulte [Especificar tipo de autenticação](#auth) abaixo.

    ![Segurança para exportação manual](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Este exemplo mostra a definição de segurança da chave de API incluída na definição de OpenAPI.

Agora que você exportou a definição de API, importe-a para criar um conector personalizado no PowerApps e no Microsoft Flow. O exemplo a seguir usa o PowerApps, mas os conectores personalizados são compartilhados entre os dois serviços, portanto, é necessário importar a definição apenas uma vez.

Para importar a definição de API para o PowerApps e o Microsoft Flow, siga estas etapas:

1. Entrar no [web.powerapps.com](https://web.powerapps.com) ou [flow.microsoft.com](https://flow.microsoft.com/). 

2. Clique no botão **Configurações** (o ícone de engrenagem) no canto superior direito da página e selecione **Conectores personalizados**.

    ![Conectores personalizados](media/app-service-export-api-to-powerapps-and-flow/custom-connectors.png)

3. Clique em **Criar conector personalizado**.

4. Na guia **Geral**, forneça um nome para a API e carregue a definição de OpenAPI ou cole a URL de metadados. Clique em **Upload** e, em seguida, em **Continuar**.

    ![Guia Geral](media/app-service-export-api-to-powerapps-and-flow/tab-general.png)

5. Na guia **Segurança**, se for solicitado que você forneça detalhes de autenticação, insira os valores corretos para o tipo de autenticação. Clique em **Continuar**.

    ![Guia Segurança](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Este exemplo mostra os campos obrigatórios para a autenticação da chave de API. Os campos são diferentes dependendo do tipo de autenticação.

6. Na guia **Definições**, todas as operações definidas no arquivo OpenAPI são populadas automaticamente. Se todas as suas operações necessárias forem definidas, você poderá ir para a próxima etapa. Caso contrário, você pode adicionar e modificar as operações aqui.

    ![Guia Definições](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Este exemplo tem uma operação, denominada `CalculateCosts`. Os metadados, como **Descrição**, vêm do arquivo OpenAPI.

7. Clique em **Criar conector** na parte superior da página.

Agora, é possível se conectar ao conector personalizado no PowerApps e no Microsoft Flow. Para obter mais informações sobre a criação de conectores nos portais do PowerApps e do Microsoft Flow, consulte [Registrar o conector personalizado no PowerApps](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) e [Registrar o conector personalizado no Microsoft Flow](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Especificar tipo de autenticação

O PowerApps e o Microsoft Flow oferecem suporte a uma coleção de provedores de identidade que fornecem autenticação para conectores personalizados. Se sua API exige autenticação, certifique-se de que ela seja capturada como uma _definição de segurança_ no documento OpenAPI, como neste exemplo:

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
Durante a exportação, será necessário fornecer valores de configuração que permitem ao PowerApps e ao Microsoft Flow autenticar usuários.

Esta seção aborda os tipos de autenticação com suporte no modo **Expresso**: chave de API, Azure Active Directory e OAuth 2.0 Genérico. O PowerApps e o Microsoft Flow também oferecem suporte à Autenticação Básica e ao OAuth 2.0 para serviços específicos, como SalesForce, Facebook e Dropbox.

### <a name="api-key"></a>Chave de API
Ao utilizar uma chave de API, os usuários do conector deverão fornecer a chave ao criar uma conexão. Você especifica um nome de chave de API para ajudá-los a reconhecer qual chave é necessária. No exemplo anterior, usamos o nome `API Key (contact meganb@contoso.com)` para que as pessoas saibam onde obter informações sobre a chave de API. No Azure Functions, a chave geralmente será uma das chaves de host que abrangem várias funções dentro do aplicativo de funções.

### <a name="azure-active-directory-azure-ad"></a>Active Directory do Azure (Azure AD)
Ao usar o Azure AD, são necessários dois registros de aplicativo do Azure AD: um para a própria API e outro para o conector personalizado:

- Para configurar o registro da API, use o recurso de [Autenticação/Autorização do Serviço de Aplicativo](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md).

- Para configurar o registro do conector, siga as etapas em [Como adicionar um aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). O registro deve ter acesso delegado à sua API e um URL de resposta de `https://msmanaged-na.consent.azure-apim.net/redirect`. 

Para obter mais informações, consulte os exemplos de registro do Azure AD para [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) e [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/). Esses exemplos usam o Azure Resource Manager como a API. Se você seguir as etapas, substitua sua API.

Os valores de configuração a seguir são necessários:
- **ID do cliente** – a ID do cliente do seu registro do Azure AD do conector
- **Segredo do cliente** – o segredo do cliente do seu registro do Azure AD do conector
- **URL de logon** – URL básica para o Azure AD. No Azure, isso é normalmente `https://login.windows.net`.
- **ID do locatário** - o ID do locatário a ser usado para o logon. Isso deve ser "comum" ou a ID do locatário em que o conector é criado.
- **URL de recurso** – URL de recurso do seu registro do Azure AD da sua API

> [!IMPORTANT]
> Caso outro indivíduo importe a definição de API para o PowerApps e o Microsoft Flow como parte do fluxo manual, será necessário fornecer a ID do cliente e o segredo do cliente do *registro do conector*, assim como o URL de recurso da sua API. Certifique-se de que esses segredos sejam gerenciados com segurança. **Não compartilhe as credenciais de segurança da API, propriamente dita.**

### <a name="generic-oauth-20"></a>OAuth 2.0 genérico
Ao usar o OAuth 2.0 genérico, é possível integrar com qualquer provedor de OAuth 2.0. Isso permite que você trabalhe com provedores personalizados sem suporte nativo.

Os valores de configuração a seguir são necessários:
- **ID do Cliente** - o ID do cliente do OAuth 2.0
- **Segredo do Cliente** - o segredo do cliente do OAuth 2.0
- **URL de Autorização** - a URL de autorização do OAuth 2.0
- **URL de Token** - a URL de token do OAuth 2.0
- **URL de Atualização** - a URL de atualização do OAuth 2.0


