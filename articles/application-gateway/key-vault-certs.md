---
title: Terminação SSL com certificados do Key Vault
description: Saiba como você pode integrar o gateway de aplicativo do Azure com o Key Vault para certificados de servidor que estão anexados para os ouvintes HTTPS habilitado.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 9ab1a42578081d4e6537f221e3f3a8f4c7babde0
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759628"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Terminação SSL com certificados do Key Vault

[O Azure Key Vault](../key-vault/key-vault-whatis.md) é um repositório de segredos gerenciados por plataforma, você pode usar para proteger segredos, chaves e certificados SSL. O Gateway de aplicativo dá suporte à integração com o Key Vault (em visualização pública) para certificados de servidor que estão anexados para os ouvintes HTTPS habilitado. Esse suporte é limitado ao v2 SKU de Gateway de aplicativo.

> [!IMPORTANT]
> A integração do Cofre de chaves do Gateway de aplicativo está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

Há dois modelos para a terminação SSL com essa visualização pública:

- Você pode fornecer explicitamente os certificados SSL anexados ao ouvinte. Esse é o modelo tradicional de passar os certificados SSL para o Gateway de aplicativo para a terminação SSL.
- Opcionalmente, você pode fornecer uma referência a um certificado existente do cofre da chave ou segredo durante HTTPS habilitado a criação de ouvinte.

Há muitos benefícios com a integração do Cofre de chaves, incluindo:

- Aumentar a segurança, pois os certificados SSL não são tratados diretamente pela equipe de desenvolvimento do aplicativo. Integração com o Key Vault permite que uma equipe de segurança separados para provisionar, controlar o ciclo de vida e conceder a permissão apropriada para selecionar os Gateways de aplicativos para certificados de acesso armazenados no Key Vault.
- Suporte para importar certificados existentes para o Cofre de chaves ou usar APIs Key Vault para criar e gerenciar novos certificados com qualquer um dos parceiros de Cofre de chaves confiáveis.
- Suporte para certificados armazenados no cofre de chaves para renovar automaticamente.

O Gateway de aplicativo atualmente suporta apenas a certificados de software validado. Não há suporte para certificados de HSM (módulo) validado de segurança de hardware. Depois que o Gateway de aplicativo é configurado para usar certificados do Key Vault, suas instâncias de recuperem o certificado do Key Vault e instalação-los localmente para a terminação SSL. As instâncias também sondam o Cofre de chaves em um intervalo de 24 horas para recuperar uma versão de renovação do certificado, se ele existir. Se um certificado atualizado for encontrado, o certificado SSL associado atualmente com o ouvinte de HTTPS é girado automaticamente.

## <a name="how-it-works"></a>Como ele funciona

Integração com o Key Vault requer um processo de configuração de três etapas:

1. **Criar identidade gerenciada atribuída pelo usuário**

   Você deve criar ou reutilizar um atribuído a identidade gerenciada que usa o Gateway de aplicativo para recuperar certificados de Cofre de chaves em seu nome de usuário existente. Para obter mais informações, consulte [What ' s identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md) Esta etapa cria uma nova identidade no locatário do Azure AD, que é confiável para a assinatura usada para criar a identidade.
1. **Configurar o Cofre de chaves**

   Você deve, em seguida, importar ou cria um novo certificado no cofre de chaves usado por aplicativos executados por meio do Gateway de aplicativo. Um segredo de Cofre de chaves armazenado como sem senha base 64 codificados arquivo PFX também pode ser usado nesta etapa. Usar um tipo de certificado é preferido devido à renovação automática recursos disponíveis com objetos de tipo de certificado no cofre de chaves. Depois de criar um certificado ou o segredo, políticas de acesso devem ser definidas no cofre de chaves para permitir que a identidade a serem concedidos *obter* acesso para buscar o segredo.

1. **Configurar o Gateway de aplicativo**

   Depois que as duas etapas anteriores forem concluídas, você pode provisionar ou modificar um Gateway de aplicativo existente para usar o identidade gerenciada atribuída ao usuário. Você também configurar o certificado SSL do ouvinte HTTP para apontar para do completa URI do Cofre de chaves certificado ou ID secreta.

![Certificados do Key Vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Próximos passos

[Configura a terminação SSL com certificados do Key Vault usando o Azure PowerShell](configure-keyvault-ps.md).