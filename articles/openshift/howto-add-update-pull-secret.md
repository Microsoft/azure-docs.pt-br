---
title: Adicionar ou atualizar seu segredo de pull do Red Hat em um cluster do Azure Red Hat OpenShift 4
description: Adicionar ou atualizar seu segredo de pull do Red Hat nos clusters de ARO 4. x existentes
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 05/21/2020
keywords: segredo de pull, toa, openshift, Red Hat
ms.openlocfilehash: b1a8e45a2eab1b7b4c8a5936049f404d5bd3441c
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071079"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>Adicionar ou atualizar seu segredo de pull do Red Hat em um cluster do Azure Red Hat OpenShift 4

Este guia aborda a adição ou atualização do seu segredo de pull do Red Hat para um cluster do Azure Red Hat OpenShift (ARO) 4. x existente.

Se você estiver criando um cluster pela primeira vez, poderá adicionar seu segredo de pull ao criar o cluster. Para obter mais informações sobre como criar um cluster de toa com um segredo de pull do Red Hat, consulte [criar um cluster do Azure Red Hat OpenShift 4](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional).

## <a name="before-you-begin"></a>Antes de começar

Este guia pressupõe que você tenha um cluster do Azure Red Hat OpenShift 4 existente. Verifique se você tem acesso de administrador ao cluster.

## <a name="prepare-your-pull-secret"></a>Preparar seu segredo de pull
Quando você cria um cluster de toa sem adicionar um segredo de pull do Red Hat, um segredo de pull ainda é criado no cluster automaticamente. No entanto, esse segredo de pull não é totalmente populado.

Esta seção percorre a atualização que extrai o segredo com valores adicionais de seu segredo de pull do Red Hat.

1. Busque o segredo nomeado `pull-secret` no `openshift-config` namespace e salve-o em um arquivo separado executando o seguinte comando: 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    A saída deve ser semelhante à seguinte. (Observe que o valor secreto real foi removido.)

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. Vá para o [portal do Gerenciador de cluster do Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) e selecione **baixar segredo de pull**. O segredo de pull do Red Hat será semelhante ao seguinte. (Observe que os valores reais de segredo foram removidos.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. Edite o arquivo secreto de pull obtido do cluster adicionando as entradas encontradas em seu segredo de pull do Red Hat. 

    > [!IMPORTANT]
    > A inclusão da `cloud.openshift.com` entrada do segredo de pull do Red Hat fará com que o cluster comece a enviar dados de telemetria para o Red Hat. Inclua esta seção somente se desejar enviar dados de telemetria. Caso contrário, deixe a seção a seguir fora.    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > Não remova nem altere você é a `arosvc.azurecr.io` entrada do seu segredo de pull. Esta seção é necessária para que o cluster funcione corretamente.

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    O arquivo final deve ser semelhante ao seguinte. (Observe que os valores reais de segredo foram removidos.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. Verifique se o arquivo é um JSON válido. Há várias maneiras de validar seu JSON. O exemplo a seguir usa JQ:

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > Se um erro estiver no arquivo, ele será exibido como `parse error` .

## <a name="add-your-pull-secret-to-your-cluster"></a>Adicionar seu segredo de pull ao cluster

Execute o comando a seguir para atualizar seu segredo de pull.

> [!NOTE]
> A execução desse comando fará com que os nós do cluster reiniciem um a um conforme eles forem atualizados. 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

Depois que o segredo for definido, você estará pronto para habilitar os operadores do Red Hat Certified.

### <a name="modify-the-configuration-files"></a>Modificar os arquivos de configuração

Modifique os objetos a seguir para habilitar os operadores do Red Hat.

Primeiro, modifique o arquivo de configuração do operador de exemplos. Em seguida, você pode executar o seguinte comando para editar o arquivo de configuração:

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

Altere os `spec.architectures.managementState` `status.architecture.managementState` valores e de `Removed` para `Managed` . 

O trecho de código YAML a seguir mostra apenas as seções relevantes do arquivo YAML editado:

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

Em segundo lugar, execute o seguinte comando para editar o arquivo de configuração do Hub operador:  

```console
oc edit operatorhub cluster -o yaml
```

Altere os `Spec.Sources.Disabled` `Status.Sources.Disabled` valores e de `true` para `false` para todas as fontes que você deseja habilitar.

O trecho de código YAML a seguir mostra apenas as seções relevantes do arquivo YAML editado:

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

Salve o arquivo para aplicar suas edições.

## <a name="validate-that-your-secret-is-working"></a>Validar que seu segredo está funcionando

Depois de adicionar o segredo de pull e modificar os arquivos de configuração corretos, o cluster pode levar vários minutos para ser atualizado. Para verificar se o cluster foi atualizado, execute o seguinte comando para mostrar os operadores certificados e as fontes de operadores Red Hat disponíveis:

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

Se você não vir os operadores certificados e os operadores do Red Hat, aguarde alguns minutos e tente novamente.

Para garantir que seu segredo de pull tenha sido atualizado e funcionando corretamente, abra OperatorHub e verifique se há algum operador verificado pelo Red Hat. Por exemplo, verifique se o operador de armazenamento de contêiner OpenShift está disponível e veja se você tem permissões para instalar.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre segredos de pull do Red Hat, consulte [usando segredos de pull da imagem](https://docs.openshift.com/container-platform/4.6/openshift_images/managing_images/using-image-pull-secrets.html).

Para saber mais sobre o Red Hat OpenShift 4, consulte a [documentação da plataforma de contêiner do Red Hat OpenShift](https://docs.openshift.com/container-platform/4.6/welcome/index.html).
