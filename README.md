# IDV449 Laboration 2 - Mezzy Labbage
###Sandra Hansson - sh222td

##Säkerhetsproblem
<strong>1. Bruten autentisiering och sessions hantering, Kvarstående kakor</strong>

<strong>Problem</strong>
När användaren loggar ut så förstörs inte kakan, detta leder till är när användaren loggar ut så redirectas man tillbaka till inloggnins sidan men om man lägger till /message i urlen så kommer man in igen som samma användare. Man kan däremot endast se innehållet, inte lägga till nya meddelanden.
Kakan förstörs endast när man stänger ner webbläsaren vilket visar att det antagligen används sessions kakor för inloggshantering.

<strong>Konsekvenser</strong> 
Kvarstående kakor är en risk då det medför att användaren kommer finnas kvar i systemet fram tills användaren stänger ner webbläsaren. Det kan i sin tur leda till att andra användare kan komma åt personens sida.

<strong>Åtgärd </strong>
Genom att skapa och kalla på en funktion som förstör kakan/sessionen när personen loggar ut så är problemet åtgärdat.


<strong>2: ABruten autentisiering och sessions hantering, Manipulering av kakor</strong>

<strong>Problem</strong>
Manipulering av en existerande kaka är tillåten mellan olika webbläsare. Loggar jag till exempel in på sidan  på google chrome och kopierar kakans värde via EditThisCookie-plugin, sen går in på samma sida fast på firefox och klistrar in kakans värde via Firebug så kan jag vara inloggad på två olika stället samtidigt. 

<strong>Konsekvenser</strong>
Detta kan leda till att en hackare kan få tag på en annan användares kaka och utnyttja den. Bryter mot A2: Bruten autentisering och sessions hantering.

<strong>Åtgärd</strong> 
Genom att använda sig utav $_SERVER[‘HTTP_USER_AGENT’] som returnerar  så kan man kontrollera att användaren endast är inloggad på sin egna agent.


<strong>3: A3.XSS, otillåtna taggar</strong>

<strong>Problem</strong>
I text fältet där man ska skriva in meddelanden så kan man lägga in html taggar som a-taggar, img-taggar, style-taggar och formulär-taggar. 

<strong>Konsekvenser</strong>
Detta är resultatet av att den inskickade datan inte valideras korrekt vilket kan leda till att en användare med onda tankar lurar okunniga användare till att gå in på osäkra och fientliga sidor.

<strong>Åtgärd</strong> 
Man kan åtgärda ett sånt problem på lite olika sätt. Antingen trimmar man texten och kör på ett regex uttryck för <,>, / och kör en replace när sådant uppkommer i texten. Man kan även köra på HTML entity kodning, viktigt och veta här dock är att det endast gäller för html taggar och inte script taggar. För större applikationer så kan man använda sig utav automatiska sanerings bibliotek som OWASPs AntiSamy, ett bibliotek för html och css kod.  (Ref. [3])


<strong>4: A1.SQL injection, inlogg av ej existerande medlem</strong>

<strong>Problem</strong>
Om man skriver in en valfri mail och 1'or'1=1 som lösenord så kan man logga in trots att mailen inte är registrerad, när man väl är inloggad så kan man se alla inlägg samt posta nya fast under ett namn som redan är registrerat i systemet.

<strong>Konsekvenser</strong>
Vilken person som helst kan få tillgång till innehållet på sidan utan att vara registrerad och validerad. Denne kan även utge sig vara någon annan då det postas under en redan registrerad användare.

<strong>Åtgärd</strong> 
Genom att hålla osäker data separerad från kommandon och queries så kan man förebygga mot attacker. Exempel på sådana lösningar att använda ett säkert API som undviker användning av tolken helt och hållet. Man måste dock fortfarande vara kritiskt mot API:er som med lagrade procedurer då det fortfarande kan införa dolda injections (Ref. [2]). 


<strong>5: A4.Osäker objektreferering </strong>

<strong>Problem</strong>
När man startar servern och applikationen så får man informationen om vart databas filen finns. Skriver man in denna filsökväg (static/message.db) så laddas databasen ner med användarna och inlägg. Man kunde även gå djupare bland filerna och få ut bilderna (static/images/"filnamn"). Detta problem sker relativt ofta då applikationer använder sig utav det riktiga namnet eller nyckeln av objekt när webb sidan genereras.

<strong>Konsekvenser</strong>
Detta problem leder till att hackaren kan få tillgång till känslig data i applikationen och utgöra en risk för alla som använder den. I fallet som testare så visste jag sökvägen men då det inte var en svår sökväg hade det inte varit svårt för en hackare att gissa fram det.

<strong>Åtgärd</strong> 
Man bör se till så att man skyddar varje användares tillgängliga objekt vilket man kan göra via indirekta objektreferenser. Istället för att använda sig utav databasnyckeln till användaren så kan man använda en drop-down list som visar ett visst antal källor som är bundna till den aktiva användaren. Ett annat sätt är att kontrollera tillgången, varje gång det görs en direkt objektreferens från en osäker källa så måste det ske en kontroll för att se ifall användaren är autentisierad eller ej. (Ref. [4]). 

<strong>6: A6.Exponering av känslig data</strong>

<strong>Problem</strong>
När jag upptäckte problem nummer fem (A4.Osäker objektreferering) så kunde man även se att lösenorden sparades i klartext i databasen, då lösenorden är det känsligaste datan som den här applikationen håller så anser jag det vara en stor risk.

<strong>Konsekvenser</strong>
Att en användare kan utgöra sig för att vara någon annan är farligt nog men då även admins lösenord var blottat så kan en användare med onda aningar göra stor skada på applikationen och dess användare.

<strong>Åtgärd</strong> 
Se till att all känslig data som lösenorden är ordentligt hashade, eventuellt saltade med för extra säkerhet och inte spara de i klartext i databasen. Man kan även överblicka ifall man behöver spara ner datan överhuvudtaget, om den inte är nödvändig finns det ingen anledning att spara den. (Ref. [5]). 


##Prestandaproblem
<strong>1. Inläsning av scriptfiler i början</strong>

<strong>Problem</strong>
Programmeraren länkar in skriptet innan html:en renderas.

<strong>Konsekvenser</strong>
Att länka in skriptet i head taggen kan medföra att det tar längre tid för html:en och bilderna på sidan att rendreras. Dock så kan jag förstå programmeraren i detta syfte då det används en partial view för att undvika upprepa kod.

<strong>Åtgärd</strong>
Då det i detta fall inte är en större applikation så spelar det inte någon större roll vart den är placerad, för snabbare rendering så kan man flytta inläsningen av skriptet längst ner på sidan.

<strong>2. “Log out” knapp vid inlogg</strong>

<strong>Problem</strong>
Innan man hunnit logga in så finns det en logga ut knapp

<strong>Konsekvenser</strong>
Kan uppstå förvirring/irritation för användaren då knappen gör en post men inget händer.

<strong>Åtgärd</strong>
Låt knappen endast visas när personen är inloggad på sidan.


<strong>3. Kvarstående text efter POST</strong>

<strong>Problem</strong>
När man postat sitt meddelande så rensas inte textfältet i formuläret.

<strong>Konsekvenser</strong>
Eventuell irritation från användaren. Enklare att spamma sidan genom att klicka Enter flera gånger.

<strong>Åtgärd</strong>
Antingen köra en document.getElementById(‘form’).reset() eller document.getElementById(‘textField’).value=’’ när formuläret  skickats;

<strong>4. Kaka skapas efter felaktigt inlogg</strong>

<strong>Problem</strong>
När man loggar in med oregistrerade uppgifter så skapas det en kaka trots att man inte loggas in, denna kaka tillåter dock inte användaren att kunna se inläggen som den manipulerade kakan kunde (Säkerhetsproblem 2).

<strong>Konsekvenser</strong>
Det skapas en onödig kaka

<strong>Åtgärd</strong>
Kontrollera att kakor endast skapas om inloggningen lyckades.

##Reflektioner


##Referenser
[1] OWASP, OWASP Top 10 - 2013, sida 6
[2] OWASP, OWASP Top 10 - 2013, sida 8
[3] OWASP, OWASP Top 10 - 2013, sida 9
[4] OWASP, OWASP Top 10 - 2013, sida 10
[5] OWASP, OWASP Top 10 - 2013, sida 12
