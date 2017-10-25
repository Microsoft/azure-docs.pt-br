---
title: Construindo cadeias de caracteres de filtro para o designer de tabela | Microsoft Docs
description: Construindo cadeias de caracteres de filtro para o designer de tabela
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: a1a10ea1-687a-4ee1-a952-6b24c2fe1a22
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: kraigb
ms.openlocfilehash: 069224d84462b4955912ce1462a65298a5acc04a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="constructing-filter-strings-for-the-table-designer"></a>Construindo cadeias de caracteres de filtro para o designer de tabela
## <a name="overview"></a>Visão geral
Para filtrar dados em uma tabela do Azure que é exibida no **Table Designer**do Visual Studio, construa uma cadeia de caracteres de filtro e insira no campo de filtro. A sintaxe de cadeia de caracteres de filtro é definida pelo WCF Data Services e é semelhante a uma cláusula SQL WHERE, mas é enviada para o serviço Tabela por meio de uma solicitação HTTP. O **Designer de tabela** lida com a codificação correta para você, portanto para filtrar um valor da propriedade desejada, você só precisa digitar o nome da propriedade, operador de comparação, valor dos critérios e, opcionalmente, o operador booliano no campo de filtro. Não é necessário incluir a opção de consulta $filter como você faria se estivesse criando uma URL para consultar a tabela por meio de [Referência de API de REST de serviços de armazenamento](http://go.microsoft.com/fwlink/p/?LinkId=400447).

O WCF Data Services é baseado no [Open Data Protocol](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). Para obter detalhes sobre a opção de consulta de sistema do filtro (**$filter**), consulte a [Especificação de convenções de URI do OData](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Operadores de comparação
Os seguintes operadores lógicos têm suporte para todos os tipos de propriedade:

| Operador lógico | Descrição | Cadeia de caracteres de filtro de exemplo |
| --- | --- | --- |
| eq |Igual a |City eq 'Redmond' |
| gt |Maior que |Preço gt 20 |
| ge |Maior ou igual a |Preço ge 10 |
| lt |Menor que |Preço lt 20 |
| le |Menor ou igual a |Preço le 100 |
| ne |Diferente de |City ne 'London' |
| e |e |Preço le 200 e Preço gt 3,5 |
| ou o |ou o |Preço le 3,5 ou Preço gt 200 |
| não |não |não isAvailable |

Ao construir uma cadeia de caracteres de filtro, as regras a seguir são importantes:

* Use os operadores lógicos para comparar uma propriedade a um valor. Observe que não é possível comparar uma propriedade a um valor dinâmico; um lado da expressão deve ser uma constante.
* Todas as partes da cadeia de caracteres de filtro diferenciam maiúsculas de minúsculas.
* O valor da constante deve ser do mesmo tipo de dados como a propriedade para que o filtro retorne resultados válidos. Para obter informações sobre tipos de propriedades com suporte, consulte [Noções básicas sobre o modelo de dados do serviço Tabela](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Filtrando nas propriedades de cadeia de caracteres
Ao filtrar nas propriedades de cadeia de caracteres, coloque a constante de cadeia de caracteres entre aspas simples.

O exemplo a seguir filtra as propriedades **PartitionKey** e **RowKey**; propriedades adicionais não chave também podem ser adicionadas à cadeia de caracteres de filtro:

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Você pode colocar cada expressão de filtro entre parênteses, embora não seja necessário:

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Observe que o serviço Tabela não dá suporte a consultas de curingas e eles também não têm suporte no Designer de tabela. No entanto, você pode executar correspondência de prefixo usando os operadores de comparação no prefixo desejado. O exemplo a seguir retorna as entidades com a propriedade LastName começando com a letra 'A':

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>Filtrando em propriedades numéricas
Para filtrar um número inteiro ou um número de ponto flutuante, especifique o número sem aspas.

Este exemplo retorna todas as entidades com uma propriedade Idade cujo valor é maior que 30:

    Age gt 30

Este exemplo retorna todas as entidades com uma propriedade AmountDue cujo valor é menor ou igual a 100,25:

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>Filtrando em Propriedades Boolianas
Para filtrar um valou o booliano, especifique **true** ou o **false** sem aspas.

O exemplo a seguir retorna todas as entidades em que a propriedade IsActive é definida como **true**:

    IsActive eq true

Você também pode escrever essa expressão de filtro sem o operador lógico. No exemplo a seguir, o serviço Tabela também retornará todas as entidades nas quais IsActive é **true**:

    IsActive

Para retornar todas as entidades nas quais IsActive for false, você pode usar o operador não:

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Filtrando em Propriedades DateTime
Para filtrar um valor DateTime, especifique a palavra-chave **datetime** , seguida pela constante de data/hora entre aspas. A constante de data/hora deve estar no formato UTC combinado, conforme descrito em [Formatando valores de propriedade DateTime](http://go.microsoft.com/fwlink/p/?LinkId=400449).

O exemplo a seguir retorna entidades nas quais a propriedade CustomerSince é igual a 10 de julho de 2008:

    CustomerSince eq datetime'2008-07-10T00:00:00Z'
