---
title: Exibir avaliações de atualização da Automação do Azure
description: Este artigo informa como exibir as avaliações de atualização para implantações de Gerenciamento de Atualizações.
services: automation
ms.subservice: update-management
ms.date: 09/17/2020
ms.topic: conceptual
ms.openlocfilehash: 39df5888a330a92ae043e34c3043da5b1f566345
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221917"
---
# <a name="view-update-assessments-in-update-management"></a>Exibir avaliações de atualização no Gerenciamento de Atualizações

No Gerenciamento de Atualizações, você pode exibir informações sobre seus computadores, atualizações ausentes, implantações de atualização e implantações de atualização agendada. Você pode exibir as informações de avaliação com escopo definido para a máquina virtual do Azure selecionada, do servidor habilitado para Arc selecionado ou da conta de automação em todos os computadores e servidores configurados.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com)

## <a name="view-update-assessment"></a>Exibir avaliação de atualização

Para exibir a avaliação de atualização de uma VM do Azure, navegue até **máquinas virtuais** e selecione sua máquina virtual na lista. No menu à esquerda, selecione **convidado + atualizações de host**e, em seguida, selecione **ir para gerenciamento de atualizações** na página de **atualizações convidado + host** .

No Gerenciamento de Atualizações, você pode exibir informações sobre sua máquina, atualizações ausentes, implantações de atualização e implantações de atualização agendada.

[![Exibição de avaliação de gerenciamento de atualizações para a VM do Azure](./media/view-update-assessments/update-assessment-azure-vm.png)](./media/view-update-assessments/update-assessment-azure-vm-expanded.png#lightbox)

Para exibir a avaliação de atualização de um servidor habilitado para Arc, navegue até **servidores – arco do Azure** e selecione o servidor na lista. No menu à esquerda, selecione **convidado e atualizações do host**. Na página **atualizações do host + convidado** , selecione **ir para gerenciamento de atualizações**.

No Gerenciamento de Atualizações, você pode exibir informações sobre sua máquina habilitada para Arc, atualizações ausentes, implantações de atualização e implantações de atualização agendada.

[![Exibição de avaliação de gerenciamento de atualizações para servidores habilitados para Arc](./media/view-update-assessments/update-assessment-arc-server.png)](./media/view-update-assessments/update-assessment-arc-server-expanded.png#lightbox)

Para exibir a avaliação de atualização em todos os computadores, incluindo servidores habilitados para Arc da sua conta de automação, navegue até **contas de automação** e selecione sua conta de automação com gerenciamento de atualizações habilitado na lista. Em sua conta de automação, selecione **Gerenciamento de atualizações** no menu à esquerda.

As atualizações para seu ambiente estão listadas na página **Gerenciamento de atualizações** . Se alguma atualização for identificada como ausente, uma lista delas será mostrada na guia **atualizações ausentes** .

[![Gerenciamento de atualizações exibição padrão](./media/overview/update-management-view.png)](./media/overview/update-management-view-expanded.png#lightbox)

Na coluna **conformidade** , você pode ver a última vez em que o computador foi avaliado. Na coluna **prontidão do agente de atualização** , você pode ver a integridade do agente de atualização. Se houver um problema, escolha o link para ir para a documentação de solução problemas que pode ajudar a corrigir isso.

Em **Link para informações**, selecione o link para uma atualização para abrir o artigo de suporte que fornece informações importantes sobre a atualização.

[![Exibir status da atualização](./media/view-update-assessments/missing-updates.png)](./media/view-update-assessments/missing-updates-expanded.png#lightbox)

Clique em qualquer lugar da atualização para abrir o painel da Pesquisa de logs. A consulta da pesquisa de log é predefinida para essa atualização específica. Você pode modificar essa consulta ou criar sua própria consulta para exibir informações detalhadas.

[![Exibir resultados da consulta de log](./media/view-update-assessments/logsearch-results.png)](./media/view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>Exibir atualizações ausentes

Selecione **Atualizações ausentes** para exibir a lista de atualizações que estão faltando nos computadores. Cada atualização é listada e pode ser selecionada. Informações sobre o número de computadores que exigem a atualização, detalhes do sistema operacional e um link para obter mais informações são mostrados. O painel Pesquisa de Logs mostra mais detalhes sobre as atualizações.

![Atualizações Ausentes](./media/view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>Trabalhar com classificações de atualização

As tabelas a seguir listam as classificações de atualização compatíveis no Gerenciamento de Atualizações, com uma definição de cada classificação.

### <a name="windows"></a>Windows

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Atualizações para problemas específicos que abordam bugs críticos não relacionados à segurança.        |
|Atualizações de segurança     | Atualizações para problemas específicos de produtos e relacionados à segurança.        |
|Pacotes cumulativos de atualização     | Conjuntos de hotfixes que são empacotados para facilitar a implantação.        |
|Feature packs     | Novos recursos do produto que são distribuídos fora de uma versão do produto.        |
|Service packs     | Conjuntos de hotfixes que são aplicados a um aplicativo.        |
|Atualizações de definição     | Atualizações para vírus ou outros arquivos de definição.        |
|Ferramentas     | Utilitários ou recursos que ajudam a concluir uma ou mais tarefas.        |
|Atualizações     | Atualizações de aplicativos ou arquivos que estão instalados no momento.        |

### <a name="linux"></a>Linux

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas ou de segurança     | Atualizações para um problema específico ou um problema relacionado à segurança específico do produto.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas nem de segurança.        |

Para o Linux, o Gerenciamento de Atualizações pode distinguir entre e atualizações críticas e de segurança na nuvem ao exibir dados de avaliação. (Essa granularidade é possível devido ao enriquecimento de dados na nuvem.) Para aplicação de patch, o Gerenciamento de Atualizações se baseia em dados de classificação disponíveis no computador. Ao contrário de outras distribuições, o CentOS não tem essas informações disponíveis nas versões RTM do produto. Se você tiver computadores CentOS configurados para retornar dados de segurança para o seguinte comando, o Gerenciamento de Atualizações poderá aplicar patch com base em classificações:

```bash
sudo yum -q --security check-update
```

Atualmente, não há nenhum método compatível para habilitar a disponibilidade de dados nativos de classificação em CentOS. Neste momento, somente o suporte de melhor esforço é fornecido a clientes que habilitaram essa funcionalidade por conta própria.

Para classificar atualizações no Red Hat Enterprise versão 6, é preciso instalar o plug-in yum-security. No Red Hat Enterprise Linux 7, o plug-in já faz parte do próprio yum, não é preciso instalar nada. Para obter mais informações, veja o [artigo de conhecimento](https://access.redhat.com/solutions/10021) da Red Hat a seguir.

## <a name="next-steps"></a>Próximas etapas

A próxima fase do processo é [implantar atualizações](deploy-updates.md) em computadores não compatíveis e examinar os resultados da implantação.
