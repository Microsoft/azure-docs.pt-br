---
title: Criar um pipeline de CI/CD com o processo de ciência de dados de Azure Pipelines equipe
description: Crie um pipeline de integração contínua e entrega contínua para aplicativos de ia (inteligência artificial) usando o Docker e o kubernetes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: af7900faff18d526686b80f23b9de3b3bce5ec5c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309606"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Criar pipelines de CI/CD para aplicativos de ia usando Azure Pipelines, Docker e kubernetes

Um aplicativo de ia (inteligência artificial) é um código de aplicativo inserido com um modelo de ML (aprendizado de máquina) pretreinado. Há sempre dois fluxos de trabalho para um aplicativo de ia: os cientistas de dados criam o modelo ML e os desenvolvedores de aplicativos criam o aplicativo e os expõem aos usuários finais para consumir. Este artigo descreve como implementar um pipeline de CI/CD (integração contínua e entrega contínua) para um aplicativo de ia que incorpore o modelo ML ao código-fonte do aplicativo. O exemplo de código e tutorial usam um aplicativo Web Python Flask e busca um modelo pretreinado de uma conta de armazenamento de BLOBs do Azure privada. Você também pode usar uma conta de armazenamento AWS S3.

> [!NOTE]
> O processo a seguir é uma das várias maneiras de fazer CI/CD. Há alternativas para essas ferramentas e os pré-requisitos.

## <a name="source-code-tutorial-and-prerequisites"></a>Código-fonte, tutorial e pré-requisitos

Você pode baixar o [código-fonte](https://github.com/Azure/DevOps-For-AI-Apps) e um [tutorial detalhado](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) do github. Siga as etapas do tutorial para implementar um pipeline de CI/CD para seu próprio aplicativo.

Para usar o código-fonte baixado e o tutorial, você precisa dos seguintes pré-requisitos: 

- O [repositório de código-fonte](https://github.com/Azure/DevOps-For-AI-Apps) bifurcado para sua conta do github
- Uma [organização DevOps do Azure](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [CLI do Azure](/cli/azure/install-azure-cli)
- Um [cluster do serviço de contêiner do Azure para kubernetes (AKs)](/previous-versions/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para executar comandos e buscar a configuração do cluster AKs 
- Uma [conta do ACR (registro de contêiner do Azure)](../../container-registry/container-registry-get-started-portal.md)

## <a name="cicd-pipeline-summary"></a>Resumo de pipeline de CI/CD

Cada nova confirmação de git inicia o pipeline de compilação. A compilação obtém com segurança o modelo de ML mais recente de uma conta de armazenamento de BLOB e a empacota com o código do aplicativo em um único contêiner. Essa desassociação do desenvolvimento de aplicativos e do workstreams de ciência de dados garante que o aplicativo de produção sempre esteja executando o código mais recente com o modelo de ML mais recente. Se o aplicativo passar no teste, o pipeline armazenará com segurança a imagem de compilação em um contêiner do Docker no ACR. Em seguida, o pipeline de liberação implanta o contêiner usando AKS. 

## <a name="cicd-pipeline-steps"></a>Etapas de pipeline de CI/CD

O diagrama e as etapas a seguir descrevem a arquitetura de pipeline de CI/CD:

![Arquitetura de pipeline de CI/CD](./media/ci-cd-flask/architecture.png)

1. Os desenvolvedores trabalham no código do aplicativo no IDE de sua escolha.
2. Os desenvolvedores confirmam o código para Azure Repos, GitHub ou outro provedor de controle do código-fonte git. 
3. Separadamente, os cientistas de dados funcionam no desenvolvimento de seu modelo de ML.
4. Os cientistas de dados publicam o modelo concluído em um repositório de modelo, nesse caso, uma conta de armazenamento de BLOBs. 
5. Azure Pipelines aciona uma compilação com base na confirmação do git.
6. O pipeline de compilação recebe o modelo de ML mais recente do armazenamento de BLOBs e cria um contêiner.
7. O pipeline envia a imagem de Build para o repositório de imagens privadas no ACR.
8. O pipeline de lançamento é ativado com base na compilação bem-sucedida.
9. O pipeline extrai a imagem mais recente do ACR e a implanta no cluster kubernetes no AKS.
10. As solicitações de usuário para o aplicativo passam pelo servidor DNS.
11. O servidor DNS passa as solicitações para um balanceador de carga e envia respostas de volta para os usuários.

## <a name="see-also"></a>Confira também

- [TDSP (Processo de Ciência de dados de Equipe)](./index.yml)
- [AML (Azure Machine Learning)](../index.yml)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [AKS (Serviço de Kubernetes do Azure)](../../aks/intro-kubernetes.md)