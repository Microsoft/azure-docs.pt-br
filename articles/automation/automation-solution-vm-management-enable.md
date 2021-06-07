---
title: Habilitar Automação do Azure Iniciar/Parar VMs fora do horário comercial
description: Este artigo informa como habilitar o recurso Iniciar/Parar VMs fora do horário comercial para suas VMs do Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 36f885416c5e9cb656d01a65b9c503f8897d2f9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593892"
---
# <a name="enable-startstop-vms-during-off-hours"></a>Habilitar Iniciar/Parar VMs fora do horário comercial

Execute as etapas neste tópico em sequência para habilitar o recurso Iniciar/Parar VMs fora do horário comercial para VMs usando uma conta de automação nova ou existente e o espaço de trabalho vinculado do Log Analytics. Depois de concluir o processo de instalação, configure as variáveis para personalizar o recurso.

>[!NOTE]
>Para usar esse recurso com VMs clássicas, você precisa de uma conta Executar como clássica, que não é criada por padrão. Veja [Criar uma Conta Executar como clássica](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="create-resources-for-the-feature"></a>Criar recursos para o recurso

1. Entre no [Portal](https://portal.azure.com) do Azure.
2. Pesquise e escolha **Contas de Automação**.
3. Na página Contas de Automação, escolha sua conta de Automação na lista.
4. Em uma Conta de Automação, escolha **Iniciar/Parar VM** em **Recursos Relacionados**. A partir daqui, você pode clicar em **Saiba mais sobre e habilite a solução**. Se você já tiver o recurso implantado, poderá clicar em **Gerenciar a solução** e encontrá-la na lista.

   ![Habilitar a conta de automação](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Você também pode criar o recurso em qualquer lugar no portal do Azure, clicando em **Criar um recurso**. Na página Marketplace, digite uma palavra-chave, como **Iniciar** ou **Iniciar/Parar**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Como alternativa, você pode digitar uma ou mais palavras-chave do nome completo do recurso e, em seguida, pressionar **Enter**. Selecione **Iniciar/Parar VMs fora do horário** nos resultados da pesquisa.

5. Na página Iniciar/Parar VMs fora do horário comercial da implantação escolhida, examine as informações de resumo e clique em **Criar**.

   ![Portal do Azure](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>Configurar o recurso

Com o recurso criado, a página Adicionar Solução é exibida. Você será solicitado a configurar o recurso antes de importá-lo na sua assinatura da Automação. Veja [Configurar Iniciar/Parar VMs fora do horário comercial](automation-solution-vm-management-config.md).

   ![Página Adicionar Solução de Gerenciamento de VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>Escolha um workspace do Log Analytics

1. Na página Adicionar Solução, escolha **Workspace**. Escolha um espaço de trabalho do Log Analytics que esteja vinculado à assinatura do Azure usada pela conta de automação. 

2. Se você não tiver um workspace, selecione **Criar Novo Workspace**. Na página Espaço de trabalho do Log Analytics, execute as seguintes etapas:

   - Especifique um nome para o novo espaço de trabalho do Log Analytics, como **ContosoLAWorkspace**.
   - Escolha uma **Assinatura** à qual se vincular, escolhendo na lista suspensa, caso a assinatura selecionada por padrão não seja adequada.
   - Em **Grupo de Recursos**, você pode criar um novo grupo de recursos ou selecionar um existente.
   - Selecione um **Local**.
   - Selecione um **tipo de preço**. Escolha a opção **Por GB (autônomo)** . Os logs do Azure Monitor atualizaram o [preço](https://azure.microsoft.com/pricing/details/log-analytics/), e o nível Por GB é a única opção.

   > [!NOTE]
   > Ao habilitar recursos, somente determinadas regiões permitem vincular um espaço de trabalho do Log Analytics e uma conta de Automação. Para obter uma lista dos pares de mapeamento compatíveis, consulte [Mapeamento de região para conta da Automação e workspace do Log Analytics](how-to/region-mappings.md).

3. Depois de fornecer as informações necessárias na página Espaço de trabalho do Log Analytics, clique em **Criar**. Você pode acompanhar o progresso em **Notificações** no menu, que retornará a página Adicionar Solução ao terminar.

## <a name="add-automation-account"></a>Adicionar conta de Automação

Acesse a página Adicionar Solução novamente e escolha **Conta de automação**. É possível escolher uma conta de automação atual que ainda não esteja vinculada a um espaço de trabalho do Log Analytics. Se você estiver criando um novo espaço de trabalho do Log Analytics, poderá criar uma nova conta de automação para associar a ela. Escolha uma conta de automação existente ou clique em **Criar uma conta de automação** e, na página Adicionar automação da conta, forneça o nome da conta de Automação no campo **Nome**.

Todas as outras opções são preenchidas automaticamente com base no espaço de trabalho do Log Analytics selecionado. Você não pode modificar essas opções. Uma conta Executar como do Azure é o método de autenticação padrão para os runbooks incluídos neste recurso. 

Depois de clicar em **OK**, as opções de configuração serão validadas e a conta de Automação será criada. Você pode acompanhar o progresso em **Notificações** no menu.

## <a name="define-feature-parameters"></a>Definir parâmetros de funcionalidades

1. Na página Adicionar Solução, escolha **Configuração**. A página Parâmetros é exibida.

    ![Página de parâmetros para a solução](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. Especifique um valor para o campo **Nomes do ResourceGroup de destino**. O campo define os nomes de grupo que contêm VMs para o recurso a ser gerenciado. É possível inserir mais de um nome e separar os nomes usando vírgulas (os valores não diferenciam maiúsculas de minúsculas). O uso de um caractere curinga tem suporte para selecionar VMs em todos os grupos de recursos na assinatura. Os valores são armazenados nas variáveis `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames`.

    > [!IMPORTANT]
    > O valor padrão para **Nomes do ResourceGroup de destino** é um **&ast;** . Essa configuração direciona todas as VMs em uma assinatura. Se não quiser que o recurso direcione todas as VMs em sua assinatura, você deverá fornecer uma lista de nomes de grupos de recursos antes de escolher um agendamento.
  
3. Especifique um valor para o campo **Lista de exclusão da VM (cadeia de caracteres)** . Este valor é o nome de uma ou mais máquinas virtuais do grupo de recursos de destino. É possível inserir mais de um nome e separar os nomes usando vírgulas (os valores não diferenciam maiúsculas de minúsculas). O uso de caracteres curingas é aceito. Esse valor de código de retorno é armazenado na variável `External_ExcludeVMNames`.
  
4. Use o campo **Agendar** para escolher um agendamento para o gerenciamento de VM pelo recurso. Escolha uma data e hora de início para seu agendamento para criar um agendamento diário recorrente a partir da hora escolhida. A seleção de uma região diferente não está disponível. Para configurar o agendamento de acordo com seu fuso horário específico após a configuração do recurso, confira [Modificar o agendamento de inicialização e desligamento](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).

5. Para receber notificações por email de um [grupo de ações](../azure-monitor/alerts/action-groups.md), aceite o valor padrão **Sim** no campo **Notificações por email** e insira um endereço de email válido. Se você escolher **Não**, mas decidir mais tarde que deseja receber notificações por email, atualize o grupo de ações que foi criado, com endereços de email válidos separados por vírgula. 

6. Habilite as seguintes regras de alerta:

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>Criar alertas

Iniciar/Parar VMs fora do horário comercial não inclui um conjunto predefinido de alertas. Examine [Criar alertas de log com Azure Monitor](../azure-monitor/alerts/alerts-log.md) para saber como criar alertas de falha de trabalho para permitir processos e procedimentos operacionais ou DevOps.

## <a name="deploy-the-feature"></a>Implantar o recurso

1. Depois de definir as configurações iniciais necessárias para o recurso, clique em **OK** para fechar a página Parâmetros.

2. Clique em **Criar**. Depois que todas as configurações são validadas, o recurso é implantado em sua assinatura. Esse processo pode levar vários segundos para ser finalizado e você pode acompanhar o progresso em **Notificações** no menu.

    > [!NOTE]
    > Se você tiver uma assinatura do Provedor de Soluções de Nuvem do Azure (CSP do Azure), após a implantação ser concluída, em sua conta de automação, vá para **Variáveis** em **Recursos Compartilhados** e defina a variável [External_EnableClassicVMs](automation-solution-vm-management.md#variables) como **False**. Isso faz com que a solução pare de procurar recursos de VM clássica.

## <a name="next-steps"></a>Próximas etapas

* Para configurar o recurso, veja [Configurar Iniciar/Parar VMs fora do horário comercial](automation-solution-vm-management-config.md).
* Para resolver erros de recurso, veja [Solucionar problemas de Iniciar/Parar VMs fora do horário comercial](troubleshoot/start-stop-vm.md).