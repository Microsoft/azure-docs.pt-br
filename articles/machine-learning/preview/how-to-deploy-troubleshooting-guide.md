---
title: "Guia de solução de problemas de implantação do Azure Machine Learning | Microsoft Docs"
description: "Guia de solução de problemas para implantação e criação de serviço"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/05/2017
ms.openlocfilehash: 066a6a223692055c7855abc63667e345ee8dc2d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="model-management-troubleshooting"></a>Solução de problemas de gerenciamento de modelos
As informações a seguir podem ajudar a determinar a causa de erros durante a implantação ou ao chamar o serviço Web.
 
## <a name="1-service-logs"></a>1. Logs de serviço
A opção `logs` da CLI de serviço fornece dados de log do Docker e Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Para configurações de log adicionais, use a opção `--help` (ou `-h`).

```
az ml service logs realtime -h
```

## <a name="2-debug-and-verbose-options"></a>2. Opções de depuração e detalhadas
Use o sinalizador `--debug` para mostrar logs de depuração à medida que o serviço estiver sendo implantado.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Use o sinalizador `--verbose` para ver detalhes adicionais à medida que o serviço estiver sendo implantado.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

## <a name="3-app-insights"></a>3. Insights de aplicativo
Defina o sinalizador `-l` como verdadeiro ao criar um serviço Web para habilitar o log de nível de solicitação. Os logs de solicitação são gravados na instância do App Insights para o seu ambiente no Azure. Pesquise por esta instância com o nome do ambiente usado ao usar o comando `az ml env setup`.

- Defina `-l` como verdadeiro ao criar o serviço.
- Abra o App Insights no portal do Azure. Use seu nome de ambiente para localizar a instância do App Insights.
- já no App Insights, clique em Pesquisar no menu superior para exibir os resultados.
- Ou vá para `Analytics` > `Exceptions` > `exceptions take | 10`.


## <a name="4-error-handling-in-script"></a>4. Tratamento de erro no script
No script do `scoring.py`, use a manipulação de exceção na função **executar** para retornar a mensagem de erro como parte de sua saída de serviço Web.

Exemplo de Python:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="5-other-common-problems"></a>5. Outros problemas comuns
- Se o comando `env setup` falhar, verifique se você tem núcleos suficientes disponíveis na sua assinatura.
- Não use o caractere de sublinhado (_) no nome do serviço Web (como em *my_webservice*).
- Repita se receber um erro **502 Gateway incorreto** ao chamar o serviço Web. Normalmente, isso significa que o contêiner não foi implantado para o cluster ainda.
- Se você receber o erro **CrashLoopBackOff** ao criar um serviço, verifique os logs. Esse normalmente é o resultado de dependências ausentes na função **iniciar**.