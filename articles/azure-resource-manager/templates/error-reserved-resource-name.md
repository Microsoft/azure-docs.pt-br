---
title: Erros de nome de recurso reservado
description: Descreve como resolver erros ao fornecer um nome de recurso que inclui uma palavra reservada.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75477623"
---
# <a name="resolve-reserved-resource-name-errors"></a>Resolver erros de nome de recurso reservado

Este artigo descreve o erro encontrado durante a implantação de um recurso que inclui uma palavra reservada em seu nome.

## <a name="symptom"></a>Sintoma

Ao implantar um recurso que está disponível por meio de um ponto de extremidade público, você poderá receber o seguinte erro:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Causa

Recursos que têm um ponto de extremidade público não podem usar palavras reservadas nem marcas no nome.

As seguintes palavras são reservadas:

* ACCESS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

As seguintes palavras não podem ser usadas como uma palavra inteira ou uma subcadeia de caracteres no nome:

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Solução

Forneça um nome que não use uma das palavras reservadas.