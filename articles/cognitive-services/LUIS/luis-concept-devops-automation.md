---
title: Fluxos de trabalho contínuos para aplicativos LUIS
description: Como implementar fluxos de trabalho de CI/CD para DevOps para o Reconhecimento vocal (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: 215399e4b131162097e54c15b84cb6fa7dac72e3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932532"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>Fluxos de trabalho de integração contínua e entrega contínua para DevOps do LUIS

Os engenheiros de software que estão desenvolvendo um aplicativo de Reconhecimento vocal (LUIS) podem aplicar práticas DevOpss em relação ao [controle do código-fonte](luis-concept-devops-sourcecontrol.md), [compilações automatizadas](luis-concept-devops-automation.md), [testes](luis-concept-devops-testing.md)e [Gerenciamento de liberações](luis-concept-devops-automation.md#release-management). Este artigo descreve os conceitos para implementar compilações automatizadas para o LUIS.

## <a name="build-automation-workflows-for-luis"></a>Criar fluxos de trabalho de automação para LUIS

![Fluxos de trabalho de CI](./media/luis-concept-devops-automation/luis-automation.png)

No seu sistema SCM (gerenciamento de código-fonte), configure pipelines de Build automatizados para execução nos seguintes eventos:

1. **PR fluxo de trabalho** disparado quando uma [solicitação pull](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) é gerada. Este fluxo de trabalho valida o conteúdo da PR *antes que* as atualizações sejam mescladas no Branch principal.
1. **Fluxo de trabalho de CI/CD** disparado quando as atualizações são enviadas para a ramificação principal, por exemplo, ao mesclar as alterações de uma pr. Esse fluxo de trabalho garante a qualidade de todas as atualizações para a ramificação principal.

O **fluxo de trabalho de CI/CD** combina dois processos de desenvolvimento complementares:

* A CI ( [integração contínua](/azure/devops/learn/what-is-continuous-integration) ) é a prática de engenharia do código com frequência de confirmação em um repositório compartilhado e a execução de uma compilação automatizada nele. Emparelhado com uma abordagem de [teste](luis-concept-devops-testing.md) automatizada, a integração contínua nos permite verificar se, para cada atualização, a origem do LUDown ainda é válida e pode ser importada para um aplicativo Luis, mas também que ele passa um grupo de testes que verificam se o aplicativo treinado pode reconhecer as intenções e as entidades necessárias para sua solução.

* A [entrega contínua](/azure/devops/learn/what-is-continuous-delivery) (CD) leva o conceito de integração contínua para implantar automaticamente o aplicativo em um ambiente no qual você pode fazer testes mais detalhados. O CD nos permite aprender antecipadamente sobre quaisquer problemas imprevistos que surjam de nossas alterações o mais rápido possível e também para aprender sobre lacunas em nossa cobertura de teste.

O objetivo da integração contínua e da entrega contínua é garantir que "Main seja sempre freqüentemente". Para um aplicativo LUIS, isso significa que poderíamos, se precisávamos, pegar qualquer versão do aplicativo LUIS do Branch principal e enviá-la para produção.

### <a name="tools-for-building-automation-workflows-for-luis"></a>Ferramentas para criar fluxos de trabalho de automação para LUIS

Há diferentes tecnologias de automação de compilação disponíveis para criar fluxos de trabalho de automação de compilação. Todos eles exigem que você possa gerar script de etapas usando uma CLI (interface de linha de comando) ou chamadas REST para que elas possam ser executadas em um servidor de compilação.

Use as seguintes ferramentas para criar fluxos de trabalho de automação para o LUIS:

* O [bot Framework Tools Luis a CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) para trabalhar com aplicativos e versões do Luis, treinar, testar e publicá-los no serviço Luis.

* [CLI do Azure](/cli/azure/) para consultar as assinaturas do Azure, buscar as chaves de criação e previsão do Luis e criar uma entidade de [serviço](/cli/azure/ad/sp) do Azure usada para autenticação de automação.

* [NLU. Ferramenta DevOps](https://github.com/microsoft/NLU.DevOps) para [testar um aplicativo Luis](luis-concept-devops-testing.md) e analisar os resultados do teste.

### <a name="the-pr-workflow"></a>O fluxo de trabalho de PR

Conforme mencionado, você configura esse fluxo de trabalho para ser executado quando um desenvolvedor gera uma PR para propor alterações a serem mescladas de uma ramificação de recurso no Branch principal. Sua finalidade é verificar a qualidade das alterações na PR antes que elas sejam mescladas ao Branch principal.

Esse fluxo de trabalho deve:

* Crie um aplicativo LUIS temporário importando a `.lu` origem na pr.
* Treine e publique a versão do aplicativo LUIS.
* Execute todos os [testes de unidade](luis-concept-devops-testing.md) em relação a ele.
* Passe o fluxo de trabalho se todos os testes forem aprovados, caso contrário, falhará.
* Limpe e exclua o aplicativo temporário.

Se houver suporte para o SCM, configure as regras de proteção de ramificação para que esse fluxo de trabalho deva ser concluído com êxito antes que a PR possa ser concluída.

### <a name="the-main-branch-cicd-workflow"></a>O fluxo de trabalho de CI/CD da ramificação principal

Configure este fluxo de trabalho para ser executado depois que as atualizações na PR tiverem sido mescladas na ramificação principal. Sua finalidade é manter a barra de qualidade do seu Branch principal alto testando as atualizações. Se as atualizações atenderem à barra de qualidade, esse fluxo de trabalho implantará a nova versão do aplicativo LUIS em um ambiente em que você possa fazer testes mais detalhados.

Esse fluxo de trabalho deve:

* Crie uma nova versão em seu aplicativo LUIS primário (o aplicativo que você mantém para a ramificação principal) usando o código-fonte atualizado.

* Treine e publique a versão do aplicativo LUIS.

  > [!NOTE]
  > Conforme explicado em [executando testes em um fluxo de trabalho de build automatizado](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow) , você deve publicar a versão do aplicativo Luis em teste para que as ferramentas, como NLU. O DevOps pode acessá-lo. O LUIS dá suporte apenas a dois slots de publicação nomeados, de *preparo* e de *produção* para um aplicativo Luis, mas você também pode [publicar uma versão diretamente](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) e [consultar por versão](./luis-migration-api-v3.md#changes-by-slot-name-and-version-name). Use a publicação de versão direta em seus fluxos de trabalho de automação para evitar ser limitado a usar os slots de publicação nomeados.

* Execute todos os [testes de unidade](luis-concept-devops-testing.md).

* Opcionalmente, execute [testes em lotes](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing) para medir a qualidade e a precisão da versão do aplicativo Luis e compará-la com alguma linha de base.

* Se os testes forem concluídos com êxito:
  * Marque a origem no repositório.
  * Execute o trabalho de entrega contínua (CD) para implantar a versão do aplicativo LUIS em ambientes para testes adicionais.

### <a name="continuous-delivery-cd"></a>CD (Entrega contínua)

O trabalho de CD em um Workflow de CI/CD é executado condicionalmente no sucesso da compilação e dos testes de unidade automatizados. Seu trabalho é implantar automaticamente o aplicativo LUIS em um ambiente em que você possa fazer mais testes.

Não há uma solução recomendada para a melhor implantação do aplicativo LUIS, e você deve implementar o processo apropriado para o seu projeto. O repositório de [modelos Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) implementa uma solução simples para isso, que é para [publicar a nova versão do aplicativo Luis](./luis-how-to-publish-app.md) no slot de publicação de *produção* . Isso é bom para uma configuração simples. No entanto, se você precisar dar suporte a vários ambientes de produção diferentes ao mesmo tempo, como *desenvolvimento*, *preparação* e *UAT*, o limite de dois slots de publicação nomeados por aplicativo ficará insuficiente.

Outras opções para implantar uma versão do aplicativo incluem:

* Deixe a versão do aplicativo publicada no ponto de extremidade de versão direta e implemente um processo para configurar ambientes de produção downstream com o ponto de extremidade de versão direta, conforme necessário.
* Mantenha diferentes aplicativos LUIS para cada ambiente de produção e grave as etapas de automação para importar o `.lu` para uma nova versão no aplicativo Luis para o ambiente de produção de destino, para treiná-lo e publicá-lo.
* Exporte a versão do aplicativo LUIS testada para um [contêiner do Docker do Luis](./luis-container-howto.md?tabs=v3) e implante o contêiner Luis nas [instâncias de contêiner](../../container-instances/index.yml)do Azure.

## <a name="release-management"></a>Gerenciamento de liberações

Em geral, é recomendável que você faça entrega contínua somente para seus ambientes de não produção, como para desenvolvimento e preparo. A maioria das equipes exige um processo manual de revisão e aprovação para a implantação em um ambiente de produção. Para uma implantação de produção, talvez você queira ter certeza de que ela acontece quando as principais pessoas da equipe de desenvolvimento estão disponíveis para suporte ou durante períodos de baixo tráfego.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [implementar o DevOps para Luis com o GitHub](luis-how-to-devops-with-github.md)
* Saiba como escrever um [fluxo de trabalho de ações do GitHub com o NLU. DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)
