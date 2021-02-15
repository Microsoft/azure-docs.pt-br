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
ms.openlocfilehash: 24822777b06fadf87ca446d9b7ff8ba4df34adc5
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007662"
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


## <a name="access-your-secure-score"></a>Acesse sua pontuação segura

Você pode encontrar a pontuação segura geral, bem como sua pontuação por assinatura, por meio da portal do Azure ou de forma programática, conforme descrito nas seções a seguir:

- [Obtenha sua pontuação segura no portal](#get-your-secure-score-from-the-portal)
- [Obtenha sua pontuação segura da API REST](#get-your-secure-score-from-the-rest-api)
- [Obtenha sua pontuação segura do grafo de recursos do Azure (ARG)](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>Obtenha sua pontuação segura no portal

A central de segurança exibe sua pontuação de forma proeminente no Portal: é o primeiro bloco principal da página Visão geral da central de segurança. Ao selecionar esse bloco, você será levado para a página de Pontuação segura dedicada, na qual verá a pontuação dividida por assinatura. Selecione uma única assinatura para ver a lista detalhada de recomendações priorizadas e o impacto potencial que a correção terá na pontuação da assinatura. 

Para recapitular, sua pontuação segura é mostrada nos locais a seguir nas páginas do portal da central de segurança.

- Em um bloco na **visão geral** da central de segurança (painel principal):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="A pontuação segura no painel da central de segurança":::

- Na página **Pontuação segura** dedicada, você pode ver a pontuação de segurança para sua assinatura e seus grupos de gerenciamento:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="A pontuação de segurança para assinaturas na página de Pontuação segura da central de segurança":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="A pontuação de segurança para grupos de gerenciamento na página de Pontuação segura da central de segurança":::

    > [!NOTE]
    > Todos os grupos de gerenciamento para os quais você não tem permissões suficientes mostrarão sua pontuação como "restrito". 

- Na parte superior da página de **recomendações** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="A pontuação de segurança na página de recomendações da central de segurança":::

### <a name="get-your-secure-score-from-the-rest-api"></a>Obtenha sua pontuação segura da API REST

Você pode acessar sua pontuação por meio da API de Pontuação segura. Os métodos de API oferecem a flexibilidade para consultar os dados e criar seu mecanismo de relatório das suas classificações de segurança ao longo do tempo. Por exemplo, você pode usar a [API de pontuações seguras](/rest/api/securitycenter/securescores) para obter a pontuação de uma assinatura específica. Além disso, você pode usar a [API de controles de Pontuação segura](/rest/api/securitycenter/securescorecontrols) para listar os controles de segurança e a pontuação atual de suas assinaturas.

![Recuperando uma única Pontuação segura por meio da API](media/secure-score-security-controls/single-secure-score-via-api.png)

Para obter exemplos de ferramentas criadas com base na API de Pontuação segura, consulte [a área de Pontuação segura da nossa comunidade do GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>Obtenha sua pontuação segura do grafo de recursos do Azure (ARG)

O grafo de recursos do Azure fornece acesso instantâneo a informações de recursos em seus ambientes de nuvem com recursos robustos de filtragem, agrupamento e classificação. É uma maneira rápida e eficiente de consultar informações em assinaturas do Azure programaticamente ou de dentro do portal do Azure. [Saiba mais sobre Azure Resource Graph](../governance/resource-graph/index.yml).

Para acessar a pontuação segura para várias assinaturas com ARG:

1. No portal do Azure, abra o **Gerenciador de grafo de recursos do Azure**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Iniciando a página de recomendação do Gerenciador de gráficos de recursos do Azure * *" :::

1. Insira sua consulta do Kusto (usando os exemplos abaixo para obter diretrizes).

    - Essa consulta retorna a ID da assinatura, a pontuação atual em pontos e como porcentagem e a pontuação máxima para a assinatura. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Essa consulta retorna o status de todos os controles de segurança. Para cada controle, você obterá o número de recursos não íntegros, a pontuação atual e a pontuação máxima. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Selecione **Executar consulta**.




## <a name="tracking-your-secure-score-over-time"></a>Acompanhando sua pontuação segura ao longo do tempo

Se você for um usuário Power BI com uma conta pro, poderá usar o painel de **Pontuação segura ao longo do tempo** Power bi para controlar sua pontuação segura ao longo do tempo e investigar as alterações.

> [!TIP]
> Você pode encontrar esse painel, bem como outras ferramentas para trabalhar de forma programática com Pontuação segura, na área dedicada da Comunidade da central de segurança do Azure no GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

O painel contém os dois relatórios a seguir para ajudá-lo a analisar seu status de segurança:

- **Resumo de recursos** -fornece dados resumidos sobre a integridade dos seus recursos.
- **Resumo da Pontuação de segurança** – fornece dados resumidos sobre o andamento da pontuação. Use o gráfico "Pontuação segura ao longo do tempo por assinatura" para exibir as alterações na pontuação. Se você perceber uma alteração drástica na sua pontuação, marque a tabela "alterações detectadas que podem afetar sua pontuação segura" para possíveis alterações que possam ter causado a alteração. Esta tabela apresenta recursos excluídos, recursos implantados recentemente ou recursos que seu status de segurança alterou para uma das recomendações.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="A pontuação segura opcional ao longo do tempo Power BI painel para acompanhar sua pontuação segura ao longo do tempo e investigar as alterações":::





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