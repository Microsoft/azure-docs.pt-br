---
title: 'Azure front door: conjunto de regras'
description: Este artigo fornece uma visão geral do recurso conjunto de regras padrão/Premium da porta do Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 8e6ceebc9e92dabe66baeb9aeff0ae9692e2bdad
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098419"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>O que é um conjunto de regras para a porta de recepção do Azure Standard/Premium (versão prévia)?

> [!Note]
> Esta documentação é para o Azure front door Standard/Premium (versão prévia). Procurando informações sobre a porta frontal do Azure? Veja [aqui](../front-door-overview.md).

Um conjunto de regras é um mecanismo de regra personalizado que agrupa uma combinação de regras em um único conjunto que você pode associar a várias rotas. O conjunto de regras permite que você personalize como as solicitações são processadas na borda e como a porta frontal do Azure lida com essas solicitações.

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="common-supported-scenarios"></a>Cenários comuns com suporte

* Implementação de cabeçalhos de segurança para evitar vulnerabilidades baseadas em navegador como HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-Frame-Options e Access-Control-Allow-Origin Headers para cenários de CORS (compartilhamento de recursos entre origens). Os atributos baseados em segurança também podem ser definidos com cookies.

* Encaminhe solicitações para versões móveis ou de desktop do seu aplicativo com base no tipo de dispositivo cliente.

* O uso de recursos de redirecionamento para retornar 301, 302, 307 e 308 redireciona para o cliente para direcioná-los para novos nomes de host, caminhos, cadeias de caracteres de consulta ou protocolos.

* Modificar dinamicamente a configuração de cache da rota com base nas solicitações de entrada.

* Reescreva o caminho da URL de solicitação e encaminhe a solicitação para a origem apropriada em seu grupo de origem configurado.

* Adicione, modifique ou remova o cabeçalho de solicitação/resposta para ocultar informações confidenciais ou capturar informações importantes por meio de cabeçalhos.

* Suporte a variáveis de servidor para alterar dinamicamente os cabeçalhos de solicitação/resposta ou os caminhos de regravação de URL/cadeias de caracteres de consulta, por exemplo, quando uma nova página é carregada ou quando um formulário é Postado. Atualmente, a variável de servidor tem suporte apenas nas **[ações de conjunto de regras](concept-rule-set-actions.md)** .

## <a name="architecture"></a>Arquitetura

O conjunto de regras lida com solicitações na borda. Quando uma solicitação chega ao ponto de extremidade padrão/Premium da porta do Azure, WAF é executado primeiro, seguido pelas configurações definidas em rota. Essas configurações incluem o conjunto de regras associado à rota. Os conjuntos de regras são processados de cima para baixo na rota. O mesmo se aplica às regras dentro de um conjunto de regras. Para que todas as ações sejam executadas em cada regra, todas as condições de correspondência dentro de uma regra têm de ser atendidas. Se uma solicitação não corresponder a nenhuma das condições na configuração do conjunto de regras, somente as configurações na rota serão executadas.

Se a opção **parar a avaliação de regras restantes** for marcada, todos os conjuntos de regras restantes associados à rota não serão executados.  

### <a name="example"></a>Exemplo

No diagrama a seguir, as políticas de WAF são executadas primeiro. Um conjunto de regras é configurado para acrescentar um cabeçalho de resposta. Em seguida, o cabeçalho altera a idade máxima do controle de cache se a condição de correspondência for atendida.

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="Diagrama que mostra a arquitetura do conjunto de regras." lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>Terminologia

Com o conjunto de regras de porta frontal do Azure, você pode criar uma combinação de configuração de conjunto de regras, cada uma composta de um conjunto de regras. As linhas a seguir destacam algumas terminologias úteis que você chegará ao configurar o conjunto de regras.

Para obter mais limite de cota, consulte [assinatura e limites de serviço, cotas e restrições do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

* *Conjunto de regras*: um conjunto de regras que é associado a uma ou várias [rotas](concept-route.md). Cada configuração é limitada a 25 regras. Você pode criar até 10 configurações.

* *Regra de conjunto de regras*: uma regra composta de até 10 condições de correspondência e 5 ações. As regras são locais para um conjunto de regras e não podem ser exportadas para uso em conjuntos de regras. Os usuários podem criar a mesma regra em vários conjuntos de regras.

* *Condição de correspondência*: há várias condições de correspondência que podem ser utilizadas para analisar suas solicitações de entrada. Uma regra pode conter até 10 condições de correspondência. As condições de correspondência são avaliadas com um operador **AND**. *Há suporte para a expressão regular em condições*. Uma lista completa de condições de correspondência pode ser encontrada em [condição de conjunto de regras](concept-rule-set-match-conditions.md).

* *Ação*: as ações ditam como o AFD lida com as solicitações de entrada com base nas condições de correspondência. Você pode modificar comportamentos de cache, modificar cabeçalhos de solicitação/cabeçalhos de resposta, fazer regravação de URL e redirecionamento de URL. As *variáveis de servidor têm suporte em ação*. Uma regra pode conter até 10 condições de correspondência. Uma lista completa de ações pode ser encontrada [como ações de conjunto de regras](concept-rule-set-actions.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar uma porta de front-Standard/Premium](create-front-door-portal.md).
* Saiba como configurar seu primeiro [conjunto de regras](how-to-configure-rule-set.md).
 
