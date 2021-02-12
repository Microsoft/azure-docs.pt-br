---
title: Montar o armazenamento de BLOBs do Azure usando o protocolo NFS 3,0 (visualização) | Microsoft Docs
description: Saiba como montar um contêiner no armazenamento de blobs de uma VM (máquina virtual) do Azure ou um cliente que é executado localmente usando o protocolo NFS 3,0.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: db946dcc0fc8571f7b6aa191909155baccf7d1a2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878571"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>Montar o armazenamento de BLOBs usando o protocolo NFS (sistema de arquivos de rede) 3,0 (versão prévia)

Você pode montar um contêiner no armazenamento de blobs de uma VM (máquina virtual) do Azure baseada em Windows ou Linux ou um sistema Windows ou Linux que é executado no local usando o protocolo NFS 3,0. Este artigo fornece orientações passo a passo. Para saber mais sobre o suporte ao protocolo NFS 3,0 no armazenamento de BLOBs, consulte [suporte ao protocolo NFS (Network File System) 3,0 no armazenamento de BLOBs do Azure (versão prévia)](network-file-system-protocol-support.md).

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>Etapa 1: registrar o recurso de protocolo NFS 3,0 com sua assinatura

1. Abra uma janela de comando do PowerShell. 

2. Entre na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

   ```powershell
   Connect-AzAccount
   ```

3. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

4. Registre o `AllowNFSV3` recurso usando o comando a seguir.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Registre o provedor de recursos usando o comando a seguir.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>Etapa 2: verificar se o recurso está registrado 

A aprovação de registro pode levar até uma hora. Para verificar se o registro foi concluído, use os comandos a seguir.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>Etapa 3: criar uma rede virtual do Azure (VNet)

Sua conta de armazenamento deve estar contida em uma VNet. Uma VNet permite que os clientes se conectem com segurança à sua conta de armazenamento. Para saber mais sobre VNet e como criar uma, consulte a documentação da [rede virtual](../../virtual-network/index.yml).

> [!NOTE]
> Os clientes na mesma VNet podem montar contêineres em sua conta. Você também pode montar um contêiner de um cliente que é executado em uma rede local, mas você precisará primeiro conectar sua rede local à sua VNet. Consulte [conexões de rede com suporte](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-4-configure-network-security"></a>Etapa 4: configurar a segurança de rede

A única maneira de proteger os dados em sua conta é usando uma VNet e outras configurações de segurança de rede. Qualquer outra ferramenta usada para proteger dados, incluindo autorização de chave de conta, segurança de Azure Active Directory (AD) e listas de controle de acesso (ACLs), ainda não tem suporte em contas que têm o suporte ao protocolo NFS 3,0 habilitado neles. 

Para proteger os dados em sua conta, consulte estas recomendações: [recomendações de segurança de rede para o armazenamento de BLOBs](security-recommendations.md#networking).

## <a name="step-5-create-and-configure-a-storage-account"></a>Etapa 5: criar e configurar uma conta de armazenamento

Para montar um contêiner usando o NFS 3,0, você deve criar uma conta de armazenamento **depois** de registrar o recurso com sua assinatura. Você não pode habilitar contas que existiam antes de registrar o recurso. 

Na versão de visualização desse recurso, o protocolo NFS 3,0 tem suporte nas contas [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) e de [uso geral v2](../common/storage-account-overview.md#general-purpose-v2-accounts) .

Ao configurar a conta, escolha estes valores:

|Configuração | Desempenho premium | Desempenho padrão  
|----|---|---|
|Location|Todas as regiões disponíveis |Uma das seguintes regiões: leste da Austrália, Coreia central e EUA Central do Sul   
|Desempenho|Premium| Standard
|Tipo de conta|BlockBlobStorage| Uso geral V2
|Replicação|Armazenamento com redundância local (LRS)| Armazenamento com redundância local (LRS)
|Método de conectividade|Ponto de extremidade público (redes selecionadas) ou ponto de extremidade privado |Ponto de extremidade público (redes selecionadas) ou ponto de extremidade privado
|Transferência segura é necessária|Desabilitado|Desabilitado
|Namespace hierárquico|habilitado|habilitado
|NFS V3|habilitado |habilitado 

Você pode aceitar os valores padrão para todas as outras configurações. 

## <a name="step-6-create-a-container"></a>Etapa 6: Criar um contêiner

Crie um contêiner em sua conta de armazenamento usando qualquer uma dessas ferramentas ou SDKs:

|Ferramentas|SDKs|
|---|---|
|[Azure portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[CLI do Azure](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>Etapa 7: montar o contêiner

Crie um diretório em seu sistema Windows ou Linux e, em seguida, monte um contêiner na conta de armazenamento.

### <a name="linux"></a>[Linux](#tab/linux)

1. Em um sistema Linux, crie um diretório.

   ```
   mkdir -p /mnt/test
   ```

2. Monte um contêiner usando o comando a seguir.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Substitua o `<storage-account-name>` espaço reservado que aparece nesse comando pelo nome da sua conta de armazenamento.  

   - Substitua o `<container-name>` espaço reservado pelo nome do seu contêiner.


### <a name="windows"></a>[Windows](#tab/windows)

1. Abra a caixa de diálogo **recursos do Windows** e ative o recurso **cliente para NFS** . 

   ![Recurso cliente para sistema de arquivos de rede](media/network-file-system-protocol-how-to/client-for-network-files-system-feature.png)

2. Abra uma janela de **prompt de comando** (cmd.exe). Em seguida, monte um contêiner usando o comando [Mount](/windows-server/administration/windows-commands/mount) .

   ```
   mount -o nolock <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name> *
   ```

   - Substitua o `<storage-account-name>` espaço reservado que aparece nesse comando pelo nome da sua conta de armazenamento.  

   - Substitua o `<container-name>` espaço reservado pelo nome do seu contêiner.

3. Se você precisar de permissões de gravação, talvez seja necessário alterar o UID e o GID padrão que o Windows usa para se conectar ao compartilhamento. Para fazer isso, execute os seguintes comandos do PowerShell como administrador:

   ```
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousUid -PropertyType DWord -Value 0
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousGid -PropertyType DWord -Value 0
   ```
   
   - Reinicie o serviço de cliente NFS ou reinicialize o servidor depois de fazer essa alteração.

---

## <a name="resolve-common-issues"></a>Resolver problemas comuns

|Problema/erro | Resolução|
|---|---|
|`Access denied by server while mounting`|Verifique se o cliente está sendo executado em uma sub-rede com suporte. Consulte os [locais de rede com suporte](network-file-system-protocol-support.md#supported-network-connections).|
|`No such file or directory`| Verifique se o contêiner que você está montando foi criado após verificar se o recurso foi registrado. Consulte [etapa 2: verificar se o recurso está registrado](#step-2-verify-that-the-feature-is-registered). Além disso, certifique-se de digitar o comando mount e os parâmetros diretamente no terminal. Se você copiar e colar qualquer parte deste comando no terminal de outro aplicativo, os caracteres ocultos nas informações coladas poderão causar esse erro.|

## <a name="see-also"></a>Confira também

[Suporte ao protocolo NFS (sistema de arquivos de rede) 3,0 no armazenamento de BLOBs do Azure (versão prévia)](network-file-system-protocol-support.md)
