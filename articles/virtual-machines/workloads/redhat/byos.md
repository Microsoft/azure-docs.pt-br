---
title: Red Hat Enterprise Linux imagens do Azure traga sua própria assinatura | Microsoft Docs
description: Saiba mais sobre as imagens traga sua própria assinatura para Red Hat Enterprise Linux no Azure.
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 7006096154d8958e13e7a86783bd07067b95d293
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872885"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux imagens Gold de sua própria assinatura no Azure

As imagens Red Hat Enterprise Linux (RHEL) estão disponíveis no Azure por meio de um modelo pago pelo uso ou BYOS (traga sua própria assinatura) (imagem do Red Hat Gold). Este artigo fornece uma visão geral das imagens do Red Hat Gold no Azure.

>[!NOTE]
> As imagens do RHEL BYOS Gold estão disponíveis nas nuvens públicas (comerciais) do Azure e do Azure governamental. Eles não estão disponíveis nas nuvens do Azure China ou do Azure Blackforest.

## <a name="important-points-to-consider"></a>Pontos importantes a serem considerados

- As imagens do Red Hat Gold fornecidas neste programa são imagens RHEL prontas para produção, semelhantes às imagens do RHEL pago pelo uso no Azure Marketplace.
- As imagens seguem as políticas atuais descritas em [Red Hat Enterprise Linux imagens no Azure](./redhat-images.md).
- As políticas de suporte padrão se aplicam a VMs criadas com base nessas imagens.
- As VMs provisionadas a partir de imagens Red Hat Gold não carregam taxas de RHEL associadas às imagens pré-pagas do RHEL.
- As imagens não são qualificadas. Você deve usar o Red Hat Subscription-Manager para registrar e assinar as VMs para obter atualizações do Red Hat diretamente.
- É possível mudar de imagens pré-pagas para BYOS usando o [benefício híbrido do Azure](../../linux/azure-hybrid-benefit-linux.md). No entanto, não é possível mudar de um BYOS implantado inicialmente para modelos de cobrança pagos conforme o uso para imagens do Linux. Para mudar o modelo de cobrança de BYOS para pré-pago, você deve reimplantar a VM da respectiva imagem.

>[!NOTE]
> As imagens de BYOS de geração 2 RHEL não estão disponíveis atualmente por meio da oferta do Marketplace. Se você precisar de uma imagem de BYOS de geração 2 RHEL, visite o painel de acesso à nuvem no gerenciamento de assinaturas do Red Hat. Para obter mais informações, consulte a [documentação do Red Hat](https://access.redhat.com/articles/4847681).

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Requisitos e condições para acessar as imagens do Red Hat Gold

1. Familiarize-se com os termos do [programa Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) . Habilite suas assinaturas do Red Hat para acesso à nuvem no [Red Hat Subscription-Manager](https://access.redhat.com/management/cloud). Você precisa ter disponível as assinaturas do Azure que serão registradas para acesso à nuvem.

1. Se as assinaturas do Red Hat que você habilitou para acesso à nuvem atenderem aos requisitos de qualificação, suas assinaturas do Azure serão habilitadas automaticamente para acesso de imagem Gold.

### <a name="expected-time-for-image-access"></a>Tempo esperado para acesso à imagem

Depois de concluir as etapas de habilitação do acesso à nuvem, o Red Hat valida sua elegibilidade para as imagens do Red Hat Gold. Se a validação for bem-sucedida, você receberá acesso às imagens Gold dentro de três horas.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Use as imagens do Red Hat Gold do portal do Azure

1. Depois que sua assinatura do Azure receber acesso às imagens do Red Hat Gold, você poderá localizá-las no [portal do Azure](https://portal.azure.com). Vá para **criar um recurso**  >  **ver tudo**.

1. Na parte superior da página, você verá que tem ofertas privadas.

    ![Ofertas privadas do Marketplace](./media/rhel-byos-privateoffers.png)

1. Selecione o link roxo ou role para baixo até a parte inferior da página para ver suas ofertas particulares.

1. O restante do provisionamento na interface do usuário não é diferente de qualquer outra imagem do Red Hat existente. Escolha a versão do RHEL e siga os prompts para provisionar sua VM. Esse processo também permite que você aceite os termos da imagem na etapa final.

>[!NOTE]
>Essas etapas até o momento não habilitarão a imagem do Red Hat Gold para implantação programática. Uma etapa adicional é necessária, conforme descrito na seção "informações adicionais".

O restante deste documento se concentra no método CLI para provisionar e aceitar os termos da imagem. A interface do usuário e a CLI são totalmente intercambiáveis no que diz respeito ao resultado final (uma VM de imagem dourada do RHEL, provisionada).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Use as imagens do Red Hat Gold do CLI do Azure

As instruções a seguir orientam você pelo processo de implantação inicial para uma VM RHEL usando o CLI do Azure. Estas instruções pressupõem que você tenha o [CLI do Azure instalado](/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Certifique-se de usar todas as letras minúsculas nas referências de Publicador, oferta, plano e imagem para todos os comandos a seguir.

1. Verifique se você está na sua assinatura desejada.

    ```azurecli
    az account show -o=json
    ```

1. Crie um grupo de recursos para sua VM de imagem do Red Hat Gold.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Aceite os termos da imagem.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn redhat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Esses termos precisam ser aceitos *uma vez por assinatura do Azure, por SKU de imagem*.

1. Adicional Valide sua implantação de VM com o seguinte comando:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest --validate
    ```

1. Provisione sua VM executando o mesmo comando, conforme mostrado no exemplo anterior sem o `--validate` argumento.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn>

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest
    ```

1. SSH em sua VM e verifique se você tem uma imagem não qualificada. Para executar esta etapa, execute `sudo yum repolist` . Para RHEL 8, use `sudo dnf repolist` . A saída solicita que você use Subscription-Manager para registrar a VM com o Red Hat.

>[!NOTE]
>No RHEL 8, `dnf` e `yum` são intercambiáveis. Para obter mais informações, consulte o [Guia de administração do RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Usar as imagens do Red Hat Gold do PowerShell

O seguinte script é um exemplo. Substitua o grupo de recursos, o local, o nome da VM, as informações de logon e outras variáveis pela configuração de sua escolha. As informações do editor e do plano devem estar em minúsculas.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher redhat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Criptografar imagens Gold de Red Hat Enterprise Linux traga sua própria assinatura

Red Hat Enterprise Linux imagens Gold BYOS podem ser protegidas por meio do uso de [Azure Disk Encryption](../../linux/disk-encryption-overview.md). A assinatura *deve* ser registrada antes que você possa habilitar a criptografia. Para obter mais informações sobre como registrar uma imagem do RHEL BYOS Gold, consulte [como registrar e assinar um sistema no portal do cliente do Red Hat usando o Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273). Se você tiver uma assinatura ativa do Red Hat, também poderá ler a [criação de chaves de ativação do portal do cliente do Red Hat](https://access.redhat.com/articles/1378093).

Não há suporte para Azure Disk Encryption em [imagens personalizadas do Red Hat](../../linux/redhat-create-upload-vhd.md). Requisitos e pré-requisitos adicionais de Azure Disk Encryption são documentados em [Azure Disk Encryption para VMs do Linux](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Para obter as etapas para aplicar Azure Disk Encryption, consulte [Azure Disk Encryption cenários em VMs do Linux](../../linux/disk-encryption-linux.md) e artigos relacionados.

## <a name="additional-information"></a>Informações adicionais

- Se você tentar provisionar uma VM em uma assinatura que não está habilitada para esta oferta, receberá a seguinte mensagem:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    Nesse caso, contate a Microsoft ou a Red Hat para habilitar sua assinatura.

- Se você modificar um instantâneo de uma imagem RHEL BYOS e tentar publicar essa imagem personalizada na Galeria de [imagens compartilhadas](../../shared-image-galleries.md), deverá fornecer informações de plano que correspondam à origem original do instantâneo. Por exemplo, o comando pode ter a seguinte aparência:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Observe os parâmetros do plano na linha final.

    Não há suporte para [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) em imagens personalizadas.

- Se você usar a automação para provisionar VMs das imagens RHEL BYOS, deverá fornecer parâmetros de plano semelhantes ao que foi mostrado nos comandos de exemplo. Por exemplo, se você usar Terraform, você fornecerá as informações do plano em um [bloco de plano](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Próximas etapas

- Mais detalhes sobre o acesso à nuvem Red Hat estão disponíveis na [documentação da nuvem pública do Red Hat](https://access.redhat.com/public-cloud)
- Para obter guias passo a passo e detalhes do programa para acesso à nuvem, consulte a [documentação do Red Hat Cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index).
- Para saber mais sobre a infraestrutura de atualização do Red Hat, consulte [infraestrutura de atualização do Red Hat do Azure](./redhat-rhui.md).
- Para saber mais sobre todas as imagens do Red Hat no Azure, consulte a [página de documentação](./redhat-images.md).
- Para obter informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL, consulte a página [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
- Para obter mais documentação sobre as imagens do RHEL Gold, consulte a [documentação do Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access).