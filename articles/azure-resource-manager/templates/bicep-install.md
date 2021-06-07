---
title: Configurar ambientes de desenvolvimento e implantação do bicep
description: Como configurar ambientes de desenvolvimento e implantação do bicep
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 0e62e6a4633bee09fcbe8b783118cc95ccd5702e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626094"
---
# <a name="install-bicep-preview"></a>Instalar o bicep (versão prévia)

Saiba como configurar ambientes de desenvolvimento e implantação do bicep.

## <a name="development-environment"></a>Ambiente de desenvolvimento

Para obter a melhor experiência de criação de bicep, você precisa de dois componentes:

- **Extensão bicep para Visual Studio Code**. Para criar arquivos bicep, você precisa de um bom editor de bicep. É recomendável [Visual Studio Code](https://code.visualstudio.com/) com a [extensão bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Essas ferramentas fornecem suporte a idiomas e preenchimento automático de recursos. Eles ajudam a criar e validar arquivos bicep. Para obter mais informações sobre como usar Visual Studio Code e a extensão bicep, consulte [início rápido: criar arquivos bicep com Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- **BICEP CLI**. Use a CLI bicep para compilar arquivos bicep para modelos de JSON de ARM e descompilar modelos JSON de ARM para arquivos bicep. Para obter as instruções de instalação, consulte [instalar a CLI do bicep](#install-manually).

## <a name="deployment-environment"></a>Ambiente de implantação

Para implantar arquivos bicep locais, você precisa de dois componentes:

- **CLI do Azure versão 2.20.0 ou posterior, ou Azure PowerShell versão 5.6.0 ou posterior**. Para obter as instruções de instalação, confira:

  - [Instale o Azure PowerShell](/powershell/azure/install-az-ps)
  - [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows)
  - [Instalar a CLI do Azure no Linux](/cli/azure/install-azure-cli-linux)
  - [Instalar a CLI do Azure no macOS](/cli/azure/install-azure-cli-macos)

  > [!NOTE]
  > Atualmente, tanto CLI do Azure quanto Azure PowerShell só podem implantar arquivos bicep locais. Para obter mais informações sobre como implantar arquivos bicep usando CLI do Azure, consulte [Deploy-CLI](./deploy-cli.md#deploy-remote-template). Para obter mais informações sobre como implantar arquivos bicep usando Azure PowerShell, consulte [Deploy-PowerShell]( ./deploy-powershell.md#deploy-remote-template).

- **BICEP CLI**. A CLI do bicep é necessária para compilar arquivos bicep para modelos JSON antes da implantação. Para obter as instruções de instalação, consulte [instalar a CLI do bicep](#install-bicep-cli).

Depois que os componentes são instalados, você pode implantar um arquivo bicep com:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -storageAccountType Standard_GRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

---

## <a name="install-bicep-cli"></a>Instalar a CLI do bicep

- Para usar a CLI do bicep para compilar e descompilar arquivos do bicep, consulte [instalar manualmente](#install-manually).
- Para usar CLI do Azure para implantar arquivos bicep, consulte [usar com CLI do Azure](#use-with-azure-cli).
- Para usar Azure PowerShell para implantar arquivos bicep, consulte [usar com Azure PowerShell](#use-with-azure-powershell).

### <a name="use-with-azure-cli"></a>Usar com CLI do Azure

Com CLI do Azure versão 2.20.0 ou posterior instalada, a CLI do bicep é instalada automaticamente quando um comando que depende dele é executado. Por exemplo:

```azurecli
az deployment group create --template-file azuredeploy.bicep --resource-group myResourceGroup
```

ou

```azurecli
az bicep ...
```

Você também pode instalar manualmente a CLI usando os comandos internos:

```bash
az bicep install
```

Para atualizar para a versão mais recente:

```bash
az bicep upgrade
```

Para instalar uma versão específica:

```bash
az bicep install --version v0.3.126
```

> [!IMPORTANT]
> CLI do Azure instala uma versão separada da CLI do bicep que não está em conflito com qualquer outra instalação do bicep que você tenha e CLI do Azure não adiciona a CLI do bicep ao seu caminho. Para usar a CLI do bicep para compilar/descompilar arquivos do bicep ou para usar Azure PowerShell para implantar arquivos do bicep, consulte [instalar manualmente](#install-manually) ou [usar com o Azure PowerShell](#use-with-azure-powershell).

Para listar todas as versões disponíveis da CLI do bicep:

```bash
az bicep list-versions
```

Para mostrar as versões instaladas:

```bash
az bicep version
```

### <a name="use-with-azure-powershell"></a>Usar com Azure PowerShell

Azure PowerShell não tem a capacidade de instalar a CLI do bicep ainda. Azure PowerShell (v 5.6.0 ou posterior) espera que a CLI do bicep já esteja instalada e disponível no caminho. Siga um dos [métodos de instalação manual](#install-manually).

Para implantar arquivos bicep, a CLI do bicep versão 0.3.1 ou posterior é necessária. Para verificar a versão da CLI do bicep:

```cmd
bicep --version
```

> [!IMPORTANT]
> CLI do Azure instala sua própria versão independente da CLI do bicep. Azure PowerShell implantação falha mesmo que você tenha as versões necessárias instaladas para CLI do Azure.

Depois que a CLI do bicep for instalada, a CLI do bicep será chamada sempre que for necessária para um cmdlet de implantação. Por exemplo:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile azuredeploy.bicep
```

### <a name="install-manually"></a>Instalar manualmente

Os métodos a seguir instalam a CLI do bicep e a adicionam ao seu caminho.

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>via homebrew

```sh
# Add the tap for bicep
brew tap azure/bicep https://github.com/azure/bicep

# Install the tool
brew install azure/bicep/bicep
```

##### <a name="macos-manual-install"></a>instalação manual do macOS

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Windows Installer

Baixe e execute o [Windows Installer mais recente](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe). O instalador não requer privilégios administrativos. Após a instalação, a CLI do bicep é adicionada ao seu caminho de usuário. Feche e reabra todas as janelas do Shell de comando abertas para que a alteração de caminho entre em vigor.

##### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>Manual com o PowerShell

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-the-nightly-builds"></a>Instalar as compilações noturnas

Se você quiser experimentar os bits de pré-lançamento mais recentes do bicep antes que eles sejam liberados, consulte [instalar compilações noturnas](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md).

> [!WARNING]
> Essas compilações de pré-lançamento têm muito mais probabilidade de haver bugs conhecidos ou desconhecidos.

## <a name="next-steps"></a>Próximas etapas

Introdução ao guia de [início rápido do bicep](./quickstart-create-bicep-use-visual-studio-code.md).
