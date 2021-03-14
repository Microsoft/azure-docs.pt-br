---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: 554730919d4226c07e099d5e457cd0fd20dbad30
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510831"
---
O serviço de metadados de instância do Azure (IMDS) fornece informações sobre as instâncias de máquina virtual em execução no momento. Você pode usá-lo para gerenciar e configurar suas máquinas virtuais.
Essas informações incluem a SKU, o armazenamento, as configurações de rede e os próximos eventos de manutenção. Para obter uma lista completa dos dados disponíveis, consulte o [Resumo de categorias de ponto de extremidade](#endpoint-categories).

IMDS está disponível para a execução de instâncias de máquinas virtuais (VMs) e instâncias do conjunto de dimensionamento de máquinas virtuais. Todos os pontos de extremidade dão suporte a VMs criadas e gerenciadas usando [Azure Resource Manager](/rest/api/resources/). Somente a parte da categoria e da rede atestadas da categoria de instância dão suporte a VMs criadas usando o modelo de implantação clássico. O ponto de extremidade atestado faz isso apenas para uma extensão limitada.

IMDS é uma API REST que está disponível em um endereço IP conhecido e não roteável ( `169.254.169.254` ). Você só pode acessá-lo de dentro da VM. A comunicação entre a VM e a IMDS nunca deixa o host.
Faça com que seus clientes HTTP ignorem proxies da Web na VM ao consultar IMDS e tratem `169.254.169.254` o mesmo que [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="usage"></a>Uso

### <a name="access-azure-instance-metadata-service"></a>Acessar o serviço de metadados de instância do Azure

Para acessar o IMDS, crie uma VM de [Azure Resource Manager](/rest/api/resources/) ou o [portal do Azure](https://portal.azure.com)e use os exemplos a seguir.
Para obter mais exemplos, consulte [exemplos de metadados de instância do Azure](https://github.com/microsoft/azureimds).

Aqui está o código de exemplo para recuperar todos os metadados de uma instância. Para acessar uma fonte de dados específica, consulte [categorias de ponto de extremidade](#endpoint-categories) para obter uma visão geral de todos os recursos disponíveis.

**Solicitação**

> [!IMPORTANT]
> Este exemplo ignora proxies. Você **deve** ignorar proxies ao consultar IMDS. Consulte [proxies](#proxies) para obter informações adicionais.

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | jq
```

---

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Segurança e autenticação

O serviço de metadados de instância só pode ser acessado de dentro de uma instância de máquina virtual em execução em um endereço IP não roteável. As VMs estão limitadas a interagir com metadados/funcionalidade que pertencem a si mesmas. A API é somente HTTP e nunca deixa o host.

Para garantir que as solicitações sejam diretamente destinadas a IMDS e impedir o redirecionamento não intencional ou indesejado de solicitações, solicitações:
- **Deve** conter o cabeçalho `Metadata: true`
- **Não** deve conter um `X-Forwarded-For` cabeçalho

Qualquer solicitação que não atender a esses **dois** requisitos será rejeitada pelo serviço.

> [!IMPORTANT]
> IMDS **não** é um canal para dados confidenciais. A API não é autenticada e aberta para todos os processos na VM. As informações expostas por esse serviço devem ser consideradas informações compartilhadas para todos os aplicativos em execução dentro da VM.

## <a name="proxies"></a>Proxies

IMDS não deve ser usado atrás de um proxy e fazer isso **não** é suportado. A maioria dos clientes HTTP fornece uma opção para você desabilitar proxies em suas solicitações, e essa funcionalidade deve ser utilizada ao se comunicar com o IMDS. Consulte a documentação do cliente para obter detalhes.

> [!IMPORTANT]
> Mesmo que você não conheça nenhuma configuração de proxy em seu ambiente, **você ainda deve substituir as configurações padrão de proxy do cliente**. As configurações de proxy podem ser descobertas automaticamente e a falha ao ignorar essas configurações expõe os riscos de interrupção caso a configuração do computador seja alterada no futuro.

## <a name="rate-limiting"></a>Limitação de taxa

Em geral, as solicitações para IMDS são limitadas a 5 solicitações por segundo. As solicitações que excederem esse limite serão rejeitadas com 429 respostas. As solicitações para a categoria de [identidade gerenciada](#managed-identity) são limitadas a 20 solicitações por segundo e cinco solicitações simultâneas.

## <a name="http-verbs"></a>Verbos HTTP

Atualmente, há suporte para os seguintes verbos HTTP:

| Verbo | Descrição |
|------|-------------|
| `GET` | Recuperar o recurso solicitado

## <a name="parameters"></a>Parâmetros

Os pontos de extremidade podem dar suporte a parâmetros obrigatórios e/ou opcionais. Consulte o [esquema](#schema) e a documentação do ponto de extremidade específico em questão para obter detalhes.

### <a name="query-parameters"></a>Parâmetros de consulta

Os pontos de extremidade IMDS dão suporte a parâmetros de cadeia de caracteres de consulta HTTP. Por exemplo: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

Especifica os parâmetros:

| Nome | Valor |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

Solicitações com nomes de parâmetros de consulta duplicados serão rejeitadas.

### <a name="route-parameters"></a>Parâmetros de rota

Para alguns pontos de extremidade que retornam BLOBs JSON maiores, damos suporte à anexação de parâmetros de rota ao ponto final de solicitação para filtrar um subconjunto da resposta: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
Os parâmetros correspondem aos índices/chaves que seriam usados para orientar o objeto JSON que você interagindo com uma representação analisada.

Por exemplo, `/metatadata/instance` retorna o objeto JSON:
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

Se quisermos filtrar a resposta para apenas a propriedade Compute, enviaremos a solicitação: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

Da mesma forma, se quisermos filtrar para uma propriedade aninhada ou elemento de matriz específico, manteremos a anexação de chaves: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
filtraria o primeiro elemento da `Network.interface` propriedade e retornaremos:

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> Ao filtrar para um nó folha, o `format=json` não funciona. Para essas consultas `format=text` , é necessário especificar explicitamente, pois o formato padrão é JSON.

## <a name="schema"></a>Esquema

### <a name="data-format"></a>Formato de dados

Por padrão, IMDS retorna dados no formato JSON ( `Content-Type: application/json` ). No entanto, os pontos de extremidade que dão suporte à filtragem de resposta (consulte [parâmetros de rota](#route-parameters)) também dão suporte ao formato `text` .

Para acessar um formato de resposta não padrão, especifique o formato solicitado como um parâmetro de cadeia de caracteres de consulta na solicitação. Por exemplo:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

Em respostas JSON, todos os primitivos serão do tipo `string` , e valores ausentes ou inaplicáveis sempre serão incluídos, mas serão definidos como uma cadeia de caracteres vazia.

### <a name="versioning"></a>Controle de versão

IMDS tem controle de versão e a especificação da versão de API na solicitação HTTP é obrigatória. A única exceção a esse requisito é o ponto de extremidade de [versões](#versions) , que pode ser usado para recuperar dinamicamente as versões de API disponíveis.

Conforme versões mais recentes são adicionadas, as versões mais antigas ainda podem ser acessadas para fins de compatibilidade se os scripts tiverem dependências de formatos de dados específicos.

Quando você não especificar uma versão, obterá um erro com uma lista das versões mais recentes com suporte:
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>Versões de API com suporte
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 01-02-2018
- 2018-04-02
- 01-10-2018
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 01-08-2019
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01

### <a name="swagger"></a>Swagger

Uma definição completa do Swagger para IMDS está disponível em: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Disponibilidade regional

O serviço está **geralmente disponível** em todas as nuvens do Azure.

## <a name="root-endpoint"></a>Ponto de extremidade raiz

O ponto de extremidade raiz é `http://169.254.169.254/metadata` .

## <a name="endpoint-categories"></a>Categorias de ponto de extremidade

A API IMDS contém várias categorias de ponto de extremidade que representam diferentes fontes de dados, cada uma contendo um ou mais pontos de extremidades. Consulte cada categoria para obter detalhes.

| Raiz da categoria | Descrição | Versão introduzida |
|---------------|-------------|--------------------|
| `/metadata/attested` | Confira [Dados Atestados](#attested-data) | 01-10-2018
| `/metadata/identity` | Consulte [identidade gerenciada via IMDS](#managed-identity) | 01-02-2018
| `/metadata/instance` | Consulte os [metadados da instância](#instance-metadata) | 2017-04-02
| `/metadata/loadbalancer` | Consulte [recuperar metadados de Load Balancer via IMDS](#load-balancer-metadata) | 2020-10-01
| `/metadata/scheduledevents` | Consulte [eventos agendados via IMDS](#scheduled-events) | 2017-08-01
| `/metadata/versions` | Consulte [as versões](#versions) | N/D

## <a name="versions"></a>Versões

> [!NOTE]
> Esse recurso foi lançado junto com a versão 2020-10-01, que está sendo distribuída no momento e talvez ainda não esteja disponível em todas as regiões.

### <a name="list-api-versions"></a>Listar versões de API

Retorna o conjunto de versões de API com suporte.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Parâmetros

Nenhum (este ponto de extremidade não tem versão).

#### <a name="response"></a>Resposta

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>Metadados da instância

### <a name="get-vm-metadata"></a>Obter metadados da VM

Expõe os metadados importantes para a instância de VM, incluindo computação, rede e armazenamento. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Parâmetros

| Nome | Obrigatório/Opcional | Descrição |
|------|-------------------|-------------|
| `api-version` | Obrigatório | A versão usada para atender à solicitação.
| `format` | Adicional | O formato ( `json` ou `text` ) da resposta. * Observação: pode ser necessário ao usar parâmetros de solicitação

Esse ponto de extremidade dá suporte à filtragem de resposta via [parâmetros de rota](#route-parameters).

#### <a name="response"></a>Resposta

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Divisão de esquema:

**Computação**

| Dados | Descrição | Versão introduzida |
|------|-------------|--------------------|
| `azEnvironment` | Ambiente do Azure em que a VM está em execução | 01-10-2018
| `customData` | Este recurso está desabilitado no momento. Atualizaremos esta documentação quando ela ficar disponível | 2019-02-01
| `evictionPolicy` | Define como uma [VM Spot](../articles/virtual-machines/spot-vms.md) será removida. | 2020-12-01
| `isHostCompatibilityLayerVm` | Identifica se a VM é executada na camada de compatibilidade do host | 2020-06-01
| `licenseType` | Tipo de licença para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Isso está presente apenas para VMs habilitadas para AHB | 2020-09-01
| `location` | Região do Azure na qual a máquina virtual está sendo executada | 2017-04-02
| `name` | Nome da VM | 2017-04-02
| `offer` | Oferece informações para a imagem da VM e está presente apenas para imagens implantadas na galeria de imagens do Azure | 2017-04-02
| `osProfile.adminUsername` | Especifica o nome da conta do administrador | 2020-07-15
| `osProfile.computerName` | Especifica o nome do computador | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Especifica se a autenticação de senha está desabilitada. Isso está presente apenas para VMs Linux | 2020-10-01
| `osType` | Linux ou Windows | 2017-04-02
| `placementGroupId` | [Grupo de Posicionamento](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) do conjunto de dimensionamento da sua Máquina Virtual | 2017-08-01
| `plan` | O [Plano](/rest/api/compute/virtualmachines/createorupdate#plan) que contém o nome, produto e editor de uma VM, se for uma imagem do Azure Marketplace | 2018-04-02
| `platformUpdateDomain` |  [Domínio de atualização](../articles/virtual-machines/availability.md) no qual a máquina virtual está sendo executada | 2017-04-02
| `platformFaultDomain` | [Domínio de falha](../articles/virtual-machines/availability.md) no qual a máquina virtual está sendo executada | 2017-04-02
| `priority` | Prioridade da VM. Consulte [detectar VMs](../articles/virtual-machines/spot-vms.md) para obter mais informações | 2020-12-01
| `provider` | Provedor da VM | 01-10-2018
| `publicKeys` | [Coleção de Chaves Públicas](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) atribuídas à VM e aos caminhos | 2018-04-02
| `publisher` | Publicador da imagem da máquina virtual | 2017-04-02
| `resourceGroupName` | [Grupo de recursos](../articles/azure-resource-manager/management/overview.md) para a sua Máquina Virtual | 2017-08-01
| `resourceId` | A ID [totalmente qualificada](/rest/api/resources/resources/getbyid) do recurso | 2019-03-11
| `sku` | SKU específica para a imagem da máquina virtual | 2017-04-02
| `securityProfile.secureBootEnabled` | Identifica se a inicialização segura UEFI está habilitada na VM | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Identifica se o Trusted Platform Module virtual (TPM) está habilitado na VM | 2020-06-01
| `storageProfile` | Consulte o perfil de armazenamento abaixo | 2019-06-01
| `subscriptionId` | Assinatura do Azure para a Máquina Virtual | 2017-08-01
| `tags` | [Marcas](../articles/azure-resource-manager/management/tag-resources.md) para a sua Máquina Virtual  | 2017-08-01
| `tagsList` | Marcas formatadas como uma matriz JSON para facilitar a análise programática  | 2019-06-04
| `version` | Versão da imagem da máquina virtual | 2017-04-02
| `vmId` | [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a máquina virtual | 2017-04-02
| `vmScaleSetName` | [Nome do conjunto de dimensionamento de máquinas virtuais](../articles/virtual-machine-scale-sets/overview.md) do seu conjunto de dimensionamento de máquinas virtuais | 2017-12-01
| `vmSize` | [Tamanho da VM](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [Zona de Disponibilidade](../articles/availability-zones/az-overview.md) da máquina virtual | 2017-12-01

**Perfil de armazenamento**

O perfil de armazenamento de uma VM é dividido em três categorias: referência de imagem, disco do sistema operacional e discos de dados.

O objeto de referência de imagem contém as seguintes informações sobre a imagem do sistema operacional:

| Dados | Descrição |
|------|-------------|
| `id` | ID de Recurso
| `offer` | Oferta da imagem da plataforma ou do marketplace
| `publisher` | Editor de imagem
| `sku` | SKU de imagem
| `version` | Versão da imagem da plataforma ou do marketplace

O objeto de disco do sistema operacional contém as seguintes informações sobre o disco do sistema operacional pela VM:

| Dados | Descrição |
|------|-------------|
| `caching` | Requisitos de cache
| `createOption` | Informações sobre como a VM foi criada
| `diffDiskSettings` | Configurações do disco efêmero
| `diskSizeGB` | Tamanho do disco em GB
| `image`   | Disco rígido virtual de imagem do usuário de origem
| `lun`     | Número de unidade lógica do disco
| `managedDisk` | Parâmetros do disco gerenciado
| `name`    | Nome do disco
| `vhd`     | Disco rígido virtual
| `writeAcceleratorEnabled` | Se writeAccelerator está habilitado ou não no disco

A matriz de discos de dados contém uma lista dos discos de dados anexada à VM. Cada objeto de disco de dados contém as seguintes informações:

Dados | Descrição |
-----|-------------|
| `caching` | Requisitos de cache
| `createOption` | Informações sobre como a VM foi criada
| `diffDiskSettings` | Configurações do disco efêmero
| `diskSizeGB` | Tamanho do disco em GB
| `encryptionSettings` | Configurações de criptografia do disco
| `image` | Disco rígido virtual de imagem do usuário de origem
| `managedDisk` | Parâmetros do disco gerenciado
| `name` | Nome do disco
| `osType` | Tipo de sistema operacional incluído no disco
| `vhd` | Disco rígido virtual
| `writeAcceleratorEnabled` | Se writeAccelerator está habilitado ou não no disco

**Rede**

| Dados | Descrição | Versão introduzida |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | Endereço IPv4 local da máquina virtual | 2017-04-02
| `ipv4.publicIpAddress` | Endereço IPv4 local da máquina virtual | 2017-04-02
| `subnet.address` | Endereço sub-rede da máquina virtual | 2017-04-02
| `subnet.prefix` | Prefixo de sub-rede, exemplo 24 | 2017-04-02
| `ipv6.ipAddress` | Endereço IPv6 local da máquina virtual | 2017-04-02
| `macAddress` | Endereço mac da máquina virtual | 2017-04-02


#### <a name="sample-1-tracking-vm-running-on-azure"></a>Exemplo 1: VM de controle em execução no Azure

Como provedor de serviço, você talvez precise controlar o número de máquinas virtuais que executam o seu software ou ter agentes que precisam controlar a exclusividade da máquina virtual. Para obter uma ID exclusiva de uma máquina virtual, use o `vmId` campo do serviço de metadados de instância.

**Solicitação**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Resposta**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>Exemplo 2: posicionamento de réplicas de dados diferentes

Para determinados cenários nos quais o posicionamento de réplicas diferentes é de vital importância. Por exemplo, o [posicionamento da réplica do HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou o posicionamento do contêiner por meio de um [orquestrador](https://kubernetes.io/docs/user-guide/node-selection/) pode exigir que você conheça o `platformFaultDomain` e `platformUpdateDomain` a VM esteja em execução.
Também é possível usar as [Zonas de Disponibilidade](../articles/availability-zones/az-overview.md) das instâncias para tomar essas decisões.
Você pode consultar esses dados diretamente por meio de IMDS.

**Solicitação**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Resposta**

```
0
```

#### <a name="sample-3-get-vm-tags"></a>Exemplo 3: obter as marcas de VM

As marcas de VM são incluídas na API de instância no ponto de extremidade de instância/computação/marcações.
As marcas podem ter sido aplicadas à VM do Azure para organizá-las logicamente em uma taxonomia. As marcas atribuídas a uma VM podem ser recuperadas usando a solicitação abaixo.

**Solicitação**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Resposta**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

O campo `tags` é uma cadeia de caracteres com as marcas delimitadas por ponto e vírgula. Essa saída pode ser um problema se ponto e vírgulas forem usados nas próprias marcas. Se um analisador for escrito para extrair as marcas programaticamente, você deverá contar com o `tagsList` campo. O `tagsList` campo é uma matriz JSON sem delimitadores e, consequentemente, mais fácil de analisar. As marcas de marca atribuídas a uma VM podem ser recuperadas usando a solicitação abaixo.

**Solicitação**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**Resposta**

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>Exemplo 4: obter mais informações sobre a VM durante o caso de suporte

Como provedor de serviços, você poderá receber uma chamada de suporte na qual gostaria de ter mais informações sobre a máquina virtual. Pedir ao cliente para informar os metadados de computação pode fornecer informações básicas para o profissional de suporte saber o tipo de VM no Azure.

**Solicitação**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>Exemplo 5: obter o ambiente do Azure onde a VM está em execução

O Azure tem várias nuvens soberanas, como o [Azure Governamental](https://azure.microsoft.com/overview/clouds/government/). Às vezes, é necessário que o Ambiente do Azure tome algumas decisões de runtime. O exemplo a seguir mostra como você pode gerar tal comportamento.

**Solicitação**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Resposta**

```
AzurePublicCloud
```

A nuvem e os valores do ambiente do Azure são listados aqui.

| Nuvem | Ambiente do Azure |
|-------|-------------------|
| [Todas as regiões globais do Azure geralmente disponíveis](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Governamental](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>Exemplo 6: recuperar informações de rede

**Solicitação**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**Resposta**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### <a name="sample-7-retrieve-public-ip-address"></a>Exemplo 7: recuperar endereço IP público

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Dados atestados

### <a name="get-attested-data"></a>Obter dados atestados

O IMDS ajuda a fornecer garantias de que os dados fornecidos são provenientes do Azure. A Microsoft faz parte dessas informações para que você possa confirmar se uma imagem no Azure Marketplace é aquela que você está executando no Azure.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Parâmetros

| Nome | Obrigatório/Opcional | Descrição |
|------|-------------------|-------------|
| `api-version` | Obrigatório | A versão usada para atender à solicitação.
| `nonce` | Opcional | Uma cadeia de caracteres de 10 dígitos que serve como um nonce criptográfico. Se nenhum valor for fornecido, IMDS usará o carimbo de data/hora UTC atual.

#### <a name="response"></a>Resposta

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> Devido ao mecanismo de cache do IMDS, um valor de nonce armazenado em cache anteriormente pode ser retornado.

O blob de assinatura é uma versão do documento assinada por [PKCS7](https://aka.ms/pkcs7). Ele contém o certificado usado para assinar junto com determinados detalhes específicos da VM.

Para VMs criadas usando Azure Resource Manager, o documento inclui `vmId` ,, `sku` , `nonce` `subscriptionId` , `timeStamp` para a criação e expiração do documento e as informações do plano sobre a imagem. As informações do plano são preenchidas apenas para as imagens do Azure Marketplace.

Para VMs criadas usando o modelo de implantação clássico, somente o `vmId` tem a garantia de ser populada. Você pode extrair o certificado da resposta e usá-lo para confirmar que a resposta é válida e proveniente do Azure.

O documento decodificado contém os seguintes campos:

| Dados | Descrição | Versão introduzida |
|------|-------------|--------------------|
| `licenseType` | Tipo de licença para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Isso está presente apenas para VMs habilitadas para AHB. | 2020-09-01
| `nonce` | Uma cadeia de caracteres que pode ser fornecida opcionalmente com a solicitação. Se nenhum `nonce` tiver sido fornecido, o carimbo de data/hora universal coordenado atual será usado. | 01-10-2018
| `plan` | O [plano de imagem do Azure Marketplace](/rest/api/compute/virtualmachines/createorupdate#plan). Contém a ID do plano (nome), a imagem do produto ou a oferta (produto) e a ID do editor (Publicador). | 01-10-2018
| `timestamp.createdOn` | O carimbo de data/hora UTC para quando o documento assinado foi criado | 2018-20-01
| `timestamp.expiresOn` | O carimbo de data/hora UTC para quando o documento assinado expira | 01-10-2018
| `vmId` | [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a máquina virtual | 01-10-2018
| `subscriptionId` | Assinatura do Azure para a Máquina Virtual | 2019-04-30
| `sku` | SKU específica para a imagem da máquina virtual | 2019-11-01

> [!NOTE]
> Para VMs clássicas (não Azure Resource Manager), somente o vmId tem a garantia de ser populada.

Documento de exemplo:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>Exemplo 1: validar que a VM está em execução no Azure

Os fornecedores no Azure Marketplace querem garantir que seu software seja licenciado para ser executado somente no Azure. Se alguém copiar o VHD para um ambiente local, o fornecedor precisará ser capaz de detectar isso. Por meio do IMDS, esses fornecedores podem obter dados assinados que garantem a resposta somente do Azure.

> [!NOTE]
> Este exemplo requer que o utilitário JQ seja instalado.

**Validação**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Verifique se a assinatura é de Microsoft Azure e verifique se há erros na cadeia de certificados.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Resultados**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Verifique se a assinatura é de Microsoft Azure e verifique se há erros na cadeia de certificados.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> Devido ao mecanismo de cache do IMDS, um valor anteriormente armazenado em cache `nonce` pode ser retornado.

O `nonce` no documento assinado pode ser comparado se você forneceu um `nonce` parâmetro na solicitação inicial.

> [!NOTE]
> O certificado para a nuvem pública e cada nuvem soberanas será diferente.

| Nuvem | Certificado |
|-------|-------------|
| [Todas as regiões globais do Azure geralmente disponíveis](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Governamental](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> Os certificados podem não ter uma correspondência exata do `metadata.azure.com` para a nuvem pública. Por esse motivo, a validação de certificação deve permitir um nome comum de qualquer `.metadata.azure.com` subdomínio.

Nos casos em que o certificado intermediário não pode ser baixado devido a restrições de rede durante a validação, você pode fixar o certificado intermediário. O Azure passa pelos certificados, que é a prática PKI padrão. Você deve atualizar os certificados fixados quando a substituição ocorrer. Sempre que uma alteração para atualizar o certificado intermediário é planejada, o blog do Azure é atualizado e os clientes do Azure são notificados. 

Você pode encontrar os certificados intermediários no [repositório PKI](https://www.microsoft.com/pki/mscorp/cps/default.htm). Os certificados intermediários de cada uma das regiões podem ser diferentes.

> [!NOTE]
> O certificado intermediário para o Azure China 21Vianet será da CA raiz DigiCert global, em vez de Baltimore.
Se você tiver fixado os certificados intermediários para o Azure China como parte de uma alteração de autoridade de cadeia raiz, os certificados intermediários deverão ser atualizados.


## <a name="managed-identity"></a>Identidade gerenciada

Uma identidade gerenciada, atribuída pelo sistema, pode ser habilitada na VM. Você também pode atribuir uma ou mais identidades gerenciadas atribuídas pelo usuário à VM.
Em seguida, você pode solicitar tokens para identidades gerenciadas de IMDS. Use esses tokens para autenticar com outros serviços do Azure, como Azure Key Vault.

Para obter as etapas detalhadas para habilitar esse recurso, confira [Adquirir um token de acesso](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="load-balancer-metadata"></a>Metadados de Load Balancer
Ao posicionar as instâncias de máquina virtual ou de conjunto de máquinas virtuais por trás de uma Standard Load Balancer do Azure, você pode usar IMDS para recuperar metadados relacionados ao balanceador de carga e às instâncias. Para obter mais informações, consulte [recuperar informações do balanceador de carga](../articles/load-balancer/instance-metadata-service-load-balancer.md).

## <a name="scheduled-events"></a>Eventos agendados
Você pode obter o status dos eventos agendados usando IMDS. Em seguida, o usuário pode especificar um conjunto de ações a serem executadas sobre esses eventos. Para obter mais informações, consulte [eventos agendados para Linux](../articles/virtual-machines/linux/scheduled-events.md) ou [eventos agendados para Windows](../articles/virtual-machines/windows/scheduled-events.md).


## <a name="sample-code-in-different-languages"></a>Código de exemplo em idiomas diferentes

A tabela a seguir lista exemplos de como chamar IMDS usando diferentes idiomas dentro da VM:

| Linguagem | Exemplo |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Go | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Erros e depuração

Se houver um elemento de dados não encontrado ou solicitações malformadas, o serviço de metadados da instância retornará o erro de HTTP padrão. Por exemplo:

| Código de status HTTP | Motivo |
|------------------|--------|
| `200 OK` | A solicitação foi bem-sucedida.
| `400 Bad Request` | `Metadata: true`Cabeçalho ausente ou parâmetro ausente `format=json` ao consultar um nó folha
| `404 Not Found` | O elemento solicitado não existe
| `405 Method Not Allowed` | Não há suporte para o método HTTP (verbo) no ponto de extremidade.
| `410 Gone` | Tente novamente após algum tempo por no máximo 70 segundos
| `429 Too Many Requests` | [Os limites de taxa](#rate-limiting) de API foram excedidos
| `500 Service Error` | Aguarde um pouco e tente novamente

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Estou recebendo o erro `400 Bad Request, Required metadata header not specified` . O que isso significa?**

IMDS exige que o cabeçalho `Metadata: true` seja passado na solicitação. Passar esse cabeçalho na chamada REST permite o acesso ao IMDS.

**Por que não estou obtendo informações de computação para minha máquina virtual?**

Atualmente, o IMDS só dá suporte a instâncias criadas com Azure Resource Manager.

**Eu criei minha VM por Azure Resource Manager algum tempo atrás. Por que não vejo informações de metadados de computação?**

Se você criou sua VM após setembro de 2016, adicione uma [marca](../articles/azure-resource-manager/management/tag-resources.md) para começar a ver os metadados de computação. Se você criou sua VM antes de setembro de 2016, adicione ou remova extensões ou discos de dados para a instância de VM para atualizar os metadados.

**Por que não vejo todos os dados populados para uma nova versão?**

Se você criou sua VM após setembro de 2016, adicione uma [marca](../articles/azure-resource-manager/management/tag-resources.md) para começar a ver os metadados de computação. Se você criou sua VM antes de setembro de 2016, adicione ou remova extensões ou discos de dados para a instância de VM para atualizar os metadados.

**Por que estou recebendo o erro `500 Internal Server Error` ou `410 Resource Gone` ?**

Repita a solicitação. Para obter mais informações, consulte [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults). Se o problema persistir, crie um problema de suporte no portal do Azure para a VM.

**Isso funcionaria para instâncias do conjunto de dimensionamento de máquinas virtuais?**

Sim, IMDS está disponível para instâncias do conjunto de dimensionamento de máquinas virtuais.

**Atualizei minhas marcas em conjuntos de dimensionamento de máquinas virtuais, mas elas não aparecem nas instâncias (ao contrário de VMs de instância única). Estou fazendo algo errado?**

Atualmente, marcas para conjuntos de dimensionamento de máquinas virtuais mostram apenas para a VM em uma reinicialização, reimagem ou alteração de disco para a instância.

**Por que minha solicitação atingiu o tempo limite para minha chamada para o serviço?**

As chamadas de metadados devem ser feitas a partir do endereço IP primário atribuído à placa de rede primária da VM. Além disso, se você alterou suas rotas, deve haver uma rota para o endereço 169.254.169.254/32 na tabela de roteamento local da VM.

#### <a name="windows"></a>[Windows](#tab/windows/)

1. Despeje sua tabela de roteamento local e procure a entrada IMDS. Por exemplo:
    ```console
    > route print
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
      169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
    ... (continues) ...
    ```
1. Verifique se existe uma rota para `169.254.169.254` e anote a interface de rede correspondente (por exemplo, `172.16.69.7` ).
1. Despeje a configuração da interface e localize a interface que corresponde à referida na tabela de roteamento, observando o endereço MAC (físico).
    ```console
    > ipconfig /all
    ... (continues) ...
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
       Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
       Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
       DHCP Enabled. . . . . . . . . . . : Yes
       Autoconfiguration Enabled . . . . : Yes
       Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
       IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
       Subnet Mask . . . . . . . . . . . : 255.255.255.0
    ... (continues) ...
    ```
1. Confirme se a interface corresponde à NIC primária e ao IP primário da VM. Você pode encontrar a NIC e o IP primário examinando a configuração de rede na portal do Azure ou examinando-a com o CLI do Azure. Observe os IPs privados (e o endereço MAC se você estiver usando a CLI). Aqui está um exemplo de CLI do PowerShell:
    ```powershell
    $ResourceGroup = '<Resource_Group>'
    $VmName = '<VM_Name>'
    $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
    foreach($NicName in $NicNames)
    {
        $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
        Write-Host $NicName, $Nic.primary, $Nic.macAddress
    }
    # Output: wintest767 True 00-0D-3A-E5-1C-C0
    ```
1. Se eles não corresponderem, atualize a tabela de roteamento para que a NIC primária e o IP sejam direcionados.

#### <a name="linux"></a>[Linux](#tab/linux/)

 1. Despeje a tabela de roteamento local com um comando como `netstat -r` e procure a entrada IMDS (por exemplo,):
    ```console
    ~$ netstat -r
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
    default         _gateway        0.0.0.0         UG        0 0          0 eth0
    168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
    169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
    172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
    ```
1. Verifique se existe uma rota para `169.254.169.254` e anote a interface de rede correspondente (por exemplo, `eth0` ).
1. Despeja a configuração de interface para a interface correspondente na tabela de roteamento (Observe que o nome exato do arquivo de configuração pode variar)
    ```console
    ~$ cat /etc/netplan/50-cloud-init.yaml
    network:
    ethernets:
        eth0:
            dhcp4: true
            dhcp4-overrides:
                route-metric: 100
            dhcp6: false
            match:
                macaddress: 00:0d:3a:e4:c7:2e
            set-name: eth0
    version: 2
    ```
1. Se você estiver usando um IP dinâmico, observe o endereço MAC. Se você estiver usando um IP estático, poderá observar os IP listados e/ou o endereço MAC.
1. Confirme se a interface corresponde à NIC primária e ao IP primário da VM. Você pode encontrar a NIC e o IP primário examinando a configuração de rede na portal do Azure ou examinando-a com o CLI do Azure. Observe os IPs privados (e o endereço MAC se você estiver usando a CLI). Aqui está um exemplo de CLI do PowerShell:
    ```powershell
    $ResourceGroup = '<Resource_Group>'
    $VmName = '<VM_Name>'
    $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
    foreach($NicName in $NicNames)
    {
        $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
        Write-Host $NicName, $Nic.primary, $Nic.macAddress
    }
    # Output: ipexample606 True 00-0D-3A-E4-C7-2E
    ```
1. Se eles não corresponderem, atualize a tabela de roteamento de modo que a NIC/IP primária seja destinada.

---

**Clustering de failover no Windows Server**

Quando você está consultando o IMDS com clustering de failover, às vezes é necessário adicionar uma rota à tabela de roteamento. Aqui está como:

1. Abra uma prompt de comando com privilégios de administrador.

1. Execute o comando a seguir e anote o endereço da interface para destino de rede ( `0.0.0.0` ) na tabela de rotas IPv4.

```bat
route print
```

> [!NOTE]
> A saída de exemplo a seguir é de uma VM do Windows Server com cluster de failover habilitado. Para simplificar, a saída contém apenas a tabela de rotas IPv4.

```
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

Execute o comando a seguir e use o endereço da interface para o destino de rede ( `0.0.0.0` ), que é ( `10.0.1.10` ) neste exemplo.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="support"></a>Suporte

Se você não conseguir obter uma resposta de metadados após várias tentativas, poderá criar um problema de suporte no portal do Azure.

## <a name="product-feedback"></a>Comentários sobre o produto

Você pode fornecer comentários sobre o produto e ideias para nosso canal de comentários do usuário em máquinas virtuais > serviço de metadados de instância aqui: https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627

## <a name="next-steps"></a>Próximas etapas

[Adquirir um token de acesso para a VM](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Eventos agendados para Linux](../articles/virtual-machines/linux/scheduled-events.md)

[Eventos agendados para Windows](../articles/virtual-machines/windows/scheduled-events.md)
