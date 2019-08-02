---
title: Computação do Azure – Extensão de Diagnóstico Linux | Microsoft Docs
description: Como configurar a Extensão de Diagnóstico Linux (LAD) no Azure para coletar métricas e eventos de log de VMs Linux em execução no Azure.
services: virtual-machines-linux
author: abhijeetgaiha
manager: sankalpsoni
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: gwallace
ms.openlocfilehash: 0627361fdd4f94a329b08b184dbd542e1927af39
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871920"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Use a Extensão de Diagnóstico Linux para monitorar as métricas e os logs

Este documento descreve a versão 3.0 e mais recente da Extensão de Diagnóstico do Linux.

> [!IMPORTANT]
> Para saber mais sobre a versão 2.3 e anteriores, veja [este documento](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Introdução

A Extensão de Diagnóstico Linux ajuda um usuário a monitorar a integridade de uma VM Linux em execução no Microsoft Azure. Ela oferece os seguintes recursos:

* Coleta métricas de desempenho do sistema da VM e as armazena em uma tabela específica em uma conta de armazenamento designada.
* Recupera os eventos de log do syslog e os armazena em uma tabela específica na conta de armazenamento designada.
* Permite que os usuários personalizem as métricas de dados que são coletadas e carregadas.
* Permite que os usuários personalizem as instalações de syslog e os níveis de severidade dos eventos que são coletados e carregados.
* Permite que os usuários carreguem arquivos de log especificados em uma tabela de armazenamento designada.
* Oferece suporte ao envio de métricas e eventos de log para pontos de extremidade arbitrários de EventHub e blobs formatados pelo JSON na conta de armazenamento designada.

Essa extensão funciona com os dois modelos de implantação do Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalando a extensão em sua VM

Você pode habilitar essa extensão usando os cmdlets do Azure PowerShell, os scripts da CLI do Azure, os modelos de ARM ou o portal do Azure. Para saber mais, veja [Recursos de extensões](features-linux.md).

Estas instruções de instalação e uma [configuração de amostra para download](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) configuram o LAD 3.0 para:

* capturar e armazenar as mesmas métricas fornecidas pelo LAD 2.3;
* capturar um conjunto útil de métricas do sistema de arquivo, novos para o LAD 3.0;
* capturar a coleção de syslog padrão habilitada pelo LAD 2.3;
* habilitar a experiência do Portal do Azure para gráficos e criação de alertas nas métricas da VM.

A configuração para download é apenas um exemplo; modifique-a para atender às suas necessidades.

### <a name="prerequisites"></a>Pré-requisitos

* **Agente Linux do Azure versão 2.2.0 ou posterior**. A maioria das imagens de galeria da VM Linux do Azure inclui a versão 2.2.7 ou posterior. Execute `/usr/sbin/waagent -version` para confirmar a versão instalada na VM. Se a VM estiver executando uma versão mais antiga do agente convidado, execute [estas instruções](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) para atualizá-la.
* **CLI do Azure**. [Configurar o ambiente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu computador.
* O comando wget, caso ainda não o tenha: Execute `sudo apt-get install wget`.
* Uma assinatura existente do Azure e uma conta de armazenamento existente nela para armazenar os dados.
* A lista de distribuições Linux com suporte está em https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Instalação de exemplo

Preencha os parâmetros corretos nas três primeiras linhas, em seguida, execute este script como raiz:

```bash
# Set your Azure VM diagnostic parameters correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

A URL para a configuração de amostra e seu conteúdo estão sujeitos a alterações. Baixe uma cópia do arquivo JSON de configurações do portal e personalize-o de acordo com as suas necessidades. Os modelos ou as automações que você construir deverão usar sua própria cópia em vez de baixar essa URL toda vez.

### <a name="updating-the-extension-settings"></a>Atualização das configurações de extensão

Depois que você tiver alterado as configurações Públicas ou Protegidas, implante-as na VM executando o mesmo comando. Se algo for alterado nas configurações, as configurações atualizadas serão enviadas para a extensão. O LAD recarrega a configuração e é reiniciado.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migração de versões anteriores da extensão

A versão mais recente da extensão é a **3.0**. **As versões anteriores (2.x) antigas são substituídas e podem ser canceladas em ou após 31 de julho de 2018**.

> [!IMPORTANT]
> Essa extensão introduz alterações significativas na configuração da extensão. Uma alteração foi feita para melhorar a segurança da extensão; como resultado disso, a compatibilidade com as versões 2.x pode não ser mantida. Além disso, o Editor de Extensões para esta extensão é diferente do editor para as versões 2.x.
>
> Para migrar da 2.x para essa nova versão da extensão, você deverá desinstalar a extensão antiga (com o nome do editor antigo) e instalar a versão 3 da extensão.

Recomendações:

* Instale a extensão com a atualização de versão secundária automática habilitada.
  * Em VMs de modelo de implantação clássicas, especifique '3.*' como a versão, se você estiver instalando a extensão por meio da CLI do Azure XPLAT ou do Powershell.
  * Nas VMs do Modelo de implantação do Azure Resource Manager, inclua '"autoUpgradeMinorVersion": true' no modelo de implantação da VM.
* Use uma conta de armazenamento nova/diferente para o LAD 3.0. Há várias pequenas incompatibilidades entre o LAD 2.3 e o LAD 3.0 que dificultam o compartilhamento de uma conta:
  * O LAD 3.0 armazena os eventos de syslog em uma tabela com um nome diferente.
  * As cadeias de caracteres counterSpecifier para as métricas `builtin` são diferentes no LAD 3.0.

## <a name="protected-settings"></a>Configurações protegidas

Esse conjunto de informações de configuração contém informações confidenciais que devem ser protegidas da visualização pública, por exemplo, as credenciais de armazenamento. Essas configurações são transmitidas para e armazenadas pela extensão de forma criptografada.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Nome | Valor
---- | -----
storageAccountName | O nome da conta de armazenamento na qual os dados são gravados pela extensão.
storageAccountEndPoint | (opcional) O ponto de extremidade que identifica a nuvem na qual existe a conta de armazenamento. Se essa configuração estiver ausente, o LAD utiliza como padrão a nuvem pública do Azure, `https://core.windows.net`. Para usar uma conta de armazenamento no Azure Alemanha, no Azure Governamental ou Azure China, defina este valor corretamente.
storageAccountSasToken | Um [Token de SAS de conta](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) para serviços Blob e de tabela (`ss='bt'`), aplicável a contêineres e objetos (`srt='co'`), que concede permissão para adicionar, criar, listar, atualizar e gravar (`sp='acluw'`). *Não* inclua o ponto de interrogação (?) no início.
mdsdHttpProxy | (opcional) As informações de proxy de HTTP necessárias para habilitar a extensão para se conectar ao ponto de extremidade e à conta de armazenamento especificados.
sinksConfig | (opcional) Detalhes de destinos alternativos para os quais as métricas e os eventos podem ser entregues. Os detalhes específicos de cada coletor de dados compatível com a extensão são abordados nas seções a seguir.


> [!NOTE]
> Ao implantar a extensão com um modelo de implantação do Azure, a conta de armazenamento e o token SAS devem ser criados com antecedência e, em seguida, passados para o modelo. Você não pode implantar uma VM, uma conta de armazenamento e configurar a extensão em um único modelo. Atualmente não há suporte para a criação de um token SAS dentro de um modelo.

Você pode facilmente construir o token de SAS necessário por meio do Portal do Azure.

1. Selecione a conta de armazenamento de uso geral na qual você deseja que a extensão grave
1. Selecione "Assinatura de acesso compartilhado" na parte de configurações do menu à esquerda
1. Verifique as seções apropriadas conforme descrito anteriormente
1. Clique no botão "Gerar SAS".

![imagem](./media/diagnostics-linux/make_sas.png)

Copie o SAS gerado no campo storageAccountSasToken; remova o ponto de interrogação ("?") do início.

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

Esta seção opcional define os destinos adicionais para os quais a extensão envia as informações coletadas. A matriz "coletor" contém um objeto para cada coletor de dados adicional. O atributo "tipo" determina os outros atributos no objeto.

Elemento | Valor
------- | -----
name | Uma cadeia de caracteres usada para se referir a esse coletor em outro lugar na configuração da extensão.
type | O tipo de coletor que está sendo definido. Determina os outros valores (se houver) em instâncias desse tipo.

A versão 3.0 da Extensão de Diagnóstico do Linux dá suporte a dois tipos de coletores: EventHub e JsonBlob.

#### <a name="the-eventhub-sink"></a>O coletor EventHub

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

A entrada "sasURL" contém a URL completa, incluindo o token de SAS para o Hub de eventos para os quais os dados devem ser publicados. O LAD exige uma SAS uma política de nomeação de SAS que permite a declaração de envio. Um exemplo:

* Criar um namespace de Hubs de Eventos chamado `contosohub`
* Criar um Hub de Eventos no namespace chamado `syslogmsgs`
* Criar uma política de acesso compartilhado no Hub de Eventos chamado `writer` que permite que a declaração de envio

Se você tiver criado uma SAS válida até meia-noite UTC em 1 de janeiro de 2018, o valor de sasURL poderá ser:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Para saber mais sobre como gerar tokens de SAS para Hubs de Eventos, veja [esta página da Web](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>O coletor JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Os dados direcionados para um coletor JsonBlob são armazenados em blobs no armazenamento do Microsoft Azure. Cada instância de LAD cria um blob a cada hora para cada nome de coletor. Cada blob sempre contém uma matriz de objeto JSON sintaticamente válida. Novas entradas são adicionadas atomicamente à matriz. Os BLOBs são armazenados em um contêiner com o mesmo nome que o coletor. As regras de armazenamento do Azure para nomes de contêineres de blob aplicam-se aos nomes dos coletores JsonBlob: entre 3 e 63 caracteres ASCII alfanuméricos minúsculos ou traços.

## <a name="public-settings"></a>Configurações públicas

Essa estrutura contém vários blocos de configurações que controlam as informações coletadas pela extensão. Cada configuração é opcional. Se você especificar `ladCfg`, também deverá especificar `StorageAccount`.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Elemento | Valor
------- | -----
StorageAccount | O nome da conta de armazenamento na qual os dados são gravados pela extensão. Deve ser o mesmo nome, conforme especificado nas [Configurações protegidas](#protected-settings).
mdsdHttpProxy | (opcional) O mesmo que nas [Configurações protegidas](#protected-settings). O valor público é substituído pelo valor particular, se tiver sido definido. Coloque as configurações de proxy que contêm um segredo, como uma senha, nas [Configurações protegidas](#protected-settings).

Os elementos restantes serão descritos em detalhes nas seções a seguir.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Essa estrutura opcional controla a reunião de métricas e logs de entrega para o serviço de Métricas do Azure e outros coletores de dados. Você deve especificar `performanceCounters` ou `syslogEvents`, ou ambos. Você deve especificar a estrutura `metrics`.

Elemento | Valor
------- | -----
eventVolume | (opcional) Controla o número de partições criadas dentro da tabela de armazenamento. Pode ser `"Large"`, `"Medium"` ou `"Small"`. Se esse campo não for especificado, o valor padrão será `"Medium"`.
sampleRateInSeconds | (opcional) O intervalo padrão entre a coleta de métricas brutas (não agregadas). A menor taxa de amostra com suporte é de 15 segundos. Se esse campo não for especificado, o valor padrão será `15`.

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Elemento | Valor
------- | -----
resourceId | A ID de recurso do Azure Resource Manager da VM ou conjunto de dimensionamento de máquinas virtuais à qual pertence a VM. Essa configuração também deverá ser especificada se algum coletor JsonBlob for usado na configuração.
scheduledTransferPeriod | A frequência na qual as métricas agregadas serão computadas e transferidas para as Métricas do Azure, expressas como um intervalo de tempo de IS 8601. O menor período de transferência é 60 segundos, ou seja, PT1M. Você deve especificar pelo menos um scheduledTransferPeriod.

As amostras de métricas especificados na seção performanceCounters são coletados a cada 15 segundos ou na taxa de amostra explicitamente definidas para o contador. Se várias frequências scheduledTransferPeriod aparecerem (como no exemplo), cada agregação será calculada independentemente.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Essa seção opcional controla a coleção de métricas. As amostras brutas são agregadas para cada [scheduledTransferPeriod](#metrics) para produzir esses valores:

* média
* mínimo
* máximo
* valor coletado por último
* contagem de amostras brutas usadas para computar a agregação

Elemento | Valor
------- | -----
coletores | (opcional) Uma lista separada por vírgulas de nomes de coletores para os quais o LAD envia resultados de métricas agregadas. Todas as métricas agregadas são publicadas em cada coletor listado. Veja [sinksConfig](#sinksconfig). Exemplo: `"EHsink1, myjsonsink"`.
type | Identifica o provedor real da métrica.
class | Junto com "counter", identifica a métrica específica dentro do namespace do provedor.
contador | Junto com "class", identifica a métrica específica dentro do namespace do provedor.
counterSpecifier | Identifica a métrica específica dentro do namespace de Métricas do Azure.
condition | (opcional) Seleciona uma instância específica do objeto ao qual a métrica se aplica ou seleciona a agregação em todas as instâncias desse objeto. Para saber mais, confira as definições de métricas `builtin`.
sampleRate | O intervalo IS 8601 que define a taxa na qual as amostras brutas para esta métrica são coletados. Se não estiver definido, o intervalo de coleta será definido pelo valor de [sampleRateInSeconds](#ladcfg). A menor taxa de amostra com suporte é de 15 segundos (PT15S).
unit | Deve ser uma destas cadeias de caracteres: "Count", "Bytes", "Seconds", "Percent", "CountPerSecond", "BytesPerSecond", "Millisecond". Define a unidade para a métrica. Os consumidores dos dados coletados esperam que os valores de dados coletados correspondam a essa unidade. O LAD ignora esse campo.
displayName | O rótulo (no idioma especificado pela configuração da localidade associada) a ser anexado a esses dados nas Métricas do Azure. O LAD ignora esse campo.

O counterSpecifier é um identificador arbitrário. Os consumidores de métricas, como o gráfico do Portal do Azure e o recurso de alerta, usam o counterSpecifier como a "chave" que identifica uma métrica ou instância de uma métrica. Para as métricas `builtin`, é recomendável usar valores counterSpecifier que começam com `/builtin/`. Se você estiver coletando a instância específica de uma métrica, recomendamos anexar o identificador da instância para o valor de counterSpecifier. Alguns exemplos:

* `/builtin/Processor/PercentIdleTime` – tempo ocioso médio de todas as vCPUs
* `/builtin/Disk/FreeSpace(/mnt)` – espaço livre para o sistema de arquivos /mnt
* `/builtin/Disk/FreeSpace` – espaço livre com a média de todos os sistemas de arquivos montados

Nem o LAD nem o Portal do Azure esperam que o valor counterSpecifier corresponda a qualquer padrão. Seja consistente no modo como você constrói valores counterSpecifier.

Quando você especifica `performanceCounters`, o LAD sempre grava dados em uma tabela no armazenamento do Azure. Você pode ter os mesmos dados gravados em blobs JSON e/ou Hubs de Eventos, mas não é possível desabilitar o armazenamento de dados em uma tabela. Todas as instâncias da extensão do diagnóstico configurado para usar o mesmo nome de conta de armazenamento e ponto de extremidade adicionam suas métricas e seus logs na mesma tabela. Se muitas VMs estiverem gravando na mesma partição de tabela, o Azure poderá limitar as gravações nessa partição. A configuração eventVolume faz as entradas serem distribuídas entre 1 (pequeno), 10 (médio) ou 100 (grande) partições diferentes. Normalmente, "médio" é suficiente para garantir que o tráfego não seja limitado. O recurso de Métricas do Azure do Portal do Azure usa os dados nesta tabela para gerar grafos ou disparar alertas. O nome da tabela é a concatenação dessas cadeias de caracteres:

* `WADMetrics`
* O "scheduledTransferPeriod" para os valores agregados armazenados na tabela
* `P10DV2S`
* Uma data, na forma "AAAAMMDD", que é alterada a cada 10 dias

Os exemplos incluem `WADMetricsPT1HP10DV2S20170410` e `WADMetricsPT1MP10DV2S20170609`.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Essa seção opcional controla a coleção de eventos de log do syslog. Se a seção for omitida, os eventos de syslog não serão capturados.

A coleção syslogEventConfiguration tem uma entrada para cada instalação de syslog de interesse. Se minSeverity for "NENHUM" para um recurso específico, ou se o recurso não aparecer no elemento, nenhum evento desse recurso será capturado.

Elemento | Valor
------- | -----
coletores | Uma lista separada por vírgulas de nomes de coletores nos quais os eventos de log individuais são publicados. Todos os eventos de log correspondentes às restrições em syslogEventConfiguration são publicados em cada coletor listado. Exemplo: "EHforsyslog"
facilityName | Um nome de recurso de syslog (como "LOG\_USER" ou "LOG\_LOCAL0"). Veja a seção "facility" da [página de manual do syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) para obter a lista completa.
minSeverity | Um nível de severidade de syslog (como "LOG\_ERR" ou "LOG\_INFO"). Veja a seção "level" da [página de manual do syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) para obter a lista completa. A extensão de captura eventos enviados para o recurso em ou acima do nível especificado.

Quando você especifica `syslogEvents`, o LAD sempre grava dados em uma tabela no armazenamento do Azure. Você pode ter os mesmos dados gravados em blobs JSON e/ou Hubs de Eventos, mas não é possível desabilitar o armazenamento de dados em uma tabela. O comportamento de particionamento para essa tabela é o mesmo descrito para `performanceCounters`. O nome da tabela é a concatenação dessas cadeias de caracteres:

* `LinuxSyslog`
* Uma data, na forma "AAAAMMDD", que é alterada a cada 10 dias

Os exemplos incluem `LinuxSyslog20170410` e `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Essa seção controla a execução de consultas [OMI](https://github.com/Microsoft/omi) arbitrárias.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Elemento | Valor
------- | -----
namespace | (opcional) O namespace OMI dentro do qual a consulta deve ser executada. Se não for especificado, o valor padrão será "root/scx", implementado pelos [Provedores de várias plataformas do System Center](https://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
query | A consulta OMI a ser executada.
table | (opcional) A tabela de armazenamento do Azure, na conta de armazenamento designada (veja [Configurações protegidas](#protected-settings)).
frequency | (opcional) O número de segundos entre a execução da consulta. O valor padrão é 300 (5 minutos); o valor mínimo é de 15 segundos.
coletores | (opcional) Uma lista separada por vírgulas de nomes de coletores adicionais para os quais os resultados brutos de métricas de amostras devem ser publicados. Nenhuma agregação desses exemplos brutos é calculada pela extensão ou Métricas do Azure.

As informações de "tabela" ou "coletores" ou de ambos devem ser especificadas.

### <a name="filelogs"></a>fileLogs

Controla a captura de arquivos de log. O LAD captura novas linhas de texto, como elas são gravadas no arquivo e as grava em linhas da tabela e/ou qualquer coletor especificado (JsonBlob ou EventHub).

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Elemento | Valor
------- | -----
file | O nome de caminho completo do arquivo de log a ser observado e capturado. O nome do caminho deve nomear um único arquivo; ele não pode nomear um diretório ou conter curingas.
table | (opcional) A tabela de armazenamento do Azure, na conta de armazenamento designada (conforme especificado na configuração protegida), na qual novas linhas depois do "final" do arquivo são gravadas.
coletores | (opcional) Uma lista separada por vírgulas de nomes de coletores adicionais para os quais as linhas de log são enviadas.

As informações de "tabela" ou "coletores" ou de ambos devem ser especificadas.

## <a name="metrics-supported-by-the-builtin-provider"></a>Métricas com suporte do provedor interno

O provedor interno de métricas é uma fonte de métricas mais interessante para um amplo conjunto de usuários. Essas métricas enquadram-se em cinco classes amplas:

* Processador
* Memória
* Rede
* Filesystem
* Disco

### <a name="builtin-metrics-for-the-processor-class"></a>métricas internas para a classe Processor

A classe de métricas Processor fornece informações sobre o uso do processador na VM. Ao agregar porcentagens, o resultado é a média em todas as CPUs. Em uma VM de duas vCPUs, se uma vCPU estiver 100% ocupada e a outra 100% ociosa, o PercentIdleTime relatado será de 50. Se cada vCPU estiver 50% ocupada para o mesmo período, o resultado relatado também será de 50. Em uma VM de vCPUs, com um vCPU 100% ocupada e a outra ociosa, o PercentIdleTime relatado seria de 75.

contador | Significado
------- | -------
PercentIdleTime | Porcentagem de tempo durante a janela de agregação que os processadores estavam executando o loop ocioso do kernel
PercentProcessorTime | Porcentagem de tempo de execução de um thread não ocioso
PercentIOWaitTime | Porcentagem de tempo esperando a conclusão das operações de E/S
PercentInterruptTime | Porcentagem de tempo de execução de interrupções de hardware/software e DPCs (chamadas de procedimento deferidas)
PercentUserTime | De tempo não ocioso durante a janela de agregação, a porcentagem de tempo gasto no usuário mais em prioridade normal
PercentNiceTime | De tempo não ocioso, a porcentagem gasta em prioridade diminuída (boa)
PercentPrivilegedTime | De tempo não ocioso, a porcentagem gasta em modo privilegiado (kernel)

Os primeiros quatro contadores devem somar 100%. Os três últimos contadores também somam 100%; eles subdividem a soma de PercentProcessorTime, PercentIOWaitTime e PercentInterruptTime.

Para obter uma única métrica agregada em todos os processadores, defina `"condition": "IsAggregate=TRUE"`. Para obter uma métrica para um processador específico, como o segundo processador lógico de uma VM de quatro vCPUs, defina `"condition": "Name=\\"1\\""`. Os números de processadores lógicos estão no intervalo `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>métricas internas para a classe Memory

A classe de métricas Memory fornece informações sobre a utilização de memória, paginação e troca.

contador | Significado
------- | -------
AvailableMemory | Memória física disponível em MiB
PercentAvailableMemory | Memória física disponível como uma porcentagem da memória total
UsedMemory | Memória física em uso (MiB)
PercentUsedMemory | Memória física em uso como uma porcentagem da memória total
PagesPerSec | Paginação total (leitura/gravação)
PagesReadPerSec | Páginas lidas do repositório de backup (arquivo de permuta, arquivo de programa, arquivo mapeado etc.)
PagesWrittenPerSec | Páginas gravadas no armazenamento de backup (arquivo de permuta, arquivo mapeado etc.)
AvailableSwap | Espaço de permuta não utilizado (MiB)
PercentAvailableSwap | Espaço de troca não utilizado como uma porcentagem da troca total
UsedSwap | Espaço de troca em uso (MiB)
PercentUsedSwap | Espaço de troca em uso como uma porcentagem da troca total

Essa classe de métricas tem apenas uma única instância. O atributo "condição" não tem configurações úteis e deve ser omitido.

### <a name="builtin-metrics-for-the-network-class"></a>métricas internas para a classe Network

A classe de métricas Network fornece informações sobre a atividade de rede em adaptadores de rede individuais desde a inicialização. O LAD não expõe as métricas de largura de banda, que podem ser recuperadas de métricas de host.

contador | Significado
------- | -------
BytesTransmitted | Total de bytes enviados desde a inicialização
BytesReceived | Total de bytes recebidos desde a inicialização
BytesTotal | Total de bytes enviados ou recebidos desde a inicialização
PacketsTransmitted | Total de pacotes enviados desde a inicialização
PacketsReceived | Total de pacotes recebidos desde a inicialização
TotalRxErrors | Número de erros de recebimento desde a inicialização
TotalTxErrors | Número de erros de transmissão desde a inicialização
TotalCollisions | Número de colisões relatadas pelas portas de rede desde a inicialização

 Embora essa classe seja instanciada, o LAD não oferece suporte à captura de métricas Network agregadas em todos os dispositivos de rede. Para obter a métrica para uma interface específica, como eth0, defina `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>métricas internas para a classe Filesystem

A classe de métricas Filesystem fornece informações sobre o uso do sistema de arquivos. Valores absolutos e porcentagem são relatados como seriam exibidos para um usuário comum (não raiz).

contador | Significado
------- | -------
FreeSpace | Espaço em disco disponível em bytes
UsedSpace | Espaço em disco usado em bytes
PercentFreeSpace | Porcentagem de espaço livre
PercentUsedSpace | Porcentagem de espaço usado
PercentFreeInodes | Porcentagem de inodes não utilizados
PercentUsedInodes | Porcentagem de inodes alocados (em uso) somados em todos os sistemas de arquivos
BytesReadPerSecond | Bytes lidos por segundo
BytesWrittenPerSecond | Bytes gravados por segundo
BytesPerSecond | Bytes lido ou gravados por segundo
ReadsPerSecond | Operações de leitura por segundo
WritesPerSecond | Operações de gravação por segundo
TransfersPerSecond | Operações de leitura ou gravação por segundo

Os valores agregados em todos os sistemas de arquivo podem ser obtidos pela configuração `"condition": "IsAggregate=True"`. Os valores para um sistema de arquivos montado específico, como "/mnt", podem ser obtidos pela configuração `"condition": 'Name="/mnt"'`. 

**OBSERVAÇÃO**: Se estiver usando o portal do Azure em vez de JSON, o formulário de campo de condição correto será Name = '/mnt '

### <a name="builtin-metrics-for-the-disk-class"></a>métricas internas para a classe Disk

A classe de métricas Disk fornece informações sobre o uso do dispositivo de disco. Essas estatísticas aplicam-se a toda a unidade. Se houver vários sistemas de arquivos em um dispositivo, os contadores do dispositivo serão, na verdade, agregados em todos eles.

contador | Significado
------- | -------
ReadsPerSecond | Operações de leitura por segundo
WritesPerSecond | Operações de gravação por segundo
TransfersPerSecond | Operações totais por segundo
AverageReadTime | Média de segundos por operação de leitura
AverageWriteTime | Média de segundos por operação de gravação
AverageTransferTime | Média de segundos por operação
AverageDiskQueueLength | Número médio de operações de disco enfileiradas
ReadBytesPerSecond | Número de bytes lidos por segundo
WriteBytesPerSecond | Número de bytes gravados por segundo
BytesPerSecond | Número de bytes lidos ou gravados por segundo

Os valores agregados em todos os discos podem ser obtidos pela configuração `"condition": "IsAggregate=True"`. Para saber mais para um dispositivo específico (por exemplo, /dev/sdf1), defina `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Instalação e configuração do LAD 3.0 via CLI

Supondo que as configurações protegidas estejam no arquivo PrivateConfig.json e suas informações de configuração pública estejam em PublicConfig.json, execute este comando:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

O comando supõe que você esteja usando o modo de Gerenciamento de Recursos do Azure (arm) da CLI do Azure. Para configurar o LAD para as VMs do modelo de implantação clássico (ASM), alterne para o modo "asm" (`azure config mode asm`) e omita o nome do grupo de recursos no comando. Para saber mais, confira a [documentação da CLI entre plataformas](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Uma configuração de exemplo do LAD 3.0

Com base nas definições anteriores, aqui está uma configuração de extensão de amostra do LAD 3.0 com alguma explicação. Para aplicar este exemplo ao seu caso, você deverá usar seu próprio nome da conta de armazenamento, token de SAS de conta e tokens de SAS de EventHubs.

### <a name="privateconfigjson"></a>PrivateConfig.json

Essas configurações privadas definem os parâmetros de:

* uma conta de armazenamento
* um token de SAS de conta correspondente
* vários coletores (JsonBlob ou EventHubs com tokens de SAS)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="publicconfigjson"></a>PublicConfig.json

Essas configurações públicas fazem o LAD:

* Carregar as métricas de porcentagem de tempo de processador e espaço em disco usado para a tabela `WADMetrics*`
* Carregar as mensagens do recurso do syslog "user" e gravidade "info" para a tabela `LinuxSyslog*`
* Carregar resultados de consulta brutos de OMI (PercentProcessorTime e PercentIdleTime) para a tabela nomeada `LinuxCPU`
* Carregar as linhas acrescentadas no arquivo `/var/log/myladtestlog` para a tabela `MyLadTestLog`

Em cada caso, os dados também são carregados para:

* O armazenamento de Blobs do Azure (o nome do contêiner é o definido no coletor JsonBlob)
* Ponto de extremidade de EventHubs (conforme especificado no coletor EventHubs)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

O `resourceId` na configuração deve corresponder à da máquina virtual ou conjunto de dimensionamento de máquinas virtuais.

* Os gráficos e os alertas das métricas da plataforma Azure conhecem o resourceId da VM em que você está trabalhando. Ele espera localizar os dados para sua VM usando a chave de pesquisa do resourceId.
* Se você usar o dimensionamento automático do Azure, o resourceId na configuração do dimensionamento automático deverá corresponder ao resourceId usado pelo LAD.
* O resourceId está integrado nos nomes de JsonBlobs escritos pelo LAD.

## <a name="view-your-data"></a>Ver seus dados

Use o Portal do Azure para exibir dados de desempenho ou definir alertas:

![imagem](./media/diagnostics-linux/graph_metrics.png)

Os dados de `performanceCounters` são sempre armazenados em uma tabela de Armazenamento do Azure. As APIs do Armazenamento do Azure estão disponíveis em várias linguagens e plataformas.

Os dados enviados para coletores JsonBlob são armazenados nos blobs na conta de armazenamento nomeada nas [Configurações protegidas](#protected-settings). Você pode consumir os dados do blob usando qualquer API de Armazenamento de Blobs do Azure.

Além disso, você pode usar essas ferramentas de interface do usuário para acessar os dados no Armazenamento do Azure:

* Gerenciador de Servidores do Visual Studio.
* [Gerenciador de Armazenamento do Microsoft Azure](https://azurestorageexplorer.codeplex.com/ "Gerenciador de Armazenamento do Azure").

Esse instantâneo de uma sessão do Gerenciador de Armazenamento do Microsoft Azure mostra as tabelas do Armazenamento do Azure geradas e os contêineres de uma extensão de LAD 3.0 configurada corretamente em uma VM de teste. A imagem não coincide exatamente com a [configuração de amostra do LAD 3.0](#an-example-lad-30-configuration).

![imagem](./media/diagnostics-linux/stg_explorer.png)

Consulte a [Documentação de EventHubs](../../event-hubs/event-hubs-what-is-event-hubs.md) correspondente para aprender a consumir mensagens publicadas em um ponto de extremidade de EventHubs.

## <a name="next-steps"></a>Próximas etapas

* Criar alertas de métricas no [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) para as métricas que você coletar.
* Criar [gráficos de monitoramento](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para suas métricas.
* Aprenda a [criar um conjunto de dimensionamento de máquinas virtuais](../linux/tutorial-create-vmss.md) usando suas métricas para controlar o dimensionamento automático.
