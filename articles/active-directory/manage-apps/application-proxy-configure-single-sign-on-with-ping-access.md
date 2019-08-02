---
title: Autenticação baseada em cabeçalho com o PingAccess para Proxy de Aplicativo do Azure AD | Microsoft Docs
description: Publique aplicativos com o PingAccess e o Proxy de Aplicativo a fim de oferecer suporte à autenticação baseada em cabeçalho.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a99d001f8cb59af3042ce8b6849a2cd9480e99
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724003"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Autenticação baseada em cabeçalho para logon único com Proxy de Aplicativo e PingAccess

Proxy de aplicativo do Azure Active Directory (Azure AD) entrou em parceria com o PingAccess para que os clientes do AD do Azure possam acessar mais de seus aplicativos. O PingAccess expande as [ofertas existentes do Proxy de Aplicativo](application-proxy.md) para incluir acesso de logon único a aplicativos que usam cabeçalhos para autenticação.

## <a name="whats-pingaccess-for-azure-ad"></a>O que é PingAccess para Azure AD?

Com o PingAccess para Azure AD, você pode dar acesso de usuários e logon único (SSO) para aplicativos que usam cabeçalhos para autenticação. O Proxy de Aplicativo trata esses aplicativos como qualquer outro, usando o Azure AD para autenticar o acesso e, depois, passando o tráfego por meio do serviço de conector. O PingAccess fica na frente dos aplicativos e converte o token de acesso do AD do Azure em um cabeçalho. O aplicativo, em seguida, recebe a autenticação no formato em que ele pode ser lido.

Os usuários não perceberão algo diferente quando entrarem para usar seus aplicativos corporativos. Eles ainda podem trabalhar de qualquer lugar e em qualquer dispositivo. Os conectores de Proxy de aplicativo direcionam o tráfego remoto a todos os aplicativos sem considerar o tipo de autenticação, portanto, eles ainda serão balancear as cargas automaticamente.

## <a name="how-do-i-get-access"></a>Como posso obter acesso?

Uma vez que esse cenário provenientes de uma parceria entre o Azure Active Directory e o PingAccess, você precisa de licenças para ambos os serviços. No entanto, as assinaturas do Azure Active Directory Premium incluem uma licença básica PingAccess que abrange até 20 aplicativos. Se você precisar publicar mais de 20 aplicativos com base em cabeçalho, poderá adquirir uma licença adicional do PingAccess.

Para obter mais informações, consulte [Edições do Active Directory do Azure](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publicar seu aplicativo no Azure

Este artigo é para as pessoas publicar um aplicativo com esse cenário pela primeira vez. Além de detalhar as etapas de publicação, ele orienta você no guia de Introdução com o Proxy de aplicativo e PingAccess. Se você já tiver configurado os dois serviços, mas quer relembrar as etapas de publicação, vá para o [adicionar seu aplicativo para o Azure AD com o Proxy de aplicativo](#add-your-application-to-azure-ad-with-application-proxy) seção.

> [!NOTE]
> Como esse cenário é uma parceria entre o Azure AD e o PingAccess, algumas das instruções estão no site do Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Instalar um conector do Proxy de Aplicativo

Se você tiver habilitado o Proxy de aplicativo habilitada e já instalado um conector, você pode ignorar esta seção e vá para [adicionar seu aplicativo para o Azure AD com o Proxy de aplicativo](#add-your-application-to-azure-ad-with-application-proxy).

O conector de Proxy de aplicativo é um serviço do Windows Server que direciona o tráfego de seus funcionários remotos para seus aplicativos publicados. Para obter instruções de instalação mais detalhadas, consulte [Tutorial: Adicionar um aplicativo local para acesso remoto por meio do Proxy de aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Entrar para o [portal do Azure Active Directory](https://aad.portal.azure.com/) como um administrador do aplicativo. O **Centro de administração do Azure Active Directory** página será exibida.
1. Selecione **Azure Active Directory** > **proxy de aplicativo** > **baixar conector de serviço**. O **Download do conector do Proxy de aplicativo** página será exibida.

   ![Download do conector de proxy de aplicativo](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Siga as instruções de instalação.

Baixar o conector deve habilitar automaticamente o Proxy de aplicativo para seu diretório, mas se não estiver, você pode selecionar **habilitar o Proxy de aplicativo**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Adicionar seu aplicativo para o Azure AD com o Proxy de aplicativo

Há duas ações necessárias no portal do Azure. Primeiro, você precisa publicar seu aplicativo com o Proxy de Aplicativo. Em seguida, você precisa coletar algumas informações sobre o aplicativo que você pode usar durante as etapas do PingAccess.

#### <a name="publish-your-application"></a>Publicar seu aplicativo

Primeiro, você terá de publicar seu aplicativo. Essa ação envolve:

- Adicionar seu aplicativo local para o Azure AD
- Atribuir um usuário para testar o aplicativo e escolhendo o SSO baseado em cabeçalho
- Configurar a URL de redirecionamento do aplicativo
- Concedendo permissões para usuários e outros aplicativos usem seu aplicativo no local

Para publicar seu próprio aplicativo no local:

1. Se você não fez isso na última seção, entrar para o [portal do Azure Active Directory](https://aad.portal.azure.com/) como um administrador do aplicativo.
1. Selecione **aplicativos empresariais** > **novo aplicativo** > **aplicativo local**. O **adicionar seu próprio aplicativo local** página será exibida.

   ![Adicionar seu próprio aplicativo local](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Preencha os campos obrigatórios com informações sobre seu novo aplicativo. Use as diretrizes abaixo para as configurações.

   > [!NOTE]
   > Para obter uma explicação mais detalhada dessa etapa, consulte [adicionar um aplicativo local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **URL interna**: Normalmente, você fornece a URL que levará você à página de entrada do aplicativo, quando você estiver na rede corporativa. Para este cenário, o conector precisa tratar o proxy do PingAccess como a página inicial do aplicativo. Use este formato: `https://<host name of your PingAccess server>:<port>`. A porta é a 3000 por padrão, mas você pode configurá-la no PingAccess.

      > [!WARNING]
      > Para esse tipo de logon único, a URL interna deve usar `https` e não é possível usar `http`.

   1. **Método de pré-autenticação**: Escolher **do Azure Active Directory**.
   1. **Traduzir URL nos cabeçalhos**: Escolher **não**.

   > [!NOTE]
   > Se este for seu primeiro aplicativo, use a porta 3000 para iniciar e retorne para atualizar essa configuração se alterar a configuração de PingAccess. Para aplicativos subsequentes, a porta será necessário combinar o ouvinte configurado no PingAccess. Saiba mais sobre [ouvintes no PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

1. Selecione **Adicionar**. A página de visão geral para o novo aplicativo aparece.

Agora, atribuir um usuário de teste de aplicativos e escolher com base no cabeçalho de logon único:

1. Na barra lateral do aplicativo, selecione **usuários e grupos** > **adicionar usuário** > **usuários e grupos (\<número > selecionados)** . Aparece uma lista de usuários e grupos para sua escolha.

   ![Mostra a lista de usuários e grupos](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Selecione um usuário de teste de aplicativos e, em seguida, selecione **selecionar**. Verifique se essa conta de teste tem acesso ao aplicativo local.
1. Selecione **Atribuir**.
1. Na barra lateral do aplicativo, selecione **logon único** > **baseada em cabeçalho**.

   > [!TIP]
   > Se esta for a primeira vez que você usa o logon único com base em cabeçalhos, será necessário instalar o PingAccess. Para certificar-se de que sua assinatura do Azure seja automaticamente associada à sua instalação do PingAccess, use o link nesta página de logon único para baixar o PingAccess. Você pode abrir o site de download agora ou voltar a esta página mais tarde.

   ![Mostra a tela de entrada baseada em cabeçalho e o PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Clique em **Salvar**.

Em seguida, verifique se o redirecionamento de que URL é definida como a URL externa:

1. Dos **Centro de administração do Azure Active Directory** barra lateral, selecione **Azure Active Directory** > **registros do aplicativo**. É exibida uma lista de aplicativos.
1. Selecione seu aplicativo.
1. Selecione o link próximo a **URIs de redirecionamento**, mostrando o número de redirecionamento de URIs definido para a web e clientes públicos. O  **\<nome do aplicativo >-autenticação** página será exibida.
1. Verifique se a URL externa que você atribuiu ao seu aplicativo anteriormente está no **URIs de redirecionamento** lista. Se não estiver, adicione a URL externa agora, usando um tipo URI de redirecionamento de **Web**e selecione **salvar**.

Finalmente, configure seu aplicativo local para que os usuários têm acesso de leitura e outros aplicativos têm acesso de leitura/gravação:

1. Dos **registros de aplicativo** barra lateral para seu aplicativo, selecione **permissões de API** > **adicionar uma permissão**  >   **As APIs da Microsoft** > **o Microsoft Graph**. O **permissões de API de solicitação** página **Microsoft Graph** for exibida, que contém as APIs para o Windows Azure Active Directory.

   ![Mostra a página de permissões de API de solicitação](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Selecione **permissões delegadas** > **usuário** > **Read**.
1. Selecione **permissões de aplicativo** > **aplicativo** > **Application.ReadWrite.All**.
1. Selecione **adicionar permissões**.
1. No **permissões de API** página, selecione **conceder consentimento do administrador para \<seu nome do diretório >** .

#### <a name="collect-information-for-the-pingaccess-steps"></a>Coletar informações sobre as etapas do PingAccess

Você precisa coletar esses três partes de informações (todos os GUIDs) para configurar seu aplicativo com PingAccess:

| Nome do campo do AD do Azure | Nome do campo de PingAccess | Formato de dados |
| --- | --- | --- |
| **ID do aplicativo (cliente)** | **ID do cliente** | GUID |
| **ID de diretório (Locatário)** | **emissor** | GUID |
| `PingAccess key` | **Segredo do Cliente** | Cadeia de caracteres aleatória |

Para coletar essas informações:

1. Dos **Centro de administração do Azure Active Directory** barra lateral, selecione **Azure Active Directory** > **registros do aplicativo**. É exibida uma lista de aplicativos.
1. Selecione seu aplicativo. O **registros de aplicativo** página de seu aplicativo é exibida.

   ![Visão geral do registro para um aplicativo](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Lado a **ID do aplicativo (cliente)** valor, selecione o **copiar para área de transferência** ícone, em seguida, copie e salve-o. Especifique esse valor posteriormente como ID do cliente. do PingAccess
1. Próximo a **ID de diretório (Locatário)** de valor, selecione também **copiar para área de transferência**, em seguida, copie e salve-o. Especifique esse valor posteriormente como o emissor do PingAccess.
1. Na barra lateral do **registros de aplicativo** para seu aplicativo, selecione **certificados e segredos** > **novo segredo do cliente**. O **adicionar um segredo do cliente** página será exibida.

   ![Mostra a adicionar uma página de segredo do cliente](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. Na **descrição**, tipo `PingAccess key`.
1. Sob **Expires**, escolha como definir a chave do PingAccess: **Em 1 ano**, **em 2 anos**, ou **nunca**.
1. Selecione **Adicionar**. Os PingAccess chave aparece na tabela de segredos do cliente, com um aleatório de cadeia de caracteres que é automaticamente preenchida na **valor** campo.
1. Ao lado da chave de PingAccess **valor** campo, selecione o **copiar para área de transferência** ícone, em seguida, copie e salve-o. Especifique esse valor posteriormente como o segredo do cliente do PingAccess.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Atualizar a API do Graph para enviar campos personalizados (opcional)

Se você precisar de uma declaração personalizada que envia outros tokens dentro access_token consumida por PingAccess, defina as `acceptMappedClaims` campo de aplicativo para `True`. Você pode usar o Explorador do Graph ou o manifesto do aplicativo do portal do Azure AD para fazer essa alteração.

**Este exemplo usa o Explorador do Graph:**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**Este exemplo usa o [portal do Azure Active Directory](https://aad.portal.azure.com/) para atualizar o `acceptMappedClaims` campo:**

1. Entrar para o [portal do Azure Active Directory](https://aad.portal.azure.com/) como um administrador do aplicativo.
1. Selecione **Azure Active Directory** > **Registros de aplicativo**. É exibida uma lista de aplicativos.
1. Selecione seu aplicativo.
1. Na barra lateral do **registros de aplicativo** página do seu aplicativo, selecione **manifesto**. O código JSON de manifesto para o registro do seu aplicativo é exibida.
1. Pesquise o `acceptMappedClaims` do campo e altere o valor para `True`.
1. Clique em **Salvar**.

### <a name="use-of-optional-claims-optional"></a>Uso de declarações opcionais (opcionais)

Declarações opcionais permite que você adicione declarações standard-but-not-included-by-default que cada usuário e locatário tem. Você pode configurar declarações opcionais para o seu aplicativo modificando o manifesto do aplicativo. Para obter mais informações, consulte o [Noções básicas sobre o artigo de manifesto de aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Exemplo para incluir o endereço de email para o access_token que consumirá o PingAccess:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Uso da política (opcional) de mapeamento de declarações

[Declarações de política de mapeamento (visualização)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) para atributos que não existem no Azure AD. Mapeamento de declarações lhe permite migrar aplicativos de locais antigos para a nuvem, adicionando declarações personalizadas extras que são apoiadas por seus objetos de usuário ou o ADFS

Para tornar seu aplicativo usar uma declaração personalizada e incluir campos adicionais, não se esqueça de que você também [criou uma política de mapeamento de declarações personalizadas e atribuída ao aplicativo](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Para usar uma declaração personalizada, você também deve ter uma política personalizada definida e atribuída ao aplicativo. Essa política deve incluir todos os atributos personalizados necessários.
>
> Você pode fazer a definição de política e atribuição por meio do PowerShell, o Azure AD Graph Explorer ou o Microsoft Graph. Se você estiver fazendo-os no PowerShell, você talvez precise usar pela primeira vez `New-AzureADPolicy` e, em seguida, atribuí-lo para o aplicativo com `Add-AzureADServicePrincipalPolicy`. Para obter mais informações, consulte [atribuição de política de mapeamento de declarações](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Exemplo:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Habilitar o PingAccess usar declarações personalizadas

Habilitar o PingAccess usar declarações personalizadas é opcional, mas é obrigatória se você espera que o aplicativo consuma declarações adicionais.

Quando você configura o PingAccess na etapa a seguir, a sessão da Web que você irá criar (Configurações -> acesso -> sessões da Web) deve ter **solicitação de perfil** desmarcada e **atualizar atributos de usuário** definido como **não**

## <a name="download-pingaccess-and-configure-your-application"></a>Baixar o PingAccess e configurar seu aplicativo

Agora que você concluiu todas as etapas de instalação do Azure Active Directory, será possível prosseguir para a configuração do PingAccess.

As etapas detalhadas para a parte do PingAccess deste cenário continuam na documentação do Ping Identity. Siga as instruções em [configurar o PingAccess para Azure AD para proteger os aplicativos publicados usando o Proxy de aplicativo do Microsoft Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) no site da web a Ping Identity.

Essas etapas ajudam você a instalar o PingAccess e configurar uma conta do PingAccess (se você ainda não tiver um). Em seguida, para criar uma conexão do Azure AD OIDC (OpenID Connect), você configura um provedor de token com o **ID de diretório (Locatário)** valor que você copiou do portal do Azure AD. Em seguida, para criar uma sessão da web no PingAccess, use o **ID do aplicativo (cliente)** e `PingAccess key` valores. Em seguida, configure o mapeamento de identidade e crie um host virtual, site e aplicativo.

### <a name="test-your-application"></a>Teste seu aplicativo

Quando tiver concluído todas essas etapas, seu aplicativo deve estar em execução. Para testá-lo, abra um navegador e navegue até a URL externa que você criou quando publicou o aplicativo no Azure. Entrar com a conta de teste que você atribuiu ao aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Configurar o PingAccess para Azure AD proteger os aplicativos publicados usando o Proxy de aplicativo do Microsoft Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Logon único em aplicativos no Azure Active Directory](what-is-single-sign-on.md)
- [Solucionar problemas e mensagens de erro do Application Proxy](application-proxy-troubleshoot.md)
