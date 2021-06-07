---
title: Proteções de cargas de trabalho para Kubernetes
description: Saiba como usar o conjunto de recomendações de segurança para proteção de cargas de trabalho do Kubernetes da Central de Segurança do Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: eacca5573c672e9f4485c26b1b580ee4c982c5d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580736"
---
# <a name="protect-your-kubernetes-workloads"></a>Proteger as cargas de trabalho do Kubernetes

Esta página descreve como usar o conjunto de recomendações de segurança da Central de Segurança do Azure dedicado à proteção de cargas de trabalho do Kubernetes.

Saiba mais sobre esses recursos em [Melhores práticas de proteção de cargas de trabalho usando o controle de admissão do Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

A Central de Segurança oferece mais recursos de segurança de contêiner quando você habilita o Azure Defender. Especificamente:

- Examine seus registros de contêiner para detectar vulnerabilidades com o [Azure Defender para registros de contêiner](defender-for-container-registries-introduction.md)
- Receba alertas de detecção de ameaças em tempo real para seus clusters K8s do [Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> Para ver uma lista com *todas* as recomendações de segurança que podem aparecer para nós e clusters do Kubernetes, confira a [seção de computação](recommendations-reference.md#recs-compute) da tabela de referência de recomendações.



## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|Gratuita|
|Funções e permissões necessárias:|**Proprietário** ou **Administrador de Segurança** para editar uma atribuição<br>**Leitor** para exibir as recomendações|
|Requisitos do ambiente:|O Kubernetes v1.14 (ou superior) é necessário<br>Nenhum recurso de PodSecurityPolicy (antigo modelo PSP) nos clusters<br>Não há suporte para nós do Windows|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||


## <a name="set-up-your-workload-protection"></a>Configurar a proteção de cargas de trabalho

A Central de Segurança do Azure inclui um pacote de recomendações que ficam disponíveis quando você instala o **Complemento do Azure Policy para Kubernetes**.

### <a name="step-1-deploy-the-add-on"></a>Etapa 1: Implantar o complemento

Para configurar as recomendações, instale o **Complemento do Azure Policy para Kubernetes**. 

- Você pode implantar automaticamente esse complemento, conforme explicado em [Habilitar o provisionamento automático de extensões e do agente do Log Analytics](security-center-enable-data-collection.md#auto-provision-mma). Quando o provisionamento automático para o complemento é definido como "ativado", a extensão fica habilitada por padrão em todos os clusters existentes e futuros (que atendem aos requisitos de instalação do complemento).

    :::image type="content" source="media/defender-for-kubernetes-usage/policy-add-on-auto-provision.png" alt-text="Usando a ferramenta de provisionamento automático da Central de Segurança para instalar o complemento de política para Kubernetes":::

- Para implantar o complemento manualmente:

    1. Na página de recomendações, procure a recomendação "**O complemento do Azure Policy para Kubernetes deve estar instalado e habilitado nos clusters**". 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="Recomendação **O complemento do Azure Policy para Kubernetes deve estar instalado e habilitado nos clusters**":::

        > [!TIP]
        > A recomendação está incluída em cinco controles de segurança diferentes e não importa qual deles você seleciona na próxima etapa.

    1. Para qualquer um dos controles de segurança, selecione a recomendação para ver os recursos em que você pode instalar o complemento.
    1. Selecione o cluster relevante e escolha **Corrigir**.

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="Página de detalhes da recomendação **O complemento do Azure Policy para Kubernetes deve estar instalado e habilitado nos clusters**":::

### <a name="step-2-view-and-configure-the-bundle-of-13-recommendations"></a>Etapa 2: Exibir e configurar o pacote de 13 recomendações

1. Aproximadamente 30 minutos após a conclusão da instalação do complemento, a Central de Segurança mostra o status de integridade dos clusters quanto às seguintes recomendações, cada qual no controle de segurança relevante, conforme mostrado:

    > [!TIP]
    > Algumas recomendações têm parâmetros que precisam ser personalizados por meio do Azure Policy para que sejam usadas com eficiência. Por exemplo, para se beneficiar da recomendação **Imagens de contêiner devem ser implantadas somente de registros confiáveis**, você precisará definir os registros confiáveis.
    > 
    > Se você não inserir os parâmetros necessários para as recomendações que exigem configuração, as cargas de trabalho serão indicadas como não íntegras.

    | Nome da recomendação                                                         | Controle de segurança                         | Configuração necessária |
    |-----------------------------------------------------------------------------|------------------------------------------|------------------------|
    | Deverão ser aplicados limites de memória e CPU ao contêiner                          | Proteger aplicativos contra ataques de DDoS | Não                     |
    | Os contêineres com privilégios deverão ser evitados                                     | Gerenciar acesso e permissões            | Não                     |
    | Um sistema de arquivos raiz imutável (somente leitura) deverá ser aplicado aos contêineres     | Gerenciar acesso e permissões            | Não                     |
    | Os contêineres com elevação de privilégio deverão ser evitados                       | Gerenciar acesso e permissões            | Não                     |
    | Executar contêineres como usuário raiz deverá ser evitado                           | Gerenciar acesso e permissões            | Não                     |
    | Os contêineres que compartilham namespaces de host confidenciais deverão ser evitados              | Gerenciar acesso e permissões            | Não                     |
    | Deverão ser aplicadas aos contêineres funcionalidades do Linux com privilégios mínimos       | Gerenciar acesso e permissões            | **Sim**                |
    | Usar montagens de volumes de HostPath do pod deverá ser restrito a uma lista conhecida    | Gerenciar acesso e permissões            | **Sim**                |
    | Os contêineres deverão escutar somente em portas permitidas                              | Restringir o acesso não autorizado à rede     | **Sim**                |
    | Os serviços deverão escutar somente em portas permitidas                                | Restringir o acesso não autorizado à rede     | **Sim**                |
    | Usar redes e portas do host deverá ser restrito                     | Restringir acesso não autorizado à rede     | **Sim**                |
    | As ações para substituir ou desabilitar o perfil do AppArmor de contêineres deverão ser restritas | Corrigir as configurações de segurança        | **Sim**                |
    | As imagens de contêiner devem ser implantadas apenas de registros confiáveis            | Corrigir vulnerabilidades                | **Sim**                |
    |||


1. Para recomendações cujos parâmetros devem ser personalizados, defina os parâmetros:

    1. No menu da Central de Segurança, selecione **Política de segurança**.
    1. Selecione a assinatura relevante.
    1. Na seção **Política padrão da Central de Segurança**, selecione **Exibir política efetiva**.
    1. Selecione "ASC Padrão".
    1. Abra a guia **Parâmetros** e modifique os valores conforme necessário.
    1. Selecione **Examinar + salvar**.
    1. Selecione **Salvar**.


1. Para impor recomendações, 

    1. Abra a página de detalhes da recomendação e selecione **Negar**:

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Opção Negar para o parâmetro do Azure Policy":::

        Isso abrirá o painel no qual você define o escopo. 

    1. Após definir o escopo, selecione **Alterar para negar**.

1. Para ver quais recomendações se aplicam aos seus clusters:

    1. Abra a página de [inventário de ativos](asset-inventory.md) da Central de Segurança e use o filtro de tipo de recurso para os **Serviços do Kubernetes**.

    1. Selecione um cluster para investigar e analise as recomendações disponíveis para ele. 

1. Ao exibir uma recomendação do conjunto de proteção de cargas de trabalho, você verá o número de pods afetados ("componentes do Kubernetes") listado com o cluster. Para ver uma lista de pods específicos, selecione o cluster e escolha **Executar ação**.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="Exibindo o pods afetados por uma recomendação de K8s"::: 

1. Para testar a imposição, use as duas implantações do Kubernetes abaixo:

    - Uma é para uma implantação íntegra, em conformidade com o pacote de recomendações de proteção de cargas de trabalho.
    - A outra é para uma implantação não íntegra, que não está em conformidade com *nenhuma* das recomendações.

    Implante os arquivos .yaml de exemplo no estado em que se encontram ou use-os como referência para corrigir sua carga de trabalho (etapa VIII)  


## <a name="healthy-deployment-example-yaml-file"></a>Arquivo .yaml de exemplo de implantação íntegra

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

## <a name="unhealthy-deployment-example-yaml-file"></a>Arquivo .yaml de exemplo de implantação não íntegra

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

Neste artigo, você aprendeu a configurar a proteção de cargas de trabalho do Kubernetes. 

Para ver outros materiais relacionados, confira as seguintes páginas: 

- [Recomendações da Central de Segurança para computação](recommendations-reference.md#recs-compute)
- [Alertas para o nível do cluster do AKS](alerts-reference.md#alerts-akscluster)
- [Alertas para o nível de host do contêiner](alerts-reference.md#alerts-containerhost)