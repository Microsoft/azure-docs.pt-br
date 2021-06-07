---
title: Migrar para compartilhamentos de Arquivos do Azure
description: Saiba mais sobre as migrações para compartilhamentos de arquivos do Azure e encontre o guia de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 27056f39885949d52c9fcc0d1472033cfc8f9aa0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554863"
---
# <a name="migrate-to-azure-file-shares"></a>Migrar para compartilhamentos de Arquivos do Azure

Este artigo aborda os aspectos básicos de uma migração para compartilhamentos de arquivos do Azure.

Este artigo contém noções básicas de migração e uma tabela de guias de migração. Esses guias ajudam você a mover seus arquivos para compartilhamentos de arquivos do Azure. Os guias são organizados com base em onde estão os dados e em qual modelo de implantação (somente em nuvem ou híbrido) você está migrando.

## <a name="migration-basics"></a>Noções básicas de migração

O Azure tem vários tipos de armazenamento em nuvem disponíveis. Um aspecto fundamental das migrações de arquivos para o Azure é determinar qual opção de armazenamento do Azure é adequada para seus dados.

Os [compartilhamentos de arquivos do Azure](storage-files-introduction.md) são adequados para dados de arquivo de uso geral. Esses dados incluem tudo o que você usa em um compartilhamento SMB ou NFS local para o. Com o [sincronização de arquivos do Azure](storage-sync-files-planning.md), você pode armazenar em cache o conteúdo de vários compartilhamentos de arquivos do Azure em servidores que executam o Windows Server local.

Para um aplicativo que é executado atualmente em um servidor local, o armazenamento de arquivos em um compartilhamento de arquivos do Azure pode ser uma boa opção. Você pode mover o aplicativo para o Azure e usar compartilhamentos de arquivos do Azure como armazenamento compartilhado. Você também pode considerar os [discos do Azure](../../virtual-machines/managed-disks-overview.md) para esse cenário.

Alguns aplicativos de nuvem não dependem de SMB ou de acesso a dados locais de computador ou acesso compartilhado. Para esses aplicativos, o armazenamento de objeto como [BLOBs do Azure](../blobs/storage-blobs-overview.md) geralmente é a melhor opção.

A chave em qualquer migração é capturar toda a fidelidade do arquivo aplicável ao mover os arquivos de seu local de armazenamento atual para o Azure. A quantidade de fidelidade que a opção de armazenamento do Azure dá suporte e o quanto seu cenário exige também ajuda a escolher o armazenamento do Azure correto. Os dados de arquivo de uso geral dependem tradicionalmente dos metadados do arquivo. Os dados do aplicativo podem não.

Estes são os dois componentes básicos de um arquivo:

- **Fluxo de dados**: o fluxo de dados de um arquivo armazena o conteúdo do arquivo.
- **Metadados de arquivo**: os metadados de arquivo têm estes subcomponentes:
   * Atributos de arquivo como somente leitura
   * Permissões de arquivo, que podem ser referenciadas como *permissões NTFS* ou *ACLs de arquivo e pasta*
   * Carimbos de data/hora, principalmente os carimbos de data/hora da criação e da última modificação
   * Um fluxo de dados alternativo, que é um espaço para armazenar grandes quantidades de propriedades não padrão

A fidelidade do arquivo em uma migração pode ser definida como a capacidade de:

- Armazene todas as informações de arquivo aplicáveis na origem.
- Transfira arquivos com a ferramenta de migração.
- Armazene arquivos no armazenamento de destino da migração.

Para garantir que a migração continue sem problemas, identifique [a melhor ferramenta de cópia para suas necessidades](#migration-toolbox) e faça a correspondência de um destino de armazenamento com sua fonte.

Levando em conta as informações anteriores, você pode ver que o armazenamento de destino para arquivos de uso geral no Azure é [compartilhamentos de arquivos do Azure](storage-files-introduction.md).

Ao contrário do armazenamento de objetos em BLOBs do Azure, um compartilhamento de arquivos do Azure pode armazenar os metadados de arquivo nativamente. Os compartilhamentos de arquivos do Azure também preservam a hierarquia de arquivos e pastas, atributos e permissões. As permissões NTFS podem ser armazenadas em arquivos e pastas porque são locais.

Um usuário de Active Directory, que é o controlador de domínio local, pode acessar nativamente um compartilhamento de arquivos do Azure. Portanto, um usuário de Azure Active Directory Domain Services (AD DS do Azure). Cada uma usa sua identidade atual para obter acesso com base nas permissões de compartilhamento e nas ACLs de arquivo e pasta. Esse comportamento é semelhante a um usuário que se conecta a um compartilhamento de arquivos local.

O fluxo de dados alternativo é o aspecto principal da fidelidade do arquivo que atualmente não pode ser armazenado em um arquivo em um compartilhamento de arquivos do Azure. Ele é preservado localmente quando Sincronização de Arquivos do Azure é usado.

Saiba mais sobre a [autenticação do Azure ad](storage-files-identity-auth-active-directory-enable.md) e a [autenticação de AD DS do Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) para compartilhamentos de arquivos do Azure.

## <a name="migration-guides"></a>Guias de migração

A tabela a seguir lista os guias de migração detalhados.

Como usar a tabela:

1. Localize a linha do sistema de origem em que os arquivos estão armazenados no momento.

1. Escolha um destes destinos:

   - Uma implantação híbrida usando Sincronização de Arquivos do Azure para armazenar em cache o conteúdo dos compartilhamentos de arquivos do Azure no local
   - Compartilhamentos de arquivos do Azure na nuvem

   Selecione a coluna de destino que corresponde à sua escolha.

1. Dentro da interseção de origem e destino, uma célula de tabela lista os cenários de migração disponíveis. Selecione uma para vincular diretamente ao guia de migração detalhado.

Um cenário sem um link ainda não tem um guia de migração publicado. Marque esta tabela ocasionalmente para obter atualizações. Novos guias serão publicados quando estiverem disponíveis.

| Fonte | Destino: </br>Implantação híbrida | Destino: </br>Implantação somente em nuvem |
|:---|:--|:--|
| | Combinação de ferramentas:| Combinação de ferramentas: |
| Windows Server 2012 R2 e posterior | <ul><li>[Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)</li><li>[Sincronização de Arquivos do Azure e data box do Azure](storage-sync-offline-data-transfer.md)</li></ul> | <ul><li>Via RoboCopy para um compartilhamento de arquivos montado do Azure</li><li>Via Sincronização de Arquivos do Azure</li></ul> |
| Windows Server 2012 e anterior | <ul><li>Via Data box e Sincronização de Arquivos do Azure para o sistema operacional de servidor recente</li><li>Por meio do serviço de migração de armazenamento para um servidor recente com Sincronização de Arquivos do Azure, faça upload</li></ul> | <ul><li>Por meio do serviço de migração de armazenamento para um servidor recente com o Sincronização de Arquivos do Azure</li><li>Via RoboCopy para um compartilhamento de arquivos montado do Azure</li></ul> |
| NAS (armazenamento conectado à rede) | <ul><li>[Por meio de upload de Sincronização de Arquivos do Azure](storage-files-migration-nas-hybrid.md)</li><li>[Via Data Box + Sincronização de Arquivos do Azure](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>Via RoboCopy para um compartilhamento de arquivos montado do Azure</li></ul> |
| Linux/Samba | <ul><li>[Sincronização de Arquivos do Azure e RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Via RoboCopy para um compartilhamento de arquivos montado do Azure</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 ou dispositivo de nuvem StorSimple 8600 | <ul><li>[Por meio do serviço de nuvem de migração de dados dedicado](storage-files-migration-storsimple-8000.md)</li></ul> | |
| Dispositivo de nuvem StorSimple 1200 | <ul><li>[Via Sincronização de Arquivos do Azure](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>Caixa de ferramentas de migração

### <a name="file-copy-tools"></a>Ferramentas de cópia de arquivos

Há várias ferramentas de cópia de arquivos disponíveis da Microsoft e de outras. Para selecionar a ferramenta certa para seu cenário de migração, você deve considerar estas perguntas fundamentais:

* A ferramenta dá suporte aos locais de origem e de destino para sua cópia de arquivo?

* A ferramenta dá suporte ao seu caminho de rede ou protocolos disponíveis (como REST, SMB ou NFS) entre os locais de armazenamento de origem e de destino?

* A ferramenta preserva a fidelidade de arquivo necessária com suporte nos locais de origem e de destino?

    Em alguns casos, o armazenamento de destino não dá suporte à mesma fidelidade que sua fonte. Se o armazenamento de destino for suficiente para suas necessidades, a ferramenta deverá corresponder apenas aos recursos de fidelidade de arquivo do destino.

* A ferramenta tem recursos que permitem que ele se ajuste à sua estratégia de migração?

    Por exemplo, considere se a ferramenta permite minimizar o tempo de inatividade.
    
    Quando uma ferramenta dá suporte a uma opção para espelhar uma origem em um destino, você geralmente pode executá-la várias vezes na mesma origem e no mesmo destino, enquanto a origem permanece acessível.

    Na primeira vez que você executar a ferramenta, ela copiará a massa dos dados. Essa execução inicial pode durar um pouco. Ele geralmente dura mais do que você deseja colocar a fonte de dados offline para seus processos de negócios.

    Ao espelhar uma origem para um destino (como com o **Robocopy/Mir**), você pode executar a ferramenta novamente na mesma origem e no mesmo destino. A execução é muito mais rápida porque precisa transportar somente as alterações de origem que ocorrem após a execução anterior. Executar novamente uma ferramenta de cópia dessa maneira pode reduzir significativamente o tempo de inatividade.

A tabela a seguir classifica as ferramentas da Microsoft e sua adequação atual para compartilhamentos de arquivos do Azure:

| Recomendadas | Ferramenta | Suporte para compartilhamentos de arquivos do Azure | Preservação da fidelidade do arquivo |
| :-: | :-- | :---- | :---- |
|![Sim, recomendado](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | Com suporte. Os compartilhamentos de arquivos do Azure podem ser montados como unidades de rede. | Fidelidade total. * |
|![Sim, recomendado](media/storage-files-migration-overview/circle-green-checkmark.png)| Sincronização de Arquivos do Azure | Integrado nativamente aos compartilhamentos de arquivos do Azure. | Fidelidade total. * |
|![Sim, recomendado](media/storage-files-migration-overview/circle-green-checkmark.png)| Serviço de Migração de Armazenamento | Com suporte indiretamente. Os compartilhamentos de arquivos do Azure podem ser montados como unidades de rede em servidores de destino do SMS. | Fidelidade total. * |
|![Sim, recomendado](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy </br>versão 10,6 | Com suporte. | Não dá suporte à cópia da ACL raiz de origem, caso contrário, fidelidade total. * </br>[Saiba como usar o AzCopy com compartilhamentos de arquivos do Azure](../common/storage-use-azcopy-files.md) |
|![Sim, recomendado](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Com suporte. | O data Box dá suporte total aos metadados. |
|![Não recomendado totalmente](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Gerenciador de Armazenamento do Azure </br>versão 1,14 | Com suporte. | Não copia ACLs. Dá suporte a carimbos de data/hora.  |
|![Não recomendado](media/storage-files-migration-overview/circle-red-x.png)| Fábrica de dados do Azure | Com suporte. | Não copia metadados. |
|||||

*\* Fidelidade total: atende ou excede os recursos de compartilhamento de arquivos do Azure.*

### <a name="migration-helper-tools"></a>Ferramentas auxiliares de migração

Esta seção descreve as ferramentas que ajudam a planejar e executar migrações.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy da Microsoft Corporation

O RoboCopy é uma das ferramentas mais aplicáveis às migrações de arquivos. Ele é fornecido como parte do Windows. A principal [documentação do Robocopy](/windows-server/administration/windows-commands/robocopy) é um recurso útil para as várias opções dessa ferramenta.

#### <a name="treesize-from-jam-software-gmbh"></a>Árvores de EMPERRAmento da Software GmbH

Sincronização de Arquivos do Azure é dimensionado principalmente com o número de itens (arquivos e pastas) e não com a quantidade total de armazenamento. A ferramenta Treeize permite que você determine o número de itens nos volumes do Windows Server.

Você pode usar a ferramenta para criar uma perspectiva antes de uma [implantação de sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md). Você também pode usá-lo quando a camada de nuvem estiver envolvida após a implantação. Nesse cenário, você vê o número de itens e quais diretórios usam o cache do servidor mais.

A versão testada da ferramenta é a versão 4.4.1. Ele é compatível com arquivos em camadas de nuvem. A ferramenta não causará a recall de arquivos em camadas durante sua operação normal.

## <a name="next-steps"></a>Próximas etapas

1. Crie um plano para o qual implantação de compartilhamentos de arquivos do Azure (somente em nuvem ou híbrido) você deseja.
1. Examine a lista de guias de migração disponíveis para encontrar o guia detalhado que corresponde à sua origem e implantação de compartilhamentos de arquivos do Azure.

Mais informações sobre as tecnologias de arquivos do Azure mencionadas neste artigo:

* [Visão geral do compartilhamento de arquivos do Azure](storage-files-introduction.md)
* [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
* [Sincronização de Arquivos do Azure: camadas de nuvem](storage-sync-cloud-tiering-overview.md)