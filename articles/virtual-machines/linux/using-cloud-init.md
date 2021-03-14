---
title: Visão geral do suporte à cloud-init para VMs do Linux no Azure
description: Visão geral de funcionalidades da cloud-init para configurar uma VM no tempo de provisionamento no Azure.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/14/2021
ms.author: danis
ms.openlocfilehash: ac907c2ea2ae53bd192c01232c66e0467025daae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563219"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Suporte à cloud-init para máquinas virtuais no Azure
Este artigo mostra que o suporte que existe para a [cloud-init](https://cloudinit.readthedocs.io) para configurar uma máquina virtual VM ou conjuntos de dimensionamento de máquinas virtuais no momento do provisionamento no Azure. Essas configurações de cloud-init são executadas na primeira inicialização depois que os recursos são provisionados pelo Azure.  

O provisionamento de VM é o processo em que o Azure passará seus valores de parâmetro de criação de VM, como nome de host, nome de usuário, senha etc. e os disponibilizará para a VM à medida que ele for inicializado. Um 'agente de provisionamento' consumirá esses valores, vai configurar a VM e relatará quando isso for concluído. 

O Azure dá suporte a dois agentes de provisionamento de [cloud-init](https://cloudinit.readthedocs.io) e ao [WALA (Agente Linux do Azure)](../extensions/agent-linux.md).

## <a name="cloud-init-overview"></a>Visão geral da cloud-init
A [cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM do Linux quando ela é inicializada pela primeira vez. Você pode utilizar a inicialização de nuvem para instalar pacotes e gravar arquivos, ou para configurar usuários e segurança. Como o cloud-init é executado durante o processo de inicialização inicial, não há etapa adicional ou agentes necessários para aplicar a configuração.  Para obter mais informações sobre como formatar corretamente seus arquivos `#cloud-config` ou outras entradas, confira o [site de documentação da cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  Os arquivos `#cloud-config` são arquivos de texto codificados em base64.

A cloud-init também funciona entre distribuições diferentes. Por exemplo, você não usa **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, você pode definir uma lista de pacotes para instalar. A cloud-init usará automaticamente a ferramenta de gerenciamento de pacote nativo de distribuição que você selecionar.

Estamos trabalhando ativamente com nossos parceiros endossados do Linux distribuição para ter imagens habilitadas para Cloud-init disponíveis no Azure Marketplace. Essas imagens farão com que as implantações e as configurações de cloud-init funcionem perfeitamente com VMs e conjuntos de dimensionamento de máquinas virtuais. Inicialmente, colaboramos com os parceiros da distribuição de Linux endossada e upstream para garantir as funções de cloud-init com o sistema operacional no Azure, então os pacotes são atualizados e disponibilizados publicamente nos repositórios de pacotes da distribuição. 

Há dois estágios para disponibilizar o cloud-init para os sistemas operacionais de distribuição do Linux endossados no Azure, o suporte a pacotes e o suporte a imagens:
* o "suporte a pacotes do cloud-init no Azure" documenta quais pacotes do cloud-init, juntamente com as versões posteriores, são compatíveis ou estão em versão prévia, para que você possa usar esses pacotes com o sistema operacional em uma imagem personalizada.
* "imagem pronta para o cloud-init" documenta se a imagem já está configurada para usar o cloud-init.


### <a name="canonical"></a>Canônico
| Fornecedor/versão| Oferta | SKU | Versão | imagem pronta para o cloud-init | suporte ao pacote do cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 20.04 |UbuntuServer |18.04-LTS |mais recente |sim | sim |
|Canonical 18.04 |UbuntuServer |18.04-LTS |mais recente |sim | sim |
|Canonical 16.04|UbuntuServer |16.04-LTS |mais recente |sim | sim |
|Canonical 14.04|UbuntuServer |14.04.5-LTS |mais recente |sim | sim |

### <a name="rhel"></a>RHEL
| Fornecedor/versão | Oferta | SKU | Versão | imagem pronta para o cloud-init | suporte ao pacote do cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |sim | sim – suporte da versão do pacote: *18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | Sim (Observação: esta é uma imagem de visualização e não **deve** mais ser usada, mas será removida em 1º de setembro de 2020) | N/D |
|RedHat 7.7 (Gen1)|RHEL |7.7 | 7.7.2020051912 | sim | sim – suporte da versão do pacote: *18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 77-gen2 | 7.7.2020051913 | sim | sim – suporte da versão do pacote: *18.5-6.el7*|
|RedHat 7.7 (Gen1)|RHEL |7-LVM | 7.7.2020051921 | sim | sim – suporte da versão do pacote: *18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 7lvm-gen2 | 7.7.2020051922  | sim | sim – suporte da versão do pacote: *18.5-6.el7*|
|RedHat 7.7 (Gen1) |rhel-byos | rhel-lvm77 | 7.7.20200416 | sim  | sim – suporte da versão do pacote: *18.5-6.el7*|
|RedHat 8.1 (Gen1) |RHEL |8.1-ci |8.1.2020042511 | Sim (Observação: esta é uma imagem de visualização e, depois que todas as imagens RHEL 8,1 dão suporte a Cloud-init, isso será removido em 1º de agosto de 2020) | Não, ETA para suporte completo em junho de 2020|
|RedHat 8.1 (Gen2) |RHEL |81-ci-gen2 |8.1.2020042524 | Sim (Observação: esta é uma imagem de visualização e, depois que todas as imagens RHEL 8,1 dão suporte a Cloud-init, isso será removido em 1º de agosto de 2020) | Não, ETA para suporte completo em junho de 2020 |

* Todas as imagens de RedHat: RHEL 7,8 e 8,2 (Gen1 e Gen2) são provisionadas usando Cloud-init.

### <a name="centos"></a>CentOS

| Fornecedor/versão | Oferta | SKU | Versão | imagem pronta para o cloud-init | suporte ao pacote do cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |Sim (Observação: esta é uma imagem de visualização e não **deve** mais ser usada, mas será removida em 1º de setembro de 2020) | N/D |
|OpenLogic 7.7 |CentOS | 7.7 |7.7.2020062400 |sim | Sim – suporte da versão do pacote: `18.5-6.el7.centos.5`|
|OpenLogic 7,7 (Gen2) |CentOS | 7_7-Gen2 |7.7.2020062401 |sim | Sim – suporte da versão do pacote: `18.5-6.el7.centos.5`|
|OpenLogic 7.7 |CentOS-HPC | 7.7 |7.6.2020062600 |sim | Sim – suporte da versão do pacote: `18.5-6.el7.centos.5`|
|OpenLogic 7,7 (Gen2) |CentOS-HPC | 7_7-Gen2 |7.6.2020062601 |sim | Sim – suporte da versão do pacote: `18.5-6.el7.centos.5`|
|OpenLogic 8,1 |CentOS | 8_1 |8.1.2020062400 |sim | Sim – suporte da versão do pacote: `18.5-7.el8_1.1`|
|OpenLogic 8,1 (Gen2) |CentOS | 8_1-Gen2 |8.1.2020062401 |sim | Sim – suporte da versão do pacote: `18.5-7.el8_1.1`|
|OpenLogic 8,1 |CentOS-HPC | 8_1 |8.1.2020062400 |sim | Sim – suporte da versão do pacote: `18.5-7.el8_1.1`|
|OpenLogic 8,1 (Gen2) |CentOS-HPC: 8_1-Gen2 | 8_1-Gen2 |8.1.2020062401 |sim | Sim – suporte da versão do pacote: `18.5-7.el8_1.1`|

* Todas as imagens OpenLogic: CentOS 7,8 e 8,2 (Gen1 e Gen2) são provisionadas usando Cloud-init.

### <a name="oracle"></a>Oracle

| Fornecedor/versão | Oferta | SKU | Versão | imagem pronta para o cloud-init | suporte ao pacote do cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7.7 |Oracle-Linux |77-ci |7.7.01| imagem de visualização (Observação: esta é uma imagem de visualização e, uma vez que todas as imagens do Oracle 7,7 dão suporte a Cloud-init, ela será removida mid 2020, o aviso será fornecido) | não, na versão prévia, o pacote é: *18.5-3.0.1.el7*

### <a name="suse-sles"></a>SUSE SLES
Essas imagens SLES foram atualizadas para provisionar usando Cloud-init, as variantes de imagem Gen2 também foram atualizadas.
* SuSE: SLES-15-SP1-{Basic/BYOS/HPC/HPC-BYOS/CHOST-BYOS}: Gen1:2020.06.10
* SuSE: SLES-SAP-15-SP1: Gen1:2020.06.10
* SuSE: SLES-SAP-15-SP1-BYOS: Gen1:2020.06.10
* SuSE: Manager-proxy-4-BYOS: Gen1:2020.06.10
* SuSE: Manager-Server-4-BYOS: Gen1:2020.06.10
* SuSE: SLES-{BYOS/SAP/SAP-BYOS}: 15:2020.06.10
* SuSE: SLES-12-SP5: Gen1:2020.06.10
* SuSE: SLES-12-SP5 {-BYOS/Basic/HPC-BYOS/HPC}: Gen1:2020.06.10
* SuSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP4:2020.06.10
* SuSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP3:2020.06.10
* SuSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP2:2020.06.10


### <a name="debian"></a>Debian
| Fornecedor/versão | Oferta | SKU | Versão | imagem pronta para o cloud-init | suporte ao pacote do cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
| Debian (GEN1) |Debian-10 | 10-cloudinit |cloud-init-preview| Sim (Observação: esta é uma imagem de visualização e não **deve** mais ser usada, isso será removido em 1º de janeiro de 2021) | Não, em versão prévia. |
| Debian (Gen2) |Debian-10 | 10-cloudinit-Gen2 |cloud-init-preview| Sim (Observação: esta é uma imagem de visualização e não **deve** mais ser usada, isso será removido em 1º de janeiro de 2021) | Não, em versão prévia. |
| Debian (GEN1) |Debian-10 | 10-cloudinit |10:0.20201013.422| sim | Sim – suporte da versão do pacote: `20.2-2~deb10u1` |
| Debian (Gen2) |Debian-10 | 10-cloudinit-Gen2 |0.20201013.422| sim | Sim – suporte da versão do pacote: `20.2-2~deb10u1` |


Atualmente, o Azure Stack dará suporte ao provisionamento de imagens habilitadas para cloud-init.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Qual é a diferença entre cloud-init e o Agente do Linux (WALA)?
O WALA é um agente específico da plataforma do Azure usado para provisionar e configurar VMs e lidar com [extensões do Azure](../extensions/features-linux.md). 

Estamos aprimorando a tarefa de configuração de VMs para usar cloud-init em vez do agente do Linux, para permitir que os clientes existentes de cloud-init usem seus scripts atuais de cloud-init ou que os novos clientes aproveitem as funcionalidades avançadas de configuração do cloud-init. Se você tiver investimentos existentes em scripts do cloud-init para configurar os sistemas Linux, não há **nenhuma configuração adicional necessária** para habilitar o cloud-init para processá-los. 

O cloud-init não pode processar extensões do Azure, portanto, o WALA ainda é necessário na imagem para processar extensões, mas precisará ter o respectivo código de provisionamento desabilitado, pois imagens de distribuições do Linux endossadas que estejam sendo convertidas para provisionamento pelo cloud-init terão o WALA instalado e estarão configuradas corretamente.

Ao criar uma VM, se você não incluir a opção `--custom-data` da CLI do Azure no momento de provisionamento, o cloud-init ou o WALA usa os parâmetros necessários mínimos para provisionar a VM e concluir a implantação com os padrões.  Se você fizer referência à configuração do cloud-init com a opção `--custom-data`, tudo o que estiver contido em seus dados personalizados estará disponível para o cloud-init quando a VM for inicializada.

As configurações de cloud-init aplicadas às VMs não têm restrições de tempo e não farão com que uma implantação falhe por tempo limite. Isso não se verifica para o WALA, pois se você alterar os padrões do WALA para processar dados personalizados, ele não poderá exceder o tempo de provisionamento total permitido de 40 minutos da VM, caso contrário, a criação da VM falhará.

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
> [!NOTE]
> Cloud-init tem vários [tipos de entrada](https://cloudinit.readthedocs.io/en/latest/topics/format.html), Cloud-init usará a primeira linha do CustomData/UserData para indicar como ele deve processar a entrada; por exemplo, `#cloud-config` indica que o conteúdo deve ser processado como uma configuração de Cloud-init.


Pressione `ctrl-X` para sair do arquivo, digite `y` para salvar o arquivo e pressione `enter` para confirmar o nome do arquivo na saída.

A etapa final cria uma VM com o comando [az vm create](/cli/azure/vm). 

O exemplo a seguir cria uma VM denominada *centos74* e cria chaves SSH, se elas ainda não existirem em um local de chave padrão. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.  Utiçize o `--custom-data` parâmetro para passar no arquivo de configuração de inicialização de nuvem. Forneça o caminho completo para a configuração *cloud-init.txt* se você salvou o arquivo fora do seu diretório de trabalho atual. 

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Quando a VM tiver sido criada, a CLI do Azure mostrará informações específicas para a sua implantação. Anote `publicIpAddress`. Esse endereço é usado para acessar a VM.  Leva algum tempo para que a VM seja criada, os pacotes sejam instalados e o aplicativo comece a funcionar. Há tarefas em segundo plano que continuarão em execução depois que a CLI do Azure faz você voltar para o prompt. Você pode usar SSH na VM e usar as etapas descritas na seção de resolução de problemas para exibir os logs de cloud-init. 

Você também pode implantar uma VM habilitada para Cloud-init passando os [parâmetros no modelo ARM](../../azure-resource-manager/templates/deploy-cli.md#inline-parameters).

## <a name="troubleshooting-cloud-init"></a>Resolução de problemas do cloud-init
Depois que a VM tiver sido provisionada, o cloud-init será executado em todos os módulos e o script definido em `--custom-data` para configurar a VM.  Se você precisar solucionar quaisquer erros ou omissões da configuração, você precisará pesquisar o nome do módulo (`disk_setup` ou `runcmd` por exemplo) no log do cloud-init - localizado em **/var/log/cloud-init.log**.

> [!NOTE]
> Nem toda falha do módulo resulta em uma falha fatal de configuração geral do cloud-init. Por exemplo, usando o módulo `runcmd`, se o script falhar, o cloud-init ainda relatará o provisionamento bem-sucedido, porque o módulo runcmd foi executado.

Para obter mais detalhes de registro do cloud-init, consulte a [documentação do cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Próximas etapas

[Solucionar problemas com Cloud-init](cloud-init-troubleshooting.md).


Para obter exemplos de alterações de configuração do cloud-init, consulte os seguintes documentos:
 
- [Add an additional Linux user to a VM](cloudinit-add-user.md) (Adicionar um usuário adicional do Linux a uma VM)
- [Run a package manager to update existing packages on first boot](cloudinit-update-vm.md) (Executar um gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização)
- [Change VM local hostname](cloudinit-update-vm-hostname.md) (Alterar o nome do host local da VM) 
- [Install an application package, update configuration files and inject keys](tutorial-automate-vm-deployment.md) (Instalar um pacote de aplicativo, atualizar os arquivos de configuração e injetar chaves)
