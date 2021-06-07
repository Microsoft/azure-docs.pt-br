---
title: Habilitar Azure AD B2C domínios personalizados
titleSuffix: Azure AD B2C
description: Saiba como habilitar domínios personalizados em suas URLs de redirecionamento para Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2de419885938b27ebce4a934db5ef966965b3dbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580157"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Habilitar domínios personalizados para Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Este artigo descreve como habilitar domínios personalizados em suas URLs de redirecionamento para Azure Active Directory B2C (Azure AD B2C). O uso de um domínio personalizado com seu aplicativo proporciona uma experiência de usuário mais direta. Da perspectiva do usuário, eles permanecem em seu domínio durante o processo de entrada em vez de redirecionar para o Azure AD B2C domínio padrão *<nome-locatário>. b2clogin.com*.

![Experiência do usuário de domínio personalizado](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>Visão geral do domínio personalizado

Você pode habilitar domínios personalizados para Azure AD B2C usando a [porta frontal do Azure](https://azure.microsoft.com/services/frontdoor/). A porta frontal do Azure é um ponto de entrada global e escalonável que usa a rede de borda global da Microsoft para criar aplicativos Web rápidos, seguros e amplamente escalonáveis. Você pode renderizar Azure AD B2C conteúdo por trás da porta frontal do Azure e, em seguida, configurar uma opção na porta frontal do Azure para entregar o conteúdo por meio de um domínio personalizado na URL do seu aplicativo.

O diagrama a seguir ilustra a integração do Azure front door:

1. Em um aplicativo, um usuário clica no botão de entrada, que os leva para a página de entrada Azure AD B2C. Esta página especifica um nome de domínio personalizado.
1. O navegador da Web resolve o nome de domínio personalizado para o endereço IP da porta frontal do Azure. Durante a resolução DNS, um registro de nome canônico (CNAME) com um nome de domínio personalizado aponta para o host de front-end padrão de sua porta de front (por exemplo, `contoso.azurefd.net` ). 
1. O tráfego endereçado para o domínio personalizado (por exemplo, `login.contoso.com` ) é roteado para o host de front-end padrão especificado da porta de frente ( `contoso.azurefd.net` ).
1. A porta frontal do Azure invoca o conteúdo Azure AD B2C usando o Azure AD B2C `<tenant-name>.b2clogin.com` domínio padrão. A solicitação para o ponto de extremidade de Azure AD B2C inclui um cabeçalho HTTP personalizado que contém o nome de domínio personalizado original.
1. Azure AD B2C responde à solicitação exibindo o conteúdo relevante e o domínio personalizado original.

![Diagrama de rede de domínio personalizado](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> A conexão do navegador para a porta frontal do Azure sempre deve usar IPv4 em vez de IPv6.

Ao usar domínios personalizados, considere o seguinte:

- Você pode configurar vários domínios personalizados. Para obter o número máximo de domínios personalizados com suporte, consulte [limites e restrições de serviço do Azure ad](../active-directory/enterprise-users/directory-service-limits-restrictions.md) para Azure ad B2C e [assinatura do Azure e limites de serviço, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) para a porta frontal do Azure.
- A porta frontal do Azure é um serviço do Azure separado, portanto, encargos adicionais serão incorridos. Para obter mais informações, consulte [preços da porta frontal](https://azure.microsoft.com/pricing/details/frontdoor).
- Para usar o Firewall do [aplicativo Web](../web-application-firewall/afds/afds-overview.md)de porta frontal do Azure, você precisa confirmar se as regras e a configuração do firewall funcionam corretamente com seus Azure ad B2C fluxos de usuário.
- Depois de configurar domínios personalizados, os usuários ainda poderão acessar o Azure AD B2C nome de domínio padrão *<nome-do-locatário>. b2clogin.com* (a menos que você esteja usando uma política personalizada e [bloqueie o acesso](#block-access-to-the-default-domain-name).
- Se você tiver vários aplicativos, migre todos eles para o domínio personalizado porque o navegador armazena a sessão de Azure AD B2C sob o nome de domínio que está sendo usado no momento.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>Adicionar um nome de domínio personalizado ao seu locatário

Siga as orientações sobre como [Adicionar e validar seu domínio personalizado no Azure ad](../active-directory/fundamentals/add-custom-domain.md). Depois que o domínio for verificado, exclua o registro TXT do DNS que você criou.

> [!IMPORTANT]
> Para essas etapas, certifique-se de entrar no seu locatário do **Azure ad B2C** e selecione o serviço **Azure Active Directory** .

Verifique cada subdomínio que você planeja usar. Verificar apenas o domínio de nível superior não é suficiente. Por exemplo, para poder entrar com *login.contoso.com* e *Account.contoso.com*, você precisa verificar ambos os subdomínios e não apenas o domínio de nível superior *contoso.com*.  

## <a name="create-a-new-azure-front-door-instance"></a>Criar uma nova instância de porta de front do Azure

Siga as etapas para [criar uma porta frontal para seu aplicativo](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application) usando as configurações padrão para o host front-end e as regras de roteamento. 

> [!IMPORTANT]
> Para essas etapas, depois de entrar no portal do Azure na etapa 1, selecione **diretório + assinatura** e escolha o diretório que contém a assinatura do Azure que você deseja usar para a porta frontal do Azure. Esse *não* deve ser o diretório que contém seu locatário de Azure ad B2C. 

Na etapa **Adicionar um back-end**, use as seguintes configurações:

* Para **tipo de host de back-end**, selecione **host personalizado**.  
* Para **nome de host de back-end**, selecione o hostname para seu ponto de extremidade Azure AD B2C, <nome do locatário>. b2clogin.com. Por exemplo, contoso.b2clogin.com. 
* Para **cabeçalho de host de back-end**, selecione o mesmo valor selecionado para **nome de host de back-end**.

![Adicionar um back-end](./media/custom-domain/add-a-backend.png)

Depois de adicionar o **back-end** ao **pool de back-end**, desabilite as **investigações de integridade**.

![Adicionar um pool de back-end](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>Configurar seu domínio personalizado na porta frontal do Azure

Siga as etapas para [Adicionar um domínio personalizado à sua porta frontal](../frontdoor/front-door-custom-domain.md). Ao criar o `CNAME` registro para seu domínio personalizado, use o nome de domínio personalizado que você verificou anteriormente na etapa [Adicionar um nome de domínio personalizado ao Azure ad](#add-a-custom-domain-name-to-your-tenant) . 

Depois que o nome de domínio personalizado for verificado, selecione **nome de domínio personalizado https**. Em seguida, sob o **tipo de gerenciamento de certificado**, selecione gerenciamento de porta de [front-end](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door)ou [Use meu próprio certificado](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate). 

A captura de tela a seguir mostra como adicionar um domínio personalizado e habilitar o HTTPS usando um certificado de porta frontal do Azure.

![Configurar o domínio personalizado da porta de recepção do Azure](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>Configurar o CORS

Se você [Personalizar a interface do usuário do Azure ad B2C](customize-ui-with-html.md) com um modelo HTML, precisará [Configurar o CORS](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) com seu domínio personalizado.

Configure o armazenamento de BLOBs do Azure para compartilhamento de recursos entre origens com as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento.
1. No menu, selecione **CORS**.
1. Para **origens permitidas**, insira `https://your-domain-name`. Substitua `your-domain-name` pelo seu nome de domínio. Por exemplo, `https://login.contoso.com`. Use todas as letras minúsculas ao inserir o nome do locatário.
1. Para **métodos permitidos**, selecione ambos `GET` e `OPTIONS`.
1. Para **cabeçalhos permitidos**, digite um asterisco (*).
1. Para **cabeçalhos expostos**, digite um asterisco (*).
1. Para **Idade máxima de**, insira 200.
1. Selecione **Salvar**.

## <a name="configure-your-identity-provider"></a>Configurar seu provedor de identidade

Quando um usuário opta por entrar com um provedor de identidade social, Azure AD B2C inicia uma solicitação de autorização e leva o usuário para o provedor de identidade selecionado para concluir o processo de entrada. A solicitação de autorização especifica o `redirect_uri` com o Azure ad B2C nome de domínio padrão: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

Se você tiver configurado sua política para permitir a entrada com um provedor de identidade externo, atualize os URIs de redirecionamento OAuth com o domínio personalizado. A maioria dos provedores de identidade permite que você registre vários URIs de redirecionamento. É recomendável adicionar URIs de redirecionamento em vez de substituí-los para que você possa testar sua política personalizada sem afetar os aplicativos que usam o Azure AD B2C nome de domínio padrão. 

No seguinte URI de redirecionamento:

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- Substitua **<>de nome de domínio personalizado** pelo seu nome de domínio personalizado.
- Substitua **<>de nome de locatário** pelo nome do seu locatário ou sua ID de locatário.

O exemplo a seguir mostra um URI de redirecionamento OAuth válido:

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

Se você optar por usar a [ID do locatário](#optional-use-tenant-id), um URI de redirecionamento OAuth válido seria semelhante ao seguinte:

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

Os metadados dos [provedores de identidade SAML](saml-identity-provider-technical-profile.md) teriam a seguinte aparência:

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>Testar seu domínio personalizado

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **Fluxos de usuários (políticas)** .
1. Selecione um fluxo de usuário e, em seguida, selecione **executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **copiar para a área de transferência**.

    ![Copiar o URI de solicitação de autorização](./media/custom-domain/user-flow-run-now.png)

1. Na URL **executar ponto de extremidade de fluxo de usuário** , substitua o Azure ad B2C domínio (<nome-locatário>. b2clogin.com) pelo seu domínio personalizado.  
    Por exemplo, em vez de:

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    utilizá

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. Selecione **Executar fluxo de usuário**. Sua política de Azure AD B2C deve ser carregada.
1. Entre com contas locais e sociais.
1. Repita o teste com o restante de suas políticas.

## <a name="configure-your-application"></a>Configurar seu aplicativo 

Depois de configurar e testar o domínio personalizado, você pode atualizar seus aplicativos para carregar a URL que especifica o domínio personalizado como o nome do host em vez do domínio Azure AD B2C. 

A integração de domínio personalizado se aplica a pontos de extremidade de autenticação que usam políticas de Azure AD B2C (fluxos de usuário ou políticas personalizadas) para autenticar usuários. Esses pontos de extremidade podem ser semelhantes ao seguinte:

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Substitua:
- **personalizado-domínio** com seu domínio personalizado
- **nome do locatário** com o nome do locatário ou a ID do locatário
- **nome** da política com o nome da política. [Saiba mais sobre as políticas de Azure ad B2C](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


Os metadados do [provedor de serviços SAML](./saml-service-provider.md) podem ser semelhantes ao seguinte: 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>Adicional Usar ID de locatário

Você pode substituir o nome do locatário B2C na URL pelo GUID da sua ID de locatário para remover todas as referências a "B2C" na URL. Você pode encontrar o GUID da sua ID de locatário na página Visão geral do B2C em portal do Azure.
Por exemplo, altere `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` para `https://account.contosobank.co.uk/<tenant ID GUID>/`

Se você optar por usar a ID do locatário em vez do nome do locatário, certifique-se de atualizar os **URIs de redirecionamento OAuth** do provedor de identidade de acordo. Para obter mais informações, consulte [configurar seu provedor de identidade](#configure-your-identity-provider).

### <a name="token-issuance"></a>Emissão de token

A declaração do nome do emissor do token (ISS) é alterada com base no domínio personalizado que está sendo usado. Por exemplo:

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>Bloquear o acesso ao nome de domínio padrão

Depois de adicionar o domínio personalizado e configurar seu aplicativo, os usuários ainda poderão acessar o <nome do locatário> domínio. b2clogin.com. Para impedir o acesso, você pode configurar a política para verificar a solicitação de autorização "nome do host" em uma lista de domínios permitidos. O nome do host é o nome de domínio que aparece na URL. O nome do host está disponível por meio de `{Context:HostName}` [resolvedores de declaração](claim-resolver-overview.md). Em seguida, você pode apresentar uma mensagem de erro personalizada. 

1. Obtenha o exemplo de uma política de acesso condicional que verifica o nome do host do [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name).
1. Em cada arquivo, substitua a cadeia de caracteres `yourtenant` pelo nome do seu locatário de Azure ad B2C. Por exemplo, se o nome do seu locatário B2C for *contosob2c*, todas as instâncias de `yourtenant.onmicrosoft.com` se tornarão `contosob2c.onmicrosoft.com` .
1. Carregue os arquivos de política na seguinte ordem: `B2C_1A_TrustFrameworkExtensions_HostName.xml` e, em seguida, `B2C_1A_signup_signin_HostName.xml` .

::: zone-end

## <a name="troubleshooting"></a>Solução de problemas

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C retorna um erro de página não encontrada

- **Sintoma** – depois de configurar um domínio personalizado, ao tentar entrar com o domínio personalizado, você receberá uma mensagem de erro http 404.
- **Possíveis causas** – esse problema pode estar relacionado à configuração do DNS ou à configuração de back-end da porta do Azure. 
- **Resolução**:  
    1. Verifique se o domínio personalizado está [registrado e verificado com êxito](#add-a-custom-domain-name-to-your-tenant) em seu locatário Azure ad B2C.
    1. Verifique se o [domínio personalizado](../frontdoor/front-door-custom-domain.md) está configurado corretamente. O `CNAME` registro do seu domínio personalizado deve apontar para o host de front-end padrão do Azure front door (por exemplo, contoso.azurefd.net).
    1. Verifique se a [configuração do pool de back-end da porta do Azure](#set-up-your-custom-domain-on-azure-front-door) aponta para o locatário em que você configurou o nome de domínio personalizado e onde seu fluxo de usuário ou políticas personalizadas são armazenadas.

### <a name="identify-provider-returns-an-error"></a>Identificar provedor retorna um erro

- **Sintoma** – depois de configurar um domínio personalizado, você poderá entrar com contas locais. Mas quando você entra com credenciais de provedores de [identidade social ou corporativa](add-identity-provider.md)externa, os provedores de identidade apresentam uma mensagem de erro.
- **Causas possíveis** -quando Azure ad B2C leva o usuário para entrar com um provedor de identidade federada, ele especifica o URI de redirecionamento. O URI de redirecionamento é o ponto de extremidade para onde o provedor de identidade retorna o token. O URI de redirecionamento é o mesmo domínio usado pelo seu aplicativo com a solicitação de autorização. Se o URI de redirecionamento ainda não estiver registrado no provedor de identidade, ele poderá não confiar no novo URI de redirecionamento, o que resultará em uma mensagem de erro. 
- **Resolução** – siga as etapas em [configurar seu provedor de identidade](#configure-your-identity-provider) para adicionar o novo URI de redirecionamento. 


## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Posso usar a configuração avançada da porta de front-end do Azure, como *as regras de firewall do aplicativo Web*? 
  
Embora as definições de configuração avançada do Azure front door não sejam oficialmente suportadas, você pode usá-las por sua conta e risco. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>Quando uso executar agora para tentar executar minha política, por que não consigo ver o domínio personalizado?

Copie a URL, altere o nome de domínio manualmente e cole-a novamente no navegador.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Qual endereço IP é apresentado a Azure AD B2C? O endereço IP do usuário ou o endereço IP da porta frontal do Azure?

A porta frontal do Azure passa o endereço IP original do usuário. Esse é o endereço IP que você verá no relatório de auditoria ou na política personalizada.

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>Posso usar um WAF (firewall de aplicativo Web) de terceiros com B2C?

Para usar seu próprio firewall de aplicativo Web na frente da porta frontal do Azure, você precisa configurar e validar que tudo funciona corretamente com seus Azure AD B2C fluxos de usuário.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [as solicitações de autorização OAuth](protocols-overview.md).