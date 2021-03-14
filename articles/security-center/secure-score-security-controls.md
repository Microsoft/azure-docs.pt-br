---
title: Pontuação segura na Central de Segurança do Azure
description: Descrição da pontuação segura da Central de Segurança do Azure e seus controles de segurança
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: b19a7c156abf32e2a0f6d70717145a6ed5ab42ce
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099668"
---
# <a name="secure-score-in-azure-security-center"></a>Pontuação segura na Central de Segurança do Azure

## <a name="introduction-to-secure-score"></a>Introdução à pontuação segura

A central de segurança do Azure tem dois objetivos principais: 

- para ajudá-lo a entender sua situação de segurança atual
- para ajudá-lo a melhorar sua segurança de forma eficiente e eficaz

O recurso central na central de segurança que permite alcançar essas metas é a **Pontuação segura**.

A Central de Segurança avalia continuamente seus recursos, suas assinaturas e a organização em busca de problemas de segurança. Em seguida, ele agrega todas as conclusões em uma única pontuação para que você possa ver, rapidamente, sua situação de segurança atual: quanto maior a pontuação, menor o nível de risco identificado.

A pontuação segura é mostrada na portal do Azure páginas como um valor percentual, mas os valores subjacentes também são claramente apresentados:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Pontuação segura geral, conforme mostrado no portal":::

Para aumentar sua segurança, examine a página recomendações da central de segurança para as ações pendentes necessárias para aumentar sua pontuação. Cada recomendação inclui instruções para ajudá-lo a corrigir o problema específico.

As recomendações são agrupadas em **controles de segurança**. Cada controle é um grupo lógico de recomendações de segurança relacionadas e reflete as superfícies de ataque vulneráveis. Sua pontuação só melhora quando você corrige *todas* as recomendações para um único recurso dentro de um controle. Para ver como sua organização está protegendo cada superfície de ataque individual, examine as pontuações de cada controle de segurança.

Para obter mais informações, consulte [como sua pontuação segura é calculada](secure-score-security-controls.md#how-your-secure-score-is-calculated) abaixo. 

## <a name="how-your-secure-score-is-calculated"></a>Como sua pontuação segura é calculada 

A contribuição de cada controle de segurança para a pontuação segura geral é mostrada claramente na página de recomendações.

[![A pontuação segura avançada apresenta os controles de segurança](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Para obter todos os pontos possíveis para um controle de segurança, todos os seus recursos devem estar em conformidade com todas as recomendações de segurança no controle de segurança. Por exemplo, a Central de Segurança tem várias recomendações sobre como proteger suas portas de gerenciamento. Você precisará corrigi-los para fazer uma diferença na sua pontuação segura.

Por exemplo, o controle de segurança chamado "Aplicar atualizações do sistema" tem uma pontuação máxima de seis pontos, que você pode ver na dica de ferramenta sobre o valor de aumento potencial do controle:

[![O controle de segurança "Aplicar atualizações do sistema"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

A pontuação máxima para esse controle, Aplicar atualizações do sistema, é sempre 6. Neste exemplo, há 50 recursos. Portanto, dividimos a pontuação máxima por 50 e o resultado é que cada recurso contribui com 0,12 ponto. 

* **Potencial de aumento** (0,12 x 8 recursos não íntegros = 0,96) - os pontos restantes disponíveis para você no controle. Se você corrigir todas as recomendações neste controle, sua pontuação aumentará em 2% (nesse caso, 0,96 ponto arredondado para até um ponto). 
* **Pontuação atual** (0,12 x 42 recursos íntegros = 5,04) - a pontuação atual deste controle. Cada controle contribui para a pontuação total. Neste exemplo, o controle está contribuindo com 5,04 pontos para o total seguro atual.
* **Pontuação máxima** - o número máximo de pontos que você pode obter ao concluir todas as recomendações em um controle. A pontuação máxima de um controle indica o significado relativo desse controle. Use os valores máximos de pontuação para fazer a triagem dos problemas a serem resolvidos primeiro. 


### <a name="calculations---understanding-your-score"></a>Cálculos - noções básicas sobre sua pontuação

|Métrica|Fórmula e exemplo|
|-|-|
|**Pontuação atual do controle de segurança**|<br>![Equação para calcular a pontuação de um controle de segurança](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Cada controle de segurança individual contribui para a pontuação de segurança. Cada recurso afetado por uma recomendação dentro do controle contribui para a pontuação atual do controle. A pontuação atual de cada controle é uma medida do status dos recursos *no* controle.<br>![Dicas de ferramenta mostrando os valores usados ao calcular a pontuação atual do controle de segurança](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Neste exemplo, a pontuação máxima de 6 seria dividida por 78 porque essa é a soma dos recursos íntegros e não íntegros.<br>6 / 78 = 0,0769<br>Multiplicar isso pelo número de recursos íntegros (4) resulta na pontuação atual:<br>0,0769 * 4 = **0,31**<br><br>|
|**Pontuação segura**<br>Assinatura única|<br>![Equação para calcular a pontuação segura de uma assinatura](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Pontuação segura de assinatura única com todos os controles habilitados](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Neste exemplo, há uma assinatura única com todos os controles de segurança disponíveis (uma pontuação máxima potencial de 60 pontos). A pontuação mostra 28 pontos de um possível 60 e os 32 pontos restantes são refletidos nas figuras de "Aumento da pontuação potencial" dos controles de segurança.<br>![Lista de controles e o aumento de pontuação potencial](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Pontuação segura**<br>Várias assinaturas|<br>![Equação para calcular a pontuação segura para várias assinaturas](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Ao calcular a pontuação combinada para várias assinaturas, a central de segurança inclui um *peso* para cada assinatura. Os pesos relativos para suas assinaturas são determinados pela central de segurança com base em fatores como o número de recursos.<br>A pontuação atual de cada assinatura é calculada da mesma maneira que para uma única assinatura, mas, em seguida, o peso é aplicado conforme mostrado na equação.<br>Na visualização de várias assinaturas, a pontuação segura avalia todos os recursos em todas as políticas habilitadas e agrupa seu impacto combinado na pontuação máxima de cada controle de segurança.<br>![Pontuação segura para várias assinaturas com todos os controles habilitados](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>A pontuação combinada **não** é uma média; em vez disso, é a postura avaliada do status de todos os recursos em todas as assinaturas.<br>Também neste caso, se você acessar a página de recomendações e adicionar os pontos potenciais disponíveis, verá que é a diferença entre a pontuação atual (24) e a pontuação máxima disponível (60).|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Quais recomendações estão incluídas nos cálculos de Pontuação segura?

Somente as recomendações internas têm um impacto na pontuação segura.

As recomendações sinalizadas como **Visualização** não estão incluídas nos cálculos da sua pontuação segura. Elas ainda deverão ser corrigidas sempre que possível, para que, quando o período de versão prévia terminar, elas contribuam para a sua classificação.

Um exemplo de recomendação de versão prévia:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recomendação com o sinalizador de versão prévia":::

## <a name="improve-your-secure-score"></a>Melhorar sua pontuação segura

Para melhorar sua pontuação segura, corrija as recomendações de segurança da sua lista de recomendações. Você pode corrigir cada recomendação manualmente para cada recurso ou usando a opção **Correção Rápida!** (quando disponível) para aplicar rapidamente uma correção para uma recomendação a um grupo de recursos. Para obter mais informações, consulte [Recomendações de correção](security-center-remediate-recommendations.md).

Outra maneira de melhorar sua pontuação e garantir que os usuários não criem recursos que afetam negativamente sua pontuação é configurar as opções de aplicação e negação nas recomendações relevantes. Saiba mais em [Impedir configurações incorretas com as recomendações de Impor/Negar](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Controles de segurança e suas recomendações

A tabela a seguir lista os controles de segurança na Central de Segurança do Azure. Para cada controle, é possível ver o número máximo de pontos que você poderá adicionar à sua pontuação segura se corrigir *todas* as recomendações listadas no controle, para *todos* os seus recursos. 

O conjunto de recomendações de segurança fornecido com a central de segurança é adaptado aos recursos disponíveis no ambiente de cada organização. As recomendações podem ser personalizadas ainda mais com a [desabilitação de políticas](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) e [a isenção de recursos específicos de uma recomendação](exempt-resource.md). 
 
Recomendamos que cada organização revise cuidadosamente suas iniciativas de Azure Policy atribuídas. 

> [!TIP]
> Para obter detalhes sobre como revisar e editar suas iniciativas, consulte [trabalhando com políticas de segurança](tutorial-security-policy.md). 

Embora a iniciativa de segurança padrão da central de segurança seja baseada em padrões e práticas recomendadas do setor, há cenários em que as recomendações internas listadas abaixo podem não se ajustar completamente à sua organização. Consequentemente, às vezes, será necessário ajustar a iniciativa padrão-sem comprometer a segurança-para garantir que ela esteja alinhada com as políticas próprias da sua organização. padrões do setor, padrões regulatórios e benchmarks que você está obrigada a atender.<br><br>
<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>Perguntas frequentes sobre pontuação segura

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Se eu abordar apenas três de quatro recomendações em um controle de segurança, minha pontuação de proteção será alterada?
Não. Ela não será alterada até que você corrija todas as recomendações para um único recurso. Para obter a pontuação máxima de um controle, corrija todas as recomendações de todos os recursos.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Se uma recomendação não for aplicável a mim e eu desabilitá-la na política, meu controle de segurança será atendido e minha pontuação segura será atualizada?
Sim. É recomendável desabilitar as recomendações quando elas não são aplicáveis em seu ambiente. Para obter instruções sobre como desabilitar uma recomendação específica, consulte [Desabilitar políticas de segurança](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Caso um controle de segurança me ofereça zero pontos em relação à minha pontuação segura, devo ignorá-lo?
Em alguns casos, você verá uma pontuação máxima de controle maior que zero, mas o impacto é zero. Quando a pontuação incremental para corrigir recursos é insignificante, ela é arredondada para zero. Não ignore essas recomendações, pois elas ainda trazem melhorias de segurança. A única exceção é o controle de "melhor prática adicional". A correção dessas recomendações não aumentará sua pontuação, mas vai aprimorar a segurança geral.

## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu a pontuação segura e os controles de segurança que ela apresenta. Para obter material relacionado, consulte os seguintes artigos:

- [Saiba mais sobre os diferentes elementos de uma recomendação](security-center-recommendations.md)
- [Saiba como corrigir recomendações](security-center-remediate-recommendations.md)
- [Exibir as ferramentas baseadas no GitHub para trabalhar de forma programática com Pontuação segura](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)


> [!div class="nextstepaction"]
> [Acesse e acompanhe sua pontuação segura](secure-score-access-and-track.md)