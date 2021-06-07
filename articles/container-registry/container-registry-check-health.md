---
title: Verificar a integridade do registro
description: Saiba como executar um comando de diagnóstico rápido para identificar problemas comuns ao usar um registro de contêiner do Azure, incluindo a configuração local do Docker e a conectividade com o registro
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: f27a99818260553cbd7ba26158db0064c145a21f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88245376"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Verificar a integridade de um registro de contêiner do Azure

Ao usar um registro de contêiner do Azure, ocasionalmente você pode encontrar problemas. Por exemplo, talvez você não consiga efetuar pull de uma imagem de contêiner devido a um problema com o Docker em seu ambiente local. Ou, um problema de rede pode impedi-lo de se conectar ao registro. 

Como uma primeira etapa de diagnóstico, execute o comando [AZ ACR check-Health][az-acr-check-health] para obter informações sobre a integridade do ambiente e, opcionalmente, o acesso a um registro de destino. Esse comando está disponível no CLI do Azure versão 2.0.67 ou posterior. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

Para obter diretrizes adicionais de solução de problemas de registro, consulte:
* [Solucionar problemas de logon do registro](container-registry-troubleshoot-login.md)
* [Solucionar problemas de rede com o registro](container-registry-troubleshoot-access.md)
* [Solucionar problemas de desempenho de registro](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>Executar AZ ACR check-Health

Os exemplos a seguir mostram diferentes maneiras de executar o `az acr check-health` comando.

> [!NOTE]
> Se você executar o comando em Azure Cloud Shell, o ambiente local não será verificado. No entanto, você pode verificar o acesso a um registro de destino.

### <a name="check-the-environment-only"></a>Verificar apenas o ambiente

Para verificar o daemon do Docker local, a versão da CLI e a configuração do cliente Helm, execute o comando sem parâmetros adicionais:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Verificar o ambiente e um registro de destino

Para verificar o acesso a um registro, bem como executar verificações de ambiente locais, passe o nome de um registro de destino. Por exemplo:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Relatório de erros

O comando registra as informações na saída padrão. Se um problema for detectado, ele fornecerá um código de erro e uma descrição. Para obter mais informações sobre os códigos e as possíveis soluções, consulte a [referência de erro](container-registry-health-error-reference.md).

Por padrão, o comando é interrompido sempre que encontrar um erro. Você também pode executar o comando para que ele forneça saída para todas as verificações de integridade, mesmo que sejam encontrados erros. Adicione o `--ignore-errors` parâmetro, conforme mostrado nos exemplos a seguir:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Saída de exemplo:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Próximas etapas

Para obter detalhes sobre os códigos de erro retornados pelo comando [AZ ACR check-Health][az-acr-check-health] , consulte a [referência de erro de verificação de integridade](container-registry-health-error-reference.md).

Consulte as [perguntas frequentes](container-registry-faq.md) para perguntas frequentes e outros problemas conhecidos sobre o registro de contêiner do Azure.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
