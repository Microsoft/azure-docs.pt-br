---
title: "Monitoramento de ponto de extremidade do Gerenciador de Tráfego do Azure | Microsoft Docs"
description: "Este artigo pode ajudar você a entender como o Gerenciador de Tráfego usa o monitoramento de ponto de extremidade e o failover automático do ponto de extremidade para ajudar clientes do Azure a implantarem aplicativos de alta disponibilidade"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: fff25ac3-d13a-4af9-8916-7c72e3d64bc7
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2017
ms.author: kumud
ms.openlocfilehash: 5ce000814f2f5899a7338fdefb39c4873c006b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitoramento de ponto de extremidade do Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure inclui monitoramento de ponto de extremidade interno e failover automático de ponto de extremidade. Este recurso ajuda você a fornecer aplicativos de alta disponibilidade resilientes a falhas de ponto de extremidade, incluindo falhas de região do Azure.

## <a name="configure-endpoint-monitoring"></a>Configurar o monitoramento de pontos de extremidade

Para configurar o monitoramento de ponto de extremidade, é necessário especificar as configurações a seguir em seu perfil do Gerenciador de Tráfego:

* **Protocolo**. Escolha HTTP, HTTPS ou TCP como o protocolo que o Gerenciador de Tráfego usa ao investigar seu ponto de extremidade para verificar sua integridade. O monitoramento HTTPS não verifica se o seu certificado SSL é válido, apenas verifica se o certificado está presente.
* **Porta**. Escolha a porta usada para a solicitação.
* **Caminho**. Esta definição de configuração é válida somente para os protocolos HTTP e HTTPS, para quais é necessário especificar o caminho de configuração. Fornecer essa configuração para o protocolo de monitoramento TCP resulta em um erro. Para o protocolo TCP, forneça o caminho relativo e o nome da página da Web ou do arquivo que o monitoramento acessa. Uma barra (/) é uma entrada válida para o caminho relativo. Esse valor indica que o arquivo está no diretório raiz (padrão).
* **Intervalo de investigação**. Esse valor especifica a frequência com que a integridade de um ponto de extremidade é verificada usando um agente de investigação do Gerenciador de Tráfego. Você pode especificar dois valores aqui: 30 segundos (investigação normal) e 10 segundos (investigação rápida). Se nenhum valor for fornecido, o perfil será definido como um valor padrão de 30 segundos. Acesse a página [Preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager) para saber mais sobre os preços de investigação rápida.
* **Número de falhas tolerado**. Esse valor especifica quantas falhas um agente de investigação do Gerenciador de Tráfego tolera antes de marcar o ponto de extremidade como não íntegro. Seu valor pode variar entre 0 e 9. Um valor de 0 significa que uma única falha de monitoramento pode fazer esse ponto de extremidade ser marcado como não íntegro. Se nenhum valor for especificado, ele usará o valor padrão de 3.
* **Tempo limite de monitoramento**. Essa propriedade especifica a quantidade de tempo que o agente de investigação do Gerenciador de Tráfego deve esperar antes de considerar uma verificação de falha quando uma investigação de verificação de integridade é enviada ao ponto de extremidade. Se o Intervalo de Investigação for definido como 30 segundos, você poderá definir o valor de Tempo Limite entre 5 e 10 segundos. Se nenhum valor for especificado, ele usará um valor padrão de 10 segundos. Se o Intervalo de Investigação for definido como 10 segundos, você poderá definir o valor de Tempo Limite entre 5 e 9 segundos. Se nenhum valor de Tempo Limite for especificado, ele usará um valor padrão de 9 segundos.

![Monitoramento de ponto de extremidade do Gerenciador de Tráfego](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

**Figura 1: Monitoramento de ponto de extremidade do Gerenciador de Tráfego**

## <a name="how-endpoint-monitoring-works"></a>Como o monitoramento de ponto de extremidade funciona

Se o protocolo de monitoramento for definido como HTTP ou HTTPS, o agente de investigação do Gerenciador de Tráfego fará uma solicitação GET ao ponto de extremidade usando o protocolo, a porta e o caminho relativo fornecidos. Se ele obtiver de volta uma resposta 200-OK, esse ponto de extremidade será considerado íntegro. Se a resposta for um valor diferente ou se nenhuma resposta for recebida dentro do período de tempo limite especificado, o agente de investigação do Gerenciador de Tráfego tentará novamente de acordo com a configuração de Número de Falhas Tolerado (nenhuma nova tentativa será feita se essa configuração for de 0). Se o número de falhas consecutivas for maior do que a configuração Número de Falhas Tolerado, o ponto de extremidade será marcado como não íntegro. 

Se o protocolo de monitoramento for TCP, o agente de sondagem do Gerenciador de Tráfego iniciará uma solicitação de conexão TCP usando a porta especificada. Se o ponto de extremidade responder à solicitação com uma resposta para estabelecer a conexão, essa verificação de integridade será marcada como um êxito e o agente de investigação do Gerenciador de Tráfego redefinirá a conexão TCP. Se a resposta for um valor diferente ou se nenhuma resposta for recebida dentro do período de tempo limite especificado, o agente de investigação do Gerenciador de Tráfego tentará novamente acordo com a configuração de número de falhas tolerado (nenhuma nova tentativa será feita se essa configuração for de 0). Se o número de falhas consecutivas for maior do que a configuração Número de Falhas Tolerado, o ponto de extremidade será marcado como não íntegro.

Em todos os casos, o Gerenciador de Tráfego investiga vários locais e a determinação de falha consecutiva acontece dentro de cada região. Isso também significa que os pontos de extremidade estão recebendo investigações de integridade do Gerenciador de tráfego com uma frequência maior do que a configuração usada para o intervalo de investigação.

>[!NOTE]
>Para um protocolo de monitoramento HTTP ou HTTPS, uma prática comum no lado do ponto de extremidade é implementar uma página personalizada dentro de seu aplicativo, por exemplo, /health.aspx. Usando esse caminho para monitoramento, você pode executar verificações específicas do aplicativo, como verificação de contadores de desempenho ou da disponibilidade do banco de dados. Com base nessas verificações personalizadas, a página retorna um código de status HTTP apropriado.

Todos os pontos de extremidade em um perfil do Gerenciador de Tráfego compartilham configurações de monitoramento. Se precisar usar configurações de monitoramento diferentes para pontos de extremidade diferentes, você pode criar [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Status do ponto de extremidade e do perfil

Você pode habilitar e desabilitar pontos de extremidade e perfis do Gerenciador de Tráfego. No entanto, uma alteração no status do ponto de extremidade também pode ocorrer como resultado das configurações e processos automatizados do Gerenciador de Tráfego.

### <a name="endpoint-status"></a>Status do ponto de extremidade

Você pode habilitar ou desabilitar um ponto de extremidade específico. O serviço subjacente, que pode ainda ser íntegro, não é afetado. A alteração do status do ponto de extremidade controla a disponibilidade do ponto de extremidade no perfil do Gerenciador de Tráfego. Quando o status de um ponto de extremidade for desabilitado, o Gerenciador de Tráfego não verificará sua integridade e o ponto de extremidade não será incluído em uma resposta DNS.

### <a name="profile-status"></a>Status do perfil

Usando a configuração de status de perfil, você pode habilitar ou desabilitar um perfil específico. Embora o status do ponto de extremidade afete um único ponto de extremidade, o status do perfil afeta todo o perfil, incluindo todos os pontos de extremidade. Quando você desabilita um perfil, os pontos de extremidade não são verificados quanto à integridade e nenhum ponto de extremidade é incluído em uma resposta DNS. Um código de resposta [NXDOMAIN](https://tools.ietf.org/html/rfc2308) é retornado para a consulta DNS.

### <a name="endpoint-monitor-status"></a>Status do monitor de ponto de extremidade

O status do monitor do ponto de extremidade é um valor gerado pelo Gerenciador de Tráfego que mostra o status do ponto de extremidade. Você não pode alterar essa configuração manualmente. O status do monitor de ponto de extremidade é uma combinação dos resultados do monitoramento de ponto de extremidade com o status de ponto de extremidade configurado. Os valores possíveis de status do monitor do ponto de extremidade estão na tabela abaixo:

| Status do perfil | Status do ponto de extremidade | Status do monitor de ponto de extremidade | Observações |
| --- | --- | --- | --- |
| Desabilitado |Habilitado |Inativo |O perfil foi desabilitado. Embora o status do ponto de extremidade seja Habilitado, o status do perfil (Desabilitado) tem precedência. Pontos de extremidade em perfis desabilitados não são monitorados. Um código de resposta NXDOMAIN é retornado para a consulta DNS. |
| &lt;qualquer&gt; |Desabilitado |Desabilitado |O ponto de extremidade foi desabilitado. Pontos de extremidade desabilitados não são monitorados. O ponto de extremidade não é incluído em respostas DNS e, portanto, não recebe tráfego. |
| Habilitado |Habilitado |Online |O ponto de extremidade é monitorado e está íntegro. Ele é incluído em respostas DNS e pode receber tráfego. |
| Habilitado |Habilitado |Degradado |As verificações de integridade de monitoramento do ponto de extremidade estão falhando. O ponto de extremidade não é incluído em respostas DNS e não recebe tráfego. <br>Uma exceção a isso é se todos os pontos de extremidade estiverem degradados, caso em que todos eles serão considerados para devolução na resposta da consulta.</br>|
| Habilitado |Habilitado |Verificando ponto de extremidade |O ponto de extremidade é monitorado, mas os resultados da primeira investigação ainda não foram recebidos. CheckingEndpoint é um estado temporário que geralmente ocorre imediatamente depois de adicionar ou habilitar um ponto de extremidade no perfil. Um ponto de extremidade nesse estado é incluído em respostas DNS e pode receber tráfego. |
| Habilitado |Habilitado |Parada |O serviço de nuvem ou aplicativo Web para o qual o ponto de extremidade aponta não está em execução. Verifique as configurações do serviço de nuvem ou aplicativo Web. Isso também poderá ocorrer se o ponto de extremidade for do tipo aninhado e o perfil filho estiver desabilitado ou inativo. <br>Um ponto de extremidade com um status Parado não é monitorado. Ele não é incluído em respostas DNS e não recebe tráfego. Uma exceção a isso é se todos os pontos de extremidade estiverem degradados, caso em que todos eles serão considerados para devolução na resposta da consulta.</br>|

Para obter detalhes sobre como o status do monitor de ponto de extremidade é calculado para pontos de extremidade aninhados, veja [Perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).

### <a name="profile-monitor-status"></a>Status do monitor de perfil

O status do monitor de perfil é uma combinação dos valores do status do perfil configurado e do status do monitor de ponto de extremidade para todos os pontos de extremidade. Os valores possíveis estão descritos na tabela abaixo:

| Status do perfil (conforme configurado) | Status do monitor de ponto de extremidade | Status do monitor de perfil | Observações |
| --- | --- | --- | --- |
| Desabilitado |&lt;qualquer&gt; ou um perfil sem pontos de extremidade definidos. |Desabilitado |O perfil foi desabilitado. |
| Habilitado |O status de pelo menos um ponto de extremidade é Degradado. |Degradado |Revise os valores de status do ponto de extremidade individual para determinar quais pontos de extremidade exigem mais atenção. |
| Habilitado |O status de pelo menos um ponto de extremidade é Online. Nenhum ponto de extremidade tem o status Degradado. |Online |O serviço está aceitando tráfego. Nenhuma ação adicional é necessária. |
| Habilitado |O status de pelo menos um ponto de extremidade é CheckingEndpoint. Nenhum ponto de extremidade tem status Online ou Degradado. |Verificando pontos de extremidade |Esse estado de transição ocorre quando um perfil é criado ou habilitado. A integridade do ponto de extremidade está sendo verificada pela primeira vez. |
| Habilitado |O status de todos os pontos de extremidade no perfil é Desabilitado ou Parado, ou o perfil não tem nenhum ponto de extremidade definido. |Inativo |Nenhum ponto de extremidade está ativo, mas o perfil ainda está habilitado. |

## <a name="endpoint-failover-and-recovery"></a>Failover e recuperação do ponto de extremidade

O Gerenciador de Tráfego verifica periodicamente a integridade de cada ponto de extremidade, incluindo pontos de extremidade não íntegros. O Gerenciador de Tráfego detecta quando um ponto de extremidade se torna íntegro e coloca-o de volta em rotação.

Um ponto de extremidade não está íntegro quando qualquer um dos seguintes eventos ocorrem:
- Se o protocolo de monitoramento for HTTP ou HTTPS:
    - Uma resposta diferente de 200 será recebida (incluindo um código 2xx diferente ou um redirecionamento 301/302).
- Se o protocolo de monitoramento for TCP: 
    - Uma resposta que não seja ACK ou SYN-ACK será recebida em resposta à solicitação de sincronização enviada pelo Gerenciador de Tráfego para tentar estabelecer uma conexão.
- Tempo limite. 
- Qualquer outro problema de conexão que resulte em o ponto de extremidade não estar acessível.

Para obter mais informações sobre como solucionar problemas de verificações com falha, consulte [Solução de problemas de status degradado no Gerenciador de Tráfego do Azure](traffic-manager-troubleshooting-degraded.md). 

A seguinte linha de tempo na Figura 2 é uma descrição detalhada do processo de monitoramento do ponto de extremidade do Gerenciador de Tráfego que tem as seguintes configurações: o protocolo de monitoramento é HTTP, o intervalo de sondagem é de 30 segundos, o número de falhas toleradas é de 3, o valor de tempo limite é de 10 segundos e o TTL do DNS é de 30 segundos.

![Sequência de failover e failback de ponto de extremidade do Gerenciador de Tráfego](./media/traffic-manager-monitoring/timeline.png)

**Figura 2: Failover do ponto de extremidade do Gerenciador de Tráfego e sequência de recuperação**

1. **GET**. Para cada ponto de extremidade, o sistema de monitoramento do Gerenciador de Tráfego executa uma solicitação GET no caminho especificados nas configurações de monitoramento.
2. **200 OK**. O sistema de monitoramento espera o retorno de uma mensagem HTTP 200 OK dentro de 10 segundos. Quando recebe essa resposta, ele reconhece que o serviço está disponível.
3. **30 segundos entre verificações**. A verificação de integridade do ponto de extremidade é repetida a cada 30 segundos.
4. **Serviço indisponível**. O serviço torna-se indisponível. O Gerenciador de Tráfego não saberá disso até a próxima verificação de integridade.
5. **Tenta acessar o caminho de monitoramento**. O sistema de monitoramento executa uma solicitação GET, mas não recebe uma resposta dentro do período de tempo limite de 10 segundos (outra opção é de uma resposta diferente de 200 ser recebida). Posteriormente, ele realiza mais três tentativas em intervalos de 30 segundos. Se uma das tentativas for bem-sucedida, o número de tentativas será redefinido.
6. **Status definido como Degradado**. Após a quarta falha consecutiva, o sistema de monitoramento marcará o status do ponto de extremidade não disponível como Degradado.
7. **Tráfego desviado para outros pontos de extremidade**. Os servidores de nome DNS do Gerenciador de Tráfego serão atualizados e o Gerenciador de Tráfego não retornará o ponto de extremidade em resposta às consultas DNS. Novas conexões serão direcionadas a outros pontos de extremidade disponíveis. No entanto, respostas DNS anteriores que incluem esse ponto de extremidade ainda poderão ser armazenadas em cache por servidores DNS recursivos e clientes DNS. Os clientes continuam a usar o ponto de extremidade até que o cache de DNS expire. Na medida em que o cache de DNS expira, os clientes fazem novas consultas DNS e são direcionados a pontos de extremidade diferentes. A duração do cache é controlada pela configuração de TTL no perfil do Gerenciador de Tráfego, por exemplo, 30 segundos.
8. **Continuação das verificações de integridade**. O Gerenciador de Tráfego continua verificando a integridade do ponto de extremidade enquanto ele está em estado Degradado. O Gerenciador de Tráfego detecta quando o ponto de extremidade volta a ser íntegro.
9. **Serviço volta a ficar online**. O serviço torna-se disponível. O ponto de extremidade mantém o status Degradado no Gerenciador de Tráfego até que o sistema de monitoramento execute sua próxima verificação de integridade.
10. **O tráfego para o serviço é retomado**. O Gerenciador de Tráfego envia uma solicitação GET e recebe uma resposta de status 200 OK. O serviço voltou ao estado íntegro. Os servidores de nome do Gerenciador de Tráfego são atualizados e começam a enviar o nome DNS do serviço nas respostas DNS. O tráfego retornará ao ponto de extremidade à medida que as respostas DNS em cache que retornam outros pontos de extremidade expirarem e suas conexões com outros pontos de extremidade forem encerradas.

    > [!NOTE]
    > Como o Gerenciador de Tráfego funciona no nível do DNS, ele não consegue influenciar as conexões existentes com qualquer ponto de extremidade. Ao direcionar o tráfego entre os pontos de extremidade (alterando as configurações de perfil ou durante um failover ou failback), o Gerenciador de Tráfego direcionará novas conexões aos pontos de extremidade disponíveis. No entanto, outros pontos de extremidade podem continuar recebendo tráfego por meio das conexões existentes, até que essas sessões sejam encerradas. Para permitir que o tráfego saia das conexões existentes, os aplicativos devem limitar a duração da sessão usada com cada ponto de extremidade.

## <a name="traffic-routing-methods"></a>Métodos de roteamento de tráfego

Quando um ponto de extremidade tem um status Degradado, ele não é retornado em resposta a consultas DNS. Em vez disso, um ponto de extremidade alternativo será escolhido e retornado. O método de roteamento de tráfego configurado no perfil determina como o ponto de extremidade alternativo é escolhido.

* **Prioridade**. Os pontos de extremidade formam uma lista priorizada. O primeiro ponto de extremidade disponível na lista sempre retorna. Se o status de um ponto de extremidade for Degradado, o próximo ponto de extremidade disponível será retornado.
* **Ponderado**. Qualquer ponto de extremidade disponível é escolhido aleatoriamente com base em seus pesos atribuídos e nos pesos de outros pontos de extremidade disponíveis.
* **Desempenho**. O ponto de extremidade mais próximo ao usuário final é retornado. Se esse ponto de extremidade não estiver disponível, um ponto de extremidade será escolhido aleatoriamente dentre todos os outros pontos de extremidade disponíveis. Escolher um ponto de extremidade aleatório evita uma falha em cascata que pode ocorrer quando o ponto de extremidade mais próximo fica sobrecarregado. Você pode configurar planos de failover alternativos para o roteamento de tráfego de desempenho usando [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geográfico**. O ponto de extremidade mapeado para atender a localização geográfica com base no IP da solicitação de consulta é retornado. Se esse ponto de extremidade não estiver disponível, não será selecionado outro ponto de extremidade para failover, já que uma localização geográfica pode ser mapeada somente para um ponto de extremidade em um perfil (mais detalhes estão nas [Perguntas frequentes](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Como uma melhor prática, ao usar o roteamento geográfico, é recomendável que os clientes usem perfis de Gerenciador de Tráfego aninhados com mais de um ponto de extremidade como os pontos de extremidade do perfil.

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



## <a name="next-steps"></a>Próximas etapas

Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-how-traffic-manager-works.md)

Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de Tráfego

Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-manage-profiles.md)

[Solucionar problemas de status Degradado](traffic-manager-troubleshooting-degraded.md) em um ponto de extremidade do Gerenciador de Tráfego
