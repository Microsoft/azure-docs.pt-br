---
title: Backup do Azure do SAP HANA com base em instantâneos de armazenamento | Microsoft Docs
description: Há duas possibilidades principais de backup para SAP HANA em máquinas virtuais do Azure, e este artigo aborda o backup do Azure do SAP HANA baseado em instantâneos de armazenamento
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 875060a59cf70d295534c3a4f56136010a560e74
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709924"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Backup do SAP HANA com base em instantâneos de armazenamento

## <a name="introduction"></a>Introdução

Este artigo faz parte de uma série dividida em três partes com artigos relacionados ao backup do SAP HANA. [Guia de backup do SAP HANA em máquinas virtuais do Azure](sap-hana-backup-guide.md) fornece uma visão geral e informações sobre como começar, e [Backup do Azure do SAP HANA no nível de arquivo](sap-hana-backup-file-level.md) aborda a opção de backup baseado em arquivo.

Ao usar um recurso de backup de VM para um sistema de demonstração do all-in-one de instância única, um considere fazer um backup VM em vez de gerenciar backups HANA no nível do sistema operacional. Uma alternativa é tirar instantâneos de blob do Azure para criar cópias de discos virtuais individuais, que são anexados a uma máquina virtual, e manter os arquivos de dados do HANA. Mas um ponto crítico é a consistência do aplicativo ao criar um instantâneo de backup ou disco VM enquanto o sistema está ligado e em execução. Veja _Consistência de dados do SAP HANA ao gerar instantâneos de armazenamento_  no artigo relacionado [Guia de backup do SAP HANA nas Máquinas Virtuais Azure](sap-hana-backup-guide.md). O SAP HANA tem um recurso que oferece suporte a esses tipos de instantâneos de armazenamento.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>Instantâneos do SAP HANA como a parte central dos backups consistentes com aplicativos

Há uma funcionalidade no SAP HANA que dá suporte para obter instantâneos de armazenamento. Há uma restrição para sistemas com um único contêiner. Os cenários de MCS do SAP HANA único com mais de um locatário não dão suporte a esse tipo de instantâneo de banco de dados do SAP HANA [confira [Create a Storage Snapshot (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)] [Criar um instantâneo de armazenamento (SAP HANA Studio)].

Ele funciona da seguinte maneira:

- Preparar para um instantâneo do armazenamento iniciando o instantâneo do SAP HANA
- Executar o instantâneo do armazenamento (do instantâneo de blob do Azure, por exemplo)
- Confirmar o instantâneo SAP HANA

![Esta captura de tela mostra que um instantâneo de dados do SAP HANA pode ser criado por meio de uma instrução SQL](media/sap-hana-backup-storage-snapshots/image011.png)

Esta captura de tela mostra que um instantâneo de dados do SAP HANA pode ser criado por meio de uma instrução SQL.

![O instantâneo também será exibido no catálogo de backup no SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

O instantâneo também será exibido no catálogo de backup no SAP HANA Studio.

![No disco, o instantâneo aparece no diretório de dados SAP HANA](media/sap-hana-backup-storage-snapshots/image013.png)

No disco, o instantâneo aparece no diretório de dados SAP HANA.

É preciso garantir que a consistência do sistema de arquivo também é garantida antes de executar o instantâneo de armazenamento enquanto o SAP HANA está no modo de preparação de instantâneo. Veja _Consistência de dados do SAP HANA ao gerar instantâneos de armazenamento_  no artigo relacionado [Guia de backup do SAP HANA nas Máquinas Virtuais Azure](sap-hana-backup-guide.md).

Depois da conclusão do instantâneo de armazenamento, é essencial confirmar o instantâneo do SAP HANA. Há uma instrução SQL correspondente para executar: BACKUP DATA CLOSE SNAPSHOT (veja [Instrução BACKUP DATA CLOSE SNAPSHOT (Backup e Recuperação)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Confirme o instantâneo HANA. Devido a &quot;Copy-on-Write,&quot; SAP HANA pode exigir espaço em disco adicional no instantâneo-modo de preparação e não é possível iniciar novos backups até que o instantâneo SAP HANA é confirmado.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Backup da VM do HANA por meio do serviço de Backup do Azure

O agente de backup do serviço de Backup do Azure não está disponível para VMs do Linux. Além disso, o Linux não tem uma funcionalidade semelhante à do Windows em relação ao VSS.  Para fazer uso de backup do Azure no nível de arquivo/diretório, copie os arquivos de backup do SAP HANA para uma VM do Windows e use o agente de backup. 

Caso contrário, apenas um backup completo de VM do Linux é possível por meio do serviço de Backup do Azure. Veja [Visão geral dos recursos do Backup do Azure](../../../backup/backup-introduction-to-azure-backup.md) para saber mais.

O serviço de Backup do Azure oferece uma opção para fazer backup e restaurar uma máquina virtual. Para saber mais sobre esse serviço e como ele funciona, veja o artigo [Planejar sua infraestrutura de backup de VM no Azure](../../../backup/backup-azure-vms-introduction.md).

Há duas considerações importantes de acordo com o artigo:

_&quot;Em máquinas virtuais Linux, apenas os backups consistentes de arquivos são possíveis, já que o Linux não tem uma plataforma equivalente ao VSS.&quot;_

_&quot;Os aplicativos precisam implementar seu próprio mecanismo de &quot;correção&quot; nos dados restaurados.&quot;_

Portanto, preciso certificar-se de que o SAP HANA esteja em um estado consistente no disco quando o backup é iniciado. Veja _Instantâneos do SAP HANA_, descrito anteriormente neste documento. Mas há um problema em potencial quando o SAP HANA permanece nesse modo de preparação de instantâneo. Veja [Criar um instantâneo de armazenamento (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) para saber mais.

O artigo declara:

_&quot;É altamente recomendável para confirmar ou abandonar um instantâneo de armazenamento assim que possível após ele ter sido criado. Enquanto o instantâneo de armazenamento está sendo preparado ou criado, os dados relevantes de instantâneo estão congelados. Enquanto os dados relevantes de instantâneo permanecem congelados, alterações ainda podem ser feitas no banco de dados. Essas alterações não fará com que os dados de instantâneo relevantes congelados a ser alterado. Em vez disso, as alterações são gravadas para posições na área de dados que são separadas do instantâneo do armazenamento. As alterações também são gravadas no log. No entanto, mais os dados relevantes de instantâneo são mantidos congelados, mais o volume de dados pode crescer.&quot;_

O Backup do Azure cuida da consistência do sistema de arquivos por meio de extensões de VM do Azure. Essas extensões não são autônomos disponíveis e funcionam somente em conjunto com o serviço de Backup do Azure. No entanto, ainda é um requisito fornecer scripts para criar e excluir um instantâneo do SAP HANA para garantir a consistência do aplicativo.

O Backup do Azure tem quatro fases principais:

- Executar script de preparação – o script precisa criar um instantâneo do SAP HANA
- Tirar instantâneo
- Executar script pós-instantâneo – o script precisa excluir o SAP HANA criado pelo script de preparação
- Transferir os dados para o cofre

Para obter detalhes de onde copiar esses scripts e detalhes de como o Backup do Azure funciona exatamente, confira os seguintes artigos:

- [Planejar sua infraestrutura de backup de VM no Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Backup consistente de aplicativos de VMs Linux do Azure](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



Neste momento, a Microsoft ainda não publicou scripts de preparação e scripts pós-instantâneo para o SAP HANA. Você, como cliente ou integrador de sistema precisaria criar esses scripts e configurar o procedimento com base na documentação mencionada acima.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Restaurar do backup consistente com o aplicativo em relação a uma VM
O processo de restauração de um backup consistente com o aplicativo obtido pelo Backup do Azure está documentado no artigo [Recuperar arquivos de um backup de máquina virtual do Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> No artigo [Recuperar arquivos de um backup de máquina virtual do Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) há uma lista de exceções e as etapas listadas ao usar conjuntos de distribuição de disco. Os discos distribuídos são provavelmente a configuração regular de VMs para o SAP HANA. Portanto, é essencial ler o artigo e testar o processo de restauração para esses casos listados no artigo. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Restaurar a chave de licença do HANA e VM por meio do serviço de Backup do Azure

O serviço de Backup do Azure foi projetado para criar uma nova máquina virtual durante a restauração. Não há nenhum plano agora para fazer um &quot;in-loco&quot; restauração de uma VM Azure existente.

![Esta figura mostra a opção de restauração do serviço do Azure no portal do Azure](media/sap-hana-backup-storage-snapshots/image019.png)

Esta figura mostra a opção de restauração do serviço do Azure no portal do Azure. Você pode escolher entre criar uma máquina virtual durante a restauração ou restaurar discos. Depois de restaurar os discos, é ainda necessário criar uma nova VM sobre ele. Sempre que uma nova máquina virtual é criada no Azure, a ID de VM exclusiva é alterada (veja [Acesso e uso da ID exclusiva de VM do Azure](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Esta figura mostra a ID exclusiva da VM do Azure antes e após a restauração por meio do serviço de Backup do Azure](media/sap-hana-backup-storage-snapshots/image020.png)

Esta figura mostra a ID exclusiva da VM do Azure antes e após a restauração por meio do serviço de Backup do Azure. A chave de hardware do SAP, que é usada para SAP licenciamento, está usando essa identificação exclusiva de VM. Como consequência, uma nova licença SAP precisa ser instalado após a restauração de uma máquina virtual.

Um novo recurso de Backup do Azure foi apresentado no modo de visualização durante a criação deste guia backup. Ele permite que uma restauração no nível de arquivo com base no instantâneo VM que foi feito para a máquina virtual backup. Isso evita a necessidade de implantar uma nova VM e, portanto, a ID exclusiva da VM permanece a mesma, e nenhuma nova chave de licença do SAP HANA é necessária. Mais documentação sobre este recurso será fornecida depois que ele foi totalmente testado.

O Backup do Azure, eventualmente, permitirá backup de discos virtuais do Azure individuais, além de arquivos e diretórios de dentro da VM. Uma grande vantagem do Backup do Azure é o gerenciamento de todos os backups, evitando o cliente precise fazê-lo. Se for necessária em uma restauração, o Backup do Azure selecionará o backup correto a ser usado.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>Backup de VM do SAP HANA por meio de instantâneo de disco manual

Em vez de usar o serviço de Backup do Azure, um pode configurar uma solução de backup individual criando instantâneos de blob do Azure VHDs manualmente por meio do PowerShell. Veja [Uso de instantâneos de blob com o PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) para obter uma descrição das etapas.

Ele oferece mais flexibilidade, mas não resolve os problemas explicados neste documento:

- Você ainda precisa verificar se o SAP HANA está em um estado consistente criando um instantâneo do SAP HANA
- O disco do sistema operacional não pode ser substituído, mesmo que a VM seja desalocada devido a um erro informando que há uma concessão. Ele funciona somente após a exclusão de VM, que poderia levar a uma nova ID exclusiva da VM e a necessidade de instalar uma nova licença SAP.

![É possível restaurar apenas os discos de dados de uma VM do Azure](media/sap-hana-backup-storage-snapshots/image021.png)

É possível restaurar apenas os discos de dados de uma VM do Azure, evitando o problema de como obter uma nova ID exclusiva da VM e, invalidada por isso, a licença do SAP:

- Para o teste, dois discos de dados do Azure foram anexados a uma máquina virtual e RAID de software foi definida sobre eles 
- Ele foi confirmado que o SAP HANA estava em um estado consistente pelo recurso de instantâneo do SAP HANA
- Congelamento de sistema de arquivos (veja _Consistência de dados do SAP HANA ao tirar instantâneos de armazenamento_ no artigo relacionado [Guia de backup do SAP HANA nas Máquinas Virtuais do Azure](sap-hana-backup-guide.md))
- Instantâneos de blob foram tirados de ambos os discos de dados
- Descongelar do sistema de arquivos
- Confirmação de instantâneo do SAP HANA
- Para restaurar os discos de dados, a VM foi desligada e desconectado de discos
- Depois de desanexar os discos, foram substituídos com os instantâneos de blob anterior
- Os discos virtuais restaurados foram anexados novamente à VM
- Depois de iniciar a VM, tudo sobre o software RAID funcionou bem e foi definido novamente como a hora do instantâneo de blob
- HANA foi definido de volta ao instantâneo HANA

Se fosse possível desligar o SAP HANA antes dos instantâneos de blob, o procedimento seria menos complexo. Nesse caso, um pode ignorar o instantâneo HANA e, se nada mais está acontecendo no sistema, também ignorar o congelamento de sistema de arquivos. Complexidade adicional entra em cena, quando for necessário fazer instantâneos enquanto tudo está online. Veja _Consistência de dados do SAP HANA ao gerar instantâneos de armazenamento_  no artigo relacionado [Guia de backup do SAP HANA nas Máquinas Virtuais Azure](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Próximas etapas
* [Guia de backup do SAP HANA nas Máquinas Virtuais do Azure](sap-hana-backup-guide.md) fornece uma visão geral e informações sobre como começar.
* [Backup do SAP HANA com base no nível de arquivo](sap-hana-backup-file-level.md) aborda a opção de backup do arquivo.
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastres do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
