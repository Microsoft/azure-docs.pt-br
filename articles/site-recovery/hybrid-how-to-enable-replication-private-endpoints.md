---
title: Habilitar a replicação para computadores locais com pontos de extremidade privados
description: Este artigo descreve como configurar a replicação para computadores locais usando pontos de extremidade privados no Site Recovery.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 7f10654e1c96f1756e5864d20fa2a6817385e994
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629786"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>Replicar computadores locais usando pontos de extremidade privados

Azure Site Recovery permite que você use pontos de extremidade particulares [do link privado do Azure](../private-link/private-endpoint-overview.md) para replicar seus computadores locais para uma rede virtual no Azure. O acesso de ponto de extremidade privado a um cofre de recuperação tem suporte em todas as regiões do Azure Commercial & governamental.

Este artigo descreve como concluir as etapas a seguir:

- Crie um cofre dos serviços de recuperação de backup do Azure para proteger seus computadores.
- Habilite uma identidade gerenciada para o cofre. Conceda as permissões necessárias para acessar as contas de armazenamento para habilitar a replicação de tráfego do local para os locais de destino do Azure. O acesso de identidade gerenciada para armazenamento é necessário para acesso de link privado para o cofre.

- Faça alterações de DNS necessárias para pontos de extremidade privados.
- Criar e aprovar pontos de extremidade privados para um cofre dentro de uma rede virtual.
- Crie pontos de extremidade privados para as contas de armazenamento. Você pode continuar a permitir o acesso público ou de firewall para armazenamento, conforme necessário. A criação de um ponto de extremidade privado para acessar o armazenamento não é necessária para Azure Site Recovery.
  
O diagrama a seguir mostra o fluxo de trabalho de replicação para recuperação de desastre híbrida com pontos de extremidade privados. Você não pode criar pontos de extremidade privados em sua rede local. Para usar links privados, você precisa criar uma rede virtual do Azure (chamada de *rede de bypass* neste artigo), estabelecer conectividade privada entre o local e a rede de bypass e, em seguida, criar pontos de extremidade privados na rede de bypass. Você pode escolher qualquer forma de conectividade privada.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Diagrama que mostra a arquitetura para pontos de extremidade Azure Site Recovery e privados.":::

## <a name="prerequisites-and-caveats"></a>Pré-requisitos e limitações

- Há suporte para links privados no Site Recovery 9,35 e posterior.
- Você pode criar pontos de extremidade privados somente para novos cofres de serviços de recuperação que não têm nenhum item registrado neles. Portanto, você deve criar pontos de extremidade privados antes que qualquer item seja adicionado ao cofre. Consulte [preços do link privado do Azure](https://azure.microsoft.com/pricing/details/private-link/) para obter informações sobre preços.
- Quando você cria um ponto de extremidade privado para um cofre, o cofre é bloqueado. Ele pode ser acessado somente de redes que têm pontos de extremidade privados.
- No momento, o Azure Active Directory não dá suporte a pontos de extremidade privados. Portanto, você precisa permitir o acesso de saída da rede virtual do Azure protegida para IPs e nomes de domínio totalmente qualificados que são necessários para que Azure Active Directory funcionem em uma região.
  Conforme aplicável, você também pode usar a marca do grupo de segurança de rede "Azure Active Directory" e as marcas de firewall do Azure para permitir o acesso a Azure Active Directory.
- São necessários cinco endereços IP na rede de bypass onde você cria seu ponto de extremidade privado. Quando você cria um ponto de extremidade privado para o cofre, Site Recovery cria cinco links privados para acessar seus microserviços.
- Um endereço IP adicional é necessário na rede de bypass para conectividade de ponto de extremidade privado para uma conta de armazenamento de cache. Você pode usar qualquer método de conectividade entre o local e o ponto de extremidade da conta de armazenamento. Por exemplo, você pode usar a Internet ou o Azure [ExpressRoute](../expressroute/index.yml). O estabelecimento de um link privado é opcional. Você pode criar pontos de extremidade privados para armazenamento somente em contas Uso Geral v2. Consulte [preços de blobs de páginas do Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) para obter informações sobre preços para transferência de dados em contas uso geral v2.

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>Criar e usar pontos de extremidade privados para o site Recovery

 As seções a seguir descrevem as etapas que você precisa executar para criar e usar pontos de extremidade privados para o site Recovery em suas redes virtuais.

> [!NOTE]
> Recomendamos que você siga estas etapas na ordem mostrada. Caso contrário, talvez você não consiga usar pontos de extremidade privados no cofre, e talvez seja necessário reiniciar o processo com um novo cofre.

### <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um cofre dos serviços de recuperação contém as informações de replicação das máquinas. Ele é usado para disparar operações de Site Recovery. Para obter informações sobre como criar um cofre dos serviços de recuperação na região do Azure onde você deseja fazer failover se houver um desastre, consulte [criar um cofre dos serviços de recuperação](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

### <a name="enable-the-managed-identity-for-the-vault"></a>Habilitar a identidade gerenciada para o cofre

Uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) permite que o cofre acesse suas contas de armazenamento. Site Recovery pode precisar acessar o armazenamento de destino e as contas de armazenamento de cache/log, dependendo de seus requisitos. O acesso de identidade gerenciada é necessário quando você está usando o serviço de vínculo privado para o cofre.

1. Vá para o cofre dos serviços de recuperação. Selecione **identidade** em **configurações**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Captura de tela que mostra a página de configurações de identidade.":::

1. Altere o **status** para **ativado** e selecione **salvar**.

   Uma ID de objeto é gerada. O cofre agora está registrado com Azure Active Directory.

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Criar pontos de extremidade privados para o cofre dos serviços de recuperação

Para proteger os computadores na rede de origem local, você precisará de um ponto de extremidade privado para o cofre na rede de bypass. Crie o ponto de extremidade privado usando o centro de vínculo privado no portal do Azure ou usando [Azure PowerShell](../private-link/create-private-endpoint-powershell.md).

1. Na caixa de pesquisa portal do Azure, pesquise "link privado". Selecione **link privado** para ir para o centro de link privado:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Captura de tela que mostra a pesquisa do portal do Azure para o centro de links privado.":::

1. No painel esquerdo, selecione **pontos de extremidade privados**. Na página **pontos** de extremidade particulares, selecione **Adicionar** para começar a criar um ponto de extremidade privado para seu cofre:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Captura de tela que mostra como criar um ponto de extremidade privado no centro de vínculo privado.":::

1. Na página **criar um ponto de extremidade privado** , especifique os detalhes para criar sua conexão de ponto de extremidade privada.

   1. **Noções básicas**. Forneça os detalhes básicos para seus pontos de extremidade privados. Use a região que você usou para a rede de bypass:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Captura de tela que mostra a guia básica para criar um ponto de extremidade privado.":::

   1. **Recurso**. Nessa guia, você precisa especificar o recurso de plataforma como serviço para o qual deseja criar a conexão. Em **tipo de recurso** para sua assinatura selecionada, selecione **Microsoft. recoveryservices/cofres**. Escolha o nome do cofre dos serviços de recuperação em **recurso**. Selecione **Azure site Recovery** como o **subrecurso de destino**.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Captura de tela que mostra a guia de recursos para vinculação a um ponto de extremidade privado.":::

   1. **Configuração**. Nessa guia, especifique a rede e a sub-rede de bypass onde você deseja que o ponto de extremidade privado seja criado. 

      Habilite a integração com uma zona DNS privada selecionando **Sim**.
      Escolha uma zona DNS existente ou crie uma nova. Selecionar **Sim** vincula automaticamente a zona à rede de bypass. Essa ação também adiciona os registros DNS necessários para a resolução DNS de novos IPs e nomes de domínio totalmente qualificados criados para o ponto de extremidade privado.

      Certifique-se de optar por criar uma nova zona DNS para cada novo ponto de extremidade privado conectando-se ao mesmo cofre. Se você escolher uma zona DNS privada existente, os registros CNAME anteriores serão substituídos. Consulte [orientação do ponto de extremidade particular](../private-link/private-endpoint-overview.md#private-endpoint-properties) antes de continuar.

      Se o seu ambiente tiver um modelo Hub e spoke, você precisará apenas de um ponto de extremidade privado e apenas uma zona DNS privada para a configuração inteira. Isso ocorre porque todas as suas redes virtuais já têm o emparelhamento habilitado entre elas. Para obter mais informações, consulte [integração de DNS do ponto de extremidade privado](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Para criar manualmente a zona DNS privada, siga as etapas em [criar zonas DNS privadas e adicionar registros DNS manualmente](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Captura de tela que mostra a guia configuração para configuração de um ponto de extremidade privado.":::

   1. **Marcas**. Opcionalmente, você pode adicionar marcas para seu ponto de extremidade particular.

   1. **Examine \+ criar**. Quando a validação for concluída, selecione **criar** para criar o ponto de extremidade privado.

Quando o ponto de extremidade privado é criado, cinco FQDNs (nomes de domínio totalmente qualificados) são adicionados ao ponto de extremidade privado. Esses links permitem que os computadores na rede local acessem, por meio da rede de bypass, todos os microserviços necessários Site Recovery no contexto do cofre. Você pode usar o mesmo ponto de extremidade privado para a proteção de qualquer computador do Azure na rede de bypass e todas as redes emparelhadas.

Os cinco nomes de domínio são formatados neste padrão:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>Aprovar pontos de extremidade privados para o site Recovery

Se você criar o ponto de extremidade privado e também for o proprietário do cofre dos serviços de recuperação, o ponto de extremidade privado criado anteriormente será automaticamente aprovado em alguns minutos. Caso contrário, o proprietário do cofre deve aprovar o ponto de extremidade privado antes que você possa usá-lo. Para aprovar ou rejeitar uma conexão de ponto de extremidade privada solicitada, acesse **conexões de ponto de extremidade privado** em **configurações** na página cofre de recuperação.

Você pode ir para o recurso de ponto de extremidade privado para examinar o status da conexão antes de continuar:

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Captura de tela que mostra a página conexões de ponto de extremidade privada do cofre e a lista de conexões.":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Adicional Criar pontos de extremidade privados para a conta de armazenamento de cache

Você pode usar um ponto de extremidade privado para o armazenamento do Azure. A criação de pontos de extremidade privados para acesso de armazenamento é opcional para replicação de Azure Site Recovery. Se você criar um ponto de extremidade privado para armazenamento, precisará de um ponto de extremidade privado para a conta de armazenamento de log/cache em sua rede virtual de bypass.

> [!NOTE]
> Se os pontos de extremidade privados não estiverem habilitados na conta de armazenamento, a proteção ainda será bem-sucedida. No entanto, o tráfego de replicação funcionaria via Internet para Azure Site Recovery pontos de extremidade públicos. Para garantir que os fluxos de tráfego de replicação por meio de links privados, a conta de armazenamento deve ser habilitada com pontos de extremidade privados.

> [!NOTE]
> Pontos de extremidade privados para armazenamento podem ser criados somente em contas de armazenamento Uso Geral v2. Para obter informações sobre preços, consulte [preços de blobs de páginas do Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Siga as [diretrizes para criar o armazenamento privado](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) para criar uma conta de armazenamento com um ponto de extremidade privado. Certifique-se de selecionar **Sim** em **integrar com a zona DNS privada**. Selecione uma zona DNS existente ou crie uma nova.

### <a name="grant-required-permissions-to-the-vault"></a>Conceder as permissões necessárias ao cofre

Dependendo da sua configuração, talvez você precise de uma ou mais contas de armazenamento na região do Azure de destino. Em seguida, conceda as permissões de identidade gerenciadas para todas as contas de armazenamento de cache/log exigidas pelo Site Recovery. Nesse caso, você deve criar as contas de armazenamento necessárias com antecedência.

Antes de habilitar a replicação de máquinas virtuais, a identidade gerenciada do cofre deve ter as seguintes permissões de função, dependendo do tipo de conta de armazenamento.

- Contas de armazenamento baseadas no Resource Manager (tipo padrão):
  - [Colaborador](../role-based-access-control/built-in-roles.md#contributor)
  - [Colaborador de dados de blob de armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Contas de armazenamento baseadas no Gerenciador de recursos (tipo Premium):
  - [Colaborador](../role-based-access-control/built-in-roles.md#contributor)
  - [Proprietário de Dados do Blob de Armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Contas de armazenamento clássicas:
  - [Colaborador da conta de armazenamento clássica](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Função do Serviço de Operador da Chave da Conta de Armazenamento Clássica](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

Estas etapas descrevem como adicionar uma atribuição de função à sua conta de armazenamento:

1. Acesse a conta de armazenamento. Selecione **controle de acesso (iam)** no painel esquerdo.

1. Na seção **Adicionar uma atribuição de função** , selecione **Adicionar**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Captura de tela que mostra a página de controle de acesso (IAM) para uma conta de armazenamento.":::

1. Na página **Adicionar uma atribuição de função** , na lista **função** , selecione a função na lista no início desta seção. Insira o nome do cofre e, em seguida, selecione **salvar**.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Captura de tela que mostra a página Adicionar atribuição de função.":::

Depois de adicionar essas permissões, você precisará permitir o acesso aos serviços confiáveis da Microsoft. Vá para **firewalls e redes virtuais** e selecione **permitir que os serviços confiáveis da Microsoft acessem essa conta de armazenamento** em **exceções**.

### <a name="protect-your-virtual-machines"></a>Proteger suas máquinas virtuais

Depois de concluir as tarefas anteriores, continue com a configuração da sua infraestrutura local. Continue concluindo uma das seguintes tarefas: 

- [Implantar um servidor de configuração para VMware e computadores físicos](./vmware-azure-deploy-configuration-server.md)
- [Configurar o ambiente do Hyper-V para replicação](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

Após a conclusão da instalação, habilite a replicação para seus computadores de origem. Não configure a infraestrutura até que os pontos de extremidade privados para o cofre sejam criados na rede de bypass.

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Criar zonas DNS privadas e adicionar registros DNS manualmente

Se você não selecionou a opção de integração com uma zona DNS privada quando criou o ponto de extremidade privado para o cofre, siga as etapas nesta seção.

Crie uma zona DNS privada para permitir que o provedor de Site Recovery (para computadores Hyper-V) ou o servidor de processo (para VMware/máquinas físicas) resolva FQDNs privados para IPs privados.

1. Crie uma zona DNS privada.

   1. Pesquise "zona DNS privada" na caixa de pesquisa **todos os serviços** e, em seguida, selecione **DNS privado zona** nos resultados:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Captura de tela que mostra a pesquisa de zona DNS privada na página novos recursos no portal do Azure.":::

   1. Na página **zonas de DNS privado** , selecione o botão **Adicionar** para começar a criar uma nova zona.

   1. Na página **criar zona DNS privada** , insira os detalhes necessários. Digite **privatelink.siterecovery.WindowsAzure.com** para o nome da zona DNS privada. Você pode escolher qualquer grupo de recursos e qualquer assinatura.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Captura de tela que mostra a guia noções básicas da página Criar DNS privado zona.":::

   1. Continue na guia **examinar \+ criar** para examinar e criar a zona DNS.

1. Vincule a zona DNS privada à sua rede virtual.

   Agora você precisa vincular a zona DNS privada que você criou para o bypass.

   1. Vá para a zona DNS privada que você criou na etapa anterior e, em seguida, vá para **links de rede virtual** no painel esquerdo. Selecione **Adicionar**.

   1. Insira os detalhes necessários. Nas listas **assinatura** e **rede virtual** , selecione os detalhes que correspondem à rede de bypass. Deixe os valores padrão nos outros campos.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Captura de tela que mostra a página Adicionar link de rede virtual.":::

1. Adicionar registros DNS.

   Agora que você criou a zona DNS privada necessária e o ponto de extremidade privado, precisará adicionar registros DNS à sua zona DNS.

   > [!NOTE]
   > Se você estiver usando uma zona DNS privada personalizada, certifique-se de fazer entradas semelhantes, conforme descrito na etapa a seguir.

   Nesta etapa, você precisa fazer entradas para cada FQDN em seu ponto de extremidade privado em sua zona DNS privada.

   1. Vá para a zona DNS privada e vá para a seção **visão geral** no painel esquerdo. Selecione **conjunto de registros** para começar a adicionar registros.

   1. Na página **Adicionar conjunto de registros** , adicione uma entrada para cada nome de domínio totalmente qualificado e IP privado como um registro **de** tipo. Você pode obter uma lista dos nomes de domínio totalmente qualificados e IPs na página **ponto de extremidade particular** em **visão geral**. Como você pode ver na captura de tela a seguir, o primeiro nome de domínio totalmente qualificado do ponto de extremidade privado é adicionado ao conjunto de registros na zona DNS privada.

      Esses nomes de domínio totalmente qualificados correspondem a esse padrão: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Captura de tela que mostra a página Adicionar conjunto de registros.":::

## <a name="next-steps"></a>Próximas etapas

Agora que você habilitou pontos de extremidade privados para a replicação de sua máquina virtual, consulte estes outros artigos para obter informações adicionais e relacionadas:

- [Implantar um servidor de configuração local](./vmware-azure-deploy-configuration-server.md)
- [Configurar a recuperação de desastre de VMs locais do Hyper-V para o Azure](./hyper-v-azure-tutorial.md)