---
title: Implantar um gateway auto-hospedado no kubernetes | Microsoft Docs
description: Saiba como implantar um componente de gateway auto-hospedado do gerenciamento de API do Azure para kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 023c2c89b90d6ddc71abc95db325dcdeb7684a2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89500123"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>Implantar um gateway auto-hospedado no Kubernetes

Este artigo descreve as etapas para implantar o componente de gateway auto-hospedado do gerenciamento de API do Azure para um cluster kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
- Crie um cluster kubernetes.
   > [!TIP]
   > [Clusters de nó único](https://kubernetes.io/docs/setup/#learning-environment) funcionam bem para fins de desenvolvimento e avaliação. Use clusters de vários nós [certificados pelo kubernetes](https://kubernetes.io/partners/#conformance) no local ou na nuvem para cargas de trabalho de produção.
- [Provisione um recurso de gateway auto-hospedado em sua instância de gerenciamento de API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Implantar para o Kubernetes

1. Selecione **gateways** em **implantação e infraestrutura**.
2. Selecione o recurso de gateway auto-hospedado que você deseja implantar.
3. Selecione **implantação**.
4. Um token de acesso na caixa de texto de **token** foi gerado automaticamente para você, com base nos valores padrão de **expiração** e **chave secreta** . Se necessário, escolha valores em um ou ambos os controles para gerar um novo token.
5. Selecione a guia **kubernetes** em **scripts de implantação**.
6. Selecione o link do arquivo ** \<gateway-name\> . yml** e baixe o arquivo YAML.
7. Selecione o ícone de **cópia** no canto inferior direito da caixa de texto **implantar** para salvar os comandos na `kubectl` área de transferência.
8. Colar comandos na janela de terminal (ou comando). O primeiro comando cria um segredo kubernetes que contém o token de acesso gerado na etapa 4. O segundo comando aplica o arquivo de configuração baixado na etapa 6 ao cluster kubernetes e espera que o arquivo esteja no diretório atual.
9. Execute os comandos para criar os objetos kubernetes necessários no [namespace padrão](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) e inicie o pods de gateway auto-hospedado da [imagem de contêiner](https://aka.ms/apim/sputnik/dhub) baixada do registro de contêiner da Microsoft.
10. Execute o comando a seguir para verificar se a implantação foi bem-sucedida. Observe que pode levar algum tempo para que todos os objetos sejam criados e que os pods sejam inicializados.
    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. Execute o comando a seguir para verificar se o serviço foi criado com êxito. Observe que o IPs de serviço e as portas serão diferentes.
    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
12. Volte para a portal do Azure e selecione **visão geral**.
13. Confirme se o **status** mostra uma marca de seleção verde, seguida por uma contagem de nós que corresponde ao número de réplicas especificado no arquivo YAML. Esse status significa que o pods de gateway de hospedagem interna implantado está se comunicando com êxito com o serviço de gerenciamento de API e tem uma "pulsação" regular.

    ![Status do gateway](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Execute o <code>kubectl logs deployment/<gateway-name></code> comando para exibir os logs de um pod selecionado aleatoriamente se houver mais de um.
> Execute <code>kubectl logs -h</code> para obter um conjunto completo de opções de comando, por exemplo, como exibir logs para um pod ou contêiner específico.

## <a name="production-deployment-considerations"></a>Considerações sobre implantação de produção

### <a name="access-token"></a>Token de acesso
Sem um token de acesso válido, um gateway auto-hospedado não pode acessar e baixar dados de configuração do ponto de extremidade do serviço de gerenciamento de API associado. O token de acesso pode ser válido por um máximo de 30 dias. Ele deve ser regenerado e o cluster configurado com um token atualizado, seja manualmente ou por meio da automação antes de expirar.

Quando você estiver automatizando a atualização de token, use [essa operação de API de gerenciamento](/rest/api/apimanagement/2019-12-01/gateway/generatetoken) para gerar um novo token. Para obter informações sobre como gerenciar segredos do kubernetes, consulte o [site do kubernetes](https://kubernetes.io/docs/concepts/configuration/secret).

### <a name="namespace"></a>Namespace
Os [namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) kubernetes ajudam a dividir um único cluster entre várias equipes, projetos ou aplicativos. Os namespaces fornecem um escopo para recursos e nomes. Eles podem ser associados a uma cota de recursos e a políticas de controle de acesso.

O portal do Azure fornece comandos para criar recursos de gateway de hospedagem interna no namespace **padrão** . Esse namespace é criado automaticamente, existe em cada cluster e não pode ser excluído.
Considere [criar e implantar](https://kubernetesbyexample.com/ns/) um gateway auto-hospedado em um namespace separado na produção.

### <a name="number-of-replicas"></a>Número de réplicas
O número mínimo de réplicas adequadas para a produção é dois.

Por padrão, um gateway auto-hospedado é implantado com uma [estratégia](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)de implantação **RollingUpdate** . Examine os valores padrão e considere definir explicitamente os campos [maxUnavailable](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) e [maxSurge](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) , especialmente quando você estiver usando uma contagem de réplicas alta.

### <a name="container-resources"></a>Recursos de contêiner
Por padrão, o arquivo YAML fornecido no portal do Azure não especifica solicitações de recursos de contêiner.

É impossível prever e recomendar de forma confiável a quantidade de recursos de CPU e memória por contêiner e o número de réplicas necessárias para dar suporte a uma carga de trabalho específica. Muitos fatores estão em jogo, como:

- Hardware específico em que o cluster está sendo executado.
- Presença e tipo de virtualização.
- Número e taxa de conexões de cliente simultâneas.
- Taxa de solicitação.
- Tipo e número de políticas configuradas.
- Tamanho da carga e se as cargas são armazenadas em buffer ou transmitidas.
- Latência do serviço de back-end.

É recomendável definir solicitações de recursos para dois núcleos e 2 GiB como um ponto de partida. Executar um teste de carga e escalar verticalmente ou horizontalmente com base nos resultados.

### <a name="container-image-tag"></a>Marca de imagem de contêiner
O arquivo YAML fornecido no portal do Azure usa a marca **mais recente** . Essa marca sempre faz referência à versão mais recente da imagem de contêiner do gateway auto-hospedado.

Considere usar uma marca de versão específica em produção para evitar a atualização não intencional para uma versão mais recente.

Você pode [baixar uma lista completa de marcas disponíveis](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list).

### <a name="dns-policy"></a>Política de DNS
A resolução de nomes DNS desempenha uma função crítica em uma capacidade de gateway de hospedagem interna para se conectar a dependências no Azure e enviar chamadas de API para serviços de back-end.

O arquivo YAML fornecido no portal do Azure aplica a política de [ClusterFirst](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) padrão. Essa política faz com que as solicitações de resolução de nomes não resolvidas pelo DNS do cluster sejam encaminhadas para o servidor DNS upstream herdado do nó.

Para saber mais sobre a resolução de nomes no kubernetes, consulte o [site do kubernetes](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service). Considere a personalização da [política DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) ou da [configuração de DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) conforme apropriado para sua configuração.

### <a name="external-traffic-policy"></a>Política de tráfego externo
O arquivo YAML fornecido no campo conjuntos de portal do Azure `externalTrafficPolicy` no objeto de [serviço](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#service-v1-core) para `Local` . Isso preserva o endereço IP do chamador (acessível no [contexto da solicitação](api-management-policy-expressions.md#ContextVariables)) e desabilita o balanceamento de carga entre nós, eliminando os saltos de rede causados por ele. Lembre-se de que essa configuração pode causar a distribuição assimétrica de tráfego em implantações com um número desigual de pods de gateway por nó.

### <a name="custom-domain-names-and-ssl-certificates"></a>Nomes de domínio personalizados e certificados SSL

Se você usar nomes de domínio personalizados para os pontos de extremidade de gerenciamento de API, especialmente se usar um nome de domínio personalizado para o ponto de fim de gerenciamento, talvez seja necessário atualizar o valor de `config.service.endpoint` no arquivo ** \<gateway-name\> . YAML** para substituir o nome de domínio padrão pelo nome de domínio personalizado. Verifique se o ponto de extremidade de gerenciamento pode ser acessado do Pod do gateway auto-hospedado no cluster kubernetes.

Nesse cenário, se o certificado SSL usado pelo ponto de extremidade de gerenciamento não estiver assinado por um certificado de autoridade de certificação conhecido, você deverá verificar se o certificado de autoridade de certificação é confiável pelo Pod do gateway de hospedagem interna.

### <a name="configuration-backup"></a>Backup de configuração
Para saber mais sobre o comportamento de gateway auto-hospedado na presença de uma interrupção de conectividade temporária do Azure, consulte [visão geral do gateway de hospedagem interna](self-hosted-gateway-overview.md#connectivity-to-azure).

Configure um volume de armazenamento local para o contêiner de gateway auto-hospedado, para que ele possa persistir uma cópia de backup da última configuração baixada. Se a conectividade estiver inativa, o volume de armazenamento poderá usar a cópia de backup após a reinicialização. O caminho de montagem do volume deve ser <code>/apim/config</code> . Consulte um exemplo no [GitHub](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml).
Para saber mais sobre o armazenamento no kubernetes, consulte o [site do kubernetes](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Métricas e logs locais
O gateway auto-hospedado envia telemetria para [Azure monitor](api-management-howto-use-azure-monitor.md) e [aplicativo Azure informações](api-management-howto-app-insights.md) de acordo com as definições de configuração no serviço de gerenciamento de API associado.
Quando a [conectividade com o Azure](self-hosted-gateway-overview.md#connectivity-to-azure) é temporariamente perdida, o fluxo de telemetria para o Azure é interrompido e os dados são perdidos durante a interrupção.
Considere [Configurar o monitoramento local](how-to-configure-local-metrics-logs.md) para garantir a capacidade de observar o tráfego de API e evitar a perda de telemetria durante interrupções de conectividade do Azure.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gateway auto-hospedado, consulte [visão geral do gateway](self-hosted-gateway-overview.md)de hospedagem interna.
