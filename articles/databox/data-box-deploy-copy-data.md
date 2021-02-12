---
title: Tutorial para copiar dados por meio de SMB no Azure Data Box | Microsoft Docs
description: Neste tutorial, aprenda a se conectar e copiar dados do seu computador host para o Azure Data Box usando o SMB com a IU da Web local.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/11/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: c68f76e56f49f055466f7332d7751ac468e034d8
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616732"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Tutorial: Copiar dados para o Azure Data Box por SMB

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box"></a>Copiar dados para o Azure Data Box

::: zone-end

::: zone target="docs"

Este tutorial descreve como conectar-se e copiar dados do computador host usando a IU da Web local.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Conectar-se à caixa de dados
> * Copiar dados para caixa de dados

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você concluiu o [Tutorial: Configurar o Azure Data Box](data-box-deploy-set-up.md).
2. Você recebeu seu Data Box e o status do pedido no portal está como **Entregue**.
3. Você tem um computador host que tem os dados que você deseja copiar para o Data Box. O computador host deve
   * Executar um [Sistema operacional com suporte](data-box-system-requirements.md).
   * Estar conectado a uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se não houver uma conexão de 10 GbE disponível, use um link de dados de 1 GbE, mas as velocidades de cópia serão afetadas.

## <a name="connect-to-data-box"></a>Conectar-se à caixa de dados

Com base na conta de armazenamento selecionada, o Data Box cria até:

* Três compartilhamentos para cada conta de armazenamento associada para GPv1 e GPv2.
* Um compartilhamento para armazenamento premium.
* Um compartilhamento para conta do Armazenamento de Blobs.

Em compartilhamentos de blob de blocos e blob de páginas, as entidades de primeiro nível são contêineres e as entidades de segundo nível são blobs. Em compartilhamentos de Arquivos do Azure, as entidades de primeiro nível são compartilhamentos e entidades de segundo nível são arquivos.

A tabela a seguir mostra o caminho UNC para os compartilhamentos na URL de caminho do Data Box e do Armazenamento do Azure em que os dados são carregados. A URL final de caminho do Armazenamento do Azure pode ser derivada do caminho de compartilhamento UNC.
 
|Tipos de Armazenamento do Azure  | Compartilhamentos do Data Box            |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de blocos do Azure | <li>Caminho UNC para compartilhamentos: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para compartilhamentos: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Arquivos do Azure       |<li>Caminho UNC para compartilhamentos: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Se estiver usando um computador host do Windows Server, siga estas etapas para conectar-se ao Data Box.

1. O primeiro passo é autenticar e iniciar uma sessão. Vá para **conectar e copiar**. Selecione **SMB** para obter as credenciais de acesso aos compartilhamentos associados à sua conta de armazenamento. 

    ![Obter credenciais de compartilhamento para compartilhamentos SMB](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. No compartilhamento de acesso e caixa de diálogo de dados de cópia, copie o **nome de usuário** e o **senha** correspondente para o compartilhamento. Depois, selecione **OK**.
    
    ![Obter nome de usuário e senha para um compartilhamento](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Para acessar os compartilhamentos associados à sua conta de armazenamento (*utsac1* no exemplo a seguir) no computador host, abra uma janela Comando. No prompt de comando, digite:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Dependendo do formato dos dados, os caminhos de compartilhamento serão os seguintes:
    - Blob de blocos do Azure – `\\10.126.76.138\utSAC1_202006051000_BlockBlob`
    - Blob de páginas do Azure – `\\10.126.76.138\utSAC1_202006051000_PageBlob`
    - Arquivos do Azure – `\\10.126.76.138\utSAC1_202006051000_AzFile`

4. Quando solicitado, digite a senha do compartilhamento. Se a senha tiver caracteres especiais, adicione aspas duplas antes e depois dela. A amostra a seguir mostra a conexão a um compartilhamento por meio do comando anterior.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.138\utSAC1_202006051000_BlockBlob /u:testuser1
    Enter the password for 'testuser1' to connect to '10.126.76.138': "ab1c2def$3g45%6h7i&j8kl9012345"
    The command completed successfully.
    ```

4. Pressione Windows + R. Na janela **Executar**, especifique o `\\<device IP address>`. Selecione **OK** para abrir o Explorador de Arquivos.
    
    ![Conectar-se ao compartilhamento por meio do Explorador de Arquivos](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Agora você verá os compartilhamentos como pastas.
    
    ![Compartilhamentos mostrados no Explorador de Arquivos](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)

    **Sempre crie uma pasta para os arquivos que você pretende copiar no compartilhamento e, em seguida, copie os arquivos para a pasta**. A pasta criada nos compartilhamentos de blob de blocos e de blob de páginas representa um contêiner no qual os dados são carregados como blobs. Não é possível copiar arquivos diretamente para a pasta *raiz* na conta de armazenamento.
    
Se estiver usando um cliente Linux, use o seguinte comando para montar o compartilhamento SMB. O parâmetro "vers" abaixo é a versão do SMB compatível com seu host do Linux. Conecte a versão adequada no comando a seguir. Para versões do SMB compatíveis com o Data Box, consulte [Sistemas de arquivo compatíveis para clientes Linux](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients) 

```console
sudo mount -t nfs -o vers=2.1 10.126.76.138:/utSAC1_202006051000_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-to-data-box"></a>Copiar dados para caixa de dados

Quando você estiver conectado aos compartilhamentos do Data Box, a próxima etapa será copiar dados. Antes de começar a cópia de dados, examine as considerações a seguir:

* Copie os dados para os compartilhamentos que correspondem ao formato de dados apropriado. Por exemplo, copie os dados blob do bloco para o compartilhamento de blobs de bloco. Copie os VHDs para blob de páginas. Se o formato de dados não corresponder ao tipo de compartilhamento apropriado, em uma etapa posterior, o upload de dados do Azure falhará.
* Sempre crie uma pasta no compartilhamento para os arquivos que você pretende copiar e, depois, copie os arquivos nessa pasta. A pasta criada nos compartilhamentos de blob de blocos e de blob de páginas representa um contêiner no qual os dados são carregados como blobs. Não é possível copiar arquivos diretamente na pasta *raiz* na conta de armazenamento.
* Ao copiar dados, verifique se o tamanho dos dados está de acordo com os limites de tamanho descritos em [Limites de tamanho da conta de armazenamento do Azure](data-box-limits.md#azure-storage-account-size-limits).
* Se você quiser preservar metadados (ACLs, carimbos de data/hora e atributos de arquivo) ao transferir dados para o serviço Arquivos do Azure, siga as diretrizes em [Preservar ACLs, atributos e carimbos de data/hora de arquivo com o Azure Data Box](data-box-file-acls-preservation.md)  
* Se os dados que estão sendo carregados pelo Data Box também estiverem sendo carregados por outro aplicativo ao mesmo tempo, além do Data Box, isso poderá resultar em falhas no trabalho de upload e dados corrompidos.
* Recomendamos que:
  * Você não use o SMB e o NFS ao mesmo tempo.
  * Você copie os mesmos dados para o mesmo destino final no Azure.
  Nesses casos, o resultado final não poderá ser determinado.

> [!IMPORTANT]
> Mantenha uma cópia dos dados de origem até que haja a confirmação de que o Data Box transferiu os seus dados para o Armazenamento do Azure.

Depois de se conectar ao compartilhamento SMB, comece a cópia dos dados. Você pode usar qualquer ferramenta de cópia de arquivos compatível com SMB, como o Robocopy, para copiar seus dados. Vários trabalhos de cópia podem ser iniciados usando o Robocopy. Use o seguinte comando:

```console
robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
```

Os atributos são descritos na tabela a seguir.
    
|Atributo  |Descrição  |
|---------|---------|
|/e     |Copia subdiretórios, inclusive diretórios vazios.         |
|/r:     |Especifica o número de repetições em cópias com falha.         |
|/w:     |Especifica o tempo de espera entre as tentativas, em segundos.         |
|/is     |Inclui os mesmos arquivos.         |
|/nfl     |Especifica que os nomes de arquivo não são registrados.         |
|/ndl    |Especifica que os nomes de diretório não são registrados.        |
|/np     |Especifica que o progresso da operação de cópia (o número de arquivos ou diretórios copiados até o momento) não será exibido. Exibindo o progresso reduz significativamente o desempenho.         |
|/MT     | Use multithreading, recomendado de 32 ou 64 threads. Esta opção não é usada com arquivos criptografados. Você pode precisar separar arquivos criptografados e não criptografados. No entanto, a única cópia encadeada reduz significativamente o desempenho.           |
|/fft     | Use para reduzir a granularidade do registro de data e hora para qualquer sistema de arquivos.        |
|/b     | Copia os arquivos no modo de Backup.        |
|/z    | Copia os arquivos no modo de reinicialização, use essa opção se o ambiente estiver instável. Esta opção reduz o rendimento devido ao log adicional.      |
| /zb     | Usa o modo de reinicialização. Se o acesso for negado, esta opção usa o modo Backup. Esta opção reduz o rendimento devido ao ponto de verificação.         |
|/efsraw     | Copia todos os arquivos criptografados em modo raw do EFS. Use somente com arquivos criptografados.         |
|log+:\<LogFile>| Acrescenta a saída ao arquivo de log existente.|    
 
A amostra a seguir mostra a saída do comando robocopy para copiar arquivos para a Caixa de Dados.

```output
C:\Users>robocopy

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:53 PM
        Simple Usage :: ROBOCOPY source destination /MIR

        source :: Source Directory (drive:\path or \\server\share\path).
        destination :: Destination Dir  (drive:\path or \\server\share\path).
                /MIR :: Mirror a complete directory tree.

    For more usage information run ROBOCOPY /?

    ****  /MIR can DELETE files as well as copy them !

C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\

        Files : *.*

        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60

    ------------------------------------------------------------------------------

    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------

                Total    Copied   Skipped  Mismatch    FAILED    Extras
    Dirs :         1         1         1         0         0         0
    Files :        17        17         0         0         0         0
    Bytes :     3.9 k     3.9 k         0         0         0         0
C:\Users>
```

Para otimizar o desempenho, use os seguintes parâmetros de robocopy ao copiar os dados.

|    Plataforma    |    Principalmente pequenos arquivos < 512 KB                           |    Principalmente arquivos médios 512 KB - 1 MB                      |    Principalmente grandes arquivos > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    Sessões de Robocopy 2 <br> 16 threads por sessões    |    Sessões de Robocopy 3 <br> 16 threads por sessões    |    Sessões de Robocopy 2 <br> 24 threads por sessões    |

Para mais informações sobre o comando Robocopy, vá para [Robocopy e alguns exemplos](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

Durante o processo de cópia, se houver algum erro, você verá uma notificação.

![Uma notificação de erro de cópia em Conectar e copiar](media/data-box-deploy-copy-data/view-errors-1.png)

Selecione **Baixar lista de problemas**.

![Conectar e copiar – Baixar lista de problemas](media/data-box-deploy-copy-data/view-errors-2.png)

Abra a lista para ver os detalhes do erro e selecione a URL de resolução para ver a resolução recomendada.

![Conectar e copiar – Baixar e exibir erros](media/data-box-deploy-copy-data/view-errors-3.png)

Para obter mais informações, veja [Exibir logs de erros durante a cópia de dados para o Data Box](data-box-logs.md#view-error-log-during-data-copy). Para obter uma lista detalhada de erros durante a cópia de dados, veja [Solucionar problemas do Data Box](data-box-troubleshoot.md).

Para garantir a integridade dos dados, a soma de verificação é computada em linha à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço usado e o espaço livre no seu dispositivo.

![Verificar o espaço livre e usado no painel](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

Você pode copiar dados de seu servidor de origem para seu Data Box usando SMB, NFS, REST, o serviço de cópia de dados ou os discos gerenciados.

Qualquer que seja o caso, garanta que os nomes do compartilhamento e das pastas, bem como o tamanho dos dados, sigam as diretrizes descritas nos [Limites de serviço do Armazenamento do Azure e do Data Box](data-box-limits.md).

## <a name="copy-data-via-smb"></a>Copiar dados via SMB

Para copiar dados via SMB:

1. Se estiver usando um host do Windows, use o seguinte comando para se conectar aos compartilhamentos SMB:

    `\\<IP address of your device>\ShareName`

2. Para obter as credenciais de acesso ao compartilhamento, acesse a página **Conectar e copiar** na interface do usuário da Web local do Data Box.
3. Use qualquer ferramenta de cópia de arquivos compatível com SMB, como o Robocopy, para copiar dados para compartilhamentos. 

Para obter instruções passo a passo, acesse [Tutorial: Copiar dados para o Azure Data Box via SMB](data-box-deploy-copy-data.md).

## <a name="copy-data-via-nfs"></a>Copiar dados por meio de NFS

Para copiar dados via NFS:

1. Se estiver usando um host NFS, use o seguinte comando para montar os compartilhamentos NFS em seu Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Para obter as credenciais de acesso ao compartilhamento, acesse a página **Conectar e copiar** na interface do usuário da Web local do Data Box.
3. Use o comando `cp` ou `rsync` para copiar seus dados.

Para obter instruções passo a passo, acesse [Tutorial: Copiar dados para o Azure Data Box via NFS](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-rest"></a>Copiar dados via REST

Para copiar dados via REST:

1. Para copiar dados usando o Armazenamento de Blobs do Data Box via APIs REST, você pode se conectar usando *http* ou *https*.
2. Para copiar dados para o Armazenamento de Blobs do Data Box, você pode usar AzCopy.

Para obter instruções passo a passo, acesse [Tutorial: Copiar dados para o Armazenamento de Blobs do Azure Data Box por meio de APIs REST](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-data-copy-service"></a>Copiar dados usando o serviço de cópia de dados

Para copiar dados usando o serviço de cópia de dados:

1. Para copiar dados usando o serviço de cópia de dados, é necessário criar um trabalho. Na IU da Web local do Data Box, acesse **Gerenciar > Copiar dados > Criar**.
2. Preencha os parâmetros e crie um trabalho.

Para obter instruções passo a passo, acesse [Tutorial: Usar o serviço de cópia de dados para copiar dados para o Azure Data Box](data-box-deploy-copy-data-via-copy-service.md).

## <a name="copy-data-to-managed-disks"></a>Copiar dados para discos gerenciados

Para copiar discos gerenciados de dados:

1. Ao solicitar o dispositivo Data Box, você precisa ter selecionado discos gerenciados como seu destino de armazenamento.
2. Você pode se conectar ao Data Box por meio de compartilhamentos SMB ou NFS.
3. Em seguida, você pode copiar dados por meio de ferramentas SMB ou NFS.

Para obter instruções passo a passo, acesse [Tutorial: Usar o Data Box para importar dados como discos gerenciados no Azure](data-box-deploy-copy-data-from-vhds.md).

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Conectar-se à caixa de dados
> * Copiar dados para caixa de dados

Avance para o próximo tutorial para saber como enviar o Data Box novamente à Microsoft.

> [!div class="nextstepaction"]
> [Envie o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)

::: zone-end