---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Saiba como o Azure AD Identity Protection permite limitar a capacidade de um invasor de explorar uma identidade ou um dispositivo comprometidos ou um dispositivo que sofreu comprometimento conhecido ou suspeito anteriormente.
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, gerenciamento de aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 0c7a8d68c0df729441e3f7faa5cd06066db1261d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

O Azure Active Directory Identity Protection é um recurso da edição Premium P2 do Azure AD que permite a você:

- Detectar possíveis vulnerabilidades que afetam as identidades da organização

- Configurar respostas automatizadas para ações suspeitas detectadas que se relacionem com as identidades da sua organização  

- Investigar incidentes suspeitos e tomar as devidas providências para resolvê-los   


## <a name="getting-started"></a>Introdução

A Microsoft protege identidades baseadas em nuvem há mais de uma década. Com o Azure Active Directory Identity Protection, você pode usar os mesmos sistemas de proteção em seu ambiente que a Microsoft usa para proteger as identidades.

A grande maioria das violações de segurança ocorre quando os invasores conseguem acessar a um ambiente roubando a identidade de um usuário. Nos últimos anos, os invasores têm se tornado cada vez mais eficazes em aproveitar as violações de terceiros e usar ataques de phishing sofisticados. Assim que um invasor obtém acesso até mesmo a uma conta de usuário com privilégios baixos, é relativamente fácil para ele conseguir acessar recursos importantes da empresa por meio de movimentação lateral.

Como consequência, você precisa:

- Proteger todas as identidades, independentemente do nível de privilégio

- Agir proativamente para evitar o uso de identidades comprometidas

Descobrir identidades comprometidas não é uma tarefa fácil. O Azure Active Directory usa algoritmos de aprendizado de máquina e heurística adaptáveis para detectar anomalias e incidentes suspeitos que indicam identidades potencialmente comprometidas. Usando esses dados, o Identity Protection gera relatórios e alertas que permitem avaliar os problemas detectados e tomar as devidas ações de mitigação ou correção.

O Azure Active Directory Identity Protection é mais do que apenas uma ferramenta de monitoramento e criação de relatórios. Para proteger as identidades da sua organização, você pode configurar políticas de risco que respondem automaticamente a problemas detectados quando um nível de risco especificado foi alcançado. Essas políticas, entre outros controles de acesso condicional fornecidos pelo Azure Active Directory e pelo EMS, podem bloquear ou iniciar automaticamente ações de correção adaptáveis que incluem redefinições de senha e a imposição de autenticação multifator.


#### <a name="identity-protection-capabilities"></a>Recursos do Identity Protection

**Detecção de vulnerabilidades e contas de risco:**  

* Fornecer recomendações personalizadas para melhorar a postura de segurança geral ao realçar as vulnerabilidades
* Calcular os níveis de risco de entrada
* Calcular os níveis de risco do usuário


**Investigação dos eventos de risco:**

* Enviar notificações para eventos de risco
* Investigar os eventos de risco usando informações relevantes e contextuais
* Fornecer fluxos de trabalho básicos para acompanhar as investigações
* Fornecer acesso fácil às ações de correção, tais como redefinição de senha

**Políticas de acesso condicional baseadas em risco:**

* Política para reduzir entradas arriscadas ao bloquear entradas ou exigir desafios de autenticação multifator.
* Política para bloquear ou proteger contas de usuário arriscadas
* Política para exigir o registro para autenticação multifator



## <a name="identity-protection-roles"></a>Funções da proteção de identidade

Para equilibrar as atividades de gerenciamento em torno de sua implementação da proteção de identidade, você pode atribuir várias funções. O Azure AD Identity Protection dá suporte a três funções do diretório:

| Função                         | O que ele pode fazer                          | O que não pode fazer
| :--                          | ---                                |  ---   |
| Administrador global         | Acesso completo à Proteção de Identidade, Proteção de Identidade integrada| |
| Administrador de segurança       | Acesso total à proteção de identidade | Proteção de Identidade integrada, redefinir senhas para um usuário |
| Leitor de segurança              | Acesso somente de leitura para a Proteção de Identidade | Integrar Proteção de Identidade, corrigir usuários, configurar políticas, redefinir senhas |




Para saber mais detalhes, consulte [Atribuindo funções de administrador no Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)



## <a name="detection"></a>Detecção

### <a name="vulnerabilities"></a>Vulnerabilidades

O Azure Active Directory Identity Protection analisa sua configuração e detecta vulnerabilidades que podem ter impacto nas identidades do usuário. Para saber mais, confira [Vulnerabilidades detectadas pelo Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md).

### <a name="risk-events"></a>Eventos de risco

O Azure Active Directory usa algoritmos de aprendizado de máquina e heurística adaptáveis para detectar ações suspeitas relacionadas a identidades do usuário. O sistema cria um registro para cada ação suspeita detectada. Esses registros também são conhecidos como eventos de risco.  
Para obter mais detalhes, veja [Eventos de risco do Azure Active Directory](active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Investigação
Sua jornada pelo Identity Protection normalmente inicia no Painel do Identity Protection.

![Correção](./media/active-directory-identityprotection/1000.png "Correção")

O painel concede acesso a:

* Relatórios, como **Usuários sinalizados por riscos**, **Eventos de risco** e **Vulnerabilidades**
* Configurações como a definição das suas **Políticas de Segurança**, **Notificações** e **registro de autenticação multifator**

Este é normalmente seu ponto de partida para investigação, que é o processo de revisão de atividades, logs e outras informações relevantes relacionadas a um evento de risco para decidir se as etapas de correção e mitigação são necessárias, como a identidade foi comprometida e entender como ela foi usada.

Você pode vincular suas atividades de investigação para as [notificações](active-directory-identityprotection-notifications.md) que o Azure Active Directory Protection envia por email.

As seções a seguir fornecerão mais detalhes e as etapas que estão relacionadas a uma investigação.  


## <a name="risky-sign-ins"></a>Entradas de risco

O Azure Active Directory detecta [tipos de evento de risco](active-directory-reporting-risk-events.md#risk-event-types) em tempo real e offline. Cada evento de risco que tiver sido detectado para a entrada de um usuário contribui para um conceito lógico chamado entrada de risco. Uma entrada de risco é um indicador de uma tentativa de logon que pode não ter sido realizada pelo proprietário legítimo de uma conta de usuário.


### <a name="sign-in-risk-level"></a>Nível de risco de entrada

Um nível de risco de entrada é uma indicação (alta, média ou baixa) da probabilidade de uma tentativa de logon não ter sido executada pelo proprietário legítimo de uma conta de usuário.

### <a name="mitigating-sign-in-risk-events"></a>Mitigação de eventos de risco de entrada

Uma mitigação é uma ação que visa limitar a capacidade de um invasor explorar uma identidade ou um dispositivo comprometidos sem restaurá-los para um estado seguro. Uma mitigação não resolve eventos de risco de entrada anteriores associados à identidade ou ao dispositivo.

Para atenuar as entradas de risco automaticamente, você pode configurar políticas de segurança de entradas de risco. Ao usar essas políticas, considere o nível de risco do usuário ou da entrada para bloquear entradas arriscadas ou exigir que o usuário realize a autenticação multifator. Essas ações podem impedir que um invasor explore uma identidade roubada para causar danos, fazendo você ganhar algum tempo para proteger a identidade.

### <a name="sign-in-risk-security-policy"></a>Política de segurança de risco de entrada
Uma política de segurança de risco de entrada é uma política de acesso condicional que avalia o risco de uma entrada específica e aplica mitigações com base em regras e condições predefinidas.

![Política de risco de entrada](./media/active-directory-identityprotection/1014.png "Política de risco de entrada")

O Azure AD Identity Protection ajuda a gerenciar a mitigação de entradas arriscadas, permitindo:

* Defina os usuários e os grupos aos quais a política se aplica:

    ![Política de risco de entrada](./media/active-directory-identityprotection/1015.png "Política de risco de entrada")
* Defina o limite de nível de risco da entrada (baixo, médio ou alto) que dispara o bloqueio de um usuário:

    ![Política de risco de entrada](./media/active-directory-identityprotection/1016.png "Política de risco de entrada")
* Defina os controles a serem impostos quando a política for disparada:  

    ![Política de risco de entrada](./media/active-directory-identityprotection/1017.png "Política de risco de entrada")
* Alterne o estado de sua política:

    ![Registro de MFA](./media/active-directory-identityprotection/403.png "Registro de MFA")
* Examine e avalie o impacto de uma alteração antes de ativá-la:

    ![Política de risco de entrada](./media/active-directory-identityprotection/1018.png "Política de risco de entrada")

#### <a name="what-you-need-to-know"></a>O que você precisa saber
Você pode configurar uma política de segurança de risco de entrada para exigir autenticação multifator:

![Política de risco de entrada](./media/active-directory-identityprotection/1017.png "Política de risco de entrada")

No entanto, por motivos de segurança, essa configuração só funciona para usuários que já foram registrados na autenticação multifator. Se a condição de exigir autenticação multifator for atendida por um usuário que ainda não está registrado na autenticação multifator, ele será bloqueado.

Como prática recomendada, se você quiser exigir a autenticação multifator para entradas de risco:

1. Habilite a [política de registro de autenticação multifator](#multi-factor-authentication-registration-policy) para os usuários afetados.
2. Exija que os usuários afetados façam logon em uma sessão sem risco para realizar um registro MFA

A conclusão dessas etapas faz com que a autenticação multifator seja exigida em caso de entrada de risco.

#### <a name="best-practices"></a>Práticas recomendadas
Escolher um limite **Alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários.  

No entanto, isso exclui entradas sinalizadas com **Baixo** e **Médio** risco da política, o que pode não impedir que um invasor explore uma identidade comprometida.

Ao definir a política,

* exclua os usuários que não tem / não podem ter a autenticação multifator
* exclua os usuários em localidades em que não é viável habilitar a política (por exemplo, sem acesso à assistência técnica)
* exclua os usuários que tendem a gerar muitos falsos positivos (desenvolvedores e analistas de segurança)
* use um limite **Alto** durante a distribuição inicial de política ou se você precisar minimizar os desafios encontrados pelos usuários finais.
* Use um limite **Baixo** se sua organização exigir uma segurança maior. Selecionar um limite **Baixo** apresenta desafios de entrada do usuário adicionais, porém representa uma segurança maior.

O padrão recomendado na maioria das organizações é configurar uma regra para um limite **Médio** para atingir um equilíbrio entre segurança e usabilidade.

A política de risco de entrada:

* é aplicada a todo o tráfego do navegador e entradas que usam autenticação moderna.
* Não é aplicada a aplicativos que usam protocolos de segurança mais antigos desabilitando o ponto de extremidade WS-Trust no IDP federado, como o ADFS.

A página **Eventos de Risco** no console do Identity Protection lista todos os eventos:

* aos quais essa política se aplica
* para os quais você pode examinar a atividade e determinar se a ação foi apropriada ou não

Para obter uma visão geral da experiência do usuário relacionada, confira:

* [Recuperação de entrada arriscada](active-directory-identityprotection-flows.md#risky-sign-in-recovery)
* [Entrada arriscada bloqueada](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  
* [Experiências de entrada com o Azure AD Identity Protection](active-directory-identityprotection-flows.md)  

**Para abrir o diálogo de configurações relacionadas**:

- Na folha **Azure AD Identity Protection**, na seção **Configurar**, clique em **Política de entrada de risco**.

    ![Política do usuário ridk](./media/active-directory-identityprotection/1014.png "Política do usuário ridk")



## <a name="users-flagged-for-risk"></a>Usuários sinalizados por risco

Todos os [eventos de risco](active-directory-identity-protection-risk-events.md) ativos que foram detectados pelo Azure Active Directory para um usuário contribuem para um conceito lógico chamado risco de usuário. Um usuários sinalizado como de risco é um indicador de que uma conta de usuário pode ter sido comprometida.

![Usuários sinalizados por risco](./media/active-directory-identityprotection/1200.png)


### <a name="user-risk-level"></a>Nível de risco do usuário

Um nível de risco do usuário é uma indicação (Alta, Média ou Baixa) da probabilidade de que a identidade do usuário foi comprometida. Ele é calculado com base nos eventos de risco do usuário associados a uma identidade do usuário.

O status de um evento de risco é **Ativo** ou **Fechado**. Somente eventos de risco **Ativos** contribuem para o cálculo de nível de risco do usuário.

O nível de risco do usuário é calculado usando as seguintes entradas:

* Eventos de risco ativos que afetam o usuário
* Nível de risco desses eventos
* Se foram tomadas ações de correção

![Riscos de usuário](./media/active-directory-identityprotection/1031.png "Riscos de usuário")

Você pode usar os níveis de risco do usuário para criar políticas de acesso condicional que bloqueiam a entrada de usuários arriscados ou os obrigue a alterar sua senha com segurança.

### <a name="closing-risk-events-manually"></a>Fechar eventos de risco manualmente

Na maioria dos casos, você tomará ações de correção, como uma redefinição de senha de segurança, para fechar automaticamente os eventos de risco. No entanto, isso nem sempre é possível.  
É o caso, por exemplo, quando:

* um usuário com eventos de risco ativo foi excluído
* uma investigação revela que um evento de risco relatado foi executado pelo usuário legítimo

Já que eventos de risco **Ativos** contribuem para o cálculo de risco do usuário, talvez seja necessário reduzir manualmente um nível de risco fechando eventos de risco manualmente.  
Durante a investigação, você pode optar por executar uma das seguintes ações para alterar o status de um evento de risco:

![Ações](./media/active-directory-identityprotection/34.png "Ações")

* **Resolver** - Se, após investigar um evento de risco, você tomou uma ação de correção apropriada fora do Identity Protection e acredita que o evento de risco deve ser considerado fechado, marque o evento como Resolvido. Eventos resolvidos definirão o status do evento de risco como Fechado e ele não contribuirá com o risco do usuário.
* **Marcar como falso positivo** - Em alguns casos, você pode investigar um evento de risco e descobrir que ele foi sinalizado incorretamente como uma situação arriscada. Você pode ajudar a reduzir o número de tais ocorrências marcando o evento de risco como falso positivo. Isso ajudará os algoritmos de aprendizado de máquina a melhorarem a classificação de eventos semelhantes no futuro. O status de eventos falso positivos é **Fechado** e não contribui mais com o risco do usuário.
* **Ignorar** - Se você não executou nenhuma ação de correção, mas deseja remover o evento de risco da lista ativa, marque um evento de risco como Ignorar e o status do evento será Fechado. Eventos ignorados não contribuem com o risco do usuário. Essa opção deve ser usada somente em circunstâncias incomuns.
* **Reativa**r - Eventos de risco fechados manualmente (escolhendo **Resolver**, **Falso positivo** ou **Ignorar**) podem ser reativados, definindo o status do evento novamente como **Ativo**. Eventos de risco reativados contribuem no cálculo de nível de risco do usuário. Eventos de risco fechados por meio de correção (como redefinir uma senha de segurança) não podem ser reativados.

**Para abrir o diálogo de configurações relacionadas**:

1. Na folha **Azure AD Identity Protection**, em **Investigar**, clique em **Eventos de risco**.

    ![Redefinição de senha manual](./media/active-directory-identityprotection/1002.png "Redefinição de senha manual")
2. Na lista **Eventos de risco** , clique em um risco.

    ![Redefinição de senha manual](./media/active-directory-identityprotection/1003.png "Redefinição de senha manual")
3. Na folha de risco, clique com o botão direito do mouse em um usuário.

    ![Redefinição de senha manual](./media/active-directory-identityprotection/1004.png "Redefinição de senha manual")

### <a name="closing-all-risk-events-for-a-user-manually"></a>Fechar manualmente todos os eventos de risco para um usuário
Em vez de fechar manualmente os eventos de risco de um usuário de forma individual, o Azure Active Directory Identity Protection também fornece um método para fechar todos os eventos para um usuário com um clique.

![Ações](./media/active-directory-identityprotection/2222.png "Ações")

Quando você clica em **Descartar todos os eventos**, todos os eventos são fechados e o usuário afetado não está mais em risco.

### <a name="remediating-user-risk-events"></a>Corrigir eventos de risco do usuário

Uma correção é uma ação que visa proteger uma identidade ou um dispositivo que já sofreu comprometimento conhecido ou suspeito anteriormente. Uma correção restaura a identidade ou dispositivo para um estado seguro e resolve eventos de risco anteriores associados à identidade ou ao dispositivo.

Para corrigir os eventos de risco do usuário, você pode:

* executar uma redefinição de senha de segurança para corrigir eventos de risco do usuário manualmente
* configurar uma política de segurança de risco do usuário para mitigar ou corrigir eventos de risco do usuário automaticamente
* recriar imagem no dispositivo infectado  

#### <a name="manual-secure-password-reset"></a>Redefinição de senha de segurança manual
Uma redefinição de senha de segurança é uma correção eficiente para muitos eventos de risco e quando executada, automaticamente fecha esses eventos de risco e recalcula o nível de risco do usuário. Você pode usar o painel do Identity Protection para iniciar uma redefinição de senha para um usuário arriscado.

A caixa de diálogo correspondente fornece dois métodos diferentes para redefinir uma senha:

**Redefinir senha** - selecione **Exigir que o usuário redefina a senha** para permitir que o usuário se recupere automaticamente se ele estiver registrado para autenticação multifator. Durante da próxima entrada do usuário, ele precisará resolver um desafio de autenticação multifator com êxito e em seguida, será forçado a alterar a senha. Essa opção não estará disponível se a conta de usuário não estiver registrada para autenticação multifator.

**Senha temporária** - selecione **Gerar uma senha temporária** para invalidar imediatamente a senha existente e criar uma nova senha temporária para o usuário. Envie a nova senha temporária para um endereço de email alternativo para o usuário ou para o gerente dele. Como a senha é temporária, o usuário precisará alterá-la após entrar.

![Política](./media/active-directory-identityprotection/1005.png "Política")

**Para abrir o diálogo de configurações relacionadas**:

1. Na folha **Azure AD Identity Protection**, clique em **Usuários com riscos sinalizados**.

    ![Redefinição de senha manual](./media/active-directory-identityprotection/1006.png "Redefinição de senha manual")
2. Na lista de usuários, selecione um usuário com eventos de pelo menos um risco.

    ![Redefinição de senha manual](./media/active-directory-identityprotection/1007.png "Redefinição de senha manual")
3. Na folha de usuário, clique em **Redefinir senha**.

    ![Redefinição de senha manual](./media/active-directory-identityprotection/1008.png "Redefinição de senha manual")

### <a name="user-risk-security-policy"></a>Política de segurança de risco do usuário
Uma política de segurança de risco do usuário é uma política de acesso condicional que avalia o nível de risco de um usuário específico e aplica ações de correção e mitigação com base em regras e condições predefinidas.

![Política do usuário ridk](./media/active-directory-identityprotection/1009.png "Política do usuário ridk")

O Azure AD Identity Protection ajuda a gerenciar a mitigação e correção de usuários sinalizados para riscos, permitindo:

* Defina os usuários e os grupos aos quais a política se aplica:

    ![Política do usuário ridk](./media/active-directory-identityprotection/1010.png "Política do usuário ridk")
* Defina o limite de nível de risco do usuário (baixo, médio ou alto) que dispara a política:

    ![Política do usuário ridk](./media/active-directory-identityprotection/1011.png "Política do usuário ridk")
* Defina os controles a serem impostos quando a política for disparada:

    ![Política do usuário ridk](./media/active-directory-identityprotection/1012.png "Política do usuário ridk")
* Alterne o estado de sua política:

    ![Política do usuário ridk](./media/active-directory-identityprotection/403.png "Registro de MFA")
* Examine e avalie o impacto de uma alteração antes de ativá-la:

    ![Política do usuário ridk](./media/active-directory-identityprotection/1013.png "Política do usuário ridk")

Escolher um limite **Alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários.
No entanto, isso exclui usuários sinalizados com **Baixo** e **Médio** risco da política, o que pode não proteger as identidades ou os dispositivos que sofreram comprometimento conhecido ou suspeito.

Ao definir a política,

* exclua os usuários que tendem a gerar muitos falsos positivos (desenvolvedores e analistas de segurança)
* exclua os usuários em localidades em que não é viável habilitar a política (por exemplo, sem acesso à assistência técnica)
* use um limite **Alto** durante a distribuição inicial de política ou se você precisar minimizar os desafios encontrados pelos usuários finais.
* use um limite **Baixo** se sua organização exigir uma segurança maior. Selecionar um limite **Baixo** apresenta desafios de entrada do usuário adicionais, porém representa uma segurança maior.

O padrão recomendado na maioria das organizações é configurar uma regra para um limite **Médio** para atingir um equilíbrio entre segurança e usabilidade.

Para obter uma visão geral da experiência do usuário relacionada, confira:

* [Fluxo de recuperação de conta comprometida](active-directory-identityprotection-flows.md#compromised-account-recovery).  
* [Fluxo de conta comprometida bloqueada](active-directory-identityprotection-flows.md#compromised-account-blocked).  

**Para abrir o diálogo de configurações relacionadas**:

- Na folha **Azure AD Identity Protection**, na seção **Configurar**, clique em **Política de risco de usuário**.

    ![Política do usuário ridk](./media/active-directory-identityprotection/1009.png "Política do usuário ridk")

### <a name="mitigating-user-risk-events"></a>Mitigar eventos de risco do usuário
Os administradores podem definir uma política de segurança de risco do usuário para bloquear os usuários ao entrar dependendo do nível de risco.

Bloquear a entrada:

* impede a geração de novos eventos de risco do usuário para o usuário afetado
* Permite aos administradores corrigir os eventos de risco que afetam a identidade do usuário manualmente, bem como restaurá-la para um estado seguro



## <a name="multi-factor-authentication-registration-policy"></a>Política de registro de autenticação multifator
A autenticação multifator do Azure é um método de verificar quem você é e que requer o uso de mais do que apenas um nome de usuário e uma senha. Ele fornece uma segunda camada de segurança para logons de usuário e transações.  
Recomendamos exigir a autenticação multifator do Azure para entradas de usuário porque:

* fornece autenticação forte com uma variedade de opções de verificação fácil
* desempenha um papel fundamental na preparação de sua organização para proteger e recuperar comprometimentos de conta

![Política do usuário ridk](./media/active-directory-identityprotection/1019.png "Política do usuário ridk")

Para obter mais detalhes, veja [O que é o Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)

O Azure AD Identity Protection ajuda a gerenciar a implementação do registro de autenticação multifator configurando uma política que permite:

* Defina os usuários e os grupos aos quais a política se aplica:

    ![Política de MFA](./media/active-directory-identityprotection/1020.png "Política de MFA")
* Defina os controles a serem impostos quando a política for disparada:  

    ![Política de MFA](./media/active-directory-identityprotection/1021.png "Política de MFA")
* Alterne o estado de sua política:

    ![Política de MFA](./media/active-directory-identityprotection/403.png "Política de MFA")
* Exiba o status atual do registro:

    ![Política de MFA](./media/active-directory-identityprotection/1022.png "Política de MFA")

Para obter uma visão geral da experiência do usuário relacionada, confira:

* [Fluxo do registro de autenticação multifator](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  
* [Experiências de entrada com o Azure AD Identity Protection](active-directory-identityprotection-flows.md).  

**Para abrir o diálogo de configurações relacionadas**:

- Na folha **Azure AD Identity Protection**, na seção **Configurar**, clique em **Registro de autenticação multifator**.

    ![Política de MFA](./media/active-directory-identityprotection/1019.png "Política de MFA")

## <a name="next-steps"></a>Próximas etapas
* [Canal 9: Azure AD e Identity Show: visualização do Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

* [Habilitando o Azure Active Directory Identity Protection](active-directory-identityprotection-enable.md)

* [Vulnerabilidades detectadas pelo Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md)

* [Eventos de risco do Azure Active Directory](active-directory-identity-protection-risk-events.md)

* [Notificações do Azure Active Directory Identity Protection](active-directory-identityprotection-notifications.md)

* [Guia estratégico do Azure Active Directory Identity Protection](active-directory-identityprotection-playbook.md)

* [Glossário do Azure Active Directory Identity Protection.](active-directory-identityprotection-glossary.md)

* [Experiências de entrada com o Azure AD Identity Protection](active-directory-identityprotection-flows.md)

* [Azure Active Directory Identity Protection - como desbloquear usuários](active-directory-identityprotection-unblock-howto.md)

* [Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
