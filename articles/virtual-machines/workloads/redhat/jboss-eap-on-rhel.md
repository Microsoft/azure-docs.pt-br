---
title: Início Rápido – Implantar o JBoss EAP (Enterprise Application Platform) no RHEL (Red Hat Enterprise Linux) para VMs e conjuntos de dimensionamento de máquinas virtuais do Azure
description: Como implantar aplicativos Java empresariais usando o JBoss EAP da Red Hat nas VMs e nos conjuntos de dimensionamento de máquinas virtuais RHEL do Azure.
author: theresa-nguyen
ms.author: bicnguy
ms.date: 10/30/2020
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom:
- mode-api
ms.collection: linux
ms.openlocfilehash: 18cf093adad858f50b2b1fa1c97e38821bd1c949
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538678"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Implantar aplicativos Java empresariais no Azure com o EAP do JBoss no Red Hat Enterprise Linux

Os modelos de Início Rápido do Azure deste artigo mostram como implantar o [JBoss EAP (Enterprise Application Platform)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) com o [RHEL (Red Hat Enterprise Linux)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) para VMs (máquinas virtuais) e conjuntos de dimensionamento de máquinas virtuais do Azure. Você usará um aplicativo Java de exemplo para validar a implantação. 

O JBoss EAP é uma plataforma de servidor de aplicativos de software livre. Ele fornece segurança, escalabilidade e desempenho de nível empresarial para seus aplicativos Java. O RHEL é uma plataforma de SO (sistema operacional) de software livre. Ele permite a escala de aplicativos existentes e a distribuição de tecnologias emergentes em todos os ambientes. 

O JBoss EAP e o RHEL incluem tudo o que você precisa para criar, executar, implantar e gerenciar aplicativos Java empresariais em qualquer ambiente. A combinação é uma solução de software livre para ambientes virtuais, locais e em nuvens privadas, públicas ou híbridas.

## <a name="prerequisites"></a>Pré-requisitos 

* Uma conta do Azure com uma assinatura ativa. Para obter uma assinatura do Azure, ative seus [créditos do Azure para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou [crie uma conta gratuitamente](https://azure.microsoft.com/pricing/free-trial).

* Instalação do JBoss EAP. Você precisa ter uma conta do Red Hat com direito do RHSM (Red Hat Subscription Management) para o JBoss EAP. Esse direito permitirá que você baixe a versão do JBoss EAP testada e certificada pela Red Hat.  

  Se você não tiver um direito do EAP, obtenha uma [assinatura de avaliação do JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) antes de começar. Para criar uma assinatura do Red Hat, acesse [portal do cliente do Red Hat](https://access.redhat.com/) e configure uma conta.

* O [CLI do Azure](/cli/azure/overview).

* Opções do RHEL. Escolha entre PAYG (pago conforme o uso) ou BYOS (traga a própria assinatura). Com o BYOS, será necessário ativar a Imagem Gold do RHEL do [Red Hat Cloud Access](https://access.redhat.com/) antes de implantar o modelo de início rápido.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Migração de aplicativos Java EE e Jakarta EE

### <a name="migrate-to-jboss-eap"></a>Fazer a migração para o JBoss EAP
O JBoss EAP 7.2 e 7.3 são implementações certificadas do Java EE (Edição Enterprise) 8 e especificações do Jacarta EE 8. O JBoss EAP fornece opções pré-configuradas para recursos como clustering de HA (alta disponibilidade), mensagens e cache distribuído. Ele também permite que os usuários gravem, implantem e executem aplicativos usando as várias APIs e os vários serviços que o JBoss EAP fornece.  

Para obter mais informações sobre o JBoss EAP, confira [Introdução ao JBoss EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) ou [Introdução ao JBoss EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Aplicativos no JBoss EAP

* **Aplicativos de serviços Web**. Os serviços Web fornecem uma forma padrão de interoperar entre aplicativos de software. Cada aplicativo pode ser executado em diferentes plataformas e estruturas. Esses serviços Web facilitam a comunicação interna e heterogênea do subsistema. 

  Para saber mais, confira [Desenvolver Aplicativos de Serviços Web no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) ou [Desenvolver Aplicativos de Serviços Web no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* **Aplicativos EJB (Enterprise Java Beans)** . O EJB 3.2 é uma API para desenvolver aplicativos Java EE e Jacarta EE distribuídos, transacionais, seguros e portáteis. O EJB usa componentes do lado do servidor chamados de Enterprise Beans para implementar a lógica de negócios de um aplicativo de uma forma dissociada, que incentiva a reutilização. 

  Para saber mais, confira [Desenvolvimento de Aplicativos EJB no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) ou [Desenvolvimento de Aplicativos EJB no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* **Aplicativos Hibernate**. Desenvolvedores e administradores podem desenvolver e implantar a JPA (Java Persistence API) e aplicativos Hibernate com o JBoss EAP. O Hibernate Core é uma estrutura de mapeamento relacional de objeto para a linguagem Java. Ele fornece uma estrutura para mapear um modelo de domínio orientado a objeto para um banco de dados relacional, a fim de que os aplicativos evitem a interação direta com o banco de dados. 

  O Hibernate Entity Manager implementa interfaces de programação e regras de ciclo de vida conforme definido pela [especificação JPA 2.1](https://www.jcp.org/en/jsr/overview). Junto com o Hibernate Annotations, esse wrapper implementa uma solução JPA completa (e autônoma) além do Hibernate Core maduro. 
  
  Para saber mais sobre o Hibernate, confira [JPA no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) ou [Jacarta Persistence no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications"></a>Kit de Ferramentas de Migração para Aplicativos do Red Hat
O [MTA (Kit de Ferramentas para Aplicativos) do Red Hat](https://developers.redhat.com/products/mta/overview) é uma ferramenta de migração para servidores de aplicativos Java. Use essa ferramenta para fazer a migração de outro servidor de aplicativos para o JBoss EAP. Ele funciona com plug-ins de IDE para o [IDE do Eclipse](https://www.eclipse.org/ide/), [Workspaces CodeReady do Red Hat](https://developers.redhat.com/products/codeready-workspaces/overview) e o [Visual Studio Code](https://code.visualstudio.com/docs/languages/java) para Java. 

O MTA é uma ferramenta de software livre gratuita que:
* Automatiza a análise de aplicativos.
* Dá suporte à estimativa de esforços.
* Acelera a migração de código.
* Dá suporte à conteinerização.
* Integra-se ao Azure Workload Builder.

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrar o JBoss EAP do local para o Azure
A oferta do Azure Marketplace do JBoss EAP no RHEL será instalada e provisionada em VMs do Azure em menos de 20 minutos. Você pode acessar essas ofertas no [Azure Marketplace](https://azuremarketplace.microsoft.com/).

Essa oferta do Azure Marketplace inclui várias combinações de versões do EAP e do RHEL para dar suporte aos requisitos exigidos. O JBoss EAP é sempre BYOS, mas, para o sistema operacional do RHEL, você pode escolher entre BYOS ou PAYG. A oferta do Azure Marketplace inclui opções de plano para o JBoss EAP no RHEL como VMs autônomas ou clusterizadas:

* JBoss EAP 7.2 na VM do RHEL 7.7 (PAYG)
* JBoss EAP 7.2 na VM do RHEL 8.0 (PAYG)
* JBoss EAP 7.3 na VM do RHEL 8.0 (PAYG)
* JBoss EAP 7.2 na VM do RHEL 7.7 (BYOS)
* JBoss EAP 7.2 na VM do RHEL 8.0 (BYOS)
* JBoss EAP 7.3 na VM do RHEL 8.0 (BYOS)

Juntamente com as ofertas do Azure Marketplace, você pode usar os modelos de Início Rápido para começar o percurso de migração para o Azure. Esses Inícios Rápidos incluem os scripts e os modelos do ARM (Azure Resource Manager) predefinidos para implantar o JBoss EAP no RHEL com várias configurações e combinações de versão. Você terá:

* Um balanceador de carga.
* Um IP privado para balanceamento de carga e VMs.
* Uma rede virtual com apenas uma sub-rede.
* Configuração da VM (em cluster ou autônoma).
* Um aplicativo Java de exemplo.

A arquitetura da solução para esses modelos inclui:

* JBoss EAP em uma VM autônoma do RHEL.
* JBoss EAP clusterizado em várias VMs do RHEL.
* JBoss EAP clusterizado por meio de conjuntos de dimensionamento de máquinas virtuais do Azure.

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migração de Carga de Trabalho do Linux para o JBoss EAP
O Azure Workload Builder simplifica o processo de prova de conceito, avaliação e migração de aplicativos Java locais para o Azure. O Workload Builder se integra à ferramenta de Descoberta de Migrações para Azure a fim de identificar os servidores do JBoss EAP. Em seguida, ele gera dinamicamente um guia estratégico do Ansible para a implantação do servidor do JBoss EAP. Ele usa a ferramenta MTA do Red Hat para migrar servidores de outros servidores de aplicativos para o JBoss EAP. 

As etapas para simplificar a migração incluem:
1. **Avaliação**. Avalie os clusters do JBoss EAP usando uma VM ou um conjunto de dimensionamento de máquinas virtuais do Azure.
1. **Avaliação**. Examine os aplicativos e a infraestrutura.
1. **Configuração da infraestrutura**. Crie um perfil de carga de trabalho.
1. **Implantação e teste**. Implante, migre e teste a carga de trabalho.
1. **Configuração pós-implantação**. Integre por meio de dados, monitoramento, segurança, backup e muito mais.

## <a name="server-configuration-choice"></a>Opção de configuração do servidor

Para a implantação da VM do RHEL, você pode escolher entre PAYG ou BYOS. As imagens do [Azure Marketplace](https://azuremarketplace.microsoft.com) adotam como padrão o PAYG. Implante uma VM do RHEL do tipo BYOS se você tiver uma imagem própria do sistema operacional do RHEL. Verifique se a conta do RHSM tem direito de BYOS por meio do Cloud Access antes de implantar a VM ou o conjunto de dimensionamento de máquinas virtuais.

O JBoss EAP tem funcionalidades de gerenciamento avançadas e fornece funcionalidades e APIs aos aplicativos dele. Essas funcionalidades de gerenciamento variam de acordo com o modo operacional usado para iniciar o JBoss EAP. Ele é compatível com o RHEL e o Windows Server. O JBoss EAP oferece um modo de operação com servidor autônomo para gerenciar instâncias discretas. Ele também oferece um modo operacional de domínio gerenciado para gerenciar grupos de instâncias de um ponto de controle. 

> [!NOTE]
> Os domínios gerenciados pelo JBoss EAP não são compatíveis com o Microsoft Azure, pois os serviços de infraestrutura do Azure gerenciam o recurso de HA. 

A variável de ambiente `EAP_HOME` indica o caminho para a instalação do JBoss EAP. Use o seguinte comando para iniciar o serviço do JBoss EAP em modo autônomo:

```
$EAP_HOME/bin/standalone.sh
```
    
Esse script de inicialização usa o arquivo EAP_HOME/bin/standalone.conf para definir algumas preferências padrão, como opções de JVM. Você pode personalizar as configurações neste arquivo. O JBoss EAP usa o arquivo de configuração standalone.xml para inicializar em modo autônomo por padrão, mas você pode usar um modo diferente para iniciá-lo. 

Para obter detalhes sobre os arquivos de configuração autônomos disponíveis e como usá-los, confira [Arquivos de Configuração de Servidor Autônomo para EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) ou [Arquivos de Configuração de Servidor Autônomo para EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files). 

Para iniciar o JBoss EAP com uma configuração diferente, use o argumento `--server-config`. Por exemplo:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Para obter uma lista completa de todos os argumentos de script de inicialização disponíveis e suas finalidades, use o argumento `--help`. Para obter mais informações, confira [Argumentos de Runtime do Servidor no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) ou [Argumentos de Runtime do Servidor no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime).
    
O JBoss EAP também pode funcionar em modo de cluster. As mensagens de cluster do JBoss EAP permitem o agrupamento de servidores de mensagens do JBoss EAP para compartilhar a carga de processamento de mensagens. Cada nó ativo no cluster é um servidor de mensagens do JBoss EAP ativo, que gerencia as próprias mensagens e manipula as próprias conexões. Para saber mais, confira [Visão Geral de Clusters no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) ou [Visão Geral de Clusters no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview). 

## <a name="support-and-subscription-notes"></a>Notas de suporte e assinatura
Esses modelos de Início Rápido são oferecidos da seguinte maneira: 

- O sistema operacional do RHEL é oferecido como PAYG ou BYOS por meio do modelo de Imagem Gold do Red Hat.
- O JBoss EAP é oferecido somente como BYOS.

#### <a name="using-rhel-os-with-the-payg-model"></a>Usando o sistema operacional do RHEL com o modelo PAYG

Por padrão, esses modelos de Início Rápido usam a imagem PAYG do RHEL 7.7 ou 8.0 Sob Demanda do Azure Marketplace. As imagens PAYG têm um custo adicional por hora na assinatura do RHEL, além dos custos normais de computação, rede e armazenamento. Ao mesmo tempo, a instância é registrada na sua assinatura do Red Hat. Isso significa que você usará um de seus direitos. 

Essa imagem PAYG levará à "dupla cobrança". Você pode evitar esse problema criando a própria imagem do RHEL. Para saber mais, leia o artigo da base de dados de conhecimento do Red Hat intitulado [Como provisionar uma VM do RHEL para o Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Ou ative sua imagem gold do RHEL do [Red Hat Cloud Access](https://access.redhat.com/).

Para obter detalhes sobre os preços da VM PAYG, confira os [preços do Red Hat Enterprise Linux](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). Para usar o RHEL no modelo PAYG, você precisará de uma Assinatura do Azure com a forma de pagamento especificada para o [RHEL 7.7 no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) ou o [RHEL 8.0 no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Essas ofertas exigem que uma forma de pagamento seja especificada na assinatura do Azure.

#### <a name="using-rhel-os-with-the-byos-model"></a>Usando o sistema operacional do RHEL com o modelo BYOS

Para usar o BYOS para o RHEL OS, você precisa ter uma assinatura do Red Hat válida com direitos para usar o RHEL OS no Azure. Cumpra os seguintes pré-requisitos antes de implantar o sistema operacional do RHEL com o modelo BYOS:

1. Verifique se você tem os direitos do sistema operacional do RHEL e do JBoss EAP anexados à assinatura do Red Hat.
2. Autorize sua ID da assinatura do Azure para usar imagens do RHEL BYOS. Siga a [documentação do Red Hat Subscription Management](https://access.redhat.com/documentation/red_hat_subscription_management/1/) para concluir o processo, que inclui estas etapas:

   1. Habilitar o Microsoft Azure como um provedor no Painel do Red Hat Cloud Access.

   1. Adicionar as IDs de assinatura do Azure.

   1. Habilitar novos produtos para o Cloud Access no Microsoft Azure.
    
   1. Ativar as Imagens Gold do Red Hat para a assinatura do Azure. Para obter mais informações, confira as [Imagens Gold do Red Hat no Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access).

   1. Aguarde até que as Imagens Gold do Red Hat estejam disponíveis na assinatura do Azure. Essas imagens normalmente são disponibilizadas em até três horas após o envio.
    
3. Aceite os termos e condições do Azure Marketplace para imagens do RHEL BYOS. Você pode concluir esse processo executando os comandos a seguir da CLI do Azure. Para obter mais informações, confira a documentação das [Imagens Gold do RHEL BYOS no Azure](./byos.md). É importante que você esteja executando a versão mais recente da CLI do Azure.

   1. Abra uma sessão da CLI do Azure e faça a autenticação com a conta do Azure. Para obter assistência, confira [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli).

   1. Verifique se as imagens do RHEL BYOS estão disponíveis na assinatura executando o comando a seguir da CLI. Se você não obtiver nenhum resultado aqui, verifique se a assinatura do Azure está ativada para imagens do RHEL BYOS.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. Execute o seguinte comando para aceitar os termos do Azure Marketplace para RHEL 7.7 BYOS e RHEL 8.0 BYOS, respectivamente:
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
Sua assinatura agora está pronta para implantar o RHEL 7.7 ou 8.0 BYOS em máquinas virtuais do Azure.

#### <a name="using-jboss-eap-with-the-byos-model"></a>Usando o JBoss EAP com o modelo BYOS

O JBoss EAP está disponível no Azure somente por meio do modelo BYOS. Ao implantar esse modelo, você precisa fornecer suas credenciais do RHSM juntamente com a ID de Pool do RHSM com direitos válidos do EAP. Se você não tiver direitos do EAP, obtenha uma [assinatura de avaliação do JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) antes de começar.

## <a name="deployment-options"></a>Opções de implantação

Você pode implantar o modelo das seguintes maneiras:

- **PowerShell**. Implante o modelo executando os seguintes comandos: 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Para obter informações sobre como instalar e configurar o Azure PowerShell, confira a [documentação do PowerShell](/powershell/azure/).  

- **CLI do Azure**. Implante o modelo executando os seguintes comandos:

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az deployment group create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Para obter detalhes sobre como instalar e configurar a CLI do Azure, confira [Instalar a CLI](/cli/azure/install-azure-cli).

- **Portal do Azure**. Você pode implantar no portal do Azure acessando os modelos de Início Rápido do Azure, conforme descrito na próxima seção. Quando estiver no Início Rápido, selecione **Implantar no Azure** ou o botão **Procurar no GitHub**.

## <a name="azure-quickstart-templates"></a>Modelos de início rápido do Azure

Você pode começar usando um dos seguintes modelos de Início Rápido para JBoss EAP no RHEL que atenda à meta de implantação:

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP no RHEL (VM autônoma)</a>. Isso implantará um aplicativo Web chamado JBoss-EAP no Azure para JBoss EAP 7.2 ou 7.3 em execução na VM do RHEL 7.7 ou 8.0.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP no RHEL (várias VMs clusterizadas)</a>. Isso implantará um aplicativo Web chamado eap-session-replication em um cluster do JBoss EAP 7.2 ou 7.3 em execução em um número *n* de VMs do RHEL 7.7 ou 8.0. O usuário decide o valor de *n*. Todas as VMs são adicionadas ao pool de back-end de um balanceador de carga.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP no RHEL (conjunto de dimensionamento de máquinas virtuais clusterizadas)</a>. Isso implantará um aplicativo Web chamado eap-session-replication em um cluster do JBoss EAP 7.2 ou 7.3 em execução em conjuntos de dimensionamento de máquinas virtuais do RHEL 7.7 ou 8.0.

## <a name="resource-links"></a>Link de recursos

* [Benefício Híbrido do Azure](../../windows/hybrid-use-benefit-licensing.md)
* [Configurar um aplicativo Java para o Serviço de Aplicativo do Azure](../../../app-service/configure-language-java.md)
* [JBoss EAP no Red Hat OpenShift no Azure](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP no Serviço de Aplicativo do Azure para Linux](../../../app-service/quickstart-java.md)
* [Implantar o JBoss EAP no Serviço de Aplicativo do Azure](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/).
* Saiba mais sobre o [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/).
* Saiba mais sobre o [Red Hat Subscription Management](https://access.redhat.com/products/red-hat-subscription-management).
* Saiba mais sobre as [cargas de trabalho do Red Hat no Azure](./overview.md).
* Implantar o [JBoss EAP em uma VM ou em um conjunto de dimensionamento de máquinas virtuais do RHEL por meio do Azure Marketplace](https://aka.ms/AMP-JBoss-EAP).
* Implantar o [JBoss EAP em uma VM ou em um conjunto de dimensionamento de máquinas virtuais do RHEL por meio de modelos de Início Rápido do Azure](https://aka.ms/Quickstart-JBoss-EAP).
