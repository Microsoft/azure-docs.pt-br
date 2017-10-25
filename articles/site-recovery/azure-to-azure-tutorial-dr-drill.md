---
title: "Fazer uma simulação de recuperação de desastre para VMs do Azure para uma região do Azure secundária com o Azure Site Recovery (versão prévia)"
description: "Saiba como fazer uma simulação de recuperação de desastre para VMs do Azure para uma região do Azure secundária usando o serviço Azure Site Recovery."
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: a3b453028b7fd32bd3ed22823a337f7a978d9aa7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region-preview"></a>Realizar uma simulação de recuperação de desastre para VMs do Azure para uma região do Azure secundária (versão prévia)

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) por manter seus aplicativos de negócios em execução e disponíveis durante interrupções planejadas e não planejadas. O Site Recovery gerencia e orquestra a recuperação de desastre de máquinas locais e de VMs (máquinas virtuais) do Azure, incluindo replicação, failover e recuperação.

Este tutorial mostra a você como realizar uma simulação de recuperação de desastre para uma VM do Azure, de uma região para outra, com um failover de teste. Uma simulação valida sua estratégia de replicação sem perda de dados ou tempo de inatividade e não afeta seu ambiente de produção. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Executar um failover de teste para uma VM

## <a name="prerequisites"></a>Pré-requisitos

- Antes de executar um failover de teste, é recomendável verificar as propriedades da VM para certificar-se de que tudo está conforme o esperado.  Acesse as propriedades da VM em **Itens replicados**. A folha **Conceitos básicos** mostra as informações sobre as configurações e o status dos computadores.
- É recomendável utilizar uma rede de VM do Azure separada para o failover de teste e não a rede padrão que foi configurada quando você habilitou a replicação.


## <a name="run-a-test-failover"></a>Execute um teste de failover

1. Em **Configurações** > **Itens Replicados**, clique no ícone da VM **+ Failover de Teste**.

2. Em **Failover de Teste**, selecione um ponto de recuperação para usar no failover:

   - **Processado mais recente**: falha na VM para o ponto de recuperação único que foi processado pelo serviço do Site Recovery. A carimbo de data/hora é mostrado. Com essa opção, nenhum tempo é gasto em processamento de dados, portanto, ela fornece um RTO (Objetivo do Tempo de Recuperação) baixo
   - **Consistente com o aplicativo mais recente**: essa opção falha em todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente. A carimbo de data/hora é mostrado.
   - **Personalizar**: selecione qualquer ponto de recuperação.

3. Selecione a rede virtual de destino do Azure para qual as VMs do Azure na região secundária serão conectadas, após o failover ocorrer.

4. Para iniciar o failover, clique em **Ok**. Para acompanhar o progresso, clique na VM para abrir suas propriedades. Ou você pode clicar no trabalho **Failover de Teste** no nome do cofre > **Configurações** > **Trabalhos** > **Trabalhos do Site Recovery**.
5. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Verifique se a VM está em execução, tem o tamanho apropriado e está conectada à rede apropriada.
6. Para excluir as VMs que foram criadas durante o failover de teste, clique em **Failover de teste de limpeza** no item replicado ou no plano de recuperação. Em **Observações**, registre e salve todas as observações associadas ao failover de teste.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Executar um failover de produção](azure-to-azure-tutorial-failover-failback.md)
