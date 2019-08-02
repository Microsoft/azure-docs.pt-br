---
title: Planejamento da migração de recursos de IaaS do clássico para o Azure Resource Manager | Microsoft Docs
description: Planejamento da migração de recursos de IaaS do clássico para o Azure Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: 713e814478f88def2559dad79aafeb6fa2737d7f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667351"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planejamento da migração de recursos de IaaS do clássico para o Azure Resource Manager
Embora o Azure Resource Manager ofereça vários recursos incríveis, é fundamental planejar sua jornada de migração para garantir que tudo ocorra sem problemas. Gastar tempo no planejamento garantirá que não ocorram problemas durante a execução das atividades de migração. 

> [!NOTE] 
> As diretrizes a seguir foram uma contribuição intensiva da equipe de Consultoria para Clientes do Azure e dos arquitetos da Solução na Nuvem que trabalham com clientes para a migração de ambientes de grande porte. Assim, este documento continuará sendo atualizado à medida que surgirem novos padrões de sucesso. Portanto, verifique se há novas recomendações periodicamente.

Há quatro fases gerais da jornada de migração:

![Fases da migração](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planejamento

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e compensações

Dependendo do tamanho dos requisitos técnicos, das regiões geográficas e das práticas operacionais, talvez você deseje considerar:

1. Por que sua organização deseja usar o Azure Resource Manager?  Quais são os motivos comerciais para uma migração?
2. Quais são os motivos técnicos para o Azure Resource Manager?  Quais serviços adicionais do Azure (se houver) você gostaria de utilizar?
3. Qual aplicativo (ou conjuntos de máquinas virtuais) está incluído na migração?
4. Há suporte para quais cenários na API de migração?  Examine os [recursos e as configurações sem suporte](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations).
5. As equipes operacionais agora darão suporte a aplicativos/VMs no Clássico e no Azure Resource Manager?
6. Como o Azure Resource Manager altera seus processos de implantação, gerenciamento, monitoramento e relatório da VM (se houver)?  Os scripts de implantação precisam ser atualizados?
7. Qual é o plano de comunicação para informar os stakeholders (usuários finais, proprietários do aplicativo e proprietários da infraestrutura)?
8. Dependendo da complexidade do ambiente, deve haver um período de manutenção em que o aplicativo não estará disponível para os usuários finais e os proprietários do aplicativo?  Em caso afirmativo, por quanto tempo?
9. Qual é o plano de treinamento para garantir que os stakeholders têm conhecimento e são proficientes no Azure Resource Manager?
10. Qual é o plano de gerenciamento do programa ou do projeto para a migração?
11. Quais são as linhas do tempo para a migração do Azure Resource Manager e outros roteiros de tecnologia relacionados?  Eles estão alinhados corretamente?

### <a name="patterns-of-success"></a>Padrões de sucesso

Os clientes de sucesso têm planos detalhados para quando as perguntas anteriores são abordadas, documentadas e controladas.  Garanta que os planos de migração são comunicados em larga escala para os patrocinadores e stakeholders.  Esteja munido com o conhecimento sobre as opções de migração; é altamente recomendável ler todo este conjunto de documentos sobre migração abaixo.

* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planejamento para a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ferramentas da comunidade para ajudar com a migração de recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Examinar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Confira as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

- Falha no planejamento.  As etapas de tecnologia desta migração são comprovadas e o resultado é previsível.
- Pressuposto de que a API de migração com suporte na plataforma considerará todos os cenários. Leia [recursos e configurações sem suporte](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) para entender para quais cenários há suporte.
- Não planejar a possível interrupção do aplicativo para os usuários finais.  Planeje ter um buffer suficiente para avisar de forma adequada os usuários finais sobre o período durante o qual o aplicativo não estará disponível.


## <a name="lab-test"></a>Teste de laboratório 

**Replicar o ambiente e fazer uma migração de teste**
  > [!NOTE]
  > A replicação exata do ambiente existente é executada com uma ferramenta que é uma contribuição da comunidade, sem suporte oficial do Suporte da Microsoft. Portanto, é uma etapa **opcional**, mas é a melhor maneira de descobrir problemas sem interferir nos ambientes de produção. Se o uso de uma ferramenta que é uma contribuição da comunidade não for uma opção, leia mais sobre a recomendação da Simulação Validar/Preparar/Anular abaixo.
  >
  
  Conduzir um teste de laboratório do cenário exato (computação, rede e armazenamento) é a melhor maneira de garantir uma migração tranquila. Isso ajuda a garantir que:

- Um laboratório totalmente separado ou um ambiente de não produção existente para o teste. Recomendamos ter um laboratório totalmente separado que pode ser migrado repetidamente e modificado de forma destrutiva.  Os scripts para coletar/hidratar metadados das assinaturas reais são listados abaixo.
- É uma boa ideia criar o laboratório em uma assinatura separada. O motivo disso é que o laboratório será destruído repetidamente e ter uma assinatura isolada e separada reduzirá a possibilidade de que algo real seja acidentalmente excluído.

  Isso pode ser feito com a ferramenta AsmMetadataParser. [Leia mais sobre essa ferramenta aqui](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Padrões de sucesso

Veja a seguir os problemas descobertos em muitas das migrações de maior porte. Esta não é uma lista completa; consulte os [recursos e as configurações sem suporte](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) para obter mais detalhes. Você poderá ou não ter estes problemas técnicos, mas se tiver, resolvê-los antes de tentar realizar a migração garantirá uma experiência mais tranquila.

- **Fazer uma Simulação Validar/Preparar/Anular** – essa é provavelmente a etapa mais importante para garantir o sucesso da migração do Clássico para o Azure Resource Manager. A API de migração apresenta três etapas principais: Valide, Prepare e Confirme. A etapa Validar lerá o estado do ambiente clássico e retornará um resultado de todos os problemas. No entanto, como talvez existam alguns problemas na pilha do Azure Resource Manager, a etapa Validar não capturará tudo. A próxima etapa no processo de migração, Preparar, ajudará a expor esses problemas. A etapa Preparar moverá os metadados do Clássico para o Azure Resource Manager, mas não confirmará a movimentação e não removerá nem alterará nada do lado do Clássico. A simulação envolve a preparação da migração e, em seguida, a anulação (**não confirmação**) da preparação da migração. O objetivo da simulação Validar/Preparar/Anular é ver todos os metadados da pilha do Azure Resource Manager, examiná-los (*de forma programática ou no Portal*), verificar se tudo é migrado corretamente e resolver os problemas técnicos.  Ela também lhe dará uma ideia da duração da migração, de forma que você possa planejar o tempo de inatividade de acordo.  Uma validação/preparação/anulação não causa nenhum tempo de inatividade para o usuário; portanto, ela é não interruptiva para o uso do aplicativo.
  - Os itens abaixo precisarão ser resolvidos antes da simulação, mas uma simulação liberará com segurança essas etapas de preparação caso elas não sejam executadas. Durante a migração corporativa, descobrimos que a simulação é uma maneira segura e inestimável para garantir a prontidão da migração.
  - Quando a etapa Preparar estiver em execução, o plano de controle (operações de gerenciamento do Azure) será bloqueado para toda a rede virtual e, portanto, nenhuma alteração poderá ser feita nos metadados da VM durante as etapas Validar/Preparar/Anular.  Mas, de outro modo, a função de qualquer aplicativo (RD, uso da VM, etc.) não será afetada.  Os usuários das VMs não saberão que a simulação está sendo executada.

- **Circuitos do ExpressRoute e VPN**. Atualmente, os Gateways do ExpressRoute com links de autorização não podem ser migrados sem tempo de inatividade. Para obter a solução desse problema, consulte [Migrar circuitos do ExpressRoute e as redes virtuais associadas do clássico para o modelo de implantação do Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **Extensões de VM** – as extensões de Máquina Virtual são possivelmente um dos maiores obstáculos para a migração de VMs em execução. A correção das Extensões de VM pode levar mais de 1 a 2 dias; portanto, planeje de forma adequada.  Um agente do Azure funcional é necessário para relatar novamente o status da Extensão de VM das VMs em execução. Se o status for retornado inválido para uma VM em execução, isso interromperá a migração. O próprio agente não precisa estar na ordem de trabalho para permitir a migração, mas se existirem extensões na VM, um agente funcional E uma conectividade de saída com a Internet (com DNS) serão necessários para continuar a migração.
  - Se a conexão a um servidor DNS for perdida durante a migração, todas as extensões de VM (exceto BGInfo v1.\*) deverão ser removidas primeira de cada VM antes da preparação da migração e subsequentemente adicionadas de volta à VM após a migração do Azure Resource Manager.  **Isso se refere apenas às VMs em execução.**  Se as VMs forem interrompidas desalocadas, as Extensões de VM não precisarão ser removidas. **Observação:** Várias extensões como o diagnóstico do Azure e o monitoramento da central de segurança serão reinstalados automaticamente após a migração e, portanto, removê-las não será um problema.
  - Além disso, verifique se os Grupos de Segurança de Rede não estão restringindo o acesso de saída à Internet. Isso pode acontecer com as configurações de alguns Grupos de Segurança de Rede. O acesso de saída à Internet (e o DNS) é necessário para que as Extensões de VM sejam migradas para o Azure Resource Manager. 
  - Há duas versões da extensão BGInfo: v1 e v2.  Se a VM foi criada usando o portal do Azure ou o PowerShell, provavelmente, ela terá a extensão v1. Essa extensão não precisa ser removida e será ignorada (não migrada) pela API de migração. No entanto, se a VM Clássica foi criada com o novo portal do Azure, provavelmente, ela terá a versão v2 baseada em JSON de BGInfo, que poderá ser migrada para o Azure Resource Manager, desde que o agente esteja funcionando e tenha acesso de saída à Internet (e DNS). 
  - **Opção de correção 1**. Se você souber que as VMs não terão acesso de saída à Internet, um serviço DNS funcional e agentes funcionais do Azure nas VMs, desinstale todas as extensões de VM como parte da migração antes da etapa Preparar e, depois, reinstale-as após a migração. 
  - **Opção de correção 2**. Se as extensões de VM forem muito problemáticas, outra opção é desligar/desalocar todas as VMs antes da migração. Migre as VMs desalocadas e, depois, reinicie-as no lado do Azure Resource Manager. A vantagem aqui é que as extensões de VM serão migradas. A desvantagem é que todos os IPs Virtuais voltados ao público serão perdidos (isso pode ser um não início), e, obviamente, as VMs serão desligada,s causando muito mais impacto nos aplicativos em funcionamento.

    > [!NOTE] 
    > Se uma política da Central de Segurança do Azure estiver configurada nas VMs em execução que estão sendo migradas, a política de segurança precisará ser interrompida antes da remoção das extensões; caso contrário, a extensão de monitoramento de segurança será reinstalada automaticamente na VM depois de removê-la.

- **Conjuntos de disponibilidade** – para que uma VNET (rede virtual) seja migrada para o Azure Resource Manager, todas as VMs recipientes da implantação Clássica (ou seja, o serviço de nuvem) devem estar em um único conjunto de disponibilidade ou não devem estar em nenhum conjunto de disponibilidade. Ter mais de um conjunto de disponibilidade no serviço de nuvem não é compatível com o Azure Resource Manager e interromperá a migração.  Além disso, não pode haver algumas VMs em um conjunto de disponibilidade e outras VMs que não estão em um conjunto de disponibilidade. Para resolver isso, você precisará corrigir ou reorganizar o serviço de nuvem.  Planeje de forma adequada, pois isso pode ser demorado. 

- **Implantações de função web/de trabalho** – Serviços de Nuvem que contém funções web e de trabalho não podem migrar para o Azure Resource Manager. As funções web/de trabalho devem primeiro ser removidas da rede virtual para que a migração possa ser iniciada.  Uma solução típica é apenas mover as instâncias de função web/de trabalho para uma rede virtual Clássica separada que também está vinculada a um circuito do ExpressRoute ou migrar o código para Serviços de Aplicativo de PaaS mais novos (essa discussão está além do escopo deste documento). No primeiro caso de reimplantação, crie uma nova rede virtual Clássica, mova/reimplante as funções web/de trabalho nessa nova rede virtual e, depois, exclua as implantações da rede virtual que está sendo movida. Não é necessário alterar o código. A nova funcionalidade [Emparelhamento de Rede Virtual](../../virtual-network/virtual-network-peering-overview.md) pode ser usada para emparelhar a rede virtual clássica que contém as funções web/de trabalho e outras redes virtuais na mesma região do Azure, como a rede virtual que está sendo migrada (**após a conclusão da migração da rede virtual, já que redes virtuais emparelhadas não podem ser migradas**), fornecendo as mesmas funcionalidades, sem perda de desempenho e sem penalidades de latência/largura de banda. Considerando a adição do [Emparelhamento de Rede Virtual](../../virtual-network/virtual-network-peering-overview.md), as implantações de função web/de trabalho agora podem ser facilmente atenuadas e não impedem a migração para o Azure Resource Manager.

- **Cotas do Azure Resource Manager** – as regiões do Azure têm cotas/limites separados para o Clássico e o Azure Resource Manager. Mesmo que em um cenário de migração o novo hardware não esteja sendo consumido *(estamos trocando VMs existentes do Clássico para o Azure Resource Manager)* , as cotas do Azure Resource Manager ainda precisam estar em vigor com capacidade suficiente antes que a migração possa ser iniciada. Veja abaixo uma lista dos principais limites que observamos que causam problemas.  Abra um tíquete de suporte de cota para aumentar os limites. 

    > [!NOTE]
    > Esses limites precisam ser acionados na mesma região do ambiente atual a ser migrado.
    >

  - Interfaces de Rede
  - Balanceadores de Carga
  - IPs Públicos
  - IPs Públicos Estáticos
  - Núcleos
  - Grupos de segurança de rede
  - Tabelas de Rotas

    Você pode verificar suas cotas atuais do Azure Resource Manager usando os comandos a seguir com a última versão da CLI do Azure.

    **Computação** *(Núcleos, Conjuntos de Disponibilidade)*

    ```bash
    az vm list-usage -l <azure-region> -o jsonc 
    ```

    **Rede** *(Redes Virtuais, IPs Públicos Estáticos, IPs Públicos, Grupos de Segurança de Rede, Interfaces de Rede, Balanceadores de Carga, Tabelas de Rotas)*
    
    ```bash
    az network list-usages -l <azure-region> -o jsonc
    ```

    **Armazenamento** *(Conta de Armazenamento)*
    
    ```bash
    az storage account show-usage
    ```

- **Limitação da API do Azure Resource Manager** – caso você tenha um ambiente grande o suficiente (por exemplo, > 400 VMs em uma VNET), poderá atingir a limitação padrão da API para gravações (atualmente, **1.200 gravações/hora**) no Azure Resource Manager. Antes de iniciar a migração, você deverá acionar um tíquete de suporte para aumentar esse limite para sua assinatura.

- **Status de VM O Provisionamento Atingiu o Tempo Limite** – se uma VM tiver o status **tempo limite do provisionamento atingido**, isso precisará ser resolvido antes da migração. A única maneira de fazer isso é com o tempo de inatividade e cancelando o provisionamento e reprovisionando a VM (excluí-la, manter o disco e recriar a VM). 

- **Status de VM RoleStateUnknown** – se a migração for interrompida devido a uma mensagem de erro **estado da função desconhecido**, inspecione a VM usando o portal e verifique se ela está em execução. Esse erro normalmente desaparecerá por si só (sem nenhuma correção necessária) após alguns minutos e, em geral, é um tipo transitório que costuma ser observado durante as operações **start**, **stop** e **restart** de uma Máquina Virtual. **Melhor prática:** tente fazer a migração novamente após alguns minutos. 

- **O Fabric Cluster não existe** – em alguns casos, determinadas VMs não podem ser migradas por vários motivos incomuns. Um desses casos conhecidos é se a VM foi criada recentemente (na última semana ou menos) e por acaso foi colocada em um cluster do Azure que ainda não está equipado para cargas de trabalho do Azure Resource Manager.  Você obterá um erro dizendo que **o cluster da malha não existe** e a VM não poderá ser migrada. Em geral, aguardar alguns resolverá esse problema específico, pois, em breve, o cluster habilitará o Azure Resource Manager. No entanto, uma solução alternativa imediata é `stop-deallocate` a VM e, em seguida, continuar com a migração e iniciar o backup da VM no Azure Resource Manager após a migração.

### <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

- Não use atalhos nem omita as migrações de simulação Validar/Preparar/Anular.
- A maioria, se não todos, os possíveis problemas ocorrerá durante as etapas Validar/Preparar/Anular.

## <a name="migration"></a>Migração

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e compensações

Agora você está pronto porque resolveu os problemas conhecidos no ambiente.

Para as migrações reais, talvez você deseje considerar:

1. Planejar e agendar a rede virtual (a menor unidade de migração) com o aumento de prioridade.  Fazer as redes virtuais simples primeiro e prosseguir com as redes virtuais mais complicadas.
2. A maioria dos clientes terá ambientes de produção e de não produção.  Agendar a produção por último.
3. **(OPCIONAL)** Agendar um tempo de inatividade de manutenção com uma grande quantidade de buffer caso ocorram problemas inesperados.
4. Comunicar-se e alinhar com as equipes de suporte em caso de problemas.

### <a name="patterns-of-success"></a>Padrões de sucesso

As diretrizes técnicas da seção Laboratório de teste acima devem ser consideradas e atenuadas antes de uma migração real.  Com testes adequados, a migração é, na verdade, um não evento.  Para ambientes de produção, pode ser útil ter suporte adicional, como um parceiro confiável da Microsoft ou os serviços do Microsoft Premier.

### <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

O teste parcial pode causar problemas e atraso na migração.  

## <a name="beyond-migration"></a>Além da migração

### <a name="technical-considerations-and-tradeoffs"></a>Considerações técnicas e compensações

Agora que você está no Azure Resource Manager, maximize a plataforma.  Leia a [visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) para descobrir mais benefícios.

Itens a serem considerados:

- Agrupe a migração com outras atividades.  A maioria dos clientes opta por uma janela de manutenção do aplicativo.  Nesse caso, talvez você deseje usar esse tempo de inatividade para habilitar outras funcionalidades do Azure Resource Manager como criptografia e migração para o Managed Disks.
- Examine os motivos técnicos e comerciais para o Azure Resource Manager; habilite os serviços adicionais disponíveis somente no Azure Resource Manager que se aplicam ao seu ambiente.
- Modernize seu ambiente com os serviços de PaaS.

### <a name="patterns-of-success"></a>Padrões de sucesso

Determine quais serviços você deseja habilitar no Azure Resource Manager agora.  Muitos clientes consideram os itens abaixo interessantes para seus ambientes do Azure:

- [Controle de Acesso Baseado em Função](../../role-based-access-control/overview.md).
- [Modelos do Azure Resource Manager para uma implantação mais fácil e mais controlada](../../azure-resource-manager/resource-group-overview.md#template-deployment).
- [Marcas](../../azure-resource-manager/resource-group-using-tags.md).
- [Controle de Atividades](../../azure-resource-manager/resource-group-audit.md)
- [Políticas do Azure](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

Lembre-se do motivo que fez você iniciar esta jornada de migração do Clássico para o Azure Resource Manager.  Quais foram os motivos comerciais originais? Você concretizou o motivo comercial?


## <a name="next-steps"></a>Próximas etapas

* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planejamento para a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas da comunidade para ajudar com a migração de recursos de IaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Examinar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Confira as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
