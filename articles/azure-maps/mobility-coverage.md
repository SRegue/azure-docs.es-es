---
title: Cobertura de movilidad (transporte público) en los servicios de Mobility de Microsoft Azure Maps (versión preliminar)
description: Conozca qué nivel de cobertura proporcionan los servicios de Mobility de Azure Maps (versión preliminar) y en qué regiones en relación con las características de transporte público, como alertas de servicio y cálculo de ruta.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: b2552b9ee4ba37ad6062229958557d81e871fbe5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733410"
---
# <a name="azure-maps-mobility-services-preview-coverage"></a>Cobertura de los servicios de Mobility de Azure Maps (versión preliminar)

> [!IMPORTANT]
> La versión preliminar del servicio Mobility de Azure Maps se ha retirado y ya no estará disponible ni será compatible después del 5 de octubre de 2021. Todas las demás API y servicios de Azure Maps no se ven afectados por este anuncio de retirada.
> Para más información, consulte [Retirada de la versión preliminar del servicio Mobility de Azure Maps](https://azure.microsoft.com/updates/azure-maps-mobility-services-preview-retirement/).

Los [servicios de Mobility](/rest/api/maps/mobility) de Azure Maps mejoran el tiempo de desarrollo de las aplicaciones con características de transporte público, como el cálculo de rutas de transporte y la búsqueda de paradas cercanas. Los usuarios pueden recuperar información detallada sobre las paradas del transporte, las líneas y los horarios. Los servicios de Mobility también permiten a los usuarios recuperar las geometrías de paradas y líneas, las alertas de paradas, líneas y áreas de servicio, así como las llegadas de transporte público en tiempo real y las alertas del servicio. Además, los servicios de Mobility proporcionan funcionalidades de cálculo de ruta con opciones de planeamiento de trayectos mixtos. El planeamiento de trayectos mixtos incluye caminar, bicicleta y transporte público en el mismo viaje. Los usuarios pueden acceder a itinerarios paso a paso detallados y mixtos.

Azure Maps no ofrece el mismo nivel de información y precisión en todas las ciudades ni en todas las regiones o países. La capacidad para llamar a los datos de transporte depende del área metropolitana. Además, es posible que los datos no incluyan todas las opciones de transporte público ni todas las empresas que prestan servicio al área metropolitana.

En la siguiente tabla se proporciona información sobre la cobertura de los servicios de Mobility de Azure Maps.

| Símbolo | Significado |
|--------|---------|
| *      |Cobertura casi completa para el país o la región.|

## <a name="americas"></a>América

| País/región |  Ciudad (área metropolitana) |
|----------------|---------|
| Antigua y Barbuda | Antigua y Barbuda* |
| Argentina       | <p>Azul, Bahía Blanca, Buenos Aires, Caleta Olivia, Catamarca, Chivilcoy, Comodoro Rivadavia, Concordia, Córdoba, Corrientes, General Pico, Gualeguaychu, La Rioja, Mar del Plata, Mendoza, Miramar, Necochea, Neuquén, Oberá, Olavarría, Paraná, Posadas, Rafaela, Rio Tercero, Rosario, Salta, San Carlos de Bariloche, San Luis, San Miguel de Tucumán, San Pedro, Santa Fe, Tandil, Ushuaia, Victoria, Viedma, Villa María</p>|
| Barbados       |  Barbados* |
| Brasil         | <p>Angra dos Reis, Anápolis, Apucarana, Aracaju, Araraquara, Araxa, Araçatuba, Atibaia, Bage, Barretos, Bauru, Bebedouro, Belem, Belo Horizonte, Blumenau, Boa Vista, Botucatu, Brasilia, Caldas Novas, Campina Grande, Campinas, Campo Belo, Campo Grande, Caraguatatuba, Caratinga, Cascavel, Cataguases, Caxias, Leopoldina e Região, Catalão, Caxias do Sul, Chapecó, Cianorte, Conselheiro Lafaiete, Corumbá, Criciúma, Cruzeiro do Sul, Cuiabá, Curitiba, Curitibanos, Curvelo, Diamantina, Divinópolis, Dourados, Estrela, Feira de Santana, Fernando de Noronha, Florianópolis, Fortaleza, Foz do Iguaçu, Franca, Garanhuns, Goiania, Governador Valadares, Guarapuava, Imperatriz, Ipatinga, Irati, Itabira, Itabuna, Itajaí, Itajuba, Ituiutaba, Jaguarao, Jaraguá do Sul, Joao Pessoa, Joinville, Juazeiro do Norte, Juiz de Fora, Jundiaí, Lages, Lavras e Regiao, Lucas do Rio Verde, Londrina, Macapa, Macaé, Maceió, Mafra e Rio Negro, Manaus, Manhuacu, Maringá, Marília, Monte Carmelo, Montes Claros, Mossoró, Natal, Osorio, Ourinhos, Ouro Preto, Palmas, Paracatu, Paranaguá, Parnaíba, Passo Fundo, Passos, Pato Branco, Patos de Minas, Patrocínio, Pelotas, Picos, Piracicaba, Pirapora, Pocos de Caldas, Ponta Grossa, Porto Alegre, Porto Ferreira, Porto Seguro, Porto Velho, Praia Grande, Recife, Ribeirão Preto, Rio, Rio Branco, Rio Verde, Rondonópolis, Salinas, Salvador, Santa Cruz do Sul, Santa Maria, Santa Rita do Sapucaí, Santarem, Santiago del Estero, Santos, Sao Gabriel do Oeste, Sao Joao del Rei, Tiradentes e Regiao, Sao Jose do Rio Preto, Sao Mateus, Sao Paulo, Sorocaba, São Carlos, São Francisco do Sul, São José dos Campos, São Lourenço, São Luís, Taubaté, Telemaco Borba, Teofilo Otoni, Teresina, Toledo , Três Lagoas, Tucurui, Ubatuba, Uberaba, Uberlândia, Ubá, Uruguaiana, Varginha, Vicosa, Videira & Fraiburgo, Vitória, Vitória da Conquista, Volta Redonda, Votuporanga </p>|
| Canadá | Banff (AB), Brandon (MB), Calgary (AB), Chatham-Kent (ON), Comox Valley (BC), Cowichan Valley (BC), Cranbrook (BC),Edmonton (AB), Fort St. John, Fredericton (NB), Greater Sudbury (ON), Greater Vancouver (BC), Halifax (NS), Kamloops (BC), Kelowna - Vernon (BC), Kingston (ON), London (ON), Moncton (NB), Montreal (QC), Nanaimo (BC), Ottawa (ON), Prince George (BC), Québec City (QC), Red Deer (AB), Regina (SK), Rimouski (QC), Saskatoon (SK), Sherbrooke (QC), Southwest British Columbia (BC), Squamish (BC), St. John's (NL), Sunshine Coast, Thunder Bay (ON), Toronto (ON), Victoria (BC), West Kootenay (BC), Whistler (BC), Windsor (ON), Winnipeg (MB), Woodstock</p>|
| Chile  | <p>Antofagasta, Arica, Aysén, Chillán, Concepción, Constitución, Copiapó, Curicó, Iquique, La Serena y Coquimbo, Linares, Los Angeles (Chile), Los Lagos, Punta Arenas, Rancagua, Santiago, Talca, Temuco, Valdivia, Valparaíso, Viña del Mar</p>|
| Colombia | <p>Barranquilla, Bogotá, Bucaramanga, Cali, Cartagena, Ibagué, Medellín, Pasto, Popayán, Santa Marta, Sincelejo, Valledupar</p>|  
| Costa Rica | San José|
| República Dominicana | Santo Domingo |
| Ecuador | Cuenca, Guayaquil, Loja, Manta, Milagro|
| El Salvador | San Salvador |
 | Guatemala | Ciudad de Guatemala (GT) |
| México | Acapulco, Aguascalientes, Cancun, Durango, Mexico City, Guadalajara, Lion, Merida, Monterrey, Puebla, Puerto Vallarta, Querétaro, San Luis Potosi, Tijuana, Torreon|
| Nicaragua | Managua |
| Panamá | Panamá*|
| Perú | Cusco, Lima |
| Puerto Rico | San Juan |
| Surinam | Paramaribo |
| Uruguay | Montevideo, Paysandu, Punta del Este, Salto |
| Estados Unidos de América | <p>Albany (GA), Albuquerque (NM), Anchorage (AK), Ann Arbor (MI), Appleton-Oshkosh-Neenah (WI), Asheville (NC), Athens (GA), Athens (OH), Atlanta (GA), Austin (TX), Bakersfield (CA), Baltimore (MD), Bend-Redmond (OR), Condado de Berkshire (MA), Birmingham (AL), Bloomington (IN), Boise (ID), Boston (MA), Boulder (CO), Bowling Green (KY), Condado de Brevard (FL), Buffalo (NY), Butte (MT), Cape Cod (MA), Condado de Centre (PA), Champaign-Urbana (IL), Charleston (SC), Charleston (WV), Charlotte (NC), Charlottesville (VA), Chattanooga (TN), Cheyenne (WY), Chicago (IL), Cincinnati (OH), Condado de Citrus (FL), Cleveland (OH), Coachella Valley (CA), Colorado Springs (CO), Columbia (TN), Columbia (SC), Columbus (OH), Corpus Christi (TX), Dallas/Forth Worth (TX), Dayton (OH), Delaware, Denver (CO), Des Moines (IA), Detroit (MI), Duluth (MN), El Paso (TX), Eugene (OR), Fairbanks (AK), Fargo (ND), Fayetteville (NC), Flagstaff (AZ), Flint (MI) Fort Collins (CO), Fort Wayne (IN), Fresno (CA), Gainesville (FL), Grand Forks (ND), Grand Rapids (MI), Green Bay (WI), Greensboro (NC), Greenville (SC), Gunnison (CO), Hampton Roads (VA), Hanford (CA), Hartford (CT), Condado de Hernando (FL), Hinesville (GA), Honolulu (HI), Houston (TX), Condado de Humboldt (CA), Huntsville (AL), Indianapolis (IN), Ithaca (NY), Jackson (MS), Jackson (TN), Jacksonville - Condado de San Juan (FL), Johnson City (TN), Jonesboro (AR), Joplin (MO), Juneau (AK), Kalamazoo (MI), Kalispell (MT), Kansas City (MO), Kauai (HI), Ketchum (ID), Knoxville (TN), Lafayette (IN), Lancaster (PA), Lansing (MI), Laredo (TX), Las Vegas (NV), Lawrence (KS), Condado de Lee (FL), Lexington (KY), Condado de Lincoln (OR), Little Rock (AR), Los Ángeles (CA), Louisville (KY), Lubbock (TX), Madison (WI), Manchester (NH), McAllen (TX), Memphis (TN), Miami (FL), Milwaukee-Waukesha (WI), Minneapolis-St. Paul (MN), Missoula (MT), Modesto (USA), Moline (IL), Condado de Monroe (PA), Montgomery (AL), Morgantown (WV), Nashville (TN), Navajo Nation), New Haven (CT), Nueva Orleans (LA), NYC-NJ Area(NY), Ocala (FL), Condado de Okaloosa (FL), Oklahoma City (OK), Omaha (NE), Orlando (FL), Palm Desert (CA), Panama City (FL), Pensacola (FL), Peoria (IL), Philadelphia (PA), Phoenix (AZ), Pittsburgh (PA), Portland (ME), Portland (OR), Racine (WI), Raleigh (NC), Redding (CA), Reno & Lake Tahoe (NV), Richmond (VA), Roanoke Valley (VA - Lynchburg), Rochester (NY), Rocky Mount (NC), Parque Nacional de las Montañas Rocosas (CO), Rogue Valley (OR), Roseburg (OR), Roseville (CA), Sacramento (CA), Salem (OR), Salt Lake City (UT), San Antonio (TX), San Diego (CA), San Luis Obispo (CA), Santa Barbara (CA), Santa Fe (NM), Sarasota (FL), Savannah (GA), Seacoast Region (NH), Seattle-Tacoma-Bellevue (WA), SF Bay Area (CA), SF-San Jose Area (CA), Sioux City (IA), Sioux Falls (SD), Sitka (AK), Spokane (WA), Springfield (MA), South Bend (IN), Springfield (IL), Springfield (Mass), St. George (UT), St. Louis (MO), Stockton (CA), Syracuse-Utica (NY), Tallahassee (FL), Tampa-St. Petersburg (FL), Terre Haute (IN), Toledo (OH), Topeka (KS), Traverse City (MI), Tucson (AZ), Tulsa (OK), Vermont, Victorville (CA), Condado de Volusia (FL), Waco (TX), Washington (DC), Waterbury (CT), Wichita (KS), Wichita Falls (TX) Wilmington (NC), Yakima (WA), Youngstown (OH), Condado de York (PA), Condado de Yuma (AZ)</p>|
| +Islas Vírgenes de EE. UU. | EE. UU. Islas Vírgenes* |
| Venezuela | Caracas |

## <a name="asia-pacific"></a>Asia Pacífico

| País/región |  Ciudad (área metropolitana) |
|--------|---------|
| Australia | <p>Adelaide, Alice Springs, Bowen, Brisbane, Bundaberg QLD, Burnie, Cairns, Canberra, Darwin, Gladstone, Hobart, Innisfail, Launceston, Mackay, Magnetic Island, Maryborough-Hervey Bay, Melbourne, New South Wales, Perth, RockHampton, South East Queensland, Sydney, Toowoomba, Townsville, Victoria, Warwick, Yeppoon</p> |
| Brunéi | Bandar Seri Begawan |
| China | <p> Changchun, Changsha, Chengdu, Chongqing, Dalian, Datong, Dongguan, Hangzhou, Harbin, Jiangyin, Jinan, Nanjing, Nantong, Ningbo, Pingdingshan, Qingdao, Shenyang, Suzhou, Tangshan, Tianjin, Weifang, Wuhan, Wuxi, Yantai, Yixing, Zhuhai, Shanghai, Beijing, Guangzhou, Shenzhen, Zhengzhou</P>|
| RAE de Hong Kong | RAE de Hong Kong*|
| RAE de Macao | RAE de Macao*|
| Maldivas | Male |
| India | Ahmedabad, Bangalore, Delhi, Hyderabad, Bombay, Mysore, Pune|
| Indonesia | Bandung, Banjarmasin, Banyuwangi, Batam, Denpasar, Jakarta, Kediri, Malang, Palembang, Semarang, Surabaya, Surakarta, Yogyakarta |
| Japón | Hokkaido, Prefectura de Shizuoka, Tokio, Wakkanai, Prefectura de Yamanashi |
| Malasia | Ipoh, Johar Bahru, Kuala Lumpur, Kuantan, Penang |
| Nueva Zelanda | Auckland, Christchurch, Dunedin, Queenstown, Timaru, Wellington|
| Filipinas | Manila |
| Singapur | Singapur* |
| Corea del Sur | Busan, Seúl |
| Taiwán | Condado de Changhua, Taipei |
| Tailandia | Bangkok, Chiang Mai |
| Uzbekistán | Samarkand |
| Vietnam | Hanoi, Ciudad Ho Chi Minh |

## <a name="europe"></a>Europa

| País/región |  Ciudad (área metropolitana) |
|----------------|---------|
| Andorra        | Andorra la Vella |
| Austria        | Viena |
| Belarús        | Gomel, Grodno, Polotsk & Novopolotsk, Zhlobin, Vileyka, Maladziečna, Minsk, Rechytsa |
| Bélgica        | Bélgica* |
| Bolivia        | La Paz, Santa Cruz de la Sierra |
| Bosnia y Herzegovina | Sarajevo |
| Bulgaria       | <p>Balchik, Blagoevgrad, Burgas, Dobrich, Gabrovo, Haskovo, Kardzhali, Lovech, Nessebar, Pazardzhik, Pernik, Pleven, Plovdiv, Ruse, Shumen, Sliven, Stara Zagora, Vratsa, Yambol, Varna, Veliko, Sofía</P> |
| Croacia | Crikvenica, Dubrovnik, Rijeka, Slovanski Brod, Zagreb |
| Chipre | Larnaca, Limassol, Nicosia |
| República Checa | Brno, Jablonec, Karlovy Vary, Liberec, Ostrava, Praga |
| Dinamarca   | Dinamarca* |
| Estonia   | Estonia* |
| Finlandia   | Hämeenlinna, Helsinki, Joensuu, Jyväskylä, Kajaani, Kouvola - Kotka, Kuopio, Lappeenranta, Mikkeli, Oulu, Pori, Rovaniemi, Seinäjoki, Tampere, Turku, Vaasa|
| Francia    | <p>Amberieu-en-Bugey, Amiens, Angers, Annecy, Annonay, Arras, Aubenas, Bayonne, Besançon, Blois, Bordeaux, Boulogne sur Mer, Brest, Briançon, Cannes, Châlons-en-Champagne, Chartres, Clermont-Ferrand, Colmar, Costa Azul, Dax, Dijon, Grenoble, Haguenau, La Rochelle, Le Mans, Lens, Lille, Lorient, Lyon, MACS, Marsella y Provenza, Metz, Millau, Mont-de-Marsan, Montpellier, Mulhouse, Nancy, Nantes, Niza, Nimes, Normandía, Nyons, París, Poitiers, Privas, Quimper, Rennes, Saint Malo, Saint-Étienne, Saint-Nazaire, Saintes, Sarrebourg, Sete, Estrasburgo, Tarbes, Toulouse, Tours</P> |
| +Guayana Francesa | Cayena |
| +Nueva Caledonia | Nouméa  |
| Georgia | Tbilisi |
| Alemania | <p>Berlín, Brandeburgo, Bremen & Niedersachsen, Colonia, Eisenach, Fráncfort, Hamburgo, Karlsruhe, Mainz, Múnich - Múnich, región Rhein-Neckar, región Rhein-Ruhr, Stuttgart, Titisee-Neustadt, Ulm</P> |
| Grecia | <p>Atenas, Arta, Amorgos, Chania, Corfu, Chios Kos, Heraklion, Ioannina, Kavala, Kalamata, Khios, Komotini, Kos, Larissa, Meganisi, Milos, Mykonos, Patra, Rethimno, Rodas, Santorini, Serres, Syros, Tinos, Tesalónica, Veria, Volos, Xanthi </P> |
| Hungría | Budapest, Gyor, Miskolc, Nograd County, Pesc, Szeged, Székesfehérvár |
| Islandia | Ísland - Islandia* |
| Irlanda | Irlanda* |
| Italia   | <p>Agrigento, Alejandría, Ancona, Bari, Bolonia - Bolonia, Cagliari - Cerdeña, Campobasso, Catania e Messina, Cosenza, Crema, Cremona, Crotone, Cuneo, Firenze - Florencia, Foggia, Genova - Genoa, Iglesias, La Spezia, Lecce, Matera, Milán - Milán, Nápoles - Nápoles, Padova, Palermo, Parma, Perugia, Pescara, Pisa, Potenza, Roma - Roma, Siena e Grosseto, Siracusa - Siracusa, Taranto, Turín - Turín, Trento, Trieste, Udine, Venecia - Venecia, </p> |
| Letonia | Rīga |
| Liechtenstein | Liechtenstein* |
| Lituania | Druskininkai, Kauno, Klaipėda, Panevėžys, Vilna |
| Luxemburgo | Luxemburgo* |
| Moldova | Chisinau |
| Montenegro | Podgorica |
| Países Bajos | Países Bajos* |
| Noruega | Noruega* |
| Polonia | <p>Breslavia, Białystok, Bydgoszcz, Elbląg, Gorzow, Kętrzyna, Krakow, Leszno, Lodz, Lublin, Mrągowo, Olsztyn, Poznań, Rzeszów, Sanok, Starachowice, Świonujście, Szczecin, Tricity, Warsaw, Wodzisław Śląski, Breslavia, Zakopane</p> |
| Portugal | Braganza, Coimbra, Funchal, Leiria, Lisboa, Portimao, Porto|
| Malta | Malta* |
| Rumanía | <p>Alba Iulia, Arad, Bistrița, Brăila, Braşov, Bucharest, Buzau, Cluj Napoca, Constanța, Craiova, Deva, Focșani, Galati, Iaşi, Miercurea Ciuc, Oradea, Piatra Neamt, Pitești, Ploieşti, Reșița, Satu Mare, Sibiu, Suceava, Targu Mures, Timisoara, Tulcea, Zalau</p> | 
| Rusia  | Kaliningrad, Rostov-on-Don, Volgograd, Yekaterinburg, Kazan, Kirov, Krasnodar, Moscow, Nalchik, Nizhny Novgorod, Novosibirsk, Noyabrsk, Omsk, Oryol, Perm, St Petersburg, Pyatigorsk, Tver, Tomsk |
| Serbia  | Belgrado, Kragujevac, Nis, Novi Sad, Valjevo, Subotica | 
| Eslovaquia | Banská Bystrica, Bratislava, Kosice, Presov, Prievidza, Ruzomberok a Liptovsky Mikulas, Stará Ľubovňa, Trencin |
| Eslovenia | Koper, Liubliana |
| España    | <p>Albacete, Coruña, Alicante, Almería, Asturias, Ávila, Badajoz, Bahía de Cádiz, Barcelona, Bilbao, Burgos, Cáceres, Campo de Gibraltar, Castellón de la Plana, Ceuta, Ciudad Real, Córdoba, Cuenca, El Hierro, Ferrol, Fuerteventura, Gran Canaria, Granada, Huelva, Huesca, Ibiza, Jaén - Úbeda, La Gomera, La Palma, Lanzarote, León, Lleida, Logroño, Lugo, Madrid, Málaga, Mallorca, Melilla, Menorca, Mérida, Murcia, Ourense, Palencia, Pamplona, Salamanca, San Sebastián, Santander, Santiago de Compostela, Segovia, Sevilla, Soria, Tarragona - Reus, Tenerife, Toledo, Valencia, Valladolid, Vigo, Vitoria-Gasteiz, Zaragoza</p> |
| Suecia | Goteborg/Gothenburg/Jonkoping, Malmö kommun - Malmö, Norrköping och Linköping, Stockholm, Sundsvall |
| Suiza | Basilea, Ginebra, Yverdon-les-Bains, Zúrich | 
| Turquía | Adana-Mersin, Ankara, Antalya, Balıkesir, Bilecik, Bolu, Bursa, Çorum, Denizli, Duzce, Edirne, Elazig, Eskisehir, Istanbul, Izmir-Aydin, Kahramanmaras, Kayseri, Konya, Malatya, Muğla, Samsun, Şanlıurfa, Trabzon |
| Reino Unido | Anglia Oriental, Tierras Medias Orientales, Londres y sureste, noreste, noroeste, Irlanda del Norte, Escocia, sudoeste, Gales, Tierras Medias Occidentales, Yorkshire |
| Ucrania | Kharkiv, Zhytomyr, Kyiv, Lviv, Chernivtsi |

## <a name="middle-east-and-africa"></a>Oriente Medio y África

| País/región |  Ciudad (área metropolitana) |
|---------|---------|
| Bahréin | Bahréin* |
| Burkina Faso | Ouagadougou |
| Congo (RDC) | Kinshasa |
| Egipto | El Cairo    |
| Israel| Israel*  |
| Kenia | Nairobi  |
| Madagascar | Antananarivo |
| Marruecos | Casablanca, Essaouira, Khouribga, Tetuán|
| Qatar| Doha|
| Arabia Saudí | Thuwal |
| Senegal | Dakar |
| Sudáfrica | Ciudad del Cabo |
| Túnez | Kairouan |
| Emiratos Árabes Unidos  | Abu Dhabi, Dubái |


## <a name="next-steps"></a>Pasos siguientes

Aprenda a solicitar datos de transporte público mediante los servicios de Mobility (versión preliminar):

> [!div class="nextstepaction"]
> [Cómo solicitar datos de tránsito](how-to-request-transit-data.md)

Aprenda a solicitar datos en tiempo real mediante los servicios de Mobility (versión preliminar):

> [!div class="nextstepaction"]
> [Cómo solicitar datos en tiempo real](how-to-request-real-time-data.md)

Exploración de la documentación de la API de los servicios de Mobility de Azure Maps (versión preliminar)

> [!div class="nextstepaction"]
> [Documentación de la API de los servicios de Mobility](/rest/api/maps/mobility)
