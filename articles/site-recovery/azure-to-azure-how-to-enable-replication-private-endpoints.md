---
title: Habilitar a replicação para pontos de extremidade privados no Azure Site Recovery
description: Este artigo descreve como configurar a replicação para VMs com pontos de extremidade privados de uma região do Azure para outra usando Site Recovery.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 86f18be73966cb07489630191420b846622e45b8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629820"
---
# <a name="replicate-machines-with-private-endpoints"></a>Replicar computadores com pontos de extremidade privados

Azure Site Recovery permite que você use pontos de extremidade particulares do [link privado do Azure](../private-link/private-endpoint-overview.md) para replicar seus computadores de dentro de uma rede virtual isolada. O acesso de ponto de extremidade privado a um cofre de recuperação tem suporte em todas as regiões do Azure Commercial & governamental.

Este artigo fornece instruções para que você execute as seguintes etapas:

- Crie um cofre dos serviços de recuperação de backup do Azure para proteger seus computadores.
- Habilite uma identidade gerenciada para o cofre e conceda as permissões necessárias para acessar as contas de armazenamento do cliente para replicar o tráfego da origem para os locais de destino. O acesso de identidade gerenciada para armazenamento é necessário quando você está configurando o acesso de link privado para o cofre.
- Fazer alterações de DNS necessárias para pontos de extremidade privados
- Criar e aprovar pontos de extremidade privados para um cofre dentro de uma rede virtual
- Crie pontos de extremidade privados para as contas de armazenamento. Você pode continuar a permitir o acesso público ou de firewall para armazenamento, conforme necessário. A criação de um ponto de extremidade privado para acessar o armazenamento não é obrigatória para Azure Site Recovery.
  
Veja abaixo uma arquitetura de referência sobre como o fluxo de trabalho de replicação é alterado com pontos de extremidade privados.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Arquitetura de referência para Site Recovery com pontos de extremidade privados.":::

## <a name="prerequisites-and-caveats"></a>Pré-requisitos e limitações

- Pontos de extremidade privados podem ser criados somente para novos cofres de serviços de recuperação que não têm nenhum item registrado no cofre. Assim, os pontos de extremidade privados **devem ser criados antes que qualquer item seja adicionado ao cofre**. Examine a estrutura de preços para [pontos de extremidade privados](https://azure.microsoft.com/pricing/details/private-link/).
- Quando um ponto de extremidade privado é criado para um cofre, o cofre é bloqueado e **não pode ser acessado de redes diferentes das redes que têm pontos de extremidade privados**.
- Azure Active Directory atualmente não dá suporte a pontos de extremidade privados. Assim, os IPs e os nomes de domínio totalmente qualificados necessários para Azure Active Directory funcionar em uma região precisam ter permissão de acesso de saída da rede protegida. Você também pode usar a marca do grupo de segurança de rede "Azure Active Directory" e as marcas de firewall do Azure para permitir acesso ao Azure Active Directory, conforme aplicável.
- **Pelo menos sete endereços IP são necessários** nas sub-redes de seus computadores de origem e de recuperação. Quando você cria um ponto de extremidade privado para o cofre, Site Recovery cria cinco links privados para acessar seus microserviços. Além disso, quando você habilita a replicação, ela adiciona dois links privados adicionais para o emparelhamento de região de origem e de destino.
- **Um endereço IP adicional é necessário** nas sub-redes de origem e de recuperação. Esse endereço IP é necessário apenas quando você precisa usar pontos de extremidade privados conectando-se às contas de armazenamento em cache.
  Pontos de extremidade privados para armazenamento só podem ser criados no tipo Uso Geral v2. Examine a estrutura de preços para [transferência de dados no GPv2](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Criando e usando pontos de extremidade privados para Site Recovery

 Esta seção aborda as etapas envolvidas na criação e no uso de pontos de extremidade privados para Azure Site Recovery dentro de suas redes virtuais.

> [!NOTE]
> É altamente recomendável que você siga estas etapas na mesma sequência fornecida. Não fazer isso pode levar ao cofre que está sendo renderizado não é possível usar pontos de extremidade privados e exigir que você reinicie o processo com um novo cofre.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um cofre dos serviços de recuperação é uma entidade que contém as informações de replicação dos computadores e é usado para disparar operações de Site Recovery. Para obter mais informações, consulte [criar um cofre dos serviços de recuperação](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

## <a name="enable-the-managed-identity-for-the-vault"></a>Habilite a identidade gerenciada para o cofre.

Uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) permite que o cofre tenha acesso às contas de armazenamento do cliente. Site Recovery precisa acessar o armazenamento de origem, o armazenamento de destino e as contas de armazenamento de cache/log, dependendo do requisito de cenário.
O acesso de identidade gerenciada é essencial quando você está usando o serviço de links privados para o cofre.

1. Vá para o cofre dos serviços de recuperação. Selecione **identidade** em _configurações_.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Mostra o portal do Azure e a página de serviços de recuperação.":::

1. Altere o **status** para _ativado_ e selecione **salvar**.

1. Uma **ID de objeto** é gerada indicando que o cofre agora está registrado com Azure Active Directory.

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Criar pontos de extremidade privados para o cofre dos serviços de recuperação

Para habilitar o failover e o failback para máquinas virtuais do Azure, você precisará de dois pontos de extremidade privados para o cofre. Um ponto de extremidade privado para a proteção de computadores na rede de origem e outro para a nova proteção dos computadores com failover na rede de recuperação.

Certifique-se de criar uma rede virtual de recuperação em sua região de destino também durante esse processo de instalação.

Crie o primeiro ponto de extremidade privado para seu cofre dentro de sua rede virtual de origem usando o centro de links privado no portal ou por meio de [Azure PowerShell](../private-link/create-private-endpoint-powershell.md). Crie o segundo ponto de extremidade privado para o cofre dentro de sua rede de recuperação. A seguir estão as etapas para criar o ponto de extremidade privado na rede de origem. Repita as mesmas diretrizes para criar o segundo ponto de extremidade privado.

1. Na barra de pesquisa portal do Azure, procure e selecione "link privado". Essa ação leva você para o centro de links privado.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Mostra a pesquisa do portal do Azure para o centro de links privado.":::

1. Na barra de navegação à esquerda, selecione **pontos de extremidade privados**. Uma vez na página pontos de extremidade particulares, selecione **\+ Adicionar** para começar a criar um ponto de extremidade privado para seu cofre.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Mostra a criação de um ponto de extremidade privado no centro de links privado.":::

1. Uma vez na experiência "criar ponto de extremidade privado", é necessário especificar detalhes para criar sua conexão de ponto de extremidade privada.

   1. **Noções básicas**: Preencha os detalhes básicos para seus pontos de extremidade privados. A região deve ser a mesma que as máquinas de origem.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Mostra a guia básica, os detalhes do projeto, a assinatura e outros campos relacionados para criar um ponto de extremidade privado no portal do Azure.":::

   1. **Recurso**: essa guia exige que você mencione o recurso de plataforma como serviço para o qual deseja criar a conexão. Selecione _Microsoft. recoveryservices/cofres_ do **tipo de recurso** para sua assinatura selecionada. Em seguida, escolha o nome do seu cofre dos serviços de recuperação para o **recurso** e defina _Azure site Recovery_ como o **subrecurso de destino**.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Mostra a guia de recursos, o tipo de recurso, o recurso e os campos de subrecurso de destino para vincular a um ponto de extremidade privado no portal do Azure.":::

   1. **Configuração**: em configuração, especifique a rede virtual e a sub-rede em que você deseja que o ponto de extremidade privado seja criado. Essa rede virtual é a rede na qual a máquina virtual está presente. Habilite a integração com a zona DNS privada selecionando **Sim**. Escolha uma zona DNS já criada ou crie uma nova. A seleção de **Sim** vincula automaticamente a zona à rede virtual de origem e adiciona os registros DNS necessários para a resolução DNS de novos IPS e nomes de domínio totalmente qualificados criados para o ponto de extremidade privado.

      Certifique-se de optar por criar uma nova zona DNS para cada novo ponto de extremidade privado conectando-se ao mesmo cofre. Se você escolher uma zona DNS privada existente, os registros CNAME anteriores serão substituídos. Consulte a [orientação do ponto de extremidade particular](../private-link/private-endpoint-overview.md#private-endpoint-properties) antes de continuar.

      Se o seu ambiente tiver um modelo Hub e spoke, você precisará apenas de um ponto de extremidade privado e apenas uma zona DNS privada para toda a configuração, já que todas as suas redes virtuais já têm o emparelhamento habilitado entre elas. Para obter mais informações, consulte [integração de DNS do ponto de extremidade privado](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Para criar manualmente a zona DNS privada, siga as etapas em [criar zonas DNS privadas e adicionar registros DNS manualmente](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Mostra a guia configuração com os campos de integração de rede e DNS para configuração de um ponto de extremidade privado no portal do Azure.":::

   1. **Marcas**: opcionalmente, você pode adicionar marcas para seu ponto de extremidade particular.

   1. **Examinar \+ criar**: quando a validação for concluída, selecione **criar** para criar o ponto de extremidade privado.

Depois que o ponto de extremidade privado for criado, cinco nomes de domínio totalmente qualificados serão adicionados ao ponto de extremidade privado. Esses links permitem que os computadores na rede virtual obtenham acesso a todos os microserviços necessários do Site Recovery no contexto do cofre. Posteriormente, quando você habilitar a replicação, dois nomes de domínio adicionais totalmente qualificados serão adicionados ao mesmo ponto de extremidade privado.

Os cinco nomes de domínio são formatados com o seguinte padrão:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Aprovar pontos de extremidade privados para Site Recovery

Se o usuário que cria o ponto de extremidade privado também for o proprietário do cofre dos serviços de recuperação, o ponto de extremidade privado criado acima será aprovado automaticamente em alguns minutos. Caso contrário, o proprietário do cofre deve aprovar o ponto de extremidade privado antes de usá-lo. Para aprovar ou rejeitar uma conexão de ponto de extremidade particular solicitada, acesse **conexões de ponto de extremidade privado** em "configurações" na página cofre de recuperação.

Você pode ir para o recurso de ponto de extremidade privado para examinar o status da conexão antes de continuar.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Mostra a página conexões de ponto de extremidade privada do cofre e a lista de conexões no portal do Azure.":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Adicional Criar pontos de extremidade privados para a conta de armazenamento de cache

Um ponto de extremidade privado para o armazenamento do Azure pode ser usado. A criação de pontos de extremidade privados para acesso de armazenamento é _opcional_ para replicação de Azure site Recovery. Ao criar um ponto de extremidade privado para armazenamento, os seguintes requisitos se aplicam:

- Você precisa de um ponto de extremidade privado para a conta de armazenamento de cache/log em sua rede virtual de origem.
- Você precisa de um segundo ponto de extremidade privado no momento da nova proteção dos computadores com failover na rede de recuperação. Esse ponto de extremidade privado é para a nova conta de armazenamento criada na região de destino.

> [!NOTE]
> Se os pontos de extremidade privados não estiverem habilitados na conta de armazenamento, a proteção ainda será bem-sucedida. No entanto, o tráfego de replicação funcionaria para Azure Site Recovery pontos de extremidade públicos. Para garantir que os fluxos de tráfego de replicação por meio de links privados, a conta de armazenamento deve ser habilitada com pontos de extremidade privados.

> [!NOTE]
> O ponto de extremidade privado para armazenamento só pode ser criado em contas de armazenamento **uso geral v2** . Para obter informações sobre preços, consulte [preços de blob de páginas padrão](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Siga as [orientações para a criação de armazenamento privado](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) para criar uma conta de armazenamento com um ponto de extremidade privado. Certifique-se de selecionar **Sim** para integração com a zona DNS privada. Selecione uma zona DNS já criada ou crie uma nova.

## <a name="grant-required-permissions-to-the-vault"></a>Conceder as permissões necessárias ao cofre

Se suas máquinas virtuais estiverem usando discos gerenciados, você precisará conceder as permissões de identidade gerenciadas somente para as contas de armazenamento de cache. Caso as máquinas virtuais estejam usando discos não gerenciados, você precisa conceder permissões de identidade gerenciadas para contas de armazenamento de origem, de cache e de destino. Nesse caso, você precisa criar a conta de armazenamento de destino com antecedência.

Antes de habilitar a replicação de máquinas virtuais, a identidade gerenciada do cofre deve ter as seguintes permissões de função, dependendo do tipo de conta de armazenamento:

- Contas de armazenamento baseadas no Resource Manager (tipo padrão):
  - [Colaborador](../role-based-access-control/built-in-roles.md#contributor)
  - [Colaborador de dados de blob de armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Contas de armazenamento baseadas no Resource Manager (tipo Premium):
  - [Colaborador](../role-based-access-control/built-in-roles.md#contributor)
  - [Proprietário de Dados do Blob de Armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Contas de armazenamento clássicas:
  - [Colaborador da conta de armazenamento clássica](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Função do Serviço de Operador da Chave da Conta de Armazenamento Clássica](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

As etapas a seguir descrevem como adicionar uma atribuição de função a suas contas de armazenamento, uma de cada vez:

1. Vá para a conta de armazenamento e navegue até **controle de acesso (iam)** no lado esquerdo da página.

1. Uma vez no **controle de acesso (iam)**, na caixa "adicionar uma atribuição de função", selecione **Adicionar**.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Mostra a página de controle de acesso (IAM) em uma conta de armazenamento e o botão ' adicionar uma atribuição de função ' no portal do Azure.":::

1. Na página do lado "adicionar uma atribuição de função", escolha a função na lista acima no menu suspenso **função** . Insira o **nome** do cofre e selecione **salvar**.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Mostra a página de controle de acesso (IAM) em uma conta de armazenamento e as opções para selecionar uma função e a qual entidade para conceder essa função no portal do Azure.":::

Além dessas permissões, os serviços confiáveis da MS também precisam ter acesso permitido. Vá para "firewalls e redes virtuais" e selecione a caixa de seleção "permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento" em **exceções**.

## <a name="protect-your-virtual-machines"></a>Proteger suas máquinas virtuais

Depois que todas as configurações acima forem concluídas, continue com a habilitação da replicação para suas máquinas virtuais. Todas as operações de Site Recovery funcionam sem etapas adicionais se a integração de DNS foi usada durante a criação de pontos de extremidade privados no cofre. No entanto, se as zonas DNS forem criadas e configuradas manualmente, você precisará de etapas adicionais para adicionar registros DNS específicos nas zonas DNS de origem e de destino depois de habilitar a replicação. Para obter detalhes e etapas, consulte [criar zonas DNS privadas e adicionar registros DNS manualmente](#create-private-dns-zones-and-add-dns-records-manually).

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Criar zonas DNS privadas e adicionar registros DNS manualmente

Se você não selecionou a opção de integração com a zona DNS privada no momento da criação do ponto de extremidade privado para o cofre, siga as etapas nesta seção.

Crie uma zona DNS privada para permitir que o agente de mobilidade resolva nomes de domínio totalmente qualificados de link privado para IPs privados.

1. Criar uma zona DNS privada

   1. Pesquise "zona de DNS privado" na barra de pesquisa **todos os serviços** e selecione "zonas de DNS privado" na lista suspensa.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Mostra a pesquisa da &quot;zona DNS privada&quot; na página novos recursos no portal do Azure.":::

   1. Na página "zonas de DNS privado", selecione o botão **\+ Adicionar** para começar a criar uma nova zona.

   1. Na página "criar zona DNS privada", preencha os detalhes necessários. Insira o nome da zona DNS privada como `privatelink.siterecovery.windowsazure.com` . Você pode escolher qualquer grupo de recursos e qualquer assinatura para criá-lo.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Mostra a guia noções básicas da página Criar zona de DNS privado e detalhes do projeto relacionado no portal do Azure.":::

   1. Continue na guia **examinar \+ criar** para examinar e criar a zona DNS.

1. Vincular zona DNS privada à sua rede virtual

   As zonas DNS privadas criadas acima agora devem ser vinculadas à rede virtual na qual os servidores estão atualmente. Você também precisa vincular a zona DNS privada à rede virtual de destino antecipadamente.

   1. Vá para a zona DNS privada que você criou na etapa anterior e navegue até os **links de rede virtual** no lado esquerdo da página. Uma vez lá, selecione o botão **\+ Adicionar** .

   1. Preencha os detalhes necessários. Os campos **assinatura** e **rede virtual** devem ser preenchidos com os detalhes correspondentes da rede virtual onde os servidores existem. Os outros campos devem ser deixados como estão.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Mostra a página para adicionar um link de rede virtual com o nome do link, a assinatura e a rede virtual relacionada no portal do Azure.":::

1. Adicionar registros DNS

   Depois de criar as zonas DNS privadas necessárias e os pontos de extremidade privados, você precisará adicionar os registros DNS às suas zonas DNS.

   > [!NOTE]
   > Caso você esteja usando uma zona DNS privada personalizada, certifique-se de que entradas semelhantes sejam feitas conforme discutido abaixo.

   Esta etapa exige que você faça entradas para cada nome de domínio totalmente qualificado em seu ponto de extremidade privado em sua zona DNS privada.

   1. Acesse sua zona DNS privada e navegue até a seção **visão geral** no lado esquerdo da página. Uma vez lá, selecione **\+ conjunto de registros** para começar a adicionar registros.

   1. Na página "Adicionar conjunto de registros" que é aberta, adicione uma entrada para cada nome de domínio totalmente qualificado e IP privado como um registro _de_ tipo. A lista de nomes de domínio totalmente qualificados e IPs pode ser obtida na página "ponto de extremidade particular" em **visão geral**. Conforme mostrado no exemplo abaixo, o primeiro nome de domínio totalmente qualificado do ponto de extremidade privado é adicionado ao conjunto de registros na zona DNS privada.

      Esses nomes de domínio totalmente qualificados correspondem ao padrão: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Mostra a página para adicionar um registro de tipo DNS para o nome de domínio totalmente qualificado para o ponto de extremidade privado no portal do Azure.":::

   > [!NOTE]
   > Depois de habilitar a replicação, dois nomes de domínio mais totalmente qualificados são criados nos pontos de extremidade privados em ambas as regiões. Verifique se você também adicionou os registros DNS para esses nomes de domínio totalmente qualificados recém-criados.

## <a name="next-steps"></a>Próximas etapas

Agora que você habilitou pontos de extremidade privados para a replicação de sua máquina virtual, consulte estas outras páginas para obter informações adicionais e relacionadas:

- [Replicar VMs do Azure para outra região do Azure](./azure-to-azure-how-to-enable-replication.md)
- [Tutorial: Configurar a recuperação de desastre para VMs do Azure](./azure-to-azure-tutorial-enable-replication.md)