---
title: Listas de exclusão de firewall do aplicativo Web na porta frontal do Azure-portal do Azure
description: Este artigo fornece informações sobre a configuração de listas de exclusão na frente do Azure com o portal do Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a92679bb3114c4a60870424f3ec68a8de7b303da
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499910"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>WAF (firewall do aplicativo Web) com listas de exclusão de serviço de porta frontal 

Às vezes, o Firewall do aplicativo Web (WAF) pode bloquear uma solicitação que você deseja permitir para seu aplicativo. Por exemplo, Active Directory insere tokens que são usados para autenticação. Esses tokens podem conter caracteres especiais que podem disparar um falso positivo das regras WAF. As listas de exclusões do WAF permitem a você omitir certos atributos de solicitação de uma avaliação do WAF.  Uma lista de exclusão pode ser configurada usando o  [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject), [CLI do Azure](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), [API REST](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)ou o portal do Azure. O exemplo a seguir mostra a configuração de portal do Azure. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Configurar listas de exclusão usando o portal do Azure
**Gerenciar exclusões** é acessível no portal do WAF em **regras gerenciadas**

![Gerenciar exclusion_add de gerenciamento de exclusão ](../media/waf-front-door-exclusion/exclusion1.png)
 ![](../media/waf-front-door-exclusion/exclusion2.png)

 Uma lista de exclusões de exemplo: ![ gerenciar exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

Este exemplo exclui o valor no campo de cabeçalho de *usuário* . Uma solicitação válida pode incluir o campo de *usuário* que contém uma cadeia de caracteres que dispara uma regra de injeção de SQL. Você pode excluir o parâmetro de *usuário* nesse caso para que a regra WAF não avalie nada no campo.

Os atributos a seguir podem ser adicionados às listas de exclusão por nome. Os valores dos campos que você usa não são avaliados em relação às regras WAF, mas seus nomes são avaliados. As listas de exclusão removem a inspeção do valor do campo.

* Nome do cabeçalho da solicitação
* Nome do cookie de solicitação
* Nome de argumentos da cadeia de caracteres de consulta
* Nome de argumentos da postagem do corpo da solicitação

Você pode especificar um cabeçalho de solicitação exato, corpo, cookie ou uma correspondência de atributo de cadeia de consulta.  Outra opção é especificar correspondências parciais. Os seguintes operadores são os critérios de correspondência com suporte:

- **Equals**: esse operador é usado para uma correspondência exata. Por exemplo, para selecionar um cabeçalho chamado **bearerToken**, use o operador Equals com o seletor definido como **bearerToken**.
- **Começa com**: esse operador corresponde com todos os campos que começam com o valor do seletor especificado.
- **Termina com**: esse operador corresponde com todos os campos de solicitação que terminam com o valor do seletor especificado.
- **Contém**: esse operador corresponde com todos os campos de solicitação que contenham o valor do seletor especificado.
- **Equals any**: Este operador corresponde a todos os campos de solicitação. * é o valor do seletor.

Os nomes de cabeçalho e cookie não diferenciam maiúsculas de minúsculas.

Se um valor de cabeçalho, um valor de cookie, um valor de argumento de postagem ou um valor de argumento de consulta produzir falsos positivos para algumas regras, você poderá excluir essa parte da solicitação de consideração pela regra:


|matchVariableName de logs do WAF  |Exclusão de regra no portal  |
|---------|---------|
|CookieValue: SOME_NAME        |Nome do cookie de solicitação igual a SOME_NAME|
|HeaderValue: SOME_NAME        |O nome do cabeçalho da solicitação é igual a SOME_NAME|
|Createparamvalue: SOME_NAME     |Nome dos argumentos da postagem do corpo da solicitação é igual a SOME_NAME|
|QueryParamValue: SOME_NAME    |Nome de argumentos da cadeia de caracteres de consulta igual a SOME_NAME|


Atualmente, há suporte apenas para exclusões de regra para os matchVariableNames acima em seus logs de WAF. Para qualquer outro matchVariableNames, você deve desabilitar as regras que fornecem falsos positivos ou criar uma regra personalizada que permita explicitamente essas solicitações. Em particular, quando matchVariableName é CookieName, HeaderName, createparamname ou QueryParamName, isso significa que o próprio nome está disparando a regra. A exclusão de regra não tem suporte para esses matchVariableNames no momento.


Se você excluir um argumento de postagem do corpo da solicitação chamado *foo*, nenhuma regra deverá mostrar o subparamvalue: foo como o matchVariableName nos logs do WAF. No entanto, você ainda pode ver uma regra com matchVariableName InitialBodyContents que corresponde ao valor do parâmetro post FOO, pois os valores de post param fazem parte do InitialBodyContents.

Você pode aplicar listas de exclusão a todas as regras no conjunto de regras gerenciadas, às regras de um grupo de regras específico ou a uma única regra, conforme mostrado no exemplo anterior.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Definir a exclusão com base nos logs de firewall do aplicativo Web
 O [monitoramento e o registro em log do firewall do aplicativo Web do Azure](waf-front-door-monitor.md) mostra os detalhes correspondentes de uma solicitação bloqueada. Se um valor de cabeçalho, um valor de cookie, um valor de argumento de postagem ou um valor de argumento de consulta produzir falsos positivos para algumas regras, você poderá excluir essa parte da solicitação de ser considerada pela regra. A tabela a seguir mostra os valores de exemplo dos logs do WAF e as condições de exclusão correspondentes.

|matchVariableName de logs do WAF    |Exclusão de regra no portal|
|--------|------|
|CookieValue: SOME_NAME  |Nome do cookie de solicitação igual a SOME_NAME|
|HeaderValue: SOME_NAME  |O nome do cabeçalho da solicitação é igual a SOME_NAME|
|Createparamvalue: SOME_NAME|  Nome dos argumentos da postagem do corpo da solicitação é igual a SOME_NAME|
|QueryParamValue: SOME_NAME| Nome de argumentos da cadeia de caracteres de consulta igual a SOME_NAME|


## <a name="next-steps"></a>Próximas etapas

Depois de definir as configurações do WAF, saiba como exibir os logs do WAF. Para obter mais informações, consulte [diagnóstico de porta frontal](../afds/waf-front-door-monitor.md).