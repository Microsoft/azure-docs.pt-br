---
title: CI/CD para o serviço de Fala Personalizada-fala
titleSuffix: Azure Cognitive Services
description: Aplicar DevOps com fluxos de trabalho Fala Personalizada e CI/CD. Implemente uma solução DevOps existente para seu próprio projeto.
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: f7f11ef097d3abee2b4c18c32a1cb215d9fe8ce8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939886"
---
# <a name="cicd-for-custom-speech"></a>CI/CD para Fala Personalizada

Implemente treinamento automatizado, teste e gerenciamento de liberações para habilitar a melhoria contínua de modelos de Fala Personalizada ao aplicar atualizações a dados de treinamento e teste. Por meio da implementação efetiva de fluxos de trabalho de CI/CD, você pode garantir que o ponto de extremidade para o melhor desempenho Fala Personalizada modelo esteja sempre disponível.

A CI ( [integração contínua](/azure/devops/learn/what-is-continuous-integration) ) é a prática de engenharia de confirmar atualizações com frequência em um repositório compartilhado e executar uma compilação automatizada nela. Os fluxos de trabalho de CI para Fala Personalizada treinam um novo modelo de suas fontes de dados e executam testes automatizados no novo modelo para garantir que ele tenha um desempenho melhor do que o modelo anterior.

A [entrega contínua](/azure/devops/learn/what-is-continuous-delivery) (CD) usa modelos do processo de CI e cria um ponto de extremidade para cada modelo de fala personalizada aprimorado. O CD torna os pontos de extremidade facilmente disponíveis para serem integrados em soluções.

As soluções personalizadas de CI/CD são possíveis, mas para uma solução robusta e predefinida, use o [repositório de modelos Speech DevOps](https://github.com/Azure-Samples/Speech-Service-DevOps-Template), que executa fluxos de trabalho de CI/CD usando ações do github.

## <a name="cicd-workflows-for-custom-speech"></a>Fluxos de trabalho de CI/CD para Fala Personalizada

A finalidade desses fluxos de trabalho é garantir que cada modelo de Fala Personalizada tenha uma precisão de reconhecimento melhor do que a compilação anterior. Se as atualizações dos dados de teste e/ou treinamento melhorarem a precisão, esses fluxos de trabalho criarão um novo ponto de extremidade Fala Personalizada.

Os servidores git, como GitHub e Azure DevOps, podem executar fluxos de trabalho automatizados quando eventos git específicos acontecem, como mesclagens ou solicitações pull. Por exemplo, um fluxo de trabalho de CI pode ser disparado quando as atualizações de dados de teste são enviadas por push para a ramificação *principal* . Diferentes servidores git terão ferramentas diferentes, mas permitirão comandos de CLI (interface de linha de comando) de script para que possam ser executados em um servidor de compilação.

Ao longo do caminho, os fluxos de trabalho devem nomear e armazenar dados, testes, arquivos de teste, modelos e pontos de extremidade, de forma que eles possam ser rastreados de volta para a confirmação ou a versão da qual vieram. Também é útil nomear esses ativos para que seja fácil ver quais foram criados após a atualização de dados de teste versus dados de treinamento.

### <a name="ci-workflow-for-testing-data-updates"></a>Fluxo de trabalho de CI para testar atualizações de dados

A finalidade principal dos fluxos de trabalho de CI/CD é criar um novo modelo usando os dados de treinamento e testar esse modelo usando os dados de teste para estabelecer se a [taxa de erros do Word](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (WER) foi aprimorada em comparação com o modelo de melhor desempenho anterior (o "modelo de parâmetro de comparação"). Se o novo modelo tiver um desempenho melhor, ele se tornará o novo modelo de benchmark em relação ao qual os modelos futuros são comparados.

O fluxo de trabalho de CI para testar atualizações de dados deve testar novamente o modelo de parâmetro de comparação atual com os dados de teste atualizados para calcular o WER revisado. Isso garante que quando o WER de um novo modelo for comparado com o WER do parâmetro de comparação, ambos os modelos foram testados nos mesmos dados de teste e você está comparando como.

Este fluxo de trabalho deve disparar atualizações para testar dados e:

- Teste o modelo de parâmetro de comparação em relação aos dados de teste atualizados.
- Armazene a saída de teste, que contém o WER do modelo de parâmetro de comparação, usando os dados atualizados.
- O WER a partir desses testes se tornará o novo parâmetro de comparação de parâmetros que os futuros modelos devem superar.
- O fluxo de trabalho do CD não é executado para atualizações de dados de teste.

### <a name="ci-workflow-for-training-data-updates"></a>Fluxo de trabalho de CI para atualizações de dados de treinamento

As atualizações dos dados de treinamento significam atualizações para o modelo personalizado.

Este fluxo de trabalho deve disparar atualizações para dados de treinamento e:

- Treine um novo modelo com os dados de treinamento atualizados.
- Teste o novo modelo em relação aos dados de teste.
- Armazene a saída de teste, que contém o WER.
- Compare o WER do novo modelo com o WER a partir do modelo de parâmetro de comparação.
- Se o WER não melhorar, pare o fluxo de trabalho.
- Se o WER melhorar, execute o fluxo de trabalho do CD para criar um ponto de extremidade Fala Personalizada.

### <a name="cd-workflow"></a>Fluxo de trabalho do CD

Depois que uma atualização para os dados de treinamento melhora o reconhecimento de um modelo, o fluxo de trabalho do CD deve ser executado automaticamente para criar um novo ponto de extremidade para esse modelo e tornar esse ponto de extremidade disponível para que ele possa ser usado em uma solução.

#### <a name="release-management"></a>Gerenciamento de liberações

A maioria das equipes exige um processo manual de revisão e aprovação para a implantação em um ambiente de produção. Para uma implantação de produção, talvez você queira ter certeza de que ela acontece quando as principais pessoas da equipe de desenvolvimento estão disponíveis para suporte ou durante períodos de baixo tráfego.

### <a name="tools-for-custom-speech-workflows"></a>Ferramentas para fluxos de trabalho Fala Personalizada

Use as seguintes ferramentas para fluxos de trabalho de automação de CI/CD para Fala Personalizada:

- [CLI do Azure](/cli/azure/) criar uma autenticação de entidade de serviço do Azure, consultar assinaturas do Azure e armazenar resultados de teste no blob do Azure.
- A [CLI de fala do Azure](spx-overview.md) para interagir com o serviço de fala a partir da linha de comando ou de um fluxo de trabalho automatizado.

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>Solução DevOps para Fala Personalizada usando ações do GitHub

Para uma solução DevOps já implementada para Fala Personalizada, vá para o [repositório de modelos do Speech DevOps](https://github.com/Azure-Samples/Speech-Service-DevOps-Template). Crie uma cópia do modelo e comece a desenvolver modelos personalizados com um sistema DevOps robusto que inclui teste, treinamento e controle de versão usando ações do GitHub. O repositório fornece dados de teste e treinamento de exemplo para auxiliar na instalação e explicar o fluxo de trabalho. Após a instalação inicial, substitua os dados de exemplo pelos dados do projeto.

O [repositório de modelos de fala DevOps](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) fornece a infraestrutura e orientações detalhadas para:

- Copie o repositório de modelos para sua conta do GitHub e, em seguida, crie recursos do Azure e uma [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) para os fluxos de trabalho de CI/CD de ações do github.
- Percorra o "[loop interno de desenvolvimento](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow)". Atualize os dados de treinamento e teste de um Branch de recurso, teste as alterações com um modelo de desenvolvimento temporário e gere uma solicitação de pull para propor e revisar as alterações.
- Quando os dados de treinamento são atualizados em uma solicitação pull para *Main*, treine modelos com o fluxo de trabalho de CI de ações do github.
- Execute o teste de precisão automatizado para estabelecer a [taxa de erros do Word](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) do modelo (WER). Armazene os resultados de teste no blob do Azure.
- Execute o fluxo de trabalho do CD para criar um ponto de extremidade quando o WER melhorar.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o DevOps com fala:

- Use o [repositório de modelos Speech DevOps](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) para implementar o DevOps para fala personalizada com as ações do github.
