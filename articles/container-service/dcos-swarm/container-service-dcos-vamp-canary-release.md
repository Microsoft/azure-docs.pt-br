---
title: (PRETERIDO) Versão Canário com Vamp no cluster de DC/SO do Azure
description: Como usar Vamp para serviços de versão canário e aplicar filtragem de tráfego inteligente a um cluster DC/SO do Serviço de Contêiner do Azure
services: container-service
author: gggina
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: f1b3c08cce2cb33feab899ea082fc6fb40225182
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457944"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(PRETERIDO) Microsserviços da versão Canário com Vamp no cluster de DC/SO do Serviço de Contêiner do Azure

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste passo a passo, configuramos o Vamp no Serviço de Contêiner do Azure com um cluster de DC/SO. Fizemos a versão canário do serviço de demonstração do Vamp “sava” e, em seguida, resolvemos uma incompatibilidade do serviço com o Firefox aplicando a filtragem de tráfego inteligente. 

> [!TIP] 
> Neste passo a passo, o Vamp é executado em um cluster de DC/SO, mas você também pode usar o Vamp com Kubernetes como o orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>Sobre versões canário e Vamp


A [versão canário](https://martinfowler.com/bliki/CanaryRelease.html) é uma estratégia de implantação inteligente adotada por organizações inovadoras como Netflix, Facebook e Spotify. É uma abordagem que faz sentido, porque reduz problemas, introduz redes de segurança e aumenta a inovação. Então, por que nem todas as empresas estão usando? Estender um pipeline de CI/CD para incluir estratégias canário adiciona complexidade e requer ampla experiência e conhecimento em DevOps. Isso é o suficiente para barrar empresas e corporações menores antes de elas sequer tentarem. 

[Vamp](https://vamp.io/) é um sistema de código-fonte aberto projetado para facilitar essa transição e levar os recursos de versão canário ao seu agendador de contêiner preferido. Funcionalidade de canário do Vamp vai além de distribuições baseadas em percentual. O tráfego pode ser filtrado e dividido em uma ampla variedade de condições, por exemplo, para direcionar a usuários, intervalos de IP ou dispositivos específicos. Vamp monitora e analisa as métricas de desempenho, permitindo a automação baseada em dados reais. Você pode configurar a reversão automática em caso de erros ou dimensionar variantes de serviço individuais com base na carga ou na latência.

## <a name="set-up-azure-container-service-with-dcos"></a>Configurar o Serviço de Contêiner do Azure com o DC/SO



1. [Implantar um cluster de DC/SO](container-service-deployment.md) com um mestre e dois agentes de tamanho padrão. 

2. [Criar um túnel SSH](../container-service-connect.md) para conectar ao cluster DC/SO. Este artigo pressupõe que você faça o túnel para o cluster na porta local 80.


## <a name="set-up-vamp"></a>Configurar o Vamp

Agora que você tem um cluster de DC/SO em execução, você pode instalar o Vamp da interface do usuário do DC/OS (http:\//localhost:80). 

![Interface do usuário do DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

A instalação é feita em duas etapas:

1. **Implantar Elasticsearch**.

2. Em seguida, **Implantar Vamp** instalando o pacote universal do DC/SO do Vamp.

### <a name="deploy-elasticsearch"></a>Implantar Elasticsearch

O Vamp requer Elasticsearch para coleta e agregação de métricas. Você pode usar [imagens do Docker magneticio](https://hub.docker.com/r/magneticio/elastic/) para implantar uma pilha de Elasticsearch Vamp compatível.

1. Na interface do usuário do DC/SO, acesse **Serviços** e clique em **Implantar Serviço**.

2. Selecione **modo JSON** no pop-up **Implantar Novo Serviço**.

   ![Selecione o modo JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Cole no JSON a seguir. Essa configuração executa o contêiner com 1 GB de RAM e uma verificação básica de integridade na porta Elasticsearch.
  
   ```JSON
   {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
   }
   ```
  

3. Clique em **Implantar**.

   O DC/SO implanta o contêiner Elasticsearch. Você pode acompanhar o andamento na página **Serviços**.  

   ![implantar e?Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Implantar o Vamp

Quando Elasticsearch é relatada como **Em execução**, você pode adicionar o pacote Universal do DC/SO do Vamp. 

1. Vá para **Universal** e pesquise **vamp**. 
   ![Vamp no DC/SO universal](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Clique em **instalar** ao lado do pacote vamp e escolha **Instalação Avançada**.

3. Role para baixo e digite a seguinte URL de elasticsearch: `http://elasticsearch.marathon.mesos:9200`. 

   ![Digitar a URL de Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Clique em **Examinar e Instalar** e, em seguida, clique em **Instalar** para iniciar a implantação.  

   O DC/SO implanta todos os componentes necessários do Vamp. Você pode acompanhar o andamento na página **Serviços**.
  
   ![Implantar Vamp como um pacote universal](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Depois que a implantação for concluída, você poderá acessar a interface do usuário do Vamp:

   ![Serviço Vamp no DC/SO](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Interface do usuário do Vamp](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Implantar seu primeiro serviço

Agora que o Vamp está em execução, implante um serviço de um plano gráfico. 

Em sua forma mais simples, um [plano gráfico do Vamp](https://vamp.io/documentation/using-vamp/blueprints/) descreve os pontos de extremidade (gateways), clusters e serviços a implantar. O Vamp usa clusters para agrupar diferentes variantes do mesmo serviço em grupos lógicos para a versão canário ou teste A/B.  

Esse cenário usa um aplicativo monolítico de exemplo chamado [ **sava**](https://github.com/magneticio/sava), que está na versão 1.0. O monolito é empacotado em um contêiner do Docker, que está no Hub do Docker em magneticio/sava:1.0.0. O aplicativo normalmente é executado na porta 8080, mas você deseja expô-los na porta 9050 neste caso. Implante o aplicativo por meio do Vamp usando um plano gráfico simples.

1. Acesse **Implantações**.

2. Clique em **Adicionar**.

3. Cole o seguinte plano gráfico YAML. Esse plano gráfico contém um cluster com apenas uma variante de serviço, que podemos alterar em uma etapa posterior:

   ```YAML
   name: sava                        # deployment name
   gateways:
    9050: sava_cluster/webport      # stable endpoint
   clusters:
    sava_cluster:               # cluster to create
     services:
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
   ```

4. Clique em **Salvar**. O Vamp inicia a implantação.

A implantação é listada na página **Implantações**. Clique na implantação para monitorar seu status.

![Interface do usuário do Vamp – implantação de sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![serviço sava na interface do usuário do Vamp](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

São criados dois gateways, que são listados na página **Gateways**:

* um ponto de extremidade estável para acessar o serviço em execução (porta 9050) 
* um gateway interno gerenciado pelo Vamp (mais informações sobre este gateway mais tarde). 

![Interface do usuário do Vamp – gateways de sava](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

O serviço sava agora foi implantado, mas você não pode acessá-lo externamente porque o Azure Load Balancer não sabe encaminhar tráfego para ele ainda. Para acessar o serviço, atualize a configuração de rede do Azure.


## <a name="update-the-azure-network-configuration"></a>Atualizar a configuração de rede do Azure

O Vamp implantou o serviço sava em nós de agente DC/SO, expondo um ponto de extremidade estável na porta 9050. Para acessar o serviço de fora do cluster de DC/SO, faça as seguintes alterações à configuração de rede do Azure em sua implantação de cluster: 

1. **Configure o Azure Load Balancer** para os agentes (o recurso denominado **dcos-agent-lb-xxxx**) com uma investigação de integridade e uma regra para encaminhar o tráfego na porta 9050 para as instâncias de sava. 

2. **Atualizar o grupo de segurança de rede** para os agentes públicos (o recurso denominado **XXXX-agent-public-nsg-XXXX**) para permitir o tráfego na porta 9050.

Para ver as etapas detalhadas concluir essas tarefas usando o portal do Azure, consulte [Habilitar acesso público a um aplicativo de Serviço de Contêiner do Azure](container-service-enable-public-access.md). Especifique a porta 9050 para todas as configurações de porta.


Quando tudo tiver sido criado, vá para a folha **Visão Geral** do balanceador de carga do agente de DC/SO (o recurso denominado **dcos-agent-lb-xxxx**). Localize o **Endereço IP público** e use-o para acessar sava na porta 9050.

![Portal do Azure – obter endereço IP público](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Executar uma versão canário

Suponha que você tenha uma nova versão desse aplicativo cuja versão canário você deseje colocar em produção. Você o colocou em um contêiner como magneticio/sava:1.1.0 e está pronto para começar. O Vamp permite adicionar novos serviços à implantação em execução. Esses serviços "mesclados" são implantados juntamente com os serviços existentes no cluster e recebem um peso de 0%. Nenhum tráfego é roteado para um serviço recém-mesclado até que você ajuste a distribuição de tráfego. O controle deslizante de peso na interface do usuário do Vamp proporciona controle total sobre a distribuição, permitindo ajustes incrementais (versão canário) ou uma reversão instantânea.

### <a name="merge-a-new-service-variant"></a>Mesclar uma nova variante do serviço

Para mesclar o novo serviço sava 1.1 com a implantação em execução:

1. Na interface do usuário do Vamp, clique em **Planos Gráficos**.

2. Clique em **Adicionar** e cole no seguinte blueprint do YAML: este blueprint descreve uma nova variante de serviço (sava: 1.1.0) para implantar no cluster existente (sava_cluster).

   ```YAML
   name: sava:1.1.0      # blueprint name
   clusters:
    sava_cluster:       # cluster to update
      services:
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
   ```
  
3. Clique em **Salvar**. O plano gráfico é armazenado e listado na página **Planos gráficos**.

4. Abra o menu de ação no plano gráfico do sava: 1.1 e clique em **Mesclar para**.

   ![Interface do usuário do Vamp – planos gráficos](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Selecione a implantação **sava** e clique em **Mesclar**.

   ![Interface do usuário do Vamp – mesclar plano gráfico à implantação](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

O Vamp implanta a nova variante de serviço sava:1.1.0 descrita no esquema juntamente com sava: 1.0.0 no **sava_cluster** da implantação em execução. 

![Interface do usuário do Vamp – implantação sava atualizada](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

O gateway **sava_cluster/sava/webport** (o ponto de extremidade do cluster) também é atualizado, adicionando uma rota ao sava:1.1.0 recém-implantado. Nesse momento, nenhum tráfego é roteado aqui (o **PESO** é definido como 0%).

![Interface do usuário do Vamp – gateway do cluster](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Versão canário

Nas ambas as versões do sava implantadas no mesmo cluster, ajuste a distribuição de tráfego entre elas movendo o controle deslizante **PESO**.

1. Clique em ![Interface do usuário do Vamp – editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) ao lado de **PESO**.

2. Defina a distribuição de peso como 50/50% e clique em **Salvar**.

   ![Interface do usuário do Vamp – controle deslizante de peso de gateway](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Volte para o navegador e atualize a página sava mais algumas vezes. Agora o aplicativo sava alterna entre uma página sava:1.0 e uma página sava:1.1.

   ![alternando os serviços sava1.0 e sava1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Essa alternância de páginas funciona melhor com o modo "Incógnito" ou "Anônimo" do navegador devido ao armazenamento em cache de ativos estáticos.
  >

### <a name="filter-traffic"></a>Filtrar o tráfego

Suponha que, depois da implantação, você descobriu uma incompatibilidade no sava:1.1.0 que causa problemas de exibição em navegadores Firefox. Você pode definir o Vamp para filtrar o tráfego de entrada e direcionar todos os usuários do Firefox de volta ao sava:1.0.0 que é sabidamente estável. Esse filtro resolve instantaneamente a interrupção para usuários do Firefox, enquanto todos os outros continuam a aproveitar os benefícios do sava:1.1.0 aprimorado.

O Vamp usa **condições** para filtrar o tráfego entre as rotas em um gateway. O tráfego primeiro é filtrado e direcionado de acordo com as condições aplicadas a cada rota. Todo o tráfego restante é distribuído de acordo com a configuração de peso do gateway.

Você pode criar uma condição para filtrar todos os usuários do Firefox e direcioná-los ao antigo sava:1.0.0:

1. Na página de **Gateways** sava/sava_cluster/webport, clique em ![Interface do usuário do Vamp – editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para adicionar uma **CONDIÇÃO** à rota sava/sava_cluster/sava:1.0.0/webport. 

2. Insira a condição **user-agent == Firefox** e clique em ![Interface do usuário do Vamp – salvar](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

   O Vamp adiciona a condição com uma força padrão de 0%. Para iniciar a filtragem de tráfego, você precisa ajustar a força da condição.

3. Clique em ![Interface do usuário do Vamp – editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para alterar a **FORÇA** aplicada à condição.
 
4. Defina a **FORÇA** como 100% e clique em ![Interface do usuário do Vamp – salvar](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) para salvar.

   O Vamp agora envia todo o tráfego correspondente à condição (todos os usuários do Firefox) para o sava:1.0.0.

   ![Interface do usuário do Vamp – aplicar condição ao gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Por fim, ajuste o peso do gateway para enviar todo o tráfego restante (todos os usuários não Firefox) para o novo sava:1.1.0. Clique em ![Interface do usuário do Vamp – editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) ao lado de **PESO** e defina a distribuição de peso de modo que 100% seja direcionado à rota sava/sava_cluster/sava:1.1.0/webport.

   Todo o tráfego não filtrado pela condição agora é direcionado para o novo sava:1.1.0.

6. Para ver o filtro em ação, abra dois navegadores diferentes (Firefox e um outro navegador) e acesse o serviço sava em ambos. Todas as solicitações do Firefox são enviadas para o sava:1.0.0, enquanto todos os outros navegadores são direcionados para o sava:1.1.0.

   ![Interface do usuário do Vamp – filtrar tráfego](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Resumindo

Este artigo foi uma breve introdução ao Vamp em um cluster de DC/SO. Para os iniciantes, você colocou o Vamp em execução no cluster de DC/SO do Serviço de Contêiner do Azure, implantou um serviço com um plano gráfico do Vamp e o acessou no ponto de extremidade exposto (gateway).

Também abordamos alguns recursos avançados do Vamp: mesclar uma nova variante de serviço à implantação em execução e introduzi-la incrementalmente, então filtrar o tráfego para resolver uma incompatibilidade conhecida.


## <a name="next-steps"></a>Próximas etapas

* Saiba como gerenciar ações do Vamp por meio da [API REST do Vamp](https://vamp.io/documentation/api/api-reference/).

* Crie scripts de automação do Vamp no Node.js e execute-os como [Fluxos de trabalho do Vamp](https://vamp.io/documentation/using-vamp/v1.0.0/workflows/#create-a-workflow).

* Consulte [Tutoriais do VAMP](https://vamp.io/documentation/tutorials/) adicionais.

