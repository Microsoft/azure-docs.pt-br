---
title: Usar pastas de trabalho Azure Monitor com Azure AD Domain Services | Microsoft Docs
description: Saiba como usar pastas de trabalho do Azure Monitor para examinar auditorias de segurança e entender problemas em um domínio Azure Active Directory Domain Services gerenciado.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 9a126fe4676f340fb45677382a3aeeed79291f0b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574215"
---
# <a name="review-security-audit-events-in-azure-active-directory-domain-services-using-azure-monitor-workbooks"></a>Examinar eventos de auditoria de segurança em Azure Active Directory Domain Services usando pastas de trabalho Azure Monitor

Para ajudá-lo a entender o estado do seu domínio gerenciado do Azure Active Directory Domain Services (AD DS do Azure), você pode habilitar eventos de auditoria de segurança. Esses eventos de auditoria de segurança podem ser revisados usando Azure Monitor pastas de trabalho que combinam texto, consultas de análise e parâmetros em relatórios interativos sofisticados. O Azure AD DS inclui modelos de pasta de trabalho para visão geral de segurança e atividade de conta que permitem que você se aprofunde em eventos de auditoria e gerencie seu ambiente.

Este artigo mostra como usar pastas de trabalho do Azure Monitor para examinar eventos de auditoria de segurança no AD DS do Azure.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, conclua o tutorial para [criar e configurar um Azure Active Directory Domain Services domínio gerenciado][create-azure-ad-ds-instance].
* Eventos de auditoria de segurança habilitados para seu domínio gerenciado que transmitem dados para um espaço de trabalho Log Analytics.
    * Se necessário, [habilite as auditorias de segurança para o Azure AD DS][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Visão geral das Azure Monitor de trabalho

Quando os eventos de auditoria de segurança são ativados no Azure AD DS, pode ser difícil analisar e identificar problemas no domínio gerenciado. Azure Monitor permite agregar esses eventos de auditoria de segurança e consultar os dados. Com Azure Monitor pastas de trabalho, você pode visualizar esses dados para torná-los mais rápidos e fáceis de identificar problemas.

Modelos de pasta de trabalho são relatórios estruturados que são projetados para reutilização flexível por vários usuários e equipes. Quando você abre um modelo de pasta de trabalho, os dados de seu ambiente de Azure Monitor são carregados. Você pode usar modelos sem impacto sobre outros usuários em sua organização e pode salvar suas próprias pastas de trabalho com base no modelo.

Os AD DS do Azure incluem os dois modelos de pasta de trabalho a seguir:

* Relatório de visão geral de segurança
* Relatório de atividade da conta

Para obter mais informações sobre como editar e gerenciar pastas de trabalho, consulte [visão geral de Azure monitor pastas de trabalho](../azure-monitor/visualize/workbooks-overview.md).

## <a name="use-the-security-overview-report-workbook"></a>Usar a pasta de trabalho de relatório visão geral de segurança

Para ajudá-lo a entender melhor o uso e a identificar possíveis ameaças à segurança, o relatório de visão geral de segurança resume os dados de entrada e identifica as contas que você pode desejar verificar. Você pode exibir eventos em um intervalo de datas específico e fazer uma busca detalhada em eventos de entrada específicos, como tentativas de senha inadequadas ou onde a conta foi desabilitada.

Para acessar o modelo de pasta de trabalho para o relatório de visão geral de segurança, conclua as seguintes etapas:

1. Procure e selecione **Azure Active Directory Domain Services** na portal do Azure.
1. Selecione seu domínio gerenciado, como *aaddscontoso.com*
1. No menu no lado esquerdo, escolha **monitoramento > pastas de trabalho**

    ![Captura de tela que hightlights onde selecionar o relatório de visão geral de segurança e o relatório de atividade da conta.](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Escolha o **relatório visão geral de segurança**.
1. Nos menus suspensos na parte superior da pasta de trabalho, selecione sua assinatura do Azure e, em seguida, um espaço de trabalho Azure Monitor.

    Escolha um **intervalo de tempo**, como os *últimos 7 dias*, conforme mostrado no seguinte exemplo de captura de tela:

    ![Selecione a opção de menu pastas de trabalho no portal do Azure](./media/use-azure-monitor-workbooks/select-query-filters.png)

    As opções **exibição de bloco** e exibição de **gráfico** também podem ser alteradas para analisar e visualizar os dados conforme desejado.

1. Para fazer uma busca detalhada em um tipo de evento específico, selecione um dos cartões de **resultados de entrada** , como *Conta bloqueada*, conforme mostrado no exemplo a seguir:

    ![Exemplo de dados de relatório de visão geral de segurança visualizado em pastas de trabalho Azure Monitor](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. A parte inferior do relatório de visão geral de segurança abaixo do gráfico, em seguida, divide o tipo de atividade selecionado. Você pode filtrar por nomes de logon envolvidos no lado direito, conforme mostrado no seguinte exemplo de relatório:

    [![Detalhes de bloqueios de conta em pastas de trabalho do Azure Monitor.](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png)](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>Usar a pasta de trabalho de relatório de atividade da conta

Para ajudá-lo a solucionar problemas de uma conta de usuário específica, o relatório de atividade da conta divide as informações detalhadas do log de eventos de auditoria. Você pode examinar quando um nome de usuário ou senha inválidos foi fornecido durante a entrada e a origem da tentativa de entrada.

Para acessar o modelo de pasta de trabalho para o relatório de atividade da conta, conclua as seguintes etapas:

1. Procure e selecione **Azure Active Directory Domain Services** na portal do Azure.
1. Selecione seu domínio gerenciado, como *aaddscontoso.com*
1. No menu no lado esquerdo, escolha **monitoramento > pastas de trabalho**
1. Escolha o **relatório atividade da conta**.
1. Nos menus suspensos na parte superior da pasta de trabalho, selecione sua assinatura do Azure e, em seguida, um espaço de trabalho Azure Monitor.

    Escolha um **intervalo de tempo**, como *últimos 30 dias*, como você deseja que o **modo de exibição de bloco** represente os dados.

    Você pode filtrar por **nome de usuário da conta**, como *Felix*, conforme mostrado no seguinte relatório de exemplo:

    [![Relatório de atividade da conta em pastas de trabalho do Azure Monitor.](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png)](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    A área abaixo do gráfico mostra eventos de entrada individuais juntamente com informações como o resultado da atividade e a estação de trabalho de origem. Essas informações podem ajudar a determinar fontes repetidas de eventos de entrada que podem causar bloqueios de conta ou indicar um ataque potencial.

Assim como no relatório de visão geral de segurança, você pode fazer uma busca detalhada nos blocos diferentes na parte superior do relatório para visualizar e analisar os dados conforme necessário.

## <a name="save-and-edit-workbooks"></a>Salvar e editar pastas de trabalho

As duas pastas de trabalho de modelo fornecidas pelo Azure AD DS são um bom local para começar com sua própria análise de dados. Se você precisar obter mais granular nas consultas e investigações de dados, poderá salvar suas próprias pastas de trabalho e editar as consultas.

1. Para salvar uma cópia de um dos modelos de pasta de trabalho, selecione **editar > salvar como > relatórios compartilhados** e, em seguida, forneça um nome e salve-o.
1. Em sua própria cópia do modelo, selecione **Editar** para entrar no modo de edição. Você pode escolher o botão de **edição** azul ao lado de qualquer parte do relatório e alterá-lo.

Todos os gráficos e tabelas em Azure Monitor pastas de trabalho são gerados usando consultas Kusto. Para obter mais informações sobre como criar suas próprias consultas, consulte Azure Monitor o [tutorial][kusto-queries] [consultas de log][azure-monitor-queries] e consultas do Kusto.

## <a name="next-steps"></a>Próximas etapas

Se você precisar ajustar políticas de bloqueio e senha, consulte [políticas de bloqueio de senha e conta em domínios gerenciados][password-policy].

Para problemas com os usuários, saiba como solucionar problemas de [conexão de conta][troubleshoot-sign-in] ou [problemas de bloqueio de conta][troubleshoot-account-lockout].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: /azure/data-explorer/kusto/query/
[kusto-queries]: /azure/kusto/query/tutorial?pivots=azuredataexplorer