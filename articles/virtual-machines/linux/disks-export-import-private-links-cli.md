---
title: CLI do Azure – Restringir o acesso de importação/exportação aos discos gerenciados com Links Privados
description: Habilite Links Privados para seus discos gerenciados com a CLI do Azure. Permitindo que você exporte e importe discos com segurança somente na sua rede virtual.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 68b96401fc4fc6ea8916664978dbd4c094d9e5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684518"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>CLI do Azure – Restringir o acesso de importação/exportação aos discos gerenciados com Links Privados

Use [pontos de extremidade privados](../../private-link/private-endpoint-overview.md) para restringir a exportação e a importação de discos gerenciados e acesse dados com segurança em um [Link Privado](../../private-link/private-link-overview.md) em clientes na sua rede virtual do Azure. O ponto de extremidade privado usa um endereço IP do espaço de endereço de rede virtual para o serviço de discos gerenciados. O tráfego de rede entre os clientes na rede virtual e os discos gerenciados atravessa somente a rede virtual e um link privado na rede de backbone da Microsoft, eliminando a exposição na Internet pública.

Para usar Links Privados para exportar/importar discos gerenciados, primeiro crie um recurso de acesso a disco e vincule-o a uma rede virtual na mesma assinatura criando um ponto de extremidade privado. Em seguida, associe um disco ou um instantâneo a uma instância de acesso a disco. Por fim, defina a propriedade NetworkAccessPolicy do disco ou o instantâneo como `AllowPrivate`. Isso limitará o acesso à sua rede virtual. 

Você pode definir a propriedade NetworkAccessPolicy como `DenyAll` para impedir que qualquer pessoa exporte os dados de um disco ou de um instantâneo. O valor padrão da propriedade NetworkAccessPolicy é `AllowAll`.

## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Faça logon na sua assinatura e defina as variáveis

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Criar um acesso a disco usando a CLI do Azure
```azurecli
az disk-access create -n $diskAccessName -g $resourceGroupName -l $region

diskAccessId=$(az disk-access show -n $diskAccessName -g $resourceGroupName --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Não há suporte para políticas de rede, como NSG (grupos de segurança de rede), em pontos de extremidade privados. Para implantar um ponto de extremidade privado em determinada sub-rede, uma configuração de desabilitação explícita é necessária nessa sub-rede. 

```azurecli
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Desabilitar políticas de ponto de extremidade privado de sub-rede

O Azure implanta recursos em uma sub-rede dentro de uma rede virtual. Portanto, você precisa atualizar a sub-rede para desabilitar as políticas de rede do ponto de extremidade privado. 

```azurecli
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Criar um ponto de extremidade privado para o objeto de acesso a disco

```azurecli
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>Configurar a Zona DNS Privada

Crie uma Zona DNS Privada para o domínio do blob de armazenamento, crie um link de associação com a Rede Virtual e crie um Grupo de Zona DNS para associar o ponto de extremidade privado à Zona DNS Privada. 

```azurecli
az network private-dns zone create --resource-group $resourceGroupName \
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>Criar um disco protegido com Links Privados
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az disk create -n $diskName \
-g $resourceGroupName \
-l $region \
--size-gb $diskSizeGB \
--sku $diskSkuName \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Criar um instantâneo de um disco protegido com Links Privados
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az snapshot create -n $snapshotNameSecuredWithPL \
-g $resourceGroupName \
-l $region \
--source $diskId \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="next-steps"></a>Próximas etapas

- [Perguntas frequentes sobre os Links Privados](../faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Exportar/copiar instantâneos gerenciados como VHD para uma conta de armazenamento em outa região com a CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd)