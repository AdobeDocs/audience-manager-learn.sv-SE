---
title: Migrera implementeringen av webbplatsen i Audience Manager från DIL på klientsidan till vidarebefordran på serversidan
description: Lär dig hur du migrerar implementeringen av Audience Manager (AAM) för din webbplats från vidarebefordran på klientsidan DIL till serversidan. Den här självstudiekursen gäller om du har både AAM och Adobe Analytics, och om du skickar träffar från sidan till AAM med DIL-kod (Data Integration Library) och du även skickar träffar från sidan till Adobe Analytics.
product: audience manager
feature: Adobe Analytics Integration
topics: null
activity: implement
doc-type: tutorial
team: Technical Marketing
kt: 1778
role: Developer, Data Engineer
level: Intermediate
exl-id: bcb968fb-4290-4f10-b1bb-e9f41f182115
source-git-commit: 2094d3bcf658913171afa848e4228653c71c41de
workflow-type: tm+mt
source-wordcount: '2333'
ht-degree: 0%

---

# Migrera implementeringen av webbplatsen i Audience Manager från DIL på klientsidan till vidarebefordran på serversidan {#migrating-your-site-s-aam-implementation-from-client-side-dil-to-server-side-forwarding}

Den här självstudiekursen gäller dig om du har både Adobe Audience Manager (AAM) och Adobe Analytics, och du för närvarande skickar en träff från sidan till AAM med DIL ([!DNL Data Integration Library])-kod och även skickar en träff från sidan till Adobe Analytics. Eftersom du har båda dessa lösningar, och eftersom de båda är en del av Adobe Experience Cloud, har du möjlighet att följa den bästa metoden att aktivera vidarebefordran på serversidan, vilket gör att datainsamlingsservrarna i [!DNL Analytics] kan vidarebefordra webbplatsanalysdata i realtid till Audience Manager, i stället för att låta klientkoden skicka en extra träff från sidan till AAM. I den här självstudiekursen får du hjälp med att gå över från den tidigare DIL-implementeringen på klientsidan till den nyare vidarebefordringsmetoden på serversidan.

## Klientsidan (DIL) jämfört med serversidan {#client-side-dil-vs-server-side}

När du jämför och kontrasterar dessa två metoder för att hämta in Adobe Analytics-data i AAM kan det först vara praktiskt att visualisera skillnaderna i följande bild:

![klientsida till serversida](assets/client-side_vs_server-side_aam_implementation.png)

### Implementering av DIL på klientsidan {#client-side-dil-implementation}

Om du använder den här metoden för att hämta Adobe Analytics-data till AAM kommer du att få två träffar från dina webbsidor: en som går till [!DNL Analytics] och en som går till AAM (efter att du har kopierat [!DNL Analytics]-data på webbsidan. [!UICONTROL Segments] returneras från AAM till sidan, där de kan användas för personalisering och så vidare. Detta betraktas som en äldre implementering och rekommenderas inte längre.

Förutom att detta inte följer bästa praxis, är nackdelarna med att använda denna metod bland annat:

* Två träffar på sidan istället för bara ett
* Vidarebefordran på serversidan krävs för delning i realtid av AAM målgrupper till [!DNL Analytics], så implementeringar på klientsidan tillåter inte den här funktionen (och eventuellt andra funktioner i framtiden)

Vi rekommenderar att du går över till en vidarebefordringsmetod på serversidan för AAM implementering.

### Vidarebefordran på serversidan {#server-side-forwarding-implementation}

Som framgår av bilden ovan kommer en träff från webbsidan till Adobe Analytics. [!DNL Analytics] vidarebefordrar sedan dessa data till AAM i realtid, och besökarna utvärderas till AAM och [!UICONTROL segments], precis som om träffen hade kommit direkt från sidan.

[!UICONTROL Segments] returneras vid samma realtidsträff tillbaka till [!DNL Analytics] som vidarebefordrar svaret till webbsidan för personalisering och så vidare.

Det finns ingen nedtid till att gå över till vidarebefordran på serversidan. Adobe rekommenderar att alla som har både Audience Manager och [!DNL Analytics] använder den här implementeringsmetoden.

## Du har två huvuduppgifter {#you-have-two-main-tasks}

Det finns en hel del information på den här sidan, och allt är förstås viktigt. Det **alla kodar dock två huvudsaker som du måste göra**:

1. Ändra koden från DIL-kod på klientsidan till kod för vidarebefordran på serversidan
1. Vänd växeln i [!DNL Analytics] [!DNL Admin Console] för att starta den faktiska dataöverföringen (per [!UICONTROL report suite])

Om du hoppar över någon av dessa åtgärder kommer vidarebefordran på serversidan inte att fungera korrekt. Steg och ytterligare data har lagts till i det här dokumentet för att hjälpa dig att göra de här två stegen på rätt sätt under installationen.

## Implementeringsalternativ {#implementation-options}

När du går över från klientsidan till serversidan är en av de uppgifter du kommer att ha att ändra koden till den nya koden för vidarebefordring på serversidan. Detta görs med något av följande alternativ:

* Adobe Experience Platform-taggar - Adobe rekommenderade implementeringsalternativ för webbegenskaper. Du kommer att se att det här är en enkel uppgift eftersom plattformstaggar har gjort allt det hårda arbete som du har gjort.
* På sidan - Du kan också placera den nya SSF-koden direkt i funktionen `doPlugins` i `appMeasurement.js`-filen, om du inte (än) använder Adobe Launch
* Andra tagghanterare - Dessa kan behandlas på samma sätt som föregående (på sidan) alternativ, eftersom du fortfarande placerar SWF-koden i `doPlugins`, där den andra tagghanteraren lagrar [!DNL AppMeasurement]-koden

Vi tittar på vart och ett av dessa nedan i avsnittet _Uppdatera koden_.

## Implementeringssteg {#implementation-steps}

I följande steg beskrivs implementeringen.

### Steg 0: Krav: Experience Cloud ID-tjänsten (ECID) {#step-prerequisite-experience-cloud-id-service-ecid}

Den viktigaste förutsättningen för att gå över till vidarebefordran på serversidan är att Experience Cloud ID-tjänsten är implementerad. Detta är enklast om du använder Experience Platform Launch, och då installerar du bara ECID-tillägget så gör det resten.

Om du använder ett TMS som inte är Adobe eller inget TMS alls implementerar du ECID för att köra **före** några andra Adobe-lösningar. Mer information finns i [ECID-dokumentationen](https://experienceleague.adobe.com/docs/id-service/using/home.html). Den enda andra förutsättningen är kodversioner, så när du bara använder de senaste versionerna av koden i följande steg kommer du att klara dig.

>[!NOTE]
>
>Läs hela dokumentet innan du implementerar det. Avsnittet&quot;Timing&quot; nedan innehåller viktig information om *när* du ska implementera varje del, inklusive ECID (om det ännu inte har implementerats).

### Steg 1: Spela in de alternativ som används för närvarande från DIL-koden {#step-record-currently-used-options-from-dil-code}

När du är redo att gå från DIL-kod på klientsidan till vidarebefordran på serversidan är det första steget att identifiera allt du gör med DIL-kod, inklusive anpassade inställningar och data som skickas till AAM. Några saker att tänka på:

* Normala [!DNL Analytics]-variabler med modulen `siteCatalyst.init` DIL - Du behöver inte bekymra dig om den här, eftersom dess jobb är att skicka de normala [!DNL Analytics]-variablerna över, och det sker genom att vidarebefordran på serversidan är aktiverat.
* Partnerunderdomän - I funktionen `DIL.create` kan du göra en anteckning om parametern `partner`. Detta kallas din&quot;partnerunderdomän&quot; eller ibland&quot;partner-ID&quot; och kommer att behövas när du placerar den nya koden för vidarebefordran på serversidan.
* [!DNL Visitor Service Namespace] - Kallas även [!DNL Org ID] eller [!DNL IMS Org ID]. Du behöver detta när du konfigurerar den nya koden för vidarebefordran på serversidan. Notera det.
* containerNSID, uidCookie och andra avancerade alternativ - Anteckna eventuella ytterligare avancerade alternativ som du använder så att du kan ange dem även i koden för vidarebefordran på serversidan.
* Ytterligare sidvariabler - Om andra variabler skickas till AAM från sidan (utöver de normala [!DNL Analytics]-variablerna som hanteras av siteCatalyst.init) måste du anteckna dem så att de kan skickas via vidarebefordring på serversidan (mellanlagringsvarning: via [!DNL contextData] -variabler).

### Steg 2: Uppdatera koden {#step-updating-the-code}

I [Implementeringsalternativ](#implementation-options) (ovan) anges flera alternativ för hur och var du implementerar vidarebefordran på serversidan. För att detta avsnitt ska bli effektivt måste vi dela upp det i dessa avsnitt (med två av dem kombinerade). Gå till den metod i det här avsnittet som bäst beskriver dina behov.

#### Adobe Experience Platform-taggar {#launch-by-adobe}

Titta på videon nedan om du vill veta mer om hur du flyttar implementeringsalternativ från DIL-kod på klientsidan till vidarebefordran på serversidan i Experience Platform Launch.

>[!VIDEO](https://video.tv.adobe.com/v/26310/?quality=12)

#### &quot;On the page&quot; eller non-Adobe tag manager {#on-the-page-or-non-adobe-tag-manager}

Titta på videon nedan om du vill veta mer om hur du flyttar implementeringsalternativ från DIL-kod på klientsidan till vidarebefordran på serversidan i [!DNL AppMeasurement]-kod, som finns antingen i en fil eller i ett tagghanteringssystem som inte är Adobe.

>[!VIDEO](https://video.tv.adobe.com/v/26312/?quality=12)

### Steg 3: Aktivera vidarebefordran (per [!UICONTROL Report Suite]) {#step-enabling-the-forwarding-per-report-suite}

Än så länge har vi ägnat all vår tid åt att växla från DIL-kod på klientsidan till vidarebefordran på serversidan. Det är bra, eftersom det är den svåraste delen. Det här avsnittet är lika viktigt som att uppdatera koden, även om det är superenkelt. I den här videon får du se hur du kan vända på den övergång som gör det möjligt att överföra data från Analytics till Audience Manager.

>[!VIDEO](https://video.tv.adobe.com/v/26355/?quality-12)

**Obs!** Som du angett i videon tar det upp till 4 timmar innan vidarebefordran kan implementeras till fullo på Experience Cloud.

## Timing {#timing}

Som en påminnelse finns det två huvuduppgifter för att gå över från DIL på klientsidan till vidarebefordran på serversidan:

1. Uppdatera koden
1. Vänder växeln i [!DNL Analytics] [!DNL Admin Console]

Men frågan är vem gör du först? Spelar det någon roll? Det var två frågor. Men svaren är.. det beror på, och ja, det *kan* spela någon roll. Hur är det för vagt? Låt oss bryta ned den. Men först ytterligare en fråga som kan ställas om du är en stor organisation med många webbplatser: Måste jag göra allt samtidigt? Den där är lite lättare. Nepp. Du kan göra det bit för bit.

### Lite djupdykning {#a-little-deeper-dive}

Orsaken till att timing och ordning spelar roll är hur vidarebefordran av _faktiskt_ fungerar, vilket kan sammanfattas i följande tekniska fakta:

* Om du har Experience Cloud ID-tjänsten (ECID) implementerad och växeln i [!DNL Analytics] [!DNL Admin Console] (&quot;växeln&quot;) är aktiverad kommer data att vidarebefordras från [!DNL Analytics] till AAM, även om du inte har uppdaterat koden än.
* Om du inte har ECID implementerat kommer data inte att vidarebefordras, även om du har växeln på och har vidarebefordringskoden på serversidan.
* Vidarebefordringskoden på serversidan (i plattformstaggar eller på sidan) hanterar svaret och är nödvändig för att slutföra migreringen.
* Kom ihåg att vidarebefordringsväxeln på serversidan är aktiverad av [!UICONTROL report suite], men att koden hanteras av egenskapen i plattformstaggar, eller av filen [!DNL AppMeasurement] om du inte använder plattformstaggar.

### God praxis {#best-practices}

Baserat på dessa tekniska detaljer finns rekommendationer för när och när detta ska ske:

#### Om du INTE har ECID ännu {#if-you-do-not-have-ecid-yet-implemented}

1. Vänd växeln i [!DNL Analytics] för varje [!UICONTROL report suite] som ska aktiveras för vidarebefordran på serversidan.

   1. Vidarebefordran startar inte än eftersom du inte har ECID.

1. Uppdatera koden per webbplats från vidarebefordran på klientsidan DIL till serversidan (detta kan vara i plattformstaggar) eller på sidan, vilket beskrivs i ett annat avsnitt ovan).

   1. Vidarebefordra nu flöden (när du har lagt till ECID) och du bör även få ett korrekt JSON-svar på din [!DNL Analytics]-beacon (mer information finns i avsnittet Validering och felsökning nedan).

#### Om du har ECID implementerat {#if-you-do-have-ecid-implemented}

1. Förbered och planera så att du är redo att uppdatera koden från DIL till vidarebefordran på serversidan PER [!UICONTROL report suite] som du aktiverar för vidarebefordran på serversidan:

   1. Vänd växeln i [!DNL Analytics] om du vill aktivera vidarebefordran på serversidan.

      1. Vidarebefordran kommer att starta eftersom du har aktiverat ECID.

   1. Uppdatera koden så snart som möjligt från vidarebefordran på klientsidan DIL till ensidig vidarebefordran (detta kan vara i plattformstaggar eller på sidan, vilket beskrivs i ett annat avsnitt ovan).

      1. Du bör få ett korrekt JSON-svar på din [!DNL Analytics]-beacon (mer information finns i avsnittet [Validering och felsökning](#validation-and-troubleshooting) nedan).

>[!NOTE]
>
>Det är viktigt att du utför dessa två steg så nära varandra som möjligt, eftersom du mellan steg 1 och 2 ovan kommer att få en dubblering av data som AAM. Med andra ord kommer vidarebefordran på en sida att ha börjat skicka data från [!DNL Analytics] till AAM, och eftersom DIL-koden fortfarande finns på sidan kommer det också att finnas en träff som går direkt från sidan till AAM, vilket fördubblar informationen. Så snart du uppdaterar koden från DIL till vidarebefordran på serversidan kommer detta att undvikas.

>[!NOTE]
>
>Om du hellre vill ha en liten avvikelse i data än en liten dubblett av data kan du ändra ordningen i steg 1 och 2 ovan. Om du flyttar koden från DIL till vidarebefordran på serversidan avbryts dataflödet till AAM tills du kan vända växeln för att aktivera vidarebefordran på serversidan för [!UICONTROL report suite]. Vanligtvis har kunderna hellre en liten dubblering av data än att missa att få besökare i sina egenskaper och [!UICONTROL segments].

#### Migreringstidsinställning när du har många platser och [!UICONTROL report suites] {#migration-timing-when-you-have-many-sites-and-report-suites}

Detta ämne behandlas kortfattat i tidigare avsnitt, i den meningen att huvudstrategin kan sammanfattas av följande:

Migrera en plats/[!UICONTROL report suite] (eller grupp av platser/[!UICONTROL report suites]) åt gången.

Detta kan dock bli lite knepigt baserat på några möjliga scenarier:

* Du har en plats som innehåller flera distinkta [!UICONTROL report suites]
* Du har en [!UICONTROL report suite] som innehåller flera webbplatser (som en global [!UICONTROL report suite])
* Du använder en plattformstaggegenskap för att täcka flera webbplatser
* Du har olika utvecklingsteam för olika webbplatser

På grund av dessa objekt kan det bli lite komplicerat. Det bästa jag kan föreslå är:

* Ta dig tid att ta fram en strategi för migrering till vidarebefordran på serversidan, baserat på vad som har förklarats ovan
* Baserat på det faktum att en enda egenskap i plattformstaggar (eller en enskild [!DNL AppMeasurement]-fil) vanligtvis mappas till 1 eller 2 distinkta [!UICONTROL report suites], kommer du troligen att kunna skapa en plan som fungerar på dessa distinkta grupper en i taget och som uppdaterar ditt företag till serversidans vidarebefordran
* Om du arbetar med Adobe Consulting kan du tala med dem om din migreringsplan, så att de kan hjälpa dig efter behov

## Validering och felsökning {#validation-and-troubleshooting}

Det viktigaste sättet att verifiera att vidarebefordran på serversidan är igång är att titta på svaret på eventuella Adobe Analytics-träffar som kommer från appen.

Om du inte vidarebefordrar data från [!DNL Analytics] till Audience Manager på serversidan finns det inget svar på [!DNL Analytics]-beacon (förutom en 2x2-pixel). Om du utför vidarebefordran på serversidan finns det dock objekt som du kan verifiera i begäran och svaret [!DNL Analytics] som talar om för dig att [!DNL Analytics] kommunicerar korrekt med Audience Manager, vidarebefordrar träffen och får ett svar.

>[!VIDEO](https://video.tv.adobe.com/v/26359/?quality=12)

>[!WARNING]
>
>Se upp för det falska &quot;Success&quot;. Om det finns ett svar, och allt verkar fungera, kontrollerar du att du har `stuff`-objektet i svaret. Om du inte gör det kan du se ett meddelande som säger `"status":"SUCCESS"`. Så galet som det låter är det faktiskt ett bevis på att det INTE fungerar som det ska.
>
>Om detta visas betyder det att du har slutfört koduppdateringen i plattformstaggar eller [!DNL AppMeasurement], men att vidarebefordran i [!DNL Analytics] [!DNL Admin Console] inte har slutförts än. I det här fallet måste du verifiera att du har aktiverat vidarebefordran på serversidan i [!DNL Analytics] [!DNL Admin Console] för din [!UICONTROL report suite]. Om du har gjort det, och det inte har gått fyra timmar än, var tålmodig, eftersom det kan ta så lång tid att göra alla nödvändiga ändringar på backend-sidan.


![false](assets/falsesuccess.png)

Mer information om vidarebefordran på serversidan finns i [dokumentationen](https://experienceleague.adobe.com/docs/analytics/admin/admin-tools/server-side-forwarding/ssf.html).
