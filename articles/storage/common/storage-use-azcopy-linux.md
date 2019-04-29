---
title: Copiar ou mover dados para o Armazenamento do Azure com o AzCopy no Linux | Microsoft Docs
description: Use o utilitário AzCopy no Linux para mover ou copiar dados entre o conteúdo de blob e de arquivo. Copie dados para o Armazenamento do Azure de arquivos locais ou copie dados dentro na mesma conta ou entre contas de armazenamento. Migre facilmente seus dados para o Armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/26/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 0f87645537576f49ee04b823341acf8853798f88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730048"
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Transferir dados com o AzCopy no Linux

O AzCopy é um utilitário de linha de comando projetado para copiar dados de/para o Armazenamento de Arquivos e de Blobs do Microsoft Azure usando comandos simples projetados para um desempenho ideal. Você pode copiar dados entre um sistema de arquivos e uma conta de armazenamento, ou entre contas de armazenamento.  

Há duas versões do AzCopy que podem ser baixadas. O AzCopy no Linux destina-se a plataformas Linux que oferecem opções de linha de comando de estilo POSIX. [AzCopy no Windows](../storage-use-azcopy.md) oferece opções de linha de comando de estilo do Windows. Este artigo aborda o AzCopy no Linux. 

> [!NOTE]  
> A partir da versão 7.2 do AzCopy, as dependências do .NET Core são empacotadas com o pacote AzCopy. Se você usar a versão 7.2 ou posterior, não precisará mais instalar o .NET Core como um pré-requisito.

## <a name="download-and-install-azcopy"></a>Baixar e instalar o AzCopy

### <a name="installation-on-linux"></a>Instalação no Linux

> [!NOTE]
> Talvez seja necessário instalar as dependências do .NET Core 2.1 destacadas neste [artigo de pré-requisitos do .NET Core](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x), dependendo da sua distribuição. 
>
> Para distribuições do RHEL 7, instale as dependências de ICU e libunwind: ```yum install -y libunwind icu```

A instalação do AzCopy no Linux (v7.2 ou posterior) é tão fácil quanto extrair um pacote tar e executar o script de instalação. 

**Distribuições baseadas em RHEL 6**: [baixar link](https://aka.ms/downloadazcopylinuxrhel6)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinuxrhel6
tar -xf azcopy.tar.gz
sudo ./install.sh
```

**Todas as outras distribuições do Linux**: [baixar link](https://aka.ms/downloadazcopylinux64)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Você poderá remover os arquivos extraídos depois que o AzCopy no Linux for instalado. Como alternativa, se você não tiver privilégios de superusuário, também poderá executar `azcopy` usando o script de shell azcopy na pasta extraída.

### <a name="alternative-installation-on-ubuntu"></a>Instalação alternativa no Ubuntu

**Ubuntu 14.04**

Adicionar fonte de apt ao repositório de produtos do Linux da Microsoft e instale o AzCopy:

```bash
sudo echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod/ trusty main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
sudo apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.04**

Adicionar fonte de apt ao repositório de produtos do Linux da Microsoft e instale o AzCopy:

```bash
echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod/ xenial main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
sudo apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

## <a name="writing-your-first-azcopy-command"></a>Como escrever seu primeiro comando do AzCopy
A sintaxe básica dos comandos do AzCopy é:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

Os exemplos a seguir demonstram vários cenários para cópia de dados entre os Blobs e os Arquivos do Microsoft Azure. Consulte o menu `azcopy --help` para obter uma explicação detalhada dos parâmetros usados em cada amostra.

## <a name="blob-download"></a>Blob: Baixar
### <a name="download-single-blob"></a>Baixar um único blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key> 
```

Se a pasta `/mnt/myfiles` não existir, o AzCopy a criará e baixará `abc.txt` na nova pasta. 

### <a name="download-single-blob-from-secondary-region"></a>Baixe um único blob de região secundária

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Observe que você deve ter um armazenamento com redundância geográfica e acesso de leitura habilitado.

### <a name="download-all-blobs"></a>Baixar todos os blobs

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Suponhamos que os seguintes blobs residam no contêiner especificado:  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

Após a operação de download, o diretório `/mnt/myfiles` inclui os seguintes arquivos:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Se você não especificar a opção `--recursive`, nenhum blob será baixado.

### <a name="download-blobs-with-specified-prefix"></a>Baixar blobs com prefixo especificado

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Suponhamos que os blobs a seguir residam no contêiner especificado. Todos os blobs que começam com o prefixo `a` são baixados.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

Após a operação de download, a pasta `/mnt/myfiles` inclui os seguintes arquivos:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

O prefixo se aplica ao diretório virtual, que forma a primeira parte do nome do blob. No exemplo mostrado acima, o diretório virtual não corresponde ao prefixo especificado e, portanto, nenhum blob é baixado. Além disso, se a opção `--recursive` não for especificada, o AzCopy não baixará nenhum blob.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Defina a hora da última modificação dos arquivos exportados como sendo a mesma dos blobs de origem

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Você também pode excluir blobs da operação de download com base na hora da última modificação Por exemplo, se você quiser excluir blobs cuja hora da última modificação for a mesma ou mais recente do que o arquivo de destino, adicione a opção `--exclude-newer`:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

Ou, se você quiser excluir blobs cuja hora da última modificação for a mesma ou mais antiga do que o arquivo de destino, adicione a opção `--exclude-older`:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Blob: Carregar
### <a name="upload-single-file"></a>Carregar um único arquivo

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --dest-key <key>
```

Se o contêiner de destino especificado não existir, o AzCopy o criará e carregará o arquivo nele.

### <a name="upload-single-file-to-virtual-directory"></a>Carregar arquivo único no diretório virtual

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/vd/abc.txt \
    --dest-key <key>
```

Se o diretório virtual especificado não existir, o AzCopy carregará o arquivo para incluir o diretório virtual no nome do blob (*por exemplo*, `vd/abc.txt` no exemplo acima).

### <a name="redirect-from-stdin"></a>Redirecionar do stdin

```azcopy
gzip myarchive.tar -c | azcopy \
    --destination https://myaccount.blob.core.windows.net/mycontainer/mydir/myarchive.tar.gz \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Carregar todos os arquivos

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

A especificação da opção `--recursive` carrega o conteúdo do diretório especificado no Armazenamento de blobs de maneira recursiva, o que significa que todas as subpastas e seus arquivos são carregados também. Por exemplo, suponhamos que os seguintes arquivos residam na pasta `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Após a operação de upload, o contêiner incluirá os seguintes arquivos:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Quando a opção `--recursive` não é especificada, somente os seguintes três arquivos são carregados:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>Carregar arquivos que correspondam ao padrão especificado

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

Vamos supor que a pasta `/mnt/myfiles`contenha os seguintes arquivos:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Após a operação de upload, o contêiner incluirá os seguintes arquivos:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Quando a opção `--recursive` não é especificada, o AzCopy ignora os arquivos que estão em subdiretórios:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Especificar o tipo de conteúdo MIME de um blob de destino
Por padrão, o AzCopy define o tipo de conteúdo de um blob de destino para `application/octet-stream`. No entanto, você pode especificar explicitamente o tipo de conteúdo por meio da opção `--set-content-type [content-type]`. Essa sintaxe define o tipo de conteúdo para todos os blobs em uma operação de carregamento.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Se a opção `--set-content-type` for especificada sem um valor, o AzCopy definirá o tipo de conteúdo de cada blob ou arquivo de acordo com sua extensão de arquivo.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

### <a name="customizing-the-mime-content-type-mapping"></a>Personalizar o mapeamento de tipo de conteúdo MIME
O AzCopy usa um arquivo de configuração que contém um mapeamento de extensão de arquivo para o tipo de conteúdo. É possível personalizar esse mapeamento e adicionar novos pares conforme necessário. O mapeamento está localizado em ```/usr/lib/azcopy/AzCopyConfig.json```

## <a name="blob-copy"></a>Blob: Copiar
### <a name="copy-single-blob-within-storage-account"></a>Copiar um único blob dentro da conta de armazenamento

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key> \
    --dest-key <key>
```

Quando você copia um blob sem a opção --sync-copy, uma operação de [cópia no servidor](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é executada.

### <a name="copy-single-blob-across-storage-accounts"></a>Copiar um único blob entre contas de armazenamento

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Quando você copia um blob sem a opção --sync-copy, uma operação de [cópia no servidor](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é executada.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Copiar um único blob de região secundária para região primária

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1/abc.txt \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Observe que você deve ter um armazenamento com redundância geográfica e acesso de leitura habilitado.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Copiar um único blob e seus instantâneos entre contas de armazenamento

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/ \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

Após a operação de cópia, o contêiner de destino incluirá o blob e seus instantâneos. O contêiner inclui o seguinte blob e seus instantâneos:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Copiar blobs em regiões entre contas de armazenamento de forma síncrona.
O AzCopy por padrão copia dados entre dois pontos de extremidade de armazenamento assincronamente. Portanto, a operação de cópia é executada em segundo plano usando a capacidade de largura de banda extra, que não tem nenhum SLA em termos da velocidade de cópia de um blob. 

A opção `--sync-copy` garante que a operação de cópia obtém uma velocidade consistente. O AzCopy realiza a cópia síncrona baixando os blobs para copiar da fonte especificada para a memória local, e, em seguida, carregá-los para o destino de armazenamento de Blob.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy` poderá gerar custos de saída adicionais, comparado à cópia assíncrona. A abordagem recomendada é usar essa opção em uma VM do Azure que está na mesma região de sua conta de armazenamento de origem, para evitar o custo de saída.

## <a name="file-download"></a>Arquivo: Baixar
### <a name="download-single-file"></a>Baixar um único arquivo

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Se a origem especificada for um compartilhamento de arquivos do Azure, você deverá especificar o nome exato do arquivo, (*por exemplo*, `abc.txt`) para baixar um único arquivo ou especificar a opção `--recursive` para baixar todos os arquivos do compartilhamento de maneira recursiva. A tentativa de especificar um padrão de arquivo e a opção `--recursive` simultaneamente resulta em um erro.

### <a name="download-all-files"></a>Baixar todos os arquivos

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Observe que as pastas vazias não são baixadas.

## <a name="file-upload"></a>Arquivo: Carregar
### <a name="upload-single-file"></a>Carregar um único arquivo

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.file.core.windows.net/myfileshare/abc.txt \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Carregar todos os arquivos

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Observe que as pastas vazias não são carregadas.

### <a name="upload-files-matching-specified-pattern"></a>Carregar arquivos que correspondam ao padrão especificado

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Arquivo: Copiar
### <a name="copy-across-file-shares"></a>Copiar entre compartilhamentos de arquivos

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Quando você copia um arquivo entre compartilhamentos de arquivos, é executada uma operação [server-side copy](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-from-file-share-to-blob"></a>Copiar de compartilhamento de arquivos para blob

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Quando você copia um arquivo do compartilhamento de arquivos no blob, é executada uma operação [server-side copy](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-from-blob-to-file-share"></a>Copiar do blob para compartilhamento de arquivos

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Quando você copia um arquivo do blob no compartilhamentos de arquivos, é executada uma operação [server-side copy](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="synchronously-copy-files"></a>Copiar arquivos de forma síncrona
Especifique a opção `--sync-copy` para copiar dados do Armazenamento de Arquivos para o Armazenamento de Arquivos, do Armazenamento de Arquivos para o Armazenamento de Blobs e do Armazenamento de Blobs para o Armazenamento de Arquivos de forma síncrona. O AzCopy executa esta operação baixando os dados de origem na memória local e, em seguida, carregando-os para o destino. Nesse caso, se aplica o custo de saída padrão.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

Durante a cópia do Armazenamento de Arquivos para o Armazenamento de Blobs, o tipo de blob padrão é o blob de blocos, e o usuário pode especificar a opção `--blob-type page` para alterar o tipo de blob de destino. Os tipos disponíveis são `page | block | append`.

Observe que `--sync-copy` pode gerar custos de saída adicionais, comparado à cópia assíncrona. A abordagem recomendada é usar essa opção em uma VM do Azure que está na mesma região de sua conta de armazenamento de origem, para evitar o custo de saída.

## <a name="other-azcopy-features"></a>Outros recursos do AzCopy
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Copiar apenas os dados que não existem no destino
Os parâmetros `--exclude-older` e `--exclude-newer` permitem a exclusão de recursos de origem mais antigos ou mais recentes da cópia, respectivamente. Se você quiser copiar apenas os recursos de origem que não existem no destino, especifique os dois parâmetros no comando AzCopy:

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Usar um arquivo de configuração para especificar parâmetros de linha de comando

```azcopy
azcopy --config-file "azcopy-config.ini"
```

É possível incluir qualquer parâmetro de linha de comando do AzCopy em um arquivo de configuração. O AzCopy processa os parâmetros no arquivo como se eles tivessem sido especificados na linha de comando, realizando uma substituição direta pelo conteúdo do arquivo.

Suponha um arquivo de configuração chamado `copyoperation`, que contém as linhas a seguir. Cada parâmetro do AzCopy pode ser especificado em uma única linha.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

ou, em linhas separadas:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

O AzCopy falhará se você dividir o parâmetro em duas linhas, conforme mostrado aqui para o parâmetro `--source-key`:

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>Especificar uma SAS

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-sas <SAS1> \
    --dest-sas <SAS2>
```

Você também pode especificar um SAS no URI do contêiner:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

### <a name="journal-file-folder"></a>Pasta de arquivo de diário
Sempre que você emite um comando para o AzCopy, ele verifica se um arquivo de diário existe na pasta padrão ou se está em uma pasta especificada por meio dessa opção. Se o arquivo de diário não estiver em nenhum dos lugares, o AzCopy tratará a operação como nova e gerar um novo arquivo de diário.

Se o arquivo de diário existir, o AzCopy verificará se a linha de comando inserida corresponde à linha de comando no arquivo de diário. Se as duas linhas de comando forem correspondentes, o AzCopy retomará a operação incompleta. Se elas não forem correspondentes, o AzCopy solicitará que o usuário substitua o arquivo de diário para iniciar uma nova operação ou cancele a operação atual.

Se você quiser usar o local padrão para o arquivo de diário:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Se você omitir a opção `--resume` ou especificar a opção `--resume` sem o caminho da pasta, conforme mostrado acima, o AzCopy criará o arquivo de diário no local padrão, que é `~\Microsoft\Azure\AzCopy`. Se o arquivo de diário já existir, o AzCopy retomará a operação com base no arquivo de diário.

Se você quiser especificar um local personalizado para o arquivo de diário:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

Este exemplo criará o arquivo de diário se ele ainda não existir. Se ele existir, o AzCopy retomará a operação com base no arquivo de diário.

Se desejar retomar uma operação do AzCopy, repita o mesmo comando. Em seguida, o AzCopy no Linux solicitará a confirmação:

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Logs detalhados de saída

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Especificar o número de operações simultâneas para começar
A opção `--parallel-level` especifica o número de operações de cópia simultâneas. Por padrão, o AzCopy inicia uma determinada quantidade de operações simultâneas para aumentar a taxa de transferência de dados. O número de operações simultâneas é igual a oito vezes o número de processadores que você tem. Se você estiver executando o AzCopy em uma rede de baixa largura de banda, poderá especificar um número menor para o nível --parallel-, a fim de evitar uma falha causada pela competição de recursos.

>[!TIP]
>Para exibir a lista completa de parâmetros do AzCopy, confira o menu “azcopy --help”.

## <a name="installation-steps-for-azcopy-71-and-earlier-versions"></a>Etapas de instalação para o AzCopy 7.1 e versões anteriores

O AzCopy no Linux (apenas v7.1 e anterior) requer o framework do .NET Core. As instruções de instalação estão disponíveis na página [instalação do .NET Core](https://www.microsoft.com/net/core#linuxubuntu).

Por exemplo, comece instalando o .NET Core no Ubuntu 16.10. Para obter o último guia de instalação, visite a página de instalação do [.NET Core no Linux](https://www.microsoft.com/net/core#linuxubuntu).


```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-sdk-2.0.0
```

Depois de instalar o .NET Core, baixe e instale o AzCopy.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Você poderá remover os arquivos extraídos depois que o AzCopy no Linux for instalado. Como alternativa, se você não tiver privilégios de superusuário, também poderá executar `azcopy` usando o script de shell azcopy na pasta extraída.

## <a name="known-issues-and-best-practices"></a>Problemas conhecidos e melhores práticas
### <a name="error-installing-azcopy"></a>Erro ao instalar o AzCopy
Caso tenha problemas com a instalação do AzCopy, tente executar o AzCopy usando o script bash na pasta `azcopy` extraída.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Limite gravações simultâneas durante a cópia de dados
Ao copiar blobs ou arquivos usando o AZCopy, lembre-se de que outro aplicativo pode estar modificando os dados enquanto você os copia. Se possível, verifique se os dados que está copiando não estão sendo modificados durante a cópia. Por exemplo, ao copiar um VHD associado a uma máquina virtual do Azure, verifique se nenhum outro aplicativo está gravando no VHD, no momento. Uma boa maneira de fazer isso é ceder o recurso para ser copiado. Também é possível criar um instantâneo do VHD primeiro e, em seguida, copiar o instantâneo.

Se não for possível evitar que outros aplicativos gravem em blobs ou arquivos enquanto são copiados, lembre-se que, quando o trabalho terminar, os recursos copiados não poderão mais ter paridade total com os recursos de origem.

### <a name="running-multiple-azcopy-processes"></a>Executar vários processos AzCopy
É possível executar vários processos AzCopy em um único cliente, desde que você use pastas de diário diferentes. Não há suporte para o uso de uma única pasta de diário para vários processos do AzCopy.

1º processo:
```azcopy
azcopy \
    --source /mnt/myfiles1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles1 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal1"
```

2º processo:
```azcopy
azcopy \
    --source /mnt/myfiles2 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles2 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal2"
```

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o Armazenamento do Azure e o AzCopy, consulte os seguintes recursos:

### <a name="azure-storage-documentation"></a>Documentação do Armazenamento do Azure:
* [Introdução ao Armazenamento do Azure](../storage-introduction.md)
* [Criar uma conta de armazenamento](../storage-create-storage-account.md)
* [Gerenciar blobs com o Gerenciador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)
* [Usando a CLI do Azure com o Armazenamento do Azure](../storage-azure-cli.md)
* [Como usar o armazenamento de Blob por meio do C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como usar o Armazenamento de Blob do Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Como usar o armazenamento de Blob do Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Como usar o armazenamento de Blob no Python](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Postagens de blog de armazenamento do Azure:
* [Anunciando a Versão Prévia do AzCopy no Linux](https://azure.microsoft.com/blog/announcing-azcopy-on-linux-preview/)
* [Apresentando a versão de visualização da biblioteca de movimentação de dados do armazenamento do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introducing synchronous copy and customized content type (AzCopy: introdução a cópia síncrona e tipo de conteúdo personalizado)](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Announcing General Availability of AzCopy 3.0 plus preview release of AzCopy 4.0 with Table and File support (AzCopy: anúncio de disponibilidade geral do AzCopy 3.0 mais versão de teste do AzCopy 4.0 com suporte para Tabela e Arquivo)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimized for Large-Scale Copy Scenarios (AzCopy: otimizado para cenários de cópia em larga escala)](https://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Support for read-access geo-redundant storage (AzCopy: suporte para o armazenamento com redundância geográfica com acesso de leitura)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Transfer data with re-startable mode and SAS token (AzCopy: transferir dados com o modo reiniciável e token de SAS)](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Using cross-account Copy Blob (AzCopy: usando blob de cópia em várias contas)](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Uploading/downloading files for Azure Blobs (AzCopy: Upload/download de arquivos para Blobs do Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
