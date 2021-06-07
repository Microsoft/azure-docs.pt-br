---
title: Autenticação baseada em cabeçalho com o PingAccess para Azure Proxy de Aplicativo do AD
description: Publique aplicativos com o PingAccess e o Proxy de Aplicativo a fim de oferecer suporte à autenticação baseada em cabeçalho.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 4abef9c848a32d9fa6a34eabe407c4d10f913797
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101643789"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Autenticação baseada em cabeçalho para logon único com Proxy de Aplicativo e PingAccess

O proxy de aplicativo Azure Active Directory (AD do Azure) fez parceria com a PingAccess para que os clientes do Azure AD possam acessar mais de seus aplicativos. O PingAccess fornece outra opção além [do logon único baseado em cabeçalho](application-proxy-configure-single-sign-on-with-headers.md)integrado.

## <a name="whats-pingaccess-for-azure-ad"></a>O que é o PingAccess para o Azure AD?

Com o PingAccess para o Azure AD, você pode conceder aos usuários acesso e logon único (SSO) a aplicativos que usam cabeçalhos para autenticação. O Proxy de Aplicativo trata esses aplicativos como qualquer outro, usando o Azure AD para autenticar o acesso e, depois, passando o tráfego por meio do serviço de conector. PingAccess residem na frente dos aplicativos e convertem o token de acesso do Azure AD em um cabeçalho. Em seguida, o aplicativo recebe a autenticação no formato que pode ser lido.

Os usuários não perceberão algo diferente quando entrarem para usar seus aplicativos corporativos. Eles ainda podem trabalhar de qualquer lugar e em qualquer dispositivo. Os conectores de proxy de aplicativo direcionam o tráfego remoto para todos os aplicativos sem considerar o tipo de autenticação, portanto, eles ainda balancearão as cargas automaticamente.

## <a name="how-do-i-get-access"></a>Como posso obter acesso?

Como esse cenário vem de uma parceria entre Azure Active Directory e PingAccess, você precisa de licenças para ambos os serviços. No entanto, as assinaturas do Azure Active Directory Premium incluem uma licença básica PingAccess que abrange até 20 aplicativos. Se você precisar publicar mais de 20 aplicativos com base em cabeçalho, poderá adquirir uma licença adicional do PingAccess.

Para obter mais informações, consulte [Edições do Active Directory do Azure](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publicar seu aplicativo no Azure

Este artigo é para que as pessoas publiquem um aplicativo com esse cenário pela primeira vez. Além de detalhar as etapas de publicação, ele o guiará na introdução ao proxy de aplicativo e ao PingAccess. Se você já tiver configurado os dois serviços, mas desejar um atualizador sobre as etapas de publicação, pule para a seção [Adicionar seu aplicativo ao Azure AD com o proxy de aplicativo](#add-your-application-to-azure-ad-with-application-proxy) .

> [!NOTE]
> Como esse cenário é uma parceria entre o Azure AD e o PingAccess, algumas das instruções estão no site do Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Instalar um conector do Proxy de Aplicativo

Se você habilitou o proxy de aplicativo habilitado e já instalou um conector, poderá ignorar esta seção e ir para [Adicionar seu aplicativo ao Azure AD com o proxy de aplicativo](#add-your-application-to-azure-ad-with-application-proxy).

O conector de proxy de aplicativo é um serviço do Windows Server que direciona o tráfego de seus funcionários remotos para seus aplicativos publicados. Para obter instruções de instalação mais detalhadas, consulte [tutorial: adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Entre no portal de [Azure Active Directory](https://aad.portal.azure.com/) como um administrador de aplicativos. A página **Azure Active Directory centro de administração** é exibida.
1. Selecione **Azure Active Directory**  >    >  **serviço do conector de download** do proxy de aplicativo. A página de **download do conector de proxy de aplicativo** é exibida.

   ![Download do conector de proxy de aplicativo](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Siga as instruções de instalação.

Baixar o conector deve habilitar automaticamente o proxy de aplicativo para seu diretório, mas se não estiver, você poderá selecionar **habilitar o proxy de aplicativo**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Adicionar seu aplicativo ao Azure AD com o proxy de aplicativo

Há duas ações necessárias no portal do Azure. Primeiro, você precisa publicar seu aplicativo com o Proxy de Aplicativo. Em seguida, você precisa coletar algumas informações sobre o aplicativo que pode usar durante as etapas de PingAccess.

#### <a name="publish-your-application"></a>Publicar seu aplicativo

Primeiro, você precisará publicar seu aplicativo. Essa ação envolve:

- Adicionando seu aplicativo local ao Azure AD
- Atribuir um usuário para testar o aplicativo e escolher SSO baseado em cabeçalho
- Configurando a URL de redirecionamento do aplicativo
- Concedendo permissões para usuários e outros aplicativos usarem seu aplicativo local

Para publicar seu próprio aplicativo local:

1. Se você não tiver na última seção, entre no portal de [Azure Active Directory](https://aad.portal.azure.com/) como um administrador de aplicativos.
1. Selecione **aplicativos empresariais**  >  **novo aplicativo**  >  **Adicionar um aplicativo local**. A página **Adicionar seu próprio aplicativo local** é exibida.

   ![Adicione seu próprio aplicativo local](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Preencha os campos obrigatórios com informações sobre seu novo aplicativo. Use as diretrizes abaixo para as configurações.

   > [!NOTE]
   > Para obter uma explicação mais detalhada desta etapa, consulte [Adicionar um aplicativo local ao Azure ad](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **URL interna**: normalmente, você fornece a URL que leva você para a página de entrada do aplicativo quando você está na rede corporativa. Para esse cenário, o conector precisa tratar o proxy PingAccess como a página inicial do aplicativo. Use este formato: `https://<host name of your PingAccess server>:<port>`. A porta é a 3000 por padrão, mas você pode configurá-la no PingAccess.

      > [!WARNING]
      > Para esse tipo de logon único, a URL interna deve usar `https` e não pode usar `http` . Além disso, há uma restrição ao configurar um aplicativo que dois aplicativos não devem ter a mesma URL interna, pois isso permite que o proxy de aplicativo mantenha a distinção entre os aplicativos.

   1. **Método de pré-autenticação**: escolha **Azure Active Directory**.
   1. **Converter URL em cabeçalhos**: escolha **não**.

   > [!NOTE]
   > Se este for seu primeiro aplicativo, use a porta 3000 para iniciar e retorne para atualizar essa configuração se alterar a configuração de PingAccess. Para aplicativos subsequentes, a porta precisará corresponder ao ouvinte que você configurou no PingAccess. Saiba mais sobre [ouvintes no PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Selecione **Adicionar**. A página Visão geral do novo aplicativo é exibida.

Agora, atribua um usuário para teste de aplicativo e escolha logon único baseado em cabeçalho:

1. Na barra lateral do aplicativo, selecione **usuários e grupos**  >  **Adicionar**  >  **usuários e grupos do usuário ( \<Number> selecionado)**. Uma lista de usuários e grupos é exibida para sua escolha.

   ![Mostra a lista de usuários e grupos](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Selecione um usuário para teste de aplicativo e selecione **selecionar**. Verifique se essa conta de teste tem acesso ao aplicativo local.
1. Selecione **Atribuir**.
1. Na barra lateral do aplicativo, selecione **logon único**  >  **com base em cabeçalho**.

   > [!TIP]
   > Se esta for a primeira vez que você usa o logon único com base em cabeçalhos, será necessário instalar o PingAccess. Para certificar-se de que sua assinatura do Azure seja automaticamente associada à sua instalação do PingAccess, use o link nesta página de logon único para baixar o PingAccess. Você pode abrir o site de download agora ou voltar a esta página mais tarde.

   ![Mostra a tela de logon baseada em cabeçalho e PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Clique em **Salvar**.

Em seguida, verifique se a URL de redirecionamento está definida para a URL externa:

1. Na barra lateral do **centro de administração Azure Active Directory** , selecione **Azure Active Directory**  >  **registros de aplicativo**. Uma lista de aplicativos é exibida.
1. Selecione seu aplicativo.
1. Selecione o link ao lado de **redirecionar URIs**, mostrando o número de URIs de redirecionamento configurados para clientes Web e públicos. A página **\<application name> -autenticação** é exibida.
1. Verifique se a URL externa que você atribuiu ao seu aplicativo anteriormente está na lista de **URIs de redirecionamento** . Se não estiver, adicione a URL externa agora, usando um tipo de URI de redirecionamento de **Web** e selecione **salvar**.

Além da URL externa, um ponto de extremidade de autorização de Azure Active Directory na URL externa deve ser adicionado à lista de URIs de redirecionamento.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

Por fim, configure seu aplicativo local para que os usuários tenham acesso de leitura e outros aplicativos tenham acesso de leitura/gravação:

1. Na barra lateral **registros de aplicativo** do seu aplicativo, selecione **permissões de API**  >  **Adicionar uma permissão**  >  **Microsoft Graph APIs da Microsoft**  >  . A página **solicitar permissões de API** para **Microsoft Graph** é exibida, que contém as APIs para o Windows Azure Active Directory.

   ![Mostra a página solicitar permissões de API](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Selecione **permissões delegadas**  >    >  **usuários usuário. ler**.
1. Selecione **permissões do aplicativo** aplicativo aplicativo  >    >  **. ReadWrite. tudo**.
1. Escolha **Adicionar permissões**.
1. Na página **permissões de API** , selecione **conceder consentimento de administrador \<your directory name> para**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Coletar informações sobre as etapas do PingAccess

Você precisa coletar essas três informações (todos os GUIDs) para configurar seu aplicativo com o PingAccess:

| Nome do campo do Azure AD | Nome do campo PingAccess | Formato de dados |
| --- | --- | --- |
| **ID do aplicativo (cliente)** | **ID do Cliente** | GUID |
| **ID do diretório (locatário)** | **Emissor** | GUID |
| `PingAccess key` | **Segredo do Cliente** | Cadeia de caracteres aleatória |

Para coletar essas informações:

1. Na barra lateral do **centro de administração Azure Active Directory** , selecione **Azure Active Directory**  >  **registros de aplicativo**. Uma lista de aplicativos é exibida.
1. Selecione seu aplicativo. A página **registros de aplicativo** para seu aplicativo é exibida.

   ![Visão geral do registro de um aplicativo](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Ao lado do valor da **ID do aplicativo (cliente)** , selecione o ícone **copiar para área de transferência** , em seguida, copie e salve-o. Você especifica esse valor posteriormente como a ID do cliente do PingAccess.
1. Em seguida, o valor da **ID do diretório (locatário)** , também selecione **copiar para a área de transferência**, copie e salve-o. Você especifica esse valor posteriormente como emissor de PingAccess.
1. Na barra lateral do **registros de aplicativo** para seu aplicativo, selecione **certificados e segredos**  >  **novo segredo do cliente**. A página **Adicionar um segredo do cliente** é exibida.

   ![Mostra a página Adicionar um segredo do cliente](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. Em **Descrição**, digite `PingAccess key` .
1. Em **expirar**, escolha como definir a chave PingAccess: **em 1 ano**, **em 2 anos** ou **nunca**.
1. Selecione **Adicionar**. A chave PingAccess aparece na tabela de segredos do cliente, com uma cadeia de caracteres aleatória que é preenchida por preenchimento automática no campo **valor** .
1. Ao lado do campo **valor** da chave PingAccess, selecione o ícone **copiar para área de transferência** , em seguida, copie e salve-o. Você especifica esse valor posteriormente como o segredo do cliente do PingAccess.

**Atualize o `acceptMappedClaims` campo:**

1. Entre no portal de [Azure Active Directory](https://aad.portal.azure.com/) como um administrador de aplicativos.
1. Selecione **Azure Active Directory** > **Registros de aplicativo**. Uma lista de aplicativos é exibida.
1. Selecione seu aplicativo.
1. Na barra lateral da página **registros de aplicativo** para seu aplicativo, selecione **manifesto**. O código JSON do manifesto para o registro do seu aplicativo é exibido.
1. Pesquise o `acceptMappedClaims` campo e altere o valor para `True` .
1. Clique em **Salvar**.

### <a name="use-of-optional-claims-optional"></a>Uso de declarações opcionais (opcional)

As declarações opcionais permitem que você adicione declarações Standard-, mas-não incluído-por padrão que todos os usuários e locatários têm. Você pode configurar declarações opcionais para seu aplicativo modificando o manifesto do aplicativo. Para obter mais informações, consulte o [artigo noções básicas sobre o manifesto de aplicativo do Azure ad](../develop/reference-app-manifest.md)

Exemplo para incluir o endereço de email no access_token que o PingAccess consumirá:

```json
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

### <a name="use-of-claims-mapping-policy-optional"></a>Uso da política de mapeamento de declarações (opcional)

[Política de mapeamento de declarações (versão prévia)](../develop/active-directory-claims-mapping.md#claims-mapping-policy-properties) para atributos que não existem em AzureAD. O mapeamento de declarações permite migrar aplicativos locais antigos para a nuvem adicionando declarações personalizadas adicionais que são apoiadas por seus objetos de usuário ou ADFS

Para fazer seu aplicativo usar uma declaração personalizada e incluir campos adicionais, certifique-se de que você também [criou uma política personalizada de mapeamento de declarações e a atribuiu ao aplicativo](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Para usar uma declaração personalizada, você também deve ter uma política personalizada definida e atribuída ao aplicativo. Essa política deve incluir todos os atributos personalizados necessários.
>
> Você pode fazer a definição de política e a atribuição por meio do PowerShell ou Microsoft Graph. Se estiver fazendo isso no PowerShell, talvez seja necessário primeiro usar `New-AzureADPolicy` e, em seguida, atribuí-lo ao aplicativo com `Add-AzureADServicePrincipalPolicy` . Para obter mais informações, consulte [atribuição de política de mapeamento de declarações](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Exemplo:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Habilitar PingAccess para usar declarações personalizadas

Habilitar o PingAccess para usar declarações personalizadas é opcional, mas será necessário se você espera que o aplicativo consuma declarações adicionais.

Ao configurar o PingAccess na etapa a seguir, a sessão da Web que você criará (configurações->Access->sessões da Web) deverá ter o **perfil de solicitação** desmarcado e atualizar os **atributos de usuário** definido como **não**

## <a name="download-pingaccess-and-configure-your-application"></a>Baixar o PingAccess e configurar seu aplicativo

Agora que você concluiu todas as etapas de instalação do Azure Active Directory, será possível prosseguir para a configuração do PingAccess.

As etapas detalhadas para a parte PingAccess desse cenário continuam na documentação de identidade de ping. Siga as instruções em [Configurar o PingAccess para o Azure ad para proteger aplicativos publicados usando Microsoft Azure ad proxy de aplicativo](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) no site de identidade de ping e baixe a [versão mais recente do PingAccess](https://www.pingidentity.com/en/lp/azure-download.html?).

Essas etapas ajudam a instalar o PingAccess e a configurar uma conta do PingAccess (se você ainda não tiver uma). Em seguida, para criar uma conexão do OpenID Connect (OIDC) do Azure AD, você configura um provedor de token com o valor de **ID de diretório (locatário)** que você copiou do portal do Azure AD. Em seguida, para criar uma sessão da Web no PingAccess, use a **ID do aplicativo (cliente)** e os `PingAccess key` valores. Em seguida, configure o mapeamento de identidade e crie um host virtual, site e aplicativo.

### <a name="test-your-application"></a>Teste seu aplicativo

Quando você concluir todas essas etapas, seu aplicativo deverá estar em execução. Para testá-lo, abra um navegador e navegue até a URL externa que você criou quando publicou o aplicativo no Azure. Entre com a conta de teste que você atribuiu ao aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Configurar o PingAccess para o Azure AD para proteger aplicativos publicados usando Microsoft Azure AD proxy de aplicativo](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [Logon único em aplicativos no Azure Active Directory](what-is-single-sign-on.md)
- [Solucionar problemas e mensagens de erro do Application Proxy](application-proxy-troubleshoot.md)