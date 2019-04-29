---
title: Visão geral do suporte de multilocatário para recuperação de desastre de VM do VMware no Azure (CSP) usando o Azure Site Recovery | Microsoft Docs
description: Apresenta uma visão geral do suporte do Azure Site Recovery para recuperação de desastres do VMWare para o Azure em um programa de ambiente multilocatário (CSP).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: d227b8d038dd686bde9b031ca2c58adc7dd6d76b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60718018"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Visão geral do suporte multilocatário para recuperação de desastre do VMware para o Azure com o CSP

O [Azure Site Recovery](site-recovery-overview.md) dá suporte a ambientes de multilocatário para assinaturas de locatário. Ele também dá suporte à multilocação para assinaturas de locatários que são criadas e gerenciadas por meio do programa CSP (Provedor de Soluções na Nuvem da Microsoft).

Este artigo fornece uma visão geral de implementação e gerenciamento da replicação do VMware no Azure de multilocatário.

## <a name="multi-tenant-environments"></a>Ambientes multilocatário

Há três modelos principais de multilocatários:

* **HSP (Provedor de Serviços de Hospedagem Compartilhado)**: O parceiro é o proprietário da infraestrutura física e usa recursos compartilhados (vCenter, datacenters, armazenamento físico, e assim por diante) para hospedar VMs de vários locatários na mesma infraestrutura. O parceiro pode fornecer gerenciamento de recuperação de desastre como um serviço gerenciado ou o locatário pode ter recuperação de desastre como uma solução de autoatendimento.

* **Provedor de Serviços de Hospedagem Dedicado**: O parceiro é proprietário da infraestrutura física, mas usa recursos dedicados (vários vCenters, armazenamentos de dados físicos, e assim por diante) para hospedar as VMs de cada locatário em uma infraestrutura separada. O parceiro pode fornecer gerenciamento de recuperação de desastre como um serviço gerenciado ou o locatário pode já tê-la como uma solução de autoatendimento.

* **MSP (Provedor de Serviços Gerenciado)**: O cliente possui a infraestrutura física que hospeda as VMs e o parceiro fornece a habilitação e gerenciamento de recuperação de desastre.

## <a name="shared-hosting-services-provider-hsp"></a>HSP (provedor de serviços de hospedagem compartilhada)

Os outros dois cenários são subconjuntos do cenário de hospedagem compartilhada e usam os mesmos princípios. As diferenças são descritas ao final da diretriz de hospedagem compartilhada.

O requisito básico em um cenário de multilocatário é que os locatários sejam isolados. Um locatário não deve ser capaz de observar o que é hospedado por outro locatário. Em um ambiente gerenciado por parceiros, esse requisito não é tão importante quanto em um ambiente de autoatendimento, no qual ele pode ser crucial. Este artigo pressupõe que o isolamento de locatários seja obrigatório.

A arquitetura é mostrada no diagrama a seguir.

![HSP compartilhado com um vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Hospedagem compartilhada com um servidor vCenter**

No diagrama, cada cliente tem um servidor de gerenciamento separado. Essa configuração limita o acesso do locatário a VMs específicas a um locatário e habilita o isolamento de locatários. A replicação de VM do VMware usa o servidor de configuração para descobrir VMs e instalar agentes. Os mesmos princípios se aplicam a ambientes de multilocatário, com a adição da restrição à descoberta de VM por meio do controle de acesso do vCenter.

O requisito de isolamento de dados significa que todas as informações confidenciais da infraestrutura (como credenciais de acesso) continuem não sendo divulgadas para os locatários. Por esse motivo, é recomendável que todos os componentes do servidor de gerenciamento permaneçam sob controle exclusivo do parceiro. Os componentes de servidor de gerenciamento são:

* Servidor de configuração
* Servidor de processo
* Servidor de destino mestre

Um servidor em processo dimensionado separado também está sob o controle do parceiro.

## <a name="configuration-server-accounts"></a>Contas do servidor de configuração

Cada servidor de configuração no cenário de multilocatário usa duas contas:

- **Conta de acesso do vCenter**: Essa conta é usada para descobrir VMs do locatário. Ela tem permissões de acesso do vCenter atribuídas a ela. Para ajudar a evitar perdas de acesso, recomendamos que os próprios parceiros insiram essas credenciais na ferramenta de configuração.

- **Conta de acesso à máquina virtual**: Essa conta é usada para instalar o agente do serviço de Mobilidade nas VMs do locatário, com um push automático. Ela é normalmente uma conta de domínio que um locatário pode fornecer a um parceiro ou uma conta que pode ser gerenciada diretamente pelo parceiro. Se um locatário não desejar compartilhar os detalhes diretamente com o parceiro, ele poderá inserir as credenciais por meio do acesso de tempo limitado no servidor de configuração. Ou, com a assistência do parceiro, ele poderá instalar o agente do serviço de Mobilidade manualmente.

## <a name="vcenter-account-requirements"></a>Requisitos de conta do vCenter

Configure o servidor de configuração com uma conta que tem uma função especial atribuída a ela.

- A atribuição de função deve ser aplicada à conta de acesso do vCenter para cada objeto do vCenter e não propagada para os objetos filho. Essa configuração garante o isolamento do locatário, pois a propagação de acesso também pode resultar em acesso acidental a outros objetos.

    ![A opção Propagar para Filho](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- A abordagem alternativa é atribuir a conta de usuário e a função no objeto de datacenter e propagá-las para os objetos filho. Em seguida, conceda à conta uma função **Sem acesso** para cada objeto (como as VMs que pertencem a outros locatários) que deve ficar inacessível a um locatário específico. Essa configuração é complicada. Ela expõe controles de acesso acidentais, pois cada novo objeto filho também recebe automaticamente o acesso herdado do pai. Portanto, é recomendável usar a primeira abordagem.

### <a name="create-a-vcenter-account"></a>Criar uma conta do vCenter

1. Crie uma nova função clonando a função *Somente leitura* predefinida e dê a ela um nome conveniente (como Azure_Site_Recovery, como mostrado neste exemplo).
2. Atribua as seguintes permissões a essa função:

   * **Armazenamento de dados**: Alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual
   * **Rede**: Atribuição de rede
   * **Recurso**: Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada
   * **Tarefas**: Criar tarefa, atualizar tarefa
   * **VM - Configuração**: Todos
   * **VM – Interação** > Responder a perguntas, Conexão do dispositivo, Configurar mídia de CD, Configurar mídia de disquete, Desligar, Ligar, Instalação de ferramentas do VMware
   * **VM – Inventário** > Criar com base em existente, Criar novo, Registrar, Cancelar registro
   * **VM – Provisionamento** > Permitir download de máquina virtual, Permitir upload de arquivos de máquina virtual
   * **VM – Gerenciamento de instantâneo** > Remover instantâneos

       ![A caixa de diálogo Editar Função](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Atribua os níveis de acesso à conta do vCenter (usada no servidor de configuração do locatário) a vários objetos, da seguinte maneira:

>| Objeto | Função | Comentários |
>| --- | --- | --- |
>| vCenter | Somente leitura | É necessário apenas para permitir o acesso do vCenter para gerenciar diferentes objetos. Você pode remover essa permissão se a conta nunca será fornecida a um locatário ou usada para operações de gerenciamento no vCenter. |
>| Datacenter | Azure_Site_Recovery |  |
>| Host e cluster de host | Azure_Site_Recovery | Verifique novamente se o acesso está no nível de objeto, de forma que somente os hosts acessíveis tenham VMs do locatário antes do failover e após o failback. |
>| Repositório de dados e cluster de repositório de dados | Azure_Site_Recovery | Mesmo que o anterior. |
>| Rede | Azure_Site_Recovery |  |
>| Servidor de gerenciamento | Azure_Site_Recovery | Inclui o acesso a todos os componentes (CS, PS e MT) fora do computador do CS. |
>| VMs do locatário | Azure_Site_Recovery | Verifique se as novas VMs de locatário de um locatário específico também recebem esse acesso ou elas não poderão ser descobertas por meio do Portal do Azure. |

O acesso à conta do vCenter está concluído. Essa etapa cumpre o requisito de permissões mínimas para concluir operações de failback. Você também pode usar essas permissões de acesso com as políticas existentes. Basta modificar seu conjunto de permissões existentes para incluir permissões de função da etapa 2, como detalhado anteriormente.

### <a name="failover-only"></a>Somente failover
Para restringir as operações de recuperação de desastre a somente failover (ou seja, sem as funcionalidades de failback), use o procedimento anterior, com estas exceções:

- Em vez de atribuir a função *Azure_Site_Recovery* à conta de acesso do vCenter, atribua apenas uma função *Somente Leitura* à essa conta. Esse conjunto de permissões permite failover e replicação de VM, mas não permite failback.
- Todo o resto do processo anterior permanece igual. Para garantir o isolamento do locatário e restringir a descoberta de VM, cada permissão ainda é atribuída apenas no nível do objeto e não propagada para os objetos filho.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Implantar recursos na assinatura do locatário

1. No Portal do Azure, crie um grupo de recursos e implante um cofre dos Serviços de Recuperação de acordo com o processo normal.
2. Baixe a chave do registro do cofre.
3. Registre o CS para o locatário usando a chave de registro do cofre.
4. Insira as credenciais para as duas contas de acesso, a conta para acessar o servidor vCenter e a conta para acessar a máquina virtual.

    ![Contas do servidor de configurações do gerenciador](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Registrar servidores no cofre

1. No portal do Azure, no cofre que você criou anteriormente, registre o servidor vCenter para o servidor de configuração, usando a conta do vCenter que você criou.
2. Conclua o processo de “Preparação da infraestrutura” para o Site Recovery de acordo com o processo normal.
3. Agora, as VMs estão prontas para serem replicadas. Verifique se apenas as VMs do locatário estão visíveis em **Replicar** > **Selecionar as máquinas virtuais**.

## <a name="dedicated-hosting-solution"></a>Solução de hospedagem dedicada

Como mostrado no diagrama a seguir, a diferença de arquitetura em uma solução de hospedagem dedicada é que a infraestrutura de cada locatário está configurada somente para esse locatário.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Cenário de hospedagem dedicada com vários vCenters**

## <a name="managed-service-solution"></a>Solução de serviço gerenciado

Conforme mostrado no diagrama a seguir, a diferença de arquitetura em uma solução de serviço gerenciado é que a infraestrutura de cada locatário também é fisicamente separada da infraestrutura de outros locatários. Esse cenário normalmente existe quando o locatário tem a infraestrutura e busca um provedor de solução para gerenciar a recuperação de desastre.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Cenário de serviço gerenciado com vários vCenters**

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais](site-recovery-role-based-linked-access-control.md) sobre o controle de acesso baseado em função no Site Recovery.
- Saiba como [configurar a recuperação de desastre de VMs do VMware para o Azure](vmware-azure-tutorial.md).
- Saiba mais sobre [multilocação com CSP para VMs VMWare](vmware-azure-multi-tenant-csp-disaster-recovery.md).
