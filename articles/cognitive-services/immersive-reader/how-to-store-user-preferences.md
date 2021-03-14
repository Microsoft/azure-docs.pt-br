---
title: Armazenar preferências do usuário
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como armazenar as preferências do usuário.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 16ecd2166604d29fbc2242229f625b30ffd684e5
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617685"
---
# <a name="how-to-store-user-preferences"></a>Como armazenar as preferências do usuário

Este artigo demonstra como armazenar as configurações de interface do usuário, formalmente conhecidas como **preferências do usuário**, por meio das opções do SDK do leitor de imersão de leitura [-preferências](./reference.md#options) e [-Preferences](./reference.md#options) .

Quando a opção SDK do [CookiePolicy](./reference.md#cookiepolicy-options) é definida como *habilitada*, o aplicativo de leitor de imersão armazena as **preferências do usuário** (tamanho do texto, cor do tema, fonte e assim por diante) em cookies, que são locais para um dispositivo e navegador específicos. Cada vez que o usuário inicia o leitor de imersão no mesmo navegador e dispositivo, ele será aberto com as preferências do usuário de sua última sessão nesse dispositivo. No entanto, se o usuário abrir o leitor de imersão em um dispositivo ou navegador diferente, as configurações serão inicialmente definidas com as configurações padrão do leitor de imersão e o usuário precisará definir suas preferências novamente e assim por diante para cada dispositivo que usar. As `-preferences` `-onPreferencesChanged` Opções do SDK do leitor de imersão fornecem uma maneira para que os aplicativos movimentem as preferências de um usuário em vários navegadores e dispositivos, para que o usuário tenha uma experiência consistente onde quer que use o aplicativo.

Primeiro, ao fornecer a `-onPreferencesChanged` opção de SDK de retorno de chamada ao iniciar o aplicativo de leitor de imersão, o leitor de imersão enviará uma cadeia de `-preferences` caracteres de volta ao aplicativo host sempre que o usuário alterar suas preferências durante a sessão do leitor de imersão. O aplicativo host é então responsável por armazenar as preferências do usuário em seu próprio sistema. Em seguida, quando o mesmo usuário inicia o leitor de imersão novamente, o aplicativo host pode recuperar as preferências desse usuário do armazenamento e fornecê-las como a `-preferences` opção de SDK de cadeia de caracteres ao iniciar o aplicativo de leitura imersiva, para que as preferências do usuário sejam restauradas.

Essa funcionalidade pode ser usada como um meio alternativo de armazenar as **preferências do usuário** no caso em que o uso de cookies não é desejável ou viável.

> [!CAUTION]
> **Importante** Não tente alterar programaticamente os valores da `-preferences` cadeia de caracteres enviada de e para o aplicativo de leitor de imersão, pois isso pode causar um comportamento inesperado, resultando em uma experiência de usuário degradada para seus clientes. Os aplicativos host nunca devem atribuir um valor personalizado ou manipular a `-preferences` cadeia de caracteres. Ao usar a `-preferences` opção de cadeia de caracteres, use apenas o valor exato que foi retornado da `-onPreferencesChanged` opção de retorno de chamada.

## <a name="how-to-enable-storing-user-preferences"></a>Como habilitar o armazenamento de preferências do usuário

o parâmetro [launchAsync](./reference.md#launchasync) do SDK do leitor de imersão `options` contém o `-onPreferencesChanged` retorno de chamada. Essa função será chamada sempre que o usuário alterar suas preferências. O `value` parâmetro contém uma cadeia de caracteres, que representa as preferências atuais do usuário. Em seguida, essa cadeia de caracteres é armazenada para esse usuário, pelo aplicativo host.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Como carregar as preferências do usuário no leitor de imersão

Passe as preferências do usuário para o leitor de imersão usando a `-preferences` opção. Um exemplo trivial para armazenar e carregar as preferências do usuário é o seguinte:

```typescript
const storedUserPreferences = localStorage.getItem("USER_PREFERENCES");
let userPreferences = storedUserPreferences === null ? null : storedUserPreferences;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
        localStorage.setItem("USER_PREFERENCES", userPreferences);
    }
};
```

## <a name="next-steps"></a>Próximas etapas

* Explorar a [Referência do SDK da Leitura Avançada](./reference.md)