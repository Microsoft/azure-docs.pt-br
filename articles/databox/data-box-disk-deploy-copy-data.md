---
title: Tutorial para copiar dados para o Azure Data Box Disk | Microsoft Docs
description: Use este tutorial para aprender a copiar dados para o Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 336cc7dae00d06e38e4be8671f1cb11ed73e5edc
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414651"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Tutorial: copiar dados para o Azure Data Box Disk e verificar

Este tutorial descreve como copiar dados do computador host e gerar somas de verificação para verificar a integridade dos dados.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Copiar dados para o Data Box Disk
> * Verificar os dados

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:
- Você concluiu o [Tutorial: instalar e configurar o Azure Data Box Disk](data-box-disk-deploy-set-up.md).
- Os discos são desbloqueados e conectados a um computador cliente.
- O computador cliente usado para copiar dados para os discos precisa executar um [Sistema operacional com suporte](data-box-disk-system-requirements.md##supported-operating-systems-for-clients).
- Certifique-se de que o tipo de armazenamento pretendido para seus dados corresponde aos [tipos de armazenamento suportados](data-box-disk-system-requirements.md#supported-storage-types-for-upload).
- Revise [Limites de disco gerenciado em limites de tamanho de objeto do Azure](data-box-disk-limits.md#azure-object-size-limits).


## <a name="copy-data-to-disks"></a>Copiar dados para os discos

Antes de copiar dados para os discos, analise as considerações a seguir:

- Você é responsável por copiar os dados para pastas que correspondam ao formato de dados apropriado. Por exemplo, copie os dados de blob de bloco para a pasta de blobs de blocos. Se o formato de dados não corresponder à pasta apropriada (tipo de armazenamento), o carregamento de dados do Azure falhará em uma etapa posterior.
- Durante a cópia de dados, verifique se o tamanho dos dados está em conformidade com os limites de tamanho descritos nos [limites do armazenamento do Azure e do Data Box Disk](data-box-disk-limits.md).
- Se dados estiverem sendo carregados pelo Data Box Disk e por outro aplicativo fora do Data Box Disk ao mesmo tempo, isso poderá causar falha no trabalho de upload e corromper os dados.

   > [!IMPORTANT]
   >  Se você especificou os discos gerenciados como um dos destinos de armazenamento durante a criação do pedido, a seção a seguir é aplicável.

- Só é possível ter um disco gerenciado com um determinado nome em um grupo de recursos em todas as pastas pré-criadas e em todo o Data Box Disk. Isso implica que os VHDs enviados para as pastas pré-criadas deverão ter nomes exclusivos. Certifique-se de que o nome determinado não corresponda a um disco gerenciado já existente em um grupo de recursos. Se os VHDs tiverem nomes iguais, somente um VHD será convertido em disco gerenciado com esse nome. Os outros VHDs serão carregados como blobs de páginas na conta de armazenamento temporário.
- Sempre copie os VHDs para uma das pastas pré-criadas. Se você copiar os VHDs fora dessas pastas ou em uma pasta que você criou, os VHDs serão carregados na conta do Armazenamento do Azure como blobs de páginas e não como discos gerenciados.
- Apenas os VHDs fixos podem ser carregados para criar discos gerenciados. VHDs dinâmicos, VHDs diferenciais ou arquivos VHDX não têm suporte.


Execute as seguintes etapas para fazer a conexão e copiar dados do computador para o Data Box Disk.

1. Exiba o conteúdo da unidade desbloqueada. A lista das pastas e subpastas pré-criadas na unidade é diferente, dependendo das opções selecionadas ao colocar o pedido do Data Box Disk.

    |Destino de armazenamento selecionado  |Tipo de conta de armazenamento|Tipo de conta de armazenamento de preparo |Pastas e subpastas  |
    |---------|---------|---------|------------------|
    |Conta de armazenamento     |GPv1 ou GPv2                 | ND | BlockBlob <br> PageBlob <br> AzureFile        |
    |Conta de armazenamento     |Conta de armazenamento de blobs         | ND | BlockBlob        |
    |Discos gerenciados     |ND | GPv1 ou GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |Conta de armazenamento <br> Discos gerenciados     |GPv1 ou GPv2 | GPv1 ou GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |Conta de armazenamento <br> Discos gerenciados    |Conta de armazenamento de blobs | GPv1 ou GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    Uma captura de tela de exemplo de um pedido no qual uma conta de armazenamento GPv2 foi especificada é mostrada abaixo:

    ![Conteúdo da unidade de disco](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Copie os dados que deverão ser importados como blobs de blocos para a pasta *BlockBlob*. Da mesma forma, copie dados como VHD/VHDX para a pasta *PageBlob* e os dados para a pasta *AzureFile*.

    Um contêiner é criado na conta de armazenamento do Azure para cada subpasta nas pastas BlockBlob e PageBlob. Todos os arquivos nas pastas BlockBlob e PageBlob são copiados para um contêiner padrão `$root` na conta de Armazenamento do Azure. Todos os arquivos no contêiner `$root` são sempre carregados como blobs de blocos.

   Copie os arquivos para uma pasta na pasta *AzureFile*. Uma subpasta dentro da pasta *AzureFile* cria um compartilhamento de arquivos. Os arquivos copiados diretamente para a pasta *AzureFile* falham e são carregados como blobs de blocos.

    Se existirem arquivos e pastas no diretório raiz, você precisará movê-los para uma pasta diferente antes de começar a copiar os dados.

    > [!IMPORTANT]
    > Todos os contêineres, blobs e nomes de arquivos devem estar em conformidade com as [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Se essas regras não forem seguidas, o carregamento de dados do Azure falhará.

3. Ao copiar arquivos, verifique se os arquivos não excedem ~4,7 TiB para blobs de blocos, ~8 TiB para blobs de páginas e ~1 TiB para Arquivos do Azure. 
4. Você pode arrastar e soltar com o Explorador de Arquivos para copiar os dados. Você também pode usar qualquer ferramenta de cópia de arquivo compatível com SMB, como Robocopy, para copiar seus dados. Vários trabalhos de cópia podem ser iniciados usando o seguinte comando Robocopy:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    Os parâmetros e as opções para o comando são tabulados da seguinte maneira:
    
    |Parâmetros/Opções  |DESCRIÇÃO |
    |--------------------|------------|
    |Fonte            | Especifica o caminho para o diretório de origem.        |
    |Destino       | Especifica o caminho para o diretório de destino.        |
    |/E                  | Copia subdiretórios, inclusive diretórios vazios. |
    |/MT[:N]             | Cria cópias com N threads, em que N é um inteiro entre 1 e 128. <br>O valor padrão para N é 8.        |
    |/R: \<N>             | Especifica o número de repetições em cópias com falha. O valor padrão de N é 1.000.000 (um milhão de repetições).        |
    |/W: \<N>             | Especifica o tempo de espera entre as tentativas, em segundos. O valor padrão de N é 30 (tempo de espera de 30 segundos).        |
    |/ NFL                | Especifica que os nomes de arquivo não devem ser registrados.        |
    |/NDL                | Especifica que os nomes de diretório não devem ser registrados.        |
    |/FFT                | Pressupõe tempos de arquivo FAT (precisão de dois segundos).        |
    |/Log:\<arquivo de log>     | Grava a saída de status no arquivo de log (substitui o arquivo de log existente).         |

    Vários discos podem ser usados em paralelo com vários trabalhos em execução em cada disco.

6. Verifique o status da cópia quando o trabalho está em andamento. O exemplo a seguir mostra a saída do comando robocopy para copiar arquivos no Data Box Disk.

    ```
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    Para otimizar o desempenho, use os seguintes parâmetros de robocopy ao copiar os dados.

    |    Plataforma    |    Principalmente pequenos arquivos < 512 KB                           |    Principalmente arquivos médios 512 KB - 1 MB                      |    Principalmente grandes arquivos > 1 MB                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
    |    Data Box Disk        |    4 sessões do Robocopy* <br> 16 threads por sessões    |    2 sessões do Robocopy* <br> 16 threads por sessões    |    2 sessões do Robocopy* <br> 16 threads por sessões    |
    
    **Cada sessão do Robocopy pode ter no máximo 7 mil diretórios e 150 milhões de arquivos.*
    
    >[!NOTE]
    > Os parâmetros sugeridos acima se baseiam no ambiente usado em testes internos.
    
    Para mais informações sobre o comando Robocopy, vá para [Robocopy e alguns exemplos](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

6. Abra a pasta de destino para exibir e verificar os arquivos copiados. Se você receber erros durante o processo de cópia, baixe os arquivos de log para solucionar problemas. Os arquivos de log estão localizados conforme especificado no comando robobopy.
 
### <a name="split-and-copy-data-to-disks"></a>Dividir e copiar dados para os discos

Este procedimento opcional pode ser usado quando você usar vários discos e tiver um grande conjunto de dados que precisa ser dividido e copiado entre todos os discos. A Data Box Split Copy ajuda a dividir e copiar os dados em um computador Windows.

>[!IMPORTANT]
> A ferramenta Data Box Split Copy também valida seus dados. Se você usar a ferramenta Data Box Split Copy para copiar dados, ignore a [etapa de validação](#validate-data).
> A ferramenta Split Copy não é compatível com discos gerenciados.

1. No seu computador Windows, verifique se você tem uma ferramenta Data Box Split Copy baixada e extraída em uma pasta local. Essa ferramenta foi baixada quando você baixou o conjunto de ferramentas para o Data Box Disk para Windows.
2. Abra o Explorador de Arquivos. Anote a unidade da fonte de dados e as letras das unidades atribuídas ao Data Box Disk. 

     ![Dividir e copiar dados](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Identifique os dados de origem para copiar. Por exemplo, nesse caso:
    - Os dados do blob de blocos a seguir foram identificados.

         ![Dividir e copiar dados](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - Os dados do blob de páginas a seguir foram identificados.

         ![Dividir e copiar dados](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Vá para a pasta em que o software foi extraído. Localize o arquivo `SampleConfig.json` nessa pasta. Esse é um arquivo somente leitura que você pode modificar e salvar.

   ![Dividir e copiar dados](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. Modifique o arquivo `SampleConfig.json`.
 
   - Forneça um nome de trabalho. Isso cria uma pasta no Data Box Disk e, eventualmente, torna-se o contêiner na conta de armazenamento do Azure associada a esses discos. O nome do trabalho precisa seguir as convenções de nomenclatura do contêiner do Azure. 
   - Forneça um caminho de origem anotando o formato do caminho no `SampleConfigFile.json`. 
   - Insira as letras das unidades correspondentes aos discos de destino. Os dados são tirados do caminho de origem e copiados para vários discos.
   - Forneça um caminho para os arquivos de log. Por padrão, ele é enviado ao diretório atual em que o `.exe` está localizado.

     ![Dividir e copiar dados](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. Para validar o formato de arquivo, acesse `JSONlint`. Salve o arquivo como `ConfigFile.json`. 

     ![Dividir e copiar dados](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Abra uma janela de Prompt de Comando. 

8. Execute o `DataBoxDiskSplitCopy.exe`. Type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Dividir e copiar dados](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Pressione Enter para continuar o script.

    ![Dividir e copiar dados](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. Quando o conjunto de dados for dividido e copiado, o resumo da ferramenta Split Copy para a sessão de cópia é apresentada. Um exemplo de saída é mostrado abaixo.

    ![Dividir e copiar dados](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Verifique se que os dados são divididos entre os discos de destino. 
 
    ![Dividir dados de cópia](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![Dividir dados de cópia](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    Se você examinar o conteúdo da unidade `n:` em mais detalhes, verá que as duas subpastas foram criadas correspondendo aos dados de formato do blob de blocos e do blob de páginas.
    
     ![Dividir e copiar dados](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Se a sessão de cópia falhar, use o seguinte comando para recuperá-la e retomá-la:

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

Se você encontrar erros usando a ferramenta de Dividir cópia, veja como [solucionar erros na ferramenta Dividir cópia](data-box-disk-troubleshoot-data-copy.md).

Depois que a cópia de dados for concluída, você poderá prosseguir para a validação dos dados. Se você usou a ferramenta Split Copy, ignore a validação (a ferramenta também executa a validação) e avance para o próximo tutorial.


## <a name="validate-data"></a>Validar dados

Se você não tiver usado a ferramenta Split Copy para copiar dados, será necessário validar seus dados. Para verificar os dados, execute as seguintes etapas.

1. Execute `DataBoxDiskValidation.cmd` para validação de soma de verificação na pasta *DataBoxDiskImport* da unidade.
    
    ![Saída da ferramenta de validação do Data Box Disk](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Escolha a opção apropriada. **É recomendado que você sempre valide os arquivos e gere somas de verificação selecionando a opção 2**. Dependendo do tamanho dos dados, esta etapa pode demorar um pouco. Depois que o script for concluído, saia da janela Comando. Se houver erros durante a validação e a geração da soma de verificação, você será notificado e também será fornecido um link para os logs de erros.

    ![Saída da soma de verificação](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - Redefina a ferramenta entre duas execuções.
    > - Use a opção 1 se estiver lidando apenas com um grande conjunto de dados que contenha arquivos pequenos (~ KBs). Essa opção somente valida os arquivos, pois a geração de soma de verificação pode levar muito tempo e o desempenho pode ficar muito lento.

3. Se estiver usando vários discos, execute o comando para cada disco.

Se você encontrar erros durante a validação, veja como [solucionar erros de validação](data-box-disk-troubleshoot.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Copiar dados para o Data Box Disk
> * Verificar a integridade dos dados

Avance para o próximo tutorial a fim de saber como devolver o Data Box Disk e verificar o carregamento de dados do Azure.

> [!div class="nextstepaction"]
> [Devolver o Azure Data Box para a Microsoft](./data-box-disk-deploy-picked-up.md)

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-disks"></a>Copiar dados para os discos

Execute as etapas a seguir para conectar e copiar dados do computador para o Data Box Disk.

1. Exiba o conteúdo da unidade desbloqueada. A lista das pastas e subpastas pré-criadas na unidade é diferente, dependendo das opções selecionadas ao colocar o pedido do Data Box Disk.
2. Copie os dados para pastas que correspondem ao formato de dados apropriado. Por exemplo, copie os dados não estruturados para a pasta *BlockBlob*, os dados do VHD ou VHDX para a pasta *PageBlob* e os arquivos para *AzureFile*. Se o formato de dados não corresponder à pasta apropriada (tipo de armazenamento), em uma etapa posterior, o upload de dados no Azure falhará.

    - Um contêiner é criado na conta de armazenamento do Azure para cada subpasta nas pastas BlockBlob e PageBlob. Todos os arquivos das pastas *BlockBlob* e *PageBlob* são copiados para um contêiner padrão $root na conta do Armazenamento do Azure. 
    - Todos os arquivos no contêiner $root são sempre carregados como blobs de blocos.
    - Copie os arquivos para uma pasta na pasta *AzureFile*. Uma subpasta dentro da pasta *AzureFile* cria um compartilhamento de arquivos. Os arquivos copiados diretamente para a pasta *AzureFile* falham e são carregados como blobs de blocos.
    - Se existirem arquivos e pastas no diretório raiz, você precisará movê-los para uma pasta diferente antes de começar a copiar os dados.
    - Caso o seu pedido tenha o Managed Disks como um dos destinos de armazenamento, confira as convenções de nomenclatura para [discos gerenciados](data-box-disk-limits.md#managed-disk-naming-conventions).

    > [!IMPORTANT]
    > Todos os contêineres, blobs e arquivos devem estar em conformidade com as [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) e os [limites de tamanho de objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Se essas regras ou esses limites não forem seguidos, o upload de dados no Azure falhará.

3. Use a operação de arrastar e soltar com o Explorador de Arquivos ou qualquer ferramenta de cópia de arquivo compatível com SMB, como o Robocopy, para copiar seus dados. Vários trabalhos de cópia podem ser iniciados usando o seguinte comando:

    ```
    Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt
    ```
4. Abra a pasta de destino para exibir e verificar os arquivos copiados. Se você receber erros durante o processo de cópia, baixe os arquivos de log para solucionar problemas. Os arquivos de log estão localizados conforme especificado no comando robobopy.

Use o procedimento opcional de [divisão e cópia](data-box-disk-deploy-copy-data.md#split-and-copy-data-to-disks) ao usar vários discos e tiver um conjunto de dados grande que precisa ser dividido e copiado entre todos os discos.

## <a name="validate-data"></a>Validar dados

Execute as etapas a seguir para verificar os dados.

1. Execute `DataBoxDiskValidation.cmd` para validação de soma de verificação na pasta *DataBoxDiskImport* da unidade.
2. Use a opção 2 para validar os arquivos e gerar somas de verificação. Dependendo do tamanho dos dados, esta etapa pode demorar um pouco. Se houver erros durante a validação e a geração da soma de verificação, você será notificado e também será fornecido um link para os logs de erros.

    Se você encontrar erros durante a validação, veja como [solucionar erros de validação](data-box-disk-troubleshoot.md).

::: zone-end
