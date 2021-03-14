---
title: Como gerenciar Sincronização de Arquivos do Azure arquivos em camadas | Microsoft Docs
description: Dicas e commandlets do PowerShell para ajudá-lo a gerenciar arquivos em camadas
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dc61792da669cd5d2c928eec0fd412d86725fc8c
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204285"
---
# <a name="how-to-manage-tiered-files"></a>Como gerenciar arquivos em camadas

Este artigo fornece diretrizes para usuários que têm dúvidas relacionadas ao gerenciamento de arquivos em camadas. Para perguntas conceituais sobre camadas de nuvem, consulte [perguntas frequentes sobre arquivos do Azure](storage-files-faq.md).

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Como verificar se os arquivos estão sendo em camadas

Se os arquivos precisam ou não ser em camadas por políticas de conjunto, é avaliado uma vez por hora. Você pode entrar em duas situações em que um novo ponto de extremidade do servidor é criado:

Quando você adiciona um novo ponto de extremidade do servidor, geralmente existem arquivos nesse local do servidor. Eles precisam ser carregados antes que a camada de nuvem possa começar. A política de espaço livre do volume não iniciará seu trabalho até que o carregamento inicial de todos os arquivos tenha sido concluído. No entanto, a política de data opcional começará a funcionar em uma base de arquivo individual, assim que um arquivo tiver sido carregado. O intervalo de uma hora também se aplica aqui. 

Quando você adiciona um novo ponto de extremidade do servidor, é possível que você tenha conectado um local do servidor vazio a um compartilhamento de arquivos do Azure com seus dados. Se você optar por baixar o namespace e recuperar o conteúdo durante o download inicial para o servidor, depois que o namespace for desativado, os arquivos serão recuperados com base no carimbo de data/hora da última modificação até que a política de espaço livre do volume e os limites de política de dados opcionais sejam atingidos.

Há várias maneiras de verificar se um arquivo foi colocado em camadas no compartilhamento de arquivos do Azure:
    
   *  **Verifique os atributos de arquivo no arquivo.**
     Clique com o botão direito do mouse em um arquivo, navegue até **Detalhes** e role para baixo até a propriedade **Atributos**. Um arquivo em camadas tem os seguintes atributos definidos:     
        
        | Carta de atributo | Atributo | Definição |
        |:----------------:|-----------|------------|
        | Um | Archive | Indica que o arquivo deve ter o backup feito pelo software de backup. Esse atributo é sempre definido independentemente de o arquivo estar em camadas ou totalmente armazenado no disco. |
        | P | Arquivos esparsos | Indica que o arquivo é um arquivo esparso. Um arquivo esparso é um tipo especializado de arquivo que o NTFS oferece para uso eficiente quando o arquivo no fluxo do disco está basicamente vazio. A Sincronização de Arquivos do Azure usa arquivos esparsos, porque um arquivo é totalmente em camadas ou parcialmente cancelado. Em um arquivo totalmente em camadas, o fluxo de arquivos é armazenado na nuvem. Em um arquivo que sofreu recall parcial, essa parte do arquivo já está no disco. Isso pode acontecer quando os arquivos são lidos parcialmente por aplicativos como players de multimídia ou utilitários de compactação. Se o recall de um arquivo é feito totalmente em disco, a Sincronização de Arquivos do Azure o converte de um arquivo esparso em um arquivo regular. Esse atributo só é definido no Windows Server 2016 e mais antigo.|
        | M | Recall no acesso a dados | Indica que os dados do arquivo não estão totalmente presentes no armazenamento local. A leitura do arquivo fará com que pelo menos um conteúdo do arquivo seja obtido de um compartilhamento de arquivos do Azure ao qual o ponto de extremidade do servidor está conectado. Esse atributo só é definido no Windows Server 2019. |
        | L | Ponto de nova análise | Indica que o arquivo tem um ponto de nova análise. Um ponto de nova análise é um ponteiro especial para ser usado por um filtro do sistema de arquivos. A Sincronização de Arquivos do Azure usa pontos de nova análise a fim de definir, para o filtro do sistema de arquivos da Sincronização de Arquivos do Azure (StorageSync.sys), o local na nuvem em que o arquivo está armazenado. Isso dá suporte a acesso contínuo. Os usuários não precisarão saber que a Sincronização de Arquivos do Azure está sendo usada ou como obter acesso ao arquivo em seu compartilhamento de arquivos do Azure. Quando o recall de um arquivo é totalmente feito, a Sincronização de Arquivos do Azure remove o ponto de nova análise do arquivo. |
        | O | Offline | Indica que parte ou nenhum conteúdo do arquivo está armazenado em disco. Quando o recall de um arquivo é totalmente feito, a Sincronização de Arquivos do Azure remove esse atributo. |

        ![A caixa de diálogo Propriedades de um arquivo com a guia Detalhes selecionada](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > Você pode ver os atributos de todos os arquivos em uma pasta adicionando o campo **Atributos** à exibição de tabela do Explorador de Arquivos. Para fazer isso, clique com o botão direito do mouse em uma coluna existente (por exemplo, **Tamanho**), selecione **Mais** e depois **Atributos** na lista suspensa.
        
        > [!NOTE]
        > Todos esses atributos também estarão visíveis para arquivos parcialmente recuperados.
        
   * **Use `fsutil` para verificar se há pontos de nova análise em um arquivo.**
       Conforme descrito na opção anterior, um arquivo em camadas sempre tem um conjunto de pontos de nova análise. Um ponto de nova análise permite que o StorageSync.sys (driver de filtro do sistema de arquivos) Sincronização de Arquivos do Azure recupere o conteúdo de compartilhamentos de arquivos do Azure que não está armazenado localmente no servidor. 

       Para verificar se um arquivo tem um ponto de nova análise, execute o utilitário `fsutil` em um prompt de comandos com privilégios elevados ou em uma janela do PowerShell:

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Se o arquivo tiver um ponto de nova análise, você deverá ver um **Valor de Marca de Nova Análise: 0x8000001e**. Esse valor hexadecimal é o valor do ponto de nova análise que pertence ao Sincronização de Arquivos do Azure. A saída também contém os dados de nova análise que representam o caminho para o arquivo no compartilhamento de arquivos do Azure.
        
        > [!WARNING]  
        > O comando do utilitário `fsutil reparsepoint` também tem a capacidade de excluir um ponto de nova análise. Não execute esse comando, a menos que a equipe de engenharia de Sincronização de Arquivos do Azure lhe solicite isso. A execução desse comando pode resultar em perda de dados. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Como excluir aplicativos do último controle de tempo de acesso em camadas da nuvem

Com o Sincronização de Arquivos do Azure Agent versão 11,1, agora você pode excluir aplicativos do último controle de acesso. Quando um aplicativo acessa um arquivo, a hora do último acesso do arquivo é atualizada no banco de dados de camadas de nuvem. Os aplicativos que examinam o sistema de arquivos como antivírus fazem com que todos os arquivos tenham o mesmo horário do último acesso, o que afeta quando os arquivos são em camadas.

Para excluir aplicativos do último controle de tempo de acesso, adicione o nome do processo à configuração do registro HeatTrackingProcessNameExclusionList que está localizada em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

Exemplo: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

> [!NOTE]
> A eliminação de duplicação de dados e os processos do Gerenciador de recursos de servidor de arquivos (FSRM) são excluídos por padrão. As alterações na lista de exclusão de processos são respeitadas pelo sistema a cada 5 minutos.

## <a name="how-to-access-the-heat-store"></a>Como acessar o armazenamento de calor

A camada de nuvem usa a hora do último acesso e a frequência de acesso de um arquivo para determinar quais arquivos devem ser em camadas. O driver de filtro de camadas de nuvem (storagesync.sys) acompanha o horário do último acesso e registra as informações no armazenamento de calor em camadas de nuvem. Você pode recuperar o armazenamento de calor e salvá-lo em um arquivo CSV usando um cmdlet do PowerShell de servidor local.

Há um único armazenamento de calor para todos os arquivos no mesmo volume. O armazenamento de calor pode ficar muito grande. Se você só precisa recuperar o número "mais frio" de itens, use-Limit e um número e também considere a filtragem por um sub Path versus a raiz do volume.

- Importe o módulo do PowerShell:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- ESPAÇO livre no VOLUME: para obter a ordem em que os arquivos serão em camadas usando a política de espaço livre do volume:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- POLÍTICA de data: para obter a ordem em que os arquivos serão em camadas usando a política de data:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Localizar as informações do armazenamento de calor para um arquivo específico:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Ver todos os arquivos em ordem decrescente por hora do último acesso:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Veja a ordem pela qual os arquivos em camadas serão rechamados por recuperação em segundo plano ou solicitação por demanda por meio do PowerShell:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Como forçar um arquivo ou diretório a ser em camadas

> [!NOTE]
> Quando você seleciona um diretório a ser colocado em camadas, somente os arquivos atualmente no diretório são em camadas. Todos os arquivos criados após esse período não são automaticamente em camadas.

Quando habilitado, o recurso de disposição em camadas na nuvem dispõe os arquivos em camadas automaticamente com base no último acesso e na última modificação para alcançar o percentual de espaço livre no volume especificado no ponto de extremidade de nuvem. Às vezes, no entanto, talvez você queira forçar um arquivo manualmente a ser dividido em camadas. Isso pode ser útil se você salva um arquivo grande que não pretende usar novamente por um longo período e agora deseja usar o espaço livre no volume para outros arquivos ou pastas. Você pode forçar a disposição em camadas com os seguintes comandos do PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Como recuperar um arquivo em camadas para o disco

A maneira mais fácil de fazer o recall de um arquivo em disco é abri-lo. O filtro de sistema de arquivos da Sincronização de Arquivos do Azure (StorageSync.sys) baixa o arquivo do seu compartilhamento de arquivos do Azure perfeitamente, sem que você precise fazer nenhum trabalho. Para tipos de arquivo que podem ser parcialmente lidos ou transmitidos, como arquivos multimídia ou. zip, simplesmente abrir um arquivo não garante que o arquivo inteiro seja baixado.

Para garantir que um arquivo seja totalmente baixado para o disco local, você deve usar o PowerShell para forçar a rechamada completa de um arquivo. Essa opção também pode ser útil se você quiser rechamar vários arquivos de uma vez, como todos os arquivos em uma pasta. Abra uma sessão do PowerShell para o nó de servidor em que a Sincronização de Arquivos do Azure está instalada e então execute os seguintes comandos do PowerShell:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Parâmetros opcionais:
* `-Order CloudTieringPolicy` o solicitará primeiro os arquivos modificados ou acessados mais recentemente e será permitido pela política de camadas atual. 
    * Se a política de espaço livre do volume estiver configurada, os arquivos serão recuperados até que a configuração de política de espaço livre do volume seja atingida. Por exemplo, se a configuração de política de volume livre for de 20%, o cancelamento será interrompido quando o espaço livre do volume atingir 20%.  
    * Se a política de espaço livre do volume e de data estiver configurada, os arquivos serão recuperados até que a configuração de política de data ou espaço livre do volume seja atingida. Por exemplo, se a configuração de política de volume livre for de 20% e a política de data for de 7 dias, a recuperação será interrompida quando o espaço livre do volume atingir 20% ou todos os arquivos acessados ou modificados dentro de 7 dias forem locais.
* `-ThreadCount` Determina quantos arquivos podem ser rechamados em paralelo.
* `-PerFileRetryCount`determina com que frequência uma recall será tentada de um arquivo bloqueado no momento.
* `-PerFileRetryDelaySeconds`determina o tempo em segundos entre tentativas de recuperação e sempre deve ser usado em combinação com o parâmetro anterior.

Exemplo:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Se o volume local que hospeda o servidor não tiver espaço livre suficiente para realizar o recall de todos os dados em camadas, o cmdlet `Invoke-StorageSyncFileRecall` falha.  

> [!Note]
> Para recuperar os arquivos que foram em camadas, a largura de banda da rede deve ser de pelo menos 1 Mbps. Se a largura de banda da rede for menor que 1 Mbps, os arquivos poderão falhar ao se recuperar com um erro de tempo limite.

## <a name="next-steps"></a>Próximas etapas
* [Perguntas frequentes sobre os Arquivos do Azure](storage-files-faq.md)
