---
title: Perguntas frequentes sobre a proteção de identidade no Azure Active Directory
description: Perguntas frequentes Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6117b1ac78faf84d73f5a78202709aec7a1f84d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492579"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Perguntas frequentes sobre a proteção de identidade em Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Ignorar problemas conhecidos de risco do usuário

**Ignorar o risco do usuário** na proteção de identidade clássica define o ator no histórico de riscos do usuário em identidade Protection para o **Azure ad**.

**Ignorar o risco do usuário** no Identity Protection define o ator no histórico de riscos do usuário em Identity Protection para **\<Admin’s name with a hyperlink pointing to user’s blade\>** .

Há um problema atual conhecido causando latência no fluxo de perda de risco do usuário. Se você tiver uma "política de risco do usuário", ela deixará de ser aplicada aos usuários descartados alguns minutos após clicar em "Ignorar o risco de usuário". No entanto, há atrasos conhecidos na experiência do usuário que atualizam o "Estado de risco" de usuários descartados. Como alternativa, atualize a página no nível do navegador para ver o "Estado de risco" mais recente do usuário.


## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="why-is-a-user-at-risk"></a>Por que um usuário está em risco?

Se você for um cliente Azure AD Identity Protection, vá para a exibição [usuários arriscados](howto-identity-protection-investigate-risk.md#risky-users) e clique em um usuário de risco. Na gaveta na parte inferior, a guia "histórico de risco" mostrará todos os eventos que levaram a uma alteração de risco do usuário. Para ver todas as entradas arriscadas do usuário, clique em ' entradas arriscadas ' do usuário. Para ver todas as detecções de risco para esse usuário, clique em "detecções de risco do usuário".

### <a name="why-was-my-sign-in-blocked-but-identity-protection-didnt-generate-a-risk-detection"></a>Por que minha entrada foi bloqueada, mas a proteção de identidade não gerou uma detecção de risco?
Entradas podem ser bloqueadas por vários motivos. É importante observar que a proteção de identidade gera apenas detecções de risco quando as credenciais corretas são usadas na solicitação de autenticação. Se um usuário usar credenciais incorretas, ele não será sinalizado pela proteção de identidade, pois não há risco de comprometimento de credenciais, a menos que um ator incorreto use as credenciais corretas. Alguns motivos pelos quais um usuário pode ser impedido de assinar que não geram uma detecção de proteção de identidade incluem:
* O **IP pode ser bloqueado** devido a atividades mal-intencionadas do endereço IP. A mensagem bloqueada por IP não diferencia se as credenciais estavam corretas ou não. Se o IP estiver bloqueado e as credenciais corretas não forem usadas, ele não gerará uma detecção de proteção de identidade
* O **[bloqueio inteligente](../authentication/howto-password-smart-lockout.md)** pode bloquear a conta de entrar após várias tentativas com falha
* Uma **política de acesso condicional** pode ser imposta que usa condições diferentes de nível de risco para bloquear uma solicitação de autenticação

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Como posso obter um relatório de detecções de um tipo específico?

Vá para a exibição de detecções de risco e filtre por ' tipo de detecção '. Em seguida, você pode baixar esse relatório no. CSV ou. Formato JSON usando o botão **baixar** na parte superior. Para obter mais informações, consulte o artigo [How to: investigue Risk](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Por que não posso definir meus próprios níveis de risco para cada detecção de riscos?

Os níveis de risco no Identity Protection baseiam-se na precisão da detecção e são alimentados por nosso aprendizado de máquina supervisionado. Para personalizar a quais experiências os usuários são apresentados, o administrador pode incluir/excluir determinados usuários/grupos das políticas de Risco de Usuário e Risco de Entrada.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Por que o local de uma entrada não corresponde àquele de onde o usuário realmente entrou?

O mapeamento de localização geográfica do IP é um desafio de toda a indústria. Se você sentir que o local listado no relatório de entradas não corresponde ao local real, entre em contato com o suporte da Microsoft. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Como eu posso fechar as detecções de risco específicas como fiz na interface do usuário antiga?

Você pode fornecer comentários sobre as detecções de risco confirmando a entrada vinculada como comprometida ou segura. Os comentários fornecidos nas vazas de entrada para todas as detecções feitas nessa entrada são feitos. Se desejar fechar as detecções que não estão vinculadas a uma entrada, você poderá fornecer esses comentários no nível do usuário. Para obter mais informações, consulte o artigo [como: fornecer comentários de risco em Azure ad Identity Protection](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Até que ponto posso voltar no tempo para entender o que está acontecendo com meu usuário?

- A exibição de [usuários arriscados](howto-identity-protection-investigate-risk.md#risky-users) mostra o risco de um usuário ficar com base em todas as entradas passadas. 
- A exibição de [entradas arriscadas](howto-identity-protection-investigate-risk.md#risky-sign-ins) mostra os sinais de risco nos últimos 30 dias. 
- A exibição de [detecções de risco](howto-identity-protection-investigate-risk.md#risk-detections) mostra as detecções de risco feitas nos últimos 90 dias.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Como posso saber mais sobre uma detecção específica?

Todas as detecções de riscos são documentadas no artigo [o que é risco](concept-identity-protection-risks.md#risk-types-and-detection). Você pode passar o mouse sobre o símbolo (i) ao lado da detecção no portal do Azure para saber mais sobre uma detecção.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Como funcionam os mecanismos de comentários no Identity Protection?

**Confirmar como comprometido** (em uma entrada) – informa o Azure AD Identity Protection que a entrada não foi executada pelo proprietário da identidade e indica um comprometimento.

- Depois de receber seus comentários, movemos o estado de risco de entrada e de usuário para **Confirmado como comprometido** e o nível de risco para **Alto**.

- Além disso, fornecemos as informações para nossos sistemas de aprendizado de máquina para futuras melhorias na avaliação de risco.

    > [!NOTE]
    > Se o usuário já foi corrigido, não clique em **Confirmar como comprometido** porque ele muda o estado de risco de entrada e de usuário para **Confirmado como comprometido** e o nível de risco para **Alto**.

**Confirmar como seguro** (em uma entrada) – informa o Azure AD Identity Protection que a entrada foi executada pelo proprietário da identidade e não indica um comprometimento.

- Depois de receber esses comentários, movemos o estado de risco de entrada (não o de usuário) para **Confirmado como seguro** e o nível de risco para **-**.

- Além disso, fornecemos as informações para nossos sistemas de aprendizado de máquina para futuras melhorias na avaliação de risco. 

    > [!NOTE]
    >Hoje, a seleção de confirmar segurança em uma entrada não impede que logons futuros com as mesmas propriedades sejam sinalizadas como arriscadas. A melhor maneira de treinar o sistema para aprender as propriedades de um usuário é usar a política de entrada arriscada com a MFA. Quando uma entrada arriscada é solicitada pela MFA e o usuário responde com êxito à solicitação, a entrada pode ter êxito e ajudar a treinar o sistema no comportamento do usuário legítimo.
    >
    > Se você achar que o usuário não foi comprometido, use **Ignorar o risco de usuário** no nível do usuário em vez de usar **Confirmado como seguro** no nível de entrada. Um **risco de ignorar usuário** no nível do usuário fecha o risco do usuário e todos os logons e as detecções de risco anteriores.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Por que estou vendo um usuário com uma pontuação de risco baixa (ou acima), mesmo que nenhuma entrada arriscada ou detecção de risco seja mostrada na proteção de identidade?

Considerando que o risco do usuário é cumulativo por natureza e não expira, um usuário pode ter um risco de usuário baixo ou superior, mesmo que não haja entradas arriscadas recentes ou detecções de risco mostradas na proteção de identidade. Essa situação pode acontecer se a única atividade mal-intencionada em um usuário fosse feita além do período de tempo para o qual armazenamos os detalhes de entradas arriscadas e detecções de risco. Nós não expiramos o risco do usuário porque os atores ruins comprovadamente permanecem no ambiente do cliente por mais de 140 dias por trás de uma identidade comprometida antes aumentar seu ataque. Os clientes podem examinar a linha do tempo de risco do usuário para entender por que um usuário está em risco ao acessar: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Por que uma entrada tem uma pontuação de "risco de entrada (agregação)" alta quando as detecções associadas a ela são de risco baixo ou médio?

A pontuação de risco de agregação alta pode ser baseada em outros recursos de entrada ou o fato de que mais de uma detecção foi acionada para essa entrada. E, por outro lado, uma entrada pode ter um risco de entrada (agregação) médio, mesmo se as detecções associadas com a entrada são de alto risco.

### <a name="what-is-the-difference-between-the-activity-from-anonymous-ip-address-and-anonymous-ip-address-detections"></a>Qual é a diferença entre as detecções "atividade de endereço IP anônimo" e "endereço IP anônimo"?

A origem da detecção de "endereço IP anônimo" é Azure AD Identity Protection, enquanto a detecção de "atividade de endereço IP anônimo" é integrada de MCAS (Microsoft Cloud App Security). Embora eles tenham nomes muito semelhantes e é possível que você veja sobreposição nesses sinais, eles têm detecções de back-end distintas.
