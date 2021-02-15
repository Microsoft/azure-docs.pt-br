---
title: Referência de YAML para grupo de contêineres
description: Referência do arquivo YAML com suporte das instâncias de contêiner do Azure para configurar um grupo de contêineres
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: d0ec8d13eebba1c60f5a52f8c43bdd8b90eeb913
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87084753"
---
# <a name="yaml-reference-azure-container-instances"></a>Referência de YAML: instâncias de contêiner do Azure

Este artigo aborda a sintaxe e as propriedades do arquivo YAML com suporte das instâncias de contêiner do Azure para configurar um [grupo de contêineres](container-instances-container-groups.md). Use um arquivo YAML para inserir a configuração do grupo para o comando [AZ container Create][az-container-create] no CLI do Azure. 

Um arquivo YAML é uma maneira conveniente de configurar um grupo de contêineres para implantações reproduzíveis. É uma alternativa concisa usar um modelo do [Resource Manager](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) ou os SDKs das instâncias de contêiner do Azure para criar ou atualizar um grupo de contêineres.

> [!NOTE]
> Essa referência se aplica a arquivos YAML para a versão da API REST de instâncias de contêiner do Azure `2019-12-01` .

## <a name="schema"></a>Esquema 

O esquema para o arquivo YAML segue, incluindo comentários para realçar as propriedades da chave. Para obter uma descrição das propriedades nesse esquema, consulte a seção [valores de propriedade](#property-values) .

```yml
name: string  # Name of the container group
apiVersion: '2019-12-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
  sku: string # SKU for the container group
  encryptionProperties:
    vaultBaseUrl: string
    keyName: string
    keyVersion: string
  initContainers: # Array of init containers in the group
  - name: string
    properties:
      image: string
      command:
      - string
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      volumeMounts:
      - name: string
        mountPath: string
        readOnly: boolean
```

## <a name="property-values"></a>Valores de propriedade

As tabelas a seguir descrevem os valores necessários para definir no esquema.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Objeto Microsoft. ContainerInstance/containerGroups

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome do grupo de contêineres. |
|  apiVersion | enum | Sim | 01-10-2018 |
|  local | string | No | O local do recurso. |
|  marcas | objeto | Não | As marcas de recurso. |
|  identidade | objeto | Não | A identidade do grupo de contêineres, se configurada. - [Objeto ContainerGroupIdentity](#containergroupidentity-object) |
|  properties | objeto | Sim | [Objeto ContainerGroupProperties](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>Objeto ContainerGroupIdentity

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  type | enum | Não | O tipo de identidade usado para o grupo de contêineres. O tipo ' SystemAssigned, userassigned ' inclui uma identidade criada implicitamente e um conjunto de identidades atribuídas pelo usuário. O tipo ' none ' removerá todas as identidades do grupo de contêineres. -SystemAssigned, userassigned, SystemAssigned, userassigned, None |
|  userAssignedIdentities | objeto | Não | A lista de identidades de usuário associada ao grupo de contêineres. As referências de chave de dicionário de identidade do usuário serão Azure Resource Manager IDs de recurso no formato: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName} '. |




### <a name="containergroupproperties-object"></a>Objeto ContainerGroupProperties

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  containers | matriz | Sim | Os contêineres dentro do grupo de contêineres. - [Objeto de contêiner](#container-object) |
|  imageRegistryCredentials | matriz | Não | As credenciais de registro de imagem pelas quais o grupo de contêineres é criado. - [Objeto ImageRegistryCredential](#imageregistrycredential-object) |
|  restartPolicy | enum | Não | Reinicie a política para todos os contêineres dentro do grupo de contêineres. - `Always` Sempre reiniciar – `OnFailure` reiniciar em caso de falha- `Never` nunca reinicie. -Sempre, OnFailure, nunca |
|  ipAddress | objeto | Não | O tipo de endereço IP do grupo de contêineres. - [Objeto IpAddress](#ipaddress-object) |
|  osType | enum | Sim | O tipo de sistema operacional exigido pelos contêineres no grupo de contêineres. -Windows ou Linux |
|  volumes | matriz | Não | A lista de volumes que podem ser montados por contêineres neste grupo de contêineres. - [Objeto de volume](#volume-object) |
|  diagnóstico | objeto | Não | As informações de diagnóstico para um grupo de contêineres. - [Objeto ContainerGroupDiagnostics](#containergroupdiagnostics-object) |
|  networkProfile | objeto | Não | As informações de perfil de rede para um grupo de contêineres. - [Objeto ContainerGroupNetworkProfile](#containergroupnetworkprofile-object) |
|  dnsConfig | objeto | Não | As informações de configuração de DNS para um grupo de contêineres. - [Objeto DnsConfiguration](#dnsconfiguration-object) |
| sku | enum | Não | A SKU para um grupo de contêineres-Standard ou dedicado |
| criptografiaproperties | objeto | Não | As propriedades de criptografia para um grupo de contêineres. - [Objeto EncryptionProperties](#encryptionproperties-object) | 
| initContainers | matriz | Não | Os contêineres de inicialização para um grupo de contêineres. - [Objeto InitContainerDefinition](#initcontainerdefinition-object) |




### <a name="container-object"></a>Objeto de contêiner

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome fornecido pelo usuário da instância de contêiner. |
|  properties | objeto | Sim | As propriedades da instância de contêiner. - [Objeto containerproperties](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>Objeto ImageRegistryCredential

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  Servidor | string | Sim | O servidor do registro de imagem do Docker sem um protocolo como "http" e "https". |
|  Nome de Usuário | string | Sim | O nome de usuário para o registro privado. |
|  password | Cadeia de caracteres | No | A senha para o registro particular. |




### <a name="ipaddress-object"></a>Objeto IpAddress

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  ports | matriz | Sim | A lista de portas expostas no grupo de contêineres. - [Objeto Port](#port-object) |
|  type | enum | Sim | Especifica se o IP é exposto à Internet pública ou à VNET privada. -Público ou privado |
|  IP | Cadeia de caracteres | No | O IP exposto à Internet pública. |
|  dnsNameLabel | Cadeia de caracteres | No | O rótulo de nome DNS para o IP. |




### <a name="volume-object"></a>Objeto de volume

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome do volume. |
|  azurefile | objeto | Não | O volume do arquivo do Azure. - [Objeto AzureFileVolume](#azurefilevolume-object) |
|  emptyDir | objeto | Não | O volume de diretório vazio. |
|  segredo | objeto | Não | O volume secreto. |
|  gitRepo | objeto | Não | O volume do repositório git. - [Objeto GitRepoVolume](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>Objeto ContainerGroupDiagnostics

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objeto | Não | Informações do log Analytics do grupo de contêineres. - [Objeto LogAnalytics](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>Objeto ContainerGroupNetworkProfile

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  id | string | Sim | O identificador de um perfil de rede. |




### <a name="dnsconfiguration-object"></a>Objeto DnsConfiguration

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  nameServers | matriz | Sim | Os servidores DNS para o grupo de contêineres. -Cadeia de caracteres |
|  searchDomains | Cadeia de caracteres | No | Os domínios de pesquisa de DNS para pesquisa de nome de host no grupo de contêineres. |
|  opções | Cadeia de caracteres | No | As opções de DNS para o grupo de contêineres. |


### <a name="encryptionproperties-object"></a>Objeto EncryptionProperties

| Nome  | Type  | Obrigatório  | Valor |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl  | string    | Sim   | A URL base do keyvault. |
| keyName   | string    | Sim   | O nome da chave de criptografia. |
| keyversion    | string    | Sim   | A versão da chave de criptografia. |

### <a name="initcontainerdefinition-object"></a>Objeto InitContainerDefinition

| Nome  | Type  | Obrigatório  | Valor |
|  ---- | ---- | ---- | ---- |
| name  | string |  Sim | O nome do contêiner de inicialização. |
| properties    | objeto    | Sim   | As propriedades do contêiner de inicialização. - [Objeto InitContainerPropertiesDefinition](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>Objeto containerproperties

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  image | string | Sim | O nome da imagem usada para criar a instância de contêiner. |
|  . | matriz | Não | Os comandos a serem executados na instância de contêiner no formato EXEC. -Cadeia de caracteres |
|  ports | matriz | Não | As portas expostas na instância do contêiner. - [Objeto ContainerPort](#containerport-object) |
|  environmentVariables | matriz | Não | As variáveis de ambiente a serem definidas na instância de contêiner. - [Objeto EnvironmentVariable](#environmentvariable-object) |
|  recursos | objeto | Sim | Os requisitos de recurso da instância de contêiner. - [Objeto ResourceRequirements](#resourcerequirements-object) |
|  volumeMounts | matriz | Não | As montagens de volume disponíveis para a instância de contêiner. - [Objeto VolumeMount](#volumemount-object) |
|  livenessProbe | objeto | Não | A investigação de tempo de vida. - [Objeto ContainerProbe](#containerprobe-object) |
|  readinessProbe | objeto | Não | A investigação de preparação. - [Objeto ContainerProbe](#containerprobe-object) |




### <a name="port-object"></a>Objeto Port

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  protocolo | enum | Não | O protocolo associado à porta. -TCP ou UDP |
|  porta | Número inteiro | Sim | O número da porta. |




### <a name="azurefilevolume-object"></a>Objeto AzureFileVolume

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  shareName | string | Sim | O nome do compartilhamento de arquivos do Azure a ser montado como um volume. |
|  readOnly | booleano | Não | O sinalizador que indica se o arquivo compartilhado do Azure montado como um volume é somente leitura. |
|  storageAccountName | string | Sim | O nome da conta de armazenamento que contém o compartilhamento de arquivos do Azure. |
|  storageAccountKey | Cadeia de caracteres | No | A chave de acesso da conta de armazenamento usada para acessar o compartilhamento de arquivos do Azure. |




### <a name="gitrepovolume-object"></a>Objeto GitRepoVolume

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  directory | Cadeia de caracteres | No | Nome do diretório de destino. Não deve conter ou começar com '.. '.  Se '. ' for fornecido, o diretório de volume será o repositório git.  Caso contrário, se especificado, o volume conterá o repositório git no subdiretório com o nome fornecido. |
|  repository | string | Sim | URL do repositório |
|  revisão | Cadeia de caracteres | No | Confirme o hash para a revisão especificada. |



### <a name="loganalytics-object"></a>Objeto LogAnalytics

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | Sim | A ID do espaço de trabalho para o log Analytics |
|  workspaceKey | string | Sim | A chave do espaço de trabalho para o log Analytics |
|  logType | enum | Não | O tipo de log a ser usado. -ContainerInsights ou ContainerInstanceLogs |
|  metadata | objeto | Não | Metadados para o log Analytics. |


### <a name="initcontainerpropertiesdefinition-object"></a>Objeto InitContainerPropertiesDefinition

| Nome  | Type  | Obrigatório  | Valor |
|  ---- | ---- | ---- | ---- |
| image | string    | No    | A imagem do contêiner de inicialização. |
| .   | matriz | Não    | O comando a ser executado no contêiner init no formato EXEC. -Cadeia de caracteres |
| environmentVariables | matriz  | Não |As variáveis de ambiente a serem definidas no contêiner de inicialização. - [Objeto EnvironmentVariable](#environmentvariable-object)
| volumeMounts |matriz   | Não    | As montagens de volume disponíveis para o contêiner de inicialização. - [Objeto VolumeMount](#volumemount-object)

### <a name="containerport-object"></a>Objeto ContainerPort

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  protocolo | enum | Não | O protocolo associado à porta. -TCP ou UDP |
|  porta | Número inteiro | Sim | O número da porta exposta no grupo de contêineres. |




### <a name="environmentvariable-object"></a>Objeto EnvironmentVariable

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome da variável de ambiente. |
|  value | string | No | O valor da variável de ambiente. |
|  Parâmetro securevalue | Cadeia de caracteres | No | O valor da variável de ambiente segura. |




### <a name="resourcerequirements-object"></a>Objeto ResourceRequirements

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  solicitações | objeto | Sim | As solicitações de recurso desta instância de contêiner. - [Objeto ResourceRequests](#resourcerequests-object) |
|  limites | objeto | Não | Os limites de recurso desta instância de contêiner. - [Objeto ResourceLimits](#resourcelimits-object) |




### <a name="volumemount-object"></a>Objeto VolumeMount

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome da montagem do volume. |
|  mountPath | string | Sim | O caminho dentro do contêiner em que o volume deve ser montado. Não deve conter dois-pontos (:). |
|  readOnly | booleano | Não | O sinalizador que indica se a montagem do volume é somente leitura. |




### <a name="containerprobe-object"></a>Objeto ContainerProbe

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  exec | objeto | Não | O comando de execução para o [objeto](#containerexec-object) Probe-ContainerExec |
|  httpGet | objeto | Não | As configurações http get para o objeto Probe- [ContainerHttpGet](#containerhttpget-object) |
|  initialDelaySeconds | Número inteiro | Não | Os segundos de atraso iniciais. |
|  periodSeconds | Número inteiro | Não | O período de segundos. |
|  Limite | Número inteiro | Não | O limite de falha. |
|  successThreshold | Número inteiro | Não | O limite de êxito. |
|  timeoutSeconds | Número inteiro | Não | O tempo limite em segundos. |




### <a name="resourcerequests-object"></a>Objeto ResourceRequests

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | número | Sim | A solicitação de memória em GB dessa instância de contêiner. |
|  cpu | número | Sim | A solicitação de CPU desta instância de contêiner. |
|  GPU | objeto | Não | A solicitação de GPU desta instância de contêiner. - [Objeto GpuResource](#gpuresource-object) |




### <a name="resourcelimits-object"></a>Objeto ResourceLimits

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | número | Não | O limite de memória em GB dessa instância de contêiner. |
|  cpu | número | Não | O limite de CPU desta instância de contêiner. |
|  GPU | objeto | Não | O limite de GPU dessa instância de contêiner. - [Objeto GpuResource](#gpuresource-object) |




### <a name="containerexec-object"></a>Objeto ContainerExec

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  . | matriz | Não | Os comandos a serem executados no contêiner. -Cadeia de caracteres |




### <a name="containerhttpget-object"></a>Objeto ContainerHttpGet

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  caminho | string | No | O caminho para investigação. |
|  porta | Número inteiro | Sim | O número da porta a ser investigada. |
|  scheme | enum | Não | O esquema. -http ou https |




### <a name="gpuresource-object"></a>Objeto GpuResource

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  count | Número inteiro | Sim | A contagem do recurso de GPU. |
|  sku | enum | Sim | A SKU do recurso de GPU. -K80, P100, V100 |


## <a name="next-steps"></a>Próximas etapas

Consulte o tutorial [implantar um grupo de vários contêineres usando um arquivo YAML](container-instances-multi-container-yaml.md).

Consulte exemplos de como usar um arquivo YAML para implantar grupos de contêineres em uma [rede virtual](container-instances-vnet.md) ou que [montam um volume externo](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

