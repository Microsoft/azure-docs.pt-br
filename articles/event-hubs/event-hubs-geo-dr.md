---
title: Recuperação de desastre geográfico – Hubs de Eventos do Azure | Microsoft Docs
description: Como usar regiões geográficas para fazer failover e executar a recuperação de desastre nos Hubs de Eventos do Azure
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4470b55973f53c924caba8665199d261fe63a8fc
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222875"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Hubs de Eventos do Azure – Recuperação de desastre geográfico 

A resiliência contra interrupções desastrosas de recursos de processamento de dados é um requisito para muitas empresas e, em alguns casos, até mesmo exigida por regulamentos do setor. 

Os hubs de eventos do Azure já espalham o risco de falhas catastróficas de máquinas individuais ou até mesmo de racks completos em clusters que abrangem vários domínios de falha em um datacenter e implementam mecanismos de detecção de falha transparente e failover, de modo que o serviço continuará a operar dentro dos níveis de serviço garantidos e, normalmente, sem interrupções perceptíveis no caso de tais falhas Se um namespace de hubs de eventos tiver sido criado com a opção habilitado para [zonas de disponibilidade](../availability-zones/az-overview.md), o risco de interrupção estará mais espalhado em três instalações fisicamente separadas, e o serviço terá reserva de capacidade suficiente para lidar instantaneamente com a perda completa e catastrófica de todo o recurso. 

O modelo de cluster todos os hubs de eventos do Azure ativos com suporte à zona de disponibilidade fornece resiliência contra graves falhas de hardware e até mesmo perda catastrófica de instalações de datacenter inteiras. Ainda assim, pode haver grave situações com a destruição física abrangente que, até mesmo, as medidas não podem se defender suficientemente. 

O recurso de recuperação de desastres de hubs de eventos é projetado para facilitar a recuperação de um desastre dessa magnitude e abandonar uma região do Azure com falha para boa e sem precisar alterar as configurações do aplicativo. Abandonar uma região do Azure normalmente envolve vários serviços e esse recurso destina-se principalmente a ajudar a preservar a integridade da configuração do aplicativo composto.  

O recurso de recuperação de Geo-Disaster garante que toda a configuração de um namespace (hubs de eventos, grupos de consumidores e configurações) seja replicada continuamente de um namespace primário para um namespace secundário quando emparelhado, e permite que você inicie uma movimentação de failover somente uma vez do primário para o secundário a qualquer momento. A movimentação de failover irá redirecionar o nome do alias escolhido para o namespace para o namespace secundário e, em seguida, interromperá o emparelhamento. O failover é quase instantâneo depois de iniciado. 

> [!IMPORTANT]
> O recurso permite a continuidade instantânea de operações com a mesma configuração, mas não **replica os dados de evento**. A menos que o desastre tenha causado a perda de todas as zonas, os dados de evento preservados no Hub de eventos primário após o failover serão recuperáveis e os eventos históricos poderão ser obtidos a partir daí quando o acesso for restaurado. Para replicar dados de eventos e operar namespaces correspondentes em configurações ativas/ativas para lidar com interrupções e desastres, não faça o acompanhamento desse conjunto de recursos de recuperação de desastres geograficamente, mas siga as [diretrizes de replicação](event-hubs-federation-overview.md).  

## <a name="outages-and-disasters"></a>Interrupções e desastres

É importante observar a diferença entre "interrupção" e "desastres". Uma **interrupção** é uma indisponibilidade temporária dos Hubs de Eventos do Azure e pode afetar alguns componentes do serviço, como um repositório de mensagens ou até mesmo o datacenter inteiro. No entanto, depois que o problema for corrigido, os Hubs de Eventos ficarão disponíveis novamente. Normalmente, uma interrupção não causa a perda de mensagens ou de outros dados. Um exemplo de tal interrupção pode ser uma falha de energia no datacenter. Algumas falhas são apenas perdas de conexão curtas devido a problemas de rede ou transitórios. 

Um *desastre* é definido como a perda permanente ou de longo prazo de um cluster dos Hubs de Eventos, uma região do Azure ou um datacenter. A região ou o datacenter pode ou não ficar disponível novamente ou pode ficar inativo por horas ou dias. Outros exemplos desses desastres são incêndios, enchentes ou terremoto. Um desastre que se torne permanente pode causar a perda de algumas mensagens, alguns eventos ou outros dados. No entanto, na maioria dos casos, não deve haver perda de dados e as mensagens poderão ser recuperadas depois que do backup do data center.

O recurso de recuperação de desastre de área geográfica dos Hubs de Eventos do Azure é uma solução de recuperação de desastre. Os conceitos e o fluxo de trabalho descrito neste artigo se aplicam a cenários de desastre e não a falhas transitórias ou temporárias. Para uma discussão detalhada sobre a recuperação de desastre no Microsoft Azure, consulte [este artigo](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Termos e conceitos básicos

O recurso de recuperação de desastre implementa a recuperação de desastre dos metadados e se baseia em namespaces de recuperação de desastre primário e secundário. 

O recurso de recuperação de desastre geográfico está disponível apenas para as [SKUs padrão e dedicadas](https://azure.microsoft.com/pricing/details/event-hubs/). Você não precisa fazer nenhuma alteração de cadeia de conexão, já que a conexão é feita por meio de um alias.

Os seguintes termos são usados neste artigo:

-  *Alias*: o nome para uma configuração de recuperação de desastre que você configurou. O alias fornece uma única cadeia de conexão estável do FQDN (Nome de Domínio Totalmente Qualificado). Aplicativos usam essa cadeia de conexão de alias para conectarem-se a um namespace. 

-  *Namespace primário/secundário*: os namespaces que correspondem ao alias. O namespace primário é "ativo" e recebe mensagens (pode ser um namespace existente ou novo). O namespace secundário "passivo" e não recebe mensagens. Os metadados entre os dois estão sincronizados, para que ambos possam aceitar mensagens continuamente sem quaisquer alterações no código do aplicativo ou na cadeia de conexão. Para garantir que apenas o namespace ativo receba mensagens, você deve usar o alias.

    > [!IMPORTANT]
    > O recurso de recuperação de desastres geograficamente exige que a assinatura e o grupo de recursos sejam os mesmos para namespaces primários e secundários. 
-  *Metadados*: entidades, como hubs de eventos e os grupos de consumidores; e suas propriedades do serviço que são associadas ao namespace. Somente entidades e suas configurações são replicadas automaticamente. Mensagens e eventos não são replicados. 

-  *Failover*: o processo de ativação do namespace secundário.

## <a name="supported-namespace-pairs"></a>Pares de namespace com suporte
Há suporte para as seguintes combinações de namespaces primários e secundários:  

| Namespace primário | Namespace secundário | Com suporte | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Sim | 
| Standard | Dedicado | Sim | 
| Dedicado | Dedicado | Sim | 
| Dedicado | Standard | Não | 

> [!NOTE]
> Não é possível emparelhar namespaces que estão no mesmo cluster dedicado. Você pode emparelhar namespaces que estão em clusters separados. 

## <a name="setup-and-failover-flow"></a>Instalação e fluxo de failover

A seção a seguir é uma visão geral do processo de failover e explica como configurar o failover inicial. 

![1][]

### <a name="setup"></a>Instalação

Primeiro crie ou use um namespace primário existente e um novo namespace secundário, depois emparelhe os dois. Esse emparelhamento fornece um alias que você pode usar para se conectar. Como você usa um alias, não precisa alterar cadeias de conexão. Somente novos namespaces podem ser adicionados ao emparelhamento de failover. 

1. Crie o namespace primário.
1. Crie o namespace secundário na assinatura e o grupo de recursos que tem o namespace principal. Esta etapa é opcional. Você pode criar o namespace secundário ao criar o emparelhamento na próxima etapa. 
1. No portal do Azure, navegue até o namespace primário.
1. Selecione **recuperação geográfica** no menu à esquerda e selecione **Iniciar emparelhamento** na barra de ferramentas. 

    :::image type="content" source="./media/event-hubs-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Iniciar emparelhamento a partir do namespace primário":::    
1. Na página **Iniciar emparelhamento** , selecione um namespace secundário existente ou crie um na assinatura e o grupo de recursos que tem o namespace principal. Em seguida, selecione **Criar**. No exemplo a seguir, um namespace secundário existente está selecionado. 

    :::image type="content" source="./media/event-hubs-geo-dr/initiate-pairing-page.png" alt-text="Selecionar o namespace secundário":::        
1. Agora, ao selecionar a **recuperação geográfica** para o namespace primário, você deverá ver a página de **alias do geo-Dr** semelhante à imagem a seguir:

    :::image type="content" source="./media/event-hubs-geo-dr/geo-dr-alias-page.png" alt-text="Página de alias do geo-DR":::    
1. Nesta página de **visão geral** , você pode executar as seguintes ações: 
    1. Quebre o emparelhamento entre os namespaces primário e secundário. Selecione **quebrar emparelhamento** na barra de ferramentas. 
    1. Fazer failover manual para o namespace secundário. Selecione **failover** na barra de ferramentas. 
    
        > [!WARNING]
        > O failover ativará o namespace secundário e removerá o namespace primário do emparelhamento de recuperação Geo-Disaster. Crie outro namespace para ter um novo par de recuperação de desastres geograficamente. 
1. Na página **alias do geo-Dr** , selecione **políticas de acesso compartilhado** para acessar a cadeia de conexão primária para o alias. Use essa cadeia de conexão em vez de usar a cadeia de conexão para o namespace primário/secundário diretamente. 

Por fim, você deve adicionar um monitoramento para detectar se um failover é necessário. Na maioria dos casos, o serviço é uma parte de um grande ecossistema, assim, failovers automáticos raramente são possíveis, uma vez que failovers devem ser executados em sincronia com o subsistema ou a infraestrutura restante.

### <a name="example"></a>Exemplo

Em um exemplo desse cenário, considere uma solução de ponto de venda (PDV) que emita mensagens ou eventos. Os Hubs de Eventos transmitem esses eventos para uma solução de mapeamento ou de reformatação, que encaminha dados mapeado para outro sistema para processamento adicional. Nesse ponto, todos esses sistemas podem ser hospedados na mesma região do Azure. A decisão de quando e de qual parte fazer failover depende do fluxo de dados em sua infraestrutura. 

Você pode automatizar o failover tanto com sistemas de monitoramento ou com soluções de monitoramento personalizadas. No entanto, essa automação precisa de planejamento e trabalho adicionais, o que está fora do escopo deste artigo.

### <a name="failover-flow"></a>Fluxo do failover

Se você iniciar o failover, as duas etapas são necessárias:

1. Caso ocorra outra interrupção, você deve ser capaz de fazer o failover novamente. Portanto, configure outro namespace passivo e atualize o emparelhamento. 

2. Faça pull das mensagens do namespace primário anterior assim que estiver disponível novamente. Depois disso, use esse namespace para mensagens regulares fora de sua configuração de recuperação geográfica ou exclua o namespace primário antigo.

> [!NOTE]
> Há suporte apenas para semânticas encaminhadas com falha. Nesse cenário, você faz o failover e emparelha novamente com um novo namespace. Não há suporte para failback, em um cluster do SQL por exemplo. 

![2][]

## <a name="management"></a>Gerenciamento

Se você cometeu um erro, por exemplo, emparelhou as regiões erradas durante a configuração inicial, você pode interromper o emparelhamento dos dois namespaces a qualquer momento. Se você quiser usar os namespaces emparelhados como namespaces regulares, exclua o alias.

## <a name="samples"></a>Exemplos

O [exemplo no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) mostra como configurar e iniciar um failover. Esse exemplo demonstra os seguintes conceitos:

- Configurações necessárias no Azure Active Directory para usar o Azure Resource Manager com os Hubs de Eventos. 
- Etapas necessárias para executar o exemplo de código. 
- Envie e receba do namespace primário atual. 

## <a name="considerations"></a>Considerações

Observe as seguintes considerações para ter em mente:

1. Por design, a recuperação de desastre geográfico dos Hubs de Eventos não replica os dados e, portanto, você não pode reutilizar o valor de deslocamento antigo do seu hub de eventos primário em seu hub de eventos secundário. É recomendável reiniciar o receptor de eventos com um dos seguintes métodos:

- *EventPosition.FromStart()* – se você quiser ler todos os dados em seu hub de eventos secundário.
- *EventPosition.FromEnd()* – se você quiser ler todos os novos dados da hora da conexão com o hub de eventos secundário.
- *EventPosition.FromEnqueuedTime(dateTime)* – se você desejar ler todos os dados recebidos em seu hub de eventos secundário a partir de uma determinada data e hora.

2. Em seu planejamento de failover, você também deve considerar o fator tempo. Por exemplo, se você perder a conectividade por mais de 15 a 20 minutos, pode decidir iniciar o failover. 
 
3. O fato de que nenhum dado é replicado significa que as sessões ativas atuais não são replicadas. Além disso, a detecção duplicada e mensagens programadas podem não funcionar. Novas sessões, mensagens programadas e novas duplicatas funcionarão. 

4. O failover de uma infraestrutura complexa distribuída deve ser [testado](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) pelo menos uma vez. 

5. A sincronização de entidades pode levar algum tempo, cerca de 50 a 100 entidades por minuto.

## <a name="availability-zones"></a>Zonas de Disponibilidades 

O SKU Standard dos Hubs de Eventos oferece suporte às [Zonas de Disponibilidade](../availability-zones/az-overview.md), fornecendo locais isolados de falhas dentro de uma região do Azure. 

> [!NOTE]
> O suporte a Zonas de Disponibilidade para o Standard do Hubs de Eventos do Azure só é oferecido nas [regiões do Azure](../availability-zones/az-region.md) em que existem zonas de disponibilidade.

Você pode habilitar as Zonas de Disponibilidade apenas em novos namespaces usando o portal do Azure. Os Hubs de Eventos não dão suporte à migração dos namespaces existentes. Você não pode desabilitar a redundância de zona depois de habilitá-la em seu namespace.

![3][]

## <a name="private-endpoints"></a>Pontos de extremidade privados
Esta seção fornece mais considerações ao usar a recuperação de desastres geograficamente com namespaces que usam pontos de extremidade privados. Para aprender a usar pontos de extremidade privados com Hubs de Eventos em geral, confira [Configurar pontos de extremidade privados](private-link-service.md).

### <a name="new-pairings"></a>Novos emparelhamentos
Se você tentar criar um emparelhamento entre um namespace primário com um ponto de extremidade privado e um namespace secundário sem um ponto de extremidade privado, o emparelhamento falhará. O emparelhamento só terá sucesso se os namespaces primários e secundários tiverem pontos de extremidade privados. Recomendamos que você use as mesmas configurações nos namespaces primário e secundário e nas redes virtuais nas quais os pontos de extremidade privados são criados.  

> [!NOTE]
> Quando você tenta emparelhar o namespace primário com um ponto de extremidade privado e um namespace secundário, o processo de validação verifica apenas se existe um ponto de extremidade privado no namespace secundário. Ele não verifica se o ponto de extremidade funciona nem se funcionará após o failover. É sua responsabilidade garantir que o namespace secundário com ponto de extremidade privado funcione conforme o esperado após o failover.
>
> Para testar se as configurações do ponto de extremidade privado são iguais nos namespaces primário e secundário, envie uma solicitação de leitura (por exemplo: [Obter Hub de Eventos](/rest/api/eventhub/get-event-hub)) para o namespace secundário de fora da rede virtual e verifique se você recebeu uma mensagem de erro do serviço.

### <a name="existing-pairings"></a>Emparelhamentos existentes
Se o emparelhamento entre o namespace primário e o secundário já existir, a criação de ponto de extremidade privado no namespace primário falhará. Para resolver, crie primeiro um ponto de extremidade privado no namespace secundário e, em seguida, crie um para o namespace primário.

> [!NOTE]
> Embora seja permitido acesso somente leitura ao namespace secundário, as atualizações para as configurações do ponto de extremidade privado são permitidas. 

### <a name="recommended-configuration"></a>Configuração recomendada
Ao criar uma configuração de recuperação de desastre para seu aplicativo e namespaces dos Hubs de Eventos, você deve criar pontos de extremidade privados para namespaces de Hubs de Eventos primários e secundários em redes virtuais que hospedam as instâncias primária e secundária do seu aplicativo. 

Digamos que você tenha duas redes virtuais: VNET-1 e VNET-2, além destes namespaces primários e secundários: EventHubs-Namespace1-Primary, EventHubs-Namespace2-Secondary. Você precisa executar as seguintes etapas: 

- No EventHubs-Namespace1-Primary, crie dois pontos de extremidade privados que usam sub-redes de VNET-1 e VNET-2
- No EventHubs-Namespace2-Secondary, crie dois pontos de extremidade privados que usam as mesmas sub-redes de VNET-1 e VNET-2 

![Pontos de extremidade privados e redes virtuais](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

A vantagem dessa abordagem é que o failover pode ocorrer na camada de aplicativo independente do namespace dos Hubs de Eventos. Considere os seguintes cenário: 

**Failover somente de aplicativo:** Aqui, o aplicativo não existirá na VNET-1, mas mudará para VNET-2. Uma vez que ambos os pontos de extremidade privados estão configurados tanto na VNET-1 quanto na VNET-2 para os namespaces primário e secundário, o aplicativo simplesmente funcionará. 

**Failover somente de namespace dos Hubs de Eventos**: aqui, novamente, como os pontos de extremidade privados são configurados em ambas as redes virtuais para namespaces primários e secundários, o aplicativo simplesmente funcionará. 

> [!NOTE]
> Para obter orientação sobre a recuperação de desastre geográfico de uma rede virtual, confira [Rede virtual – continuidade dos negócios](../virtual-network/virtual-network-disaster-recovery-guidance.md).
 
## <a name="next-steps"></a>Próximas etapas

* O [exemplo de GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) apresenta um fluxo de trabalho simples que cria um emparelhamento de área geográfica e inicia um failover para um cenário de recuperação de desastre.
* A [referência da API REST](/rest/api/eventhub/) descreve as APIs para executar a configuração de recuperação de desastres de replicação geográfica.

Para saber mais sobre Hubs de Eventos, acesse os seguintes links:

- Introdução aos Hubs de Eventos
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
* [Aplicativos de exemplo que usam Hub de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
