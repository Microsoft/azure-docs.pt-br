---
title: Terminação de TLS com certificados de Azure Key Vault
description: Saiba como você pode integrar o Gateway de Aplicativo do Azure com o Key Vault para certificados de servidor que estejam anexados a ouvintes habilitados para HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 8a64956deb7849568e70e94c9b58170df60db1e3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775724"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Terminação TLS com os certificados do Key Vault

[Azure Key Vault](../key-vault/general/overview.md) é um repositório de segredos gerenciado por plataforma que você pode usar para proteger segredos, chaves e certificados TLS/SSL. O Gateway de Aplicativo v2 do Azure dá suporte à integração com o Key Vault para certificados de servidor que estejam anexados a ouvintes habilitados para HTTPS. Esse suporte é limitado à SKU v2 do gateway de aplicativo.

A integração do Key Vault oferece dois modelos para terminação de TLS:

- Você pode fornecer explicitamente certificados TLS/SSL anexados ao ouvinte. Esse modelo é a maneira tradicional de passar certificados TLS/SSL para o gateway de aplicativo para terminação TLS.
- Opcionalmente, você pode fornecer uma referência a um certificado ou segredo de Key Vault existente ao criar um ouvinte habilitado para HTTPS.

A integração do gateway de aplicativo com o Key Vault oferece muitos benefícios, incluindo:

- Segurança mais forte, pois os certificados TLS/SSL não são tratados diretamente pela equipe de desenvolvimento de aplicativos. A integração permite que uma equipe de segurança separada:
  * Configurar gateways de aplicativo.
  * Controlar ciclos de vida do gateway de aplicativo.
  * Conceda permissões a gateways de aplicativo selecionados para acessar certificados armazenados em seu cofre de chaves.
- Suporte para importar certificados existentes para o cofre de chaves. Ou use Key Vault APIs para criar e gerenciar novos certificados com qualquer um dos parceiros de Key Vault confiáveis.
- Suporte para renovação automática de certificados armazenados em seu cofre de chaves.

O gateway de aplicativo atualmente dá suporte apenas a certificados validados por software. Não há suporte para certificados validados do HSM (módulo de segurança de hardware). Depois que o gateway de aplicativo é configurado para usar certificados de Key Vault, suas instâncias recuperam o certificado do Key Vault e os instalam localmente para terminação de TLS. As instâncias também sondam Key Vault em intervalos de 4 horas para recuperar uma versão renovada do certificado, se existir. Se um certificado atualizado for encontrado, o certificado TLS/SSL atualmente associado ao ouvinte HTTPS será girado automaticamente.

> [!NOTE]
> O portal do Azure dá suporte apenas a certificados do keyvault, não a segredos. O gateway de aplicativo ainda dá suporte à referência de segredos do keyvault, mas somente por meio de recursos que não são do portal, como PowerShell, CLI, API, modelos ARM, etc. 

## <a name="how-integration-works"></a>Como funciona a integração

A integração do gateway de aplicativo com o Key Vault requer um processo de configuração de três etapas:

1. **Criar uma identidade gerenciada atribuída ao usuário**

   Você cria ou reutiliza uma identidade gerenciada atribuída pelo usuário existente, que o gateway de aplicativo usa para recuperar certificados de Key Vault em seu nome. Para obter mais informações, consulte [criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Esta etapa cria uma nova identidade no locatário Azure Active Directory. A identidade é confiável para a assinatura que é usada para criar a identidade.

1. **Configurar o cofre de chaves**

   Em seguida, importe um certificado existente ou crie um novo no cofre de chaves. O certificado será usado por aplicativos que são executados por meio do gateway de aplicativo. Nesta etapa, você também pode usar um segredo de Key Vault que também permite armazenar um arquivo PFX codificado por senha, baseado em base 64. É recomendável usar um tipo de "certificado" devido ao recurso de renovação automática disponível com esse tipo de objeto na Key Vault. Depois de criar um certificado ou um segredo, você deve definir políticas de acesso no Key Vault para permitir que a identidade receba acesso ao segredo.
   
   > [!IMPORTANT]
   > A partir de 15 de março de 2021, Key Vault reconhece o gateway de Aplicativo Azure como um dos serviços confiáveis, permitindo, assim, que você crie um limite de rede seguro no Azure. Isso lhe dá a capacidade de negar o acesso ao tráfego de todas as redes (incluindo o tráfego da Internet) para Key Vault, mas ainda torná-lo acessível para o recurso de gateway de aplicativo em sua assinatura. 

   > Você pode configurar o gateway de aplicativo em uma rede restrita de Key Vault da seguinte maneira. <br />
   > a) na folha de rede de Key Vault <br />
   > b) escolher ponto de extremidade privado e redes selecionadas na guia "firewall e redes virtuais" <br/>
   > c) em seguida, usando redes virtuais, adicione a rede virtual e a sub-rede do gateway de aplicativo. Durante o processo, configure também o ponto de extremidade de serviço ' Microsoft. keyvault ' selecionando sua caixa de seleção. <br/>
   > d) por fim, selecione "Sim" para permitir que os serviços confiáveis ignorem o Firewall do Key Vault. <br/>
   > 
   > ![Key Vault firewall](media/key-vault-certs/key-vault-firewall.png)


   > [!NOTE]
   > Se você implantar o gateway de aplicativo por meio de um modelo ARM, usando o CLI do Azure ou o PowerShell, ou por meio de um aplicativo do Azure implantado no portal do Azure, o certificado SSL será armazenado no cofre de chaves como um arquivo PFX codificado em base64. Você deve concluir as etapas em [usar Azure Key Vault para passar o valor do parâmetro seguro durante a implantação](../azure-resource-manager/templates/key-vault-parameter.md). 
   >
   > É particularmente importante definir `enabledForTemplateDeployment` como `true` . O certificado pode ser com senha ou pode ter uma senha. No caso de um certificado com uma senha, o exemplo a seguir mostra uma configuração possível para a `sslCertificates` entrada no `properties` para a configuração do modelo ARM para um gateway de aplicativo. Os valores de `appGatewaySSLCertificateData` e `appGatewaySSLCertificatePassword` são pesquisados no cofre de chaves, conforme descrito na seção [segredos de referência com ID dinâmica](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id). Siga as referências para trás do `parameters('secretName')` para ver como a pesquisa ocorre. Se o certificado não tiver senha, omita a `password` entrada.
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **Configurar o gateway de aplicativo**

   Depois de concluir as duas etapas anteriores, você pode configurar ou modificar um gateway de aplicativo existente para usar a identidade gerenciada atribuída pelo usuário. Para obter mais informações, consulte [set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity).

   Você também pode configurar o certificado TLS/SSL do ouvinte HTTP para apontar para o URI completo do certificado de Key Vault ou da ID secreta.

   ![Certificados do Key Vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Próximas etapas

[Configurar a terminação de TLS com certificados de Key Vault usando Azure PowerShell](configure-keyvault-ps.md)
