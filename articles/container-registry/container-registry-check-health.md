---
title: Verificar a integridade do registro no registro de contêiner do Azure
description: Saiba como executar um comando de diagnóstico rápido para identificar problemas comuns ao usar um registro de contêiner do Azure, incluindo a configuração local do Docker e a conectividade com o registro
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3511655d220ee85ce6b5744612e5d6fddafbe877
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309727"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Verificar a integridade de um registro de contêiner do Azure

Ao usar um registro de contêiner do Azure, ocasionalmente você pode encontrar problemas. Por exemplo, talvez você não consiga efetuar pull de uma imagem de contêiner devido a um problema com o Docker em seu ambiente local. Ou, um problema de rede pode impedi-lo de se conectar ao registro. 

Como uma primeira etapa de diagnóstico, execute o procedimento [AZ ACR check-Health][az-acr-check-health] command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli].

## <a name="run-az-acr-check-health"></a>Executar AZ ACR check-Health

Os exemplos a seguir mostram diferentes maneiras de executar `az acr check-health` o comando.

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

Exemplo de saída:

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
