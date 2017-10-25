---
title: "CENC com Vários DRMs e Controle de Acesso: um Design e Implementação de Referência no Azure e nos Serviços de Mídia do Azure | Microsoft Docs"
description: "Saiba mais sobre como licenciar o Kit de Portabilidade de Cliente do Microsoft® Smooth Streaming."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 7814739b-cea9-4b9b-8370-538702e5c615
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;kilroyh;yanmf;juliako
ms.openlocfilehash: 730917b6859f8dbd800ef2cb141062f45d7779ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC com vários DRM e Controle de Acesso: design e implementação de referência no Azure e nos Serviços de Mídia do Azure
 
## <a name="introduction"></a>Introdução
Todos sabem que é uma tarefa complexa projetar e criar um subsistema DRM para OTT ou solução de streaming online. E uma prática comum usada pelos provedores/operadores de vídeo online é a terceirização desta parte para provedores de serviço DRM especializados. O objetivo deste documento é apresentar um design e uma implementação de referência do subsistema DRM ponta a ponta em OTT ou solução de streaming online.

Este documento se destina a engenheiros que trabalham no subsistema DRM de OTT ou de soluções multitela/streaming online ou a leitores interessados no subsistema DRM. Pressupõe-se que os leitores estejam familiarizados com pelo menos uma das tecnologias DRM do mercado, como o PlayReady, o Widevine, o FairPlay ou o Adobe Access.

Em relação a DRM, também incluímos CENC (criptografia comum) com vários DRM. Uma das principais tendências do setor de streaming online e OTT é usar CENC com vários DRM nativos em várias plataformas clientes, o que é uma mudança na tendência anterior de usar um único DRM e seu SDK do cliente para várias plataformas clientes. Quando você usa CENC com vários DRM nativos, tanto o PlayReady quanto o Widevine são criptografados de acordo com a especificação [Criptografia comum (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Os benefícios da CENC com vários DRM são os seguintes:

1. Reduz o custo de criptografia, já que um único processo de criptografia é usado para diferentes plataformas e seus DRMs nativos;
2. Reduz o custo de gerenciamento de ativos criptografados, pois é necessária apenas uma única cópia dos ativos criptografados;
3. Elimina o custo de licenciamento do cliente DRM, já que o cliente DRM nativo normalmente é gratuito na plataforma nativa.

A Microsoft sempre promoveu ativamente DASH e CENC juntamente com alguns principais vetores da indústria. Os Serviços de Mídia do Microsoft Azure dão suporte a DASH e a CENC. Para obter comunicados recentes, confira os blogs da Mingfei: [Announcing Google Widevine license delivery services in Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/) (Anunciando os serviços de entrega da licença Google Widevine nos Serviços de Mídia do Azure) e [Azure Media Services adds Google Widevine packaging for delivering multi-DRM stream](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) (Serviços de Mídia do Azure adiciona pacote do Google Widevine para fornecer fluxo de vários DRMs).  

### <a name="overview-of-this-article"></a>Visão geral deste artigo
O objetivo deste artigo inclui o seguinte:

1. Fornece um design de referência do subsistema DRM usando CENC com vários DRM;
2. Fornece uma implementação de referência na plataforma Microsoft Azure/Serviços de Mídia do Azure;
3. Analisa alguns tópicos de design e implementação.

No artigo, "várias DRM" abordam o seguinte:

1. Microsoft PlayReady
2. Google Widevine
3. Apple FairPlay 

A tabela a seguir resume o aplicativo nativo/plataforma nativa e navegadores com suporte de cada DRM.

| **Plataforma cliente** | **Suporte nativo ao DRM** | **Navegador/aplicativo** | **Formatos de streaming** |
| --- | --- | --- | --- |
| **Smart TVs, STBs de operador, STBs OTT** |Basicamente PlayReady e/ou Widevine e/ou outros |Linux, Opera, WebKit, outros |Vários formatos |
| **Dispositivos com Windows 10 (computadores Windows, tablets Windows, Windows Phone, Xbox)** |PlayReady |MS Edge/IE11/EME<br/><br/><br/>UWP |DASH (No HLS, o PlayReady não tem suporte)<br/><br/>DASH, Smooth Streaming (No HLS, o PlayReady não tem suporte) |
| **Dispositivos com Android (telefone, tablet, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), clientes OS X e Apple TV** |FairPlay |Safari 8+/EME |HLS |


Considerando o estado atual da implantação para cada DRM, um serviço geralmente desejará implementar 2 ou 3 DRMs para se certificar de que você resolva todos os tipos de ponto de extremidade da melhor maneira.

Há um equilíbrio entre a complexidade da lógica do serviço e a complexidade no lado do cliente para atingir um determinado nível de experiência do usuário em vários clientes.

Para fazer sua seleção, lembre-se destes pontos:

* O PlayReady é nativamente implementado em cada dispositivo com Windows, em alguns dispositivos com Android e está disponível em SDKs de software em praticamente qualquer plataforma
* O Widevine é nativamente implementado em cada dispositivo com Android, Chrome e alguns outros dispositivos
* O FairPlay está disponível apenas em clientes iOS e Mac OS, ou no iTunes.

Portanto, um típico DRM variado seria:

* Opção 1: PlayReady e Widevine
* Opção 2: PlayReady, Widevine e FairPlay

## <a name="a-reference-design"></a>Um design de referência
Nesta seção, apresentaremos um design de referência que é indiferente às tecnologias usadas para implementá-lo.

Um subsistema DRM pode conter os seguintes componentes:

1. Gerenciamento de chaves
2. Empacotamento de DRM
3. Entrega de licença do DRM
4. Verificação de autorização
5. Autenticação/autorização
6. Jogador
7. Origem/CDN

O diagrama a seguir ilustra a interação de alto nível entre os componentes em um subsistema DRM.

![Subsistema DRM com CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Há três "camadas" básicas no design:

1. A camada de back-office (em preto) que não é exposta externamente;
2. A camada "DMZ" (azul) que contém todos os pontos de extremidade voltados para o público;
3. A camada de Internet pública (azul-clara) que contém CDN e players com tráfego pela Internet pública.

Deve haver uma ferramenta de gerenciamento de conteúdo para controlar a proteção do DRM, independentemente de a criptografia ser dinâmica ou estática. As entradas para criptografia de DRM devem incluir:

1. Conteúdo de vídeo MBR;
2. Chave de conteúdo;
3. URLs de aquisição de licença.

Durante o tempo de reprodução, o fluxo de alto nível é:

1. O usuário é autenticado;
2. O token de autorização é criado para o usuário.
3. O conteúdo protegido por DRM (manifesto) é baixado para o player;
4. O player envia a solicitação de aquisição de licenças para servidores de licença junto com a ID de chave e o token de autorização.

Antes de passar para o próximo tópico, algumas palavras sobre o design do gerenciamento de chaves.

| **ContentKey-para-ativo** | **Cenário** |
| --- | --- |
| 1 para 1 |O caso mais simples. Ele fornece o controle mais refinado. Mas isso geralmente resulta no custo de entrega de licença mais alto. Pelo menos uma solicitação de licença é necessária para cada ativo protegido. |
| 1-para-muitos |Você pode usar a mesma chave de conteúdo para vários ativos. Por exemplo, para todos os ativos em um grupo lógico, como um gênero ou um subconjunto de gênero (ou Gênero de Filme), você pode usar uma única chave de conteúdo. |
| Muitos-para-1 |Várias chaves de conteúdo são necessárias para cada ativo. <br/><br/>Por exemplo, se você precisar aplicar proteção CENC dinâmica com vários DRMs para MPEG-DASH e criptografia dinâmica AES-128 para HLS, será preciso duas chaves de conteúdo separadas, cada uma com seu próprio ContentKeyType. (Para a chave de conteúdo usada na proteção CENC dinâmica, é preciso usar ContentKeyType.CommonEncryption; já para a chave de conteúdo usada na criptografia dinâmica AES-128, é preciso usar ContentKeyType.EnvelopeEncryption.)<br/><br/>Outro exemplo: na proteção CENC de conteúdo DASH, teoricamente, uma chave de conteúdo pode ser usada para proteger a transmissão de vídeo e outra chave de conteúdo para proteger a transmissão de áudio. |
| Muitos-para-muitos |Combinação dos dois cenários acima: um conjunto de chaves de conteúdo é usado para cada um dos vários ativos no mesmo "grupo" de ativos. |

Outro fator importante a se considerar é o uso de licenças persistentes e não persistentes.

Por que essas considerações são importantes?

Elas têm impacto direto no custo de entrega de licença se você usa a nuvem pública para entrega de licença. Vamos considerar os dois casos de design diferentes abaixo para ilustrar:

1. Assinatura mensal: use a licença persistente e o mapeamento de chaves para ativos com conteúdo de um para muitos. Por exemplo para todos os filmes de crianças, usamos uma única chave de conteúdo para criptografia. Nesse caso:

    Número total de licenças solicitadas para todos os filmes de criança/dispositivo = 1
2. Assinatura mensal: use a licença não persistente e o mapeamento de um para um entre a chave e o ativo do conteúdo. Nesse caso:

    Número total de licenças solicitadas para todos os filmes de criança/dispositivo = [# de filmes observados] x [# de sessões]

Como você pode ver facilmente, os dois designs diferentes resultam em padrões de solicitação de licença muito diferentes e, consequentemente, em custos de entrega de licença diferentes se o serviço de entrega é feito por nuvem pública, como os Serviços de Mídia do Azure.

## <a name="mapping-design-to-technology-for-implementation"></a>Mapeando design à tecnologia para implementação
Em seguida, podemos mapear nosso design genérico para tecnologias na plataforma Microsoft Azure/Serviços de Mídia do Azure especificando qual tecnologia usar para cada bloco de construção.

A tabela abaixo mostra o mapeamento:

| **Bloco de construção** | **Tecnologia** |
| --- | --- |
| **Player** |[Player de Mídia do Azure](https://azure.microsoft.com/services/media-services/media-player/) |
| **IDP (Provedor de identidade)** |Azure Active Directory |
| **STS (Serviço de Token Seguro)** |Azure Active Directory |
| **Fluxo de trabalho de proteção de DRM** |Proteção dinâmica dos Serviços de Mídia do Azure |
| **Entrega de licença do DRM** |1. Entrega de licença dos Serviços de Mídia do Azure (PlayReady, Widevine, FairPlay) ou <br/>2. Servidor de licença Axinom, <br/>3. Servidor de licença do PlayReady personalizado |
| **Origem** |Ponto de extremidade dos Serviços de Mídia do Azure |
| **Gerenciamento de Chaves** |Não é necessário para a implementação de referência |
| **Gerenciamento de Conteúdo** |Aplicativo do console C# |

Em outras palavras, tanto o IDP (provedor de identidade) quanto o STS (Serviço de Token Seguro) serão do AD do Azure. Para o player, usaremos a [API do Player de Mídia do Azure](http://amp.azure.net/libs/amp/latest/docs/). Os Serviços de Mídia do Azure e o Player de Mídia do Azure dão suporte a DASH e a CENC com vários DRM.

O diagrama a seguir mostra a estrutura geral e um fluxo com o mapeamento de tecnologia acima.

![CENC em AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Para configurar a criptografia CENC dinâmica, a ferramenta de gerenciamento de conteúdo usará as seguintes entradas:

1. Abrir conteúdo;
2. Chave de conteúdo do gerenciamento/geração de chave;
3. URLs de aquisição de licença;
4. Uma lista de informações do AD do Azure.

A saída da ferramenta de gerenciamento de conteúdo será:

1. ContentKeyAuthorizationPolicy contendo a especificação de como a entrega de licença verifica um token JWT e as especificações de licença do DRM;
2. AssetDeliveryPolicy contendo especificações de formato de streaming, proteção DRM e URLs de aquisição de licença.

Durante a execução, o fluxo é o seguinte:

1. Após a autenticação de usuário, um token JWT é gerado;
2. Uma das declarações contidas no token JWT é a declaração "grupos" que contém a ID de objeto do grupo de "EntitledUserGroup". Essa declaração será usada para a passagem de "verificação de autorização".
3. O player baixa o manifesto do cliente de um conteúdo protegido por CENC e "vê" o seguinte:

   1. ID da chave;
   2. o conteúdo é protegido por CENC;
   3. URLs de aquisição de licença.
4. O player faz uma solicitação de aquisição de licença baseado no navegador/DRM com suporte. Na solicitação de aquisição de licença, a ID de chave e o token JWT também serão enviados. O serviço de entrega de licença verificará o token JWT e as declarações contidas antes de emitir a licença necessária.

## <a name="implementation"></a>Implementação
### <a name="implementation-procedures"></a>Procedimentos de implementação
A implementação incluirá as seguintes etapas:

1. Preparar os ativos de teste: criptografar/empacotar um vídeo de teste para MP4 fragmentado com várias taxas de bits nos Serviços de Mídia do Azure. Esse ativo NÃO é protegido por DRM. A proteção DRM será feita pela proteção dinâmica posteriormente.
2. Criar chave de ID e chave de conteúdo (opcionalmente da semente de chave). Para nosso objetivo, o sistema de gerenciamento de chaves não é necessário, pois estamos lidando com apenas um único conjunto de ID de chave ID e de chave de conteúdo para alguns recursos de teste;
3. Usar a API do AMS para configurar os serviços de entrega de licença de vários DRM para o ativo de teste. Se você estiver usando servidores de licenças personalizados por sua empresa ou por fornecedores em vez de serviços de licença dos Serviços de Mídia do Azure, poderá ignorar esta etapa e especificar URLs de aquisição de licença na etapa de configuração de entrega de licença. A API do AMS é necessária para especificar algumas configurações detalhadas, como restrição de política de autorização, modelos de resposta de licença para diferentes serviços de licença de DRM, etc. Neste momento, o portal do Azure não fornece ainda a interface do usuário necessária para esta configuração. Você pode encontrar informações e códigos de exemplo no nível de API no documento de Julia Kornich: [Usando a criptografia dinâmica comum do PlayReady e/ou do Widevine](media-services-protect-with-drm.md).
4. Usar a API de AMS para configurar a política de fornecimento de ativos para o ativo de teste. Você pode encontrar informações e códigos de exemplo no nível de API no documento de Julia Kornich: [Usando a criptografia dinâmica comum do PlayReady e/ou do Widevine](media-services-protect-with-drm.md).
5. Criar e configurar um locatário do Active Directory do Azure no Azure;
6. Criar algumas contas de usuário e grupos em seu locatário do Active Directory do Azure: você deve criar pelo menos o grupo "EntitledUser" e adicionar um usuário a esse grupo. Os usuários nesse grupo passarão na verificação de autorização da aquisição de licenças, e os usuários fora desse grupo falharão na verificação de autenticação e não poderão adquirir licença. Ser membro do grupo "EntitledUser" é uma declaração "grupos" necessária no token JWT emitido pelo AD do Azure. Esse requisito de declaração deve ser especificado na etapa de configuração dos serviços de entrega de licença para vários DRM.
7. Crie um aplicativo MVC ASP.NET que hospedará o player de vídeo. Esse aplicativo ASP.NET será protegido com autenticação de usuário no locatário do Active Directory do Azure. As declarações apropriadas serão incluídas nos tokens de acesso obtidos após a autenticação do usuário. A API Connect OpenID é recomendada para esta etapa. Você precisa instalar os seguintes pacotes NuGet:
   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
8. Crie um player usando a [API do Player de Mídia do Azure](http://amp.azure.net/libs/amp/latest/docs/). [API ProtectionInfo do Player de Mídia do Azure](http://amp.azure.net/libs/amp/latest/docs/) permite que você especifique qual tecnologia DRM usar em diferentes plataformas DRM.
9. Matriz de teste:

| **DRM** | **Navegador** | **Resultado de usuário qualificado** | **Resultado para usuário não qualificado** |
| --- | --- | --- | --- |
| **PlayReady** |MS Edge ou IE11 no Windows 10 |Êxito |Reprovado |
| **Widevine** |Chrome no Windows 10 |Êxito |Reprovado |
| **FairPlay** |TBD | | |

George Trifonov, da equipe de Serviços de Mídia do Azure, escreveu um blog fornecendo as etapas detalhadas de configuração do Active Directory do Azure para um aplicativo de player MVC do ASP.NET: [Integrate Azure Media Services OWIN MVC based app with Azure Active Directory and restrict content key delivery based on JWT claims](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

George também escreveu um blog sobre [JWT token Authentication in Azure Media Services and Dynamic Encryption](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/). E aqui está seu [exemplo de integração do AD do Azure à distribuição de chaves dos Serviços de Mídia do Azure](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/).

Para obter informações sobre o Active Directory do Azure:

* Você pode encontrar informações de desenvolvedor no [Guia do desenvolvedor do Active Directory do Azure](../active-directory/active-directory-developers-guide.md).
* Você pode encontrar informações de administrador em [Administrar seu diretório do AD do Azure](../active-directory/active-directory-administer.md).

### <a name="some-gotchas-in-implementation"></a>Algumas pegadinhas na implementação
Há algumas "pegadinhas" na implementação. Esperamos que a lista de “pegadinhas” a seguir possa ajudar você a solucionar problemas caso eles apareçam.

1. A URL do **emissor** deve terminar com **"/"**.  

    **Audience** deve ser a ID de cliente do aplicativo de player; você também deve adicionar **"/"** no fim da URL do emissor.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    Em [JWT Decoder](http://jwt.calebb.net/), você deve ver **aud** e **iss** como mostrado abaixo no token JWT:

    ![1ª pegadinha](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)
2. Adicione permissões para o aplicativo no AAD (na guia Configurar do aplicativo). Isso é necessário para cada aplicativo (versões locais e implantadas).

    ![2ª pegadinha](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)
3. Use o emissor correto na configuração de proteção CENC dinâmica:

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Os itens a seguir não funcionarão:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    O GUID é a ID de locatário do AAD. O GUID pode ser encontrado no pop-up Pontos de Extremidade no portal do Azure.
4. Conceder privilégios de declarações de associação de grupo. Verifique se temos o seguinte no arquivo de manifesto de aplicativo do AAD

    "groupMembershipClaims": "All",    (o valor padrão é null)
5. Definir o TokenType apropriado durante a criação de requisitos de restrição.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Com a adição do suporte de JWT (AAD) além de SWT (ACS), o TokenType padrão é TokenType.JWT. Se você usar SWT/ACS, deverá definir como TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Tópicos Adicionais para Implementação
Em seguida, iremos analisar alguns tópicos adicionais em nosso design e implementação.

### <a name="http-or-https"></a>HTTP ou HTTPS?
O aplicativo de player MVC ASP.NET que criamos deve dar suporte ao seguinte:

1. Autenticação de usuário por meio do AD do Azure que precisa estar em HTTPS;
2. Troca de token JWT entre o cliente e o AD do Azure que precisa estar em HTTPS;
3. Aquisição de licença do DRM pelo cliente que precisa estar em HTTPS se a entrega da licença for fornecida pelos Serviços de Mídia do Azure. Obviamente, o pacote de produtos PlayReady não obriga HTTPS para entrega de licença. Se o servidor de licença do PlayReady estiver fora dos Serviços de Mídia do Azure, HTTP ou HTTPS poderão ser usados.

Portanto, o aplicativo player ASP.NET usará o HTTPS como uma prática recomendada. Isso significa que o Player de Mídia do Azure estará em uma página em HTTPS. No entanto, para streaming, preferimos HTTP; portanto, precisamos considerar a questão do conteúdo misto.

1. O navegador não permite conteúdo misto. Mas os plug-ins como o plug-in do Silverlight e do OSMF para smooth e DASH, sim. O conteúdo misto é uma preocupação de segurança; isso é devido a ameaça potencial de se injetar JS mal-intencionado, o que pode fazer com que os dados do cliente fiquem em risco.  Os navegadores bloqueiam isso por padrão e, até agora, a única maneira de contorná-lo é no lado do servidor (origem), para permitir todos os domínios (independentemente de ser https ou http). Isso provavelmente também não será uma boa ideia.
2. Devemos evitar conteúdo misto: ou ambos usam HTTP, ou ambos usam HTTPS. Durante a reprodução de conteúdo misto, a tecnologia silverlightSS exige a desmarcação de um aviso de conteúdo misto. A tecnologia flashSS lida com conteúdo misto sem aviso de conteúdo misto.
3. Se o ponto de extremidade de streaming foi criado antes de agosto de 2014, ele não dá suporte a HTTPS. Nesse caso, crie e use um novo ponto de extremidade de streaming para HTTPS.

Na implementação de referência, para conteúdo protegido por DRM, tanto o aplicativo quanto o streaming serão em HTTPS. Para conteúdo aberto, o player não precisa de autenticação ou licença; portanto, você tem a liberdade de usar HTTP ou HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Substituição de chave de assinatura do Active Directory do Azure
Isso é um ponto importante a se levar em consideração na sua implementação. Se você não considerar isso em sua implementação, o sistema concluído eventualmente deixará de funcionar completamente dentro de no máximo seis semanas.

O AD do Azure usa o padrão da indústria para estabelecer a confiança entre ele e os aplicativos que usam o AD do Azure. Especificamente, o AD do Azure usa uma chave de assinatura que consiste em um par de chaves público e privado. Quando o AD do Azure cria um token de segurança com informações sobre o usuário, esse token é assinado pelo AD do Azure usando sua chave privada antes de ser enviado para o aplicativo. Para verificar se o token é válido e realmente se origina do AD do Azure, o aplicativo deve validar a assinatura do token usando a chave pública exposta pelo AD do Azure que está contida no documento de metadados da federação do locatário. Essa chave pública, e a chave de assinatura da qual deriva, é a mesma usada para todos os locatários no AD do Azure.

Informações detalhadas sobre a substituição de chave do AD do Azure podem ser encontradas no documento: [Informações importantes sobre substituição de chaves de assinatura no AD do Azure](../active-directory/active-directory-signing-key-rollover.md).

Entre o [par de chaves pública/privada](https://login.microsoftonline.com/common/discovery/keys/),

* A chave privada é usada pelo Active Directory do Azure para gerar um token JWT;
* A chave pública é usada por um aplicativo, como os Serviços de Entrega de Licença DRM no AMS para verificar o token JWT;

Para fins de segurança, o Active Directory do Azure gira esse certificado periodicamente (a cada 6 semanas). No caso de violações de segurança, a substituição de chave pode ocorrer a qualquer momento. Portanto, os serviços de entrega de licença do AMS precisam atualizar a chave pública usada, já que o AD do Azure gira o par de chaves; caso contrário, a autenticação do token no AMS falhará e nenhuma licença será emitida.

Isso é feito definindo TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument ao configurar serviços de distribuição de licença do DRM.

O fluxo do token JWT é o seguinte:

1. O AD do Azure emite o token JWT com a chave privada atual para um usuário autenticado;
2. Quando um player visualiza um CENC com conteúdo protegido por vários DRM, primeiro ele localiza o token JWT emitido pelo AD do Azure.
3. O player envia a solicitação de aquisição de licença com o token JWT para os serviços de entrega de licença no AMS;
4. Os serviços de entrega de licença do AMS usarão a chave pública atual/válida do AD do Azure para verificar o token JWT antes de emitir licenças.

Os serviços de entrega de licença do DRM sempre verificarão a chave pública válida/atual do AD do Azure. A chave pública apresentada pelo AD do Azure será a chave usada para verificar um token JWT emitido pelo AD do Azure.

E se a substituição de chave acontece depois que o AAD gera um token JWT, mas antes do JWT token ser enviado pelos players para os serviços de entrega de licença DRM no AMS para verificação?

Como uma chave pode ser substituída a qualquer hora, há sempre mais de uma chave pública válida disponível no documento de metadados de federação. A entrega de licença dos Serviços de Mídia do Azure pode usar qualquer uma das chaves especificadas no documento, já que uma das chaves pode ser substituída em breve, a outra pode ser a sua substituta e assim por diante.

### <a name="where-is-the-access-token"></a>Onde está o Token de Acesso?
Se você der uma olhada em como um aplicativo Web chama um aplicativo de API em [Identidade do aplicativo com concessão de credenciais de cliente OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api), verá que o fluxo de autenticação é o seguinte:

1. Um usuário está conectado ao AD do Azure no aplicativo Web (confira [Navegador da Web para o aplicativo Web](../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application)).
2. O ponto de extremidade de autorização do AD do Azure redireciona o agente do usuário para o aplicativo cliente com um código de autorização. O agente do usuário retorna o código de autorização ao URI de redirecionamento do aplicativo cliente.
3. O aplicativo Web precisa adquirir um token de acesso para que ele possa autenticar a API da Web e recuperar o recurso desejado. Ele faz uma solicitação ao ponto de extremidade do token do Azure AD, fornecendo a credencial, a ID do cliente e o URI de ID do aplicativo da API da Web. Ele apresenta o código de autorização para comprovar que o usuário consentiu.
4. O Azure AD autentica o aplicativo e retorna um token de acesso JWT que é usado para chamar a API da Web.
5. Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho de autorização da solicitação à API da Web. A API da Web, em seguida, valida o token JWT e, se a validação for bem-sucedida, retorna o recurso desejado.

Nesse fluxo de "identidade do aplicativo", a API Web confia que o aplicativo Web autenticou o usuário. Por esse motivo, esse padrão é chamado de subsistema confiável. O [diagrama nesta página](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) descreve como funciona o fluxo de concessão do código de autorização.

Na aquisição de licença com restrição de token, seguimos o mesmo padrão de subsistema confiável. E o serviço de entrega de licença nos Serviços de Mídia do Azure é o recurso de API Web, o "recurso de back-end" que o aplicativo Web precisa acessar. Onde está o token de acesso?

Na verdade, podemos obter token de acesso do AD do Azure. Após a autenticação bem-sucedida do usuário, o código de autorização é retornado. O código de autorização é então usado, junto com a chave de aplicativo e a ID de cliente, para trocar pelo token de acesso. E o token de acesso serve para acessar um aplicativo de "ponteiro" que aponta ou representa os serviços de entrega de licença dos Serviços de Mídia do Azure.

Precisamos registrar e configurar o aplicativo "ponteiro" no AD do Azure seguindo as etapas abaixo:

1. No locatário do AD do Azure

   * adicione um aplicativo (recurso) com a URL de logon:

   https://[nome_do_recurso].azurewebsites.net/ e

   * URL da ID do aplicativo:

   https://[nome_do_locatário_aad].onmicrosoft.com/[nome_do_recurso];
2. Adicionar uma nova chave para o aplicativo de recurso;
3. Atualize o arquivo de manifesto do aplicativo para que a propriedade groupMembershipClaims tenha o seguinte valor: "groupMembershipClaims": "All",  
4. No aplicativo Azure AD que aponta para o aplicativo Web player, na seção "permissões para outros aplicativos", adicione o aplicativo de recurso que foi adicionado na etapa 1 acima. Em "permissões delegadas", verifique a marca de seleção de "Acessar [nome_recurso]". Isso dá ao aplicativo Web permissão para criar tokens de acesso a fim de acessar o aplicativo de recurso. Você deverá fazer isso para a versão local e a implantada do aplicativo Web se estiver desenvolvendo com Visual Studio e aplicativo Web do Azure.

Portanto, o token JWT emitido pelo AD do Azure é, de fato, o token de acesso para acessar esse recurso de "ponteiro".

### <a name="what-about-live-streaming"></a>E quanto à Transmissão ao Vivo?
No ponto acima, nossa discussão tem se concentrado em ativos sob demanda. E quanto à Transmissão ao Vivo?

A boa notícia é que você pode usar exatamente o mesmo design e a mesma implementação para proteger a transmissão ao vivo nos Serviços de Mídia do Azure, tratando o ativo associado a um programa como um "ativo VOD".

Mais especificamente, é bem sabido que, para fazer transmissão ao vivo nos Serviços de Mídia do Azure, você precisa criar um canal e um programa nesse canal. Para criar o programa, você precisa criar um ativo que contém o arquivo ao vivo do programa. Para fornecer proteção CENC com vários DRM ao conteúdo ao vivo, tudo o que você precisa fazer é aplicar o mesmo processamento/configuração de instalação para o ativo como se fosse um "ativo VOD" antes de iniciar o programa.

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>E os servidores de licença fora dos Serviços de Mídia do Azure?
Muitas vezes os clientes investiram em um farm de servidores de licença em seu próprio data center ou hospedado por provedores de serviço DRM. Felizmente, a Proteção de Conteúdo dos Serviços de Mídia do Azure permite que você opere no modo híbrido: conteúdo hospedado e dinamicamente protegido nos Serviços de Mídia do Azure enquanto as licenças DRM são fornecidas por servidores fora dos Serviços de Mídia do Azure. Nesse caso, as seguintes considerações sobre alterações devem ser feitas:

1. O Serviço de Token seguro precisa emitir tokens que podem ser aceitos e verificados pelo farm de servidores de licença. Por exemplo, os servidores de licença do Widevine fornecidos pela Axinom exigem um token JWT específico que contém "mensagem de autorização". Portanto, você precisa ter um STS para emitir tal token JWT. Os autores concluíram uma implementação do tipo, e você pode encontrar os detalhes no seguinte documento do [Centro de Documentação do Azure](https://azure.microsoft.com/documentation/): [Usando o Axinom para fornecer licenças Widevine para os Serviços de Mídia do Azure](media-services-axinom-integration.md).
2. Você não precisa configurar o serviço de entrega de licença (ContentKeyAuthorizationPolicy) nos Serviços de Mídia do Azure. O que você precisa fazer é fornecer URLs de aquisição de licença (por PlayReady, Widevine e FairPlay) quando configurar AssetDeliveryPolicy durante a instalação da CENC com vários DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>E se eu quiser usar um STS personalizado?
Há uma série de motivos que levam um cliente a optar por usar um STS (Serviço de Token Seguro) para fornecer tokens JWT. Alguns deles são:

1. O IDP (provedor de identidade) usado pelo cliente não dá suporte ao STS. Nesse caso, um STS personalizado pode ser uma opção.
2. O cliente pode precisar de controle mais flexível na integração do STS com o sistema de cobrança de assinatura do cliente. Por exemplo, um operador MVPD pode oferecer vários pacotes de assinante OTT, como premium, basic, esportes etc. O operador pode querer corresponder as declarações em um token com o pacote do assinante para que somente o conteúdo do pacote correto seja disponibilizado. Nesse caso, um STS personalizado oferece a flexibilidade e o controle necessários.

Duas alterações precisam ser feitas ao usar um STS personalizado:

1. Ao configurar o serviço de entrega de licença para um ativo, você precisa especificar a chave de segurança usada para verificação pelo STS personalizado (mais detalhes abaixo) em vez da chave atual do Active Directory do Azure.
2. Quando um token JTW é gerado, uma chave de segurança é especificada em vez da chave privada do certificado X509 atual no Active Directory do Azure.

Há dois tipos de chaves de segurança:

1. Chave simétrica: a mesma chave é usada para gerar e verificar um token JWT;
2. Chave assimétrica: um par de chaves pública-privada em um certificado X509 é usado com uma chave privada para criptografar/gerar um token JWT, e a chave pública, para verificar o token.

#### <a name="tech-note"></a>Nota técnica
Se você usar o .NET Framework /C# como sua plataforma de desenvolvimento, o certificado X509 usado para a chave assimétrica de segurança deve ter o comprimento da chave de pelo menos 2048. Este é um requisito da classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey no .NET Framework. Caso contrário, a seguinte exceção será lançada:

IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' para a assinatura não pode ter menos de '2048' bits.

## <a name="the-completed-system-and-test"></a>Sistema concluído e teste
Vamos abordar alguns cenários no sistema de ponta a ponta concluído para que os leitores possam ter uma "imagem" básica do comportamento antes de obter uma conta de logon.

O aplicativo Web do player e seu logon podem ser encontrados [aqui](https://openidconnectweb.azurewebsites.net/).

Se o que você precisa é de cenários "não integrados": ativos de vídeo hospedados nos Serviços de Mídia do Azure que são desprotegidos ou protegidos por DRM, mas sem autenticação de token (emitindo uma licença para quem solicitá-la), pode testá-lo sem logon (alternando para HTTP se o streaming de vídeo é sobre HTTP).

Se o você precisa é do cenário integrado de ponta a ponta: os ativos de vídeo estão sob a proteção DRM dinâmica nos Serviços de Mídia do Azure, com a autenticação de token e o token JWT sendo gerados pelo AD do Azure. Você precisa fazer logon.

### <a name="user-login"></a>Logon de usuário
Para testar o sistema DRM integrado de ponta a ponta, você precisa ter uma "conta" criada ou adicionada.

Qual conta?

Embora o Azure originalmente permitisse acesso somente por usuários de contas da Microsoft, ele agora permite o acesso por usuários de ambos os sistemas. Isso foi feito fazendo com que todas as propriedades do Azure confiassem no AD do Azure para autenticação, tornando o AD do Azure responsável por autenticar usuários da organização e criando um relacionamento de federação em que o AD do Azure confia no sistema de identidade do consumidor de conta da Microsoft para autenticar usuários consumidores. Como resultado, o AD do Azure é capaz de autenticar contas de "convidado" da Microsoft e também contas "nativas" do AD do Azure.

Como o AD do Azure confia no domínio MSA (Conta da Microsoft), você pode adicionar contas de qualquer um dos seguintes domínios do locatário do AD do Azure personalizados e usar a conta para entrar:

| **Nome de domínio** | **Domínio** |
| --- | --- |
| **Domínio de locatário do AD do Azure personalizado** |somename.onmicrosoft.com |
| **Domínio corporativo** |microsoft.com |
| **Domínio de MSA (Conta da Microsoft)** |outlook.com, live.com, hotmail.com |

Você pode contatar qualquer um dos autores para que criem ou adicionem uma conta em seu nome.

Abaixo estão as capturas de tela de diferentes páginas de logon usadas por diferentes contas de domínio.

**Conta de domínio do locatário do AD do Azure personalizada**: nesse caso, você vê a página de logon personalizada do domínio de locatário do AD do Azure personalizado.

![Conta de domínio do locatário do AD do Azure personalizada](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Conta de domínio da Microsoft com cartão inteligente**: nesse caso, você vê a página de logon personalizada pela TI corporativa da Microsoft com autenticação de dois fatores.

![Conta de domínio do locatário do AD do Azure personalizada](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**MSA (Conta da Microsoft)**: nesse caso, você vê a página de logon da Conta da Microsoft para consumidores.

![Conta de domínio do locatário do AD do Azure personalizada](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>Usando Extensões de Mídia Criptografada para PlayReady
Em um navegador moderno com suporte a EME (Extensões de Mídia Criptografada) para PlayReady, como o IE 11 no Windows 8.1 e posteriores, e navegador Microsoft Edge no Windows 10, o PlayReady será o DRM subjacente para EME.

![Usando EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

A área escura do player ocorre porque a proteção do PlayReady evita a captura de tela de um vídeo protegido.

A tela a seguir mostra os plug-ins do player e o suporte do MSE/EME.

![Usando EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

A EME no Microsoft Edge e no IE 11 no Windows 10 permite invocar o [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) em dispositivos com Windows 10 que o suportam. O PlayReady SL3000 desbloqueia o fluxo de conteúdo premium avançado (4K, HDR, etc.) e novos modelos de distribuição de conteúdo (antigamente a janela para Conteúdo Avançado).

Concentre-se nos dispositivos com Windows: o PlayReady é o único DRM em HW disponível em dispositivos com Windows (PlayReady SL3000). Um serviço de streaming pode usar o PlayReady por meio da EME ou de um aplicativo UWP e oferecer uma qualidade mais alta de vídeo usando o PlayReady SL3000 em vez de outro DRM. Normalmente, o conteúdo de 2K fluirá por meio do Chrome ou Firefox, e o conteúdo de 4K pelo Microsoft Edge/IE11 ou um aplicativo UWP no mesmo dispositivo (dependendo das configurações do serviço e da implementação).

#### <a name="using-eme-for-widevine"></a>Usando EME para Widevine
Em um navegador moderno com suporte a EME/Widevine, como Chrome 41+ no Windows 10, Windows 8.1, Mac OSX Yosemite e Chrome no Android 4.4.4, o Google Widevine é o DRM por trás do EME.

![Usando EME para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Observe que o Widevine não impede a captura de tela de vídeo protegido.

![Usando EME para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>Usuários não qualificados
Se não for membro do grupo "Usuários Qualificados", o usuário não será capaz de passar na "verificação de autorização" e o serviço de licença com vários DRMs se recusará a emitir a licença solicitada, como mostrado abaixo. A descrição detalhada é "falha na aquisição de licença", que está de acordo com o design.

![Usuários não qualificados](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>Executando Serviço de Token Seguro personalizado
Para o cenário de execução de STS (Serviço de Token Seguro) personalizado, o token JWT será emitido pelo STS personalizado usando a chave simétrica ou assimétrica.

No caso de uso de chave simétrica (usando o Chrome):

![Executando STS personalizado](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

No caso de uso de chave assimétrica por meio de certificado X509 (usando navegador moderno da Microsoft).

![Executando STS personalizado](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Em ambos os casos acima, a autenticação do usuário permanece a mesma: por meio do AD do Azure. A única diferença é que os tokens JWT são emitidos pelo STS personalizado em vez de pelo AD do Azure. É claro que, ao configurar a proteção de CENC dinâmica, a restrição do serviço de entrega de licença especifica o tipo de token JWT, seja chave simétrica ou assimétrica.

## <a name="summary"></a>Resumo
Neste documento, vimos CENC com vários DRM nativos e controle de acesso por meio de autenticação de token; seu design e sua implementação usando o Azure, os Serviços de Mídia do Azure e o Player de Mídia do Azure.

* Um design de referência é apresentado contendo todos os componentes necessários a um subsistema DRM/CENC;
* Uma implementação de referência no Azure, nos Serviços de Mídia do Azure e no Player de Mídia do Azure.
* Alguns tópicos envolvidos diretamente no design e na implementação também são analisados.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
