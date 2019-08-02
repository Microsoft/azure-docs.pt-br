---
title: Desenvolvimento em equipe com o Azure Dev Spaces usando .NET Core e Visual Studio
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: 53c870ad135fe13eb3bf7556678cac29352911aa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442921"
---
# <a name="team-development-with-azure-dev-spaces"></a>Desenvolvimento em equipe com Azure Dev Spaces

Neste tutorial, você aprenderá como uma equipe de desenvolvedores pode colaborar simultaneamente no mesmo cluster Kubernetes usando o Azure Dev Spaces.

## <a name="learn-about-team-development"></a>Saiba mais sobre o desenvolvimento em equipe

Até agora, você executou seu código do aplicativo como se fosse o único desenvolvedor trabalhando no aplicativo. Nesta seção, você aprenderá como o Azure Dev Spaces simplifica o desenvolvimento em equipe:
* Habilite uma equipe de desenvolvedores a trabalhar no mesmo ambiente, trabalhando em um espaço de desenvolvimento compartilhado ou em espaços de desenvolvimento distintos, conforme for necessário.
* Dá suporte a cada desenvolvedor iterando em seu código em isolamento e sem o receio de interromper os outros.
* Testa o código de ponta a ponta, antes da confirmação, sem a necessidade de criar simulações ou simular dependências.

### <a name="challenges-with-developing-microservices"></a>Desafios com o desenvolvimento de microsserviços
O aplicativo de exemplo não é complexo no momento. Porém, no desenvolvimento do mundo real, os desafios logo surgem à medida que você adiciona mais serviços e a equipe de desenvolvimento cresce.

* Seu computador de desenvolvimento pode não ter recursos suficientes para executar cada serviço de que você precisa de uma vez.
* Alguns serviços podem precisar estar publicamente acessíveis. Por exemplo, um serviço pode precisar ter um ponto de extremidade que responde a um webhook.
* Se quiser executar um subconjunto de serviços, você precisará conhecer a hierarquia de dependências completa entre todos os seus serviços. Pode ser difícil determinar essa hierarquia, especialmente à medida que o número de serviços aumenta.
* Alguns desenvolvedores recorrem à simulação de muitas de suas dependências de serviço. Essa abordagem pode ajudar, mas o gerenciamento dessas simulações pode afetar rapidamente o custo de desenvolvimento. Além disso, essa abordagem faz com que seu ambiente de desenvolvimento seja diferente da produção, o que pode causar alguns bugs sutis.
* Sendo assim, fazer qualquer tipo de teste de integração se torna difícil. Os testes de integração só podem ocorrer de maneira realista após a confirmação, o que significa que você verá problemas mais tarde no ciclo de desenvolvimento.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Trabalhar em um espaço de desenvolvimento compartilhado
Com o Azure Dev Spaces, você pode configurar um espaço de desenvolvimento *compartilhado* no Azure. Cada desenvolvedor pode se concentrar exatamente em sua parte do aplicativo e pode desenvolver iterativamente um *código de pré-confirmação* em um espaço que já contém todos os outros serviços e recursos de nuvem dos quais seus cenários dependem. As dependências estão sempre atualizadas, e os desenvolvedores trabalham de uma forma que reflete a produção.

### <a name="work-in-your-own-space"></a>Trabalhar em seu próprio espaço
À medida que você desenvolve o código para seu serviço, e antes de estar pronto para fazer check-in nele, em geral, o código não estará em um bom estado. Você ainda o está moldando iterativamente, testando e experimentando soluções. O Azure Dev Spaces apresenta o conceito de um **espaço**, permitindo que você trabalhe em isolamento e sem o receio de interromper os membros de sua equipe.

## <a name="use-dev-spaces-for-team-development"></a>Usar o Azure Dev Spaces para desenvolvimento em equipe
Vamos demonstrar essas ideias com um exemplo concreto usando nosso aplicativo de exemplo *webfrontend* -> *mywebapi*. Vamos imaginar um cenário em que um desenvolvedor, Scott, precisa fazer uma alteração no serviço *mywebapi*, e *somente* desse serviço. O *webfrontend* não precisará ser alterado como parte da atualização de Scott.

_Sem_ usar o Azure Dev Spaces, Scott teria algumas maneiras de desenvolver e testar sua atualização, nenhuma das quais seria ideal:
* Executar TODOS os componentes localmente, o que requer um computador de desenvolvimento mais avançado com o Docker instalado e, potencialmente, o MiniKube.
* Executar TODOS os componentes em um namespace isolado no cluster Kubernetes. Como *webfrontend* não será alterado, usar um namespace isolado é um desperdício de recursos do cluster.
* Executar SOMENTE *mywebapi* e fazer chamadas REST manuais para testar. Este tipo de teste não testa o fluxo completo de ponta a ponta.
* Adicionar código focado em desenvolvimento a *webfrontend* que permita que o desenvolvedor envie solicitações para uma instância diferente de *mywebapi*. Adicionar esse código complica o serviço de *webfrontend*.

### <a name="set-up-your-baseline"></a>Configurar a linha de base
Primeiro, precisaremos implantar uma linha de base de nossos serviços. Essa implantação representará a "última boa configuração conhecida", para que você possa comparar facilmente o comportamento do código local e a versão do check-in. Em seguida, criaremos um espaço filho com base nessa linha de base para que possamos testar nossas alterações de *mywebapi* dentro do contexto do aplicativo maior.

1. Clone o [aplicativo de exemplo do Azure Dev Spaces](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Faça check-out do branch remoto *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Feche todas as sessões de depuração/F5 de ambos os serviços, mas mantenha os projetos abertos nas janelas do Visual Studio.
1. Alterne para a janela do Visual Studio com o projeto _mywebapi_.
1. Clique com o botão direito do mouse no **Gerenciador de Soluções** e selecione **Propriedades**.
1. Selecione a guia **Depurar** à esquerda para mostrar as configurações do Azure Dev Spaces.
1. Selecione **Alterar** para criar o espaço que será usado quando você usar F5 ou Ctrl+F5 no serviço.
1. No menu suspenso Espaço, selecione **\<Criar Novo Espaço…\>** .
1. Verifique se o espaço pai está definido como **\<none\>** e insira o nome do espaço **dev**. Clique em OK.
1. Pressione Ctrl+F5 para executar _mywebapi_ sem o depurador anexado.
1. Alterne para a janela do Visual Studio com o projeto _webfrontend_ e pressione Ctrl+F5 para executá-lo também.

> [!Note]
> Às vezes, é necessário atualizar seu navegador depois que a página da Web é exibida inicialmente, seguindo um Ctrl+F5.

> [!TIP]
> As etapas acima configuram manualmente uma linha de base, mas recomendamos que as equipes usem CI/CD para manter a linha de base atualizada automaticamente com o código confirmado.
>
> Confira nosso [guia para configurar CI/CD com o Azure DevOps](how-to/setup-cicd.md) para criar um fluxo de trabalho semelhante ao diagrama a seguir.
>
> ![Exemplo de diagrama de CI/CD](media/common/ci-cd-complex.png)

Qualquer pessoa que abrir a URL pública e navegar para o aplicativo Web invocará o caminho do código escrito, que é executado em ambos os serviços usando o espaço _dev_ padrão. Agora, suponha que você queira continuar desenvolvendo *mywebapi*: como você pode fazer isso sem interromper outros desenvolvedores que estão usando o espaço de desenvolvimento? Para fazer isso, você configurará seu próprio espaço.

### <a name="create-a-new-dev-space"></a>Criar um novo espaço de desenvolvimento
De dentro do Visual Studio, é possível criar espaços adicionais que serão usados ao aplicar o F5 ou Ctrl+F5 em seu serviço. Você pode chamar de espaço qualquer coisa que deseje, e pode ser flexível sobre o que seu significado (por ex. _sprint4_ ou _demonstração_).

Faça o seguinte para criar um novo espaço:
1. Alterne para a janela do Visual Studio com o projeto *mywebapi*.
2. Clique com o botão direito do mouse no **Gerenciador de Soluções** e selecione **Propriedades**.
3. Selecione a guia **Depurar** à esquerda para mostrar as configurações do Azure Dev Spaces.
4. A partir daqui, você pode alterar ou criar o cluster e/ou o espaço que será usado ao aplicar F5 ou Ctrl+F5. *Verifique se o Azure Dev Space criado anteriormente está selecionado*.
5. No menu suspenso Espaço, selecione **\<Criar Novo Espaço…\>** .

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. Na caixa de diálogo **Adicionar Espaço**, defina o espaço pai como **dev** e digite um nome para seu novo espaço. Você pode usar seu nome (por exemplo, “scott”) para o novo espaço para que seus colegas possam identificar em qual espaço você está trabalhando. Clique em **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Agora você deve ver seu cluster AKS e o novo espaço selecionado na página de propriedades do projeto.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Atualizar o código para *mywebapi*

1. No projeto *mywebapi*, faça uma alteração no código para o método `string Get(int id)` no arquivo `Controllers/ValuesController.cs` da seguinte maneira:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Defina um ponto de interrupção neste bloco atualizado de código (talvez você já tenha um conjunto anterior).
3. Pressione F5 para iniciar o serviço _mywebapi_, o que iniciará o serviço em seu cluster usando o espaço selecionado. Nesse caso, o espaço selecionado é _scott_.

Aqui está um diagrama que ajudará você a entender como os diferentes espaços funcionam. O caminho roxo mostra uma solicitação por meio do espaço _dev_, que é o caminho padrão usado quando nenhum espaço é acrescentado à URL. O caminho rosa mostra uma solicitação por meio do espaço _dev/scott_.

![](media/common/Space-Routing.png)

Essa funcionalidade interna do Azure Dev Spaces permite que você teste o código de ponta a ponta em um ambiente compartilhado, sem a necessidade de cada desenvolvedor recriar a pilha completa de serviços em seus espaços. Esse roteamento exige que cabeçalhos de propagação sejam encaminhados em seu código de aplicativo, conforme ilustrado na etapa anterior deste guia.

### <a name="test-code-running-in-the-devscott-space"></a>Testar o código em execução no espaço _dev/scott_
Para testar a nova versão de *mywebapi* em conjunto com *webfrontend*, abra o navegador na URL de ponto de acesso público de *webfrontend* (por exemplo, http://dev.webfrontend.123456abcdef.eus.azds.io), e acesse a página Sobre. Você deve ver a mensagem original “Hello from webfrontend and Hello from mywebapi”.

Agora, adicione a parte “scott.s.” parte do URL para que leia algo parecido com http\://scott.s.dev.webfrontend.123456abcdef.eus.azds.io e atualize o navegador. O ponto de interrupção que você definiu em seu projeto *mywebapi* deverá ser atingido. Clique em F5 para continuar e, em seu navegador, agora você deve ver a nova mensagem “Hello from webfrontend and mywebapi now says something new”. Isso ocorre porque o caminho para o código atualizado em *mywebapi* está em execução no espaço _dev/scott_.

Quando tiver um espaço _dev_ que sempre contém suas alterações mais recentes e supondo que seu aplicativo tenha sido projetado para aproveitar o roteamento baseado em espaço do Dev Space, conforme descrito nesta seção do tutorial, deverá ser fácil ver como o Azure Dev Spaces podem ajudar bastante com o teste de novos recursos dentro do contexto do aplicativo maior. Em vez de precisar implantar _todos_ os serviços em seu espaço privado, você poderá criar um espaço privado derivado de _dev_ e apenas "aumentar" os serviços em que de fato está trabalhando. A infraestrutura de roteamento do Azure Dev Spaces cuidará do restante utilizando tantos serviços de seu espaço privado quantos puder encontrar, e usando como padrão a versão mais recente em execução no espaço _dev_. E ainda melhor, _vários_ desenvolvedores podem desenvolver ativamente serviços diferentes ao mesmo tempo em seu próprio espaço sem interromper uns aos outros.

### <a name="well-done"></a>Muito bem!
Você concluiu o guia de introdução! Você aprendeu como:

> [!div class="checklist"]
> * Configure o Azure Dev Spaces com um cluster Kubernetes gerenciado no Azure.
> * Desenvolva iterativamente o código em contêineres.
> * Desenvolva independentemente dois serviços separados e use a descoberta de serviço de DNS do Kubernetes para fazer uma chamada para outro serviço.
> * Desenvolva e teste o código produtivamente em um ambiente de equipe.
> * Estabelecer uma linha de base de funcionalidade usando o Azure Dev Spaces para testar facilmente alterações isoladas dentro do contexto de um aplicativo de microsserviço maior

Agora que você explorou o Azure Dev Spaces, [compartilhe seu espaço de desenvolvimento com um membro da equipe](how-to/share-dev-spaces.md) e ajude-o a ver como é fácil colaborar em conjunto.

## <a name="clean-up"></a>Limpar
Para excluir completamente uma instância do Azure Dev Spaces em um cluster, incluindo todos os espaços de desenvolvimento e execução de serviços dentro dele, use o comando `az aks remove-dev-spaces`. Tenha em mente que essa ação é irreversível. Você pode adicionar suporte para Azure Dev Spaces novamente no cluster, mas isso será como se você estiver iniciando novamente. Os serviços e os espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores de Azure Dev Spaces em sua assinatura ativa e então exclui o controlador de Azure Dev Spaces associado ao cluster AKS ‘myaks’ do grupo de recursos 'myaks-rg'.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
