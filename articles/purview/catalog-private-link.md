---
title: Usar pontos de extremidade privados para proteger o acesso ao alcance
description: Este artigo descreve como você pode configurar um ponto de extremidade privado para sua conta do alcance
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 09fa10e7f7751321601c5c4871b2cf36ccf6f01f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720913"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>Usar pontos de extremidade privados para sua conta do alcance

Você pode usar pontos de extremidade privados para suas contas do alcance para permitir que clientes e usuários em uma rede virtual (VNet) acessem com segurança o catálogo por meio de um link privado. O ponto de extremidade privado usa um endereço IP do espaço de endereço de VNet para sua conta do alcance. O tráfego de rede entre os clientes na VNet e a conta alcance atravessa a VNet e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública.

## <a name="create-purview-account-with-a-private-endpoint"></a>Criar conta do alcance com um ponto de extremidade privado

1. Navegue até a [portal do Azure](https://portal.azure.com) e, em seguida, para sua conta do alcance.

1. Preencha as informações básicas e defina o método de conectividade como ponto de extremidade privado na guia **rede** . Configure seus pontos de extremidade privados de ingestão fornecendo detalhes de **assinatura, vnet e sub-rede** que você deseja emparelhar com seu ponto de extremidades privado.

    > [!NOTE]
    > Crie um ponto de extremidade privado de ingestão somente se pretender habilitar o isolamento de rede para cenários de verificação de ponta a ponta, para suas fontes locais e do Azure. No momento, não há suporte para pontos de extremidade privados de ingestão trabalhando com suas fontes AWS.

    :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Criar um ponto de extremidade privado no portal do Azure":::

1. Opcionalmente, você também pode optar por configurar uma **zona de DNS privado** para cada ponto de extremidade particular de ingestão.

1. Clique em Adicionar para adicionar um ponto de extremidade privado para sua conta do alcance.

1. Na página criar ponto de extremidade privado, defina alcance sub-recurso como **conta**, escolha sua rede virtual e sub-rede e selecione a zona de DNS privado onde o DNS será registrado (você também pode utilizar seus próprios servidores DNS ou criar registros DNS usando arquivos de host em suas máquinas virtuais).

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="Seleções de criação de ponto de extremidade particular":::

1. Selecione **OK**.

1. Selecione **Examinar + criar**. Você é levado até a página Examinar + criar, na qual o Azure valida sua configuração.

1. Quando a mensagem Validação aprovada for exibida, selecione **Criar**.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Validação aprovada para a criação da conta":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>Criar um ponto de extremidade privado para o portal da Web do alcance

1. Navegue até a conta do alcance que você acabou de criar, selecione as conexões de ponto de extremidade privado na seção Configurações.

1. Clique em + privado ponto de extremidade para criar um novo ponto de extremidade privado.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Criar ponto de extremidade privado do portal":::

1. Preencha as informações básicas.

1. Na guia recurso, selecione tipo de recurso para ser **Microsoft. alcance/accounts**.

1. Selecione o recurso para ser a conta alcance recém-criada e selecione o subrecurso de destino a ser **portal**.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="Detalhes do ponto de extremidade privado do portal":::

1. Selecione a rede virtual e a zona de DNS privado na guia configuração. Navegue até a página Resumo e clique em **criar** para criar o ponto de extremidade privado do Portal.

## <a name="enabling-access-to-azure-active-directory"></a>Habilitando o acesso ao Azure Active Directory

> [!NOTE]
> Se sua VM, gateway de VPN ou gateway de emparelhamento VNET tem acesso público à Internet, ele pode acessar o portal do alcance e a conta alcance habilitada com pontos de extremidade privados e você não precisa seguir o restante das instruções abaixo. Se sua rede privada tiver regras de grupo de segurança de rede definidas para negar todo o tráfego de Internet público, será necessário adicionar algumas regras para habilitar o acesso ao AAD. Siga as instruções abaixo para fazer isso.

As instruções abaixo são para acessar o alcance com segurança de uma VM do Azure. Etapas semelhantes precisam ser seguidas se você estiver usando VPN ou outros gateways de emparelhamento vnet.

1. Navegue até sua VM no portal do Azure, selecione a guia rede na seção Configurações. Em seguida, selecione regras de porta de saída e clique em Adicionar regra de porta de saída.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Adicionando regra de saída":::

2. Na folha Adicionar regra de porta de saída, selecione *destino* como marca de serviço, marca de serviço de destino a ser **AzureActiveDirectory**, intervalos de portas de destino para serem *, ação a ser permitida, **prioridade deve ser maior do que a regra que negou todo o tráfego da Internet**. Crie a regra.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Adicionando detalhes da regra de saída":::

3. Siga as mesmas etapas para criar outra regra para permitir a marca de serviço '**AzureResourceManager**'. Se você precisar acessar o portal do Azure, também poderá adicionar uma regra para a marca de serviço '*AzurePortal*'.

4. Conecte-se à VM, abra o navegador, navegue até o console do navegador (Ctrl + Shift + J) e alterne para a guia rede para monitorar as solicitações de rede. Insira web.purview.azure.com na caixa URL e tente fazer logon usando suas credenciais do AAD. O logon pode provavelmente falhar e, na guia rede do console, você pode ver o AAD tentando acessar o aadcdn.msauth.net, mas está sendo bloqueado.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Detalhes de falha de logon":::

5. Nesse caso, abra um prompt de comando na VM e execute o ping nesta URL (aadcdn.msauth.net), obtenha seu IP e, em seguida, adicione uma regra de porta de saída para o IP nas regras de segurança de rede da VM. Defina o destino para o endereço IP e defina os endereços IP de destino como IP do aadcdn. Devido ao balanceador de carga e ao Gerenciador de tráfego, o IP da CDN do AAD pode ser dinâmico. Depois de obter seu IP, é melhor adicioná-lo ao arquivo de host da VM para forçar o navegador a visitar esse IP para obter a CDN do AAD.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Testar ping":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Regra de CDN do AAD":::

6. Depois que a nova regra for criada, navegue de volta para a VM e tente fazer logon usando suas credenciais do AAD novamente. Se o logon for executado com sucesso, o portal do alcance estará pronto para uso. Mas, em alguns casos, o AAD será redirecionado para outros domínios para logon com base no tipo de conta do cliente. Por exemplo, para uma conta do live.com, o AAD será redirecionado para o live.com para logon, então essas solicitações serão bloqueadas novamente. Para contas de funcionários da Microsoft, o AAD acessará o msft.sts.microsoft.com para obter informações de logon. Verifique as solicitações de rede na guia rede do navegador para ver quais solicitações de domínio estão sendo bloqueadas, refaça a etapa anterior para obter seu IP e adicionar regras de porta de saída no grupo de segurança de rede para permitir solicitações para esse IP (se possível, adicione a URL e o IP ao arquivo de host da VM para corrigir a resolução de DNS). Se você souber os intervalos de IP do domínio de logon exato, também poderá adicioná-los diretamente às regras de rede.

7. Agora, o logon no AAD deve ser bem-sucedido. O portal do alcance será carregado com êxito, mas a listagem de todas as contas do alcance não funcionará, pois ela só pode acessar uma conta alcance específica. Insira *Web. alcance. Azure. com/Resource/{PurviewAccountName}* para visitar diretamente a conta alcance para a qual você configurou com êxito um ponto de extremidade privado para.
 
## <a name="ingestion-private-endpoints-and-scanning-sources-in-private-networks-vnets-and-behind-private-endpoints"></a>Pontos de extremidade particulares de ingestão e fontes de verificação em redes privadas, Vnets e atrás de pontos de extremidade privados

Se você quiser garantir o isolamento de rede para seus metadados fluindo da origem que está sendo examinada para o alcance DataMap, siga estas etapas:
1. Habilitar um **ponto de extremidade privado de ingestão** seguindo as etapas [nesta seção](#creating-an-ingestion-private-endpoint)
1. Digitalize a origem usando um **ir auto-hospedado**.
 
    1. Todos os tipos de origem locais, como SQL Server, Oracle, SAP e outros, atualmente têm suporte apenas por meio de verificações baseadas em IR de hospedagem interna. O IR auto-hospedado deve ser executado em sua rede privada e, em seguida, ser emparelhado com sua vnet no Azure. Sua vnet do Azure deve, então, ser habilitada no ponto de extremidade particular de ingestão seguindo as etapas [abaixo](#creating-an-ingestion-private-endpoint) 
    1. Para todos os tipos de origem **do Azure** , como o armazenamento de BLOBs do Azure, o banco de dados SQL do Azure e outros, você deve escolher explicitamente executar a verificação usando o ir de hospedagem interna para garantir o isolamento de rede. Siga as etapas [aqui](manage-integration-runtimes.md) para configurar um ir auto-hospedado. Em seguida, configure sua verificação na origem do Azure escolhendo o IR auto-hospedado na lista suspensa **conectar via Integration Runtime** para garantir o isolamento de rede. 
    
    :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Executando o exame do Azure usando o IR auto-hospedado":::

> [!NOTE]
> No momento, não há suporte para o método de credencial MSI quando você examina suas fontes do Azure usando um IR auto-hospedado. Você deve usar um dos outros métodos de credencial com suporte para essa origem do Azure.

## <a name="enable-private-endpoint-on-existing-purview-accounts"></a>Habilitar ponto de extremidade privado em contas de alcance existentes

Há duas maneiras de adicionar pontos de extremidade privados do alcance depois de criar sua conta do alcance:

- Usando o portal do Azure (conta alcance)
- Usando o centro de links privado

### <a name="using-the-azure-portal-purview-account"></a>Usando o portal do Azure (conta alcance)

1. Navegue até a conta alcance da portal do Azure, selecione as conexões de ponto de extremidade privado na seção **rede** de **configurações**.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Criar ponto de extremidade privado da conta":::

1. Clique em + privado ponto de extremidade para criar um novo ponto de extremidade privado.

1. Preencha as informações básicas.

1. Na guia recurso, selecione tipo de recurso para ser **Microsoft. alcance/accounts**.

1. Selecione o recurso para ser a conta alcance e selecione subrecurso de destino como **conta**.

1. Selecione a **rede virtual** e a **zona de DNS privado** na guia configuração. Navegue até a página Resumo e clique em **criar** para criar o ponto de extremidade privado do Portal.

> [!NOTE]
> Você precisará seguir as mesmas etapas acima para o subrecurso de destino selecionado como **portal** também.

#### <a name="creating-an-ingestion-private-endpoint"></a>Criando um ponto de extremidade privado de ingestão

1. Navegue até a conta alcance da portal do Azure, selecione as conexões de ponto de extremidade privado na seção **rede** de **configurações**.
1. Navegue até a guia **conexões de ponto de extremidade privado de ingestão** e clique em **+ novo** para criar um novo ponto de extremidade privado de ingestão.

1. Preencha informações básicas e detalhes de vnet.
 
    :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="Preencher detalhes do ponto de extremidade privado":::

1. Clique em **criar** para concluir a configuração.

> [!NOTE]
> Pontos de extremidade privados de ingestão podem ser criados somente por meio da experiência de portal do Azure alcance descrita acima. Ele não pode ser criado no centro de links privado.

### <a name="using-the-private-link-center"></a>Usando o centro de links privado

1. Navegue até o [Portal do Azure](https://portal.azure.com).

2. Na barra de pesquisa na parte superior da página, procure "vínculo privado" e navegue até a folha de link privado clicando na primeira opção.

3. Clique em ' + Adicionar ' e preencha os detalhes básicos.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Criar PE do centro de links privado":::

4. Selecione o recurso que será a conta do alcance já criada e selecione subrecurso de destino como **conta**.

5. Selecione a rede virtual e a zona de DNS privado na guia configuração. Navegue até a página Resumo e clique em **criar** para criar o ponto de extremidade privado da conta.

> [!NOTE]
> Você precisará seguir as mesmas etapas acima para o subrecurso de destino selecionado como **portal** também.

## <a name="firewalls-to-restrict-public-access"></a>Firewalls para restringir o acesso público

Para cortar o acesso à conta alcance completamente da Internet pública, siga as etapas abaixo. Essa configuração será aplicada ao ponto de extremidade privado e a conexões de ponto de extremidade privadas de ingestão.

1. Navegue até a conta alcance da portal do Azure, selecione as conexões de ponto de extremidade privado na seção **rede** de **configurações**.
1. Navegue até a guia firewall e verifique se a opção Ativar/desativar está definida como **negar**.

    :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="Configurações do firewall de ponto de extremidade privado":::

## <a name="next-steps"></a>Próximas etapas

- [Procurar o catálogo de dados do Azure alcance](how-to-browse-catalog.md)

- [Pesquisar no Catálogo de Dados do Azure Purview](how-to-search-catalog.md)
