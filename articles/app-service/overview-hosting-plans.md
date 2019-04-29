---
title: Visão geral dos planos do Serviço de Aplicativo - Azure | Microsoft Docs
description: Saiba como os planos do Serviço de Aplicativo para o Serviço de Aplicativo do Azure funcionam e como eles beneficiam sua experiência de gerenciamento.
keywords: serviço de aplicativo, serviço de aplicativo do azure, escala, escalonável, escalabilidade, plano de serviço de aplicativo, custo de serviço de aplicativo
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ab04d1288eb3a851774128b8aaaae03868c2ffa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60839004"
---
# <a name="azure-app-service-plan-overview"></a>Visão geral do plano do Serviço de Aplicativo do Azure

No Serviço de Aplicativo, um aplicativo é executado em um _Plano de Serviço de Aplicativo_. Um plano de serviço de aplicativo define um conjunto de recursos de computação para um aplicativo Web ser executado. Esses recursos de computação são análogos ao [ _farm de servidores_ ](https://wikipedia.org/wiki/Server_farm) na hospedagem na web convencional. Um ou mais aplicativos podem ser configurados para ser executado nos mesmos recursos de computação (ou no mesmo plano de Serviço de Aplicativo).

Quando você cria um plano de serviço de aplicativo em uma determinada região (por exemplo, Europa Ocidental), um conjunto de recursos de computação é criado para esse plano nessa região. Quaisquer aplicativos que você coloque nesse plano de serviço de aplicativo é executado nesses recursos de computação conforme definido pelo seu plano de serviço de aplicativo. Cada plano de serviço de aplicativo define:

- Região (Oeste dos EUA, Leste dos EUA, etc.)
- Número de instâncias de máquina virtual
- Tamanho de instâncias de máquina virtual (pequeno, médio, grande)
- Tipo de preço (Gratuito, Compartilhado, Básico, Standard, Premium, PremiumV2, Isolado, Consumo)

O _tipo de preço_ de um plano de serviço de aplicativo determina quais recursos do serviço de aplicativo que você obtém e quanto você paga pelo plano. Há algumas categorias de tipos de preços:

- **Computação compartilhada**: **Grátis** e **Compartilhada**, os dois tipos base, executam um aplicativo na mesma máquina virtual do Azure como outros aplicativos do serviço de aplicativo, incluindo aplicativos de outros clientes. Esses tipos alocam cotas de CPU para cada aplicativo que é executado em recursos compartilhados, e não é possível expandir os recursos.
- **Computação dedicada**: Os tipos **Básico**, **Standard**, **Premium**, e **PremiumV2** executam aplicativos em máquinas virtuais dedicadas do Azure. Somente os aplicativos no mesmo plano do serviço de aplicativo compartilham os mesmos recursos de computação. Quanto maior o nível, mais instâncias de máquina virtual estão disponíveis para a sua expansão.
- **Isolado**: Esse tipo executa máquinas virtuais dedicadas do Azure em redes virtuais dedicadas do Azure, que fornece isolamento de rede além do isolamento de computação para os seus aplicativos. Ele fornece a capacidade máxima de expansão.
- **Consumo**: Esse tipo está disponível apenas para [aplicativos de funções](../azure-functions/functions-overview.md). Ele dimensiona as funções dinamicamente, dependendo da carga de trabalho. Para obter mais informações, consulte [Comparação dos planos de hospedagem do Azure Functions](../azure-functions/functions-scale.md).

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Cada tipo também fornece um subconjunto específico de recursos do serviço de aplicativo. Esses recursos incluem domínios personalizados e certificados SSL, dimensionamento automático, slots de implantação, backups, integração do Gerenciador de tráfego do Microsoft Azure e muito mais. Quanto maior o nível, mais recursos estão disponíveis. Para descobrir quais recursos têm suporte em cada tipo de preços, consulte os [Detalhes do plano do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> O novo tipo de preço **PremiumV2** fornece [VMs de série Dv2](../virtual-machines/windows/sizes-general.md#dv2-series), com processadores mais rápidos, armazenamento SSD e relação memória/núcleo duas vezes maior quando comparado ao tipo de preço **Standard**. O **PremiumV2** também dá suporte a uma escala maior por meio da maior contagem de instâncias e ainda fornece todas as funcionalidades avançadas encontradas no plano Standard. Todos os recursos disponíveis no tipo de preço **Premium** existente estão incluídos no **PremiumV2**.
>
> Semelhante a outros tipos de preço dedicados, três tamanhos de VM estão disponíveis para esse tipo de preço:
>
> - Pequeno (um núcleo de CPU, 3,5 GiB de memória) 
> - Médio (dois núcleos de CPU, 7 GiB de memória) 
> - Grande (quatro núcleos de CPU, 14 GiB de memória)  
>
> Para obter informações de preço do **PremiumV2**, confira [Preço do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/).
>
> Para uma introdução ao novo tipo de preço **PremiumV2**, consulte [Configurar o tipo de preço PremiumV2 no Serviço de Aplicativo](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Como o meu aplicativo é executado e dimensionado?

Nos tipos **Grátis** e **Compartilhado**, um aplicativo recebe minutos de CPU em uma instância compartilhada da máquina virtual e não é possível expandir. Nos outros tipos, um aplicativo é executado e pode ser expandido do modo a seguir.

Ao criar um aplicativo no serviço de aplicativo, ele é colocado em um plano do Serviço de Aplicativo. Quando o aplicativo é executado, ele é executado em todas as instâncias da máquina virtual configuradas no plano do serviço de aplicativo. Se vários aplicativos estiverem no mesmo plano do serviço de aplicativo, todos eles compartilham as mesmas instâncias de máquina virtual. Se você tiver vários slots de implantação para um aplicativo, todos os slots de implantação também serão executados nas mesmas instâncias da máquina virtual. Se você habilitar os logs de diagnósticos, executar backups ou executar trabalhos da Web, eles também usarão ciclos de CPU e memória nessas instâncias da máquina virtual.

Dessa forma, o plano de serviço de aplicativo é a unidade de escala dos aplicativos do serviço de aplicativo. Se o plano estiver configurado para executar cinco instâncias da máquina virtual, todos os aplicativos no plano serão executados em todas as cinco instâncias. Se o plano estiver configurado para dimensionamento automático, todos os aplicativos no plano são dimensionados juntos com base nas configurações de dimensionamento automático.

Para obter informações sobre o dimensionamento de um aplicativo, consulte [Dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Quanto custa o plano do serviço de aplicativo?

Esta seção descreve como os aplicativos do serviço de aplicativo são cobrados. Para obter informações detalhadas de preço específicas a certas regiões, consulte [Preço do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/).

Exceto para o tipo **Grátis**, um plano de Serviço de Aplicativo carrega uma carga horária sobre os recursos de computação que usa.

- No tipo **Compartilhado**, cada aplicativo recebe uma cota de minutos de CPU, portanto _cada aplicativo_ é cobrado por hora para a cota de CPU.
- Nos tipos dedicados de computação (**Básico**, **Standard**, **Premium**, **PremiumV2**), o plano do serviço de aplicativo define o número de instâncias de máquina virtual para os quais os aplicativos são dimensionados, portanto _cada instância de máquina virtual_ no plano do serviço de aplicativo tem uma taxa por hora. Essas instâncias de máquina virtual são cobradas da mesma forma, independentemente de quantos aplicativos forem executados nelas. Para evitar cobranças inesperadas, consulte [Limpar um plano do serviço de aplicativo](app-service-plan-manage.md#delete).
- No tipo **Isolado**, o ambiente do serviço de aplicativo define o número de trabalhadores isolados que executam seus aplicativos, e _cada trabalhador_ é cobrado por hora. Além disso, há um valor base por hora para a execução do ambiente do serviço de aplicativo em si. 
- (Somente para o Azure Functions) O tipo de **consumo** aloca dinamicamente as instâncias de máquina virtual para cargas de trabalho de uma função do aplicativo de serviço, e é cobrado dinamicamente por segundo pelo Azure. Para saber mais, confira [Preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Você não é cobrado por usar os recursos do serviço de aplicativo que estão disponíveis para você (como configurar domínios personalizados, certificados SSL, slots de implantação, backups, etc.). As exceções são:

- Domínios do serviço de aplicativo - você paga quando compra um no Azure e quando você o renova todo ano.
- Certificados do serviço de aplicativo - você paga quando compra um no Azure e quando você o renova todo ano.
- Conexões SSL com base em IP - há uma taxa por hora para cada conexão SSL com base em IP, mas alguns tipos **Standard** ou superior fornecem uma conexão SSL com base em IP gratuitamente. Conexões de SSL com base em SNI são gratuitas.

> [!NOTE]
> Se você integrar o serviço de aplicativo com outro serviço do Azure, você precisará considerar encargos desses outros serviços. Por exemplo, se você usar o Azure Traffic Manager para dimensionar o seu aplicativo geograficamente, o Azure Traffic Manager também cobrará você com base em seu uso. Para estimar o custo de serviços no Azure, consulte [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>E se o meu aplicativo precisar de mais recursos?

O seu plano do serviço de aplicativo pode ser dimensionado para mais ou para menos a qualquer momento. É tão simples quanto alterar o tipo de preço do plano. Você pode escolher um tipo de preço mais baixo a um primeiro momento, e aumentá-lo mais tarde quando você precisar de mais recursos do serviço de aplicativo.

Por exemplo, você pode começar a testar seu aplicativo web em um plano do serviço de aplicativo **Grátis** sem pagar nada. Quando você desejar adicionar o [nome DNS personalizado](app-service-web-tutorial-custom-domain.md) para o aplicativo web, apenas dimensione o seu plano até o tipo **Compartilhado**. Posteriormente, quando você desejar adicionar um [certificado SSL personalizado](app-service-web-tutorial-custom-ssl.md), dimensione o seu plano até o tipo **Básico**. Quando você desejar ter [ambientes de preparo](deploy-staging-slots.md), dimensione até o tipo **Standard**. Quando você precisar de mais núcleos, memória ou armazenamento, escale verticalmente para um tamanho maior de máquina virtual no mesmo tipo.

O mesmo funciona na ordem inversa. Quando você achar que não precisa mais dos recursos de um tipo superior, você pode reduzir a escala a um nível mais baixo, o que economiza dinheiro.

Para obter informações sobre como escalar verticalmente seu plano do serviço de aplicativo, acesse [Escalar verticalmente um aplicativo no Azure](web-sites-scale.md).

Se o seu aplicativo estiver no mesmo plano do serviço de aplicativo com outros aplicativos, convém melhorar o desempenho do aplicativo, isolando os recursos de computação. Você pode fazer isso ao mover o aplicativo para um plano do serviço de aplicativo separado. Para obter mais informações, consulte [Mover um aplicativo para outro plano do serviço de aplicativo](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Devo colocar um aplicativo em um novo plano ou um plano existente?

Como você paga pelos recursos de computação que o seu plano do serviço de aplicativo aloca (consulte [Quanto custa o plano de serviço de aplicativo?](#cost)), você pode economizar dinheiro colocando vários aplicativos em um plano do serviço de aplicativo. Você pode continuar a adicionar aplicativos a um plano existente, desde que o plano tenha recursos suficientes para lidar com a carga. No entanto, tenha em mente que os aplicativos no mesmo plano do serviço de aplicativo compartilham os mesmos recursos de computação. Para determinar se o novo aplicativo tem ou não os recursos necessários, você precisa compreender a capacidade do Plano do Serviço de Aplicativo existente e a carga esperada para o novo aplicativo. A sobrecarga de um plano do serviço de aplicativo tem potencial de causar tempo de inatividade para seus aplicativos novos e existentes.

Isole o seu aplicativo em um novo Plano do Serviço de Aplicativo quando:

- O aplicativo faz uso intensivo de recursos.
- Você deseja dimensionar o aplicativo independentemente de outros aplicativos no plano existente.
- O aplicativo precisa de recursos em uma região geográfica diferente.

Dessa forma, você pode alocar um novo conjunto de recursos para seu aplicativo e ter mais controle sobre seus aplicativos.

## <a name="manage-an-app-service-plan"></a>Criar um plano do Serviço de Aplicativo

> [!div class="nextstepaction"]
> [Gerenciar um Plano do Serviço de Aplicativo](app-service-plan-manage.md)
