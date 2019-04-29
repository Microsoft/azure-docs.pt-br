---
title: Como implantar Arquivos do Azure | Microsoft Docs
description: Saiba como implantar Arquivos do Azure do início ao fim.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6afe54d269d273c6a93e6431e9f1c1af7b18cc0e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766420"
---
# <a name="how-to-deploy-azure-files"></a>Como implantar Arquivos do Azure
O [Arquivos do Azure](storage-files-introduction.md) oferece compartilhamentos de arquivos totalmente gerenciados na nuvem, acessíveis por meio do protocolo SMB padrão no setor. Este artigo mostra como implantar Arquivos do Azure de maneira prática dentro de sua organização.

É altamente recomendável ler [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md) antes de seguir as etapas neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha completado as seguintes etapas:

- Criado uma conta de Armazenamento do Azure com as opções de resiliência e de criptografia desejadas, na região desejada. Para orientações passo a passo sobre como criar uma conta de armazenamento, consulte [Criar uma conta de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- Criado um compartilhamento de arquivos do Azure com sua cota desejada na sua conta de armazenamento. Consulte [Criar um compartilhamento de arquivos](storage-how-to-create-file-share.md) para obter instruções passo a passo sobre como criar um compartilhamento de arquivos.

## <a name="transfer-data-into-azure-files"></a>Transferir dados para Arquivos do Azure
Talvez você queira migrar compartilhamentos de arquivos existentes, tais como aqueles armazenados localmente, para o novo compartilhamento de Arquivos do Azure. Esta seção mostrará como mover dados em um arquivo do Azure por meio de vários métodos populares detalhados no [guia de planejamento](storage-files-planning.md#data-transfer-method)

### <a name="azure-file-sync"></a>Sincronização de Arquivos do Azure
A Sincronização de Arquivos do Azure permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. Ele faz isso transformando Windows Servers em um cache rápido do seu compartilhamento de Arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter todos os caches de que precisar ao redor do mundo.

A Sincronização de Arquivos do Azure pode ser usada para migrar dados para um compartilhamento de Arquivos do Azure, mesmo que o mecanismo de sincronização não seja desejável para uso de longo prazo. Mais informações sobre como usar a Sincronização de Arquivos do Azure para transferir dados em um compartilhamento de arquivos do Azure podem ser encontradas em [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Importação/Exportação do Azure
O serviço de importação/exportação do Azure permite a transferência de grandes quantidades de dados com segurança em um compartilhamento de arquivos do Azure pelo envio de discos rígidos para um datacenter do Azure. Consulte [Usar o serviço de Importação/Exportação do Microsoft Azure para transferir dados para o Armazenamento do Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter uma visão geral mais detalhada do serviço.

> [!Note]  
> O serviço de Importação/Exportação do Azure não dá suporte à exportação de arquivos de um compartilhamento de Arquivos do Azure no momento.

As etapas a seguir importarão dados de uma localização local para o compartilhamento de Arquivos do Azure.

1. Adquira o número necessário de discos rígidos para email no Azure. Os discos rígidos podem ter qualquer capacidade, mas devem ser um SSD ou HD de 2,5" ou 3,5", com suporte ao padrão SATA II ou SATA III. 

2. Conecte-se e monte cada disco no servidor/PC realizando a transferência de dados. Para otimizar o desempenho, é recomendável que o trabalho de exportação local seja executado localmente no servidor que contém os dados. Em alguns casos, assim como quando o servidor de arquivos que serve os dados é um dispositivo NAS, isso pode não ser possível. Nesse caso, é perfeitamente aceitável montar cada disco em um computador.

3. Verifique se cada unidade está online, inicializada e com uma letra da unidade atribuída a ela. Para colocar uma unidade online, inicializá-la e atribuir uma letra da unidade a ela, abra o snap-in do MMC de gerenciamento de disco (diskmgmt.msc).

    - Para colocar um disco online (se ainda não estiver online), clique com o botão direito do mouse no disco no painel inferior do MMC de Gerenciamento de Disco e selecione "Online".
    - Para inicializar um disco, clique com o botão direito do mouse no disco no painel inferior (depois que o disco estiver online) e selecione "Inicializar". Quando solicitado, verifique se a opção "GPT" foi selecionada.

        ![Uma captura de tela do menu Inicializar Disco no MMC de Gerenciamento de Disco](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Para atribuir uma letra da unidade ao disco, clique com o botão direito do mouse no espaço "não alocado" do disco online e inicializado e clique em "Novo Volume Simples". Isso permitirá que você atribua a letra da unidade. Observe que não é necessário formatar o volume, já que isso será feito mais tarde.

        ![Uma captura de tela do assistente de Novo Volume Simples no MMC de Gerenciamento de Disco](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Crie o arquivo CSV de conjunto de dados. O arquivo CSV de conjunto de dados é um mapeamento entre o caminho para os dados locais e o compartilhamento de Arquivos do Azure desejado para o qual os dados devem ser copiados. Por exemplo, o arquivo CSV de conjunto de dados a seguir mapeia um compartilhamento de arquivos local ("F:\shares\scratch") para um compartilhamento de Arquivos do Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Vários compartilhamentos com uma conta de armazenamento podem ser especificados. Consulte [Preparar o arquivo CSV de conjunto de dados](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file) para obter mais informações.

5. Crie o arquivo CSV de conjunto de unidades. O arquivo CSV de conjunto de unidades lista os discos disponíveis para o agente de exportação local. Por exemplo, o arquivo CSV de conjunto de unidades a seguir lista as unidades `X:`, `Y:` e `Z:` para serem usadas no trabalho de exportação local:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Consulte [Preparar o arquivo CSV de conjunto de unidades](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file) para obter mais informações.

6. Use a [ferramenta WAImportExport](https://www.microsoft.com/download/details.aspx?id=55280) para copiar seus dados para um ou mais discos rígidos.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Não modifique os dados em unidades de disco rígido ou o arquivo de diário depois de concluir a preparação do disco.

7. [Crie um trabalho de importação](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy é uma ferramenta de cópia bem conhecida que é fornecida com o Windows e o Windows Server. Robocopy pode ser usado para transferir dados para arquivos do Azure montando o compartilhamento de arquivos localmente e, em seguida, usando a localização montada como o destino no comando Robocopy. Usar o Robocopy é bastante simples:

1. [Monte o compartilhamento de Arquivos do Azure](storage-how-to-use-files-windows.md). Para otimizar o desempenho, é recomendável a montagem do compartilhamento de arquivos do Azure localmente no servidor que contém os dados. Em alguns casos, assim como quando o servidor de arquivos que serve os dados é um dispositivo NAS, isso pode não ser possível. Nesse caso, é perfeitamente aceitável para montar o compartilhamento de arquivos do Azure em um PC. Neste exemplo, `net use` é usado na linha de comando para montar o compartilhamento de arquivos:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Use `robocopy` na linha de comando para mover dados para o Azure file Sync - Sincronização de arquivos do Azure:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    O Robocopy tem um número significativo de opções para modificar o comportamento de cópia conforme desejado. Para obter mais informações, exiba a página de manual do [Robocopy](https://technet.microsoft.com/library/cc733145.aspx).

### <a name="azcopy"></a>AzCopy
O AzCopy é um utilitário de linha de comando projetado para copiar dados de e para os Arquivos do Azure e o Armazenamento de Blobs do Azure, usando comandos simples com o desempenho ideal. É fácil usar o AzCopy:

1. Baixe a [versão mais recente do AzCopy no Windows](https://aka.ms/downloadazcopy) ou [Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#download-and-install-azcopy).
2. Use `azcopy` na linha de comando para mover dados para o compartilhamento de Arquivos do Azure. A sintaxe no Windows é conforme a seguir: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    No Linux, a sintaxe do comando é um pouco diferente:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    O AzCopy tem um número significativo de opções para modificar o comportamento de cópia conforme desejado. Para obter mais informações, exiba [AzCopy no Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) e [AzCopy no Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Montar automaticamente em computadores/servidores necessários
Para substituir um compartilhamento de arquivos local, vale a pena montar previamente os compartilhamentos nos computadores em que ele será usado. Isso pode ser feito automaticamente em uma lista de computadores.

> [!Note]  
> Montar um compartilhamento de Arquivos do Azure requer o uso da chave de conta de armazenamento como a senha, portanto, recomendamos a montagem apenas em ambientes confiáveis. 

### <a name="windows"></a> Windows
O PowerShell pode ser usado para executar o comando de montagem em vários computadores. No exemplo a seguir, `$computers` é populado manualmente, mas você pode gerar a lista de computadores para montagem automática. Por exemplo, você pode popular essa variável com resultados do Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Um script do bash simples combinado com SSH pode gerar o mesmo resultado no exemplo a seguir. A variável `$computer` é similarmente deixada para ser populada pelo usuário:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Próximas etapas
- [Planejar uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Solucionar problemas dos Arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Solucionar problemas dos Arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
