---
title: Montar um volume secreto em Instâncias de Contêiner do Azure
description: Saiba como montar um volume secreto para armazenar informações confidenciais de acesso pelas instâncias de contêiner
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: danlep
ms.openlocfilehash: 2e96ef73c3ff89fd7941fa14a8a1e53e6d4d8593
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325420"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Montar um volume secreto em Instâncias de Contêiner do Azure

Use um volume *secreto* para fornecer informações confidenciais aos contêineres em um grupo de contêineres. O volume *secreto* armazena os segredos em arquivos no volume, permanecendo acessíveis pelos contêineres no grupo de contêineres. Armazenando segredos em um volume *secreto*, é possível evitar adicionar dados confidenciais, como chaves SSH ou credenciais de banco de dados, ao código do aplicativo.

Todos  os volumes secretos são apoiados pelo [tmpfs][tmpfs], um sistema de arquivos com suporte de RAM; seu conteúdo nunca é gravado no armazenamento não volátil.

> [!NOTE]
> Volumes *secretos* são atualmente restritos a contêineres do Linux. Saiba como passar variáveis de ambiente seguras para contêineres do Windows e do Linux em [Definir variáveis de ambiente](container-instances-environment-variables.md). Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças da plataforma atual na [visão geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="mount-secret-volume---azure-cli"></a>Montar um volume secreto - CLI do Azure

Para implantar um contêiner com um ou mais segredos usando o CLI do Azure, inclua os `--secrets` parâmetros e `--secrets-mount-path` no comando [AZ container Create][az-container-create] . Este exemplo monta um volume *secreto* que consiste em dois segredos, "mysecret1" e "mysecret2", em `/mnt/secrets`:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

A saída do seguinte [AZ container exec][az-container-exec] mostra a abertura de um shell no contêiner em execução, listando os arquivos dentro do volume secreto e exibindo seu conteúdo:

```console
$ az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
/usr/src/app # ls -1 /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Montar um volume secreto - YAML

Também é possível implantar grupos de contêineres com a CLI do Azure e um [modelo YAML](container-instances-multi-container-yaml.md). A implantação pelo modelo YAML é o método preferencial ao implantar grupos de contêineres que consistem em vários contêineres.

Na implantação com um modelo YAML, os valores secretos devem ser **codificados em Base64** no modelo. No entanto, os valores secretos aparecem em texto não criptografado dentro dos arquivos no contêiner.

O modelo YAML a seguir define um grupo de contêineres com um contêiner que monta um volume *secreto* em `/mnt/secrets`. O volume secreto tem dois segredos, "mysecret1" e "mysecret2."

```yaml
apiVersion: '2018-10-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Para implantar com o modelo YAML, salve o YAML anterior em um arquivo chamado `deploy-aci.yaml`e execute o comando [AZ container Create][az-container-create] com o `--file` parâmetro:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Montar o volume secreto - Resource Manager

Além da implantação de CLI e YAML, é possível implantar um grupo de contêineres usando um [modelo do Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Primeiro, popule a matriz `volumes` na seção `properties` do grupo de contêineres do modelo. Ao implantar com um modelo YAML, os valores secretos devem ser **codificados em Base64** no modelo. No entanto, os valores secretos aparecem em texto não criptografado dentro dos arquivos no contêiner.

Em seguida, para cada contêiner do grupo de contêineres no qual você deseja montar o volume *secreto*, popule a matriz `volumeMounts` na seção `properties` da definição de contêiner.

O seguinte modelo do Resource Manager define um grupo de contêineres com um contêiner que monta um volume *secreto* em `/mnt/secrets`. O volume secreto tem dois segredos, "mysecret1" e "mysecret2."

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Para implantar com o modelo do Resource Manager, salve o JSON anterior em um arquivo `deploy-aci.json`chamado e execute o comando [AZ Group Deployment Create][az-group-deployment-create] com o `--template-file` parâmetro:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Próximas etapas

### <a name="volumes"></a>Volumes

Saiba como montar outros tipos de volume em Instâncias de Contêiner do Azure:

* [Montar um compartilhamento de arquivos do Azure em Instâncias de Contêiner do Azure](container-instances-volume-azure-files.md)
* [Montar um volume emptyDir em Instâncias de Contêiner do Azure](container-instances-volume-emptydir.md)
* [Montar um volume gitRepo em Instâncias de Contêiner do Azure](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Variáveis de ambiente seguro

Outro método para fornecer informações confidenciais aos contêineres (incluindo contêineres do Windows) é usando [variáveis de ambiente seguro](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
