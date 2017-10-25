## <a name="overview"></a>Visão geral
Quando você cria uma nova VM (máquina virtual) em um Grupo de Recursos implantando uma imagem do [Azure Marketplace](https://azure.microsoft.com/marketplace/), a unidade do sistema operacional padrão normalmente é 127 GB (algumas imagens têm tamanhos de disco de sistema operacional menores por padrão). Embora seja possível adicionar discos de dados à VM (a quantidade depende do SKU escolhido) e além de ser recomendável instalar aplicativos e cargas de trabalho que consomem bastante CPU nesses discos adicionados, muitas vezes, os clientes precisam expandir a unidade do sistema operacional para oferecer suporte a determinados cenários como estes:

1. Suporte a aplicativos herdados que instalam componentes na unidade do sistema operacional.
2. Migração de um PC físico ou máquina virtual local com uma unidade do sistema operacional maior.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: Resource Manager e Clássico. Este artigo aborda o uso do modelo de implantação Resource Manager. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.
> 
> 

## <a name="resize-the-os-drive"></a>Redimensionar a unidade do sistema operacional
Neste artigo, vamos executar a tarefa de redimensionar a unidade do sistema operacional usando módulos do Resource Manager do [Azure PowerShell](/powershell/azureps-cmdlets-docs). Abra o ISE do PowerShell ou a janela do PowerShell no modo administrativo e siga as etapas abaixo:

1. Entre em sua conta do Microsoft Azure no modo de gerenciamento de recursos e escolha a sua assinatura como se segue:
   
   ```Powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Defina o nome do grupo de recursos e o nome da VM como se segue:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Obtenha uma referência para sua VM da seguinte maneira:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Interrompa a VM antes de redimensionar o disco da seguinte maneira:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. E chega o momento pelo qual estávamos esperando! Defina o tamanho do disco do sistema operacional para o valor desejado e atualize a VM da seguinte maneira:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é 2048 GB. (É possível expandir o blob VHD além desse tamanho, mas o sistema operacional só poderá trabalhar com os primeiros 2048 GB de espaço.)
   > 
   > 
6. A atualização da VM pode demorar um pouco. Depois que o comando concluir a execução, reinicie a VM como se segue:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

É isso. Agora, com o RDP na VM, abra Gerenciamento do Computador (ou Gerenciamento de Disco) e expanda a unidade usando o espaço recentemente alocado.

## <a name="summary"></a>Resumo
Neste artigo, usamos os módulos do Azure Resource Manager do PowerShell para expandir a unidade do sistema operacional de uma máquina virtual IaaS. Veja abaixo o script completo reproduzido para sua referência:

```Powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="next-steps"></a>Próximas etapas
Embora neste artigo tenhamos focado basicamente a expansão do disco do sistema operacional da VM, o script desenvolvido também pode ser usado para expandir os discos de dados conectados à VM, bastando alterar uma única linha de código. Por exemplo, para expandir o primeiro disco de dados conectado à VM, substitua o objeto ```OSDisk``` de ```StorageProfile``` pela matriz ```DataDisks``` e use um índice numérico para obter uma referência para o primeiro disco de dados conectado, como mostrado abaixo:

```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Da mesma forma, você pode fazer referência a outros discos de dados conectados à VM usando um índice, conforme mostrado acima, ou a propriedade ```Name``` do disco, conforme ilustrado abaixo:

```Powershell
($vm.StorageProfile.DataDisks | Where {$_.Name -eq 'my-second-data-disk'})[0].DiskSizeGB = 1023
```

Se quiser saber como conectar discos a uma VM do Azure Resource Manager, leia este [artigo](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

