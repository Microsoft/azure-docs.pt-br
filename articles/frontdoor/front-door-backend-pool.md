---
title: Front-ends e pools de back-end na porta frontal do Azure | Microsoft Docs
description: Este artigo descreve o que os back-ends e os pools de backend estão na configuração de porta frontal.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4beba141fec7a819df52e4c3a669312a4ad76998
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449289"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Front-ends e pools de back-end na porta frontal do Azure
Este artigo descreve os conceitos sobre como mapear sua implantação de aplicativo Web com a porta frontal do Azure. Ele também explica as diferentes terminologias usadas na configuração de porta frontal em volta dos back-ends do aplicativo.

## <a name="backends"></a>Back-ends
Um back-end refere-se a uma implantação de aplicativo Web em uma região. A porta frontal dá suporte a recursos do Azure e não Azure no pool de back-end. O aplicativo pode estar no datacenter local ou localizado em outro provedor de nuvem.

Os back-ends da porta frontal referem-se ao nome do host ou IP público do seu aplicativo que atende às solicitações do cliente. Os back-ends não devem ser confundidos com a camada do banco de dados, a camada de armazenamento e assim por diante. Os back-ends devem ser exibidos como o ponto de extremidade público para o backend do aplicativo. Ao adicionar um back-end a um pool de back-end da porta frontal, você também deve adicionar o seguinte:

- **Tipo de host de back-end**. O tipo de recurso que você deseja adicionar. A porta frontal dá suporte à descoberta automática de back-ends de seu aplicativo do serviço de aplicativo, serviço de nuvem ou armazenamento. Se você quiser um recurso diferente no Azure ou mesmo em um back-end não Azure, selecione **host personalizado**.

    >[!IMPORTANT]
    >Durante a configuração, as APIs não validam se o back-end é inacessível de ambientes de porta frontal. Verifique se a porta frontal pode alcançar o back-end.

- **Nome do host de back-end e assinatura**. Se você não tiver selecionado **host personalizado** para tipo de host de back-end, selecione o back-end escolhendo a assinatura apropriada e o nome de host de back-end correspondente na interface do usuário.

- **Cabeçalho de host de back-end**. O valor do cabeçalho de host enviado ao back-end para cada solicitação. Para obter mais informações, consulte [cabeçalho de host de back-end](#hostheader).

- **Prioridade**. Atribua prioridades a seus back-ends diferentes quando desejar usar um backend de serviço primário para todo o tráfego. Além disso, forneça backups se os back-ends primário ou de backup estiverem indisponíveis. Para obter mais informações, consulte [Priority](front-door-routing-methods.md#priority).

- **Peso**. Atribua pesos aos seus back-ends diferentes para distribuir o tráfego entre um conjunto de back-ends, seja uniformemente ou de acordo com os coeficientes de peso. Para obter mais informações, consulte [pesos](front-door-routing-methods.md#weighted).

### <a name="backend-host-header"></a><a name = "hostheader"></a>Cabeçalho de host de back-end

As solicitações encaminhadas pela porta frontal para um back-end incluem um campo de cabeçalho de host que o back-end usa para recuperar o recurso de destino. O valor para esse campo normalmente é proveniente do URI de back-end que tem o cabeçalho e a porta do host.

Por exemplo, uma solicitação feita para `www.contoso.com` o terá o cabeçalho de host www.contoso.com. Se você usar portal do Azure para configurar o back-end, o valor padrão desse campo será o nome do host do back-end. Se o back-end for contoso-westus.azurewebsites.net, no portal do Azure, o valor preenchido automaticamente para o cabeçalho de host de back-end será contoso-westus.azurewebsites.net. No entanto, se você usar modelos de Azure Resource Manager ou outro método sem definir explicitamente esse campo, a porta frontal enviará o nome de host de entrada como o valor para o cabeçalho de host. Se a solicitação foi feita para www \. contoso.com e o back-end for contoso-westus.azurewebsites.NET que tem um campo de cabeçalho vazio, a porta frontal definirá o cabeçalho de host como www \. contoso.com.

A maioria dos back-ends de aplicativo (aplicativos Web do Azure, armazenamento de BLOBs e serviços de nuvem) exige que o cabeçalho de host corresponda ao domínio do back-end. No entanto, o host de front-end que roteia para seu back-end usará um nome de host diferente, como www.contoso.net.

Se o back-end exigir que o cabeçalho de host corresponda ao nome de host de back-end, verifique se o cabeçalho de host de back-end inclui o nome de host do back-end.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configurando o cabeçalho de host do back-end para o back-end

Para configurar o campo de **cabeçalho de host de back-end** para um back-end na seção pool de back-end:

1. Abra o recurso de porta frontal e selecione o pool de back-end com o back-end para configurar.

2. Adicione um back-end se você ainda não tiver feito isso, ou editar um existente.

3. Defina o campo de cabeçalho de host de back-end como um valor personalizado ou deixe em branco. O nome do host para a solicitação de entrada será usado como o valor do cabeçalho de host.

## <a name="backend-pools"></a>Pools de back-end
Um pool de back-end na porta frontal refere-se ao conjunto de front-ends que recebem tráfego semelhante para seu aplicativo. Em outras palavras, trata-se de um agrupamento lógico de suas instâncias de aplicativo em todo o mundo que recebem o mesmo tráfego e respondem com o comportamento esperado. Esses back-ends são implantados em diferentes regiões ou na mesma região. Todos os back-ends podem estar no modo de implantação ativo/ativo ou o que é definido como configuração ativa/passiva.

Um pool de back-end define como os back-ends diferentes devem ser avaliados por meio de investigações de integridade. Ele também define como ocorre o balanceamento de carga entre eles.

### <a name="health-probes"></a>Investigações de integridade
A porta frontal envia solicitações de investigação HTTP/HTTPS periódicas para cada um dos back-ends configurados. As solicitações de investigação determinam a proximidade e a integridade de cada back-end para balancear a carga de suas solicitações do usuário final. As configurações de investigação de integridade para um pool de back-end definem como Sondamos o status de integridade dos back-ends do aplicativo. As configurações a seguir estão disponíveis para a configuração de balanceamento de carga:

- **Caminho**: a URL usada para solicitações de investigação para todos os back-ends no pool de back-end. Por exemplo, se um dos seus back-ends for contoso-westus.azurewebsites.net e o caminho for definido como/Probe/Test.aspx, os ambientes de porta frontal, supondo que o protocolo está definido como HTTP, enviará solicitações de investigação de integridade para http \: //contoso-westus.azurewebsites.net/Probe/Test.aspx.

- **Protocolo**: define se as solicitações de investigação de integridade devem ser enviadas da porta frontal para seus back-ends com o protocolo http ou HTTPS.

- **Método**: o método http a ser usado para enviar investigações de integridade. As opções incluem GET ou HEAD (padrão).
    > [!NOTE]
    > Para menor carga e custo em seus back-ends, a porta da frente recomenda o uso de solicitações HEAD para investigações de integridade.

- **Intervalo (segundos)**: define a frequência das investigações de integridade para seus back-ends ou os intervalos nos quais cada um dos ambientes de porta frontal envia uma investigação.

    >[!NOTE]
    >Para failovers mais rápidos, defina o intervalo para um valor mais baixo. Quanto menor o valor, maior será o volume de investigação de integridade que os back-ends receberão. Por exemplo, se o intervalo for definido como 30 segundos com digamos, 100 a porta frontal será exibida globalmente, cada back-end receberá cerca de 200 solicitações de investigação por minuto.

Para obter mais informações, consulte [investigações de integridade](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Configurações de balanceamento de carga
As configurações de balanceamento de carga para o pool de back-end definem como avaliamos as investigações de integridade. Essas configurações determinam se o back-end está íntegro ou não íntegro. Eles também verificam como balancear a carga do tráfego entre back-ends diferentes no pool de backend. As configurações a seguir estão disponíveis para a configuração de balanceamento de carga:

- **Tamanho da amostra**. Identifica quantos exemplos de investigações de integridade precisamos considerar para a avaliação de integridade de back-end.

- **Tamanho de amostra bem-sucedido**. Define o tamanho da amostra conforme mencionado anteriormente, o número de amostras bem-sucedidas necessárias para chamar o back-end íntegro. Por exemplo, suponha que um intervalo de investigação de integridade de porta frontal seja de 30 segundos, o tamanho da amostra seja 5 e o tamanho de amostra bem-sucedido seja 3. Cada vez que avaliamos as investigações de integridade para seu back-end, examinamos os últimos cinco exemplos em 150 segundos (5 x 30). Pelo menos três investigações bem-sucedidas são necessárias para declarar o back-end como íntegro.

- **Sensibilidade de latência (latência adicional)**. Define se você deseja que a porta frontal envie a solicitação para os back-ends dentro do intervalo de sensibilidade de medição de latência ou encaminhe a solicitação para o backend mais próximo.

Para obter mais informações, consulte [método de roteamento com base em latência mínima](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Próximas etapas

- [Criar um perfil de Front Door](quickstart-create-front-door.md)
- [Como funciona a porta frontal](front-door-routing-architecture.md)
