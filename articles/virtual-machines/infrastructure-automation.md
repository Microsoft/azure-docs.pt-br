---
title: Usar ferramentas de automação de infraestrutura
description: Saiba como usar ferramentas de automação de infraestrutura como Ansible, chefe, Puppet, Terraform e Packer para criar e gerenciar máquinas virtuais no Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/17/2020
ms.author: cynthn
ms.openlocfilehash: 7a702fbf405011a9df3e7e77936a9a37f0fdfad7
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730631"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Usar ferramentas de automação de infraestrutura com máquinas virtuais no Azure

Para criar VMs (máquinas virtuais) em larga escala de maneira consistente, é ideal ter algum grau de automação. Há muitas ferramentas e soluções que permitem automatizar a implantação de toda a infraestrutura do Azure e o ciclo de vida de gerenciamento. Este artigo apresenta algumas das ferramentas de automação de infraestrutura que você pode usar no Azure. Essas ferramentas normalmente se encaixam em uma das seguintes abordagens:

- Automatizar a configuração de VMs
    - As ferramentas incluem o modelo [Ansible](#ansible), [chefe](#chef), [Puppet](#puppet)e [Azure Resource Manager](#azure-resource-manager-template).
    - As ferramentas específicas para a personalização de VM incluem [cloud-init](#cloud-init) para VMs do Linux, [DSC (Configuração de Estado Desejado) do PowerShell](#powershell-dsc) e [Extensão de Script Personalizado do Azure](#azure-custom-script-extension) para todas as VMs do Azure.

- Automatizar o gerenciamento de infraestrutura
    - As ferramentas incluem o [Packer](#packer), para automatizar as builds de imagem de VM personalizadas, e o [Terraform](#terraform), para automatizar a infraestrutura do processo de compilação.
    - A [Automação do Azure](#azure-automation) pode executar ações em sua infraestrutura local e do Azure.

- Automatizar a entrega e a implantação de aplicativos
    - Os exemplos incluem [Azure DevOps Services](#azure-devops-services) e [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) é um mecanismo de automação para gerenciamento de configurações, criação de VMs ou implantação de aplicativos. O Ansible usa um modelo sem agente, normalmente com as chaves SSH para autenticar e gerenciar computadores de destino. As tarefas de configuração são definidas em playbooks, com uma quantidade de módulos do Ansible para executar tarefas específicas. Para saber mais, confira [Como o Ansible funciona](https://www.ansible.com/how-ansible-works).

Saiba como:

- [Instalar e configurar o Ansible no Linux para usar com o Azure](/azure/developer/ansible/install-on-linux-vm).
- [Crie uma máquina virtual do Linux](/azure/developer/ansible/vm-configure).
- [Gerencie uma máquina virtual do Linux](/azure/developer/ansible/vm-manage).


## <a name="chef"></a>Chef
O [Chef](https://www.chef.io/) é uma plataforma de automação que ajuda a definir como a infraestrutura é configurada, implantada e gerenciada. Outros componentes incluem o Chef Habitat, para automação de ciclo de vida do aplicativo em vez da infraestrutura, e o Chef InSpec, que ajuda a automatizar a conformidade com requisitos de segurança e política. Os clientes Chef são instalados em computadores de destino, com um ou mais Servidores Chef centrais que armazenam e gerenciam as configurações. Para saber mais, confira [Visão geral do Chef](https://docs.chef.io/chef_overview.html).

Saiba como:

- [Implantar o Chef Automate do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Instalar o Chef no Windows e criar VMs do Azure](/azure/developer/chef/windows-vm-configure).


## <a name="puppet"></a>Puppet
O [Puppet](https://www.puppet.com) é uma plataforma de automação pronta para empresas que gerencia o processo de entrega e implantação do aplicativo. Os agentes são instalados em computadores de destino para permitir que o Puppet Master execute manifestos que definem a configuração desejada da infraestrutura e das VMs do Azure. O Puppet pode se integrar a outras soluções, como Jenkins e GitHub, para um fluxo de trabalho de devops aprimorado. Para saber mais, confira [Como o Puppet funciona](https://puppet.com/products/how-puppet-works).

Saiba como:

- [Implantar Puppet](https://puppet.com/docs/puppet/5.5/install_windows.html).


## <a name="cloud-init"></a>Cloud-init
[Inicialização de nuvem](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM do Linux, quando ela é inicializada pela primeira vez. Você pode utilizar a inicialização de nuvem para instalar pacotes e gravar arquivos, ou para configurar usuários e segurança. Como o cloud-init é executado durante o processo de inicialização inicial, não há etapa adicional ou agentes necessários para aplicar a configuração.  Para obter mais informações sobre como formatar corretamente seus arquivos `#cloud-config`, consulte o [site de documentação de cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  Os arquivos `#cloud-config` são arquivos de texto codificados em base64.

A inicialização de nuvem também funciona em distribuições. Por exemplo, você não usa **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, você pode definir uma lista de pacotes para instalar. Inicialização de nuvem usa automaticamente a ferramenta de gerenciamento de pacote nativo de distribuição que você selecionar.

Estamos trabalhando ativamente com nossos parceiros endossados do Linux distribuição para ter imagens habilitadas para Cloud-init disponíveis no Azure Marketplace. Essas imagens fazem com que as suas configurações e implantações de cloud-init funcionem perfeitamente com VMs e conjuntos de dimensionamento de máquina virtual.
Saiba mais detalhes sobre o cloud-init no Azure:

- [Suporte do cloud-init para máquinas virtuais Linux no Azure](./linux/using-cloud-init.md)
- [Tente um tutorial sobre configuração de VM automatizada usando o cloud-init](./linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
A [DSC (Configuração de Estado Desejado) do PowerShell](/powershell/scripting/dsc/overview/overview) é uma plataforma de gerenciamento para definir a configuração dos computadores de destino. A DSC também pode ser usada no Linux por meio do [servidor OMI (infraestrutura de gerenciamento aberta)](https://collaboration.opengroup.org/omi/).

As configurações de DSC definem o que instalar em um computador e como configurar o host. Um mecanismo do LCM (Gerenciador de Configurações Local) é executado em cada nó de destino que processa as ações solicitadas com base em configurações enviadas por push. Um servidor de pull é um serviço Web que é executado em um host central para armazenar as configurações de DSC e os recursos associados. O servidor de pull se comunica com o mecanismo de LCM em cada host de destino para fornecer as configurações necessárias e os relatórios de conformidade.

Saiba como:

- [Criar uma configuração básica de DSC](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Configurar um servidor de pull de DSC](/powershell/scripting/dsc/pull-server/pullserver).
- [Usar a DSC para Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Extensão de Script Personalizado do Azure
A Extensão de Script Personalizado do Azure para [Linux](./extensions/custom-script-linux.md) ou [Windows](./extensions/custom-script-windows.md) baixa e executa scripts em VMs do Azure. Você pode usar a extensão ao criar uma VM ou a qualquer momento depois que a VM estiver em uso.

Os scripts podem ser baixados do armazenamento do Azure ou de qualquer local público, por exemplo, um repositório GitHub. Com a Extensão de Script Personalizado, você pode escrever scripts em qualquer linguagem compatível com a VM de origem. Esses scripts podem ser usados para instalar aplicativos ou configurar a VM conforme desejado. Para proteger as credenciais, informações confidenciais, como senhas, podem ser armazenadas em uma configuração protegida. Essas credenciais são descriptografadas somente dentro da VM.

Saiba como:

- [Criar uma VM Linux com a CLI do Azure e usar a Extensão de Script Personalizado](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx?toc=%2fcli%2fazure%2ftoc.json).
- [Criar uma VM Windows com o Azure PowerShell e usar a Extensão de Script Personalizado](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis).


## <a name="packer"></a>Packer
O [Packer](https://www.packer.io) automatiza o processo de compilação quando você cria uma imagem de VM personalizada no Azure. Você pode usar o Packer para definir o sistema operacional e executar scripts pós-configuração que personalizam a VM para suas necessidades específicas. Uma vez configurado, a VM é capturada como uma imagem de Disco Gerenciado. O Packer automatiza o processo para criar os recursos de VM de origem, rede e armazenamento, executar os scripts de configuração e criar a imagem da VM.

Saiba como:

- [Usar o Packer para criar uma imagem de VM Linux no Azure](./linux/build-image-with-packer.md).
- [Usar o Packer para criar uma imagem de VM Windows no Azure](./windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
O [Terraform](https://www.terraform.io) é uma ferramenta de automação que permite definir e criar toda uma infraestrutura do Azure com uma única linguagem em formato de modelo - a HCL (HashiCorp Configuration Language). Com o Terraform, você define modelos que automatizam o processo para criar recursos de rede, armazenamento e VM para determinada solução de aplicativo. Você pode usar os modelos do Terraform existentes para outras plataformas com o Azure a fim de garantir a consistência e simplificar a implantação da infraestrutura sem a necessidade de convertê-los em modelos do Azure Resource Manager.

Saiba como:

- [Instalar e configurar o Terraform com o Azure](/azure/developer/terraform/getting-started-cloud-shell).
- [Criar uma infraestrutura do Azure com o Terraform](/azure/developer/terraform/create-linux-virtual-machine-with-infrastructure).


## <a name="azure-automation"></a>Automação do Azure
A [Automação do Azure](https://azure.microsoft.com/services/automation/) usa runbooks para processar um conjunto de tarefas nas máquinas virtuais desejadas. A Automação do Azure é usada para gerenciar VMs existentes em vez de criar uma infraestrutura. A Automação do Azure pode ser executada em VMs Linux e Windows, assim como em máquinas virtuais ou físicas locais por meio de um Hybrid Runbook Worker. Os runbooks podem ser armazenados em um repositório de controle de origem, como o GitHub. Esses runbooks podem ser executados manualmente ou em um agendamento definido.

A Automação do Azure também oferece um serviço de DSC (Configuração de Estado Desejado) que permite criar definições de como determinado conjunto de máquinas virtuais deve ser configurado. A DSC faz com que a configuração obrigatória seja aplicada e a VM permanece consistente. A DSC da Automação do Azure é executada em computadores Windows e Linux.

Saiba como:

- [Criar um runbook do PowerShell](../automation/learn/automation-tutorial-runbook-textual-powershell.md).
- [Usar Hybrid Runbook Worker para gerenciar recursos locais](../automation/automation-hybrid-runbook-worker.md).
- [Usar o DSC de Automação do Azure](../automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
O [Azure DevOps Services](https://www.visualstudio.com/team-services/) é um pacote de ferramentas que ajuda você a compartilhar e acompanhar código, usar builds automatizados e criar um pipeline de CI/CD (integração e desenvolvimento contínuos completos). O Azure DevOps Services integra-se ao Visual Studio e a outros editores para simplificar o uso. O Azure DevOps Services também pode criar e configurar VMs do Azure e implantar o código nelas.

Saiba mais sobre:

- [Azure DevOps Services](/azure/devops/user-guide/index).


## <a name="jenkins"></a>Jenkins
O [Jenkins](https://jenkins.io) é um servidor de integração contínua que ajuda a implantar e testar aplicativos e a criar pipelines automatizados para entrega de código. Há centenas de plug-ins para estender a plataforma principal do Jenkins, e você também pode integrar com muitos outros produtos e soluções por meio de webhooks. Manualmente, você pode instalar o Jenkins em uma VM do Azure, executá-lo de dentro de um contêiner do Docker ou usar uma imagem do Azure Marketplace pré-criada.

Saiba como:

- [Crie uma infraestrutura de desenvolvimento em uma VM do Linux no Azure com Jenkins, GitHub e Docker](/azure/developer/jenkins/pipeline-with-github-and-docker).


## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager
O [Azure Resource Manager](../azure-resource-manager/templates/overview.md) é o serviço de implantação e gerenciamento do Azure. Ele fornece uma camada de gerenciamento que lhe permite criar, atualizar e excluir recursos em sua assinatura do Azure. Use recursos de gerenciamento, como controle de acesso, bloqueios e marcas, para proteger e organizar seus recursos após a implantação.

Saiba como:

- [Implante VMs pontuais usando um modelo do Resource Manager](./linux/spot-template.md).
- [Crie uma máquina virtual do Windows a partir de um modelo do Resource Manager](./windows/ps-template.md).
- [Baixe o modelo para uma VM](/previous-versions/azure/virtual-machines/windows/download-template).
- [Crie um modelo do Azure Image Builder](./linux/image-builder-json.md).

## <a name="next-steps"></a>Próximas etapas
Há muitas opções diferentes para usar as ferramentas de automação de infraestrutura no Azure. Você tem a liberdade de usar a solução que melhor atenda às suas necessidades e ao seu ambiente. Para começar e experimentar algumas das ferramentas internas do Azure, confira como automatizar a personalização de uma VM [Linux](./linux/tutorial-automate-vm-deployment.md) ou [Windows](./windows/tutorial-automate-vm-deployment.md).
