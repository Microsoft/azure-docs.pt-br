---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 57f557a812ec5e4eea75b76ca1394ca360a85d30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419957"
---
As VMs (Máquinas Virtuais) do Azure passam por diferentes estados que podem ser categorizados entre os estados de *provisionamento* e *energia*. A finalidade deste artigo é descrever esses estados e realçar especificamente quando os clientes são cobrados pelo uso de instância. 

## <a name="power-states"></a>Estados de energia

O estado de energia representa o último estado conhecido da VM.

![Diagrama de estado de energia da VM](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
A tabela a seguir fornece uma descrição de cada estado da instância e indica se ele é cobrado pelo uso da instância ou não.

<table>
<tr>
<th>
Estado
</th>
<th>
DESCRIÇÃO
</th>
<th>
Cobrança de uso de instância
</th>
</tr>
<tr>
<td>
<p><b>Iniciando</b></p>
</td>
<td>
<p>A VM está iniciando.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Não é cobrado</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Executando</b></p>
</td>
<td>
<p>Estado de funcionamento normal para uma VM</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Cobrado</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Parando</b></p>
</td>
<td>
<p>Esse é um estado de transição. Quando concluído, ele será exibido como **Parado**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Cobrado</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Interrompido</b></p>
</td>
<td>
<p>A VM foi desligada de dentro do sistema operacional convidado ou usando as APIs PowerOff.</p>
<p>O hardware ainda estará alocado para a VM e permanecerá no host. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Cobrado&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Desalocando</b></p>
</td>
<td>
<p>Estado de transição. Quando concluído, a VM será mostrada como **Desalocada**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Não é cobrado&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Desalocada</b></p>
</td>
<td>
<p>A VM foi parada com êxito e removida do host. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Não é cobrado</b></p>
</td>
</tr>
</tbody>
</table>


&#42;Alguns recursos do Azure, como Discos e Redes, geram encargos. Licenças de software na instância não geram encargos.

## <a name="provisioning-states"></a>Estados de provisionamento

Um estado de provisionamento é o status de uma operação iniciada pelo usuário de plano de controle na VM. Esses estados são separados do estado de energia de uma VM.

- **Criar**: criação da VM.

- **Atualização**: atualiza o modelo para uma VM existente. Algumas alterações que não são no modelo da VM, como Iniciar/Reiniciar também se enquadram na atualização.

- **Excluir**: exclusão de VM.

- **Desalocar**: é o ponto e que uma VM é interrompida e removida do host. A desalocação de uma VM é considerada uma atualização, portanto, ela exibirá estados de provisionamento relacionados à atualização.



Aqui estão os estados operação de transição depois que a plataforma aceitou uma ação iniciada pelo usuário:

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>Estados</b></p>
</td>
<td width="366">
<p>DESCRIÇÃO</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>Criando</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Atualizando</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Excluindo</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Estados de provisionamento do sistema operacional</b></p>
</td>
<td width="366">
<p>Se uma VM for criada com uma imagem do sistema operacional e não com uma imagem especializada, os seguintes subestados podem ser observados:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; A VM está em execução e a instalação do sistema operacional convidado está em andamento. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; Estado de curta duração. A VM faz a transição rapidamente para **Sucesso**, a menos que extensões precisem ser instalados. A instalação de extensões pode demorar. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Observação</b>: o provisionamento do sistema operacional poderá fazer a transição para **Falha** se houver uma falha de sistema operacional ou o sistema operacional não for instalado no tempo. Os clientes serão cobrados pela VM implantada na infraestrutura.</p>
</td>
</tr>
</table>


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

A API de exibição de instância fornece informações sobre o estado de execução da VM. Para obter mais informações, consulte a documentação da API [Máquinas virtuais – Exibição de instância](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview).

O Gerenciador de recursos do Azure fornece uma interface do usuário simples para exibir o estado de execução de VM: [Resource Explorer](https://resources.azure.com/).

Os estados de provisionamento são visíveis na exibição de instância e de propriedades da VM. Os estados de energia estão disponíveis na exibição de instância da VM. 

