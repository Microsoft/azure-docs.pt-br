---
title: Solucionar problemas de grupos de servidores de hiperescala PostgreSQL
description: Solucionar problemas de grupos de servidores de hiperescala PostgreSQL com um Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: caaab07200a8631935a2b5d5368a0c16ea9a60c5
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320227"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Solucionando problemas de grupos de servidores de hiperescala PostgreSQL
Este artigo descreve algumas técnicas que você pode usar para solucionar problemas do seu grupo de servidores. Além deste artigo, talvez você queira ler como usar o [Kibana](monitor-grafana-kibana.md) para pesquisar os logs ou usar o [Grafana](monitor-grafana-kibana.md) para visualizar as métricas sobre o grupo de servidores. 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>Obtendo mais detalhes sobre a execução de um comando azdata
Você pode adicionar o parâmetro **--debug** a qualquer comando azdata que você executar. Isso será exibido para as informações adicionais do seu console sobre a execução desse comando. Você deve achar útil obter detalhes para ajudá-lo a entender o comportamento desse comando.
Por exemplo, você pode executar
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

ou
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

Além disso, você pode usar o parâmetro--Help em qualquer comando azdata para exibir alguma ajuda, lista de parâmetros para um comando específico. Por exemplo:
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>Coletando logs do controlador de dados e de seus grupos de servidores
Leia o artigo sobre como [obter logs para os serviços de dados habilitados para o Azure Arc](troubleshooting-get-logs.md)



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Solução de problemas interativa com notebooks Jupyter no Azure Data Studio
Os notebooks podem documentar os procedimentos, incluindo o conteúdo de redução para descrever o que fazer/como fazê-lo. Eles também podem fornecer código executável para automatizar um procedimento.  Esse padrão é útil para tudo, desde procedimentos operacionais padrão até guias de solução de problemas.

Por exemplo, vamos solucionar um grupo de servidores de hiperescala PostgreSQL que pode ter alguns problemas usando Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>Instalar ferramentas

Instale o Azure Data Studio `kubectl` e [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] no computador cliente que você está usando para executar o bloco de anotações em Azure Data Studio. Para fazer isso, siga as instruções em [instalar ferramentas de cliente](install-client-tools.md)

### <a name="update-the-path-environment-variable"></a>Atualizar a variável de ambiente PATH

Verifique se essas ferramentas podem ser invocadas de qualquer lugar neste computador cliente. Por exemplo, em um computador cliente Windows, atualize a variável de ambiente do sistema PATH e adicione a pasta na qual você instalou o kubectl.

### <a name="sign-in-with-azure-data-cli-azdata"></a>Entrar com [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Conecte seu controlador de dados de arco a partir desta máquina cliente e antes de iniciar o Azure Data Studio. Para fazer isso, execute um comando como:

```console
azdata login --endpoint https://<IP address>:<port>
```

Substitua `<IP address>` pelo endereço IP do cluster kubernetes e `<port>` pela porta na qual o kubernetes está escutando. Você será solicitado a fornecer o nome de usuário e a senha. Para ver mais detalhes, execute: _

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Faça logon em seu cluster do kubernetes com kubectl

Para fazer isso, talvez você queira usar os comandos de exemplo [fornecidos nesta postagem de blog.](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/)
Você executaria comandos como:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>O notebook de solução de problemas

Inicie o Azure Data Studio e abra o notebook de solução de problemas. 

Implemente as etapas descritas em  [033-Manage-postgres-with-AzureDataStudio.MD](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) para:

1. Conectar-se ao controlador de dados Arc
2. Selecione a instância do postgres e escolha **[gerenciar]**
3. Selecione o **painel [diagnosticar e resolver problemas]**
4. Selecione o **link [solucionar problemas]**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Bloco de notas de solução de problemas do Azure Data Studio Open PostgreSQL":::

O **TSG100-o notebook de solução de problemas de hiperescala do PostgreSQL habilitado para o Azure Arc** abre: :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Bloco de notas de solução de problemas do Azure Data Studio Open PostgreSQL":::

#### <a name="run-the-scripts"></a>Executar os scripts
Selecione o botão ' executar tudo ' na parte superior para executar o bloco de anotações de uma só vez ou você pode percorrer e executar cada célula de código uma a uma.

Exiba a saída da execução das células de código para quaisquer possíveis problemas.

Vamos adicionar mais detalhes ao notebook ao longo do tempo sobre como reconhecer problemas comuns e como solucioná-los.

## <a name="next-step"></a>Próxima etapa
- Leia sobre como [obter logs para os serviços de dados habilitados para o Azure Arc](troubleshooting-get-logs.md)
- Leia sobre como [pesquisar logs com o Kibana](monitor-grafana-kibana.md)
- Leia sobre [monitoramento com o Grafana](monitor-grafana-kibana.md)
- Crie seus próprios blocos de anotações
