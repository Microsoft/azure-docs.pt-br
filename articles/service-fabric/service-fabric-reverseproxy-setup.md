---
title: Proxy reverso de configuração do Azure Service Fabric | Microsoft Docs
description: Entenda como configurar o proxy reverso do Service Fabric.
services: service-fabric
documentationcenter: na
author: jimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/13/2018
ms.author: chackdan
ms.openlocfilehash: df25c52e7a3f35355eb52bd95a39f55852adfcae
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876587"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Defina e configure o proxy reverso no Azure Service Fabric
O proxy reverso é um serviço opcional no Azure Service Fabric que ajuda microsserviços em execução em um cluster do Service Fabric a descobrir e comunicar-se com outros serviços que têm pontos de extremidade http. Para saber mais, confira [Proxy reverso no Azure Service Fabric](service-fabric-reverseproxy.md). Este artigo mostra como instalar e configurar o proxy reverso no cluster. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Habilitar o proxy reverso por meio do portal do Azure

O portal do Azure fornece uma opção para habilitar o proxy inverso ao criar um novo cluster do Service Fabric. Você não pode atualizar um cluster existente para usar proxy reverso através do portal. 

Para configurar o proxy reverso ao [criar um cluster usando o portal do Azure](./service-fabric-cluster-creation-via-portal.md), certifique-se de fazer o seguinte:

1. Na **Etapa 2: Configuração do Cluster**, em **Configuração do tipo de nó**, selecione **Habilitar proxy reverso**.

   ![Habilitar proxy reverso por meio do portal](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Opcional) Para configurar o proxy reverso seguro, você precisa configurar um certificado SSL. Na **Etapa 3: Segurança**, em **Definir configurações de segurança do cluster**, em **Tipo de configuração**, selecione **Personalizado**. Em seguida, em **Certificado SSL de Proxy reverso**, selecione **Incluir um certificado SSL para proxy reverso** e insira os detalhes do certificado.

   ![Configurar o proxy reverso seguro no portal](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Se você optar por não configurar o proxy reverso com um certificado ao criar o cluster, você pode fazer isso mais tarde por meio do modelo do Gerenciador de Recursos para o grupo de recursos do cluster. Para saber mais, [Habilitar proxy reverso, por meio de modelos do Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Habilitar proxy reverso, por meio de modelos do Azure Resource Manager

Para clusters no Azure, você pode usar o modelo do Azure Resource Manager do Azure para habilitar o proxy reverso no Service Fabric. Você pode habilitar o proxy inverso ao criar o cluster ou habilitá-lo ao atualizar o cluster em um momento posterior. 

Para um novo cluster, você pode [criar um modelo do Gerenciador de Recursos personalizado](service-fabric-cluster-creation-via-arm.md) ou você pode usar um modelo de exemplo. 

Você pode encontrar modelos do Gerenciador de Recursos de exemplo que podem ajudar a configurar o proxy reverso seguro para um cluster do Azure em [Modelos de exemplo de Proxy Reverso Seguro](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample) no GitHub. Consulte [Configurar Proxy Reverso HTTPS em um cluster seguro](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) para o arquivo LEIAME para instruções e exemplos para usar e configurar o proxy reverso seguro com um certifico e tratar a substituição do certificado.

Para um cluster existente, você pode exportar o modelo do Gerenciador de Recursos para o recurso do cluster de grupo usando o [portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell), ou a [CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli).

Depois de ter um modelo do Gerenciador de Recursos, habilite o proxy reverso com as etapas a seguir:

1. Defina uma porta para o proxy inverso na [seção Parâmetros](../azure-resource-manager/resource-group-authoring-templates.md) do modelo.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Especifique a porta para cada um dos objetos nodetype em [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [Seção do tipo de recurso](../azure-resource-manager/resource-group-authoring-templates.md).

    A porta é identificada pelo nome do parâmetro, reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Para configurar certificados SSL na porta de proxy Inverso, adicione o certificado à propriedade ***reverseProxyCertificate*** na **seção tipo de recurso** [Microsoft.ServiceFabric/clusters](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Suporte um certificado de proxy reverso diferente do certificado de cluster
 Se o certificado de proxy reverso é diferente do certificado que protege o cluster, o certificado especificado anteriormente deve ser instalado na máquina virtual e adicionado à ACL (lista de controle de acesso) para que o Service Fabric possa acessá-lo. Isso pode ser feito na [ **seção de tipo de recurso**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) [ Microsoft.Compute/virtualMachineScaleSets](../resource-group-authoring-templates.md). Para instalação, adicione o certificado ao osProfile. A seção de extensão do modelo pode atualizar o certificado na ACL.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Ao usar certificados que são diferentes do certificado do cluster para habilitar o proxy reverso em um cluster existente, instale o certificado de proxy reverso e atualize a ACL no cluster antes de habilitar o proxy reverso. Conclua a implantação do [modelo do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) usando as configurações mencionadas acima antes de iniciar uma implantação para habilitar o proxy reverso usando as etapas 1 a 3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Habilitar o proxy reverso nos clusters autônomos

Para clusters autônomos, você habilita o proxy reverso no arquivo ClusterConfig.json. Você pode habilitar o proxy reverso no momento da criação do cluster ou atualizando a configuração para um cluster existente. Para saber mais sobre as configurações disponíveis nos arquivos ClusterConfig.json, consulte [configurações de cluster autônomo](./service-fabric-cluster-manifest.md).

As etapas a seguir mostram as configurações a serem usadas para habilitar o proxy reverso e, opcionalmente, para o proxy reverso seguro com um certificado X.509. 

1. Para habilitar o proxy reverso, defina o valor **reverseProxyEndpointPort** para o tipo de nó em **propriedades** na configuração do cluster. O JSON a seguir mostra a configuração da porta de ponto de extremidade de proxy reverso para 19081 para nós com um tipo de “NodeType0”:

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. (Opcional) Para um proxy reverso seguro, configure um certificado na seção **segurança** em **propriedades**. 
   - Para um ambiente de desenvolvimento ou teste, você pode usar a configuração **ReverseProxyCertificate**:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - Para um ambiente de produção, a configuração **ReverseProxyCertificateCommonNames** é recomendada:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   Para obter mais informações sobre como configurar e gerenciar certificados para um cluster autônomo, bem como para obter mais detalhes sobre como configurar certificados usados para proteger o proxy reverso, consulte [X509 segurança baseada em certificado](./service-fabric-windows-cluster-x509-security.md).

Depois de modificar as configurações no arquivo ClusterConfig.json, siga as instruções em [Atualizar a configuração do cluster](service-fabric-cluster-config-upgrade-windows-server.md) para aplicar as alterações ao seu cluster.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Expor um proxy reverso em uma porta pública por meio do Azure Load Balancer

Para resolver o proxy inverso de fora do cluster do Azure, configure as regras do Azure Load Balancer e Investigação de Integridade do Azure para a porta do proxy reverso. Essas etapas podem ser executadas usando o portal do Azure ou o modelo do Gerenciador de Recursos a qualquer momento depois de criar o cluster. 

> [!WARNING]
> Quando você configura a porta do proxy reverso no Load Balancer, todos os microsserviços no cluster que expõem um ponto de extremidade HTTP se tornam endereçáveis da parte externa do cluster. Isso significa que os microsserviços destinados a serem internos pode ser descobertos por um determinado usuário mal-intencionado. Isso potencialmente apresenta vulnerabilidades sérias que podem ser exploradas, por exemplo:
>
> * Um usuário mal-intencionado pode lançar um ataque de negação de serviço chamando repetidamente o serviço interno que não tem uma superfície de ataque protegida.
> * Um usuário mal-intencionado poderá entregar pacotes mal formados a um serviço interno resultando em comportamento não intencionado.
> * Um serviço destinado a ser interno pode devolver informações particulares ou sensíveis não destinadas a serem expostas a serivoços fora do cluster, assim expondo essas informações sensíveis a um usuário mal-intencionado. 
>
> Certifique-se de entender totalmente e reduza as implicações de segurança potenciais para o cluster e os aplicativos em execução, antes de tornar pública a porta do proxy reverso. 
>

Se você quiser expor um proxy reverso publicamente para um cluster autônomo, a maneira na qual você fizer isso dependerá do sistema que hospeda o cluster e está além do escopo deste artigo. O aviso anterior sobre como expor o proxy reverso publicamente, no entanto, ainda se aplica.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Exponha o proxy reverso por meio do portal do Azure 

1. No portal do Azure, clique no grupo de recursos para seu cluster, em seguida clique no balanceador de carga para seu cluster.
2. Para adicionar uma integridade de investigação para a porta de proxy reverso, no painel esquerdo da janela do balanceador de carga, em **CONFIGURAÇÕES**, clique em **Investigações de integridade**. Em seguida, clique em **Adicionar** na parte superior da janela de investigações de integridadee insira os detalhes para a porta de proxy reverso, em seguida clique em **Ok**. Por padrão, a porta de proxy reverso é 19081, a menos que alterada ao criar o cluster.

   ![Configurar a investigação de integridade de proxy reverso](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Para adicionar uma regra do Balanceador de Carga para expor a porta do proxy reverso, no painel esquerdo da janela do Azure Load Balancer, em **CONFIGURAÇÕES**, clique em **Regras de balanceamento de carga**. Em seguida, clique em **Adicionar** na parte superior da janela de regras de balanceamento de carga e insira os detalhes para a porta do proxy reverso. Verifique se você definiu o valor da **Porta** para a porta que você deseja que o proxy reverso seja exposto, o valor da **porta de back-end** para a porta definida quando você habilitou o proxy reverso, e o valor **de investigação de integridade** para a investigação de integridade que você configurou na etapa anterior. Defina outros campos conforme apropriado e clique em **OK**.

   ![Configurar regra de balanceador de carga para o proxy reverso](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Expor o proxy reverso, por meio de modelos do Gerenciador de Recursos

O JSON a seguir referencia o mesmo modelo que é usado em [Habilitar o proxy reverso por meio de modelos do Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates). Consulte a seção do documento para obter informações sobre como criar um modelo do Gerenciador de Recursos ou exportar um modelo para um cluster existente.  As alterações são feitas para a [seção do tipo de recurso](../resource-group-authoring-templates.md) [**Microsoft.Network/loadBalancers**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers).

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Personalizar o comportamento de proxy reverso usando as configurações de malha

Você pode personalizar o comportamento de proxy reverso por meio das configurações de malha no modelo do Gerenciador de Recursos para clusters hospedados no Azure ou no arquivo ClusterConfig.json para clusters autônomos. As configurações que controlam o comportamento de proxy reverso estão localizadas na seção [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) na seção **fabricSettings** na seção de **propriedades** do cluster. 

Por exemplo, você pode definir o valor de **DefaultHttpRequestTimeout** para definir o tempo limite para solicitações para o proxy reverso para 180 segundos como o JSON a seguir:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Para obter mais informações sobre como atualizar as configurações de malha para clusters do Azure, consulte [Personalizar as configurações de cluster usando modelos do Gerenciador de Recursos](service-fabric-cluster-config-upgrade-azure.md). Para clusters autônomos, consulte [Personalizar configurações para clusters autônomos](service-fabric-cluster-config-upgrade-windows-server.md). 

Várias configurações de malha são usadas para ajudar a estabelecer uma comunicação segura entre o proxy reverso e serviços. Para obter informações detalhadas sobre essas configurações, consulte [conectar-se a um serviço seguro com o proxy reverso](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Próximas etapas
* [Definir encaminhamento para o serviço HTTP seguro com um proxy reverso](service-fabric-reverseproxy-configure-secure-communication.md)
* Para obter outras opções de configuração de proxy reverso, consulte a seção [ApplicationGateway/Http em Personalizar as configurações de cluster do Service Fabric](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
