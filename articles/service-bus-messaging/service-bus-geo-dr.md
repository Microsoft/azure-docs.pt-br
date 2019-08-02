---
title: Recuperação de desastre em área geográfica do Barramento de Serviço do Azure | Microsoft Docs
description: Como usar regiões geográficas para fazer failover e executar a recuperação de desastre no Barramento de Serviço do Azure
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190157"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Recuperação de desastre em área geográfica do Barramento de Serviço do Azure

Quando datacenters ou regiões inteiras do Azure (se nenhuma [zona de disponibilidade](../availability-zones/az-overview.md) for usada) enfrentam tempo de inatividade, é essencial para o processamento de dados continuar a operar em uma região ou datacenter diferente. Assim, a *recuperação de desastre geográfico* é um recurso importante para qualquer empresa. O Barramento de Serviço do Azure oferece suporte à recuperação de desastre geográfico no nível do namespace.

O recurso de recuperação de desastres em área geográfica fica globalmente disponível para a SKU Premium do Barramento de Serviço. 

>[!NOTE]
> A recuperação de desastre geográfico atualmente garante apenas que os metadados (filas, tópicos, assinaturas, filtros) sejam copiados do namespace primário para o namespace secundário quando emparelhados.

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

## <a name="setup"></a>Configuração

A seção a seguir é uma visão geral da configuração do emparelhamento entre os namespaces.

![1][]

O processo de instalação é o seguinte:

1. Provisione um Namespace Premium do Barramento de Serviço ***primário***.

2. Provisione um Namespace Premium do Barramento de Serviço ***secundário*** em uma região *diferente de onde o namespace primário está provisionado*. Isso ajuda a permitir o isolamento de falhas nas diferentes regiões de datacenter.

3. Crie o emparelhamento entre os namespaces primário e secundário para obter o ***alias***.

    >[!NOTE] 
    > Se você tiver [migrado seu namespace de barramento de serviço de Azure Standard para Premium do barramento de serviço do Azure](service-bus-migrate-standard-premium.md), em seguida, você deve usar o alias já existente (ou seja, o barramento de serviço Standard namespace conexão cadeia de caracteres) para criar a recuperação de desastre configuração por meio de **PS/CLI** ou **API REST**.
    >
    >
    > Isso ocorre porque, durante a migração, seu nome de DNS/cadeia de caracteres de conexão namespace Standard de barramento de serviço do Azure em si se torna um alias para seu namespace do barramento de serviço Premium do Azure.
    >
    > Seus aplicativos cliente devem utilizar este alias (ou seja, a Azure Service Bus Standard namespace conexão cadeia de caracteres) para se conectar ao namespace Premium em que o emparelhamento de recuperação de desastre tiver sido configurado.
    >
    > Se você usar o Portal para configurar a configuração de recuperação de desastre, o portal será abstrair essa limitação de você.


4. Use o ***alias*** obtido na etapa 3 para conectar os aplicativos clientes ao namespace primário habilitado para recuperação de desastre geográfico. Inicialmente, o alias aponta para o namespace primário.

5. [Opcional] Adicione um monitoramento para detectar se um failover é necessário.

## <a name="failover-flow"></a>Fluxo do failover

Um failover é disparado manualmente pelo cliente (seja explicitamente por meio de um comando ou por meio da lógica de negócios de propriedade do cliente que aciona o comando) e nunca pelo Azure. Isso dá ao cliente visibilidade e propriedade completa para resolução de interrupção no backbone do Azure.

![4][]

Após o failover ser disparado:

1. A cadeia de caracteres de conexão do ***alias*** é atualizada para apontar para o namespace Premium secundário.

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

## <a name="samples"></a>Amostras

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
> O suporte a Zonas de Disponibilidade para o Barramento de Serviço Premium do Azure só é oferecido nas [regiões do Azure](../availability-zones/az-overview.md#services-support-by-region) em que existem zonas de disponibilidade.

Você pode habilitar as Zonas de Disponibilidade apenas em novos namespaces usando o portal do Azure. O Barramento de Serviço não dá suporte à migração dos namespaces existentes. Você não pode desabilitar a redundância de zona depois de habilitá-la em seu namespace.

![3][]

## <a name="next-steps"></a>Próximas etapas

- Consulte a [referência da API REST de recuperação de desastre em área geográfica aqui](/rest/api/servicebus/disasterrecoveryconfigs).
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
