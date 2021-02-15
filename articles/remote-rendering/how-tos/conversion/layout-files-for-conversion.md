---
title: Layout de arquivos para conversão
description: Recomendações sobre a melhor maneira de colocar os arquivos no contêiner de entrada.
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: 13c4b841fe2d0b62472628d9382b5f6ee3d1fa6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318068"
---
# <a name="laying-out-files-for-conversion"></a>Layout de arquivos para conversão

Para processar um ativo corretamente, o serviço de conversão precisa ser capaz de localizar todos os arquivos de entrada.
Eles consistem no arquivo de ativo principal que está sendo convertido e geralmente alguns outros arquivos referenciados por caminhos no arquivo de ativo.
A solicitação para converter um ativo recebe dois parâmetros que determinam como o serviço de conversão localiza esses arquivos: o `input.folderPath` (que é opcional) e o `input.inputAssetPath` .
Eles são totalmente documentados na página da [API REST de conversão](conversion-rest-api.md) .
Com a finalidade de dispor arquivos, o importante a observar é que o determina um `folderPath` conjunto completo de arquivos que estão disponíveis para o serviço de conversão ao processar o ativo.

## <a name="placing-files-so-they-can-be-found"></a>Colocando arquivos para que eles possam ser encontrados

Quando um ativo de origem faz uso de arquivos externos, os caminhos para esses arquivos serão armazenados no ativo.
O serviço de conversão precisa interpretar esses caminhos em um sistema de arquivos que é diferente do sistema de arquivos original do ativo.
Se os caminhos forem armazenados como caminhos relativos e o local relativo entre o ativo de origem e o arquivo ao qual ele faz referência for inalterado, será fácil para o serviço de conversão localizar o arquivo referenciado.

> [!Note]
> É recomendável colocar os arquivos no contêiner de entrada para que os locais relativos dos arquivos sejam os mesmos que eram quando o ativo foi criado.

> [!Note]
> Prefira criar ativos que contêm caminhos relativos.
> O tutorial sobre como [Configurar materiais para 3ds Max](../../tutorials/modeling/3dsmax-material-setup.md) fornece um exemplo de 3ds Max de como garantir que um ativo use caminhos relativos.

## <a name="finding-textures"></a>Localizando texturas

Devido às muitas maneiras que os ativos podem ser gerados, o serviço de conversão precisa ser flexível.
Em particular, ele precisa lidar com situações em que os caminhos no ativo e o local das texturas não coincidem precisamente.
Um exemplo é quando os ativos são gerados contendo caminhos absolutos, já que esses caminhos nunca corresponderão ao sistema de arquivos usado pelo serviço de conversão.
Para lidar com essa situação entre outras, usamos uma abordagem de melhor esforço para a localização de texturas.

O algoritmo para localizar texturas é o seguinte: dado um caminho como armazenado em um ativo, localize o sufixo de subcaminho mais longo que, quando usado como um caminho relativo do local do ativo de origem, tem como alvo um arquivo que existe.
Se nenhum subcaminho (incluindo o caminho inteiro) for direcionado a um arquivo, a textura será considerada como ausente.

Considere o seguinte sistema de arquivos forçado: 
```
G:\CONVERSION
├───Assets
│   │   myAsset.fbx
│   │   myTexture.png            <- A
│   │
│   └───Textures
│       │   myTexture.png        <- B
│       │
│       └───MyAssetTextures
│               myTexture.png    <- C
│
└───Textures
    │   myTexture.png            <- D
    │
    └───MyAssetTextures
            myTexture.png        <- E
```
Se myasset. FBX fizer referência a uma textura com o caminho relativo `..\Textures\MyAssetTextures\myTexture.png` , o serviço de conversão usará o arquivo E. Na verdade, ele pode usar qualquer um dos arquivos A, C e e, se existirem, com o arquivo E sendo preferido, pois ele é encontrado com o sufixo mais longo.
Os arquivos B e D nunca serão usados, porque `Textures\myTexture.png` não faz parte de qualquer sufixo do caminho armazenado.
Se o ativo contiver os caminhos `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` ou `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png` , o serviço de conversão poderá localizar os arquivos a e C, se existirem (preferindo C sobre A). No entanto, e não pode ser encontrado dessa maneira, e o arquivo precisará ser realocado.
Isso pode ser corrigido movendo a pasta de texturas ao lado do ativo.

> [!Note]
> Se as texturas não estiverem sendo encontradas, uma possível solução é garantir que o ativo seja um irmão de alguma subárvore contendo as texturas.

## <a name="next-steps"></a>Próximas etapas

- [Conversão de modelo](model-conversion.md)
