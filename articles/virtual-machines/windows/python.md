---
title: Criar e gerenciar uma VM Windows no Azure usando Python | Microsoft Docs
description: Aprenda a usar o Python para criar e gerenciar uma VM Windows no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: cynthn
ms.openlocfilehash: 748bc08e003d398e96ef55493e4f3b0bf6b7da28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034749"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Criar e gerenciar VMs Windows no Azure usando Python

Uma [VM (Máquina Virtual) do Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) precisa de vários recursos do Azure suporte. Este artigo aborda a criação, o gerenciamento e a exclusão de recursos da VM usando Python. Você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto do Visual Studio
> * Instalar Pacotes
> * Criar credenciais
> * Criar recursos
> * Executar outras tarefas de gerenciamento
> * Excluir recursos
> * Executar o aplicativo

São necessários cerca de 20 minutos para a conclusão destas etapas.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Se você ainda não fez isso, instale o [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selecione **Desenvolvimento do Python** na página Cargas de Trabalho e, em seguida, clique **Instalar**. No resumo, você pode ver que **Python 3 64 bits (3.6.0)** está selecionado automaticamente para você. Se você já tiver instalado o Visual Studio, poderá adicionar a carga de trabalho do Python usando o Inicializador do Visual Studio.
2. Depois de instalar e iniciar o Visual Studio, clique em **Arquivo** > **Novo** > **Projeto**.
3. Clique em **Modelos** > **Python** > **Aplicativo do Python**, insira *myPythonProject* para o nome do projeto, selecione a localização do projeto e clique em **OK**.

## <a name="install-packages"></a>Instalar Pacotes

1. No Gerenciador de Soluções, em *myPythonProject*, clique com botão direito do mouse em **Ambientes do Python** e selecione **Adicionar Ambiente Virtual**.
2. Na tela Adicionar Ambiente Virtual, aceite o nome padrão do *env*, certifique-se de que *Python 3.6 (64 bits)* esteja selecionado para o interpretador de base e, em seguida, clique em **Criar**.
3. Clique com o botão direito do mouse no ambiente *env* que você criou, clique em **Instalar Pacote do Python**, digite *azure* na caixa de pesquisa e pressione Enter.

Você deve ver nas janelas de saída que os pacotes do Azure foram instalados com êxito. 

## <a name="create-credentials"></a>Criar credenciais

Antes de começar essa etapa, verifique se você tem uma [entidade de serviço do Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Você também deve registrar a ID do aplicativo, a chave de autenticação e a ID de locatário que precisará em uma etapa posterior.

1. Abra o arquivo *myPythonProject.py* que foi criado e, em seguida, adicione este código para habilitar o aplicativo para execução:

    ```python
    if __name__ == "__main__":
    ```

2. Para importar o código que é necessário, adicione estas instruções à parte superior do arquivo .py:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Em seguida no arquivo .py, adicione variáveis após as instruções de importação para especificar valores comuns usados no código:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Substitua **subscription-id** pelo identificador da assinatura.

4. Para criar as credenciais do Active Directory que você precisa para fazer solicitações, adicione essa função após as variáveis no arquivo .py:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Substitua **application-id**, **authentication-key** e **tenant-id** pelos valores coletados anteriormente ao criar a entidade de serviço do Azure Active Directory.

5. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Criar recursos
 
### <a name="initialize-management-clients"></a>Inicializar clientes de gerenciamento

Os clientes de gerenciamento são necessários para criar e gerenciar recursos usando o SDK do Python no Azure. Para criar os clientes de gerenciamento, adicione esse código sob a instrução **if** no final do arquivo .py:

```python
resource_group_client = ResourceManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>Criar a VM e recursos de suporte

Todos os recursos devem estar contidos em um [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md).

1. Para criar um grupo de recursos, adicione essa função após as variáveis no arquivo .py:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

Os [conjuntos de disponibilidade](tutorial-availability-sets.md) facilitam a manutenção das máquinas virtuais usadas por seu aplicativo.

1. Para criar um conjunto de disponibilidade, adicione essa função após as variáveis no arquivo .py:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

Um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) é necessário para se comunicar com a máquina virtual.

1. Para criar um endereço IP público para a máquina virtual, adicione essa função após as variáveis no arquivo .py:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Uma máquina virtual precisa estar em uma sub-rede de uma [Rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Para criar uma rede virtual, adicione essa função após as variáveis no arquivo .py:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Para adicionar uma sub-rede à rede virtual, adicione essa função após as variáveis no arquivo .py:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Uma máquina virtual precisa de uma interface de rede para se comunicar na rede virtual.

1. Para criar um adaptador de rede, adicione essa função após as variáveis no arquivo .py:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Agora que você criou todos os recursos de suporte, você pode criar uma máquina virtual.

1. Para criar a máquina virtual, adicione essa função após as variáveis no arquivo .py:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > Este tutorial cria uma máquina virtual executando uma versão do sistema operacional do Windows Server. Para saber mais sobre a seleção de outras imagens, veja [Navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Executar outras tarefas de gerenciamento

Durante o ciclo de vida de uma máquina virtual, é possível que você queira executar tarefas de gerenciamento, como inicialização, interrupção ou exclusão de uma máquina virtual. Além disso, é possível que você queira criar um código para automatizar tarefas complexas ou repetitivas.

### <a name="get-information-about-the-vm"></a>Obter informações sobre a VM

1. Para obter informações sobre a máquina virtual, adicione essa função após as variáveis no arquivo .py:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Pare a VM.

Você pode parar uma máquina virtual e manter todas as suas configurações, mas continuará a ser cobrado por ela, ou pode parar uma máquina virtual e desalocá-la. Quando uma máquina virtual é desalocada, todos os recursos associados a ela também são desalocadas e a cobrança para eles termina.

1. Para parar a máquina virtual sem desalocá-la, adicione essa função após as variáveis no arquivo .py:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Se você desejar desalocar a máquina virtual, altere a chamada power_off para este código:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Iniciar a VM

1. Para iniciar a máquina virtual, adicione essa função após as variáveis no arquivo .py:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Redimensionar a VM

Muitos aspectos da implantação devem ser considerados ao decidir sobre um tamanho para sua máquina virtual. Para obter mais informações, consulte [Tamanhos de VM](sizes.md).

1. Para alterar o tamanho da máquina virtual, adicione essa função após as variáveis no arquivo .py:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Adicionar um disco de dados à VM

Máquinas virtuais podem ter um ou mais [discos de dados](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) que são armazenados como VHDs.

1. Para adicionar um disco de dados à máquina virtual, adicione essa função após as variáveis no arquivo .py: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Excluir recursos

Como você é cobrado pelos recursos usados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Se você quiser excluir as máquinas virtuais e todos os recursos de suporte, tudo o que precisa fazer é excluir o grupo de recursos.

1. Para excluir o grupo de recursos e todos os recursos, adicione essa função após as variáveis no arquivo .py:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Para chamar a função que você adicionou anteriormente, adicione este código sob a instrução **if** no final do arquivo .py:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Salve *myPythonProject.py*.

## <a name="run-the-application"></a>Executar o aplicativo

1. Para executar o aplicativo de console, clique em **Iniciar** no Visual Studio.

2. Pressione **Enter** depois que o status de cada recurso for retornado. Nas informações de status, você deverá ver um estado de provisionamento **Bem-sucedido**. Após a criação da máquina virtual será possível excluir todos os recursos criados. Antes de pressionar **Enter** para iniciar a exclusão dos recursos, você terá alguns minutos para verificar a criação no portal do Azure. Se o portal do Azure for aberto, talvez seja necessário atualizar a folha para verificar novos recursos.  

    Devem ser necessários cerca de cinco minutos para o aplicativo de console executar completamente do início ao fim. Pode levar alguns minutos depois que o aplicativo foi concluído antes de todos os recursos e o grupo de recursos serem excluídos.


## <a name="next-steps"></a>Próximas etapas

- Se houver problemas com a implantação, uma próxima etapa será examinar [Solucionando problemas de implantações do grupo de recursos com o portal do Azure](../../resource-manager-troubleshoot-deployments-portal.md)
- Saiba mais sobre a [Biblioteca do Python do Azure](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)

