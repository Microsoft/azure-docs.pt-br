---
title: Diretrizes de recuperação de desastre para avere vFXT para Azure
description: Como proteger dados no avere vFXT do Azure contra exclusões ou interrupções acidentais
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 5cc4678b082aa5a4a3f90518ff8fac448f414f1a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92342239"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Diretrizes de recuperação de desastre para avere vFXT para Azure

Este artigo descreve estratégias para proteger seu avere vFXT para o fluxo de trabalho do Azure e fornece diretrizes para fazer backup de dados para que você possa se recuperar de acidentes ou interrupções.

O avere vFXT para Azure armazena temporariamente dados em seu cache. Os dados são armazenados a longo prazo em sistemas de armazenamento de back-end-sistemas de hardware locais, contêineres de armazenamento de BLOBs do Azure ou ambos.

Para se proteger contra interrupções e possível perda de dados, considere estas quatro áreas:

* Proteção contra tempo de inatividade se um avere vFXT para o sistema do Azure ficar indisponível
* Protegendo dados no cache do cluster
* Protegendo dados no armazenamento de hardware NAS de back-end
* Protegendo dados no armazenamento de nuvem de BLOBs do Azure de back-end

Cada vFXT avere para o cliente do Azure deve criar seu próprio plano de recuperação de desastres abrangente que inclui planos para esses itens. Você também pode criar resiliência em aplicativos que você usa com o cluster vFXT. Leia os links nas [próximas etapas](#next-steps) para obter ajuda.

## <a name="protect-against-downtime"></a>Proteger contra tempo de inatividade

A redundância é interna à avere vFXT do produto Azure:

* O cluster está altamente disponível, e nós de cluster individuais podem fazer failover com interrupção mínima.
* Os dados alterados no cache são gravados regularmente em filers de núcleo de back-end (hardware NAS ou BLOB do Azure) para armazenamento de longo prazo.

Cada vFXT avere para o cluster do Azure deve estar localizado em uma única zona de disponibilidade, mas você pode usar clusters redundantes localizados em diferentes zonas ou regiões diferentes para fornecer acesso rapidamente no caso de uma interrupção regional.

Você também pode posicionar contêineres de armazenamento em várias regiões se estiver preocupado em perder o acesso aos dados. No entanto, tenha em mente que as transações entre as regiões têm maior latência e um custo maior do que as transações que permanecem dentro de uma região.

## <a name="protect-data-in-the-cluster-cache"></a>Proteger dados no cache de cluster

Os dados armazenados em cache são sempre gravados nos principais Filers antes de um desligamento normal, mas em um desligamento não controlado, os dados alterados no cache podem ser perdidos.

Se você usar o cluster para otimizar somente as leituras de arquivo, não haverá nenhuma alteração a ser perdida. Se você também usar o cluster para armazenar em cache as alterações de arquivo (gravações), considere se deseja ou não ajustar as [políticas de cache](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) dos Filers principais<!-- link to legacy doc --> para personalizar a frequência com que os dados são gravados em armazenamento de longo prazo.

Em geral, seu plano de recuperação deve se concentrar no backup dos sistemas de armazenamento de back-end, que contêm mais dados e geralmente são mais importantes para restabelecer o fluxo de trabalho após uma falha.

## <a name="protect-data-in-nas-core-filers"></a>Proteger dados em filers do NAS Core

Use métodos aceitos para proteger os dados armazenados em um arquivo de núcleo de hardware de NAS local, incluindo instantâneos e backups completos, conforme recomendado pelo provedor NAS. A recuperação de desastres para esses principais Filers está além do escopo deste artigo.

## <a name="protect-data-in-azure-blob-storage"></a>Proteger dados no armazenamento de BLOBs do Azure

O avere vFXT para Azure usa o LRS (armazenamento com redundância local) para os Filers de núcleo de blob do Azure. Isso significa que os dados em seus contêineres de blob são copiados automaticamente para proteção contra falhas de hardware transitórias dentro de um data center.

Esta seção fornece dicas sobre como proteger ainda mais seus dados no armazenamento de BLOBs contra interrupções raras em toda a região ou exclusões acidentais.

As práticas recomendadas para proteger os dados no armazenamento de BLOBs do Azure incluem:

* Copie seus dados críticos para outra conta de armazenamento em outra região com frequência (quantas vezes for determinado pelo seu plano de recuperação de desastre).
* Controle o acesso a dados em todos os sistemas de destino para evitar a exclusão acidental ou corrupção. Considere o uso de [bloqueios de recursos](../azure-resource-manager/management/lock-resources.md) no armazenamento de dados.
* Habilite o recurso avere vFXT for Azure [Cloud snapshot](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) para seus Filers de núcleo de BLOB.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Copiar dados do avere vFXT Core do arquivo para uma conta de backup

Siga estas etapas para estabelecer um backup de dados em outra conta.

1. Se necessário, gere uma nova chave de criptografia e armazene-a com segurança fora dos sistemas afetados.

   Se os dados forem criptografados pelo avere vFXT para o cluster do Azure, você deverá gerar uma nova chave de criptografia antes de copiar os dados para outra conta de armazenamento. Armazene essa chave e senha com segurança em uma instalação que seja segura e que não será afetada por uma falha regional.

   Você deve fornecer essa chave ao adicionar o contêiner a um cluster, mesmo se você estiver adicionando-o novamente ao cluster original.

   Ler [configurações de criptografia de nuvem](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> para obter informações detalhadas.

   Se o contêiner usar apenas a criptografia interna do Azure, você poderá ignorar esta etapa.

1. Remova o filer principal do sistema. Isso força o cluster a gravar todos os dados alterados no armazenamento de back-end.

   Embora você precise adicionar novamente o filer principal após o backup, removê-lo é a melhor maneira de garantir que todos os dados sejam gravados completamente no back-end. (A opção "suspender" pode, às vezes, deixar os dados alterados no cache.) <!-- xxx true? or just metadata? -->

   Anote o nome do Filer principal e as informações de junção (listadas na página **namespace** no painel de controle) para que você possa replicá-lo ao adicionar novamente o contêiner após o backup.

   Use o painel de controle de cluster para remover o filer principal. [Abra o painel de controle de cluster](avere-vfxt-cluster-gui.md) e escolha **principal Filer**  >  **gerenciar Filers principais**. Localize o sistema de armazenamento do qual você deseja fazer backup e use o botão **remover** para excluí-lo do cluster.

1. Crie um novo contêiner de armazenamento de blob vazio em outra conta de armazenamento em outra região.

1. Use qualquer ferramenta de cópia conveniente para copiar os dados no arquivo principal do Filer para o novo contêiner. A cópia deve replicar os dados sem alterações e sem interromper o formato de sistema de arquivos de nuvem proprietário usado pelo avere vFXT para o Azure. As ferramentas baseadas no Azure incluem [AzCopy](../storage/common/storage-use-azcopy-v10.md), [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)e [Azure data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Depois de copiar os dados para o contêiner de backup, adicione o contêiner original de volta ao cluster, conforme descrito em [Configurar armazenamento](avere-vfxt-add-storage.md).

   * Use o mesmo nome de arquivo principal e informações de junção para que os fluxos de trabalho do cliente não precisem ser alterados.
   * Defina o valor de **conteúdo do Bucket** para a opção dados existentes.
   * Se o contêiner foi criptografado pelo cluster, você deve inserir a chave de criptografia atual para seu conteúdo. (Essa é a chave que você atualizou na etapa 1.)

Para backups após o primeiro, você não precisa criar um novo contêiner de armazenamento. No entanto, considere a possibilidade de gerar uma nova chave de criptografia sempre que você fizer um backup para verificar se você tem a chave atual armazenada em um local que você se lembra.

### <a name="access-a-backup-data-source-during-an-outage"></a>Acessar uma fonte de dados de backup durante uma interrupção

Para acessar o contêiner de backup de um avere vFXT para o cluster do Azure, siga este processo:

1. Se necessário, crie um novo avere vFXT para o cluster do Azure em uma região não afetada.

   > [!TIP]
   > Ao criar um avere vFXT para o cluster do Azure, você pode salvar uma cópia de seu modelo de criação e parâmetros. Se você salvar essas informações ao criar o cluster primário, poderá usá-lo para criar um cluster de substituição com as mesmas propriedades. Na página [Resumo](avere-vfxt-deploy.md#validation-and-purchase) , clique no link **baixar modelo e parâmetros** . Salve as informações em um arquivo antes de criar o cluster.

1. Adicione um novo arquivo de nuvem do Cloud Core que aponta para o contêiner de blob duplicado.

   Certifique-se de especificar que o contêiner de destino já contém dados na configuração de **conteúdo do Bucket** do assistente de criação do Filer principal. (O sistema deve alertá-lo se você deixar acidentalmente essa definição como **vazia**.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Se necessário, atualize os clientes para que eles montem o novo cluster ou o novo Filer principal em vez do original. (Se você adicionar o arquivo de núcleo de substituição com o mesmo nome e caminho de junção que o contêiner original, não será necessário atualizar os processos de cliente, a menos que você precise montar o novo cluster em um novo endereço IP.)

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como personalizar as configurações do avere vFXT para o Azure, leia [ajuste do cluster](avere-vfxt-tuning.md).
* Saiba mais sobre a recuperação de desastres e a criação de aplicativos resilientes no Azure:

  * [Orientações técnicas de resiliência do Azure](/azure/architecture/framework/resiliency/overview)
  * [Recuperar-se de uma interrupção do serviço em toda a região](/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Recuperação de desastre e alta disponibilidade para aplicativos do Azure](/azure/architecture/framework/resiliency/backup-and-recovery)
  <!-- can't find these in the source tree to use relative links -->