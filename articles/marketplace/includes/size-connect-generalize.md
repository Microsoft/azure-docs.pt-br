---
title: incluir arquivo
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: addc18a0ebf9e49d3474d3f40cb1e2a6e0f0b272
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97826617"
---
## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens do Azure Marketplace devem ser reutilizáveis de uma forma genérica. Para obter isso, o VHD do sistema operacional deve ser generalizado, uma operação que remove todos os identificadores específicos de instância e drivers de software de uma VM.

### <a name="for-windows"></a>Para Windows

Os discos do sistema operacional Windows são generalizados com a ferramenta [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Se, mais tarde, você atualizar ou reconfigurar o sistema operacional, deverá executar novamente o Sysprep.

> [!WARNING]
> Depois de executar o Sysprep, desligue a VM até que ela seja implantada, pois as atualizações podem ser executadas automaticamente. Esse desligamento evitará atualizações subsequentes de alterações específicas da instância no sistema operacional ou serviços instalados. Para obter mais informações sobre como executar o sysprep, confira [Etapas para generalizar um VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Para Linux

O processo a seguir generalizará uma VM do Linux e a reimplantará como uma VM separada. Para detalhes, confira [Como criar uma imagem de uma máquina virtual ou um VHD](../../virtual-machines/linux/capture-image.md). Você pode parar quando chegar à seção chamada "criar uma VM com base na imagem capturada".

1. Remova o agente Linux do Azure.
    1. Conecte-se à VM do Linux usando um cliente SSH.
    2. Na janela SSH, digite este comando: `sudo waagent –deprovision+user` .
    3. Digite Y para continuar (Você pode adicionar o parâmetro -force ao comando anterior para evitar essa etapa de confirmação).
    4. Após a conclusão do comando, digite **Exit** para fechar o cliente SSH.
2. Parar máquina virtual.
    1. No portal do Azure, selecione seu RG (grupo de recursos) e desaloque a VM.
    2. Sua VM agora está generalizada e você pode criar uma nova VM usando esse disco de VM.

### <a name="take-a-snapshot-of-the-vm-disk"></a>Tirar um instantâneo do disco da VM

1. Entre no [portal do Azure](https://ms.portal.azure.com/).
2. Começando no canto superior esquerdo, selecione **criar um recurso**, em seguida, pesquise e selecione **instantâneo**.
3. Na folha instantâneo, selecione  **criar**.
4. Insira um **Nome** para o instantâneo.
5. Selecione um grupo de recursos existente ou insira o nome de um novo.
6. Para **Disco de origem**, selecione o disco gerenciado para instantâneo.
7. Escolha o **Tipo de conta** a ser usado para armazenar o instantâneo. Use **HD Standard**, a menos que você precise dele armazenado em um SSD de alto desempenho.
8. Selecione **Criar**.

#### <a name="extract-the-vhd"></a>Extrair o VHD

Use o script a seguir para exportar o instantâneo para um VHD em sua conta de armazenamento.

```azurecli-interactive
#Provide the subscription Id where the snapshot is created
$subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
$resourceGroupName=myResourceGroupName

#Provide the snapshot name
$snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
$sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
$storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
$storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
$storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
$destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para gerar o URI de SAS para um instantâneo e copia o VHD subjacente para uma conta de armazenamento usando o URI de SAS. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
| --- | --- |
| az disk grant-access | Gera o SAS somente leitura usado para copiar o arquivo VHD subjacente para uma conta de armazenamento ou o baixá-lo localmente
| az storage blob copy start | Copia um blob de forma assíncrona de uma conta de armazenamento para outra. Use `az storage blob show` para verificar o status do novo BLOB. |
|
