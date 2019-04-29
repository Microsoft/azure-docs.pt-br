---
title: Guia de backup para SAP HANA em Máquinas Virtuais do Azure | Microsoft Azure
description: O guia de backup do SAP HANA oferece duas possibilidades principais de backup para SAP HANA em máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 89896fab7b1c359007ed23d4f9d9771e366ca68a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60936919"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Guia de backup para SAP HANA em Máquinas Virtuais do Azure

## <a name="getting-started"></a>Introdução

O guia de backup do SAP HANA em execução em máquinas virtuais do Azure descrevem apenas tópicos específicos do Azure. Para itens gerais relacionados ao SAP HANA, consulte a documentação do SAP HANA (consulte _Documentação de backup do SAP HANA_ posteriormente neste artigo).

O foco deste artigo é sobre as duas principais possibilidades de backup para SAP HANA em máquinas virtuais do Azure:

- Backup do HANA para o sistema de arquivos em uma Máquina Virtual do Linux Azure (consulte [Backup do Azure do SAP HANA para arquivo](sap-hana-backup-file-level.md))
- Backup do HANA baseado em instantâneos de armazenamento usando o recurso de instantâneo do Azure Storage Blob manualmente ou o Serviço de Backup do Azure (consulte [Backup do SAP HANA baseado em instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md)) 

O SAP HANA oferece uma API de backup, que permite que as ferramentas de backup de terceiros sejam integradas diretamente no SAP HANA. (Isto não está dentro do escopo deste guia.) Não há nenhuma integração direta do SAP HANA com o Serviço de Backup do Azure disponível nessa API no momento.

O SAP HANA é compatível oficialmente com vários tipos de VM do Azure, como a série M do Azure. Para obter uma lista completa de VMs do Azure certificadas pelo SAP HANA, confira [Find Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Localizar plataformas de IaaS certificadas). Este artigo será atualizado conforme novas ofertas do SAP HANA forem disponibilizados para o Azure.

Há também uma solução híbrida do SAP HANA disponível no Azure, onde o SAP HANA é executado de forma não virtualizada em servidores físicos. No entanto, este guia de backup do Azure do SAP HANA abrange um ambiente puro do Azure onde o SAP HANA é executado em uma VM do Azure, sem ter o SAP HANA em execução em &quot;instâncias grandes.&quot; Consulte [Visão geral e arquitetura do SAP HANA (instâncias grandes) no Azure](hana-overview-architecture.md) para obter mais informações sobre essa solução de backup em &quot;instâncias grandes&quot; com base em instantâneos de armazenamento.

Informações gerais sobre produtos SAP com suporte no Azure podem ser encontradas em [SAP Observação 1928533](https://launchpad.support.sap.com/#/notes/1928533).

As três figuras a seguir fornecem uma visão geral das opções de backup do SAP HANA usando recursos nativos atuais do Azure e também mostram três possíveis cenários futuros de backup. Os artigos relacionados [Backup do Azure no SAP HANA para arquivos](sap-hana-backup-file-level.md) e [Backup SAP HANA com base em instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md) descrevem essas opções mais detalhadamente, incluindo considerações de tamanho e desempenho para backups SAP HANA de vários terabytes de tamanho.

![Esta figura mostra duas possibilidades para salvar o estado atual da VM](media/sap-hana-backup-guide/image001.png)

Esta figura mostra a possibilidade de salvar o estado atual da VM, por meio do Serviço de Backup do Azure ou instantâneo manual de discos de VM. Com essa abordagem, não é necessário gerenciar backups do SAP HANA. O desafio do cenário de instantâneo do disco é a consistência do sistema de arquivos e um estado de disco consistente com o aplicativo. O tópico de consistência é discutido na seção _Consistência de dados do SAP HANA para instantâneos de armazenamento_ posteriormente neste artigo. Recursos e restrições de Serviço de Backup do Azure relacionados a backups do SAP HANA também são discutidos neste artigo.

![Esta figura mostra opções para colocar um backup de arquivo do SAP HANA dentro da VM](media/sap-hana-backup-guide/image002.png)

Esta figura mostra opções para fazer um backup de arquivo do SAP HANA dentro da VM e, em seguida, armazenando os arquivos de backup do SAP HANA em outro lugar usando ferramentas diferentes. Fazer um backup do HANA requer mais tempo do que uma solução de backup do instantâneo, mas ele tem vantagens em relação à integridade e consistência. Mais detalhes são fornecidos neste artigo.

![Esta figura mostra um potencial futuro cenário de backup do SAP HANA](media/sap-hana-backup-guide/image003.png)

Esta figura mostra um potencial futuro cenário de backup do SAP HANA. Se o SAP HANA permitisse fazer backups a partir de uma replicação secundária, ele poderia adicionar mais opções para estratégias de backup. Atualmente isso não é possível de acordo com uma postagem no Wiki do SAP HANA:

_&quot;É possível fazer backups no lado secundário?_

_Não, atualmente podem ser usados backups de dados e log no lado primário. Se o backup de log automática estiver habilitado, após a tomada de controle pelo lado secundário, os backups de log serão automaticamente gravados no lado secundário.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Recursos do SAP para backup HANA

### <a name="sap-hana-backup-documentation"></a>Documentação de backup do SAP HANA

- [Introdução à Administração do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planejando sua estratégia de backup e recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Agendar Backup HANA usando ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Agendar Backups de Dados (Ferramenta Cockpit do SAP HANA)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Perguntas frequentes sobre o backup do SAP HANA em [Observação 1642148 do SAP](https://launchpad.support.sap.com/#/notes/1642148)
- Perguntas frequentes sobre instantâneos de banco de dados e armazenamento do SAP HANA em [Observação 2039883 do SAP](https://launchpad.support.sap.com/#/notes/2039883)
- Sistemas de arquivos de rede inadequados para backup e recuperação em [Observação 1820529 do SAP](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Por que o backup do SAP HANA?

O Armazenamento do Azure oferece disponibilidade e confiabilidade imediatas (consulte [Introdução ao Armazenamento do Microsoft Azure](../../../storage/common/storage-introduction.md) para obter mais informações sobre o armazenamento do Azure).

O mínimo para o &quot;backup&quot; é contar com os SLAs do Azure, mantendo os arquivos de dados e de log do SAP HANA em VHDs do Azure conectados à VM do servidor SAP HANA. Este método aborda falhas da VM, mas não possíveis danos aos arquivos de log e dados do SAP HANA ou erros lógicos, como a exclusão de dados ou arquivos por engano. Os backups também são necessários por motivos legais ou de conformidade. Em resumo, sempre há a necessidade de backups do SAP HANA.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Como verificar a exatidão do backup do SAP HANA
Ao usar instantâneos de armazenamento, recomenda-se executar uma restauração de teste em um sistema diferente. Essa abordagem fornece uma maneira de garantir que um backup está correto e os processos internos para backup e restauração funcionam conforme o esperado. Embora esse seja um grande obstáculo local, a realização na nuvem é muito mais fácil, fornecendo recursos necessários temporariamente para essa finalidade.

Tenha em mente que fazer uma restauração simples e verificar se o HANA está ativo e em execução não são o suficiente. O ideal é executar uma verificação de consistência de tabela para certificar-se de que está tudo bem com o banco de dados restaurado. O SAP HANA oferece vários tipos de verificações de consistência descritos [Observação 1977584 do SAP](https://launchpad.support.sap.com/#/notes/1977584).

Informações sobre a verificação de consistência da tabela também podem ser encontradas no site do SAP em [Verificações de consistência da tabela e do catálogo](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

Para backups de arquivos padrões, um teste de restauração não é necessário. Há duas ferramentas do SAP HANA que ajudam a verificar qual backup pode ser usados para restauração: hdbbackupdiag e hdbbackupcheck. Consulte [Verificar manualmente se uma recuperação é possível](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) para obter mais informações sobre essas ferramentas.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Prós e contras do backup do HANA versus instantâneo do armazenamento

O SAP não dá preferência para fazer backup do HANA ou instantâneo de armazenamento. Ele lista seus prós e contras, portanto, é possível determinar qual deles usar dependendo da situação e tecnologia de armazenamento disponível (consulte [Planejando sua estratégia de backup e recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

No Azure, lembre-se do fato de que o recurso de instantâneo do blob do Azure não garante a consistência do sistema de arquivos (consulte [Usando instantâneos de blob com o PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). A próxima seção, _Consistência de dados do SAP HANA ao fazer instantâneos de armazenamento_, aborda algumas considerações sobre esse recurso.

Além disso, é preciso entender as implicações de cobranças ao trabalhar com frequência com instantâneos de blob, conforme descrito neste artigo: [Noções básicas sobre como instantâneos acumulam cobranças](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— ele não é&#39;tão óbvio quanto usar discos virtuais do Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Consistência de dados do SAP HANA ao realizar instantâneos de armazenamento

A consistência de aplicativos e do sistema de arquivos é um problema complexo quando ao realizar instantâneos de armazenamento. A maneira mais fácil de evitar problemas seria desligando o SAP HANA ou, talvez, até mesmo a máquina virtual inteira. Um desligamento pode ser viável com uma demonstração ou protótipo ou até mesmo como um sistema de desenvolvimento, mas não é uma opção para um sistema de produção.

No Azure, é preciso ter em mente que o recurso de instantâneo de blob do Azure não garante a consistência do sistema de arquivos. No entanto, ele funciona bem usando o recurso de instantâneo do SAP HANA, desde que haja apenas um único disco virtual envolvido. Mas, mesmo com um único disco, outros itens devem ser verificados. A [Observação 2039883 do SAP](https://launchpad.support.sap.com/#/notes/2039883) possui informações importantes sobre backups do SAP HANA por meio de instantâneos de armazenamento. Por exemplo, ela menciona que, com o sistema de arquivos XFS, é necessário executar **xfs\_congelar** antes de iniciar um instantâneo de armazenamento para garantir a consistência (consulte [xfs\_congelar(8) - página do manual Linux](https://linux.die.net/man/8/xfs_freeze) para obter detalhes sobre **xfs\_congelar**).

O tópico de consistência se torna ainda mais desafiador em casos onde um sistema de arquivos único abrange vários discos/volumes. Por exemplo, ao usar mdadm ou LVM e distribuição. A Observação SAP mencionada acima diz o seguinte:

_&quot;Mas tenha em mente que o sistema de armazenamento deve garantir a consistência de E/S ao criar um instantâneo de armazenamento por volume de dados do SAP HANA, ou seja, o instantâneo de um volume de dados específicos de serviço do SAP HANA deve ser uma operação atômica.&quot;_

Supondo que haja um sistema de arquivos XFS abrangendo quatro discos virtuais do Azure, as seguintes etapas fornecem um instantâneo consistente que representa a área de dados do HANA:

- Preparação do instantâneo do HANA
- Congele o sistema de arquivos (por exemplo, use **xfs\_congelar**)
- Crie todos os instantâneos de blob necessários no Azure
- Descongele o sistema de arquivos
- Confirme o instantâneo do HANA

A recomendação é usar o procedimento acima em todos os casos, por segurança, não importa qual sistema de arquivos. Ou, se for um disco único ou distribuição, via mdadm ou LVM em vários discos.

É importante confirmar o instantâneo do HANA. Devido à &quot;Cópia em gravação,&quot; o SAP HANA pode não precisar de espaço em disco adicional enquanto estiver no modo de preparação do instantâneo. Também não é possível iniciar novos backups até que o instantâneo do SAP HANA seja confirmado.

O Serviço de Backup do Azure usa extensões da VM do Azure para tratar da consistência do sistema de arquivos. Essas extensões de VM não estão disponíveis para uso independente. Ainda é preciso gerenciar a consistência do SAP HANA. Consulte o artigo relacionado [Backup do Azure do SAP HANA para arquivos](sap-hana-backup-file-level.md) para obter mais informações.

### <a name="sap-hana-backup-scheduling-strategy"></a>Estratégia de agendamento de backup do SAP HANA

O artigo do SAP HANA [Planejando sua estratégia de backup e recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) descreve um plano básico para fazer backups:

- Realize um instantâneo do armazenamento (diariamente)
- Faça um backup completo de dados usando o arquivo ou formato bacint (uma vez por semana)
- Backups de log automático

Opcionalmente, é possível não realizar nenhum instantâneo de armazenamento; eles podem ser substituídos por backups de delta do HANA, como backups incrementais ou diferenciais (consulte [Backups Delta](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

O guia de administração do HANA fornece uma lista de exemplo. Ele sugere que é possível recuperar o SAP HANA para um ponto específico no tempo usando a seguinte sequência de backups:

1. Backup de dados completo
2. Backup diferencial
3. Backup incremental 1
4. Backup incremental 2
5. Backups de log

Em relação a um agendamento exato sobre quando e com que frequência um tipo específico de backup deve ocorrer, não é possível fornecer uma orientação geral, isso é algo muito específico para cada cliente e depende de quantas alterações de dados ocorrem no sistema. Uma recomendação básica do lado do SAP, que pode ser vista como orientação geral, é criar um backup do HANA completo uma vez por semana.
Em relação aos backups de log, consulte a documentação do SAP HANA [Backups de Log](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

O SAP também recomenda a limpeza do catálogo de backup para impedir que ele cresça infinitamente (consulte [Manutenção para o catálogo de backup e armazenamento de backup](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>Arquivos de configuração do SAP HANA

Conforme mencionado nas pergunta frequentes da [Observação 1642148 do SAP](https://launchpad.support.sap.com/#/notes/1642148), os arquivos de configuração do SAP HANA não fazem parte de um backup padrão do HANA. Eles não são essenciais para restaurar um sistema. A configuração do HANA pode ser alterada manualmente após a restauração. Caso seja desejado obter a mesma configuração personalizada durante o processo de restauração, é necessário fazer backup dos arquivos de configuração do HANA separadamente.

Se os backups padrões do HANA forem para um sistema de arquivos de backup do HANA, também é possível copiar os arquivos de configuração para o mesmo sistema de arquivos de backup e copiar tudo para o destino final de armazenamento como armazenamento de blobs frio.

### <a name="sap-hana-cockpit"></a>Ferramenta Cockpit do SAP HANA

A Ferramenta Cockpit do SAP HANA oferece a possibilidade de monitorar e gerenciar o SAP HANA por meio de um navegador. Ele também permite a manipulação de backups do SAP HANA e, portanto, pode ser usado como uma alternativa ao Studio do SAP HANA e ao ABAP DBACOCKPIT (consulte [Ferramenta Cockpit do SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) para obter mais informações).

![Esta figura mostra a tela de administração de banco de dados da Ferramenta Cockpit do SAP HANA](media/sap-hana-backup-guide/image004.png)

Esta figura mostra a tela de administração de banco de dados da Ferramenta Cockpit do SAP HANA e o bloco de backup à esquerda. Ver o bloco de backup requer permissões de usuário apropriadas para a conta de logon.

![Os backups podem ser monitorados na Ferramenta Cockpit do SAP HANA enquanto eles estiverem em andamento](media/sap-hana-backup-guide/image005.png)

Os backups podem ser monitorados na Ferramenta Cockpit do SAP HANA enquanto estiverem em andamento e, após a conclusão, todos os detalhes de backup estão disponíveis.

![Um exemplo usando o Firefox em uma VM do Azure SLES 12 com área de trabalho Gnome](media/sap-hana-backup-guide/image006.png)

As capturas de tela anteriores foram feitas em uma VM do Azure Windows. Esse é um exemplo usando o Firefox em uma VM do Azure SLES 12 com área de trabalho Gnome Ele mostra a opção de definir agendamentos de backup do SAP HANA na Ferramenta Cockpit do SAP HANA. Como é possível ver, ele sugere a data/hora como um prefixo para os arquivos de backup. No SAP HANA Studio, o prefixo padrão é &quot;COMPLETE\_DATA\_BACKUP&quot; ao fazer um backup completo do arquivo. É recomendável usar um prefixo exclusivo.

### <a name="sap-hana-backup-encryption"></a>Criptografia de backup do SAP HANA

O SAP HANA oferece criptografia de dados e de log. Se os dados do SAP HANA e log não são criptografados, então os backups também não são criptografados. Cabe ao cliente usar algum tipo de solução de terceiros para criptografar os backups do SAP HANA. Consulte [Criptografia de volume de log e dados](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) para obter mais informações sobre a criptografia do SAP HANA.

No Microsoft Azure, um cliente pode usar o recurso de criptografia de VM do IaaS para fazer a criptografia. Por exemplo, é possível usar discos de dados dedicados anexados à VM, que são usados para armazenar os backups do SAP HANA, em seguida, fazer cópias desses discos.

O Serviço de Backup do Azure pode manipular discos/VMs criptografados (consulte [Como fazer backup e restaurar máquinas virtuais criptografadas com criptografia do backup do Azure](../../../backup/backup-azure-vms-encryption.md)).

Outra opção seria manter a VM do SAP HANA e seus discos sem criptografia e armazenar os arquivos de backup do SAP HANA em uma conta de armazenamento para a qual a criptografia foi habilitada (consulte [Criptografia do serviço de armazenamento do Azure para dados em repouso](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Configuração de teste

### <a name="test-virtual-machine-on-azure"></a>Teste da máquina virtual no Azure

Para executar nossos testes, foi usada uma instalação do SAP HANA em uma VM GS5 do Azure para os seguintes testes de backup/restauração. Os princípios são os mesmos que os das VMs da série M.

![Esta figura mostra uma parte da visão geral do portal do Azure para a VM de teste do HANA](media/sap-hana-backup-guide/image007.png)

Esta figura mostra uma parte da visão geral do portal do Azure para a VM de teste do HANA.

### <a name="test-backup-size"></a>Teste do tamanho do backup

![Esta figura representa o console de backup no HANA Studio e mostra o tamanho do arquivo de backup de 229 GB para o servidor de índice do HANA](media/sap-hana-backup-guide/image008.png)

Uma tabela fictícia estava cheia de dados para obter um tamanho do backup total de dados de mais de 200 GB para produzir dados de desempenho realísticos. A figura representa o console de backup no HANA Studio e mostra o tamanho do arquivo de backup de 229 GB para o servidor de índice do HANA. Para os testes, o prefixo de backup padrão "COMPLETE_DATA_BACKUP" no SAP HANA Studio foi usado. Em sistemas de produção real, um prefixo mais útil deve ser definido. A Ferramenta Cockpit da SAP HANA sugere a data/hora.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Teste a ferramenta para copiar arquivos diretamente para o armazenamento do Azure

Para transferir arquivos de backup do SAP HANA diretamente para o armazenamento de blobs do Azure ou compartilhamentos de arquivos do Azure, a ferramenta blobxfer foi usada, pois esta oferece suporte a ambos os destinos e pode ser facilmente integrada aos scripts de automação devido à sua interface de linha de comando. A ferramenta blobxfer está disponível no [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Teste a estimativa de tamanho do backup

É importante estimar o tamanho do backup do SAP HANA. Essa estimativa ajuda a melhorar o desempenho, definindo o tamanho máximo de arquivo de backup para diferentes arquivos de backup, devido ao paralelismo durante a cópia de um arquivo. (Esses detalhes são explicados mais adiante neste artigo.) Também é possível decidir se será feito um backup completo ou um backup delta (incremental ou diferencial).

Felizmente, há uma instrução SQL simples que calcula o tamanho dos arquivos de backup: **select \* from M\_BACKUP\_SIZE\_ESTIMATIONS** (consulte [Estimar o espaço necessário no sistema de arquivos para um backup de dados](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![A saída desta instrução SQL corresponde quase ao tamanho real do backup completo de dados em disco](media/sap-hana-backup-guide/image009.png)

Para o sistema de teste, a saída desta instrução SQL corresponde quase ao tamanho real do backup completo de dados em disco.

### <a name="test-hana-backup-file-size"></a>Teste o tamanho do arquivo de backup do HANA

![O console do backup do HANA Studio permite restringir o tamanho máximo dos arquivos de backup do HANA](media/sap-hana-backup-guide/image010.png)

O console do backup do HANA Studio permite restringir o tamanho máximo dos arquivos de backup do HANA. No ambiente de exemplo, esse recurso torna possível obter vários arquivos menores de backup em vez de um arquivo de backup de 230 GB. O tamanho de arquivo menor tem um impacto significativo no desempenho (consulte o artigo relacionado [Backup do SAP HANA no Azure em nível de arquivo](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Resumo

Com base nos resultados do teste, as tabelas a seguir mostram os prós e contras das soluções para fazer backup de um banco de dados do SAP HANA em execução em máquinas virtuais do Azure.

**Faça o backup do SAP HANA para o sistema de arquivos e copie os arquivos de backup posteriormente para o destino de backup final**

|Solução                                           |Prós                                 |Contras                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Mantém os backups do HANA em discos de VM                      |Nenhum trabalho de gerenciamento adicional     |Consome espaço em disco da VM local           |
|A ferramenta Blobxfer para copiar arquivos de backup para o armazenamento de blob |Paralelismo para copiar vários arquivos, opção de uso do armazenamento de blobs frio | Manutenção de ferramenta adicional e scripts personalizados | 
|Cópia de blob por meio do Powershell ou CLI                    |Nenhuma ferramenta adicional necessária, pode ser realizada por meio do Azure Powershell ou CLI |processo manual, o cliente deve cuidar dos scripts e gerenciamento de blobs copiados para restauração|
|Copiar para o compartilhamento de NFS                                  |Pós-processamento de arquivos de backup em outra VM sem impacto no servidor do HANA|Processo de cópia lento|
|Cópia blobxfer ao serviço de arquivos do Azure                |Não consome espaço nos discos VM locais|Sem suporte direto para gravação para backup do HANA, restrição de tamanho do compartilhamento de arquivos no momento em 5 TB|
|Agente de Backup do Azure                                 | Seria a melhor solução         | Não disponível no momento no Linux    |



**Backup do SAP HANA baseado em instantâneos de armazenamento**

|Solução                                           |Prós                                 |Contras                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Serviço de Backup do Azure                               | Permite o backup da VM com base em instantâneos de blob | Quando a restauração no nível de arquivo não for usada, é exigida a criação de uma nova VM para o processo de restauração, o que implica, em seguida, a necessidade de uma nova chave de licença do SAP HANA|
|Instantâneos de blob manual                              | Flexibilidade para criar e restaurar discos de VM específicos sem alterar a ID exclusiva da VM|Todo trabalho manual, que deve ser feito pelo cliente|

## <a name="next-steps"></a>Próximas etapas
* O [Backup do SAP HANA no Azure em nível de arquivo](sap-hana-backup-file-level.md) descreve a opção de backup baseada em arquivo.
* [O backup do SAP HANA com base em instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md) descreve a opção de backup baseados em instantâneo de armazenamento.
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastres do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
