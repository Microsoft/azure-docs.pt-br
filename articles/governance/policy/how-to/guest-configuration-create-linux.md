---
title: Como criar políticas de Configuração de Convidado para o Linux
description: Saiba como criar uma política de Configuração de Convidado do Azure Policy para Linux.
ms.date: 08/17/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 352c8b1936c38c9b5f706ac88bd4fd06e008b892
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525340"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Como criar políticas de Configuração de Convidado para o Linux

Antes de criar políticas personalizadas, leia as informações de visão geral em [Configuração de Convidado do Azure Policy](../concepts/guest-configuration.md).
 
Para saber mais sobre como criar políticas de Configuração de Convidado para o Windows, consulte a página [Como criar políticas de Configuração de Convidado para o Windows](./guest-configuration-create.md)

Ao auditar o Linux, a Configuração de Convidado usa o [Chef InSpec](https://www.inspec.io/). O perfil InSpec define a condição em que o computador deve estar. Caso a avaliação da configuração falhe, o efeito da política **auditIfNotExists** será disparado e o computador será considerado **não conforme**.

A [Configuração de Convidado do Azure Policy](../concepts/guest-configuration.md) só pode ser usada para auditar configurações dentro de computadores. A correção das configurações dentro de computadores ainda não está disponível.

Use as ações a seguir para criar sua própria configuração para validar o estado de um computador que tem ou não o Azure.

> [!IMPORTANT]
> As definições de política personalizadas com a configuração de convidado no Azure governamental e nos ambientes do Azure China são um recurso de visualização.
>
> A extensão de Configuração de Convidado é necessária para executar auditorias em máquinas virtuais do Azure. Para implantar a extensão em escala em todas as máquinas Linux, atribua a seguinte definição de política: `Deploy prerequisites to enable Guest Configuration Policy on Linux VMs`
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
> Não há suporte para a compilação de configurações no Linux.

### <a name="base-requirements"></a>Requisitos base

Sistemas operacionais em que é possível instalar o módulo:

- Linux
- macOS
- Windows

> [!NOTE]
> O cmdlet `Test-GuestConfigurationPackage` requer o OpenSSL versão 1,0, devido a uma dependência em OMI. Isso causa um erro em qualquer ambiente com OpenSSL 1,1 ou posterior.
>
> A execução do cmdlet `Test-GuestConfigurationPackage` só tem suporte no Windows para o módulo de configuração de convidado versão 2.1.0.

O módulo do recurso de Configuração de Convidado requer o seguinte software:

- PowerShell 6.2 ou superior. Se ainda não estiver instalado, siga [estas instruções](/powershell/scripting/install/installing-powershell).
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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Artefatos e política de Configuração de Convidado para Linux

Mesmo em ambientes Linux, a Configuração de Convidado usa a Desired State Configuration como uma abstração de linguagem. A implementação é baseada em código nativo (C++) para que o carregamento do PowerShell não seja necessário. No entanto, é preciso haver um MOF de configuração que descreva os detalhes sobre o ambiente.
A DSC está agindo como um wrapper do InSpec para padronizar como ele é executado, como os parâmetros são fornecidos e como a saída é retornada ao serviço. É preciso ter pouco conhecimento da DSC para trabalhar com o conteúdo do InSpec personalizado.

#### <a name="configuration-requirements"></a>Requisitos de configuração

O nome da configuração personalizada deve ser o mesmo em todos os lugares. O nome do arquivo. zip para o pacote de conteúdo, o nome da configuração no arquivo MOF e o nome da atribuição de convidado no modelo de Azure Resource Manager (modelo ARM), deve ser o mesmo.

Os cmdlets do PowerShell auxiliam na criação do pacote.
Nenhuma pasta de nível de raiz ou de versão é necessária.
O formato do pacote deve ser um arquivo zip. e não pode exceder um tamanho total de 100 MB quando não compactado.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Personalizar a Configuração de Convidado no Linux

A Configuração de Convidado no Linux usa o recurso `ChefInSpecResource` para fornecer ao mecanismo o nome do [perfil InSpec](https://www.inspec.io/docs/reference/profiles/). O **nome** é a única propriedade de recurso necessária. Crie um arquivo YaML e um arquivo de script Ruby conforme detalhado abaixo.

Primeiro, crie o arquivo YaML usado pelo InSpec. O arquivo apresenta informações básicas sobre o ambiente. Um exemplo é dado abaixo:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Nomeie o arquivo como `inspec.yml` e salve-o em uma pasta denominada `linux-path` no diretório do projeto.

Em seguida, crie o arquivo Ruby com a abstração de linguagem InSpec usada para auditar o computador.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Nomeie o arquivo como `linux-path.rb` e salve-o em uma nova pasta denominada `controls` dentro do diretório `linux-path`.

Por fim, crie uma configuração, importe o módulo de recurso **PSDesiredStateConfiguration** e compile a configuração.

```powershell
# import PSDesiredStateConfiguration module
import-module PSDesiredStateConfiguration

# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
AuditFilePathExists -out ./Config
```

Nomeie o arquivo como `config.ps1` e salve-o na pasta do projeto. Execute-o no PowerShell por meio do comando `./config.ps1` no terminal. Um novo arquivo mof será criado.

O comando `Node AuditFilePathExists` não é tecnicamente necessário, mas ele produz um arquivo denominado `AuditFilePathExists.mof` em vez do padrão `localhost.mof`. Ao nomear o arquivo .mof conforme a configuração, fica mais fácil organizar vários arquivos ao operar em escala.

Agora você deve ter uma estrutura de projeto conforme a seguir:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        inspec.yml
        / controls
            linux-path.rb 
```

Os arquivos de suporte devem ser empacotados juntos. O pacote fechado é usado pela Configuração de Convidado para criar as definições do Azure Policy.

O cmdlet `New-GuestConfigurationPackage` cria um pacote. Parâmetros do cmdlet `New-GuestConfigurationPackage` ao criar um conteúdo do Linux:

- **Name**: nome do pacote da Configuração de Convidado.
- **Configuração**: caminho completo do documento com a configuração compilada.
- **Caminho**: caminho da pasta de saída. Esse parâmetro é opcional. Caso não seja especificado, o pacote será criado no diretório atual.
- **ChefInspecProfilePath**: caminho completo para o perfil inspec. Esse parâmetro só tem suporte durante a criação de conteúdo para auditar o Linux.

Execute o seguinte comando para criar um pacote usando a configuração fornecida na etapa anterior:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

Depois de criar o pacote de configuração, mas antes de publicá-lo no Azure, você pode testar o pacote em sua estação de trabalho ou em um ambiente de CI/CD (integração contínua e implantação contínua). O cmdlet GuestConfiguration `Test-GuestConfigurationPackage` inclui o mesmo agente em seu ambiente de desenvolvimento, assim como ele é usado dentro de computadores com o Azure. Ao usar essa solução, você pode executar testes de integração localmente antes da liberação para ambientes de nuvem cobrados.

Como o agente está realmente avaliando o ambiente local, na maioria dos casos, você precisa executar o cmdlet Test- na mesma plataforma do SO que planeja auditar.

Parâmetros do cmdlet `Test-GuestConfigurationPackage`:

- **Name**: nome da Configuração de Convidado.
- **Parâmetro**: parâmetros de política fornecidos no formato de tabela de hash.
- **Caminho**: caminho completo do pacote de Configuração de Convidado.

Execute o comando a seguir para testar o pacote criado pela etapa anterior:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

O cmdlet também tem suporte para a entrada do pipeline do PowerShell. Conduza a saída do cmdlet `New-GuestConfigurationPackage` para o cmdlet `Test-GuestConfigurationPackage`.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefInspecProfilePath './' | Test-GuestConfigurationPackage
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
Publish-GuestConfigurationPackage -Path ./AuditFilePathExists/AuditFilePathExists.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Os arquivos a seguir são criados por `New-GuestConfigurationPolicy`:

- **auditIfNotExists.json**

A saída do cmdlet retorna um objeto que contenha o nome de exibição da iniciativa e o caminho dos arquivos da política.

Por fim, publique as definições de política usando o cmdlet `Publish-GuestConfigurationPolicy`. O cmdlet tem apenas o parâmetro **Path** que aponta para o local dos arquivos JSON criados por `New-GuestConfigurationPolicy`.

Para executar o comando Publish, você precisa ter acesso de criação das políticas no Azure. Os requisitos de autorização específicos estão documentados na página [Visão geral do Azure Policy](../overview.md). A melhor função interna é a de **Colaborador da política de recurso**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path './policies'
```

 O cmdlet `Publish-GuestConfigurationPolicy` aceita o caminho do pipeline do PowerShell. Com esse recurso, você pode criar os arquivos de política e publicá-los em um único conjunto de comandos de pipe.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

Com a política criada no Azure, a última etapa é atribuir a definição. Consulte como atribuir a definição com o [portal](../assign-policy-portal.md), [CLI do Azure](../assign-policy-azurecli.md)e [Azure PowerShell](../assign-policy-powershell.md).

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Usar parâmetros em políticas de Configuração de Convidado personalizadas

A Configuração de Convidado tem suporte à substituição de propriedades de uma Configuração em tempo de execução. Com esse recurso, os valores no arquivo MOF no pacote não precisam ser considerados estáticos. Os valores de substituição são fornecidos por meio do Azure Policy e não afetam a forma como as configurações são criadas ou compiladas.

Com o InSpec, os parâmetros normalmente são tratados como entradas em tempo de execução ou como códigos usando atributos. A Configuração de Convidado ofusca esse processo para que a entrada possa ser fornecida quando a política é atribuída. Um arquivo de atributos é criado automaticamente no computador. Você não precisa criar e adicionar um arquivo no seu projeto. Existem duas etapas para adicionar parâmetros ao seu projeto de auditoria do Linux.

Defina a entrada no arquivo Ruby no qual você faz o script do que auditar no computador. Um exemplo é dado abaixo.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Adicione a propriedade **AttributesYmlContent** em sua configuração com qualquer cadeia de caracteres como o valor.
O agente de Configuração Convidado cria automaticamente o arquivo YAML usado pelo InSpec para armazenar atributos. Veja o exemplo abaixo.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "fromParameter"
        }
    }
}
```

Recompile o arquivo MOF usando os exemplos fornecidos neste documento.

Os cmdlets `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` incluem um parâmetro chamado **Parameter**. Esse parâmetro usa uma tabela de hash que inclui todos os detalhes sobre cada parâmetro e cria automaticamente todas as seções necessárias dos arquivos usados para criar cada definição do Azure Policy.

O exemplo a seguir cria uma definição de política para auditar um caminho de arquivo, em que o usuário fornece o caminho no momento da atribuição de política.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = 'File path to be audited.'      # Policy parameter description (optional)
        ResourceType = 'ChefInSpecResource'           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = 'AttributesYmlContent' # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

$uri = 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D'

New-GuestConfigurationPolicy -ContentUri $uri `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameter $PolicyParameterInfo `
    -Platform 'Linux' `
    -Version 1.0.0
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

### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrar políticas de Configuração de Convidado usando tags

As políticas criadas por cmdlets no módulo de Configuração de Convidado podem incluir um filtro para tags como opção. O parâmetro **-Tag** de `New-GuestConfigurationPolicy` dá suporte a uma matriz de hashtables contendo toda a tag individual. As tags serão adicionadas à seção `If` da definição de política e não poderão ser modificadas por uma atribuição de política.

A seguir, é fornecido um trecho de código de exemplo de uma definição de política que filtrará as tags.

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
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>Opcional: Assinar pacotes de Configuração de Convidado

As políticas personalizadas de Configuração de Convidado usam o hash SHA256 para validar se o pacote de política não foi alterado.
Como opção, os clientes também podem usar um certificado para assinar pacotes e forçar a extensão de Configuração de Convidado a permitir somente o conteúdo assinado.

Para habilitar esse cenário, duas etapas precisam ser concluídas. Execute o cmdlet para assinar o pacote de conteúdo e acrescente uma tag para os computadores que devem exigir que o código seja assinado.

Para usar o recurso de Validação de Assinatura, execute o cmdlet `Protect-GuestConfigurationPackage` para assinar o pacote antes que ele seja publicado. Esse cmdlet requer um certificado de “Assinatura de Código”.

Parâmetros do cmdlet `Protect-GuestConfigurationPackage`:

- **Caminho**: caminho completo do pacote de Configuração de Convidado.
- **PublicGpgKeyPath**: caminho da chave GPG pública. Esse parâmetro só tem suporte ao assinar um conteúdo para o Linux.

Uma boa referência para a criação de chaves GPG a serem usadas com computadores Linux é fornecida por um artigo no GitHub, [Gerar uma nova chave GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

O agente GuestConfiguration espera que a chave pública do certificado esteja presente no caminho `/usr/local/share/ca-certificates/extra` em computadores com Linux. Para que o nó verifique o conteúdo assinado, instale a chave pública do certificado no computador antes de aplicar a política personalizada. Esse processo pode ser feito usando qualquer técnica dentro da VM ou usando o Azure Policy. Um modelo de exemplo é [fornecido aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
A política de acesso de Key Vault deve permitir o acesso do provedor de recursos de computação a certificados durante as implantações. Para obter etapas detalhadas, consulte [Configurar o Key Vault para máquinas virtuais no Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Depois que o conteúdo for publicado, acrescente uma tag com o nome `GuestConfigPolicyCertificateValidation` e o valor `enabled` a todas as máquinas virtuais em que a assinatura de código deve ser necessária. Consulte as [Amostras de tag](../samples/built-in-policies.md#tags) sobre como tags podem ser entregues em escala usando o Azure Policy. Depois que essa tag estiver sendo usada, a definição de política gerada usando o cmdlet `New-GuestConfigurationPolicy` habilita o requisito por meio da extensão da Configuração de Convidado.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as VMs de auditoria com a [Configuração de Convidado](../concepts/guest-configuration.md).
- Entenda como [criar políticas de forma programática](./programmatically-create.md).
- Saiba como [obter dados de conformidade](./get-compliance-data.md).
