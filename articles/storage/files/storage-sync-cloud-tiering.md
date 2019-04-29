---
title: Noções básicas sobre a Camada de Nuvem da Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba mais sobre a Camada de Nuvem do recurso de Sincronização de Arquivos do Azure
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 09/21/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1851e9b2bb5ff86583228136dee977001cf0a3fd
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763474"
---
# <a name="cloud-tiering-overview"></a>Visão geral da Camada de Nuvem
A camada de nuvem é um recurso opcional da Sincronização de Arquivos do Azure em que arquivos acessados frequentemente são armazenados em cache localmente no servidor, enquanto todos os outros arquivos são organizados em camadas para Arquivos do Azure com base nas configurações de política. Quando um arquivo está disposto em camadas, o filtro do sistema de arquivos da Sincronização de Arquivos do Azure (StorageSync.sys) substitui o arquivo localmente por um ponteiro ou ponto de nova análise. O ponto de nova análise representa uma URL para o arquivo nos Arquivos do Azure. Um arquivo em camadas tem o atributo "offline" e o atributo FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS definidos em NTFS, de modo que aplicativos de terceiros podem identificar com segurança os arquivos dispostos em camadas.
 
Quando um usuário abre um arquivo em camadas, a Sincronização de Arquivos do Azure recupera os dados de arquivo diretamente dos Arquivos do Azure, sem que o usuário precise saber que o arquivo está realmente armazenado no Azure. 
 
 > [!Important]  
 > A camada de nuvem não dá suporte a pontos de extremidade do servidor nos volumes de sistema do Windows e somente arquivos com tamanho maior que 64 KiB podem ser dispostos em camadas nos Arquivos do Azure.
    
A Sincronização de Arquivos do Azure não dá suporte à disposição em camadas para arquivos menores que 64 KiB, já que a sobrecarga de desempenho causada pela disposição em camadas e subsequente recuperação desses arquivos pequenos seria mais onerosa que a economia de espaço.

 > [!Important]  
 > Para recuperar arquivos que têm em camadas, a largura de banda de rede deve ser pelo menos 1 Mbps. Se a largura de banda de rede é menos de 1 Mbps, os arquivos podem falhar ao Lembre-se com um erro de tempo limite.

## <a name="cloud-tiering-faq"></a>Perguntas frequentes das Camadas de Nuvem

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Como a disposição em camadas de nuvem funciona?
O filtro do sistema de Sincronização de Arquivos do Azure cria um "mapa de calor" do seu namespace em cada ponto de extremidade do servidor. Ele monitora acessos (operações leitura e gravação) ao longo do tempo e, em seguida, com base na frequência de acessos e no quanto eles são recentes, atribui uma pontuação de calor a todos os arquivos. Um arquivo acessado com frequência que foi aberto recentemente é considerado frequente, enquanto um arquivo que quase não é usado e que não foi acessado por algum tempo é considerado frio. Quando o volume de arquivos em um servidor excede o limite de espaço livre do volume definido, ele fará a disposição dos arquivos mais frios em camadas nos Arquivos do Azure até que o percentual de espaço livre necessário seja alcançado.

Nas versões 4.0 e superiores do agente de Sincronização de Arquivos do Azure, é possível especificar adicionalmente uma política de data em cada ponto de extremidade do servidor que classificará os arquivos não acessados ou modificados em um determinado número de dias.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Como a política de disposição em camadas do espaço livre no volume funciona?
O espaço livre no volume é a quantidade de espaço livre que você deseja reservar no volume no qual reside um ponto de extremidade do servidor. Por exemplo, se o espaço livre do volume estiver definido como 20% em um volume que tenha um ponto de extremidade do servidor, até 80% do espaço do volume será ocupado pelos arquivos acessados mais recentemente, com quaisquer arquivos restantes que não caibam nesse espaço sendo dispostos em camadas no Azure. O espaço livre no volume aplica-se no nível do volume, em vez de no nível de diretórios individuais ou de grupos de sincronização. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Como a política de disposição em camadas para espaço livre no volume funciona com relação a novos pontos de extremidade do servidor?
Quando um ponto de extremidade do servidor é provisionado recentemente e conectado a um compartilhamento de arquivos do Azure, o servidor primeiro eliminará o namespace e, em seguida, eliminará os arquivos reais, até atingir seu limite de espaço livre do volume. Esse processo é também conhecido como recuperação de desastre rápida ou restauração de namespace rápida.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Como o espaço livre no volume é interpretado quando tenho vários pontos de extremidade do servidor em um volume?
Quando há mais de um ponto de extremidade do servidor em um volume, o limite de espaço livre no volume efetivo é o maior espaço livre do volume especificado em qualquer ponto de extremidade do servidor nesse volume. Os arquivos serão colocados em camadas de acordo com seus padrões de uso, independentemente do ponto de extremidade do servidor ao qual pertencem. Por exemplo, se houver dois pontos de extremidade do servidor em um volume, Endpoint1 e Endpoint2, em que o Endpoint1 tenha um limite de espaço livre no volume de 25% e o Endpoint2 tenha um limite de espaço livre no volume de 50%, o limite de espaço livre no volume dos dois pontos de extremidade do servidor será de 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Como a política de camada de data funciona em conjunto com a política de camada de espaço livre no volume? 
Ao habilitar a camada de nuvem em um ponto de extremidade do servidor, você define uma política de espaço livre no volume. Ela sempre tem precedência sobre qualquer outra política, incluindo a política de datas. Opcionalmente, é possível habilitar uma política de data para cada ponto de extremidade de servidor nesse volume, o que significa que somente os arquivos acessados (isto é, lidos ou gravados) dentro do intervalo de dias que essa política descreve serão mantidos locais, com quaisquer arquivos obsoletos em camadas. Lembre-se de que a política de espaço livre no volume sempre terá precedência e, quando não houver espaço livre suficiente no volume para reter o mesmo número de arquivos, conforme descrito pela política de data, a Sincronização de Arquivos do Azure continuará a disposição em camadas dos arquivos mais obsoletos até que a porcentagem de espaço livre do volume seja atingida.

Por exemplo, digamos que você tenha uma política de camada com base em data de 60 dias e uma política de espaço livre no volume de 20%. Se, depois de aplicar a política de data, houver menos de 20% de espaço livre no volume, a política de espaço livre no volume será ativada e substituirá a política de data. Isso resultará em mais arquivos em camadas, de modo que a quantidade de dados mantidos no servidor poderá ser reduzida de 60 dias para 45 dias. Por outro lado, essa política forçará a colocação em camadas de arquivos fora do intervalo de tempo, mesmo que não tenha atingido o limite de espaço livre e, portanto, um arquivo com 61 dias será colocado em camadas mesmo que o volume esteja vazio.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Como determino a quantidade apropriada de espaço livre no volume?
A quantidade de dados que você deve manter armazenada localmente é determinada por alguns fatores: a largura de banda, o padrão de acesso do seu conjunto de dados e o orçamento. Se você tiver uma conexão com baixa largura de banda, talvez você queira manter uma parte maior de seus dados localmente para garantir que haja um atraso mínimo para seus usuários. Caso contrário, você pode baseá-lo na taxa de rotatividade durante um determinado período. Por exemplo, se você sabe que cerca de 10% do seu conjunto de dados de 1 TB são alterados ou são ativamente acessados a cada mês, então talvez você queira manter 100 GB armazenados localmente, de modo que você não fique recuperando arquivos com frequência. Se o volume é de 2 TB, é melhor que você mantenha 5% (ou 100 GB) localmente, o que significa que o restante de 95% é o percentual de espaço livre do volume. No entanto, é recomendável que você adicione um buffer para levar em conta os períodos de maior variação – em outras palavras, começando com um percentual menor de espaço livre no volume e, depois, ajustando-o mais tarde, se necessário. 

Manter mais dados armazenados localmente significará custos de saída menores, já que menos arquivos serão recuperados do Azure, mas também exigirá que você mantenha uma quantidade maior de armazenamento local, o que terá o seu próprio custo. Quando você tem uma instância da Sincronização de Arquivos do Azure implantada, você pode examinar a saída de sua conta de armazenamento para medir aproximadamente se as configurações de espaço livre do volume estão apropriadas para seu uso. Supondo que a conta de armazenamento contém apenas o ponto de extremidade de nuvem da Sincronização de Arquivos do Azure (ou seja, seu compartilhamento de sincronização), então uma saída alta significa que muitos arquivos estão sendo recuperados da nuvem, e você deve considerar aumentar o seu cache local.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Eu adicionei um novo ponto de extremidade de servidor. Quanto tempo levará até que meus arquivos estejam nessa camada de servidor?
Nas versões 4.0 e superiores do agente de Sincronização de Arquivos do Azure, depois que os arquivos forem carregados no compartilhamento de arquivos do Azure, eles serão colocados em camadas de acordo com as políticas assim que a próxima sessão de camada for executada, o que acontece uma vez por hora. Em agentes mais antigos, a disposição em camadas pode levar até 24 horas para ocorrer.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Como saber se um arquivo foi distribuído em camadas?
Há várias maneiras de verificar se um arquivo foi colocado em camadas no compartilhamento de arquivos do Azure:
    
   *  **Verifique os atributos de arquivo no arquivo.**
     Clique com o botão direito do mouse em um arquivo, navegue até **Detalhes** e role para baixo até a propriedade **Atributos**. Um arquivo em camadas tem os seguintes atributos definidos:     
        
        | Carta de atributo | Atributo | Definição |
        |:----------------:|-----------|------------|
        | O  | Arquivo | Indica que o arquivo deve ter o backup feito pelo software de backup. Esse atributo é sempre definido independentemente de o arquivo estar em camadas ou totalmente armazenado no disco. |
        | P | Arquivos esparsos | Indica que o arquivo é um arquivo esparso. Um arquivo esparso é um tipo especializado de arquivo que o NTFS oferece para uso eficiente quando o arquivo no fluxo do disco está basicamente vazio. A Sincronização de Arquivos do Azure usa arquivos esparsos, porque um arquivo é totalmente em camadas ou parcialmente cancelado. Em um arquivo totalmente em camadas, o fluxo de arquivos é armazenado na nuvem. Em um arquivo que sofreu recall parcial, essa parte do arquivo já está no disco. Se o recall de um arquivo é feito totalmente em disco, a Sincronização de Arquivos do Azure o converte de um arquivo esparso em um arquivo regular. |
        | L | Ponto de nova análise | Indica que o arquivo tem um ponto de nova análise. Um ponto de nova análise é um ponteiro especial para ser usado por um filtro do sistema de arquivos. A Sincronização de Arquivos do Azure usa pontos de nova análise a fim de definir, para o filtro do sistema de arquivos da Sincronização de Arquivos do Azure (StorageSync.sys), o local na nuvem em que o arquivo está armazenado. Isso dá suporte a acesso contínuo. Os usuários não precisarão saber que a Sincronização de Arquivos do Azure está sendo usada ou como obter acesso ao arquivo em seu compartilhamento de arquivos do Azure. Quando o recall de um arquivo é totalmente feito, a Sincronização de Arquivos do Azure remove o ponto de nova análise do arquivo. |
        | O | Off-line | Indica que parte ou nenhum conteúdo do arquivo está armazenado em disco. Quando o recall de um arquivo é totalmente feito, a Sincronização de Arquivos do Azure remove esse atributo. |

        ![A caixa de diálogo Propriedades de um arquivo com a guia Detalhes selecionada](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Você pode ver os atributos de todos os arquivos em uma pasta adicionando o campo **Atributos** à exibição de tabela do Explorador de Arquivos. Para fazer isso, clique com o botão direito do mouse em uma coluna existente (por exemplo, **Tamanho**), selecione **Mais** e depois **Atributos** na lista suspensa.
        
   * **Use `fsutil` para verificar se há pontos de nova análise em um arquivo.**
       Conforme descrito na opção anterior, um arquivo em camadas sempre tem um conjunto de pontos de nova análise. Um ponteiro de nova análise é um ponteiro especial para o filtro de sistema de arquivos de Sincronização de Arquivos do Azure (StorageSync.sys). Para verificar se um arquivo tem um ponto de nova análise, execute o utilitário `fsutil` em um prompt de comandos com privilégios elevados ou em uma janela do PowerShell:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Se o arquivo tiver um ponto de nova análise, você deverá ver um **Valor de tag de nova análise: 0x8000001e**. Esse valor hexadecimal é o valor de ponto de nova análise pertencente à Sincronização de Arquivos do Azure. A saída também contém os dados da nova análise que representam o caminho para o arquivo em seu compartilhamento de arquivos do Azure.

        > [!WARNING]  
        > O comando do utilitário `fsutil reparsepoint` também tem a capacidade de excluir um ponto de nova análise. Não execute esse comando, a menos que a equipe de engenharia de Sincronização de Arquivos do Azure lhe solicite isso. A execução desse comando pode resultar em perda de dados. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Um arquivo que eu desejo usar foi distribuído em camadas. Como é possível fazer o recall do arquivo no disco para usá-lo localmente?
A maneira mais fácil de fazer o recall de um arquivo em disco é abri-lo. O filtro de sistema de arquivos da Sincronização de Arquivos do Azure (StorageSync.sys) baixa o arquivo do seu compartilhamento de arquivos do Azure perfeitamente, sem que você precise fazer nenhum trabalho. Para tipos de arquivo que podem ser lidos parcialmente, tais como arquivos zip ou multimídia, abrir um arquivo não resultará no download do arquivo inteiro.

Você também pode usar o PowerShell para forçar o recall de um arquivo. Essa opção poderá ser útil se você quiser fazer o recall de vários arquivos ao mesmo tempo, por exemplo, todos os arquivos dentro de uma pasta. Abra uma sessão do PowerShell para o nó de servidor em que a Sincronização de Arquivos do Azure está instalada e então execute os seguintes comandos do PowerShell:
    
    ```powershell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Por que, após usar a Sincronização de Arquivos do Azure, a propriedade *Tamanho em disco* de um arquivo não corresponde à propriedade *Tamanho*? 
O Explorador de Arquivos do Windows expõe duas propriedades para representar o tamanho de um arquivo: **Tamanho** e **Tamanho em disco**. O significado dessas propriedades difere um pouco. **Tamanho** representa o tamanho completo do arquivo. **Tamanho em disco** representa o tamanho do fluxo de arquivo que é armazenado no disco. Os valores dessas propriedades podem ser diferentes por vários motivos, como compactação, uso da eliminação de duplicação de dados ou da disposição em camadas na nuvem com a Sincronização de Arquivos do Azure. Se um arquivo está em camadas em um compartilhamento de arquivos do Azure, o tamanho em disco é zero porque o fluxo de arquivos está armazenado em seu compartilhamento de arquivos do Azure, não no disco. Também é possível que um arquivo esteja parcialmente em camadas (ou seja parcialmente em recall). Em um arquivo parcialmente hierárquico, parte do arquivo está no disco. Isso pode acontecer quando os arquivos são lidos parcialmente por aplicativos como players de multimídia ou utilitários de compactação. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Como posso forçar um arquivo ou diretório a ficar em camadas?
Quando habilitado, o recurso de disposição em camadas na nuvem dispõe os arquivos em camadas automaticamente com base no último acesso e na última modificação para alcançar o percentual de espaço livre no volume especificado no ponto de extremidade de nuvem. Às vezes, no entanto, talvez você queira forçar um arquivo manualmente a ser dividido em camadas. Isso pode ser útil se você salva um arquivo grande que não pretende usar novamente por um longo período e agora deseja usar o espaço livre no volume para outros arquivos ou pastas. Você pode forçar a disposição em camadas com os seguintes comandos do PowerShell:

    ```powershell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

## <a name="next-steps"></a>Próximas etapas
* [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
