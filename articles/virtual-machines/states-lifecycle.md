---
title: Ciclo de vida e Estados de uma VM no Azure
description: Visão geral do ciclo de vida de uma VM no Azure, incluindo descrições dos vários Estados em que uma VM pode estar em qualquer momento.
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: vashan
ms.openlocfilehash: 8c9641e722cf3892450bbfbea54f169ac6dc764b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963288"
---
# <a name="virtual-machines-lifecycle-and-states"></a>Ciclo de vida e estados de máquinas virtuais

As VMs (Máquinas Virtuais) do Azure passam por diferentes estados que podem ser categorizados entre os estados de *provisionamento* e *energia*. A finalidade deste artigo é descrever esses estados e realçar especificamente quando os clientes são cobrados pelo uso de instância. 

## <a name="power-states"></a>Estados de energia

O estado de energia representa o último estado conhecido da VM.

![Diagrama de estado de energia da VM](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
A tabela a seguir fornece uma descrição de cada estado da instância e indica se ele é cobrado pelo uso da instância ou não.

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="":::

   **Descrição**

   :::column-end:::
   :::column span="":::

   **Uso da instância cobrado**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Iniciando**

   :::column-end:::
   :::column span="":::

   A VM está iniciando.

   ```json
   "statuses": [
    {
    "code": "PowerState/starting",
    "level": "Info",
    "displayStatus": "VM starting"
    }
   ]
   ```
   :::column-end:::
   :::column span="":::

   **Não é cobrado**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Executando**

   :::column-end:::
   :::column span="":::

   Estado de funcionamento normal para uma VM

   ```json
   "statuses": [
    {
    "code": "PowerState/running",
    "level": "Info",
    "displayStatus": "VM running"
    }
  ]
  ```
   :::column-end:::
   :::column span="":::

   **Cobrado**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Parando**

   :::column-end:::
   :::column span="":::

   Esse é um estado de transição. Quando concluído, ele será exibido como **Parado**.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopping",
    "level": "Info",
    "displayStatus": "VM stopping"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Cobrado**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Parado**

   :::column-end:::
   :::column span="":::

   A VM foi desligada de dentro do sistema operacional convidado ou usando as APIs PowerOff.

   O hardware ainda estará alocado para a VM e permanecerá no host.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopped",
    "level": "Info",
    "displayStatus": "VM stopped"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Cobrado***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Desalocando**

   :::column-end:::
   :::column span="":::

   Estado de transição. Quando concluído, a VM será mostrada como **Desalocada**.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocating",
    "level": "Info",
    "displayStatus": "VM deallocating"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Não cobrado***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Desalocada**

   :::column-end:::
   :::column span="":::

   A VM foi parada com êxito e removida do host.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocated",
    "level": "Info",
    "displayStatus": "VM deallocated"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Não é cobrado**

   :::column-end:::
:::row-end:::


&#42; alguns recursos do Azure, como discos e rede, incorrem em encargos. Licenças de software na instância não geram encargos.

## <a name="provisioning-states"></a>Estados de provisionamento

Um estado de provisionamento é o status de uma operação iniciada pelo usuário de plano de controle na VM. Esses estados são separados do estado de energia de uma VM.

- **Criar**: criação da VM.

- **Atualização**: atualiza o modelo para uma VM existente. Algumas alterações que não são no modelo da VM, como Iniciar/Reiniciar também se enquadram na atualização.

- **Excluir**: exclusão de VM.

- **Desalocar**: é o ponto e que uma VM é interrompida e removida do host. A desalocação de uma VM é considerada uma atualização, portanto, ela exibirá estados de provisionamento relacionados à atualização.



Aqui estão os estados operação de transição depois que a plataforma aceitou uma ação iniciada pelo usuário:

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="2":::

   **Descrição**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Criando**

   :::column-end:::
   :::column span="2":::

  ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating",
    "level": "Info",
    "displayStatus": "Creating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Atualizar**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/updating",
    "level": "Info",
    "displayStatus": "Updating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Excluir**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/deleting",
    "level": "Info",
    "displayStatus": "Deleting"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Estados de provisionamento do sistema operacional**
   
   :::column-end:::
   :::column span="2":::

   **Descrição**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::



   :::column-end:::
   :::column span="2":::

   Se uma VM for criada com uma imagem do sistema operacional e não com uma imagem especializada, os seguintes subestados podem ser observados:

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningInprogress**

   :::column-end:::
   :::column span="2":::

   A VM está em execução e a instalação do sistema operacional convidado está em andamento.
 
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningInprogress",
    "level": "Info",
    "displayStatus": "OS Provisioning In progress"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningComplete**

   :::column-end:::
   :::column span="2":::
   
   Estado de curta duração. A VM faz a transição rapidamente para **Sucesso**, a menos que extensões precisem ser instalados. A instalação de extensões pode demorar.
   
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningComplete",
    "level": "Info",
    "displayStatus": "OS Provisioning Complete"
    }
   [
   ```
   
   **Observação**: o provisionamento do sistema operacional poderá fazer a transição para **Falha** se houver uma falha de sistema operacional ou o sistema operacional não for instalado no tempo. Os clientes serão cobrados pela VM implantada na infraestrutura.

   :::column-end:::

:::row-end:::

Depois que a operação for concluída, a VM fará a transição para um dos seguintes estados:

- **Êxito**: as ações iniciadas pelo usuário foram concluídas.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Falha**: representa uma operação com falha. Consulte os códigos de erro para obter mais informações e soluções possíveis.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Exibição de instância da VM

A API de exibição de instância fornece informações sobre o estado de execução da VM. Para obter mais informações, consulte a documentação da API [Máquinas virtuais – Exibição de instância](/rest/api/compute/virtualmachines/instanceview).

O Gerenciador de recursos do Azure fornece uma interface do usuário simple para exibir o estado de execução de VM: [Resource Explorer](https://resources.azure.com/).

Os estados de provisionamento são visíveis na exibição de instância e de propriedades da VM. Os estados de energia estão disponíveis na exibição de instância da VM.

Para recuperar o estado de energia de todas as VMs na sua assinatura, use a [API Máquinas Virtuais – Listar Todas](/rest/api/compute/virtualmachines/listall) com o parâmetro **statusOnly** definido como *true*.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como monitorar sua VM, consulte [monitorar máquinas virtuais no Azure](../azure-monitor/insights/monitor-vm-azure.md).