---
title: 'Azure Active Directory Domain Services: Configuração do grupo de solução de problemas de segurança de rede | Microsoft Docs'
description: Resolução de problemas de configuração do NSG para o Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 08875ec23740eab7787c4a919566df521deba9a5
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473918"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Solucionar problemas de configuração de rede inválida para o domínio gerenciado
Este artigo ajuda você a solucionar problemas e resolver erros de configuração relacionados à rede que resultam na seguinte mensagem de alerta:

## <a name="alert-aadds104-network-error"></a>Alerta AADDS104: Erro de rede
**Mensagem de alerta:** *A Microsoft não consegue alcançar os controladores de domínio para este domínio gerenciado. Isso pode ocorrer se um grupo de segurança de rede (NSG) configurado na sua rede virtual bloquear o acesso ao domínio gerenciado. Outro motivo possível é a existência de uma rota definida pelo usuário que bloqueia o tráfego de entrada da Internet.*

Configurações inválidas do NSG são a causa mais comum de erros de rede para o Azure AD Domain Services. O NSG (Grupo de Segurança de Rede) configurado para a sua rede virtual deve permitir o acesso ao [portas específicas](network-considerations.md#ports-required-for-azure-ad-domain-services). Se essas portas são bloqueadas, a Microsoft não é capaz de monitorar ou atualizar o domínio gerenciado. Além disso, a sincronização entre o seu diretório do Azure AD e seu domínio gerenciado é afetada. Ao criar o NSG, mantenha essas portas abertas para evitar interrupção no serviço.

### <a name="checking-your-nsg-for-compliance"></a>Verificando o NSG para fins de conformidade

1. Navegue até a página [Grupos de segurança de rede](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) no portal do Azure
2. Na tabela, escolha o NSG associado à sub-rede na qual o domínio gerenciado está habilitado.
3. Em **Configurações** no painel esquerdo, clique em **Regras de segurança de entrada**
4. Examine as regras em vigor e identifique quais regras estão bloqueando o acesso a [essas portas](network-considerations.md#ports-required-for-azure-ad-domain-services)
5. Edite o NSG para garantir a conformidade excluindo a regra, adicionando uma regra, ou criando um NSG completamente novo. Vaja abaixo as etapas para [adicionar uma regra](#add-a-rule-to-a-network-security-group-using-the-azure-portal) ou criar um novo NSG compatível

## <a name="sample-nsg"></a>Exemplo de NSG
A tabela a seguir ilustra um exemplo de NSG que manteria seu domínio gerenciado seguro ao mesmo tempo em que permitiria o monitoramento, gestão e atualização das informações pela Microsoft.

![Exemplo de NSG](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> O Azure AD Domain Services exige acesso irrestrito de saída da rede virtual. É recomendável não criar nenhuma regra de NSG adicional que restrinja o acesso de saída para a rede virtual.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Adicionar uma regra a um Grupo de Segurança de Rede usando o Portal do Azure
Se você não quiser usar o PowerShell, você pode adicionar manualmente regras únicas para os NSGs usando o portal do Azure. Conclua estas etapas para criar regras no seu Grupo de Segurança de Rede:

1. Navegue até a página [Grupos de segurança de rede](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) no portal do Azure.
2. Na tabela, escolha o NSG associado à sub-rede na qual o domínio gerenciado está habilitado.
3. Em **Configurações**, no painel de navegação esquerdo, clique em **Regras de segurança de entrada** ou em **Regras de segurança de saída**.
4. Crie a regra clicando em **Adicionar** e preenchendo as informações. Clique em **OK**.
5. Verifique se a regra foi criada localizando-a na tabela de regras.


## <a name="need-help"></a>Precisa de ajuda?
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](contact-us.md).
