---
title: Provisionar pools de Lote do Azure de imagens personalizadas | Microsoft Docs
description: "Você pode criar um pool de Lote de computação de uma imagem personalizada para provisionar nós que contêm o software e os dados que você precisa para seu aplicativo. Imagens personalizadas são uma maneira eficiente para configurar nós de computação para executar suas cargas de trabalho do Lote."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.openlocfilehash: 0816c464b6b52747148cc42a55445048901e7595
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Usar uma imagem personalizada para criar um pool de máquinas virtuais

Quando cria um pool de máquinas virtuais no Lote do Azure, você especifica uma imagem de VM (máquina virtual) que fornece o sistema operacional para cada nó de computação no pool. Você pode criar um pool de máquinas virtuais usando uma imagem do Azure Marketplace ou fornecendo uma imagem de VHD personalizada que você preparou. Quando você fornece uma imagem personalizada, você tem controle sobre como o sistema operacional é configurado no momento em que cada nó de computação é provisionado. Sua imagem personalizada também pode incluir aplicativos e dados de referência que estão disponíveis no nó de computação, assim que ela é provisionada.

Usar uma imagem personalizada pode economizar tempo na obtenção do seu pool de nós de computação prontos para executar sua carga de trabalho do Lote. Embora você sempre possa usar uma imagem do Azure Marketplace e instalar o software em cada nó de computação depois de ele ser configurado, essa abordagem pode ser menos eficiente do que usar uma imagem personalizada. 

Alguns motivos para usar uma imagem personalizada configurada para seu cenário incluem a necessidade de:

- **Configurar o SO (sistema operacional)** Qualquer configuração especial do sistema operacional pode ser feita na imagem personalizada. 
- **Instalar aplicativos grandes.** Instalar aplicativos em uma imagem personalizada é mais eficiente do que instalá-los em cada nó de computação após seu provisionamento.
- **Copiar quantidades significativas de dados.** Se os dados forem copiados para a imagem personalizada, eles só precisarão ser copiados uma vez, em vez de serem copiados para cada nó de computação, economizando tempo e largura de banda.
- **Reinicializar a VM durante o processo de instalação.** Reinicializar a VM pode ser um processo demorado, especialmente se você tiver vários nós de computação.

## <a name="prerequisites"></a>Pré-requisitos

- **Uma conta do Lote criada com o modo de alocação de pools de Assinatura de Usuário.** Com esse modo, os pools do Lote são alocados para a assinatura onde reside a conta. Veja a seção [Conta](batch-api-basics.md#account) em [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md) para obter mais informações.

- **Uma conta de Armazenamento do Azure.** Para criar um pool de máquinas virtuais usando uma imagem personalizada, você precisa de uma conta padrão de uso geral do Armazenamento do Azure na mesma assinatura e região. Se criar uma imagem personalizada de uma VM do Azure, você copiará a imagem para a conta de armazenamento em que o disco do SO da VM reside e não será necessário criar uma conta de armazenamento separada. 
    
## <a name="prepare-a-custom-image"></a>Preparar uma imagem personalizada

Para preparar uma imagem personalizada para uso com o Lote, você deve generalizar uma instalação existente do Windows ou do Linux. Generalizar uma instalação de sistema operacional remove informações específicas do computador. O resultado é uma imagem que pode ser instalada em outros computadores ou VMs.  

> [!IMPORTANT]
> Atualmente, o Lote não dá suporte ao uso de imagens gerenciadas do Azure para provisionar um pool. A imagem personalizada que você usa para provisionar um pool deve estar armazenada no Armazenamento do Azure. 
>
> Ao preparar sua imagem personalizada, lembre-se do seguinte:
> - Verifique se a imagem do sistema operacional base usada para provisionar os pools do Lote não têm extensões do Azure pré-instaladas, como a extensão Script Personalizado. Se a imagem contém uma extensão pré-instalada, o Azure pode ter problemas ao implantar a VM.
> - Verifique se a imagem do sistema operacional base fornecida usa a unidade temporária padrão, já que o agente de nó do Lote espera a unidade temporária padrão.
>
>

Você pode usar qualquer imagem preparada existente do Windows ou Linux como uma imagem personalizada. Por exemplo, se você quiser usar uma imagem local, carregue a imagem para uma conta de Armazenamento do Azure que esteja na mesma assinatura e região que sua conta do Lote usando [AzCopy](../storage/storage-use-azcopy.md) ou outra ferramenta de upload.

Você também pode preparar uma imagem personalizada de uma VM do Azure nova ou existente. Se estiver criando uma nova VM, você poderá usar uma imagem do Azure Marketplace como a imagem base para sua imagem personalizada e, então, personalizá-la. Para personalizar a imagem base, crie uma VM do Azure e adicione os aplicativos ou dados a ela. Em seguida, generalize a VM para servir como sua imagem personalizada e salve-a no Armazenamento do Azure. 

Para preparar uma imagem personalizada em uma VM do Azure, siga estas etapas:

1. Crie uma VM do Azure **não gerenciada** em uma imagem do Azure Marketplace. O Azure Marketplace inclui imagens para [Windows](../virtual-machines/windows/quick-create-portal.md) e [Linux](../virtual-machines/linux/quick-create-portal.md).
    
    Na etapa 3 do processo de criação da VM, é importante selecionar **Não** para a opção **Armazenamento: usar discos gerenciados**. Além disso, tome nota da conta de armazenamento do disco do SO da VM, pois essa conta de armazenamento também é onde o Azure salvará sua imagem personalizada:

    ![Criar uma VM não gerenciada e tomar nota do nome da conta de armazenamento](media/batch-custom-images/vm-create-storage.png)
 
2. Conclua o processo de criação de sua VM e espere que ela seja alocada pelo Azure. Veja uma imagem que mostra uma VM no portal do Azure no estado de execução:

    ![Criar uma VM de uma imagem do Marketplace](media/batch-custom-images/vm-status-running.png)

3. Quando a VM estiver em execução, conecte-se a ela via RDP (para Windows) ou SSH (para Linux). Instale o software necessário ou copie os dados desejados e, em seguida, generalize a VM. Execute as etapas descritas em [Generalizar a VM](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized.md#generalize-the-vm). 
   
4. Siga as etapas para [Fazer logon no Azure PowerShell](../virtual-machines/windows/sa-copy-generalized.md#log-in-to-azure-powershell). Para instalar o Azure PowerShell, consulte [Visão geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.2.0). 

5. Em seguida, siga as etapas para [Desalocar a VM e definir o estado como generalizado](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized#deallocate-the-vm-and-set-the-state-to-generalized). 

    No portal do Azure, observe que a VM está desalocada:

    ![Certifique-se de que a VM esteja desalocada](media/batch-custom-images/vm-status-deallocated.png)

6.  Crie e salve a imagem da VM no Armazenamento do Azure usando o cmdlet [Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) do PowerShell. Siga as instruções descritas em [Criar a imagem](../virtual-machines/windows/sa-copy-generalized.md#create-the-image).
    
    A imagem da VM é salva na conta de Armazenamento do Azure criada quando a VM foi criada, conforme mostrado na etapa 1 deste procedimento. O cmdlet Save-AzureRmVMImage salva a imagem no contêiner **system** na conta de armazenamento. O parâmetro `-DestinationContainername` nomeia um diretório virtual dentro do contêiner **system**. O parâmetro `-VHDNamePrefix` especifica um prefixo para o nome do blob. Esse prefixo é anexado ao início do nome do blob com um hífen. 

    Por exemplo, suponha que você chame Save-AzureRmVMImage com os seguintes parâmetros:  

        Save-AzureRmVMImage -ResourceGroupName sample-resource-group -Name vm-custom-image -DestinationContainerName batchimages -VHDNamePrefix custom -Path C:\Temp\Images\vm-custom-image.json

    A imagem resultante será salva no local e com o nome do blob mostrado aqui:

    ![Local do VHD salvo no contêiner do sistema](media/batch-custom-images/vhd-in-vm-storage-account.png)

    > [!NOTE]
    > Uma VM não gerenciada do Azure cria várias contas de armazenamento para finalidades diferentes. Se você não tiver tomado nota do nome do contêiner de armazenamento do disco do SO quando a VM foi criada, localize a conta de armazenamento associada que contém o contêiner **system**. Navegue pelo contêiner **system** para localizar a imagem personalizada usando os valores especificados para o comando **Save-AzureRmVMImage**.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Criar um pool de uma imagem personalizada no portal

Após ter salvo sua imagem personalizada e quando souber sua localização, você pode criar um pool do Lote dessa imagem. Siga estas etapas para criar um pool no portal do Azure:

1. Navegue até sua conta do Lote no portal do Azure. Esta conta deve estar na mesma assinatura e na mesma região que a conta de armazenamento que contém a imagem personalizada. 
2. Na janela **Configurações** à esquerda, selecione o item de menu **Pools**.
3. Na janela **Pools**, selecione o comando **Adicionar**.
4. Na janela **Adicionar Pool**, selecione **Imagem Personalizada (Windows/Linux)** na lista suspensa **Tipo de Imagem**. O portal exibe o seletor **Imagem Personalizada**. Navegue até a conta de armazenamento na qual se encontra a imagem personalizada e selecione o VHD desejado no contêiner. 
5. Selecione o **Editor/Oferta/SKU** adequado para seu VHD personalizado.
6. Especifique o restante das configurações necessárias, incluindo o **Tamanho do nó**, os **Nós dedicados de destino** e os **Nós de baixa prioridade**, bem como qualquer configuração opcional desejada.

    Por exemplo, para uma imagem personalizada do Microsoft Windows Server Datacenter 2016, a janela **Adicionar Pool** aparece como mostrado aqui:

    ![Adicionar pool de uma imagem personalizada do Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Para verificar se um pool existente é baseado em uma imagem personalizada, consulte a propriedade **Sistema Operacional** na seção de resumo de recursos da folha **Pool**. Se o pool tiver sido criado de uma imagem personalizada, ela estará definida como **Imagem de VM personalizada**.

Todos os VHDs personalizados associados a um pool são exibidos na janela **Propriedades** do pool.
 
## <a name="next-steps"></a>Próximas etapas

- Para uma visão geral detalhada do Lote, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).
- Para obter informações sobre como criar uma conta do Lote, confira [Criar uma conta do Lote com o Portal do Azure](batch-account-create-portal.md).