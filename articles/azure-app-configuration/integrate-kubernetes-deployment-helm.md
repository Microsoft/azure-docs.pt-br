---
title: Integrar a Configuração de Aplicativos do Azure na Implantação do Kubernetes usando Helm
description: Saiba como usar configurações dinâmicas na implantação do Kubernetes com o Helm.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 6276fc2027e92d5b7baaf9237a928e7828a3b021
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775755"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integrar com a Implantação do Kubernetes usando o Helm

O Helm fornece uma forma de definir, instalar e atualizar aplicativos em execução no Kubernetes. Um gráfico do Helm contém as informações necessárias para criar uma instância de um aplicativo Kubernetes. A configuração é armazenada fora do próprio gráfico, em um arquivo chamado *values.yaml*. 

Durante o processo de lançamento, o Helm mescla o gráfico com a configuração adequada para executar o aplicativo. Por exemplo, as variáveis definidas em *valores.yaml* podem ser referenciadas como variáveis de ambiente dentro dos contêineres em execução. O Helm também dá suporte à criação de Segredos do Kubernetes, que podem ser montados como volumes de dados ou expostos como variáveis de ambiente.

Você pode substituir os valores armazenados em *values.yaml* fornecendo arquivos de configuração baseados em YAML adicionais na linha de comando ao executar o Helm. A Configuração de Aplicativos do Azure dá suporte à exportação de valores de configuração para arquivos YAML. A integração dessa funcionalidade de exportação à sua implantação permite que seus aplicativos Kubernetes aproveitem os valores de configuração armazenados na Configuração de Aplicativos.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Use valores da Configuração de Aplicativos ao implantar um aplicativo no Kubernetes usando o Helm.
> * Criar um Segredo do Kubernetes com base em uma referência do Key Vault na Configuração de Aplicativos.

Este tutorial pressupõe uma compreensão básica do gerenciamento de Kubernetes com o Helm. Saiba mais sobre como instalar aplicativos com o Helm no [Serviço de Kubernetes do Azure](../aks/kubernetes-helm.md).

## <a name="prerequisites"></a>Pré-requisitos

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- Instalar a [CLI do Azure](/cli/azure/install-azure-cli) (versão 2.4.0 ou posterior)
- Instalar o [Helm](https://helm.sh/docs/intro/install/) (versão 2.14.0 ou posterior)
- Um cluster do Kubernetes.

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Gerenciador de Configurações** > **Criar** para adicionar os seguintes pares chave-valor:

    | Chave | Valor |
    |---|---|
    | settings.color | Branco |
    | settings.message | Dados da Configuração de Aplicativo do Azure |

    Deixe **Rótulo** e **Tipo de Conteúdo** vazios por enquanto.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Adicionar uma referência do Key Vault à Configuração de Aplicativos
1. Entre no [portal do Azure](https://portal.azure.com) e adicione um segredo ao [Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) com o nome **Password** e o valor **myPassword**. 
2. Selecione a instância do repositório da Configurações de Aplicativos que você criou na seção anterior.

3. Selecione **Gerenciador de Configurações**.

4. Clique em **+ Criar** > **Referência do Key Vault** e especifique os seguintes valores:
    - **Chave**: Selecione **secrets.password**.
    - **Rótulo**: deixe esse valor em branco.
    - **Assinatura**, **Grupo de recursos** e **Cofre de chaves**: insira os valores correspondentes àqueles no cofre de chaves criado na etapa anterior.
    - **Segredo**: selecione o segredo chamado **Password** criado na seção anterior.

## <a name="create-helm-chart"></a>Criar gráfico do Helm ##
Primeiro, crie um gráfico do Helm de exemplo com o comando a seguir
```console
helm create mychart
```

O Helm cria um diretório chamado mychart com a estrutura mostrada abaixo. 

> [!TIP]
> Siga este [guia de gráficos](https://helm.sh/docs/chart_template_guide/getting_started/) para saber mais.

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

Em seguida, atualize a seção **spec:template:spec:containers** do arquivo *deployment.yaml*. O snippet a seguir adiciona duas variáveis de ambiente ao contêiner. Você definirá seus valores dinamicamente no momento da implantação.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

O arquivo *deployment.yaml* completo após a atualização deve ter a aparência mostrada abaixo.

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

Para armazenar dados confidenciais como Segredos do Kubernetes, adicione um arquivo *secrets.yaml* à pasta de modelos.

> [!TIP]
> Saiba mais sobre como usar [Segredos do Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

Por fim, atualize o arquivo *values.yaml* com o conteúdo a seguir para, opcionalmente, fornecer valores padrão dos parâmetros de configuração e segredos referenciados nos arquivos *deployment.yaml* e *secrets.yaml*. Seus valores reais serão substituídos pela configuração extraída da Configuração de Aplicativos.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Passar a configuração da Configuração de Aplicativos na instalação do Helm ##
Primeiro, baixe a configuração da Configuração de Aplicativos para um arquivo *myConfig.yaml*. Use um filtro de chave para baixar apenas as chaves que começam com **settings.** . Se, no seu caso, o filtro de chave não for suficiente para excluir chaves das referências do Key Vault, use o argumento **--skip-keyvault** para excluí-las. 

> [!TIP]
> Saiba mais sobre o [comando export](/cli/azure/appconfig/kv#az_appconfig_kv_export). 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Em seguida, baixe os segredos para um arquivo chamado *mySecrets.yaml*. O argumento de linha de comando **--resolve-keyvault** resolve as referências do Key Vault recuperando os valores reais no Key Vault. Você precisará executar esse comando com credenciais que têm permissões de acesso ao Key Vault correspondente.

> [!WARNING]
> Como ele contém informações confidenciais, mantenha o arquivo com cuidado e limpe-o quando ele não for mais necessário.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Use o argumento **-f** de atualização do Helm para passar os dois arquivos de configuração que foram criados. Eles substituirão os valores de configuração definidos em *values.yaml* pelos valores exportados da Configuração de Aplicativos.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

Você também pode usar o argumento **--set** de atualização do Helm para passar valores de chave literais. Usar o argumento **--set** é uma boa maneira de evitar a persistência de dados confidenciais no disco. 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

Verifique se as configurações e os segredos foram definidos com êxito acessando o [Painel do Kubernetes](../aks/kubernetes-dashboard.md). Você verá que os valores de **cor** e **mensagem** da Configuração de Aplicativos foram populados nas variáveis de ambiente do contêiner.

![Inicialização local do aplicativo do Início Rápido](./media/kubernetes-dashboard-env-variables.png)

Um segredo, **password**, armazenado como uma referência do Key Vault na Configuração de Aplicativos, também foi adicionado aos Segredos do Kubernetes. 

![Captura de tela que realça a senha na seção Dados.](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você exportou os dados da Configuração de Aplicativos do Azure para uso em uma implantação do Kubernetes com o Helm. Para saber mais sobre como usar a Configuração de Aplicativo, continue para ver as amostras da CLI do Azure.

> [!div class="nextstepaction"]
> [CLI do Azure](/cli/azure/appconfig)
