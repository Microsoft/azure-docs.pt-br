---
title: Introdução ao controle de mapa da Web do Microsoft Azure Maps
description: Descubra como adicionar mapas a aplicativos Web e móveis usando o Controle de Mapeamento biblioteca JavaScript do lado do cliente no Azure Maps. Consulte Como localizar mapas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: b283cacf94ff3567428787e50a3d7db9ba91fd9c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864033"
---
# <a name="use-the-azure-maps-map-control"></a>Usar o controle de mapeamento do Azure Mapas

O Controle de Mapeamento biblioteca JavaScript do lado do cliente permite renderizar mapas e a funcionalidade do Azure Maps incorporada em seu aplicativo Web ou móvel.

Esta documentação usa o SDK da Web do Azure Maps, no entanto, os serviços do Azure Maps podem ser usados com qualquer controle de mapa. [Aqui](open-source-projects.md#third-part-map-control-plugins) estão alguns controles de mapa de software livre populares para os quais a equipe do Azure Mapas criou plug-ins.

## <a name="prerequisites"></a>Pré-requisitos

Para usar o Controle de Mapeamento em uma página da Web, você deve ter um dos seguintes pré-requisitos:

* [Faça uma conta do Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account) e [obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura.

* Obtenha suas credenciais do Azure Active Directory (AAD) com [as opções de autenticação](/javascript/api/azure-maps-control/atlas.authenticationoptions).

## <a name="create-a-new-map-in-a-web-page"></a>Criar um novo mapa em uma página da web

Você pode inserir um mapa em uma página da Web usando o Controle de Mapeamento biblioteca JavaScript do lado do cliente.

1. Criar um novo arquivo HTML.

2. Carregar no SDK Web do Azure Mapas. Escolha uma das duas opções:

    * Use a versão da CDN hospedada globalmente do SDK da Web do Azure Maps adicionando referências ao JavaScript e à folha `<head>` de estilos no elemento do arquivo HTML:

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * Carregue o código-fonte do SDK da Web do Azure Maps localmente usando o pacote [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) NPM e hospede-o com seu aplicativo. Esse pacote também inclui definições de TypeScript.

      > **npm install azure-maps-control**

    Em seguida, adicione referências à folha de estilos do Azure Maps ao `<head>` elemento do arquivo:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > As definições do typescript podem ser importadas para seu aplicativo adicionando o seguinte código:
    >
    > ```javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Para renderizar o mapa de modo que ele preencha a página inteira, adicione o seguinte elemento `<style>` ao elemento `<head>`.

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. No corpo da página, adicione um elemento `<div>` e dê a ele um `id` de **myMap**.

   ```HTML
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. Agora, inicializaremos o controle de mapa. Para autenticar o controle, você precisará ter uma chave de assinatura do Azure Maps ou usar as credenciais do Azure Active Directory (AAD) com [as opções de autenticação](/javascript/api/azure-maps-control/atlas.authenticationoptions).

    Se você estiver usando uma chave de assinatura para autenticação, copie e cole o seguinte elemento script dentro do `<head>` elemento e abaixo do primeiro `<script>` elemento. Substitua `<Your Azure Maps Key>` pela chave de assinatura primária do Azure Maps.

     ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
       }
    </script>
    ```

    Se você estiver usando Azure Active Directory (AAD) para autenticação, copie e cole o seguinte elemento script dentro do `<head>` elemento e abaixo do primeiro `<script>` elemento.

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'aad',
                    clientId: '<Your AAD Client Id>',
                    aadAppId: '<Your AAD App Id>',
                    aadTenant: '<Your AAD Tenant Id>'
                }
            });
        }
    </script>
   ```

    Para obter mais informações sobre a autenticação com o Azure Maps, consulte o documento [autenticação com o Azure Maps](azure-maps-authentication.md) . Além disso, uma lista de exemplos que mostram como integrar Azure Active Directory (AAD) com mapas do Azure pode ser encontrada [aqui](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

    >[!TIP]
    >Neste exemplo, passamos o `id` do mapa `<div>` . Outra maneira de fazer isso é passar o `HTMLElement` objeto passando `document.getElementById('myMap')` como o primeiro parâmetro.

6. Opcionalmente, você pode achar útil adicionar os seguintes `meta` elementos ao `head` elemento da página:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Juntando tudo isso, o arquivo HTML deve ser semelhante à marcação a seguir:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

8. Abra o arquivo no seu navegador da Web e exiba o mapa renderizado. Ele deve ser semelhante à imagem abaixo:

   ![Imagem do mapa mostrando resultado renderizado](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Como localizar o mapa

O mapas do Azure fornece duas maneiras diferentes de definir o idioma e a exibição regional para o mapa renderizado. A primeira opção é adicionar essas informações ao `atlas` namespace global, o que resultará em todas as instâncias de controle de mapa em seu aplicativo padronizando essas configurações. O seguinte define o idioma como francês ("fr-FR") e a exibição regional como "Automática":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

A segunda opção é passar essas informações para as opções de mapa ao carregar o mapa da seguinte maneira:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!NOTE]
> É possível carregar várias instâncias de mapa na mesma página com diferentes configurações de idioma e região. Além disso, essas configurações podem ser atualizadas depois que o mapa é carregado usando a `setStyle` função do mapa.

Aqui está um exemplo de mapas do Azure com o idioma definido como "fr-FR" e a exibição regional definida como "auto".

![Imagem do mapa mostrando rótulos em francês](./media/how-to-use-map-control/websdk-localization.png)

Uma lista completa de idiomas e exibições regionais compatíveis está documentada [aqui](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Suporte de nuvem do Azure governamental

O SDK da Web do Azure Maps dá suporte à nuvem do Azure governamental. Todas as URLs de JavaScript e CSS usadas para acessar o SDK da Web do Azure Maps permanecem as mesmas. As tarefas a seguir precisarão ser feitas para se conectar à versão de nuvem do Azure governamental da plataforma do Azure Maps.

Ao usar o controle de mapa interativo, adicione a seguinte linha de código antes de criar uma instância da `Map` classe.

```javascript
atlas.setDomain('atlas.azure.us');
```

Certifique-se de usar os detalhes de autenticação do Azure Maps da plataforma de nuvem do Azure governamental ao autenticar o mapa e os serviços.

Ao usar o módulo de serviços, o domínio para os serviços precisa ser definido ao criar uma instância de um ponto de extremidade de URL de API. Por exemplo, o código a seguir cria uma instância da `SearchURL` classe e aponta o domínio para a nuvem do Azure governamental.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Se acessar diretamente os serviços REST do Azure Maps, altere o domínio da URL para `atlas.azure.us` . Por exemplo, se estiver usando o serviço de API de pesquisa, altere o domínio de URL de `https://atlas.microsoft.com/search/` para `https://atlas.azure.us/search/` .

## <a name="javascript-frameworks"></a>Estruturas do JavaScript

Se você estiver desenvolvendo com uma estrutura JavaScript, um dos seguintes projetos open-source pode ser útil:

* [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps) – um wrapper Angular 10 alternativo nos mapas do Azure.
* [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) – um componente Blazor dos Azure Mapas.
* [Componente React do Azure Mapas](https://github.com/WiredSolutions/react-azure-maps) – um wrapper React para o controle dos Azure Mapas.
* [Vue Azure Mapas](https://github.com/rickyruiz/vue-azure-maps) – um componente dos Azure Mapas para o aplicativo Vue.

## <a name="next-steps"></a>Próximas etapas

Saiba como criar e interagir com um mapa:

> [!div class="nextstepaction"]
> [Criar um mapa](map-create.md)

Saiba como definir o estilo de um mapa:

> [!div class="nextstepaction"]
> [Escolher um estilo de mapa](choose-map-style.md)

Conheça as práticas recomendadas e veja exemplos:

> [!div class="nextstepaction"]
> [Práticas recomendadas](web-sdk-best-practices.md)

> [!div class="nextstepaction"]
> [Exemplos de código](/samples/browse/?products=azure-maps)

Para obter uma lista de exemplos que mostram como integrar o Azure Active Directory (AAD) com o Azure Maps, consulte:

> [!div class="nextstepaction"]
> [Exemplos de autenticação do Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
