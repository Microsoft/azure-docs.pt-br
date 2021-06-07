---
title: Personalizar tarefas de análise de código de segurança da Microsoft
titleSuffix: Azure
description: Este artigo descreve como personalizar as tarefas na extensão de análise de código de segurança da Microsoft
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
ms.openlocfilehash: ad395e1b782edb28845bb7db0607d2bab5b5697c
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802005"
---
# <a name="configure-and-customize-the-build-tasks"></a>Configurar e personalizar as tarefas de compilação

> [!Note]
> A partir de 1º de março de 2022, a extensão MSCA (análise de código de segurança da Microsoft) será desativada. Os clientes MSCA existentes manterão seu acesso ao MSCA até 1º de março de 2022. Consulte as ferramentas de [análise de código-fonte OWASP](https://owasp.org/www-community/Source_Code_Analysis_Tools) para obter opções alternativas no Azure DevOps. Para os clientes que planejam migrar para o GitHub, você pode conferir a [segurança avançada do GitHub](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

Este artigo descreve detalhadamente as opções de configuração disponíveis em cada uma das tarefas de compilação. O artigo começa com as tarefas de ferramentas de análise de código de segurança. Ele termina com as tarefas de pós-processamento.

## <a name="anti-malware-scanner-task"></a>Tarefa de scanner antimalware

>[!NOTE]
> A tarefa de compilação de scanner antimalware requer um agente de compilação com o Windows Defender habilitado. O Visual Studio 2017 hospedado e posterior fornece um agente desse tipo. A tarefa de compilação não será executada no agente hospedado do Visual Studio 2015.
>
> Embora as assinaturas não possam ser atualizadas nesses agentes, as assinaturas devem ter sempre menos de três horas de idade.

Os detalhes da configuração de tarefa são mostrados na captura de tela e no texto a seguir.

![Configurando a tarefa de compilação do scanner antimalware](./media/security-tools/5-add-anti-malware-task600.png)

Na caixa de listagem **tipo** da captura de tela, **básico** é selecionado. Selecione **personalizado** para fornecer argumentos de linha de comando que personalizam a verificação.

O Windows Defender usa o cliente Windows Update para baixar e instalar assinaturas. Se a atualização de assinatura falhar em seu agente de compilação, o código de erro **HRESULT** provavelmente será proveniente de Windows Update.

Para obter mais informações sobre erros de Windows Update e sua mitigação, consulte [Windows Update códigos de erro por componente](/windows/deployment/update/windows-update-error-reference) e o artigo do TechNet [Windows Update códigos de erro de agente](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

Para obter informações sobre a configuração de YAML para essa tarefa, verifique nossas [Opções de YAML de anti-malware](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>Tarefa BinSkim

> [!NOTE]
> Antes que você possa executar a tarefa BinSkim, sua compilação deve atender a uma destas condições:
>  - Sua compilação produz artefatos binários do código gerenciado.
>  - Você tem artefatos binários confirmados que deseja analisar com BinSkim.

Os detalhes da configuração de tarefa são mostrados na seguinte captura de tela e lista.

![Configurando a tarefa de compilação BinSkim](./media/security-tools/7-binskim-task-details.png)

- Defina a configuração de compilação a ser depurada para que os arquivos de depuração. pdb sejam produzidos. O BinSkim usa esses arquivos para mapear problemas nos binários de saída de volta para o código-fonte.
- Para evitar a pesquisa e a criação da sua própria linha de comando:
     - Na lista **tipo** , selecione **básico**.
     - Na lista de **funções** , selecione **analisar**.
- Em **destino**, insira um ou mais especificadores para um arquivo, diretório ou padrão de filtro. Esses especificadores são resolvidos para um ou mais binários a serem analisados:
    - Vários destinos especificados devem ser separados por um ponto-e-vírgula (;).
    - Um especificador pode ser um único arquivo ou conter curingas.
    - As especificações de diretório sempre devem terminar com \\ *.
    - Exemplos:

```binskim-targets
           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
```

- Se você selecionar **linha de comando** na lista **tipo** , precisará executar binskim.exe:
     - Certifique-se de que os primeiros argumentos para binskim.exe são a **análise** de verbo seguida por uma ou mais especificações de caminho. Cada caminho pode ser um caminho completo ou um caminho relativo ao diretório de origem.
     - Vários caminhos de destino devem ser separados por um espaço.
     - Você pode omitir a opção **/o** ou **/output** . O valor de saída é adicionado ou substituído.
     - As configurações de linha de comando padrão são mostradas a seguir.

```binskim-line-args
           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
```

> [!NOTE]
> A trilha \\ * é importante se você especificar os diretórios para o destino.

Para obter mais informações sobre argumentos de linha de comando BinSkim, regras por ID ou códigos de saída, consulte o [Guia do usuário do BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Para obter informações sobre a configuração de YAML para essa tarefa, verifique nossas [Opções de YAML do BinSkim](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Tarefa do verificador de credenciais

Os detalhes da configuração de tarefa são mostrados na seguinte captura de tela e lista.

![Configurando a tarefa de compilação do verificador de credenciais](./media/security-tools/3-taskdetails.png)

As opções disponíveis incluem:
  - **Nome de exibição**: o nome da tarefa DevOps do Azure. O valor padrão é executar o verificador de credenciais
  - **Versão principal da ferramenta**: os valores disponíveis incluem **CredScan v2**, **CredScan v1**. Recomendamos que os clientes usem a versão **CredScan v2** .
  - **Formato de saída**: os valores disponíveis incluem **TSV**, **CSV**, **SARIF** e **PREfast**.
  - **Versão da ferramenta**: Recomendamos que você selecione **mais recente**.
  - **Pasta de verificação**: a pasta do repositório a ser verificada.
  - **Tipo de arquivo do Searchers**: as opções para localizar o arquivo de pesquisa que é usado para verificação.
  - **Arquivo de supressão**: um arquivo [JSON](https://json.org/) pode suprimir problemas no log de saída. Para obter mais informações sobre cenários de supressão, consulte a seção de perguntas frequentes deste artigo.
  - **Saída detalhada**: autoexplicativa.
  - **Tamanho do lote**: o número de threads simultâneos usados para executar o verificador de credenciais. O valor padrão é 20. Os valores possíveis variam de 1 a 2.147.483.647.
  - **Tempo limite de correspondência**: a quantidade de tempo em segundos a ser gasto tentando uma correspondência de pesquisador antes de abandonar a verificação.
  - **Tamanho do buffer de leitura de verificação de arquivo**: o tamanho em bytes do buffer usado enquanto o conteúdo é lido. O valor padrão é 524.288.  
  - **Máximo de bytes lidos da verificação de arquivo**: o número máximo de bytes a serem lidos de um arquivo durante a análise de conteúdo. O valor padrão é 104.857.600.
  - **Opções**  >  de controle **Executar esta tarefa**: especifica quando a tarefa será executada. Selecione **condições personalizadas** para especificar condições mais complexas.
  - **Versão**: a versão da tarefa de compilação no Azure DevOps. Essa opção não é usada com frequência.

Para obter informações sobre a configuração de YAML para essa tarefa, verifique nossas [Opções de YAML do verificador de credenciais](yaml-configuration.md#credential-scanner-task)

## <a name="roslyn-analyzers-task"></a>Tarefa de analisadores de Roslyn

> [!NOTE]
> Antes de executar a tarefa analisadores de Roslyn, sua compilação precisa atender a estas condições:
>
> - Sua definição de compilação inclui a tarefa interna de compilação MSBuild ou VSBuild para compilar o código C# ou Visual Basic. A tarefa de analisadores depende da entrada e da saída da tarefa interna para executar a compilação do MSBuild com analisadores Roslyn habilitados.
> - O agente de compilação que executa essa tarefa de compilação tem o Visual Studio 2017 versão 15,5 ou posterior instalado, para que ele use o compilador versão 2,6 ou posterior.

Os detalhes da configuração de tarefa são mostrados na lista e observação a seguir.

As opções disponíveis incluem:

- **RuleSet**: os valores são o **SDL necessário**, o **SDL é recomendado** ou seu próprio conjunto de regras personalizado.
- **Versão do analisadores**: Recomendamos que você selecione **mais recente**.
- **Arquivo de supressões de avisos do compilador**: um arquivo de texto com uma lista de IDs de Avisos suprimidos.
- **Opções**  >  de controle **Executar esta tarefa**: especifica quando a tarefa será executada. Escolha **condições personalizadas** para especificar condições mais complexas.

> [!NOTE]
>
> - Os analisadores Roslyn são integrados ao compilador e podem ser executados somente como parte da compilação de csc.exe. Portanto, essa tarefa requer que o comando do compilador executado anteriormente na compilação seja reproduzido ou executado novamente. Essa reprodução ou execução é feita consultando o Azure DevOps (anteriormente Visual Studio Team Services) para os logs de tarefa de compilação do MSBuild.
>
>   Não há outra maneira para a tarefa obter de forma confiável a linha de comando de compilação do MSBuild da definição de compilação. Consideramos a adição de uma caixa de texto de forma livre para permitir que os usuários insiram suas linhas de comando. Mas seria difícil manter essas linhas de comando atualizadas e sincronizadas com a compilação principal.
>
>   As compilações personalizadas exigem a repetição de todo o conjunto de comandos, não apenas os comandos do compilador. Nesses casos, a habilitação de analisadores Roslyn não é trivial ou confiável.
>
> - Os analisadores Roslyn são integrados ao compilador. Para ser invocado, os analisadores Roslyn exigem compilação.
>
>   Essa nova tarefa de compilação é implementada pela recompilação de projetos C# que já foram criados. A nova tarefa usa apenas as tarefas de compilação MSBuild e VSBuild na mesma compilação ou definição de compilação que a tarefa original. No entanto, nesse caso, a nova tarefa as usa com os analisadores de Roslyn habilitados.
>
>   Se a nova tarefa for executada no mesmo agente que a tarefa original, a saída da nova tarefa substituirá a saída da tarefa original na pasta *s* sources. Embora a saída da compilação seja a mesma, aconselhamos que você execute o MSBuild, copie a saída para o diretório de preparo dos artefatos e, em seguida, execute os analisadores do Roslyn.

Para obter recursos adicionais para a tarefa analisadores de Roslyn, confira [os analisadores baseados em Roslyn](/dotnet/standard/analyzers/api-analyzer) no Microsoft docs.

Você pode encontrar o pacote do analisador instalado e usado por essa tarefa de compilação na página do NuGet [Microsoft. CodeAnalysis. FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Para obter informações sobre a configuração de YAML para essa tarefa, verifique nossas [Opções de YAML de analisadores de Roslyn](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>Tarefa TSLint

Para obter mais informações sobre TSLint, acesse o [repositório GitHub TSLint](https://github.com/palantir/tslint).

>[!NOTE] 
>Como você deve estar ciente, o [repositório GitHub TSLint](https://github.com/palantir/tslint) Home Page diz que o TSLint será preterido em algum momento em 2019. A Microsoft está investigando o [ESLint](https://github.com/eslint/eslint) como uma tarefa alternativa.

Para obter informações sobre a configuração de YAML para essa tarefa, verifique nossas [Opções de YAML do TSLint](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Tarefa publicar logs de análise de segurança

Os detalhes da configuração de tarefa são mostrados na seguinte captura de tela e lista.

![Configurando a tarefa de compilação de logs de análise de segurança de publicação](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Nome do artefato**: qualquer identificador de cadeia de caracteres.
- **Tipo de artefato**: dependendo da sua seleção, você pode publicar logs em seu Azure DevOps Server ou em um arquivo compartilhado que seja acessível para o agente de compilação.
- **Ferramentas**: você pode optar por preservar os logs para ferramentas específicas ou pode selecionar **todas as ferramentas** para preservar todos os logs.

Para obter informações sobre a configuração de YAML para essa tarefa, verifique nossas [Opções de YAML de logs de segurança de publicação](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Tarefa de relatório de segurança

Os detalhes da configuração do relatório de segurança são mostrados na seguinte captura de tela e lista.

![Configurando a tarefa de compilação do relatório de segurança](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Relatórios**: selecione qualquer um dos formatos do **console do pipeline**, do **arquivo TSV** e do **arquivo HTML** . Um arquivo de relatório é criado para cada formato selecionado.
- **Ferramentas**: selecione as ferramentas em sua definição de compilação para as quais você deseja um resumo dos problemas detectados. Para cada ferramenta selecionada, pode haver uma opção para selecionar se você vê somente erros ou vê erros e avisos no relatório de resumo.
- **Opções avançadas**: se não houver nenhum log para uma das ferramentas selecionadas, você poderá optar por registrar um aviso ou um erro. Se você registrar um erro, a tarefa falhará.
- **Pasta de logs de base**: você pode personalizar a pasta de logs de base onde os logs devem ser encontrados. Mas essa opção normalmente não é usada.

Para obter informações sobre a configuração de YAML para essa tarefa, verifique nossas [Opções de YAML de relatório de segurança](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>Tarefa de pós-análise

Os detalhes da configuração de tarefa são mostrados na seguinte captura de tela e lista.

![Configurando a tarefa de compilação pós-análise](./media/security-tools/a-post-analysis600.png)

- **Ferramentas**: selecione as ferramentas em sua definição de compilação para as quais você deseja injetar condicionalmente uma quebra de compilação. Para cada ferramenta selecionada, pode haver uma opção para selecionar se deseja interromper somente erros ou se há erros e avisos.
- **Relatório**: opcionalmente, você pode gravar os resultados que estão causando a quebra de compilação. Os resultados são gravados na janela do console DevOps do Azure e no arquivo de log.
- **Opções avançadas**: se não houver nenhum log para uma das ferramentas selecionadas, você poderá optar por registrar um aviso ou um erro. Se você registrar um erro, a tarefa falhará.

Para obter informações sobre a configuração de YAML para essa tarefa, verifique nossas [Opções de YAML de pós-análise](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre a configuração baseada em YAML, consulte nosso [Guia de configuração do YAML](yaml-configuration.md).

Se você tiver mais dúvidas sobre a extensão de análise de código de segurança e as ferramentas oferecidas, confira [nossa página de perguntas frequentes](security-code-analysis-faq.md).