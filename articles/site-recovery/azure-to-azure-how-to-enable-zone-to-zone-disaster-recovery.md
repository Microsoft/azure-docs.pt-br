---
title: Habilitar a recuperação de desastre de zona para zona para máquinas virtuais do Azure
description: Este artigo descreve quando e como usar a recuperação de desastre de zona para zona para máquinas virtuais do Azure.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: beba8e1d8126818f142e4873d551ed077af869d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035230"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Habilitar a recuperação de desastre de VM do Azure entre zonas de disponibilidade

Este artigo descreve como replicar, fazer failover e failback de máquinas virtuais do Azure de uma zona de disponibilidade para outra, na mesma região do Azure.

>[!NOTE]
>
>- O suporte para a recuperação de desastre de zona para zona está limitado atualmente às seguintes regiões: Sudeste Asiático, leste do Japão, leste da Austrália, Sul do Reino Unido, Europa Ocidental, Europa Setentrional, EUA Central, leste dos EUA, leste dos EUA 2 e oeste dos EUA 2.  
>- Site Recovery não move nem armazena os dados do cliente fora da região em que ele é implantado quando o cliente está usando a recuperação de desastre de zona para zona. Os clientes poderão selecionar um cofre dos serviços de recuperação em uma região diferente, se escolherem. O cofre dos serviços de recuperação contém metadados, mas não dados reais do cliente.

Site Recovery serviço contribui para sua estratégia de recuperação de desastre e continuidade de negócios mantendo seus aplicativos de negócios em funcionamento, durante interrupções planejadas e não planejadas. É a opção de recuperação de desastre recomendada para manter seus aplicativos em funcionamento, se houver interrupções regionais.

As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona tem um ou mais datacenters. 

Se você quiser mover VMs para uma zona de disponibilidade em uma região diferente, [examine este artigo](../resource-mover/move-region-availability-zone.md).

## <a name="using-availability-zones-for-disaster-recovery"></a>Usando Zonas de Disponibilidade para recuperação de desastres 

Normalmente, Zonas de Disponibilidade são usadas para implantar VMs em uma configuração de alta disponibilidade. Eles podem estar muito próximos uns dos outros para servir como uma solução de recuperação de desastre em caso de desastre natural.

No entanto, em alguns cenários, Zonas de Disponibilidade pode ser aproveitada para a recuperação de desastres:

- Muitos clientes que tinham uma estratégia de recuperação de desastres Metro enquanto hospedavam aplicativos no local às vezes procuram imitar essa estratégia depois de migrarem os aplicativos para o Azure. Esses clientes reconhecem o fato de que a estratégia de recuperação de desastres metro pode não funcionar no caso de um desastre físico em larga escala e aceitar esse risco. Para tais clientes, a recuperação de desastres de zona para zona pode ser usada como uma opção de recuperação de desastre.

- Muitos outros clientes têm uma infraestrutura de rede complicada e não querem recriá-la em uma região secundária devido ao custo e à complexidade associados. A recuperação de desastres de zona a zona reduz a complexidade, pois aproveita os conceitos de rede redundantes em Zonas de Disponibilidade tornando a configuração muito mais simples. Esses clientes preferem a simplicidade e também podem usar Zonas de Disponibilidade para recuperação de desastres.

- Em algumas regiões que não têm uma região emparelhada na mesma jurisdição legal (por exemplo, Sudeste Asiático), a recuperação de desastres de zona para zona pode servir como a solução de recuperação de desastres de fato, pois ajuda a garantir a conformidade legal, já que seus aplicativos e dados não se movem entre limites nacionais. 

- A recuperação de desastres de zona para zona implica a replicação de dados entre distâncias menores quando comparada com a recuperação de desastre do Azure para o Azure e, portanto, você pode ver uma latência menor e, consequentemente, RPO inferior

Embora essas sejam vantagens fortes, há a possibilidade de que a recuperação de desastres de zona para zona possa cair de requisitos de resiliência no caso de um desastre natural em toda a região.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Rede para recuperação de desastre de zona para zona

Conforme mencionado acima, a recuperação de desastre de zona para zona reduz a complexidade, pois aproveita os conceitos de rede redundantes em Zonas de Disponibilidade tornando a configuração muito mais simples. O comportamento dos componentes de rede no cenário de recuperação de desastre zona a zona é descrito abaixo: 

- Rede virtual: você pode usar a mesma rede virtual que a rede de origem para failovers reais. Use uma rede virtual diferente para a rede virtual de origem para failovers de teste.

- Sub-rede: há suporte para o failover na mesma sub-rede.

- Endereço IP privado: se você estiver usando endereços IP estáticos, poderá usar os mesmos IPs na zona de destino se optar por configurá-los de forma tal.

- Rede acelerada: semelhante à recuperação de desastre do Azure para o Azure, você pode habilitar a rede acelerada se o SKU da VM oferecer suporte a ela.

- Endereço IP público: você pode anexar um endereço IP público padrão criado anteriormente na mesma região para a VM de destino. Os endereços IP públicos básicos não dão suporte a cenários relacionados à zona de disponibilidade.

- Balanceador de carga: o Load Balancer Standard é um recurso regional e, portanto, a VM de destino pode ser anexada ao pool de back-end do mesmo balanceador de carga. Um novo balanceador de carga não é necessário.

- Grupo de segurança de rede: você pode usar os mesmos grupos de segurança de rede aplicados à VM de origem.

## <a name="pre-requisites"></a>Pré-requisitos

Antes de implantar a recuperação de desastre de zona em zona para suas VMs, é importante garantir que outros recursos habilitados na VM sejam interoperáveis com a recuperação de desastre de zona para zona.

|Recurso  | Declaração de suporte  |
|---------|---------|
|VMs clássicas   |     Sem suporte    |
|VMs ARM    |    Com suporte    |
|Azure Disk Encryption V1 (passagem dupla, com Azure Active Directory (Azure AD))     |     Com suporte   |
|Azure Disk Encryption v2 (passagem única, sem o Azure AD)    |    Com suporte    |
|Discos não gerenciados    |    Sem suporte    |
|Discos gerenciados    |    Com suporte    |
|Chaves gerenciadas pelo cliente    |    Com suporte    |
|Grupos de posicionamento de proximidade    |    Com suporte    |
|Interoperabilidade de backup    |    Há suporte para backup e restauração em nível de arquivo. Backup e restauração de nível de VM e de disco e sem suporte.    |
|Adicionar/remover quente    |    Os discos podem ser adicionados depois de habilitar a replicação de zona para zona. Não há suporte para a remoção de discos após habilitar a replicação de zona para zona.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Configurar a zona de Site Recovery para a recuperação de desastres de zona

### <a name="log-in"></a>Fazer logon

Faça logon no Portal do Azure.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Habilitar a replicação para a máquina virtual zonal do Azure

1. No menu do portal do Azure, selecione Máquinas virtuais ou pesquise e selecione Máquinas virtuais em qualquer página. Selecione a VM que você deseja replicar. Para a recuperação de desastre de zona para zona, essa VM já deve estar em uma zona de disponibilidade.

2. Em Operações, clique em Recuperação de desastre.

3. Conforme mostrado abaixo, na guia noções básicas, selecione ' Sim ' para ' recuperação de desastre entre Zonas de Disponibilidade? '

    ![Página configurações básicas](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Se você aceitar todos os padrões, clique em ' revisar + iniciar replicação ' seguido de ' Iniciar replicação '.

5. Se você quiser fazer alterações nas configurações de replicação, clique em ' Avançar: configurações avançadas '.

6. Altere as configurações para longe do padrão, sempre que apropriado. Para usuários do Azure para a recuperação de desastre do Azure, essa página pode parecer familiar. Mais detalhes sobre as opções apresentadas nesta folha podem ser encontrados [aqui](./azure-to-azure-tutorial-enable-replication.md)

    ![Página Configurações avançadas](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Clique em ' Avançar: revisar + iniciar replicação ' e ' Iniciar replicação '.

## <a name="faqs"></a>Perguntas frequentes

**1. como o preço funciona para a recuperação de desastre de zona para zona?**
Os preços da recuperação de desastre de zona para zona são idênticos aos preços da recuperação de desastre do Azure para o Azure. Você pode encontrar mais detalhes na página de preços [aqui](https://azure.microsoft.com/pricing/details/site-recovery/) e [aqui](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/). Observe que os encargos de egresso que você veria na recuperação de desastre de zona para zona seriam menores do que a recuperação de desastre da região para a região.

**2. qual é o SLA para RTO e RPO?**
O SLA do RTO é o mesmo que para Site Recovery geral. Prometemos o RTO de até 2 horas. Não há SLA definido para RPO.

**3. a capacidade é garantida na zona secundária?**
A equipe do Site Recovery e equipe de gerenciamento de capacidade do Azure planejam a capacidade de infraestrutura suficiente. Quando você inicia um failover, as equipes também ajudam a garantir que as instâncias de VM protegidas pelo Site Recovery serão implantadas na zona de destino.

**4. quais sistemas operacionais têm suporte?**
A recuperação de desastre de zona para zona dá suporte aos mesmos sistemas operacionais que a recuperação de desastre do Azure para o Azure. Consulte a matriz de suporte [aqui](./azure-to-azure-support-matrix.md).

**5. os grupos de recursos de origem e de destino podem ser iguais?**
Não, você deve fazer failover para um grupo de recursos diferente.

## <a name="next-steps"></a>Próximas etapas

As etapas que precisam ser seguidas para executar uma análise de recuperação de desastre, failover, proteger novamente e failback são as mesmas que as etapas no cenário de recuperação de desastre do Azure para o Azure.

Para executar uma análise de recuperação de desastre, siga as etapas descritas [aqui](./azure-to-azure-tutorial-dr-drill.md).

Para executar um failover e proteger novamente as VMs na zona secundária, siga as etapas descritas [aqui](./azure-to-azure-tutorial-failover-failback.md).

Para fazer failback para a zona primária, siga as etapas descritas [aqui](./azure-to-azure-tutorial-failback.md).
