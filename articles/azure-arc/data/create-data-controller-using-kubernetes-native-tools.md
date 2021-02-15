---
title: Criar um controlador de dados usando as ferramentas do kubernetes
description: Criar um controlador de dados usando as ferramentas do kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e8d00055d9a4d7355ccd8a33c8a9b811b852f5c8
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955273"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Criar controlador de dados de arco do Azure usando ferramentas de kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Examine o tópico [criar o controlador de dados de arco do Azure](create-data-controller.md) para obter informações gerais.

Para criar o controlador de dados de arco do Azure usando as ferramentas do kubernetes, você precisará ter as ferramentas do kubernetes instaladas.  Os exemplos neste artigo usarão `kubectl` , mas abordagens semelhantes podem ser usadas com outras ferramentas de kubernetes como, por exemplo, o painel do kubernetes, `oc` ou `helm` se você estiver familiarizado com essas ferramentas e kubernetes YAML/JSON.

[Instalar a ferramenta kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> Algumas das etapas para criar o controlador de dados Arc do Azure que são indicadas abaixo exigem permissões de administrador de cluster kubernetes.  Se você não for um administrador de cluster kubernetes, será necessário ter o administrador de cluster kubernetes para executar essas etapas em seu nome.

### <a name="cleanup-from-past-installations"></a>Limpeza de instalações anteriores

Se você instalou o controlador de dados Arc do Azure no passado, no mesmo cluster e excluiu o controlador de dados Arc do Azure usando o `azdata arc dc delete` comando, pode haver alguns objetos no nível do cluster que ainda precisariam ser excluídos. Execute os seguintes comandos para excluir objetos do nível de cluster do controlador de dados Arc do Azure:

```console
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
```

## <a name="overview"></a>Visão geral

A criação do controlador de dados de arco do Azure tem as seguintes etapas de alto nível:
1. Crie as definições de recursos personalizados para o controlador de dados Arc, instância gerenciada do SQL do Azure e hiperescala do PostgreSQL. **[Requer permissões de administrador de cluster kubernetes]**
2. Crie um namespace no qual o controlador de dados será criado. **[Requer permissões de administrador de cluster kubernetes]**
3. Crie o serviço de bootstrapper, incluindo o conjunto de réplicas, a conta de serviço, a função e a associação de função.
4. Crie um segredo para o nome de usuário e a senha do administrador do controlador de dados.
5. Crie o controlador de dados.
   
## <a name="create-the-custom-resource-definitions"></a>Criar as definições de recurso personalizado

Execute o comando a seguir para criar as definições de recursos personalizados.  **[Requer permissões de administrador de cluster kubernetes]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>Criar um namespace no qual o controlador de dados será criado

Execute um comando semelhante ao seguinte para criar um novo namespace dedicado no qual o controlador de dados será criado.  Neste exemplo e o restante dos exemplos neste artigo, será usado um nome de namespace `arc` . Se você optar por usar um nome diferente, use o mesmo nome em todo o.

```console
kubectl create namespace arc
```

Se outras pessoas estiverem usando esse namespace que não são administradores de cluster, é recomendável criar uma função de administrador de namespace e conceder essa função a esses usuários por meio de uma associação de função.  O administrador do namespace deve ter permissões completas no namespace.  Mais informações serão fornecidas posteriormente sobre como fornecer acesso mais granular baseado em função aos usuários.

## <a name="create-the-bootstrapper-service"></a>Criar o serviço de bootstrapper

O serviço bootstrapper trata as solicitações de entrada para criar, editar e excluir recursos personalizados, como um controlador de dados, uma instância gerenciada SQL ou um grupo de servidores de hiperescala PostgreSQL.

Execute o comando a seguir para criar um serviço de bootstrapper, uma conta de serviço para o serviço bootstrapper e uma função e Associação de função para a conta de serviço de bootstrapper.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/bootstrapper.yaml
```

Verifique se o Pod do bootstrapper está em execução usando o comando a seguir.  Talvez seja necessário executá-lo algumas vezes até que o status seja alterado para `Running` .

```console
kubectl get pod --namespace arc
```

O arquivo de modelo bootstrapper. YAML usa como padrão a obtenção da imagem de contêiner do bootstrapper do registro de contêiner da Microsoft (MCR).  Se o seu ambiente não tiver acesso diretamente ao registro de contêiner da Microsoft, você poderá fazer o seguinte:
- Siga as etapas para efetuar [pull das imagens de contêiner do registro de contêiner da Microsoft e enviá-las por push para um registro de contêiner privado](offline-deployment.md).
- [Crie um segredo de pull de imagem](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) para o registro de contêiner privado.
- Adicione um segredo de pull de imagem ao contêiner do bootstrapper. Consulte o exemplo a seguir.
- Altere o local da imagem para a imagem do bootstrapper. Consulte o exemplo a seguir.

O exemplo a seguir pressupõe que você criou um nome de segredo de pull de imagem `regcred` , conforme indicado na documentação do kubernetes.

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-dec-2020  <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>Criar um segredo para o administrador do controlador de dados

O nome de usuário e a senha do administrador do controlador de dados são usados para autenticar a API do controlador de dados para executar funções administrativas.  Escolha uma senha segura e compartilhe-a com apenas aquelas que precisam ter privilégios de administrador de cluster.

Um segredo kubernetes é armazenado como uma cadeia de caracteres codificada em base64-um para o nome de usuário e outro para a senha.

Você pode usar uma ferramenta online para codificar o nome de usuário e a senha desejados ou pode usar ferramentas de CLI internas, dependendo da sua plataforma.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

Depois de codificar o nome de usuário e a senha, você pode criar um arquivo com base no [arquivo de modelo](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/controller-login-secret.yaml) e substituir os valores de nome de usuário e senha pelos seus próprios.

Em seguida, execute o comando a seguir para criar o segredo.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>Criar o controlador de dados

Agora você está pronto para criar o próprio controlador de dados.

Primeiro, crie uma cópia do [arquivo de modelo](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/data-controller.yaml) localmente no seu computador para que você possa modificar algumas das configurações.

Edite o seguinte, conforme necessário:

**Necessário**
- **local**: altere esta para o local do Azure onde os _metadados_ sobre o controlador de dados serão armazenados.  Você pode ver a lista de locais do Azure disponíveis no artigo [visão geral do controlador de dados de criação](create-data-controller.md) .
- **resourcegroup**: o grupo de recursos do Azure no qual você deseja criar o recurso de controlador de dados do azure no Azure Resource Manager.  Normalmente, esse grupo de recursos já deve existir, mas não é necessário até o momento em que você carregar os dados no Azure.
- **assinatura**: o GUID da assinatura do Azure para a assinatura na qual você deseja criar os recursos do Azure.

**RECOMENDADO PARA REVISAR E POSSIVELMENTE ALTERAR OS PADRÕES**
- **armazenamento.. className**: a classe de armazenamento a ser usada para os dados do controlador de dados e os arquivos de log.  Se não tiver certeza das classes de armazenamento disponíveis no cluster kubernetes, você poderá executar o seguinte comando: `kubectl get storageclass` .  O padrão é o pressuposto de que `default` há uma classe de armazenamento que existe e que é nomeada `default` não que haja uma classe de armazenamento que _seja_ o padrão.  Observação: há duas configurações de className a serem definidas para a classe de armazenamento desejada – uma para os dados e outra para os logs.
- **ServiceType**: altere o tipo de serviço para `NodePort` se você não estiver usando um balanceador de carga.  Observação: há duas configurações do serviceType que precisam ser alteradas.

**ADICIONAL**
- **nome**: o nome padrão do controlador de dados é `arc` , mas você pode alterá-lo, se desejar.
- **DisplayName**: Defina como o mesmo valor que o atributo Name na parte superior do arquivo.
- **registro**: o registro de contêiner da Microsoft é o padrão.  Se você estiver extraindo as imagens do registro de contêiner da Microsoft e [enviando-as por push para um registro de contêiner privado](offline-deployment.md), insira o endereço IP ou o nome DNS do registro aqui.
- **dockerRegistry**: o segredo de pull de imagem a ser usado para efetuar pull das imagens de um registro de contêiner privado, se necessário.
- **Repository**: o repositório padrão no registro de contêiner da Microsoft é `arcdata` .  Se você estiver usando um registro de contêiner privado, insira o caminho da pasta/repositório que contém as imagens de contêiner de serviços de dados habilitados do Azure arr.
- **imageTag**: a marca de versão mais recente atual é padronizada no modelo, mas você pode alterá-la se quiser usar uma versão mais antiga.

Exemplo de um arquivo de YAML do controlador de dados concluído:
```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: mssql-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-dec-2020 
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

Salve o arquivo editado no computador local e execute o seguinte comando para criar o controlador de dados:

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>Monitorando o status de criação

A criação do controlador levará alguns minutos para ser concluída. Você pode monitorar o progresso em outra janela do terminal com os seguintes comandos:

> [!NOTE]
>  Os comandos de exemplo a seguir pressupõem que você criou um controlador de dados e o namespace kubernetes com o nome `arc` .  Se você usou um nome de namespace/controlador de dados diferente, poderá substituir `arc` pelo seu nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Você também pode verificar o status de criação de qualquer Pod específico executando um comando como abaixo.  Isso é especialmente útil para solucionar problemas.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

A extensão Arc do Azure para Azure Data Studio fornece um notebook para orientá-lo na experiência de como configurar o kubernetes habilitado para o Azure Arc e configurá-lo para monitorar um repositório git que contém um arquivo de exemplo SQL Instância Gerenciada YAML. Quando tudo estiver conectado, um novo Instância Gerenciada de SQL será implantado em seu cluster kubernetes.

Consulte o bloco de anotações **implantar um SQL instância gerenciada usando o Azure Arc habilitado kubernetes e fluxo** na extensão Arc do azure para Azure Data Studio.

## <a name="troubleshooting-creation-problems"></a>Solucionando problemas de criação

Se você encontrar qualquer problemas com a criação, consulte o [Guia de solução de problemas](troubleshoot-guide.md).

## <a name="next-steps"></a>Próximas etapas

- [Criar uma instância gerenciada do SQL usando ferramentas nativas do kubernetes](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Criar um grupo de servidores de hiperescala PostgreSQL usando ferramentas nativas do kubernetes](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
