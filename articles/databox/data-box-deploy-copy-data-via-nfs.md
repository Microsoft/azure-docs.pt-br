---
title: Tutorial para copiar dados para o Azure Data Box por meio do NFS | Microsoft Docs
description: Saiba como copiar dados para o Azure Data Box por meio do NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: c74ed93383ea880900a5428a6f24b5b44a3ff135
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443144"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Tutorial: Copiar dados para o Azure Data Box por meio do NFS

Este tutorial descreve como conectar-se e copiar dados do computador host usando a IU da Web local.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Pré-requisitos
> * Conectar-se à caixa de dados
> * Copiar dados para caixa de dados

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você concluiu o [Tutorial: Configurar o Azure Data Box](data-box-deploy-set-up.md).
2. Você recebeu seu Data Box e o status do pedido no portal está como **Entregue**.
3. Você tem um computador host que tem os dados que você deseja copiar para o Data Box. O computador host deve
    - Executar um [Sistema operacional com suporte](data-box-system-requirements.md).
    - Estar conectado a uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se uma conexão de 10 GbE não estiver disponível, um link de dados de 1 GbE poderá ser usado, mas as velocidades de cópia serão afetadas. 

## <a name="connect-to-data-box"></a>Conectar-se à caixa de dados

Com base na conta de armazenamento selecionada, o Data Box cria até:
- Três compartilhamentos para cada conta de armazenamento associada para GPv1 e GPv2.
- Um compartilhamento para armazenamento premium. 
- Um compartilhamento para conta do Armazenamento de Blobs. 

Em compartilhamentos de blob de blocos e blob de páginas, as entidades de primeiro nível são contêineres e as entidades de segundo nível são blobs. Em compartilhamentos de Arquivos do Azure, as entidades de primeiro nível são compartilhamentos e entidades de segundo nível são arquivos.

A tabela a seguir mostra o caminho UNC para os compartilhamentos na URL de caminho do Data Box e do Armazenamento do Azure em que os dados são carregados. A URL final de caminho do Armazenamento do Azure pode ser derivada do caminho de compartilhamento UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de blocos do Azure | <li>Caminho UNC para compartilhamentos: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para compartilhamentos: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Arquivos do Azure       |<li>Caminho UNC para compartilhamentos: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Se você estiver usando um computador host Linux, execute as seguintes etapas para configurar o Data Box para permitir o acesso aos clientes NFS.

1. Forneça os endereços IP dos clientes permitidos que podem acessar o compartilhamento. Na interface do usuário da web local, acesse **Conectar e copiar** a página. Sob **as configurações de NFS**, clique em **acesso para cliente NFS**. 

    ![Configurar o acesso de cliente NFS 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Forneça o endereço IP do cliente NFS e clique em **Add**. Você pode configurar o acesso para vários clientes NFS repetindo essa etapa. Clique em **OK**.

    ![Configurar o acesso do cliente NFS 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Assegure-se de que o computador host Linux tenha uma [versão suportada](data-box-system-requirements.md) do cliente NFS instalado. Use a versão específica para sua distribuição do Linux. 

3. Depois que o cliente NFS for instalado, use o seguinte comando para montar o compartilhamento NFS em seu dispositivo Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    O exemplo a seguir mostra como se conectar via NFS a um compartilhamento Caixa de Dados. O IP do dispositivo Data Box é `10.161.23.130`, o compartilhamento `Mystoracct_Blob` é montado no ubuntuVM, sendo o ponto de montagem`/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Para clientes Mac, você precisará adicionar uma outra opção da seguinte maneira: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Sempre crie uma pasta para os arquivos que você pretende copiar no compartilhamento e, em seguida, copie os arquivos para a pasta**. A pasta criada nos compartilhamentos de blob de blocos e de blob de páginas representa um contêiner no qual os dados são carregados como blobs. Não é possível copiar arquivos diretamente para a pasta *raiz* na conta de armazenamento.

## <a name="copy-data-to-data-box"></a>Copiar dados para caixa de dados

Quando você estiver conectado aos compartilhamentos de caixa de dados, a próxima etapa é copiar dados. Antes de começar a cópia de dados, examine as considerações a seguir:

- Assegure-se de copiar os dados para compartilhamentos que correspondam ao formato de dados apropriado. Por exemplo, copie os dados blob do bloco para o compartilhamento de blobs de bloco. Copie VHDs para blobs de páginas. Se o formato de dados não corresponder ao tipo de compartilhamento apropriado, em uma etapa posterior, o upload de dados para o Azure falhará.
-  Ao copiar dados, verifique se o tamanho dos dados está de acordo com os limites de tamanho descritos nos [armazenamento do Azure e nos limites da Caixa de Dados](data-box-limits.md). 
- Se os dados, que estão sendo carregados pelo Data Box, forem carregados simultaneamente por outros aplicativos fora do Data Box, isso poderá resultar em falhas de trabalho de upload e corrupção de dados.
- Recomendamos que você não use o SMB e o NFS simultaneamente ou copie os mesmos dados para o mesmo destino final no Azure. Em tais casos, o resultado final não pode ser determinado.
- **Sempre crie uma pasta para os arquivos que você pretende copiar no compartilhamento e, em seguida, copie os arquivos para a pasta**. A pasta criada nos compartilhamentos de blob de blocos e de blob de páginas representa um contêiner no qual os dados são carregados como blobs. Não é possível copiar arquivos diretamente para a pasta *raiz* na conta de armazenamento.
- Em caso de ingestão de diretório e nomes de arquivos que diferenciam maiúsculas de minúsculas de um compartilhamento NFS para NFS em Data Box: 
    - O diferença entre maiúsculas e minúsculas é preservada no nome.
    - Os arquivo não diferenciam maiúsculas de minúsculas.
    
    Por exemplo, ao copiar `SampleFile.txt` e `Samplefile.Txt`, a diferença será preservada no nome quando copiada para a Data Box, mas o segundo arquivo substituirá o primeiro, pois são considerados o mesmo arquivo.


Se você estiver usando um computador host Linux, use um utilitário de cópia semelhante ao Robocopy. Algumas das alternativas disponíveis no Linux são [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) ou [Ultracopier](https://ultracopier.first-world.info/).  

O comando `cp` é uma das melhores opções para copiar um diretório. Para mais informações sobre o uso, vá para [cp man pages](http://man7.org/linux/man-pages/man1/cp.1.html).

Se usar a opção de rsync para obter uma cópia com multithread, siga estas diretrizes:

 - Instale o pacote **CIFS Utils** ou **NFS Utils**, dependendo do sistema de arquivos usado pelo seu cliente Linux.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  Instale **Rsync** e **Paralelo** (varia dependendo da versão distribuída do Linux).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - Crie um ponto de montagem.

    `sudo mkdir /mnt/databox`

 - Monte o volume.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

 - Espelhe a estrutura de pastas.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

 - Copie arquivos. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     onde j especifica o número de paralelização, X = número de cópias paralelas

     Recomendamos que você inicie com 16 cópias paralelas e aumente o número de segmentos, dependendo dos recursos disponíveis.

> [!IMPORTANT]
> Não há suporte para os seguintes tipos de arquivo do Linux: links simbólicos, arquivos de caracteres, arquivos de bloco, soquetes e pipes. Esses tipos de arquivo resultarão em falhas durante a etapa **Preparar para o envio**.

Abra a pasta de destino para exibir e verificar os arquivos copiados. Se você tiver algum erro durante o processo de cópia, baixe os arquivos de erro para solução de problemas. Para obter mais informações, veja [Exibir logs de erros durante a cópia de dados para o Data Box](data-box-logs.md#view-error-log-during-data-copy). Para obter uma lista detalhada de erros durante a cópia de dados, veja [Solucionar problemas do Data Box](data-box-troubleshoot.md).

Para garantir a integridade dos dados, a soma de verificação é computada em linha à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço usado e o espaço livre no seu dispositivo.
    
   ![Verificar o espaço livre e usado no painel](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
> * Pré-requisitos
> * Conectar-se à caixa de dados
> * Copiar dados para caixa de dados


Avance para o próximo tutorial para saber como enviar o Data Box novamente à Microsoft.

> [!div class="nextstepaction"]
> [Envie o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)

