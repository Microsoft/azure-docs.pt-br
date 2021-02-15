---
title: Solução de problemas do Azure Blockchain Workbench
description: Como solucionar problemas de um aplicativo de visualização do Azure Blockchain Workbench.
ms.date: 10/14/2019
ms.topic: troubleshooting
ms.reviewer: brendal
ms.openlocfilehash: 20c0f9bdd6f820a73b1ba6660de805268c0d8714
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85212846"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Solução de problemas de visualização do Azure Blockchain Workbench

Um script do PowerShell está disponível para auxiliar na depuração de desenvolvedor ou suporte. O script gera um resumo e coleta logs detalhados para solução de problemas. Os logs coletados incluem:

* Rede de Blockchain, como Ethereum
* Microservices Blockchain Workbench
* Application Insights
* Monitoramento do Azure (logs de Azure Monitor)

Você pode usar as informações para determinar as próximas etapas e determinar a causa raiz de problemas.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Script de solução de problemas

O script de solução de problemas PowerShell está disponível no GitHub. [Baixe um arquivo zip](https://github.com/Azure-Samples/blockchain/archive/master.zip) ou clone o exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Executar o script
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Execute o `collectBlockchainWorkbenchTroubleshooting.ps1` script para coletar logs e criar um arquivo ZIP que contém uma pasta de informações de solução de problemas. Por exemplo:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Este script aceita os seguintes parâmetros:

| Parâmetro  | Descrição | Obrigatório |
|---------|---------|----|
| SubscriptionID | SubscriptionID para criar ou localizar todos os recursos. | Sim |
| ResourceGroupName | Nome do grupo de recursos do Azure onde o Blockchain Workbench foi implantado. | Sim |
| OutputDirectory | Caminho para criar a saída do arquivo ZIP. Se não for especificado, o diretório atual é o padrão. | Não |
| LookbackHours | Número de horas a utilizar ao efetuar pull de telemetria. O valor padrão é de 24 horas. O valor máximo é de 90 horas | Não |
| OmsSubscriptionId | A ID da assinatura na qual os logs do Azure Monitor são implantados. Passe esse parâmetro somente se os logs de Azure Monitor para a rede blockchain forem implantados fora do grupo de recursos do Blockchain Workbench.| Não |
| OmsResourceGroup |O grupo de recursos em que os logs do Azure Monitor são implantados. Passe esse parâmetro somente se os logs de Azure Monitor para a rede blockchain forem implantados fora do grupo de recursos do Blockchain Workbench.| Não |
| OmsWorkspaceName | O nome do espaço de trabalho do Log Analytics. Passe esse parâmetro somente se os logs de Azure Monitor para a rede blockchain forem implantados fora do grupo de recursos do Blockchain Workbench | Não |

## <a name="what-is-collected"></a>O que é coletado?

O arquivo ZIP de saída contém a seguinte estrutura de pasta:

| Pasta ou Arquivo | Descrição  |
|---------|---------|
| \Summary.txt | Resumo do sistema |
| \Metrics\blockchain | Métricas sobre o blockchain |
| \Metrics\Workbench | Métricas sobre o workbench |
| \Details\Blockchain | Logs detalhados sobre o blockchain |
| \Details\Workbench | Logs detalhados sobre o workbench |

O arquivo de resumo oferece um instantâneo da integridade do aplicativo e o estado geral do aplicativo. O resumo fornece as ações recomendadas, destaca os principais erros e os metadados sobre a execução de serviços.

A pasta **Métrica** contém as métricas de vários componentes do sistema ao longo do tempo. Por exemplo, o arquivo de saída `\Details\Workbench\apiMetrics.txt` contém um resumo de códigos de resposta diferentes e tempos de resposta durante o período de coleção. A pasta **Detalhes** contém logs detalhados para solucionar problemas específicos com o Workbench ou a rede de blockchain subjacente. Por exemplo, `\Details\Workbench\Exceptions.csv` contém uma lista das exceções mais recentes que ocorreram no sistema, o que é útil para solucionar erros com contratos inteligentes ou interações com a blockchain. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Guia de solução de problemas do Application Insights do Azure Blockchain Workbench](https://aka.ms/workbenchtroubleshooting)
