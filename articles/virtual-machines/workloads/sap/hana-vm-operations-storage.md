---
title: Configurações de armazenamento de máquina virtual do SAP HANA no Azure | Microsoft Docs
description: Recomendações de armazenamento para VMs que têm o SAP HANA implantado nelas.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure HANA, ultra Disk de armazenamento, armazenamento Premium
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/03/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0c0fbb1280fc2a7eaca1d97e7e016cf480873c8b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666579"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurações de armazenamento de máquina virtual do SAP HANA no Azure

O Azure fornece diferentes tipos de armazenamento principais adequados a máquinas virtuais do Azure que executam o SAP HANA. Os **tipos de armazenamento do Azure certificados pelo SAP HANA** que podem ser considerados para implantações do SAP HANA são listados como: 

- Armazenamento Premium ou SSD do Azure Premium 
- [Disco Ultra](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Para saber mais sobre esses tipos de disco, confira o artigo [tipos de armazenamento do Azure para carga de trabalho do SAP](./planning-guide-storage.md) e [Selecione um tipo de disco](../../disks-types.md)

O Azure oferece dois métodos de implantação para VHDs no armazenamento Standard e Premium do Azure. Esperamos que você aproveite o [disco gerenciado do Azure](https://azure.microsoft.com/services/managed-disks/) para implantações de armazenamento em bloco do Azure. 

Para obter uma lista de tipos de armazenamento e os respectivos SLAs em IOPS e taxa de transferência de armazenamento, revise a [documentação do Azure para discos gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Independentemente do tipo de armazenamento do Azure escolhido, o sistema de arquivos usado nesse armazenamento precisa ter suporte do SAP para o sistema operacional específico e o DBMS. [Nota de suporte SAP #2972496](https://launchpad.support.sap.com/#/notes/2972496) lista os sistemas de arquivos com suporte para diferentes sistemas operacionais e bancos de dados, incluindo SAP Hana. Isso se aplica a todos os volumes que o SAP HANA pode acessar para leitura e gravação de qualquer tarefa. Especificamente usando o NFS no Azure para o SAP HANA, restrições adicionais de versões do NFS se aplicam conforme mencionado mais adiante neste artigo 


As condições mínimas certificadas pelo SAP HANA para os diferentes tipos de armazenamento são: 

- O armazenamento Premium do Azure- **/Hana/log** precisa ter suporte do Azure [acelerador de gravação](../../how-to-enable-write-accelerator.md). O volume **/Hana/data** pode ser colocado no armazenamento Premium sem o Azure acelerador de gravação ou no ultra Disk
- Ultra Disk do Azure pelo menos para o volume **/Hana/log** . O volume **/Hana/data** pode ser colocado em um armazenamento Premium sem o Azure acelerador de gravação ou para obter tempos de reinicialização mais rápidos
- Volumes **NFS v 4.1** sobre Azure NetApp Files para **/Hana/log e/Hana/data**. O volume de/Hana/Shared pode usar o protocolo NFS v3 ou NFS v 4.1

Alguns tipos de armazenamento podem ser combinados. Por exemplo, é possível colocar **/Hana/data** no armazenamento Premium e **/Hana/log** pode ser colocado em armazenamento de ultra disco para obter a baixa latência necessária. Se você usar um volume baseado em seja para **/Hana/data**, o  **/Hana/log** volume também precisará ser baseado em NFS na parte superior do seja. **Não há suporte** para o uso de NFS sobre seja para um dos volumes (como/Hana/Data) e armazenamento Premium do Azure ou ultra Disk para o outro volume (como **/Hana/log**).

No mundo local, você raramente precisava preocupar-se com os subsistemas de E/S e as funcionalidades. O motivo era que o fornecedor do dispositivo precisava certificar-se de que os requisitos mínimos de armazenamento fossem atendidos para o SAP HANA. Ao criar a infraestrutura do Azure por conta própria, você deve estar ciente de alguns desses requisitos emitidos pelo SAP. Algumas das características mínimas de taxa de transferência que o SAP recomenda, são:

- Leitura/gravação em **/Hana/log** de 250 MB/s com tamanhos de e/s de 1 MB
- Leia a atividade de pelo menos 400 MB/s para **/Hana/data** para os tamanhos de e/s de 16 mb e 64 MB
- Atividade de gravação de pelo menos 250 MB/s para **/Hana/data** com tamanhos de e/s de 16 mb e 64 MB

Uma vez que a baixa latência de armazenamento é crítica aos sistemas DBMS, mesmo que o DBMS, como SAP HANA, mantenha os dados na memória. O caminho crítico no armazenamento é geralmente em torno das gravações de log de transações dos sistemas DBMS. Mas também operações como gravar pontos de salvamento ou carregar dados na memória após a recuperação de falhas poderão ser críticas. Portanto, é **obrigatório** aproveitar o armazenamento Premium do Azure, ultra Disk ou seja para volumes **/Hana/data** e **/Hana/log** . 


Alguns princípios de orientação na seleção da configuração de armazenamento para HANA podem ser listados como:

- Decida sobre o tipo de armazenamento com base nos [tipos de armazenamento do Azure para carga de trabalho SAP](./planning-guide-storage.md) e [Selecione um tipo de disco](../../disks-types.md)
- A taxa de transferência de e/s de VM geral e os limites de IOPS em mente ao dimensionar ou decidir uma VM. A taxa de transferência geral do armazenamento de VM está documentada no artigo [tamanhos de máquina virtual com otimização de memória](../../sizes-memory.md)
- Ao decidir pela configuração de armazenamento, tente ficar abaixo da taxa de transferência geral da VM com a configuração do volume **/Hana/data** . Escrevendo pontos de salvamento, SAP HANA pode ser a emissão agressiva de e/SS. É fácil enviar até limites de taxa de transferência do volume **/Hana/data** ao escrever um salvamento de pontos. Se os discos que criam o volume **/Hana/data** tiverem uma taxa de transferência maior do que a sua VM permitir, você poderá se deparar com situações em que a taxa de transferência utilizada pela gravação do ponto de salvamento está interferindo com as demandas de taxa de transferência das gravações de log de restauração. Uma situação que pode afetar a taxa de transferência do aplicativo


> [!IMPORTANT]
> As sugestões para as configurações de armazenamento são destinadas como instruções para começar. Executando a carga de trabalho e analisando os padrões de utilização de armazenamento, você pode perceber que não está utilizando toda a largura de banda de armazenamento ou IOPS fornecidos. Em seguida, você pode considerar o downsizing no armazenamento. Ou, em contrário, sua carga de trabalho pode precisar de mais taxa de transferência de armazenamento do que o sugerido com essas configurações. Como resultado, talvez seja necessário implantar mais capacidade, IOPS ou taxa de transferência. No campo de tensão entre a capacidade de armazenamento necessária, a latência de armazenamento necessária, a taxa de transferência de armazenamento e o IOPS necessários e a configuração menos dispendiosa, o Azure oferece tipos de armazenamento diferentes suficientes com diferentes recursos e pontos de preço diferentes para localizar e ajustar para o comprometimento correto para você e sua carga de trabalho do HANA.


## <a name="stripe-sets-versus-sap-hana-data-volume-partitioning"></a>Conjuntos de distribuição versus SAP HANA particionamento de volume de dados
Usando o armazenamento Premium do Azure, você pode atingir a melhor taxa de preço/desempenho ao distribuir o volume de **/Hana/data** e/ou **/Hana/log** em vários discos do Azure. Em vez de implantar volumes de disco maiores que fornecem mais informações sobre IOPS ou taxa de transferência necessária. Até agora, isso foi feito com os gerenciadores de volumes LVM e MDADM que fazem parte do Linux. O método de distribuição de discos é décadas antigos e bem conhecidos. Tão benéfico quanto os volumes distribuídos para chegar aos recursos de IOPS ou de taxa de transferência que você pode precisar, ele adiciona complexidades ao gerenciamento desses volumes distribuídos. Especialmente em casos em que os volumes precisam ser estendidos na capacidade. Pelo menos para **/Hana/data**, o SAP introduziu um método alternativo que alcança a mesma meta que a distribuição entre vários discos do Azure. Desde SAP HANA 2,0 SPS03, o HANA indexserver é capaz de distribuir sua atividade de e/s em vários arquivos de dados do HANA que estão localizados em diferentes discos do Azure. A vantagem é que você não precisa cuidar da criação e do gerenciamento de um volume distribuído em diferentes discos do Azure. A funcionalidade SAP HANA do particionamento de volume de dados é descrita em detalhes em:

- [O guia do administrador do HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog sobre SAP HANA – Particionando volumes de dados](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [Observação SAP #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [Observação SAP #2700123](https://launchpad.support.sap.com/#/notes/2700123)

Lendo os detalhes, é aparente que aproveitar essa funcionalidade retira as complexidades dos conjuntos de distribuição baseados no Gerenciador de volumes. Você também percebe que o particionamento do volume de dados do HANA não está funcionando apenas para o armazenamento de blocos do Azure, como o armazenamento Premium do Azure. Você pode usar essa funcionalidade também para distribuir entre compartilhamentos NFS caso esses compartilhamentos tenham limitações de IOPS ou taxa de transferência.  


## <a name="linux-io-scheduler-mode"></a>Modo Agendador de E/S do Linux
O Linux possui vários modos de agendamento de E/S diferentes. A recomendação comum por meio de fornecedores do Linux e do SAP é reconfigurar o modo do Agendador de e/s para volumes de disco do modo **Kyber** ou do **data limite do MQ** para o modo **NOOP** (não-multifila) ou **nenhum** para (Multiqueue), se ainda não for feito pelos perfis do SLES saptune. Os detalhes são referenciados em: 

- [Observação SAP #1984787](https://launchpad.support.sap.com/#/notes/1984787)
- [Observação SAP #2578899](https://launchpad.support.sap.com/#/notes/2578899) 
- [Problema com a configuração NOOP no SLES 12 SP4](https://www.suse.com/support/kb/doc/?id=000019547)

No Red Hat, deixe as configurações estabelecidas pelos perfis de ajuste específicos para os diferentes aplicativos SAP.


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluções com armazenamento Premium e Acelerador de Gravação do Azure para máquinas virtuais da série M do Azure
O Acelerador de Gravação do Azure é uma funcionalidade disponível exclusivamente para VMs da Série M do Azure. Como o nome indica, a finalidade da funcionalidade é melhorar a latência de e/s de gravações no armazenamento Premium do Azure. Para o SAP HANA, acelerador de gravação deve ser usado em relação a **hana/log** somente no volume. Portanto, **/hana/data** e **/gana/log** são volumes separados com o Acelerador de Gravação do Azure que dão suporte apenas ao volume **/hana/log**. 

> [!IMPORTANT]
> Ao usar o armazenamento Premium do Azure, o uso do Azure [acelerador de gravação](../../how-to-enable-write-accelerator.md) para o volume **/Hana/log** é obrigatório. Acelerador de Gravação está disponível para armazenamento Premium e somente para VMs da série M e do Mv2-Series. Acelerador de Gravação não está funcionando em combinação com outras famílias de VM do Azure, como Esv3 ou Edsv4.

As recomendações de cache para os discos Premium do Azure abaixo estão supondo as características de e/s para SAP HANA essa lista, como:

- Praticamente, não há nenhuma carga de trabalho de leitura nos arquivos de dados do HANA. As exceções são E / Ss de tamanho grande após o reinício da instância do HANA ou quando os dados são carregados no HANA. Outro caso de E/Ss de leitura maiores em arquivos de dados podem ser backups de banco de dados do HANA. Como resultado, em grande parte, o cache de leitura não faz sentido pois, na maioria dos casos, todos os volumes de arquivos de dados precisam ser lidos na íntegra. 
- A gravação nos arquivos de dados está ocorrendo em intermitências com base nos pontos de salvamento e na recuperação de pane do HANA. Os pontos de salvamento de gravação são assíncronos e não impedem nenhuma transação de usuário. A gravação de dados durante a recuperação de pane é crítica para o desempenho para que o sistema responda com rapidez novamente. No entanto, a recuperação de pane devem ser situações excepcionais
- Praticamente, não há nenhuma leitura dos arquivos de refazer do HANA. As exceções são E/Ss grandes ao executar backups de log de transações, recuperação de pane ou na fase de reinicialização de uma instância do HANA.  
- A carga principal no arquivo de log de refazer do SAP HANA são gravações. Dependendo da natureza da carga de trabalho, você pode ter E/Ss pequenas de 4 KB ou, em outros casos, tamanhos de E/S iguais ou superiores a 1 MB. A latência de gravação no log de refazer do SAP HANA é crítica para o desempenho.
- Todas as gravações precisam ser persistidas em disco de forma confiável

**Recomendação: como resultado desses padrões de e/s observados por SAP HANA, o cache para os diferentes volumes usando o armazenamento Premium do Azure deve ser definido como:**

- **/Hana/data** -sem cache ou cache de leitura
- **/Hana/log** -sem cache-exceção para VMs M-e Mv2-Series em que o acelerador de gravação do Azure deve ser habilitado 
- **hana/shared** - ler o cache
- **Disco do sistema operacional** -não altere o cache padrão definido pelo Azure no momento da criação da VM


Se você estiver usando LVM ou mdadm para criar conjuntos de distribuição em vários discos Premium do Azure, será necessário definir tamanhos de distribuição. Esses tamanhos diferem entre **/Hana/data** e **/Hana/log**. **Recomendação: como tamanhos de distribuição, a recomendação é usar:**

- 256 KB para **/hana/data**
- 64 KB para **/Hana/log**

> [!NOTE]
> O tamanho de distribuição para **/Hana/data** foi alterado de recomendações anteriores chamando 64 kb ou 128 kb para 256 KB com base nas experiências do cliente com versões mais recentes do Linux. O tamanho de 256 KB fornece um desempenho ligeiramente melhor. Também alteramos a recomendação para os tamanhos de distribuição de **/Hana/log** de 32 kb a 64 KB para obter uma taxa de transferência suficiente com tamanhos maiores de e/s.

> [!NOTE]
> Você não precisa configurar nenhum nível de redundância usando volumes RAID, pois o armazenamento de blocos do Azure mantém três imagens de um VHD. O uso de um conjunto de distribuição com discos Premium do Azure é puramente para configurar volumes que fornecem IOPS e/ou taxa de transferência de e/s suficiente.

A acumulação de vários VHDs do Azure sob um conjunto de distribuição é acumulada de um lado de taxa de transferência de IOPS e armazenamento. Portanto, se você colocar um conjunto de distribuição em mais de 3 x p30 discos de armazenamento Premium do Azure, ele deverá fornecer três vezes o IOPS e três vezes a taxa de transferência de armazenamento de um único disco p30 de armazenamento Premium do Azure.

> [!IMPORTANT]
> Caso você esteja usando LVM ou mdadm como Gerenciador de volumes para criar conjuntos de distribuição em vários discos Premium do Azure, os três SAP HANA sistemas de/data,/log e/Shared não devem ser colocados em um grupo de volumes padrão ou raiz. É altamente recomendável seguir as diretrizes de fornecedores do Linux, que costumam criar grupos de volumes individuais para /data, /log e /shared.


### <a name="azure-burst-functionality-for-premium-storage"></a>Funcionalidade de intermitência do Azure para armazenamento Premium
Para discos de armazenamento Premium do Azure menores ou iguais a 512 GiB em capacidade, a funcionalidade de intermitência é oferecida. A maneira exata de como funciona a intermitência de disco é descrita no artigo [intermitência de disco](../../disk-bursting.md). Ao ler o artigo, você entende o conceito de acumular IOPS e taxa de transferência nos momentos em que a carga de trabalho de e/s está abaixo do IOPS nominal e da taxa de transferência dos discos (para obter detalhes sobre a taxa de transferência nominal, consulte [preço do disco gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/)). Você vai acumular o Delta de IOPS e a taxa de transferência entre o uso atual e os valores nominais do disco. As intermitências são limitadas a um máximo de 30 minutos.

Os casos ideais em que essa funcionalidade de intermitência pode ser planejada provavelmente serão os volumes ou discos que contêm arquivos de dados para o DBMS diferente. A carga de trabalho de e/s esperada nesses volumes, especialmente com sistemas de pequeno a médio porte, deve parecer com a seguinte aparência:

- Carga de trabalho de leitura baixa a moderada, pois os dados são idealmente armazenados em cache na memória, ou como no caso do HANA devem estar completamente na memória
- Picos de gravação disparadas por pontos de verificação de banco de dados ou ponto de salvamento emitidos regularmente
- Carga de trabalho de backup que lê em um fluxo contínuo em casos em que os backups não são executados por meio de instantâneos de armazenamento
- Por SAP HANA, carregar os dados na memória após uma reinicialização da instância

Especialmente em sistemas DBMS menores em que sua carga de trabalho está lidando com apenas algumas centenas de transações por segundo, uma funcionalidade de intermitência também pode fazer sentido para os discos ou volumes que armazenam a transação ou efetuar o log de refazer. A carga de trabalho esperada em um disco ou em volumes é semelhante a:

- Gravações regulares no disco que são dependentes da carga de trabalho e a natureza da carga de trabalho, pois cada confirmação emitida pelo aplicativo provavelmente disparam uma operação de e/s
- Maior carga de trabalho na taxa de transferência para casos de tarefas operacionais, como criar ou recriar índices
- Ler intermitências ao executar o log de transações ou fazer backups de log de restauração


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>Solução de armazenamento recomendada para produção com base no armazenamento Premium do Azure

> [!IMPORTANT]
> Certificação do SAP HANA para máquinas virtuais da série M do Azure é exclusivamente com o Acelerador de gravação do Azure para o **hana/log** volume. Como resultado, as implantações de SAP HANA de cenário de produção em máquinas virtuais da série M do Azure devem ser configurados com o Acelerador de gravação do Azure para o **hana/log** volume.  

> [!NOTE]
> Em cenários que envolvem o armazenamento Premium do Azure, estamos implementando recursos de intermitência na configuração. Como você está usando as ferramentas de teste de armazenamento de qualquer forma ou formulário, mantenha o [funcionamento do Azure Premium Disk Burst](../../disk-bursting.md) em mente. Executando os testes de armazenamento fornecidos por meio da ferramenta SAP HWCCT ou HCMT, não estamos esperando que todos os testes passem os critérios, já que alguns dos testes excederão os créditos de intermitência que você pode acumular. Especialmente quando todos os testes são executados sequencialmente sem interrupção.


> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo de VM tem suporte pelo SAP HANA pelo SAP na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Recomendação: as configurações recomendadas com o armazenamento Premium do Azure para cenários de produção são semelhantes a:**

Configuração do volume do SAP **/Hana/data** :

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | /hana/data | Taxa de transferência provisionada | Taxa de transferência máxima de intermitência | IOPS | IOPS de intermitência |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14.000 |
| M32ls | 256 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14.000 |
| M64ls | 512 GiB | 1\.000 MBps | 4 x P10 | 400 MBps | 680 MBps | 2\.000 | 14.000 |
| M64s | 1\.000 GiB | 1\.000 MBps | 4 x P15 | 500 MBps | 680 MBps | 4.400 | 14.000 |
| M64ms | 1\.750 GiB | 1\.000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9.200 | 14.000 |  
| M128s | 2\.000 GiB | 2.000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9.200| 14.000 | 
| M128ms | 3\.800 GiB | 2.000 MBps | 4 x P30 | 800 MBps | sem intermitência | 20.000 | sem intermitência | 
| M208s_v2 | 2\.850 GiB | 1\.000 MBps | 4 x P30 | 800 MBps | sem intermitência | 20.000| sem intermitência | 
| M208ms_v2 | 5\.700 GiB | 1\.000 MBps | 4 x P40 | 1\.000 MBps | sem intermitência | 30,000 | sem intermitência |
| M416s_v2 | 5\.700 GiB | 2.000 MBps | 4 x P40 | 1\.000 MBps | sem intermitência | 30,000 | sem intermitência |
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 4 x P50 | 2.000 MBps | sem intermitência | 30,000 | sem intermitência |


Para o volume **/Hana/log** . a configuração teria a seguinte aparência:

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | volume **/Hana/log** | Taxa de transferência provisionada | Taxa de transferência máxima de intermitência | IOPS | IOPS de intermitência |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1\.500 | 10.500 | 
| M32ls | 256 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1\.500 | 10.500 | 
| M64ls | 512 GiB | 1\.000 MBps | 3 x P10 | 300 MBps | 510 MBps | 1\.500 | 10.500 | 
| M64s | 1\.000 GiB | 1\.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 | 
| M64ms | 1\.750 GiB | 1\.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 |  
| M128s | 2\.000 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500|  
| M128ms | 3\.800 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 | 
| M208s_v2 | 2\.850 GiB | 1\.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 |  
| M208ms_v2 | 5\.700 GiB | 1\.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 |  
| M416s_v2 | 5\.700 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 |  
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 | 


Para os outros volumes, a configuração teria a seguinte aparência:

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | /hana/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MBps | 1 x P15 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 MBps |  1 x P15 | 1 x P6 | 1 x P6 |
| M64ls | 512 GiB | 1000 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1\.000 GiB | 1\.000 MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1\.750 GiB | 1\.000 MBps | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2\.000 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3\.800 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2\.850 GiB | 1\.000 MBps |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5\.700 GiB | 1\.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5\.700 GiB | 2.000 MBps |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 


Verifique se a taxa de transferência de armazenamento para os diferentes volumes recomendados atende à carga de trabalho que você quer executar. Se a carga de trabalho exigir volumes maiores para **/Hana/data** e **/Hana/log**, você precisará aumentar o número de VHDs de armazenamento Premium do Azure. Dimensionar um volume com mais VHDs do que o listado aumenta a taxa de transferência de E/S e IOPS dentro dos limites do tipo de máquina virtual do Azure.

O Acelerador de Gravação do Azure funciona somente em conjunto com o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Portanto, pelo menos os discos de armazenamento Premium do Azure que formam o volume **/Hana/log** precisam ser implantados como discos gerenciados. Instruções e restrições mais detalhadas do Azure Acelerador de Gravação podem ser encontradas no artigo [acelerador de gravação](../../how-to-enable-write-accelerator.md).

Para as VMs certificadas do HANA da família Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) e do [Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series), você precisará seja para o volume **/Hana/data** e **/Hana/log** . Ou você precisa aproveitar o armazenamento do Azure ultra Disk em vez do armazenamento Premium do Azure somente para o volume **/Hana/log** . Como resultado, as configurações para o volume **/Hana/data** no armazenamento Premium do Azure poderiam ser semelhantes a:

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | /hana/data | Taxa de transferência provisionada | Taxa de transferência máxima de intermitência | IOPS | IOPS de intermitência |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MBps | 3 x P10 | 300 MBps | 510 MBps | 1\.500 | 10.500 |
| E32ds_v4 | 256 GiB | 768 MBps | 3 x P10 |  300 MBps | 510 MBps | 1\.500 | 10.500|
| E48ds_v4 | 384 GiB | 1.152 MBps | 3 x P15 |  375 MBps |510 MBps | 3.300  | 10.500 | 
| E64ds_v4 | 504 GiB | 1\.200 MBps | 3 x P15 |  375 MBps | 510 MBps | 3.300 | 10.500 | 
| E64s_v3 | 432 GiB | 1\.200 MB/s | 3 x P15 |  375 MBps | 510 MBps | 3.300 | 10.500 | 

Para os outros volumes, incluindo **/Hana/log** no ultra Disk, a configuração poderia ser semelhante a:

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | Volume /hana/log | Taxa de transferência de E/S /hana/log | IOPS /hana/log | /hana/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MBps | 80 GB | 250 MBps | 1.800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 MBps | 128 GB | 250 MBps | 1.800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1.152 MBps | 192 GB | 250 MBps | 1.800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1\.200 MBps | 256 GB | 250 MBps | 1.800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1\.200 MBps | 220 GB | 250 MBps | 1.800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configuração de armazenamento de disco Ultra do Azure para SAP HANA
Outro tipo de armazenamento do Azure é chamado de [ultra Disk do Azure](../../disks-types.md#ultra-disk). A diferença significativa entre o armazenamento do Azure oferecido até o momento e o disco Ultra é que as funcionalidades de disco não estão mais vinculadas ao tamanho do disco. Como um cliente, você pode definir esses recursos para o disco Ultra:

- Tamanho de um disco que varia de 4 GiB a 65.536 GiB
- O IOPS varia de 100 IOPS a 160 mil IOPS (o máximo também depende dos tipos de VM)
- Taxa de transferência de armazenamento de 300 MB/s a 2.000 MB/s

O disco Ultra oferece a possibilidade de definir um único disco que atenda a seu tamanho, ao IOPS e ao intervalo de taxa de transferência de disco. Em vez de usar gerenciadores de volume lógico como LVM ou MDADM sobre o armazenamento Premium do Azure para construir volumes que atendam aos requisitos de taxa de transferência de armazenamento e IOPS. Você pode executar uma combinação de configuração entre o ultra Disk e o armazenamento Premium. Como resultado, você pode limitar o uso de ultra Disk para os volumes críticos de desempenho **/Hana/data** e **/Hana/log** e abranger os outros volumes com o armazenamento Premium do Azure

Outras vantagens do ultra Disk podem ser a melhor latência de leitura em comparação com o armazenamento Premium. A latência de leitura mais rápida pode ter vantagens quando você deseja reduzir os tempos de inicialização do HANA e a carga subsequente dos dados na memória. As vantagens do armazenamento de disco Ultra também podem ser sentidas quando o HANA está escrevendo pontos de salvamento. 

> [!NOTE]
> O disco Ultra ainda não está presente em todas as regiões do Azure e também ainda não está dando suporte a todos os tipos de VM listados abaixo. Para obter informações detalhadas sobre onde o disco Ultra está disponível para quais famílias de VM há suporte, consulte o artigo [Quais tipos de disco estão disponíveis no Azure?](../../disks-types.md#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Solução de armazenamento recomendada para produção com configuração de disco Ultra pura
Nessa configuração, você mantém os volumes **/Hana/data** e **/Hana/log** separadamente. Os valores sugeridos são derivados dos KPIs que o SAP precisa certificar para tipos de VM para SAP HANA e as configurações de armazenamento, conforme recomendado no [Whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

As recomendações geralmente estão excedendo os requisitos mínimos do SAP, conforme mencionado anteriormente neste artigo. As recomendações listadas são um comprometimento entre as recomendações de tamanho do SAP e a taxa de transferência de armazenamento máxima que os diferentes tipos de VM fornecem.

> [!NOTE]
> O ultra Disk do Azure está impondo um mínimo de 2 IOPS por capacidade de Gigabyte de um disco


| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | Volume /hana/data | Taxa de transferência de E/S /hana/data | IOPS /hana/data | Volume /hana/log | Taxa de transferência de E/S /hana/log | IOPS /hana/log |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/s | 200 GB | 400 MBps | 2\.500 | 80 GB | 250 MB | 1.800 |
| E32ds_v4 | 256 GiB | 768 MB/s | 300 GB | 400 MBps | 2\.500 | 128 GB | 250 MBps | 1.800 |
| E48ds_v4 | 384 GiB | 1152 MB/s | 460 GB | 400 MBps | 3\.000 | 192 GB | 250 MBps | 1.800 |
| E64ds_v4 | 504 GiB | 1200 MB/s | 610 GB | 400 MBps | 3\.500 |  256 GB | 250 MBps | 1.800 |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 610 GB | 400 MBps | 3\.500 | 220 GB | 250 MB | 1.800 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 2\.500 | 96 GB | 250 MBps  | 1.800 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 2\.500 | 256 GB | 250 MBps  | 1.800 |
| M64ls | 512 GiB | 1\.000 MB/s | 620 GB | 400 MBps | 3\.500 | 256 GB | 250 MBps  | 1.800 |
| M64s | 1\.000 GiB | 1\.000 MB/s |  1\.200 GB | 600 MBps | 5\.000 | 512 GB | 250 MBps  | 2\.500 |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 2\.100 GB | 600 MBps | 5\.000 | 512 GB | 250 MBps  | 2\.500 |
| M128s | 2\.000 GiB | 2\.000 MB/s |2\.400 GB | 750 MBps | 7.000 | 512 GB | 250 MBps  | 2\.500 | 
| M128ms | 3\.800 GiB | 2\.000 MB/s | 4\.800 GB | 750 MBps |9.600 | 512 GB | 250 MBps  | 2\.500 | 
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 3\.500 GB | 750 MBps | 7.000 | 512 GB | 250 MBps  | 2\.500 | 
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 7\.200 GB | 750 MBps | 14.400 | 512 GB | 250 MBps  | 2\.500 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 7\.200 GB | 1\.000 MBps | 14.400 | 512 GB | 400 MBps  | 4.000 | 
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 14.400 GB | 1\.500 MBps | 28.800 | 512 GB | 400 MBps  | 4.000 |   

**Os valores listados se destinam a ser um ponto de partida e precisam ser avaliados em relação às demandas reais.** A vantagem com o Disco Ultra do Azure é que os valores de IOPS e taxa de transferência podem ser adaptados sem a necessidade de desligar a VM ou de interromper a carga de trabalho aplicada ao sistema.   

> [!NOTE]
> Até agora, os instantâneos de armazenamento com o armazenamento de disco Ultra não estão disponíveis. Isso bloqueia o uso de instantâneos de VM com os serviços de Backup do Azure


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Volumes NFS v4.1 em Azure NetApp Files
Para obter detalhes sobre o seja para HANA, leia os volumes do documento [NFS v 4.1 em Azure NetApp Files para SAP Hana](./hana-vm-operations-netapp.md)




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Solução consciente de custo com o armazenamento Premium do Azure
Até agora, a solução de armazenamento Premium do Azure descrita neste documento na seção [soluções com armazenamento Premium e acelerador de gravação do Azure para máquinas virtuais da série M do Azure](#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) foi destinada para SAP Hana cenários com suporte de produção. Uma das características das configurações com suporte de produção é a separação dos volumes para SAP HANA dados e o log de restauração em dois volumes diferentes. A razão para essa separação é que as características de carga de trabalho nos volumes são diferentes. E isso com as configurações de produção sugeridas, um tipo diferente de cache ou até mesmo tipos diferentes de armazenamento de bloco do Azure poderia ser necessário. Para cenários de não produção, algumas das considerações tomadas para sistemas de produção podem não se aplicar a sistemas de não produção mais baixos. Como resultado, o volume de dados e de log do HANA pode ser combinado. Embora, eventualmente, com alguns culpados, como eventualmente não atender a determinados KPIs de taxa de transferência ou latência que são necessários para sistemas de produção. Outro aspecto para reduzir os custos em tais ambientes pode ser o uso do [armazenamento de SSD standard do Azure](./planning-guide-storage.md#azure-standard-ssd-storage). Tenha em mente que a escolha de SSD Standard ou HDD Standard o armazenamento do Azure tenha impacto sobre seus SLAs de VM única, conforme documentado no artigo  [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Uma alternativa menos dispendiosa para essas configurações poderia ser semelhante a:


| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | hana/data e hana/log<br /> distribuídos com LVM ou MDADM | /hana/shared | /root volume | /usr/sap | comments |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | Não alcançará menos de 1 MS latência de armazenamento<sup>1</sup> |
| E16v3 | 128 GiB | 384 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | Tipo de VM não certificado pelo HANA <br /> Não alcançará menos de 1 MS latência de armazenamento<sup>1</sup> |
| M32ts | 192 GiB | 500 MB/s | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | O uso de Acelerador de Gravação para dados combinados e volume de log limitará a taxa de IOPS a 5.000<sup>2</sup> |
| E20ds_v4 | 160 GiB | 480 MB/s | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | Não alcançará menos de 1 MS latência de armazenamento<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Tipo de VM não certificado pelo HANA <br /> Não alcançará menos de 1 MS latência de armazenamento<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 MBps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Não alcançará menos de 1 MS latência de armazenamento<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | O uso de Acelerador de Gravação para dados combinados e volume de log limitará a taxa de IOPS a 5.000<sup>2</sup> |
| E48ds_v4 | 384 GiB | 1.152 MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Não alcançará menos de 1 MS latência de armazenamento<sup>1</sup> |
| E64v3 | 432 GiB | 1\.200 MB/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Não alcançará menos de 1 MS latência de armazenamento<sup>1</sup> |
| E64ds_v4 | 504 GiB | 1200 MB/s |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Não alcançará menos de 1 MS latência de armazenamento<sup>1</sup> |
| M64ls | 512 GiB | 1\.000 MB/s | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | O uso de Acelerador de Gravação para dados combinados e volume de log limitará a taxa de IOPS a 10.000<sup>2</sup> |
| M64s | 1\.000 GiB | 1\.000 MB/s | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | O uso de Acelerador de Gravação para dados combinados e volume de log limitará a taxa de IOPS a 10.000<sup>2</sup> |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | O uso de Acelerador de Gravação para dados combinados e volume de log limitará a taxa de IOPS a 10.000<sup>2</sup> |
| M128s | 2\.000 GiB | 2\.000 MB/s |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | O uso de Acelerador de Gravação para dados combinados e volume de log limitará a taxa de IOPS a 20.000<sup>2</sup> |
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | O uso de Acelerador de Gravação para dados combinados e volume de log limitará a taxa de IOPS a 10.000<sup>2</sup> |
| M128ms | 3\.800 GiB | 2\.000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | O uso de Acelerador de Gravação para dados combinados e volume de log limitará a taxa de IOPS a 20.000<sup>2</sup> |
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | O uso de Acelerador de Gravação para dados combinados e volume de log limitará a taxa de IOPS a 10.000<sup>2</sup> |
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | O uso de Acelerador de Gravação para dados combinados e volume de log limitará a taxa de IOPS a 20.000<sup>2</sup> |
| M416ms_v2 | 11400 GiB | 2\.000 MB/s | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | O uso de Acelerador de Gravação para dados combinados e volume de log limitará a taxa de IOPS a 20.000<sup>2</sup> |


<sup>1</sup> o [Azure acelerador de gravação](../../how-to-enable-write-accelerator.md) não pode ser usado com as famílias de VMs Ev4 e Ev4. Como resultado do uso do armazenamento Premium do Azure, a latência de e/s não será menor que 1 MS

<sup>2</sup> a família de VMs dá suporte ao [Azure acelerador de gravação](../../how-to-enable-write-accelerator.md), mas há um potencial de que o limite de IOPS do acelerador de gravação pode limitar as configurações de disco recursos de IOPS

No caso de combinar os dados e o volume de log para SAP HANA, os discos que criam o volume distribuído não devem ter cache de leitura ou cache de leitura/gravação habilitado.

Há tipos de VM listados que não são certificados com o SAP e, como tal, não são listados no, portanto, chamados de [SAP Hana diretório de hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Os comentários dos clientes eram que esses tipos de VM não listados foram usados com êxito para algumas tarefas que não são de produção.


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte:

- [Guia de alta disponibilidade do SAP HANA para máquinas virtuais do Azure](./sap-hana-availability-overview.md).