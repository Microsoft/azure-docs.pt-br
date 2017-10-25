---
title: "Referência do mecanismo de regras CDN do Azure | Microsoft Docs"
description: "Documentação de referência para regras e recursos de condições de correspondência do mecanismo da CDN do Azure."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: c10145661a8c575381493c9aaa901c3ef92c2e81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine"></a>Mecanismo de regras da CDN do Azure
Este tópico lista descrições detalhadas dos recursos e das condições de correspondência disponíveis para o [mecanismo de regras](cdn-rules-engine.md)da CDN (Rede de Distribuição de Conteúdo do Azure)

O Mecanismo de Regras de HTTP é projetado para ser a autoridade final sobre como tipos específicos de solicitações são processados pela CDN.

**Usos comuns**:

- Substituir ou definir uma política de cache personalizada.
- Proteger ou negar solicitações de conteúdo confidencial.
- Solicitações de redirecionamento.
- Armazenar dados de log personalizados.

## <a name="terminology"></a>Terminologia
Uma regra é definida com o uso de [**expressões condicionais**](cdn-rules-engine-reference-conditional-expressions.md), [**correspondências**](cdn-rules-engine-reference-match-conditions.md) e [**recursos**](cdn-rules-engine-reference-features.md). Esses elementos são realçados na ilustração a seguir.

 ![Condição de correspondência CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Sintaxe

A maneira pela qual os caracteres especiais serão tratados varia dependendo de como um recurso ou uma condição de correspondência lida com valores de texto. Uma condição de correspondência ou recurso pode interpretar o texto de uma das seguintes maneiras:

1. [**Valores literais**](#literal-values) 
2. [**Valores de caractere curinga**](#wildcard-values)
3. [**Expressões regulares**](#regular-expressions)

### <a name="literal-values"></a>Valores literais
Um texto interpretado como um valor literal tratará todos os caracteres especiais, exceto o símbolo %, como parte do valor que deve ser correspondido. Em outras palavras, uma condição de correspondência literal definida como `\'*'\` será satisfeita somente quando esse valor exato (ou seja, `\'*'\`) for encontrado.
 
Um símbolo de porcentagem é usado para indicar a codificação de URL (por exemplo, `%20`).

### <a name="wildcard-values"></a>Valores de caractere curinga
Um texto que é interpretado como um valor curinga atribuirá significado adicional a caracteres especiais. A tabela a seguir descreve como o conjunto de caracteres a seguir será interpretado.

Character | Descrição
----------|------------
\ | Uma barra invertida é usada para funcionar como escape para qualquer um dos caracteres especificados nesta tabela. Uma barra invertida deve ser especificada diretamente antes do caractere especial que deve ter escape.<br/>Por exemplo, a seguinte sintaxe ignora um asterisco: `\*`
% | Um símbolo de porcentagem é usado para indicar a codificação de URL (por exemplo, `%20`).
* | Um asterisco é um caractere curinga que representa um ou mais caracteres.
Espaço | Um caractere de espaço indica que uma condição de correspondência pode ser atendida por qualquer um dos valores especificados ou padrões.
'valor' | As aspas simples não têm significado especial. No entanto, um conjunto de aspas simples é usado para indicar que um valor deve ser tratado como um valor literal. Ele pode ser usado das seguintes maneiras:<br><br/>- Permite que uma condição de correspondência seja satisfeita sempre que o valor especificado corresponder a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer uma das seguintes cadeias de caracteres: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- Permite que um caractere especial seja especificado como um caractere literal. Por exemplo, você pode especificar um caractere de espaço literal colocando um caractere de espaço dentro de um conjunto de aspas simples (ou seja, `' '` ou `'sample value'`).<br/>- Permite a especificação de um valor em branco. Especifique um valor em branco especificando um conjunto de aspas simples (ou seja, '').<br /><br/>**Importante:**<br/>- Se o valor especificado não contiver um caractere curinga, ele será automaticamente considerado um valor literal. Isso significa que não é necessário especificar um conjunto de aspas simples.<br/>- Se uma barra invertida não funcionar como escape para outro caractere nesta tabela, ele será ignorado quando especificado dentro de um conjunto de aspas simples.<br/>- Outra maneira de especificar um caractere especial, como um caractere literal, é isolá-lo usando uma barra invertida (ou seja, `\`).

### <a name="regular-expressions"></a>Expressões regulares

As expressões regulares definem um padrão que será pesquisado em um valor de texto. A notação de expressão regular define significados específicos para uma variedade de símbolos. A tabela a seguir indica como os caracteres especiais são tratados por condições de correspondência e recursos que dão suporte a expressões regulares.

Caractere especial | Descrição
------------------|------------
\ | Uma barra invertida funciona como escape para o caractere que a segue. Isso faz com que esse caractere seja tratado como um valor literal em vez de usar seu significado da expressão regular. Por exemplo, a seguinte sintaxe ignora um asterisco: `\*`
% | O significado de um símbolo de porcentagem depende de seu uso.<br/><br/> `%{HTTPVariable}`: essa sintaxe identifica uma variável HTTP.<br/>`%{HTTPVariable%Pattern}`: essa sintaxe usa um símbolo de porcentagem para identificar uma variável HTTP e como um delimitador.<br />`\%`: usar escape em um símbolo de porcentagem permite que ele seja usado como um valor literal ou indique a codificação de URL (por exemplo, `\%20`).
* | Um asterisco permite que o caractere precedente corresponda a zero ou mais vezes. 
Espaço | Normalmente, um caractere de espaço é tratado como um caractere literal. 
'valor' | Aspas simples são tratadas como caracteres literais. Um conjunto de aspas simples não tem significado especial.


## <a name="next-steps"></a>Próximas etapas
* [Condições de correspondência do Mecanismo de regras](cdn-rules-engine-reference-match-conditions.md)
* [Expressões condicionais do Mecanismo de regras](cdn-rules-engine-reference-conditional-expressions.md)
* [Recursos do Mecanismo de Regras](cdn-rules-engine-reference-features.md)
* [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-rules-engine.md)
* [Visão geral da CDN do Azure](cdn-overview.md)
