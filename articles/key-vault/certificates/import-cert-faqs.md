---
title: Perguntas frequentes-Azure Key Vault importação de certificado
description: Obtenha respostas para perguntas frequentes sobre a importação de certificados Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: e75786c42b54882c249b5ed7100ebd12f3f78e78
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254352"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Perguntas frequentes sobre importação de certificados Azure Key Vault

Este artigo responde a perguntas frequentes sobre a importação de certificados de Azure Key Vault.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Como importar um certificado no Azure Key Vault?

Para uma operação de importação de certificado, Azure Key Vault aceita dois formatos de arquivo de certificado: PEM e PFX. Embora haja arquivos PEM com apenas a parte pública, Key Vault requer e aceita apenas um arquivo PEM ou PFX com uma chave privada. Para obter mais informações, consulte [importar um certificado para Key Vault](./tutorial-import-certificate.md#import-a-certificate-to-key-vault).

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Depois de importar um certificado protegido por senha para Key Vault e, em seguida, baixá-lo, por que não consigo ver a senha associada a ele?
    
Depois que um certificado é importado e protegido no Key Vault, sua senha associada não é salva. A senha é necessária apenas uma vez durante a operação de importação. Isso é por design, mas você sempre pode obter o certificado como um segredo e convertê-lo de Base64 para PFX adicionando a senha por meio de [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Como resolver um erro de "parâmetro insatisfatório"? Quais são os formatos de certificado com suporte para importar para Key Vault?

Ao importar um certificado, você precisa garantir que a chave seja incluída no arquivo. Se você tiver uma chave privada armazenada separadamente em um formato diferente, será necessário combinar a chave com o certificado. Algumas CAs (autoridades de certificação) fornecem certificados em outros formatos. Portanto, antes de importar o certificado, verifique se ele está no formato de arquivo PEM ou PFX e se a chave usa a criptografia de Rivest – Shamir – adlemat (RSA) ou ECC (criptografia de curva elíptica). 

Para obter mais informações, consulte requisitos de [certificado](./certificate-scenarios.md#formats-of-import-we-support) e [requisitos de chave de certificado](../keys/about-keys.md).

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>Posso importar um certificado usando um modelo de ARM?

Não, não é possível executar operações de certificado usando um modelo de Azure Resource Manager (ARM). Uma solução alternativa recomendada seria usar os métodos de importação de certificado na API do Azure, o CLI do Azure ou o PowerShell. Se você tiver um certificado existente, poderá importá-lo como um segredo.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Quando importo um certificado por meio do portal do Azure, recebo um erro "algo deu errado". Como posso investigar ainda mais?
    
Para exibir um erro mais descritivo, importe o arquivo de certificado usando [o CLI do Azure ou o](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) [PowerShell](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Como posso resolver "tipo de erro: acesso negado ou o usuário não está autorizado a importar o certificado"?
    
A operação de importação exige que você conceda permissões de usuário para importar o certificado nas políticas de acesso. Para fazer isso, vá para o cofre de chaves, selecione **políticas de acesso**  >  **Adicionar política de acesso**  >  **selecionar** entidade de permissões de certificado  >  , pesquise o usuário e, em seguida, adicione o endereço de email do usuário. 

Para obter mais informações sobre políticas de acesso relacionadas a certificados, consulte [about Azure Key Vault Certificates](./about-certificates.md#certificate-access-control).


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Como posso resolver "tipo de erro: conflito ao criar um certificado"?
    
Cada nome de certificado deve ser exclusivo. Um certificado com o mesmo nome pode estar em um estado excluído de maneira reversível. Além disso, de acordo com a [composição de um certificado](./about-certificates.md#composition-of-a-certificate), quando um novo certificado é criado, ele cria um segredo endereçável com o mesmo nome, portanto, se houver outra chave ou segredo no cofre de chaves com o mesmo nome que você está tentando especificar para o certificado, a criação do certificado falhará e você precisará remover essa chave ou segredo ou usar um nome diferente para o certificado. 

Para obter mais informações, consulte [obter operação de certificado excluído](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate).

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Por que estou recebendo "tipo de erro: o comprimento do caractere é muito longo"?
Esse erro pode ser causado por uma destas duas razões:    
* O nome da entidade do certificado é limitado a 200 caracteres.
* A senha do certificado é limitada a 200 caracteres.


### <a name="error-the-specified-pem-x509-certificate-content-is-in-an-unexpected-format-please-check-if-certificate-is-in-valid-pem-format"></a>Erro "o conteúdo do certificado PEM X. 509 especificado está em um formato inesperado. Verifique se o certificado está em um formato PEM válido. "
Verifique se o conteúdo no arquivo PEM usa separadores de linha de estilo UNIX `(\n)`

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Posso importar um certificado expirado para Azure Key Vault?
    
Não, os certificados PFX expirados não podem ser importados para Key Vault.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Como posso converter meu certificado para o formato correto?

Você pode solicitar que sua autoridade de certificação forneça o certificado no formato necessário. Também há ferramentas de terceiros que podem ajudá-lo a converter o certificado para o formato adequado.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Posso importar certificados de CAs que não são de parceiros?
Sim, você pode importar certificados de qualquer AC, mas o cofre de chaves não poderá renová-los automaticamente. Você pode definir lembretes para serem notificados sobre a expiração do certificado.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Se eu importar um certificado de uma autoridade de certificação de parceiro, o recurso de renovação automática ainda funcionará?
Sim. Depois de carregar o certificado, certifique-se de especificar a rotação autogiro na política de emissão do certificado. Suas configurações permanecerão em vigor até o lançamento do próximo ciclo ou da versão do certificado.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Por que não consigo ver o certificado do serviço de aplicativo que importei para Key Vault? 
Se você importou o certificado com êxito, você deve ser capaz de confirmá-lo acessando o painel **segredos** .


## <a name="next-steps"></a>Próximas etapas

- [Azure Key Vault certificados](./about-certificates.md)
