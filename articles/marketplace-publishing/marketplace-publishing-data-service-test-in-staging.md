---
title: "Testando sua oferta do Serviço de Dados para o Marketplace | Microsoft Docs"
description: "Saiba como testar sua oferta do Serviço de Dados para o Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e861bd11-f74d-4d77-b4b5-23fb463644ad
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 56a8aad7484fed18b74200ffa7acf22363625a15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="testing-your-data-service-offer-in-staging"></a>Testando sua oferta do Serviço de Dados no preparo
> [!IMPORTANT]
> **Neste momento, não estamos mais realizando a integração de novos editores de Serviço de Dados. Novos serviços de dados não serão ser aprovados para listagem.** Se você tiver um aplicativo de negócios de SaaS que quer publicar no AppSource, encontre mais informações [aqui](https://appsource.microsoft.com/partners). Se você tiver aplicativos de IaaS ou serviços de desenvolvedor para publicar no Azure Marketplace, saiba mais [aqui](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Depois de concluir as duas primeiras etapas de [Criar sua conta de Desenvolvedor da Microsoft](marketplace-publishing-accounts-creation-registration.md) e [Criar sua oferta de Serviço de Dados no Portal de Publicação](marketplace-publishing-data-service-creation.md), você estará pronto para disponibilizar sua oferta no Azure Marketplace. Este tópico explica a primeira etapa, intermediária chamada “Preparo”

Preparo significa implantar a sua oferta em uma "área restrita" privada, em que você poderá testar e verificar sua funcionalidade antes de publicá-la para produção. A oferta aparecerá no preparo como se o cliente tivesse implantado.

## <a name="step-1-pushing-your-offer-to-staging"></a>Etapa 1. Enviando por push sua oferta para preparo
O envio da sua oferta para o preparo permite que você teste a oferta antes que ela se torne disponível para futuros assinantes.  Você pode ver como sua oferta será exibida e como ela funcionará para os usuários que se inscreverem nos seus dados.  

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1. Faça logon no [Portal de Publicação](https://publish.windowsazure.com)
2. Selecione **Serviços de Dados** na janela de navegação à esquerda
3. Selecione a oferta que você deseja enviar por push para o preparo. Você verá a tela acima.
4. Clique no botão **Enviar por Push para Preparo** .  
5. Se houver problemas com a oferta que precisam ser concluídos antes de enviar para o preparo, você verá uma lista exibida.  Corrija esses itens clicando em cada um deles na lista. Quando todas as correções forem feitas, clique novamente no botão **Enviar por Push para Preparo** .

Se não houver nenhum problema com a sua oferta, você verá a janela pop-up abaixo.  

Se você não planeja ou não aprovou a demonstração de sua oferta no Portal do Azure (atualmente com capacidade limitada), basta fechar a janela pop-up.

Para testar o Serviço de Dados no Portal do Azure (além do portal do DataMarket), você precisará de uma ID de Assinatura do Azure para teste.  Essa ID de Assinatura identificará a conta que terá permissão para testar sua oferta.  

Recorte e cole sua ID de Assinatura e clique na marca de seleção para continuar.

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [!NOTE]
> Essas IDs de assinaturas do Azure só são necessárias para teste e preparo no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com). Elas não são necessárias para testar no Azure Marketplace.
> 
> 

A próxima tela exibida mostra que a publicação está ocorrendo pela exibição do ícone “Em andamento” realçado em amarelo abaixo. O envio por push para o preparo leva de 10 a 15 minutos.  Se levar mais tempo, primeiro atualize seu navegador (pressione F5 no IE).  Nos casos raros em que a sua oferta ainda estiver sendo enviada por push para preparo após uma hora, clique no link Entre em contato conosco para nos informar de um problema.

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

Quando o Envio por Push para o Preparo for concluído, o ícone “Em andamento” para de se mover e o status é atualizado para “Preparado”.  Agora você está pronto para testar sua oferta.  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>Etapa 2. Testar sua oferta preparada no DataMarket
Clique no link após o texto **“Ver sua oferta do serviço em...”** para exibir a tela que o assinante verá quando sua oferta for para a produção e aparecerá no DataMarket.

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Teste ou verifique cada um dos 12 itens marcados acima para garantir que todos os logotipos, preços/transações, texto, imagens, documentação e links estão corretos e funcionando corretamente.  Esse é um bom momento para garantir que todos os valores de teste que você digitou ao criar sua oferta foram substituídos por valores reais.

1. Logotipo da oferta
2. Nome da oferta
3. Link/nome do Editor para o site de sua empresa
4. Pesquisar categorias para a oferta
5. Link de suporte de sua oferta para auxiliar os assinantes
6. Descrição contextual da oferta
7. Plano de oferta que descreve os detalhes de cobrança
8. Vincular ao código de implementação
9. Imagens de exemplo que ilustram o uso dos dados da oferta
10. Metadados de Entrada/Saída para cada serviço dentro da oferta
11. Termos de Uso da oferta
12. Visualização dos dados da oferta

Por fim, verifique se o serviço funcionará por meio do Datamarket, clicando no link “EXPLORAR ESTE CONJUNTO DE DADOS”.  Uma nova janela será aberta na ferramenta que chamamos de “Gerenciador de Serviços”, para que possa visualizar os resultados de uma consulta em seu serviço.  Nessa janela, você pode inserir os parâmetros necessários e ver os resultados exibidos em uma consulta em seu serviço.   Além disso, é exibida a URL de sua Consulta.  

> [!NOTE]
> Examinar a descrição textual do serviço exibida na parte superior.  E se a sua oferta consistir em mais de uma chamada de serviço, clique nas guias na parte inferior para alternar para o próximo serviço para examinar e testar.
> 
> 

## <a name="next-step"></a>Próxima etapa
Se você estiver tendo problemas e precisar de ajuda para resolvê-los, entre em contato com o [Suporte do Editor do Azure](http://go.microsoft.com/fwlink/?LinkId=272975).

Se você estiver satisfeito e pronto para publicar sua oferta, leia a documentação [Solicitar aprovação para enviar por Push para a produção](marketplace-publishing-push-to-production.md) .

## <a name="see-also"></a>Consulte também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

