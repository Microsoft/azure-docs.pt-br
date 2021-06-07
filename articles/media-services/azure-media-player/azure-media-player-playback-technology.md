---
title: Tecnologia de reprodução do Player de Mídia do Azure
description: Saiba mais sobre a tecnologia de reprodução usada para reproduzir o vídeo ou áudio.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 0fadb679260582a788eb6408ff3761f00c2ceb1a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448807"
---
# <a name="playback-technology-tech"></a>Tecnologia de reprodução ("tech") #

A tecnologia de reprodução refere-se à tecnologia de navegador ou de plug-in específica usada para reproduzir o vídeo ou áudio.

- **azureHtml5JS**: utiliza os padrões MSE e EME em conjunto com o elemento de vídeo para a reprodução sem plug-in de conteúdo DASH compatível com conteúdo com criptografia de envelope de 128 bits AES ou conteúdo com criptografia comum DRM(via PlayReady e Widevine quando o navegador é compatível com eles) dos Serviços de Mídia do Azure
- **flashSS**: utiliza a tecnologia do Flash Player para reproduzir conteúdo Smooth compatível com descriptografia de envelope de 128 bits AES dos Serviços de Mídia do Azure – requer a versão 11.4 ou posterior do Flash
- **html5FairPlayHLS**: utiliza a tecnologia de reprodução baseada em navegador específica do Safari por meio do HLS com o elemento de vídeo. Essa tecnologia é necessária para reproduzir conteúdo protegido por FairPlay dos Serviços de Mídia do Azure e faz parte da techOrder desde 19/10/2016
- **SilverlightSS**: utiliza a tecnologia Silverlight para reproduzir conteúdo Smooth com suporte para conteúdo protegido pelo PlayReady dos Serviços de Mídia do Azure.
- **html5**: utiliza a tecnologia de reprodução baseada em navegador com o elemento de vídeo.  Quando em um dispositivo Apple iOS ou Android, essa tecnologia permite a reprodução de fluxos de HLS com algum suporte básico para criptografia de envelope de 128 bits AES ou conteúdo de DRM (via FairPlay quando o navegador é compatível com ele).

## <a name="tech-order"></a>Ordem de tecnologias ##

Para garantir que o ativo seja reproduzido em uma ampla variedade de dispositivos, a seguinte ordem de tecnologias é recomendada e é o padrão se: `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` e pode ser definido diretamente no `<video>` ou programaticamente nas opções:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

ou

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Matriz de compatibilidade ##

Dada a ordem de tecnologia recomendada com conteúdo de streaming dos Serviços de Mídia do Azure, a matriz de reprodução de compatibilidade a seguir é esperada

| Navegador        | Sistema operacional                                                       | Tecnologia esperada (Clear)  | Tecnologia esperada (AES)  | Tecnologia esperada (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| EdgeIE 11      | Windows 10, Windows 8.1, Windows Phone 10<sup>1</sup>               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11          | Windows 7, Windows Vista<sup>1</sup>                     | flashSS                | flashSS              | SilverlightSS (PlayReady)    |
| IE 11          | Windows Phone 8,1                                        | azureHtml5JS           | azureHtml5JS         | sem suporte                |
| Microsoft Edge           | Xbox One<sup>1</sup> (atualização de novembro de 2015)                   | azureHtml5JS           | azureHtml5JS         | sem suporte                |
| Chrome 37 ou posterior     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47 ou posterior    | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | SilverlightSS (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | flashSS                | flashSS              | SilverlightSS (PlayReady)    |
| Safari         | iOS 6+                                                   | html5                  | HTML5 (sem token)3    | sem suporte                |
| Safari 8 ou posterior      | OS X Yosemite+                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | OS X Mountain Lion<sup>1</sup>                           | flashSS                | flashSS              | SilverlightSS (PlayReady)    |
| Chrome 37 ou posterior     | Android 4.4.4+<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37 ou posterior     | Android 4.02                                             | html5                  | html5 (sem token)<sup>3</sup>    | sem suporte                |
| Firefox 42 ou posterior    | Android 5.0+<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | sem suporte                |
| IE 8, IE 9, IE 10  | Windows                                                  | sem suporte          | sem suporte        | sem suporte                |

<sup>1</sup> Configuração não compatível ou não testada; listado como referência para conclusão.

<sup>2</sup> A reprodução bem-sucedida em dispositivos Android requer uma combinação de funcionalidades do dispositivo, suporte a elementos gráficos, renderização de codec, suporte ao sistema operacional e outros. Como o Android é uma plataforma de software livre que permite que os fabricantes de telefone alterem o sistema operacional Android Vanilla fornecido pelo Google, isso causou um pouco de fragmentação no espaço do Android e alguns dispositivos podem não ser compatíveis devido à falta de recursos. Além disso, alguns dispositivos Android não são compatíveis com todos os codecs.  

<sup>3</sup> Nos casos em que não há compatibilidade com token, um proxy pode ser usado para adicionar essa funcionalidade. Confira este [blog](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) para saber mais sobre essa solução.

> [!NOTE]
> Se a tecnologia esperada escolhida exigir que um plug-in (como o Flash) esteja instalado e esse plugin não estiver instalado no computador do usuário, o Player de Mídia do Azure continuará a verificar as funcionalidades da próxima tecnologia, juntamente com as informações de proteção e tipos de origem, na lista de tecnologias. Por exemplo, se for realizada a tentativa de exibir um fluxo não protegido sob demanda no Safari 8 no OS X Yosemite e o Flash e o Silverlight não estiverem instalados, o Player de Mídia do Azure selecionará a tecnologia HTML5 nativa para reprodução.<br/><br/>Novas tecnologias de navegador estão surgindo diariamente, o que pode vir a afetar essa matriz.

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)