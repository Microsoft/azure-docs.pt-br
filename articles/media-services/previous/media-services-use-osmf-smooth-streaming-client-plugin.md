---
title: Plug-in do Smooth Streaming para o Open Source Media Framework
description: Saiba como usar o plug-in Smooth Streaming dos Serviços de Mídia do Azure para o Adobe Open Source Media Framework.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: e41e33b5f30cd861ae8715e0ad7f6694f8854198
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046922"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Como usar o plug-in do Microsoft Smooth Streaming para o Adobe Open Source Media Framework

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Visão geral
O plug-in do Microsoft Smooth Streaming para o Open Source Media Framework 2.0 (SS para OSMF) estende os recursos padrão do OSMF e adiciona a reprodução de conteúdo do Microsoft Smooth Streaming para players do OSMF novos e existentes. O plug-in também adiciona recursos de reprodução de Smooth Streaming para Strobe Media Playback (SMP).

O SS para OSMF inclui duas versões do plug-in:

* Plug-in do Static Smooth Streaming para OSMF (.swc)
* Plug-in do Dynamic Smooth Streaming para OSMF (.swf)

Este documento pressupõe que o leitor tenha um conhecimento geral de trabalho de plug-ins OSMF e OSMF. Para obter mais informações sobre o OSMF, consulte a documentação no site oficial do OSMF.

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Plug-in do Smooth Streaming para OSMF 2.0
O plug-in oferece suporte para o carregamento e a reprodução de conteúdo de Smooth Streaming sob demanda com os seguintes recursos:

* Reprodução de Smooth Streaming sob demanda (reproduzir, pausar, buscar, parar)
* Reprodução de Smooth Streaming ao vivo (reproduzir)
* Funções de DVR ao vivo (pausar, buscar, reprodução de DVR, Go-to-Live)
* Suporte para codecs de vídeo - H.264
* Suporte para codecs de áudio - AAC
* Comutação de vários idiomas de áudio com as APIs internas do OSMF
* Seleção da qualidade de reprodução máxima com as APIs internas do OSMF
* Legendas codificadas sidecar com o plug-in de legendas do OSMF
* Adobe&reg; Flash&reg; Player 11.4 ou superior.
* Essa versão dá suporte apenas ao OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Recursos com suporte e problemas conhecidos
Para obter uma lista completa de recursos com suporte, recursos sem suporte e problemas conhecidos, confira [este documento](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/).

## <a name="loading-the-plugin"></a>Carregando o plug-in
Os plug-ins do OSMF podem ser carregados estaticamente (em tempo de compilação) ou dinamicamente (em tempo de execução). O plug-in do Smooth Streaming para download do OSMF inclui versões dinâmicas e estáticas.

* Carregamento estático: para carregar estaticamente, é necessário um arquivo de biblioteca estática (SWC). Os plug-ins estáticos são adicionados como uma referência aos projetos e são mesclados no arquivo de saída final em tempo de compilação.
* Carregamento dinâmico: para carregar dinamicamente, é necessário um arquivo (SWF) pré-compilado. Os plug-ins dinâmicos são carregados em runtime e não são incluídos na saída do projeto. (Saída compilada) Os plug-ins dinâmicos podem ser carregados usando os protocolos HTTP e FILE.

Para obter mais informações sobre carregamento estático e dinâmico, consulte a [página oficial do plug-in do OSMF](https://www.ibm.com/support/knowledgecenter/SSLTBW_2.3.0/com.ibm.zos.v2r3.izua300/IZUHPINFO_PluginsPlanning.htm).

### <a name="ss-for-osmf-static-loading"></a>SS para carregamento dinâmico de OSMF
O snippet de código a seguir mostra como carregar o plug-in SS para OSMF estaticamente e executa um vídeo básico usando a classe MediaFactory do OSMF. Antes de incluir o SS para código de OSMF, verifique se a referência ao projeto inclui o plug-in estático "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc". 

```csharp
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>Carregamento dinâmico de SS para OSMF
O snippet de código a seguir mostra como carregar o plug-in SS para OSMF dinamicamente e reproduzir um vídeo básico usando a classe MediaFactory do OSMF. Antes de incluir o SS para código do OSMF, copie o plug-in dinâmico da "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" na pasta do projeto se desejar carregar usando protocolo FILE ou copiar em um servidor web para carga HTTP. Não é necessário incluir "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" nas referências ao projeto.

```csharp
package 
{

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Strobe Media Playback com o plug-in dinâmico do ODMF do SS
O plug-in dinâmico do Smooth Streaming para o OSMF é compatível com o [Strobe Media Playback (SMP)](https://sourceforge.net/adobe/smp/home/Strobe%20Media%20Playback/). Você pode usar o SS para o plug-in OSMF para adicionar reprodução de conteúdo de Smooth Streaming ao SMP. Para fazer isso, copie o "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" em um servidor web para carga HTTP, usando as seguintes etapas:

1. Procure a [página de configuração do Strobe Media Playback](http://www.koopman.me/bob3/setup.html). 
2. Defina a src para uma fonte de Smooth Streaming, (por exemplo, http: \/ /devplatem.vo.msecnd.net/Sintel/Sintel_H264.ISM/manifest) 
3. Faça as alterações desejadas na configuração e clique em Visualizar e Atualizar.
   
   **Observação** o servidor web de conteúdo precisa de um crossdomain.xml válido. 
4. Copie e cole o código em uma página HTML simples usando seu editor de texto preferido, como no exemplo a seguir:

    ```html
    <html>
    <body>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
    </embed>
    </object>
    </body>
    </html>
    ```


1. Adicione o plug-in do OSMF do Smooth Streaming ao código de inserção e salve.
   
    ```html
    <html>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
    </embed>
    </object>
    </html>
    ```

2. Salve a página HTML e publique em um servidor web. Procure a página da Web publicada usando seu navegador de Internet habilitado para Flash&reg; Player favorito (Internet Explorer, Chrome, Firefox, etc).
3. Aproveite o conteúdo de Smooth Streaming no Adobe&reg; Flash&reg; Player.

Para obter mais informações sobre o desenvolvimento de OSMF geral, consulte a [página oficial de desenvolvimento do OSMF](https://www.adobe.com/devnet/video/articles1/osmf_overview.html)

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte Também
[plug-in Microsoft Adaptive Streaming para atualização OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

