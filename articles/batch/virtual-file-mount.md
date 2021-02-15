---
title: Montar um sistema de arquivos virtual em um pool
description: Saiba como montar um sistema de arquivos virtual em um pool do Lote.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/13/2019
ms.openlocfilehash: df03275fdeea88df1a2f2b6e2cda55021497cdf7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89145477"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Montar um sistema de arquivos virtual em um pool do Lote

Agora o Lote do Azure dá suporte à montagem de armazenamento em nuvem ou um sistema de arquivos externo nos nós de computação do Windows ou Linux nos pools do Lote. Quando um nó de computação entra em um pool, o sistema de arquivos virtual é montado e tratado como uma unidade local nesse nó. Você pode montar sistemas de arquivos, como Arquivos do Azure, armazenamento de Blobs do Azure, NFS (sistema de arquivos de rede), incluindo um [cache Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) ou CIFS (sistema de arquivos de Internet comum).

Neste artigo, você saberá como montar um sistema de arquivos virtual em um pool de nós de computação, usando a [Biblioteca de gerenciamento de lotes para .NET](/dotnet/api/overview/azure/batch).

> [!NOTE]
> A montagem de um sistema de arquivos virtual é compatível com os pools do Lote criados na versão 2019-08-19 ou posterior. Os pools do Lote criados antes da versão 2019-08-19 não são compatíveis com esse recurso.
> 
> As APIs de montagem dos sistemas de arquivos em um nó de computação fazem parte da [Biblioteca do Lote para .NET](/dotnet/api/microsoft.azure.batch).

## <a name="benefits-of-mounting-on-a-pool"></a>Benefícios da montagem em um pool

A montagem do sistema de arquivos no pool, em vez de permitir que as tarefas recuperem seus próprios dados de um grande conjunto de dados, torna mais fácil e mais eficiente que as tarefas acessem os dados necessários.

Considere um cenário com várias tarefas que exigem acesso a um conjunto comum de dados, como a renderização de um filme. Cada tarefa renderiza um ou mais quadros por vez a partir dos arquivos de cena. Ao montar uma unidade que contém os arquivos de cena, é mais fácil para nós de computação acessar dados compartilhados. Além disso, o sistema de arquivos subjacente pode ser escolhido e dimensionado de forma independente com base no desempenho e na escala (taxa de transferência e IOPS) exigidos pelo número de nós de computação que acessam os dados simultaneamente. Por exemplo, um cache distribuído na memória do [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) pode ser usado para dar suporte a renderizações em grande escala de filme com milhares de nós de renderização simultâneos, acessando os dados de origem que residem no local. Como alternativa, para dados que já residem no armazenamento de Blobs em nuvem, o [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) pode ser usado para montar esses dados como um sistema de arquivos local. O blobfuse está disponível apenas nos nós do Linux. No entanto, os [Arquivos do Azure](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) fornecem um fluxo de trabalho semelhante e está disponível no Windows e no Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Montar um sistema de arquivos virtual em um pool  

A montagem de um sistema de arquivos virtual em um pool torna o sistema de arquivos disponível para cada nó de computação no pool. O sistema de arquivos é configurado quando um nó de computação entra em um pool ou quando o nó é reiniciado ou refeito.

Para montar um sistema de arquivos em um pool, crie um objeto `MountConfiguration`. Escolha o objeto que se adapte ao sistema de arquivos virtual: `AzureBlobFileSystemConfiguration`, `AzureFileShareConfiguration`, `NfsMountConfiguration`ou `CifsMountConfiguration`.

Todos os objetos de configuração de montagem precisam dos seguintes parâmetros de base. Algumas configurações de montagem têm parâmetros específicos para o sistema de arquivos que estão sendo usados, que são abordados mais detalhadamente nos exemplos de código.

- **Nome ou origem da conta**: Para montar um compartilhamento de arquivos virtual, você precisará do nome da conta de armazenamento ou de sua origem.
- **Caminho de montagem relativo ou origem**: O local do sistema de arquivos montado no nó de computação em relação ao diretório `fsmounts` padrão acessível no nó por meio de `AZ_BATCH_NODE_MOUNTS_DIR`. O local exato varia dependendo do sistema operacional usado no nó. Por exemplo, o local físico em um nó Ubuntu é mapeado para `mnt\batch\tasks\fsmounts` e, em um nó CentOS, ele é mapeado para `mnt\resources\batch\tasks\fsmounts`.
- **Opções de montagem ou opções de blobfuse**: Essas opções descrevem parâmetros específicos para montar um sistema de arquivos.

Depois que o objeto `MountConfiguration` for criado, atribua o objeto à propriedade `MountConfigurationList` ao criar o pool. O sistema de arquivos é montado quando um nó entra em um pool ou quando o nó é reiniciado ou refeito.

Quando o sistema de arquivos é montado, é criada uma variável de ambiente `AZ_BATCH_NODE_MOUNTS_DIR` que aponta para o local dos sistemas de arquivos montados, bem como dos arquivos de log, que são úteis para solução de problemas e depuração. Os arquivos de log são explicados mais detalhadamente na seção [Diagnosticar erros de montagem](#diagnose-mount-errors).  

> [!IMPORTANT]
> O número máximo de sistemas de arquivos montados em um pool é 10. Confira [Cotas e limites de serviço do Lote](batch-quota-limit.md#other-limits) para obter detalhes e outros limites.

## <a name="examples"></a>Exemplos

Os exemplos de código a seguir demonstram a montagem de diversos compartilhamentos de arquivos em um pool de nós de computação.

### <a name="azure-files-share"></a>Compartilhamento de Arquivos do Azure

Os Arquivos do Azure consistem na oferta padrão do sistema de arquivos de nuvem do Azure. Para saber mais sobre como obter qualquer um dos parâmetros no exemplo de código de configuração de montagem, confira [Usar um compartilhamento de Arquivos do Azure](../storage/files/storage-how-to-use-files-windows.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Sistema de arquivos de blob do Azure

Outra opção é usar o armazenamento de blob do Azure por meio do [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). A montagem de um sistema de arquivos de blob requer um `AccountKey` ou `SasKey` para sua conta de armazenamento. Para obter informações sobre como obter essas chaves, confira [Gerenciar chaves de acesso da conta de armazenamento](../storage/common/storage-account-keys-manage.md) ou [Uso de assinaturas de acesso compartilhado (SAS)](../storage/common/storage-sas-overview.md). Para obter mais informações sobre o uso do blobfuse, confira as [Perguntas frequentes sobre solução de problemas](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ) do blobfuse. Para obter acesso padrão ao diretório montado do blobfuse, execute a tarefa como **Administrador**. O blobfuse monta o diretório no espaço do usuário e, na criação do pool, é montado como raiz. No Linux, todas as tarefas de **Administrador** são raiz. Todas as opções do módulo FUSE são descritas na [página de referência do FUSE](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Além do guia de solução de problemas, os problemas do GitHub no repositório blobfuse são uma maneira útil de verificar os problemas e as resoluções atuais do blobfuse. Para obter mais informações, confira [problemas do blobfuse](https://github.com/Azure/azure-storage-fuse/issues).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>NFS

O NFS (sistema de arquivos de rede) também pode ser montado nos nós de pool, permitindo que os sistemas de arquivos tradicionais sejam acessados facilmente pelos nós do Lote do Azure. Pode ser um único servidor NFS implantado na nuvem ou um servidor NFS local acessado em uma rede virtual. Como alternativa, aproveite a solução de cache distribuído na memória do [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md), que fornece conectividade contínua ao armazenamento local, lendo dados sob demanda no cache e oferece alto desempenho e escalabilidade para nós de computação em nuvem.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Sistema de arquivos de Internet comum

O CIFS (sistema de arquivos de Internet comum) também pode ser montado nos nós de pool, permitindo que os sistemas de arquivos tradicionais sejam acessados facilmente pelos nós do Lote do Azure. O CIFS é um protocolo de compartilhamento de arquivos que fornece um mecanismo de plataforma cruzada e aberta para solicitar serviços e arquivos do servidor de rede. O CIFS é baseado na versão aprimorada do protocolo SMB da Microsoft para compartilhamento de arquivos na Internet e intranet, além de ser usado para montar sistemas de arquivos externos nos nós do Windows. Para saber mais sobre o SMB, confira [Servidor de arquivos e SMB](/windows-server/storage/file-server/file-server-smb-overview).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnosticar erros de montagem

Se uma configuração de montagem falhar, o nó de computação no pool falhará e o estado do nó se tornará inutilizável. Para diagnosticar uma falha na configuração de montagem, inspecione a propriedade [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) para obter detalhes sobre o erro.

Para obter os arquivos de log para depuração, use [OutputFiles](batch-task-output-files.md) para carregar os arquivos de `*.log`. Os arquivos de `*.log` contêm informações sobre a montagem do sistema de arquivos no local `AZ_BATCH_NODE_MOUNTS_DIR`. Os arquivos de log de montagem têm o formato: `<type>-<mountDirOrDrive>.log` para cada montagem. Por exemplo, uma montagem `cifs` em um diretório de montagem chamado `test` terá um arquivo de log de montagem chamado: `cifs-test.log`.

## <a name="supported-skus"></a>SKUs com suporte

| Publicador | Oferta | SKU | Compartilhamento de Arquivos do Azure | Blobfuse | Montagem de NFS | Montagem de CIFS |
|---|---|---|---|---|---|---|
| lote | rendering-centos73 | renderização | :heavy_check_mark: <br>Observação: Compatível com CentOS 7.7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canônico | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>Observação: Compatível com CentOS 7.4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>Observação: Compatível com armazenamento de A_8 ou 9</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais detalhes sobre a montagem de um compartilhamento de Arquivos do Azure com o [Windows](../storage/files/storage-how-to-use-files-windows.md) ou o [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Saiba mais sobre como usar e montar os sistemas de arquivos virtuais do [blobfuse](https://github.com/Azure/azure-storage-fuse).
- Confira [Visão geral do sistema de arquivos de rede](/windows-server/storage/nfs/nfs-overview) para saber mais sobre o NFS e seus aplicativos.
- Confira [Protocolo SMB da Microsoft e visão geral do protocolo CIFS](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) para saber mais sobre o CIFS.
