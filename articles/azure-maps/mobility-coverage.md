---
title: Cobertura de mobilidade (trânsito) no Microsoft Azure Maps serviços de mobilidade (versão prévia)
description: Saiba qual nível de cobertura os serviços de mobilidade do Azure Maps (versão prévia) fornecem em quais regiões os recursos públicos de trânsito, como roteamento e alertas de serviço.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e902f313edf22d75f6b183575c3dc8d0dd94bc1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96904747"
---
# <a name="azure-maps-mobility-services-preview-coverage"></a>Cobertura dos serviços de mobilidade do Azure Maps (versão prévia)

> [!IMPORTANT]
> Os serviços de mobilidade do Azure Maps estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Os serviços de [mobilidade](/rest/api/maps/mobility) do Azure Maps aprimoram o tempo de desenvolvimento para aplicativos com recursos de trânsito públicos, como roteamento de trânsito e pesquisa para interrupções de trânsito públicos próximos. Os usuários podem recuperar informações detalhadas sobre as interrupções de trânsito, linhas e cronogramas. Os serviços de mobilidade também permitem que os usuários recuperem geometrias de parada e de linha, alertas de interrupções, linhas e áreas de serviço, bem como entradas de trânsito e alertas de serviço em tempo real. Além disso, os serviços Mobilidade fornecem funcionalidades de roteamento com opções de planejamento de viagem multimodal. O planejamento de viagens multimodal incorpora opções de movimentação, semiciclo e trânsito público, tudo em uma viagem. Os usuários também podem acessar os roteiros passo a passo multimodais detalhados.

O mapas do Azure não fornece o mesmo nível de informações e precisão para todas as cidades e países/regiões. A capacidade de chamar dados de trânsito públicos depende da área de metrô. Além disso, os dados do mapa podem não incluir todas as opções de trânsito público e as agências que servem a área de metrô.

A tabela a seguir fornece informações de cobertura para os serviços de mobilidade do Azure Maps.

| Símbolo | Significado |
|--------|---------|
| *      |Cobertura quase completa para o país/região.|

## <a name="americas"></a>Américas

| País/Região |  Cidade (área de metrô) |
|----------------|---------|
| Antígua e Barbuda | Antígua e Barbuda * |
| Argentina       | <p>Azul, Bahía Blanca, Buenos Aires, Caleta Olivia, Catamarca, Chivilcoy, comqueimadoo Rivadavia, Concordia, nicaraguense, Corrientes, geral RsFilter, Gualeguaychu, La Rioja, Mar del Plata, Mendoza, Miramar, Necochea, Neuquén, Oberá, Olavarría, Paraná, Posadas, Rafaela, Rio Tercero, Rosario, salta, San Carlos de Bariloche, San Luis, San Miguel de Tucumán, San Pedro, Santa Fe, Tandil, Ushuaia, Victoria, Viedma, Villa María</p>|
| Barbados       |  Barbados |
| Brasil         | <p>Angra dos reis, Anápolis, Apucarana, Aracaju, Araraquara, Araxa, Araçatuba, Atibaia, Bage, Barretos, Bauru, bebedouro, Belém, Belo Horizonte, Blumenau, boa vista, Botucatu, Brasília, caldas novas, Campina moca, Campinas, campo belo, campo moca, Caraguatatuba, Caratinga, Cascavel, Cataguases, Caxias, Leopoldina e região, catalão, Caxias de Sul, Chapecó, Cianorte, Conselheiro Lafaiete, Corumbá, Criciúma, cruzeiro do Sul, Cuiabá, Curitiba, Curitibanos, Curvelo, Diamantina, Divinópolis, dourados, estrela, feira de Santana, Fernando de Noronha, Florianópolis, fortaleza, Foz do Iguaçu, franca, Garanhuns, Goiania, Governador Valadares, Guarapuava, Imperatriz, Ipatinga, Irati, Itabira, Itabuna, Itajaí, Itajuba, Ituiutaba, Jaguarao, Jaraguá Sul, Joao, Joinville, Juazeiro do Norte, juiz de consiga, Jundiaí, Lages, Lavras e regiao, Lucas do rio verde, Londrina, Macapa, Macaé, Maceió, Mafra e Rio Negro, Manaus, Manhuacu, Maringá, Marília, Monte Carmelo , Montes claros, Mossoró, Natal, Osorio, Ourinhos, ouro preto, palmas, Paracatu, Paranaguá, Parnaíba, passo fundo, passos, pato branco, patos de minas, patrocínio, Pelotas, picos, Piracicaba, Pirapora, pocos de Caldas, ponta grossa, Porto Alegre, Porto Ferreira, porto seguro, porto velho, praia moca, Recife, Ribeirão Preto, Rio, rio branco, Rio Verde, Rondonópolis, Salinas, Salvador, Santa Cruz do Sul, Santa Maria, Santa Rita do Sapucaí, Santarem, Santiago del Estero, Santos, são Gabriel do oeste, são joao del Rei, Tiradentes e regiao, são José do rio preto, são Mateus, são Paulo, Sorocaba, são Carlos, São Francisco do Sul, são José dos campos, São Lourenço, são Luís, Taubaté, telemaco borba, Teofilo Otoni, Teresina, caramba, três lagoas, Tucurui, Ubatuba, Uberaba, Uberlândia, Ubá, Uruguaiana, Varginha, Vicosa, videira & Fraiburgo , Vitória, vitória da conquista, voltar redonda, Votuporanga </p>|
| Canadá | Banff (AB), Brandon (MB), Calgary (AB), Chatham-Kent (ON), Comox valer (BC), Cowichan vale (BC), Cranbrook (BC), Edmonton (AB), Fort St. John, Fredericton (NB), maior Sudbury (ON), maior Vancouver (BC), Halifax (NS), Kamloops (BC), Kelowna-Vernon (BC), Kingston (ON), London (ON), Moncton (NB), Montreal (QC), Nanaimo (BC), Ottawa (ON), Príncipe George (BC), Québec City (QC), Red Deer (AB), Regina (SK), Rimouski (QC), Saskatoon (SK), Sherbrooke (QC), sudoeste British Columbia (BC), Squamish (BC), St. João (NL), costa sol, compartimento do Thunder (ON), Toronto (ON), Victoria (BC), West Kootenay (BC), Whistler (BC), Windsor (ON), Winnipeg (MB), Woodstock</p>|
| Chile  | <p>Antofagasta, Arica, Aysén, Chillán, Concepción, Constitución, Copiapó, Curicó, Iquique, la Serena y Coquimbo, Linares, Los Angeles (Chile), los Lagos, Punta Arenas, Rancagua, Santiago, Talca, Temuco, Valdivia, Valparaíso, Viña del mar</p>|
| Colômbia | <p>Barranquilla, Bogotá, Bucaramanga, Cali, Cartagena, Ibagué, Medellín, pasto, Popayán, Santa Marta, Sincelejo, Valledupar</p>|  
| Costa Rica | José San|
| República Dominicana | Santo Santo |
| Equador | Cuenca, Guaiaquil, loja, manta, Milagro|
| El Salvador | San Salvador |
 | Guatemala | Ciudad de Guatemala (GT) |
| México | Acapulco, Aguascalientes, Cancun, Durango, cidade do México, Guadalajara, Lion, Merida, Monterrey, Puebla, Porto Vallarta, Querétaro, San Luis Potosí, Tijuana, Torreon|
| Nicarágua | Manágua |
| Panamá | Panamá|
| Peru | Cusco, Lima |
| Porto Rico | Juan San |
| Suriname | Paramaribo |
| Uruguai | Montevidéu, Paysandu, Punta del Este, salto |
| Estados Unidos da América | <p>Albany (GA), Albany (NY), Albuquerque (NM), Anchorage (AK), Ana Arbor (MI) Appleton-Oshkosh-Neenah (WI), Asheville (NC), Atenas (GA), Atenas (OH), Atlanta (GA), Austin (TX), Bakersfield (CA), Baltimore (MD), Bend-Redmond (ou), Berkshire County (MA), Birmingham (AL), Bloomington (IN), Boise (ID), Boston (MA), Boulder (CO), de boliche (KY), Brevard County (FL), Buffalo (NY), Butte (MT), Cape Cod (MA), Condado de centro (PA), Champaign-Urbana (IL), Charleston (SC), Charleston (WV), Charlotte (NC), Charlottesville (VA), Chattanooga (TN), Cheyenne (WY), Chicago (IL), Cincinnati (OH), região de cítrico (FL), Cleveland (OH), Coachella vale (CA), molas de Colorado (CO), Colúmbia (TN), Colúmbia (SC), Columbus (OH), Corpus Christi (TX), Dallas/em vale a pena (TX), Dayton (OH), Delaware, Denver (CO), Des Moines (IA), Detroit (MI), Duluth (MN), El Paso (TX), Eugene (ou), Fairbanks (AK), Fargo (ND) , Fayetteville (NC), Flagstaff (AZ), Flint (MI) Fort Collins (CO), Fort Wayne (IN), Fresno (CA), Gainesville (FL), Grand Bifurcates (ND), Grand Rapids (MI), gabinete verde (WI), Greensboro (NC), Greenville (SC), Gunnison (CO), Hampton estradas (VA), Hanford (CA), Hartford (CT), Hernando County (FL), Hinesville (GA), Honolulu (Olá), Houston (TX), Humboldt County (CA), Huntsville (AL), Indianápolis (IN), Ithaca (NY), Jackson (MS), Jackson (TN), Jacksonville-St. Condado de João (FL), Johnson City (TN), Jonesboro (AR), Joplin (MO), Juneau (AK), Kalamazoo (MI), Kalispell (MT), Kansas City (MO), Kauai (HI), Ketchum (ID), Knoxville (TN), Lafayette (IN), Lancaster (PA), Lansing (MI), Laredo (TX), Las Vegas (NV), Lawrence (KS), Condado de Lee (FL), Lexington (KY), Lincoln County (ou), Little Rock (AR), Los Angeles (CA), Louisville (KY), Lubbock (TX), Madison (WI), Manchester (NH) , McAllen (TX), Memphis (TN), Miami (FL), Milwaukee-Waukesha (WI), Minneapolis-St. Paul (MN), Missoula (MT), modesto (EUA), Moline (IL), Monroe County (PA), Montgomery (AL), Morgantown (WV), Nashville (TN),-Navajo nação), New foram (CT), New Orleans (LA), NYC-NJ Area (NY), Ocala (FL), Okaloosa County (FL), Oklahoma City (OK), Omaha (NE), Orlando (FL), Palm deserto (CA), Panamá City (FL), Pensacola (FL), Peoria (IL), Filadélfia (PA), Phoenix (AZ), Pittsburgh (PA), Portland (ME), Portland (ou), Racine (WI), Raleigh (NC), Redding (CA), Reno & Lake Tahoe (NV), Richmond (VA), Roanoke vale (VA-Lynchburg), Rochester (NY), Rockford (IL), Rocky Mount (NC), Rocky Mountain National Park (CO), vale não autorizado (ou), Roseburg (ou), Roseville (CA), Sacramento (CA), Salem (ou), Salt Lake City (UT), San Antonio (TX), San Diego (CA), San Luis Obispo (CA), Santa Bárbara (CA), Santa Fe (NM), Sarasota (FL) , Savannah (GA), região do macoast (NH), Seattle-Tacoma-Bellevue (WA), área do compartimento de it (CA), SF-San área de José (CA), Sioux City (IA), Sioux cai (SD), Sitka (AK), Spokane (WA), Springfield (MA), curva sul (IN), Springfield (IL), Springfield (massa), St. George (UT), St. Louis (MO), Stockton (CA), Syracuse-Utica (NY), Tallahassee (FL), tampa-St. Petersburgo (FL), Terre Haute (IN), caramba (OH), Topeka (KS), cidade de atravessamento (MI), Tucson (AZ), Tulsa (OK), Vermont, Victorville (CA), Volusia County (FL), Waco (TX), Washington (DC), Waterbury (CT), Wichita (KS), Wichita is (TX) Wilmington (NC), Yakima (WA), Youngstown (OH), York County (PA) e Yuma County (AZ)</p>|
| + Ilhas Virgens dos EUA | Ilhas Virgens dos EUA * |
| Venezuela | Caracas |

## <a name="asia-pacific"></a>Pacífico Asiático

| País/Região |  Cidade (área de metrô) |
|--------|---------|
| Austrália | <p>Adelaide, molas de Alice, Bowen, Brisbane, Bundaberg QLD, Burne, Cairns, Camberra, Darwin, Gladstone, Hobart, Innisfail, Launceston, Mackay nesta, ilha magnética, Maryborough-Hervey Bay, Melbourne, novo Sul Gales, Perth, RockHampton, sudeste do Sul Queensland, Sydney, Toowoomba, Townsville, Victoria, Warwick, Yeppoon</p> |
| Brunei | Bandar inserir Begawan |
| China | <p> Changchun, Changsha, Chengdu, Chonquim, Dalian, Datong, Dongguan, Hangzhou, Harbin, Jiangyin, Jinan, Nanquim, Nantong, Ningbo, Pingdingshan, Qingdao, Shenyang, Suzhou, Tangshan, Tianjin, Weifang, Wuhan, Wuxi, Yantai, Yixing, Zhuhai, Xangai, Pequim, Guangzhou, Shenzhen, Zhengzhou</P>|
| RAE de Hong Kong | Rae de Hong Kong *|
| RAE de Macau | Rae de Macau *|
| Maldivas | Masculino |
| Índia | Ahmedabad, Bengaluru, Delhi, Hyderabad, Mumbai, Mysuru, Pune|
| Indonésia | Bandung, Banjarmasin, Banyuwangi, batam, Denpasar, Jacarta, Kediri, Malang, Palembang, Semarang, Surabaya, Surakarta, Yogyakarta |
| Japão | Hokkaido, Shizuoka prefeitura Saitama, Tóquio, Wakkanai, Yamanashi prefeitura Saitama |
| Malásia | Ipoh, Johar Bahru, Kuala Lumpur, Kuantan, Penang |
| Nova Zelândia | Auckland, Christchurch, Dunedin, Queenstown, Timaru, Wellington|
| Filipinas | Manila |
| Singapura | Cingapura |
| Coreia do Sul | Busan, Seul |
| Taiwan | Condado de Changhua, Taipé |
| Tailândia | Bancoc, Chiang Mai |
| Uzbequistão | Samarkand |
| Vietnã | Hanói, Ho Chi Minh City |

## <a name="europe"></a>Europa

| País/Região |  Cidade (área de metrô) |
|----------------|---------|
| Andorra        | Andorra la Vella |
| Áustria        | Viena |
| Bielorrússia        | Gomel, Grodno, Polotsk & Novopolotsk, Zhlobin, Vileyka, Maladziečna, Minsk, Rechytsa |
| Bélgica        | Bélgica |
| Bolívia        | La paz, Santa Cruz de la Sierra |
| Bósnia e Herzegovina | Sarajevo |
| Bulgária       | <p>Balchik, Blagoevgrad, Burgas, Dobrich, Gabrovo, Haskovo, Kardzhali, Lovech, Nessebar, Pazardzhik, Pernik, Pleven, Plovdiv, Ruse, Shumen, Sliven, Stara Zagora, Vratsa, Yambol, Varna, Veliko, Sófia</P> |
| Croácia | Crikvenica, Dubrovnik, Rijeka, Slovanski Brod, Zagreb |
| Chipre | Larnaca, Limassol, Nicósia |
| República Tcheca | Brno, Jablonec, Karlovy Vary, Liberec, Ostrava, Praga |
| Dinamarca   | Dinamarca |
| Estônia   | Estônia |
| Finlândia   | Hämeenlinna, Helsinque, Joensuu, Jyväskylä, Kajaani, Kouvola-Kotka, Kuopio, Lappeenranta, Mikkeli, Oulu, Pori, Rovaniemi, Seinäjoki, Tampere, Turku, Vaasa|
| França    | <p>Amberieu-en-Bugey, Amiens, Angers, Annecy, Annonay, Arras, Aubenas, Bayonne, Besançon, Blois, Bordeaux, Boulogne sur Mer, Brest, Briançon, Cannes, Châlons-en-champanhe, Chartres, Clermont-Ferrand, Colmar, Côte d'Azur, Dax, Dijon, Grenoble, Haguenau, La Rochelle, Le Mans, Lens, Lille, Lorient, Lyon, MACS, Marselha & Provence, Metz, Millau, Mont-de-Marsan, Montpellier, Mulhouse, Nancy, Nantes, boa, boa Côte d'Azur, Nimes, Normandia, Nyons, Paris, Poitiers, Privas, Quimper, Rennes, Saint Malo, Saint-Étienne, Saint-Nazaire, Saintes, Sarrebourg, sete, Strasbourg, Tarbes, Toulouse, Tours</P> |
| + Guiana francesa | Caiena |
| + Nova Caledônia | Nouméa  |
| Geórgia | Tbilisi |
| Alemanha | <p>Berlim, Brandenburg, Bremen & Niedersachsen, Cologne, Eisenach, Frankfurt, Hamburgo, Karlsruhe, Mainz, München-Munique, Rhein-Neckar região, Rhein-Ruhr região, Stuttgart, Titisee-Neustadt, Ulm</P> |
| Grécia | <p>Agrinio, Aigio, Atenas, Arta, Amorgos, Chania, Corfu, Chios Kos, Heraklion, Ioannina, Kavala, Kalamata, Khios, Komotini, Kos, Larissa, Meganisi, Milos, Mykonos, Patra, Rethimno, Rhodes, Santorini, Serres, Syros, Tinos, Thessaloniki, versãoa, Volos, Xanthi </P> |
| Hungria | Budapeste, Gyor, Miskolc, County, PESC, Szeged, Székesfehérvár |
| Islândia | Ísland-Islândia * |
| Irlanda | Irlanda |
| Itália   | <p>Agrigento, Alessandria, Ancona, Bari, Bologna-Bologne, Cagliari-Sardinia, Campobasso, Catania e Messina, Cosenza, Crema, Cremona, Crotone, Cuneo, Firenze-Florence, Foggia, Genova-Genoa, Iglesias, La Spezia, Lecce, Matera, Milano-Milão, Napoli-Naples, Padova, Palermo, Parma, Perugia, Pescara, Pisa, Potenza, Roma-Roma, Siena e Grosseto, Siracusa-Syracuse, Taranto, Torino-Turim, Trento, Trieste, Udine, Veneza-Venice, </p> |
| Letônia | Rīga |
| Liechtenstein | Liechtenstein |
| Lituânia | Druskininkai, Kauno, Klaipėda, Panevėžys, Vilnius |
| Luxemburgo | Luxemburgo |
| Moldova | Chisinau |
| Montenegro | Podgorica |
| Países Baixos | Países Baixos |
| Noruega | Noruega |
| Polônia | <p>Wrocław, Białystok, Bydgoszcz, Elbląg, Elk, Gorzow, Kętrzyna, Krakow, Leszno, Lodz, Lublin, Mrągowo, Olsztyn, Poznań, Rzeszów, Sanok, Starachowice, Świonujście, Szczecin, Tricity, Varsóvia, Wodzisław Śląski, Wrocław, Zakopane</p> |
| Portugal | Bragança, Coimbra, Funchal, Leiria, Lisboa, Portimao, Porto|
| Malta | Malta |
| Romênia | <p>Alba Iulia, Arad, Bistrița, Brăila, Braşov, Bucareste, Buzau, Cluj Napoca, Constanța, Craiova, DESVPADA, Focșani, Galati, Iaşi, Miercurea Ciuc, Oradea, Piatra Neamt, Pitești, Ploieşti, Reșița, Satu Mare, Sibiu, Suceava, Targu Mures, Timisoara, Tulcea, Zalau</p> | 
| Rússia  | Caliningrado, Rostov-on-Don, Volgogrado, Yekaterinburg, Kazan, Kirov, Krasnodar, Moscou, Nalchik, Nizhniy Novgorod, Novosibirsk, Noyabrsk, Omsk, Oryol, Perm, São Petersburgo, Pyatigorsk, Tver, Tomsk |
| Sérvia  | Beograd, Kragujevac, NIS, Novi triste, Valjevo, Subotica | 
| Eslováquia | Banská Bystrica, Bratislava, Kosice, Presov, Prievidza, Ruzomberok a Liptovsky Mikulas, Stará Ľubovňa, Trencin |
| Eslovênia | Koper, Liubliana |
| Espanha    | <p>Albacete, Corunna, Alicante, Almería, Asturias, Avila, Badajoz, Bay de Cadiz, Barcelona, Bilbao, Burgos, Caceres, campo de Gibraltar, Castellon de la plana, Ceuta, Ciudad Real, Córdoba, Cuenca, El Hierro, Ferrol, Fuerteventura, Gran Canaria, Granada, Huelva, Huesca, Ibiza, Jaén-Úbeda, la Gomera, la Palma, Lanzarote, León, Lleida, Logroño, Lugo, Madri, Malaga, Mallorca, Majorca, Melilha, Menorca, Merida, Murcia, Ourense, Palencia, Pamplona, Salamanca, San Sebastián , Santander, Santiago de Compostela, Segovia, Seville, Soria, Tarragona-Reus, Tenerife, caramba, Valência, Valladolid, Vigo, Vitoria-Gasteiz, Zaragoza-Saragossa</p> |
| Suécia | Goteborg/Gothenburg/Jonkoping, Malmö kommun-Malmö, Norrköping och Linköping, Estocolmo, Sundsvall |
| Suíça | Basel, Geneva, Yverdon-les-Bains, Zurique | 
| Turquia | Adana-Mersin, Ankara, Antalya, Balıkesir, Bilecik, Bolu, Bursa, Çorum, Denizli, Duzce, Edirne, Elazig, Eskisehir, Istambul, Izmir-Aydin, Kahramanmaras, Kayseri, Konya, Malatya, Muğla, Samsun, Şanlıurfa, Trabzon |
| Reino Unido | Leste Anglia, leste Midlands, Londres e sudeste, leste do Norte, norte da Índia, Irlanda do Norte, Escócia, sudoeste do Sul, Gales, oeste Midlands, Yorkshire |
| Ucrânia | Kharkiv, Zhytomyr, Kiev, Lviv, Chernivtsi |

## <a name="middle-east-and-africa"></a>Oriente Médio e África

| País/Região |  Cidade (área de metrô) |
|---------|---------|
| Bahrein | Bahrein |
| Burkina Faso | Ouagadougou |
| Congo | Kinshasa |
| Egito | Cairo    |
| Israel| Israel  |
| Quênia | Nairóbi  |
| Madagascar | Antananarivo |
| Marrocos | Casablanca, Essaouira, Khouribga, Tétouan|
| Catar| Doha|
| Arábia Saudita | Thuwal |
| Senegal | Dacar |
| África do Sul | Cidade do Cabo |
| Tunísia | Kairouan |
| Emirados Árabes Unidos  | Abu Dhabi, Dubai |


## <a name="next-steps"></a>Próximas etapas

Saiba como solicitar dados de trânsito usando os serviços de mobilidade (versão prévia):

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Saiba como solicitar dados em tempo real usando os serviços de mobilidade (versão prévia):

> [!div class="nextstepaction"]
> [Como solicitar dados em tempo real](how-to-request-real-time-data.md)

Explorar a documentação da API dos serviços de mobilidade do Azure Maps (versão prévia)

> [!div class="nextstepaction"]
> [Documentação da API dos serviços de mobilidade](/rest/api/maps/mobility)
