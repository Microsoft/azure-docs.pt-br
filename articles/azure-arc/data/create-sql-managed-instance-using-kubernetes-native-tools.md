---
title: Criar uma instância gerenciada do SQL usando ferramentas do kubernetes
description: Criar uma instância gerenciada do SQL usando ferramentas do kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: cade888d951c2071f8f40c145e28eed3c3a5d27c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384241"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>Criar uma instância gerenciada do SQL do Azure usando ferramentas de kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você já deve ter criado um [controlador de dados de arco do Azure](./create-data-controller.md).

Para criar uma instância gerenciada do SQL usando as ferramentas do kubernetes, você precisará ter as ferramentas do kubernetes instaladas.  Os exemplos neste artigo usarão `kubectl` , mas abordagens semelhantes podem ser usadas com outras ferramentas de kubernetes como, por exemplo, o painel do kubernetes, `oc` ou `helm` se você estiver familiarizado com essas ferramentas e kubernetes YAML/JSON.

[Instalar a ferramenta kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Visão geral

Para criar uma instância gerenciada do SQL, você precisa criar um segredo kubernetes para armazenar o logon e a senha do administrador do sistema com segurança e um recurso personalizado da instância gerenciada do SQL com base na definição de recurso personalizado do sqlmanagedinstance.

## <a name="create-a-yaml-file"></a>Criar um arquivo YAML

Você pode usar o arquivo [YAML do modelo](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/sqlmi.yaml) como um ponto de partida para criar seu próprio arquivo YAML de instância gerenciada do SQL personalizado.  Baixe esse arquivo em seu computador local e abra-o em um editor de texto.  É útil usar um editor de texto como [vs Code](https://code.visualstudio.com/download) que dão suporte a realce e reutilização de sintaxe para arquivos YAML.

Este é um exemplo de arquivo YAML:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded user name. 'sa' is not allowed>
kind: Secret
metadata:
  name: sql1-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
  name: sql1
spec:
  limits:
    memory: 4Gi
    vcores: "4"
  requests:
    memory: 2Gi
    vcores: "1"
  service:
    type: LoadBalancer
  storage:
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>Personalizando o logon e a senha

Um segredo kubernetes é armazenado como uma cadeia de caracteres codificada em base64-um para o nome de usuário e outro para a senha.  Você precisará codificar em base64 um logon e uma senha de administrador do sistema e colocá-los no local do espaço reservado em `data.password` e `data.username` .  Não inclua os `<` símbolos e `>` fornecidos no modelo.

> [!NOTE]
> Para obter a segurança ideal, o uso do valor ' sa ' não é permitido para o logon.
> Siga a [política de complexidade de senha](/sql/relational-databases/security/password-policy#password-complexity).

Você pode usar uma ferramenta online para codificar o nome de usuário e a senha desejados ou pode usar ferramentas de CLI internas, dependendo da sua plataforma.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

### <a name="customizing-the-name"></a>Personalizando o nome

O modelo tem um valor de ' sql1 ' para o atributo Name.  Você pode alterar isso, mas deve ser caracteres que sigam os padrões de nomenclatura do DNS.  Você também deve alterar o nome do segredo para corresponder.  Por exemplo, se você alterar o nome da instância gerenciada do SQL para ' sql2 ', deverá alterar o nome do segredo de ' sql1-login-Secret ' para ' sql2-login-Secret '

### <a name="customizing-the-resource-requirements"></a>Personalizando os requisitos de recursos

Você pode alterar os requisitos de recursos-os limites de RAM e de núcleo e as solicitações-conforme necessário.  

> [!NOTE]
> Você pode aprender mais sobre a [governança de recursos do kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Requisitos para limites e solicitações de recursos:
- O valor do limite de núcleos é **necessário** para fins de cobrança.
- O restante das solicitações e dos limites de recursos são opcionais.
- O limite e a solicitação de núcleos devem ser um valor inteiro positivo, se especificado.
- O mínimo de 2 núcleos é necessário para a solicitação de núcleos, se especificado.
- O formato do valor da memória segue a notação kubernetes.  
- Um mínimo de 2Gi é necessário para a solicitação de memória, se especificado.
- Como uma diretriz geral, você deve ter 4 GB de RAM para cada 1 núcleo para casos de uso de produção.

### <a name="customizing-service-type"></a>Personalizando o tipo de serviço

O tipo de serviço pode ser alterado para NodePort, se desejado.  Um número de porta aleatório será atribuído.

### <a name="customizing-storage"></a>Personalizando o armazenamento

Você pode personalizar as classes de armazenamento para armazenamento para corresponder ao seu ambiente.  Se você não tiver certeza de quais classes de armazenamento estão disponíveis, poderá executar o comando `kubectl get storageclass` para exibi-las.  O modelo tem um valor padrão de ' default '.  Isso significa que há uma classe de armazenamento _chamada_ ' default ', não que haja uma classe de armazenamento que _seja_ o padrão.  Opcionalmente, você também pode alterar o tamanho do seu armazenamento.  Você pode ler mais sobre a [configuração de armazenamento](./storage-configuration.md).

## <a name="creating-the-sql-managed-instance"></a>Criando a instância gerenciada do SQL

Agora que você personalizou o arquivo YAML da instância gerenciada do SQL, é possível criar a instância gerenciada do SQL executando o seguinte comando:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```


## <a name="monitoring-the-creation-status"></a>Monitorando o status de criação

A criação da instância gerenciada do SQL levará alguns minutos para ser concluída. Você pode monitorar o progresso em outra janela do terminal com os seguintes comandos:

> [!NOTE]
>  Os comandos de exemplo a seguir pressupõem que você criou uma instância gerenciada do SQL denominada ' sql1 ' e o namespace kubernetes com o nome ' Arc '.  Se você usou um nome de instância gerenciada de namespace/SQL diferente, poderá substituir ' Arc ' e ' sqlmi ' por seus nomes.

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Você também pode verificar o status de criação de qualquer Pod específico executando um comando como abaixo.  Isso é especialmente útil para solucionar problemas.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Solucionando problemas de criação

Se você encontrar qualquer problemas com a criação, consulte o [Guia de solução de problemas](troubleshoot-guide.md).

## <a name="next-steps"></a>Próximas etapas

[Conectar-se ao SQL Instância Gerenciada habilitado para Arc do Azure](connect-managed-instance.md)
