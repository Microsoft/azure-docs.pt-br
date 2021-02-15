---
title: Executar contêineres de inicialização
description: Execute contêineres de inicialização em instâncias de contêiner do Azure para executar tarefas de instalação em um grupo de contêineres antes da execução dos contêineres de aplicativo.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 5a729263ee632eb9227694ec8684eb6889c6324b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85954274"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Executar um contêiner de inicialização para tarefas de instalação em um grupo de contêineres

As instâncias de contêiner do Azure dão suporte a *contêineres init* em um grupo de contêineres. Os contêineres init são executados até a conclusão antes do início do contêiner de aplicativos ou contêineres. Semelhante aos [contêineres de inicialização kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/), use um ou mais contêineres init para executar a lógica de inicialização para seus contêineres de aplicativo, como configuração de contas, execução de scripts de instalação ou configuração de bancos de dados.

Este artigo mostra como usar um modelo de Azure Resource Manager para configurar um grupo de contêineres com um contêiner de inicialização.

## <a name="things-to-know"></a>Observações importantes

* **Versão da API** -você precisa de pelo menos a API de instâncias de contêiner do Azure versão 2019-12-01 para implantar contêineres de inicialização. Implante usando uma `initContainers` propriedade em um [arquivo YAML](container-instances-multi-container-yaml.md) ou um [modelo do Resource Manager](container-instances-multi-container-group.md).
* **Ordem de execução** – contêineres de inicialização são executados na ordem especificada no modelo e antes de outros contêineres. Por padrão, você pode especificar um máximo de 59 contêineres de inicialização por grupo de contêineres. Pelo menos um contêiner não init deve estar no grupo.
* **Ambiente de host** – contêineres de inicialização são executados no mesmo hardware que o restante dos contêineres no grupo.
* **Recursos** -você não especifica recursos para contêineres de inicialização. Eles recebem o total de recursos, como CPUs e memória disponível para o grupo de contêineres. Enquanto um contêiner de inicialização é executado, nenhum outro contêiner é executado no grupo.
* **Propriedades com suporte** – os contêineres init podem usar propriedades de grupo, como volumes, segredos e identidades gerenciadas. No entanto, eles não podem usar portas ou um endereço IP se estiverem configurados para o grupo de contêineres. 
* **Política de reinicialização** -cada contêiner de inicialização deve sair com êxito antes do início do próximo contêiner no grupo. Se um contêiner de inicialização não for encerrado com êxito, sua ação de reinicialização dependerá da [política de reinicialização](container-instances-restart-policy.md) configurada para o grupo:

    |Política no grupo  |Política em init  |
    |---------|---------|
    |Sempre     |OnFailure         |
    |OnFailure     |OnFailure         |
    |Nunca     |Nunca         |
* **Encargos** – o grupo de contêineres incorre em encargos da primeira implantação de um contêiner de inicialização.

## <a name="resource-manager-template-example"></a>Exemplo de modelo do Resource Manager

Comece copiando o JSON a seguir em um novo arquivo chamado `azuredeploy.json`. O modelo configura um grupo de contêineres com um contêiner de inicialização e dois contêineres de aplicativo:

* O contêiner *init1* executa a imagem [busybox](https://hub.docker.com/_/busybox) do Hub do Docker. Ele é suspenso por 60 segundos e grava uma cadeia de caracteres de linha de comando em um arquivo em um [volume emptyDir](container-instances-volume-emptydir.md).
* Ambos os contêineres de aplicativo executam a imagem de contêiner da Microsoft `aci-wordcount` :
    * O contêiner *Hamlet* executa o aplicativo WordCount em sua configuração padrão, contando frequências de palavras no *Hamlet*de execução de Shakespeare.
    * O contêiner do aplicativo *Julieta* lê a cadeia de caracteres de linha de comando do volume emptDir para executar o aplicativo WordCount em vez de *Romeu e Julieta*de Shakespeare.

Para obter mais informações e exemplos usando a `aci-wordcount` imagem, consulte [definir variáveis de ambiente em instâncias de contêiner](container-instances-environment-variables.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>Implantar o modelo

Crie um grupo de recursos com o comando [az group create][az-group-create].

```azurecli
az group create --name myResourceGroup --location eastus
```

Implante o modelo com o comando [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

Em um grupo com um contêiner de inicialização, o tempo de implantação é aumentado devido ao tempo necessário para a conclusão do contêiner de inicialização ou dos contêineres.


## <a name="view-container-logs"></a>Exibir logs do contêiner

Para verificar se o contêiner de inicialização foi executado com êxito, exiba a saída de log dos contêineres de aplicativo usando o comando [AZ container logs][az-container-logs] . O argumento `--container-name` especifica o contêiner do qual efetuar pull dos logs. Neste exemplo, faça pull dos logs para os contêineres *Hamlet* e *Julieta* , que mostram a saída de comando diferente:

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

Saída:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

Saída:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Próximas etapas

Os contêineres de inicialização ajudam você a executar tarefas de instalação e inicialização para seus contêineres de aplicativo. Para obter mais informações sobre a execução de contêineres baseados em tarefas, consulte [executar tarefas em contêineres com políticas de reinicialização](container-instances-restart-policy.md).

As instâncias de contêiner do Azure fornecem outras opções para modificar o comportamento dos contêineres de aplicativo. Os exemplos incluem:

* [Definir variáveis de ambiente em instâncias de contêiner](container-instances-environment-variables.md)
* [Definir a linha de comando em uma instância de contêiner para substituir a operação de linha de comando padrão](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-container-logs]: /cli/azure/container#az-container-logs
