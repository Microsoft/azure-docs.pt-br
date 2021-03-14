---
title: Implantar um aplicativo Java com o Liberty/WebSphere Liberty aberto em um cluster do Azure Red Hat OpenShift 4
description: Implante um aplicativo Java com o Liberty/WebSphere Liberty aberto em um cluster do Azure Red Hat OpenShift 4.
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: Java, Jacarta, Java, microperfil, Open-Liberty, WebSphere-Liberty, aro, openshift, Red Hat
ms.openlocfilehash: 08fd3ab112498a983b438d5ba1f1f100816cbf5d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212987"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Implantar um aplicativo Java com o Liberty/WebSphere Liberty aberto em um cluster do Azure Red Hat OpenShift 4

Este guia demonstra como executar seu aplicativo Java, Java EE, [Jacarta EE](https://jakarta.ee/)ou [microprofile](https://microprofile.io/) no tempo de execução do Liberty/WebSphere Liberty aberto e, em seguida, implantar o aplicativo em contêineres em um cluster do Azure Red Hat OpenShift (toa) 4 usando o operador Open Liberty. Este artigo o orientará na preparação de um aplicativo do Liberty, na criação da imagem do Docker do aplicativo e na execução do aplicativo em contêineres em um cluster toa 4.  Para obter mais detalhes sobre o Liberty aberto, consulte [a página Abrir projeto do Liberty](https://openliberty.io/). Para obter mais detalhes sobre o IBM WebSphere Liberty, consulte [a página do produto WebSphere Liberty](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua os seguintes pré-requisitos para percorrer com êxito este guia.

> [!NOTE]
> O Red Hat OpenShift no Azure requer um mínimo de 40 núcleos para criar e executar um cluster do OpenShift. A cota de recursos padrão do Azure para uma nova assinatura do Azure não atende a esse requisito. Para solicitar um aumento no limite de recursos, confira [Cota Standard: Aumentar limites por série de VM](../azure-portal/supportability/per-vm-quota-requests.md). Observe que a assinatura de avaliação gratuita não está qualificada para um aumento de cota, [atualize para uma assinatura paga conforme o uso](../cost-management-billing/manage/upgrade-azure-subscription.md) antes de solicitar um aumento de cota.

1. Prepare um computador local com o sistema operacional semelhante ao Unix instalado (por exemplo, Ubuntu, macOS).
1. Instale uma implementação Java SE (por exemplo, [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
1. Instale o [Maven](https://maven.apache.org/download.cgi) 3.5.0 ou superior.
1. Instale o [Docker](https://docs.docker.com/get-docker/) para o seu sistema operacional.
1. Instale o [CLI do Azure](/cli/azure/install-azure-cli) 2.0.75 ou posterior.
1. Verifique e instale [`envsubst`](https://command-not-found.com/envsubst) se ele não é pré-instalado em seu sistema operacional.
1. Clone o código deste exemplo em seu sistema local. O exemplo está no [GitHub](https://github.com/Azure-Samples/open-liberty-on-aro).
1. Siga as instruções em [criar um cluster do Azure Red Hat OpenShift 4](./tutorial-create-cluster.md).

   Embora a etapa "obter um segredo de pull do Red Hat" seja rotulada como opcional, **ela é necessária para este artigo**.  O segredo de pull permite que o cluster do Azure Red Hat OpenShift encontre o operador Open Liberty.

   Se você planeja executar aplicativos com uso intensivo de memória no cluster, especifique o tamanho adequado da máquina virtual para os nós de trabalho usando o `--worker-vm-size` parâmetro. Por exemplo, `Standard_E4s_v3` é o tamanho mínimo da máquina virtual para instalar o operador Elasticsearch em um cluster. Para obter mais informações, consulte:

   * [CLI do Azure criar um cluster](/cli/azure/aro#az-aro-create)
   * [Tamanhos de máquina virtual com suporte para memória otimizada](./support-policies-v4.md#memory-optimized)
   * [Pré-requisitos para instalar o operador Elasticsearch](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Conecte-se ao cluster seguindo as etapas em [conectar-se a um cluster do Azure Red Hat OpenShift 4](./tutorial-connect-cluster.md).
   * Certifique-se de seguir as etapas em "instalar a CLI do OpenShift", pois usaremos o `oc` comando mais adiante neste artigo.
   * Anote a URL do console de cluster que se parece com `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .
   * Anote as `kubeadmin` credenciais.

1. Verifique se você pode entrar na CLI do OpenShift com o token para o usuário `kubeadmin` .

### <a name="enable-the-built-in-container-registry-for-openshift"></a>Habilitar o registro de contêiner interno para OpenShift

As etapas neste tutorial criam uma imagem do Docker que deve ser enviada por push a um registro de contêiner acessível para OpenShift. A opção mais simples é usar o registro interno fornecido pelo OpenShift. Para habilitar o registro de contêiner interno, siga as etapas em [Configurar registro de contêiner interno para o Azure Red Hat OpenShift 4](built-in-container-registry.md). Três itens dessas etapas são usados neste artigo.

* O nome de usuário e a senha da usuária do Azure AD para entrar no console Web do OpenShift.
* A saída de `oc whoami` depois de seguir as etapas para entrar na CLI do OpenShift.  Esse valor é chamado **AAD-User** para discussão.
* A URL do registro de contêiner.

Observe esses itens à medida que você concluir as etapas para habilitar o registro de contêiner interno.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Criar um namespace OpenShift para o aplicativo Java

1. Entre no console Web do OpenShift no seu navegador usando as `kubeadmin` credenciais.
2. Navegue até **Administração**  >  **namespaces**  >  **criar namespace**.
3. Preencha `open-liberty-demo` para **nome** e selecione **criar**, conforme mostrado a seguir.

   ![criar namespace](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Criar um administrador para o projeto de demonstração

Além do gerenciamento de imagens, o **usuário do AAD** também receberá permissões administrativas para gerenciar recursos no projeto de demonstração do cluster toa 4.  Entre na CLI do OpenShift e conceda ao **usuário do AAD** os privilégios necessários seguindo estas etapas.

1. Entre no console Web do OpenShift no seu navegador usando as `kubeadmin` credenciais.
1. Na parte superior direita do console Web, expanda o menu de contexto do usuário conectado e selecione o **comando copiar logon**.
1. Entre em uma nova janela de guia com o mesmo usuário, se necessário.
1. Selecione o **token de exibição**.
1. Copie o valor listado abaixo de **fazer logon com esse token** na área de transferência e execute-o em um shell, como mostrado aqui.
1. Execute os comandos a seguir para conceder a `admin` função ao **usuário do AAD** no namespace `open-liberty-demo` .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Instalar o operador Open Liberty OpenShift

Depois de criar e conectar-se ao cluster, instale o operador Open Liberty.  A página inicial principal para o operador Open Liberty está no [GitHub](https://github.com/OpenLiberty/open-liberty-operator).

1. Entre no console Web do OpenShift no seu navegador usando as `kubeadmin` credenciais.
2. Navegue até **operadores**  >  **OperatorHub** e pesquise o **operador Open Liberty**.
3. Selecione **abrir operador de Liberty** nos resultados da pesquisa.
4. Selecione **Instalar**.
5. Na assinatura do **operador criar** pop-up, verifique **todos os namespaces no cluster (padrão)** para o **modo de instalação**, **beta** para o **canal de atualização** e a **estratégia de aprovação** **automática** :

   ![criar assinatura do operador para o operador Open Liberty](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Selecione **assinar** e aguarde um minuto ou dois até que o operador abrir o Liberty seja exibido.
7. Observe o operador Open Liberty com o status "Succeeded".  Se você não fizer isso, diagnostique e resolva o problema antes de continuar.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Os operadores instalados que mostram o Open Liberty são instalados.":::

## <a name="prepare-the-liberty-application"></a>Preparar o aplicativo Liberty

Usaremos um aplicativo Java EE 8 como nosso exemplo neste guia. Abra o Liberty é um servidor totalmente compatível com o [Java EE 8](https://javaee.github.io/javaee-spec/javadocs/) e, portanto, ele pode executar facilmente o aplicativo.  Abra o Liberty também é [compatível com o perfil completo com o Jacarta EE 8](https://jakarta.ee/specifications/platform/8/apidocs/).

### <a name="run-the-application-on-open-liberty"></a>Executar o aplicativo no Liberty aberto

Para executar o aplicativo no Liberty aberto, você precisa criar um arquivo de configuração do servidor do Liberty aberto para que o [plug-in Liberty Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) possa empacotar o aplicativo para implantação. O plug-in do Liberty Maven não é necessário para implantar o aplicativo no OpenShift.  No entanto, vamos usá-lo neste exemplo com o modo Developer (dev) do Open Liberty.  O modo de desenvolvedor permite executar facilmente o aplicativo localmente. Conclua as etapas a seguir no computador local.

1. Copie `2-simple/src/main/liberty/config/server.xml` para `1-start/src/main/liberty/config` , substituindo o arquivo de comprimento zero existente. Isso `server.xml` configura o servidor do Liberty aberto com os recursos do Java EE.
1. Copiar `2-simple/pom.xml` para `1-start/pom.xml` .  Esta etapa adiciona o `liberty-maven-plugin` ao pom.
1. Altere o diretório para `1-start` de seu clone local.
1. Execute `mvn clean package` em um console para gerar um pacote War `javaee-cafe.war` no diretório `./target` .
1. Execute `mvn liberty:dev` para começar a abrir o Liberty no modo dev.
1. Aguarde até que o servidor seja iniciado. A saída do console deve terminar com a seguinte mensagem:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. Abra `http://localhost:9080/` o no navegador para visitar o home page do aplicativo. O aplicativo será semelhante à imagem a seguir:

   ![Interface do usuário da Web de cafe do Java](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Pressione **Control-C** para interromper o aplicativo e abrir o servidor do Liberty.

O diretório `2-simple` do seu clone local mostra o projeto Maven com as alterações acima já aplicadas.

## <a name="prepare-the-application-image"></a>Preparar a imagem do aplicativo

Para implantar e executar seu aplicativo Liberty em um cluster toa 4, use o contêiner para colocar o aplicativo como uma imagem do Docker usando [imagens de contêiner do Liberty](https://github.com/OpenLiberty/ci.docker) ou imagens de contêiner do [WebSphere Liberty](https://github.com/WASdev/ci.docker).

### <a name="build-application-image"></a>Criar imagem de aplicativo

Conclua as seguintes etapas para criar a imagem do aplicativo:

1. Altere o diretório para `2-simple` de seu clone local.
2. Execute `mvn clean package` para empacotar o aplicativo.
3. Execute um dos comandos a seguir para criar a imagem do aplicativo.
   * Crie com a imagem base do Liberty aberta:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Crie com a imagem base do WebSphere Liberty:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Executar o aplicativo localmente com o Docker

Antes de implantar o aplicativo em contêiner em um cluster remoto, execute com seu Docker local para verificar se ele funciona:

1. Execute `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` no console do.
2. Aguarde até que o servidor do Liberty seja iniciado e o aplicativo seja implantado com êxito.
3. Abra `http://localhost:9080/` o no navegador para visitar o home page do aplicativo.
4. Pressione **Control-C** para interromper o aplicativo e o servidor do Liberty.

### <a name="push-the-image-to-the-container-image-registry"></a>Enviar a imagem por push para o registro de imagem de contêiner

Quando estiver satisfeito com o estado do aplicativo, envie-o por push para o registro de imagem de contêiner interno seguindo as instruções abaixo.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Faça logon na CLI do OpenShift como o usuário do Azure AD

1. Entre no console Web do OpenShift do seu navegador usando as credenciais de um usuário do Azure AD.

   1. Use um recurso de janela de navegador InPrivate, Incognito ou outros equivalentes para entrar no console.
   1. Selecionar **OpenID**

   > [!NOTE]
   > Anote o nome de usuário e a senha que você usa para entrar aqui. Esse nome de usuário e senha funcionarão como administrador para outras ações neste e em outros artigos.
1. Entre com a CLI do OpenShift usando as etapas a seguir.  Para discussão, esse processo é conhecido como `oc login` .
   1. Na parte superior direita do console Web, expanda o menu de contexto do usuário conectado e selecione o **comando copiar logon**.
   1. Entre em uma nova janela de guia com o mesmo usuário, se necessário.
   1. Selecione o **token de exibição**.
   1. Copie o valor listado abaixo de **fazer logon com esse token** na área de transferência e execute-o em um shell, como mostrado aqui.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>Enviar por push a imagem de contêiner para o registro de contêiner para OpenShift

Execute estes comandos para enviar a imagem por push para o registro de contêiner para OpenShift.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

A saída bem-sucedida terá uma aparência semelhante à seguinte.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

Envie a imagem por push para o registro de imagem de contêiner interno com o comando a seguir.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Implantar aplicativo no cluster toa 4

Agora você pode implantar o aplicativo de exemplo Liberty no cluster do Azure Red Hat OpenShift 4 criado anteriormente ao trabalhar com os pré-requisitos.

### <a name="deploy-the-application-from-the-web-console"></a>Implantar o aplicativo do console Web

Como usamos o operador Open Liberty para gerenciar aplicativos do Liberty, precisamos criar uma instância de sua definição de *recurso Personalizada*, do tipo "OpenLibertyApplication". O operador cuidará de todos os aspectos do gerenciamento dos recursos de OpenShift necessários para a implantação.

1. Entre no console Web do OpenShift do seu navegador usando as credenciais do usuário do Azure AD.
1. Expanda **início**, selecione **projetos**  >  **Open-Liberty-demo**.
1. Navegue até **operadores**  >  **instalados operadores**.
1. No meio da página, selecione **abrir operador de Liberty**.
1. No meio da página, selecione **abrir aplicativo do Liberty**.  A navegação de itens na interface do usuário espelha a hierarquia de confinamento real de tecnologias em uso.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![Confinamento de Java de toa](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Selecione **criar OpenLibertyApplication**
1. Substitua o YAML gerado pelo seu, que está localizado em `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. Selecione **Criar**. Você será retornado à lista de OpenLibertyApplications.
1. Selecione **Java-café-simples**.
1. No meio da página, selecione **recursos**.
1. Na tabela, selecione o link para **Java-café-simples** com o **tipo** de **rota**.
1. Na página que é aberta, selecione o link abaixo do **local**.

Você verá o aplicativo home page aberto no navegador.

### <a name="delete-the-application-from-the-web-console"></a>Excluir o aplicativo do console Web

Quando você terminar o aplicativo, siga estas etapas para excluir o aplicativo do Open Shift.

1. No painel de navegação à esquerda, expanda a entrada para **operadores**.
1. Selecione **operadores instalados**.
1. Selecione **abrir operador de Liberty**.
1. No meio da página, selecione **abrir aplicativo do Liberty**.
1. Selecione as reticências verticais (três pontos verticais) e, em seguida, selecione **excluir aplicativo OpenLiberty**.

### <a name="deploy-the-application-from-cli"></a>Implantar o aplicativo da CLI

Em vez de usar a GUI do console Web, você pode implantar o aplicativo da CLI. Se você ainda não tiver feito isso, baixe e instale a `oc` ferramenta de linha de comando seguindo a documentação [do Red Hat introdução com a CLI](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html).

1. Entre no console Web do OpenShift do seu navegador usando as credenciais do usuário do Azure AD.
2. Entre na CLI do OpenShift com o token para o usuário do Azure AD.
3. Altere o diretório para `2-simple` de seu clone local e execute os comandos a seguir para implantar seu aplicativo Liberty no cluster toa 4.  A saída do comando também é mostrada em linha.

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. Verifique para ver `1/1` na `READY` coluna antes de continuar.  Caso contrário, investigue e resolva o problema antes de continuar.
5. Descubra o host de rota para o aplicativo com o `oc get route` comando, como mostrado aqui.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Depois que o aplicativo Liberty estiver em execução, abra a saída do **host de rota** em seu navegador para visitar o aplicativo Home Page.

### <a name="delete-the-application-from-cli"></a>Excluir o aplicativo da CLI

Exclua o aplicativo da CLI executando este comando.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua o cluster toa seguindo as etapas em [tutorial: excluir um cluster do Azure Red Hat OpenShift 4](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a:
> [!div class="checklist"]
>
> * Preparar o aplicativo Liberty
> * Criar a imagem do aplicativo
> * Executar o aplicativo em contêineres em um cluster toa 4 usando a GUI e a CLI

Você pode aprender mais sobre as referências usadas neste guia:

* [Abra o Liberty](https://openliberty.io/)
* [Red Hat OpenShift no Azure](https://azure.microsoft.com/services/openshift/)
* [Abrir operador de Liberty](https://github.com/OpenLiberty/open-liberty-operator)
* [Abrir configuração do servidor do Liberty](https://openliberty.io/docs/ref/config/)
* [Plug-in do Liberty Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Abrir imagens de contêiner do Liberty](https://github.com/OpenLiberty/ci.docker)
* [Imagens de contêiner do WebSphere Liberty](https://github.com/WASdev/ci.docker)