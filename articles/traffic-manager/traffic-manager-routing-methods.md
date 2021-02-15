---
title: Gerenciador de Tráfego do Azure – métodos de roteamento de tráfego
description: Este artigo ajuda você a entender os diferentes métodos de roteamento de tráfego usados pelo Gerenciador de tráfego
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: eeebded128f636ecba2e4e0dab1bc2f0632aaa6a
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730965"
---
# <a name="traffic-manager-routing-methods"></a>Métodos de roteamento do Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure dá suporte a seis métodos de roteamento de tráfego para determinar como rotear o tráfego de rede aos vários pontos de extremidade de serviço. O Gerenciador de Tráfego aplica o método de roteamento de tráfego associado a cada perfil para cada consulta DNS recebida. O método de roteamento de tráfego determina qual ponto de extremidade é retornado na resposta DNS.

Os seguintes métodos de roteamento de tráfego estão disponíveis no Gerenciador de Tráfego:

* **[Prioridade](#priority-traffic-routing-method):** selecione roteamento de **prioridade** quando desejar ter um ponto de extremidade de serviço primário para todo o tráfego. Você pode fornecer vários pontos de extremidade de backup caso o primário ou um dos pontos de extremidade de backup não estejam disponíveis.
* **[Ponderado](#weighted):** selecione roteamento **ponderado** quando desejar distribuir o tráfego entre um conjunto de pontos de extremidade com base em seu peso. Defina o peso a ser distribuído uniformemente entre todos os pontos de extremidade.
* **[Desempenho](#performance):** selecione roteamento de **desempenho** quando você tiver pontos de extremidade em diferentes localizações geográficas e desejar que os usuários finais usem o ponto "mais próximo" para a menor latência de rede.
* **[Geográfico](#geographic):** selecione roteamento **geográfico** para direcionar os usuários a pontos de extremidade específicos (Azure, externo ou aninhado) com base em onde suas consultas DNS são originadas geograficamente. Com esse método de roteamento, ele permite que você esteja em conformidade com cenários como mandatos da soberania de dados, localização de conteúdo & experiência do usuário e medição do tráfego de diferentes regiões.
* **[Vários valores](#multivalue):** selecione **MultiValue** para os perfis do Gerenciador de Tráfego que podem ter apenas endereços IPv4/IPv6 como pontos de extremidade. Quando uma consulta for recebida para este perfil, todos os pontos de extremidade íntegros serão retornados.
* **[Sub-rede](#subnet):** selecione o método de roteamento de tráfego de **sub-rede** para mapear conjuntos de intervalos de endereços IP de usuário final para um ponto de extremidade específico. Quando uma solicitação for recebida, o ponto de extremidade retornado será o mapeado para o endereço IP de origem da solicitação. 


Todos os perfis do Gerenciador de tráfego têm monitoramento de integridade e failover automático de pontos de extremidade. Para obter mais informações, consulte [Monitoramento do Ponto de Extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md). Em um perfil do Gerenciador de tráfego, você só pode configurar um método de roteamento de tráfego por vez. Você pode selecionar um método de roteamento de tráfego diferente para o seu perfil a qualquer momento. Suas alterações serão aplicadas em um minuto sem nenhum tempo de inatividade. Você pode combinar os métodos de roteamento de tráfego usando perfis aninhados do Gerenciador de tráfego. Os perfis de aninhamento permitem configurações sofisticadas de roteamento de tráfego que atendam às necessidades de aplicativos maiores e complexos. Para obter mais informações, consulte [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Método de roteamento de tráfego por prioridade

Muitas vezes, uma organização deseja fornecer confiabilidade para seus serviços. Para fazer isso, eles implantam um ou mais serviços de backup caso seu primário fique inativo. O método de roteamento de tráfego por “Prioridade” permite que os clientes do Azure implementem esse padrão de failover com facilidade.

![Método de roteamento de tráfego por “Prioridade” do Gerenciador de Tráfego do Azure](media/traffic-manager-routing-methods/priority.png)

O perfil do Gerenciador de Tráfego contém uma lista priorizada de pontos de extremidade de serviço. Por padrão, o Gerenciador de Tráfego envia todo o tráfego para o ponto de extremidade primário (prioridade mais alta). Se o ponto de extremidade primário não estiver disponível, o Gerenciador de Tráfego roteará o tráfego para o segundo ponto de extremidade. Em uma situação em que os pontos de extremidade primários e secundários não estão disponíveis, o tráfego passa para o terceiro e assim por diante. A disponibilidade do ponto de extremidade é baseada no status configurado (habilitado ou desabilitado) e no monitoramento contínuo do ponto de extremidade.

### <a name="configuring-endpoints"></a>Configurando pontos de extremidade

Com o Azure Resource Manager, você configura a prioridade do ponto de extremidade usando explicitamente a propriedade “priority” para cada ponto de extremidade. Essa propriedade é um valor entre 1 e 1000. Um valor mais baixo representa uma prioridade mais alta. Os pontos de extremidade não podem compartilhar valores de prioridade. A configuração da propriedade é opcional. Quando é omitida, uma prioridade padrão baseada na ordem do ponto de extremidade é usada.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Método de roteamento de tráfego ponderado
O método de roteamento de tráfego “Ponderado” permite que você distribua o tráfego de maneira uniforme ou use uma ponderação predefinida.

![Método de roteamento de tráfego “Ponderado” do Gerenciador de Tráfego do Azure](media/traffic-manager-routing-methods/weighted.png)

No método de roteamento de tráfego Ponderado, você atribui um peso a cada ponto de extremidade na configuração do perfil do Gerenciador de Tráfego. Cada peso é um inteiro de 1 a 1000. Esse parâmetro é opcional. Se ele for omitido, os Gerenciadores de Tráfego usarão um peso padrão de “1”. Quanto maior o peso, maior a prioridade.

Para cada consulta DNS recebida, o Gerenciador de Tráfego escolhe aleatoriamente um ponto de extremidade disponível. A probabilidade de escolher um ponto de extremidade baseia-se nos pesos atribuídos a todos os pontos de extremidade disponíveis. Usar o mesmo peso em todos os pontos de extremidade resulta em uma distribuição uniforme do tráfego. Usar pesos maiores ou menores em pontos de extremidade específicos faz com que esses pontos de extremidade sejam retornados com maior ou menor frequência nas respostas DNS.

O método ponderado permite alguns cenários úteis:

* Atualização gradual de aplicativos: dada uma porcentagem de tráfego para rotear para um novo ponto de extremidade e aumente gradualmente o tráfego ao longo do tempo para 100%.
* Migração de aplicativos para o Azure: crie um perfil com pontos de extremidade externos e do Azure. Ajuste o peso dos pontos de extremidade para dar preferência aos novos pontos de extremidade.
* Intermitência de nuvem para obter mais capacidade: expanda rapidamente uma implantação local na nuvem, colocando-a atrás de um perfil do Gerenciador de tráfego. Quando precisar de capacidade extra na nuvem, você poderá adicionar ou habilitar mais pontos de extremidade e especificar qual parte do tráfego vai para cada ponto de extremidade.

Você pode configurar pesos usando as APIs de portal do Azure, Azure PowerShell, CLI ou REST.

Um ponto a ser lembrado é que as respostas DNS são armazenadas em cache pelos clientes. Eles também são armazenados em cache pelos servidores DNS recursivos que os clientes usam para resolver nomes DNS. Esse cache pode ter um efeito em distribuições de tráfego ponderado. Quando o número de clientes e de servidores DNS recursivo é grande, a distribuição de tráfego funciona conforme esperado. No entanto, quando o número de clientes ou de servidores DNS recursivo é pequeno, o cache pode distorcer consideravelmente a distribuição de tráfego.

Os casos de uso comuns incluem:

* Ambientes de desenvolvimento e teste
* Comunicações de aplicativo para aplicativo
* Aplicativos voltados para uma base de usuários estreita que compartilha uma infraestrutura comum de DNS recursivo (por exemplo, funcionários de uma empresa que se conectam por meio de um proxy)

Esses efeitos do caching de DNS são comuns a todos os sistemas de roteamento de tráfego baseados em DNS, e não específicos ao Gerenciador de Tráfego do Azure. Em alguns casos, limpar explicitamente o cache DNS pode fornecer uma solução alternativa. Se isso não funcionar, um método de roteamento de tráfego alternativo poderá ser mais apropriado.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Método de roteamento de tráfego por desempenho

A implantação de pontos de extremidade em dois ou mais locais em todo o mundo pode melhorar a capacidade de resposta de seus aplicativos. Com o método de roteamento de tráfego de "desempenho", você pode rotear o tráfego para o local mais próximo para você.

![Método de roteamento de tráfego por “Desempenho” do Gerenciador de Tráfego do Azure](media/traffic-manager-routing-methods/performance.png)

O ponto de extremidade "mais próximo" não é necessariamente mais próximo que medido por distância geográfica. Em vez disso, o método de roteamento de tráfego por “Desempenho” determina o ponto de extremidade mais próximo medindo a latência de rede. O Gerenciador de Tráfego mantém uma Tabela de Latência da Internet para controlar o tempo da viagem de ida e volta entre intervalos de endereços IP e em cada data center do Azure.

O Gerenciador de Tráfego pesquisa o endereço IP de origem da solicitação DNS de entrada na Tabela de Latência da Internet. O Gerenciador de tráfego escolhe um ponto de extremidade disponível no datacenter do Azure que tem a menor latência para esse intervalo de endereços IP. Em seguida, o Gerenciador de tráfego retorna esse ponto de extremidade na resposta DNS.

Conforme explicado em [como funciona o Gerenciador de tráfego, o](traffic-manager-how-it-works.md)Gerenciador de tráfego não recebe consultas DNS diretamente dos clientes. Em vez disso, as consultas DNS são provenientes do serviço DNS recursivo que os clientes estão configurados para usar. Como tal, o endereço IP usado para determinar o ponto de extremidade "mais próximo" não é o endereço IP do cliente, mas é o endereço IP do serviço DNS recursivo. Esse endereço IP é um bom proxy para o cliente.


Regularmente, o Gerenciador de Tráfego atualiza a Tabela de Latência da Internet para verificar se há alterações na Internet global e em novas regiões do Azure. No entanto, o desempenho do aplicativo varia de acordo com as variações em tempo real na carga pela Internet. O roteamento de tráfego de desempenho não monitora a carga em um determinado ponto de extremidade de serviço. Se um ponto de extremidade ficar indisponível, o Gerenciador de tráfego não o incluirá nas respostas de consulta DNS.


Pontos a serem observados:

* Se seu perfil contiver vários pontos de extremidade na mesma região do Azure, o Gerenciador de Tráfego distribuirá o tráfego de maneira uniforme pelos pontos de extremidade disponíveis nessa região. Se preferir uma distribuição de tráfego diferente em uma região, use os [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).
* Se todos os pontos de extremidade habilitados na região do Azure mais próximo estiverem degradados, o Gerenciador de Tráfego moverá o tráfego para os pontos de extremidade na próxima região do Azure mais próxima. Se desejar definir uma sequência de failover preferencial, use os [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).
* Ao usar o método de roteamento de tráfego por Desempenho com pontos de extremidade externos ou aninhados, você precisará especificar a localização desses pontos de extremidade. Escolha a região do Azure mais próxima de sua implantação. Essas localizações são os valores com suporte na Tabela de Latência da Internet.
* O algoritmo que escolhe o ponto de extremidade é determinístico. Consultas DNS repetidas do mesmo cliente são direcionadas ao mesmo ponto de extremidade. Normalmente, os clientes usam servidores DNS recursivo diferentes quando estão viajando. O cliente pode ser encaminhado para outro ponto de extremidade. O roteamento também pode ser afetado pelas atualizações à Tabela de Latência da Internet. É por isso que o método de roteamento de tráfego de desempenho não garante que um cliente sempre seja roteado para o mesmo ponto de extremidade.
* Quando a Tabela de Latência da Internet é alterada, você pode observar que alguns clientes foram direcionados para um ponto de extremidade diferente. Essa alteração de roteamento é mais precisa com base nos dados de latência atuais. Essas atualizações são essenciais para manter a precisão do roteamento de tráfego por Desempenho à medida que a Internet evolui continuamente.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Método de roteamento de tráfego geográfico

Os perfis do Gerenciador de tráfego podem ser configurados para usar o método de roteamento geográfico para que os usuários sejam direcionados para pontos de extremidade específicos (Azure, externo ou aninhado) com base na localização geográfica da qual sua consulta DNS provém. Com esse método de roteamento, ele permite que você esteja em conformidade com as normas da soberania de dados, a localização de conteúdo & experiência do usuário e a medição do tráfego de diferentes regiões.
Quando um perfil é configurado para roteamento geográfico, cada ponto de extremidade associado a esse perfil precisa ter um conjunto de regiões geográficas atribuído a ele. Uma região geográfica pode estar nos seguintes níveis de granularidade 
- Mundo – qualquer região
- Agrupamento Regional – por exemplo, África, Oriente Médio, Austrália/Pacífico etc. 
- País/região – por exemplo, Irlanda, Peru, RAE de Hong Kong etc. 
- Estado/Província – por exemplo, EUA-Califórnia, Austrália-Queensland, Canadá-Alberta etc. (observação: esse nível de granularidade conta com suporte apenas nos estados/províncias da Austrália, do Canadá e dos EUA).

Quando uma região ou um conjunto de regiões é atribuído a um ponto de extremidade, todas as solicitações dessas regiões são roteadas somente para esse ponto de extremidade. O Gerenciador de tráfego usa o endereço IP de origem da consulta DNS para determinar a região de onde um usuário está consultando. Geralmente encontrado como o endereço IP do resolvedor DNS local que faz a consulta para o usuário.  

![Método de roteamento de tráfego "Geográfico" do Gerenciador de Tráfego do Azure](./media/traffic-manager-routing-methods/geographic.png)

O Gerenciador de tráfego lê o endereço IP de origem da consulta DNS e decide em qual região geográfica ela se origina. Em seguida, ele procura ver se há um ponto de extremidade que tenha essa região geográfica mapeada para ele. Essa pesquisa começa no nível de granularidade mais baixo (estado/província onde há suporte, mais no nível de país/região) e passa até o nível mais alto, que é o **mundo**. A primeira correspondência encontrada usando essa passagem é escolhida como o ponto de extremidade para retornar na resposta da consulta. Quando houver correspondência com um ponto de extremidade do tipo Aninhado, um ponto de extremidade nesse perfil filho será retornado, com base em seu método de roteamento. Os seguintes pontos se aplicam a esse comportamento:

- Uma região geográfica poderá ser mapeada apenas para um ponto de extremidade em um perfil do Gerenciador de Tráfego quando o tipo de roteamento for Roteamento Geográfico. Essa restrição garante que o roteamento de usuários seja determinístico e os clientes possam habilitar cenários que exigem limites geográficos não ambíguos.
- Se a região de um usuário estiver listada em dois mapeamentos geográficos diferentes de pontos de extremidade, o Gerenciador de tráfego selecionará o ponto de extremidade com a granularidade mais baixa. O Gerenciador de tráfego não considerará as solicitações de roteamento dessa região para o outro ponto de extremidade. Por exemplo, considere um perfil de tipo de Roteamento Geográfico com dois pontos de extremidade: Ponto de Extremidade 1 e Ponto de Extremidade 2. O Ponto de Extremidade 1 está configurado para receber tráfego da Irlanda e o Ponto de Extremidade 2 está configurado para receber tráfego da Europa. Se uma solicitação for originada na Irlanda, ela será sempre roteada para Endpoint1.
- Como uma região pode ser mapeada somente para um ponto de extremidade, o Gerenciador de tráfego retorna uma resposta se o ponto de extremidade está íntegro ou não.

    >[!IMPORTANT]
    >É enfaticamente recomendado que os clientes que usem o método de roteamento geográfico o associem aos pontos de extremidade do tipo Aninhado com perfis filho contendo pelo menos dois pontos de extremidade cada.
- Se for encontrada uma correspondência de ponto de extremidade e esse ponto de extremidade estiver no estado **Parado** o Gerenciador de Tráfego retornará uma resposta NODATA. Nesse caso, nenhuma outra pesquisa será feita acima na hierarquia da região geográfica. Esse comportamento também vale para tipos de ponto de extremidade aninhados quando o perfil filho está no estado **Parado** ou **Desabilitado**.
- Se um ponto de extremidade exibir o status **Desabilitado**, ele não será incluído no processo de correspondência de região. Esse comportamento também vale para tipos de ponto de ponto de extremidade aninhados quando o ponto de extremidade está no estado **Desabilitado**.
- Se uma consulta estiver vindo proveniente de uma região geográfica que não tenha mapeamento no perfil, o Gerenciador de Tráfego retornará uma resposta NODATA. É por isso que é altamente recomendável que você use o roteamento geográfico com um ponto de extremidade. O ideal é o tipo aninhado com pelo menos dois pontos de extremidade dentro do perfil filho, com a região **mundo** atribuída a ele. Essa configuração também garante que todos os endereços IP que não são mapeados para uma região sejam manipulados.

Conforme explicado em [como funciona o Gerenciador de tráfego, o](traffic-manager-how-it-works.md)Gerenciador de tráfego não recebe consultas DNS diretamente dos clientes. As consultas DNS são provenientes do serviço DNS recursivo que os clientes estão configurados para usar. É por isso que o endereço IP usado para determinar a região não é o endereço IP do cliente, mas sim o endereço IP do serviço DNS recursivo. Esse endereço IP é um bom proxy para o cliente.

### <a name="faqs"></a>Perguntas Frequentes

* [Quais são alguns casos de uso em que o roteamento geográfico é útil?](./traffic-manager-faqs.md#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Como fazer para decidir se eu devo usar o método de roteamento de desempenho ou método de roteamento geográfico?](./traffic-manager-faqs.md#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Quais são as regiões com suporte do Gerenciador de Tráfego para roteamento geográfico?](./traffic-manager-faqs.md#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Como o Gerenciador de Tráfego determina de que região um usuário está fazendo uma consulta?](./traffic-manager-faqs.md#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Há garantia de que o Gerenciador de Tráfego possa determinar o local geográfico exato do usuário em todos os casos corretamente?](./traffic-manager-faqs.md#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Um ponto de extremidade precisa estar fisicamente localizado na mesma região com a qual ele está configurado para roteamento geográfico?](./traffic-manager-faqs.md#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Posso atribuir regiões geográficas a pontos de extremidade em um perfil que não está configurado para fazer o roteamento geográfico?](./traffic-manager-faqs.md#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Por que está ocorrendo um erro ao tentar alterar o método de roteamento de um perfil existente para Geográfico?](./traffic-manager-faqs.md#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Por que é enfaticamente aconselhável que os clientes criem perfis aninhados em vez de pontos de extremidade em um perfil com o roteamento geográfico habilitado?](./traffic-manager-faqs.md#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Existem restrições quanto à versão de API que oferece suporte a esse tipo de roteamento?](./traffic-manager-faqs.md#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Método de roteamento de tráfego de vários valores
O método de roteamento de tráfego de **vários valores** permite que você obtenha vários pontos de extremidade íntegros em uma única resposta de consulta DNS. Essa configuração permite que o chamador faça repetições do lado do cliente com outros pontos de extremidade, caso um ponto de extremidades retornado não responda. Esse padrão pode aumentar a disponibilidade de um serviço e reduzir a latência associada a uma nova consulta DNS para obter um ponto de extremidade íntegro. O método de roteamento MultiValue funcionará apenas se todos os pontos de extremidade forem do tipo 'Externo' e especificados como endereços IPv4 ou IPv6. Quando uma consulta for recebida para este perfil, todos os pontos de extremidade íntegros serão retornados e estarão sujeitos a uma contagem de retorno máxima configurável.

### <a name="faqs"></a>Perguntas Frequentes

* [Quais são alguns casos de uso em que o roteamento de Múltiplos Valores é útil?](./traffic-manager-faqs.md#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Quantos pontos de extremidade serão retornados quando o roteamento de Múltiplos Valores for usado?](./traffic-manager-faqs.md#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Obterei o mesmo conjunto de pontos de extremidade quando o roteamento de Múltiplos Valores for usado?](./traffic-manager-faqs.md#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Método de roteamento de tráfego de sub-rede
O método de roteamento de tráfego de **sub-rede** permite mapear um conjunto de intervalos de endereços IP de usuário final para pontos de extremidade específicos em um perfil. Se o Gerenciador de tráfego receber uma consulta DNS para esse perfil, ele inspecionará o endereço IP de origem dessa solicitação. Em seguida, ele determinará a qual ponto de extremidade ele está mapeado e retornará esse ponto de extremidade na resposta da consulta. Na maioria dos casos, o endereço IP de origem é o resolvedor de DNS que é usado pelo chamador.

O endereço IP a ser mapeado para um ponto de extremidade pode ser especificado como intervalos CIDR (por exemplo, 1.2.3.0/24) ou como um intervalo de endereços (por exemplo, 1.2.3.4-5.6.7.8). Os intervalos de IP associados a um ponto de extremidade precisam ser exclusivos dentro desse perfil. O intervalo de endereços não pode ter uma sobreposição com o conjunto de endereços IP de um ponto de extremidade diferente no mesmo perfil.
Se você definir um terminal sem intervalo de endereços, ele funcionará como um fallback e obterá tráfego de qualquer sub-rede restante. Se nenhum ponto de extremidade de fallback for incluído, o Gerenciador de Tráfego enviará uma resposta NODATA para qualquer intervalo indefinido. É altamente recomendável que você defina um ponto de extremidade de fallback para garantir que todos os intervalos IP possíveis sejam especificados em seus pontos de extremidades.

O roteamento de sub-rede pode ser usado para fornecer uma experiência diferente aos usuários que se conectam de um espaço IP específico. Por exemplo, você pode fazer com que todas as solicitações de seu escritório corporativo sejam roteadas para um ponto de extremidade diferente. Esse método de roteamento é especialmente útil se você estiver tentando testar uma versão somente interna do seu aplicativo. Outro cenário é se você deseja fornecer uma experiência diferente aos usuários conectando-se de um ISP específico (por exemplo, bloquear usuários de um determinado ISP).

### <a name="faqs"></a>Perguntas Frequentes

* [Quais são alguns casos de uso em que o roteamento de sub-rede é útil?](./traffic-manager-faqs.md#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Como o Gerenciador de Tráfego reconhece o endereço IP do usuário final?](./traffic-manager-faqs.md#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Como posso especificar endereços IP ao usar o roteamento de sub-rede?](./traffic-manager-faqs.md#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Como posso especificar um ponto de extremidade de fallback ao usar o roteamento de sub-rede?](./traffic-manager-faqs.md#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [O que acontece se um ponto de extremidade estiver desabilitado em um perfil de tipo de roteamento de sub-rede?](./traffic-manager-faqs.md#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Próximas etapas

Saiba como desenvolver aplicativos de alta disponibilidade usando o [monitoramento de ponto de extremidade do Gerenciador de tráfego](traffic-manager-monitoring.md)