---
title: Modelos de problemas no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como personalizar o conteúdo das páginas de Problemas no portal do desenvolvedor do Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: c70e88a572bc261ad9d25f9a742b1987d0c19b44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656656"
---
# <a name="issue-templates-in-azure-api-management"></a>Modelos de problemas no Gerenciamento de API do Azure
O Gerenciamento de API do Azure fornece a capacidade de personalizar o conteúdo das páginas do portal do desenvolvedor usando um conjunto de modelos que configura o respectivo conteúdo. Usando a sintaxe [DotLiquid](http://dotliquidmarkup.org/) e o editor de sua escolha, como o [DotLiquid para Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), bem como um conjunto fornecido de [Recursos de cadeia de caracteres](api-management-template-resources.md#strings), [Recursos do Glyph](api-management-template-resources.md#glyphs) e [Controles de página](api-management-page-controls.md) localizados, você tem grande flexibilidade para configurar o conteúdo das páginas, conforme a necessidade, usando esses modelos.  
  
 Os modelos desta seção permitem personalizar o conteúdo das páginas de problemas no portal do desenvolvedor.  
  
-   [Lista de problemas](#IssueList)  
  
> [!NOTE]
>  Os modelos de amostra padrão estão incluídos na documentação a seguir, mas estão sujeitos à alteração devido a melhorias contínuas. Você pode exibir os modelos padrão em tempo real no portal do desenvolvedor, navegando até os modelos individuais desejados. Para saber mais sobre como trabalhar com modelos, consulte [Como personalizar o portal de desenvolvedor de Gerenciamento de API usando modelos](api-management-developer-portal-templates.md).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="IssueList"></a> Lista de problemas  
 O modelo **Lista de problemas** possibilita personalizar o corpo da página de lista de problemas no portal do desenvolvedor.  
  
 ![Lista de problemas do portal do desenvolvedor](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "Lista de problemas do portal do desenvolvedor do APIM")  
  
### <a name="default-template"></a>Modelo padrão  
  
```xml
<div class="row">
  <div class="col-md-9">
    <h2>{% localized "IssuesStrings|WebIssuesIndexTitle" %}</h2>
  </div>
</div>
<div class="row">
  <div class="col-md-12">
    {% if issues.size > 0 %}
    <ul class="list-unstyled">
      {% capture reportedBy %}{% localized "IssuesStrings|WebIssuesStatusReportedBy" %}{% endcapture %}
      {% assign replaceString0 = '{0}' %}
      {% assign replaceString1 = '{1}' %}
      {% for issue in issues %}
      <li>
        <h3>
          <a href="/issues/{{issue.id}}">{{issue.title}}</a>
        </h3>
        <p>{{issue.description}}</p>
        <em>
          {% capture state %}{{issue.issueState}}{% endcapture %}
          {% capture devName %}{{issue.subscriptionDeveloperName}}{% endcapture %}
          {% capture str1 %}{{ reportedBy | replace : replaceString0, state }}{% endcapture %}
          {{ str1 | replace : replaceString1, devName }}
          <span class="UtcDateElement">{{ issue.reportedOn | date: "r" }}</span>
        </em>
      </li>
      {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    {% if canReportIssue %}
    <a class="btn btn-primary" id="createIssue" href="/Issues/Create">{% localized "IssuesStrings|WebIssuesReportIssueButton" %}</a>
    {% elsif isAuthenticated %}
    <hr />
    <p>{% localized "IssuesStrings|WebIssuesNoActiveSubscriptions" %}</p>
    {% else %}
    <hr />
    <p>
      {% capture signIntext %}{% localized "IssuesStrings|WebIssuesNotSignin" %}{% endcapture %}
      {% capture link %}<a href="/signin">{% localized "IssuesStrings|WebIssuesSignIn" %}</a>{% endcapture %}
      {{ signIntext | replace : replaceString0, link }}
    </p>
    {% endif %}
  </div>
</div>
```
  
### <a name="controls"></a>Controles  
 O modelo `Issue list` pode usar os seguintes [controles de página](api-management-page-controls.md).  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`Issues`|Coleção de entidades de [problemas](api-management-template-data-model-reference.md#Issue).|Os problemas visíveis para o usuário atual.|  
|`Paging`|Entidade de [paginação](api-management-template-data-model-reference.md#Paging).|As informações de paginação da coleção de aplicativos.|  
|`IsAuthenticated`|boolean|Se o usuário atual está conectado ao portal do desenvolvedor.|  
|`CanReportIssues`|boolean|Se o usuário atual tem permissões para arquivar um problema.|  
|`Search`|string|Essa propriedade foi preterida e não deve ser usada.|  
  
### <a name="sample-template-data"></a>Amostra de dados do modelo  
  
```json
{
    "Issues": [
        {
            "Id": "5702b68bb16653124c8f9ba7",
            "ApiId": "570275f1b16653124c8f9ba3",
            "Title": "I couldn't figure out how to connect my application to the API",
            "Description": "I'm having trouble connecting my application to the backend API.",
            "SubscriptionDeveloperName": "Clayton",
            "IssueState": "Proposed",
            "ReportedOn": "2016-04-04T18:46:35.64",
            "Comments": null,
            "Attachments": null,
            "Services": null
        }
    ],
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 1,
        "ShowAll": false,
        "PageCount": 1
    },
    "IsAuthenticated": true,
    "CanReportIssue": true,
    "Search": null
}
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como trabalhar com modelos, consulte [Como personalizar o portal de desenvolvedor de Gerenciamento de API usando modelos](api-management-developer-portal-templates.md).
