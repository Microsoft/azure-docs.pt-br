---
title: Implantar um Serviço de Nuvem do Azure (suporte estendido) – modelos
description: Implantar um Serviço de Nuvem do Azure (suporte estendido) usando modelos do ARM
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9849648c8a0a76ff89a6f95e64eeade791e7135c
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106381767"
---
# <a name="deploy-a-cloud-service-extended-support-using-arm-templates"></a>Implantar um Serviço de Nuvem (suporte estendido) usando modelos do ARM

Este tutorial explica como usar [modelos do ARM](../azure-resource-manager/templates/overview.md) para criar uma implantação do Serviço de Nuvem (suporte estendido). 

## <a name="before-you-begin"></a>Antes de começar

1. Examine os [pré-requisitos de implantação](deploy-prerequisite.md) dos Serviços de Nuvem (suporte estendido) e crie os recursos associados.

2. Crie um grupo de recursos usando o [portal do Azure](../azure-resource-manager/management/manage-resource-groups-portal.md) ou o [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md). Essa etapa é opcional se você está usando um grupo de recursos existente.

3. Crie um endereço IP público e defina a propriedade rótulo DNS do endereço IP público. Os Serviços de Nuvem (suporte estendido) dão suporte somente aos endereços IP Públicos do SKU (https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) ) [Básico]. Os IPs públicos do SKU Standard não funcionam com os Serviços de Nuvem.
Se você estiver usando um IP estático, ele precisará ser referenciado como um IP Reservado no arquivo de Configuração de Serviço (.cscfg). Se estiver usando um endereço IP existente, ignore esta etapa e adicione as informações de endereço IP diretamente aos parâmetros de configuração do balanceador de carga do modelo do ARM.

4. Crie um objeto de perfil de rede e associe o endereço IP público ao front-end do balanceador de carga. A plataforma Azure cria automaticamente um recurso de balanceador de carga de SKU 'clássico' na mesma assinatura que o recurso de serviço de nuvem. O recurso de balanceador de carga é um recurso somente leitura no ARM. Todas as atualizações para o recurso têm suporte apenas por meio dos arquivos de implantação do serviço de nuvem (.cscfg e .csdef)
 
5. Crie uma conta de armazenamento usando o [portal do Azure](../storage/common/storage-account-create.md?tabs=azure-portal) ou o [PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell). Essa etapa é opcional se você está usando uma conta de armazenamento existente.

6. Carregue os arquivos de Definição de Serviço (.csdef) e de Configuração de Serviço (.cscfg) para a conta de armazenamento usando o [portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), [AzCopy](../storage/common/storage-use-azcopy-blobs-upload.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou o [PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md#upload-blobs-to-the-container). Obtenha os URIs de SAS dos dois arquivos a serem adicionados ao modelo do ARM posteriormente neste tutorial.

6. (Opcional) Criar um cofre de chaves e carregar um certificado.

    -  Certificados podem ser anexados aos serviços de nuvem para habilitar a comunicação segura com o serviço. Para usar certificados, suas impressões digitais devem ser especificadas no arquivo de Configuração do Serviço (.cscfg) e carregadas em um Key Vault. Um Key Vault pode ser criado por meio do [portal do Azure](../key-vault/general/quick-create-portal.md) ou do [PowerShell](../key-vault/general/quick-create-powershell.md).
    - O Key Vault associado precisa estar localizado na mesma região e assinatura que o serviço de nuvem.
    - O Key Vault associado deve receber as permissões apropriadas para que o recurso dos Serviços de Nuvem (suporte estendido) possa recuperar o certificado dele. Para saber mais, confira [Certificados e o Key Vault](certificates-and-key-vault.md)
    - O Key Vault precisa ser referenciado na seção OsProfile do modelo do ARM mostrado nas etapas a seguir.

## <a name="deploy-a-cloud-service-extended-support"></a>Implantar um Serviço de Nuvem (suporte estendido)

> [!NOTE]
> Uma forma mais fácil e mais rápida de gerar o modelo do ARM e o arquivo de parâmetro é usando o [portal do Azure](https://portal.azure.com). Você pode [baixar o modelo do ARM gerado](generate-template-portal.md) por meio do portal para criar seu serviço de nuvem por meio do PowerShell
 
1. Crie a rede virtual. O nome da rede virtual deve corresponder às referências no arquivo de Configuração de Serviço (.cscfg). Se estiver usando uma rede virtual existente, omita esta seção do modelo do ARM.

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     Se estiver criando uma rede virtual, adicione o código abaixo à seção `dependsOn` para garantir que a plataforma crie a rede virtual antes de criar o serviço de nuvem.

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. Crie um endereço IP público e (opcionalmente) defina a propriedade rótulo DNS do endereço IP público. Se você estiver usando um IP estático, ele precisará ser referenciado como um IP Reservado no arquivo de Configuração de Serviço (.cscfg). Se estiver usando um endereço IP existente, ignore esta etapa e adicione as informações de endereço IP diretamente aos parâmetros de configuração do balanceador de carga do modelo do ARM.
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     Se estiver criando um endereço IP, adicione o código abaixo à seção `dependsOn` para garantir que a plataforma crie o endereço IP antes de criar o serviço de nuvem.
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. Crie um objeto de perfil de rede e associe o endereço IP público ao front-end do balanceador de carga. Um balanceador de carga é criado automaticamente pela plataforma.

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

4. Adicione a referência do cofre de chaves na seção `OsProfile` do modelo do ARM. O Key Vault é usado para armazenar certificados associados aos Serviços de Nuvem (suporte estendido). Adicione os certificados ao Key Vault e referencie as impressões digitais do certificado no arquivo de Configuração do Serviço (.cscfg). Você também precisa habilitar as Políticas de acesso do Key Vault para as Máquinas Virtuais do Azure para implantação (no portal) para que o recurso dos Serviços de Nuvem (suporte estendido) possa recuperar o certificado armazenado como segredos do Key Vault. O Key Vault precisa estar localizado na mesma região e assinatura que o Serviço de Nuvem e ter um nome exclusivo. Para obter mais informações, confira [Usando certificados com os Serviços de Nuvem (suporte estendido)](certificates-and-key-vault.md).
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > SourceVault é a ID de recurso do ARM para seu Key Vault. Você pode encontrar essas informações localizando a ID do Recurso na seção de propriedades de seu Key Vault.
    > - certificateUrl pode ser encontrado navegando até o certificado no cofre de chaves rotulado como **Identificador Secreto**.  
   >  - certificateUrl deve estar no formato https://{keyvault-endpoin}/secrets/{secretname}/{secret-id}

5. Crie um Perfil de Função. Verifique se o número de funções, nomes de função, número de instâncias em cada função e tamanhos são os mesmos nas seções Configuração de Serviço (.cscfg), Definição de Serviço (.csdef) e Perfil de Função no modelo do ARM.
    
    ```json
    "roleProfile": {
      "roles": {
        "value": [
          {
            "name": "WebRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            }
          },
          {
            "name": "WorkerRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            } 
          } 
        ]
      }
    }   
    ```

6. (Opcional) Crie um perfil de extensão para adicionar extensões ao serviço de nuvem. Para este exemplo, estamos adicionando a Área de Trabalho Remota e a extensão de diagnóstico do Microsoft Azure.
   > [!Note] 
   > A senha da área de trabalho remota precisa ter entre 8 e 123 caracteres e deve satisfazer a pelo menos 3 dos requisitos de complexidade de senha a seguir: 1) Conter um caractere maiúsculo 2) Conter um caractere minúsculo 3) Conter um dígito numérico 4) Conter um caractere especial 5) Caracteres de controle não são permitidos

    ```json
        "extensionProfile": {
          "extensions": [
            {
              "name": "RDPExtension",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Windows.Azure.Extensions",
                "type": "RDP",
                "typeHandlerVersion": "1.2.1",
                "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
              }
            },
            {
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Azure.Diagnostics",
                "type": "PaaSDiagnostics",
                "typeHandlerVersion": "1.5",
                "settings": "[parameters('wadPublicConfig_WebRole1')]",
                "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                "rolesAppliedTo": [
                  "WebRole1"
                ]
              }
            }
          ]
        }
    ```

7. Examine o modelo completo.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "wadPublicConfig_WebRole1": {
          "type": "string",
          "metadata": {
             "description": "Public configuration of Windows Azure Diagnostics extension"
          }
        },
        "wadPrivateConfig_WebRole1": {
          "type": "securestring",
          "metadata": {
            "description": "Private configuration of Windows Azure Diagnostics extension"
          }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2021-03-01",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
            "roleProfile": {
              "roles": [
                {
                  "name": "WebRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                },
                {
                  "name": "WorkerRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                }
              ]
            },
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
            "extensionProfile": {
              "extensions": [
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                  ]
                }
              }
            ]
          }
        }
       }
      ]
    }
    ```

8. Implante o modelo e o arquivo de parâmetro (definindo parâmetros no arquivo de modelo) para criar a implantação do serviço de nuvem (suporte estendido). Veja estes [modelos de exemplo](https://github.com/Azure-Samples/cloud-services-extended-support), conforme necessário.

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "ContosOrg" -TemplateFile "file path to your template file" -TemplateParameterFile "file path to your parameter file"
    ```

## <a name="next-steps"></a>Próximas etapas 

- Examine as [perguntas frequentes](faq.md) sobre os Serviços de Nuvem (suporte estendido).
- Implante um Serviço de Nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md), o [PowerShell](deploy-powershell.md), o [Modelo](deploy-template.md) ou o [Visual Studio](deploy-visual-studio.md).
- Visite o [repositório de exemplos dos Serviços de Nuvem (suporte estendido)](https://github.com/Azure-Samples/cloud-services-extended-support)
