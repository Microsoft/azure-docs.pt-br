---
title: Automatizar compilações, testes e implantações de um trabalho de Azure Stream Analytics usando as ferramentas de CI/CD
description: Este artigo descreve como usar Azure Stream Analytics ferramentas de CI/CD para criar, testar e implantar automaticamente um projeto de Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: 2dbb491e77f132daf7b432f27705eba9e3e3cd3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036955"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>Automatizar compilações, testes e implantações de um trabalho de Azure Stream Analytics usando as ferramentas de CI/CD

Você pode usar o pacote Azure Stream Analytics CI/CD NPM para compilar, testar e implantar automaticamente seus projetos do Azure Stream Analytics Visual Studio Code ou do Visual Studio. Os projetos podem ser criados usando ferramentas de desenvolvimento ou podem ser exportados de trabalhos de Stream Analytics existentes. Este artigo descreve como usar o pacote NPM com qualquer sistema de CI/CD. Para implantação com Azure Pipelines, consulte [usar o Azure DevOps para criar um pipeline de CI/CD para um trabalho de Stream Analytics](set-up-cicd-pipeline.md).

## <a name="installation"></a>Instalação

Você pode [baixar o pacote](https://www.npmjs.com/package/azure-streamanalytics-cicd) diretamente ou instalá-lo [globalmente](https://docs.npmjs.com/downloading-and-installing-packages-globally) usando o `npm install -g azure-streamanalytics-cicd` comando. É recomendável usar o comando, que também pode ser usado em uma tarefa do PowerShell ou CLI do Azure script de um pipeline de compilação no **Azure pipelines**.

## <a name="build-the-project"></a>Compilar o projeto

O pacote NPM do **asa-streamanalytics-cICD** fornece as ferramentas para gerar Azure Resource Manager modelos de [projetos](./quick-create-visual-studio-code.md) do Stream Analytics Visual Studio Code ou [projetos do Visual Studio](stream-analytics-quick-create-vs.md). Você também pode usar o pacote NPM no Windows, no macOS e no Linux sem instalar o Visual Studio Code ou o Visual Studio.

Depois de instalar o pacote, use o comando a seguir para compilar seus projetos de Stream Analytics.

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

O comando *Build* faz uma verificação de sintaxe de palavra-chave e gera o modelo de Azure Resource Manager.

| Parâmetro | Descrição |
|---|---|
| `-project` | O caminho absoluto do **asaproj.jsno** arquivo para seu projeto Visual Studio Code ou **[nome do projeto]. asaproj** para o projeto do Visual Studio. |
| `-outputPath` | O caminho da pasta de saída para modelos de Azure Resource Manager. Se não for especificado, os modelos serão colocados no diretório atual. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

Quando um projeto de Stream Analytics é compilado com êxito, ele gera os dois arquivos a seguir na pasta de saída:

* Azure Resource Manager arquivo de modelo

   `[ProjectName].JobTemplate.json`

* Arquivo de parâmetro Azure Resource Manager

   `[ProjectName].JobTemplate.parameters.json`

Os parâmetros padrão na parameters.jsno arquivo são das configurações em seu projeto do Visual Studio Code ou do Visual Studio. Se você deseja implantar em outro ambiente, apenas substitua os parâmetros adequadamente.

Os valores padrão para todas as credenciais são **NULL**. Você precisa definir os valores antes de implantar no Azure.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

Para usar a identidade gerenciada para Azure Data Lake Store Gen1 como um coletor de saída, você precisa fornecer acesso à entidade de serviço usando o PowerShell antes de implantar no Azure. Saiba mais sobre como [implantar o ADLS Gen1 com a Identidade Gerenciada e o modelo do Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).

## <a name="local-run"></a>Execução local

Se o projeto tiver especificado arquivos de entrada locais, você poderá executar um script de Stream Analytics localmente usando o `localrun` comando.

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parâmetro | Descrição |
|---|---|
| `-project` | O caminho do **asaproj.jsno** arquivo para seu projeto Visual Studio Code ou **[nome do projeto]. asaproj** para o projeto do Visual Studio. |
| `-outputPath` | O caminho da pasta de saída. Se não for especificado, os arquivos de resultado de saída serão colocados no diretório atual. |
| `-customCodeZipFilePath` | O caminho do arquivo zip para código personalizado em C#, como um UDF ou um desserializador, se eles forem usados. Empacote as DLLs em um arquivo zip e especifique esse caminho. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> A UDF do JavaScript só funciona no Windows.

## <a name="automated-test"></a>Teste automatizado

Você pode usar o pacote NPM de CI/CD para configurar e executar testes automatizados para seu script de Stream Analytics.

### <a name="add-a-test-case"></a>Adicionar um caso de teste

Os casos de teste são descritos em um arquivo de configuração de teste. Para começar, use o `addtestcase` comando para adicionar um modelo de caso de teste ao arquivo de configuração de teste. Se o arquivo de configuração de teste não existir, um será criado por padrão.

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| Parâmetro | Descrição |
|---|---|
| `-project` | O caminho do **asaproj.jsno** arquivo para seu projeto Visual Studio Code ou **[nome do projeto]. asaproj** para o projeto do Visual Studio. |
| `-testConfigPath` | O caminho do arquivo de configuração de teste. Se não for especificado, o arquivo será pesquisado em **\test** no diretório atual do **asaproj.jsno** arquivo, com o nome de arquivo padrão **testConfig.jsem**. Um novo arquivo será criado se não existir. |

> [!NOTE]
> O `Script` valor notestConfig.jsgerado **no** arquivo é apenas para fornecer o contexto; Ele não é usado na lógica de teste. 

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

Se o arquivo de configuração de teste estiver vazio, o conteúdo a seguir será gravado no arquivo. Caso contrário, um caso de teste é adicionado à matriz de **casos**. As configurações de entrada necessárias são preenchidas automaticamente de acordo com os arquivos de configuração de entrada, se existirem. Caso contrário, os valores padrão serão configurados. O **FilePath** de cada entrada e a saída esperada devem ser especificados antes da execução do teste. Você pode modificar a configuração manualmente.

Se você quiser que a validação de teste ignore uma determinada saída, defina o campo **obrigatório** dessa saída esperada como **false**.

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": [Required],
          "Required": true
        }
      ]
    }
  ]
}
```

> [!NOTE]
> Atualmente, o único valor permitido para o `ScriptType` elemento é `InputMock` , que também é o valor padrão. Se você defini-lo como qualquer outro valor, ele é ignorado e o valor padrão ( `InputMock` ) é usado. 

### <a name="run-a-unit-test"></a>Executar um teste de unidade

Você pode usar o comando a seguir para executar vários casos de teste para seu projeto. Um resumo dos resultados de teste é gerado na pasta de saída. O processo é encerrado com o código **0** para todos os testes aprovados; **-1** para exceção ocorreu; **-2** para testes com falha.

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parâmetro | Descrição |
|---|---|
| `-project` | O caminho do **asaproj.jsno** arquivo para seu projeto Visual Studio Code ou **[nome do projeto]. asaproj** para o projeto do Visual Studio. |
| `-testConfigPath` | O caminho para o arquivo de configuração de teste. Se não for especificado, o arquivo será pesquisado em **\test** no diretório atual do **asaproj.jsno** arquivo, com o nome de arquivo padrão **testConfig.jsem**.
| `-outputPath` | O caminho da pasta de saída do resultado de teste. Se não for especificado, os arquivos de resultado de saída serão colocados no diretório atual. |
| `-customCodeZipFilePath` | O caminho do arquivo zip para o código personalizado, como um UDF ou um desserializador, se eles forem usados. |

Quando todos os testes forem concluídos, um resumo dos resultados do teste no formato JSON será gerado na pasta de saída. O arquivo de resumo é nomeado **testResultSummary.jsem**.

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>Implantar no Azure

Você pode usar o modelo de Azure Resource Manager e os arquivos de parâmetro gerados do Build para [implantar seu trabalho no Azure](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template).

## <a name="next-steps"></a>Próximas etapas

* [Integração contínua e implantação contínua para Azure Stream Analytics](cicd-overview.md)
* [Configurar o pipeline de CI/CD para Stream Analytics trabalho usando Azure Pipelines](set-up-cicd-pipeline.md)
