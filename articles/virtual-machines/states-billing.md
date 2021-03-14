---
title: Estados e cobrança de máquinas virtuais do Azure
description: Visão geral de vários Estados que uma VM pode inserir e quando um usuário é cobrado.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: 0325dcf16c8e637a58365311a4ebd37a442d6b8c
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522407"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>Estados e cobrança de máquinas virtuais do Azure

As VMs (Máquinas Virtuais) do Azure passam por diferentes estados que podem ser categorizados entre os estados de *provisionamento* e *energia*. A finalidade deste artigo é descrever esses estados e realçar especificamente quando os clientes são cobrados pelo uso de instância. 

## <a name="get-states-using-instance-view"></a>Obter Estados usando a exibição de instância

A API de exibição de instância fornece informações sobre o estado de execução da VM. Para obter mais informações, consulte a documentação da API [Máquinas virtuais – Exibição de instância](/rest/api/compute/virtualmachines/instanceview).

O Gerenciador de recursos do Azure fornece uma interface do usuário simple para exibir o estado de execução de VM: [Resource Explorer](https://resources.azure.com/).

Os estados de provisionamento são visíveis na exibição de instância e de propriedades da VM. Os estados de energia estão disponíveis na exibição de instância da VM.

Para recuperar o estado de energia de todas as VMs na sua assinatura, use a [API Máquinas Virtuais – Listar Todas](/rest/api/compute/virtualmachines/listall) com o parâmetro **statusOnly** definido como *true*.

> [!NOTE]
> [Máquinas virtuais-listar todas as APIs](/rest/api/compute/virtualmachines/listall) com o parâmetro **statusOnly** definido como true recuperará os Estados de energia de todas as VMs em uma assinatura. No entanto, em algumas situações raras, o estado de energia pode não estar disponível devido a problemas intermitentes no processo de recuperação. Em tais situações, é recomendável tentar novamente usando a mesma API ou usando [Azure Resource Health](../service-health/resource-health-overview.md) ou o [grafo de recursos do Azure](..//governance/resource-graph/overview.md) para verificar o estado de energia de suas VMs.
 
## <a name="power-states-and-billing"></a>Estados de energia e cobrança

O estado de energia representa o último estado conhecido da VM.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="Imagem mostra o diagrama dos Estados de energia que uma VM pode passar. ":::

A tabela a seguir fornece uma descrição de cada estado da instância e indica se ele é cobrado pelo uso da instância ou não.

| Estado de energia | Descrição | Cobrança |  
|---|---|---|
| Iniciando| A máquina virtual está ligando. |Não cobrado * | 
| Executando | A máquina virtual está totalmente ativa. Esse é o estado de trabalho padrão. | Cobrado | 
| Parando | Esse é um estado de transição entre a execução e a parada. | Cobrado| 
|Parado | A máquina virtual foi desligada de dentro do SO convidado ou usando APIs estado desligado. Nesse estado, a máquina virtual ainda está concedendo o hardware subjacente. Esse estado também é conhecido como *parado (alocado)*. | Cobrado | 
| Desalocando | Esse é o estado de transição entre a execução e a desalocada. | Não cobrado * | 
| Desalocada | A máquina virtual liberou a concessão no hardware subjacente e está completamente desligada. Esse estado também é conhecido como *parado (desalocado)*. | Não cobrado * | 

&#42; alguns recursos do Azure, como [discos](https://azure.microsoft.com/pricing/details/managed-disks) e [rede](https://azure.microsoft.com/pricing/details/bandwidth/) , continuarão a incorrer em encargos.


## <a name="provisioning-states"></a>Estados de provisionamento

Um estado de provisionamento é o status de uma operação iniciada pelo usuário de plano de controle na VM. Esses estados são separados do estado de energia de uma VM.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="Imagem mostra os Estados de provisionamento pelos quais uma VM pode passar.":::

| Estado de provisionamento | Descrição | Estado de energia | Cobrança | 
|---|---|---|---|
| Criar | Criação de máquina virtual. | Iniciando | Não cobrado * | 
| Atualizar | Atualiza o modelo para uma máquina virtual existente. Algumas alterações não modeladas em uma máquina virtual, como iniciar e reiniciar, se enquadram no estado de atualização. | Executando | Cobrado | 
| Excluir | Exclusão da máquina virtual. | Desalocando | Não cobrado * |
| Desalocar | A máquina virtual está totalmente parada e removida do host subjacente. A desalocação de uma máquina virtual é considerada uma atualização e exibirá Estados de provisionamento semelhantes à atualização. | Desalocando | Não cobrado * | 

&#42; alguns recursos do Azure, como [discos](https://azure.microsoft.com/pricing/details/managed-disks) e [rede](https://azure.microsoft.com/pricing/details/bandwidth/) , continuarão a incorrer em encargos.

## <a name="os-provisioning-states"></a>Estados de provisionamento do so
OS Estados de provisionamento do so se aplicam somente a máquinas virtuais criadas com uma imagem do sistema operacional. Imagens especializadas não exibirão esses Estados. 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="Imagem mostra os Estados de provisionamento do sistema operacional em que uma VM pode passar.":::

| Estado de provisionamento do so | Descrição | Estado de energia | Cobrança | 
|---|---|---|---|
| OSProvisioningInProgress | A VM está em execução e a instalação do sistema operacional convidado está em andamento. | Executando | Cobrado | 
| OSProvisioningComplete | Esse é um estado de curta duração. A máquina virtual faz a transição rapidamente desse Estado para o **sucesso**. Se as extensões ainda estiverem sendo instaladas, você continuará a ver esse Estado até que elas sejam concluídas. | Executando | Cobrado | 
| Com sucesso | As ações iniciadas pelo usuário foram concluídas. | Executando | Cobrado | 
| Com falha | Representa uma operação com falha. Consulte o código de erro para obter mais informações e possíveis soluções. | Executando  | Cobrado | 


## <a name="next-steps"></a>Próximas etapas
- Examine a [documentação de cobrança e gerenciamento de custos do Azure](https://docs.microsoft.com/azure/cost-management-billing/)
- Use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para planejar suas implantações.
- Saiba mais sobre como monitorar sua VM, consulte [monitorar máquinas virtuais no Azure](../azure-monitor/insights/monitor-vm-azure.md).