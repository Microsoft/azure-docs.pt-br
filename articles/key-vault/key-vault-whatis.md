---
title: "O que é o Cofre da Chave do Azure? | Microsoft Docs"
description: "O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Usando o Cofre da Chave do Azure, os clientes podem criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, arquivos .PFX e senhas) usando chaves que são protegidas por HSMs (módulos de segurança de hardware)."
services: key-vault
documentationcenter: 
author: cabailey
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/19/2017
ms.author: cabailey
ms.openlocfilehash: 3b679a2a8ba91a8a9dec0534a3d8930fcaa7afcf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-key-vault"></a>O que é o Cofre da Chave do Azure?
O Cofre da Chave do Azure está disponível na maioria das regiões. Para obter mais informações, consulte a [Página de preços do Cofre da Chave](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução
O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Usando o Cofre de Chaves, você pode criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, arquivos .PFX e senhas) usando chaves que são protegidas por HSMs (módulos de segurança de hardware). Para garantia extra, você pode importar ou gerar chaves em HSMs. Se você optar por fazer isso, a Microsoft processará suas chaves em HSMs FIPS 140-2 Nível 2 validados (hardware e firmware).  

O Cofre da Chave simplifica o processo de gerenciamento de chaves e permite que você tenha controle das chaves que acessam e criptografam seus dados. Desenvolvedores podem criar chaves para desenvolvimento e teste em minutos e depois migrá-las diretamente para chaves de produção. Administradores de segurança podem conceder (e revogar) permissão a chaves conforme for necessário.

Use a tabela a seguir para entender melhor como o Cofre da Chave pode ajudar a atender às necessidades de desenvolvedores e administradores de segurança.

| Função | Problema declarado | Solucionado pelo Cofre da Chave do Azure |
| --- | --- | --- |
| Desenvolvedor de um aplicativo do Azure |"Eu quero escrever um aplicativo para o Azure que use chaves de assinatura e criptografia, mas quero que as chaves sejam externas ao aplicativo, para que a solução seja adequada a um aplicativo distribuído geograficamente. <br/><br/>Também quero que essas chaves e segredos sejam protegidos, sem precisar escrever o código sozinho. E também que essas chaves e segredos sejam fáceis de usar em meu aplicativo, com desempenho ideal.” |√ As chaves são armazenadas em um cofre e invocadas pelo URI quando necessário.<br/><br/> √ As chaves são protegidas pelo Azure, usando módulos de segurança de hardware (HSMs), comprimentos da chave e algoritmos padrão da indústria.<br/><br/> √ As chaves são processadas em HSMs que residem nos mesmos datacenters do Azure que os aplicativos. Isso permite uma maior confiabilidade e uma latência reduzida do que quando as chaves ficam em um local separado, como localmente. |
| Desenvolvedor de SaaS (software como serviço) |"Eu não quero a responsabilidade ou ser o possível culpado por problemas com as chaves e segredos de locatário dos meus clientes. <br/><br/>Quero que os clientes tenham e gerenciem suas chaves para que eu possa me concentrar em fazer o que faço melhor, que é fornecer os recursos centrais do software." |√ Os clientes podem importar suas próprias chaves para o Azure e gerenciá-las. Quando um aplicativo de SaaS precisa executar operações criptográficas usando as chaves de seus clientes, o Cofre de Chaves faz essas operações em nome dele. O aplicativo não vê as chaves dos clientes. |
| Diretor-chefe de segurança (CSO) |"Quero saber que nossos aplicativos estão em conformidade com HSMs FIPS 140-2 Nível 2, para um gerenciamento de chaves seguro. <br/><br/>Quero garantir que minha organização controle o ciclo de vida da chave e possa monitorar seu uso. <br/><br/>E embora usemos vários recursos e serviços do Azure, quero gerenciar as chaves de um único local no Azure. |√ Os HSMs têm certificação FIPS 140-2 Nível 2.<br/><br/>√ O Cofre de Chaves foi projetado para que a Microsoft não veja nem extraia suas chaves.<br/><br/>√ Registro em log quase em tempo real do uso da chave.<br/><br/>√ O cofre fornece uma única interface, independentemente de quantos cofres você tenha no Azure, quais sejam as regiões com suporte e quais aplicativos as usem. |

Qualquer pessoa com uma assinatura do Azure pode criar e usar cofres de chaves. Embora o Cofre da Chave beneficie desenvolvedores e administradores de segurança, ele pode ser implementado e gerenciado pelo administrador de uma organização que gerencie outros serviços do Azure para a organização. Por exemplo, esse administrador faria logon com uma assinatura do Azure, criaria um cofre para a organização na qual as chaves seriam armazenadas e seria responsável por tarefas operacionais, como:

* Criar ou importar uma chave ou segredo
* Revogar ou excluir uma chave ou segredo
* Autorizar usuários ou aplicativos a acessar o cofre da chave para que eles possam gerenciar ou usar suas chaves e segredos
* Configurar o uso de chaves (por exemplo, assinar ou criptografar)
* Monitorar o uso de chaves

Esse administrador, então, forneceria aos desenvolvedores URIs a serem chamados de seus aplicativos e forneceria ao administrador de segurança as informações de log de uso de chave. 

   ![Visão geral da o Cofre da Chave do Azure][1]

Os desenvolvedores também podem gerenciar as chaves diretamente, por meio de APIs. Para saber mais, confira o [guia do desenvolvedor do Cofre da Chave](key-vault-developers-guide.md).

## <a name="next-steps"></a>Próximas etapas
Para ver um tutorial de introdução para um administrador, consulte [Introdução ao Cofre da Chave do Azure](key-vault-get-started.md).

Para saber mais sobre o log de uso do Cofre da Chave, confira [Log do Cofre da Chave do Azure](key-vault-logging.md).

Para obter mais informações sobre como usar as chaves e os segredos com o Cofre de Chaves do Azure, consulte [Sobre Chaves, Segredos e Certificados](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
