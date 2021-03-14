---
title: Planos do Serviço de Aplicativo
description: Saiba como os planos do serviço de aplicativo funcionam no serviço Azure App, como eles são cobrados para o cliente e como dimensioná-los para suas necessidades.
keywords: serviço de aplicativo, serviço de aplicativo do azure, escala, escalonável, escalabilidade, plano de serviço de aplicativo, custo de serviço de aplicativo
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 6e5de3cdec7a9c503f4b7bf7056bd62f1ddf682d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594025"
---
# <a name="azure-app-service-plan-overview"></a>Visão geral do plano do Serviço de Aplicativo do Azure

No serviço de aplicativo (aplicativos Web, aplicativos de API ou aplicativos móveis), um aplicativo sempre é executado em um _plano do serviço de aplicativo_. Além disso, [Azure Functions](../azure-functions/dedicated-plan.md) também tem a opção de ser executada em um _plano do serviço de aplicativo_. Um plano de serviço de aplicativo define um conjunto de recursos de computação para um aplicativo Web ser executado. Esses recursos de computação são análogos ao [_farm de servidores_](https://wikipedia.org/wiki/Server_farm) na hospedagem na Web convencional. Um ou mais aplicativos podem ser configurados para ser executado nos mesmos recursos de computação (ou no mesmo plano de Serviço de Aplicativo).

Quando você cria um plano de serviço de aplicativo em uma determinada região (por exemplo, Europa Ocidental), um conjunto de recursos de computação é criado para esse plano nessa região. Quaisquer aplicativos que você coloque nesse plano de serviço de aplicativo é executado nesses recursos de computação conforme definido pelo seu plano de serviço de aplicativo. Cada plano de serviço de aplicativo define:

- Região (Oeste dos EUA, Leste dos EUA, etc.)
- Número de instâncias de VM
- Tamanho de instâncias de máquina virtual (pequeno, médio, grande)
- Tipo de preço (gratuito, compartilhado, básico, Standard, Premium, PremiumV2, PremiumV3, isolado)

O _tipo de preço_ de um plano de serviço de aplicativo determina quais recursos do serviço de aplicativo que você obtém e quanto você paga pelo plano. Há algumas categorias de tipos de preços:

- **Computação compartilhada**: **grátis** e **compartilhada**, os dois tipos base, executam um aplicativo na mesma máquina virtual do Azure como outros aplicativos do serviço de aplicativo, incluindo aplicativos de outros clientes. Esses tipos alocam cotas de CPU para cada aplicativo que é executado em recursos compartilhados, e não é possível expandir os recursos.
- **Computação dedicada**: as camadas **Basic**, **Standard**, **Premium**, **PremiumV2** e **PremiumV3** executam aplicativos em VMs do Azure dedicadas. Somente os aplicativos no mesmo plano do serviço de aplicativo compartilham os mesmos recursos de computação. Quanto maior o nível, mais instâncias de máquina virtual estão disponíveis para a sua expansão.
- **Isolado**: essa camada executa VMs do Azure dedicadas em redes virtuais do Azure dedicadas. Ele fornece isolamento de rede na parte superior do isolamento de computação para seus aplicativos. Ele fornece a capacidade máxima de expansão.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Cada tipo também fornece um subconjunto específico de recursos do serviço de aplicativo. Esses recursos incluem domínios personalizados e certificados TLS/SSL, dimensionamento automático, slots de implantação, backups, integração do Gerenciador de tráfego e muito mais. Quanto maior o nível, mais recursos estão disponíveis. Para descobrir quais recursos têm suporte em cada tipo de preços, consulte os [Detalhes do plano do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv3"></a>

> [!NOTE]
> O novo tipo de preço **PremiumV3** garante que computadores com processadores mais rápidos ( [ACU](../virtual-machines/acu.md) mínimo de 195 por CPU virtual), armazenamento de SSD e proporção de memória para núcleo quádruplo em comparação com a camada **Standard** . O **PremiumV3** também dá suporte à escala maior por meio de maior contagem de instâncias e ainda fornece todos os recursos avançados encontrados na camada **Standard** . Todos os recursos disponíveis na camada **PremiumV2** existente estão incluídos no **PremiumV3**.
>
> Semelhante a outros tipos de preço dedicados, três tamanhos de VM estão disponíveis para esse tipo de preço:
>
> - Pequeno (2 núcleos de CPU, 8 GiB de memória) 
> - Médio (4 núcleos de CPU, 16 GiB de memória) 
> - Grande (8 núcleos de CPU, 32 GiB de memória)  
>
> Para obter informações sobre preços do **PremiumV3** , consulte [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).
>
> Para começar a usar o novo tipo de preço do **PremiumV3** , consulte [Configurar a camada PremiumV3 para o serviço de aplicativo](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Como o meu aplicativo é executado e dimensionado?

Nas camadas **gratuita** e **compartilhada** , um aplicativo recebe minutos de CPU em uma instância de VM compartilhada e não pode escalar horizontalmente. Em outras camadas, um aplicativo é executado e dimensionado da seguinte maneira.

Ao criar um aplicativo no serviço de aplicativo, ele é colocado em um plano do Serviço de Aplicativo. Quando o aplicativo é executado, ele é executado em todas as instâncias da máquina virtual configuradas no plano do serviço de aplicativo. Se vários aplicativos estiverem no mesmo plano do serviço de aplicativo, todos eles compartilham as mesmas instâncias de máquina virtual. Se você tiver vários slots de implantação para um aplicativo, todos os slots de implantação também serão executados nas mesmas instâncias da máquina virtual. Se você habilitar os logs de diagnósticos, executar backups ou executar trabalhos da Web, eles também usarão ciclos de CPU e memória nessas instâncias da máquina virtual.

Dessa forma, o plano de serviço de aplicativo é a unidade de escala dos aplicativos do serviço de aplicativo. Se o plano estiver configurado para executar cinco instâncias da máquina virtual, todos os aplicativos no plano serão executados em todas as cinco instâncias. Se o plano estiver configurado para dimensionamento automático, todos os aplicativos no plano são dimensionados juntos com base nas configurações de dimensionamento automático.

Para obter informações sobre o dimensionamento de um aplicativo, consulte [Dimensionar a contagem de instâncias manual ou automaticamente](../azure-monitor/autoscale/autoscale-get-started.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Quanto custa o plano do serviço de aplicativo?

Esta seção descreve como os aplicativos do serviço de aplicativo são cobrados. Para obter informações detalhadas de preço específicas a certas regiões, consulte [Preço do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/).

Exceto para a camada **gratuita** , um plano do serviço de aplicativo carrega um encargo sobre os recursos de computação que ele usa.

- Na camada **compartilhada** , cada aplicativo recebe uma cota de minutos de CPU, de modo que _cada aplicativo_ é cobrado pela cota de CPU.
- Nas camadas de computação dedicadas (**Basic**, **Standard**, **Premium**, **PremiumV2**, **PremiumV3**), o plano do serviço de aplicativo define o número de instâncias de VM para as quais os aplicativos são dimensionados para que _cada instância de VM_ no plano do serviço de aplicativo seja cobrada. Essas instâncias de máquina virtual são cobradas da mesma forma, independentemente de quantos aplicativos forem executados nelas. Para evitar cobranças inesperadas, consulte [Limpar um plano do serviço de aplicativo](app-service-plan-manage.md#delete).
- Na camada **isolada** , o ambiente do serviço de aplicativo define o número de trabalhadores isolados que executam seus aplicativos e _cada trabalho_ é cobrado. Além disso, há uma tarifa de carimbo simples para a execução do Ambiente do Serviço de Aplicativo em si.

Você não é cobrado pelo uso dos recursos do serviço de aplicativo que estão disponíveis para você (Configurando domínios personalizados, certificados TLS/SSL, slots de implantação, backups, etc.). As exceções são:

- Domínios do serviço de aplicativo - você paga quando compra um no Azure e quando você o renova todo ano.
- Certificados do serviço de aplicativo - você paga quando compra um no Azure e quando você o renova todo ano.
- Conexões TLS baseadas em IP – há uma cobrança por hora para cada conexão TLS baseada em IP, mas alguma camada **Standard** ou superior fornece uma conexão TLS baseada em IP gratuitamente. As conexões TLS baseadas em SNI são gratuitas.

> [!NOTE]
> Se você integrar o serviço de aplicativo com outro serviço do Azure, você precisará considerar encargos desses outros serviços. Por exemplo, se você usar o Azure Traffic Manager para dimensionar o seu aplicativo geograficamente, o Azure Traffic Manager também cobrará você com base em seu uso. Para estimar o custo de serviços no Azure, consulte [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/). 

Deseja otimizar e reduzir seus gastos com a nuvem?

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>E se o meu aplicativo precisar de mais recursos?

O seu plano do serviço de aplicativo pode ser dimensionado para mais ou para menos a qualquer momento. É tão simples quanto alterar o tipo de preço do plano. Você pode escolher um tipo de preço mais baixo em um primeiro momento e depois aumentá-lo quando precisar de mais recursos do Serviço de Aplicativo.

Por exemplo, você pode começar a testar seu aplicativo web em um plano do serviço de aplicativo **Grátis** sem pagar nada. Quando você desejar adicionar o [nome DNS personalizado](app-service-web-tutorial-custom-domain.md) para o aplicativo web, apenas dimensione o seu plano até o tipo **Compartilhado**. Posteriormente, quando você quiser [criar uma associação TLS](configure-ssl-bindings.md), dimensione seu plano até a camada **básica** . Quando você desejar ter [ambientes de preparo](deploy-staging-slots.md), dimensione até o tipo **Standard**. Quando você precisar de mais núcleos, memória ou armazenamento, escale verticalmente para um tamanho maior de máquina virtual no mesmo tipo.

O mesmo funciona na ordem inversa. Quando você achar que não precisa mais dos recursos de um tipo superior, você pode reduzir a escala a um nível mais baixo, o que economiza dinheiro.

Para obter informações sobre como escalar verticalmente seu plano do serviço de aplicativo, acesse [Escalar verticalmente um aplicativo no Azure](manage-scale-up.md).

Se o seu aplicativo estiver no mesmo plano do serviço de aplicativo com outros aplicativos, convém melhorar o desempenho do aplicativo, isolando os recursos de computação. Você pode fazer isso ao mover o aplicativo para um plano do serviço de aplicativo separado. Para obter mais informações, consulte [Mover um aplicativo para outro plano do serviço de aplicativo](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Devo colocar um aplicativo em um novo plano ou um plano existente?

Como você paga pelos recursos de computação que o seu plano do serviço de aplicativo aloca (consulte [Quanto custa o plano de serviço de aplicativo?](#cost)), você pode economizar dinheiro colocando vários aplicativos em um plano do serviço de aplicativo. Você pode continuar a adicionar aplicativos a um plano existente, desde que o plano tenha recursos suficientes para lidar com a carga. No entanto, tenha em mente que os aplicativos no mesmo plano do serviço de aplicativo compartilham os mesmos recursos de computação. Para determinar se o novo aplicativo tem os recursos necessários, você precisa compreender a capacidade do Plano do Serviço de Aplicativo existente e a carga esperada para o novo aplicativo. A sobrecarga de um plano do serviço de aplicativo tem potencial de causar tempo de inatividade para seus aplicativos novos e existentes.

Isole o seu aplicativo em um novo Plano do Serviço de Aplicativo quando:

- O aplicativo faz uso intensivo de recursos.
- Você deseja dimensionar o aplicativo independentemente dos outros aplicativos no plano existente.
- O aplicativo precisa de recursos em uma região geográfica diferente.

Dessa forma, você pode alocar um novo conjunto de recursos para seu aplicativo e ter mais controle sobre seus aplicativos.

## <a name="manage-an-app-service-plan"></a>Criar um plano do Serviço de Aplicativo

> [!div class="nextstepaction"]
> [Criar um plano do Serviço de Aplicativo](app-service-plan-manage.md)