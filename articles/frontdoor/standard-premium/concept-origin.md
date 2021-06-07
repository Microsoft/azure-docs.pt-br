---
title: Grupo de origem e origem no Azure front door Standard/Premium
description: Este artigo descreve qual origem e grupo de origem estão em uma configuração de porta frontal do Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 42a9a0ea23faa481140a46ec52b2214431dd723e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101098517"
---
# <a name="origin-and-origin-group-in-azure-front-door-standardpremium-preview"></a>Grupo de origem e origem no Azure front door Standard/Premium (visualização)

> [!Note]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Está procurando informações sobre o Azure Front Door? Veja [aqui](../front-door-overview.md).

Este artigo abordará os conceitos sobre como a implantação de seu aplicativo Web funciona com o Azure front door Standard/Premium. Você também aprenderá sobre o que é um *grupo* de *origem* e de origem na configuração Standard/Premium da porta do Azure.

> [!IMPORTANT]
> O Azure Front Door Standard/Premium (versão prévia) está na fase de versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="origin"></a>Origem

A origem padrão/Premium da porta do Azure refere-se ao nome do host ou IP público do seu aplicativo que atende às solicitações do cliente. O padrão de porta frontal do Azure/Premium dá suporte a recursos do Azure e não Azure em um grupo de origem. O aplicativo também pode ser hospedado em seu datacenter local ou com outro provedor de nuvem. A origem não deve ser confundida com a camada de banco de dados ou a camada de armazenamento. A origem deve ser exibida como o ponto de extremidade público para o back-end do aplicativo. Ao adicionar uma origem a um grupo de origem padrão/Premium da porta do Azure, você também deve adicionar as seguintes informações:

* **Tipo de origem:** O tipo de recurso que você deseja adicionar. A porta frontal dá suporte à descoberta automática de back-ends de seu aplicativo do serviço de aplicativo, serviço de nuvem ou armazenamento. Se você quiser um recurso diferente no Azure ou mesmo em um back-end não Azure, selecione **host personalizado**.

    >[!IMPORTANT]
    >Durante a configuração, as APIs não validam se a origem não está acessível a partir do ambiente de porta frontal. Verifique se a porta frontal pode alcançar sua origem.

* **Nome do host de origem e assinatura:** Se você não selecionou **host personalizado** para o tipo de host de back-end, selecione o back-end escolhendo a assinatura apropriada e o nome de host de back-end correspondente.

* **Cabeçalho de host de origem:** O valor do cabeçalho de host enviado ao back-end para cada solicitação. Para obter mais informações, consulte [cabeçalho de host de origem](#hostheader).

* **Prioridade:** Atribua prioridades à sua origem diferente quando desejar usar uma origem primária para todo o tráfego. Além disso, forneça backups se as origens primárias ou de backup estiverem indisponíveis. Para obter mais informações, consulte [Priority](#priority).

* **Peso:** Atribua pesos à sua origem diferente para distribuir o tráfego entre um conjunto de origens, seja uniformemente ou de acordo com os coeficientes de peso. Para obter mais informações, consulte [pesos](#weighted).

### <a name="origin-host-header"></a><a name = "hostheader"></a>Cabeçalho de host de origem

Solicitações que são encaminhadas pelo Azure front door Standard/Premium para uma origem incluirão um campo de cabeçalho de host que a origem usa para recuperar o recurso de destino. O valor para esse campo normalmente vem do URI de origem que tem o cabeçalho e a porta do host.

Por exemplo, uma solicitação feita para terá `www.contoso.com` o cabeçalho de host `www.contoso.com` . Se você usar portal do Azure para configurar sua origem, o valor padrão desse campo será o nome do host do back-end. Se sua origem for `contoso-westus.azurewebsites.net` , na portal do Azure, o valor preenchido automaticamente para o cabeçalho de host de origem será `contoso-westus.azurewebsites.net` . No entanto, se você usar modelos de Azure Resource Manager ou outro método sem definir explicitamente esse campo, a porta frontal enviará o nome de host de entrada como o valor para o cabeçalho de host. Se a solicitação foi feita para `www.contoso.com` , e sua origem for `contoso-westus.azurewebsites.net` que tem um campo de cabeçalho vazio, a porta da frente definirá o cabeçalho do host como `www.contoso.com` .

A maioria dos back-ends de aplicativo (aplicativos Web do Azure, armazenamento de BLOBs e serviços de nuvem) exige que o cabeçalho de host corresponda ao domínio do back-end. No entanto, o host de front-end que roteia para seu back-end usará um nome de host diferente, como `www.contoso.net` .

Se sua origem exigir que o cabeçalho de host corresponda ao nome de host de back-end, certifique-se de que o cabeçalho de host de back-end inclui o hostname do back-end.

#### <a name="configuring-the-origin-host-header-for-the-origin"></a>Configurando o cabeçalho de host de origem para a origem

Para configurar o campo de **cabeçalho de host de origem** para uma origem na seção do grupo de origem:

1. Abra o recurso de porta frontal e selecione o grupo de origem com a origem a ser configurada.

2. Adicione uma origem se você ainda não tiver feito isso ou editar uma existente.

3. Defina o campo de cabeçalho de host de origem como um valor personalizado ou deixe em branco. O nome do host para a solicitação de entrada será usado como o valor do cabeçalho de host.

## <a name="origin-group"></a>Grupo de origem

Um grupo de origem no Azure front door Standard/Premium refere-se ao conjunto de origens que recebe tráfego semelhante para seus aplicativos. Em outras palavras, trata-se de um agrupamento lógico de suas instâncias de aplicativo em todo o mundo que recebem o mesmo tráfego e respondem com um comportamento esperado. Essas origens podem ser implantadas em diferentes regiões ou na mesma região. Todas as origens podem estar no modo de implantação ativo/ativo ou o que é definido como configuração ativa/passiva.

Um grupo de origem define como as origens devem ser avaliadas por meio de investigações de integridade. Ele também define como ocorre o balanceamento de carga entre eles.

### <a name="health-probes"></a>Investigações de integridade

O Azure front door Standard/Premium envia solicitações de investigação HTTP/HTTPS periódicas para cada uma de suas origens configuradas. As solicitações de investigação determinam a proximidade e a integridade de cada origem para balancear a carga de suas solicitações do usuário final. As configurações de investigação de integridade para um grupo de origem definem como sondar o status de integridade de back-ends do aplicativo. As configurações a seguir estão disponíveis para a configuração de balanceamento de carga:

* **Caminho**: a URL usada para solicitações de investigação para todas as origens no grupo de origem. Por exemplo, se uma das suas origens é `contoso-westus.azurewebsites.net` e o caminho é definido como/Probe/Test.aspx, os ambientes de porta frontal, supondo que o protocolo é http, enviará solicitações de investigação de integridade para `http://contoso-westus.azurewebsites.net/probe/test.aspx` .

* **Protocolo**: define se as solicitações de investigação de integridade devem ser enviadas da porta frontal para suas origens com o protocolo http ou HTTPS.

* **Método**: o método http a ser usado para enviar investigações de integridade. As opções incluem GET ou HEAD (padrão).
    > [!NOTE]
    > Para menor carga e custo em seus back-ends, a porta da frente recomenda o uso de solicitações HEAD para investigações de integridade.

* **Intervalo (segundos)**: define a frequência das investigações de integridade para suas origens ou os intervalos nos quais cada um dos ambientes de porta frontal envia uma investigação.

    >[!NOTE]
    >Para failovers mais rápidos, defina o intervalo para um valor mais baixo. Quanto menor o valor, maior será o volume de investigação de integridade que os back-ends receberão. Por exemplo, se o intervalo for definido como 30 segundos com digamos, 100 a porta frontal será exibida globalmente, cada back-end receberá cerca de 200 solicitações de investigação por minuto.

Para obter mais informações, consulte [investigações de integridade](concept-health-probes.md).

### <a name="load-balancing-settings"></a>Configurações de balanceamento de carga

As configurações de balanceamento de carga para o grupo de origem definem como avaliamos as investigações de integridade. Essas configurações determinam se a origem está íntegra ou não íntegra. Eles também verificam como balancear a carga do tráfego entre origens diferentes no grupo de origem. As configurações a seguir estão disponíveis para a configuração de balanceamento de carga:

* **Tamanho da amostra:** Identifica quantos exemplos de investigações de integridade precisamos considerar para a avaliação da integridade da origem.

* **Tamanho de amostra bem-sucedido:** Define o tamanho da amostra conforme mencionado anteriormente, o número de amostras bem-sucedidas necessárias para chamar a origem íntegra. Por exemplo, suponha que um intervalo de investigação de integridade de porta frontal seja de 30 segundos, o tamanho da amostra seja 5 e o tamanho de amostra bem-sucedido seja 3. Cada vez que avaliamos as investigações de integridade para sua origem, examinamos os últimos cinco exemplos em 150 segundos (5 x 30). Pelo menos três investigações bem-sucedidas são necessárias para declarar a origem como íntegra.

* **Sensibilidade de latência (latência extra):** Define se você deseja que o Azure front door Standard/Premium envie a solicitação para a origem dentro do intervalo de sensibilidade de medição de latência ou encaminhe a solicitação para o back-end mais próximo.

Para obter mais informações, consulte [método de roteamento com base em latência mínima](#latency).

## <a name="routing-methods"></a>Métodos de roteamento

O padrão de porta frontal do Azure/Premium dá suporte a diferentes tipos de métodos de roteamento de tráfego para determinar como rotear seu tráfego HTTP/HTTPS para diferentes pontos de extremidade de serviço. Quando o cliente solicita a porta frontal, o método de roteamento configurado é aplicado para garantir que as solicitações sejam encaminhadas para a melhor instância de back-end. 

Há quatro métodos de roteamento de tráfego disponíveis no Azure front door Standard/Premium:

* **[Latência](#latency):** o roteamento baseado em latência garante que as solicitações sejam enviadas para os back-ends de latência mais baixos aceitáveis dentro de um intervalo de sensibilidade. Basicamente, suas solicitações de usuário são enviadas para o conjunto "mais próximo" de back-ends em relação à latência de rede.
* **[Prioridade](#priority):** Você pode atribuir prioridades a seus back-ends quando desejar configurar um back-end primário para atender a todo o tráfego. O back-end secundário pode ser um backup caso o back-end primário fique indisponível.
* **[Ponderado](#weighted):** Você pode atribuir pesos aos back-ends quando desejar distribuir o tráfego entre um conjunto de back-ends. Se você deseja distribuir uniformemente ou de acordo com os coeficientes de peso.

Todas as configurações padrão/Premium da porta do Azure incluem o monitoramento de integridade de back-end e failover global instantâneo automatizado. Para obter mais informações, consulte [monitoramento de back-end](concept-health-probes.md). Sua porta frontal pode trabalhar com base em um único método de roteamento. Mas, dependendo das necessidades do seu aplicativo, você também pode combinar vários métodos de roteamento para criar uma topologia de roteamento ideal.

### <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Roteamento de tráfego baseado em latências mais baixas

A implantação de back-ends em dois ou mais locais em todo o mundo pode melhorar a capacidade de resposta de seus aplicativos roteando o tráfego para o destino que é ' mais próximo ' aos seus usuários finais. O método de roteamento de tráfego padrão para a configuração de porta frontal encaminha as solicitações de seus usuários finais para o back-end mais próximo do ambiente de porta frontal que recebeu a solicitação. Combinado com a arquitetura anycast da porta frontal do Azure, essa abordagem garante que cada um de seus usuários finais obtenha o máximo de desempenho personalizado com base em sua localização.

O back-end ' mais próximo ' não é necessariamente mais próximo que medido por distância geográfica. Em vez disso, o Front Door determina os back-ends mais próximos medindo a latência de rede.

Veja abaixo o fluxo geral de decisão:

| Back-ends disponíveis | Prioridade | Sinal de latência (baseado na investigação de integridade) | Pesos |
|-------------| ----------- | ----------- | ----------- |
| Primeiro, selecione todos os back-ends que estão habilitados e retornou íntegro (200 OK) para a investigação de integridade. Se houver seis back-ends A, B, C, D, e e F e entre eles, o C não está íntegro e o E está desabilitado. A lista de back-ends disponíveis é A, B, D e F.  | Em seguida, os back-ends de prioridade superior entre os disponíveis são selecionados. Se o back-end A, B e D tiverem prioridade 1 e o back-end F tiver uma prioridade de 2. Em seguida, os back-ends selecionados serão A, B e D.| Selecione os back-ends com intervalo de latência (menor latência e sensibilidade à latência especificadas em ms). Se o back-end A for 15 ms, B será 30 ms e D é de 60 MS longe do ambiente de porta frontal em que a solicitação descarregou, e a sensibilidade de latência for de 30 ms, o pool de latência mais baixo consistirá no back-end A e B, porque D está além de 30 ms para longe do back-end mais próximo que é um. | Por fim, o Front Door realizará round robin no tráfego entre o pool final de back-ends selecionado conforme a proporção de ponderação especificada. Por exemplo, se um back-end A tiver um peso 5 e o B tiver um peso 8, o tráfego será distribuído conforme a proporção de 5:8 entre os back-ends A e B. |

>[!NOTE]
> Por padrão, a propriedade de sensibilidade à latência é definida como 0 ms, ou seja, a solicitação sempre é encaminhada para o back-end mais rápido disponível.

### <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Roteamento de tráfego baseado em prioridade

Muitas vezes, uma organização deseja fornecer alta disponibilidade para seus serviços implantando mais de um serviço de backup, caso o primário fique inativo. Em toda a indústria, essa topologia também é conhecida como topologia de implantação Ativo/Em espera ou Ativo/Passivo. O método de roteamento de tráfego por “Prioridade” permite que os clientes do Azure implementem esse padrão de failover com facilidade.

O Front Door padrão contém uma lista de back-ends de igual prioridade. Por padrão, o Front Door envia o tráfego somente para os back-ends de prioridade mais alta (valor mais baixo de prioridade), ou seja, o conjunto principal de back-ends. Se os back-ends primários não estiverem disponíveis, a porta frontal roteará o tráfego para o conjunto secundário de back-ends (segundo valor mais baixo para prioridade). Se os back-ends primário e secundário não estiverem disponíveis, o tráfego passará para o terceiro e assim por diante. A disponibilidade do back-end depende do status configurado (habilitado ou desabilitado) e do status contínuo de integridade do back-end, conforme determinado pela investigações de integridade.

#### <a name="configuring-priority-for-backends"></a>Configurar prioridade para back-ends

Cada back-end em seu pool de back-end da configuração de porta frontal tem uma propriedade chamada ' priority ', que pode ser um número entre 1 e 5. Com a porta frontal do Azure, você configura a prioridade de back-end explicitamente usando essa propriedade para cada back-end. Essa propriedade é um valor entre 1 e 5. Valores mais baixos representam uma prioridade mais alta. Os back-ends podem ter os mesmos valores de prioridade.

### <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Método de roteamento de tráfego ponderado
O método de roteamento de tráfego “Ponderado” permite que você distribua o tráfego de maneira uniforme ou use uma ponderação predefinida.

No Método de roteamento de tráfego ponderado, você atribui um peso a cada back-end na configuração do Front Door do seu pool de back-ends. Cada peso é um inteiro de 1 a 1000. Esse parâmetro usa um peso padrão de “50”.

Com a lista de back-ends disponíveis que têm uma sensibilidade de latência aceitável, o tráfego é distribuído com um mecanismo de rodízio usando a taxa de pesos especificados. Se a sensibilidade de latência for definida como 0 milissegundos, essa propriedade não entrará em vigor, a menos que haja dois back-ends com a mesma latência de rede. 

O método ponderado permite alguns cenários úteis:

* **Atualização gradual de aplicativos**: fornece uma porcentagem de tráfego a ser roteado para um novo back-end e aumenta gradualmente o tráfego ao longo do tempo para trazê-lo em um mesmo nível com outros back-ends.
* **Migração de aplicativo para o Azure**: crie um pool de back-end com back-ends externos e do Azure. Ajuste o peso dos back-ends para dar preferência aos novos back-ends. Você pode definir isso gradualmente começando com os novos back-ends desabilitados e atribuindo a eles pesos mais baixos, aumentando lentamente até alcançar os níveis em que eles receberão a maior parte do tráfego. Por fim, basta desabilitar os back-end menos preferenciais e removê-los do pool.  
* **Estouro de nuvem para capacidade adicional**: expanda rapidamente uma implantação local na nuvem colocando-a atrás do Front Door. Quando você precisar de capacidade extra na nuvem, poderá adicionar ou habilitar mais back-ends e especificar qual parte do tráfego vai para cada back-end.

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar uma porta de front-Standard/Premium](create-front-door-portal.md)
