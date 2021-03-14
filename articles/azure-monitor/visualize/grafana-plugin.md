---
title: Monitorar os serviços e aplicativos do Azure usando Grafana
description: Encaminhe dados do Azure Monitor e do Application Insights para que você possa exibi-los no Grafana.
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: decd0353bcb2cf048fd5591ea8c895cf22944809
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048651"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorar os serviços do Azure no Grafana
Agora você pode monitorar os serviços e aplicativos do Azure do [Grafana](https://grafana.com/) usando o [plug-in da fonte de dados do Monitor do Azure](https://grafana.com/plugins/grafana-azure-monitor-datasource). O plug-in reúne os dados de desempenho do aplicativo coletados pelo Azure Monitor, incluindo vários logs e métricas. Em seguida, exiba esses dados no painel do Grafana.

Use as etapas a seguir para configurar um servidor Grafana e criar painéis para métricas e logs do Monitor do Azure.

## <a name="set-up-a-grafana-server"></a>Configurar um servidor do Grafana

### <a name="set-up-grafana-locally"></a>Configurar o Grafana localmente
Para configurar um servidor Grafana local, [baixe e instale o Grafana em seu ambiente local](https://grafana.com/grafana/download). Para usar a integração de Azure Monitor do plug-in, instale o Grafana versão 5,3 ou superior.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Configurar o Grafana no Azure por meio do Azure Marketplace
1. Acesse o Azure Marketplace e escolha Grafana da Grafana Labs.

2. Preencha os nomes e os detalhes. Criar um grupo de recursos. Acompanhe os valores escolhidos para o nome de usuário da VM, senha da VM e senha do administrador do servidor do Grafana.  

3. Escolha um tamanho de VM e uma conta de armazenamento.

4. Configure as definições de configuração de rede.

5. Exiba o resumo e selecione **Criar** depois de aceitar os termos de uso.

6. Após a conclusão da implantação, selecione **Ir para o Grupo de Recursos**. Você verá uma lista de recursos recém-criados.

    ![Objetos do grupo de recursos do Grafana](media/grafana-plugin/grafana1.png)

    Se você selecionar o grupo de segurança de rede (*grafana-nsg*, nesse caso), poderá ver que a porta 3000 é usada para acessar o servidor do Grafana.

7. Obtenha o endereço IP público do seu servidor Grafana - volte à lista de recursos e selecione **Endereço IP público**.

## <a name="sign-in-to-grafana"></a>Entrar no Grafana

1. Usando o endereço IP do seu servidor, abra a página de logon em *http:// \<IP address\> : 3000* ou *\<DNSName> \: 3000* em seu navegador. Enquanto 3000 é a porta padrão, note que você pode ter selecionado uma porta diferente durante a configuração. Você deve ver uma página de login para o servidor Grafana que você construiu.

    ![Tela de logon do Grafana](./media/grafana-plugin/grafana-login-screen.png)

2. Entre com o *administrador* de nome de usuário e a senha de administrador do servidor Grafana que você criou anteriormente. Se você estiver usando uma configuração local, a senha padrão será *admin* e você deverá alterá-la no primeiro login.

## <a name="configure-data-source-plugin"></a>Configurar o plug-in de fonte de dados

Depois de fazer logon com êxito, você deverá ver que o plug-in de fonte de dados do Azure Monitor já está incluído.

![Grafana inclui o plug-in do Azure Monitor](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Selecione **Adicionar origem de dados** para adicionar e configurar a fonte de dados do Monitor do Azure.

2. Escolha um nome para a fonte de dados e selecione **Monitor do Azure** como o tipo na lista suspensa.

3. Crie uma entidade de serviço - o Grafana usa uma entidade de serviço do Azure Active Directory para se conectar às APIs do Monitor do Azure e coletar dados. Você deve criar ou usar uma entidade de serviço existente para gerenciar o acesso aos seus recursos do Azure.
    * Consulte [estas instruções](../../active-directory/develop/howto-create-service-principal-portal.md) para criar uma entidade de serviço. Copie e salve seu ID de locatário (ID do diretório), ID do cliente (ID do aplicativo) e segredo do cliente (valor da chave do aplicativo).
    * Consulte [Atribuir aplicativo à função](../../active-directory/develop/howto-create-service-principal-portal.md) para atribuir a função Leitor ao aplicativo Azure Active Directory na assinatura, grupo de recursos ou recurso que você deseja monitorar. 
    A API do Log Analytics requer a função [ Log Analytics Reader ](../../role-based-access-control/built-in-roles.md#log-analytics-reader), que inclui as permissões da função Leitor e adiciona a ela.

4. Forneça os detalhes da conexão para as APIs que você deseja usar. Você pode se conectar a todos ou alguns deles. 
    * Se você se conectar a métricas e logs em Azure Monitor, poderá reutilizar as mesmas credenciais selecionando os **mesmos detalhes que Azure monitor API**.
    * Ao configurar o plug-in, você pode indicar qual nuvem do Azure você gostaria que o plug-in monitorasse (público, governo dos EUA do Azure, Alemanha do Azure ou China do Azure).
    * Se você usar o Application Insights, também poderá incluir a API do Application Insights e a ID do aplicativo para coletar métricas baseadas no Application Insights. Para obter mais informações, consulte [Obtendo sua chave de API e a ID do Aplicativo](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Alguns campos da fonte de dados são nomeados Diferentemente de suas configurações do Azure correlacionadas:
        > * ID do locatário é o ID de diretório do Azure
        > * ID do cliente é o ID do aplicativo do Azure Active Directory
        > * Segredo do cliente é o valor da chave de aplicativo do Azure Active Directory

5. Se você usar o Application Insights, também poderá incluir a API do Application Insights e a ID do aplicativo para coletar métricas baseadas no Application Insights. Para obter mais informações, consulte [Obtendo sua chave de API e a ID do Aplicativo](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Selecione **salvar**, e o Grafana testará as credenciais para cada API. Você deverá ver uma mensagem semelhante à mostrada a seguir.  
    ![Configuração aprovada da fonte de dados do Grafana](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Criar um painel do Grafana

1. Vá para a página inicial do Grafana e selecione **novo painel**.

2. No novo painel, selecione o **Gráfico**. Você pode tentar outras opções de gráficos, mas este artigo usa *Gráfico* como exemplo.

3. Um gráfico em branco é mostrado no painel. Clique no título do painel e selecione **Editar** para inserir os detalhes dos dados que você deseja plotar nesse gráfico.
    ![Novo gráfico do Grafana](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Selecione a fonte de dados do Azure Monitor que você configurou.
   * Coletando métricas do Monitor do Azure - selecione **Azure Monitor** na lista suspensa de serviços. Uma lista de seletores é exibida, onde você pode selecionar os recursos e a métrica a serem monitorados neste gráfico. Para coletar métricas de uma VM, use o namespace **Microsoft.Compute / VirtualMachines**. Depois de selecionar VMs e métricas, você pode começar a visualizar os dados no painel.
     ![Configuração de gráfico do Grafana para o Monitor do Azure](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Coletando dados de log de Azure Monitor-selecione **log Analytics do Azure** na lista suspensa serviço. Selecione o workspace que você deseja consultar e defina o texto da consulta. Você pode copiar aqui qualquer consulta de log que já tenha ou criar uma nova. À medida que você digita sua consulta, o IntelliSense aparecerá e sugerirá opções de preenchimento automático. Selecione o tipo de visualização **Série temporal** **Tabela** e execute a consulta.
    
     > [!NOTE]
     >
     > A consulta padrão fornecida com o plug-in usa duas macros: "$ __ timeFilter () e $ __ interval. 
     > Essas macros permitem que o Grafana calcule dinamicamente o intervalo de tempo e o tempo, quando você aumenta o zoom em parte de um gráfico. Você pode remover essas macros e usar um filtro de hora padrão, como *TimeGenerated> ago (1h)*, mas isso significa que o gráfico não suportaria o recurso de zoom in.
    
     ![Configuração do Grafana graph para o Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Veja a seguir um painel simples com dois gráficos. O gráfico à esquerda mostra o percentual de CPU de duas VMs. O gráfico à direita mostra as transações em uma conta de Armazenamento do Azure discriminadas pelo tipo de API de Transação.
    ![Exemplo de gráficos do Grafana dois](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Opcional: Monitorar suas métricas personalizadas no mesmo servidor do Grafana

Você também pode instalar o Telegraf e o InfluxDB para coletar e plotar tanto métricas customizadas quanto baseadas em agentes na mesma instância do Grafana. Há muitos plug-ins de fonte de dados que você pode usar para reunir essas métricas em um painel.

Também reutilize essa configuração para incluir métricas do servidor do Prometheus. Use o plug-in de fonte de dados do Prometheus na galeria de plug-ins do Grafana.

Estes são bons artigos de referência sobre como usar o Telegraf, InfluxDB, Prometheus e Docker
 - [Como monitorar as métricas do sistema com a Pilha TICK no Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Uma solução de monitoramento para hosts, contêineres e serviços em contêineres do Docker](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Esta é uma imagem de um painel completo do Grafana que contém métricas do Azure Monitor e do Application Insights.
![Métricas de exemplo do Grafana](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Recursos avançados do Grafana

### <a name="variables"></a>Variáveis
Alguns valores de consulta podem ser selecionados por meio de menus suspensos da interface do usuário e atualizados na consulta. Considere a seguinte consulta como um exemplo:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Você pode configurar uma variável que listará todos os valores **Solution** disponíveis e, em seguida, atualizará sua consulta para usá-la.
Para criar uma nova variável, clique no botão Configurações do painel na área superior direita, selecione **Variáveis** e, em seguida, **Nova**.
Na página da variável, defina a origem de dados e a consulta a serem executadas para obter a lista de valores.
![Configurar o Grafana variável](./media/grafana-plugin/grafana-configure-variable-dark.png)

Uma vez criada, ajuste a consulta para usar o (s) valor (es) selecionado (s) e seus gráficos responderão de acordo:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Usar variáveis do Grafana](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Criar playlists do painel

Um dos muitos recursos úteis do Grafana é a playlist do painel. Você pode criar vários painéis e adicioná-los à playlist configurando um intervalo a ser mostrado para cada painel. Selecione **Reproduzir** para ver os painéis serem percorridos. Você pode querer exibi-los em um monitor de parede grande para fornecer uma placa de status para o seu grupo.

![Exemplo de playlist do Grafana](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você configurou um ambiente do Grafana no Azure, você será cobrado quando as VMs são executadas, independentemente de você está usando ou não. Para evitar incorrer em encargos adicionais, limpe o grupo de recursos criado neste artigo.

1. No menu à esquerda no portal do Azure, clique em **Grupos de recursos** e, em seguida, em **Grafana**.
2. Na página do grupo de recursos, clique em **Excluir**, digite **Grafana** na caixa de texto e, em seguida, clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas
* [Visão geral das métricas do Azure Monitor](../data-platform.md)
