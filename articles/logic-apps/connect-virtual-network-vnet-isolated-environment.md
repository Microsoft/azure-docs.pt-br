---
title: Conectar redes virtuais do Azure de Aplicativos Lógicos do Azure por meio de um ambiente do serviço de integração (ISE)
description: Criar um ISE (Ambiente de Serviço de Integração) para que os aplicativos lógicos e contas de integração possam acessar as VNets (redes virtuais) do Azure, enquanto permanecem privados e isolados do Azure público ou “global”
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/24/2019
ms.openlocfilehash: cd611918b755ac3d5b6088ec6abe1711962921c7
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423234"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Conectar redes virtuais do Azure a partir dos Aplicativos Lógicos do Azure, usando um ISE (Ambiente de Serviço de Integração)

Para cenários em que seus aplicativos lógicos e contas de integração precisam de acesso a uma [Rede virtual do Azure](../virtual-network/virtual-networks-overview.md), crie um [*ISE* (Ambiente de Serviço de Integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Um ISE é um ambiente privado e isolado que usa armazenamento dedicado e outros recursos que são mantidos separados do serviço de aplicativos lógicos públicos ou "globais". Essa separação também reduz os impactos que outros locatários do Azure podem ter no desempenho de seus aplicativos.

Quando você cria um ISE, o Azure *injeta* esse ISE em sua rede virtual do Azure, que, em seguida, implanta o serviço de aplicativos lógicos em sua rede virtual. Ao criar um aplicativo lógico ou uma conta de integração, selecione o ISE como seu local. Sua conta de integração ou de aplicativo lógico, em seguida, pode acessar diretamente os recursos, como máquinas virtuais (VMs), servidores, sistemas e serviços em sua rede virtual.

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Para que os aplicativos lógicos e as contas de integração trabalhem juntos em um ISE, ambos devem usar o *mesmo ISE* como seu local.

Um ISE aumentou os limites de duração da execução, retenção de armazenamento, taxa de transferência, tempos limite de solicitação e resposta HTTP, tamanhos de mensagem e solicitações de conector personalizado. Para obter mais informações, consulte [limites e configuração para aplicativos lógicos do Azure](logic-apps-limits-and-config.md). Para saber mais sobre o ISEs, confira [acesso aos recursos de rede virtual do Azure de aplicativos lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Este artigo mostra como concluir essas tarefas:

* Certifique-se de que todas as portas necessárias em uma rede virtual estejam abertas para que o tráfego possa percorrer o ambiente do serviço de integração (ISE) entre as sub-redes nessa rede virtual.

* Crie seu ISE (ambiente de serviço de integração).

* Crie um aplicativo lógico que pode ser executado no ISE.

* Crie uma conta de integração para seus aplicativos lógicos no ISE.

> [!IMPORTANT]
> Os aplicativos lógicos, os gatilhos internos, as ações internas e os conectores executados no ISE usam um plano de preços diferente do plano de preços baseado em consumo. Para saber como o preço e a cobrança funcionam para o ISEs, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter taxas de preços, consulte [preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Uma [Rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Se ainda não tiver uma rede virtual, aprenda a [criar uma Rede virtual do Azure](../virtual-network/quick-create-portal.md).

  * Sua rede virtual deve ter quatro  sub-redes vazias para criar e implantar recursos no ISE. Você pode criar essas sub-redes com antecedência, ou pode esperar até criar seu ISE, em que você pode criar sub-redes ao mesmo tempo. Saiba mais sobre [os requisitos de sub-rede](#create-subnet).
  
    > [!NOTE]
    > Se você usar o [ExpressRoute](../expressroute/expressroute-introduction.md), que fornece uma conexão privada com os serviços de nuvem da Microsoft, deverá [criar uma tabela de rotas](../virtual-network/manage-route-table.md) que tenha a seguinte rota e vincular essa tabela a cada sub-rede usada pelo ISE:
    > 
    > **Nome**: <*nome da rota*><br>
    > **Prefixo de endereço**: 0.0.0.0/0<br>
    > **Próximo salto**: Internet

  * Certifique-se de que sua rede virtual disponibilize [essas portas](#ports) para que o ISE funcione corretamente e permaneça acessível.

* Se você quiser usar servidores DNS personalizados para sua rede virtual do Azure, [Configure esses servidores seguindo estas etapas](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) antes de implantar o ISE em sua rede virtual. Caso contrário, todas as vezes que você alterar o servidor DNS, será necessário também reiniciar o ISE, que é um recurso disponível na visualização pública do ISE.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="check-network-ports"></a>Verificar portas de rede

Quando você usa um ISE (ambiente do serviço de integração) com uma rede virtual, um problema de configuração comum é ter uma ou mais portas bloqueadas. Os conectores que você usa para criar conexões entre o ISE e o sistema de destino também podem ter seus próprios requisitos de porta. Por exemplo, ao comunicar-se com um sistema FTP usando o conector FTP, certifique-se de que a porta usada nesse sistema FTP, como a porta 21 para enviar comandos, está disponível.

Para controlar o tráfego nas sub-redes da rede virtual em que você implanta o ISE, você pode, opcionalmente, configurar [NSGs (grupos de segurança de rede)](../virtual-network/security-overview.md) em sua rede virtual filtrando o [tráfego de rede entre sub-redes](../virtual-network/tutorial-filter-network-traffic.md). Se você escolher essa rota, verifique se o ISE abre portas específicas, conforme descrito na tabela a seguir, na rede virtual que usa o NSGs. Se você tiver NSGs ou firewalls existentes em sua rede virtual, verifique se eles abriram essas portas. Dessa forma, o ISE permanece acessível e pode funcionar corretamente para que você não perca o acesso ao ISE. Caso contrário, se qualquer porta necessária estiver indisponível, o ISE para de funcionar.

Essas tabelas descrevem as portas na rede virtual que o ISE usa e onde essas portas são usadas. As [marcas de serviço do Gerenciador de recursos](../virtual-network/security-overview.md#service-tags) representam um grupo de prefixos de endereço IP que ajudam a minimizar a complexidade ao criar regras de segurança.

> [!IMPORTANT]
> Para a comunicação interna dentro de suas sub-redes, o ISE exige que você abra todas as portas dentro dessas sub-redes.

| Finalidade | Direction | Portas | Marca de serviço de origem | Marca de serviço de destino | Observações |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Comunicação a partir do Aplicativos Lógicos do Azure | Saída | 80 e 443 | VirtualNetwork | Internet | A porta depende do serviço externo com o qual o serviço de aplicativos lógicos se comunica |
| Active Directory do Azure | Saída | 80 e 443 | VirtualNetwork | AzureActiveDirectory | |
| Dependência de Armazenamento do Azure | Saída | 80 e 443 | VirtualNetwork | Armazenamento | |
| Comunicação entre sub-redes | Entrada e Saída | 80 e 443 | VirtualNetwork | VirtualNetwork | Para comunicação entre sub-redes |
| Comunicação para Aplicativos Lógicos do Azure | Entrada | 443 | Internet | VirtualNetwork | O endereço IP do computador ou serviço que chama qualquer gatilho de solicitação ou webhook que existe em seu aplicativo lógico. Fechar ou bloquear essa porta impede chamadas HTTP para aplicativos lógicos com gatilhos de solicitação.  |
| Histórico de execução do aplicativo lógico | Entrada | 443 | Internet | VirtualNetwork | O endereço IP do computador do qual você exibe o histórico de execução do aplicativo lógico. Embora o fechamento ou o bloqueio dessa porta não impeça que você exiba o histórico de execução, não é possível exibir as entradas e saídas de cada etapa nesse histórico de execução. |
| Gerenciamento de Conexão | Saída | 443 | VirtualNetwork  | Internet | |
| Publicar métricas e logs de diagnóstico | Saída | 443 | VirtualNetwork  | AzureMonitor | |
| Comunicação do Gerenciador de tráfego do Azure | Entrada | 443 | AzureTrafficManager | VirtualNetwork | |
| Designer de Aplicativos Lógicos - propriedades dinâmicas | Entrada | 454 | Internet  | VirtualNetwork | As solicitações são provenientes dos [endereços IP de entrada do ponto de extremidade de acesso](../logic-apps/logic-apps-limits-and-config.md#inbound)dos aplicativos lógicos nessa região. |
| Dependência de Gerenciamento de Serviço de Aplicativo | Entrada | 454 e 455 | AppServiceManagement | VirtualNetwork | |
| Implantação do conector | Entrada | 454 & 3443 | Internet  | VirtualNetwork | Necessário para implantar e atualizar conectores. Fechar ou bloquear essa porta faz com que implantações do ISE falhem e impeçam atualizações ou correções do conector. |
| Dependência do SQL do Azure | Saída | 1433 | VirtualNetwork | SQL |
| Azure Resource Health | Saída | 1886 | VirtualNetwork | Internet | Para a publicação do status de integridade para Resource Health |
| Gerenciamento de API - ponto de extremidade de gerenciamento | Entrada | 3443 | APIManagement  | VirtualNetwork | |
| Dependência do Log para agente de monitoramento e política do Hub de Eventos | Saída | 5672 | VirtualNetwork  | EventHub | |
| Acessar Instâncias do Cache do Azure para Redis entre Instâncias de Função | Entrada <br>Saída | 6379-6383 | VirtualNetwork  | VirtualNetwork | Além disso, para que o ISE funcione com o cache do Azure para Redis, você deve abrir essas [portas de saída e de entrada descritas no cache do Azure para perguntas frequentes do Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Entrada | * | AzureLoadBalancer | VirtualNetwork |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Criar seu ISE

Para criar seu ISE (ambiente de serviço de integração), siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **Criar um recurso**.
Na caixa de pesquisa, digite “ambiente de serviço de integração” como filtro.

   ![Criar novo recurso](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. No painel criação de Ambiente de Serviço de Integração, escolha **criar**.

   ![Escolha “Criar”](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Forneça esses detalhes para seu ambiente e, em seguida, escolha **Revisar + criar**, por exemplo:

   ![Fornecer detalhes do ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | **Assinatura** | Sim | <*Azure-subscription-name*> | A assinatura do Azure a ser usada para o ambiente |
   | **Grupo de recursos** | Sim | <*Azure-resource-group-name*> | O grupo de recursos do Azure no qual deseja criar seu ambiente |
   | **Nome do Ambiente de Serviço de Integração** | Sim | <*environment-name*> | O nome que o ambiente terá |
   | **Localidade** | Sim | <*Azure-datacenter-region*> | A região do datacenter do Azure na qual o ambiente será implantado |
   | **SKU** | Sim | **Premium** ou **desenvolvedor (sem SLA)** | A SKU do ISE a ser criada e usada. Para diferenças entre essas SKUs, consulte [SKUs do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). |
   | **Capacidade adicional** | Premium: <br>Sim <p><p>Developer: <br>Não aplicável | Premium: <br>0 a 10 <p><p>Developer: <br>Não aplicável | O número de unidades de processamento adicionais a serem usadas para este recurso do ISE. Para adicionar capacidade após a criação, consulte [adicionar capacidade do ISE](#add-capacity). |
   | **Rede virtual** | Sim | <*Azure-virtual-network-name*> | A rede virtual do Azure na qual você deseja injetar seu ambiente para que os aplicativos lógicos no ambiente possam acessar sua rede virtual. Se você não tiver uma rede, [primeiro crie uma rede virtual do Azure](../virtual-network/quick-create-portal.md). <p>**Importante**: Você pode executar essa injeção *apenas* quando cria seu ISE. |
   | **Sub-redes** | Sim | <*subnet-resource-list*> | Um ISE requer quatro  sub-redes vazias para criar e implantar recursos em seu ambiente. Para criar cada sub-rede, [siga as etapas nesta tabela](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **Criar sub-rede**

   Para criar e implantar recursos em seu ambiente, o ISE precisa de  quatro sub-redes vazias que não são delegadas a nenhum serviço. Você *não pode* alterar esses endereços de sub-rede depois de criar seu ambiente. Cada sub-rede deve atender a estes critérios:

   * Tem um nome que começa com um caractere alfabético ou um sublinhado e não tem estes caracteres: `<` `%`, `>` `&` `\\` `?`,,,,,`/`

   * Usa o [formato CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e um espaço de endereço da classe B.

   * Usa pelo menos um `/27` no espaço de endereço porque cada sub-rede deve ter *pelo menos* 32 endereços como um *mínimo*. Por exemplo:

     * `10.0.0.0/27`tem 32 endereços porque 2<sup>(32-27)</sup> é 2<sup>5</sup> ou 32.

     * `10.0.0.0/24`tem 256 endereços porque 2<sup>(32-24)</sup> é 2<sup>8</sup> ou 256.

     * `10.0.0.0/28`tem apenas 16 endereços e é muito pequeno porque 2<sup>(32-28)</sup> é 2<sup>4</sup> ou 16.

     Para saber mais sobre como calcular endereços, consulte [blocos CIDR de IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se você usar o [ExpressRoute](../expressroute/expressroute-introduction.md), lembre-se de [criar uma tabela de rotas](../virtual-network/manage-route-table.md) que tenha a rota a seguir e vincular essa tabela a cada sub-rede usada pelo ISE:

     **Nome**: <*nome da rota*><br>
     **Prefixo de endereço**: 0.0.0.0/0<br>
     **Próximo salto**: Internet

   1. Na lista **Sub-redes**, escolha **Gerenciar configuração de sub-rede**.

      ![Gerenciar configuração de sub-rede](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. No painel **Sub-redes**, escolha **Sub-rede**.

      ![Adicionar sub-rede](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. No painel **Adicionar sub-rede**, forneça estas informações.

      * **Nome**: o nome da sub-rede
      * **Intervalo de endereços (bloco CIDR)** : o intervalo da sub-rede em sua rede virtual e no formato CIDR

      ![Adicionar detalhes da sub-rede](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Quando terminar, escolha **OK**.

   1. Repita essas etapas para mais três sub-redes.

      > [!NOTE]
      > Se as sub-redes que você tentar criar não forem válidas, o portal do Azure mostrará uma mensagem, mas não bloqueará seu progresso.

1. Depois que o Azure validar com êxito suas informações do ISE, escolha **Criar**; por exemplo:

   ![Após a validação bem-sucedida, escolha "Criar"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   O Azure começa a implantar seu ambiente, mas esse processo pode levar *até duas horas* para ser concluído. Para verificar o status de implantação, na barra de ferramentas do Azure, escolha o ícone de notificações, o que abre o painel de notificações.

   ![Verifique o status da implantação](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se a implantação for concluída com êxito, o Azure mostrará esta notificação:

   ![Implantação com êxito](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   Caso contrário, siga as instruções de portal do Azure para solucionar problemas de implantação.

   > [!NOTE]
   > Se a implantação falhar ou você excluir o ISE, o Azure poderá levar até uma hora antes de liberar suas sub-redes. Esse atraso significa que você pode precisar esperar antes de reutilizar essas sub-redes em outro ISE.
   >
   > Se você excluir sua rede virtual, o Azure geralmente levará até duas horas antes de lançar suas sub-redes, mas essa operação poderá levar mais tempo. 
   > Ao excluir redes virtuais, certifique-se de que nenhum recurso ainda esteja conectado. 
   > Consulte [excluir rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Para exibir seu ambiente, escolha **Ir para o recurso** se o Azure não for automaticamente para seu ambiente após a conclusão da implantação.  

Para obter mais informações sobre como criar sub-redes, consulte [Adicionar uma sub-rede de rede virtual](../virtual-network/virtual-network-manage-subnet.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Criar aplicativo lógico – ISE

Para criar aplicativos lógicos que são executados em seu ISE (ambiente do serviço de integração), siga estas etapas:

1. Localize e abra o ISE, se ainda não estiver aberto. No menu ISE, em **configurações**, selecione **aplicativos** > lógicos**Adicionar**.

   ![Adicionar novo aplicativo lógico ao ISE](./media/connect-virtual-network-vnet-isolated-environment/add-logic-app-to-ise.png)

   - ou -

   No menu principal do Azure, selecione **Criar um recurso** > **Integração** > **Aplicativo Lógico**.

1. Forneça o nome, a assinatura do Azure e o grupo de recursos do Azure (novo ou existente) a serem usados para seu aplicativo lógico.

1. Na lista **local** , na seção **ambientes do serviço de integração** , selecione o ISE, por exemplo:

   ![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-ise.png)

   > [!IMPORTANT]
   > Se você quiser usar seus aplicativos lógicos com uma conta de integração, esses aplicativos lógicos e a conta de integração deverão usar o mesmo ISE.

1. Continue [criando seu aplicativo lógico da maneira usual](../logic-apps/quickstart-create-first-logic-app-workflow.md).

Para obter diferenças em como os gatilhos e as ações funcionam e como eles são rotulados quando você usa um ISE em comparação com o serviço de aplicativos lógicos globais, consulte [isolado versus global na visão geral do ISE](connect-virtual-network-vnet-isolated-environment-overview.md#difference).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Criar conta de integração – ISE

Com base no [SKU do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) selecionado na criação, seu ISE inclui um uso de conta de integração específico sem custo adicional. Os aplicativos lógicos que existem em um ambiente do serviço de integração (ISE) podem referenciar somente as contas de integração que existem no mesmo ISE. Portanto, para que uma conta de integração funcione com aplicativos lógicos em um ISE, tanto a conta de integração quanto os aplicativos lógicos devem usar o *mesmo ambiente* que o local. Para obter mais informações sobre contas de integração e ISEs [, consulte contas de](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment
)integração com o ISE.

Para criar uma conta de integração que usa um ISE, siga estas etapas:

1. Localize e abra o ISE, se ainda não estiver aberto. No menu ISE, em **configurações**, selecione **contas** > de integração**Adicionar**.

   ![Adicionar nova conta de integração ao ISE](./media/connect-virtual-network-vnet-isolated-environment/add-integration-account-to-ise.png)

   - ou -

   No menu principal do Azure, selecione **criar uma** > **conta de integração**de**integração** > de recursos.

1. Forneça o nome, a assinatura do Azure, o grupo de recursos do Azure (novo ou existente) e o tipo de preço a ser usado para sua conta de integração.

1. Na lista **local** , na seção **ambientes do serviço de integração** , selecione o mesmo ISE que seus aplicativos lógicos usam, por exemplo:

   ![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

1. Continue [criando sua conta de integração da maneira usual](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Adicionar capacidade do ISE

A unidade base do ISE Premium tem capacidade fixa, portanto, se você precisar de mais taxa de transferência, poderá adicionar mais unidades de escala, seja durante a criação ou depois. Você pode dimensionar automaticamente com base nas métricas de desempenho ou com base em várias unidades de processamento adicionais. Se você escolher dimensionamento automático com base nas métricas, poderá escolher entre vários critérios e especificar as condições de limite para atender a esses critérios. A SKU do desenvolvedor não inclui a capacidade de adicionar unidades de escala.

1. No portal do Azure, encontre o ISE.

1. Para examinar o uso e as métricas de desempenho do ISE, no menu principal do ISE, selecione **visão geral**.

   ![Exibir o uso do ISE](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Para configurar o dimensionamento automático, em **configurações**, selecione **escalar horizontalmente**. Na guia **Configurar** , escolha **Habilitar dimensionamento automático**.

   ![Ativar dimensionamento automático](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Para **nome da configuração de dimensionamento automático**, forneça um nome para sua configuração.

1. Na seção **padrão** , escolha **escala com base em uma métrica** ou **escala para uma contagem de instâncias específica**.

   * Se você escolher baseado em instância, insira o número de unidades de processamento entre 0 e 10, inclusive.

   * Se você escolher baseado em métrica, siga estas etapas:

     1. Na seção **regras** , escolha **Adicionar uma regra**.

     1. No painel **regra** de dimensionamento, configure os critérios e a ação a serem tomadas quando a regra for disparada.

     1. Quando terminar, escolha **Adicionar**.

1. Quando tiver concluído as configurações de dimensionamento automático, salve as alterações.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
