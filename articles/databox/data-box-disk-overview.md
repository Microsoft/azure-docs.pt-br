---
title: Visão geral do Microsoft Azure Data Box Disk | Microsoft Docs em dados
description: Descreve o Azure Data Box Disk, uma solução de nuvem que permite a transferência de grandes quantidades de dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: ca46ce3355edf2b77400011d023cd6af330a3b58
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770425"
---
# <a name="what-is-azure-data-box-disk"></a>O que é o Azure Data Box Disk?

A solução do Microsoft Azure Data Box Disk permite que você envie terabytes de dados locais para o Azure de forma rápida, barata e confiável. A transferência de dados segura é acelerada por meio do envio de uma a cinco SSDs (unidades de estado sólido). Esses discos criptografados de 8 TB são enviados ao seu datacenter por meio de uma operadora regional.

Você pode configurar, conectar e desbloquear rapidamente os discos por meio do serviço Data Box no portal do Azure. Copie seus dados em discos e envie os discos de volta para o Azure. No datacenter do Azure, seus dados são carregados automaticamente das unidades para a nuvem usando um link de carregamento rápido de rede privada.

## <a name="use-cases"></a>Casos de uso

Use i Data Box Disk para transferir TBs de dados em cenários sem conectividade de rede limitada. A movimentação de dados pode ser única, periódica ou uma transferência de dados em massa inicial seguida por transferências periódicas.

- **Migração única** - quando uma grande quantidade de dados local é transferida para o Azure. Por exemplo, movimentar dados de fitas offline para dados de arquivamento no armazenamento esporádico do Azure.
- **Transferência incremental** - quando uma transferência em massa inicial é feita usando o Data Box Disk (semente) seguida por transferências incrementais pela rede. Por exemplo, Commvault e Data Box Disk são usados para mover cópias de backup para o Azure. Essa migração é seguida da cópia de dados incrementais pela rede para o Armazenamento do Azure.
- **Carregamentos periódicos** - quando grandes quantidades de dados são geradas periodicamente e precisam ser movidas para o Azure. Por exemplo, na exploração de energia, em que o conteúdo de vídeo é gerado em plataformas de petróleo e em produções de energia eólica.

### <a name="ingestion-of-data-from-data-box"></a>Ingestão de dados do Data Box

Os provedores do Azure e os provedores não Azure podem ingerir dados do Azure Data Box. Os serviços do Azure que fornecem ingestão de dados do Azure Data Box incluem:

- **SharePoint Online**: use o Azure Data Box e a SPMT (Ferramenta de Migração do SharePoint) para migrar o conteúdo do compartilhamento de arquivo para o SharePoint Online. Usando o Data Box, remova a dependência do link da WAN para transferir os dados. Para obter mais informações, confira [Usar o Azure Data Box Heavy para migrar o conteúdo do compartilhamento de arquivo para o SharePoint Online](data-box-heavy-migrate-spo.md).

- **Sincronização de Arquivos do Azure**: replica arquivos do Data Box para um compartilhamento de arquivo do Azure, permitindo centralizar seus serviços de arquivo no Azure, mantendo o acesso local aos seus dados. Para obter mais informações, confira [Implantar a Sincronização de Arquivos do Azure](../storage/file-sync/file-sync-deployment-guide.md).

- **Repositórios HDFS**: migre dados de um repositório HDFS (de Sistema de Arquivos Distribuído Hadoop) local do seu cluster Hadoop para o Armazenamento do Azure usando o Data Box. Para obter mais informações, confira [Migrar do repositório HDFS local para o Armazenamento do Azure com o Azure Data Box](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

- **Backup do Azure**: permite mover grandes backups de dados empresariais críticos por meio de mecanismos offline para um cofre dos Serviços de Recuperação do Azure. Para obter mais informações, confira [Visão geral do Backup do Azure](../backup/backup-overview.md).

Use seus dados do Data Box com muitos provedores de serviços não Azure. Por exemplo:

- **[Commvault](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** : permite migrar grandes volumes de dados para o Microsoft Azure usando o Azure Data Box.
- **[Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** : permite fazer backup e replicar grandes quantidades de dados do computador do Hyper-V para o Data Box.

Para obter uma lista de outros provedores de serviços não Azure que se integram ao Data Box, confira [Parceiros do Azure Data Box](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf).

## <a name="the-workflow"></a>O fluxo de trabalho

Um fluxo de trabalho típico inclui as seguintes etapas:

1. **Pedido**: crie um pedido no portal do Azure, forneça informações de envio e a conta de destino do Armazenamento do Azure para seus dados. Se houver discos disponíveis, o Azure os criptografará, preparará e enviará com uma ID de acompanhamento de remessa.

2. **Receber** - Após a entrega dos discos, descompacte e conecte os discos ao computador que tem os dados a serem copiados. Desbloqueie os discos.

3. **Copiar dados** - Arraste e solte para copiar os dados nos discos.

4. **Retornar** - Preparar e enviar os discos de volta para o datacenter do Azure.

5. **Carregar** - Os dados são copiados automaticamente dos discos para o Azure. Os discos são apagados com segurança, de acordo com as diretrizes do NIST (Instituto Nacional de Padrões e Tecnologia).

Durante esse processo, você será notificado por email sobre todas as alterações de status. Para saber mais sobre o fluxo detalhado, acesse [Implantar Data Box Disk no Portal do Azure](data-box-disk-quickstart-portal.md).

## <a name="benefits"></a>Benefícios

O Data Box Disk foi projetado para mover grandes quantidades de dados para o Azure sem afetar a rede. A solução oferece as seguintes vantagens:

- **Velocidade** - O Data Box Disk usa uma conexão USB 3.0 para mover até 35 TB de dados para o Azure em menos de uma semana.

- **Fácil de usar** - O Data Box é uma solução fácil de usar.

  - Os discos usam a conectividade USB sem exigir quase nenhum tempo de configuração.
  - Os discos têm um fator forma compacto que os torna fáceis de manusear.
  - Os discos não têm requisitos de alimentação externa.
  - Os discos podem ser usados com um laptop, desktop ou um servidor de datacenter.
  - A solução fornece o acompanhamento completo por meio do portal do Azure.

- **Seguro** - O Data Box Disk tem proteções de segurança internas para os discos, dados e serviço.
  - Os discos são resistente a adulterações e oferecem suporte à atualização segura.
  - Os dados nos discos são sempre protegidos por uma criptografia AES de 128 bits.
  - Os discos só podem ser desbloqueados com uma chave fornecida no Portal do Azure.
  - O serviço é protegido pelos recursos de segurança do Azure.
  - Após o carregamento dos seus dados no Azure, os discos são limpos, conforme os padrões NIST 800-88r1.  

Para saber mais, acesse [Segurança e proteção de dados do Azure Data Box Disk](data-box-disk-security.md).

## <a name="features-and-specifications"></a>Recursos e especificações

| Especificações                                          | Descrição              |
|---------------------------------------------------------|--------------------------|
| Peso                                                  | < 0,90 kg. por caixa. Até 5 discos na caixa                |
| Dimensões                                              | Disco - SSD de 2,5" |
| Cabos                                                  | 1 cabo USB 3.1 por disco|
| Capacidade de armazenamento por pedido                              | 40 TB (~ 35 TB utilizáveis)|
| Capacidade de armazenamento de disco                                   | 8 TB (~ 7 TB utilizáveis)|
| Interface de dados                                          | USB   |
| Segurança                                                | Previamente criptografado usando o BitLocker e atualização segura <br> Discos protegidos por chave de acesso <br> Dados sempre criptografados  |
| Taxa de transferência de dados                                      | até 430 MBps, dependendo do tamanho do arquivo      |
|Gerenciamento                                               | Portal do Azure |

## <a name="region-availability"></a>Disponibilidade de região

Para saber mais sobre a disponibilidade de região, acesse [Produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). O Data Box Disk também pode ser implantado na Nuvem do Azure Governamental. Para obter mais informações, confira [O que é o Azure Governamental?](../azure-government/documentation-government-welcome.md).

## <a name="pricing"></a>Preços

Para saber mais sobre preços, acesse a [página Preços](https://azure.microsoft.com/pricing/details/databox/disk/).

## <a name="next-steps"></a>Próximas etapas

- Examine os [requisitos do Data Box Disk](data-box-disk-system-requirements.md).
- Entenda os [limites do Data Box Disk](data-box-disk-limits.md).
- Implante rapidamente o [Azure Data Box Disk](data-box-disk-quickstart-portal.md) no portal do Azure.