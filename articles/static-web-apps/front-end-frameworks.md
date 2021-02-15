---
title: Configurar estruturas de front-end com a visualização de aplicativos Web estáticos do Azure
description: Configurações para estruturas de front-end populares necessárias para aplicativos Web estáticos do Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 14564b0591ef0146131b3f9324556b613e25daac
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901225"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Configurar estruturas e bibliotecas de front-end com a visualização de aplicativos Web estáticos do Azure

Os aplicativos Web estáticos do Azure requerem que você tenha os valores de configuração apropriados no [arquivo de configuração de compilação](github-actions-workflow.md) para sua estrutura ou biblioteca de front-end.

## <a name="configuration"></a>Configuração

A tabela a seguir lista as configurações de uma série de estruturas e bibliotecas<sup>1</sup>.

A intenção das colunas da tabela é explicada pelos seguintes itens:

- **Local de saída**: lista o valor para `output_location` , que é a [pasta para versões compiladas de arquivos de aplicativo](github-actions-workflow.md#build-and-deploy).

- **Comando de compilação personalizada**: quando a estrutura requer um comando diferente de `npm run build` ou `npm run azure:build` , você pode definir um [comando de compilação personalizado](github-actions-workflow.md#custom-build-commands).

| Estrutura | Localização do artefato do aplicativo | Comando de compilação personalizado |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | n/a <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Universal angular](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | n/a |
| [Backbone.js](https://backbonejs.org/) | `/` | n/a |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | n/a |
| [Ember](https://emberjs.com/) | `dist` | n/a |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | n/a |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | n/a |
| [Hyperapp](https://hyperapp.dev/) | `/` | n/a |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | n/a |
| [jQuery](https://jquery.com/) | `/` | n/a |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | n/a |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | n/a |
| [Marko](https://markojs.com/) | `public` | n/a |
| [Meteoro](https://www.meteor.com/) | `bundle` | n/a |
| [Mithril](https://mithril.js.org/) | `dist` | n/a |
| [Polymer](https://www.polymer-project.org/) | `build/default` | n/a |
| [Preact](https://preactjs.com/) | `build` | n/a |
| [React](https://reactjs.org/) | `build` | n/a |
| [Stencil](https://stenciljs.com/) | `www` | n/a |
| [Svelte](https://svelte.dev/) | `public` | n/a |
| [Three.js](https://threejs.org/) | `/` | n/a |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | n/a |
| [Vue.js](https://vuejs.org/) | `dist` | n/a |

<sup>1</sup> a tabela acima não deve ser uma lista completa de estruturas e bibliotecas que funcionam com aplicativos Web estáticos do Azure.

<sup>2</sup> não aplicável

## <a name="next-steps"></a>Próximas etapas

- [Configuração de build e fluxo de trabalho](github-actions-workflow.md)
