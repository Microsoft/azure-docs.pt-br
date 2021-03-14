---
title: Entender Sincronização de Arquivos do Azure camadas de nuvem | Microsoft Docs
description: Entenda a disposição em camadas da nuvem, um recurso opcional de Sincronização de Arquivos do Azure. Arquivos acessados com frequência são armazenados em cache localmente no servidor; outras estão em camadas para os arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9ca8df21e0221d364f75eaeb26f0b2961d7e0b08
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204292"
---
# <a name="cloud-tiering-overview"></a>Visão geral de camadas de nuvem
A camada de nuvem, um recurso opcional do Sincronização de Arquivos do Azure, diminui a quantidade de armazenamento local necessária ao mesmo tempo em que mantém o desempenho de um servidor de arquivos local.

Quando habilitado, esse recurso armazena somente os arquivos acessados com frequência (quente) no servidor local. Arquivos não acessados com pouca frequência (frio) são divididos em namespace (estrutura de arquivos e pastas) e conteúdo de arquivo. O namespace é armazenado localmente e o conteúdo do arquivo armazenado em um compartilhamento de arquivos do Azure na nuvem. 

Quando um usuário abre um arquivo em camadas, Sincronização de Arquivos do Azure rechama diretamente os dados de arquivo do compartilhamento de arquivos no Azure.

## <a name="how-cloud-tiering-works"></a>Como funciona a disposição em camadas de nuvem

### <a name="cloud-tiering-policies"></a>Políticas de camadas de nuvem
Quando você habilita a camada de nuvem, há duas políticas que podem ser definidas para informar Sincronização de Arquivos do Azure quando fazer a camada de arquivos frios: a **política de espaço livre do volume** e a **política de data**. 

#### <a name="volume-free-space-policy"></a>Política de espaço livre do volume
A **política de espaço livre do volume** informa sincronização de arquivos do Azure para nivelar arquivos frios para a nuvem quando uma determinada quantidade de espaço é consumida em seu disco local. 

Por exemplo, se a capacidade do disco local for de 200 GB e você quiser pelo menos 40 GB de sua capacidade de disco local para sempre permanecer livre, defina a política de espaço livre do volume como 20%. O espaço livre do volume se aplica no nível do volume, e não no nível de diretórios individuais ou pontos de extremidade do servidor. 

Para saber como a política de espaço livre do volume afeta os arquivos baixados inicialmente quando um novo ponto de extremidade do servidor é adicionado, (consulte a seção [sincronizar políticas que afetam a](#sync-policies-that-affect-cloud-tiering)disposição em camadas da nuvem).

#### <a name="date-policy"></a>Política de data
Com a **política de data**, os arquivos interessantes são em camadas para a nuvem se eles não foram acessados (ou seja, lidos ou gravados) por um número x de dias. Por exemplo, se você observou que os arquivos que ficaram mais de 15 dias sem serem acessados são normalmente arquivos de arquivamento, você deve definir sua política de data para 15 dias. 

Para obter mais exemplos de como a política de data e o espaço livre do volume funcionam em conjunto, consulte [escolher sincronização de arquivos do Azure políticas de camadas de nuvem](storage-sync-choose-cloud-tiering-policies.md).

### <a name="windows-server-data-deduplication"></a>Eliminação de duplicação de dados do Windows Server
A eliminação de duplicação de dados tem suporte em volumes que têm a camada de nuvem habilitada a partir do Windows Server 2016. Para obter mais detalhes, consulte [planejando uma implantação de sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#data-deduplication).

### <a name="cloud-tiering-heatmap"></a>Calor em camadas de nuvem
O Sincronização de Arquivos do Azure monitora o acesso aos arquivos (operações de leitura e gravação) ao longo do tempo e, com base no acesso frequente e recente, atribui uma pontuação de calor a cada arquivo. Ele usa essas pontuações para criar um "calor" de seu namespace em cada ponto de extremidade do servidor. Este calor é uma lista de todos os arquivos de sincronização em um local com camadas de nuvem habilitadas, ordenadas por sua pontuação de calor. Os arquivos acessados com frequência que foram abertos recentemente são considerados quentes, enquanto os arquivos que não foram pouco utilizados e não foram acessados por algum tempo são considerados interessantes. 

Para determinar a posição relativa de um arquivo individual nesse calor, o sistema usa o máximo de seus carimbos de data/hora, na seguinte ordem: máx (último tempo de acesso, última modificação do horário, hora da criação). 

Normalmente, o horário do último acesso é acompanhado e está disponível. No entanto, quando um novo ponto de extremidade do servidor é criado, com a camada de nuvem habilitada, não há tempo suficiente para observar o acesso ao arquivo. Se não houver nenhum último tempo de acesso válido, a hora da última modificação será usada em vez disso, para avaliar a posição relativa no calor.  

A política de data funciona da mesma maneira. Sem uma hora do último acesso, a política de data atuará na hora da última modificação. Se isso não estiver disponível, ele retornará ao tempo de criação de um arquivo. Ao longo do tempo, o sistema observará cada vez mais solicitações de acesso a arquivos e começará a usar automaticamente a hora do último acesso rastreado.

> [!Note]
> A disposição em camadas da nuvem não depende do recurso NTFS para controlar o último tempo de acesso. Esse recurso NTFS está desativado por padrão e, devido a considerações de desempenho, não recomendamos que você habilite manualmente esse recurso. A camada de nuvem controla o horário do último acesso separadamente.

### <a name="sync-policies-that-affect-cloud-tiering"></a>Políticas de sincronização que afetam a disposição em camadas da nuvem

Com o agente do Sincronização de Arquivos do Azure versão 11, há duas políticas de Sincronização de Arquivos do Azure adicionais que podem ser definidas que afetam a disposição em camadas da nuvem: **download inicial** e **rechamada proativa**.

#### <a name="initial-download"></a>Download inicial

Quando um servidor está se conectando a um compartilhamento de arquivos do Azure com arquivos, agora você pode decidir como deseja que o servidor Baixe inicialmente os dados de compartilhamento de arquivos. Quando a disposição em camadas da nuvem estiver habilitada, você terá duas opções. 

![Uma captura de tela do download inicial quando a disposição em camadas da nuvem está habilitada](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

A primeira opção é recuperar o namespace primeiro e, em seguida, recuperar o conteúdo do arquivo pelo carimbo de data/hora da última modificação, até que a capacidade do disco local seja atingida. Se você tiver espaço em disco suficiente e souber que os arquivos que foram modificados pela última vez devem ser armazenados em cache localmente, essa opção será ideal. Os arquivos que não podem ser armazenados localmente devido à falta de espaço em disco serão colocados em camadas.        

A segunda opção é primeiro recuperar o namespace e rechamar o conteúdo do arquivo somente quando acessado. Essa opção é melhor se você quiser minimizar a capacidade usada em seu disco local e desejar que os usuários decidam quais arquivos devem ser armazenados em cache localmente. Todos os arquivos serão iniciados como arquivos em camadas com essa opção.

![Uma captura de tela do download inicial quando a disposição em camadas da nuvem está desabilitada](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

Quando a disposição em camadas da nuvem está desabilitada, você tem uma terceira opção, que é evitar todos os arquivos em camadas. Nessa situação, os arquivos só aparecerão no servidor depois que eles forem totalmente baixados. Se você tiver aplicativos que exigem a presença de arquivos completos e não puder tolerar arquivos em camadas em seu namespace, isso será ideal.      

#### <a name="proactive-recalling"></a>Rechamada proativa

Quando um arquivo é criado ou modificado, você pode recuperar proativamente um arquivo nos servidores que você especificar. Isso torna o arquivo novo ou modificado prontamente disponível para consumo em cada servidor especificado. 

Por exemplo, uma empresa distribuída globalmente tem filiais nos EUA e na Índia. Nos operadores de informações de manhã (horário dos EUA), eles criam uma nova pasta e novos arquivos para um projeto totalmente novo e trabalham o dia todo. Sincronização de Arquivos do Azure sincronizará a pasta e os arquivos com o compartilhamento de arquivos do Azure (ponto de extremidade de nuvem). Os operadores de informações na Índia continuarão a trabalhar no projeto em seu fuso horário. Quando chegam na manhã, o servidor local Sincronização de Arquivos do Azure habilitado na Índia precisa ter esses novos arquivos disponíveis localmente, de modo que a equipe da Índia possa trabalhar com eficiência em um cache local. Habilitar esse modo impede que o acesso inicial do arquivo seja mais lento devido a uma recall sob demanda e permite que o servidor retorne proativamente os arquivos assim que eles foram alterados ou criados no compartilhamento de arquivos do Azure.

Se os arquivos recuperados para o servidor não forem realmente necessários localmente, a recuperação desnecessária poderá aumentar o tráfego de saída e os custos. Portanto, habilite apenas a rechamada proativa quando você sabe que preencher previamente o cache de um servidor com alterações recentes da nuvem terá um efeito positivo sobre os usuários ou aplicativos que usam os arquivos nesse servidor. 

Habilitar o cancelamento proativo também pode resultar no aumento do uso da largura de banda no servidor e pode fazer com que outros conteúdos relativamente novos no servidor local sejam agressivos em camadas devido ao aumento nos arquivos que estão sendo recuperados. Por sua vez, isso pode levar a mais recuperações se os arquivos que estão sendo em camadas são considerados quentes por servidores. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Uma imagem que mostra o comportamento de download do compartilhamento de arquivos do Azure para um ponto de extremidade de servidor atualmente em vigor e um botão para abrir um menu que permite alterá-lo.":::

Para obter mais detalhes sobre a rechamada proativa, consulte [implantar sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Comportamento de arquivo em camadas versus localmente armazenado em cache

Camadas de nuvem é a separação entre o namespace (a hierarquia de arquivos e pastas, bem como propriedades de arquivo) e o conteúdo do arquivo. 

#### <a name="tiered-file"></a>Arquivo em camadas

Para arquivos em camadas, o tamanho em disco é zero, pois o conteúdo do arquivo em si não está sendo armazenado localmente. Quando um arquivo está em camadas, o StorageSync.sys (filtro do sistema de arquivos Sincronização de Arquivos do Azure) substitui o arquivo localmente por um ponteiro (ponto de nova análise). O ponto de nova análise representa uma URL para o arquivo no compartilhamento de arquivos do Azure. Um arquivo em camadas tem o atributo "offline" e o atributo FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS definidos em NTFS, de modo que aplicativos de terceiros podem identificar com segurança os arquivos dispostos em camadas.   

![Uma captura de tela das propriedades de um arquivo quando ele é em camadas somente de namespace.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>Arquivo armazenado em cache localmente

Por outro lado, para um arquivo armazenado em um servidor de arquivos local, o tamanho em disco é igual ao tamanho lógico do arquivo, já que todo o arquivo (atributos de arquivo + conteúdo do arquivo) é armazenado localmente.     

![Uma captura de tela das propriedades de um arquivo quando não é um namespace em camadas + conteúdo de arquivo.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

Também é possível que um arquivo esteja parcialmente em camadas (ou seja parcialmente em recall). Em um arquivo parcialmente hierárquico, parte do arquivo está no disco. Isso pode ocorrer quando os arquivos são lidos parcialmente por aplicativos como players de multimídia ou utilitários de zip que dão suporte ao acesso de streaming aos arquivos. Sincronização de Arquivos do Azure é inteligente e recupera apenas as informações solicitadas do compartilhamento de arquivos do Azure conectado.

> [!NOTE]
> Tamanho representa o tamanho lógico do arquivo. Tamanho em disco representa o tamanho físico do fluxo de arquivos armazenado no disco.

## <a name="next-steps"></a>Próximas etapas
* [Escolha Sincronização de Arquivos do Azure políticas de camadas de nuvem](storage-sync-choose-cloud-tiering-policies.md)
* [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)

