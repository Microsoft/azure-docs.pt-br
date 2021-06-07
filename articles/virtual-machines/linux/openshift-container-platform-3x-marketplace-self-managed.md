---
title: Implantar a oferta autogerenciada do OpenShift Container Platform 3.11 do Marketplace no Azure
description: Implantar a oferta autogerenciada do OpenShift Container Platform 3.11 do Marketplace no Azure.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670443"
---
# <a name="configure-prerequisites"></a>Configurar pré-requisitos

Antes de usar a oferta do Marketplace para implantar um cluster autogerenciado do OpenShift Container Platform 3.11 no Azure, alguns pré-requisitos precisam ser configurados.  Leia o artigo [Pré-requisitos do OpenShift](./openshift-container-platform-3x-prerequisites.md) para obter instruções para criar uma chave SSH (sem uma frase secreta), um cofre de chaves do Azure, um segredo do cofre de chaves e uma entidade de serviço.

 
## <a name="deploy-using-the-marketplace-offer"></a>Implantar usando a oferta do Marketplace

A maneira mais simples de implantar um cluster autogerenciado do OpenShift Container Platform 3.11 no Azure é usar a [oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

Essa opção é a mais simples, mas também tem recursos de personalização limitados. A oferta do Marketplace implanta o OpenShift Container Platform 3.11.82 e inclui as seguintes opções de configuração:

- **Nós mestres**: tipo de instância de nós mestres três (3) com configurável.
- **Nós mestres**: tipo de instância de nós mestres três (3) com configurável.
- **Nós**: o número de nós (entre 1 e 9) e o tipo de instância são configuráveis.
- **Tipo de Disco**: Discos gerenciados são usados.
- **Rede**: suporte para rede nova ou existente, bem como intervalo CIDR personalizado.
- **CNS**: CNS pode ser habilitado.
- **Métricas**: métricas de Hawkular podem ser habilitadas.
- **Registro em log**: registro em log de EFK pode ser habilitado.
- **Provedor de Nuvem do Azure**: habilitado por padrão, pode ser desabilitado.

No canto superior esquerdo do portal do Azure, clique em **Criar um recurso**, insira 'openshift container platform' na caixa de pesquisa e pressione Enter.

   ![Pesquisa de novos recursos](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

A página de Resultados será aberta com o **Red Hat OpenShift Container Platform 3.11 Self-Managed** na lista. 

   ![Resultado da pesquisa de novos recursos](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Clique na oferta para ver os detalhes dela. Para implantar a oferta, clique em **Criar**. A interface do usuário para inserir os parâmetros necessários será exibida. A primeira tela é a folha **Básico**.

   ![Página de título da oferta](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Noções básicas**

Para obter ajuda com qualquer parâmetro de entrada, passe o mouse sobre o ***i*** ao lado do nome do parâmetro.

Insira valores para os parâmetros de entrada e clique em **OK**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome do Usuário Administrador da VM | O usuário administrador a ser criado em todas as instâncias de VM |
| Chave Pública SSH do Usuário Administrador | Chave pública SSH usada para fazer logon na VM – não pode ter uma frase secreta |
| Subscription | Assinatura do Azure na qual implantar o cluster |
| Grupo de recursos | Crie um grupo de recursos ou selecione qualquer grupo de recursos existente para os recursos de cluster |
| Localização | Região do Azure na qual o cluster será implantado |

   ![Folha Básico da oferta](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Configurações de infraestrutura**

Insira valores para os parâmetros de entrada e clique em **OK**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Prefixo do Nome do Cluster OCP | Prefixo de cluster usado para configurar os nomes de host de todos os nós. Entre 1 e 20 caracteres |
| Tamanho do Nó Mestre | Aceite o tamanho da VM padrão ou clique em **Alterar Tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho de VM apropriado para sua carga de trabalho |
| Tamanho do Nó de Infraestrutura | Aceite o tamanho da VM padrão ou clique em **Alterar Tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho de VM apropriado para sua carga de trabalho |
| Número de Nós do Aplicativo | Aceite o tamanho da VM padrão ou clique em **Alterar Tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho de VM apropriado para sua carga de trabalho |
| Tamanho do Nó do Aplicativo | Aceite o tamanho da VM padrão ou clique em **Alterar Tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho de VM apropriado para sua carga de trabalho |
| Tamanho do Bastion Host | Aceite o tamanho da VM padrão ou clique em **Alterar Tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho de VM apropriado para sua carga de trabalho |
| Rede Virtual Nova ou Existente | Crie uma VNet (padrão) ou use uma existente |
| Escolher Configurações de CIDR Padrão ou personalizar o Intervalo de IP (CIDR) | Aceite os intervalos de CIDR padrão ou selecione **Intervalo de IP Personalizado** e insira informações de CIDR personalizadas.  As configurações padrão criarão uma VNet com o CIDR 10.0.0.0/14, uma sub-rede mestre com 10.1.0.0/16, uma sub-rede de infraestrutura com 10.2.0.0/16 e uma sub-rede de computação e CNS com 10.3.0.0/16 |
| Nome do Grupo de Recursos do Key Vault | O nome do Grupo de Recursos que contém o cofre de chaves |
| Nome do cofre de chaves | O nome do cofre de chaves que contém o segredo com a chave privada SSH.  Somente caracteres alfanuméricos e traços são permitidos, com 3 a 24 caracteres |
| Nome do Segredo | O nome do segredo que contém a chave privada SSH.  São permitidos somente caracteres alfanuméricos e traços |

   ![Folha de infraestrutura da oferta](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Alterar o tamanho**

Para selecionar um tamanho de VM diferente, clique em ***Alterar o tamanho** _.  A janela seleção da VM será aberta.  Selecione o tamanho de VM que deseja e clique em _*Selecionar**.

   ![Selecionar o tamanho da VM](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Rede Virtual Existente**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome da Rede Virtual Existente | Nome da VNet existente |
| Nome da sub-rede para os nós mestres | Nome da sub-rede existente para os nós mestres.  Precisa conter pelo menos 16 endereços IP e seguir o RFC 1918 |
| Nome da sub-rede para nós de infraestrutura | Nome da sub-rede existente para os nós de infraestrutura.  Precisa conter pelo menos 32 endereços IP e seguir o RFC 1918 |
| Nome da sub-rede para nós de computação e de CNS | Nome da sub-rede existente para nós de computação e de CNS.  Precisa conter pelo menos 32 endereços IP e seguir o RFC 1918 |
| Grupo de Recursos da Rede Virtual existente | Nome do grupo de recursos que contém a VNet existente |

   ![VNet existente da infraestrutura da oferta](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Intervalo de IP Personalizado**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Intervalo de Endereços para a Rede Virtual | CIDR personalizado para a VNet |
| Intervalo de Endereços da sub-rede que contém os nós mestres | CIDR personalizado para a sub-rede mestre |
| Intervalo de Endereços da sub-rede que contém os nós de infraestrutura | CIDR personalizado para sub-rede de infraestrutura |
| Intervalo de Endereços para a sub-rede que contém os nós de computação e de CNS | CIDR personalizado para os nós de computação e de CNS |

   ![Intervalo de IP personalizado da infraestrutura da oferta](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Insira valores para os parâmetros de entrada e clique em **OK**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Senha do Usuário Administrador do OpenShift | Senha do usuário do OpenShift inicial.  Esse usuário também será o administrador do cluster |
| Confirmar Senha do Usuário Administrador do OpenShift | Digite novamente a senha do usuário administrador do OpenShift |
| Nome de Usuário do Gerenciador de Assinaturas do Red Hat | Nome de usuário para acessar sua assinatura ou ID da organização do Red Hat.  Essa credencial é usada para registrar a instância de RHEL em sua assinatura e não será armazenada pela Microsoft nem pelo Red Hat |
| Senha do Usuário do Gerenciador de Assinaturas do Red Hat | Senha para acessar sua assinatura ou chave de ativação do Red Hat.  Essa credencial é usada para registrar a instância de RHEL em sua assinatura e não será armazenada pela Microsoft nem pelo Red Hat |
| ID do Pool do OpenShift do Gerenciador de Assinaturas do Red Hat | ID do Pool que contém o direito do OpenShift Container Platform. Verifique se você tem direitos suficientes do OpenShift Container Platform para instalação do cluster |
| ID do Pool do OpenShift do Gerenciador de Assinaturas do Red Hat para Nós de Agente/Mestre | ID do Pool que contém direitos do OpenShift Container Platform para nós de agente/mestre. Verifique se você tem direitos suficientes do OpenShift Container Platform para instalação do cluster. Se não estiver usando a ID do pool do agente/mestre, insira a ID do pool dos Nós de Aplicativo |
| Configurar o Provedor de Nuvem do Azure | Configure o OpenShift para usar o Provedor de Nuvem do Azure. Necessário para usar a anexação de disco do Azure para volumes persistentes.  O padrão é Sim |
| GUID da ID do Cliente da Entidade de Serviço do Azure AD | GUID da ID do cliente da entidade de serviço do Azure AD – também conhecido como AppID. Necessário somente se Configurar o Provedor de Nuvem do Azure estiver definido como **Sim** |
| Segredo da ID do Cliente da Entidade de Serviço do Azure AD | Segredo da ID do cliente da entidade de serviço do Azure AD. Necessário somente se Configurar o Provedor de Nuvem do Azure estiver definido como **Sim** |
 
   ![Folha do OpenShift da oferta](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Configurações adicionais**

A folha Configurações Adicionais permite a configuração do CNS para o armazenamento GlusterFS, registro em log, métricas e subdomínio do roteador.  O padrão é não instalar nenhuma dessas opções e usar nip.io como o subdomínio do roteador para fins de teste. Habilitar o CNS instalará três nós de computação adicionais com três discos anexados adicionais que hospedarão os pods do GlusterFS.  

Insira valores para os parâmetros de entrada e clique em **OK**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Configurar o CNS (Armazenamento Nativo de Contêiner) | Instala o CNS no cluster do OpenShift e o habilita como armazenamento. Será padrão se o Provedor do Azure estiver desabilitado |
| Configurar o Log do Cluster | Instala a funcionalidade de registro em log do EFK no cluster.  Dimensione os nós da infraestrutura de maneira adequada para hospedar pods do EFK |
| Configurar Métricas para o Cluster | Instala métricas do Hawkular no cluster do OpenShift.  Dimensione os nós da infraestrutura de maneira adequada para hospedar pods de métricas do Hawkular |
| Subdomínio do Roteador Padrão | Selecione nipio para teste ou personalizado para inserir seu subdomínio para produção |
 
   ![Folha adicional da oferta](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Configurações adicionais – Parâmetros extra**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Tamanho do Nó (CNS) | Aceite o tamanho do nó padrão ou selecione **Alterar tamanho** para selecionar um novo tamanho de VM |
| Insira seu subdomínio personalizado | O domínio de roteamento personalizado a ser usado para expor aplicativos por meio do roteador no cluster do OpenShift.  Crie a entrada de DNS de curinga apropriada] |
 
   ![Instalação de CNS adicional da oferta](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Resumo**

A validação ocorre nesta fase para verificar se a cota do núcleo é suficiente para implantar o número total de VMs selecionadas para o cluster.  Examine todos os parâmetros inseridos.  Se as entradas forem aceitáveis, clique em **OK** para continuar.

   ![Folha de resumo da oferta](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Comprar**

Confirme as informações de contato na página Comprar e clique em **Comprar** para aceitar os termos de uso e iniciar a implantação do cluster do OpenShift Container Platform.

   ![Folha de compra da oferta](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Conectar-se ao cluster OpenShift

Quando a implantação for concluída, recupere a conexão da seção de saída da implantação. Conecte-se ao console do OpenShift com o navegador usando a **URL do console do OpenShift**. Você também pode usar SSH para acessar o bastion host. A seguir está um exemplo em que o nome de usuário do administrador é clusteradmin e o IP público de bastiões FQDN do DNS é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

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
