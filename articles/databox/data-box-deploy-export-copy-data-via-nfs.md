---
title: Tutorial para copiar dados do Azure Data Box por meio do NFS | Microsoft Docs
description: Saiba como copiar dados do Azure Data Box por meio do NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 64bb5e94c4b18626d1f85d7e61252aae74202eb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680626"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-nfs"></a>Tutorial: Copiar dados do Azure Data Box por meio do NFS

Este tutorial descreve como conectar-se e copiar dados da IU da Web local do Data Box para um servidor de dados local via NFS. Os dados no Data Box são exportados da conta de Armazenamento do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Conectar-se à caixa de dados
> * Copiar dados do Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você realizou o pedido para o Azure Data Box.
    - Para uma ordem de importação, confira [Tutorial: Solicitar o Azure Data Box](data-box-deploy-ordered.md).
    - Para uma ordem de exportação, confira [Tutorial: Solicitar o Azure Data Box](data-box-deploy-export-ordered.md).
2. Você recebeu seu Data Box e o status do pedido no portal está como **Entregue**.
3. Você tem um computador host para o qual você deseja copiar os dados do Data Box. O computador host deve
   * Executar um [Sistema operacional com suporte](data-box-system-requirements.md).
   * Estar conectado a uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se não houver uma conexão de 10 GbE disponível, use um link de dados de 1 GbE, mas as velocidades de cópia serão afetadas.

## <a name="connect-to-data-box"></a>Conectar-se à caixa de dados

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Se você estiver usando um computador host Linux, execute as seguintes etapas para configurar o Data Box para permitir o acesso aos clientes NFS. O Data Box pode conectar até cinco clientes NFS por vez.

1. Forneça os endereços IP dos clientes permitidos que podem acessar o compartilhamento:

    1.  Na IU da Web local, acesse a página **Conectar e copiar**. Sob **as configurações de NFS**, clique em **acesso para cliente NFS**. 

        ![Abrir o acesso ao cliente NFS](media/data-box-deploy-export-copy-data/nfs-client-access-1.png)

    1. Para adicionar um cliente NFS, forneça o endereço IP do cliente e clique em **Adicionar**. O Data Box pode conectar até cinco clientes NFS por vez. Quando terminar, clique em **OK**.

         ![Adicionar um cliente NFS](media/data-box-deploy-export-copy-data/nfs-client-access-2.png)

2. Assegure-se de que o computador host Linux tenha uma [versão suportada](data-box-system-requirements.md) do cliente NFS instalado. Use a versão específica para sua distribuição do Linux. 

3. Depois que o cliente NFS for instalado, use o seguinte comando para montar o compartilhamento NFS em seu dispositivo Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    O exemplo a seguir mostra como se conectar via NFS a um compartilhamento Caixa de Dados. O IP do dispositivo Data Box é `10.161.23.130`, o compartilhamento `Mystoracct_Blob` é montado no ubuntuVM, sendo o ponto de montagem`/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Para clientes Mac, você precisará adicionar uma outra opção da seguinte maneira: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Sempre crie uma pasta para os arquivos que você pretende copiar no compartilhamento e, em seguida, copie os arquivos para a pasta**. A pasta criada nos compartilhamentos de blob de blocos e de blob de páginas representa um contêiner no qual os dados são carregados como blobs. Não é possível copiar arquivos diretamente para a pasta *raiz* na conta de armazenamento.

## <a name="copy-data-from-data-box"></a>Copiar dados do Data Box

Quando você estiver conectado aos compartilhamentos do Data Box, a próxima etapa será copiar dados.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]

 Agora você pode iniciar a cópia de dados. Se você estiver usando um computador host Linux, use um utilitário de cópia semelhante ao Robocopy. Algumas das alternativas disponíveis no Linux são o [`rsync`](https://rsync.samba.org/), o [FreeFileSync](https://www.freefilesync.org/), o [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) ou o [Ultracopier](https://ultracopier.first-world.info/).  

O comando `cp` é uma das melhores opções para copiar um diretório. Para mais informações sobre o uso, vá para [cp man pages](http://man7.org/linux/man-pages/man1/cp.1.html).

Se você usar a opção `rsync` para obter uma cópia com multithread, siga estas diretrizes:

* Instale o pacote **CIFS Utils** ou **NFS Utils**, dependendo do sistema de arquivos usado pelo seu cliente Linux.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Instale o `rsync` e o **Parallel** (varia dependendo da versão distribuída do Linux).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* Crie um ponto de montagem.

    `sudo mkdir /mnt/databox`

* Monte o volume.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* Espelhe a estrutura de pastas.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* Copie arquivos.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     onde j especifica o número de paralelização, X = número de cópias paralelas

     Recomendamos que você inicie com 16 cópias paralelas e aumente o número de segmentos, dependendo dos recursos disponíveis.

> [!IMPORTANT]
> Não há suporte para os seguintes tipos de arquivo do Linux: links simbólicos, arquivos de caracteres, arquivos de bloco, soquetes e pipes. Esses tipos de arquivo resultarão em falhas durante a etapa **Preparar para o envio**.

Quando a cópia estiver concluída, acesse o **Painel** e verifique o espaço usado e o espaço livre no dispositivo.

Agora você pode continuar a enviar o seu Data Box para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Conectar-se à caixa de dados
> * Copiar dados do Data Box

Avance para o próximo tutorial para saber como enviar o Data Box novamente à Microsoft.

> [!div class="nextstepaction"]
> [Envie o Azure Data Box para a Microsoft](./data-box-deploy-export-picked-up.md)
