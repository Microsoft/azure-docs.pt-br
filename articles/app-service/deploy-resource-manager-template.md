---
title: Implantar aplicativos com modelos
description: Encontre orientações sobre como criar modelos de Azure Resource Manager para provisionar e implantar aplicativos do serviço de aplicativo.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1146b5979d81b91c6c6894aa54b2e0ca50c896c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88961594"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Diretrizes sobre a implantação de aplicativos Web usando modelos do Azure Resource Manager

Este artigo fornece recomendações para a criação de modelos do Azure Resource Manager para implantar soluções do Serviço de Aplicativo do Azure. Essas recomendações podem ajudá-lo a evitar problemas comuns.

## <a name="define-dependencies"></a>Definir dependências

Definir dependências para aplicativos web requer uma compreensão de como os recursos de um aplicativo web interagem. Se você especificar dependências em uma ordem incorreta, poderá causar erros de implantação ou criar uma condição de corrida que interrompe a implantação.

> [!WARNING]
> Se você incluir uma extensão de site do MSDeploy no seu modelo, será necessário configurar qualquer recurso de configuração como dependente do recurso do MSDeploy. As alterações de configuração fazem com que o site seja reiniciado de maneira assíncrona. Ao tornar os recursos de configuração dependentes do MSDeploy, você assegura que o MSDeploy seja concluída antes do reinício do site. Sem essas dependências, o site poderá reiniciar durante o processo de implantação do MSDeploy. Para obter um modelo de exemplo, consulte [modelo do WordPress com dependência de implantação da Web](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

A imagem a seguir mostra a ordem de dependência para vários recursos do Serviço de Aplicativo:

![Dependências do aplicativo Web](media/web-sites-rm-template-guidance/web-dependencies.png)

Os recursos são implantados na seguinte ordem:

**Camada 1**
* Plano do Serviço de Aplicativo.
* Quaisquer outros recursos relacionados, como bancos de dados ou contas de armazenamento.

**Camada 2**
* Aplicativo Web - depende do plano do Serviço de Aplicativo.
* Instância do Application Insights que direciona o farm de servidores - depende do plano do Serviço de Aplicativo.

**Nível 3**
* Controle do código-fonte - depende do aplicativo Web.
* Extensão de site do MSDeploy - depende do aplicativo Web.
* Aplicativo Azure instância do insights que se destina ao aplicativo Web – depende do aplicativo Web.

**Camada 4**
* Certificado do Serviço de Aplicativo - depende do controle do código-fonte ou MSDeploy se qualquer um estiver presente. Caso contrário, ele depende do aplicativo web.
* Conjunto de configurações (cadeias de conexão, valores de configuração da web, configurações de aplicativo) - depende do controle do código-fonte ou MSDeploy se qualquer um estiver presente. Caso contrário, ele depende do aplicativo web.

**Camada 5**
* Associações de nome do host - depende do certificado se estiver presente. Caso contrário, ele depende de um recurso de nível superior.
* Extensões de site - depende das configurações, se presente. Caso contrário, ele depende de um recurso de nível superior.

Normalmente, sua solução inclui apenas alguns desses recursos e camadas. Para camadas ausentes, mapeie recursos mais baixos para o próximo nível superior.

O exemplo a seguir mostra parte de um modelo. O valor de configuração da cadeia de conexão depende da extensão do MSDeploy. A extensão do MSDeploy depende do aplicativo Web e do banco de dados. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Para um exemplo pronto para execução que usa o código acima, consulte [Modelo: Compilar um aplicativo Web Umbraco simples](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Localizar informações sobre erros do MSDeploy

Se seu modelo do Resource Manager usar MSDeploy, as mensagens de erro de implantação poderão ser difíceis de compreender. Para obter mais informações após uma implantação com falha, experimente as etapas a seguir:

1. Vá para o [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) do site.
2. Navegue até a pasta em D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Procure os arquivos appManagerStatus.xml e appManagerLog.xml. O primeiro arquivo registra o status. O segundo arquivo registra as informações sobre o erro. Se o erro não estiver claro para você, você poderá incluí-lo quando estiver solicitando ajuda no [Fórum](/answers/topics/azure-webapps.html).

## <a name="choose-a-unique-web-app-name"></a>Escolha um nome de aplicativo Web exclusivo

O nome do seu aplicativo Web deve ser globalmente exclusivo. Você pode usar uma convenção de nomenclatura que provavelmente seja exclusiva ou pode usar a [função uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) para ajudar a gerar um nome exclusivo.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Implantar certificado de aplicativo Web do Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se o modelo incluir um recurso [Microsoft. Web/Certificates](/azure/templates/microsoft.web/certificates) para a associação TLS/SSL e o certificado for armazenado em um Key Vault, você deverá verificar se a identidade do serviço de aplicativo pode acessar o certificado.

No Azure global, a entidade de serviço do Serviço de Aplicativo tem a ID **abfa0a7c-a6b6-4736-8310-5855508787cd**. Para conceder acesso ao Key Vault para a entidade de serviço do Serviço de Aplicativo, use:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

No Azure Governamental, a entidade de serviço do Serviço de Aplicativo tem a ID **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Use essa ID no exemplo anterior.

No Key Vault, selecione **Certificados** e **Gerar/Importar** para carregar o certificado.

![Importar certificado](media/web-sites-rm-template-guidance/import-certificate.png)

No modelo, forneça o nome do certificado para o `keyVaultSecretName`.

Para obter um modelo de exemplo, confira [Implantar um certificado de aplicativo Web de um segredo do Key Vault e usá-lo para a criação da associação SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Próximas etapas

* Para um tutorial sobre a implantação de aplicativos Web com um modelo, consulte [Provisionar e implantar microsserviços previsíveis no Azure](deploy-complex-application-predictably.md).
* Para saber mais sobre a sintaxe JSON e as propriedades de tipos de recursos em modelos, consulte [Referência de modelo do Azure Resource Manager](/azure/templates/).