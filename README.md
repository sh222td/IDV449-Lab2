# IDV449 Laboration 2 - Mezzy Labbage
###Sandra Hansson
###sh222td

##Säkerhetsproblem
<strong>1. Kvarstående kakor</strong>

<strong>Problem</strong>
När användaren loggar ut så förstörs inte kakan, detta leder till är när användaren loggar ut så redirectas man tillbaka till inloggnins sidan men om man lägger till /message i urlen så kommer man in igen som samma användare. Man kan däremot endast se innehållet, inte lägga till nya meddelanden.
Kakan förstörs endast när man stänger ner webbläsaren vilket visar att det antagligen används sessions kakor för inloggshantering.

<strong>Konsekvenser</strong> 
Kvarstående kakor är en risk då det medför att användaren kommer finnas kvar i systemet fram tills användaren stänger ner webbläsaren. Det kan i sin tur leda till att andra användare kan komma åt personens sida.

<strong>Åtgärdning </strong>
Genom att skapa och kalla på en funktion som förstör kakan/sessionen när personen loggar ut så är problemet åtgärdat.


<strong>2. Manipulering av kakor</strong>

<strong>Problem</strong>
Manipulering av en existerande kaka är tillåten mellan olika webbläsare. Loggar jag till exempel in på sidan  på google chrome och kopierar kakans värde via EditThisCookie-plugin, sen går in på samma sida fast på firefox och klistrar in kakans värde via Firebug så kan jag vara inloggad på två olika stället samtidigt. 

<strong>Konsekvenser</strong>
Detta kan leda till att en hackare kan få tag på en annan användares kaka och utnyttja den. Bryter mot A2: Bruten autentisering och sessions hantering.

<strong>Åtgärdning</strong> 
Genom att använda sig utav $_SERVER[‘HTTP_USER_AGENT’] så kan man kontrollera att användaren endast är inloggad på sin egna agent.


<strong>3. XSS, otillåtna taggar</strong>

<strong>Problem</strong>
I text fältet där man ska skriva in meddelanden så kan man lägga in html taggar som a-taggar, img-taggar, style-taggar och formulär-taggar. 

<strong>Konsekvenser</strong>
Detta är resultatet av att den inskickade datan inte valideras korrekt vilket kan leda till att en användare med onda tankar lurar okunniga användare till att gå in på osäkra och fientliga sidor.

<strong>Åtgärdning</strong> 
Man kan åtgärda ett sånt problem på lite olika sätt. Antingen trimmar man texten och kör på ett regex uttryck för <,>, / och kör en replace när sådant uppkommer i texten. Man kan även köra på HTML entity kodning, viktigt och veta här dock är att det endast gäller för html taggar och inte script taggar. För större applikationer så kan man använda sig utav automatiska sanerings bibliotek som OWASPs AntiSamy, ett bibliotek för html och css kod.  (Referens https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet)



##Prestandaproblem
<strong>1. “Log out” knapp vid inlogg</strong>

<strong>Problem</strong>
Innan man hunnit logga in så finns det en logga ut knapp

<strong>Konsekvenser</strong>
Kan uppstå förvirring/irritation för användaren då knappen gör en post men inget händer.

<strong>Åtgärdning</strong>
Låt knappen endast visas när personen är inloggad på sidan.


<strong>2. Kvarstående text efter POST</strong>

<strong>Problem</strong>
När man postat sitt meddelande så rensas inte textfältet i formuläret.

<strong>Konsekvenser</strong>
Eventuell irritation från användaren. Enklare att spamma sidan genom att klicka Enter flera gånger.

<strong>Åtgärdning</strong>
Antingen köra en document.getElementById(‘form’).reset() eller document.getElementById(‘textField’).value=’’ när formuläret  skickats;
-----------------------------------------------------
##Reflektioner
