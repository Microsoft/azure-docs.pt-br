---
title: Criar e gerenciar uma Máquina Virtual do Azure usando o Java | Microsoft Docs
description: Use o Java e o Azure Resource Manager para implantar uma máquina virtual e todos os seus recursos de suporte.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: b02fd8f012dee2436f4f276e05185428008508a1
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722580"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Criar e gerenciar VMs Windows no Azure usando o Java

Uma [VM (Máquina Virtual) do Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) precisa de vários recursos do Azure suporte. Este artigo aborda a criação, o gerenciamento e a exclusão de recursos de VM usando o Java. Você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto Maven
> * Adicionar dependências
> * Criar credenciais
> * Criar recursos
> * Executar outras tarefas de gerenciamento
> * Excluir recursos
> * Executar o aplicativo

São necessários cerca de 20 minutos para a conclusão destas etapas.

## <a name="create-a-maven-project"></a>Criar um projeto Maven

1. Se você ainda não fez isso, instale o [Java](https://aka.ms/azure-jdks).
2. Instale o [Maven](https://maven.apache.org/download.cgi).
3. Crie uma nova pasta e o projeto:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Adicionar dependências

1. Na pasta `testAzureApp`, abra o arquivo `pom.xml` e adicione a configuração de build ao &lt;projeto&gt; para habilitar a criação do aplicativo:

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Adicione as dependências necessárias para acessar o SDK do Java do Azure.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency>
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. Salve o arquivo.

## <a name="create-credentials"></a>Criar credenciais

Antes de começar essa etapa, verifique se você tem acesso a uma [entidade de serviço do Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Além disso, registre o ID do aplicativo, a chave de autenticação e o ID do locatário que serão necessários em uma etapa posterior.

### <a name="create-the-authorization-file"></a>Criar o arquivo de autorização

1. Crie um arquivo chamado `azureauth.properties` e adicione estas propriedades a ele:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Substitua **&lt;subscription-id&gt;** pelo identificador da assinatura, **&lt;application-id&gt;** pelo identificador de aplicativo do Active Directory, **&lt;authentication-key&gt;** pela chave do aplicativo e **&lt;tenant-id&gt;** pelo identificador do locatário.

2. Salve o arquivo.
3. Defina uma variável de ambiente chamada AZURE_AUTH_LOCATION no shell com o caminho completo para o arquivo de autenticação.

### <a name="create-the-management-client"></a>Criar o cliente de gerenciamento

1. Abra o arquivo `App.java` em `src\main\java\com\fabrikam` e verifique se esta declaração de pacote está na parte superior:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. Abaixo da declaração de pacote, adicione estas instruções de importação:
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. Para criar as credenciais do Active Directory necessárias para fazer solicitações, adicione este código ao método principal da classe App:
   
    ```java
    try {
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>Criar recursos

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Todos os recursos devem estar contidos em um [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md).

Para especificar valores para o aplicativo e criar o grupo de recursos, adicione este código ao bloco try no método principal:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Criar o conjunto de disponibilidade

Os [conjuntos de disponibilidade](tutorial-availability-sets.md) facilitam a manutenção das máquinas virtuais usadas por seu aplicativo.

Para criar o conjunto de disponibilidade, adicione este código ao bloco try no método principal:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Criar um endereço IP público

Um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) é necessário para se comunicar com a máquina virtual.

Para criar o endereço IP público para a máquina virtual, adicione este código ao bloco try no método principal:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Uma máquina virtual precisa estar em uma sub-rede de uma [Rede virtual](../../virtual-network/virtual-networks-overview.md).

Para criar uma sub-rede e uma rede virtual, adicione este código ao bloco try no método principal:

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>Criar a interface de rede

Uma máquina virtual precisa de uma interface de rede para se comunicar na rede virtual.

Para criar um adaptador de rede, adicione este código ao bloco try no método principal:

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Agora que você criou todos os recursos de suporte, você pode criar uma máquina virtual.

Para criar a máquina virtual, adicione este código ao bloco try no método principal:

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> Este tutorial cria uma máquina virtual executando uma versão do sistema operacional do Windows Server. Para saber mais sobre a seleção de outras imagens, veja [Navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Se você quiser usar um disco existente em vez de uma imagem do marketplace, use este código: 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .withSizeInGB(128)
    .withSku(DiskSkuTypes.PremiumLRS)
    .create();

azure.virtualMachines.define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .withExistingAvailabilitySet(availabilitySet)
    .withSize(VirtualMachineSizeTypes.StandardDS1)
    .create();
```

## <a name="perform-management-tasks"></a>Executar tarefas de gerenciamento

Durante o ciclo de vida de uma máquina virtual, é possível que você queira executar tarefas de gerenciamento, como inicialização, interrupção ou exclusão de uma máquina virtual. Além disso, é possível que você queira criar um código para automatizar tarefas complexas ou repetitivas.

Quando você precisar fazer alguma coisa com a VM, precisará obter uma instância dela. Adicione este código ao bloco try do método principal:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Obter informações sobre a VM

Para obter informações sobre a máquina virtual, adicione este código ao bloco try no método principal:

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="stop-the-vm"></a>Pare a VM.

Você pode parar uma máquina virtual e manter todas as suas configurações, mas continuará a ser cobrado por ela, ou pode parar uma máquina virtual e desalocá-la. Quando uma máquina virtual é desalocada, todos os recursos associados a ela também são desalocadas e a cobrança para eles termina.

Para interromper a máquina virtual sem desalocá-la, adicione este código ao bloco try no método principal:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Se você desejar desalocar a máquina virtual, altere a chamada PowerOff para este código:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Iniciar a VM

Para iniciar a máquina virtual, adicione este código ao bloco try no método principal:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Redimensionar a VM

Muitos aspectos da implantação devem ser considerados ao decidir sobre um tamanho para sua máquina virtual. Para obter mais informações, consulte [Tamanhos de VM](sizes.md).  

Para alterar o tamanho da máquina virtual, adicione este código ao bloco try no método principal:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adicionar um disco de dados à VM

Para adicionar um disco de dados à máquina virtual que tem 2 GB de tamanho, um LUN igual a 0 e um tipo de cache ReadWrite, adicione este código ao bloco try no método principal:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Excluir recursos

Como você é cobrado pelos recursos utilizados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Se você quiser excluir as máquinas virtuais e todos os recursos de suporte, tudo o que precisa fazer é excluir o grupo de recursos.

1. Para excluir o grupo de recursos, adicione este código ao bloco try no método principal:
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. Salve o arquivo App.java.

## <a name="run-the-application"></a>Executar o aplicativo

Devem ser necessários cerca de cinco minutos para o aplicativo de console executar completamente do início ao fim.

1. Para executar o aplicativo, use este comando do Maven:

    ```
    mvn compile exec:java
    ```

2. Antes de pressionar **Enter** para iniciar a exclusão de recursos, reserve alguns minutos para verificar a criação dos recursos no portal do Azure. Clique no status de implantação para ver informações sobre a implantação.


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como usar as [bibliotecas do Azure para Java](https://docs.microsoft.com/java/azure/java-sdk-azure-overview).

