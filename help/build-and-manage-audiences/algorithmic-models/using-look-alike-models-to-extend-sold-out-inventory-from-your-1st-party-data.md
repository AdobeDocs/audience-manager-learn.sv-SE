---
title: Använd lookalike-modeller för att utöka sålt lager från egna data
description: I den här självstudiekursen går vi igenom de steg du bör ta för att konfigurera och använda lookalike-modeller, så att du kan skapa nya lookalike-målgrupper och sälja dem som ett tillägg till ditt konverteringssegment.
feature: Algorithmic Models
topics: null
activity: use
doc-type: feature video
team: Technical Marketing
thumbnail: 23523.jpg
kt: 1688
role: User, Developer, Data Engineer, Architect, Data Architect, Admin, Leader
level: Intermediate
exl-id: 6820528e-3211-4a1d-be05-50f1292179d2
source-git-commit: 2094d3bcf658913171afa848e4228653c71c41de
workflow-type: tm+mt
source-wordcount: '849'
ht-degree: 0%

---

# Använd lookalike-modeller för att utöka sålt lager från egna data {#using-look-alike-models-to-extend-sold-out-inventory-from-your-st-party-data}

I den här självstudiekursen går vi igenom de steg som du bör ta för att konfigurera och använda Look-Alike [!UICONTROL Models], så att du kan skapa nya lookalike-målgrupper och sälja dem som ett tillägg till ditt konverteringssegment.

## Användningsinformation {#use-case-details}

Du är innehållsutgivare. Om du redan har sålt ut lager för konverterare på din webbplats kanske du tror att dina möjligheter upphör där. Ange AAM utseende [!UICONTROL Models]. Genom att använda den här funktionen kan ni utöka det sålda lagret ytterligare och även sälja målgrupper som kanske inte har konverterat än, men som ser ut eller beter sig som personer som har konverterat. Det här målgruppssegmentet säljer normalt för mindre än de faktiska konverterarna, men ger er ändå möjlighet att utöka resultatet genom att erbjuda ett extra målgruppsalternativ för de annonsörer som vill placera ut annonser på er webbplats. Den ytterligare fördelen med detta användningsexempel är att det inte kostar er något att köra den här modellen på era egna data från första part.

Stegen i den här självstudiekursen är som följer:

1. Identifiera/skapa en idealisk användaregenskap (konvertering) eller ett segment
1. Skapa en modell med det här konverteringsegenskapen/segmentet som basartikel
1. Välj [!UICONTROL First party] datakällor i modellen och kör modellen
1. Skapa en [!UICONTROL Algorithmic Trait] från modellresultaten och lägg till egenskapen i ett segment
1. Erbjud segmentet till intresserade annonsörer för att utöka försäljningen av konverteringssegmentet

## Identifiera eller skapa ett idealiskt användar- (konvertering) eller segment {#identify-create-an-ideal-user-conversion-trait-or-segment}

Vad försöker du få folk att göra på din sajt? Vad är din konverteringshändelse? Det finns förstås många olika svar på den här frågan, beroende på webbplatstyp/vertikal och organisationens mål. I vilket fall som helst är det vanligt att AAM skapar en egenskap för besökare som uppfyller dessa kriterier.

I det här fallet antas detta redan eftersom du har sålt lagret för personer som är konverterare. I den här självstudiekursen kan det dock vara bra att diskutera den som referens för resten av användningsfallet.

När du använder händelser för att skapa egenskaper är det en stor grej som du måste tänka på, så att du inte samlar in fler användare än du borde. Titta på följande video för den stora avslöjandet. :)

>[!VIDEO](https://video.tv.adobe.com/v/23431/?quality=12)

**OBS!** I videon ovan antar jag att du har Adobe Analytics. Så är inte fallet. Om du har Google Analytics (GA) har vi en modul som du kan använda för att skicka data till AAM (se [dokumentationen](https://experienceleague.adobe.com/docs/audience-manager/user-guide/dil-api/dil-overview.html)). Om din konverteringsaktivitet på din webbplats skickas till AAM av GA kan du skapa din konverteringsegenskap utifrån detta. Om du har en annan analyslösning (eller ingen analyslösning) kan du ändå skicka in data till AAM via vår DIL-kod och funktionen `submit`, osv. (se [dokumentationen](https://experienceleague.adobe.com/docs/audience-manager/user-guide/dil-api/dil-modules.html)). Sedan skapar du konverteringsegenskapen utifrån de data som skickas när konverteringsaktiviteten utförs på webbplatsen.

## Skapa en look-alike-modell av egna data {#creating-a-look-alike-model-from-first-party-data}

I det här steget ska vi skapa en [!UICONTROL First Party] Look-Alike Model. Det innebär att vi inte bara kommer att använda egna konverteringsegenskaper/segment för vårt bassegment (det skulle vara normalt för de flesta modeller ändå), utan vi kommer också att undersöka poolen av förstapartsdata för fler personer som ser ut som konverterarna. Vi kommer inte att titta på data från andra företag eller tredje part.

I det här fallet är detta viktigt, eftersom vi försöker skapa ett segment med användare på vår webbplats som ser ut som konverterare, men inte har konverterat än, så att vi kan sälja det här lookalike-segmentet till intresserade annonsörer.

>[!VIDEO](https://video.tv.adobe.com/v/23504/?quality-12)

## Skapa en algoritmisk egenskap {#creating-an-algorithmic-trait}

Därefter måste vi skapa en [!UICONTROL Algorithmic Trait] så att modellens resultat kan användas. Utan att skapa ett drag är modellen värdelös. Så när modellen har körts måste du gå till egenskapsdialogrutan och skapa en [!UICONTROL Algorithmic Trait]. I följande video visas några tips.

>[!VIDEO](https://video.tv.adobe.com/v/23523/?quality=12)

## Erbjud [!UICONTROL Algorithmic Segment] till annonsörer {#offering-the-algorithmic-segment-to-advertisers}

När du har skapat en [!UICONTROL Algorithmic Trait] kan du skapa ett nytt segment som du kan placera den i, så att du kan aktivera data (du kan inte aktivera en egenskap, utan i stället skapa ett nytt entraitetssegment med [!UICONTROL Algorithmic Trait] i det, så att du kan aktivera (använda) segmentet.

När du har skapat ett segment med förstahandsbesökare som har nått höga poäng i lookalike-modellen (dvs. som ser ut som konverterare men inte konverterat än) kan du erbjuda det här segmentet till annonsörer på webbplatsen, även efter att du har sålt ut hela ert lager med faktiska konverterare på webbplatsen. Detta är ett bra sätt att utöka den här målgruppen och fortsätta att se ytterligare intäkter genom att använda Look-Alike [!UICONTROL Models] i Audience Manager.
