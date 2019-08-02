---
title: Instalar Istio no AKS (Serviço de Kubernetes do Azure)
description: Saiba como instalar e usar o Istio para criar uma malha de serviço em um cluster do AKS (Serviço de Kubernetes do Azure)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 9d973cb2ac210e912d93941a2f81889557379f43
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625989"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalar e usar o Istio no AKS (Serviço de Kubernetes do Azure)

[Istio][istio-github] is an open-source service mesh that provides a key set of functionality across the microservices in a Kubernetes cluster. These features include traffic management, service identity and security, policy enforcement, and observability. For more information about Istio, see the official [What is Istio?][istio-docs-concepts] documentação.

Este artigo mostra como instalar o Istio. O Istio `istioctl` binários do cliente é instalado no computador cliente e os componentes do Istio estão instalados em um cluster Kubernetes no AKS.

> [!NOTE]
> Estas instruções fazem referência a versão Istio `1.1.3`.
>
> O Istio `1.1.x` versões foram testadas pela equipe em relação a versões do Kubernetes Istio `1.11`, `1.12`, `1.13`. Você pode encontrar versões Istio adicionais no [GitHub - versões Istio][istio-github-releases] and information about each of the releases at [Istio - Release Notes][istio-release-notes].

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Baixar o Istio
> * Instalar o cliente de istioctl Istio binário
> * Instalar os CRDs Istio no AKS
> * Instale os componentes de Istio no AKS
> * Validar a instalação do Istio
> * Acessar os complementos
> * Desinstalar o Istio do AKS

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste artigo pressupõem que você criou um cluster do AKS (Kubernetes `1.11` e acima, com o RBAC habilitado) e ter estabelecido um `kubectl` conexão com o cluster. Se você precisar de ajuda com qualquer um desses itens, consulte o [início rápido do AKS][aks-quickstart].

Você precisará [Helm][helm] siga estas instruções e instalar o Istio. É recomendável que você tenha a versão `2.12.2` ou posterior corretamente instalado e configurado no cluster. Se você precisar de ajuda com a instalação do Helm, consulte o [diretrizes de instalação do Helm AKS][helm-install]. Todos os compartimentos de Istio também devem ser agendados para execução em nós do Linux.

Este artigo separa as diretrizes de instalação do Istio em várias etapas discretas. O resultado final é o mesmo na estrutura da instalação oficial do Istio [orientação][istio-install-helm].

## <a name="download-istio"></a>Baixar o Istio

Primeiro, baixe e extraia a última versão do Istio. As etapas são um pouco diferentes para um shell bash no MacOS, Linux ou subsistema Windows para Linux e para um shell do PowerShell. Escolha uma das seguintes etapas de instalação que corresponde ao seu ambiente preferencial:

* [Bash ni MacOS, Linux ou Subsistema do Windows para Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

No MacOS, use `curl` para baixar a última versão do Istio e, em seguida, extrair com `tar`, conforme a seguir:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

No Linux ou no Subsistema do Windows para Linux, use `curl` para baixar a última versão do Istio e, em seguida, extrair com `tar`, conforme a seguir:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Agora ir para a seção [instalar o cliente de istioctl Istio binário](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

No PowerShell, use `Invoke-WebRequest` para baixar a versão mais recente do Istio e, em seguida, extrair com `Expand-Archive` da seguinte maneira:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Agora ir para a seção [instalar o cliente de istioctl Istio binário](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Instalar o cliente de istioctl Istio binário

> [!IMPORTANT]
> Certifique-se de que você execute as etapas nesta seção, da pasta de nível superior da versão Istio que você baixou e extraiu.

O `istioctl` binários do cliente é executado no computador cliente e permite que você interaja com a malha de serviço Istio. As etapas de instalação são um pouco diferentes entre os sistemas operacionais de cliente. Escolha uma das seguintes etapas de instalação que corresponde ao seu ambiente preferencial:

* [MacOS](#macos)
* [Linux ou o subsistema do Windows para Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

Para instalar o binário de cliente do Istio `istioctl` em um shell baseado em bash no MacOS, use os comandos a seguir. Esses comandos copiam o `istioctl` binário de cliente para o local do programa do usuário padrão no `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Se você quiser concluir a linha de comando do binário de cliente do Istio `istioctl`, configure-o conforme a seguir:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Agora passaremos para a próxima seção para [instalar o CRDs Istio no AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux ou Subsistema do Windows para Linux

Use os comandos a seguir para instalar o Istio `istioctl` cliente binário em um shell de bash no Linux ou [subsistema Windows para Linux][install-wsl]. Esses comandos copiam o `istioctl` binário de cliente para o local do programa do usuário padrão no `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Se você quiser concluir a linha de comando do binário de cliente do Istio `istioctl`, configure-o conforme a seguir:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Agora passaremos para a próxima seção para [instalar o CRDs Istio no AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Para instalar o Istio `istioctl` cliente binário em um **Powershell**-shell com base no Windows, use os seguintes comandos. Copie estes comandos os `istioctl` binário para uma pasta Istio de cliente e, em seguida, disponibilizá-lo imediatamente (no shell atual) e permanentemente (entre as reinicializações de shell) por meio de seu `PATH`. Você não precisa de privilégios de (administrador) com privilégios elevados para executar esses comandos, e você não precisa reiniciar o shell.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

Agora passaremos para a próxima seção para [instalar o CRDs Istio no AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Instalar os CRDs Istio no AKS

> [!IMPORTANT]
> Certifique-se de que você execute as etapas nesta seção, da pasta de nível superior da versão Istio que você baixou e extraiu.

Usa o Istio [definições de recurso personalizado (CRDs)][kubernetes-crd] para gerenciar sua configuração de tempo de execução. É necessário instalar o CRDs Istio em primeiro lugar, desde que os componentes de Istio têm uma dependência neles. Use o Helm e o `istio-init` gráfico para instalar o CRDs Istio no `istio-system` namespace em seu cluster do AKS:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Trabalhos][kubernetes-jobs] são implantadas como parte do `istio-init` gráfico do Helm para instalar os CRDs. Esses trabalhos devem levar entre 1 a 2 minutos para ser concluído, dependendo do seu ambiente de cluster. Você pode verificar que os trabalhos foram concluídos com êxito da seguinte maneira:

```azurecli
kubectl get jobs -n istio-system
```

A saída de exemplo a seguir mostra os trabalhos concluídos com êxito.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Agora que temos confirmado a conclusão bem-sucedida dos trabalhos, vamos verificar que temos o número correto de CRDs Istio instalado. Você pode verificar se todos os CRDs Istio 53 foram instaladas executando o comando apropriado para seu ambiente. O comando deve retornar o número `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

Powershell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Se você tem até o momento, que significa que o CRDs Istio foi instalado com êxito. Agora passaremos para a próxima seção para [instale os componentes do Istio AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Instale os componentes de Istio no AKS

> [!IMPORTANT]
> Certifique-se de que você execute as etapas nesta seção, da pasta de nível superior da versão Istio que você baixou e extraiu.

Estamos vai ser instalando [Grafana][grafana] and [Kiali][kiali] como parte da nossa instalação Istio. Grafana fornece análises e painéis de monitoramento e Kiali fornece um painel de observação de malha do serviço. Em nossa configuração, cada um desses componentes requer credenciais que devem ser fornecidas como uma [segredo][segredos do kubernetes].

Antes que podemos instalar os componentes do Istio, devemos criar os segredos para Grafana e Kiali. Crie esses segredos executando os comandos apropriados para seu ambiente.

### <a name="add-grafana-secret"></a>Adicionar segredo do Grafana

Substitua o `REPLACE_WITH_YOUR_SECURE_PASSWORD` de token com sua senha e execute os seguintes comandos:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>Adicionar segredo Kiali

Substitua o `REPLACE_WITH_YOUR_SECURE_PASSWORD` de token com sua senha e execute os seguintes comandos:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>Instalar componentes Istio

Agora que criamos com êxito os Grafana e Kiali segredos em nosso cluster AKS, chegou a hora para instalar os componentes do Istio. Use o Helm e o `istio` gráfico para instalar os componentes de Istio no `istio-system` namespace em seu cluster do AKS. Use os comandos apropriados para seu ambiente.

> [!NOTE]
> Estamos usando as opções a seguir como parte da nossa instalação:
> - `global.controlPlaneSecurityEnabled=true` -habilitado para o plano de controle TLS mútua
> - `mixer.adapters.useAdapterCRDs=false` -Remova inspeções no adaptador do mixer CRDs conforme eles preterido e isso irá melhorar o desempenho
> - `grafana.enabled=true` -Habilitar a implantação do Grafana para análises e painéis de monitoramento
> - `grafana.security.enabled=true` -Habilitar a autenticação para o Grafana
> - `tracing.enabled=true` -Habilitar a implantação de Jaeger para rastreamento
> - `kiali.enabled=true` -Habilitar a implantação de Kiali para um painel de observação de malha de serviço

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Powershell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

O `istio` gráfico do Helm implanta um grande número de objetos. Você pode ver a lista da saída do seu `helm install` comando acima. A implantação dos componentes do Istio pode levar de 4 a 5 minutos para ser concluído, dependendo do seu ambiente de cluster.

> [!NOTE]
> Todos os compartimentos de Istio devem ser agendados para execução em nós do Linux. Se você tiver pools de nós do Windows Server, além de pools de nós do Linux no seu cluster, verifique se todos os compartimentos Istio foram agendados para execução em nós do Linux.

Neste ponto, você implantou Istio no cluster do AKS. Para garantir que temos uma implantação bem-sucedida do Istio, vamos passar para a próxima seção para [validar a instalação do Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Validar a instalação do Istio

Primeiro, confirme se os serviços esperados foram criados. Use o [kubectl get svc][kubectl-get] comando para exibir os serviços em execução. Consulta de `istio-system` namespace, onde os componentes do Istio e complemento foram instalados pelo `istio` gráfico do Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

A saída de exemplo a seguir mostra os serviços que devem estar em execução agora:

- `istio-*` serviços
- `jaeger-*`, `tracing`, e `zipkin` serviços de rastreamento de complemento
- `prometheus` serviço de métricas de complemento
- `grafana` serviço do painel de monitoramento e análise de complemento
- `kiali` serviço de painel de malha de serviço de complemento

Se `istio-ingressgateway` mostrar um ip externo de `<pending>`, aguarde alguns minutos até que um endereço IP seja atribuído pela rede do Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
```

Em seguida, confirme se os pods necessários foram criados. Use o [kubectl get pods][kubectl-get] de comando e consultar novamente o `istio-system` namespace:

```console
kubectl get pods --namespace istio-system
```

A saída do exemplo a seguir mostra os pods em execução:

- o `istio-*` pods
- o `prometheus-*` pod de métricas de complemento
- o `grafana-*` pod de painel de monitoramento e análise de complemento
- o `kiali` pod de painel de malha de serviço de complemento

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

Deve haver duas `istio-init-crd-*` pods com um `Completed` status. Esses compartimentos foram responsáveis por executar os trabalhos que criou os CRDs em uma etapa anterior. Todos os outros pods devem mostrar um status de `Running`. Se os pods não tiverem esses status, aguarde um minuto ou dois até que tenham. Se qualquer pods relatam um problema, use o [kubectl descrevem pod][kubectl-describe] comando para examinar sua saída e o status.

## <a name="accessing-the-add-ons"></a>Acessar os complementos

Um número de complementos instalaram o Istio na configuração acima, que fornecem funcionalidade adicional. As interfaces de usuário para os complementos não são expostas publicamente por meio de um endereço IP externo. Para acessar as interfaces de usuário de complemento, use o [encaminhamento de porta kubectl][kubectl-port-forward] comando. Este comando cria uma conexão segura entre o computador cliente e o pod relevante em seu cluster do AKS.

Adicionamos uma camada adicional de segurança para o Grafana e Kiali especificando credenciais para que eles neste artigo.

### <a name="grafana"></a>Grafana

As análises e painéis de monitoramento para Istio são fornecidas pelos [Grafana][grafana]. Encaminhe a porta local `3000` no computador cliente à porta `3000` no pod que está executando o Grafana no cluster do AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

A seguinte saída de exemplo mostra o encaminhamento de porta sendo configurado para Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Agora, é possível acessar o Grafana na seguinte URL e no computador cliente - [http://localhost:3000](http://localhost:3000). Lembre-se de usar as credenciais que você criou por meio do Grafana secreta anteriormente quando solicitado.

### <a name="prometheus"></a>Prometheus

As métricas para Istio são fornecidas pelo [Prometheus][prometheus]. Encaminhe a porta local `9090` no computador cliente à porta `9090` no pod que Prometeu está em execução no cluster do AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

A seguinte saída de exemplo mostra o encaminhamento de porta sendo configurado para Prometheus:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Agora, é possível acessar o navegador de expressão do Prometheus na seguinte URL no computador cliente - [http://localhost:9090](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

O rastreamento Istio é fornecido pela [Jaeger][jaeger]. Encaminhe a porta local `16686` no computador cliente à porta `16686` no pod que Jaeger está em execução no cluster do AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

A seguinte saída de exemplo mostra o encaminhamento de porta sendo configurado para Jaeger:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Agora, é possível acessar a interface do usuário de rastreamento Jaeger na seguinte URL no computador cliente - [http://localhost:16686](http://localhost:16686).

### <a name="kiali"></a>Kiali

Um painel de observação de malha de serviço é fornecido pelo [Kiali][kiali]. Encaminhe a porta local `20001` no computador cliente à porta `20001` no pod que Kiali está em execução no cluster do AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

A seguinte saída de exemplo mostra o encaminhamento de porta sendo configurado para Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Agora, é possível acessar o painel de observabilidade de malha do serviço do Kiali na seguinte URL no computador cliente - [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/). Lembre-se de usar as credenciais que você criou por meio de Kiali secreta anteriormente quando solicitado.

## <a name="uninstall-istio-from-aks"></a>Desinstalar o Istio do AKS

> [!WARNING]
> Excluindo Istio de um sistema em execução pode resultar em tráfego problemas entre os serviços relacionados. Certifique-se de que você fez provisões para que seu sistema ainda funcionem corretamente sem Istio antes de continuar.

### <a name="remove-istio-components-and-namespace"></a>Remover componentes do Istio e namespace

Para remover o Istio de cluster do AKS, use os comandos a seguir. O `helm delete` comandos removerá a `istio` e `istio-init` gráficos e o `kubectl delete ns` comando removerá o `istio-system` namespace.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Remover CRDs Istio

Os comandos acima excluem todos os componentes do Istio e namespace, mas ainda deixamos com o CRDs Istio. Para excluir os CRDs, você pode usar um abordagens a seguir.

Abordagem #1 - Este comando assume que você está executando esta etapa da pasta de nível superior da versão baixada e extraída do Istio que você usou para instalar o Istio com.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Abordagem #2 – Use um desses comandos, se você não tiver acesso à versão baixado e extraído do Istio que você usou para instalar o Istio com. Esse comando irá demorar um pouco mais - isso poderá levar alguns minutos para ser concluída.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

Powershell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Próximas etapas

A documentação a seguir descreve como você pode usar o Istio para fornecer roteamento inteligente para implementar uma versão canário:

> [!div class="nextstepaction"]
> [Cenário de roteamento inteligente Istio AKS][istio-scenario-routing]

Para explorar mais opções de instalação e configuração para Istio, consulte os seguintes artigos oficiais do Istio:

- [Istio - guia de instalação do Helm][istio-install-helm]
- [Istio - opções de instalação do Helm][istio-install-helm-options]

Você também pode seguir os cenários adicionais usando o [exemplo de aplicativo do Istio Bookinfo][istio-bookinfo-example].

Para saber como monitorar seu aplicativo AKS usando o Application Insights e o Istio, consulte a documentação do Azure Monitor a seguir:
- [Monitoramento de aplicativos zero instrumentação para Kubernetes aplicativos hospedados][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/about/notes/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
