---
title: Tutorial – Criar um pipeline de desenvolvimento no Azure com o Jenkins | Microsoft Docs
description: Tutorial – Neste tutorial, você aprenderá a criar uma máquina virtual Jenkins no Azure que efetua pull do GitHub em cada confirmação de código e a criar um novo contêiner do Docker para executar o aplicativo.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d8ef524121f41129d842cfdf9822fe6a19c71810
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709561"
---
# <a name="tutorial-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Tutorial: Criar uma infraestrutura de desenvolvimento em uma VM Linux no Azure com Jenkins, GitHub e Docker

Para automatizar as fases de build e teste do desenvolvimento de aplicativos, você pode usar um pipeline de CI/CD (implantação e integração contínuas). Neste tutorial, você cria um pipeline de CI/CD em uma VM do Azure, incluindo como:

> [!div class="checklist"]
> * Criar uma VM Jenkins
> * Instalar e configurar o Jenkins
> * Criar integração de webhooks entre o GitHub e Jenkins
> * Criar e disparar trabalhos de build do Jenkins por meio de confirmações do GitHub
> * Criar uma imagem de Docker para o aplicativo
> * Verificar se as confirmações do GitHub compilam a nova imagem do Docker e atualizam o aplicativo em execução

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-jenkins-instance"></a>Criar instância do Jenkins
Em um tutorial anterior sobre [Como personalizar uma máquina virtual do Linux na primeira inicialização](tutorial-automate-vm-deployment.md), você aprendeu a automatizar a personalização de VM com a inicialização de nuvem. Este tutorial usa um arquivo de inicialização de nuvem para instalar o Jenkins e o Docker em uma VM. Jenkins é um conhecido servidor de automação de código aberto que se integra perfeitamente com o Azure para habilitar a integração contínua (CI) e fornecimento contínuo (CD). Para ver mais tutoriais sobre como usar o Jenkins, consulte [Jenkins no hub do Azure](https://docs.microsoft.com/azure/jenkins/).

No shell atual, crie um arquivo chamado *cloud-init-jenkins.txt* e cole a configuração a seguir. Por exemplo, crie o arquivo no Cloud Shell e não em seu computador local. Insira `sensible-editor cloud-init-jenkins.txt` para criar o arquivo e ver uma lista de editores disponíveis. Certifique-se de que o arquivo de inicialização de nuvem inteiro seja copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - apt install openjdk-8-jre-headless -y
  - wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
  - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

Antes de criar uma máquina virtual, crie um grupo de recursos com o [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroupJenkins* na localização *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Agora, crie uma VM com [az vm create](/cli/azure/vm). Utiçize o `--custom-data` parâmetro para passar no arquivo de configuração de inicialização de nuvem. Forneça o caminho completo para a configuração *cloud-init-jenkins.txt* se você salvou o arquivo fora do seu diretório de trabalho atual.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

Demora alguns minutos para que a VM seja criada e configurada.

Para permitir que o tráfego da Web alcance a VM, use [az vm open-port](/cli/azure/vm) para abrir a porta *8080* para tráfego do Jenkins e a porta *1337* para o aplicativo do Node.js que é usado para executar um aplicativo de exemplo:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Configurar o Jenkins
Para acessar a instância do Jenkins, obtenha o endereço IP público da VM:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Para fins de segurança, você precisa inserir a senha de administrador inicial que é armazenada em um arquivo de texto na sua VM para iniciar a instalação do Jenkins. Use o endereço IP público obtido na etapa anterior para conectar-se à sua VM por SSH:

```bash
ssh azureuser@<publicIps>
```

Verifique se o Jenkins está executando usando o comando `service`:

```bash
$ service jenkins status
● jenkins.service - LSB: Start Jenkins at boot time
   Loaded: loaded (/etc/init.d/jenkins; generated)
   Active: active (exited) since Tue 2019-02-12 16:16:11 UTC; 55s ago
     Docs: man:systemd-sysv-generator(8)
    Tasks: 0 (limit: 4103)
   CGroup: /system.slice/jenkins.service

Feb 12 16:16:10 myVM systemd[1]: Starting LSB: Start Jenkins at boot time...
...
```

Exiba o `initialAdminPassword` para sua instalação do Jenkins e copie-o:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Se o arquivo ainda não está disponível, aguarde alguns minutos para a nuvem-init concluir a instalação Jenkins e o Docker.

Agora, abra um navegador da Web e vá para `http://<publicIps>:8080`. Conclua a configuração inicial do Jenkins da seguinte maneira:

- Escolha **Selecionar plug-ins para instalar**.
- Procure *GitHub* na caixa de texto na parte superior. Marque a caixa *GitHub* e selecione **Instalar**
- Crie o primeiro usuário administrador. Insira um nome de usuário, por exemplo, **admin**, depois forneça sua própria senha de segurança. Por fim, digite um nome completo e o endereço de email.
- Selecione **Salvar e Concluir**
- Assim que o Jenkins estiver pronto, selecione **Começar a usar o Jenkins**
  - Se seu navegador da Web exibir uma página em branco quando você começar a usar o Jenkins, reinicie o serviço do Jenkins. Em sua sessão do SSH, digite `sudo service jenkins restart`, depois atualize seu navegador da Web.
- Se necessário, faça logon no Jenkins com o nome de usuário e a senha que você criou.


## <a name="create-github-webhook"></a>Criar um webhook do GitHub
Para configurar a integração com o GitHub, abra o [aplicativo de exemplo Olá, Mundo do Node.js](https://github.com/Azure-Samples/nodejs-docs-hello-world) do repositório de exemplos do Azure. Para bifurcar o repositório para sua conta do GitHub, selecione o botão **Bifurcação** no canto superior direito.

Crie um webhook dentro da bifurcação criada:

- Selecione **Configurações** e, em seguida, **Webhooks** no lado esquerdo.
- Escolha **Adicionar webhook** e, em seguida, digite *Jenkins* na caixa de filtro.
- Para a **URL de Conteúdo**, insira `http://<publicIps>:8080/github-webhook/`. Certifique-se de incluir a barra à direita (/)
- Para **Tipo de Conteúdo**, selecione *application/x-www-form-urlencoded*.
- Para **Quais eventos você deseja que disparem esse webhook?** , selecione *Apenas o evento de envio por push.*
- Defina **Ativo** como marcado.
- Clique em **Adicionar webhook**.

![Adicione um webhook do GitHub ao seu repositório bifurcado](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>Criar trabalho do Jenkins
Para o Jenkins responder a um evento no GitHub, tal como confirmação de código, crie um trabalho do Jenkins. Use as URLs para sua própria bifurcação do GitHub.

No seu site do Jenkins, selecione **Criar novos trabalhos** na página inicial:

- Insira *HelloWorld* como nome do trabalho. Escolha **Projeto Freestyle** e selecione **OK**.
- Na seção **Geral**, selecione o **projeto do GitHub** e insira a URL do repositório bifurcado, como *https://github.com/cynthn/nodejs-docs-hello-world*
- Na seção **Gerenciamento de código-fonte**, selecione **Git** e insira a URL *.git* do repositório bifurcado, por exemplo, *https://github.com/cynthn/nodejs-docs-hello-world.git*
- Na seção **Gatilhos de Build**, selecione **Gatilho de gancho do GitHub para sondagem de GITscm**.
- Na seção **Compilar**, clique em **Adicionar etapa de compilação**. Selecione **Executar shell** e, em seguida, digite `echo "Test"` na janela de comando.
- Selecione **Salvar** na parte inferior da janela de trabalhos.


## <a name="test-github-integration"></a>Testar a integração do GitHub
Para testar a integração do GitHub com o Jenkins, confirme uma alteração em seu bifurcação. 

De volta à interface do usuário da Web do GitHub, selecione o repositório bifurcado e, em seguida, o arquivo **index.js**. Selecione o ícone de lápis para editar esse arquivo, de modo que a linha 6 fique assim:

```javascript
response.end("Hello World!");
```

Para confirmar suas alterações, selecione o botão **Confirmar alterações** na parte inferior.

No Jenkins, um novo build começa na seção **Histórico de build** do canto inferior esquerdo da sua página de trabalho. Escolha o link com o número de build e selecione **Saída do console** no lado esquerdo. Você pode exibir as etapas que o Jenkins realiza conforme seu código é extraído por pull do GitHub e a ação de build gera a mensagem `Test` no console. Cada vez que uma confirmação é feita no GitHub, o webhook alcança o Jenkins e dispara um novo build dessa maneira.


## <a name="define-docker-build-image"></a>Definir a imagem de build do Docker
Para ver o aplicativo do Node.js em execução com base em suas confirmações GitHub, compilaremos uma imagem do Docker para executar o aplicativo. A imagem é compilada de um Dockerfile que define como configurar o contêiner que executa o aplicativo. 

Da conexão do SSH para a VM, altere para o diretório de workspace do Jenkins nomeado como o trabalho que você criou em uma etapa anterior. Nesse exemplo, isso foi nomeado como *HelloWorld*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Crie um arquivo nesse diretório de workspace com `sudo sensible-editor Dockerfile` nese cole o conteúdo a seguir. Certifique-se de que o Dockerfile inteiro foi copiado corretamente, especialmente a primeira linha:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Este Dockerfile usa a imagem base Node.js usando o Linux Alpine, expõe a porta 1337 em que o aplicativo Olá, Mundo é executado e, em seguida, copia os arquivos do aplicativo e o inicializa.


## <a name="create-jenkins-build-rules"></a>Criar regras de build do Jenkins
Na etapa anterior, você criou uma regra de build básica do Jenkins que gera uma mensagem para o console. Permite criar a etapa de build para usar nosso Dockerfile e executar o aplicativo.

Em sua instância do Jenkins, selecione o trabalho que você criou em uma etapa anterior. Selecione **Configurar** no lado esquerdo e role para baixo até a seção **Build**:

- Remova sua etapa de build `echo "Test"` existente. Selecione a cruz vermelha no canto superior direito da caixa da etapa de build existente.
- Escolha **Adicionar etapa de build** e, em seguida, selecione **Executar shell**
- Na caixa **Comando**, insira os comandos do Docker a seguir e então selecione **Salvar**:

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

As etapas de build do Docker criam uma imagem e marcam-na com o número de build do Jenkins para que você possa manter um histórico das imagens. Qualquer contêiner existente executando o aplicativo é interrompido e, em seguida, removido. Um novo contêiner é então iniciado usando a imagem e executa o aplicativo do Node.js com base nas confirmações mais recentes no GitHub.


## <a name="test-your-pipeline"></a>Testar o pipeline
Para ver o pipeline inteiro em ação, edite novamente o arquivo *index.js* no seu repositório bifurcado do GitHub e selecione **Confirmar alteração**. Um novo trabalho é iniciado no Jenkins com base no webhook para GitHub. Leva alguns segundos para criar a imagem de Docker e iniciar seu aplicativo em um novo contêiner.

Se necessário, obtenha o endereço IP público de sua VM novamente:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Abra um navegador da Web e insira `http://<publicIps>:1337`. Seu aplicativo Node.js é exibido e reflete as confirmações mais recentes em sua bifurcação do GitHub, da seguinte maneira:

![Executar o aplicativo Node.js](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

Agora, faça outra edição para o arquivo *index.js* no GitHub e confirme a alteração. Aguarde alguns segundos para o trabalho ser concluído no Jenkins e atualize seu navegador da Web para ver a versão atualizada de seu aplicativo em execução em um novo contêiner, da seguinte maneira:

![Executar o aplicativo Node.js após outra confirmação do GitHub](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou o GitHub para executar um trabalho de build do Jenkins em cada confirmação de código e, em seguida, implantar um contêiner do Docker para testar seu aplicativo. Você aprendeu como:

> [!div class="checklist"]
> * Criar uma VM Jenkins
> * Instalar e configurar o Jenkins
> * Criar integração de webhooks entre o GitHub e Jenkins
> * Criar e disparar trabalhos de build do Jenkins por meio de confirmações do GitHub
> * Criar uma imagem de Docker para o aplicativo
> * Verificar se as confirmações do GitHub compilam a nova imagem do Docker e atualizam o aplicativo em execução

Avance para o próximo tutorial para saber mais sobre como integrar o Jenkins ao Azure DevOps Services.

> [!div class="nextstepaction"]
> [Implantar aplicativos com o Jenkins e o Azure DevOps Services](tutorial-build-deploy-jenkins.md)
