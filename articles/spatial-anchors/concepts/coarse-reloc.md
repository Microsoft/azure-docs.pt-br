---
title: Relocalidade grosseira
description: Saiba mais sobre como usar a relocalização grosseira para encontrar âncoras perto de você.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: fc04242e61c748d7ae52e61e40206ba338a1b6aa
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071114"
---
# <a name="coarse-relocalization"></a>Relocalização grosseira

A relocalização de alta capacidade é um recurso que permite a localização em larga escala fornecendo uma resposta aproximada, mas rápida para a pergunta: *onde está meu dispositivo agora/qual conteúdo devo observar?* A resposta não é precisa, mas, em vez disso, está no formato: *você está perto dessas âncoras; Tente localizar uma delas*.

A relocalização de alta definição funciona marcando âncoras com várias leituras de sensor no dispositivo que são usadas posteriormente para consultas rápidas. Para cenários de posicionamento externo, os dados do sensor normalmente são a posição do GPS (GPS) do dispositivo. Quando o GPS não está disponível ou não é confiável (como inportas), os dados do sensor consistem nos pontos de acesso WiFi e nos beacons do Bluetooth no intervalo. Os dados de sensor coletados contribuem para manter um índice espacial usado pelas âncoras espaciais do Azure para determinar rapidamente quais âncoras estão na proximidade do seu dispositivo.

## <a name="when-to-use-coarse-relocalization"></a>Quando usar a relocalização de altat

Se você estiver planejando lidar com mais de 35 âncoras espaciais em um espaço maior do que um Tribunal de tênis, provavelmente se beneficiará da indexação espacial de alta relocalização.

A pesquisa rápida de âncoras habilitadas por alta relocalização é projetada para simplificar o desenvolvimento de aplicativos apoiados por coleções de escala mundial de âncoras (digamos, milhões de geograficamente distribuídos). A complexidade da indexação espacial está todas ocultas, permitindo que você se concentre na lógica do aplicativo. Toda a âncora pesada é feita para você nos bastidores por âncoras espaciais do Azure.

## <a name="using-coarse-relocalization"></a>Usando a relocalização de alta

O fluxo de trabalho típico para criar e consultar âncoras espaciais do Azure com relocalização grosso é:
1.  Crie e configure um provedor de impressão digital do sensor para coletar dados de sensor de sua escolha.
2.  Inicie uma sessão de âncora espacial do Azure e crie âncoras. Como a impressão digital do sensor está habilitada, as âncoras são indexadas espacialmente por relocalização grosseira.
3.  Consulte as âncoras ao redor usando a relocalização de alta utilização, usando os critérios de pesquisa dedicados na sessão de âncora espacial do Azure.

Você pode consultar o seguinte tutorial correspondente para configurar a relocalização de alta segurança em seu aplicativo:
* [Relocalação de grande no Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Relocalidade alta no Objective-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Relocalação grosseira no Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Relocalidade alta no Java](../how-tos/set-up-coarse-reloc-java.md)
* [Relocalidade alta no C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Relocalidade alta no C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Sensores e plataformas

### <a name="platform-availability"></a>Disponibilidade da plataforma

Os tipos de dados de sensor que você pode enviar para o serviço de âncora são:

* Posição do GPS: latitude, longitude, altitude.
* Intensidade de sinal dos pontos de acesso WiFi no intervalo.
* Intensidade de sinal de beacons Bluetooth no intervalo.

A tabela a seguir resume a disponibilidade dos dados do sensor em plataformas com suporte, juntamente com quaisquer advertências específicas da plataforma:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Não<sup>1</sup>  | Sim<sup>2</sup> | Sim<sup>3</sup> |
| **Fio**        | Sim<sup>4</sup> | Sim<sup>5</sup> | Não  |
| **Beacons BLE** | Sim<sup>6</sup> | Sim<sup>6</sup> | Sim<sup>6</sup>|


<sup>1</sup> um dispositivo GPS externo pode ser associado ao HoloLens. Entre em contato com [nosso suporte](../spatial-anchor-support.md) se você estiver disposto a usar o HoloLens com um controlador de GPS.<br/>
<sup>2</sup> com suporte por meio de APIs de [locationmanager][3] (GPS e rede)<br/>
<sup>3</sup> com suporte por meio de APIs [CLLocationManager][4]<br/>
<sup>4</sup> com suporte em uma taxa de aproximadamente uma verificação a cada 3 segundos <br/>
<sup>5</sup> a partir do nível de API 28, as verificações de WiFi são limitadas a 4 chamadas a cada 2 minutos. No Android 10, a limitação pode ser desabilitada no menu de configurações do desenvolvedor. Para obter mais informações, consulte a [documentação do Android][5].<br/>
<sup>6</sup> limitado a [Eddystone][1] e [iBeacon][2]

### <a name="which-sensor-to-enable"></a>Qual sensor habilitar

A escolha do sensor é específica para o aplicativo que você está desenvolvendo e a plataforma.
O diagrama a seguir fornece um ponto de partida em que a combinação de sensores pode ser habilitada dependendo do cenário de localização:

![Diagrama da seleção de sensores habilitados](media/coarse-relocalization-enabling-sensors.png)

As seções a seguir fornecem mais informações sobre as vantagens e limitações para cada tipo de sensor.

### <a name="gps"></a>GPS

O GPS é a opção de passagem para cenários de uso externo.
Ao usar o GPS em seu aplicativo, tenha em mente que as leituras fornecidas pelo hardware normalmente são:

* frequência assíncrona e baixa (menos de 1 Hz).
* Não confiável/ruidosa (em média, no desvio padrão de 7 m).

Em geral, o so do dispositivo e as âncoras espaciais do Azure farão alguma filtragem e extrapolação no sinal de GPS bruto em uma tentativa de mitigar esses problemas. Esse processamento extra exige tempo para convergência, portanto, para obter os melhores resultados, você deve tentar:

* criar um provedor de impressão digital do sensor o mais cedo possível em seu aplicativo
* manter o provedor de impressão digital do sensor ativo entre várias sessões
* compartilhar o provedor de impressão digital do sensor entre várias sessões

Dispositivos GPS de nível de consumidor normalmente são imprecisos. Um estudo de [Zandenbergen e Barbeau (2011)][6] relata a precisão mediana de telefones celulares com GPS assistido (A-GPS) para cerca de 7 metros – um valor grande a ser ignorado! Para considerar esses erros de medição, o serviço trata as âncoras como distribuições de probabilidade no espaço GPS. Dessa forma, uma âncora é agora a região de espaço que mais provavelmente (isto é, com mais de 95% de confiança) contém sua posição GPS verdadeira e desconhecida.

O mesmo raciocínio é aplicado ao consultar com GPS. O dispositivo é representado como outra região de confiança espacial em relação à sua posição de GPS verdadeira, desconhecida. Descobrir âncoras próximas se traduz em simplesmente localizar as âncoras com regiões de confiança *próximas o suficiente* da região de confiança do dispositivo, conforme ilustrado na imagem abaixo:

![Seleção de candidatos à âncora com GPS](media/coarse-reloc-gps-separation-distance.png)

### <a name="wifi"></a>Wi-Fi

No HoloLens e no Android, a força do sinal WiFi pode ser uma boa opção para habilitar a relocalização de alta granularidade em granularidade.
Sua vantagem é a possível disponibilidade imediata de pontos de acesso WiFi (comum em, por exemplo, espaços do Office ou shopping totais) sem a necessidade de uma configuração extra.

> [!NOTE]
> o iOS não fornece nenhuma API para ler a força do sinal WiFi e, como tal, não pode ser usada para a relocalidade de alta habilitada para WiFi.

Ao usar o WiFi em seu aplicativo, tenha em mente que as leituras fornecidas pelo hardware normalmente são:

* frequência assíncrona e baixa (menos de 0,1 Hz).
* potencialmente limitado no nível do sistema operacional.
* Não confiável/ruidosa (em média, no desvio padrão de 3-dBm).

As âncoras espaciais do Azure tentarão criar um mapa de força de sinal WiFi filtrado durante uma sessão em uma tentativa de mitigar esses problemas. Para obter os melhores resultados, você deve tentar:

* Crie a sessão bem antes de colocar a primeira âncora.
* Mantenha a sessão ativa pelo tempo máximo possível (ou seja, crie todas as âncoras e a consulta em uma sessão).

### <a name="bluetooth-beacons"></a>Beacons Bluetooth
<a name="beaconsDetails"></a>

A implantação cuidadosa de beacons Bluetooth é uma boa solução para cenários de relocalização de grandes volumes de escalabilidade, em que o GPS está ausente ou impreciso. Também é o único método interno com suporte em todas as três plataformas.

Os beacons são geralmente dispositivos versáteis, onde tudo-incluindo UUIDs e endereços MAC-podem ser configurados. As âncoras espaciais do Azure esperam que os beacons sejam identificados exclusivamente por seus UUIDs. A falha ao garantir essa exclusividade provavelmente causará resultados incorretos. Para obter os melhores resultados, você deve:

* Atribua UUIDs exclusivos aos seus beacons.
* implante-os de uma maneira que cubra seu espaço de forma uniforme e, para que pelo menos 3 beacons possam ser acessados de qualquer ponto no espaço.
* passar a lista de UUIDs de Beacon exclusivos para o provedor de impressão digital do sensor

Sinais de rádio, como Bluetooth, são afetados por obstáculos e podem interferir em outros sinais de rádio. Por esses motivos, pode ser difícil adivinhar se o seu espaço é coberto uniformemente. Para garantir uma melhor experiência do cliente, recomendamos que você teste manualmente a cobertura de seus beacons. Isso pode ser feito ao percorrer seu espaço com os dispositivos candidatos e um aplicativo mostrando o Bluetooth no intervalo. Ao testar a cobertura, certifique-se de que você pode alcançar pelo menos 3 beacons de qualquer posição estratégica do seu espaço. Configurar muitos beacons pode resultar em mais interferência entre eles e não vai necessariamente melhorar a precisão da relocalização excessiva.

Os beacons Bluetooth normalmente têm uma cobertura de 80 metros se nenhum gargalo estiver presente no espaço.
Isso significa que, para um espaço que não tem grandes obstáculos, é possível implantar beacons em um padrão de grade a cada 40 metros.

Um Beacon ficando sem bateria afetará os resultados negativamente, portanto, certifique-se de monitorar sua implantação periodicamente para baterias baixas ou inativas.

As âncoras espaciais do Azure só controlarão os beacons Bluetooth que estão na lista UUIDs de proximidade de Beacon conhecidos. Os beacons mal-intencionados programados para ter UUIDs relacionados à permissão podem afetar negativamente a qualidade do serviço. Por esse motivo, você obterá melhores resultados em espaços organizados onde você pode controlar sua implantação.

### <a name="sensors-accuracy"></a>Precisão dos sensores

A precisão do sinal GPS, tanto na criação de âncoras quanto durante as consultas, tem uma grande influência sobre o conjunto de âncoras retornadas. Por outro lado, as consultas baseadas em Wi-Fi/beacons considerarão todas as âncoras que têm pelo menos um ponto de acesso/Beacon em comum com a consulta. Nesse sentido, o resultado de uma consulta baseada em Wi-Fi/beacons é determinado principalmente pelo intervalo físico dos pontos de acesso/beacons e obstruções ambientais.
A tabela a seguir estima o espaço de pesquisa esperado para cada tipo de sensor:

| Sensor      | Raio do espaço de pesquisa (aproximadamente) | Detalhes |
|-------------|:-------:|---------|
| GPS         | 20 m-30 m | Determinado pela incerteza de GPS entre outros fatores. Os números relatados são estimados para a precisão de GPS mediana de telefones celulares com um-GPS, que é 7 metros. |
| Wi-Fi        | 50 m-100 m | Determinado pelo intervalo dos pontos de acesso sem fio. Depende da frequência, força do transmissor, obstruções físicas, interferência e assim por diante. |
| Beacons BLE |  70 m | Determinado pelo intervalo do Beacon. Depende da frequência, da força de transmissão, das obstruções físicas, da interferência e assim por diante. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
