---
title: Implantar o OpenShift Container Platform Marketplace autogerenciada oferta no Azure | Microsoft Docs
description: Implante o OpenShift Container Platform Marketplace autogerenciada oferta no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 1228c770799de37c85b8a48b1dc923ac8294eeca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773393"
---
# <a name="configure-prerequisites"></a>Configurar pré-requisitos

Antes de usar a oferta do Marketplace para implantar um cluster autogerenciado do OpenShift Container Platform no Azure, alguns pré-requisitos que devem ser configurados.  Leia as [pré-requisitos do OpenShift](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-prerequisites) artigo para obter instruções para criar um ssh chave (sem uma frase secreta), do Azure key vault, segredo do Cofre de chaves e uma entidade de serviço.

 
## <a name="deploy-using-the-marketplace-offer"></a>Implantar usando a oferta do Marketplace

A maneira mais simples para implantar um cluster do OpenShift Container Platform gerenciado automaticamente para o Azure é usar o [oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Essa opção é a mais simples, mas ele também tem recursos de personalização limitados. A oferta do Marketplace implanta o OpenShift Container Platform 3.11.82 e inclui as seguintes opções de configuração:

- **Nós mestres**: três (3) nós mestres com tipo de instância configurável.
- **Nós de infraestrutura**: três (3) nós de infraestrutura com tipo de instância configurável.
- **Nós**: O número de nós (entre 1 e 9) e o tipo de instância é configurável.
- **Tipo de disco**: são usados os Managed Disks.
- **Rede**: Suporte para rede nova ou existente e o intervalo CIDR personalizado.
- **CNS**: o CNS pode ser habilitado.
- **Métrica**: as métricas podem ser habilitadas.
- **Registro em log**: o registro em log pode ser habilitado.
- **Azure Cloud Provider**: Habilitado por padrão, pode ser desabilitado.

No canto superior esquerdo do portal do Azure, clique em **criar um recurso**, insira 'openshift container platform' na caixa de pesquisa e pressione Enter.

   ![Nova pesquisa de recursos](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

A página de resultados será aberto com **Red Hat OpenShift Container Platform Self-Managed** na lista. 

   ![Novo resultado de pesquisa de recursos](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Clique na oferta para exibir detalhes da oferta. Para implantar essa oferta, clique em **criar**. Será exibida a interface do usuário para inserir os parâmetros necessários. A primeira tela é o **Noções básicas de** folha.

   ![Página de título da oferta](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Noções básicas**

Para obter ajuda sobre qualquer um dos parâmetros de entrada, passe o mouse sobre o ***eu*** próximo ao nome do parâmetro.

Insira valores para os parâmetros de entrada e clique em **Okey**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome de usuário administrador VM | O usuário de administrador a ser criado em todas as instâncias VM |
| SSH chave pública para o usuário administrador | Chave pública do SSH usada para fazer logon na VM – não deve ter uma frase secreta |
| Assinatura | Assinatura do Azure para implantar o cluster em |
| Grupo de recursos | Criar um novo grupo de recursos ou selecione um grupo de recursos vazio existente para recursos de cluster |
| Local padrão | Região do Azure para implantar o cluster em |

   ![Folha de Noções básicas de oferta](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Configurações de infraestrutura**

Insira valores para os parâmetros de entrada e clique em **Okey**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Prefixo do nome do Cluster OCP | O usuário de administrador a ser criado em todas as instâncias VM |
| Tamanho do nó mestre | Aceite o tamanho da VM padrão ou clique em **alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho da VM apropriado para sua carga de trabalho |
| Tamanho do nó de infraestrutura | Aceite o tamanho da VM padrão ou clique em **alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho da VM apropriado para sua carga de trabalho |
| Número de nós de aplicativo | Aceite o tamanho da VM padrão ou clique em **alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho da VM apropriado para sua carga de trabalho |
| Tamanho do nó do aplicativo | Aceite o tamanho da VM padrão ou clique em **alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho da VM apropriado para sua carga de trabalho |
| Tamanho do Host de bastiões | Aceite o tamanho da VM padrão ou clique em **alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho da VM apropriado para sua carga de trabalho |
| Rede Virtual nova ou existente | Criar uma nova rede virtual (padrão) ou usar uma rede virtual existente |
| Escolha as configurações padrão de CIDR ou personalizar o intervalo de IP (CIDR) | Aceite os intervalos de CIDR padrão ou selecione **intervalo de IP personalizado** e insira as informações personalizadas de CIDR.  As configurações padrão serão criar uma rede virtual com o CIDR de 10.0.0.0/14, sub-rede mestre com 10.1.0.0/16 infraestrutura sub-rede com 10.2.0.0/16 e a sub-rede de computação e cns com 10.3.0.0/16 |
| Nome do grupo de recursos do Cofre de chave | O nome do grupo de recursos que contém o Cofre de chaves |
| Nome do cofre de chaves | O nome do Cofre de chaves que contém o segredo com o ssh chave privada.  Somente caracteres alfanuméricos e traços são permitidos e ter entre 3 e 24 caracteres |
| Nome do segredo | O nome do segredo que contém o ssh chave privada.  Somente caracteres alfanuméricos e traços são permitidos |

   ![Folha de infraestrutura de oferta](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Alterar tamanho**

Para selecionar um tamanho VM diferente, clique em ***alterar o tamanho***.  A janela de seleção da VM será aberta.  Selecione o tamanho VM desejado e clique em **selecionar**.

   ![Selecione o tamanho da VM](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Rede Virtual existente**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome de rede Virtual existente | Nome da rede virtual existente |
| Nome da sub-rede para nós mestres | Nome da sub-rede existente para nós mestres.  Deve conter pelo menos 16 endereços IP e seguem o RFC 1918 |
| Nome da sub-rede de infraestrutura nós | Nome do existente subrede de infraestrutura de nós.  Deve conter pelo menos 32 endereços IP e seguem o RFC 1918 |
| Nome da sub-rede para nós de computação e cns | Nome da sub-rede existente para nós de computação e cns.  Deve conter pelo menos 32 endereços IP e seguem o RFC 1918 |
| Grupo de recursos para a rede Virtual existente | Nome do grupo de recursos que contém a rede virtual existente |

   ![Rede virtual existente da infraestrutura da oferta](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Intervalo IP personalizados**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Intervalo de endereços para a rede Virtual | Personalizado CIDR para a rede virtual |
| Intervalo de endereços da sub-rede que contém os nós mestres | Personalizado CIDR para a sub-rede mestre |
| Intervalo de endereços da sub-rede que contém os nós de infraestrutura | Personalizado CIDR para a sub-rede de infraestrutura |
| Intervalo de endereços de sub-rede que contém os nós de computação e cns | CIDR personalizado para os nós de computação e cns |

   ![Intervalo IP personalizado da infraestrutura da oferta](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform**

Insira valores para os parâmetros de entrada e clique em **Okey**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Senha do usuário administrador do OpenShift | Senha do usuário inicial do OpenShift.  Esse usuário também será o administrador de cluster |
| Confirme a senha do usuário administrador do OpenShift | Digite novamente a senha do administrador do OpenShift |
| Nome de usuário do Red Hat assinatura Manager | Nome de usuário para acessar sua assinatura do Red Hat ou ID da organização.  Essa credencial é usada para registrar a instância RHEL à sua assinatura e não será armazenada pela Microsoft ou Red Hat |
| Senha de usuário do Red Hat assinatura Manager | Senha para acessar sua assinatura do Red Hat ou a chave de ativação.  Essa credencial é usada para registrar a instância RHEL à sua assinatura e não será armazenada pela Microsoft ou Red Hat |
| ID do Pool de OpenShift do Red Hat assinatura Manager | ID do pool que contém o direito de OpenShift Container Platform. Verifique se que você tem suficiente direitos de OpenShift Container Platform para a instalação do cluster |
| ID do Pool de OpenShift do Red Hat assinatura Manager para Broker / mestre nós | Que contém os direitos de OpenShift Container Platform para o agente de ID do pool / nós mestres. Verifique se que você tem suficiente direitos de OpenShift Container Platform para a instalação do cluster. Se não usando o broker / ID do pool do mestre, insira a ID do pool de nós de aplicativo |
| Configurar provedor de nuvem do Azure | Configure o OpenShift para usar o provedor de nuvem do Azure. Necessário se conectar-se usar o disco do Azure para volumes persistentes.  O padrão é Sim |
| GUID da ID de cliente de entidade de serviço do AD do Azure | GUID do Azure AD entidade de serviço cliente ID - também conhecido como AppID. Necessário somente se configurar o provedor de nuvem do Azure definido como **Sim** |
| Segredo de ID do cliente de entidade de serviço do AD do Azure | Segredo da ID do cliente de entidade de serviço do AD do Azure. Necessário somente se configurar o provedor de nuvem do Azure definido como **Sim** |
 
   ![Folha de OpenShift de oferta](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Configurações adicionais**

A folha de configurações adicional permite a configuração dos CNS para o armazenamento de glusterfs, registro em log, métricas e Sub do roteador de domínio.  O padrão não será instalado qualquer uma dessas opções e usará nip.io como o subdomínio do roteador para fins de teste. Habilitar CNS, você instalará três nós de computação adicionais com três discos adicionais anexados que hospedarão os pods glusterfs.  

Insira valores para os parâmetros de entrada e clique em **Okey**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Configurar o contêiner de armazenamento nativo (CNS) | Instala CNS in o OpenShift do cluster e habilitá-lo como o armazenamento. Será o padrão se o provedor do Azure está desabilitado |
| Configurar o log de Cluster | Instala a funcionalidade de registro em log EFK no cluster.  Infraestrutura dimensionar nós adequadamente com os pods EFK do host |
| Configurar as métricas para o Cluster | Instala Hawkular métricas para o cluster do OpenShift.  Infraestrutura dimensionar nós adequadamente para os pods Hawkular métricas de host |
| Subdomínio roteador padrão | Selecione nipio para teste ou personalizado para inserir seu próprio domínio sub para produção |
 
   ![Folha adicional da oferta](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Configurações adicionais - parâmetros extras**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| (CNS) Tamanho do nó | Aceite o tamanho de nó padrão ou selecione **alterar o tamanho** para selecionar um novo tamanho VM |
| Insira o subdomínio personalizado | O domínio de roteamento personalizado a ser usado para expor aplicativos por meio do roteador no cluster do OpenShift.  Certifique-se de criar a entrada DNS curinga apropriado] |
 
   ![Oferecer cns adicionais instalar](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Resumo**

A validação ocorre neste estágio para verificar a cota de núcleo é suficiente para implantar o número total de VMs selecionadas para o cluster.  Examine todos os parâmetros que foram inseridos.  Se as entradas forem aceitáveis, clique em **Okey** para continuar.

   ![Folha de resumo da oferta](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Comprar**

Confirme as informações de contato na página de comprar e clique em **compra** para aceitar os termos de uso e iniciar a implantação do cluster do OpenShift Container Platform.

   ![Folha de compra da oferta](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Conectar-se ao cluster OpenShift

Quando a implantação for concluída, recupere a conexão da seção de saída da implantação. Conectar-se ao console do OpenShift com o navegador usando o **URL do Console OpenShift**. Você também pode SSH para o host de bastiões. A seguir está um exemplo em que o nome de usuário do administrador é clusteradmin e o IP público de bastiões FQDN do DNS é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Próximas etapas

- [Tarefas de pós-implantação](./openshift-post-deployment.md)
- [Solução de problemas de implantação do OpenShift no Azure](./openshift-troubleshooting.md)
- [Introdução ao OpenShift Container Platform](https://docs.openshift.com/container-platform)

