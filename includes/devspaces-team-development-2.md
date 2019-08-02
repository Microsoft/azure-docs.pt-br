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
ms.openlocfilehash: e0f768b876b49ec006ce98decf121d73d334b6d8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172361"
---
### <a name="run-the-service"></a>Executar o serviço

Pressione F5 (ou digite `azds up` na Janela do Terminal) para executar o serviço. O serviço será executado automaticamente no espaço _dev/scott_ recém-selecionado. Confirme se o serviço está sendo executado em seu próprio espaço executando `azds list-up`:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Observe uma instância do *mywebapi* agora em execução no espaço _dev/scott_. A versão em execução em _dev_ ainda está em execução, mas não está listada.

Listar as URLs para o espaço atual executando `azds list-uris`.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Observe que a URL de ponto de acesso público para *webfrontend* é prefixado com *scott.s*. Essa URL é exclusiva para o espaço _dev/scott_. Esse prefixo de URL informa o Controlador de entrada para rotear solicitações para a versão _dev/scott_ de um serviço. Quando uma solicitação com essa URL é manipulada por espaços de desenvolvimento, o Controlador de Entrada primeiro tenta encaminhar a solicitação para o serviço *webfrontend* pelo espaço _dev/scott_. Se isso falhar, a solicitação será encaminhada para o serviço *webfrontend* pelo espaço _dev_ como um fallback. Também note que há uma URL do localhost para acessar o servidor sobre o localhost usando a funcionalidade do Kubernetes *port-forward*. Para obter mais informações sobre URLs e o roteamento em Azure Dev Spaces, consulte [Como o Azure Dev Spaces funciona e é configurado](../articles/dev-spaces/how-dev-spaces-works.md).



![Roteamento do Espaço](../articles/dev-spaces/media/common/Space-Routing.png)

Esse recurso interno do Azure Dev Spaces permite que você teste o código em um espaço compartilhado, sem a necessidade de cada desenvolvedor recriar a pilha completa de serviços em seus espaços. Esse roteamento exige o código do aplicativo para encaminhar cabeçalhos de propagação, conforme ilustrado na etapa anterior deste guia.

### <a name="test-code-in-a-space"></a>Testar o código em um espaço
Para testar a nova versão de *mywebapi* com *webfrontend*, abra o navegador na URL de ponto de acesso público de *webfrontend* e acesse a página Sobre. Você deverá ver a nova mensagem exibida.

Agora, remova a parte "scott.s." da URL e atualize o navegador. Você deverá ver o comportamento antigo (com a versão *mywebapi* em execução em _dev_).

Quando tiver um espaço _dev_, que sempre contém suas alterações mais recentes, e supondo que seu aplicativo tenha sido projetado para aproveitar o roteamento baseado em espaço do Dev Space, conforme descrito nesta seção do tutorial, deverá ser fácil ver como o Azure Dev Spaces podem ajudar bastante com o teste de novos recursos dentro do contexto do aplicativo maior. Em vez de precisar implantar _todos_ os serviços em seu espaço privado, você poderá criar um espaço privado derivado de _dev_ e apenas "aumentar" os serviços em que de fato está trabalhando. A infraestrutura de roteamento do Azure Dev Spaces cuidará do restante utilizando tantos serviços de seu espaço privado quantos puder encontrar, e usando como padrão a versão mais recente em execução no espaço _dev_. E ainda melhor, _vários_ desenvolvedores podem desenvolver ativamente serviços diferentes ao mesmo tempo em seu próprio espaço sem interromper uns aos outros.

### <a name="well-done"></a>Muito bem!
Você concluiu o guia de introdução! Você aprendeu como:

> [!div class="checklist"]
> * Configure o Azure Dev Spaces com um cluster Kubernetes gerenciado no Azure.
> * Desenvolva iterativamente o código em contêineres.
> * Desenvolva independentemente dois serviços separados e use a descoberta de serviço de DNS do Kubernetes para fazer uma chamada para outro serviço.
> * Desenvolva e teste o código produtivamente em um ambiente de equipe.
> * Estabelecer uma linha de base de funcionalidade usando o Azure Dev Spaces para testar facilmente alterações isoladas dentro do contexto de um aplicativo de microsserviço maior

Agora que você explorou o Azure Dev Spaces, [compartilhe seu espaço de desenvolvimento com um membro da equipe](../articles/dev-spaces/how-to/share-dev-spaces.md) e ajude-o a ver como é fácil começar a colaborar.

## <a name="clean-up"></a>Limpar
Para excluir completamente uma instância do Azure Dev Spaces em um cluster, incluindo todos os espaços de desenvolvimento e execução de serviços dentro dele, use o comando `az aks remove-dev-spaces`. Tenha em mente que essa ação é irreversível. Você pode adicionar suporte para Azure Dev Spaces novamente no cluster, mas isso será como se você estiver iniciando novamente. Os serviços e os espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores de Azure Dev Spaces em sua assinatura ativa e então exclui o controlador de Azure Dev Spaces associado ao cluster AKS ‘myaks’ do grupo de recursos 'myaks-rg'.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```