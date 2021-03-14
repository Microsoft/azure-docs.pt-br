---
title: Implantar a OpenShift do contêiner da plataforma 3,11 Self-Managed oferta do Marketplace no Azure
description: Implante a plataforma de contêiner OpenShift 3,11 Self-Managed oferta do Marketplace no Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f257ed1097f49074d70f45f59e9040265f6cedef
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670443"
---
# <a name="configure-prerequisites"></a>Configurar pré-requisitos

Antes de usar a oferta do Marketplace para implantar um cluster da plataforma de contêiner do OpenShift autogerenciada 3,11 no Azure, alguns pré-requisitos devem ser configurados.  Leia o artigo [pré-requisitos do OpenShift](./openshift-container-platform-3x-prerequisites.md) para obter instruções para criar uma chave SSH (sem uma frase secreta), cofre de chaves do Azure, segredo do cofre de chaves e uma entidade de serviço.

 
## <a name="deploy-using-the-marketplace-offer"></a>Implantar usando a oferta do Marketplace

A maneira mais simples de implantar um cluster OpenShift da plataforma de contêiner autogerenciada 3,11 no Azure é usar a [oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

Essa opção é a mais simples, mas também tem recursos de personalização limitados. A oferta do Marketplace implanta a plataforma de contêiner OpenShift 3.11.82 e inclui as seguintes opções de configuração:

- **Nós mestres**: tipo de instância de nós mestres três (3) com configurável.
- **Nós mestres**: tipo de instância de nós mestres três (3) com configurável.
- **Nós**: o número de nós (entre 1 e 9) e o tipo de instância são configuráveis.
- **Tipo de Disco**: Discos gerenciados são usados.
- **Rede**: suporte para rede nova ou existente e intervalo CIDR personalizado.
- **CNS**: CNS pode ser habilitado.
- **Métricas**: as métricas Hawkular podem ser habilitadas.
- **Registro em log**: o log de EFK pode ser habilitado.
- **Provedor de nuvem do Azure**: habilitado por padrão, pode ser desabilitado.

No canto superior esquerdo do portal do Azure, clique em **criar um recurso**, insira ' plataforma de contêiner openshift ' na caixa de pesquisa e pressione Enter.

   ![Nova pesquisa de recursos](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

A página de resultados será aberta com a **plataforma de contêiner do Red Hat OpenShift 3,11 autogerenciado** na lista. 

   ![Novo resultado da pesquisa de recursos](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Clique na oferta para exibir os detalhes da oferta. Para implantar essa oferta, clique em **criar**. A interface do usuário para inserir os parâmetros necessários será exibida. A primeira tela é a folha **noções básicas** .

   ![Página de título da oferta](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Noções básicas**

Para obter ajuda sobre qualquer um dos parâmetros de entrada, passe o mouse sobre o ***i*** ao lado do nome do parâmetro.

Insira valores para os parâmetros de entrada e clique em **OK**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome de usuário do administrador da VM | O usuário administrador a ser criado em todas as instâncias de VM |
| Chave pública SSH para usuário administrador | Chave pública SSH usada para fazer logon na VM-não deve ter uma frase secreta |
| Subscription | Assinatura do Azure na qual implantar o cluster |
| Grupo de recursos | Criar um novo grupo de recursos ou selecionar um grupo de recursos vazio existente para recursos de cluster |
| Local | Região do Azure na qual implantar o cluster |

   ![Folha noções básicas da oferta](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Configurações de infraestrutura**

Insira valores para os parâmetros de entrada e clique em **OK**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Prefixo do nome do cluster OCP | Prefixo de cluster usado para configurar os nomes de host para todos os nós. Entre 1 e 20 caracteres |
| Tamanho do nó mestre | Aceite o tamanho da VM padrão ou clique em **alterar tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho apropriado da VM para sua carga de trabalho |
| Tamanho do nó de infraestrutura | Aceite o tamanho da VM padrão ou clique em **alterar tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho apropriado da VM para sua carga de trabalho |
| Número de nós de aplicativo | Aceite o tamanho da VM padrão ou clique em **alterar tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho apropriado da VM para sua carga de trabalho |
| Tamanho do nó do aplicativo | Aceite o tamanho da VM padrão ou clique em **alterar tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho apropriado da VM para sua carga de trabalho |
| Tamanho do host bastião | Aceite o tamanho da VM padrão ou clique em **alterar tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho apropriado da VM para sua carga de trabalho |
| Rede virtual nova ou existente | Criar uma nova vNet (padrão) ou usar uma vNet existente |
| Escolha as configurações de CIDR padrão ou personalize o intervalo de IP (CIDR) | Aceite intervalos CIDR padrão ou selecione **intervalo de IP personalizado** e insira informações de CIDR personalizadas.  As configurações padrão criarão vNet com CIDR/14, sub-rede mestre com 10.1.0.0/16, rede de infraestrutura com 10.2.0.0/16 e sub-rede de computação e CNS com 10.3.0.0/16 |
| Nome do grupo de recursos Key Vault | O nome do grupo de recursos que contém o Key Vault |
| Nome do cofre de chaves | O nome do Key Vault que contém o segredo com a chave privada SSH.  Somente caracteres alfanuméricos e traços são permitidos e têm entre 3 e 24 caracteres |
| Nome do segredo | O nome do segredo que contém a chave privada SSH.  Somente caracteres alfanuméricos e traços são permitidos |

   ![Folha de infraestrutura da oferta](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Alterar tamanho**

Para selecionar um tamanho de VM diferente, clique em ***alterar tamanho** _.  A janela seleção de VM será aberta.  Selecione o tamanho da VM que você deseja e clique em _ * selecionar * *.

   ![Selecionar tamanho da VM](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Rede virtual existente**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome da rede virtual existente | Nome da vNet existente |
| Nome da sub-rede para nós mestres | Nome da sub-rede existente para nós mestres.  Precisa conter pelo menos 16 endereços IP e seguir o RFC 1918 |
| Nome da sub-rede para nós de infraestrutura | Nome da sub-rede existente para nós de infraestrutura.  Precisa conter pelo menos 32 endereços IP e seguir a RFC 1918 |
| Nome da sub-rede para nós de computação e CNS | Nome da sub-rede existente para nós de computação e CNS.  Precisa conter pelo menos 32 endereços IP e seguir a RFC 1918 |
| Grupo de recursos para a rede virtual existente | Nome do grupo de recursos que contém a vNet existente |

   ![Oferecer vnet existente de infraestrutura](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Intervalo de IP personalizado**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Intervalo de endereços para a rede virtual | CIDR personalizado para a vNet |
| Intervalo de endereços da sub-rede que contém os nós mestres | CIDR personalizado para sub-rede mestre |
| Intervalo de endereços da sub-rede que contém os nós de infraestrutura | CIDR personalizado para sub-rede de infraestrutura |
| Intervalo de endereços para a sub-rede que contém os nós de computação e CNS | CIDR personalizado para os nós de computação e CNS |

   ![Intervalo de IP personalizado de infraestrutura de oferta](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Insira valores para os parâmetros de entrada e clique em **OK**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Senha de usuário do administrador do OpenShift | Senha para o usuário OpenShift inicial.  Esse usuário também será o administrador do cluster |
| Confirmar senha de usuário do administrador do OpenShift | Digite novamente a senha de usuário do administrador do OpenShift |
| Nome de usuário do Gerenciador de assinaturas do Red Hat | Nome de usuário para acessar sua assinatura do Red Hat ou ID da organização.  Essa credencial é usada para registrar a instância de RHEL em sua assinatura e não será armazenada pela Microsoft ou Red Hat |
| Senha de usuário do Gerenciador de assinaturas do Red Hat | Senha para acessar sua assinatura do Red Hat ou chave de ativação.  Essa credencial é usada para registrar a instância de RHEL em sua assinatura e não será armazenada pela Microsoft ou Red Hat |
| ID do pool OpenShift do Gerenciador de assinaturas do Red Hat | ID do pool que contém a qualificação da plataforma de contêiner OpenShift. Verifique se você tem direitos suficientes da plataforma de contêiner do OpenShift para a instalação do cluster |
| ID do pool OpenShift do Gerenciador de assinaturas do Red Hat para nós de agente/mestre | ID do pool que contém direitos de plataforma de contêiner OpenShift para nós de agente/mestre. Verifique se você tem direitos suficientes da plataforma de contêiner do OpenShift para a instalação do cluster. Se não estiver usando a ID do pool do agente/mestre, insira a ID do pool para nós de aplicativo |
| Configurar o provedor de nuvem do Azure | Configure o OpenShift para usar o provedor de nuvem do Azure. Necessário se estiver usando a anexação de disco do Azure para volumes persistentes.  O padrão é sim |
| GUID de ID do cliente da entidade de serviço do Azure AD | GUID de ID do cliente da entidade de serviço do Azure AD – também conhecido como AppID. Necessário somente se configurar o provedor de nuvem do Azure definido como **Sim** |
| Segredo da ID do cliente da entidade de serviço do Azure AD | Segredo da ID do cliente da entidade de serviço do Azure AD. Necessário somente se configurar o provedor de nuvem do Azure definido como **Sim** |
 
   ![Folha de OpenShift da oferta](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Configurações adicionais**

A folha configurações adicionais permite a configuração do CNS para armazenamento GlusterFS, registro em log, métricas e subdomínio do roteador.  O padrão não instalará nenhuma dessas opções e usará nip.io como o subdomínio do roteador para fins de teste. A habilitação do CNS instalará três nós de computação adicionais com três discos anexados adicionais que hospedarão o GlusterFS pods.  

Insira valores para os parâmetros de entrada e clique em **OK**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Configurar o armazenamento nativo do contêiner (CNS) | Instala o CNS no cluster OpenShift e o habilita como armazenamento. Será padrão se o provedor do Azure estiver desabilitado |
| Configurar o log de cluster | Instala a funcionalidade de log de EFK no cluster.  Dimensionar nós de infraestrutura apropriadamente para hospedar pods de EFK |
| Configurar métricas para o cluster | Instala as métricas de Hawkular no cluster OpenShift.  Dimensionar nós de infraestrutura apropriadamente para hospedar pods de métricas de Hawkular |
| Subdomínio do roteador padrão | Selecione nipio para teste ou personalizado para inserir seu próprio subdomínio para produção |
 
   ![Oferecer folha adicional](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Configurações adicionais-parâmetros extras**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| CNS Tamanho do nó | Aceite o tamanho do nó padrão ou selecione **alterar tamanho** para selecionar um novo tamanho de VM |
| Insira seu subdomínio personalizado | O domínio de roteamento personalizado a ser usado para expor aplicativos por meio do roteador no cluster OpenShift.  Certifique-se de criar a entrada DNS de curinga apropriada] |
 
   ![Oferecer instalação adicional do CNS](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Resumo**

A validação ocorre neste estágio para verificar se a cota de núcleo é suficiente para implantar o número total de VMs selecionadas para o cluster.  Examine todos os parâmetros que foram inseridos.  Se as entradas forem aceitáveis, clique em **OK** para continuar.

   ![Folha de resumo da oferta](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Comprar**

Confirme as informações de contato na página comprar e clique em **comprar** para aceitar os termos de uso e iniciar a implantação do cluster da plataforma de contêiner do OpenShift.

   ![Folha de compra da oferta](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Conectar-se ao cluster OpenShift

Quando a implantação for concluída, recupere a conexão da seção de saída da implantação. Conecte-se ao console do OpenShift com seu navegador usando a **URL do console do OpenShift**. Você também pode SSH para o host bastião. A seguir está um exemplo em que o nome de usuário do administrador é clusteradmin e o IP público de bastiões FQDN do DNS é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Próximas etapas

- [Tarefas de pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Solução de problemas de implantação do OpenShift no Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Introdução ao OpenShift Container Platform](https://docs.openshift.com)
- 
