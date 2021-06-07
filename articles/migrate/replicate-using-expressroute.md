---
title: Replicar dados por meio do ExpressRoute com migração de servidor de migrações para Azure
description: Como usar o Azure ExpressRoute para replicação com migração de servidor de migrações para Azure
author: DeSeelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: 9aa9a42422f3c114490d1dbb28a146b6e76ca8cd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558611"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Replicar dados por meio do ExpressRoute com migrações para Azure: migração de servidor

Neste artigo, você aprenderá a configurar as [migrações para Azure: migração de servidor](./migrate-services-overview.md#azure-migrate-server-migration-tool) para replicar dados em um circuito de ExpressRoute durante a migração de servidores para o Azure.

## <a name="understand-azure-expressroute-circuits"></a>Entender os circuitos do Azure ExpressRoute
Um circuito ExpressRoute (ER) conecta sua infraestrutura local à Microsoft por meio de um provedor de conectividade. Os circuitos do ExpressRoute podem ser configurados para usar o emparelhamento privado, o emparelhamento da Microsoft ou ambos. Examine o artigo sobre [circuitos e emparelhamento do expressroute](../expressroute/expressroute-circuit-peerings.md#peeringcompare) para saber mais sobre as várias opções de emparelhamento disponíveis com o ExpressRoute.

A ferramenta de migração de servidor do migrações para Azure ajuda a migrar servidores e servidores locais de outras nuvens para máquinas virtuais do Azure. A ferramenta funciona Configurando um fluxo de replicação em andamento para replicar dados dos servidores a serem migrados para discos gerenciados em sua assinatura do Azure. Quando você estiver pronto para migrar os servidores, os dados replicados no Azure serão usados para migrar os servidores.

Os dados replicados de seus servidores locais podem ser configurados para serem enviados à sua assinatura do Azure pela Internet (usando uma conexão segura criptografada) ou por uma conexão de ExpressRoute. Quando você tem um grande número de servidores para migrar, o uso do ExpressRoute para replicação pode ajudá-lo a migrar servidores com mais eficiência usando a largura de banda provisionada disponível com o circuito do ExpressRoute.

Neste artigo, você aprenderá
> [!div class="checklist"]
>
> * Como replicar dados usando um circuito do ExpressRoute com emparelhamento privado.
> * Como replicar dados usando um circuito do ExpressRoute com emparelhamento da Microsoft.

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>Replicar dados usando um circuito do ExpressRoute com emparelhamento privado

> [!NOTE]
> Atualmente, a replicação em um circuito de emparelhamento privado tem suporte apenas para [a migração sem agente de máquinas virtuais VMware para o Azure](./tutorial-migrate-vmware.md). O suporte de ponto de extremidade privado para outros [métodos de replicação](./migrate-services-overview.md#azure-migrate-server-migration-tool) estará disponível em breve.

No método sem agente de migrar máquinas virtuais VMware para o Azure, o dispositivo de migrações para Azure carrega primeiro os dados de replicação em uma conta de armazenamento (conta de armazenamento em cache) em sua assinatura. Os dados replicados da conta de armazenamento em cache são movidos para discos gerenciados por réplica em sua assinatura pelo serviço migrações para Azure. Para usar um circuito de emparelhamento privado para replicação, você criará e anexará um ponto de extremidade privado para o uso da conta de armazenamento de cache. Os pontos de extremidade privados usam um ou mais endereços IP privados de sua rede virtual (VNet), colocando efetivamente a conta de armazenamento em sua VNet do Azure. O ponto de extremidade privado permite que o dispositivo migrações para Azure se conecte à conta de armazenamento de cache usando o emparelhamento privado do ExpressRoute e transfira dados diretamente no endereço IP privado. <br/>  

![Processo de replicação](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - Além dos dados de replicação, o dispositivo de migração do Azure se comunica com o serviço de migrações para Azure para suas atividades de plano de controle, incluindo orquestrando a replicação. A comunicação do plano de controle entre o dispositivo de migrações para Azure e o serviço de migrações para Azure continua ocorrendo pela Internet no ponto de extremidade público do serviço de migrações para Azure.
> - O ponto de extremidade privado da conta de armazenamento deve ser acessível da rede em que o dispositivo de migração do Azure está implantado.
> - O DNS deve ser configurado para resolver consultas DNS pelo dispositivo de migração do Azure para o ponto de extremidade do serviço BLOB da conta de armazenamento de cache para o endereço IP privado do ponto de extremidade privado anexado à conta de armazenamento em cache.
> - A conta de armazenamento em cache deve estar acessível em seu ponto de extremidade público. (O serviço de migrações para Azure usa o ponto de extremidade público da conta de armazenamento em cache para mover dados da conta de armazenamento para discos gerenciados de réplica.) 


### <a name="1-pre-requisites"></a>1. pré-requisitos

O usuário do Azure que cria o ponto de extremidade privado deve ter as seguintes permissões no grupo de recursos e na rede virtual em que o ponto de extremidade privado será criado.

**Caso de uso** | **Permissões** 
--- | --- 
 Crie e gerencie pontos de extremidade privados. | Microsoft. Network/privateEndpoint/Write/Action<br/>Microsoft. Network/privateEndpoint/leitura/ação  
|Anexe um ponto de extremidade privado a uma VNet/sub-rede.<br/>Isso é necessário na rede virtual em que o ponto de extremidade privado será criado.| Microsoft. Network/virtualNetworks/sub-rede/junção/ação Microsoft. Network/virtualNetworks/junção/ação
|Vincule o ponto de extremidade privado a uma conta de armazenamento. <br/>| Microsoft. Microsoft. Storage/storageAccounts/privateEndpointConnectionApproval/Action <br/> Microsoft. Microsoft. Storage/storageAccounts/privateEndpointConnections/Read
|Crie uma interface de rede e ingresse-a em um grupo de segurança de rede. | Microsoft.Network/networkInterfaces/read <br/> Microsoft. Network/networkInterfaces/sub-redes/gravação <br/> Microsoft. Network/networkInterfaces/sub-redes/leitura<br/> Microsoft. Network/networkSecurityGroups/Join/Action (opcional)
Crie e gerencie zonas DNS privadas.| Função colaborador de zona DNS privado <br/> _Or_ <br/> Microsoft. Network/privateDnsZones/A/* <br/>  Microsoft. Network/privateDnsZones/Write Microsoft. Network/privateDnsZones/Read <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/Write <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/Read <br/> Microsoft. Network/privateDnsZones/virtualNetworkLinks/Write <br/>  Microsoft. Network/privateDnsZones/virtualNetworkLinks/Read <br/> Microsoft. Network/virtualNetworks/junção/ação 

### <a name="2-identify-the-cache-storage-account"></a>2. identificar a conta de armazenamento em cache 
 
As migrações para Azure criam automaticamente uma conta de armazenamento em cache quando você configura a replicação (usando a experiência de portal do Azure) para uma máquina virtual pela primeira vez em um projeto de migrações para Azure. A conta de armazenamento é criada na mesma assinatura e no mesmo grupo de recursos em que você criou o projeto de migrações para Azure.

Para criar e localizar a conta de armazenamento:

1. Use a experiência de portal do Azure para migrações para Azure para replicar uma ou mais máquinas virtuais no projeto.
2. Navegue até o grupo de recursos do projeto de migrações para Azure.
3. Localize a conta de armazenamento em cache identificando o prefixo **"LSA"** no nome da conta de armazenamento.

![Exibição do grupo de recursos](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
>  
> Se você tiver mais de uma conta de armazenamento com o prefixo **"LSA"** em seu grupo de recursos, poderá verificar a conta de armazenamento navegando até o menu configurações de replicação e configuração de destino para qualquer uma das VMs de replicação no projeto. <br/> 
> ![Visão geral das configurações de replicação](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3. atualizar a conta de armazenamento de cache para Uso Geral v2 

Você pode criar pontos de extremidade privados somente em uma conta de armazenamento do Uso Geral v2 (GPv2). Se a conta de armazenamento em cache não for uma conta de armazenamento GPv2, atualize-a para o GPv2 usando as seguintes etapas:

1. Navegue para sua conta de armazenamento.
2. Selecione **Configuração**.
3. Em **tipo de conta**, selecione **Atualizar**.
4. Em **confirmar atualização**, digite o nome da sua conta.
5. Selecione **Atualizar** na parte inferior da página.

![Atualizar conta de armazenamento](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4. criar um ponto de extremidade privado para a conta de armazenamento

1. Vá para sua conta de armazenamento, selecione **rede** no menu à esquerda e selecione a guia **conexões de ponto de extremidade privado** .  
2. Selecione **+ Ponto de extremidade privado**.

    a. Na janela **criar um ponto de extremidade privado** – selecione a **assinatura** e o **grupo de recursos**. Forneça um nome para seu ponto de extremidade particular e selecione a região da conta de armazenamento.  
    ![Janela de configuração do PE](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. Na guia **recurso** , forneça o **nome da assinatura** na qual a conta de armazenamento está. Escolha **Microsoft. Storage/storageAccounts** como o **tipo de recurso**. Em **recurso**, forneça o nome do tipo de GPv2 conta de armazenamento de replicação. Selecione **blob** como o **subrecurso de destino**.  
    ![storageaccountpesettings](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    c. Na guia **configuração** , selecione a **rede virtual** e a **sub-rede** para o ponto de extremidade privado da conta de armazenamento.  

    > [!Note]
    > A rede virtual deve conter o ponto de extremidade do gateway do ExpressRoute ou deve estar conectada à rede virtual com o gateway de ExpressRoute. 

    Na seção **integração de DNS privado** , selecione **Sim** e integre com uma zona DNS privada. A seleção de **Sim** vincula automaticamente a zona DNS à rede virtual selecionada e adiciona os registros DNS necessários para a resolução DNS de novos IPS e nomes de domínio totalmente qualificados criados para o ponto de extremidade privado. Saiba mais sobre [zonas DNS privadas.](../dns/private-dns-overview.md)

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. Você também pode adicionar **marcas** para seu ponto de extremidade particular.  

    e. Continue a **examinar + criar** depois de inserir detalhes. Quando a validação for concluída, selecione **criar** para criar o ponto de extremidade privado.

    > [!Note]
    > Se o usuário que cria o ponto de extremidade privado também for o proprietário da conta de armazenamento, o ponto de extremidade privado será aprovado automaticamente. Caso contrário, o proprietário deve aprovar o ponto de extremidade privado para uso. 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>Criar zonas DNS privadas e adicionar registros DNS manualmente (opcional)

Se você não selecionou a opção de integração com uma zona DNS privada no momento da criação do ponto de extremidade privado, siga as etapas nesta seção para criar manualmente uma zona DNS privada. 

> [!Note]
> Se você selecionou **Sim** para integrar com uma zona DNS privada, poderá ignorar esta seção. 

1. Crie uma zona DNS privada. 

    ![createprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a. Na página **zonas de DNS privado** , selecione o botão **+ Adicionar** para começar a criar uma nova zona.  
    b. Na página **criar zona DNS privada** , preencha os detalhes necessários. Insira o nome da zona DNS privada como _privatelink_. blob.Core.Windows.net.  
    c. Continue na guia **revisar + criar** para examinar e criar a zona DNS.   

2. Vincule a zona DNS privada à sua rede virtual.  

    A zona DNS privada criada acima deve ser vinculada à rede virtual à qual o ponto de extremidade privado está anexado.

    a. Vá para a zona DNS privada criada na etapa anterior e navegue até os links de rede virtual no lado esquerdo da página. Selecione o botão **+ Adicionar** .   
    b. Preencha os detalhes necessários. Os campos **assinatura** e **rede virtual** devem ser preenchidos com os detalhes correspondentes da rede virtual em que o ponto de extremidade privado está anexado. Os outros campos podem ser deixados como estão.

3. A próxima etapa é adicionar registros DNS à zona DNS. Adicione uma entrada para o nome de domínio totalmente qualificado da conta de armazenamento em sua zona DNS privada.

    a. Acesse sua zona DNS privada e navegue até a seção **visão geral** no lado esquerdo da página. Selecione **+** conjunto de registros para começar a adicionar registros.  

    b. Na página **Adicionar conjunto de registros** , adicione uma entrada para o nome de domínio totalmente qualificado e o IP privado como um registro de tipo.

> [!Important]
> Você pode exigir configurações adicionais de DNS para resolver o endereço IP privado do ponto de extremidade privado da conta de armazenamento do ambiente de origem. [Examine este artigo](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) para entender a configuração de DNS necessária.

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>Replicar dados usando um circuito do ExpressRoute com emparelhamento da Microsoft

Você pode usar o emparelhamento da Microsoft ou um domínio de emparelhamento público existente (preterido para novas conexões do ExpressRoute) para rotear o tráfego de replicação por meio de um circuito do ExpressRoute, conforme ilustrado no diagrama a seguir.
![replicationwithmicrosoftpeering](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

Mesmo com os dados de replicação que passam pelo circuito emparelhado da Microsoft, você ainda precisará de conectividade com a Internet do site local para outras comunicações (plano de controle) com o serviço migrações para Azure. Há algumas URLs adicionais que não podem ser acessadas pelo ExpressRoute, que o host do Hyper-V e o dispositivo de replicação precisam de acesso para orquestrar o processo de replicação. Você pode examinar os requisitos de URL com base no cenário de migração, [migrações sem agente do VMware](./migrate-appliance.md#public-cloud-urls) ou [migrações baseadas em agente](./migrate-replication-appliance.md).  

Caso você use um proxy em seu site local e queira usar o ExpressRoute para o tráfego de replicação, você precisa configurar um bypass de proxy para URLs relevantes no dispositivo local. 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Configurar regras de bypass de proxy no dispositivo de migrações para Azure (para migrações sem agente do VMware)

1. Faça logon (área de trabalho remota) no dispositivo de migrações para Azure.   
2. Abra o arquivo C:/ProgramData/MicrosoftAzure/config/appliance.jsusando o bloco de notas.
3. No arquivo, altere a linha que diz "EnableProxyBypassList": "false" para "EnableProxyBypassList": "true". Salve as alterações e reinicie o dispositivo.
4. Após a reinicialização, ao abrir o Gerenciador de configuração do dispositivo, você poderá ver a opção proxy bypass na interface do usuário do aplicativo Web. Adicione as URLs abaixo à lista de bypass de proxy.   
    - . *. vault.azure.net
    - . *. servicebus.windows.net
    - . *. discoverysrv.windowsazure.com
    - . *. migration.windowsazure.com
    - . *. hypervrecoverymanager.windowsazure.com
    - . *. blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>Configurar regras de bypass de proxy no dispositivo de replicação (para migrações baseadas em agente)

Siga as etapas abaixo para configurar a lista de bypass de proxy no servidor de configuração e servidores de processo:

1. [Baixe a ferramenta PsExec](/sysinternals/downloads/psexec) para acessar o contexto de usuário do sistema.
2. Abra o Internet Explorer no contexto do usuário do sistema executando a seguinte linha de comando PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"
3. Adicionar configurações de proxy no IE.
4. Na lista de bypass, adicione a URL de armazenamento do Azure. *. blob. Core. Windows. net.  

As regras de bypass acima garantirão que o tráfego de replicação possa fluir por meio do ExpressRoute, enquanto a comunicação de gerenciamento pode passar pelo proxy para a Internet.  

Além disso, você deve anunciar rotas no filtro de rota para as seguintes comunidades BGP para fazer com que seu tráfego de replicação de migrações para Azure atravesse um circuito de ExpressRoute em vez da Internet.  

- Comunidade de BGP regional para a região do Azure de origem (Azure migrar região do projeto)
- Comunidade de BGP regional para a região do Azure de destino (região para migração)
- Comunidade BGP para Azure Active Directory (12076:5060)

Saiba mais sobre [filtros de rota](../expressroute/how-to-routefilter-portal.md) e a lista de [comunidades BGP para ExpressRoute](../expressroute/expressroute-routing.md#bgp). 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [circuitos do ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Saiba mais sobre os [domínios de roteamento do ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Saiba mais sobre [pontos de extremidade privados](../private-link/private-endpoint-overview.md).