---
title: Alta disponibilidade e recuperação de desastres do Hub IoT do Azure | Microsoft Docs
description: Descreve os recursos do Hub IoT e do Azure que ajudam a criar soluções IoT do Azure altamente disponíveis habilitadas para recuperação de desastre.
author: rkmanda
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: philmea
ms.openlocfilehash: 32caebf8ea216050427f4400102cf56ffc657b55
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875257"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de desastres do Hub IoT

Como primeiro passo para implementar uma solução de IoT resiliente, os arquitetos, desenvolvedores e proprietários de negócios devem definir as metas de tempo de atividade das soluções que estão criando. Essas metas podem ser definidas principalmente com base em objetivos de negócios específicos para cada cenário. Neste contexto, o artigo [Orientação Técnica de Continuidade de Negócios do Azure](https://docs.microsoft.com/azure/architecture/resiliency/) descreve uma estrutura geral para ajudá-lo a pensar em continuidade de negócios e recuperação de desastres. O documento [Recuperação de desastre e alta disponibilidade para aplicativos do Azure](https://msdn.microsoft.com/library/dn251004.aspx) fornece diretrizes de arquitetura sobre estratégias para que os aplicativos do Azure consigam alta disponibilidade (HA) e recuperação de desastres (DR).

Este artigo descreve os recursos de HA e DR oferecidos especificamente pelo serviço IoT Hub. As grandes áreas discutidas neste artigo são:

- HA entre regiões
- Recuperação de desastres de região cruzada
- Alcançar a região cruzada HA

Dependendo das metas de tempo de atividade definidas para suas soluções de IoT, você deve determinar quais das opções descritas abaixo são mais adequadas aos seus objetivos de negócios. Incorporar qualquer uma dessas alternativas de HA / DR à sua solução de IoT requer uma avaliação cuidadosa dos trade-offs entre:

- Nível de resiliência que você precisa 
- Complexidade de implementação e manutenção
- Impacto de COGS

## <a name="intra-region-ha"></a>HA entre regiões

O serviço Hub IoT fornece HA intra-região implementando redundâncias em quase todas as camadas do serviço. O [SLA publicado pelo serviço IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub) é obtido com o uso dessas redundâncias. Nenhum trabalho adicional é exigido pelos desenvolvedores de uma solução de IoT para aproveitar esses recursos de alta disponibilidade. Embora o IoT Hub ofereça uma garantia razoavelmente alta de tempo de atividade, falhas transitórias ainda podem ser esperadas como em qualquer plataforma de computação distribuída. Se você estiver apenas começando a migrar suas soluções para a nuvem de uma solução local, seu foco precisará mudar da otimização "tempo médio entre falhas" para "tempo médio de recuperação". Em outras palavras, falhas transitórias devem ser consideradas normais durante a operação com a nuvem no mix. As políticas de repetição [adequadas](iot-hub-reliability-features-in-sdks.md) devem ser incorporadas aos componentes que interagem com um aplicativo em nuvem para lidar com falhas transitórias.

> [!NOTE]
> Alguns serviços do Azure também fornecem camadas adicionais de disponibilidade em uma região, integrando com [Zonas de disponibilidade (AZs)](../availability-zones/az-overview.md). No momento, os AZs não são suportados pelo serviço IoT Hub.

## <a name="cross-region-dr"></a>Recuperação de desastres de região cruzada

Pode haver algumas situações raras em que um datacenter experimenta interrupções prolongadas devido a falhas de energia ou outras falhas envolvendo ativos físicos. Tais eventos são raros durante os quais a capacidade de HA da região intra descrita acima nem sempre ajuda. O Hub IoT fornece várias soluções para recuperar-se de interrupções prolongadas. 

As opções de recuperação disponíveis para clientes nessa situação são "failover iniciado pelo Microsoft" e "failover manual". A diferença fundamental entre os dois é que a Microsoft inicia a primeira e o usuário inicia a segunda. Além disso, o failover manual fornece um menor objetivo tempo de recuperação (RTO) em comparação comparado a opção de failover iniciado pelo Microsoft. Os RTOs específicos oferecidos com cada opção são discutidos nas seções abaixo. Quando qualquer uma dessas opções para executar o failover de um hub de IoT de sua região primária for exercida, o hub se tornará totalmente funcional na região correspondente  do [Azure emparelhada geograficamente](../best-practices-availability-paired-regions.md).

Ambas as opções de failover oferecem os seguintes objetivos de ponto de recuperação (RPOs):

| Tipo de dados | Objetivos do ponto de recuperação (RPO) |
| --- | --- |
| Registro de identidade |Perda de dados de 0 a 5 minutos |
| Dados do dispositivo gêmeo |Perda de dados de 0 a 5 minutos |
| Mensagens da nuvem para dispositivo<sup>1</sup> |Perda de dados de 0 a 5 minutos |
| Pai<sup>1</sup> e trabalhos do dispositivo |Perda de dados de 0 a 5 minutos |
| Mensagens do dispositivo para a nuvem |Todas as mensagens não lidas são perdidas |
| Mensagens de monitoramento de operações |Todas as mensagens não lidas são perdidas |
| Mensagens de feedback da nuvem para o dispositivo |Todas as mensagens não lidas são perdidas |

<sup> 1 </sup> As mensagens da nuvem para o dispositivo e as tarefas pai não são recuperadas como parte do failover manual na oferta de visualização desse recurso.

Depois que a operação de failover do hub IoT for concluída, espera-se que todas as operações do dispositivo e dos aplicativos de back-end continuem funcionando sem exigir uma intervenção manual.

> [!CAUTION]
> - O nome e o ponto final do Hub compatível com eventos do ponto de extremidade de eventos internos do Hub IoT são alterados após o failover. Ao receber mensagens de telemetria do terminal interno usando o host do hub de eventos ou do processador de eventos, você deve [usar a cadeia de conexão de hub IoT](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) para estabelecer a conexão. Isso garante que seus aplicativos de back-end continuem a funcionar sem exigir o failover de pós-intervenção manual. Se você usar o nome e o ponto de extremidade compatíveis com Event Hub no seu aplicativo de backend diretamente, precisará reconfigurar seu aplicativo [buscando o novo nome e ponto de extremidade compatíveis com o Event Hub](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) após o failover para continuar as operações.
>
> - Após o failover, os eventos emitidos via Event Grid podem ser consumidos por meio da (s) mesma (s) assinatura (s) configurada (s) anteriormente, contanto que essas assinaturas de Event Grid continuem disponíveis.
>
> - Ao rotear o armazenamento de blob, é recomendável inscrever os blobs e, em seguida, iterar sobre eles, para garantir que todos os contêineres são lidos sem fazer suposições de partição. O intervalo de partição pode ser alterado durante um failover iniciado pela Microsoft ou um failover manual. Para saber como enumerar a lista de BLOBs, consulte [Roteamento para o armazenamento](iot-hub-devguide-messages-d2c.md#azure-blob-storage)de BLOBs.

### <a name="microsoft-initiated-failover"></a>Failover iniciado pelo Microsoft

Failover iniciado pelo Microsoft seja utilizado pela Microsoft em raras situações de failover a IoT todos os hubs de uma região afetada à região geográfica emparelhada correspondente. Este processo é uma opção padrão (não há como os usuários optarem por não participar) e não requer intervenção do usuário. A Microsoft se reserva o direito de determinar quando essa opção será exercida. Esse mecanismo não envolve o consentimento do usuário antes do failover do hub do usuário. Failover iniciado pelo Microsoft tem um objetivo de tempo de recuperação (RTO) de 2 a 26 horas. 

O grande RTO é porque a Microsoft deve executar a operação de failover em nome de todos os clientes afetados nessa região. Se você estiver executando uma solução de IoT menos importante que possa manter um tempo de inatividade de aproximadamente um dia, não há problema em você depender dessa opção para satisfazer as metas gerais de recuperação de desastre da sua solução de IoT. O tempo total para operações de tempo de execução se tornarem totalmente operacionais depois que esse processo é acionado é descrito na seção "Tempo para recuperação".

### <a name="manual-failover-preview"></a>Failover manual (visualização)

Se suas metas de tempo de atividade de negócios não forem atendidas pelo RTO fornecido pelo failover iniciado pela Microsoft, você deverá considerar usar o failover manual para acionar o processo de failover sozinho. O RTO usando essa opção pode estar entre 10 minutos a algumas horas. O RTO é atualmente uma função do número de dispositivos registrados em relação à instância do hub IoT com failover. Você pode esperar que o RTO para um hub que hospede aproximadamente 100.000 dispositivos seja de 15 minutos. O tempo total para operações de tempo de execução se tornarem totalmente operacionais depois que esse processo é acionado é descrito na seção "Tempo para recuperação".

A opção de failover manual está sempre disponível para uso, independentemente de a região principal estar com tempo de inatividade ou não. Portanto, essa opção poderia ser usada para realizar failovers planejados. Um exemplo de uso de failovers planejados é executar exercícios de failover periódicos. Uma palavra de cautela é que uma operação de failover planejada resulta em um tempo de inatividade para o hub para o período definido pelo RTO para essa opção e também resulta em uma perda de dados, conforme definido pela tabela de RPO acima. Você pode considerar a configuração de uma instância de hub de IoT de teste para exercer a opção de failover planejada periodicamente para ganhar confiança em sua capacidade de obter suas soluções de ponta a ponta funcionando quando ocorre um desastre real.

> [!IMPORTANT]
> - Os exercícios de teste não devem ser executados em hubs de IoT que estão sendo usados em seus ambientes de produção.
>
> - O failover manual não deve ser usado como um mecanismo para migrar permanentemente seu hub entre as regiões emparelhadas do Azure. Fazer isso causaria uma latência maior para as operações que estão sendo executadas no hub a partir de dispositivos hospedados na antiga região primária.

### <a name="failback"></a>Failback

O failback para a região principal antiga pode ser obtido acionando a ação de failover em outra ocasião. Se a operação de failover original foi executada para recuperar de uma interrupção estendida na região primária original, recomendamos que o hub seja reprovado para o local original depois que esse local tiver se recuperado da situação de interrupção.

> [!IMPORTANT]
> - Os usuários só podem executar 2 failover bem-sucedido e 2 operações de failback bem-sucedidas por dia.
>
> - As operações de failover / failback de volta para trás não são permitidas. Os usuários terão que esperar por 1 hora entre essas operações.

### <a name="time-to-recover"></a>Tempo de recuperação

Enquanto o FQDN (e, portanto, a cadeia de caracteres de conexão) da instância do hub IoT permanecem o mesmo após o failover, o endereço IP subjacente será alterado. Portanto, o tempo geral para as operações de tempo de execução que está sendo executada em relação a sua instância do hub IoT para se tornar totalmente operacional depois que o processo de failover é disparado pode ser expresso usando a função a seguir.

Tempo para recuperar = RTO [10 min - 2 horas para failover manual | 2 a 26 horas para failover iniciado pela Microsoft] + atraso de propagação de DNS + tempo gasto pelo aplicativo cliente para atualizar qualquer endereço IP do IoT Hub armazenado em cache.

> [!IMPORTANT]
> Os SDKs da IoT não armazenam em cache o endereço IP do hub IoT. Recomendamos que a interface de código de usuário com os SDKs não armazene em cache o endereço IP do hub IoT.

## <a name="achieve-cross-region-ha"></a>Alcance a região cruzada HA

Se suas metas de tempo de atividade de negócios não forem atendidas pelo RTO que as opções de failover manual ou de failover iniciadas pela Microsoft fornecem, você deve considerar a implementação de um mecanismo de failover de região cruzada automático por dispositivo.
Um tratamento completo das topologias de implantação em soluções de IoT está fora do escopo deste artigo. O artigo discute o modelo de implantação de *failover regional* para fins de alta disponibilidade e recuperação de desastres.

Em um modelo de failover regional, o back-end da solução é executado primariamente em um local de datacenter. Um hub IoT secundário e o back-end são implantadas em outro local do datacenter. Se o hub IoT na região primária sofrer uma interrupção ou a conectividade de rede do dispositivo para a região principal for interrompida, os dispositivos usarão um ponto de extremidade de serviço secundário. Você pode melhorar a disponibilidade da solução com a implementação de um modelo de failover entre regiões, em vez de manter-se dentro de uma única região. 

Em um nível alto, para implementar um modelo de failover regional com o Hub IoT, você precisa seguir os seguintes passos:

* **Uma lógica de roteamento de dispositivo e Hub IOT secundário**: Se o serviço em sua região primária for interrompido, os dispositivos deverão começar a se conectar à sua região secundária. Com base na natureza consciente do estado da maioria dos serviços envolvidos, é comum os administradores de solução acionarem o processo de failover entre regiões. A melhor maneira de comunicar o novo ponto de extremidade para os dispositivos, enquanto mantém o controle do processo, é fazer com que eles verifiquem regularmente um serviço de *concierge* para o ponto de extremidade ativo atual. O serviço de concierge pode ser um aplicativo Web que é replicado e acessível usando técnicas de redirecionamento de DNS (por exemplo, usando o [Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md)).

   > [!NOTE]
   > O serviço de hub da IoT não é um tipo de terminal suportado no Gerenciador de Tráfego do Azure. A recomendação é integrar o serviço de concierge proposto ao gerenciador de tráfego do Azure, implementando a API de análise de integridade do ponto de extremidade.

* **Replicação de registro de identidade**: Para ser utilizável, o Hub IoT secundário deve conter todas as identidades de dispositivo que podem se conectar à solução. A solução deve manter backups replicados geograficamente das identidades do dispositivo e carregá-los no Hub IoT secundário antes de mudar o ponto de extremidade ativo para os dispositivos. A funcionalidade de exportação de identidade do dispositivo do Hub IoT é útil neste contexto. Para obter mais informações, consulte [Guia do desenvolvedor do Hub IoT - Registro de identidade](iot-hub-devguide-identity-registry.md).

* **Lógica**de mesclagem: Quando a região primária ficar disponível novamente, todos os Estados e dados que foram criados no site secundário devem ser migrados de volta para a região primária. Esse estado e os dados estão relacionados principalmente às identidades de dispositivo e aos metadados do aplicativo, que deverão ser mesclados ao Hub IoT primário e com todos os outros armazenamentos específicos do aplicativo na região primária. 

Para simplificar essa etapa, você deve usar operações idempotentes. Operações idempotentes minimizam os efeitos colaterais da distribuição eventual e consistente de eventos e também de duplicatas ou a entrega de eventos fora de ordem. Além disso, a lógica do aplicativo deve ser projetada para tolerar possíveis inconsistências ou um estado ligeiramente desatualizado. Essa situação pode ocorrer devido ao tempo adicional necessário para o sistema recuperar com base nos objetivos de ponto de recuperação (RPO).

## <a name="choose-the-right-hadr-option"></a>Escolha a opção de HA/DR certa

Aqui está um resumo das opções de HA/DR apresentado neste artigo que pode ser usado como um quadro de referência para escolher a opção correta que funciona para sua solução.

| Opção de HA/DR | RTO | RPO | Requer intervenção manual? | Complexidade da implementação | Impacto do custo adicional|
| --- | --- | --- | --- | --- | --- |
| Failover iniciado pelo Microsoft |2 - 26 horas|Consulte a tabela RPO acima|Não|Nenhum|Nenhum|
| Failover manual |10 min - 2 horas|Consulte a tabela RPO acima|Sim|Muito baixa. Você só precisará disparar essa operação no portal.|Nenhum|
| Entre a alta disponibilidade de região |< 1 minuto|Depende da frequência de replicação de sua solução personalizada de alta disponibilidade|Não|Alto|> 1 vezes o custo do hub do IoT 1|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Hub IoT do Azure, siga estes links:

* [Introdução aos Hubs de IoT (guia de início rápido)](quickstart-send-telemetry-dotnet.md)
* [O que é o Hub IoT do Azure?](about-iot-hub.md)