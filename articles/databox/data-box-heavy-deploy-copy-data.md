---
title: Tutorial para copiar dados por meio de SMB no Azure Data Box Heavy | Microsoft Docs
description: Saiba como copiar dados para o Azure Data Box Heavy por meio de SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 1c45e06159e4c2850efa2d3ab3290647961fb7e1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592431"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Tutorial: Copiar dados para o Azure Data Box Heavy por meio do SMB

Este tutorial descreve como conectar-se e copiar dados do computador host usando a IU da Web local.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conectar-se ao Data Box Heavy
> * Copiar dados para o Data Box Heavy


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você concluiu o [Tutorial: Configurar o Azure Data Box Heavy](data-box-deploy-set-up.md).
2. Você recebeu o Data Box Heavy e o status do pedido no portal está como **Entregue**.
3. Você tem um computador host que tem os dados que você deseja copiar para o Data Box Heavy. O computador host deve
    - Executar um [Sistema operacional com suporte](data-box-system-requirements.md).
    - Estar conectado a uma rede de alta velocidade. Para velocidades de cópia mais rápidas, duas conexões de 40 GbE (uma por nó) podem ser utilizadas em paralelo. Se você não tem uma conexão de 40 GbE disponível, recomendamos que você tenha pelo menos duas conexões de 10 GbE (uma por nó).

## <a name="connect-to-data-box-heavy-shares"></a>Conectar-se compartilhamentos do Data Box Heavy

Com base na conta de armazenamento selecionada, o Data Box Heavy cria até:
- Três compartilhamentos para cada conta de armazenamento associada para GPv1 e GPv2.
- Um compartilhamento para armazenamento premium.
- Um compartilhamento para conta do Armazenamento de Blobs.

Esses compartilhamentos são criados nos nós do dispositivo.

Nos compartilhamentos do blob de blocos e do blob de páginas:
- Entidades de primeiro nível são contêineres.
- Entidades de segundo nível são blobs.

Nos compartilhamentos dos Arquivos do Azure:
- Entidades de primeiro nível são compartilhamentos.
- Entidades de segundo nível são arquivos.

A tabela a seguir mostra o caminho UNC para os compartilhamentos na URL de caminho do Data Box Heavy e do Armazenamento do Azure em que os dados são carregados. A URL final de caminho do Armazenamento do Azure pode ser derivada do caminho de compartilhamento UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Blobs de blocos do Azure | <li>Caminho UNC para compartilhamentos: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para compartilhamentos: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Arquivos do Azure       |<li>Caminho UNC para compartilhamentos: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

As etapas para se conectar que usam um cliente Windows ou Linux são diferentes.

> [!NOTE]
> Siga as mesmas etapas para se conectar a ambos os nós do dispositivo em paralelo.

### <a name="connect-on-a-windows-system"></a>Conectar-se em um sistema Windows

Se estiver usando um computador host do Windows Server, siga estas etapas para conectar-se ao Data Box Heavy.

1. O primeiro passo é autenticar e iniciar uma sessão. Vá para **conectar e copiar**. Clique em **Obter credenciais** para obter as credenciais de acesso aos compartilhamentos associados à sua conta de armazenamento.

    ![Obter as credenciais de compartilhamento 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. No compartilhamento de acesso e caixa de diálogo de dados de cópia, copie o **nome de usuário** e o **senha** correspondente para o compartilhamento. Clique em **OK**.
    
    ![Obter as credenciais de compartilhamento 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Para acessar os compartilhamentos associados à sua conta de armazenamento (*databoxe2etest* no exemplo a seguir) no computador host, abra uma janela Comando. No prompt de comando, digite:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Dependendo do formato dos dados, os caminhos de compartilhamento serão os seguintes:
    - Blob de blocos do Azure – `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Blob de páginas do Azure – `\\10.100.10.100\databoxe2etest_PageBlob`
    - Arquivos do Azure – `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Quando solicitado, digite a senha do compartilhamento. A amostra a seguir mostra a conexão a um compartilhamento por meio do comando anterior.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Pressione Windows + R. Na janela **Executar**, especifique o `\\<device IP address>`. Clique em **OK** para abrir o Explorador de Arquivos.
    
    ![Conecte-se para compartilhar via File Explorer 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Agora você verá os compartilhamentos como pastas.
    
    ![Conecte-se para compartilhar via File Explorer 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Sempre crie uma pasta para os arquivos que você pretende copiar no compartilhamento e, em seguida, copie os arquivos para a pasta**. A pasta criada nos compartilhamentos de blob de blocos e de blob de páginas representa um contêiner no qual os dados são carregados como blobs. Não é possível copiar arquivos diretamente para a pasta *raiz* na conta de armazenamento.
    
### <a name="connect-on-a-linux-system"></a>Conectar-se em um sistema Linux

Se estiver usando um cliente Linux, use o seguinte comando para montar o compartilhamento SMB.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

O parâmetro `vers` é a versão do SMB compatível com o host do Linux. Conecte a versão adequada no comando acima.

Para versões do SMB compatíveis com o Data Box Heavy, confira [Sistemas de arquivo compatíveis para clientes Linux](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Copiar dados para o Data Box Heavy

Quando você estiver conectado aos compartilhamentos do Data Box Heavy, a próxima etapa será copiar dados.

### <a name="copy-considerations"></a>Considerações de cópia

Antes de começar a cópia de dados, examine as considerações a seguir:

- Copie os dados para os compartilhamentos que correspondem ao formato de dados apropriado. Por exemplo, copie os dados blob do bloco para o compartilhamento de blobs de bloco. Copie os VHDs para blob de páginas.

    Se o formato de dados não corresponder ao tipo de compartilhamento apropriado, em uma etapa posterior, o upload de dados do Azure falhará.
-  Durante a cópia de dados, verifique se o tamanho dos dados está em conformidade com os limites de tamanho descritos nos [Limites do Armazenamento do Azure e do Data Box Heavy](data-box-heavy-limits.md).
- Se dados estiverem sendo carregados pelo Data Box Heavy e por outro aplicativo fora do Data Box Heavy ao mesmo tempo, isso poderá causar falha no trabalho de upload e dados corrompidos.
- Recomendamos que:
    - Você não use o SMB e o NFS ao mesmo tempo.
    - Você copie os mesmos dados para o mesmo destino final no Azure.
     
  Nesses casos, o resultado final não poderá ser determinado.
- Sempre crie uma pasta para os arquivos que você pretende copiar no compartilhamento e, em seguida, copie os arquivos nessa pasta. A pasta criada nos compartilhamentos de blob de blocos e de blob de páginas representa um contêiner no qual os dados são carregados como blobs. Não é possível copiar arquivos diretamente para a pasta *raiz* na conta de armazenamento.

Depois de conectar-se ao compartilhamento SMB, comece a cópia de dados.

1. Você pode usar qualquer ferramenta de cópia de arquivos compatível com SMB, como o Robocopy, para copiar seus dados. Vários trabalhos de cópia podem ser iniciados usando o Robocopy. Use o seguinte comando:
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    Os atributos são descritos na tabela a seguir.
    
    |Atributo  |DESCRIÇÃO  |
    |---------|---------|
    |/e      |Copia subdiretórios, inclusive diretórios vazios.         |
    |/r:     |Especifica o número de repetições em cópias com falha.         |
    |/w:     |Especifica o tempo de espera entre as tentativas, em segundos.         |
    |/is     |Inclui os mesmos arquivos.         |
    |/nfl    |Especifica que os nomes de arquivo não são registrados.         |
    |/ndl    |Especifica que os nomes de diretório não são registrados.        |
    |/np     |Especifica que o progresso da operação de cópia (o número de arquivos ou diretórios copiados até o momento) não será exibido. Exibindo o progresso reduz significativamente o desempenho.         |
    |/MT     | Use multithreading, recomendado de 32 ou 64 threads. Esta opção não é usada com arquivos criptografados. Você pode precisar separar arquivos criptografados e não criptografados. No entanto, a única cópia encadeada reduz significativamente o desempenho.           |
    |/fft    | Use para reduzir a granularidade do registro de data e hora para qualquer sistema de arquivos.        |
    |/b      | Copia os arquivos no modo de Backup.        |
    |/z      | Copia os arquivos no modo de reinicialização, use essa opção se o ambiente estiver instável. Esta opção reduz o rendimento devido ao log adicional.      |
    | /zb    | Usa o modo de reinicialização. Se o acesso for negado, esta opção usa o modo Backup. Esta opção reduz o rendimento devido ao ponto de verificação.         |
    |/efsraw | Copia todos os arquivos criptografados em modo raw do EFS. Use somente com arquivos criptografados.         |
    |log+:\<LogFile>| Acrescenta a saída ao arquivo de log existente.|
    
 
    O exemplo a seguir mostra a saída do comando robocopy para copiar arquivos para o Data Box Heavy.

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
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

2. Para otimizar o desempenho, use os seguintes parâmetros de robocopy ao copiar os dados. (Os números abaixo representam os melhores cenários de caso.)

    | Plataforma    | Principalmente pequenos arquivos < 512 KB    | Principalmente arquivos médios 512 KB a 1 MB  | Principalmente grandes arquivos > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 sessões do Robocopy <br> 24 threads por sessões | 6 sessões do Robocopy <br> 16 threads por sessões | 6 sessões do Robocopy <br> 16 threads por sessões |


    Para mais informações sobre o comando Robocopy, vá para [Robocopy e alguns exemplos](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

3. Abra a pasta de destino para exibir e verificar os arquivos copiados.

    ![Exibir arquivos copiados](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Conforme os dados são copiados:

    - Os nomes, tamanhos e o formato do arquivo são validados para verificar se eles atendem aos limites de objeto e de armazenamento do Azure, além das convenções de nomenclatura de arquivo e de contêiner do Azure.
    - Para garantir a integridade dos dados, a soma de verificação também é computada de forma embutida.

    Se você tiver algum erro durante o processo de cópia, baixe os arquivos de erro para solução de problemas. Selecione o ícone de seta para baixar os arquivos de erro.

    ![Baixar arquivos de erro](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Para saber mais, confira [Exibir logs de erros durante a cópia de dados para o Data Box Heavy](data-box-logs.md#view-error-log-during-data-copy). Para obter uma lista detalhada de erros durante a cópia de dados, confira [Solucionar problemas do Data Box Heavy](data-box-troubleshoot.md).

5. Abra o arquivo de erro no Bloco de Notas. O arquivo de erro a seguir indica que os dados não estão alinhados corretamente.

    ![Abrir arquivo de erro](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Para um blob de páginas, os dados precisam ter 512 bytes alinhados. Depois que esses dados são removidos, o erro é resolvido, conforme mostrado na captura de tela a seguir.

    ![Erro resolvido](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Depois que a cópia por concluída, acesse a página **Exibir Painel**. Verifique o espaço usado e o espaço livre em seu dispositivo.
    
    ![Verificar o espaço livre e usado no painel](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Repita as etapas acima para copiar os dados para o segundo nó do dispositivo.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box Heavy, como:

> [!div class="checklist"]
> * Conectar-se ao Data Box Heavy
> * Copiar dados para o Data Box Heavy


Passe para o próximo tutorial para saber como enviar seu Data Box Heavy de volta para a Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box Heavy para a Microsoft](./data-box-heavy-deploy-picked-up.md)

