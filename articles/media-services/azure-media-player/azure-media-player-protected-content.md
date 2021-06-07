---
title: Conteúdo protegido do Player de Mídia do Azure
description: O Player de Mídia do Azure atualmente dá suporte ao conteúdo com criptografia de envelope de 128 bits AES e ao conteúdo com criptografia comum.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 8c04a3847ce1005b530042926efb8fbbe75f9cfe
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448705"
---
# <a name="protected-content"></a>Conteúdo protegido #

O Player de Mídia do Azure atualmente dá suporte a conteúdo com criptografia de envelope de 128 bits AES, conteúdo com criptografia comum (por meio do PlayReady e Widevine) e conteúdo criptografado por meio do FairPlay. Para reproduzir o conteúdo protegido corretamente, você deve informar ao Player de Mídia do Azure o `protectionInfo`. Essas informações existem de maneira distinta para cada origem e podem ser adicionadas diretamente na marca `<source>` por meio do `data-setup`.  Se você estiver configurando a origem dinamicamente, também poderá adicionar o `protectionInfo` diretamente como um parâmetro.

`protectionInfo` aceita um objeto JSON e inclui:

- `type`: `AES` ou `PlayReady` ou `Widevine` ou `FairPlay`
- `certificateUrl`: deve ser um link direto para seu certificado FairPlay hospedado

- `authenticationToken`: é um campo de opção para adicionar um token de autenticação não codificado

> [!IMPORTANT]
> O objeto **certificateUrl** só é necessário para o DRM do FairPlay.***
>[!NOTE]
> O techOrder padrão foi alterado para acomodar a nova tecnologia (`html5FairPlayHLS`) especificamente para reproduzir conteúdo FairPlay nativamente em navegadores que dão suporte a ele (Safari no OSX 8 ou posterior). Se você tiver conteúdo de FairPlay para reprodução **E** tiver alterado o techOrder padrão para um personalizado em seu aplicativo, precisará adicionar essa nova tecnologia em seu objeto techOrder. É recomendável incluí-lo antes do silverlightSS para que o conteúdo não seja reproduzido via PlayReady.

## <a name="code-sample"></a>Exemplo de código ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

ou, com vários DRMs

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> Nem todos os navegadores/plataformas são capazes de reproduzir conteúdo protegido. Confira a seção [Tecnologia de reprodução](azure-media-player-playback-technology.md) para obter mais informações sobre o que é compatível.
> [!IMPORTANT]
> O token passado para o player destina-se ao conteúdo protegido e é usado somente para usuários autenticados. Supõe-se que o aplicativo esteja usando SSL ou alguma outra forma de medida de segurança. Além disso, existe a confiança de que o usuário final não fará o uso incorreto do token; se o contrário ocorrer, solicite a ajuda de seus especialistas em segurança.

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)