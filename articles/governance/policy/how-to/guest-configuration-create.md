---
title: Como criar políticas de Configuração de Convidado para o Windows
description: Saiba como criar uma política de Configuração de Convidado do Azure Policy para Windows.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: ae9af51ad3b2eb237f8655c996a1345140a8a635
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070637"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Como criar políticas de Configuração de Convidado para o Windows

Antes de criar definições de política personalizadas, é uma boa ideia ler as informações de visão geral conceitual na página [Configuração de Convidado do Azure Policy](../concepts/guest-configuration.md).
 
Para saber mais sobre como criar políticas de Configuração de Convidado para o Linux, confira a página [Como criar políticas de Configuração de Convidado para o Linux](./guest-configuration-create-linux.md)

Ao auditar o Windows, a Configuração de Convidado usa um módulo de recurso [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) para criar o arquivo de configuração. A configuração DSC define a condição em que o computador deve estar. Se a avaliação da configuração falhar, o efeito da política **auditIfNotExists** será disparado e o computador será considerado **não compatível**.

A [Configuração de Convidado do Azure Policy](../concepts/guest-configuration.md) só pode ser usada para auditar configurações dentro de computadores. A correção das configurações dentro de computadores ainda não está disponível.

Use as ações a seguir para criar sua própria configuração para validar o estado de um computador que tem ou não o Azure.

> [!IMPORTANT]
> As definições de política personalizadas com a configuração de convidado no Azure governamental e nos ambientes do Azure China são um recurso de visualização.
>
> A extensão de Configuração de Convidado é necessária para executar auditorias em máquinas virtuais do Azure.
> Para implantar a extensão em escala em todas as máquinas com Windows, atribua as seguintes definições de política: `Deploy prerequisites to enable Guest Configuration Policy on Windows VMs`
> 
> Não use informações confidenciais ou segredos em pacotes de conteúdo personalizados.

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

O módulo de Configuração de Convidado automatiza o processo de criação de conteúdo personalizado, incluindo:

- A criação de um artefato de conteúdo de Configuração de Convidado (.zip)
- O teste automatizado do artefato
- A criação de uma definição de política
- A publicação da política

O módulo pode ser instalado em um computador que execute Windows, macOS ou Linux e que tenha o PowerShell 6.2 ou posterior em execução localmente, ou o [Azure Cloud Shell](https://shell.azure.com) ou a [imagem do Core Docker do Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Ainda não há suporte para a compilação de configurações no Linux.

### <a name="base-requirements"></a>Requisitos base

Sistemas operacionais em que é possível instalar o módulo:

- Linux
- macOS
- Windows

O módulo do recurso de Configuração de Convidado requer o seguinte software:

- PowerShell 6.2 ou posterior. Se ainda não estiver instalado, siga [estas instruções](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 ou superior. Se ainda não estiver instalado, siga [estas instruções](/powershell/azure/install-az-ps).
  - Somente os módulos AZ ' AZ. Accounts ' e ' AZ. Resources ' são necessários.

### <a name="install-the-module"></a>Instalar o módulo

Para instalar o módulo **GuestConfiguration** no PowerShell:

1. Em um prompt do PowerShell, execute o seguinte comando:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Confirme que o módulo foi importado:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Artefatos e política de Configuração de Convidado para Windows

A Configuração de Convidado usa a Desired State Configuration do PowerShell como uma abstração de linguagem para gravar o que auditar no Windows. O agente carrega uma instância autônoma do PowerShell 6.2, portanto, não há conflito com o uso do PowerShell DSC no Windows PowerShell 5.1 e não é necessário pré-instalar o PowerShell 6.2 ou posterior.

Para obter uma visão geral dos conceitos e terminologia da DSC, confira [Visão geral da DSC do PowerShell](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Como os módulos de Configuração de Convidado diferem dos módulos da DSC do Windows PowerShell

Quando a configuração de convidado audita um computador, a sequência de eventos é diferente da DSC do Windows PowerShell.

1. O agente primeiro executa `Test-TargetResource` para determinar se a configuração está no estado correto.
1. O valor booliano retornado pela função determina se o status do Azure Resource Manager para a Atribuição de Convidado deve ser compatível/não compatível.
1. O provedor executa `Get-TargetResource` para retornar o estado atual de cada configuração, para que estejam disponíveis detalhes sobre o motivo pelo qual um computador não é compatível e para confirmar que o estado atual é compatível.

Os parâmetros em Azure Policy que passam valores para atribuições de configuração de convidado devem ser do tipo _cadeia de caracteres_ . Não é possível passar matrizes por meio de parâmetros, mesmo que o recurso de DSC dê suporte a matrizes.

### <a name="get-targetresource-requirements"></a>Requisitos da função Get-TargetResource

A função `Get-TargetResource` tem requisitos especiais para a Configuração de Convidado que não foram necessários para a Desired State Configuration do Windows.

- A tabela de hash retornada precisa incluir uma propriedade chamada **Motivos**.
- A propriedade Motivos deve ser uma matriz.
- Cada item da matriz deve ser uma tabela de hash com chaves denominadas **Code** e **Phrase**.

A propriedade Motivos é usada pelo serviço para padronizar como as informações são apresentadas quando um computador está fora de conformidade. Você pode pensar em cada item em Motivos como um "motivo" pelo qual o recurso não é compatível. A propriedade é uma matriz porque um recurso pode estar fora de conformidade por mais de um motivo.

As propriedades **Code** e **Phrase** são esperadas pelo serviço. Ao criar um recurso personalizado, defina o texto (normalmente stdout) que você gostaria de mostrar como o motivo pelo qual o recurso não é compatível como o valor para **Frase**. **Code** tem requisitos de formatação específicos para que os relatórios possam exibir claramente informações sobre o recurso usado para fazer a auditoria. Essa solução torna a Configuração de Convidado extensível. Qualquer comando pode ser executado desde que a saída possa ser retornada como um valor de cadeia de caracteres para a propriedade **Frase**.

- **Code** (cadeia de caracteres): O nome do recurso, repetido e, em seguida, um nome curto sem espaços como um identificador do motivo. Esses três valores devem ser delimitados por dois pontos sem espaços.
  - Um exemplo seria `registry:registry:keynotpresent`
- **Phrase** (cadeia de caracteres): Texto legível por humanos para explicar por que a configuração não é compatível.
  - Um exemplo seria `The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

A propriedade Reasons deve ser adicionada ao Schema MOF para o recurso como uma classe incorporada.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

Se o recurso tiver propriedades obrigatórias, elas também deverão ser retornadas `Get-TargetResource` em paralelo com a `reasons` classe. Se `reasons` não estiver incluído, o serviço incluirá um comportamento "capturar tudo" que compara a entrada de valores para `Get-TargetResource` e os valores retornados por e `Get-TargetResource` fornece uma comparação detalhada como `reasons` .

### <a name="configuration-requirements"></a>Requisitos de configuração

O nome da configuração personalizada deve ser o mesmo em todos os lugares. O nome do arquivo. zip para o pacote de conteúdo, o nome da configuração no arquivo MOF e o nome da atribuição de convidado no modelo de Azure Resource Manager (modelo ARM), deve ser o mesmo.

### <a name="policy-requirements"></a>Requisitos de política

A seção de definição de política `metadata` deve incluir duas propriedades para o serviço de configuração de convidado para automatizar o provisionamento e a emissão de relatórios de atribuições de configuração de convidado. A `category` propriedade deve ser definida como "configuração de convidado" e uma seção chamada `Guest Configuration` deve conter informações sobre a atribuição de configuração de convidado. O `New-GuestConfigurationPolicy` cmdlet cria esse texto automaticamente.
Consulte as instruções detalhadas nesta página.

O exemplo a seguir demonstra a `metadata` seção.

```json
    "metadata": {
      "category": "Guest Configuration",
      "guestConfiguration": {
        "name": "test",
        "version": "1.0.0",
        "contentType": "Custom",
        "contentUri": "CUSTOM-URI-HERE",
        "contentHash": "CUSTOM-HASH-VALUE-HERE",
        "configurationParameter": {}
      }
    },
```

### <a name="scaffolding-a-guest-configuration-project"></a>Processo de scaffolding em um projeto de Configuração de Convidado

Os desenvolvedores que desejam acelerar o processo de introdução e trabalhar com o código de exemplo podem instalar um projeto da comunidade chamado **Projeto de Configuração de Convidado**. O projeto instala um modelo para o módulo [Plaster](https://github.com/powershell/plaster) do PowerShell. Essa ferramenta pode ser usada para monitorar um projeto, incluindo uma configuração funcional e um recurso de amostra, e um conjunto de testes [Pester](https://github.com/pester/pester) para validar o projeto. O modelo também inclui executores de tarefas para Visual Studio Code para automatizar a criação e a validação do pacote de Configuração de Convidado. Para saber mais, confira o projeto GitHub [Projeto de Configuração de Convidado](https://github.com/microsoft/guestconfigurationproject).

Para saber mais sobre como trabalhar com configurações em geral, confira [Gravar, compilar e aplicar uma configuração](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Conteúdo esperado de um artefato de Configuração de Convidado

O pacote fechado é usado pela Configuração de Convidado para criar as definições do Azure Policy. O pacote consiste em:

- A configuração de DSC compilada como um MOF
- Pasta dos módulos
  - Módulo GuestConfiguration
  - Módulo DscNativeResources
  - Módulos de recursos DSC (Windows) exigidos pelo MOF

Os cmdlets do PowerShell auxiliam na criação do pacote.
Nenhuma pasta de nível de raiz ou de versão é necessária.
O formato do pacote deve ser um arquivo. zip e não pode exceder um tamanho total de 100 MB quando não compactado.

### <a name="storing-guest-configuration-artifacts"></a>Armazenar artefatos de Configuração de Convidado

O pacote .zip deve ser armazenado em um local acessível pelas máquinas virtuais gerenciadas.
Exemplos incluem repositórios do GitHub, um repositório do Azure ou armazenamento do Azure. Se você preferir não tornar o pacote público, poderá incluir um [token SAS](../../../storage/common/storage-sas-overview.md) na URL. Você também pode implementar o [ponto de extremidade de serviço](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) para máquinas em uma rede privada, embora essa configuração se aplique apenas ao acesso ao pacote e não à comunicação com o serviço.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Criar uma política de auditoria de Configuração de Convidado personalizada para Windows passo a passo

Crie uma configuração DSC para auditar as configurações. O exemplo de script do PowerShell a seguir cria uma configuração chamada **AuditBitLocker**, importa o módulo de recurso **PsDscResources** e usa o recurso `Service` para auditar um serviço em execução. O script de configuração pode ser executado em um computador Windows ou macOS.

```powershell
# Add PSDscResources module to environment
Install-Module 'PSDscResources'

# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker ./Config
```

Nomeie o arquivo como `config.ps1` e salve-o na pasta do projeto. Execute-o no PowerShell por meio do comando `./config.ps1` no terminal. Um novo arquivo mof é criado.

O comando `Node AuditBitlocker` não é tecnicamente necessário, mas ele produz um arquivo chamado `AuditBitlocker.mof` em vez do padrão `localhost.mof`. Ao nomear o arquivo .mof conforme a configuração, fica mais fácil organizar vários arquivos ao operar em escala.

Depois que o MOF é compilado, os arquivos de suporte devem ser empacotados juntos. O pacote fechado é usado pela Configuração de Convidado para criar as definições do Azure Policy.

O cmdlet `New-GuestConfigurationPackage` cria o pacote. Os módulos que são necessários para a configuração devem estar disponíveis no `$Env:PSModulePath`. Parâmetros do cmdlet `New-GuestConfigurationPackage` ao criar um conteúdo do Windows:

- **Name**: nome do pacote da Configuração de Convidado.
- **Configuração**: caminho completo do documento com a configuração DSC compilada.
- **Caminho**: caminho da pasta de saída. Esse parâmetro é opcional. Caso não seja especificado, o pacote será criado no diretório atual.

Execute o comando a seguir para criar um pacote usando a configuração fornecida na etapa anterior:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Depois de criar o pacote de configuração, mas antes de publicá-lo no Azure, você pode testar o pacote em sua estação de trabalho ou em um ambiente de CI/CD (integração contínua e implantação contínua). O cmdlet GuestConfiguration `Test-GuestConfigurationPackage` inclui o mesmo agente em seu ambiente de desenvolvimento, assim como ele é usado dentro de computadores com o Azure. Ao usar essa solução, você pode executar testes de integração localmente antes da liberação para ambientes de nuvem cobrados.

Como o agente está realmente avaliando o ambiente local, na maioria dos casos, você precisa executar o cmdlet Test- na mesma plataforma do SO que planeja auditar. O teste usa apenas módulos que estão incluídos no pacote de conteúdo.

Parâmetros do cmdlet `Test-GuestConfigurationPackage`:

- **Name**: nome da Configuração de Convidado.
- **Parâmetro**: parâmetros de política fornecidos no formato de tabela de hash.
- **Caminho**: caminho completo do pacote de Configuração de Convidado.

Execute o comando a seguir para testar o pacote criado pela etapa anterior:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

O cmdlet também tem suporte para a entrada do pipeline do PowerShell. Conduza a saída do cmdlet `New-GuestConfigurationPackage` para o cmdlet `Test-GuestConfigurationPackage`.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

A próxima etapa é publicar o arquivo no armazenamento de BLOBs do Azure. O comando `Publish-GuestConfigurationPackage` requer o `Az.Storage` módulo.

Parâmetros do cmdlet `Publish-GuestConfigurationPackage`:

- **Caminho**: local do pacote a ser publicado
- **ResourceGroupName**: nome do grupo de recursos em que a conta de armazenamento está localizada
- **StorageAccountName**: nome da conta de armazenamento na qual o pacote deve ser publicado
- **StorageContainerName**: (padrão: *guestconfiguration*) nome do contêiner de armazenamento na conta de armazenamento
- **Forçar**: substituir o pacote existente na conta de armazenamento com o mesmo nome

O exemplo a seguir publica o pacote para um nome de contêiner de armazenamento ' guestconfiguration '.

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
```

Depois que um pacote de política personalizada de Configuração de Convidado tiver sido criado e carregado, crie a definição de política de Configuração de Convidado. O cmdlet `New-GuestConfigurationPolicy` usa um pacote de política personalizado e cria uma definição de política.

Parâmetros do cmdlet `New-GuestConfigurationPolicy`:

- **ContentUri**: URI de http(s) público do pacote de conteúdo da Configuração de Convidado.
- **DisplayName**: nome de exibição da política.
- **Descrição**: descrição da política.
- **Parâmetro**: parâmetros de política fornecidos no formato de tabela de hash.
- **Versão**: versão da política.
- **Caminho**: caminho de destino no qual as definições de política são criadas.
- **Platform**: plataforma de destino (Windows/Linux) da política de Configuração de Convidado e do pacote de conteúdo.
- **Tag** adiciona um ou mais filtros de tag à definição de política
- **Category** define o campo de metadados da categoria na definição de política

O exemplo a seguir cria as definições de política em um caminho específico de um pacote de política personalizado:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

Os arquivos a seguir são criados por `New-GuestConfigurationPolicy`:

- **auditIfNotExists.json**

A saída do cmdlet retorna um objeto que contenha o nome de exibição da iniciativa e o caminho dos arquivos da política.

Por fim, publique as definições de política usando o cmdlet `Publish-GuestConfigurationPolicy`. O cmdlet tem apenas o parâmetro **Path** que aponta para o local dos arquivos JSON criados por `New-GuestConfigurationPolicy`.

Para executar o comando Publish, você precisa ter acesso de criação das políticas no Azure. Os requisitos de autorização específicos estão documentados na página [Visão geral do Azure Policy](../overview.md). A melhor função interna é a de **Colaborador da política de recurso**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

O cmdlet `Publish-GuestConfigurationPolicy` aceita o caminho do pipeline do PowerShell. Com esse recurso, você pode criar os arquivos de política e publicá-los em um único conjunto de comandos de pipe.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Com a política criada no Azure, a última etapa é atribuir a definição. Consulte como atribuir a definição com o [portal](../assign-policy-portal.md), [CLI do Azure](../assign-policy-azurecli.md)e [Azure PowerShell](../assign-policy-powershell.md).

### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrar políticas de Configuração de Convidado usando tags

As políticas criadas por cmdlets no módulo de Configuração de Convidado podem incluir um filtro para marcas como opção. O parâmetro **Tag** de `New-GuestConfigurationPolicy` oferece suporte a uma matriz de tabelas de hash contendo toda a marca individual. As marcas são adicionadas à `If` seção da definição de política e não podem ser modificadas por uma atribuição de política.

A seguir, é fornecido um trecho de código de exemplo de uma definição de política que filtra as marcas.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content
    }
  ]
}
```

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Usar parâmetros em definições de políticas de Configuração de Convidado personalizadas

A Configuração de Convidado tem suporte à substituição de propriedades de uma Configuração em tempo de execução. Com esse recurso, os valores no arquivo MOF no pacote não precisam ser considerados estáticos. Os valores de substituição são fornecidos por meio do Azure Policy e não afetam a forma como as configurações são criadas ou compiladas.

Os cmdlets `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` incluem um parâmetro chamado **Parameter**. Esse parâmetro usa uma tabela de hash que inclui todos os detalhes sobre cada parâmetro e cria as seções necessárias de cada arquivo usado para a definição do Azure Policy.

O exemplo a seguir cria uma definição de política para auditar um serviço, no qual o usuário seleciona a partir de uma lista no momento da atribuição da política.

```azurepowershell-interactive
# This DSC Resource text:
Service 'UserSelectedNameExample'
      {
          Name = 'ParameterValue'
          Ensure = 'Present'
          State = 'Running'
      }

# Would require the following hashtable:
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'UserSelectedNameExample'                                   # DSC configuration resource id (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>Estender a Configuração de Convidado com ferramentas de terceiros

Os pacotes de artefato para a Configuração de Convidado podem ser estendidos para incluir ferramentas de terceiros.
A extensão da Configuração de Convidado requer o desenvolvimento de dois componentes.

- Um recurso Desired State Configuration que trata todas as atividades relacionadas ao gerenciamento da ferramenta de terceiros
  - Instalar
  - Invoke
  - Converter saída
- Conteúdo no formato correto para a ferramenta consumir nativamente

O recurso DSC requer desenvolvimento personalizado se uma solução de comunidade ainda não existir.
As soluções da comunidade podem ser descobertas pesquisando na Galeria do PowerShell a marca [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22).

> [!Note]
> A extensibilidade da Configuração de Convidado é um cenário "traga sua própria licença". Verifique se você atende aos termos e condições de quaisquer ferramentas de terceiros antes do uso.

Após a instalação do recurso DSC no ambiente de desenvolvimento, use o parâmetro **FilesToInclude** para `New-GuestConfigurationPackage` a fim de incluir o conteúdo da plataforma de terceiros no artefato de conteúdo.

### <a name="step-by-step-creating-a-content-artifact-that-uses-third-party-tools"></a>Criar um artefato de conteúdo que usa ferramentas de terceiros passo a passo

Somente o cmdlet `New-GuestConfigurationPackage` requer uma alteração das diretrizes passo a passo para artefatos de conteúdo DSC. Neste exemplo, use o módulo `gcInSpec` para estender a Configuração de Convidado para auditar máquinas Windows usando a plataforma InSpec em vez do módulo interno usado no Linux. O módulo da comunidade é mantido como um [projeto de código aberto no GitHub](https://github.com/microsoft/gcinspec).

Instale os módulos necessários em seu ambiente de desenvolvimento:

```azurepowershell-interactive
# Update PowerShellGet if needed to allow installing PreRelease versions of modules
Install-Module PowerShellGet -Force

# Install GuestConfiguration module prerelease version
Install-Module GuestConfiguration -allowprerelease

# Install commmunity supported gcInSpec module
Install-Module gcInSpec
```

Primeiro, crie o arquivo YaML usado pelo InSpec. O arquivo apresenta informações básicas sobre o ambiente. Um exemplo é dado abaixo:

```YaML
name: wmi_service
title: Verify WMI service is running
maintainer: Microsoft Corporation
summary: Validates that the Windows Service 'winmgmt' is running
copyright: Microsoft Corporation
license: MIT
version: 1.0.0
supports:
  - os-family: windows
```

Salve esse arquivo chamado `wmi_service.yml` em uma pasta chamada `wmi_service` no diretório do projeto.

Em seguida, crie o arquivo Ruby com a abstração de linguagem InSpec usada para auditar o computador.

```Ruby
control 'wmi_service' do
  impact 1.0
  title 'Verify windows service: winmgmt'
  desc 'Validates that the service, is installed, enabled, and running'

  describe service('winmgmt') do
    it { should be_installed }
    it { should be_enabled }
    it { should be_running }
  end
end

```

Salve esse arquivo `wmi_service.rb` em uma nova pasta chamada `controls` dentro do `wmi_service` diretório.

Por fim, crie uma configuração, importe o módulo de recurso **GuestConfiguration** e use o recurso `gcInSpec` para definir o nome do perfil InSpec.

```powershell
# Define the configuration and import GuestConfiguration
Configuration wmi_service
{
    Import-DSCResource -Module @{ModuleName = 'gcInSpec'; ModuleVersion = '2.1.0'}
    node 'wmi_service'
    {
        gcInSpec wmi_service
        {
            InSpecProfileName       = 'wmi_service'
            InSpecVersion           = '3.9.3'
            WindowsServerVersion    = '2016'
        }
    }
}

# Compile the configuration to create the MOF files
wmi_service -out ./Config
```

Agora você deve ter uma estrutura de projeto conforme a seguir:

```file
/ wmi_service
    / Config
        wmi_service.mof
    / wmi_service
        wmi_service.yml
        / controls
            wmi_service.rb 
```

Os arquivos de suporte devem ser empacotados juntos. O pacote fechado é usado pela Configuração de Convidado para criar as definições do Azure Policy.

O cmdlet `New-GuestConfigurationPackage` cria o pacote. Para conteúdo de terceiros, use o parâmetro **FilesToInclude** para adicionar o conteúdo InSpec ao pacote. Você não precisa especificar o **ChefProfilePath** como para pacotes do Linux.

- **Name**: nome do pacote da Configuração de Convidado.
- **Configuração**: caminho completo do documento com a configuração compilada.
- **Caminho**: caminho da pasta de saída. Esse parâmetro é opcional. Caso não seja especificado, o pacote será criado no diretório atual.
- **FilesoInclude**: caminho completo para o perfil InSpec.

Execute o comando a seguir para criar um pacote usando a configuração fornecida na etapa anterior:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'wmi_service' `
  -Configuration './Config/wmi_service.mof' `
  -FilesToInclude './wmi_service'  `
  -Path './package' 
```

## <a name="policy-lifecycle"></a>Ciclo de vida da política

Se você quiser liberar uma atualização para a política, faça a alteração para o pacote de configuração do convidado e os detalhes da definição de Azure Policy.

> [!NOTE]
> A `version` propriedade da atribuição de configuração de convidado afeta apenas os pacotes que são hospedados pela Microsoft. A prática recomendada para o controle de versão de conteúdo personalizado é incluir a versão no nome do arquivo.

Primeiro, ao executar `New-GuestConfigurationPackage` , especifique um nome para o pacote que o torna exclusivo de versões anteriores. Você pode incluir um número de versão no nome, como `PackageName_1.0.0` .
O número neste exemplo é usado apenas para tornar o pacote exclusivo, não para especificar que o pacote deve ser considerado mais recente ou mais antigo que outros pacotes.

Em segundo lugar, atualize os parâmetros usados com o `New-GuestConfigurationPolicy` cmdlet seguindo cada uma das explicações abaixo.

- **Versão**: ao executar o cmdlet `New-GuestConfigurationPolicy`, você deve especificar um número de versão maior do que o publicado atualmente.
- **contentUri**: ao executar o `New-GuestConfigurationPolicy` cmdlet, você deve especificar um URI para o local do pacote. A inclusão de uma versão do pacote no nome do arquivo garantirá que o valor dessa propriedade seja alterado em cada versão.
- **contentHash**: essa propriedade é atualizada automaticamente pelo cmdlet `New-GuestConfigurationPolicy`. Trata-se de um valor de hash do pacote criado por `New-GuestConfigurationPackage`. A propriedade deve estar correta para o arquivo `.zip` que você publicar. Caso apenas a propriedade **contentUri** seja atualizada, a extensão não aceitará o pacote de conteúdo.

A maneira mais fácil de liberar um pacote atualizado é repetindo o processo descrito neste artigo e fornecendo um número de versão atualizado. Esse processo garante que todas as propriedades tenham sido atualizadas corretamente.

## <a name="optional-signing-guest-configuration-packages"></a>Opcional: Assinar pacotes de Configuração de Convidado

As políticas personalizadas de Configuração de Convidado usam o hash SHA256 para validar se o pacote de política não foi alterado.
Como opção, os clientes também podem usar um certificado para assinar pacotes e forçar a extensão de Configuração de Convidado a permitir somente o conteúdo assinado.

Para habilitar esse cenário, duas etapas precisam ser concluídas. Execute o cmdlet para assinar o pacote de conteúdo e acrescente uma tag para os computadores que devem exigir que o código seja assinado.

Para usar o recurso de Validação de Assinatura, execute o cmdlet `Protect-GuestConfigurationPackage` para assinar o pacote antes que ele seja publicado. Esse cmdlet requer um certificado de “Assinatura de Código”.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parâmetros do cmdlet `Protect-GuestConfigurationPackage`:

- **Caminho**: caminho completo do pacote de Configuração de Convidado.
- **Certificado**: certificado de autenticação de código para assinar o pacote. Esse parâmetro só tem suporte ao assinar um conteúdo para o Windows.

O agente GuestConfiguration espera que a chave pública do certificado esteja presente em "Autoridades de Certificado Raiz Confiável" em computadores Windows e no caminho `/usr/local/share/ca-certificates/extra` em computadores Linux. Para que o nó verifique o conteúdo assinado, instale a chave pública do certificado no computador antes de aplicar a política personalizada. Esse processo pode ser feito usando qualquer técnica dentro da VM ou usando o Azure Policy. Um modelo de exemplo é [fornecido aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
A política de acesso de Key Vault deve permitir o acesso do provedor de recursos de computação a certificados durante as implantações. Para obter etapas detalhadas, confira [Configurar o Key Vault para máquinas virtuais no Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

A seguir, é apresentado um exemplo para exportar a chave pública de um certificado de assinatura e importar para o computador.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Depois que o conteúdo for publicado, acrescente uma marca com o nome `GuestConfigPolicyCertificateValidation` e o valor `enabled` a todas as máquinas virtuais em que a assinatura de código deve ser necessária. Consulte as [Amostras de tag](../samples/built-in-policies.md#tags) sobre como tags podem ser entregues em escala usando o Azure Policy. Depois que essa tag estiver sendo usada, a definição de política gerada usando o cmdlet `New-GuestConfigurationPolicy` habilita o requisito por meio da extensão da Configuração de Convidado.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as VMs de auditoria com a [Configuração de Convidado](../concepts/guest-configuration.md).
- Entenda como [criar políticas de forma programática](./programmatically-create.md).
- Saiba como [obter dados de conformidade](./get-compliance-data.md).
