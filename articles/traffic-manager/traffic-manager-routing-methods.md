---
title: Gerenciador de Tráfego do Azure – métodos de roteamento de tráfego
description: Estes artigos o ajudam a entender os diferentes métodos de roteamento de tráfego usados pelo Gerenciador de Tráfego
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: allensu
ms.openlocfilehash: 305f24fc274ad48f5c60762223b7bf4e970fe083
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333741"
---
# <a name="traffic-manager-routing-methods"></a>Métodos de roteamento do Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure dá suporte a seis métodos de roteamento de tráfego para determinar como rotear o tráfego de rede aos vários pontos de extremidade de serviço. O Gerenciador de Tráfego aplica o método de roteamento de tráfego associado a cada perfil para cada consulta DNS recebida. O método de roteamento de tráfego determina qual ponto de extremidade é retornado na resposta DNS.

Os seguintes métodos de roteamento de tráfego estão disponíveis no Gerenciador de Tráfego:

* **[Prioridade](#priority):** Selecione **Prioridade** quando desejar usar um ponto de extremidade de serviço primário para todo o tráfego e fornecer backups, caso os pontos de extremidade primário ou de backup não estejam disponíveis.
* **[Ponderado](#weighted):** Selecione **Ponderado** quando quiser distribuir o tráfego entre um conjunto de pontos de extremidade, seja uniformemente ou de acordo com os pesos que você definir.
* **[Desempenho](#performance):** Selecione **Desempenho** quando tiver pontos de extremidade em diferentes regiões e quiser que os usuários finais usem o ponto de extremidade “mais próximo” em termos de menor latência de rede.
* **[Geográfico](#geographic):** Selecione **Geográfico** para que os usuários sejam direcionados para pontos de extremidade específicos (Azure, Externo ou Aninhado) com base no local geográfico dos quais as respectivas consultas DNS são originadas. Isso permite que os clientes do Gerenciador de Tráfego habilitem cenários em que saber a região geográfica de um usuário e roteá-lo com base nela é importante. Os exemplos incluem conformidade com normas de soberania de dados, localização de conteúdo e experiência do usuário, bem como medição do tráfego de diferentes regiões.
* **[Vários valores](#multivalue):** Selecione **MultiValue** para os perfis do Gerenciador de Tráfego que podem ter apenas endereços IPv4/IPv6 como pontos de extremidade. Quando uma consulta for recebida para este perfil, todos os pontos de extremidade íntegros serão retornados.
* **[Sub-rede](#subnet):** Selecione o método de roteamento de tráfego da **sub-rede** para mapear conjuntos de intervalos de endereços IP do usuário final para um ponto de extremidade específico dentro de um perfil do Gerenciador de Tráfego. Quando uma solicitação for recebida, o ponto de extremidade retornado será o mapeado para o endereço IP de origem da solicitação. 


Todos os perfis do Gerenciador de Tráfego incluem o monitoramento da integridade do ponto de extremidade e o failover automático do ponto de extremidade. Para obter mais informações, consulte [Monitoramento do Ponto de Extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md). Um único perfil do Gerenciador de Tráfego pode usar apenas um único método de roteamento de tráfego. Você pode selecionar um método de roteamento de tráfego diferente para o seu perfil a qualquer momento. As alterações são aplicadas em até um minuto e não há tempo de inatividade. Métodos de roteamento de tráfego podem ser combinados usando perfis aninhados do Gerenciador de Tráfego. O aninhamento permite configurações sofisticadas e flexíveis de roteamento de tráfego para atender às necessidades de aplicativos maiores e mais complexos. Para obter mais informações, consulte [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Método de roteamento de tráfego por prioridade

Frequentemente, as organizações desejam fornecer confiabilidade para seus serviços implantando um ou mais serviços de backup, caso seu serviço primário fique inativo. O método de roteamento de tráfego por “Prioridade” permite que os clientes do Azure implementem esse padrão de failover com facilidade.

![Método de roteamento de tráfego por “Prioridade” do Gerenciador de Tráfego do Azure](media/traffic-manager-routing-methods/priority.png)

O perfil do Gerenciador de Tráfego contém uma lista priorizada de pontos de extremidade de serviço. Por padrão, o Gerenciador de Tráfego envia todo o tráfego para o ponto de extremidade primário (prioridade mais alta). Se o ponto de extremidade primário não estiver disponível, o Gerenciador de Tráfego encaminhará o tráfego para o segundo ponto de extremidade. Se os pontos de extremidade primário e secundário não estiverem disponíveis, o tráfego passará para o terceiro e assim por diante. A disponibilidade do ponto de extremidade é baseada no status configurado (habilitado ou desabilitado) e no monitoramento contínuo do ponto de extremidade.

### <a name="configuring-endpoints"></a>Configurando pontos de extremidade

Com o Azure Resource Manager, você configura a prioridade do ponto de extremidade usando explicitamente a propriedade “priority” para cada ponto de extremidade. Essa propriedade é um valor entre 1 e 1000. Valores mais baixos representam uma prioridade mais alta. Pontos de extremidade não podem compartilhar os valores de prioridade. A configuração da propriedade é opcional. Quando é omitida, uma prioridade padrão baseada na ordem do ponto de extremidade é usada.

## <a name = "weighted"></a>Método de roteamento de tráfego ponderado
O método de roteamento de tráfego “Ponderado” permite que você distribua o tráfego de maneira uniforme ou use uma ponderação predefinida.

![Método de roteamento de tráfego “Ponderado” do Gerenciador de Tráfego do Azure](media/traffic-manager-routing-methods/weighted.png)

No método de roteamento de tráfego Ponderado, você atribui um peso a cada ponto de extremidade na configuração do perfil do Gerenciador de Tráfego. Cada peso é um inteiro de 1 a 1000. Esse parâmetro é opcional. Se ele for omitido, os Gerenciadores de Tráfego usarão um peso padrão de “1”. Quanto maior o peso, maior a prioridade.

Para cada consulta DNS recebida, o Gerenciador de Tráfego escolhe aleatoriamente um ponto de extremidade disponível. A probabilidade de escolher um ponto de extremidade baseia-se nos pesos atribuídos a todos os pontos de extremidade disponíveis. Usar o mesmo peso em todos os pontos de extremidade resulta em uma distribuição uniforme do tráfego. Usar pesos maiores ou menores em pontos de extremidade específicos faz com que esses pontos de extremidade sejam retornados com maior ou menor frequência nas respostas DNS.

O método ponderado permite alguns cenários úteis:

* Atualização gradual do aplicativo: Aloque um percentual do tráfego para roteá-lo para um novo ponto de extremidade e aumentar gradualmente o tráfego até que ele chegue a 100%.
* Migração de aplicativos para o Azure: Crie um perfil com pontos de extremidade externos e do Azure. Ajuste o peso dos pontos de extremidade para dar preferência aos novos pontos de extremidade.
* Estouro de nuvem para capacidade adicional: Expanda rapidamente uma implantação local na nuvem colocando-a atrás de um perfil do Gerenciador de Tráfego. Quando precisar de capacidade extra na nuvem, você poderá adicionar ou habilitar mais pontos de extremidade e especificar qual parte do tráfego vai para cada ponto de extremidade.

Além de usar o portal do Azure, você pode configurar pesos usando o Azure PowerShell, a CLI e as APIs REST.

É importante entender que as respostas DNS são armazenadas em cache por clientes e pelos servidores DNS recursivo que os clientes usam para resolver nomes DNS. Esse cache pode ter um impacto nas distribuições de tráfego ponderado. Quando o número de clientes e de servidores DNS recursivo é grande, a distribuição de tráfego funciona conforme esperado. No entanto, quando o número de clientes ou de servidores DNS recursivo é pequeno, o cache pode distorcer consideravelmente a distribuição de tráfego.

Os casos de uso comuns incluem:

* Ambientes de desenvolvimento e teste
* Comunicações de aplicativo para aplicativo
* Aplicativos voltados para uma base de usuários estreita que compartilha uma infraestrutura comum de DNS recursivo (por exemplo, funcionários de uma empresa que se conectam por meio de um proxy)

Esses efeitos do caching de DNS são comuns a todos os sistemas de roteamento de tráfego baseados em DNS, e não específicos ao Gerenciador de Tráfego do Azure. Em alguns casos, limpar explicitamente o cache DNS pode fornecer uma solução alternativa. Em outros casos, um método alternativo de roteamento de tráfego pode ser mais apropriado.

## <a name = "performance"></a>Método de roteamento de tráfego por desempenho

A implantação de pontos de extremidade em duas ou mais localizações do mundo pode melhorar a capacidade de resposta de vários aplicativos, encaminhando o tráfego para a localização “mais próxima” a você. O método de roteamento de tráfego por “Desempenho” fornece essa funcionalidade.

![Método de roteamento de tráfego por “Desempenho” do Gerenciador de Tráfego do Azure](media/traffic-manager-routing-methods/performance.png)

O ponto de extremidade “mais próximo” não é necessariamente o mais próximo em termos de distância geográfica. Em vez disso, o método de roteamento de tráfego por “Desempenho” determina o ponto de extremidade mais próximo medindo a latência de rede. O Gerenciador de Tráfego mantém uma Tabela de Latência da Internet para controlar o tempo da viagem de ida e volta entre intervalos de endereços IP e em cada data center do Azure.

O Gerenciador de Tráfego pesquisa o endereço IP de origem da solicitação DNS de entrada na Tabela de Latência da Internet. O Gerenciador de Tráfego escolhe um ponto de extremidade disponível no datacenter do Azure que tem a latência mais baixa para esse intervalo de endereços IP e retorna o ponto de extremidade na resposta DNS.

Como explicamos em [Como funciona o Gerenciador de Tráfego](traffic-manager-how-it-works.md), o Gerenciador de Tráfego não recebe consultas DNS diretamente de clientes. Em vez disso, as consultas DNS são recebidas do serviço DNS recursivo que os clientes são configurados para usar. Portanto, o endereço IP usado para determinar o ponto de extremidade “mais próximo” não é o endereço IP do cliente, mas o endereço IP do serviço DNS recursivo. Na prática, esse endereço IP é um bom proxy para o cliente.


Regularmente, o Gerenciador de Tráfego atualiza a Tabela de Latência da Internet para verificar se há alterações na Internet global e em novas regiões do Azure. No entanto, o desempenho do aplicativo varia de acordo com as variações em tempo real na carga pela Internet. O roteamento de tráfego por desempenho não monitora a carga em um ponto de extremidade de serviço específico. No entanto, se um ponto de extremidade ficar indisponível, o Gerenciador de Tráfego não incluirá isso nas respostas a consultas DNS.


Pontos a serem observados:

* Se seu perfil contiver vários pontos de extremidade na mesma região do Azure, o Gerenciador de Tráfego distribuirá o tráfego de maneira uniforme pelos pontos de extremidade disponíveis nessa região. Se preferir uma distribuição de tráfego diferente em uma região, use os [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).
* Se todos os pontos de extremidade habilitados na região do Azure mais próximo estiverem degradados, o Gerenciador de Tráfego moverá o tráfego para os pontos de extremidade na próxima região do Azure mais próxima. Se desejar definir uma sequência de failover preferencial, use os [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).
* Ao usar o método de roteamento de tráfego por Desempenho com pontos de extremidade externos ou aninhados, você precisará especificar a localização desses pontos de extremidade. Escolha a região do Azure mais próxima de sua implantação. Essas localizações são os valores com suporte na Tabela de Latência da Internet.
* O algoritmo que escolhe o ponto de extremidade é determinístico. Consultas DNS repetidas do mesmo cliente são direcionadas ao mesmo ponto de extremidade. Normalmente, os clientes usam servidores DNS recursivo diferentes quando estão viajando. O cliente pode ser encaminhado para outro ponto de extremidade. O roteamento também pode ser afetado pelas atualizações à Tabela de Latência da Internet. Portanto, o método de roteamento de tráfego por Desempenho não garante que um cliente sempre seja encaminhado para o mesmo ponto de extremidade.
* Quando a Tabela de Latência da Internet é alterada, você pode observar que alguns clientes foram direcionados para um ponto de extremidade diferente. Essa alteração de roteamento é mais precisa com base nos dados de latência atuais. Essas atualizações são essenciais para manter a precisão do roteamento de tráfego por Desempenho à medida que a Internet evolui continuamente.

## <a name = "geographic"></a>Método de roteamento de tráfego geográfico

Os perfis do Gerenciador de Tráfego podem ser configurados para usar o método de roteamento geográfico de modo que os usuários sejam direcionados para pontos de extremidade específicos (Azure, Externo ou Aninhado) com base no local geográfico em que as respectivas consultas DNS são originadas. Isso permite que os clientes do Gerenciador de Tráfego habilitem cenários em que saber a região geográfica de um usuário e roteá-lo com base nela é importante. Os exemplos incluem conformidade com normas de soberania de dados, localização de conteúdo e experiência do usuário, bem como medição do tráfego de diferentes regiões.
Quando um perfil é configurado para roteamento geográfico, cada ponto de extremidade associado a esse perfil precisa ter um conjunto de regiões geográficas atribuído a ele. Uma região geográfica pode estar nos seguintes níveis de granularidade 
- Mundo – qualquer região
- Agrupamento Regional – por exemplo, África, Oriente Médio, Austrália/Pacífico etc. 
- País/região – por exemplo, Irlanda, Peru, RAE de Hong Kong etc. 
- Estado/Província – por exemplo, EUA-Califórnia, Austrália-Queensland, Canadá-Alberta etc. (observação: esse nível de granularidade conta com suporte apenas nos estados/províncias da Austrália, do Canadá e dos EUA).

Quando uma região ou um conjunto de regiões é atribuído a um ponto de extremidade, todas as solicitações dessas regiões são roteadas apenas para esse ponto de extremidade. O Gerenciador de Tráfego usa o endereço IP de origem da consulta DNS para determinar a região da qual um usuário está consultando. Em geral, será o endereço IP do resolvedor DNS local que está fazendo a consulta em nome do usuário.  

![Método de roteamento de tráfego "Geográfico" do Gerenciador de Tráfego do Azure](./media/traffic-manager-routing-methods/geographic.png)

O Gerenciador de Tráfego lê o endereço IP de origem da consulta DNS e decide de qual região geográfica ela se origina. Ele então verifica se há um ponto de extremidade com essa região geográfica mapeada para ele. Essa pesquisa começa no nível mais baixo de granularidade (Estado/Província em que tem suporte, do contrário, no nível de País/Região) e percorre todo o caminho até o nível mais alto, que é **Mundo**. A primeira correspondência encontrada que usa essa passagem será designada como o ponto de extremidade para retorno na resposta da consulta. Quando houver correspondência com um ponto de extremidade do tipo Aninhado, um ponto de extremidade nesse perfil filho será retornado, com base em seu método de roteamento. Os seguintes pontos se aplicam a esse comportamento:

- Uma região geográfica poderá ser mapeada apenas para um ponto de extremidade em um perfil do Gerenciador de Tráfego quando o tipo de roteamento for Roteamento Geográfico. Isso garante que o roteamento dos usuários seja determinístico e que os clientes possam habilitar cenários que exijam limites geográficos inequívocos.
- Se a região de um usuário estiver sob mapeamento geográficos de dois pontos de extremidade diferentes, o Gerenciador de Tráfego selecionará o ponto de extremidade com a granularidade mais baixa e não considerará as solicitações de roteamento dessa região para o outro ponto de extremidade. Por exemplo, considere um perfil de tipo de Roteamento Geográfico com dois pontos de extremidade: Ponto de Extremidade 1 e Ponto de Extremidade 2. O Ponto de Extremidade 1 está configurado para receber tráfego da Irlanda e o Ponto de Extremidade 2 está configurado para receber tráfego da Europa. Se uma solicitação for originada da Irlanda, ela sempre será roteada para o Ponto de Extremidade 1.
- Uma que uma região pode ser mapeada apenas para um ponto de extremidade, o Gerenciador de Tráfego a retornará, independentemente de o ponto de extremidade estar íntegro ou não.

    >[!IMPORTANT]
    >É enfaticamente recomendado que os clientes que usem o método de roteamento geográfico o associem aos pontos de extremidade do tipo Aninhado com perfis filho contendo pelo menos dois pontos de extremidade cada.
- Se for encontrada uma correspondência de ponto de extremidade e esse ponto de extremidade estiver no estado **Parado** o Gerenciador de Tráfego retornará uma resposta NODATA. Nesse caso, nenhuma outra pesquisa será feita acima na hierarquia da região geográfica. Esse comportamento também vale para tipos de ponto de extremidade aninhados quando o perfil filho está no estado **Parado** ou **Desabilitado**.
- Se um ponto de extremidade exibir o status **Desabilitado**, ele não será incluído no processo de correspondência de região. Esse comportamento também vale para tipos de ponto de ponto de extremidade aninhados quando o ponto de extremidade está no estado **Desabilitado**.
- Se uma consulta estiver vindo proveniente de uma região geográfica que não tenha mapeamento no perfil, o Gerenciador de Tráfego retornará uma resposta NODATA. Portanto, é enfaticamente aconselhável que os clientes que usem roteamento geográfico com um ponto de extremidade, idealmente do tipo Aninhado com pelo menos dois pontos de extremidade no perfil filho, com a região **Mundo** atribuída a ele. Isso também garante que os endereços IP que não são mapeados para uma região sejam manipulados.

Como explicamos em [Como funciona o Gerenciador de Tráfego](traffic-manager-how-it-works.md), o Gerenciador de Tráfego não recebe consultas DNS diretamente de clientes. Em vez disso, as consultas DNS são recebidas do serviço DNS recursivo que os clientes são configurados para usar. Portanto, o endereço IP usado para determinar a região não é o endereço IP do cliente, mas o endereço IP do serviço DNS recursivo. Na prática, esse endereço IP é um bom proxy para o cliente.

### <a name="faqs"></a>Perguntas Frequentes

* [Quais são alguns casos de uso em que o roteamento geográfico é útil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Como fazer decidir se devo usar o método de roteamento de desempenho ou o método de roteamento geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Quais são as regiões com suporte do Gerenciador de tráfego para roteamento geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Como o Gerenciador de tráfego determina de onde um usuário está consultando?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [É garantido que o Gerenciador de tráfego possa determinar corretamente a localização geográfica exata do usuário em todos os casos?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Um ponto de extremidade precisa estar fisicamente localizado na mesma região com a qual ele está configurado para roteamento geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Posso atribuir regiões geográficas a pontos de extremidade em um perfil que não está configurado para fazer o roteamento geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Por que estou recebendo um erro quando tento alterar o método de roteamento de um perfil existente para geográfico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Por que é altamente recomendável que os clientes criem perfis aninhados em vez de pontos de extremidade em um perfil com roteamento geográfico habilitado?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Há alguma restrição na versão da API que dá suporte a esse tipo de roteamento?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name = "multivalue"></a>Método de roteamento de tráfego de vários valores
O método de roteamento de tráfego de **vários valores** permite que você obtenha vários pontos de extremidade íntegros em uma única resposta de consulta DNS. Isso permite que o chamador faça novas tentativas do lado do cliente com outros pontos de extremidade no caso de um ponto de extremidade retornado ficar sem dar resposta. Esse padrão pode aumentar a disponibilidade de um serviço e reduzir a latência associada a uma nova consulta DNS para obter um ponto de extremidade íntegro. O método de roteamento MultiValue funcionará apenas se todos os pontos de extremidade forem do tipo 'Externo' e especificados como endereços IPv4 ou IPv6. Quando uma consulta for recebida para este perfil, todos os pontos de extremidade íntegros serão retornados e estarão sujeitos a uma contagem de retorno máxima configurável.

### <a name="faqs"></a>Perguntas Frequentes

* [Quais são alguns casos de uso em que o roteamento de vários valores é útil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Quantos pontos de extremidade são retornados quando o roteamento de vários valores é usado?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Eu receberei o mesmo conjunto de pontos de extremidade quando o roteamento de vários valores for usado?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name = "subnet"></a>Método de roteamento de tráfego de sub-rede
O método de roteamento de tráfego de **sub-rede** permite que você mapeie um conjunto de intervalos de endereços IP do usuário final para pontos de extremidade específicos em um perfil. Depois disso, se o Gerenciador de Tráfego receber uma consulta DNS para esse perfil, ele inspecionará o endereço IP de origem dessa solicitação (na maioria dos casos esse será o endereço IP de saída do resolvedor DNS usado pelo chamador), determinará para qual ponto de extremidade ele está mapeado e retornará esse ponto de extremidade na resposta da consulta. 

O endereço IP a ser mapeado para um ponto de extremidade pode ser especificado como intervalos de CIDR (por exemplo, 1.2.3.0/24) ou como um intervalo de endereços (por exemplo, 1.2.3.4-5.6.7.8). Os intervalos de IP associados a um ponto de extremidade precisam ser exclusivos dentro do perfil e não podem ter nenhuma sobreposição no conjunto de endereços IP de um ponto de extremidade diferente no mesmo perfil.
Se você definir um terminal sem intervalo de endereços, ele funcionará como um fallback e obterá tráfego de qualquer sub-rede restante. Se nenhum ponto de extremidade de fallback for incluído, o Gerenciador de Tráfego enviará uma resposta NODATA para qualquer intervalo indefinido. Portanto, é altamente recomendável que você defina um ponto de extremidade de fallback ou assegure-se de que todos os intervalos de IP possíveis sejam especificados em seus endpoints.

O roteamento de sub-rede pode ser usado para fornecer uma experiência diferente aos usuários que se conectam de um espaço IP específico. Por exemplo, usando o roteamento da sub-rede, um cliente pode fazer com que todas as solicitações de seu escritório corporativo sejam roteadas a um ponto de extremidade diferente no qual eles possam estar testando uma única versão interna do aplicativo. Outro cenário é se você deseja fornecer uma experiência diferente aos usuários conectando-se de um ISP específico (por exemplo, bloquear usuários de um determinado ISP).

### <a name="faqs"></a>Perguntas Frequentes

* [Quais são alguns casos de uso em que o roteamento de sub-rede é útil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Como o Gerenciador de tráfego sabe o endereço IP do usuário final?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Como especificar endereços IP ao usar o roteamento de sub-rede?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Como posso especificar um ponto de extremidade de fallback ao usar o roteamento de sub-rede?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [O que acontece se um ponto de extremidade estiver desabilitado em um perfil de tipo de roteamento de sub-rede?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Próximas etapas

Aprenda a desenvolver aplicativos de alta disponibilidade usando o [monitoramento de ponto de extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md)




