---
title: Práticas recomendadas para dimensionamento automático
description: Padrões de dimensionamento automático no Azure para Aplicativos Web, conjuntos de Dimensionamento de Máquina Virtual e Serviços de Nuvem
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 3700fb90318da3787830f9b6c202436c0e45e2fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61063372"
---
# <a name="best-practices-for-autoscale"></a>Práticas recomendadas para Dimensionamento Automático
O dimensionamento automático do Azure Monitor aplica-se somente aos [Conjuntos de Dimensionamento de Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), aos [Serviços de Nuvem](https://azure.microsoft.com/services/cloud-services/), ao [Serviço de Aplicativo – Aplicativos Web](https://azure.microsoft.com/services/app-service/web/) e aos [Serviços de Gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="autoscale-concepts"></a>Conceitos de dimensionamento automático
* Um recurso pode ter apenas *uma* configuração de Dimensionamento Automático
* Uma configuração de Dimensionamento Automático pode ter um ou mais perfis e cada perfil pode ter uma ou mais regras de dimensionamento automático.
* Uma configuração de dimensionamento automático dimensiona instâncias horizontalmente, que é *escalar* ao aumentar as instâncias e *reduzir* ao diminuir o número de instâncias.
  Uma configuração de dimensionamento automático tem um valor máximo, mínimo e padrão de instâncias.
* Um trabalho de dimensionamento automático sempre lê a métrica associada para expandir, verificando se ele ultrapassou o limite configurado para escalar ou reduzir horizontalmente. Você pode exibir uma lista de métricas que o dimensionamento automático pode dimensionar em [métricas comuns de dimensionamento automático do Azure Monitor](autoscale-common-metrics.md).
* Todos os limites são calculados em um nível de instância. Por exemplo, "escalar horizontalmente por uma instância quando a média da CPU > 80% quando a contagem de instâncias for 2" significa escalar horizontalmente quando a média da CPU em todas as instâncias for maior que 80%.
* Todas as falhas de dimensionamento automático são registradas no log de atividades. Você pode configurar um [alerta do log de atividades](./../../azure-monitor/platform/activity-log-alerts.md) para que você possa ser notificado por email, SMS, webhook, sempre que houver uma falha de dimensionamento automático.
* Da mesma forma, todas as ações de escala bem-sucedidas são lançadas no Log de atividades. Você pode configurar um alerta de log de atividades para que possa ser notificado por email, SMS, webhook, sempre que houver uma ação de dimensionamento automático bem-sucedida. Você também pode configurar as notificações por e-mail ou webhook para obter notificações de ações de dimensionamento bem-sucedidas através do guia de notificações nas configurações de dimensionamento automático.

## <a name="autoscale-best-practices"></a>Práticas recomendadas de dimensionamento automático
Use as seguintes práticas recomendadas ao usar o dimensionamento automático.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Verifique se os valores mínimo e máximo são diferentes e têm uma margem suficiente entre eles
Se você tiver uma configuração que tenha no máximo = 2, mínimo = 2 e atual contagem de instâncias for 2, nenhuma ação de escala poderá ocorrer. Mantenha uma margem suficiente entre as contagens de instância máxima e mínima, que são inclusivas. O dimensionamento automático sempre dimensiona dentro desses limites.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>O redimensionamento manual é redefinido pelo máximo e pelo mínimo do dimensionamento automático
Se você atualizar manualmente a contagem de instâncias para um valor acima ou abaixo do máximo, o mecanismo de dimensionamento automático dimensionará novamente para o mínimo (se estiver abaixo) ou máximo (se estiver acima) automaticamente. Por exemplo, você pode definir o intervalo entre 3 e 6. Se você tiver uma instância em execução, o mecanismo de dimensionamento automático dimensionará para três instâncias na sua próxima execução. Da mesma forma, se você definir manualmente a escala para oito instâncias, na próxima execução, o dimensionamento automático será dimensionado de volta para seis instâncias na sua próxima execução.  O dimensionamento manual é muito temporário, a menos que você também redefina as regras de dimensionamento automático.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Sempre use uma combinação de regras de escala e redução horizontal que executa um aumento e uma redução
Se você usar apenas uma parte da combinação, dimensionamento automático só terão ação em uma única direção (escala horizontal, ou) até atingir o máximo ou contagens de instâncias mínima de definido no perfil. Isso não é ideal, o ideal é que você deseja que seu recurso escale verticalmente em momentos de alto uso para garantir a disponibilidade. Da mesma forma, em momentos de pouco uso, você deseja que seu recurso reduza verticalmente para economizar custos.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Escolher a estatística apropriada para sua métrica de diagnóstico
Para métricas de diagnóstico, você pode escolher entre *Média*, *Mínimo*, *Máximo* e *Total* como uma métrica para expandir. A estatística mais comum é *Média*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Escolha os limites cuidadosamente para todos os tipos de métrica
Recomendamos escolher cuidadosamente limites diferentes para escalar e reduzir horizontalmente com base em situações práticas.

*Não recomendamos* as configurações de dimensionamento automático como os exemplos abaixo com os valores de limite igual ou muito semelhante para condições de redução e escala:

* Aumentar as instâncias por contagem de 1 quando a Contagem de Threads < = 600
* Reduzir as instâncias por contagem de 1 quando a Contagem de Threads >= 600

Vejamos um exemplo de como o que pode levar a um comportamento que possa parecer confuso. Considere a sequência a seguir.

1. Suponha que há duas instâncias para começar e, em seguida, o número médio de threads por instância aumenta para 625.
2. O dimensionamento automático escala horizontalmente uma terceira instância.
3. Em seguida, suponha que a contagem média de threads entre a instância seja de 575.
4. Antes de reduzir verticalmente, o dimensionamento automático tenta estimar como será o estado final de ele for reduzido horizontalmente. Por exemplo, 575 x 3 (contagem de instância atual) = 1.725 / 2 (número final de instâncias quando reduzido verticalmente) = 862.5 threads. Isso significa que o dimensionamento automático terá que imediatamente escalar horizontalmente novamente mesmo depois de ter sido reduzido horizontalmente, se a contagem média de threads permanecer a mesma ou tiver apenas uma pequena quantidade reduzida. No entanto, se escalado verticalmente novamente, todo o processo se repetiria, resultando em um loop infinito.
5. Para evitar essa situação (conhecida como “flapping”), o dimensionamento automático não reduz verticalmente. Em vez disso, ele ignora e reavalia a condição novamente na próxima vez em que executa trabalho do serviço. Isso poderia confundir muitas pessoas porque o dimensionamento automático não funcionaria quando a contagem média de threads fosse 575.

A estimativa durante uma redução serve para evitar situações de "oscilação", em que as ações de redução e expansão variam continuamente. Lembre-se desse comportamento ao escolher os mesmos limites de redução e expansão.

Recomendamos escolher uma margem suficiente entre os limites de redução e escala horizontal. Por exemplo, considere a seguinte combinação de melhor regra.

* Aumentar as instâncias por contagem de 1 quando CPU% >= 80
* Reduzir as instâncias por contagem de 1 quando CPU% <= 60

Nesse caso  

1. Suponha que há 2 instâncias para começar.
2. Se a porcentagem média de CPU entre instâncias for até 80, o dimensionamento automático escalará horizontalmente, adicionando uma terceira instância.
3. Agora suponha que, ao longo do tempo, o percentual de CPU caia para 60.
4. A regra de redução horizontal do dimensionamento automático estimaria o estado final se fosse necessário fazer a redução horizontal. Por exemplo, 60 x 3 (contagem de instância atual) = 180 / 2 (número final de instâncias quando reduzido verticalmente) = 90 threads. Portanto, o dimensionamento automático não reduz horizontalmente, pois ele teria que escalar horizontalmente novamente imediatamente. Em vez disso, ele ignora a redução vertical.
5. Na próxima verificação do dimensionamento automático, a CPU continuará a cair para 50. Ele estima novamente - instância de 3 x 50 = 150 / 2 instâncias = 75, que está abaixo do limite de escala horizontal de 80, portanto ele reduz horizontalmente para que ele é dimensionado em com êxito para 2 instâncias.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Considerações sobre valores de dimensionamento de limite para métricas especiais
 Para métricas especiais, como a métrica do comprimento da Fila do Barramento de Serviço ou de Armazenamento, o limite é o número médio de mensagens disponíveis por número atual de instâncias. Opte por escolher cuidadosamente o valor de limite para esta métrica.

Vamos ilustrá-lo com um exemplo para que entender o comportamento melhor.

* Aumentar o número de instâncias por contagem de 1 quando a contagem de mensagem de Fila de Armazenamento > = 50
* Reduzir o número de instâncias por contagem 1 de contagem de mensagem de Fila de Armazenamento > = 10

Considere a sequência a seguir:

1. Há duas instâncias de fila de armazenamento.
2. As mensagens continuam chegando e, ao examinar a fila de armazenamento, a contagem total é de 50. Você pode pressupor que o dimensionamento automático deve iniciar uma ação de escala horizontal. No entanto, observe que ainda é 50/2 = 25 mensagens por instância. Portanto, a escala horizontal não ocorrerá. Para a primeira escala horizontal acontecer, a contagem total de mensagens na fila de armazenamento deve ser de 100.
3. Em seguida, suponha que a contagem total de mensagens chegue a 100.
4. Uma terceira instância de fila de armazenamento é adicionada devido a uma ação de escala horizontal.  A ação de escala horizontal a seguir não ocorrerá até que a contagem total de mensagens na fila atinja 150 porque 150/3 = 50.
5. Agora, o número de mensagens na fila é menor. Com três instâncias, a primeira ação de redução horizontal acontece quando o total de mensagens em todas as filas atinge 30, porque 30/3 = 10 mensagens por instância, que é o limite de redução horizontal.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Considerações sobre dimensionamento quando vários perfis são configurados em uma configuração de dimensionamento automático
Em uma configuração de dimensionamento automático, você pode escolher um perfil padrão, que é sempre aplicado sem qualquer dependência de agenda ou hora, ou você pode escolher um perfil recorrente ou um perfil para um período fixo com um intervalo de data e hora.

Quando o serviço de dimensionamento automático o processar, ele sempre verifica na seguinte ordem:

1. Perfil de Data Fixa
2. Perfil recorrente
3. Perfil ("Sempre") Padrão

Se uma condição de perfil for atendida, o dimensionamento automático não verificará a próxima condição de perfil abaixo dele. O dimensionamento automático só processa um perfil por vez. Isso significa que, se você quiser incluir também uma condição de processamento de um perfil de nível inferior, você deverá incluir essas regras bem no perfil atual.

Vamos examinar isso usando um exemplo:

A imagem abaixo mostra uma configuração de dimensionamento automático com um perfil padrão de instâncias mínimas = 2 e máximo de instâncias = 10. Neste exemplo, as regras são configuradas para escalar horizontalmente quando a contagem de mensagens na fila for maior que 10 e reduzir horizontalmente quando a contagem de mensagens na fila for menor que três. Agora, o recurso pode ser dimensionado entre duas e dez instâncias.

Além disso, há um perfil recorrente para segunda-feira. Ele é definido para instâncias mínimas = 3 e instâncias máximas = 10. Isso significa que, na segunda-feira, na primeira vez que o dimensionamento automático verificar essa condição, se contagem de instâncias for de dois, ela será dimensionada para o novo mínimo de três. Enquanto o dimensionamento automático continuar encontrando essa condição de perfil correspondida (segunda-feira), ele só processará as regras de redução/escala horizontal com base na CPU configuradas para esse perfil. Neste momento, ele não verificará o comprimento da fila. No entanto, se você deseja que a condição de comprimento de fila seja verificada, você deve incluir as regras do perfil padrão também em seu perfil de segunda-feira.

Da mesma forma, quando o dimensionamento automático alternar de volta para o perfil padrão, ele primeiro verifica se as condições de mínimas e máxima são atendidas. Se o número de instâncias no momento é 12, ele é dimensionado em 10, o máximo permitido para o perfil padrão.

![configurações de dimensionamento automático](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Considerações sobre dimensionamento quando várias regras são configuradas em um perfil
Há casos em que talvez você precise definir várias regras em um perfil. O seguinte conjunto de regras de dimensionamento automático é usado por serviços quando várias regras são definidas.

Em *escalar horizontalmente*, o dimensionamento automático será executado se nenhuma regra for atendida.
Em *reduzir horizontalmente*, o dimensionamento automático exige que todas as regras sejam atendidas.

Para ilustrar, suponha que você tenha as seguintes quatro regras de dimensionamento automático:

* Se CPU < 30%, reduza horizontalmente em 1
* Se Memória < 50%, reduza horizontalmente em 1
* Se CPU < 75%, escale horizontalmente em 1
* Se Memória < 75, escale horizontalmente em 1

Em seguida, ocorrerá o seguinte:

* Se a CPU for 76% e a Memória 50%, escalaremos horizontalmente.
* Se a CPU for 50% e a Memória 76%, escalaremos horizontalmente.

Por outro lado, se a CPU é 25% e a memória é 51%, dimensionamento automático **não** será reduzido horizontalmente. Para reduzir horizontalmente, a CPU deve ser 29% e memória 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Sempre selecione uma contagem de instância de segurança padrão
A contagem de instâncias padrão é importante porque o dimensionamento automático dimensiona o serviço para essa contagem quando métricas não estão disponíveis. Portanto, selecione uma contagem de instância padrão que seja segura para suas cargas de trabalho.

### <a name="configure-autoscale-notifications"></a>Configurar notificações de dimensionamento automático
O dimensionamento automático registrará no Log de atividades se qualquer das seguintes condições ocorrer:

* O dimensionamento automático emite uma operação de dimensionamento
* O serviço de dimensionamento automático conclui com sucesso uma ação de dimensionamento
* O serviço de dimensionamento automático não pode executar uma ação de dimensionamento.
* As métricas não estão disponíveis para o serviço de dimensionamento automático tomar uma decisão de escala.
* As métricas estão disponíveis (recuperação) novamente para tomar uma decisão de escala.

Você também pode usar um alerta do Log de Atividades para monitorar a integridade do mecanismo de dimensionamento automático. Aqui estão exemplos para [criar um Alerta de Log de Atividades para monitorar todas as operações do mecanismo de dimensionamento automático em sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) ou [criar um Alerta de Log de Atividades para monitorar todas as operações de escalar horizontalmente/reduzir horizontalmente com falha na sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Além de usar os alertas do log de atividades, você também pode configurar as notificações por e-mail ou webhook para obter notificações de ações de dimensionamento bem-sucedidas através do guia de notificações nas configurações de dimensionamento automático.

## <a name="next-steps"></a>Próximas etapas
- [Crie um Alerta de Log de Atividades para monitorar todas as operações de mecanismo de dimensionamento automático em sua assinatura.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Crie um Alerta de Log de Atividades para monitorar todas as operações de escalar horizontalmente/reduzir horizontalmente com falha na sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

