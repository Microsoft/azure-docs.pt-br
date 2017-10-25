---
title: "Inserindo anúncios no lado do cliente | Microsoft Docs"
description: "Este tópico mostra como inserir anúncios no lado do cliente."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 52ba731f88c630830560e3cf8406ba2e9613c8a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="inserting-ads-on-the-client-side"></a>Inserção de anúncios no lado do cliente
Este tópico contém informações sobre como inserir vários tipos de anúncios no lado do cliente.

Para obter informações sobre o suporte a legendagem oculta e anúncios em vídeos de transmissão ao vivo, consulte [Padrões de legendagem oculta e inserção de anúncios com suporte](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Atualmente, o Azure Media Player não dá suporte a anúncios.
> 
> 

## <a id="insert_ads_into_media"></a>Inserir anúncios em sua mídia
Os Serviços de Mídia do Azure dão suporte à inserção de anúncios por meio da Plataforma de Mídia do Windows: Player Frameworks. As estruturas de player com suporte a anúncios estão disponíveis para dispositivos com Windows 8, Silverlight, Windows Phone 8 e iOS. Cada estrutura de player contém um código de exemplo que mostra como implementar um aplicativo de player. Há três tipos diferentes de anúncios que podem ser inseridos em sua media:list.

* **Lineares** – anúncios em tela cheia que pausam o vídeo principal.
* **Não lineares** – anúncios de sobreposição que são exibidos quando o vídeo principal está sendo reproduzido, geralmente um logotipo ou outra imagem estática colocada no player.
* **Complementares** – anúncios que são exibidos fora do player.

Os anúncios podem ser inseridos em qualquer ponto na linha do tempo do vídeo principal. Você deve indicar ao player quando reproduzir o anúncio e quais anúncios devem ser reproduzidos. Isso é feito usando um conjunto de arquivos padrão baseados em XML: VAST (Video Ad Service Template), VMAP (Digital Video Multiple Ad Playlist), MAST (Media Abstract Sequencing Template) e VPAID (Digital Video Player Ad Interface Definition). Os arquivos VAST especificam quais anúncios devem ser exibidos. Os arquivos VMAP especificam quando reproduzir diversos anúncios e contêm XML VAST. Os arquivos MAST são outra maneira de sequenciar anúncios que também podem conter XML VAST. Os arquivos VPAID definem uma interface entre o player de vídeo e o anúncio ou o servidor de anúncios.

Cada estrutura do player funciona de maneira diferente e cada uma será abordada em seu próprio tópico. Este tópico descreverá os mecanismos básicos usados para inserir anúncios. Os aplicativos do player de vídeo solicitam os anúncios de um servidor de anúncios. O servidor do anúncio pode responder de várias maneiras:

* Retornar um arquivo VAST
* Retornar um arquivo VMAP (com VAST incorporado)
* Retornar um arquivo MAST (com VAST incorporado)
* Retornar um arquivo VAST com anúncios VPAID

### <a name="using-a-video-ad-service-template-vast-file"></a>Usando um arquivo (VAST) de modelo do serviço de anúncio de vídeo
Um arquivo VAST especifica quais anúncios devem ser exibidos. O XML a seguir é um exemplo de um arquivo VAST para um anúncio linear:

    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>

O anúncio linear é descrito pelo elemento <**Linear**>. Especifica a duração do anúncio, os eventos de rastreamento, taxa de cliques, rastreamento de cliques e o número de elementos **MediaFile**. Os eventos de rastreamento são especificados no elemento <**TrackingEvents**> e permitem que um servidor de anúncios rastreie diversos eventos que ocorrem durante a visualização do anúncio. Nesse caso, o início, o ponto médio, conclusão e eventos de expansão são rastreados. O evento de inicialização ocorre quando o anúncio é exibido. O evento de ponto médio ocorre quando pelo menos 50% da linha do tempo do anúncio foi exibida. O evento de expansão completo ocorre quando o anúncio foi reproduzido até o final. O evento de expansão ocorre quando o usuário expande o player de vídeo para tela inteira. As taxas de cliques são especificadas com um elemento <**ClickThrough**> dentro de um elemento <**VideoClicks**> e especifica um URI para um recurso a exibir quando o usuário clica no anúncio. O rastreamento de cliques é especificado em um elemento <**ClickTracking**>, também dentro do elemento <**VideoClicks**> e especifica um recurso de rastreamento para o leitor solicitar quando o usuário clica no anúncio. Os elementos <**MediaFile**> especificam informações sobre uma codificação específica de um anúncio. Quando houver mais de um elemento <**MediaFile**>, o leitor de vídeo poderá escolher a melhor codificação para a plataforma. 

Os anúncios lineares podem ser exibidos na ordem especificada. Para fazer isso, adicione mais elementos de <Ad> ao arquivo VAST e especifique a ordem usando o atributo de sequência. O exemplo a seguir ilustra isso:

    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>

Anúncios não lineares também são especificados em um elemento de <Creative>. A exemplo a seguir mostra um elemento de <Creative> que descreve um anúncio não linear.

    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>


O elemento <**NonLinearAds**> pode conter um ou mais elementos <**NonLinear**>, cada um deles podendo descrever um anúncio não linear. O elemento <**NonLinear**> especifica o recurso para o anúncio não linear. O recurso pode ser um <**StaticResource**>, <**IFrameResource**> ou <**HTMLResource**>. <**StaticResource**> descreve um recurso não HTML e define um atributo creativeType que especifica como o recurso é exibido:

Image/gif, image/jpeg, image/png – o recurso é exibido em uma marcação HTML <**img**>.

Application/x-javascript – o recurso é exibido em uma marcação HTML <**script**>.

Application/x-shockwave-flash – o recurso é exibido em um Flash player.

**IFrameResource** descreve um recurso HTML que pode ser exibido em um IFrame. **HTMLResource** descreve um trecho de código HTML que pode ser inserido em uma página da Web. **TrackingEvents** especificar os eventos de rastreamento e o URI a solicitar quando o evento ocorre. Neste exemplo, os eventos acceptInvitation e collapse são rastreados. Para obter mais informações sobre o elemento **NonLinearAds** e seus filhos, consulte IAB.NET/VAST. Observe que o elemento **TrackingEvents** está localizado no elemento **NonLinearAds**, em vez do elemento **NonLinear**.

Anúncios complementares são definidos dentro de um elemento de <CompanionAds>. O elemento <CompanionAds> pode conter um ou mais elementos de <Companion>. Cada elemento de <Companion> descreve um anúncio complementar e pode conter um <StaticResource>, <IFrameResource>, ou <HTMLResource> que são especificados da mesma maneira que um anúncio não linear. Um arquivo VAST pode conter diversos anúncios complementares e o aplicativo de player pode escolher o anúncio mais apropriado a ser exibido. Para saber mais sobre VAST, consulte [VAST 3.0](http://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Usando um arquivo VMAP (Digital Video Multiple Ad Playlist)
Um arquivo VMAP permite que você especifique quando ocorrerem a intervalos de anúncios, quanto tempo cada tem cada intervalo, quantos anúncios podem ser exibidos durante um intervalo e quais tipos de anúncios podem ser exibidos durante um intervalo. O seguinte em um exemplo de arquivo VMAP que define um único intervalo de anúncio:

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

Um arquivo VMAP começa com um elemento de <VMAP> que contém um ou mais elementos de <AdBreak>, cada um definindo um intervalo de anúncio. Cada intervalo de anúncio especifica um tipo de intervalo, ID de intervalo e deslocamento de tempo. O atributo breakType especifica o tipo de anúncio que pode ser reproduzido durante o intervalo: linear, não linear ou exibição. Exibir o mapa de anúncios para anúncios VAST complementares. Mais de um tipo de anúncio pode ser especificado em uma lista separada por vírgulas (sem espaços). O breakID é um identificador opcional para o anúncio. O timeOffset especifica quando o anúncio deve ser exibido. Ele pode ser especificado em uma das seguintes maneiras:

1. Tempo – em formato hh:mm:ss ou hh:mm:ss.mmm em que .mmm são milissegundos. O valor deste atributo especifica o tempo desde o início da linha do tempo de vídeo para o início do intervalo de anúncio.
2. Porcentagem – em formato %n, em que n é a porcentagem da linha do tempo de vídeo a ser executado antes da execução do anúncio
3. Início/fim – especifica que um anúncio deve ser exibido antes ou depois que o vídeo foi exibido
4. Posição – especifica a ordem dos intervalos dos anúncios quando o tempo dos intervalos dos anúncios é desconhecido, como na transmissão ao vivo. A ordem de cada intervalo de anúncio é especificada no formato #n, onde n é um inteiro 1 ou maior. 1 significa que o anúncio deve ser reproduzido na primeira oportunidade, 2 significa que o anúncio deve ser reproduzido na segunda oportunidade e assim por diante.

Dentro do elemento <**AdBreak>** pode ser um elemento <**AdSource**>. O elemento <**AdSource**> contém os seguintes atributos:

1. Id – especifica um identificador para a origem do anúncio
2. allowMultipleAds – um valor booleano que especifica se vários anúncios podem ser exibidos durante o intervalo de anúncio
3. followRedirects – um valor booleano opcional que especifica se o player de vídeo deve usar redirecionamentos dentro de uma resposta de anúncio

O elemento <**AdSource**> fornece ao player uma resposta embutida de anúncio ou uma referência a uma resposta de anúncio. Ele pode conter um dos seguintes elementos:

* <VASTAdData> indica que uma resposta de anúncio VAST está incorporada no arquivo VMAP
* <AdTagURI> um URI que faz referência a uma resposta de anúncio de outro sistema
* <CustomAdData> -uma cadeia de caracteres arbitrária que representa uma resposta não VAST

Neste exemplo, uma resposta embutida de anúncio é especificada com um elemento <VASTAdData> que contém uma resposta de anúncio VAST. Para saber mais sobre os outros elementos, consulte [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

O elemento <**AdBreak**> também pode conter um elemento <**TrackingEvents**>. O elemento <**TrackingEvents**> permite que você rastreie o início ou término de um intervalo de anúncio ou se ocorreu um erro durante o intervalo de anúncio. O elemento <**TrackingEvents**> contém um ou mais elementos <**Tracking**>, cada um deles especifica um evento de rastreamento e um URI de rastreamento. Os eventos de rastreamento possíveis são:

1. breakStart – rastreia o início de um intervalo de anúncio
2. breakEnd – rastreia a conclusão de um intervalo de anúncio
3. error – rastreia um erro que ocorreu durante o intervalo de anúncio

O exemplo a seguir mostra um arquivo VMAP que especifica os eventos de rastreamento

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

Para saber mais sobre o elemento <**TrackingEvents**> e seus filhos, consulte http://iab.org/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Usando um arquivo MAST (Media Abstract Sequencing Template)
Um arquivo MAST permite que você especifique gatilhos que definem quando um anúncio é exibido. O exemplo a seguir é de arquivo MAST que contém gatilhos para um anúncios do tipo pre-roll, mid-roll e post-roll.

    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>



Um arquivo MAST começa com um elemento **MAST** que contém um elemento **triggers**. O elemento <triggers> contém um ou mais elementos **trigger** que definem quando um anúncio deve ser reproduzido. 

O elemento **trigger** contém um elemento **startConditions** que especifica quando um anúncio deve começar a reproduzir. O elemento **startConditions** contém um ou mais elementos <condition>. Quando cada <condition> é avaliada como true, um gatilho é iniciado ou revogado dependendo da <condition> estar contida em um elemento **startConditions** ou **endConditions**, respectivamente. Quando vários elementos <condition> estão presentes, eles são tratados como um implícitos OU qualquer condição avaliada como true fará com que o gatilho seja iniciado. Os elementos <condition> podem ser aninhados. Quando os elementos da <condition> filha estiverem presentes, eles são tratados como implícitos E todas as condições devem ser avaliadas como true para que o acionador seja iniciado. O elemento <condition> contém os seguintes atributos que definem a condição: 

1. **tipo** – especifica o tipo de condição, evento ou propriedade
2. **nome** – o nome da propriedade ou evento a ser usado durante a avaliação
3. **valor** – o valor contra o qual uma propriedade será avaliada
4. **operador** – operação a ser usada durante a avaliação: EQ (igual), NEQ (não igual), GTR (maior), GEQ (maior ou igual), LT (menor que), LEQ (menor ou igual), MOD (módulo)

**endConditions** também contêm elementos <condition>. Quando uma condição for avaliada como true, o gatilho é redefinido. O elemento de <trigger> também contém um elemento de <sources> que contém um ou mais elementos de <source>. Os elementos de <source> definem o URI para a resposta de anúncio e o tipo de resposta de anúncio. Neste exemplo, um URI é dado a uma resposta VAST. 

    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>


### <a name="using-video-player-ad-interface-definition-vpaid"></a>Usando a VPAID (Video Player-Ad Interface Definition)
VPAID é uma API para habilitar unidades executáveis de anúncio para se comunicar com um player de vídeo. Isso permite experiências de anúncios altamente interativos. O usuário pode interagir com o anúncio e o anúncio pode responder às ações tomadas pelo visualizador. Por exemplo, um anúncio pode exibir botões que permitem ao usuário visualizar mais informações ou uma versão mais longa do anúncio. O player de vídeo deve oferecer suporte à API do VPAID e o anúncio executável deve implementar a API. Quando um player solicita um anúncio de um servidor de anúncios, o servidor pode responder com uma resposta VAST que contenha um anúncio VPAID.

Um anúncio executável é criado no código que deve ser executado em um ambiente de tempo de execução como Adobe Flash ™ ou JavaScript, que pode ser executado em um navegador da Web. Quando um servidor de anúncios retorna uma resposta VAST que contém um anúncio VPAID, o valor do atributo apiFramework no elemento <MediaFile> deve ser "VPAID". Este atributo especifica que o anúncio contido é um anúncio VPAID executável. O atributo de tipo deve ser definido como o tipo MIME do executável, como "application/x-shockwave-flash" ou "application/x-javascript". O trecho XML a seguir mostra o elemento <MediaFile> de uma resposta VAST que contém um anúncio VPAID executável. 

    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>


Um anúncio executável pode ser inicializado usando o elemento <AdParameters> dentro dos elementos <Linear> ou <NonLinear> em um resposta VAST. Para saber mais sobre o elemento <AdParameters>, consulte [VAST 3.0](http://www.iab.net/media/file/VASTv3.0.pdf). Para saber mais sobre o VPAID API, consulte [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementação de um player no Windows ou Windows Phone 8 com suporte a anúncios
A Plataforma de Mídia da Microsoft: Player Framework para Windows 8 e Windows Phone 8 contém uma coleção de aplicativos de exemplo que mostra como implementar um aplicativo de player de vídeo usando a estrutura. Você pode baixar o Player Framework e os exemplos do [Player Framework para Windows 8 e Windows Phone 8](https://playerframework.codeplex.com).

Quando você abrir a solução Microsoft.PlayerFramework.Xaml.Samples, verá um número de pastas dentro do projeto. A pasta Anúncios contém o código de exemplo relevante para a criação de um player de vídeo com suporte para anúncios. Dentro da pasta Anúncios há vários arquivos XAML/cs, cada um dos quais mostra como inserir anúncios de maneira diferente. A lista a seguir descreve cada:

* AdPodPage.xaml mostra como exibir um pacote de anúncios.
* AdSchedulingPage.xaml mostra como agendar anúncios.
* FreeWheelPage.xaml mostra como usar o plug-in FreeWheel para agendar anúncios.
* MastPage.xaml mostra como agendar anúncios com um arquivo MAST.
* Programmaticadpage mostra como agendar anúncios programaticamente em um vídeo.
* ScheduleClipPage.xaml Shows mostra como agendar um anúncio sem um arquivo VAST.
* VastLinearCompanionPage.xaml mostra como inserir um anúncio linear e complementar.
* VastNonLinearPage.xaml mostra como inserir um anúncio não linear.
* VmapPage.xaml mostra como especificar anúncios com um arquivo VMAP.

Cada um desses exemplos usa a classe de Media Player definida pela estrutura de player. A maioria dos exemplos usam plugins que adicionam suporte para vários formatos de resposta de anúncio. O exemplo ProgrammaticAdPage interage programaticamente com uma instância do MediaPlayer.

### <a name="adpodpage-sample"></a>Exemplo do AdPodPage
Este exemplo usa o AdSchedulerPlugin para definir quando exibir um anúncio. Neste exemplo, um anúncio mid-roll é agendado para ser reproduzido depois de 5 segundos. O pacote de anúncios (grupo de anúncios que devem ser exibidos em ordem) é especificado em um arquivo VAST retornado de um servidor de anúncios. O URI para o arquivo VAST é especificado no elemento <RemoteAdSource>.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>

Para obter mais informações sobre o AdSchedulerPlugin, consulte [Anunciando no Player Framework no Windows 8 e Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
Este exemplo também usa o AdSchedulerPlugin. Ele agenda três anúncios, um anúncio pre-roll, um anúncio mid-roll e um anúncio post-roll. O URI para o VAST de cada anúncio é especificado em um elemento <RemoteAdSource>.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>


### <a name="freewheelpage"></a>FreeWheelPage
Este exemplo usa o FreeWheelPlugin que especifica um atributo de origem que especifica um URI que aponta para um arquivo SmartXML, que especifica o conteúdo do anúncio, bem como informações de agendamento de anúncios.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="mastpage"></a>MastPage
Este exemplo usa o MastSchedulerPlugin que permite que você use um arquivo MAST. O atributo de origem especifica o local do arquivo MAST.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Este exemplo interage programaticamente com o MediaPlayer. O arquivo ProgrammaticAdPage.xaml instancia o MediaPlayer:

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>

O arquivo ProgrammaticAdPage.xaml.cs cria um AdHandlerPlugin, adiciona um TimelineMarker para especificar quando um anúncio deve ser exibido e, em seguida, adiciona um manipulador para o evento MarkerReached que carrega um RemoteAdSource especificando um URI para um arquivo VAST e reproduz o anúncio.

    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }

### <a name="scheduleclippage"></a>ScheduleClipPage
Este exemplo usa o AdSchedulerPlugin para agendar um anúncio Mid-roll, especificando um arquivo .wmv que contém o anúncio.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
Este exemplo ilustra como usar o AdSchedulerPlugin para agendar um anúncio linear mid-roll com um anúncio complementar. O elemento <RemoteAdSource> especifica o local do arquivo VAST.

    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
Este exemplo usa AdSchedulerPlugin para agendar anúncio um linear e um não linear. O local do arquivo VAST é especificado com o elemento <RemoteAdSource>.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

### <a name="vmappage"></a>VMAPPage
Esse exemplos usam o VmapSchedulerPlugin para agendar anúncios usando um arquivo VMAP. O URI para o arquivo VMAP é especificado no atributo de origem do elemento <VmapSchedulerPlugin>.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementando um iOS Video Player com suporte para anúncios
A Plataforma de Mídia da Microsoft: Player Framework para iOS contém uma coleção de aplicativos de exemplo que mostra como implementar um aplicativo de player de vídeo usando a estrutura. Você pode baixar o Player Framework e os exemplos do [Framework do Azure Media Player](https://github.com/Azure/azure-media-player-framework). A página do github tem um link para um Wiki que contém informações adicionais sobre a estrutura de player e uma introdução ao exemplo do player: [Wiki do Azure Media Player](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Agendando anúncios com VMAP
O exemplo a seguir mostra como agendar anúncios usando um arquivo VMAP.

    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }          
            }

### <a name="scheduling-ads-with-vast"></a>Agendando anúncios com VAST
O exemplo a seguir mostra como agendar um anúncio VAST de associação tardia.

    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

   O exemplo a seguir mostra como agendar um anúncio VAST de associação antecipada.
//Example:4 Schedule an early binding VAST ad //Download the VAST file if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]]) { [self logFrameworkError]; } else { adLinearTime.startTime = 7; adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }

O exemplo a seguir mostra como inserir um anúncio usando uma RCE (edição de corte irregular)

    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

O exemplo a seguir mostra como agendar um pacote de anúncios.

    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo a seguir mostra como agendar um anúncio não adesivo mid-roll. Um anúncio não adesivo é reproduzido apenas uma vez, independentemente das buscas realizadas pelo visualizador.

    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo a seguir mostra como agendar um anúncio adesivo mid-roll. Um anúncio adesivo será exibido cada vez que o ponto especificado na linha do tempo de vídeo for atingido.

    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }


O exemplo a seguir mostra como agendar um anúncio post-roll.

    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo a seguir mostra como agendar um anúncio pre-roll.

    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo a seguir mostra como agendar um anúncio com sobreposição mid-roll.

    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }



## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também
[Desenvolver aplicativos de player de vídeo](media-services-develop-video-players.md)

