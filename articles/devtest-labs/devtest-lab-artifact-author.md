---
title: Criar artefatos personalizados para sua máquina virtual do DevTest Labs | Microsoft Docs
description: Saiba como criar artefatos para usar com Azure DevTest Labs para implantar e configurar aplicativos depois de provisionar uma máquina virtual.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85acfcc3811e671e58fadab08a23951778e1323d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88270675"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Criar artefatos personalizados para sua máquina virtual do DevTest Labs

Assista ao vídeo a seguir para obter uma visão geral das etapas descritas neste artigo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Visão geral
Você pode usar *artefatos* para implantar e configurar seu aplicativo depois de provisionar uma VM. Um artefato é composto por um arquivo de definição de artefato e outros arquivos de script que são armazenados em uma pasta em um repositório Git. Arquivos de definição de artefato são formados por JSON e expressões que você pode usar para especificar o que você deseja instalar em uma VM. Por exemplo, você pode definir o nome de um artefato, um comando a ser executado e parâmetros que estão disponíveis quando o comando é executado. Você pode consultar por nome outros arquivos de script dentro do arquivo de definição de artefato.

## <a name="artifact-definition-file-format"></a>Formato do arquivo de definição de artefato
O exemplo a seguir mostra as seções que compõem a estrutura básica de um arquivo de definição:

```json
  {
    "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
    "title": "",
    "description": "",
    "iconUri": "",
    "targetOsType": "",
    "parameters": {
      "<parameterName>": {
        "type": "",
        "displayName": "",
        "description": ""
      }
    },
    "runCommand": {
      "commandToExecute": ""
    }
  }
```

| Nome do elemento | Necessário? | Descrição |
| --- | --- | --- |
| $schema |Não |Local do arquivo de esquema JSON. O arquivo de esquema JSON pode lhe ajudar a testar a validade do arquivo de definição. |
| título |Sim |Nome do artefato exibido no laboratório. |
| descrição |Sim |Descrição do artefato exibido no laboratório. |
| iconUri |Não |URI do ícone exibido no laboratório. |
| targetOsType |Sim |Sistema operacional da VM em que o artefato está instalado. As opções com suporte são Windows e Linux. |
| parameters |Não |Valores fornecidos quando o comando de instalação do artefato é executado em um computador. Isso ajuda você a personalizar seu artefato. |
| runCommand |Sim |Comando de instalação do artefato executado em uma VM. |

### <a name="artifact-parameters"></a>Parâmetros do artefato
Na seção de parâmetros do arquivo de definição, especifique quais valores um usuário pode inserir ao instalar um artefato. Você pode consultar esses valores no comando de instalação do artefato.

Para definir parâmetros, use a seguinte estrutura:

```json
  "parameters": {
    "<parameterName>": {
      "type": "<type-of-parameter-value>",
      "displayName": "<display-name-of-parameter>",
      "description": "<description-of-parameter>"
    }
  }
```

| Nome do elemento | Necessário? | Descrição |
| --- | --- | --- |
| type |Sim |Tipo do valor do parâmetro. Veja a lista a seguir para os tipos permitidos. |
| displayName |Sim |Nome do parâmetro exibido para um usuário no laboratório. |
| descrição |Sim |Descrição do parâmetro exibido no laboratório. |

Os tipos permitidos são:

* string (ualquer cadeia de caracteres JSON válida)
* int (qualquer inteiro JSON válido)
* bool (qualquer booliano JSON válido)
* array (qualquer matriz JSON válida)

## <a name="secrets-as-secure-strings"></a>Segredos como cadeias de caracteres seguras
Declare segredos como cadeias de caracteres seguras. Aqui está a sintaxe para declarar um parâmetro de cadeia de caracteres segura na `parameters` seção do **artifactfile.jsno** arquivo:

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

Para o comando de instalação do artefato, execute o script do PowerShell que usa a cadeia de caracteres segura criada usando o comando ConvertTo-SecureString. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

Para obter o exemplo completo artifactfile.jse o artifact.ps1 (script do PowerShell), consulte [Este exemplo no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

Outro ponto importante a ser observado não é registrar os segredos no console, pois a saída é capturada para depuração do usuário. 

## <a name="artifact-expressions-and-functions"></a>Expressões e funções de artefatos
Você pode usar expressões e funções para construir o comando de instalação do artefato.
As expressões são colocadas entre colchetes ([ e ]) e avaliadas quando o artefato é instalado. Expressões podem aparecer em qualquer lugar em um valor de cadeia de caracteres JSON. Expressões sempre retornam outro valor JSON. Se precisar usar uma cadeia de caracteres literal que começa com um colchete ([), você deverá usar dois colchetes ([[).
Normalmente, você usa expressões com funções para construir um valor. Assim como no JavaScript, as chamadas de função são formatadas como **functionName(arg1,arg2,arg3)**.

A lista a seguir mostra funções comuns:

* **parameters(parameterName)**: retorna um valor de parâmetro fornecido quando o comando do artefato é executado.
* **concat(arg1, arg2, arg3,….. )**: combina diversos valores de cadeia de caracteres. Essa função pode conter uma variedade de argumentos.

O exemplo a seguir mostra como usar expressões e funções para construir um valor:

```json
  runCommand": {
      "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
  , ' -RawPackagesList ', parameters('packages')
  , ' -Username ', parameters('installUsername')
  , ' -Password ', parameters('installPassword'))]"
  }
```

## <a name="create-a-custom-artifact"></a>Criar um artefato personalizado

1. Instalar um editor de JSON. Você precisa de um editor de JSON para trabalhar com arquivos de definição de artefato. É recomendável usar [Visual Studio Code](https://code.visualstudio.com/), que está disponível para Windows, Linux e os X.
2. Obtenha um exemplo de arquivo de definição artifactfile.json. Confira os artefatos criados pela equipe do DevTest Labs em nosso [repositório GitHub](https://github.com/Azure/azure-devtestlab). Você criou uma biblioteca avançada artefatos que podem lhe ajudar a criar seus próprios artefatos. Baixe um arquivo de definição de artefato e faça as alterações nele para criar seus próprios artefatos.
3. Faça uso do IntelliSense. Use o IntelliSense para ver os elementos válidos que você pode usar para construir um arquivo de definição de artefato. Você também pode ver as diferentes opções de valores para um elemento. Por exemplo, quando você edita o elemento **targetOsType**, o IntelliSense mostra a você as duas opções, Windows ou Linux.
4. Armazene o artefato no [repositório público do Git para DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) ou no [seu próprio repositório Git](devtest-lab-add-artifact-repo.md). No repositório público, você pode exibir os artefatos compartilhados por outras pessoas que você pode usar diretamente ou personalizá-los para atender às suas necessidades.
   
   1. Crie um diretório separado para cada artefato. O nome do diretório deve ser igual ao nome do artefato.
   2. Armazene o arquivo de definição de artefato (artifactfile.json) no diretório que você criou.
   3. Armazene os scripts referenciados pelo comando de instalação de artefato.
      
      Veja um exemplo de como uma pasta de artefatos pode ser:
      
      ![Exemplo de pasta de artefato](./media/devtest-lab-artifact-author/git-repo.png)
5. Se estiver usando seu próprio repositório para armazenar artefatos, adicione-o ao laboratório seguindo as instruções no artigo: [Add a Git repository for artifacts and templates](devtest-lab-add-artifact-repo.md) (Adicionar um repositório Git para artefatos e modelos).

## <a name="related-articles"></a>Artigos relacionados
* [Como diagnosticar falhas de artefato em DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Ingresse uma VM em um domínio do Active Directory existente usando um modelo do Resource Manager no DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Próximas etapas
* Saiba como [adicionar um repositório de artefatos Git a um laboratório](devtest-lab-add-artifact-repo.md).
