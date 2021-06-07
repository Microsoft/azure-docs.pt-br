---
title: Usar o Link Privado do Azure para conectar redes com segurança ao Azure Monitor
description: Usar o Link Privado do Azure para conectar redes com segurança ao Azure Monitor
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 76c6d7caf3c63779e12443304688192f7311720a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594556"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Usar o Link Privado do Azure para conectar redes com segurança ao Azure Monitor

O [Link Privado do Azure](../../private-link/private-link-overview.md) permite vincular com segurança os serviços PaaS do Azure a sua rede virtual usando pontos de extremidade privados. Em muitos serviços, basta configurar um ponto de extremidade por recurso. Já o Azure Monitor é uma constelação de diferentes serviços interconectados que trabalham juntos para monitorar suas cargas de trabalho. Como resultado, criamos um recurso chamado de Azure Monitor escopo de link privado (AMPLS). O AMPLS permite que você defina os limites de sua rede de monitoramento e conecte-se à sua rede virtual. Este artigo aborda quando usar e como configurar um Escopo de Link Privado do Azure Monitor.

## <a name="advantages"></a>Vantagens

Com o Link Privado, você pode:

- Conectar-se de maneira privada ao Azure Monitor sem abrir nenhum acesso de rede pública
- Garantir que o acesso aos seus dados de monitoramento esteja restrito a redes privadas autorizadas
- Impedir a exportação de dados de suas redes privadas definindo recursos específicos do Azure Monitor que se conectam por meio de seu ponto de extremidade privado
- Conectar sua rede local privada ao Azure Monitor com segurança usando ExpressRoute e Link Privado
- Manter todo o tráfego dentro da rede de backbone do Microsoft Azure

Para mais informações, confira [Principais benefícios do Link Privado](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Como ele funciona

Azure Monitor escopo de link privado (AMPLS) conecta pontos de extremidade privados (e os VNets em que estão contidos) a um ou mais recursos de Azure Monitor-Log Analytics espaços de trabalho e componentes Application Insights.

![Diagrama da topologia de recursos básica](./media/private-link-security/private-link-basic-topology.png)

* O ponto de extremidade privado em sua VNet permite que ele alcance Azure Monitor pontos de extremidade por meio de IPs privados do pool da sua rede, em vez de usar o para os IPs públicos desses pontos de extremidade. Isso permite que você continue usando seus recursos de Azure Monitor sem abrir sua VNet para tráfego de saída não obrigatório. 
* O tráfego do ponto de extremidade privado para seus recursos de Azure Monitor passará pelo backbone de Microsoft Azure e não roteado para redes públicas. 
* Você pode configurar cada um de seus espaços de trabalho ou componentes para permitir ou negar ingestão e consultas de redes públicas. Que fornece uma proteção em nível de recurso, para que você possa controlar o tráfego para recursos específicos.

> [!NOTE]
> Um único recurso do Azure Monitor pode pertencer a vários AMPLSs, mas não é possível conectar uma única VNet a mais de um AMPLS. 

## <a name="planning-your-private-link-setup"></a>Planejando a configuração do seu link privado

Antes de configurar sua configuração de link privado Azure Monitor, considere sua topologia de rede e, especificamente, sua topologia de roteamento de DNS. 

### <a name="the-issue-of-dns-overrides"></a>O problema de substituições de DNS
Alguns serviços de Azure Monitor usam pontos de extremidade globais, o que significa que eles atendem a solicitações direcionadas a qualquer espaço de trabalho/componente. Alguns exemplos são o ponto de extremidade de ingestão Application Insights e o ponto de extremidade de consulta de Application Insights e Log Analytics.

Quando você configura uma conexão de link privado, o DNS é atualizado para mapear Azure Monitor pontos de extremidade para endereços IP privados do intervalo de IP de sua VNet. Essa alteração substitui qualquer mapeamento anterior desses pontos de extremidade, o que pode ter implicações significativas, revisado abaixo. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor link privado se aplica a todos os Azure Monitor recursos – é tudo ou nada
Como alguns pontos de extremidade Azure Monitor são globais, é impossível criar uma conexão de link privado para um componente ou espaço de trabalho específico. Em vez disso, quando você configura um link privado para um único componente de Application Insights ou Log Analytics espaço de trabalho, os registros DNS são atualizados para **todos os** componentes do Application insights. Qualquer tentativa de ingerir ou consultar um componente passará pelo link privado e possivelmente falhará. Com relação à Log Analytics, os pontos de extremidade de ingestão e configuração são específicos do espaço de trabalho, o que significa que a configuração de link privado será aplicada somente aos espaços de trabalho especificados. A ingestão e a configuração de outros espaços de trabalho serão direcionadas para os pontos de extremidade de Log Analytics públicos padrão.

![Diagrama de substituições de DNS em uma única VNet](./media/private-link-security/dns-overrides-single-vnet.png)

Isso é verdade não apenas para uma VNet específica, mas para todos os VNets que compartilham o mesmo servidor DNS (consulte [o problema de substituições de DNS](#the-issue-of-dns-overrides)). Portanto, por exemplo, a solicitação para ingerir logs para qualquer Application Insights componente sempre será enviada por meio da rota de link privado. Os componentes que não estão vinculados ao AMPLS falharão na validação do link privado e não passarão por ele.

> [!NOTE]
> Para concluir: depois de configurar uma conexão de link privado com um único recurso, ela se aplica a Azure Monitor recursos em toda a rede. Para Application Insights recursos, isso é ' tudo ou nada '. Isso significa efetivamente que você deve adicionar todos os recursos de Application Insights na sua rede ao seu AMPLS ou a nenhum deles.
> 
> Para lidar com riscos de vazamento de dados, nossa recomendação é adicionar todos os Application Insights e Log Analytics recursos ao seu AMPLS e bloquear o tráfego de saída de suas redes o máximo possível.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor link privado se aplica a toda a sua rede
Algumas redes são compostas por vários VNets. Se o VNets usar o mesmo servidor DNS, ele substituirá os mapeamentos de DNS uns dos outros e poderá interromper a comunicação entre si com Azure Monitor (consulte [o problema de substituições de DNS](#the-issue-of-dns-overrides)). Por fim, somente a última VNet será capaz de se comunicar com Azure Monitor, já que o DNS mapeará Azure Monitor pontos de extremidade para IPs privados desse intervalo VNets (que talvez não possa ser acessado de outros VNets).

![Diagrama de substituições de DNS em vários VNets](./media/private-link-security/dns-overrides-multiple-vnets.png)

No diagrama acima, VNet 10.0.1. x se conecta primeiro ao AMPLS1 e mapeia os pontos de extremidade globais Azure Monitor para IPs de seu intervalo. Posteriormente, a VNet 10.0.2. x se conecta ao AMPLS2 e substitui o mapeamento DNS dos *mesmos pontos de extremidade globais* por IPS de seu intervalo. Como esses VNets não são emparelhados, a primeira VNet agora falha em atingir esses pontos de extremidade.

> [!NOTE]
> Para concluir: a instalação do AMPLS afeta todas as redes que compartilham as mesmas zonas DNS. Para evitar a substituição de mapeamentos de ponto de extremidade DNS de cada um, é melhor configurar um único ponto de extremidade privado em uma rede emparelhada (como uma VNet de Hub) ou separar as redes no nível de DNS (exemplo de para usando encaminhadores de DNS ou servidores DNS separados inteiramente).

### <a name="hub-spoke-networks"></a>Redes hub-spoke
Topologias hub-spoke podem evitar o problema de substituições de DNS definindo um link privado na VNet do Hub (principal), em vez de configurar um link privado para cada VNet separadamente. Essa configuração faz sentido, especialmente se os recursos de Azure Monitor usados pelo VNets de spoke são compartilhados. 

![Hub-e-spoke-single-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Você pode intencionalmente preferir criar links privados separados para seu spoke VNets, por exemplo, para permitir que cada VNet acesse um conjunto limitado de recursos de monitoramento. Nesses casos, você pode criar um ponto de extremidade privado dedicado e AMPLS para cada VNet, mas também deve verificar se eles não compartilham as mesmas zonas DNS para evitar substituições de DNS.


### <a name="consider-limits"></a>Considerar limites

Conforme listado em [restrições e limitações](#restrictions-and-limitations), o objeto AMPLS tem vários limites, mostrados na topologia abaixo:
* Cada VNet se conecta a apenas **um** objeto AMPLS.
* O AMPLS B está conectado a pontos de extremidade privados de dois VNets (VNet2 e VNet3), usando duas das 10 conexões de ponto de extremidades privadas possíveis.
* O AMPLS A se conecta a dois espaços de trabalho e a um componente do Application Insight, usando três das 50 conexões de recursos Azure Monitor possíveis.
* Workspace2 conecta-se ao AMPLS A e AMPLS B, usando duas das 5 conexões de AMPLS possíveis.

![Diagrama de limites de AMPLS](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Exemplo de conexão

Comece criando um recurso de Escopo de Link Privado do Azure Monitor.

1. No portal do Azure, acesse **Criar um recurso** e procure por **Escopo de Link Privado do Azure Monitor**.

   ![Localizar Azure Monitor escopo de link privado](./media/private-link-security/ampls-find-1c.png)

2. Selecione **criar**.
3. Escolha uma assinatura e um grupo de recursos.
4. Nomeie o AMPLS. É melhor usar um nome significativo e claro, como "AppServerProdTelem".
5. Selecione **Examinar + criar**. 

   ![Criar Azure Monitor escopo de link privado](./media/private-link-security/ampls-create-1d.png)

6. Permita que a validação seja aprovada e, em seguida, selecione **criar**.

### <a name="connect-azure-monitor-resources"></a>Conectar recursos do Azure Monitor

Conecte Azure Monitor recursos (Log Analytics espaços de trabalho e componentes do Application Insights) ao seu AMPLS.

1. Em seu escopo de vínculo privado Azure Monitor, selecione **Azure monitor recursos** no menu à esquerda. Selecione o botão **Adicionar**.
2. Adicione o workspace ou componente. A seleção do botão **Adicionar** abre uma caixa de diálogo onde você pode selecionar Azure monitor recursos. Você pode navegar por suas assinaturas e seus grupos de recursos ou digitar o nome deles para filtrá-los. Selecione o espaço de trabalho ou componente e selecione **aplicar** para adicioná-los ao seu escopo.

    ![Captura de tela com a experiência de usuário Selecionar um escopo](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> A exclusão de Azure Monitor recursos exige que você os Desconecte primeiro de todos os objetos AMPLS aos quais eles estão conectados. Não é possível excluir recursos conectados a um AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Conectar-se a um ponto de extremidade privado

Agora que os recursos estão conectados a seu AMPLS, crie um ponto de extremidade privado para conectar nossa rede. Você pode realizar essa tarefa no [Centro de Links Privados do portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) ou no Escopo de Link Privado do Azure Monitor, conforme feito neste exemplo.

1. Em seu recurso de escopo, selecione **conexões de ponto de extremidade privado** no menu de recursos à esquerda. Selecione **ponto de extremidade privado** para iniciar o processo de criação do ponto de extremidade. Você também pode aprovar conexões que foram iniciadas no centro de links privado aqui selecionando-as e selecionando **aprovar**.

    ![Captura de tela da experiência de usuário Conexões de Ponto de Extremidade Privado](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Escolha a assinatura, o grupo de recursos, o nome do ponto de extremidade e a região em que ele deve ser alocado. A região precisa ser a mesma região da VNet à qual você se conecta.

3. Selecione **Avançar: Recurso**. 

4. Na tela de recursos,

   a. Escolha a **Assinatura** que contém seu recurso de Escopo Privado do Azure Monitor. 

   b. Em **tipo de recurso**, escolha **Microsoft.insights/privateLinkScopes**. 

   c. Na lista suspensa de **recursos**, escolha o escopo de Link Privado que você criou anteriormente. 

   d. Selecione **Avançar: configuração >**.
      ![Captura de tela com a opção Criar Ponto de Extremidade privado](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. No painel de configuração,

   a.    Escolha a **rede virtual** e a **sub-rede** que você deseja conectar aos recursos do Azure Monitor. 
 
   b.    Na opção **Integrar à zona DNS privada**, escolha **Sim** e aguarde enquanto ela cria automaticamente uma Zona DNS Privada. As zonas DNS reais podem ser diferentes do que é mostrado na captura de tela abaixo. 
   > [!NOTE]
   > Se você escolher **não** e preferir gerenciar manualmente os registros DNS, primeiro conclua a configuração de seu link privado, incluindo esse ponto de extremidade privado e a configuração AMPLS. Em seguida, configure o DNS de acordo com as instruções em [configuração de DNS do ponto de extremidade privado do Azure](../../private-link/private-endpoint-dns.md). Certifique-se de não criar registros vazios como preparação para a configuração do seu link privado. Os registros DNS que você cria podem substituir as configurações existentes e impactar a conectividade com o Azure Monitor.
 
   c.    Selecione **Examinar + criar**.
 
   d.    Aguarde a aprovação de validação. 
 
   e.    Selecione **Criar**. 

    ![Captura de tela dos detalhes de selecionar ponto de extremidade particular.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Agora você criou um novo ponto de extremidade privado que está conectado a este AMPLS.

## <a name="review-and-validate-your-private-link-setup"></a>Examine e valide sua configuração de link privado

### <a name="reviewing-your-endpoints-dns-settings"></a>Examinando as configurações de DNS do ponto de extremidade
O ponto de extremidade privado que você criou agora deve ter quatro zonas DNS configuradas:

[![Captura de tela das zonas DNS do ponto de extremidade privado.](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-monitor-Azure-com
* privatelink-OMS-opinsights-Azure-com
* privatelink-ODS-opinsights-Azure-com
* privatelink-AgentSvc-Azure-Automation-net

> [!NOTE]
> Cada uma dessas zonas mapeia Azure Monitor pontos de extremidade específicos para IPs privados do pool de IPs da VNet. Os endereços IP mostrados nas imagens abaixo são apenas exemplos. Em vez disso, sua configuração deve mostrar IPs privados de sua própria rede.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-Azure-com
Essa zona abrange os pontos de extremidade globais usados pelo Azure Monitor, o que significa que esses pontos de extremidade atendem a solicitações que consideram todos os recursos, não um específico. Esta zona deve ter pontos de extremidade mapeados para:
* `in.ai` -(Application Insights ponto de extremidade de ingestão, você verá uma entrada global e uma regional
* `api` -Application Insights e Log Analytics ponto de extremidade de API
* `live` -Ponto de extremidade de métricas Application Insights Live
* `profiler` -Ponto de extremidade do Application Insights Profiler
* `snapshot`-Application Insights [ ![ captura de tela do ponto de extremidade de instantâneos do DNS privado monitor de zona-Azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-OMS-opinsights-Azure-com
Esta zona aborda o mapeamento específico do espaço de trabalho para pontos de extremidade do OMS. Você deve ver uma entrada para cada espaço de trabalho vinculado ao AMPLS conectado a esse ponto de extremidade privado.
[![Captura de tela da zona de DNS privado OMS-opinsights-Azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ODS-opinsights-Azure-com
Esta zona aborda o mapeamento específico do espaço de trabalho para os pontos de extremidade do ODS – o ponto final de ingestão de Log Analytics. Você deve ver uma entrada para cada espaço de trabalho vinculado ao AMPLS conectado a esse ponto de extremidade privado.
[![Captura de tela da zona de DNS privado ODS-opinsights-Azure-com.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-AgentSvc-Azure-Automation-net
Esta zona aborda o mapeamento específico do espaço de trabalho para os pontos de extremidade de automação do serviço do Agent. Você deve ver uma entrada para cada espaço de trabalho vinculado ao AMPLS conectado a esse ponto de extremidade privado.
[![Captura de tela de DNS privado agente de zona svc-Azure-Automation-net.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

### <a name="validating-you-are-communicating-over-a-private-link"></a>Validando você está se comunicando por meio de um link privado
* Para validar suas solicitações agora são enviadas por meio do ponto de extremidade privado e para os pontos de extremidades mapeados por IP privado, você pode examiná-las com um rastreamento de rede para ferramentas ou até mesmo seu navegador. Por exemplo, ao tentar consultar seu espaço de trabalho ou aplicativo, verifique se a solicitação é enviada para o IP privado mapeado para o ponto de extremidade da API, neste exemplo, é *172.17.0.9*.

    Observação: alguns navegadores podem usar outras configurações de DNS (consulte [configurações de DNS do navegador](#browser-dns-settings)). Verifique se as configurações de DNS se aplicam.

* Para certificar-se de que seu espaço de trabalho ou componente não está recebendo solicitações de redes públicas (não conectadas por meio de AMPLS), defina os sinalizadores de ingestão pública e consulta do recurso como *não* , conforme explicado em [gerenciar o acesso de fora dos escopos de links privados](#manage-access-from-outside-of-private-links-scopes).

* De um cliente em sua rede protegida, use `nslookup` para qualquer um dos pontos de extremidade listados em suas zonas DNS. Ele deve ser resolvido pelo servidor DNS para os IPs privados mapeados em vez dos IPs públicos usados por padrão.


## <a name="configure-log-analytics"></a>Configurar a análise de logs

Vá para o portal do Azure. No menu de recursos do espaço de trabalho Log Analytics, há um item chamado **isolamento de rede** no lado esquerdo. Nesse menu, é possível controlar dois estados diferentes.

![Isolamento da rede de LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Escopos de link privado Azure Monitor conectados
Todos os escopos conectados ao espaço de trabalho aparecem nesta tela. Conectar-se a escopos (AMPLSs) permite o tráfego de rede da rede virtual conectada a cada AMPLS para alcançar este espaço de trabalho. A criação de uma conexão aqui tem o mesmo efeito que configurá-la no escopo, como fizemos para [conectar Azure monitor recursos](#connect-azure-monitor-resources). Para adicionar uma nova conexão, selecione **Adicionar** e selecione o escopo do link Azure monitor privado. Selecione **aplicar** para conectar-se. Observe que um espaço de trabalho pode se conectar a 5 objetos AMPLS, conforme mencionado em [restrições e limitações](#restrictions-and-limitations). 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>Gerenciar o acesso de fora dos escopos de links privados
As configurações na parte inferior dessa página controlam o acesso de redes públicas, o que significa que as redes não estão conectadas por meio dos escopos listados acima. A configuração **permitir acesso à rede pública para ingestão** para **não** bloqueia a ingestão de logs de computadores fora dos escopos conectados. A configuração **permitir acesso à rede pública para consultas** para **não** bloquear consultas provenientes de computadores fora dos escopos. Isso inclui consultas executadas por meio de pastas de trabalho, painéis, experiências de cliente baseadas em API, informações no portal do Azure e muito mais. As experiências em execução fora do portal do Azure e essa consulta Log Analytics dados também precisam estar em execução na VNET vinculada ao privado.

### <a name="exceptions"></a>Exceções
Restringir o acesso, conforme explicado acima, não se aplica ao Azure Resource Manager e, portanto, tem as seguintes limitações:
* O acesso a dados-ao bloquear/permitir consultas de redes públicas se aplica à maioria das experiências de Log Analytics, algumas experiências consultam dados por meio de Azure Resource Manager e, portanto, não poderão consultar dados, a menos que as configurações de vínculo privado sejam aplicadas ao Gerenciador de recursos também (o recurso será lançado em breve). Os exemplos são Azure Monitor soluções, pastas de trabalho e informações e o conector do LogicApp.
* Gerenciamento de espaço de trabalho-configurações de espaço de trabalho e alterações de configuração (incluindo a ativação dessas configurações de acesso ativadas ou desativadas) são gerenciadas Azure Resource Manager pelo Restrinja o acesso ao gerenciamento de espaço de trabalho usando as funções, permissões, controles de rede e auditoria apropriados. Para obter mais informações, confira [Funções, permissões e segurança do Azure Monitor](../roles-permissions-security.md).

> [!NOTE]
> Os logs e as métricas carregados em um workspace por meio das [Configurações de Diagnóstico](../essentials/diagnostic-settings.md) passam por um canal privado e seguro da Microsoft e não são controlados por essas configurações.

### <a name="log-analytics-solution-packs-download"></a>Download de Log Analytics Solution packs

Para permitir que o agente do Log Analytics baixe pacotes de soluções, adicione os nomes de domínio totalmente qualificados à sua lista de permissões do firewall. 


| Ambiente de nuvem | Recurso de agente | Portas | Direção |
|:--|:--|:--|:--|
|Público do Azure     | scadvisorcontent.blob.core.windows.net         | 443 | Saída
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Saída
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Saída

## <a name="configure-application-insights"></a>Configurar o Application Insights

Vá para o portal do Azure. No Azure Monitor recurso de componente Application Insights, é um isolamento de **rede** de item de menu no lado esquerdo. Nesse menu, é possível controlar dois estados diferentes.

![Isolamento da rede de IA](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Primeiro, você pode conectar esse recurso do Application Insights aos escopos de Link Privado do Azure Monitor aos quais você tem acesso. Selecione **Adicionar** e selecione o **escopo de link Azure monitor privado**. Selecione aplicar para conectar-se. Todos os escopos conectados são exibidos nessa tela. Fazer essa conexão permite que o tráfego de rede nas redes virtuais conectadas alcance esse componente e tenha o mesmo efeito que conectá-lo do escopo como fizemos para [conectar Azure monitor recursos](#connect-azure-monitor-resources). 

Em segundo lugar, você pode controlar como esse recurso pode ser acessado de fora dos escopos de link privado (AMPLS) listados anteriormente. Se você definir **permitir acesso à rede pública para ingestão** como **não**, os computadores ou SDKs fora dos escopos conectados não poderão carregar dados para esse componente. Se você definir **permitir acesso de rede pública para consultas** como **não**, os computadores fora dos escopos não poderão acessar dados nesse Application insights recurso. Esses dados incluem acesso a logs de APM, métricas e fluxo de métricas em tempo real, além de experiências baseadas nesses parâmetros, como pastas de trabalho, dashboards, experiências de clientes baseadas em API de consulta, insights no portal do Azure e muito mais. 

> [!NOTE]
> As experiências de consumo que não são de portal também devem ser executadas na VNET vinculada privada que inclui as cargas de trabalho monitoradas.

Será necessário adicionar ao link privado recursos que hospedem as cargas de trabalho monitoradas. Por exemplo, consulte [usando pontos de extremidade privados para o aplicativo Web do Azure](../../app-service/networking/private-endpoint.md).

Essa restrição de acesso se aplica apenas aos dados no recurso Application Insights. No entanto, as alterações de configuração, incluindo a ativação ou desativação dessas configurações de acesso, são gerenciadas pelo Azure Resource Manager. Portanto, você deve restringir o acesso ao Resource Manager usando as funções, permissões, controles de rede, e auditoria apropriados. Para obter mais informações, confira [Funções, permissões e segurança do Azure Monitor](../roles-permissions-security.md).

> [!NOTE]
> Para proteger totalmente o Application Insights baseado no workspace, é necessário bloquear o acesso ao recurso do Application Insights e ao workspace subjacente do Log Analytics.
>
> O diagnóstico de nível de código (criador de perfil/depurador) precisa que você [forneça sua própria conta de armazenamento](../app/profiler-bring-your-own-storage.md) para dar suporte ao link privado.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Lidando com a natureza tudo ou nada dos links privados
Conforme explicado em [planejando a configuração do link privado](#planning-your-private-link-setup), a configuração de um link privado até mesmo para um único recurso afeta todos os Azure monitor recursos nessas redes e em outras redes que compartilham o mesmo DNS. Esse comportamento pode tornar o processo de integração desafiador. Considere as seguintes opções:

* Toda a abordagem mais simples e segura é adicionar todos os seus componentes de Application Insights ao AMPLS. Para os componentes que você ainda deseja acessar de outras redes, deixe os sinalizadores "permitir acesso público à Internet para ingestão/consulta" definidos como Sim (o padrão).
* Isolar redes – se você estiver (ou puder se alinhar) usando o spoke vnets, siga as orientações em [topologia de rede hub-spoke no Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Em seguida, configure as configurações de vínculo privado separadas no VNets de spoke relevante. Lembre-se também de separar zonas DNS, já que o compartilhamento de zonas DNS com outras redes spoke causará [substituições de DNS](#the-issue-of-dns-overrides).
* Usar zonas DNS personalizadas para aplicativos específicos – essa solução permite que você acesse os componentes selecionados Application Insights por meio de um link privado, mantendo todo o tráfego nas rotas públicas.
    - Configure uma [zona DNS privada personalizada](../../private-link/private-endpoint-dns.md)e dê a ela um nome exclusivo, como Internal.monitor.Azure.com
    - Criar um AMPLS e um ponto de extremidade privado e optar por **não** integrar automaticamente com o DNS privado
    - Acesse o ponto de extremidade privado-> configuração de DNS e examine o mapeamento sugerido de FQDNs.
    - Escolha Adicionar configuração e escolha a zona internal.monitor.azure.com que você acabou de criar
    - Adicionar registros para a ![ captura de tela acima da zona DNS configurada](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - Vá para o componente Application Insights e copie sua [cadeia de conexão](../app/sdk-connection-string.md).
    - Aplicativos ou scripts que desejam chamar esse componente sobre um link privado devem usar a cadeia de conexão com EndpointSuffix = Internal. monitor. Azure. com
* Mapear pontos de extremidade por meio de arquivos hosts em vez de DNS – para ter um link privado acesso somente de um computador/VM específico em sua rede:
    - Configurar um AMPLS e um ponto de extremidade privado e optar por **não** integrar automaticamente com o DNS privado 
    - Configurar os registros a acima em um computador que executa o aplicativo no arquivo de hosts


## <a name="use-apis-and-command-line"></a>Usar APIs e linha de comando

Você pode automatizar o processo descrito anteriormente usando modelos de Azure Resource Manager, REST e interfaces de linha de comando.

Para criar e gerenciar escopos de link privado, use a [API REST](/rest/api/monitor/private%20link%20scopes%20(preview)) ou [CLI do Azure (AZ monitor privado-link-Scope)](/cli/azure/monitor/private-link-scope).

Para gerenciar o acesso à rede, use os sinalizadores `[--ingestion-access {Disabled, Enabled}]` e `[--query-access {Disabled, Enabled}]` nos [Workspaces do Log Analytics](/cli/azure/monitor/log-analytics/workspace) ou [Componentes do Application Insights](/cli/azure/ext/application-insights/monitor/app-insights/component).

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Coletar logs personalizados e log do IIS sobre o link privado

As contas de armazenamento são usadas no processo de ingestão de logs personalizados. Por padrão, são usadas contas de armazenamento gerenciadas por serviços. No entanto, para ingerir logs personalizados em links privados, você deve usar suas contas de armazenamento e associá-las aos workspaces do Log Analytics. Confira mais detalhes sobre como configurar essas contas usando a [linha de comando](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Para obter mais informações sobre como associar sua conta de armazenamento, confira [Contas de armazenamento de propriedade do cliente para ingestão de log](private-storage.md)

## <a name="restrictions-and-limitations"></a>Restrições e limitações

### <a name="ampls"></a>AMPLS
O objeto AMPLS tem vários limites que você deve considerar ao planejar sua configuração de link privado:

* Uma VNet só pode se conectar a um objeto AMPLS. Isso significa que o objeto AMPLS deve fornecer acesso a todos os Azure Monitor recursos aos quais a VNet deve ter acesso.
* Um recurso de Azure Monitor (espaço de trabalho ou componente Application Insights) pode se conectar a 5 AMPLSs no máximo.
* Um objeto AMPLS pode se conectar a 50 Azure Monitor recursos no máximo.
* Um objeto AMPLS pode se conectar a 10 pontos de extremidade privados no máximo.

Veja [considerar os limites](#consider-limits) para uma análise mais profunda desses limites.

### <a name="agents"></a>Agentes

As versões mais recentes dos agentes do Windows e do Linux devem ser usadas para dar suporte à ingestão segura para Log Analytics espaços de trabalho. As versões mais antigas não podem carregar dados de monitoramento em uma rede privada.

**Agente Windows do Log Analytics**

Use o agente de Log Analytics versão 10.20.18038.0 ou posterior.

**Agente Linux do Log Analytics**

Use o agente na versão 1.12.25 ou posterior. Se você não puder, execute os comandos a seguir em sua VM.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Portal do Azure

Para usar as experiências do portal do Azure Monitor, como Application Insights e Log Analytics, você precisa permitir que o portal do Azure e as extensões do Azure Monitor estejam acessíveis nas redes privadas. Adicione as [marcas de serviço](../../firewall/service-tags.md) **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor. FirstParty** e **AzureFrontDoor. frontend** ao seu grupo de segurança de rede.

### <a name="querying-data"></a>Consultando dados
O [ `externaldata` operador](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) não tem suporte em um link privado, pois lê dados de contas de armazenamento, mas não garante que o armazenamento seja acessado em particular.

### <a name="programmatic-access"></a>Acesso Programático

Para usar a API REST, a [CLI](/cli/azure/monitor) ou o PowerShell com Azure monitor em redes privadas, adicione as [marcas de serviço](../../virtual-network/service-tags-overview.md)  **AzureActiveDirectory** e **AzureResourceManager** ao seu firewall.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Downloads do SDK do Application Insights em uma rede de distribuição de conteúdo

Agrupe o código JavaScript em seu script para que o navegador não tente baixar o código de uma CDN. Confira um exemplo no [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Configurações de DNS do navegador

Se você estiver se conectando aos recursos do Azure Monitor por meio de um link privado, o tráfego para esses recursos deverá passar pelo ponto de extremidade privado configurado em sua rede. Para habilitar o ponto de extremidade privado, atualize suas configurações de DNS conforme explicado em [conectar-se a um ponto de extremidade privado](#connect-to-a-private-endpoint). Alguns navegadores usam suas próprias configurações de DNS em vez daqueles que você definiu. O navegador pode tentar se conectar a Azure Monitor pontos de extremidade públicos e ignorar totalmente o link privado. Verifique se as configurações de navegadores não substituem ou armazenam configurações de DNS antigas. 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [armazenamento privado](private-storage.md)