---
title: Diagnosticar erros de pacote de código comum usando Service Fabric | Microsoft Docs
description: Saiba como solucionar problemas de erros de pacote de código comum com o Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: grzuber
manager: gkhanna
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 320a55e8b14648b1d7e256855582ab31846a63cf
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249212"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Diagnosticar erros de pacote de código comum usando Service Fabric

Este artigo descreve o que significa que um pacote de códigos é encerrado inesperadamente. Ele fornece informações sobre as possíveis causas de códigos de erro comuns, juntamente com as etapas de solução de problemas.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Quando um processo ou contêiner termina inesperadamente?

Quando o Azure Service Fabric recebe uma solicitação para iniciar um pacote de códigos, ele começa a preparar o ambiente no sistema local de acordo com as opções definidas nos manifestos do aplicativo e do serviço. Essas preparações podem incluir a reserva de recursos ou pontos de extremidade de rede, a configuração de regras de firewall ou a configuração de restrições de governança de recursos. 

Depois que o ambiente tiver sido configurado corretamente, Service Fabric tentará abrir o pacote de códigos. Esta etapa será considerada com êxito se o tempo de execução do sistema operacional ou contêiner relatar que o processo ou o contêiner foi ativado com êxito. Se a ativação não for bem-sucedida, você deverá ver uma mensagem de integridade em SFX que se assemelha ao seguinte:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Depois que o pacote de códigos tiver sido ativado com êxito, Service Fabric começará a monitorar seu tempo de vida. Neste ponto, um processo ou contêiner pode ser encerrado a qualquer momento por vários motivos. Por exemplo, ele pode ter falhado em inicializar uma DLL ou o sistema operacional pode ter esgotado o espaço de heap de área de trabalho. Se seu pacote de códigos foi encerrado, você deverá ver a seguinte mensagem de integridade em SFX:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

O código de saída nessa mensagem de integridade é a única pista que o processo ou o contêiner fornece sobre por que ele terminou. Ele pode ser gerado por qualquer nível da pilha. Por exemplo, esse código de saída pode estar relacionado a um erro do sistema operacional ou a um problema do .NET, ou pode ter sido gerado pelo seu código. Use este artigo como um ponto de partida para diagnosticar a origem dos códigos de saída de encerramento e as soluções possíveis. Mas tenha em mente que essas são soluções gerais para cenários comuns e podem não se aplicar ao erro que você está vendo.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Como saber se Service Fabric meu pacote de código foi encerrado?

Service Fabric pode ser responsável por encerrar seu pacote de códigos por vários motivos. Por exemplo, pode decidir posicionar o pacote de códigos em outro nó para fins de balanceamento de carga. Você pode verificar se Service Fabric terminou seu pacote de códigos se vir qualquer um dos códigos de saída na tabela a seguir.

>[!NOTE]
> Se o processo ou o contêiner terminar com um código de saída diferente dos códigos na tabela a seguir, Service Fabric não será responsável por encerrá-lo.

Código de saída | DESCRIÇÃO
--------- | -----------
7147 | Indica que Service Fabric desligam normalmente o processo ou o contêiner enviando-o um sinal CTRL + C.
7148 | Indica que Service Fabric terminou o processo ou o contêiner. Às vezes, esse código de erro indica que o processo ou o contêiner não respondeu oportunamente depois de enviar um sinal CTRL + C e precisava ser encerrado.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Outros códigos de erro comuns e suas possíveis correções

Código de saída | Valor hexadecimal | Descrição breve | Causa raiz | Correção potencial
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Esse erro às vezes significa que a máquina ficou sem espaço de heap de área de trabalho. Essa causa é especialmente provável se você tiver vários processos que pertencem ao seu aplicativo em execução no nó. | Se seu programa não foi criado para responder a sinais CTRL + C, você poderá habilitar a configuração **EnableActivateNoWindow** no manifesto do cluster. A habilitação dessa configuração significa que seu pacote de códigos será executado sem uma janela GUI e não receberá sinais CTRL + C. Essa ação também reduz a quantidade de espaço de heap de área de trabalho que cada processo consome. Se o seu pacote de códigos precisar receber sinais CTRL + C, você poderá aumentar o tamanho do heap de área de trabalho do nó.
3762504530 | 0xe0434352 | N/D | Esse valor representa o código de erro para uma exceção sem tratamento do código gerenciado (ou seja, .NET). | Esse código de saída indica que seu aplicativo gerou uma exceção que permanece sem tratamento e que encerrou o processo. Como a primeira etapa para determinar o que disparou esse erro, depure os logs e os arquivos de despejo do aplicativo.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [diagnosticar outros cenários comuns](service-fabric-diagnostics-common-scenarios.md).
* Obtenha uma visão geral mais detalhada dos logs de Azure Monitor e o que eles oferecem lendo [Azure monitor visão geral](../operations-management-suite/operations-management-suite-overview.md).
* Saiba mais sobre [alertas](../log-analytics/log-analytics-alerts.md) de Azure monitor logs para auxiliar na detecção e no diagnóstico.
* Familiarize-se com os recursos de [pesquisa de logs e consulta](../log-analytics/log-analytics-log-searches.md) oferecidos como parte dos logs de Azure monitor.
