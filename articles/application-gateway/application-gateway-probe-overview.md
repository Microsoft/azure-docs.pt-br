---
title: Visão geral do monitoramento de integridade do Gateway de Aplicativo do Azure
description: O Gateway de Aplicativo do Azure monitora a integridade de todos os recursos em seu pool de back-end e remove automaticamente qualquer recurso do pool que não for considerado íntegro.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: f0e5a153efe26640e54f386600f07c7b3d4711d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89649061"
---
# <a name="application-gateway-health-monitoring-overview"></a>Visão geral do monitoramento de integridade do Gateway de Aplicativo

Por padrão, o Gateway de Aplicativo do Azure monitora a integridade de todos os recursos em seu pool de back-end e remove automaticamente qualquer recurso do pool que não for considerado íntegro. O Gateway de Aplicativo continua monitorando as instâncias não íntegras e as adiciona de volta ao pool de back-end íntegro depois que elas se tornarem disponíveis e responderem a investigações de integridade. Por padrão, o gateway de aplicativo envia as investigações de integridade com a mesma porta que é definida nas configurações de HTTP de back-end. Uma porta de investigação personalizada pode ser configurada usando uma investigação de integridade personalizada.

O Gateway de Aplicativo de endereço IP de origem usado para investigações de integridade depende do pool de back-end:
 
- Se o endereço do servidor no pool de back-end for um ponto de extremidade público, o endereço de origem será o endereço IP público de front-end do gateway de aplicativo.
- Se o endereço do servidor no pool de back-end for um ponto de extremidade privado, o endereço IP de origem será do espaço de endereço IP privado da sub-rede do gateway de aplicativo.

![exemplo de investigação de gateway de aplicativo][1]

Além de usar o monitoramento da investigação de integridade padrão, você também pode personalizar a investigação de integridade para atender às necessidades do seu aplicativo. Neste artigo, serão abordadas as investigações de integridade padrão e personalizadas.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Investigação de integridade padrão

Um Application Gateway configura automaticamente uma investigação de integridade padrão quando você não define nenhuma configuração de investigação personalizada. O comportamento de monitoramento funciona fazendo uma solicitação HTTP GET para os endereços IP ou o FQDN configurado no pool de back-ends. Para investigações padrão se as configurações de http de back-end forem configuradas para HTTPS, a investigação usará HTTPS para testar a integridade dos servidores de back-end.

Por exemplo:  Configure seu Application Gateway para usar os servidores de back-end A, B e C para receber o tráfego de rede HTTP na porta 80. O monitoramento de integridade padrão testa os três servidores a cada 30 segundos para uma resposta HTTP íntegra com um tempo limite de 30 segundos para cada solicitação. Uma resposta de HTTP íntegra tem um [código de status](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 e 399. Nesse caso, a solicitação HTTP GET para a investigação de integridade será parecida com http://127.0.0.1/ .

Se a verificação de investigação padrão falhar para o servidor A, o gateway de aplicativo interromperá o encaminhamento de solicitações para esse servidor. A investigação padrão ainda continua a verificar o servidor A a cada 30 segundos. Quando o servidor A responde com êxito a uma solicitação de uma investigação de integridade padrão, o gateway de aplicativo começa a encaminhar as solicitações para o servidor novamente.

### <a name="default-health-probe-settings"></a>Configurações da investigação de integridade padrão

| Propriedades da investigação | Valor | Descrição |
| --- | --- | --- |
| URL de investigação |\<protocol\>://127.0.0.1:\<port\>/ |O protocolo e a porta são herdados das configurações de HTTP de back-end para as quais a investigação está associada |
| Intervalo |30 |A quantidade de tempo em segundos a esperar antes da próxima investigação de integridade é enviada.|
| Tempo limite |30 |A quantidade de tempo em segundos o gateway de aplicativo aguarda uma resposta de investigação antes da marcação da investigação como não íntegro. Se uma investigação é retornada como íntegra, o back-end correspondente será imediatamente marcado como íntegro.|
| Limite não íntegro |3 |Controla quantas investigações enviar caso ocorra uma falha da investigação de integridade regular. No SKU v1, essas investigações de integridade adicionais são enviadas em sucessão rápida para determinar a integridade do back-end rapidamente e não aguardam o intervalo de investigação. No caso da SKU v2, as investigações de integridade esperam o intervalo. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite de não íntegro. |

A investigação padrão se parece apenas com \<protocol\> : \/ /127.0.0.1: \<port\> para determinar o status de integridade. Se precisar configurar a investigação de integridade para ir para uma URL personalizada ou modificar outras configurações, você deverá usar investigações personalizadas. Para obter mais informações sobre investigações HTTPS, consulte [visão geral da terminação de TLS e TLS de ponta a ponta com o gateway de aplicativo](ssl-overview.md#for-probe-traffic).

### <a name="probe-intervals"></a>Intervalos de investigação

Todas as instâncias do Gateway de Aplicativo investiga o back-end independente um do outro. A mesma configuração de investigação se aplica a cada instância de Gateway de Aplicativo. Por exemplo, se a configuração de investigação é enviar as investigações de integridade a cada 30 segundos e o gateway de aplicativo tem duas instâncias, em seguida, ambas as instâncias de enviam a investigação de integridade a cada 30 segundos.

Também se houver vários ouvintes, cada ouvinte investiga o back-end independente uns dos outros. Por exemplo, se houver dois ouvintes que aponta para o mesmo pool de back-end em duas portas diferentes (configurado por duas configurações de http de back-end), em seguida, cada ouvinte investiga o mesmo back-end independentemente. Nesse caso, há dois testes de cada instância de gateway de aplicativo para os dois ouvintes. Se houver duas instâncias do gateway de aplicativo nesse cenário, a máquina virtual de back-end veria quatro investigações por intervalo de sondagem configurado.

## <a name="custom-health-probe"></a>Investigação de integridade personalizada

As investigações personalizadas permitem que você tenha um controle mais granular sobre o monitoramento de integridade. Ao usar investigações personalizadas, você pode configurar um nome de host personalizado, caminho de URL, intervalo de investigação e quantas respostas com falha aceitar antes de marcar a instância do pool de back-end como não íntegra, etc.

### <a name="custom-health-probe-settings"></a>Configurações da investigação de integridade personalizada

A tabela a seguir fornece definições para as propriedades de uma investigação de integridade personalizada.

| Propriedades da investigação | Descrição |
| --- | --- |
| Nome |O nome da investigação. Esse nome é usado para identificar e consultar a investigação nas configurações de HTTP de back-end. |
| Protocolo |O protocolo usado para enviar a investigação. Isso deve corresponder ao protocolo definido nas configurações de HTTP de back-end ao qual está associado|
| Host |Nome do host com o qual enviar a investigação. No SKU v1, esse valor será usado somente para o cabeçalho de host da solicitação de investigação. No SKU v2, ele será usado como cabeçalho de host, bem como SNI |
| Caminho |O caminho relativo da investigação. Um caminho válido começa com "/" |
| Porta |Se definido, é usado como a porta de destino. Caso contrário, ele usa a mesma porta que as configurações de HTTP às quais ele está associado. Esta propriedade só está disponível na SKU v2
| Intervalo |Intervalo de investigação em segundos. Esse valor é o intervalo de tempo entre duas investigações consecutivas |
| Tempo limite |Tempo limite da investigação em segundos. Se uma resposta válida não for recebida dentro desse período de tempo limite, a investigação será marcada como com falha  |
| Limite não íntegro |Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite não íntegro |

### <a name="probe-matching"></a>Correspondência de investigação

Por padrão, uma resposta HTTP(S) com código de status entre 200 e 399 é considerada íntegra. As investigações de integridade personalizadas também fornecem suporte a dois critérios correspondentes. Critérios de correspondência podem ser utilizados para, opcionalmente, mudar a interpretação padrão do que constitui uma resposta íntegra.

A seguir estão os critérios de correspondência: 

- **Correspondência de código de status de resposta HTTP** - Critério de correspondência de teste para aceitar o código de resposta HTTP ou intervalos de códigos de resposta especificados pelo usuário. Códigos de status de resposta separados por vírgulas individuais ou um intervalo de código de status têm suporte.
- **Correspondência do corpo da resposta HTTP** - Critério de correspondência da análise que examina o corpo da resposta HTTP e corresponde a uma cadeia de caracteres especificada pelo usuário. A correspondência procura apenas a presença de uma cadeia de caracteres especificada pelo usuário no corpo da resposta e não é uma correspondência de expressão regular completa.

Os critérios de correspondência podem ser especificados usando o cmdlet `New-AzApplicationGatewayProbeHealthResponseMatch`.

Por exemplo:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Depois que os critérios de correspondência forem especificados, ele poderá ser anexado à configuração da investigação usando um parâmetro `-Match` no PowerShell.

## <a name="nsg-considerations"></a>Considerações NSG

Você precisa permitir o tráfego de entrada na Internet nas portas TCP 65503-65534 para a SKU do Gateway de Aplicativo v1 e as portas TCP 65200-65535 para a SKU V2 com a sub-rede de destino como **Qualquer** e a origem como a marca de serviço **GatewayManager**. Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure.

Além disso, a conectividade de Internet de saída não pode ser bloqueada, e o tráfego de entrada proveniente da marca **AzureLoadBalancer** deve ser permitido.

Para obter mais informações, confira [Visão geral da configuração do Gateway de Aplicativo](configuration-infrastructure.md#network-security-groups).

## <a name="next-steps"></a>Próximas etapas
Depois de aprender sobre o monitoramento de integridade do Gateway de Aplicativo, você poderá configurar uma [investigação de integridade personalizada](application-gateway-create-probe-portal.md) no portal do Azure ou uma [investigação de integridade personalizada](application-gateway-create-probe-ps.md) usando o PowerShell e o modelo de implantação do Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
