---
title: Uppdaterar till Adobe Audience Manager DIL version 8.0 (eller senare)
description: I den här artikeln beskrivs steg och rekommendationer för hur du uppdaterar Adobe Audience Manager (AAM) Data Integration Library (DIL)-kod till version 8.0 eller senare. Det handlar om implementering på klientsidan, inte vidarebefordran av Adobe Analytics-data på serversidan, och omfattar DTM, Launch by Adobe och implementeringar utan tagghanterarlösning för Adobe.
feature: DIL Implementation
topics: null
activity: implement
doc-type: technical video
team: Technical Marketing
kt: 1841
role: Developer, Data Engineer
level: Intermediate
exl-id: 8c1e6ed5-0f21-427b-a681-0ecb020a0e60
source-git-commit: 62b43b5627dabf754cf821f974a56c60989ef7ef
workflow-type: tm+mt
source-wordcount: '1074'
ht-degree: 0%

---

# Uppdatera till Adobe Audience Manager DIL version 8.0 (eller senare) {#updating-to-adobe-audience-manager-s-dil-version-or-greater}

Den här artikeln innehåller steg och rekommendationer för hur du uppdaterar Adobe Audience Manager (AAM) [!DNL Data Integration Library] (DIL)-kod till version 8.0 eller senare. Det handlar om implementering på klientsidan, inte vidarebefordran av Adobe Analytics-data på serversidan, och omfattar DTM, Launch by Adobe och implementeringar utan tagghanterarlösning för Adobe.

## Översikt {#overview}

Med Audience Manager [!DNL Data Integration Library] (DIL)-kod kan du implementera AAM på din webbplats*. Vid implementering av tidigare versioner av DIL krävdes inte att Adobe&#39;s Experience Cloud ID Service (ECID) implementerades (även om det var en mycket bra idé). Från och med DIL version 8.0 finns ett starkt beroende av ECID version 3.3 eller senare. Om du implementerar DIL 8.0 eller senare utan ECID 3.3, eller med en tidigare version, får du ett felmeddelande som inte fungerar. Eftersom du kan implementera AAM på flera olika sätt har vi skapat den här sidan för att ge dig några steg att gå igenom samt några rekommendationer. Här nedan hittar du de här stegen och rekommendationerna uppdelade efter plattform/implementeringsmetod. Mer information om DIL finns i [dokumentationen](https://experienceleague.adobe.com/docs/audience-manager/user-guide/dil-api/dil-overview.html?lang=en).

* Som framgår av beskrivningen av den här sidan omfattar detta endast implementeringar på klientsidan av DIL som används av AAM som inte har Adobe Analytics. Om du har Adobe Analytics bör du använda metoden för vidarebefordran på serversidan för att implementera AAM. Den här metoden beskrivs i [dokumentationen](https://experienceleague.adobe.com/docs/analytics/admin/admin-tools/server-side-forwarding/ssf.html).

## Duplicera och ta bort element och metoder {#duplicate-and-deprecated-elements-and-methods}

I tidigare versioner av DIL och ECID fanns det dubbleringsmetoder (metoder som gör samma funktion i både DIL och ECID), som orsakade förvirring om vilken som användes. Vanligtvis behövde ni använda båda och matcha dem, och det budskapet förmedlades inte väl till våra kunder. Från och med DIL 8.0 har dessa dubblettmetoder och -element tagits bort i DIL och du bör använda ECID-versionen.

Exempel:

* När du använder [!DNL DIL.create] har några element tagits bort och du bör använda ECID-elementen i stället. Dessa element anropas i [[!DNL DIL.create] dokumentationen](https://experienceleague.adobe.com/docs/audience-manager/user-guide/dil-api/class-level-dil-methods/dil-create.html).
* Metoden [!DNL idSync] på förekomstnivå har också tagits bort, vilket anropas i metodens [dokumentation](https://experienceleague.adobe.com/docs/audience-manager/user-guide/dil-api/dil-instance-methods.html).

## ID-synkronisering med ett kund-ID {#id-syncing-with-a-customer-id}

I AAM kan du synkronisera ditt UUID (anonymt unikt användar-ID) på datorn med ett kund-ID, så att du kan överföra offlinedata om den kunden och koppla dem till deras onlinebeteende för att få en bättre förståelse för dina kunder. Tidigare har detta gjorts på ett av två sätt:

* Metoden [!DNL idSync] på förekomstnivå
* Elementet [!DNL declaredId] i [!DNL DIL.create]

Om du har använt någon av dessa äldre metoder för att synkronisera med ett kund-ID rekommenderar vi att du uppdaterar till med metoden [!DNL setCustomerIDs], som ingår i ECID-tjänsten. Mer information om [!DNL setCustomerIDs] finns i metodens [dokumentation](https://experienceleague.adobe.com/docs/id-service/using/id-service-api/methods/setcustomerids.html).

**Snabbtips:** När du tidigare använde någon av metoderna ovan refererade du till AAM [!UICONTROL Data Source] med [!UICONTROL Data Source] ID (AKA, &quot;DPID&quot;). När du uppdaterar till [!DNL setCustomerIDs] måste du använda AAM [!UICONTROL Integration Code] i stället. [!UICONTROL Data Source] Den pekar fortfarande på samma [!UICONTROL Data Source], men är bara en annan identifierare. Detta visas i videon nedan.

>[!VIDEO](https://video.tv.adobe.com/v/23873/?quality=12)

I följande avsnitt listas steg och rekommendationer för uppdatering till DIL 8.0 baserat på din implementeringsmetod:

## Uppdatera till DIL 8.0 i Adobe Experience Platform-taggar {#updating-to-dil-in-experience-platform-launch}

Grundläggande steg för uppdatering till DIL 8.0

1. Om du använder före 8.0 DIL innan du uppgraderar går du till DIL-konfigurationen i AAM och noterar eventuella avancerade alternativ som du använder (som ska användas i ett senare steg).
1. Uppdatera AAM till version 8.0 eller senare.
1. Kontrollera att Experience Cloud ID-tjänsttillägget är version 3.3.0 eller senare.
1. Aktivera ECID-metoderna i ECID-tillägget för alla borttagna metoder och element (som `disableIDSyncs`) som fanns i AAM för tidigare version än 8.0 eller i anpassad kod för DIL.

   1. (DIL) `disableDestinationPublishingIframe` -> (ECID) `disableIdSyncs`
   1. (DIL) `disableIDSyncs` -> (ECID) `disableIdSyncs`
   1. (DIL) `iframeAkamaiHTTPS` -> (ECID) `dSyncSSLUseAkamai`
   1. (DIL) `declaredId` -> (ECID) `setCustomerIDs`

1. Publish ändringarna.

>[!VIDEO](https://video.tv.adobe.com/v/23874/?quality=12)

## Uppdatera till DIL 8.0 i Adobe DTM {#updating-to-dil-in-adobe-dtm}

1. Uppdatera AAM till version 8.0 eller senare. Den här versionsinställningen finns under avsnittet Allmänt i AAM.
1. Observera (så att du kan lägga till dem i ECID-verktyget) och sedan ta bort dem från den anpassade [!DNL DIL code] i AAM-verktyget för alla borttagna metoder och element (som `disableIDSyncs`) som fanns i den anpassade koden för verktyget för AAM för DIL.
1. Uppdatera Experience Cloud ID-tjänsttillägget till version 3.3.0 eller senare
1. Lägg till de avancerade alternativen i ECID-verktyget som du har tagit bort från AAM kod.
1. Publish the changes

## Uppdatera till DIL 8.0 utan Adobe Tag Management-lösning {#additional-resources}

Om du uppdaterar koden direkt på sidan kan du ersätta äldre objekt med nyare objekt, förutom när du behöver flytta metoder/element från DIL till ECID, vilket beskrivs ovan. I så fall ersätter du helt enkelt den gamla metoden/elementet på DIL-platsen med ECID-metoden/-elementet på ECID-platsen.

Detsamma gäller tagghanterare som inte är Adobe. Oavsett var du har de gamla versionerna i tagghanteringslösningen ersätter du den med den nya koden enligt beskrivningen i följande steg.

1. Uppdatera ditt DIL-bibliotek till den senaste versionen (8.0 eller senare) - Du måste få den senaste DIL-koden från Adobe Consulting eller Adobe kundtjänst eftersom den för närvarande inte är tillgänglig på en offentlig plats. Ersätt sedan den gamla DIL-bibliotekskoden med den nya DIL-bibliotekskoden och gå vidare till nästa steg (stanna inte nu, annars får du problem).
1. Installera [!DNL ECID Service] eller uppdatera din befintliga version till 3.3.0 eller senare. Du kan hämta den senaste versionen av Experience Cloud ID-tjänsten [ från vår GitHub-sida](https://github.com/Adobe-Marketing-Cloud/id-service/releases). Om du behöver hjälp med detta kan du läsa [dokumentationen](https://experienceleague.adobe.com/docs/id-service/using/home.html) eller prata med en Adobe-konsult.

1. Kontrollera att alla föråldrade metoder eller element i din anpassade kod för DIL flyttas till ECID-metoderna:

   1. (DIL) `disableDestinationPublishingIframe` -> (ECID) `disableIdSyncs`

      [Dokumentation](https://experienceleague.adobe.com/docs/id-service/using/id-service-api/configurations/disableidsync.html)

   1. (DIL) `disableIDSyncs` -> (ECID) `disableIdSyncs`

      [Dokumentation](https://experienceleague.adobe.com/docs/id-service/using/id-service-api/configurations/disableidsync.html)

   1. (DIL) `iframeAkamaiHTTPS` -> (ECID) `idSyncSSLUseAkamai`

      [Dokumentation](https://experienceleague.adobe.com/docs/audience-manager/user-guide/dil-api/class-level-dil-methods/dil-create.html)

   1. (DIL) `declaredId` -> (ECID) `setCustomerIDs`

      [Dokumentation](https://experienceleague.adobe.com/docs/id-service/using/id-service-api/methods/setcustomerids.html)
