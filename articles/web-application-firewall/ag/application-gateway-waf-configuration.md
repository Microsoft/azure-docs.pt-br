---
title: Limites de tamanho de solicitação do Web application firewall e listas de exclusão no Gateway de aplicativo do Azure - portal do Azure
description: Este artigo fornece informações sobre limites de tamanho de solicitação de firewall do aplicativo Web e a configuração de listas de exclusão no gateway de aplicativo com o portal do Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 2d34641fdecfe334e84347efe1a2f64482cae74b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93040251"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limites de tamanho de solicitação de firewall do aplicativo Web e listas de exclusão

O WAF (firewall do aplicativo Web) Aplicativo Azure Gateway fornece proteção para aplicativos Web. Este artigo descreve a configuração dos limites de tamanho de solicitação e listas de exclusões de WAF. Essas configurações estão localizadas na política WAF associada ao seu gateway de aplicativo. Para saber mais sobre as políticas de WAF, consulte [Firewall do aplicativo Web do Azure no gateway de aplicativo Azure](ag-overview.md) e [criar políticas de firewall do aplicativo Web para o gateway de aplicativo](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>Listas de exclusão de WAF

![Limites de tamanho de solicitação](../media/application-gateway-waf-configuration/waf-policy.png)

As listas de exclusões do WAF permitem a você omitir certos atributos de solicitação de uma avaliação do WAF. Um exemplo comum são os tokens inseridos do Active Directory que são usados para autenticação ou campos de senha. Esses atributos são propensos a conter os caracteres especiais que podem disparar um falso positivo das regras de WAF. Depois que um atributo é adicionado à lista de exclusões do WAF, ele não é levado em consideração por nenhuma regra do WAF configurada e ativa. As listas de exclusão são globais em escopo.

Os atributos a seguir podem ser adicionados às listas de exclusão por nome. Os valores do campo escolhido não são avaliados em relação às regras WAF, mas seus nomes ainda são (Veja o exemplo 1 abaixo, o valor do cabeçalho User-Agent é excluído da avaliação WAF). As listas de exclusão removem a inspeção do valor do campo.

* Cabeçalhos de solicitação
* Cookies de solicitação
* O nome do atributo de solicitação (args) pode ser adicionado como um elemento de exclusão, como:

   * Nome do campo de formulário
   * Entidade JSON
   * Argumentos de cadeia de caracteres de consulta de URL

Você pode especificar um cabeçalho de solicitação exato, corpo, cookie ou uma correspondência de atributo de cadeia de consulta.  Outra opção é especificar correspondências parciais. Regras de exclusão são de escopo global e se aplicam a todas as páginas e todas as regras.

A seguir estão os operadores de critérios de correspondência com suporte:

- **Equals**: esse operador é usado para uma correspondência exata. Como exemplo, para a seleção de um cabeçalho chamado **bearerToken**, use o operador equals com seletor definido como **bearerToken**.
- **Começa com**: esse operador corresponde com todos os campos que começam com o valor do seletor especificado.
- **Termina com**: esse operador corresponde com todos os campos de solicitação que terminam com o valor do seletor especificado.
- **Contém**: esse operador corresponde com todos os campos de solicitação que contenham o valor do seletor especificado.
- **Equals any**: Este operador corresponde a todos os campos de solicitação. * será o valor do seletor.

Em todos os casos, a correspondência diferencia maiúsculas de minúsculas e a expressão regular não é permitida como seletor.

> [!NOTE]
> Para obter mais informações e ajuda para solução de problemas, consulte [solução de problemas do WAF](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Exemplos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Os exemplos a seguir demonstram o uso de exclusões.

#### <a name="example-1"></a>Exemplo 1

Neste exemplo, você deseja excluir o cabeçalho do agente do usuário. O cabeçalho de solicitação do agente de usuário contém uma cadeia de caracteres característica que permite que os pares de protocolo de rede identifiquem o tipo de aplicativo, sistema operacional, fornecedor de software ou a versão de software do agente de usuário de software solicitante. Para obter mais informações, consulte [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Pode haver vários motivos para desabilitar a avaliação desse cabeçalho. Pode haver uma cadeia de caracteres que o WAF vê e supõe que seja mal-intencionado. Por exemplo, o ataque SQL clássico "x = x" em uma cadeia de caracteres. Em alguns casos, isso pode ser um tráfego legítimo. Portanto, talvez seja necessário excluir esse cabeçalho da avaliação do WAF.

O cmdlet a seguir Azure PowerShell exclui o cabeçalho do agente do usuário da avaliação:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Exemplo 2

Este exemplo exclui o valor no parâmetro *User* que é passado na solicitação por meio da URL. Por exemplo, digamos que seja comum em seu ambiente para o campo de usuário conter uma cadeia de caracteres que o WAF exibe como conteúdo mal-intencionado, para que ele o bloqueie.  Você pode excluir o parâmetro de usuário nesse caso para que o WAF não avalie nada no campo.

O cmdlet a seguir Azure PowerShell exclui o parâmetro do usuário da avaliação:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Portanto, se a URL `http://www.contoso.com/?user%281%29=fdafdasfda` for passada para o WAF, ela não avaliará a cadeia de caracteres **fdafdasfda**, mas ainda avaliará o usuário de nome de parâmetro **%281 %29**. 

## <a name="waf-request-size-limits"></a>Limites de tamanho de solicitação de WAF



O firewall do aplicativo Web permite a você configurar limites de tamanho de solicitação dentro de limites inferior e superior. As duas configurações de limites de tamanho a seguir estão disponíveis:

- O campo tamanho máximo do corpo da solicitação é especificado em quilobytes e controla o limite de tamanho de solicitação geral, excluindo todos os carregamentos de arquivo. Esse campo tem um valor mínimo de 1 KB e um valor máximo de 128 KB. O valor padrão para o tamanho do corpo da solicitação é 128 KB.
- O campo de limite de carregamento de arquivo é especificado em MB e controla o tamanho máximo de carregamento de arquivos permitido. Esse campo pode ter um valor mínimo de 1 MB e os máximos a seguir:

   - 100 MB para gateways de WAF médio v1
   - 500 MB para gateways de WAF grande v1
   - 750 MB para gateways WAF v2 

 O valor padrão para o limite de carregamento de arquivos é 100 MB.

O WAF também oferece um botão configurável para ativar ou desativar a inspeção de corpo da solicitação. Por padrão, a inspeção de corpo da solicitação está habilitada. Se a inspeção do corpo da solicitação for desativada, WAF não avaliará o conteúdo do corpo da mensagem HTTP. Nesses casos, o WAF continua a impor regras de WAF no URI, cookies e cabeçalhos. Se a inspeção do corpo da solicitação estiver desativada, o campo de tamanho máximo do corpo da solicitação não será aplicável e não poderá ser definido. Desativar a inspeção de corpo da solicitação permite que mensagens maiores que 128 KB sejam enviadas ao WAF, mas o corpo da mensagem não é inspecionado quanto a vulnerabilidades.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar as configurações de WAF, você pode aprender como exibir os logs de WAF. Para obter mais informações, consulte [Diagnósticos do Gateway de Aplicativo](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).
