---
title: Stöd för IAB TCF 2.2
description: Lär dig mer om plugin-programmet för Audience Manager i IAB TCF och hur det fungerar med Adobe opt-in-objektet och din CMP (Consent Management Provider).
feature: Data Governance & Privacy
thumbnail: 26434.jpg
kt: 5027
role: Developer, Data Engineer, Architect
level: Experienced
exl-id: 04b4e786-0457-4dcc-bcf9-a79eda67bb2e
source-git-commit: f9708e705d95b43084ff11e342dc54ff11d6326c
workflow-type: tm+mt
source-wordcount: '1059'
ht-degree: 0%

---

# Stöd för IAB TCF 2.2 i Audience Manager {#iab-tcf-support-in-audience-manager}

Adobe ger dig möjlighet att hantera och förmedla användarnas valmöjligheter i fråga om integritet via Opt-in-funktionen och Audience Manager Plug-in till stödet för IAB Transparency och Consent Framework 2.2 (TCF 2.2). Den här artikeln fungerar tillsammans med dokumentationen som hjälper dig att förstå Audience Manager plugin-programmet till IAB TCF och hur det fungerar tillsammans med Adobe Opt-in-objektet och din CMP (Consent Management Provider). Mer information om IAB finns på deras webbplats på [https://www.iabeurope.eu/](https://www.iabeurope.eu/).

## Första steget: Förstå deltagande i Experience Cloud ID {#first-step-understand-ecid-s-opt-in}

För att förstå hur du ska arbeta med IAB TCF måste du först förstå [!DNL Opt-in]-funktionen, som är en del av ECID-tjänstbiblioteket (Experience Cloud ID Service). Om du inte känner till hur deltagande fungerar kan du läsa [den här användbara artikeln](https://experienceleague.adobe.com/docs/core-services-learn/tutorials/id-service/use-opt-in-to-control-experience-cloud-activities-based-on-user-consent.html?lang=sv-SE) först. Du bör även läsa avanmälningsdokumentationen [till ](https://experienceleague.adobe.com/docs/id-service/using/implementation/opt-in-service/optin-overview.html?lang=sv-SE). När du har gått igenom resurserna går du tillbaka till den här sidan och fortsätter.

## Plugin-programmet Audience Manager för IAB TCF {#the-audience-manager-plug-in-for-iab-tcf}

Nu när du har åtminstone en grundläggande förståelse för hur Opt-in-tjänsten fungerar, kan Audience Manager utnyttja stödet för [!DNL IAB Transparency and Consent Framework (TCF)], vilket görs via ett plugin-program i Opt-in-objektet.

Insticksprogrammet Audience Manager för IAB TCF utökar funktionaliteten hos Opt-in och gör det möjligt för AAM kunder att utvärdera, följa och vidarebefordra användarintegritetsalternativ till samarbetspartners i enlighet med IAB TCF. Den tillhandahåller en standard som datakontrollanter (dvs. du som Adobe-kund) och leverantörer (dMP, DSP, SSP, annonsservrar osv.) kan använda för att förstå samtycke i hela det medgivande landskapet.

## Aktivera IAB TCF {#enabling-iab-tcf}

Det är enkelt att aktivera plugin-programmet Audience Manager för IAB TCF om du använder Adobe Experience Platform Launch, vilket visas i den korta videon nedan:

>[!VIDEO](https://video.tv.adobe.com/v/26433/?quality=12)

Om du inte använder Launch kan du använda `isIabContext=true` för att aktivera det när du initierar Experience Cloud Visitor. Detta initierar IAB TCF-flödet, d.v.s. lägger till ytterligare ett steg till insamlingen av medgivande med hjälp av IAB TCF för att fråga efter IAB TC-strängen och skickar tillbaka det till Opt-in, som i sin tur kommunicerar med Experience Cloud-lösningarna.

## IAB TC-sträng {#iab-tcf-consent-string}

En av de standarder som IAB tillhandahåller är en&quot;medgivandesträng&quot; (kallas även&quot;DaisyBit&quot;), som egentligen består av två listor:

1. Syfte: **Vad** innebär samtycke?
1. Leverantörer: **Vem** godkänner?

### Syfte {#purposes}

Med IAB TCF 2.2 finns det tio&quot;syften&quot; att samla in samtycke för (vad leverantörer kan göra med besökarens data). Adobe Audience Manager kräver inte alla tio, utan bara godkännande för följande ändamål, utöver leverantörens samtycke:

* **Syfte 1:** Lagra och/eller få åtkomst till information på en enhet;
* **Syfte 10:** Utveckla och förbättra produkter;
* **Särskilt syfte 1:** Säkerställ säkerhet, förhindra bedrägeri och felsökning.

Detta är den första delen av IAB TC-strängen och registreras precis som 1:or och 0:or, vilket anger om syftet/aktiviteten är godkänd eller inte.

>[!NOTE]
>
>Enligt IAB:s regler tillåts alltid Special Purpose 1 (Securitys, prevent Fault, and debug) och användarna kan inte motsätta sig det.

### Leverantörer {#vendors}

En annan del av IAB TC-strängen är en lång lista med flera hundra leverantörer, så att besökarna kan visas med en lista över tillämpliga leverantörer som har taggar på webbplatsen och kan välja vilka leverantörer som ska användas. Leverantörer behåller sin plats i listan. Exempel: Adobe Audience Manager leverantörsnummer i den här listan är 565. Om numret i listan har värdet 1 kan Audience Manager utföra de godkända uppgifterna från listans framsida. Om AAM har ett &quot;0&quot; kan det inte göra något med data.

**Om du vill att Audience Manager ska kunna tillhandahålla ett användargränssnitt så att kunder kan använda IAB TCF för att välja dessa syften och leverantörer, eller för att godkänna/avvisa all aktivitet, måste du använda en CMP-partner som är registrerad med IAB TCF eller skapa en som stöder IAB TCF och som är registrerad med IAB TCF.**

## Opt-in: Översättning mellan IAB- och Adobe-program {#opt-in-translating-between-iab-and-adobe-solutions}

En av fördelarna med IAB TCF är att de standardsyften som anges ovan antagligen ger slutanvändaren en bättre uppfattning om vad de godkänner än en lista över Adobe. Slutanvändare kanske inte vet vad det innebär att&quot;godkänna&quot; Audience Manager eller [!DNL Target], men&quot;Lagra och/eller få åtkomst till information på en enhet&quot; eller&quot;Utveckla och förbättra produkter&quot; är antagligen enklare för dem att förstå och godkänna.

För att Audience Manager ska kunna godkännas (dvs. För att översätta IAB för ändamålet att anmäla sig till att ge AAM ett ja) måste slutanvändaren ge sitt medgivande för syftena 1 och 10 enligt ovan. Om något av dessa inte godkänns, eller om en leverantör inte godkänns, kommer AAM inte att köra pixelbränder eller ange cookies. Det är också bra att veta att många kunder helt enkelt väljer att förse slutanvändaren med ett&quot;allt eller ingenting&quot;-gränssnitt, vilket förstås skulle tillåta eller förbjuda användning av Audience Manager (och andra lösningar från Experience Cloud).

Det finns en del bra information i [dokumentationen](https://experienceleague.adobe.com/docs/audience-manager/user-guide/overview/data-privacy/consent-management/aam-iab-plugin.html?lang=sv-SE) om hur Audience Manager-plugin-programmet för IAB TCF-flödet gäller både för Publisher och Advertiser.

## IAB: Skickar samtycke längre fram i kedjan {#iab-sending-consent-downstream}

När plugin-programmet Audience Manager för IAB TCF används skickas även användarens samtycke till ID-synkronisering på plattformsnivå (tredje part) för partners som finns i den globala leverantörslistan, så att partnern får användarens samtycke och kan agera på den också. Den här informationen skickas i två variabler:

* gdpr = 1
* gdpr_medgivande = [kodad medgivandesträng]

Caveat innebär att om användaren befinner sig i IAB-sammanhang och inte ger sitt samtycke (eller ger negativt samtycke), så samlar Audience Manager inte alls IAB TC-strängen, vilket innebär att samtalen tas bort. Så i så fall.. ingen överföring av samtycke längre fram.

## Demo {#demo}

I videon nedan ser du hur cookies och fyrar från ECID och lösningar påverkas av IAB:s val av användare.

>[!VIDEO](https://video.tv.adobe.com/v/26434/?quality=12)

Mer information om plugin-programmet Audience Manager för IAB TCF 2.2, inklusive hur du implementerar och testar, använder exempel och arbetsflöden, finns i [dokumentationen](https://experienceleague.adobe.com/docs/audience-manager/user-guide/overview/data-privacy/consent-management/aam-iab-plugin.html?lang=sv-SE).
