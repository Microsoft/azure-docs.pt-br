---
title: Mover as VMs do Azure para outra região com o Azure Site Recovery
description: Use o Azure Site Recovery para mover as VMs IaaS do Azure de uma região do Azure para outra.
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 5ae930240872c00c8dbb45857e4e77d82766eadf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93398055"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Mover máquinas virtuais do Azure para outra região do Azure

Talvez você queira mover a infraestrutura do Azure como uma máquina virtual de serviço (IaaS) de uma região para outra para melhorar a confiabilidade, disponibilidade, gerenciamento ou governança. Este tutorial mostra como mover as VMs para outra região usando o Azure Site Recovery. Você aprenderá a:

> [!div class="checklist"]
> * Verificar pré-requisitos
> * Preparar as VMs de origem
> * Preparar a região de destino
> * Copiar os dados para a região de destino
> * Testar a configuração
> * Realizar a movimentação
> * Descartar os recursos da região de origem


> [!IMPORTANT]
> Este tutorial descreve como mover as VMs do Azure de uma região para outra *no estado em que estão*. Se seu objetivo é melhorar a disponibilidade de sua infraestrutura, movendo VMs para as zonas de disponibilidade, consulte [Mover VMs do Azure para as zonas de disponibilidade](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Pré-requisitos

- Verifique se você tem as VMs do Azure na região de origem do Azure *da qual* deseja realizar a movimentação.
- Verifique se [há suporte para sua escolha de combinação de região de origem – região de destino](./azure-to-azure-support-matrix.md#region-support) e tome uma decisão informada sobre a região de destino.
- Verifique se você entende os [componentes e a arquitetura do cenário](azure-to-azure-architecture.md).
- Examine os [requisitos e limitações com suporte](azure-to-azure-support-matrix.md).
- Verificar permissões da conta. Se acabou de criar sua conta gratuita do Azure, *você* é o administrador da assinatura. Se você não for o administrador da assinatura, trabalhe com o administrador para obter as permissões necessárias:
  -  Para habilitar a replicação para uma VM e copiar os dados para o destino usando o Site Recovery, você deve ter permissões para criar uma VM em seus recursos do Azure. A função interna Colaborador da Máquina Virtual tem essas permissões. Com as permissões, você pode:
        - Criar uma VM no grupo de recursos selecionado.
        - Criar uma VM na rede virtual selecionada.
        - Gravar na conta de armazenamento selecionada.

  - Você também precisa de permissão para gerenciar as operações do Site Recovery. A função Colaborador do Site Recovery tem todas as permissões necessárias para gerenciar operações do Site Recovery em um cofre dos Azure Site Recovery.

## <a name="prepare-the-source-vms"></a>Preparar as VMs de origem

1. Verifique se as VMs do Azure que você planeja mover tem os certificados raiz mais recentes. Caso contrário, não será possível habilitar a cópia de dados para a região de destino devido a restrições de segurança.

    - Para VMs Windows, instale todas as atualizações do Windows mais recentes para que todos os certificados raiz confiáveis estejam no computador. Em um ambiente desconectado, siga os processos padrão do Windows Update e de atualização de certificado para sua organização.
    - Para VMs Linux, siga as diretrizes fornecidas pelo seu distribuidor Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogado.
2. Certifique-se de que você não está usando um proxy de autenticação para controlar a conectividade de rede das VMs que você quer mover.
3. Se a VM que você está tentando mover não tiver acesso à Internet ou estiver usando um proxy de firewall para controlar o acesso de saída, verifique os [requisitos](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).
4. Documente o layout da rede de origem e todos os recursos que estão sendo usados no momento – incluindo, entre outros, balanceadores de carga, NSGs, endereços IP público para a verificação.

## <a name="prepare-the-target-region"></a>Preparar a região de destino

1. Em sua assinatura do Azure, verifique se é possível criar VMs na região de destino utilizada para recuperação de desastre. Contate o suporte para habilitar a cota necessária.

2. Verifique se a sua assinatura tem recursos suficientes para dar suporte a suas VMs de origem. Se você estiver usando o Site Recovery para copiar os dados para o destino, ele escolherá o mesmo tamanho ou o tamanho mais próximo possível para as VMs de destino.

3. Certifique-se de criar um recurso de destino para cada componente identificado no layout de rede de origem. Isso garante que as VMs tenham toda a funcionalidade e os recursos na região de destino que você tinha na região de origem.

   Quando você habilita a replicação para a VM de origem, o Azure Site Recovery automaticamente descobre e cria uma conta de armazenamento e rede virtual. Você também pode pré-criar esses recursos e atribuí-los à VM como parte da etapa de habilitação da replicação. Mas será necessário criar manualmente outros recursos na região de destino. Consulte os documentos a seguir para criar os recursos de rede mais comumente usados que são relevantes para você, com base na configuração da VM de origem:

   - [Grupos de segurança de rede](../virtual-network/manage-network-security-group.md)
   - [Balanceadores de carga](../load-balancer/index.yml)
   - [IP público](../virtual-network/virtual-network-public-ip-address.md)
    
   Para quaisquer outros componentes de rede, consulte a [documentação da rede Azure](../index.yml?pivot=products&panel=network). 

4. [Crie manualmente uma rede de não produção](../virtual-network/quick-create-portal.md) na região de destino, caso você queira testar a configuração antes de executar a movimentação final. Testar a configuração criará interferência mínima com o ambiente de produção e é recomendado.
    
## <a name="copy-data-to-the-target-region"></a>Copiar os dados para a região de destino
As etapas a seguir usam o Azure Site Recovery para copiar dados para a região de destino.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Crie o cofre em qualquer região, exceto a região de origem

1. Entre no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Selecione **Criar um recurso** > **Ferramentas de Gerenciamento** > **Backup e Site Recovery**.
3. Em **Nome**, especifique o nome amigável **ContosoVMVault**. Se você tiver mais de uma assinatura, selecione uma delas.
4. Crie um grupo de recursos **ContosoRG**.
5. Especifique uma região do Azure. Para verificar as regiões com suporte, consulte os [Detalhes do Preço do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Nos cofres dos Serviços de Recuperação, clique em **Visão Geral** > **ConsotoVMVault** >  **+Replicar**.
7. Em **Origem**, selecione **Azure**.
8. Em **Local de origem**, selecione a fonte de região do Azure em que suas VMs estão sendo executadas.
9. Selecione o modelo de implantação do Azure Resource Manager. Em seguida, selecione a **Assinatura de origem** e o **Grupo de recursos de origem**.
10. Selecione **OK** para salvar as configurações.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Habilitar replicação para VMs do Azure e iniciar cópia dos dados

O Site Recovery recupera uma lista das VMs associadas à assinatura e ao grupo de recursos.

1. Selecione a VM que você quer mover e selecione **OK**.
2. Em **Configurações**, selecione **Recuperação de desastre**.
3. Em **Configurar a recuperação de desastre** > **Região de destino**, selecione a região de destino para a qual você está replicando.
4. Optar por usar os recursos de destino padrão ou aqueles que você tenha criado previamente.
5. Selecione **habilitar a replicação** para iniciar o trabalho.


 

## <a name="test-the-configuration"></a>Testar a configuração


1. Vá para o cofre. Em **Configurações** > **Itens replicados**, selecione a VM que você quer mover para a região de destino. Em seguida, selecione **Failover de Teste**.
2. Em **Failover de Teste**, selecione um ponto de uso para o failover:

   - **Mais recente processado**: Faz failover da VM para o ponto de recuperação único que foi processado pelo serviço do Site Recovery. A carimbo de data/hora é mostrado. Nenhum tempo é gasto no processamento de dados, portanto, ele fornece um RTO (Objetivo do Tempo de Recuperação) baixo.
   - **Consistente com o aplicativo mais recente**: Fazer failover de todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente. A carimbo de data/hora é mostrado.
   - **Personalizar**: selecione qualquer ponto de recuperação.

3. Selecione a rede virtual de destino do Azure para a qual deseja mover as VMs do Azure para testar a configuração.

   > [!IMPORTANT]
   > Recomendamos que você use uma rede de VMs do Azure separada para a falha de teste, não a rede de produção na região de destino.

4. Para começar a testar a movimentação, selecione **OK**. Para acompanhar o progresso, selecione a VM para abrir suas **Propriedades.** Ou selecione o trabalho **Failover de teste** no cofre. Em seguida, selecione **as configurações** > **trabalhos** > **trabalhos de recuperação de Site**.
5. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Verifique se a VM está em execução, tem o tamanho apropriado e está conectada à rede apropriada.
6. Para excluir a VM que você criou para teste, selecione **Limpar failover de teste** no item replicado. De **Anotações**, registre e salve todas as observações relacionadas ao teste.

## <a name="perform-the-move-and-confirm"></a>Realizar a movimentação e confirmar

1. Vá até o cofre, em **Configurações** > **Itens replicados**, selecione a máquina virtual e, em seguida, selecione **Failover**.
1. Em **Failover**, selecione **Mais recente**. 
2. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tenta desligar a VM de origem antes de acionar o failover. O failover continuará, mesmo se o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
3. Depois que o trabalho for concluído, verifique se a VM é exibida na região de destino do Azure conforme esperado.
4. Em **Itens replicados**, clique com o botão direito do mouse em VM e selecione **Confirmar**. Isso conclui a mudança. Aguarde até que o trabalho de confirmação seja concluído.

## <a name="discard-the-resources-from-the-source-region"></a>Descartar os recursos da região de origem

- Vá até a VM e selecione **Desabilitar replicação**. Isso interromperá o processo de cópia dos dados para a VM.

  > [!IMPORTANT]
  > Execute esta etapa para evitar ser cobrado por replicação do Site Recovery após a movimentação.

Se você não tiver planos de reutilizar os recursos de origem, siga estas etapas:

1. Exclua todos os recursos de rede relevantes na região de origem listada na etapa 4 em [Preparar as VMs de origem](#prepare-the-source-vms).
2. Exclua a conta de armazenamento correspondente da região de origem.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a mover VMs do Azure para uma região diferente dele. Agora é possível configurar a recuperação de desastres para essas VMs.

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastres após a migração](azure-to-azure-quickstart.md)
