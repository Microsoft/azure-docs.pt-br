---
title: Guia de backup para SAP HANA em Máquinas Virtuais do Azure | Microsoft Azure
description: O guia de backup do SAP HANA oferece duas possibilidades principais de backup para SAP HANA em máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0004afb5895d7549e5db8ad5e53b52fa17991520
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667910"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Guia de backup para SAP HANA em Máquinas Virtuais do Azure

## <a name="getting-started"></a>Introdução

O guia de backup do SAP HANA em execução em máquinas virtuais do Azure descrevem apenas tópicos específicos do Azure. Para obter SAP HANA de itens relacionados ao backup geral, consulte a documentação do SAP HANA. Esperamos que você esteja familiarizado com as estratégias de backup de banco de dados, os motivos e as motivações para ter um som e uma estratégia de backup válida e esteja ciente dos requisitos que sua empresa tem para o procedimento de backup, o período de retenção de backups e o procedimento de restauração.

O SAP HANA é compatível oficialmente com vários tipos de VM do Azure, como a série M do Azure. Para obter uma lista completa de VMs do Azure certificadas SAP HANA e unidades de instância grande do HANA, confira [Localizar plataformas de IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). O Microsoft Azure oferece várias unidades em que SAP HANA é executado não virtualizado em servidores físicos. Esse serviço é chamado de [instâncias grandes Hana](hana-overview-architecture.md). Este guia não abordará os processos de backup e as ferramentas para instâncias grandes do HANA. Mas vai ser limitado às máquinas virtuais do Azure. Para obter detalhes sobre processos de backup/restauração com instâncias grandes do HANA, leia o artigo [backup e restauração do HLI](./hana-backup-restore.md).

O foco deste artigo é de três possibilidades de backup para SAP HANA em máquinas virtuais do Azure:

- Backup do HANA por meio [dos serviços de backup do Azure](../../../backup/backup-overview.md) 
- Backup do HANA para o sistema de arquivos em uma Máquina Virtual do Linux Azure (consulte [Backup do Azure do SAP HANA para arquivo](sap-hana-backup-file-level.md))
- Backup do HANA com base em instantâneos de armazenamento usando o recurso de instantâneo do Azure Storage blob manualmente ou o serviço de backup do Azure


O SAP HANA oferece uma API de backup, que permite que as ferramentas de backup de terceiros sejam integradas diretamente no SAP HANA. Produtos como o serviço de backup do Azure ou [CommVault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) estão usando essa interface proprietária para disparar SAP Hana banco de dados ou backups de log redo. 


Informações sobre como você pode encontrar o software SAP com suporte no Azure podem ser encontradas no artigo [que software SAP tem suporte para implantações do Azure](./sap-supported-product-on-azure.md).

## <a name="azure-backup-service"></a>Serviço de Backup do Azure

O primeiro cenário mostrado é um cenário em que o serviço de backup do Azure está usando a `backint` interface SAP Hana para executar um backup de streaming com um banco de dados SAP Hana. Ou você usa um recurso mais genérico do serviço de backup do Azure para criar um instantâneo de disco consistente com o aplicativo e fazer com que ele seja transferido para o serviço de backup do Azure.

O backup do Azure integra-se e é certificado como solução de backup para SAP HANA usando a interface SAP HANA proprietária chamada [BACKINT](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5). Para obter mais detalhes sobre a solução, seus recursos e as regiões do Azure onde estão disponíveis, leia o artigo [matriz de suporte para backup de bancos de dados SAP Hana em VMs do Azure](../../../backup/sap-hana-backup-support-matrix.md#scenario-support). Para obter detalhes e princípios sobre o serviço de backup do Azure para HANA, leia o artigo [sobre SAP Hana backup de banco de dados em VMs do Azure](../../../backup/sap-hana-db-about.md). 

A segunda possibilidade de aproveitar o serviço de backup do Azure é criar um backup consistente com o aplicativo usando instantâneos de disco do armazenamento Premium do Azure. Outros armazenamentos do Azure certificados pelo HANA, como o [Azure ultra Disk](../../disks-enable-ultra-ssd.md) e o [Azure NetApp files](https://azure.microsoft.com/services/netapp/) não dão suporte a esse tipo de instantâneo por meio do serviço de backup do Azure. Lendo estes artigos:

- [Planejar sua infraestrutura de backup de VM no Azure](../../../backup/backup-azure-vms-introduction.md)
- [Backup consistente de aplicativos de VMs Linux do Azure](../../../backup/backup-azure-linux-app-consistent.md) 

Essa sequência de atividade surge:

- O backup do Azure precisa executar um script de pré-instantâneo que coloca o aplicativo, neste caso SAP HANA, em um estado consistente
- Como esse estado consistente é confirmado, o backup do Azure executará os instantâneos de disco
- Depois de concluir os instantâneos, o backup do Azure desfará a atividade que fez no script de pré-instantâneo
- Após a execução bem-sucedida, o backup do Azure transmitirá os dados para o cofre de backup

No caso de SAP HANA, a maioria dos clientes está usando o Acelerador de Gravação do Azure para os volumes que contêm o log de refazer SAP HANA. O serviço de backup do Azure excluirá automaticamente esses volumes dos instantâneos. Essa exclusão não prejudica a capacidade do HANA de restaurar. Embora ele bloqueie a capacidade de restaurar com quase todos os outros DBMS com suporte do SAP.

A desvantagem dessa possibilidade é o fato de que você precisa desenvolver seu próprio script pré e pós-instantâneo. O script de pré-instantâneo precisa criar um instantâneo do HANA e lidar com casos de exceção eventual. Enquanto o script de pós-instantâneo precisa excluir o instantâneo do HANA novamente. Para obter mais detalhes sobre a lógica necessária, comece com a [Observação de suporte do SAP #2039883](https://launchpad.support.sap.com/#/notes/2039883). As considerações da seção "SAP HANA consistência de dados ao fazer instantâneos de armazenamento" neste artigo se aplicam totalmente a esse tipo de backup.

> [!NOTE]
> Backups baseados em instantâneo de disco para SAP HANA em implantações em que vários contêineres de banco de dados são usados, requer uma versão mínima do HANA 2,0 SP04
> 

Veja detalhes sobre instantâneos de armazenamento mais adiante neste documento.

![Esta figura mostra duas possibilidades para salvar o estado atual da VM](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Outros métodos de backup do HANA
Há três outros métodos ou caminhos de backup que podem ser considerados:

- Fazendo backup em um compartilhamento NFS baseado em Azure NetApp Files (seja). O seja novamente tem a capacidade de criar instantâneos desses volumes nos quais você armazena os backups. Dada a taxa de transferência que você eventualmente precisa para gravar os backups, essa solução pode se tornar um método caro. Embora seja fácil de estabelecer, uma vez que o HANA pode gravar os backups diretamente no compartilhamento NFS nativo do Azure
- Executar o backup do HANA em discos anexados à VM de SSD Standard ou armazenamento Premium do Azure. Como a próxima etapa, você pode copiar esses arquivos de backup no armazenamento de BLOBs do Azure. Essa estratégia pode ser atraente em termos de preço
- Executar o backup do HANA em discos anexados à VM de SSD Standard ou armazenamento Premium do Azure. Como a próxima etapa, o disco é instantâneo regularmente. Após o primeiro instantâneo, instantâneos incrementais podem ser usados para reduzir os custos

![Esta figura mostra opções para colocar um backup de arquivo do SAP HANA dentro da VM](media/sap-hana-backup-guide/other-hana-backup-paths.png)

Esta figura mostra opções para fazer um backup de arquivo do SAP HANA dentro da VM e, em seguida, armazenando os arquivos de backup do SAP HANA em outro lugar usando ferramentas diferentes. No entanto, todas as soluções que não envolvem um serviço de backup de terceiros ou o serviço de backup do Azure têm vários obstáculos em comum. Alguns deles podem ser listados, como administração de retenção, processo de restauração automática e fornecimento automático de recuperação pontual como serviço de backup do Azure ou outros pacotes de backup e serviços de terceiros especializados fornecidos. Muitos desses serviços de terceiros que podem ser executados no Azure. 


## <a name="sap-resources-for-hana-backup"></a>Recursos do SAP para backup HANA

### <a name="sap-hana-backup-documentation"></a>Documentação de backup do SAP HANA

- [Introdução à Administração do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planejando sua estratégia de backup e recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Agendar Backup HANA usando ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Agendar Backups de Dados (Ferramenta Cockpit do SAP HANA)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Perguntas frequentes sobre o backup do SAP HANA em [Observação 1642148 do SAP](https://launchpad.support.sap.com/#/notes/1642148)
- Perguntas frequentes sobre instantâneos de banco de dados e armazenamento do SAP HANA em [Observação 2039883 do SAP](https://launchpad.support.sap.com/#/notes/2039883)
- Sistemas de arquivos de rede inadequados para backup e recuperação em [Observação 1820529 do SAP](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Como verificar a exatidão do backup do SAP HANA
Independentemente do seu método de backup, a execução de um teste de restauração em um sistema diferente é uma necessidade absoluta. Essa abordagem fornece uma maneira de garantir que um backup está correto e os processos internos para backup e restauração funcionam conforme o esperado. Embora a restauração de backups possa ser um obstáculo local devido ao seu requisito de infraestrutura, é muito mais fácil realizar na nuvem fornecendo os recursos necessários temporariamente para essa finalidade. Está certo de que há ferramentas fornecidas com o HANA que podem verificar os arquivos de backup na capacidade de restaurar. No entanto, a finalidade dos exercícios de restauração frequentes é testar o processo de uma restauração de banco de dados e treinar esse processo com a equipe de operações.

Tenha em mente que fazer uma restauração simples e verificar se o HANA está ativo e em execução não são o suficiente. Você deve executar uma verificação de consistência de tabela para ter certeza de que o banco de dados restaurado está bem. O SAP HANA oferece vários tipos de verificações de consistência descritos [Observação 1977584 do SAP](https://launchpad.support.sap.com/#/notes/1977584).

Informações sobre a verificação de consistência da tabela também podem ser encontradas no site do SAP em [Verificações de consistência da tabela e do catálogo](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Prós e contras do backup do HANA versus instantâneo do armazenamento

O SAP não dá preferência para fazer backup do HANA ou instantâneo de armazenamento. Ele lista seus prós e contras, portanto, é possível determinar qual deles usar dependendo da situação e tecnologia de armazenamento disponível (consulte [Planejando sua estratégia de backup e recuperação](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/ef085cd5949c40b788bba8fd3c65743e.html)).

No Azure, esteja ciente do fato de que o recurso de instantâneo de blob do Azure não&#39;a fornecer consistência do sistema de arquivos em vários discos (consulte [usando instantâneos de blob com o PowerShell](/archive/blogs/cie/using-blob-snapshots-with-powershell)). 

Além disso, é preciso entender as implicações de cobranças ao trabalhar com instantâneos de blob com frequência conforme descrito neste artigo: [Noções básicas sobre como instantâneos acumulam cobranças](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— não é tão óbvio quanto usar discos virtuais do Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Consistência de dados do SAP HANA ao realizar instantâneos de armazenamento

Conforme documentado anteriormente, descrevendo os recursos de backup de instantâneo do backup do Azure, a consistência do sistema de arquivos e do aplicativo é obrigatória ao tirar instantâneos de armazenamento. A maneira mais fácil de evitar problemas seria desligando o SAP HANA ou, talvez, até mesmo a máquina virtual inteira. Algo que não é viável para uma instância de produção.

> [!NOTE]
> Backups baseados em instantâneo de disco para SAP HANA em implantações em que vários contêineres de banco de dados são usados, requer uma versão mínima do HANA 2,0 SP04
> 

O armazenamento do Azure, não fornece consistência do sistema de arquivos em vários discos ou volumes anexados a uma VM durante o processo de instantâneo. Isso significa que a consistência do aplicativo durante o instantâneo precisa ser entregue pelo aplicativo, neste caso SAP HANA ela mesma. A [Observação do SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883) tem informações importantes sobre backups de SAP Hana por instantâneos de armazenamento. Por exemplo, com os sistemas de arquivos XFS, é necessário executar o **xfs \_ Freeze** antes de iniciar um instantâneo de armazenamento para fornecer consistência de aplicativo (consulte [xfs \_ congelar (8)-página do manual do Linux](https://linux.die.net/man/8/xfs_freeze) para obter detalhes sobre o **xfs \_ Freeze**).

Supondo que haja um sistema de arquivos XFS abrangendo quatro discos virtuais do Azure, as seguintes etapas fornecem um instantâneo consistente que representa a área de dados do HANA:

1. Criar preparação de instantâneo de dados do HANA
1. Congelar os sistemas de arquivos de todos os discos/volumes (por exemplo, usar **xfs \_ Freeze**)
1. Crie todos os instantâneos de blob necessários no Azure
1. Descongele o sistema de arquivos
1. Confirmar o instantâneo de dados do HANA (excluirá o instantâneo)

Ao usar a capacidade do backup do Azure para executar backups de instantâneo consistente com o aplicativo, as etapas #1 precisam ser codificadas/inseridas em script por você no para o script de pré-instantâneo. O serviço de backup do Azure executará as etapas #2 e #3. As etapas #4 e #5 precisam ser fornecidas novamente pelo seu código no script de pós-instantâneo. Se você não estiver usando o serviço de backup do Azure, também precisará codificar/gerar script da etapa #2 e #3 por conta própria.
Mais informações sobre a criação de instantâneos de dados do HANA podem ser encontradas nestes artigos:

- [Instantâneos de dados do HANA] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Mais detalhes para executar a etapa #1 podem ser encontrados no artigo [criar um instantâneo de dados (Native SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 
- Detalhes para confirmar/excluir instantâneos de dados do HANA conforme necessário na etapa #5 podem ser encontrados no artigo [criar um instantâneo de dados (SQL nativo)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

É importante confirmar o instantâneo do HANA. Devido à &quot;Cópia em gravação,&quot; o SAP HANA pode não precisar de espaço em disco adicional enquanto estiver no modo de preparação do instantâneo. Também não é possível iniciar novos backups até que o instantâneo do SAP HANA seja confirmado.


### <a name="sap-hana-backup-scheduling-strategy"></a>Estratégia de agendamento de backup do SAP HANA

O artigo SAP HANA [planejamento de sua estratégia de backup e recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) informa um plano básico para fazer backups. Conte com a documentação do SAP sobre o HANA e suas experiências com outros DBMS para definir a estratégia de backup/restauração e o processo para SAP HANA. A sequência de diferentes tipos de backups e o período de retenção são altamente dependentes dos SLAs que você precisa fornecer.


### <a name="sap-hana-backup-encryption"></a>Criptografia de backup do SAP HANA

O SAP HANA oferece criptografia de dados e de log. Se SAP HANA dados e o log não forem criptografados, os backups não serão criptografados por padrão. No entanto, o SAP HANA oferece uma criptografia de backup separada, conforme documentado em [SAP Hana criptografia de backup](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html). Se você estiver executando versões mais antigas do SAP HANA, talvez seja necessário verificar se a criptografia de backup ainda faz parte da funcionalidade fornecida.  


## <a name="next-steps"></a>Próximas etapas
* O [Backup do SAP HANA no Azure em nível de arquivo](sap-hana-backup-file-level.md) descreve a opção de backup baseada em arquivo.
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastres do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
