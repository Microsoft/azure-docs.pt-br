---
title: Migrar dados para sincronização de arquivos do Azure usando o Azure Data Box e outros métodos
description: Migre dados em massa de uma maneira que seja compatível com a sincronização de arquivos do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d1ec5168b898d0aa75c12e6eb435e20c09de1929
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759374"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Migrar dados em massa para a sincronização de arquivos do Azure
Você pode migrar dados em massa para a sincronização de arquivos do Azure de duas maneiras:

* **Carregar os arquivos usando a sincronização de arquivos do Azure.** Esse é o método mais simples. Mover os arquivos localmente para o Windows Server 2012 R2 ou posterior e instale o agente de sincronização de arquivos do Azure. Depois de configurar a sincronização, os arquivos serão carregados do servidor. (Os nossos clientes tenham atualmente uma velocidade de carregamento médio de 1 TiB sobre cada dois dias.) Para garantir que o servidor não usar muita largura de banda para seu datacenter, você talvez queira configurar uma [agendamento de limitação de largura de banda](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Transfira seus arquivos off-line.** Se você não tiver largura de banda suficiente, você não poderá carregar arquivos no Azure em um período de tempo razoável. O desafio é a sincronização inicial de todo o conjunto de arquivos. Para superar esse desafio, use as ferramentas de migração em massa offline, como o [família do Azure Data Box](https://azure.microsoft.com/services/storage/databox). 

Este artigo explica como migrar arquivos off-line de forma que seja compatível com a sincronização de arquivos do Azure. Siga estas instruções para evitar conflitos de arquivo e para preservar seus arquivos e listas de controle de acesso (ACLs) de pasta e os carimbos de hora depois de habilitar a sincronização.

## <a name="online-migration-tools"></a>Ferramentas para migração online
O processo descrito neste artigo funciona não só para caixa de dados, mas também para outras ferramentas de migração off-line. Ele também funciona para ferramentas online como o AzCopy, Robocopy, ou ferramentas de parceiros e serviços. No entanto você superar inicial carregue o desafio, siga as etapas neste artigo para usar essas ferramentas de forma que seja compatível com a sincronização de arquivos do Azure.


## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Benefícios de usar uma ferramenta para transferir dados off-line
Aqui estão os principais benefícios de usar uma ferramenta de transferência, como a caixa de dados para migração offline:

- Você não precisa carregar todos os seus arquivos pela rede. Para esses namespaces, essa ferramenta poderia economizar tempo e largura de banda de rede significativo.
- Quando você usar a sincronização de arquivos do Azure, independentemente de qual ferramenta de transferência que você usar (caixa de dados, serviço de importação/exportação do Azure e assim por diante), seu servidor ao vivo carrega apenas os arquivos que alteram depois de mover os dados para o Azure.
- A sincronização de arquivos do Azure é sincronizado com as ACLs de arquivo e pasta, mesmo se a ferramenta de migração em massa offline não ACLs de transporte.
- Caixa de dados e sincronização de arquivos do Azure exigem sem tempo de inatividade. Quando você usa o Data Box para transferir dados para o Azure, você usa a largura de banda com eficiência e preserva a fidelidade do arquivo. Você também manter seu namespace atualizado, carregando apenas os arquivos que alterar depois de mover os dados para o Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Pré-requisitos para a transferência de dados offline
Você não deve habilitar a sincronização no servidor do qual que você está migrando antes de concluir a transferência de dados offline. Outras coisas a considerar antes de começar serão o seguinte:

- Se você planeja usar o Data Box para a migração em massa: Analise os [pré-requisitos de implantação do Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planeje a sua topologia de sincronização de arquivos do Azure final: [Planejar uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- Escolha a conta de armazenamento do Azure que conterá os compartilhamentos de arquivos com os quais você deseja sincronizar. Assegure-se de que sua migração em massa ocorra nos compartilhamentos de preparo temporários da mesma Conta de Armazenamento. A migração em massa só poderá ser ativada utilizando um compartilhamento final e um compartilhamento de preparo que residem na mesma conta de armazenamento.
- Uma migração em massa só pode ser utilizada quando você cria uma nova relação de sincronização com um local do servidor. Você não pode habilitar uma migração em massa com uma relação de sincronização existente.


## <a name="process-for-offline-data-transfer"></a>Processo de transferência de dados offline
Aqui está como configurar a sincronização de arquivos do Azure de forma que seja compatível com as ferramentas de migração em massa, como o Azure Data Box:

![Diagrama que mostra como configurar a sincronização de arquivos do Azure](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Etapa | Detalhes |
|---|---------------------------------------------------------------------------------------|
| ![Etapa 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Solicite o Data Box](../../databox/data-box-deploy-ordered.md). As ofertas de família do Data Box [vários produtos](https://azure.microsoft.com/services/storage/databox/data) para atender às suas necessidades. Quando você receber o Data Box, execute sua [documentação para copiar seus dados](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) a esse caminho UNC no Data Box:  *\\< DeviceIPAddres\>\<StorageAccountName_AzFile\> \<ShareName\>*. Aqui, *ShareName* é o nome do compartilhamento de preparo. Envie o Data Box para o Azure. |
| ![Etapa 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Aguarde até que os arquivos exibidos em compartilhamentos de arquivos do Azure que você escolheu como compartilhamentos de preparo temporários. *Não habilite a sincronização a esses compartilhamentos.* |
| ![Etapa 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Crie um novo compartilhamento vazio para cada compartilhamento de arquivos que a caixa de dados criada para você. Esse novo compartilhamento deve ser na mesma conta de armazenamento que o compartilhamento de dados de caixa. [Como criar um novo compartilhamento de arquivos do Azure](storage-how-to-create-file-share.md). |
| ![Etapa 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Criar um grupo de sincronização](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) em um serviço de sincronização de armazenamento. Referenciar o compartilhamento vazio como um ponto de extremidade de nuvem. Repita essa etapa para cada compartilhamento de arquivos do Data Box. [Configurar a sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md). |
| ![Etapa 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Adicione seu diretório do servidor ao vivo como um ponto de extremidade do servidor](storage-sync-files-deployment-guide.md#create-a-server-endpoint). No processo, especifique que você moveu os arquivos para o Azure e os compartilhamentos de preparo de referência. Você pode habilitar ou desabilitar as camadas conforme a necessidade de nuvem. Durante a criação de um ponto de extremidade do servidor no seu servidor ao vivo, fazer referência ao compartilhamento de preparo. Sobre o **Adicionar ponto de extremidade do servidor** folha, em **off-line de transferência de dados**, selecione **habilitado**e, em seguida, selecione o compartilhamento de preparo que deve estar na mesma conta de armazenamento como a nuvem ponto de extremidade. Aqui, a lista de compartilhamentos disponíveis é filtrada por conta de armazenamento e compartilhamentos que já não estão sincronizando. |

![Captura de tela da interface do usuário do portal do Azure, mostrando como habilitar a transferência de dados offline durante a criação de um novo ponto de extremidade do servidor](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sincronização do compartilhamento
Depois de criar seu ponto de extremidade do servidor, a sincronização será iniciado. O processo de sincronização determina se cada arquivo no servidor também existe no compartilhamento de preparo em que o Data Box depositado os arquivos. Se o arquivo existe lá, o processo de sincronização copia o arquivo do compartilhamento de preparo em vez de carregá-lo do servidor. Se o arquivo não existir no compartilhamento de preparo, ou se uma versão mais recente está disponível no servidor local, o processo de sincronização carrega o arquivo do servidor local.

> [!IMPORTANT]
> Você pode habilitar o modo de migração em massa somente durante a criação de um ponto de extremidade do servidor. Depois de estabelecer um ponto de extremidade do servidor, você não pode integrar dados em massa-migrado de um servidor de sincronização já dentro do namespace.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACLs e carimbos de data/hora em arquivos e pastas
A sincronização de arquivos do Azure garante que as ACLs de arquivo e pasta são sincronizadas do servidor ao vivo, mesmo se a ferramenta de migração em massa que você usou inicialmente não transporte ACLs. Por isso, o compartilhamento de preparo não precisa conter todas as ACLs de arquivos e pastas. Quando você habilita o recurso de migração de dados offline que você crie um novo ponto de extremidade do servidor, todas as ACLs de arquivo são sincronizadas no servidor. Os carimbos de hora recém-criado e modificados também são sincronizados.

## <a name="shape-of-the-namespace"></a>Forma do namespace
Quando você habilita a sincronização, o conteúdo do servidor determina a forma do namespace. Se os arquivos são excluídos do servidor local depois de concluir o instantâneo da caixa de dados e a migração, esses arquivos não mova para o namespace ao vivo, sincronizando. Mantenha-se no compartilhamento de preparo, mas eles não são copiados. Isso é necessário porque a sincronização mantém o namespace de acordo com o servidor ao vivo. O Data Box *instantâneo* é apenas uma preparação para a cópia de arquivos eficiente. Não é a autoridade para uma forma de namespace ao vivo.

## <a name="cleaning-up-after-bulk-migration"></a>Limpando após a migração em massa 
Como o servidor concluir a sincronização inicial do namespace, os arquivos de caixa de dados em massa-migrado usam o compartilhamento de arquivos de preparo. Sobre o **propriedades de ponto de extremidade do servidor** folha no portal do Azure, no **off-line de transferência de dados** seção, o status muda de **em andamento** para **concluído** . 

![Captura de tela da folha de propriedades de ponto de extremidade do servidor, em que os controles de status e desabilitar para transferência de dados off-line estão localizados](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Agora você pode limpar o compartilhamento de preparo para economizar custos:

1. Sobre o **propriedades de ponto de extremidade do servidor** folha, quando o status for **concluído**, selecione **desativar a transferência de dados offline**.
2. Considere a exclusão do compartilhamento de preparo para economizar custos. O compartilhamento de preparo provavelmente não contém as ACLs de arquivo e pasta, portanto, não é muito útil. Point-in-time para fins de backup, crie um real [instantâneo de compartilhamento de arquivos do Azure sincronize](storage-snapshots-files.md). Você pode [configurar o Backup do Azure para tirar instantâneos]( ../../backup/backup-azure-files.md) em um agendamento.

Desabilitar o modo de transferência de dados offline somente quando o estado é **concluído** ou quando você deseja cancelar devido a uma configuração incorreta. Se você desabilitar o modo durante a implantação, arquivos serão iniciado carregar a partir do servidor, mesmo se o compartilhamento de preparo ainda estiver disponível.

> [!IMPORTANT]
> Depois de desabilitar o modo de transferência de dados offline, você não pode habilitá-lo novamente, mesmo se o compartilhamento de preparação da migração em massa ainda está disponível.

## <a name="next-steps"></a>Próximas etapas
- [Planejar uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
