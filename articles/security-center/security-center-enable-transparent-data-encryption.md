---
title: Habilitar Transparent Data Encryption na Central de Segurança do Azure | Microsoft Docs
description: Este documento mostra como implementar a recomendação da Central de Segurança do Azure para **Habilitar a Transparent Data Encryption**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d5ddec40a1b20e377ec18ce871018f674557e7b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703962"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Habilitar Transparent Data Encryption na Central de Segurança do Azure
A Central de Segurança do Azure recomendará habilitar a TDE (Transparent Data Encryption) em bancos de dados SQL se já não estiver habilitada. A TDE protege seus dados e ajuda a cumprir os requisitos de conformidade, criptografando seu banco de dados, backups associados e arquivos de log de transações em repouso, sem exigir alterações no seu aplicativo. Para saber mais, consulte [Transparent Data Encryption com o Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/dn948096).

Essa recomendação se aplica apenas ao serviço do SQL Azure, não incluindo o SQL em execução em máquinas virtuais.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Ela não é um guia passo a passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na folha **Recomendações**, selecione **Habilitar Transparent Data Encryption**.
   ![Habilitar Transparent Data Encryption][1]
2. Isso abre a folha **Habilitar Transparent Data Encryption em bancos de dados SQL** . Selecione um banco de dados SQL no qual você deseja habilitar a TDE.
   ![Selecionar o banco de dados SQL no qual habilitar a TDE][2]
3. Na folha **Criptografia de dados transparentes**, selecione **ATIVAR** em Criptografia de dados e selecione **Salvar** na faixa de opções superior da folha.
   ![Ativar TDE][3]

   Quando a TDE for habilitada no banco de dados SQL selecionado, o **Status da criptografia** será alterado para **Criptografado**.    

   ![Status de criptografia][4]

## <a name="see-also"></a>Consulte também
Este artigo mostrou como implementar a recomendação da Central de Segurança "Habilitar a Transparent Data Encryption". Para saber mais sobre a TDE do SQL, consulte o seguinte:

* [Transparent Data Encryption com o Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/dn948096)
* [Introdução ao Transparent Data Encryption (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Monitoramento de soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiro.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço de localização.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) : obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
