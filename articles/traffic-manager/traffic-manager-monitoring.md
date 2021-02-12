---
title: Monitoramento de ponto de extremidade do Gerenciador de Tráfego do Azure | Microsoft Docs
description: Este artigo pode ajudar você a entender como o Gerenciador de Tráfego usa o monitoramento de ponto de extremidade e o failover automático do ponto de extremidade para ajudar clientes do Azure a implantarem aplicativos de alta disponibilidade
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2021
ms.author: duau
ms.openlocfilehash: 455eeb83ef5a9608c077de24b8d3d4722d26a822
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742702"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitoramento de ponto de extremidade do Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure inclui monitoramento de ponto de extremidade interno e failover automático de ponto de extremidade. Este recurso ajuda você a fornecer aplicativos de alta disponibilidade resilientes a falhas de ponto de extremidade, incluindo falhas de região do Azure.

## <a name="configure-endpoint-monitoring"></a>Configurar o monitoramento de pontos de extremidade

Para configurar o monitoramento de ponto de extremidade, é necessário especificar as configurações a seguir em seu perfil do Gerenciador de Tráfego:

* **Protocolo**. Escolha HTTP, HTTPS ou TCP como o protocolo que o Gerenciador de Tráfego usa ao investigar seu ponto de extremidade para verificar sua integridade. O monitoramento de HTTPS não verifica se o certificado TLS/SSL é válido – ele verifica apenas se o certificado está presente.
* **Porta**. Escolha a porta usada para a solicitação.
* **Path**. Esta definição de configuração é válida somente para os protocolos HTTP e HTTPS, para quais é necessário especificar o caminho de configuração. Fornecer essa configuração para o protocolo de monitoramento TCP resulta em um erro. Para o protocolo HTTP e HTTPS, forneça o caminho relativo e o nome da página da Web ou do arquivo que o monitoramento acessa. Uma barra (/) é uma entrada válida para o caminho relativo. Esse valor indica que o arquivo está no diretório raiz (padrão).
* **Configurações de cabeçalho personalizadas**. Essa definição de configuração ajuda a adicionar cabeçalhos HTTP específicos às verificações de integridade que o Gerenciador de tráfego envia aos pontos de extremidade em um perfil. Os cabeçalhos personalizados podem ser especificados em um nível de perfil para ser aplicável a todos os pontos de extremidade nesse perfil e / ou em um nível de ponto de extremidade aplicável somente a esse ponto de extremidade. Você pode usar cabeçalhos personalizados para verificações de integridade de pontos de extremidade em um ambiente multilocatário. Dessa forma, ele pode ser roteado corretamente para seu destino especificando um cabeçalho de host. Também é possível usar essa configuração adicionando cabeçalhos exclusivos que podem ser usados para identificar solicitações HTTP(S) originadas no Gerenciador de Tráfego e processá-las de forma diferente. Você pode especificar até oito pares de cabeçalho: valor separados por uma vírgula. Por exemplo, "header1: value1, header2: value2". 
* **Intervalos de códigos de status esperados**. Essa configuração permite que você especifique vários intervalos de códigos de êxito no formato 200-299, 301-301. Se esses códigos de status forem recebidos como resposta de um ponto de extremidade quando uma verificação de integridade for feita, o Gerenciador de tráfego marcará esses pontos como íntegros. Você pode especificar um máximo de oito intervalos de código de status. Essa configuração se aplica somente aos protocolos HTTP e HTTPS e a todos os pontos de extremidade. Essa configuração reside no nível de perfil do Gerenciador de Tráfego e, por padrão, o valor de 200 é definido como o código de status com êxito.
* **Intervalo de investigação**. Esse valor especifica a frequência com que a integridade de um ponto de extremidade é verificada usando um agente de investigação do Gerenciador de Tráfego. Você pode especificar dois valores aqui: 30 segundos (investigação normal) e 10 segundos (investigação rápida). Se nenhum valor for fornecido, o perfil será definido como um valor padrão de 30 segundos. Acesse a página [Preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager) para saber mais sobre os preços de investigação rápida.
* **Número de falhas toleradas**. Esse valor especifica quantas falhas um agente de investigação do Gerenciador de Tráfego tolera antes de marcar o ponto de extremidade como não íntegro. Seu valor pode variar entre 0 e 9. Um valor de 0 significa que uma única falha de monitoramento pode fazer esse ponto de extremidade ser marcado como não íntegro. Se nenhum valor for especificado, ele usará o valor padrão de 3.
* **Tempo limite de investigação**. Essa propriedade especifica a quantidade de tempo que o agente de investigação do Traffic Manager deve aguardar antes de considerar uma verificação de investigação de integridade em um ponto de extremidade de uma falha. Se o Intervalo de Investigação for definido como 30 segundos, você poderá definir o valor de Tempo Limite entre 5 e 10 segundos. Se nenhum valor for especificado, ele usará um valor padrão de 10 segundos. Se o Intervalo de Investigação for definido como 10 segundos, você poderá definir o valor de Tempo Limite entre 5 e 9 segundos. Se nenhum valor de Tempo Limite for especificado, ele usará um valor padrão de 9 segundos.

    ![Monitoramento de ponto de extremidade do Gerenciador de Tráfego](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Figura: monitoramento de ponto de extremidade do Gerenciador de Tráfego**

## <a name="how-endpoint-monitoring-works"></a>Como o monitoramento de ponto de extremidade funciona

Quando o protocolo de monitoramento é definido como HTTP ou HTTPS, o agente de investigação do Traffic Manager faz uma solicitação GET ao ponto de extremidade usando o protocolo, a porta e o caminho relativo fornecido. Um ponto de extremidade será considerado Íntegro se o agente de investigação receber uma resposta 200-OK ou qualquer uma das respostas configuradas nos **\* intervalos de código de status esperados**. Se a resposta for um valor diferente ou nenhuma resposta for recebida dentro do período de tempo limite, o agente de investigação do Traffic Manager tentará novamente de acordo com a configuração de número de falhas tolerado. Nenhuma nova tentativa será feita se essa configuração for 0. O ponto de extremidade será marcado como não íntegro se o número de falhas consecutivas for maior do que a configuração de número de falhas toleradas.

Quando o protocolo de monitoramento é TCP, o agente de investigação do Traffic Manager cria uma solicitação de conexão TCP usando a porta especificada. Se o ponto de extremidade responder à solicitação com uma resposta para estabelecer a conexão, essa verificação de integridade será marcada como êxito. O agente de investigação do Traffic Manager redefine a conexão TCP. Nos casos em que a resposta é um valor diferente ou nenhuma resposta é recebida dentro do período de tempo limite, o agente de investigação do Traffic Manager tenta novamente de acordo com a configuração de número de falhas tolerado. Nenhuma nova tentativa será feita se essa configuração for 0. Se o número de falhas consecutivas for maior do que a configuração Número de Falhas Tolerado, o ponto de extremidade será marcado como não íntegro.

Em todos os casos, o Gerenciador de tráfego investiga de vários locais. A falha consecutiva determina o que acontece em cada região. É por isso que os pontos de extremidade estão recebendo investigações de integridade do Gerenciador de tráfego com uma frequência maior do que a configuração usada para o intervalo de investigação.

>[!NOTE]
>Para um protocolo de monitoramento HTTP ou HTTPS, uma prática comum no lado do ponto de extremidade é implementar uma página personalizada dentro de seu aplicativo, por exemplo, /health.aspx. Usando esse caminho para monitoramento, você pode executar verificações específicas do aplicativo, como verificação de contadores de desempenho ou da disponibilidade do banco de dados. Com base nessas verificações personalizadas, a página retorna um código de status HTTP apropriado.

Todos os pontos de extremidade em um perfil do Gerenciador de Tráfego compartilham configurações de monitoramento. Se precisar usar configurações de monitoramento diferentes para pontos de extremidade diferentes, você pode criar [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Status do ponto de extremidade e do perfil

Você pode habilitar e desabilitar pontos de extremidade e perfis do Gerenciador de Tráfego. No entanto, uma alteração no status do ponto de extremidade também pode ocorrer devido às configurações e processos automatizados do Gerenciador de tráfego.

### <a name="endpoint-status"></a>Status do ponto de extremidade

Você pode habilitar ou desabilitar um ponto de extremidade específico. O serviço subjacente, que pode ainda ser íntegro, não é afetado. A alteração do status do ponto de extremidade controla a disponibilidade do ponto de extremidade no perfil do Gerenciador de Tráfego. Quando um status de ponto de extremidade é desabilitado, o Gerenciador de tráfego não verifica sua integridade e o ponto de extremidade não é incluído em uma resposta DNS.

### <a name="profile-status"></a>Status do perfil

Usando a configuração de status de perfil, você pode habilitar ou desabilitar um perfil específico. Embora o status do ponto de extremidade afete um único ponto de extremidade, o status do perfil afeta todo o perfil, incluindo todos os pontos de extremidade. Quando você desabilita um perfil, os pontos de extremidade não são verificados quanto à integridade e nenhum ponto de extremidade é incluído em uma resposta DNS. Um código de resposta [NXDOMAIN](https://tools.ietf.org/html/rfc2308) é retornado para a consulta DNS.

### <a name="endpoint-monitor-status"></a>Status do monitor de ponto de extremidade

O status do monitor do ponto de extremidade é um valor gerado pelo Gerenciador de Tráfego que mostra o status do ponto de extremidade. Você não pode alterar essa configuração manualmente. O status do monitor de ponto de extremidade é uma combinação dos resultados do monitoramento de ponto de extremidade com o status de ponto de extremidade configurado. Os valores possíveis de status do monitor do ponto de extremidade estão na tabela abaixo:

| Status do perfil | Status do ponto de extremidade | Status do monitor de ponto de extremidade | Observações |
| --- | --- | --- | --- |
| Desabilitado |habilitado |Inativo |O perfil foi desabilitado. Embora o status do ponto de extremidade seja Habilitado, o status do perfil (Desabilitado) tem precedência. Os pontos de extremidade em perfis desabilitados não são monitorados. Um código de resposta NXDOMAIN é retornado para a consulta DNS. |
| &lt;qualquer&gt; |Desabilitado |Desabilitado |O ponto de extremidade foi desabilitado. Os pontos de extremidade desabilitados não são monitorados. O ponto de extremidade não está incluído nas respostas DNS, pois isso não recebe tráfego. |
| habilitado |habilitado |Online |O ponto de extremidade é monitorado e está íntegro. Ele está incluído nas respostas DNS e pode receber tráfego. |
| habilitado |habilitado |Degradado |As verificações de integridade de monitoramento do ponto de extremidade estão falhando. O ponto de extremidade não está incluído nas respostas DNS e não recebe tráfego. <br>Uma exceção é se todos os pontos de extremidade estiverem degradados. Nesse caso, todos eles são considerados retornados na resposta da consulta.</br>|
| habilitado |habilitado |Verificando ponto de extremidade |O ponto de extremidade é monitorado, mas os resultados da primeira investigação ainda não foram recebidos. CheckingEndpoint é um estado temporário que geralmente ocorre imediatamente depois de adicionar ou habilitar um ponto de extremidade no perfil. Um ponto de extremidade nesse estado é incluído em respostas DNS e pode receber tráfego. |
| habilitado |habilitado |Parado |O aplicativo Web ao qual o ponto de extremidade aponta não está em execução. Verifique as configurações do aplicativo Web. Esse status também pode ocorrer se o ponto de extremidade for do tipo ponto de extremidade aninhado e o perfil filho for desabilitado ou estiver inativo. <br>Um ponto de extremidade com um status parado não é monitorado. Ele não está incluído nas respostas DNS e não recebe tráfego. Uma exceção é se todos os pontos de extremidade estiverem degradados. Nesse caso, todas elas serão consideradas retornadas na resposta da consulta.</br>|

Para obter detalhes sobre como o status do monitor de ponto de extremidade é calculado para pontos de extremidade aninhados, veja [Perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).

>[!NOTE]
> Um status de monitor de Ponto de Extremidade Interrompido pode ocorrer no Serviço de Aplicativo do Azure, se seu aplicativo web não estiver na execução na camada padrão ou acima. Para obter mais informações, consulte [Integração do Gerenciador de Tráfego com Serviço de Aplicativo](../app-service/web-sites-traffic-manager.md).

### <a name="profile-monitor-status"></a>Status do monitor de perfil

O status do monitor de perfil é uma combinação dos valores do status do perfil configurado e do status do monitor de ponto de extremidade para todos os pontos de extremidade. Os possíveis valores são descritos na tabela seguinte:

| Status do perfil (conforme configurado) | Status do monitor de ponto de extremidade | Status do monitor de perfil | Observações |
| --- | --- | --- | --- |
| Desabilitado |&lt;qualquer&gt; ou um perfil sem pontos de extremidade definidos. |Desabilitado |O perfil foi desabilitado. |
| Habilitada |O status de pelo menos um ponto de extremidade é Degradado. |Degradado |Revise os valores de status do ponto de extremidade individual para determinar quais pontos de extremidade exigem mais atenção. |
| Habilitada |O status de pelo menos um ponto de extremidade é Online. Nenhum ponto de extremidade tem o status Degradado. |Online |O serviço está aceitando tráfego. Nenhuma ação do usuário é necessária. |
| Habilitada |O status de pelo menos um ponto de extremidade é CheckingEndpoint. Nenhum ponto de extremidade tem status Online ou Degradado. |Verificando pontos de extremidade |Esse estado de transição ocorre quando um perfil é criado ou habilitado. A integridade do ponto de extremidade está sendo verificada pela primeira vez. |
| Habilitada |O status de todos os pontos de extremidade no perfil é Desabilitado ou Parado, ou o perfil não tem nenhum ponto de extremidade definido. |Inativo |Nenhum ponto de extremidade está ativo, mas o perfil ainda está habilitado. |

## <a name="endpoint-failover-and-recovery"></a>Failover e recuperação do ponto de extremidade

O Gerenciador de Tráfego verifica periodicamente a integridade de cada ponto de extremidade, incluindo pontos de extremidade não íntegros. O Gerenciador de Tráfego detecta quando um ponto de extremidade se torna íntegro e coloca-o de volta em rotação.

Um ponto de extremidade não está íntegro quando qualquer um dos seguintes eventos ocorrem:

- Se o protocolo de monitoramento for HTTP ou HTTPS:
    - Uma resposta diferente de 200 ou uma resposta que não inclui o intervalo de status especificado na configuração de **intervalos de códigos de status esperados** é recebida. (Incluindo um código 2xx diferente ou um redirecionamento 301/302).
- Se o protocolo de monitoramento for TCP: 
    - Uma resposta diferente de ACK ou SYN-ACK é recebida em resposta à solicitação SYN enviada pelo Gerenciador de tráfego para tentar um estabelecimento de conexão.
- Tempo limite. 
- Qualquer outro problema de conexão que resulte em o ponto de extremidade não estar acessível.

Para obter mais informações sobre como solucionar problemas de verificações com falha, consulte [Solução de problemas de status degradado no Gerenciador de Tráfego do Azure](traffic-manager-troubleshooting-degraded.md). 

A linha do tempo na figura a seguir é uma descrição detalhada do processo de monitoramento do ponto de extremidade do Gerenciador de tráfego que tem as seguintes configurações:

* O protocolo de monitoramento é HTTP.
* O intervalo de investigação é de 30 segundos.
* O número de falhas toleradas é 3.
* O valor de tempo limite é de 10 segundos.
* O TTL do DNS é de 30 segundos.

![Sequência de failover e failback de ponto de extremidade do Gerenciador de Tráfego](./media/traffic-manager-monitoring/timeline.png)

**Figura: failover do ponto de extremidade do Gerenciador de Tráfego e sequência de recuperação**

1. **Obter**. Para cada ponto de extremidade, o sistema de monitoramento do Traffic Manager faz uma solicitação GET no caminho especificado nas configurações de monitoramento.
2. **200 OK ou o intervalo de códigos personalizado especificou configurações de monitoramento de perfil do Gerenciador de tráfego**. O sistema de monitoramento espera um HTTP 200 OK ou um código de status no intervalo especificado nas configurações de monitoramento a serem retornadas em 10 segundos. Quando recebe essa resposta, ele reconhece que o serviço está disponível.
3. **30 segundos entre verificações**. A verificação de integridade do ponto de extremidade é repetida a cada 30 segundos.
4. **Serviço indisponível**. O serviço torna-se indisponível. O Gerenciador de tráfego não saberá até a próxima verificação de integridade.
5. **Tenta acessar o caminho de monitoramento**. O sistema de monitoramento faz uma solicitação GET, mas não recebe uma resposta dentro do período de tempo limite de 10 segundos. Posteriormente, ele realiza mais três tentativas em intervalos de 30 segundos. Se uma das tentativas for bem-sucedida, o número de tentativas será redefinido.
6. **Status definido como Degradado**. Após a quarta falha consecutiva, o sistema de monitoramento marcará o status do ponto de extremidade não disponível como Degradado.
7. **Tráfego desviado para outros pontos de extremidade**. Os servidores de nome DNS do Gerenciador de Tráfego serão atualizados e o Gerenciador de Tráfego não retornará o ponto de extremidade em resposta às consultas DNS. Novas conexões serão direcionadas a outros pontos de extremidade disponíveis. No entanto, respostas DNS anteriores que incluem esse ponto de extremidade ainda poderão ser armazenadas em cache por servidores DNS recursivos e clientes DNS. Os clientes continuam a usar o ponto de extremidade até que o cache de DNS expire. Na medida em que o cache de DNS expira, os clientes fazem novas consultas DNS e são direcionados a pontos de extremidade diferentes. A duração do cache é controlada pela configuração de TTL no perfil do Gerenciador de Tráfego, por exemplo, 30 segundos.
8. **Continuação das verificações de integridade**. O Gerenciador de Tráfego continua verificando a integridade do ponto de extremidade enquanto ele está em estado Degradado. O Gerenciador de Tráfego detecta quando o ponto de extremidade volta a ser íntegro.
9. **Serviço volta a ficar online**. O serviço torna-se disponível. O ponto de extremidade mantém seu status degradado no Gerenciador de tráfego até que o sistema de monitoramento faça sua próxima verificação de integridade.
10. **O tráfego para o serviço é retomado**. O Gerenciador de Tráfego envia uma solicitação GET e recebe uma resposta de status 200 OK. O serviço voltou ao estado íntegro. Os servidores de nome do Gerenciador de Tráfego são atualizados e começam a enviar o nome DNS do serviço nas respostas DNS. O tráfego retorna ao ponto de extremidade, uma vez que as respostas de DNS em cache que retornam outros pontos de extremidades expiram e, como as conexões existentes com outros pontos de extremidade estão terminando.

    > [!NOTE]
    > Como o Gerenciador de Tráfego funciona no nível do DNS, ele não consegue influenciar as conexões existentes com qualquer ponto de extremidade. Ao direcionar o tráfego entre os pontos de extremidade (alterando as configurações de perfil ou durante um failover ou failback), o Gerenciador de Tráfego direcionará novas conexões aos pontos de extremidade disponíveis. No entanto, outros pontos de extremidade podem continuar recebendo tráfego por meio das conexões existentes, até que essas sessões sejam encerradas. Para permitir que o tráfego saia das conexões existentes, os aplicativos devem limitar a duração da sessão usada com cada ponto de extremidade.

## <a name="traffic-routing-methods"></a>Métodos de roteamento de tráfego

Quando um ponto de extremidade tem um status degradado, ele não é mais retornado em resposta a consultas DNS. Em vez disso, um ponto de extremidade alternativo será escolhido e retornado. O método de roteamento de tráfego configurado no perfil determina como o ponto de extremidade alternativo é escolhido.

* **Prioridade**. Os pontos de extremidade formam uma lista priorizada. O primeiro ponto de extremidade disponível na lista sempre retorna. Se o status de um ponto de extremidade for Degradado, o próximo ponto de extremidade disponível será retornado.
* **Ponderado**. Todos os pontos de extremidade disponíveis são escolhidos aleatoriamente com base em seus pesos atribuídos e os pesos dos outros pontos de extremidade disponíveis.
* **Desempenho**. O ponto de extremidade mais próximo ao usuário final é retornado. Se esse ponto de extremidade não estiver disponível, o Gerenciador de Tráfego moverá o tráfego para os pontos de extremidade da região do Azure seguinte mais próxima. Você pode configurar planos de failover alternativos para o roteamento de tráfego de desempenho usando [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geográfico**. O ponto de extremidade mapeado para atender a localização geográfica com base no IP da solicitação de consulta é retornado. Se esse ponto de extremidade não estiver disponível, outro ponto de extremidade não será selecionado para fazer failover no, pois uma localização geográfica pode ser mapeada somente para um ponto de extremidade em um perfil. (Mais detalhes estão nas [perguntas frequentes](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Como uma melhor prática, ao usar o roteamento geográfico, é recomendável que os clientes usem perfis de Gerenciador de Tráfego aninhados com mais de um ponto de extremidade como os pontos de extremidade do perfil.
* **Vários Valores** Vários pontos de extremidade mapeados para endereços IPv4/IPv6 são retornados. Quando uma consulta é recebida para esse perfil, os pontos de extremidade íntegros são retornados com base na **contagem máxima de registros no** valor de resposta que você especificou. O número padrão de respostas são dois pontos de extremidade.
* **Sub-rede** O ponto de extremidade mapeado para um conjunto de intervalos de endereços IP será retornado. Quando uma solicitação é recebida de endereço IP, o ponto de extremidade retornado é aquele mapeado para esse endereço IP. 

Para obter mais informações, consulte [Métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md).

> [!NOTE]
> Uma exceção para o comportamento normal de roteamento de tráfego ocorre quando todos os pontos de extremidade qualificados têm um estado degradado. O Gerenciador de Tráfego faz uma tentativa do tipo “esforço possível” e *responde como se todos os pontos de extremidade com status Degradado na verdade estivessem online*. Esse comportamento é preferível à opção alternativa, que seria não retornar nenhum ponto de extremidade na resposta DNS. Pontos de extremidade com status Parado ou Desabilitado não são monitorados e, portanto, não são considerados qualificados para o tráfego.
>
> Essa condição geralmente é causada por uma configuração incorreta do serviço, como:
>
> * Uma ACL (lista de controle de acesso) bloqueando as verificações de integridade do Gerenciador de Tráfego.
> * Uma configuração incorreta do protocolo ou da porta de monitoramento no perfil do Gerenciador de Tráfego.
>
> A consequência deste comportamento é que quando as verificações de integridade do Gerenciador de Tráfego não estiverem configuradas corretamente, o roteamento de tráfego pode fazer parecer que o Gerenciador de Tráfego *está* funcionando corretamente. No entanto, neste caso, o failover do ponto de extremidade não acontece e isso afeta a disponibilidade geral do aplicativo. É importante verificar se o perfil mostra um status Online em vez de Degradado. Um status Online indica que as verificações de integridade do Gerenciador de Tráfego estão funcionando corretamente.

Para obter mais informações sobre como solucionar problemas de verificações de integridade com falha, consulte [Solução de problemas de status Degradado no Gerenciador de Tráfego do Azure](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>Perguntas Frequentes

* [O Gerenciador de Tráfego é resistente a falhas de região do Azure?](./traffic-manager-faqs.md#is-traffic-manager-resilient-to-azure-region-failures)

* [Como a escolha da localização do grupo de recursos afeta o Gerenciador de Tráfego?](./traffic-manager-faqs.md#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Como determinar a integridade atual de cada ponto de extremidade?](./traffic-manager-faqs.md#how-do-i-determine-the-current-health-of-each-endpoint)

* [Posso monitorar os pontos de extremidade HTTPS?](./traffic-manager-faqs.md#can-i-monitor-https-endpoints)

* [Eu devo usar um endereço IP ou um nome DNS ao adicionar um ponto de extremidade?](./traffic-manager-faqs.md#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Quais tipos de endereços IP eu posso usar ao adicionar um ponto de extremidade?](./traffic-manager-faqs.md#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [É possível usar diferentes tipos de endereçamento de ponto de extremidade em um único perfil?](./traffic-manager-faqs.md#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [O que acontece quando o tipo de registro de uma consulta recebida é diferente do tipo de registro associado ao tipo de endereçamento dos pontos de extremidade?](./traffic-manager-faqs.md#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [É possível usar um perfil com pontos de extremidade endereçados IPv4/IPv6 em um perfil aninhado?](./traffic-manager-faqs.md#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Eu parei um ponto de extremidade do aplicativo Web no meu perfil do Traffic Manager, mas não estou recebendo nenhum tráfego mesmo depois de reiniciá-lo. Como posso corrigir isso?](./traffic-manager-faqs.md#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Posso usar o Gerenciador de tráfego mesmo que meu aplicativo não tenha suporte para HTTP ou HTTPS?](./traffic-manager-faqs.md#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Quais respostas específicas são necessárias do ponto de extremidade ao usar o monitoramento de TCP?](./traffic-manager-faqs.md#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Com qual velocidade o Gerenciador de Tráfego move meus usuários para fora de um ponto de extremidade não íntegro?](./traffic-manager-faqs.md#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Como especificar diferentes configurações de monitoramento para diferentes pontos de extremidade em um perfil?](./traffic-manager-faqs.md#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Como posso atribuir cabeçalhos HTTP às verificações de saúde do Gerenciador de Tráfego aos meus pontos de extremidade?](./traffic-manager-faqs.md#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Qual cabeçalho host as verificações de integridade do ponto de extremidade usam?](./traffic-manager-faqs.md#what-host-header-do-endpoint-health-checks-use)

* [Quais são os endereços IP dos quais as verificações de integridade se originam?](./traffic-manager-faqs.md#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Quantas verificações de integridade no meu ponto de extremidade posso esperar do Gerenciador de Tráfego?](./traffic-manager-faqs.md#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Como posso receber uma notificação se um dos meus pontos de extremidade ficar inativo?](./traffic-manager-faqs.md#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Próximas etapas

Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-how-it-works.md)

Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de Tráfego

Saiba como [criar um perfil do Gerenciador de tráfego](traffic-manager-manage-profiles.md)

[Solucionar problemas de status Degradado](traffic-manager-troubleshooting-degraded.md) em um ponto de extremidade do Gerenciador de Tráfego