---
title: "Suporte para vários locatários para replicação de VM do VMware no Azure (programa CSP) | Microsoft Docs"
description: "Descreve como implantar o Azure Site Recovery em um ambiente de multilocatário para orquestrar a replicação, o failover e a recuperação de VMs (máquinas virtuais) VMware locais no Azure por meio do Programa CSP usando o Portal do Azure"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: manayar
ms.openlocfilehash: 97edbe67c25036dc1156f0f0ca5431a617d7a004
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Suporte a multilocatários no Azure Site Recovery para replicar máquinas virtuais do VMware no Azure por meio do CSP

O Azure Site Recovery oferece suporte a ambientes multilocatários para assinaturas de locatários. Ele também dá suporte à multilocação para assinaturas de locatários que são criadas e gerenciadas por meio do programa CSP (Provedor de Soluções na Nuvem da Microsoft). Este artigo detalha as diretrizes para implementar e gerenciar cenários multilocatários do VMware para o Azure. Ele também aborda a criação e o gerenciamento de assinaturas de locatários por meio do CSP.

Essa diretriz é amplamente na documentação existente de replicação de máquinas virtuais VMware para o Azure. Para obter mais informações, consulte [Replicar máquinas virtuais VMware para o Azure com o Site Recovery](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Ambientes multilocatário
Há três modelos principais de multilocatários:

* **HSP (Provedor de Serviços de Hospedagem) Compartilhado**: o parceiro tem a infraestrutura física e usa os recursos compartilhados (vCenter, datacenters, armazenamento físico e assim por diante) para hospedar VMs de vários locatários na mesma infraestrutura. O parceiro pode fornecer gerenciamento de recuperação de desastre como um serviço gerenciado ou o locatário pode ter recuperação de desastre como uma solução de autoatendimento.

* **Provedor de Serviços de Hospedagem Dedicado**: o parceiro tem a infraestrutura física, mas usa recursos dedicados (vários vCenters, repositórios de dados físicos e assim por diante) para hospedar VMs de cada locatário em uma infraestrutura separada. O parceiro pode fornecer gerenciamento de recuperação de desastre como um serviço gerenciado ou o locatário pode já tê-la como uma solução de autoatendimento.

* **MSP (Provedor de Serviços Gerenciado)**: o cliente tem a infraestrutura física que hospeda as VMs e o parceiro fornece a habilitação e gerenciamento de recuperação de desastre.

## <a name="shared-hosting-multi-tenant-guidance"></a>Diretriz de multilocatário de hospedagem compartilhada
Esta seção aborda com detalhes o cenário de hospedagem compartilhada. Os outros dois cenários são subconjuntos do cenário de hospedagem compartilhada e usam os mesmos princípios. As diferenças são descritas ao final da diretriz de hospedagem compartilhada.

O requisito básico em um cenário multilocatário é isolar os vários locatários. Um locatário não deve ser capaz de observar o que é hospedado por outro locatário. Em um ambiente gerenciado por parceiros, esse requisito não é tão importante quanto em um ambiente de autoatendimento, no qual ele pode ser crucial. Este guia presume que o isolamento de locatários é necessário.

A arquitetura é apresentada no diagrama a seguir:

![HSP compartilhado com um vCenter](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**Cenário de hospedagem compartilhada com um vCenter**

Conforme visto no diagrama anterior, cada cliente tem um servidor de gerenciamento separado. Essa configuração limita o acesso do locatário a VMs de locatários específicos e habilita o isolamento de locatários. Um cenário de replicação de máquina virtual do VMware usa o servidor de configuração para gerenciar contas a fim de descobrir VMs e instalar agentes. Seguimos os mesmos princípios para ambientes multilocatários, mas acrescentando a restrição à descoberta de VM por meio do controle de acesso do vCenter.

O requisito de isolamento de dados precisa que todas as informações confidenciais de infraestrutura (como credenciais de acesso) continuem não sendo reveladas para os locatários. Por esse motivo, é recomendável que todos os componentes do servidor de gerenciamento permaneçam sob controle exclusivo do parceiro. Os componentes de servidor de gerenciamento são:
* CS (Servidor de configuração)
* PS (Servidor de processo)
* MT (Servidor de destino mestre) 

Um PS de expansão também está sob controle do parceiro.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>Cada CS no cenário de multilocatários usa duas contas

- **Conta de acesso do vCenter**: use essa conta para descobrir VMs do locatário. Ela recebe a atribuição de permissões de acesso do vCenter (conforme descrito na próxima seção). Para ajudar a evitar vazamentos de acesso, é recomendável que os próprios parceiros insiram essas credenciais na ferramenta de configuração.

- **Conta de acesso à máquina virtual**: use essa conta para instalar o agente de mobilidade em VMs do locatário por meio de envio por push automático. Essa normalmente é uma conta de domínio que um locatário pode fornecer ao parceiro ou que, como alternativa, pode ser gerenciada diretamente pelo parceiro. Se o locatário não quiser compartilhar os detalhes diretamente com o parceiro, ele poderá receber permissão para inserir as credenciais por meio de um acesso por tempo limitado ao CS ou, com a assistência do parceiro, instalar agentes de mobilidade manualmente.

### <a name="requirements-for-a-vcenter-access-account"></a>Requisitos para uma conta de acesso do vCenter

Conforme mencionado na seção anterior, você deve configurar o CS com uma conta atribuída com uma função especial. A atribuição de função deve ser aplicada à conta de acesso do vCenter para cada objeto do vCenter e não propagada para os objetos filhos. Essa configuração garante o isolamento do locatário, pois a propagação de acesso também pode resultar em acesso acidental a outros objetos.

![A opção Propagar para Filho](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

A alternativa é atribuir a conta de usuário e a função no objeto do datacenter e propagá-las para os objetos filhos. Em seguida, conceda à conta uma função *Sem acesso* para cada objeto (como VMs de outros locatários) que deve ficar inacessíveis para um locatário específico. Essa configuração é trabalhosa e expõe controles de acesso acidentais, pois cada novo objeto filho criado também recebe automaticamente o acesso herdado do pai. Portanto, é recomendável usar a primeira abordagem.

O procedimento de acesso da conta do vCenter é o seguinte:

1. Crie uma nova função clonando a função *Somente leitura* predefinida e dê a ela um nome conveniente (como Azure_Site_Recovery usado neste exemplo).

2. Atribua as seguintes permissões a essa função:

    * **Repositório de dados**: Alocar espaço, Procurar repositório de dados, Operações de arquivo de baixo nível, Remover arquivo, Atualizar arquivos de máquina virtual

    * **Rede**: Atribuição de rede

    * **Recurso**: Atribuir VM ao pool de recursos, Migrar VM desligada, Migrar VM ligada

    * **Tarefas**: Criar tarefa, Atualizar tarefa

    * **Máquina virtual**: 
        * Configuração > todas
        * Interação > Responder perguntas, Conexão do dispositivo, Configurar mídia de CD, Configurar mídia de disquete, Desligar, Ligar, Instalação de ferramentas VMware
        * Inventário > Criar com base em existente, Criar novo, Registrar, Cancelar registro
        * Provisionamento > Permitir download de máquina virtual, Permitir upload de arquivos de máquina virtual
        * Gerenciamento de instantâneo > Remover instantâneos

    ![A caixa de diálogo Editar Função](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3. Atribua os níveis de acesso à conta do vCenter (usada no CS do locatário) a diferentes objetos da seguinte maneira:

>| Objeto | Função | Comentários |
>| --- | --- | --- |
>| vCenter | Somente leitura | É necessário apenas para permitir o acesso do vCenter para gerenciar diferentes objetos. Você pode remover essa permissão se a conta nunca será fornecida a um locatário ou usada para operações de gerenciamento no vCenter. |
>| Datacenter | Azure_Site_Recovery |  |
>| Host e cluster de host | Azure_Site_Recovery | Verifique novamente se o acesso está no nível de objeto, de forma que somente os hosts acessíveis tenham VMs do locatário antes do failover e após o failback. |
>| Repositório de dados e cluster de repositório de dados | Azure_Site_Recovery | Mesmo que o anterior. |
>| Rede | Azure_Site_Recovery |  |
>| Servidor de gerenciamento | Azure_Site_Recovery | Inclui o acesso a todos os componentes (CS, PS e MT) se algum estiver fora do computador do CS. |
>| VMs do locatário | Azure_Site_Recovery | Verifique se as novas VMs de locatário de um locatário específico também recebem esse acesso ou elas não poderão ser descobertas por meio do Portal do Azure. |

O acesso à conta do vCenter está concluído. Essa etapa cumpre o requisito de permissões mínimas para concluir operações de failback. Você também pode usar essas permissões de acesso com as políticas existentes. Basta modificar seu conjunto de permissões existentes para incluir permissões de função da etapa 2, como detalhado anteriormente.

Para restringir as operações de recuperação de desastre até o estado de failover (ou seja, sem os recursos de failback), siga o procedimento anterior, com uma exceção: em vez de atribuir a função *Azure_Site_Recovery* à conta de acesso do vCenter, atribua apenas uma função *Somente Leitura* a essa conta. Esse conjunto de permissões permite failover e replicação de VM, mas não permite failback. Todo o resto do processo anterior permanece igual. Para garantir o isolamento do locatário e a restringir a descoberta de VM, cada permissão ainda é atribuída apenas no nível do objeto e não propagada aos objetos filhos.

## <a name="other-multi-tenant-environments"></a>Outros ambientes multilocatários

A seção anterior descreveu detalhadamente como configurar um ambiente multilocatário para uma solução de hospedagem compartilhada. As outras duas soluções principais são dedicadas à hospedagem e ao serviço gerenciado. A arquitetura dessas soluções é descrita nas seções a seguir.

### <a name="dedicated-hosting-solution"></a>Solução de hospedagem dedicada

Como mostrado no diagrama a seguir, a diferença de arquitetura em uma solução de hospedagem dedicada é que a infraestrutura de cada locatário está configurada somente para esse locatário. Como os locatários são isolados por meio de vCenters separados, o provedor de hospedagem ainda precisa seguir as etapas de CSP fornecidas para hospedagem compartilhada, mas não precisa se preocupar com o isolamento do locatário. A configuração do CSP permanece inalterada.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)  
**Cenário de hospedagem dedicada com vários vCenters**

### <a name="managed-service-solution"></a>Solução de serviço gerenciado

Conforme mostrado no diagrama a seguir, a diferença de arquitetura em uma solução de serviço gerenciado é que a infraestrutura de cada locatário também é fisicamente separada da infraestrutura de outros locatários. Esse cenário normalmente existe quando o locatário tem a infraestrutura e busca um provedor de solução para gerenciar a recuperação de desastre. Novamente, como os locatários são isolados fisicamente por meio de infraestruturas diferentes, o parceiro ainda precisa seguir as etapas de CSP fornecidas para hospedagem compartilhada, mas não precisa se preocupar com o isolamento do locatário. O provisionamento de CSP permanece inalterado.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)  
**Cenário de serviço gerenciado com vários vCenters**

## <a name="csp-program-overview"></a>Visão geral do programa CSP
O [Programa CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) promove histórias de colaboração de sucesso que proporcionam aos parceiros todos os serviços em nuvem da Microsoft, incluindo o Office 365, Enterprise Mobility Suite e Microsoft Azure. Com o CSP, nossos parceiros controlem a relação ponta a ponta com os clientes e se tornem o principal ponto de contato principal desse relacionamento. Os parceiros pode implantar as assinaturas do Azure para os clientes e combiná-las com suas próprias ofertas personalizadas de valor agregado.

Com o Azure Site Recovery, os parceiros podem gerenciar a solução completa de Recuperação de Desastre para os clientes diretamente por meio de do CSP. Ou então, eles podem usar o CSP para configurar ambientes de Site Recovery e permitir que os clientes gerenciem suas próprias necessidades de recuperação de desastre como autoatendimento. Em ambos os cenários, os parceiros são o elo entre o Site Recovery e seus clientes. Os parceiros cuidam do relacionamento com o cliente e cobram pelo uso do Site Recovery.

## <a name="create-and-manage-tenant-accounts"></a>Criar e gerenciar contas de locatário

### <a name="step-0-prerequisite-check"></a>Etapa 0: Verificação de pré-requisitos

Os pré-requisitos de VM são os mesmos descritos na [documentação do Azure Site Recovery](site-recovery-vmware-to-azure.md). Além desses pré-requisitos, você precisa ter os controles de acesso mencionados anteriormente em vigor antes de prosseguir com o gerenciamento de locatário por meio do CSP. Para cada locatário, crie um servidor de gerenciamento separado que possa se comunicar com as VMs do locatário e com o vCenter do parceiro. Somente o parceiro tem direitos de acesso a esse servidor.

### <a name="step-1-create-a-tenant-account"></a>Etapa 1: criar uma conta de locatário

1. Entre na sua conta do CSP por meio do [Microsoft Partner Center](https://partnercenter.microsoft.com/). 
 
2. No menu **Painel**, selecione **Clientes**.

    ![O link de clientes do Microsoft Partner Center](./media/site-recovery-multi-tenant-support-vmware-using-csp/csp-dashboard-display.png)

3. Na página que se abre, clique no botão **Adicionar cliente**.

    ![O botão Adicionar Cliente](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-new-customer.png)

4. Na página **Novo Cliente**, preencha todos os detalhes de informações da conta para o locatário e clique em **Avançar: Assinaturas**.

    ![A página Informações da Conta](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-add-filled.png)

5. Na página de seleção de assinaturas, marque a caixa de seleção **Microsoft Azure**. Você pode adicionar outras assinaturas agora ou a qualquer momento.

    ![A caixa de seleção da assinatura do Microsoft Azure](./media/site-recovery-multi-tenant-support-vmware-using-csp/azure-subscription-selection.png)

6. Na página **Revisão**, confirme os detalhes do locatário e clique em **Enviar**.

    ![A página Revisão](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-summary-page.png)  

    Depois de criar a conta do locatário, uma página de confirmação é mostrada exibindo os detalhes da conta e a senha padrão para essa assinatura. 

7. Salve as informações e altere a senha mais tarde, conforme necessário, na página de Logon do Portal do Azure.  
 
    Você pode compartilhar essas informações dessa forma com o locatário ou criar e compartilhar uma conta separada, se necessário.

### <a name="step-2-access-the-tenant-account"></a>Etapa 2: acessar a conta de locatário

Você pode acessar a assinatura do locatário por meio do Painel do Microsoft Partner Center, conforme descrito em “Etapa 1: criar uma conta de locatário”. 

1. Acesse a página **Clientes** e clique no nome da conta de locatário.

2. Na página **Assinaturas** da conta do locatário, você pode monitorar as assinaturas existentes da conta e adicionar mais assinaturas conforme necessário. Para gerenciar operações de recuperação de desastre do locatário, selecione **Todos os recursos (Portal do Azure)**.

    ![O link Todos os Recursos](./media/site-recovery-multi-tenant-support-vmware-using-csp/all-resources-select.png)  
    
    Clicar em **Todos os recursos** concede a você o acesso às assinaturas do Azure do locatário. Você pode verificar o acesso clicando no link do Azure Active Directory na parte superior direita do Portal do Azure.

    ![Link do Azure Active Directory](./media/site-recovery-multi-tenant-support-vmware-using-csp/aad-admin-display.png)

Agora você pode executar todas as operações de recuperação de site para o locatário por meio do Portal do Azure e gerenciar as operações de recuperação de desastre. Para acessar a assinatura do locatário por meio de CSP para recuperação de desastre gerenciada, siga o processo descrito anteriormente.

### <a name="step-3-deploy-resources-to-the-tenant-subscription"></a>Etapa 3: implantar recursos na assinatura do locatário
1. No Portal do Azure, crie um grupo de recursos e implante um cofre dos Serviços de Recuperação de acordo com o processo normal. 
 
2. Baixe a chave do registro do cofre.

3. Registre o CS para o locatário usando a chave de registro do cofre.

4. Insira as credenciais das duas contas de acesso: a conta de acesso do vCenter e a conta de acesso da VM.

    ![Contas do servidor de configurações do gerenciador](./media/site-recovery-multi-tenant-support-vmware-using-csp/config-server-account-display.png)

### <a name="step-4-register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Etapa 4: registrar a infraestrutura do Site Recovery no cofre dos Serviços de Recuperação
1. No Portal do Azure, no cofre que você criou anteriormente, registre o servidor do vCenter para o CS que você registrou na “Etapa 3: implantar recursos para a assinatura do locatário”. Use a conta de acesso do vCenter para essa finalidade.
2. Conclua o processo de “Preparação da infraestrutura” para o Site Recovery de acordo com o processo normal.
3. Agora, as VMs estão prontas para serem replicadas. Verifique se apenas as VMs do locatário estão visíveis na folha **Selecione as máquinas virtuais** na opção **Replicar**.

    ![Lista de VMs do locatário na folha Selecione as máquinas virtuais](./media/site-recovery-multi-tenant-support-vmware-using-csp/tenant-vm-display.png)

### <a name="step-5-assign-tenant-access-to-the-subscription"></a>Etapa 5: atribuir acesso do locatário à assinatura

Para recuperação de desastre de autoatendimento, forneça os detalhes da conta ao locatário, conforme mencionado no passo 6 da seção “Etapa 1: criar uma conta de locatário”. Execute esta ação depois que o parceiro configurar a infraestrutura de recuperação de desastre. Se o tipo de recuperação de desastre for gerenciado ou autoatendimento, os parceiros precisarão acessar as assinaturas de locatário por meio do portal do CSP. Eles configuram o cofre do parceiro e registram a infraestrutura para as assinaturas do locatário.

Os parceiros também podem adicionar um novo usuário à assinatura do locatário por meio do portal do CSP da seguinte maneira:

1. Acesse a página da assinatura do CSP do locatário e selecione a opção **Usuários e licenças**.

    ![A página da assinatura do CSP do locatário](./media/site-recovery-multi-tenant-support-vmware-using-csp/users-and-licences.png)

    Agora você pode criar um novo usuário inserindo os detalhes relevantes e selecionando as permissões ou carregando a lista de usuários em um arquivo CSV.

2. Depois de criar um novo usuário, volte para o Portal do Azure e, na folha **Assinatura**, selecione a assinatura relevante.

3. Na nova folha que se abre, selecione **IAM (Controle de Acesso)** e clique em **Adicionar** para adicionar um usuário com o nível de acesso relevante.      
    Os usuários criados por meio do Portal do CSP são exibidos automaticamente na folha que é aberta após você clicar em um nível de acesso.

    ![Adicionar um usuário](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-user-subscription.png)

    Para a maioria das operações de gerenciamento, a função de *Colaborador* é suficiente. Usuários com esse nível de acesso podem fazer tudo em uma assinatura, exceto alterar os níveis de acesso (para tal é necessário o nível de acesso *Proprietário*). Você também pode ajustar os níveis de acesso conforme o necessário.
