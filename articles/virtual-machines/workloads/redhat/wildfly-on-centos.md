---
title: Guia de Início Rápido – WildFly no CentOS
description: Implantar aplicativos Java em WildFly na VM do CentOS
author: Theresa-Nguyen
ms.author: bicnguy
ms.date: 10/23/2020
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom:
- mode-api
ms.collection: linux
ms.openlocfilehash: bad9331a6d9a0c0936b1458e3aa4a84559e4a107
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533562"
---
# <a name="quickstart-wildfly-on-centos-8"></a>Início Rápido: WildFly no CentOS 8

Este guia de início rápido mostra como implantar o nó autônomo de WildFly da VM do CentOS 8. Ele é ideal para desenvolvimento e teste de aplicativos Java empresariais no Azure. A assinatura do servidor de aplicativos não é necessária para implantar este guia de início rápido.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. Se não tiver uma assinatura do Azure, você poderá ativar os [benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou [criar uma conta gratuita](https://azure.microsoft.com/pricing/free-trial).

## <a name="use-case"></a>Caso de uso

O WildFly é ideal para desenvolvimento e teste de aplicativos Java empresariais no Azure. As listas de tecnologias disponíveis nos perfis de configuração de servidor WildFly 18 estão disponíveis no [Guia de introdução do WildFly](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly).

Você pode usar o WildFly no modo Autônomo ou de Cluster de acordo com o seu caso de uso. Você pode garantir a alta disponibilidade de aplicativos Jakarta EE críticos por WildFly em um cluster de nós. Faça um pequeno número de alterações na configuração de aplicativo e implante o aplicativo no cluster. Para saber mais sobre isso, confira o [Guia de alta disponibilidade do WildFly](https://docs.wildfly.org/18/High_Availability_Guide.html).

## <a name="configuration-choice"></a>Opção de configuração

O WildFly pode ser inicializado no modo de **Servidor Autônomo** – Uma instância de servidor autônomo é um processo independente, assim como uma instância do AS (JBoss Application Server) 3, 4, 5 ou 6. As instâncias autônomas podem ser iniciadas por meio dos scripts de inicialização standalone.sh ou standalone.bat. Para mais de uma instância autônoma, é responsabilidade do usuário coordenar o gerenciamento de vários servidores entre os servidores.

Você também pode iniciar a instância do WildFly com a configuração alternativa usando os arquivos de configuração disponíveis na pasta de configuração.

Veja abaixo os arquivos de Configuração do Servidor Autônomo:

- standalone.xml (padrão) – Essa configuração é o arquivo padrão usado para iniciar a instância de WildFly. Ela contém a configuração certificada pelo Jakarta Web Profile com as tecnologias necessárias.
   
- standalone-ha.xml – A configuração certificada do Jakarta EE Web Profile 8 com alta disponibilidade (direcionada a aplicativos Web).
   
- standalone-full.xml – A configuração certificada do Jakarta EE Platform 8, incluindo todas as tecnologias necessárias para hospedar aplicativos Jakarta EE.

- standalone-full-ha.xml – A configuração certificada do Jakarta EE Platform 8 com alta disponibilidade para hospedar aplicativos Jakarta EE.

Para iniciar o servidor WildFly autônomo com outra configuração fornecida, use o argumento --server-config com o arquivo server-config.

Por exemplo, para usar o Jakarta EE Platform 8 com funcionalidades de clustering, use o seguinte comando:

```
./standalone.sh --server-config=standalone-full-ha.xml
```

Para saber mais sobre as configurações, confira o [Guia de introdução do WildFly](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations).

## <a name="licensing-support-and-subscription-notes"></a>Notas de licenciamento, suporte e assinatura

A imagem do Azure CentOS 8 é uma imagem de VM de PAYG (Pagamento Conforme o Uso) e não exige que o usuário obtenha uma licença. Na primeira vez que a VM for iniciada, a licença do sistema operacional da VM será ativada automaticamente e será cobrada uma taxa por hora. Isso é adicional às taxas de VM por hora do Linux da Microsoft. Clique em [Preço da VM do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux) para obter detalhes. O WildFly é gratuito para download e uso e não requer uma assinatura ou licença do Red Hat.

## <a name="how-to-consume"></a>Como consumir

Você pode implantar o modelo das seguintes três maneiras:

- Use o PowerShell – Implantar o modelo executando os seguintes comandos: (Confira [Azure PowerShell](/powershell/azure/) para obter informações sobre como instalar e configurar o Azure PowerShell.)

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- Use a CLI do Azure – Implante o modelo executando os seguintes comandos: (Confira [Linha de Comando Multiplataforma do Azure](/cli/azure/install-azure-cli) para obter detalhes sobre como instalar e configurar a Interface de Linha de Comando Multiplataforma do Azure).

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```

- Use o portal do Azure – Implante o modelo clicando <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">aqui</a> e faça logon no seu portal do Azure.

## <a name="arm-template"></a>Modelo de ARM

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank"> WildFly 18 no CentOS 8 (VM autônoma)</a> – Esse é um modelo de início rápido que cria um nó autônomo de WildFly 18.0.1. Final na VM do CentOS 8 no seu RG (Grupo de Recursos), que inclui um IP privado para a VM, Rede Virtual e Conta de Armazenamento de Diagnóstico. Ele também implanta um aplicativo Java de exemplo chamado JBoss-EAP no Azure no WildFly.

## <a name="resource-links"></a>Link de recursos

* Saiba mais sobre o [WildFly 18](https://docs.wildfly.org/18/)
* Saiba mais sobre as [Distribuições do Linux no Azure](../../linux/endorsed-distros.md)
* [Documentação dos desenvolvedores do Azure para Java](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Próximas etapas

Para o ambiente de produção, confira os modelos do ARM de Início Rápido do Azure do Red Hat JBoss EAP:

Máquina virtual RHEL autônoma com aplicativo de exemplo:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> JBoss EAP no RHEL (VM autônoma)</a>

Máquinas virtuais RHEL clusterizadas com o aplicativo de exemplo:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> JBoss EAP no RHEL (VMs clusterizadas)</a>

Conjunto de dimensionamento de máquinas virtuais RHEL clusterizadas com o aplicativo de exemplo:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> JBoss EAP no RHEL (conjunto de dimensionamento de máquinas virtuais clusterizadas)</a>
