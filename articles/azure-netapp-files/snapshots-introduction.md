---
title: Como os instantâneos de Azure NetApp Files funcionam | Microsoft Docs
description: Explica como os instantâneos do Azure NetApp Files funcionam, incluindo maneiras de criar instantâneos, maneiras de restaurar instantâneos, como usar instantâneos em configurações de replicação entre regiões.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 526ef0af08833954aef4136716930cec0df40eea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625240"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Como funcionam os instantâneos do Azure NetApp Files

Este artigo explica como Azure NetApp Files os instantâneos funcionam. A tecnologia de instantâneos Azure NetApp Files fornece estabilidade, escalabilidade e capacidade de recuperação mais rápida, sem impacto no desempenho. A tecnologia de instantâneos de Azure NetApp Files fornece a base para soluções de proteção de dados, incluindo restaurações de arquivo único, restaurações de volume e clones e replicação entre regiões. 

Para obter as etapas sobre como usar instantâneos de volume, consulte [gerenciar instantâneos usando Azure NetApp files](azure-netapp-files-manage-snapshots.md). Para obter considerações sobre o gerenciamento de instantâneos na replicação entre regiões, consulte [requisitos e considerações para usar a replicação entre regiões](cross-region-replication-requirements-considerations.md).

## <a name="what-volume-snapshots-are"></a>Quais instantâneos de volume são  

Um instantâneo Azure NetApp Files é uma imagem de sistema de arquivos de ponto no tempo (volume). Ele pode servir como um backup online ideal. Você pode usar um instantâneo para [criar um novo volume](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume), [restaurar um arquivo](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)ou [reverter um volume](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert). Em dados de aplicativos específicos armazenados em volumes Azure NetApp Files, podem ser necessárias etapas extras para garantir a consistência do aplicativo.  

Instantâneos de baixa sobrecarga são possibilitados pelos recursos exclusivos da tecnologia de virtualização de volume subjacente que faz parte do Azure NetApp Files. Assim como um banco de dados, essa camada usa ponteiros para os blocos de dados reais em disco. Mas, ao contrário de um banco de dados, ele não reescreve os blocos existentes; Ele grava os dados atualizados em um novo bloco e altera o ponteiro. Um instantâneo Azure NetApp Files simplesmente manipula ponteiros de bloco, criando uma exibição "congelada" e somente leitura de um volume que permite que os aplicativos acessem versões mais antigas de arquivos e hierarquias de diretório sem programação especial. Os blocos de dados reais não são copiados. Assim, os instantâneos são eficientes no tempo necessário para criá-los; Eles são quase instantâneos, independentemente do tamanho do volume. Os instantâneos também são eficientes no espaço de armazenamento. Eles não consomem nenhum espaço e somente os blocos Delta entre os instantâneos e o volume ativo são mantidos. 

Os seguintes diagramas ilustram os conceitos:  

![Diagramas que mostram os principais conceitos de instantâneos](../media/azure-netapp-files/snapshot-concepts.png)

Nos diagramas, um instantâneo é obtido na Figura 1a. Na figura 1B, os dados alterados são gravados em um *novo bloco* e o ponteiro é atualizado. Mas o ponteiro de instantâneo ainda aponta para o *bloco escrito anteriormente*, oferecendo uma exibição dinâmica e histórica dos dados. Outro instantâneo é obtido na figura 1C. Agora você tem acesso a três gerações de dados (os dados dinâmicos, o instantâneo 2 e o instantâneo 1, em ordem de idade), sem ocupar o espaço do volume que três cópias completas exigirão. 

Um instantâneo usa apenas uma cópia dos metadados de volume (*tabela de inode*). Leva apenas alguns segundos para criar, independentemente do tamanho do volume, da capacidade usada ou do nível de atividade no volume. Portanto, tirar um instantâneo de um volume 100-TiB leva o mesmo tempo (ao lado de zero) como tirar um instantâneo de um volume 100-GiB. Depois que um instantâneo é criado, as alterações nos arquivos de dados são refletidas na versão ativa dos arquivos, como de costume.

Enquanto isso, os blocos de dados que são apontados de um instantâneo permanecem estáveis e imutáveis. Devido à natureza de "redirecionamento na gravação" de Azure NetApp Files volumes, um instantâneo não provoca nenhuma sobrecarga de desempenho e, em si, não consome nenhum espaço. Você pode armazenar até 255 instantâneos por volume ao longo do tempo, todos acessíveis como somente leitura e versões online dos dados, consumindo uma pequena capacidade como o número de blocos alterados entre cada instantâneo. Os blocos modificados são armazenados no volume ativo. Os blocos apontados para os instantâneos são mantidos (como somente leitura) no volume para fins de proteção, para serem redefinidos somente quando todos os ponteiros (no volume ativo e instantâneos) tiverem sido apagados. Portanto, a utilização de volume aumentará ao longo do tempo, por novos blocos de dados ou blocos de dados (modificados) mantidos em instantâneos.

 O diagrama a seguir mostra os instantâneos de um volume e o espaço usado ao longo do tempo: 

![Diagrama que mostra os instantâneos de um volume e o espaço usado ao longo do tempo](../media/azure-netapp-files/snapshots-used-space-over-time.png)

Como um instantâneo de volume registra apenas as alterações de bloco desde o último instantâneo, ele fornece os seguintes benefícios principais:

* Os instantâneos são ***eficientes no armazenamento***.   
    Os instantâneos consomem espaço mínimo de armazenamento porque não copia os blocos de dados do volume inteiro. Dois instantâneos tirados em sequência diferem apenas pelos blocos adicionados ou alterados no intervalo de tempo entre os dois. Esse comportamento incremental de bloco limita o consumo de capacidade de armazenamento associado. Muitas implementações alternativas de instantâneos consomem volumes de armazenamento iguais ao sistema de arquivos ativo, aumentando os requisitos de capacidade de armazenamento. Dependendo das taxas de alteração no *nível de bloco* diário do aplicativo, os instantâneos de Azure NetApp files consumirão mais ou menos capacidade, mas somente nos dados alterados. O consumo de instantâneo diário médio varia de apenas 1-5% da capacidade de volume usada para muitos volumes de aplicativos ou até 20-30% para volumes como SAP HANA volumes de banco de dados. Certifique-se de [monitorar seu volume e o uso de instantâneo](azure-netapp-files-metrics.md#volumes) para consumo de capacidade de instantâneo em relação ao número de instantâneos criados e mantidos.   

* Os instantâneos são ***rápidos de criar, replicar, restaurar ou clonar***.   
    Leva apenas alguns segundos para criar, replicar, restaurar ou clonar um instantâneo, independentemente do tamanho do volume e do nível de atividades. Você pode criar um instantâneo de volume [sob demanda](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume). Você também pode usar [políticas de instantâneo](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) para especificar quando Azure NetApp files deve criar automaticamente um instantâneo e quantos instantâneos devem ser mantidos em um volume.  A consistência do aplicativo pode ser obtida por meio da orquestração de instantâneos com a camada de aplicativo, por exemplo, usando a [ferramenta AzAcSnap](azacsnap-introduction.md) para SAP Hana.

* Os instantâneos não têm impacto sobre o ***desempenho*** do volume.   
    Devido à natureza de "redirecionamento na gravação" da tecnologia de subposição, armazenar ou reter Azure NetApp Files instantâneos não tem impacto no desempenho, mesmo com atividade de dados pesada. A exclusão de um instantâneo também tem pouco ou nenhum impacto no desempenho na maioria dos casos. 

* Os instantâneos fornecem ***escalabilidade*** , pois podem ser criados com frequência e muitos podem ser retidos.   
    Os volumes Azure NetApp Files dão suporte a até 255 instantâneos. A capacidade de armazenar um grande número de instantâneos criados com frequência de baixo impacto aumenta a probabilidade de que a versão desejada de dados possa ser recuperada com êxito.

* Os instantâneos fornecem ***visibilidade do usuário** _ e _ * recuperação de _arquivos_* *.   
O alto desempenho, a escalabilidade e a estabilidade de Azure NetApp Files tecnologia de instantâneo significa que ele fornece um backup online ideal para a recuperação controlada pelo usuário. Os instantâneos podem se tornar acessíveis ao usuário para fins de restauração de arquivo, diretório ou volume. Soluções adicionais permitem copiar backups para armazenamento offline ou [replicar entre regiões](cross-region-replication-introduction.md) para fins de retenção ou recuperação de desastre.

## <a name="ways-to-create-snapshots"></a>Maneiras de criar instantâneos   

Você pode usar vários métodos para criar e manter instantâneos:

* Manualmente (sob demanda), usando:   
    * As ferramentas [portal do Azure](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), [API REST](/rest/api/netapp/snapshots), [CLI do Azure](/cli/azure/netappfiles/snapshot)ou [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot)
    * Scripts (veja [exemplos](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts))

* Automatizado, usando:
    * Políticas de instantâneo, por meio das ferramentas [portal do Azure](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies), [API REST](/rest/api/netapp/snapshotpolicies), [CLI do Azure](/cli/azure/netappfiles/snapshot/policy)ou [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy)
    * Ferramentas de instantâneo consistente com o aplicativo, como [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>Como os volumes e instantâneos são replicados entre regiões para DR  

O Azure NetApp Files dá suporte à [replicação entre regiões](cross-region-replication-introduction.md) para fins de Dr (recuperação de desastre). Azure NetApp Files replicação entre regiões usa a tecnologia SnapMirror. Somente os blocos alterados são enviados pela rede em um formato compactado e eficiente. Depois que uma replicação entre regiões é iniciada entre volumes, todo o conteúdo do volume (ou seja, os blocos de dados armazenados reais) é transferido apenas uma vez. Essa operação é chamada de *transferência de linha de base*. Após a transferência inicial, somente os blocos alterados (como capturados em instantâneos) são transferidos. O resultado é uma réplica 1:1 assíncrona do volume de origem, incluindo todos os instantâneos. Esse comportamento segue um mecanismo de replicação completo e incremental. Essa tecnologia minimiza a quantidade de dados necessária para replicar entre as regiões, economizando, portanto, os custos de transferência de dados. Ele também reduz o tempo de replicação. Você pode obter um RPO (objetivo de ponto de recuperação) menor, pois mais instantâneos podem ser criados e transferidos com mais frequência com transferências de dados limitadas. Além disso, elimina a necessidade de mecanismos de replicação baseados em host, evitando o custo de licença de software e de máquina virtual.

O diagrama a seguir mostra o tráfego de instantâneo em cenários de replicação entre regiões: 

![Diagrama que mostra o tráfego de instantâneo em cenários de replicação entre regiões](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>Maneiras de restaurar dados de instantâneos  

A tecnologia de instantâneos de Azure NetApp Files melhora muito a frequência e a confiabilidade dos backups. Isso incorre em sobrecarga mínima de desempenho e pode ser criado com segurança em um volume ativo. Os instantâneos de Azure NetApp Files permitem restaurações quase instantâneas, seguras e, opcionalmente, gerenciadas pelo usuário. Esta seção descreve várias maneiras em que os dados podem ser acessados ou restaurados de Azure NetApp Files instantâneos.

### <a name="restoring-files-or-directories-from-snapshots"></a>Restaurando arquivos ou diretórios de instantâneos 

Se a [visibilidade do caminho do instantâneo](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) não estiver definida como `hidden` , os usuários poderão acessar diretamente os instantâneos para se recuperar da exclusão acidental, corrupção ou modificação de seus dados. A segurança de arquivos e diretórios é mantida no instantâneo e os instantâneos são somente leitura por design. Assim, a restauração é segura e simples. 

O diagrama a seguir mostra o acesso ao arquivo ou ao diretório para um instantâneo: 

![Diagrama que mostra o acesso de arquivo ou diretório a um instantâneo](../media/azure-netapp-files/snapshot-file-directory-access.png)

No diagrama, o instantâneo 1 consome apenas os blocos Delta entre o volume ativo e o momento da criação do instantâneo. Mas quando você acessar o instantâneo por meio do caminho do instantâneo de volume, os dados serão *exibidos* como se fosse a capacidade de volume total no momento da criação do instantâneo. Ao acessar as pastas de instantâneos, os usuários podem restaurar dados automaticamente copiando arquivos e diretórios para fora de um instantâneo de escolha.

Da mesma forma, os instantâneos em volumes de replicação entre regiões de destino podem ser acessados somente leitura para recuperação de dados na região de recuperação de desastre.  

O diagrama a seguir mostra o acesso ao instantâneo em cenários de replicação entre regiões: 

![Diagrama que mostra o acesso ao instantâneo na replicação entre regiões](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

Consulte [restaurar um arquivo de um instantâneo usando um cliente](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) sobre como restaurar arquivos ou diretórios individuais de instantâneos.

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>Restaurando (clonando) um instantâneo para um novo volume

Você pode restaurar Azure NetApp Files instantâneos para um volume separado e independente. Essa operação é quase instantânea, independentemente do tamanho do volume e da capacidade consumida. O volume recém-criado está quase imediatamente disponível para acesso, enquanto os blocos de dados reais de volume e instantâneo estão sendo copiados. Dependendo do tamanho e da capacidade do volume, esse processo pode levar um tempo considerável durante o qual o volume pai e o instantâneo não podem ser excluídos. No entanto, o volume já pode ser acessado após a criação inicial, enquanto o processo de cópia está em andamento em segundo plano. Esse recurso permite a criação rápida de volumes para recuperação de dados ou clonagem de volume para teste e desenvolvimento. Por natureza do processo de cópia de dados, o consumo do pool de capacidade de armazenamento dobrará quando a restauração for concluída e o novo volume mostrará a capacidade total ativa do instantâneo original. Depois que esse processo for concluído, o volume será independente e desassociado com o volume original, e os volumes de origem e o instantâneo poderão ser gerenciados ou removidos independentemente do novo volume.

O diagrama a seguir mostra um novo volume criado pela restauração (clonagem) de um instantâneo:   

![Diagrama que mostra um novo volume criado pela restauração de um instantâneo](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

As mesmas operações podem ser executadas em instantâneos replicados para um volume de recuperação de desastres (DR). Qualquer instantâneo pode ser restaurado para um novo volume, mesmo quando a replicação entre regiões permanece ativa ou em andamento. Esse recurso permite a criação sem interrupções de ambientes de teste e desenvolvimento em uma região de DR, colocando os dados a serem usados, enquanto os volumes replicados seriam usados apenas para fins de DR. Esse caso de uso permite que o teste e o desenvolvimento sejam isolados da produção, eliminando o impacto potencial em ambientes de produção.  

O diagrama a seguir mostra a restauração de volume (clonagem) usando o instantâneo de volume de destino de DR enquanto a replicação entre regiões está ocorrendo:  

![Diagrama que mostra a restauração de volume usando o instantâneo de volume de destino de DR](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

Consulte [restaurar um instantâneo em um novo volume](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) sobre as operações de restauração de volume.

### <a name="restoring-reverting-a-snapshot-in-place"></a>Restaurando (revertendo) um instantâneo in-loco

Em alguns casos, como o novo volume consumirá a capacidade de armazenamento, a criação de um novo volume de um instantâneo pode não ser necessária ou apropriada. Para recuperar-se de dados corrompidos rapidamente (por exemplo, danos ou ataques de ransomware), talvez seja mais apropriado restaurar um instantâneo dentro do próprio volume. Essa operação pode ser feita usando a funcionalidade de reversão de instantâneo Azure NetApp Files. Essa funcionalidade permite que você reverta rapidamente um volume para o estado em que ele estava quando um determinado instantâneo foi tirado. Na maioria dos casos, reverter um volume é muito mais rápido do que restaurar arquivos individuais de um instantâneo para o sistema de arquivos ativo, especialmente em volumes grandes e TiB. 

A reversão de um instantâneo de volume é quase instantânea e leva apenas alguns segundos para ser concluída, mesmo para os maiores volumes. Os metadados do volume ativo (*tabela de inode*) são substituídos pelos metadados do instantâneo a partir do momento da criação do instantâneo, revertendo o volume para esse momento específico. Nenhum bloco de dados precisa ser copiado para que a reversão entre em vigor. Assim, é mais eficiente do que restaurar um instantâneo para um novo volume. 

O diagrama a seguir mostra uma reversão de volume para um instantâneo anterior:  

![Diagrama que mostra uma reversão de volume para um instantâneo anterior](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> Os dados de sistema de arquivos ativos que foram gravados e instantâneos que foram feitos depois que o instantâneo selecionado foi obtido serão perdidos. A operação de reversão de instantâneo substituirá todos os dados no volume de destino pelos dados no instantâneo selecionado. Você deve prestar atenção ao conteúdo do instantâneo e à data de criação ao selecionar um instantâneo. Não é possível desfazer a operação de reversão de instantâneo.  

Consulte [reverter um volume usando o instantâneo REVERT](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert) sobre como usar esse recurso.

## <a name="how-snapshots-are-deleted"></a>Como os instantâneos são excluídos 

Os instantâneos consomem a capacidade de armazenamento. Como tal, eles normalmente não são mantidos indefinidamente. Para proteção de dados, retenção e capacidade de recuperação, vários instantâneos (criados em vários pontos no tempo) geralmente são mantidos online por uma determinada duração, dependendo do RPO, RTO e requisitos de SLA de retenção. No entanto, os instantâneos mais antigos geralmente não precisam ser mantidos no serviço de armazenamento e talvez precisem ser excluídos para liberar espaço. Qualquer instantâneo pode ser excluído (não necessariamente em ordem de criação) por um administrador a qualquer momento. 

> [!IMPORTANT]
> A operação de exclusão de instantâneo não pode ser desfeita. Você deve reter cópias offline do volume para fins de proteção e retenção de dados. 

Quando um instantâneo é excluído, todos os ponteiros desse instantâneo para os blocos de dados existentes serão removidos. Quando um bloco de dados não tem mais ponteiros apontando para ele (pelo volume ativo ou outros instantâneos no volume), o bloco de dados é retornado para o espaço livre do volume para uso futuro. Portanto, a remoção de instantâneos geralmente libera mais capacidade em um volume do que excluir dados do volume ativo, pois os blocos de dados geralmente são capturados em instantâneos criados anteriormente. 

O diagrama a seguir mostra o efeito sobre o consumo de armazenamento da exclusão do instantâneo 3 de um volume:  

![Diagrama que mostra o efeito de consumo de armazenamento da exclusão de instantâneo](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

Certifique-se de [monitorar o consumo de volume e instantâneo](azure-netapp-files-metrics.md#volumes) e entender como o aplicativo, o volume ativo e o consumo de instantâneos interagem. 

Consulte [excluir instantâneos](azure-netapp-files-manage-snapshots.md#delete-snapshots) sobre como gerenciar a exclusão de instantâneo. Consulte [gerenciar políticas de instantâneo](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) sobre como automatizar esse processo.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar instantâneos por meio do Azure NetApp Files](azure-netapp-files-manage-snapshots.md)
* [Monitorar métricas de volume e instantâneo](azure-netapp-files-metrics.md#volumes)
* [Solucionar problemas de políticas de instantâneo](troubleshoot-snapshot-policies.md)
* [Limites de recursos do Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vídeo de instantâneos de Azure NetApp Files 101](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Visão geral do instantâneo de Azure NetApp Files](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)



