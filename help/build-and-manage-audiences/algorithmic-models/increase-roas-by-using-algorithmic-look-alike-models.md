---
title: Öka ROAS genom att använda algoritmiska (lookalike)-modeller
description: Den verkliga styrkan hos Audience Manager Look-alike-modellering kommer när ni vill utöka er baslinjepublik mot en ny uppsättning kvalitetsanvändare från datakällor från andra och tredje part. I den här självstudiekursen lär du dig hur du skapar en modell utifrån dessa data.
feature: Algorithmic Models
topics: null
activity: use
doc-type: feature video
team: Technical Marketing
thumbnail: 25188.jpg
kt: 1849
role: User, Developer, Data Engineer, Architect, Data Architect, Admin, Leader
level: Intermediate
exl-id: 6626ae11-8709-4302-9e03-0d55878d2409
source-git-commit: 2094d3bcf658913171afa848e4228653c71c41de
workflow-type: tm+mt
source-wordcount: '896'
ht-degree: 0%

---

# Öka ROAS genom att använda algoritmiska (Look-Alike) modeller i Audience Manager {#increase-roas-by-using-algorithmic-look-alike-models-in-audience-manager}

Den verkliga styrkan hos Audience Manager Look-alike [!UICONTROL Modeling] kommer när du försöker utöka din baslinje mot en ny uppsättning kvalitetsanvändare från andra parts och tredjeparts datakällor. I den här självstudiekursen får du lära dig hur du skapar en modell utifrån dessa data.

## Aktivera dataströmmar från andra leverantörer eller tredje part från Audience Marketplace {#enable-2nd-or-3rd-party-data-streams-from-the-audience-marketplace}

För att kunna använda data från andra leverantörer och tredje part i en lookalike-modell måste vi först aktivera dessa data i Audience Manager-gränssnittet. Adobe har ett stort antal tredjeparts- och tredjepartsdataleverantörer som du kan välja mellan. Dessa finns i ett självbetjäningsgränssnitt i AAM via Audience Marketplace. Navigera till Audience Marketplace och bläddra bland alternativen. I följande video visas hur du gör detta, inklusive hur du aktiverar kostnadsfria &quot;try before you buy&quot;-strömmar så att du kan låsa in de data som kommer att vara mest användbara för din organisation innan du bestämmer dig för dataleverantörens priser.

För att du ska kunna utforska och avgöra vilken dataleverantör som ska användas är en bra resurs [[!DNL Adobe Audience Finder]](https://www.adobe-audience-finder.com/).

>[!VIDEO](https://video.tv.adobe.com/v/25188/?quality=12)

## Identifiera eller skapa ett idealiskt användar- (konvertering) eller segment {#identify-create-an-ideal-user-conversion-trait-or-segment}

Vad försöker du få folk att göra på din sajt? Vad är din konverteringshändelse? Det finns förstås många olika svar på den här frågan, beroende på webbplatstyp/vertikal och organisationens mål. I vilket fall som helst är det vanligt att AAM skapar en egenskap för besökare som uppfyller dessa kriterier.

I videon nedan visar jag hur du skapar en konverteringskedja som du vill ha på plats när du fortsätter igenom kursen och skapar en look-alike-modell.

När du använder Adobe Analytics-händelser för att skapa egenskaper är det dessutom mycket viktigt att tänka på, så att du inte samlar in fler användare än du borde i trakten. Titta på följande video för den stora avslöjandet. :)

>[!VIDEO](https://video.tv.adobe.com/v/23431/?quality=12)

**OBS!** I videon ovan antar jag att du har Adobe Analytics. Så är inte fallet. Om du har Google Analytics (GA) har vi en modul som du kan använda för att skicka data till AAM (se [dokumentationen](https://experienceleague.adobe.com/docs/audience-manager/user-guide/dil-api/dil-modules.html?lang=sv-SE)). Om din konverteringsaktivitet på din webbplats skickas till AAM av GA kan du skapa din konverteringsegenskap utifrån detta. Om du har en annan analyslösning (eller ingen analyslösning) kan du ändå skicka in data till AAM via vår DIL-kod och funktionen `submit`, osv. (se [dokumentationen](https://experienceleague.adobe.com/docs/audience-manager/user-guide/dil-api/dil-overview.html?lang=sv-SE)). Skapa sedan konverteringsegenskapen baserat på data som skickas när konverteringsaktiviteten utförs på webbplatsen.

## Skapa en look-Alike-modell av data från andra leverantörer eller tredje part {#create-a-look-alike-model-from-2nd-or-3rd-party-data}

När vi har slutfört stegen ovan kan vi nu skapa en algoritmisk modell (Look-alike). När vi skapar modellen kommer vi att använda konverteringsegenskapen som grundegenskap (nyckelbesökare som vi vill duplicera), och vi kommer att använda den aktiverade dataströmmen från tredje part som vår personpool att hämta från.

>[!VIDEO](https://video.tv.adobe.com/v/25190/?quality-12)

## En viktig god praxis {#an-important-best-practice}

När vi skapar den algoritmiska modellen i Audience Manager vill vi naturligtvis att modellen ska vara så effektiv som möjligt. Eftersom modellen beaktar alla egenskaper som medlemmarna i din grundegenskap/segment är en del av, hjälper den inte modellen om ALLA personer är i en egenskap/ett segment. Om du har några supergeneriska egenskaper (som alla som gick till din webbplats, eller alla som har fått någon annons från dig, osv.), måste du därför se till att datakällan som de tillhör INTE inkluderas i datakällorna i din modell. I den här artikelns fall är det osannolikt att du skulle göra det, eftersom vi fokuserar på att titta på data från tredje part för att se våra nya lookalike, men det är värt att nämna i alla fall, och det gäller ALLA dina algoritmiska modeller.

## Skapa en [!UICONTROL Algorithmic Trait] {#creating-an-algorithmic-trait}

Därefter måste vi skapa en [!UICONTROL Algorithmic Trait] så att modellens resultat kan användas. Utan att skapa ett drag är modellen värdelös. När modellen har körts måste du gå till dialogrutan Egenskaper och skapa en [!UICONTROL Algorithmic Trait]. I följande video visas några tips.

>[!VIDEO](https://video.tv.adobe.com/v/25191/?quality=12)

## Skapa ett segment från modelldata och skicka det till DSP {#creating-a-segment-from-the-model-data-and-sending-it-to-dsps}

När du har skapat en [!UICONTROL Algorithmic Trait] kan du skapa ett nytt segment som du kan placera den i, så att du kan aktivera data (du kan inte aktivera en egenskap, utan i stället skapa ett nytt entraitetssegment med [!UICONTROL Algorithmic Trait] i det, så att du kan aktivera (använda) segmentet).

När du har skapat ett segment utifrån denna algoritmiska egenskap har du en publik med potentiella kunder som ser ut som personer som redan har konverterat på din webbplats. Nu kan du mappa det här segmentet till någon av dina DSP destinationer i Audience Manager. Ni kommer att kunna inrikta er på marknadsföring mot de där lookagilla-markeringarna, som är mer benägna att konvertera på er webbplats än bara den vanliga publiken, vilket ökar avkastningen på annonskostnaderna.
