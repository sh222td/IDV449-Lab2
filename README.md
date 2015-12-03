# IDV449 Laboration 2 - Mezzy Labbage
###Sandra Hansson - sh222td

##Säkerhetsproblem
<strong>1. A2.Bruten autentisering och sessions-hantering, Kvarstående kakor</strong>

<strong>Problem</strong>
När användaren loggar ut förstörs inte kakan, detta leder till att när användaren loggar ut så omderigeras man tillbaka till inloggningssidan men om man lägger till "/message" i url'en kommer man in igen som samma användare. Man kan däremot endast se innehållet, inte lägga till nya meddelanden. Kakan förstörs endast när man stänger ner webbläsaren vilket visar att det antagligen används sessions-kakor för inloggshantering.

<strong>Konsekvenser</strong> 
Kvarstående kakor är en risk då det medför att användaren kommer finnas kvar i systemet fram tills användaren stänger ner webbläsaren. Det kan i sin tur leda till att andra användare kan komma åt personens sida.

<strong>Åtgärd </strong>
Genom att skapa och kalla på en funktion som förstör kakan/sessionen när personen loggar ut blir problemet åtgärdat.


<strong>2: A2.Bruten autentisering och sessions-hantering, Manipulering av kakor</strong>

<strong>Problem</strong>
Manipulering av en existerande kaka är tillåten mellan olika webbläsare. Loggar jag till exempel in på sidan på Google Chrome, kopierar kakans värde via EditThisCookie-plugin, går in på samma sida igen fast på Firefox och klistrar in kakans värde via Firebug så kan jag vara inloggad på två olika stället samtidigt.

<strong>Konsekvenser</strong>
Detta kan leda till att en hackare kan få tag på en annan användares kaka och utnyttja den. Det bryter mot A2: Bruten autentisering och sessions hantering.

<strong>Åtgärd</strong> 
Genom att använda sig av $_SERVER[‘HTTP_USER_AGENT’] som returnerar webbläsarens identifierare kan man kontrollera att användaren endast är inloggad på sin egna agent, om man använder sig av php. Man bör även se till att kontrollerna för hantering av sessioner och autensiering möter kraven som defineras i OWASPs Application Security Verification Standard.


<strong>3: A3.XSS, otillåtna taggar</strong>

<strong>Problem</strong>
I textfältet där man ska skriva in meddelanden kan man lägga in html-taggar som a-taggar, img-taggar, style-taggar och formulär-taggar. Då XSS är den vanligaste säkerhetsbristen är det inte en ovanlighet att en webbsida kan vara oskyddad mot det, som tur är så är den lätt att upptäcka. (Ref. [3])

<strong>Konsekvenser</strong>
Detta är resultatet av att den inskickade datan inte valideras korrekt vilket kan leda till att en hackare kan utföra skript i en omedveten användares webbläsare för att stjäla dess sessioner, dirigera om användaren till osäkra sidor eller vanställa hemsidor.

<strong>Åtgärd</strong> 
Man kan åtgärda ett sånt problem på lite olika sätt. Antingen trimmar man texten och använder sig av ett regex-uttryck för <,>, / och använder en replace när sådant uppkommer i texten. Man kan även använda HTML entity kodning, viktigt och veta här är dock att det endast gäller för html-taggar och inte script-taggar. För större applikationer kan man använda sig av automatiska saneringsbibliotek som OWASPs AntiSamy, ett bibliotek för html och CSS kod. (Ref. [3])


<strong>4: A1.SQL injection, inlogg av ej existerande medlem</strong>

<strong>Problem</strong>
Om man skriver in en valfri mejl och "1'or'1=1" som lösenord så kan man logga in trots att mejlen inte är registrerad, när man väl är inloggad kan man se alla inlägg samt posta nya fast under ett namn som redan är registrerat i systemet.

<strong>Konsekvenser</strong>
 Vilken person som helst kan få tillgång till innehållet på sidan utan att vara registrerad och validerad. Denne kan även utge sig vara någon annan då det postas under en redan registrerad användare.

<strong>Åtgärd</strong> 
Genom att hålla osäker data separerad från kommandon och queries kan man förebygga attacker. Exempel på sådana lösningar är att använda ett säkert API som undviker användning av tolken helt och hållet. Man måste dock fortfarande vara kritiskt mot API:er med lagrade procedurer då det fortfarande kan införa dolda injektions (Ref. [2]).


<strong>5: A4.Osäker objektreferering </strong>

<strong>Problem</strong>
När man startar servern och applikationen får man informationen om vart databasfilen finns. Skriver man in denna filsökväg (static/message.db) laddas databasen ner med användarna och inlägg. Man kunde även gå djupare bland filerna och få ut bilderna (static/images/"filnamn"). Detta problem sker relativt ofta då applikationer använder sig av det riktiga namnet eller nyckeln av objekt när webbsidan genereras (Ref. [4]).

<strong>Konsekvenser</strong>
Detta problem leder till att hackaren kan få tillgång till känslig data i applikationen och utgöra en risk för alla som använder den. I rollen som testare visste jag sökvägen men då det inte var en svår sökväg hade det inte varit svårt för en hackare att gissa fram det.

<strong>Åtgärd</strong> 
Man bör se till så att man skyddar varje användares tillgängliga objekt vilket man kan göra via indirekta objektreferenser. Istället för att använda sig av databasnyckeln till användaren så kan man använda en drop-down list som visar ett visst antal källor som är bundna till den aktiva användaren. Ett annat sätt är att kontrollera tillgången, varje gång det görs en direkt objektreferens från en osäker källa så måste det ske en kontroll för att se ifall användaren är autentiserad eller ej. (Ref. [4]).

<strong>6: A6.Exponering av känslig data</strong>

<strong>Problem</strong>
När jag upptäckte problem nummer fem (A4.Osäker objektreferering) så kunde man även se att lösenorden sparades i klartext i databasen, då lösenorden är det känsligaste datan som den här applikationen håller anser jag att det är en stor risk. De vanligaste anledningarna till såna här misstag är att man helt enkelt inte valt at lägga ner tid på att kryptera datan ordentligt eller att man använt sig av uråldriga och svaga algoritmer. (Ref. [5])

<strong>Konsekvenser</strong>
Att en användare kan utgöra sig för att vara någon annan är farligt nog men då även Admin-lösenordet var blottat så kan en användare med onda aningar göra stor skada på applikationen och dess användare.

<strong>Åtgärd</strong> 
Se till att all känslig data som lösenorden är ordentligt hashade, eventuellt saltade med för extra säkerhet och inte spara de i klartext i databasen. Man kan även överblicka ifall man behöver spara ner datan överhuvudtaget, om den inte är nödvändig finns det ingen anledning att spara den. (Ref. [5]).


##Prestandaproblem
<strong>1. Inläsning av scriptfiler i början</strong>

<strong>Problem</strong>
Programmeraren länkar in skriptet innan html:en renderas.

<strong>Konsekvenser</strong>
Att länka in skriptet i head-taggen kan medföra att det tar längre tid för html'en och bilderna på sidan att renderas. Dock kan jag förstå programmeraren i detta syfte då det används en partial view för att undvika att upprepa kod, genom att ha det en gång i headern slipper man ange det längst ner i varje html-fil som läses in.

<strong>Åtgärd</strong>
Då det i detta fall inte är en större applikation så anser jag det inte spela någon större roll vart den är placerad, för snabbare rendering kan man flytta inläsningen av skriptet längst ner på sidan.

<strong>2. Kaka skapas efter felaktigt inlogg</strong>

<strong>Problem</strong>
När man loggar in med oregistrerade uppgifter skapas det en kaka trots att man inte loggas in, denna kaka tillåter dock inte användaren att kunna se inläggen som den manipulerade kakan kunde (Säkerhetsproblem 2).

<strong>Konsekvenser</strong>
Det skapas en onödig kaka

<strong>Åtgärd</strong>
Kontrollera att kakor endast skapas om inloggningen lyckades.

##Reflektioner
Efter att ha studerat och testat applikationen så upptäckte jag även lite småsaker som inte utgjorde någon fara men som räknades mer som förvirrings- eller irritationsmoment hos användaren. Ett av problemen var att man kunde se en "log out"-knapp på inlogningssidan, detta är egentligen bara ett enkelt fel som går att lösa genom att placera funktionen i rätt vy. Ett annat moment var när man postar sitt meddelande. Skriften kvarstår nämligen i textfältet vilket får det att se slarvigt ut i applikationen, det medför även att man enklare kan spamma sitt meddelande. Det problemet kan lätt lösas genom att köra en document.getElementById(‘form’).reset() eller document.getElementById(‘textField’).value=’’ när formuläret  skickats. De resterande fem säkerthetshålen från OWASP hade jag svårigheter att hitta i denna applikation då jag var osäker på hur man skulle gå tillväga för att testa de, något som jag kommer jobba vidare på i framtiden när man är lite mer insatt i det.

Jag tycker att den här laborationen har varit väldigt kul och lärorik, jag har även fått en djupare insikt i hur blottade webbsidor egentligen är från attacker. Att få jobba med att testa en webbsida gjorde det hela mycket mer underhållande med än att vi skulle exempelvis bara fått läsa på om säkerhet. Det hade däremot känts mer givande ifall vi även fick lära oss lite konkreta lösningar på hur man skulle förebygga koden mot attacker då vissa förklaringar på OWASPs sida kunde vara lite "flummiga".

I framtida arbeten kommer jag försöka vara mer noggrann med säkerheten och testa sidan efter OWASPs säkerhetspunkter.

##Referenser
[1] OWASP, "OWASP Top 10 - 2013", sida 6

[2] OWASP, "OWASP Top 10 - 2013", sida 8

[3] OWASP, "OWASP Top 10 - 2013", sida 9

[4] OWASP, "OWASP Top 10 - 2013", sida 10

[5] OWASP, "OWASP Top 10 - 2013", sida 12


