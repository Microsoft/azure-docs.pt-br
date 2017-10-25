---
title: "Backup do Azure do SAP HANA com base em instantâneos de armazenamento | Microsoft Docs"
description: "Há duas possibilidades principais de backup para SAP HANA em máquinas virtuais do Azure, e este artigo aborda o backup do Azure do SAP HANA baseado em instantâneos de armazenamento"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: f332b8ac091b75a23489ac27f15ad1fd10d24ec6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Backup do SAP HANA com base em instantâneos de armazenamento

## <a name="introduction"></a>Introdução

Este artigo faz parte de uma série dividida em três partes com artigos relacionados ao backup do SAP HANA. [Guia de backup do SAP HANA em máquinas virtuais do Azure](sap-hana-backup-guide.md) fornece uma visão geral e informações sobre como começar, e [Backup do Azure do SAP HANA no nível de arquivo](sap-hana-backup-file-level.md) aborda a opção de backup baseado em arquivo.

Ao usar um recurso de backup de VM para um sistema de demonstração do all-in-one de instância única, um considere fazer um backup VM em vez de gerenciar backups HANA no nível do sistema operacional. Uma alternativa é tirar instantâneos de blob do Azure para criar cópias de discos virtuais individuais, que são anexados a uma máquina virtual, e manter os arquivos de dados do HANA. Mas um ponto crítico é a consistência do aplicativo ao criar um instantâneo de backup ou disco VM enquanto o sistema está ligado e em execução. Veja _Consistência de dados do SAP HANA ao gerar instantâneos de armazenamento_  no artigo relacionado [Guia de backup do SAP HANA nas Máquinas Virtuais Azure](sap-hana-backup-guide.md). O SAP HANA tem um recurso que oferece suporte a esses tipos de instantâneos de armazenamento.

## <a name="sap-hana-snapshots"></a>Instantâneos do SAP HANA

Há um recurso no SAP HANA que oferece suporte a tirar um instantâneo do armazenamento. No entanto, a partir de dezembro de 2016, há uma restrição para sistemas de único contêiner. As configurações de contêiner multilocatário não dão suporte a esse tipo de instantâneo de banco de dados (veja [Criar um instantâneo de armazenamento (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

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

A partir de dezembro de 2016, o agente de backup do serviço de Backup do Azure não está disponível para VMs do Linux. Para fazer uso de backup do Azure no nível de arquivo/diretório, copie os arquivos de backup do SAP HANA para uma VM do Windows e use o agente de backup. Caso contrário, apenas um backup completo de VM do Linux é possível por meio do serviço de Backup do Azure. Veja [Visão geral dos recursos do Backup do Azure](../../../backup/backup-introduction-to-azure-backup.md) para saber mais.

O serviço de Backup do Azure oferece uma opção para fazer backup e restaurar uma máquina virtual. Para saber mais sobre esse serviço e como ele funciona, veja o artigo [Planejar sua infraestrutura de backup de VM no Azure](../../../backup/backup-azure-vms-introduction.md).

Há duas considerações importantes de acordo com o artigo:

_&quot;Em máquinas virtuais Linux, apenas os backups consistentes de arquivos são possíveis, já que o Linux não tem uma plataforma equivalente ao VSS.&quot;_

_&quot;Os aplicativos precisam implementar seu próprio mecanismo de &quot;correção&quot; nos dados restaurados.&quot;_

Portanto, preciso certificar-se de que o SAP HANA esteja em um estado consistente no disco quando o backup é iniciado. Veja _Instantâneos do SAP HANA_, descrito anteriormente neste documento. Mas há um problema em potencial quando o SAP HANA permanece nesse modo de preparação de instantâneo. Veja [Criar um instantâneo de armazenamento (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) para saber mais.

O artigo declara:

_&quot;É altamente recomendável para confirmar ou abandonar um instantâneo de armazenamento assim que possível após ele ter sido criado. Enquanto o instantâneo de armazenamento está sendo preparado ou criado, os dados relevantes de instantâneo estão congelados. Enquanto os dados relevantes de instantâneo permanecem congelados, alterações ainda podem ser feitas no banco de dados. Essas alterações não fará com que os dados de instantâneo relevantes congelados a ser alterado. Em vez disso, as alterações são gravadas para posições na área de dados que são separadas do instantâneo do armazenamento. As alterações também são gravadas no log. No entanto, mais os dados relevantes de instantâneo são mantidos congelados, mais o volume de dados pode crescer.&quot;_

O Backup do Azure cuida da consistência do sistema de arquivos por meio de extensões de VM do Azure. Essas extensões não são autônomos disponíveis e funcionam somente em conjunto com o serviço de Backup do Azure. No entanto, ainda é um requisito para gerenciar um instantâneo do SAP HANA para garantir a consistência do aplicativo.

O Backup do Azure tem duas fases principais:

- Tirar instantâneo
- Transferir os dados para o cofre

Você pode confirmar o instantâneo do SAP HANA quando a fase de serviço de Backup do Azure de tirar um instantâneo é concluída. Ele pode levar vários minutos para ver no portal do Azure.

![Esta figura mostra parte da lista de trabalho de backup de um serviço de Backup do Azure](media/sap-hana-backup-storage-snapshots/image014.png)

Esta figura mostra parte da lista de trabalho de backup de um serviço de Backup do Azure, que era usado para fazer backup da VM do HANA de teste.

![Para mostrar os detalhes do trabalho, clique no trabalho de backup no portal do Azure](media/sap-hana-backup-storage-snapshots/image015.png)

Para mostrar os detalhes do trabalho, clique no trabalho de backup no portal do Azure. Aqui, é possível ver as duas fases. Pode levar alguns minutos até que ele mostra a fase de instantâneo como concluídos. A maioria do tempo é gasto na fase de transferência de dados.

## <a name="hana-vm-backup-automation-via-azure-backup-service"></a>Automação de backups HANA VM por meio do serviço de Backup do Azure

Você pode confirmar manualmente o instantâneo SAP HANA após a fase de instantâneo de Backup do Azure é concluída, conforme descrito anteriormente, mas vale a pena considerar a automação porque um administrador não pode monitorar a lista de trabalhos de backup no portal do Azure.

Aqui está uma explicação de como isso pode ser obtido por meio de cmdlets do Azure PowerShell.

![Um serviço de Backup do Azure foi criado com o nome hana--Cofre de backup](media/sap-hana-backup-storage-snapshots/image016.png)

Um serviço de Backup do Azure foi criado com o nome &quot;hana Cofre de backup.&quot; O comando PS **AzureRmRecoveryServicesVault de Get-nome do Cofre de backup hana** recupera o objeto correspondente. Em seguida, esse objeto é usado para definir o contexto de backup, como mostrado na figura a seguir.

![É possível verificar para o trabalho de backup em andamento](media/sap-hana-backup-storage-snapshots/image017.png)

Depois de definir o contexto correto, um pode verificar o trabalho de backup está em andamento e procure os detalhes do trabalho. A lista de subtarefa mostra se a fase de instantâneo do trabalho de backup do Azure já está concluída:

```
$ars = Get-AzureRmRecoveryServicesVault -Name hana-backup-vault
Set-AzureRmRecoveryServicesVaultContext -Vault $ars
$jid = Get-AzureRmRecoveryServicesBackupJob -Status InProgress | select -ExpandProperty jobid
Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
```

![O valor em um loop de sondagem até se tornar como concluída](media/sap-hana-backup-storage-snapshots/image018.png)

Depois que os detalhes do trabalho são armazenados em uma variável, é simplesmente a sintaxe PS para ir para a primeira entrada de matriz e recuperar o valor de status. Para concluir o script de automação, sondar o valor em um loop até que ele se transforme &quot;concluído.&quot;

```
$st = Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
$st[0] | select -ExpandProperty status
```

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

- Você ainda deve se certificar de que o SAP HANA está em um estado consistente
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
