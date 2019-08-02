---
title: Tutorial – Personalizar uma VM Linux com a inicialização de nuvem no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá como usar a inicialização de nuvem e o Key Vault para personalizar as VMs do Linux na primeira vez em que são inicializadas no Azure
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
ms.date: 05/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d56aa1c9b86542df3c2a7154669856686823e1dd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708597"
---
# <a name="tutorial---how-to-use-cloud-init-to-customize-a-linux-virtual-machine-in-azure-on-first-boot"></a>Tutorial – Como usar a inicialização de nuvem para personalizar uma máquina virtual do Linux no Azure na primeira inicialização

Em um tutorial anterior, você aprendeu como SSH em uma máquina virtual (VM) e instalar manualmente o NGINX. Para criar VMs de maneira rápida e consistente, alguma forma de automação, em geral, é desejada. Uma abordagem comum para personalizar uma VM na primeira inicialização é utilizar [inicialização de nuvem](https://cloudinit.readthedocs.io). Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um arquivo de configuração cloud-init
> * Criar uma VM que usa um arquivo cloud-init
> * Exibir um aplicativo Node.js em execução após a criação da VM
> * Usar o Key Vault para armazenar certificados com segurança
> * Automatizar implantações seguras de NGINX com cloud-init

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="cloud-init-overview"></a>Visão geral da inicialização de nuvem
[Inicialização de nuvem](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM do Linux, quando ela é inicializada pela primeira vez. Você pode utilizar a inicialização de nuvem para instalar pacotes e gravar arquivos, ou para configurar usuários e segurança. Como a inicialização de nuvem é executada durante o processo de inicialização inicial, não há etapa adicional ou agentes necessários para aplicar a configuração.

A inicialização de nuvem também funciona em distribuições. Por exemplo, você não usa **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, você pode definir uma lista de pacotes para instalar. Inicialização de nuvem usa automaticamente a ferramenta de gerenciamento de pacote nativo de distribuição que você selecionar.

Estamos trabalhando com parceiros para incluir a inicialização de nuvem e trabalhar nas imagens que eles fornecem para o Azure. A tabela a seguir descreve a disponibilidade de inicialização de nuvem atual nas imagens da plataforma Azure:

| Alias | Publicador | Oferta | SKU | Versão |
|:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canônico |UbuntuServer |16.04-LTS |mais recente |
| UbuntuLTS |Canônico |UbuntuServer |14.04.5-LTS |mais recente |
| CoreOS |CoreOS |CoreOS |Estável |mais recente |
| | OpenLogic | CentOS | 7-CI | mais recente |
| | RedHat | RHEL | 7-RAW-CI | mais recente |


## <a name="create-cloud-init-config-file"></a>Criar arquivo de configuração cloud-init
Para ver a inicialização de nuvem em ação, crie uma VM que instala o NGINX e execute um simples "Hello World" do aplicativo do Node. js. A seguinte configuração de inicialização de nuvem instala os pacotes necessários, cria um aplicativo do Node. js, em seguida, inicializa e inicia o aplicativo.

No shell atual, crie um arquivo chamado *cloud-init.txt* e cole a configuração a seguir. Por exemplo, crie o arquivo no Cloud Shell e não em seu computador local. Você pode usar qualquer editor que queira. Insira `sensible-editor cloud-init.txt` para criar o arquivo e ver uma lista de editores disponíveis. Certifique-se de que o arquivo de inicialização de nuvem inteiro seja copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Para obter mais informações sobre opções de configuração de inicialização de nuvem, consulte [exemplos de configuração de inicialização de nuvem](https://cloudinit.readthedocs.io/en/latest/topics/examples.html).

## <a name="create-virtual-machine"></a>Criar máquina virtual
Antes de criar uma máquina virtual, crie um grupo de recursos com o [az group create](/cli/azure/group#az-group-create). O seguinte exemplo cria um grupo de recursos chamado *myResourceGroupAutomate* no local *eastus*:

```azurecli-interactive
az group create --name myResourceGroupAutomate --location eastus
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). Utiçize o `--custom-data` parâmetro para passar no arquivo de configuração de inicialização de nuvem. Forneça o caminho completo para a configuração *cloud-init.txt* se você salvou o arquivo fora do seu diretório de trabalho atual. O exemplo a seguir cria uma VM chamada *myVM*:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Demora alguns minutos para que a VM seja criada, os pacotes para instalar e iniciar o aplicativo. Há tarefas em segundo plano que continuarão em execução depois que a CLI do Azure faz você voltar para o prompt. Pode demorar mais alguns minutos antes que você possa acessar o aplicativo. Quando a VM tiver sido criada, observe o `publicIpAddress` exibido pela CLI do Azure. Esse endereço é usado para acessar o aplicativo do Node. js por meio de um navegador da web.

Para permitir o tráfego da web para acessar sua VM, abra a porta 80 da Internet com [az vm open-port](/cli/azure/vm#az-vm-open-port):

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myVM
```

## <a name="test-web-app"></a>Testar o aplicativo da web
Agora, você pode abrir um navegador da Web e digitar *http:\/\/\<publicIpAddress>* na barra de endereços. Forneça seu próprio endereço de IP público do processo de criação da máquina virtual. Seu aplicativo Node.js é exibido como mostrado no exemplo a seguir:

![Exibir o site NGINX em execução](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Injetar certificados do Cofre de chave
Essa seção mostra como você pode armazenar certificados no Cofre de Chaves do Azure e colocá-los durante a implantação da máquina virtual com segurança. Em vez de usar uma imagem personalizada que inclui os certificados embutida, esse processo garante que os certificados mais recentes são injetados em uma máquina virtual na primeira inicialização. Durante o processo, o certificado nunca deixa a plataforma Azure ou é exposto em um script, o histórico de linha de comando ou o modelo.

O Azure Key Vault protege chaves e segredos criptográficos, como certificados ou senhas. O Cofre da Chave simplifica o processo de gerenciamento de chaves e permite que você tenha controle das chaves que acessam e criptografam seus dados. Este cenário apresenta alguns conceitos de Cofre da Chave para criar e usar um certificado, porém não é uma visão geral detalhada sobre como usar o Cofre da Chave.

As etapas a seguir mostram como você pode:

- Criar um Cofre de chaves do Azure
- Gerar ou carregar um certificado para o Cofre da Chave
- Criar um segredo do certificado para inserir em uma máquina virtual
- Criar uma máquina virtual e inserir o certificado

### <a name="create-an-azure-key-vault"></a>Criar um Cofre de chaves do Azure
Primeiro, crie um Cofre de Chaves com o [az keyvault create](/cli/azure/keyvault#az-keyvault-create) e habilitá-lo para uso quando você implanta uma máquina virtual. Cada Cofre de Chave requer um nome exclusivo e deve estar escrito com todas as letras minúsculas. Substitua *mykeyvault* no exemplo a seguir com seu próprio nome exclusivo do Key Vault:

```azurecli-interactive
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Gerar certificado e armazenar no Cofre da Chave
Para uso em produção, você deve importar um certificado válido assinado por um fornecedor confiável com o [az keyvault certificate import](/cli/azure/keyvault/certificate#az-keyvault-certificate-import). Para este tutorial, o exemplo a seguir mostra como você pode gerar um certificado autoassinado com [criar certificado de keyvault az](/cli/azure/keyvault/certificate#az-keyvault-certificate-create) que usa a política de certificado padrão:

```azurecli-interactive
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Preparar o certificado para uso com a VM
Para usar o certificado durante o processo de criação de VM, obtenha a identificação do seu certificado com as [ versões secretas de az keyvault](/cli/azure/keyvault/secret#az-keyvault-secret-list-versions). A VM precisa do certificado em um determinado formato para colocá-lo na inicialização, então, converta o certificado com [az vm secret format](/cli/azure/vm). O exemplo a seguir atribui a saída desses comandos variáveis de facilidade de uso nas próximas etapas:

```azurecli-interactive
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secret "$secret")
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Criar a configuração de inicialização de nuvem para proteger o NGINX
Quando você cria uma VM, certificados e chaves são armazenados no diretório protegido */var/lib/waagent/* . Para automatizar adicionando o certificado para a máquina virtual e configurando o NGINX, você pode usar uma configuração de inicialização de nuvem atualizada do exemplo anterior.

Crie um arquivo chamado *cloud-init-secured.txt* e cole a configuração a seguir. Novamente, se você usar o Cloud Shell, crie o arquivo de configuração de nuvem init lá e não em seu computador local. Use `sensible-editor cloud-init-secured.txt` para criar o arquivo e ver uma lista de editores disponíveis. Certifique-se de que o arquivo de inicialização de nuvem inteiro seja copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Criar VM segura
Agora, crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). Os dados do certificado são injetados no cofre da chave com o `--secrets` parâmetro. Como no exemplo anterior, você também passa a configuração de inicialização de nuvem com o `--custom-data` parâmetro:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

Demora alguns minutos para que a VM seja criada, os pacotes para instalar e iniciar o aplicativo. Há tarefas em segundo plano que continuarão em execução depois que a CLI do Azure faz você voltar para o prompt. Pode demorar mais alguns minutos antes que você possa acessar o aplicativo. Quando a VM tiver sido criada, observe o `publicIpAddress` exibido pela CLI do Azure. Esse endereço é usado para acessar o aplicativo do Node. js por meio de um navegador da web.

Para permitir o tráfego da web para acessar sua VM, abra a porta 443 da Internet com [az vm open-port](/cli/azure/vm#az-vm-open-port):

```azurecli-interactive
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --port 443
```

### <a name="test-secure-web-app"></a>Testar o aplicativo da Web protegido
Agora, você pode abrir um navegador da Web e digitar *https:\/\/\<publicIpAddress>* na barra de endereços. Forneça seu próprio endereço IP público, conforme mostrado na saída do processo de criação de VM anterior. Se você usou um certificado autoassinado, aceite o aviso de segurança:

![Aceite o aviso de segurança do navegador da web](./media/tutorial-automate-vm-deployment/browser-warning.png)

Seu site NGINX e Node. js seguro é exibido como no exemplo a seguir:

![Exibir o site NGINX em execução](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou as VMs na primeira inicialização com cloud-init. Você aprendeu como:

> [!div class="checklist"]
> * Criar um arquivo de configuração cloud-init
> * Criar uma VM que usa um arquivo cloud-init
> * Exibir um aplicativo Node.js em execução após a criação da VM
> * Usar o Key Vault para armazenar certificados com segurança
> * Automatizar implantações seguras de NGINX com cloud-init

Vá para o próximo tutorial para aprender a gerenciar imagens de VM.

> [!div class="nextstepaction"]
> [Criar imagens de VM personalizada](./tutorial-custom-images.md)
