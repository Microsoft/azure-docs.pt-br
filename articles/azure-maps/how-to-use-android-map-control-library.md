---
title: Introdução ao controle de mapa do Android | Microsoft Azure Mapas
description: Familiarize-se com o SDK do Android do Azure Maps. Veja como criar um projeto no Android Studio, instalar o SDK e criar um mapa interativo.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: a60ee8faf8d19afba59e46c52aaba9395c3a5292
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604440"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Introdução ao SDK do Android do Azure Mapas

O SDK do Android do Azure Mapas é uma biblioteca de mapas vetoriais para Android. Este artigo orienta você pelos processos de instalação do SDK do Android do Azure Mapas e de carregamento de um mapa.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de concluir as etapas no documento [início rápido: criar um aplicativo Android](quick-android-map.md) .

## <a name="localizing-the-map"></a>Como localizar o mapa

O SDK do Android do Azure Mapas fornece três maneiras diferentes de definir o idioma e a exibição regional do mapa. O código a seguir mostra como definir o idioma como francês ("fr-FR") e a exibição regional como "auto".

A primeira opção é passar as informações de idioma e exibição regional para a classe `AzureMaps` usando os métodos `setLanguage` e `setView` estáticos globalmente. Isso definirá o idioma padrão e a exibição regional em todos os controles do Azure Mapas carregados em seu aplicativo.

::: zone pivot="programming-language-java-android"

```java
static {
    //Alternatively use Azure Active Directory authenticate.
    AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

    //Set your Azure Maps Key.
    //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");   

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
companion object {
    init {
        //Alternatively use Azure Active Directory authenticate.
        AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

        //Set your Azure Maps Key.
        //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

A segunda opção é passar as informações de idioma e de exibição para o XML de controle de mapeamento.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

A terceira opção é definir programaticamente o idioma e a exibição regional do mapa usando o método `setStyle` dos mapas. Isso pode ser feito a qualquer momento para alterar o idioma e a exibição regional do mapa.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl.onReady(OnReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
})
```

::: zone-end

Aqui está um exemplo de mapas do Azure com a linguagem definida como "fr-FR" e exibição regional definida como "auto".

![Azure Mapas, imagem de mapa mostrando rótulos em francês](media/how-to-use-android-map-control-library/android-localization.png)

Uma lista completa de idiomas e exibições regionais compatíveis está documentada [aqui](supported-languages.md).

## <a name="navigating-the-map"></a>Navegar no mapa

Há várias maneiras diferentes de ampliar, deslocar, girar e inclinar o mapa. A seguir, são detalhadas todas as diferentes maneiras de navegar no mapa.

**Ampliar o mapa**

* Toque no mapa com dois dedos e junte-os para reduzir, ou afaste-os para ampliar.
* Toque duas vezes no mapa para ampliar um nível.
* Toque duas vezes com dois dedos para reduzir o mapa um nível.
* Toque duas vezes. No segundo toque, mantenha seu dedo no mapa e arraste para cima para ampliar ou para baixo para reduzir.

**Deslocar o mapa**

* Toque no mapa e arraste em qualquer direção.

**Girar o mapa**

* Toque no mapa com dois dedos e gire.

**Inclinar o mapa**

* Toque no mapa com dois dedos e arraste-os para cima ou para baixo juntos.

## <a name="azure-government-cloud-support"></a>Suporte de nuvem do Azure governamental

O Azure Maps SDK do Android dá suporte à nuvem do Azure governamental. O SDK do Android do Azure Maps é acessado do mesmo repositório do Maven. As tarefas a seguir precisarão ser feitas para se conectar à versão de nuvem do Azure governamental da plataforma do Azure Maps.

No mesmo local em que os detalhes de autenticação do Azure Maps são especificados, adicione a seguinte linha de código para informar ao mapa para usar o domínio de nuvem do Azure Maps governamental.

::: zone pivot="programming-language-java-android"

```java
AzureMaps.setDomain("atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

Certifique-se de usar os detalhes de autenticação do Azure Maps da plataforma de nuvem do Azure governamental ao autenticar o mapa e os serviços.

## <a name="next-steps"></a>Próximas etapas

Saiba como adicionar dados de sobreposição no mapa:

> [!div class="nextstepaction"]
> [Gerenciar autenticação no Azure Mapas](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Alterar estilos de mapa nos mapas do Android](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](how-to-add-shapes-to-android-map.md)
