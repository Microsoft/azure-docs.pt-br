---
title: "Expressões regulares em pesquisas de logs do Log Analytics do OMS | Microsoft Docs"
description: "É possível usar a palavra-chave RegEx em pesquisas de logs do Log Analytics para filtrar os resultados de acordo com uma expressão regular.  Este artigo fornece a sintaxe para essas expressões com vários exemplos."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: bwren
ms.openlocfilehash: 9746170f157ed5065adc953a31687ff18bd73708
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-regular-expressions-to-filter-log-searches-in-log-analytics"></a>Usando expressões regulares para filtrar pesquisas de logs no Log Analytics

As [Pesquisas de logs](log-analytics-log-searches.md) permitem extrair informações do repositório do Log Analytics.  As [Expressões de filtro](log-analytics-search-reference.md#filter-expressions) permitem filtrar os resultados da pesquisa de acordo com critérios específicos.  A palavra-chave **RegEx** permite que você especifique uma expressão regular para este filtro.  

Este artigo fornece detalhes sobre a sintaxe de expressão regular usada pelo Log Analytics.

> [!NOTE]
> Você só pode usar RegEx com campos pesquisáveis.  Para obter mais informações sobre campos de pesquisa, veja **tipos de campo** na [localizar os dados usando pesquisas de log na análise de Log](log-analytics-log-searches.md#use-additional-filters).


## <a name="regex-keyword"></a>Palavra-chave RegEx

Utilize a seguinte sintaxe para usar a palavra-chave **RegEx** em uma pesquisa de logs.  É possível usar as outras seções deste artigo para determinar a sintaxe da expressão regular em si.

    field:Regex("Regular Expression")
    field=Regex("Regular Expression")

Por exemplo, para usar uma expressão regular para retornar registros de alerta com um tipo de *Aviso* ou *Erro*, você usaria a seguinte pesquisa de logs.

    Type=Alert AlertSeverity=RegEx("Warning|Error")

## <a name="partial-matches"></a>Correspondências parciais
Observe que a expressão regular deve corresponder a todo o texto da propriedade.  Correspondências parciais não retornarão nenhum registro.  Por exemplo, se você está tentando retornar registros de um computador chamado srv01.contoso.com, a seguinte pesquisa de logs **não** retornaria nenhum registro.

    Computer=RegEx("srv..")

Isso ocorre porque a primeira parte do nome corresponde à expressão regular.  As duas pesquisas de logs seguintes retornariam registros deste computador porque correspondem ao nome completo.

    Computer=RegEx("srv..@")
    Computer=RegEx("srv...contoso.com")

## <a name="characters"></a>Caracteres
Especificar caracteres diferentes.

| Character | Descrição | Exemplo | Correspondências de exemplo |
|:--|:--|:--|:--|
| a | Uma ocorrência do caractere. | Computer=RegEx("srv01.contoso.com") | srv01.contoso.com |
| . | Qualquer caractere único. | Computer=RegEx("srv...contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| a? | Zero ou uma ocorrência do caractere. | Computer=RegEx("srv01?.contoso.com") | srv0.contoso.com<br>srv01.contoso.com |
| a* | Zero ou mais ocorrências do caractere. | Computer=RegEx("srv01*.contoso.com") | srv0.contoso.com<br>srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| a+ | Uma ou mais ocorrências do caractere. | Computer=RegEx("srv01+.contoso.com") | srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| [*abc*] | Corresponde a qualquer caractere único entre os colchetes | Computer=RegEx("srv0[123].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [*a*-*z*] | Corresponde a um caractere único no intervalo.  Pode incluir vários intervalos. | Computer=RegEx("srv0[1-3].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [^*abc*] | Nenhum dos caracteres nos colchetes | Computer=RegEx("srv0[^123].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [^*a*-*z*] | Nenhum dos caracteres no intervalo. | Computer=RegEx("srv0[^1-3].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [*n*-*m*] | Corresponde a um intervalo de caracteres numéricos. | Computer=RegEx("srv[01-03].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| @ | Qualquer cadeia de caracteres. | Computer=RegEx("srv@.contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |


## <a name="multiple-occurences-of-character"></a>Várias ocorrências do caractere
Especifique várias ocorrências de um determinado caractere.

| Character | Descrição | Exemplo | Correspondências de exemplo |
|:--|:--|:--|:--|
| a{n} |  *n* ocorrências do caractere. | Computer=RegEx("bw-win-sc01{3}.bwren.lab") | bw-win-sc0111.bwren.lab |
| a{n,} |  *n* ou mais ocorrências do caractere. | Computer=RegEx("bw-win-sc01{3,}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab<br>bw-win-sc0111111.bwren.lab |
| a{n,m} |  *n* a *m* ocorrências do caractere. | Computer=RegEx("bw-win-sc01{3,5}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab |


## <a name="logical-expressions"></a>Expressões lógicas
Selecione de diversos valores.

| Character | Descrição | Exemplo | Correspondências de exemplo |
|:--|:--|:--|:--|
| &#124; | OR Lógico.  Retornará o resultado se corresponder a qualquer expressão. | Type=Alert AlertSeverity=RegEx("Warning&#124;Error") | Aviso<br>Erro |
| & | AND Lógico.  Retornará o resultado se corresponder as duas expressões | EventData=regex("(Security.\*&.\*success.\*)") | Auditoria de segurança bem-sucedida |


## <a name="literals"></a>Literais
Converter caracteres especiais em caracteres literais.  Isso inclui caracteres que fornecem funcionalidade para expressões regulares como ?-\*^\[\]{}\(\)+\|.&.

| Character | Descrição | Exemplo | Correspondências de exemplo |
|:--|:--|:--|:--|
| \\ | Converte um caractere especial em um literal. | Status_CF=\\[Error\\]@<br>Status_CF=Error\\-@ | [Erro] Arquivo não encontrado.<br>Erro-Arquivo não encontrado. |


## <a name="next-steps"></a>Próximas etapas

* Familiarizar-se com [pesquisas de logs](log-analytics-log-searches.md) para exibir e analisar dados no repositório do Log Analytics.
