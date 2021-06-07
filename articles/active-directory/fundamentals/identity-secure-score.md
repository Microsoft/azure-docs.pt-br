---
title: O que é Pontuação segura de identidade? -Azure Active Directory
description: Como você pode usar a pontuação de segurança de identidade para melhorar a postura de segurança do seu diretório
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: guptashi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23832d9f1205105f1f9711cdf3260b74ee4a9bb1
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952242"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>O que é a classificação de segurança de identidade no Azure Active Directory?

Quão seguro é seu locatário do Azure AD? Se você não souber como responder a essa pergunta, este artigo explicará como a pontuação segura de identidade ajuda você a monitorar e melhorar sua postura de segurança de identidade.

## <a name="what-is-an-identity-secure-score"></a>O que é uma classificação de segurança de identidade?

A pontuação de segurança de identidade é o percentual que funciona como um indicador de quão alinhado você é com as práticas recomendadas da Microsoft para segurança. Cada ação de aprimoramento na pontuação de segurança de identidade é adaptada para sua configuração específica.  

![Classificação de segurança](./media/identity-secure-score/identity-secure-score-overview.png)

A classificação ajuda você a:

- Medir objetivamente a sua postura de segurança de identidade
- Planejar aprimoramentos de segurança de identidade
- Examinar o sucesso das suas melhorias

Você pode acessar a classificação e informações relacionadas a no painel de classificação de segurança de identidade. Neste painel, você encontrará:

- Sua pontuação de segurança de identidade
- Um grafo de comparação que mostra como sua pontuação segura de identidade se compara a outros locatários no mesmo setor e tamanho semelhante
- Um grafo de tendência mostrando como sua pontuação segura de identidade mudou ao longo do tempo
- Uma lista de possíveis melhorias

Ao seguir as ações de melhoria, você pode:

- Melhore sua postura de segurança e sua pontuação
- Aproveite os recursos disponíveis para sua organização como parte de seus investimentos em identidade

## <a name="how-do-i-get-my-secure-score"></a>Como faço para obter minha classificação de segurança?

A pontuação de segurança de identidade está disponível em todas as edições do Azure AD. As organizações podem acessar sua pontuação segura de identidade do **portal do Azure**  >  **Azure Active Directory**  >    >  **Pontuação segura de identidade** de segurança.

## <a name="how-does-it-work"></a>Como ele funciona?

A cada 48 horas, o Azure analisa sua configuração de segurança e compara suas configurações com as práticas recomendadas. Com base no resultado dessa avaliação, uma nova pontuação é calculada para seu diretório. É possível que sua configuração de segurança não esteja totalmente alinhada com as diretrizes de práticas recomendadas e as ações de melhoria sejam atendidas apenas parcialmente. Nesses cenários, você receberá apenas uma parte da pontuação máxima disponível para o controle.

Cada recomendação é medida com base na sua configuração do Azure AD. Se você estiver usando produtos de terceiros para habilitar uma recomendação de prática recomendada, poderá indicar essa configuração nas configurações de uma ação de aperfeiçoamento. Você também tem a opção de definir recomendações a serem ignoradas se elas não se aplicarem ao seu ambiente. Uma recomendação ignorada não contribui para o cálculo de sua classificação.

![Ignorar ou marcar a ação como coberta por terceiros](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>Como isso me ajuda?

A classificação de segurança ajuda você a:

- Medir objetivamente a sua postura de segurança de identidade
- Planejar aprimoramentos de segurança de identidade
- Examinar o sucesso das suas melhorias

## <a name="what-you-should-know"></a>O que você deve saber

### <a name="who-can-use-the-identity-secure-score"></a>Quem pode usar a classificação de segurança de identidade?

A classificação de segurança de identidade pode ser usada pelas funções a seguir:

- Administrador global
- Administrador de segurança
- Leitores de segurança

### <a name="how-are-controls-scored"></a>Como os controles são pontuados?

Os controles podem ser classificados de duas maneiras. Algumas são pontuadas de maneira binária – você obtém 100% da Pontuação se tiver o recurso ou a configuração configurada com base em nossa recomendação. Outras pontuações são calculadas como uma porcentagem da configuração total. Por exemplo, se a recomendação de aperfeiçoamento indicar que você obterá um máximo de 10,71% se você proteger todos os usuários com MFA e tiver apenas 5 de 100 total de usuários protegidos, você receberá uma pontuação parcial cerca de 0,53% (5 protegidos/100 total * 10,71% Maximum = 0,53% Pontuação parcial).

### <a name="what-does-not-scored-mean"></a>O que [Não Pontuado] significa?

Ações rotuladas como [Não Pontuada] são aquelas você pode executar em sua organização, mas não serão pontuadas porque não estão conectadas à ferramenta (ainda!). Portanto, você ainda pode melhorar sua segurança, mas não receberá crédito por essas ações no momento.

### <a name="how-often-is-my-score-updated"></a>Com que frequência minha classificação é atualizada?

A classificação é calculada uma vez por dia (aproximadamente à 1h PST). Se você fizer uma alteração a uma ação de medida, a classificação será atualizada automaticamente no dia seguinte. Podem ser necessárias até 48 horas para uma alteração ser refletida na sua classificação.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Minha classificação mudou. Como faço para descobrir por quê?

Acesse a [central de segurança do Microsoft 365](https://security.microsoft.com/), onde você encontrará sua pontuação de segurança completa da Microsoft. Você pode ver facilmente todas as alterações na sua pontuação segura revisando as alterações detalhadas na guia histórico.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>A pontuação segura mede o risco de serem violadas?

Em breve, não. A pontuação segura não expressa uma medida absoluta de quão provável que você tenha sido violada. Expressa a extensão em que você adotou recursos que podem compensar o risco de sofrer uma violação. Nenhum serviço pode garantir que você não será violado e a pontuação segura não deve ser interpretada como uma garantia de qualquer forma.

### <a name="how-should-i-interpret-my-score"></a>Como devo interpretar a minha classificação?

Sua pontuação melhora a configuração de recursos de segurança recomendados ou a execução de tarefas relacionadas à segurança (como a leitura de relatórios). Algumas ações são pontuadas para conclusão parcial, como habilitar MFA (autenticação multifator) para seus usuários. Sua pontuação segura é um representante direto dos serviços de segurança da Microsoft que você usa. Lembre-se de que a segurança deve ser equilibrada com usabilidade. Todos os controles de segurança têm um componente de impacto ao usuário. Controles com baixo impacto ao usuário devem ter pouco ou nenhum efeito sobre as operações diárias dos usuários.

Para ver seu histórico de pontuação, acesse a [central de segurança do Microsoft 365](https://security.microsoft.com/) e examine sua pontuação de segurança geral da Microsoft. Você pode examinar as alterações em sua pontuação de segurança geral clicando em Exibir histórico. Escolha uma data específica para ver quais controles foram habilitados para aquele dia e que pontos você ganhou para cada um.

### <a name="how-does-the-identity-secure-score-relate-to-the-microsoft-365-secure-score"></a>Como a pontuação segura de identidade está relacionada à pontuação segura de Microsoft 365?

A [Pontuação de segurança da Microsoft](/office365/securitycompliance/microsoft-secure-score) contém cinco categorias de controle distinto e Pontuação:

- Identidade
- Dados
- Dispositivos
- Infraestrutura
- Aplicativos

A pontuação de segurança de identidade representa a parte de identidade da Pontuação segura da Microsoft. Essa sobreposição significa que suas recomendações para a pontuação de segurança de identidade e a pontuação de identidade na Microsoft são as mesmas.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre a pontuação de segurança da Microsoft](/office365/securitycompliance/microsoft-secure-score)