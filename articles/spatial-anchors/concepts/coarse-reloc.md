---
title: Relocalização grosseira
description: Saiba como e quando usar a relocalização grosseira. A relocalização de alta proteção ajuda você a encontrar âncoras que estão perto de você.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f0b04183c4df469d4f723486103790c4f97671b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656168"
---
# <a name="coarse-relocalization"></a>Relocalização grosseira

A relocalização de alta capacidade é um recurso que permite a localização em larga escala fornecendo uma resposta aproximada, mas rápida, a essas perguntas: 
- *Onde está meu dispositivo agora?* 
- *Qual conteúdo devo observar?* 
 
A resposta não é precisa. Está neste formulário: *você está perto dessas âncoras. Tente localizar um* deles.

A relocalização de alta definição funciona marcando âncoras com várias leituras de sensor no dispositivo que são usadas posteriormente para consultas rápidas. Para cenários de posicionamento externo, os dados do sensor normalmente são a posição do GPS (GPS) do dispositivo. Quando o GPS está indisponível ou não é confiável, como quando você está inportando, os dados do sensor consistem nos pontos de acesso Wi-Fi e nos beacons do Bluetooth no intervalo. Os dados de sensor coletados contribuem para a manutenção de um índice espacial usado pelas âncoras espaciais do Azure para determinar rapidamente quais âncoras estão próximas ao seu dispositivo.

## <a name="when-to-use-coarse-relocalization"></a>Quando usar a relocalização de altat

Se você estiver planejando lidar com mais de 35 âncoras espaciais em um espaço maior do que um Tribunal de tênis, você provavelmente se beneficiará da indexação espacial de relocalização alta.

A rápida pesquisa de âncoras habilitadas por alta relocalização é projetada para simplificar o desenvolvimento de aplicativos apoiados por coleções de escala mundial de, por exemplo, milhões de âncoras distribuídas geograficamente. A complexidade da indexação espacial é tudo oculta, para que você possa se concentrar na lógica do aplicativo. Todo o trabalho difícil é feito em segundo plano pelas âncoras espaciais do Azure.

## <a name="using-coarse-relocalization"></a>Usando a relocalização de alta

Aqui está o fluxo de trabalho típico para criar e consultar âncoras espaciais do Azure com relocalização alta:
1.  Crie e configure um provedor de impressão digital do sensor para coletar os dados do sensor desejados.
2.  Inicie uma sessão de âncoras espaciais do Azure e crie as âncoras. Como a impressão digital do sensor está habilitada, as âncoras são indexadas espacialmente por relocalização grosseira.
3.  Consulte âncoras ao redor usando a relocalidade de alta por meio dos critérios de pesquisa dedicados na sessão de âncoras espaciais.

Você pode consultar um desses tutoriais para configurar a relocalização de alta segurança em seu aplicativo:
* [Relocalização grosseira no Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Relocalização grosseira no Objective-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Relocalização grosseira no Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Relocalização grosseira em Java](../how-tos/set-up-coarse-reloc-java.md)
* [Relocalização grosseira em C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Relocalização grosseira no C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Sensores e plataformas

### <a name="platform-availability"></a>Disponibilidade da plataforma

Você pode enviar esses tipos de dados de sensor para o serviço de âncora:

* Posição do GPS: latitude, longitude, altitude
* Intensidade de sinal de pontos de acesso Wi-Fi no intervalo
* Intensidade de sinal de beacons Bluetooth no intervalo

Esta tabela resume a disponibilidade dos dados do sensor em plataformas com suporte e fornece informações das quais você deve estar ciente:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Não<sup>1</sup>  | Sim<sup>2</sup> | Sim<sup>3</sup> |
| **Wi-Fi**        | Sim<sup>4</sup> | Sim<sup>5</sup> | Não  |
| **Beacons BLE** | Sim<sup>6</sup> | Sim<sup>6</sup> | Sim<sup>6</sup>|


<sup>1</sup> um dispositivo GPS externo pode ser associado ao HoloLens. Entre em contato com [nosso suporte](../spatial-anchor-support.md) se você estiver disposto a usar o HoloLens com um controlador de GPS.<br/>
<sup>2</sup> com suporte por meio de APIs de [locationmanager][3] (GPS e rede).<br/>
<sup>3</sup> com suporte por meio de APIs [CLLocationManager][4] .<br/>
<sup>4</sup> com suporte em uma taxa de aproximadamente uma verificação a cada 3 segundos. <br/>
<sup>5</sup> a partir do nível de API 28, as verificações de Wi-Fi são limitadas a quatro chamadas a cada 2 minutos. A partir do Android 10, você pode desabilitar essa limitação no menu de **configurações do desenvolvedor** . Para obter mais informações, consulte a [documentação do Android][5].<br/>
<sup>6</sup> limitado a [Eddystone][1] e [iBeacon][2].

### <a name="which-sensor-to-enable"></a>Qual sensor habilitar

A escolha do sensor depende do aplicativo que você está desenvolvendo e da plataforma.
Este diagrama fornece um ponto de partida para determinar qual combinação de sensores você pode habilitar, dependendo do cenário de localização:

![Diagrama que mostra os sensores habilitados para vários cenários.](media/coarse-relocalization-enabling-sensors.png)

As seções a seguir fornecem mais informações sobre as vantagens e limitações de cada tipo de sensor.

### <a name="gps"></a>GPS

O GPS é a opção de passagem para cenários de uso externo.
Ao usar o GPS em seu aplicativo, tenha em mente que as leituras fornecidas pelo hardware normalmente são:

* Frequência assíncrona e baixa (menos de 1 Hz).
* Não confiável/ruidosa (em média, desvio padrão de 7 m).

Em geral, tanto o so do dispositivo quanto as âncoras espaciais farão alguma filtragem e extrapolação do sinal de GPS bruto em uma tentativa de mitigar esses problemas. Esse processamento extra exige tempo para convergência, portanto, para obter os melhores resultados, você deve tentar:

* Crie um provedor de impressão digital do sensor o mais cedo possível em seu aplicativo.
* Mantenha o provedor de impressão digital do sensor ativo entre várias sessões.
* Compartilhe o provedor de impressão digital do sensor entre várias sessões.

Dispositivos GPS de nível de consumidor normalmente são imprecisos. Um estudo de [Zandenbergen e Barbeau (2011)][6] relata que a precisão mediana de telefones móveis que têm o GPS assistido (A-GPS) é de cerca de 7 metros. Esse é um valor muito grande a ser ignorado! Para considerar esses erros de medição, o serviço trata as âncoras como distribuições de probabilidade no espaço GPS. Portanto, uma âncora é a região de espaço que mais provável (com mais de 95% de confiança) contém sua posição de GPS verdadeira, desconhecida.

O mesmo raciocínio se aplica quando você consulta usando GPS. O dispositivo é representado como outra região de confiança espacial em relação à sua posição de GPS verdadeira, desconhecida. A descoberta de âncoras próximas se traduz em localizar as âncoras com regiões de confiança *próximas o suficiente* da região de confiança do dispositivo, conforme ilustrado aqui:

![Diagrama que ilustra a localização de candidatos a ancoragem usando GPS.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

No HoloLens e no Android, a intensidade de sinal de Wi-Fi pode ser uma boa maneira de habilitar a relocalização de alta do interno.
A vantagem é a possível disponibilidade imediata de pontos de acesso de Wi-Fi (comum em espaços do Office e shopping totais, por exemplo) sem a necessidade de configuração adicional.

> [!NOTE]
> o iOS não fornece uma API para ler Wi-Fi intensidade do sinal, portanto, ele não pode ser usado para a relocalidade alta habilitada via Wi-Fi.

Quando você usa Wi-Fi em seu aplicativo, tenha em mente que as leituras fornecidas pelo hardware normalmente são:

* Frequência assíncrona e baixa (menos de 0,1 Hz).
* Potencialmente limitado no nível do sistema operacional.
* Não confiável/ruidosa (em média, desvio padrão de 3-dBm).

As âncoras espaciais tentarão criar um mapa filtrado de intensidade de sinal de Wi-Fi durante uma sessão em uma tentativa de mitigar esses problemas. Para obter melhores resultados, tente:

* Crie a sessão bem antes de inserir a primeira âncora.
* Mantenha a sessão ativa pelo tempo máximo possível. (Ou seja, crie todas as âncoras e a consulta em uma sessão.)

### <a name="bluetooth-beacons"></a>Beacons Bluetooth
<a name="beaconsDetails"></a>

A implantação cuidadosa de beacons de Bluetooth é uma boa solução para cenários de relocalização de alta escala em ambientes de redirecionamento de grande porte, em que o GPS está ausente ou impreciso. Também é o único método interno com suporte em todas as três plataformas.

Os beacons normalmente são dispositivos versáteis nos quais tudo pode ser configurado, incluindo UUIDs e endereços MAC. As âncoras espaciais do Azure esperam que os beacons sejam identificados exclusivamente por seus UUIDs. Se você não garantir essa exclusividade, provavelmente obterá resultados incorretos. Para obter melhores resultados:

* Atribua UUIDs exclusivos aos seus beacons.
* Implante beacons de uma maneira que cubra seu espaço de forma uniforme e, de modo que pelo menos três beacons possam ser acessados de qualquer ponto no espaço.
* Passe a lista de UUIDs de Beacon exclusivos para o provedor de impressão digital do sensor.

Sinais de rádio como aqueles do Bluetooth são afetados por obstáculos e podem interferir em outros sinais de rádio. Portanto, pode ser difícil adivinhar se o seu espaço é coberto uniformemente. Para garantir uma melhor experiência do cliente, recomendamos que você teste manualmente a cobertura de seus beacons. Você pode conduzir um teste ao percorrer seu espaço com os dispositivos candidatos e um aplicativo que mostra o Bluetooth no intervalo. Ao testar a cobertura, verifique se você pode acessar pelo menos três beacons de qualquer posição estratégica no seu espaço. Ter muitos beacons pode resultar em mais interferência entre eles e não vai necessariamente melhorar a precisão da relocalização de alta.

Os beacons Bluetooth normalmente cobrem 80 metros se nenhum gargalo estiver presente no espaço.
Portanto, para um espaço que não tem grandes obstáculos, você pode implantar beacons em um padrão de grade a cada 40 metros.

Um Beacon que está ficando sem bateria afetará os resultados, portanto, certifique-se de monitorar sua implantação periodicamente em busca de baterias baixas ou não encarregadas.

As âncoras espaciais do Azure rastrearão somente os beacons Bluetooth que estão na lista UUIDs de proximidade de Beacon conhecidos. Mas beacons mal-intencionados programados para ter UUIDs allowlisted podem afetar negativamente a qualidade do serviço. Assim, você obterá os melhores resultados em espaços organizados em que é possível controlar a implantação de Beacon.

### <a name="sensor-accuracy"></a>Precisão do sensor

A precisão do sinal GPS, durante a criação de âncora e durante as consultas, tem uma influência significativa no conjunto de âncoras retornadas. Por outro lado, as consultas baseadas em Wi-Fi/beacons considerarão todas as âncoras que têm pelo menos um ponto de acesso/Beacon em comum com a consulta. Nesse sentido, o resultado de uma consulta baseada em Wi-Fi/beacons é determinado principalmente pelo intervalo físico dos pontos de acesso/beacons e obstruções ambientais.
Esta tabela estima o espaço de pesquisa esperado para cada tipo de sensor:

| Sensor      | RADIUS de espaço de pesquisa (aproximado) | Detalhes |
|-------------|:-------:|---------|
| **GPS**         | 20 m a 30 m | Determinado pela incerteza de GPS, entre outros fatores. Os números relatados são estimados para a precisão de GPS mediana de telefones celulares com um-GPS: 7 metros. |
| **Wi-Fi**        | 50 m a 100 m | Determinado pelo intervalo dos pontos de acesso sem fio. Depende da frequência, força do transmissor, obstruções físicas, interferência e assim por diante. |
| **Beacons BLE** |  70 m | Determinado pelo intervalo do Beacon. Depende da frequência, da força de transmissão, das obstruções físicas, da interferência e assim por diante. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
