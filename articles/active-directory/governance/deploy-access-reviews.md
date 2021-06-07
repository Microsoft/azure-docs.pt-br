---
title: Planejar uma implantação de revisões de Azure Active Directory de acesso
description: Guia de planejamento para uma implantação de revisões de acesso bem-sucedidas
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4122e645b76751e8944704a6405cf5dee09129f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97932428"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Planejamento Azure Active Directory implantação de revisões de acesso

As [revisões de acesso do Azure Active Directory (Azure AD)](access-reviews-overview.md) ajudam sua organização a manter a rede mais segura gerenciando seu [ciclo de vida de acesso aos recursos](identity-governance-overview.md). Com as revisões de acesso, você pode:

* Agende revisões regulares ou Realize revisões ad hoc para ver quem tem acesso a recursos específicos, como aplicativos e grupos

* Acompanhe as revisões de informações, conformidade ou motivos de política

* Delegue revisões para administradores específicos, proprietários de negócios ou usuários finais que podem se atestar automaticamente para a necessidade de acesso contínuo

* Use as informações para determinar com eficiência se os usuários devem continuar a ter acesso

* Automatizar resultados de revisão, como remover o acesso de usuários aos recursos

  ![Diagrama que mostra o fluxo de revisões de acesso.](./media/deploy-access-review/1-planning-review.png)

As revisões de acesso são um recurso [Azure ad Identity Governance](identity-governance-overview.md) . Os outros recursos são [Gerenciamento de direitos](entitlement-management-overview.md), [Privileged Identity Management](../privileged-identity-management/pim-configure.md) e [termos de uso](../conditional-access/terms-of-use.md). Juntos, eles ajudam as organizações a resolver essas quatro perguntas:

* Quais usuários devem ter acesso a quais recursos?

* O que esses usuários estão fazendo com esse acesso?

* Há um controle organizacional eficaz para o gerenciamento de acesso?

* Auditores podem verificar se os controles estão funcionando?

Planejar sua implantação de revisões de acesso é essencial para garantir que você atinja a estratégia de governança desejada para os usuários em sua organização.

### <a name="key-benefits"></a>Principais benefícios

Os principais benefícios de habilitar as revisões de acesso são:

* **Controle a colaboração**. As revisões de acesso permitem que as organizações gerenciem o acesso a todos os recursos de que seus usuários precisam. Quando seus usuários compartilham e colaboram, as organizações podem ter certeza de que as informações estão entre usuários autorizados.

* **Gerencie riscos**. As revisões de acesso fornecem às organizações uma maneira de examinar o acesso a dados e aplicativos, reduzindo o risco de vazamento de dados e despejo de dados. Isso inclui recursos para examinar regularmente o acesso do parceiro externo aos recursos corporativos. 

* **Resolva a conformidade e a governança**. Com as revisões de acesso, você pode controlar e recertificar o ciclo de vida de acesso a grupos, aplicativos e sites. Você pode controlar as revisões de controle de conformidade ou aplicativos sensíveis a riscos específicos à sua organização. 

* **Reduza o custo**. As revisões de acesso são criadas na nuvem e funcionam nativamente com recursos de nuvem, como grupos, aplicativos e pacotes de acesso. O uso de revisões de acesso é menos dispendioso do que criar suas próprias ferramentas ou atualizar seu conjunto de ferramentas local.

### <a name="training-resources"></a>Recursos de treinamento

Os vídeos a seguir podem ser úteis à medida que você aprende sobre as revisões de acesso:

* [O que são revisões de acesso no Azure AD?](https://youtu.be/kDRjQQ22Wkk)

* [Como criar revisões de acesso no Azure AD](https://youtu.be/6KB3TZ8Wi40)

* [Como habilitar as revisões de acesso no Azure AD](https://youtu.be/X1SL2uubx9M)

* [Como revisar o acesso usando meu acesso](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Licenças

Você precisa de uma licença válida de Azure AD Premium (P2) para cada pessoa, além de administradores globais ou administradores de usuários, que criarão ou realizarão revisões de acesso. Para obter mais informações, consulte [acesso examina requisitos de licença](access-reviews-overview.md).

Você também pode precisar de outros recursos de governança de identidade, como [Gerenciamento do ciclo de vida de autorização](entitlement-management-overview.md) ou gerenciamento de identidades privilegiados. Nesse caso, você também pode precisar de licenças relacionadas. Para obter mais informações, consulte [Preço do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="plan-the-access-reviews-deployment-project"></a>Planejar o projeto de implantação de revisões de acesso

Considere suas necessidades organizacionais para determinar a estratégia de implantação de revisões de acesso em seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, eles normalmente fazem isso devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, [certifique-se de que você esteja envolvendo as partes interessadas certas](../fundamentals/active-directory-deployment-plans.md) e que as funções do projeto sejam claras.

Para as revisões de acesso, você provavelmente incluirá os representantes das seguintes equipes em sua organização:

* A **Administração de ti** gerencia sua infraestrutura de ti e administra seus investimentos em nuvem e aplicativos SaaS (software como serviço). Essa equipe irá:

   * Examine o acesso privilegiado a infraestrutura e aplicativos, incluindo o Microsoft 365 e o Azure AD.

   * Agende e execute revisões de acesso em grupos que são usados para manter listas de exceções ou projetos de piloto de ti, para manter listas de acesso atualizadas.

   * Verifique se o acesso programático (com script) a recursos por meio de entidades de serviço é regido e revisado.

* As **equipes de desenvolvimento** criam e mantêm aplicativos para sua organização. Essa equipe irá:

   * Controle quem pode acessar e gerenciar componentes em SaaS, PaaS e recursos de IaaS que compõem as soluções desenvolvidas.

   * Gerencie grupos que podem acessar aplicativos e ferramentas para o desenvolvimento de aplicativos internos.

   * Exigir identidades com privilégios que tenham acesso ao software de produção ou soluções que são hospedadas para seus clientes

* As **unidades de negócios** gerenciam projetos e aplicativos próprios. Essa equipe irá: 

   * Revise e aprove ou negue o acesso a grupos e aplicativos para usuários internos e externos.

   * Agende e realize revisões para atestar o acesso contínuo a funcionários e identidades externas, como parceiros de negócios.

* A **governança corporativa** garante que a organização esteja seguindo a política interna e esteja em conformidade com os regulamentos. Essa equipe irá:

   * Solicitar ou agendar novas revisões de acesso.

   * Avalie processos e procedimentos para revisar o acesso, incluindo documentação e registro de manutenção para fins de conformidade.

   * Examine os resultados das revisões anteriores para obter os recursos mais críticos.

> [!NOTE]
> Para revisões que exigem avaliações manuais, certifique-se de planejar os revisores adequados e os ciclos de revisão que atendam às suas necessidades de conformidade e política. Se os ciclos de revisão forem muito frequentes ou houver poucos revisores, a qualidade poderá ser perdida e muitas ou poucas pessoas poderão ter acesso. 

### <a name="plan-communications"></a>Planejar a comunicação

A comunicação é fundamental para o sucesso de qualquer novo processo de negócios. Comunique-se de forma proativa aos usuários como e quando sua experiência será alterada e como obter suporte se eles tiverem problemas. 

#### <a name="communicate-changes-in-accountability"></a>Comunicar alterações na responsabilidade

As revisões de acesso dão suporte à mudança da responsabilidade de revisar e agir sobre o acesso contínuo aos proprietários de negócios. A desassociação de decisões de acesso da ti impulsiona decisões de acesso mais precisas. Essa é uma alteração cultural na responsabilidade e responsabilidade do proprietário do recurso. Comunique-se proativamente essa alteração e garanta que os proprietários de recursos sejam treinados e possam usar as informações para tomar boas decisões.

Claramente, ele desejará manter o controle de todas as decisões de acesso relacionadas à infraestrutura e as atribuições de função com privilégios. 

#### <a name="customize-email-communication"></a>Personalizar a comunicação por email

Ao agendar uma revisão, você nomea os usuários que executarão essa revisão. Esses revisores recebem uma notificação por email sobre novas revisões atribuídas a eles, bem como lembretes antes de uma revisão atribuída a eles expirar.

Os administradores podem optar por enviar essa notificação de meio caminho antes que a revisão expire ou um dia antes de sua expiração. 

O email enviado aos revisores pode ser personalizado para incluir uma mensagem curta personalizada que os incentiva a agir na análise. Recomendamos que você use o texto adicional para:

* Inclua uma mensagem pessoal para os revisores, para que eles compreendam que são enviados por seu departamento de conformidade ou de ti.

* Inclua um hiperlink ou referência a informações internas sobre quais são as expectativas da revisão, além de referência adicional ou material de treinamento.

* Inclua um link para obter instruções sobre [como executar uma autorevisão do Access.](review-your-access.md) 

  ![Email do revisor](./media/deploy-access-review/2-plan-reviewer-email.png)

Após selecionar Iniciar revisão, os revisores serão direcionados para o [portal myaccess](https://myapplications.microsoft.com/) para revisões de acesso de grupo e aplicativo. O portal fornece uma visão geral de todos os usuários que têm acesso ao recurso que estão sendo revisados e recomendações do sistema com base na última entrada e informações de acesso.

### <a name="plan-a-pilot"></a>Planejar um piloto

Incentivamos os clientes a revisar inicialmente as revisões de acesso com um pequeno grupo e direcionar os recursos não críticos. O piloto pode ajudá-lo a ajustar os processos e as comunicações conforme necessário e aumentar a capacidade dos usuários e dos revisores para atender aos requisitos de segurança e conformidade.

No seu piloto, recomendamos:

* Comece com revisões em que os resultados não são aplicados automaticamente e você pode controlar as implicações.

* Verifique se todos os usuários têm endereços de email válidos listados no Azure AD e se eles recebem comunicação de email para executar a ação apropriada. 

* Documente qualquer acesso removido como parte do piloto, caso você precise restaurá-lo rapidamente.

* Monitore os logs de auditoria para garantir que todos os eventos sejam auditados corretamente.

Para obter mais informações, consulte [práticas recomendadas para um piloto](../fundamentals/active-directory-deployment-plans.md).

## <a name="introduction-to-access-reviews"></a>Introdução às revisões de acesso

Esta seção apresenta conceitos de análise de acesso que você deve saber antes de planejar suas revisões.

### <a name="what-resource-types-can-be-reviewed"></a>Quais tipos de recursos podem ser revisados?

Depois de integrar os recursos da sua organização ao Azure AD (como usuários, aplicativos e grupos), eles poderão ser gerenciados e revisados. 

Os destinos típicos para análise incluem:

* [Aplicativos integrados ao Azure ad para logon único](../manage-apps/what-is-application-management.md) (como SaaS, linha de negócios).

* [Associação](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) de grupo (sincronizada com o Azure ad ou criada no Azure ad ou Microsoft 365, incluindo Microsoft Teams).

* O [pacote do Access](./entitlement-management-overview.md) que agrupa recursos (grupos, aplicativos e sites) em um único pacote para gerenciar o acesso.

* [Funções do Azure AD e funções de recurso do Azure](../privileged-identity-management/pim-resource-roles-assign-roles.md) , conforme definido em Privileged Identity Management.

### <a name="who-will-create-and-manage-access-reviews"></a>Quem vai criar e gerenciar as revisões de acesso?

A função administrativa necessária para criar, gerenciar ou ler uma revisão de acesso depende do tipo de recurso que está sendo revisado. A tabela a seguir denota as funções necessárias para cada tipo de recurso:

| Tipo de recurso| Criar e gerenciar revisões de acesso (criadores)| Ler resultados da revisão de acesso |
| - | - | -|
| Grupo ou aplicativo| Administrador Global <p>Administrador de usuários| Criadores e administrador de segurança |
| Funções com privilégios no Azure AD| Administrador Global <p>Administrador de função com privilégios| Criadores <p>Leitor de segurança<p>Administrador de Segurança |
| Funções com privilégios no Azure (recursos)| Administrador Global<p>Administrador de usuários<p>Proprietário do recurso| Criadores |
| Pacote de acesso| Administrador Global<p>Criador do pacote de acesso| Somente administrador global |


Para obter mais informações, consulte [Permissões da função de administrador no Azure Active Directory](../roles/permissions-reference.md).

### <a name="who-will-review-the-access-to-the-resource"></a>Quem vai revisar o acesso ao recurso?

O criador da revisão de acesso decide no momento da criação que realizará a revisão. Essa configuração não pode ser alterada depois que a revisão é iniciada. Os revisores são representados por três personas:

* Proprietários de recursos, que são os proprietários de negócios do recurso.

* Um conjunto de delegados selecionados individualmente, conforme selecionado pelo administrador de revisões de acesso.

* Os usuários finais que serão cada um autoatesta para sua necessidade de acesso contínuo.

Ao criar uma revisão de acesso, os administradores podem escolher um ou mais revisores. Todos os revisores podem iniciar e executar uma revisão, escolhendo os usuários para obter acesso contínuo a um recurso ou removê-los. 

### <a name="components-of-an-access-review"></a>Componentes de uma revisão de acesso

Antes de implementar suas revisões de acesso, você deve planejar os tipos de revisões relevantes para sua organização. Para fazer isso, você precisará tomar decisões de negócios sobre o que deseja examinar e as ações a serem executadas com base nessas revisões.

Para criar uma política de revisão de acesso, você deve ter as seguintes informações.

* Quais são os recursos a serem revisados?

* Cujo acesso está sendo revisado.

* Com que frequência a revisão deve ocorrer?

* Quem executará a análise?

   * Como eles serão notificados para análise?

   * Quais são as linhas do tempo a serem impostas para análise?

* Quais ações automáticas devem ser impostas com base na análise?

   * O que acontece se o revisor não responder no tempo?

* Quais ações manuais serão tomadas como resultado com base na análise?

* Quais comunicações devem ser enviadas com base nas ações executadas?


**Plano de revisão de acesso de exemplo**

| Componente| Valor |
| - | - |
| **Recursos a serem examinados**| Acesso ao Microsoft Dynamics |
| **Frequência de revisão**| Mensal |
| **Quem executa a revisão**| Gerentes de programa do Dynamics Business Group |
| **Notificação**| Email 24 horas antes da revisão para o alias Dynamics-Pms<p>Incluir a encorajação de mensagens personalizadas a revisores para proteger sua compra |
| **Linha do tempo**| 48 horas da notificação |
|**Ações automáticas**| Remova o acesso de qualquer conta que não tenha entrada interativa dentro de 90 dias, removendo o usuário do grupo de segurança Dynamics-Access. <p>*Execute ações se não forem revisadas na linha do tempo.* |
| **Ações manuais**| Os revisores podem executar a aprovação de remoções antes da ação automatizada, se desejado. |
| **Comunicações**| Enviar usuários internos (Membros) que foram removidos um email explicando que eles são removidos e como reobter o acesso. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Automatizar ações com base nas revisões de acesso

Você pode optar por ter a remoção de acesso automatizada Configurando a opção aplicar resultados automaticamente ao recurso para habilitar o.

  ![Planejando revisões de acesso](./media/deploy-access-review/3-automate-actions-settings.png)

Depois que a revisão for concluída e finalizada, os usuários que não foram aprovados pelo revisor serão removidos automaticamente do recurso – ou mantidos com o acesso contínuo. Isso pode significar a remoção de sua associação de grupo, sua atribuição de aplicativo ou a revogação de seu direito de elevação para uma função privilegiada.

Fazer recomendações

As recomendações são exibidas para os revisores como parte da experiência do revisor e indicam a última entrada da pessoa para o locatário ou o último acesso a um aplicativo. Essas informações ajudam os revisores a tomar a decisão de acesso certa. A seleção de recomendações seguirá as recomendações da revisão de acesso. No final de uma revisão de acesso, o sistema aplicará essas recomendações automaticamente para os usuários aos quais os revisores não responderam.

As recomendações são baseadas nos critérios na revisão de acesso. Por exemplo, se você configurar a revisão para remover o acesso sem entrada interativa por 90 dias, será recomendável que todos os usuários que se ajustam aos critérios sejam removidos. A Microsoft está trabalhando continuamente para aprimorar as recomendações. 

### <a name="review-guest-user-access"></a>Examinar o acesso do usuário convidado

Use as revisões de acesso para revisar e limpar identidades de parceiros de colaboração de organizações externas. A configuração de uma revisão por parceiro pode atender aos requisitos de conformidade.

Identidades externas podem receber acesso aos recursos da empresa por meio de uma das seguintes ações:

* Adicionado a um grupo 

* Convidado para equipes 

* Atribuído a um aplicativo empresarial ou a um pacote de acesso

* Atribuiu uma função com privilégios no Azure AD ou em uma assinatura do Azure

Consulte [script de exemplo](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). O script mostrará onde as identidades externas convidadas para o locatário são usadas. Você pode ver a associação de grupo, as atribuições de função e as atribuições de aplicativo do usuário externo no Azure AD. O script não mostrará as atribuições fora do Azure AD, por exemplo, atribuição de direitos diretos aos recursos do SharePoint, sem o uso de grupos.

Ao criar uma revisão de acesso para grupos ou aplicativos, você pode optar por permitir que o revisor se concentre em todos com acesso ou somente a usuários convidados. Ao selecionar somente usuários convidados, os revisores recebem uma lista focada de identidades externas do Azure AD B2B que têm acesso ao recurso.

 ![Examinar usuários convidados](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> Isso não incluirá membros externos que tenham um UserType de membro. Isso também não incluirá usuários convidados fora da colaboração B2B do Azure AD, por exemplo, aqueles que têm acesso ao conteúdo compartilhado diretamente por meio do SharePoint.

## <a name="plan-access-reviews-for-access-packages"></a>Planejar revisões de acesso para pacotes do Access

Os [pacotes do Access](entitlement-management-overview.md) podem simplificar amplamente sua estratégia de governança e de análise de acesso. Um pacote do Access é um pacote de todos os recursos com o acesso que um usuário precisa para trabalhar em um projeto ou executar sua tarefa. Por exemplo, talvez você queira criar um pacote de acesso que inclui todos os aplicativos que os desenvolvedores de sua organização precisam ou todos os aplicativos aos quais os usuários externos devem ter acesso. Um administrador ou um Gerenciador de pacotes de acesso delegado agrupa os recursos (grupos ou aplicativos) e as funções de que os usuários precisam para esses recursos.

Ao [criar um pacote do Access](entitlement-management-access-package-create.md), você pode criar uma ou mais políticas de acesso que definem condições para as quais os usuários podem solicitar um pacote de acesso, a aparência do processo de aprovação e com que frequência uma pessoa teria que solicitar o acesso novamente. As revisões de acesso são configuradas durante a criação ou edição de uma política de pacote de acesso.

Abra a guia ciclo de vida para rolar para baixo até revisões de acesso.

 ![Captura de tela que mostra a "Editar política" na guia "ciclo de vida".](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Planejar revisões de acesso para grupos

Além de pacotes de acesso, a revisão da Associação de grupo é a maneira mais eficiente de controlar o acesso. É recomendável que o acesso aos recursos seja atribuído por [grupos de segurança ou grupos de Microsoft 365](../fundamentals/active-directory-manage-groups.md)e que os usuários sejam adicionados a esses grupos para obter acesso.

Um único grupo pode receber acesso a todos os recursos apropriados. Você pode atribuir o acesso de grupo a recursos individuais ou a um pacote de acesso que agrupa aplicativos e outros recursos. Com esse método, você pode examinar o acesso ao grupo em vez do acesso de um indivíduo a cada aplicativo. 

A associação de grupo pode ser revisada por: 

* Administradores

* Proprietários do grupo

* Usuários selecionados, recurso de revisão delegada quando a revisão é criada

* Membros do grupo, atestado para si mesmos

### <a name="group-ownership"></a>Propriedade do grupo

Recomendamos que os proprietários do grupo examinem a associação, pois eles estão mais bem situados para saber quem precisa de acesso. A propriedade de grupos difere com o tipo de Grupo:

Os grupos criados no Microsoft 365 e no Azure AD têm um ou mais proprietários bem definidos. Na maioria dos casos, esses proprietários fazem revisores perfeitos para seus próprios grupos, pois sabem quem deve ter acesso. 

Por exemplo, o Microsoft Teams usa grupos de Microsoft 365 como o modelo de autorização subjacente para conceder aos usuários acesso a recursos que estão no SharePoint, Exchange, OneNote ou outros serviços Microsoft 365. O criador da equipe se torna automaticamente um proprietário e deve ser responsável por atestar a associação do grupo. 

Os grupos criados manualmente no portal do Azure AD ou por meio de scripts por meio de Microsoft Graph podem não necessariamente ter proprietários definidos. Recomendamos que você os defina por meio do portal do AD do Azure na seção "proprietários" do grupo ou por meio do grafo.

Os grupos que são sincronizados do Active Directory local não podem ter um proprietário no Azure AD. Ao criar uma revisão de acesso para eles, você deve selecionar indivíduos mais adequados para decidir sobre associação neles.

> [!NOTE]
> É recomendável definir políticas de negócios que definem como os grupos são criados para garantir uma propriedade de grupo clara e responsabilidade para a revisão regular da associação. 

### <a name="review-membership-of-exclusion-groups-in-conditional-access-policies"></a>Examinar a associação de grupos de exclusão em políticas de acesso condicional 

Há ocasiões em que as políticas de acesso condicional projetadas para manter sua rede segura não devem ser aplicadas a todos os usuários. Por exemplo, uma política de acesso condicional que permite apenas que os usuários entrem enquanto na rede corporativa pode não se aplicar à equipe de vendas, que viaja extensivamente. Nesse caso, os membros da equipe de vendas seriam colocados em um grupo e esse grupo seria excluído da política de acesso condicional. 

Examine essa associação de grupo regularmente, pois a exclusão representa um risco em potencial se os membros errados forem excluídos do requisito.

Você pode [usar as revisões de acesso do Azure ad para gerenciar usuários excluídos das políticas de acesso condicional](conditional-access-exclusion.md).

### <a name="review-external-users-group-memberships"></a>Examinar as associações de grupo do usuário externo

Para minimizar o trabalho manual e os possíveis erros associados, considere o uso de [grupos dinâmicos](../enterprise-users/groups-create-rule.md) para atribuir a associação de grupo com base nos atributos de um usuário. Talvez você queira criar um ou mais grupos dinâmicos para usuários externos. O patrocinador interno pode agir como um revisor para associação no grupo. 

Observação: usuários externos que são removidos de um grupo como o resultado de uma revisão de acesso não são excluídos do locatário. 

Eles podem ser excluídos de um locatário removidos manualmente ou por meio de um script.

### <a name="review-access-to-on-premises-groups"></a>Examinar o acesso a grupos locais

As revisões de acesso não podem alterar a associação de grupo de grupos que você sincroniza do local com [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md). Isso ocorre porque a origem da autoridade é local.

Você ainda pode usar as revisões de acesso para agendar e manter revisões regulares de grupos locais. Os revisores executarão ações no grupo local. Essa estratégia mantém as revisões de acesso como a ferramenta para todas as revisões.

Você pode usar os resultados de uma revisão de acesso em grupos locais e processá-los ainda mais, seja pelo:

* Baixar o relatório CSV da revisão de acesso e executar a ação manualmente.

* Usar Microsoft Graph para acessar programaticamente resultados e decisões em revisões de acesso concluídas.

Por exemplo, para acessar os resultados de um grupo gerenciado pelo Windows AD, use este [script de exemplo do PowerShell](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises). O script descreve as chamadas de gráfico necessárias e exporta os comandos do Windows AD-PowerShell para realizar as alterações.

## <a name="plan-access-reviews-for-applications"></a>Planejar revisões de acesso para aplicativos 

Ao examinar o acesso a um aplicativo, você está examinando o acesso de funcionários e identidades externas às informações e aos dados dentro do aplicativo. Escolha examinar um aplicativo quando precisar saber quem tem acesso a um aplicativo específico, em vez de um pacote de acesso ou um grupo. 

Recomendamos que você planeje as revisões de aplicativos nos seguintes cenários:

* Os usuários recebem acesso direto ao aplicativo (fora de um grupo ou pacote de acesso).

* O aplicativo expõe informações críticas ou confidenciais.

* O aplicativo tem requisitos de conformidade específicos para os quais você deve atestar.

* Você suspeita de acesso inadequado.

Para criar revisões de acesso para um aplicativo, defina a atribuição de usuário necessária? Propriedade como Sim. Se definido como não, todos os usuários em seu diretório, incluindo identidades externas, podem acessar o aplicativo e você não pode examinar o acesso ao aplicativo. 

 ![planejar atribuições de aplicativo](./media/deploy-access-review/6-plan-applications-assignment-required.png)

Em seguida, você deve [atribuir os usuários e grupos](../manage-apps/assign-user-or-group-access-portal.md) aos quais você deseja ter acesso. 

### <a name="reviewers-for-an-application"></a>Revisores para um aplicativo

As revisões de acesso podem ser de membros de um grupo ou usuários que foram atribuídos a um aplicativo. Os aplicativos no Azure AD não têm necessariamente um proprietário, motivo pelo qual a opção para selecionar o proprietário do aplicativo como um revisor não é possível. Você pode ainda obter um melhor escopo para examinar apenas os usuários convidados atribuídos ao aplicativo, em vez de revisar todo o acesso.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Revisão do plano do Azure AD e das funções de recurso do Azure

O [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) simplifica o modo como as empresas gerenciam o acesso privilegiado aos recursos no Azure AD. Isso mantém a lista de funções privilegiadas, tanto no [Azure ad](../roles/permissions-reference.md) quanto em [recursos do Azure](../../role-based-access-control/built-in-roles.md) muito menores e aumenta a segurança geral do diretório.

As revisões de acesso permitem que os revisores atestam se os usuários ainda precisam estar em uma função. Assim como as revisões de acesso para pacotes de acesso, as revisões para funções do Azure AD e recursos do Azure são integradas à experiência do usuário de administrador do PIM. Recomendamos que você revise as seguintes atribuições de função regularmente:

* Administrador Global

* Administrador de usuários

* Administrador de Autenticação Privilegiada

* Administrador de Acesso Condicional

* Administrador de Segurança

* Todas as funções de administração de serviço Microsoft 365 e Dynamics

As funções selecionadas aqui incluem função permanente e qualificada. 

Na seção Revisores, selecione uma ou mais pessoas para examinar todos os usuários no escopo. Ou você pode selecionar para que os membros examinem seus próprios acessos.

 ![Editar política](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Implantar revisões de acesso

Depois de preparar uma estratégia e um plano para revisar o acesso aos recursos integrados ao Azure AD, implante e gerencie as revisões usando os recursos abaixo.

### <a name="review-access-packages"></a>Examinar pacotes de acesso

Para reduzir o risco de acesso obsoleto, os administradores podem habilitar revisões periódicas de usuários que têm atribuições ativas a um pacote do Access. Siga as instruções no link abaixo:

| Artigos de instruções| Descrição |
| - | - |
| [Criar revisões de acesso](entitlement-management-access-reviews-create.md)| Habilitar revisões do pacote de acesso. |
| [Executar revisões de acesso](entitlement-management-access-reviews-review-access.md)| Executar revisões de acesso para outros usuários atribuídos a um pacote do Access. |
| [Revisar pacote (s) de acesso atribuído por conta própria](entitlement-management-access-reviews-self-review.md)| Revisão automática de pacotes de acesso atribuídos |


> [!NOTE]
> Os usuários finais que revisam e dizem que não precisam mais de acesso não são removidos imediatamente do pacote de acesso. Eles são removidos do pacote do Access quando a revisão termina ou se um administrador para a revisão.

### <a name="review-groups-and-apps"></a>Examinar grupos e aplicativos

As necessidades de acesso a grupos e aplicativos para funcionários e convidados provavelmente mudam com o passar do tempo. Para reduzir o risco associado às atribuições de acesso obsoletas, os administradores podem criar revisões de acesso para membros do grupo ou acesso ao aplicativo. Siga as instruções no link abaixo:

| Artigos de instruções| Descrição |
| - | - |
| [Criar revisões de acesso](create-access-review.md)| Crie uma ou mais revisões de acesso para membros do grupo ou acesso ao aplicativo. |
| [Executar revisões de acesso](perform-access-review.md)| Executar a revisão de acesso para membros de um grupo ou usuários com acesso a um aplicativo. |
| [Examine automaticamente seu acesso](review-your-access.md)| Os membros revisam o acesso a um grupo ou um aplicativo |
| [Concluir revisão de acesso](complete-access-review.md)| Exibir uma revisão de acesso e aplicar os resultados |
| [Tomar medidas para grupos locais](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| Exemplo de script do PowerShell para agir em revisões de acesso para grupos locais. |


### <a name="review-azure-ad-roles"></a>Examinar as funções do Azure AD

Para reduzir o risco associado a atribuições de função obsoletas, você deve examinar regularmente o acesso de funções privilegiadas do Azure AD.

![Captura de tela que mostra a lista "revisar Associação" das funções do Azure A D.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Siga as instruções nos links abaixo:

| Artigos de instruções | Descrição |
| - | - |
 [Criar revisões de acesso](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Criar revisões de acesso para funções privilegiadas do Azure AD no PIM |
| [Examine automaticamente seu acesso](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Se você estiver atribuído a uma função administrativa, aprove ou negue o acesso à sua função |
| [Concluir uma revisão de acesso](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Exibir uma revisão de acesso e aplicar os resultados |


### <a name="review-azure-resource-roles"></a>Examinar funções de recurso do Azure

Para reduzir o risco associado a atribuições de função obsoletas, você deve examinar regularmente o acesso de funções privilegiadas de recursos do Azure. 

![examinar as funções do Azure AD](./media/deploy-access-review/9-review-azure-roles-picker.png)

Siga as instruções nos links abaixo:

| Artigos de instruções| Descrição |
| - | -|
| [Criar revisões de acesso](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Criar revisões de acesso para funções de recursos do Azure com privilégios no PIM |
| [Examine automaticamente seu acesso](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Se você estiver atribuído a uma função administrativa, aprove ou negue o acesso à sua função |
| [Concluir uma revisão de acesso](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Exibir uma revisão de acesso e aplicar os resultados |


## <a name="use-the-access-reviews-api"></a>Usar a API de revisões de acesso

Consulte [métodos da API do Graph](/graph/api/resources/accessreviews-root?view=graph-rest-beta) e as [verificações de autorização de permissão de aplicativo e função](/graph/api/resources/accessreviews-root?view=graph-rest-beta) para interagir com e gerenciar recursos reviewáveis. Os métodos de revisões de acesso na API Microsoft Graph estão disponíveis para contextos de aplicativo e de usuário. Ao executar scripts no contexto do aplicativo, a conta usada para executar a API (a entidade de serviço) deve receber a permissão "AccessReview. Read. All" para consultar informações de revisões de acesso.

As tarefas populares de revisões de acesso para automatizar o uso do API do Graph para revisões de acesso são:

* Criar e iniciar uma revisão de acesso

* Encerrar manualmente uma revisão de acesso antes da sua extremidade agendada

* Listar todas as revisões de acesso em execução e seu status

* Veja o histórico de uma série de revisão e as decisões e ações tomadas em cada revisão.

* Coletar decisões de uma análise de acesso

* Colete decisões de revisões concluídas em que o revisor levou uma decisão diferente do que o sistema recomendado.

Ao criar novas consultas de API do Graph para automação, recomendamos o uso do [Gerenciador de gráficos](https://developer.microsoft.com/en-us/graph/graph-explorer). Você pode criar e explorar suas consultas de grafo antes de colocá-las em scripts e código. Isso pode ajudá-lo a iterar rapidamente sua consulta para que você obtenha exatamente os resultados que está procurando, sem alterar o código do script.

## <a name="monitor-access-reviews"></a>Monitorar revisões de acesso

As atividades de revisões de acesso são registradas e disponíveis nos [logs de auditoria do Azure ad](../reports-monitoring/concept-audit-logs.md). Você pode filtrar os dados de auditoria na categoria, no tipo de atividade e no intervalo de datas. Aqui está uma consulta de exemplo:

| Categoria| Política |
| - | - |
| Tipo de atividade| Criar a revisão de acesso |
| | Atualizar revisão de acesso |
| | Revisão de acesso finalizada |
| | Excluir revisão de acesso |
| | Aprovar decisão |
| | Decisão de negação |
| | Redefinir decisão |
| | Aplicar decisão |
| Intervalo de datas| sete dias |


Para consultas mais avançadas e análise de revisões de acesso e para controlar as alterações e a conclusão das revisões, recomendamos que você exporte os logs de auditoria do Azure AD para o [azure log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) ou o Hub de eventos do Azure. Quando armazenados no Azure Log Analytics, você pode usar a [linguagem de análise avançada](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) – e criar seus próprios painéis.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as tecnologias relacionadas abaixo.

* [O que é o gerenciamento de direitos do AD do Azure?](entitlement-management-overview.md)

* [O que é o Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)