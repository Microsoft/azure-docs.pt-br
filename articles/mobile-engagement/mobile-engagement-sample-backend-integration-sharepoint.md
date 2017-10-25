---
title: "Azure Mobile Engagement - integração do back-end"
description: Conectar o Azure Mobile Engagement com um back-end do SharePoint para criar campanhas do SharePoint
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 06297b43-579f-46e6-8a58-961a68f9aa09
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d49f1094f4c3f170f3618f3e19e42266f9ae8858
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement---api-integration"></a>Integração da API do Azure Mobile Engagement
Em um sistema automatizado de marketing, a criação e ativação das campanhas de marketing também ocorrerem automaticamente. Para essa finalidade - o Azure Mobile Engagement permite criar essas campanhas de marketing automatizadas usando também as APIs. 

Normalmente os clientes usam a interface de front-end do Mobile Engagement para criar anúncios/pesquisas etc como parte de suas campanhas de marketing. No entanto conforme as campanhas de marketing ficam maduras, é necessário aproveitar os dados bloqueados nos sistemas de back-end (como qualquer sistema CRM ou sistema CMS como o SharePoint) para que possa ser criado um pipeline totalmente automatizado que cria campanhas em Mobile Engagement dinamicamente com base nos dados que fluem dos sistemas back-end. 

![][5]

Este tutorial passa pelo cenário em que um usuário de negócios do SharePoint preenche uma lista do SharePoint com dados de marketing e um processo automatizado pega itens da lista e conecta-se com o sistema Mobile Engagement usando as APIs REST disponíveis para criar uma campanha de marketing dos dados do SharePoint. 

> [!IMPORTANT]
> Em geral, é possível usar este exemplo como ponto de partida para entender como chamar qualquer API REST do Mobile Engagement, pois ela detalha os dois principais aspectos de chamar as APIs - autenticação e passagem de parâmetros. 
> 
> 

## <a name="sharepoint-integration"></a>Integração do SharePoint
1. A lista do SharePoint de exemplo é semelhante ao seguinte. **Title**, **Category**, **NotificationTitle**, **Message** e **URL** são usados para criar o anúncio. Há uma coluna chamada **IsProcessed** que é usada pelo processo de automação de exemplo na forma de um programa de console. É possível executar esse console do programa como um Trabalho Web do Azure para que você pode programá-lo ou usar diretamente o fluxo de trabalho do SharePoint para programar a criação e ativação do anúncio quando um item é inserido na lista do SharePoint. Neste exemplo, usamos o programa de console que vai pelos itens da lista do SharePoint e cria um anúncio no Azure Mobile Engagement para cada um deles e, em seguida, marca o sinalizador **IsProcessed** como true na criação do anúncio com êxito.
   
    ![][1]
2. Estamos usando o código do exemplo *Autenticação remota no SharePoint Online usando o modelo de objeto do cliente* [aqui](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) para autenticar com a lista do SharePoint.
3. Depois de autenticado, executamos um loop pelos itens de lista para localizar os itens recém-criados (que têm **IsProcessed** = false). 
   
         static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);
   
                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();
   
                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;
   
                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;
   
                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";
   
                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Integração do Mobile Engagement
1. Depois de encontrarmos um item que requer processamento - podemos extrair as informações necessárias para criar um anúncio do item de lista e chamada `CreateAzMECampaign` para criá-lo e subsequentemente `ActivateAzMECampaign` para ativá-lo. Essas são essencialmente chamadas da API REST chamaando para o back-end do Mobile Engagement. 
2. As APIs REST do Mobile Engagement exigem um **Cabeçalho de autenticação HTTP de esquema de autenticação básica** composto pelo `ApplicationId` e pelo `ApiKey` que você obtém do portal do Azure. Certifique-se de estar usando a chave da seção **chaves de api** e *não* da seção **chaves do sdk**. 
   
   ![][2]
   
       static string CreateAuthZHeader()
       {
           string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
           return BasicAuthzHeaderString;
       }
   
       static public string EncodeTo64(string toEncode)
       {
           byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
           string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
           return returnValue;
       }  
3. Para criar a campanha do tipo do anúncio - consulte a [documentação](https://msdn.microsoft.com/library/azure/mt683750.aspx). Você precisará se certificar de que está especificando a campanha `kind` como *anúncio* e [carga](https://msdn.microsoft.com/library/azure/mt683751.aspx) e passá-la como FormUrlEncodedContent. 
   
        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";
   
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }
4. Uma vez que o anúncio é criado, você verá algo semelhante ao seguinte no portal Mobile Engagement (observe que Estado = Rascunho e Ativado = N/A)
   
    ![][3]
5. `CreateAzMECampaign` cria uma campanha de anúncio e retorna sua identificação para o chamador. `ActivateAzMECampaign` necessita dessa Id como o parâmetro para ativar a campanha. 
   
        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }
6. Uma vez que o anúncio é ativado, você verá algo semelhante ao seguinte no portal Mobile Engagement:
   
    ![][4]
7. Assim que a campanha é ativada, os dispositivos que atendem aos critérios da campanha começarão a ver notificações. 
8. Você também observará que o item de lista marcada com IsProcessed = false foi definido como True depois que a campanha de anúncio foi criada.  

Esse exemplo criou uma campanha de anúncio simples especificando principalmente as propriedades necessárias. Você pode personalizá-la tanto quanto possível a partir do portal usando as informações [aqui](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png



