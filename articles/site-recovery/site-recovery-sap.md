---
title: "Proteger uma implantação de aplicativos SAP NetWeaver de várias camada usando o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como proteger as implantações de aplicativos SAP NetWeaver usando o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: manayar
ms.openlocfilehash: 951980eeba61e53c983d5b23c301c81eee9528bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-using-azure-site-recovery"></a>Proteger uma implantação de aplicativo SAP NetWeaver de várias camada usando o Azure Site Recovery

A maioria das implantações do SAP de grande e médio porte têm alguma forma de solução de recuperação de desastre.  A importância de se ter soluções de recuperação de desastre sólidas foi aumentando à medida que os processos de negócios mais vitais foram movidos para aplicativos como o SAP.  O Azure Site Recovery foi testado e integrado a aplicativos SAP e ultrapassa as capacidades da maioria das soluções de recuperação de desastre locais, com menor TCO (custo total de propriedade) que as soluções da concorrência.
Com o Azure Site Recovery, você pode:
* Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver Production em execução no local, por meio da replicação de componentes no Azure.
* Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver Production em execução no Azure, por meio da replicação de componentes em outro datacenter do Azure.
* Simplifique a migração na nuvem usando o Site Recovery para migrar sua implantação do SAP para o Azure.
* Simplifique as atualizações, teste e criação de protótipos de projeto SAP criando um clone de produção sob demanda para testar aplicativos SAP.

Este artigo descreve como proteger as implantações de aplicativos SAP NetWeaver usando o [Azure Site Recovery](site-recovery-overview.md). Este artigo aborda as melhores práticas para proteger uma implantação do SAP NetWeaver de três camadas no Azure por meio da replicação para outro datacenter do Azure usando o Azure Site Recovery, os cenários e as configurações com suporte e como executar failovers, tanto os de teste (simulações de recuperação de desastre) como os reais.


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisa entender o seguinte:

1. [Replicar uma máquina virtual no Azure](azure-to-azure-walkthrough-enable-replication.md)
2. Como [criar uma rede de recuperação](site-recovery-azure-to-azure-networking-guidance.md)
3. [Executar um failover de teste no Azure](azure-to-azure-walkthrough-test-failover.md)
4. [Executar um failover no Azure](site-recovery-failover.md)
5. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
6. Como [replicar o SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Cenários com suporte
Com o Azure Site Recovery, você pode implementar uma solução de recuperação de desastre para os seguintes cenários:
* Sistemas SAP em execução em um datacenter do Azure replicando para outro datacenter do Azure (recuperação de desastre do Azure para o Azure), conforme projetado [aqui](https://aka.ms/asr-a2a-architecture).
* Sistemas SAP em execução em servidores VMWare (ou físicos) locais replicando para um site de recuperação de desastre em um datacenter do Azure (recuperação de desastre do VMware para o Azure), que exige alguns componentes adicionais, conforme projetado [aqui](https://aka.ms/asr-v2a-architecture).
* Sistemas SAP em execução em Hyper-V locais replicando para um site de recuperação de desastre em um datacenter do Azure (recuperação de desastre do Hyper-V para o Azure), que exige alguns componentes adicionais, conforme projetado [aqui](https://aka.ms/asr-h2a-architecture).

Este documento usa o primeiro caso, recuperação de desastre do Azure para o Azure, para demonstrar os recursos de recuperação de desastre do SAP do Azure Site Recovery. Como a replicação do Azure Site Recovery independe do aplicativo, o processo descrito deve funcionar também em outros cenários.

### <a name="required-foundation-services"></a>Serviços básicos necessários
Este cenário de documentação foi todo implantado com os seguintes serviços básicos:
* ExpressRoute ou VPN (Rede Virtual Privada) Site a Site
* Pelo menos um Controlador de Domínio do Active Directory e um servidor DNS em execução no Azure

É recomendável que a infraestrutura acima seja estabelecida antes da implantação do Azure Site Recovery.


## <a name="typical-sap-application-deployment"></a>Implantação típica do aplicativo SAP
Os clientes grandes do SAP geralmente implantam entre 6 e 20 aplicativos SAP individuais.  A maioria desses aplicativos é baseada em mecanismos Java ou ABAP do SAP NetWeaver.  Dando suporte a esses aplicativos NetWeaver, existem vários mecanismos autônomos e aplicativos específicos que não são SAP NetWeaver.  

É fundamental fazer o inventário de todos os aplicativos SAP em execução em um cenário para determinar o modo de implantação (2 ou 3 camadas), as versões, os patches, os tamanhos, as taxas de variação e os requisitos de persistência do disco.

![Padrão de implantação](./media/site-recovery-sap/sap-typical-deployment.png)

A camada de persistência do Banco de Dados SAP deve ser protegida por meio de ferramentas nativas do DBMS, como AlwaysOn do SQL Server, Oracle Data Guard ou HANA System Replication. A camada de clientes também não é protegida pelo Azure Site Recovery, mas é importante considerar os tópicos que afetam essa camada, como atraso de propagação de DNS, segurança e acesso remoto ao data center de recuperação de desastre.

O Azure Site Recovery é a solução recomendada para a camada de aplicativo, incluindo o (A)SCS. Outros aplicativos, como aplicativos que não são SAP NetWeaver e SAP, fazem parte do ambiente geral de implantação do SAP e também devem ser protegidos com o Azure Site Recovery.

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais
Siga [este guia](azure-to-azure-walkthrough-enable-replication.md) para iniciar a replicação de todas as máquinas de virtuais do aplicativo SAP para o data center de recuperação de desastre do Azure.

Se você estiver usando um IP estático, poderá especificar o IP que a máquina virtual deverá usar na seção Placas de adaptador de rede, nas configurações de Rede e Computação.

![IP de Destino](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação permite o sequenciamento do failover de várias camadas em um aplicativo de várias camadas, mantendo assim a consistência do aplicativo. Execute as etapas descritas [aqui](site-recovery-create-recovery-plans.md) ao criar um plano de recuperação para um aplicativo Web de várias camadas.

### <a name="adding-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação
Talvez seja necessário fazer algumas operações nas máquinas virtuais do Azure após o failover/teste de failover para que seus aplicativos funcionem corretamente. Você pode automatizar a operação de failover, como a atualização da entrada DNS e a alteração de associações e conexões, adicionando scripts correspondentes ao plano de recuperação, conforme descrito [neste artigo](site-recovery-create-recovery-plans.md#add-scripts).

### <a name="dns-update"></a>Atualização de DNS
Se o DNS estiver configurado para atualização dinâmica de DNS, as máquinas virtuais normalmente atualizarão o DNS com o novo IP quando forem iniciadas. Se você quiser adicionar uma etapa explícita para atualizar o DNS com os novos IPs das máquinas virtuais, adicione este [script para atualizar o IP no DNS](https://aka.ms/asr-dns-update) como uma ação posterior nos grupos de plano de recuperação.  

## <a name="example-azure-to-azure-deployment"></a>Exemplo de implantação do Azure para o Azure
O cenário de recuperação de desastre do Azure Site Recovery de Azure para Azure está descrito no diagrama abaixo:
* O Datacenter Primário está localizado em Cingapura (Sudeste Asiático no Azure) e o data center de recuperação de desastre está em Hong Kong (Ásia Oriental no Azure).  Nesse cenário, a Alta Disponibilidade local é fornecida por duas VMs que executam o SQL Server AlwaysOn no modo síncrono em Cingapura.
* O ASCS de Compartilhamento de Arquivos pode ser usado para fornecer alta disponibilidade aos pontos únicos de falha do SAP. O ASCS de Compartilhamento de Arquivos não requer um disco de cluster compartilhado, e aplicativos como o SIOS não são necessários.
* A proteção de recuperação de desastre para a camada de DBMS é obtida usando replicação assíncrona.
* Este cenário mostra a "recuperação de desastre simétrica", um termo usado para descrever uma solução de recuperação de desastre que é uma réplica exata da produção e, portanto, a solução de recuperação de desastre do SQL Server tem Alta Disponibilidade local. O uso de recuperação de desastre simétrica não é obrigatório para a camada de banco de dados, e muitos clientes aproveitam a flexibilidade de implantações de nuvem para criar um Nó de Alta Disponibilidade local rapidamente após um evento de recuperação de desastre.
* O diagrama ilustra a camada de servidor de aplicativo e do SAP NetWeaver ASCS replicada pelo Azure Site Recovery.

![Cenário de replicação](./media/site-recovery-sap/sap-replication-scenario.png)

## <a name="doing-a-test-failover"></a>Executar um failover de teste
Siga [este guia](azure-to-azure-walkthrough-test-failover.md) fazer um failover de teste.

1.  Vá para o Portal do Azure e selecione seu cofre dos Serviços de Recuperação.
2.  Clique no plano de recuperação criado para os aplicativos SAP.
3.  Clique em 'Failover de Teste'.
4.  Selecione o ponto e recuperação e a rede virtual do Azure para iniciar o processo de failover de teste.
5.  Quando o ambiente secundário estiver funcionando, você poderá executar sua validações.
6.  Assim que as validações forem concluídas, clique em “Limpar o failover de teste” para limpar o ambiente de failover.

## <a name="doing-a-failover"></a>Executar um failover
Siga [este guia](site-recovery-failover.md) quando estiver realizando um failover.

1.  Vá para o Portal do Azure e selecione seu cofre dos Serviços de Recuperação.
2.  Clique no plano de recuperação criado para o aplicativo de SAP.
3.  Clique em 'Failover'.
4.  Selecione o ponto de recuperação para iniciar o processo de failover.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como criar uma solução de recuperação de desastre para implantações do SAP NetWeaver usando o Azure Site Recovery [neste white paper](http://aka.ms/asr-sap). O white paper também aborda as recomendações para diferentes arquiteturas do SAP, lista de aplicativos com suporte e tipos de VM para SAP no Azure e descreve possíveis planos de teste para a solução de recuperação de desastre.

Saiba mais sobre [como replicar outras cargas de trabalho](site-recovery-workload.md) usando o Site Recovery.
