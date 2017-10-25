---
title: Usar o Terraform para criar uma VM do Linux completa no Azure | Microsoft Docs
description: "Saber como usar o Terraform para criar e gerenciar um ambiente completo de máquina virtual do Linux no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: timlt
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2017
ms.author: echuvyrov
ms.openlocfilehash: a4a418a3b277d41b62aa049941a4c65e3bb82808
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Criar uma infraestrutura completa de máquina virtual do Linux no Azure usando o Terraform
O Terraform permite definir e criar implantações de infraestrutura completa no Azure. Você cria modelos do Terraform em um formato legível que criar e configurar os recursos do Azure de maneira consistente e reproduzível. Este artigo mostra como criar um ambiente Linux completo e os recursos de apoio com o Terraform. Você também pode aprender como [instalar e configurar o Terraform](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Criar conexão e grupo de recursos do Azure
Vamos percorrer cada seção de um modelo do Terraform. Você também poderá ver a versão completa do [modelo do Terraform](#complete-terraform-script) que você pode copiar e colar.

A seção `provider` informa o Terraform para usar um provedor do Azure. Para obter os valores de *subscription_id*, *client_id*, *client_secret* e *tenant_id*, veja [Instalar e configurar o Terraform](terraform-install-configure.md). Se você criar variáveis de ambiente para os valores, não inclua esta seção.

```tf
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```tf
resource "azurerm_resource_group" "myResourceGroup" {
    name     = "myResourceGroup"
    location = "East US"

    tags {
        environment = "Terraform Demo"
    }
}
```

Nas seções adicionais, você faz referência ao grupo de recursos com *${azurerm_resource_group.myterraform.name}*.


## <a name="create-virtual-network"></a>Criar rede virtual
A seção a seguir em cria uma rede virtual chamada *myVnet* no espaço de endereço *10.0.0.0/16*:

```tf
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "East US"
    resource_group_name = "${azurerm_resource_group.myterraform.name}"

    tags {
        environment = "Terraform Demo"
    }
}
```

A seção a seguir cria uma sub-rede denominada *mySubnet* na rede virtual *myVnet*

```tf
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Criar um endereço IP público
Para acessar os recursos na Internet, crie e atribua um endereço IP público para a sua VM. A seção a seguir cria um endereço IP público denominado *myPublicIP*:

```tf
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "East US"
    resource_group_name          = "${azurerm_resource_group.myterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Criar um grupo de segurança de rede
Os grupos de segurança de rede controlam o fluxo de entrada e saída de tráfego de rede de sua VM. A seção a seguir cria um grupo de segurança de rede denominado *myNetworkSecurityGroup* e define uma regra para permitir o tráfego de SSH na porta TCP 22:

```tf
resource "azurerm_network_security_group" "temyterraformpublicipnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "East US"
    resource_group_name = "${azurerm_resource_group.myterraform.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Criar placa de adaptador de rede virtual
Uma placa de adaptador de rede virtual (NIC) conecta-se à VM para uma determinada rede virtual, um endereço IP público e um grupo de segurança de rede. A seção a seguir em um guia estratégico do Ansible cria uma NIC virtual denominada *myNIC* conectada aos recursos da rede virtual que você criou:

```tf
resource "azurerm_network_interface" "myterraformnic" {
    name                = "myNIC"
    location            = "East US"
    resource_group_name = "${azurerm_resource_group.myterraform.name}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Criar conta de armazenamento para diagnóstico
Para armazenar o diagnóstico de inicialização para uma máquina virtual, você precisará de uma conta de armazenamento. Estes diagnósticos de inicialização podem ajudá-lo a solucionar problemas e monitorar o status da VM. A conta de armazenamento que você cria serve apenas para armazenar os dados de diagnóstico de inicialização. Como cada conta de armazenamento deve ter um nome exclusivo, a seção a seguir gera textos aleatórios:

```tf
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraform.name}"
    }
    
    byte_length = 8
}
```

Agora você pode criar uma conta de armazenamento. A seção a seguir cria uma conta de armazenamento com o nome com base no texto aleatório gerado na etapa anterior:

```tf
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraform.name}"
    location            = "East US"
    account_type        = "Standard_LRS"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Criar máquina virtual
A etapa final é criar uma máquina virtual e usar todos os recursos criados. A seção a seguir cria uma VM denominada *myVM* e anexa a NIC virtual denominada *myNIC*. A imagem mais recente do *Ubuntu 16.04-LTS* é usada e um usuário chamado *azureuser* é criado com a autenticação de senha desabilitada. Os dados de chave SSH são fornecidos na seção *ssh_keys*.

```tf
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "East US"
    resource_group_name   = "${azurerm_resource_group.myterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled     = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Script completo do Terraform

Para reunir todas essas seções e ver Terraform em ação, crie um arquivo chamado *terraform_azure.tf* e cole o seguinte conteúdo:

```tf
variable "resourcename" {
  default = "myResourceGroup"
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn’t exist
resource "azurerm_resource_group" "myterraform" {
    name     = "myResourceGroup"
    location = "East US"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "East US"
    resource_group_name = "${azurerm_resource_group.myterraform.name}"

    tags {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "East US"
    resource_group_name          = "${azurerm_resource_group.myterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "East US"
    resource_group_name = "${azurerm_resource_group.myterraform.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "East US"
    resource_group_name       = "${azurerm_resource_group.myterraform.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraform.name}"
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraform.name}"
    location            = "East US"
    account_type        = "Standard_LRS"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "East US"
    resource_group_name   = "${azurerm_resource_group.myterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Criar e i,Implantar a infraestrutura
Com o modelo do Terraform criado, a primeira etapa é inicializar o Terraform. Esta etapa garante que o Terraform tem todos os pré-requisitos para criar o modelo no Azure.

```bash
terraform init
```

A próxima etapa é fazer o Terraform revisar e validar o modelo. Esta etapa compara os recursos solicitados para as informações de estado salvo por Terraform e, em seguida, gera a execução planejada. Os recursos *não* são criados no Azure.

```bash
terraform plan
```

Depois de executar o comando anterior, você deverá ver algo semelhante à seguinte tela:

```bash
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.

azurerm_resource_group.myterraform: Refreshing state... (ID: /subscriptions/guid/resourceGroups/myResourceGroup)
azurerm_public_ip.myterraformips: Refreshing state... (ID: /subscriptions/guid...t.Network/publicIPAddresses/myPublicIP)
azurerm_virtual_network.myterraformnetwork: Refreshing state... (ID: /subscriptions/guid...crosoft.Network/virtualNetworks/myVnet)
azurerm_subnet.myterraformsubnet: Refreshing state... (ID: /subscriptions/guid...irtualNetworks/myVnet/subnets/mySubnet)
azurerm_network_interface.myterraformnic: Refreshing state... (ID: /subscriptions/guid...rosoft.Network/networkInterfaces/myNIC)
azurerm_virtual_machine.myterraformvm: Refreshing state... (ID: /subscriptions/guid...Microsoft.Compute/virtualMachines/myVM)

The Terraform execution plan has been generated and is shown below.
Resources are shown in alphabetical order for quick scanning. Green resources
will be created (or destroyed and then created if an existing resource
exists), yellow resources are being changed in-place, and red resources
will be destroyed. Cyan entries are data sources to be read.

Note: You didn’t specify an “-out” parameter to save this plan, so when
“apply” is called, Terraform can’t guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Se tudo estiver correto, e você estiver pronto para criar a infraestrutura no Azure, aplique o modelo no Terraform:

```bash
terraform apply
```

Após a conclusão do Terraform, sua infraestrutura de VM estará pronta. Obtenha o endereço IP público da sua VM com [az vm show](/cli/azure/vm#show):

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Depois, você pode enviar por SSH para sua VM, como de costume:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Próximas etapas
Você criou a infraestrutura básica no Azure usando o Terraform. Para obter cenários mais complexos, incluindo exemplos que usam balanceadores de carga e conjuntos de dimensionamento de máquinas virtuais, confira os vários [exemplos do Terraform para o Azure](https://github.com/hashicorp/terraform/tree/master/examples). Para obter uma lista atualizada de provedores do Azure com suporte, consulte a [Documentação do Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).