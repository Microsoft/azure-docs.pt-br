---
title: Branch de desenvolvimento e controle do código-fonte – LUIS
description: Como manter seu aplicativo de Reconhecimento vocal (LUIS) no controle do código-fonte. Como aplicar atualizações a um aplicativo LUIS enquanto trabalha em uma ramificação de desenvolvimento.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 68d88ef667da9f22d3e3a17f10036693fcca0c3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932521"
---
# <a name="devops-practices-for-luis"></a>Práticas de DevOps para LUIS

Os engenheiros de software que estão desenvolvendo um aplicativo de Reconhecimento vocal (LUIS) podem aplicar práticas DevOpss sobre [controle do código-fonte](luis-concept-devops-sourcecontrol.md), [compilações automatizadas](luis-concept-devops-automation.md), [testes](luis-concept-devops-testing.md)e [Gerenciamento de liberações](luis-concept-devops-automation.md#release-management) seguindo estas diretrizes.

## <a name="source-control-and-branch-strategies-for-luis"></a>Controle do código-fonte e estratégias de branch do LUIS

Um dos fatores principais dos quais o sucesso do DevOps depende é o [controle do código-fonte](/azure/devops/user-guide/source-control). Um sistema de controle do código-fonte permite que os desenvolvedores colaborem no código e controlem as alterações. O uso de branches permite que os desenvolvedores alternem entre diferentes versões da base de código e trabalhem independentemente de outros membros da equipe. Quando os desenvolvedores geram uma [solicitação de pull](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) para propor atualizações de uma ramificação para outra, ou quando as alterações são mescladas, elas podem ser o gatilho para [compilações automatizadas](luis-concept-devops-automation.md) para compilar e testar continuamente o código.

Usando os conceitos e diretrizes descritos neste documento, você pode desenvolver um aplicativo LUIS enquanto controla alterações em um sistema de controle do código-fonte e seguir estas práticas recomendadas de engenharia de software:

- **Controle do código-fonte**
  - O código-fonte para seu aplicativo LUIS está em um formato legível.
  - O modelo pode ser criado a partir da origem de maneira repetitiva.
  - O código-fonte pode ser gerenciado por um repositório de código-fonte.
  - As credenciais e os segredos, como a criação e as chaves de assinatura, nunca são armazenados no código-fonte.

- **Ramificação e mesclagem**
  - Os desenvolvedores podem trabalhar de ramificações independentes.
  - Os desenvolvedores podem trabalhar em várias ramificações simultaneamente.
  - É possível integrar alterações a um aplicativo LUIS de uma ramificação em outra por meio de troca de base ou mesclagem.
  - Os desenvolvedores podem mesclar uma PR para a ramificação pai.

- **Controle de versão**
  - Cada componente em um aplicativo grande deve ser versionado de forma independente, permitindo que os desenvolvedores detectem alterações significativas ou atualizações apenas observando o número da versão.

- **Revisões de código**
  - As alterações na PR são apresentadas como código-fonte legível por humanos que pode ser revisado antes de aceitar a PR.

## <a name="source-control"></a>Controle do código-fonte

Para manter a [definição de esquema de aplicativo](./app-schema-definition.md) de um aplicativo Luis em um sistema de gerenciamento de código-fonte, use a representação de [formato LUDown ( `.lu` )](/azure/bot-service/file-format/bot-builder-lu-file-format)  do aplicativo. `.lu` o formato é preferencial para ser `.json` formatado porque é legível por humanos, o que torna mais fácil fazer e revisar alterações no PRS.

### <a name="save-a-luis-app-using-the-ludown-format"></a>Salvar um aplicativo LUIS usando o formato LUDown

Para salvar um aplicativo LUIS no `.lu` formato e colocá-lo no controle do código-fonte:

- OU: [exporte a versão do aplicativo](./luis-how-to-manage-versions.md#other-actions) como `.lu` no [portal do Luis](https://www.luis.ai/) e adicione-a ao seu repositório de controle do código-fonte

- OU: Use um editor de texto para criar um `.lu` arquivo para um aplicativo Luis e adicioná-lo ao seu repositório de controle do código-fonte

> [!TIP]
> Se você estiver trabalhando com a exportação JSON de um aplicativo LUIS, poderá [convertê-lo em LUDown](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert).  Use a `--sort` opção para garantir que as intenções e declarações sejam classificadas em ordem alfabética.  
> Observe que o **.** A funcionalidade de exportação de Lu interna no portal do Luis já classifica a saída.

### <a name="build-the-luis-app-from-source"></a>Criar o aplicativo LUIS da origem

Para um aplicativo LUIS, para criar a *partir do meio de origem* , [crie uma nova versão do aplicativo Luis importando a `.lu` origem](./luis-how-to-manage-versions.md#import-version) , para [treinar a versão](./luis-how-to-train.md) e [publicá-la](./luis-how-to-publish-app.md). Você pode fazer isso no portal do LUIS ou na linha de comando:

- Use o portal do LUIS para [importar a `.lu` versão](./luis-how-to-manage-versions.md#import-version) do aplicativo do controle do código-fonte e [treine](./luis-how-to-train.md) e [publique](./luis-how-to-publish-app.md) o aplicativo.

- Use a [interface de linha de comando do bot Framework para Luis](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) na linha de comando ou em um fluxo de trabalho de CI/CD para [importar](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) a `.lu` versão do aplicativo do controle do código-fonte para um aplicativo Luis e [treinar](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun) e [publicar](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) o aplicativo.

### <a name="files-to-maintain-under-source-control"></a>Arquivos a serem mantidos no controle do código-fonte

Os seguintes tipos de arquivos para seu aplicativo LUIS devem ser mantidos sob controle do código-fonte:

- `.lu` arquivo para o aplicativo LUIS

- [Arquivos de definição de teste de unidade](luis-concept-devops-testing.md#writing-tests) (declarações e resultados esperados)

- [Arquivos de teste em lotes](./luis-how-to-batch-test.md#batch-test-file) (declarações e resultados esperados) usados para teste de desempenho

### <a name="credentials-and-keys-are-not-checked-in"></a>Não há check-in de credenciais e chaves

Não inclua chaves de assinatura ou valores confidenciais semelhantes em arquivos que você faz check-in em seu repositório, onde eles podem estar visíveis para pessoas não autorizadas. As chaves e outros valores que você deve impedir do check-in incluem:

- Chaves de criação e previsão do LUIS
- Pontos de extremidade de criação e previsão do LUIS
- Chaves de assinatura do Azure
- Tokens de acesso, como o token para uma [entidade de serviço](/cli/azure/ad/sp) do Azure usada para autenticação de automação

#### <a name="strategies-for-securely-managing-secrets"></a>Estratégias para o gerenciamento seguro de segredos

As estratégias para o gerenciamento seguro de segredos incluem:

- Se você estiver usando o controle de versão do git, poderá armazenar os segredos de tempo de execução em um arquivo local e impedir o check-in do arquivo adicionando um padrão para corresponder o nome de arquivo a um arquivos [. gitignore](https://git-scm.com/docs/gitignore)
- Em um fluxo de trabalho de automação, você pode armazenar segredos com segurança na configuração de parâmetros oferecida por essa tecnologia de automação. Por exemplo, se você estiver usando [ações do GitHub](https://github.com/features/actions), poderá armazenar segredos com segurança nos [segredos do GitHub](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).

## <a name="branching-and-merging"></a>Ramificação e mesclagem

Os sistemas de controle de versão distribuídos, como o Git, oferecem flexibilidade para a publicação, o compartilhamento, a análise e a iteração de alterações de código por meio de branches de desenvolvimento compartilhados com outras pessoas. Adote uma [estratégia de ramificação git](/azure/devops/repos/git/git-branching-guidance) apropriada para sua equipe.

Seja qual for a estratégia de ramificação que você adotar, um princípio fundamental de todos eles é que os membros da equipe podem trabalhar na solução dentro de um *Branch de recursos* independentemente do trabalho que está acontecendo em outras ramificações.

Para dar suporte a trabalho independente em branches com um projeto LUIS:

- **O Branch principal tem seu próprio aplicativo LUIS.** Este aplicativo representa o estado atual de sua solução para seu projeto e sua versão ativa atual sempre deve mapear para a `.lu` origem que está na ramificação principal. Todas as atualizações na `.lu` origem para esse aplicativo devem ser examinadas e testadas para que esse aplicativo possa ser implantado para criar ambientes como a produção a qualquer momento. Quando as atualizações do `.lu` são mescladas em Main de um Branch de recurso, você deve criar uma nova versão no aplicativo Luis e aumentar [o número de versão](#versioning).

- **Cada ramificação de recurso deve usar sua própria instância de um aplicativo Luis**. Os desenvolvedores trabalham com esse aplicativo em uma ramificação de recursos sem o risco de afetar os desenvolvedores que trabalham em outras ramificações. Este aplicativo de ' ramificação de desenvolvimento ' é uma cópia funcional que deve ser excluída quando a ramificação de recurso é excluída.

![Ramificação de recurso git](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>Os desenvolvedores podem trabalhar de ramificações independentes

Os desenvolvedores podem trabalhar em atualizações em um aplicativo LUIS independentemente de outros branches:

1. Criar uma ramificação de recurso a partir da ramificação principal (dependendo da sua estratégia de ramificação, geralmente principal ou desenvolva).

1. [Crie um novo aplicativo Luis no portal do Luis](./luis-how-to-start-new-app.md) (o "*aplicativo de ramificação de desenvolvimento*") somente para dar suporte ao trabalho na ramificação de recurso.

   * Se a `.lu` origem da sua solução já existir em seu Branch, pois ela foi salva após o trabalho feito em outra ramificação anterior no projeto, crie seu aplicativo Luis Branch de desenvolvimento importando o `.lu` arquivo.

   * Se você estiver iniciando o trabalho em um novo projeto, ainda não terá a `.lu` origem do seu aplicativo Luis principal no repositório. Você criará o `.lu` arquivo exportando seu aplicativo de Branch de desenvolvimento do portal quando tiver concluído o trabalho de ramificação de recursos e o enviará como parte de sua pr.

1. Trabalhe na versão ativa do seu aplicativo de Branch de desenvolvimento para implementar as alterações necessárias. É recomendável que você trabalhe apenas em uma única versão do seu aplicativo de ramificação de desenvolvimento para todo o trabalho de ramificação de recursos. Se você criar mais de uma versão em seu aplicativo de Branch de desenvolvimento, tenha cuidado para controlar qual versão contém as alterações que você deseja fazer check-in ao gerar sua PR.

1. Testar as atualizações-consulte [Testing for Luis DevOps](luis-concept-devops-testing.md) para obter detalhes sobre como testar seu aplicativo de Branch de desenvolvimento.

1. Exporte a versão ativa do seu aplicativo de Branch `.lu` de desenvolvimento da [lista de versões](./luis-how-to-manage-versions.md).

1. Faça check-in de suas atualizações e convide a revisão de pares de suas atualizações. Se você estiver usando o GitHub, você gerará uma [solicitação de pull](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests).

1. Quando as alterações forem aprovadas, mescle as atualizações na ramificação principal. Neste ponto, você criará uma nova [versão](./luis-how-to-manage-versions.md) do aplicativo Luis *principal* , usando o atualizado `.lu` no principal. Consulte [controle de versão](#versioning) para obter considerações sobre como definir o nome da versão.

1. Quando o Branch de recurso é excluído, é uma boa ideia excluir o aplicativo LUIS Branch de desenvolvimento que você criou para o trabalho de ramificação de recursos.

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>Os desenvolvedores podem trabalhar em várias ramificações simultaneamente

Se você seguir o padrão descrito acima em [os desenvolvedores puderem trabalhar de ramificações independentes](#developers-can-work-from-independent-branches), você usará um aplicativo Luis exclusivo em cada ramificação de recurso. Um único desenvolvedor pode trabalhar em várias ramificações simultaneamente, desde que mudem para o aplicativo LUIS de Branch de desenvolvimento correto para o Branch no qual estão trabalhando no momento.

É recomendável que você use o mesmo nome para o Branch de recurso e para o aplicativo LUIS Branch de desenvolvimento que você cria para o trabalho de ramificação de recursos, para tornar menos provável que você trabalhe acidentalmente no aplicativo errado.

Conforme observado acima, recomendamos que, para simplificar, você trabalhe em uma única versão em cada aplicativo de Branch de desenvolvimento. Se você estiver usando várias versões, tome cuidado para ativar a versão correta conforme alternar entre os aplicativos de Branch de desenvolvimento.

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>Vários desenvolvedores podem trabalhar na mesma ramificação simultaneamente

Você pode dar suporte a vários desenvolvedores que trabalham na mesma ramificação de recursos ao mesmo tempo:

- Os desenvolvedores verificam o mesmo Branch de recursos e enviam por push e efetuam pull de alterações enviadas por si mesmos e por outros desenvolvedores enquanto o trabalho continua, como de costume.

- Se você seguir o padrão descrito acima em [os desenvolvedores puderem trabalhar de ramificações independentes](#developers-can-work-from-independent-branches), essa ramificação usará um aplicativo Luis exclusivo para dar suporte ao desenvolvimento. Esse aplicativo LUIS ' Branch de desenvolvimento ' será criado pelo primeiro membro da equipe de desenvolvimento que começa a trabalhar na ramificação de recursos.

- [Adicione membros da equipe como colaboradores](./luis-how-to-collaborate.md) ao aplicativo Luis Branch de desenvolvimento.

- Quando o trabalho de ramificação de recursos for concluído, exporte a versão ativa do aplicativo LUIS Branch de desenvolvimento como `.lu` na [lista versões](./luis-how-to-manage-versions.md), salve o `.lu` arquivo atualizado no repositório e faça check-in e PR as alterações.

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>Incorporando alterações de uma ramificação para outra com a troca de base ou mesclagem

Alguns outros desenvolvedores da sua equipe que trabalham em outra ramificação podem ter feito atualizações na `.lu` origem e mesclá-los no Branch principal após a criação do Branch de recursos. Talvez você queira incorporar suas alterações em sua versão de trabalho antes de continuar a fazer alterações no seu Branch de recursos. Você pode fazer isso [rebasear ou mesclar para o principal](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) da mesma forma que qualquer outro ativo de código. Como o aplicativo LUIS no formato LUDown é legível por humanos, ele dá suporte à mesclagem usando ferramentas de mesclagem padrão.

Siga estas dicas se estiver rebaseando seu aplicativo LUIS em uma ramificação de recurso:

- Antes de trocar de base ou mesclar, certifique-se de que sua cópia local da `.lu` origem do seu aplicativo tenha todas as alterações mais recentes que você aplicou usando o portal do Luis, exportando novamente seu aplicativo do portal primeiro. Dessa forma, você pode garantir que todas as alterações feitas no portal e ainda não exportadas não sejam perdidas.

- Durante a mesclagem, use ferramentas padrão para resolver conflitos de mesclagem.

- Não se esqueça depois que a troca de base ou a mesclagem estiver concluída para importar novamente o aplicativo de volta para o portal, para que você esteja trabalhando com o aplicativo atualizado enquanto você continua a aplicar suas próprias alterações.

### <a name="merge-prs"></a>Mesclar PRs

Depois que sua PR for aprovada, você poderá mesclar suas alterações em seu Branch principal. Nenhuma consideração especial se aplica à origem do LUDown para um aplicativo LUIS: é legível por humanos e, portanto, dá suporte à mesclagem usando ferramentas de mesclagem padrão. Quaisquer conflitos de mesclagem podem ser resolvidos da mesma maneira que com outros arquivos de origem.

Depois que sua PR foi mesclada, é recomendável limpar:

- Excluir a ramificação em seu repositório

- Exclua o aplicativo LUIS ' Branch de desenvolvimento ' criado para o trabalho de ramificação de recurso.

Da mesma maneira que com os ativos de código do aplicativo, você deve escrever testes de unidade para acompanhar as atualizações do aplicativo LUIS. Você deve empregar fluxos de trabalho de integração contínua para testar:

- Atualizações em uma PR antes de a PR ser mesclada
- O aplicativo LUIS do Branch principal após uma PR foi aprovado e as alterações foram mescladas no principal.

Para obter mais informações sobre o teste do LUIS DevOps, consulte [Testing for DevOps for Luis](luis-concept-devops-testing.md). Para obter mais detalhes sobre como implementar fluxos de trabalho, consulte [fluxos de trabalho de automação para Luis DevOps](luis-concept-devops-automation.md).

## <a name="code-reviews"></a>Revisões de código

Um aplicativo LUIS no formato LUDown é legível por humanos, que dá suporte à comunicação de alterações em uma PR, adequada para revisão. Os arquivos de teste de unidade também são escritos no formato LUDown e também podem ser facilmente reexibidos em uma PR.

## <a name="versioning"></a>Controle de versão

Um aplicativo consiste em vários componentes que podem incluir coisas como um bot em execução no [serviço de bot do Azure](/azure/bot-service/bot-service-overview-introduction), [QnA Maker](https://www.qnamaker.ai/), [serviço de fala do Azure](../speech-service/overview.md)e muito mais. Para atingir o objetivo de aplicativos menos rígidos, use o [controle de versão](/azure/devops/learn/git/what-is-version-control) para que cada componente de um aplicativo tenha versão independente, permitindo que os desenvolvedores detectem alterações significativas ou atualizações apenas examinando o número de versão. É mais fácil fazer a versão do seu aplicativo LUIS independentemente de outros componentes, se você o mantiver em seu próprio repositório.

O aplicativo LUIS para a ramificação principal deve ter um esquema de controle de versão aplicado. Ao mesclar atualizações para o `.lu` para um aplicativo Luis no principal, você importará essa origem atualizada para uma nova versão no aplicativo Luis para a ramificação principal.

É recomendável que você use um esquema de controle de versão numérico para a versão principal do aplicativo LUIS, por exemplo:

`major.minor[.build[.revision]]`

Cada atualização do número de versão é incrementada no último dígito.

A versão principal/secundária pode ser usada para indicar o escopo das alterações na funcionalidade do aplicativo LUIS:

* Versão principal: uma alteração significativa, como suporte para uma nova [tentativa](./luis-concept-intent.md) ou [entidade](./luis-concept-entity-types.md)
* Versão secundária: uma alteração secundária compatível com versões anteriores, como após um novo treinamento significativo
* Compilação: nenhuma alteração de funcionalidade, apenas uma compilação diferente.

Depois de determinar o número de versão para a revisão mais recente do seu aplicativo LUIS principal, você precisará criar e testar a nova versão do aplicativo e publicá-la em um ponto de extremidade onde ela possa ser usada em diferentes ambientes de compilação, como garantia de qualidade ou produção. É altamente recomendável que você automatize todas essas etapas em um fluxo de trabalho de CI (integração contínua).

Consulte:
- [Fluxos de trabalho de automação](luis-concept-devops-automation.md) para obter detalhes sobre como implementar um fluxo de trabalho de CI para testar e liberar um aplicativo Luis.
- [Release Management](luis-concept-devops-automation.md#release-management) para obter informações sobre como implantar seu aplicativo Luis.

### <a name="versioning-the-feature-branch-luis-app"></a>Controle de versão do aplicativo LUIS ' ramificação de recurso '

Quando você estiver trabalhando com um aplicativo LUIS "Branch de desenvolvimento" que você criou para dar suporte ao trabalho em uma ramificação de recursos, você exportará seu aplicativo quando seu trabalho for concluído e você incluirá o atualizado `'lu` em seu pr. A ramificação em seu repositório e o aplicativo LUIS ' Branch de desenvolvimento ' devem ser excluídos depois que a PR é mesclada em Main. Como esse aplicativo existe apenas para dar suporte ao trabalho na ramificação de recursos, não há nenhum esquema de controle de versão específico que você precise aplicar nesse aplicativo.

Quando as alterações na sua PR são mescladas no principal, isso ocorre quando o controle de versão deve ser aplicado, de modo que todas as atualizações para o principal tenham controle de versão de forma independente.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [teste para Luis DevOps](luis-concept-devops-testing.md)
* Saiba como [implementar o DevOps para Luis com o GitHub](luis-how-to-devops-with-github.md)
