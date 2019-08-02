---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/11/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 099bca7483100da1a4ee2f8f10057c416ad145b0
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841423"
---
Você pode aplicar marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome/valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção.

Depois de aplicar marcas, você pode recuperar todos os recursos em sua assinatura com esse nome e valor de marca. As marcas permitem que você recupere recursos relacionados de diferentes grupos de recursos. Esta abordagem será útil quando você precisar organizar os recursos de gerenciamento ou de cobrança.

Sua taxonomia deve considerar uma estratégia de marcação de metadados de autoatendimento, além de uma estratégia de marcação automática para reduzir a carga nos usuários e aumentar a precisão.

As seguintes limitações se aplicam a marcas:

* Nem todos os tipos de recursos suportam tags. Para determinar se você pode aplicar uma tag a um tipo de recurso, consulte [Suporte a tags para recursos do Azure](../articles/azure-resource-manager/tag-support.md).
* Cada recurso ou grupo de recursos pode ter um máximo de 50 pares de nome/valor de marca. Atualmente, as contas de armazenamento dão suporte a apenas 15 marcas, mas esse limite pode ser aumentado para 50 em uma versão futura. Se você precisar aplicar marcas mais que o número máximo permitido, use uma cadeia de caracteres JSON para o valor da marca. A cadeia de caracteres JSON pode conter diversos valores que são aplicados a um único nome de marca. Um grupo de recursos pode conter muitos recursos que possuem 50 pares de nome/valor de marca.
* O nome da marca é limitado a 512 caracteres e o valor da marca é limitado a 256 caracteres. Para contas de armazenamento, o nome da marca é limitado a 128 caracteres e o valor da marca é limitado a 256 caracteres.
* Não generalizadas não dão suporte a marcas.
* Marcas aplicadas ao grupo de recursos não são herdadas pelos recursos desse grupo de recursos.
* As marcas não podem ser aplicadas a recursos clássicos como Serviços de Nuvem.
* Os nomes das marcas não podem conter esses caracteres: `<`, `>`, `%`, `&`, `\`, `?`, `/`
