---
title: Proteções de carga de trabalho para suas cargas de trabalho do kubernetes
description: Saiba como usar o conjunto de recomendações de segurança de proteção de carga de trabalho kubernetes da central de segurança do Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/16/2021
ms.author: memildin
ms.openlocfilehash: 6d0e660ecce1d45dab4d6003cdba391ba2eb4ee9
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095588"
---
# <a name="protect-your-kubernetes-workloads"></a>Proteger as cargas de trabalho do Kubernetes

Esta página descreve como usar o conjunto de recomendações de segurança da central de segurança do Azure dedicado à proteção de carga de trabalho do kubernetes.

Saiba mais sobre esses recursos nas [práticas recomendadas de proteção de carga de trabalho usando o controle de admissão kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

A central de segurança oferece mais recursos de segurança de contêiner se você habilitar o Azure defender. Especificamente:

- Examinar os registros de contêiner em busca de vulnerabilidades com o [Azure defender para registros de contêiner](defender-for-container-registries-introduction.md)
- Obtenha alertas de detecção de ameaças em tempo real para seus clusters K8s [do Azure defender para kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> Para obter uma lista de *todas as* recomendações de segurança que podem aparecer para os nós e clusters kubernetes, consulte a [seção computação](recommendations-reference.md#recs-compute) da tabela de referência de recomendações.



## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|Gratuita|
|Funções e permissões necessárias:|**Proprietário** ou **administrador de segurança** para editar uma atribuição<br>**Leitor** para exibir as recomendações|
|Requisitos de ambiente:|Kubernetes v 1.14 (ou superior) é necessário<br>Nenhum recurso PodSecurityPolicy (antigo modelo PSP) nos clusters<br>Não há suporte para nós do Windows|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||


## <a name="set-up-your-workload-protection"></a>Configurar a proteção da carga de trabalho

A central de segurança do Azure inclui um conjunto de recomendações que estão disponíveis quando você instalou o **complemento de Azure Policy para o kubernetes**.

### <a name="step-1-deploy-the-add-on"></a>Etapa 1: implantar o complemento

Para configurar as recomendações, instale o  **complemento Azure Policy para kubernetes**. 

- Você pode implantar automaticamente esse complemento, conforme explicado em [habilitar o provisionamento automático do agente e das extensões de log Analytics](security-center-enable-data-collection.md#auto-provision-mma). Quando o provisionamento automático para o complemento é definido como "ativado", a extensão fica habilitada por padrão em todos os clusters existentes e futuros (que atendem aos requisitos de instalação do complemento).

    :::image type="content" source="media/defender-for-kubernetes-usage/policy-add-on-auto-provision.png" alt-text="Usando a ferramenta de provisionamento automático da central de segurança para instalar o complemento de política para kubernetes":::

- Para implantar manualmente o complemento:

    1. Na página recomendações, procure a recomendação "o **complemento Azure Policy para kubernetes deve ser instalado e habilitado em seus clusters**". 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="Recomendação * * Azure Policy complemento para kubernetes deve ser instalado e habilitado em seus clusters * *":::

        > [!TIP]
        > A recomendação está incluída em cinco controles de segurança diferentes e não importa qual deles você selecionou na próxima etapa.

    1. Em qualquer um dos controles de segurança, selecione a recomendação para ver os recursos nos quais você pode instalar o complemento.
    1. Selecione o cluster relevante e **Corrija**.

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="A página de detalhes de recomendação para * * Azure Policy complemento para kubernetes deve ser instalada e habilitada em seus clusters * *":::

### <a name="step-2-view-and-configure-the-bundle-of-13-recommendations"></a>Etapa 2: exibir e configurar o grupo de 13 recomendações

1. Aproximadamente 30 minutos após a conclusão da instalação do complemento, a central de segurança mostra o status de integridade dos clusters para as seguintes recomendações, cada um no controle de segurança relevante, conforme mostrado:

    > [!TIP]
    > Algumas recomendações têm parâmetros que devem ser personalizados por meio de Azure Policy para usá-los com eficiência. Por exemplo, para se beneficiar das imagens de contêiner de recomendação **deve ser implantado somente de registros confiáveis**, você precisará definir seus registros confiáveis.
    > 
    > Se você não inserir os parâmetros necessários para as recomendações que exigem configuração, suas cargas de trabalho serão mostradas como não íntegras.

    | Nome da recomendação                                                         | Controle de segurança                         | Configuração necessária |
    |-----------------------------------------------------------------------------|------------------------------------------|------------------------|
    | Deverão ser aplicados limites de memória e CPU ao contêiner                          | Proteger aplicativos contra ataques de DDoS | Não                     |
    | Os contêineres com privilégios deverão ser evitados                                     | Gerenciar acesso e permissões            | Não                     |
    | Um sistema de arquivos raiz imutável (somente leitura) deverá ser aplicado aos contêineres     | Gerenciar acesso e permissões            | Não                     |
    | Os contêineres com elevação de privilégio deverão ser evitados                       | Gerenciar acesso e permissões            | Não                     |
    | Executar contêineres como usuário raiz deverá ser evitado                           | Gerenciar acesso e permissões            | Não                     |
    | Os contêineres que compartilham namespaces de host confidenciais deverão ser evitados              | Gerenciar acesso e permissões            | Não                     |
    | Os recursos do Linux com privilégios mínimos devem ser impostos para contêineres       | Gerenciar acesso e permissões            | **Sim**                |
    | Usar montagens de volumes de HostPath do pod deverá ser restrito a uma lista conhecida    | Gerenciar acesso e permissões            | **Sim**                |
    | Os contêineres deverão escutar somente em portas permitidas                              | Restringir o acesso não autorizado à rede     | **Sim**                |
    | Os serviços deverão escutar somente em portas permitidas                                | Restringir o acesso não autorizado à rede     | **Sim**                |
    | Usar redes e portas do host deverá ser restrito                     | Restringir acesso não autorizado à rede     | **Sim**                |
    | As ações para substituir ou desabilitar o perfil do AppArmor de contêineres deverão ser restritas | Corrigir as configurações de segurança        | **Sim**                |
    | As imagens de contêiner devem ser implantadas apenas de registros confiáveis            | Corrigir vulnerabilidades                | **Sim**                |
    |||


1. Para as recomendações com parâmetros devem ser personalizadas, defina os parâmetros:

    1. No menu da central de segurança, selecione **política de segurança**.
    1. Selecione a assinatura relevante.
    1. Na seção **política padrão da central de segurança** , selecione **Exibir política efetiva**.
    1. Selecione "ASC padrão".
    1. Abra a guia **parâmetros** e modifique os valores conforme necessário.
    1. Selecione **Examinar + salvar**.
    1. Clique em **Salvar**.


1. Para impor qualquer uma das recomendações, 

    1. Abra a página de detalhes de recomendação e selecione **negar**:

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Opção Deny para o parâmetro Azure Policy":::

        Isso abrirá o painel em que o escopo será definido. 

    1. Quando você tiver definido o escopo, selecione **alterar para negar**.

1. Para ver quais recomendações se aplicam aos seus clusters:

    1. Abra a página [inventário de ativos](asset-inventory.md) da central de segurança e use o filtro tipo de recurso para **Serviços Kubernetess**.

    1. Selecione um cluster para investigar e revisar as recomendações disponíveis para ele. 

1. Ao exibir uma recomendação do conjunto de proteção de carga de trabalho, você verá o número de pods afetados ("componentes kubernetes") listados junto com o cluster. Para obter uma lista de pods específicos, selecione o cluster e, em seguida, selecione **executar ação**.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="Exibindo o pods afetado por uma recomendação de K8s"::: 

1. Para testar a imposição, use as duas implantações kubernetes abaixo:

    - Uma é para uma implantação íntegra, em conformidade com o pacote de recomendações de proteção de carga de trabalho.
    - A outra é para uma implantação não íntegra, não compatível com *nenhuma* das recomendações.

    Implante os arquivos de exemplo. YAML no estado em que se encontram ou use-os como uma referência para corrigir sua própria carga de trabalho (etapa VIII)  


## <a name="healthy-deployment-example-yaml-file"></a>Arquivo. YAML de exemplo de implantação íntegra

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        apparmor.security.beta.kubernetes.io/pod: runtime/default
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>Arquivo. YAML de exemplo de implantação não íntegra

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a configurar a proteção de carga de trabalho do kubernetes. 

Para obter outros materiais relacionados, consulte as seguintes páginas: 

- [Recomendações da central de segurança para computação](recommendations-reference.md#recs-compute)
- [Alertas para o nível de cluster AKS](alerts-reference.md#alerts-akscluster)
- [Alertas para o nível de host do contêiner](alerts-reference.md#alerts-containerhost)