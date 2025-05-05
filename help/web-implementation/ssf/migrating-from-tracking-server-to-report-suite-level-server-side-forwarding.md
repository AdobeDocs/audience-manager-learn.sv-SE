---
title: Migrera från spårningsserver till vidarebefordran på servernivå på rapportnivå
description: Lär dig hur du aktiverar vidarebefordran av Adobe Analytics-data på serversidan till Audience Manager på en rapportsvitnivå i stället för på en spårningsservernivå.
product: audience manager
feature: Adobe Analytics Integration
topics: null
activity: implement
doc-type: technical video
team: Technical Marketing
kt: 1776
role: Developer, Data Engineer
level: Intermediate
exl-id: 08b81e52-a28a-43e4-a284-df2460a43016
source-git-commit: 4adaade180545bcf5f911b7453a7e9939e2ed178
workflow-type: tm+mt
source-wordcount: '582'
ht-degree: 0%

---

# Migrera från spårningsserver till vidarebefordran på servernivå på rapportnivå {#migrating-from-tracking-server-to-report-suite-level-server-side-forwarding}

I den här artikeln och videon visas hur du aktiverar vidarebefordran av [!DNL Analytics]-data på serversidan till Audience Manager på [!UICONTROL report suite]-nivå i stället för på [!UICONTROL tracking server]-nivå.

## Introduktion {#introduction}

Om du har Adobe Audience Manager OCH Adobe Analytics kan du implementera vidarebefordran på serversidan av [!DNL Analytics]-data till Audience Manager. Det innebär att i stället för att din sida skickar två träffar (ett till [!DNL Analytics] och ett till Audience Manager) kan den skicka en träff till [!DNL Analytics], och [!DNL Analytics] vidarebefordrar dessa data till Audience Manager.

Om du redan har detta igång och har det aktiverats/implementerats före oktober 2017 kan vidarebefordran på serversidan vara baserad på din [!UICONTROL Tracking Server], som måste aktiveras av Adobe kundtjänst eller Adobe Consulting. Från och med oktober 2017 kan du nu konfigurera vidarebefordran på serversidan själv och göra det på rapportsvitnivå (vidarebefordran per rapportserie). Det finns viktiga fördelar med detta, som diskuteras nedan.

## Vidarebefordrar [!UICONTROL Tracking server] {#tracking-server-forwarding}

[!UICONTROL tracking server] är den plats dit du skickar dina [!DNL Analytics]-data och den domän där bildbegäran och cookie-filen skrivs. Den ska anges i DTM eller [!DNL Experience Platform Launch], eller i filen [!DNL AppMeasurement.js], och ska vanligtvis se ut så här, med plats- eller företagsnamnet som ersätter &quot;minwebbplats&quot;:

`s.trackingServer = "mysite.sc.omtrdc.net";`

Om vidarebefordran på serversidan är konfigurerad att vidarebefordras på [!UICONTROL tracking server]-nivå vidarebefordras alla träffar som skickas till [!UICONTROL tracking server] (OM Experience Cloud ID-tjänsten också är aktiverad) till Audience Manager. Detta måste aktiveras av Adobe Customer Care eller Adobe Consulting. Det är också de som kan inaktivera det EFTER att du har växlat över till vidarebefordran av [!UICONTROL report suite] enligt beskrivningen nedan.

Om du är osäker på om [!DNL tracking server forwarding] är aktiverat för dig kan du kontakta Adobe kundtjänst eller Adobe Consulting och de bör kunna meddela dig.

## Vidarebefordran på serversidan på [!UICONTROL Report-suite]-nivå {#report-suite-level-server-side-forwarding}

En av de största fördelarna med att gå över till vidarebefordran av [!UICONTROL report suite] från vidarebefordran av [!UICONTROL tracking server] är att du nu kan använda Audience Analytics, vilket är möjligheten att vidarebefordra Audience Manager [!UICONTROL segments] tillbaka till Adobe Analytics för detaljerad segmentanalys. Den här fantastiska funktionen stöds INTE om du fortfarande vidarebefordrar [!UICONTROL tracking server] och inte vidarebefordrar [!UICONTROL report suite]. Mer information om Audience Analytics finns i [dokumentationen](https://experienceleague.adobe.com/docs/analytics/integration/audience-analytics/mc-audiences-aam.html?lang=sv-SE).

>[!VIDEO](https://video.tv.adobe.com/v/23701/?quality=12)

## Viktigt tips {#additional-resources}

Som framgår av videon ovan bör du kontakta Adobe kundtjänst eller Adobe Consulting och inaktivera vidarebefordran av [!UICONTROL tracking server] när du har angett alla [!UICONTROL report suites] som du vill vidarebefordra till Audience Manager. Du behöver inte göra detta eftersom både [!UICONTROL tracking server]-vidarebefordran och [!UICONTROL report suite]-vidarebefordran inte leder till dubbla träffar. Det är dock en bra rutin att endast ha [!UICONTROL report suite] vidarebefordrat.

Om du låter [!UICONTROL tracking server] vidarebefordras kanske det inte bara vidarebefordrar data från [!UICONTROL report suites] som du inte vill vidarebefordra, utan även i framtiden, när du (och alla på ditt företag) har glömt att [!UICONTROL tracking server] vidarebefordran är aktiverat, kan du tro att data inte vidarebefordras för en specifik [!UICONTROL report suite]. Detta beror på att det inte är aktiverat på rapportsvitnivå, men data vidarebefordras ändå på grund av [!UICONTROL tracking server]. Sedan kommer du att slösa tid och pengar på att ta reda på varför det vidarebefordras och även betala för AAM serversamtal som du inte hade väntat dig. Det är därför en bra idé att inaktivera vidarebefordran av [!UICONTROL tracking server] så snart du har alla [!UICONTROL report suites] som är inställda att vidarebefordra som passar ditt företags behov.
