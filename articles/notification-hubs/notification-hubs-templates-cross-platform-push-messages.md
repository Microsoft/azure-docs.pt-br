---
title: Modelos de hubs de notificação do Azure
description: Saiba mais sobre como usar modelos para os hubs de notificação do Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ee42512a468f4ff86ad7ba273d3971fd124779e2
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635635"
---
# <a name="notification-hubs-templates"></a>Modelos de hubs de notificação

Os modelos permitem que um aplicativo cliente especifique o formato exato das notificações que deseja receber. Ao usar modelos, um aplicativo pode reconhecer diversos benefícios diferentes, incluindo os seguintes:

- Um back-end independente de plataforma
- Notificações personalizadas
- Independência da versão do cliente
- Localização fácil

Esta seção fornece dois exemplos detalhados de como usar modelos para enviar notificações independentes de plataforma direcionando todos os seus dispositivos entre plataformas e para personalizar notificações de difusão para cada dispositivo.

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

O recurso de modelo de Hubs de Notificação permite que um aplicativo cliente crie registros especiais, chamados registros modelos, que abrangem, além do conjunto de marcas, um modelo. O recurso de modelo de hubs de notificação permite que um aplicativo cliente associe dispositivos a modelos se você estiver trabalhando com instalações (preferenciais) ou registros. Considerando os exemplos de carga precedentes, as únicas informações independentes de plataforma são a mensagem de alerta real (**Olá!**). Um modelo é um conjunto de instruções para o Hub de notificação sobre como formatar uma mensagem independente de plataforma para o registro desse aplicativo cliente específico. No exemplo anterior, a mensagem independente de plataforma é uma propriedade única: `message = Hello!`.

A seguinte figura ilustra o processo:

![Diagrama mostrando o processo para usar modelos de plataforma cruzada](./media/notification-hubs-templates/notification-hubs-hello.png)

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

Observe que a mensagem real é substituída pela expressão `$(message)` . Essa expressão instrui o Hub de notificação, sempre que envia uma mensagem para esse registro específico, para criar uma mensagem que a segue e insere o valor comum.

Se você estiver trabalhando com o modelo de instalação, a chave de instalação "modelos" mantém um JSON de vários modelos. Se você estiver trabalhando com o modelo de registro, o aplicativo cliente poderá criar vários registros para usar vários modelos; por exemplo, um modelo para mensagens de alerta e um modelo para atualizações de bloco. Os aplicativos clientes também podem mesclar registros nativos (registros sem um modelo) e registros de modelo.

O Hub de notificação envia uma notificação para cada modelo sem considerar se eles pertencem ao mesmo aplicativo cliente. Esse comportamento pode ser usado para converter notificações independentes de plataforma em mais notificações. Por exemplo, a mesma mensagem independente de plataforma para o Hub de notificação pode ser convertida diretamente em um alerta do sistema e uma atualização de bloco, sem exigir que o back-end esteja ciente dela. Algumas plataformas (por exemplo, iOS) podem recolher várias notificações para o mesmo dispositivo se elas forem enviadas em um curto período de tempo.

## <a name="using-templates-for-personalization"></a>Como usar modelos para personalização

Outra vantagem de usar modelos é a capacidade de usar os Hubs de Notificação para realizar a personalização por registro de notificações. Por exemplo, considere um aplicativo meteorológico que exibe um bloco com as condições meteorológicos em um local específico. Um usuário pode escolher entre graus Celsius ou Fahrenheit e uma previsão única ou de cinco dias. Ao usar modelos, cada instalação do aplicativo cliente pode registrar para o formato necessário (1 dia Celsius, 1 dia Fahrenheit, 5 dias Celsius, 5 dias Fahrenheit) e o back-end pode enviar uma única mensagem que contenha todas as informações necessárias para preencher esses modelos (por exemplo, uma previsão de cinco dias com graus Celsius e Fahrenheit).

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

A mensagem enviada para o Hub de notificação contém todas as seguintes propriedades:

| day1_image | day2_image | day3_image | day4_image | day5_image |
|------------|------------|------------|------------|------------|
| day1_tempC | day2_tempC | day3_tempC | day4_tempC | day5_tempC |
| day1_tempF | day2_tempF | day3_tempF | day4_tempF | day5_tempF |

Ao usar esse padrão, o back-end envia apenas uma única mensagem sem ter que armazenar opções de personalização específicas para os usuários do aplicativo. A figura a seguir ilustra esse cenário:

![Diagrama mostrando como o back-end envia apenas uma única mensagem para cada plataforma.](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Como registrar modelos

Para registrar com modelos usando o modelo de instalação (preferencial) ou o modelo de registro, consulte [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Linguagem de expressão do modelo

Os modelos são limitados aos formatos de documento XML ou JSON. Além disso, você só pode colocar expressões em locais específicos; por exemplo, atributos de nó ou valores para XML, valores de propriedade de cadeia de caracteres para JSON.

A tabela a seguir mostra a linguagem permitida nos modelos:

| Expression       | Descrição |
| ---------------- | --- |
| $(prop)          | Referência para uma propriedade de evento com o nome fornecido. Os nomes de propriedade não diferenciam maiúsculas de minúsculas. Esta expressão é convertida para o valor de texto da propriedade ou em uma sequência de caracteres vazia se a propriedade não estiver presente. |
|$(prop, n)       | Como consta acima, mas o texto é explicitamente cortado em n caracteres, por exemplo, $(title, 20) corta o conteúdo da propriedade de título em 20 caracteres. |
| .(prop, n)      | Como consta acima, mas o texto é sufixado com três pontos quando é cortado. O tamanho total da cadeia de caracteres recortada e o sufixo não excedem n caracteres. (título, 20) com uma propriedade de entrada de "esta é a linha de título" resulta neste **é o título...** |
| %(prop)          | Semelhante ao $(name), exceto que a saída é codificada para URI. |
| #(prop)          | Usada em modelos JSON (por exemplo, para modelos iOS e Android).<br><br>Essa função funciona exatamente da mesma forma que ' $ (prop) ' especificada anteriormente, exceto quando usada em modelos JSON (por exemplo, modelos da Apple). Nesse caso, se essa função não estiver entre "{", "}" (por exemplo, "myjsonproperty": "# (Name)") e for avaliada como um número no formato JavaScript, por exemplo, RegExp: (0&#124; (&#91;1-9&#93;&#91;0-9&#93; *)) ( \.&#91;0-9&#93;+)? ( (e&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, em seguida, o JSON de saída é um número.<br><br>Por exemplo, ' Emblema: ' # (Name) ' torna-se ' crachá ': 40 (e não ' 40 '). |
| ' texto ' ou "texto" | Um literal. Literais contêm texto arbitrário entre aspas simples ou duplas. |
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

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre os hubs de notificação do Azure](notification-hubs-push-notification-overview.md)
