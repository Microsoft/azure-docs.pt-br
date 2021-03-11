---
title: Computação do Azure-extensão de diagnóstico do Linux 4,0
description: Como configurar a extensão de diagnóstico Linux do Azure (LAD) 4,0 para coletar métricas e eventos de log de VMs Linux em execução no Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/05/2021
ms.openlocfilehash: 4c4851ab28e5da74e7f1fa36f087ecfdabb1c638
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560116"
---
# <a name="use-linux-diagnostic-extension-40-to-monitor-metrics-and-logs"></a>Usar a extensão de diagnóstico do Linux 4,0 para monitorar as métricas e os logs

Este documento descreve a versão 4,0 e mais recente da extensão de diagnóstico do Linux.

> [!IMPORTANT]
> Para obter informações sobre a versão 3. *, consulte  [este documento](./diagnostics-linux-v3.md). Para saber mais sobre a versão 2.3 e anteriores, veja [este documento](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

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

>[!NOTE]
>Determinados componentes da extensão de VM de diagnóstico também são fornecidos na [extensão de vm log Analytics](./oms-linux.md). Devido a essa arquitetura, os conflitos podem surgir se ambas as extensões forem instanciadas no mesmo modelo do ARM. Para evitar esses conflitos de tempo de instalação, use a [ `dependsOn` diretiva](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) para garantir que as extensões sejam instaladas sequencialmente. As extensões podem ser instaladas em qualquer ordem.

Essas instruções de instalação e uma [configuração de exemplo baixável](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) configuram o Lad 4,0 para:

* Capture e armazene as mesmas métricas fornecidas pelo LAD 2,3, 3 *;
* Envie métricas para Azure Monitor coletor juntamente com o coletor usual para o armazenamento do Azure, novo no Lad 4,0
* Capture um conjunto útil de métricas do sistema de arquivos, como foi fornecido pelo LAD 3,0;
* capturar a coleção de syslog padrão habilitada pelo LAD 2.3;
* habilitar a experiência do Portal do Azure para gráficos e criação de alertas nas métricas da VM.

A configuração para download é apenas um exemplo; modifique-a para atender às suas necessidades.

### <a name="supported-linux-distributions"></a>Distribuições Linux compatíveis

A extensão de diagnóstico do Linux é compatível com as distribuições e versões a seguir. A lista de distribuições e versões aplica-se somente a imagens de fornecedor do Linux endossadas pelo Azure. Imagens BYOL e BYOS de terceiros, como dispositivos, geralmente não são compatíveis com a Extensão de Diagnóstico do Linux.

Uma distribuição que lista somente as versões principais, como Debian 7, também é compatível com todas as versões secundárias. Se uma versão secundária específica for especificada, somente essa versão específica será compatível; se "+" for anexado, as versões secundárias iguais ou posteriores à versão especificada serão compatíveis.

Distribuições e versões compatíveis:

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- RHEL 7, 6.7+

### <a name="prerequisites"></a>Pré-requisitos

* **Agente Linux do Azure versão 2.2.0 ou posterior**. A maioria das imagens de galeria da VM Linux do Azure inclui a versão 2.2.7 ou posterior. Execute `/usr/sbin/waagent -version` para confirmar a versão instalada na VM. Se a VM estiver executando uma versão mais antiga do agente convidado, execute [estas instruções](./update-linux-agent.md) para atualizá-la.
* **CLI do Azure**. [Configurar o ambiente da CLI do Azure](/cli/azure/install-azure-cli) em seu computador.
* O comando wget, caso ainda não o tenha: Execute `sudo apt-get install wget`.
* Uma assinatura do Azure existente e uma conta de armazenamento de uso geral existente para armazenar os dados no.  As contas de armazenamento de uso geral dão suporte ao armazenamento de tabela que é necessário.  Uma conta de armazenamento de BLOBs não funcionará.
* Python 2

### <a name="python-requirement"></a>Requisito do Python

A extensão de diagnóstico do Linux requer Python 2. Se sua máquina virtual estiver usando um distribuição que não inclua o Python 2 por padrão, você deverá instalá-lo. Os comandos de exemplo a seguir instalarão o Python 2 em distribuições diferentes.    

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

O executável python2 deve ter um alias para *Python*. A seguir, um método que você pode usar para definir este alias:

1. Execute o comando a seguir para remover todos os aliases existentes.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Execute o comando a seguir para criar o alias.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Instalação de exemplo

> [!NOTE]
> Para qualquer um dos exemplos, preencha os valores corretos para as variáveis na primeira seção antes de executar. 

A configuração de exemplo baixada nesses exemplos coleta um conjunto de dados padrão e os envia para o Armazenamento de Tabelas. A URL para a configuração de exemplo e o respectivo conteúdo estão sujeitos a alterações. Na maioria dos casos, você deve baixar uma cópia do arquivo JSON de configurações do portal e personalizá-lo para suas necessidades e, em seguida, fazer com que qualquer modelo ou automação que você construa use sua versão do arquivo de configuração, em vez de baixar essa URL a cada vez.

> [!NOTE]
> Para habilitar o novo coletor de Azure Monitor, as VMs precisam ter a identidade atribuída pelo sistema habilitada para a geração de token de autenticação do MSI. Isso pode ser feito durante a criação da VM ou após a criação da VM. Etapas para habilitar a identidade atribuída pelo sistema por meio do portal, da CLI, do PowerShell e do Resource Manager.  são listados detalhadamente [aqui](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md). 

#### <a name="azure-cli-sample"></a>Exemplo de CLI do Azure

```azurecli
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Enable System Assigned Identity to the existing VM
az vm identity assign -g $my_resource_group -n $my_linux_vm

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-40-extension-on-the-virtual-machine-scale-set-instance"></a>CLI do Azure exemplo para instalar a extensão LAD 4,0 na instância do conjunto de dimensionamento de máquinas virtuais

```azurecli
#Set your Azure VMSS diagnostic variables correctly below
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Enable System Assigned Identity to the existing VMSS
az vmss identity assign -g $my_resource_group -n $my_linux_vmss

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VMSS resource ID. Replace storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>Exemplo do PowerShell

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Enable System Assigned Identity on an existing VM
Update-AzVM -ResourceGroupName $VMresourceGroup -VM $vm -IdentityType SystemAssigned

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0 
```

### <a name="updating-the-extension-settings"></a>Atualização das configurações de extensão

Depois que você tiver alterado as configurações Públicas ou Protegidas, implante-as na VM executando o mesmo comando. Se algo for alterado nas configurações, as configurações atualizadas serão enviadas para a extensão. O LAD recarrega a configuração e é reiniciado.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migração de versões anteriores da extensão

A versão mais recente da extensão é **4,0, que está atualmente em visualização pública**. **As versões mais antigas do 3. x ainda têm suporte enquanto as versões de 2. x são preteridas desde 31 de julho de 2018**.

> [!IMPORTANT]
> Para migrar do 3. x para essa nova versão da extensão, você deve desinstalar a extensão antiga e, em seguida, instalar a versão 4 da extensão (com a configuração atualizada para a identidade atribuída pelo sistema e coletores para enviar métricas para Azure Monitor coletor.)

Recomendações:

* Instale a extensão com a atualização de versão secundária automática habilitada.
  * Em VMs do modelo de implantação clássica, especifique ' 4. * ' como a versão se você estiver instalando a extensão por meio da CLI do Azure XPLAT ou do PowerShell.
  * Nas VMs do Modelo de implantação do Azure Resource Manager, inclua '"autoUpgradeMinorVersion": true' no modelo de implantação da VM.
* Pode usar a mesma conta de armazenamento para LAD 4,0 como com o LAD 3. *. 

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

Para obter um token SAS dentro de um modelo do Resource Manager, use a função **listAccountSas**. Para obter um modelo de exemplo, confira [Exemplo da função de lista](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Você pode facilmente construir o token de SAS necessário por meio do Portal do Azure.

1. Selecione a conta de armazenamento de uso geral na qual você deseja que a extensão grave
1. Selecione "Assinatura de acesso compartilhado" na parte de configurações do menu à esquerda
1. Verifique as seções apropriadas conforme descrito anteriormente
1. Clique no botão "Gerar SAS".

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Captura de tela mostra a página de assinatura de acesso compartilhado com gerar S A S.":::

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

A versão 4,0 da extensão de diagnóstico do Linux dá suporte a dois tipos de coletor: EventHub e JsonBlob.

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

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Para saber mais sobre como gerar e recuperar informações sobre tokens SAS para Hubs de Eventos, confira [esta página da Web](/rest/api/eventhub/generate-sas-token#powershell).

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

Essa estrutura contém vários blocos de configurações que controlam as informações coletadas pela extensão. Cada configuração (exceto ladCfg) é opcional. Se você especificar a coleta de métrica ou de syslog no `ladCfg` , também deverá especificar `StorageAccount` . o elemento sinksConfig precisa ser especificado para habilitar o coletor de Azure Monitor para métricas de LAD 4,0

```json
{
    "ladCfg":  { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "sinksConfig": { ... },
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

Essa estrutura controla a coleta de métricas e logs para entrega ao serviço de métricas do Azure e a outros coletores de dados. Você deve especificar `performanceCounters` ou `syslogEvents`, ou ambos. Você deve especificar a estrutura `metrics`.

Se você não quiser habilitar o syslog ou a coleção de métricas, poderá simplesmente especificar uma estrutura vazia para o elemento ladCfg, conforme mostrado abaixo- 

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {}
    }
```

Elemento | Valor
------- | -----
eventVolume | (opcional) Controla o número de partições criadas dentro da tabela de armazenamento. Pode ser `"Large"`, `"Medium"` ou `"Small"`. Se esse campo não for especificado, o valor padrão será `"Medium"`.
sampleRateInSeconds | (opcional) O intervalo padrão entre a coleta de métricas brutas (não agregadas). A menor taxa de amostra com suporte é de 15 segundos. Se esse campo não for especificado, o valor padrão será `15`.

#### <a name="metrics"></a>Métricas

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

### <a name="sinksconfig"></a>sinksConfig

Essa seção opcional controla a habilitação de métricas de envio para o coletor de Azure Monitor além da conta de armazenamento e da folha de métricas de convidado padrão.

> [!NOTE]
> Isso requer que a identidade atribuída pelo sistema esteja habilitada nas VMs/VMSS. Isso pode ser feito por meio do portal, da CLI, do PowerShell e do Resource Manager. As etapas são listadas em detalhes [aqui](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md). As etapas para habilitar isso também estão listadas nos exemplos de instalação para AZ CLI, PowerShell, etc. acima. 

```json
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
```


### <a name="filelogs"></a>fileLogs

Controla a captura de arquivos de log. O LAD captura novas linhas de texto, como elas são gravadas no arquivo e as grava em linhas da tabela e/ou qualquer coletor especificado (JsonBlob ou EventHub).

> [!NOTE]
> Os logs de filesão capturados por um subcomponente de LAD chamado `omsagent` . Para coletar filelogs, você deve garantir que o `omsagent` usuário tenha permissões de leitura nos arquivos especificados, bem como permissões de execução em todos os diretórios no caminho para esse arquivo. Você pode verificar isso executando `sudo su omsagent -c 'cat /path/to/file'` após a instalação do Lad.

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
file | O nome de caminho completo do arquivo de log a ser observado e capturado. O nome do caminho deve nomear um único arquivo; ele não pode nomear um diretório ou conter curingas. A conta de usuário ' omsagent ' deve ter acesso de leitura ao caminho do arquivo.
tabela | (opcional) A tabela de armazenamento do Azure, na conta de armazenamento designada (conforme especificado na configuração protegida), na qual novas linhas depois do "final" do arquivo são gravadas.
coletores | (opcional) Uma lista separada por vírgulas de nomes de coletores adicionais para os quais as linhas de log são enviadas.

As informações de "tabela" ou "coletores" ou de ambos devem ser especificadas.

## <a name="metrics-supported-by-the-builtin-provider"></a>Métricas com suporte do provedor interno

> [!NOTE]
> As métricas padrão com suporte de LAD são agregadas em todos os sistemas de arquivos/discos/nome. Para métricas não agregadas, consulte o suporte de métricas do coletor de Azure Monitor mais recente.

O provedor interno de métricas é uma fonte de métricas mais interessante para um amplo conjunto de usuários. Essas métricas enquadram-se em cinco classes amplas:

* Processador
* Memória
* Rede
* Sistema de arquivos
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

A classe de métricas de rede fornece informações sobre a atividade de rede em uma interface de rede individual desde a inicialização. O LAD não expõe as métricas de largura de banda, que podem ser recuperadas de métricas de host.

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

## <a name="installing-and-configuring-lad-40"></a>Instalando e Configurando o LAD 4,0

### <a name="azure-cli"></a>CLI do Azure

Supondo que as configurações protegidas estejam no arquivo ProtectedSettings.jsem e suas informações de configuração pública estejam em PublicSettings.jsem, execute este comando:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

O comando pressupõe que você esteja usando o modo de gerenciamento de recursos do Azure da CLI do Azure. Para configurar o LAD para as VMs do modelo de implantação clássico (ASM), alterne para o modo "asm" (`azure config mode asm`) e omita o nome do grupo de recursos no comando. Para saber mais, confira a [documentação da CLI entre plataformas](/cli/azure/authenticate-azure-cli).

### <a name="powershell"></a>PowerShell

Supondo que as configurações protegidas estejam na `$protectedSettings` variável e que suas informações de configuração pública estejam na `$publicSettings` variável, execute este comando:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0
```

## <a name="an-example-lad-40-configuration"></a>Um exemplo de configuração do LAD 4,0

Com base nas definições anteriores, aqui está um exemplo de configuração de extensão do LAD 4,0 com alguma explicação. Para aplicar este exemplo ao seu caso, você deverá usar seu próprio nome da conta de armazenamento, token de SAS de conta e tokens de SAS de EventHubs.

> [!NOTE]
> Dependendo se você usar o CLI do Azure ou o PowerShell para instalar o LAD, o método para fornecer configurações públicas e protegidas será diferente. Se estiver usando o CLI do Azure, salve as configurações a seguir para ProtectedSettings.jse PublicSettings.jsem para usar com o comando de exemplo acima. Se estiver usando o PowerShell, salve as configurações em `$protectedSettings` e `$publicSettings` executando `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Configurações protegidas

Essas configurações protegidas são configuradas:

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

### <a name="public-settings"></a>Configurações públicas

Essas configurações públicas fazem o LAD:

* Carregar as métricas de porcentagem de tempo de processador e espaço em disco usado para a tabela `WADMetrics*`
* Carregar as mensagens do recurso do syslog "user" e gravidade "info" para a tabela `LinuxSyslog*`
* Carregar as linhas acrescentadas no arquivo `/var/log/myladtestlog` para a tabela `MyLadTestLog`

Em cada caso, os dados também são carregados para:

* O armazenamento de Blobs do Azure (o nome do contêiner é o definido no coletor JsonBlob)
* Ponto de extremidade de EventHubs (conforme especificado no coletor EventHubs)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
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
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
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

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Captura de tela mostra a portal do Azure com o espaço em disco usado na métrica selecionada e o gráfico resultante.":::

Os dados de `performanceCounters` são sempre armazenados em uma tabela de Armazenamento do Azure. As APIs do Armazenamento do Azure estão disponíveis em várias linguagens e plataformas.

Os dados enviados para coletores JsonBlob são armazenados nos blobs na conta de armazenamento nomeada nas [Configurações protegidas](#protected-settings). Você pode consumir os dados do blob usando qualquer API de Armazenamento de Blobs do Azure.

Além disso, você pode usar essas ferramentas de interface do usuário para acessar os dados no Armazenamento do Azure:

* Gerenciador de Servidores do Visual Studio.
* [Captura de tela mostra contêineres e tabelas em Gerenciador de armazenamento do Azure.](https://azurestorageexplorer.codeplex.com/ "Gerenciador de Armazenamento do Azure").

Esse instantâneo de uma sessão do Gerenciador de Armazenamento do Microsoft Azure mostra as tabelas do Armazenamento do Azure geradas e os contêineres de uma extensão de LAD 3.0 configurada corretamente em uma VM de teste. A imagem não coincide exatamente com a [configuração de amostra do LAD 3.0](#an-example-lad-40-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Captura de tela mostra a Gerenciador de Armazenamento do Azure.":::

Consulte a [Documentação de EventHubs](../../event-hubs/event-hubs-about.md) correspondente para aprender a consumir mensagens publicadas em um ponto de extremidade de EventHubs.

## <a name="next-steps"></a>Próximas etapas

* Criar alertas de métricas no [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md) para as métricas que você coletar.
* Criar [gráficos de monitoramento](../../azure-monitor/data-platform.md) para suas métricas.
* Aprenda a [criar um conjunto de dimensionamento de máquinas virtuais](../linux/tutorial-create-vmss.md) usando suas métricas para controlar o dimensionamento automático.