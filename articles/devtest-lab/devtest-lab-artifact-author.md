---
title: "Criar artefatos personalizados para sua máquina virtual do DevTest Labs | Microsoft Docs"
description: "Aprenda a criar seus próprios artefatos para uso com o Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 679819618452d65847c6163569e04945ba8a414d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
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

| Nome do elemento | Obrigatório? | Descrição |
| --- | --- | --- |
| $schema |Não |Local do arquivo de esquema JSON. O arquivo de esquema JSON pode lhe ajudar a testar a validade do arquivo de definição. |
| título |Sim |Nome do artefato exibido no laboratório. |
| Descrição |Sim |Descrição do artefato exibido no laboratório. |
| iconUri |Não |URI do ícone exibido no laboratório. |
| targetOsType |Sim |Sistema operacional da VM em que o artefato está instalado. As opções com suporte são Windows e Linux. |
| parâmetros |Não |Valores fornecidos quando o comando de instalação do artefato é executado em um computador. Isso ajuda você a personalizar seu artefato. |
| runCommand |Sim |Comando de instalação do artefato executado em uma VM. |

### <a name="artifact-parameters"></a>Parâmetros do artefato
Na seção de parâmetros do arquivo de definição, especifique quais valores um usuário pode inserir ao instalar um artefato. Você pode consultar esses valores no comando de instalação do artefato.

Para definir parâmetros, use a seguinte estrutura:

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| Nome do elemento | Obrigatório? | Descrição |
| --- | --- | --- |
| type |Sim |Tipo do valor do parâmetro. Veja a lista a seguir para os tipos permitidos. |
| displayName |Sim |Nome do parâmetro exibido para um usuário no laboratório. | |
| Descrição |Sim |Descrição do parâmetro exibido no laboratório. |

Os tipos permitidos são:

* string (ualquer cadeia de caracteres JSON válida)
* int (qualquer inteiro JSON válido)
* bool (qualquer booliano JSON válido)
* array (qualquer matriz JSON válida)

## <a name="artifact-expressions-and-functions"></a>Expressões e funções de artefatos
Você pode usar expressões e funções para construir o comando de instalação do artefato.
As expressões são colocadas entre colchetes ([ e ]) e avaliadas quando o artefato é instalado. Expressões podem aparecer em qualquer lugar em um valor de cadeia de caracteres JSON. Expressões sempre retornam outro valor JSON. Se precisar usar uma cadeia de caracteres literal que começa com um colchete ([), você deverá usar dois colchetes ([[).
Normalmente, você usa expressões com funções para construir um valor. Assim como no JavaScript, as chamadas de função são formatadas como **functionName(arg1,arg2,arg3)**.

A lista a seguir mostra funções comuns:

* **parameters(parameterName)**: retorna um valor de parâmetro fornecido quando o comando do artefato é executado.
* **concat(arg1,arg2,arg3, …..)**: combina diversos valores de cadeia de caracteres. Essa função pode conter uma variedade de argumentos.

O exemplo a seguir mostra como usar expressões e funções para construir um valor:

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Criar um artefato personalizado

1. Instalar um editor de JSON. Você precisa de um editor de JSON para trabalhar com arquivos de definição de artefato. Recomendamos o uso do [Visual Studio Code](https://code.visualstudio.com/), que está disponível para Windows, Linux e OS X.
2. Obtenha um exemplo de arquivo de definição artifactfile.json. Confira os artefatos criados pela equipe do DevTest Labs em nosso [repositório GitHub](https://github.com/Azure/azure-devtestlab). Você criou uma biblioteca avançada artefatos que podem lhe ajudar a criar seus próprios artefatos. Baixe um arquivo de definição de artefato e faça as alterações nele para criar seus próprios artefatos.
3. Faça uso do IntelliSense. Use o IntelliSense para ver os elementos válidos que você pode usar para construir um arquivo de definição de artefato. Você também pode ver as diferentes opções de valores para um elemento. Por exemplo, quando você edita o elemento **targetOsType**, o IntelliSense mostra a você as duas opções, Windows ou Linux.
4. Armazene o artefato em um [repositório Git](devtest-lab-add-artifact-repo.md).
   
   1. Crie um diretório separado para cada artefato. O nome do diretório deve ser igual ao nome do artefato.
   2. Armazene o arquivo de definição de artefato (artifactfile.json) no diretório que você criou.
   3. Armazene os scripts referenciados pelo comando de instalação de artefato.
      
      Veja um exemplo de como uma pasta de artefatos pode ser:
      
      ![Exemplo de pasta de artefato](./media/devtest-lab-artifact-author/git-repo.png)
5. Adicione o repositório de artefatos ao laboratório. Veja [Adicionar um repositório Git para artefatos e modelos](devtest-lab-add-artifact-repo.md).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Como diagnosticar falhas de artefato em DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Ingresse uma VM em um domínio do Active Directory existente usando um modelo do Resource Manager no DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Próximas etapas
* Saiba como [adicionar um repositório de artefatos Git a um laboratório](devtest-lab-add-artifact-repo.md).

