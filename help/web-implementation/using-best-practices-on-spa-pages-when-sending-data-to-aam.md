---
title: Använd bästa praxis SPA sidor när du skickar data till AAM
description: Lär dig de bästa sätten att skicka data från ensidiga program (SPA) till Adobe Audience Manager (AAM). Den här artikeln fokuserar på att använda Experience Platform-taggar, den rekommenderade implementeringsmetoden.
feature: Implementation Basics
topics: spa
activity: implement
doc-type: technical video
team: Technical Marketing
kt: 1390
topic: SPA
role: Developer, Data Engineer
level: Experienced
exl-id: 99ec723a-dd56-4355-a29f-bd6d2356b402
source-git-commit: d4874d9f6d7a36bb81ac183eb8b853d893822ae0
workflow-type: tm+mt
source-wordcount: '547'
ht-degree: 0%

---

# Använd bästa praxis SPA sidor när du skickar data till AAM {#using-best-practices-on-spa-pages-when-sending-data-to-aam}

I det här dokumentet beskrivs flera metoder för att skicka data från ensidiga program (SPA) till Adobe Audience Manager (AAM). Den här artikeln fokuserar på att använda [!UICONTROL Experience Platform tags], den rekommenderade implementeringsmetoden.

## Inledande noteringar

* Objekten nedan förutsätter att du använder plattformstaggar för implementering på din webbplats. Det finns fortfarande anledning att tänka på om du inte använder plattformstaggar, men du måste anpassa dem till din implementeringsmetod.
* Alla SPA är olika, så du kan behöva justera några av följande objekt för att bäst uppfylla dina krav, men Adobe vill dela med dig av några bästa metoder som du behöver tänka på när du skickar data från SPA sidor till Audience Manager.

## Enkelt diagram över att arbeta med SPA och AAM i Experience Platform-taggar (tidigare Launch){#simple-diagram-of-working-with-spas-and-aam-in-experience-platform-launch}

![spa för namn i taggar](assets/spa_for_aam_in_launch.png)

>[!NOTE]
>Detta är som sagt ett förenklat diagram över hur SPA sidor hanteras i en Adobe Audience Manager-implementering (utan Adobe Analytics) med hjälp av plattformstaggar. Som du ser är det ganska rakt framåt. Det stora beslutet är hur du ska förmedla en vyändring (eller en åtgärd) till plattformstaggar.

## Utlösa taggar från SPA {#triggering-launch-from-the-spa-page}

Två av de vanligaste sätten att aktivera en regel i plattformstaggar (och därmed skicka data till Audience Manager) är:

* Ställa in anpassade JavaScript-händelser (se exempel [HERE](https://helpx.adobe.com/analytics/kt/using/spa-analytics-best-practices-feature-video-use.html) med Adobe Analytics)
* Använda en [!UICONTROL Direct Call Rule]

I det här Audience Manager-exemplet använder du en [!UICONTROL Direct Call rule] i Platform-taggar för att utlösa träffen i Audience Manager. Som du kommer att se i nästa avsnitt blir detta användbart genom att ställa in [!UICONTROL Data Layer] på ett nytt värde, så att det kan hämtas av [!UICONTROL Data Element] i plattformstaggar.

## Demo {#demo-page}

Här är en liten sida som visar hur du ändrar ett värde i datalagret och skickar det till Audience Manager, precis som på en SPA sida. Den här funktionen kan utformas för mer komplicerade ändringar. Du hittar den här demosidan [HERE](https://aam.enablementadobe.com/SPA-Launch.html).

## Ange datalagret {#setting-the-data-layer}

Som vi tidigare nämnt måste datalagret ställas in dynamiskt i sidhuvudet på sidan BEFORE Platform-taggar anropas och [!UICONTROL rules] körs när nytt innehåll läses in på sidan eller när någon utför en åtgärd på webbplatsen, så att plattformstaggar kan hämta de nya värdena från datalagret och överföra dem till Audience Manager.

Om du går till demowebbplatsen som listas ovan och tittar på sidkällan ser du:

* Datalagret ligger i sidans huvud, före anropet till plattformstaggar
* JavaScript i den simulerade SPA-länken ändrar [!UICONTROL Data Layer] och anropar sedan plattformstaggar (anropet `_satellite.track()`). Om du använde anpassade händelser från JavaScript i stället för [!UICONTROL Direct Call Rule] är lektionen densamma. Ändra först [!DNL data layer] och anropa sedan plattformstaggar.

>[!VIDEO](https://video.tv.adobe.com/v/23322/?quality=12)

## Ytterligare resurser {#additional-resources}

* [SPA diskussion på Adobe-forumen](https://forums.adobe.com/thread/2451022)
* [Referensarkitekturwebbplatser som visar hur du implementerar SPA i plattformstaggar](https://helpx.adobe.com/experience-manager/kt/integration/using/launch-reference-architecture-SPA-tutorial-implement.html)
* [Använda vedertagna metoder för att spåra SPA i Adobe Analytics](https://helpx.adobe.com/analytics/kt/using/spa-analytics-best-practices-feature-video-use.html)
* [Demonstrationswebbplats som används för den här artikeln](https://aam.enablementadobe.com/SPA-Launch.html)
