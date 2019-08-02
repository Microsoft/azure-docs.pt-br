---
title: Proteger os serviços de armazenamento e dados do Azure na central de segurança do Azure | Microsoft Docs
description: Este documento aborda as recomendações na Central de Segurança do Azure que ajudam a proteger seus dados e o serviço SQL do Azure, bem como a cumprir as políticas de segurança.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: v-mohabe
ms.openlocfilehash: 9e48114d0d4159d40006710f9c8194dea0d775f8
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295643"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Proteger os serviços de armazenamento e dados do Azure na central de segurança do Azure
Este tópico mostra como exibir e implementar recomendações de segurança para dados e recursos de armazenamento. A central de segurança do Azure encontrou essas recomendações ao analisar o estado de segurança de seus recursos do Azure.

## <a name="view-your-data-security-information"></a>Exibir suas informações de segurança de dados

1. Na seção **higiene de segurança de recursos** , clique em **dados e recursos de armazenamento**.

   ![Recursos de armazenamento de & de dados](./media/security-center-monitoring/click-data.png)

    A página **segurança de dados** é aberta com recomendações para recursos de dados.

     ![Recursos de dados](./media/security-center-monitoring/sql-overview.png)

Nessa página, você pode:

* Clique na guia **visão geral** para listar todas as recomendações de recursos de dados a serem corrigidas. 
* Clique em cada guia e exiba as recomendações por tipo de recurso.

    > [!NOTE]
    > Para saber mais sobre criptografia de armazenamento, leia [Habilitar a criptografia para a conta de armazenamento do Azure na Central de Segurança do Azure](security-center-enable-encryption-for-storage-account.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Corrigir uma recomendação em um recurso de dados

1. Em qualquer uma das guias de recurso, clique em um recurso. A página informações é aberta, listando as recomendações a serem corrigidas.

    ![Informações do recurso](./media/security-center-monitoring/sql-recommendations.png)

2. Clique em uma recomendação. A página recomendação é aberta e exibe as **etapas de correção** para implementar a recomendação.

   ![Etapas de correção](./media/security-center-monitoring/remediate1.png)

3. Clique em **executar ação**. A página Configurações de recurso é exibida.

    ![Habilitar recomendação](./media/security-center-monitoring/remediate2.png)

4. Siga as **etapas de correção** e clique em **salvar**.

## <a name="data-and-storage-recommendations"></a>Recomendações de disco e de armazenamento

|Tipo de recurso|Classificação de segurança|Recomendações|DESCRIÇÃO|
|----|----|----|----|
|Conta de armazenamento|20|A transferência segura para contas de armazenamento deve ser habilitada|A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camada de rede, como Man-in-the-Middle, espionagem e seqüestro de sessão.|
|Redis|20|Somente conexões seguras para o cache Redis devem ser habilitadas|Habilite apenas conexões via SSL para o Cache Redis do Azure. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão.|
|SQL|15|Transparent Data Encryption em bancos de dados SQL devem ser habilitadas|Habilite a criptografia transparente de dados para proteger dados em repouso e atender aos requisitos de conformidade.|
|SQL|15|A auditoria do SQL Server deve ser habilitada|Habilite auditoria para servidores SQL do Azure. (Apenas o serviço do SQL Azure. Não inclui o SQL em execução nas suas máquinas virtuais.)|
|Data Lake Analytics|5|Os logs de diagnóstico no Data Lake Analytics devem ser habilitados|Ativar os logs e mantenha-os por até um ano. Isso permitirá que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou sua rede estiver comprometida. |
|Data Lake Store|5|Os logs de diagnóstico no Azure Data Lake Store devem ser habilitados|Ativar os logs e mantenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida. |
|SQL|30|Vulnerabilidades em seus bancos de dados SQL devem ser corrigidas|A avaliação de vulnerabilidade do SQL examina seu banco de dados quanto a vulnerabilidades de segurança e expõe quaisquer desvios das práticas recomendadas, como configurações incorretas, excesso de permissões e dados confidenciais não protegidos. Resolver as vulnerabilidades encontradas pode melhorar muito o desenvolvimento da segurança de seu banco de dados.|
|SQL|20|Provisionar um administrador do Microsoft Azure Active Directory para o servidor SQL|Provisione um administrador do Microsoft Azure Active Directory para o servidor SQL para habilitar a autenticação do Microsoft Azure Active Directory. A autenticação do Microsoft Azure Active Directory permite o gerenciamento simplificado de permissões e o gerenciamento centralizado de identidades dos usuários de banco de dados e de outros serviços da Microsoft.|
|Conta de armazenamento|15|O acesso a contas de armazenamento com firewall e configurações de rede virtual deve ser restrito|Audite o acesso irrestrito à rede nas configurações de firewall da conta de armazenamento. Em vez disso, configure as regras de rede de forma que somente aplicativos das redes permitidas podem acessar a conta de armazenamento. Para permitir conexões de clientes locais ou da Internet específicos, você pode conceder acesso ao tráfego de redes virtuais específicas do Azure ou a intervalos de endereços IP de Internet públicos.|
|Conta de armazenamento|1|As contas de armazenamento devem ser migradas para novos recursos de Azure Resource Manager|Use o New Azure Resource Manager V2 para suas contas de armazenamento para fornecer aprimoramentos de segurança, como: controle de acesso mais forte (RBAC), melhor auditoria, implantação e governança baseadas no Resource Manager, acesso a identidades gerenciadas, acesso ao cofre de chaves para segredos e autenticação baseada no Azure AD e suporte para marcas e grupos de recursos para facilitar o gerenciamento da segurança.|

## <a name="see-also"></a>Consulte também
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte os seguintes tópicos:

* [Protegendo suas máquinas virtuais na Central de Segurança do Azure](security-center-virtual-machine-recommendations.md)
* [Protegendo seus aplicativos na Central de segurança do Azure](security-center-application-recommendations.md)
* [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)

Para saber mais sobre a central de segurança, consulte os seguintes tópicos:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
