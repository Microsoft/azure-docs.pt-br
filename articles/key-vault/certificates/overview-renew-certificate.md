---
title: Sobre a renovação de certificados do Azure Key Vault
description: Este artigo aborda a renovação de certificados do Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: ffa130c0598d2405469d272a3ac6852f281ed965
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726355"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Renovar seus certificados do Azure Key Vault

Com o Azure Key Vault, você pode provisionar, gerenciar e implantar com facilidade certificados digitais para a sua rede e habilitar comunicações seguras para seus aplicativos. Para obter mais informações sobre certificados, confira [Sobre os certificados do Azure Key Vault](./about-certificates.md).

Usando certificados de curta duração ou aumentando a frequência da rotação do certificado, você pode ajudar a impedir o acesso aos seus aplicativos por usuários não autorizados.

Este artigo aborda a renovação dos seus certificados do Azure Key Vault.

## <a name="get-notified-about-certificate-expiration"></a>Receba notificações sobre a expiração do certificado
Para receber notificações sobre eventos de tempo de vida do certificado, você precisa adicionar o contato do certificado. Contatos de certificado contêm informações de contato para enviar notificações disparadas por eventos de tempo de vida do certificado. As informações de contatos são compartilhadas por todos os certificados no cofre de chaves. Uma notificação é enviada a todos os contatos especificados para um evento para qualquer certificado no cofre de chaves.

### <a name="steps-to-set-certificate-notifications"></a>Etapas para definir as notificações do certificado:
Primeiro, adicione um contato do certificado ao cofre de chaves. Você pode adicioná-lo usando o portal do Azure ou o cmdlet PowerShell [`Add-AzureKeyVaultCertificateContact`](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact).

Em segundo lugar, configure quando você deseja ser notificado sobre a validade do certificado. Para configurar os atributos de ciclo de vida do certificado, confira [Configurar a rotação de certificado no Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

Se uma política de certificado for definida como renovação automática, uma notificação é enviada nos eventos a seguir.

- Antes da renovação de certificado
- Após a renovação do certificado, indicar se o certificado foi renovado com êxito ou se houve um erro, exigindo a renovação manual do certificado.  

  Quando uma política de certificação é definida para ser renovada manualmente (somente por email), uma notificação é enviada quando chega o momento de renovar o certificado.  

No Key Vault, há três categorias de certificados:
-    Certificados que são criados com uma AC (autoridade de certificação) integrada, como a DigiCert ou a GlobalSign
-    Certificados que são criados com uma AC não integrada
-    Certificados autoassinados

## <a name="renew-an-integrated-ca-certificate"></a>Renovar um Certificado de Autoridade de Certificação integrado 
O Azure Key Vault cuida da manutenção de ponta a ponta dos certificados emitidos pelas autoridades de certificação Microsoft confiáveis DigiCert e GlobalSign. Saiba como [integrar uma AC confiável ao Key Vault](./how-to-integrate-certificate-authority.md).

## <a name="renew-a-nonintegrated-ca-certificate"></a>Renovar um Certificado de Autoridade de Certificação não integrado 
Usando o Azure Key Vault, você pode importar certificados de qualquer AC, um benefício que permite que você integre vários recursos do Azure e facilite a implantação. Se você está preocupado em perder o controle das datas de validade do certificado ou descobriu que um certificado já expirou, o cofre de chaves pode ajudar você a se manter atualizado. Para Certificados de Autoridade de Certificação não integrados, o cofre de chaves permite que você configure notificações de email próximo à validade. Essas notificações também podem ser definidas para vários usuários.

> [!IMPORTANT]
> Um certificado é um objeto com controle de versão. Se a versão atual estiver expirando, você precisará criar outra. Conceitualmente, cada nova versão é um novo certificado composto por uma chave e um blob que vincula essa chave a uma identidade. Quando você usa uma AC não parceira, o cofre de chaves gera um par chave-valor e retorna uma CSR (solicitação de assinatura de certificado).

Para renovar um Certificado de Autoridade de Certificação não integrado, faça o seguinte:

1. Entre no portal do Azure e abra o certificado que deseja renovar.
1. No painel do certificado, selecione **Nova Versão**.
1. Selecione **Operação de Certificado**.
1. Selecione **Baixar CSR** para baixar um arquivo CSR na unidade local.
1. Envie a CSR para sua escolha de AC para assinar a solicitação.
1. Retorne a solicitação assinada e selecione **Mesclar CSR** no mesmo painel de operação do certificado.

> [!NOTE]
> É importante mesclar a CSR assinada com a mesma solicitação de CSR que você criou. Caso contrário, a chave não terá uma correspondência.

Para obter mais informações sobre como criar uma CSR, confira [Criar e mesclar uma CSR no Key Vault]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renew-a-self-signed-certificate"></a>Renovar um certificado autoassinado

O Azure Key Vault também cuida da renovação automática de certificados autoassinados. Para saber mais sobre como alterar a política de emissão e atualizar os atributos de ciclo de vida de um certificado, confira [Configurar a rotação de certificado no Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

## <a name="troubleshoot"></a>Solucionar problemas
* Se o certificado emitido estiver com o status *desabilitado* no portal do Azure, acesse **Operação de Certificado** para ver a mensagem de erro do certificado.
* Tipo de erro "O CSR usado para obter o seu certificado já foi usado. Tente gerar um novo certificado com um novo CSR".
  Acesse a seção 'Política Avançada' do certificado e verifique se a opção **'reutilizar chave na renovação'** está desativada.


## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Como testar o recurso de rotação automática do certificado?**

Crie um certificado com uma validade de **1 mês** e defina a ação de tempo de vida para rotação como **1%** . Essa configuração vai girar o certificado a cada 7,2 horas.
  
**As marcas serão replicadas após a renovação automática do certificado?**

Sim, as marcas serão replicadas após a renovação automática.

## <a name="next-steps"></a>Próximas etapas
*    [Integrar o Key Vault à autoridade de certificação DigiCert](how-to-integrate-certificate-authority.md)
*    [Tutorial: Configurar a rotação automática do certificado no Key Vault](tutorial-rotate-certificates.md)
