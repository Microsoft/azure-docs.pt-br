---
title: Saiba como integrar as soluções de gerenciamento de atualizações, controle de alterações e inventário para várias VMs na automação do Azure
description: Saiba como integrar uma Máquina Virtual do Azure com soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário que fazem parte da Automação do Azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/11/2019
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: df59342bebae3ac0f6e80e5b58f429fedf3c3336
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738959"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Habilitar o gerenciamento de atualizações, o controle de alterações e as soluções de inventário em várias VMs

A Automação do Azure fornece soluções para gerenciar atualizações de segurança do sistema operacional, controlar alterações e inventariar o que está instalado em seus computadores. Há várias maneiras para integrar máquinas, você pode integrar a solução [de uma máquina virtual](automation-onboard-solutions-from-vm.md), da sua [conta de automação](automation-onboard-solutions-from-automation-account.md), durante a navegação de máquinas virtuais ou pelo [runbook](automation-onboard-solutions.md). Este artigo aborda essas soluções de integração ao procurar máquinas virtuais no Azure.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Fazer logon no Azure em https://portal.azure.com

## <a name="enable-solutions"></a>Habilitar soluções

No portal do Azure, navegue até **máquinas virtuais**.

Usando as caixas de seleção, selecione as máquinas virtuais que você deseja integrar com o controle de alterações e inventário ou de gerenciamento de atualização. A integração está disponível para até três grupos de recursos diferente cada vez. As VMs do Azure podem existir em qualquer região não importa o local de sua conta de automação.

![Lista de VMs](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Use os controles de filtro para modificar a lista de máquinas virtuais e, em seguida, clique na caixa de seleção superior para selecionar todas as máquinas virtuais na lista.

Na barra de comandos, clique em **serviços** e selecione **controle de alterações**, **inventário**, ou **Update Management**.

> [!NOTE]
> **Controle de alterações** e **Inventário** usam a mesma solução, quando um está habilitado, o outro também está habilitado.

A imagem a seguir é para o gerenciamento de atualizações. O controle de alterações e inventário têm o mesmo layout e comportamento.

A lista de máquinas virtuais é filtrada para mostrar somente as máquinas virtuais que estão na mesma assinatura e local. Se suas máquinas virtuais estiverem em mais de três grupos de recursos, os três primeiros grupos de recursos serão selecionados.

### <a name="resource-group-limit"></a> Limitações de integração

O número de grupos de recursos que você pode usar para a integração é limitado pelos [limites de implantação do Resource Manager](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md). As implantações do Resource Manager, que não devem ser confundidas com implantações de atualização, estão limitadas a cinco grupos de recursos por implantação. Para garantir a integridade da integração, dois desses grupos de recursos são reservados para configurar o espaço de trabalho do Log Analytics, a conta de Automação e os recursos relacionados. Isso deixa você com três grupos de recursos para selecionar para a implantação.

Use os controles de filtro para selecionar máquinas virtuais em assinaturas diferentes, locais e grupos de recursos.

![Solução integrada de gerenciamento de atualização](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Revise as opções para o espaço de trabalho do Log Analytics e a conta de automação. Um workspace existente e a conta de Automação são selecionados por padrão. Se desejar usar um espaço de trabalho do Log Analytics diferente e a Conta de Automação, clique em **PERSONALIZAR** para selecioná-los na página **Configuração personalizada**. Quando você escolhe um espaço de trabalho do Log Analytics, é feita uma verificação para determinar se ele está vinculado a uma Conta de Automação. Se uma Conta de Automação vinculada for encontrada, você verá a tela a seguir. Quando terminar, clique em **OK**.

![Selecione workspace e conta](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Se o workspace selecionado não estiver vinculado a uma Conta de Automação, você verá a tela a seguir. Selecione uma Conta de Automação e clique em **OK** ao concluir.

![Nenhum workspace](media/automation-onboard-solutions-from-browse/no-workspace.png)

Ao habilitar soluções, somente determinadas regiões têm suporte para vincular um espaço de trabalho do Log Analytics e uma Conta de Automação.

A tabela abaixo mostra os mapeamentos com suporte:

|**Região do Workspace do Log Analytics**|**Região da Automação do Azure**|
|---|---|
|AustraliaSoutheast|AustraliaSoutheast|
|Canadá Central|Canadá Central|
|CentralIndia|CentralIndia|
|EastUS<sup>1</sup>|EastUS2|
|JapanEast|JapanEast|
|SoutheastAsia|SoutheastAsia|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|WestEurope|WestEurope|
|UKSouth|UKSouth|
|USGovVirginia|USGovVirginia|
|EastUS2EUAP<sup>1</sup>|CentroEUAEUAP|

<sup>1</sup> mapeamentos EastUS2EUAP e EastUS para espaços de trabalho do Log Analytics para contas de automação não são um mapeamento de região para região exato, mas é o mapeamento correto.

<sup>2</sup> devido a restrições de capacidade a região não está disponível durante a criação de novos recursos. Isso inclui contas de automação e o Log Analytics para espaços de trabalho. No entanto, os recursos vinculados pré-existentes na região devem continuam a funcionar.

Desmarque a caixa de seleção ao lado de qualquer máquina virtual que você não deseja habilitar. Máquinas virtuais que não podem ser habilitadas já estão desmarcadas.

Clique em **Ativar** para ativar a solução. A solução demora até 15 minutos para habilitar.

## <a name="unlink-workspace"></a>Desvincular o workspace

As seguintes soluções são dependentes de um espaço de trabalho do Log Analytics:

* [Gerenciamento de atualizações](automation-update-management.md)
* [Controle de alterações](automation-change-tracking.md)
* [Iniciar/parar VMs durante os horários fora de pico](automation-solution-vm-management.md)

Se você decidir que você não deseja mais integrar sua conta de automação com um espaço de trabalho do Log Analytics, você pode desvincular sua conta diretamente do portal do Azure. Antes de prosseguir, você precisa remover as soluções mencionadas anteriormente, caso contrário, esse processo será impedido de continuar. Examine o artigo sobre a solução específica que você importou para entender as etapas necessárias para removê-la.

Depois de remover essas soluções, você poderá executar as etapas a seguir para desvincular sua conta de Automação.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitoramento do Azure SQL, podem ter criado ativos de automação e também podem precisar ser removidas antes de desvincular o workspace.

1. No portal do Azure, abra sua conta da Automação e, na página da conta da Automação, selecione **Workspace vinculado** na seção **Recursos Relacionados** à esquerda.

2. Na página Desvincular o workspace, clique em **Desvincular o workspace**.

   ![Página Desvincular workspace](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Você receberá uma solicitação perguntando se deseja prosseguir.

3. Enquanto a Automação do Azure tenta desvincular a conta do seu espaço de trabalho do Log Analytics, você pode acompanhar o progresso no menu **Notificações**.

Se você tiver usado a solução Gerenciamento de Atualizações, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Atualizar agendas - Cada uma terá nomes que correspondam às implantações de atualizações que você criou)

* Grupos de trabalho híbrido criados para a solução - Cada um receberá um nome semelhante a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Se você tiver usado a solução Iniciar/parar VMs durante os horários fora de pico, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Iniciar e parar agendas de runbook da VM
* Iniciar e parar runbooks da VM
* Variáveis

## <a name="troubleshooting"></a>Solução de problemas

Ao integrar várias máquinas, pode haver máquinas que mostrem como **Não é possível ativar**. Há diferentes razões que algumas máquinas não podem ser ativadas. As seções a seguir mostram possíveis razões para o estado **Impossível ativar** em uma VM ao tentar integrar.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Relatórios de VM para outro workspace: '\<workspaceName\>'.  Alterar a configuração para usá-lo para ativar

**Causa**: este erro mostra a máquina virtual em que você está tentando carregar relatórios para outro espaço de trabalho.

**Solução**: Clique em **Usar como configuração**  para alterar o espaço de trabalho da conta de automação e do Log Analytics.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Relatórios VM para um workspace que não está disponível nesta assinatura

**Causa**: o espaço de trabalho ao qual a máquina virtual se reporta:

* Está em uma assinatura diferente ou
* Não existe mais, ou
* Está em um grupo de recursos que você não tem as permissões de acesso

**Solução**: localizar a conta de automação associada ao espaço de trabalho ao qual a máquina virtual se reporta e integrar a máquina virtual ao alterar a configuração do escopo.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>A versão ou distribuição do sistema operacional VM não é suportada

**Causa:** a solução não é suportada em todas as distribuições do Linux ou em todas as versões do Windows.

**Solução:** consulte a [lista de clientes compatíveis](automation-update-management.md#clients) para obter a solução.

### <a name="classic-vms-cannot-be-enabled"></a>VMs clássicas não podem ser habilitadas

**Causa**: máquinas virtuais que usam o modelo de implantação clássico não são suportadas.

**Solução**: migre a máquina virtual para o modelo de implantação do gerenciador de recursos. Para aprender como fazer isso, consulte [ Migrar recursos do modelo de implantação clássico ](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>A VM está parada. (deslocada)

**Causa**: a máquina virtual não está no estado **Em Execução**.

**Solução**: para integrar uma VM a uma solução, a VM deve estar em execução. Clique no link embutido **Iniciar VM** para iniciar a VM sem sair da página.

## <a name="next-steps"></a>Próximas etapas

Agora que a solução está habilitada para suas máquinas virtuais, visite o artigo Visão Geral do Gerenciamento de Atualizações para saber como visualizar a avaliação de atualizações de suas máquinas.

> [!div class="nextstepaction"]
> [Gerenciamento de atualização - avaliação de atualização do modo de exibição](./automation-update-management.md#viewing-update-assessments)

Tutoriais de adição sobre as soluções e como usá-los:

* [Tutorial - Gerenciar as atualizações de sua VM](automation-tutorial-update-management.md)

* [Tutorial - Identificar o software em uma VM](automation-tutorial-installed-software.md)

* [Tutorial - Solucionar problemas de alterações em uma VM](automation-tutorial-troubleshoot-changes.md)
