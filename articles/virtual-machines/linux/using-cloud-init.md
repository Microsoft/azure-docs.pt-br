---
title: Visão geral do suporte de cloud-init para máquinas virtuais Linux no Azure | Microsoft Docs
description: Visão geral dos recursos de cloud-init no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 057f7c42c037dac4cb2be686df09287de7113f0d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695383"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Cloud-init para máquinas virtuais no Azure
Este artigo mostra que o suporte que existe para a [cloud-init](https://cloudinit.readthedocs.io) para configurar uma máquina virtual VM ou conjunto de dimensionamento de máquinas virtuais (VMSS) no momento do provisionamento no Azure. Esses scripts de cloud-init são executados na primeira inicialização depois que os recursos são provisionados pelo Azure.  

## <a name="cloud-init-overview"></a>Visão geral da inicialização de nuvem
[Inicialização de nuvem](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM do Linux, quando ela é inicializada pela primeira vez. Você pode utilizar a inicialização de nuvem para instalar pacotes e gravar arquivos, ou para configurar usuários e segurança. Como o cloud-init é executado durante o processo de inicialização inicial, não há etapa adicional ou agentes necessários para aplicar a configuração.  Para obter mais informações sobre como formatar corretamente seus arquivos `#cloud-config`, consulte o [site de documentação de cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  Os arquivos `#cloud-config` são arquivos de texto codificados em base64.

A inicialização de nuvem também funciona em distribuições. Por exemplo, você não usa **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, você pode definir uma lista de pacotes para instalar. Inicialização de nuvem usa automaticamente a ferramenta de gerenciamento de pacote nativo de distribuição que você selecionar.

 Trabalhamos ativamente com nossos parceiros endossados de distribuição de Linux para termos imagens de cloud-init habilitadas disponíveis no marketplace do Azure. Essas imagens farão com que as suas configurações e implantações de cloud-init funcionem perfeitamente com VMs e Conjuntos de Dimensionamento de VM (VMSS). A tabela a seguir descreve a disponibilidade de imagens habilitadas de cloud-init na plataforma do Azure:

| Publicador | Oferta | SKU | Versão | Cloud-init pronto |
|:--- |:--- |:--- |:--- |:--- |
|Canônico |UbuntuServer |18.04-LTS |latest |sim | 
|Canônico |UbuntuServer |17.10 |latest |sim | 
|Canônico |UbuntuServer |16.04-LTS |latest |sim | 
|Canônico |UbuntuServer |14.04.5-LTS |latest |sim |
|CoreOS |CoreOS |Estável |latest |sim |
|OpenLogic |CentOS |7-CI |latest |preview |
|RedHat |RHEL |7-RAW-CI |latest |preview |

Atualmente, o Azure Stack não dá suporte para provisionamento de RHEL 7.4 e CentOS 7.4 usando o cloud-init.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Qual é a diferença entre cloud-init e o Agente do Linux (WALA)?
WALA é um agente específico da plataforma do Azure usado para provisionar e configurar VMs e lidar com extensões do Azure. Estamos aprimorando a tarefa de configuração de VMs para usar cloud-init em vez do agente do Linux, para permitir que os clientes existentes de cloud-init usem seus scripts atuais de cloud-init.  Se você tiver investimentos existentes em scripts de cloud-init para configurar os sistemas Linux, não há **nenhuma configuração adicional necessária** para habilitá-los. 

Se você não incluir a opção `--custom-data` da CLI do Azure no momento de provisionamento, WALA usa os parâmetros necessários mínimos para provisionar a VM e concluir a implantação com os padrões.  Se você fizer referência à opção `--custom-data` do cloud-init, tudo o que estiver contido em seus dados personalizados (configurações individuais ou script completo) substituirá os padrões WALA. 

As configurações de WALA de VMs possuem limites de tempo para trabalharem dentro do tempo máximo de provisionamento da VM.  As configurações de cloud-init aplicadas às VMs não têm restrições de tempo e não farão com que uma implantação falhe por tempo limite. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Implantando uma Máquina Virtual habilitada para cloud-init
Implantar uma máquina virtual habilitada para cloud-init é tão simples quanto fazer referência a uma distribuição habilitada para cloud-init durante a implantação.  Os mantenedores da distribuição de Linux precisam optar por habilitar e integrar o cloud-init em suas imagens publicadas da base do Azure. Depois de confirmar, a imagem que você deseja implantar é habilitada para cloud-init, e você pode usar a CLI do Azure para implantar a imagem. 

A primeira etapa para implantar essa imagem é criar um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
A próxima etapa é criar um arquivo no shell atual, chamado *cloud-init.txt*, e colar a configuração a seguir. Para este exemplo, crie o arquivo no Cloud Shell, não no seu computador local. Você pode usar qualquer editor que queira. Insira `sensible-editor cloud-init.txt` para criar o arquivo e ver uma lista de editores disponíveis. Escolha #1 para usar o editor **nano**. Certifique-se de que o arquivo de inicialização de nuvem inteiro seja copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Pressione `ctrl-X` para sair do arquivo, digite `y` para salvar o arquivo e pressione `enter` para confirmar o nome do arquivo na saída.

A etapa final cria uma VM com o comando [az vm create](/cli/azure/vm). 

O exemplo a seguir cria uma VM denominada *centos74* e cria chaves SSH, se elas ainda não existirem em um local de chave padrão. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.  Utiçize o `--custom-data` parâmetro para passar no arquivo de configuração de inicialização de nuvem. Forneça o caminho completo para a configuração *cloud-init.txt* se você salvou o arquivo fora do seu diretório de trabalho atual. O exemplo a seguir cria uma VM chamada *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Quando a VM tiver sido criada, a CLI do Azure mostrará informações específicas para a sua implantação. Anote `publicIpAddress`. Esse endereço é usado para acessar a VM.  Leva algum tempo para que a VM seja criada, os pacotes sejam instalados e o aplicativo comece a funcionar. Há tarefas em segundo plano que continuarão em execução depois que a CLI do Azure faz você voltar para o prompt. Você pode usar SSH na VM e usar as etapas descritas na seção de resolução de problemas para exibir os logs de cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Resolução de problemas do cloud-init
Depois que a VM tiver sido provisionada, o cloud-init será executado em todos os módulos e o script definido em `--custom-data` para configurar a VM.  Se você precisar solucionar quaisquer erros ou omissões da configuração, você precisará pesquisar o nome do módulo (`disk_setup` ou `runcmd` por exemplo) no log do cloud-init - localizado em **/var/log/cloud-init.log**.

> [!NOTE]
> Nem toda falha do módulo resulta em uma falha fatal de configuração geral do cloud-init. Por exemplo, usando o módulo `runcmd`, se o script falhar, o cloud-init ainda relatará o provisionamento bem-sucedido, porque o módulo runcmd foi executado.

Para obter mais detalhes de registro do cloud-init, consulte a [documentação do cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Próximas etapas
Para obter exemplos de alterações de configuração do cloud-init, consulte os seguintes documentos:
 
- [Add an additional Linux user to a VM](cloudinit-add-user.md) (Adicionar um usuário adicional do Linux a uma VM)
- [Run a package manager to update existing packages on first boot](cloudinit-update-vm.md) (Executar um gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização)
- [Change VM local hostname](cloudinit-update-vm-hostname.md) (Alterar o nome do host local da VM) 
- [Install an application package, update configuration files and inject keys](tutorial-automate-vm-deployment.md) (Instalar um pacote de aplicativo, atualizar os arquivos de configuração e injetar chaves)
