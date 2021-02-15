---
title: Continuidade dos negócios e recuperação de desastres
description: Projete sua estratégia para proteger dados, recuperar rapidamente de eventos de interrupção, restaurar recursos exigidos por funções comerciais críticas e manter a continuidade dos negócios para aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 0a36cb468ebcb77c0614bffd0afc392df3655c20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89658205"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Continuidade dos negócios e recuperação de desastre para aplicativos lógicos do Azure

Para ajudar a reduzir o impacto e os efeitos que os eventos imprevisíveis têm em seus negócios e clientes, certifique-se de que você tenha uma solução de [Dr ( *recuperação de desastres* )](https://en.wikipedia.org/wiki/Disaster_recovery) em vigor para que possa proteger os dados, restaurar rapidamente os recursos que dão suporte a funções comerciais críticas e manter as operações em execução para manter a [ *continuidade dos negócios* (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning). Por exemplo, as interrupções podem incluir interrupções, perdas em infraestrutura subjacente ou componentes como recursos de armazenamento, rede ou computação, falhas de aplicativos irrecuperáveis ou até mesmo uma perda completa de datacenter. Tendo uma solução de BCDR (continuidade de negócios e recuperação de desastre) pronta, sua empresa ou organização pode responder mais rapidamente a interrupções, planejadas ou não planejadas, além de reduzir o tempo de inatividade para seus clientes.

Este artigo fornece diretrizes e estratégias de BCDR que você pode aplicar ao criar fluxos de trabalho automatizados usando o [aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md). Os fluxos de trabalho do aplicativo lógico ajudam você a integrar e orquestrar dados com mais facilidade entre aplicativos, serviços de nuvem e sistemas locais, reduzindo a quantidade de código que você precisa escrever. Ao planejar o BCDR, certifique-se de considerar não apenas seus aplicativos lógicos, mas também os recursos do Azure que você usa com seus aplicativos lógicos:

* [Conexões](../connectors/apis-list.md) que você cria de aplicativos lógicos para outros aplicativos, serviços e sistemas. Para obter mais informações, consulte [conexões a recursos](#resource-connections) mais adiante neste tópico.

* [Gateways de dados locais](../logic-apps/logic-apps-gateway-connection.md) que são recursos do Azure que você cria e usa em seus aplicativos lógicos para acessar dados em sistemas locais. Cada recurso de gateway representa uma [instalação de gateway de dados](../logic-apps/logic-apps-gateway-install.md) separada em um computador local. Para obter mais informações, consulte [gateways de dados locais](#on-premises-data-gateways) mais adiante neste tópico.

* [Contas de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) nas quais você define e armazena os artefatos que os aplicativos lógicos usam para cenários de [integração empresarial B2B (entre empresas)](../logic-apps/logic-apps-enterprise-integration-overview.md) . Por exemplo, você pode [Configurar a recuperação de desastre entre regiões para contas de integração](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md).

* [Ambientes de serviço de integração (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) em que você cria aplicativos lógicos que são executados em uma instância de tempo de execução de aplicativos lógicos isolados em uma rede virtual do Azure. Esses aplicativos lógicos podem, então, acessar os recursos protegidos por trás de um firewall nessa rede virtual.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Locais primários e secundários

Cada aplicativo lógico precisa especificar o local que você deseja usar para a implantação. Esse local é uma região pública no Azure multilocatário global, como "oeste dos EUA", ou um ISE (ambiente do serviço de integração) que você criou e implantou anteriormente em uma rede virtual do Azure. A execução de aplicativos lógicos em um ISE é semelhante à execução de aplicativos lógicos em uma região global do Azure, o que significa que sua estratégia de recuperação de desastre pode se aplicar a ambos os cenários. No entanto, ISEs têm outras considerações, como configurar o acesso a recursos que estão disponíveis somente para o ISEs.

> [!NOTE]
> Se seu aplicativo lógico também trabalha com artefatos B2B, como parceiros comerciais, contratos, esquemas, mapas e certificados, que são armazenados em uma conta de integração, a conta de integração e os aplicativos lógicos devem especificar o mesmo local.

Essa estratégia de recuperação de desastres concentra-se na configuração de seu aplicativo lógico primário para fazer [*failover*](https://en.wikipedia.org/wiki/Failover) em um aplicativo lógico de backup ou em espera em um local alternativo em que os aplicativos lógicos do Azure também estão disponíveis. Dessa forma, se o primário sofrer perdas, interrupções ou falhas, o secundário poderá assumir o trabalho. Essa estratégia requer que seu aplicativo lógico secundário e os recursos dependentes já estejam implantados e prontos no local alternativo.

Se você seguir boas práticas de DevOps, você já usa [Azure Resource Manager modelos](../azure-resource-manager/management/overview.md) para definir e implantar seus aplicativos lógicos e seus recursos dependentes. Os modelos do Resource Manager oferecem a capacidade de usar uma única definição de implantação e, em seguida, usar arquivos de parâmetro para fornecer os valores de configuração a serem usados para cada destino de implantação. Esse recurso significa que você pode implantar o mesmo aplicativo lógico em ambientes diferentes, por exemplo, desenvolvimento, teste e produção. Você também pode implantar o mesmo aplicativo lógico em diferentes regiões do Azure ou ISEs, que oferece suporte a estratégias de recuperação de desastres que usam [regiões emparelhadas](../best-practices-availability-paired-regions.md).

Para a estratégia de failover, seus aplicativos lógicos e locais devem atender a esses requisitos:

* A instância do aplicativo lógico secundário tem acesso aos mesmos aplicativos, serviços e sistemas que a instância do aplicativo lógico primário.

* Ambas as instâncias do aplicativo lógico têm o mesmo tipo de host. Portanto, ambas as instâncias são implantadas em regiões no Azure multilocatário global, ou ambas as instâncias são implantadas em ISEs, o que permite que seus aplicativos lógicos acessem diretamente os recursos em uma rede virtual do Azure. Para obter as práticas recomendadas e obter mais informações sobre regiões emparelhadas para BCDR, consulte [continuidade dos negócios e recuperação de desastres (BCDR): regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

  Por exemplo, os locais primário e secundário devem ser ISEs quando o aplicativo lógico primário é executado em um ISE e usa [conectores com controle de versão do ISE](../connectors/apis-list.md#ise-connectors), ações http para chamar recursos na rede virtual do Azure ou ambos. Nesse cenário, seu aplicativo lógico secundário também deve ter uma configuração semelhante no local secundário como o aplicativo lógico primário.

  > [!NOTE]
  > Para cenários mais avançados, você pode misturar vários locatários do Azure e um ISE como locais. No entanto, certifique-se de considerar e entender as [diferenças entre o modo como os aplicativos lógicos são executados em um ISE em vez do Azure com vários locatários](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

* Se você usar o ISEs, [Verifique se eles foram expandidos ou têm capacidade suficiente](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) para lidar com a carga.

#### <a name="example-multi-tenant-azure"></a>Exemplo: vários locatários do Azure

Este exemplo mostra as instâncias de aplicativo lógico primário e secundário, que são implantadas em regiões separadas no Azure multilocatário global para esse cenário. Um [modelo do Gerenciador de recursos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) único define as instâncias do aplicativo lógico e os recursos dependentes exigidos por esses aplicativos lógicos. Arquivos de parâmetro separados especifique os valores de configuração a serem usados para cada local de implantação:

![Instâncias de aplicativo lógico primário e secundário em locais separados](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Exemplo: ambiente do serviço de integração

Este exemplo mostra as instâncias de aplicativo lógico primário e secundário anteriores, mas implantadas em ISEs separadas. Um modelo do Gerenciador de recursos único define as duas instâncias do aplicativo lógico, os recursos dependentes exigidos por esses aplicativos lógicos e o ISEs como os locais de implantação. Arquivos de parâmetro separados definem os valores de configuração a serem usados para implantação em cada local:

![Aplicativos lógicos primários e secundários em locais diferentes](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Conexões com recursos

Os aplicativos lógicos do Azure fornecem [gatilhos e ações internos, além de centenas de conectores gerenciados](../connectors/apis-list.md) que seu aplicativo lógico pode usar para trabalhar com outros aplicativos, serviços, sistemas e outros recursos, como contas de armazenamento do Azure, SQL Server bancos de dados, contas de email corporativas ou de estudante e assim por diante. Se seu aplicativo lógico precisar de acesso a esses recursos, você criará conexões que autenticam o acesso a esses recursos. Cada conexão é um recurso do Azure separado que existe em um local específico e não pode ser usado por recursos em outros locais.

Para sua estratégia de recuperação de desastres, considere os locais onde os recursos dependentes existem em relação às instâncias do aplicativo lógico:

* A instância primária e os recursos dependentes existem em locais diferentes. Nesse caso, sua instância secundária pode se conectar aos mesmos recursos ou pontos de extremidade dependentes. No entanto, você deve criar conexões especificamente para sua instância secundária. Dessa forma, se o local principal ficar indisponível, as conexões secundárias não serão afetadas.

  Por exemplo, suponha que seu aplicativo lógico primário se conecte a um serviço externo, como o Salesforce. Normalmente, a disponibilidade e o local do serviço externo são independentes da disponibilidade do seu aplicativo lógico. Nesse caso, sua instância secundária pode se conectar ao mesmo serviço, mas deve ter sua própria conexão.

* A instância primária e os recursos dependentes existem no mesmo local. Nesse caso, os recursos dependentes devem ter backups ou versões replicadas em um local diferente para que sua instância secundária ainda possa acessar esses recursos.

  Por exemplo, suponha que seu aplicativo lógico primário se conecte a um serviço que esteja no mesmo local ou região, por exemplo, banco de dados SQL do Azure. Se toda a região ficar indisponível, o serviço de banco de dados SQL do Azure nessa região também estará provavelmente indisponível. Nesse caso, você desejaria que sua instância secundária use um banco de dados replicado ou de backup junto com uma conexão separada com esse banco de dados.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Gateway de dados local

Se seu aplicativo lógico for executado no Azure multilocatário e precisar de acesso a recursos locais, como bancos de dados do SQL Server, você precisará instalar o [Gateway de data](../logic-apps/logic-apps-gateway-install.md) local em um computador local. Em seguida, você pode criar um recurso de gateway de dados no portal do Azure para que seu aplicativo lógico possa usar o gateway quando você criar uma conexão com o recurso.

O recurso de gateway de dados é associado a um local ou a uma região do Azure, assim como o recurso de aplicativo lógico. Em sua estratégia de recuperação de desastres, certifique-se de que o gateway de dados permaneça disponível para uso pelo seu aplicativo lógico. Você pode [habilitar a alta disponibilidade para o gateway](../logic-apps/logic-apps-gateway-install.md#high-availability) quando tiver várias instalações de gateway.

> [!NOTE]
> Se seu aplicativo lógico for executado em um ambiente do serviço de integração (ISE) e usar somente conectores com controle de versão do ISE para fontes de dados locais, você não precisará do gateway de dados porque os conectores do ISE fornecem acesso direto ao recurso local.
>
> Se nenhum conector com controle de versão do ISE estiver disponível para o recurso local desejado, seu aplicativo lógico ainda poderá criar a conexão usando um conector não ISE, que é executado no Azure multilocatário global, não em seu ISE. No entanto, essa conexão requer o gateway de dados local.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Funções ativo-ativo versus ativo-passivo

Você pode configurar seus locais primários e secundários para que as instâncias do aplicativo lógico nesses locais possam desempenhar essas funções:

| Função primária-secundária | Descrição |
|------------------------|-------------|
| *Ativo-ativo* | As instâncias do aplicativo lógico primário e secundário em ambos os locais manipulam solicitações ativamente seguindo qualquer um destes padrões: <p><p>- *Balanceamento de carga*: você pode fazer com que ambas as instâncias escutem um ponto de extremidade e equilibre o tráfego de carga para cada instância, conforme necessário. <p>- *Consumidores concorrentes*: você pode fazer com que ambas as instâncias atuem como consumidores concorrentes para que as instâncias concorram para mensagens de uma fila. Se uma instância falhar, a outra instância assumirá a carga de trabalho. |
| *Ativo-passivo* | A instância do aplicativo lógico principal manipula ativamente toda a carga de trabalho, enquanto a instância secundária é passiva (desabilitada ou inativa). O secundário aguarda um sinal de que o primário está indisponível ou não está funcionando devido à interrupção ou falha e assume a carga de trabalho como a instância ativa. |
| Combinação | Alguns aplicativos lógicos desempenham uma função ativo-ativo, enquanto outros aplicativos lógicos desempenham uma função ativa-passiva. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Exemplos de ativo-ativo

Esses exemplos mostram a configuração ativo-ativo em que ambas as instâncias do aplicativo lógico lidam ativamente com solicitações ou mensagens. Algum outro sistema ou serviço distribui as solicitações ou mensagens entre instâncias, por exemplo, uma destas opções:

* Um balanceador de carga "físico", como uma parte do hardware que roteia o tráfego

* Um balanceador de carga "soft", como [Azure Load Balancer](../load-balancer/load-balancer-overview.md) ou [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md). Com o gerenciamento de API, você pode especificar políticas que determinam como balancear a carga do tráfego de entrada. Ou você pode usar um serviço que dá suporte ao controle de estado, por exemplo, o [barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md).

  Embora este exemplo mostre principalmente Azure Load Balancer, você pode usar a opção que melhor atenda às necessidades do seu cenário:

  ![Configuração "ativa-ativa" que usa um balanceador de carga ou um serviço com estado](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Cada instância de aplicativo lógico atua como um consumidor e ambas as instâncias concorrem para mensagens de uma fila:

  ![Configuração "ativa-ativa" que usa "consumidores concorrentes"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Exemplos ativos-passivos

Este exemplo mostra a configuração ativa-passiva em que a instância do aplicativo lógico primário está ativa em um único local, enquanto a instância secundária permanece inativa em outro local. Se o principal enfrentar uma interrupção ou falha, você poderá fazer com que um operador execute um script que ative o secundário para assumir a carga de trabalho.

![Configuração "ativa-passiva" que usa "consumidores concorrentes"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Combinação com ativo-ativo e ativo-passivo

Este exemplo mostra uma configuração combinada em que o local principal tem ambas as instâncias de aplicativo lógico ativo, enquanto o local secundário tem instâncias de aplicativo lógico ativo-passivo. Se o local principal sofrer uma interrupção ou falha, o aplicativo lógico ativo no local secundário, que já está manipulando uma carga de trabalho parcial, poderá assumir toda a carga de trabalho.

* No local principal, um aplicativo lógico ativo escuta uma fila do barramento de serviço do Azure em busca de mensagens, enquanto outro aplicativo lógico ativo verifica emails usando um gatilho de sondagem do Outlook do Office 365.

* No local secundário, um aplicativo lógico ativo funciona com o aplicativo lógico no local principal ouvindo e competindo mensagens da mesma fila do barramento de serviço. Enquanto isso, um aplicativo lógico inativo passivo aguarda em espera para verificar emails quando o local primário fica indisponível, mas está *desabilitado* para evitar a releitura de emails.

![Combinação "ativa-passiva" e "ativa-passiva" que usa gatilhos de recorrência](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Estado e histórico do aplicativo lógico

Quando seu aplicativo lógico é disparado e começa a ser executado, o estado do aplicativo é armazenado no mesmo local em que o aplicativo foi iniciado e não é transferível para outro local. Se ocorrer uma falha ou interrupção, qualquer instância de fluxo de trabalho em andamento será abandonada. Quando você tem um local primário e secundário configurado, novas instâncias de fluxo de trabalho começam a ser executadas no local secundário.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Reduzir instâncias em andamento abandonadas

Para minimizar o número de instâncias de fluxo de trabalho em andamento abandonadas, você pode escolher entre vários padrões de mensagem que podem ser implementados, por exemplo:

* [Padrão de guia de roteamento fixo](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Esse padrão de mensagem empresarial que divide um processo de negócios em estágios menores. Para cada estágio, você configura um aplicativo lógico que manipula a carga de trabalho para esse estágio. Para se comunicar entre si, seus aplicativos lógicos usam um protocolo de mensagens assíncrono, como filas ou tópicos do barramento de serviço do Azure. Ao dividir um processo em estágios menores, você reduz o número de processos de negócios que podem ficar presos em uma instância de aplicativo lógico com falha. Para obter mais informações gerais sobre esse padrão, consulte [padrões de integração corporativa-guia de roteamento](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  Este exemplo mostra um padrão de guia de roteamento em que cada aplicativo lógico representa um estágio e usa uma fila do barramento de serviço para se comunicar com o próximo aplicativo lógico no processo.

  ![Dividir um processo comercial em estágios representados por aplicativos lógicos, que se comunicam entre si usando filas do barramento de serviço do Azure](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Se as instâncias de aplicativo lógico primário e secundário seguirem o mesmo padrão de guia de roteamento em seus locais, você poderá implementar o [padrão de consumidores concorrentes](/azure/architecture/patterns/competing-consumers) Configurando [funções ativas-ativas](#roles) para essas instâncias.

* [Padrão do Gerenciador de processos (agente)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Bloqueio de inspeção sem o padrão de tempo limite](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Acesso ao gatilho e ao histórico de execuções

Para obter mais informações sobre as execuções de fluxo de trabalho anteriores do aplicativo lógico, você pode examinar o gatilho do aplicativo e o histórico de execuções. O histórico de execução do histórico de um aplicativo lógico é armazenado no mesmo local ou região em que o aplicativo lógico foi executado, o que significa que não é possível migrar esse histórico para um local diferente. Se a instância primária fizer failover para uma instância secundária, você só poderá acessar o gatilho de cada instância e o histórico de execuções nos respectivos locais em que essas instâncias foram executadas. No entanto, você pode obter informações independentes de local sobre o histórico do aplicativo lógico Configurando seus aplicativos lógicos para enviar eventos de diagnóstico para um espaço de trabalho do Azure Log Analytics. Você pode examinar a integridade e o histórico entre aplicativos lógicos que são executados em vários locais.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Diretrizes de tipo de gatilho

O tipo de gatilho que você usa em seus aplicativos lógicos determina suas opções de como você pode configurar aplicativos lógicos entre locais em sua estratégia de recuperação de desastre. Aqui estão os tipos de gatilho disponíveis que você pode usar em aplicativos lógicos:

* [Gatilho de recorrência](#recurrence-trigger)
* [Gatilho de sondagem](#polling-trigger)
* [Gatilho de solicitação](#request-trigger)
* [Gatilho de webhook](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Gatilho de recorrência

O gatilho de **recorrência** é independente de qualquer serviço ou ponto de extremidade específico e é acionado exclusivamente com base em um agendamento especificado e nenhum outro critério, por exemplo:

* Uma frequência fixa e um intervalo, como a cada 10 minutos
* Uma agenda mais avançada, como a última segunda-feira de cada mês às 5:00 PM

Quando seu aplicativo lógico começa com um gatilho de recorrência, você precisa configurar suas instâncias de aplicativo lógico primário e secundário com as [funções ativo-passivo](#roles). Para reduzir o RTO ( *objetivo de tempo de recuperação* ), que se refere à duração de destino da restauração de um processo de negócios após uma interrupção ou desastre, você pode configurar suas instâncias de aplicativo lógico com uma combinação de [funções ativas-passiva](#roles) e [funções passivas ativas](#roles). Nessa configuração, você divide a agenda entre locais.

Por exemplo, suponha que você tenha um aplicativo lógico que precisa ser executado a cada 10 minutos. Você pode configurar seus aplicativos lógicos e locais para que, se o local principal ficar indisponível, o local secundário possa assumir o trabalho:

![Combinação "ativo-passivo" e "passiva-ativa" que usa gatilhos de recorrência](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* No local principal, configure [funções ativas-passivas](#roles) para esses aplicativos lógicos:

  * Para o aplicativo lógico habilitado *ativo* , defina o gatilho de recorrência para iniciar na parte superior da hora e repita a cada 20 minutos, por exemplo, 9:00 am, 9:20 am e assim por diante.

  * Para o aplicativo lógico desabilitado *passivo* , defina o gatilho de recorrência como o mesmo agendamento, mas comece com 10 minutos após a hora e repita a cada 20 minutos, por exemplo, 9:10 am, 9:30 am e assim por diante.

* No local secundário, configure [passivo-ativo](#roles) para estes aplicativos lógicos:

  * Para o aplicativo lógico desabilitado *passivo* , defina o gatilho de recorrência como o mesmo agendamento que o aplicativo lógico ativo no local principal, que está no início da hora e repita a cada 20 minutos, por exemplo, 9:00 am, 9:10 am e assim por diante.

  * Para o aplicativo lógico habilitado *ativo* , defina o gatilho de recorrência como o mesmo agendamento que o aplicativo lógico passivo no local principal, que deve começar com 10 minutos após a hora e repetir a cada 20 minutos, por exemplo, 9:10 am, 9:20 am e assim por diante.

Agora, se ocorrer um evento de interrupção no local primário, ative o aplicativo lógico passivo no local alternativo. Dessa forma, se encontrar a falha levar tempo, essa configuração limitará o número de recorrências perdidas durante esse atraso.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Gatilho de sondagem

Para verificar regularmente se novos dados para processamento estão disponíveis de um serviço ou ponto de extremidade específico, seu aplicativo lógico pode usar um gatilho de *sondagem* que chame repetidamente o serviço ou o ponto de extremidade com base em uma agenda de recorrência fixa. Os dados que o serviço ou o ponto de extremidade fornece podem ter um destes tipos:

* Dados estáticos, que descrevem os dados que estão sempre disponíveis para leitura
* Dados voláteis, que descrevem os dados que não estão mais disponíveis após a leitura

Para evitar a leitura repetida dos mesmos dados, seu aplicativo lógico precisa se lembrar de quais dados foram lidos anteriormente mantendo o estado no lado do cliente ou no servidor, no serviço ou no lado do sistema.

* Aplicativos lógicos que funcionam com gatilhos de uso de estado do lado do cliente que podem manter o estado.

  Por exemplo, um gatilho que lê uma nova mensagem de uma caixa de entrada de email requer que o gatilho possa se lembrar da mensagem de leitura mais recente. Dessa forma, o gatilho inicia o aplicativo lógico somente quando chega a próxima mensagem não lida.

* Os aplicativos lógicos que funcionam com o servidor, serviço ou estado do lado do sistema usam valores de propriedade ou configurações que estão no servidor, no serviço ou no lado do sistema.

  Por exemplo, um gatilho baseado em consulta que lê uma linha de um banco de dados requer que a linha tenha uma `isRead` coluna definida como `FALSE` . Toda vez que o gatilho lê uma linha, o aplicativo lógico atualiza essa linha alterando a `isRead` coluna de `FALSE` para `TRUE` .

  Essa abordagem do lado do servidor funciona de forma semelhante para filas ou tópicos do barramento de serviço que têm semântica de enfileiramento em que um gatilho pode ler e bloquear uma mensagem enquanto o aplicativo lógico processa a mensagem. Quando o aplicativo lógico conclui o processamento, o gatilho exclui a mensagem da fila ou do tópico.

De uma perspectiva de recuperação de desastres, quando você configura as instâncias primária e secundária do aplicativo lógico, certifique-se de considerar esses comportamentos com base em se o aplicativo lógico rastreia o estado no lado do cliente ou no lado do servidor:

* Para um aplicativo lógico que funciona com o estado do lado do cliente, certifique-se de que seu aplicativo lógico não leia a mesma mensagem mais de uma vez. Apenas um local pode ter uma instância de aplicativo lógico ativa em qualquer momento específico. Verifique se a instância do aplicativo lógico no local alternativo está inativa ou desabilitada até que a instância primária faça failover para o local alternativo.

  Por exemplo, o gatilho do Outlook do Office 365 mantém o estado do lado do cliente e controla o carimbo de data/hora para o email lido mais recentemente para evitar a leitura de uma duplicata.

* Para um aplicativo lógico que funciona com o estado do lado do servidor, você pode configurar as instâncias do aplicativo lógico para reproduzir as [funções ativas e](#roles) ativas em que funcionam como consumidores concorrentes ou [funções ativas/passivas](#roles) em que a instância alternativa aguarda até que a instância primária faça failover para o local alternativo.

  Por exemplo, a leitura de uma fila de mensagens, como uma fila do barramento de serviço do Azure, usa o estado do servidor, pois o serviço de enfileiramento mantém bloqueios em mensagens para impedir que outros clientes leiam as mesmas mensagens.

  > [!NOTE]
  > Se seu aplicativo lógico precisar ler mensagens em uma ordem específica, por exemplo, de uma fila do barramento de serviço, você poderá usar o padrão de consumidor concorrente, mas somente quando combinado com sessões do barramento de serviço, que também é conhecido como o [padrão *comboio sequencial* ](/azure/architecture/patterns/sequential-convoy). Caso contrário, você deve configurar suas instâncias de aplicativo lógico com as funções ativo-passivo.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Gatilho de solicitação

O gatilho de **solicitação** torna seu aplicativo lógico chamável de outros aplicativos, serviços e sistemas e é normalmente usado para fornecer esses recursos:

* Uma API REST direta para seu aplicativo lógico que outras pessoas podem chamar

  Por exemplo, use o gatilho de solicitação para iniciar seu aplicativo lógico para que outros aplicativos lógicos possam chamar o gatilho usando a ação **chamar o fluxo de trabalho-lógica de aplicativos** .

* Um [webhook](#webhook-trigger) ou mecanismo de retorno de chamada para seu aplicativo lógico

* Uma maneira de executar manualmente operações ou rotinas de usuário para chamar seu aplicativo lógico, por exemplo, usando um script do PowerShell que executa uma tarefa específica

De uma perspectiva de recuperação de desastres, o gatilho de solicitação é um receptor passivo porque o aplicativo lógico não realiza nenhum trabalho e aguarda até que algum outro serviço ou sistema chame explicitamente o gatilho. Como um ponto de extremidade passivo, você pode configurar suas instâncias primárias e secundárias das seguintes maneiras:

* [Ativo-ativo](#roles): ambas as instâncias lidam ativamente com solicitações ou chamadas. O chamador ou o roteador equilibra ou distribui o tráfego entre essas instâncias.

* [Ativo-passivo](#roles): somente a instância primária está ativa e lida com todo o trabalho, enquanto a instância secundária aguarda até que o primário cause interrupção ou falha. O chamador ou o roteador determina quando chamar a instância secundária.

Como uma arquitetura recomendada, você pode usar o gerenciamento de API do Azure como um proxy para os aplicativos lógicos que usam gatilhos de solicitação. O gerenciamento de API fornece [resiliência interna entre regiões e a capacidade de rotear o tráfego entre vários pontos de extremidade](../api-management/api-management-howto-deploy-multi-region.md).

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Gatilho de webhook

Um gatilho de *webhook* fornece a capacidade de seu aplicativo lógico assinar um serviço passando uma URL de *retorno de chamada* para esse serviço. Seu aplicativo lógico pode escutar e esperar que um evento específico aconteça nesse ponto de extremidade de serviço. Quando o evento acontece, o serviço chama o gatilho webhook usando a URL de retorno de chamada, que então executa o aplicativo lógico. Quando habilitado, o gatilho de webhook assina o serviço. Quando desabilitado, o gatilho cancela a assinatura do serviço.

De uma perspectiva de recuperação de desastre, configure as instâncias primárias e secundárias que usam gatilhos de webhook para reproduzir funções ativas-passivas porque apenas uma instância deve receber eventos ou mensagens do ponto de extremidade assinado.

## <a name="assess-primary-instance-health"></a>Avaliar a integridade da instância primária

Para que sua estratégia de recuperação de desastre funcione, sua solução precisa de maneiras de executar essas tarefas:

* [Verificar a disponibilidade da instância primária](#check-primary-availability)
* [Monitorar a integridade da instância primária](#monitor-primary-health)
* [Ativar a instância secundária](#activate-secondary)

Esta seção descreve uma solução que você pode usar de forma totalmente ou como base para seu próprio design. Aqui está uma visão geral de alto nível para esta solução:

![Criar aplicativo lógico de Watchdog que monitora um aplicativo lógico de verificação de integridade no local principal](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Verificar disponibilidade da instância primária

Para determinar se a instância primária está disponível, em execução e capaz de trabalhar, você pode criar um aplicativo lógico de "verificação de integridade" que está no mesmo local que a instância primária. Em seguida, você pode chamar esse aplicativo de verificação de integridade a partir de um local alternativo. Se o aplicativo de verificação de integridade responder com êxito, a infraestrutura subjacente para o serviço de aplicativos lógicos do Azure nessa região estará disponível e funcionando. Se o aplicativo de verificação de integridade não responder, você poderá pressupor que o local não está mais íntegro.

Para essa tarefa, crie um aplicativo lógico de verificação de integridade básica que executa estas tarefas:

1. Recebe uma chamada do aplicativo Watchdog usando o gatilho de solicitação.

1. Responda com um status que indique se o aplicativo lógico selecionado ainda funciona usando a ação de resposta.

   > [!IMPORTANT]
   > O aplicativo lógico de verificação de integridade deve usar uma ação de resposta para que o aplicativo responda de forma síncrona, não assincronamente.

1. Opcionalmente, para determinar ainda mais se o local principal está íntegro, você pode considerar a integridade de quaisquer outros serviços que interagem com o aplicativo lógico de destino neste local. Basta expandir o aplicativo lógico de verificação de integridade para avaliar a integridade desses outros serviços também.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Criar um aplicativo lógico de Watchdog

Para monitorar a integridade da instância primária e chamar o aplicativo lógico de verificação de integridade, crie um aplicativo lógico de "Watchdog" em um *local alternativo*. Por exemplo, você pode configurar o aplicativo lógico de Watchdog para que, se a chamada à lógica de verificação de integridade falhe, o Watchdog possa enviar um alerta para sua equipe de operações para que ele possa investigar a falha e por que a instância primária não responde.

> [!IMPORTANT]
> Verifique se o aplicativo de lógica de Watchdog está em um *local diferente do local primário*. Se o serviço de aplicativos lógicos no local primário enfrentar problemas, seu aplicativo lógico de Watchdog poderá não ser executado.

Para essa tarefa, no local secundário, crie um aplicativo lógico de Watchdog que executa estas tarefas:

1. Executar com base em uma recorrência fixa ou agendada usando o gatilho de recorrência.

   Você pode definir a recorrência para um valor que esteja abaixo do nível de tolerância para seu RTO (objetivo de tempo de recuperação).

1. Chame o aplicativo lógico de verificação de integridade no local principal usando a ação HTTP, por exemplo:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Ativar sua instância secundária

Para ativar automaticamente a instância secundária, você pode criar um aplicativo lógico que chama a API de gerenciamento, como o [conector de Azure Resource Manager](/connectors/arm/) para ativar os aplicativos lógicos apropriados no local secundário. Você pode expandir seu aplicativo de Watchdog para chamar esse aplicativo lógico de ativação após um número específico de falhas acontecer.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Coletar dados de diagnóstico

Você pode configurar o registro em log para suas execuções de aplicativo lógico e enviar os dados de diagnóstico resultantes para serviços como o armazenamento do Azure, hubs de eventos do Azure e Log Analytics do Azure para manipulação e processamento adicionais.

* Se desejar usar esses dados com o Azure Log Analytics, você poderá disponibilizar os dados para os locais primários e secundários definindo **as configurações de diagnóstico** do aplicativo lógico e enviando os dados para vários espaços de trabalho do log Analytics. Para obter mais informações, consulte [Configurar Logs de Azure monitor e coletar dados de diagnóstico para aplicativos lógicos do Azure](../logic-apps/monitor-logic-apps-log-analytics.md).

* Se desejar enviar os dados para o armazenamento do Azure ou hubs de eventos do Azure, você poderá disponibilizar os dados para os locais primários e secundários Configurando a redundância geográfica. Para obter mais informações, consulte estes artigos:<p>

  * [Recuperação de desastre do armazenamento de BLOBs do Azure e failover de conta](../storage/common/storage-disaster-recovery-guidance.md)
  * [Recuperação de desastre geográfica dos hubs de eventos do Azure](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Próximas etapas

* [Visão geral de resiliência para o Azure](/azure/architecture/framework/resiliency/overview)
* [Lista de verificação de resiliência para serviços específicos do Azure](/azure/architecture/checklist/resiliency-per-service)
* [Gerenciamento de dados para resiliência no Azure](/azure/architecture/framework/resiliency/data-management)
* [Backup e recuperação de desastre para aplicativos do Azure](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Recuperar-se de uma interrupção do serviço em toda a região](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Contratos de nível de serviço (SLAs) da Microsoft para serviços do Azure](https://azure.microsoft.com/support/legal/sla/)
