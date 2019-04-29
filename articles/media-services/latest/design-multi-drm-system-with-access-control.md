---
title: Criação de um sistema de proteção de conteúdo multi-DRM com controle de acesso – Serviços de Mídia do Azure | Microsoft Docs
description: Aprenda a licenciar o Kit de Portabilidade de Cliente do Smooth Streaming da Microsoft.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: ef695d913c73f0a4266b20f21f1008108b85b4d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734004"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Criação de um sistema de proteção de conteúdo de DRM múltiplo com controle de acesso 

## <a name="overview"></a>Visão geral

O design e a criação de um subsistema de Microsoft Ad Rights Management (gerenciamento de direitos digitais) para uma solução de streaming OTT (over-the-top) ou online é uma tarefa complexa. Os operadores ou provedores de vídeo online normalmente terceirizam essa tarefa para provedores de serviço de DRM especializados. A meta deste documento é apresentar um design e uma implementação de referência de um subsistema DRM de ponta a ponta em uma solução de streaming OTT ou online.

Este documento se destina a engenheiros que trabalham em subsistemas de DRM de soluções multitela/streaming OTT ou online, ou a leitores interessados em subsistemas de DRM. Pressupõe-se que os leitores estejam familiarizados com pelo menos uma das tecnologias de DRM do mercado, como o PlayReady, o Widevine, o FairPlay ou o Adobe Access.

Nessa discussão, por vários DRMs, incluímos 3 DRMs que têm suporte dos Serviços de Mídia do Azure: Criptografia comum (CENC) para PlayReady e Widevine, FairPlay, além de criptografia do tipo Clear Key AES-128. Uma das principais tendências no setor de streaming online e OTT é o uso de CENC com DRM múltiplo nativo em várias plataformas de cliente. Essa tendência é uma mudança da anterior, que usava um único DRM e seu SDK de cliente para várias plataformas de cliente. Quando você usa CENC com DRM múltiplo nativo, tanto o PlayReady quanto o Widevine são criptografados de acordo com a especificação [Criptografia comum (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/).

Os benefícios do uso de vários DRM nativos para proteção de conteúdo são:

* Redução do custo de criptografia, pois um único processo de criptografia é usado para diferentes plataformas e seus DRMs nativos.
* Reduz o custo de gerenciamento de ativos criptografados, pois é necessária apenas uma única cópia no armazenamento.
* Eliminação do custo de licenciamento de cliente de DRM, pois o cliente de DRM nativo normalmente é gratuito na respectiva plataforma nativa.


### <a name="goals-of-the-article"></a>Metas do artigo

Os objetivos deste artigo são:

* Fornece um design de referência de um subsistema DRM que usa todos os 3 DRMs (CENC para DASH, o FairPlay para HLS e PlayReady para streaming suave).
* Forneça uma implementação de referência em uma plataforma dos Serviços de Mídia do Azure.
* Analisar alguns tópicos sobre design e implementação.

A tabela a seguir resume o suporte DRM nativo em diferentes plataformas e suporte a EME em navegadores diferentes.

| **Plataforma cliente** | **DRM Nativo** | **EME** |
| --- | --- | --- |
| **Smart TVs, STBs** | PlayReady, Widevine e/ou outros | Navegador inserido/EME para PlayReady e/ou Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 para PlayReady|
| **Dispositivos Android (telefone, tablet, TV)** |Widevine |Chrome para Widevine |
| **iOS** | FairPlay | Safari para FairPlay (desde o iOS 11.2) |
| **macOS** | FairPlay | Safari para FairPlay (desde o Safari 9 + no Mac OS X 10.11 e posteriores El Capitan)|
| **tvOS** | FairPlay | |

Considerando o estado atual da implantação para cada DRM, um serviço geralmente deseja implementar dois ou três DRMs para se certificar de que você resolva todos os tipos de ponto de extremidade da melhor maneira.

Há um equilíbrio entre a complexidade da lógica do serviço e a complexidade no lado do cliente para atingir um determinado nível de experiência do usuário em vários clientes.

Para fazer a seleção, lembre-se que:

* O PlayReady é implementado nativamente em cada dispositivo Windows, em alguns dispositivos Android e está disponível por meio de SDKs de software em praticamente qualquer plataforma.
* O Widevine é implementado nativamente em cada dispositivo Android, no Chrome e em alguns outros dispositivos. Também há suporte para Widevine em navegadores Firefox e Opera em DASH.
* FairPlay está disponível no iOS, macOS e tvOS.


## <a name="a-reference-design"></a>Um design de referência
Esta seção apresenta um design de referência que é independente das tecnologias usadas para implementá-lo.

Um subsistema de DRM pode conter os seguintes componentes:

* Gerenciamento de chaves
* Empacotamento de criptografia do DRM
* Entrega de licença do DRM
* Controle de acesso/seleção de direitos
* Autenticação/autorização do usuário
* Aplicativo do jogador
* Origem/CDN (rede de distribuição de conteúdo)

O diagrama a seguir ilustra a interação de alto nível entre os componentes em um subsistema de DRM:

![Subsistema DRM com CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

O design tem três camadas básicas:

* Uma camada de back office (preta), que não é exposta externamente.
* Uma camada de DMZ (azul escura), que contém todos os pontos de extremidade voltados para o público.
* Uma camada de Internet pública (azul clara) que contém a CDN e os players com tráfego na Internet pública.

Também deve haver uma ferramenta de gerenciamento de conteúdo para controlar a proteção do DRM, independentemente de sua criptografia dinâmica ou estática. As entradas para a criptografia do DRM incluem:

* Conteúdo de vídeo MBR
* Chave de conteúdo
* URLs de aquisição de licença

Este é o fluxo de alto nível durante o tempo de reprodução:

* O usuário é autenticado.
* Um token de autorização é criado para o usuário.
* O conteúdo protegido por DRM (manifesto) é baixado no player.
* O player envia uma solicitação de aquisição de licenças para servidores de licença junto com uma ID de chave e um token de autorização.

A seção a seguir descreve o design do gerenciamento de chaves.

| **ContentKey para ativo** | **Cenário** |
| --- | --- |
| 1 para 1 |O caso mais simples. Ele fornece o controle mais refinado. Mas essa disposição geralmente resulta em custo de entrega de licença mais alto. Pelo menos uma solicitação de licença é necessária para cada ativo protegido. |
| 1 para muitos |Você pode usar a mesma chave de conteúdo para vários ativos. Por exemplo, para todos os ativos em um grupo lógico, como um gênero ou o subconjunto de um gênero (ou gênero de filme), você pode usar uma única chave de conteúdo. |
| Muitos para 1 |Várias chaves de conteúdo são necessárias para cada ativo. <br/><br/>Por exemplo, se você precisar aplicar proteção CENC dinâmica com DRM múltiplo para MPEG-DASH e criptografia dinâmica AES-128 para HLS, será preciso duas chaves de conteúdo separadas. Cada chave de conteúdo precisará de sua própria ContentKeyType. (Para a chave de conteúdo usada para proteção de CENC dinâmica, use ContentKeyType.CommonEncryption. Para a chave de conteúdo usada para criptografia AES-128 dinâmica, use ContentKeyType.EnvelopeEncryption).<br/><br/>Como outro exemplo, na proteção CENC de conteúdo DASH, você poderia, teoricamente, usar uma chave de conteúdo para proteger a transmissão de vídeo e outra chave de conteúdo para proteger a transmissão de áudio. |
| Muitos para muitos |Combinação dos dois cenários anteriores. Um conjunto de chaves de conteúdo é usado para cada um dos vários ativos no mesmo grupo de ativos. |

Outro fator importante a se considerar é o uso de licenças persistentes e não persistentes.

Por que essas considerações são importantes?

Se você usa uma nuvem pública para a entrega de licenças, as licenças persistentes e não persistente têm um impacto direto no custo de entrega de licença. Os dois casos de design diferentes a seguir servem para ilustrar:

* Assinatura mensal: use uma licença persistente e o mapeamento de chaves para ativos com conteúdo de um para muitos. Por exemplo, para todos os filmes de crianças, usamos uma única chave de conteúdo para criptografia. Nesse caso:

    Número total de licenças solicitadas para todos os filmes de criança/dispositivo = 1

* Assinatura mensal: use uma licença não persistente e o mapeamento de um para um entre a chave de conteúdo e o ativo. Nesse caso:

    Número total de licenças solicitadas para todos os filmes de criança/dispositivo = [número de filmes assistidos] x [número de sessões]

Os dois designs diferentes resultam em padrões de solicitação de licença muito diferentes. Os padrões diferentes resultam em um custo de entrega de licenças diferente caso o serviço de entrega de licença seja fornecido por uma nuvem pública, como os Serviços de Mídia.

## <a name="map-design-to-technology-for-implementation"></a>Mapear design à tecnologia para implementação
Em seguida, o design genérico é mapeado para tecnologias na plataforma Azure/Serviços de Mídia especificando qual tecnologia usar para cada bloco de construção.

A tabela abaixo mostra o mapeamento.

| **Bloco de construção** | **Tecnologia** |
| --- | --- |
| **Player** |[Player de Mídia do Azure](https://azure.microsoft.com/services/media-services/media-player/) |
| **IDP (Provedor de identidade)** |Active Directory do Azure (Azure AD) |
| **STS (Serviço de Token Seguro)** |AD do Azure |
| **Fluxo de trabalho de proteção de DRM** |Proteção dinâmica dos Serviços de Mídia do Azure |
| **Entrega de licença do DRM** |* Entrega de licença dos Serviços de Mídia (PlayReady, Widevine, FairPlay) <br/>* Servidor de licença Axinom <br/>* Servidor de licença do PlayReady personalizado |
| **Origem** |Ponto de extremidade dos Serviços de Mídia do Azure |
| **Gerenciamento de chaves** |Não é necessário para a implementação de referência |
| **Gerenciamento de conteúdo** |Aplicativo do console C# |

Em outras palavras, tanto IDP quanto STS são usados pelo Microsoft Azure Active Directory. A [API do Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/) é usada para o player. Os Serviços de Mídia do Azure e o Player de Mídia do Azure são compatíveis com CENC em DASH, FairPlay em HLS, PlayReady em streaming suave e criptografia AES-128 para DASH, HLS e suave.

O diagrama a seguir mostra a estrutura geral e o fluxo com o mapeamento de tecnologia anterior:

![CENC nos Serviços de Mídia](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Para configurar a proteção de conteúdo DRM, a ferramenta de gerenciamento de conteúdo usa as entradas a seguir:

* Conteúdo aberto
* Chave de conteúdo do gerenciamento de chave
* URLs de aquisição de licença
* Uma lista de informações do Microsoft Azure Active Directory, como declarações de token, emissor e público-alvo

Esta é a saída da ferramenta de gerenciamento de conteúdo:

* O ContentKeyPolicy descreve o modelo de licença do DRM para cada tipo de DRM usado;
* O ContentKeyPolicyRestriction descreve o controle de acesso antes da emissão de uma licença do DRM
* O Streamingpolicy descreve as várias combinações de formato de contêiner - modo de criptografia - protocolo de transmissão - DRM, para streaming
* O StreamingLocator descreve a chave de conteúdo/IV usada para criptografia e as URLs de streaming 

Aqui está o fluxo durante o tempo de execução:

* Após a autenticação de usuário, um JWT é gerado.
* Uma das declarações contidas no JWT é uma declaração grupos que contém o EntitledUserGroup da ID de objeto do grupo. Esta declaração é usada para passar a verificação de autorização.
* O player baixa o manifesto do cliente do conteúdo protegido por CENC e identifica o seguinte:
   * A ID da chave.
   * O conteúdo é protegido por DRM.
   * URLs de aquisição de licença.
* O player faz uma solicitação de aquisição de licença com base no navegador/DRM compatível. Na solicitação de aquisição de licença, a ID de chave e o JWT também são enviados. O serviço de entrega de licença verifica o JWT e as declarações contidas antes de emitir a licença necessária.

## <a name="implementation"></a>Implementação
### <a name="implementation-procedures"></a>Procedimentos de implementação
A implementação inclui as seguintes etapas:

1. Preparar os ativos de teste. Codificar/empacotar um vídeo de teste para MP4 fragmentado com taxas de bits múltiplas nos Serviços de Mídia. Esse ativo *não* é protegido por DRM. A proteção por DRM é feita pela proteção dinâmica posteriormente.

2. Criar uma ID de chave e uma chave de conteúdo (opcionalmente de uma semente de chave). Nesse exemplo, o sistema de gerenciamento de chaves não é necessário porque somente uma única ID de chave e chave de conteúdo são necessárias para alguns dos ativos de teste.

3. Usar a API dos Serviços de Mídia para configurar os serviços de entrega de licença de DRM múltiplo para o ativo de teste. Se você usa servidores de licença personalizados pela sua empresa ou por fornecedores de sua empresa em vez de serviços de licença nos Serviços de Mídia, você pode ignorar esta etapa. Você pode especificar URLs de aquisição de licença na etapa em que configura a entrega de licença. A API dos Serviços de Mídia é necessária para especificar algumas configurações detalhadas, como restrição de política de autorização e modelos de resposta de licença para diferentes serviços de licença de DRM. Por enquanto, o portal do Azure não fornece a interface do usuário necessária para esta configuração. Para obter informações de nível de API e código de exemplo, consulte [Usar criptografia dinâmica comum do PlayReady e/ou Widevine](protect-with-drm.md).

4. Usar a API dos Serviços de Mídia para configurar a política de entrega de ativos para o ativo de teste. Para obter informações de nível de API e código de exemplo, consulte [Usar criptografia dinâmica comum do PlayReady e/ou Widevine](protect-with-drm.md).

5. Criar e configurar um locatário do Azure AD no Azure.

6. Criar algumas contas de usuário e grupos em seu locatário do Azure AD. Crie pelo menos um grupo "Usuário Autorizado" e adicione um usuário a esse grupo. Os usuários desse grupo passam na verificação de autorização na aquisição da licença. Os usuários que não estão nesse grupo não passam na verificação de autenticação e não podem adquirir uma licença. A associação neste grupo "Usuário Autorizado" é uma declaração grupos necessária no JWT emitido pelo Azure AD. Você especifica esse requisito de declaração na etapa em que configura os serviços de entrega de licença de DRM múltiplo.

7. Criar um aplicativo ASP.NET MVC para hospedar o player de vídeo. Esse aplicativo ASP.NET é protegido com autenticação de usuário no locatário do Azure AD. As declarações apropriadas são incluídas nos tokens de acesso obtidos após a autenticação do usuário. É recomendável a API OpenID Connect para esta etapa. Instale os seguintes pacotes NuGet:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Criar um player usando a [API do Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/). Use a [API ProtectionInfo do Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/) para especificar qual tecnologia de DRM usar em diferentes plataformas de DRM.

9. A tabela abaixo mostra a matriz de teste.

    | **DRM** | **Navegador** | **Resultado para usuário autorizado** | **Resultado para usuário não autorizado** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge ou Internet Explorer 11 no Windows 10 |Êxito |Reprovado |
    | **Widevine** |Chrome, Firefox, Opera |Êxito |Reprovado |
    | **FairPlay** |Safari no macOS      |Êxito |Reprovado |
    | **AES-128** |Navegadores mais modernos  |Êxito |Reprovado |

Para obter informações sobre como configurar o Azure AD para um aplicativo player do ASP.NET MVC, consulte [Integrar um aplicativo OWIN com base em MVC dos Serviços de Mídia do Azure com o Azure Active Directory e restringir o fornecimento da chave de conteúdo com base em declarações JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Para saber mais, confira [Autenticação de token JWT nos Serviços de Mídia do Azure e criptografia dinâmica](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Para obter informações sobre o Azure AD:

* Você pode encontrar informações de desenvolvedor no [Guia do desenvolvedor do Azure Active Directory](../../active-directory/develop/v1-overview.md).
* Você pode encontrar informações de administrador em [Administrar seu diretório de locatário do Azure AD](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Alguns problemas na implementação
Use as seguintes informações de solução de problemas para obter ajuda com problemas de implementação.

* A URL do emissor deve terminar com "/". A audiência deve ser a ID de cliente do aplicativo de player. Além disso, adicione "/" no final da URL do emissor.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    No [JWT Decoder](http://jwt.calebb.net/), você verá **aud** e **iss**, conforme mostrado no JWT:

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Adicione permissões para o aplicativo no Azure AD na guia **Configurar** do aplicativo. As permissões são necessárias para cada aplicativo, tanto para a versão local quanto para a implantada.

    ![Permissões](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* Use o emissor correto ao configurar a proteção por CENC dinâmica.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    O exemplo a seguir não funciona:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    O GUID é a ID de locatário do Azure AD. O GUID pode ser encontrado no menu pop-up **Pontos de Extremidade** no Portal do Azure.

* Conceder privilégios de declarações de associação de grupo. Verifique se o seguinte está presente no arquivo de manifesto do aplicativo do Azure AD: 

    "groupMembershipClaims": "All"    (o valor padrão é null)

* Defina o TokenType adequado ao criar requisitos de restrição.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Como você adiciona suporte para JWT (Azure AD) além de SWT (ACS), o TokenType padrão é TokenType.JWT. Se você usar SWT/ACS, deverá definir o token como TokenType.SWT.

## <a name="faqs"></a>Perguntas frequentes

Esta seção aborda alguns tópicos adicionais sobre design e implementação.

### <a name="http-or-https"></a>HTTP ou HTTPS?
O aplicativo de player ASP.NET MVC deve ser compatível com o seguinte:

* Autenticação de usuário por meio do Azure AD que está em HTTPS.
* Troca de JWT entre o cliente e o Azure AD, que está em HTTPS.
* Aquisição de licença de DRM pelo cliente, que deve estar em HTTPS se a entrega de licença é fornecida pelos Serviços de Mídia. O pacote de produtos PlayReady não faz com que HTTPS seja obrigatório para a entrega de licença. Se o servidor de licença do PlayReady está fora dos Serviços de Mídia, você pode usar HTTP ou HTTPS.

O aplicativo de player ASP.NET usa HTTPS como uma melhor prática, portanto o Player de Mídia é uma página em HTTPS. No entanto, o HTTP é preferencial para streaming, então é necessário considerar o problema do conteúdo misto.

* O navegador não permite conteúdo misto. Mas plug-ins como os do Silverlight e OSMF, para smooth e DASH, permitem. O conteúdo misto é uma preocupação de segurança devido à ameaça potencial de injeção de JavaScript mal-intencionado, o que pode fazer com que os dados do cliente fiquem em risco. Os navegadores bloqueiam essa funcionalidade por padrão. A única maneira de contornar isso é no lado do servidor (origem), por meio da permissão de todos os domínios (sejam HTTPS ou HTTP). Isso provavelmente também não será uma boa ideia.
* Evite conteúdo misto. O aplicativo de player e o Player de Mídia devem usar HTTP ou HTTPS. Durante a reprodução de conteúdo misto, a tecnologia silverlightSS exige a desmarcação de um aviso de conteúdo misto. A tecnologia flashSS lida com conteúdo misto sem um aviso de conteúdo misto.
* Se o ponto de extremidade de streaming tiver sido criado antes de agosto de 2014, ele não será compatível com HTTPS. Nesse caso, crie e use um novo ponto de extremidade de streaming para HTTPS.

Na implementação de referência, para conteúdo protegido por DRM, tanto o aplicativo quanto o streaming estão em HTTPS. Para conteúdo aberto, o player não precisa de autenticação ou de uma licença, portanto, você pode usar HTTP ou HTTPS.

### <a name="what-is-azure-active-directory-signing-key-rollover"></a>O que é a substituição de chave de assinatura do Active Directory do Azure?
A substituição de chave de assinatura é um ponto importante a ser considerado na sua implementação. Se ignorar isso, o sistema finalizado eventualmente vai parar de funcionar completamente em, no máximo, seis semanas.

O Azure AD usa padrões do setor para estabelecer confiança entre si mesmo e os aplicativos que usam o Azure AD. Especificamente, o AD do Azure usa uma chave de assinatura que consiste em um par de chaves público e privado. Quando o Azure AD cria um token de segurança que contém informações sobre o usuário, ele é assinado pelo Azure AD com uma chave privada antes de ser devolvido ao aplicativo. Para verificar se o token é válido e se foi originado no Azure AD, o aplicativo deve validar a assinatura do token. O aplicativo usa a chave pública exposta pelo Azure AD que está contida no documento de metadados de federação do locatário. Essa chave pública, e a chave de assinatura da qual ela deriva, é a mesma usada para todos os locatários no Azure AD.

Para obter mais informações sobre a substituição de chave do Azure AD, consulte [Informações importantes sobre substituição de chave de assinatura no Azure AD](../../active-directory/active-directory-signing-key-rollover.md).

Entre o [par de chaves pública/privada](https://login.microsoftonline.com/common/discovery/keys/):

* A chave privada é usada pelo Azure AD para gerar um JWT.
* A chave pública é usada por um aplicativo, como serviços de entrega de licença de DRM nos Serviços de Mídia para verificar o JWT.

Por questão de segurança, o Azure AD troca o certificado periodicamente (a cada seis semanas). No caso de violações de segurança, a substituição de chave pode ocorrer a qualquer momento. Portanto, os serviços de entrega de licença nos Serviços de Mídia precisam atualizar a chave pública usada de acordo com a troca do par de chaves do Azure AD. Caso contrário, a autenticação de token nos Serviços de Mídia falhará e nenhuma licença será emitida.

Para configurar esse serviço, você deve definir um TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument ao configurar serviços de entrega de licença do DRM.

Veja o fluxo de JWT:

* O Azure AD emite o JWT com a chave privada atual para um usuário autenticado.
* Quando um player visualiza um CENC com conteúdo protegido por DRM múltiplo, primeiro ele localiza o JWT emitido pelo Azure AD.
* O player envia uma solicitação de aquisição de licença com o JWT para os serviços de entrega de licença nos Serviços de Mídia.
* Os serviços de entrega de licença dos Serviços de Mídia usam a chave pública atual/válida do Azure AD para verificar o JWT antes de emitir licenças.

Os serviços de entrega de licença do DRM sempre verificam a chave pública válida/atual do Azure AD. A chave pública apresentada pelo Azure AD é a chave usada para verificar um JWT emitido pelo Azure AD.

E se a substituição de chave acontecer depois que o Azure AD gerar um JWT, mas antes que o JWT seja enviado pelos players para os serviços de entrega de licença de DRM nos Serviços de Mídia para verificação?

Como uma chave pode ser substituída a qualquer momento, mais de uma chave pública válida estará sempre disponível no documento de metadados de federação. A entrega de licenças dos Serviços de Mídia pode usar qualquer uma das chaves especificadas no documento. Como uma chave poderá ser substituída em breve, outra poderá ser sua substituição, e assim por diante.

### <a name="where-is-the-access-token"></a>Onde está o token de acesso?
Se você der uma olhada em como um aplicativo Web chama um aplicativo de API em [Identidade do aplicativo com concessão de credenciais de cliente OAuth 2.0](../../active-directory/develop/web-api.md), verá que o fluxo de autenticação é o seguinte:

* Um usuário entra no Azure AD no aplicativo Web. Para obter mais informações, consulte [Navegador da Web para aplicativo Web](../../active-directory/develop/web-app.md).
* O ponto de extremidade de autorização do AD do Azure redireciona o agente do usuário para o aplicativo cliente com um código de autorização. O agente do usuário retorna o código de autorização ao URI de redirecionamento do aplicativo cliente.
* O aplicativo Web precisa adquirir um token de acesso para que ele possa autenticar a API da Web e recuperar o recurso desejado. Ele faz uma solicitação ao ponto de extremidade do token do Azure AD e fornece a credencial, a ID do cliente e o URI da ID do aplicativo da API da Web. Ele apresenta o código de autorização para comprovar que o usuário consentiu.
* O Azure AD autentica o aplicativo e retorna um token de acesso JWT que é usado para chamar a API da Web.
* Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho de "Autorização" da solicitação à API da Web. Então, a API da Web valida o JWT. Se a validação for bem-sucedida, ela retornará o recurso desejado.

Nesse fluxo de identidade do aplicativo, a API da Web confia que o aplicativo Web autenticou o usuário. Por esse motivo, esse padrão é chamado de subsistema confiável. O [diagrama de fluxo de autorização](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) descreve como o fluxo concessão de código de autorização funciona.

A aquisição de licença com restrição de token segue o mesmo padrão de subsistema confiável. O serviço de entrega de licença nos Serviços de Mídia é o recurso da API da Web ou o "recurso de back-end" que o aplicativo Web precisa acessar. Onde está o token de acesso?

O token de acesso é obtido do Azure AD. Após a autenticação bem-sucedida do usuário, um código de autorização é retornado. O código de autorização é então usado, junto com a chave de aplicativo e a ID de cliente, para trocar pelo token de acesso. O token de acesso é usado para acessar um aplicativo "ponteiro" que aponta para ou representa o serviço de entrega de licença dos Serviços de Mídia.

Para registrar e configurar o aplicativo de ponteiro no Azure AD, execute as seguintes etapas:

1. No locatário do Azure AD:

   * Adicione um aplicativo (recurso) com a URL de logon https://[resource_name].azurewebsites.net/. 
   * Adicione uma ID do aplicativo com a URL https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Adicione uma nova chave para o aplicativo de recurso.

3. Atualize o arquivo de manifesto do aplicativo para que a propriedade groupMembershipClaims tenha o valor "groupMembershipClaims": "All".

4. No aplicativo do Azure AD que aponta para o aplicativo Web de player, na seção **Permissões para outros aplicativos**, adicione o aplicativo de recurso que foi adicionado na etapa 1. Em **Permissão delegada**, selecione **Acessar [resource_name]**. Essa opção concede permissão ao aplicativo Web para criar tokens de acesso que acessam o aplicativo de recurso. Faça isso para a versão local e a implantada do aplicativo Web se você desenvolve com Visual Studio e o Aplicativo Web do Azure.

O JWT emitido pelo Azure AD é o token de acesso usado para acessar o recurso de ponteiro.

### <a name="what-about-live-streaming"></a>E quanto à Transmissão ao Vivo?
A discussão anterior se concentrou em ativos sob demanda. E quanto à Transmissão ao Vivo?

Você pode usar exatamente o mesmo design e a mesma implementação para proteger a transmissão ao vivo nos Serviços de Mídia, tratando o ativo associado a um programa como um ativo de VoD.

Especificamente, para fazer transmissão ao vivo nos Serviços de Mídia, você precisa criar um canal e, em seguida, um programa nesse canal. Para criar o programa, você precisa criar um ativo que contenha o arquivo ao vivo do programa. Para fornecer proteção CENC com DRM múltiplo ao conteúdo ao vivo, aplique o mesmo processamento/configuração ao ativo, como se fosse um ativo de VoD, antes de iniciar o programa.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licença fora dos Serviços de Mídia?

Muitas vezes os clientes investiram em um farm de servidores de licença em seu próprio data center ou hospedado por provedores de serviço de DRM. Com a Proteção de Conteúdo dos Serviços de Mídia, você pode operar em modo híbrido. O conteúdo pode ser hospedado e protegido dinamicamente nos Serviços de Mídia, enquanto as licenças de DRM são fornecidas pelos servidores fora dos Serviços de Mídia. Nesse caso, considere as seguintes alterações:

* O STS precisa emitir tokens que sejam aceitáveis e possam ser verificados pelo farm de servidores de licença. Por exemplo, os servidores de licença do Widevine fornecidos pela Axinom exigem um JWT específico que contém uma mensagem de autorização. Portanto, você precisa ter um STS para emitir esse JWT. 
* Você não precisa mais configurar o serviço de entrega de licença nos Serviços de Mídia. Você precisa fornecer URLs de aquisição de licença (para o PlayReady, o Widevine e o FairPlay) ao configurar o ContentKeyPolicies.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>E se eu quiser usar um STS personalizado?
Um cliente pode optar por usar um STS personalizado para fornecer JWTs. Os motivos incluem:

* O IDP usado pelo cliente não é compatível com o STS. Nesse caso, um STS personalizado poderá ser uma opção.
* O cliente poderá precisar de controle mais flexível ou mais restrito na integração do STS com o sistema de cobrança de assinante do cliente. Por exemplo, um operador de MVPD poderá oferecer vários pacotes de assinante OTT, como Premium, básico e esportes. O operador poderá corresponder as declarações de um token com o pacote do assinante para que somente o conteúdo de um pacote específico seja disponibilizado. Nesse caso, um STS personalizado oferece a flexibilidade e o controle necessários.

Ao usar um STS personalizado, duas alterações devem ser feitas:

* Ao configurar o serviço de entrega de licença para um ativo, você precisa especificar a chave de segurança usada para verificação pelo STS personalizado em vez da chave atual do Azure AD. (Mais detalhes a seguir). 
* Quando um token JTW é gerado, uma chave de segurança é especificada em vez da chave privada do certificado X509 atual no Azure AD.

Há dois tipos de chaves de segurança:

* Chave simétrica: a mesma chave é usada para gerar e verificar um JWT.
* Chave assimétrica: um par de chaves pública/privada em um certificado X509 é usado com uma chave privada para criptografar/gerar um JWT e, com a chave pública, para verificar o token.

> [!NOTE]
> Se você usa o .NET Framework /C# como sua plataforma de desenvolvimento, o certificado X509 usado para uma chave assimétrica de segurança deve ter um comprimento de chave de pelo menos 2048. Este é um requisito da classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey no .NET Framework. Caso contrário, a seguinte exceção é lançada:
> 
> IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' para a assinatura não pode ter menos de '2048' bits.

## <a name="the-completed-system-and-test"></a>Sistema concluído e teste
Esta seção orienta nas seguintes situações do sistema de ponta a ponta concluído para que você possa ter uma ideia básica do comportamento antes de obter uma conta de conexão:

* Se você precisa de um cenário não integrado:

    * Para ativos de vídeo hospedados nos Serviços de Mídia que sejam desprotegidos ou protegidos por DRM, mas sem autenticação de token (emitindo uma licença para qualquer pessoa que tenha solicitado), você poderá testá-los sem precisar entrar. Mude para HTTP se o streaming de vídeo for via HTTP.

* Se você precisa de um cenário de ponta a ponta integrado:

    * Para os ativos de vídeo sob proteção de DRM dinâmica nos Serviços de Mídia com a autenticação de token e o JWT gerado pelo Azure AD, você precisa entrar.

Para o aplicativo Web de player e sua entrada, consulte [este site](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Entrada do usuário
Para testar o sistema DRM integrado de ponta a ponta, você precisa ter uma conta criada ou adicionada.

Qual conta?

Embora o Azure originalmente permitisse acesso somente por usuários de contas da Microsoft, agora o acesso é permitido por usuários de ambos os sistemas. Todas as propriedades do Azure agora confiam no Azure AD para autenticação e o Azure AD autentica usuários organizacionais. Uma relação de federação foi criada, em que o Azure AD confia no sistema de identidade do consumidor de conta da Microsoft para autenticar usuários consumidores. Como resultado, o Azure AD é capaz de autenticar contas de convidado da Microsoft e também contas nativas do Azure AD.

Como o Azure AD confia no domínio da conta da Microsoft, você pode adicionar contas de qualquer um dos seguintes domínios ao locatário do Azure AD personalizado e usar a conta para entrar:

| **Nome de domínio** | **Domínio** |
| --- | --- |
| **Domínio de locatário do AD do Azure personalizado** |somename.onmicrosoft.com |
| **Domínio corporativo** |microsoft.com |
| **Domínio da conta da Microsoft** |outlook.com, live.com, hotmail.com |

Entre em contato com qualquer um dos autores para que criem ou adicionem uma conta para você.

As capturas de tela a seguir mostram as diferentes páginas de entrada usadas por diferentes contas de domínio:

**Conta de domínio do locatário do AD do Azure personalizada**: a página de entrada personalizada do domínio de locatário do AD do Azure personalizado.

![Conta de domínio do locatário do AD do Azure personalizada 1](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Conta de domínio da Microsoft com cartão inteligente**: a página de entrada personalizada pela TI corporativa da Microsoft com autenticação de dois fatores.

![Conta de domínio do locatário do AD do Azure personalizada 2](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Conta Microsoft**: a página de entrada da conta Microsoft para consumidores.

![Conta de domínio do locatário do AD do Azure personalizada 3](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Usar extensões de mídia criptografada para PlayReady
Em um navegador moderno compatível com EME (extensões de mídia criptografada) para PlayReady, como o Internet Explorer 11 no Windows 8.1 ou posteriores, e o navegador Microsoft Edge no Windows 10, o PlayReady é o DRM subjacente para EME.

![Usar EME para PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

A área escura do player é porque a proteção do PlayReady evita que você capture a tela de um vídeo protegido.

A captura de tela a seguir mostra os plug-ins do player e o suporte do MSE (Microsoft Security Essentials)/EME:

![Plug-ins do player para PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

A EME no Microsoft Edge e no Internet Explorer 11 no Windows 10 permite que o [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) seja invocado em dispositivos Windows 10 compatíveis. O PlayReady SL3000 desbloqueia o fluxo de conteúdo Premium avançado (4K, HDR) e novos modelos de distribuição de conteúdo (para conteúdo avançado).

Para concentrar-se nos dispositivos Windows, o PlayReady é o único DRM no hardware disponível em dispositivos Windows (PlayReady SL3000). Um serviço de streaming pode usar o PlayReady por meio da EME ou de um aplicativo da Plataforma Universal do Windows usando o PlayReady SL3000 e oferecer uma qualidade de vídeo mais alta do que com o uso de outro DRM. Normalmente, conteúdo de até 2K flui no Chrome ou no Firefox e conteúdo de até 4K flui no Microsoft Edge/Internet Explorer 11 ou em um aplicativo da Plataforma Universal do Windows no mesmo dispositivo. O valor dependerá da implementação e das configurações de serviço.

#### <a name="use-eme-for-widevine"></a>Usar EME para Widevine
Em um navegador moderno com suporte a EME/Widevine, como Chrome 41+ no Windows 10, Windows 8.1, Mac OSX Yosemite e Chrome no Android 4.4.4, o Google Widevine é o DRM por trás do EME.

![Usar EME para Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

O Widevine não impede que você faça captura de tela de vídeo protegido.

![Plug-ins de player para Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Usar EME para FairPlay
Da mesma forma, você pode testar o conteúdo do FairPlay protegido deste reprodutore de teste no Safari no macOS ou iOS 11.2 e posterior.

Verifique se você coloca "FairPlay" como protectionInfo.type e coloca na URL correta para seu certificado de aplicativo no caminho de CA FPS (FairPlay Streaming Application Certificate Path).

### <a name="unentitled-users"></a>Usuários não autorizados
Se um usuário não for um membro do grupo "Usuários Autorizados", ele não passará na verificação de autorização. Então, o serviço de licença de DRM múltiplo se recusará a emitir a licença solicitada conforme mostrado. A descrição detalhada será "Falha na aquisição de licença", o que está de acordo com o design.

![Usuários não autorizados](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Executar um serviço de token de segurança personalizado
Se você executar um STS personalizado, o JWT será emitido pelo STS personalizado usando uma chave simétrica ou assimétrica.

A captura de tela a seguir mostra um cenário que usa uma chave simétrica (usando o Chrome):

![STS personalizado com uma chave simétrica](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

A captura de tela a seguir mostra um cenário que usa uma chave assimétrica por meio de um certificado X509 (usando um navegador moderno da Microsoft):

![STS personalizado com uma chave assimétrica](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

Nos dois casos anteriores, a autenticação do usuário permanece a mesma. Ela ocorre por meio do Azure AD. A única diferença é que os JWTs são emitidos pelo STS personalizado em vez do Azure AD. Ao configurar a proteção por CENC dinâmica, a restrição do serviço de entrega de licença especifica o tipo de JWT, sendo uma chave simétrica ou assimétrica.

## <a name="summary"></a>Resumo
Este documento discutiu a proteção de conteúdo usando 3 DRMs e o controle de acesso através da autenticação do token, seu projeto e sua implementação usando o Azure, Serviços de Mídia do Azure o Player de Mídia do Azure.

* Um design de referência foi apresentado, contendo todos os componentes necessários em um subsistema DRM.
* Uma implementação de referência foi apresentada no Azure, os serviços de mídia do Azure e o Player de Mídia do Azure.
* Alguns tópicos envolvidos diretamente no design e na implementação também foram analisados.

## <a name="next-steps"></a>Próximas etapas

[Proteger o conteúdo com o DRM](protect-with-drm.md)
