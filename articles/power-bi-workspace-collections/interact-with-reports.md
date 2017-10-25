---
title: "Interagir com relatórios usando a API JavaScript | Microsoft Docs"
description: "A API JavaScript do Power BI permite que você insira relatórios do Power BI com facilidade em seus aplicativos."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: bdd885d3-1b00-4dcf-bdff-531eb1f97bfb
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 62a95807c35fcba15a8e5ffdf340a307dd22a642
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interagir com relatórios do Power BI usando a API JavaScript

A API JavaScript do Power BI permite que você insira relatórios do Power BI com facilidade em seus aplicativos. Com a API, seus aplicativos podem interagir programaticamente com diferentes elementos do relatório, como páginas e filtros. Essa interatividade faz com que os relatórios do Power BI sejam uma parte mais integrada do seu aplicativo.

> [!IMPORTANT]
> As Coleções do Espaço de Trabalho do Power BI foram preteridas e só estarão disponíveis até junho de 2018 ou conforme a indicação do seu contrato. Recomendamos planejar a migração para o Power BI Embedded a fim de evitar interrupções em seu aplicativo. Para saber mais sobre como migrar seus dados para o Power BI Embedded, confira [Como migrar o conteúdo das Coleções do Espaço de Trabalho do Power BI para o Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Você pode inserir um relatório do Power BI em seu aplicativo usando um iframe hospedado como parte do aplicativo. O iframe age como um limite entre seu aplicativo e o relatório, como você pode ver na imagem a seguir:

![iframe da Coleção de Espaços de Trabalho do Power BI sem API do Javascript](media/interact-with-reports/iframe-without-javacript.png)

O iframe torna o processo de incorporação muito mais fácil, mas sem a API JavaScript o relatório e o seu aplicativo não podem interagir entre si. Essa falta de interação pode dar impressão de que o relatório não faz realmente parte do aplicativo. O relatório e o aplicativo realmente precisam se comunicar, como na imagem a seguir:

![iframe da Coleção de Espaços de Trabalho do Power BI com API do Javascript](media/interact-with-reports/iframe-with-javascript.png)

A API JavaScript do Power BI permite que você escreva código que possa passar com segurança pelo limite do iframe. Isso permite que seu aplicativo execute programaticamente uma ação em um relatório e escute eventos de ações que os usuários fazem no relatório.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>O que você pode fazer com a API JavaScript do Power BI?

Com a API JavaScript você pode gerenciar relatórios, navegar pelas páginas de um relatório, filtrar um relatório e manipular eventos de inserção. O diagrama a seguir mostra a estrutura da API.

![Diagrama da API JavaScript do Power BI](media/interact-with-reports/javascript-api-diagram.png)

### <a name="manage-reports"></a>Gerenciar relatórios
A API Javascript permite que você gerencie o comportamento no nível de página e de relatório:

* Insira um Relatório específico do Power BI com segurança em seu aplicativo - experimente o [aplicativo de demonstração inserção](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  * Definir token de acesso
* Configurar o relatório
  * Habilite e desabilite o painel de filtro e o painel de navegação de página - experimente o [aplicativo de demonstração de configurações de atualização](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  * Defina padrões para páginas e filtros - experimente o [aplicativo para definir padrões](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
* Entrar e sair do modo de tela inteira

[Saiba mais sobre como inserir um relatório](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>Navegar pelas páginas de um relatório
A API JavaScript permite que você descubra todas as páginas de um relatório e defina a página atual. Experimente o [aplicativo de demonstração de navegação](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Saiba mais sobre a navegação de página](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtrar um relatório
A API JavaScript fornece recursos básicos e avançados de filtragem para relatórios inseridos e páginas de relatório. Experimente o [aplicativo de demonstração de filtragem](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)e examine alguns códigos introdutórios aqui.

#### <a name="basic-filters"></a>Filtros básicos
Um filtro básico é colocado em uma coluna ou nível hierárquico e contém uma lista de valores a serem incluídos ou excluídos.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```

#### <a name="advanced-filters"></a>Filtros avançados
Os filtros avançados usam o operador lógico E ou OU e aceitam uma ou duas condições, cada uma com seus próprios operador e valor. As condições com suporte são:

* Nenhum
* LessThan
* LessThanOrEqual
* GreaterThan
* GreaterThanOrEqual
* Contém:
* DoesNotContain
* StartsWith
* DoesNotStartWith
* Is
* IsNot
* IsBlank
* IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```

[Saiba mais sobre filtragem](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### <a name="handling-events"></a>Manipulação de eventos

Além de enviar informações para o iframe, seu aplicativo também pode receber informações sobre os seguintes eventos provenientes do iframe:

* Inserir
  * carregado
  * error
* Relatórios
  * pageChanged
  * dataSelected (em breve)

[Saiba mais sobre a manipulação de eventos](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a API JavaScript do Power BI, confira os links a seguir:

* [Wiki da API JavaScript](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [Referência de modelo de objeto](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* [Demonstração ao vivo](https://microsoft.github.io/PowerBI-JavaScript/demo/)