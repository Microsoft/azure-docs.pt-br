---
title: Recuperação completa de desastres de máquinas virtuais
description: Este artigo mostra como concluir a recuperação de desastre de máquinas virtuais usando a solução VMware do Azure
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 688d91bc181e1479f5090a10af4b3b262d7ddb7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92779604"
---
# <a name="complete-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Concluir a recuperação de desastre de máquinas virtuais usando a solução VMware do Azure

Este artigo contém o processo para concluir a recuperação de desastre de suas VMs (máquinas virtuais) com a solução VMware HCX e o uso de uma nuvem privada da solução Azure VMware como o site de recuperação ou destino.

O VMware HCX fornece várias operações que fornecem uma granularidade e um controle fino nas políticas de replicação. As operações disponíveis incluem:

- **Inverter** – após a ocorrência de um desastre. Reverse ajuda a tornar o site B o site de origem e o site A, onde a VM protegida agora reside.

- **Pausar** – Pause a política de replicação atual associada à VM selecionada.

- **Retomar** -retome a política de replicação atual associada à VM selecionada.

- **Remover** -remove a política de replicação atual associada à VM selecionada.

- **Sincronizar agora** – fora da VM de origem da sincronização vinculada à VM protegida.

Este guia aborda os seguintes cenários de replicação:

- Proteger uma VM ou um grupo de VMs.

- Conclua uma recuperação de teste de uma VM ou de um grupo de VMs.

- Recuperar uma VM ou um grupo de VMs.

- Proteção reversa de uma VM ou de um grupo de VMs.

## <a name="protect-vms"></a>Proteger VMs

1. Faça logon no **vSphere Client** no site de origem e acesse o **plug-in HCX**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="Opção HCX em vSphere" border="true":::

1. Insira a área de **recuperação de desastre** e selecione **proteger VMs**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="Selecione proteger VMs" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. Selecione a origem e os sites remotos. O site remoto, nesse caso, deve ser a nuvem privada da solução Azure VMware.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="janela proteger VMs" border="true":::

1. Se necessário, selecione as opções de **replicação padrão** :

   - **Habilitar compactação:** Recomendado para cenários de baixa taxa de transferência.

   - **Habilitar quiescence:** Pausa a VM para garantir que uma cópia consistente seja sincronizada com o site remoto.

   - **Armazenamento de destino:** Repositório de armazenamento remoto para as VMs protegidas e em uma nuvem privada da solução Azure VMware, que deve ser o armazenamento de os vSAN.

   - **Contêiner de computação:** Cluster vSphere remoto ou pool de recursos.

   - **Pasta de destino:** A pasta de destino remoto, que é opcional e, se nenhuma pasta for selecionada, as VMs serão colocadas diretamente no cluster selecionado.

   - **RPO:** Intervalo de sincronização entre a VM de origem e a VM protegida. Pode ser de 5 minutos a 24 horas.

   - **Intervalo de instantâneos:** Intervalo entre instantâneos.

   - **Número de instantâneos:** Número total de instantâneos dentro do intervalo de instantâneos configurado.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="opções de proteção de VMs" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. Selecione uma ou mais VMs na lista e configure as opções de replicação conforme necessário.

   Por padrão, as VMs herdam a política de configurações globais configurada nas opções de replicação padrão. Para cada interface de rede na VM selecionada, configure o **grupo de portas de rede** remota e selecione **concluir** para iniciar o processo de proteção.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="opções de interface de rede" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. Monitore o processo para cada uma das VMs selecionadas na mesma área de recuperação de desastre.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="monitorar o progresso da proteção" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. Depois que a VM tiver sido protegida, você poderá exibir os instantâneos diferentes na guia **instantâneos** .

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="lista de instantâneos" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   O triângulo amarelo significa que os instantâneos e as máquinas virtuais não foram testados em uma operação de recuperação de teste.

   Há diferenças importantes entre uma VM desligada e uma ligada. A imagem mostra o processo de sincronização para uma VM ligada. Ele inicia o processo de sincronização até que ele termine o primeiro instantâneo, que é uma cópia completa da VM e, em seguida, conclua as próximas no intervalo configurado. Ele sincroniza uma cópia para uma VM desligada e, em seguida, a VM aparece como inativa e a operação de proteção é mostrada como concluída.  Quando a VM é ligada, ela inicia o processo de sincronização para o site remoto.

## <a name="complete-a-test-recover-of-vms"></a>Concluir uma recuperação de teste de VMs

1. Faça logon no **vSphere Client** no site remoto, que é a nuvem privada da solução Azure VMware. 
1. No **plug-in HCX**, na área recuperação de desastres, selecione as reticências verticais em qualquer VM para exibir o menu operações e, em seguida, selecione **testar recuperar VM**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="Selecione testar recuperação de VM" border="true":::

1. Selecione as opções para o teste e o instantâneo que você deseja usar para testar Estados diferentes da VM.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="Escolha um instantâneo e selecione teste" border="true":::

1. Depois de selecionar **Test**, a operação de recuperação é iniciada.

1. Quando terminar, você poderá verificar a nova VM na nuvem privada da solução Azure VMware vCenter.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="verificar a operação de recuperação" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. Após a realização do teste na VM ou em qualquer aplicativo em execução, faça uma limpeza para excluir a instância de teste.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="instância de teste de limpeza" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>Recuperar VMs

1. Faça logon no **vSphere Client** no site remoto, que é a nuvem privada da solução Azure VMware e acesse o **plug-in HCX**.

   Para o cenário de recuperação, um grupo de VMs usado para este exemplo.

1. Selecione a VM a ser recuperada na lista, abra o menu **ações** e selecione **recuperar VMs**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="recuperar VMs" border="true":::

1. Configure as opções de recuperação para cada instância e selecione **recuperar** para iniciar a operação de recuperação.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="confirmação de recuperação de VMs" border="true":::

1. Depois que a operação de recuperação for concluída, as novas VMs aparecerão no inventário de vCenter Server remoto.

## <a name="complete-a-reverse-replication-on-vms"></a>Concluir uma replicação inversa em VMs

1. Faça logon no **vSphere Client** em sua nuvem privada da solução Azure VMware e acesse o **plug-in HCX**.
   
   >[!NOTE]
   > Verifique se as VMs originais no site de origem estão desligadas antes de iniciar a replicação inversa. A operação falhará se as VMs não estiverem desligadas.

1. Na lista, selecione as VMs a serem replicadas de volta para o site de origem, abra o menu **ações** e selecione **inverter**. 
1. Selecione **Reverse** para iniciar a replicação.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="Selecionar ação inversa em proteger operações" border="true":::

1. Monitore na seção de detalhes de cada VM.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="examinar os resultados da ação inversa" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>Automação do plano de recuperação de desastre

Atualmente, o VMware HCX não tem um mecanismo interno para criar e automatizar um plano de recuperação de desastres. No entanto, o VMware HCX fornece um conjunto de APIs REST, incluindo APIs para a operação de recuperação de desastre. A especificação de API pode ser acessada no VMware HCX Manager na URL.

Essas APIs abrangem as seguintes operações na recuperação de desastres.

- Proteger

- Recuperar

- Recuperação de teste

- Recuperação planejada

- Reverse

- Consulta

- Limpeza de teste

- Pausar

- Retomar

- Remover Proteção

- Reconfigurar

Um exemplo de uma carga de operação de recuperação em JSON é mostrado abaixo.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Com essas APIs, você pode criar um mecanismo personalizado para automatizar a criação e a execução de um plano de recuperação de desastre.
