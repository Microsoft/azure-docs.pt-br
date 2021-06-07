---
title: CLI do Azure Service Fabric-sfctl SA-cluster
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para gerenciar clusters autônomos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 7e9f4b55945afc04e5c826b26632d68cb75e502f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260278"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Gerencie clusters autônomos do Service Fabric.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| config | Obtenha a configuração de cluster autônomo do Service Fabric. |
| config-upgrade | Comece a atualizar a configuração de um cluster autônomo do Service Fabric. |
| upgrade-status | Obtenha o status de atualização de configuração do cluster de um cluster autônomo do Service Fabric. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
Obtenha a configuração de cluster autônomo do Service Fabric.

A configuração do cluster contém propriedades do cluster que incluem diferentes tipos de nó no cluster, as configurações de segurança, falhas e topologias de domínio de atualização etc.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --configuration-api-version [Obrigatório] | A versão da API da configuração do json do cluster autônomo. |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
Comece a atualizar a configuração de um cluster autônomo do Service Fabric.

Valide os parâmetros de upgrade da configuração fornecidos e comece a atualizar a configuração do cluster do se os parâmetros forem válidos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --cluster-config            [Obrigatório] | A configuração do cluster. |
| --application-health-policies | O JSON codificava o dicionário de pares de nome de tipo de aplicativo e percentual máximo não íntegro antes de gerar o erro. |
| --delta-unhealthy-nodes | O percentual máximo permitido de degradação de integridade delta permitida durante o upgrade. Os valores permitidos são inteiros, de zero a 100. |
| --nova tentativa de verificação da integridade | O período entre as tentativas de realizar verificações de integridade, se o aplicativo ou o cluster não estiver íntegro.  Padrão\: PT0H0M0S. |
| --estabilidade de verificação de integridade | A quantidade de tempo que o aplicativo ou o cluster deve permanecer íntegro antes que a atualização passe para o próximo domínio de atualização.  Padrão\: PT0H0M0S. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| --espera de verificação de integridade | O período de espera após a conclusão de um domínio de atualização, antes de iniciar o processo de verificações de integridade.  Padrão\: PT0H0M0S. |
| --timeout -t | Padrão\: 60. |
| --unhealthy-applications | O percentual máximo permitido de aplicativos não íntegros durante o upgrade. Os valores permitidos são inteiros, de zero a 100. |
| --unhealthy-nodes | O percentual máximo permitido de nós não íntegros durante o upgrade. Os valores permitidos são inteiros, de zero a 100. |
| --upgrade-domain-delta-unhealthy-nodes | O percentual máximo permitido de degradação de integridade delta do domínio de atualização durante o upgrade. Os valores permitidos são inteiros, de zero a 100. |
| -tempo limite ao domínio de upgrade | A quantidade de tempo que cada domínio de atualização deve concluir antes de FailureAction ser executado.  Padrão\: PT0H0M0S. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |
| -tempo limite de upgrade | A quantidade de tempo que a atualização geral deve concluir antes de FailureAction ser executado.  Padrão\: PT0H0M0S. <br><br> Primeiro, é interpretado como uma cadeia de caracteres representando uma duração ISO 8601. Se isso falhar, será interpretado como um número que representa o número total de milissegundos. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

### <a name="examples"></a>Exemplos

Iniciar uma atualização de configuração do cluster
```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
Obtenha o status de atualização de configuração do cluster de um cluster autônomo do Service Fabric.

Obtenha detalhes do status de atualização de configuração do cluster de um cluster autônomo do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |


## <a name="next-steps"></a>Próximas etapas
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](./scripts/sfctl-upgrade-application.md).
