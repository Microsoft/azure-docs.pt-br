---
title: Usar certificados com o Enterprise Integration Pack | Microsoft Docs
description: "Saiba como usar certificados com o Enterprise Integration Pack | Aplicativo Lógico do Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 0570aab14283b38f9efcc50636f0c0c1c8e3ed13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>Saiba mais sobre certificados e o Enterprise Integration Pack
## <a name="overview"></a>Visão geral
A integração corporativa usa certificados para proteger as comunicações B2B. Você pode usar dois tipos de certificados em seus aplicativos de integração corporativa:

* Certificados públicos, que devem ser adquiridos de uma autoridade de certificação (CA).
* Certificados privados, você pode emitir por conta própria. Esses certificados são chamados de certificados autoassinados.

## <a name="what-are-certificates"></a>O que são certificados?
Certificados são documentos digitais que verificam a identidade dos participantes de comunicações eletrônicas, e também servem para proteger as comunicações eletrônicas.

## <a name="why-use-certificates"></a>Por que usar certificados?
Às vezes, as comunicações B2B devem ser mantidas confidenciais. A integração corporativa usa certificados para proteger essas comunicações de duas formas:

* Criptografando o conteúdo das mensagens
* Assinando digitalmente as mensagens  

## <a name="upload-a-public-certificate"></a>Carregar um certificado público

Para usar um *certificado público* em seus aplicativos lógicos com recursos de B2B, primeiro você precisa carregá-lo em sua conta de integração.  

Depois de carregar um certificado, ele fica disponível para ajudar você com a proteção de suas mensagens B2B durante a definição de suas propriedades nos [contratos](logic-apps-enterprise-integration-agreements.md) criados.  

Estas são as etapas detalhadas para carregar os certificados públicos em sua conta de integração depois de entrar no Portal do Azure:

1. Selecione **Mais serviços** e insira **integração** na caixa de filtro de pesquisa. Selecione **Contas de Integração** na lista de resultados     
![Selecionar Procurar](media/logic-apps-enterprise-integration-certificates/overview-1.png)  
2. Selecione a conta de integração à qual você deseja adicionar o certificado.  
![Selecione a conta de integração à qual você deseja adicionar o certificado](media/logic-apps-enterprise-integration-certificates/overview-3.png)  
3. Escolha o bloco **Certificados** .  
![Escolha o bloco Certificados](media/logic-apps-enterprise-integration-certificates/certificate-1.png)
4. Na folha **Certificados** que é aberta, selecione o botão **Adicionar**.   
![Selecionar o botão Adicionar](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
5. Insira um **Nome** para o certificado e selecione o tipo de certificado como **público** na lista suspensa.  
6. Selecione o ícone de pasta no lado direito da caixa de texto **Certificado**. Quando o seletor de arquivos for aberto, encontre e selecione o arquivo de certificado que você deseja carregar em sua conta de integração.
7. Selecione o certificado, e selecione **OK** no seletor de arquivo. Isso valida e carrega o certificado em sua conta de integração.
8. Por fim, volte à folha **Adicionar certificado** e selecione o botão **OK**.  
![Selecionar o botão OK](media/logic-apps-enterprise-integration-certificates/certificate-3.png)  
9. Escolha o bloco **Certificados** . Você deverá ver o certificado recém-adicionado.  
![Consulte o novo certificado](media/logic-apps-enterprise-integration-certificates/certificate-4.png)  

## <a name="upload-a-private-certificate"></a>Carregar um certificado privado

Para usar um *certificado privado* em seus aplicativos lógicos com recursos de B2B, carregue um certificado privado em sua conta de integração por meio das seguintes etapas

1. [Carregar sua chave privada no Key Vault](../key-vault/key-vault-get-started.md "Saiba mais sobre o Key Vault") e forneça um **Nome de Chave** 
   
   > [!TIP]
   > Você deve autorizar o Aplicativos Lógicos a executar operações no Key Vault. Você pode conceder acesso à entidade de serviço de Aplicativos Lógicos usando o comando a seguir do PowerShell: `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  
   > 
   > 

Depois de concluir a etapa anterior, adicione um certificado particular à conta de integração.

Veja a seguir as etapas detalhadas para carregar os certificados privados em sua conta de integração depois de entrar no Portal do Azure:  
 
1. Selecione a conta de integração à qual você deseja adicionar o certificado e selecione o bloco **Certificados**.  
![Escolha o bloco Certificados](media/logic-apps-enterprise-integration-certificates/certificate-1.png)  
2. Na folha **Certificados** que é aberta, selecione o botão **Adicionar**.   
![Selecionar o botão Adicionar](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
3. Insira um **Nome** para o certificado e selecione o tipo de certificado como **privado** na lista suspensa.   
4. Selecione o ícone de pasta no lado direito da caixa de texto **Certificado**. Quando o seletor de arquivos for aberto, encontre o certificado público correspondente que você deseja carregar em sua conta de integração.   
   
   > [!Note]
   > Ao adicionar um certificado privado é importante adicionar o certificado público correspondente para mostrar nas configurações de envio e recebimento do [contrato AS2](logic-apps-enterprise-integration-as2.md) para assinar e criptografar as mensagens.
   > 
   >   

5. Selecione o **Grupo de Recursos**, **Key Vault**, **Nome da Chave** e selecione o botão **OK**.  
![Adicionar certificado](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)  
6. Escolha o bloco **Certificados** . Você deverá ver o certificado recém-adicionado.
![Consulte o novo certificado](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)  



* [Criar um contrato de B2B](logic-apps-enterprise-integration-agreements.md)  
* [Saiba mais sobre o Cofre de Chaves](../key-vault/key-vault-get-started.md "Saiba mais sobre o Cofre de Chaves")  

