---
title: Modelos
description: Este tópico explica os modelos de hubs de notificação do Azure.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 02473eb5649c7d201b6a54fd57faea997c1a21cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60872069"
---
# <a name="templates"></a>Modelos

Os modelos permitem que um aplicativo cliente especifique o formato exato das notificações que deseja receber. Ao usar modelos, um aplicativo pode reconhecer diversos benefícios diferentes, incluindo os seguintes:

- Um back-end independente de plataforma
- Notificações personalizadas
- Independência da versão do cliente
- Localização fácil

Esta seção fornece dois exemplos detalhados de como usar modelos para enviar notificações independentes de plataformas ao direcionar todos os dispositivos em plataformas e para personalizar a notificação de difusão para cada dispositivo.

## <a name="using-templates-cross-platform"></a>Como usar modelos de plataforma cruzada

O modo padrão para enviar notificações por push é enviar, para cada notificação que será enviada, uma carga específica aos serviços de notificação de plataforma (WNS, APNS). Por exemplo, para enviar um alerta para APNS, a carga é um objeto JSON da seguinte forma:

```json
{"aps": {"alert" : "Hello!" }}
```

Para enviar uma mensagem de notificação do sistema semelhante em um aplicativo da Windows Store, a carga XML é a seguinte:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

É possível criar cargas similares para MPNS (Windows Phone) e para plataformas FCM (Android).

Esse requisito força o back-end do aplicativo a produzir cargas diferentes para cada plataforma e torna o back-end responsável por parte da camada de apresentação do aplicativo. Algumas questões incluem a localização e layouts gráficos (especialmente para aplicativos da Windows Store que englobam notificações para vários tipos de blocos).

O recurso de modelo de Hubs de Notificação permite que um aplicativo cliente crie registros especiais, chamados registros modelos, que abrangem, além do conjunto de marcas, um modelo. O recurso modelo dos Hubs de Notificação permite que um aplicativo cliente associe os dispositivos com modelos se você estiver trabalhando com instalações (preferidas) ou Registros. Considerando os exemplos anteriores de carga, a única informação independente de plataforma é a mensagem de alerta real (Olá!). Um modelo é um conjunto de instruções para o Hub de Notificação sobre como formatar uma mensagem independente de plataforma para o registro daquele aplicativo cliente específico. No exemplo anterior, a mensagem independente de plataforma é uma propriedade única: `message = Hello!`.

A figura a seguir ilustra o processo:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

O modelo para o registro do aplicativo cliente iOS é como segue:

```json
{"aps": {"alert": "$(message)"}}
```

O modelo correspondente para o aplicativo do cliente da Windows Store é:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Observe que a mensagem real é substituída pela expressão $(message). A expressão instrui o Hub de Notificação, sempre que ele envia uma mensagem para este determinado registro, para compilar uma mensagem que segue a ele e aos comutadores no valor comum.

Se você estiver trabalhando com o modelo de instalação, a chave de instalação “modelos” terá uma JSON de vários modelos. Se você estiver trabalhando com o modelo de registro, o aplicativo cliente poderá criar vários registros para usar vários modelos. Por exemplo, um modelo para mensagens de alerta e um modelo para atualizações de bloco. Os aplicativos clientes também podem mesclar registros nativos (registros sem um modelo) e registros de modelo.

O Hub de Notificação envia uma notificação para cada modelo sem considerar se eles pertencem ao mesmo aplicativo cliente. Esse comportamento pode ser usado para converter notificações independentes de plataforma em mais notificações. Por exemplo, a mesma mensagem independente de plataforma para o Hub de Notificação pode ser convertida em um alerta de notificação do sistema e uma atualização de bloco, sem a necessidade de back-end para estar ciente dele. Algumas plataformas (por exemplo, iOS) podem recolher diversas notificações no mesmo dispositivo se elas forem enviadas em um curto período de tempo.

## <a name="using-templates-for-personalization"></a>Como usar modelos para personalização

Outra vantagem de usar modelos é a capacidade de usar os Hubs de Notificação para realizar a personalização por registro de notificações. Por exemplo, considere um aplicativo sobre clima que exibe um bloco com as condições climáticas em um local específico. Um usuário pode escolher entre graus Celsius ou Fahrenheit e uma previsão única ou de cinco dias. Ao usar modelos, cada instalação do aplicativo cliente pode registrar para o formato necessário (1 dia Celsius, 1 dia Fahrenheit, 5 dias Celsius, 5 dias Fahrenheit) e o back-end pode enviar uma única mensagem que contenha todas as informações necessárias para preencher esses modelos (por exemplo, uma previsão de cinco dias com graus Celsius e Fahrenheit).

O modelo para a previsão de um dia com temperaturas em graus Celsius é como segue:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

A mensagem enviada ao Hub de Notificação contém as seguintes propriedades:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Ao usar esse padrão, o back-end envia apenas uma única mensagem sem ter que armazenar opções de personalização específicas para os usuários do aplicativo. A figura a seguir ilustra esse cenário:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Como registrar modelos

Para registrar usando modelos de instalação (preferencial) ou o modelo de registro, consulte a seção [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Linguagem de expressão do modelo

Os modelos são limitados aos formatos de documento XML ou JSON. Além disso, só é possível usar expressões em determinados locais. Por exemplo, os atributos de nó ou valores para XML, cadeia de valores de propriedade para JSON.

A tabela a seguir mostra a linguagem permitida nos modelos:

| Expressão       | DESCRIÇÃO |
| ---------------- | --- |
| $(prop)          | Referência para uma propriedade de evento com o nome fornecido. Os nomes de propriedade não diferenciam maiúsculas de minúsculas. Esta expressão é convertida para o valor de texto da propriedade ou em uma sequência de caracteres vazia se a propriedade não estiver presente. |
| $(prop, n)       | Como consta acima, mas o texto é explicitamente cortado em n caracteres, por exemplo, $(title, 20) corta o conteúdo da propriedade de título em 20 caracteres. |
| .(prop, n)       | Como consta acima, mas o texto é sufixado com três pontos quando é cortado. O tamanho total da cadeia de caracteres cortada e do sufixo não excede n caracteres. .(title, 20) com uma propriedade de entrada de "Esta é a linha de título" resulta em **Este é o título...** |
| %(prop)          | Semelhante ao $(name), exceto que a saída é codificada para URI. |
| #(prop)          | Usada em modelos JSON (por exemplo, para modelos iOS e Android).<br><br>Essa função funciona exatamente como $(prop) anteriormente especificada, exceto quando usada em modelos JSON (por exemplo, modelos Apple). Nesse caso, se essa função não for delimitada por “{‘,’}” (por exemplo, ‘myJsonProperty’ : ‘#(name)’) e for avaliada como um número em formato Javascript, por exemplo,regexp: (0&#124;(&#91;1-9&#93;&#91;0-9&#93;*))(\.&#91;0-9&#93;+)?((e&#124;E)(+&#124;-)?&#91;0-9&#93;+)?, então, a saída JSON será um número.<br><br>Por exemplo, ‘badge : ‘#(name)’se torna ‘badge’ : 40 (e não ‘40‘). |
| 'texto' ou "texto" | Um literal. Literais contêm texto arbitrário entre aspas simples ou duplas. |
| expr1 + expr2    | O operador de concatenação que une duas expressões em uma única cadeia de caracteres. |

As expressões podem ter qualquer uma das formas anteriores.

Ao usar concatenação, toda a expressão deve estar entre `{}`. Por exemplo, `{$(prop) + ‘ - ’ + $(prop2)}`.

Por exemplo, o modelo a seguir não é um modelo XML válido:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Como explicado antes, ao usar concatenação, as expressões devem ser colocadas entre colchetes. Por exemplo: 

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```
