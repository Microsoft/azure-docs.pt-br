---
title: Como criar implantações de atualização para a automação do Azure Gerenciamento de Atualizações
description: Este artigo descreve como agendar implantações de atualização e revisar seu status.
services: automation
ms.subservice: update-management
ms.date: 03/19/2021
ms.topic: conceptual
ms.openlocfilehash: 6d35d6b49ab72d8aa7b25506011147ab624273fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669671"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Como implantar atualizações e examinar os resultados

Este artigo descreve como agendar uma implantação de atualização e examinar o processo após a conclusão da implantação. Você pode configurar uma implantação de atualização de uma máquina virtual do Azure selecionada, do servidor habilitado para Arc selecionado ou da conta de automação em todos os computadores e servidores configurados.

Em cada cenário, a implantação que você cria tem como destino o computador ou servidor selecionado, ou no caso de criação de uma implantação da sua conta de automação, você pode direcionar um ou mais computadores. Quando você agenda uma implantação de atualização de uma VM do Azure ou de um servidor habilitado para Arc, as etapas são as mesmas para a implantação de sua conta de automação, com as seguintes exceções:

* O sistema operacional é previamente selecionado automaticamente com base no sistema operacional do computador
* O computador de destino para atualizar está definido para o próprio destino automaticamente
* Ao configurar o agendamento, você pode especificar **Atualizar agora**, ocorre uma vez ou usa um agendamento recorrente.

> [!IMPORTANT]
> Ao criar uma implantação de atualização, você aceita os termos dos termos de licença de software (EULA) fornecidos pelas atualizações de oferta da empresa para seu sistema operacional.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Agendar uma implantação de atualização cria um recurso de [agendamento](../shared-resources/schedules.md) vinculado ao runbook **patch-MicrosoftOMSComputers** que manipula a implantação de atualização no computador ou máquinas de destino. Você precisa agendar uma implantação que siga o agendamento de versão e o período de serviço para instalar as atualizações. Você pode escolher os tipos de atualização que deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualização.

>[!NOTE]
>Se você excluir o recurso de agenda no portal do Azure ou usando o PowerShell depois de criar a implantação, a exclusão interromperá a implantação de atualização agendada e apresentará um erro quando você tentar reconfigurar o recurso de agenda no portal. Você só pode excluir o recurso de agendamento excluindo o agendamento de implantação correspondente.  

Para agendar uma nova implantação de atualização, execute as etapas a seguir. Dependendo do recurso selecionado (ou seja, conta de automação, servidor habilitado para Arc, VM do Azure), as etapas a seguir se aplicam a todos com pequenas diferenças ao configurar a agenda de implantação.

1. No portal, para agendar uma implantação para o:

   * Um ou mais computadores, navegue até **contas de automação** e selecione sua conta de automação com gerenciamento de atualizações habilitado na lista.
   * Para uma VM do Azure, navegue até **máquinas virtuais** e selecione sua VM na lista.
   * Para um servidor habilitado para Arc, navegue até **servidores-Azure Arc** e selecione o servidor na lista.

2. Dependendo do recurso selecionado, navegue até Gerenciamento de Atualizações:

   * Se você tiver selecionado sua conta de automação, vá para **Gerenciamento de atualizações** em **Gerenciamento de atualizações** e selecione **agendar implantação de atualização**.
   * Se você selecionou uma VM do Azure, vá para **convidado + atualizações do host** e, em seguida, selecione **ir para gerenciamento de atualizações**.
   * Se você selecionou um servidor habilitado para Arc, vá para **Gerenciamento de atualizações** e, em seguida, selecione **agendar implantação de atualização**.

3. Em **nova implantação de atualização**, no campo **nome** , insira um nome exclusivo para sua implantação.

4. Selecione o sistema operacional de destino para a implantação de atualização.

    > [!NOTE]
    > Essa opção não estará disponível se você tiver selecionado uma VM do Azure ou um servidor habilitado para Arc. O sistema operacional é identificado automaticamente.

5. Na região **grupos a serem atualizados** , defina uma consulta que combine assinatura, grupos de recursos, locais e marcas para criar um grupo dinâmico de VMs do Azure a serem incluídas na sua implantação. Para saber mais, confira [Usar grupos dinâmicos com o Gerenciamento de Atualizações](configure-groups.md).

    > [!NOTE]
    > Essa opção não estará disponível se você tiver selecionado uma VM do Azure ou um servidor habilitado para Arc. O computador é direcionado automaticamente para a implantação agendada.

6. Na região **Computadores para atualizar**, selecione uma pesquisa salva, um grupo importado ou selecione **Computadores** no menu suspenso e selecione computadores individuais. Com essa opção, você pode ver a prontidão do agente do Log Analytics para cada computador. Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos logs do Azure Monitor, confira [Grupos de computadores nos logs do Azure Monitor](../../azure-monitor/logs/computer-groups.md). Você pode incluir até um máximo de 1000 computadores em uma implantação de atualização agendada.

    > [!NOTE]
    > Essa opção não estará disponível se você tiver selecionado uma VM do Azure ou um servidor habilitado para Arc. O computador é direcionado automaticamente para a implantação agendada.

7. Use a região **Classificações de atualização** para especificar [classificações de atualização](view-update-assessments.md#work-with-update-classifications) para os produtos. para cada produto, desmarque todas as classificações de atualização com suporte, exceto as que serão incluídas na implantação da atualização.

    Se sua implantação for destinada a aplicar apenas um conjunto selecionado de atualizações, será necessário desmarcar todas as classificações de atualização pré-selecionadas ao configurar a opção **incluir/excluir atualizações** , conforme descrito na próxima etapa. Isso garante que somente as atualizações que você especificou para *incluir* nessa implantação sejam instaladas nos computadores de destino.

8. Use a região **incluir/excluir atualizações** para adicionar ou excluir as atualizações selecionadas da implantação. Na página **incluir/excluir** , insira os números de ID do artigo da base de conhecimento a serem incluídos ou excluídos para atualizações do Windows. Para distribuições do Linux com suporte, você especifica o nome do pacote.

   > [!IMPORTANT]
   > Lembre-se de que as exclusões substituem as inclusões. Por exemplo, se você definir uma regra de exclusão de `*`, o Gerenciamento de Atualizações excluirá todos os patches ou pacotes da instalação. Correções excluídas ainda são mostradas como ausentes dos computadores. Para computadores Linux, se você incluir um pacote que tem um pacote dependente que foi excluído, o Gerenciamento de Atualizações não instalará o pacote principal.

   > [!NOTE]
   > Não é possível especificar atualizações que foram substituídas para inclusão na implantação de atualização.

9. Selecione **Configurações da agenda**. A hora de início padrão é 30 minutos após a hora atual. Você pode definir a hora de início para qualquer momento a partir de 10 minutos.

    > [!NOTE]
    > Essa opção será diferente se você selecionou um servidor habilitado para Arc. Você pode selecionar **Atualizar agora** ou uma hora de início 20 minutos no futuro.

10. Use a **recorrência** para especificar se a implantação ocorre uma vez ou usa um agendamento recorrente e, em seguida, selecione **OK**.

11. Na região **pré-scripts + pós-scripts** , selecione os scripts a serem executados antes e depois da implantação. Para saber mais, confira [Gerenciar pré-scripts e pós-scripts](pre-post-scripts.md).

12. Use o campo **Janelas de manutenção (minutos)** para especificar o tempo permitido para a instalação das atualizações. Considere os seguintes detalhes ao especificar uma janela de manutenção:

    * As janelas de manutenção controlam o número de atualizações que são instaladas.
    * O Gerenciamento de Atualizações não interromperá a instalação de novas atualizações se o fim de uma janela de manutenção estiver se aproximando.
    * O Gerenciamento de Atualizações não encerrará as atualizações em andamento se a janela de manutenção for excedida. As atualizações restantes a serem instaladas não serão tentadas. Se isso estiver ocorrendo de forma consistente, reavalie a duração da janela de manutenção.
    * Se a janela de manutenção é excedida no Windows, isso geralmente ocorre devido a uma longa instalação de atualização do service pack.

    > [!NOTE]
    > Para evitar atualizações aplicadas fora da janela de manutenção no Ubuntu, reconfigure o pacote `Unattended-Upgrade` para desabilitar as atualizações automáticas. Para saber mais sobre como configurar o pacote, veja o [tópico Atualizações automáticas no Guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

13. Use o campo **Opções de reinicialização** para especificar como lidar com reinicializações durante a implantação. As seguintes opções estão disponíveis: 
    * Reinicializar se necessário (padrão)
    * Sempre reinicializar
    * Nunca reinicializar
    * Somente reinicialização; esta opção não instala atualizações

    > [!NOTE]
    > As chaves do Registro listadas em [Chaves do Registro usadas para gerenciar a reinicialização](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) poderão causar um evento de reinicialização se a opção **Opções de reinicialização** estiver definida como **Nunca reinicializar**.

14. Quando você terminar de configurar a agenda de implantação, selecione **criar**.

    ![Painel Configurações de agendamento de atualizações](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > Quando você terminar de configurar a agenda de implantação para um servidor habilitado para Arc selecionado, selecione **examinar + criar**.

15. Você é retornado ao painel de status. Selecione **agendamentos de implantação** para mostrar a agenda de implantação que você criou. São listados no máximo 500 agendamentos. Se você tiver mais de 500 agendas e desejar revisar a lista completa, consulte o método de API de [configuração de atualização de software – lista de configurações de atualizações](/rest/api/automation/softwareupdateconfigurations/list) . Especifique a versão de API 2019-06-01 ou superior.

## <a name="schedule-an-update-deployment-programmatically"></a>Agendar uma implantação de atualização programaticamente

Para saber como criar uma implantação de atualizações com a API REST, confira [Configurações de atualização de software – Criar](/rest/api/automation/softwareupdateconfigurations/create).

Você pode usar um runbook de exemplo para criar uma implantação de atualizações semanal. Para saber mais sobre este runbook, consulte [Criar uma implantação de atualização semanal para uma ou mais VMs em um grupo de recursos](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group).

## <a name="check-deployment-status"></a>Verifique o status da implantação

Depois que a implantação agendada for iniciada, você poderá ver seu status na guia **histórico** em **Gerenciamento de atualizações**. O status é **Em andamento** se a implantação está em execução no momento. Quando a implantação for concluída com êxito, o status será alterado para **Êxito**. Se houver falhas com uma ou mais atualizações na implantação, o status será **falha**.

## <a name="view-results-of-a-completed-update-deployment"></a>Exibir resultados de uma implantação de atualização concluída

Quando a implantação for concluída, você poderá selecioná-la para ver seus resultados.

[![Atualizar o painel de status de implantação para uma implantação específica](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

Em **Resultados da atualização** há um resumo do número total de atualizações e resultados de implantação nas VMs de destino. A tabela à direita mostra uma análise detalhada de cada atualização e os resultados da instalação de cada uma delas.

Os valores disponíveis são:

* **Não foi tentada**: a atualização não foi instalada devido a tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Não selecionado**: a atualização não foi selecionada para implantação.
* **Êxito**: a atualização foi bem-sucedida.
* **Falha**: falha na atualização.

Selecione **Todos os logs** para ver todas as entradas de log que a implantação criou.

Selecione **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização nas VMs de destino.

Selecione **Erros** para ver informações detalhadas sobre quaisquer erros da implantação.

## <a name="next-steps"></a>Próximas etapas

* Para saber como criar alertas para notificá-lo sobre os resultados da implantação de atualização, consulte [criar alertas para gerenciamento de atualizações](configure-alerts.md).
* Para solucionar problemas gerais do Gerenciamento de Atualizações, confira [Solucionar problemas do Gerenciamento de Atualizações](../troubleshoot/update-management.md).
