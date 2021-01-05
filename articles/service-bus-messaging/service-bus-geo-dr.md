---
title: Recuperação de desastre em área geográfica do Barramento de Serviço do Azure | Microsoft Docs
description: Como usar regiões geográficas para fazer failover e executar a recuperação de desastre no Barramento de Serviço do Azure
ms.topic: article
ms.date: 01/04/2021
ms.openlocfilehash: c07721c07923a40da9fe28e0e3116bfd6a52210f
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862360"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Recuperação de desastre em área geográfica do Barramento de Serviço do Azure

A resiliência contra interrupções desastrosas de recursos de processamento de dados é um requisito para muitas empresas e, em alguns casos, até mesmo exigida por regulamentos do setor. 

O barramento de serviço do Azure já espalha o risco de falhas catastróficas de máquinas individuais ou até mesmo de racks completos em clusters que abrangem vários domínios de falha em um datacenter e implementa mecanismos de detecção de falha transparente e failover, de modo que o serviço continuará a operar dentro dos níveis de serviço garantidos e, normalmente, sem interrupções perceptíveis no caso de tais falhas. Se um namespace do barramento de serviço tiver sido criado com a opção habilitado para [zonas de disponibilidade](../availability-zones/az-overview.md), o risco de interrupção será mais disseminado em três instalações fisicamente separadas, e o serviço terá reserva de capacidade suficiente para lidar instantaneamente com a perda catastrófica completa de todo o recurso. 

O modelo de cluster do barramento de serviço do Azure tudo ativo com suporte à zona de disponibilidade é superior a qualquer produto do agente de mensagens local em termos de resiliência contra graves falhas de hardware e até mesmo perda catastrófica de instalações de datacenter inteiras. Ainda assim, pode haver grave situações com a destruição física abrangente que, até mesmo, as medidas não podem se defender suficientemente. 

O recurso de recuperação de desastre geográfica do barramento de serviço foi projetado para facilitar a recuperação de um desastre dessa magnitude e abandonar uma região do Azure com falha para boa e sem precisar alterar as configurações do aplicativo. Abandonar uma região do Azure normalmente envolve vários serviços e esse recurso destina-se principalmente a ajudar a preservar a integridade da configuração do aplicativo composto. O recurso está globalmente disponível para o SKU Premium do barramento de serviço. 

O recurso de recuperação de Geo-Disaster garante que toda a configuração de um namespace (filas, tópicos, assinaturas, filtros) seja replicada continuamente de um namespace primário para um namespace secundário quando emparelhado, e permite que você inicie um failover somente uma vez, do primário para o secundário a qualquer momento. A movimentação de failover irá redirecionar o nome do alias escolhido para o namespace para o namespace secundário e, em seguida, interromperá o emparelhamento. O failover é quase instantâneo depois de iniciado. 

> [!IMPORTANT]
> O recurso permite a continuidade instantânea de operações com a mesma configuração, mas não **Replica as mensagens mantidas em filas ou assinaturas de tópico ou filas de mensagens mortas**. Para preservar a semântica da fila, essa replicação exigirá não apenas a replicação de dados da mensagem, mas de cada alteração de estado no agente. Para a maioria dos namespaces do barramento de serviço, o tráfego de replicação necessário excederia muito o tráfego do aplicativo e com filas de alta taxa de transferência, a maioria das mensagens ainda seria replicada para o secundário enquanto eles já estão sendo excluídos do primário, causando um tráfego excessiva de desperdício. Para rotas de replicação de alta latência, que se aplicam a vários emparelhamentos que você escolheria para a recuperação de desastres geográficos, também pode ser impossível para o tráfego de replicação acompanhar o tráfego do aplicativo devido a efeitos de limitação induzida por latência.
 
> [!TIP]
> Para replicar o conteúdo de filas e assinaturas de tópico e operar namespaces correspondentes em configurações ativas/ativas para lidar com interrupções e desastres, não faça o acompanhamento desse conjunto de recursos de recuperação de desastres geograficamente, mas siga as [diretrizes de replicação](service-bus-federation-overview.md).  

## <a name="outages-and-disasters"></a>Interrupções e desastres

É importante observar a diferença entre "interrupção" e "desastres". 

Uma *interrupção* é uma indisponibilidade temporária do Barramento de Serviço do Azure e pode afetar alguns componentes do serviço, como um repositório de mensagens ou até mesmo o datacenter inteiro. No entanto, depois que o problema for corrigido, o Barramento de Serviço ficará disponível novamente. Normalmente, uma interrupção não causa a perda de mensagens ou de outros dados. Um exemplo de tal interrupção pode ser uma falha de energia no datacenter. Algumas falhas são apenas perdas de conexão curtas devido a problemas de rede ou transitórios. 

Um *desastre* é definido como a perda permanente ou de longo prazo de um cluster do Barramento de Serviço, uma região do Azure ou um datacenter. A região ou o datacenter pode ou não ficar disponível novamente ou pode ficar inativo por horas ou dias. Outros exemplos desses desastres são incêndios, enchentes ou terremoto. Um desastre que se torne permanente pode causar a perda de algumas mensagens, alguns eventos ou outros dados. No entanto, na maioria dos casos, não deve haver perda de dados e as mensagens poderão ser recuperadas depois que do backup do data center.

O recurso de recuperação de desastre em área geográfica do Barramento de Serviço do Azure é uma solução de recuperação de desastre. Os conceitos e o fluxo de trabalho descrito neste artigo se aplicam a cenários de desastre e não a falhas transitórias ou temporárias. Para uma discussão detalhada sobre a recuperação de desastre no Microsoft Azure, consulte [este artigo](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Termos e conceitos básicos

O recurso de recuperação de desastre implementa a recuperação de desastre dos metadados e se baseia em namespaces de recuperação de desastre primário e secundário. Observe que o recurso de recuperação de desastre em área geográfica está disponível somente para a [SKU Premium](service-bus-premium-messaging.md). Você não precisa fazer nenhuma alteração de cadeia de conexão, já que a conexão é feita por meio de um alias.

Os seguintes termos são usados neste artigo:

-  *Alias*: o nome para uma configuração de recuperação de desastre que você configurou. O alias fornece uma única cadeia de conexão estável do FQDN (Nome de Domínio Totalmente Qualificado). Aplicativos usam essa cadeia de conexão de alias para conectarem-se a um namespace. Usar um alias garante que a cadeia de caracteres de conexão fique inalterada quando o failover for disparado.

-  *Namespace primário/secundário*: os namespaces que correspondem ao alias. O namespace primário é "ativo" e recebe mensagens (pode ser um namespace existente ou novo). O namespace secundário "passivo" e não recebe mensagens. Os metadados entre os dois estão sincronizados, para que ambos possam aceitar mensagens continuamente sem quaisquer alterações no código do aplicativo ou na cadeia de conexão. Para garantir que apenas o namespace ativo receba mensagens, você deve usar o alias. 

-  *Metadados*: Entidades como filas, tópicos e assinaturas; e suas propriedades do serviço que são associadas ao namespace. Observe que somente entidades e suas configurações são replicadas automaticamente. Mensagens não são replicadas.

-  *Failover*: o processo de ativação do namespace secundário.

## <a name="setup"></a>Instalação

A seção a seguir é uma visão geral da configuração do emparelhamento entre os namespaces.

![1][]

O processo de instalação é o seguinte:

1. Provisionar um namespace Premium do barramento de serviço do ***primário**.

2. Provisionar um namespace Premium _*_secundário_*_ do barramento de serviço em uma região _different de onde o namespace primário é provisionado *. Isso ajuda a permitir o isolamento de falhas nas diferentes regiões de datacenter.

3. Crie emparelhamento entre o namespace primário e o namespace secundário para obter o ***alias** _.

    >[!NOTE] 
    > Se você tiver [migrado seu namespace standard do barramento de serviço do Azure para o barramento de serviço Premium do Azure](service-bus-migrate-standard-premium.md), deverá usar o alias pré-existente (ou seja, sua cadeia de conexão de namespace padrão do barramento de serviço) para criar a configuração de recuperação de desastre por meio do _ *PS/CLI** ou da **API REST**.
    >
    >
    > Isso ocorre porque, durante a migração, sua cadeia de conexão de namespace do Barramento de Serviço do Azure Standard/nome DNS em si se torna um alias para o namespace Premium do Barramento de Serviço do Azure.
    >
    > Seus aplicativos cliente devem utilizar esse alias (ou seja, a cadeia de conexão de namespace do Barramento de Serviço do Azure Standard) para se conectar ao namespace Premium em que o emparelhamento de recuperação de desastre foi configurado.
    >
    > Se você usar o portal para configurar a configuração de recuperação de desastre, o portal abstrairá essa limitação de você.


4. Use o **_alias_* _ obtido na etapa 3 para conectar seus aplicativos cliente ao namespace primário habilitado para Dr geográfica. Inicialmente, o alias aponta para o namespace primário.

5. [Opcional] Adicione um monitoramento para detectar se um failover é necessário.

## <a name="failover-flow"></a>Fluxo do failover

Um failover é disparado manualmente pelo cliente (seja explicitamente por meio de um comando ou por meio da lógica de negócios de propriedade do cliente que aciona o comando) e nunca pelo Azure. Isso dá ao cliente visibilidade e propriedade completa para resolução de interrupção no backbone do Azure.

![4][]

Após o failover ser disparado:

1. A cadeia de conexão do _*_alias_*_ é atualizada para apontar para o namespace Premium secundário.

2. Os clientes (remetentes e receptores) conectam-se automaticamente no namespace secundário.

3. O emparelhamento existente entre os namespaces premium primário e secundário é interrompido.

Depois que o failover é iniciado:

1. Caso ocorra outra interrupção, você deve ser capaz de fazer o failover novamente. Portanto, configure outro namespace passivo e atualize o emparelhamento. 

2. Faça pull das mensagens do namespace primário anterior assim que estiver disponível novamente. Depois disso, use esse namespace para mensagens regulares fora de sua configuração de recuperação geográfica ou exclua o namespace primário antigo.

> [!NOTE]
> Há suporte apenas para semânticas encaminhadas com falha. Nesse cenário, você faz o failover e emparelha novamente com um novo namespace. Não há suporte para failback, em um cluster do SQL por exemplo. 

Você pode automatizar o failover tanto com sistemas de monitoramento ou com soluções de monitoramento personalizadas. No entanto, essa automação precisa de planejamento e trabalho adicionais, o que está fora do escopo deste artigo.

![2][]

## <a name="management"></a>Gerenciamento

Se você cometeu um erro, por exemplo, emparelhou as regiões erradas durante a configuração inicial, você pode interromper o emparelhamento dos dois namespaces a qualquer momento. Se você quiser usar os namespaces emparelhados como namespaces regulares, exclua o alias.

## <a name="use-existing-namespace-as-alias"></a>Usar namespace existente como alias

Caso tenha um cenário no qual você não pode alterar as conexões de produtores e consumidores, você pode reutilizar o nome do namespace como o nome do alias. Consulte o [exemplo de código no GitHub aqui](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Exemplos

Os [exemplos no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) mostram como configurar e iniciar um failover. Esses exemplos demonstram os conceitos a seguir:

- Um exemplo de .NET e configurações necessárias no Azure Active Directory para usar o Azure Resource Manager com o Barramento de Serviço para configurar e habilitar a recuperação de desastre geográfico.
- Etapas necessárias para executar o exemplo de código.
- Como usar um namespace existente como alias.
- Etapas para habilitar a recuperação de desastres de geográficos por meio do PowerShell ou CLI como alternativa.
- [Envie e receba](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) do namespace primário ou secundário atual usando o alias.

## <a name="considerations"></a>Considerações

Observe as seguintes considerações a serem lembradas quanto a esta versão:

1. Em seu planejamento de failover, você também deve considerar o fator tempo. Por exemplo, se você perder a conectividade por mais de 15 a 20 minutos, pode decidir iniciar o failover.

2. O fato de que nenhum dado seja replicado significa que sessões atualmente ativas não são replicadas. Além disso, a detecção duplicada e mensagens programadas podem não funcionar. Novas sessões, novas mensagens agendadas e novas duplicatas funcionarão. 

3. O failover de uma infraestrutura complexa distribuída deve ser [testado](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) pelo menos uma vez.

4. A sincronização de entidades pode levar algum tempo, cerca de 50 a 100 entidades por minuto. Assinaturas e regras também são contadas como entidades.

## <a name="availability-zones"></a>Zonas de Disponibilidades

O SKU Premium do Barramento de Serviço também oferece suporte às [Zonas de Disponibilidade](../availability-zones/az-overview.md), fornecendo locais isolados de falhas dentro de uma região do Azure.

> [!NOTE]
> O suporte a Zonas de Disponibilidade para o Barramento de Serviço Premium do Azure só é oferecido nas [regiões do Azure](../availability-zones/az-region.md) em que existem zonas de disponibilidade.

Você pode habilitar as Zonas de Disponibilidade apenas em novos namespaces usando o portal do Azure. O Barramento de Serviço não dá suporte à migração dos namespaces existentes. Você não pode desabilitar a redundância de zona depois de habilitá-la em seu namespace.

![3][]

## <a name="private-endpoints"></a>Pontos de extremidade privados
Esta seção fornece considerações adicionais ao usar a recuperação de desastre geográfico com namespaces que usam pontos de extremidade privados. Para saber mais sobre como usar pontos de extremidade privados com o Barramento de Serviço em geral, confira [Integrar o Barramento de Serviço do Azure ao Link Privado do Azure](private-link-service.md).

### <a name="new-pairings"></a>Novos emparelhamentos
Se você tentar criar um emparelhamento entre um namespace primário com um ponto de extremidade privado e um namespace secundário sem um ponto de extremidade privado, o emparelhamento falhará. O emparelhamento só terá sucesso se os namespaces primários e secundários tiverem pontos de extremidade privados. Recomendamos que você use as mesmas configurações nos namespaces primário e secundário e nas redes virtuais nas quais os pontos de extremidade privados são criados. 

> [!NOTE]
> Quando você tenta emparelhar o namespace primário com um ponto de extremidade privado e o namespace secundário, o processo de validação verifica apenas se existe um ponto de extremidade privado no namespace secundário. Ele não verifica se o ponto de extremidade funciona nem se funcionará após o failover. É sua responsabilidade garantir que o namespace secundário com ponto de extremidade privado funcione conforme o esperado após o failover.
>
> Para testar se as configurações do ponto de extremidade privado são as mesmas, envie uma solicitação [Obter filas](/rest/api/servicebus/stable/queues/get) ao namespace secundário de fora da rede virtual e verifique se você recebeu uma mensagem de erro do serviço.

### <a name="existing-pairings"></a>Emparelhamentos existentes
Se o emparelhamento entre o namespace primário e o secundário já existir, a criação de ponto de extremidade privado no namespace primário falhará. Para resolver, crie primeiro um ponto de extremidade privado no namespace secundário e, em seguida, crie um para o namespace primário.

> [!NOTE]
> Embora seja permitido acesso somente leitura ao namespace secundário, as atualizações para as configurações do ponto de extremidade privado são permitidas. 

### <a name="recommended-configuration"></a>Configuração recomendada
Ao criar uma configuração de recuperação de desastre para seu aplicativo e Barramento de Serviço, você deve criar pontos de extremidade privados para namespaces de Barramento de Serviço primário e secundário em redes virtuais que hospedam as instâncias primária e secundária do seu aplicativo.

Digamos que você tenha duas redes virtuais: VNET-1 e VNET-2, além destes namespaces primários e secundários: ServiceBus-Namespace1-Primary, ServiceBus-Namespace2-Secondary. Você precisa executar as seguintes etapas: 

- No ServiceBus-Namespace1-Primary, crie dois pontos de extremidade privados que usam sub-redes de VNET-1 e VNET-2
- No ServiceBus-Namespace2-Secondary, crie dois pontos de extremidade privados que usam as mesmas sub-redes de VNET-1 e VNET-2 

![Pontos de extremidade privados e redes virtuais](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


A vantagem dessa abordagem é que o failover pode ocorrer na camada de aplicativo independente do namespace do Barramento de Serviço. Considere os seguintes cenário: 

_ *Failover somente de aplicativo:** aqui, o aplicativo não existirá na vnet-1, mas mudará para vnet-2. Uma vez que ambos os pontos de extremidade privados estão configurados tanto na VNET-1 quanto na VNET-2 para os namespaces primário e secundário, o aplicativo simplesmente funcionará. 

**Failover somente de namespace do Barramento de Serviço**: aqui, novamente, como os pontos de extremidade privados são configurados em ambas as redes virtuais para namespaces primários e secundários, o aplicativo simplesmente funcionará. 

> [!NOTE]
> Para obter orientação sobre a recuperação de desastre geográfico de uma rede virtual, confira [Rede virtual – continuidade dos negócios](../virtual-network/virtual-network-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Próximas etapas

- Consulte a [referência da API REST de recuperação de desastre em área geográfica aqui](/rest/api/servicebus/stable/disasterrecoveryconfigs).
- Execute o [exemplo no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2) da recuperação de desastre em área geográfica.
- Consulte o [exemplo que envia mensagens a um alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) da recuperação de desastre em área geográfica.

Para saber mais sobre as mensagens do Barramento de Serviço, confira os artigos a seguir:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
