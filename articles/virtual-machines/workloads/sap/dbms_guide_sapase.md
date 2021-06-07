---
title: Implantação do DBMS de Máquinas de Virtuais do ASE SAP do Azure para carga de trabalho do SAP | Microsoft Docs
description: Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 430af1dfcbd9c7faabfca2f3f5b80b3b3106a889
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675798"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP

Neste documento, aborda várias áreas diferentes a serem consideradas ao implantar o ASE SAP no IaaS do Azure. Como uma pré-condição para este documento, você deve ler o documento [Considerações para implantação de DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md), e outros guias de [carga de trabalho do SAP na documentação do Azure](./get-started.md). Este documento aborda o SAP ASE em execução no Linux e em sistemas operacionais Windows. A versão mínima com suporte no Azure é o SAP ASE 16.0.02 (versão 16 do pacote de suporte 2). É recomendável implantar a versão mais recente do SAP e o nível de patch mais recente.  Como é recomendado um mínimo de 16.0.03.07 do SAP ASE (versão 16 do pacote de suporte 3, nível de patch 7).  A versão mais recente do SAP pode ser encontrada nas [informações de cronograma de versão do ASE 16,0 de destino e da lista de CR](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

Informações adicionais sobre o suporte de versão com aplicativos SAP ou local de mídia de instalação são encontradas, além da matriz de disponibilidade de produto SAP nestes locais:

- [#2134316 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2134316)
- [#1941500 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1941500)
- [#1590719 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1590719)
- [#1973241 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1973241)

Comentário: em toda a documentação dentro e fora do SAP World, o nome do produto é referenciado como Sybase ASE ou SAP ASE ou, em alguns casos, ambos. Para manter a consistência, usamos o nome **SAP ase** nesta documentação.

## <a name="operating-system-support"></a>Suporte do sistema operacional
A matriz de disponibilidade de produto SAP contém as combinações de sistema operacional e kernel do SAP com suporte para cada aplicativo SAP.  As distribuições do Linux SUSE 12. x, SUSE 15. x, Red Hat 7. x têm suporte total.  Não há suporte para Oracle Linux como sistema operacional para o SAP ASE.  É recomendável usar as versões mais recentes do Linux disponíveis. Os clientes do Windows devem usar as versões do Windows Server 2016 ou do Windows Server 2019.  Versões mais antigas do Windows, como o Windows 2012, são tecnicamente suportadas, mas a versão mais recente do Windows é sempre recomendada.


## <a name="specifics-to-sap-ase-on-windows"></a>Informações específicas para o SAP ASE no Windows
Começando com o Microsoft Azure, você pode migrar seus aplicativos SAP ASE existentes para máquinas virtuais do Azure. O SAP ASE em uma Máquina Virtual do Azure permite reduzir o custo total de propriedade de implantação, gerenciamento e manutenção de aplicativos de amplitude empresarial migrando facilmente esses aplicativos para o Microsoft Azure. Com o SAP ASE em uma máquina virtual do Azure, os administradores e desenvolvedores ainda podem usar as mesmas ferramentas de desenvolvimento e administração que estão disponíveis localmente.

O Microsoft Azure oferece vários tipos de máquinas virtuais diferentes que permitem que você execute menores sistemas SAP e paisagens até grandes sistemas SAP e paisagens com milhares de usuários. Os números de dimensionamento SAP de SAPS dos diferentes SKUs de VM certificadas pelo SAP são fornecidos na [Nota de suporte sap #1928533](https://launchpad.support.sap.com/#/notes/1928533).

A documentação para instalar o SAP ASE no Windows pode ser encontrada no [Guia de instalação do SAP ase para Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

Bloquear páginas na memória é uma configuração que impedirá que o buffer de banco de dados do SAP ASE seja paginado.  Essa configuração é útil para sistemas grandes ocupados com muita memória. Contate BC-DB-SYB para obter mais informações. 


## <a name="linux-operating-system-specific-settings"></a>Configurações específicas do sistema operacional Linux
Em VMs do Linux, execute `saptune` com o perfil SAP – as páginas enormes do Linux do ase devem ser habilitadas por padrão e podem ser verificadas com o comando  

`cat /proc/meminfo` 

Normalmente, o tamanho da página é de 2048 KB. Para obter detalhes, consulte o artigo [páginas enormes no Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Recomendações sobre a estrutura de VM e de disco para implantações do SAP ASE

O SAP ASE para aplicativos SAP NetWeaver tem suporte em qualquer tipo de VM listado na [Nota de suporte sap #1928533](https://launchpad.support.sap.com/#/notes/1928533) tipos típicos de VM usados para servidores de banco de dados SAP ase de médio porte incluem Esv3.  Grandes bancos de dados de vários terabytes podem aproveitar os tipos de VM da série M. O desempenho de gravação do disco do log de transações do SAP ASE pode ser melhorado habilitando a Acelerador de Gravação da série M. Acelerador de Gravação deve ser testado com cuidado com o SAP ASE devido à forma como o SAP ASE executa gravações de log.  Examine a [Nota de suporte SAP #2816580](../../how-to-enable-write-accelerator.md) e considere a execução de um teste de desempenho.  
Acelerador de Gravação é projetado apenas para o disco de log de transações. O cache de nível de disco deve ser definido como nenhum. Não se surpreenda se o Azure Acelerador de Gravação não mostrar melhorias semelhantes às de outros DBMS. Com base no modo como o SAP ASE grava no log de transações, pode ser que haja pouca ou nenhuma aceleração pelo Azure Acelerador de Gravação.
Discos separados são recomendados para dispositivos de dados e dispositivos de log.  Os bancos de dados do sistema sybsecuritym e `saptools` não exigem discos dedicados e podem ser colocados nos discos que contêm os dispositivos de log e de data do SAP 

![Configuração de armazenamento para SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Sistemas de arquivos, tamanho de distribuição & balanceamento de e/s 
O SAP ASE grava dados em sequência em dispositivos de armazenamento em disco, a menos que seja configurado de outra forma. Isso significa que um banco de dados do SAP ASE vazio com quatro dispositivos gravará dados no primeiro dispositivo.  Os outros dispositivos de disco só serão gravados quando o primeiro dispositivo estiver cheio.  A quantidade de e/s de leitura e gravação para cada dispositivo do SAP ASE provavelmente será diferente. Para balancear a e/s de disco em todos os discos do Azure disponíveis, os espaços de armazenamento do Windows ou o LVM2 do Linux precisam ser usados. No Linux, é recomendável usar o sistema de arquivos XFS para formatar os discos. O tamanho de distribuição LVM deve ser testado com um teste de desempenho. o tamanho de distribuição de 128 KB é um bom ponto de partida. No Windows, o tamanho da unidade de alocação NTFS (AUS) deve ser testado. 64 KB podem ser usados como um valor inicial. 

É recomendável configurar a expansão automática de banco de dados, conforme descrito no artigo [Configurando a expansão automática de espaço de banco de dados no SAP Adaptive Server Enterprise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/)  e [sap support Note #1815695](https://launchpad.support.sap.com/#/notes/1815695). 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Exemplo do SAP ASE em máquinas virtuais do Azure, disco e configurações do sistema de arquivos 
Os modelos a seguir mostram as configurações de exemplo para Linux e Windows. Antes de confirmar a máquina virtual e a configuração de disco, verifique se as cotas de largura de banda de rede e de armazenamento da VM individual são suficientes para atender ao requisito de negócios. Também tenha em mente que diferentes tipos de VM do Azure têm números máximos de discos diferentes que podem ser anexados à VM. Por exemplo, uma VM E4s_v3 tem um limite de taxa de transferência de e/s de armazenamento de 48 MB/seg. Se a taxa de transferência de armazenamento exigida pela atividade de backup do banco de dados exigir mais de 48 MB/s, um tipo de VM maior com mais taxa de transferência de largura de banda de armazenamento será inevitável. Ao configurar o armazenamento do Azure, você também precisa ter em mente que, especialmente com o [armazenamento Premium do Azure](../../premium-storage-performance.md) , a taxa de transferência e o IOPS por GB de capacidade mudam. Veja mais sobre este tópico no artigo [quais tipos de disco estão disponíveis no Azure?](../../disks-types.md). As cotas para tipos específicos de VM do Azure são documentadas no artigo [tamanhos de máquina virtual com otimização de memória](../../sizes-memory.md) e artigos vinculados a ele. 

> [!NOTE]
>  Se um sistema DBMS estiver sendo movido do local para o Azure, é recomendável executar o monitoramento na VM e avaliar a CPU, a memória, o IOPS e a taxa de transferência de armazenamento. Compare os valores de pico observados com os limites de cota da VM documentados nos artigos mencionados acima

Os exemplos fornecidos abaixo são para fins ilustrativos e podem ser modificados com base em necessidades individuais. Devido ao design do SAP ASE, o número de dispositivos de dados não é tão crítico quanto com outros bancos. O número de dispositivos de dados detalhados neste documento é apenas um guia. 

Um exemplo de uma configuração para um pequeno servidor de BD do SAP ASE com um tamanho de banco de dados entre 50 GB – 250 GB, como o SAP Solution Manager, poderia ser semelhante a

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo de VM | E4s_v3 (4 vCPU/32 GB de RAM) | E4s_v3 (4 vCPU/32 GB de RAM) | --- |
| Rede Acelerada | Habilitar | Habilitar | ---|
| Versão do SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| n º de dispositivos de dados | 4 | 4 | ---|
| n º de dispositivos de log | 1 | 1 | --- |
| n º de dispositivos temporários | 1 | 1 | mais para carga de trabalho de SAP BW |
| Sistema operacional | Windows Server 2019 | SUSE 12 SP4/15 SP1 ou RHEL 7,6 | --- |
| Agregação de disco | Espaços de Armazenamento | LVM2 | --- |
| Sistema de arquivos | NTFS | XFS |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| n º e tipo de discos de dados | Armazenamento Premium: 2 x P10 (RAID0) | Armazenamento Premium: 2 x P10 (RAID0)| Cache = somente leitura |
| # e tipo de discos de log | Armazenamento Premium: 1 x P20  | Armazenamento Premium: 1 x P20 | Cache = nenhum |
| Parâmetro MaxMemory do ASE | 90% da RAM física | 90% da RAM física | Presumindo instância única |
| n º de dispositivos de backup | 4 | 4| --- |
| # e tipo de discos de backup | 1 | 1 | --- |


Um exemplo de uma configuração para um servidor de BD SAP ASE médio com um tamanho de banco de dados entre 250 GB – 750 GB, como um sistema SAP Business Suite menor, poderia ser semelhante a

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo de VM | E16s_v3 (16 vCPU/128 GB de RAM) | E16s_v3 (16 vCPU/128 GB de RAM) | --- |
| Rede Acelerada | Habilitar | Habilitar | ---|
| Versão do SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| n º de dispositivos de dados | 8 | 8 | ---|
| n º de dispositivos de log | 1 | 1 | --- |
| n º de dispositivos temporários | 1 | 1 | mais para carga de trabalho de SAP BW |
| Sistema operacional | Windows Server 2019 | SUSE 12 SP4/15 SP1 ou RHEL 7,6 | --- |
| Agregação de disco | Espaços de Armazenamento | LVM2 | --- |
| Sistema de arquivos | NTFS | XFS |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| n º e tipo de discos de dados | Armazenamento Premium: 4 x P20 (RAID0) | Armazenamento Premium: 4 x P20 (RAID0)| Cache = somente leitura |
| # e tipo de discos de log | Armazenamento Premium: 1 x P20  | Armazenamento Premium: 1 x P20 | Cache = nenhum |
| Parâmetro MaxMemory do ASE | 90% da RAM física | 90% da RAM física | Presumindo instância única |
| n º de dispositivos de backup | 4 | 4| --- |
| # e tipo de discos de backup | 1 | 1 | --- |

Um exemplo de uma configuração para um pequeno servidor de BD do SAP ASE com um tamanho de banco de dados entre 750 GB – 2000 GB, como um sistema SAP Business Suite maior, poderia ser semelhante a

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo de VM | E64s_v3 (64 vCPU/432 GB de RAM) | E64s_v3 (64 vCPU/432 GB de RAM) | --- |
| Rede Acelerada | Habilitar | Habilitar | ---|
| Versão do SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| n º de dispositivos de dados | 16 | 16 | ---|
| n º de dispositivos de log | 1 | 1 | --- |
| n º de dispositivos temporários | 1 | 1 | mais para carga de trabalho de SAP BW |
| Sistema operacional | Windows Server 2019 | SUSE 12 SP4/15 SP1 ou RHEL 7,6 | --- |
| Agregação de disco | Espaços de Armazenamento | LVM2 | --- |
| Sistema de arquivos | NTFS | XFS |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| n º e tipo de discos de dados | Armazenamento Premium: 4 x p30 (RAID0) | Armazenamento Premium: 4 x p30 (RAID0)| Cache = somente leitura |
| # e tipo de discos de log | Armazenamento Premium: 1 x P20  | Armazenamento Premium: 1 x P20 | Cache = nenhum |
| Parâmetro MaxMemory do ASE | 90% da RAM física | 90% da RAM física | Presumindo instância única |
| n º de dispositivos de backup | 4 | 4| --- |
| # e tipo de discos de backup | 1 | 1 | --- |


Um exemplo de uma configuração para um pequeno servidor de BD do SAP ASE com um tamanho de banco de dados de 2 TB +, como um sistema SAP Business Suite maior usado globalmente, poderia ser semelhante a

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo de VM | Série M (1,0 a 4,0 TB de RAM)  | Série M (1,0 a 4,0 TB de RAM) | --- |
| Rede Acelerada | Habilitar | Habilitar | ---|
| Versão do SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| n º de dispositivos de dados | 32 | 32 | ---|
| n º de dispositivos de log | 1 | 1 | --- |
| n º de dispositivos temporários | 1 | 1 | mais para carga de trabalho de SAP BW |
| Sistema operacional | Windows Server 2019 | SUSE 12 SP4/15 SP1 ou RHEL 7,6 | --- |
| Agregação de disco | Espaços de Armazenamento | LVM2 | --- |
| Sistema de arquivos | NTFS | XFS |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| n º e tipo de discos de dados | Armazenamento Premium: 4 + x p30 (RAID0) | Armazenamento Premium: 4 + x p30 (RAID0)| Cache = somente leitura, considere o Azure ultra Disk |
| # e tipo de discos de log | Armazenamento Premium: 1 x P20  | Armazenamento Premium: 1 x P20 | Cache = nenhum, considere o ultra Disk do Azure |
| Parâmetro MaxMemory do ASE | 90% da RAM física | 90% da RAM física | Presumindo instância única |
| n º de dispositivos de backup | 16 | 16 | --- |
| # e tipo de discos de backup | 4 | 4 | Usar o LVM2/espaços de armazenamento |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Considerações de restauração de & de backup para o SAP ASE no Azure
Aumentar o número de dados e dispositivos de backup aumenta o desempenho de backup e restauração. É recomendável distribuir os discos do Azure que estão hospedando o dispositivo de backup do SAP ASE, conforme mostrado nas tabelas mostradas anteriormente. Deve-se ter cuidado para balancear o número de dispositivos e discos de backup e garantir que a taxa de transferência de backup não deva exceder 40%-50% da cota de produtividade total da VM. É recomendável usar a compactação de backup do SAP como padrão. Mais detalhes podem ser encontrados nos artigos:

- [#1588316 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1588316)
- [#1801984 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1801984)
- [#1585981 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1585981) 

Não usar a unidade D:\ ou/Temp espaço como banco de dados ou destino de despejo de log.

### <a name="impact-of-database-compression"></a>Impacto da compactação do banco de dados
Em configurações nas quais a largura de banda de e/s pode se tornar um fator limitante, as medidas, que reduzem o IOPS podem ajudar a ampliar a carga de trabalho que uma pode executar em um cenário de IaaS como o Azure. Portanto, é recomendável certificar-se de que a compactação do SAP ASE seja usada antes de carregar um banco de dados SAP existente no Azure.

A recomendação para aplicar a compactação antes de carregar no Azure é fornecida por várias razões:

* A quantidade de dados a ser carregada no Azure é menor
* A duração da execução de compactação é menor, presumindo-se que seja possível usar um hardware mais poderoso com mais CPUs ou maior largura de banda de E/S ou menor latência de E/S local
* Tamanhos menores de banco de dados podem levar a menos custos de alocação de disco

A compactação de LOB e dados funciona em uma VM hospedada em máquinas virtuais do Azure como o faz localmente. Para obter mais detalhes sobre como verificar se a compactação já está em uso em um banco de dados do SAP ASE existente, consulte a [Nota de suporte do sap 1750510](https://launchpad.support.sap.com/#/notes/1750510). Para obter mais detalhes sobre a verificação de compactação de banco de dados SAP ASE, [observe #2121797 de observação](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Alta disponibilidade do SAP ASE no Azure 
O guia de usuários do HADR detalha a configuração e a configuração de uma solução de 2 nós do SAP ASE "Always on".  Além disso, também há suporte para um terceiro nó de recuperação de desastre. O SAP ASE dá suporte a muitas configurações de alta disponibilidade, incluindo disco compartilhado e clustering de sistema operacional nativo (IP flutuante). A única configuração com suporte no Azure está usando o Gerenciador de falhas sem IP flutuante.  O método de endereço IP flutuante não funcionará no Azure.  O kernel do SAP é um aplicativo de "reconhecimento de HA" e conhece os servidores SAP ASE primários e secundários. Não há integrações próximas entre o SAP ASE e o Azure, o balanceador de carga interno do Azure não é usado. Portanto, a documentação padrão do SAP ASE deve ser seguida a partir do [Guia de usuários do SAP ase HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> A única configuração com suporte no Azure está usando o Gerenciador de falhas sem IP flutuante.  O método de endereço IP flutuante não funcionará no Azure. 

### <a name="third-node-for-disaster-recovery"></a>Terceiro nó para recuperação de desastres
Além de usar o SAP ASE Always-On para alta disponibilidade local, talvez você queira estender a configuração para um nó replicado assincronamente em outra região do Azure. A documentação para tal cenário pode ser encontrada [aqui](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199).

## <a name="sap-ase-database-encryption--ssl"></a>Criptografia de banco de dados SAP ASE & SSL 
O Gerenciador de provisionamento de software SAP (SWPM) está fornecendo uma opção para criptografar o banco de dados durante a instalação.  Se você quiser usar a criptografia, é recomendável usar a criptografia de banco de dados completo do SAP.  Consulte os detalhes documentados em:

- [#2556658 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2556658)
- [#2224138 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2224138)
- [#2401066 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2401066)
- [#2593925 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Se um banco de dados do SAP ASE for criptografado, a compactação de despejo de backup não funcionará. Consulte também a [Observação de suporte SAP #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Lista de verificação da implantação do SAP ASE no Azure
 
- Implantar o SAP ASE 16.0.03.07 ou superior
- Atualizar para a versão mais recente e patches de Faultmanager e ao saphostagent
- Implantar no sistema operacional certificado mais recente disponível, como o Windows 2019, SuSE 15,1 ou RedHat 7,6 ou superior
- Usar VMs certificadas pelo SAP – SKUs de VM do Azure de alta memória, como Es_v3 ou para SKUs de VM série M de sistemas grandes, são recomendados
- Corresponda a cota de taxa de transferência total de disco e de IOPS da VM com o design do disco.  Implantar um número suficiente de discos
- Agregar discos usando espaços de armazenamento do Windows ou o LVM2 do Linux com o tamanho de distribuição e o sistema de arquivos corretos
- Crie um número suficiente de dispositivos para fins de dados, log, temp e backup
- Considere o uso de UltraDisk para sistemas x-grandes 
- Executar o `saptune` SAP-ase no sistema operacional Linux 
- Proteger o banco de dados com criptografia de BD – armazene manualmente as chaves no Azure Key Vault 
- Concluir a [lista de verificação do SAP no Azure](./sap-deployment-checklist.md) 
- Configurar backup de log e backup completo 
- Teste de HA/DR, backup e restauração e execute estresse & teste de volume 
- Confirmar se a extensão de banco de dados automática está funcionando 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Usando DBACockpit para monitorar instâncias de banco de dados
Para sistemas SAP que estão usando o SAP ASE como plataforma de banco de dados, o DBACockpit está acessível como janelas de navegador inseridas no DBACockpit da transação ou como Webdynpro. No entanto, a funcionalidade completa para monitorar e administrar o banco de dados está disponível apenas na implementação Webdynpro do DBACockpit.

Como com sistemas locais, são necessárias várias etapas para habilitar toda a funcionalidade do SAP NetWeaver usada pela implementação do Webdynpro do DBACockpit. Siga a [Observação de suporte do SAP #1245200](https://launchpad.support.sap.com/#/notes/1245200) para habilitar o uso de webdynpros e gerar os necessários. Ao seguir as instruções nas observações acima, você também configura o Gerenciador de comunicação da Internet ( `ICM` ) junto com as portas a serem usadas para conexões http e HTTPS. A configuração padrão para http tem esta aparência:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

e os links gerados no DBACockpit da transação são semelhantes a este:

> https: \/ / \<fullyqualifiedhostname> : 44300/SAP/BC/Webdynpro/SAP/dba_cockpit
> 
> http: \/ / \<fullyqualifiedhostname> : 8000/SAP/BC/Webdynpro/SAP/dba_cockpit
> 
> 

Dependendo de como a máquina virtual do Azure que hospeda o sistema SAP está conectado ao seu AD e DNS, você precisa se certificar de que o ICM esteja usando um nome de host totalmente qualificado que pode ser resolvido na máquina virtual da qual você está abrindo o DBACockpit. Consulte [#773830 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/773830) para entender como o ICM determina o nome de host totalmente qualificado com base nos parâmetros de perfil e definir o parâmetro ICM/host_name_full explicitamente, se necessário.

Se você implantou a VM em um cenário de Cloud-Only sem conectividade entre locais entre o local e o Azure, você precisa definir um endereço IP público e um `domainlabel` . O formato do nome DNS público da VM tem esta aparência:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Mais detalhes relacionados ao nome DNS podem ser encontrados [aqui] [Virtual-Machines-azurerm-vs-azuresm].

Definindo o parâmetro de perfil do SAP icm/host_name_full para o nome DNS da VM do Azure, o link pode ser semelhante a:

> https: \/ /mydomainlabel.westeurope.cloudapp.net:44300/SAP/BC/Webdynpro/SAP/dba_cockpit
> 
> http: \/ /mydomainlabel.westeurope.cloudapp.net:8000/SAP/BC/Webdynpro/SAP/dba_cockpit

Nesse caso, você precisa se certificar de:

* Adicione regras de entrada ao Grupo de Segurança de Rede no portal do Azure para as portas TCP/IP usadas para comunicação com o ICM
* Adicionar regras de entrada para a configuração do Firewall do Windows para as portas TCP/IP usadas para se comunicar com o ICM

Para um processo importado automatizado de todas as correções disponíveis, recomenda-se aplicar periodicamente a Nota SAP da coleção de correções aplicável à sua versão do SAP:

* [#1558958 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1558958)
* [#1619967 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1619967)
* [#1882376 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1882376)

Mais informações sobre o DBA Cockpit para SAP ASE podem ser encontradas nas seguintes Notas SAP:

* [#1605680 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1605680)
* [#1757924 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1757924)
* [#1757928 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1757928)
* [#1758182 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1758182)
* [#1758496 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1758496)    
* [#1814258 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1814258)
* [#1922555 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1922555)
* [#1956005 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Links úteis, observações & White papers para o SAP ASE
A página inicial da [documentação do SAP ase 16.0.03.07](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) fornece links para vários documentos dos quais os documentos:

- Jornada de aprendizagem do SAP ASE – monitoramento de & de administração
- Jornada de aprendizagem do SAP ASE-instalação & atualização

são úteis. Outro documento útil são [os aplicativos SAP em práticas recomendadas do SAP Adaptive Server Enterprise para migração e tempo de execução](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

Outras notas de suporte SAP úteis são:

- [#2134316 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2134316) 
- [#1748888 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1748888) 
- [#2588660 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2588660) 
- [#1680803 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1680803) 
- [#1724091 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1724091) 
- [#1775764 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1775764) 
- [#2162183 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2162183) 
- [#1928533 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1928533)
- [#2015553 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2015553)
- [#1750510 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1750510) 
- [#1752266 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1752266) 
- [#2162183 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2162183) 
- [#1588316 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/158831) 

Outras informações são publicadas em 

- [Aplicativos SAP no SAP Adaptive Server Enterprise](https://community.sap.com/topics/applications-on-ase)
- [InfoCenter do SAP ASE](http://infocenter.sybase.com/help/index.jsp) 
- [SAP ASE Always on com a configuração do terceiro nó de DR](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

Um boletim informativo mensal é publicado por meio da [Observação de suporte do SAP #2381575](https://launchpad.support.sap.com/#/notes/2381575) 


## <a name="next-steps"></a>Próximas etapas
Verifique o artigo [cargas de trabalho do SAP no Azure: lista de verificação de planejamento e implantação](./sap-deployment-checklist.md)