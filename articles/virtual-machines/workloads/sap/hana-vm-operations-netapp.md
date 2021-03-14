---
title: SAP HANA a configuração de seja de máquina virtual do Azure | Microsoft Docs
description: Azure NetApp Files recomendações de armazenamento para SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, seja, HANA, Azure NetApp Files, instantâneo
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/23/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de8af71928ad6a83d4930e4e6e0b8dd257148111
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666609"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>Volumes NFS v4.1 no Azure NetApp Files para SAP HANA

O Azure NetApp Files fornece compartilhamentos NFS nativos que podem ser usados para volumes **/Hana/Shared**, **/Hana/data** e **/Hana/log** . O uso de compartilhamentos NFS baseados em seja para os volumes **/Hana/data** e **/Hana/log** requer o uso do protocolo v 4.1 NFS. Não há suporte para o protocolo de NFS v3 para o uso de volumes **/Hana/data** e **/Hana/log** ao basear os compartilhamentos em seja. 


> [!IMPORTANT]
> O protocolo NFS v3 implementado em Azure NetApp Files **não** tem suporte para ser usado para **/Hana/data** e **/Hana/log**. O uso do NFS 4,1 é obrigatório para volumes **/Hana/data** e **/Hana/log** de um ponto de vista funcional. Enquanto para o volume **/Hana/Shared** , o NFS v3 ou o protocolo NFS v 4.1 podem ser usados a partir de um ponto de vista funcional.

## <a name="important-considerations"></a>Considerações importantes

Ao considerar Azure NetApp Files para o SAP Netweaver e o SAP HANA, esteja ciente das seguintes considerações importantes:

- O pool de capacidade mínima é 4 TiB  
- O tamanho mínimo do volume é 100 GiB
- Azure NetApp Files e todas as máquinas virtuais, em que Azure NetApp Files volumes são montados, devem estar na mesma rede virtual do Azure ou em [redes virtuais emparelhadas](../../../virtual-network/virtual-network-peering-overview.md) na mesma região
- É importante que as máquinas virtuais sejam implantadas bem próximas ao armazenamento do Azure NetApp para ter baixa latência.  
- A rede virtual selecionada deve ter uma sub-rede, delegada para Azure NetApp Files
- Verifique se a latência do servidor de banco de dados para o volume seja é medida e abaixo de 1 milissegundo
- A taxa de transferência de um volume do Azure NetApp é uma função da cota de volume e do nível de serviço, conforme documentado no [Nível de serviço para Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Ao dimensionar os volumes da NetApp do Azure do HANA, verifique se a taxa de transferência resultante atende aos requisitos do sistema do HANA
- Tente "consolidar" volumes para obter mais desempenho em um volume maior, por exemplo, use um volume para/sapmnt,/usr/SAP/trans,... se possível  
- Azure NetApp Files oferece a [política de exportação](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): você pode controlar os clientes permitidos e o tipo de acesso (leitura e gravação, somente leitura etc.). 
- O recurso do Azure NetApp Files ainda não tem reconhecimento de zona. No momento, o recurso do Azure NetApp Files não está implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja atendo às possíveis implicações de latência em algumas regiões do Azure.   
- A ID de Usuário para <b>sid</b>adm e a ID de Grupo para `sapsys` nas máquinas virtuais devem corresponder com a configuração no Azure NetApp Files. 

> [!IMPORTANT]
> Para cargas de trabalho do SAP HANA, baixa latência é fundamental. Trabalhe com seu representante da Microsoft para verificar se as máquinas virtuais e os volumes do Azure NetApp Files foram implantados bem próximos.  

> [!IMPORTANT]
> Se houver uma incompatibilidade entre a ID de usuário para <b>Sid</b>ADM e a ID de grupo para `sapsys` entre a máquina virtual e a configuração do Azure NetApp, as permissões para arquivos nos volumes do Azure NetApp, montadas na VM, seriam exibidas como `nobody` . Especifique a ID de Usuário correta para <b>sid</b>adm e a ID do Grupo para `sapsys`, ao [integrar um novo sistema](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) ao Azure NetApp Files.


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionar o banco de dados do HANA no Azure NetApp Files

A taxa de transferência de um volume do Azure NetApp é uma função do tamanho de volume e do nível de serviço, conforme documentado no [Nível de serviço para Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Importante entender é a relação de desempenho do tamanho e se há limites físicos para um LIF (interface lógica) do SVM (máquina virtual de armazenamento).

A tabela a seguir demonstra que pode fazer sentido criar um volume "padrão" grande para armazenar backups e que não faz sentido criar um volume "ultra" maior que 12 TB porque a capacidade de largura de banda física de um único LIF seria excedida. 

A taxa de transferência máxima para um LIF e uma única sessão do Linux é entre 1,2 e 1,4 GB/s. Se precisar de mais taxa de transferência para/Hana/data, você poderá usar SAP HANA particionamento de volume de dados para distribuir a atividade de e/s durante o recarregamento de dados ou os pontos de salvamento do HANA em vários arquivos de dados do HANA localizados em vários compartilhamentos NFS. Para obter mais detalhes sobre a distribuição de volume de dados do HANA, leia estes artigos:

- [O guia do administrador do HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog sobre SAP HANA – Particionando volumes de dados](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [Observação SAP #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [Observação SAP #2700123](https://launchpad.support.sap.com/#/notes/2700123)


| Tamanho  | Taxa de transferência padrão | Taxa de transferência Premium | Taxa de transferência Ultra |
| --- | --- | --- | --- |
| 1 TB | 16 MB/s | 64 MB/s | 128 MB/s |
| 2 TB | 32 MB/s | 128 MB/s | 256 MB/s |
| 4 TB | 64 MB/s | 256 MB/s | 512 MB/s |
| 10 TB | 160 MB/s | 640 MB/s | 1.280 MB/s |
| 15 TB | 240 MB/s | 960 MB/s | 1.400 MB/s |
| 20 TB | 320 MB/s | 1.280 MB/s | 1.400 MB/s |
| 40 TB | 640 MB/s | 1.400 MB/s | 1.400 MB/s |

É importante entender que os dados são gravados no mesmo SSDs no back-end de armazenamento. A cota de desempenho do pool de capacidade foi criada para poder gerenciar o ambiente.
Os KPIs de armazenamento são iguais para todos os tamanhos de banco de dados do HANA. Em quase todos os casos, essa suposição não reflete a realidade e a expectativa do cliente. O tamanho dos sistemas HANA não significa necessariamente que um pequeno sistema exige baixa taxa de transferência de armazenamento – e um sistema grande requer alta taxa de transferência de armazenamento. Mas, em geral, podemos esperar mais requisitos de taxa de transferência para instâncias de banco de dados HANA maiores. Como resultado das regras de dimensionamento do SAP para o hardware subjacente, essas instâncias mais grandes do HANA também fornecem mais recursos de CPU e maior paralelismo em tarefas como carregar dados após uma reinicialização de instâncias. Como resultado, os tamanhos de volume devem ser adotados para as expectativas e os requisitos do cliente. E não só é orientado por requisitos de capacidade puras.

Ao projetar a infraestrutura para o SAP no Azure, você deve estar ciente de alguns requisitos mínimos de taxa de transferência de armazenamento (para sistemas de produção) pela SAP, que se convertem em características de taxa de transferência mínima do:

| Tipo de volume e tipo de e/s | KPI mínimo exigido pelo SAP | Nível de serviço Premium | Nível de ultra serviço |
| --- | --- | --- | --- |
| Gravação de volume de log | 250 MB/s | 4 TB | 2 TB |
| Gravação do volume de dados | 250 MB/s | 4 TB | 2 TB |
| Leitura de volume de dados | 400 MB/s | 6,3 TB | 3,2 TB |

Como todos os três KPIs são solicitados, o volume **/Hana/data** precisa ser dimensionado em direção à capacidade maior para atender aos requisitos mínimos de leitura.

Para sistemas HANA, que não exigem alta largura de banda, os tamanhos de volume seja podem ser menores. E, no caso de um sistema HANA exigir mais taxa de transferência, o volume pode ser adaptado redimensionando a capacidade online. Nenhum KPI é definido para volumes de backup. No entanto, a taxa de transferência do volume de backup é essencial para um ambiente com bom desempenho. O log – e o desempenho do volume de dados devem ser projetados para as expectativas do cliente.

> [!IMPORTANT]
> Independentemente da capacidade que você implanta em um único volume NFS, espera-se que a taxa de transferência fique estável no intervalo de largura de banda de 1,2 a 1,4 GB/s utilizada por um consumidor em uma máquina virtual. Isso tem a ver com a arquitetura subjacente da oferta do ANF e com os limites de sessão do Linux relacionados em relação ao NFS. Os números de desempenho e taxa de transferência, conforme documentado no artigo [Resultados do teste de benchmark de desempenho para Azure NetApp Files](../../../azure-netapp-files/performance-benchmarks-linux.md) foram realizados em um volume NFS compartilhado com várias VMs de cliente e como resultado com várias sessões. Esse cenário é diferente para o cenário que medimos no SAP. Nele, medimos a taxa de transferência de uma única VM com relação a um volume NFS. Hospedado em seja.

Para atender aos requisitos de taxa de transferência mínima do SAP para dados e log, e de acordo com as diretrizes para **/Hana/Shared**, os tamanhos recomendados seriam semelhantes a:

| Volume | Tamanho<br /> Camada de armazenamento Premium | Tamanho<br /> Camada de armazenamento Ultra | Protocolo do NFS com suporte |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v4.1 |
| expansão/Hana/Shared | Mín. (1 TB, 1 x RAM)  | Mín. (1 TB, 1 x RAM) | v3 ou v4.1 |
| expansão de/Hana/Shared | 1 x RAM do nó de trabalho<br /> por 4 nós de trabalho  | 1 x RAM do nó de trabalho<br /> por 4 nós de trabalho  | v3 ou v4.1 |
| /Hana/logbackup | 3 x RAM  | 3 x RAM | v3 ou v4.1 |
| /hana/backup | 2 x RAM  | 2 x RAM | v3 ou v4.1 |

Para todos os volumes, o NFS v 4.1 é altamente recomendado

Os tamanhos dos volumes de backup são estimativas. Requisitos exatos precisam ser definidos com base em processos de carga de trabalho e operação. Para backups, você pode consolidar muitos volumes de diferentes SAP HANA instâncias para um (ou dois) volumes maiores, o que poderia ter um nível de serviço inferior de seja.

> [!NOTE]
> O Azure NetApp Files, as recomendações de dimensionamento declaradas neste documento estão visando os requisitos mínimos do SAP expressos em direção aos seus provedores de infraestrutura. Em implantações de clientes e cenários de carga de trabalho reais, isso pode não ser suficiente. Use essas recomendações como um ponto de partida e adapte-se, com base nos requisitos de sua carga de trabalho específica.  

Portanto, você pode considerar implantar uma taxa de transferência semelhante para os volumes do ANF conforme já listado para o armazenamento de disco Ultra. Além disso, considere os tamanhos listados para os volumes dos diferentes SKUs de VM, como já foi feito nas tabelas do disco Ultra.

> [!TIP]
> Você pode redimensionar os volumes do Azure NetApp Files dinamicamente, sem a necessidade de `unmount` os volumes, parar as máquinas virtuais ou parar o SAP HANA. Isso oferece flexibilidade para você atender às demandas de taxa de transferência tanto esperadas quanto imprevistas do seu aplicativo.

A documentação sobre como implantar uma configuração de expansão do SAP HANA com o nó em espera usando volumes NFS v4.1 hospedados no ANF foi publicada na [Expansão do SAP HANA com o nó em espera em VMs do Azure com o Azure NetApp Files no SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md).


## <a name="availability"></a>Disponibilidade
Atualizações do sistema seja são aplicadas sem afetar o ambiente do cliente. O [SLA definido é de 99,99%](https://azure.microsoft.com/support/legal/sla/netapp/).


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>Volumes e endereços IP e pools de capacidade
Com o seja, é importante entender como a infraestrutura subjacente é criada. Um pool de capacidade é apenas uma estrutura, o que torna mais simples criar um modelo de cobrança para seja. Um pool de capacidade não tem nenhuma relação física com a infraestrutura subjacente. Se você criar um pool de capacidade, apenas um shell será criado, o que poderá ser cobrado, e não mais. Quando você cria um volume, o primeiro SVM (máquina virtual de armazenamento) é criado em um cluster de vários sistemas NetApp. Um único IP é criado para este SVM acessar o volume. Se você criar vários volumes, todos os volumes serão distribuídos neste SVM por esse cluster NetApp multicontrolador. Mesmo que você obtenha apenas um IP, os dados serão distribuídos em vários controladores. O seja tem uma lógica que distribui automaticamente cargas de trabalho do cliente quando os volumes ou/e a capacidade do armazenamento configurado atingem um nível predefinido interno. Você pode observar esses casos, pois um novo endereço IP é atribuído para acessar os volumes.

##<a name="log-volume-and-log-backup-volume"></a>Volume de log de volume e backup de log
O "volume de log" (**/Hana/log**) é usado para gravar o log de refazer online. Portanto, há arquivos abertos localizados neste volume e não faz sentido fazer o instantâneo desse volume. Os arquivos de log redo online são arquivados ou copiados em backup para o volume de backup de log quando o arquivo de log de restauração online está cheio ou um backup de log de restauração é executado. Para fornecer um desempenho de backup razoável, o volume de backup de log requer uma boa taxa de transferência. Para otimizar os custos de armazenamento, é possível fazer sentido consolidar o volume de backup de log de várias instâncias do HANA. Para que várias instâncias do HANA aproveitem o mesmo volume e gravem seus backups em diretórios diferentes. Usando essa consolidação, você pode obter mais produtividade com o, pois precisa aumentar um pouco o volume. 

O mesmo se aplica ao volume que você usa para gravar backups completos do banco de dados HANA.  
 

## <a name="backup"></a>Backup
Além dos backups de streaming e do serviço de back do Azure fazendo backup SAP HANA bancos de dados, conforme descrito no artigo [Guia de backup para SAP Hana em máquinas virtuais do Azure](./sap-hana-backup-guide.md), Azure NetApp files abre a possibilidade de executar backups de instantâneo baseados em armazenamento. 

O SAP HANA dá suporte a:

- Backups de instantâneo baseados em armazenamento de SAP HANA 1,0 SPS7 em
- Suporte de backup de instantâneo baseado em armazenamento para ambientes de contêiner de vários bancos de dados (MDC) HANA de SAP HANA 2,0 SPS4 em


A criação de backups de instantâneo baseados em armazenamento é um procedimento simples de quatro etapas, 
1. Criando um instantâneo do banco de dados HANA (interno)-uma atividade que você ou as ferramentas precisam executar 
1. SAP HANA grava os dados nos arquivos para criar um estado consistente no armazenamento – o HANA executa essa etapa como resultado da criação de um instantâneo do HANA
1. Crie um instantâneo no volume **/Hana/data** no armazenamento-uma etapa ou ferramentas que precisam ser executadas. Não é necessário executar um instantâneo no volume **/Hana/log**
1. Excluir o instantâneo do banco de dados HANA (interno) e retomar a operação normal-uma etapa ou ferramentas que precisam ser executadas

> [!WARNING]
> A última etapa ou a falha na execução da última etapa tem um impacto grave na demanda de memória do SAP HANA e pode levar a uma interrupção do SAP HANA

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![Backup de instantâneo seja para SAP HANA](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![Backup de instantâneo seja para SAP HANA parte 2](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

Esse procedimento de backup de instantâneo pode ser gerenciado de várias maneiras, usando várias ferramentas. Um exemplo é o script Python "ntaphana_azure. py" disponível no GitHub. [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) esse é o código de exemplo, fornecido "no estado em que se encontra" sem nenhuma manutenção ou suporte.



> [!CAUTION]
> Um instantâneo em si não é um backup protegido, pois está localizado no mesmo armazenamento físico que o volume do qual você acabou de fazer um instantâneo. É obrigatório "proteger" pelo menos um instantâneo por dia para um local diferente. Isso pode ser feito no mesmo ambiente, em uma região remota do Azure ou no armazenamento de BLOBs do Azure.


Para usuários de produtos de backup do CommVault, uma segunda opção é o CommVault IntelliSnap V. 11.21 e posterior. Esta ou mais versões do CommVault oferecem suporte Azure NetApp Files. O artigo [CommVault IntelliSnap 11,21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) fornece mais informações.


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>Fazer backup do instantâneo usando o armazenamento de BLOBs do Azure
Fazer backup no armazenamento de BLOBs do Azure é um método econômico e rápido para salvar backups de instantâneo de armazenamento de banco de dados HANA com base em seja. Para salvar os instantâneos no armazenamento de BLOBs do Azure, a ferramenta azcopy é preferida. Baixe a versão mais recente dessa ferramenta e instale-a, por exemplo, no diretório bin em que o script Python do GitHub está instalado.
Baixe a ferramenta azcopy mais recente:

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

O recurso mais avançado é a opção de sincronização. Se você usar a opção de sincronização, azcopy manterá a origem e o diretório de destino sincronizados. O uso do parâmetro **--delete-Destination** é importante. Sem esse parâmetro, azcopy não está excluindo arquivos no site de destino e a utilização de espaço no lado de destino aumentaria. Crie um contêiner de blob de blocos em sua conta de armazenamento do Azure. Em seguida, crie a chave SAS para o contêiner de BLOB e sincronize a pasta de instantâneo para o contêiner de BLOBs do Azure.

Por exemplo, se um instantâneo diário deve ser sincronizado com o contêiner de blob do Azure para proteger os dados. E apenas um instantâneo deve ser mantido, o comando a seguir pode ser usado.

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>Próximas etapas
Leia o artigo:

- [Alta disponibilidade do SAP HANA para máquinas virtuais do Azure](./sap-hana-availability-overview.md)