---
title: Adicionar autenticação para proteger chamadas para APIs personalizadas
description: Como configurar a autenticação para melhorar a segurança de chamadas para APIs personalizadas de aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 5e0dcd478c6eb6696a0e07d35d4dccddac68ac1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993169"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>Aumentar a segurança para chamadas para APIs personalizadas de aplicativos lógicos do Azure

Para melhorar a segurança para chamadas para suas APIs, você pode configurar a autenticação do Azure Active Directory (AD do Azure) por meio do portal do Azure para que não precise atualizar seu código. Ou você pode exigir e aplicar a autenticação por meio do seu código da API.

## <a name="authentication-options-for-your-api"></a>Opções de autenticação para sua API

Você pode melhorar a segurança para chamadas para sua API personalizada das seguintes maneiras:

* [Sem alterações de código](#no-code): proteja sua API com [Azure AD (Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md) por meio do Portal do Azure, para que você não precise atualizar o código ou reimplantar sua API.

  > [!NOTE]
  > Por padrão, a autenticação do Azure AD que você ativa no Portal do Azure não fornece autorizações refinadas. Por exemplo, essa autenticação bloqueia sua API para apenas um locatário específico, não para um usuário ou aplicativo específico. 

* [Atualizar o código da API](#update-code): proteja sua API impondo a [autenticação de certificado](#certificate), a [autenticação básica](#basic) ou a [autenticação do Azure AD](#azure-ad-code) por meio de código.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Autenticar chamadas para a API sem alterar o código

Eis as etapas gerais para este método:

1. Crie duas identidades de aplicativo do Azure AD (Azure Active Directory): uma para seu aplicativo lógico e um para seu aplicativo Web (ou aplicativo de API).

2. Para autenticar chamadas à API, use as credenciais (ID do cliente e segredo) para a entidade de serviço associada à identidade do aplicativo do Azure AD para seu aplicativo lógico.

3. Inclua as IDs de aplicativo na definição do aplicativo lógico.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Parte 1: Criar uma identidade do aplicativo do Azure AD para seu aplicativo lógico

Seu aplicativo lógico usa essa identidade do aplicativo do Azure AD para autenticar no Azure AD. Você só precisa configurar essa identidade uma vez para seu diretório. Por exemplo, você pode optar por usar a mesma identidade para todos os seus aplicativos lógicos, embora possa criar identidades exclusivas para cada aplicativo lógico. É possível configurar essas identidades no Portal do Azure ou usar o [PowerShell](#powershell).

**Criar a identidade do aplicativo para seu aplicativo lógico no Portal do Azure**

1. No [portal do Azure](https://portal.azure.com "https://portal.azure.com"), escolha **Azure Active Directory**. 

2. Confirme que você está no mesmo diretório que o aplicativo Web ou aplicativo de API.

   > [!TIP]
   > Para mudar de diretório, selecione seu perfil e selecione outro diretório. Ou escolha **visão geral**  >  **alternar diretório**.

3. No menu de diretório, em **Gerenciar**, escolha **Registros do aplicativo** > **Novo registro do aplicativo**.

   > [!TIP]
   > Por padrão, a lista de registros do aplicativo mostra todos os registros do aplicativo em seu diretório. Para exibir apenas seus registros do aplicativo, ao lado da caixa de pesquisa, selecione **Meus aplicativos**. 

   ![Criar novo registro de aplicativo](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Dê um nome à identidade do aplicativo, deixe **Tipo do aplicativo** definido como **Aplicativo Web/API**, forneça uma cadeia de caracteres formatada única como um domínio para **URL de Entrada** e escolha **Criar**.

   ![Forneça o nome e a URL de entrada para a identidade do aplicativo](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   A identidade do aplicativo que você criou para seu aplicativo lógico agora aparece na lista de registros de aplicativo.

   ![Identidade do aplicativo para seu aplicativo lógico](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Na lista de registros de aplicativo, selecione sua nova identidade do aplicativo. Copie e salve a **ID do Aplicativo** para usar como a “ID do cliente” para o aplicativo lógico na Parte 3.

   ![Copie e salve a ID do aplicativo para o aplicativo lógico](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Se as configurações de identidade do aplicativo não estiverem visíveis, escolha **Configurações** ou **Todas as configurações**.

7. Em **Acesso à API**, escolha **Chaves**. Em **Descrição**, forneça um nome para sua chave. Em **Expira**, selecione uma duração para a chave.

   A chave que você está criando atua como o “segredo” ou senha da identidade do aplicativo para seu aplicativo lógico.

   ![Criar chave para identidade de aplicativo lógico](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Na barra de ferramentas, escolha **Salvar**. Em **Valor**, agora sua chave aparece. 
**Certifique-se de copiar e salvar sua chave** para uso posterior porque a chave é ocultada quando você sai da página de **Chaves**.

   Quando você configurar seu aplicativo lógico na Parte 3, poderá especificar esta chave como o "segredo" ou a senha.

   ![Copiar e salvar a chave para mais tarde](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Criar a identidade do aplicativo para seu aplicativo lógico no PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode executar essa tarefa por meio do Azure Resource Manager com o PowerShell. No PowerShell, execute estes comandos:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Insira uma senha e pressione Enter.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. Certifique-se de copiar a **ID do Locatário** (GUID para o locatário do Azure AD), a **ID do Aplicativo** e a senha usada.

Para obter mais informações, saiba como [criar uma entidade de serviço com o PowerShell para acessar os recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Parte 2: Criar uma identidade do aplicativo do Azure AD para seu aplicativo Web ou aplicativo de API

Se seu aplicativo Web ou aplicativo de API já estiver implantado, você poderá ativar a autenticação e criar a identidade do aplicativo no Portal do Azure. Caso contrário, você pode [ativar a autenticação quando implantar com um modelo do Azure Resource Manager](#authen-deploy). 

**Criar a identidade do aplicativo e ativar a autenticação no Portal do Azure para aplicativos implantados**

1. No [portal do Azure](https://portal.azure.com "https://portal.azure.com"), localize e selecione o aplicativo Web ou aplicativo de API. 

2. Em **Configurações**, escolha **Autenticação/Autorização**. Em **Autenticação do Serviço de Aplicativo**, **Ative** a autenticação. Em **Provedores de Autenticação**, escolha **Azure Active Directory**.

   ![Ativar a autenticação](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Agora crie uma identidade do aplicativo para seu aplicativo Web ou aplicativo de API conforme mostrado aqui. Na página **Configurações do Azure Active Directory**, defina **Modo de gerenciamento** como **Expresso**. Escolha **Criar novo aplicativo do AD**. Dê um nome à identidade do aplicativo e escolha **OK**. 

   ![Criar identidade do aplicativo para seu aplicativo Web ou aplicativo de API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Na página **Autenticação/Autorização**, escolha **Salvar**.

Agora você deve encontrar a ID do cliente e a ID do locatário para a identidade do aplicativo associada ao aplicativo Web ou aplicativo de API. Você usará essas IDs na Parte 3. Portanto, continue com essas etapas para o Portal do Azure.

**Localizar a ID do cliente e a ID do locatário da identidade do aplicativo do aplicativo Web ou aplicativo de API no Portal do Azure**

1. Em **Provedores de Autenticação**, escolha **Azure Active Directory**. 

   ![Escolha "Azure Active Directory"](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Na página **Configurações do Azure Active Directory**, defina **Modo de gerenciamento** como **Avançado**.

3. Copie a **ID do Cliente** e salve o GUID para uso na Parte 3.

   > [!TIP] 
   > Se a **ID do Cliente** e a **URL do Emissor** não aparecerem, tente atualizar o Portal do Azure e repita a Etapa 1.

4. Em **URL do Emissor**, copie e salve apenas o GUID para a Parte 3. Você também pode usar esse GUID no modelo de implantação do aplicativo Web ou aplicativo de API, se necessário.

   Esse GUID é GUID específico do locatário ("ID de locatário") e deve aparecer nessa URL: `https://sts.windows.net/{GUID}`

5. Sem salvar as alterações, feche a página **Configurações do Azure Active Directory**.

<a name="authen-deploy"></a>

**Ativar a autenticação quando implantar com um modelo do Azure Resource Manager**

Você ainda precisa criar uma identidade de aplicativo do Azure AD para seu aplicativo Web ou aplicativo de API que difere da identidade do aplicativo para seu aplicativo lógico. Para criar a identidade do aplicativo, siga as etapas anteriores na Parte 2 para o Portal do Azure. 

Você também pode seguir as etapas na Parte 1, mas certifique-se de usar o `https://{URL}` real do aplicativo Web ou aplicativo de API para **URL de Entrada** e **URI da ID do Aplicativo**. Nessas etapas, você precisa salvar a ID do cliente e a ID do locatário para uso no modelo de implantação do aplicativo e na Parte 3.

> [!NOTE]
> Quando você cria a identidade do aplicativo do Azure AD para seu aplicativo Web ou aplicativo de API, é necessário usar o Portal do Azure, não o PowerShell. O cmdlet do PowerShell não configura as permissões necessárias para conectar os usuários em um site.

Depois que você obtiver a ID do cliente e a ID do locatário, inclua essas IDs como um recurso secundário do seu aplicativo Web ou aplicativo de API no seu modelo de implantação:

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Para implantar automaticamente um aplicativo Web em branco e um aplicativo lógico com a autenticação do Azure Active Directory, [exiba o modelo completo aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json) ou clique em **Implantar no Azure** aqui:

[![Implantar no Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Parte 3: Preencher a seção Autorização no aplicativo lógico

O modelo anterior já tem essa seção de autorização definida, mas se você estiver criando o aplicativo lógico diretamente, precisará incluir a seção de autorização completa.

Abra a definição do aplicativo lógico na exibição de código, vá para a definição da ação **http** , localize a seção **autorização** e inclua estas propriedades:

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| Propriedade | Obrigatório | Descrição | 
| -------- | -------- | ----------- | 
| locatário | Sim | O GUID para o locatário do Azure AD | 
| audiência | Sim | O GUID do recurso de destino que você deseja acessar, que é a ID do cliente da identidade de aplicativo para seu aplicativo Web ou aplicativo de API | 
| clientId | Sim | O GUID do cliente que solicita o acesso, que é a ID do cliente da identidade do aplicativo para seu aplicativo lógico | 
| segredo | Sim | A chave ou a senha da identidade do aplicativo para o cliente que está solicitando o token de acesso | 
| type | Sim | O tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`. | 
|||| 

Por exemplo:

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Proteger chamadas à API por meio de código

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Autenticação de certificado

Para validar as solicitações recebidas do aplicativo lógico para o aplicativo Web ou aplicativo de API, você pode usar certificados do cliente. Para configurar seu código, [aprenda a configurar a autenticação mútua TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

Na seção **autorização** , inclua estas propriedades:

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Propriedade | Obrigatório | Descrição |
| -------- | -------- | ----------- |
| `type` | Sim | O tipo de autenticação. Para certificados de cliente TLS/SSL, o valor deve ser `ClientCertificate` . |
| `password` | Não | A senha para acessar o certificado do cliente (arquivo PFX) |
| `pfx` | Sim | O conteúdo codificado por base64 do certificado do cliente (arquivo PFX) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Autenticação básica

Para validar solicitações de entrada de seu aplicativo lógico para o aplicativo Web ou aplicativo de API, você pode usar a autenticação básica, como um nome de usuário e senha. A autenticação básica é um padrão comum e você pode usar essa autenticação em qualquer linguagem usada para compilar seu aplicativo Web ou aplicativo de API.

Na seção **autorização** , inclua estas propriedades:

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Propriedade | Obrigatório | Descrição | 
| -------- | -------- | ----------- | 
| type | Sim | O tipo de autenticação que você deseja usar. Para a autenticação básica, o valor deve ser `Basic`. | 
| Nome de Usuário | Sim | O nome de usuário que você deseja usar para autenticação | 
| password | Sim | A senha que você deseja usar para autenticação | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Autenticação do Azure Active Directory pelo código

Por padrão, a autenticação do Azure AD que você ativa no Portal do Azure não fornece autorizações refinadas. Por exemplo, essa autenticação bloqueia sua API para apenas um locatário específico, não para um usuário ou aplicativo específico. 

Para restringir o acesso à API para seu aplicativo lógico pelo código, extraia o cabeçalho que tem o JWT (Token Web JSON). Verifique a identidade do chamador e rejeite as solicitações que não correspondem.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Próximas etapas

* [Implantar e chamar APIs personalizadas de fluxos de trabalho do aplicativo lógico](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
