---
title: Melhores práticas de implantação
description: Saiba mais sobre os principais mecanismos de implantação no serviço Azure App. Encontre recomendações específicas de idioma e outras advertências.
keywords: serviço de aplicativo do Azure, aplicativo Web, implantar, implantação, pipelines, compilação
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 3b49177eb592906e3bf84d359699b354f8c87c6e
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185658"
---
# <a name="deployment-best-practices"></a>Práticas recomendadas de implantação

Cada equipe de desenvolvimento tem requisitos exclusivos que podem tornar difícil a implementação de um pipeline de implantação eficiente em qualquer serviço de nuvem. Este artigo apresenta os três principais componentes da implantação para o serviço de aplicativo: fontes de implantação, pipelines de compilação e mecanismos de implantação. Este artigo também aborda algumas práticas recomendadas e dicas para pilhas de idiomas específicas.

## <a name="deployment-components"></a>Componentes de implantação

### <a name="deployment-source"></a>Origem da implantação

Uma origem de implantação é o local do código do aplicativo. Para aplicativos de produção, a origem da implantação geralmente é um repositório hospedado pelo software de controle de versão, como [GitHub, BitBucket ou Azure Repos](deploy-continuous-deployment.md). Para cenários de desenvolvimento e teste, a origem da implantação pode ser [um projeto em seu computador local](deploy-local-git.md). O serviço de aplicativo também dá suporte a [pastas do onedrive e do Dropbox](deploy-content-sync.md) como fontes de implantação. Embora as pastas de nuvem possam facilitar a introdução ao serviço de aplicativo, normalmente não é recomendável usar essa fonte para aplicativos de produção de nível empresarial. 

### <a name="build-pipeline"></a>Pipeline de build

Depois de decidir sobre uma origem de implantação, a próxima etapa é escolher um pipeline de compilação. Um pipeline de compilação lê o código-fonte da origem da implantação e executa uma série de etapas (como código de compilação, HTML minificar e JavaScript, testes em execução e componentes de empacotamento) para obter o aplicativo em um estado executável. Os comandos específicos executados pelo pipeline de compilação dependem da pilha de idiomas. Essas operações podem ser executadas em um servidor de compilação, como Azure Pipelines ou executadas localmente.

### <a name="deployment-mechanism"></a>Mecanismo de implantação

O mecanismo de implantação é a ação usada para colocar seu aplicativo interno no diretório */Home/site/wwwroot* do seu aplicativo Web. O diretório */wwwroot* é um local de armazenamento montado compartilhado por todas as instâncias do seu aplicativo Web. Quando o mecanismo de implantação coloca seu aplicativo nesse diretório, suas instâncias recebem uma notificação para sincronizar os novos arquivos. O serviço de aplicativo dá suporte aos seguintes mecanismos de implantação:

- Kudu pontos de extremidade: o [kudu](https://github.com/projectkudu/kudu/wiki) é a ferramenta de produtividade de desenvolvedor de software livre que é executada como um processo separado no serviço de aplicativo do Windows e como um segundo contêiner no serviço de aplicativo do Linux. O kudu lida com implantações contínuas e fornece pontos de extremidade HTTP para implantação, como zipdeploy.
- FTP e WebDeploy: usando suas [credenciais de site ou de usuário](deploy-configure-credentials.md), você pode carregar arquivos [via FTP](deploy-ftp.md) ou WebDeploy. Esses mecanismos não passam pelo kudu.  

As ferramentas de implantação, como os plug-ins Azure Pipelines, Jenkins e editor, usam um desses mecanismos de implantação.

## <a name="use-deployment-slots"></a>Usar slots de implantação

Sempre que possível, use [Slots de implantação](deploy-staging-slots.md) ao implantar uma nova compilação de produção. Ao usar uma camada de plano do serviço de aplicativo padrão ou melhor, você pode implantar seu aplicativo em um ambiente de preparo, validar suas alterações e fazer testes de fumaça. Quando estiver pronto, você poderá trocar os slots de preparo e de produção. A operação de permuta ativa as instâncias de trabalho necessárias para corresponder à escala de produção, eliminando assim o tempo de inatividade.

### <a name="continuously-deploy-code"></a>Implantar código continuamente

Se o projeto tiver as ramificações designadas para teste, p e r e preparo, cada uma dessas ramificações deverá ser implantada continuamente em um slot de preparo. (Isso é conhecido como o [design do Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).) Isso permite que seus participantes avaliem e testem facilmente a ramificação implantada. 

A implantação contínua nunca deve ser habilitada para seu slot de produção. Em vez disso, sua ramificação de produção (geralmente principal) deve ser implantada em um slot de não produção. Quando você estiver pronto para liberar a ramificação de base, troque-a no slot de produção. Alternar para a produção — em vez de implantar na produção — evita o tempo de inatividade e permite reverter as alterações alternando novamente. 

![Diagrama que mostra o fluxo entre o desenvolvimento, o preparo e os branches principal e os slots para os quais eles são implantados.](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Implantar contêineres continuamente

Para contêineres personalizados do Docker ou de outros registros de contêiner, implante a imagem em um slot de preparo e troque em produção para evitar o tempo de inatividade. A automação é mais complexa do que a implantação de código, pois você deve enviar por push a imagem para um registro de contêiner e atualizar a marca de imagem no webapp.

Para cada ramificação que você deseja implantar em um slot, configure a automação para fazer o seguinte em cada confirmação para a ramificação.

1. **Crie e marque a imagem**. Como parte do pipeline de compilação, marque a imagem com a ID de confirmação git, o carimbo de data/hora ou outras informações de identificação. É melhor não usar a marca padrão "mais recente". Caso contrário, é difícil rastrear o código que está implantado no momento, o que torna a depuração muito mais difícil.
1. **Envie por push a imagem marcada**. Depois que a imagem é criada e marcada, o pipeline envia a imagem por push para nosso registro de contêiner. Na próxima etapa, o slot de implantação efetuará pull da imagem marcada do registro de contêiner.
1. **Atualize o slot de implantação com a nova marca de imagem**. Quando essa propriedade for atualizada, o site será reiniciado automaticamente e efetuará pull da nova imagem de contêiner.

![Visual de uso do slot](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Há exemplos abaixo para estruturas de automação comuns.

### <a name="use-azure-devops"></a>Usar DevOps do Azure

O serviço de aplicativo tem [entrega contínua interna](deploy-continuous-deployment.md) para contêineres por meio da central de implantação. Navegue até seu aplicativo no [portal do Azure](https://portal.azure.com/) e selecione **centro de implantação** em **implantação**. Siga as instruções para selecionar seu repositório e ramificação. Isso configurará um pipeline de Build e versão do DevOps para compilar, marcar e implantar automaticamente o contêiner quando novas confirmações forem enviadas para o Branch selecionado.

### <a name="use-github-actions"></a>Usar ações do GitHub

Você também pode automatizar a implantação de contêiner [com as ações do GitHub](deploy-container-github-action.md).  O arquivo de fluxo de trabalho abaixo criará e marcará o contêiner com a ID de confirmação, a enviará por push a um registro de contêiner e atualizará o slot de site especificado com a nova marca de imagem.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@main

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Usar outros provedores de automação

As etapas listadas anteriormente se aplicam a outros utilitários de automação, como CircleCI ou Travis CI. No entanto, você precisa usar o CLI do Azure para atualizar os slots de implantação com novas marcas de imagem na etapa final. Para usar o CLI do Azure em seu script de automação, gere uma entidade de serviço usando o comando a seguir.

```azurecli
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

Em seu script, faça logon usando `az login --service-principal` , fornecendo as informações da entidade de segurança. Você pode usar `az webapp config container set` para definir o nome do contêiner, a marca, a URL do registro e a senha do registro. Abaixo estão alguns links úteis para que você construa seu processo de CI de contêiner.

- [Como fazer logon no CLI do Azure no CI de círculo](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Considerações sobre Language-Specific

### <a name="java"></a>Java

Use o kudu [zipdeploy/](deploy-zip.md) API para implantar aplicativos jar e [wardeploy/](deploy-zip.md#deploy-war-file) para aplicativos War. Se você estiver usando o Jenkins, poderá usar essas APIs diretamente na fase de implantação. Para obter mais informações, consulte [este artigo](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli).

### <a name="node"></a>Nó

Por padrão, o kudu executa as etapas de Build para o seu aplicativo de nó ( `npm install` ). Se você estiver usando um serviço de compilação como o Azure DevOps, a compilação kudu será desnecessária. Para desabilitar a compilação kudu, crie uma configuração de aplicativo, `SCM_DO_BUILD_DURING_DEPLOYMENT` , com um valor de `false` .

### <a name="net"></a>.NET 

Por padrão, o kudu executa as etapas de Build para seu aplicativo .NET ( `dotnet build` ). Se você estiver usando um serviço de compilação como o Azure DevOps, a compilação kudu será desnecessária. Para desabilitar a compilação kudu, crie uma configuração de aplicativo, `SCM_DO_BUILD_DURING_DEPLOYMENT` , com um valor de `false` .

## <a name="other-deployment-considerations"></a>Outras considerações sobre implantação

### <a name="local-cache"></a>Cache Local

O conteúdo do Serviço de Aplicativo do Azure é armazenado no Armazenamento do Microsoft Azure e exibido de forma duradoura como um compartilhamento de conteúdo. No entanto, alguns aplicativos precisam apenas de um repositório de conteúdo somente leitura de alto desempenho que podem ser executados com alta disponibilidade. Esses aplicativos podem se beneficiar do uso do [cache local](overview-local-cache.md). O cache local não é recomendado para sites de gerenciamento de conteúdo, como WordPress.

Sempre use o cache local em conjunto com [Slots de implantação](deploy-staging-slots.md) para evitar o tempo de inatividade. Consulte [esta seção](overview-local-cache.md#best-practices-for-using-app-service-local-cache) para obter informações sobre como usar esses recursos juntos.

### <a name="high-cpu-or-memory"></a>Alta CPU ou memória

Se o plano do serviço de aplicativo estiver usando mais de 90% da memória ou CPU disponível, a máquina virtual subjacente poderá ter problemas para processar sua implantação. Quando isso acontece, expanda temporariamente sua contagem de instâncias para executar a implantação. Depois que a implantação for concluída, você poderá retornar a contagem de instâncias para seu valor anterior.

Para obter mais informações sobre as práticas recomendadas, visite [diagnóstico do serviço de aplicativo](./overview-diagnostics.md) para descobrir práticas recomendadas acionáveis específicas ao seu recurso.

- Navegue até seu aplicativo Web no [portal do Azure](https://portal.azure.com).
- Clique em **diagnosticar e resolver problemas** no painel de navegação esquerdo, que abre o diagnóstico do serviço de aplicativo.
- Escolha o bloco página inicial de **práticas recomendadas** .
- Clique em **práticas recomendadas para disponibilidade & desempenho** ou **práticas recomendadas para a configuração ideal** para exibir o estado atual do seu aplicativo em relação a essas práticas recomendadas.

Você também pode usar esse link para abrir diretamente o diagnóstico do serviço de aplicativo para seu recurso: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` .