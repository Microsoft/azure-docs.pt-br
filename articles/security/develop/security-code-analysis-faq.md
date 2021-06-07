---
title: FAQ da documentação de análise de código de segurança da Microsoft
description: Saiba mais sobre a extensão de análise de código de segurança da Microsoft examinando perguntas frequentes (FAQs).
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 3bff42b9b82104909c79b75c7b1de51c9ddb6e0d
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801308"
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes

> [!Note]
> A partir de 1º de março de 2022, a extensão MSCA (análise de código de segurança da Microsoft) será desativada. Os clientes MSCA existentes manterão seu acesso ao MSCA até 1º de março de 2022. Consulte as ferramentas de [análise de código-fonte OWASP](https://owasp.org/www-community/Source_Code_Analysis_Tools) para obter opções alternativas no Azure DevOps. Para os clientes que planejam migrar para o GitHub, você pode conferir a [segurança avançada do GitHub](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

Tem dúvidas? Confira as perguntas frequentes a seguir para obter mais informações.

## <a name="general-faq"></a>Perguntas frequentes gerais

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Posso instalar a extensão na minha instância do Visual Studio Team Foundation Server em vez de em uma instância do Azure DevOps?

Não. A extensão não está disponível para download e instalação para o Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>É necessário executar a análise de código de segurança da Microsoft com minha compilação? 

Talvez. Depende do tipo de ferramenta de análise. O código-fonte pode ser a única coisa necessária ou a saída da compilação pode ser necessária.

Por exemplo, o verificador de credenciais (CredScan) analisa arquivos dentro da estrutura de pastas do repositório de códigos. Devido a essa análise, você pode executar as tarefas de compilação CredScan e publicar logs de análise de segurança em uma compilação autônoma para obter resultados.

Para outras ferramentas como BinSkim que analisam os artefatos de pós-compilação, a compilação é necessária primeiro.

### <a name="can-i-break-my-build-when-results-are-found"></a>Posso interromper minha compilação quando os resultados são encontrados?

Sim. Você pode introduzir uma quebra de compilação quando qualquer ferramenta relata um problema ou problema em seu arquivo de log. Basta adicionar a tarefa de compilação pós-análise e marcar a caixa de seleção de qualquer ferramenta para a qual você deseja interromper a compilação.

Na interface do usuário da tarefa de pós-análise, você pode optar por interromper a compilação quando qualquer ferramenta relatar somente erros ou erros e avisos.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Como os argumentos de linha de comando no Azure DevOps diferem desses argumentos nas ferramentas de área de trabalho autônoma? 

Para a maior parte, as tarefas de compilação do Azure DevOps são wrappers diretos em torno dos argumentos de linha de comando das ferramentas de segurança. Você pode passar como argumentos para uma tarefa de compilação, tudo o que normalmente passa para uma ferramenta de linha de comando.

Diferenças perceptíveis:

- Ferramentas executadas na pasta de origem do agente $ (Build. SourcesDirectory) ou de% BUILD_SOURCESDIRECTORY%. Um exemplo é C:\agent \_ work\1\s.
- Os caminhos nos argumentos podem ser relativos à raiz do diretório de origem listado anteriormente. Os caminhos também podem ser absolutos. Você obtém caminhos absolutos usando variáveis de compilação DevOps do Azure ou executando um agente local com locais de implantação conhecidos de recursos locais.
- As ferramentas fornecem automaticamente um caminho ou pasta de arquivo de saída. Se você fornecer um local de saída para uma tarefa de compilação, esse local será substituído por um caminho para nosso local conhecido de logs no agente de compilação
- Alguns argumentos de linha de comando adicionais são alterados para algumas ferramentas. Um exemplo é a adição ou remoção de opções que garantem que nenhuma GUI seja iniciada.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Posso executar uma tarefa de compilação como o verificador de credenciais em vários repositórios em uma compilação DevOps do Azure?

Não. Não há suporte para a execução de ferramentas de desenvolvimento seguro em vários repositórios em um único pipeline.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>O arquivo de saída especificado não está sendo criado ou não consigo encontrar o arquivo de saída que especifiquei

As tarefas de compilação filtram alguma entrada do usuário. Para essa pergunta especificamente, eles atualizam o local do arquivo de saída gerado para ser um local comum no agente de compilação. Para obter mais informações sobre esse local, consulte as perguntas a seguir.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Onde os arquivos de saída são gerados pelas ferramentas salvas? 

As tarefas de compilação adicionam automaticamente caminhos de saída a esse local conhecido no agente de compilação: $ (Agent. BuildDirectory) \_ sdt\logs. Como padronizamos esse local, todas as equipes que produzem ou consomem logs de análise de código têm acesso à saída.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Posso colocar uma compilação em fila para executar essas tarefas em um agente de compilação hospedado? 

Sim. Todas as tarefas e ferramentas na extensão podem ser executadas em um agente de compilação hospedado.

>[!NOTE]
> A tarefa de compilação de scanner antimalware requer um agente de compilação com o Windows Defender habilitado. O Visual Studio 2017 hospedado e posterior fornece um agente desse tipo. A tarefa de compilação não será executada no agente hospedado do Visual Studio 2015.
>
> Embora as assinaturas não possam ser atualizadas nesses agentes, as assinaturas devem ter sempre menos de três horas de idade.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Posso executar essas tarefas de compilação como parte de um pipeline de lançamento em vez de um pipeline de compilação?

Na maioria dos casos, sim.

No entanto, o Azure DevOps não dá suporte a tarefas em execução em pipelines de versão quando essas tarefas publicam artefatos. Essa falta de suporte impede que a tarefa publicar logs de análise de segurança seja executada com êxito em um pipeline de liberação. Em vez disso, a tarefa falhará com uma mensagem de erro descritiva.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>De onde as tarefas de compilação baixam as ferramentas?

As tarefas de compilação podem baixar os pacotes NuGet das ferramentas do [feed gerenciamento de pacotes do Azure DevOps](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). As tarefas de compilação também podem usar o Gerenciador de pacotes de nó, que deve ser pré-instalado no agente de compilação. Um exemplo de tal instalação é o comando **NPM install tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Que efeito a instalação da extensão tem na minha organização do Azure DevOps? 

Após a instalação, as tarefas de compilação de segurança fornecidas pela extensão ficam disponíveis para todos os usuários em sua organização. Quando você cria ou edita um pipeline do Azure, essas tarefas estão disponíveis na lista coleção de tarefas de compilação. Caso contrário, instalar a extensão em sua organização DevOps do Azure não terá nenhum efeito. A instalação não modifica nenhuma configuração de conta, configurações de projeto ou pipelines.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>A instalação da extensão modifica o Azure Pipelines existente? 

Não. A instalação da extensão torna as tarefas de compilação de segurança disponíveis para adição aos seus pipelines. Você ainda precisa adicionar ou atualizar definições de compilação, para que as ferramentas possam trabalhar com o processo de compilação.

## <a name="task-specific-faq"></a>Perguntas frequentes específicas da tarefa

As perguntas específicas para tarefas de compilação são listadas nesta seção.

### <a name="credential-scanner"></a>Verificador de credenciais

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>O que são cenários e exemplos de supressão comuns?

Aqui estão os detalhes de dois dos cenários de supressão mais comuns.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Para suprimir todas as ocorrências de um determinado segredo dentro do caminho especificado

A chave de hash do segredo do arquivo de saída CredScan é necessária, conforme mostrado no exemplo a seguir.

```js
{
    "tool": "Credential Scanner",
    "suppressions": [
    {
        "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
        "_justification": "Secret used by MSDN sample, it is fake."
    }
  ]
}
```

>[!WARNING]
> A chave de hash é gerada por uma parte do valor correspondente ou do conteúdo do arquivo. Qualquer revisão de código-fonte pode alterar a chave de hash e desabilitar a regra de supressão.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Para suprimir todos os segredos em um arquivo especificado ou suprimir o próprio arquivo de segredos

A expressão de arquivo pode ser um nome de arquivo. Ele também pode ser a parte basename de um caminho de arquivo completo ou um nome de arquivo. Não há suporte para caracteres curinga.

Os exemplos a seguir mostram como suprimir o arquivo \<InputPath>\src\JS\lib\angular.js

Exemplos de regras de supressão válidas:

- \<InputPath>\src\JS\lib\angular.js-suprime o arquivo no caminho especificado
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js-suprime qualquer arquivo com o mesmo nome

```js
{
    "tool": "Credential Scanner",
    "suppressions": [
    {
        "file": "\\files\\AdditonalSearcher.xml", 
        "_justification": "Additional CredScan searcher specific to my team"
    },
    {
        "file": "\\files\\unittest.pfx", 
        "_justification": "Legitimate UT certificate file with private key"
    }
  ]
}
```

>[!WARNING] 
> Todos os segredos futuros adicionados ao arquivo também serão suprimidos automaticamente.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Quais são as diretrizes recomendadas para o gerenciamento de segredos?

Os recursos a seguir ajudam você a gerenciar com segurança segredos e acessar informações confidenciais de dentro de seus aplicativos:

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Active Directory do Azure (Azure AD)](../../azure-sql/database/authentication-aad-overview.md)
 - [Identidade de Serviço Gerenciada do Azure AD (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Identidades gerenciadas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Identidades gerenciadas no serviço Azure App e Azure Functions](../../app-service/overview-managed-identity.md)
 - [Biblioteca AppAuthentication](/dotnet/api/overview/azure/service-to-service-authentication)


Para obter mais informações, consulte a postagem de blog [Gerenciando segredos com segurança na nuvem](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/).

#### <a name="can-i-write-my-own-custom-searchers"></a>Posso escrever meus próprios pesquisadores personalizados?

O verificador de credenciais depende de um conjunto de pesquisa de conteúdo que são normalmente definidos no arquivo de buildsearchers.xml. O arquivo contém uma matriz de objetos serializados XML que representam um objeto **ContentSearcher** . O programa é distribuído com um conjunto de pesquisadores bem testados. Mas você também pode implementar seus próprios pesquisadores personalizados.

Um pesquisador de conteúdo é definido da seguinte maneira:

- **Nome**: o nome descritivo do pesquisador a ser usado em arquivos de saída do verificador de credenciais. Recomendamos que você use a Convenção de nomenclatura do camel case para nomes de pesquisador.
- **RuleId**: a ID opaca estável do pesquisador:
    - Um pesquisador de credenciais padrão recebe um valor **RuleId** como CSCAN0010, CSCAN0020 ou CSCAN0030. O último dígito é reservado para potencialmente mesclagem ou divisão de grupos de pesquisa por meio de expressões regulares (Regex).
    - O valor de **RuleId** para um pesquisador personalizado deve ter seu próprio namespace. Os exemplos incluem CSCAN- \<Namespace\> 0010, CSCAN- \<Namespace\> 0020 e CSCAN- \<Namespace\> 0030.
    - Um nome do pesquisador totalmente qualificado é a combinação de um valor de **RuleId** e um nome de pesquisador. Os exemplos incluem CSCAN0010. KeyStoreFiles e CSCAN0020. Base64EncodedCertificate.
- **ResourceMatchPattern**: Regex de extensões de arquivo para verificar no Pesquisador.
- **ContentSearchPatterns**: uma matriz de cadeias de caracteres que contém instruções Regex para fazer a correspondência. Se nenhum padrão de pesquisa for definido, todos os arquivos correspondentes ao valor de **ResourceMatchPattern** serão retornados.
- **ContentSearchFilters**: uma matriz de cadeias de caracteres que contém instruções Regex para filtrar falsos positivos específicos do pesquisador.
- **MatchDetails**: uma mensagem descritiva, instruções de mitigação ou ambas a serem adicionadas para cada correspondência do pesquisador.
- **Recomendação**: o conteúdo do campo de sugestões para uma correspondência usando o formato de relatório PREfast.
- **Severidade**: um inteiro que reflete o nível de severidade de um problema. O nível de severidade mais alto tem o valor 1.

  ![XML mostrando a instalação do verificador de credenciais](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Analisadores Roslyn

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Quais são os erros comuns ao usar a tarefa analisadores de Roslyn?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>O projeto foi restaurado usando uma versão incorreta do Microsoft.NETCore.App

A mensagem de erro completa:

"Erro: o projeto foi restaurado usando a versão Microsoft.NETCore.App x. x *. x*, mas com as configurações atuais, a versão *y* . y seria usada em seu lugar. Para resolver esse problema, verifique se as mesmas configurações são usadas para restauração e para as operações seguintes, como build ou publicação. Normalmente, esse problema pode ocorrer se a propriedade RuntimeIdentifier é definida durante o build ou a publicação, mas não durante a restauração."

Como as tarefas de analisadores Roslyn são executadas como parte da compilação, a árvore de origem no computador de compilação precisa estar em um estado compilável.

Uma etapa entre as etapas da compilação principal e dos analisadores Roslyn pode colocar a árvore de origem em um estado que impede a criação. Essa etapa extra provavelmente **dotnet.exe publicar**. Tente duplicar a etapa que faz uma restauração do NuGet logo antes da etapa dos analisadores de Roslyn. Essa etapa duplicada pode colocar a árvore de origem de volta em um estado compilável.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe não pode criar uma instância do analisador

A mensagem de erro completa:

"' csc.exe ' saiu com o código de erro 1--uma instância do Analyzer *aaaa* não pode ser criada a partir de C: \\ *bbbb*. dll: não foi possível carregar o arquivo ou assembly ' Microsoft. CodeAnalysis, Version =*X. x. x. x*, Culture = neutral, PublicKeyToken = 31bf3856ad364e35 ' ou uma de suas dependências. O sistema não pode localizar o arquivo especificado."

Verifique se o compilador dá suporte a analisadores Roslyn. Executar o comando **csc.exe/Version** deve relatar um valor de versão de 2,6 ou posterior.

Às vezes, um arquivo. csproj pode substituir a instalação do Visual Studio da máquina de compilação referenciando um pacote de Microsoft.Net. compilers. Se você não pretende usar uma versão específica do compilador, remova as referências a Microsoft.Net. compilers. Caso contrário, verifique se a versão do pacote referenciado também é 2,6 ou posterior.

Tente obter o caminho do log de erros, que é especificado na opção **csc.exe/ErrorLog** . A opção e o caminho aparecem no log para a tarefa de compilação de analisadores de Roslyn. Eles podem ter uma aparência semelhante a **/ErrorLog: f:\ts-services-123 \_ work\456\s\Some\Project\Code\Code.csproj.Sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>A versão do compilador C# não é recente o suficiente

Para obter as versões mais recentes do compilador C#, vá para [Microsoft.net. compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers). Para obter a versão instalada, execute **csc.exe/Version** em um prompt de comando. Certifique-se de fazer referência a um pacote NuGet do Microsoft.Net. compilers que seja da versão 2,6 ou posterior.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>Os logs do MSBuild e do VSBuild não foram encontrados

A tarefa de compilação de analisadores Roslyn precisa consultar o Azure DevOps para o log do MSBuild da tarefa de compilação do MSBuild. Se a tarefa do analisador for executada imediatamente após a tarefa do MSBuild, o log ainda não estará disponível. Coloque outras tarefas entre a tarefa do MSBuild e a tarefa analisadores de Roslyn. Exemplos de outras tarefas incluem o BinSkim e o scanner antimalware.

## <a name="next-steps"></a>Próximas etapas

Se você precisar de assistência adicional, o suporte à análise de código de segurança da Microsoft estará disponível de segunda a sexta-feira, de 9:00 às 5:00, hora oficial do Pacífico.

- Integração: consulte nossa [documentação de integração](security-code-analysis-onboard.md)
  
- Suporte: envie a equipe por email [para o suporte da análise de código de segurança da Microsoft](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)