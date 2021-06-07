---
title: Configurações do aplicativo-LUIS
description: As configurações de aplicativos para aplicativos de reconhecimento de linguagem de serviços cognitivas do Azure são armazenadas no aplicativo e no Portal.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/04/2020
ms.openlocfilehash: 0578e3c3c952a475c6beb01ffcf354e19eda6e26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91319156"
---
# <a name="app-and-version-settings"></a>Configurações do aplicativo e da versão

Essas configurações são armazenadas no aplicativo [exportado](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) e atualizadas com as APIs REST ou o portal do Luis.

Alterar as configurações de versão do aplicativo redefine o status de treinamento do aplicativo como não treinado.

[!INCLUDE [App and version settings](includes/app-version-settings.md)]


Referência de texto e exemplos incluem:

* [Pontuação](#punctuation-normalization)
* [Diacríticos](#diacritics-normalization)

## <a name="diacritics-normalization"></a>Normalização de diacríticos

O declarações a seguir mostra como a normalização de diacríticos afeta o declarações:

|Com sinais diacríticos definidos como false|Com sinais diacríticos definidos como true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Suporte a idiomas para sinais diacríticos

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Diacríticos do Português do Brasil `pt-br`

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>Diacríticos holandeses `nl-nl`

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>`fr-`Diacríticos franceses

Isso inclui as subculturas francesa e canadense.

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>Diacríticos em alemão `de-de`

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Diacríticos em italiano `it-it`

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Diacríticos em espanhol `es-`

Isso inclui espanhol e Canadá mexicano.

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|

## <a name="punctuation-normalization"></a>Normalização de Pontuação

O declarações a seguir mostra como a pontuação afeta declarações:

|Com pontuação definida como false|Com pontuação definida como true|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Pontuação removida

A pontuação a seguir é removida com `NormalizePunctuation` é definido como true.

|Pontuação|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|

## <a name="next-steps"></a>Próximas etapas

* Aprenda [conceitos](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) de sinais diacríticos e pontuação.
