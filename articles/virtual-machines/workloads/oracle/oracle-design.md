---
title: Projetar e implementar um banco de dados Oracle no Azure | Microsoft Docs
description: Projete e implemente um banco de dados Oracle no seu ambiente do Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 6e59d0065dfa74979bf3bbc72458bda516e3b641
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669982"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Projete e implemente um banco de dados Oracle no Azure

## <a name="assumptions"></a>Suposições

- Você está planejando a migração de um banco de dados Oracle do local para o Azure.
- Você tem o [pacote de diagnóstico](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) ou o [repositório de carga de trabalho automático](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) para o Oracle Database que você está procurando migrar
- Você tem uma compreensão das várias métricas no Oracle.
- Você tem uma compreensão das linha de base do desempenho de aplicativo e da utilização da plataforma.

## <a name="goals"></a>Metas

- Compreender como otimizar sua implantação do Oracle no Azure.
- Explorar as opções de ajuste de desempenho para um banco de dados Oracle em um ambiente do Azure.
- Tenha expectativas claras entre os limites de ajuste físico por meio da arquitetura e das vantagens ou do ajuste lógico do código do banco de dados, (SQL) e do design geral do banco de dados.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>As diferenças entre uma implementação local e no Azure 

Veja a seguir algumas coisas importantes a ter em mente ao migrar aplicativos locais para o Azure. 

Uma diferença importante é que, em uma implementação do Azure, recursos como VMs, discos e redes virtuais são compartilhados entre outros clientes. Além disso, os recursos podem ser limitados de acordo com os requisitos. Em vez de se concentrar em evitar falhas (MTBF), o Azure é mais focado em sobreviver a elas (MTTR).

A tabela a seguir lista algumas das diferenças entre uma implementação local e uma implementação do Azure de um banco de dados Oracle.


|  | Implementação local | Implementação no Azure |
| --- | --- | --- |
| **Rede** |LAN/WAN  |SDN (Rede definida por software)|
| **Grupo de segurança** |Ferramentas de restrições de IP/porta |[Grupo de segurança de rede (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **Resiliência** |MTBF (tempo médio entre falhas) |MTTR (tempo médio para recuperação)|
| **Manutenção planejada** |Aplicação de patch/upgrades|[Conjuntos de disponibilidade](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (aplicação de patch/upgrades gerenciados pelo Azure) |
| **Recurso** |Dedicado  |Compartilhado com outros clientes|
| **Regiões** |Datacenters |[Pares de regiões](../../regions.md#region-pairs)|
| **Storage** |SAN/discos físicos |[Armazenamento gerenciado pelo Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Escala** |Escala vertical |Escala horizontal|


### <a name="requirements"></a>Requisitos

- Determine o uso real da CPU, já que a Oracle é licenciada por núcleo, dimensionar as necessidades de vCPU pode ser um exercício essencial para a economia de custos. 
- Determine o tamanho do banco de dados, o armazenamento de backup e a taxa de crescimento.
- Determine os requisitos de e/s, que podem ser estimados com base nos relatórios do Oracle STATSPACK e AWR ou nas ferramentas de monitoramento de armazenamento de nível do sistema operacional.

## <a name="configuration-options"></a>Opções de configuração

Há quatro áreas possíveis que você pode ajudar para melhorar o desempenho em um ambiente do Azure:

- Tamanho da máquina virtual
- Taxa de transferência de rede
- Tipos e configurações de disco
- Configurações de cache de disco

### <a name="generate-an-awr-report"></a>Gerar um relatório AWR

Se você tiver um banco de dados Oracle Enterprise Edition existente e estiver planejando migrar para o Azure, terá várias opções. Se você tiver o [pacote de diagnóstico](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) para suas instâncias do Oracle, poderá executar o relatório AWR da Oracle para obter as métricas (IOPS, Mbps, GiBs e assim por diante). Para esses bancos de dados sem a licença do pacote de diagnóstico ou para um banco de dados Standard Edition, as mesmas métricas importantes podem ser coletadas com um relatório STATSPACK após a coleta de instantâneos manuais.  A principal diferença entre esses dois métodos de relatório é que o AWR é automaticamente coletado e fornece mais informações sobre o banco de dados do que é a opção de relatório predecessora de STATSPACK.

Considere a execução do relatório AWR durante cargas de trabalho regulares e de pico para poder comparar a diferença. Para coletar a carga de trabalho mais precisa, considere um relatório de janela estendida de uma semana, versus uma execução de 24 horas e perceba que o AWR fornece as médias como parte de seus cálculos no relatório.  Para uma migração de datacenter, é recomendável coletar relatórios para o dimensionamento nos sistemas de produção e estimar as cópias de banco de dados restantes usadas para teste de usuário, teste, desenvolvimento, etc. por percentuais (UAT igual a produção, teste e desenvolvimento 50% de dimensionamento de produção, etc.)

Por padrão, o repositório AWR retém 8 dias de dados e usa instantâneos em intervalos por hora.  Para executar um relatório AWR na linha de comando, o seguinte pode ser executado em um terminal:

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>Métricas-chave

O relatório solicitará as seguintes informações:
- Tipo de relatório: HTML ou TEXT, (HTML em 12,1 e fornece informações adicionais do que o formato de texto.)
- O número de dias de instantâneos a serem exibidos, (para intervalos de uma hora, um relatório de uma semana seria um 168 diferente em IDs de instantâneo)
- O Instantâneoid inicial da janela de relatório.
- O Instantâneoid final para a janela de relatório.
- O nome do relatório a ser criado pelo script AWR.

Se estiver executando o AWR em um cluster de aplicativo real, (RAC), o relatório de linha de comando será o awrgrpt. SQL em vez de awrrpt. Sql.  O relatório "g" criará um relatório para todos os nós no banco de dados RAC em um único relatório versus ter que executar um em cada nó RAC.

Veja a seguir as métricas que você pode obter do relatório AWR:

- Nome do banco de dados, nome da instância e nome do host
- Versão do banco de dados, (suporte da Oracle)
- CPU/núcleos
- SGA/PGA, (e consultores para informá-lo se ele for subdimensionado)
- Memória total em GB
- % De CPU ocupada
- CPUs de BD
- IOPs (leitura/gravação)
- MBPs (leitura/gravação)
- Taxa de transferência de rede
- Taxa de latência de rede (baixa/alta)
- Principais eventos de espera 
- Configurações de parâmetro para o banco de dados
- É Database RAC, Exadata, usando recursos avançados ou configurações

### <a name="virtual-machine-size"></a>Tamanho da máquina virtual

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. estimar o tamanho da VM com base na CPU, memória e uso de e/s do relatório AWR

Examine os cinco eventos de primeiro plano maior tempo, que indicam onde estão os gargalos do sistema.

Por exemplo, no diagrama a seguir, a sincronização de arquivos de log está na parte superior. Isso indica o número de esperas necessárias antes que o LGWR grave o buffer de log no arquivo de log da fase refazer. Esses resultados indicam que há a necessidade de armazenamento ou discos com um desempenho melhor. Além disso, o diagrama também mostra o número de CPU (núcleos) e da quantidade de memória.

![Captura de tela que mostra a sincronização do arquivo de log na parte superior da tabela.](./media/oracle-design/cpu_memory_info.png)

O diagrama a seguir mostra o total de E/S de leitura e gravação. 59 GB de leitura e 247,3 GB de gravação ocorreram durante o período do relatório.

![Captura de tela que mostra a e/s total de leitura e gravação.](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. escolher uma VM

Com base nas informações coletadas do relatório AWR, a próxima etapa é escolher uma VM com um tamanho parecido que atenda às suas necessidades. Encontre uma lista de VMs disponíveis no artigo [Memória otimizada](../../sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Ajuste o dimensionamento da VM com uma série de VMs semelhante com base no ACU

Depois de escolher a VM, preste atenção às ACUs das VMs. Você pode escolher uma VM diferente com base no valor da ACU que atender melhor às suas necessidades. Para saber mais, confira [Unidade de computação do Azure](../../acu.md).

![Captura de tela da página de unidades de ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Taxa de transferência de rede

O diagrama a seguir mostra a relação entre a taxa de transferência e o IOPS:

![Captura de tela da taxa de transferência](./media/oracle-design/throughput.png)

A taxa de transferência de rede total é estimada com base nas seguintes informações:
- Tráfego SQL \*Rede
- MBps x número de servidores (fluxo de saída, como o Oracle Data Guard)
- Outros fatores, como replicação de aplicativo

![Captura de tela da taxa de transferência SQL*Rede](./media/oracle-design/sqlnet_info.png)

Com base nos requisitos de largura de banda de sua rede, há vários tipos de gateway para escolher. Entre eles basic, VpnGw e Azure ExpressRoute. Para saber mais, confira a [página de preços do gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Recomendações**

- A latência de rede é maior em comparação com uma implantação local. Reduzir as viagens de ida e volta da rede pode melhorar significativamente o desempenho.
- Para reduzir as viagens de ida e volta, consolide os aplicativos que têm transações alta ou aplicativos “comunicativos” na mesma máquina virtual.
- Use máquinas virtuais com [rede acelerada](../../../virtual-network/create-vm-accelerated-networking-cli.md) para melhorar o desempenho da rede.
- Para determinadas distribuições do Linux, considere habilitar o [suporte a corte/desmapeamento](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Instale o [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) em uma máquina virtual separada.
- As páginas enormes não são habilitadas no Linux por padrão. Considere habilitar páginas enormes e definir `use_large_pages = ONLY` no Oracle DB. Isso pode ajudar a aumentar o desempenho. Encontre mais informações [aqui](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Tipos e configurações de disco

- *Discos padrão do SO*: esses tipos de disco oferecem dados persistentes e cache. Eles são otimizados para acesso de SO na inicialização, e não foram projetados para cargas de trabalho transacionais ou de data warehouse (analíticas).

- *Discos gerenciados*: o Azure gerencia contas de armazenamento que podem ser usadas para seus discos de VM. Você especifica o tipo de disco (geralmente a SSD Premium para cargas de trabalho do Oracle) e o tamanho do disco necessário. O Azure cria e gerencia o disco para você.  SSD Premium disco gerenciado só está disponível para séries de VMs com otimização de memória e especificamente projetadas. Depois de escolher um tamanho de VM específico, o menu mostrará apenas os SKUs de armazenamento premium disponíveis com base no tamanho da VM.

![Captura de tela da página de disco gerenciado](./media/oracle-design/premium_disk01.png)

Depois de configurar o armazenamento em uma VM, convém realizar um teste de carga nos discos antes de criar um banco de dados. Saber a taxa de E/S em termos de latência e taxa de transferência pode ajudar a determinar se as VMs dão suporte às metas de taxa de transferência e latência esperadas.

Há várias ferramentas para testes de carga de aplicativo, como Oracle Orion, Sysbench, SLOB e fio.

Execute o teste de carga novamente depois de implantar um banco de dados Oracle. Inicie suas cargas de trabalho de pico e regulares, e os resultados mostram a linha de base de seu ambiente.  Seja realista no teste de carga de trabalho – não faz sentido executar uma carga de trabalho que não seja como a que será executada na VM na realidade.

Como a Oracle é um banco de dados com uso intensivo de e/s para muitos, é muito importante dimensionar o armazenamento com base na taxa de IOPS em vez do tamanho do armazenamento. Por exemplo, se o IOPS exigido for 5.000, mas você só precisar de 200 GB, você ainda poderá obter o disco premium de classe P30, embora ele venha com mais de 200 GB de armazenamento.

A taxa de IOPS pode ser obtida no relatório do AWR. Ela é determinada pela taxa de gravações, leituras físicas e log da fase refazer.  Sempre verifique se a série de VMs escolhida também tem a capacidade de lidar com a demanda de e/s da carga de trabalho.  Se a VM tiver um limite de e/s menor do que o armazenamento, o limite máximo será definido pela VM.

![Captura de tela da página do relatório AWR](./media/oracle-design/awr_report.png)

Por exemplo, o tamanho da fase refazer é 12.200.000 bytes por segundo, que é igual a 11,63 MBPs.
O IOPS é 12.200.000 / 2.358 = 5.174.

Quando você tiver uma visão clara dos requisitos de E/S, poderá escolher a combinação de unidades mais adequada para atender a esses requisitos.

**Recomendações**

- Para o espaço de tabela de dados, distribua a carga de trabalho de E/S entre diversos discos usando o armazenamento gerenciado ou o Oracle ASM.
- Use a compactação avançada Oracle para reduzir a e/s (para dados e índices).
- Separe os logs de refazer, os espaços de tabela temp e desfazer em discos de dados separados.
- Não coloque arquivos de aplicativo em discos do SO padrão (/dev/sda). Esses discos são otimizados inicializações rápidas de VM, e talvez não forneçam um bom desempenho para seu aplicativo.
- Ao usar VMs da série M no armazenamento Premium, habilite [acelerador de gravação](../../how-to-enable-write-accelerator.md) no disco de logs de restauração.
- Considere a possibilidade de mover os logs de refazer com alta latência para o ultra Disk.

### <a name="disk-cache-settings"></a>Configurações de cache de disco

Há três opções de cache de host, mas para um banco de dados Oracle, somente o cache ReadOnly é recomendado para uma carga de trabalho de banco de dados.  O ReadWrite pode introduzir vulnerabilidades significativas em um arquivo de dados, onde o objetivo de uma gravação de banco de dados é gravá-lo no arquivo de informações, não armazenar em cache a informação.

Ao contrário de um sistema de arquivos ou aplicativo, para um banco de dados, a recomendação para o cache de host é *ReadOnly*: todas as solicitações são armazenadas em cache para leituras futuras. Todas as gravações continuam a ser gravadas no disco.

**Recomendações**

Para maximizar a taxa de transferência, recomendamos que você comece com **ReadOnly** para o cache de host sempre que possível. Para o armazenamento Premium, tenha em mente que você deve desabilitar as "barreiras" ao montar o sistema de arquivos com as opções **ReadOnly** . Atualize o arquivo /etc/fstab com o UUID para os discos.

![Captura de tela da página de disco gerenciado que mostra as opções ReadOnly e None.](./media/oracle-design/premium_disk02.png)

- Para discos do sistema operacional, use o cache de **leitura/gravação** padrão e use o SSD Premium para VMs de carga de trabalho Oracle.  Além disso, verifique se o volume usado para permuta também está no SSD Premium.
- Para todos os arquivos de arquivo, use **ReadOnly** para Caching. O cache somente leitura está disponível somente para o disco gerenciado Premium, p30 e superior.  Há um limite de um volume 4095GiB que pode ser usado com cache ReadOnly.  Qualquer alocação maior desabilitará o cache de host por padrão.

Se as cargas de trabalho variam muito entre o dia e a noite e a carga de trabalho de e/s pode dar suporte a ela, o P1-P20 SSD Premium com intermitência pode fornecer o desempenho necessário durante cargas de lote de tempo noturno ou demandas de e/s limitadas.  

## <a name="security"></a>Segurança

Depois de instalar e configurar seu ambiente do Azure, a próxima etapa será proteger sua rede. Veja algumas recomendações:

- *Política de NSG*: o NSG pode ser definido por uma sub-rede ou NIC. É mais simples controlar o acesso no nível de sub-rede, tanto para segurança quanto para roteamento de força para coisas como firewalls de aplicativo.

- *Jumpbox*: para um acesso mais seguro, os administradores não devem conectar diretamente ao serviço de aplicativo ou ao banco de dados. Um jumpbox é usado como um intermediário entre o computador do administrador e os recursos do Azure.
![Captura de tela da página de topologia do Jumpbox](./media/oracle-design/jumpbox.png)

    O computador do administrador deve oferecer somente acesso restrito por IP ao jumpbox. O jumpbox deve ter acesso ao aplicativo e ao banco de dados.

- *Rede privada* (sub-redes): recomendamos que o serviço de aplicativo e o banco de dados estejam em sub-redes separadas, assim é possível definir um controle melhor por política de NSG.


## <a name="additional-reading"></a>Leituras adicionais

- [Configurar o Oracle ASM](configure-oracle-asm.md)
- [Configurar o Oracle Data Guard](configure-oracle-dataguard.md)
- [Configurar o Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Backup e recuperação do Oracle](./oracle-overview.md)

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: criar VMs altamente disponíveis](../../linux/create-cli-complete.md)
- [Explorar exemplos da CLI do Azure de implantação de VM](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
