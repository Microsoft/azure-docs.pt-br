---
title: Arquivo de inclusão
description: Arquivo de inclusão
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 40c1be20df845b975c023616e38cbb932c985735
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172360"
---
# <a name="team-development-with-azure-dev-spaces"></a>Desenvolvimento em equipe com Azure Dev Spaces

Neste tutorial, você aprenderá como uma equipe de desenvolvedores pode colaborar simultaneamente no mesmo cluster Kubernetes usando o Azure Dev Spaces.

## <a name="learn-about-team-development"></a>Saiba mais sobre o desenvolvimento em equipe
Até agora, você executou o código do aplicativo como se fosse o único desenvolvedor trabalhando no aplicativo. Nesta seção, você aprenderá como o Azure Dev Spaces simplifica o desenvolvimento em equipe:
* Habilite uma equipe de desenvolvedores a trabalhar no mesmo ambiente, trabalhando em um espaço de desenvolvimento compartilhado ou em espaços de desenvolvimento distintos, conforme for necessário.
* Dá suporte a cada desenvolvedor iterando em seu código em isolamento e sem o receio de interromper os outros.
* Testa o código de ponta a ponta, antes da confirmação, sem a necessidade de criar simulações ou simular dependências.

### <a name="challenges-with-developing-microservices"></a>Desafios com o desenvolvimento de microsserviços
O aplicativo de exemplo não é muito complexo no momento. Porém, no desenvolvimento do mundo real, os desafios logo surgem à medida que você adiciona mais serviços e a equipe de desenvolvimento cresce. A execução de tudo localmente para o desenvolvimento pode se tornar irrealista.

* Seu computador de desenvolvimento pode não ter recursos suficientes para executar cada serviço de que você precisa de uma vez.
* Alguns serviços podem precisar estar publicamente acessíveis. Por exemplo, um serviço pode precisar ter um ponto de extremidade que responde a um webhook.
* Se quiser executar um subconjunto de serviços, você precisará conhecer a hierarquia de dependências completa entre todos os seus serviços. Pode ser difícil determinar isso, especialmente à medida que o número de serviços aumenta.
* Alguns desenvolvedores recorrem à simulação de muitas de suas dependências de serviço. Essa abordagem pode ajudar, mas o gerenciamento dessas simulações pode afetar rapidamente o custo de desenvolvimento. Além disso, essa abordagem faz com que seu ambiente de desenvolvimento seja muito diferente da produção, o que permite o surgimento de alguns bugs sutis.
* Sendo assim, fazer qualquer tipo de teste de integração se torna difícil. Os testes de integração só podem ocorrer de maneira realista após a confirmação, o que significa que você verá problemas mais tarde no ciclo de desenvolvimento.

    ![](../articles/dev-spaces/media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Trabalhar em um espaço de desenvolvimento compartilhado
Com o Azure Dev Spaces, você pode configurar um espaço de desenvolvimento *compartilhado* no Azure. Cada desenvolvedor pode se concentrar exatamente em sua parte do aplicativo e pode desenvolver iterativamente um *código de pré-confirmação* em um espaço que já contém todos os outros serviços e recursos de nuvem dos quais seus cenários dependem. As dependências estão sempre atualizadas, e os desenvolvedores trabalham de uma forma que reflete a produção.

### <a name="work-in-your-own-space"></a>Trabalhar em seu próprio espaço
À medida que você desenvolve o código para seu serviço, e antes de estar pronto para fazer check-in nele, em geral, o código não estará em um bom estado. Você ainda o está moldando iterativamente, testando e experimentando soluções. O Azure Dev Spaces apresenta o conceito de um **espaço**, permitindo que você trabalhe em isolamento e sem o receio de interromper os membros de sua equipe.

## <a name="use-dev-spaces-for-team-development"></a>Usar o Azure Dev Spaces para desenvolvimento em equipe
Vamos demonstrar essas ideias com um exemplo concreto usando nosso aplicativo de exemplo *webfrontend* -> *mywebapi*. Vamos imaginar um cenário em que um desenvolvedor, Scott, precisa fazer uma alteração no serviço *mywebapi*, e *somente* desse serviço. O *webfrontend* não precisará ser alterado como parte da atualização de Scott.

_Sem_ usar o Azure Dev Spaces, Scott teria algumas maneiras de desenvolver e testar sua atualização, nenhuma das quais seria ideal:
* Executar TODOS os componentes localmente. Isso requer um computador de desenvolvimento mais avançado com o Docker instalado e, potencialmente, o MiniKube.
* Executar TODOS os componentes em um namespace isolado no cluster Kubernetes. Como *webfrontend* não será alterado, isso é um desperdício de recursos do cluster.
* Executar SOMENTE *mywebapi* e fazer chamadas REST manuais para testar. Isso não testa o fluxo completo de ponta a ponta.
* Adicionar código focado em desenvolvimento a *webfrontend* que permita que o desenvolvedor envie solicitações para uma instância diferente de *mywebapi*. Isso complica o serviço de *webfrontend*.

### <a name="set-up-your-baseline"></a>Configurar a linha de base
Primeiro, precisaremos implantar uma linha de base de nossos serviços. Essa implantação representará a "última boa configuração conhecida", para que você possa comparar facilmente o comportamento do código local e a versão do check-in. Em seguida, criaremos um espaço filho com base nessa linha de base para que possamos testar nossas alterações de *mywebapi* dentro do contexto do aplicativo maior.

1. Clone o [aplicativo de exemplo do Azure Dev Spaces](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Faça check-out do branch remoto *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Selecione o espaço _dev_: `azds space select --name dev`. Quando solicitado a selecionar um espaço de desenvolvimento pai, selecione _\<none\>_ .
1. Navegue até o diretório _mywebapi_ e execute: `azds up -d`
1. Navegue até o diretório _webfrontend_ e execute: `azds up -d`
1. Execute `azds list-uris` para ver o ponto de extremidade público de _webfrontend_

> [!TIP]
> As etapas acima configuram manualmente uma linha de base, mas recomendamos que as equipes usem CI/CD para manter a linha de base atualizada automaticamente com o código confirmado.
>
> Confira nosso [guia para configurar CI/CD com o Azure DevOps](../articles/dev-spaces/how-to/setup-cicd.md) para criar um fluxo de trabalho semelhante ao diagrama a seguir.
>
> ![Exemplo de diagrama de CI/CD](../articles/dev-spaces/media/common/ci-cd-complex.png)

Neste ponto, sua linha de base deve estar em execução. Execute o comando `azds list-up --all` e você verá uma saída semelhante à seguinte:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

A coluna DevSpace mostra que ambos os serviços estão em execução em um espaço chamado _dev_. Qualquer pessoa que abrir a URL pública e navegar para o aplicativo Web invocará o caminho do código do qual você fez check-in e que percorre ambos os serviços. Agora, suponha que você queira continuar desenvolvendo _mywebapi_. Como fazer alterações de código e testá-las e não interromper os outros desenvolvedores que estão usando o ambiente de desenvolvimento? Para fazer isso, você configurará seu próprio espaço.

### <a name="create-a-dev-space"></a>Criar um espaço de desenvolvimento
Para executar sua própria versão de _mywebapi_ em um espaço que não seja _dev_, crie seu próprio espaço usando o seguinte comando:

```cmd
azds space select --name scott
```

Quando solicitado, selecione _dev_ como o **espaço de desenvolvimento pai**. Isso significa que nosso novo espaço, _dev/scott_, será derivado do espaço _dev_. Em breve, veremos como isso nos ajudará nos testes.

Mantendo nossa hipótese inicial, usamos o nome _scott_ para o novo espaço para que colegas possam identificar quem está trabalhando nele. Mas ele pode ter o nome que você quiser e pode ter flexibilidade com relação ao significado, como _sprint4_ ou _demonstração_. Seja qual for o caso, _dev_ serve como a linha de base para todos os desenvolvedores que trabalham em uma parte do aplicativo:

![](../articles/dev-spaces/media/common/ci-cd-space-setup.png)

Execute o comando `azds space list` para ver uma lista de todos os espaços no ambiente de desenvolvimento. A coluna _Selecionado_ indica qual espaço está selecionado no momento (verdadeiro/falso). Em seu caso, o espaço chamado _dev/scott_ foi selecionado automaticamente quando foi criado. Você pode selecionar outro espaço a qualquer momento com o comando `azds space select`.

Vamos ver isso tudo em ação.