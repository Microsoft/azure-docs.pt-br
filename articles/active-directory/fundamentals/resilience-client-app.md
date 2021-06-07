---
title: Aumentar a resiliência de autenticação e autorização em aplicativos cliente que você desenvolve
titleSuffix: Microsoft identity platform
description: Diretrizes para aumentar a resiliência de autenticação e autorização no aplicativo cliente usando a plataforma de identidade da Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: bc3b041e44fad66a4edc6ff34c0e534dc423de86
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226583"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>Aumentar a resiliência de autenticação e autorização em aplicativos cliente que você desenvolve

Esta seção fornece orientação sobre a criação de resiliência em aplicativos cliente que usam a plataforma de identidade da Microsoft e o Azure Active Directory para conectar usuários e executar ações em nome desses usuários.

## <a name="use-the-microsoft-authentication-library-msal"></a>Usar a MSAL (biblioteca de autenticação da Microsoft)

A [MSAL (biblioteca de autenticação da Microsoft)](../develop/msal-overview.md) é uma parte fundamental da [plataforma de identidade da Microsoft](../develop/index.yml). Ele simplifica e gerencia a aquisição, o gerenciamento, o armazenamento em cache e a atualização de tokens e usa as práticas recomendadas para resiliência. O MSAL foi projetado para habilitar uma solução segura sem que os desenvolvedores precisem se preocupar com os detalhes da implementação.

O MSAL armazena em cache os tokens e usa um padrão de aquisição de token silencioso. Ele também serializa automaticamente o cache de token em plataformas que fornecem nativamente armazenamento seguro, como o Windows UWP, iOS e Android. Os desenvolvedores podem personalizar o comportamento de serialização ao usar [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization), [MSAL.net](../develop/msal-net-token-cache-serialization.md), [MSAL para Java](../develop/msal-java-token-cache-serialization.md)e [MSAL para Python](../develop/msal-python-token-cache-serialization.md).

![Imagem de dispositivo com e aplicativo usando MSAL para chamar o Microsoft Identity](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

Ao usar MSAL, o cache de token, a atualização e a aquisição silenciosa têm suporte automaticamente. Você pode usar padrões simples para adquirir os tokens necessários para a autenticação moderna. Damos suporte a várias linguagens e você pode encontrar um exemplo que corresponda ao seu idioma e cenário em nossa página de [exemplos](../develop/sample-v2-code.md) .

## <a name="c"></a>[C#](#tab/csharp)

```csharp
try
{
    result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
    result = await app.AcquireToken(scopes).WithClaims(ex.Claims).ExecuteAsync()
}
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
return myMSALObj.acquireTokenSilent(request).catch(error => {
    console.warn("silent token acquisition fails. acquiring token using redirect");
    if (error instanceof msal.InteractionRequiredAuthError) {
        // fallback to interaction when silent call fails
        return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
            console.log(tokenResponse);

            return tokenResponse;
        }).catch(error => {
            console.error(error);
        });
    } else {
        console.warn(error);
    }
});
```

---

O MSAL pode, em alguns casos, atualizar tokens proativamente. Quando o Microsoft Identity emite um token de vida longa, ele pode enviar informações ao cliente para o tempo ideal para atualizar o token ("atualizar \_ "). O MSAL atualizará proativamente o token com base nessas informações. O aplicativo continuará a ser executado enquanto o token antigo for válido, mas terá um período de tempo maior durante o qual fazer outra aquisição de token bem-sucedida.

### <a name="stay-up-to-date"></a>Fique atualizado

Os desenvolvedores devem ter um processo de atualização para a versão mais recente do MSAL. A autenticação faz parte da segurança do seu aplicativo e seu aplicativo precisa permanecer atualizado com os aprimoramentos de segurança contidos nas novas versões do MSAL. Geralmente, essa é uma boa prática para bibliotecas sob desenvolvimento contínuo e isso garantirá que você tenha o código mais atualizado em relação à resiliência do aplicativo. À medida que a identidade da Microsoft continua a inovar de maneiras para que os aplicativos sejam mais resilientes, os aplicativos que usam o MSAL mais recente serão os mais preparados para se basear nessas inovações.

[Verifique a versão mais recente do MSAL.js e as notas de versão](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Verifique a versão mais recente do MSAL do .NET e as notas de versão](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[Verifique a versão mais recente do MSAL do Python e as notas de versão](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[Verifique a versão do MSAL do Java e as notas de versão mais recentes](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[Verifique as versões mais recentes do MSAL iOS e macOS e notas de versão](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[Verifique a versão mais recente do MSAL do Android e as notas de versão](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[Verifique a versão angular mais recente do MSAL e as notas de versão](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Verifique a versão mais recente do Microsoft. Identity. Web e notas de versão](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="use-resilient-patterns-for-token-handling"></a>Usar padrões resilientes para tratamento de tokens

Se você não estiver usando o MSAL, poderá usar esses padrões resilientes para manipulação de token. Essas práticas recomendadas são implementadas automaticamente pela biblioteca MSAL. 

Em geral, um aplicativo que usa autenticação moderna chamará um ponto de extremidade para recuperar tokens que autenticam o usuário ou autorizam o aplicativo a chamar APIs protegidas. O MSAL destina-se a lidar com os detalhes de autenticação e implementa vários padrões para melhorar a resiliência desse processo. Use as orientações desta seção para implementar as práticas recomendadas se você optar por usar uma biblioteca diferente de MSAL. Se você usar o MSAL, obterá todas essas práticas recomendadas gratuitamente, pois MSAL as implementa automaticamente.

### <a name="cache-tokens"></a>Tokens de cache

Os aplicativos devem armazenar em cache corretamente os tokens recebidos da identidade da Microsoft. Quando seu aplicativo recebe tokens, a resposta HTTP que contém os tokens também contém uma propriedade "expires_in" que informa ao aplicativo quanto tempo armazenar em cache e reutilizar, o token. É importante que os aplicativos usem a propriedade "expires_in" para determinar o ciclo de vida do token. O aplicativo nunca deve tentar decodificar um token de acesso de API.

![Um aplicativo fazendo uma chamada para a identidade da Microsoft, mas a chamada passa por um cache de token no dispositivo que executa o aplicativo](media/resilience-client-app/token-cache.png)

O uso do token armazenado em cache impede o tráfego desnecessário entre seu aplicativo e a identidade da Microsoft e torna seu aplicativo menos suscetível a falhas de aquisição de token, reduzindo o número de chamadas de aquisição de token. Os tokens armazenados em cache também melhoram o desempenho do aplicativo, pois o aplicativo precisa bloquear a aquisição de tokens com menos. Seu usuário pode permanecer conectado ao seu aplicativo durante o tempo de vida do token.

### <a name="serialize-and-persist-tokens"></a>Serializar e persistir tokens

Os aplicativos devem serializar com segurança seu cache de token para persistir os tokens entre instâncias do aplicativo. Os tokens podem ser reutilizados desde que estejam dentro de seu tempo de vida válido. Os [tokens de atualização](../develop/v2-oauth2-auth-code-flow.md#refresh-the-access-token)e, cada vez mais, os [tokens de acesso](../develop/access-tokens.md), são emitidos por muitas horas. Essa hora válida pode abranger um usuário que inicia seu aplicativo muitas vezes. Quando seu aplicativo é iniciado, ele deve verificar se há um token de acesso ou de atualização válido que possa ser usado. Isso aumentará a resiliência e o desempenho do aplicativo, pois evita quaisquer chamadas desnecessárias à identidade da Microsoft.

![Um aplicativo fazendo uma chamada para a identidade da Microsoft, mas a chamada passa por um cache de token, bem como um repositório de token no dispositivo que executa o aplicativo](media/resilience-client-app/token-store.png)

O armazenamento de token persistente deve ter acesso controlado e criptografado para o usuário proprietário ou a identidade do processo. Em plataformas como dispositivos móveis, Windows e Mac, o desenvolvedor deve aproveitar os recursos internos para armazenar credenciais.

### <a name="acquire-tokens-silently"></a>Adquirir tokens silenciosamente

O processo de autenticação de um usuário ou de recuperação de autorização para chamar uma API pode exigir várias etapas no Microsoft Identity. Por exemplo, quando o usuário entra pela primeira vez, pode ser necessário inserir as credenciais e executar uma autenticação multifator por meio de uma mensagem de texto. Cada etapa adiciona uma dependência no recurso que fornece esse serviço. A melhor experiência para o usuário e aquele com as dependências mínimas é tentar adquirir um token silenciosamente para evitar essas etapas adicionais antes de solicitar a interação do usuário.

![Diagrama mostrando os vários serviços dentro da identidade da Microsoft que talvez precisem ser executados para concluir o processo de autenticação ou autorização de um usuário](media/resilience-client-app/external-dependencies.png)

A aquisição de um token é iniciada silenciosamente com o uso de um token válido do cache de token do aplicativo. Se não houver um token válido disponível, o aplicativo deverá tentar adquirir um token usando um token de atualização, se disponível, e o ponto de extremidade do token. Se nenhuma dessas opções estiver disponível, o aplicativo deverá adquirir um token usando o parâmetro "prompt = None". Isso usará o ponto de extremidade de autorização, mas não mostrará nenhuma interface de usuário para o usuário. Se a identidade da Microsoft puder fornecer um token para o aplicativo sem interagir com o usuário, será. Se nenhum desses métodos resultar em um token, um usuário precisará se autenticar novamente de forma interativa.

> [!NOTE]
> Em geral, os aplicativos devem evitar o uso de prompts como "logon" e "consentimento", pois eles forçarão a interação do usuário mesmo quando nenhuma interação for necessária.

## <a name="handle-service-responses-properly"></a>Tratar respostas de serviço corretamente

Embora os aplicativos devam lidar com todas as respostas de erro, há algumas respostas que podem afetar a resiliência. Se seu aplicativo receber um código de resposta HTTP 429, muitas solicitações, a identidade da Microsoft estará limitando suas solicitações. Se seu aplicativo continuar a fazer muitas solicitações, ele continuará a ser limitado, impedindo que seu aplicativo receba tokens. Seu aplicativo não deve tentar adquirir um token novamente até depois do tempo, em segundos, no campo de resposta de Retry-After passado. Receber uma resposta 429 geralmente é uma indicação de que o aplicativo não está armazenando em cache e reutilizando tokens corretamente. Os desenvolvedores devem examinar como os tokens são armazenados em cache e reutilizados no aplicativo.

Quando um aplicativo recebe um código de resposta HTTP 5xx, o aplicativo não deve entrar em um loop de repetição rápido. Quando presente, o aplicativo deve respeitar o mesmo Retry-After tratamento que o faz para uma resposta de 429. Se nenhum cabeçalho de Retry-After for fornecido pela resposta, é recomendável implementar uma nova tentativa de retirada exponencial com a primeira tentativa, pelo menos, 5 segundos após a resposta.

Quando uma solicitação atinge o tempo limite, os aplicativos não devem tentar de imediato imediatamente. Implemente uma nova tentativa de retirada exponencial com a primeira tentativa, pelo menos, 5 segundos após a resposta.

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>Avaliar as opções para recuperar informações relacionadas à autorização

Muitos aplicativos e APIs precisam de informações específicas sobre o usuário para tomar decisões de autorização. Há algumas maneiras de um aplicativo obter essas informações. Cada método tem suas vantagens e desvantagens. Os desenvolvedores devem avaliar isso para determinar qual estratégia é a melhor para resiliência em seu aplicativo.

### <a name="tokens"></a>Tokens

Tokens de identidade (ID) e tokens de acesso contêm declarações padrão que fornecem informações sobre o assunto. Eles estão documentados em [tokens de ID da plataforma Microsoft Identity](../develop/id-tokens.md) e [tokens de acesso da plataforma de identidade da Microsoft](../develop/access-tokens.md). Se as informações de que seu aplicativo precisa já estiverem no token, a técnica mais eficiente para recuperar esses dados será usar declarações de token, pois isso economizará o sinal de uma chamada de rede adicional para recuperar informações separadamente. Menos chamadas de rede significam maior resiliência geral para o aplicativo.

> [!NOTE]
> Alguns aplicativos chamam o ponto de extremidade UserInfo para recuperar declarações sobre o usuário autenticado. As informações disponíveis no token de ID que seu aplicativo pode receber são um superconjunto das informações que ele pode obter do ponto de extremidade de UserInfo. Seu aplicativo deve usar o token de ID para obter informações sobre o usuário em vez de chamar o ponto de extremidade de UserInfo.

Um desenvolvedor de aplicativos pode aumentar as declarações de token padrão com [declarações opcionais](../develop/active-directory-optional-claims.md). Uma declaração opcional comum são os [grupos](../develop/active-directory-optional-claims.md#configuring-groups-optional-claims). Há várias maneiras de adicionar declarações de grupo. A opção "grupo de aplicativos" inclui apenas grupos atribuídos ao aplicativo. As opções "todos" ou "grupos de segurança" incluem grupos de todos os aplicativos no mesmo locatário, que podem adicionar muitos grupos ao token. É importante avaliar o efeito no seu caso, pois ele pode potencialmente negar a eficiência obtida pela solicitação de grupos no token, causando o excesso de tokens e até mesmo exigindo chamadas adicionais para obter a lista completa de grupos.

Em vez de usar grupos em seu token, você pode usar e incluir funções de aplicativo. Os desenvolvedores podem definir [funções de aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para seus aplicativos e APIs que o cliente pode gerenciar a partir de seu diretório usando o portal ou as APIs. Os profissionais de ti podem atribuir funções a diferentes usuários e grupos para controlar quem tem acesso a qual conteúdo e funcionalidade. Quando um token é emitido para o aplicativo ou API, as funções atribuídas ao usuário estarão disponíveis na declaração de funções no token. Obter essas informações diretamente em um token pode salvar chamadas de APIs adicionais.

Por fim, os administradores de ti também podem adicionar declarações com base em informações específicas em um locatário. Por exemplo, uma empresa pode ter uma extensão para ter uma ID de usuário específica da empresa.

Em todos os casos, adicionar informações do diretório diretamente a um token pode ser eficiente e aumentar a resiliência dos aplicativos, reduzindo o número de dependências que o aplicativo tem. Por outro lado, ele não resolve problemas de resiliência de não conseguir adquirir um token. Você deve adicionar apenas declarações opcionais para os principais cenários do seu aplicativo. Se o aplicativo exigir informações apenas para a funcionalidade de administrador, é melhor que o aplicativo obtenha essas informações apenas conforme necessário.

### <a name="microsoft-graph"></a>Microsoft Graph

O Microsoft Graph fornece um ponto de extremidade de API unificado para acessar os dados de Microsoft 365 que descrevem os padrões de produtividade, identidade e segurança em uma organização. Os aplicativos que usam Microsoft Graph podem potencialmente usar qualquer uma das informações em Microsoft 365 para tomar decisões de autorização.

Os aplicativos exigem apenas um único token para acessar todos os Microsoft 365. Isso é mais resiliente do que usar as APIs mais antigas que são específicas para Microsoft 365 componentes como o Microsoft Exchange ou o Microsoft SharePoint, onde vários tokens são necessários.

Ao usar as APIs do Microsoft Graph, sugerimos o uso de um [SDK do Microsoft Graph](/graph/sdks/sdks-overview). Os SDKs de Microsoft Graph foram projetados para simplificar a criação de aplicativos de alta qualidade, eficientes e resilientes que acessam Microsoft Graph.

Para decisões de autorização, os desenvolvedores devem considerar quando usar as declarações disponíveis em um token como uma alternativa para algumas chamadas de Microsoft Graph. Conforme mencionado acima, os desenvolvedores podem solicitar grupos, funções de aplicativo e declarações opcionais em seus tokens. Em termos de resiliência, o uso de Microsoft Graph para autorização requer chamadas de rede adicionais que dependem da identidade da Microsoft (para obter o token para acessar Microsoft Graph), bem como Microsoft Graph em si. No entanto, se seu aplicativo já depender de Microsoft Graph como sua camada de dados, depender do grafo para autorização não é um risco adicional a ser adotado.

## <a name="use-broker-authentication-on-mobile-devices"></a>Usar a autenticação do agente em dispositivos móveis

Em dispositivos móveis, o uso de um agente de autenticação como Microsoft Authenticator melhorará a resiliência. O agente adiciona benefícios acima do que está disponível com outras opções, como o navegador do sistema ou uma WebView incorporada. O agente de autenticação pode utilizar um PRT ( [token de atualização principal](../devices/concept-primary-refresh-token.md) ) que contém declarações sobre o usuário e o dispositivo e pode ser usado para obter tokens de autenticação para acessar outros aplicativos do dispositivo. Quando um PRT é usado para solicitar acesso a um aplicativo, seu dispositivo e as declarações de MFA são confiáveis pelo Azure AD. Isso aumenta a resiliência, evitando etapas adicionais para autenticar o dispositivo novamente. Os usuários não serão desafiados com vários prompts MFA no mesmo dispositivo, aumentando, portanto, a resiliência reduzindo as dependências dos serviços externos e melhorando a experiência do usuário.

![Um aplicativo fazendo uma chamada para a identidade da Microsoft, mas a chamada passa por um cache de token, bem como um repositório de token e um agente de autenticação no dispositivo que executa o aplicativo](media/resilience-client-app/authentication-broker.png)

A autenticação do agente é suportada automaticamente pelo MSAL. Você pode encontrar mais informações sobre como usar a autenticação orientada nas seguintes páginas:

- [Configurar o SSO no macOS e no iOS](../develop/single-sign-on-macos-ios.md#sso-through-authentication-broker-on-ios)
- [Como habilitar o SSO entre aplicativos no Android usando o MSAL](../develop/msal-android-single-sign-on.md)

## <a name="adopt-continuous-access-evaluation"></a>Adote a avaliação de acesso contínuo

A [Continuous Access Evaluation (CAE)](../conditional-access/concept-continuous-access-evaluation.md) é um desenvolvimento recente que pode aumentar a segurança e a resiliência do aplicativo com tokens de vida útil longa. CAE é um padrão industrial emergente que está sendo desenvolvido no grupo de trabalho de sinais e eventos compartilhados do OpenID Foundation. Com o CAE, um token de acesso pode ser revogado com base em [eventos críticos](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) e na [avaliação da política](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview), em vez de depender de um tempo de vida de token curto. Para algumas APIs de recursos, como o risco e a política são avaliados em tempo real, o CAE pode aumentar substancialmente o tempo de vida do token de até 28 horas. À medida que as APIs de recursos e os aplicativos adotarem o CAE, a identidade da Microsoft poderá emitir tokens de acesso que sejam revogável e válidos por longos períodos de tempo. Esses tokens de longa duração serão atualizados proativamente pelo MSAL.

Embora o CAE esteja nas primeiras fases, é possível [desenvolver aplicativos cliente hoje que se beneficiarão do CAE](../develop/app-resilience-continuous-access-evaluation.md) quando os recursos (APIs) usados pelo aplicativo adotarem o CAE. À medida que mais recursos adotarem o CAE, seu aplicativo poderá adquirir tokens habilitados para CAE para esses recursos também. A API de Microsoft Graph e os [SDKs de Microsoft Graph](/graph/sdks/sdks-overview), visualizarão o recurso CAE no início 2021. Se você quiser participar da visualização pública de Microsoft Graph com CAE, você pode nos informar que está interessado aqui: [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview) .

Se você desenvolver APIs de recursos, recomendamos que você participe dos [sinais e eventos compartilhados WG](https://openid.net/wg/sse/). Estamos trabalhando com esse grupo para habilitar o compartilhamento de eventos de segurança entre o Microsoft Identity e os provedores de recursos.

## <a name="next-steps"></a>Próximas etapas

- [Como usar APIs habilitadas para avaliação de acesso contínuo em seus aplicativos](../develop/app-resilience-continuous-access-evaluation.md)
- [Crie resiliência em aplicativos de daemon](resilience-daemon-app.md)
- [Crie resiliência em sua infraestrutura de gerenciamento de identidade e acesso](resilience-in-infrastructure.md)
- [Crie resiliência em seus sistemas CIAM](resilience-b2c.md)
